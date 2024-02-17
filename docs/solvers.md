# Solver Plugins


Solver plugins solve natural language queries, they define a unified api around specific kinds of questions and provide auto translation capabilities for language support

A plugin can define the language it works in, eg, wolfram alpha only accepts english input at the time of this writing

Bidirectional translation will be handled behind the scenes for other languages

Solvers are used by individual skills and by the [Persona Framework](https://github.com/OpenVoiceOS/ovos-persona)


## Question Solvers

**NEW** in `ovos-core` version **0.0.8**

Given a free form natural language question, return an answer

Originally implemented for [Neon](https://github.com/Neongeckocom/neon_solvers)

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
for sentence in d.long_answer(query):
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
sentence = d.spoken_answer("Quem é Isaac Newton", context={"lang": "pt"})
print(sentence)
# Sir Isaac Newton foi um matemático inglês, físico, astrônomo, alquimista, teólogo e autor amplamente reconhecido como um dos maiores matemáticos e físicos de todos os tempos e entre os cientistas mais influentes. Ele era uma figura chave na revolução filosófica conhecida como o Iluminismo. Seu livro Philosophiæ Naturalis Principia Mathematica, publicado pela primeira vez em 1687, estabeleceu a mecânica clássica. Newton também fez contribuições seminais para a óptica, e compartilha crédito com o matemático alemão Gottfried Wilhelm Leibniz para desenvolver cálculo infinitesimal. No Principia, Newton formulou as leis do movimento e da gravitação universal que formaram o ponto de vista científico dominante até ser superado pela teoria da relatividade
```

Plugins are expected to implement the `get_xxx` methods and leave the user facing equivalents alone

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


## Multiple Choice Solvers

**NEW** in `ovos-core` version **0.0.8**

given a question and multiple answers, select the best answer

```python
class MultipleChoiceSolver(AbstractSolver):
    """ select best answer from question + multiple choice
    handling automatic translation back and forth as needed"""

    # plugin methods to override

    @abc.abstractmethod
    def select_answer(self, query: str, options: List[str],
                      context: Optional[dict] = None) -> str:
        """
        query and options assured to be in self.default_lang
        return best answer from options list
        """
        raise NotImplementedError

    # user facing methods
    def solve(self, query: str, options: List[str],
              context: Optional[dict] = None, lang: Optional[str] = None) -> str:
        """
        cache and auto translate query and options if needed
        returns best answer from provided options
        """
        user_lang = self._get_user_lang(context, lang)
        query, context, lang = self._tx_query(query, context, lang)
        opts = [self.translator.translate(opt, lang, user_lang)
                for opt in options]

        # select best answer
        ans = self.select_answer(query, opts, context)

        idx = opts.index(ans)
        return options[idx]

```

## Evidence Solver

**NEW** in `ovos-core` version **0.0.8**

given a document and a question about it, select the best passage that answers the question

```python
class EvidenceSolver(AbstractSolver):
    """perform NLP reading comprehension task,
    handling automatic translation back and forth as needed"""

    # plugin methods to override

    @abc.abstractmethod
    def get_best_passage(self, evidence: str, question: str,
                         context: Optional[dict] = None) -> str:
        """
        evidence and question assured to be in self.default_lang
         returns summary of provided document
        """
        raise NotImplementedError

    # user facing methods
    def extract_answer(self, evidence: str, question: str,
                       context: Optional[dict] = None, lang: Optional[str] = None) -> str:
        """
        cache and auto translate evidence and question if needed
        returns passage from evidence that answers question
        """
        user_lang = self._get_user_lang(context, lang)
        evidence, context, lang = self._tx_query(evidence, context, lang)
        question, context, lang = self._tx_query(question, context, lang)

        # extract answer from doc
        ans = self.get_best_passage(evidence, question, context)

        # translate output to user lang
        if self.enable_tx and user_lang not in self.supported_langs:
            return self.translator.translate(ans, user_lang, lang)
        return ans
```

## Entailment Solver

**NEW** in `ovos-core` version **0.0.8**

Given a hypothesis and a premise, return True if the premise entails the hypothesis, False otherwise

```python

class EntailmentSolver(AbstractSolver):
    """ select best answer from question + multiple choice
    handling automatic translation back and forth as needed"""

    # plugin methods to override

    @abc.abstractmethod
    def check_entailment(self, premise: str, hypothesis: str,
                         context: Optional[dict] = None) -> bool:
        """
        premise and hyopithesis assured to be in self.default_lang
        return Bool, True if premise entails the hypothesis False otherwise
        """
        raise NotImplementedError

    # user facing methods
    def entails(self, premise: str, hypothesis: str,
                context: Optional[dict] = None, lang: Optional[str] = None) -> bool:
        """
        cache and auto translate premise and hypothesis if needed
        return Bool, True if premise entails the hypothesis False otherwise
        """
        user_lang = self._get_user_lang(context, lang)
        query, context, lang = self._tx_query(query, context, lang)

        # summarize
        return self.check_entailment(premise, hypothesis)
```


## Summarization Solver

**NEW** in `ovos-core` version **0.0.8**

Given a document, return it's summary

```python
class TldrSolver(AbstractSolver):
    """perform NLP summarization task,
    handling automatic translation back and forth as needed"""

    # plugin methods to override

    @abc.abstractmethod
    def get_tldr(self, document: str,
                 context: Optional[dict] = None) -> str:
        """
        document assured to be in self.default_lang
         returns summary of provided document
        """
        raise NotImplementedError

    # user facing methods
    def tldr(self, document: str,
             context: Optional[dict] = None, lang: Optional[str] = None) -> str:
        """
        cache and auto translate query if needed
        returns summary of provided document
        """
        user_lang = self._get_user_lang(context, lang)
        document, context, lang = self._tx_query(document, context, lang)

        # summarize
        tldr = self.get_tldr(document, context)

        # translate output to user lang
        if self.enable_tx and user_lang not in self.supported_langs:
            return self.translator.translate(tldr, user_lang, lang)
        return tldr
```

