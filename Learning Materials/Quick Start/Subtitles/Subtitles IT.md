## Quick Start 

Ciao! Benvenuto nel Quick Start di CUBA Platform.

CUBA Platform è un framework open source basato su Spring, uno tra i framework web più famosi al mondo.

Lo scopo di CUBA è di semplificare il processo di sviluppo di applicazioni aziendali.

Questo video affronterà i seguenti argomenti:
- Creazione del modello dei dati e del database
- Sviluppo dell'interfaccia utente e sua personalizzazione
- Implementazione della logica di business

Come esempio, svilupperemo una semplice applicazione, ma perfettamente funzionante, per la pianificazione delle sessioni in una conferenza.

Grazie a questo video sarete già in grado di sviluppare la vostra prima applicazione CUBA.

In questo video, oltre al framework CUBA, useremo un plugin per IntelliJ Idea: CUBA Studio.

## Creazione Progetto

Creiamo un progetto CUBA vuoto usando il namespace "planner". Useremo Java 11 come JDK di default e l'ultima versione stabile di CUBA.

Useremo un database in-memory: HSQLDB. Questo dovrebbe essere sufficiente per questo quick start. È possibile selezionare un altro database dall'elenco a discesa. Lasciamo invariati i parametri di connessione al database.

Il nome del progetto sarà Session Planner.

Dopo il primo avvio, CUBA Studio offre la possibilità di sottoscrivere un abbonamento di prova a CUBA Studio Premium. È gratuito per 28 giorni e ci offre alcuni utili designer opzionali. Richiediamolo.

Sul lato sinistro dello schermo si può vedere l'albero di navigazione del progetto CUBA. Al centro c'è una pagina di benvenuto che consente di accedere rapidamente ad alcune funzioni di CUBA, alla documentazione, al marketplace, nonché alle versioni e alle informazioni relative all'abbonamento. 

Sul lato destro dello schermo si può vedere la finestra Gradle Tool. Il framework CUBA utilizza Gradle per compilare ed eseguire l'applicazione.

La finestra di compilazione standard di IntelliJ è in basso. Possiamo chiudere alcune finestre per avere un po' di spazio sullo schermo. Il menu principale di CUBA fornisce un accesso rapido alle caratteristiche specifiche di CUBA nell'IDE.

## Creazione Modello Dati

Adesso andremo a creare le classi JPA insieme alle regole di business come i campi obbligatori, i campi univoci e le relazioni.

Il modello di dominio prevede solo due classi (chiamate anche Entità): Speaker e Session.

Per semplificare il processo di sviluppo abbiamo aggiunto a CUBA Studio un visual designer che facilita la creazione di entità JPA. Cliccare con il tasto destro del mouse su " Data Model" e selezionare "New -> Entity".

Come prima cosa, dobbiamo creare l'entità Speaker. Questa avrà tre campi: first name (obbligatorio), last name (opzionale) ed email (obbligatorio e univoco). Aggiungiamo anche la validazione per il campo email.

CUBA permette di specificare come verrà visualizzata l'entità nell'interfaccia utente, sotto forma di stringa: l' "Instance Name". Per lo Speaker, selezioniamo sia il campo first name, sia last name.

Diamo un'occhiata più da vicino all'entity designer. Accanto alla scheda del designer possiamo vedere il testo generato - la classe java con le annotazioni JPA. Sono integrati, quindi è possibile modificare il testo e vedere tutti i cambiamenti nel designer e viceversa. Inoltre, è possibile vedere l'anteprima DDL e, se necessario, creare indici.

Per esempio, ci aspettiamo molte ricerche per cognome. Per rendere questa ricerca più efficiente possiamo creare un indice per il campo cognome.

Andiamo oltre e creiamo l'entità Session e colleghiamola alla nostra classe Speaker. Per prima cosa, definiamo l'attributo Topic - una stringa obbligatoria. Dopo di che, aggiungeremo la data di inizio della sessione e l'attributo time. Aggiungiamo la durata della sessione con la validazione. La data e l'ora di fine sessione saranno un valore calcolato che configureremo in seguito.

Adesso aggiungiamo un riferimento obbligatiorio allo Speaker. La relazione è di tipo many-to-one, quindi aggiungeremo un campo ASSOCIATION chiamato speaker, che fa riferimento alla classe Speaker.
 
In aggiunta creiamo anche un campo description, che conterrà la descrizione della sessione. Per il nome di istanza selezioniamo l'attributo topic al posto di  description.

## Aggiunta Campo Calcolato

Adesso dobbiamo creare un attributo calcolato automaticamente per la data e l'ora di fine della sessione. Per prima cosa aggiungiamo il metodo getter e lo chiamiamo getEndDate. Annotiamo il metodo con l'annotazione @MetaProperty. Per l'attributo calcolato, dobbiamo specificare i campi da caricare. Nel nostro caso, questi sono startDate e duration. Infine basta aggiungere la logica di calcolo. 

Si noti che Studio evidenzia il metodo perché dobbiamo specificare un'etichetta di testo per l'attributo. Aggiungiamolo al pacchetto dei messaggi. 

Ora torniamo al designer e diamo un'occhiata. Vediamo che il campo endDate è stato aggiunto ed è in sola lettura e gli attributi correlati sono specificati.

Il nostro modello di dominio è stato creato. Ora possiamo creare una semplice UI per eseguire operazioni CRUD sulle tabelle del database.

## Generazione Schermate CRUD

CUBA Studio rende disponibile un wizard per la creazione di interfacce utente di base:

* Browser - per mostrare la lista di entità in griglia
* Editor - per modificare una singola istanza di entità con un form generato automaticamente

Quindi procediamo a creare le schermate che gestiranno i relatori. Dato che questa entità è molto semplice, possiamo accettare i parametri di default che ci propone il wizard di creazione.

Come possiamo vedere da questi primi esempi, ogni schermata consiste di due parti: un file XML che definisce l'aspetto dell'interfaccia utente e visualizza l'anteprima, e il controller, scritto in Java, che contiene la logica interna della schermata e il codice che gestisce gli eventi.

Diamo un'occhiata alla struttura del layout dello schermo: ci sono due blocchi principali. Il primo definisce il modo in cui lavoriamo con i dati e il secondo blocco definisce il layout che specifica la posizione dei componenti sullo schermo.

Adesso creiamo una schermata di browse e una di edit per le sessioni. Prima di continuare dobbiamo soffermarci su alcuni concetti di CUBA.

In CUBA, una Entity View (Vista di Entità) definisce quali campi saranno letti dal database. È possibile definire le viste in un file separato per utilizzarle nei diversi moduli della propria applicazione, oppure creare viste in linea durante la creazione delle schermate.

Ora creiamo una vista in linea. Basta selezionare i dati necessari.

In questo modo i campi corrispondenti vengono aggiunti in automatico alle schermate.

Inoltre, impostiamo il valore di durata predefinito per la nuova sessione a un'ora. Per fare questo, andiamo nella schermata dell'editor e sottoscriviamo l'evento InitEntity, e impostiamo questo valore nel codice.

Ora dobbiamo generare gli script per creare il database.

## Creazione Database

Per generare gli script che servono a creare il database, dobbiamo selezionare il menù CUBA -> Generate Database Scripts.

Per applicare questi script e creare il database, basta selezionare il menù Create Database. Oltre alle tabelle applicative, CUBA crea anche delle tabelle di sistema dove memorizza informazioni quali gli utenti, i ruoli e le attività schedulate.

## Eseguire l'Applicazione in Modalità Sviluppo

Ora possiamo lanciare l'applicazione direttamente dall'IDE.

Premiamo il pulsante "Run" nell'IDE per lanciare l'applicazione. Potete vedere il log dell'applicazione nella finestra "Run" dell'IDE. Dopo aver atteso qualche secondo la compilazione, possiamo accedere all'applicazione nel browser. Clicchiamo sull'URL visualizzato e accediamo all'applicazione usando il nome utente "admin". La password di default è "admin".

Aggiungiamo dei dati di test all'applicazione: per primi un paio di relatori

Come potete vedere, la validazione del campo email funziona come ci aspettavamo.

Adesso aggiungiamo un paio di sessioni per oggi e domani. Vedete che il campo endDate è stato calcolato in automatico.

La generazione automatica delle schermate è ottima per le operazioni di base, ma nelle applicazioni reali le interfacce utente sono più complesse. Proviamo ad aggiungere un'interfaccia per visualizzare le sessioni in un calendario, in aggiunta alla griglia.

## Personalizzazione Interfaccia Utente

Ora aggiungeremo un controllo a schede nella schermata di browse, ci metteremo un controllo calendario, e implementeremo la logica per modificare e rischedulare le sessioni usando questo controllo.

Racchiudiamo la tabella delle sessioni in un tab sheet nella schermata di browse delle sessioni. Aggiungiamo una nuova scheda e inseriamo un controllo calendario.

Espandiamo l'elemento TabSheet a tutto lo schermo. Studio chiede un id. In CUBA, abbiamo bisogno di ID per fare riferimento a un elemento dello schermo nel codice.

Inoltre, impostiamo gli id e le didascalie per ogni tab.

Modifichiamo il calendario - assegniamo un contenitore di dati ed espandiamolo.

Infine, espandiamo la tabella delle sessioni.

In CUBA, i componenti di interfaccia sono collegati alle entità e alle loro proprietà. 

Collegheremo il calendario alla collezione di dati caricata nella schermata. Usiamo il campo di ricerca per trovare le proprietà da collegare:

* startDateProperty al campo startDate di una Session
* endDateProperty al campo endDate di una Session
* captionProperty al campo topic di una Session
* e infine descriptionProperty al campo description di una Session

Facciamo in modo che il calendario visualizzi solo le ore lavorative.

Oltre al designer visuale, possiamo modificare direttamente il codice XML della schermata nell'editor testuale. Aggiungiamo dei pulsanti di navigazione.

Ora possiamo riaprire la schermata nel browser per controllare le modifiche - CUBA supporta l' "hot reload" delle schermate dopo ciascuna modifica. Adesso possiamo vedere le sessioni visualizzate all'interno del calendario.

## Usare le Screen API

Adesso facciamo in modo di aprire la schermata di modifica per una sessione quando facciamo click su un evento nel calendario.

Quando un utente interagisce con l'interfaccia, vengono generati degli eventi. Possiamo rispondere a questi eventi gestendoli da codice. Ora gestiamo il click su un evento del calendario.

Dobbiamo richiamare la schermata dell'editor per modificare le proprietà della sessione. Utilizziamo l'EditorScreenFacet. Si tratta di un componente che permette di pre-configurare una schermata di modifica.

Mettiamolo sotto l'elemento window nella finestra della Gerarchia dei componenti. E impostiamo le proprietà:

* ID
* Abbiamo bisogno di un editor per la classe session. Impostiamo la classe Entity "session" e il corrispondente contenitore di dati. 
* Classe della schermata: SessionEdit.
*  La schermata verrà aperta come popup.
* Impostiamo la modalità di modifica.

Torniamo al gestore dell'evento. Iniettiamo l'EditorScreenFacet. Passiamo l'entità session ricevuta all'interno dell'oggetto evento al facet. Infine dobbiamo mostrare l'editor.

Ora proviamo a riaprire la schermata nel browser e aprire l'editor.

Come potete vedere, dobbiamo ritoccare l'aspetto della finestra di modifica, aggiustando la larghezza e l'altezza. Il modo più semplice per farlo è impostare "auto" sia per la larghezza che per l'altezza.

Proviamo a riaprire la schermata per controllare le modifiche appena effettuate.
Ok, adesso è arrivato il momento di aggiungere la logica di business alla nostra applicazione.

## Sviluppo Logica di Business

In questa sezione useremo CUBA Studio per creare un servizio che implementerà la logica di business, e useremo questo servizio in una schermata. Il servizio fornirà una funzionalità per rischedulare una sessione, in modo che un relatore non possa avere più di una sessione nella stessa giornata.

Facciamo tasto destro sul nodo Service nel progetto CUBA e creiamo un'interfaccia SessionService e una classe di implementazione SessionServiceBean. Creiamo inoltre un metodo nell'interfaccia e implementiamolo nella classe SessionServiceBean.

Il metodo accetterà in ingresso una sessione e una nuova data e ora per la stessa. Il metodo restituirà l'istanza Session aggiornata.

Per prima cosa, calcoliamo l'ora di inizio e di fine nella giornata in cui è prevista la sessione.

Utilizzeremo le API CUBA per l'accesso ai dati - la classe DataManager. 

Con DataManager creiamo una query JPQL per verificare se ci sono sessioni programmate per il relatore in un intervallo di tempo definito, e aggiungiamo i valori dei parametri. 

Poi controlliamo il risultato della query e, a seconda del risultato, aggiorniamo la sessione con una nuova data di inizio o semplicemente restituiamo l'istanza originale della sessione.

## Chiamare i Servizi dalle Schermate

Il servizio è pronto, adesso richiamiamolo dalla schermata di browse delle sessioni. Sarà invocato quando l'utente farà drag-and-drop di un evento all'interno del calendario.

Nel metodo che ha sottoscritto l'evento di drag-and-drop, aggiungiamo del codice per estrarre l'entità Session dall'evento del calendario e passiamolo al servizio per rischedulare la sessione. Quindi aggiorniamo l'entità nel data container della schermata di browse.

Per poter utilizzare il nuovo servizio, dobbiamo riavviare l'applicazione.

Dopo averla riavviata possiamo aprire di nuovo il calendario delle sessioni - e voilà! Il drag-and-drop degli eventi nel calendario è funzionante! Vediamo se funziona davvero.. Aggiungiamo un paio di nuove sessioni e proviamo a rischedulare una di queste in una giornata già occupata.

## Branding

In questa sezione andremo a personalizzare le etichette standard dell'applicazione.

In CUBA, possiamo modificare i file di risorsa contenenti il testo visualizzato nell'applicazione. Modifichiamo quindi i testi per il nostro dominio di business - la pianificazione delle conferenze.

Grazie alla funzionalità "Hot Deploy" di CUBA, tutto quello che dobbiamo fare è loggarci nuovamente nell'applicazione, e le nostre modifiche saranno già applicate.

## Marketplace

Il framework dispone anche di un marketplace che contiene molti componenti pronti all'uso, che possono essere aggiunti alle nostre applicazioni, fornendo funzionalità quali grafici o mappe.

Possiamo installare questi componenti, senza lasciare CUBA Studio, utilizzando il menù "Marketplace".

Aggiungiamo l'add-on Helium. È un nuovo tema visuale che si può usare al posto dei temi standard. Basta fare clic su installa e applicare le modifiche.

Ora dobbiamo fermare l'applicazione e applicare gli script di init del componente aggiuntivo.

Eseguiamo l'applicazione e andiamo alla schermata delle impostazioni. Troveremo il tema appena aggiunto nell'elenco a discesa. Selezioniamolo e applichiamolo. Usciamo e rientriamo nell'applicazione - il tema è già applicato. Possiamo anche aprire la schermata delle impostazioni del tema e modificare le impostazioni con l'anteprima in tempo reale.

## Conclusioni

Il framework CUBA fornisce una vasta gamma di API per aiutarci a creare applicazioni aziendali molto velocemente. Questo quickstart ha mostrato solo le funzionalità di base del framework e di CUBA Studio.

Potrete trovare molti altri esempi e tutorials sul nostro sito web: cuba-platform.it

Grazie per averci seguito!

# Applicazione. Didascalie

Quick start

Un framework Java full stack per lo sviluppo di applicazioni aziendali

Powered by

Creazione Modello Dati e Database

Sviluppo Interfaccia Utente

Implementazione Logica di Business

Grazie per averci seguito