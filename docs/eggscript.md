# Eggscript

Eggscript is a markup language that can be "compiled" into a valid OVOS Skill

> **EXPERIMENTAL** This is an experimental feature

It is intended as an easy way for user to create simple skills, while offering an easy transition to regular skills

It also helps getting a lot of the boilerplate done for you when getting started

You can find a developer preview of eggscript in [github](https://github.com/OpenVoiceOS/eggscript)

## Crash Course

Example files written in eggscript


hello.eggscript

        // this is a comment
        // all comments and blank lines are ignored
        // special interperter variables can be set with @var syntax
        // - @name -> skill name
        // - @author -> skill author
        // - @email -> author contact
        // - @license -> skill license
        // - @interpreter -> supported interperter, eg, cli
        // - @compiler -> supported compiler, eg, mycroft skill
        
        @author jarbasai
        @email jarbasai@mailfence.com
        @license MIT
        @name hello world
        @url https://github.com/author/repo
        @version 0.1.0
        
        // this script can be used standalone in the cli
        @interpreter cli
        // a standalone python file can be generated
        @compiler cli
        // a mycroft skill can be generated
        @compiler mycroft
        
        // intent definition
        # hello world
        + hello world
        - hello world
        
        // you can define python code, executed after TTS
        ```
        hello = "world"
        if hello == "world":
        print("python code!")
        ```

dialogs.eggscript


        // this is a comment
        // all comments and blank lines are ignored
        
        // text after # is the intent name
        # hello world
        // text after + is the user utterance
        + hello world
        // text after - is mycroft's response
        - hello world
        
        
        # weather in location
        // you can capture variables and use them using {var} syntax
        + how is the weather in {location}
        - how am i supposed to know the weather in {location}
        
        
        # weather
        // this will create a intent file with the 3 + utterances
        + what is the weather like
        + how is the weather
        + how does it look outside
        // this will create a dialog file with the 2 - utterances
        - i do not know how to check the weather
        - stick your head ouf of the window and check for yourself
        
        
        # count to 10
        + count to 10
        // if ident level matches its an alternate dialog
        - i will only count to 5
        - i only know how to count to 5
          // use tab for identation
          // each ident level defines a new utterance to be spoken
              - 1
                  - 2
                      - 3
                          - 4
                              - 5
        
        

layers.eggscript


        // this is a comment
        // all comments and blank lines are ignored
        // this sample scripts show intent layers usage
        
        
        // the number of # in intent definition determines an intent layer
        # tell me about
        + tell me about {thing}
          - {thing} exists
        
        // N times + will enable layer N
        // to enable layer 2
        ++
        
        
        // use N times # for layer N
        // this intent is in layer 2, enabled by previous intent
        ## tell me more
        + tell me more
        + continue
          - i do not know more
        
        // N times - will disable layer N
        // to disable layer 2
        --




## Interpreters

Can run a subset of eggscript directly, enough to test simple skills in the terminal

```python
from eggscript import CliInterpreter
from os.path import dirname

c = CliInterpreter()
c.load_eggscript_file(f"{dirname(__file__)}/dialogs.eggscript")
c.run()

```

## Compilers

```python
from eggscript import OVOSSkillCompiler
from os.path import dirname

c = OVOSSkillCompiler()
c.load_eggscript_file(f"{dirname(__file__)}/layers.eggscript")
c.export("myskill")
```

You can now continue extending your exported skill to add more advanced functionality