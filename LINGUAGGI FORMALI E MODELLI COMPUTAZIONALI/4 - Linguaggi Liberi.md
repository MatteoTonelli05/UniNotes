>[!quote] Ricorda
>Linguaggi $\rightarrow$ Linguaggi Formali $\rightarrow$ Insiemi di stringhe

---

## Grammatiche Libere Dal Contesto (**CFG**)

> [!question] A cosa servono?
> Sono nate per descrivere il linguaggio naturale
> 
> _Esempio: in un automa a stati finiti si possono gestire un FINITO numero di parentesi, nella realtà (banalmente nei linguaggi) possiamo inserire un qualsiasi numero di parentesi_

### Esempio informale di CFG

Consideriamo $L_{pal}=\{w\in\Sigma^*:w=w^R\} \quad$ _il linguaggio delle stringhe palindrome_

> [!hint] Definiamo $L_{pal} induttivamente$
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
