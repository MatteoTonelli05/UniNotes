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
* **Relazione con la concorrenza:** Come sottolineato da Rob Pike, *la concorrenza fornisce la struttura che rende possibile sfruttare il parallelismo*.

### Programmazione Distribuita (Livello di Rete)
Si entra nell'ambito dei sistemi distribuiti quando i diversi processori non si trovano più sullo stesso chip o sulla stessa macchina, ma sono dislocati su una rete. In questo scenario vengono a mancare la memoria condivisa e un clock globale comune, introducendo la gestione dei guasti indipendenti dei singoli nodi.

---

## 2. L'Evoluzione Hardware: La "Giungla" delle Architetture

Per capire perché oggi la programmazione concorrente sia diventata indispensabile, bisogna guardare all'evoluzione dell'hardware negli ultimi decenni.

### La fine del "Free Lunch"
Fino al 2005 circa, gli sviluppatori hanno goduto del cosiddetto *Single-Threaded Free Lunch*: bastava attendere l'uscita della CPU successiva per veder girare il proprio programma più velocemente, grazie al costante incremento della frequenza di clock (i GHz).

Tuttavia, attorno al 2005 si è raggiunto il limite fisico della dissipazione termica (*thermal wall*). Da quel momento la frequenza dei singoli core ha smesso di crescere significativamente, e i produttori hanno iniziato a moltiplicare il numero di core sullo stesso chip. Oggi viviamo nell'era della **Hardware Jungle**, caratterizzata da un'enorme varietà di architetture di calcolo.

### Panoramica delle Architetture Fisiche

* **Multicore Standard (Chip Multiprocessors):** Più core indipendenti sullo stesso chip che condividono la memoria RAM e i livelli superiori di cache (come la L3). Un esempio storico è la famiglia Intel Core i7, o i processori ad alte prestazioni AMD Ryzen Threadripper costruiti tramite moduli distinti detti *chiplet* (CCD).
* **Architetture Ibride (P-core ed E-core):** I processori desktop e mobile recenti combinano due tipi di core per bilanciare prestazioni e consumi:
  * **Performance-cores (P-cores):** Core grandi e veloci per compiti pesanti su singolo thread.
  * **Efficient-cores (E-cores):** Core più piccoli che consumano poco, ideali per task in background e per aumentare l'efficienza complessiva.
* **Processori Eterogenei e Coprocessori Specializzati:** Oltre alla CPU tradizionale, i chip moderni integrano componenti focalizzati su compiti specifici:
  * **GPU / GPGPU:** Centinaia o migliaia di core semplici progettati per il calcolo matriciale massivamente parallelo (grafica e intelligenza artificiale).
  * **SoC (System-on-Chip) come Apple Silicon:** Integra in un unico chip CPU, GPU, NPU (unità neurali per l'IA), ISP (elaborazione immagini da fotocamera) e processori di sicurezza (SEP).
* **Hardware nativo per l'IA (Es. Cerebras WSE-3):** Supera i colli di bottiglia del silicio tradizionale utilizzando un intero wafer come unico chip (con 900.000 core e 44 GB di memoria integrata sul silicio), azzerando la lentezza nei trasferimenti dati.
* **Supercomputer e Cluster:** Grandi infrastrutture formate da migliaia di nodi multi-core interconnessi tramite reti dedicate ad altissima velocità e bassissima latenza (es. il supercomputer giapponese Fugaku).
* **Cloud Computing:** Erogazione di capacità computazionale, piattaforme e software sotto forma di servizio misurabile attraverso la rete (SaaS, PaaS, IaaS).

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

## 6. Modellazione e Analisi dei Sistemi Concorrenti

La programmazione concorrente non riguarda solo la scrittura del codice o l'uso delle API di sistema, ma richiede strumenti teorici per progettare e verificare la correttezza dei comportamenti.

* **Modellazione:** Rappresentare il comportamento del sistema astenendosi dai dettagli implementativi. I linguaggi e i formalismi principali sono:
  * **LTS (Labelled Transition Systems)**
  * **Algebre di Processo**
  * **Reti di Petri**
* **Analisi:** Verificare che il sistema soddisfi le proprietà di correttezza formale (es. assenza di *deadlock*, garanzia di *liveness*, rispetto della mutua esclusione).