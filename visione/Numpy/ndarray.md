## Attributi Fondamentali
| **Attributo**  | **Descrizione**                                                                                                    |
| -------------- | ------------------------------------------------------------------------------------------------------------------ |
| **`ndim`**     | Il numero di dimensioni o assi (_axes_) dell'array.                                                                |
| **`shape`**    | Una tupla di interi che indica la dimensione dell'array lungo ogni asse                                            |
| **`size`**     | Il numero totale di elementi presenti nell'array (prodotto degli elementi di `shape`).                             |
| **`dtype`**    | Descrive il tipo degli elementi (es. `int32`, `float64`).                                                          |
| **`itemsize`** | La dimensione in **byte** di ogni singolo elemento dell'array.                                                     |
| **`data`**     | Puntatore al buffer di memoria contenente gli elementi (usato raramente, si preferisce l'indicizzazione standard). |
### Ad Esempio

``` Python
import numpy as np

# Creazione di un array 3x5 (3 righe, 5 colonne)
a = np.array([[ 0,  1,  1,  2,  3],
              [ 5,  8, 13, 21, 34],
              [55, 89, 144, 233, 377]])

print('Tipo oggetto:', type(a)) # type: <class 'numpy.ndarray'>
print('Dimensioni (ndim):', a.ndim) # ndim: 2
print('Forma (shape):', a.shape) # shape: (3, 5)
print('Totale elementi (size):', a.size) # size: 15
print('Tipo dati (dtype):', a.dtype) # dype: int32
print('Byte per elemento (itemsize):', a.itemsize) # itemsize: 4
```

## Creare un array
### Funzione array()
- La funzione array consente di creare un array partendo da una lista o tupla Python
- Il tipo dell'array viene dedotto dagli elementi, oppure può essere specificato
- Sequenze di sequenze sono trasformate in array bidimensionali (e così via)

``` Python
a = np.array([2,3,5])
a = np.array([2,3,5], np.uint8)

a = np.array([[ 0, 1, 1, 2, 3],
			[ 5, 8, 13, 21, 34],
			[ 55, 89, 144, 233, 377]])
			
a = np.array([ [ [1,2] ], [ [3,4] ] ]) # array tridimensionale
```

### Altri modi per creare un array

**`empty()`** crea un array lasciando i valori non inizializzati 

``` Python
a = np.empty((2,7), np.int16) # 2 righe, 7 colonne
```

>[!attention]
>NON INIZIALIZZATI NON SIGNIFICA ZERO (ma i valori garbage di default)

---
**`zeros()`** e **`ones()`** creano un array di soli zero/uno

``` Python
print(np.zeros(5)) # [0. 0. 0. 0.]
print(np.ones(3, np.int)) # [1 1 1]
print(np.zeros((2,3))) # [[0. 0. 0.][0. 0. 0.]]
```
---

**`arange()`** crea un array che copre un certo range, si può specificare anche il `passo (step)`

``` Python
a = np.arange(100, 110, 2) # [100 102 104 106 108]
```
---
**`identity()`** crea una matrice identità

``` Python
a = np.identity(3)
'''
[[1. 0. 0.]
[0. 1. 0.]
[0. 0. 1.]]
'''
```



