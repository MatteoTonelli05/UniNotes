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

```
PROCESSO = (azione -> PROCESSO_SUCCESSIVO).
```
_(Nota: ogni definizione di processo in FSP termina con un punto `.`)_
#### Esempio 1: Processo che termina (`STOP`)

`STOP` è un processo speciale predefinito che rappresenta uno stato finale (nessuna transizione ulteriore).

```
ONESHOT = (once -> STOP).
```

**Cosa significa?** Il processo `ONESHOT` esegue l'azione `once` e poi si ferma (`STOP`).

![[Pasted image 20260917181019.png]]
#### Esempio 2: Processo Ricorsivo (Ciclo Infinito)

Se definiamo un processo richiamando se stesso, otteniamo un comportamento che si ripete all'infinito:

```
SWITCH = OFF,
OFF    = (on -> ON),
ON     = (off -> OFF).
```

Oppure in forma contratta (sostituendo le definizioni):
```
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

>_Esempio_
>Un distributore automatizzato di bevande che eroga caffè caldo se viene premuto il pulsante rosso, oppure tè freddo se viene premuto il pulsante blu.
>``` FSP
>DRINKS = ( red  -> coffee -> DRINKS
>         | blue -> tea    -> DRINKS
 >        ).
>```
>![[Pasted image 20260918102746.png|291]]


#### Modellare una scelta Non-Deterministica

Il processo `(x -> P | x -> Q)` è detto non-deterministico poiché, a seguito dell'azione $x$, può comportarsi a tutti gli effetti sia come $P$ sia come $Q$.

```
COIN = ( toss -> heads -> COIN
       | toss -> tails -> COIN
       ).
```
![[Pasted image 20260918104501.png|369]]

