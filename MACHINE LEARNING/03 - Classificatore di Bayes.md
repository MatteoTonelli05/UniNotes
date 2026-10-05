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
>**Densità condizionale $p(x\vert{}w_i)$ in un intorno del punto $x$**, contando i punti presenti nella regione circolare attorno a $x$: $$\text{Per i maschi } (w_1):p(x|w_1)=\frac{1}{8} \text{(1 punto blu su 8)}$$$$
>\text{Per le femmine } (w_2):p(x|w_2)=\frac{2}{10}=\frac{1}{5} \text{(2 punto rossi su 10)}$$
>Calcoliamo quindi $p(x) = \left(\frac{1}{8} \times \frac{8}{18}\right) + \left(\frac{1}{5} \times \frac{10}{18}\right) = \frac{1}{18} + \frac{2}{18} = \frac{3}{18} = \frac{1}{6}$
> **Calcolo della Probabilità a Posteriori**:
$$P(w_1\vert{}x) = \frac{1/18}{1/6} = \frac{3}{9} = \frac{1}{3} \approx 33.3\%$$
$$P(w_2\vert{}x) = \frac{2/18}{1/6} = \frac{6}{9} = \frac{2}{3} \approx 66.7\%$$
 Poiché $P(w_2\vert{}x) > P(w_1\vert{}x)$, il pattern $x$ viene classificato come **femmina ($w_2$)**.

---

> [!important] Problema
> La stima delle probabilità a priori $P(w_i)$ è generalmente semplice, ma conoscere le reali densità di probabilità condizionali $p(x\vert{}w_i)$ è quasi impossibile in contesti reali. Per ovviare a ciò, si utilizzano due approcci principali

---
## Bayes: Approccio Parametrico

Si ipotizza a priori la forma analitica della distribuzione (es. distribuzione Gaussiana/Multinormale) e dal training set si apprendono solo i parametri chiave (es. vettore medio $\mu$ e matrice di covarianza $\Sigma$).

> [!attention] Ha **meno gradi di libertà** e riduce notevolmente il rischio di _overfitting_ quando il training set è piccolo.

### Parametrico - Distribuzione normale mono-dimensionale ($d=1$)

La densità di probabilità della curva a campana è definita come:
$$p(x) = \frac{1}{\sigma\sqrt{2\pi}} e^{-\frac{(x-\mu)^{2}}{2\sigma^{2}}}$$
In cui
- **$\mu$ (Media o valor medio)**: Indica la **posizione del centro** della campana (dove la curva raggiunge il suo picco massimo).
    
- **$\sigma$ (Deviazione Standard o scarto quadratico medio)**: Indica quanto la campana è **larga o stretta**.
    
- **$\sigma^2$ (Varianza)**: Il quadrato della deviazione standard.

![[Pasted image 20261005122517.png|466]]
> [!question] Come calcolare $\mu$ e $\sigma^2$ dai dati
> ![[Pasted image 20261005123451.png]]
> Per calcolare la **media campionaria** ($\mu$) si sommano tutti i numeri e si dividono per il numero dei campioni$$\mu = \frac{1}{10} \sum_{i=1}^{10} x_i = \frac{3+7+9+(-2)+15+54+(-11)+0+23+(-8)}{10} = \frac{90}{10} = 9$$
> _il centro della campana gaussiana sarà posizionato a 9_
>
>Per la varianza campionaria ($\mu^2$) bisogna invece misurare la distanza da ogni punto alla media 9, elevarla al quadrato e fare la media di quegli scarti:
>$$\sigma^2 = \frac{1}{10} \sum_{i=1}^{10} (x_i - \mu)^2 = \frac{(3-9)^2 + (7-9)^2 + (9-9)^2 + \dots + (-8-9)^2}{10} = 318.8$$
>_La deviazione standard sarà $\sigma = \sqrt{318.8} \approx 17.855$._
>

### Parametrico - Distribuzione Normale Multivariata (Multinormale)

> [!question] Cosa cambia dalla variante mono-dimensionale al multivariato?
> Nel caso multivariato abbiamo $d>1$ ovvero due o più caratteristiche contemporaneamente

Quando un dato $x$ è un vettore con $d$ caratteristiche (es. $x = [\text{peso}, \text{altezza}]^T$), la curva a campana monodimensionale diventa una **superficie/solido tridimensionale** (o una iper-superficie in più di 3 dimensioni)

Per non fare confusione tra i vettori e i singoli numeri:
- $x_i$: Il pattern $i$-esimo (es. le misure dell'individuo $i$)
- $x_i^j$: La componente $j$-esima (lo scalare) del pattern $i$-esimo.

Con la formula della densità multinormale $$p(x) = \frac{1}{(2\pi)^{d/2} \vert{}\Sigma\vert{}^{1/2}} \, e^{-\frac{1}{2} (x - \mu)^t \Sigma^{-1} (x - \mu)}$$
> [!important] I due parametri fondamentali
> Nel caso 1D avevamo la media $\mu$ e la varianza $\sigma^2$. Nel caso $d$-dimensionale abbiamo:
> - Vettore medio $\mu = [\mu^1,\mu^2,...,\mu^d]^T$ che fissa le coordinate del centro della distribuzione
> - È una matrice quadrata $d \times d$ che descrive la dispersione dei dati in tutte le direzioni.$$\Sigma = \begin{bmatrix} \sigma^{11} & \sigma^{12} & \dots & \sigma^{1d} \\ \sigma^{21} & \sigma^{22} & \dots & \sigma^{2d} \\ \vdots & \vdots & \ddots & \vdots \\ \sigma^{d1} & \sigma^{d2} & \dots & \sigma^{dd} \end{bmatrix}$$

> [!info] Info e Proprietà
> 1. È sempre **simmetrica** ($\sigma^{ij} = \sigma^{ji}$) e **definita positiva** (quindi ammette sempre la matrice inversa $\Sigma^{-1}$). Essendo simmetrica, per definirla bastano $\frac{d(d+1)}{2}$ parametri distinti.
> 2. Gli elementi diagonali ($\sigma^{ii}$) sono le **varianze** delle singole componenti $x^i$ (ossia $(\sigma^i)^2$)
> 3. Gli elementi fuori diagonali **($\sigma^{ij}$ con $i \neq j$)** sono le **covarianze** tra la caratteristica $x^i$ e la caratteristica $x^j$:
> 	- **$\sigma^{ij} = 0$**: $x^i$ e $x^j$ sono **statisticamente indipendenti**.
> 	- **$\sigma^{ij} > 0$**: **Correlazione positiva** (all'aumentare di $x^i$ tende ad aumentare anche $x^j$).
> 	- **$\sigma^{ij} < 0$**: **Correlazione negativa** (all'aumentare di $x^i$ tende a diminuire $x^j$).
> 	
![[Pasted image 20261005164412.png|305]]

### Distanza di Mahalanobis

$$r^2=(x-\mu)^t \Sigma^{-1} (x-\mu)$$
![[Pasted image 20261005172953.png|316]]
> [!important] La Distanza di Mahalanobis definisce la distanza dal centro di un certo dato.
> Se arriva un ragazzo alto 200 cm che pesa 100 kg, la sua distanza dal centro $\mu$ è 0.
> Se arriva un ragazzo alto 200 cm ma che pesa 60 kg:
>
>- La **distanza Euclidea normale** vedrebbe solo la differenza di peso (40 kg di scarto).
>
>- La **distanza di Mahalanobis** dice: _"Ehi, per un giocatore alto 200 cm, pesare 60 kg è un'anomalia assurda perché le due cose sono correlate!"_ e gli dà una distanza **enorme** (lo scarta).


---
