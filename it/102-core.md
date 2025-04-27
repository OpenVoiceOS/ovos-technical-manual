# ovos-core

[OpenVoiceOS](https://openvoiceos.org) è una assistente vocale open source che permette di dialogare con i tuoi dispositivi.

OpenVoiceOS ha un design completamente modulare, tutti i suoi componenti sono sviluppati come plugin. Ciò significa che non solo è un ottimo assistente vocale, ma è anche una bella libreria di funzioni vocali!

`ovos-core` è "il cervello" di OpenVoiceOS, tutti i suoi componenti e gli algoritmi di Natural Language Processing vengono gestiti qui.

## Gestione delle competenze

Il sistema di gestione delle competenze, <strong>skills service</strong> in inglese, è responsabile per la gestione delle skills e degli intenti.

Tutte le richieste all'assistente vocale sono gestite dal servizio di gestione delle skills, il cervello di OVOS

OVOS è davvero modulare. Ad esempio, tutti i plugins di Mycroft funzionano senza problemi fino alla versione **0.1.0** di `ovos-core`!

Al momento, tutte le skills sono semplici pacchetti Python. Una volta installate saranno caricati automaticamente da `ovos-core`

Importante! A partire da `ovos-core` **0.0.8** è possibile provare una skill senza dover installare tutto, ma semplicemente usando `ovos-workshop`, un sistema di contenitori che si basa su [ovos-docker](https://openvoiceos.github.io/ovos-docker)

Questa funzione si rivela essere utile soprattutto durante lo sviluppo delle skills, dove bisogna fare molte prove prima di creare un pacchetto.

```bash
ovos-skill-launcher {skill_id} [path/to/my/skill_id]
```

## Configurazione

```javascript
"skills": {

    // blacklisted skills to not load
    // NB: This is the skill_id, usually the basename() of the directory where the skill lives, so if
     // the skill you want to blacklist is in /usr/share/mycroft/skills/mycroft-alarm.mycroftai/
    // then you should write `["mycroft-alarm.mycroftai"]` below.
    "blacklisted_skills": [],

    // fallback skill configuration (see below)
    "fallbacks": {...},

    // converse stage configuration (see below)
    "converse": {...}

},
```

## Utterance transformers

**Introdotto** da `ovos-core` **v.0.0.8**

Originariamente sviluppato da <a>Neon.ai</a>, la <strong>utterance transformer</strong> permette a `ovos-core` di ricevere una richiesta in linguaggio naturale o una `utterance` da un utilizzatore, e di inviarla a un plugin di "pre trattamento".

Il framework dei `utterance` transformers è costituito da un numero qualsiasi di plugin ordinati per priorità (definita dallo sviluppatore). `utterance` e messaggio.`context` vengono quindi inviati a tutti i plugin che li analizzeranno in modo sequenziale.

![imagem](https://github.com/OpenVoiceOS/ovos-technical-manual/assets/33701864/75b67a91-dd8d-48cd-a146-91988bbbf374)

Per attivare un nuovo `utterance transformer` è sufficiente configurarlo in <code>mycroft.conf</code> dopo averlo installato

```javascript
// To enable a utterance transformer plugin just add it's name with any relevant config
// these plugins can mutate the utterance between STT and the Intent stage
// they may also modify message.context with metadata
// plugins only load if they are installed and enabled in this section
"utterance_transformers": {

 "ovos-utterance-normalizer": {},

 // cancel utterances mid command
 "ovos-utterance-plugin-cancel": {},
  
 // define utterance fixes via fuzzy match ~/.local/share/mycroft/corrections.json
 // define unconditional replacements at word level ~/.local/share/mycroft/word_corrections.json
 "ovos-utterance-corrections-plugin": {},
 
 // translation plugin
 "ovos-utterance-translation-plugin": {
   "bidirectional": true,
   "verify_lang": false,
   "ignore_invalid": true,
   "translate_secondary_langs": false
 }
},
```

## Metadata Transformers

**Introdotto** da `ovos-core` **v.0.0.8**

Come gli <strong>utterance transformers</strong>, questi plugins trasformano solo il `message.context`.

```javascript
// To enable a metadata transformer plugin just add it's name with any relevant config
// these plugins can mutate the message.context between STT and the Intent stage
"metadata_transformers": {},
```

## Pipelines

**Introdotto** da `ovos-core` **v.0.0.8**

I concetto di pipeline configurabile è stato introdotto con <code>ovos-core</code> <strong>v.0.0.8</strong> ed é pianificato per diventare completamente operativo con la versione <strong>0.1.0</strong>.

Le pipeline includono parser di intenti, framework di conversazione, di query comuni e di skills di fallback.

```javascript
  // Intent Pipeline / plugins config
  "intents" : {
    // the pipeline is a ordered set of frameworks to send an utterance too
    // if one of the frameworks fails the next one is used, until an answer is found
    // NOTE: if padatious is not installed, it will be replaced with padacioso (much slower)
    // in the future these will become plugins, and new pipeline stages can be added by end users
    "pipeline": [
        "stop_high",
        "converse",
        "padatious_high",
        "adapt_high",
        "fallback_high",
        "stop_medium",
        "padatious_medium",
        "adapt_medium",
        "adapt_low",
        "common_qa",
        "fallback_medium",
        "fallback_low"
    ]
  },
```

La maggior parte dei componenti della pipeline potrà essere configurata. Per esempio:

pipeline | descrizione | note
--- | --- | ---
stop_high | il comando di "stop" corrisponde esattamente | rimpiazza [OpenVoiceOS/skill-ovos-stop](https://github.com/OpenVoiceOS/skill-ovos-stop)
converse | intercettazione continua delle conversazioni |
padacioso_high | fidarsi ciecamente di padacioso | estremamente lento!!! <br> disabilitato
padatious_high | fidarsi ciecamente di padatious |
adapt_high | fidarsi ciecamente di adapt |
fallback_high | alta priorità della skill di ripiego |
stop_medium | il comando di "stop" corrisponde all'incirca | rimpiazza [OpenVoiceOS/skill-ovos-stop](https://github.com/OpenVoiceOS/skill-ovos-stop)
padacioso_medium | fidarsi così così di padacioso | estremamente lento!!! <br> disabilitato
padatious_medium | fidarsi così così di padatious |
adapt_medium | fidarsi così così di adapt |
adapt_low | fidarsi appena di adapt |
common_query | invia l'utterance alle skills di common_query e seleziona la corrispondenza migliore |
fallback_medium | media priorità della skill di ripiego |
stop_low | fidarsi appena del comando "stop" captato | disabilitato
padacioso_low | fidarsi appena di padacioso | estremamente lento!!! <br> disabilitato
padatious_low | fidarsi appena di padatious | sbaglia quasi sempre<br> disabilitato
fallback_low | bassa priorità della skill di ripiego | 
