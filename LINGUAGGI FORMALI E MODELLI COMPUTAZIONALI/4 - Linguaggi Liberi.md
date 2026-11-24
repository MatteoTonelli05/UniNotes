>[!quote] Ricorda
>Linguaggi $\rightarrow$ Linguaggi Formali $\rightarrow$ Insiemi di stringhe

---

## Grammatiche Libere Dal Contesto (**CFG**)

> [!question] A cosa servono?
> Sono nate per descrivere il linguaggio naturale
> 
> _Esempio: in un automa a stati finiti si possono gestire un FINITO numero di parentesi, nella realtà (banalmente nei linguaggi) possiamo inserire un qualsiasi numero di parentesi_

### Esempio informale di CFG

Consideriamo $L_{pal}=\{w\in\Sigma^*:w=w^R\}$ su un alfabeto binario $\{0,1\}$ 
_(il linguaggio delle stringhe palindrome)_

> [!question] Perchè NON è regolare?
> Un automa a stati finiti ha memoria finita ($n$ stati). Se proviamo a leggere la prima metà della stringa per verificare la seconda metà speculare, l'automa è costretto a entrare in un ciclo (loop) perdendo il conteggio esatto dei simboli scritti.

> [!hint] Definiamo $L_{pal}$ induttivamente
> **Base:** $\epsilon, 0, 1$ sono palindromi
> **Induzione:** se $w$ è una palindroma, anche $0w0$ e $1w1$ lo sono

Le CFG sono un modo formale per definizioni come quella per $L_{pal}$ 
![[Pasted image 20261015102919.png|542]]
Una CFG è un **quadrupla** $$G = (V,T,P,S)$$dove:
- $V$ è un insieme finito di variabili (chiamate anche *non-terminali* o *categorie sintattiche*)
- $T$ è un insieme finito di terminali
- $P$ è un insieme finito di produzioni (chiamate anche *regole*) della forma $A \rightarrow a$, dove $A$ è una variabile e $a \in (V\cup T)^*$
- $S$ è una variabile distinta chiamata *variabile iniziale*

---
> [!example] Esempio: 
> $G_{pal}=(\{S\},\{0,1\},P,S)$, dove $$P=\{S\rightarrow\epsilon,S\rightarrow0,S\rightarrow1,S\rightarrow0S0,S\rightarrow1S1\}$$
> Dato che le produzioni con la stessa testa si possono raggruppare possiamoo scrivere anche: $$P = \{S\rightarrow\epsilon|0|1|0S0|1S1\}$$
> > [!attention] ATTENZIONE: le frecce sono cone le transizioni $\delta$

---
### Concetto di Derivazione ($\Rightarrow$)

> [!abstract] **Derivare** significa sostituire una **variabile** $A$ presente in una stringa con il corpo di una delle sue regole (Esempio: $A \rightarrow\gamma$) $$\alpha A \beta \Rightarrow \alpha \gamma \beta$$
> Definiamo il **contesto** come ciò che circonda la variabile derivata
> - **Contesto** a sinistra ($\alpha$)
> - **Contesto** a destra ($\beta$)

> [!example] Ad esempio per derivare $a*(a+b00)$ da $E$...
>![[Pasted image 20261015105351.png]]
>
>Seguiamo le **derivazioni**:
>
>![[Pasted image 20261015105411.png]]
>> _in pratica ad ogni passo si sceglie una variabile e la si sostituisce seguendo le regole della grammatica._
>
> Si può scrivere anche $E \overset{*}{\Rightarrow} a*(a+b00)$ il che significa che da $E$ con 0,1 o più passi si possa arrivare a $a*(a+b00)$, dimostriamo così il linguaggio della grammatica

---
> [!question] Perchè si chiama **Libera dal Contesto**?
> Perchè di fatti non interessa del contesto ma solo della variabile di partenza

In particolare possiamo definire due tipi di scelta della variabile da derivare:

1. **Derivazione a destra ($\mathop{\Rightarrow}\limits_{rm}$):** Ad ogni passo si sostituisce **sempre la variabile più a destra**.
2. **Derivazione a sinistra ($\mathop{\Rightarrow}\limits_{lm}$):** Ad ogni passo si sostituisce **sempre la variabile più a sinistra**.

> Rispettivamente **RightMost e LeftMost Derivation**, non importa quale si usi dato che sono equivalenti, entrambe le scelte possono portare allo stesso insieme di stringhe.

![[Pasted image 20261015111809.png|565]]

---
## Forma Normale di Chomsky (CNF)

Ogni CFL **senza $\epsilon$** è generato da una CFG in cui **tutte** le produzioni hanno una di queste due forme:

- **A → BC**
- **A → a**

dove A, B e C sono variabili, e $a$ è un simbolo terminale. Questa è detta forma normale di Chomsky.

> [!question] Perché "senza $\epsilon$"? 
> Con produzioni solo di queste due forme non si può mai generare la stringa vuota.

|Produzione|In CNF?|
|---|---|
|$A \to BC$|sì|
|$A \to a$|sì|
|$A \to aB$|no (terminale e variabile mescolati)|
|$A \to BCD$|no (troppo lunga)|
|$A \to B$|no (produzione unità)|
|$A \to \epsilon$|no|

### Come si ottiene? Semplificazioni su CFG

Si fanno tre semplificazioni preliminari:

1. eliminare i **simboli inutili** (non strettamente necessario per la CNF);
2. eliminare le **produzioni $\epsilon$**, della forma $A \to \epsilon$;
3. eliminare le **produzioni unità**, della forma $A \to B$ con $A$ e $B$ variabili.

---
#### 1. Eliminazione dei simboli inutili

> [!abstract] Simboli utili
> Sia $G = (V, T, P, S)$.
> Un simbolo $X$ è **utile** se esiste una derivazione
> $$S \Rightarrow^*_G \alpha X \beta \Rightarrow^*_G w$$
> per una stringa di terminali $w$. I simboli non utili sono detti **inutili**.

> [!attention] **PROPRIETÀ**
>
>- $X$ è **GENERANTE** se $X \Rightarrow^*_G w$ per qualunque $w \in T^*$ (da $X$ si ottiene una stringa di soli terminali; un terminale è generante di per sé).
>- $X$ è **RAGGIUNGIBILE** se $S \Rightarrow^*_G \alpha X \beta$ per qualunque $\{\alpha, \beta\} \subseteq (V \cup T)^*$ (dal simbolo iniziale si arriva a una forma che contiene $X$).

**Procedura** (se $L(G) \neq \emptyset$): 
si eliminano **prima** i simboli **non generanti**, **poi** quelli **non raggiungibili**. Restano solo simboli utili.

>[!attention] Eliminando un simbolo si eliminano anche **tutte le produzioni in cui compare**.

> [!example] Esempio
>
$$\begin{align} S &\to AB \mid a \\ A &\to b \end{align}$$
>
>- $S$ e $A$ sono generanti. $B$ **non** lo è (non ha produzioni).
>- Eliminando $B$ va via anche $S \to AB$. Resta $S \to a,\ A \to b$.
>- Ora solo $S$ è raggiungibile. Eliminando $A$ resta **$S \to a$**, con linguaggio $\{a\}$.

##### Perché l'ordine conta

Se si controllano prima i raggiungibili, tutti lo sono ($S \to AB$ raggiunge $A$ e $B$). Poi si elimina $B$ (non generante) e resta

$$S \to a, \quad A \to b$$

che contiene ancora $A$, ormai inutile ma non più eliminata. Quindi: **prima i generanti, poi i raggiungibili**.

---

#### 2. Eliminazione delle produzioni $\epsilon$

Se $L$ è un CFL, allora $L \setminus \{\epsilon\}$ ha una grammatica **priva di produzioni $\epsilon$**. Questo mostra anche che $L \setminus \{\epsilon\}$ è CFL.

Una variabile $A$ è **annullabile** se $A \Rightarrow^* \epsilon$. Si indica con $n(G)$ l'insieme dei simboli annullabili di $G = (V, T, P, S)$.

**Calcolo di $n(G)$** (dal libro):

- se $A \to \epsilon$, allora $A$ è annullabile;
- se $A \to X_1 X_2 \dots X_k$ e **tutti** gli $X_i$ sono annullabili, allora $A$ è annullabile.

**Procedura.** Sia $A$ annullabile. Una regola

$$B \to \alpha A \beta$$

si sostituisce con

$$B \to \alpha A \beta, \qquad B \to \alpha \beta$$

(rimpiazzando in tal modo anche le nuove regole via via ottenute, cioè prendendo **tutte le combinazioni** "tengo / tolgo" per le occorrenze annullabili). Poi si **cancellano tutte le regole con corpo $\epsilon$**. Se una combinazione dà corpo vuoto, quella regola non si aggiunge.

##### Esempio

$$\begin{align} S &\to AB \\ A &\to aAA \mid \epsilon \\ B &\to bBB \mid \epsilon \end{align}$$

- $A$ e $B$ sono annullabili direttamente. $S \to AB$ ha entrambi annullabili, quindi anche $S$ lo è: $n(G) = \{A, B, S\}$.
- $S \to AB$ diventa $S \to AB \mid A \mid B$ (la versione senza entrambi, $S \to \epsilon$, viene scartata).
- $A \to aAA$ ha due occorrenze di $A$, quindi 4 combinazioni: $aAA$, $aA$ (tolgo la prima), $aA$ (tolgo la seconda), $a$ (tolgo entrambe). Le due $aA$ coincidono.
- Analogamente per $B$.
- Si cancellano le regole con corpo $\epsilon$ e si ottiene $G_1$:

$$\begin{align} S &\to AB \mid A \mid B \\ A &\to aAA \mid aA \mid a \\ B &\to bBB \mid bB \mid b \end{align}$$

> Controllo: $L(G) = a^*b^*$, mentre $L(G_1) = a^*b^* \setminus \{\epsilon\}$. Si perde proprio $\epsilon$, perché $S$ era annullabile.

---

#### 3. Eliminazione delle produzioni unità

$$A \to B$$

è una **produzione unità** se $A$ e $B$ sono variabili. Le produzioni unità possono essere eliminate: si dà ad $A$ **tutto quello che sa fare $B$**.

Si consideri la grammatica

$$\begin{align} E &\to T \mid E + T \\ T &\to F \mid T \ast F \\ F &\to I \mid (E) \\ I &\to a \mid b \mid Ia \mid Ib \mid I0 \mid I1 \end{align}$$

Le produzioni unità sono $E \to T$, $T \to F$, $F \to I$.

##### Procedimento a espansione

Per $E \to T$:

1. Si espande $T$: si ottengono $E \to F$ e $E \to T \ast F$.
2. Si espande $E \to F$: si ottiene $E \to I \mid (E) \mid T \ast F$.
3. Si espande $E \to I$: si ottiene $$E \to a \mid b \mid Ia \mid Ib \mid I0 \mid I1 \mid (E) \mid T \ast F$$

Si fa lo stesso per le altre unità ($T \to F$ e $F \to I$). La grammatica diventa:

$$\begin{align} E &\to a \mid b \mid Ia \mid Ib \mid I0 \mid I1 \mid (E) \mid T \ast F \mid E + T \\ T &\to a \mid b \mid Ia \mid Ib \mid I0 \mid I1 \mid (E) \mid T \ast F \\ F &\to a \mid b \mid Ia \mid Ib \mid I0 \mid I1 \mid (E) \\ I &\to a \mid b \mid Ia \mid Ib \mid I0 \mid I1 \end{align}$$

(La regola $E \to E + T$ non era una unità e resta com'è.)

##### Il problema dei cicli

Il procedimento **non funziona** se ci sono cicli di unità, per esempio

$$A \to B, \quad B \to C, \quad C \to A$$

perché ogni espansione genera una nuova unità e non si finisce mai.

**Soluzione:** se durante il procedimento si genera una unità che si è **già espansa**, la si scarta. Espanderla di nuovo produrrebbe solo regole già ottenute.

##### Esempio con ciclo

$$\begin{align} A &\to B \mid a \\ B &\to C \mid b \\ C &\to A \mid c \end{align}$$

Elimino $A \to B$:

1. Si espande $B$: $A \to C \mid b$.
2. Si espande $A \to C$: $A \to A \mid c \mid b$.
3. Si espande $A \to A$: $A \to B \mid a$, regole già viste. Mi fermo.

Tolgo le unità e resta $A \to a \mid b \mid c$. Lo stesso per $B$ e $C$:

$$\begin{align} A &\to a \mid b \mid c \\ B &\to a \mid b \mid c \\ C &\to a \mid b \mid c \end{align}$$

> Metodo equivalente (dal libro, senza problemi con i cicli): si calcolano le coppie $(A, B)$ con $A \Rightarrow^* B$ usando solo unità; per ogni coppia si aggiunge ad $A$ ogni regola **non unità** di $B$.

---

#### Sommario: l'ordine

Per convertire una CFG in una equivalente senza simboli inutili, produzioni $\epsilon$ o produzioni unità si deve:

1. eliminare le produzioni $\epsilon$;
2. eliminare le produzioni unità;
3. eliminare i simboli inutili;

**in questo ordine**.

**Perché:**

- eliminare le $\epsilon$ **può creare** unità (con $S \to AB$ e $A$ annullabile si aggiunge $S \to B$);
- eliminare le unità non crea $\epsilon$;
- eliminare gli inutili non crea né $\epsilon$ né unità.

##### Esercizio della slide

_Trovare una grammatica in cui, cambiando l'ordine, non si ottiene la forma semplificata._

$$\begin{align} S &\to AB \\ A &\to \epsilon \\ B &\to b \end{align}$$

- Se faccio **prima le unità**, non ce ne sono e non cambia nulla.
- Poi le $\epsilon$: $A$ annullabile, ottengo $S \to AB \mid B$ e $B \to b$.
- Ora $S \to B$ è una **unità** e non viene più eliminata.

---

### Trasformazione in CNF

Ogni CFL non vuoto, che non contiene $\epsilon$, ha una grammatica $G$ priva di simboli inutili e in CNF, cioè con produzioni nella forma

- $A \to BC$, dove $\{A, B, C\} \subseteq V$, oppure
- $A \to a$, dove $A \in V$ e $a \in T$.

Per ottenerla, a partire da una qualsiasi grammatica del CFL:

1. Eliminare produzioni $\epsilon$ e unità (e, non strettamente necessario per la CNF, i simboli inutili).
2. Modificare le produzioni con 2 o più simboli in modo che contengano **solo variabili**.
3. Ridurre i corpi di lunghezza superiore a 2 in **cascate** di produzioni con corpi da 2 variabili.

#### Passo 2: terminali nei corpi lunghi

Per ogni terminale $a$ che compare in un corpo di lunghezza $\geq 2$:

- si crea una nuova variabile, ad esempio $A$;
- si sostituisce $a$ con $A$ in tutti questi corpi;
- si aggiunge la nuova regola $A \to a$.

> Le regole con un terminale solo (tipo $E \to a$) sono già in CNF e **non si toccano**.

#### Passo 3: spezzare i corpi di lunghezza $\geq 3$

Per ogni regola

$$A \to B_1 B_2 \dots B_k, \qquad k \geq 3$$

si introducono le nuove variabili $C_1, C_2, \dots, C_{k-2}$ e la si sostituisce con

$$\begin{align} A &\to B_1 C_1 \\ C_1 &\to B_2 C_2 \\ &\dots \\ C_{k-3} &\to B_{k-2} C_{k-2} \\ C_{k-2} &\to B_{k-1} B_k \end{align}$$

Effetto sull'albero: un nodo con $k$ figli $B_1 \dots B_k$ diventa un albero **binario** "a pettine" con le stesse foglie.

##### Mini-esempio

$A \to aBcD$ ($k = 4$ dopo il passo 2):

- passo 2: $X \to a$, $Y \to c$, la regola diventa $A \to XBYD$;
- passo 3: servono $k - 2 = 2$ nuove variabili:

$$\begin{align} A &\to XC_1 \\ C_1 &\to BC_2 \\ C_2 &\to YD \end{align}$$

#### Esempio completo

Si parte dalla grammatica senza unità:

$$\begin{align} E &\to E + T \mid T \ast F \mid (E) \mid a \mid b \mid Ia \mid Ib \mid I0 \mid I1 \\ T &\to T \ast F \mid (E) \mid a \mid b \mid Ia \mid Ib \mid I0 \mid I1 \\ F &\to (E) \mid a \mid b \mid Ia \mid Ib \mid I0 \mid I1 \\ I &\to a \mid b \mid Ia \mid Ib \mid I0 \mid I1 \end{align}$$

**Passo 2.** Nuove variabili:

$$A \to a, \quad B \to b, \quad Z \to 0, \quad O \to 1$$ $$P \to +, \quad M \to \ast, \quad L \to (, \quad R \to )$$

La grammatica diventa:

$$\begin{align} E &\to EPT \mid TMF \mid LER \mid a \mid b \mid IA \mid IB \mid IZ \mid IO \\ T &\to TMF \mid LER \mid a \mid b \mid IA \mid IB \mid IZ \mid IO \\ F &\to LER \mid a \mid b \mid IA \mid IB \mid IZ \mid IO \\ I &\to a \mid b \mid IA \mid IB \mid IZ \mid IO \end{align}$$

più le regole $A \to a$, $B \to b$, $Z \to 0$, $O \to 1$, $P \to +$, $M \to \ast$, $L \to ($, $R \to )$.

**Passo 3.** Si spezzano i corpi di lunghezza 3:

- $E \to EPT$ diventa $E \to EC_1$ con $C_1 \to PT$
- $E \to TMF$ e $T \to TMF$ diventano $E \to TC_2$ e $T \to TC_2$ con $C_2 \to MF$
- $E \to LER$, $T \to LER$, $F \to LER$ diventano $E \to LC_3$, $T \to LC_3$, $F \to LC_3$ con $C_3 \to ER$

(Si riusa la stessa $C_2$ e la stessa $C_3$ per tutte le regole con lo stesso corpo.)

**Grammatica finale in CNF:**

$$\begin{align} E &\to EC_1 \mid TC_2 \mid LC_3 \mid a \mid b \mid IA \mid IB \mid IZ \mid IO \\ T &\to TC_2 \mid LC_3 \mid a \mid b \mid IA \mid IB \mid IZ \mid IO \\ F &\to LC_3 \mid a \mid b \mid IA \mid IB \mid IZ \mid IO \\ I &\to a \mid b \mid IA \mid IB \mid IZ \mid IO \\ C_1 &\to PT, \quad C_2 \to MF, \quad C_3 \to ER \\ A &\to a, \quad B \to b, \quad Z \to 0, \quad O \to 1 \\ P &\to +, \quad M \to \ast, \quad L \to (, \quad R \to ) \end{align}$$
