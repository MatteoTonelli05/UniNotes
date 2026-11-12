# Riduzione Dimensionalità

> [!abstract] Obiettivo dei metodi di **riduzione di dimensionalità** 
è eseguire un **mapping** dallo spazio iniziale $\mathbb{R}^d$ a uno spazio di dimensione inferiore $\mathbb{R}^k$, con $k<d$.

## Definizioni

> [!warning] Ridurre la dimensionalità **non** significa mantenere alcune "dimensioni" e cancellarne altre, ma **combinare** le dimensioni in modo opportuno. 
> (Tenere solo alcune colonne sarebbe _feature selection_.)

> [!fail] Problemi ricorrenti risoldi dalla *Riduzione della Dimensionalità*
> - Dati **ridondanti** (informazioni correlate) e **rumorosi**
> - **Curse of dimensionality**: in spazi ad elevata dimensionalità i pattern sono molto **sparsi** e servono ingenti moli di dati per l'addestramento.
## PCA (Principal Component Analysis)

> [!abstract] Trasformazione **lineare** e **non supervisionata**, detta anche **Karhunen-Loève (KL) transform**. l'obiettivo è **preservare al massimo l'informazione** dei pattern.

> [!hint] Ricorda
> **Trasformazioni non supervisionate (Unsupervised):** Modificano le feature ($X$) guardando esclusivamente la loro distribuzione interna, ignorando completamente il target ($y$). Servono a pulire, normalizzare o ridurre la dimensionalità dei dati senza alcun riferimento a ciò che il modello dovrà predire.
>
>**Trasformazioni supervisionate (Supervised):** Utilizzano attivamente i valori della variabile target ($y$) per guidare e ottimizzare la modifica delle feature ($X$). L'obiettivo è trasformare i dati in modo da massimizzare la separabilità delle classi o la correlazione con il risultato da predire.
### Definizione

> [!quote] La PCA trova le **direzioni in cui i dati variano di più** e tiene solo quelle.

Dato un training set $TS=\{\mathbf{x}_i\in\mathbb{R}^d,\ i=1\dots n\}$:

$$\bar{\mathbf{x}}=\frac{1}{n}\sum_{i=1}^{n}\mathbf{x}_i \qquad \boldsymbol{\Sigma}=\frac{1}{n-1}\sum_{i=1}^{n}(\mathbf{x}_i-\bar{\mathbf{x}})(\mathbf{x}_i-\bar{\mathbf{x}})^t$$

- $\bar{\mathbf{x}}\in\mathbb{R}^d$ $\rightarrow$ vettore medio, ovvero il baricentro della nuvola di dati
- $\boldsymbol{\Sigma}\in\mathbb{R}^{d\times d}$ $\rightarrow$ matrice di covarianza, approssima la forma della nuvola a partire dal centro

> [!hint] Cosa c'è dentro la matrice di covarianza?
>**Diagonale** $\Sigma_{jj}$: la **varianza** della feature $j$, cioè quanto si “sparpaglia” attorno alla sua media.
>
>**Fuori diagonale** $\Sigma_{jk}$: la **covarianza** tra le feature $j$ e $k$, cioè se tendono a muoversi insieme.
 >   - $>0$: quando una cresce, tende a crescere anche l’altra (es. altezza e peso).
 >   - $<0$: quando una cresce, l’altra tende a scendere.
 >   - $\approx0$: nessuna relazione lineare.
 >
 > > [!important] Nota 
 > > - gli **autovettori** di $\boldsymbol{\Sigma}$ sono gli **assi naturali** della nuvola (le direzioni principali);
 > > - gli **autovalori** $\lambda$ sono le **varianze lungo quegli assi**: più è grande $\lambda$, più la nuvola è **lunga** in quella direzione.

> [!attention] **Il problema** 
> Le feature originali (altezza, peso...) sono gli assi del foglio, ma la nuvola può essere **inclinata** rispetto a questi assi. Gli assi "naturali" della nuvola sono altri.

Per un dato $k$ (con $0<k<d$ e $k<n$), lo spazio PCA $(S_{\bar{\mathbf{x}},\Phi_k})$ è definito univocamente dal **vettore medio** e dalla **matrice di proiezione** $\Phi_k\in\mathbb{R}^{d\times k}$, le cui colonne sono gli **autovettori di $\boldsymbol{\Sigma}$** relativi ai $k$ **autovalori PIÙ GRANDI**:
$$\Phi_k=[\boldsymbol{\varphi}_{i_1},\boldsymbol{\varphi}_{i_2},\dots,\boldsymbol{\varphi}_{i_k}], \qquad \lambda_{i_1}\ge\lambda_{i_2}\ge\dots\ge\lambda_{i_k}\ge\dots\ge\lambda_{i_d}$$
- $\boldsymbol{\varphi}_{i_1}$ indica la direzione di **maggior varianza** nel training set.
- I primi $k$ autovettori si chiamano **componenti principali** (da qui il nome _Principal Component Analysis_).

![[Pasted image 20261112101528.png]]
> [!hint] Intuizione su autovettori e autovalori di $\boldsymbol{\Sigma}$ Immagina la nuvola di punti come un'**ellisse**:
> 
> - gli **autovettori** sono gli **assi** dell'ellisse (le direzioni principali)
> - gli **autovalori** sono le **varianze** lungo quegli assi (più è grande, più l'ellisse è "lunga" lì)
> 
> Tenere i $k$ assi con varianza maggiore significa tenere le direzioni dove i dati "si muovono" di più, scartando quelle quasi piatte.
>
>**RICORDA:** stiamo parlando di una riduzione da $d$ dimensioni a $k$
### Proiezione ($\to$) e retroproiezione ($\leftarrow$)

**Proiezione** ($\to$) da $\mathbb{R}^d$ a $\mathbb{R}^k$: una volta determinato lo spazio PCA, la proiezione di un pattern su tale spazio è semplicemente la proiezione geometrica di un vettore sull’iperpiano che definisce lo spazio.
$$\mathbf{y}=\overrightarrow{PCA}(\mathbf{x},S_{\bar{\mathbf{x}},\Phi_k})=\Phi_k^t(\mathbf{x}-\bar{\mathbf{x}})$$

**Retroproiezione** ($\leftarrow$) da $\mathbb{R}^k$ a $\mathbb{R}^d$:
$$\mathbf{x}'=\overleftarrow{PCA}(\mathbf{y},S_{\bar{\mathbf{x}},\Phi_k})=\Phi_k\mathbf{y}+\bar{\mathbf{x}}$$

> [!attention] La retroproiezione non sposta il vettore
> Questa trasformazione non sposta spazialmente il vettore, che giace ancora sullo spazio PCA, ma opera un cambiamento di coordinate che ne permette la codifica in termini delle 𝑑 componenti dello spazio originale.

> [!hint] In pratica
> 
> - $\mathbf{y}$ = versione **compressa** di $\mathbf{x}$ ($k$ numeri)
> - $\mathbf{x}'$ = versione **ricostruita** (approssimata)
> - $\mathbf{x}-\mathbf{x}'$ = informazione **persa** (errore di ricostruzione)
### Scelta di $k$

- A volte è **obbligata**, ad esempio per la visualizzazione 2D o 3D.
- Se invece si vuole scartare informazione inutile e dati correlati **mantenendo gran parte del contenuto informativo**: si fissa una percentuale $t$ da preservare (es. $t=95$) e si prende il **minimo $k$** tale che la somma dei $k$ autovalori più grandi, rispetto alla somma di tutti, sia $\ge t$.

$$k=\arg\min_{z}\left\{z \;:\; \frac{\sum_{i=1}^{z}\lambda_{i}}{\sum_{i=1}^{d}\lambda_{i}} \geq \theta\right\}\qquad(\text{autovalori in ordine decrescente})$$

> [!hint] Gli autovalori sono varianze
>  il rapporto è la **varianza conservata** rispetto alla varianza totale. 
>  > [!example] Esempio:
>  > Supponi che gli autovalori siano **60, 30, 8, ...** e che la somma di tutti sia **100** (i puntini sono gli altri autovalori, piccolissimi: $100-60-30-8=2$ in totale).
>  > Con $k=1$ e $k=2$ non arrivo al 95%. Con $k=3$ sì, quindi **$k=3$** (il minimo che funziona).
### Codifica di un'immagine (eigenfaces)

Un'immagine di volto è un vettore $\mathbf{x}\in\mathbb{R}^{16500}$ (un valore per pixel), le **eigenfaces** (nome usato nel riconoscimento del volto) sono gli 8 componenti principali.

> [!hint] Ogni volto è approssimato come **volto medio + combinazione pesata di eigenfaces**. I 15 numeri di $\mathbf{y}$ sono i pesi.

![[Pasted image 20261112113509.png|351]]
_la retro-proiezione ricrea un volto sfocato_
### Calcolo PCA in pratica

> [!fail] Per $d$ elevato (immagini, audio...) la matrice di covarianza è enorme: per $d=16500$, $\boldsymbol{\Sigma}\in\mathbb{R}^{16500\times16500}$, oltre **272 milioni di valori**.

Se $n\ll d$ conviene calcolare la matrice di proiezione (primi $k$ autovettori) con la **DECOMPOSIZIONE SVD** della matrice rettangolare dei pattern centrati $X\in\mathbb{R}^{d\times n}$, **senza passare per $\boldsymbol{\Sigma}$**:
![[Pasted image 20261112121403.png]]
Per $d>n$: $\mathbf{X}=\mathbf{U}\boldsymbol{\Gamma}\mathbf{V}^t$ con

- $\mathbf{U}\in\mathbb{R}^{d\times n}$ ortonormale
- $\mathbf{V}\in\mathbb{R}^{n\times n}$ ortonormale
- $\boldsymbol{\Gamma}\in\mathbb{R}^{n\times n}$ diagonale

$$\boldsymbol{\Sigma}=\frac{1}{n}\mathbf{X}\mathbf{X}^t=\frac{1}{n}\mathbf{U}\boldsymbol{\Gamma}\mathbf{V}^t\mathbf{V}\boldsymbol{\Gamma}\mathbf{U}^t=\frac{1}{n}\mathbf{U}\boldsymbol{\Gamma}^2\mathbf{U}^t$$

Questa è la **decomposizione spettrale** della matrice quadrata $\boldsymbol{\Sigma}$. Quindi:

- **autovettori** di $\boldsymbol{\Sigma}$ $\rightarrow$ colonne di $\mathbf{U}$ (**vettori singolari sinistri** di $\mathbf{X}$)
- **autovalori** di $\boldsymbol{\Sigma}$ $\rightarrow$ elementi diagonali di $\boldsymbol{\Gamma}^2$ (**valori singolari al quadrato** di $\mathbf{X}$), a meno del fattore $1/n$

> [!note] Il passaggio chiave è $\mathbf{V}^t\mathbf{V}=\mathbf{I}$. Il fattore $1/n$ (o $1/(n-1)$) scala solo gli autovalori e **non cambia gli autovettori**, quindi non cambia lo spazio PCA.

### PCA Whitening

> [!abstract] Tecnica di **pre-normalizzazione** dei dati.

1. **Rimuove le correlazioni** tra le dimensioni: ruota la nuvola di punti allineando gli assi principali (autovettori) agli **assi cartesiani**.
![[Pasted image 20261112121514.png]]
2. **Sfericizza** l'ellissoide: uniforma a **1** le varianze (gli autovalori) lungo tutti gli assi.
![[Pasted image 20261112121531.png]]

Dopo aver proiettato i pattern sullo spazio PCA (primi $k$ autovettori) basta **dividere ogni dimensione per $\sqrt{\lambda_i}$** (la deviazione standard):

$$y_i^{white}=\frac{y_i}{\sqrt{\lambda_i}}$$

> [!abstract] La matrice di covarianza dei dati normalizzati è l'**identità**.

> [!question] Perché serve? 
> Molti algoritmi (ad esempio quelli basati su distanze euclidee come K-means) funzionano meglio con feature **scorrelate** e sulla **stessa scala**. Il whitening ruota (decorrela) e ridimensiona (normalizza).

## LDA (Linear Discriminant Analysis)

> [!fail] La PCA cerca la direzione dove i dati **variano di più**, ma **ignora a quale classe appartiene ogni punto**. A volte la direzione di massima varianza è proprio quella _inutile_ per distinguere le classi.

> [!abstract] Riduzione di dimensionalità **lineare** e **supervisionata** il cui obiettivo è **massimizzare la separazione tra le classi** (che nel TS sono etichettate). La soluzione ottimale per discriminare può essere molto diversa dalla soluzione PCA.

![[Pasted image 20261112122126.png]]
### Matrici di scattering

Una **matrice di scatter** si calcola come una matrice di covarianza **senza normalizzare** per il numero di pattern ("sparpagliamento"). Misura lo “sparpagliamento” in valore assoluto.
Ne servono due, una per ogni obiettivo:

- **Within-class** $\mathbf{S}_w$ $\rightarrow$ come i vettori sono sparpagliati rispetto al **centro della propria classe**. Si vuole **piccola** (classi compatte).
- **Between-class** $\mathbf{S}_b$ $\rightarrow$ come i **centri delle classi** sono sparpagliati rispetto al **centro generale** della distribuzione (quanto le classi sono lontane tra loro). Si vuole **grande**.

### Calcolo

Dato un training set di $n$ pattern $(\mathbf{x}_1,y_1)\dots(\mathbf{x}_n,y_n)$ con $\mathbf{x}_i\in\mathbb{R}^d$ e $y_i\in[1\dots s]$ (etichette delle $s$ classi), siano $n_i$ e $\bar{\mathbf{x}}_i$ numero di pattern e vettore medio della classe $i$.

##### **Within-class**

$$\mathbf{S}_w=\sum_{i=1}^{s}\mathbf{S}_i,\qquad \mathbf{S}_i=\sum_{\mathbf{x}_j,|,y_j=i}(\mathbf{x}_j-\bar{\mathbf{x}}_i)(\mathbf{x}_j-\bar{\mathbf{x}}_i)^t$$

_(la somma interna è sui pattern della classe $i$)_

##### **Between-class**

$$\mathbf{S}_b=\sum_{i=1}^{s}n_i\cdot(\bar{\mathbf{x}}_i-\bar{\mathbf{x}}_0)(\bar{\mathbf{x}}_i-\bar{\mathbf{x}}_0)^t,\qquad \bar{\mathbf{x}}_0=\frac{1}{n}\sum_{i=1}^{s}n_i\cdot\bar{\mathbf{x}}_i$$

($\bar{\mathbf{x}}_0$ = **media globale**)

### Criterio di ottimizzazione

Il più usato è la massimizzazione di

$$J_1=\text{tr}(\mathbf{S}_w^{-1}\mathbf{S}_b)=\sum_{i=1}^{d}\lambda_i$$

dove $\text{tr}$ è la **traccia** (somma degli autovalori) della matrice.

> [!hint] Intuizione 
> Il criterio massimizza lo scattering **tra** le classi ($\mathbf{S}_b$) e minimizza al contempo (con la matrice inversa $\mathbf{S}_w^{-1}$) quello **all'interno** di ogni classe: direzioni dove le classi sono lontane tra loro e ben raccolte.

Si dimostra che per massimizzare $J_1$ lo spazio LDA è definito (**analogia con PCA**) dagli **autovettori** relativi ai primi $k$ autovalori della matrice $\mathbf{S}_w^{-1}\mathbf{S}_b$, con $k<n$, $k<s$, $k<d$.

> [!warning] Valore massimo di $k$ = $s-1$ Con $s$ classi si ottengono **al massimo $s-1$ dimensioni**. Con **due classi** si ha quindi **una sola direzione** (come nell'esempio PCA vs LDA).

> [!hint] Perché $s-1$? $\mathbf{S}_b$ è costruita con $s$ vettori $(\bar{\mathbf{x}}_i-\bar{\mathbf{x}}_0)$ che **non sono indipendenti**: la loro somma pesata con $n_i$ vale zero (perché $\bar{\mathbf{x}}_0$ è la media pesata). Il rango di $\mathbf{S}_b$ è quindi al massimo $s-1$ e solo $s-1$ autovalori sono diversi da zero.

> [!tip] Extra: $\mathbf{S}_w$ deve essere **invertibile**. Con $d$ grande e pochi pattern ($n\le d$) può non esserlo: in quel caso si applica prima una **PCA** per ridurre $d$ e poi la LDA.

## Esempio PCA vs LDA

Riduzione $d=2 \to k=1$ su due classi (rosse e blu). 
Entrambi sono mapping lineari $\mathbb{R}^2\to\mathbb{R}^1$, ma la retta è diversa:

- **Segmento nero (PCA)** $\rightarrow$ la retta su cui, proiettando i pattern (indipendentemente dalla classe), si **conserva al massimo l'informazione**.
- **Segmento verde (LDA)** $\rightarrow$ la retta su cui, proiettando, si **distinguono al meglio le due classi**.
![[Pasted image 20261109140938.png|420]]

## Riduzione di dimensionalità per visualizzazione (2D o 3D)

- Anche **PCA** (con $k=2$ o $k=3$) può essere usata, ma dati con distribuzioni spiccatamente **non multinormali** non possono essere ridotti efficacemente con un mapping **lineare**.
- **t-SNE** e **UMAP** rappresentano lo **stato dell'arte** per la visualizzazione 2D o 3D di dati multidimensionali.

### t-SNE (t-distributed Stochastic Neighbor Embedding)

- Tecnica **non lineare** e **non supervisionata**, introdotta nel **2008** da **van der Maaten e Hinton**.
- **Esempio**: visualizzazione 2D di **MNIST** (cifre scritte a mano). Le 10 cifre appaiono come "isole" ben separate. I colori servono solo per colorare i punti, le etichette non sono usate per costruire la mappa.

> [!tip] Idea di fondo (extra, non nelle slide) Nello spazio originale si costruisce una distribuzione di probabilità che dice **quanto due punti sono vicini**. Poi si cerca una disposizione in 2D/3D che riproduca le stesse relazioni di vicinanza, usando una **distribuzione t di Student** nello spazio ridotto (da qui la "t") e minimizzando la divergenza di Kullback-Leibler tra le due distribuzioni.

**In sklearn** $\rightarrow$ visualizzazione 2D di un dataset con 1000 cani + 1000 gatti (immagini non controllate), con feature **HOG** (_Histogram of Oriented Gradients_) invece dei pixel:

```python
from sklearn.manifold import TSNE

tsne = TSNE(n_components=2, perplexity=30.0, n_iter=1500,
            init='random', random_state=1, verbose=2)

X_embedded_hog = tsne.fit_transform(hog_features_x)

plt.scatter(X_embedded_hog[0:1000, 0], X_embedded_hog[0:1000, 1], c="r")
plt.scatter(X_embedded_hog[1000:2000, 0], X_embedded_hog[1000:2000, 1], c="g")
plt.show()
```

- I due cluster risultano **molto sovrapposti**.
- I classificatori tradizionali (es. SVM) raggiungono circa il **70%**, sfruttando la maggiore densità dei pattern verdi (gatti) in una certa regione.
- Per tarare i parametri (es. **perplexity**) vedi l'articolo _How to Use t-SNE Effectively_ su distill.pub.

> [!hint] Cos'è la perplexity? Grosso modo controlla **quanti vicini** ciascun punto "considera" (tipicamente tra 5 e 50). Valori diversi possono dare mappe molto diverse: conviene **provarne più di uno**.

> [!warning] Cautele nella lettura di una mappa t-SNE
> 
> - Serve a **visualizzare**, non a produrre feature per un classificatore.
> - **Dimensioni** dei cluster e **distanze tra cluster** spesso **non hanno significato**.
> - Il risultato dipende da inizializzazione casuale e parametri.

### UMAP

> [!abstract] **UMAP** (_Uniform Manifold Approximation and Projection for Dimension Reduction_, McInnes e Healy, 2018) è una tecnica **non lineare** e **non supervisionata**, simile a t-SNE, utile per la visualizzazione 2D o 3D.

**In comune con t-SNE**

- preserva le **distanze locali**
- individua **cluster** di dati localmente connessi

**Differenza**

- UMAP preserva meglio anche la **struttura globale** dei cluster, cioè le **distanze inter-cluster**.
- t-SNE può visualizzare cluster vicini o lontani **indipendentemente** dalla loro similarità nello spazio originale.

---

> [!tip] Come orientarsi
> - **Supervisionata?** PCA no, LDA sì.
> - **Lineare?** PCA, LDA e ICA sì; Kernel PCA, LLE, t-SNE e UMAP no.
> - **Scopo?** PCA = rappresentare; LDA = separare; t-SNE/UMAP = visualizzare.

## Altre tecniche di interesse

- **ICA** (Independent Component Analysis) $\rightarrow$ trasformazione lineare che proietta su componenti **statisticamente indipendenti**, anche **non ortogonali** (come invece in PCA e LDA). Considera statistiche **oltre il secondo ordine**, PCA si limita al secondo.

- **Kernel PCA** $\rightarrow$ simile a PCA ma con mapping **non lineare**. Usa un "trucco" simile a quello che porta dalla SVM lineare alla SVM non lineare.

- **LLE** (Local Linear Embedding) $\rightarrow$ non lineare; invece di un mapping **globale** considera le relazioni tra gruppi di pattern **vicini**.