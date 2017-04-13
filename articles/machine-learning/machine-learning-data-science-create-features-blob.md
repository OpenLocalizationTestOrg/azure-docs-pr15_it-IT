<properties
    pageTitle="Creare le funzionalità per i dati di spazio di archiviazione blob Azure utilizzando Panda | Microsoft Azure"
    description="Come creare caratteristiche per i dati archiviati nel contenitore di blob Azure con il pacchetto Panda Python."
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
    ms.author="bradsev;garye" />

#<a name="create-features-for-azure-blob-storage-data-using-panda"></a>Creare le funzionalità per i dati di spazio di archiviazione blob Azure utilizzando Panda

In questo documento viene illustrato come creare caratteristiche per i dati archiviati nel contenitore di blob Azure utilizzando il pacchetto di [Pandas](http://pandas.pydata.org/) Python. Una volta strutturato come caricare i dati in un intervallo di dati Panda, viene illustrato come generare caratteristiche per categoria usando gli script Python con valori di indicatori e binning caratteristiche.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]Questo **menu** fornisce collegamenti ad argomenti che descrivono come creare caratteristiche per i dati in ambienti diversi. Questa attività è un passaggio di [Team dati scienza processo (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="prerequisites"></a>Prerequisiti

In questo articolo presuppone che sia stato creato un account di archiviazione blob Azure e archiviare i dati sono. Se sono necessarie istruzioni per configurare un account, vedere [creare un account di archiviazione Azure](../storage/storage-create-storage-account.md#create-a-storage-account)


## <a name="load-the-data-into-a-pandas-data-frame"></a>Caricare i dati in un intervallo di dati Pandas
Per esplorare e modificare un set di dati, è necessario scaricarlo dal sito di origine blob in un file locale che può essere caricato in un intervallo di dati Pandas. Ecco i passaggi da seguire per eseguire questa procedura:

1. Scaricare i dati di Azure blob con il codice Python di esempio seguente, utilizzando blob servizio. Sostituire la variabile di codice riportato di seguito con i valori specifici:

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

##<a name="blob-featuregen"></a>Generazione di funzionalità

Le due sezioni successive viene illustrato come generare caratteristiche binning usando gli script Python e caratteristiche esclusive dei valori degli indicatori per categoria.

###<a name="blob-countfeature"></a>Valore dell'indicatore in base a caratteristica generazione

Caratteristiche per categoria possono essere create come indicato di seguito:

1. Esaminare la distribuzione della colonna categorie:

        dataframe_blobdata['<categorical_column>'].value_counts()

2. Generare dei valori degli indicatori per ognuno dei valori di colonna

        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. Partecipare a colonna degli indicatori con riquadro dati originale

            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. Rimuovere la variabile originale:

        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)

###<a name="blob-binningfeature"></a>Generazione di funzionalità binning

Per la generazione caratteristiche binned, procedere come segue:

1. Aggiungere una sequenza di colonne in una colonna numerica di bin

        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)

2. Convertire binning a una sequenza di variabili boolean

        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')

3. Aggiungere infine variabili fittizie frame dati originale

        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)

##<a name="sql-featuregen"></a>Scrivere dati blob Azure e l'utilizzo di apprendimento Azure

Dopo avere esaminato i dati e creazione le funzionalità necessarie, è possibile caricare i dati (campionati o featurized) in un Azure blob e utilizzarlo in Azure apprendimento procedendo come segue: si noti che è possono creare in Azure Machine Learning Studio anche caratteristiche aggiuntive.
1. Scrivere il frame di dati nel file locale

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Caricare i dati blob Azure come indicato di seguito:

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
            print ("Something went wrong with uploading blob:"+BLOBNAME)

3. A questo punto è possono leggere i dati del BLOB uso del modulo di Azure apprendimento [Importa dati](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) , come illustrato nell'immagine seguente:

![blob reader](./media/machine-learning-data-science-process-data-blob/reader_blob.png)
