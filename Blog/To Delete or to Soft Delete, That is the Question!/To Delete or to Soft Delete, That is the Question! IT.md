La cancellazione logica è una tecnica ampiamente utilizzata nelle applicazioni aziendali. Consente di contrassegnare alcuni record come cancellati senza cancellarli effettivamente dal database. In pratica, si impedisce la selezione di un record cancellato in modo logico, mentre tutti i record esistenti possono ancora fare riferimento ad esso.

## Quando ha senso utilizzare la cancellazione logica?

Se lo domanderete ad alcuni sviluppatori a caso, molto probabilmente sentirete che la cancellazione logica viene utilizzata per poter ripristinare i record cancellati. Tuttavia, se questo è l'unico requisito aziendale che vi spinge a integrare la cancellazione logica - pensateci due volte. 
Considerate i seguenti requisiti oltre al "semplice ripristino dei dati" prima di prendere una decisione sull'utilizzo della cancellazione logica nel vostro progetto:

1. Registrazione della cronologia e audit (ad es. per motivi legali)
2. Mantenere l'integrità referenziale ed evitare la cancellazione a cascata
3. È necessario cancellare in modalità "Graceful". Ad esempio, un processo aziendale di lunga durata può richiedere dati che potrebbero essere stati "cancellati", ma che sono comunque necessari affinché questo particolare processo si concluda

Se notate che le vostre esigenze aziendali rientrano nell'elenco precedente, significa che potrebbe essere necessario implementare la funzione "Soft Delete" nella vostra applicazione. 

E implementare la funzione "Soft Delete" è facile! Basta aggiungere la colonna "Is_Deleted" o "Delete_Date" alle vostre tabelle (o agli attributi del vostro documento) e sostituire tutti i comandi di cancellazione nel codice della vostra applicazione con degli aggiornamenti. E sì, è necessario modificare anche tutti i comandi di selezione per tenere conto di questo nuovo attributo. O è meglio creare una vista? O una vista materializzata? In questo caso, potrebbe essere necessario implementare trigger "instead of" o stored procedure per gestire le viste. E cancellando un'entità si potrebbe voler cancellare alcuni attributi a cui fa riferimento. In questo caso, per poterla ripristinare è necessario anche memorizzare il contesto di eliminazione. E... è qui che la vita di uno sviluppatore si complica. 

## Le sfide della cancellazione logica

In realtà, l'implementazione di una cancellazione logica "corretta" non è un compito facile. A volte gli sviluppatori scelgono di NON implementare la cancellazione logica solo perché richiede molti sforzi. Si potrebbe pensare ad altre opzioni come il mirroring delle tabelle con una tabella speciale "cestino" che contiene i record cancellati dalla tabella "originale". 

Ma se si decide di andare avanti con l'implementazione del soft delete, è necessario tenere conto di alcune condizioni.

**Supporto dei database.** Uno dei problemi con i record che sono stati contrassegnati come cancellati sono le loro chiavi univoche. Ad esempio, dobbiamo memorizzare gli utenti insieme alle loro e-mail. Le email devono essere univoche, quindi dobbiamo creare un indice univoco per la tabella che memorizza gli utenti. E se un utente viene cancellato, dovremmo essere in grado di inserire un nuovo utente con la stessa email. Quindi, il vostro database dovrebbe essere in grado di gestire tali casi. Una delle opzioni è includere un campo che contrassegna il soft delete nella chiave univoca. Se il vostro campo non è un semplice valore booleano, ma il timestamp di eliminazione, allora potrebbe funzionare. 

Un'altra opzione - indici univoci speciali. Per esempio, in PostgreSQL sono chiamato=i partial [unique indexes](https://www.postgresql.org/docs/12/indexes-partial.html), in MSSQL [filtered indexes](https://docs.microsoft.com/en-us/sql/relational-databases/indexes/create-filtered-indexes). Essi permettono di non prendere in considerazione alcune righe quando si costruiscono gli indici specificando un predicato. Quindi, per l'implementazione della funzione di cancellazione logica, possiamo escludere i record "cancellati" dall'indice.

Se il vostro database supporta i trigger per le viste, ci sono buone probabilità che possiate creare il vostro modello di dati usando le viste e il tipo di trigger `instead of`. Ma questo approccio ha un impatto sulle prestazioni e non risolve il problema dei vincoli univoci applicati alla/e tabella/e sottostante/i.

**Supporto Applicativo.** Assicuratevi che TUTTE le query che menzionano entità soft-deleted siano sottoposte a un doppio controllo, altrimenti possono portare a perdite di dati inaspettate e a problemi critici di performance. Nel mondo ideale, uno sviluppatore non dovrebbe essere a conoscenza dell'esistenza del soft-delete. Quindi, se possibile, cercate di introdurre delle API di accesso ai dati e impedite agli sviluppatori di utilizzare altre strutture per accedere a un database (come la connessione diretta JDBC). Si potrebbe anche voler scavalcare le API standard come l'Entity Manager di JPA per supportare correttamente la cancellazione logica. 

**Rivedere l'utilizzo delle API standard.** Prima di tutto, scordatevi di utilizzare alcune delle funzionalità standard del DB e di JPA. Se avete la vostra tabella di database e il vostro modello di vincoli accuratamente progettato, così come il vostro livello di accesso JPA, l'introduzione della cancellazione logica richiede di considerare quanto segue:

1. Le politiche standard di cancellazione in cascata non funzioneranno. Ricordate che in caso di cancellazione logica, non cancellate i record, ma li aggiornate. Se avete ancora bisogno di una cancellazione a cascata, i trigger possono aiutarvi ad implementare questo comportamento.  
2. Per quanto riguarda i trigger, dovrete rivedere anche quelli, per assicurarvi che il codice dei trigger pre- e post-cancellazione sia eseguito nei trigger pre- e post-aggiornamento solo durante l'aggiornamento come soft-delete, e non durante il processo di aggiornamento "standard".
3. I metodi marcati come `@PreRemove` e `@PostRemove` non funzioneranno. Anche in questo caso, cercare altre opzioni per implementare la funzionalità che è codificata in questi callback. La regola in questo caso è simile a quella applicata ai trigger - nei callback relativi all'aggiornamento è necessario distinguere tra i processi di aggiornamento standard e quelli di cancellazione logica. 

**Dovete comunque soddisfare i requisiti normativi.** A titolo di esempio - il GDPR. Il regolamento dice che i dati devono essere completamente rimossi se viene richiesto da un utente. Per soddisfare questo requisito è ancora necessario implementare un processo di "spurgo" oltre alla cancellazione logica. E questa procedura di eliminazione nella maggior parte dei casi causerà cancellazioni a cascata nel vostro database. Quindi è necessario fare attenzione a non cancellare dei dati critici per l'azienda. 

## Implementazione del Soft Delete nella piattaforma CUBA

La Piattaforma CUBA supporta la cancellazione logica e fornisce la funzione di soft-delete come parte del comportamento predefinito dell'applicazione. Nella maggior parte dei casi, uno sviluppatore non dovrebbe preoccuparsi se viene utilizzata o meno la cancellazione logica. L'API di manipolazione dei dati in CUBA è per il 99% identica alla nota API JPA. Gli sviluppatori possono usare il metodo `remove()` dell'`EntityManager` o anche usare JPQL per manipolare i dati nell'applicazione. 

Dietro le quinte, la piattaforma CUBA intercetta tutte le invocazioni di EntityManager e le query JPQL, e le trasforma in base alle impostazioni dell'applicazione. Il framework svolgerà il lavoro pesante per noi e fornirà un comportamento corretto di default: i record "cancellati" non saranno interessati da un aggiornamento, l'istruzione `DELETE` sarà trasformata in `UPDATE` e `SELECT` non leggerà i dati "cancellati".

L'implementazione è costruita su tre pilastri principali:

1. Interfaccia `SoftDelete`. CUBA usa JPA (EclipseLink) come framework di default per accedere ai dati. Se l'entità JPA implementa l'interfaccia `SoftDelete`, CUBA non cancellerà i dati dal database ma li marcherà semplicemente come cancellati.
2. Classe `EntityManager`. Questa è l'API principale per accedere ai dati in CUBA. Intercetta tutte le richieste al database e invoca le istruzioni `delete` o `update` in base al tipo di entità e al suo valore del campo `soft delete` (abilitato o disabilitato). Quindi è possibile disabilitare la cancellazione logica per le entità a runtime, se necessario. Questo approccio ci permette di invocare i callback JPA relativi alla rimozione di entità, anche se in realtà nella piattaforma CUBA viene generato un comando di aggiornamento.
3. Interfaccia API "JoinExpressionProvider". L'implementazione `SoftDeleteJoinExpressionProvider` genera un predicato che tiene conto dello stato di un'entità (cancellata o meno) e della sua posizione nella query. Ad esempio, se selezioniamo un record esistente che fa riferimento a un record "soft deleted", allora la condizione corretta verrà aggiunta alla query per selezionare il record "cancellato".

Queste API rendono il processo di "soft delete" trasparente per gli sviluppatori ed elimina l'onere di rispondere alla domanda "delete o soft delete" ogni volta che si invoca un'API JPA o si esegue una query JPQL. 

Tutto questo funziona la maggior parte delle volte, ma il nostro non è un mondo perfetto. Per garantire la massima flessibilità, CUBA consente comunque di eseguire SQL nativo e di accedere a un oggetto DataSource sottostante. Quindi, anche con il framework che risolve molti problemi di implementazione di soft delete, è necessario prestare particolare attenzione alle query di basso livello. 

## Conclusione

L'utilizzo della cancellazione logica anche per una sola entità vi condurrà a una serie di nuove convenzioni di sviluppo. È necessario tenere presente che il processo di cancellazione non è solo la cancellazione, ma a volte l'aggiornamento, e non tutti i dati in una tabella di database dovrebbero essere disponibili per l'applicazione. Ciò significa che il vostro codice dovrebbe essere ingegnerizzato in modo da tenere conto di tutto ciò in tutti i livelli dell'applicazione - dallo schema del database fino all'interfaccia utente (sì, anche quella). 

Con un framework adeguato sarete in grado di risolvere tutti questi problemi, ma dovreste comunque tenere a mente che avete "cancellato" dati che in realtà esistono ancora, e adottare standard di sviluppo e convenzioni che ne tengano conto.
