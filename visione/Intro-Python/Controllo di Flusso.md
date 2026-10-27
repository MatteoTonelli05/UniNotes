## Condizioni if..elif..else

``` Python
a = 200
b = 1
if b > a:
	print("b>a")
	m = a
elif b == a:
	print("a=b")
	m = a
else:
	print("b<a")
	m = b
print(m)

# Espressione condizionale
print("b>a") if b>a else print("b<=a")
# Due espressioni condizionali a cascata

print("b>a") if b>a else print("b=a") if b==a \
							else print("b<a")
							
# forma compatta
print("b>a" if b>a else "b=a" if b==a else "b<a")
```

## Match Case

``` Python
def analizza_stato(codice):
	match codice:
		case 200:
			return "OK"
		case 404:
			return "Not Found"
		case 500 | 501: # OR logico nel pattern
			return "Server Error"
		case _:
			return "Codice sconosciuto"

print(analizza_stato(200))
print(analizza_stato(500))
print(analizza_stato(999))

# Esempio con strutture
punto = (0, 10)
match punto:
case (0, 0):
print("Origine")
case (0, y):
print(f"Asse Y a altezza {y}")
case (x, y):
print(f"Punto generico: {x}, {y}")
```

**Note:**
-  Il carattere "\_" funge da **wildcard** (default) per gestire i casi non specificati.
-  **Più potente del classico switch** di altri linguaggi: permette di confrontare non solo valori, ma anche strutture e tipi.

## Ciclo while e ciclo for

``` Python
i = 0 
while True: 
	i += 1 
	if i == 4: 
		continue # passa alla prossima iterazione
	print(i) 
	if i == 6: 
		break # interrompe il ciclo

i = 0 
while i < 6:
	print(i) 
	i += 1 
else: 
	print("Fine del ciclo") 

colori = ["rosso" , "verde" , "blu" ] 
for x in colori: 
	print(x) 
	
for x in "Visione Artificiale": 
	print(x) 

for i in range (6):
	print(i)
```

## Walrus Operator

> [!info] Perchè si chiama Walrus?
> Soprannominato **Walrus operator** per la somiglianza con le zanne di un tricheco

``` Python
dati = [1, 2, 3, 4, 5]

# Senza walrus operator
n = len(dati)
if n > 3:
	print(f"Lista troppo lunga: {n} elementi")

# Con walrus operator
if (n := len(dati)) > 3:
	print(f"Lista troppo lunga: {n} elementi")

# Utilizzo in un ciclo
# (simula lettura input fino a 'stop')
while (comando := input("Comando: ")) != "stop":
	print(f"Eseguo: {comando}")

# In list comprehension
from math import factorial as f
valori = [3, 7, 15, 20]
risultati = [x for v in valori if (x := f(v)) > 5]
```