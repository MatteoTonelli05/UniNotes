# Evoluzione del Riconoscimento

Il riconoscimento di oggetti si è evoluto in diverse sotto-categorie, a seconda della precisione richiesta:

- **Classificazione:** _"In questa immagine c'è un gatto"_. (Output: Etichetta).
    
- **Localizzazione:** _"C'è un gatto ed è in questo punto"_. (Output: Label + Coordinate $x, y$).
    
- **Object Detection:** _"Trova tutti i gatti e i cani nell'immagine e disegna un rettangolo (**Bounding Box**) attorno a ognuno"_.
    
- **Instance Segmentation:** Il livello più alto. Non solo identifichiamo l'oggetto, ma lo separiamo dallo sfondo **pixel per pixel**, definendone l'esatto contorno.
    
- **Descrizione della Scena (Scene Understanding):** Capire il contesto e le relazioni tra gli oggetti (es: "un uomo sta andando in bicicletta su una strada").
    

---

# Il Problema del Confronto Diretto

L'idea ingenua sarebbe confrontare due immagini pixel per pixel per vedere se sono uguali. Questo approccio fallisce quasi sempre per i seguenti motivi:

> [!attention] **Perché il confronto pixel-pixel non funziona?**
> 
> - **Variazioni di Luce:** Un'ombra cambia tutti i valori numerici dei pixel.
>     
> - **Geometria:** Se l'oggetto è leggermente ruotato o scalato, i pixel non corrispondono più.
>     
> - **Punto di Vista:** Cambiando prospettiva, la proiezione 2D cambia radicalmente.
>     
> - **Rumore:** Il rumore del sensore introduce micro-differenze numeriche.
>     

![[Pasted image 20260407161040.png]]

---
# Template Matching

Per superare i limiti del confronto globale, si usa il **Template Matching**. Invece di confrontare due immagini intere, prendiamo una piccola immagine di riferimento (il **Template** $T$) e la facciamo scorrere (sliding window) sull'immagine principale ($I$).

![[Pasted image 20260408100933.png|339]]
## Somma delle Differenze al Quadrato (SQDIFF)

Questo metodo misura la **discrepanza** tra il template e l'immagine. Più basso è il valore ottenuto, più il template e l'immagine sono simili.

### SQDIFF Standard (CV_TM_SQDIFF)

La formula calcola la differenza tra il valore del pixel del template e quello dell'immagine, la eleva al quadrato e somma tutti i risultati nell'area $h \times w$:

$$R(y, x) = \sum_{i=0}^{h-1} \sum_{j=0}^{w-1} (T[i,j] - I[y + i, x + j])^2$$

- **Match Perfetto:** Il valore risultante è **0**.
    
- **Caratteristica:** È sensibile alle variazioni globali di luminosità. Se l'immagine diventa più chiara, il valore di $R$ cambia drasticamente anche se il contenuto è lo stesso.
    
![[Pasted image 20260408101747.png]]
### SQDIFF Normalizzato (CV_TM_SQDIFF_NORMED)

Per ridurre l'influenza dell'intensità luminosa, si divide il risultato per la radice del prodotto delle energie del template e della porzione di immagine:

$$R[y, x] = \frac{\sum_{i=0}^{h-1} \sum_{j=0}^{w-1} (T[i,j] - I[y + i, x + j])^2}{\sqrt{(\sum_{i=0}^{h-1} \sum_{j=0}^{w-1} (T[i,j])^2) \cdot (\sum_{i=0}^{h-1} \sum_{j=0}^{w-1} (I[y + i, x + j])^2)}}$$

- Il risultato è compreso tra **0 e 1**.
    
- Il **match migliore** si trova cercando il valore **minimo**.

> [!question] Quando si utilizza questa normalizzazione?
> Quando ci sono più template. Con un solo template infatti il denominatore non sarà nient'altro che una costante rendendo inutile la normalizzazione.
>  

![[Pasted image 20260408101816.png]]


---

## Correlazione Cross-Correlazione (CCORR)

Invece di misurare la differenza, questo metodo misura la **sovrapposizione** tra i pixel.
### CCORR Standard (CV_TM_CCORR)

Moltiplica i pixel corrispondenti e somma i risultati:

$$R(y, x) = \sum_{i=0}^{h-1} \sum_{j=0}^{w-1} T(i,j) \cdot I(y + i, x + j)$$

- **Match Perfetto:** Valore molto alto (infatti cerchiamo il massimo).
    
- **Problema:** Un'area dell'immagine molto chiara (bianca) produrrà un valore di correlazione molto alto anche se non somiglia affatto al template.
    
![[Pasted image 20260408102919.png]]
### CCORR Normalizzato (CV_TM_CCORR_NORMED)

Normalizza il valore dividendo per l'energia di template e immagine:
$$R[y, x] = \frac{\sum_{i=0}^{h-1} \sum_{j=0}^{w-1} (T[i,j] \cdot I[y + i, x + j])}{\sqrt{(\sum_{i=0}^{h-1} \sum_{j=0}^{w-1} (T[i,j])^2) \cdot (\sum_{i=0}^{h-1} \sum_{j=0}^{w-1} (I[y + i, x + j])^2)}}$$

- Il risultato è compreso tra **0 e 1**.
    
- Il **match migliore** è il valore **massimo** (vicino a 1).
    
![[Pasted image 20260408102937.png]]

---
## Coefficiente di Correlazione (CCOEFF)

Questo è il metodo più sofisticato e robusto perché lavora sulle **differenze rispetto alla media**. È come se sottraessimo la luminosità media dal template e dall'immagine prima di confrontarli.

### CCOEFF Standard (CV_TM_CCOEFF)

Si calcolano prima le versioni "centrate" del template ($T'$) e dell'immagine ($I'$), sottraendo la media ($\bar{T}$ e $\bar{I}$):

$$T'(i,j) = T(i,j) - \frac{1}{w \cdot h} \sum_{i',j'} T(i',j')$$

$$I'(y+i, x+j) = I(y+i, x+j) - \frac{1}{w \cdot h} \sum_{i',j'} I(y+i', x+j')$$

Poi si calcola la correlazione:

$$R(y, x) = \sum_{i,j} T'(i,j) \cdot I'_{x,y}(y+i, x+j)$$

- Questo metodo è eccellente per gestire variazioni di contrasto o offset di luminosità.
- Ma c'è ancora il problema della luminosità

### CCOEFF Normalizzato (CV_TM_CCOEFF_NORMED)

È la versione normalizzata del coefficiente di correlazione. In statistica, questo valore è noto come **coefficiente di correlazione di Pearson**.

$$R(y, x) = \frac{\sum_{i,j} T'(i,j) \cdot I'_{x,y}(y+i, x+j)}{\sqrt{\sum_{i,j} T'(i,j)^2 \cdot \sum_{i,j} I'_{x,y}(y+i, x+j)^2}}$$

- **Range:** Da **-1 a 1**.
    
- **Match Perfetto:** **1**.
    
- **Match Opposto (negativo):** **-1**.
    
- **Assenza di correlazione:** **0**.
    
- **Vantaggio:** È il metodo più accurato in condizioni di luce variabili, poiché è invariante sia alle variazioni lineari di luminosità che di contrasto.

# in OpenCV

``` Python
# Confronta T con tutte le posizioni (x,y) di I, risultato in R
R = cv.matchTemplate(I,T,metodo)

# Cerca il minimo e il massimo di R
r_min, r_max, pos_min, pos_max = cv.minMaxLoc(R)
```

