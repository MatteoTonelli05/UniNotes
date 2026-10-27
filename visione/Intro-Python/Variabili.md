## Intro
- Ogni variabile è un oggetto, caratterizzato da:
	- tipo — `type()`
	- identità — `id()`
	- valore
---
- il nome deve essere `Case-sensitive` con codifica UNICODE
- ogni variabile ha un **tipo**, definito al momento dell'assegnazione

```python
x = 5 # int
x = 20.5 # float
x = y = z = 99 # assegnamento dello stesso valore a più variabili
x, y, z = "Rosso","Verde","Blu" # assegnamento di più variabili in linea
```

La variabile va intesa come etichetta ad un puntatore in memoria; 
- nella prima istruzione `x` punta ad un oggetto di tipo `int` che contiene un valore 5, 
- nella seconda istruzione cambiamo il riferimento ad `x`, che punta ad un nuovo oggetto di tipo `float`

```python
a = 10
b = a
a = 20
print(a, b) # Output: 10 20
```

C'è una sorta di ottimizzazione interna per alcuni numeri

```python
for i in range(300):
	# l'interprete non è abbastanza furbo da capire che si tratta dello stesso oggetto; 
	# non entra nell'if se non perché riconosce che il numero è già stato utilizzato in passato
	if id(i) == id(i + 0): 
		print(i)
	else:
		print("Non ottimizzato", i) 
```

Se eseguiamo il codice, vedremo che dal numero 257 in su viene stampato "Non ottimizzato"
- esiste una sorta di cache interna per i numeri più utilizzati (da 0 a 256 per esempio)

## Globali e Locali
All'interno delle funzioni le variabili (anche con lo stesso nome) vengono dichiarate come locali
```python
a = "divertente" # Variabile globale
def funzione():
	a = "facile" # Variabile locale
	print("Python è " + a)
funzione()
print("Python è " + a)
```

>[!info] Come si fa a modificare una variabile dentro una funzione? 
> Si definisce la variabile come **global**
> ```python
>a = "divertente" # Variabile globale
>def funzione():
>	global a
>	a = "facile" # Variabile locale
>	print("Python è " + a)
>funzione()
>print("Python è " + a)
>```

## Tipi di dati

```Python
x = "Visione Artificiale" # str 
print(type(x)) x = 20 # int 
print(type(x)) x = 20.5 # float 
print(type(x)) x = 1j # complex 
print(type(x)) x = ["verde", "rosso", "blu"] # list
print(type(x)) x = ("verde", "rosso", "blu") # tuple
x = range(6) # range 
x = {"nome" : "VA", "codice" : 17634}# dict
x = {"verde", "rosso", "blu"} # set
x = frozenset(x) # frozenset 
x = True # bool 
x = b"ABCD" # bytes 
x = bytearray(5) # bytearray 
x = memoryview(bytes(5)) # memoryview
```

## Tipi mutabili o immutabili 

Il seguente codice

```python
pi = 3.14
print(type(pi)) # Output: float
pi *= 2
``` 

Non va a modificare l'oggetto referenziato da `pi`, ma ne crea uno nuovo contenente il valore dell'espressione `pi * 2` e lo assegna all'oggetto `pi`.

Questo perché il tipo di dato `float` è **immutabile** — l'oggetto in memoria non cambia, ma si ricrea.

## Conversioni
### Numeriche
``` Python
x = 1 # int
y = 35e3 # float 
z = 3+5j# complex
a = float(x) # converte da int a float 
b = int(y) # converte da float a int 
c = complex(x) # converte da int a complex 
```

### Stringhe
``` Python
print("Sono una stringa")
x = 'Altra'
y = 'stringa'
v = x + ' ' + y # Concatenazione di stringhe

# Stringa multi-linea
soldati = """Si sta come
d’autunno
sugli alberi
le foglie."""
print(soldati)

print(v[0]) # Stringa come 'array'
print(len(v)) # Lunghezza di una stringa
print(v.upper()) # Metodo della classe stringa
print(v.replace("A", "U")) # Altro metodo

x = 34.23322
# Esempio di Formatted string literal
s = f'Form
```

### Booleani 
``` Python
print(10 > 9)
print(10 == 9)
print(10 < 9)

a = 200
b = 33

if b > a:
	print("b > a")
else:
	print("b <= a")
	
# Valori convertiti a True
x = "VA"
y = 15
print(bool(x), bool(y), bool("abc"), bool(123))
print(bool(["rosse", "verde", "blu"]))

# Valori convertiti a False
print(bool(False), bool(None), bool(0), bool(""))
print(bool(()), bool([]), bool({}))
```