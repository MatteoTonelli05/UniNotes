# Modello

> [!important] Cos'è un **modello**?
> È una rappresentazione semplificata della realtà e, per questo, include soltanto gli aspetti del sistema reale rilevanti per il problema da risolvere.

**Ruoli** principali dei modelli:
- descrivere il comportamento di un sistema
- supportare il **design** di un sistema concorrente
- analizzare il comportamento del sistema e del relativo programma
  
  >[!attention] Key Feature
  > I modelli si fondano su teorie e framework formali e rigorosi che consentono di verificare meccanicamente il rispetto delle proprietà di correttezza richieste al software una volta implementato.
  
Tipologie di approcci ai modelli:
1. **Labelled Transition Systems (LTS) and Process Algebra**
2. **Petri Nets**
# Labelled Transition Systems (LTS) and Finite State Processes (FSP)

Un **Labelled Transition System (LTS)** è la struttura matematica formale che descrive una macchina a stati in cui i passaggi da uno stato sono associati a specifiche etichette. 
_(è una macchina a stati finiti)_

**FSP** è un linguaggio algebrico testuale formale. Serve a descrivere i processi concorrenti in formato testuale; ciascuna descrizione scritta in FSP genera in modo biunivoco e meccanico una corrispondente macchina a stati LTS.


> [!attention] Perchè allora non si modella con le **FSM**?
> Rappresentare le macchine a stati tramite **grafici** limita la complessità dei problemi che si possono affrontare. 

> [!quote] Nella programmazione concorrente quando si parla di *processo* intendiamo l'esecuzione di un programma sequenziale, in cui:
> -  lo *stato* di quel processo è l'insieme dei valori delle variabili
> - lo stato cambia a seconda deli *statement*, ovvero una o più *azioni atomiche*.

[[Sintassi FSP - LTS]]

---
