> [!quote] La derivata seconda, indicata con $f''(x)$ o $\frac{d^{2}f}{dx^{2}}$, è la derivata della derivata prima di una funzione. Essa misura la rapidità con cui varia la pendenza della funzione stessa
> 
![[Pasted image 20260404235702.png|307]]

> [!info] Idea
> Il bordo è dove la derivata seconda passa dallo **zero**. (non vale viceversa)
> Infatto in **Canny** noi cerchiamo il picco della derivata prima

## Derivata seconda di una funzione discreta

In un sistema discreto (come i pixel di un'immagine), non possiamo calcolare il limite per $h \to 0$. Assumendo un passo $h=1$, la derivata prima $f'(x)$ viene approssimata tramite la **differenza centrale**:

$$f'(x) \approx \frac{f(x+h) - f(x-h)}{2h} \approx f\left(x + \frac{1}{2}\right) - f\left(x - \frac{1}{2}\right)$$

Questo misura quanto cambia il valore tra due punti adiacenti.
_(Lo abbiamo già visto)_

> [!attention] 
> **Primo passaggio:** Rappresenta la **differenza centrale**, un metodo per approssimare la derivata di una funzione utilizzando i valori dei punti adiacenti ($x+h$ e $x-h$).

---

Applichiamo dunque **ricorsivamente** questa assunzione e otteniamo:

![[Pasted image 20260405000811.png]]

Quindi una volta ottenuta l'approssimazione $$f''(x) = f(x+1)-2f(x)+f(x-1)$$
> [!quote] Notiamo che è una stima data dai pixel adiacenti in un immagine e quindi troviamo i filtri:
> ![[Pasted image 20260405001059.png]]

---
## Il Laplaciano Discreto

Il Laplaciano in 2D è definito come la **somma delle due derivate parziali seconde** in un punto:
$$\Delta f(x, y) = \frac{\partial^2 f}{\partial x^2} + \frac{\partial^2 f}{\partial y^2}$$

> [!info] Proprietà del laplaciano
> In un'immagine $\mathbb{I}$, in presenza di un bordo sufficientemente contrastato, il laplaciano $\nabla\mathbb{I}$ è:
• **zero** lontano dal bordo e in corrispondenza del bordo
• **positivo** vicino al bordo (dal lato dove pixel più scuri diventano più chiari muovendosi verso il bordo)
• **negativo** vicino al bordo (dal lato opposto)
>
> ![[Pasted image 20260405002957.png]]
> 	_nell'immagine abbiamo sopra la funzione che rappresenta i pixel dell'immagine (in una retta), in quella di sotto abbiamo il Laplaciano_

Grazie alla proprietà distributiva della convoluzione, possiamo sommare i due kernel delle derivate seconde ($F_x^2$ e $F_y^2$) per ottenere un unico **filtro Laplaciano** $F_\Delta$:

$$\Delta \mathbf{I} = \frac{\partial^2 \mathbf{I}}{\partial x^2} + \frac{\partial^2 \mathbf{I}}{\partial y^2} = \mathbf{I} * \mathbf{F}_x^2 + \mathbf{I} * \mathbf{F}_y^2 = \mathbf{I} * (\mathbf{F}_x^2 + \mathbf{F}_y^2) = \mathbf{I} * \mathbf{F}_\Delta$$
![[Pasted image 20260405003631.png]]

---
>[!attention] Problema del rumore
> La derivata seconda è molto rumorosa, poichè è pur sempre una stima di una stima).
> Per questo si introduce lo **smoothing** (sfocatura).
> ![[Pasted image 20260405004204.png]]

---

## Il Laplacian of Gaussian (LoG)

Invece di sfocare l'immagine e _poi_ calcolare il Laplaciano, la proprietà associativa della convoluzione ci permette di calcolare il Laplaciano direttamente sulla funzione Gaussiana.

La funzione Gaussiana in 2D è:

$$G(x, y, \sigma) = \frac{1}{2\pi\sigma^2} e^{-\frac{x^2+y^2}{2\sigma^2}}$$

Il filtro 𝐋𝐨𝐆 esegue quindi entrambe le operazioni e si può ottenere calcolando **il laplaciano della funzione gaussiana**:

$$\Delta(G_{2D}(x, y)) = \frac{\partial^2 G_{2D}(x, y)}{\partial x^2} + \frac{\partial^2 G_{2D}(x, y)}{\partial y^2} = \left( \frac{x^2}{\sigma^4} - \frac{1}{\sigma^2} \right) G_{2D}(x, y) + \left( \frac{y^2}{\sigma^4} - \frac{1}{\sigma^2} \right) G_{2D}(x, y)$$
$$\Delta(G_{2D}(x, y)) = \left( \frac{x^2 + y^2}{\sigma^4} - \frac{2}{\sigma^2} \right) G_{2D}(x, y)$$

> [!attention] Ricorda 
> Ricorda che i pixel non sono precisi, non avremo un pixel sullo zero del laplaciano, ma noteremo quando un pixel positivo e negativo sono adiacenti.



