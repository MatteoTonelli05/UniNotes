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

### Non-atomicità a livello macchina

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

> [!warning] **Atomico** è solo ciò che l'hardware o il linguaggio _garantiscono_ essere indivisibile. 
> In Java, ad esempio, `contatore++` non è atomico; lo è invece `AtomicInteger.incrementAndGet()`.

---
## Strutture dati non atomiche

La nozione di "atomico" non vale solo per le istruzioni, ma anche per i **dati**.
### Oggetto dati atomico

> [!abstract] Un oggetto dati è **atomico** se il numero dei suoi possibili stati coincide con il numero dei valori che può assumere, e le operazioni cambiano _atomicamente_ quello stato. 

***Esempio**: un `boolean`. Ha 2 stati possibili (vero, falso) e 2 valori possibili (vero, falso): corrispondono uno a uno. Scrivere un valore lo porta da uno stato all’altro in un colpo solo.*



>[!warning] _I **tipi primitivi** dei linguaggi concorrenti sono tipicamente atomici_
>In Java `double` e `long` (64 bit) **non** sono garantiti atomici se non dichiarati `volatile`: la specifica ammette che una scrittura venga eseguita come **due scritture da 32 bit**. Un altro thread potrebbe leggere una “metà vecchia, metà nuova”, cioè un numero che nessuno ha mai scritto. È un caso di dato primitivo non atomico.

### Tipi di dati astratti (ADT)

Una classe, una `struct`, un array, una lista sono fatti da **più pezzi** (campi, nodi, puntatori). Modificarli richiede **più istruzioni**, e tra un’istruzione e la successiva l’oggetto è “a metà”.

```java
class Intervallo {
    int min;   // invariante: min <= max
    int max;

    void imposta(int a, int b) {   // si assume a <= b
        min = a;     // passo 1
        max = b;     // passo 2
    }
}
```

Per questi si **distinguono DUE tipi di stato**:

|                   | A chi interessa                                      | Esempio (lista)                            |
| ----------------- | ---------------------------------------------------- | ------------------------------------------ |
| **Stato Interno** | A chi **definisce** l'oggetto (chi scrive la classe) | Puntatori dei nodi, contatore `size`, ecc. |
| **Stato Esterno** | A chi **usa** l'oggetto                              | "La lista contiene [a, b, c]"              |

La corrispondenza tra stati interni ed esterni è **parziale**:

- esistono stati interni **senza** un corrispondente stato esterno;
- gli stati interni che hanno un corrispondente esterno si dicono **CONSISTENTI**.

> [!example] Esempio con una lista concatenata 
> Inserire un elemento richiede più passi: 
> 1. creare il nodo
> 2. collegarlo alla lista
> 3. aggiornare `size`
> 
> Dopo aver collegato il nodo ma **prima** di aver aggiornato `size`, lo stato interno è **inconsistente** (la lista ha 4 nodi ma `size` dice 3). 

### Perché è un problema solo nella concorrenza

- **Programmazione sequenziale:** nessun problema, grazie all'_information hiding_. Chi usa la lista chiama un metodo e riprende il controllo solo a operazione finita: **non vede mai** gli stati intermedi inconsistenti.
- **Programmazione concorrente:** un altro processo può **accedere all'oggetto mentre è a metà operazione** e trovarlo in stato inconsistente.

> [!warning] Soluzione 
> Servono meccanismi che garantiscano che i processi lavorino su oggetti dati **sempre in stati consistenti**: in pratica, rendere le operazioni sull'oggetto **atomiche** rispetto agli altri processi, cioè in **mutua esclusione** (con lock, `synchronized`, monitor...).

---
## Processi ciclici e cicli nel diagramma

Due processi che ciclano su una condizione, con `n` inizialmente a 1:

![[Pasted image 20261102144250.png]]
- `p1` è il **controllo** della condizione, `p2` è il **corpo** del ciclo (idem per `q1`, `q2`).
- Il diagramma di stato ha stati $\langle p, q, n \rangle$ e contiene **cicli**.

![[Pasted image 20261102144341.png|250]]

> [!info] Si noti come un grafo **finito** può rappresentare computazioni **infinite**: basta un ciclo. 
> Per questo le proprietà di _vivacità_ (qualcosa di buono prima o poi accade) si studiano cercando **cicli** nel diagramma in cui il "qualcosa di buono" non avviene mai.

---
## Il problema della sezione critica

### Definizione

Si hanno $N$ processi, ciascuno dei quali esegue **in un ciclo infinito** una sequenza di istruzioni divisibile in due parti:

- **sezione critica (CS, _critical section_)**: istruzioni che accedono a **oggetti condivisi**;
- **sezione non critica (NCS, _non-critical section_)**: tutto il resto.
![[Pasted image 20261102144520.png|497]]
Il compito è **progettare il pre- e il post-protocollo** in modo che valgano certe proprietà.
### Le proprietà richieste

| Proprietà                             | Significato                                                                                                                                                                                              |
| ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Mutua esclusione**                  | Le istruzioni delle sezioni critiche di due o più processi **non** devono essere intrecciate: al più un processo alla volta è in CS                                                                      |
| **Assenza di deadlock**               | Se alcuni processi cercano di entrare nella loro CS, **almeno uno** ci riesce prima o poi                                                                                                                |
| **Assenza di starvation individuale** | Se un **qualsiasi** processo cerca di entrare nella CS, **prima o poi ci riesce** (_bounded waiting_: nella forma più forte, c'è un limite al numero di volte in cui gli altri possono passargli avanti) |
| **Progresso della CS**                | Una volta che un processo inizia la sua CS, la **deve finire**                                                                                                                                           |

> [!tip] Differenza tra le due proprietà di vivacità
> 
> - **Assenza di deadlock** è una garanzia a livello di **sistema**: "se qualcuno vuole entrare, qualcuno entrerà". Ma potrebbe sempre entrare lo stesso processo, lasciando un altro fuori per sempre.
> - **Assenza di starvation** è una garanzia a livello di **singolo processo**: "chi vuole entrare, prima o poi entra".
> > [!info] **La seconda è più forte: se vale l'assenza di starvation vale anche l'assenza di deadlock, non viceversa.**

> [!attention] Si assume che la **CS termini sempre** (altrimenti nessuno potrebbe mai entrare dopo)
> ma la **NCS può non terminare**: un processo può rimanere per sempre nella sezione non critica (ad esempio perché ha finito il suo lavoro o è andato in crash). Un buon protocollo **non può dipendere** dal fatto che l'altro processo ne esca.
### Esempio con due processi

Usiamo una variabile condivisa per sincronizzare due processi $p$ e $q$
![[Pasted image 20261102153051.png|512]]
> [!info] `await turn = 1` significa “resta qui finché `turn` non vale 1”.
> È una notazione indipendente dall'implementazione; la si può realizzare (in modo inefficiente) con un'**attesa attiva** (_busy-wait_): un ciclo che non fa nulla finché la condizione è falsa.

---
## Come si verifica la correttezza: LTL e diagrammi di stato

### Costruire il diagramma

Per dimostrare che una soluzione è corretta si costruisce il suo diagramma di stato e si **esaminano gli stati raggiungibili e le transizioni tra loro**. Il diagramma si costruisce **incrementalmente** dallo stato iniziale, aggiungendo gli stati che si possono raggiungere.

- **Mutua esclusione:** vale se l'insieme degli stati raggiungibili **non contiene** alcuno stato in cui entrambi i processi sono nella CS.
- Proprietà di vivacità (deadlock, starvation): si cercano **cammini o cicli** in cui qualcosa non succede mai.

### Esprimere le proprietà in LTL

La **logica temporale lineare (LTL)** permette di scrivere le proprietà in modo preciso. Due operatori bastano qui:

| Simbolo                | Si legge                | Significato                                      |
| ---------------------- | ----------------------- | ------------------------------------------------ |
| $\small{\Box} \varphi$ | "sempre $\varphi$"      | $\varphi$ vale in **ogni** stato dell'esecuzione |
| $\diamond \varphi$     | "prima o poi $\varphi$" | $\varphi$ vale in **qualche** stato futuro       |

> [!info] **Mutua esclusione:** $\Box \neg (p_3 \wedge q_3)$ 
> sempre: non è vero che `p` e `q` sono entrambi nella CS."

 > [!info] **Assenza di starvation:** $\Box (p_2 \rightarrow \Diamond p_3)$ e $\Box (q_2 \rightarrow \Diamond q_3)$ 
 > "Sempre: se `p` è nel pre-protocollo (in attesa), allora prima o poi arriva nella CS." (E lo stesso per `q`)

> [!tip] **Formula di vivacità:** "$\Box(A \rightarrow \Diamond B)$" 
>"Ogni volta che accade $A$, prima o poi accade $B$". È il modello tipico di tutte le proprietà "qualcosa di buono prima o poi succede".
### Ridurre il numero di stati

Per studiare la correttezza del protocollo si può **abbreviare** il programma senza cambiarne la semantica rilevante:

- le istruzioni della CS e della NCS sono **irrilevanti** per la correttezza dell'algoritmo di sincronizzazione (per ipotesi non toccano le variabili dei protocolli);
- quindi si possono **rimuovere**, lasciando solo i protocolli e un punto che rappresenta "sono nella CS".

Il diagramma risultante è molto più piccolo (ad esempio da 16 stati a 4 nel primo tentativo qui sotto).

![[Pasted image 20261102160250.png]]

> [!warning] Togliere la NCS dal modello non significa che la NCS "non può essere infinita": significa solo che nel modello ridotto non la si vede. 
> Quando si analizza la starvation bisogna ricordarsi che **il processo potrebbe fermarsi per sempre nella NCS** (si ragiona quindi anche sul programma completo).

---
## Cinque tentativi verso la soluzione

La sezione critica per due processi si risolve con un percorso a tappe. Ogni tentativo sembra ragionevole ma ha un difetto: studiarli uno per uno mostra **quali proprietà sono difficili da ottenere insieme** e come si arriva agli algoritmi giusti.

### Primo tentativo: la variabile `turn`
![[Pasted image 20261102160638.png|561]]
> [!info] **Idea:** 
> `turn` indica di chi è il **turno**. `p` entra solo se `turn = 1`, e uscendo passa il turno a `q`, e viceversa. 

**Versione abbreviata** (rimosse CS e NCS):
![[Pasted image 20261102160929.png|593]]

Il diagramma ha solo **4 stati**, disposti in un ciclo:

$$\langle p1,q1,1\rangle \xrightarrow{p1} \langle p2,q1,1\rangle \xrightarrow{p2} \langle p1,q1,2\rangle \xrightarrow{q1} \langle p1,q2,2\rangle \xrightarrow{q2} \langle p1,q1,1\rangle$$

> [!fail] C'è starvation
> Si supponga che `p` entri in CS, passi il turno a `q` (`turn := 2`), poi torni nella sua NCS e **ci rimanga per sempre** (la NCS non ha l'obbligo di progredire). `q` può entrare una volta, riporta `turn := 1` e riparte, ma al giro successivo aspetta `turn = 2`: **solo `p` può impostarlo** e `p` non lo farà mai. Quindi `q` resta bloccato per sempre in attesa.
### Secondo tentativo: le variabili `want`

Per risolvere il problema si rende **esplicita e osservabile** l'intenzione di ciascun processo di entrare, con una variabile booleana per processo.
![[Pasted image 20261102161314.png|578]]

> [!abstract] **Idea** 
> prima di entrare guardo se l'altro vuole entrare; se non vuole, annuncio che voglio entrare ed entro.

**Proprietà in LTL (versione completa):**
- Mutua esclusione: $\Box \neg (p_4 \wedge q_4)$
- Assenza di starvation: $\Box ((p_2 \rightarrow \Diamond p_4) \wedge (q_2 \rightarrow \Diamond q_4))$

**Versione abbreviata:**
![[Pasted image 20261102161423.png|574]]

(Tra `p2` e `p3` il processo è nella CS.) La mutua esclusione diventa $\Box \neg (p_3 \wedge q_3)$.

> [!fail] **La mutua esclusione NON vale.**
>  Con il diagramma di stato si trova lo stato **raggiungibile** $\langle p3, q3, \text{true}, \text{true} \rangle$, cioè entrambi nella CS. Scenario:
>  
>| Passo | Cosa succede                                                    | Stato           |
>| ----- | --------------------------------------------------------------- | --------------- |
>| 1     | `p` esegue `await wantq = false`: `wantq` è falso, passa        | `p` è a `p2`    |
>| 2     | `q` esegue `await wantp = false`: `wantp` è ancora falso, passa | `q` è a `q2`    |
>| 3     | `p` esegue `wantp := true`                                      | `p` entra in CS |
>| 4     | `q` esegue `wantq := true`                                      | `q` entra in CS |
### Terzo tentativo: annunciare prima di controllare

> [!abstract] Idea
> Si risolve il problema precedente **spostando l'assegnazione prima dell'attesa**: prima dichiaro che voglio entrare, poi controllo.

![[Pasted image 20261102161955.png|573]]

> [!fail] **Si può bloccare tutto** 
> Scenario: p2 (wantp = true), q2 (wantq = true). Ora p aspetta !wantq e q aspetta !wantp, entrambi vogliono entrare, ma nessuno ce la farà mai.

> [!question] Deadlock o livelock? 
> Il materiale del corso chiama questa situazione _livelock_, perché i processi in `await` stanno facendo **attesa attiva** (consumano CPU controllando una condizione). 
> 
> Nella terminologia di Ben-Ari, e a rigore, è un **deadlock**: nessuno dei due può più cambiare lo stato da solo.
### Quarto tentativo: rinunciare all'intenzione

Il blocco nasce dal fatto che entrambi **insistono**. Il passo successivo è chiedere a un processo di **rinunciare** alla sua intenzione quando scopre che c'è contesa.
![[Pasted image 20261102162253.png|558]]
> [!abstract] **Idea**
> Se vedo che l'altro vuole entrare, ritiro la mia intenzione (`wantp := false`) e riprovo (`wantp := true`), dando all'altro la possibilità di passare.

> [!fail] Scenario di livelock 
> I due processi procedono **in perfetto sincronismo**, passo dopo passo: entrambi annunciano, entrambi vedono la contesa, entrambi rinunciano, entrambi riannunciano, e così via all'infinito. 

## Soluzione Corretta
### Algoritmo di Dekker

La soluzione arriva da un'idea semplice: il quarto tentativo ha il difetto della **simmetria** (i due processi si comportano allo stesso modo e quindi possono ripetere la stessa mossa all'infinito). Serve qualcosa che **rompa la simmetria** quando c'è contesa.

> [!info] Idea
> Si inserisce la variabile `turn` del primo tentativo; questa non decide **chi può entrare**, ma **chi ha il diritto di insistere** quando c'è contesa..

![[Pasted image 20261102162538.png|581]]
**Perché funziona.**
- **Mutua esclusione:** `p` entra solo se vede `wantq = false`, avendo già `wantp = true`.
- **Niente livelock:** quando c'è contesa uno dei due ha il turno e **insiste**, l'altro **cede**: la simmetria è rotta.
- **Niente starvation:** uscendo dalla CS ciascuno passa il turno all'altro, e `turn` non può restare "bloccato" nell'altro perché serve solo in caso di contesa.

**Proprietà in LTL:**
- Mutua esclusione: $\Box \neg (p_8 \wedge q_8)$
- Assenza di starvation: $\Box ((p_2 \rightarrow \Diamond p_8) \wedge (q_2 \rightarrow \Diamond q_8))$

> [!tip] Cosa notare L'algoritmo funziona su **qualsiasi architettura** che fornisca solo `load` e `store` atomici. È un risultato notevole (non servono istruzioni speciali), ma l'algoritmo è lungo e delicato.

### Algoritmo di Peterson

Fonde i due `await` in uno solo** con condizione composta.
![[Pasted image 20261102162837.png|575]]
**Come leggerlo (lato `p`).**
- `p2`: "io voglio entrare".
- `p3`: "**ma ti lascio il turno**" (`turn := 2` significa "tocca a `q`"). È un gesto di cortesia.
- `p4`: "aspetto finché **o** tu non vuoi entrare, **o** il turno è tornato a me".

> [!warning] Le macchine reali di Dekker e Peterson sono corretti con **memoria sequenzialmente consistente**
> I processori moderni e i compilatori **riordinano** letture e scritture per ottimizzare: su hardware reale questi algoritmi, scritti ingenuamente, **possono non funzionare** senza barriere di memoria (in Java, variabili `volatile`). Sono algoritmi di valore didattico e teorico; in pratica si usano i lock del linguaggio.

### Algoritmo del ticket (per N processi)

Per più di due processi si usa un'idea diversa, quella dei **biglietti numerati** (come al banco della salumeria).
![[Pasted image 20261102163038.png|539]]
**Come funziona.**
- Per entrare, ogni processo **prende un biglietto** (`turn[i]`) e incrementa il distributore (`num`).
- Aspetta finché il numero **servito** (`next`) è uguale al proprio biglietto.
- Uscendo, incrementa `next`, **chiamando il biglietto successivo**.

> [!prop] **Proprietà** 
> Poiché i biglietti sono serviti in ordine, l'accesso è **FIFO** (_first come, first served_): niente starvation, attesa limitata.

> [!attention] **Difetti.**
>- Le variabili `num` e `next` crescono **sempre**: prima o poi possono andare in **overflow aritmetico** (si può mitigare con aritmetica modulare).
>- Il passo `p2` (prendi il biglietto **e** incrementa) deve essere **atomico**: gli angoli `< ... >` indicano un'istruzione atomica composta, che richiede un supporto della macchina (come _fetch-and-add_).
## Istruzioni atomiche composte

### Perché servono

L'algoritmo di Dekker funziona usando soltanto `load` e `store` atomici. Ma il problema della sezione critica (e della mutua esclusione in generale) si **semplifica molto** se la macchina offre istruzioni atomiche **più potenti**, direttamente supportate dall'hardware.

La notazione per specificare l'atomicità è mettere le istruzioni tra **parentesi angolari** $\langle \dots \rangle$: il gruppo viene eseguito **senza possibilità di intreccio**.

Le istruzioni composte più comuni:

| Istruzione                                             | Cosa fa (atomicamente)                                                                          |
| ------------------------------------------------------ | ----------------------------------------------------------------------------------------------- |
| **test-and-set**$(x, r)$                               | $\langle r := x;\ x := 1 \rangle$: leggi il vecchio valore di $x$ in $r$ e imposta $x$ a 1      |
| **exchange**$(a, b)$                                   | scambia i valori di $a$ e $b$                                                                   |
| **fetch-and-add**$(x, k)$                              | restituisce il valore di $x$ e lo incrementa di $k$                                             |
| **compare-and-swap**$(x, \text{atteso}, \text{nuovo})$ | se $x$ vale `atteso`, lo sostituisce con `nuovo` e segnala il successo; altrimenti non fa nulla |
### Sezione critica con test-and-set

> [!abstract] Il test-and-set è un’istruzione che **legge e scrive una variabile in un unico passo indivisibile**.

![[Pasted image 20261102171037.png]]
**Come funziona.**
```
test-and-set(x, r):
    < r := x ;  x := 1 >
```
1. **legge** il valore attuale di `x` e lo copia in `r` (il _vecchio_ valore);
2. **imposta** `x` a 1, **sempre**, qualunque cosa ci fosse prima.
##### **Pro e Contro**

**Pro:**
- Funziona per **N processi**

 **Contro:**
- L'attesa attiva **spreca CPU**.
- Non garantisce da sola l'assenza di starvation, nulla stabilisce chi vince quando più processi "provano" insieme.

---
## I lock

> [!abstract] Lock
>Un lock offre due operazioni (entrambe atomiche):
>- **`acquire`**: prendi il lock (se è già preso, aspetta);
>- **`release`**: rilascia il lock.

![[Pasted image 20261102173734.png|519]]
*Il pre-protocollo diventa una sola chiamata `acquire` e il post-protocollo un'altra `release`*
### Atomicità in Java: `synchronized`

In Java il lock è disponibile come **blocco `synchronized`** su un **oggetto lock condiviso**:
![[Pasted image 20261102174614.png]]
Poiché entrambi i blocchi sono protetti **dallo stesso oggetto `lock`**, la sequenza `a, b` e la sequenza `c, d` vengono eseguite **senza intreccio tra le due sequenze**: non possono esistere scenari come `a c b d` o `c a d b`. Sono possibili solo `a b c d` oppure `c d a b`.

> [!warning] Cose da tenere presenti
> 
> - Serve **lo stesso oggetto lock** nei due blocchi
> - Il blocco garantisce che nessun **altro blocco sullo stesso lock** si intrecci, ma il codice **non protetto** può comunque girare in qualsiasi momento (e il thread può essere sospeso dallo scheduler _dentro_ il blocco).
> - Il lock viene rilasciato automaticamente all'uscita dal blocco, anche in caso di eccezione.
> - Usare più lock annidati apre la strada al **deadlock** (ordine di acquisizione diverso tra thread).

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

