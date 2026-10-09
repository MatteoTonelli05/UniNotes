# Classificatore di Bayes

## Fondamenti Teorici

> [!abstract] Teoria
> Il problema della classificazione viene formalizzato in termini probabilistici. Se tutte le distribuzioni di probabilità in gioco sono note, la regola di Bayes rappresenta la **soluzione ottima** (migliore classificazione teoricamente possibile).

Sia $\mathbf{V}$ uno spazio di pattern $d$-dimensionali e $W = \{w_1, w_2 \dots w_s\}$ un insieme di $s$ classi disgiunte costituite da elementi di $\mathbf{V}$.

- **Dato**: Per ogni $\mathbf{x} \in \mathbf{V}$ e per ogni $w_i \in W$, indichiamo con $p(\mathbf{x}|w_i)$ la *densità di probabilità condizionata* di $\mathbf{x}$ data $w_i$, ovvero la densità di probabilità che il prossimo pattern sia $\mathbf{x}$ sotto l'ipotesi che la sua classe di appartenenza sia $w_i$.
- **Probabilità a Priori**: Per ogni $w_i \in W$, indichiamo con $P(w_i)$ la *probabilità a priori* di $w_i$, ovvero la probabilità, indipendentemente dall'osservazione, che il prossimo pattern da classificare sia di classe $w_i$.
- **Densità Assoluta (Evidenza)**: Per ogni $\mathbf{x} \in \mathbf{V}$, indichiamo con $p(\mathbf{x})$ la *densità di probabilità assoluta* di $\mathbf{x}$, ovvero la densità di probabilità che il prossimo pattern da classificare sia $\mathbf{x}$:
  $$p(\mathbf{x}) = \sum_{i=1}^{s} p(\mathbf{x}|w_i) \cdot P(w_i) \qquad \text{dove} \qquad \sum_{i=1}^{s} P(w_i) = 1$$
- **Probabilità a Posteriori**: Per ogni $w_i \in W$ e per ogni $\mathbf{x} \in \mathbf{V}$, indichiamo con $P(w_i|\mathbf{x})$ la *probabilità a posteriori* di $w_i$ dato $\mathbf{x}$, ovvero la probabilità che, avendo osservato il pattern $\mathbf{x}$, la classe di appartenenza sia $w_i$.

Per il **Teorema di Bayes**:
$$P(w_i|\mathbf{x}) = \frac{p(\mathbf{x}|w_i) \cdot P(w_i)}{p(\mathbf{x})}$$

> [!hint] Concetto di Ottimale
> Se conosciamo dunque sia la frequenza delle classi sia la forma dei dati al loro interno, applicare la regola di Bayes ci permette di calcolare la probabilità **a posteriori** $P(w_i|\mathbf{x})$, ovvero la certezza che un elemento appartenga alla classe $w_i$ dopo averne osservato le caratteristiche $\mathbf{x}$. Scegliere la classe con la probabilità a posteriori più alta garantisce matematicamente il minor numero di errori possibile.

---

### Regola di Classificazione ed Errore Bayesiano

Dato un pattern $x$ da classificare in una delle $s$ classi $w_1,w_2,...,w_s$ di cui sono note:
- la probabilità a priori $P(w_1),P(w_2),...,P(w_s)$
- la densità di probabilità condizionali $p(x|w_1),p(x|w_2),...,p(x|w_s)$

> [!abstract] Regola di Bayes
> La regola di classificazione di Bayes assegna $x$ alla classe $b$ per cui è massima la probabilità a posteriori:
> $$b = \arg\max_{i=1..s} \{P(w_i|x)\}$$

Massimizzare la probabilità a posteriori significa massimizzare la densità di probabilità condizionale tenendo comunque conto della probabilità a priori delle classi.

> [!hint] Errore Minimo
> La regola si dimostra ottima in quanto minimizza l'errore di classificazione. Ad esempio nel caso di 2 classi e $d = 1$:
> $$P(\text{error}) = \int_{\mathcal{R}_1} p(x|w_2) P(w_2) \, dx + \int_{\mathcal{R}_2} p(x|w_1) P(w_1) \, dx$$
> ![[Pasted image 20260928155630.png|470]]
> 
> Le due curve rappresentano le distribuzioni di probabilità delle classi (ad esempio l'altezza di donne e uomini pesata sulla loro frequenza). L'area di sovrapposizione indica l'incertezza e definisce l'errore inevitabile; per questo si fissa una **soglia di decisione** ($x_B$) per cui, se $x > x_B$, il dato viene assegnato a una classe, altrimenti all'altra.
> 
> _Il grafico dimostra visivamente che **solo mettendo il confine $x_B$ nel punto in cui le due curve si incrociano si ottiene l'area d'errore minima**. Qualsiasi altra scelta ($x^*$) aggiunge il triangolino rosso di "errore evitabile"._

---

### Esempio Empirico (Maschi vs Femmine 2D)

> [!example] Esempio
> **Problema**: Classificare persone in due classi ($W = \{w_1, w_2\}$ con $w_1 = \text{maschi (blu)}$ e $w_2 = \text{femmine (rosso)}$) sulla base di due caratteristiche ($d=2$: peso ed altezza).
> 
> ![[Pasted image 20261005105634.png]]
> 
> _**Dati del Training Set (18 campioni totali)**: 8 maschi e 10 femmine._
> Quindi possiamo dire che la probabilità a priori è:
> $$P(w_1) = \frac{8}{18}, \quad P(w_2) = \frac{10}{18}$$
> 
> **Densità condizionale $p(x|w_i)$ in un intorno del punto $x$**, contando i punti presenti nella regione circolare attorno a $x$:
> $$\text{Per i maschi } (w_1): p(x|w_1) = \frac{1}{8} \quad \text{(1 punto blu su 8)}$$
> $$\text{Per le femmine } (w_2): p(x|w_2) = \frac{2}{10} = \frac{1}{5} \quad \text{(2 punti rossi su 10)}$$
> 
> Calcoliamo quindi l'evidenza $p(x)$:
> $$p(x) = \left(\frac{1}{8} \times \frac{8}{18}\right) + \left(\frac{1}{5} \times \frac{10}{18}\right) = \frac{1}{18} + \frac{2}{18} = \frac{3}{18} = \frac{1}{6}$$
> 
> **Calcolo della Probabilità a Posteriori**:
> $$P(w_1|x) = \frac{1/18}{1/6} = \frac{3}{9} = \frac{1}{3} \approx 33.3\%$$
> $$P(w_2|x) = \frac{2/18}{1/6} = \frac{6}{9} = \frac{2}{3} \approx 66.7\%$$
> 
> Poiché $P(w_2|x) > P(w_1|x)$, il pattern $x$ viene classificato come **femmina ($w_2$)**.

---

## Bayes: Approccio Parametrico

> [!important] Problema
> La stima delle probabilità a priori $P(w_i)$ è generalmente semplice, ma conoscere le reali densità di probabilità condizionali $p(x|w_i)$ è quasi impossibile in contesti reali. Per ovviare a ciò, si ipotizza a priori la forma analitica della distribuzione (es. distribuzione Gaussiana/Multinormale) e dal training set si apprendono solo i parametri chiave (es. vettore medio $\mu$ e matrice di covarianza $\Sigma$).
> 
> Ha **meno gradi di libertà** e riduce notevolmente il rischio di *overfitting* quando il training set è piccolo.

### Stima per Massima Verosimiglianza (Maximum Likelihood Estimation - MLE)

È il metodo statistico utilizzato nell'approccio parametrico per calcolare i parametri incogniti di una distribuzione teorica (es. $\mu$ e $\sigma^2$ per la Gaussiana) a partire dai dati reali del Training Set.

> [!attention] Scelta della forma analitica
> La scelta della forma analitica va comunque verificata; può essere valutata in due modalità:
> - **In modo formale**: test statistici (es. test di Malkovich - Afifi).
> - **In modo empirico**: analisi di tool predisposti o confronto degli istogrammi con le curve teoriche.

---

### Parametrico - Distribuzione normale mono-dimensionale ($d=1$)

La densità di probabilità della curva a campana è definita come:
$$p(x) = \frac{1}{\sigma\sqrt{2\pi}} e^{-\frac{(x-\mu)^{2}}{2\sigma^{2}}}$$

In cui:
- **$\mu$ (Media o valor medio)**: Indica la **posizione del centro** della campana (dove la curva raggiunge il suo picco massimo).
- **$\sigma$ (Deviazione Standard o scarto quadratico medio)**: Indica quanto la campana è **larga o stretta**.
- **$\sigma^2$ (Varianza)**: Il quadrato della deviazione standard.

![[Pasted image 20261005122517.png|466]]

> [!question] Come calcolare $\mu$ e $\sigma^2$ dai dati
> ![[Pasted image 20261005123451.png]]
> 
> Per calcolare la **media campionaria** ($\mu$) si sommano tutti i numeri e si dividono per il numero dei campioni:
> $$\mu = \frac{1}{10} \sum_{i=1}^{10} x_i = \frac{3+7+9+(-2)+15+54+(-11)+0+23+(-8)}{10} = \frac{90}{10} = 9$$
> _Il centro della campana gaussiana sarà posizionato a 9._
> 
> Per la **varianza campionaria** ($\sigma^2$) bisogna invece misurare la distanza da ogni punto alla media 9, elevarla al quadrato e fare la media di quegli scarti:
> $$\sigma^2 = \frac{1}{10} \sum_{i=1}^{10} (x_i - \mu)^2 = \frac{(3-9)^2 + (7-9)^2 + (9-9)^2 + \dots + (-8-9)^2}{10} = 318.8$$
> _La deviazione standard sarà $\sigma = \sqrt{318.8} \approx 17.855$._

---

### Parametrico - Distribuzione Normale Multivariata (Multinormale)

> [!question] Cosa cambia dalla variante mono-dimensionale al multivariato?
> Nel caso multivariato abbiamo $d > 1$, ovvero due o più caratteristiche considerate contemporaneamente.

Quando un dato $x$ è un vettore con $d$ caratteristiche (es. $x = [\text{peso}, \text{altezza}]^T$), la curva a campana monodimensionale diventa una **superficie/solido tridimensionale** (o una iper-superficie in più di 3 dimensioni).

Per non fare confusione tra i vettori e i singoli numeri:
- $x_i$: Il pattern $i$-esimo (es. le misure dell'individuo $i$).
- $x_i^j$: La componente $j$-esima (lo scalare) del pattern $i$-esimo.

**Formula della densità multinormale**:
$$p(x) = \frac{1}{(2\pi)^{d/2} |\Sigma|^{1/2}} \, e^{-\frac{1}{2} (x - \mu)^t \Sigma^{-1} (x - \mu)}$$

#### I due parametri fondamentali
Nel caso 1D avevamo la media $\mu$ e la varianza $\sigma^2$. Nel caso $d$-dimensionale abbiamo:
1. **Vettore medio**: $\mu = [\mu^1, \mu^2, \dots, \mu^d]^T$, che fissa le coordinate del centro della distribuzione.
2. **Matrice di covarianza**: Matrice quadrata $d \times d$ che descrive la dispersione dei dati in tutte le direzioni:
   $$\Sigma = \begin{bmatrix} \sigma^{11} & \sigma^{12} & \dots & \sigma^{1d} \\ \sigma^{21} & \sigma^{22} & \dots & \sigma^{2d} \\ \vdots & \vdots & \ddots & \vdots \\ \sigma^{d1} & \sigma^{d2} & \dots & \sigma^{dd} \end{bmatrix}$$

**Info e Proprietà**:
- È sempre **simmetrica** ($\sigma^{ij} = \sigma^{ji}$) e **definita positiva** (quindi ammette sempre la matrice inversa $\Sigma^{-1}$). Essendo simmetrica, per definirla bastano $\frac{d(d+1)}{2}$ parametri distinti.
- Gli **elementi diagonali** ($\sigma^{ii}$) sono le varianze delle singole componenti $x^i$ (ossia $(\sigma^i)^2$).
- Gli **elementi fuori diagonale** ($\sigma^{ij}$ con $i \neq j$) sono le covarianze tra la caratteristica $x^i$ e la caratteristica $x^j$:
  - $\sigma^{ij} = 0$: $x^i$ e $x^j$ sono statisticamente indipendenti.
  - $\sigma^{ij} > 0$: Correlazione positiva (all'aumentare di $x^i$ tende ad aumentare anche $x^j$).
  - $\sigma^{ij} < 0$: Correlazione negativa (all'aumentare di $x^i$ tende a diminuire $x^j$).

---

### Distanza di Mahalanobis

$$r^2 = (x-\mu)^t \Sigma^{-1} (x-\mu)$$

La Distanza di Mahalanobis definisce la distanza dal centro di un certo dato pesando le varianze e le correlazioni.
- Se arriva un ragazzo alto 200 cm che pesa 100 kg, la sua distanza dal centro $\mu$ è 0.
- Se arriva un ragazzo alto 200 cm ma che pesa 60 kg:
  - La **distanza Euclidea normale** vedrebbe solo la differenza di peso (40 kg di scarto).
  - La **distanza di Mahalanobis** dice: *"Ehi, per un giocatore alto 200 cm, pesare 60 kg è un'anomalia assurda perché le due cose sono correlate!"* e gli dà una distanza enorme (lo scarta).

---

### Classificatore di Bayes con Distribuzioni Multinormali

Dopo aver imparato a calcolare la Multinormale per una singola classe, qui vediamo cosa succede quando abbiamo 2 classi di dati nello spazio 2D e dobbiamo decidere dove tracciare la linea di confine tra di esse.

Le due "montagne" del grafico rappresentano le densità di probabilità condizionali delle due classi ($p(\mathbf{x}|\omega_1)$ e $p(\mathbf{x}|\omega_2)$) già pesate per le rispettive probabilità a priori $P(\omega_1)$ e $P(\omega_2)$.
- La regola di Bayes assegna ogni punto $\mathbf{x}$ alla classe con la montagnola più alta in quel punto.
- Proiettando questo confronto sul piano di base, lo spazio viene diviso in regioni di decisione ($\mathcal{R}_1$ e $\mathcal{R}_2$).

#### E sul confine?
Il **Decision Boundary** (o superficie decisionale) è la linea/superficie di confine in cui le probabilità a posteriori delle due classi sono perfettamente uguali ($P(\omega_1|\mathbf{x}) = P(\omega_2|\mathbf{x})$). Perciò sul confine la classificazione è completamente ambigua.

#### La forma geometrica del confine
La forma della linea di confine dipende dalle Matrici di Covarianza ($\Sigma$) delle due classi:
- **$\Sigma_1 = \Sigma_2$**: le due montagne, essendo uguali, si tagliano perfettamente lungo una linea dritta/piano (**Iper-piano**).
  ![[Pasted image 20261006140111.png]]
- **$\Sigma_1 \neq \Sigma_2$**: le due montagne, essendo diverse, si incrociano lungo una curva (**Iper-quadratica**).
  ![[Pasted image 20261006140146.png]]

---

### Bayes e Confidenza di Classificazione

Uno dei più grandi punti di forza del classificatore di Bayes rispetto ad altri algoritmi è che fornisce in output un valore probabilistico reale compreso tra 0 e 1 (la cui somma tra le classi fa 1).

Grazie alla confidenza possiamo:
- Integrare Bayes in multi-classificatori (anche non binari).
- Calcolare la certezza di una valutazione e scartare pattern ambigui (es. se $P(w_1|\mathbf{x}) = 0.51$ e $P(w_2|\mathbf{x}) = 0.49$).

> [!tip] Semplificazione dei Calcoli
> Se non si è interessati alla confidenza (quindi alla percentuale), basta trasformare la formula di Bayes da:
> $$b = \arg\max_{i=1..s} \left\{ P(w_i|x) \right\} = \arg\max_{i=1..s} \left\{ \frac{p(x|w_i) \cdot P(w_i)}{p(x)} \right\}$$
> **a** (eliminando la divisione per $p(x)$):
> $$b = \arg\max_{i=1..s} \left\{ p(x|w_i) \cdot P(w_i) \right\}$$

## Bayes: Approccio Non Parametrico

Nel blocco precedente abbiamo visto l'approccio parametrico: si assume che la distribuzione abbia una forma fissa (la campana Gaussiana) e si stimano solo media $\mu$ e covarianza $\Sigma$.

> [!abstract] Nell'approccio non parametrico 
> Non vengono fatte ipotesi sulle distribuzioni dei pattern e le densità di probabilità $p(x\vert{}w_i)$ sono **stimate direttamente** dal training set.

### Stima della Densità e Curse of Dimensionality

> [!attention] Cos'è la stime della densità?
>La **stima della densità** è il processo attraverso il quale cerchiamo di **ricostruire la funzione di densità di probabilità $p(x)$** di una popolazione a partire da un insieme finito di dati osservati (il _training set_)
>
> _Letteralmente la ricerca del tipo di distribuzione dei dati del dataset_

> [!example] Esempio del Cubo vs Ipercubo
> - In un cubo 3D di lato 1, la distanza media tra due punti casuali è $\approx 0.66$
> - In un ipercubo ad $1.000.000$ di dimensioni (di lato 1), la distanza media sale a **$408.25$**
>
> _è facile intuire che i punti del set così isolati al crescere delle dimensioni rendono difficilissimo stimare la densità_

Come possiamo stimare la densità $p(x)$ in un generico punto $x$ senza usare una formula predefinita?
1. **Definizione della Regione $R$**: Si considera una piccola regione $R$ di volume $V$ centrata attorno al punto $x$ che si vuole valutare.
	
2. **Probabilità $P_1$**: La probabilità che un **punto generico** cada dentro la regione $R$ è $P_1 = \int_{\mathcal{R}} p(x') dx'$.
    
3. **Distribuzione Binomiale**: Dati $n$ campioni indipendenti nel training set, la probabilità che $k$ di questi cadano nella regione $R$ segue una distribuzione binomiale. $$P_k=\binom{n}{k}\cdot P_1^k(1-P_1)^{n-k}$$ 
	l valore medio di punti attesi è $k = n \cdot P_1$, da cui $P_1 \approx \frac{k}{n}$.

> [!hint] Ricorda
> La binomiale è la probabilità che su $n$ tentativi, $k$ volte ci sia successo 

> [!example]
> - **$\binom{n}{k}$** $\rightarrow$ **Per tutte le combinazioni possibili** di disporre $k$ successi in $n$ tentativi...
>- **$p^k$** $\rightarrow$ ...moltiplichi la probabilità che quei $k$ eventi si verifichino tutti...  
>- **$(1 - p)^{n-k}$** $\rightarrow$ ...per la probabilità che i restanti $n - k$ tentativi falliscano tutti.

4. **Approssimazione**: Se la regione $R$ ha un volume $V$ molto piccolo, possiamo assumere che $p(x)$ non vari significativamente al suo interno:$$P_1=\int_Rp(x') dx'\approx p(x)\cdot V$$$$P_1 \approx p(x) \cdot V \implies p(x) = \frac{P_1}{V} = \frac{k}{n \cdot V}$$
### Parzen Window (Kernel Ipercubico vs Soft/Gaussiano)

Il metodo **Parzen Window** definisce la regione di stima $R$ come un **ipercubo $d$-dimensionale** di lato $h_n$ e volume $V_n = h_n^d$. Si definisce la funzione finestra unitaria: $$\varphi(u)=\begin{cases}1 & |u_j|\le\frac{1}{2}, \, j=1...d \\ 0 & \text{altrimenti}\end{cases}$$ Il numero di punti $k_n$ che cadono dentro l'ipercubo centrato in $x$ è dato dalla somma $\sum_{i=1}^{n}\varphi(\frac{x_i-x}{h_n})$. La densità stimata nel punto $x$ diventa quindi: $$p_n(x) = \frac{1}{n \cdot V_n} \sum_{i=1}^{n} \varphi\left(\frac{x_i - x}{h_n}\right)$$
>[!attention] Impatto dell'Iperparametro $h_n$ (Dimensione Finestra) 
> - **Finestra troppo piccola ($h_n$ piccolo)**: Stima rumorosa, molto frastagliata e instabile (forte rischio di *overfitting*). 
> - **Finestra troppo grande ($h_n$ grande)**: Stima sfuocata, troppo smussata e vaga (rischio di *underfitting*). 
> 
> Per garantire convergenza matematica al crescere del numero dei campioni $n$, la dimensione del volume deve contrarsi secondo la regola: $$V_n = \frac{V_1}{\sqrt{n}}$$

#### Parzen Window con Soft Kernel (Gaussian Kernel)

Invece di usare funzioni finestra ipercubiche rigide (che generano stime a scalini), si preferiscono **Kernel morbidi (*soft*)** grazie ai quali ogni pattern $x_i$ contribuisce alla densità in base alla sua effettiva distanza dal punto $x$.

Le funzioni Kernel devono essere funzioni densità (integrate su tutto lo spazio danno 1). Utilizzando la **Multinormale Standard**:
$$\varphi(u) = \frac{1}{(2\pi)^{d/2}} e^{-\frac{u^t u}{2}}$$

**Vantaggio**: Ogni punto del Training Set "irradia" una collinetta di probabilità sfumata. Le superfici decisionali risultanti diventano molto più **regolari, fluide e smussate (*smoothed*)**.

![[Pasted image 20261009112955.png|418]]
#### Esempio Pratico: Maschi / Femmine con Parzen Window

Valutando il punto $x = [57, 168]^T$ con $P(w_1) = 8/18$ e $P(w_2) = 10/18$:

- **Kernel Ipercubico ($h=10$)**:$$p(x|w_1) = 0.0038, \quad p(x|w_2) = 0.0040$$$$P(w_1|x) \approx 43\%, \quad P(w_2|x) \approx 57\% \implies \text{Femmina } (w_2)$$
  *Produce regioni "a gradini" e superfici squadrate.*

- **Kernel Gaussiano ($h=3$)**:$$p(x|w_1) = 0.0024, \quad p(x|w_2) = 0.0041$$$$P(w_1|x) \approx 32\%, \quad P(w_2|x) \approx 68\% \implies \text{Femmina } (w_2)$$
  *Produce superfici decisionali morbide e sfumature continue.*

---
## Classificatori Nearest Neighbor (NN e k-NN)

### Nearest Neighbor (1-NN) e Tassellazione di Voronoi
### k-Nearest Neighbor (k-NN) e Confidenza
### Complessità Computazionale, Editing e Condensing

## Metriche di Distanza e Normalizzazione
### Metriche e Spazi di Variazione (Minkowski, Euclidea)
### Tecniche di Normalizzazione (Min-Max, Standardization, Whitening)
### Metric Learning (LDA)
### Similarità e Distanza Coseno