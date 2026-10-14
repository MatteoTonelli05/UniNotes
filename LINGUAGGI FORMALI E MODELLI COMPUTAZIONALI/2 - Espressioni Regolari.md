## Espressioni Regolari

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

## Equivalenza di FA e espressioni regolari

> [!question] Le espressioni regolari possono formalizzare le stesse cose che formalizzano FA, DFA, NFA, $\epsilon$-NFA?
> Si, tramite alcuni trasformazioni infatti si può passare da uno all'altro
> 
> ![[Pasted image 20261014110429.png]]

### Da DFA a RE
![[Pasted image 20261014110606.png]]
##### 1. Eliminazione iterativa degli stati

![[Pasted image 20261014111313.png|291]]
Tutti gli archi che comprendono lo stato $s$ vengono **rimossi** e per compensare, viene *introdotta* per ogni predecessore ($q_{i}$) di $s$ e ogni successore $(p_{j})$ una ***espressione regolare*** che rappresenta *tutti i percorsi* che partono da $q_{i}$, passano da $s$ e finiscono in $p_{j}$.

> [!hint] L'espressione per questi percorsi è **$$Q_{i}+S^{*}P_{j}$$**

Questa espressione è aggiunta con l'operatore di unione all'arco da $q_{i}$ a $p_{j}$.
![[Pasted image 20261014112739.png|267]]
##### 2. Una volta rimossi tutti i nodi di accettazione...

...la strategia per costruire l'***espressione regolare*** è la seguente:
- Per ogni stato di accettazione $q$ applicare il processo di riduzione per creare un automa equivalente con espressioni regolari negli archi, eliminando tutti gli stati eccetto $q$ e lo stato iniziale $q_{0}$.
- Se $q\neq q_{0}$ allora si ha un automa con due stati come definito in figura.
![[Pasted image 20261014114915.png]]
> [!attention] Se lo *stato iniziale* è uno ***stato di accettazione***
>Va fatto una ulteriore riduzione, mantenendo lo stato iniziale.
>Una volta fatto questo, rimane uno *stato unico* con un **autociclo** che come etichetta contiene l'espressione regolare dell'automa.

---
#### Esempio da DFA a RE

Dato un DFA, che rappresenta tutte le stringhe che **NON** terminano con `01`:
![[Pasted image 20261014124634.png|552]]
> [!fail] Iniziamo a eliminare tutti i nodi che **non sono di accettazione** ($q_{2}$).
>Prendiamo tutte le transizioni passanti per $q_{2}$ entranti e uscenti
>		- **Predecessori**: $\{ q_{1} \}$
>		- **Successori**: $\{ q_{0},q_{1} \}$

Analizziamo tutte le *combinazioni* che partono dai **predecessori** e arrivano ai **successori**, calcolando $Q_{i}+S^{*}P_{j}$.

| Predecessore | Successore | Espressione                      |
| ------------ | ---------- | -------------------------------- |
| $q_{1}$      | $q_{0}$    | $\emptyset + 1\emptyset^{*}1=11$ |
| $q_{1}$      | $q_{1}$    | $0+1\emptyset^{*}0=0+10$         |
Sostituendo le espressioni regolari ottenute ed eliminando il nodo $q_{2}$ si ottiene il seguente *automa*.
![[Pasted image 20261014124830.png|543]]
> [!abstract] Sono tutti nodi di accettazione
> _guarda "Se lo *stato iniziale* è uno ***stato di accettazione***", bisogna eseguire un ulteriore riduzione_

Dobbiamo quindi fare un'ulteriore riduzione, ma non possiamo rimuovere $q_0$ poichè è un nodo iniziale.

1. Fisso $q_{1}$, lo chiamo $A_{q1}$ e calcolo l'espressione regolare del `DFA` risultante.
![[Pasted image 20261014125204.png|552]]

2. Usando la formula descritta in precedenza:
$$
E_{q1}=(1+0(0+10)^{*}11)^{*}.0(0+10)^{*}
$$

3. Ora fisso $q_{0}$ e lo chiamo $A_{q0}$
![[Pasted image 20261014125217.png|532]]
4. ***Elimino*** il nodo $q_{1}$.
	- **Predecessori**: $\{ q_{0} \}$
	- **Successori**: $\{ q_{0}\}$

| Predecessore | Successore | Espressione         |
| ------------ | ---------- | ------------------- |
| $q_{0}$      | $q_{0}$    | $1 + 0(0+10)^{*}11$ |
![[Pasted image 20261014125232.png|540]]$$
E_{q0}=(1+0(0+10)^{*})^{*}
$$

> [!done] Espressione regolare finale
> L'espressione regolare del `DFA` dato è la seguente:
> $$ E_{fin}= E_{q0} + E_{q1} = (1+0(0+10)^{*})^{*}+(1+0(0+10)^{*}11)^{*}.0(0+10)^{*}$$





