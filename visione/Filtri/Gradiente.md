Il **gradiente** di un'immagine in un punto $(x,y)$ è il vettore che ha per componenti le due derivate parziali:
$$\nabla \mathbf{I}[y, x] = \left[ \frac{\partial \mathbf{I}}{\partial x}[y, x], \frac{\partial \mathbf{I}}{\partial y}[y, x] \right]^\top$$

> [!info] In pratica
> Indica la **direzione di maggior variazione** dell'immagine nel punto $(x,y)$
> 
![[Pasted image 20260402175437.png]]

## Modulo e orientazione del gradiente

Sia $\nabla = [\nabla_x,\nabla_y]^\top$ il gradiente nel punto $(x,y)$ possiamo trovare
	
- **Angolo del gradiente**: $\theta=atan2(\nabla_x,\nabla_y)$
	
- **Modulo del gradiente:** $|\nabla|=\sqrt{\nabla_x^2+\nabla_y^2}$

> [!attention] L'angolo del gradiente è misurato in **senso orario**
> ![[Pasted image 20260402180502.png]]

### in OpenCV

``` Python
# trasformiamo in grayscale perchè queste operazioni si fanno sulla luminosità
img = cv.imread('esempi/cat.jpg', flags=cv.IMREAD_GRAYSCALE)
img = cv.GaussianBlur(img, (3,3), 0) # si usa un blur per diminuire il rumore

# Calcolo derivate parziali tramite sobel (derivate secondo x/y)
dx,dy = cv.Sobel(img,cv.CV_32F,1,0,scale=1/8),
		cv.Sobel(img,cv.CV_32F,0,1,scale=1/8)
		
# Calcola il modulo del gradiente in ogni pixel
mod = cv.magnitude(dx, dy) 
# Calcola l'angolo del gradiente in ogni pixel 
# (selezionato in gradi, di norma è radianti)
ang = cv.phase(dx, dy, angleInDegrees=True) 

# Disegna e stampa i dati del gradiente in un punto
x, y = 127, 116
w, h = 35, 25
scale, mult = 11, 1/4
roi = img[y-h//2:y+h//2, x-w//2:x+w//2]
# roi_large è l'immagine ingrandita dei pixel in roi
px, py = w//2, h//2
p1 = (px*scale+scale//2, py*scale+scale//2)
p2 = (int(round((px+dx[y,x]*mult)*scale)),
	  int(round((py+dy[y,x]*mult)*scale)))
cv.arrowedLine(roi_large, p1, p2, (240,176,0), 3, cv.LINE_AA)
print(f'Gradiente in ({x},{y}): ∇x={dx[y,x]:.1f}, ∇y={dy[y,x]:.1f}, ' +
f'𝜃={ang[y,x]:.0f}°, |∇|={mod[y,x]:.2f}')
```
![[Pasted image 20260403193033.png]]
## Precisione nella stima dell'angolo
### Dimostrazione pratica della precisione di Scharr

1) Generiamo un'immagine sintetica che segue la funzione $f(x,y)=sin(x^2+y^2)$
	-**Perchè?** Perchè così possiamo trovare facilmente le derivate parziali: 
	$$
	\frac{\partial f}{\partial x}(x,y)=2x\cdot cos(x^2+y^2) \qquad \frac{\partial f}{\partial y}(x,y)=2y\cdot cos(x^2+y^2)
	$$
2) In questo modo posso calcolare l'errore di ogni singolo metodo
$$Err = Risultato_{formula} + Risultato_{stima}$$

![[Pasted image 20260402185238.png]]
_L'errore è mostrato come un istogramma che mostra gli errori per ogni pixel di un certo valore_

## A cosa serve il gradiente?

> [!important] Proprietà Fondamentali
> 1) Il gradiente avrà **modulo** maggiore in prossimità dei bordi
> 2) Il **bordo** (edge) di un oggetto è sempre **ortogonale** (perpendicolare) alla direzione del vettore gradiente.
> 
> ![[Pasted image 20260403185316.png]]

## Visualizzare modulo e angolo del gradiente in OpenCV

``` Python
img = cv.imread('esempi/cat.jpg', flags=cv.IMREAD_GRAYSCALE)
dx, dy = cv.spatialGradient(img) # mi da tramite Sobel le due derivate
dx, dy = dx.astype(np.float32), dy.astype(np.float32) # converto come float
# Calcola il modulo del gradiente per ciascun pixel 
m = cv.magnitude(dx, dy) 
# Calcola l'angolo del gradiente per ciascun pixel (in radianti) 
a = cv.phase(dx, dy) 
# Normalizzo i moduli nel range [0,255] 
m = cv.normalize(m, None, 0, 255, cv.NORM_MINMAX, cv.CV_8U) 
# Converte gli angoli da [0,2pi] a [0,255] 
a = (a/(2*np.pi)*255).round().astype(np.uint8) # usa NumPy
# Uso una mappa di colori per associare gli angoli a un colore
a = cv.applyColorMap(a, cv.COLORMAP_HSV) # Angolo -> Hue 
```
![[Pasted image 20260403230722.png|237]]
``` Python
# Visualizza solo i gradienti con modulo superiore a una soglia 
# Quelli minori alla soglia li setto a 0
t = 40
a1 = a.copy(); a1[m<t] = 0
m1 = m.copy(); m1[m<t] = 0
```
![[Pasted image 20260403230539.png]]

> [!info] Idea
> Potrebbe essere un ottimo modo per trovare i bordi di un'immagine

> [!attention] Problemi
> - **frammenti** di bordi non connessi
> - **bordi spuri** (dovuti al rumore)
> - bordi più **spessi** di un pixel
> 
> ![[Pasted image 20260403231538.png]]
