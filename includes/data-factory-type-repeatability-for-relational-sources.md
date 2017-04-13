## <a name="repeatability-during-copy"></a>Ripetibilità durante la copia

Quando si copiano dati da e a stores relazionali, è necessario mantenere ripetibilità precauzioni per evitare risultati imprevisti. 

Una sezione è possibile eseguire nuovamente automaticamente in Azure Data Factory in base ai criteri di tentativi specificati. È consigliabile impostare un criterio di tentativi per evitare errori temporanei. Ripetibilità pertanto è importante a prendersi cura della durante lo spostamento dei dati. 

**Come origine:**

> [AZURE.NOTE] Negli esempi seguenti sono relativi SQL Azure ma si applicano a qualsiasi archivio di dati che supporta rettangolare set di dati. Potrebbe essere necessario modificare il **tipo** di origine e la proprietà **query** (ad esempio: query anziché sqlReaderQuery) per i dati archivio.   

In genere, durante la lettura da archivi relazionali, si vuole leggere solo i dati corrispondenti a tale sezione. Un modo sarebbe utilizzando le variabili WindowStart e WindowEnd disponibili in Azure Data Factory. Informazioni sulle variabili e funzioni in Azure Data Factory qui nell'articolo [pianificazione e l'esecuzione](../articles/data-factory/data-factory-scheduling-and-execution.md) . Esempio: 
    
      "source": {
        "type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
      },

Questa query legge i dati dalla tabella' ' compreso nell'intervallo di durata sezione. Rieseguire di questa sezione sarebbe assicurarsi anche sempre questo comportamento. 

In altri casi, è consigliabile leggere l'intera tabella (si supponga che per una volta spostare solo) e possono definire la sqlReaderQuery come indicato di seguito:

    
    "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "select * from MyTable"
              },
    
