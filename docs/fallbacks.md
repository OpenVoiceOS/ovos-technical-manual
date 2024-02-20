# Fallback Skills

**UNDER CONSTRUCTION**

## Security

Just like with converse a badly designed or malicious skill can hijack the fallback skill loop, while this is not as serious as with converse some protections are also provided

You can configure what skills are allowed to use the fallback mechanism, you can also modify the fallback priority to ensure skills behave well together. 

Since priority is defined by developers sometimes the default value is not appropriate and does not fit well with the installed skills collection

```javascript
"skills": {
    // fallback skill configuration
    "fallbacks": {
        // you can add skill_id: priority to override the developer defined
        // priority of those skills, this allows customization
        // of unknown intent handling for default_skills + user preferences
        "fallback_priorities": {
           // "skill_id": 10
        },
        // fallback skill handling has 3 modes of operations:
        // - "accept_all"  # default mycroft-core behavior
        // - "whitelist"  # only call fallback for skills in "fallback_whitelist"
        // - "blacklist"  # only call fallback for skills NOT in "fallback_blacklist"
        "fallback_mode": "accept_all",
        "fallback_whitelist": [],
        "fallback_blacklist": []
    }
},
```
