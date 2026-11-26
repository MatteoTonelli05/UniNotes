## Applicazioni alla linguistica formale: Language Models e Generative Models

**Filo conduttore.** Finora le grammatiche decidevano se una stringa appartiene a un linguaggio (sì/no). Qui si aggiungono le **probabilità**: la grammatica diventa un modello che dice **quanto è probabile** ogni stringa. Da lì si arriva ai **language model**, che sono un caso particolare dei **generative model** (compresi gli HMM e, come cenno, le reti neurali / LLM).

Collegamenti con le note precedenti:

- **CFG, alberi sintattici, ambiguità** → diventano PCFG, alberi con probabilità, ambiguità risolta scegliendo l'albero più probabile.
- **Grammatiche regolari** → diventano PRG (probabilistic regular grammar), equivalenti agli HMM.
- **CYK** → si accenna a una variante probabilistica per calcolare $P(w)$ in tempo polinomiale.
- **Simboli generanti / raggiungibili, eliminazione dei simboli inutili** → qui compaiono nelle condizioni di consistenza.
- **Gerarchia di Chomsky** → PRL $\subset$ PCFL, e HMM tra BOW e PCFG.

---

## 1. Phrase structure grammars

- **Noam Chomsky** introdusse il termine *phrase structure grammar* per indicare grammatiche come le **CFG**.
- Nei linguaggi naturali (per esempio l'inglese) le **categorie sintattiche** come **NP** (*noun phrase*, sintagma nominale) e **VP** (*verb phrase*, sintagma verbale) sono le **variabili** della grammatica.
- Rappresentano parti di frasi, dette **costituenti**.
- Una grammatica esprime quindi la **struttura a sintagmi** (*phrase structure*): la relazione tra i nodi (*constituency relation*) di un albero sintattico. Esempio: una frase (radice $S$) è formata da un NP (figlio sinistro) seguito da un VP (figlio destro).

La slide chiude con una domanda: **e i simboli terminali della grammatica?** La risposta è nella sezione successiva: sono i **token**.

---

## 2. Tokenizzazione

La **tokenizzazione** è il processo che divide un testo in una sequenza di parole (**token**): segmenta il flusso di caratteri in parole, punteggiatura, ecc.

Esempio:

```
From the start, it took a person with great qualities to succeed
```

diventa

```
From | the | start | , | it | took | a | person | with | great | qualities | to | succeed
```

- Per l'inglese è abbastanza semplice, per esempio dividendo il testo in corrispondenza degli spazi.
- Si assume che i token appartengano a un **insieme finito**:
  - le parole del **vocabolario** della lingua (corrette secondo il suo dizionario, per esempio il dizionario open source **WordNet**, più regole morfologiche come la flessione);
  - la punteggiatura, ecc.

### I token sono l'alfabeto

I token formano l'**alfabeto** $\Sigma$, e le frasi sono **stringhe in $\Sigma^*$**. Per la frase sopra:

$$\Sigma = \{[a], [from], [great], [it], [person], [qualities], [start], [succeed], [the], [to], [took], [with], [,], \dots\}$$

- Può avvenire una **normalizzazione**: per esempio "From" e "from" sono rappresentati dallo stesso token $[from]$.
- Spesso si scrive il token (per esempio $[from]$) semplicemente con la parola o punteggiatura che rappresenta ("From").

Collegamento: è qui che si colma il vuoto della sezione 1. I **terminali** della grammatica sono i token, e le **produzioni lessicali** associano le categorie ai token.

---

## 3. Ambiguità nei linguaggi naturali

I linguaggi naturali sono **ambigui**: è normale che una frase abbia **più alberi sintattici** (stesso concetto visto per le CFG).

Grammatica d'esempio:

$$\begin{align} S &\to NP\ VP \\ NP &\to Noun \mid Pronoun \\ VP &\to Verb\ NP \mid Modal\ Verb \\ Noun &\to [fish] \mid \dots \\ Pronoun &\to [I] \mid [they] \mid \dots \\ Verb &\to [can] \mid [fish] \mid \dots \\ Modal &\to [can] \mid \dots \end{align}$$

La frase **"I can fish"** ha **due alberi sintattici**:

| Albero | Struttura | Significato |
|---|---|---|
| $T_1$ | $S \to NP\ VP$, $NP \to Pronoun\ (I)$, $VP \to Modal\ (can)\ Verb\ (fish)$ | "io **posso** pescare" |
| $T_2$ | $S \to NP\ VP$, $NP \to Pronoun\ (I)$, $VP \to Verb\ (can)\ NP$, $NP \to Noun\ (fish)$ | "io **inscatolo** (fish) pesce" |

Si noti che c'è anche **ambiguità lessicale**: lo stesso token ($can$, $fish$) può appartenere a **più categorie lessicali** (*Modal* o *Verb*; *Verb* o *Noun*).

**Come si gestisce l'ambiguità:** con un approccio **probabilistico**, assegnando probabilità alle produzioni della grammatica, per esempio per **scegliere l'albero sintattico più probabile**.

---

## 4. Grammatiche probabilistiche (PCFG)

### Definizione

Una **PCFG** (*Probabilistic Context-Free Grammar*) è una **5-upla**

$$G = (V, \Sigma, P, S, D)$$

dove:

- $(V, \Sigma, P, S)$ è una **CFG**;
- $D$ è una funzione che assegna una **probabilità $p$ a ogni produzione** di $P$,

con il vincolo:

> per ogni variabile $A \in V$, la **somma delle probabilità delle produzioni di $A$ deve essere 1**.

**Notazione:**

$$A \to \beta\ [p]$$

significa: data la variabile $A$, essa viene espansa nella sequenza $\beta$ con probabilità $p$.

In altre parole: una PCFG definisce, per ogni variabile $A$, una **distribuzione di probabilità sulle produzioni di $A$**.

### Esempio: la variabile Adjs

$$\begin{align} Adjs &\to Adjective\ \ [0.80] \\ &\mid\ Adjective\ Adjs\ \ [0.20] \end{align}$$

Le probabilità delle produzioni di $Adjs$ sommano a $0.80 + 0.20 = 1$. Significato: con probabilità 0.8 la sequenza di aggettivi finisce con un aggettivo; con probabilità 0.2 ce ne sono altri.

### Esempio del libro: "tiny English grammar" per il Wumpus World

Un piccolo frammento di inglese per **interagire con un agente** che esplora il *Wumpus world* (comandi e informazioni dei sensori). Il Wumpus world è descritto qui sotto, nella sezione 12.

**Categorie sintattiche:**

$$\begin{align} S &\to NP\ VP\ [0.90] \mid S\ Conj\ S\ [0.10] \\ \\ NP &\to Pronoun\ [0.25] \mid Name\ [0.10] \mid Noun\ [0.10] \mid Article\ Noun\ [0.25] \\ &\quad\mid Article\ Adjs\ Noun\ [0.05] \mid Digit\ Digit\ [0.05] \mid NP\ PP\ [0.10] \\ &\quad\mid NP\ RelClause\ [0.05] \mid NP\ Conj\ NP\ [0.05] \\ \\ VP &\to Verb\ [0.40] \mid VP\ NP\ [0.35] \mid VP\ Adjective\ [0.05] \mid VP\ PP\ [0.10] \mid VP\ Adverb\ [0.10] \\ \\ Adjs &\to Adjective\ [0.80] \mid Adjective\ Adjs\ [0.20] \\ PP &\to Prep\ NP\ [1.00] \\ RelClause &\to RelPro\ VP\ [1.00] \end{align}$$

Esempi di frasi per ogni produzione (dalla slide):

| Produzione | Esempio |
|---|---|
| $S \to NP\ VP$ | *I + feel a breeze* |
| $S \to S\ Conj\ S$ | *I feel a breeze + and + It stinks* |
| $NP \to Pronoun$ | *I* |
| $NP \to Name$ | *Ali* |
| $NP \to Article\ Noun$ | *the + wumpus* |
| $NP \to Article\ Adjs\ Noun$ | *the + smelly dead + wumpus* |
| $NP \to Digit\ Digit$ | *3 4* |
| $NP \to NP\ PP$ | *the wumpus + in 1 3* |
| $NP \to NP\ RelClause$ | *the wumpus + that is smelly* |
| $NP \to NP\ Conj\ NP$ | *the wumpus + and + I* |
| $VP \to Verb$ | *stinks* |
| $VP \to VP\ NP$ | *feel + a breeze* |
| $VP \to VP\ Adjective$ | *smells + dead* |
| $VP \to VP\ PP$ | *is + in 1 3* |
| $VP \to VP\ Adverb$ | *go + ahead* |
| $PP \to Prep\ NP$ | *to + the east* |
| $RelClause \to RelPro\ VP$ | *that + is smelly* |

**Categorie lessicali** (le produzioni che portano ai token):

$$\begin{align} Noun &\to stench\ [0.05] \mid breeze\ [0.10] \mid wumpus\ [0.15] \mid pits\ [0.05] \mid \dots \\ Verb &\to is\ [0.10] \mid feel\ [0.10] \mid smells\ [0.10] \mid stinks\ [0.05] \mid \dots \\ Adjective &\to right\ [0.10] \mid dead\ [0.05] \mid smelly\ [0.02] \mid breezy\ [0.02] \dots \\ Adverb &\to here\ [0.05] \mid ahead\ [0.05] \mid nearby\ [0.02] \mid \dots \\ Pronoun &\to me\ [0.10] \mid you\ [0.03] \mid I\ [0.10] \mid it\ [0.10] \mid \dots \\ RelPro &\to that\ [0.40] \mid which\ [0.15] \mid who\ [0.20] \mid whom\ [0.02] \mid \dots \\ Name &\to Ali\ [0.01] \mid Bo\ [0.01] \mid Boston\ [0.01] \mid \dots \\ Article &\to the\ [0.40] \mid a\ [0.30] \mid an\ [0.10] \mid every\ [0.05] \mid \dots \\ Prep &\to to\ [0.20] \mid in\ [0.10] \mid on\ [0.05] \mid near\ [0.10] \mid \dots \\ Conj &\to and\ [0.50] \mid or\ [0.10] \mid but\ [0.20] \mid yet\ [0.02] \mid \dots \\ Digit &\to 0\ [0.20] \mid 1\ [0.20] \mid 2\ [0.20] \mid 3\ [0.20] \mid 4\ [0.20] \mid \dots \end{align}$$

> I puntini ($\dots$) indicano che le liste di token sono parziali: nel libro la grammatica ne ha altri, quindi le probabilità elencate non sommano a 1.

**Lessicale vs sintattico:** le categorie lessicali (quelle che producono token) e quelle sintattiche (quelle che producono altre categorie) sono insieme le **variabili** della grammatica. La distinzione riguarda **solo l'interpretazione linguistica**, non la forma della grammatica.

### Il lessico

Il **lessico** della lingua è specificato da:

- l'**insieme finito $\Sigma$** dei simboli terminali, cioè i **token** (parole del vocabolario, punteggiatura, ecc.);
- le produzioni che dicono, per ogni categoria lessicale, **quali token appartengono a quella categoria**.

$$C \to t\ [p]$$

significa: **quando si genera un token di categoria $C$, si sceglie $t$ con probabilità $p$**. Esprime quindi **quanto spesso** quello specifico $t$ compare quando si ha un token di categoria $C$.

### Da dove vengono le grammatiche

Storicamente, le categorie e la forma delle produzioni sono state studiate **annotando a mano** frasi in linguaggio naturale. La fonte più nota è il **Penn Treebank**: un corpus di circa **100 mila frasi** annotate con la struttura dell'albero sintattico.

---

## 5. Probabilità di una derivazione e di un albero

### Derivazione

Una derivazione $S \Rightarrow^* \alpha$, con $\alpha \in (V \cup \Sigma)^*$, in una PCFG è definita **come per le CFG**.

> La probabilità $P_G(S \Rightarrow^* \alpha)$ di una derivazione è il **prodotto delle probabilità delle produzioni applicate**.

Il prodotto vale perché si assume l'**indipendenza** della scelta della produzione a ogni sostituzione di variabile.

### Esempio: "every wumpus smells"

Derivazione:

$$S \Rightarrow NP\ VP \Rightarrow Article\ Noun\ VP \Rightarrow every\ Noun\ VP \Rightarrow every\ wumpus\ VP \Rightarrow every\ wumpus\ Verb \Rightarrow every\ wumpus\ smells$$

Produzioni usate e probabilità:

| Passo | Produzione | Probabilità |
|---|---|---|
| 1 | $S \to NP\ VP$ | 0.90 |
| 2 | $NP \to Article\ Noun$ | 0.25 |
| 3 | $Article \to every$ | 0.05 |
| 4 | $Noun \to wumpus$ | 0.15 |
| 5 | $VP \to Verb$ | 0.40 |
| 6 | $Verb \to smells$ | 0.10 |

$$0.90 \cdot 0.25 \cdot 0.05 \cdot 0.15 \cdot 0.40 \cdot 0.10 = 0.0000675$$

### Albero sintattico (parse tree)

- Gli **alberi sintattici** di una PCFG (in NLP detti **parse tree**) sono definiti **come per le CFG**: quelli che generano una stringa $w \in \Sigma^*$ e (salvo diversa indicazione) hanno $S$ come radice.
- La probabilità $P_G(T)$ di un albero $T$ è la **probabilità di una qualsiasi derivazione** che lo genera, cioè il **prodotto delle probabilità di tutte le produzioni dell'albero**.
- Si omette il pedice $G$ quando è chiaro dal contesto.

L'albero di "Every wumpus smells" ha come archi: $S \xrightarrow{0.90} NP\ VP$, $NP \xrightarrow{0.25} Article\ Noun$, $VP \xrightarrow{0.40} Verb$, e le foglie lessicali $Article \xrightarrow{0.05} Every$, $Noun \xrightarrow{0.15} wumpus$, $Verb \xrightarrow{0.10} smells$. Il prodotto è lo stesso: $0.0000675$.

Collegamento: ricorda quanto visto sui CFG, un albero corrisponde a una sola derivazione a sinistra (e a una a destra), quindi non serve distinguere tra "probabilità dell'albero" e "probabilità di una sua derivazione".

---

## 6. Ambiguità nelle PCFG

"Every wumpus smells" aveva **un solo** albero, ma in generale i linguaggi naturali sono ambigui.

- L'ambiguità nelle PCFG è definita **come per le CFG**: una stringa $w \in \Sigma^*$ può avere più derivazioni a sinistra (o a destra) o, equivalentemente, **più alberi sintattici**.
- La probabilità di generare $w$ è la **somma** delle probabilità di tutti i suoi alberi (o derivazioni a sinistra/destra).

$$P_G(w) = \sum_{T \in Tree_G(w)} P_G(T)$$

dove $Tree_G(w)$ è l'insieme degli alberi di $G$ che generano $w$. Se $w$ non ha alberi ($Tree_G(w) = \emptyset$) la somma vale 0.

**Nota sulla complessità.** Il numero di alberi in $Tree_G(w)$ è in generale **esponenziale** in $|w|$, ma $P_G(w)$ si può calcolare in **tempo polinomiale** con una **variante probabilistica di CYK**. Le slide lo annunciano, ma il calcolo non è svolto in questo PDF.

### Esempio: "I can fish"

Grammatica con probabilità:

$$\begin{align} S &\to NP\ VP\ [1.0] \\ NP &\to Noun\ [0.6] \mid Pronoun\ [0.4] \\ VP &\to Verb\ NP\ [0.8] \mid Modal\ Verb\ [0.2] \\ Noun &\to fish\ [0.3] \mid \dots \\ Pronoun &\to I\ [0.4] \mid they\ [0.3] \mid \dots \\ Verb &\to can\ [0.01] \mid fish\ [0.1] \mid \dots \\ Modal &\to can\ [0.3] \mid \dots \end{align}$$

Probabilità dei due alberi:

$$P(T_1) = 1 \cdot 0.4 \cdot 0.4 \cdot 0.2 \cdot 0.3 \cdot 0.1 = 0.00096 \quad \text{(il più probabile)}$$

$$P(T_2) = 1 \cdot 0.4 \cdot 0.4 \cdot 0.8 \cdot 0.01 \cdot 0.6 \cdot 0.3 = 0.0002304$$

$$P(\text{"I can fish"}) = 0.00096 + 0.0002304 = 0.0011904$$

Come leggere i fattori di $T_1$: $S \to NP\,VP$ (1), $NP \to Pronoun$ (0.4), $Pronoun \to I$ (0.4), $VP \to Modal\,Verb$ (0.2), $Modal \to can$ (0.3), $Verb \to fish$ (0.1).
Per $T_2$: $S \to NP\,VP$ (1), $NP \to Pronoun$ (0.4), $Pronoun \to I$ (0.4), $VP \to Verb\,NP$ (0.8), $Verb \to can$ (0.01), $NP \to Noun$ (0.6), $Noun \to fish$ (0.3).

**Uso pratico.** Per interpretare la frase si sceglie $T_1$ (il **più probabile**: "io posso pescare"). La probabilità totale della stringa è la somma.

---

## 7. Linguaggi formali probabilistici

### Definizione

Dato un alfabeto $\Sigma$, un **linguaggio formale probabilistico** è una **distribuzione di probabilità $P$ su $\Sigma^*$**, cioè una funzione $P : \Sigma^* \to [0,1]$ tale che

$$\sum_{w \in \Sigma^*} P(w) = 1$$

> Differenza con i linguaggi formali visti finora: un linguaggio classico è un **insieme** di stringhe (una stringa c'è o non c'è). Qui a ogni stringa è associato un **numero** (quanto è probabile).

### Esempio 1

$\Sigma = \{[hello], [bye]\}$, con

- $P([hello][bye]) = 0.9$
- $P([hello][hello][bye]) = 0.1$
- $P(w) = 0$ per ogni altra $w$.

Le probabilità sommano a $0.9 + 0.1 = 1$: è un linguaggio formale probabilistico.

### Esempio 2

Con $\Sigma$ l'alfabeto della *tiny English PCFG* $G$, la funzione $P_G(w)$ è un linguaggio formale probabilistico. L'intuizione: il processo di derivazione genera **sempre** un albero di qualche stringa $w \in \Sigma^*$, quindi la somma delle probabilità degli alberi è 1:

- la somma delle probabilità delle produzioni di $S$ è 1;
- scegliendo tra le produzioni alternative di una variabile $A$ si **suddivide** ulteriormente la probabilità totale dell'albero secondo probabilità che sommano a 1.

### Linguaggio sottostante (underlying language)

Il **supporto** di un linguaggio formale probabilistico $P$, detto **underlying language**, è il linguaggio formale classico

$$L = \{ w \mid P(w) > 0 \}$$

- Per l'Esempio 1 è $\{[hello][bye],\ [hello][hello][bye]\}$.
- Per l'Esempio 2 è $L(G)$, dove $G$ è la CFG ottenuta **togliendo le probabilità** dalla tiny English PCFG.

---

## 8. PCFG problematiche: probabilità di terminazione e consistenza

**Non tutte le PCFG rappresentano un linguaggio formale probabilistico.**

### Esempio

$$\begin{align} S &\to a\ [0.4] \\ S &\to A\ [0.6] \\ A &\to A\ [1] \end{align}$$

- $G$ può generare **solo la stringa "a"**, e solo con **un albero** (quello che applica subito $S \to a$).
- Quindi $\sum_{w} P_G(w) = 0.4$, non 1.
- Il restante $0.6$ di probabilità va in derivazioni **che non terminano mai** ($S \to A \to A \to A \dots$).

> In generale certe regole ricorsive fanno **perdere massa di probabilità** a causa di derivazioni che non terminano.

### Probabilità di terminazione

La **termination probability** $P_G$ di una PCFG è la probabilità totale che $G$ generi **qualche stringa terminale**:

$$P_G = \sum_{w \in \Sigma^*} P_G(w)$$

Si può calcolare direttamente dagli alberi sintattici:

$$P_G = \sum_{w \in \Sigma^*} \sum_{T \in Tree_G(w)} P_G(T) = \sum_{T \in Tree_G} P_G(T)$$

dove $Tree_G$ è l'insieme di **tutti** gli alberi sintattici di $G$.

Significato: è la probabilità totale che il processo di derivazione **termini con successo**, producendo un vero albero sintattico $T$, cioè un albero **finito** che genera una stringa $w \in \Sigma^*$.

### PCFG consistente

Una PCFG $G$ è **consistente** se $P_G = 1$:

$$P_G = \sum_{w \in \Sigma^*} P_G(w) = 1$$

Una PCFG consistente rappresenta:

- un **linguaggio formale probabilistico**, la distribuzione $P_G(w)$ su $\Sigma^*$ definita da $P_G(w) = \sum_{T \in Tree_G(w)} P_G(T)$;
- anche una **distribuzione $P_G(T)$ sugli alberi sintattici** $T \in Tree_G$.

Esempi:

- tiny English PCFG: $P_G = 1$, quindi **consistente**;
- l'esempio con $A \to A$: $P_G = 0.4$, quindi **non consistente**.

### Condizione necessaria per la consistenza

> **Ogni variabile raggiungibile è generante.**

Come per le CFG, data $G = (V, \Sigma, P, S)$:

- $A$ è **raggiungibile** se $S \Rightarrow^* \alpha A \beta$ per qualche $\alpha, \beta \in (V \cup \Sigma)^*$, **con $P_G(S \Rightarrow^* \alpha A \beta) > 0$**;
- $A$ è **generante** se $A \Rightarrow^* w$ per qualche $w \in \Sigma^*$, **con $P_G(A \Rightarrow^* w) > 0$**.

Nell'esempio, $A$ è raggiungibile (da $S \to A$) ma **non generante**, quindi si conclude subito che $G$ non è consistente.

**Differenza con le CFG:** nelle PCFG **non si possono semplicemente eliminare le variabili non generanti**, come si faceva nelle CFG. Eliminando $A$ resterebbe solo $S \to a\ [0.4]$, e le probabilità di $S$ non sommerebbero più a 1: non sarebbe più una PCFG. Si possono invece eliminare le variabili **non raggiungibili**.

### La condizione NON è sufficiente

Esempio di PCFG non consistente dove **ogni variabile raggiungibile è generante**:

$$\begin{align} S &\to a\ [0.4] \\ S &\to S\ S\ [0.6] \end{align}$$

$P_G$ (cioè la probabilità di terminazione di $S$) deve soddisfare, perché le due $S$ vengono sostituite **in modo indipendente**:

$$P_G = 0.4 + 0.6 \cdot P_G \cdot P_G$$

Lettura: o si usa $S \to a$ (probabilità 0.4), oppure $S \to SS$ (0.6) e **entrambe** le $S$ devono terminare (probabilità $P_G \cdot P_G$).

L'equazione $0.6\,P^2 - P + 0.4 = 0$ ha **due soluzioni**: $P = 1$ e $P = \tfrac{2}{3}$. Verifica per $\tfrac{2}{3}$:

$$0.4 + 0.6 \cdot \left(\tfrac{2}{3}\right)^2 = \tfrac{4}{10} + \tfrac{6}{10}\cdot\tfrac{4}{9} = \tfrac{4}{10} + \tfrac{4}{15} = \tfrac{12}{30} + \tfrac{8}{30} = \tfrac{20}{30} = \tfrac{2}{3}$$

**Qual è quella giusta?** $P_G$ si calcola **sommando progressivamente le probabilità di tutti gli alberi**:

- alberi di profondità massima 1 (solo $S \to a$): $0.4$;
- alberi di profondità massima 2: $0.4 + 0.6 \cdot 0.4^2$, cioè $0.496$;
- e così via.

Si ottiene una **successione crescente di approssimazioni dal basso**, che parte da 0 e ha come limite la **soluzione più piccola** (*least fixed point*) dell'equazione, cioè $\tfrac{2}{3}$. La soluzione $1$ esiste ma non è quella raggiunta. Quindi $P_G = \tfrac{2}{3} < 1$: **non consistente**.

**Perché succede.** Il problema è una variabile **ricorsiva $A$ che può generare repliche di se stessa in più di un ramo** dell'albero. Se il **numero medio di repliche generate è $> 1$**, la probabilità di terminazione scende sotto 1. Per $S$ nell'esempio la media è $1.2$: la probabilità $0.6$ moltiplicata per un albero con 2 rami, ognuno con una replica di $S$.

---

## 9. Grammatiche regolari probabilistiche (PRG)

Una **PRG** (*Probabilistic Regular Grammar*) è una **PCFG le cui produzioni sono quelle di una grammatica regolare**, cioè della forma:

- $A \to aB$
- $A \to a$
- eventualmente (per generare la stringa vuota) $S \to \epsilon$ per il simbolo iniziale $S$, purché nessuna produzione $A \to \beta$ abbia $S$ in $\beta$.

(Vedi la nota *Gerarchia di Chomsky* per la definizione delle grammatiche di tipo 3.)

### Caratterizzazione della consistenza per le PRG

Per una PRG la condizione necessaria delle PCFG, **ogni variabile raggiungibile è generante**, è anche **sufficiente**.

**Perché:** nei corpi delle produzioni c'è **al più una variabile**, quindi in un albero sintattico **al più un figlio è una variabile**. Una variabile ricorsiva $A$ non può dunque generare repliche di se stessa in più di un ramo, ed è proprio il fenomeno che rendeva inconsistente $S \to SS$.

Una condizione sufficiente **più forte**: ogni variabile della PRG è **sia raggiungibile sia generante**.

### Classi di linguaggi probabilistici

- **PCFL** (*Probabilistic Context-Free Languages*): i linguaggi formali probabilistici delle PCFG **consistenti**.
- **PRL** (*Probabilistic Regular Languages*): i linguaggi formali probabilistici delle PRG **consistenti**.

Ovviamente:

- il linguaggio sottostante di un **PCFL** è un linguaggio **libero dal contesto**;
- il linguaggio sottostante di un **PRL** è un linguaggio **regolare**.

---

## 10. Language models e generative models

### Language model (introduzione)

Un **language model** probabilistico permette di **rappresentare in modo finito**, tramite **parametri del modello**, un linguaggio formale probabilistico $P$ (che in generale è infinito).

- Esempio: una PCFG consistente. I **parametri** del modello sono le **probabilità associate alle produzioni**.
- Anche una PRG è quindi un language model.
- Altri esempi: **Mixture of Bag-of-Words** (in generale modelli **n-gram**), **Hidden Markov Model**, **Recurrent Neural Network**, **LLM / Transformer**, ...

Inoltre, i modelli che generano le stringhe $w$ **attraverso valori di classificazione** (come le categorie sintattiche e lessicali negli alberi di una PCFG) rappresentano:

- non solo una distribuzione di probabilità $P(w)$ sulle stringhe,
- ma anche una distribuzione sulle **strutture generate** (per esempio $P_G(T)$ sugli alberi $T$).

I language model sono un caso particolare di una classe più ampia: i **generative model**.

### Generative (sequence) model

Un **generative model** è una **rappresentazione finita**, tramite parametri, di una **distribuzione di probabilità**

- su uno **spazio di dati** (possibilmente infinito) e, quando presenti nel modello,
- su **valori di classificazione / etichette** collegati.

Il **sequence modeling** considera uno spazio di dati in cui ogni dato è una **sequenza ordinata di simboli**. Se le sequenze hanno lunghezza non limitata, lo spazio dei dati è **infinito**.

### Language model (definizione)

> Un **language model** è un generative model il cui spazio dei dati è $\Sigma^*$, cioè sequenze illimitate di token in $\Sigma$.

Esempio: una PCFG consistente $G$ rappresenta una distribuzione $P_G(T)$ sugli alberi, che includono le stringhe di $\Sigma^*$ (spazio dei dati); i valori di classificazione sono le **categorie lessicali e sintattiche** usate per generarle.

---

## 11. Mixture of Bag-of-Words (BOW)

### Idea

Esempio semplice: delle **categorie** (business, weather, ...) per classificare le frasi (stringhe in $\Sigma^*$), e la generazione della frase basata su questi parametri:

- una **distribuzione di probabilità sulle categorie** (finite), usata **prima** per scegliere la categoria della frase intera (per esempio *weather*);
- un **bag-of-words** (distribuzione di probabilità sulle parole) **per ogni categoria**:
  - ogni parola è su un foglietto dentro il sacchetto; più la parola è comune, più foglietti ha (duplicati);
  - si usa **solo** il sacchetto della categoria scelta, per generare **tutte** le parole della frase;
  - si pesca una parola a caso (sarà la prima della frase), si **rimette** nel sacchetto, si pesca la seconda, ecc.

### Confronto con le PCFG

Invece di generare un albero sintattico (con molte categorie sintattiche e lessicali diverse):

- si sceglie inizialmente **una sola categoria** (lessicale) $Categ$, con una distribuzione di probabilità sulle categorie espressa in modo simile alle produzioni di $S$ in una PCFG;
- poi si generano **tutte le parole** usando il sacchetto di $Categ$ più volte, con una distribuzione sui token $t$ espressa in modo simile alle produzioni $Categ \to t$.

Quindi, al posto della generazione ad albero, una **generazione sequenziale da sinistra a destra**, più semplice:

- si ripete finché non viene generato un **token di fine frase** speciale, indicato per esempio con $\langle EOS \rangle$ (*End Of Sentence*);
- per ogni categoria, $\langle EOS \rangle$ deve avere probabilità **non nulla**.

### Esempio numerico

- Categorie: $\{Business, Weather\}$.
- Token: $\langle EOS \rangle$ più l'alfabeto finito $\Sigma = \{[cloudy], [continued], [heavy], [pound], [rain], [stocks], [to], \dots\}$.
- Distribuzione sulle categorie: $P(Business) = 0.6$, $P(Weather) = 0.4$.
- Distribuzione sui token per ogni categoria:

| Token $t$ | $P(t \mid \text{Business})$ | $P(t \mid \text{Weather})$ |
|---|---|---|
| $\langle EOS \rangle$ | 0.08 | 0.08 |
| $[cloudy]$ | 0.01 | 0.16 |
| $[continued]$ | 0.04 | 0.04 |
| $[heavy]$ | 0.05 | 0.06 |
| $[pound]$ | 0.15 | 0.01 |
| $[rain]$ | 0.01 | 0.21 |
| $[stocks]$ | 0.15 | 0.01 |
| $[to]$ | 0.04 | 0.04 |
| ... | ... | ... |

**Probabilità di generare** $w = $ "Heavy rain continued to pound" (6 token: i 5 della frase più $\langle EOS \rangle$).

**Via Business:**

$$P(w \mid Business) = 0.05 \cdot 0.01 \cdot 0.04 \cdot 0.04 \cdot 0.15 \cdot 0.08 = 0.0000000096$$

$$P(Business) \cdot P(w \mid Business) = 0.6 \cdot 0.0000000096 = 0.00000000576$$

**Via Weather:**

$$P(w \mid Weather) = 0.06 \cdot 0.21 \cdot 0.04 \cdot 0.04 \cdot 0.01 \cdot 0.08 = 0.000000016128$$

$$P(Weather) \cdot P(w \mid Weather) = 0.4 \cdot 0.000000016128 = 0.0000000064512$$

**Totale:**

$$P(w) = 0.00000000576 + 0.0000000064512 = 0.0000000122112$$

**Analogia con l'ambiguità delle PCFG:** una frase con più alberi (valori di classificazione) corrisponde qui a una frase generabile da **più categorie**. La domanda: **è più probabile la generazione via Business o via Weather?** Qui è più probabile **Weather** ($6.45 \cdot 10^{-9} > 5.76 \cdot 10^{-9}$), il che è ragionevole per "Heavy rain...".

### Formule

Probabilità di generare una stringa $w = t_{1:N}$ (sequenza di $N$ token $t_1, \dots, t_N$) più $t_{N+1} = \langle EOS \rangle$ **tramite una categoria** $Categ$:

$$P(Categ, w) = P(Categ)\, P(w \mid Categ) = P(Categ) \prod_{i=1}^{N(w)} P(t_i \mid Categ)$$

dove $N(w) = |w| + 1 = N + 1$ è il **numero di token necessari** per generare $w$ (compreso $\langle EOS \rangle$).

È la stessa cosa che si fa per $P(T)$ in una PCFG: si **moltiplicano le probabilità di tutte le scelte**, con la stessa **assunzione di indipendenza** (la scelta dell'$i$-esimo token).

**Probabilità totale** di una stringa $w \in \Sigma^*$:

$$P(w) = \sum_{Categ} P(Categ, w)$$

È questo il linguaggio formale probabilistico rappresentato.

### È un modello utile?

- Assumere che ogni parola sia **indipendente dalle altre** è chiaramente **irrealistico**: il modello **può generare frasi inglesi scorrette**.
- È però **buono a classificare** una frase data: nell'esempio la generazione via Weather risulta la più probabile. È analogo a inferire la probabilità degli alberi di una frase $w$ nelle PCFG (**parsing**).
- Per questo, storicamente, si sono considerati modelli più espressivi:
  - **modello n-gram**: la probabilità di ogni parola dipende dalle **$n-1$ parole precedenti** (il Bag-of-Words è tecnicamente il modello **1-gram**);
  - **Hidden Markov Model**: secondo la slide, la probabilità di ogni parola può dipendere da **tutte le parole precedenti** (attraverso lo stato nascosto).

---

## 12. Automi a stati finiti probabilistici, MDP e POMDP

Prima di arrivare agli HMM, le slide introducono (come contrasto) i **PFA**: un'estensione probabilistica degli automi a stati finiti che però **non è generativa**.

### PFA (Probabilistic Finite-state Automaton)

Un **PFA** è una **5-upla** $(Q, \Sigma, \delta, \pi, F)$, dove:

- $Q$ è un insieme finito di **stati**;
- $\Sigma$ è un **alfabeto** finito;
- $\delta$ è una **funzione di transizione parziale** da $Q \times \Sigma$ a **distribuzioni di probabilità su $Q$**;
- $\pi$ è una **distribuzione di probabilità iniziale** su $Q$;
- $F \subseteq Q$ è l'insieme degli **stati finali**.

L'idea: i PFA considerano distribuzioni di probabilità sulle **destinazioni** delle transizioni.

**Esempio dalla slide:**

- Distribuzione iniziale: $\pi(s_8) = 0.8$, $\pi(s_0) = 0.2$.
- Da $s_8$ con $a$: va in $s_1$ con prob. $0.2$ o in $s_2$ con prob. $0.8$.
- Da $s_0$ con $a$: va in $s_3$ ($0.6$), $s_4$ ($0.3$) o $s_5$ ($0.1$).
- Da $s_0$ con $b$: va in $s_6$ ($0.5$) o $s_7$ ($0.5$).
- Stati finali: $s_2$, $s_4$, $s_6$.

### Perché i PFA non sono un modello generativo

I PFA **non sono un modello probabilistico generativo**: per esempio **non dicono con quale probabilità si esegue una transizione $a$ o $b$ dallo stato $s_0$**. Dicono solo cosa succede **dopo** che un simbolo è stato scelto.

Sono invece un **modello probabilistico reattivo**:

- reagiscono ad **azioni scelte da un agente esterno** che interagisce con l'automa;
- in *reinforcement learning* modellano il comportamento dell'**ambiente** (per esempio le regole del gioco del Wumpus world): le **reazioni probabilistiche** dell'ambiente alle azioni $a$ o $b$ scelte dall'agente;
- sono molto importanti: sono la **base dei Markov Decision Process (MDP)**.

### MDP (Markov Decision Process)

Un **MDP** è una **6-upla** $(Q, \Sigma, \delta, R, \pi, F)$, dove:

- $(Q, \Sigma, \delta, \pi, F)$ è un **PFA**;
- $R$ è una **funzione di ricompensa** (*reward*) dalle transizioni del PFA $Q \times \Sigma \times Q$ ai numeri reali $\mathbb{R}$: assegna una ricompensa $r$ positiva, nulla (assente) o negativa a ogni transizione $s \xrightarrow{a} s'$.

**Esempio dalla slide** (stesso grafo del PFA, con ricompense):

- $s_8 \xrightarrow{a} s_1$: **+5**; $s_8 \xrightarrow{a} s_2$: **−3**;
- $s_0 \xrightarrow{a} s_3$: **+10**;
- $s_0 \xrightarrow{b} s_7$: **−1**.

### Come interagisce un agente con un MDP

- L'agente sceglie un'**azione** secondo una **policy** (per esempio nel Wumpus world: *TurnLeft, TurnRight, Forward, Grab, Shoot, Climb*).
- L'MDP riceve in input l'azione (come i simboli di una stringa in un automa a stati finiti standard) e **reagisce** passando a un nuovo stato e dando una **ricompensa** (per esempio oro $+1000$, morte $-1000$, $-1$ per passo, $-10$ per la freccia).
- L'agente **impara dalla ricompensa** e **aggiorna la policy**.

### Il Wumpus World (PEAS)

Il Wumpus world è l'ambiente d'esempio del libro. Descrizione **PEAS** (*Performance, Environment, Actuators, Sensors*):

| PEAS | Contenuto | Ruolo nel modello MDP |
|---|---|---|
| **Performance measure** | uscire dalla caverna con l'oro $+1000$; cadere in una buca o essere mangiati dal wumpus $-1000$; $-1$ per passo; $-10$ per l'uso della freccia | **reward** |
| **Environment** | griglia $4 \times 4$ di stanze circondata da muri; l'agente parte da $[1,1]$; le altre caselle contengono a caso 1 wumpus, 1 oro e delle buche | **stato** |
| **Actuators** | TurnLeft ($90°$), TurnRight ($90°$), Forward (1 passo), Grab (prende l'oro se è nella stessa casella), Shoot (spara l'unica freccia in linea retta e uccide il wumpus se lo colpisce), Climb (esce solo da $[1,1]$) | **azioni** |
| **Sensors** | Stench (nella casella direttamente, non in diagonale, adiacente al wumpus), Breeze (adiacente a una buca), Glitter (nella casella dell'oro), Bump (urto contro un muro), Scream (percepito ovunque quando il wumpus è ucciso) | **come si modellano?** (vedi POMDP) |

### POMDP

Spesso l'agente ha una **visibilità parziale** dello stato dell'ambiente: nel Wumpus world lo osserva (e ne deduce parti) solo tramite i **sensori** (Stench, Breeze, Glitter, Bump, Scream). Un'**osservazione** è un insieme di questi valori.

In questo caso gli **stati dell'MDP sono nascosti** all'agente e si associano loro **osservazioni probabilistiche**:

- agli **stati**, per esempio per Stench, Breeze e Glitter;
- ed eventualmente anche alle **azioni** eseguite per arrivarci, per esempio per Bump (azione Forward) e Scream (azione Shoot).

Questo insieme forma il cosiddetto **sensor model**. L'estensione dell'MDP si chiama **POMDP** (*Partially Observable Markov Decision Process*).

Collegamento con quello che segue: l'idea di "stati nascosti + osservazioni" è proprio quella degli **HMM**.

---

## 13. Catene di Markov e Hidden Markov Model

Obiettivo: una versione probabilistica degli automi a stati finiti che sia davvero **generativa**. Servono prima le **catene di Markov**, poi gli **HMM**.

### Catena di Markov

Una **catena di Markov** è un automa a stati finiti probabilistico **senza etichette sulle transizioni e senza stati finali**: per ogni stato c'è una distribuzione di probabilità sulle transizioni uscenti.

Una **catena di Markov a tempo discreto (MC)** è una **tripla** $(Q, \delta, \pi)$, dove:

- $Q$ è un insieme finito di **stati**;
- $\delta$ è una **funzione di transizione** dagli stati $Q$ a distribuzioni di probabilità su $Q$;
- $\pi$ è una **distribuzione di probabilità iniziale** su $Q$.

### Esempio: giorno soleggiato / piovoso

$Q = \{Sunny, Rainy\}$, con:

| | Valore |
|---|---|
| $\pi(Sunny)$ | 0.7 |
| $\pi(Rainy)$ | 0.3 |
| $\delta(Sunny \to Sunny)$ | 0.8 |
| $\delta(Sunny \to Rainy)$ | 0.2 |
| $\delta(Rainy \to Rainy)$ | 0.6 |
| $\delta(Rainy \to Sunny)$ | 0.4 |

(Ogni riga di uscita somma a 1: $0.8 + 0.2 = 1$, $0.6 + 0.4 = 1$, $0.7 + 0.3 = 1$.)

### Probabilità di una sequenza di stati

$Seq = Sunny \to Sunny \to Rainy \to Rainy$:

$$P(Seq) = 0.7 \cdot 0.8 \cdot 0.2 \cdot 0.6 = 0.0672$$

(prima probabilità iniziale, poi le tre transizioni). Se si fissa una lunghezza $k$, $P(Seq)$ è una **distribuzione di probabilità su tutte le sequenze $Seq$ di lunghezza $k$**.

### Aggiungere le emissioni: HMM

Per ottenere automi a stati finiti probabilistici che **generano dati**, bisogna aggiungere alle catene di Markov un **alfabeto $\Sigma$** per i simboli delle sequenze generate.

Gli **HMM**, invece di usare i simboli di $\Sigma$ come etichette delle transizioni (come nei PFA), **associano a ogni stato delle emissioni probabilistiche** di simboli di $\Sigma$.

Un **HMM** è una **5-upla** $(Q, \Sigma, \delta, E, \pi)$, dove:

- $(Q, \delta, \pi)$ è una **catena di Markov**;
- $\Sigma$ sono i simboli dell'alfabeto, detti **osservazioni**;
- $E$ è una **funzione di emissione** dagli stati $Q$ a distribuzioni di probabilità sulle osservazioni $\Sigma$.

Graficamente le emissioni (da stati a osservazioni) si rappresentano con frecce come le transizioni (nelle slide: **frecce tratteggiate**).

**Perché "hidden":** si assume di **non poter osservare direttamente gli stati** (sono *nascosti*), ma solo i loro effetti, tramite le osservazioni. Come nei POMDP: per questo la funzione di emissione si chiama anche **sensor model**.

### Esempio di HMM

$(Q, \delta, \pi)$ è la catena Sunny/Rainy di prima; un'osservazione è: **una persona porta l'ombrello o no**, quindi $\Sigma = \{true, false\}$.

| Stato | $P(true)$ | $P(false)$ |
|---|---|---|
| $Sunny$ | 0.2 | 0.8 |
| $Rainy$ | 0.9 | 0.1 |

(Con $\delta$ e $\pi$ come nella catena di Markov di sopra.)

### Generare sequenze di osservazioni

- Gli **stati nascosti** rappresentano **categorie** che generano osservazioni: il giorno Sunny o Rainy nel mondo esterno che genera le osservazioni dell'ombrello (fatte, per esempio, da dentro un edificio).
- Anche le **categorie lessicali** che generano parole/token: le emissioni sono come le produzioni delle categorie lessicali in una PCFG.
- Gli HMM generano **sequenze di osservazioni**.
- L'**inferenza** negli HMM è il problema di determinare la probabilità di una **sequenza di stati nascosti** a partire da una sequenza di osservazioni (rappresentata da una stringa, per esempio $w = true\ false\ true\ true$). Come l'inferenza degli alberi sintattici nelle PCFG (**parsing** di $w$).

### Probabilità di generare una stringa via una sequenza di stati

Stringa $w = o_{1:N}$ (osservazioni $o_1, \dots, o_N$), tramite una sequenza di stati $Seq = Categ_{1:N}$:

$$P(Seq, w) = P(Seq)\, P(w \mid Seq) = P(Seq) \prod_{i=1}^{N(w)} P(o_i \mid Categ_i)$$

dove $N(w) = |w| = N$ è il numero di osservazioni necessarie.

**Esempio:** $w = true\ false\ true\ true$ via $Seq = Sunny \to Sunny \to Rainy \to Rainy$:

- sequenza di stati: $P(Seq) = 0.7 \cdot 0.8 \cdot 0.2 \cdot 0.6 = 0.0672$;
- emissioni: $P(w \mid Seq) = 0.2 \cdot 0.8 \cdot 0.9 \cdot 0.9$ (Sunny emette $true$: 0.2; Sunny emette $false$: 0.8; Rainy emette $true$: 0.9; Rainy emette $true$: 0.9);

$$P(Seq, w) = 0.0672 \cdot 0.2 \cdot 0.8 \cdot 0.9 \cdot 0.9 = 0.00870912$$

Corrisponde a quanto si fa per la probabilità $P(T)$ di un albero di una PCFG: si **moltiplicano le probabilità** (stessa assunzione di indipendenza).

### Probabilità totale

Ogni stringa $w$ può essere generata da **più sequenze di stati** $Seq$ (in numero **esponenziale**). Quella sopra è, come si vedrà, **quella che dà la probabilità di generazione massima** per $w = true\ false\ true\ true$.

$$P(w) = \sum_{Seq \in Seq(w)} P(Seq, w)$$

dove $Seq(w)$ è l'insieme delle sequenze di stati che possono generare $w$, cioè tali che $|Seq| = N(w)$.

- Se si fissa una lunghezza $k$ delle stringhe, $P(w)$ è una distribuzione sulle stringhe $w$ di lunghezza $k$ (per avere una distribuzione bisogna **evitare stringhe che sono prefisso di un'altra**).
- Per rappresentare un **linguaggio formale probabilistico**, cioè una distribuzione su stringhe di **lunghezza illimitata**, si richiede la generazione di un **token finale speciale $\langle EOS \rangle$**.

---

## 14. Terminating HMM

Un **Terminating HMM** continua a generare token **finché non viene generato un $\langle EOS \rangle$**. Per generare la frase $w = o_{1:N}$ deve generare anche $o_{N+1} = \langle EOS \rangle$, quindi $N(w) = |w| + 1 = N + 1$, tramite sequenze di stati $Seq$ con $|Seq| = N + 1$.

### Come language model

- L'osservazione speciale $\langle EOS \rangle$ **non è inclusa nell'alfabeto dei dati $\Sigma$**.
- Come per le PCFG, generano una categoria (lessicale), eventualmente diversa, **per ogni token** della frase.
- Invece di usare una CFG probabilistica per generare tali categorie, usano una **catena di Markov**:
  - gli **stati** rappresentano tutte le possibili categorie (lessicali);
  - una **transizione probabilistica $p$** dalla categoria $Categ$ alla categoria $Categ'$ significa: data la categoria del token corrente $Categ$, la probabilità che la categoria del token successivo sia $Categ'$ è $p$.
- Come per le PCFG, ogni categoria è associata (funzione di emissione) a una **distribuzione di probabilità sui token**: simboli di $\Sigma$ oppure il token $\langle EOS \rangle$.

### Confronto con la Mixture of BOW e con le reti neurali

- Come nel Mixture of BOW: generazione **sequenziale da sinistra a destra**; nei terminating HMM **non si fissa una lunghezza massima**: si ripete finché non esce $\langle EOS \rangle$.
- Meccanismo simile alla **generazione autoregressiva** delle reti neurali (per esempio **RNN, LLM / Transformer**). Differenza: nelle reti neurali la generazione del token successivo $P(t_{i+1} \mid t_1, \dots, t_i)$ si basa **direttamente sui token già generati** $t_1, \dots, t_i$, **non** su stati nascosti probabilistici.

### Mixture of BOW come Terminating HMM

Il Mixture of BOW è semplicemente un terminating HMM in cui gli stati sono le categorie possibili e:

- la **distribuzione iniziale degli stati** è la distribuzione sulle categorie del Mixture of BOW;
- ogni stato ha un **self-loop con probabilità 1**;
- le **emissioni** di ogni stato sono le probabilità condizionate sui token di ciascuna categoria del Mixture of BOW.

Nell'esempio: stati $Business$ (iniziale 0.6) e $Weather$ (iniziale 0.4), ciascuno con self-loop di probabilità 1; $Business$ emette $\langle EOS \rangle$ con 0.08, $[cloudy]$ con 0.01, $[continued]$ con 0.04, ...; $Weather$ emette $\langle EOS \rangle$ con 0.08, $[cloudy]$ con 0.16, $[continued]$ con 0.04, ...

### Non tutti i terminating HMM sono consistenti

Come per le PCFG, **non tutti** i terminating HMM rappresentano un linguaggio formale probabilistico. Esempio: un HMM con uno **stato iniziale $Categ$** (cioè con $\pi(Categ) > 0$) da cui **non è raggiungibile alcuno stato terminante**: non c'è un cammino di transizioni di probabilità non nulla che porti a uno stato $Categ'$ tale che $P(\langle EOS \rangle \mid Categ') > 0$.

### HMM consistente

Un terminating HMM è **consistente** se la somma delle probabilità di tutte le $w \in \Sigma^*$ è 1:

$$\sum_{w \in \Sigma^*} P(w) = 1$$

Un terminating HMM consistente **rappresenta un linguaggio formale probabilistico**.

**Caratterizzazione:** un terminating HMM è consistente **se e solo se**

- da **ogni stato raggiungibile** da uno stato iniziale $Categ$ (cioè con $\pi(Categ) > 0$)
- è possibile **raggiungere uno stato terminante** $Categ'$ (cioè con $P(\langle EOS \rangle \mid Categ') > 0$).

("Raggiungibile" significa che esiste un cammino di transizioni con probabilità non nulla.)

È simile alla caratterizzazione della consistenza per le PRG. I terminating HMM che rappresentano un Mixture of BOW sono **consistenti**.

---

## 15. Equivalenza tra modelli

### Equivalenza di generative model e language model

Due generative model $M_1$ e $M_2$ con lo **stesso spazio dei dati $D$** sono **equivalenti** se rappresentano la **stessa distribuzione di probabilità su $D$**.

- A differenza dell'equivalenza forte, qui **il processo di generazione** (comprese le classificazioni/etichette) **viene ignorato**.
- In particolare, due language model $M_1$ e $M_2$ con lo stesso alfabeto $\Sigma$ sono equivalenti se rappresentano la stessa distribuzione su $\Sigma^*$.

Esempio: il terminating HMM e il Mixture of BOW del nostro esempio sono **equivalenti**.

In generale, per ogni Mixture of BOW esiste un terminating HMM consistente equivalente. **Ma il viceversa non vale.**

### PRG $\Leftrightarrow$ Terminating HMM

> Per ogni PRG (consistente) su $\Sigma$ esiste un terminating HMM (consistente) equivalente su $\Sigma$, e viceversa.

**Dimostrazione** (le PRG generano le stringhe da sinistra a destra come gli HMM). È facile se si parte da un terminating HMM:

- si prendono come **variabili della PRG gli stati dell'HMM**;
- per ogni coppia di categorie si considera
  - $C \to o\, C'\ [p \cdot p']$, dove $p$ è la probabilità che $C$ emetta $o \neq \langle EOS \rangle$ e $p'$ è la probabilità che $C$ transiti in $C'$;
  - $C \to \epsilon\ [p]$, dove $p$ è la probabilità che $C$ emetta $\langle EOS \rangle$;
- poi si applica la **normale eliminazione delle produzioni $\epsilon$**, che **preserva** il linguaggio formale probabilistico rappresentato.

(Il verso opposto, da PRG a HMM, non è svolto in dettaglio nelle slide.)

**Conseguenze:**

- i **PRL** (Probabilistic Regular Languages) sono anche la classe dei linguaggi formali probabilistici dei terminating HMM (consistenti);
- la classe dei linguaggi probabilistici dei **Mixture of BOW** è una **sottoclasse stretta** dei PRL.

### Riassunto: espressività

$$\text{Mixture of BOW} \subsetneq \text{HMM (terminating)} \equiv \text{PRG} \subsetneq \text{PCFG}$$

- gli HMM sono **strettamente più espressivi** del Mixture of BOW e **strettamente meno espressivi** delle PCFG;
- sono **automi a stati finiti probabilistici generativi**, equivalenti alle grammatiche regolari probabilistiche.

Gli HMM sono usati non solo per i linguaggi formali probabilistici (cioè con $\Sigma^*$ come dataset, **language model**), ma anche per altri tipi di **modellazione generativa di sequenze**.

---

## 16. HMM per il POS tagging

### Cos'è

Un'applicazione storica tipica dei terminating HMM è il **POS tagging** (*Part of Speech*, etichettatura delle parti del discorso).

- L'obiettivo è **categorizzare le parole** secondo la loro **categoria lessicale**: sostantivo, verbo, aggettivo, ecc.
- Di solito le categorie sono più specifiche e includono **informazioni morfologiche**: per esempio il tag **VBZ** significa "verbo, terza persona singolare, presente".
- Cattura relazioni tra tag nella lingua, come "in inglese gli aggettivi vengono generalmente **prima** dei nomi" (in altre lingue, come francese o italiano, in genere è il contrario).
- Rispetto alle PCFG è un modello **più semplice**: considera solo le **dipendenze tra categorie lessicali** (tag POS) e **non la struttura sintattica** delle frasi.

### Il Penn Treebank

Il corpus del Penn Treebank usa **45 tag** e contiene **oltre tre milioni di parole** annotate con tag POS. Estratto dalla slide:

```
IN   DT  NN    ,  PRP  VBD  DT  NN      IN    JJ     NNS       TO  VB
From the start , it   took a   person  with  great  qualities to  succeed
```

"from" è etichettato come preposizione (IN), "the" come determinante (DT), ecc.

### Come funziona il modello

Rispetto all'esempio dell'ombrello (osservazioni: ombrello sì/no; stati nascosti: giorno Rainy/Sunny):

- nel POS tagging le **osservazioni sono le parole (token)** e gli **stati nascosti sono le categorie lessicali**;
- l'HMM dice che per produrre il linguaggio si **parte da uno stato**, come IN (preposizioni), e poi si fanno **due scelte**:
  - **quale parola emettere** (per esempio "from");
  - **quale stato viene dopo** (per esempio DT);
- il modello **non considera nessun contesto** oltre allo stato POS corrente.

### Limiti e successo

- Quando genera frasi, l'HMM **non ha idea della struttura della frase**. Eppure è un modello **utile**.
- Data una frase, se si inferiscono gli **stati nascosti più probabili** che possono averla prodotta, l'etichettatura ha un'accuratezza molto alta: di solito intorno al **97%**.
- Il POS tagging è un primo passo utile per molti altri compiti di NLP che non richiedono la struttura sintattica completa.
- Anche in un compito semplice come la **sintesi vocale** (text-to-speech) è importante sapere che il sostantivo "record" si pronuncia in modo diverso dal verbo "record".

---

## 17. I compiti dei generative model

Compiti (calcolabili) di un generative model:

### Generation (generazione)

**Campionamento** (*sampling*) di valori dello spazio dei dati (e dei valori nascosti / di classificazione). Esempi:

- modello **PCFG**: campionare una frase (insieme all'albero sintattico usato per generarla);
- modello **Mixture of BOW**: campionare una frase (insieme alla categoria usata per generarla);
- modello **HMM**: campionare una sequenza di osservazioni (insieme alla sequenza di categorie usata per generarla).

### Inference (inferenza)

Dato un valore dello spazio dei dati:

- inferire la sua **verosimiglianza** (*likelihood*, cioè la probabilità totale di generazione);
- per i modelli con valori nascosti/di classificazione come i tre sopra, inferire (la probabilità del)le sue **etichette**: l'albero sintattico (parsing), la categoria, la sequenza di stati.

### Learning (apprendimento)

**Aggiornare i parametri del modello** dato un insieme di valori dello spazio dei dati (e di valori nascosti/di classificazione):

- tipicamente basato sulla **massimizzazione della verosimiglianza** di tale insieme;
- **apprendimento non supervisionato**: il dataset contiene solo valori dello spazio dei dati;
- **apprendimento supervisionato**, per modelli con valori nascosti/di classificazione (etichette): il dataset contiene valori dello spazio dei dati **annotati/etichettati**;
- per i language model il dataset si chiama **corpus di frasi**.

---

## Da ricordare

- **Phrase structure grammar** = CFG; NP, VP sono variabili; i terminali sono i **token**.
- **Tokenizzazione**: il testo diventa una stringa in $\Sigma^*$ con $\Sigma$ = vocabolario + punteggiatura.
- **PCFG** $= (V, \Sigma, P, S, D)$: ogni produzione ha una probabilità e per ogni variabile le probabilità **sommano a 1**.
- **Probabilità di derivazione / albero** = prodotto delle probabilità delle produzioni. **Probabilità di una stringa** $P_G(w)$ = **somma** sulle probabilità dei suoi alberi (esempio "I can fish": $0.00096 + 0.0002304 = 0.0011904$). L'albero più probabile risolve l'ambiguità.
- **Linguaggio formale probabilistico** = distribuzione $P$ su $\Sigma^*$ con $\sum_w P(w) = 1$. **Linguaggio sottostante** = $\{w \mid P(w) > 0\}$.
- **Consistenza**: $P_G = \sum_w P_G(w) = 1$. Condizione necessaria: ogni variabile raggiungibile è generante. **Non basta** ($S \to a\ [0.4] \mid SS\ [0.6]$ ha $P_G = \tfrac{2}{3}$: più di una replica media per ramo). Nelle PCFG **non si eliminano** le non generanti.
- **PRG**: la condizione necessaria è anche **sufficiente** (al più una variabile nei corpi, niente repliche multiple). **PRL** $\subset$ **PCFL**; i loro linguaggi sottostanti sono regolari e liberi.
- **Generative model**: rappresentazione finita (parametri) di una distribuzione su uno spazio di dati (+ etichette). **Language model**: spazio dei dati $\Sigma^*$.
- **Mixture of BOW**: scelgo una categoria, poi genero tutte le parole indipendentemente dal suo sacchetto fino a $\langle EOS \rangle$; $P(w) = \sum_{Categ} P(Categ) \prod_i P(t_i \mid Categ)$.
- **PFA**: automa con distribuzioni sulle destinazioni, **non generativo**, **reattivo**; con le ricompense diventa **MDP**; con osservazioni parziali **POMDP**.
- **Catena di Markov** $(Q, \delta, \pi)$; **HMM** $= (Q, \Sigma, \delta, E, \pi)$: stati nascosti che **emettono** osservazioni. $P(Seq, w) = P(Seq) \prod_i P(o_i \mid Categ_i)$ (esempio: $0.0672 \cdot 0.2 \cdot 0.8 \cdot 0.9 \cdot 0.9 = 0.00870912$).
- **Terminating HMM**: genera fino a $\langle EOS \rangle$. Consistente **se e solo se** da ogni stato raggiungibile si può raggiungere uno stato terminante.
- **Equivalenza**: Mixture of BOW $\subsetneq$ terminating HMM $\equiv$ PRG $\subsetneq$ PCFG.
- **POS tagging**: HMM con tag come stati e parole come osservazioni (Penn Treebank, 45 tag, accuratezza ~97%).
- **Compiti**: generation (sampling), inference (likelihood, etichette), learning (massimizzazione della verosimiglianza, supervisionato o no).