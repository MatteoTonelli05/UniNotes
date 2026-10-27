# Definizione

``` Python
def nome_funzione(variabile: tipo,
				 *positional_arguments,
				 variabile: tipo = valore_default,
				 **keyword_arguments
				 ) -> tipo_di_ritorno: 
""" 
Docstring 
:param variabile: descrizione
:param variabile: descrizione
:return: descrizione
"""
	# contenuto_metodo
	return variabile_ritorno
```
## Parametri

>Come qualsiasi variabile Python, i parametri sono **riferimenti a oggetti** 	
>- Per gli oggetti **immutabili** (es. numeri, stringhe) l'effetto è sostanzialmente analogo al passaggio per valore. 

> [!info] cos'è un **Positional Arguments** ?
> Permette di passare quanti nomi vogliamo senza dover creare una lista prima 
> (Python li impacchetta in una **Tupla**).
> Viene preceduto dall' **asterisco** (\*)

> [!info] cos'è un **Keyword Arguments** ?
> Cattura qualsiasi altra informazione extra e la mette in un **Dizionario**
> Viene preceduto dal **doppio asterisco** (\**)


## Unpacking...
### ...nel Passaggio dei Parametri
- L'operatore \* consente di passare una sequenza (lista, tupla, …) a una funzione che richiede un elenco di argomenti separati 
- L'operatore \** consente di passare un dizionario a una funzione che richiede un elenco separato di argomenti 
### ...nell'Assegnamento
- L'operatore \* permette di catturare tutti gli elementi restanti in una lista
- 
``` Python
def calcola(a, b, c):
	return a + b + c
parametri = [4, 18, 20]

print(calcola(*parametri))
a = ["Python", "NumPy", "OpenCV"]
s = {"M101":"C.Rossi","M103":"M.Bianchi","M111":"L.Verdi"}
Esame("Visione Artificiale", *a, **s)

x, y, z = 2, 3, 4 # (x, y, z) = (2, 3, 4)

a1, a2, a3 = a
print(a1, a2, a3)
a1, *r = a
print(a1, r)

primo, secondo, *altri, ultimo = range(10)
print(primo, secondo, ultimo, altri)
```

# Funzioni come Oggetti

> [!attention] In Python, le funzioni sono oggetti a tutti gli effetti. 
> Questo significa che hanno attributi, possono essere memorizzate in variabili e passate ad altre funzioni.

## Caratteristiche degli Oggetti Funzione

Ogni funzione possiede dei metadati accessibili tramite attributi speciali:

- **`__name__`**: Restituisce il nome della funzione (stringa).
    
- **`__doc__`**: Restituisce la stringa di documentazione (docstring).
    
- **`type()`**: Restituisce `<class 'function'>`.
    
> [!attention] 
> **`__name__`** ritorna **`__main__`** se il modulo è eseguito come script
## Higher-Order Functions (Funzioni di ordine superiore)

Sono funzioni che accettano altre funzioni come argomenti.

``` Python
def prodotto(x, y): return x * y

def esegui(f, x, y):
    """Esegue la funzione 'f' passata come argomento."""
    return f(x, y)

print(esegui(prodotto, 5, 4)) # Output: 20
```

---
# Funzioni Lambda (Anonime)

La keyword `lambda` definisce funzioni rapide "usa e getta" senza un nome, costituite da una sola espressione.

**Sintassi:** `lambda parametri: espressione`

|**Caratteristica**|**Descrizione**|
|---|---|
|**Anonimato**|Non richiedono `def` o un nome (se non assegnate a una variabile).|
|**Singola riga**|Possono contenere solo un'espressione, il cui risultato viene restituito automaticamente.|
|**Esempio**|`f = lambda x, y: x ** y` (elevamento a potenza).|

---

# Type Hints (Annotazioni di Tipo)

A partire da Python 3.5 (e migliorate nelle 3.9/3.10), le annotazioni permettono di dichiarare i tipi attesi. 

**Nota:** Python resta a **tipizzazione dinamica**; i type hints servono per documentazione e tool di analisi (come MyPy), non bloccano l'esecuzione.
### Sintassi delle Annotazioni

- **Variabili**: `nome_variabile: tipo = valore`
- **Funzioni**: `def nome(param: tipo) -> tipo_ritorno:`
### Esempio

``` Python
def raddoppia(valore: int | float) -> int | float:
    return valore * 2
```

---

## Funzioni Predefinite (Built-in) Fondamentali

L'interprete Python fornisce strumenti pronti all'uso per gestire le collezioni.

### Funzioni Statistiche e Ordine

- **`sum(seq)`**: Somma gli elementi di una sequenza.
    
- **`min(seq)` / `max(seq)`**: Restituiscono il valore minimo/massimo.
    
- **`sorted(seq)`**: Restituisce una **nuova** lista ordinata (senza modificare l'originale).
### Funzioni di Aggregazione e Iterazione

| **Funzione**      | **Scopo**                                   | **Esempio Risultato**  |
| ----------------- | ------------------------------------------- | ---------------------- |
| **`enumerate()`** | Aggiunge un contatore a un iterabile.       | `[(0, 'A'), (1, 'B')]` |
| **`zip()`**       | Accoppia elementi di più sequenze in tuple. | `[('A', 1), ('B', 2)]` |
| **`len()`**       | Restituisce la lunghezza della collezione.  | `3`                    |
| **`type()`**      | Restituisce la classe dell'oggetto.         | `<class 'int'>`        |

``` Python
s = "Python"
# Esempio zip: accoppia caratteri e i loro codici ASCII
codici = [ord(c) for c in s]
accoppiati = list(zip(s, codici)) 
# Risultato: [('P', 80), ('y', 121), ('t', 116), ...]
```