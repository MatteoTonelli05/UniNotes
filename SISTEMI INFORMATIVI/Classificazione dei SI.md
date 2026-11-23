> [!quote] Un CIO efficace deve saper mappare le applicazioni aziendali non in base alla tecnologia, ma in funzione dei fabbisogni informativi e dei processi che devono supportare.

![[Pasted image 20260925123249.png]]
> [!danger] **Catena del valore di Porter**
> Processo principale che crea valore in azienda **(Core Business)**

> [!hint] Osservazione
> Nella piramide possiamo notare la divisione tra Strategic level (alto) e Operative Level (basso) 

## SI operativi

Informatizzano processi volti all’**esecuzione** di attività o alla loro **programmazione**
_(gestione del cliente, amministrazione, gestione produzione, parte commerciale)_

> per **programmazione** si intende la pianificazione spicciola

> [!question] Quanto l'azienda investirà sul fatto che questi processi funzionino bene? 
> Se il processo ha un Information Processing Capacity allineata con quelli dell'azienda, per capirlo ci si basa su due misurazioni:
>- intensità informativa del processo _(quanto il processo è complesso)_
>- intensità informativa del prodotto _(il prodotto è ricco di informazione)_
>
![[Pasted image 20260925124210.png]]

### SI direzionali

> [!question] Perchè si affrontano dopo i SI operativi?
> Perchè i SI direzionali prendono in input le informazioni prodotte dai SI operativi.

I SI Direzionali operano secondo il modello del controllo di gestione:
* Definizione degli obiettivi aziendali.
* Rilevazione delle prestazioni effettive.
* Confronto tra atteso ed effettivo (analisi degli scostamenti).
* Azioni correttive.
![[Pasted image 20260925142355.png]]
### SI analitici

> [!question] Che **differenza** c'è con i SI direzionali?
> I direzionali monitorano tutto ciò che succede nella vita aziendale (es. fatture)
> Gli **analitici** monitorano tutto ciò che succede fuori (es. mercato, competitor, clienti)

> [!quote] Escono fuori da quelli che è il perimetro aziendale perchè si misurano fenomenti esterni all'azienda

I SI sono un esempio di trasformazione digitale che va oltre al perimetro aziendale.
I SI analitici vengono utilizzate da aziende con clientela numerosa ad esempio:
![[Pasted image 20261116100049.png]]

---
## La mappa dei SI

> [!abstract] Quando parliamo di SI NON parliamo per forza di software, ma parliamo delle informazioni che sono necessarie a far funzionare i processi aziendali 

> [!question] Cosa significa costruire la mappa dei **SI**
> Significa che stiamo identificando le esigenze informative aziendali scomponendoli in cluster o gruppi. Sopra questi gruppi ci "appiccichiamo" dei moduli software

### Esempio da SAP

> [!abstract] SAP
> è il più grosso produttore di sistemi gestionali (**LP**)

> [!example] ![[Pasted image 20261116100651.png]]_L'esempio mostra i moduli che SAP consiglia per ogni necessità aziendale di automotive_

### Portagoglio applicativo aziendale

è un modo pratico che serve a costruire la **mappa** incrociando le fasi della catena di porter (colonne) con varie tipologie di attività, negli incroci incontriamo l'**esigenza informativa**.
![[Pasted image 20261116101032.png|577]]
> [!attention] Ovviamente le tipologie di attività variano per tipo di azienda
> > [!example] Un azienda siderurgica sarà interessata più alla gestione dei macchinari, un'azienda telefonica preferisce concentrarsi sull'erogazione del servizio.

> [!question] A cosa serve?
> In questo modo potremo definire il grado di **Information Processing Capacity** che, se elevato su un certo processo, porterà ad un investimento su quest'ultimo.

#### Divisione del portafoglio
![[Pasted image 20261116102245.png|574]]
**PORTAFOGLIO DIREZIONALE:** Contiene le attività di governo dell’azienda, divise in:
1. **PORTAFOGLIO ISTITUZIONALE:** Sono i processi di supporto, necessari a qualsiasi azienda, qualunque cosa produca.
2. **PORTAFOGLIO OPERATIVO:** Sono i processi che creano e portano sul mercato il prodotto, cioè la catena del valore.

Gestiti da diversi sistemi software _(riquadri tratteggiati)_:
- **ERP** (Enterprise Resource Planning): è il grande gestionale integrato. Copre il portafoglio istituzionale e la parte “interna” dell’operativo (progettazione, approvvigionamento, fabbricazione). Serve a gestire risorse, magazzino, contabilità, ordini in modo unico.
- **CRM** (Customer Relationship Management): copre il lato cliente, cioè Distribuzione e Vendita e Post-Vendita. Gestisce contatti, ordini, assistenza e fidelizzazione.
- **CAD** (Computer Aided Design): software di progettazione tecnica, legato a “Progettazione e industrializzazione”.
- **SCADA / CIM**: sistemi di controllo e supervisione della produzione in fabbrica (macchine, impianti, automazione). Legati alla “Fabbricazione”. CIM = Computer Integrated Manufacturing.

---

## Smart Manufactoring (AKA CIM)

Vuol dire avere una catena di software che ci permette di collegare le macchine che sono dotate di sensori e attuatori fino all'ordine, ovvero al SI in quanto tale.
![[Pasted image 20261116104544.png]] _da leggere dal basso verso l'alto_
### Sistemi Scada

I sistemi **SCADA** (Supervisory Control and Data Acquisition) sono usati per monitorare e controllare grandi impianti industriali e sistemi meccanici/elettronici distribuiti sul territorio.

> [!question] Perchè servono?
> - Riduzione delle esigenze di personale
> - Maggiore reattività rispetto a situazioni anomale

> [!example] I Digital Twin sono degli esempi perfetti
> Ad esempio i produttori di ammortizzatori usano i DT per il testing perchè sistemi precisissimi.

#### Architettura dei sistemi **SCADA**
![[Pasted image 20261116110605.png]]
> [!question] Perchè 3 server?
> 1. **Front-end server:** Riceve i dati dai vari siti/impianti, ognuno nel proprio formato, e li traduce in un formato che il server SCADA può usare.
> 2. **SCADA server:** monitora l’impianto e gestisce gli allarmi, lavorando in tempo reale con uno stream di dati.
> 3. **Data server:** salva i dati nel tempo.

> [!question] Che differenza c'è tra un sistema SCADA e IoT?
> Il sistema SCADA fa da imbuto verso un sistema centralizzato.

> [!fail] L'industria moderna va più verso IoT piuttosto che verso SCADA

---

### I sistemi MES

**Manufacturing Execution Systems** - sono sistemi per l'ottimizzazione nella gestione della fabbrica, raccolgono informazioni dallo SCADA e forniscono informazioni aggiornate all'ERP.

> [!example] Sistema amazon, i sistemi MES ottimizza l'arrivo dei robottini che portano i pacchi

---

### I sistemi ERP
**Enterprise Resource Planning** (*nome coniato da **Garner Group***) 

 > [!question] A cosa servono?
 > A evitare la digitalizzazione "a macchia di leopardo", in pratica un sistema ERP è un unico sistema che copra tutti i processi aziendale usando un sistema di dati centrato nell'organizzazione.
 > 
 > ![[Pasted image 20261116122506.png]]
 
 > [!abstract] Tuttora è una delle componenti principali della digitalizzazione
 
> [!quote] Il vantaggio dell’integrazione sta nella **circolarità dell’informazione**

**UNICITÀ DEL DATO** - l'unicità dell'informazione è ottenuta utilizzando un'unica base di dati condivisa che offre i seguenti vantaggi:
- Sincronizzazione dei dati
- Assenza di ridondanza
- Tracciabilità degli aggiornamenti
- Affidabilità dell'informazione aziendale (non si rischia inconsistenza)

Si parla di **MASTER DATA** (o _dato anagrafico/maestro_) è l'informazione aziendale centrale, strutturata e a lento mutamento che descrive le entità fondamentali su cui si basano i processi e le transazioni di un'organizzazione.

#### Paradigma ERP

> [!attention] Un sistema ERP non può essere SOLO un gestionale, ma deve essere esteso a tutto.
> Ovviamente per coprire tutto si vanno a creare diversi moduli che vanno a comporre la soluzione ottimale.

Per scegliere i moduli di interesse si utilizzano diverse strategie:
##### 1. Incrementale
si acquistano progressivamente i moduli che nella precedente configurazione del SI mancavano o erano realizzati mediante sistemi legacy inadeguati

##### 2. One stop shopping
si predilige la linearità acquistando i moduli di un solo vendor, possibilmente in un’unica tranche.

##### 3. Best of breed
vengono utilizzati i moduli di diversi vendor che meglio si prestano alle esigenze dell’azienda o che vengono considerati i migliori

> [!fail] Il metodo incrementale e one stop shopping coprono i processi ma non al meglio

 > [!success] Il Best of Breed risolve questo problema, ovviamente l'integrazione dei moduli è a carico dell'azienda produttrice dei moduli
 > > [!example] SAP ha diversi contratti con aziende di business intelligence
 
 ![[Pasted image 20261116131949.png]]
#### Prescrittività dei sistemi ERP

> [!attention] I software ERP sono sistemi già programmati e predefiniti
> Nel momento della vendità vengono garantite alcune flessibilità, ovvero
> - la compatibilità con le prossime *tot* versioni.
>- la parametrizzazione del software per adattarlo alle esigenze

> [!example] 
> ![[Pasted image 20261123134757.png|566]]

> [!question] E se volessi superare la **parametrizzazione**?
> Si passa alla personalizzazione, in quel caso si entra in quello che è chiamato **Lock in**, ovvero incompatibilità con le versioni successive
> > [!example] Conad è chiuso alle sue personalizzazioni, il programmatore responsabile delle personalizzazioni va in pensione tra due anni e loro sono completamente dipendenti da quest'ultimo

> [!abstract] Rivedere i processi in base a quelli che sono stati già pensati fa si che il momento di adozione di un **ERP** diventi anche un momento di **Business Process Reengineering (BPR)**

Solitamente gli ERP vengono utilizzati nelle imprese medie-grandi, dove la standardizzazione dei dati è resa necessaria dalla grande mole di dati.

---
### Sistemi CRM


