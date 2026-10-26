# Random Forest e Metodi di Bagging

> [!abstract] **Random Forest** fa parte dei metodi di **Bagging** (_Bootstrap Aggregating_).

![[Pasted image 20261026174622.png|569]]
### Principi del Bagging

1. Si estraggono sottoinsiemi $S_j$ di pattern dal Training Set originale tramite campionamento con re-imbussolamento (generalmente $\sim 2/3$ del totale dei dati).
    
2. Si addestra in modo indipendente un classificatore $C_j$ su ciascun sottoinsieme $S_j$.
    
3. Le predizioni dei singoli classificatori vengono aggregate e fuse (ad esempio tramite _majority vote rule_ o somma delle confidenze).
    

### Decision Trees (CART)

Nelle Random Forest i singoli classificatori deboli sono **Decision Tree Classifiers** (in particolare l'algoritmo **CART**).

- **Partizionamento del nodo**: CART è un albero binario. Ad ogni nodo le istanze afferenti vengono divise tra i due nodi figli in base alla soglia impostata su una singola feature.
    
- **Criterio di Split**: La coppia (feature $k$, soglia $t_k$) viene scelta in modo greedy cercando la massima purezza dei sottonodi, ovvero minimizzando la funzione di costo:
    
    $$J(k, t_k) = \frac{m_{\text{left}}}{m} G_{\text{left}} + \frac{m_{\text{right}}}{m} G_{\text{right}}$$
    
- **Metriche di Impurità**:
    
    - **Indice di Gini** (impostazione predefinita per velocità di calcolo):
        
        $$\text{Gini} = 1 - \sum_{i=1}^{s} p_i^2$$
        
    - **Entropia**:
        
        $$\text{Entropy} = -\sum_{i=1}^{s} p_i \log_2(p_i)$$
        
- **Classificazione della foglia**: Raggiunta una foglia, l'assegnazione della classe avviene per maggioranza dei campioni presenti al suo interno.
    

### Caratteristiche specifiche di Random Forest

- **Feature Bagging**: Ad ogni split di un nodo, la selezione della feature migliore viene effettuata non sull'insieme di tutte le $d$ variabili disponibili, ma su un sottoinsieme casuale di dimensione ridotta (valore tipico $d' = \sqrt{d}$). Ciò evita che alberi differenti scelgano ripetutamente le medesime feature dominanti, aumentando l'indipendenza strutturale degli alberi.
    
- **Iperparametri chiave**:
    
    - `n_estimators`: Numero di alberi da generare nella foresta (solitamente da diverse centinaia a migliaia). Incrementarlo non genera overfitting, al più aumenta l'impegno computazionale.
        
    - `max_depth`: Limite di profondità massima degli alberi. Ha un effetto regolarizzante e riduce il rischio di overfitting.
        
- **Stima Out-Of-Bag (OOB)**: Dato l'uso del bagging, la capacità di generalizzazione può essere validata tramite i pattern rimasti esclusi dai singoli campionamenti (senza dover riservare un validation set separato).
    

# AdaBoost (Adaptive Boosting)

Proposto da Freund e Schapire nel 1995, **AdaBoost** appartiene ai metodi di **Boosting**. Si basa sull'idea di combinare iterativamente un insieme di classificatori "deboli" (_weak classifiers_) per costruire un classificatore "forte" (_strong classifier_).

### Differenze chiave rispetto al Bagging

- L'addestramento non avviene in parallelo ma in modo **sequenziale**.
    
- Ad ogni iterazione viene modificato il peso associato a ciascun campione del training set: i pattern erroneamente classificati acquisiscono un peso maggiore per forzare il classificatore successivo a concentrarsi su di essi.
    

### Formulazione Matematica e Algoritmo (Caso Binario)

1. **Inizializzazione dei pesi**:
    
    $$w_i^1 = \frac{1}{n}, \quad i = 1 \dots n$$
    
2. **Ciclo di addestramento** (per $t = 1 \dots T$):
    
    - Normalizzare i pesi affinché la somma valga 1.
        
    - Calcolare l'errore pesato $\epsilon_j$ per ciascun classificatore candidato $h_j^t(x)$:
        
        $$\epsilon_j = \sum_{i=1}^n w_i^t \cdot I(h_j^t(x_i) \neq y_i)$$
        
    - Selezionare il classificatore $h^t(\cdot)$ con errore minimo $\epsilon^t = \min \epsilon_j$.
        
    - Calcolare l'importanza/peso $\alpha^t$ da assegnare al classificatore selezionato:
        
        $$\alpha^t = \frac{1}{2} \ln \left( \frac{1 - \epsilon^t}{\epsilon^t} \right)$$
        
    - Aggiornare i pesi dei campioni per l'iterazione successiva:
        
        $$w_i^{t+1} = w_i^t \cdot e^{-\alpha^t y_i h^t(x_i)}$$
        
3. **Classificatore Finale Strong**:
    
    $$H(x) = \text{sign}\left( \sum_{t=1}^{T} \alpha^t h^t(x) \right)$$
    

### Comportamento dei pesi

- **Peso del classificatore $\alpha^t$**: Se l'errore $\epsilon^t$ è pari a $0.5$ (equivalente al caso puramente casuale), $\alpha^t = 0$. Se l'errore si avvicina a 0, $\alpha^t$ cresce esponenzialmente in positivo. Se l'errore è $> 0.5$, $\alpha^t$ assume valore negativo.
    
- **Aggiornamento campioni $w_i^{t+1}$**: Il peso di un campione aumenta se viene classificato in maniera errata da un modello con $\alpha^t > 0$, mentre diminuisce se classificato correttamente.
    

# Casi Applicativi: Viola & Jones Face Detector

Metodologia per il rilevamento del volto in immagini basata su AdaBoost:

- **Weak Classifiers**: Costituiti da semplici filtri rettangolari (_Haar features_, $\approx 160.000$ variazioni possibili) calcolabili rapidamente tramite l'immagine integrale.
    
- **Architettura a Cascata**: Sequenza di _strong classifier_ addestrati con AdaBoost. Un sottoinsieme di immagini viene scartato immediatamente non appena un livello individua un pattern _non-face_.
    
- I primi livelli della cascata impiegano filtri molto leggeri (ad esempio 2 soli weak classifier per eliminare circa il 50% dei falsi positivi iniziali), garantendo elevata efficienza complessiva.
    

# Gradient Boosting

Tecnica di boosting alternativa ad AdaBoost:

- Invece di incrementare il peso dei pattern difficili (_hard examples_), adatta iterativamente i nuovi modelli deboli sui **residui dell'errore** commessi dai modelli precedenti.
    
- Ampiamente impiegato sia per compiti di classificazione sia di regressione.
    
- **XGBoost**: Implementazione ottimizzata ad alte prestazioni integrate con gli standard della libreria scikit-learn (`fit` / `predict`).