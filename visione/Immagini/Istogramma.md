### in un'immagine grayscale
Indica il numero di pixel dell'immagine per ciascuno dei 255 livello di grigio.

![[Pasted image 20260312160608.png]]
\[se nell'immagine ci sarà tanto bianco il grafico sarà alto sul bianco]

>[!info] A cosa serve?
> A facilitare l'analisi di alcuni aspetti dell'immagine presa in considerazione.
> _( Ad esempio se i colori sono condensati tutti in una zona si può capire che l'immagine ha uno scarso contrasto )_
> 

### identificazione di uno sfondo (esempio)

un istogramma **bimodale** (con due gruppi predominanti) può indicare la presenza di un oggetto abbastanza omogeneo su uno sfondo di luminosità pressoché costante.

![[Pasted image 20260312161324.png]]
## Calcolo dell'istogramma

``` Python
def calc_hist_py(img):
	h = np.zeros(256, dtype=int) # istrogramma come array np
	for p in np.nditer(img): # per tutti i pixel dell'immagine
		h[p] += 1
	return h
	
def calc_hist_np(img):
# istogramma standard di numpy, si specifica anche il range di valori
	return np.histogram(img,256,[0,256])[0] 
	
def calc_hist_cv(img):
	return cv.calcHist([img], [0], None, [256], [0, 256]).squeeze()
# ci sono altri modi ma con OpenCv è il più veloce
```

### Come sappiamo che quello è il più veloce?

``` Python
img = cv.imread('../immagine.jpg', cv.IMREAD_GRAYSCALE)
methods = [calc_hist_py, calc_hist_np, calc_hist_cv] # i metodi da comparare
# funzione per stampare gli istogrammi
va.show(*[(va.draw_hist(f(img), 98), f.__name__) for f in methods])
for f in methods:
	print(f.__name__, end=': ')
	%timeit f(img) # funzione jupiter per stampare l'esecuzione 
```
![[Pasted image 20260316095321.png]]

---
### Radice quadrata
spesso ai valori degli istogrammi viene applicata la **radice quadrata** per meglio visualizzare l'effetto delle varie operazioni

> [!info] Perchè?
> Avendo valori molto alti quelli più bassi diventerebbero invisibili, con la radice quadrata si attenua la differenza
