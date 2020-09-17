## Panoramica
L'add-on introduce il supporto di un unico database multitenancy per le applicazioni CUBA.
Consente di utilizzare una singola istanza di applicazione per servire più tenant - gruppi di utenti che sono invisibili l'uno all'altro e non condividono i dati a cui hanno accesso in scrittura.
Il componente supporta due tipi di dati:
- Dati comuni - condivisi tra tenant.
- Dati specifici dei singoli tenant - non accessibili ad altri tenant.
Tutti i tenant hanno i propri utenti amministratori, che possono creare degli utenti tenant e assegnare ruoli e permessi specifici ai tenant.
## Funzionalità
- Differenziazione dell'accesso ai dati per gruppi di utenti all'interno di una singola applicazione.
- Semplice configurazione di entità specifiche del tenant.
- Interfaccia utente per la gestione dei tenant.
## Collegamenti
- [Progetto Demo](https://github.com/cuba-platform/multitenancy-addon-demo)
- [Documentazione](https://github.com/cuba-platform/multitenancy-addon/blob/master/README.md)
