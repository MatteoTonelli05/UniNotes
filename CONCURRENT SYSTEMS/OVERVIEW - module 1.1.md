# Concurrent Systems — Overview

La **concorsualità** rappresenta uno dei concetti cardine dell'informatica moderna, attraversando ambiti che vanno dai sistemi operativi al calcolo distribuito, fino ai sistemi in tempo reale. 

---

## 1. Concorrenza, Parallelismo e Sistemi Distribuiti

Per comprendere l'architettura dei sistemi moderni, è fondamentale fare una distinzione precisa tra tre livelli concettuali che spesso vengono confusi: **concorrenza**, **parallelismo** e **sistemi distribuiti**. 

### Concorrenza (Livello Logico e di Astrazione)
La concorrenza riguarda la **strutturazione del software**. 
 
> [!quote] Un sistema è concorrente se è composto da molteplici attività computazionali le cui esecuzioni si sovrappongono nel tempo e che potenzialmente interagiscono tra loro.

* **Non richiede hardware dedicato:** Si parla di concorrenza anche quando più processi condividono un singolo processore tramite un'alternanza guidata dal sistema operativo (*interleaving*).
* **Obiettivo principale:** Gestire la complessità, definire modelli di programmazione puliti e modellare il mondo reale (che è intrinsecamente fatto di agenti indipendenti che interagiscono).

### Parallelismo (Livello Fisico e di Prestazioni)
Il parallelismo riguarda **l'esecuzione fisica simultanea** delle istruzioni. 
* **Richiede hardware dedicato:** Può avvenire solo se il sistema dispone di più unità fisiche di elaborazione (più core o più CPU).
* **Obiettivo principale:** Ottimizzare le prestazioni, aumentare il throughput e ridurre i tempi di calcolo di algoritmi complessi.

>[!quote] *La concorrenza fornisce la struttura che rende possibile sfruttare il parallelismo*.

### Programmazione Distribuita (Livello di Rete)
Si entra nell'ambito dei sistemi distribuiti quando i diversi processori non si trovano più sullo stesso chip o sulla stessa macchina, ma sono dislocati su una rete. In questo scenario vengono a mancare la memoria condivisa e un clock globale comune, introducendo la gestione dei guasti indipendenti dei singoli nodi.

---

## {orange}2. L'Evoluzione Hardware: La "Giungla" delle Architetture

Per capire perché oggi la programmazione concorrente sia diventata indispensabile, bisogna guardare all'evoluzione dell'hardware negli ultimi decenni.

### La fine del "Free Lunch"
Fino al 2005 circa, gli sviluppatori hanno goduto del cosiddetto *Single-Threaded Free Lunch*: bastava attendere l'uscita della CPU successiva per veder girare il proprio programma più velocemente, grazie al costante incremento della frequenza di clock (i GHz).

Tuttavia, attorno al 2005 si è raggiunto il limite fisico della dissipazione termica (*thermal wall*). Da quel momento la frequenza dei singoli core ha smesso di crescere significativamente, e i produttori hanno iniziato a moltiplicare il numero di core sullo stesso chip. 

Oggi viviamo nell'era della **Hardware Jungle**, caratterizzata da un'enorme varietà di architetture di calcolo.

### Panoramica delle Architetture Fisiche

| **Categoria**                                   | **Descrizione**                                                                                    | **Caratteristiche Chiave**                                                                                                                                                                                                             | **Esempi / Modelli**                                                                                                      |
| ----------------------------------------------- | -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| **Multicore Standard** _(Chip Multiprocessors)_ | Più core indipendenti sullo stesso chip che condividono RAM e livelli superiori di cache (es. L3). | Memoria condivisa; architettura a chiplet (CCD) nei sistemi ad alte prestazioni.                                                                                                                                                       | Intel Core i7, AMD Ryzen Threadripper                                                                                     |
| **Architetture Ibride**                         | Combinazione di core differenti per bilanciare prestazioni consumi energetici.                     | • **P-core**: Grandi e veloci per task single-thread pesanti.<br><br>• **E-core**: Piccoli ed efficienti per task in background.                                                                                                       | Processori desktop e mobile recenti (es. Intel Alder Lake e successivi)                                                   |
| **Processori Eterogenei & Coprocessori**        | Chip che integrano componenti dedicati a compiti specifici oltre alla CPU.                         | • **GPU / GPGPU**: Calcolo matriciale massivamente parallelo.<br><br>• **SoC**: CPU, GPU, NPU, ISP e SEP in un unico chip.<br><br>• **Hardware IA Nativo**: Wafer integrato per eliminare i colli di bottiglia nei trasferimenti dati. | • GPU/GPGPU<br><br>  <br><br>• Apple Silicon (M-series)<br><br>  <br><br>• Cerebras WSE-3 (900k core, 44 GB SRAM on-chip) |
| **Supercomputer & Cluster**                     | Infrastrutture su larga scala per il calcolo ad altissime prestazioni.                             | Migliaia di nodi multicore interconnessi tramite reti dedicate ad altissima velocità e bassissima latenza.                                                                                                                             | Fugaku (Giappone)                                                                                                         |
| **Cloud Computing**                             | Erogazione di risorse computazionali e software tramite rete.                                      | Servizio misurabile e scalabile erogato in diverse modalità (SaaS, PaaS, IaaS).                                                                                                                                                        | Piattaforme Cloud (AWS, Azure, GCP, ecc.)                                                                                 |

---

## 3. Classificazione delle Architetture: La Tassonomia di Flynn

Per classificare tutti questi sistemi di calcolo, si utilizza la **Tassonomia di Flynn**, che suddivide le architetture in base alla combinazione dei flussi di istruzioni (*Instruction Stream*) e di dati (*Data Stream*):

1. **SISD (Single Instruction, Single Data):** Il classico modello Von Neumann monocore. Un'istruzione alla volta elabora un dato alla volta.
2. **SIMD (Single Instruction, Multiple Data):** Una singola istruzione viene inviata a più unità che la applicano contemporaneamente su dati diversi. È la base del parallelismo delle GPU e dei processori vettoriali.
3. **MISD (Multiple Instruction, Single Data):** Più istruzioni lavorano sullo stesso dato. Modello prevalentemente teorico (usato raramente se non per sistemi ultra-ridondanti di sicurezza).
4. **MIMD (Multiple Instruction, Multiple Data):** Più processori eseguono istruzioni diverse su dati diversi. È la categoria che comprende la maggior parte dei sistemi concorrenti e paralleli moderni.

### Sottocategorie del Modello MIMD

Il modello MIMD si articola ulteriormente in base al modo in cui viene gestita la **memoria**:

* **MIMD a Memoria Condivisa (Shared Memory):** Tutti i processori condividono uno spazio di indirizzamento comune e comunicano leggendo e scrivendo su variabili condivise.
	  * **SMP (Symmetric Multi-Processing):** Tutti i processori accedono a qualsiasi punto della memoria con la stessa velocità.
	  * **NUMA (Non-Uniform Memory Access):** La memoria è condivisa, ma alcuni blocchi di memoria sono fisicamente più vicini a determinati processori, rendendo l'accesso ad essi più rapido rispetto ad altri.
<br>
* **MIMD a Memoria Distribuita (Distributed Memory):** Ogni processore ha il proprio spazio di memoria privato e la comunicazione avviene esclusivamente tramite lo scambio di messaggi.
	  * **MPP (Massively Parallel Processors):** Migliaia di processori fortemente accoppiati da reti fisiche speciali (usati nell'HPC).
	  * **Cluster:** Computer commerciali collegati tra loro tramite reti standard (es. Beowulf cluster).
	  * **Grid:** Risorse eterogenee distribuite geograficamente su reti LAN/WAN senza un controllo amministrativo centrale.

---

## 4. Classificazione Concettuale dei Sistemi Concorrenti

Oltre all'hardware, è necessario analizzare la concorrenza dal punto di vista dell'ambiente, della struttura del software e delle interazioni.

### Sistema + Ambiente: Trasformativi vs Reattivi
I sistemi informatici non lavorano tutti allo stesso modo rispetto all'ambiente esterno:
* **Sistemi Trasformativi:** Prendono un input, eseguono una trasformazione computazionale (formalizzabile come una Macchina di Turing) e producono un output terminando l'esecuzione.
* **Sistemi Reattivi:** Computano reagendo continuamente a stimoli asincroni provenienti dall'ambiente esterno (es. interfacce utente, sistemi di controllo, server web). In questi sistemi la non-terminazione è una proprietà fondamentale.

### Struttura Interna del Sistema
Dal punto di vista architetturale, un sistema concorrente può essere descritto secondo tre dimensioni principali:
* **Strutturale:** Insieme di componenti attivi (che incapsulano un flusso di controllo) e componenti passivi.
* **Comportamentale:** Insieme di *processi*, ovvero sequenze di azioni che possono essere potenzialmente infinite.
* **Interattiva:** La modalità con cui i processi interagiscono, che può avvenire tramite memoria condivisa (componenti passivi), scambio di messaggi, o produzione/consumo di eventi.

---

## 5. Mappa concettuale della Programmazione Concorrente

Possiamo immaginare i vari approcci alla programmazione concorrente come collocati in uno spazio multidimensionale (*ipercubo della concorrenza*), definito da quattro assi principali:

1. **Flusso di controllo:** 
   * *Sincrono* (es. multithreading classico).
   * *Asincrono* (es. event-loop, callback, future/promises).
2. **Modello di Interazione:** 
   * *Memoria Condivisa* (mutua esclusione, lock, semafori).
   * *Scambio di Messaggi* (che può essere asincrono come nel modello ad Attori, oppure sincrono/basato su canali).
3. **Distribuzione:** 
   * *Locale* (un solo nodo).
   * *Distribuito* (più nodi in rete).
4. **Orientamento:** 
   * *Orientato al controllo* (gestione esplicita dei thread e dei flussi).
   * *Orientato ai dati* (programmazione reattiva e data-flow).

---
