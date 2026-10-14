## Linguaggi Regolari
### Linguaggio Formale

> [!quote] Ricorda
>Se $\Sigma$ è un _alfabeto_ e $L\subseteq\Sigma^*$ allora $L$ è un **_linguaggio formale_**.

### Proprietà di Chiusura dei Linguaggi Regolari

> [!question] Che cos'è una chiusura?
> Dimostrare che i linguaggi regolari sono **chiusi rispetto a un'operazione** (es. l'unione) significa dire che se prendi due linguaggi regolari e li unisci, il linguaggio risultante è ancora regolare.

> [!abstract] Sommario dell Proprietà 
> ![[Pasted image 20261014145822.png]]

---
#### Dimostrazioni delle proprietà


> [!abstract] Chiusura rispetto all'unione ($L \cup M$)
> Se $L$ ed $M$ sono linguaggi regolari, allora anche $L \cup M$ è un linguaggio regolare.
> 
> **Dimostrazione:**
> Poiché $L$ ed $M$ sono regolari, sono descritti da due espressioni regolari, $L = L(R)$ e $M = L(S)$.  
> Per definizione dell'operatore $+$ nelle espressioni regolari:
> $$L \cup M = L(R + S)$$
> Quindi $L \cup M$ è un linguaggio regolare. $\blacksquare$

---

> [!abstract] Chiusura rispetto al complemento ($\overline{L}$)
> Se $L$ è un linguaggio regolare sull'alfabeto $\Sigma$, allora anche $\overline{L} = \Sigma^* - L$ è un linguaggio regolare.
> 
> **Dimostrazione:**
> Sia $L = L(A)$ per un **DFA** $A = (Q, \Sigma, \delta, q_0, F)$.  
> Costruiamo un nuovo DFA $B = (Q, \Sigma, \delta, q_0, Q \setminus F)$.  
> 
> $B$ è identico ad $A$, ma gli stati di accettazione di $A$ diventano non-accettanti in $B$, e viceversa.  
> 
> Una stringa $w \in L(B) \iff \hat{\delta}(q_0, w) \in Q \setminus F \iff w \notin L(A) \iff w \in \overline{L}$.  
> Quindi $L(B) = \overline{L}$. $\blacksquare$
>
> > [!warning] WARNING
> > La funzione di transizione estesa $\hat{\delta}(q_0, w)$ deve essere **sempre definita**: non ci devono essere transizioni mancanti nel DFA $A$ (l'automa deve essere completo).
> >
> > > [!danger] CRITICAL
> > > Se mancano delle transizioni, alcune stringhe non condurrebbero ad alcun stato di $A$ (né accettante né non accettante), finendo per mancare sia in $L(A)$ che in $L(B)$.

---

> [!abstract] Chiusura rispetto all'intersezione ($L \cap M$)
> Se $L$ ed $M$ sono linguaggi regolari, allora anche $L \cap M$ è un linguaggio regolare.
> 
> **Dimostrazione (via De Morgan):**
> Per le leggi di De Morgan:
> $$L \cap M = \overline{\overline{L} \cup \overline{M}}$$
> Poiché i linguaggi regolari sono chiusi rispetto al complemento e all'unione, anche $L \cap M$ è regolare. $\blacksquare$
> 
> **Dimostrazione alternativa (Automa Prodotto / Simulazione in Parallelo):**
> Siano $A_L = (Q_L, \Sigma, \delta_L, q_L, F_L)$ e $A_M = (Q_M, \Sigma, \delta_M, q_M, F_M)$ due DFA per $L$ ed $M$.  
> 
> Costruiamo l'automa prodotto $A = (Q_L \times Q_M, \Sigma, \delta, (q_L, q_M), F_L \times F_M)$ che li esegue in parallelo:
> $$\delta((p, q), a) = (\delta_L(p, a), \delta_M(q, a))$$
> Lo stato $(p, q)$ è accettante se e solo se $p \in F_L$ **E** $q \in F_M$.  
> Quindi $A$ accetta $w \iff w \in L$ e $w \in M$. $\blacksquare$
> 
> ![[Pasted image 20261014173919.png]]

---

> [!abstract] Chiusura rispetto alla differenza ($L \setminus M$)
> Se $L$ ed $M$ sono linguaggi regolari, allora anche la differenza $L \setminus M$ (o $L - M$) è un linguaggio regolare.
> 
> **Dimostrazione:**
> Per le proprietà degli insiemi:
> $$L \setminus M = L \cap \overline{M}$$
> $\overline{M}$ è regolare (chiusura sul complemento) e l'intersezione tra $L$ e $\overline{M}$ è regolare (chiusura sull'intersezione).  
> Di conseguenza, $L \setminus M$ è regolare. $\blacksquare$

---

> [!abstract] Chiusura rispetto all'inversione ($L^R$)
> Se $L$ è un linguaggio regolare, lo è anche il suo inverso $L^R = \{ w^R \mid w \in L \}$.
> 
> **Dimostrazione:**
> Sia $L$ riconosciuto da un automa $A$. Costruiamo $A^R$ che riconosce $L^R$:
> 1. Invertiamo il verso di tutti gli archi;
> 2. Il vecchio stato iniziale diventa l'unico stato finale;
> 3. Creiamo un **nuovo stato iniziale** $p_0$ e aggiungiamo transizioni-$\epsilon$ da $p_0$ verso tutti i vecchi stati finali di $A$.
> 
> $A^R$ accetta tutte e sole le stringhe di $L$ lette al contrario. $\blacksquare$

---

> [!abstract] Altre Chiusure
> 
> **Concatenazione ($L \cdot M$):**  
> Se $L = L(R)$ e $M = L(S)$, l'espressione regolare $R \cdot S$ descrive $L \cdot M$.
> 
> **Chiusura di Kleene ($L^*$):**  
> Se $L = L(R)$, l'espressione regolare $R^*$ descrive $L^*$.

---

### Problemi di decisione per i Linguaggi Regolari

Lo studio degli algoritmi per questioni sui linguaggi regolari prende le mosse da un
riepilogo dei modi per tradurre una rappresentazione in un’altra dello stesso linguaggio.

> Note: In particolare la complessità in tempo degli algoritmi di conversione

1. il linguaggio descritto è vuoto $L = \emptyset$?
2. una certa stringa w appartiene al linguaggio descritto $w \in L$?
3. due descrizioni definiscono lo stesso linguaggio?

#### 1. Verificare se un Linguaggio Regolare è vuoto $L = \emptyset$

> [!hint] Osservazione
> **Base:** lo stato iniziale è sicuramente raggiungibile dallo stato iniziale
>
> **Induzione:** se lo stato $q$ è raggiungibile dallo stato iniziale e c'è un arco da $q$ a $p$ con etichetta arbitraria, anche $p$ è raggiungibile

##### Prima Soluzione

Utilizzando il passo induttivo appena descritto si determina l’insieme degli stati raggiungibili. Se in questo insieme c’è uno stato accettante, la risposta è “no” (il linguaggio dell’automa non è vuoto), altrimenti è “si”

##### Seconda Soluzione

Si osserve l'espressione regolare $E$ di lunghezza $n$

> [!hint] Osservazione
> 