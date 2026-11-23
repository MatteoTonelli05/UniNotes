## Legge di Martec

> [!quote] Il problema sta nel fatto che la tecnologia evolve esponenzialmente mentre le persone si evolvono in scala logaritmica, per cui c'è un'avversione al cambiamento
> 
![[Pasted image 20261123152415.png]]

> [!abstract] Legge di Moore
> Il numero di transistor su un chip di silicio raddoppia circa ogni due anni. Questo dimostra come la potenza di calcolo cresca esponenzialmente nel tempo mentre i costi diminuiscono.

> [!abstract] Law of Accelerating Returns
> Il progresso tecnologico accelera in modo esponenziale anziché lineare perché ogni nuova innovazione fornisce gli strumenti per sviluppare le innovazioni successive a un ritmo sempre più rapido (sistemi auto-amplificanti e convergenza tra diverse tecnologie come AI, biologia e nanotecnologia).

---
## Ciclo dell'innovazione

![[Pasted image 20261123164006.png]]
Solitamente nelle aziende occidentali il ciclo ha la tempistica dell'anno

- **1. Pianificazione:** Si formalizzano le strategie, si validano i progetti selezionati e si assegnano:
  - priorità di intervento;
  - risorse, budget e valore economico;
  - tempistiche e responsabilità.
        
- **2. Attuazione (o Realizzazione):** È la fase operativa più lunga (spesso pluriennale per i progetti strategici), in cui si eseguono gli studi di fattibilità, si acquisiscono/sviluppano i sistemi dal mercato e si erogano i servizi.
    
- **3. Verifica:** Si raccolgono dati e misure per monitorare e diagnosticare lo stato attuale (**as-is**) di processi, risorse informative e sistemi informatici (efficacia, efficienza, qualità e costi), individuando eventuali criticità.
    
- **4. Intervento:** Sulla base della diagnosi precedente, si progettano e avviano le azioni correttive (**to-be**), ovvero la reingegnerizzazione dei processi di servizio e l'adeguamento delle infrastrutture informatiche.

---

## Definizione degli obiettivi

Per assicurare il raggiungimento degli obiettivi è necessario formalizzare responsabilità, tempi e priorità nel **piano di informatizzazione**, approvato dai vertici decisionali.

| Tipo di Piano | Orizzonte Temporale | Contenuto e Scopo |
| --- | --- | --- |
| **Piano Strategico** | **3 - 5 anni** | Dà unitarietà evitando sistemi frammentati e incompatibili. Contiene obiettivi strategici, architetture tecnologiche/applicative di riferimento e grandi progetti. Spesso gestito **a scorrimento** (aggiornato annualmente). |
| **Piano Operativo** | **1 anno** | Dettaglia gli interventi previsti dal piano strategico per l'esercizio in corso e introduce eventuali elementi correttivi. |

### Approcci alla definizione: Top-Down vs Bottom-Up

- **Top-Down:** Parte dagli obiettivi strategici dei vertici per definire caratteristiche del SI, aree di intervento e progetti. È maggiormente innovativo e discontinuo, ma causa maggiori tensioni e rischi organizzativi.
- **Bottom-Up:** Parte dalle indicazioni delle singole unità organizzative, poi integrate e razionalizzate nel budget. È più conservativo, mira al miglioramento dell'esistente e presenta scarsi rischi realizzativi.
- **Approccio Misto (pratica diffusa):**
  1. Individuazione e diffusione delle strategie generali di evoluzione.
  2. Richiesta alle diverse unità di elaborare una proposta specifica coerente.
  3. Integrazione delle proposte con verifica di coerenza globale rispetto agli obiettivi.
  4. Definizione degli interventi infrastrutturali e del piano finale.

---

## Aziende Data-Driven e percorso di digitalizzazione

Il percorso di digitalizzazione è **incrementale** e raramente permette di saltare dei passaggi: è rischioso, costoso e inutile adottare soluzioni avanzate se manager, dati e processi non sono pronti.

> [!warning] Regola pratica
> *"Dubitate dei consulenti e dei fornitori di software che offrono analisi avanzate se la vostra azienda sfrutta a malapena un foglio Excel."*

Un'**azienda data-driven** basa decisioni e processi su conoscenze quantitative piuttosto che qualitative, rendendo la conoscenza una risorsa aziendale che non va persa se i manager cambiano. Il passaggio dallo stato $A$ allo stato $B$ avviene per obiettivi intermedi (realizzabili in meno di un anno, con costi correlati agli utili) lungo **tre dimensioni**:

1. **Technological Infrastructure:** L'infrastruttura tecnologica è appropriata per supportare la raccolta e l'analisi dei dati?
2. **Data Quality & Quantity:** I processi sono completamente digitalizzati e producono dati affidabili?
3. **Digital Culture:** Abbiamo le persone giuste per guidare i processi e sfruttare i risultati?

---

## Business Process Reengineering (BPR)

### Approcci al miglioramento

- **Approccio Giapponese:** Miglioramento continuo e incrementale.
- **Approccio Occidentale:** Miglioramento discontinuo a gradini.
- **Perché non basta:** Il solo miglioramento incrementale non permette di eliminare il gap di valore percepito dal cliente rispetto all'azienda leader di mercato.

### Cos'è il BPR

Il **BPR (Business Process Reengineering)** nasce agli inizi degli anni '90 per impulso di **Michael Hammer** (MIT) come completo ripensamento e radicale ridisegno da zero dei processi fondamentali di un'organizzazione alla luce delle potenzialità offerte dall'IT, puntando a un salto nelle prestazioni (**Breakthrough**).

> [!quote] Michael Hammer
> *"It is time to stop paving the cow paths. Instead of embedding outdated processes in silicon and software, we should obliterate them and start over."*

### I tre livelli di ridisegno dei processi

| Modalità | Approccio e Focus | Orizzonte | Esempi di intervento |
| --- | --- | --- | --- |
| **Streamlining** | Incrementale sui processi | Pianificare il presente | Modificare sequenze, semplificare/automatizzare attività, ridefinire input/output, bilanciare carichi di lavoro. |
| **Business Process Reengineering** | Radicale sui processi | Pianificare il futuro a parità di business | Eliminare e combinare processi, ricostruire da zero, introdurre competenze nuove, tecnologie avanzate e ridisegnare il sistema premiante. |
| **Enterprise Transformation** | Radicale sul business | Pianificare il business | Eliminare/sviluppare combinazioni prodotto/mercato, alleanze strategiche, ribilanciare il portafoglio di business. |

---

## Descrizione e Scomposizione dei Processi

Un **processo** è l'insieme delle attività tra loro interrelate, finalizzate alla realizzazione di un risultato definito e misurabile (**output**) a partire da degli **input**, che contribuisce alla missione aziendale e soddisfa un **cliente** (interno o esterno). Le classificazioni normative (*best practice* mappate nei pacchetti software) forniscono un riferimento della situazione *to-be*.

### Scomposizione gerarchica

1. **Macroprocesso:** Es. *Catena del valore di Porter* (Vendite, Produzione, ecc.).
2. **Processo:** Illustra a livello dettagliato le operazioni aziendali (es. *Gestione vendite*, oppure *Sviluppo prodotti*: Concept, Pianificazione, Progettazione, Prototipazione, Test, Ingegnerizzazione).
3. **Fase:** Tappa in cui un processo è implementato (es. *Evasione ordini*).
4. **Attività:** Scomposizione sequenziale delle fasi (es. *Ricezione ordini*, *Prelievo materiale*, *Spedizione*, *Fatturazione*).
5. **Operazioni:** Azioni elementari di un'attività (es. dentro *Spedizione*: *Pricing*, *Confezione*, *Carico*, *Preparazione fattura*).

### Variabili di progettazione dei processi

- **Flusso delle attività:** Sequenza logica che determina durata e flessibilità (modellabile con **UML Activity Diagram**).
- **Organizzazione del processo:** Suddivisione operativa del lavoro e coordinamento, modellabile mediante **Linear Responsibility Charting (LRC)**, una griglia che incrocia attività e unità organizzative assegnando i ruoli:
  - $D$: decide, autorizza, ratifica;
  - $E$: esegue;
  - $A$: partecipa a tempo parziale, fornisce assistenza operativa e supporto;
  - $I$: è sistematicamente informato.
- **Competenze delle risorse umane:** Determinano la differenza tra risultato effettivo e massimo teorico. L'adozione di tecnologie innovative richiede figure rare sul mercato (con costi massimi e minima disponibilità prima della diffusione di massa) e formazione per evitare il rigetto del nuovo sistema.
- **Sistema di misurazione e controllo delle prestazioni:** Realizzato tramite KPI o Balanced Scorecard.

---

## Key Performance Indicator (KPI)

Un **KPI** è un indicatore quantificabile e rilevabile che misura l'**efficacia** (grado di raggiungimento dell'obiettivo) e/o l'**efficienza** (economicità nel raggiungimento dell'obiettivo) di un processo, correlato agli obiettivi di business.

### Proprietà dei KPI

- **Significatività:** Collegamento con gli obiettivi strategici.
- **Controllabilità:** Misura risultati influenzabili da azioni intraprese in un periodo di tempo definito (mese, trimestre, anno).
- **Semplicità:** Facilità concettuale; pochi indicatori chiari sono preferibili a metriche complesse.
- **Misurabilità:** Disponibilità, affidabilità e accuratezza dei dati.
- **Equilibrio:** Collegamento tra **indicatori tecnici (ex-ante)**, che misurano le attività operanti (cause), e **indicatori di risultato (ex-post)**, che misurano la performance finale (effetti) sugli output. La scelta degli indicatori di risultato deve precedere quella degli indicatori tecnici.

### La mappa degli indicatori

Per misurare efficacemente un fenomeno occorre definire una mappa che chiarisca:
- **Nome** e **Fenomeno da misurare**
- **Obiettivi** da raggiungere
- **Misure** (aspetti qualitativi da monitorare)
- **Stakeholder** interessati (che osservano i dati a diversi livelli di aggregazione)
- **Sorgente dati** (ERP, POS, RFID, sensori, ecc.)
- **Formulazione** (formula matematica di calcolo)
- **Target** (valore atteso, soglia di attenzione, valore critico)

> [!example] Esempi di KPI (Retail)
> - **Tasso di disponibilità a scaffale:**
> $$\text{Disponibilità} = \frac{\text{Prodotti disponibili}}{\text{Prodotti previsti a scaffale}} \times 100$$
> *(Target: atteso $\ge 98\%$, attenzione $\ge 95\%$, critico $< 95\%$)*
> - **Tempo medio di attesa alle casse:**
> $$\text{Tempo medio} = \frac{\sum_{i=1}^{\text{NumClienti}} (\text{InizioServizio}_i - \text{IngressoCoda}_i)}{\text{NumClienti}}$$
> *(Target: atteso $< 3\text{ min}$, attenzione $3\text{--}5\text{ min}$, critico $> 5\text{ min}$)*

### Visualizzazione ed Esempi Settoriali

- **Sanità (Indicatori del Sant'Anna):** Utilizzano lo **scatter plot** (confronto tra livello di performance e trend di miglioramento/peggioramento), il **bersaglio** (dove il centro verde indica la performance migliore su gruppi di indicatori) e il **pentagramma** (che mostra l'andamento lungo le fasi di un processo, es. percorso materno-infantile).
- **Manifatturiero — OEE (Overall Equipment Effectiveness):** Principale KPI della *Lean Manufacturing* per misurare la capacità produttiva (richiede sistemi SCADA e MES):

$$\text{OEE} = \text{Availability} \times \text{Performance} \times \text{Quality}$$

  - **Availability** $[0,1]$: Percentuale di tempo effettivamente lavorato rispetto al tempo disponibile (al netto di setup, avviamenti, guasti, allarmi e controlli manuali).
  - **Performance** $[0,1]$: Percentuale di pezzi effettivamente lavorati rispetto ai pezzi teoricamente lavorabili a regime ottimale.
  - **Quality** $[0,1]$: Rapporto percentuale dei pezzi conformi sul totale dei pezzi prodotti (al netto di scarti e rilavorazioni).

---

## Fasi e Tecniche del BPR

Il processo di BPR segue una sequenza strutturata di attività:
1. **Identificazione dei processi da reingegnerizzare** (descrizione dei processi).
2. **Identificazione del breakthrough** (analisi dei tempi di ciclo e analisi della creazione del valore).
3. **Definizione delle priorità di intervento** sulla base dell'impatto (analisi di Pareto e matrici di portafoglio).
4. **Reingegnerizzazione dei processi** per rimuovere le barriere alla performance.
5. **Implementazione del cambiamento**.
6. **Istituzionalizzazione delle misure** per il miglioramento continuo.

### Identificazione dei Breakthrough

Esistono soglie di performance (costo, qualità, servizio, tempi di ciclo) il cui raggiungimento genera un miglioramento più che proporzionale dei benefici. Si individuano combinando:

- **Voce del Cliente (VoC):** Raccolta sistematica di opinioni, aspettative ed esperienze dei clienti (finali, interni e attori della *supply chain*) tramite sondaggi, interviste, focus group, social media e analisi del supporto clienti. Senza VoC si rischia di focalizzare il BPR su aree non a valore per il cliente.
- **Benchmarking:** Processo di comparazione delle performance interne (misurate tramite KPI) con standard esterni di eccellenza (*best-in-class*) per catalizzare soluzioni innovative e accelerare il cambiamento.

### Analisi dei tempi di ciclo (*Cycle Time Analysis*)

Misura e ottimizza l'efficienza scomponendo il tempo totale in:
- **Tempo di lavorazione (*Processing Time*):** Tempo effettivo dedicato alla realizzazione dell'attività/prodotto.
- **Tempo di attesa (*Waiting Time*):** Tempo in cui il lavoro resta fermo tra una fase e l'altra.
- **Setup Time:** Tempo richiesto per preparare macchine o processi.
- **Tempo di movimentazione (*Transport Time*):** Tempo impiegato per spostare materiali o informazioni.
- **Tempo di inattività (*Idle Time*):** Periodi in cui risorse o macchine non vengono utilizzate.

> [!example] Caso delle Utilities: Processo di fatturazione
> Un'azienda energetica in rapida crescita impiegava **4-5 giorni** per emettere le bollette, subendo perdite finanziarie. L'analisi dei tempi di ciclo ha rivelato che il sistema ERP era rapido, mentre l'**80% del tempo** era assorbito da controlli manuali fuori sistema su fogli Excel per verificare anomalie presenti in meno dello $0,1\%$ delle fatture.
> - **Soluzione adottata:** Eliminare i controlli dal processo di fatturazione (anziché implementarli nell'ERP, soluzione onerosa, ricorrente e fonte di *lock-in*), giudicando meno oneroso il rischio di emettere 1 fattura sbagliata su 1.000 rispetto a una perdita finanziaria certa.

### Analisi della creazione del valore

Consiste nel focalizzarsi sui processi che generano alto valore sia per il **cliente** sia per gli **azionisti**. A livello di singola attività, si applicano quattro domande filtro per identificare ciò che aggiunge realmente valore:

1. *Questa attività è stata richiesta dal cliente o sarebbe disposto a pagare per averla?* (Se **NO** $\rightarrow$ non aggiunge valore).
2. *È possibile eliminare questa attività senza impattare la qualità del prodotto o del servizio?* (Se **SÌ** $\rightarrow$ eliminare).
3. *Esiste una tecnologia che consente di eliminare questa attività?* (Se **SÌ** $\rightarrow$ automatizzare/eliminare).
4. *Questa attività potrebbe essere eliminata se alcune attività precedenti fossero fatte in maniera diversa o correttamente?* (Se **SÌ** $\rightarrow$ riprogettare a monte).

### Definizione delle priorità e Analisi di Pareto

La scelta dei processi su cui intervenire dipende dalla complessità del contesto (socio-politica e organizzativa) e dalla matrice **Impatto Potenziale / Difficoltà di implementazione e tempo**:
- **Alto impatto + Bassa difficoltà/veloce:** Implementare subito (*Early Wins*).
- **Alto impatto + Alta difficoltà/lento:** Perseguire *"challenge"*.
- **Basso impatto + Bassa difficoltà/veloce:** Perseguire con target conservativi.
- **Basso impatto + Alta difficoltà/lento:** Differire.

#### Analisi di Pareto (Regola 80/20)
Strumento statistico basato sul principio di Vilfredo Pareto secondo cui circa l'**80% degli effetti deriva dal 20% delle cause**. Si costruisce raccogliendo i dati sui problemi, ordinando le cause per frequenza/impatto decrescente in un grafico a barre e sovrapponendo la curva percentuale cumulata per individuare il *"20% cruciale"* su cui concentrare gli interventi.

---

## Ruolo dell'IT e Strumenti Analitici

Le tecnologie informatiche sono parte integrante delle tecniche di reengineering e devono essere sviluppate contestualmente al modello d'impresa (Organizzazione, Processi, Informazioni, Applicazioni, Tecnologia):

- **Azienda estesa:** Gestire processi oltre i confini dell'impresa (LAN/WAN, EDI, Internet/Extranet, E-Commerce).
- **Parallelizzazione:** Sostituire processi sequenziali con processi paralleli (Workflow, Groupware, Cooperative processing).
- **Riallocazione:** Incorporare l'expertise funzionale in sistemi e strumenti (Networking, Database distribuiti, ERP).
- **Eliminazione:** Eliminare attività tradizionali e steccati funzionali (Automazione funzioni, Integrazione applicazioni).
- **Cicli di miglioramento continuo:** Costruire cicli di feedback sulle prestazioni (Data Warehouse, Decision Support System, Business Intelligence).

### Innovazioni Technology-Driven vs Application-Driven

- **Technology-Driven:** Si basano su tecnologie estremamente innovative (es. uso di un LLM per fare pre-screening automatico di centinaia di bandi di gara), tali per cui gli stakeholder del servizio non riescono nemmeno a immaginarne l'utilizzo.
- **Application-Driven:** Si basano su tecnologie note e consolidate (es. app come *MyCicero* per pagare il parcheggio, oppure l'adozione di ERP e CRM), dove l'innovazione risiede nell'immaginarne l'applicazione in uno specifico contesto operativo o di mercato.

---

## Modello di ridisegno in 6 fasi e Change Management

In un programma di trasformazione aziendale il BPR va integrato con lo sviluppo dei nuovi supporti informatici e con il **Change Management** lungo 6 fasi:

1. **Condivisione obiettivi di business:** Documentare visione strategica, piani di sviluppo e modello di interrelazione tra volumi, costi e servizio.
2. **Definizione delle linee guida:** Selezionare i processi, definire macro-target, identificare gli skill, strutturare i team e avviare il piano di comunicazione.
3. **Analisi di base e Benchmark:** Analizzare processi *as-is*, confrontarli con le *best practice*, posizionare gli output sulla matrice costo-valore e ricostruire i requisiti dei clienti.
4. **Sviluppo dei concetti innovativi:** Sviluppare la visione di un processo eccellente (*to-be*).
5. **Progettazione del cambiamento:** Disegnare i nuovi flussi di processo, gestire le interfacce critiche e definire i fabbisogni di risorse umane e investimenti.
6. **Pianificazione dell'implementazione:** Concordare piano di migrazione, futura struttura organizzativa post-implementazione e allineare i criteri di misurazione delle prestazioni.

> [!tip] Importanza degli Early Wins
> Durante la realizzazione del cambiamento è essenziale ottenere **risultati immediati e tangibili (Early Wins)** perché:
> - giustificano l'impegno delle risorse;
> - determinano la credibilità del processo di gestione del cambiamento;
> - forniscono la prova che il management è impegnato nel cambiamento;
> - creano entusiasmo tra le persone.