## Automi a stati finiti (FA) - Concetti base

**ALFABETO**: Insieme finito e non vuoto di simboli, si rappresenta con $\Sigma={a,...,z}$
	_Può essere **binario** se l'insieme ha 2 elementi_

**STRINGA**: Sequenza finita di simboli da un alfabeto $\Sigma$ 
- si dice **Stringa** **Vuota** se ha zero occorrenze dei simboli da $\Sigma$ , e si rappresenta con $\epsilon$

> [!tip] Ricorda
$|w|$ denota la lunghezza della stringa $w$   
   > - *Ex* :   $|0110| = 4, |\epsilon| = 0$ 

CONCATENAZIONE:  Se $x$ e $y$ sono stringhe allora $xy$ è la stringa ottenuta collocando una copia di $y$ subito dopo una copia di $x$:
$$x = a_1a_2 . . . a_i, y = b_1b_2 . . . b_j xy = a_1a_2 . . . a_ib_1b_2 . . . b_j$$
> [!faq]- Esempio di concatenazione
> Esempio: $x = 01101, y = 110, xy = 01101110$ 
> 
> Nota: Per ogni stringa $x$ tale che $x\epsilon = \epsilon x = x$

**POTENZE DI UN ALFABETO** $\Sigma^k$ = Insieme delle stringhe di lunghezza $k$ con simboli da  $\Sigma$ 

> [!danger] Attenzione
> $\Sigma^0 = {\epsilon}$  e **non** è un insieme vuoto

L’insieme di tutte le potenze dell'alfabeto $\Sigma$ è $\Sigma^∗$ 
- $\Sigma^∗ = \Sigma^0 \cup \Sigma^1 \cup \Sigma^2 \cup · · ·$
	- Nel dettaglio sarebbe:
			-  **Chiusura Positiva** ($\Sigma^+) = \Sigma^1 \cup \Sigma^2 \cup \Sigma^3 \cup{...}$
			-  **Chiusura di Kleene** ($\Sigma^∗) = \Sigma^+ \cup \{\epsilon\}$


**Linguaggio formale**: Se $\Sigma$ è un alfabeto, e $L \subset \Sigma^*$ allora $L$ è un linguaggio (formale)

> [!faq]- Esempio di linguaggio
> Insieme delle stringhe che consistono di $n$ zeri seguita da $n$ uni 
	> -  $\{\epsilon, 01, 0011, 000111, . . .\}$
		>	- $L = {0^n, 1^n \, \, | \, \, n >= 0}$

> [!info] Ricorda
> Il linguaggio vuoto è $\emptyset$
> Il linguaggio {$\epsilon$} consiste nella stringa vuota, e **NON** è vuoto

## Automi a stati finiti deterministici (DFA)

Un ==DFA è una quintupla==: $A=  (Q, \Sigma,\delta,q_0,F)$
- **Q** è un insieme finito di stati
- **$\Sigma$** è un alfabeto finito, ossia i simboli in input
- $\delta$ è una funzione di transizione $Q \times \Sigma$ a $Q$ cioè: $(q,a) \rightarrow p$       
		 _"ad ogni coppia stato-simbolo c'è solo una transizione"_
- $q_0 \in Q$ è lo stato iniziale
- $F \subseteq Q$ è un insieme di stati finali

> [!info] Quand'è deterministico?? 
> È deterministico quando ogni stato ha una ed una sola transizione uscente etichettata con un elemento dell'alfabeto.
>

> [!quote] Fatti a cazzo di cane
> - Il fatto che sia deterministico, implica che si ha un **solo** cammino, e non si bloccherà.
>- Dato un automa a stati finiti sigma, riesco ad individuare l'insieme delle stringhe accettate su un alfabeto $\Sigma$, e quindi definire un linguaggio formale.

> [!faq]  Esempio deterministico
>![[Pasted image 20260917121519.png]]
Affinchè un automa sia considerato valido, deve raggiungere uno stato di accettazione (doppio cerchio), ossia deve avere uno stato per cui qualsiasi cosa viene inserita dopo, è valida.
> - Lo stato di accettazione è lo stato in cui si trova l'automa dopo aver divorato tutta la stringa in input

> [!danger] Occhio
Nell'esempio, viene alla fine definito un linguaggio formale, che accetta TUTTE le stringhe che siano però formate da 01 all'inizio, quindi $01y$

### Funzione di transizione estesa

La funzione di transizione $\delta$ può essere estesa a $\hat\delta$ che opera su stati e stringhe (invece che su stati e simboli):
- Base: $\hat\delta(q,\epsilon)=q$
- Induzione: $\hat\delta(q,xa) = \delta(\hat\delta(q,x),a)$

Definiamo quindi il linguaggio accettato da un automa a stati finiti deterministico A come: $$L(A) = \{ w: \hat\delta(q0,w) \in F \}$$
I linguaggi accettati da automi a stati finiti sono detti **LINGUAGGI REGOLARI**

> [!faq] Esempio: Calcoliamo $\hat\delta(q_0,0110)$
> - $\hat\delta(q_0, \epsilon) = q_0$
>- $\hat\delta(q_0, \epsilon0) = \delta(\hat\delta(q_0,\epsilon),0) = \delta(q_0,0) = q_2$
> - $\hat\delta(q_0, 01) = \delta(\hat\delta(q_0,0),1) = \delta(q_2,1) = q_1$
> - $\hat\delta(q_0, 011) = \delta(\hat\delta(q_0,01),1) = \delta(q_1,1) = q_1$
> - $\hat\delta(q_0, 011) = \delta(\hat\delta(q_0,011),0) =  \delta(q_1,0) = q_1$

## Automi a stati finiti nondeterministici (NFA)

Un NFA accetta una stringa se, tra i tanti possibili, esiste un cammino che conduce ad uno stato finale.
![[Pasted image 20260924102130.png]]
> [!attention] Differenze da un classico **DFA**:
>- $q_0$ ha 2 transizioni uscenti etichettate con 0
>- $q_1$ e $q_2$ non hanno transizione uscenti per ogni simbolo  

> [!faq] Cosa succede quando l'automa elabora l'input `00101`?
> ![[Pasted image 20260924102915.png]]
> **Dopo aver visto tutte le strade possibili, basta che esiste una strada che porti ad uno stato di accettazione.**

### Definizione Formale

Un ==NFA è una quintupla==: $A=  (Q, \Sigma,\delta,q_0,F)$
- **Q** è un insieme finito di stati
- **$\Sigma$** è un alfabeto finito, ossia i simboli in input
- $\delta$ è una funzione di transizione $Q \times \Sigma$ all'insieme dei sottoinsiemi di $Q$ cioè: $$(q,a) \rightarrow Q' \quad con \quad Q'\subseteq Q$$
		_ora non ho più un unico stato di arrivo per ogni coppia stato-simbolo_
- $q_0 \in Q$ è lo stato iniziale
- $F \subseteq Q$ è un insieme di stati finali

![[Pasted image 20260924104015.png|404]]
### Stato Pozzo

> stato che, durante un'esecuzione di un **DFA**, non ha transizioni che cambiano effettivamente stato.

> [!danger] All'interno dei NFA lo stato pozzo viene rappresentato dall'assenza di transizioni

> [!attention] Lo stato pozzo non è mai uno stato di accettazione, lo stato pozzo è "l'infelicità eterna"
### Funzione di transizione estesa

La funzione di transizione $\delta$ può essere estesa a $\hat\delta$ che opera su stati e stringhe (invece che su stati e simboli):

- Base: $\hat\delta(q,\epsilon)= \{q\}$
- Induzione:  $\widehat{\delta}(q, xa) = \displaystyle\bigcup_{p \in \widehat{\delta}(q, x)} \delta(p, a)$

Definiamo quindi il linguaggio accettato da un automa a stati finiti **nondeterministico** A come: $$L(A) = \{ w: \hat\delta(q_0,w) \cap F \neq \emptyset \}$$


> [!faq] Esempio: Calcoliamo $\hat\delta(q_0,0010)$
> - $\hat\delta(q_0, \epsilon) = \{q_0\}$
>- $\hat\delta(q_0, \epsilon0) = \delta(q_0,0) = \{q_0,q_1\}$
>			_"quando mi magno lo 0 posso essere in $q_0$ o $q_1$"_
> - $\hat\delta(q_0, 00) = \delta(q_0,0) \cup \delta(q_0,0) = \{q_0,q_1\} \cup \emptyset = \{q_0,q_1\}$
> - $\hat\delta(q_0, 001) = \delta(q_0,1) \cup \delta(q_1,1) = \{q_0\} \cup \{q_2\} = \{q_0,q_2\}$ 
> - $\hat\delta(q_0, 0010) = \delta(q_0,0) \cup \delta(q_2,0) = \{q_0,q_1\} \cup \emptyset = \{q_0,q_1\}$

## Equivalenza di DFA e NFA

> [!abstract] Per ogni NFA $N$ c'è un DFA $D$, tale che $L(D) = L(N)$, e viceversa.

> [!hint]  Idea
> Uno stato del DFA è rappresentato da un'insieme di stati dell'NFA

> [!danger] Costruzione a livello matematico 
>  Dato un NFA   $N=(Q_N,\Sigma,\delta_N,q_0,F_N)$
 > Costruiamo un DFA   $D=(Q_D,\Sigma,\delta_D,\{q_0\},F_D)$
>  tali che 
>  - $L(D)=L(N)$
>$\quad$  
> - $Q_D=\{S:S\subseteq Q_N\}$
> - $|Q_D| =2^{|Q_N|}$ , anche se la maggior parte degli stati in $Q_D$ sono ”garbage”, cioè non raggiungibili dallo stato iniziale.
> $\quad$  
>- $F_D = \{S \subseteq Q_N : S \cap F_N \neq \emptyset\}$
> - Per ogni $S \subseteq Q_N$ e $a \in \Sigma$ $$\delta_D(S, a) = \bigcup_{p \in S} \delta_N(p, a)$$
>

### Esempio di trasformazione 
![[Pasted image 20260924113435.png|570]]![[Pasted image 20260924113453.png]]
 _calcoliamo le transizione da ogni combinazione di stati, quando calcoliamo la transizione da un insieme (es. $\{q_0,q_1\}$) calcoliamo l'unione tra le transizioni dei singoli stati (es. da $q_0$ con 1 posso andare a $q_0$, da $q_1$ con 1 posso andare a $q_2$)_

> [!danger] All'effettivo **NON** serve analizzare **TUTTI** gli stati ma solo quelli raggiungibili

![[Pasted image 20260924114557.png|582]]
 _gli stati del DFA sono denonminati tramite sottoinsiemi 
 es. $\{q_0,q_1\}$ è il nome di un singolo state, fottitene se è un insieme, possiamo chiamarlo anche "pippo"_
 ![[Pasted image 20260924114611.png|584]]
> [!question] Come sappiamo se uno stato è quello finale?
>  **Se almeno uno stato del sottoinsieme è un stato finale**

> [!danger] Nel caso in cui una transizione porti all'insieme vuoto (banalmente non ha una transizione di arrivo) si parla di **stato pozzo**

### Teoremi
![[Pasted image 20260924121037.png|519]]

## NFA con transizioni epsilon ($\epsilon^-NFA$)

> [!question] Cosa sono le transizioni epsilon?
> Sono transizioni che permettono il passaggio di stato senza "mangiare" l'input
#### **Esempio:** facciamo un NFA per leggere i floating point

Definiamo l'alfabeto:
1. un segno $+$ o $-$, opzionale
2. una stringa di cifre decimali
3. un punto decimale _(scegliamo punto perchè internazionale)_
4. un'altra stringa di cifre decimali
![[Pasted image 20260924121520.png|425]]
Le transizioni epsilon sono le transizioni **opzionali**, ovvero che hanno $\epsilon$
_nell'immagine sono le transizioni $q_0\rightarrow q_1$ e $q_3\rightarrow q_5$ 

> [!question] A cosa serve $q_4$ ?
> serve a permettere la scrittura di numeri senza alcun carattere dopo il `.`

---
---

un $\epsilon^-NFA$ è una quintupla $(Q,\Sigma,\delta, q_0, F)$ dove $\delta$ è una funzione da $Q \times (\Sigma \cup \{\epsilon\})$ all'insieme dei sottoinsiemi $Q$
![[Pasted image 20260924122532.png|457]]
### Epsilon-Chiusura

> [!abstract] Chiudiamo uno stato aggiungendo tutti gli stati raggiungibili da lui tramite una sequenza (0 o più) transizioni-$\epsilon$

>**$ECLOSE(q)$** è la definizione di $\epsilon$-closure, matematicamente definito come:
>**Base**:
>$\quad q\in ECLOSE(q)$
>**Induzione**:
>$\quad p \in ECLOSE(q) \quad and \quad r \in \delta(p,\epsilon)$
>$\quad r\in ECLOSE(q)$

![[Pasted image 20260924122754.png|443]]

> [!danger] Definizione induttiva di $\hat{\delta}$ per automi $\epsilon$-NFA
**Base:**
$$ \hat{\delta}(q, \epsilon) = \text{ECLOSE}(q) $$
**Induzione:**
$$ \hat{\delta}(q, xa) = \bigcup_{p \in \hat{\delta}(q, x)} \left( \bigcup_{t \in \delta(p, a)} \text{ECLOSE}(t) \right) $$
>- *Linguaggio $L$ accettato è ancora definito* $\{ w : \hat{\delta}(q_0, w) \cap F \neq \emptyset \}$

## Equivalenza di DFA e $\epsilon^-NFA$

![[Pasted image 20260924124516.png|534]]

Dato un $\epsilon$-NFA: $\quad E = (Q_E, \Sigma, \delta_E, q_0, F_E)$
Possiamo costruire un DFA equivalente: $\quad D = (Q_D, \Sigma, \delta_D, q_D, F_D)$
tale che $L(D) = L(E)$.
### Dettagli della costruzione

- **Insieme degli stati ($Q_D$):**
  L'insieme degli stati di $D$ comprende i sottoinsiemi di $Q_E$ che sono chiusi rispetto alla $\epsilon$-chiusura:$$Q_D = \left\{ S \subseteq Q_E \mid S = \bigcup_{s \in S} \text{ECLOSE}(s) \right\}$$
- **Stato iniziale ($q_D$):**$$q_D = \text{ECLOSE}(q_0)$$
- **Insieme degli stati finali ($F_D$):**$$F_D = \{ S \in Q_D \mid S \cap F_E \neq \emptyset \}$$
- **Funzione di transizione ($\delta_D$):**
  Per ogni $S \in Q_D$ e per ogni $a \in \Sigma$:$$\delta_D(S, a) = \bigcup_{t \in S} \left( \bigcup_{p \in \delta_E(t, a)} \text{ECLOSE}(p) \right)$$
![[Pasted image 20260924124732.png|512]]_Praticamente la stessa equivalenza di prima in cui consideriamo con transizioni da $q_i$ anche il suo $ECLOSE(q_i)$_


> [!question] Quali sono gli stati di accettazione?
> Quelli che contengono almeno uno stato di accettazione nell'automa originale.


