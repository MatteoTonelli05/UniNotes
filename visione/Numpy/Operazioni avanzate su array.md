## Indicizzazione e slicing su array monodimensionali

Negli array **NumPy monodimensionali**, l'accesso agli elementi è analogo a quanto avviene con liste e altre sequenze in Python.
``` Python
a = np.arange(10)**3
print(a[2]) # Accesso a un elemento
print(a[2:5]) # Slicing: dall'elemento 2 al 4
a[:6:2] = 42 # Modifica elementi di posto 0, 2, 4
print(a[::-1]) # Step negativo: ordine inverso
a[::2] += a[1::2] # a[i] += a[i+1], i=0,2,4,6,8
a[:] = -1 # Modifica tutti gli elementi
```

## Indicizzazione e slicing su array multidimensionali

![[Pasted image 20260309133839.png]]
![[Pasted image 20260309134010.png]]

## Slicing con ellissi

> [!info] Cos'è un ellissi?
> Un'ellissi è un oggetto python che può essere indicato con `...`

> [!info] E a cosa serve?
> NumPy lo utilizza nello slicing per rappresentare tutti i ':' che mancano per produrre una tupla di indicizzazione completa.

``` Python
# Inizializza un array con 3 dimensioni
c = np.array( [[[ 0, 1, 2],
				[ 10, 12, 13]],
			   [[ 50, 51, 52],
				[ 60, 62, 63]]])
				
print(c.shape)
print(c[1,...]) # Equivale a c[1] e a c[1,:,:]
print(c[...,2]) # Equivale a c[:,:,2]

```
## Iterare su un array

Come già detto, si può iterare un ndarray , ma è **fortemente sconsigliato**. 

> [!info] Differenza tra array python e numpy
> - **Lista Python:** È un contenitore di puntatori ad oggetti sparsi nella memoria. L'iterazione è il suo modo naturale di funzionare.
> 
> - **NumPy Array:** È un blocco contiguo di memoria (C-style o Fortran-style). Accedere agli elementi uno alla volta tramite un ciclo `for` in Python distrugge i vantaggi di performance della "vettorizzazione".**

A tal proposito è considerabile come una qualunque lista default di Python.
Per iterare tutti gli elementi di myarray , si usa **myarray.flat** (attributo)

``` Python
for x in myarray.flat:
	...
```

## Modifica della forma

``` Python
a = np.arange(12)
print(a.shape) # (12,) <--- RICORDA: la virgola è per definire la tupla
print(a) # [ 0 1 2 3 4 5 6 7 8 9 10 11]
```

`reshape()` modifica la forma dell'array

``` Python
b = a.reshape(2, 6)
print(b.shape) # (2, 6)
print(b)
'''
[[ 0 1 2 3 4 5]
[ 6 7 8 9 10 11]]
'''
```

con `reshape()` e una dimensione è -1, viene calcolata automaticamente

``` Python
c = a.reshape(2, 2, -1)
print(c.shape) # (2, 2, 3)
print(c)
'''
[[[ 0 1 2]
  [ 3 4 5]]
 [[ 6 7 8]
  [ 9 10 11]]]
'''
```
---
`resize()` modifica l'array stesso

``` Python
a.resize(2,6) 
print(a) 
'''
[[ 0 1 2 3 4 5]
[ 6 7 8 9 10 11]]
'''
```
---
`ravel()` restituisce i dati come array monodimensionale

``` Python
d = a.ravel()
print(d)
'''
[ 0 1 2 3 4 5 6 7 8 9 10 11]
'''
```
---
si possono aggiungere nuove dimensioni con `np.newaxis`

``` Python
'''
[[ 0 1 2 3 4 5]
[ 6 7 8 9 10 11]]
'''
a = np.arange(12).reshape(2,6)
b = a[np.newaxis, ...]
print(b.shape) # (1,2,6)
print(b) 
'''
[[[ 0 1 2 3 4 5]
  [ 6 7 8 9 10 11]]]
'''
c = a[np.newaxis, ..., np.newaxis, np.newaxis]
print(c.shape) # (1, 2, 6, 1, 1)
```

>[!info] Ricorda
> np.newaxis non è altro che un riferimento a None.
> 
> `np.newaxis is None` restituisce `True`

---
si può trasporre le dimensioni con `transpose()` o `.T`

``` Python
d = a.T
e = a.transpose() 
print( d.shape, e.shape) # (6, 2) (6, 2)
```
---
`squeeze()` elimina eventuali dimensioni a uno

``` Python
f = c.squeeze() 
print(f.shape) # (2,6)
```

## Concatenare array

dati due **array**:
`a = [[5. 6.] [2. 0.]]`
`b = [[4. 8.] [1. 7.]]`
possiamo concatenarli tramite:

**`np.vstack((a,b))`** 
	- **Cosa fa?** Prende il primo array e mette il secondo esattamente sotto di esso *(vertical stack)*
	- **Requisiti:** Gli array devono avere lo stesso numero di colonne.
	- **Output:** `[[5. 6.] [2. 0.] [4. 8.] [1. 7.]]`

---
	**`np.hstack((a,b))`** 
	- **Cosa fa?** Incolla gli array uno accanto all'altro lateralmente. *(horizontal stack)*
		- **Requisiti:** Gli array devono avere lo stesso numero di righe.
	- **Output:** `[[5. 6. 4. 8.],[2. 0. 1. 7.]]`

---
Quando invece hai array "**piatti**" (1D), come `x = [4, 2]` e `y = [3, 8]`, le due funzioni si comportano in modo molto diverso:
- **`np.hstack((x, y))`**: Si limita a concatenarli in un unico lungo array 1D `[4, 2, 3, 8]` 
- **`np.column_stack((x, y))`**: Tratta ogni array 1D come se fosse una **colonna** di una matrice 2D. `[[4., 3.],[2., 8.]]`

## Gestione della memoria
### Assegnamento
Non crea nulla di nuovo. `b` è solo un altro nome per `a`. Se cambi `b`, cambi `a`.  `b` è solo un **nuovo riferimento.**

``` Python
a = np.arange(7)
print(a)
b = a
print(b is a) # True
```
---
### Vista
Lo slicing (come altre operazioni) crea una **vista**.
È **velocissimo** perché non copia i dati, ma se modifichi la vista, modifichi l'originale.
`a.base` è un riferimento all'oggetto su cui è costruita la vista.

``` Python
c = a[3::2]
print(c is a, c.base is a) # False, True
c[0] = -1 # Cambierà anche l'array a perchè è una vista
```
---
### Copia
Il metodo `copy()` crea un duplicato totale in un'altra zona di memoria.

``` Python
d = a.copy() # Il metodo copy() crea una copia dell'array 
print(d is a, d.base is a, d.base) # False False None
d[0] = -1 # Non cambierà l'array a
```

## Broadcasting
Strumento con cui NumPy consente alle ufunc di agire su input con forme diverse. 

- **Prima regola**: se gli array di input non hanno lo stesso numero di dimensioni, un "1" viene anteposto alla shape dell'array più piccolo finché il numero di dimensioni non coincide. 
```
a = [[7 5 3 1]]
b = [[0 1 2 3] [4 5 6 7] [8 9 10 11]]

[[7 5 3 1]] --> [[1] [7 5 3 1]] --> [[1] [1] [7 5 3 1]]
```

- **Seconda regola**: le dimensioni a 1 sono trattate come se il numero di elementi fosse pari a quello dell'array con più elementi lungo le corrispondenti dimensioni: si assume che tutti gli elementi lungo tale dimensione siano uguali. 
```
a = [[7 5 3 1]]
b = [[0 1 2 3] [4 5 6 7] [8 9 10 11]]

[[1] [1] [7 5 3 1]] --> [[7 5 3 1] [7 5 3 1] [7 5 3 1]]
```

- Se dopo l'applicazione di queste due regole gli array hanno la stessa forma, l'operazione viene eseguita.

 ``` Python
 c = a + b 
 print(c)
 # [[7 5 3 1] [7 5 3 1] [7 5 3 1]] +
 # [[0 1 2 3] [4 5 6 7] [8 9 10 11]] =
 # ----------------------------------
 # [[7 6 5 4] [11 10 9 8] [15 14 13 12]]
 ```

## (Fancy Indexing) Indicizzare...
### ...con array di indici

Oltre a interi e slicing, si possono utilizzare **array di interi** per indicizzare altri array
``` Python
 a = np.arange(12)**2 # [ 0 1 4 9 16 25 36 49 64 81 100 121]
idx1 = np.array( [ 1,1,3,8,5 ] )
print(a[idx1]) # [ 1 1 9 64 25]
```
L'array di interi può anche essere multidimensionale: il risultato ha la forma dell'array usato come indice.
``` Python
idx2 = np.array( [ [3, 4], [9, 7] ] )
print(a[idx1]) # [[ 9 16] [81 49]]
```

![[Pasted image 20260312103852.png]]
### ...con array di booleani
In questo caso si scelgono esplicitamente quali elementi si vogliono e quali no. 
Il modo più semplice è utilizzare array booleani con la stessa forma dell'array originale.

``` Python
a = np.arange(12).reshape(3,4) 
b = a > 4 
print(a[b]) # un array 1D con gli elementi > 4
```