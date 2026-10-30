# Programmazione concorrente: concetti fondamentali

## Differenza tra Concorrente, parallelo, distribuito

|                        | **CONCORRENTE**                                                         | **PARALLELO**                                                                       | **DISTRIBUITO**                    |
| ---------------------- | ----------------------------------------------------------------------- | ----------------------------------------------------------------------------------- | ---------------------------------- |
| **Cos'è**              | Più attività che si sovrappongono nel tempo e tipicamente interagiscono | Esecuzione che si sovrappone nel tempo perché avviene su processori fisici separati | Processori distribuiti su una rete |
| **Livello**            | Logico / astratto / di programmazione                                   | Fisico                                                                              | Fisico / di rete                   |
| **Focus**              | Organizzazione del programma                                            | Prestazioni                                                                         | Comunicazione tra nodi             |
| **Memoria condivisa?** | Dipende dal modello                                                     | Tipicamente sì                                                                      | No                                 |

> [!tip] Un programma **concorrente** può girare anche su **un solo processore**
>  le attività si alternano (interleaving) e dall'esterno sembrano simultanee. Il parallelismo richiede invece hardware con più unità di calcolo realmente attive nello stesso istante.

> [!question] Che rapporto c'è tra concorrenza e parallelismo?
> la concorrenza **non è** parallelismo, ma lo **rende possibile**: la concorrenza è la gestione di più cose contemporaneamente, il parallelismo è l'esecuzione fisica di più cose contemporaneamente 

## Paradigmi di concorrenza

Esistono tre grandi famiglie di approcci per la gestione della concorrenza:

1. **Programmazione multi-thread**
	- Stato **condiviso** tra le attività.
	- Servono meccanismi di **sincronizzazione** per usarlo correttamente: semafori, monitor.

2. **Programmazione basata su messaggi**
	- **Nessuno stato condiviso**.
	- Le attività interagiscono solo **scambiandosi messaggi**.
	- Può essere **asincrona** (il mittente non aspetta) o **sincrona** (il mittente aspetta, tipo appuntamento).

3. **Programmazione guidata dagli eventi**
	- Il flusso del programma è **determinato dagli eventi**: azioni dell'utente (click, tasti), sensori, messaggi provenienti da altri thread, processi o applicazioni.
	- Tipico delle interfacce grafiche e dei sistemi reattivi.

## Perché programmare in modo concorrente

### 1. Prestazioni

Due benefici distinti:

- **Throughput**: più lavoro svolto per unità di tempo, sfruttando hardware parallelo.
- **Reattività (responsiveness)**: ottimizzare l'interazione tra attività di CPU e di I/O. Mentre un'attività aspetta il disco o la rete, la CPU può fare altro (e un'interfaccia non si "congela").
#### Speedup

Misura quantitativa delle prestazioni:
$$S = \frac{T_1}{T_N}$$
- $T_1$ = tempo dell'algoritmo **sequenziale**
- $T_N$ = tempo dell'algoritmo **parallelo** con $N$ processori

 > [!info] Se $S = 4$ con 4 processori significa che ci metto un quarto del tempo.
#### Legge di Amdahl

Dà il **massimo speedup** ottenibile parallelizzando un sistema:

$$S = \frac{1}{(1-P) + \frac{P}{N}}$$

- $P$ = proporzione del programma che **può** essere parallelizzata
- $(1-P)$ = parte che **non** può esserlo (resta sequenziale)
- $N$ = numero di processori

> [!hint] l'aggiunta di processori accelera solo la parte $P$; la parte $(1-P)$ rimane sempre uguale e diventa il vero limite.

Per $N \to \infty$ il termine $P/N$ tende a 0, quindi:

$$S_{max} = \frac{1}{1-P}$$


> [!example] Esempio Se il 90% del lavoro è parallelizzabile, anche con un milione di processori non andrò mai più veloce di **10 volte**. Il restante 10% sequenziale fa da tappo.
> 
> ![[Pasted image 20261029150903.png]]

> [!abstract] Osservazioni
>- Il caso teorico ideale è $P = 1$: **speedup lineare** ($S = N$).
>- Esistono casi particolari di speedup **super-lineare** ($S > N$), ad esempio quando i dati suddivisi tra più processori stanno meglio nelle cache.

#### Efficienza

Misura **normalizzata** di quanto bene viene usato ogni processore:

$$E = \frac{S}{N}$$

> [!fail] $E = 1$: tutti i processori lavorano a **piena capacità** (caso ideale).
> Nella pratica $E < 1$, perché ci sono parti sequenziali, costi di sincronizzazione, attese ecc.

#### Il problema del collo di bottiglia: la memoria

![[Pasted image 20261029151508.png|508]]
In un sistema multiprocessore a memoria condivisa, tutti i processori accedono alla stessa memoria tramite un **bus**.
- Il bus è un **potenziale collo di bottiglia**: una sola operazione di memoria alla volta.
- Per questo le **cache** (L1 per istruzioni e dati, L2) sono fondamentali: riducono gli accessi alla memoria principale.
 
 > [!attention] Ma più cache significano più copie degli stessi dati: servono **protocolli di coerenza della cache** (cache coherency), sempre più complessi, per garantire che tutti i processori vedano valori consistenti.

### 2. Astrazione

> [!quote] La concorrenza non serve solo ad andare più veloci: è uno **strumento di progettazione**.


Programmi che interagiscono con l'ambiente, controllano più attività e gestiscono più eventi hanno bisogno di un **livello di astrazione adeguato**; gli **oggetti** della programmazione a oggetti da soli **non bastano**.

Costringe a **ripensare**:
    - il modo in cui si risolvono i problemi (algoritmi e strutture dati di base);
    - il modo in cui si progettano i sistemi (nuovo livello di astrazione, diverso tipo di decomposizione, modularizzazione, incapsulamento).

> [!question] Quindi in che modo l'astrazione è un pro di usare sistemi concorrenti?
> Perchè spesso è il modo **più naturale** di descrivere il problema, anche su una macchina con un solo processore dove non si guadagna nulla in velocità
## Il processo come unità di base

> [!abstract] Un **Processo** è in pratica un programma sequenziale in esecuzione.

È **l'unità di base** di un sistema concorrente, con un **singolo flusso di controllo** (thread of control).

> [!question] Cosa significa che il flusso di controllo è **logico**?
> Poichè dice quali istruzioni vanno eseguite e in quale ordine, ma non chi lo esegue (livello fisico)

### **Proprietà fondamentali**

- **Indipendenza dalla velocità (speed independence)**: i processi sono completamente **asincroni** fra loro. Non si può fare **nessuna assunzione** sulla loro velocità relativa.
- **Non-determinismo**: come conseguenza, l'ordine in cui le azioni dei vari processi si intrecciano può cambiare da un'esecuzione all'altra.

> [!attention] La correttezza deve valere per **tutti** gli interleaving possibili.

## L'interazione tra processi

Qualsiasi programma concorrente non banale ha più processi che **devono interagire**. Le interazioni si dividono in tre tipi, a seconda che siano _volute_ e _attese_ oppure no:

|Tipo|Attesa?|Voluta?|
|---|---|---|
|**Cooperazione**|Sì|Sì|
|**Competizione / contesa**|Sì|**No** (ma necessaria)|
|**Interferenza**|**No**|**No**|

### Cooperazione

Interazioni **attese e volute**, che fanno parte della **semantica** del programma. 
La cooperazione può avvenire in DUE MODALITÀ:
##### **1. Comunicazione**
- Realizza un **flusso di informazione** tra processi, di solito tramite **messaggi**.
- Richiede supporti specifici per lo scambio di messaggi.
##### **2. Sincronizzazione**
- Definisce **relazioni temporali** o dipendenze tra processi e tra azioni di processi distinti ("questa azione deve avvenire dopo quella").
- Richiede supporti specifici per lo scambio di **segnali temporali**.

### Competizione (contesa)

Interazioni **attese e necessarie, ma non volute**: nascono dal fatto che più processi vogliono usare le **stesse risorse condivise**.
Individuiamo così 2 CLASSI DI PROBLEMI:

1. **Mutua esclusione**: regolare l'accesso a risorse condivise da parte di processi distinti.
2. **Sezioni critiche**: regolare l'esecuzione concorrente di **blocchi di azioni** da parte di processi distinti (in modo che un solo processo alla volta li esegua).

### Sincronizzazione vs mutua esclusione

Sono concetti **diversi, anche se collegati**. Pensare che "sincronizzazione = mutua esclusione" è una _leggenda metropolitana_, ancora presente in alcuni testi.

| **Aspecto**                 | **Sincronizzazione**                                                                                   | **Mutua esclusione**                          |
| --------------------------- | ------------------------------------------------------------------------------------------------------ | --------------------------------------------- |
| **Cosa definisce**          | Una relazione temporale tra processi                                                                   | Una restrizione sull'accesso a dati condivisi |
| **Esempi**                  | Un'azione deve avvenire prima di un'altra (precedenza); azioni contemporanee; stessa velocità relativa | Un solo processo alla volta tocca un dato     |
| **Servono dati condivisi?** | No, non necessariamente                                                                                | Sì: senza dati condivisi non ha senso         |

> [!info] La mutua esclusione richiede tipicamente una **sincronizzazione implicita**: un processo viene _bloccato_ finché un altro non ha finito.
## Interferenze e race condition

> [!abstract] Un'interferenza è una qualsiasi interazione tra processi che non era prevista e non è voluta. 

Le interferenze sono legate a due tipi di errori di programmazione:
- **cattiva gestione di interazioni attese** (ad esempio una sincronizzazione sbagliata);
- **presenza di interazioni spurie non previste** dal problema.

> [!attention] Sono errori dipendenti dal tempo
> I più noti sono gli **heisenbug**, ovvero bug che cambiano o spariscono quando si cerca di osservarli (ad esempio col debugger, che altera i tempi di esecuzione)

### Race Condition

**Race condition (o race hazard)**: tipo di interferenza in cui due o più processi **accedono e aggiornano concorrentemente risorse condivise**, e il risultato dell'aggiornamento **dipende dall'ordine specifico** con cui avvengono gli accessi.

> [!example] Due thread eseguono `contatore++` su una variabile condivisa. 
> L'operazione è in realtà composta da tre passi: **leggi**, **incrementa**, **scrivi**. Se i passi si intrecciano, entrambi leggono lo stesso valore e uno dei due incrementi va perso.

---
## Situazioni critiche

Errori e interferenze possono portare l'intero sistema in situazioni critiche.

### Deadlock

*(In passato chiamato anche **deadly embrace**, "abbraccio mortale", da Dijkstra.)*

- Due o più processi sono **in attesa l'uno dell'altro** e quindi **nessuno termina mai**.
- Riguarda tipicamente il rilascio di una risorsa bloccata (lock), la ricezione di un segnale temporale o di un messaggio.
- Coinvolge **più processi**.

> [!example] Esempio Il processo A prende il lock 1 e poi chiede il lock 2; il processo B prende il lock 2 e poi chiede il lock 1. Ognuno tiene ciò che serve all'altro e aspetta ciò che l'altro tiene: **stallo**.

### Starvation (o iniquità, unfairness)

- Un processo resta **bloccato in un'attesa infinita**.
- In particolare la _starvation di risorse_: al processo viene negato perennemente l'accesso a ciò che gli serve.
- Coinvolge **un singolo processo**; gli altri possono continuare a lavorare.

### Livelock

- Simile al deadlock, ma gli **stati dei processi cambiano continuamente** l'uno rispetto all'altro, **senza che nessuno progredisca**.

> [!tip] Chiarimento Nel deadlock i processi sono **fermi** (bloccati). Nel livelock sono **attivi ma inconcludenti**, come due persone in un corridoio che si spostano entrambe dallo stesso lato ripetutamente per lasciarsi passare.

|**Concetto**|**Deadlock**|**Starvation**|**Livelock**|
|---|---|---|---|
|**Processi coinvolti**|Più di uno|Uno|Più di uno|
|**I processi...**|Sono bloccati|Sono bloccati indefinitamente|Continuano a cambiare stato|
|**Progresso**|Nessuno|Il processo "affamato" non progredisce|Nessuno|

## Linguaggi e macchine concorrenti

Per scrivere ed eseguire programmi concorrenti servono due cose:

- **Linguaggio di programmazione concorrente**: permette di scrivere un programma come insieme di istruzioni da eseguire concorrentemente.
- **Macchina concorrente**: una macchina (anche **astratta**) progettata per eseguire più processi sequenziali, sfruttando più processori (**fisici o virtuali**).

### Cosa fornisce una macchina concorrente

- Supporto all'esecuzione di programmi concorrenti, realizzando computazioni concorrenti.
- **Tanti processori virtuali quanti sono i processi** della computazione.
- Meccanismi di base per:
    - **multiprogrammazione**;
    - **sincronizzazione e comunicazione**;
    - **controllo degli accessi alle risorse**.

### Meccanismi di base

**Multiprogrammazione**

- Creare nuovi **processori virtuali**.
- Assegnare i **processori fisici** della macchina sottostante ai processori virtuali, tramite **algoritmi di scheduling**.

**Sincronizzazione e comunicazione**: dipendono dal **modello architetturale** della macchina.

|Modello|Memoria|Interazione|Esempio|
|---|---|---|---|
|**Memoria condivisa**|C'è una memoria condivisa tra i processori virtuali|Tramite dati condivisi (con sincronizzazione)|Programmazione multi-thread|
|**Scambio di messaggi** (message passing)|Ogni processore virtuale ha la **propria memoria**, niente memoria condivisa|**Solo** tramite messaggi|Attori, processi con messaggi|

### Dalle macchine ai linguaggi

I linguaggi permettono di scrivere programmi come **insiemi di processi sequenziali** eseguiti concorrentemente sui processori virtuali della macchina. Devono offrire costrutti per:

- **specificare la concorrenza**: creazione di più processi;
- **specificare l'interazione tra processi**: sincronizzazione, comunicazione, mutua esclusione.

### Tre approcci di progettazione dei linguaggi

|Approccio|Idea|Esempi|
|---|---|---|
|**Linguaggio sequenziale + libreria**|La concorrenza è fornita da primitive in una libreria, il linguaggio non cambia|C + PThreads|
|**Linguaggio progettato per la concorrenza**|La concorrenza è parte nativa del linguaggio|OCCAM, Ada, Erlang, Go|
|**Approccio ibrido**|Paradigma sequenziale **esteso** con supporto nativo alla concorrenza, più librerie e pattern costruiti sopra|Java, Scala (con `java.util.concurrent`)|

## La concorrenza nei linguaggi mainstream

La maggior parte dei linguaggi diffusi, sia procedurali (C) sia a oggetti (Java), offre la creazione ed esecuzione di processi **tramite librerie**, senza estendere il linguaggio. Per Java questo è solo in parte vero, perché ha un supporto nativo per la concorrenza.

### Thread

- Il **thread** è l'implementazione concreta della nozione astratta di processo.
- Detti anche **processi leggeri** (lightweight), in contrapposizione ai **processi pesanti** (heavyweight) del sistema operativo.
- Attenzione a non confondere: nei sistemi operativi un **processo** è un programma in esecuzione che può avere **uno o più flussi di controllo (thread)**.

Esempi principali:

- programmazione **multi-thread in Java**;
- **libreria Pthread** per C/C++ su sistemi POSIX.

## Approcci alternativi ai Thread

I thread non sono l'unico modo, e spesso non sono il più comodo. Altri approcci:

|Approccio|Idea in breve|
|---|---|
|**Framework basati su task**|Si descrivono **compiti** da eseguire, e un framework li assegna ai thread (es. Executor di Java)|
|**Programmazione asincrona**|Modelli basati su **event loop**; operazioni lunghe lanciate in background (es. AsyncTask)|
|**Attori**|OOP concorrente: entità con stato **privato** che comunicano solo con messaggi; variante degli _active objects_|
|**Scambio di messaggi sincrono**|Processi che comunicano con messaggi in modo sincrono (rendez-vous)|
|**Programmazione reattiva**|Flussi di dati ed eventi come astrazione centrale (es. Functional Reactive Programming, Reactive Extensions / Rx)|
|**Calcolo parallelo**|Programmazione **GPGPU** (calcolo generico su GPU); architetture e parallelismo per i **Big Data** (es. Lambda Architecture)|
