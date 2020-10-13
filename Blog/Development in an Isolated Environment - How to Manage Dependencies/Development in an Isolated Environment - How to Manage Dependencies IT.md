Sviluppare in un Ambiente Isolato - Come Gestire le Dipendenze?

Come costruire un vero e proprio stack di framework e librerie per sviluppare comodamente se si lavora da remoto in un villaggio lontano, volando per 8 ore su un aereo, o se l'accesso alla rete mondiale della vostra azienda è così limitato che non si possono utilizzare archivi di artefatti pubblici, come maven central? Come possiamo disporre di tutti gli artefatti necessari da cui dipendiamo, comprese le dipendenze transitive? Come aggiornarli con le nuove versioni del framework?

Cerchiamo di trovare le risposte a queste domande. In questo articolo vi parlerò di CUBA SDK - uno strumento a riga di comando che fornisce la possibilità di risolvere tutte le dipendenze transitive per le librerie Maven e gestirle in repository remoti. Inoltre, vi illustreremo con un esempio le nostre pratiche collaudate per qualsiasi applicazione Java che utilizzi le dipendenze Maven.

Come sapete, il nostro team lavora su CUBA - un framework Open Source basato su Java per lo sviluppo di applicazioni aziendali. La piattaforma CUBA è un intero ecosistema costituito dal framework e da vari add-on che forniscono funzionalità extra. E questa funzionalità è pronta all'uso in pochi click.  Negli ultimi anni il framework ha ottenuto una grande popolarità. Ora è utilizzato da più di 20.000 sviluppatori in tutto il mondo. Con la crescita della popolarità abbiamo incontrato molti casi interessanti. Questo articolo si soffermerà su uno di essi. Probabilmente potrebbe essere d'aiuto per il vostro progetto, soprattutto se lavorate in un'azienda che ha un rigoroso sistema di sicurezza. 

## Caricamento delle dipendenze transitorie nei repository interni 

Quasi tutti i progetti JVM moderni che utilizzano Apache Maven o Gradle come strumento di compilazione hanno un gran numero di dipendenze transitive per ogni libreria e/o framework utilizzato. Con un repository condiviso accessibile dalla rete, queste dipendenze sono risolte automaticamente da un gestore di dipendenze. 

Ma cosa fare nel caso in cui i repository pubblici siano inaccessibili dalla rete interna? 

## Soluzioni Possibili 

La soluzione può sembrare semplice - ci basta prendere Nexus e delegare l'accesso ai repository attraverso di esso. In effetti, nella maggior parte dei casi può funzionare, ma non se si hanno requisiti di sicurezza particolarmente restrittivi. È possibile incontrarli in aziende appartenenti a determinate tipologie: militari, finanziarie, ministeri e altre autorità governative. L'accesso a internet in tali società è strettamente regolamentato e di solito non consente il tradizionale proxying. 

Cosa possiamo fare? 

- Opzione 0. Supplicare il team di sicurezza.
- Opzione 1. Gateway.
- Opzione 2. Gestione manuale delle dipendenze.

L'opzione 0 non la prendiamo in considerazione, quindi analizziamo le opzioni 1 e 2. 

L'opzione gateway implica che un'azienda ha un computer gateway che può essere connesso sia alla rete esterna che a quella interna. Dopo aver caricato e controllato le librerie necessarie dalla rete esterna, il gateway passa alla rete interna, e solo dopo che le librerie sono state caricate nei repository interni. L'aggiunta di nuove dipendenze o l'aggiornamento di quelle esistenti per un singolo progetto di solito richiede molto tempo, in quanto è necessario ottenere l'accesso al gateway, caricare gli artefatti necessari, controllare che tutte le dipendenze transitive siano state caricate correttamente, e poi scaricare le dipendenze nel repository interno. Se ci sono molti progetti in azienda, lo sviluppo può rimanere fermo fino a quando le dipendenze richieste non diventano disponibili nei repository interni.

Considerando l'opzione di gestione manuale delle dipendenze, ogni volta che si aggiornano o si aggiungono componenti si dovrà controllare la lista delle dipendenze delle librerie e confrontarla con le librerie già caricate. 

Come possiamo vedere, aggiungere o aggiornare le dipendenze può essere un processo piuttosto complesso.

Se non si considerano i sistemi di sicurezza aziendali, il problema dell'accesso ai repository continua ad esistere. Per esempio, uno sviluppatore che si trova a lavorare in una zona isolata dove la connessione a Internet è debole. Per prepararsi a un caso del genere si possono sempre provare i plugin offline per Gradle o Maven. Ma se avete diversi progetti, per ognuno di essi dovrete 
- configurare un plugin offline
- aggiungere al progetto tutte le dipendenze potenzialmente utili in modo che i plugin offline possano aggiungerle correttamente nella cache delle dipendenze, giusto per stare sicuri 
- scaricare tutte le dipendenze nella cartella locale

Tutto ciò non è molto comodo, in quanto il progetto potrebbe essere sovraccarico di dipendenze e configurazioni che dovrete tenere a mente e cancellare dal progetto in seguito. E anche con tutto questo il problema persiste se volessimo creare un nuovo progetto, perché potrebbero mancare i moduli necessari. 

Quindi cosa possiamo fare per risolvere questi problemi?

## CUBA SDK

Tra gli utenti della Piattaforma CUBA vi sono aziende che limitano l'accesso alla rete esterna per motivi di sicurezza.

Abbiamo deciso di facilitare la vita degli utenti e di creare uno strumento a riga di comando chiamato CUBA SDK, in grado di risolvere tutte le dipendenze della piattaforma CUBA, dei componenti aggiuntivi e di tutte le altre librerie con coordinate maven. 

Quindi qual è la differenza tra CUBA SDK e i plugin offline per Gradle o Maven? 
La differenza principale è che CUBA SDK non mette in cache le dipendenze di un particolare progetto. Permette di sincronizzare gli artefatti tra i repository interni ed esterni, in modo che il processo di sviluppo in un ambiente isolato sia confortevole.
CUBA SDK non richiede un progetto, quindi è possibile creare uno stack offline completo di framework, add-on e librerie con tutte le dipendenze. 

Gli sviluppatori possono utilizzarlo se lavorano su più progetti o prevedono di crearne uno nuovo, e quindi non sanno quali moduli saranno utilizzati nel progetto. Con l'aiuto di CUBA SDK questi moduli possono essere caricati in anticipo nel repository locale o interno. 

Le aziende inoltre possono beneficiare di CUBA SDK usandolo per la sincronizzazione centralizzata dei repository interni.

![text](http://192.168.45.58:1337/uploads/1ba02a67d60047cc97c6798d345289ae.png)

CUBA SDK fornisce la possibilità di risolvere, esportare e caricare in repository esterni tutte le dipendenze per il framework CUBA, i componenti aggiuntivi o qualsiasi libreria esterna con pochi semplici comandi. Per reti completamente isolate è possibile utilizzare comandi di importazione ed esportazione o installare CUBA SDK sul gateway.

Vantaggi di CUBA SDK:

- raccoglie automaticamente tutte le dipendenze incluso il codice sorgente per le librerie caricate 
- risolve le dipendenze per la piattaforma CUBA e i componenti aggiuntivi
- verifica la presenza di nuove versioni delle librerie e le installa
- può lavorare con diversi repository alla volta per la ricerca di artefatti, compresi i repository locali di maven
- ha un repository Nexus OSS incorporato 
- fornisce la possibilità di caricare artefatti in diversi repository alla volta, inclusi i repository maven locali 
- importa ed esporta gli artefatti con tutte le dipendenze
- fornisce una modalità interattiva con suggerimenti per l'installazione della piattaforma CUBA e dei componenti aggiuntivi
- utilizza gli strumenti Gradle per risolvere le dipendenze
- è indipendente dall'IDE utilizzato 
- può essere installato su server CI

## Comandi dell'SDK

L'elenco completo dei comandi disponibili si trova su [GitHub](https://github.com/cuba-platform/cuba-sdk).

CUBA SDK supporta di default tre tipi di componenti: CUBA Framework, CUBA add-on e una libreria che può essere caricata attraverso le coordinate maven. Questa lista può essere estesa ad altri tipi di componenti tramite i plugin CUBA SDK. 

È possibile installare un componente in un repository remoto tramite il comando **install**. Durante la creazione di SDK abbiamo previsto che possa essere installato su una macchina gateway o su un dispositivo portatile. In questo caso l'installazione dei componenti è possibile tramite i comandi **resolve** e **push**.

**resolve** - risolve e scarica tutte le dipendenze nella cache locale dell'SDK
**push** - passa gli artefatti caricati con le loro dipendenze ai repository di destinazione impostati

Per lavorare con i repository, SDK ha un gestore di repository incorporato.

Il repository manager supporta i repository locali e remoti che sono divisi in due categorie all'interno dell'SDK 
- **source** - repository utilizzati per la ricerca di artefatti
- **target** - repository su cui verranno caricati gli artefatti

L'SDK stesso può essere utilizzato come repository. Usando il comando **setup-nexus** SDK scarica, installa e configura il repository Nexus OSS. Per lanciare e fermare il repository usare i comandi start e stop.

Per controllare e installare gli aggiornamenti basta eseguire il comando **check-updates**. 

## Risoluzione delle dipendenze

Il problema principale che l'SDK è destinato a risolvere è la corretta risoluzione e la raccolta di dipendenze per i componenti. Durante il processo di sviluppo abbiamo provato diversi approcci per risolvere le dipendenze transitive dei componenti. Inizialmente abbiamo avuto l'idea di analizzare i file .pom e di comporre un albero delle dipendenze. Ma siamo arrivati alla conclusione che l'analisi manuale delle dipendenze non era una buona idea, soprattutto perché Apache Maven è già in grado di farlo normalmente. 

## Maven come Dependency Manager

Così, abbiamo adottato Apache Maven come strumento per la gestione delle dipendenze transitive.

Per farlo CUBA SDK carica maven nella cartella home dell'SDK ed esegue i comandi tramite Java Runtime.

Per esempio, il comando 
```
dependency:resolve -Dtransitive=true -DincludeParents=true -DoverWriteSnapshots=true -Dclassifier=<classifier> -f pom.xml
```

ci permette di risolvere tutte le dipendenze transitive dei componenti descritte in pom.xml, e di caricarli automaticamente nell'archivio del maven locale. Dopo di che eseguiamo il comando
```
org.apache.maven.plugins:maven-deploy-plugin:3.0.0-M1:deploy-file -Durl=<repository URL>
```
che carica gli artefatti nel repository richiesto.

Il seguente comando ci permette di caricare la libreria nel repository locale.
```
org.apache.maven.plugins:maven-dependency-plugin:3.1.1:get -Dartifact=<maven coordinates>
```
Per eseguire i comandi Maven nell'applicazione CUBA SDK abbiamo generato il file settings.xml. Esso contiene la lista di tutti i repository che devono essere utilizzati per il recupero e il caricamento degli artefatti.

## Gradle come Dependency Manager

Nella prima versione dell'applicazione le dipendenze venivano risolte correttamente ma piuttosto lentamente, e durante i test abbiamo avuto delle collisioni durante la risoluzione delle dipendenze per alcuni componenti aggiuntivi della piattaforma CUBA. Tuttavia, non ci sono stati problemi di questo tipo durante la compilazione del progetto con Gradle. 

Così abbiamo deciso di spostare la logica di risoluzione delle dipendenze su Gradle. Per fare questo abbiamo creato uno script build.gradle con i task necessari per caricare e risolvere le dipendenze dei componenti.

Per richiamare i task Gradle abbiamo usato Gradle Tooling API. 

Per definire il percorso delle dipendenze attraverso Gradle abbiamo usato l'API di risoluzione degli artefatti. Il codice seguente ci ha aiutato ad ottenere il percorso del sorgente della libreria:
```
 def component = project.dependencies.createArtifactResolutionQuery()
            .forComponents(artifact.id.componentIdentifier)
            .withArtifacts(JvmLibrary, SourcesArtifact)
            .execute()
            .resolvedComponents[0]
 def sourceFile = component?.getArtifacts(SourcesArtifact)[0]?.file
```
Così, abbiamo ottenuto i percorsi di tutti i file nella cache locale Gradle e li abbiamo salvati nell'archivio dell' SDK. 

Per risolvere e caricare le dipendenze per i componenti nella cache locale aggiungiamo i componenti alla configurazione e otteniamo tutte le dipendenze usando `lenientConfiguration`. 
```
project.ext.properties["toResolve"].tokenize(';').each {
            dependencies.add 'extraLibs', it
        }
        def resolved = [:]
        configurations.all.collect {
            if (it.canBeResolved) {
                it.resolvedConfiguration.lenientConfiguration.artifacts.each { art ->
                    try {
                        ...
                    } catch (e) {
                        logger.error("Error: " + e.getMessage(), e)
                        logger.error("could not find pom for {}", art.file)
                    }
                }
            }
        }
```        
Usiamo `lenientConfiguration` per evitare che lo script Gradle vada in crash nel caso in cui il componente non possa essere trovato nei repository.

Per caricare gli artefatti nei repository, CUBA SDK esegue il task Gradle `PublishToMavenRepository`.
```
task publishArtifact(type: PublishToMavenRepository) {
    doLast {
        if (project.ext.hasProperty("toUpload")) {
            def toUpload = new JsonSlurper().parseText(project.ext.properties["toUpload"])
            def descriptors = new JsonSlurper().parseText(project.ext.properties["descriptors"])

            artifactId toUpload.artifactId
            groupId toUpload.groupId
            version toUpload.version
            descriptors.each { descriptor ->
                artifact(descriptor.filePath) {
                    classifier descriptor.classifier.type
                    extension descriptor.classifier.extenstion
                }
            }
        }
    }
}
```
Grazie a Gradle evitiamo le collisioni risolvendo le dipendenze transitive e velocizziamo notevolmente il funzionamento dell'applicazione. 

## Compilazione del Progetto

Per compilare CUBA SDK abbiamo utilizzato lo stesso approccio di CUBA CLI. Con lo strumento jlink abbiamo compilato tutti i moduli necessari e li abbiamo combinati ad un JRE personalizzato pacchettizzato insieme all'applicazione. Questo approccio ha reso l'SDK indipendente dalla versione Java installata. Potete trovare un esempio di tale build nel [CLI Core Sample project](https://github.com/cuba-platform/cli-core-sample). 

## Supporto di Plugins di terze parti

Poiché CUBA SDK è basato sulla [CLI Core library](https://github.com/cuba-platform/cli-core), supporta plugin di terze parti. Al momento, l'SDK dispone di manager di dipendenze per maven e gradle e di provider per componenti CUBA implementati tramite plugin di terze parti.

Vediamo un esempio di come si può estendere la funzionalità dell'SDK con un plugin. Creeremo un provider per gli Spring Boot Starters dal noto progetto [Spring Initializr](https://start.spring.io/).

Per prima cosa creiamo un nuovo progetto. Come esempio useremo il plugin CUBA CLI, come descritto [qui](https://github.com/cuba-platform/cuba-cli/wiki/Plugin-Development), e aggiungeremo le dipendenze:
```
implementation "com.haulmont.cli.core:cli-core:1.0.0"
implementation "com.haulmont.cli.sdk:cuba-sdk:1.0.1"
```
Creiamo un nuovo provider per gli spring boot starters - SpringBootProvider, che estende BintraySearchComponentProvider. BintraySearchComponentProvider consente la ricerca automatica di versioni di componenti accessibili utilizzando l'API Bintray.
```
class SpringBootProvider : BintraySearchComponentProvider() {
   var springComponentsInfo: SpringComponentsInfo? = null

   override fun getType() = "boot-starter"
   override fun getName() = "Spring boot starter"

 ...

   override fun load() {
       springComponentsInfo = Gson().fromJson(readSpringFile(), SpringComponentsInfo::class.java)
   }

   private fun readSpringFile(): String {
       return SpringComponentsPlugin::class.java.getResourceAsStream("spring-components.json")
           .bufferedReader()
           .use { it.readText() }
   }
```

Questo provider cercherà i componenti accessibili dal file spring-components.json che è la [versione json del file yml nell'applicazione Spring Initializr](https://github.com/spring-io/start.spring.io/blob/5294926a91392c12cc709f481c3a1df9650d5747/start-site/src/main/resources/application.yml). 

Per la mappatura da json agli oggetti creiamo delle semplici data classes: 
```
data class SpringComponent(
   val name: String,
   val id: String,
   val groupId: String?,
   val artifactId: String?,
   val description: String?,
   val starter: Boolean? = true
)

data class SpringComponentCategory(
   val name: String,
   val content: List<SpringComponent>
)

data class SpringInitializr(
   val dependencies: List<SpringComponentCategory>
)

data class SpringComponentsInfo(
   val initializr: SpringInitializr
)
```
Per aggiungere questo provider agli altri provider nell'SDK, dobbiamo registrarlo nell'evento init del plugin:
```
class SpringBootComponentsPlugin : CliPlugin {
   private val componentRegistry: ComponentRegistry by sdkKodein.instance<ComponentRegistry>()

   @Subscribe
   fun onInit(event: InitPluginEvent) {
       val bootProvider = SpringBootProvider()
       componentRegistry.addProviders(bootProvider)
       bootProvider.load()
   }

}
```

E questo è tutto. Ora, per installare il plugin attraverso il terminale o l'IDE, eseguire il comando *gradle installPlugin*.

Run SDK
![text](http://192.168.45.58:1337/uploads/83754cff3c7440a7ad4ad3b5bb34572a.png)

Possiamo vedere che il nostro plugin è stato caricato con successo. Ora controlliamo che la nostra logica funzioni con l'aiuto del comando *resolve boot-starter*:
![text](http://192.168.45.58:1337/uploads/5eac281fa5b342679ce4cbe129461f79.png)

![text](http://192.168.45.58:1337/uploads/5c09660756d0414591f2fa0a26ff2df8.png)

Come possiamo vedere, i suggerimenti per i componenti e le loro versioni funzionano come previsto.

Se si memorizzano artefatti e dipendenze nello storage in modo diverso rispetto ai repository maven, allora si possono usare i plugin per implementare la propria interfaccia *ArtifactManager*, che può contenere la logica per lavorare con il proprio storage personalizzato.

Il codice sorgente del plugin di esempio può essere consultato dalla [pagina GitHub](https://github.com/cuba-platform/cuba-sdk-spring-boot-plugin).

## Conclusioni

In primo luogo, CUBA SDK sarà utile per le aziende che hanno un accesso limitato alla rete esterna a causa dei requisiti di sicurezza. 

Al di là della politica di sicurezza delle aziende, il problema della disponibilità dei repository è importante anche per gli sviluppatori. Ad esempio, se uno sviluppatore deve lavorare da solo in viaggio e ha una scarsa connessione internet. In questo caso CUBA SDK è un'ottima soluzione che vi aiuterà a creare uno stack efficiente di librerie e framework a livello locale, direttamente sul vostro PC.