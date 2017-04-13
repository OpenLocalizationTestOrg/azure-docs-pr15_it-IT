Azure consente di verificare la versione di Python da utilizzare per l'ambiente virtuale con la priorità seguente:

1. versione specificata in runtime.txt nella cartella radice
1. versione specificato dall'impostazione Python nella configurazione dell'applicazione web ( **Impostazioni** > blade**Impostazioni applicazione** per un'app web nel portale di Azure)
1. Python 2.7 è l'impostazione predefinita se tutte le risposte sopra specificati

Valori validi per il contenuto di 

    \runtime.txt

sono:

- Python 2.7
- 3.4 Python

Se la versione micro (terza cifra) viene specificato, verrà ignorata.
