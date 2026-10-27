## Operazioni di Base

Gli operatori aritmetici si applicano agli array **elemento-per-elemento** e il risultato viene tipicamente memorizzato in un nuovo array.

``` Python
a = np.array( [25,36,49,64] )
b = np.arange( 4 )
print(b) # [0 1 2 3]
print(a-b) # [25 35 47 61]
print(b**2) # [0 1 4 9]
print(np.sqrt(a)) # [5. 6. 7. 8.]
print(a < 37) # [True True False False]

b = np.array( [25,37,49,63] )
print(a == b) # [True False True False]
```

per quanto riguarda il **prodotto** dobbiamo differenziare:
- il prodotto tra Matrici (`@`)
- il prodotto elemento-per-elemento (`*`)
``` Python
# Un vettore "colonna": shape = (3, 1)
c = np.array([[1], [2], [3]])
# Un vettore "riga": shape = (1, 3)
r = np.array([[3,0,2]])
print(c @ r) # (3,1)x(1,3) = (3,3)
print(r @ c) # (1,3)x(3,1) = (1,1)
```

## Operatori di Assegnamento

Gli operatori di assegnamento come `+=` o `*=` modificano  l'array esistente senza doverne creare uno nuovo.

``` Python
a = np.ones((2,3))
b = np.array([[1,0,2], [0,3,0]])

id_a = id(a)
a *= b
print(id_a==id(a)) # True
a = a * b
print(id_a==id(a)) # False
```

## Type Cast (Implicito)

In un operazione tra array di tipo diverso Python converte l'array soluzione nel tipo che ritiene adatto.

``` Python
a = np.ones(3, np.int32)
b = np.array([1.4, 1.5, 1.6])
print(a.dtype, b.dtype) # int32 float64

c = a + b
print(c) # [2.4 2.5 2.6]
print(c.dtype) # float64
```

Per creare un nuovo array cambiando il tipo di un array esistente, si può usare il metodo `astype()`

``` Python
a = np.arange(10, dtype = np.uint8)
print(a.dtype) # uint8
a = a.astype(np.uint64) 
print(a.dtype) # uint64
```

## Alcune esempi di operazioni su un array

``` Python
# Crea una matrice contenente numeri casuali nell'intervallo [0,1)
a = np.random.random((2,3))
print(a)
# [[0.80024488 0.95481118 0.29314655] [0.20377526 0.98809386 0.8159563 ]]
```

> [!info] Spieghiamo il random
> - **`np.random.random`**: È la funzione che genera numeri in virgola mobile (float) compresi nell'intervallo $[0.0, 1.0)$. Il valore $0.0$ è incluso, mentre $1.0$ è escluso. 
> - **`(2,3)`** è la **shape** (forma) dell'array

``` Python
print(f'Min: {a.min()}') # Min: 0.2037752645645331
print(f'Max: {a.max()}') # Max: 0.9880938639265971
print(f'Sum: {a.sum()}') # Sum: 4.05602804986859
```

``` Python
print(f'Somma di ogni colonna: {a.sum(axis=0)}')
# Somma di ogni colonna: [1.00402014 1.94290504 1.10910285]
print(f'Somma di ogni riga: {a.sum(axis=1)}')
# Somma di ogni riga: [2.04820261 2.00782542]
```

## Funzioni Universali (ufunc)

Alcune **ufunc** sono automaticamente chiamate quando si usano i corrispondenti operatori Python _(ad esempio **np.add()** è chiamata quando si usa l'operatore  fra due `+` **ndarray**)_

Alcuni esempi di ufunc sono:
- **Operazioni matematiche** 
	- `add, subtract, multiply, divide, power, exp, log, sqrt`
- **Trigonometria**
	- `sin, cos, tan, arcsin, arccos, arctan, arctan2, sinh, cosh, tanh, deg2rad, rad2deg`
- **Operazioni sui bit** 
	- `bitwise_and, bitwise_or, bitwise_xor, invert, left_shift, right_shift`
- **Confronto** 
	- `greater, greater_equal, less, less_equal, not_equal, equal, logical_and, logical_or, logical_xor, logical_not, maximum, minimum, fmax, fmin`
- **Floating point** 
	- `isfinite, isinf, isnan, fabs, floor, ceil, trunc`
