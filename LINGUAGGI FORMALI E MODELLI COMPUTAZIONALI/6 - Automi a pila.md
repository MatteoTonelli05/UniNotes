## Introduzione Informale

> [!abstract] Un'automa a pila (**PDA**) è in pratica un $\epsilon$-NFA con una pila

![[Pasted image 20261022100416.png]]_il Finite State Control nell'immagine è un $\epsilon$-NFA_

> [!question] Come funziona una transizione
> 1. Legge il simbolo corrente dell'input (oppure esegue una mossa $\epsilon$ senza consumare input).
>
>2. Esamina e rimuove (_pop_) il simbolo che si trova in cima alla pila.
  >  
>3. Passa a un nuovo stato. (in base ai due simboli appena esaminati)
  >  
>4. Inserisce (_push_) una stringa di simboli $\gamma \in \Gamma^*$ al posto del simbolo appena rimosso. Se $\gamma = \epsilon$, la cima viene semplicemente rimossa; se $\gamma$ è pari al simbolo consumato, la pila rimane inalterata.

---
### Transizione

> [!example] Esempio

Il linguaggio $L_{ww^R} = \{ww^R : w \in \{0,1\}^*\}$ è l'insieme di tutte le **stringhe palindrome di lunghezza pari** su alfabeto $\{0,1\}$.

- $w$ è la prima metà della stringa.    
- $w^R$ è la seconda metà, cioè la stringa $w$ **scritta al contrario** (_reversed_)

>[!fail] Un automa a stati finiti (DFA/NFA) senza memoria non può riconoscere questo linguaggio perché non ha modo di "ricordare" cosa ha letto nella prima metà per poi confrontarlo con la seconda. La **pila** risolve questo problema.

1. Legge w un simbolo alla volta, rimanendo nello stato $q_0$, e aggiungendo il simbolo di input alla pila. 
	
2. Decide non deterministicamente che sta nel mezzo di $ww^R$ e va nello stato $q_1$. 
	
3. Legge $w^R$ un simbolo alla volta e lo paragona col simbolo al top della pila: Se sono uguali, fa un pop della pila, e rimane nello stato $q_1$. Se non sono uguali, si blocca. 
	
4. Se la pila non ha piu’ simboli (0 o 1), va nello stato $q_2$ e accetta.

> [!question] Ma come faccio a sapere quando passare dal push al pop?
> Si sfrutta il **non-determinismo**, si analizzano tutti gli scenari, se c'è almeno una casistica in cui la transizione si conclude allora sapremo che è palindroma.

---
![[Pasted image 20261022104701.png|450]]
_la notazione indica `A , B / C ` in cui_
- **A :** `input`
- **B :** `Cosa ci deve essere in cima alla pila` **(NON può essere $\epsilon$)**
- **C :** `Con cosa verrà sostituita la cima della pila`

> [!attention] nel caso in cui la stringa in **C** sia più di un carattere, quest'ultima viene inserita nella pila in quell' ordine, nel caso `0 1` in cima alla pila ci sarà `0`

#### ### Esempio visivo: stringa `0110`

- **Inizio:** Pila $= [Z_0]$, stato $q_0$.
- Legge `0`: mette `0` in pila $\rightarrow$ Pila $= [0, Z_0]$.
- Legge `1`: mette `1` in pila $\rightarrow$ Pila $= [1, 0, Z_0]$.
- _Indovina il centro!_ Passa a $q_1$ senza leggere input $\rightarrow$ Pila $= [1, 0, Z_0]$.
- Legge `1`: la cima della pila è `1` (match!). Fa _pop_ $\rightarrow$ Pila $= [0, Z_0]$.
- Legge `0`: la cima della pila è `0` (match!). Fa _pop_ $\rightarrow$ Pila $= [Z_0]$.
- Vede $Z_0$ in cima alla pila: passa a $q_2$ $\rightarrow$ **Stringa accettata!**

---
## Definizione formale di PDA

# Definizione formale di PDA

Un PDA è una tupla di 7 elementi:

$$P = (Q, \Sigma, \Gamma, \delta, q_0, Z_0, F)$$

dove:
* $Q$ è un insieme finito di stati,
* $\Sigma$ è un alfabeto finito di input,
* $\Gamma$ è un alfabeto finito di pila,
* $\delta$ è una funzione di transizione da $Q \times (\Sigma \cup \{\epsilon\}) \times \Gamma$ a sottoinsiemi di $Q \times \Gamma^*$,
* $q_0$ è lo stato iniziale,
* $Z_0 \in \Gamma$ è il simbolo iniziale per la pila, e
* $F \subseteq Q$ è l'insieme di stati di accettazione.

![[Pasted image 20261022110842.png]]

---

### Descrizioni istantanee

Per ragionare sulle computazioni dei PDA, usiamo delle descrizioni istantanee (ID) del PDA. Una ID è una tripla: $$(q, w, γ)$$
dove $q$ è lo stato, $w$ l’input rimanente, e $γ$ il contenuto della pila.
![[Pasted image 20261022111917.png]]

---
Se dovessimo rappresentare TUTTI i tentativi di ricerca della metà dovremmo fare
![[Pasted image 20261022114246.png|292]]

---
