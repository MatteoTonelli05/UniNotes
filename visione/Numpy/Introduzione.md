**Numpy (Numerical Python)** è un package fondamentale per il **calcolo scientifico** in Python.

> [!info] Cosa fornisce?
> - un **array multi-dimensionale** efficiente e con potenti funzionalità
> - sofisticate tecniche per operare su tale struttura dati
> - utili funzioni matematiche di base

## Vantaggi

1) **In generale è perchè è molto efficiente**
	>- Numpy lavora su interi vettori, dunque **si evitano i loop** (poco efficienti in python)
	>- Gli array contengono elementi **dello stessi tipo**, memorizzandoli in modo contiguo rende molto più efficiente la struttura
2) **OpenCV-Python** utilizza **NumPy**, dato che le immagini sono rappresentate come array tridimensionali.

## Utilizzare Numpy

``` Python
import numpy as np
print(np.__version__)
```


