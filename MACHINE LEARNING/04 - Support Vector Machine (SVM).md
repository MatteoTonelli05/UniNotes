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

### Iperpiano di Separazione e Parametri

### Calcolo del Margine

### Support Vectors

### Formulazione Lagrangiana e Problema Duale

### Condizioni KKT e Programmazione Quadratica

### Vantaggi e Scalabilità

## SVM Lineare: Pattern non Separabili

### Variabili di Slack

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