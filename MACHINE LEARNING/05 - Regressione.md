# Regressione

> [!abstract] Nella **regressione** i valori $y_i$ (classi di appartenenza) sono numerici e continui in $R$, e l'obbiettivo del training è l'apprendimento di una funzione $f(\mathbf{x}) \to y$ con $y \in \mathbb{R}$ continuo. 

> [!question] Cosa cambia dalla classificazione?
> L'associazione pattern $x_i$ $\rightarrow$ etichetta $y_i$ è identica, ma l'obiettivo del training è l'apprendimento di un **mapping** dallo spazio dei pattern a quello discreto delle etichette
## Simple linear regression

Dato un training set con $n$ pattern $(x_1,y_1)\dots(x_n,y_n)$ si assume
$$y_i = \alpha + \beta \cdot x_i + \varepsilon_i$$
- $\varepsilon_i$ $\rightarrow$ errore di misura (incognito) del pattern $i$
- $\alpha, \beta$ $\rightarrow$ parametri da determinare
![[Pasted image 20261027130813.png|389]]
La soluzione **least square** è la retta $f(x) = \alpha + \beta x$ che minimizza la somma dei quadrati dei residui:

$$\alpha^*, \beta^* = \arg\min_{\alpha,\beta} \sum_{i=1}^{n} (f(x_i) - y_i)^2 = \sum_{i=1}^{n} \varepsilon_i^2$$

Si ottiene uguagliando a zero le derivate parziali rispetto a $\alpha,\beta$ (**equazioni normali**, 2 equazioni in 2 incognite):

$$\beta^* = \frac{\sum_i (x_i-\bar{x})(y_i-\bar{y})}{\sum_i (x_i-\bar{x})^2}, \qquad \alpha^* = \bar{y} - \beta^* \bar{x}$$

dove $\bar{x}, \bar{y}$ sono le medie.

> [!fail] Servono almeno due punti con $x_i$ diverse, altrimenti il denominatore si annulla.

![[Pasted image 20261027134155.png|538]]

## Multiple linear regression

> [!hint] Nella simple linear regression hai **una** variabile in ingresso $x$ e cerchi una retta. Nella multiple ne hai $d$ e cerchi un **iperpiano**. Il modello è
>
$$\hat{y} = \beta_1 x_1 + \beta_2 x_2 + \dots + \beta_d x_d + \beta_{d+1}$$

Con $d=1$ è una retta, con $d=2$ un piano nello spazio $(x_1, x_2, y)$, con $d>2$ non si disegna più ma la logica resta identica. Il modello è lineare **nei parametri** $\beta_j$, ed è questo che conta.
![[Pasted image 20261027140737.png|415]]


- $\mathbf{X}$ è la matrice $n \times (d+1)$ con i pattern sulle righe e **una colonna di tutti 1 a destra** (serve per il termine noto).
- $\mathbf{y}$ = vettore delle $y_i$ (uno scalare per pattern).
- $\boldsymbol{\beta}$ = vettore dei $d+1$ parametri (l'ultimo è il termine noto).
![[Pasted image 20261027141957.png]]
Le relazioni diventano
$$y_i = \sum_{j=1}^{d+1} x_{ij},\beta_j \quad \Longleftrightarrow \quad \mathbf{y} = \mathbf{X}\boldsymbol{\beta}$$
Per $n > d+1$ il sistema è **sovradeterminato**.

Funzione obiettivo (least square):
$$| \mathbf{y} - \mathbf{X}\boldsymbol{\beta} |^2$$

Equazioni normali e soluzione:
$$(\mathbf{X}^t \mathbf{X}),\boldsymbol{\beta}^* = \mathbf{X}^t \mathbf{y} \quad\Rightarrow\quad \boldsymbol{\beta}^* = (\mathbf{X}^t\mathbf{X})^{-1}\mathbf{X}^t\mathbf{y}$$

> [!note] Casi particolari
> 
> - $n = d+1$: $\mathbf{X}$ è quadrata e l'iperpiano passa per tutti i punti (**interpolazione**).
> - $n > d+1$: più equazioni che incognite, l'iperpiano **approssima** i punti.
> - $\mathbf{X}^t\mathbf{X}$ deve essere a **rango massimo** per essere invertibile: problemi con punti collineari (colonne di $\mathbf{X}$ linearmente dipendenti).
> - $\mathbf{X}^t\mathbf{X}$ è spesso **mal condizionata**, quindi l'inversione può essere numericamente instabile.
### Soluzione stabile con SVD

Si decompone $\mathbf{X} = \mathbf{U}\boldsymbol{\Gamma}\mathbf{V}^t$ dove:

- $\mathbf{U}$ ortogonale $n \times n$
- $\mathbf{V}$ ortogonale $(d+1)\times(d+1)$
- $\boldsymbol{\Gamma}$ diagonale rettangolare $n \times (d+1)$ (elementi non nulli solo con indici di riga e colonna uguali)

Si dimostra che

$$\boldsymbol{\beta}^* = \mathbf{V},\boldsymbol{\Gamma}^{+},\mathbf{U}^t,\mathbf{y}$$

con $\boldsymbol{\Gamma}^+$ la **pseudoinversa** di $\boldsymbol{\Gamma}$: si ottiene invertendo gli elementi non nulli e trasponendo.
## Regressione vs geometrical fitting

> [!question] I minimi quadrati sono un buon metodo per fittare nuvole di punti 2D/3D?
> No, perchè la regressione lineare (least square) minimizza le distanze verticali dall'iperpiano e non le distanze euclidee (ottenibile invece attraverso **PCA**)

![[Pasted image 20261027143702.png|462]]
## Accuratezza di predizione

|Metrica|Formula|Note|
|---|---|---|
|**MSE**|$\frac{1}{N}\sum (pred_i - true_i)^2$|È il criterio implicito nel least square|
|**RMSE**|$\sqrt{MSE}$|Indicatore usato finora|
|**MAE**|$\frac{1}{N}\sum \lvert pred_i - true_i \rvert$|Più robusta agli outlier, mantiene l'unità di misura|
|**MAPE**|$\frac{1}{N}\sum \left\lvert \frac{pred_i - true_i}{true_i} \right\rvert$|Variante del MAE: penalità in base allo scostamento percentuale|
|**R²**|coefficiente di determinazione|Valori tra 0 e 1, misura quanto il modello lineare spiega la varianza della dipendente|

> [!warning] MAE, MAPE e R² **non sono direttamente utilizzabili come loss** nel modello lineare least square. R² **non è valido per modelli non lineari**.

> [!hint] Ricorda
> La **loss** (funzione di perdita) è un numero che misura **quanto sbaglia il modello** su un dato.
## Variabile indipendente non lineare

> [!hint] Si può continuare a usare la regressione lineare per approssimare **curve e superfici**
> Puoi applicare qualsiasi trasformazione alle tue variabili d'ingresso $x$, finché la formula finale ha la forma:
>
>$$y = w_0 + w_1 \cdot f_1(x) + w_2 \cdot f_2(x) + \dots$$

![[Pasted image 20261027181115.png|502]]_$\qquad \qquad$ nell'esempio sopra possiamo dire che $x^2$ è $f_1(x)$, $x$ è $f_2(x)$ ecc._

### Regressione non lineare con dipendenza nota

> [!fail] Se il modello è **non lineare nei parametri** non esiste soluzione least square in forma chiusa.

Se la dipendenza è nota si usa **ottimizzazione numerica** iterativa:
- **Gradient Descent**: metodo del primo ordine, passi in direzione contraria al gradiente.
- **Gauss-Newton**: il più noto e usato, metodo del secondo ordine (richiede una stima dell'Hessiana).

### Regressori non lineari (dipendenza sconosciuta)

Quando la dipendenza non è nota si prova e si valida prima il **modello lineare (baseline)**, poi modelli non lineari via via più complessi. 

> [!question] Perchè si dovrebbero usare modelli non lineari?
 > - Maggiore **flessibilità** (più gradi di libertà).
 > - Controllo della **complessità** della soluzione (gradi di libertà, regolarizzazione, ecc.).
> -  Possibilità di usare **loss alternative** a MSE (MAE, MAPE, ecc.).

#### Support Vector Regressor (SVR)

> [!abstract] Rispetto alla classificazione, dove si volevano spingere i pattern delle due classi oltre al margine, qui si inverte la logica: vogliamo che nella fascia del margine ricada il maggior numero di pattern del training set.

![[Pasted image 20261027182255.png]]
**IPERPARAMETRI UTILI**
- La larghezza della fascia è controllata da $\varepsilon$ (più piccolo → fascia più stretta).
- L'iperparametro $C$ e i regolarizzatori del kernel (in caso di approssimazione non lineare) controllano l'**overfitting** e mantengono il regressore _smoothed_.

#### Random Forest Regressor


![[Pasted image 20261027182753.png]]
- Stessa logica della foresta (**doppio bagging** su feature e pattern), ma con alberi **CART usati come regressori**.
- Ogni nodo (foglie comprese) predice la **media delle $y$** dei pattern che gli appartengono.
- La regola di split (coppia feature, soglia) **minimizza l'MSE** delle $y$ nei due nodi figli.
- Risultato: approssimazione **costante a tratti**, che può essere discontinua con salti repentini.
- Principale regolarizzante: **massima profondità** degli alberi.

#### Gradient Boosting Regressor Trees

- Tecnica di **boosting**: aggiunge iterativamente nuovi regression tree addestrati sui **residuali** (differenza tra $y$ e le predizioni dei predecessori).
- La predizione finale è la **somma dei contributi** dei singoli alberi: $h(x) = h_1(x) + h_2(x) + h_3(x) + \dots$
- Con loss MSE i residuali coincidono con i **gradienti** (da qui il nome). La formulazione generale, matematicamente più complessa, permette anche altre loss.
- Nel gradient boosting per la **classificazione** i modelli intermedi sono comunque regressori; solo alla fine una funzione mappa il valore continuo in una classe.

> [!tip] XGBoost
> 
> - Implementazione ottimizzata con interfaccia `fit/predict` standard di scikit-learn, quindi facile da aggiungere al toolkit.
> - Training fino a **10 volte più veloce** della versione classica.
> - Include un **termine regolarizzante sulla loss** per controllare meglio l'overfitting.
> - In molte challenge Kaggle su **dati tabulari** ha ottenuto le prestazioni migliori.
## Quantile regression

- Il modello lineare ai minimi quadrati stima la **media condizionata**: errori positivi e negativi pesano uguale.
- Se si vuole **pesare diversamente** sovrastime e sottostime (es. una sottostima ha conseguenze più gravi) si usa la **regressione ai quantili/percentili**.
    - Percentile **50%** → si passa da media a **mediana** (metà dei punti sopra la retta, metà sotto).
    - Percentile **p** → la retta lascia il $p%$ dei punti sotto e il $(1-p)%$ sopra.
- Si usa una **loss MAE con penalità diversa** per predizioni in eccesso e in difetto.
- Con modello lineare serve ottimizzazione numerica; esiste però un algoritmo iterativo che richiama la **regressione lineare pesata** (Quantile Linear Regression, estensione di scikit-learn).
- Si può fare anche con **modelli non lineari** (alberi, deep learning).

## Riepilogo

> [!summary] Problemi di piccole-medie dimensioni
> 
> 1. **Lineare nei parametri** con least square (fattorizzazione SVD): approccio più semplice, è la **baseline**.
> 2. **Non lineare nella variabile indipendente ma lineare nei parametri**: ottima seconda scelta, unisce semplicità e flessibilità.
> 3. **Modelli più potenti** → SVR, Random Forest, Gradient Boosting. I metodi ad **alberi** sono la prima scelta per **dati tabellari eterogenei**, mentre SVR modella meglio i **fenomeni fisici continui**.
> 4. **Dipendenza nota ma non lineare nei parametri** → ottimizzazione numerica con **Gauss-Newton**, prima scelta.

> [!summary] Problemi di grandi dimensioni
> 
> - Specie su dati non strutturati, **Gradient Descent + Reti Neurali** è spesso preferibile per trattabilità numerica e complessità computazionale.
> - Esempio in deep learning: stima della posizione di un oggetto in un'immagine da milioni di esempi (**Faster R-CNN**). La variabile indipendente è l'immagine (pixel), la dipendente è la **bounding box**, cioè un vettore → **multivariate multiple regression**.