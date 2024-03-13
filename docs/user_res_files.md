# Customization

## Resource Files

Resource files are essential components of OVOS skills, containing data such as dialogs, intents, vocabularies, regular expressions, templates, and more. 

These files define how a skill interacts with the user and responds to queries.

> **RECAP**: the skill contains a `locale` folder with subfolders for each lang, eg `en-us`, learn more in [skill structure docs](https://openvoiceos.github.io/ovos-technical-manual/skill_structure/)

### Local Language support

Adding support for additional languages to existing skills enables users to interact with OVOS in their preferred language. 

While developing or waiting for skills to support your language you might want to add it locally:

Users can add language support for a skill by creating a new language folder in the user resources directory and copying the necessary files over:

**Identify the Skill and Language to Add**:

   - Determine the ID of the skill for which you want to add language support. Let's continue using the skill ID `skill-ovos-date-time.openvoiceos`.
   - Identify the language you want to add support for. For this example, let's say you want to add support for Spanish (language code: `es-es`).

**Create the New Language Folder**:

   - Create a new directory with the name of the language code (`es-es` for Spanish) within the `locale` directory of the skill.
   - This can be done using file manager tools or command-line utilities such as `mkdir` on Unix-like systems.

**Copy the Required Files to the New Language Folder**:

   - Copy all the necessary resource files from an existing language folder (e.g., `en-us`) to the newly created language folder (`es-es`).
   - This includes files such as dialogues, vocabularies, regex patterns, etc., depending on the resources used by the skill.
   - Ensure that all files are placed in the corresponding directories within the new language folder to maintain the directory structure of the original skill.

**Verify the Language Addition**:

   - Once the files are copied over, verify that the new language is supported by the skill.
   - Restart the Mycroft or OpenVoiceOS service to allow the skill to recognize the newly added language resources.
   - Test the skill using the newly added language to ensure that it functions correctly and uses the appropriate language-specific resources.

By following these steps, users can add support for additional languages to existing skills by creating new language folders and copying the required resource files. 

This allows users to extend the language capabilities of skills beyond the languages provided by default.

### Customizing Dialogs

Users can personalize the behavior of skills by customizing dialogues to better suit their preferences. 

To give a unique twist and personality to your assistant you don't need to fork existing skills only to change dialogs

Here's a step-by-step guide on how to replace the dialog of an existing skill:

**Identify the Skill and Resource to Replace**:

   - Determine the ID of the skill whose dialog you want to replace. In this example, let's assume the skill ID is `skill-ovos-date-time.openvoiceos`.
   - Identify the specific dialog file you want to replace. For this example, let's say you want to replace the `time.current.dialog` file located in the `locale/en-us/dialog` directory of the skill.

**Create the Replacement Dialog File**:

   - Create a new dialog file with the same name (`time.current.dialog`) as the original file.
   - Customize the content of the dialog file according to your preferences. You can modify the existing dialogues, add new ones, or remove any that you don't want to use.

**Locate the User-Specific Resource Directory**:

   - Use the provided skill ID (`skill-ovos-date-time.openvoiceos`) to locate the user-specific resource directory.
   - The user-specific resource directory is typically located within the XDG data directory. It follows the path `XDG_DATA_HOME/mycroft/resources/skill-ovos-date-time.openvoiceos` (where `XDG_DATA_HOME` is the user's data directory, usually `~/.local/share` on Linux).

**Copy the Replacement Dialog File to the User-Specific Directory**:

   - Copy or move the replacement dialog file (`time.current.dialog`) to the appropriate directory within the user-specific resource directory.
   - Place the file in the `locale/en-us/dialog` directory within the user-specific resource directory. This mirrors the directory structure of the original skill.
   - In this example the final path of the file would be `~/.local/share/mycroft/resources/skill-ovos-date-time.openvoiceos/locale/en-us/dialog/time.current.dialog` 

**Verify the Replacement**:

   - Test the skill to ensure that the modified dialogues are being used instead of the original ones.


Customizing dialogues offers users flexibility in tailoring the behavior of skills to their specific needs and preferences.
