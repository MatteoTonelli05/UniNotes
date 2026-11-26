
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

### Semplificazioni su CFG

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
si eliminano **prima** le variabili (**simboli**) **non generanti**, **poi** quelli **non raggiungibili**. 
Restano solo simboli utili.

Eliminando un simbolo si eliminano anche **tutte le produzioni in cui compare**.

> [!example] Esempio
>
$$\begin{align} S &\to AB \mid a \\ A &\to b \end{align}$$
>
>- $S$ e $A$ sono generanti. $B$ **non** lo è (non ha produzioni).
>- Eliminando $B$ va via anche $S \to AB$. Resta $S \to a,\ A \to b$.
>- Ora solo $S$ è raggiungibile (in quanto stato iniziale). 
>- Eliminando $A$ resta **$S \to a$**, con linguaggio $\{a\}$.

> [!question] Perché l'ordine conta?
>
>Se si controllano prima i raggiungibili, tutti lo sono ($S \to AB$ raggiunge $A$ e $B$). Poi si elimina $B$ (non generante) e resta
>
>$$S \to a, \quad A \to b$$
>
>che contiene ancora $A$, ormai inutile ma non più eliminata.

---

#### 2. Eliminazione delle produzioni $\epsilon$

> [!abstract] Annullabile
> Se $L$ è un CFL, allora $L \setminus \{\epsilon\}$ ha una grammatica **priva di produzioni $\epsilon$**. 
> Questo mostra anche che $L \setminus \{\epsilon\}$ è CFL.
>
> Una variabile $A$ è **annullabile** se $A \Rightarrow^* \epsilon$. Si indica con $n(G)$ l'insieme dei simboli annullabili di $$G = (V, T, P, S)$$

**Procedura** 
Sia $A$ annullabile. Una regola
$$B \to \alpha A \beta$$
si sostituisce con
$$B \to \alpha A \beta, \qquad B \to \alpha \beta$$
e **cancellando tutte le regole con corpo $\epsilon$**. (Se una combinazione dà corpo vuoto, quella regola non si aggiunge.)

> [!example] Esempio
>
$$\begin{align} S &\to AB \\ A &\to aAA \mid \epsilon \\ B &\to bBB \mid \epsilon \end{align}$$
>
>- $A$ e $B$ sono annullabili direttamente. $S \to AB$ ha entrambi annullabili, quindi anche $S$ lo è: $n(G) = \{A, B, S\}$.
>- $S \to AB$ diventa $S \to AB \mid A \mid B$ (la versione senza entrambi, $S \to \epsilon$, viene scartata).
>- $A \to aAA$ ha due occorrenze di $A$, quindi 4 combinazioni: $aAA$, $aA$ (tolgo la prima), $aA$ (tolgo la seconda), $a$ (tolgo entrambe). Le due $aA$ coincidono.
>- Analogamente per $B$.
>- Si cancellano le regole con corpo $\epsilon$ e si ottiene $G_1$:
>
$$\begin{align} S &\to AB \mid A \mid B \\ A &\to aAA \mid aA \mid a \\ B &\to bBB \mid bB \mid b \end{align}$$
>
> Controllo: $L(G) = a^*b^*$, mentre $L(G_1) = a^*b^* \setminus \{\epsilon\}$. Si perde proprio $\epsilon$, perché $S$ era annullabile.

---

#### 3. Eliminazione delle produzioni unità

> [!abstract] Produzioni unità
> $$A \to B$$
>
> è una **produzione unità** se $A$ e $B$ sono variabili. Le produzioni unità possono essere eliminate: si dà ad $A$ **tutto quello che sa fare $B$**.

##### Il problema dei cicli

Il procedimento **non funziona** se ci sono cicli di unità, per esempio

$$A \to B, \quad B \to C, \quad C \to A$$

perché ogni espansione genera una nuova unità e non si finisce mai.

**Soluzione:** se durante il procedimento si genera una unità che si è **già espansa**, la si scarta. Espanderla di nuovo produrrebbe solo regole già ottenute.

> [!example] Esempio con ciclo
>$$\begin{align} A &\to B \mid a \\ B &\to C \mid b \\ C &\to A \mid c \end{align}$$
>
Elimino $A \to B$:
>
>1. Si espande $B$: $A \to C \mid b$.
>2. Si espande $A \to C$: $A \to A \mid c \mid b$.
>3. Si espande $A \to A$: $A \to B \mid a$, regole già viste. Mi fermo.
>
Tolgo le unità e resta $A \to a \mid b \mid c$. Lo stesso per $B$ e $C$:
>
$$\begin{align} A &\to a \mid b \mid c \\ B &\to a \mid b \mid c \\ C &\to a \mid b \mid c \end{align}$$
>
>> [!hint] Metodo equivalente: si calcolano le coppie $(A, B)$ con $A \Rightarrow^* B$ usando solo unità; per ogni coppia si aggiunge ad $A$ ogni regola **non unità** di $B$.

---
### Come ottenera la forma di Chomsky - Trasformazione in CNF

Ogni CFL non vuoto, che non contiene $\epsilon$, **HA** una grammatica $G$ priva di simboli inutili e in CNF, cioè con produzioni nella forma

- $A \to BC$, dove $\{A, B, C\} \subseteq V$, oppure
- $A \to a$, dove $A \in V$ e $a \in T$.

Per ottenerla, a partire da una qualsiasi grammatica del CFL:

1. Eliminare produzioni $\epsilon$ e unità (e, anche se non necessario per la CNF, i simboli inutili).
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
si introducono le nuove variabili $C_1, C_2, \dots, C_{k-2}$ e la si sostituisce con b
$$\begin{align} A &\to B_1 C_1 \\ C_1 &\to B_2 C_2 \\ &\dots \\ C_{k-3} &\to B_{k-2} C_{k-2} \\ C_{k-2} &\to B_{k-1} B_k \end{align}$$
> [!example] Mini-esempio
>
$A \to aBcD$ ($k = 4$ dopo il passo 2):
>
>- passo 2: $X \to a$, $Y \to c$, la regola diventa $A \to XBYD$;
>- passo 3: servono $k - 2 = 2$ nuove variabili:
>
>$$\begin{align} A &\to XC_1 \\ C_1 &\to BC_2 \\ C_2 &\to YD \end{align}$$

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

---

## Chomsky Grammar Hierarchy

La gerarchia di Chomsky **classifica le grammatiche in 4 tipi** in base alla forma delle produzioni. 
A ogni tipo corrisponde una **classe di linguaggi** e una **macchina** che li riconosce. 
Le classi sono una dentro l'altra.

---

> [!attention] In tutti i casi si parla di $G = (V, T, P, S)$, con $V$ variabili, $T$ terminali, $P$ produzioni, $S$ simbolo iniziale. 
> Cambia **solo la forma ammessa per le produzioni**.

### Tipo 0: grammatiche senza restrizioni

Produzioni
$$\alpha \to \beta \in P \qquad \text{con } \alpha \in (V \cup T)^+ \text{ e } \beta \in (V \cup T)^*$$

- A sinistra può esserci **qualsiasi sequenza non vuota** di variabili e terminali.
- A destra **qualsiasi sequenza**, anche vuota ($\beta = \epsilon$ è ammesso).
- È la classe più generale.

> [!hint] Ricorda
> - $X^*$ è l'insieme di tutte le sequenze formate da **zero o più** elementi di $X$, compresa la **stringa vuota** $\epsilon$.
> - $X^+$ è lo stesso insieme ma con **una o più** ripetizioni, quindi **senza la stringa vuota**:
> $$X^+ = X^* \setminus \{\epsilon\}$$
### Tipo 1: grammatiche dipendenti dal contesto / monotone

Si restringe a produzioni $\alpha \to \beta$ con
$$|\beta| \geq |\alpha|$$

cioè **la lunghezza non diminuisce mai** durante una derivazione (per questo "monotone").

> [!attention] In particolare sono ammesse le produzioni **dipendenti dal contesto**
$$\alpha_1 A \alpha_2 \to \alpha_1 \beta \alpha_2 \qquad \text{con } A \in V,\ \beta \in (V \cup T)^+,\ \alpha_1, \alpha_2 \in (V \cup T)^*$$
> > [!hint] Come leggerle
> > La variabile $A$ si riscrive in $\beta$ **solo se** si trova tra $\alpha_1$ e $\alpha_2$. La riscrittura **dipende dal contesto**, da cui il nome.

> Le produzioni dipendenti dal contesto sono anche monotone: $\beta$ non è vuota, quindi $|\alpha_1\beta\alpha_2| \geq |\alpha_1 A \alpha_2|$. Le due definizioni (monotone e dipendenti dal contesto) danno la stessa classe di linguaggi.

### Tipo 2: grammatiche libere dal contesto (CFG)

Produzioni
$$A \to \beta \qquad \text{con } A \in V \text{ e } \beta \in (V \cup T)^+$$

- A sinistra **una sola variabile**.
- La riscrittura vale **sempre**, qualunque cosa ci sia attorno ad $A$: è **libera dal contesto**.

### Tipo 3: grammatiche regolari

Produzioni
$$A \to a \qquad \text{oppure} \qquad A \to aB \qquad \text{con } A, B \in V \text{ e } a \in T$$

- Un terminale, eventualmente seguito da **una sola variabile** a destra (grammatiche _lineari a destra_).
- Sono le grammatiche che generano esattamente i linguaggi riconosciuti dagli automi a stati finiti.

---

### La nota sulla stringa vuota

> [!abstract] I tipi 1, 2 e 3 
> Ammettono anche una possibile produzione $S \to \epsilon$ per il simbolo iniziale $S$ (per rappresentare linguaggi che contengono $\epsilon$), **purché non esista alcuna $\alpha \to \beta \in P$ con $S$ che compare in $\beta$**.

> [!question]**Perché la condizione?** 
> Se $S$ potesse comparire a destra, $S \to \epsilon$ potrebbe essere usata "in mezzo" a una derivazione e far **accorciare** la forma sentenziale, rompendo la proprietà che caratterizza i tipi 1, 2 e 3 (ad esempio la monotonia del tipo 1).

---

## Esempi di grammatiche specifiche

### Esempio di grammatica monotone (tipo 1)

$$\begin{align} S &\to aSBc \mid aBc \\ cB &\to Bc \\ aB &\to ab \\ bB &\to bb \end{align}$$

Genera

$$L(G) = \{a^n b^n c^n \mid n \geq 1\}$$

**Cosa fa ogni regola:**

|Regola|Effetto|
|---|---|
|$S \to aSBc \mid aBc$|genera $a^n (Bc)^n$ per ogni $n \geq 1$ (per $n = 2$: $aaBcBc$)|
|$cB \to Bc$|scambia una $c$ con una $B$ che la segue: ripetuta, porta **tutte le $B$ prima delle $c$**|
|$aB \to ab$|trasforma in $b$ la $B$ più a sinistra, quella subito dopo l'ultima $a$|
|$bB \to bb$|trasforma in $b$ le altre $B$, **solo se** hanno già una $b$ subito prima|

**Derivazione di $aabbcc$:**

$$S \Rightarrow aSBc \Rightarrow aaBcBc \Rightarrow aaBBcc \Rightarrow aabBcc \Rightarrow aabbcc$$

Passo per passo:

1. $S \to aSBc$
2. $S \to aBc$ dentro: $a\,(aBc)\,Bc = aaBcBc$
3. $cB \to Bc$ sulla $c$ seguita da $B$: $aaBBcc$
4. $aB \to ab$ sulla $B$ dopo l'ultima $a$: $aabBcc$
5. $bB \to bb$: $aabbcc$

**Perché è monotone:** $S \to aSBc$ passa da 1 a 4 simboli e le altre regole sostituiscono 2 simboli con 2 simboli. Nessuna accorcia.

**Perché non è libera dal contesto:** $cB \to Bc$ e le altre hanno **due simboli a sinistra**. E non potrebbe esserlo: $\{a^n b^n c^n\}$ **non è un CFL** (si dimostra con l'intersezione di due CFL, vedi nota _Linguaggi liberi 75-93_, sezione sulla non chiusura rispetto all'intersezione).

> Perché l'ordine delle regole è importante: le $B$ non possono trasformarsi in $b$ finché non sono state portate tutte a sinistra delle $c$ e adiacenti alle $a$ (o a una $b$ già prodotta). In questo modo si ottengono solo stringhe con tutte le $a$, poi tutte le $b$, poi tutte le $c$, nello stesso numero.

### Esempio di grammatica regolare (tipo 3)

$$S \to aS \mid a$$

Genera

$$L(G) = \{a^n \mid n \geq 1\} = \{a\}^+$$

Ogni regola è della forma $A \to aB$ ($S \to aS$) oppure $A \to a$ ($S \to a$).

---

## Aggiungere $\epsilon$ al linguaggio

Non basta aggiungere $S \to \epsilon$ a una grammatica $G$, perché $S$ compare a destra (per esempio in $aS$) e violerebbe la condizione della nota (*).

**Costruzione.** Da $G = (V, T, P, S)$ si ottiene $G'$ con un **nuovo simbolo iniziale $S'$** (che non compare in nessun corpo) e le produzioni:

- $S' \to \epsilon$;
- $S' \to \alpha$ per **ogni** produzione $S \to \alpha \in P$;
- tutte le produzioni di $G$ restano.

Risultato:

$$L(G') = L(G) \cup \{\epsilon\}$$

**Perché funziona.** $S'$ viene usato solo come **primo passo**: o si ottiene $\epsilon$, oppure si fa la stessa mossa che avrebbe fatto $S$. Dopo il primo passo la derivazione prosegue come in $G$. Inoltre $S'$ non compare mai a destra, quindi la condizione di (*) è rispettata.

### Esempi con $\epsilon$

**Monotone $G'$:**

$$\begin{align} S' &\to aSBc \mid aBc \mid \epsilon \\ S &\to aSBc \mid aBc \\ cB &\to Bc \\ aB &\to ab \\ bB &\to bb \end{align}$$ $$L(G') = \{a^n b^n c^n \mid n \geq 0\}$$

**Regolare $G'$:**

$$\begin{align} S' &\to aS \mid a \mid \epsilon \\ S &\to aS \mid a \end{align}$$ $$L(G') = \{a^n \mid n \geq 0\} = \{a\}^*$$

---

## La gerarchia dei linguaggi

A ogni tipo di grammatica corrisponde una classe di linguaggi, con una macchina che la riconosce.

|Tipo|Classe di linguaggi|Chi li riconosce|
|---|---|---|
|**0**|**Semi-decidibili**|procedura (**macchina di Turing**) che, se la stringa appartiene al linguaggio, **si ferma e accetta**; altrimenti rifiuta **oppure non si ferma**|
|**1**|**Dipendenti dal contesto**|algoritmo **non deterministico** (macchina di Turing che **si ferma sempre**) con complessità di spazio **lineare**, cioè $\text{NSPACE}(n)$|
|**2**|**Liberi dal contesto**|**PDA** non deterministico|
|**3**|**Regolari**|**automa a stati finiti** (DFA o NFA) oppure descritti da una **espressione regolare**|

### Commento classe per classe

**Tipo 3, regolari.** Memoria finita. DFA, NFA ed espressioni regolari sono equivalenti. Sono i linguaggi della prima parte del corso.

**Tipo 2, liberi dal contesto.** Memoria = **una pila**. Il PDA è quello **non deterministico** del capitolo. I DPDA riconoscono meno linguaggi (Regolari $\subset L(\text{DPDA}) \subset$ CFL, vedi nota _Linguaggi liberi 75-93_).

**Tipo 1, dipendenti dal contesto.** Macchina di Turing non deterministica che usa **spazio lineare** nella lunghezza dell'input: può leggere e scrivere, ma solo su un nastro grande quanto l'input (nel libro sono gli _automi limitati linearmente_). Il non determinismo è quello visto per gli NFA e i PDA: la macchina "sceglie bene" tra più mosse. Gli algoritmi di questa classe **terminano sempre**, quindi i linguaggi di tipo 1 sono **decidibili**.

**Tipo 0, semi-decidibili.** Macchine di Turing senza limiti di spazio. Se la stringa è nel linguaggio la macchina prima o poi si ferma e accetta. Se non lo è, **può non fermarsi mai**, quindi non si può essere sicuri di un "no". Per questo "semi-decidibile": si decide solo il sì.

### Ogni classe contiene la precedente (inclusioni strette)

$$\text{Regolari} \subset \text{Liberi dal contesto} \subset \text{Dipendenti dal contesto} \subset \text{Semi-decidibili}$$

**Perché per le grammatiche si vede subito:**

- Tipo 3 $\subseteq$ tipo 2: $A \to aB$ e $A \to a$ sono già della forma $A \to \beta$ con $\beta$ non vuota.
- Tipo 2 $\subseteq$ tipo 1: $A \to \beta$ con $|\beta| \geq 1 = |A|$, quindi non accorcia.
- Tipo 1 $\subseteq$ tipo 0: le restrizioni del tipo 1 sono un caso particolare di nessuna restrizione.

Che le inclusioni siano **strette**, cioè che ogni classe abbia linguaggi che non stanno nella precedente, lo mostrano gli esempi qui sotto.

---

## Schema con gli esempi di linguaggi

Lo schema dell'ultima pagina è una serie di insiemi concentrici, dall'interno all'esterno:

```
Semi-decidibili                        FOL-VALID, FOL-SAT
  Dipendenti dal contesto              {a^n b^n c^n}
    Liberi dal contesto                {a^n b^n}
      Regolari                         {a^n}
```

|Classe|Esempio|Perché sta lì (e non in quella più interna)|
|---|---|---|
|Regolari|$\{a^n\}$|lo riconosce un DFA con un solo ciclo|
|Liberi dal contesto|$\{a^n b^n\}$|lo riconosce un PDA (si impilano le $a$, si tolgono con le $b$). **Non è regolare**: si dimostra con il pumping lemma, come per le palindrome|
|Dipendenti dal contesto|$\{a^n b^n c^n\}$|lo genera la grammatica monotone vista sopra. **Non è libero dal contesto**: intersezione di due CFL (vedi nota _Linguaggi liberi 75-93_)|
|Semi-decidibili|FOL-VALID, FOL-SAT|problemi della logica del primo ordine, fuori dalle classi decidibili|

### FOL-VALID e FOL-SAT

- **FOL-VALID:** dire se una formula della logica del primo ordine (FOL) è **valida**, cioè vera in ogni interpretazione.
- **FOL-SAT:** dire se è **soddisfacibile**, cioè vera in almeno un'interpretazione.

Nota mia, non dalle slide: in senso stretto la validità di FOL è semi-decidibile (si possono enumerare le dimostrazioni, e se la formula è valida se ne trova una), ma non decidibile. La soddisfacibilità è il problema "complementare" di uno semi-decidibile (la formula è insoddisfacibile se e solo se la negazione è valida). La slide li colloca entrambi nella zona esterna dello schema, per indicare i problemi **oltre** le classi decidibili.
