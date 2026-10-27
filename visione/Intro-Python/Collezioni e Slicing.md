# Collezioni
## Confronto Rapido
| **Tipo**          | **Simbolo** | **Ordinata** | **Modificabile** | **Duplicati** |
| ----------------- | ----------- | ------------ | ---------------- | ------------- |
| **Lista**         | `[]`        | Sì           | Sì               | Sì            |
| **Tupla**         | `()`        | Sì           | **No**           | Sì            |
| **Insieme (Set)** | `{}`        | No           | Sì*              | **No**        |
| **Dizionario**    | `{k:v}`     | Sì           | Sì               | No (chiavi)   |
\* Gli elementi non sono modificabili singolarmente, ma si possono aggiungere/rimuovere.
## Liste (`list`)
### Creazione

``` Python
coloriA = ['Rosso', 'Verde', 'Blu'] 
coloriB = [c for c in coloriA if c[0]!='Rosso'] # "list comprehension"
coloriC = list("Ciao") # crea `['C', 'i', 'a', 'o']`
coloriD = list(range(5)) # genera `[0, 1, 2, 3, 4]`.
```
### Accesso agli elementi
- con indice fra parentesi quadre (**0-based**)
- usando lo **slicing**
- iterazione con **for...in**
## Tuple 
### Creazione

``` Python
colori = ('Rosso', 'Verde', 'Blu')
t = tuple([1, 2, 3])  # Risultato: (1, 2, 3) [11]
stringa = tuple("ciao") # Risultato: ('c', 'i', 'a', 'o') [11]
```
### Accesso agli elementi
- ***COME LE LISTE***

> [!attention]
> ``` Python
> singolo = (5,)  # Senza la virgola è solo un numero tra parentesi
> ```


> [!info] Perchè usare le Tuple e non le Liste?
> Le **Liste** sono flessibili, le **Tuple** sono più veloci e sicure (costanti).


## Insiemi (Set)

### Creazione

``` Python
colori = ('Rosso', 'Verde', 'Blu')
t = set([1, 2, 3])  
# Esempi di "set comprehension" 
colori_a = {c for c in colori if c[0]=='A'} 
quadrati = {x**2 for x in range(1,10)}
```
### Accesso agli element
- Iterazione con **for…in**
## Dizionari

### Creazione

``` Python
d = {101: "C.Rossi", 103: "M.Bianchi"} 
d = dict(nome="Luca", eta=25)
d = {}
d = dict() 
d = {k: v for k, v in lista_tuple} # Dictionary comprehension
```
### Accesso agli elementi

``` Python
val = d[101]
val = d.get(105, "N/A") # Ritorna "N/A" (default) se la chiave manca
if 103 in d: # controlla se una chiave è presente
```

# Slicing

``` Python
a = "Python!"
print(a[4:6], a[0:2], a[:2]) # on Py Py
print(a[4:len(a)], a[4:100], a[4:]) # on! on! on!
print(a[:-3], a[-3:]) # Pyth on!
print(a[1:-1], a[:]) # ython Python!

print(a[::2]) # Pto!
print(a[1::2]) # yhn
print(a[::-1]) # !nohtyP
print(a[-3::2]) # o!
print(a[-1:-4:-2]) # !o

a = [1, 2, 3, 4, 5, 6, 7, 8, 9]
print(a[-2::-2]) # [8, 6, 4, 2]
a[:3] = a[3:]
print(a) # [4, 5, 6, 7, 8, 9, 4, 5, 6, 7, 8, 9]
a[::2] = [0]*6
print(a) # [0, 5, 0, 7, 0, 9, 0, 5, 0, 7, 0, 9]
a[3:-3] = []
print(a) # [0, 5, 0, 7, 0, 9]
a[:] = a[::-1]
print(a) # [9, 0, 7, 0, 5, 0]
```