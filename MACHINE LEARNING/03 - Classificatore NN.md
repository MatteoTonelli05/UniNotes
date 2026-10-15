## Classificatori Nearest Neighbor (NN e k-NN)

> [!abstract] Data un metrica $dist(\cdot)$ nello spazio multidimensionale *(es. distanza euclidea)* il classificatore **nearest neighbor** classifica un pattern $x$ con la stessa classe dell'elemento $x'$ da esso più vicino nel training set $TS$:
> $$dist(x,x') = min_{x_i\in{TS}}{\{dist(x,x_i)\}}$$

> [!question] Come funziona?
> Si presuppone che se $x'$ è molto vicino a $x$: $$P(w_i|x) \approx P(w_i|x')$$

> [!error] Attenzione
> Nonostante si possa **dimostrare** che la probabilità di errore $P$ del NN non è mai peggiore del doppo del minimo errore possibile $P^*$ (quello Bayesiano)
> Nonostante ciò bisogna valutare il contesto.
### Nearest Neighbor (1-NN) e Tassellazione di Voronoi

La regola nearest neighbor produce un partizionamento dello dello spazio, noto come **tassellazione di Voronoi** 
![[Pasted image 20261013152934.png]]
> [!attention] Così facendo ogni elemento determina un tassello all'interno del quale i pattern saranno assegnati alla stessa classe, il problema è che basta un elemento **outlier** affinchè tutta la classificazione vada in vacca
### k-Nearest Neighbor (k-NN) e Confidenza

> [!hint] Come ovviare alla problematica dell'1-NN
> La regola **k-Nearest Neighbor (k-NN)** determina i $k$ elementi più vicini al pattern $𝐱$ da classificare ($k$ è un iperparametro); ogni pattern tra i $k$ vicini vota per la classe cui esso stesso appartiene; il pattern $𝐱$ viene assegnato alla classe che ha ottenuto il maggior numero di voti.
> 
> ![[Pasted image 20261013154508.png]]

Da un classificatore k-NN risulta piuttosto semplice estrarre una confidenza (probabilistica) circa la classificazione eseguita:$$[v_1,v_2,...v_s], \qquad \sum_{i=1}^s{v_i=k}$$
i voti ottenuti dal pattern $𝐱$, allora le confidenze (vedi sfumature in figura sotto) possono essere semplicemente ottenute dividendo per 𝑘 i voti ottenuti:
![[Pasted image 20261013160112.png|341]]
### Complessità Computazionale, Editing e Condensing

> [!error] Per il classificatore NN serve **memorizzare** tutti i pattern del training set e, per ogni classificazione, calcolare la distanza da tutti i pattern e ordinare le distanze per prendere le più piccole.

##### **1 Soluzione. Editing/Condensing (Riduzione del dataset)**

Anziché salvare **tutti** i punti del dataset e calcolare centinaia di distanze per ogni classificazione, si individuano pochi punti rappresentativi per ciascuna classe chiamati **prototipi**. La classificazione avviene calcolando la distanza **solo rispetto a questi prototipi**.

- **Editing:** Rimuove o seleziona punti esistenti direttamente dal Training Set senza crearne di nuovi.
    
- **Condensing:** Deriva nuovi punti sintetici (i prototipi) che non esistevano originariamente nel Training Set.

##### **2  Soluzione. Strutture Spaziali (es. $k$-d tree, Ball tree)**

Sono strutture dati ad albero pensate per indicizzare i punti nello spazio ed evitare di calcolare la distanza con _tutti_ gli elementi del dataset. Dividono ricorsivamente lo spazio in regioni più piccole, consentendo di scartare intere sezioni lontane dal punto da classificare. Questo riduce la complessità computazionale da un confronto lineare $O(N)$ a uno logaritmico $O(\log N)$ in fase di ricerca dei vicini. Diventano tuttavia meno efficienti all'aumentare delle dimensioni delle caratteristiche (_maledizione della dimensionalità_).

##### **3  Soluzione. Libreria FLANN 

È una libreria open-source scritta in C++ che velocizza la ricerca trovando i vicini _approssimati_ anziché quelli esatti. Integra diversi algoritmi (come foreste di $k$-d tree casuali e k-means gerarchici) e seleziona automaticamente quello migliore e i suoi parametri in base al dataset. In cambio di un'infima perdita di accuratezza, offre un'accelerazione di diversi ordini di grandezza nei tempi di calcolo. È fondamentale per applicazioni in tempo reale e con dataset enormi, come nella computer vision.

---

## Metriche
### Il problema delle metriche nel $k$-NN e le scale delle Feature
La regola del $k$-Nearest Neighbor ($k$-NN) si basa sul calcolo della distanza tra punti. La scelta della funzione di distanza (o metrica) è quindi fondamentale per il suo funzionamento.

#### Distanza di Minkowski ($L_k$)
La famiglia di metriche di Minkowski tra due vettori $d$-dimensionali $\mathbf{a}, \mathbf{b} \in \mathbb{R}^d$ è definita come:$$L_k(\mathbf{a}, \mathbf{b}) = \left( \sum_{i=1}^{d} \vert{}a_i - b_i\vert{}^k \right)^{\frac{1}{k}}$$
- Per $k = 2$, si ottiene la classica **Distanza Euclidea**:    $$d_{\text{euc}}(\mathbf{a}, \mathbf{b}) = \sqrt{\sum_{i=1}^{d} (a_i - b_i)^2}$$
- Per $k = 1$, si ottiene la **Distanza Manhattan**:$$d_{\text{man}}(\mathbf{a}, \mathbf{b}) = \sum_{i=1}^{d} \vert{}a_i - b_i\vert{}$$
#### Il problema dello "Spazio di Variazione" (Dominanza di scala)
Se le feature hanno intervalli di valore (_range_) molto diversi, la componente con l'intervallo più ampio domina arbitrariamente il calcolo della distanza Euclidea.

- **Esempio:** Consideriamo due feature per classificare una persona:
    - $x_1 =$ Altezza $\in [140, 210]\text{ cm}$ (Spazio di variazione: $\Delta x_1 = 70\text{ cm}$).
    - $x_2 =$ Lunghezza del piede $\in [20, 40]\text{ cm}$ (Spazio di variazione: $\Delta x_2 = 20\text{ cm}$).
        ![[Pasted image 20261013164550.png|389]]
- **Conseguenza:** Poiché $\Delta x_1 > \Delta x_2$, una variazione nell'altezza influenzerà il calcolo di $d_{\text{euc}}$ molto più di un'analoga variazione nella lunghezza del piede, portando a classificazioni sbilanciate.
### Tecniche di Normalizzazione
Per evitare che feature con intervalli più ampi dominino il modello, si trasformano i dati tramite opportuni metodi di riscalamento.

#### Min-Max Scaling (Rescaling)
Mappa ogni feature $i$-esima in un intervallo prefissato, solitamente $[0, 1]$:

$$x'_i = \frac{x_i - \min_i}{\max_i - \min_i}$$

dove $\min_i$ e $\max_i$ sono il valore minimo e massimo della feature $i$-esima nel Training Set.

#### Standardization (Z-score normalization)
Trasforma i dati in modo che abbiano media nulla ($\mu = 0$) e deviazione standard unitaria ($\sigma = 1$) sul Training Set:$$x'_i = \frac{x_i - \text{mean}_i}{\text{stddev}_i}$$
dove:
- $\text{mean}_i = \mu_i = \frac{1}{N}\sum_{j=1}^{N} x_{j,i}$
- $\text{stddev}_i = \sigma_i = \sqrt{\frac{1}{N}\sum_{j=1}^{N} (x_{j,i} - \mu_i)^2}$

> **Regola fondamentale di Data Leakage:** I parametri della normalizzazione ($\min_i, \max_i, \mu_i, \sigma_i$) devono essere calcolati **esclusivamente sul Training Set** e poi applicati indistintamente a Validation e Test Set (o ri-calcolati ad ogni fold nella $k$-fold Cross-Validation).

### Correlazione tra Feature e Trasformazione di Whitening
Le normalizzazioni univariate (Min-Max e Standardization) lavorano su ogni feature in modo indipendente. Tuttavia, non risolvono il problema della **correlazione tra le feature**.

#### Il problema della correlazione
Se due feature sono fortemente correlate, introducono ridondanza e riducono la capacità discriminativa complessiva del classificatore. Feature correlate deformano lo spazio Euclideo in "ellissoidi" orientati obliquamente rispetto agli assi.

#### Soluzioni per la decorrelazione e sfericizzazione
1. **Distanza di Mahalanobis:** Modifica la metrica di distanza tenendo conto della matrice di covarianza $\mathbf{\Sigma}$ dei dati:    $$d_{\text{Mahalanobis}}(\mathbf{a}, \mathbf{b}) = \sqrt{(\mathbf{a} - \mathbf{b})^T \mathbf{\Sigma}^{-1} (\mathbf{a} - \mathbf{b})}$$
2. **Whitening Transform (Sfericizzazione):** Modifica i dati stessi a priori applicando una trasformazione lineare $\mathbf{W}$:$$\mathbf{x}' = \mathbf{W}\mathbf{x}$$
    Dopo la trasformazione di Whitening, la matrice di covarianza dei nuovi dati diventa l'identità ($\mathbf{\Sigma}' = \mathbf{I}$), il che equivale a sfericizzare la distribuzione e rendere valida l'applicazione della classica Distanza Euclidea.
    

### Metric Learning (Apprendimento della Metrica)
Invece di usare metriche fisse predefinite, il **Metric Learning** apprende una trasformazione ideale direttamente dai dati tramite apprendimento supervisionato.
#### Obiettivo
Trovare una trasformazione dello spazio degli input che:
- **Avvicini** pattern appartenenti alla stessa classe.
- **Allontani** pattern appartenenti a classi diverse.
#### Formulazione Lineare
Invece di calcolare la distanza Euclidea standard tra gli input $d(\mathbf{a}, \mathbf{b}) = \Vert{}\mathbf{a} - \mathbf{b}\Vert{}_2$, si apprende una matrice di trasformazione $\mathbf{G}$:
$$\text{dist}(\mathbf{a}, \mathbf{b}) = \Vert{}\mathbf{G}\mathbf{a} - \mathbf{G}\mathbf{b}\Vert{}_2 = \sqrt{(\mathbf{a} - \mathbf{b})^T \mathbf{G}^T \mathbf{G} (\mathbf{a} - \mathbf{b})}$$
_(Se poniamo $\mathbf{M} = \mathbf{G}^T \mathbf{G}$, si nota come questa sia una generalizzazione della distanza di Mahalanobis)._

#### Formulazione Non Lineare
Quando i confini di separazione sono complessi, si proietta l'input in uno spazio non lineare tramite una funzione $\phi(\cdot)$:
$$\text{dist}(\mathbf{a}, \mathbf{b}) = \Vert{}\mathbf{G}\phi(\mathbf{a}) - \mathbf{G}\phi(\mathbf{b})\Vert{}_2$$

### Similarità e Distanza Coseno
La distanza Euclidea misura la differenza in **ampiezza/modulo** tra punti. In ambiti come _Text Mining_ o _Information Retrieval_, la lunghezza assoluta dei vettori è irrilevante (es. un documento lungo contiene più parole di uno corto, ma il loro argomento/contenuto può essere identico).

### Similarità Coseno
Misura il coseno dell'angolo $\theta$ compreso tra due vettori $\mathbf{a}$ e $\mathbf{b}$:$$\text{CosSimilarity}(\mathbf{a}, \mathbf{b}) = \cos(\theta) = \frac{\mathbf{a}^T \mathbf{b}}{\Vert{}\mathbf{a}\Vert{} \Vert{}\mathbf{b}\Vert{}} = \frac{\sum_{i=1}^d a_i b_i}{\sqrt{\sum_{i=1}^d a_i^2} \sqrt{\sum_{i=1}^d b_i^2}}$$
- $\text{CosSimilarity} \in [-1, 1]$
- Vettori con stessa direzione: $\cos(\theta) = 1$
- Vettori ortogonali: $\cos(\theta) = 0$
- Vettori opposti: $\cos(\theta) = -1$
    
### Distanza Coseno
Si definisce come il complemento a 1 della similarità:
$$\text{CosDistance}(\mathbf{a}, \mathbf{b}) = 1 - \text{CosSimilarity}(\mathbf{a}, \mathbf{b}) = 1 - \frac{\mathbf{a}^T \mathbf{b}}{\Vert{}\mathbf{a}\Vert{} \Vert{}\mathbf{b}\Vert{}}$$
> **Proprietà Metrica:** La $\text{CosDistance}$ **non è una vera metrica** formale perché non rispetta la disuguaglianza triangolare.

Per ottenere una vera metrica formale basata sull'angolo, si utilizza la **Distanza Angolare**:
$$\text{AngularDistance}(\mathbf{a}, \mathbf{b}) = \cos^{-1}\left( \text{CosSimilarity}(\mathbf{a}, \mathbf{b}) \right) = \theta$$