<properties 
    pageTitle="Dati di esempio in Azure nell'archiviazione blob | Microsoft Azure" 
    description="Dati di esempio in archiviazione Blob Azure" 
    services="machine-learning,storage" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Dati di esempio in Azure nell'archiviazione blob.


Dati di esempio archiviati in archiviazione blob Azure scaricarla a livello di programmazione e quindi campionamento utilizzando procedure scritte in Python illustrati in questo documento.

**Perché i dati di esempio?**
Se il set di dati che si intende analizzare grandi dimensioni, in genere è consigliabile campioni verso il basso i dati per ridurre le dimensioni inferiori ma representative e più gestibili. In questo modo si facilita informazioni sui dati, ricerca e progettazione di funzionalità. Il proprio ruolo nel processo di Analitica Cortana è consentire la creazione di prototipi veloce delle funzioni di elaborazione dei dati e modelli di apprendimento automatico.

**Menu** collegamenti ad argomenti che descrivono come i dati da vari ambienti di spazio di archiviazione di esempio seguenti. 

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Questa attività di esempio è un passaggio di [Team dati scienza processo (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="download-and-down-sample-data"></a>Download e dati di esempio verso il basso
1. Scaricare i dati da archiviazione blob Azure mediante il servizio di blob dal codice di Python di esempio seguente: 

        from azure.storage.blob import BlobService
        import tables
        
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))

2. Leggere i dati in un intervallo di dati Pandas da file scaricato in precedenza.

        import pandas as pd

        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Campioni down dei dati mediante la `numpy`del `random.choice` come indicato di seguito:

        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

A questo punto è possibile lavorare con il frame di dati precedente con l'esempio dell'1% per un'ulteriore analisi e generazione di funzionalità.

##<a name="heading"></a>Caricare dati e leggere in apprendimento Azure

È possibile utilizzare il codice di esempio seguente i dati di esempio a verso il basso e usare direttamente in Azure ML:

1. Scrivere il frame di dati in un file locale

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Caricare il file locale in un archivio blob Azure utilizzando il codice di esempio seguente:

        from azure.storage.blob import BlobService
        import tables

        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
        
        try:
       
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
        
        except:         
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Leggere i dati del blob Azure tramite Azure ML [Importa dati](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) , come illustrato nell'immagine seguente:
 
![blob reader](./media/machine-learning-data-science-sample-data-blob/reader_blob.png)

 
