# Versionamento dei rilasci
La piattaforma CUBA segue una numerazione semantica standard per le sue versioni stabili - major.minor.maintenance, ad esempio 7.2.4
Versione maggiore "7"
Versione minore "2"
Versione di manutenzione "4

## RILASCIO DI MANUTENZIONE
Una release di manutenzione è compatibile con la precedente, apportando per lo più correzioni di bug, piccoli miglioramenti, correzioni di prestazioni critiche e problemi di sicurezza. In rari casi, può includere alcune caratteristiche minori o miglioramenti delle API, che non introducono modifiche incompatibili.

## RILASCIO MINORE
Una release minore è per lo più compatibile con la precedente, tuttavia, può includere alcune modifiche incompatibili a livello funzionale e di API. Tutti i cambiamenti incompatibili sono elencati nelle [note di rilascio](https://www.cuba-platform.com/download/previous-platform/), così come un modo per risolverli. Spesso, le modifiche richieste nel codice sorgente e nelle configurazioni delle applicazioni sono applicate automaticamente da CUBA Studio dopo un aggiornamento della versione. Una release minore ha lo scopo di introdurre nuove funzionalità, mantenendo nel frattempo la procedura di aggiornamento facile e veloce.

## RILASCIO MAGGIORE
Una release maggiore è destinata a introdurre gli ultimi paradigmi di sviluppo, gli approcci e i miglioramenti nell'architettura e nelle funzionalità. In generale, introduce cambiamenti incompatibili nell'architettura di base, nelle caratteristiche funzionali, nelle API a livello di applicazione, nelle librerie sottostanti e nelle loro versioni. Gli aggiornamenti principali non hanno l'obbligo di essere retro compatibili; tuttavia, nelle nuove versioni principali, le API legacy non vengono rimosse ma vengono deprecate, il che significa che l'aggiornamento formale può essere eseguito in modo relativamente semplice.

# Versioni instabili e anteprime
Potreste anche aver bisogno di utilizzare versioni in fase di sviluppo:

## VERSIONE ISTANTANEA
è contrassegnata come
major.minor-SNAPSHOT.
È una build notturna del ramo di sviluppo. Tali versioni possono essere utilizzate per accedere in anticipo alle funzionalità più recenti.

## VERSIONE ALFA
è contrassegnata come
major.minor[.maintenance].ALPHAx
e funge da anteprima del prossimo rilascio.

## VERSIONE BETA
è contrassegnata come
major.minor[.maintenance].BETAx
e funge da anteprima finale e completa del prossimo rilascio.

# Termini di supporto

## 5 ANNI
La Manutenzione gratuita è prevista per 5 anni per ogni ultima versione minore. Ciò significa che i rilasci di manutenzione saranno pubblicati entro 5 anni dal rilascio dell'ultimo aggiornamento minore stabile per ogni versione principale della piattaforma CUBA. **La manutenzione gratuita** è prevista anche per 3 mesi per la precedente versione minore di ogni versione maggiore da quando viene rilasciata la nuova versione minore. Si presume che tale periodo sia sufficiente per aggiornare l'applicazione all'ultima versione minore.

Per esempio, la versione 100.8.x viene rilasciata il 1 settembre 3000. La penultima versione minore (100.7.x) sarà in manutenzione gratuita fino al 30 novembre 3000. La versione 100.8.x diventa l'ultima versione minore per la versione maggiore 100. Quindi, la versione 100.8.x sarà in manutenzione gratuita per altri 5 anni fino al 31 agosto 3005; o fino al rilascio della nuova versione minore (100.9.x).

Si prega di notare che, pur essendoci impegnati a rispettare questa politica di manutenzione gratuita, non è un'offerta legalmente vincolante e potrebbe eventualmente cambiare.

## 10 ANNI

La Manutenzione commerciale
è disponibile per qualsiasi versione minore della Piattaforma CUBA, rilasciata entro i 10 anni precedenti. Questa opzione potrebbe essere utile per progetti con un lungo ciclo di aggiornamento. Inoltre, rimanendo sull'ultima versione minore più di 5 anni, è anche possibile utilizzare questa opzione per estendere la manutenzione per altri 5 anni.

Potete saperne di più sul servizio di manutenzione commerciale [qui](https://www.haulmont.com/services/cuba-platform-services/support/).

## DOPO

La Fine della vita (EOL) 
di una versione avviene dopo 10 anni (3650 giorni) dalla data di rilascio. In altre parole, le versioni più vecchie di 10 anni non vengono mantenute nemmeno su base commerciale.