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
## Operazioni aritmetiche fra immagini
### Differenza
La sottrazione dello "sfondo" può consentire di individuare gli oggetti di interesse

``` Python
img, back = cv.imread('esempi/mario-game.png'), cv.imread('esempi/mario-back.png')
diff = img - back # N.B. diff = cv.subtract(img, back) è più efficiente
mask = diff!=0
res = np.zeros_like(img)
res[mask] = img[mask]
```
![[Pasted image 20260316105203.png]]

### Operazioni Bitwise - Possibili utilizzi
Ritagliare il soggetto dell'immagine e incollarlo in un'altra immagine.
Data questa immagine:

![[Pasted image 20260316113111.png]]

``` Python
import random
# nonzero torna gli indici dei pixel che non sono zero
iy, ix = np.nonzero(res)[:2]
# ritagliamo tramite slicing il soggetto 
sprite = res[iy.min():iy.max()+1, ix.min():ix.max()+1]
sh, sw = sprite.shape[:2] # prendo le dimensioni del soggetto
mask = np.zeros_like(sprite)
# se rgb assieme mi danno zero lo faccio diventare bianco (asse 2 è la profondità)
mask[sprite.sum(axis = 2) == 0] = 255 
va.show(sprite, mask, enlarge_small_imags=False)
```
![[Pasted image 20260316113400.png]]

```Python
@interaxt(n=(0,99))
def genera(n=1):
	img = cv.imread('mario-landscape.jpg') # carico lo sfondo
	h, w = img.shape[:2] # prendo le dimensioni dello sfondo
	for i in range(n): # per il numero selezionato
		# genero la nuova posizione di mario
		x, y = random.randint(0, w-sw), random.randint(0, h-sh)
		# roi -> region of interest
		roi = img[y:y+sh, x:x+sw] # è slicing quindi è una vista
		roi &= mask # and con i valori a zero cancella tutto, si cancella lo spazio dove inserire lo sprite
		roi |= sprite # si inserisce lo sprite
	va.show(img)
```
![[Pasted image 20260316113841.png]]

---
## Alpha blending

 >[!attention] Aliasing
 > Un immagine incollata su un'altra si può facilmente definire (si vede il distacco).
 
 Per avere un risultato migliore si usa un ulteriore canale detto 'alpha' che regola la trasparenza.
 Quindi per copiare un' immagine incolliamo anche il canale di trasparenza, come?
 
 ``` Python
 img1 = cv.imread('esempi/hill.jpg')
 img2 = cv.imread('esempi/study.png')
 img2_alpha = cv.imread('esempi/study-alpha.png')
 h, w= img2.shape[:2]
 a = img2_alpha / 255 # Trasforma il canale alpha da [0,255] a [0,1]
 
 x, y = 200,100
 res = img1.copy()
 res[y:y+h,x:x+w] = img1[y:y+h,x:x+w]*(1.0-a) + img2*a
 ```
 
 ![[Pasted image 20260316133223.png]]
 
 