## Panoramica
L'add-on permette di ricevere informazioni sugli eventi Spring container da qualsiasi blocco applicativo del sistema.
Nella semplice configurazione in esecuzione in una singola JVM è possibile inviare eventi da blocchi `core` a blocchi `web` per notificare le schermate dell'interfaccia utente o i managed beans. In un ambiente cluster, un evento pubblicato all'interno di un blocco sarà ricevuto nei blocchi middleware e nei blocchi client `web` e `portal`.
## Funzionalità
- Eventi asincroni, compreso il deployment locale.
- Ordine esatto solo all'interno di un blocco.
- Non c'è ritrasmissione - la consegna nel deployment distribuito è garantita 'al massimo una volta sola'.
- Nel deployment distribuito il blocco web inizia a ricevere le notifiche non appena un utente si connette.
## Collegamenti
- [Demo project](https://github.com/cuba-platform/global-events-demo)
- [Documentazione](https://github.com/cuba-platform/global-events-addon/blob/master/README.md)
