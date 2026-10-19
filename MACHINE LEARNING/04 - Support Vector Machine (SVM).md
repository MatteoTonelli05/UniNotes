## Introduzione e Principi Base

> [!abstract] **SVM** nasce come classificatore binario (2 classi), estendibile a più classi.

> [!hint] Date **due classi** di pattern multidimensionali linearmente separabili, tra tutti i possibili **iperpiani** di separazione, SVM determina quello in grado di separare le classi con il maggior margine possibili
> ![[Pasted image 20261015163458.png]]
>

> [!question] Perchè massimizzare il margine?
> Un margine più ampio garantisce che anche i punti futuri (del _test set_) vicini ai confini vengano classificati correttamente con maggiore stabilità.
>
> Il problema si formula come un'**ottimizzazione convessa** (_convex optimization_), il che garantisce l'esistenza di un **unico minimo globale**

## SVM Lineare: Pattern Separabili

### Definizione del problema

> [!abstract] Definizione del problema
> Date due classi di pattern (**lineramente separabili**) e un training set $TS$ contenente $m$ campioni $(x_1,y_1)...(x_n,y_n)$, dove $x_i\in R^d$ sono i pattern multidimensionali e $y_i\in \{+1,-1\}$ le etichette delle due classi.
>
>Un generico iperpiano di separazione è descritto dalla funzione $$D(x) = w \cdot x + b$$
>Dove:
>- $w$ è il vettore normale (perpendicolare) all'iperpiano.
>- $\frac{b}{\Vert{}w\Vert{}}$ rappresenta la distanza dell'iperpiano dall'origine.
>
> ![[Pasted image 20261016131748.png|311]]

Definiamo così la distanza $r$ di un generico punto $x$ dall'iperpiano $$r = \frac{D(x)}{\Vert{}w\Vert{}}$$
Per garantire che i punti delle due classi ($y_i = +1$ e $y_i = -1$) siano separati correttamente e stiano all'esterno del margine (con distanza minima di $1/\Vert{}w\Vert{}$), si impongono le condizioni:
- $w \cdot x_i + b \ge +1$ se $y_i = +1$
- $w \cdot x_i + b \le -1$ se $y_i = -1$
### Concetto intuitivo di Margine

> [!question] Che cos'è $D(x)$?
> $D(x) = w \cdot x + b$ è la **funzione di decisione** (o funzione distanza) dell'SVM:
>
>- **$D(x) = 0$** rappresenta l'**iperpiano di separazione ottimo** (il confine di decisione vero e proprio). Tutti i punti che si trovano esattamente su questo piano hanno $D(x) = 0$.
>- Se $D(x) > 0$, il punto $x$ si trova dal lato della classe positiva ($y_i = +1$).  
>- Se $D(x) < 0$, il punto $x$ si trova dal lato della classe negativa ($y_i = -1$).

> [!important] Non ci interessa solo separare le due classi, ma vogliamo farlo lasciando una **zona cuscinetto (il margine)** attorno all'iperpiano di separazione $D(x) = 0$.
> Definiamo quindi $D(x) = +1$ è la retta/piano che delimiterà il confine del margine per la classe **positiva**.

![[Pasted image 20261019095518.png|316]]
La minima distanza tra l’iperpiano di separazione e un pattern del training set è detta **MARGINE** $$\tau=2/||w|| \qquad \text{(margine totale)}$$
> [!abstract] L’iperpiano ottimo secondo **SVM** è quello soddisfa i vincoli di separazione dei pattern e massimizza il margine $\tau$ (o alternativamente **minimizza** il suo inverso):
$$\min_{w,b} \frac{1}{2} \Vert{}w\Vert{}^2 \quad \text{sotto il vincolo } y_i(w \cdot x_i + b) - 1 \ge 0$$

I punti del Training Set che giacciono _esattamente_ sul margine ($D(x) = +1$ o $D(x) = -1$) prendono il nome di **Support Vectors**.
### Formulazione Duale e Lagrangiana


### Vantaggi e Scalabilità

## SVM Lineare: Pattern non Separabili

### Variabili di Slack ($\xi$)

### Bilanciamento del Margine e Parametro C

## SVM Non Lineare

### Mapping Non Lineare

### Kernel Trick e Teorema di Mercer

### Kernel Polinomiale

### Kernel RBF (Radial Basis Function)

### Kernel Sigmoid

## SVM: Estensione Multiclasse

### One-Against-All

### One-Against-One

## SVM in Pratica

### Implementazione e Librerie (LIBSVM, LIBLINEAR, Scikit-Learn)

### Guida alla Scelta (Lineare vs Non Lineare)

# Multi-Classificatori (Ensemble Methods)

## Principi Generali

### Indipendenza e Diversità tra Classificatori

### Prestazioni Teoriche e Distribuzione Binomiale

## Fusione a Livello di Decisione

### Majority Vote Rule

### Borda Count

## Fusione a Livello di Confidenza

### Metodi Sum, Prod, Max e Min

### Somma Pesata (Avgsum)

# Algoritmi Ensemble Specifici

## Random Forest (Bagging)

### Metodi di Bagging

### Decision Tree Classifiers (CART)

### Impurità: Indice Gini ed Entropia

### Feature Bagging e Iperparametri

### Stima Out-Of-Bag (OOB)

## AdaBoost (Boosting)

### Principi del Boosting (Weak e Strong Classifiers)

### Assegnazione dei Pesi ai Pattern

### Selezione dei Classificatori e Peso Alpha

### Pseudocodice dell'Algoritmo

### Applicazione: Viola & Jones Face Detector

## Gradient Boosting

### Apprendimento sui Residuali di Errore

### Implementazione e XGBoost