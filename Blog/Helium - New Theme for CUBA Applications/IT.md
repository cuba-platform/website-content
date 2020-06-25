Siamo lieti di presentare il nostro nuovo tema - Helium! Helium è molto più pulito e luminoso con meno interferenze visive rispetto ai suoi predecessori. Il tema viene fornito con un editor che permette di creare rapidamente preset di colori personalizzati.

CUBA non ha mai smesso di concentrarsi sullo sviluppo di applicazioni aziendali. Le applicazioni aziendali prevedono normalmente un’interfaccia utente per il personale di back-office, dove il fattore più importante è la sua funzionalità. Per molti anni l'aspetto non è stata la preoccupazione principale per le interfacce di back-office. I controlli erano piuttosto piccoli per un unico motivo: inserirne il maggior numero possibile in un unico schermo, rendendolo il più funzionale possibile.

Nell'ultimo decennio sono entrati in gioco sempre più dispositivi touch-screen con schermi piccoli. Questo ha cambiato le regole del gioco. I controlli dovrebbero essere facili da toccare, mantenendo una spaziatura sufficiente. I colori sono più vivaci e contrastanti. Lo stile è diventato una priorità molto più importante di prima, ricoprendo una parte significativa dei requisiti non funzionali in qualsiasi applicazione moderna.

Seguendo queste tendenze, siamo lieti di presentare il nostro nuovo tema - Helium! Helium è molto più pulito e luminoso con meno interferenze visive rispetto ai suoi predecessori. Un'altra cosa importante del nuovo tema è la possibilità di personalizzare lo stile con il minimo sforzo, sia per gli utenti finali che per gli sviluppatori CUBA.

## Funzionalità
Prima di tutto, il nuovo tema è molto dinamico, quindi è possibile cambiare lo stile dell'applicazione al volo! In precedenza, CUBA utilizzava le variabili SCSS per definire i colori e questo approccio richiedeva una ricompilazione ogni volta che venivano modificate. Il nuovo tema si basa sulle [proprietà personalizzate](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties) dei CSS, che vengono applicate immediatamente in fase di esecuzione, anche senza aggiornare o ricaricare la pagina.

![text](http://192.168.45.58:1342/uploads/22fb189514904f158dd9206932b06bad.gif)

### Colori Predefiniti
Ci sono due combinazioni di colore predefinite - chiara e scura.

Per gestire le combinazioni colore disponibili per gli utenti finali è sufficiente modificare le seguenti proprietà nel file theme.properties (la procedura di installazione del tema è descritta nella sezione omonima qui sotto):

```
cuba.theme.modes = light|dark
cuba.theme.defaultMode = light
```

Inoltre, è possibile creare combinazioni colore personalizzate usando l’editor online (maggiori dettagli nella sezione Demo Online ed Editor).

### Dimensioni Predefinite
Ci sono tre combinazioni predefinite per le dimensioni dei controlli: piccola, media e grande:

Come per le combinazioni colore, si possono modificare le dimensioni predefinite nel file theme.properties modificando le seguenti proprietà:

```
cuba.theme.sizes = small|medium|large
cuba.theme.defaultSize = medium
```
### Schermata Impostazioni
La schermata delle impostazioni si trova nel menu principale alla voce Help -> Theme Settings. Essa aiuterà ogni utente finale a personalizzare le proprie combinazioni di colore e dimensioni. La schermata illustra tutti i controlli principali, in modo che gli utenti possano vedere in anteprima l'aspetto della loro applicazione.

## [Demo Online ed Editor](https://demo10.cuba-platform.com/helium-editor/)

![text](http://192.168.45.58:1342/uploads/d3e3e54da9cb4afa983ca7e6269455b7.png)

Un'altra caratteristica importante del nuovo tema è il suo nuovo editor interattivo [disponibile online](https://demo10.cuba-platform.com/helium-editor/). Qui si può giocare con le variabili di stile disponibili e vedere subito il risultato - una volta che il tema soddisfa le vostre esigenze, basta premere il pulsante di download e seguire le istruzioni fornite per applicarlo nella vostra applicazione CUBA.

La parte migliore dell'editor online è che non è riservato esclusivamente agli sviluppatori. Basta inviare questo link ad un cliente avanzato, in modo che possa personalizzare i colori in base alle sue preferenze e inviarvi il risultato, che può essere applicato in pochi minuti nella vostra applicazione.

Per applicare una combinazione personalizzata si deve creare un [tema personalizzato](https://doc.cuba-platform.com/studio/#generic_ui_themes) basato su Helium.

## Installazione
Il tema è distribuito come [add-on](https://www.cuba-platform.it/marketplace/helium/). Il componente aggiuntivo è compatibile con le applicazioni CUBA a partire dalla versione 7.1.5 (si noti che raccomandiamo sempre di seguire i nostri rilasci di bugfix e di aggiornare l'applicazione all'ultima versione). Trovate la guida all'installazione dei componenti aggiuntivi in [questo capitolo](https://doc.cuba-platform.com/studio/#add_ons) della documentazione di Studio.

Una volta installato il componente aggiuntivo, è possibile abilitare il nuovo tema nei seguenti modi:
Avviare l'applicazione e aprire la schermata delle impostazioni disponibile nel menu principale: Aiuto -> Impostazioni. Scegliere Helium nel campo Tema.

Per rendere disponibile Helium di default aggiungi 

`cuba.web.theme = helium` 

nel file `web-app.properties`.

Se avete impostato la proprietà personalizzata `cuba.themeConfig`, non dimenticate di includere le proprietà di Helium:

`cuba.themeConfig = +/com/haulmont/addon/helium/web/helium-theme.properties`.

Se volete personalizzare **le proprietà del tema**, create il file `helium-theme.properties` nel vostro package principale nel modulo web con il seguente contenuto:

```
@include=com/haulmont/addon/helium/web/helium-theme.properties

cuba.theme.modes = light|dark
cuba.theme.defaultMode = light

cuba.theme.sizes = small|medium|large
cuba.theme.defaultSize = medium
```

Inoltre dovete aggiungere questo file di proprietà nella proprietà `cuba.themeConfig`:

```
cuba.themeConfig = +/com/example/project/helium-theme.properties
```

Se è necessario fare personalizzazioni più avanzate, è preferibile creare un tema personalizzato. Seguite le seguenti [istruzioni](https://github.com/cuba-platform/helium-theme#applying-custom-color-preset).

## Roadmap

Stiamo progettando di evolvere il tema Helium e di introdurre nuove funzionalità, tra cui:
- Miglioramenti del tema scuro
- Altri template nell'editor di temi
- Commutazione automatica tra tema chiaro e scuro
- Raggio dei bordi personalizzabile, anche in base alle variabili CSS
- Kit di componenti CUBA per Figma. Figma è uno degli strumenti più utilizzati dai designer di UI/UХ, usato per realizzare i mock-up delle schermate.

## Conclusioni

Ci auguriamo che il nuovo tema e il suo editor live siano utili per i nuovi progetti e anche per chi volesse portare una boccata d'aria fresca nelle proprie applicazioni. Se avete domande, potete creare un argomento sul nostro [forum di supporto](https://www.cuba-platform.it/discuss/). Il codice sorgente è disponibile su GitHub: [tema](https://github.com/cuba-platform/helium-theme) / [editor](https://github.com/cuba-platform/helium-theme-editor). Non vediamo l'ora di ricevere il vostro feedback!
