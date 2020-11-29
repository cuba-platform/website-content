## Introduzione

Multitenancy è un riferimento alla modalità di funzionamento del software in cui più istanze indipendenti di una o più applicazioni operano in un ambiente condiviso. Le istanze (tenants) sono logicamente isolate, ma fisicamente integrate. 

Potete trovare una [bella spiegazione](https://www.netsolutions.com/insights/5-reasons-why-you-should-choose-multi-tenant-architecture-for-your-saas-application/) su Internet dove l'approccio multi-tenant è paragonato al leasing di edifici per uffici:

*Gli appartamenti sono un perfetto esempio di architettura multi-tenant. Hanno un'amministrazione centralizzata per la sicurezza (al cancello principale), l'elettricità, l'acqua e altre strutture. Queste strutture sono di proprietà del proprietario dell'appartamento e sono condivise dagli inquilini.*

In questo articolo, ragioneremo sull'architettura multi-tenancy e analizzeremo una delle implementazioni di questo approccio nella piattaforma CUBA.

## Una breve storia della multi-tenancy

Partiamo da un argomento completamente diverso: la distribuzione e la messa in produzione del software. Inizialmente il software veniva distribuito utilizzando supporti fisici - nastri, dischi, unità flash. E di solito si doveva distribuire il software sul server che era installato nella rete locale. I canali di comunicazione di allora non erano così buoni come lo sono ora.

Ma gradualmente la larghezza di banda di Internet è cresciuta, e ora stiamo parlando di canali a megabit e gigabit. Così, è diventato molto più facile consegnare software via internet - scaricare un centinaio di megabyte richiede meno tempo che andare in un negozio per acquistare un DVD o un disco Blu-ray con il software. 

Nel frattempo, i canali di comunicazione e lo sviluppo dell'hardware informatico hanno fatto emergere un nuovo mercato: la virtualizzazione e, più tardi, l'IaaS (infrastruttura come servizio). È diventato più facile impostare un server con un prodotto software preinstallato e consentire l'accesso agli utenti. 
Di conseguenza, i fornitori hanno adottato il modello di distribuzione SaaS - "software-as-a-service". Il cliente doveva solo acquistare l'accesso a un prodotto software distribuito. Non era necessario mantenere i server locali, quindi il TCO era inferiore.

L'installazione di un'istanza software per ogni client non era ancora un'operazione facile, così gli sviluppatori di software hanno iniziato a utilizzare l'architettura multi-tenant per semplificare la distribuzione e la manutenzione. Si possono ancora vedere molte implementazioni SaaS su Internet. Ad esempio, Salesforce ha iniziato a utilizzare un approccio multi-tenant e la modalità SaaS molto tempo fa.
Ma implementare applicazioni multi-tenant è un compito impegnativo. Bisogna pensare a separare i dati dei clienti, il consumo di risorse comuni, la condivisione di dati comuni e altre cose. L'ultima risposta a queste sfide è il modello PaaS (platform-as-a-service). 

La containerizzazione e lo sviluppo dei processi DevOps sono le minacce principali per le applicazioni multi-tenant. Oggi è molto facile avviare un nuovo "server" o addirittura un'intera "infrastruttura", compresi database, servizi applicativi, server di caching e reverse proxy, grazie a prodotti come Docker e Kubernetes. Per gli sviluppatori, è molto più semplice implementare un'applicazione in single-tenant e poi implementarne una nuova istanza per ogni nuovo cliente, letteralmente, in pochi secondi e avviene anche in modo completamente automatizzato. A volte potrebbe portare ad un altro fenomeno chiamato "[cube sprawl](https://www.infoq.com/presentations/multi-tenancy-kubernetes/)", ma niente è perfetto a questo mondo.  

Ciononostante, le applicazioni multi-tenant sono ancora oggi molto diffuse (vedi Salesforce, Work Day, Sumo Logic, ecc.), e ancora, c'è una richiesta per lo sviluppo di tali prodotti. Esaminiamo alcuni approcci all'implementazione del multi-tenancy e poi vediamo cosa può offrire la Piattaforma CUBA in questo settore.   

## Implementazioni multi-tenancy

Ci sono tre tipi principali di modelli multi-tenancy applicati alla costruzione di un datastore multi-tenant:
  
<br>
<img src="http://192.168.45.58:1337/uploads/be0139222d3f4ee39baca047cedf227c.png" style="float: right; height: 180px; margin-left:35px;margin-bottom:20px;">
1. Singolo database condiviso. I dati dei tenants sono memorizzati nello stesso schema e condividono le stesse tabelle. Per separare i dati di diversi tenants, uno sviluppatore introduce una colonna discriminante che memorizza l'identificativo del tenant. Ciò significa che si isolano i dati separati a livello di query. Un'applicazione aggiunge implicitamente un predicato per ogni query, quindi non c'è modo di selezionare i dati di altri tenant. 
Questo è un modo semplice ed economico per implementare un'architettura multi-tenancy. È anche facile da mantenere - è necessario aggiornare solo un'istanza dell'applicazione. Tuttavia, potrebbero esserci alcune preoccupazioni riguardo alla sicurezza dei dati. Se avete un errore nel vostro SQL, potete accidentalmente esporre i dati di altri tenant. 

<br style="clear:both;">
<br> 
<img src="http://192.168.45.58:1337/uploads/b2096b52ad9a4808af5ba9d938880916.png" style="float: right; height: 180px;margin-left:35px;margin-bottom:20px;">
2. Il secondo approccio è quello di utilizzare schemi di database multipli per diversi tenant, ma l'istanza dell'applicazione rimane ancora condivisa. Si tratta di un'architettura di valore quando i dati di diversi tenant devono essere trattati in modo diverso - come se dovessero passare attraverso diverse regolamentazioni geografiche. Ma allo stesso tempo si è in grado di condividere alcuni dati comuni tra i tenant per evitare duplicazioni. Per implementare questa architettura, dovrete usare qualche meccanismo di routing per reindirizzare le query a schemi diversi. Ad esempio, nel framework Spring si può trovare una classe RoutingDataSource implementata proprio per questo scopo.

<br style="clear:both;">
<br>
<img src="http://192.168.45.58:1337/uploads/aa252517ca5a4524b70d21b24f73c53d.png" style="float: right; width: 150px;margin-left:35px; margin-bottom:20px;">
3. Il terzo tipo di architettura multi-tenant ospita i dati in più database. Utilizza anche un meccanismo di routing, ma reindirizza le query tra diversi database, non tra gli schemi. Questo modello è relativamente complesso in termini di gestione e manutenzione. Quando l'applicazione viene aggiornata, è necessario aggiornare tutti i database. Anche l'aggiornamento della versione del database sarà un compito oneroso, soprattutto se si hanno molti tenant. 
Nonostante le complessità di questo approccio, i tenant sono separati più nettamente e il rischio di accedere ai dati di qualcun altro e di consumare le risorse di un'altra applicazione è ridotto al minimo. Inoltre, questo approccio offre buone possibilità di scalabilità, infatti è possibile scalare l'archiviazione dei dati individualmente in base alle esigenze di ogni tenant.
<br style="clear:both;">
<br>
 
**Vantaggi e svantaggi del multi-tenancy**

Ci sono una serie di pro e contro per l'architettura multi-tenant:

Pro:

1. Costi. Di solito è meno costosa rispetto ad altre soluzioni.
2. Manutenzione semplificata - gli amministratori dovrebbero monitorare, correggere, aggiornare e mettere a punto un solo sistema.
3. Facilmente scalabile - l'aggiunta di un nuovo tenant è un processo standardizzato, rapido e semplice.

Contro:

1. Creare un'applicazione multi-tenant è un compito più complesso rispetto a quello di un singolo tenant.
2. La flessibilità potrebbe essere un problema, soprattutto per le applicazioni condivise da clienti di diverse aree geografiche. Possono esserci diversi requisiti per la protezione dei dati personali, il calcolo della logica di business, ecc.
3. Problema del "vicino ficcanaso". Un altro tenant potrebbe accedere ai vostri dati o consumare le vostre risorse.
4. E il problema del "single point of failure". Se tutti i clienti condividono la stessa risorsa (ad es. banca dati), tutti sono interessati se questa risorsa diventa indisponibile. 

## Multi-tenancy in CUBA

Il framework CUBA ci permette di cambiare al volo le query generate nel database, è così che viene implementata la sicurezza basata sulle righe. Ed è stato abbastanza facile estendere questo meccanismo per il supporto di applicazioni multi-tenancy e implementare un add-on.

Quando si implementa un'applicazione multi-tenant utilizzando CUBA, è necessario decidere quali entità memorizzeranno i dati specifici del tenant. Prendiamo ad esempio l'applicazione di esempio ["PetClinic"](https://demo10.cuba-platform.com/petclinic/#login). Il modello di dati per l'applicazione è rappresentato nel diagramma sottostante.

![text](http://192.168.45.58:1337/uploads/85af16bf471844e4adf96adc8693a4a2.jpg)

Si possono rendere tutte le entità specifiche per ogni tenant ma c'è un'altra opzione - rendere condivise alcune entità di riferimento. Possiamo dire che le specialità veterinarie e i tipi di animali domestici sono "sicuri" in termini di condivisione tra i diversi clienti. Possiamo dire lo stesso di alcuni altri dati "comuni", ad esempio l'elenco dei paesi del mondo, ecc. Questo ci permetterà di risparmiare un po' di spazio e di garantire che i dati "standard" rimangano gli stessi per tutti i tenant, in modo da poter fare affidamento su di essi nell'implementazione della logica di business. 

Se volete che la vostra entità supporti la multi-tenancy in CUBA, dovete implementare un'interfaccia speciale - `com.haulmont.cuba.core.entity.TenantEntity`. Dopodiché, l'add-on terrà conto della colonna speciale per la separazione dei tenant. Per esempio, ecco il frammento di codice dell'entità Pet:

```
public class Pet extends NamedEntity implements TenantEntity {
   
   @TenantId
   @Column(name = "TENANT_ID")

   protected String tenantId;
   @NotNull
   @Column(name = "IDENTIFICATION_NUMBER", nullable = false)
   protected String identificationNumber;

   @OneToOne(fetch = FetchType.LAZY)
   @JoinColumn(name = "TYPE_ID")
   protected PetType petType;
```

Si prega di notare che tutte le entità "di sistema" di CUBA supportano il multi-tenancy di default. Così otterrete tutti gli utenti, i ruoli, i filtri salvati, i gruppi di sicurezza, ecc. separati per ogni cliente. 

Dopo la creazione delle entità, CUBA aggiungerà una condizione extra " where tenant_id = <tenant>" per tutte le query JPQL (incluse le join) che vengono generate per le entità multi-tenant. Per gli sviluppatori di applicazioni, questo approccio è per lo più trasparente, a meno che non vogliano usare SQL nativo. In questo caso, devono aggiungere esplicitamente la condizione aggiuntiva alle loro query per evitare fughe di dati. 

Ad esempio, se vogliamo selezionare animali domestici e proprietari dal database Petclinic, la query finale sarà simile a questa:

```
SELECT *
FROM PETCLINIC_PET t1
        LEFT OUTER JOIN PETCLINIC_OWNER t0
                        ON ((t0.ID = t1.OWNER_ID)
                       AND (t0.TENANT_ID = t1.TENANT_ID))
        LEFT OUTER JOIN PETCLINIC_PET_TYPE t2
                        ON (t2.ID = t1.TYPE_ID)
WHERE ((t1.TENANT_ID = 'clinic_one') AND (t1.DELETE_TS IS NULL))
```

Si prega di notare le seguenti condizioni nella richiesta: **`AND (t0.TENANT_ID = t1.TENANT_ID))`** e **`(t1.TENANT_ID = 'clinic_one')`**. Queste sono condizioni che CUBA aggiunge in modo automatico.

Ma c'è un modo per applicare in modo trasparente il multi-tenancy a livello di database oltre al livello di applicazione? Una delle risposte è [Citus](https://www.citusdata.com/).

## Citus: multi-tenancy per PostgreSQL 

Come si legge nella [descrizione](https://docs.citusdata.com/en/v9.3/get_started/what_is_citus.html): "Citus è fondamentalmente un Postgres chiavi in mano che è progettato per la scalabilità. È un'estensione di Postgres che distribuisce dati e interrogazioni in un cluster di macchine multiple. Essendo un'estensione (piuttosto che un fork), Citus supporta le nuove release di PostgreSQL, permettendo agli utenti di beneficiare di nuove funzionalità mantenendo la compatibilità con gli strumenti PostgreSQL esistenti.

Citus scala orizzontalmente PostgreSQL su più macchine utilizzando sharding e replicazione. Il suo motore di query parallelizza le query SQL in arrivo attraverso questi server per consentire risposte in tempo reale (meno di un secondo) su grandi insiemi di dati".

E sembra che Citus risolva molte sfide legate alla creazione di applicazioni multi-tenant. Ci permette di utilizzare l'approccio "database separato per tenant" e "si presenta" come un unico database per l'applicazione. Tutte le query vengono modificate secondo le regole definite durante la creazione del cluster Citus. 

Ciò significa che è possibile utilizzare l'implementazione CUBA multi-tenant e ottenere database fisicamente separati senza riscrivere le query. Diamo un'occhiata più da vicino al processo di creazione del database di Citus.

Per prima cosa, è necessario creare un cluster di database e aggiungervi dei nodi. Il cluster è composto dal nodo coordinatore e dai nodi lavoratori. Ogni nodo worker memorizza i dati specifici del tenant e il coordinatore reindirizzerà le query secondo la colonna "tenant_id". L'applicazione invia le query solo al coordinatore. Il processo si trova nell'immagine sottostante (tratta dalla documentazione di Citus DB):

![text](http://192.168.45.58:1337/uploads/381851d1da3e4d4b99e4a192a4f06731.jpg)

Inoltre, per lavorare con Citus, è necessario definire i tipi di tabella durante la creazione del database. Ce ne sono tre:

1. Distribuite. Queste sono le tabelle suddivise tra i nodi dei lavoratori. Contengono dati specifici per i tenant.
2. Tabelle di riferimento - tabelle che memorizzano informazioni che possono essere utilizzate da tutti i tenant. Prendendo l'esempio di PetClinic, quelle saranno "Specialty" e "Pet Type".
3. Tabelle locali. Tabelle di servizio che non vengono utilizzate nelle query di join con tabelle distribuite o di riferimento. Ad esempio le tabelle di metadati di Citus memorizzate sul nodo del coordinatore.

La versione Enterprise di Citus permette di separare esplicitamente i dati del tenant assegnando il seguente operatore: 
```
SELECT isolate_tenant_to_new_shard('table_name', 'tenant_id');
```
E questo sostituirà il partizionamento basato sull'hash che viene utilizzato di default. 
## Usare Citus con CUBA

Per le applicazioni CUBA non è necessario riscrivere il codice dell'applicazione, ma il processo di creazione del database e delle tabelle deve essere modificato per essere "Citus-compatibile". Tutti noi amiamo CUBA per la sua magia della generazione SQL, ma purtroppo è necessario creare manualmente alcuni script per supportare Citus. 

Il processo di creazione del database deve essere cambiato. È necessario creare l'estensione Citus, aggiungere nodi e abilitare la replica:

```
CREATE EXTENSION citus;

SELECT * from master_add_node('localhost', 9701);
SELECT * from master_add_node('localhost', 9702);

SET citus.replication_model = 'streaming';
```

La buona notizia è che si può sviluppare un'applicazione su PostgreSQL locale (per la maggior parte del tempo) e aggiungere gli script di creazione di tabelle specifici per Citus solo prima del deploy all'ambiente di staging o di produzione (se si è abbastanza coraggiosi da non usare lo staging). L'unica cosa che cambia è l'indirizzo del database - invece del database locale di dev, è necessario specificare l'indirizzo del database del coordinatore del cluster Citus. 

Per quanto riguarda la creazione e l'aggiornamento del database, è necessario specificare esplicitamente le tabelle distribuite (nome della tabella e colonna della partizione) in questo modo:

```
SELECT create_distributed_table('petclinic_vet', 'tenant_id');
SELECT create_distributed_table('petclinic_owner', 'tenant_id');
SELECT create_distributed_table('petclinic_pet', 'tenant_id');
SELECT create_distributed_table('petclinic_visit', 'tenant_id');
```

E un processo simile dovrebbe essere applicato per le tabelle di riferimento: 

```
SELECT create_reference_table('petclinic_specialty');
SELECT create_reference_table('petclinic_vet_specialty_link');
SELECT create_reference_table('petclinic_pet_type');
```

Per migliorare le prestazioni, è possibile aggiungere una tabella di [colocation](https://docs.citusdata.com/en/v9.3/sharding/data_modeling.html#colocation) oltre al partizionamento. Ma lato CUBA non è necessario cambiare nulla, il multi-tenancy è un processo assolutamente trasparente. 

Questo è tutto. Con il minimo sforzo, possiamo scalare l'applicazione PetClinic in orizzontale e usare il multi-tenancy anche a livello di database. Tutta la complessità è nascosta nel framework e nel plugin Citus.

## Conclusione
 
La creazione di applicazioni multi-tenant potrebbe essere un lavoro impegnativo. Dovreste considerare tutte le opzioni prima di iniziare a implementare questo approccio. E la tecnologia è solo una parte dell'equazione: è necessario considerare i costi di licenza, le tariffe dei servizi cloud, la manutenibilità, la scalabilità, ecc. Al giorno d'oggi, il modello PaaS, insieme alla containerizzazione e ai processi di DevOps propriamente detti, appare più attraente rispetto alla "tradizionale" architettura multi-tenant, ma la richiesta di tali applicazioni è ancora notevole. 

Se decidete di procedere con il multi-tenancy, assicuratevi che la vostra applicazione soddisfi tutti i requisiti di sicurezza, ad esempio GDPR, HIPAA, ecc. Alcuni di essi possono proibire esplicitamente la memorizzazione dei dati nello stesso database, per cui sarà necessario utilizzare un approccio "database separato" per soddisfare tali requisiti.

E ricordate che gli strumenti sono importanti. Un framework di sviluppo adeguato e sistemi di gestione dei dati che supportino l'architettura multi-tenancy può semplificare notevolmente lo sviluppo e prevenire molti problemi tipici legati all'architettura multi-tenancy.