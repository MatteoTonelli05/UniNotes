# Analysing Concurrent Systems

## Analisi dei Sistemi: Verification vs Testing

Nello sviluppo del software, specialmente concorrente, esistono diverse attività di analisi del sistema con obiettivi differenti:

1. **Testing** $\quad \rightarrow \quad$ Attività volta a **scovare i malfunzionamenti** eseguendo il sistema con determinati input.
2.  **Verification** $\quad \rightarrow \quad$Controllare se il sistema è conforme alle sue specifiche formali
3.  **Validation** $\quad \rightarrow \quad$Controllare se il sistema soddisfa le reali aspettative dell'utente/cliente
### Definizioni: Fault, Error e Failure

| Error                                                                           | Fault                                                                                    | Failure                                                                                             |
| ------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| Un'azione umana <br>errata commessa durante la <br>progettazione o la codifica. | La manifestazione o <br>presenza dell'errore <br>all'interno del codice<br>o del sistema | L'evento visibile in cui<br>il sistema devia dal<br>comportamento atteso <br>a causa di un _fault_. |
> [!abstract] Un singolo _fault_ può causare molteplici _failures_, e una _failure_ può essere causata da più _faults_ combinati
### Testing di Safety e Liveness

I test tradizionali su sistemi software si dividono principalmente in due categorie:

1. **Test di Safety**: Verificano che il comportamento del sistema rispetti le specifiche 
2. **Test di Liveness**: Verificano che il sistema compia progressi (_progress / non-progress tests_). Sono molto più difficili da configurare ed eseguire tramite test classici.

> [!error] Bisogna stare attenti a controllare la totalità del sistema
## Metodi Formali per la Verifica

> [!question] Cosa cambia tra verifica e testing?
> - il **Testing** dimostra che una proprietà vale per alcuni scenari selezionati, trova il bug, ma non assicura che il sistema sia privo di difetti.
> - la **Verifica** dimostra che una proprietà valga per TUTTI gli scenari e garantisce la correttezza matematica del modello rispetto alla specifica.
### Approcci Principali: Model Checking e Prove Induttive

#### Model Checking

> [!question] Cosa fa?
> Verifica il sistema generando uno per uno tutti gli stati del sistema e controllando le proprietà stato per stato.

_viene automatizzato da strumenti di **model checker**, i quali se analizza un proprietà violata genera un controesempio che porta all'errore_

#### Prove induttive di invarianti

Le proprietà invarianti vengono dimostrate per induzione sugli stati del sistema, può essere automatizzato da strumenti chiamati sistemi deduttivi.

> [!important] Le proprietà invarianti sono quelle proprietà che non cambiano mai a partire dall'avvio del sistema
### Il Problema dell'Esplosione dello Spazio degli Stati (State-Space Explosion)

> [!error] Dovendo analizzare tutte le casistiche c'è il problema dell'esplosione dello spazio degli stati, per cui si può utilizzare:
> - RIDUZIONE DEGLI STATI: limitando il dominio dei valori
> - COSTRUZIONE INCREMENTALE: esplorare solo gli stati effettivamente raggiungibili dall'esecuzione
> - SYMBOLIC MODEL CHECKING: lavorare con insiemi/rappresentazioni simboliche di stati
> 
## Specificare le Proprietà dei Sistemi

> [!quote] Una **proprietà** è un attributo o un requisito del sistema che deve risultare **vero per ogni possibile traccia / scenario di esecuzione**.
### Proprietà di Safety e Proprietà di Liveness

Le proprietà di correttezza nei sistemi concorrenti si dividono in **due grandi categorie**:

#### 1. Safety Properties

- Asseriscono che **"qualcosa di male non deve mai accadere"**
- Deve valere **in ogni singolo stato** dell'LTS.
- Spesso vengono specificate in forma negativa: definiscono uno stato sgradito/errato che non deve essere mai raggiunto.

_Esempio: assenza di deadlock, Mutua Esclusione _
#### 2. Liveness Properties

- Asseriscono che **"qualcosa di buono prima o poi deve accadere"**
- In ogni traccia di esecuzione, il sistema deve prima o poi raggiungere uno stato in cui la condizione è vera.

_Esempio: Assenza di starvation (prima o poi un utente otterrà la risorsa richiesta)_
### Specificare Proprietà in FSP

FSP offre due approcci per definire e verificare le proprietà:

- **Supporto Ad-Hoc (Costrutti nativi specifici)**:
    1. **Property Processes**: usati per le proprietà di _Safety_.
    2. **Progress Properties**: usati per le proprietà di _Liveness / Progress_.
        
- **Supporto Logico Generale**:
    - Uso dei **Fluents** e della **Linear Temporal Logic (FLTL)** 

## Supporto Ad-Hoc in FSP
### Property Processes (Safety)

> [!abstract] Si definisce un normale processo FSP preceduto dalla parola chiave `property`

![[Pasted image 20261012171937.png|512]]
> [!question] Come funziona?
> Un _property process_ definisce un processo **deterministico** che descrive esclusivamente le tracce ammesse / corrette.
>
>Se il sistema tenta di eseguire un'azione presente nell'alfabeto della proprietà ma **non prevista** dalla sequenza ammessa, la transizione porta automaticamente allo stato speciale **`ERROR`** (`-1`)

#### Requisiti dei Property Processes

1. **Trasparenza** $\quad \rightarrow \quad$ La composizione di un _property process_ **non deve alterare** il normale funzionamento del sistema; serve solo da "osservatore/guardiano".
2. **Determinismo** $\quad \rightarrow \quad$a definizione della proprietà **non deve contenere scelte non-deterministiche** (altrimenti la verifica fallisce o diventa ambigua)
3. **Alphabet Extension** $\quad \rightarrow \quad$ È possibile estendere esplicitamente l'alfabeto della proprietà per catturare eventicatastrofici che non devono mai verificarsi
![[Pasted image 20261012172804.png]]
### Progress Properties (Liveness)

> [!abstract] Si definisce utilizzando la parola chiave `progress`

![[Pasted image 20261012172946.png|415]]

> [!question] Cosa fa?
>  Assertisce che in **qualsiasi esecuzione infinita** del sistema, **almeno una** delle azioni presenti nel set `{a1, a2, ..., an}` verrà eseguita **infinite volte** (_infinitely often_).

![[Pasted image 20261012173035.png]]
## Supporto Logico Generale: Fluents e LTL

Il supporto ad-hoc visto finora (`property` e `progress`) ha limiti di espressività. Per esprimere proprietà arbitrarie e complesse di Safety e Liveness in modo unico e flessibile, si ricorre alla **Linear Temporal Logic (LTL)** combinata con il concetto di **Fluent**.
### Linear Temporal Logic (LTL) e Operatori di Base

> [!abstract] Le Linear Temporal Logic (LTL) servono semplicemente a scrivere formule matematiche che descrivono **come le cose cambiano lungo il tempo (lungo la traccia degli stati)**.  

_In **LTL** (Linear Temporal Logic), il tempo viene visto come una linea retta di momenti/stati che si succedono (Stato 0 $\rightarrow$ Stato 1 $\rightarrow$ Stato 2 $\rightarrow$ ...)._
#### Operatore Always (${\small \square}/G$)

>La formula `[] p` è vera in uno stato $s$ se e solo se la proprietà $p$ è vera nello stato $s$ e **in tutti gli stati futuri** lungo l'esecuzione.

_Esempio: proprietà di Safety_
#### Operatore Eventually ($\diamond/F$)

> La formula `<> p` è vera in uno stato $s$ se e solo se la proprietà $p$ è vera in $s$ oppure **in almeno uno stato futuro** $s'$ lungo l'esecuzione.

_Esempio: proprietà di Liveness_
### Proprietà Fondamentali e Deduzione Temporale

Possiamo definire una **Dualità tra Always ed Eventually**:$$\text{NOT } (\diamond p) \equiv {\small \square} (\text{NOT } p)$$$$\text{NOT } ({\small \square} p) \equiv \diamond (\text{NOT } p)$$
    _(Dire che "non è mai vero che $p$ accadrà" equivale a dire che "sempre non vale $p$")_.
    
E possiamo definire anche **Sequenze di Operatori Composti**:
- ${\small \square} \diamond p$ (_Always Eventually_): $p$ accade infinite volte lungo la traccia (esprime un requisito di **progresso/infinità**).
- $\diamond\text{ }{\small \square}\text{ }p$ (_Eventually Always_): Da un certo istante in poi, $p$ rimarrà vera per sempre (esprime la **stabilizzazione**).
## Fluents in FSP

Per applicare LTL all'algebra dei processi FSP (dove le transizioni sono descritte da tracce di azioni), occorre un ponte tra le azioni del modello e le proposizioni logiche. Questo ponte è rappresentato dai **Fluents**.
### Definizione e Sintassi dei Fluents

> [!abstract] Un **Fluent** è una condizione booleana astratta (una proposizione) il cui valore di verità cambia nel tempo al verificarsi delle azioni.

``` FSP
fluent FL = <{s1, ..., sn}, {e1, ..., en}> initially B
```

- `{s1, ..., sn}`: **Azioni Iniziatrici** (_initiating actions_). Quando si verifica una qualsiasi di queste azioni, il fluent `FL` diventa **`True`**.
- `{e1, ..., en}`: **Azioni Terminatrici** (_terminating actions_). Quando si verifica una qualsiasi di queste azioni, il fluent `FL` diventa **`False`**.
- `initially B`: Valore booleano iniziale (`True` o `False`). Se omesso, di default è `False`.
- _Vincolo_: Un'azione non può essere contemporaneamente sia iniziatrice che terminatrice per lo stesso fluent.
    
### Families of Fluents (Indicizzati)

I fluents possono essere definiti per famiglie di indici esplicite, analogamente ai processi:

``` FSP
fluent LIGHT[i:1..2] = <{on[i]}, {off[i], power_cut}>
```

_(Corrisponde a definire due distinti fluents: `LIGHT_1` per l'indice 1 e `LIGHT_2` per l'indice 2)_.
``` FSP
fluent LIGHT = <{on}, {off, power_cut}> initially False
```
### Action Fluents

Esistono fluents speciali definiti direttamente sulle singole azioni per indicare l'evento istantaneo: _"l'azione è appena stata eseguita"_.

- **Semantica**: Un **Action Fluent** porta lo stesso nome dell'azione. Diventa `True` nell'istante esatto in cui l'azione avviene e torna immediatamente `False` all'azione successiva (compresa la transizione silente $\tau$).
### Espressioni Fluent e Operatori Logici
I fluents (sia espliciti che action fluents) possono essere combinati con gli ordinari operatori booleani:

- `&&` (congiunzione / AND)
- `||` (disgiunzione / OR)
- `!` (negazione / NOT)
- `<->` (equivalenza)
- `->` (implicazione: $(A \rightarrow B) \equiv (!A \vert{}\vert{} B)$)
 
**Esempio di Implicazione**:
``` FSP
fluent POWER = <power_on, power_off>
fluent LIGHT = <on, off>

LIGHT -> POWER
```

_(Esprime la condizione: "Se la luce è accesa (`LIGHT` è `True`), allora la corrente deve essere attiva (`POWER` è `True`)"_).
### Espressioni `forall` ed `exists`

Per valutare espressioni booleane su insiemi di fluents indicizzati si utilizzano i quantificatori:

- **`forall` (AND generalizzato)**:

``` FSP
  forall [i:1..2] LIGHT[i]   // Equivale a: LIGHT[1] && LIGHT[2]
```

- **`exists` (OR generalizzato)**:
    
``` FSP
exists [i:1..2] LIGHT[i]   // Equivale a: LIGHT[1] || LIGHT[2]
````

## Model Checking in FSP tramite FLTL (Fluent LTL)

### Caso di Studio: Il Problema della Sezione Critica (Critical Section)

Consideriamo un sistema con due processi (`USER a` e `USER b`) che eseguono codice non critico (`ncs`) e vogliono accedere a una risorsa/sezione critica condivisa (`cs`).

``` FSP
USER    = (ncs -> enterCS -> cs -> exitCS -> USER).
||USERS = (a:USER || b:USER).
```

Se eseguiamo questo sistema senza alcun controllo, la proprietà di **Mutua Esclusione viene violata**: esistono scenari di esecuzione (tracce) in cui sia `a` che `b` si trovano contemporaneamente all'interno di `cs`.

### Verifica della Safety (Mutua Esclusione con `[]`)

Per verificare formalmente la violazione con il Model Checker dell'LTSA, procediamo in due passi:

1. **Definiamo gli stati astratti d'interesse mediante i Fluents**:

``` FSP
fluent USER_A_IN_CS = <a.enterCS, a.exitCS>
fluent USER_B_IN_CS = <b.enterCS, b.exitCS>
```

- `USER_A_IN_CS` è `True` tra l'azione `a.enterCS` e `a.exitCS`.
- `USER_B_IN_CS` è `True` tra l'azione `b.enterCS` e `b.exitCS`.

2. **Scriviamo la proprietà di Safety in FLTL usando l'operatore Always `[]`**:

```
assert MUTEX = [] !(USER_A_IN_CS && USER_B_IN_CS)
```
    
_(Significa: "In ogni singolo stato, non deve MAI accadere che sia A che B siano contemporaneamente nella sezione critica")_.

### Individuazione delle Violazioni con LTSA

Eseguendo la verifica (_Check LTL property_) all'interno dell'LTSA, il tool esplora lo spazio degli stati e **individua la violazione**, mostrando la traccia esatta del controesempio (_counterexample_):

```
Trace to property violation in MUTEX:
  a.ncs
  a.enterCS       --> USER_A_IN_CS diventa True
  b.ncs
  b.enterCS       --> USER_B_IN_CS diventa True (MUTEX violata!)
```

### Risoluzione del Problema tramite Lock

Per garantire la mutua esclusione, introduciamo un processo condiviso `LOCK` che funge da semaforo/mutua esclusione e obbliga i processi ad acquisire il lock prima di entrare in `cs` e a rilasciarlo all'uscita:

``` FSP
USER = (ncs -> acquire -> enterCS -> cs -> exitCS -> release -> USER).
LOCK = (acquire -> release -> LOCK).

||USERS = (a:USER || b:USER || {a,b}::LOCK).

fluent USER_A_IN_CS = <a.enterCS, a.exitCS>
fluent USER_B_IN_CS = <b.enterCS, b.exitCS>

assert MUTEX = [] !(USER_A_IN_CS && USER_B_IN_CS)
```

- **Risultato del Model Checking**: Con l'introduzione del `LOCK`, la proprietà `MUTEX` viene **verificata con successo** per tutti i possibili scenari.

### Verifica della Liveness (Assenza di Starvation con `<>`)

Oltre alla Safety, dobbiamo assicurarci che il sistema rispetti la **Liveness**: se un utente richiede di entrare in sezione critica (`a.acquire`), prima o poi gli verrà concesso l'accesso (`a.enterCS`).

Esprimiamo l'assenza di starvation tramite l'operatore Eventually `<>`:

``` FSP
assert NO_STARV = [] (a.acquire -> <> a.enterCS)
```

_(Si legge: "È SEMPRE vero (`[]`) che se si verifica l'azione `a.acquire`, da quel momento PRIMA O POI (`<>`) si verificherà l'azione `a.enterCS`")_.

## Altri Operatori della Fluent Linear Temporal Logic (FLTL)

Oltre ad `Always` (`[]`) ed `Eventually` (`<>`), FLTL mette a disposizione altri operatori temporali per esprimere relazioni di precedenza:

### Operatore Until (`U`)

- **Semantica**: La formula `p U q` è vera se $q$ diventa vera nello stato attuale o in un istante futuro, e $p$ rimane continuamente vera **fino a quell'istante**.
    
- **Esempio (Polite - Bussare prima di entrare)**:
    
``` FSP
assert POLITE = (!enter U knock)
```

_(Modella sia la Safety "non si può entrare prima di bussare", sia la Liveness "l'azione knock deve prima o poi verificarsi")_.

#### Mutex tramite `Until` (Uso di soli Action Fluents)

Utilizzando `Until`, la mutua esclusione può essere definita direttamente sugli action fluents senza creare fluents espliciti:

``` FSP
assert MUTEX_U = [] ((a.enterCS -> (!b.enterCS U a.exitCS)) &&
                     (b.enterCS -> (!a.enterCS U b.exitCS)))
```

_(Garantisce sia la mutua esclusione sia la liveness che, se un processo entra, prima o poi uscirà)_.

### Operatore Weak Until (`W`)

- **Semantica**: La formula `p W q` è vera se $p$ vale fino a quando non diventa vera $q$, oppure se $p$ rimane vera **indefinitamente** (senza l'obbligo che $q$ debba per forza accadere).
    
- **Differenza con `Until`**: Il `Weak Until` rimuove il requisito di liveness (non obbliga $q$ ad accadere).
    
- **Esempio**:

``` FSP
assert POLITE_W = (!enter W knock)
```
    
_(Diventa una pura proprietà di Safety: non richiede che si bussi per forza, ma garantisce che se non si bussa non si può entrare)_.

### Equivalenze Algebriche tra Operatori Temporali

Tutti gli operatori temporali visti possono essere derivati algebricamente dall'operatore `Until` (`U`):

- `<> p` $\equiv \text{True } U \text{ p}$
    
- `[] p` $\equiv ! (<> !p)$
    
- `p W q` $\equiv []p \text{ } \vert{}\vert{} \text{ } (p \text{ } U \text{ } q)$

### Espressioni "Rigid" (`rigid(1)` e `rigid(0)`)

In FLTL non esistono le costanti esplicite `True` e `False` come simboli atomici. Le espressioni booleane costanti vengono definite come **espressioni rigide** (_rigid expressions_), cioè espressioni il cui valore non cambia con il passare del tempo / l'esecuzione delle azioni:

``` FSP
assert True  = rigid(1)
assert False = rigid(0)
```