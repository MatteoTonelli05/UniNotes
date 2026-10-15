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
Una CFG è un **quadrubla** $$G = (V,T,P,S)$$dove:
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

> Rispettivamente **RightMost e LeftMost Derivation**, si possono usare entrambe ma in quel caso non ci sarebbe nessuna regola di scelta

![[Pasted image 20261015111809.png|565]]


