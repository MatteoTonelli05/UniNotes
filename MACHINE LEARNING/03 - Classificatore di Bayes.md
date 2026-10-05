## Bayes

> [!abstract] Teoria
> Il problema della classificazione viene formalizzato in termini probabilistici. Se tutte le distribuzioni di probabilità in gioco sono note, la regola di Bayes rappresenta la **soluzione ottima** (migliore classificazione teoricamente possibile).

Sia $\mathbf{V}$ uno spazio di pattern $d$-dimensionali e $W = \{w_1, w_2 \dots w_s\}$ un insieme di $s$ classi disgiunte costituite da elementi di $\mathbf{V}$

Per ogni $\mathbf{x} \in \mathbf{V}$ (**dato**) e per ogni $w_i \in W$ (**classe**), indichiamo con $p(\mathbf{x}|w_i)$ la *densità di probabilità condizionata di $\mathbf{x}$ data $w_i$, ovvero la densità di probabilità che il prossimo pattern sia $\mathbf{x}$ sotto l'ipotesi che la sua classe di appartenenza sia $w_i$

Per ogni $w_i \in W$, indichiamo con $P(w_i)$ la *probabilità a priori* di $w_i$ ovvero la probabilità, indipendentemente dall'osservazione, che il prossimo pattern da classificare sia di classe $w_i$

Per ogni $\mathbf{x} \in \mathbf{V}$ indichiamo con $p(\mathbf{x})$ la *densità di probabilità assoluta* di $\mathbf{x}$, ovvero la densità di probabilità che il prossimo pattern da classificare sia $\mathbf{x}$

$$p(\mathbf{x}) = \sum_{i=1}^{s} p(\mathbf{x}|w_i) \cdot P(w_i) \qquad \text{dove} \qquad \sum_{i=1}^{s} P(w_i) = 1$$

Per ogni $w_i \in W$ e per ogni $\mathbf{x} \in \mathbf{V}$ indichiamo con $P(w_i|\mathbf{x})$ la *probabilità a posteriori* di $w_i$ dato $\mathbf{x}$, ovvero la probabilità che avendo osservato il pattern $\mathbf{x}$, la classe di appartenenza sia $w_i$. Per il **teorema di Bayes**:

$$P(w_i|\mathbf{x}) = \frac{p(\mathbf{x}|w_i) \cdot P(w_i)}{p(\mathbf{x})}$$

> [!hint] Concetto di Ottimale
> Se conosciamo dunque sia la frequenza delle classi sia la forma dei dati al loro interno, applicare la regola di Bayes ci permette di calcolare la probabilità **a posteriori** $P(w_i\vert{}\mathbf{x})$, ovvero la certezza che un elemento appartenga alla classe $w_i$ dopo averne osservato le caratteristiche $\mathbf{x}$. Scegliere la classe con la probabilità a posteriori più alta garantisce matematicamente il minor numero di errori possibile.

## Classificatore di Bayes

Dato un pattern $x$ da classificare in una delle $s$ classi $w_1,w_2,...,w_s$ di cui sono note:
- la probabilità a priori $P(w_1),P(w_2),...,P(w_s)$
- la densità di probabilità condizionali $p(x|w_1),p(x|w_2),...,p(x|w_s)$

>[!abstract] La regola di classificazione di Bayes assegna $x$ alla classe $b$ per cui è massima la probabilità a posteriori:$$b=\arg\max_{i=1..s}{\{P(w_i|x)\}}$$
 
 Massimizzare la probabilità a posteriori significa massimizzare la densità di probabilità condizionale tenendo comunque conto della probabilità a priori delle classi.

> [!hint] La regola si dimostra ottima in quanto minimizza l’errore di classificazione. Ad esempio nel caso di 2 classi e 𝑑 = 1 $$P(\text{error}) = \int_{\mathcal{R}_1} p(x\vert{}w_2)P(w_2)\,dx + \int_{\mathcal{R}_2} p(x\vert{}w_1)P(w_1)\,dx$$![[Pasted image 20260928155630.png|470]]
> Le due curve rappresentano le distribuzioni di probabilità delle classi (ad esempio l'altezza di donne e uomini pesata sulla loro frequenza). L'area di sovrapposizione indica l'incertezza e definisce l'errore inevitabile; per questo si fissa una **soglia di decisione** ($x_B$) per cui, se $x > x_B$, il dato viene assegnato a una classe, altrimenti all'altra. 
> 
> _Il grafico dimostra visivamente che **solo mettendo il confine $x_B$ nel punto in cui le due curve si incrociano si ottiene l'area d'errore minima**. Qualsiasi altra scelta ($x^*$) aggiunge il triangolino rosso di "errore evitabile"._

> [!example] Esempio
> **Problema**: Classificare persone in due classi ($W = \{w_1, w_2\}$ con $w_1 = \text{maschi (blu)}$ e $w_2 = \text{femmine (rosso)}$) sulla base di due caratteristiche ($d=2$: peso ed altezza).
> 
>![[Pasted image 20261005105634.png]]
>_**Dati del Training Set (18 campioni totali)**: 8 maschi e 10 femmine._
>Quindi possiamo dire che la probabilità a priori è $$P(w_1) = \frac{8}{18}, \quad P(w_2) = \frac{10}{18}$$
>**Densità condizionale $p(x\vert{}w_i)$ in un intorno del punto $x$**, contando i punti presenti nella regione circolare attorno a $x$: $$\text{Per i maschi } (w_1):p(x|w_1)=\frac{1}{8} \text{(1 punto blu su 8)}
>\text{Per le femmine } (w_2):p(x|w_2)=\frac{2}{10}=\frac{1}{5} \text{(2 punto blu su 10)}$$
>Calcoliamo quindi $p(x) = \left(\frac{1}{8} \times \frac{8}{18}\right) + \left(\frac{1}{5} \times \frac{10}{18}\right) = \frac{1}{18} + \frac{2}{18} = \frac{3}{18} = \frac{1}{6}$
> **Calcolo della Probabilità a Posteriori**:
$$P(w_1\vert{}x) = \frac{1/18}{1/6} = \frac{3}{9} = \frac{1}{3} \approx 33.3\%$$
$$P(w_2\vert{}x) = \frac{2/18}{1/6} = \frac{6}{9} = \frac{2}{3} \approx 66.7\%$$
 Poiché $P(w_2\vert{}x) > P(w_1\vert{}x)$, il pattern $x$ viene classificato come **femmina ($w_2$)**.

