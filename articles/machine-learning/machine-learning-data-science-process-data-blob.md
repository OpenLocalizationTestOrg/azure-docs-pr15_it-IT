<properties 
    pageTitle="Elaborare dati blob Azure con analitica avanzate | Microsoft Azure" 
    description="Elaborazione dei dati di archiviazione Blob di Azure." 
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

#<a name="heading"></a>Elaborazione dei dati blob Azure con analitica avanzate

In questo documento vengono illustrate exploring dati e la funzionalità la generazione di dati archiviati in archiviazione Blob di Azure. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Caricare i dati in un frame di dati Pandas
Per esplorare e modificare un set di dati, è necessario scaricarlo dall'origine blob in un file locale che può essere caricato in una cornice di dati Pandas. Di seguito sono i passaggi da seguire per questa procedura:

1. Scarica i dati di Azure blob con Python codice di esempio utilizza il servizio di blob. Sostituire la variabile nel codice seguente con i valori specificati: 

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


2. Leggere i dati in un frame di dati Pandas dal file scaricato.

        #LOCALFILE is the file path 
        dataframe_blobdata = pd.read_csv(LOCALFILE)

A questo punto si è pronti per esplorare i dati e generare funzionalità in questo set di dati.


##<a name="blob-dataexploration"></a>Esplorazione dei dati

Di seguito sono forniti alcuni esempi di modi per esplorare i dati utilizzando Pandas:

1. Controllare il numero di righe e colonne 

        print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. Controllare il prima o l'ultima poche righe nel set di dati come indicato di seguito:

        dataframe_blobdata.head(10)
        
        dataframe_blobdata.tail(10)

3. Verificare il tipo di dati che ogni colonna è stato importato con codice di esempio
    
        for col in dataframe_blobdata.columns:
            print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. Controllare le statistiche di base per le colonne nel set di dati come indicato di seguito
 
        dataframe_blobdata.describe()
    
5. Prendere nota del numero di voci per ogni valore della colonna come indicato di seguito

        dataframe_blobdata['<column_name>'].value_counts()

6. Contare valori mancanti o il numero effettivo di voci in ogni colonna utilizzando il seguente codice di esempio

        miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
        print miss_num
     
7.  Se si dispone di valori mancanti per una determinata colonna di dati, è possibile rilasciarli nel modo seguente:

        dataframe_blobdata_noNA = dataframe_blobdata.dropna()
        dataframe_blobdata_noNA.shape

    È possibile sostituire i valori mancanti con la funzione modalità:
    
        dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})        

8. Creare un tracciato istogramma utilizzando un numero variabile di collocazione per tracciare la distribuzione di una variabile 
    
        dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
        
        np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    
9. Esaminare correlazioni tra le variabili utilizzando un scatterplot o utilizzando la funzione correlazione incorporata

        #relationship between column_a and column_b using scatter plot
        plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
        
        #correlation between column_a and column_b
        dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    
    
##<a name="blob-featuregen"></a>Generazione di funzionalità
    
È possibile generare funzionalità mediante Python come indicato di seguito:

###<a name="blob-countfeature"></a>Valore dell'indicatore di base generazione caratteristica

Funzionalità per categoria possono essere create nel modo seguente:

1. Controllare la distribuzione della colonna per categoria:
    
        dataframe_blobdata['<categorical_column>'].value_counts()

2. Generazione dei valori degli indicatori per ognuno dei valori di colonna

        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. Accedere a colonna indicatori con frame di dati originale 
 
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. Rimuovere la variabile originale:

        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
    
###<a name="blob-binningfeature"></a>Generazione di funzionalità binning

Per la generazione di funzionalità binned, è procedere come segue:

1. Aggiungere una sequenza di colonne da una colonna numerica bin
 
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
        
2. Convertire binning a una sequenza di variabili di tipo boolean

        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
    
3. Aggiungere alla fine, le variabili fittizie frame dati originale

        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool) 


##<a name="sql-featuregen"></a>Scrivere dati blob Azure e utilizzo in Azure l'apprendimento

Dopo aver esaminato i dati e creazione le funzionalità necessarie, è possibile caricare i dati (viene eseguito o featurized) a un Azure blob e di utilizzo in Azure apprendimento procedendo nel modo seguente: si noti che è possibile creare funzionalità aggiuntive in anche di Studio Azure Learning automatica. 
1. Scrivere la cornice di dati nel file locale

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

3. Ora possono leggere i dati blob utilizzando l' apprendimento Azure [Importa dati] [ import-data] modulo come illustrato nella schermata seguente:
 
![blob lettore][1]

[1]: ./media/machine-learning-data-science-process-data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 
