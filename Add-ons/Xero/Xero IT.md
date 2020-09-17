## Panoramica
Questo componente permette di integrarsi facilmente con le API di Xero.

Sfrutta essenzialmente l'SDK ufficiale [Xero Java](https://github.com/XeroAPI/Xero-Java). La configurazione dell'SDK avviene tramite le proprietà dell'applicazione CUBA.
Per poter scaricare tutte le dipendenze è necessario aggiungere i seguenti repository Maven al vostro `build.gradle`:
```
buildscript {         \n//...
    repositories {\n        maven { url "https://raw.github.com/XeroAPI/Xero-Java/mvn-repo" }
        maven { url "https://raw.github.com/XeroAPI/XeroAPI-Schemas/mvn-repo" }
                                        //...
                                }
                        }
```
È possibile aggiungere i repository anche utilizzando la finestra di configurazione dei repository Studio: modificare le proprietà del progetto, quindi fare clic sul pulsante nel campo Repository.

Un [video tutorial](https://www.youtube.com/watch?v=A5HZM-wlOJA) su come configurare l'SDK Xero per integrarlo con un'applicazione CUBA.