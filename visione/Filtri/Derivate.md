# Derivata di una funzione discreta

> [!info] Definizione di derivata
> Una derivata è un limite del rapporto incrementale:
$$f'(x)=\lim_{t \to 0}\frac{f(x+t)-f(x)}{t}$$

Immagini digitali: i valori sono **discreti**, non è possibile calcolare esattamente il limite, ma si può **stimare** per un valore piccolo di $𝑡$, perciò si usa la stima mediante differenza finita "centrata".
$$f'(x)\approx\frac{f(x+1)-f(x-1)}{2}$$
![[Pasted image 20260401175343.png|645]]

## Derivate parziali

> [!attention] L'immagine è una funzione discreta di due variabili

Quindi bisogna stimare due derivate parziali di $f(x,y)$:

$$\frac{\partial f(x, y)}{\partial x} \approx \frac{f(x + 1, y) - f(x - 1, y)}{2}$$
$$\frac{\partial f(x, y)}{\partial y} \approx \frac{f(x, y + 1) - f(x, y - 1)}{2}$$

![[Pasted image 20260401180845.png|293]]

---
### Filtro per calcolare la derivata

> [!info] Idea
> Creaiamo un filtro che calcoli la derivata parziale, il filtro $[-1,0,1]$, che quindi calcola la differenza tra il punto prima e quello dopo (poi normalizzando tutto) 
> 
> ![[Pasted image 20260401181059.png]]

![[Pasted image 20260401181808.png]]

![[Pasted image 20260401181750.png]]

### **Problema**: rumore nelle immagini

> [!attention] Il rumore viene amplificato dalla derivata

 Per evitarlo, si applica un filtro di **Smooth ($S$)** prima della **Derivata ($D$)**.
Grazie alla proprietà associativa della convoluzione, si crea un unico filtro $F$: $$I' = (I * S) * D = I * (S * D)$$quindi
$$ I'=I*F, \quad con \quad F = S * D$$
In questo modo, otterremo un profilo decisamente più lineare e privo di interferenze.

![[Pasted image 20260402162919.png|357]]

### La direzione di derivazione ($x$) e quella di smoothing (y)

> [!info] Idea
> La direzione di smoothing è **ortogonale** a quella di derivazione

Per trovare un bordo verticale, devi guardare come cambia l'intensità muovendoti da sinistra a destra (lungo l'asse **$x$**).

Applicando lo **smooth in verticale** ($y$), correggeremo dunque il rumore sui bordi verticali.

![[Pasted image 20260402165524.png]]

---
## I principali filtri derivativi 3x3

![[Pasted image 20260402173114.png]]

**Note:**
- Sono stati ottenuti come filtri di **convoluzione** ($∗$): per utilizzarli con la correlazione ($\otimes$) è sufficiente ribaltarli sugli assi
- Sono tutti filtri separabili

### in OpenCV

``` Python
def derivata(img, t, d):
	# è praticamente un dizionario usato come switch (sceglie il metodo in base a t)
	s = {'Prewitt': [1,1,1], 'Sobel': [1,2,1], 'Scharr': [3,10,3]}[t]
	D = np.float32([-1,0,1])/2
	S = np.float32(s)/np.float32(s).sum()
	fx, fy = (D, S) if d=='x' else (S, D)
	return cv.sepFilter2D(img, cv.CV_32F, fx, fy)
	
img = cv.imread('esempi/cat.jpg', flags=cv.IMREAD_GRAYSCALE)
res = [(derivata(img,t,d), f'{t} ({d})') # deriviamo l'immagine
	for t in ('Prewitt', 'Sobel', 'Scharr') # per ogni metodo
		for d in ('x', 'y')] # in entrambe le direzione
```
![[Pasted image 20260402174111.png]]

> [!attention] Esistono già le funzioni più ottimizzate
> - Per applicare i filtri di Sobel e Scharr utilizzare `cv.Sobel()` e `cv.Scharr()`
> - Si possono ottenere i filtri di Sobel e Scharr con `cv.getDerivKernels()`
> - Infine `cv.spatialGradient()` restituisce entrambe le derivate parziali calcolate con i filtri di Sobel non normalizzati
