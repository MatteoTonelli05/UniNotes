## Automi a stati finiti - Concetti base

**Alfabeto**: Insieme finito e non vuoto di simboli, si rappresenta con $Σ$ 

**Stringa**: Sequenza finita di simboli da un alfabeto.
- *Vuota*: Con zero occorrenze dei simboli da alfabeto
- Si rappresenta la vuota con $ϵ$-

> [!tip] Ricorda
$|w|$ denota la lunghezza della stringa $w$   
   > - *Ex* :   $|0110| = 4, |ϵ| = 0$ 

**Concatenazione**:  Se $x$ e $y$ sono stringhe allora $xy$ è la stringa ottenuta collocando una copia di $y$ subito dopo una copia di $x$:
$$x = a1a2 . . . ai, y = b1b2 . . . bj xy = a1a2 . . . aib1b2 . . . bj$$
> [!faq]- Esempio di concatenazione
> Esempio: $x = 01101, y = 110, xy = 01101110$ 
> 
> Nota: Per ogni stringa $x xϵ = ϵx = x$

**Potenze di un alfabeto** $Σ^k$ = Insieme delle stringhe di lunghezza $k$ con simboli da  $Σ$ 

> [!danger] Attenzione
> $Σ^0 = {ϵ}$  e **non** è un insieme vuoto

L’insieme di tutte le stringhe di $Σ$ è $Σ^∗$ 
- $Σ^∗ = Σ^0 ∪ Σ^1 ∪ Σ^2 ∪ · · ·$
	- L’operatore in questione è la ==stella di Kleene==
		- Nel dettaglio sarebbe:
			-  $Σ^+ = Σ^1 ∪ Σ^2 ∪ Σ^3 ∪ · ·$
			-  $Σ^∗ = Σ+ ∪$ {ϵ}


**Linguaggio formale**: Se $\Sigma$ è un alfabeto, e $L \subset \Sigma^*$ allora $L$ è un linguaggio (formale)

> [!faq]- Esempio di linguaggio
> Insieme delle stringhe che consistono di $n$ zeri seguita da $n$ uni 
	> -  ${ϵ, 01, 0011, 000111, . . .}$
		>	- $L = {0^n, 1^n \, \, | \, \, n >= 0}$

> [!info] Ricorda
> Il linguaggio vuoto è $∅$
> Il linguaggio {ϵ} consiste nella stringa vuota, e **NON** è vuoto

### Automi a stati finiti deterministici

Un ==DFA è una quintupla==: $A=  (Q, \Sigma,\delta,q0,F)$
- **Q** è un insieme finito di stati
- **$\Sigma$** è un alfabeto finito, ossia i simboli in input
- $\delta$ è una funzione di transizione $Q x \Sigma x Q$ cioè $(q,a \rightarrow p)$
- $q_0 \in Q$ è lo stato iniziale
- $F \subset Q$ è un insieme di stati finali

> [!info] Quand è deterministico?? 
> È deterministico quando ogni stato ha una ed una sola transizione uscente etichettata con un elemento dell'alfabeto.
>- Il fatto che sia deterministico, implica che si ha un **solo** cammino, e non si bloccherà.
	>	- Dato un automa a stati finiti sigma, riesco ad individuare l'insieme delle stringhe accettate su un alfabeto $\Sigma$, e quindi definire un linguaggio formale.

> [!faq]  Esempio deterministico
>![[Pasted image 20260917121519.png]]
Affinchè un automa sia considerato valido, deve raggiungere uno stato di accettazione (doppio cerchio), ossia deve avere uno stato per cui qualsiasi cosa viene inserita dopo, è valida.
> - Lo stato di accettazione è lo stato in cui si trova l'automa dopo aver divorato tutta la stringa in input

> [!danger] Occhio
Nell'esempio, viene alla fine definito un linguaggio formale, che accetta TUTTE le stringhe che siano però formate da 01 all'inizio, quindi $01y$

#### Funzione di transizione estesa

La funzione di transizione $\delta$ può essere estesa a $\hat\delta$ che opera su stati e stringhe (invece che su stati e simboli):
- Base: $\hat\delta(q,e)=q$
- Induzione: $\hat\delta(q,xa) = \delta(\hat\delta(q,x),a)$

Possiamo dire quindi che il linguaggio accettato da un automa a stati finiti deterministico A è: $L(A) = \{ w: \hat\delta(q0,w) \in F \}$
- I linguaggi accettati da automi a stati finiti sono detti **linguaggi regolari**

> [!faq] Esempio: Calcoliamo $\hat\delta(q_0,0110)$
> - $\hat\delta(q_0, \epsilon) = q_0$
>- $\hat\delta(q_0, \epsilon0) = \delta(\hat\delta(q_0,\epsilon),0) = \delta(q_0,0) = q_2$
> - $\hat\delta(q_0, 01) = \delta(\hat\delta(q_0,0),1) = \delta(q_2,1) = q_1$
> - $\hat\delta(q_0, 011) = \delta(\hat\delta(q_0,01),1) = \delta(q_1,1) = q_1$
> - $\hat\delta(q_0, 011) = \delta(\hat\delta(q_0,011),0) =  \delta(q_1,0) = q_1$
