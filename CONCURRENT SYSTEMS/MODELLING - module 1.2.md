# Intro

> [!important] Cos'è un **modello**?
> È una rappresentazione semplificata della realtà e, per questo, include soltanto gli aspetti del sistema reale rilevanti per il problema da risolvere.

**Ruoli** principali dei modelli:
- descrivere il comportamento di un sistema
- supportare il **design** di un sistema concorrente
- analizzare il comportamento del sistema e del relativo programma
  
  >[!attention] Key Feature
  > I modelli si fondano su teorie e framework formali e rigorosi che consentono di verificare meccanicamente il rispetto delle proprietà di correttezza richieste al software una volta implementato.
  
Tipologie di approcci ai modelli:
- **Labelled Transition Systems (LTS) and Process Algebra**
- **Petri Nets**

# Labelled Transition Systems (LTS) and Finite State Processes (FSP)

Un **Labelled Transition System (LTS)** è la struttura matematica formale che descrive una macchina a stati in cui i passaggi da uno stato all'altro non avvengono in modo anonimo, ma sono associati a specifiche etichette.

**FSP** è un linguaggio algebrico testuale formale. Serve a descrivere i processi concorrenti in formato testuale; ciascuna descrizione scritta in FSP genera in modo biunivoco e meccanico una corrispondente macchina a stati LTS.


> [!attention] Perchè allora non si modella con le **FSM**?
> Rappresentare le macchine a stati tramite **grafici** limita la complessità dei problemi che si possono affrontare. Di conseguenza, per descrivere i modelli utilizzeremo una notazione testuale denominata Finite State Processes (FSP)
>
>- In termini tecnici, FSP è un algebra di processi (una notazione)

> [!quote] Nella programmazione concorrente quando si parla di *processo* intendiamo l'esecuzione di un programma sequenziale, in cui:
> -  lo *stato* di quel processo è l'insieme dei valori delle variabili
> - lo stato cambia a seconda deli *statement*, ovvero una o più *azioni atomiche*.

---
## 1. LTS formalmente

### Il Transition System Semplice
Prima di aggiungere le etichette, un sistema di transizione base si definisce formalmente come una coppia **$(S, T)$**:
* **$S$** rappresenta l'insieme totale degli stati possibili del sistema.
* **$T$** è la relazione di transizione (ovvero un sottoinsieme del prodotto cartesiano $S \times S$), che definisce quali passaggi da uno stato all'altro sono ammessi.
* Se la coppia $(p, q) \in T$, significa che il sistema può passare direttamente dallo stato $p$ allo stato $q$, evento che si indica simbolicamente con la notazione $p \to q$.

### Il sistema etichettato (LTS)
Per modellare l'esecuzione di azioni specifiche, il sistema si arricchisce inserendo un insieme di etichette. Formamente diventa una tripla **$(S, \Lambda, T)$**:
* **$S$** è l'insieme degli stati.
* **$\Lambda$** è l'insieme di tutte le etichette ammesse nel sistema.
* **$T$** è la relazione di transizione etichettata, definita come sottoinsieme del prodotto cartesiano $S \times \Lambda \times S$.

In questo contesto, la presenza della tripla $(p, \alpha, q) \in T$ indica che il sistema passa dallo stato $p$ allo stato $q$ attraverso l'esecuzione dell'azione etichettata con $\alpha$. La notazione matematica usata è:

$$p \xrightarrow{\alpha} q$$

### Cosa rappresentano le etichette?
A seconda del modello d'interesse, l'etichetta $\alpha$ assume significati operativi differenti:
* Un evento esterno recepito dal sistema.
* Un dato di input atteso.
* Una condizione logica che deve risultare vera per scatenare il cambio di stato.
* Un'azione concreta eseguita dal processo durante la transizione.

---

## 2. Finite State Processes (FSP)
### Convenzioni sintattiche fondamentali
Per distinguere gli elementi all'interno di una specifica FSP, si applica una regola di sintassi basata sui caratteri:
* Le **azioni** si scrivono sempre con la lettera iniziale **minuscola** (es. `a`, `x`, `read`).
* I **processi** si indicano sempre con la lettera iniziale **maiuscola** (es. `P`, `Q`, `BUFFER`).
* Le definizioni terminano con il punto (`.`), mentre la virgola (`,`) permette di definire più processi interconnessi nello stesso blocco.

### Panoramica degli operatori di base
FSP si basa su pochi operatori combinabili per modellare comportamenti complessi:
* **Prefix di azione (`->`):** Lega un'azione al processo successivo.
* **Scelta (`|`):** Modella una decisione deterministica o non deterministica tra più azioni alternative.
* **Composizione parallela (`||`):** Permette l'esecuzione simultanea e concorrente di più processi.
* **Sincronizzazione su azioni condivise:** Due o più processi concorrenti si coordinano eseguendo contemporaneamente le azioni che hanno in comune.

### L'Operatore Action Prefix (`->`) nel dettaglio

#### Regole di funzionamento
* **Sintassi:** Si scrive sempre inserendo un'azione a sinistra dell'operatore `->` e un processo a destra: `(x -> P)`.
* **Significato operativo:** Il processo descritto da `(x -> P)` esegue prima l'azione `x` e, subito dopo averla completata, si trasforma nel processo `P` assumendone il comportamento.

#### Definizione dei processi e Ricorsione

Attraverso questo operatore possiamo definire il ciclo di vita dei processi in diversi modi:

- **Definizione derivata:** Un nuovo processo **Q** viene definito a partire dall'esecuzione dell'azione **x**, seguita dal comportamento espresso dal processo **P**:
    
    `Q = (x -> P).`
    
- **Processi non-terminanti (Ricorsione):** Se un processo richiama se stesso all'interno della propria definizione, si crea un ciclo d'esecuzione infinito. È il modo formale usato in FSP per modellare i sistemi reattivi continui:
    
    `P = (x -> P).`
    
    _(In questo caso il sistema eseguirà l'azione x all'infinito)._
    
- **Ricorsione mutua tra processi:** Più processi possono essere definiti in modo incrociato ed alternato, separando le singole istruzioni con una virgola e chiudendo la sequenza con il punto finale:
    
    `P = (x -> Q),`
    
    `Q = (y -> P).`
    
    _(Il sistema eseguirà l'azione x, si trasformerà nel processo Q eseguendo l'azione y, per poi ritornare al processo P ripetendo il ciclo)._


![[Pasted image 20260914115205.png]]