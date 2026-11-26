## Proprietà di decisione per CFL

> [!abstract] Problema **Decidibile**
>Un problema è **decidibile** se esiste un algoritmo che risponde sempre correttamente sì/no.

Analizzeremo i seguenti problemi **decidibili**:
1. verificare se $w \in L(G)$, cioè se la grammatica $G$ può generare $w$.
2. verificare se $L(G) \neq \emptyset$, per una CFG $G$.
E elencheremo alcuni problemi **indecidibili**

---

## 1. Appartenenza: $w \in L(G)$?

### Tecnica inefficiente

Supponiamo che $G$ sia in **CNF** e che $|w| = n$.

Un albero sintattico in CNF è **binario**: per una stringa di lunghezza $n$ ci sono **$2n - 1$ nodi interni**.

> [!question] Perché $2n - 1$? 
> Ci sono $n - 1$ usi di $A \to BC$ e $n$ usi di $A \to a$, e ogni passo di derivazione è un nodo interno.

Basta generare **tutti** gli alberi sintattici di $G$ con $2n - 1$ nodi interni e controllare se almeno uno ha prodotto $w$.

> [!fail] Il numero di alberi/etichettature possibili è **esponenziale** in $n$, quindi l'algoritmo è esponenziale.

> [!question] Perché serve la CNF? 
> Senza le produzioni $\epsilon$ e unità renderebbero la lunghezza delle derivazioni non limitata, e la ricerca non avrebbe un limite.

---
### Algoritmo CYK

Assumiamo $G$ in CNF e che $w = a_1 a_2 \cdots a_n$.

Costruiamo una **tabella triangolare** dove $X_{ij}$ è l'insieme di tutte le variabili $A$ tali che
$$A \Rightarrow^*_G a_i a_{i+1} \cdots a_j$$
cioè le variabili che possono generare la sottostringa da $i$ a $j$.

![[Pasted image 20261124181551.png]]
> [!hint] La cella $X_{ij}$ si trova nella **riga $j - i + 1$**, cioè la lunghezza della sottostringa.

#### Come si riempie

> [!abstract] Idea
> Per sapere se S genera tutta la stringa, ti chiedi quali variabili generano **ogni sua sottostringa**, partendo da quelle di una lettera e allargando.

Si riempie **riga per riga, dal basso verso l'alto**. 
La prima riga è il passo base, le successive il passo induttivo.

**BASE:**$\qquad X_{ii} = \{A : A \to a_i \text{ è in } G\}$

**INDUZIONE:** $\qquad$ calcoliamo $X_{ij}$ (riga $j - i + 1$). Si ha $A \in X_{ij}$ se e solo se:

- $A \Rightarrow^* a_i a_{i+1} \cdots a_j$, che equivale a dire che $A \to BC$ e, per qualche $i \leq k < j$, vale $B \Rightarrow^* a_i \cdots a_k$ e $C \Rightarrow^* a_{k+1} \cdots a_j$;
- ma questo equivale a controllare che esista una produzione $A \to BC$ e un $k$ con $i \leq k < j$ tale che
$$B \in X_{ik} \quad \text{e} \quad C \in X_{(k+1)j}$$

> [!question] Perchè è giusto? 
> Se la sottostringa ha lunghezza $\geq 2$, in CNF il primo passo di 
> $A \Rightarrow^* a_i \cdots a_j$ è per forza $A \to BC$. Allora $B$ deriva un prefisso non vuoto (da $i$ a $k$) e $C$ il resto (da $k+1$ a $j$). Per questo si provano **tutti** i punti di taglio $k$.

**Obiettivo:** calcolare $X_{1n}$ : si ha $w \in L(G) \text{ se e solo se } S \in X_{1n}$

---
#### Esempio
$G$ ha le produzioni
$$\begin{align} S &\to AB \mid BC &\ A &\to BA \mid a &\ B &\to CC \mid b &\ C &\to AB \mid a \end{align}$$

con $w = baaba$ (quindi $a_1 = b$, $a_2 = a$, $a_3 = a$, $a_4 = b$, $a_5 = a$).

![[Pasted image 20261125135614.png|499]]
$S \in X_{15}$, quindi **$baaba \in L(G)$**.

> [!quote] Riempimento in parole povere
> **Passo 1** -  guardi ogni lettera e scrivi le variabili che hanno una regola `**A → lettera**`
> **Passo 2** -  Ti chiedi: **quale variabile può produrre "una variabile della prima + una variabile della seconda"?**
> > [!example] **"ba" (lettere 1 e 2):**
> > - la "b" è produttibile da {B}, la "a" da {A, C};
> >- le coppie possibili sono B·A e B·C;
> >- dalla tabellina: BA lo produce **A**, BC lo produce **S**;
> >- quindi la cella è **{S, A}**.
> 
> **Passo 3** - adatta il passo 2 agli altri livelli

 
---

#### Costo computazionale

Per calcolare $X_{ij}$ si analizzano al più $n$ coppie di insiemi già calcolati:
$$(X_{ii}, X_{i+1,j}),\ (X_{i,i+1}, X_{i+2,j}),\ \dots,\ (X_{i,j-1}, X_{jj})$$

Per $w = a_1 \cdots a_n$ ci sono $O(n^2)$ insiemi $X_{ij}$ da calcolare, e per ognuno si analizzano $n$ coppie.
Il costo computazionale totale è quindi $O(n^3)$

> [!hint] è polinomiale, molto meglio dell'esponenziale della tecnica ingenua; la dimensione della grammatica è considerata costante.

---
### Generare gli alberi sintattici tramite backpointers

Se $w \in L(G)$ si può usare CYK per generare **tutti i possibili alberi sintattici** di $w$ (**parsing** di $w$).

- Si memorizzano esplicitamente i **backpointers** ai simboli che hanno causato l'aggiunta di una variabile a un insieme.
![[Pasted image 20261125142923.png|358]]
- Se una variabile viene aggiunta **più volte** allo stesso insieme (come, nell'esempio, la variabile $S$ nel ${S,A,C}$ di $X_{15}$), si memorizzano **tutte le coppie alternative** di backpointers.
- Poi si fa **traceback** a partire da $S$ nell'insieme $X_{1n}$, seguendo i backpointers, per costruire tutti gli alberi sintattici di $w$.
![[Pasted image 20261125143516.png]]

> [!attention] Nell'esempio, $S \in X_{15}$ ha due alternative:
>
>- $S \to BC$ con $B \in X_{11}$ e $C \in X_{25}$;
>- $S \to AB$ con $A \in X_{12}$ e $B \in X_{35}$.
>
>Quindi $baaba$ ha **due alberi sintattici** ed è ambigua in $G$ (stesso concetto dell'ambiguità vista all'inizio del capitolo, ora rilevato da un algoritmo).

##### **Note**

- Il traceback costruisce una quantità teoricamente **esponenziale** di alberi (una specifica alternativa per ogni variabile che ne ha più d'una), ma a partire da una rappresentazione **polinomiale** della "foresta" di alberi (i sottoalberi sono condivisi).
	
- La grammatica era stata trasformata in CNF. Conservando abbastanza informazioni **durante la conversione** si possono poi ricostruire gli alberi sintattici di $w$ nella grammatica **originale** $G$.

---

## Verificare se un CFL è non vuoto

> [!hint] Ricorda $\iff$ significa "se e solo se"

$$L(G) \neq \emptyset \iff \text{il simbolo iniziale } S \text{ è generante}$$
- Un'implementazione naive del calcolo dei simboli generanti di $G$ richiede tempo $O(n^2)$.
- Ottimizzando le strutture dati di appoggio si può calcolare in tempo $O(n)$.

(Qui $n$ è la dimensione della grammatica.)

---
## Proprietà di chiusura dei CFL

> [!question] Cosa significa chiusura?
> **Chiuso** rispetto a un'operazione, significa che applicandola a CFL si ottiene ancora un CFL. 

![[Pasted image 20261125162335.png|626]]
**TEOREMA (rovesciamento).** Se $L$ è CFL, allora lo è anche $L^R$.

_Prova._ Sia $L$ generato da $G = (V, T, P, S)$. Costruiamo $G^R = (V, T, P^R, S)$ dove
$$P^R = {A \to \alpha^R : A \to \alpha \in P}$$
cioè si **rovesciano i corpi** delle produzioni. Si mostra per induzione sulla lunghezza delle derivazioni in $G$ e in $G^R$ che $(L(G))^R = L(G^R)$.

---

### I CFL NON sono chiusi rispetto all'intersezione

> [!abstract] Dimostrazione mediante controesempio

Sia $L_1 = \{0^n 1^n 2^i\} : n \geq 1, i \geq 1$. È CFL, con grammatica

$$\begin{align} S &\to AB \ A &\to 0A1 \mid 01 \ B &\to 2B \mid 2 \end{align}$$

Inoltre $L_2 = {0^i 1^n 2^n : n \geq 1, i \geq 1}$ è CFL, con grammatica

$$\begin{align} S &\to AB \ A &\to 0A \mid 0 \ B &\to 1B2 \mid 12 \end{align}$$

Invece

$$L_1 \cap L_2 = {0^n 1^n 2^n : n \geq 1}$$

**non è CFL**.

> Intuizione (non è una dimostrazione): avendo a disposizione un'unica pila, la riempio con gli 0 e la svuoto con gli 1. A quel punto la pila è vuota e non riesco a controllare che anche i 2 siano tanti quanti gli 0.

---

### Operazioni su liberi e regolari

![[Pasted image 20261126103745.png]]
##### Dimostrazione del teorema 7.27

Sia $L$ accettato dal PDA
$$P = (Q_P, \Sigma, \Gamma, \delta_P, q_P, Z_0, F_P)$$
per stato finale, e sia $R$ accettato dal DFA
$$A = (Q_A, \Sigma, \delta_A, q_A, F_A)$$
Costruiamo un PDA per $L \cap R$, i due automi girano **in parallelo**. La pila la gestisce solo il PDA.
![[Pasted image 20261126104138.png|444]]

Formalmente:
$$P' = (Q_P \times Q_A, \Sigma, \Gamma, \delta, (q_P, q_A), Z_0, F_P \times F_A)$$
dove

$$\delta((q, p), a, X) = {((r, \hat{\delta}_A(p, a)), \gamma) : (r, \gamma) \in \delta_P(q, a, X)}$$

> [!quote] In parole
>
>- lo stato di $P'$ è una **coppia** (stato di $P$, stato di $A$);
>- quando $P$ fa una mossa leggendo $a$, il DFA fa la mossa su $a$ (se $a = \epsilon$ il DFA non si muove: $\hat{\delta}_A(p, \epsilon) = p$);
>- $P'$ accetta se **entrambi** sono in stati finali.

> [!warning] Con due PDA servirebbero due pile: è il motivo per cui l'intersezione tra due CFL non è chiusa.

---

![[Pasted image 20261126104712.png]]
> [!hint] $\overline{L}$ è il complementare di $L$
##### Dimostrazione del teorema 7.29

1. $\overline{R}$ è regolare, $L \cap \overline{R}$ è CFL  e $L \cap \overline{R} = L \setminus R$.
2. Se $\overline{L}$ fosse sempre CFL, allora per De Morgan
$$L_1 \cap L_2 = \overline{\overline{L_1} \cup \overline{L_2}}$$

sarebbe sempre CFL (l'unione è chiusa). Ma l'intersezione non è chiusa. **Contraddizione**

3. $\Sigma^*$ è CFL (è regolare). Se $L_1 \setminus L_2$ fosse sempre CFL, allora lo sarebbe sempre anche $\Sigma^* \setminus L = \overline{L}$, contro il punto 2.

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

> [!abstract] **Indecidibile**  
> Non esiste alcun algoritmo che risponda sempre correttamente.

I seguenti problemi sono indecidibili:

1. Una data CFG $G$ è **ambigua**?
2. Un dato CFL $L$ è **inerentemente ambiguo**?
3. L'intersezione di due CFL è **vuota**?
4. Due CFL sono **uguali**?
5. Un CFL è **universale** (cioè uguale a $\Sigma^*$)?


> [!hint]
>- Il punto 1 spiega perché non esiste un algoritmo per eliminare l'ambiguità: non ne esiste nemmeno uno per riconoscerla.
>- Il punto 2 si riferisce all'ambiguità inerente (esempio ${a^n b^n c^m d^m} \cup {a^n b^m c^m d^n}$).
>- I punti 4 e 5 sono decidibili per i linguaggi regolari, ma non per i CFL.
>- Contrastano con i problemi **decidibili** visti sopra: appartenenza e vuoto.

---
## PDA deterministici (DPDA)

Un PDA $P = (Q, \Sigma, \Gamma, \delta, q_0, Z_0, F)$ è **deterministico** se e solo se:

1. ogni $\delta(q, a, X)$, con $a \in \Sigma \cup \{\epsilon\}$, contiene **al più un elemento**;
2. se $\delta(q, a, X)$ è non vuoto per un $a \in \Sigma$, allora $\delta(q, \epsilon, X)$ è **vuoto** (non si può scegliere tra consumare un simbolo e fare una mossa $\epsilon$).

> [!hint] Quindi è deterministico se in ogni situazione la mossa successiva è determinata (o non c'è nessuna mossa).

> [!example] Esempio
> Definiamo
$$L_{wcw^R} = {wcw^R : w \in {0,1}^*}$$
![[Pasted image 20261126110244.png]]


> [!questino] Perché ora è deterministico?
>  Nel PDA per $L_{ww^R}$ bisognava **indovinare** dov'è la metà con una transizione $\epsilon$. Qui la $c$ dice dove sta il centro, e non c'è più nulla da indovinare.

---

### Regolari $\subseteq L(\text{DPDA}) \subseteq$ CFL

![[Pasted image 20261126110441.png]]

Mostreremo che
$$\text{Regolari} \subset L(\text{DPDA}) \subset \text{CFL}$$

##### Dimostrazione Teorema 6.17

Dato che $L$ è regolare, esiste un DFA $A$ tale che $L = L(A)$. Sia
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
- Ci sono linguaggi in $\text{CFL} \setminus L(\text{DPDA})$? **Sì**, per esempio $L_{ww^R}$ 

---

### DPDA e non ambiguità
![[Pasted image 20261126110659.png]]
> [!hint] Essendo $P$ deterministico è possibile ottenere una CFG $G$ con **derivazioni a sinistra uniche** per ogni stringa (ricorda: albero unico $\iff$ derivazione a sinistra unica).

> [!warning] **Il viceversa non vale.**

> [!example] Esempio
> $L_{ww^R}$ ha una grammatica non ambigua
>$$S \to 0S0 \mid 1S1 \mid \epsilon$$
ma **non appartiene** a $L(\text{DPDA})$.
>
Quindi $L(\text{DPDA})$ è una **sottoclasse stretta** dei CFL aventi grammatiche non ambigue (cioè non inerentemente ambigui).
>

---
