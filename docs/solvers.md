# Solver Plugins

Solver plugins solve natural language queries, they define a unified api around specific kinds of questions and provide auto translation capabilities for language support

## Question Solvers
  
A plugin can define the language it works in, eg, wolfram alpha only accepts english input at the time of this writing

Bidirectional translation will be handled behind the scenes for other languages

**Developing a solver: **

Plugins are expected to implement the get_xxx methods and leave the user facing equivalents alone

```python
from ovos_plugin_manager.templates.solvers import QuestionSolver


class MySolver(QuestionSolver):
    enable_tx = False  # if True enables bidirectional translation
    priority = 100

    def __init__(self, config=None):
        config = config or {}
         # set the "internal" language, defined by dev, not user
         # this plugin internally only accepts and outputs english
        config["lang"] = "en"
        super().__init__(config)
        
    # expected solver methods to be implemented
    def get_data(self, query, context):
        """
        query assured to be in self.default_lang
        return a dict response
        """
        return {"error": "404 answer not found"}

    def get_image(self, query, context=None):
        """
        query assured to be in self.default_lang
        return path/url to a single image to acompany spoken_answer
        """
        return "http://stock.image.jpg"

    def get_spoken_answer(self, query, context=None):
        """
        query assured to be in self.default_lang
        return a single sentence text response
        """
        return "The full answer is XXX"

    def get_expanded_answer(self, query, context=None):
        """
        query assured to be in self.default_lang
        return a list of ordered steps to expand the answer, eg, "tell me more"

        {
            "title": "optional",
            "summary": "speak this",
            "img": "optional/path/or/url
        }
        :return:
        """
        steps = [
            {"title": "the question", "summary": "we forgot the question", "image": "404.jpg"},
            {"title": "the answer", "summary": "but the answer is 42", "image": "42.jpg"}
        ]
        return steps

```

**Using a solver:**

solvers work with any language as long as you stick to the officially supported wrapper methods

```python
    # user facing methods, user should only be calling these
    def search(self, query, context=None, lang=None):
        """
        cache and auto translate query if needed
        returns translated response from self.get_data
        """
   
    def visual_answer(self, query, context=None, lang=None):
        """
        cache and auto translate query if needed
        returns image that answers query
        """

    def spoken_answer(self, query, context=None, lang=None):
        """
        cache and auto translate query if needed
        returns chunked and translated response from self.get_spoken_answer
        """

    def long_answer(self, query, context=None, lang=None):
        """
        return a list of ordered steps to expand the answer, eg, "tell me more"
        translated response from self.get_expanded_answer
        {
            "title": "optional",
            "summary": "speak this",
            "img": "optional/path/or/url
        }
        :return:
        """
```

**Example Usage  - DuckDuckGo plugin**

single answer

```python
from skill_ovos_ddg import DuckDuckGoSolver

d = DuckDuckGoSolver()

query = "who is Isaac Newton"

# full answer
ans = d.get_spoken_answer(query)
print(ans)
# Sir Isaac Newton was an English mathematician, physicist, astronomer, alchemist, theologian, and author widely recognised as one of the greatest mathematicians and physicists of all time and among the most influential scientists. He was a key figure in the philosophical revolution known as the Enlightenment. His book Philosophiæ Naturalis Principia Mathematica, first published in 1687, established classical mechanics. Newton also made seminal contributions to optics, and shares credit with German mathematician Gottfried Wilhelm Leibniz for developing infinitesimal calculus. In the Principia, Newton formulated the laws of motion and universal gravitation that formed the dominant scientific viewpoint until it was superseded by the theory of relativity.
```

chunked answer, for conversational dialogs, ie "tell me more"

```python
from skill_ovos_ddg import DuckDuckGoSolver

d = DuckDuckGoSolver()

query = "who is Isaac Newton"

# chunked answer
for sentence in d.get_long_answer(query):
    print(sentence["title"])
    print(sentence["summary"])
    print(sentence.get("img"))
    
    # who is Isaac Newton
    # Sir Isaac Newton was an English mathematician, physicist, astronomer, alchemist, theologian, and author widely recognised as one of the greatest mathematicians and physicists of all time and among the most influential scientists.
    # https://duckduckgo.com/i/ea7be744.jpg
    
    # who is Isaac Newton
    # He was a key figure in the philosophical revolution known as the Enlightenment.
    # https://duckduckgo.com/i/ea7be744.jpg
    
    # who is Isaac Newton
    # His book Philosophiæ Naturalis Principia Mathematica, first published in 1687, established classical mechanics.
    # https://duckduckgo.com/i/ea7be744.jpg
    
    # who is Isaac Newton
    # Newton also made seminal contributions to optics, and shares credit with German mathematician Gottfried Wilhelm Leibniz for developing infinitesimal calculus.
    # https://duckduckgo.com/i/ea7be744.jpg
    
    # who is Isaac Newton
    # In the Principia, Newton formulated the laws of motion and universal gravitation that formed the dominant scientific viewpoint until it was superseded by the theory of relativity.
    # https://duckduckgo.com/i/ea7be744.jpg
  ```  

Auto translation, pass user language in context

```python
from skill_ovos_ddg import DuckDuckGoSolver

d = DuckDuckGoSolver()

# bidirectional auto translate by passing lang context
sentence = d.get_spoken_answer("Quem é Isaac Newton",
                           context={"lang": "pt"})
print(sentence)
# Sir Isaac Newton foi um matemático inglês, físico, astrônomo, alquimista, teólogo e autor amplamente reconhecido como um dos maiores matemáticos e físicos de todos os tempos e entre os cientistas mais influentes. Ele era uma figura chave na revolução filosófica conhecida como o Iluminismo. Seu livro Philosophiæ Naturalis Principia Mathematica, publicado pela primeira vez em 1687, estabeleceu a mecânica clássica. Newton também fez contribuições seminais para a óptica, e compartilha crédito com o matemático alemão Gottfried Wilhelm Leibniz para desenvolver cálculo infinitesimal. No Principia, Newton formulou as leis do movimento e da gravitação universal que formaram o ponto de vista científico dominante até ser superado pela teoria da relatividade
```
