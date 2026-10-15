## Alberi Sintattici

> [!abstract] L'albero sintattico è una rappresentazione (sintattica) della struttura di una stringa $w \in L(G)$.

> [!error] Problema dell'ambiguità
> Per una stessa stringa potrebbero esistere più alberi sintattici distinti. L'obiettivo ideale è avere grammatiche **non ambigue**, così da garantire che ogni programma/frase abbia un'unica interpretazione sintattica "vera".

### Regole di Costruzione di un Albero Sintattico

![[Pasted image 20261015115354.png|338]]
Dato un albero per una **grammatica** $G = (V, T, P, S)$ questo deve seguire 3 vincoli:
1. **Nodi interni:** Devono sempre essere etichettati con una **variabile** (non-terminale in $V$).
    
2. **Foglie:** Possono essere etichettate con un **terminale** ($T$), una **variabile** ($V$), oppure la stringa vuota ($\epsilon$). Se una foglia è $\epsilon$, dev'essere l'unico figlio del genitore.
    
3. **Relazione Padre-Figli:** Se un nodo interno $A$ ha figli etichettati $X_1, X_2, \dots, X_k$ (da sinistra a destra), allora nella grammatica deve esistere la regola di produzione $$A \rightarrow X_1 X_2 \dots X_k \in P$$
> [!hint] Il prodotto di un albero sintattico è la **stringa di foglie da sinistra a destra**
> ![[Pasted image 20261015115609.png]]
> $$ E \overset{*}{\Rightarrow} a*(a+b00)$$

> [!important] 
> Se costruissimo tutti gli alberi con:
> 1. Il prodotto che è una stringa terminale.
> 2. La radice che è etichettata dal simbolo iniziale.
> 
> L’insieme dei prodotti di questi alberi sintattici e’ il linguaggio della grammatica

---
### Teorema di Equivalenza tra Alberi e Derivazioni

Questo teorema stabilisce che i tre seguenti concetti sono **perfettamente equivalenti**:
1. Esiste una derivazione generica $A \overset{*}{\Rightarrow} w$.
2. Esiste una **derivazione a sinistra** $A \mathop{\overset{*}{\Rightarrow}}\limits_{lm} w$ (o a destra $A \mathop{\overset{*}{\Rightarrow}}\limits_{rm} w$).
3. Esiste un **albero sintattico** con radice $A$ e prodotto $w$.
#### Esempio per dimostrare il Teorema

##### Costruiamo la derivata a sinistra

Andiamo a de-costruire l'albero suddividendolo in sottoalberi
![[Pasted image 20261015124008.png]]

---

### Ambiguità in Grammatiche e Linguaggi

Consideriamo la grammatica: $$E \rightarrow I \mid E+E \mid E*E \mid (E)$$
>[!error] Per la stringa $E+E*E$, possiamo trovare due derivazioni diverse che producono **due alberi sintattici distinti**
>1.  $E + (E * E)$
>2. $(E+E)*E$

#### Definizione Formale di Grammatica Ambigua

> [!quote] Definizione non formale
> Ciò che rende dannosa una grammatica è unicamente l'esistenza di **più alberi sintattici distinti** per la stessa stringa.

Una CFG $G = (V, T, P, S)$ è **ambigua** se esiste almeno una stringa $w \in T^*$ che ammette **due o più alberi sintattici distinti**.

Se ogni stringa generata dal linguaggio $L(G)$ ha un **unico** albero sintattico, la grammatica è detta **non-ambigua**.

> [!question] Come rimuovere le ambiguità?
> Per le espressioni matematiche, risolviamo l'ambiguità introducendo una **gerarchia di variabili**:
> 1. **$E$ (Expressions):** Somma di Termini ($+$).
> 2. **$T$ (Terms):** Moltiplicazione di Fattori ($*$).
> 3. **$F$ (Factors):** Identificatori $I$ o espressioni tra parentesi $(E)$


> [!warning] Ci sono ancora ambiguità su sequenze dello stesso operatore 
> $a+b+c$ va letto come $(a+b)+c$ o $a+(b+c)$?
>
> La soluzione unica è forzare la letturra $(a+b)+c$ (associativa a sinistra),
> per fare ciò bisogna imporre la ricorsione a **SINISTRA**
> > [!hint]
> > $E \rightarrow E - T \mid T$ ha la ricorsione a sinistra
> > $E \rightarrow T \mid E - T$ ha la ricorsione a destra

