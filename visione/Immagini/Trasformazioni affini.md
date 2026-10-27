Una trasformazione affine è una **trasformazione geometrica** che preserva l'allineamento dei punti (rette in rette) e il parallelismo, ma non necessariamente lunghezze e angoli
$$
g(x, y) =
\begin{pmatrix}
s_x \cos \theta & -s_y \sin \theta \\
s_x \sin \theta & s_y \cos \theta
\end{pmatrix}
\begin{pmatrix}
x \\
y
\end{pmatrix}
+
\begin{pmatrix}
t_x \\
t_y
\end{pmatrix}
$$
Tramite una trasformazione affine puoi fare tre cose principali:
- **ruota** di un angolo $\theta$  **rispetto all'origine**
	- _angolo positivo: verso orario_
	- _angolo negativo: verso anti-orario_
- applica un **fattore di scala** $s_x$/$s_y$ lungo gli assi $x$/$y$
- esegue una **traslazione** $t_x$/$t_y$ **pixel** lungo gli assi $x$/$y$

> [!info]
> più in generale, una trasformazione affine 2D può essere rappresentata come:
> $$
> \begin{pmatrix}
> x' \\
> y'
> \end{pmatrix}
> =
> \begin{pmatrix}
> a_{00} & a_{01} \\
> a_{10} & a_{11}
> \end{pmatrix}
> \begin{pmatrix}
> x \\
> y
> \end{pmatrix}
> +
> \begin{pmatrix}
> t_x \\
> t_y
> \end{pmatrix}
> $$

> [!attention] Problema
> Fare una moltiplicazione e poi una somma è "scomodo" se devi farlo per milioni di pixel. Sarebbe molto meglio fare **solo una moltiplicazione**.
## Matrice aumentata e coordinate omogenee

Aggiungendo una terza coordinata (sempre pari a 1) a tutti i vettori e aumentando la matrice con una nuova riga di zeri e una nuova colonna contenente il vettore traslazione alla **trasformazione affine 2D scritta sopra** si può riscrivere come:
$$
\begin{pmatrix}
x' \\
y' \\
1
\end{pmatrix}
=
\begin{pmatrix}
a_{00} & a_{01} & t_x \\
a_{10} & a_{11} & t_y \\
0 & 0 & 1
\end{pmatrix}
\begin{pmatrix}
x \\
y \\
1
\end{pmatrix}
$$

> [!info] Perchè aggiungendo l'1 non cambia il risultato?
> Immagina di avere un foglio 2D. Aggiungere la terza coordinata $z=1$ è come dire che il tuo foglio non è più "sul tavolo" (altezza 0), ma "fluttua" ad altezza 1. In questo spazio 3D, una **traslazione** (spostamento) può essere vista come una **inclinazione** (shear) dello spazio 3D. Quando guardi l'effetto finale sul tuo foglio ad altezza 1, vedi l'oggetto che si è semplicemente spostato.

### Trasformazioni affini in OpenCV

in OpenCV esiste la funzione `warpAffine()` che lavora con le matrici aumentate, ma sapendo che l'ultima riga è $[0 0 1]$ allora chiede solo la matrice :
$$
\mathbf{M} = \begin{bmatrix} a_{00} & a_{01} & t_x \\ a_{10} & a_{11} & t_y \end{bmatrix}
$$
``` Python
import math # per usare il pigreco ecc.
img = cv.imread('esempi/mario-c.png'); # carico l'immagine 
b = img.shape[0]//2; # metà altezza
bc = img[0,0].tolist() # è il colore del pixel in alto a destra (back_color)

# copio l'immagine con un bordo di b in tutti e 4 i lati, costante, con colore bc.
img = cv.copyMakeBorder(img, b, b, b, b, cv.BORDER_CONSTANT, value = bc);
h, w, _ = img.shape

trasf = lambda M: cv.warpAffine(//
img,// # sorgente
M,// # matrice di trasformazione
(w,h),// # dimensioni output
None,// # immagine di destinazione
cv.INTER_CUBIC,// # tipo di interpolazione
cv.BORDER_CONSTANT,// # modalità del bordo
bc// # colore del bordo
) 

# Traslazione di 9 pixel verso destra e 9 verso l'alto
M_T = np.array([ [1., 0., 9],
				 [0., 1., -9] ])
img_T = trasf(M_T)

# Riduzione di scala x e aumento y
M_S = np.array([ [0.7, 0.0, 0],
				 [0.0, 1.3, 0] ])
img_S = trasf(M_S)

# Rotazione di - π/8
θ = -math.pi/8; cos_θ, sin_θ = math.cos(θ), math.sin(θ)
M_R = np.array([ [cos_θ,-sin_θ, 0],
				 [sin_θ, cos_θ, 0] ])
img_R = trasf(M_R)
```
![[Pasted image 20260317143008.png]]

> [!attention] Problema?
> In OpenCV tutte le trasformazioni avvengono rispetto all'origine.
> Come faccio a trasformare rispetto ad un punto diverso dall'origine?
> 

### Trasformazioni da un punto diverso dall'origine

**In linea teorica:**
1. Traslare l'origine nel punto desiderato
``` Python
# Trasla il centro dell'immagine nell'origine
M_T1 = np.array([ [1., 0., -cx],
			      [0., 1., -cy],
				  [0., 0., 1]])
img_T1 = trasf(M_T1[:2])
```
2. Eseguire la trasformazione
``` Python
# Rotazione di π/4
θ = math.pi/4
cos_θ, sin_θ = math.cos(θ), math.sin(θ)
M_R = np.array([ [cos_θ,-sin_θ, 0],
[sin_θ, cos_θ, 0],
[0., 0., 1]])
img_R = trasf(M_R[:2])
```
3. Eseguire la traslazione inversa
``` Python
# Traslazione inversa
M_T2 = np.array([ [1., 0., cx],
				  [0., 1., cy],
				  [0., 0., 1]])
img_T2 = trasf(M_T2[:2])
```

> [!info] getRotationMatrix2D()
> In OpenCV è disponibile una funzione che restituisce direttamente la matrice a partire dalle coordinate del punto
> ``` Python
>import cv2
>cv2.getRotationMatrix2D(center, angle, scale)
>```
> 

### Trasformazione affine da coppie di punti corrispondenti

> [!info] Cosa significa?
> Significa far coincidere 3 punti di un'immagine a 3 punti di un'altra immagine

OpenCV mette a disposizione `getAffineTransform()` che, date tre coppie di punti corrispondenti, calcola la matrice di trasformazione affine.

``` Python
back = cv.imread('esempi/cake.png')
m = cv.imread('esempi/mario.png')
h, w = m.shape[:2]
m_pts = np.float32([[1,1],[w-2,1],[1,h-2]]) # punti di mario
c_pts = np.float32([ # punti per traslare (non ci sono tutti)
	[[ 68, 35],[106, 35],[ 68, 66]],
	# ... 3 punti per ogni quadrato nell'immagine
	[[ 4,116],[ 38,140],[ 4,163]] ])

''' # funzioni utili al printing delle immagini
def drawPoints(img, lp):
	for pts in lp:
		for i in range(3):
			p = tuple(pts[i].round().astype(int))
			cv.drawMarker(img, p, (255,0,0), i, 3)
	return img
	
back_points = drawPoints(back.copy(), c_pts)
m_points = drawPoints(m.copy(), [m_pts])
'''

for pts in c_pts:
	M = cv.getAffineTransform(m_pts,pts)
	cv.warpAffine(m, M, back.shape[1::-1], back,
		cv.INTER_LINEAR, cv.BORDER_TRANSPARENT)
```
![[Pasted image 20260317151800.png]]

> [!attention] Non è sempre possibile (ma quasi)

### Trasformazione proiettiva

> [!info] Cosa cambia dalle altre?
> A differenza delle trasformazioni affini, **non preserve il parallelismo fra rette**

$$\begin{bmatrix} x' \\ y' \end{bmatrix} = \begin{bmatrix} \frac{x_c}{w} \\ \frac{y_c}{w} \end{bmatrix}, \quad \text{con } w = \begin{cases} z_c & \text{se } z_c \neq 0 \\ \infty & \text{altrimenti} \end{cases}$$
$$\begin{bmatrix} x_c \\ y_c \\ z_c \end{bmatrix} = \mathbf{M}_P \begin{bmatrix} x \\ y \\ 1 \end{bmatrix}, \quad \mathbf{M}_P \in \mathbb{R}^{3 \times 3}$$

In OpenCV sono disponibili le funzioni:
- `getPrespectiveTransform()` che calcola la matrice $M_p$ a partire da quattro coppie di punti corrispondenti
- `warpPerspective()` che applica la trasformazione definita da una matrice $M_p$ a un'immagine, dopo aver invertito la trasformazione ottenendo $𝑓 = 𝑔^{-1}$ , con il tipo di interpolazione e la gestione dei bordi specificati.


``` Python
img = cv.imread('esempi/building.jpg')
pts1 = np.float32([[195,16], [466,183], [485,269], [181,293]])
w, h = 400, 120 # Dimensioni dell'immagine destinazione
pts2 = np.float32([[0,0], [w-1,0], [w-1, h-1], [0, h-1]])
Mp = cv.getPerspectiveTransform(pts1, pts2)
res = cv.warpPerspective(img, Mp, (w,h), flags = cv.INTER_CUBIC)
```
![[Pasted image 20260317153251.png]]
``` Python
m = cv.imread('esempi/sprite.png')
h, w = m.shape[:2] # Dimensioni di m
pts_m = np.float32([[0,0], [w-1,0], [w-1, h-1], [0, h-1]])
pts_b = np.float32([[243,111], [269,122], [273,220], [244,217]])
Mp = cv.getPerspectiveTransform(pts_m, pts_b)
res2 = cv.warpPerspective(m, Mp, img.shape[1::-1], img.copy(),
cv.INTER_LINEAR, cv.BORDER_TRANSPARENT)
```
![[Pasted image 20260317153305.png]]