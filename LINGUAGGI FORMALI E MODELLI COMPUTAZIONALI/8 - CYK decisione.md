## Proprietà di decisione per CFL

> [!abstract] Problema **Decidibile**
>Un problema è **decidibile** se esiste un algoritmo che risponde sempre correttamente sì/no.

Analizzeremo i seguenti problemi **decidibili**:
- verificare se $w \in L(G)$, per una stringa $w$ e una CFG $G$;
- verificare se $L(G) \neq \emptyset$, per una CFG $G$.
E elencheremo alcuni problemi **indecidibili** (vedi sezione finale).

---

## Appartenenza: $w \in L(G)$?

### Tecnica inefficiente

Supponiamo che $G$ sia in **CNF** e che $|w| = n$.

Un albero sintattico in CNF è **binario**: per una stringa di lunghezza $n$ ci sono **$2n - 1$ nodi interni**.

> Perché $2n - 1$: ci sono $n - 1$ usi di $A \to BC$ e $n$ usi di $A \to a$, e ogni passo di derivazione è un nodo interno.

Basta generare **tutti** gli alberi sintattici di $G$ con $2n - 1$ nodi interni e controllare se almeno uno ha prodotto $w$.

Il numero di alberi/etichettature possibili è **esponenziale** in $n$, quindi l'algoritmo è esponenziale.

> Perché serve la CNF: senza, le produzioni $\epsilon$ e unità renderebbero la lunghezza delle derivazioni non limitata, e la ricerca non avrebbe un limite.

---

### Algoritmo CYK

Nome dagli inventori: **C**ocke, **Y**ounger, **K**asami.

Assumiamo $G$ in CNF e $w = a_1 a_2 \cdots a_n$.

Costruiamo una **tabella triangolare** dove $X_{ij}$ è l'insieme di tutte le variabili $A$ tali che

$$A \Rightarrow^*_G a_i a_{i+1} \cdots a_j$$

cioè le variabili che possono generare la sottostringa da $i$ a $j$.

```
X15
X14 X25
X13 X24 X35
X12 X23 X34 X45
X11 X22 X33 X44 X55
 a1  a2  a3  a4  a5
```

La cella $X_{ij}$ si trova nella **riga $j - i + 1$**, cioè la lunghezza della sottostringa.

#### Come si riempie

Si riempie **riga per riga, dal basso verso l'alto**. La prima riga è il passo base, le successive il passo induttivo.

**Base:**

$$X_{ii} = {A : A \to a_i \text{ è in } G}$$

**Induzione:** calcoliamo $X_{ij}$ (riga $j - i + 1$). Si ha $A \in X_{ij}$ se e solo se:

- $A \Rightarrow^* a_i a_{i+1} \cdots a_j$, che equivale a dire che $A \to BC$ e, per qualche $i \leq k < j$, vale $B \Rightarrow^* a_i \cdots a_k$ e $C \Rightarrow^* a_{k+1} \cdots a_j$;
- ma questo equivale a controllare che esista una produzione $A \to BC$ e un $k$ con $i \leq k < j$ tale che

$$B \in X_{ik} \quad \text{e} \quad C \in X_{(k+1)j}$$

> Perché è giusto: se la sottostringa ha lunghezza $\geq 2$, in CNF il primo passo di $A \Rightarrow^* a_i \cdots a_j$ è per forza $A \to BC$. Allora $B$ deriva un prefisso non vuoto (da $i$ a $k$) e $C$ il resto (da $k+1$ a $j$). Per questo si provano **tutti** i punti di taglio $k$.

**Obiettivo:** calcolare $X_{1n}$.

$$w \in L(G) \iff S \in X_{1n}$$

---

#### Esempio

$$\begin{align} S &\to AB \mid BC \ A &\to BA \mid a \ B &\to CC \mid b \ C &\to AB \mid a \end{align}$$

con $w = baaba$ (quindi $a_1 = b$, $a_2 = a$, $a_3 = a$, $a_4 = b$, $a_5 = a$).

Tabella (colonna = posizione di partenza $i$, riga = lunghezza):

|lunghezza|$i=1$|$i=2$|$i=3$|$i=4$|$i=5$|
|---|---|---|---|---|---|
|5|${S,A,C}$|||||
|4|$\emptyset$|${S,A,C}$||||
|3|$\emptyset$|${B}$|${B}$|||
|2|${S,A}$|${B}$|${S,C}$|${S,A}$||
|1|${B}$|${A,C}$|${A,C}$|${B}$|${A,C}$|
||$b$|$a$|$a$|$b$|$a$|

**Riga 1 (base).** $B \to b$ dà $X_{11} = X_{44} = {B}$. $A \to a$ e $C \to a$ danno $X_{22} = X_{33} = X_{55} = {A, C}$.

**Alcuni calcoli per capire la regola:**

- $X_{12}$: $X_{11} \times X_{22} = {B} \times {A, C}$, cioè $BA$ e $BC$. Da $A \to BA$ e $S \to BC$ si ottiene ${S, A}$.
- $X_{23}$: ${A,C} \times {A,C}$, cioè $AA, AC, CA, CC$. Solo $CC$ compare in un corpo ($B \to CC$), quindi ${B}$.
- $X_{34}$: ${A,C} \times {B}$, cioè $AB$ e $CB$. $AB$ compare in $S \to AB$ e $C \to AB$, quindi ${S, C}$.
- $X_{13}$: due tagli. $k=1$: $X_{11} \times X_{23} = BB$ (nessuna regola). $k=2$: $X_{12} \times X_{33} = {S,A} \times {A,C}$ (nessuna regola). Quindi $\emptyset$.
- $X_{25}$: tre tagli. $k=2$: ${A,C} \times {B}$ dà $AB$, quindi $S$ e $C$. $k=3$: ${B} \times {S,A}$ dà $BA$, quindi $A$. $k=4$: ${B} \times {A,C}$ dà $BA$ e $BC$, quindi $A$ e $S$. Unione: ${S, A, C}$.
- $X_{15}$: $k=1$: $X_{11} \times X_{25} = {B} \times {S,A,C}$ dà $BS, BA, BC$, quindi $A$ e $S$. $k=2$: $X_{12} \times X_{35} = {S,A} \times {B}$ dà $SB, AB$, quindi $S$ e $C$. $k=3$ e $k=4$ danno $\emptyset$ (perché $X_{13} = X_{14} = \emptyset$). Unione: ${S, A, C}$.

$S \in X_{15}$, quindi **$baaba \in L(G)$**.

---

#### Costo computazionale

Per calcolare $X_{ij}$ si analizzano al più $n$ coppie di insiemi già calcolati:

$$(X_{ii}, X_{i+1,j}),\ (X_{i,i+1}, X_{i+2,j}),\ \dots,\ (X_{i,j-1}, X_{jj})$$

Per $w = a_1 \cdots a_n$ ci sono $O(n^2)$ insiemi $X_{ij}$ da calcolare, e per ognuno si analizzano $n$ coppie.

Il costo computazionale totale è quindi

$$O(n^3)$$

(polinomiale, molto meglio dell'esponenziale della tecnica ingenua; la dimensione della grammatica è considerata costante).

---

### Generare gli alberi sintattici tramite backpointers

Se $w \in L(G)$ si può usare CYK per generare **tutti i possibili alberi sintattici** di $w$ (**parsing** di $w$).

- Si memorizzano esplicitamente i **backpointers** ai simboli che hanno causato l'aggiunta di una variabile a un insieme.
- Se una variabile viene aggiunta **più volte** allo stesso insieme (come, nell'esempio, la variabile $S$ nel ${S,A,C}$ di $X_{15}$), si memorizzano **tutte le coppie alternative** di backpointers.
- Poi si fa **traceback** a partire da $S$ nell'insieme $X_{1n}$, seguendo i backpointers, per costruire tutti gli alberi sintattici di $w$.

Nell'esempio, $S \in X_{15}$ ha due alternative:

- $S \to BC$ con $B \in X_{11}$ e $C \in X_{25}$;
- $S \to AB$ con $A \in X_{12}$ e $B \in X_{35}$.

Quindi $baaba$ ha **due alberi sintattici** ed è ambigua in $G$ (stesso concetto dell'ambiguità vista all'inizio del capitolo, ora rilevato da un algoritmo).

**Note:**

- Il traceback costruisce una quantità **esponenziale** di alberi (una specifica alternativa per ogni variabile che ne ha più d'una), ma a partire da una rappresentazione **polinomiale** della "foresta" di alberi (i sottoalberi sono condivisi).
- La grammatica era stata trasformata in CNF. Conservando abbastanza informazioni **durante la conversione** si possono poi ricostruire gli alberi sintattici di $w$ nella grammatica **originale** $G$.

---

## Verificare se un CFL è non vuoto

$$L(G) \neq \emptyset \iff \text{il simbolo iniziale } S \text{ è generante}$$

(definizione di "generante": vedi nota _CNF e semplificazioni CFG_, sezione simboli inutili).

- Un'implementazione naive del calcolo dei simboli generanti di $G$ richiede tempo $O(n^2)$.
- Ottimizzando le strutture dati di appoggio si può calcolare in tempo $O(n)$.

(Qui $n$ è la dimensione della grammatica.)

---

## Da ricordare

- **Appartenenza** $w \in L(G)$: decidibile. Tecnica ingenua: esponenziale. **CYK**: $O(n^3)$, richiede $G$ in **CNF**.
- **CYK**: tabella $X_{ij}$, base $X_{ii} = {A : A \to a_i}$, induzione sui tagli $k$ con $A \to BC$, $B \in X_{ik}$, $C \in X_{(k+1)j}$. Risposta: $S \in X_{1n}$.
- **Backpointers**: servono per il parsing (costruire gli alberi). Foresta polinomiale, alberi anche esponenziali.
- **Vuoto**: $L(G) \neq \emptyset \iff S$ generante.

---

## Proprietà di chiusura dei CFL

**Chiuso** rispetto a un'operazione: applicandola a CFL si ottiene ancora un CFL. Per i regolari erano chiusi unione, concatenazione, star, intersezione, complemento. Per i CFL **solo alcune** valgono.

### Ciò che vale

**Teorema 7.24.** I CFL sono chiusi rispetto a:

1. **unione**
2. **concatenazione**
3. **chiusura di Kleene** $^*$ e **chiusura positiva** $^+$

_Prova: per esercizio._ Schema (dal libro): si rinominano le variabili in modo che siano disgiunte, si aggiunge una nuova variabile iniziale $S$ e le regole

- unione: $S \to S_1 \mid S_2$
- concatenazione: $S \to S_1 S_2$
- star: $S \to S_1 S \mid \epsilon$
- plus: $S \to S_1 S \mid S_1$

**Teorema (rovesciamento).** Se $L$ è CFL, allora lo è anche $L^R$.

_Prova._ Sia $L$ generato da $G = (V, T, P, S)$. Costruiamo $G^R = (V, T, P^R, S)$ dove

$$P^R = {A \to \alpha^R : A \to \alpha \in P}$$

cioè si **rovesciano i corpi** delle produzioni. Si mostra per induzione sulla lunghezza delle derivazioni in $G$ e in $G^R$ che $(L(G))^R = L(G^R)$.

---

### I CFL NON sono chiusi rispetto all'intersezione

Sia $L_1 = {0^n 1^n 2^i : n \geq 1, i \geq 1}$. È CFL, con grammatica

$$\begin{align} S &\to AB \ A &\to 0A1 \mid 01 \ B &\to 2B \mid 2 \end{align}$$

Inoltre $L_2 = {0^i 1^n 2^n : n \geq 1, i \geq 1}$ è CFL, con grammatica

$$\begin{align} S &\to AB \ A &\to 0A \mid 0 \ B &\to 1B2 \mid 12 \end{align}$$

Invece

$$L_1 \cap L_2 = {0^n 1^n 2^n : n \geq 1}$$

**non è CFL**.

> Intuizione (non è una dimostrazione): avendo a disposizione un'unica pila, la riempio con gli 0 e la svuoto con gli 1. A quel punto la pila è vuota e non riesco a controllare che anche i 2 siano tanti quanti gli 0.

---

### Operazioni su liberi e regolari

**Teorema 7.27.** Se $L$ è CFL e $R$ è regolare, allora $L \cap R$ è CFL.

_Prova._ Sia $L$ accettato dal PDA

$$P = (Q_P, \Sigma, \Gamma, \delta_P, q_P, Z_0, F_P)$$

per stato finale, e sia $R$ accettato dal DFA

$$A = (Q_A, \Sigma, \delta_A, q_A, F_A)$$

Costruiamo un PDA per $L \cap R$: i due automi girano **in parallelo** (nella figura: stato del PDA e stato del DFA, con un AND finale sull'accettazione). La pila la gestisce solo il PDA.

Formalmente:

$$P' = (Q_P \times Q_A, \Sigma, \Gamma, \delta, (q_P, q_A), Z_0, F_P \times F_A)$$

dove

$$\delta((q, p), a, X) = {((r, \hat{\delta}_A(p, a)), \gamma) : (r, \gamma) \in \delta_P(q, a, X)}$$

In parole:

- lo stato di $P'$ è una **coppia** (stato di $P$, stato di $A$);
- quando $P$ fa una mossa leggendo $a$, il DFA fa la mossa su $a$ (se $a = \epsilon$ il DFA non si muove: $\hat{\delta}_A(p, \epsilon) = p$);
- $P'$ accetta se **entrambi** sono in stati finali.

> Collegamento: è la costruzione del **prodotto** tra due DFA vista per i regolari. Qui basta che **uno solo** sia un DFA, perché un solo automa può usare la pila. Con due PDA servirebbero due pile: è il motivo per cui l'intersezione tra due CFL non è chiusa.

---

**Teorema 7.29.** Siano $L, L_1, L_2$ CFL e $R$ regolare. Allora:

1. $L \setminus R$ è CFL.
2. $\overline{L}$ **non** è necessariamente CFL.
3. $L_1 \setminus L_2$ **non** è necessariamente CFL.

_Prova._

1. $\overline{R}$ è regolare, $L \cap \overline{R}$ è CFL (Teo. 7.27) e $L \cap \overline{R} = L \setminus R$.
2. Se $\overline{L}$ fosse sempre CFL, allora per De Morgan

$$L_1 \cap L_2 = \overline{\overline{L_1} \cup \overline{L_2}}$$

sarebbe sempre CFL (l'unione è chiusa). Ma l'intersezione non è chiusa. Contraddizione.

3. $\Sigma^_$ è CFL (è regolare). Se $L_1 \setminus L_2$ fosse sempre CFL, allora lo sarebbe sempre anche $\Sigma^_ \setminus L = \overline{L}$, contro il punto 2.

**Riassunto chiusure:**

|Operazione|CFL chiusi?|
|---|---|
|unione, concatenazione, $^*$, $^+$|sì|
|rovesciamento $L^R$|sì|
|intersezione con un regolare|sì|
|differenza $L \setminus R$ con $R$ regolare|sì|
|intersezione di due CFL|**no**|
|complemento|**no**|
|differenza di due CFL|**no**|

---

## Problemi indecidibili per CFL

**Indecidibile** = non esiste alcun algoritmo che risponda sempre correttamente.

I seguenti problemi sono indecidibili:

1. Una data CFG $G$ è **ambigua**?
2. Un dato CFL $L$ è **inerentemente ambiguo**?
3. L'intersezione di due CFL è **vuota**?
4. Due CFL sono **uguali**?
5. Un CFL è **universale** (cioè uguale a $\Sigma^*$)?

Collegamenti:

- Il punto 1 spiega perché non esiste un algoritmo per eliminare l'ambiguità: non ne esiste nemmeno uno per riconoscerla.
- Il punto 2 si riferisce all'ambiguità inerente (esempio ${a^n b^n c^m d^m} \cup {a^n b^m c^m d^n}$).
- I punti 4 e 5 sono decidibili per i linguaggi regolari, ma non per i CFL.
- Contrastano con i problemi **decidibili** visti sopra: appartenenza e vuoto.

---

## PDA deterministici (DPDA)

Un PDA $P = (Q, \Sigma, \Gamma, \delta, q_0, Z_0, F)$ è **deterministico** se e solo se:

1. ogni $\delta(q, a, X)$, con $a \in \Sigma \cup {\epsilon}$, contiene **al più un elemento**;
2. se $\delta(q, a, X)$ è non vuoto per un $a \in \Sigma$, allora $\delta(q, \epsilon, X)$ è **vuoto** (non si può scegliere tra consumare un simbolo e fare una mossa $\epsilon$).

Quindi in ogni situazione la mossa successiva è determinata (o non c'è nessuna mossa).

### Esempio

$$L_{wcw^R} = {wcw^R : w \in {0,1}^*}$$

(come $L_{ww^R}$, ma con un **marcatore $c$** al centro). È riconosciuto da un DPDA con 3 stati ($q_0$, $q_1$, $q_2$):

- in $q_0$: si impilano gli 0 e gli 1 letti
    - $0, Z_0 / 0Z_0$ e $1, Z_0 / 1Z_0$
    - $0, 0 / 00$ e $0, 1 / 01$ e $1, 0 / 10$ e $1, 1 / 11$
- $q_0 \to q_1$ leggendo la $c$: $c, Z_0 / Z_0$ e $c, 0 / 0$ e $c, 1 / 1$
- in $q_1$: si toglie dalla pila se coincide con l'input: $0, 0 / \epsilon$ e $1, 1 / \epsilon$
- $q_1 \to q_2$ (finale): $\epsilon, Z_0 / Z_0$

> Perché ora è deterministico: nel PDA per $L_{ww^R}$ bisognava **indovinare** dov'è la metà con una transizione $\epsilon$. Qui la $c$ dice dove sta il centro, e non c'è più nulla da indovinare.

---

### Regolari $\subseteq L(\text{DPDA}) \subseteq$ CFL

Mostreremo che

$$\text{Regolari} \subset L(\text{DPDA}) \subset \text{CFL}$$

**Teorema 6.17.** Se $L$ è regolare, allora $L = L(P)$ per qualche DPDA $P$.

_Prova._ Dato che $L$ è regolare, esiste un DFA $A$ tale che $L = L(A)$. Sia

$$A = (Q, \Sigma, \delta_A, q_0, F)$$

Definiamo il DPDA

$$P = (Q, \Sigma, {Z_0}, \delta_P, q_0, Z_0, F)$$

dove

$$\delta_P(q, a, Z_0) = {(\delta_A(q, a), Z_0)}$$

La pila contiene sempre solo $Z_0$: il DPDA simula il DFA e **ignora la pila**.

---

### La gerarchia

- Abbiamo visto che $\text{Regolari} \subseteq L(\text{DPDA})$.
- Ovviamente $L(\text{DPDA}) \subseteq \text{CFL}$ (un DPDA è un PDA).
- $L_{wcw^R} \in L(\text{DPDA}) \setminus \text{Regolari}$ (non è regolare: stesso argomento del pumping usato per le palindrome).
- Ci sono linguaggi in $\text{CFL} \setminus L(\text{DPDA})$? **Sì**, per esempio $L_{ww^R}$ (la slide lo afferma senza dimostrarlo; intuizione: senza marcatore una macchina deterministica non può sapere dove è la metà).

---

### DPDA e non ambiguità

**Teorema 6.21.** Se $L = L(P)$ per qualche DPDA $P$, allora $L$ ha una CFG **non ambigua** $G$.

_Intuizione._ Essendo $P$ deterministico è possibile ottenere una CFG $G$ con **derivazioni a sinistra uniche** per ogni stringa (collegamento al teorema: albero unico $\iff$ derivazione a sinistra unica).

**Il viceversa non vale.**

Esempio: $L_{ww^R}$ ha una grammatica non ambigua

$$S \to 0S0 \mid 1S1 \mid \epsilon$$

ma **non appartiene** a $L(\text{DPDA})$.

Quindi $L(\text{DPDA})$ è una **sottoclasse stretta** dei CFL aventi grammatiche non ambigue (cioè non inerentemente ambigui).

```
Regolari
  ⊂ L(DPDA)                         (es. w c w^R)
  ⊂ CFL non inerentemente ambigui   (es. w w^R: grammatica non ambigua, ma niente DPDA)
  ⊂ CFL                             (es. {a^n b^n c^m d^m} ∪ {a^n b^m c^m d^n}: inerentemente ambiguo)
```

---

## Da ricordare (blocco chiusura, indecidibilità, DPDA)

- **Chiusi**: unione, concatenazione, $^*$, $^+$, rovesciamento, intersezione con un regolare, differenza con un regolare.
- **Non chiusi**: intersezione di due CFL (controesempio $0^n 1^n 2^n$), complemento, differenza di due CFL.
- **Intersezione CFL $\cap$ regolare**: prodotto PDA $\times$ DFA, stati a coppie, accetta se entrambi accettano.
- **Indecidibili**: ambiguità di una CFG, ambiguità inerente, intersezione vuota, uguaglianza, universalità.
- **DPDA**: $\le 1$ mossa per situazione, nessuna scelta tra input e $\epsilon$. Regolari $\subset L(\text{DPDA}) \subset$ CFL.
- **$L_{wcw^R} \in L(\text{DPDA})$**, **$L_{ww^R} \notin L(\text{DPDA})$** (serve il non determinismo).
- **DPDA $\Rightarrow$ grammatica non ambigua**, ma non viceversa.