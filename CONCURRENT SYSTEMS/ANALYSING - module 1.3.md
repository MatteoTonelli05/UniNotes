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

##### 1. Safety Properties

- Asseriscono che **"qualcosa di male non deve mai accadere"**
- Deve valere **in ogni singolo stato** dell'LTS.
- Spesso vengono specificate in forma negativa: definiscono uno stato sgradito/errato che non deve essere mai raggiunto.

_Esempio: assenza di deadlock, Mutua Esclusione _
##### 2. Liveness Properties

-
### Specificare Proprietà in FSP

## Supporto Ad-Hoc in FSP
### Property Processes (Safety)
#### Funzionamento, Stato ERROR e Requisito di Determinismo
### Progress Properties (Liveness)
#### Sintassi `progress` e Assenza di Starvation
#### Esempio di Violazione del Progress

## Supporto Logico Generale: Fluents e LTL
### Introduzione alle Logiche Temporali
### Linear Temporal Logic (LTL) e Operatori di Base
#### Operatore Always (`[]` / `G`)
#### Operatore Eventually (`<>` / `F`)
### Proprietà Fondamentali e Deduzione Temporale

## Fluents in FSP
### Definizione e Sintassi dei Fluents
### Families of Fluents (Indicizzati)
### Action Fluents
### Espressioni Fluent e Operatori Logici
### Espressioni `forall` ed `exists`

## Model Checking in FSP tramite FLTL (Fluent LTL)
### Caso di Studio: Il Problema della Sezione Critica (Critical Section)
### Verifica della Safety (Mutua Esclusione con `[]`)
### Individuazione delle Violazioni con LTSA
### Risoluzione del Problema tramite Lock
### Verifica della Liveness (Assenza di Starvation con `<>`)

## Altri Operatori della Fluent Linear Temporal Logic (FLTL)
### Operatore Until (`U`)
#### Specificare Safety e Liveness con `Until`
#### Mutex tramite `Until`
### Operatore Weak Until (`W`)
### Equivalenze Algebriche tra Operatori Temporali
### Espressioni "Rigid" (`rigid(1)` e `rigid(0)`)