## Semafori

### Il tipo di dato

Un semaforo `S` è un tipo composto con **due campi**:
- `S.V`: un **intero** $\geq 0$ (il "valore" o contatore);
- `S.L`: un **insieme di processi** (gli identificatori dei processi bloccati sul semaforo).

Si inizializza con un valore $k \geq 0$ per `S.V` e con l'insieme vuoto per `S.L`:
```text
semaphore S = (k, {})
```

Offre **due operazioni atomiche** (*spiegate dopo*):
- `wait(S)` o `P(S)`
- `signal(S)` o `V(S)`

### L'operazione `wait`
![[Pasted image 20261103172305.png|338]]_(`p` è il processo che sta eseguendo la `wait`.)_

> [!example] **In parole:**
>- se `S.V > 0` (il semaforo è **verde**): lo si **decrementa** e il processo prosegue;
>- altrimenti, se `S.V = 0`: il processo viene **bloccato** sul semaforo (aggiunto nell'insieme `S.L`).

### L'operazione `signal`

![[Pasted image 20261104093408.png|507]]
> [!example] **In parole:**
>- se **nessun processo** è in attesa: si **incrementa** il valore;
>- altrimenti si **sceglie un processo bloccato** e lo si **sblocca**.

> [!tip] **Entrambe le operazioni sono atomiche**: nessun intreccio tra le istruzioni interne.
### L'invariante del semaforo

Sia $k$ il valore iniziale, $\#signal(S)$ il numero di `signal(S)` eseguite e $\#wait(S)$ il numero di `wait(S)` **completate** (non quelle in cui il processo è ancora bloccato). Allora **in ogni stato** vale:

$$S.V \geq 0 \qquad\text{e}\qquad S.V = k + \#signal(S) - \#wait(S)$$

> [!quote] **Lettura:** 
> il valore del semaforo è sempre "quanti permessi c'erano all'inizio, più quelli restituiti, meno quelli presi". Non può mai andare sotto zero perché chi non trova permessi **si blocca** invece di decrementare.
### Tipi di semafori (per uso)

| Tipo                                            | Valori di `S.V`           | Uso tipico                                                             |
| ----------------------------------------------- | ------------------------- | ---------------------------------------------------------------------- |
| **Binario** (o **mutex**)                       | Solo 0 e 1                | **Mutua esclusione** (da qui il nome _mutex_)                          |
| **Generale** (o **counting**, o **di risorse**) | Qualsiasi valore $\geq 0$ | Contare **risorse** disponibili (posti liberi, elementi nel buffer...) |
| **Ad evento** (_event semaphore_)               | Inizializzato a **0**     | **Usato per la sincronizzazione**: segnalano "è successo X"            |

### Tipi di semafori 

Le differenze riguardano **le proprietà di vivacità** (liveness) e **non** le proprietà di sicurezza (safety), che derivano dagli invarianti.
#### Semafori deboli e forti

- **Debole** (_weak_): `S.L` è un **insieme**. `signal` sceglie un processo **arbitrario**. Nulla garantisce che un processo bloccato venga prima o poi scelto.

- **Forte** (_strong_): `S.L` è una **coda** (FIFO). `signal` sblocca il processo **in testa**.
![[Pasted image 20261104104245.png]]

> [!info] **Proprietà chiave** 
> Con un semaforo **forte** la **starvation è impossibile**, per qualsiasi numero $N$ di processi 

#### Semafori ad attesa attiva (busy-wait)

Semafori **senza** `S.L`: non c'è coda di bloccati, i processi aspettano controllando in continuazione.
![[Pasted image 20261104105245.png]]
Le operazioni sono comunque **atomiche** (nessun intreccio tra l'`await` e il decremento).

> [!fail] **Si perde l'assenza di starvation** 
> Con semafori busy-wait non si può assumere che un processo entri nella CS, nemmeno nella soluzione a 2 processi (nulla stabilisce chi "vince" quando il valore torna positivo).


> [!question] Perchè si usa? 
> In un sistema **multiprocessore** in cui il processo in attesa ha **un processore tutto per sé** e non spreca CPU che servirebbe ad altro; oppure con **poca contesa**, così l'attesa è breve.

---

## Sezione critica con i semafori

### Per due processi

Analizziamo due processi che usano un semaforo `S = (1, {})` come lock attorno alla sezione critica.
![[Pasted image 20261104110342.png|442]]

- `S = (1, {})`: il permesso è **uno solo**.
- Il pre-protocollo è `wait(S)`, il post-protocollo è `signal(S)`.
- Chi arriva per secondo trova `S.V = 0` e **si blocca** finché il primo esce.

##### 1. Riduciamo il diagramma

Come sempre, si costruisce il **diagramma di stato ridotto** (tolte CS e NCS):
![[Pasted image 20261104114920.png|478]]
Qui, quando un processo è a `p2` (o `q2`) significa che è **nella CS**.

##### 2. Diagramma di stato con stati bloccati

Lo stato è una tupla $\langle p, q, \{S.V, S.L\} \rangle$:
- la riga di `p`;
- la riga di `q`;
- **tutto il semaforo**: il valore `S.V` e l’insieme `S.L` dei processi bloccati
![[Pasted image 20261104115038.png|464]]
> [!example] **Lettura passo per passo:**
>1. Stato iniziale: entrambi pronti, `S.V = 1`.
>2. `p` esegue `wait`: `S.V` scende a 0, `p` è nella CS (`p2`). Simmetricamente se parte `q`.
>3. Da $\langle p2, q1, {0,{}} \rangle$ le strade sono due: `p` esce (`signal`), `S.V` torna a 1; oppure `q` tenta `wait` e **si blocca** → $\langle p2, q1B, {0,{Q}} \rangle$.
>4. Da lì l'unica mossa è `p2` (`p` fa `signal`): poiché `Q` è in attesa, **non** si incrementa `S.V` ma si **sblocca `Q`**, che ora è nella CS → $\langle p1, q2, {0, {}} \rangle$.

**PROPRIETÀ DIMOSTRATE**
- Mutua esclusione $\rightarrow$ Poichè non esiste lo stato   $\langle p2, q2, ... \rangle$
- Assenza di deadlock $\rightarrow$ Da ogni stato esce almeno una freccia (chi è bloccato viene sbloccato da chi è in CS)
- Assenza di starvation $\rightarrow$ Quando `Q` è bloccato e `P` esce, `Q` è l'**unico** candidato e viene sbloccato

> [!fail] Con N processi l'assenza di starvation non c'è più
> `signal` sceglie un bloccato **arbitrario**, quindi un processo sfortunato può essere sempre scavalcato dagli altri.
## Sincronizzazione con i semafori

Per la sincronizzazione si usano i **semafori ad evento**: inizializzati a `(0, {})`, servono a **inviare/ricevere un segnale temporale**.
- Chi deve **aspettare** un evento fa `wait`: se l'evento non c'è ancora, si blocca.
- Chi **produce** l'evento fa `signal`.

> [!example] Esempio merge sort concorrente
> Si sortano le due metà e si fondono
>
>![[Pasted image 20261104134927.png|581]]

---
## Il problema produttore-consumatore

Due tipi di processi:
- **produttori**: creano un dato (`produce`) e lo inviano;
- **consumatori**: ricevuto un dato, lo elaborano (`consume`).
### Comunicazione sincrona o asincrona

> [!hint] Tipi di comunicazione
> Quando un dato passa da un processo all'altro, la comunicazione può essere:
>- **sincrona**: non avviene finché **entrambi** (produttore e consumatore) sono pronti;
>- **asincrona**: il **canale ha una capacità** di memorizzazione (un _buffer_), quindi produttore e consumatore non devono incontrarsi.

> [!attention] L'asincrona è molto utile, ma serve un **buffer**, cioè una **struttura dati condivisa con stato modificabile**, scritta dai produttori e letta dai consumatori.
### Buffer infinito

Con un buffer illimitato c'è **un'unica interazione** da sincronizzare: il consumatore **non deve prelevare da un buffer vuoto**. 
![[Pasted image 20261104135609.png|537]]
> [!info] `availItems` è un **semaforo di risorse** che **conta quanti elementi ci sono nel buffer**:

- il produttore, dopo aver inserito, fa `signal` (un elemento in più);
- il consumatore, prima di prelevare, fa `wait`: se il buffer è vuoto (`availItems = 0`) **si blocca** finché arriva un elemento.

**INVARIANTE:** `availItems.V = #buffer` (valore del semaforo = numero di elementi nel buffer).

> [!note] Precisazioni
> 
> - L'invariante è **strettamente vera** solo se si considerano atomici i due gruppi `p2+p3` e `q1+q2`; tra una coppia di istruzioni il valore del semaforo e la lunghezza del buffer possono differire di uno.
> - In questo esempio le operazioni `append` e `take` sono **supposte atomiche**. Con più produttori/consumatori e una struttura non atomica serve un mutex (vedi sotto).

### Buffer limitato

Avendo il buffer limitato il produttore **NON deve aggiungere a un buffer pieno**. Servono quindi **due semafori**, uno per ogni "vincolo":
![[Pasted image 20261104135954.png|522]]
> [!info] I due semafori sono detti **semafori divisi** (_split semaphores_): insieme rappresentano un'unica risorsa vista da due lati.
### Più produttori e più consumatori

Se la struttura dati non è atomica, **due produttori** o **due consumatori** potrebbero manipolarla insieme e corromperla. Serve un **mutex** per la **mutua esclusione** sull'accesso al buffer, **oltre** ai semafori di sincronizzazione.
![[Pasted image 20261104140505.png|575]]

| Semaforo      | Tipo                | Scopo                                                  |
| ------------- | ------------------- | ------------------------------------------------------ |
| `availItems`  | Contatore (risorse) | Sincronizzazione: il consumatore aspetta gli elementi  |
| `availPlaces` | Contatore (risorse) | Sincronizzazione: il produttore aspetta i posti liberi |
| `mutex`       | Binario             | Mutua esclusione sulla struttura dati                  |

---
## Il deadlock in dettaglio

> [!abstract] Definizione
>Situazione in cui due o più azioni (processi) in competizione **aspettano che l'altra finisca**, quindi **nessuna finisce mai**.

### Le condizioni di Coffman

> [!important] Un deadlock **può verificarsi solo se tutte e quattro** le condizioni sono vere insieme:
>1. **Mutua esclusione** $\rightarrow$ Una risorsa non può essere usata da più di un processo alla volta
>2. **Hold and wait** $\rightarrow$ Un processo che già tiene delle risorse può **chiederne altre
>3. **No preemption** $\rightarrow$ Una risorsa **non può essere tolta** a chi la tiene; la rilascia solo lui
>4. **Attesa circolare** $\rightarrow$ Esiste una **catena circolare** di processi, ciascuno in attesa di una risorsa tenuta dal successivo

> [!hint] Per **evitare** un deadlock basta garantire che **una sola** di queste condizioni sia sempre falsa. 
### Rilevazione e recupero

Alcuni sistemi **non evitano** i deadlock ma li **rilevano e li risolvono**:

- **Database:** quando si rileva un deadlock si costruisce il **grafo delle attese** ("chi aspetta chi"), si cercano i **cicli**; se si trova un ciclo si sceglie una **vittima** e se ne **annulla** la transazione.
	
- **JVM:** Se i thread vanno in deadlock al massimo si arresta l'applicazione e si fa partire la diagnosi post-mortem. 

---
## Il problema dei filosofi a cena

![[Pasted image 20261104143800.png|387]]![[Pasted image 20261104143558.png|385]]

Si devono progettare i protocolli perché valgano:
- un filosofo mangia **solo se ha due bacchette**;
- **mutua esclusione**: nessuna forchetta è in mano a due filosofi contemporaneamente;
- **assenza di deadlock**;
- **assenza di starvation**;
- **comportamento efficiente in assenza di contesa** (se i vicini non mangiano, non devo aspettare).
### Soluzione

> [!hint] Se tutti prendono contemporaneamente la forchetta alla loro sinistra si va in deadlock, basta quindi **rompere la simmetria** e fare in modo che l'ultimo prenda prima quella alla sua destra.

![[Pasted image 20261104151018.png|396]]
### Regola generale per evitare il deadlock

Quando $N$ processi condividono e acquisiscono **più lock**:

1. assegna un **ordine totale** ai lock;
2. tutti i processi dovranno acquisire i lock **sempre in quell'ordine**.

Funziona perché rende **impossibile la dipendenza circolare** ("wait-for"), che è una condizione necessaria del deadlock.

---

## Il problema dei lettori e scrittori

### Il problema

È simile alla mutua esclusione (più processi competono per accedere a una sezione critica), ma i processi sono divisi in **due classi**:

1. **LETTORI** $\rightarrow$ possono leggere la risorsa anche in più di uno, ma mentre leggono nessuno può scrivere
2. **SCRITTORI** $\rightarrow$ possono scrivere uno alla volta, mentre uno scrive nessun altro può leggere o scrivere.
### Invarianti richiesti
![[Pasted image 20261104151839.png]]
**Lettura:** non ci sono mai due scrittori attivi insieme; e **lettori e scrittori non sono mai attivi insieme**.
### Soluzione con lettori concorrenti

> [!hint] Idea
> **Solo il primo lettore** blocca gli scrittori; **l'ultimo lettore** li sblocca. 
> I lettori in mezzo entrano senza fare `wait(rw)`.

![[Pasted image 20261104152755.png]]

**Come funziona.**
- `mutexR` evita che due lettori modifichino `nr` insieme (race condition sul contatore).
- `rw` è il lock che regola che ci sia o uno scrittore, o uno o più lettori.
- Il **primo lettore** (`nr == 0`) fa `wait(rw)`: se c'è uno scrittore, si blocca; altrimenti prende `rw` **a nome di tutti i lettori**.
- I lettori successivi **non** toccano `rw`: incrementano `nr` ed entrano.
- L'**ultimo lettore** che esce (`nr` torna a 0) fa `signal(rw)` e permette agli scrittori di entrare.

> [!warning] Priorità ai lettori 
>  Se i lettori arrivano continuamente, gli scrittori sono in **starvation**. 
>
>  Esistono varianti che danno priorità agli scrittori o che garantiscono equità (ad esempio con una coda comune), a costo di una maggiore complessità.

---

## Il problema dei fumatori di sigarette

### Il problema

> [!quote] Enunciato
> Quattro persone: 3 **fumatori** e 1 **agente** (arbitro). 
> Per fare e fumare una sigaretta servono **tre ingredienti**: carta, tabacco, fiammiferi. Ogni fumatore ha una **scorta infinita** di **uno** solo degli ingredienti (uno la carta, uno il tabacco, uno i fiammiferi); l'agente ha scorte infinite di **tutti e tre**.

Il ciclo è questo:
1. l'agente mette sul tavolo **due ingredienti** (a caso);
2. il fumatore che possiede **l'ingrediente mancante** li prende, si fa la sigaretta, la fuma e **avvisa l'agente**;
3. l'agente ne mette altri due, e così via.

Il compito è **scrivere un programma che sincronizzi l'agente e i fumatori**.

### L'argomento di Patil

Patil sosteneva che le primitive a semaforo di Dijkstra fossero **limitate**: usava questo problema per dire che **non si può risolvere con i semafori**. Ma poneva **vincoli molto forti**:

- la soluzione **non può usare istruzioni condizionali** né **array di semafori**.
- il codice dell'agente è **fisso e non modificabile**:
![[Pasted image 20261104155411.png]]
Con questi due vincoli la soluzione con semafori è **impossibile**.

> [!tip] Perché è difficile? 
> L'ostacolo è che il semaforo `S[tabacco]` può essere atteso da **più fumatori** (quello con la carta e quello con i fiammiferi), non avendo istruzioni condizionali per decidere di chi è il turno. Se l'agente mette tabacco e carta, il fumatore giusto è quello con i fiammiferi, ma **l'altro fumatore potrebbe "rubare" un ingrediente** (ad esempio il tabacco) e il fumatore giusto resta con la carta soltanto: **deadlock**. 

---

## Monitor

> [!fail] Ogni processo deve ricordarsi di fare `wait` e `signal` nel punto giusto e nell’ordine giusto, basta una `wait` dimenticata per mandare tutto in vacca.

> [!info] Definizione 
> Un **monitor** è un'**astrazione dati per la programmazione concorrente** che **incapsula** la **politica di sincronizzazione e mutua esclusione** per accedervi: 
> 
> **stato + operazioni + politica di concorrenza**
> > [!quote] Invece di dire ai processi come proteggere un dato, si mette il dato dentro una “scatola” che si protegge da sola.

> [!example] **Generalizzazioni** utili per capirlo
> **Del kernel dei sistemi operativi.** Nei SO le sezioni critiche (ad esempio l'allocazione di memoria) sono **centralizzate** in un programma privilegiato, il **kernel**: i programmi applicativi **richiedono servizi** al kernel, che gira in una modalità hardware tale da non poter essere disturbato. I monitor sono **versioni decentralizzate** del kernel monolitico.

### Struttura di un monitor
![[Pasted image 20261104165301.png|394]]
- **Variabili permanenti:** lo stato. “Permanenti” perché durano per tutta la vita del monitor, non solo durante una chiamata.
- **Inizializzazione:** il codice che prepara lo stato iniziale. Viene eseguito **prima** che qualsiasi procedura sia chiamata.
- **Procedure (o _entries_):** le operazioni che i processi possono invocare.

### Proprietà

Il monitor è un’**istanza di un tipo di dato astratto**:

- **Dall’esterno si vedono solo i nomi delle procedure, e rappresentano l’_interfaccia_. 
- Le chiamate sono fatte tramite `call MonitorName.OpName(params)`, o semplicemente `MonitorName.OpName(params)`.
- **Il codice dentro il monitor non può toccare variabili dichiarate fuori.**

> [!important] La **mutua esclusione** è **intrinseca**, non la scrive il programmatore, al più un processo può accedere al monitor, se lo trova occupato viene inserito in coda.

> [!fail] Non c'è una **coda esplicita** associata all'ingresso del monitor: il rischio di **starvation** esiste (nulla garantisce l'ordine con cui entrano i processi in attesa).

### Sincronizzazione: le variabili condizione

La mutua esclusione implicita non basta: spesso un processo deve **aspettare che lo stato del monitor soddisfi una condizione** ("il buffer non è pieno"). Per questo i monitor offrono un **supporto esplicito di sincronizzazione** tramite le **variabili condizione** (_condition variables_):

- usate **dentro il monitor** dal programmatore per **ritardare** un processo che non può proseguire in sicurezza finché lo stato non soddisfa una **condizione booleana**;
- usate anche per **risvegliare** un processo in attesa quando la condizione diventa vera.

Sono **tipi di dato primitivi** per sospendere (`wait`) e riprendere (`signal`) processi dentro un monitor, rappresentando **condizioni (eventi) sullo stato del monitor** che aspettano di essere soddisfatte e che lo diventano.

**Due operazioni atomiche**, `waitC` e `signalC` (talvolta scritte semplicemente `wait` e `signal`). **Ogni variabile condizione ha una coda FIFO** di processi bloccati.
![[Pasted image 20261109155833.png]]![[Pasted image 20261109155810.png|410]]

> [!warning] `waitC` rilascia il lock del monitor È **essenziale**: se un processo che fa `waitC` **continuasse a tenere** il lock del monitor mentre dorme, nessun altro potrebbe entrare nel monitor per rendere vera la condizione (e quindi svegliarlo). Sarebbe un deadlock. Per questo c'è un **legame esplicito** tra la variabile condizione e il monitor che la contiene: la `waitC` **rilascia automaticamente** il monitor.

### Esempio: cella sincronizzata

Una cella che contiene un valore: chi legge (`get`) **aspetta** che qualcuno abbia scritto (`set`).

![[Pasted image 20261109155904.png|236]]

- `set` imposta il valore, segna `available` e **risveglia** un lettore in attesa.
- `get`, se il valore non c'è ancora, **si sospende** sulla condizione `isAvail` (rilasciando il monitor) e, una volta risvegliato, restituisce il valore.

**Differenza con i semafori:** il `signalC` **non ricorda** il segnale. Se nessuno sta aspettando, **non ha effetto**. Per questo serve la variabile `available`, che **tiene memoria** del fatto che il valore è stato scritto (così un `get` che arriva _dopo_ il `set` non si blocca).

### Altre primitive

|Primitiva|Cosa fa|
|---|---|
|`emptyC(cond)`|Controlla se la coda della condizione è **vuota**|
|`signalAllC(cond)`|Come `signalC`, ma **risveglia tutti** i processi in attesa|
|`waitC(cond, rank)`|Si accoda **in ordine crescente di `rank`** (coda con priorità)|
|`minrank(cond)`|Restituisce il `rank` del processo in **testa** alla coda|

### Cella sincronizzata, varianti

**Con `signalAllC`:** il `set` risveglia **tutti** i lettori in attesa.

```text
procedure set(int v) {
    value := v
    available := true
    signalAllC(isAvail)
}
```

**Senza `signalAllC` (segnalazione a catena):** si ottiene lo stesso effetto facendo **ri-segnalare** ogni processo che si è appena svegliato.

```text
procedure get(): int {
    if (!available) {
        waitC(isAvail)
        signalC(isAvail)     // passo il "testimone" al prossimo in attesa
    }
    return value
}
```

Il `set` ne sveglia **uno**; quello, appena sveglio, ne sveglia un altro, e così via: tutti i lettori in attesa proseguono.

### I semafori realizzati con i monitor

Si può realizzare un semaforo con un monitor in due modi:

**Versione 1**

```text
monitor Semaphore {
    integer s := <InitValue>
    cond notZero

    procedure wait() {
        if s = 0
            waitC(notZero)
        s := s - 1
    }
    procedure signal() {
        s := s + 1
        signalC(notZero)
    }
}
```

**Versione 2**

```text
monitor Semaphore {
    integer s := <InitValue>
    cond notZero

    procedure wait() {
        if s = 0
            waitC(notZero)
        else
            s := s - 1
    }
    procedure signal() {
        if emptyC(notZero)
            s := s + 1
        else
            signalC(notZero)
    }
}
```

> [!tip] La differenza
> 
> - **Versione 2** è **fedele alla definizione di semaforo**: se qualcuno è in attesa, `signal` **non incrementa** `s` ma **consegna direttamente** il permesso al processo sbloccato (che dopo la `waitC` non decrementa più).
> - **Versione 1** incrementa sempre `s` e fa decrementare al processo risvegliato. Con una disciplina di segnalazione in cui un _terzo_ processo può inserirsi tra il `signalC` e la ripresa del risvegliato, quest'ultimo potrebbe trovare `s = 0` e **sbagliare** (qui si assume che il risvegliato riprenda subito).

### Semafori e variabili condizione a confronto

|Semaforo|Variabile condizione (in un monitor)|
|---|---|
|`wait` **può bloccare o no** (dipende dal valore)|`waitC` **blocca sempre**|
|`signal` **ha sempre effetto** (incrementa o sblocca)|`signalC` **non ha effetto** se la coda è vuota|
|`signal` sblocca un processo **arbitrario** (semaforo debole)|`signalC` sblocca il processo **in testa alla coda** (FIFO)|
|Il processo sbloccato può **riprendere subito**|A seconda della **semantica di segnalazione**, il processo sbloccato deve **aspettare** che il segnalatore lasci il monitor|

---

## La disciplina di segnalazione

### Il problema

Quando un processo esegue `signalC`, ci possono essere **due processi che potrebbero proseguire**: il **segnalatore** (che è ancora dentro il monitor) e il **processo risvegliato**. Ma per la semantica del monitor **uno solo** può essere attivo. Quindi bisogna **scegliere** chi continua: o il segnalatore, o il risvegliato, **non entrambi**.

### Le tre semantiche

|Disciplina|Chi prosegue|Caratteristiche|
|---|---|---|
|**Signal and Continue**|Il **segnalatore** continua; il risvegliato eseguirà **più tardi**|**Non preemptive**|
|**Signal and Wait**|Il **risvegliato** esegue **subito**; il segnalatore **aspetta**, e poi compete con gli altri per rientrare|**Preemptive**|
|**Signal and Urgent Wait** (o _Immediate Resumption Requirement_)|Come Signal and Wait, ma il **segnalatore ha priorità** sugli altri processi in attesa di entrare|È la **soluzione classica** per i monitor|

### Precedenze

Siano:

- $S$ = precedenza dei processi **segnalatori**;
- $W$ = precedenza dei processi **in attesa sulla condizione**;
- $E$ = precedenza dei processi **bloccati all'ingresso** del monitor.

|Disciplina|Precedenze|
|---|---|
|Signal and Continue|$E < W < S$|
|Signal and Wait|$E = S < W$|
|Signal and Urgent Wait|$E < S < W$|

### Conseguenza pratica: `if` o `while`

Il modo di scrivere l'attesa dipende dalla disciplina:

```text
// Signal and Continue → serve while
while (!B)
    waitC(cond)
<accesso>

// Signal and Wait / Urgent Wait → basta if
if (!B)
    waitC(cond)
<accesso>
```

**Perché.**

- Con **Signal and Continue** il risvegliato riprende **dopo**: nel frattempo un altro processo potrebbe aver **cambiato lo stato** e **riportato la condizione a falsa**. Quindi dopo il risveglio bisogna **ricontrollare** la condizione: da qui il `while`.
- Con **Signal and (Urgent) Wait** il risvegliato riprende **subito**, senza che nessuno si inserisca nel mezzo: la condizione è ancora vera, quindi basta un `if`.

> [!warning] Java usa Signal and Continue In Java (`wait`/`notify`/`notifyAll`) la semantica è **Signal and Continue**, quindi si scrive **sempre** `while (!condizione) wait();`. In più possono esistere **risvegli spuri** (il thread si sveglia anche senza una `notify`): un altro motivo per usare il `while`.

---

## Usare i monitor

I monitor servono per realizzare **qualsiasi risorsa o struttura dati** usata in concorrenza da più processi e di cui si vuole **incapsulare la politica di sincronizzazione**.

### Produttori-consumatori: il buffer limitato come monitor

```text
monitor BoundedBuffer {
    ElemType buffer := <EmptyBuffer>
    cond notFull, notEmpty

    procedure put(ElemType elem) {
        if (buffer is full)
            waitC(notFull)
        append(buffer, elem)
        signalC(notEmpty)
    }

    procedure take(): ElemType {
        if (buffer is empty)
            waitC(notEmpty)
        ElemType el := head(buffer)
        signalC(notFull)
        return el
    }
}

Producer                              Consumer
loop                                  loop
  p1: ElemType el := produce            q1: ElemType el := BoundedBuffer.take()
  p2: BoundedBuffer.put(el)             q2: consume(el)
```

**Come leggerlo.**

- Ci sono **due condizioni**: `notFull` (c'è posto) e `notEmpty` (c'è almeno un elemento).
- `put` aspetta se pieno, inserisce e **segnala** che non è più vuoto.
- `take` aspetta se vuoto, preleva e **segnala** che non è più pieno.
- **La mutua esclusione sul buffer è implicita**, non serve nessun `mutex`: ecco il vantaggio rispetto alla versione con semafori.

**Con un array circolare:**

```text
monitor BoundedBuffer {
    int[] elems := new int[MAX_ELEMS]
    int first := 0, last := 0
    cond notFull, notEmpty

    procedure put(int elem) {
        if ((last + 1) % MAX_ELEMS) = first
            waitC(notFull)
        elems[last] = elem
        last := (last + 1) % MAX_ELEMS
        signalC(notEmpty)
    }

    procedure take(): int {
        if (first = last)
            waitC(notEmpty)
        int elem = elems[first]
        first = (first + 1) % MAX_ELEMS
        signalC(notFull)
        return elem
    }
}
```

- `first` punta al prossimo elemento da prelevare, `last` alla prossima posizione libera.
- Il buffer è **vuoto** quando `first = last`; **pieno** quando `(last + 1) % MAX = first`.

> [!note] Capacità reale Con questa convenzione si lascia sempre **una cella libera** (per distinguere "pieno" da "vuoto"): la capacità utile è `MAX_ELEMS - 1`.

> [!warning] `if` o `while`? Il codice usa `if`, quindi vale con **Signal and Urgent Wait**. In un linguaggio con **Signal and Continue** (come Java) occorre `while`.

### Lettori-scrittori come monitor

**Versione con `while`** (corretta con qualsiasi disciplina di segnalazione):

```text
monitor RWLock {
    int nr, nw = 0
    cond okToRead, okToWrite

    procedure request_read() {
        while (nw > 0)
            waitC(okToRead)
        nr := nr + 1
    }
    procedure release_read() {
        nr := nr - 1
        if (nr = 0)
            signalC(okToWrite)
    }
    procedure request_write() {
        while (nr > 0 or nw > 0)
            waitC(okToWrite)
        nw := nw + 1
    }
    procedure release_write() {
        nw := nw - 1
        signalC(okToWrite)
        signalAllC(okToRead)
    }
}
```

**Invariante:** `(nr == 0 or nw == 0) and (nw <= 1)`.

**Come funziona:**

- un lettore può entrare se **non ci sono scrittori attivi**;
- uno scrittore può entrare se **non ci sono né lettori né scrittori attivi**;
- all'uscita, l'ultimo lettore sveglia uno scrittore; uno scrittore che esce sveglia **sia** un altro scrittore **sia** tutti i lettori (entrerà chi trova per primo la condizione soddisfatta, grazie al `while`).

**Versione con `if` e segnalazione a catena** (che usa i processi lettori e scrittori con `startRead/endRead`, `startWrite/endWrite`):

```text
monitor RWLock {
    integer readers := 0
    integer writers := 0
    cond okToRead, okToWrite

    procedure startRead() {
        if writers != 0
            waitC(okToRead)
        readers := readers + 1
        signalC(okToRead)             // a catena: sveglio il lettore successivo
    }
    procedure endRead() {
        readers := readers - 1
        if readers = 0
            signalC(okToWrite)
    }
    procedure startWrite() {
        if writers != 0 or readers != 0
            waitC(okToWrite)
        writers := writers + 1
    }
    procedure endWrite() {
        writers := writers - 1
        if !emptyC(okToRead)
            then signalAllC(okToRead)
            else signalC(okToWrite)
    }
}

Reader                         Writer
p1: RWLock.startRead           q1: RWLock.startWrite
p2: read the dbase             q2: write the dbase
p3: RWLock.endRead             q3: RWLock.endWrite
```

Qui **`endWrite` dà la precedenza ai lettori in attesa** (se ce ne sono li risveglia tutti, altrimenti sveglia uno scrittore). Ogni lettore appena entrato risveglia il successivo (`signalC(okToRead)` in `startRead`).

> [!tip] Rispetto ai semafori Il monitor rende **esplicita e leggibile** la politica (chi ha la priorità, quando si sveglia chi), mentre con i semafori andava dedotta da un intreccio di `wait` e `signal`. Cambiare politica (ad esempio dare la precedenza agli scrittori) significa modificare **solo il monitor**, non i processi che lo usano.

### Allocatore di risorse

I monitor funzionano tipicamente da **allocatori di risorse**: impongono una **politica** per assegnare risorse ai processi che le richiedono.

```text
monitor ResourceAllocator {

    procedure request(<Params>) {
        < block the request until the resource or a resource
          is available, according to some policy >
    }

    procedure release(<Params>) {
        < possibly unblock some pending request >
    }
}
```

---

## Realizzare un monitor con i semafori

Un monitor si può costruire a partire dai semafori:

- **un semaforo `mutex`** per la mutua esclusione;
- per **ogni variabile condizione**, un semaforo `condsem` e un contatore `condcount` che tiene il conto dei processi sospesi sulla variabile.

### Signal and Continue

```text
Prologue di ogni operazione:    wait(mutex)
Epilogue di ogni operazione:    signal(mutex)

waitC(cond) =                   signalC(cond) =
    condcount++                     if (condcount > 0) {
    signal(mutex)                       condcount--
    wait(condsem)                       signal(condsem)
    wait(mutex)                     }
```

**Lettura.**

- Ogni procedura inizia prendendo il `mutex` e finisce rilasciandolo (**mutua esclusione implicita**).
- `waitC`: si conta come sospeso, **rilascia il mutex** (così gli altri possono entrare), si **blocca** sul `condsem`, e una volta risvegliato **riprende il mutex** (deve rientrare nel monitor come un processo qualsiasi: è la semantica Signal and Continue).
- `signalC`: se c'è qualcuno sospeso, ne scala il conto e **sblocca** un processo da `condsem`; il segnalatore **continua** (non cede il monitor).

### Signal and Wait

```text
waitC(cond) =                   signalC(cond) =
    condcount++                     if (condcount > 0) {
    signal(mutex)                       condcount--
    wait(condsem)                       signal(condsem)
                                        wait(mutex)
                                    }
```

**Differenza.**

- Il processo risvegliato **non** fa `wait(mutex)`: **riceve direttamente il possesso del monitor** dal segnalatore (il mutex viene "ereditato").
- Il **segnalatore**, dopo aver sbloccato il risvegliato, fa `wait(mutex)` e **si mette in attesa** di rientrare: cede il monitor al risvegliato.

> [!tip] Perché non c'è il problema di un `signal` arrivato "troppo presto" In `waitC` c'è un piccolo intervallo tra `signal(mutex)` e `wait(condsem)`: un `signalC` potrebbe arrivare proprio lì. Non è un problema, perché il **semaforo ricorda** il `signal`: quando il processo arriva a `wait(condsem)` lo trova già a 1 e prosegue senza bloccarsi. È un'applicazione concreta di "il semaforo conta e ricorda".

---
## I monitor in Java (aggiunta utile)

Java incorpora i monitor in modo **parziale**: ogni oggetto ha un lock intrinseco e **una sola coda di attesa**.

|Concetto|In Java|
|---|---|
|Mutua esclusione implicita|`synchronized` su metodo o blocco|
|`waitC`|`wait()` (rilascia il lock dell'oggetto)|
|`signalC`|`notify()`|
|`signalAllC`|`notifyAll()`|
|Disciplina|**Signal and Continue** → sempre `while`|
|Più variabili condizione|`ReentrantLock` + `Condition` (`await`, `signal`, `signalAll`)|
|Semafori|`java.util.concurrent.Semaphore` (`acquire`, `release`)|

**Buffer limitato in Java:**

```java
class BoundedBuffer<T> {
    private final java.util.LinkedList<T> items = new java.util.LinkedList<>();
    private final int capacity;

    BoundedBuffer(int capacity) { this.capacity = capacity; }

    public synchronized void put(T elem) throws InterruptedException {
        while (items.size() == capacity)   // while, non if
            wait();
        items.addLast(elem);
        notifyAll();                       // sveglia chi aspetta (consumatori)
    }

    public synchronized T take() throws InterruptedException {
        while (items.isEmpty())
            wait();
        T el = items.removeFirst();
        notifyAll();                       // sveglia chi aspetta (produttori)
        return el;
    }
}
```

> [!note] Perché `notifyAll` Con una **sola coda di attesa per oggetto**, produttori e consumatori aspettano sulla stessa coda: `notify()` potrebbe svegliare quello sbagliato (ad esempio un altro produttore quando il buffer è pieno) e il segnale andrebbe sprecato. `notifyAll()` sveglia tutti: ciascuno ricontrolla la condizione nel `while`. Con `ReentrantLock` e due `Condition` (`notFull`, `notEmpty`) si può essere più precisi.

---

## Semafori e monitor a confronto

||**Semafori**|**Monitor**|
|---|---|---|
|Livello|Primitiva di basso livello|Astrazione di alto livello|
|Mutua esclusione|**Esplicita** (`wait`/`signal` attorno alla CS)|**Implicita** (ogni procedura è in mutua esclusione)|
|Sincronizzazione|Esplicita, con semafori ad evento o contatori|Variabili condizione|
|Dove sta la logica|**Sparsa** nel codice dei processi|**Incapsulata** nel monitor, insieme ai dati|
|Memoria dei segnali|Il semaforo **ricorda** (valore)|La condizione **non ricorda** (serve una variabile di stato)|
|Rischio di errori|Alto (ordine delle `wait`, `signal` dimenticate)|Più basso, ma restano la scelta `if`/`while` e la disciplina|
|Modulo riutilizzabile|Difficile|Naturale (è un oggetto)|

---
