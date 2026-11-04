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
>- altrimenti, se `S.V = 0`: il processo viene **bloccato** sul semaforo (aggiunto nella lista `S.L`).

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

**Proprietà chiave:** con un semaforo **forte** la **starvation è impossibile**, per qualsiasi numero $N$ di processi (chi è in coda prima o poi arriva in testa).

#### Semafori ad attesa attiva (busy-wait)

Semafori **senza** `S.L`: non c'è coda di bloccati, i processi aspettano controllando in continuazione.

```text
wait(S) =                          signal(S) =
< await (S.V > 0)                  < S.V ← S.V + 1 >
  S.V ← S.V - 1 >
```

Le operazioni sono comunque **atomiche** (nessun intreccio tra l'`await` e il decremento).

- ❌ **Si perde l'assenza di starvation:** con semafori busy-wait non si può assumere che un processo entri nella CS, nemmeno nella soluzione a 2 processi (nulla stabilisce chi "vince" quando il valore torna positivo).
- ✅ **Quando hanno senso:** in un sistema **multiprocessore** in cui il processo in attesa ha **un processore tutto per sé** e non spreca CPU che servirebbe ad altro; oppure con **poca contesa**, così l'attesa è breve.

### A cosa servono i semafori

Sono **mattoni di basso livello** con cui si risolve quasi ogni problema di interazione tra processi in architetture a memoria condivisa. Si usano per:

- **mutua esclusione**: problema della sezione critica, realizzazione di lock;
- **sincronizzazione**: segnalazione con semafori ad evento, barriere, ordinamento di azioni.

---

## Sezione critica con i semafori

### Due processi

Con un semaforo usato come **lock**, il problema diventa banale (rispetto agli algoritmi del modulo precedente):

```text
binary semaphore S ← (1, {})

P                              Q
loop forever                   loop forever
  p1: NCS                        q1: NCS
  p2: wait(S)                    q2: wait(S)
  p3: CS                         q3: CS
  p4: signal(S)                  q4: signal(S)
```

- `S = (1, {})`: il permesso è **uno solo**.
- Il pre-protocollo è `wait(S)` (prendi il permesso), il post-protocollo è `signal(S)` (restituiscilo).
- Chi arriva per secondo trova `S.V = 0` e **si blocca** finché il primo esce.

### Dimostrazione di correttezza

Come sempre, si costruisce il **diagramma di stato ridotto** (tolte CS e NCS):

```text
binary semaphore S ← (1, {})

p                        q
loop forever             loop forever
  p1: wait(S)              q1: wait(S)
  p2: signal(S)            q2: signal(S)
```

Qui, quando un processo è a `p2` (o `q2`) significa che è **nella CS**.

### Diagramma di stato con stati bloccati

Cambia qualcosa rispetto ai diagrammi precedenti: lo stato include **tutto il semaforo**, cioè la coppia `{S.V, S.L}`, e c'è un nuovo tipo di stato: quello in cui un processo è **bloccato**, indicato con `p1B` / `q1B` ("`p` è bloccato sulla `wait` di `p1`").

Stato = $\langle p, q, {S.V, S.L} \rangle$. Gli stati raggiungibili sono **5** (più simmetrie):

```text
                  <p1, q1, {1, {}}>
                   /               \
                p1/                 \q1
                 v                   v
      <p2, q1, {0, {}}>        <p1, q2, {0, {}}>
        |           \              /          |
       q1|         p2 \          / q2         |p1
        v             (torna a <p1,q1,{1,{}}>)  v
 <p2, q1B, {0, {Q}}>                <p1B, q2, {0, {P}}>
        |                                      |
       p2 (signal sblocca Q)             q2 (signal sblocca P)
        v                                      v
   <p1, q2, {0, {}}>                    <p2, q1, {0, {}}>
```

**Lettura passo per passo:**

1. Stato iniziale: entrambi pronti, `S.V = 1`.
2. `p` esegue `wait`: `S.V` scende a 0, `p` è nella CS (`p2`). Simmetricamente se parte `q`.
3. Da $\langle p2, q1, {0,{}} \rangle$ le strade sono due: `p` esce (`signal`), `S.V` torna a 1; oppure `q` tenta `wait` e **si blocca** → $\langle p2, q1B, {0,{Q}} \rangle$.
4. Da lì l'unica mossa è `p2` (`p` fa `signal`): poiché `Q` è in attesa, **non** si incrementa `S.V` ma si **sblocca `Q`**, che ora è nella CS → $\langle p1, q2, {0, {}} \rangle$.

**Verifica delle proprietà:**

|Proprietà|Esito|Come si vede nel diagramma|
|---|---|---|
|Mutua esclusione|✅|Non esiste lo stato $\langle p2, q2, _ \rangle$|
|Assenza di deadlock|✅|Da ogni stato esce almeno una freccia (chi è bloccato viene sbloccato da chi è in CS)|
|Assenza di starvation|✅ (con 2 processi)|Quando `Q` è bloccato e `P` esce, `Q` è l'**unico** candidato e viene sbloccato|

### N processi

Lo **stesso codice** funziona per $N$ processi:

```text
binary semaphore S ← (1, {})

Any process
loop forever
  p1: NCS
  p2: wait(S)
  p3: CS
  p4: signal(S)
```

La mutua esclusione e l'assenza di deadlock valgono ancora. Ma con $N \geq 3$ e un semaforo **debole**, **non** vale più l'assenza di starvation: `signal` sceglie un bloccato **arbitrario**, quindi un processo sfortunato può essere sempre scavalcato dagli altri.

> [!tip] Chiarimento Con 2 processi il semaforo debole basta perché c'è **un solo candidato** da sbloccare. Con 3 o più la scelta arbitraria apre alla starvation. Se invece il semaforo è **forte** (coda FIFO), la starvation è impossibile per qualsiasi $N$.

---

## Sincronizzazione con i semafori

I semafori non servono solo alla mutua esclusione: risolvono anche i **problemi di ordine di esecuzione** ("questa azione deve avvenire dopo quella"). Si usano i **semafori ad evento**: inizializzati a `(0, {})`, servono a **inviare/ricevere un segnale temporale**.

- Chi deve **aspettare** un evento fa `wait`: se l'evento non c'è ancora, si blocca.
- Chi **produce** l'evento fa `signal`.

### Esempio: merge sort concorrente

Si ordinano le due metà di un array in parallelo e poi si fondono. La fusione deve avvenire **solo dopo** che entrambe le metà sono ordinate.

```text
binary semaphore S1 ← (0, {})
binary semaphore S2 ← (0, {})
integer array A

sort1                      sort2                      merge
p1: sort 1st half of A     q1: sort 2nd half of A     r1: wait(S1)
p2: signal(S1)             q2: signal(S2)             r2: wait(S2)
                                                      r3: merge halves of A
```

**Come funziona:**

- `merge` aspetta due "eventi": prima `S1`, poi `S2`;
- `sort1` fa `signal(S1)` quando ha finito la sua metà, `sort2` fa `signal(S2)`;
- solo quando **entrambi** i segnali sono arrivati `merge` supera `r1` e `r2` e fonde.

> [!tip] Perché l'ordine non conta Se `sort2` finisce **prima** di `sort1`, `S2` passa a 1 (il segnale **viene ricordato**). `merge` bloccato su `S1` non lo vede ancora, ma quando `S1` arriva supera `r1` e poi trova `S2 = 1` e passa subito. È la proprietà "il semaforo conta e ricorda" già notata.

---

## Il problema produttore-consumatore

### Il problema

È l'esempio tipico di problema di **ordine di esecuzione** (e di comunicazione). Due tipi di processi:

- **produttori**: creano un dato (`produce`) e lo inviano;
- **consumatori**: ricevuto un dato, lo elaborano (`consume`).

È un **pattern onnipresente** in informatica:

|Produttore|Consumatore|
|---|---|
|Linea di comunicazione|Browser web|
|Browser web|Linea di comunicazione|
|Tastiera|Sistema operativo|
|Elaboratore di testi|Stampante|
|Programma di gioco|Schermo|

### Comunicazione sincrona o asincrona

Quando un dato passa da un processo all'altro, la comunicazione può essere:

- **sincrona**: non avviene finché **entrambi** (produttore e consumatore) sono pronti;
- **asincrona**: il **canale ha una capacità** di memorizzazione (un _buffer_), quindi produttore e consumatore non devono incontrarsi.

L'asincrona è molto utile:

- **disaccoppiamento temporale** tra i partecipanti;
- l'insieme dei processi può essere **dinamico**/aperto;
- si adatta a produttori e consumatori con **velocità diverse**.

Il costo: serve un **buffer**, cioè una **struttura dati condivisa con stato modificabile**, scritta dai produttori e letta dai consumatori.

### Buffer infinito

Con un buffer illimitato c'è **un'unica interazione** da sincronizzare: il consumatore **non deve prelevare da un buffer vuoto**. (Il produttore può sempre aggiungere.)

```text
UnboundedQueue<Item> buffer ← empty queue
semaphore availItems ← (0, {})

producer                           consumer
loop forever                       loop forever
  p1: Item el ← produce              q1: wait(availItems)
  p2: append(buffer, el)             q2: Item el ← take(buffer)
  p3: signal(availItems)             q3: consume(el)
```

**Come funziona.** `availItems` è un **semaforo di risorse** che **conta quanti elementi ci sono nel buffer**:

- il produttore, dopo aver inserito, fa `signal` (un elemento in più);
- il consumatore, prima di prelevare, fa `wait`: se il buffer è vuoto (`availItems = 0`) **si blocca** finché arriva un elemento.

**Invariante:** `availItems.V = #buffer` (valore del semaforo = numero di elementi nel buffer).

> [!note] Precisazioni
> 
> - L'invariante è **strettamente vera** solo se si considerano atomici i due gruppi `p2+p3` e `q1+q2`; tra una coppia di istruzioni il valore del semaforo e la lunghezza del buffer possono differire di uno.
> - In questo esempio le operazioni `append` e `take` sono **supposte atomiche**. Con più produttori/consumatori e una struttura non atomica serve un mutex (vedi sotto).

### Buffer limitato

Ora c'è anche una **seconda interazione**: il produttore **non deve aggiungere a un buffer pieno**. Servono quindi **due semafori**, uno per ogni "vincolo":

```text
BoundedQueue<Item> buffer ← empty queue
semaphore availItems  ← (0, {})     // elementi presenti
semaphore availPlaces ← (N, {})     // posti liberi

producer                           consumer
loop forever                       loop forever
  p1: Item el ← produce              q1: wait(availItems)
  p2: wait(availPlaces)              q2: Item el ← take(buffer)
  p3: append(buffer, el)             q3: signal(availPlaces)
  p4: signal(availItems)             q4: consume(el)
```

I due semafori sono detti **semafori divisi** (_split semaphores_): insieme rappresentano un'unica risorsa vista da due lati.

||`availItems`|`availPlaces`|
|---|---|---|
|Significato|Elementi nel buffer|Posti liberi nel buffer|
|Parte da|0|$N$|
|Blocca|Il **consumatore** (se vuoto)|Il **produttore** (se pieno)|
|Somma|`availItems.V + availPlaces.V = N` (a regime)||

### Più produttori e più consumatori

Se la struttura dati non è atomica (come un buffer qualsiasi), **due produttori** o **due consumatori** potrebbero manipolarla insieme e corromperla. Serve un **mutex** per la **mutua esclusione** sull'accesso al buffer, **oltre** ai semafori di sincronizzazione.

```text
BoundedQueue<Item> buffer ← empty queue
semaphore availItems  ← (0, {})
semaphore availPlaces ← (N, {})
binary semaphore mutex ← (1, {})

producer                           consumer
loop forever                       loop forever
  p1: Item el ← produce              q1: wait(availItems)
  p2: wait(availPlaces)              q2: wait(mutex)
  p3: wait(mutex)                    q3: Item el ← take(buffer)
  p4: append(buffer, el)             q4: signal(mutex)
  p5: signal(mutex)                  q5: signal(availPlaces)
  p6: signal(availItems)             q6: consume(el)
```

> [!warning] L'ordine delle `wait` è fondamentale Si fa **prima** la `wait` sul semaforo contatore (`availPlaces` / `availItems`) e **poi** quella sul mutex. Se si invertisse (prendere il mutex e poi aspettare), si creerebbe un **deadlock**: un consumatore che ha preso il mutex e aspetta un elemento in un buffer vuoto **blocca anche i produttori**, che non riescono a entrare per inserirne uno. Nessuno può sbloccare nessuno.
> 
> Regola pratica: **mai tenere un lock mentre si aspetta una condizione** che solo altri, entrando nella stessa sezione, possono rendere vera.

**Riassunto dei ruoli:**

|Semaforo|Tipo|Scopo|
|---|---|---|
|`availItems`|Contatore (risorse)|Sincronizzazione: il consumatore aspetta gli elementi|
|`availPlaces`|Contatore (risorse)|Sincronizzazione: il produttore aspetta i posti liberi|
|`mutex`|Binario|Mutua esclusione sulla struttura dati|

---

## Il problema dei filosofi a cena

### Origine e descrizione

Classico problema della programmazione concorrente. Nasce da una domanda d'esame di **Dijkstra nel 1971** su un problema di sincronizzazione in cui **cinque computer** si contendevano **cinque unità a nastro** condivise; fu poi raccontato come "filosofi a cena" da **Tony Hoare**. Oggi è un modo divertente e semplice-ma-impegnativo per **confrontare formalismi** di scrittura e dimostrazione di programmi concorrenti.

**La storia.**

- Una comunità isolata di **cinque filosofi** che fanno solo due cose: **pensare** e **mangiare**.
- Mangiano insieme a un tavolo con **5 piatti** e **5 forchette** (una tra ogni coppia di piatti).
- Al centro c'è una ciotola di spaghetti sempre piena.
- Gli spaghetti sono così intricati che per mangiare servono **due forchette**.
- Ogni filosofo può prendere la forchetta a **sinistra** e quella a **destra**, ma **una alla volta**.

### Cosa si chiede

Ogni filosofo esegue:

```text
Philosopher
loop forever
  p1: think
  p2: <pre-protocol>
  p3: eat
  p4: <post-protocol>
```

Si devono progettare i protocolli perché valgano:

- un filosofo mangia **solo se ha due forchette**;
- **mutua esclusione**: nessuna forchetta è in mano a due filosofi contemporaneamente;
- **assenza di deadlock**;
- **assenza di starvation**;
- **comportamento efficiente in assenza di contesa** (se i vicini non mangiano, non devo aspettare).

> [!tip] Numerazione Filosofo $i$ ($i = 0, \dots, 4$), forchette $0..4$. Il filosofo $i$ usa la forchetta $i$ (la sua **sinistra**) e la forchetta $(i+1) \bmod N$ (la sua **destra**). Quindi ogni forchetta è contesa da **due vicini**.

### Primo tentativo: una forchetta alla volta

Ogni forchetta è un **semaforo binario**: `wait` = prendere la forchetta, `signal` = posarla.

```text
semaphore array[0..4] fork ← [1,1,1,1,1]

loop forever
  p1: think
  p2: wait(fork[i])
  p3: wait(fork[(i+1) % N])
  p4: eat
  p5: signal(fork[i])
  p6: signal(fork[(i+1) % N])
```

- ✅ **Mutua esclusione:** nessuna forchetta è mai in mano a due filosofi (è garantita dal semaforo).
- ❌ **Va in deadlock.**

**Scenario di deadlock.** Tutti i filosofi, in un intreccio sfortunato, **prendono la forchetta di sinistra**: ora ognuno ha **una** forchetta e aspetta quella di destra, che è la sinistra del vicino e quindi è **già in mano a qualcuno che a sua volta aspetta**. Nessuno mangia mai e nessuno posa nulla.

```text
F0 ←─ Fil0 ─→ F1 ←─ Fil1 ─→ F2 ←─ Fil2 ─→ F3 ←─ Fil3 ─→ F4 ←─ Fil4 ─→ F0
  (ognuno tiene la sua sinistra e aspetta la destra, tenuta dal vicino)
```

---

## Il deadlock in dettaglio

### Definizione

Situazione in cui due o più azioni (processi) in competizione **aspettano che l'altra finisca**, quindi **nessuna finisce mai**.

### Le condizioni di Coffman (1971)

Un deadlock **può verificarsi solo se tutte e quattro** le condizioni sono vere insieme:

|Condizione|Significato|Nei filosofi|
|---|---|---|
|**Mutua esclusione**|Una risorsa non può essere usata da più di un processo alla volta|Una forchetta alla volta in una mano|
|**Hold and wait**|Un processo che già tiene delle risorse può **chiederne altre**|Tengo la sinistra e chiedo la destra|
|**No preemption**|Una risorsa **non può essere tolta** a chi la tiene; la rilascia solo lui|Nessuno può strappare la forchetta al vicino|
|**Attesa circolare**|Esiste una **catena circolare** di processi, ciascuno in attesa di una risorsa tenuta dal successivo|La catena Fil0 → Fil1 → ... → Fil4 → Fil0|

> [!tip] Come usarle Per **evitare** un deadlock basta garantire che **una sola** di queste condizioni sia sempre falsa. La più semplice da rompere, di solito, è l'**attesa circolare**.

### Deadlock con i lock

Succede quando **più thread aspettano per sempre** a causa di una **dipendenza ciclica** tra lock. Il caso più semplice: il thread A tiene il lock `L` e cerca di prendere `M`, mentre il thread B tiene `M` e cerca di prendere `L`. Entrambi aspettano per sempre: è il già citato **abbraccio mortale** (_deadly embrace_).

### Rilevazione e recupero

Alcuni sistemi **non evitano** i deadlock ma li **rilevano e li risolvono**:

- **Database:** sono progettati per rilevare e uscire dai deadlock. Le transazioni acquisiscono molti lock fino al commit, quindi non è raro che due si blocchino a vicenda. Si costruisce il **grafo delle attese** ("chi aspetta chi"), si cercano i **cicli**; se si trova un ciclo si sceglie una **vittima** e se ne **annulla** la transazione.
- **JVM:** **nessun meccanismo automatico** di rilevazione e recupero. Se i thread vanno in deadlock "è finita": al massimo si arresta l'applicazione. Esiste solo un supporto per la **diagnosi post-mortem** (ad esempio i _thread dump_, che indicano quali thread sono bloccati su quali lock).

### Filosofi: prima soluzione, i biglietti

Per garantire la vivacità si **limita il numero di filosofi** che possono competere insieme (cioè "entrare nella sala"), con **$N-1$ biglietti** per $N$ filosofi:

```text
semaphore array[0..4] fork ← [1,1,1,1,1]
semaphore ticket ← (4, {})

loop forever
  p1: think
  p2: wait(ticket)
  p3: wait(fork[i])
  p4: wait(fork[(i+1) % N])
  p5: eat
  p6: signal(fork[i])
  p7: signal(fork[(i+1) % N])
  p8: signal(ticket)
```

**Perché non c'è deadlock.** Al più **4 filosofi** sono "in gioco" e quindi almeno **uno è fuori**. Tra i quattro in gioco c'è sempre almeno un filosofo il cui **vicino di destra è quello fuori**: la forchetta tra i due è contesa solo da lui (il vicino di destra non la può avere, perché non ha il biglietto). Quel filosofo la prende, mangia, e poi rilascia tutto: la **catena circolare non può chiudersi**. Si rompe la condizione di attesa circolare.

> [!note] Starvation Per garantire anche l'assenza di starvation i semafori devono essere **forti** (FIFO): altrimenti un filosofo potrebbe essere scavalcato all'infinito.

### Filosofi: seconda soluzione, rompere la catena

Basta che **l'ultimo filosofo** prenda **prima la destra e poi la sinistra**. Questo **spezza la catena di attesa**: ora non possono più essere tutti in attesa della forchetta di destra.

Osservazione: dato l'**ordine totale** sugli identificatori delle forchette ($0 < 1 < \dots < 4$), l'ultimo filosofo (il 4, con forchette 4 e 0) prendeva le forchette **in ordine opposto** agli altri (prima la 4 e poi la 0). La soluzione consiste nel prendere **sempre le forchette nello stesso ordine**: prima quella con indice minore, poi quella con indice maggiore.

```text
semaphore array[0..4] fork ← [1,1,1,1,1]

integer first  = min(i, (i+1) % N)
integer second = max(i, (i+1) % N)

loop forever
  p1: think
  p2: wait(fork[first])
  p3: wait(fork[second])
  p4: eat
  p5: signal(fork[first])
  p6: signal(fork[second])
```

Per $i = 0..3$: `first = i`, `second = i+1`. Per $i = 4$: `first = 0`, `second = 4`.

**Perché funziona.** Ogni processo acquisisce le risorse in **ordine crescente di indice**: chi tiene la forchetta $k$ aspetta solo forchette con indice **maggiore** di $k$. Una **catena circolare** richiederebbe a un certo punto di aspettare un indice **minore** di quello tenuto, cosa che non accade mai: **è impossibile avere attesa circolare**.

### Regola generale per evitare il deadlock

Quando $N$ processi condividono e acquisiscono **più lock**:

1. assegna un **ordine totale** ai lock;
2. acquisisci i lock **sempre in quell'ordine**.

Funziona perché rende **impossibile la dipendenza circolare** ("wait-for"), che è una condizione necessaria del deadlock.

> [!example] In Java Se due metodi devono prendere i lock `a` e `b`, entrambi devono prenderli nello stesso ordine (ad esempio sempre `a` e poi `b`). Un metodo che prende `a` poi `b` e uno che prende `b` poi `a` possono andare in deadlock.

---

## Il problema dei lettori e scrittori

### Il problema

Proposto da **Courtois, Heymans e Parnas (1971)**. È simile alla mutua esclusione (più processi competono per accedere a una sezione critica), ma i processi sono divisi in **due classi**:

|Classe|Deve escludere|
|---|---|
|**Lettori** (_readers_)|Gli **scrittori**, ma **non** gli altri lettori|
|**Scrittori** (_writers_)|**Tutti**: sia i lettori sia gli altri scrittori|

È un'astrazione dell'**accesso a un database** (o a qualsiasi risorsa condivisa): non c'è pericolo se più processi **leggono** insieme; **scrivere/modificare** dev'essere fatto in **mutua esclusione** per mantenere i dati consistenti.

### Invarianti richiesti

Con $nR$ = numero di lettori attivi e $nW$ = numero di scrittori attivi:

$$nR \geq 0 \qquad nW = 0 ;\vee; nW = 1 \qquad (nR > 0 \rightarrow nW = 0) ;\wedge; (nW = 1 \rightarrow nR = 0)$$

**Lettura:** non ci sono mai due scrittori attivi insieme; e **lettori e scrittori non sono mai attivi insieme**.

### Prima soluzione: troppo restrittiva

Un solo semaforo come lock:

```text
binary semaphore rw ← (1, {})
DataBase dbase

reader                             writer
loop forever                       loop forever
  p1: wait(rw)                       q1: wait(rw)
  p2: Item el ← read(dbase)          q2: Item el ← create_record
  p3: signal(rw)                     q3: write(dbase, el)
                                     q4: signal(rw)
```

È **corretta** (rispetta gli invarianti) ma **troppo vincolata**: ogni lettore e ogni scrittore ha accesso esclusivo, quindi **anche i lettori vengono serializzati**, perdendo il parallelismo che si poteva avere.

### Soluzione con lettori concorrenti

Idea: **solo il primo lettore** blocca gli scrittori; **l'ultimo lettore** li sblocca. I lettori in mezzo entrano senza fare `wait(rw)`. Serve un contatore dei lettori, `nr`, e un mutex `mutexR` per proteggerlo (è una variabile condivisa tra i lettori).

```text
binary semaphore mutexR ← (1, {})
int nr ← 0
binary semaphore rw ← (1, {})
DataBase dbase

reader                             writer
loop forever                       loop forever
  p1:  wait(mutexR)                  q1: wait(rw)
  p2:  if (nr == 0)                  q2: Item el ← create_record
  p3:      wait(rw)                  q3: write(dbase, el)
  p4:  nr ← nr + 1                   q4: signal(rw)
  p5:  signal(mutexR)
  p6:  Item el ← read(dbase)
  p7:  wait(mutexR)
  p8:  nr ← nr - 1
  p9:  if (nr == 0)
  p10:     signal(rw)
  p11: signal(mutexR)
```

**Come funziona.**

- `rw` è il lock "stanza": o c'è uno scrittore, o ci sono lettori (uno o più).
- Il **primo lettore** (`nr == 0`) fa `wait(rw)`: se c'è uno scrittore, si blocca; altrimenti prende `rw` **a nome di tutti i lettori**.
- I lettori successivi **non** toccano `rw`: incrementano `nr` ed entrano.
- L'**ultimo lettore** che esce (`nr` torna a 0) fa `signal(rw)` e permette agli scrittori di entrare.
- `mutexR` evita che due lettori modifichino `nr` insieme (race condition sul contatore).

> [!warning] Starvation degli scrittori Questa soluzione dà **priorità ai lettori**: finché c'è sempre almeno un lettore attivo, `nr` non scende mai a 0 e quindi **lo scrittore non entra mai**. Se i lettori arrivano continuamente, gli scrittori sono in **starvation**. Esistono varianti che danno priorità agli scrittori o che garantiscono equità (ad esempio con una coda comune), a costo di una maggiore complessità.

---

## Altri problemi classici

|Problema|Origine|
|---|---|
|**Barbiere** (_Barber Shop_, "barbiere addormentato")|Dijkstra|
|**Fumatori di sigarette** (_Cigarette Smokers_)|Patil, 1971|
|**Babbo Natale** (_The Santa Claus problem_)|Bansal, 2006|

> [!info] Babbo Natale in breve Babbo Natale dorme finché non lo sveglia il ritorno di **tutte le renne** oppure di un **gruppo di elfi** con un problema: serve sincronizzare gruppi con priorità diverse.

### I fumatori di sigarette

Proposto da **S. Patil nel 1971** per indagare i **limiti dei semafori**.

**Enunciato.** Quattro persone: 3 **fumatori** e 1 **agente** (arbitro). Per fare e fumare una sigaretta servono **tre ingredienti**: carta, tabacco, fiammiferi. Ogni fumatore ha una **scorta infinita** di **uno** solo degli ingredienti (uno la carta, uno il tabacco, uno i fiammiferi); l'agente ha scorte infinite di **tutti e tre**.

Il ciclo è questo:

1. l'agente mette sul tavolo **due ingredienti** (a caso);
2. il fumatore che possiede **l'ingrediente mancante** li prende, si fa la sigaretta, la fuma e **avvisa l'agente**;
3. l'agente ne mette altri due, e così via.

Il compito è **scrivere un programma che sincronizzi l'agente e i fumatori**.

### L'argomento di Patil

Patil sosteneva che le primitive a semaforo di Dijkstra fossero **limitate**: usava questo problema per dire che **non si può risolvere con i semafori**. Ma poneva **vincoli molto forti**:

- il codice dell'agente è **fisso e non modificabile**:

```text
shared S: array[1..3] of binary semaphores, initially all 0
       agent: binary semaphore, initially 1
local i, j: range over [1,2,3]

loop
  set i and j (at random) to two different values from [1,2,3]
  wait(agent)
  signal(S[i])
  signal(S[j])
end_loop
```

- la soluzione **non può usare istruzioni condizionali** né **array di semafori**.

Con questi due vincoli la soluzione con semafori è **impossibile**.

> [!tip] Perché è difficile (e come si aggira) L'ostacolo è che il semaforo `S[tabacco]` può essere atteso da **più fumatori** (quello con la carta e quello con i fiammiferi). Se l'agente mette tabacco e carta, il fumatore giusto è quello con i fiammiferi, ma **l'altro fumatore potrebbe "rubare" un ingrediente** (ad esempio il tabacco) e il fumatore giusto resta con la carta soltanto: **deadlock**. Non essendoci istruzioni condizionali per decidere "è il mio turno?" il problema sembra irrisolvibile. Se si **rimuovono i vincoli** (ad esempio si introducono dei processi ausiliari che guardano gli ingredienti e svegliano **solo** il fumatore giusto, o si usano condizionali) il problema **diventa risolvibile** con i semafori. La lezione: la limitatezza dipendeva dai vincoli, non dai semafori in sé.

---

## Oltre i semafori: i monitor

### Perché cambiare

I semafori sono potenti ma **di bassissimo livello**:

- i programmi sono **soggetti a errori** (una `wait` dimenticata, un `signal` di troppo, ordini sbagliati);
- sono **difficili da usare** in programmi concorrenti complessi (la logica di sincronizzazione è **sparsa** nel codice dei processi).

Si cercano quindi costrutti di **livello più alto**: i **monitor**, introdotti da **Brinch Hansen (1973)** e generalizzati da **Hoare (1974)**.

### Cos'è un monitor

> [!info] Definizione Un **monitor** è un'**astrazione dati per la programmazione concorrente** che **incapsula** la **politica di sincronizzazione e mutua esclusione** per accedervi: **stato + operazioni + politica di concorrenza**.

È come un **modulo** più **meccanismi di base per garantire la correttezza** quando il modulo è usato da più processi in concorrenza.

**Due generalizzazioni** utili per capirlo:

- **Del kernel dei sistemi operativi.** Nei SO le sezioni critiche (ad esempio l'allocazione di memoria) sono **centralizzate** in un programma privilegiato, il **kernel**: i programmi applicativi **richiedono servizi** al kernel, che gira in una modalità hardware tale da non poter essere disturbato. I monitor sono **versioni decentralizzate** del kernel monolitico.
- **Della nozione di oggetto** nella programmazione a oggetti: classi che incapsulano **dati + operazioni + politica di sincronizzazione/mutua esclusione**.

### Struttura di un monitor

```text
monitor MonitorName {

    declaration of permanent variables

    initialization statements

    procedures (or entries)

}
```

(La sintassi varia da linguaggio a linguaggio.)

**Proprietà:**

- Il monitor è un'**istanza di un tipo di dato astratto**: all'esterno sono visibili **solo i nomi delle procedure** (l'**interfaccia**). Sono le **uniche porte** attraverso il "muro" definito dalla dichiarazione del monitor.
- Le procedure si chiamano con `call MonitorName.OpName(params)` (spesso scritto semplicemente `MonitorName.OpName(params)`).
- Le istruzioni dentro il monitor **non possono accedere a variabili dichiarate fuori** dal monitor.
- Le **variabili permanenti** sono inizializzate **prima** che venga chiamata qualsiasi procedura.

### Mutua esclusione implicita

La mutua esclusione è **intrinseca**, non la scrive il programmatore:

- una procedura è **attiva** se qualche processo sta eseguendo un'istruzione al suo interno;
- **al più una** procedura del monitor può essere attiva alla volta (un solo processo "dentro" il monitor);
- i processi che trovano il monitor **occupato** vengono **sospesi**.

> [!example] Contatore thread-safe
> 
> ```text
> monitor Counter {
>     int count;
> 
>     procedure inc() {
>         count := count + 1
>     }
> 
>     procedure getValue(): int {
>         return count;
>     }
> }
> ```
> 
> L'incremento (non atomico a livello macchina, come visto) è **sicuro** perché due `inc()` non possono mai essere attive insieme. Nessun `wait`/`signal` da scrivere.

**Osservazioni:**

- Se le operazioni **dello stesso monitor** sono chiamate da più processi, l'implementazione garantisce che siano eseguite **in mutua esclusione**, cioè **atomicamente l'una rispetto all'altra**.
- Se invece si chiamano operazioni di **monitor diversi**, le esecuzioni **possono intrecciarsi**.
- Non c'è una **coda esplicita** associata all'ingresso del monitor: il rischio di **starvation** esiste (nulla garantisce l'ordine con cui entrano i processi in attesa).

### Sincronizzazione: le variabili condizione

La mutua esclusione implicita non basta: spesso un processo deve **aspettare che lo stato del monitor soddisfi una condizione** ("il buffer non è pieno"). Per questo i monitor offrono un **supporto esplicito di sincronizzazione** tramite le **variabili condizione** (_condition variables_):

- usate **dentro il monitor** dal programmatore per **ritardare** un processo che non può proseguire in sicurezza finché lo stato non soddisfa una **condizione booleana**;
- usate anche per **risvegliare** un processo in attesa quando la condizione diventa vera.

Sono **tipi di dato primitivi** per sospendere (`wait`) e riprendere (`signal`) processi dentro un monitor, rappresentando **condizioni (eventi) sullo stato del monitor** che aspettano di essere soddisfatte e che lo diventano.

**Due operazioni atomiche**, `waitC` e `signalC` (talvolta scritte semplicemente `wait` e `signal`). **Ogni variabile condizione ha una coda FIFO** di processi bloccati.

```text
waitC(cond) =
< append p to cond.queue
  p.state := blocked
  monitor.lock := release >

signalC(cond) =
< if cond.queue != empty
      q := remove head of cond.queue
      q.state := ready >
```

> [!warning] `waitC` rilascia il lock del monitor È **essenziale**: se un processo che fa `waitC` **continuasse a tenere** il lock del monitor mentre dorme, nessun altro potrebbe entrare nel monitor per rendere vera la condizione (e quindi svegliarlo). Sarebbe un deadlock. Per questo c'è un **legame esplicito** tra la variabile condizione e il monitor che la contiene: la `waitC` **rilascia automaticamente** il monitor.

### Esempio: cella sincronizzata

Una cella che contiene un valore: chi legge (`get`) **aspetta** che qualcuno abbia scritto (`set`).

```text
monitor SynchCell {
    int value;
    boolean available := false;
    cond isAvail;

    procedure set(int v) {
        value := v
        available := true
        signalC(isAvail)
    }

    procedure get(): int {
        if (!available)
            waitC(isAvail)
        return value
    }
}
```

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

## Costruire componenti riutilizzabili con i monitor

Con i monitor si realizzano **componenti di sincronizzazione/coordinamento riutilizzabili**:

|Componente|A cosa serve|
|---|---|
|**Latch**|Blocca i processi finché un certo numero di eventi si è verificato; poi li lascia passare (una volta sola)|
|**Barriera**|Punto in cui tutti i processi di un gruppo devono arrivare prima che qualcuno possa proseguire|
|**Rendez-vous**|Incontro tra due processi: nessuno prosegue finché entrambi sono arrivati|
|**Message box**|Scambio di messaggi tramite una casella|
|**Blackboard**|Lavagna condivisa dove i processi scrivono e leggono informazioni|
|**Servizi di eventi**|Notifica di eventi a chi si è registrato|

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
