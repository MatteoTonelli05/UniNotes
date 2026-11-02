> [!question] Cosa sono i modelli?
Un **modello** è una descrizione rigorosa della struttura e del comportamento di un programma (o sistema) a un **livello di astrazione adeguato**: include le informazioni rilevanti e **ignora** quelle che non lo sono.

Esistono due grandi famiglie di modelli, con scopi diversi:
1. **DIAGRAMMATICI:** utili a progettare e comunicare la struttura *(es. UML )*
2. **FORMALI:** ragionare in modo matematico sul comportamento *(es. FSP, LTL, PlusCal)*

> [!hint] I modelli servono quindi per:
>- **progettare**: descrivere struttura e comportamento _senza_ i dettagli di basso livello dell'implementazione concreta;
>- **verificare**: poter ragionare sul comportamento **dinamico**, cioè su _tutte_ le esecuzioni possibili.

> [!attention] Perché la verifica è particolarmente importante 
> Nei programmi concorrenti i test non bastano: il non-determinismo fa sì che un test possa passare mille volte e fallire alla millesima e una. Servono tecniche che ragionino su **tutti gli intrecci possibili**.

---
## Come si modella un programma concorrente

L'approccio di fondo è basato su 3 concetti fondamentali:
1. **ISTRUZIONI ATOMICHE**, ogni processo è una sequenza di istruzioni indivisibili
2. **ARBITRARY INTERLEAVING**, l'esecuzione concorrente comporta che, a ogni passo, _uno_ dei processi pronti esegue _una_ istruzione atomica
3. **SPEED INDEPENDENCE**, vale l'**indipendenza dalla velocità**


> [!abstract] Il paradigma di programmazione considerato per ora è il **multi-threading con memoria condivisa**: i processi interagiscono tramite variabili condivise.
### 1. Modellazione con FSP

Con FSP esiste **una sola astrazione**: il _processo_. Quindi si modella con processi **TUTTO**:

- l'esecuzione di un **programma sequenziale** → un processo;
- l'esecuzione di un **programma concorrente** → la **composizione parallela** di processi;
- le **variabili condivise** (memoria condivisa) → **processi**, che offrono azioni di lettura e scrittura con cui gli altri processi si sincronizzano.

![[Pasted image 20261102112128.png|286]]

> [!hint] Pensare a una variabile condivisa come a un processo 
> Una variabile è qualcosa che _sta in uno stato_ (il suo valore) e _accetta azioni_ (`read`, `write`) che ne cambiano lo stato. È un piccolo automa a stati finiti.

### 2. Modellazione con diagrammi di stato

L'esecuzione di un programma concorrente si rappresenta con **stati** e **transizioni**.
##### **Stato** 
È una tupla che contiene:
- per **ogni processo**, un elemento che è un'**etichetta** (la prossima istruzione da eseguire, cioè il suo "puntatore di controllo");
- per **ogni variabile** (globale o locale), un **valore** del tipo corrispondente.

##### **Transizione.** 
C'è una transizione da $s_1$ a $s_2$ se eseguire un'istruzione in $s_1$ porta in $s_2$. 

L'istruzione eseguita deve essere una di quelle puntate da un puntatore di controllo in $s_1$ (cioè la prossima istruzione di uno dei processi).

##### **Diagramma di stato.** 
È il grafo di **tutti gli stati raggiungibili** dallo stato iniziale.

Definiamo **scenario** (esecuzione possibile), un **cammino orientato** nel grafo a partire dallo stato iniziale.

> [!fail] Un **ciclo** nel grafo rappresenta la possibilità di una **computazione infinita** in un grafo finito.

> [!info] Convenzioni di lettura
> 
> - `end` (o `–`) al posto di un'etichetta significa che quel processo è **terminato**.
> - Uno stato senza transizioni uscenti è uno **stato finale** (nei diagrammi è il pallino nero).
> - Se due processi hanno entrambi un'istruzione pronta, dallo stato escono **due** transizioni: è qui che si vede il non-determinismo.

---

##sempi di diagrammi di stato

### Esempio con variabile condivisa

Due processi, ciascuno con una sola istruzione, una variabile condivisa `n` e variabili locali `k1`, `k2`:
![[Pasted image 20261102114624.png]]
- Ogni riga etichettata è **un'istruzione atomica**.
- Le variabili locali (`k1`, `k2`) sono **private** del processo; `n` è **condivisa**.
![[Pasted image 20261102114720.png|481]]
### Esempio con due processi che stampano
![[Pasted image 20261102114838.png]]
Qui non ci sono variabili: lo stato è $\langle p, q \rangle$, ovvero le azioni che posso fare. 
![[Pasted image 20261102114954.png]]
In ogni scenario `p1` precede sempre `p2` e `q1` precede sempre `q2`: **l'ordine interno di ciascun processo è sempre rispettato**, cambia solo come le due sequenze si "mescolano".

> [!hint] Se avessimo 2 processi con 3 azioni ciascuno
> Una sequenza di 6 passi si ottiene scegliendo **in quali 3 posizioni** (su 6) mettere le azioni di `p`; le altre 3 vanno a `q`. Quindi il numero è $\binom{6}{3} = 20$. Per $m$ azioni per processo: $\binom{2m}{m}$.

---
## L'esplosione degli scenari

### Formula generale

Con $n$ processi, dove il processo $i$ ha $m_i$ azioni **indipendenti** tra loro, il numero di scenari è:

$$ns = \frac{\left(\sum_{i=1}^{n} m_i\right)!}{\prod_{i=1}^{n} (m_i!)}$$

*(È il coefficiente multinomiale: gli ordinamenti di tutte le azioni, divisi per gli ordinamenti interni che non contano perché ciascun processo mantiene il proprio ordine.)*

> [!tip] Verificare una proprietà guardando gli **stati raggiungibili** e le **transizioni** è quindi molto più economico che enumerare i cammini.

---
## L'importanza di essere atomici

### Incremento atomico vs non atomico

![[Pasted image 20261102123120.png]]![[Pasted image 20261102123140.png]]
> [!fail] Solo **2 scenari su 6** danno il risultato corretto: negli altri 4 un incremento va **perso** (_lost update_). È una **race condition** nella sua forma più pura.

### Anche `n := n + 1` non è atomico a livello macchina

Anche se nel codice sorgente `n := n + 1` sembra una sola istruzione, il processore la esegue in più passi. A seconda dell'architettura:

**Macchina a stack**
```text
p1: push n       // metti n sullo stack
p2: push #1      // metti la costante 1
p3: add          // somma i due valori in cima
p4: pop n        // scrivi il risultato in n
```

**Macchina a registri**
```text
p1: load R1, n       // leggi n nel registro
p2: add R1, #1       // incrementa il registro
p3: store n, R1      // riscrivi in n
```

(Il processo `q` esegue le stesse istruzioni.) Quindi l'intreccio dei passi di basso livello produce gli stessi problemi del caso non atomico di sopra.

> [!warning] **Atomico** è solo ciò che l'hardware o il linguaggio _garantiscono_ essere indivisibile. 
> In Java, ad esempio, `contatore++` non è atomico; lo è invece `AtomicInteger.incrementAndGet()`.

---

## Strutture dati non atomiche

La nozione di "atomico" non vale solo per le istruzioni, ma anche per i **dati**.

### Oggetto dati atomico

Un oggetto dati è **atomico** se il numero dei suoi possibili stati coincide con il numero dei valori che può assumere, e le operazioni cambiano _atomicamente_ quello stato. Ad esempio, un intero a 32 bit.

- I **tipi primitivi** dei linguaggi concorrenti sono tipicamente atomici...
- ...ma **non sempre**: in Java, `double` e `long` (64 bit) **non** sono garantiti atomici in lettura/scrittura se non dichiarati `volatile`, perché potrebbero essere scritti in due metà da 32 bit.

### Tipi di dati astratti (ADT)

Gli oggetti composti da più dati più semplici (classi nei linguaggi a oggetti, `struct` in C) sono tipicamente **non atomici**.

Per questi si distinguono due tipi di stato:

|Stato|A chi interessa|Esempio (lista)|
|---|---|---|
|**Interno**|A chi **definisce** l'oggetto (chi scrive la classe)|Puntatori dei nodi, contatore `size`, ecc.|
|**Esterno**|A chi **usa** l'oggetto|"La lista contiene [a, b, c]"|

La corrispondenza tra stati interni ed esterni è **parziale**:

- esistono stati interni **senza** un corrispondente stato esterno;
- gli stati interni che hanno un corrispondente esterno si dicono **consistenti**.

> [!example] Esempio con una lista concatenata Inserire un elemento richiede più passi: creare il nodo, collegarlo alla lista, aggiornare `size`. Dopo aver collegato il nodo ma **prima** di aver aggiornato `size`, lo stato interno è **inconsistente** (la lista ha 4 nodi ma `size` dice 3). Non corrisponde a nessuna lista "vera" vista da fuori.

### Perché è un problema solo nella concorrenza

- **Programmazione sequenziale:** nessun problema, grazie all'_information hiding_. Chi usa la lista chiama un metodo e riprende il controllo solo a operazione finita: **non vede mai** gli stati intermedi inconsistenti.
- **Programmazione concorrente:** un altro processo può **accedere all'oggetto mentre è a metà operazione** e trovarlo in stato inconsistente.

> [!warning] Conclusione Servono meccanismi che garantiscano che i processi lavorino su oggetti dati **sempre in stati consistenti**: in pratica, rendere le operazioni sull'oggetto **atomiche** rispetto agli altri processi, cioè in **mutua esclusione** (con lock, `synchronized`, monitor...).

---

## Processi ciclici e cicli nel diagramma

Due processi che ciclano su una condizione, con `n` inizialmente a 1:

```text
integer n := 1
p:  p1: while (n < 1)           q:  q1: while (n >= 0)
    p2:     n := n + 1               q2:     n := n - 1
```

- `p1` è il **controllo** della condizione, `p2` è il **corpo** del ciclo (idem per `q1`, `q2`).
- Il diagramma di stato ha stati $\langle p, q, n \rangle$ e contiene **cicli**.

### Domande tipiche e relative risposte

**Un scenario in cui il ciclo di `p` viene eseguito esattamente una volta.** `q1, q2` (n diventa 0) → `p1` (0 < 1, vero) → `p2` (n torna 1) → `p1` (1 < 1, falso: `p` esce). Il corpo di `p` è stato eseguito una sola volta.

**Esattamente tre volte.** Si ripete per tre volte la coppia "`q` decrementa, poi `p` incrementa", e poi `p` controlla e trova la condizione falsa: `q1 q2 | p1 p2 | q1 q2 | p1 p2 | q1 q2 | p1 p2 | p1` (a questo punto `n = 1` e `p` esce). L'idea: perché `p` entri nel corpo serve `n < 1`, quindi `q` deve abbassare `n` tra un giro di `p` e il successivo.

**Entrambi i cicli infiniti.** Si alternano senza fine: `q1 q2 p1 p2 q1 q2 p1 p2 ...` `q` porta `n` da 1 a 0 (la sua condizione `n >= 0` resta vera), `p` lo riporta a 1 (la sua condizione `n < 1` era vera con `n = 0`). È un **ciclo** nel diagramma di stato.

> [!tip] Cosa insegna questo esempio Un grafo **finito** può rappresentare computazioni **infinite**: basta un ciclo. Per questo le proprietà di _vivacità_ (qualcosa di buono prima o poi accade) si studiano cercando **cicli** nel diagramma in cui il "qualcosa di buono" non avviene mai.

---

## Il problema della sezione critica

### Definizione

Introdotto da **Dijkstra** nel 1965, è uno dei problemi più importanti e più studiati della programmazione concorrente, nell'ambito della **competizione** tra processi.

Si hanno $N$ processi, ciascuno dei quali esegue **in un ciclo infinito** una sequenza di istruzioni divisibile in due parti:

- **sezione critica (CS, _critical section_)**: istruzioni che accedono a **oggetti condivisi**;
- **sezione non critica (NCS, _non-critical section_)**: tutto il resto.

Schema di ogni processo:

```text
P[i]:
loop forever
    <non-critical section>
    entry protocol  (pre-protocollo)
    <critical section>
    exit protocol   (post-protocollo)
```

Il compito è **progettare il pre- e il post-protocollo** in modo che valgano certe proprietà.

> [!info] A cosa serve il modello Il problema rappresenta un sistema che svolge calcoli complessi ma ogni tanto deve accedere a dati o risorse (ad esempio hardware) **condivisi** con altri processi. Esempio concreto: un chiosco di check-in in aeroporto che esegue molto lavoro locale ma, per registrare un passeggero, deve accedere a un **database centrale** condiviso con tutti gli altri chioschi.

### Le proprietà richieste

|Proprietà|Significato|
|---|---|
|**Mutua esclusione**|Le istruzioni delle sezioni critiche di due o più processi **non** devono essere intrecciate: al più un processo alla volta è in CS|
|**Assenza di deadlock**|Se alcuni processi cercano di entrare nella loro CS, **almeno uno** ci riesce prima o poi|
|**Assenza di starvation individuale**|Se un **qualsiasi** processo cerca di entrare nella CS, **prima o poi ci riesce** (_bounded waiting_: nella forma più forte, c'è un limite al numero di volte in cui gli altri possono passargli avanti)|
|**Progresso della CS**|Una volta che un processo inizia la sua CS, la **deve finire**|

> [!tip] Differenza tra le due proprietà di vivacità
> 
> - **Assenza di deadlock** è una garanzia a livello di **sistema**: "se qualcuno vuole entrare, qualcuno entrerà". Ma potrebbe sempre entrare lo stesso processo, lasciando un altro fuori per sempre.
> - **Assenza di starvation** è una garanzia a livello di **singolo processo**: "chi vuole entrare, prima o poi entra".
> - La seconda è più forte: se vale l'assenza di starvation vale anche l'assenza di deadlock, non viceversa.

**Un'asimmetria importante: la NCS non deve progredire.** Si assume che la **CS termini sempre** (altrimenti nessuno potrebbe mai entrare dopo), ma la **NCS può non terminare**: un processo può rimanere per sempre nella sezione non critica (ad esempio perché ha finito il suo lavoro o è andato in crash). Un buon protocollo **non può dipendere** dal fatto che l'altro processo ne esca.

### Algoritmi o meccanismi?

Il problema si risolve facilmente con **meccanismi** forniti dalla macchina concorrente (semafori, lock). Qui invece ci si concentra su soluzioni **puramente algoritmiche**:

- senza meccanismi di alto livello;
- usando **solo istruzioni atomiche elementari**: lettura (_load_) e scrittura (_store_) di variabili condivise.

È un esercizio fondamentale perché mostra **perché** quei meccanismi servono e quanto sia sottile farne a meno.

### Il caso con due processi

Si studia prima il problema per due processi, `p` e `q`, che condividono alcune variabili globali:

```text
loop forever
    non-critical section
    pre-protocol
    critical section
    post-protocol
```

Assunzione: i protocolli possono usare variabili locali o globali, ma **nessuna variabile usata nella CS o nella NCS viene usata nei protocolli** (così CS/NCS non interferiscono con i protocolli).

Nei listati si usa l'istruzione `await condizione`: significa "**aspetta finché la condizione diventa vera**". È una notazione indipendente dall'implementazione; la si può realizzare (in modo inefficiente) con un'**attesa attiva** (_busy-wait_): un ciclo che non fa nulla finché la condizione è falsa.

---

## Come si verifica la correttezza: LTL e diagrammi di stato

### Costruire il diagramma

Per dimostrare che una soluzione è corretta si costruisce il suo diagramma di stato e si **esaminano gli stati raggiungibili e le transizioni tra loro**. Il diagramma si costruisce **incrementalmente** dallo stato iniziale, aggiungendo gli stati che si possono raggiungere.

- **Mutua esclusione:** vale se l'insieme degli stati raggiungibili **non contiene** alcuno stato in cui entrambi i processi sono nella CS.
- Proprietà di vivacità (deadlock, starvation): si cercano **cammini o cicli** in cui qualcosa non succede mai.

### Esprimere le proprietà in LTL

La **logica temporale lineare (LTL)** permette di scrivere le proprietà in modo preciso. Due operatori bastano qui:

|Simbolo|Si legge|Significato|
|---|---|---|
|$\Box \varphi$|"sempre $\varphi$"|$\varphi$ vale in **ogni** stato dell'esecuzione|
|$\Diamond \varphi$|"prima o poi $\varphi$"|$\varphi$ vale in **qualche** stato futuro|

Con $p_3$ che indica "il processo `p` è all'istruzione 3" (nella CS), vale:

- **Mutua esclusione:** $\Box \neg (p_3 \wedge q_3)$ "Sempre: non è vero che `p` e `q` sono entrambi nella CS."
- **Assenza di starvation:** $\Box (p_2 \rightarrow \Diamond p_3)$ e $\Box (q_2 \rightarrow \Diamond q_3)$ "Sempre: se `p` è nel pre-protocollo (in attesa), allora prima o poi arriva nella CS." (E lo stesso per `q`.)

> [!tip] Una formula di vivacità si legge così "$\Box(A \rightarrow \Diamond B)$" = "ogni volta che accade $A$, prima o poi accade $B$". È il modello tipico di tutte le proprietà "qualcosa di buono prima o poi succede".

### Ridurre il numero di stati

Per studiare la correttezza del protocollo si può **abbreviare** il programma senza cambiarne la semantica rilevante:

- le istruzioni della CS e della NCS sono **irrilevanti** per la correttezza dell'algoritmo di sincronizzazione (per ipotesi non toccano le variabili dei protocolli);
- quindi si possono **rimuovere**, lasciando solo i protocolli e un punto che rappresenta "sono nella CS".

Il diagramma risultante è molto più piccolo (ad esempio da 16 stati a 4 nel primo tentativo qui sotto).

> [!warning] Attenzione a cosa si perde Togliere la NCS dal modello non significa che la NCS "non può essere infinita": significa solo che nel modello ridotto non la si vede. Quando si analizza la starvation bisogna ricordarsi che **il processo potrebbe fermarsi per sempre nella NCS** (si ragiona quindi anche sul programma completo).

---

## Cinque tentativi verso la soluzione

La sezione critica per due processi si risolve con un percorso a tappe. Ogni tentativo sembra ragionevole ma ha un difetto: studiarli uno per uno mostra **quali proprietà sono difficili da ottenere insieme** e come si arriva agli algoritmi giusti.

### Primo tentativo: la variabile `turn`

```text
integer turn := 1

p:                               q:
loop forever                     loop forever
  p1: non-critical section         q1: non-critical section
  p2: await turn = 1               q2: await turn = 2
  p3: critical section             q3: critical section
  p4: turn := 2                    q4: turn := 1
```

**Idea:** `turn` indica di chi è il **turno**. `p` entra solo se `turn = 1`, e uscendo passa il turno a `q`, e viceversa. Si può paragonare a un **testimone** (_baton_) che passa da una mano all'altra: solo chi ce l'ha può entrare.

**Stati.** $\langle p_i, q_j, turn \rangle$. Il diagramma completo ha **16 stati**.

**Versione abbreviata** (rimosse CS e NCS):

```text
p:  p1: await turn = 1     q:  q1: await turn = 2
    p2: turn := 2              q2: turn := 1
```

Il diagramma ha solo **4 stati**, disposti in un ciclo:

$$\langle p1,q1,1\rangle \xrightarrow{p1} \langle p2,q1,1\rangle \xrightarrow{p2} \langle p1,q1,2\rangle \xrightarrow{q1} \langle p1,q2,2\rangle \xrightarrow{q2} \langle p1,q1,1\rangle$$

**Verifica delle proprietà:**

|Proprietà|Esito|Perché|
|---|---|---|
|Mutua esclusione|✅ Vale|Non è mai raggiungibile uno stato $\langle p2, q2, _ \rangle$ (versione abbreviata)|
|Assenza di deadlock|✅ Vale|Se qualcuno è in `await`, prima o poi il turno gli viene passato e uno dei due entra|
|Assenza di starvation|❌ **Non vale**|Vedi sotto|

**Perché c'è starvation.** Si supponga che `p` entri in CS, passi il turno a `q` (`turn := 2`), poi torni nella sua NCS e **ci rimanga per sempre** (la NCS non ha l'obbligo di progredire). `q` può entrare una volta, riporta `turn := 1` e riparte, ma al giro successivo aspetta `turn = 2`: **solo `p` può impostarlo** e `p` non lo farà mai. Quindi `q` resta bloccato per sempre in attesa.

> [!warning] Il difetto di fondo `turn` è una **risorsa-permesso** che si può tenere indefinitamente: se il processo che ne è il titolare si ferma nella NCS (o muore), l'altro non entra mai. Entrambi i processi leggono e scrivono **una sola variabile globale**: se uno "muore", l'altro è bloccato. In più impone una **stretta alternanza** (p, q, p, q...) anche quando uno dei due non ha alcun interesse a entrare.

### Secondo tentativo: le variabili `want`

Per risolvere il problema si rende **esplicita e osservabile** l'intenzione di ciascun processo di entrare, con una variabile booleana per processo.

```text
boolean wantp := false
boolean wantq := false

p:                                 q:
loop forever                       loop forever
  p1: non-critical section           q1: non-critical section
  p2: await wantq = false            q2: await wantp = false
  p3: wantp := true                  q3: wantq := true
  p4: critical section               q4: critical section
  p5: wantp := false                 q5: wantq := false
```

**Idea:** "prima di entrare guardo se l'altro vuole entrare; se non vuole, annuncio che voglio entrare ed entro".

**Proprietà in LTL (versione completa):**

- Mutua esclusione: $\Box \neg (p_4 \wedge q_4)$
- Assenza di starvation: $\Box ((p_2 \rightarrow \Diamond p_4) \wedge (q_2 \rightarrow \Diamond q_4))$

**Versione abbreviata:**

```text
p:  p1: await wantq = false     q:  q1: await wantp = false
    p2: wantp := true               q2: wantq := true
    p3: wantp := false              q3: wantq := false
```

(Tra `p2` e `p3` il processo è nella CS.) La mutua esclusione diventa $\Box \neg (p_3 \wedge q_3)$.

**Verifica: ❌ la mutua esclusione NON vale.** Con il diagramma di stato si trova lo stato **raggiungibile** $\langle p3, q3, \text{true}, \text{true} \rangle$, cioè entrambi nella CS. Scenario:

|Passo|Cosa succede|Stato|
|---|---|---|
|1|`p` esegue `await wantq = false`: `wantq` è falso, passa|`p` è a `p2`|
|2|`q` esegue `await wantp = false`: `wantp` è ancora falso, passa|`q` è a `q2`|
|3|`p` esegue `wantp := true`|`p` entra in CS|
|4|`q` esegue `wantq := true`|`q` entra in CS|

**Perché.** Il **controllo** ("l'altro non vuole") e l'**annuncio** ("io voglio") sono due istruzioni separate: tra le due si può inserire l'altro processo. Il protocollo non è atomico. È la stessa struttura _check-then-act_ dell'esempio del latte.

### Terzo tentativo: annunciare prima di controllare

Si risolve il problema precedente **spostando l'assegnazione prima dell'attesa**: prima dichiaro che voglio entrare, poi controllo.

```text
boolean wantp := false
boolean wantq := false

p:                                 q:
loop forever                       loop forever
  p1: non-critical section           q1: non-critical section
  p2: wantp := true                  q2: wantq := true
  p3: await !wantq                   q3: await !wantp
  p4: critical section               q4: critical section
  p5: wantp := false                 q5: wantq := false
```

**Verifica:**

- ✅ **Mutua esclusione.** Se `p` è alla CS ha `wantp = true` e ha visto `wantq = false` dopo averlo annunciato; `q` non può quindi passare.
- ❌ **Si può bloccare tutto.** Scenario: `p2` (wantp = true), `q2` (wantq = true). Ora `p` aspetta `!wantq` e `q` aspetta `!wantp`: **entrambi vogliono entrare, nessuno ce la farà mai**.

> [!note] Deadlock o livelock? Il materiale del corso chiama questa situazione _livelock_, perché i processi in `await` stanno facendo **attesa attiva** (consumano CPU controllando una condizione). Nella terminologia di Ben-Ari, e a rigore, è un **deadlock**: nessuno dei due può più cambiare lo stato da solo (ognuno aspetta una condizione che solo l'altro può cambiare). Quello che conta è la conclusione: **entrambi tentano di entrare e nessuno ci riuscirà mai**, quindi la proprietà di assenza di deadlock è violata.

### Quarto tentativo: rinunciare all'intenzione

Il blocco nasce dal fatto che entrambi **insistono**. Il passo successivo è chiedere a un processo di **rinunciare** alla sua intenzione quando scopre che c'è contesa.

```text
boolean wantp := false
boolean wantq := false

p:                                 q:
loop forever                       loop forever
  p1: non-critical section           q1: non-critical section
  p2: wantp := true                  q2: wantq := true
  p3: while wantq                    q3: while wantp
  p4:   wantp := false               q4:   wantq := false
  p5:   wantp := true                q5:   wantq := true
  p6: critical section               q6: critical section
  p7: wantp := false                 q7: wantq := false
```

**Idea:** se vedo che l'altro vuole entrare, ritiro la mia intenzione (`wantp := false`) e riprovo (`wantp := true`), dando all'altro la possibilità di passare.

**Verifica:** ✅ mutua esclusione, ✅ non si blocca completamente, ma ❌ **può verificarsi un livelock**.

> [!example] Scenario di livelock I due processi procedono **in perfetto sincronismo**, passo dopo passo: entrambi annunciano, entrambi vedono la contesa, entrambi rinunciano, entrambi riannunciano, e così via all'infinito. Cambiano continuamente stato ma **nessuno entra mai**. Basta che lo scheduler sia "sfortunato" nell'alternare i due: è esattamente la situazione dei due pedoni nel corridoio che si spostano insieme dallo stesso lato.

È una situazione **improbabile** ma **possibile**, e un programma concorrente deve essere corretto per **tutti** gli intrecci, non solo per quelli probabili.

### Algoritmo di Dekker

La soluzione arriva da un'idea semplice: il quarto tentativo ha il difetto della **simmetria** (i due processi si comportano allo stesso modo e quindi possono ripetere la stessa mossa all'infinito). Serve qualcosa che **rompa la simmetria** quando c'è contesa: la variabile `turn` del primo tentativo, usata però in modo diverso.

> [!info] L'idea chiave `turn` non decide **chi può entrare**, ma **chi ha il diritto di insistere** quando c'è contesa. È trovata per la prima volta dal matematico olandese **T. J. Dekker** nel 1965. L'algoritmo è la **combinazione del primo e del quarto tentativo**.

```text
boolean wantp := false
boolean wantq := false
integer turn := 1

p:                                  q:
loop forever                        loop forever
  p1:  non-critical section           q1:  non-critical section
  p2:  wantp := true                  q2:  wantq := true
  p3:  while wantq                    q3:  while wantp
  p4:    if turn = 2                  q4:    if turn = 1
  p5:      wantp := false             q5:      wantq := false
  p6:      await turn = 1             q6:      await turn = 2
  p7:      wantp := true              q7:      wantq := true
  p8:  critical section               q8:  critical section
  p9:  turn := 2                      q9:  turn := 1
  p10: wantp := false                 q10: wantq := false
```

**Come funziona (lato `p`).**

1. `p2`: annuncio che voglio entrare.
2. `p3`: se l'altro **non** vuole entrare (`wantq` falso), esco subito dal `while` ed entro. Nessuna contesa → `turn` è irrilevante. Questo risolve il difetto del primo tentativo (bloccarsi se l'altro è fermo nella NCS).
3. Se c'è **contesa** (`wantq` vero), guardo di chi è il turno:
    - se `turn = 1` (tocca a me): **insisto**, resto nel `while` finché `q` ritira la sua intenzione;
    - se `turn = 2` (tocca a lui): **cedo**: ritiro la mia intenzione (`p5`), aspetto che il turno torni a me (`p6`), poi riannuncio (`p7`) e riprovo.
4. `p8`: sezione critica.
5. `p9`: passo il turno all'altro. `p10`: ritiro l'intenzione.

**Perché funziona.**

- **Mutua esclusione:** `p` entra solo se vede `wantq = false`, avendo già `wantp = true`.
- **Niente livelock:** quando c'è contesa uno dei due ha il turno e **insiste**, l'altro **cede**: la simmetria è rotta.
- **Niente starvation:** uscendo dalla CS ciascuno passa il turno all'altro, e `turn` non può restare "bloccato" nell'altro perché serve solo in caso di contesa.

**Proprietà in LTL:**

- Mutua esclusione: $\Box \neg (p_8 \wedge q_8)$
- Assenza di starvation: $\Box ((p_2 \rightarrow \Diamond p_8) \wedge (q_2 \rightarrow \Diamond q_8))$

> [!tip] Cosa notare L'algoritmo funziona su **qualsiasi architettura** che fornisca solo `load` e `store` atomici. È un risultato notevole (non servono istruzioni speciali), ma l'algoritmo è lungo e delicato.

### Algoritmo di Peterson

Altre soluzioni sono state proposte nel tempo per migliorare quella di Dekker (**Peterson 1981**, Manna-Pnueli, Doran-Thomas). Quella di **Peterson** è la più concisa: **fonde i due `await` in uno solo** con condizione composta.

```text
boolean wantp := false
boolean wantq := false
integer turn := 1

p:                                  q:
loop forever                        loop forever
  p1: NCS                             q1: NCS
  p2: wantp := true                   q2: wantq := true
  p3: turn := 2                       q3: turn := 1
  p4: await !wantq || turn = 1        q4: await !wantp || turn = 2
  p5: CS                              q5: CS
  p6: wantp := false                  q6: wantq := false
```

**Come leggerlo (lato `p`).**

- `p2`: "io voglio entrare".
- `p3`: "**ma ti lascio il turno**" (`turn := 2` significa "tocca a `q`"). È un gesto di cortesia.
- `p4`: "aspetto finché **o** tu non vuoi entrare, **o** il turno è tornato a me".

**Perché funziona.** Se **solo `p`** vuole entrare, `wantq` è falso e passa subito. Se **entrambi** vogliono entrare, entrambi scrivono `turn`: vince la **scrittura più recente**, quindi chi ha scritto **per ultimo** ha ceduto il turno all'altro e deve aspettare, mentre l'altro passa. Il "cedere" è fatto in modo che **uno solo** dei due resti in attesa.

|Situazione|Cosa accade|
|---|---|
|Solo `p` vuole entrare|`wantq = false` → `p` entra subito|
|Entrambi vogliono entrare|L'ultimo a scrivere `turn` cede; l'altro entra|
|Uscita|Chi esce abbassa il suo `want` → l'altro può passare|

> [!warning] Una precisazione importante sulle macchine reali Dekker e Peterson sono corretti con **memoria sequenzialmente consistente** (le scritture sono viste dagli altri processi nell'ordine in cui avvengono). I processori moderni e i compilatori **riordinano** letture e scritture per ottimizzare: su hardware reale questi algoritmi, scritti ingenuamente, **possono non funzionare** senza barriere di memoria (in Java, variabili `volatile`). Sono algoritmi di valore didattico e teorico; in pratica si usano i lock del linguaggio.

### Algoritmo del ticket (per N processi)

Per più di due processi si usa un'idea diversa, quella dei **biglietti numerati** (come al banco della salumeria). Nel materiale è indicato come variante dell'algoritmo _bakery_.

```text
int num := 1         // prossimo numero da distribuire
int next := 1        // numero attualmente servito
turn[1..n] := [0,0,0,...]    // numero in mano a ciascun processo

p[i]:
loop forever
  p1: NCS
  p2: < turn[i] := num; num := num + 1 >     // prendi il biglietto (atomico)
  p3: await turn[i] = next                   // aspetta il tuo turno
  p4: CS
  p5: next := next + 1                       // chiama il prossimo
```

**Come funziona.**

- Per entrare, ogni processo **prende un biglietto** (`turn[i]`) e incrementa il distributore (`num`).
- Aspetta finché il numero **servito** (`next`) è uguale al proprio biglietto.
- Uscendo, incrementa `next`, **chiamando il biglietto successivo**.

**Proprietà.** Poiché i biglietti sono serviti in ordine, l'accesso è **FIFO** (_first come, first served_): niente starvation, attesa limitata.

**Difetti.**

- Le variabili `num` e `next` crescono **sempre**: prima o poi possono andare in **overflow aritmetico** (si può mitigare con aritmetica modulare).
- Il passo `p2` (prendi il biglietto **e** incrementa) deve essere **atomico**: gli angoli `< ... >` indicano un'istruzione atomica composta, che richiede un supporto della macchina (come _fetch-and-add_).

> [!note] Nota Il vero algoritmo _bakery_ di Lamport evita proprio questa istruzione atomica, usando un array di flag e il calcolo del massimo; quella riportata qui è la versione a ticket, più semplice ma che assume il supporto atomico.

### Tabella riassuntiva dei tentativi

|Tentativo|Idea|Mutua esclusione|Assenza di deadlock|Assenza di starvation|Problema|
|---|---|---|---|---|---|
|**Primo** (`turn`)|Testimone che si passa|✅|✅|❌|Se un processo resta in NCS l'altro si blocca; alternanza forzata|
|**Secondo** (`want`, controlla poi annuncia)|Annuncio l'intenzione|❌|—|—|Controllo e annuncio non atomici: entrano in due|
|**Terzo** (`want`, annuncia poi controlla)|Annuncio prima di controllare|✅|❌|—|Entrambi annunciano e aspettano per sempre|
|**Quarto** (rinuncia)|Se c'è contesa, cedo|✅|✅ (se lo scheduler aiuta)|❌|Livelock con esecuzione in lockstep|
|**Dekker**|Terzo/quarto + `turn` per rompere la simmetria|✅|✅|✅|Lungo e delicato|
|**Peterson**|Come Dekker, in forma compatta|✅|✅|✅|Solo 2 processi; sensibile al riordino della memoria|
|**Ticket**|Numeri progressivi|✅|✅|✅ (FIFO)|Overflow; serve un passo atomico|

---

## Istruzioni atomiche composte

### Perché servono

L'algoritmo di Dekker funziona usando soltanto `load` e `store` atomici. Ma il problema della sezione critica (e della mutua esclusione in generale) si **semplifica molto** se la macchina offre istruzioni atomiche **più potenti**, direttamente supportate dall'hardware.

La notazione per specificare l'atomicità è mettere le istruzioni tra **parentesi angolari** $\langle \dots \rangle$: il gruppo viene eseguito **senza possibilità di intreccio**.

Le istruzioni composte più comuni:

|Istruzione|Cosa fa (atomicamente)|
|---|---|
|**test-and-set**$(x, r)$|$\langle r := x;\ x := 1 \rangle$: leggi il vecchio valore di $x$ in $r$ e imposta $x$ a 1|
|**exchange**$(a, b)$|scambia i valori di $a$ e $b$|
|**fetch-and-add**$(x, k)$|restituisce il valore di $x$ e lo incrementa di $k$|
|**compare-and-swap**$(x, \text{atteso}, \text{nuovo})$|se $x$ vale `atteso`, lo sostituisce con `nuovo` e segnala il successo; altrimenti non fa nulla|

> [!tip] Perché sono utili In tutte e quattro **leggere e modificare** una variabile condivisa avviene in un **unico passo indivisibile**. È proprio la separazione tra "leggi" e "scrivi" che crea le race condition (come nell'incremento non atomico): queste istruzioni la eliminano.

### Sezione critica con test-and-set

```text
integer lock := 0

p:                                       q:
integer was_locked                       integer was_locked
loop forever                             loop forever
  p1: NCS                                  q1: NCS
      repeat                                   repeat
  p2:   test_and_set(lock, was_locked)     q2:   test_and_set(lock, was_locked)
  p3: until was_locked = 0                 q3: until was_locked = 0
  p4: CS                                   q4: CS
  p5: lock := 0                            q5: lock := 0
```

**Come funziona.**

- `lock = 0` significa **libero**, `lock = 1` **occupato**.
- `test_and_set` legge il valore **vecchio** in `was_locked` e mette `lock` a 1, in modo atomico.
- Se `was_locked = 0`: ho trovato il lock libero e **l'ho preso io** (ora è 1) → entro.
- Se `was_locked = 1`: era già occupato (e lo è ancora, ho solo riscritto 1) → riprovo (**attesa attiva**, _spin_).
- Uscendo, `lock := 0` lo libera.

**Perché la mutua esclusione vale.** Due processi non possono leggere entrambi "0": la lettura e l'impostazione a 1 sono indivisibili, quindi **uno solo** vede 0.

**Pregi e limiti.**

- ✅ Funziona per **N processi**, non solo per 2, ed è molto più corto dei protocolli algoritmici.
- ⚠️ L'attesa attiva **spreca CPU**.
- ⚠️ Non garantisce da sola l'assenza di starvation: nulla stabilisce chi vince quando più processi "provano" insieme.

---

## I lock

Con le istruzioni atomiche composte si realizza facilmente un meccanismo di base: il **lock**.

Un lock offre due operazioni (entrambe atomiche):

- **`acquire`**: prendi il lock (se è già preso, aspetta);
- **`release`**: rilascia il lock.

```text
lock sharedlock

p:                                q:
loop forever                      loop forever
  p1: NCS                           q1: NCS
  p2: acquire(sharedlock)           q2: acquire(sharedlock)
  p3: CS                            q3: CS
  p4: release(sharedlock)           q4: release(sharedlock)
```

Il pre-protocollo diventa una sola chiamata (`acquire`) e il post-protocollo un'altra (`release`): **protegge** il codice della sezione critica con una soluzione semplice al problema.

> [!info] Come si realizza un lock Un lock può essere implementato con una **spin-lock** (attesa attiva su test-and-set, come sopra) oppure, nei sistemi operativi e nelle JVM, facendo **sospendere** il thread che non ottiene il lock (attesa bloccante) per non sprecare CPU.

### Atomicità in Java: `synchronized`

In Java il lock è disponibile come **blocco `synchronized`** su un **oggetto lock condiviso**:

```java
// thread A
synchronized (lock) {
    statementA();
    statementB();
}

// thread B
synchronized (lock) {
    statementC();
    statementD();
}
```

Poiché entrambi i blocchi sono protetti **dallo stesso oggetto `lock`**, la sequenza `a, b` e la sequenza `c, d` vengono eseguite **senza intreccio tra le due sequenze**: non possono esistere scenari come `a c b d` o `c a d b`. Sono possibili solo `a b c d` oppure `c d a b`.

> [!warning] Cose da tenere presenti
> 
> - Serve **lo stesso oggetto lock** nei due blocchi: se i due thread sincronizzano su oggetti diversi, **non c'è nessuna esclusione reciproca**.
> - Il blocco garantisce che nessun **altro blocco sullo stesso lock** si intrecci, ma il codice **non protetto** può comunque girare in qualsiasi momento (e il thread può essere sospeso dallo scheduler _dentro_ il blocco: gli altri semplicemente non possono entrarvi).
> - Il lock viene rilasciato automaticamente all'uscita dal blocco, anche in caso di eccezione.
> - Usare più lock annidati apre la strada al **deadlock** (ordine di acquisizione diverso tra thread).

### Oltre i lock

I lock risolvono la mutua esclusione ma non coprono tutto (ad esempio la **sincronizzazione**: aspettare che una condizione diventi vera senza attesa attiva). Per questo esistono meccanismi più ricchi, come **semafori** e **monitor**, trattati nel modulo successivo.

---

## Model checking di programmi concorrenti

### L'idea

Costruire a mano il diagramma di stato e controllare le proprietà funziona per programmi minuscoli. Per programmi veri si automatizza con un **model checker**: uno strumento che prende un **modello** del programma e una **proprietà** (di solito scritta in LTL) ed esplora **in modo esaustivo** tutti gli stati raggiungibili, per dire se la proprietà vale.

Il quadro di lavoro include:

1. un **linguaggio per definire il modello** del programma concorrente;
2. il **model checker**, che legge il modello e lo analizza;
3. (di solito) un linguaggio per **specificare le proprietà**, ad esempio LTL.

Se la proprietà è violata, il model checker restituisce un **controesempio**: l'esecuzione concreta (sequenza di passi) che porta alla violazione, preziosissima per capire e correggere il bug.

||Vantaggi|Limiti|
|---|---|---|
|Model checking|Automatico, **esaustivo** (non "a campione"), fornisce il controesempio|**Esplosione degli stati**: il modello deve avere un numero di stati gestibile|

Gli strumenti principali visti: **SPIN/PROMELA**, **TLA+/PlusCal**, **Java PathFinder (JPF)**.

### SPIN e PROMELA

- **SPIN** è un model checker molto diffuso sia nella ricerca accademica sia nello sviluppo software industriale. È **estremamente efficiente** e viene usato per modellare e progettare sistemi concorrenti e distribuiti in generale.
- **PROMELA** è il **linguaggio** con cui si scrivono i modelli per SPIN: ha un numero limitato di costrutti pensati per costruire modelli di sistemi concorrenti.

Esempio: l'algoritmo di Dekker in PROMELA (lato `p`; `q` è simmetrico).

```promela
bool wantp = false, wantq = false;
byte turn = 1;

proctype p() {
    do ::
        wantp = true;
        do :: !wantq -> break;
           :: else ->
                if :: (turn == 1)
                   :: (turn == 2) ->
                        wantp = false;
                        (turn == 1);
                        wantp = true
                fi
        od;
        printf("LOG: p in CS\n");
        turn = 2;
        wantp = false
    od
}

proctype q() { /* simile */ }

init {
    atomic {
        run p();
        run q()
    }
}
```

**Come leggerlo.**

|Costrutto|Significato|
|---|---|
|`proctype p() { ... }`|Definisce un **tipo di processo**|
|`do :: ... :: ... od`|**Ciclo** con alternative: a ogni giro si sceglie una delle alternative **eseguibili** (guardate)|
|`if :: ... :: ... fi`|**Scelta** tra alternative|
|`!wantq -> break`|"se `!wantq` è vero, esci dal ciclo": la condizione prima della freccia è la **guardia**|
|`else`|Alternativa scelta solo se nessuna altra è eseguibile|
|`(turn == 1);`|Un'espressione da sola è un'istruzione che **blocca** finché non è vera: equivale all'`await`|
|`init { atomic { run p(); run q() } }`|Avvia i due processi in modo atomico|
|`printf`|Stampa (utile per tracciare l'esecuzione)|

> [!example] Come si verifica la mutua esclusione in Promela (aggiunta) Un modo comune è usare un contatore e un'asserzione: si dichiara `byte critical = 0;`, e nella CS di ogni processo si scrive `critical++; assert(critical == 1); critical--;`. Se esiste un intreccio in cui due processi sono nella CS, SPIN trova la violazione dell'`assert` e mostra la traccia. Si lancia con `spin -run modello.pml`.

### TLA+ e PlusCal

**TLA** (_Temporal Logic of Actions_) è un linguaggio formale di specifica introdotto da **Leslie Lamport**, basato su **matematica discreta semplice** (teoria degli insiemi e predicati). L'ultima versione è **TLA+**. Si usa per specificare e verificare sistemi complessi del mondo reale: un esempio noto è l'uso nei servizi cloud di **Amazon Web Services**.

**Cosa descrive una specifica TLA+.** L'insieme di tutti i **comportamenti leciti** (tracce di esecuzione) di un sistema. Così si può mostrare che un progetto del sistema ("**il come**") realizza correttamente le proprietà desiderate ("**il cosa**").

**TLC** è il model checker di TLA+: prende una specifica e controlla **esaustivamente** le proprietà su **tutte** le possibili tracce.

**PlusCal** (prima chiamato +CAL) è un **linguaggio per algoritmi** basato su TLA+: sta agli algoritmi come un linguaggio di programmazione sta ai programmi. Un algoritmo PlusCal viene **tradotto** in una specifica TLA+ che si controlla con TLC.

Esempio: l'algoritmo di Peterson in PlusCal.

```text
--algorithm Peterson {
  variables flag = [i \in {0,1} |-> FALSE], turn = 0;

  process (proc \in {0,1}) {
    a0:  while (TRUE) {
    a1:    flag[self] := TRUE;
    a2:    turn := Not(self);
    a3a:   if (flag[Not(self)]) { goto a3b } else { goto cs };
    a3b:   if (turn = Not(self)) { goto a3a } else { goto cs };
    cs:    skip;                     \* sezione critica
    a4:    flag[self] := FALSE;
    }
  }
}
```

**Come leggerlo.**

- Un'**azione atomica** va **da un'etichetta alla successiva**: tutto ciò che sta tra due etichette è un passo indivisibile. Le etichette sono quindi i "punti di intreccio" possibili.
- `flag[i]` è l'equivalente di `wantp`/`wantq` (un array indicizzato dall'id del processo); `self` è l'id del processo corrente e `Not(self)` l'id dell'altro.
- `a3a` e `a3b` implementano `await !wantq || turn = ...` come una coppia di controlli con salti (`goto`).

**Verifica.** La proprietà di mutua esclusione è espressa come un **invariante**, cioè una condizione vera in **tutti** gli stati raggiungibili:

$$\text{MutualExclusion} ;=; (pc[0] \neq \text{"cs"}) ;\vee; (pc[1] \neq \text{"cs"})$$

dove $pc$ è il "program counter" di ciascun processo. TLC esplora l'algoritmo (qui con soli **146 stati raggiungibili**) e conferma che l'invariante vale sempre.

> [!tip] Quando il model checking non basta Per algoritmi con moltissimi (o infiniti) stati raggiungibili, TLC non riesce più a esplorare tutto: la correttezza va allora **dimostrata deduttivamente** (ad esempio trovando un **invariante induttivo**: vero nello stato iniziale e preservato da ogni transizione). TLC resta comunque molto utile per scovare errori **prima** di investire nello sforzo della dimostrazione.

### Java PathFinder (JPF)

**JPF** è un model checker specializzato nella **verifica di programmi Java**, sviluppato dalla **NASA** per software critico, ed è un progetto **open source**.

**Come funziona.** JPF è una **JVM speciale** che esegue il programma **lungo tutti i possibili scenari** (cammini di esecuzione), cioè esplora tutti gli intrecci dei thread. Controlla la presenza di **violazioni di proprietà**, come:

- **deadlock**;
- **eccezioni non catturate**;
- violazioni di asserzioni.

Se trova un errore, JPF riporta **l'intera esecuzione** che lo provoca (i passi di ogni thread e gli stack dei thread), come nel caso di un deadlock riportato come "1 Error Found: Deadlock".

**Architettura (modello di funzionamento).**

|Componente|Ruolo|
|---|---|
|**Virtual Machine** (core)|Esegue il bytecode Java e gestisce gli stati|
|**Search Strategy**|Decide come esplorare lo spazio degli stati (quale cammino seguire, quando tornare indietro)|
|**Property checker**|Controlla le proprietà da verificare (deadlock, eccezioni, ecc.)|
|**Listener** (VM e di ricerca)|Osservano l'esecuzione e la ricerca per raccogliere informazioni o intervenire|
|**Choice generator**|Genera le scelte non deterministiche (ad esempio l'ordine di scheduling) con euristiche sui dati e sullo scheduling|
|**Library abstraction** (MJI)|Sostituisce parti della libreria (ad esempio metodi nativi) con versioni modellabili|
|**Verification report**|Output: esito della verifica con il percorso che porta all'errore|

---

## Quadro d'insieme

```text
Programma concorrente
        │
        ▼
   Modello (interleaving di istruzioni atomiche)
        │      ├── FSP: tutto è un processo
        │      └── Diagramma di stato: <puntatori, variabili>
        ▼
   Proprietà (LTL): mutua esclusione, assenza di deadlock/starvation
        │
        ▼
   Verifica
        ├── a mano: costruire il diagramma e cercare stati/cicli "cattivi"
        └── automatica: model checker (SPIN, TLC, JPF) → verdetto o controesempio
```

---

## Riepilogo veloce

- Un **modello** descrive un programma a un livello di astrazione adeguato; quelli **formali** permettono di ragionare su tutte le esecuzioni.
- L'esecuzione concorrente si modella come **intreccio arbitrario** di istruzioni **atomiche**; uno **stato** è la tupla (puntatori di controllo, valori delle variabili); uno **scenario** è un cammino nel diagramma; un **ciclo** è una computazione infinita.
- Il numero di scenari esplode: $\frac{(\sum m_i)!}{\prod m_i!}$. Per questo si lavora sugli **stati**, non sugli scenari.
- **Atomicità**: `n := n+1` non è atomico (leggi-modifica-scrivi). Con due processi, 4 scenari su 6 perdono un incremento. Anche i dati composti (liste, classi) passano per **stati interni inconsistenti**: servono mutua esclusione sulle operazioni.
- **Sezione critica**: pre-protocollo, CS, post-protocollo. Proprietà: **mutua esclusione**, **assenza di deadlock**, **assenza di starvation**, **progresso della CS** (la NCS può non progredire).
- Le proprietà si scrivono in **LTL**: $\Box \neg(p_{CS} \wedge q_{CS})$ per la mutua esclusione, $\Box(\text{attesa} \rightarrow \Diamond \text{CS})$ per l'assenza di starvation.
- Tentativi: `turn` (starvation) → `want` controlla poi annuncia (**viola** la mutua esclusione) → `want` annuncia poi controlla (blocco) → `want` con rinuncia (livelock) → **Dekker** (combina `turn` e rinuncia) e **Peterson** (versione compatta). Per **N processi**: ticket/bakery.
- Con istruzioni atomiche composte (**test-and-set**, exchange, fetch-and-add, compare-and-swap) il problema si semplifica: nascono i **lock**, in Java il blocco `synchronized`.
- **Model checking**: esplorazione esaustiva automatica degli stati con controesempio. Strumenti: **SPIN/PROMELA**, **TLA+/PlusCal (TLC)**, **JPF**.

---

## Glossario

|Termine|Significato|
|---|---|
|Istruzione atomica|Istruzione eseguita in modo indivisibile, senza intreccio|
|Interleaving|Intreccio delle istruzioni atomiche dei vari processi|
|Stato|Tupla: puntatori di controllo dei processi + valori delle variabili|
|Scenario|Cammino nel diagramma di stato da quello iniziale; un'esecuzione possibile|
|Race condition|Esito che dipende dall'ordine di accesso a dati condivisi|
|Sezione critica (CS)|Parte di codice che accede a risorse condivise e va eseguita in mutua esclusione|
|NCS|Parte di codice non critica; può non terminare|
|Pre/post-protocollo|Codice di ingresso/uscita attorno alla CS|
|Busy-wait (spin)|Attesa che controlla ripetutamente una condizione consumando CPU|
|Starvation|Un processo attende indefinitamente pur essendo ripetutamente possibile servirlo|
|Livelock|Processi che cambiano stato continuamente senza progredire|
|Invariante|Proprietà vera in tutti gli stati raggiungibili|
|LTL|Logica temporale lineare; operatori $\Box$ (sempre), $\Diamond$ (prima o poi)|
|Model checker|Strumento che verifica esaustivamente proprietà su un modello a stati finiti|
|Controesempio|Traccia di esecuzione che mostra una violazione di una proprietà|
|test-and-set|Istruzione atomica: leggi il vecchio valore e imposta a 1|
|Lock|Meccanismo con `acquire`/`release` per garantire la mutua esclusione|

---

## Domande di autoverifica

> [!question]- Quanti scenari ha un sistema con 2 processi da 4 azioni ciascuno? $\binom{8}{4} = 70$.

> [!question]- Perché `contatore++` può dare risultati sbagliati con due thread? Perché si scompone in lettura, incremento, scrittura. Se i due thread leggono lo stesso valore prima che uno scriva, un incremento va perso. Con 2 processi e incremento non atomico, 4 scenari su 6 danno risultato 1 invece di 2.

> [!question]- Quali proprietà deve avere una soluzione al problema della sezione critica? Mutua esclusione, assenza di deadlock, assenza di starvation individuale e progresso della CS (la NCS invece non è tenuta a progredire).

> [!question]- Qual è la differenza tra assenza di deadlock e assenza di starvation? La prima è a livello di sistema ("se qualcuno vuole entrare, qualcuno entra"), la seconda a livello di singolo processo ("chi vuole entrare, prima o poi entra"). La seconda implica la prima.

> [!question]- Perché il primo tentativo (`turn`) soffre di starvation? Perché un processo può restare per sempre nella NCS senza passare il turno: l'altro, che attende `turn` al suo valore, resta bloccato per sempre.

> [!question]- Perché il secondo tentativo viola la mutua esclusione? Il controllo ("l'altro non vuole entrare") e l'annuncio ("io voglio entrare") non sono atomici: entrambi i processi possono passare il controllo prima che uno dei due annunci, ed entrare insieme nella CS.

> [!question]- Come risolve il problema l'algoritmo di Dekker? Mantiene l'annuncio con `want` (come nei tentativi precedenti) e usa `turn` **solo in caso di contesa**, per decidere chi insiste e chi cede. Così rompe la simmetria che causava il livelock e non blocca nessuno se l'altro è fermo nella NCS.

> [!question]- In che modo test-and-set permette di risolvere la sezione critica per N processi? Legge il vecchio valore del lock e lo imposta a 1 in modo atomico: un solo processo può leggere 0 e quindi entrare; gli altri leggono 1 e riprovano.

> [!question]- Cosa garantisce `synchronized (lock)` in Java e cosa no? Garantisce che i blocchi protetti dallo **stesso** lock non si intreccino tra loro. Non protegge codice non sincronizzato né blocchi su lock diversi.

> [!question]- Qual è il vantaggio del model checking rispetto al testing? È **esaustivo**: esplora tutti gli intrecci possibili (non solo quelli che capita di eseguire) e, se trova un errore, fornisce la traccia esatta per riprodurlo. Il limite è l'esplosione degli stati.