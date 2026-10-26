## Random Forest (Bagging)

Random Forest (Breiman, 2001) fa parte delle tecniche di **Bootstrap Aggregating (Bagging)**

> [!question] Cos'è il Bagging ? (**Bootstrap Aggregating)
> Se dai l'intero pacchetto di dati ad un solo modello, quel modello si farà un'idea rigida del problema. Nel Bagging, invece, prendi il tuo set di dati e ne estrai a caso dei sottoinsiemi (usando il "re-imbussolamento", cioè rimettendo dentro ogni volta i punti estratti). Di solito ogni sottoinsieme contiene circa due terzi dei dati originali. Addestri un modello diverso su ciascun sottoinsieme e poi fai votare tutti i modelli insieme.

1. **Pattern Bagging**: Si estraggono $S_j$ campioni con re-imbussolamento dal TS (circa 2/3 del totale per ogni albero).
2. **Feature Bagging**: Ad ogni nodo di ogni albero decisionale (CART), la scelta della feature migliore su cui effettuare lo split non viene fatta su tutte le $d$ feature, ma su un sottoinsieme casuale di dimensione $d' = \sqrt{d}$.
3. **Classificatori base**: Alberi decisionali CART binarizzati.