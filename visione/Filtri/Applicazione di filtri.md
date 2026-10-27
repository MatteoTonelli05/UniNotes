## Filtri in Python e OpenCV

### Implementazione con python 

> [!attention]  Estremamente lento a causa dei cicli: ogni operazione passa per l'interprete Python.

Vediamo l'applicazione di un filtro lineare a un'immagine con un solo canale
Il risultato (`res`) è memorizzato su un'immagine di interi con segno a 16 bit.

``` Python
def filter2D_py(img, f):
	h, w = img.shape
	kh, kw = f.shape
	dy, dx = kh//2, kw//2
	# copio l'immagine con un bordo
	imgb = cv.copyMakeBorder(img, dy, dy, dx, dx, cv.BORDER_DEFAULT)
	res = np.zeros(img.shape, dtype=np.int16)
	for y in range(h):
		for x in range(w):
			s = 0
			for i in range(kh):
				for j in range(kw):
					# N.B. sarebbe imgb[(dy+y)+(i-dy), (dx+x)+(j-dx)]
					s += imgb[y+i, x+j]*f[i, j]
			res[y, x] = s
	return res
```

### implementazione NumPy (slicing)

> [!attention]  Più veloce: le operazioni sulle matrici sono scritte in C.

``` Python
def filter2D_np(img, f):
	h, w = img.shape
	kh, kw = f.shape
	dy, dx = kh//2, kw//2
	imgb = cv.copyMakeBorder(img, dy, dy, dx, dx, cv.BORDER_DEFAULT)
	res = np.zeros(img.shape, dtype=np.int16)
	for y in range(h):
		for x in range(w):
			res[y, x] = (imgb[y:y+kh, x:x+kw]*f).sum()
	return res
```

### Implementazione con OpenCv

``` Python
def filter2D_cv(img, f):
	return cv.filter2D(img, cv.CV_16S, f)
	
# con un filtro 9x9 con tutti i coefficienti pari a uno. (BLUR)
img = cv.cvtColor(cv.imread('esempi/bolts.png'), cv.COLOR_BGR2GRAY)
f = np.ones((9,9))
filter2D_cv(img, f)
```

---

## Filtri separabili

> [!attention] Un filtro $m \times m$ richiede normalmente $m^2$ operazioni per ogni pixel
> $$
> I'[y,x]= \sum_{i=-d}^{d}\sum_{j=-d}^{d}F[i,j]\cdot I[y+i,x+j] \quad t.c. \quad d=\lfloor \frac{m}{2} \rfloor
> $$
> _La somma pesata ha $m^2$ moltiplicazioni e somme per ciascun pixel del risultato_

Se la matrice $F$ può essere ottenuta come prodotto di due vettori **($F=F_y \cdot F_x^T$)** il filtro si dice **separabile**:

![[Pasted image 20260330172238.png|203]]

Dunque si ha:
$$
I'[y,x]=\sum_i\sum_jF_y[j]\cdot F_x[j] \cdot I[y+i,x+j] = \sum_i F_y[i] \cdot \sum_j F_x[j] \cdot I[y+i,x+j]
$$
quindi
$$
I'[y,x]=\sum_{i=-d}^dF_y[i]\cdot T[y+i,x] \quad con \quad T[y,x]=\sum_{j=-d}^{d}F_x[j]\cdot I[y,x+j]
$$
si può quindi applicare il filtro $F_x$ a tutta l'immagine, ottenendo un'immagine intermedia $T$, quindi applicare $F_y$ per ottenere il risultato $I'$
### Implementazione con Python

> [!attention] 
> In questo e negli altri metodi i vettori kernel sono richiesti come parametri

``` Python
def sepFilter2D_py(img, fX, fY):
	fX, fY = fX.ravel(), fY.ravel() # si assicura che i kernel siano 1D.
	h, w = img.shape
	kw, kh = fX.size, fY.size
	dy, dx = kh//2, kw//2
	
'''
Quando il filtro passa sui bordi dell'immagine, "esce" dai limiti.`copyMakeBorder` aggiunge dei pixel extra attorno all'immagine originale per permettere al filtro di calcolare i valori anche sui pixel periferici senza causare errori.
'''
	imgb = cv.copyMakeBorder(img, 0, 0, dx, dx, cv.BORDER_DEFAULT)
	t = np.empty(img.shape, dtype=np.int16) # immagine intermedia
'''
Qui il codice scorre l'immagine riga per riga. Per ogni pixel, moltiplica i vicini orizzontali per i pesi del vettore `fX`
'''
	for y in range(h):
		for x in range(w):
			t[y, x] = sum(imgb[y, x+j]*fX[j] for j in range(kw))
	# applica un nuovo bordo all'immagine intermedia
	tb = cv.copyMakeBorder(t, dy, dy, 0, 0, cv.BORDER_DEFAULT)
	res = np.empty(img.shape, dtype=np.int16)
	for y in range(h):
		for x in range(w):
			res[y, x] = sum(tb[y+i, x]*fY[i] for i in range(kh))
	return res
```
### Implementazione con Numpy

``` Python
def sepFilter2D_np(img, fX, fY):
	fX, fY = fX.ravel(), fY.ravel()
	h, w = img.shape
	kw, kh = fX.size, fY.size
	dy, dx = kh//2, kw//2
	imgb = cv.copyMakeBorder(img, 0, 0, dx, dx, cv.BORDER_DEFAULT)
	t = np.empty(img.shape, dtype=np.int16)
	for y in range(h):
		for x in range(w):
			t[y, x] = (imgb[y, x:x+kw]*fX).sum()
	tb = cv.copyMakeBorder(t, dy, dy, 0, 0, cv.BORDER_DEFAULT)
	res = np.empty(img.shape, dtype=np.int16)
	for y in range(h):
		for x in range(w):
			res[y, x] = (tb[y:y+kh, x]*fY).sum()
	return res
```
### Implementazione con OpenCV

> [!info] 
> Con OpenCV è la soluzione più efficiente

``` Python
def sepFilter2D_cv(img, fx, fy):
	return cv.sepFilter2D(img, cv.CV_16S, fx, fy)
	
img = cv.cvtColor(cv.imread('esempi/bolts.png'), cv.COLOR_BGR2GRAY)

# filtro 9x9 con tutti i coefficienti pari a uno.
fx, fy = np.ones((9,1)), np.ones((1,9))
sepFilter2D_cv(img, fx, fy)
```
