## Concetti di base

### Definizione e difficoltà

> [!abstract] Definizione
> Il **clustering** è una tecnica di **apprendimento automatico non supervisionato** (_unsupervised machine learning_) che serve a raggruppare un insieme di dati non etichettati in gruppi omogenei, chiamati **cluster**.
>
![[Pasted image 20261106164506.png|334]]
> _Un **pattern** = un punto (vettore) $\mathbf{x}$ nello spazio multidimensionale._

> [!attention] Il problema è **NP-hard** 
> La ricerca esaustiva della soluzione ottima è fattibile **solo** con pochissimi pattern.
### Quante soluzioni possibili?

Con $n$ pattern e $s$ cluster (noto), il numero di partizioni è dell'ordine di:

$$\frac{s^n}{s!}$$

(approssimazione del **numero di Stirling di seconda specie**, ovvero il **numero di modi in cui è possibile suddividere un insieme di \(n\) oggetti distinguibili in \(k\) sottoinsiemi disgiunti e non vuoti**).


> [!example] Esempio con $n=4$, $s=2$ 
> $2^3-1=7$ soluzioni 
> $(A)(BCD)$, $(B)(ACD)$, $(C)(ABD)$, $(D)(ABC)$, $(AB)(CD)$, $(AC)(BD)$, $(AD)(BC)$

> [!important] Se $s$ non è noto, il numero di soluzioni è dato dal numero di **Bell** 
### Criteri vs algoritmi

> [!important] Distinzione fondamentale
> 
> - **Criterio** = _cosa_ vogliamo ottenere (una funzione che dice quanto è buona una partizione).
> - **Algoritmo** = _come_ cerchiamo una soluzione che ottimizzi il criterio.

### Criterio: minimizzazione delle distanze dai centroidi

$$J_e=\sum_{i=1}^{s}\sum_{\mathbf{x}\in C_i}|\mathbf{x}-\bar{\mathbf{x}}_i|^2,\qquad \bar{\mathbf{x}}_i=\frac{1}{n_i}\sum_{\mathbf{x}\in C_i}\mathbf{x}$$

- $C_i$ = $i$-esimo cluster, 
- $n_i$ = numero di pattern in $C_i$, 
- $\bar{\mathbf{x}}_i$ = suo centroide (baricentro, media).

In pratica: somma dei **quadrati delle distanze** dei punti dal centroide del proprio cluster (detta anche _SSE_ o _inertia_).

> [!hint] Buono per cluster a **simmetria radiale** (circolari/sferici).

### Criterio: minimizzazione delle distanze intra-classe

$$J_e=\sum_{i=1}^{s} n_i\cdot\bar{s}_i,\qquad \bar{s}_i=\frac{1}{n_i}\sum_{\mathbf{x}\in C_i} f_s(\mathbf{x},C_i)$$

dove $f_s(\mathbf{x},C_i)$ misura la distanza tra $\mathbf{x}$ e il suo cluster. Due scelte tipiche:

| Scelta di $f_s$                | Formula                                                                             | Effetto                             |
| ------------------------------ | ----------------------------------------------------------------------------------- | ----------------------------------- |
| **Media** delle distanze       | $\frac{1}{n_i}\sum_{\mathbf{x}'\in C_i}\|\mathbf{x}-\mathbf{x}'\|^2$                | Simile al criterio dei centroidi    |
| **Minimo** (vicino più vicino) | $\min_{\mathbf{x}'\in C_i,\ \mathbf{x}'\neq\mathbf{x}}\|\mathbf{x}-\mathbf{x}'\|^2$ | **Non penalizza cluster allungati** |
![[Pasted image 20261106171015.png|410]]_il criterio della distanza dal centroide produrrebbe i due cerchi trattgeggiati_

---

## Famiglie di algoritmi

##### **1. Gerarchico**

  >[!abstract] Idea
  >Aggrega i pattern "bottom-up" in base a una distanza e produce un albero (dendrogramma).
  
  > [!example] Esempi
  >  single / average / complete link.

##### **2. Basato su centroidi**
  
  > [!abstract] Idea
  > euristiche iterative che minimizzano la distanza dei pattern dai centroidi.
  
  > [!example] Esempi
  > K-means, Fuzzy K-means, Expectation-Maximization con Gaussian Mixture|EM / Gaussian Mixture.

##### **3. Basato sulla densità**
  
> [!abstract] Idea
> i cluster sono regioni connesse ad alta densità.

> [!example] Esempio
> **DBSCAN**.
### Hard vs soft

- **Hard (esclusivo)**: ogni pattern appartiene a **un solo** cluster.
- **Soft (fuzzy)**: ogni pattern ha un **grado di appartenenza** (es. tra 0 e 1) a ciascun cluster.
    - Più efficace con pattern vicini al **bordo** tra cluster e con **outlier**.
    - Si può tornare a un'assegnazione hard scegliendo, per ogni pattern, il cluster con appartenenza **massima**.

---

## Clustering gerarchico

Simile alla **tassonomia biologica**: si passa da famiglie molto ampie a famiglie sempre più ristrette.

**Approccio agglomerativo (bottom-up)**

1. Si parte con ogni pattern come cluster a sé.
2. Ad ogni livello si aggregano (pattern-pattern, pattern-cluster o cluster-cluster) gli elementi **meno distanti**, rispetto a una soglia che dipende dal livello.
3. Il risultato è un **dendrogramma** (albero). Tagliando l'albero a un certo livello si ottiene il numero di cluster $k$ desiderato.

**Come si misura la distanza tra due cluster** (la vera differenza tra le varianti):

|Variante|Distanza tra cluster A e B|Tendenza|
|---|---|---|
|**Single link**|minima tra due pattern (uno per cluster)|Cluster allungati, effetto "catena"|
|**Average link**|media delle distanze tra tutte le coppie|Compromesso|
|**Complete link**|massima tra due pattern|Cluster compatti|

> [!tip] Lettura del dendrogramma L'asse verticale (_similarity scale_) indica a che livello due elementi si fondono. Fusioni "in basso" (alta similarità) = elementi molto vicini; fusioni "in alto" = gruppi molto diversi tra loro. Nell'esempio con $x_1\dots x_8$ la riga $k=1$ è un solo cluster, $k=8$ sono tutti singoli.

> [!note] Vantaggio Non serve fissare $s$ in anticipo: il numero di cluster si sceglie _dopo_, decidendo dove tagliare l'albero.

---

## K-means

(detto anche **C-means**)

> [!abstract] Idea Ripeti due passi: **assegna** ogni punto al centroide più vicino, poi **ricalcola** ogni centroide come media dei suoi punti. Fermati quando non cambia più nulla.

### Caratteristiche

- Semplicissimo da capire e da implementare → spesso **la prima scelta**.
- Minimizza **implicitamente** $J_e$ (distanze dai centroidi).
- **Input**: numero di cluster $s$ + soluzione iniziale (centroidi iniziali).
- Ottimizzazione **iterativa e locale** → può fermarsi in un **ottimo locale**.
- Converge di solito in **pochi passi** (< 10 nell'esempio).
- Forma dei cluster:
    - distanza **euclidea** → cluster **iper-sferici**
    - distanza di **Mahalanobis** → cluster **iper-ellissoidali**

> [!warning] Nota terminologica La slide parla di "massimi locali", ma qui si **minimizza** $J_e$, quindi in realtà si parla di **minimi locali** (la slide successiva infatti dice "minimi locali").

### Algoritmo

```
Inizializza n, s
Scegli a caso s pattern come centroidi iniziali
do {
    assegna ogni pattern al cluster il cui centroide è più vicino
    ricalcola i centroidi come media dei pattern di ciascun cluster
} while (i cluster sono cambiati  &  iterazione < max)
```

Termina quando i centroidi sono stabili, cioè le partizioni non cambiano più.

> [!example] Esempio della slide ($n=86$, $s=3$) Dalla soluzione iniziale (centroidi molto vicini tra loro al centro) i centroidi si "allargano" iterazione dopo iterazione, finché alla 7ª iterazione si ha **convergenza** con tre gruppi distinti.

### Limitazioni

- Non riconosce cluster **non sferici** (allungati, a forma irregolare, innestati): nelle slide, 3 classi in 8 iterazioni e 11 classi in 31 iterazioni producono suddivisioni "tagliate male" proprio perché i gruppi reali non sono sferici.
- Richiede di **conoscere $s$**.
- Dipende dall'**inizializzazione**.
- Sensibile agli **outlier** (la media viene trascinata).
- Dipende dalla **scala** delle feature → conviene normalizzare.

### Come ridurre il rischio di minimi locali

Eseguire l'algoritmo **più volte** con soluzioni iniziali diverse e tenere la migliore (quella con $J_e$ minore). Le iniziali possono essere:

- random
- prodotte da un altro euristico
- prodotte da un metodo evoluzionistico (**algoritmo genetico**)

> [!tip] Extra utile (non nelle slide) **K-means++** è la tecnica di inizializzazione standard (e il default di scikit-learn): sceglie i centroidi iniziali sparsi tra loro, con probabilità proporzionale alla distanza dai centroidi già scelti.

---

## K-means validazione e scelta di s

**Problema**: quanti cluster scegliere? (_clustering validation_)

### Perché $J_e$ da solo non basta

$J_e$ **diminuisce sempre** all'aumentare di $s$. Per $s=n$ (ogni punto è un cluster) si ha $J_e=0$: soluzione "perfetta" ma inutile.

Serve quindi un criterio che bilanci **bontà** e **complessità**.

### Soluzione 1: penalizzazione

$$J_e^{*}=J_e+\text{penalty}\cdot s$$

Il problema si sposta sulla scelta di _penalty_ (non risolve davvero).

### Soluzione 2: metodo del gomito (elbow)

Si traccia $J_e$ al variare di $s$ e si cerca il punto di **flessione**, dove il calo **rallenta di colpo**.

- Se i dati hanno raggruppamenti evidenti, scegliendo il giusto $s$ si vede una discontinuità nel grafico.
- Nell'esempio della slide: per $s=4$ il calo rallenta molto → la curva sembra un braccio e $s=4$ è il **gomito**.

### Soluzione 3: silhouette score

Più oggettivo, ma adatto a cluster **sferici** (come quelli di K-means).

$$\text{Silhouette coefficient}=\frac{b-a}{\max(a,b)}$$

- $a$ = distanza **media intra-cluster** del pattern (da tutti gli altri punti del suo cluster)
- $b$ = distanza media dai pattern del **cluster più vicino** (escluso il proprio)

**Silhouette score** = media dei coefficienti di tutti i pattern. Si sceglie l'$s$ che lo **massimizza**.

> [!tip] Come leggere il valore
> 
> - vicino a **+1**: il punto è ben dentro il proprio cluster e lontano dagli altri
> - vicino a **0**: il punto è al confine tra due cluster
> - **negativo**: probabilmente è nel cluster sbagliato

- In scikit-learn: `silhouette_score()`
- Pesante computazionalmente su grandi dataset (servono le distanze tra tutte le coppie).
- Il **silhouette diagram** (vedi Géron) mostra graficamente i coefficienti, cluster per cluster, ed è utile per la validazione.

---

## Fuzzy K-means

Variante **soft**: ogni pattern appartiene a **tutti** i cluster con una certa probabilità/grado.

### Criterio da ottimizzare

$$J_{fuz}=\sum_{i=1}^{s}\sum_{j=1}^{n}\big[P(C_i|\mathbf{x}_j,\Theta)\big]^m\cdot|\mathbf{x}_j-\boldsymbol{\theta}_i|^2$$

- $P(C_i|\mathbf{x}_j,\Theta)$ = grado di appartenenza di $\mathbf{x}_j$ al cluster $C_i$
- $\Theta={\boldsymbol{\theta}_1\dots\boldsymbol{\theta}_s}$ = insieme dei centroidi della soluzione attuale
- $m>1$ = parametro di "sfumatura" (tipico $m=2$)

### Formule di aggiornamento

**Eq. (1): centroidi come media pesata** (peso = appartenenza)

$$\boldsymbol{\theta}_i=\frac{\sum_{j}P(C_i|\mathbf{x}_j,\Theta)\cdot\mathbf{x}_j}{\sum_{j}P(C_i|\mathbf{x}_j,\Theta)}$$

**Eq. (2): gradi di appartenenza**

$$P(C_i|\mathbf{x}_j,\Theta)=\frac{1}{\sum_{k=1}^{s}\left(\dfrac{|\mathbf{x}_j-\boldsymbol{\theta}_i|}{|\mathbf{x}_j-\boldsymbol{\theta}_k|}\right)^{\frac{2}{m-1}}}$$

> [!tip] Intuizione su Eq. (2) Più $\mathbf{x}_j$ è **vicino** a $\boldsymbol{\theta}_i$ rispetto agli altri centroidi, più il rapporto delle distanze è piccolo e più l'appartenenza a $C_i$ è alta. Le appartenenze di un punto a tutti i cluster **sommano a 1**.

> [!tip] Effetto di $m$
> 
> - $m\to1$: assegnazione sempre più **hard** (si avvicina a K-means)
> - $m$ grande: appartenenze sempre più **uniformi** (tutto sfumato)

### Algoritmo

```
Inizializza n, s
Inizializza a caso P(C_i | x_j, Θ), per ogni i=1..s, j=1..n
do {
    calcola i centroidi Θ                  // Eq. (1)
    calcola i gradi di appartenenza P      // Eq. (2)
} while (riduzione di J_fuz significativa & iterazione < max)
```

> [!note] Differenza nell'inizializzazione K-means parte da **centroidi** iniziali; fuzzy K-means parte da **appartenenze** iniziali casuali (da normalizzare perché sommino a 1 per ogni pattern).

### Pro e contro rispetto a K-means

- ✅ Convergenza a volte **più robusta**.
- ✅ Gestisce meglio punti di confine e outlier.
- ❌ L'appartenenza dipende **implicitamente dal numero di cluster**: se $s$ è sbagliato si ottengono cattive soluzioni.
- ❌ Come K-means, trova solo cluster di forma sferica.

---

## Expectation-Maximization con Gaussian Mixture

### Idea generativa

Si ipotizza che i dati siano generati da un **mix di distribuzioni** (_mixture_): ogni classe genera dati secondo una **propria distribuzione**, ma a generazione finita i pattern sembrano prodotti da **un'unica distribuzione multimodale** (con più "gobbe").

**Obiettivo**: dai pattern del training set, **risalire ai parametri** delle singole distribuzioni che li hanno generati.

Ipotesi semplificative: la **forma** delle distribuzioni è nota e sono **tutte dello stesso tipo**. Caso tipico: mix di $s$ **gaussiane multivariate** (multinormali).

### Parametri da stimare

Per ogni componente $i=1\dots s$:

- vettore medio $\boldsymbol{\mu}_i$
- matrice di covarianza $\boldsymbol{\Sigma}_i$
- coefficiente (peso) $\alpha_i$

$$p(\mathbf{x}|\Theta)=\sum_{i=1}^{s}\alpha_i\cdot p_i(\mathbf{x}|\boldsymbol{\theta}_i),\qquad \boldsymbol{\theta}_i={\boldsymbol{\mu}_i,\boldsymbol{\Sigma}_i}$$

dove $\Theta={\alpha_1\dots\alpha_s,\boldsymbol{\theta}_1\dots\boldsymbol{\theta}_s}$ e $p_i(\cdot|\boldsymbol{\theta}_i)$ è una multinormale.

> [!note] Vincolo implicito I pesi $\alpha_i$ sono non negativi e **sommano a 1** ($\alpha_i$ = quanto "pesa" la componente $i$ nel mix, cioè la proporzione di dati che genera).

### Maximum likelihood

Si stimano i parametri col criterio della **massima verosimiglianza**:

$$\mathcal{L}(\Theta|X)=p(X|\Theta)$$

È la probabilità (densità) di aver ottenuto i pattern **dati i parametri**; "invertiamo" il punto di vista: i dati sono fissi, i parametri variano.

Per semplicità si massimizza il **logaritmo**. Con pattern indipendenti il prodotto diventa somma:

$$\log p(X|\Theta)=\log\prod_{j=1}^{n}p(\mathbf{x}_j|\Theta)=\sum_{j=1}^{n}\log\sum_{i=1}^{s}\alpha_i\cdot p_i(\mathbf{x}_j|\boldsymbol{\theta}_i)$$

> [!warning] Punto difficile: perché è difficile massimizzarlo? C'è una **sommatoria dentro il logaritmo**: derivando non si ottengono formule chiuse. Se però sapessimo da **quale componente** è stato generato ciascun pattern, il $\log$ si applicherebbe a un singolo termine (niente somma interna) e il problema diventerebbe semplice.

### Il trucco di EM

EM nasce per il maximum likelihood con **dati incompleti**: abbiamo $X={\mathbf{x}_1\dots\mathbf{x}_n}$ ma mancano alcuni valori $\mathbf{y}={\mathbf{y}_1\dots\mathbf{y}_n}$. Il pattern completo è $\mathbf{z}_j=(\mathbf{x}_j,\mathbf{y}_j)$.

Nel nostro caso i dati sono completi, ma **inventiamo** una variabile nascosta $y_j$ che indica **quale gaussiana ha generato $\mathbf{x}_j$**. In questo modo la massimizzazione diventa trattabile.

> [!tip] Intuizione "uovo e gallina"
> 
> - Se conoscessimo i parametri → potremmo calcolare quanto ogni punto appartiene a ogni componente.
> - Se conoscessimo le appartenenze → potremmo stimare i parametri.
> - EM alterna i due: parte da parametri a caso, stima le appartenenze, riaggiorna i parametri, e così via.

### EM nel caso generale

Obiettivo: massimizzare il **log-likelihood dei dati completi**:

$$\log\mathcal{L}(\Theta|Z)=\log\mathcal{L}(\Theta|X,\mathbf{y})=\log p(X,\mathbf{y}|\Theta)$$

Si iterano due passi fino a convergenza:

**Expectation (E)**: calcolo del valore atteso del log-likelihood completo, dato il training set e la stima corrente $\Theta^g$ (dell'iterazione precedente):

$$Q(\Theta|\Theta^g)=E\big[\log p(X,\mathbf{y}|\Theta),\big|,X,\Theta^g\big]=\int_{\mathbf{y}\in\Psi}\log p(X,\mathbf{y}|\Theta)\cdot f(\mathbf{y}|X,\Theta^g),d\mathbf{y}$$

La media è calcolata rispetto alla variabile casuale $\mathbf{y}$, distribuita secondo $f(\mathbf{y}|X,\Theta^g)$.

**Maximization (M)**: si trovano i parametri che massimizzano $Q$:

$$\Theta^{g+1}=\arg\max_{\Theta}Q(\Theta|\Theta^g)$$

> [!tip] In parole povere
> 
> - **E**: "con i parametri attuali, quanto è probabile che ogni punto venga da ciascuna componente?"
> - **M**: "dati questi pesi di appartenenza, quali parametri spiegano meglio i dati?"

### EM nel caso specifico (Gaussian Mixture)

Con la variabile nascosta $y_j\in{1\dots s}$ (indice della gaussiana che ha generato $\mathbf{x}_j$) il log-likelihood completo diventa:

$$\log\mathcal{L}(\Theta|X,\mathbf{y})=\sum_{j=1}^{n}\log\big(\alpha_{y_j}\cdot p_{y_j}(\mathbf{x}_j|\boldsymbol{\theta}_{y_j})\big)$$

(niente più somma dentro il log).

Gli $y_j$ non si conoscono, ma se ne stima la **distribuzione** con il **teorema di Bayes** usando i parametri correnti $\Theta^g$:

$$P(y_j=k|\mathbf{x}_j,\Theta^g)=\frac{\alpha_k^g\cdot p_k(\mathbf{x}_j|\boldsymbol{\theta}_k^g)}{\sum_{i=1}^{s}\alpha_i^g\cdot p_i(\mathbf{x}_j|\boldsymbol{\theta}_i^g)}$$

Per un intero vettore di osservazioni (pattern indipendenti):

$$P(\mathbf{y}|X,\Theta^g)=\prod_{j=1}^{n}P(y_j|\mathbf{x}_j,\Theta^g)$$

e il valore atteso $Q$ si scrive come somma pesata su tutti i possibili $\mathbf{y}$.

> [!note] Calcoli intermedi I passaggi algebrici per arrivare alle formule finali sono "non banali" (riferimento: **Bilmes, 1998**, _A Gentle Tutorial of the EM Algorithm..._). Si ottengono **ponendo a 0 le derivate parziali** rispetto ai parametri incogniti. All'esame conta soprattutto saper **usare e interpretare** le formule finali.

### Formule incrementali

**Eq. (1): probabilità di appartenenza (passo E)**

$$P(C_k|\mathbf{x}_j,\Theta^g)=\frac{\alpha_k^g\cdot p(\mathbf{x}_j|\boldsymbol{\theta}_k^g)}{\sum_{i=1}^{s}\alpha_i^g\cdot p(\mathbf{x}_j|\boldsymbol{\theta}_i^g)}$$

**Eq. (2): peso del cluster (passo M)**

$$\alpha_k^{g+1}=\frac{1}{n}\sum_{j=1}^{n}P(C_k|\mathbf{x}_j,\boldsymbol{\theta}_k^g)$$

**Eq. (3): media del cluster (passo M)**

$$\boldsymbol{\mu}_k^{g+1}=\frac{\sum_{j}P(C_k|\mathbf{x}_j,\boldsymbol{\theta}_k^g)\cdot\mathbf{x}_j}{\sum_{j}P(C_k|\mathbf{x}_j,\boldsymbol{\theta}_k^g)}$$

**Eq. (4): covarianza del cluster (passo M)**

$$\boldsymbol{\Sigma}_k^{g+1}=\frac{\sum_{j}P(C_k|\mathbf{x}_j,\boldsymbol{\theta}_k^g),(\mathbf{x}_j-\boldsymbol{\mu}_k^{g+1})(\mathbf{x}_j-\boldsymbol{\mu}_k^{g+1})^t}{\sum_{j}P(C_k|\mathbf{x}_j,\boldsymbol{\theta}_k^g)}$$

> [!tip] Lettura intuitiva (trattando le componenti come cluster)
> 
> - **Eq. (1)**: probabilità che $\mathbf{x}_j$ appartenga a $C_k$, calcolata con la **regola di Bayes**. Ricorda che $p(\cdot|\boldsymbol{\theta}_k)$ è una multinormale.
> - **Eq. (2)**: il **peso** di $C_k$ è la media delle appartenenze a $C_k$ su tutto il training set (= "frazione di punti" che gli appartengono).
> - **Eq. (3) e (4)**: **media** e **covarianza** _pesate_ col grado di appartenenza. Se tutte le appartenenze fossero 0 o 1, si ricadrebbe nella media e covarianza classiche del cluster.
> - Nota: in Eq. (4) si usa la media **già aggiornata** $\boldsymbol{\mu}_k^{g+1}$.

### Algoritmo

```
Inizializza n, s
Inizializza g = 0, α_k^0, θ_k^0 = {μ_k^0, Σ_k^0}, k = 1..s
do {
    Expectation: calcola P(C_k | x_j, Θ^g)   k=1..s, j=1..n     // Eq. (1)
    Maximization: calcola α_k^{g+1}, μ_k^{g+1}, Σ_k^{g+1}       // Eq. (2)(3)(4)
    g = g + 1
} while (parametri α_k e θ_k non stabili & g < max)
```

**Relazione con gli altri algoritmi**: è simile a fuzzy K-means, ma oltre ai centroidi (i vettori medi $\boldsymbol{\mu}_k$) abbiamo anche **covarianze** e **pesi**, quindi più gradi di libertà → cluster **ellissoidali** con orientamento e dimensioni proprie.

### Attenzione all'implementazione

> [!warning] Matrici di covarianza Non si possono generare completamente a caso: devono essere **simmetriche** e **definite positive**. **Consiglio**: partire con **matrici diagonali**.

> [!warning] Problemi numerici Le probabilità di appartenenza possono diventare **piccolissime** (underflow). **Consiglio**: usare costanti moltiplicative se necessario e **normalizzare alla fine**. (Nella pratica si lavora in scala logaritmica con il trucco _log-sum-exp_.)

### Esempi della slide

- 3 classi, ~40 iterazioni: EM riconosce nuvole di punti **ellissoidali**, anche molto allungate e inclinate, dove K-means e fuzzy K-means fallirebbero.
- 11 classi, ~200 iterazioni: stessa idea con molti cluster sovrapposti. Notare che servono **molte più iterazioni** rispetto a K-means (7-31).

> [!tip] Extra utile (non nelle slide)
> 
> - EM converge solo a un **ottimo locale** del likelihood, quindi conviene rilanciarlo con inizializzazioni diverse (spesso inizializzato con K-means).
> - Per scegliere $s$ con le gaussian mixture si usano criteri come **BIC** e **AIC**.
> - Una GMM con covarianze sferiche uguali e assegnazione hard equivale a K-means.

---

## Confronto finale

||**K-means**|**Fuzzy K-means**|**EM (Gaussian Mixture)**|
|---|---|---|---|
|Assegnazione|hard|soft|soft (probabilità)|
|Cosa stima|centroidi|centroidi|medie + covarianze + pesi|
|Forma dei cluster|sferica (euclidea)|sferica|**ellissoidale**|
|Criterio|$J_e$|$J_{fuz}$|**log-likelihood**|
|Parametro chiave|$s$|$s$, $m$|$s$|
|Velocità|molto veloce|media|più lento (molte iterazioni)|
|Rischio|minimi locali|$s$ sbagliato → cattive soluzioni|minimi locali, problemi numerici|
|Ottimizzazione|locale/iterativa|locale/iterativa|locale/iterativa|

> [!summary] Punti chiave da ricordare
> 
> - **Criterio ≠ algoritmo.**
> - Il numero di partizioni cresce in modo **esplosivo** ($\approx s^n/s!$) → servono euristiche.
> - **K-means**: assegna → ricalcola medie; semplice, ma sferico, locale, richiede $s$.
> - Scelta di $s$: **elbow** su $J_e$, oppure **silhouette** (da massimizzare). $J_e$ da solo scende sempre.
> - **Fuzzy**: appartenenze graduali con parametro $m$ ($m=2$ tipico).
> - **EM/GMM**: variabile nascosta + Bayes; alterna E (appartenenze) e M (parametri); cluster ellissoidali.
> - **Gerarchico**: dendrogramma; single link = catene, complete link = compatti, average = via di mezzo.
> - **DBSCAN** per cluster basati sulla densità.

## Domande tipiche da ripasso

- [ ] Differenza tra criterio e algoritmo di clustering?
- [ ] Perché il problema è NP-hard? Quante soluzioni ci sono con $s=2$?
- [ ] Quali sono single, average e complete link?
- [ ] Scrivi l'algoritmo di K-means. Perché può non trovare l'ottimo globale?
- [ ] Perché non si può scegliere $s$ minimizzando $J_e$? Come si risolve (elbow, silhouette)?
- [ ] Come cambia fuzzy K-means rispetto a K-means? Che cosa fa $m$?
- [ ] Perché nell'EM la sommatoria dentro il logaritmo è un problema? Come lo aggira la variabile nascosta?
- [ ] Interpreta le Eq. (1)-(4) dell'EM.
- [ ] Quando preferire EM a K-means?