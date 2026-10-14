## Linguaggi Regolari
### Linguaggio Formale

> [!quote] Ricorda
>Se $\Sigma$ è un _alfabeto_ e $L\subseteq\Sigma^*$ allora $L$ è un **_linguaggio formale_**.

### Proprietà dei linguaggi regolari

> [!question] Che cos'è una chiusura?
> Dimostrare che i linguaggi regolari sono **chiusi rispetto a un'operazione** (es. l'unione) significa dire che se prendi due linguaggi regolari e li unisci, il linguaggio risultante è ancora regolare.

> [!abstract] Sommario dell Proprietà 
> ![[Pasted image 20261014145822.png]]

---
#### Dimostrazioni delle proprietà

> [!important] Chiusura rispetto all'Unione $L \cup M$
> **TEOREMA:** Per ogni coppia di linguaggi regolari $L \text{e} M, L \cup M$ è regolare.
> 
> **DIMOSTRAZIONE:** Poiché L e M sono regolari, essi sono descritti da espressioni regolari, poniamo $L = L(R) \text{ e } M = L(S)$. Allora $L \cup M = L(R + S)$ per la definizione dell’operatore $+$ delle espressioni regolari.

> [!abstract] Teorema 3.3.2: Chiusura rispetto al complemento
> Se $L$ è un linguaggio regolare sull'alfabeto $\Sigma$, allora anche $\overline{L} = \Sigma^* - L$ è regolare.
> 
> > [!cite] DIMOSTRAZIONE
> > Sia $L = L(A)$ per un **DFA** $A = (Q, \Sigma, \delta, q_0, F)$.  
> > Allora $\overline{L} = L(B)$, dove $B$ è il **DFA** $(Q, \Sigma, \delta, q_0, Q - F)$.  
> > 
> > In altre parole, $B$ è esattamente come $A$, ma gli stati accettati di $A$ sono diventati stati non accettanti di $B$, e viceversa.  
> > 
> > Allora $w \in L(B) \iff \hat{\delta}(q_0, w) \in Q - F \iff w \notin L(A)$. $\blacksquare$
>
> > [!warning] WARNING
> > È importante che $\hat{\delta}(q_0, w)$ sia **sempre definito**; vale a dire che **non ci siano transizioni mancanti** in $A$.
> >
> > > [!danger] CRITICAL
> > > Nel caso in cui manchino delle transizioni, alcune stringhe potrebbero non condurre ad alcuno stato di $A$ (né accettante né non accettante) e le stringhe verrebbero a mancare sia in $L(A)$ che in $L(B)$.


