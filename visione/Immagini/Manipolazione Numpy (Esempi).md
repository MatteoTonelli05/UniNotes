## Manipolazione dei canali
``` Python
# Caricamento di un'immagine da file (formato BGR)
m = cv.imread('esempi/mario-c.png')
# Crea tre immagini BGR ciascuna con valori solo in un canale e gli altri due a zero
b, g, r = m.copy(), m.copy(), m.copy()
b[...,1:3], g[...,0:3:2], r[...,0:2] = 0, 0, 0
# Crea tre immagini BGR ciascuna corrispondente alla somma di due canali
c, m, y = b+g, b+r, g+r
```
![[Pasted image 20260312145619.png]]

---
## Reshaping dell'immagine

``` Python
t = cv.imread('immagini/toys.png')
```
![[Pasted image 20260312145956.png]]
``` Python
# Creo una tupla di immagini modificate
f = (t[::-1], t[:,::-1], t[::-1,::-1], t[...,::-1])
```
![[Pasted image 20260312145924.png]]
``` Python
# Prendo le dimensioni dell'immagine
h, w, c = t.shape
# Calcolo gli assi
h2, w2 = h//2, w//2
# Ritaglio l'immagine
s = (t[:,:w2], t[:h2], t[:h2,:w2], t[:h2,w2:], t[h2:,:w2], t[h2:,w2:])
```
![[Pasted image 20260312150109.png]]
``` Python
r = [t[::k,::k] for k in range(2,30,2)]
```
![[Pasted image 20260312150130.png]]

---