# Applicazioni dei filtri lineari
## Box filter
Il **Box Filter** è un filtro lineare rappresentato da una matrice quadrata i cui elementi sono tutti uguali a $1$. Per evitare che l'intensità dell'immagine aumenti drasticamente, il filtro viene **normalizzato** dividendo ogni coefficiente per il numero totale di elementi del kernel ($m \times m$):

$$\mathbf{F}_{box} = \frac{1}{m^2} \begin{bmatrix} 1 & 1 & \dots & 1 \\ 1 & 1 & \dots & 1 \\ \vdots & \vdots & \ddots & \vdots \\ 1 & 1 & \dots & 1 \end{bmatrix}$$

L'effetto principale è il **blur** (sfocatura), che attenua il contrasto locale e riduce il rumore ad alta frequenza agendo come un filtro passa-basso.

---

### Separabilità del Box Filter

Il Box Filter è un **filtro separabile**. Ciò significa che la matrice 2D può essere scomposta nel prodotto esterno di due vettori 1D (uno riga e uno colonna) composti da soli $1$:

$$\mathbf{F}_{box} = \mathbf{f}_{col} \cdot \mathbf{f}_{row} = \frac{1}{m} \begin{bmatrix} 1 \\ 1 \\ \vdots \\ 1 \end{bmatrix} \cdot \frac{1}{m} \begin{bmatrix} 1 & 1 & \dots & 1 \end{bmatrix}$$

In **OpenCV**, questa proprietà permette di ottimizzare il calcolo:

- `cv.filter2D`: Applica la convoluzione 2D standard (complessità $O(m^2)$).
    
- `cv.sepFilter2D`: Applica prima il filtro 1D orizzontale e poi quello verticale (complessità $O(m)$).
    
- `cv.boxFilter`: Implementazione specifica ancora più efficiente che sfrutta la tecnica delle **immagini integrali**.
    

---

### Immagini a Colori

Quando si applica un filtro a un'immagine multicanale (RGB/BGR), OpenCV esegue l'operazione in modo indipendente su ogni canale:

$$\mathbf{I}'_{channel} = \mathbf{I}_{channel} \otimes \mathbf{F} \quad \text{per } channel \in \{R, G, B\}$$

Il risultato finale è la ricomposizione dei tre canali processati singolarmente.

``` Python
# Nelle immagini a colori, OpenCV applica lo stesso filtro a ciascun canale
img = cv.imread('esempi/mario-c.png')

# Box filter con normalizzazione
smoothed = cv.boxFilter(img, -1, (7,7), normalize = True)

# La funzione cv.blur eqivale a chiamare boxFilter con normalize=True
test = [cv.blur(img, (sx, sy)) for sx in range(1, 10, 2) for sy in range(1, 10, 2)]
```

> [!attention]
> All'aumentare delle dimensioni della matrice filtro si ottiene un'immagine sempre più sfocata

## Gaussian Blur

il Box filter è il filtro più semplice ed efficiente, ottimizzato particolarmente da OpenCV.

> [!attention] Problema
> Produce artefatti "quadrati". Poichè dà lo stesso peso ai pixel centrali e a quelli lontani, la sfocatura non è naturale e può creare scie rettangolari.

> [!info] Soluzione
> Creaiamo in cui i pesi sono massimi al centro e diminuiscono esponenzialmente verso i bordi.

Ci basiamo su gauss in 2 dimensioni $(x,y)$
$$G_{2d}(x,y)=\frac{1}{2\pi \sigma^2}e^{-\frac{x^2+y^2}{2\sigma^2}}= \left( \frac{1}{\sigma\sqrt{2\pi}} \right)^2 e^{-\frac{x^2}{2\sigma^2}} e^{-\frac{y^2}{2\sigma^2}} = G_{1D}(x) \cdot G_{1D}(y)$$
con $G_{1D}(x)=\frac{1}{\sigma\sqrt{2\pi}}e^-\frac{x^2}{2\sigma^2}$ _(ovvero gauss in una dimensione)_

![[Pasted image 20260331125050.png]]

In termini di matrici (o kernel), questo significa che un kernel gaussiano $G$ di dimensione $m \times m$ può essere ottenuto come il **prodotto esterno** di un vettore colonna per un vettore riga.

> [!info] 
> Abbiamo la fortuna che il filtro è simmetrico, quindi i due vettori sono identici e trasposti
> 
> ![[Pasted image 20260401142706.png|505]]

> [!attention] I parametri sono:
> - $m$ che è la lunghezza del lato della matrice (**è quadrata**)
> - $\sigma$ che è il parametro fondamentale che controlla la "larghezza" della campana gaussiana, quindi determina **quanto è intensa la sfocatura (blur)**.

### in Python
``` Python
# Leggo l'immagine
img = cv.imread('esempi/study.png')

# Crea un filtro gaussiano 1D e lo passa a sepFilter2D
g = cv.getGaussianKernel(5, 1) # m=5, σ=1
img_blurred = cv.sepFilter2D(img, -1, g, g)

# Ancora più semplice utilizzare direttamente l'apposita funzione GaussianBlur
img_blurred2 = cv.GaussianBlur(img, (5,5), 1) # m=5, σ=1
# N.B. se σ<=0, la funzione calcola σ da m come σ = 0.3*((m-1)*0.5 - 1) + 0.8
list_img_blurred = [cv.GaussianBlur(img, (m,m), 0) for m in range(3,17,2)]
```
![[Pasted image 20260401152437.png]]

## Sharpening

> [!info] Cos'è?
> "è un insieme di tecniche finalizzate a migliorare i dettagli, definire i bordi e correggere sfocature o mosso nelle immagini digitali."
> 
> **Pro:** Aumenta il contrasto
> **Contro:** Aumenta il rumore

1) Si procede calcolando un'immagine "blurred" mediante **convoluzione con un filtro $F_b$ normalizzato** (es. box filter o gaussiano) 

![[Pasted image 20260401153152.png]]

2) Si sottrae l'immagine "blurrata" da quella originale

![[Pasted image 20260401153310.png|430]]

3) L'immagine 𝐌 (pixel con valori interi) è infine sommata all'immagine originale, dopo essere stata moltiplicata per un parametro 𝑘 che controlla l'intensità dell'effetto

![[Pasted image 20260401153345.png|431]]

---
### Derivazione del Filtro di Sharpening ($F_s$)

>[!attention] Problema
>Tutte queste operazioni costano troppe risorse, dobbiamo quindi costruire un singolo filtro che esegua l'intera operazione di **sharpening**

Definiamo alcune variabili:
- $I$ l'immagine originale
- $F_b$ un filtro di sfocatura (blur)
- $F_{id}$ il filtro identità (con un solo 1 al centro)
- $k$ un coefficiente che controlla l'intensità dello sharpening.

$$\begin{aligned} \text{1. Maschera di contrasto (Unsharp Mask):} \quad & M = I - I * F_b \\ & M = I * F_{id} + I * (-F_b) \\ \text{2. Proprietà distributiva:} \quad & M = I * (F_{id} - F_b) \\ & \mathbf{M = I * F_L}, \quad \text{con } F_L = F_{id} - F_b \\ \\ \text{3. Risultato dello sharpening:} \quad & I' = I + k \cdot M \\ & I' = I + k \cdot (I * F_L) \\ \text{4. Proprietà associativa e commutativa:} \quad & I' = I + I * (k \cdot F_L) \\ \text{5. Applicazione finale della distributiva:} \quad & I' = I * F_{id} + I * (k \cdot F_L) \\ & I' = I * (F_{id} + k \cdot F_L) \\ \\ \text{6. Definizione del filtro unico } F_s: \quad & \mathbf{I' = I * F_s} \end{aligned}$$

>[!info] **Formula Finale del Filtro**
> 
> $$\Large F_s = F_{id} + k \cdot (F_{id} - F_b)$$
> il filtro dovrà essere applicato direttamente sull'immagine originale: $I'=I*F_s$
### Esempio
![[Pasted image 20260401155020.png]]

## in OpenCV

``` Python
# Metodo 1: filtro di blur e operazioni aritmetiche fra immagini
def sharpen(img, gaussian_blur, m, k):
	blurred = cv.GaussianBlur(img, (m,m), 0) if gaussian_blur else cv.blur(img, (m,m))
	mask = img.astype(np.int16) - blurred
	return np.clip(np.round(img.astype(float)+k*mask), 0, 255).astype(np.uint8)
	
# Metodo 2: creazione di un unico filtro che fa tutto
def create_sharpen_filter(gaussian_blur, m, k):
	if gaussian_blur:
		g = cv.getGaussianKernel(m, 0); F_b = g.reshape(1,-1)*g
	else:
		F_b = np.ones((m,m), np.float32)
	F_id = np.zeros_like(F_b); F_id[m//2, m//2] = 1
	F_b /= F_b.sum() # Normalizza il filtro di blur
	return F_id + k*(F_id - F_b)
	
img = cv.imread('esempi/kernel.png', cv.IMREAD_GRAYSCALE)
f = create_sharpen_filter(False, 5, 2)
res1 = sharpen(img, False, 5, 2)
res2 = cv.filter2D(img, -1, f)
```

