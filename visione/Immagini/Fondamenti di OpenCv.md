# Fondamenti di Immagini Digitali con Python e OpenCV

Un'immagine digitale è un'approssimazione discreta di una scena continua. È composta da un insieme di **pixel** (_picture elements_).

Ogni pixel rappresenta un'informazione di intensità luminosa che viene:
1. **Campionata**: misurata in una griglia spaziale finita (scelta della risoluzione).
2. **Quantizzata**: convertita in un valore numerico finito (scelta della profondità di bit).
## Caratteristiche Principali

- **Dimensione ($W \times H$):** Numero di pixel in larghezza (Width) e altezza (Height).
- **Risoluzione (DPI - Dots Per Inch):** Indica la densità dei pixel, fondamentale per la stampa ma meno per la pura elaborazione digitale.
- **Formato dei Pixel:**
    - _Bianco/Nero (Binario):_ 1 bit per pixel (0 o 1).
    - _Grayscale (Scala di grigi):_ 1 byte per pixel (valori 0-255).
    - _Colore:_ Solitamente 3 byte per pixel (RGB/BGR).
- **Occupazione di Memoria (Non compressa):**$$\text{Memoria} = W \times H \times \text{Depth}$$
    _( Dove Depth è il numero di bit per pixel )_

### Formati di Memorizzazione e Compressione

I file che salviamo su disco non sono quasi mai "matrici pure" perché occuperebbero troppo spazio. Esistono tre filosofie principali:

1. **BMP:** Formato "Raw", quasi nessuna compressione. File pesanti ma fedeli.
2. **PNG:** Compressione **Lossless** (senza perdita). Ideale per grafiche e loghi, supporta la trasparenza (canale Alpha).
3. **JPG:** Compressione **Lossy** (con perdita). Elimina dettagli che l'occhio umano fatica a percepire. Ideale per fotografie, pessimo per testi o bordi netti.

---
## Rappresentazione dell'Immagine Grayscale

Possiamo immaginare un'immagine in scala di grigi come una **matrice di punti di luce**.

![[Pasted image 20260312130546.png|645]]

- **Valori alti (es. 255 o 1.0):** Maggiore luminosità (Bianco).
- **Valori bassi (es. 0):** Minore luminosità (Nero).

> [!info] Perchè diciamo 255 o 1.0 ?
> Perchè ci sono due filosofie principali di rappresentazioni del dato:
> - `uint8`: $[0, 255]$ (Standard per file e visualizzazione).
> - `float32`: $[0.0, 1.0]$ (Standard per calcoli matematici e filtri).

---

## Coordinate e Organizzazione in Memoria

Esistono due modi per riferirsi a un pixel, ed è qui che spesso nascono errori:

1. **Notazione Cartesiana (OpenCV):** `(x, y)`. Usata nelle funzioni di disegno come `cv.line` o `cv.circle`.
2. **Notazione Matriciale (NumPy):** `[row, col]` che corrisponde a `[y, x]`.

**Esempio:**
``` Python
cv.line(img, (4,0), (3,6), color) # (x,y) -> dal punto x=4,y=0 al punto x=3,y=6
pixel = img[3, 1]                # [y,x] -> riga 3, colonna 1
```
![[Pasted image 20260312143859.png]]
### Canali e Ordinamento

In un'immagine a colori, il terzo valore è l'indice del canale.
- **RGB:** Standard universale (Red=0, Green=1, Blue=2).
- **BGR:** Standard di **OpenCV**. Storicamente scelto per compatibilità con i vecchi standard hardware.
- **Layout in memoria:** Solitamente di tipo **Interleaved** (`BGRBGRBGR...`), _dove i valori dei tre colori per un singolo pixel sono adiacenti in memoria._

![[Pasted image 20260312144826.png]]

---

## Immagini a Colori: Tensori 3D

Nelle immagini a colori, aggiungiamo una dimensione: l'altezza del "pacchetto" di matrici.

### Il Modello Additivo RGB

Ogni colore è la somma di tre componenti. Se tutti i canali sono al massimo (255, 255, 255), otteniamo il **Bianco**. Se sono a 0, il **Nero**.

- **Perché lo spazio RGB è limitato?** È ottimo per gli schermi, ma non è intuitivo. Se vogliamo rendere un colore "più scuro" o "meno saturo", dobbiamo cambiare tutti e tre i valori R, G e B in modo proporzionale, il che è complesso matematicamente.
![[Pasted image 20260312152020.png]]
---

## Spazi Colore Alternativi: HSL / HSV

Quando l'RGB non basta, convertiamo l'immagine in altri spazi. I più comuni sono **HSV** (Hue, Saturation, Value) o **HSL** (Hue, Saturation, Lightness).
![[Pasted image 20260312152058.png]]
- **Hue (Tonalità):** Rappresenta il "colore" puro (es. Rosso, Verde, Blu). In OpenCV è espresso solitamente in gradi $[0, 179]$ per stare in un byte.
- **Saturation (Saturazione):** Quanto il colore è "vivo" o tendente al grigio.
- **Value/Lightness (Brillantezza):** Quanto il colore è luminoso o scuro.

> [!info] Vantaggio
> Per isolare un oggetto di un certo colore (es. "trova tutte le palline rosse"), nello spazio HSV basta guardare il canale **Hue**, ignorando se la pallina è in ombra o in luce.

![[Pasted image 20260312153911.png]]
### Conversione in OpenCV

- si usa **cvtColor** per convertire da RGB a HSV/HSL e viceversa: 
	- COLOR_HSV2BGR, COLOR_BGR2HSV
	- COLOR_HLS2BGR, COLOR_BGR2HLS

> [!attention]
> in OpenCV l'ordine di HSL è (H=0, L=1, S=2)
>Intervalli di valori per immagini di byte:
>- H: \[0..179], corrispondente a \[0..2$\pi$]
>- S, V/L: \[0..255], corrispondente a \[0..1]

> [!info]
> Per visualizzare o salvare le immagini, è necessario convertirle in formato **BGR**

``` Python
originale = cv.imread('immagini/toys.png')
# Converte in HLS
hls = cv.cvtColor(originale, cv.COLOR_BGR2HLS)
# Separa i tre canali in 3 immagini grayscale
h, l, s = cv.split(hls)
# Modifica alcuni dei valori HLS
# Dimezza la luminosità di tutti i pixel
l1 = l//2
# Valore di saturazione 64 per tutti i pixel
s1 = np.full_like(s, 64)
# Riunisce i canali e converte in BGR
risultato = cv.cvtColor(cv.merge((h, l1, s1)),
cv.COLOR_HLS2BGR)
```
![[Pasted image 20260312155113.png]]

``` Python
s = [0, 64, 128, 192, 255] # 5 livelli di saturazione
# 18 valori di luminosità in ogni colonna
v = np.tile(np.arange(255, -1, -15, np.uint8)[:,np.newaxis], (1, 18))
# 18 valori di hue in ogni riga
h = np.tile(np.arange(0, 180, 10, np.uint8), (18, 1))
# Combina i 3 canali (si poteva usare anche cv.merge)
hsv = [np.dstack((h, np.full_like(h, x), v)) for x in s]
hls = [i[...,[0,2,1]] for i in hsv] # Scambia gli ultimi due canali
```
![[Pasted image 20260312155332.png]]


