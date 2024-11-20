# OVOS File Formats Specification


## Dialog Files

- .dialog files
- text files
- each line is an alternative formulation of same sentence
- supports "optional" syntax, eg `(word1|word2)` or `(optional word|)`
- supports variables to be replaced at runtime, via curly braces syntax `{var_name}`

## Intent files

Example based intents (padatious style intents)

contents are example sentences, Intent prediction models are trained on the content of these files

- .intent files
- text files
- each line is an alternative formulation of same sentence
- supports "optional" syntax, eg `(word1|word2)` or `(optional word|)`
- supports variable capture, via curly braces syntax `{var_name}`

## Voc files

Keyword rule based intents (adapt style intents)

contents are not a full sentence, but rather key words/phrases that must be present in the final sentence. Intents are defined by rules around .voc files (required, optional, at\_least\_one, excludes)

- .voc files
- text files
- each line is an alternative formulation of same keyword
- supports "optional" syntax, eg `(word1|word2)` or `(optional word|)`

## Regex Files

keyword intents do not allow variable capture, but they support regex expressions to capture keywords

- .rx files
- text files
- each line is a regex expression, the capture group is the `variable_name`
- `variable_name` becomes available as a keyword
- complements .voc files
