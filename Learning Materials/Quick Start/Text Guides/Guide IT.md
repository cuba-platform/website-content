<div id="chapter0" class="section"></div>

## Introduzione

La piattaforma CUBA è un framework open-source destinato a snellire il processo di sviluppo delle applicazioni aziendali. Combina un'architettura collaudata, componenti di livello enterprise pronti all'uso e strumenti di produttività, in modo da poter realizzare applicazioni web moderne più velocemente che mai. 

In questo Quick Start, gratteremo la superficie di CUBA e svilupperemo un'applicazione per la pianificazione di conferenze molto semplice, ma completamente funzionale. Mostrerà quattro attività principali per la creazione di qualsiasi applicazione web: come progettare un modello di dati, come manipolare i dati, come creare una logica di business e, infine, come creare un'interfaccia utente. Come esempio creeremo un'applicazione che aiuterà a pianificare le sessioni di una conferenza e, grazie a questo tutorial, avrete le competenze sufficienti per iniziare a sviluppare la vostra prima applicazione CUBA. Useremo <a target="_self" href="/tools/">CUBA Studio</a> nel corso del tutorial, quindi installatelo prima di iniziare, e accettate un abbonamento di prova per avere accesso ai designer visuali.  

Repository di esempio: [https://github.com/cuba-platform/sample-session-planner](https://github.com/cuba-platform/sample-session-planner).
<div id="chapter1" class="section"></div>

## Creazione di un progetto vuoto

<div class="timestamp-wrapper">
   <a id="stamp1" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">0:48</div>
  </div></a>
</div>

Creiamo un progetto CUBA vuoto utilizzando il corrispondente menu Intellij IDEA. Chiamiamolo SessionPlanner e usiamo il namespace planner. Useremo Java 11 come JDK di default. 

![](/images/learn/v14/qs-screen1.png)

Useremo un database in-memory - HSQLDB.

![](/images/learn/v14/qs-screen2.png)

Dopo il primo lancio CUBA Studio vi offre la possibilità di richiedere un abbonamento di prova a CUBA Studio Premium.

![](/images/learn/v14/qs-screen3.png)

Applichiamo un abbonamento di prova. È gratuito per 28 giorni e fornisce alcuni utili designer opzionali.

<div id="chapter2"  class="section"></div>

## Creazione Modello Dati

<div class="timestamp-wrapper">
   <a id="stamp2" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">2:29</div>
  </div></a>
</div>

Il primo compito è quello di creare le entità. Il modello di dominio aziendale ha solo due classi: Speaker e Session. Il rapporto è uno a molti. Un relatore può condurre molte sessioni. 

![](/images/learn/v14/qs-screen4.png)

Per cominciare, creiamo l'entità ```Speaker```. Per fare questo, possiamo usare un link nella pagina di benvenuto del progetto:

![](/images/learn/v14/qs-screen5.png)

Oppure basta cliccare con il tasto destro del mouse sul nodo **Data Model** in CUBA Project Tree sul lato sinistro dello schermo e selezionare **New -> Entity**.

![](/images/learn/v14/qs-screen6.png)

Inserire il nome dell'entità - ```Speaker``` e creare gli attributi secondo le seguenti specifiche:

<div class="scrollable-table ">
<table>

<tbody>

<tr>

<td>Name</td>

<td>Type</td>

<td>Mandatory</td>

<td>Other constraints</td>

</tr>

<tr>

<td>firstName</td>

<td>String (255)</td>

<td>Yes</td>

<td></td>

</tr>

<tr>

<td>lastName</td>

<td>String (255)</td>

<td></td>

<td></td>

</tr>

<tr>

<td>email</td>

<td>String (1024)</td>

<td>Yes</td>

<td>“Email” validator</td>

</tr>

</tbody>

</table>
</div>

In CUBA utilizziamo entità JPA standard, quindi è possibile crearle utilizzando sia l'editor di codice che il visual designer. Basta cliccare sull'icona "+" e aggiungere gli attributi all'entità, CUBA Studio genererà per voi i membri della classe.

![](/images/learn/v14/qs-screen7.png)

In CUBA, è possibile specificare un formato per una corretta visualizzazione dell'entità come stringa nell'interfaccia utente utilizzando <b >Instance Name</b>. Per lo speaker, selezioneremo sia il nome che il cognome.

![](/images/learn/v14/qs-screen8.png)

Se diamo un'occhiata alla scheda **Text** in fondo all'entity designer, possiamo vedere una semplice classe Java con annotazioni JPA. Il codice generato può essere modificato se necessario, il designer sarà aggiornato di conseguenza quando si passa alla scheda <b>Designer</b>.

Inoltre, si può vedere l'Anteprima DDL e si possono creare indici se necessario.
Per esempio, ci aspettiamo molte ricerche per cognome. Per rendere questa ricerca più efficiente possiamo creare un indice per il campo cognome.

![](/images/learn/v14/qs-screen9.png)

Andiamo oltre e creiamo l'entità ```Session``` e colleghiamola alla nostra classe ```Speaker```. Segue la tabella delle specifiche dei campi. L'ora di fine sessione sarà un valore calcolato, ovvero la data di inizio più la durata.

<div class="scrollable-table ">
<table>

<tbody>

<tr>

<td>Name</td>

<td>Type</td>

<td>Mandatory</td>

</tr>

<tr>

<td>topic</td>

<td>String (255)</td>

<td>Yes</td>

</tr>

<tr>

<td>startDate</td>

<td>LocalDateTime</td>

<td>Yes</td>

</tr>

<tr>

<td>endDate</td>

<td>LocalDateTime</td>

<td></td>

</tr>

<tr>

<td>speaker</td>

<td>Associazione all'entità “Speaker”, cardinalità many-to-one</td>

<td>Yes</td>

</tr>
<tr>

<td>duration</td>

<td>Integer</td>

<td>Yes</td>

</tr>
<tr>

<td>description</td>

<td>String (2000)</td>

<td></td>

</tr>

</tbody>

</table>
</div>

Vediamo l'aggiunta di un riferimento obbligatorio allo speaker. La relazione è molti-a-uno, quindi definiremo un campo di associazione chiamato speaker che fa riferimento alla classe di Speaker. Alla fine, la definizione del campo dovrebbe corrispondere a questa:

![](/images/learn/v14/qs-screen10.png)

<div id="chapter3" class="section"></div>

## Creazione Attributo Calcolato

<div class="timestamp-wrapper">
   <a id="stamp3" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">5:01</div>
  </div></a>
</div>

Ora, dobbiamo creare un attributo calcolato automaticamente per la data e l'ora di fine della sessione. Per prima cosa aggiungere il metodo getter e chiamarlo ```getEndDate```. Marcare il metodo con l'annotazione ```@MetaProperty```. Per l'attributo calcolato, dobbiamo specificare i campi da caricare. Nel nostro caso, questi sono ```startDate``` e ```duration```. Infine basta aggiungere la logica di calcolo.

```java
@Transient
@MetaProperty(related = {"startDate", "duration"})
public LocalDateTime getEndDate() {
   return (startDate != null && duration != null) ? startDate.plusHours(duration) : null;
}
```    

Si noti che Studio evidenzia il metodo perché dobbiamo specificare un'etichetta di testo per l'attributo. Premere **Alt+Invio** e aggiungere un testo al bundle di messaggi.

![](/images/learn/v14/qs-screen11plus.png)

Ecco fatto. Il nostro modello di dominio è stato creato.

<div id="chapter4" class="section"></div>

## Creazione delle Schermate CRUD

<div class="timestamp-wrapper">
   <a id="stamp4" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">06:00</div>
  </div></a>
</div>

CUBA Studio comprende una procedura guidata per la generazione di schermate UI che ci aiuta a creare le seguenti tipologie di schermate:

* Browser - per mostrare la lista delle entità in una griglia
* Editor - per modificare un'istanza di entità utilizzando un'interfaccia utente simile a quella di un modulo

Per prima cosa, creeremo delle schermate per lavorare con gli speakers. Poiché questa entità è piuttosto semplice, useremo i parametri di default per la creazione delle schermate. Avviare una procedura guidata cliccando sulla voce di menu **Create Screen** nel menu **Screens** nella parte superiore dell'entity designer.

![](/images/learn/v14/qs-screen11.png)

È inoltre possibile avviare la procedura guidata di generazione delle schermate cliccando con il tasto destro del mouse su un'entità nell'albero del progetto CUBA e selezionando **New -> Screen** dal menu contestuale.

![](/images/learn/v14/qs-screen12.png)

Per l'entità ```Speaker```, creeremo un browser e un editor di default.  Selezionare <b>Entity browser and editor screens</b> nella scheda <b>Screen Templates</b> nella procedura guidata e fare clic su <b>Next</b>. Poiché questa entità è piuttosto semplice, possiamo accettare i parametri di default per la creazione delle schermate. 

![](/images/learn/v14/qs-screen13.png)

Come si può vedere, ogni schermata è composta da due parti: un controllore, scritto in Java, che è responsabile della logica interna della schermata e della gestione degli eventi e un layout XML che definisce l'aspetto della schermata. Nel nostro caso, il browser sarà composto dai file ```speaker-browse.xml``` e ```SpeakerBrowse.java``` ed editor - ```speaker-edit.xml``` e ```SpeakerEdit.java```. I file sorgente si trovano sotto il menu **Generic UI -> Screens** nell'albero del progetto CUBA.

![](/images/learn/v14/qs-screen14.png)

Si prega di notare la sezione ```data``` nei descrittori delle schermate XML - essa definisce come i dati vengono recuperati dal database.

```java
<data readOnly="true">
   <collection id="speakersDc"
               class="com.company.planner.entity.Speaker"
               view="_local">
       <loader id="speakersDl">
           <query>
               <![CDATA[select e from planner_Speaker e]]>
           </query>
       </loader>
   </collection>
</data>
```

Con CUBA Studio è possibile navigare facilmente tra il controllore dello schermo, il descrittore e le entità collegate utilizzando i pulsanti nella parte superiore della finestra:

![](/images/learn/v14/qs-screen15.png)

![](/images/learn/v14/qs-screen16.png)

![](/images/learn/v14/qs-screen17.png)

<div id="chapter5" class="section"></div>

## Creazione di un browser e un editor per le sessioni

<div class="timestamp-wrapper">
   <a id="stamp5" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">07:11</div>
  </div></a>
</div>

Eseguire la procedura guidata di generazione delle schermate, selezionare <b>Entity browser and editor screens</b> e fermarsi al passo <b>Entity browser view</b>.

Nella piattaforma CUBA, Entity View specifica quali campi verranno recuperati dal database. È possibile definire le viste in un file separato per utilizzarle nei diversi moduli della propria applicazione, oppure creare viste in linea durante la creazione delle schermate.

Creiamo una vista in linea. Basta selezionare i dati necessari: data di fine e riferimento dello speaker.

![](/images/learn/v14/qs-screen18.png)

Nella fase successiva i campi necessari sono già selezionati.

![](/images/learn/v14/qs-screen19.png)

Si può vedere che i campi corrispondenti vengono aggiunti alle schermate. 

![](/images/learn/v14/qs-screen20.png)

Inoltre, impostare il valore di durata predefinito per la nuova sessione su un'ora. Per fare questo, andare alla finestra <b>Component Inspector</b> e sottoscrivere l'evento ```InitEntity``` nella scheda Handlers.   

![](/images/learn/v14/qs-screen21.png)

Quindi impostare questo valore nel codice.

```java
@Subscribe
public void onInitEntity(InitEntityEvent<Session> event) {
   event.getEntity().setDuration(1);
}
```

<div id="chapter6" class="section"></div>

## Creazione Database

<div class="timestamp-wrapper">
   <a id="stamp6" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">8:11</div>
  </div></a>
</div>

Selezionare il menu <b>CUBA -> Generate Database Scripts</b> per generare gli script SQL per la creazione del database. È possibile rivedere gli script generati nella finestra pop-up prima di salvarli come file di progetto. Si prega di notare che questi script fanno parte del progetto, li potete trovare sotto il nodo <b>Main Data Store</b> nell'albero del progetto.

![](/images/learn/v14/qs-screen22.png)

È possibile modificare gli script se si desidera aggiungere alcune cose specifiche come indici aggiuntivi o istruzioni INSERT per i dati iniziali. 

![](/images/learn/v14/qs-screen23.png)

Fare clic sul pulsante <b>Create database</b> per applicare questi script e creare il database.  Oltre alle tabelle di applicazione, CUBA crea tabelle di sistema in cui memorizza le informazioni sugli utenti, i ruoli, i task, ecc.

Questo è tutto. Il database è stato creato.

<div id="chapter7" class="section"></div>

## Esecuzione dell'applicazione in modalità sviluppo

<div class="timestamp-wrapper">
   <a id="stamp7" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">8:39</div>
  </div></a>
</div>

Per eseguire l'applicazione CUBA si può usare sia la barra in alto nella finestra IDE.

![](/images/learn/v14/qs-screen24.png)

Oppure selezionare **CUBA -> Start Application Server** dal menu principale. 

![](/images/learn/v14/qs-screen25.png)

Dopo un po' di tempo è possibile accedere all'applicazione utilizzando il browser. L'URL verrà visualizzato nella toolbox Run in IDEA. Nel nostro caso sarà http://localhost:8080/app . Aprite l'URL nel vostro browser e accedete all'applicazione usando "admin" come nome utente. La password è "admin" per impostazione predefinita. Potete trovare le schermate per la manipolazione delle entità sotto il menu **Application**. 
Poi aggiungiamo alcuni dati al database: un paio di relatori e due sessioni previste per il resto della settimana. Potete provare a inserire un'email non valida per un relatore e verificare che il validatore di email funzioni come previsto. 

![](/images/learn/v14/qs-screen26.png)

![](/images/learn/v14/qs-screen27.png)

Le schermate generate sono utili per le operazioni di base, ma nella pratica l'interfaccia utente è più complessa. 

<div id="chapter8" class="section"></div>

## Personalizzazione dell'interfaccia utente

<div class="timestamp-wrapper">
   <a id="stamp8" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">10:05</div>
  </div></a>
</div>

Aggiungiamo una visualizzazione basata sul calendario per consultare le sessioni, in aggiunta alla visualizzazione a griglia. Per la schermata del browser, aggiungeremo un controllo a schede, inseriremo un controllo calendario e forniremo una funzionalità per modificare e riprogrammare le sessioni utilizzando questo calendario come nell'immagine qui sotto:

![](/images/learn/v14/qs-screen28.png)

Aprire il file ```session-browse.xml``` nel designer e racchiudere la tabella delle sessioni in una scheda nella finestra <b>Component Hierarchy</b>.

![](/images/learn/v14/qs-screen29.png)

Aggiungere un'altra pagina ```tab``` sotto il ```TabSheet```.

![](/images/learn/v14/qs-screen30.png)

Inserire un controllo calendario.

![](/images/learn/v14/qs-screen31.png)

Selezionare l'elemento ```TabSheet``` nella finestra <b>Component Hierarchy</b> e selezionare ```expanded``` nella finestra <b>Component Inspector</b>. Studio richiederà un ID. In CUBA, abbiamo bisogno di un ID per fare riferimento a un elemento della schermata nel codice.

![](/images/learn/v14/qs-screen32.png)

Assegnare gli ID ```calendarTab``` e ```tableTab``` per le schede. Quindi impostare le didascalie ```Calendario sessioni``` e ``` Tabella sessioni``` per queste schede. 

![](/images/learn/v14/qs-screen33.png)

Aggiornare il calendario - assegnare un contenitore di dati ed espanderlo.

![](/images/learn/v14/qs-screen34.png)

Infine, estendere la tabella delle Sessioni.

![](/images/learn/v14/qs-screen35.png)

In CUBA, i componenti dell'interfaccia utente possono essere legati alle entità e alle loro proprietà. 

Leghiamo il calendario alla collection impostata nella schermata. Utilizzate il campo di ricerca per trovare le proprietà e legare:
- ```startDateProperty``` alla data di inizio di una sessione
- ```endDateProperty``` alla data di fine della sessione
- ```captionProperty``` all'argomento di una sessione
- e infine ```descriptionProperty``` alla descrizione di una sessione

![](/images/learn/v14/qs-screen36.png)

Facciamo in modo che il calendario mostri solo le ore lavorative.

![](/images/learn/v14/qs-screen37.png)

Oltre al visual designer, è possibile utilizzare l'editor di markup XML. Aggiungiamo i pulsanti di navigazione.  

![](/images/learn/v14/qs-screen37_1.png)

Per vedere i cambiamenti nell'interfaccia utente non è necessario riavviare l'applicazione, è sufficiente riaprire la schermata. Il framework CUBA supporta il deploy in tempo reale per le schermate. Ora è possibile verificare che le sessioni siano visualizzate sul calendario. 

<div id="chapter9" class="section"></div>

## Utilizzo delle Screen API

<div class="timestamp-wrapper">
   <a id="stamp9" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">12:09</div>
  </div></a>
</div>

Quando interagiamo con l'interfaccia utente, vengono generati degli eventi, quindi CUBA fornisce un'API che è possibile utilizzare per sottoscrivere tali eventi per gestirli. Gestiamo un click sulla voce di un calendario e invochiamo l'editor di sessione. Per manipolare le schermate, CUBA fornisce un'API che ora andremo ad utilizzare.

In ```session-browse.xml``` selezionare ```sessionsCalendar``` e andare alla scheda Handlers nella finestra <b>Component Inspector</b>. Selezionare ```CalendarEventClickEvent``` e cliccare sulla freccia per passare al controllore.

![](/images/learn/v14/qs-screen38.png)

Verrà generato un metodo vuoto in automatico.

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventClick(Calendar.CalendarEventClickEvent<LocalDateTime> event) {
  
}
```

Dobbiamo richiamare la schermata dell'editor per modificare le proprietà della sessione. Usiamo l' ```EditorScreenFacet```. Si tratta di un componente che fornisce la possibilità di pre-configurare una schermata dell'editor. 

Andate su ```session-browse.xml``` e cercate <b>EditorScreen</b> nella <b>Component Palette</b> e spostatelo sotto l'elemento window nella <b>Component Hierarchy</b>.

![](/images/learn/v14/qs-screen39.png)

Impostare le seguenti proprietà:

- ID - ```sessionEditDialog```;
- data container - ```sessionsDc```;
- edit mode - ```EDIT```;
- entity class - ```Session```;
- open mode - ```DIALOG```;
- screen class - ```SessionEdit```.

![](/images/learn/v14/qs-screen40.png)

Tornate al gestore dell'evento. Cliccate su **Inject** nella parte superiore della finestra e selezionate il servizio ```sessionEditDialog``` sotto la sezione Screen API nella finestra popup. 

Un altro modo per l'iniezione (e la sottoscrizione) - premere la combinazione di tasti **Alt+Insert** nell'editor e selezionare **Inject** nel menu a comparsa:

![](/images/learn/v14/qs-screen40_1.png)

Per cercare il servizio, basta iniziare a digitare il suo nome e poi usare i tasti **Su** e **Giù** per navigare tra i servizi disponibili per l'iniezione.

![](/images/learn/v14/qs-screen40_2.png)

Passare l'entità della sessione ricevuta all'interno dell'oggetto dell'evento per la modifica. Alla fine dovremmo mostrare l'editor.

Il codice del gestore evento dovrebbe essere questo:

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventClick(Calendar.CalendarEventClickEvent<LocalDateTime> event) {
   sessionEditDialog.setEntityProvider(() -> (Session) event.getEntity());
   sessionEditDialog.show();
}
```

E questo è tutto. È possibile riaprire la schermata del browser delle sessioni nell'applicazione, e richiamare l'editor cliccando sulla sessione nel calendario. 

![](/images/learn/v14/qs-screen41.png)

L'editor non si presenta bene, quindi dobbiamo regolarne la larghezza e l'altezza. Nell'IDE, aprire il descrittore XML dello schermo, selezionare la proprietà ```dialogMode``` e impostare le proprietà ```width``` e ```height``` su ```auto```.

![](/images/learn/v14/qs-screen42.png)

Passare all'applicazione e riaprire l'editor chiudendolo e cliccando di nuovo sulla sessione nel calendario. Ora si presenta meglio.

![](/images/learn/v14/qs-screen43.png)

<div id="chapter10" class="section"></div>

## Aggiunta della logica di business

<div class="timestamp-wrapper">
   <a id="stamp10" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">13:47</div>
  </div></a>
</div>

Ora useremo CUBA Studio per creare un servizio che implementi la logica di business, e sfrutteremo questo servizio in una schermata. Sarà un servizio per la riprogrammazione delle sessioni che garantirà che un relatore non abbia più di due sessioni in un giorno.

Cliccare con il tasto destro del mouse sul nodo service nell'albero del progetto CUBA e selezionare **New ->Service**. Si aprirà una finestra di dialogo per la creazione di un servizio. Inserire ```SessionService``` come nome dell'interfaccia, e ```SessionServiceBean``` sarà il nome della classe di implementazione. 

![](/images/learn/v14/qs-screen44.png)

Creare un metodo ```rescheduleSession``` nell'interfaccia come riportato nel frammento di codice qui sotto: 

```java
public interface SessionService {
   String NAME = "planner_SessionService";

   Session rescheduleSession(Session session, LocalDateTime newStartDate);
}
```

Il metodo accetta una sessione e una nuova data e ora della sessione. Il servizio restituirà l'istanza di sessione aggiornata.

Per implementare il metodo, aprire l'editor di codice per la classe ```SessionServiceBean```, lo si può trovare sotto il nodo **Middleware - Services** nell'albero del progetto CUBA:

![](/images/learn/v14/qs-screen45.png)

Vedrete che la classe è vuota e non valida:

![](/images/learn/v14/qs-screen461.png)

Premere **Alt+Insert** nel corpo della classe e selezionare **Implement Methods** nel menu a comparsa:

![](/images/learn/v14/qs-screen46.png)

Selezionare il metodo ```rescheduleSession```, e verrà generato lo stub di codice:

```iava
@Service(SessionService.NAME)
public class SessionServiceBean implements SessionService {

   @Override
public Session rescheduleSession(Session session, LocalDateTime newStartDate) {
       return null;
   }
}
```
Per prima cosa, calcolare l'ora di inizio e di fine nella giornata in cui è prevista la sessione.
```java
@Override
public Session rescheduleSession(Session session, LocalDateTime newStartDate) {
   LocalDateTime dayStart = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(8);
   LocalDateTime dayEnd = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(19);
return null;   
}
```

Per il servizio, useremo le API CUBA per l'accesso ai dati - la classe ```DataManager```. Con questa classe creiamo una query JPQL per verificare se ci sono sessioni programmate per il relatore in un intervallo di tempo definito e vi aggiungiamo i valori dei parametri. Poi controlliamo il risultato della query e, a seconda del risultato, aggiorniamo la sessione con una nuova data di inizio o semplicemente restituiamo l'istanza originale della sessione.

Iniettare il DataManager nel servizio premendo **Alt+Insert** nel corpo della classe e selezionare **Inject from** nel menu a comparsa:

![](/images/learn/v14/qs-screen481.png)

Selezionare ```DataManager``` nella finestra popup:

![](/images/learn/v14/qs-screen48.png)

L'implementazione del metodo si trova nel frammento di codice qui sotto:
```java
@Override
public Session rescheduleSession(Session session, LocalDateTime newStartDate) {
   LocalDateTime dayStart = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(8);
   LocalDateTime dayEnd = newStartDate.truncatedTo(ChronoUnit.DAYS).withHour(19);
   Long sessionsSameTime = dataManager.loadValue("select count(s) from planner_Session s where " +
           "(s.startDate between :dayStart and :dayEnd) " +
           "and s.speaker = :speaker " +
           "and s.id <> :sessionId", Long.class)
           .parameter("dayStart", dayStart)
           .parameter("dayEnd", dayEnd)
           .parameter("speaker", session.getSpeaker())
           .parameter("sessionId", session.getId())
           .one();
   if (sessionsSameTime >= 2) {
       return session;
   }
   session.setStartDate(newStartDate);
   return dataManager.commit(session);
}
```

Il servizio è pronto, ora aggiungiamolo alla schermata del browser della sessione. Verrà invocato per l'evento drag-and-drop nel calendario. 

In ```session-browse.xml``` selezionare ```sessionsCalendar``` e andare alla scheda **Handlers** nella finestra **Component Inspector**. Selezionare ```CalendarEventMoveEvent``` e cliccare sulla freccia per passare al controllore.

Nel metodo che è sottoscritto all'evento drag-and-drop, aggiungiamo un po' di codice per estrarre l'entità della sessione dall'evento del calendario, e lo passiamo al servizio per riprogrammare questa sessione. Dopo di che, aggiorniamo questa voce nel contenitore dei dati del browser.

```java
@Subscribe("sessionsCalendar")
public void onSessionsCalendarCalendarEventMove(Calendar.CalendarEventMoveEvent<LocalDateTime> event) {
   Session session = sessionService.rescheduleSession((Session) event.getEntity(), event.getNewStart());
   sessionsDc.replaceItem(session);
}
```

Per poter utilizzare il nuovo servizio, dobbiamo riavviare l'applicazione: possiamo usare sempre il pulsante **Run** in IDEA . 

![](/images/learn/v14/qs-screen49.png)

Dopo il riavvio possiamo aprire il calendario delle sessioni - e voilà! Abbiamo la funzione di drag-and-drop per la riprogrammazione nel calendario! Testiamola aggiungendo una sessione extra e provando a riprogrammarla.


<div id="chapter11" class="section"></div>

## Branding

<div class="timestamp-wrapper">
   <a id="stamp11" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">16:27</div>
  </div></a>
</div>

In CUBA, è possibile aggiornare i file delle risorse e sovrascrivere le etichette standard. Aggiorniamo il testo in base al dominio di business dell'applicazione - pianificazione delle conferenze.

Apriamo il file principale del pacchetto di messaggi - ```messages.properties``` che si trova sotto il nodo **Generic UI - Main Message Pack** nell'albero del progetto CUBA. 

![](/images/learn/v14/qs-screen50.png)

Si tratta di un file standard ```java .properties```, quindi è possibile modificarlo liberamente, aggiungendo nuove chiavi di messaggio e aggiornando quelle esistenti. Aggiornare i messaggi per riflettere lo scopo dell'applicazione. L'esempio è riportato di seguito:

```java
application.caption = Session Planner
application.logoLabel = Session Planner
application.logoImage = branding/app-icon-menu.png
application.welcomeText = Welcome to Session Planner!

loginWindow.caption = Session Planner Login
loginWindow.welcomeLabel = Welcome to Session Planner!
loginWindow.logoImage = branding/app-icon-login.png

menu-config.application-planner = Sessions and Speakers
menu-config.planner_Speaker.browse=Speakers
menu-config.planner_Session.browse=Sessions
```

Con la funzione di aggiornamento in tempo reale di CUBA, tutto quello che dobbiamo fare è accedere nuovamente all'applicazione per vedere le modifiche.

<div id="chapter12" class="section"></div>

## Marketplace

<div class="timestamp-wrapper">
   <a id="stamp12" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">16:58</div>
  </div></a>
</div>

Il framework dispone di un <a target="_self" href="/marketplace/">marketplace</a> che contiene molti componenti, in modo da poter aggiungere facilmente funzionalità utili come <a target="_self" href="/marketplace/charts-and-maps/">grafici</a> o <a target="_self" href="/marketplace/maps/">mappe</a> ad una applicazione esistente. È possibile installare questi componenti direttamente da Studio utilizzando il menu <b>CUBA -> Marketplace</b>.

![](/images/learn/v14/qs-screen51.png)

Aggiungiamo l'add-on [Helium](https://www.cuba-platform.com/marketplace/helium/). È un nuovo tema visivo, che si può usare al posto dei temi standard. Basta fare clic su installa e applicare le modifiche. 

Ora dobbiamo fermare l'applicazione e aggiornare il database per applicare gli init script del componente aggiuntivo. 

![](/images/learn/v14/qs-screen52.png)

Eseguire l'applicazione e passare alla schermata delle impostazioni. È possibile trovare il tema aggiunto nell'elenco a discesa. Selezionarlo e applicare.

![](/images/learn/v14/qs-screen53.png)

Accedere di nuovo all'applicazione - il tema è già applicato.

![](/images/learn/v14/qs-screen54.png)

Possiamo anche aprire la schermata delle impostazioni del tema e modificare le impostazioni con l'anteprima.

<div id="chapter13" class="section"></div>

## Conclusioni

<div class="timestamp-wrapper">
   <a id="stamp13" class="timestamp-wrapper-buttons"><div class="timestamp">
    <img src="/images/learn/play.svg" class="delta">
    <div class="video-time">17:58</div>
  </div></a>
</div>

Il framework CUBA fornisce molte API utili per aiutarvi a creare rapidamente applicazioni aziendali. Questo quickstart mostra solo le basi del framework CUBA e di CUBA Studio. Potete trovare altri <a target="_self" href="/learn">esempi</a> e <a target="_self" href="/documentation">tutorials</a> sul nostro sito web: <a target="_self" href="/">cuba-platform.com</a>. 

Grazie per il vostro interesse nella piattaforma CUBA. Buon sviluppo con CUBA! 
