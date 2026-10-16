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

Date due classi di pattern (**lineramente separabili**)

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