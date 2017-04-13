<properties 
    pageTitle="Esplorare i dati in archiviazione blob Azure con Pandas | Microsoft Azure" 
    description="Come esplorare i dati archiviati nel contenitore di blob Azure utilizzando Pandas." 
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
    ms.date="09/13/2016" 
    ms.author="bradsev" /> 

#<a name="explore-data-in-azure-blob-storage-with-pandas"></a>Esplorare i dati in archiviazione blob Azure con Pandas

In questo documento viene illustrato come esplorare i dati archiviati nel contenitore di blob Azure utilizzando [Pandas](http://pandas.pydata.org/) Python pacchetto.

I collegamenti seguenti **menu** ad argomenti che descrivono come utilizzare gli strumenti per esplorare i dati da vari ambienti di spazio di archiviazione. Questa attività è un passaggio del [Processo di scienze dei dati]().

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


## <a name="prerequisites"></a>Prerequisiti
In questo articolo si presuppone che sia:

* Creare un account di archiviazione Azure. Se è necessario istruzioni, vedere [creare un account di archiviazione Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Archiviati i dati in un account di archiviazione blob Azure. Se è necessario istruzioni, vedere [spostamento dati da e verso lo spazio di archiviazione di Azure](../storage/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Caricare i dati in un DataFrame Pandas
Per esplorare e modificare un set di dati, è necessario innanzitutto scaricarlo dal sito di origine blob in un file locale, può essere caricato in un DataFrame Pandas. Ecco i passaggi da seguire per eseguire questa procedura:

1. Scaricare i dati di Azure blob con nell'esempio seguente Python con blob servizio. Sostituire la variabile di codice riportato di seguito con i valori specifici: 

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


2. Leggere i dati in un intervallo di dati Pandas dal file scaricato.

        #LOCALFILE is the file path 
        dataframe_blobdata = pd.read_csv(LOCALFILE)

A questo punto si è pronti esplorare i dati e generare caratteristiche in questo set di dati.

##<a name="blob-dataexploration"></a>Esempi di esplorazione dei dati utilizzando Pandas

Ecco alcuni esempi di modi per esplorare i dati mediante Pandas:

1. Controllare il **numero di righe e colonne** 

        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. **Controlla** il primo o l'ultimo alcune **righe** nel set di dati seguente:

        dataframe_blobdata.head(10)
        
        dataframe_blobdata.tail(10)

3. Controllare il **tipo di dati** di che ogni colonna è stato importato come utilizzando il seguente codice di esempio
    
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. Controllare le **statistiche di base** per le colonne nel set di dati come segue
 
        dataframe_blobdata.describe()
    
5. Esaminare il numero di voci per ogni valore della colonna come indicato di seguito

        dataframe_blobdata['<column_name>'].value_counts()

6. **Contare valori mancanti** e il numero effettivo di voci in ogni colonna utilizzando il seguente codice di esempio

        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
     
7.  Se si dispone **contiene valori** di una colonna specifica nei dati, è possibile rilasciarli come indicato di seguito:

        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape

    È possibile sostituire i valori mancanti con la funzione modalità:
    
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        

8. Creare un grafico **istogramma** utilizzando variabile numero di bin per tracciare la distribuzione di una variabile 
    
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
        
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    
9. Osservare **correlazioni** tra le variabili utilizzando un scatterplot o utilizzare la funzione correlazione incorporata

        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
        
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()
