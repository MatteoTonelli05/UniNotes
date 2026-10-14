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

> [!important] Unione $L \cup M$
> **Teorema:** Per ogni coppia di linguaggi regolari $L \text{e} M, L \cup M$ è regolare.
> **Dimostrazione:** Sia $L = L(E) \text{e} M = L(F)$. Allora $L(E + F) = L \cup M$ per definizione.

> [!important] Complemento ($\overline{L}$)
> **Teorema:** Se $L$ e’ un linguaggio regolare su $\Sigma$, allora anche $\overline{L} = \Sigma^* \backslash L$ è regolare
> **Prova:** Sia $L$ riconosciuto da un DFA $A = (Q, \Sigma, \delta, q_0, F)$
>Sia $B = (Q, \Sigma, \delta, q_0, Q \setminus F)$. Allora $L(B) = \overline{L}$.
    
> [!important] Chiusura di Kleene / Star ($L^*$)
    

