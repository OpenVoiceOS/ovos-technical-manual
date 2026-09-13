# settingsmeta.json

## Define settings UI for a Skill

To define our Skills settings UI we use a `settingsmeta.json` or `settingsmeta.yaml` file. 
This file must be in the root directory of the Skill and must follow a specific structure.

Once settings have been defined using a `settingsmeta` file, they will be presented to the user in the configured backend or helper application

### Example settingsmeta file

To see it in action, lets look at a simple example from the [Date-Time Skill](https://github.com/MycroftAI/skill-date-time). First using the JSON syntax as a `settingsmeta.json` file:

```javascript
{
    "skillMetadata": {
        "sections": [
            {
                "name": "Display",
                "fields": [
                    {
                        "name": "show_time",
                        "type": "checkbox",
                        "label": "Show digital clock when idle",
                        "value": "true"
                    }
                ]
            }
        ]
    }
}
```

Now, here is the same settings, as it would be defined with YAML in a `settingsmeta.yaml` file:

```yaml
skillMetadata:
   sections:
      - name: Display
        fields:
          - name: show_time
            type: checkbox
            label: Show digital clock when idle
            value: "true"
```

Notice that the value of `false` is surrounded by "quotation marks". This is because OVOS expects a string of `"true"` or `"false"` rather than a Boolean.

Both of these files would result in the same settings block.

![](https://3867939753-files.gitbook.io/~/files/v0/b/gitbook-legacy-files/o/assets%2F-LocrEaSe-b3SJ4H87SC%2Fsync%2F1535fed57a285e48b63090cb9e6c82591f3aacc0.png?generation=1599008778025054&alt=media)

It is up to your personal preference which syntax you choose.

### Structure of the settingsmeta file

Whilst the syntax differs, the structure of these two filetypes is the same. This starts at the top level of the file by defining a `skillMetadata` object. This object must contain one or more `sections` elements.

#### Sections

Each section represents a group of settings that logically sit together. This enables us to display the settings more clearly in the web interface for users.

In the simple example above we have just one section. However, the [Spotify Skill settings](https://github.com/forslund/spotify-skill/blob/19.08/settingsmeta.json) contains two sections. The first is for Spotify Account authentication, and the second section contains settings to define your default playback device.

Each section must contain a `name` attribute that is used as the heading for that section, and an Array of `fields`.

#### Fields

Each section has one or more `fields`. Each field is a setting available to the user. Each field takes four properties:

*   `name` (String)

    The `name` of the `field` is used by the Skill to get and set the value of the `field`. It will not usually be displayed to the user, unless the `label` property has not been set.
*   `type` (Enum)

    The data type of this field. The supported types are:

    * `text`: any kind of text
    * `email`: text validated as an email address
    * `checkbox`: boolean, True or False
    * `number`: text validated as a number
    * `password`: text hidden from view by default
    * `select`: a drop-down menu of options
    * `label`: special field to display text for information purposes only. No name or value is required for a `label` field.
*   `label` (String)

    The text to be displayed above the setting field.
*   `value` (String)

    The initial value of the field.

Examples for each type of field are provided in JSON and YAML at the end of this page.


## SettingsMeta Examples

### Label Field

```yaml
skillMetadata:
   sections:
      - name: Label Field Example
        fields:
          - type: label
            label: This is descriptive text.
```

### Text Field

```yaml
skillMetadata:
   sections:
      - name: Text Field Example
        fields:
          - name: my_string
            type: text
            label: Enter any text
            value:
```

### Email

```yaml
skillMetadata:
   sections:
      - name: Email Field Example
        fields:
          - name: my_email_address
            type: email
            label: Enter your email address
            value:
```


### Checkbox

```yaml
skillMetadata:
   sections:
      - name: Checkbox Field Example
        fields:
          - name: my_boolean
            type: checkbox
            label: This is an example checkbox. It creates a Boolean value.
            value: "false"
```

### Number

```yaml
skillMetadata:
   sections:
      - name: Number Field Example
        fields:
          - name: my_number
            type: number
            label: Enter any number
            value: 7
```


### Password

```yaml
skillMetadata:
   sections:
      - name: Password Field Example
        fields:
          - name: my_password
            type: password
            label: Enter your password
            value:
```

### Select

```yaml
skillMetadata:
   sections:
      - name: Select Field Example
        fields:
          - name: my_selected_option
            type: select
            label: Select an option
            options: Option 1|option_one;Option 2|option_two;Option 3|option_three
            value: option_one
```
