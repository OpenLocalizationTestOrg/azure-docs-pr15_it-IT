## <a name="repeatability-during-copy"></a>Ripetibilità durante la copia

Quando si archivia copia di dati di Azure SQL/SQL Server da altri dati è necessario mantenere ripetibilità precauzioni per evitare risultati imprevisti. 

Quando si copiano dati al Database SQL di SQL Azure, Copia attività verrà Accodamento set di dati alla tabella sink per impostazione predefinita. Ad esempio, quando si copiano dati da un'origine di file CSV (dati dei valori separati da virgola) che contiene due record al Database SQL di SQL Azure, è aspetto della tabella:
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00

Si supponga trovare gli errori nel file di origine e aggiornata la quantità di verso il basso tubi da 2 a 4 nel file di origine. Se si esegue nuovamente la sezione di dati per quel periodo, sono disponibili due nuovi record aggiunti al Database SQL di SQL Azure. Di seguito si presuppone nessuna delle colonne nella tabella chiave primaria.
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

Per evitare il problema, è necessario specificare la semantica UPSERT utilizzando uno del sotto 2 meccanismi riportati di seguito.

> [AZURE.NOTE] Una sezione può essere nuovamente eseguita automaticamente in Azure Data Factory in base ai criteri di tentativi specificati.

### <a name="mechanism-1"></a>Meccanismo 1

È possibile sfruttare le proprietà **sqlWriterCleanupScript** per eseguire prima azione pulizia durante l'esecuzione di una sezione. 

    "sink":  
    { 
      "type": "SqlSink", 
      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
    }

Verrà eseguito lo script di pulizia prima durante la copia di una porzione specificata che consentirebbe di eliminare i dati della tabella di SQL corrispondente a tale sezione. L'attività viene successivamente inserire i dati nella tabella di SQL. 

Se la sezione è ora eseguire di nuovo e quindi si troverà che la quantità viene aggiornata come desiderato.
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

Si supponga che il record rondellapiatta viene rimosso da csv originale. Eseguire nuovamente la sezione da producono il risultato seguente: 
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    7   Down Tube   4           2015-05-01 00:00:00

Non è una novità doveva essere eseguita. L'attività di copia è stato eseguito lo script di pulizia per eliminare i dati corrispondenti per tale sezione. Quindi lette l'input da csv (che quindi contenuto solo 1 record) e inserite nella tabella. 

### <a name="mechanism-2"></a>Meccanismo 2
> [AZURE.IMPORTANT] sliceIdentifierColumnName non è supportata per Azure SQL Data Warehouse al momento. 

Un altro per ottenere ripetibilità consiste mediante la ricezione di una colonna dedicata (**sliceIdentifierColumnName**) nella tabella di destinazione. La colonna da utilizzata da Azure Data Factory per assicurarsi che la sincronizzazione di origine e destinazione. Questo approccio funziona quando c'è flessibilità per la modifica o la definizione dello schema di tabella SQL di destinazione. 

La colonna da utilizzata da Azure Data Factory ai fini della ripetibilità e del processo di Azure Data Factory non renderà eventuali modifiche allo schema alla tabella. Metodo per usare questo approccio:

1.  Definire una colonna di tipo binario (32) nella tabella SQL di destinazione. Non dovrebbe essere presente alcun vincolo su questa colonna. Assegnare un nome di seguito in questa colonna come 'ColumnForADFuseOnly' in questo esempio.
2.  Da usare nell'attività di copia come indicato di seguito:

        "sink":  
        { 
          "type": "SqlSink", 
          "sliceIdentifierColumnName": "ColumnForADFuseOnly"
        }

Azure Data Factory verrà inserito in questa colonna in base alla necessità di assicurarsi che la sincronizzazione di origine e destinazione. I valori della colonna non è usati all'esterno di questo contesto dall'utente. 

Come meccanismo di 1, Copia attività verranno automaticamente prima di tutto la pulizia dei dati per la sezione specificato dalla tabella SQL di destinazione e quindi eseguire le attività di copia normalmente per inserire i dati dall'origine alla destinazione per tale sezione. 
