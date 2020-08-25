## Panoramica
Questo componente controlla automaticamente la vostra applicazione CUBA per verificarne il corretto funzionamento. Mostrerà i risultati della verifica dello stato di salute all'amministratore dell'applicazione con informazioni su cosa è andato storto e informazioni su come risolvere il problema.
Questo aiuterà l'amministratore del vostro software in due modi:
1. Installando e configurando il software come voi (in qualità di sviluppatore) intendevate
2. Evidenziando i problemi con il software in esecuzione
È possibile definire i propri controlli sullo stato di salute al momento dello sviluppo o durante il runtime per verificare lo stato dell'applicazione. Ecco alcuni esempi di possibili controlli:
* Un servizio è online e in grado di accogliere le richieste?
* Il database contiene un Report che avete creato e di cui avete bisogno nell'applicazione?
* I filtri personalizzati memorizzati nel database si adattano al modello di entità?
* Tutti gli script di modifica del database sono stati applicati con successo?
Per maggiori informazioni, consultare il [README](https://github.com/mariodavid/cuba-component-health-check#cuba-platform-component---health-check) su GitHub.