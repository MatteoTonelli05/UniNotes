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
> 
> ```
> ![[Pasted image 20260921110729.png]]

