# Padacioso

*A lightweight, dead-simple intent parser*

Built on top of [simplematch](https://github.com/tfeldmann/simplematch), inspired by [Padaos](https://github.com/MycroftAI/padaos)

## Example

```python
from padacioso import IntentContainer

container = IntentContainer()

## samples
container.add_intent('hello', ['hello', 'hi', 'how are you', "what's up"])

## "optionally" syntax
container.add_intent('hello world', ["hello [world]"])

## "one_of" syntax
container.add_intent('greeting', ["(hi|hey|hello)"])

## entity extraction
container.add_intent('buy', [
    'buy {item}', 'purchase {item}', 'get {item}', 'get {item} for me'
])
container.add_intent('search', [
    'search for {query} on {engine}', 'using {engine} (search|look) for {query}',
    'find {query} (with|using) {engine}'
])
container.add_entity('engine', ['abc', 'xyz'])
container.calc_intent('find cats using xyz')
# {'conf': 1.0, 'name': 'search', 'entities': {'query': 'cats', 'engine': 'xyz'}}

## wildcards syntax
container.add_intent('say', ["say *"])
container.calc_intent('say something, whatever')
# {'conf': 0.85, 'entities': {}, 'name': 'test'}

## typed entities syntax
container.add_intent('pick_number', ['* number {number:int}'])
container.calc_intent('i want number 3')
# {'conf': 0.85, 'entities': {'number': 3}, 'name': 'pick_number'})

```
