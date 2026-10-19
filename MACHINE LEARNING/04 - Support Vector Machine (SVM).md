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

#### Support Vectors

> [!hint] I punti del Training Set che giacciono _esattamente_ sul margine ($D(x) = +1$ o $D(x) = -1$) prendono il nome di **Support Vectors**.
>
Mantenendo in memoria solo i Support Vectors possiamo dunque definire completamente la soluzione del problema, individuando il confine mediante i pattern vicini.
### Formulazione Duale e Lagrangiana

> [!fail] Problema
> Noi vogliamo **minimizzare** $\frac{1}{2}\Vert{}w\Vert{}^2$ (cioè minimizzare i pesi per massimizzare il margine).
>
> Ma abbiamo una montagna di **vincoli d'ineguaglianza**: $y_i(w \cdot x_i + b) \ge 1$ per ogni singolo punto $i=1 \dots n$.

> [!hint] Tramite la riformulazione Duale e Lagrangiana trasformiamo i vincoli in un'unica equazione lineare

#### Trasformazione Lagrangiana $\rightarrow$ Duale

Per ogni vincolo (cioè per ogni dato del training set $i=1 \dots n$), introduciamo un numero positivo chiamato $\alpha_i \ge 0$ (moltiplicatore di Lagrange).

Costruiamo la funzione **$Q(w, b, \alpha)$**:
$$Q(w,b,\alpha) = \underbrace{\frac{1}{2} w \cdot w}_{\text{Obiettivo da minimizzare}} - \sum_{i=1}^{n} \alpha_i \underbrace{\Big( y_i [w \cdot x_i + b] - 1 \Big)}_{\text{Vincolo di separazione}}$$

Per minimizzare rispetto a $w$ e $b$, facciamo le derivate parziali di $Q$ e le poniamo uguali a zero (Condizioni di Karush-Kuhn-Tucker - KKT).

Da queste derivate otteniamo due relazioni fondamentali:

1. **$w = \sum_{i=1}^{n} \alpha_i y_i x_i$**: Il vettore normale $w$ è semplicemente una combinazione lineare dei punti di addestramento pesati da $\alpha_i$ e dall'etichetta $y_i$!
    
2. **$\sum_{i=1}^{n} \alpha_i y_i = 0$**: La somma pesata degli $\alpha_i$ per le loro classi deve fare zero.
    

Sostituendo queste due espressioni dentro la funzione di Lagrange $Q(w,b,\alpha)$, **$w$ e $b$ scompaiono completamente** e otteniamo la **Formulazione Duale** $Q(\alpha)$ (Pag. 6):

$$\max_{\alpha} Q(\alpha) = \sum_{i=1}^{n} \alpha_i - \frac{1}{2} \sum_{i=1}^{n} \sum_{j=1}^{n} \alpha_i \alpha_j y_i y_j (x_i \cdot x_j)$$

con vincoli:

$$\sum_{i=1}^{n} y_i \alpha_i = 0 \quad \text{e} \quad \alpha_i \ge 0 \quad \forall i=1 \dots n$$
### Vantaggi e Scalabilità

#### Vantaggi
1. La soluzione ora è definita da un numero ridotto di support vector ($n_{sv}$).
2. L'errore medio sul test set è limitato superiormente dal rapporto $\frac{n_{sv}}{n}$.

#### Scalabilità
Ora il problema scala molto bene rispetto alla dimensionalità dello spazio $d$ grazie ai prodotti scalari. La complessità di training è quadratica rispetto al numero $n$ di pattern.

![[Pasted image 20261019104942.png|513]]

---

## SVM Lineare: Pattern non Separabili

### Variabili di Slack ($\xi$)

>[!fail] Le classi possono non essere linearmente separabili
 
In questo caso si rilassano i vincoli introducendo $n$ variabili di slack positive $\xi_i \ge 0$ :
$$y_i [w \cdot x_i + b] \ge 1 - \xi_i \quad \text{per } i = 1 \dots n$$
- $\xi_i = 0$: pattern separabile e corretto rispetto al margine.
- $\xi_i > 0$: deviazione dal margine / pattern erroneamente classificato.

![[Pasted image 20261019105532.png|325]]
### Bilanciamento del Margine e Parametro C
La nuova funzione obiettivo bilancia l'ampiezza del margine e le violazioni/errori:
$$\min_{\mathbf{w}, b, \xi} \underbrace{\frac{\Vert{}\mathbf{w}\Vert{}^2}{2}}_{\text{Massimizzare il margine}} + \underbrace{C \sum_{i=1}^{n} \xi_i}_{\text{Minimizzare la somma degli errori/scostamenti}}$$

> [!question] Cos'è l'**Iperparametro $C$**?
> $C$ regola l'importanza relativa degli errori di classificazione rispetto all'ampiezza del margine.
> - $C$ elevato: penalizza fortemente gli errori $\Rightarrow$ margine più stretto, meno errori nel TS.
> - $C$ basso: tollera più errori nel TS $\Rightarrow$ margine più ampio, migliore generalizzazione.

Se permettessimo alle variabili di Slack $\xi_i$ di crescere a piacere senza penalità, la SVM farebbe $\xi_i$ enormi per tutti i punti e metterebbe l'iperpiano a caso. Inserendo una penaliltà per gli errori si evita un abuso di slack.

![[Pasted image 20261019110632.png]]
#### Formulazione Duale con Slack Variables:
$$Q(\alpha) = \sum_{i=1}^{n} \alpha_i - \frac{1}{2} \sum_{i, j=1}^{n} \alpha_i \alpha_j y_i y_j (x_i \cdot x_j)$$
con vincoli:
$$\sum_{i=1}^{n} y_i \alpha_i = 0 \quad \text{e} \quad 0 \le \alpha_i \le C \quad \forall i = 1 \dots n$$
L'unica variazione rispetto al caso separabile è il limite superiore $C$ introdotto sui moltiplicatori $\alpha_i$.

---

## SVM Non Lineare

### Mapping Non Lineare e Kernel Trick
Per separare classi complesse in $\mathbb{R}^d$, si applica un mapping non lineare $\Phi$ verso uno spazio a più alta dimensione $\mathbb{R}^m$ ($m > d$):
$$\Phi: \mathbb{R}^d \rightarrow \mathbb{R}^m, \quad \Phi(x) = [g_1(x), g_2(x), \dots, g_m(x)]$$

In $\mathbb{R}^m$ i pattern mappati possono essere separati da un iperpiano lineare. Poiché i pattern nella formulazione duale compaiono unicamente tramite prodotto scalare $\Phi(x_i) \cdot \Phi(x_j)$, si utilizza una **Kernel function $K$**:
$$K(x, x') = \Phi(x) \cdot \Phi(x')$$

> [!hint] In parole semplici
> Nel caso del duale, dei punti nello spazio ci interessa solo il prodotto scalare (ossia una misura di quanto due punti si somigliano tra loro)
>
>**Il kernel**, al posto di spostare i punti nello spazio "gigante" e fare i calcoli nello spazio più grande, **velocizza il calcolo sputando direttamente il prodotto scalare finale.** 

> [!abstract] **Teorema di Mercer**
> Se $K(x, x')$ è continua e simmetrica, garantisce l'esistenza di uno spazio ad alta dimensione in cui $K$ corrisponde al prodotto scalare delle funzioni mappate, permettendo di calcolarlo senza mai esplicitare $\Phi$ o operare direttamente in $\mathbb{R}^m$.

---
### Funzioni Kernel Comuni

1. **Polinomiale** (iperparametro: grado $q$):$$K(x, x') = [(x \cdot x') + 1]^q$$![[Pasted image 20261019113604.png]]
2. **Radial Basis Function (RBF)** (iperparametro ampiezza $\sigma$ o $\gamma$):$$K(x, x') = \exp\left(-\frac{\Vert{}x - x'\Vert{}^2}{2\sigma^2}\right) = \exp\left(-\gamma \Vert{}x - x'\Vert{}^2\right)$$
   *(Lo spazio mappato da RBF ha dimensione infinita $m = \infty$)*.
![[Pasted image 20261019113629.png]]

3. **Sigmoide**:$$K(x, x') = \tanh(\nu (x \cdot x') + a)$$
#### Regola di classificazione non lineare:
$$D(x) = \sum_{i=1}^{n} \alpha_i^* y_i K(x, x_i) + b^*$$
- **$x$**: È il nuovo dato da classificare 
- **$x_i$**: Sono i dati di addestramento che la SVM ha tenuto in memoria. (Support Vector)
- **$y_i$**: È la classe del Support Vector $x_i$ ($+1$ o $-1$).
- **$\alpha_i^*$**: È il "peso" o l'importanza di quel Support Vector. 
- **$K(x, x_i)$**: il kernel, che misura **quanto il nuovo dato $x$ sia "vicino" al Support Vector $x_i$**.
- **$b^*$**: È l'intercetta (o il bias), ovvero una costante di offset che trasla la superficie decisionale per bilanciarla correttamente.

Per classificare il nuovo dato
- Se **$D(x) > 0$** $\rightarrow$ il punto $x$ viene assegnato alla **classe $+1$**.
- Se **$D(x) < 0$** $\rightarrow$ il punto $x$ viene assegnato alla **classe $-1$**.

---

## SVM: Estensione Multiclasse

SVM è nativamente un classificatore binario. Per gestire $s$ classi si applicano le strategie:
### One-Against-All (One-vs-Rest)

Si addestrano $s$ classificatori SVM separati, in cui per ciascuna classe $w_k$, si considerano i suoi elementi come $+1$ e quelli di tutte le altre classi come $-1$, ottenendo la funzione $D_k(x)$

Ogni pattern $𝐱$ viene assegnato alla classe $k^*$ per cui, su ogni classificatore, è massima la confidenza (distanza dalla linea di separazione):  $$k^* = \arg\max_{k=1 \dots s} \{D_k(x)\}$$
### One-Against-One (One-vs-One)
- Si addestrano $\frac{s(s-1)}{2}$ classificatori binari (tutte le coppie di classi).
- Ogni classificatore esprime un voto per la classe vincente.
- Il pattern $x$ viene assegnato alla classe con il maggior numero di voti (Majority Vote).
- Più accurato di One-Against-All ma computazionalmente più oneroso all'aumentare di $s$.

---

## SVM in Pratica

### Implementazione e Librerie
- **LIBSVM**: Implementazione standard. Utilizza internamente One-Against-One per multiclasse. In Scikit-Learn è usata via `sklearn.svm.SVC`.
- **LIBLINEAR**: Ottimizzata per SVM lineare su grandi mole di dati/alte dimensioni (`sklearn.svm.LinearSVC`).

### Guida alla Scelta:
- **Alta dimensionalità ($d \ge 5000$)**: Usare **SVM Lineare** (i pattern sono già sparsi; tarare solo $C$).
- **Bassa dimensionalità ($d \le 20$)**: Usare **SVM Non Lineare con Kernel RBF** (tarare $C$ e $\sigma / \gamma$).
- **Media dimensionalità ($d \approx 200$)**: Provare entrambe le opzioni tramite Validation Set o Cross-Validation.

> [!example]
> ![[Pasted image 20261019125602.png|402]]![[Pasted image 20261019125626.png|404]] ![[Pasted image 20261019132902.png|405]]

## Multi-Classificatori (Ensemble Methods)

### Principi Generali

> [!hint] I **Multi-Classificatori** (chiamati in inglese **Ensemble Methods**) 
> Questa tecnica, invece di addestrare un unico "super-modello" e sperare che non sbagli, mette insieme **più classificatori** e si fondono le loro decisioni.
#### Strategie per ottenere la diversità

> [!question] Cos'è la diversità?
> I singoli classificatori devono essere (almeno parzialmente) INDIPENDENTI tra loro, cioè NON devono commettere gli stessi errori nello stesso momento.

Strategie principali per ottenere la diversità:
1. Feature diverse o diverse estrazioni di feature.
2. Algoritmi di classificazione differenti (es. combinando una SVM, una Rete Neurale e un KNN).
3. Addestramento dello stesso algoritmo su porzioni diverse di training set (**Bagging**).
4. Addestramento di modelli in sequenza, in cui ciascuno si focalizza sugli errori del precedente (**Boosting**).

#### Prestazioni Teoriche e Distribuzione Binomiale
Se un ensemble è composto da $nc$ classificatori indipendenti con accuratezza $P > 0.5$, l'accuratezza totale tramite maggioranza si calcola con la distribuzione binomiale:
$$P_{multi}(nc) = \sum_{m=k}^{nc} \binom{nc}{m} P^m (1 - P)^{nc-m}, \quad k = \frac{nc + 1}{2}$$

> [!quote] **è la formula che calcola la probabilità che una squadra di classificatori (l'ensemble) prenda la decisione giusta, basandosi sulla bravura del singolo e su quanti compongono la squadra**.

![[Pasted image 20261019145735.png]]
> [!fail] La formula è basata sull'**indipendenza (totale) dei classificatori**, cosa difficilmente ottenibile in pratica.

---
### Fusioni

#### Fusione a Livello di Decisione

Ogni classificatore $C_j$ restituisce solo l'etichetta della classe scelta, dopodichè la combinazione dei risultati può avvenire a due livelli:

- **Majority Vote Rule**: Vince la classe che riceve più voti dai singoli classificatori
  $$t = \arg\max_{i=1 \dots s} \sum_{j=1}^{nc} \theta_{ij}, \quad \theta_{ij} = \begin{cases} 1 & \text{se } C_j \text{ vota classe } i \\ 0 & \text{altrimenti} \end{cases}$$
![[Pasted image 20261019144019.png|297]]


- **Borda Count**: Ogni classificatore fornisce una graduatoria (ranking) delle classi. I punteggi ottenuti dai posizionamenti vengono sommati.
#### Fusione a Livello di Confidenza

> [!abstract] Ogni classificatore $C_j$ restituisce un vettore di confidenza/probabilità
> $$conf_j = [conf_{j1}, \dots, conf_{js}]$$
>Dove $s$ è il numero di classi ed ciascun elemento $\text{conf}_{ji}$ indica il **grado di appartenenza** (o probabilità/score) del pattern alla classe $i$-esima secondo il classificatore $j$.

Per combinare questi vettori e prendere la decisione finale su quale sia la classe vincente $t$, si possono usare diversi operatori matematici:

- **SOMMA ($\text{sum}$)**: Si sommano i valori di confidenza forniti da tutti i classificatori per ciascuna classe $i$. Si sceglie la classe che ottiene la somma massima.$$\text{sum}_i = \sum_{j=1}^{nc} \text{conf}_{ji} \quad \implies \quad t = \arg\max_{i=1\dots s} \{\text{sum}_i\}$$
- **PRODOTTO ($\text{prod}$)**: Si moltiplicano le confidenze di tutti i classificatori per ciascuna classe $i$ e si sceglie la classe con il valore massimo.$$\text{prod}_i = \prod_{j=1}^{nc} \text{conf}_{ji} \quad \implies \quad t = \arg\max_{i=1\dots s} \{\text{prod}_i\}$$
- **MASSIMO ($\text{max}$)**: Si individua la confidenza massima registrata per ciascuna classe tra tutti i classificatori e si sceglie la classe con il massimo assoluto.
    
- **MINIMO ($\text{min}$)**: Si individua la confidenza minima ottenuta per ciascuna classe e poi si seleziona il **massimo tra i minimi** ($\arg\max_i \{\text{min}_i\}$). Questo criterio (meno intuitivo) premia la classe che non ha ricevuto punteggi/confidenze eccessivamente basse da nessun classificatore.
- **SOMMA PESATA (Avgsum)**Una variante molto efficace della somma consiste nel pesare il contributo di ciascun classificatore $C_j$ in base alla sua affidabilità o "grado di abilità" $g_j$:

$$\text{avgsum}_i = \sum_{j=1}^{nc} g_j \cdot \text{conf}_{ji} \quad \implies \quad t = \arg\max_{i=1\dots s} \{\text{avgsum}_i\}$$

> [!hint] **Determinate i pesi ($g_j$)** 
> I gradi di abilità vengono tipicamente assegnati in modo **inversamente proporzionale all'errore di classificazione** del singolo classificatore sul training set (un esempio tipico di questa logica si trova nell'algoritmo _AdaBoost_).

![[Pasted image 20261019143944.png|321]]



---

## Algoritmi Ensemble Specifici

### Random Forest (Bagging)

Random Forest (Breiman, 2001) fa parte delle tecniche di **Bootstrap Aggregating (Bagging)**

> [!question] Cos'è il Bagging ? (**Bootstrap Aggregating)
> Se dai l'intero pacchetto di dati ad un solo modello, quel modello si farà un'idea rigida del problema. Nel Bagging, invece, prendi il tuo set di dati e ne estrai a caso dei sottoinsiemi (usando il "re-imbussolamento", cioè rimettendo dentro ogni volta i punti estratti). Di solito ogni sottoinsieme contiene circa due terzi dei dati originali. Addestri un modello diverso su ciascun sottoinsieme e poi fai votare tutti i modelli insieme.

1. **Pattern Bagging**: Si estraggono $S_j$ campioni con re-imbussolamento dal TS (circa 2/3 del totale per ogni albero).
2. **Feature Bagging**: Ad ogni nodo di ogni albero decisionale (CART), la scelta della feature migliore su cui effettuare lo split non viene fatta su tutte le $d$ feature, ma su un sottoinsieme casuale di dimensione $d' = \sqrt{d}$.
3. **Classificatori base**: Alberi decisionali CART binarizzati.


==PAG.28==
