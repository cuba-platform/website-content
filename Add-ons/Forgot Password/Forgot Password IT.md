## Panoramica
Questo componente fornisce le seguenti funzionalità una volta aggiunto ad un progetto CUBA:
* Consente di visualizzare un link per reimpostare la password nella finestra di login, inviando una email all'utente.
* Abilita l'accesso sia usando il nome utente sia con l'email registrata (utilizzabile anche attraverso REST API). NOTA: richiede un vincolo UNICO sull'attributo email dell'entità User (in parole povere, le email degli utenti devono essere uniche nel sistema).
* Espone un nuovo servizio REST (extsec_UserManagementService) che consente ai client REST di utilizzare la funzionalità di password dimenticata tramite chiamate API
Per maggiori informazioni, consultare il [README](https://github.com/pfurini/cuba-component-forgot-password#cuba-forgot-password-component) su GitHub.