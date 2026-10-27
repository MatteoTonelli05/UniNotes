# Geometria Proiettiva

Quando proiettiamo il mondo 3D su un piano, avvengono trasformazioni radicali
*(ad esempio Prospettiva, Distanza, Convergenza, Distorsioni di forme e angoli)*

![[Pasted image 20260320121709.png|207]]

## Proiezione Prospettica

Il nostro compito sarebbe capire dove un punto $P(X,Y,Z)$ dello spazio finirà sul piano immagine $(x,y)$.

>[!info] Idea
> usiamo la **similitudine tra triangoli**.


La figura mostra il modello in sezione laterale (lungo il piano $YZ$):
- il centro di proiezione o fuoco della camera coincide con l’origine $O$ degli assi
- il piano immagine o piano di proiezione $\pi$ è perpendicolare all'asse $Z$
- la distanza tra $O$ e $\pi$ è la lunghezza focale $f$

![[Pasted image 20260320122007.png]]

Dato che i triangolo $pOQ$ e $POR$ sono simili, si ha:
$$
\overline{pQ}:\overline{PR} = \overline{OQ}:\overline{OR} \rightarrow y:Y = f:Z
$$
da qui otteniamo (analogamente per $x$):
$$
x = f \cdot \frac{X}{Z} \qquad y = f \cdot \frac{Y}{Z}
$$
> [!info] Nota
> La divisione per $Z$ è il motivo per cui gli oggetti lontani appaiono piccoli.

> [!attention] Il sistema di riferimento non è così "perfettamente allineato"

# Calibrazione della camera

Nella realtà, la fotocamera non è quasi mai al centro dell'universo ($0,0,0$). Si muove e ruota. Per questo dobbiamo distinguere tra:

- **Coordinate Mondo $P_w = (X_w,Y_w,Z_w)$:** Dove si trova l'oggetto rispetto a un punto fisso (es. l'angolo di una stanza).
    
- **Coordinate Camera $P_c = (X_c,Y_c,Z_c)$:** Dove si trova l'oggetto rispetto all'obiettivo della fotocamera.

---

Per passare dalle coordinate mondo a quelle camera, usiamo i ==**Parametri Estrinseci**==:

1. **Rotazione ($R$):** Una matrice che descrive come la camera è inclinata (3 gradi di libertà: beccheggio, imbardata, rollio).
    ![[Pasted image 20260320130847.png|336]]
2. **Traslazione ($t$):** Un vettore con 3 dimensioni che indica la posizione della camera nello spazio.
    
La formula di trasformazione completa è:
$$P_c = R \cdot P_w + t$$
> [!info] A cosa serve la formula di trasformazione?
> Possono essere utilizzati per descrivere il movimento di una camera attorno a una scena statica, oppure il movimento di un oggetto rispetto a una camera ferma.

---
### Esempio pratico

$P_w$ deve essere rappresentato tramite la telecamera, usiamo la formula e troviamo $P_c$

![[Pasted image 20260323140336.png]]

una volta che abbiamo le coordinate espresse nel sistema di riferimento della camera, si possono applicare le equazioni fondamentali della proiezione prospettica:
$$
x = f \cdot \frac{X_c}{Z_c} \qquad y = f \cdot \frac{Y_c}{Z_c}
$$
![[Pasted image 20260323141802.png]]

> [!attention] Per determinare la trasformazione servono dei ==**Parametri Intrinseci**==:
> - Lunghezza focale $f$ 
> - Coordinate del punto principale $(c_x,c_y)$ 
> - La relazione tra la dimensione dei pixel e l'unità di misura della scena 3D $(s_x,s_y)$
> - _ci sarebbero anche i parametri del modello di deformazione: $k_1,k_2,k_3,...$_

### Coordinate omogenee

> [!info] 
> In coordinate omogenee, due vettori (punti nello spazio 3D) sono equivalenti se differiscono solo di un fattore di scala
> $$P=(X,Y,Z)\leftrightarrow \tilde{P}=(X\cdot w,Y\cdot w,Z\cdot w)=(X,Y,Z,1),w\neq 0 $$

Anche in questo caso le coordinate omogenee possono aiutarci a semplificare le operazioni

la **Rototraslazione** da $P_w$ a $P_c$ in coordinate omogenee:
![[Pasted image 20260324103611.png|466]]

la **Proiezione Prospettica** da $P_c=(X_c,Y_c,Z_c) a p = (U,v)$ in coordinate omogenee:
![[Pasted image 20260324103716.png|464]]

> [!attention]
>  ![[Pasted image 20260324103909.png]]
# Distorsione Radiale

> [!attention] Le lenti causano una certa distorsione nell'immagine
> Nella realtà, le lenti non sono perfette. Più ci si allontana dal centro ottico, più i raggi luminosi vengono deviati in modo non lineare.

### Distorsione Radiale - Modello Matematico
Le coordinate ideali $(x,y)$ vengono trasformate in coordinate distorte $(x', y')$ tramite un polinomio basato sul raggio $r^2=x^2+y^2$
$$x' = x(1 + k_1r^2 + k_2r^4 + k_3r^6)$$

$$y' = y(1 + k_1r^2 + k_2r^4 + k_3r^6)$$
> [!info]
> ![[Pasted image 20260324112811.png]] 
>- **Distorsione a Barilotto ($k_1 > 0$):** Le linee rette curvano verso l'esterno (tipico dei grandangoli).
> - **Distorsione a Cuscinetto ($k_1 < 0$):** Le linee curvano verso l'interno (tipico dei teleobiettivi).

### Il Modello di Proiezione Completo
![[Pasted image 20260324113017.png]]


# Calibrazione

> [!info] Quindi cosa significa calibrare?
> Consiste nel **determinare i parametri estrinseci ed intrinseci** di una camera
> Disponendo di un numero sufficiente di **corrispondenze** fra punti 3D della scena e punti 2D dell'immagine, è possibile stimare i vari parametri

## Ricerca di punti corrispondenti

**L'oggetto di calibrazione** 
>Si usa solitamente una **scacchiera**. 

**Perché?** 
> Perché i vertici interni sono facili da individuare matematicamente con altissima precisione.

**Come si calibra?**
> Da una serie di **𝑛 immagini** con posizioni diverse (dell'oggetto o della camera), si ottengono quindi, per ogni immagine, una serie di corrispondenze $<P_w^𝑖, p^i>$ fra coordinate nella scena 3D $P_w^𝑖$ e coordinate 2D $p^i$
> ![[Pasted image 20260324114822.png]]

## Calibrazione in OpenCV

| **Funzione**                 | **Scopo**                                                                                                   |
| ---------------------------- | ----------------------------------------------------------------------------------------------------------- |
| `cv.findChessboardCorners()` | Trova i vertici della scacchiera nell'immagine.                                                             |
| `cv.cornerSubPix()`          | Raffina la ricerca con precisione sub-pixel                                                                 |
| `cv.drawChessboardCorners()` | Disegna i vertici della scacchiera.                                                                         |
| `cv.calibrateCamera()`       | Calcola matrici intrinseche ed estrinseche.                                                                 |
| `cv.undistort()`             | Rimuove la distorsione da un'immagine usando i parametri calcolati.                                         |
| `cv.projectPoints()`         | Proiezione di punti 3D sull’immagine (conversione da $P_w$ a $p$) dati i parametri intrinseci ed estrinseci |
