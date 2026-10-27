## Operazioni sui pixel

le operazioni sui pixel sono applicazioni di una funzione su ciascun pixel di un'immagine.

> [!info] Su una singola immagine:
> $$I'[y,x] = f(I[y,x])$$
> Ogni pixel dell'immagine di uscita è una funzione solo del corrispondente pixel dell'immagine di input
>
_[Esempi: variazione della luminosità/contrasto]_

> [!info] Su più immagini:
> $$I[y,x] = f(I_1[y,x], I_2[y,x], \ldots)$$
> Ogni pixel dell’immagine di uscita è funzione solo dei corrispondenti pixel delle immagini di input.
> 
> _[Esempi: operazioni aritmetiche fra immagini: somma, sottrazione, AND, OR, XOR, ...]_

---
## Su una singola immagine
### Variazione luminosità e contrasto

La tipica funzione **lineare** per cambiare luminosità e contrasto dei pixel è
$$f(v)=\alpha * v + \beta$$
- $v$ è il valore del pixel
- $\alpha$ controlla il contrasto
- $\beta$ controlla la luminosità

>[!attention] I valori dei pixel devono appartenere a $[0,255]$
> I valori fengono forzati / tagliati in quell'insieme

![[Pasted image 20260316101416.png]]

---
### Gamma correction

La tipica funzione **lineare** per cambiare luminosità e contrasto dei pixel è
$$
f(v) = \left(\frac{v}{255}\right)^{\gamma} \cdot 255
$$
- $\gamma < 1$: aumenta la luminosità dei toni scuri
- $\gamma > 1$: diminuisce la luminosità dei toni scuri
  ![[Pasted image 20260316102543.png]]
---
## Lookup Table (LUT)

> [!attention] Problema
> In un'immagine spesso dobbiamo ripetere le stesse operazioni su pixel con valori identici, sprecando risorse importanti.

> [!info] Soluzione
> Se i valori dei pixel sono un insieme limitato allora si può tenere in memoria una **LUT** per tener traccia delle associazioni $v:f(v)$
> 

``` Python
# Un esempio di funzione f (Gamma correction per un certo valore di 𝛾)
𝛾 = 0.5
# Calcolo di un singolo valore di f
f = lambda p: 255 * (p/255.0)**𝛾

# Calcolo di f su tutti i valori di un array NumPy (ricorda Broadcasting)
f_np = lambda a: f(a).astype(np.uint8)
# Calcolo dell'array LUT
lut = f_np(np.arange(256))

# Semplice implementazione Python con LUT
def applica_py_lut(img):
	res = np.empty_like(img)
	h, w = res.shape
	for y in range(h):
		for x in range(w):
			res[y,x] = lut[img[y,x]]
	return res
	
# Implementazione NumPy con LUT
def applica_np_lut(img):
	return lut[img] # indicizzazione con array di interi
	
# Funzione LUT di OpenCV
def applica_cv_lut(img):
	return cv.LUT(img, lut)
```

### LUT da grayscale a RGB

OpenCV mette a disposizione una funzione apposita (`applyColorMap`) e anche una serie di mappe di colori già pronte all'uso

![[Pasted image 20260316104410.png]]

Serve per rendere alcuni dettagli meglio visibili all'**utente umano**.

## Binarizzazione...
### ...su un'immagine grayscale

Per eseguire questa operazione è necessario selezionare una soglia, che definirà quali colori devono diventare **bianco** e quali **nero**.
$$
f(v,t) =
\begin{cases}
0 & v < t \\
255 & v \ge t
\end{cases}
$$
**Come si sceglie la soglia?**
- Manualmente
- Osservando l'istogramma
- **Metodo di Otsu**:  in automatico cerca sull'istogramma la soglia che minimizza la varianza intra-classe dell'intensità dei pixel delle due classi (foreground e background) determinate dalla soglia stessa.
  (il metodo di otsu è quella usata in OpenCv)

> [!attention] Problema
> Ci sono delle immagini con determinate luci che non permettono al Metodo di Otsu di funzionare correttamente.
> ![[Pasted image 20260316140702.png]]

> [!info] Soluzione
> Si usa una **Soglia Locale (o adattiva)** per ogni pixel.
> Si calcola la soglia come media dei pixel nella regione, meno un valore costante
> ![[Pasted image 20260316141656.png]]
> 

### Binarizzare in OpenCv (greyscale)

``` Python
# Sogli globale (128)
img = cv.imread('immagine.png', cv.IMREAD_GRAYSCALE)
_, res = cv.threshold(img, 128, 255, cv.THRESH_BINARY)
va.show(img, res)

# Soglia globale determinata dall'algoritmo di Otsu
img = cv.imread('immagine.png', cv.IMREAD_GRAYSCALE)
t, res = cv.threshold(img, -1, 255, cv.THRESH_BINARY)
va.show(img, res)

# Soglia locale (media su intorno 11x11 meno il valore 10)
img = cv.imread('immagine.png', cv.IMREAD_GRAYSCALE)
t, res = cv.adaptiveThreshold(img, 255, cv.ADAPTIVE_THRESH_MEAN_C, cv.THRESH_BINARY, 11, 10)
va.show(img, res)
```
### ...su un'immagine RGB

 La binarizzazione avviene su un canale solo, dunque dobbiamo ragionare in modo a
 
``` Python
img = cv.imread('immagine.jpg')
hls = cv.cvtColor(img, cv.COLOR_BGR2HLS) # converto in HLS

# hls[...,2] prende il canale di saturazione
# noi binarizziamo il canale saturazione
_,hls[...,2] = cv.threshold(hls[...,2], 150, 255, cv.THRESH_BINARY)

res = cv.cvtColor(hls, cv.COLOR_HLS2BGR)
```
![[Pasted image 20260316143802.png]]

## Contrast stretching
Il **contrast stretching** serve ad **aumentare il contrasto** di un’immagine espandendo i livelli di grigio.

### Mapping Lineare
Si applica una trasformazione lineare ai pixel:

> [!attention] Problema
> Se i pixel dell’immagine usano solo una piccola parte del range $[0,255]$, l’immagine appare **piatta e poco contrastata**.

$$f(I(y,x))=255⋅\frac{(I(y,x)−\alpha)}{\beta-\alpha}​$$
dove:
- $I(y,x)$ = intensità del pixel
- $\alpha$ = valore minimo da cui iniziare lo stretching
- $\beta$ = valore massimo
- $output ∈ [0,255]$

```Python
def contrast_stretching(img, a, b):
	# Converte in floating point e applica la funzione di mapping
	d = b-a if b!=a else 1
	n = 255 * (img.astype(float)-a) / d
	
	# Forza il range [0,255] e converte in byte
	return np.clip(n, 0, 255).astype(np.uint8)
```

> [!attention] Problema
> un solo pixel pari a 0 e uno pari a 255 rende inutile l'operazione, quindi dobbiamo scegliere $\alpha$ e $\beta$ in modo intelligente
> _(non basta usare il minimo e massimo livello di grigio dell'immagine)_

### Percentili dell'istogramma
il **percentile** è una misura statistica che indica il valore al di sotto del quale ricade una determinata percentuale di osservazioni in un insieme di dati ordinati.

> [!info] **Definizione:**
> Se un valore si trova al 75-esimo percentile significa che il 75% dei soggetti ha un valore inferiore o uguale, mentre il 25% ha un valore superiore.

vengono scelti come $\alpha$ e $\beta$ il 5° e il 95° percentile

**In pratica:**
- **5% pixel più scuri → compressi a 0**
- **5% pixel più chiari → compressi a 255**
![[Pasted image 20260316153229.png]]

> [!attention] Non c'è un metodo OpenCV per farlo direttamente

``` Python
def contrast_stretching(img, a, b):
	# Converte in floating point e applica la funzione di mapping
	d = b-a if b!=a else 1
	n = 255 * (img.astype(float)-a) / d
	# Forza il range [0,255] e converte in byte
	return np.clip(n, 0, 255).astype(np.uint8)
	
img = cv.imread('immagini/rice.png', cv.IMREAD_GRAYSCALE)

# Contrast stretching con 𝛼=𝑚𝑖𝑛(I) e 𝛽=𝑚𝑎𝑥(I)
res = contrast_stretching(img, img.min(), img.max())

img = cv.imread('esempi/kernel.png', cv.IMREAD_GRAYSCALE)

# Contrast stretching con 𝛼=𝑃_5(I) e 𝛽=𝑃_95(I)
res = contrast_stretching(img, np.percentile(img, 5), np.percentile(img, 95))
```

## Equalizzazione...
### ...dell'Istogramma
Metodo molto usato per migliorare il contrasto e per rendere **comparabili immagini con illuminazione diversa**.
$$f(v) = \sum_{i=0}^{v} H_i$$
con $H$ istogramma dell'immagine normalizzato:
$$H[i]=\frac{255⋅h[i]}{\sum_{}^{} h[i]}=>\sum_{i=0}^{255} H[i] = 255$$

> [!info] Definizione
> Distribuire l'istogramma **uniformemente** sui livelli di grigio

![[Pasted image 20260316154151.png|299]]
### ...delle immagini a colori

>[!info] Non bisogna equalizzare separatamente i canali RGB.
>**Perchè?**
>Cambierebbe i rapporti tra i colori, portando a colori irreali.

**Come fare allora?**

``` Python
# convertire in HLS
hls = cv.cvtColor(bgr, cv.COLOR_BGR2HLS)
h, l, s = cv.split(hls)
# equalizzare **solo la luminosità L
l_eq = cv.equalizeHist(l)

hls_eq = cv.merge((h, l_eq, s))
# tornare in RGB
res_ok = cv.cvtColor(hls_eq, cv.COLOR_HLS2BGR)
```

