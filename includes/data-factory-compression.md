### <a name="compression-support"></a>Supporto per la compressione  
L'elaborazione di grandi quantità di dati può causare bottiglia i/o e di rete. Di conseguenza, dati compressi in archivi possono non solo velocizzare i trasferimenti di dati della rete e risparmiare spazio su disco, ma anche porta miglioramenti significativi delle prestazioni durante l'elaborazione di dati. Attualmente la compressione è supportata per gli archivi di dati basate su file, ad esempio Blob Azure o locale File System.  

> [AZURE.NOTE] Impostazioni di compressione non sono supportate per i dati nel **AvroFormat**, **OrcFormat**o **ParquetFormat**. 

Per specificare la compressione per un set di dati, utilizzare la proprietà di **compressione** nel set di dati JSON come illustrato nell'esempio seguente:   

    {  
        "name": "AzureBlobDataSet",  
        "properties": {  
            "availability": {  
                "frequency": "Day",  
                "interval": 1  
            },  
            "type": "AzureBlob",  
            "linkedServiceName": "StorageLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                "folderPath": "compression/file/",  
                "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                }  
            }  
        }  
    }  
 
La sezione **compressione** ha due proprietà:  
  
- **Tipo:** codec di compressione, può essere **GZIP**, **Deflate** o **BZIP2**.  
- **Livello:** compressione, che può essere **ottimale** o **più veloce**. 
    - **Più veloce:** L'operazione di compressione deve completare più presto possibile, anche se il file risultante non è compresso in modo ottimale. 
    - **Ottimale**: l'operazione di compressione deve essere in modo ottimale compresso, anche se l'operazione richiede più tempo per completare. 
    
    Per ulteriori informazioni, vedere l'argomento relativo a [Livello di compressione](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . 

Si supponga che il set di dati di esempio precedente viene utilizzato come output di un'attività di copia, l'attività Copia comprime i dati di output con codec GZIP utilizzando rapporto ottimale e quindi inserire i dati compressi in un file denominato pagecounts.csv.gz nell'archivio Blob Azure.   

Quando si specifica proprietà compressione in un set di dati JSON input, la pipeline di possibile leggere i dati compressi dal sito di origine e quando si specifica la proprietà in un set di dati di output JSON, l'attività Copia possibile scrivere dati compressi alla destinazione. Ecco alcuni scenari di esempio: 

- Dati compressi GZIP lettura da un archivio blob Azure decomprimere essa e scrivere dati dei risultati in un database SQL Azure. È possibile definire il set di dati Blob Azure input con la compressione proprietà JSON in questo caso. 
- Leggere i dati da un file di testo dal File System in locale, comprimerli formato GZip e scrivere dati compressi in un archivio blob Azure. È possibile definire un set di dati Blob Azure output la compressione proprietà JSON in questo caso.  
- Leggere un dati compressi GZIP blob Azure, decomprimere il, comprimerli BZIP2 e scrivere dati dei risultati in un archivio blob Azure. Definire il set di dati Blob Azure input con tipo di compressione impostato su GZIP e il set di dati di output con tipo di compressione impostato su BZIP2 in questo caso.   
