## Contribute to Our Project Translations with GitLocalize!

<iframe width="560" height="315" src="https://www.youtube.com/embed/2udvdIW9W2s" title="OVOS translation guide" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Thank you for your interest in helping translate our project! Your contributions will help make our project accessible to more people around the world. We’ve made it easy for you to get started, even if you’re not familiar with GitHub or coding. Follow the steps below to join our translation effort using GitLocalize.

### Step-by-Step Guide to Translating with GitLocalize

#### Visit Our GitLocalize Project Page
![image](https://gist.github.com/assets/33701864/0a678e87-2cdf-4a9b-924c-e496e47befb4)

1. **Click on the link** to our GitLocalize project: https://gitlocalize.com/users/OpenVoiceOS
2. You will see a list of OVOS repositories to translate, select one
3. You will see a list of languages and translation tasks available.

![image](https://gist.github.com/assets/33701864/c3770638-1452-44fb-ada1-39e39683aea8)

#### Sign Up or Log In
1. If you don’t have an account, **sign up** with your email or GitHub account (you don’t need to know GitHub to do this!).
2. If you already have an account, simply **log in**.

#### Choose a Language

> Adding new languages to the list is a manual process, if your language is unlisted let us know!

1. Select the language you want to translate into from the list of available languages.
2. You will see a list of files that need translation. 
  - `dialogs.json` contains sentences that OVOS will speak
  - `intents.json` contains sentences that the user will speak to OVOS
  - `vocabs.json` similar to intents, but contain sentence fragments/keywords, not full utterances
 

![image](https://gist.github.com/assets/33701864/444cbb6d-1e9e-47b0-84f1-aeb38eef6a17)

#### Start Translating
1. Click on a file that you want to translate.
2. The translation editor will open. Here, you’ll see the original text on the left and a space to enter your translation on the right.
3. Begin translating the text. If you’re unsure about any phrase, feel free to leave it and move on to the next one.

When you open a JSON file for translation in GitLocalize, you’ll see two parts:
- **Key**: This corresponds to a file name in the OVOS repository you selected.
- **Value**: This is the sentence you need to translate.

#### Variables

Variables are placeholders within sentences that represent changing content, such as names or numbers. 

- Original: `My name is {var_name}`
- Translation: `Mi nombre es {var_name}`

![image](https://gist.github.com/assets/33701864/2779c8a3-46de-47b3-894f-eccab0bdd2b5)

**Important Rules**:
- **Do not translate the variable names** (the text inside curly braces `{}`).
- You can **rearrange the position** of variables in your translation, but do not create new variables.
- Ensure that variables are not separated by only whitespace; there should be at least one word between them.

#### Slots

Sometimes, the same file will appear several times, each with a different variation of the same sentence. These variations are called "slots". 

![image](https://gist.github.com/assets/33701864/70f30bc5-56f0-4d87-a521-2c4c77790bfe)

**Important Rules**:
- **Translate at least one slot** in each file.
- If a slot is not needed in your language, enter `[UNUSED]`. This tells us that you reviewed the slot and marked it as translated.
- If you run out of slots to fit all variations of a sentence, you can use newlines to add more translations.

![image](https://gist.github.com/assets/33701864/c70379ef-ef29-484d-a6fb-84d9fca47660)


#### Alternative/Optional words

You can use the "alternative word" syntax to provide options or optional words within a sentence.

- Alternative words: `I love (cats|dogs|birds)` becomes `Amo (gatos|perros|pájaros)`
- Optional words: `I (really|) love (cats|dogs|birds)` becomes `Yo (realmente|) amo (gatos|perros|pájaros)`

![image](https://gist.github.com/assets/33701864/75288b2d-559e-47c3-8e73-bb5391e4a888)


### Tips for Effective Translation

- **Consistency**: Try to use consistent terminology throughout the project.
- **Context**: If a phrase seems unclear, consider the overall context of the project or reach out for clarification.
- **Accuracy**: Aim to convey the meaning as accurately as possible, rather than a literal word-for-word translation.

#### Key Take Aways

- For each sentence (slot), enter your translation.
- If a slot is not needed, enter `[UNUSED]`.
- Leave the variable names in curly braces `{}` unchanged.
- Rearrange variables as needed but do not create new ones.
- Provide multiple options using the syntax `(option1|option2|option3)`.
- Include optional words using the syntax `(optional|)`.
- If there are not enough slots, press `Enter` to add a new line and enter your alternative translation on the new line.

#### Review and Feedback
1. Once you’ve finished translating a file, you can **submit** it for review.
2. Your translations will be reviewed by other native speakers and project maintainers.
3. If any changes are needed, you might receive feedback. Simply log back in, review the comments, and make the necessary adjustments.
![image](https://gist.github.com/assets/33701864/f76df4fb-a825-48ec-8e76-c8e47e013392)

### Need Help?
If you have any questions or need assistance at any point:
- **Join our Matrix chat**: https://matrix.to/#/#openvoiceos-languages:matrix.org
- **Email us**: support@openvoiceos.org
### Thank You!
Your contributions are invaluable, and we appreciate your effort in helping us reach a global audience. Happy translating!




