# Morfologia Matematica

È un'insieme di tecniche basate sulle operazioni sugli **insiemi**.

![[Pasted image 20260407142438.png]]

Serve a:
- migliorare la qualità di immagini binarie
- estrarre informazioni riguardante la forma di un certo oggetto

> [!important] Elemento Strutturante (SE)
> La morfologia matematica utilizza un **Elemento Strutturante (SE)** — una piccola immagine binaria quadrata dispari (3x3, 5x5, etc.) — da usare come **parametro** nelle operazioni.
## Operazioni Fondamentali

La maggior parte delle operazioni morfologiche si basano su due soli operatori:

- **Dilatazione ($\oplus$):** L'oggetto si espande. Un pixel diventa foreground se il SE, posizionato con il centro sul pixel selezionato, "tocca" almeno un pixel di $F$. Utile per chiudere piccoli buchi.
    
- **Erosione ($\ominus$):** L'oggetto si contrae. Un pixel sopravvive solo se il SE è interamente contenuto in $F$. Utile per rimuovere rumore (puntini isolati).

![[Pasted image 20260407141855.png|499]]
### in OpenCV

``` Python
img = cv.imread('esempi/labirinto.png', cv.IMREAD_GRAYSCALE)

# Elemento strutturante: quadrato 15x15
se = cv.getStructuringElement(cv.MORPH_RECT, (15,15))

d = cv.morphologyEx(img, cv.MORPH_DILATE, se)
e = cv.morphologyEx(img, cv.MORPH_ERODE, se)
```
![[Pasted image 20260407142832.png]]

``` Python
# Colora i pixel aggiunti dalla dilatazione
d1 = cv.cvtColor(d, cv.COLOR_GRAY2BGR)
d1[d!=img] = (0,255,0)

# Colora i pixel rimossi dall'erosione
e1 = cv.cvtColor(img, cv.COLOR_GRAY2BGR)
e1[e!=img] = (0,128,255)
```
![[Pasted image 20260407142845.png]]

### Operazioni Composte

|**Operazione**|**Formula**|**Effetto Principale**|
|---|---|---|
|**Apertura**|$(F \ominus S) \oplus S$|Rimuove piccoli oggetti e stacca connessioni sottili.|
|**Chiusura**|$(F \oplus S) \ominus S$|Chiude buchi interni e salda piccole interruzioni.|
![[Pasted image 20260407143032.png|357]]
> _nell'immagine l'SE:_
>- _dell'apertura è un po' più piccolo dei pallini dell'immagine_ 
>- _nella chiusura è leggermente più grande_

> [!attention] **Proprietà**
> 
> L'apertura e la chiusura sono **idempotenti**: applicarle una seconda volta con lo stesso SE non cambia ulteriormente l'immagine.

### Apertura e Chiusura in OpenCV
![[Pasted image 20260407143300.png]]

---