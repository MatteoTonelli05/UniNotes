> Un ***Finite Automaton*** (DFA o NFA) è un metodo per costruire una macchina che riconosce **linguaggio regolare**

>[!abstract] Espressioni regolari
>Una **espressione regolare** è un modo dichiarativo per **descrivere un linguaggio regolare**
>
>_Esempio `01*+10*`_

Invece di disegnare una macchina, usiamo un **linguaggio di formule** (molto usato nella programmazione reale, come nei comandi `grep` o nei parser).

Per costruire queste formule utilizziamo:

- **Unione:** $L \cup M = \{w : w \in L \text{ oppure } w \in M\}$
    
- **Concatenazione:** $L.M = \{w : w = xy, x \in L, y \in M\}$
    
- **Chiusura di Kleene:** $L^* = \bigcup_{i \ge 0} L^i \qquad$ _(la ripetizione di L per 0,1,2... volte)_
	
> [!important] Per tutte le formule valgono le normali regole algebriche (commutatività, associatività, distributività), con un'unica eccezione: la concatenazione **non è commutativa** ($ab \neq ba$).
> ![[Pasted image 20261014102825.png]]
> ![[Pasted image 20261014102836.png]]
> ![[Pasted image 20261014102848.png]]

---

### Costruire le espressioni regolari

_Ricorda: $\epsilon$ è la stringa vuota, mentre $\emptyset$ è l'insieme vuoto_

> [!abstract] Definizione induttiva
>
> ***Base***: $\epsilon$ e $\emptyset$ sono espressioni regolari
>- Se $a\in\Sigma$, allora $a$ è un'espressione regolare che descrive il linguaggio $L(a)=\{ a \}$
>
> ***Induzione***:
>
>1. Se $E$ è un'espressione regolare allora $(E)$ è un'espressione regolare
$$$L((E))=L(E)$$
>
>2. Se $E$ e $F$ sono espressioni regolari. allora $E+F$ è un'espressione regolare
$$L(E+F)=L(E)\cup L(F)$$
>
>3. Se $E$ e $F$ sono espressioni regolari allora $E.F$ è una un'espressione regolare
$$L(E.F)=L(E).L(F)$$
>
>4. Se $E$ è un'espressione regolare allora $E^{*}$ è un'espressione regolare
$$L(E^{*})=(L(E))^{*}$$


> [!example] Esempio
> Vogliamo scrivere un'espressione regolare per descrivere tutte le stringhe composte da `0` e `1` in cui i caratteri si alternano sempre (es. `0101`, `10101`, `0`, `1`, $\epsilon$, ecc.).
> 
>- **Prima forma (Unione di tutte le combinazioni possibili):**
    >$$(01)^* + (10)^* + 0(10)^* + 1(01)^*$$
>
> 	 - $(01)^*$ gestisce le stringhe pari che iniziano con 0 (es. `0101`).
>    
>	- $(10)^*$ gestisce le stringhe pari che iniziano con 1 (es. `1010`).
>  
>	- $0(10)^*$ gestisce le stringhe dispari che iniziano con 0 (es. `01010`).
>  
>	- $1(01)^*$ gestisce le stringhe dispari che iniziano con 1 (es. `10101`).
>  
>- **Seconda forma (Più compatta):**
  >  
>	$$(\epsilon + 1)(01)^*(\epsilon + 0)$$
>
>	- $(\epsilon + 1)$: la stringa può opzionalmente iniziare con `1`.
>  
>	- $(01)^*$: poi c'è il blocco centrale di coppie `01` riprodotto zero o più volte.
> 
>	- $(\epsilon + 0)$: infine, la stringa può opzionalmente finire con `0`

> [!summary] Ordine di precedenza degli operatori
> Proprio come nelle espressioni matematiche dove la moltiplicazione ($\cdot$) ha la precedenza sull'addizione ($+$), anche nelle espressioni regolari c'è una gerarchia per evitare di dover mettere troppe parentesi:
> 
> 1. Chiusura ($^{*}$)
> 2. Concatenazione ($.$)
> 3. Più ($+$)

---

### Equivalenza di FA e espressioni regolari

