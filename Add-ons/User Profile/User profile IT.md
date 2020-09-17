## Panoramica
Questo componente dell'applicazione fornisce le seguenti funzionalità una volta aggiunto ad un progetto CUBA:
* Fornisce un `UserProfileService` con metodi per ottenere e aggiornare i dettagli dell'utente attualmente connesso (il _profilo utente_)
* Espone un nuovo endpoint REST API (_Richardson Maturity Model - Level 2_ compliant), chiamato `/rest/nxsecup/v1/userProfile` che supporta le richieste GET (`getProfile`) e PUT (`updateProfile`), e un endpoint `/rest/nxsecup/v1/userProfile/password` che supporta le richieste POST per l'aggiornamento della password dell'utente
* Aggiunge una schermata `userProfile`, e la corrispondente voce di menu dopo quella delle Impostazioni nel menu principale della Guida
* Opzionalmente nasconde il pulsante _Cambia password_ dalla schermata `Impostazioni` (perché quel pulsante è replicato nella schermata `userProfile`)
Per maggiori informazioni, consultare il [README](https://github.com/pfurini/cuba-component-user-profile#cuba-user-profile-component) su GitHub.