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

#### Testare se un linguaggio è vuoto

> [!cite] Teorema
> $L(A)=\emptyset$ per **FA** $A$ ***se e solo se*** uno stato finale è raggiungibile dallo stato iniziale in $A$.

Totale: $O(n^{2})$ passi.

> Oppure

Possiamo guardare un'[[Espressioni Regolari|espressione regolare]] $E$ di lunghezza $s$ e vedere se $L(E)=\emptyset$ in $O(s)$ passi.
Usiamo il seguente metodo:
- $E=F+G$ allora $L(E)$ è vuoto *se e solo se* sia $L(F)$ che $L(G)$ sono **vuoti**.
- $E=F.G$ allora $L(E)$ è vuoto *se e solo se* $L(F)$ e $L(G)$ sono vuoti
- $E=F^{*}$ allora $L(E)$ non è mai vuoto perché $\epsilon \in L(E)$
- $E=\epsilon$ allora $L(E)$ non è vuoto
- $E=a$ allora $L(E)$ non è vuoto
- $E=\emptyset$ allora $L(E)$ è vuoto

### Controllare l'appartenenza

> [!abstract] Per DFA
> Per controllare se $w\in L(A)$ per `DFA` $A$, simuliamo $A$ su $w$. Se $|w|=n$ questo prende $O(n)$ passi.

> [!caution] Per NFA
> Se $A$ è un `NFA` e ha $s$ stati, simulare $A$ su $w$ prende $O(ns^{2})$ passi.

Lo stesso vale per $\epsilon$-NFA calcolando preventivamente le $\text{ECLOSE}$ di tutti gli stati.

> [!todo] Per espressione regolare
> Se $L=L(E)$ per l'espressione regolare $E$ di lunghezza $s$ prima convertiamo $E$ in un $\epsilon$-NFA con $2s$ stati, poi simuliamo $w$ su questo automa in $O(ns^{2})$ 

### Equivalenza e Minimizzazione di Automi

#### Equivalenza degli Stati

> [!tldr] Idea
> Due stati sono ***equivalenti*** se e solo se per ogni stringa dell'alfabeto, partendo dai due stati *si raggiunge uno stato di accettazione*. 

Sia $A=(Q,\Sigma,\delta,q_{0},F)$ un DFA e $\{ p,q \}\subseteq Q$. 
Definiamo:
$$p\equiv q \iff \forall w\in\Sigma^{*}:\hat{\delta}(p,w)\in F \iff \hat{\delta}(q,w)\in F$$

> [!summary] Se $p\equiv q$ diciamo che $p$ e $q$ sono ***equivalenti***

> [!fail] Se $p\not\equiv q$ diciamo che $p$ e $q$ sono ***distinguibili***

In altre parole $p$ e $q$ sono **distinguibili** *se e solo se*
$$
\exists w:\hat{\delta}(p,w)\in F \text{ e } \hat{\delta}(q,w)\notin F
$$
o viceversa.

##### Algoritmo

> [!caution] Table-filling algorithm (**Riempi Tabella**)
> Possiamo ***calcolare coppie di stati distinguibili*** con il seguente metodo induttivo.
>
> **Base:** Se $p\in F$ e $q\notin F$ allora $p\not\equiv q$
>
> **Induzione:** Se $\exists a\in \Sigma :\delta(p,a) \not\equiv \delta(q,a)$ allora $p\not\equiv q$.

![[Pasted image 20261019180656.png]]
##### Esempio
![[Pasted image 20261014181505.png|648]]
Inizio contrassegnando come distinte tutte le coppie di stati ***accettazione***/***non accettazione*** (*passo base*).

Successivamente scorro tutte le coppie di nodi:
- Controllo gli archi uscenti dalla coppia con lo stesso simbolo e controllo la coppia di stati che raggiungono.
- Se la coppia di stati è già stata segnata, allora segno nella tabella anche quella corrente.


> [!done] Fine dell'algoritmo
> L'algoritmo finisce quando passo tutte le coppie di stati ancora non segnati, ***senza modificare la tabella***.

La tabella finale dell'esempio è la seguente:
![[Pasted image 20261015151141.png|481]]

> Stati equivalenti:
- $(A,E)$
- $(B,H)$
- $(D,F)$

> [!cite] Teorema
> Se $p$ e $q$ **non sono distinti** dall'algoritmo, allora $p \equiv q$

###### Testare l'equivalenza di linguaggi regolari
> Il ***table-filling algorithm*** da una procedura facile per testare se due linguaggi regolari sono equivalenti.

> [!hint] Test
> Siano $L$ e $M$ ***linguaggi regolari*** descritti in qualche forma.

Per testare se $L=M$
1. Converto sia $L$ che $M$ in `DFA`
2. Immaginiamo il `DFA` che è l'unione dei due `DFA`
3. Se l'algoritmo dice che i due stati iniziali sono ***distinguibili*** allora $L\neq M$ altrimenti $L=M$

> Esempio di `DFA` equivalenti

![[Pasted image 20261015151150.png|568]]

 ![[Pasted image 20261015151157.png|578]]
> [!question] Perchè sono equivalenti i due DFA?
> Perchè nell'algoritmo *Riempi-Tabella* è risultato $A\equiv C$


#### Minimizzazione di DFA
> [!cite] Algoritmo
> Per minimizzare un `DFA` mettiamo insieme tutti gli ***stati equivalenti*** considerando poi come stati le ***classi di equivalenza*** $p/_{\equiv}$ al posto di $p$ ed eliminando eventuali stati non raggiungibili dallo stato iniziale.

> Esempio

![[Pasted image 20261015151206.png]]

Dal seguente `DFA`, dopo aver applicato il *table-filling algorithm*, possiamo trovare le seguenti classi di equivalenza:
$$
\{ \{ A,E \},\{ B,H \}, \{ C \}, \{ D,F \}, \{ G \} \}
$$

> [!nota]
> $p /_{\equiv}$ è classe di equivalenza della [[../Algebra e Geometria/Basi dell'algebra/1 - Introduzione#Relazione di Equivalenza|relazione di equivalenza]], cioè una relazione *riflessiva*, *simmetrica* e *transitiva*.

Per minimizzare un `DFA` $A=(Q,\Sigma,\delta,q_{0},F)$ dove assumiamo di aver già eliminato tutti gli stati non raggiungibili dallo stato iniziale.

Costruiamo ora un `DFA` $B=(Q /_{\equiv},\Sigma /_{\equiv},\gamma /_{\equiv},q_{0} /_{\equiv},F/_{\equiv})$ dove:
- $\gamma(p/_{\equiv}, a)=\delta(p,a)/_{\equiv}$
- $Q/_{\equiv}$ è l'insieme delle classi di equivalenza degli stati di $Q$
- $F/_{\equiv}$ è l'insieme di quelle degli **stati accettanti**.

Affinché $B$ sia ben definito, dobbiamo mostrare che:
- Se
$$
p\equiv q
$$
allora
$$
\delta(p,a) \equiv \delta(q,a)
$$
Ma se $\delta(p,a) \not\equiv \delta(q,a)$ allora l'algoritmo concluderebbe $p\not\equiv q$, quindi $B$ è ***ben definito***.

Di seguito l'automa equivalente a quello in esempio:
![[Pasted image 20261015151213.png]]