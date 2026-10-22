> [!abstract] Un'automa a pila (**PDA**) è in pratica un $\epsilon$-NFA con una pila

![[Pasted image 20261022100416.png]]
> [!question] Come funziona una transizione
> 1. Legge il simbolo corrente dell'input (oppure esegue una mossa $\epsilon$ senza consumare input).
>
>2. Esamina e rimuove (_pop_) il simbolo che si trova in cima alla pila.
  >  
>3. Passa a un nuovo stato.
  >  
>4. Inserisce (_push_) una stringa di simboli $\gamma \in \Gamma^*$ al posto del simbolo appena rimosso. Se $\gamma = \epsilon$, la cima viene semplicemente rimossa; se $\gamma$ è pari al simbolo consumato, la pila rimane inalterata.