# Canali di rilascio

OVOS è un sistema **modulare** , il che significa che non devi installare tutti i suoi componenti. A seconda delle tue esigenze, puoi installare solo i moduli specifici che desideri, risparmiando sia spazio che dipendenze. I diversi componenti di OVOS sono raggruppati in **extra** , ovvero set di funzionalità opzionali che puoi scegliere di installare.

> ⚠️ i file di vincoli sono considerati stabili solo dopo la prima [versione del nome in codice](https://github.com/OpenVoiceOS/ovos-releases/issues/5) , i file di vincoli sono un lavoro in corso e soggetti a modifiche

OVOS è costituito da centinaia di pacchetti con il proprio ciclo di rilascio, durante la versione **0.0.7** o precedente `ovos-core` OVOS era un singolo repository forkato da `mycroft-core` , dalla versione **0.0.8** i servizi sono stati suddivisi nei propri repository

### Distribuzioni OVOS

Le distribuzioni OVOS sono progetti che forniscono OVOS, ma hanno opinioni diverse su quali componenti e competenze dovrebbero essere disponibili per impostazione predefinita e preconfigurati.

- [**raspOVOS**](https://github.com/OpenVoiceOS/raspOVOS) - immagine rasperry pi
- [**ovos-installer**](https://github.com/OpenVoiceOS/ovos-installer) : installa OVOS su più OVOS
- [**ovos-buildroot**](https://github.com/OpenVoiceOS/ovos-buildroot) - in fase di sviluppo

Queste distribuzioni forniscono un modo più semplice per iniziare a usare OVOS su hardware o piattaforme specifici. Le configurazioni sono preimpostate con servizi comunemente usati, rendendo più veloce l'implementazione di OVOS su dispositivi diversi.

### Canali di rilascio

OVOS segue [**il versioning semantico**](https://semver.org/) (SemVer) e un **modello di rilascio progressivo** con tre canali di rilascio principali: **stabile** , **di test** e **alpha** .

Questi canali sono gestiti tramite i [file di vincoli](https://pip.pypa.io/en/stable/user_guide/#constraints-files) ospitati nel repository [ovos-releases](ttps://github.com/OpenVoiceOS/ovos-releases)

> ℹ️ I file di vincoli sono simili a `requirements.txt` , ma specificano *le versioni consentite* invece delle *versioni richieste* , i pacchetti nei file di vincoli non vengono installati automaticamente

1. **Canale stabile**

    - Il canale di rilascio **stabile** include **solo correzioni di bug** , nessuna modifica dirompente o nuove funzionalità. È sicuro per l'uso generale.
    - **Installazione** : utilizzare il file `constraints-stable.txt` per installare le versioni stabili.

2. **Canale di prova**

    - Il canale di rilascio **di prova** include **correzioni di bug e nuove funzionalità** , ma potrebbe non essere testato in modo così approfondito come le versioni stabili.
    - **Installazione** : utilizzare il file `constraints-testing.txt` per installare le versioni di test.

3. **Canale alfa**

    - Il canale **alfa** include le ultime funzionalità sperimentali che sono **ancora in fase di sviluppo** . Non sono consigliate per l'uso in produzione.
    - **Installazione** : utilizzare il flag `--pre` per installare le versioni alpha.

### Comandi di installazione per ogni canale

#### Installazione di rilascio stabile

Per installare la versione stabile con gli extra desiderati, utilizzare:

```bash
pip install ovos-core[mycroft] -c constraints-stable.txt
```

#### Installazione di rilascio di prova

Per installare la versione di prova con gli extra desiderati, utilizzare:

```bash
pip install ovos-core[mycroft] -c constraints-testing.txt
```

#### Installazione della versione Alpha

Per installare l'ultima versione alpha con gli extra desiderati, utilizzare:

```bash
pip install ovos-core[mycroft] --pre
```

### OVOS da zero

Invece di utilizzare le distribuzioni, puoi personalizzare OVOS installando manualmente solo i servizi selezionati rilevanti per il tuo caso d'uso

- **[messagebus](https://github.com/OpenVoiceOS/ovos-messagebus)** - fornisce un livello di comunicazione tra tutti i servizi
- **[core](https://github.com/OpenVoiceOS/ovos-core)** - gestisce tutto ciò che riguarda le competenze
- **[audio](https://github.com/OpenVoiceOS/ovos-audio)** - gestisce tutto ciò che riguarda l'uscita audio (TTS, suoni, musica...)
- **[listener](https://github.com/OpenVoiceOS/ovos-dinkum-listener)** - gestisce tutto ciò che riguarda l'input audio (WakeWord, VAD, STT...)
- **[gui](https://github.com/OpenVoiceOS/ovos-gui)** - fornisce informazioni sull'interfaccia utente per le app client GUI (ad esempio, ovos-shell)
- **[PHAL](https://github.com/OpenVoiceOS/ovos-PHAL)** - Plugin di astrazione piattaforma/hardware

Ad esempio, se stai configurando un server Hivemind, puoi omettere lo stack audio per risparmiare risorse.

### Cosa sono gli OVOS Extra?

I pacchetti OVOS sono divisi in diversi **extra** che definiscono i componenti che desideri installare. Ogni extra è un gruppo di funzionalità correlate e puoi scegliere quali installare in base al tuo caso d'uso. Ad esempio:

- **mycroft** : include tutti i servizi individuali, equivalenti all'architettura monolitica Mycroft-core (ad esempio, ovos-audio, ovos-dinkum-listener, ovos-gui, ovos-PHAL, ovos-messagebus).
- **lgpl** : include dipendenze facoltative con licenza LGPL (ad esempio, Padatious).
- **plugin** : include vari plugin per funzionalità aggiuntive.
- **skills-essential** : Include competenze essenziali.
- **skills-audio** : include competenze che richiedono capacità di input/output audio.
- **skills-gui** : include le competenze che richiedono un'interfaccia grafica.
- **skills-internet** : Include competenze che richiedono una connessione a Internet.
- **skills-media** : include competenze OCP (riproduzione multimediale).
- **skills-desktop** : include competenze relative al desktop.

Per **un'installazione completa** di OVOS con tutti i moduli opzionali, puoi utilizzare il seguente comando:

```bash
pip install ovos-core[mycroft,lgpl,plugins,skills-essential,skills-audio,skills-gui,skills-internet,skills-media,skills-desktop]
```

Tuttavia, **non è necessario installare tutto** . Puoi personalizzare la tua installazione selezionando solo gli extra di cui hai bisogno, a seconda delle funzionalità che vuoi utilizzare.

Ad esempio, se desideri funzionalità minime, puoi installare solo quelle:

```bash
pip install ovos-core[mycroft,plugins,skills-essential]
```

Questa flessibilità consente di adattare l'installazione alle proprie esigenze, senza componenti non necessari.

### Riepilogo

- **OVOS è modulare** : puoi scegliere quali componenti (extra) installare in base alle tue esigenze.
- **Canale stabile** : solo correzioni di bug (utilizzare `constraints-stable.txt` ).
- **Canale di test** : correzioni di bug e nuove funzionalità (utilizzare `constraints-testing.txt` ).
- **Canale Alpha** : ultime funzionalità sperimentali (usa `--pre` ).
- Utilizza il comando base o personalizza l'installazione in base alle tue esigenze selezionando solo gli extra necessari.
