# Generazione del Codice in CUBA: Uno sguardo dietro la magia

Riassunto: 

La generazione di codice è una prassi comune nei framework moderni. Come ogni tecnologia, la generazione di codice ha diverse aree di applicazione e limitazioni. In questo articolo, daremo uno sguardo all'uso della generazione di codice in CUBA e discuteremo lo sviluppo futuro di questa tecnica.

## Introduzione

La generazione di codice è una prassi comune nei framework moderni. Ci possono essere diverse ragioni dietro l'introduzione della generazione di codice - eliminare codice boilerplate, evitare l'uso di reflection fino alla creazione di soluzioni complesse basate su linguaggi di dominio.

Come ogni tecnologia, la generazione del codice ha diverse aree di applicazione e limitazioni. In questo articolo, daremo uno sguardo all'uso della generazione di codice in CUBA e discuteremo lo sviluppo futuro di questa tecnica.

## Cosa viene generato in CUBA?

Il framework CUBA è costruito sul noto framework Spring. Fondamentalmente, ogni applicazione CUBA può essere trattata come un'applicazione Spring con l'aggiunta di alcune API volte a semplificare lo sviluppo delle comuni funzionalità di business.

CUBA fornisce una libreria basata su Vaadin per lo sviluppo dell'interfaccia utente in modo rapido. E questa libreria utilizza l'approccio dichiarativo del binding dei dati. Pertanto, è possibile visualizzare diversi valori di proprietà di un oggetto nello stesso widget di UI, solo cambiando il binding in runtime. 

Ciò significa che ogni oggetto dovrebbe essere in grado di fornire un valore di proprietà avendo il suo nome come stringa. Avere il framework Spring come base significa che la reflection può essere usata facilmente per recuperare i valori delle proprietà. 

### Potenziamento delle entità

La reflection è uno strumento potente, ma è ancora lenta nonostante tutti gli sforzi di ottimizzazione del team JVM. E quando si parla di UI, soprattutto per quanto riguarda la visualizzazione di grandi griglie di dati, si giunge alla conclusione che i metodi di reflection saranno invocati abbastanza frequentemente. Ad esempio, la visualizzazione di 20 linee con 10 proprietà porta facilmente a 200 invocazioni. Moltiplichiamolo per il numero di utenti e teniamo conto che tutte queste invocazioni avverranno sul server (è il modo in cui Vaadin funziona), e potremmo ottenere un carico di lavoro abbastanza elevato per il server.

Quindi, per ogni classe di oggetti dati (entità) dobbiamo definire un semplice metodo che invocherà una proprietà getter (o setter) basata sul nome della proprietà. Basterà un semplice comando switch. 

Inoltre, l'interfaccia utente deve "sapere" se un oggetto è stato modificato, in modo che un utente possa confermare i cambiamenti di dati quando lo schermo viene chiuso. Quindi, dobbiamo iniettare un listener di stato nel setter di ogni oggetto per verificare se il valore della proprietà è cambiato.

E in aggiunta al metodo che imposta o ottiene i valori delle proprietà, dobbiamo aggiornare ogni setter e invocare un listener di cambio di stato per marcare l'oggetto entità come modificato. 

Anche questo metodo non è complesso, fondamentalmente una riga. Ma sarebbe ingiusto chiedere a uno sviluppatore di fare tutto questo lavoro noioso - aggiungendo e aggiornando un gruppo di metodi molto semplici per ogni proprietà dell'entità. E questo è esattamente il caso in cui la generazione del codice ci viene in soccorso. 

CUBA usa l'ORM EclipseLink dietro le quinte. E questo framework assolve alcuni dei compiti elencati in precedenza. Come indicato nella [documentazione](https://www.eclipse.org/eclipselink/documentation/2.7/concepts/app_dev005.htm): "Il provider di persistenza JPA di EclipseLink usa il weaving per potenziare sia le entità JPA che le classi Plain Old Java Object (POJO) con operazioni come il lazy loading, il tracciamento delle modifiche, i gruppi di fetch e le ottimizzazioni interne".

In CUBA, il weaving statico di EclipseLink è invocato a build-time (altrimenti verrebbe eseguito a run-time di default) dal plugin di build CUBA.

Oltre a questo, invocare i change listener rimane un compito che dovrebbe essere assolto da CUBA. E infatti viene eseguito dal plugin di build in build-time. Quindi, se si apre il file `.class` di un'entità, si possono vedere una serie di metodi che non si vedono nel codice sorgente. E si può essere sorpresi da come sono cambiati i setter. Per esempio, invece di:

```
public void setName(String name) {
   this.name = name;
}
```
nel codice decompilato vedreste:

```
public void setName(String name) {
   String __prev = this.getName();
   this._persistence_set_name(name);
   Object var5 = null;
   String __new = this.getName();
   if (!InstanceUtils.propertyValueEquals(__prev, __new)) {
       this.propertyChanged("name", __prev, __new);
   }
}
```


Questo è il mix di codice generato dal weaver EclipseLink e il plugin di build di CUBA. Quindi, in CUBA le classi di entità compilate sono diverse da ciò che si scrive effettivamente nell'IDE. 

### Messaggi di validazione dei Beans
La piattaforma CUBA supporta l'internazionalizzazione dei messaggi di convalida dei beans. Ciò significa che nelle annotazioni di validazione JPA si può fare riferimento alla voce del file `.properties` invece di scrivere la stringa del messaggio direttamente nel valore dell'annotazione.

Il codice sorgente si presenta così:

```
@NotNull(message = "{msg://hr_Person.name.validation.NotNull}")
@Column(name = "NAME", nullable = false, unique = true)
private String name;
```

I file delle risorse di traduzione per le entità dovrebbero essere nello stesso package delle entità. Quindi, se vogliamo semplificare il caricamento dei valori delle proprietà, dobbiamo specificare il nome del pacchetto in questa riga. L'azione è semplice, l'algoritmo di aggiornamento è chiaro, quindi si è deciso di usare la generazione del codice.

Il plugin CUBA Platform trasforma il riferimento al messaggio di cui sopra nel formato:

```
@NotNull(message = "{msg://com.company.hr/hr_Person.name.validation.NotNull}")
@Column(name = "NAME", nullable = false, unique = true)
private String name;
```

E ora che abbiamo il nome del pacchetto, recuperare il messaggio dal file delle risorse usando il metodo `getResourceAsStream()` è molto più semplice.

## Cosa aspettarsi in futuro?

Non c'è molto codice generato al momento, ma il framework si sta evolvendo. Il team di sviluppo sta pensando di usare la generazione del codice per altri casi.

### Metodi comuni nelle Entità

Al momento, in CUBA la struttura delle entità è flessibile, ma si basa su interfacce, quindi è necessario implementare i metodi definiti in tali interfacce. Per esempio, se si vuole che la propria entità supporti la cancellazione logica, è necessario implementare la seguente interfaccia:

```
public interface SoftDelete {
   Date getDeleteTs();
   String getDeletedBy();
   //More methods here
}

```

Naturalmente, ci sono implementazioni predefinite di queste interfacce come `com.haulmont.cuba.core.entity.StandardEntity`, quindi è possibile estendere questa entità per utilizzarne l'implementazione. 

Ma sarebbe molto più semplice usare nomi di metodi che non sono hardcoded e marcare semplicemente le proprietà che si vogliono usare per memorizzare la data di cancellazione e un nome utente di chi ha eseguito la cancellazione. In questo caso, potremmo generare i metodi mostrati sopra e mappare le invocazioni ai getter e ai setter appropriati. Diamo un'occhiata ad un esempio di entità:

```
@Entity
public class Account {

   //Other fields
   @DeletedDate
   private Date disposedAt;

   @DeletedBy
   private String disposedBy;

   public Date getDisposedAt() {
       return disposedAt;
   }

   public String getDisposedBy() {
       return disposedBy;
   }

}
```

In questa entità si possono vedere campi speciali definiti per memorizzare i dati relativi al processo di cancellazione. Quindi, cosa vedremo se applichiamo qualche potenziamento a questa entità?

```
@Entity
public class Account implements SoftDelete {

   //Other fields
   @DeletedDate
   private Date disposedAt;

   @DeletedBy
   private String disposedBy;

   public Date getDisposedAt() {
       return disposedAt;
   }

   public String getDisposedBy() {
       return disposedBy;
   }

   //Generated
   @Override
   public Date getDeleteTs() {
       return getDisposedAt();
   }

   //Generated
   @Override
   public String getDeletedBy() {
       return getDisposedBy();
   }
}
``` 

Ora possiamo verificare se l'istanza supporta la cancellazione logica applicando l'operatore `instanceof`, implementando così un approccio generico per le operazioni di cancellazione logica all'interno del framework, basandoci solo sulle interfacce e sui metodi del framework invece di rilevare le annotazioni a runtime. 

Questo approccio aggiungerà maggiore flessibilità alla definizione delle entità, specialmente nel reverse engineering dei database. 

Così, nelle future versioni CUBA, abbiamo in programma di aggiungere più bit e pezzi di generazione di codice qua e là per rendere più facile la vita dello sviluppatore. 

## Generazione build-time vs generazione runtime

Come si può notare, in CUBA, la generazione del codice avviene in fase di compilazione. Ci sono pro e contro di questo approccio, descriviamoli.

La generazione in fase di compilazione consente di individuare i problemi nelle fasi iniziali. Quando si genera codice, ci sono molte "variabili" di cui si dovrebbe tener conto. Per esempio, se le API di EclipseLink cambiano, allora le invocazioni generate da CUBA durante il processo di potenziamento diventeranno non valide. Anche le modifiche alle API JDK possono causare problemi. Generando codice durante il build-time ci si affida al compilatore Java per trovare tali problemi nelle fasi iniziali. E gli errori del compilatore sono di solito più facili da identificare rispetto a quelli di runtime, il codice sorgente è una cosa statica. Anche se viene generato.

Ma la generazione durante la compilazione richiede un tool separato che non fa parte del codice di base del progetto: il plugin di compilazione. Introdurre un altro strumento significa introdurre un altro punto di errore. Uno sviluppatore ora dipende da un compilatore e da uno strumento di generazione del codice. E se uno di essi contiene un bug allora è un bel problema, perché lo sviluppatore non può aggiornarli da solo.

In fase di esecuzione non esiste uno strumento separato, quindi un generatore di codice fa parte del framework. Ma la generazione avviene a runtime e gli sviluppatori dipendono dallo stato del programma e dallo stato della VM. A volte la generazione dinamica del codice può fallire improvvisamente a causa del consumo di memoria o di altri problemi, perché è abbastanza difficile controllare completamente lo stato della VM. 

Quindi, per CUBA abbiamo scelto la generazione del codice in fase di compilazione. La quantità di codice generato non è così grande, l'insieme delle classi è limitato alle sole entità, quindi per questo caso particolare il generatore di codice è stato abbastanza semplice e non ci sono stati problemi bloccanti fino ad ora.

## Strumenti di generazione

In Java, un approccio standardizzato alla generazione di codice è apparso a partire da Java 5, ed è stata l'elaborazione delle annotazioni. L'idea è semplice: si crea un processore in grado di generare nuovo codice sulla base delle annotazioni nel codice esistente. E si può generare codice con annotazioni che possono innescare un altro ciclo di elaborazione. 

Il processore di annotazione standard ha una limitazione - non può aggiornare il codice esistente, ma solo generarne di nuovo. Così, per CUBA, è stata usata una libreria Javassist. 

Questa libreria permette l'aggiornamento del codice esistente, e può usare semplici stringhe. Per esempio, questo codice memorizza il valore precedente della proprietà prima di un'invocazione del setter:

```
ctMethod.insertBefore(
       "__prev = this." + getterName + "();"
);
```

Javassist contiene un proprio compilatore Java limitato per verificare la correttezza del codice. L'uso di stringhe per la generazione del codice non fornisce sicurezza di tipo, quindi alcuni bug possono essere introdotti a causa di un errore di tipo. Ma è molto più semplice da usare rispetto a una libreria che usa un modello tipizzato per la generazione del codice come ByteBuddy. Si può letteralmente vedere il codice che verrà aggiunto alle classi.  

## Conclusione

La generazione di codice è uno strumento molto potente che aiuta gli sviluppatori a:

1. Evitare di fare lavori noiosi come scrivere semplice codice ripetitivo
2. Automatizzare l'aggiornamento di alcuni metodi a fronte di modifiche al codice

D'altra parte, il vostro programma non è quello che avete scritto. La generazione di codice può cambiare completamente i vostri sorgenti, quindi dovrete eseguire il debug non solo del vostro codice, ma anche di quello generato. 

Oltre a questo, si diventa dipendenti dai generatori di codice dei framework e, in caso di bug, si deve attendere l'aggiornamento del plugin. 

In CUBA, le aree di generazione del codice sono limitate alle entità, e abbiamo intenzione di estendere questa area lentamente per semplificare il lavoro degli sviluppatori e aggiungere più flessibilità al framework.

Quindi, se avete intenzione di creare il vostro framework o di introdurre un generatore di codice per uno esistente, considerate che questa tecnica è molto potente, ma fragile. Provate a generare codice semplice e a documentare tutti i passaggi e le condizioni di generazione, perché qualsiasi cambiamento in qualsiasi API può rompere facilmente la generazione.