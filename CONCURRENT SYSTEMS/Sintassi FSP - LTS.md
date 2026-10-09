# Sintassi FSP - LTS

> [!attention] FSP usa una regola sintattica sui nomi:
> Le **Azioni** (gli eventi/transizioni) iniziano sempre con la **lettera minuscola** (es. `on`, `off`, `tick`).
> I **Processi** (gli stati/comportamenti) iniziano sempre con la **lettera MAIUSCOLA** (es. `SWITCH`, `ON`, `STOP`).

## Labelled Transition Systems (LTS) - Definizione Formale

 Definiamo una Transition System **semplice** $(S, T)$ come un sistema di transizioni è una coppia $(S, T)$ dove:
- **$S$**: è l'insieme di tutti i possibili stati (_Set of states_).
- **$T$**: è la relazione di transizione (_Transition relation_), che è un sottoinsieme di $S \times S$.
- _Significato_: Se $(p, q) \in T$, diciamo che c'è una transizione dallo stato $p$ allo stato $q$ (e si scrive $p \rightarrow q$).

> [!important] *Labelled Transition System $(S, A, T)$*
> Aggiunge le etichette per dare un nome alle transizioni: è una tripla $(S, A, T)$ dove:
>- **$S$**: insieme degli stati.
>- **$A$** (o $\Lambda$): insieme delle etichette/azioni (_Set of labels_).
>- **$T$**: relazione di transizione etichettata, sottoinsieme di $S \times A \times S$.
>- _Significato_: Se $(p, \alpha, q) \in T$, c'è una transizione dallo stato $p$ allo stato $q$ con etichetta $\alpha$ (si scrive $p \xrightarrow{\alpha} q$)

## Main Operators

### Action Prefix

L'operatore `->` (prefisso d'azione) indica una sequenza temporale in cui prima avviene l'azione a sinistra, poi il sistema si comporta come il processo a destra.

``` FSP
PROCESSO = (azione -> PROCESSO_SUCCESSIVO).
```
_(Nota: ogni definizione di processo in FSP termina con un punto `.`)_
#### Esempio 1: Processo che termina (`STOP`)

`STOP` è un processo speciale predefinito che rappresenta uno stato finale (nessuna transizione ulteriore).

``` FSP
ONESHOT = (once -> STOP).
```

**Cosa significa?** Il processo `ONESHOT` esegue l'azione `once` e poi si ferma (`STOP`).

![[Pasted image 20260917181019.png]]
#### Esempio 2: Processo Ricorsivo (Ciclo Infinito)

Se definiamo un processo richiamando se stesso, otteniamo un comportamento che si ripete all'infinito:

``` FSP
SWITCH = OFF,
OFF    = (on -> ON),
ON     = (off -> OFF).
```

Oppure in forma contratta (sostituendo le definizioni):
``` FSP
SWITCH = (on -> off -> SWITCH).
```

**Come si legge?**
1. `SWITCH` parte nello stato iniziale (`OFF`).
2. Fa l'azione `on` e va nello stato `ON`.
3. Fa l'azione `off` e torna allo stato iniziale (`SWITCH` / stato `OFF`).

_(nel LTS qui sotto :   OFF = 0 e ON = 1)_
![[Pasted image 20260917180926.png]]

> [!important] Alfabeto del Processo
> L'alfabeto di un processo è l'insieme di tutte le azioni in cui quel processo può impegnarsi
>In FSP, l'alfabeto si ricava raccogliendo tutte le azioni scritte con la minuscola nella sua definizione.
>_Ad esempio il processo `ALARM = ( trigger -> ring -> reset -> ALARM )` avrà come alfabeto `{trigger, ring, reset}`_
### Choice

Il costrutto Scelta (`|`) in FSP definisce che, Se $x$ e $y$ sono azioni, la notazione `(x -> P | y -> Q)` descrive un processo che inizialmente offre la possibilità di eseguire l'azione $x$ oppure l'azione $y$.

Una volta eseguita la prima azione, il comportamento successivo sarà descritto da $P$ (se la prima azione è stata $x$) oppure da $Q$ (se la prima azione è stata $y$)

> [!example] Esempio
>Un distributore automatizzato di bevande che eroga caffè caldo se viene premuto il pulsante rosso, oppure tè freddo se viene premuto il pulsante blu.
>``` FSP
>DRINKS = ( red  -> coffee -> DRINKS
>         | blue -> tea    -> DRINKS
 >        ).
>```
>![[Pasted image 20260918102746.png|291]]


#### Modellare una scelta Non-Deterministica

Il processo `(x -> P | x -> Q)` è detto non-deterministico poiché, a seguito dell'azione $x$, può comportarsi a tutti gli effetti sia come $P$ sia come $Q$.

``` FSP
COIN = ( toss -> heads -> COIN
       | toss -> tails -> COIN
       ).
```
![[Pasted image 20260918104501.png|369]]

### Indexed Processes

Sia le azioni che i processi locali possono essere **indicizzati**.

``` FSP
BUFF = (in[i:0..3] -> out[i] -> BUFF).
```
![[Pasted image 20260918121258.png]]
**Requisito**: Gli intervalli devono sempre essere **finiti** per permettere l'analisi automatica dei modelli.

_si può scrivere in modo più pulito definendo il range_
``` FSP
range T = 0..3

BUFF       = (in[i:T] -> STORE[i]),
STORE[i:T] = (out[i] -> BUFF).
```

> [!important] `STORE[i:T]` 
> è un **processo locale indicizzato**: serve a memorizzare temporaneamente il valore `i` letto prima di eseguire la corrispettiva azione `out[i]`.

> [!question] A cosa serve?
> Possiamo per esempio numerare il ticking
> ``` FSP
> CLOCK = TICKING[0]
> TICKING[t:0..9] = (tick -> TICKING[t+1])
> ```
> ![[Pasted image 20260918121626.png]]

> [!attention] ERRORE
> Come si può vedere l'esempio precedente ritorna un errore dato che non esiste `ticking[10]`. Per riparare servono le *Guardie*

### Processi Parametrizzati e Costanti

I processi possono essere **parametrizzati** per consentire di descriverli in forma generale e poi istanziarli per uno specifico valore numerico.

Ad esempio, anziché fissare rigidamente la dimensione di un buffer a 3, definiamo un processo generale `BUFF` con dimensione $N$

``` FSP
BUFF(N=3) = (in[i:0..N] -> out[i] -> BUFF).
```

In alternativa, se il valore deve essere riutilizzato in più punti del sistema, si può dichiarare una **costante globale**:

``` FSP
const N = 3
BUFF = (in[i:0..N] -> out[i] -> BUFF).
```
### Guard Actions

`(when B x -> P | y -> Q)`
- **Se `B` è vera (`true`)**: sia l'azione `x` che l'azione `y` sono disponibili e possono essere scelte.
- **Se `B` è falsa (`false`)**: l'azione `x` è completamente **bloccata/disabilitata**; il sistema può scegliere soltanto l'azione `y`

> [!idea] Il problema precedente risolto tramite le guardie:
> ```
> CLOCK = TICKING[0],
>TICKING[t:0..9] = ( when (t < 9) tick -> TICKING[t+1]
>                 | when (t == 9) tick -> TICKING[0]
>                  ).
> ```

> [!example] Es. Count Down Timer
> ```
> COUNTDOWN (N=3) = (start->COUNTDOWN[N]), 
> COUNTDOWN[i:0..N] = (when(i>0) tick->COUNTDOWN[i-1] 
> 	|when(i==0) beep->STOP 
> 	|stop->STOP 
> 	).
> ```
> ![[Pasted image 20260918155056.png]]

### Parallel Composition

Se `P` e `Q` sono due processi, `(P || Q)` rappresenta l'esecuzione concorrente di `P` e `Q`

**Modellazione per Interleaving**: L'LTS risultante genera tutte le possibili _interleavings_ delle tracce dei singoli processi costituenti. _Ricorda che la Parallel Composition è un processo a se._

> LTS/FSP non assume un tempo reale, ma modella la concorrenza consentendo alle azioni non condivise dei processi di alternarsi in qualsiasi ordine logico.

![[Pasted image 20260918160817.png|581]]
> [!attention] Nota:
> - i **Processi Primitivi** sono definiti usando _action prefix_ (`->`) e _choice_(`|`)
> - i **Processi Compositi** iniziano con `||` e sono definiti solo tramite _parallel composition_

> [!example] Esempio: CLOCK RADIO
> Dobbiamo modellare una radio sveglia che integra due attività indipendenti, un'orologio che scatta il tempo con un azione continua e una radio che può essere accesa e spenta
> ```
> CLOCK = (tick -> CLOCK)
> RADIO = (on -> off -> RADIO)
> || CLOCK_RADIO = (CLOCK || RADIO)
> ```
> ![[Pasted image 20260921110729.png]]

#### Algebra - Parallel Composition

L'operatore di composizione parallela `||` in FSP definisce un'algebra e soddisfa due leggi algebriche fondamentali:

- **Commutativa** `(P || Q) = (Q || P)` 
- **Associativa** `(P || (Q || R)) = ((P || Q) || R) = (P || Q || R)`

#### Modellare la Concorrenza per Interleaving

> [!question] Abbiamo bisogno di modellare la velocità con cui un processo viene eseguito rispetto a un altro?
> Considerando che la velocità di un processo dipende dai processori e dallo scheduling del SO che non possiamo controllare, stabiliamo semplicemente che i processi si eseguono a velocità relative arbitrarie.

> [!abstract] **Interleaving** (intercalamento)
> L'**interleaving** (in italiano _intercalamento_) è il modello teorico e concettuale con cui rappresentiamo l'esecuzione di più processi concorrenti come un'unica sequenza ordinata di azioni.

Invece di provare a tracciare cosa succede _esattamente nello stesso istante fisico_ su più processori, l'interleaving "appiattisce" il tempo e modella la concorrenza mescolando i singoli passi atomici dei vari processi.

> [!important] La simultaneità è una scelta non-deterministica
> Se due azioni $A$ e $B$ di due processi diversi avvengono contemporaneamente, il modello interleaving le analizza come due casi distinti: prima $A$ poi $B$ ($A \rightarrow B$), oppure prima $B$ poi $A$ ($B \rightarrow A$).
### Interactions & Shared Actions

L'interazione tra processi concorrenti in FSP e LTS viene modellata attraverso le **azioni condivise** (_shared actions_), ovvero azioni che appartengono all'alfabeto di più processi contemporaneamente.

> [!abstract] Un'azione **condivisa** deve invece essere eseguita **simultaneamente e in modo sincrono** da tutti i processi nel cui alfabeto figura quell'azione.
> A differenza delle azioni non confivise che possono essere intercalate (*interleaved*)

##### Esempio di Shared Action

```
BILL = (play -> meet -> STOP).
BEN  = (work -> meet -> STOP).

||BILL_BEN = (BILL || BEN).
```

- `BILL` ha alfabeto `{play, meet}`.
- `BEN` ha alfabeto `{work, meet}`.

L'azione `meet` è condivisa ed agisce da **punto di sincronizzazione**.
- Ovvero, in entrambi i casi, l'azione `meet` può avvenire solo dopo che sia `play` sia `work` sono state eseguite.
![[Pasted image 20261009141705.png|361]]
##### Esempio di Produttore Consumatore

![[Pasted image 20261009141739.png|436]]
`ready` viene usato per sbloccare lo user nel consumare quello che il maker ha prodotto.
#### Handshake (Accoppiamento Forte)

Se il comportamento del modello `MAKER_USER` precedente non è desiderato (ad esempio se vogliamo evitare che il produttore `MAKER` vada avanti a produrre un secondo oggetto prima che il primo sia stato effettivamente consumato), si introduce il meccanismo dell'**Handshake**

```
MAKERv2 = (make -> ready -> used -> MAKERv2).
USERv2  = (ready -> use -> used -> USERv2).

||MAKER_USERv2 = (MAKERv2 || USERv2).
```

![[Pasted image 20261009141803.png]]
#### Multi-Party Synchronisation

La sincronizzazione basata su azioni condivise in FSP non è limitata a soli due processi, ma può coinvolgere un numero arbitrario di processi (**sincronizzazione multi-parte**).

```
MAKE_A   = (makeA -> ready -> used -> MAKE_A).
MAKE_B   = (makeB -> ready -> used -> MAKE_B).
ASSEMBLE = (ready -> assemble -> used -> ASSEMBLE).

||FACTORY = (MAKE_A || MAKE_B || ASSEMBLE).
```
### Process Labelling & Prefix Sets

Quando si desidera istanziare più copie dello stesso processo all'interno di un sistema concorrente, occorre fare attenzione alla condivisione involontaria delle azioni.
#### Process Labelling (Istanze Distinte)

Per creare istanze indipendenti, si utilizza il **Process Labelling** (`etichetta:PROCESSO`), che aggiunge un prefisso all'alfabeto del processo

```
SWITCH = (on -> off -> SWITCH).

||TWO_SWITCH = (a:SWITCH || b:SWITCH).
```

> [!attention] Risultato
> - L'alfabeto di `a:SWITCH` diventa `{a.on, a.off}`.
> 
>- L'alfabeto di `b:SWITCH` diventa `{b.on, b.off}`.

#### Parametrised Composite Processes (forall)

Quando il numero di istanze di un processo è elevato o parametrizzato, si utilizza il costrutto `forall` per comporre in parallelo una serie di processi indicizzati:

```
||SWITCHES(N=3) = (forall[i:1..N] s[i]:SWITCH).
```
o in forma abbreviata
```
||SWITCHES(N=3) = (s[i:1..N]:SWITCH).
```

_(Equivale a comporre `(s[1]:SWITCH || s[2]:SWITCH || ... || s[N]:SWITCH)`)_.
#### Set di Prefissi (Condivisione Risorse e Mutua Esclusione)

L'etichettatura può essere applicata anche specificando un insieme (set) di etichette di prefisso nella forma `{a1..ax}::P`
- Sostituisce ogni etichetta di azione $n$ nell'alfabeto di $P$ con il set di etichette $a1.n, ..., ax.n$.

```
RESOURCE = (acquire -> release -> RESOURCE).
USER     = (acquire -> use -> release -> USER).

||RESOURCE_SHARE = ( a:USER 
                   || b:USER 
                   || {a,b}::RESOURCE 
                   ).
```

![[Pasted image 20261009151414.png]]
> [!attention] Analisi del Comportamento:
> 1. L'alfabeto della risorsa si espande in `{a.acquire, a.release, b.acquire, b.release}`
> 2. Quando l'utente `a` esegue `a.acquire`, la risorsa passa allo stato occupato e offre **solo** l'azione `a.release`.
> 3. L'utente `b` viene bloccato sulla sua `b.acquire` finché `a` non esegue `a.release`

> [!hint] Viene così garantita formalmente la **mutua esclusione** nell'accesso alla risorsa.
### Relabelling (Adattamento Interfacce)

> [!error] Quando si progettano processi indipendenti, ciascuno viene spesso definito con il proprio alfabeto specifico. Nella composizione di un sistema più ampio, potrebbe essere necessario far interagire tali processi traducendo o connettendo i loro nomi di azione per stabilire la sincronizzazione.

##### Esempio di Relabelling

Un client esegue un'operazione mediante `call` e attende con `wait`. Un server offre un servizio rispondendo alle richieste `request` e inviando una risposta `reply`:
```
CLIENT = (call -> wait -> continue -> CLIENT).
SERVER = (request -> service -> reply -> SERVER).

||CLIENT_SERVER = (CLIENT || SERVER)
                  /{call/request, reply/wait}.
```

> [!hint] Effetto del Relabelling
> - Nella descrizione del `SERVER`, l'azione `request` viene sostituita da `call`.
> - Nella descrizione del `CLIENT`, l'azione `wait` viene sostituita da `reply`.
>
>Di conseguenza, `call` e `reply` diventano azioni condivise tra `CLIENT` e `SERVER`, sincronizzando la chiamata e il completamento del servizio.
### Hiding & Silent Actions

In alcuni contesti è necessario rendere alcune azioni di un processo "private", ovvero non accessibili per la sincronizzazione o l'interazione con altri processi esterni.
#### Operatori di Nascondimento (\ e @)

##### Operatore Hiding (\):
**Sintassi**: `\ {a1, ..., ax}`

Rimuove i nomi delle azioni specificate dall'alfabeto del processo P e trasforma tali transizioni in azioni silenti (rappresentate dal simbolo $\tau$ o tau).   
```
USER = (acquire -> use -> release -> USER)\{use}.
```
##### Operatore Interface (@):
**Sintassi**: `@ {a1, ..., ax}`

Espone esclusivamente le azioni indicate nel set e nasconde tutte le altre azioni presenti nell'alfabeto del processo (rendendole silenti $\tau$).  
```
USER = (acquire -> use -> release -> USER)@{acquire, release}.
```

> [!bstract] Proprietà delle Azioni Silenti ($\tau$)
> Le azioni silenti rappresentano eventi interni non osservabili dall'esterno.
> A differenza delle azioni normali, le azioni $\tau$ di processi diversi non si sincronizzano mai tra loro e non possono condizionare l'esecuzione degli altri processi concorrenti. 

![[Pasted image 20261009152724.png]]
#### Minimizzazione dell'LTS

Nascondere le azioni interne consente di applicare algoritmi di **minimizzazione dello stato** per semplificare il modello:

> [!important] **Rimozione delle azioni $\tau$**
> Un'azione silente può essere eliminata dall'LTS fondendo gli stati connessi da essa, a patto che il comportamento osservabile dall'esterno (cioè l'insieme delle tracce pubbliche ammesse) rimanga del tutto invariato.

> [!question] A cosa serve?
> La minimizzazione riduce drasticamente il numero di stati e di transizioni delle macchine a stati complesse, rendendo possibile l'analisi automatica di sistemi di grandi dimensioni (riduzione della complessità nell'analisi delle proprietà).

![[Pasted image 20261009152755.png]]*Esempio di prima **minimizzato***
### Sequential Processes

Sebbene in astratto un processo rappresenti sempre una sequenza di stati e transizioni, FSP distingue concettualmente tre tipi di processi:

1. **PROCESSI LOCALI**: definiscono uno stato all'interno di un processo primitivo.
    
2. **PROCESSI PRIMITIVI**: definiti mediante un insieme di processi locali, prefissi d'azione e scelte. Ad es. `SWITCH = (on -> off -> SWITCH).`
    
3. **PROCESSI COMPOSITI**: usano la composizione parallela, il relabelling e l'hiding per combinare processi primitivi. Ad es. `||TWO_SWITCH = (a:SWITCH || b:SWITCH).`
    
Estendiamo questa definizione introducendo i **processi sequenziali**, ossia processi capaci di **terminare**. Ovvero che arriva a `END`.

> [!question] A cosa serve `END`
> Permette la **composizione sequenziale** `P; Q` significa: _"fai tutto il processo P; quando P arriva a `END`, fai partire Q"_.

#### Contesti Ricorsivi e Costrutto If-Then-Else

La composizione sequenziale può essere impiegata all'interno di definizioni ricorsive ed essere combinata con strutture di controllo condizionali:

```
if <cond> then Q else R
```

In FSP si valuta una condizione booleana: se vera il processo si comporta come `Q`, altrimenti come `R`

#### Composizione Parallela di Processi Sequenziali

> [!abstract] Quando due o più processi sequenziali vengono composti in parallelo (`SP1 || SP2`), la composizione globale termina soltanto quando **tutti** i singoli processi costituenti sono giunti a terminazione (`END`).

##### Esempio

![[Pasted image 20261009154926.png|294]]
Il processo composito `S` eseguirà in interleaving le azioni `a.x` e `b.x` e raggiungerà lo stato finale `END` solo dopo che entrambi i processi avranno completato la propria transizione.
## Petri Nets
### Introduzione e Concetti Base

> [!definition] Definizione
> Le **Reti di Petri** (introdotte da Carl Adam Petri attorno al 1965) costituiscono un modello matematico e grafico per descrivere e analizzare il flusso di informazioni e di controllo in sistemi distribuiti, asincroni e concorrenti.

> [!question] Cosa cambia da **FSP/LTS**?
> Le Reti di Petri non si basano sull'interleaving per modellare la concorrenza, ma rappresentano direttamente le relazioni di dipendenza causale e di indipendenza tra eventi.
#### Struttura Grafica (Posti, Transizioni e Archi)

Una Rete di Petri è rappresentata graficamente come un **grafo orientato bipartito** composto da due tipi distinti di nodi:

1. Posti (Places) $\rightarrow$ Rappresentano le condizioni, gli stati locali o le risorse del sistema. 
		-  i cerchi $(p_1,p_2,...)$
2. Transizioni (Transitions) $\rightarrow$ Rappresentano gli eventi o le azioni che modificano lo stato del sistema.
		- le barre/rettangoli $(t_1,t_2,...)$
![[Pasted image 20261009170354.png|290]]
> [!error] Archi Orientati (Directed Arcs)
> - Connettono nodi di **tipo diverso**
> - **NON** esistono archi diretti tra nodi di stesso tipo

#### Token e Marca (Marking)

### Dinamica e Regole di Esecuzione
#### Abilitazione e Scatto (Firing)
#### Transizioni al Limite (Source e Sink)
#### Archi Pesati

### Modellazione di Sistemi Concorrenti
#### Interpretazioni di Posti e Transizioni
#### Parallelismo e Sincronizzazione
#### Computazione Data-Flow
#### Conflitto, Scelta e Confusione
#### Asincronia e Località
#### Non-Determinismo

### Aspetti Avanzati di Modellazione
#### Eventi Atomici vs Non-Atomici
#### Gerarchie (Astrazione e Raffinamento)

### Estensioni e Strumenti
#### Archi Inibitori (Inhibitor Arcs - Test a Zero)
#### Reti Temporizzate (Timed Nets) e Reti ad Alto Livello (Coloured Nets)
#### Tool per Reti di Petri (PIPE 2, TINA)