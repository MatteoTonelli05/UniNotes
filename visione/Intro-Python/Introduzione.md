# Introduzione

> [!quote] La materia
> *La Visione Artificiale (Computer Vision) studia come insegnare ai computer a*
 *comprendere e interpretare le informazioni presenti in immagini e video.*

**Difficoltà**: 
- presa un'immagine, arrivare alla stessa percezione umana dalla sua rappresentazione matriciale.
- una serie di immagini a noi identiche hanno differenze sostanziali in rappresentazione matriciale, date da:
	- illuminazione
	- prospettiva
	- deformazione
	- occlusione ("si vede solo una parte dell'oggetto")
	- scala
	- movimento ("a volte può essere utilissimo per riconoscere qualcosa")
	- variazioni intra-classe
		- classe: gruppi di oggetti con caratteristiche simili
		- esempio: classe "cane"
			- due elementi di classe "cane" possono essere molto diversi a seconda della razza - *per esempio*
	- sfondo

>[!quote] "Il modo migliore di imparare ad usare un computer è dimostrare di non averne bisogno"

# Python
---

> [!info] **Slide di riferimento**
> `VA_2_Python.pdf`

**Caratteristiche chiave**
- linguaggio di altissimo livello **general-purpose**
- progettato per garantire grande **leggibilità** di codice
	- allo stesso tempo verboso (ad esempio `and`, `or` invece dei canonici `&&`, `||`)
	- obbligo di strutturare il codice in modo leggibile, ad esempio indentazione obbligatoria per compilare
- supporta **più paradigmi** di programmazione (procedurale, ad oggetti, imperativo, funzionale (ad es.: *lambda function*))
	- ogni variabile fa riferimento ad una classe (a differenza di Java o C#, non esistono tipi nativi / value types)
- ampia **libreria standard**, facilmente estendibile
---
- creatore: Guido von Rossum 
- nome deriva da gruppo comico inglese "*Monty Python*"
	- lo stesso che ha dato origine al termine "spam"
- già dalle prime release (1.0 e 2.0) c'erano feature molto avanzate
	- Garbage Collector, lambda functions, tuple, list comprehension, supporto Unicode...
- la versione che **rompe le compatibilità è la 3.0**
	- ad esempio: prima `print` era un comando, non una funzione
---
## Briciole di Sintassi
- **Indentazione:** l'indentazione definisce i blocchi di codice, sono *obbligatori*
- **Commenti:** iniziano con il carattere '#'
- **Istruzioni:**
	- il carattere **'\\'** permette di dividere una istruzione su più linee
	- - il carattere **';'** permette di mettere più istruzione sulla stessa riga
- **Oggetti predefiniti:**
	- None: *assenza di un valore*
	- Ellipsis: 
	- NotImplemented: *metodi possono restituire questo valore se non implementano l'operazione per determinati operandi*

## Moduli
Un modulo è semplicemente un file contenente codice Python. Il nome del file (senza estensione `.py`) diventa il nome del modulo.

 **Esempio**: Se crei un file `matematica.py`, il modulo si chiamerà `matematica`.

---

### Varianti del comando `import`

Esistono diversi modi per importare funzionalità, a seconda di quanto si vuole essere specifici e di come si vuole gestire il "namespace" (lo spazio dei nomi).

| **Comando**                 | **Descrizione**                                      | **Accesso alle funzioni** |
| --------------------------- | ---------------------------------------------------- | ------------------------- |
| `import fib`                | Importa l'intero modulo.                             | `fib.fibonacci(90)`       |
| `import fib as f`           | Importa con un **alias** (soprannome).               | `f.fibonacci(90)`         |
| `from fib import fibonacci` | Importa una funzione specifica.                      | `fibonacci(90)`           |
| `from fib import *`         | Importa **tutto** (sconsigliato: rischio conflitti). | `fibonacci(90)`           |
| `from fib import f as fibo` | Importa una funzione con alias.                      | `fibo(90)`                |