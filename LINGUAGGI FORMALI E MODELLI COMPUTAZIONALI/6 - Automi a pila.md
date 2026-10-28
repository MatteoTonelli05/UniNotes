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
![[lcmc.png]]

---
Se dovessimo rappresentare TUTTI i tentativi di ricerca della metà dovremmo fare
![[Pasted image 20261022114246.png|292]]

---
## Modalità di Accettazione dei PDA e Trasformazioni

Un Automa a Pila (PDA) $P = (Q, \Sigma, \Gamma, \delta, q_0, Z_0, F)$ si dice che **riconosce** un linguaggio solo se rispetta uno di due criteri equivalenti: **stato finale** oppure **pila vuota**.
### 1. Accettazione per Stato Finale

Un PDA accetta una stringa $w \in \Sigma^*$ per **stato finale** se, partendo dalla configurazione iniziale $(q_0, w, Z_0)$, dopo aver consumato l'intero input $w$, si trova in uno **stato di accettazione** $q \in F$, indipendentemente dal contenuto della pila.

#### Definizione Formale
Il linguaggio accettato per stato finale è definito come:
$$L(P) = \{ w \in \Sigma^* \mid (q_0, w, Z_0) \vdash^* (q, \epsilon, \alpha), \text{ con } q \in F \text{ e } \alpha \in \Gamma^* \}$$

> [!info] **Nota** 
> Il contenuto finale della pila ($\alpha$) è del tutto irrilevante ai fini dell'accettazione.
### 2. Accettazione per Pila Vuota

Un PDA accetta una stringa $w \in \Sigma^*$ per **pila vuota** se, partendo dalla configurazione iniziale $(q_0, w, Z_0)$, dopo aver consumato l'intero input $w$, la **pila diventa completamente vuota** ($\epsilon$), indipendentemente dallo stato in cui si trova l'automa.

#### Definizione Formale
Il linguaggio accettato per pila vuota è definito come:

$$N(P) = \{ w \in \Sigma^* \mid (q_0, w, Z_0) \vdash^* (q, \epsilon, \epsilon), \text{ con } q \in Q \}$$

> [!info] **Nota**
>  In questo caso l'insieme degli stati finali $F$ non è necessario (può essere omesso dalla tupla del PDA o considerato vuoto).

---

> [!important] Equivalenza dei Modelli
> I due metodi sono **computazionalmente equivalenti**: un linguaggio è generato da una CFG se e solo se è accettato da un PDA per pila vuota, se e solo se è accettato da un PDA per stato finale.

---

### Trasformazione: Da Pila Vuota a Stato Finale

Dato un PDA $P_N$ che accetta un linguaggio $L$ per pila vuota ($L = N(P_N)$), è sempre possibile costruire un PDA $P_F$ che accetta lo stesso linguaggio $L$ ma per stato finale ($L = L(P_F)$).

![[Pasted image 20261022120939.png]]
### Costruzione Formale di $P_F$
Si definisce $P_F$ aggiungendo due nuovi stati ($p_0$ e $p_f$) e un nuovo simbolo di fondo pila ($X_0$):

$$P_F = (Q \cup \{p_0, p_f\}, \Sigma, \Gamma \cup \{X_0\}, \delta_F, p_0, X_0, \{p_f\})$$

Dove la funzione di transizione $\delta_F$ comprende:
1. **Inizializzazione:** $\delta_F(p_0, \epsilon, X_0) = \{(q_0, Z_0 X_0)\}$
   * Pone il nuovo simbolo $X_0$ sotto il simbolo iniziale $Z_0$ e passa allo stato iniziale di $P_N$.
2. **Simulazione:** Per ogni $q \in Q$, $a \in \Sigma \cup \{\epsilon\}$ e $Y \in \Gamma$:
   $$\delta_F(q, a, Y) = \delta_N(q, a, Y)$$
3. **Cattura del vuoto ed Accettazione:** Per ogni $q \in Q$:$$(p_f, \epsilon) \in \delta_F(q, \epsilon, X_0)$$
   * Se $P_N$ svuota tutta la sua pila ($\Gamma^*$), in cima rimane solo il simbolo speciale $X_0$. $P_F$ lo rileva e passa allo stato finale $p_f$.

![[Pasted image 20261028165350.png]]
#### Dimostrazione

Sia $P_{F}=(Q\cup \{ p_{0},p_{f} \},\Sigma,\Gamma\cup \{ X_{0} \},\delta_{F},p_{0},X_{0},\{ p_{f} \})$

Dove
- $\delta_{F}(p_{0},\epsilon,X_{0})=\{ (q_{0},Z_{0}X_{0}) \}$, In questo stato iniziale, $P_{F}$ esegue una transizione spontanea allo stato iniziale di $P_{N}$, inserendo il carattere $X_{0}$ all'inizio della pila.
- Per ogni stato $q\in Q$, input $a\in\Sigma\cup\{ \epsilon \}$ e simboli di stack $Y\in\Gamma$, $\delta_{F}(q,a,Y)$ contiene tutte le coppie $\delta_{N}(q,a,Y)$.
	- Significa che $P_{F}$ simula le mosse di $P_{N}$ finché in cima alla pila c'è un simbolo di $\Gamma$.
- In aggiunta alla regola precedente $(p_{f},\epsilon)\in\delta_{F}(q,\epsilon,X_{0})$
	- Da qualunque stato $q$ se in cima alla pila c'è $X_{0}$ posso spostarmi senza legger input in $p_{f}$


---
### Trasformazione: Da stato finale a pila vuota



Introduco un ***marcatore di fondo*** $X_{0}$.
Aggiungo uno stato di accettazione collegato a tutti gli stati del `PDA` a pila vuota e uno iniziale collegato allo stato iniziale originale.

- Alla prima transazione aggiungo il ***marcatore di fondo*** (diverso dai caratteri $\in\Sigma$) alla pila.
	- $\epsilon,X_{0}/Z_{0}X_{0}$
	- In realtà la pila inizia con $X_{0}$ a cui viene aggiunto il ***carattere iniziale*** in cima.
- Tutte le transizioni allo stato finale rimuoveranno il *marcatore*.
	- $\epsilon, X_{0}/\epsilon$
![[Pasted image 20261028165820.png]]
#### Dimostrazione

Sia $P_{N}=(Q\cup \{ p_{0},p \},\Sigma,\Gamma\cup \{ X_{0} \},\delta_{N},p_{0},X_{0})$

Dove
- $\delta_{N}(p_{0},\epsilon,X_{0})=\{ (q_{0},Z_{0}X_{0}) \}$, iniziamo inserendo il carattere iniziale di $P_{F}$ nella pila e passare allo stato iniziale di $P_{F}$.
- Per ogni stato $q\in Q$, simboli di input $a\in\Sigma\cup\{ \epsilon \}$ e simboli di stack $Y\in\Gamma$, $\delta_{N}(q,a,Y)$ contiene tutte le coppie $\delta_{F}(q,a,Y)$.
	- Significa che $P_{N}$ simula le mosse di $P_{F}$.
- Per ciascun stato di accettazione $q\in F$ e per ogni simbolo dello stack $Y\in\Gamma\cup X_{0}$, $\delta_{N}(q,\epsilon,Y)$ contiene $(p,\epsilon)$.
	- Con questa regola, quando $P_{F}$ accetta una stringa, $P_{N}$ può iniziare a ***svuotare lo stack senza consumare altri input***.
- Per ciascun simbolo dello stack $Y\in\Gamma\cup X_{0}$, $\delta_{N}(p,\epsilon,Y)=\{ (p,\epsilon) \}$, una volta nello stato $p$, che succede solo quando $P_{F}$ ha accettato la stringa, $P_{N}$ rimuove tutti i simboli dallo stack, finché la pila è vuota.

## Equivalenza di PDA e CFG
---
>[!summary] Equivalenza
> Un ***linguaggio è generato*** da una [[Grammatiche e Linguaggi Liberi dal contesto|CFG]] $\iff$ è accettato da una `PDA` per pila vuota $\iff$ è accettato da un `PDA` per stato finale.

Sappiamo già andare da pila vuota a stato finale.
![[Pasted image 20261028165450.png]]

### Da CFG a PDA

> [!tldr] Idea
> ***Trasformazione a 2 passi*** partendo da un automa con *un unico stato*:
> 1. Prendo tutte le produzioni e le trasformo in autocicli
> 2. Per ciascun carattere terminale, creo una transizione che consuma il carattere sia dall'input che dalla pila (sostituisce con $\epsilon$)

> [!example] Esempio:
>1. Sia $S\to Sx$ una ***produzione della grammatica***, la transizione equivalente del `PDA` sarà: $\epsilon,S/Sx$
>	- Non consumo un carattere, richiedo una variabile $S$ in cima alla pila e lo sostituisco con il risultato della produzione. 
>2. Se $x$ è un ***terminale della grammatica***, quando lo incontro sia sulla pila che nell'input, lo consumo e lo rimuovo dalla pila.
>	- Devo aggiungere un *autociclo* con transizione $x,x/\epsilon$.

> Data una grammatica $G$ costruiamo un `PDA` che simula una ***derivazione canonica sinistra*** $\overset{*}{\underset{ lm }{ \implies }}$.
Sia $w$ la stringa data in input al `PDA` e $y$ tale che $w=xy$, allora il `PDA` va ***non deterministicamente*** dalla configurazione $(q,y,A\alpha)$ alla configurazione $(q,y,\beta\alpha)$
- Alla configurazione $(q,y,\beta\alpha)$ il `PDA` si comporta come prima, a meno che ci siano terminali nel prefisso di $\beta$.
- In questo caso il `PDA` li elimina se li legge nell'input (fanno *match*).

>[!done] Se tutte le scommesse sono giuste, il `PDA` finisce l'input con la pila vuota
#### Definizione Formale
> [!cite] Definizione
> Sia $G=(V,T,Q,S)$ una `CFG` definiamo $P_{G}$ come:
> $$(\{ q \},T,V\cup T,\delta,q,S)$$

Dove:
- $\delta(q,\epsilon,A)=\{ (q,\beta):A\to \beta \in Q\}$ per $A\in V$
- $\delta(q,a,a)=\{ (q,\epsilon) \}$ per $a\in T$