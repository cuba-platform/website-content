## Panoramica
Il controllo della cronologia e della scadenza delle password è una funzionalità importante negli ambienti aziendali. CUBA Password-plus è un componente aggiuntivo per la piattaforma CUBA che implementa la convalida della cronologia delle password.

Quando l'utente cambia la sua password, il componente convalida la password inserita rispetto a una lista di password memorizzate in una tabella. Una volta convalidata la nuova password, quella vecchia viene memorizzata in quella tabella per essere controllata in futuro.

L'amministratore può attivare o disattivare la funzione di tracciamento della password e impostare la lunghezza della tabella di tracciamento della password.
Estende `UserChangePasswordDialog` per aggiungere la nuova validazione e il metodo `changeUserPassword` della classe `UserManagementServiceBean` per memorizzare le password nella nuova tabella.

Per maggiori informazioni, consultate il [README](https://github.com/pakuda/pswdplus/blob/master/README.md) su GitHub.