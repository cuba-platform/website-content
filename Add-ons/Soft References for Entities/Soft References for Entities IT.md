## Panoramica
Questo componente può essere utilizzato per il collegamento di entità senza chiave esterna nel database.

## Cos'è un soft reference?
Un soft reference è un riferimento nel modello dei dati, che non crea una chiave esterna nel database e non crea un'integrità referenziale. Si tratta solo di un riferimento "debole" o "soft" ad un'altra entità.

**Esempio: Cliente - Ordine**

Prendiamo il seguente esempio Cliente <>--> Ordine. Un cliente può avere più ordini. Lo modelliamo come composizione in CUBA. Questo significa che ci sarà una colonna "customer_id" nella tabella degli ordini, che memorizza la chiave primaria del cliente nella tabella degli ordini. Per ogni Ordine, c'è un Cliente associato ad esso.
Nell'esempio di riferimento soft, questo sarà diverso. Ci sarà sempre una colonna "customer_id" nella tabella degli ordini. Al contrario, non ci sarà alcuna associazione di chiavi esterne nel database. Invece la seguente stringa sarà memorizzata in questa colonna di riferimento soft: esempio $Customer-2fdc4906-fa89-11e7-8c3f-9a214cf093ae.

### Le chiavi esterne sono importanti, perché qualcuno non vorrebbe averle?
Esatto. Per quasi tutti i casi, non c'è bisogno di usare riferimenti soft. Poiché mancano tutte le caratteristiche peculiari dell'integrità referenziale, ci sono molti lati negativi. Perché ne abbiamo bisogno comunque? Ecco un esempio:

**Esempio: Cliente - Ordine - Commento**

Supponiamo di voler avere una colonna chiamata "Commento". I commenti possono essere creati per molte entità diverse. Ci possono essere commenti per i clienti così come commenti per ordini, prodotti, ecc. Il modo comune per gestire questo in un linguaggio di programmazione come Java è quello di creare un'interfaccia chiamata Commentable. Questa interfaccia contrassegna tutte le classi che vogliono essere commentabili come tali. Quindi la classe Comment potrebbe avere un riferimento ad un oggetto Commentable. Questo è il polimorfismo.

Tuttavia - non esiste un equivalente nativo in un database relazionale. Il motivo è che quando si vuole creare una chiave esterna, è necessario puntarla su una tabella di destinazione. Ma non esiste una tabella generica a cui si possa puntare. Al contrario, si vuole puntare a tabelle totalmente diverse.

Nei casi in cui si desidera una funzionalità generica, i soft references possono essere la soluzione.