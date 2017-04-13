<properties
   pageTitle="Creare HDInsight cluster con archivio di Azure Lake dati tramite il portale | Azure"
   description="Portale di Azure consente di creare e utilizzare HDInsight cluster con archivio Lake dati di Azure"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="nitinme"/>

# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-portal"></a>Creare un cluster di HDInsight con archivio Lake dati nel portale di Azure

> [AZURE.SELECTOR]
- [Nel portale](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Utilizzo di PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)
- [Gestione risorse](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)


Informazioni su come usare portale Azure per creare un cluster di HDInsight (Hadoop, HBase, motori o eccesso) con l'accesso all'archivio Lake dati di Azure. Alcune considerazioni importanti per questa versione:

* **Per i cluster (Linux) e Hadoop cluster (Windows e Linux)**, l'archivio Lake dati può essere usata solo come un account di spazio di archiviazione aggiuntivo. L'account di archiviazione predefinito per i quali cluster continuerà a essere Azure lo spazio di archiviazione BLOB (WASB).

* **Cluster per eccesso (Windows e Linux)**, l'archivio Lake dati può essere utilizzata per scrivere i dati da una topologia eccesso. Archivio dati Lake è utilizzabile anche per archiviare i dati di riferimento che possono essere letti da una topologia eccesso. Per ulteriori informazioni, vedere [Utilizzare dati Lake archivio in una topologia eccesso](#use-data-lake-store-in-a-storm-topology).

* **Per HBase cluster (Windows e Linux)**, l'archivio Lake dati può essere utilizzata come un spazio di archiviazione predefinito, nonché spazio di archiviazione aggiuntivo. Per ulteriori informazioni, vedere [Utilizzare dati Lake Store con i cluster HBase](#use-data-lake-store-with-hbase-clusters).

> [AZURE.NOTE] Ecco alcuni punti importanti da tenere presente. 
> 
> * Opzione per creare cluster HDInsight con l'accesso ai dati Lake Store è disponibile solo per le versioni HDInsight 3.2 e 3.4 (per eccesso, Hadoop e HBase cluster in Windows, nonché Linux). Per i cluster di motori su Linux, questa opzione è disponibile solo nei cluster 3.4 HDInsight.
>
> * Come detto in precedenza, archivio Lake dati è disponibile come spazio di archiviazione predefinito per alcuni tipi di grafico (HBase) e spazio di archiviazione aggiuntivo per gli altri tipi di grafico (Hadoop, motori, eccesso). Uso di archivio dati Lake come un account di spazio di archiviazione aggiuntivo non ha effetti prestazioni o la possibilità di lettura/scrittura per lo spazio di archiviazione dal cluster. In uno scenario in archivio Lake dati viene utilizzata come ulteriore spazio di archiviazione, file correlati a cluster (ad esempio, i registri e così via) vengono salvati spazio di archiviazione predefinito (BLOB Azure), mentre i dati che si desidera elaborare possono essere creati in un account di archivio di dati Lake.


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Account azure dati Lake Store**. Seguire le istruzioni nella [Guida introduttiva di Azure dati Lake archivio tramite il portale di Azure](data-lake-store-get-started-portal.md). 

- **Caricare alcuni dati di esempio al proprio account Azure dati Lake Store**. Dopo aver creato l'account, eseguire le attività seguenti per caricare alcuni dati di esempio. È necessario questi dati in un secondo momento nell'esercitazione per eseguire i processi da un cluster di HDInsight l'accesso ai dati nell'archivio Lake dati.

    * [Creare una cartella nell'archivio dati Lake](data-lake-store-get-started-portal.md#createfolder).
    * [Caricare un file di archivio dati Lake](data-lake-store-get-started-portal.md#uploaddata). Se si sta cercando alcuni dati di esempio caricare, è possibile ottenere la cartella **Ambulanza dati** dal [Repository fra Lake dei dati di Azure](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

- **Principale del servizio di azure Active Directory**. Procedura descritta in questa esercitazione le istruzioni su come creare un'entità servizio in Azure Active Directory. Tuttavia, è necessario essere un amministratore di Azure Active Directory per poter creare un'entità servizio. Se si è un amministratore di Azure Active Directory, è possibile ignorare questo prerequisito e procedere con l'esercitazione.
    
    **Se non è un amministratore di Azure Active Directory**, non sarà possibile eseguire i passaggi necessari per creare un'entità servizio. In questo caso, l'amministratore di Azure Active Directory innanzitutto necessario creare un'entità servizio prima di creare un cluster di HDInsight con dati Lake archivio. Inoltre, il capitale servizio deve essere creato utilizzando un certificato, come descritto in [creare un servizio dell'entità con certificato](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

## <a name="do-you-learn-faster-with-videos"></a>Possono reperire più velocemente con video?

Guardare i video seguenti per imparare a effettuare il provisioning di cluster HDInsight con l'accesso all'archivio Lake dati.

* [Creare un cluster di HDInsight con l'accesso ai dati Lake archivio](https://mix.office.com/watch/l93xri2yhtp2)
* Dopo aver cluster configurato, [dati di Access nell'archivio Lake dati usando gli script Hive e maialino](https://mix.office.com/watch/1n9g5w0fiqv1q)

## <a name="create-an-hdinsight-cluster-with-access-to-azure-data-lake-store"></a>Creare un cluster di HDInsight con l'accesso all'archivio Lake dati di Azure

In questa sezione, si crea un cluster di HDInsight Hadoop che utilizza l'archivio Lake dati come un'ulteriore spazio di archiviazione. In questa versione, per un cluster Hadoop archivio Lake dati può essere usata solo come un ulteriore spazio di archiviazione per il cluster. Spazio di archiviazione predefinito sarà comunque i BLOB Azure dello spazio di archiviazione (WASB). Affermativo, è necessario creare prima di tutto l'account di archiviazione e contenitori di archiviazione necessari per il cluster.

1. Effettuare l'accesso con il nuovo [Portale Azure](https://portal.azure.com).

2. Seguire la procedura illustrata [cluster creare Hadoop in HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal) per avviare il provisioning di un cluster di HDInsight.

3. Scegliere **Origine dati**e il **Facoltativi** . In e **l'Origine dati** , specificare i dettagli per l'account di archiviazione e contenitore di spazio di archiviazione, specificare un **percorso** come **Stati Uniti orientali 2**e quindi fare clic su **Cluster AAD identità**.

    ![Aggiungi servizio principale a cluster HDInsight] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Aggiungi servizio principale a cluster HDInsight")

4. In e il **Cluster AAD identità** , è possibile selezionare un capitale servizio esistente o crearne uno nuovo.

    * **Creare una nuova identità di servizio**

        * In e il **Cluster AAD identità** , fare clic su **Crea nuovo**e quindi fornire i valori per creare una nuova identità di servizio e **creare un'entità servizio** il **Principale del servizio**. Come parte di tale, un certificato e un'applicazione di Azure Active Directory anche viene creato. Fare clic su **Crea**.

            ![Aggiungi servizio principale a cluster HDInsight] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Aggiungi servizio principale a cluster HDInsight")

        * Scegliere **Gestire l'accesso al ADLS**e il **Cluster AAD identità** . Il riquadro mostra gli account di archivio di dati Lake associati all'abbonamento. Tuttavia, è possibile impostare le autorizzazioni solo per l'account creato. Selezionare le autorizzazioni di lettura/scrittura ed esecuzione per l'account che si desidera associare il cluster HDInsight e quindi fare clic su **Salva autorizzazioni**.

            ![Aggiungi servizio principale a cluster HDInsight] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Aggiungi servizio principale a cluster HDInsight")

        * Scegliere **Il certificato di Download** per scaricare il certificato associato al servizio principale che è stato creato e il **Cluster AAD identità** . Questo è utile se si desidera utilizzare la stessa entità di servizio in futuro, durante la creazione di ulteriori cluster HDInsight. Fare clic su **Seleziona**.

            ![Aggiungi servizio principale a cluster HDInsight] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "Aggiungi servizio principale a cluster HDInsight")


    * **Scegliere un capitale servizio esistente**

        * In e il **Cluster AAD identità** , fare clic su **Usa esistente**, fare clic su **Principale del servizio**e quindi in e **Selezionare un'entità servizio** il cercare un capitale servizio esistente. Fare clic su un nome dell'entità servizio e quindi fare clic su **Seleziona**.

            ![Aggiungi servizio principale a cluster HDInsight] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Aggiungi servizio principale a cluster HDInsight")

        * Scegliere e il **Cluster AAD identità** , caricare il certificato (PFX) associato principale del servizio che è selezionata e quindi fornire la password del certificato.

5. Fare clic su **Gestisci accesso ADLS** e quindi fare clic su **Seleziona autorizzazioni del file**. 

    ![Aggiungi servizio principale a cluster HDInsight] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.existing.save.png "Aggiungi servizio principale a cluster HDInsight")

6. In e **selezionare file autorizzazioni** dall'elenco a discesa **Account** selezionare l'account di archivio Lake dati che si desidera associato al cluster HDInsight. E il sono elencati i file e cartelle disponibili nella finestra account archivio Lake dati selezionati. 
 
    ![Specifica l'accesso all'archivio dati Lake] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-1.png "Specifica l'accesso all'archivio dati Lake")

    Successivamente, determinare le autorizzazioni per il file selezionati e delle cartelle. Per le cartelle, anche specificare se le autorizzazioni applicano nella cartella solo o la cartella e tutti gli elementi figlio nella cartella. Effettuare questa selezione selezionando il valore appropriato da **Applicare a** discesa. Per rimuovere un'autorizzazione, fare clic sull'icona **Elimina**

    ![Specifica l'accesso all'archivio dati Lake] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-2.png "Specifica l'accesso all'archivio dati Lake")

    Ripetere questi passaggi per i file associati e le cartelle da altri account di archivio di dati Lake anche. Dopo aver completato l'assegnazione delle autorizzazioni, fare clic su **Selezionare** nella parte inferiore della stessa e.

7. In e **assegnare le autorizzazioni selezionate** l'esaminare le autorizzazioni che si disponibili e quindi fare clic su **Esegui** per concedere le autorizzazioni.

    ![Specifica l'accesso all'archivio dati Lake] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi-adl-permission-3.png "Specifica l'accesso all'archivio dati Lake")

    Nella colonna stato visualizzato lo stato di avanzamento. Una volta completata assegnate tutte le autorizzazioni, fare clic su **Chiudi**. 

6. Fare clic su **Seleziona** sui blade **Cluster AAD identità** e **Origine dati** e quindi continuare con creazioni cluster come descritto nel [cluster di creare Hadoop in HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

7. Una volta che viene completato il provisioning cluster, è possibile verificare che il capitale servizio è associato al cluster HDInsight. A tale scopo, da e il grafico, fare clic su **Cluster AAD identità** illustrano il capitale servizio associato.

    ![Aggiungi servizio principale a cluster HDInsight] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Aggiungi servizio principale a cluster HDInsight")

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-azure-data-lake-store"></a>Eseguire i processi di test su cluster HDInsight usi l'archivio Lake dati di Azure

Dopo aver configurato un cluster di HDInsight, è possibile eseguire test processi sul cluster per verificare che il cluster HDInsight può accedere ai dati nell'archivio Lake dati di Azure. A tale scopo, si eseguirà alcune query hive tale Lake archivio di destinazione.

### <a name="for-a-linux-cluster"></a>Per un cluster Linux

1. Aprire e il cluster per il cluster che appena viene completato il provisioning e quindi fare clic su **Dashboard**. Verrà aperta Ambari per il cluster Linux. Quando si accede Ambari, verrà richiesto per eseguire l'autenticazione al sito. Immettere l'amministratore (amministratore predefinito), nome dell'account e password usati per creare il cluster.

    ![Dashboard di cluster di avvio] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Dashboard di cluster di avvio")

    È anche possibile passare direttamente a Ambari facendo clic su https://CLUSTERNAME.azurehdinsight.net in un web browser (dove **nome cluster** è il nome del cluster HDInsight).

2. Aprire la visualizzazione Hive. Selezionare il set di quadrati dal menu della pagina (accanto a collegamento **amministratore** e pulsante a destra della pagina) le visualizzazioni elenco disponibile. Selezionare la visualizzazione **Hive** .

    ![Se si selezionano ambari visualizzazioni](./media/data-lake-store-hdinsight-hadoop-use-portal/selecthiveview.png)

3. Verrà visualizzata una pagina simile al seguente:

    ![Immagine della pagina di visualizzazione hive, che contiene una sezione dell'editor di query](./media/data-lake-store-hdinsight-hadoop-use-portal/hiveview.png)

4. Incollare l'istruzione HiveQL seguente nella sezione **Dell'Editor di Query** della pagina del foglio di lavoro:

        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

5. Fare clic sul pulsante **Esegui** nella parte inferiore dell' **Editor di Query** per avviare la query. Una sezione **Query processo risultati** dovrà essere visualizzato di sotto **Dell'Editor di Query** e visualizzare informazioni sul processo.

6. Al termine di query, la sezione **Query processo risultati** visualizzerà i risultati dell'operazione. Nella scheda **risultati** deve contenere le informazioni seguenti:

7. Eseguire la query seguente per verificare che la tabella è stata creata.

        SHOW TABLES;

    Nella scheda **risultati** deve essere visualizzata le operazioni seguenti:

        hivesampletable
        vehicles

    **veicoli** costituisce la tabella creata in precedenza. **hivesampletable** è una tabella di esempio disponibile in tutti i cluster HDInsight per impostazione predefinita.

8. È anche possibile eseguire una query per recuperare i dati dalla tabella **veicoli** .

        SELECT * FROM vehicles LIMIT 5;

### <a name="for-a-windows-cluster"></a>Per un cluster di Windows

1. Aprire e il cluster per il cluster che appena viene completato il provisioning e quindi fare clic su **Dashboard**.

    ![Dashboard di cluster di avvio] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Dashboard di cluster di avvio")

    Quando richiesto, immettere le credenziali di amministratore per il cluster.

2. Verrà aperta la Console di Query di Microsoft Azure HDInsight. Fare clic su **Hive Editor**.

    ![Editor Open Hive] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster2.png "Editor Open Hive")

3. Nell'Editor Hive immettere la query seguente e quindi fare clic su **Invia**.

        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

    In questa query Hive, viene creata una tabella dai dati memorizzati in archivio Lake dati in `adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder`. Questo percorso è un file di dati di esempio caricati deve in precedenza.

    La tabella **Della sessione di processo** nella parte inferiore mostra lo stato del processo di modifica da **inizializzazione**, sia in **esecuzione**su **completata**. È anche possibile fare clic su **Visualizza dettagli** per visualizzare ulteriori informazioni sul processo completato.

    ![Creare una tabella] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster3.png "Creare una tabella")

4. Eseguire la query seguente per verificare che la tabella è stata creata.

        SHOW TABLES;

    Fare clic su **Visualizza dettagli** corrispondente a questa query e l'output deve essere visualizzata come segue:

        hivesampletable
        vehicles

    **veicoli** costituisce la tabella creata in precedenza. **hivesampletable** è una tabella di esempio disponibile in tutti i cluster HDInsight per impostazione predefinita.

5. È anche possibile eseguire una query per recuperare i dati dalla tabella **veicoli** .

        SELECT * FROM vehicles LIMIT 5;


## <a name="access-data-lake-store-using-hdfs-commands"></a>Accesso dati Lake archivio usando i comandi HDFS

Dopo aver configurato il cluster HDInsight per utilizzare dati Lake archivio, è possibile utilizzare i comandi di shell HDFS per accedere all'archivio.

### <a name="for-a-linux-cluster"></a>Per un cluster Linux

In questa sezione è verrà SSH nel cluster ed eseguire i comandi HDFS. Windows non è disponibile un client SSH incorporato. È consigliabile utilizzare **PuTTY**, che può essere scaricato dal [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per ulteriori informazioni sull'uso di PuTTY, vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Una volta connessa, il comando seguente HDFS file System per visualizzare un elenco di file di archivio di Lake dati.

    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Verranno visualizzate il file caricato in precedenza all'archivio Lake dati.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

È inoltre possibile utilizzare il `hdfs dfs -put` comando caricare alcuni file all'archivio Lake dati e quindi utilizzare `hdfs dfs -ls` per verificare se i file sono stati caricati correttamente.


### <a name="for-a-windows-cluster"></a>Per un cluster di Windows

1. Effettuare l'accesso con il nuovo [Portale Azure](https://portal.azure.com).

2. Fare clic su **Sfoglia**e quindi fare clic sul cluster HDInsight creato **cluster HDInsight**.

3. In e il cluster fare clic su **Desktop remoto**e quindi scegliere **Connetti**e il **Desktop remoto** .

    ![Remote in cluster HDI] (./media/data-lake-store-hdinsight-hadoop-use-portal/ADL.HDI.PS.Remote.Desktop.png "Creare un gruppo di risorse Azure")

    Quando richiesto, immettere le credenziali fornite per l'utente desktop remoto.

4. Nella sessione remota avviare Windows PowerShell e utilizzare i comandi di file System HDFS per elencare i file nell'archivio Lake dati di Azure.

        hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

    Verranno visualizzate il file caricato in precedenza all'archivio Lake dati.

        15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
        Found 1 items
        -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

    È inoltre possibile utilizzare il `hdfs dfs -put` comando caricare alcuni file all'archivio Lake dati e quindi utilizzare `hdfs dfs -ls` per verificare se i file sono stati caricati correttamente.

## <a name="use-data-lake-store-with-spark-cluster"></a>Usare dati Lake Store con cluster ad

In questa sezione, è possibile Jupyter blocco appunti disponibile con i cluster HDInsight Spark per eseguire un processo che legge i dati da un account di archivio di dati Lake associato a un cluster di HDInsight Spark, anziché l'account Azure lo spazio di archiviazione Blob predefinito.

1. Copiare alcuni dati di esempio dall'account di archiviazione predefinito (WASB) associato al cluster motori all'account di archiviazione Azure dati Lake associato al cluster. È possibile utilizzare lo [strumento ADLCopy](http://aka.ms/downloadadlcopy) per farlo. Scaricare e installare lo strumento dal collegamento.

2. Aprire un prompt dei comandi e passare alla cartella in cui AdlCopy è installato, in genere `%HOMEPATH%\Documents\adlcopy`.

3. Eseguire il seguente comando per copiare un blob specifico il contenitore di origine in un archivio di Lake dati:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Per questa esercitazione, copiare il file di dati di esempio **HVAC.csv** in **/HdiSamples/HdiSamples/SensorSampleData/hvac/** nell'account Azure dati Lake Store. Il frammento di codice dovrebbe essere simile:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[AZURE.WARNING] Verificare che si che sono i nomi di file e il percorso con l'iniziale maiuscola.

4. Verrà richiesto di immettere le credenziali per l'abbonamento Azure in cui si dispone di account archivio Lake dati. Verrà visualizzato un output simile al seguente:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Il file di dati (**HVAC.csv**) verrà copiato in una cartella **/hvac** nella finestra account archivio Lake dati.

4. Dal [Portale di Azure](https://portal.azure.com/), da startboard, fare clic sul riquadro per il cluster motori (se è bloccato il startboard). È inoltre possibile passare al cluster in **Esplora tutto** > **Cluster HDInsight**.   

2. Fare clic su **Collegamenti rapidi**, e il cluster motori e da e il **Cluster Dashboard** , fare clic su **Blocco appunti Jupyter**. Se richiesto, immettere le credenziali di amministratore per il cluster.

    > [AZURE.NOTE] Si può anche raggiungere il blocco appunti Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire __nome cluster__ con il nome del cluster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Creare un nuovo blocco appunti. Fare clic su **Nuovo**e quindi fare clic su **PySpark**.

    ![Creare un nuovo blocco appunti Jupyter] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdispark.note.jupyter.createnotebook.png "Creare un nuovo blocco appunti Jupyter")

3. Un nuovo blocco appunti viene creato e aperto con il nome **Untitled.pynb**. 

4. Perché è stato creato un blocco appunti usando kernel PySpark, non è necessario creare in modo esplicito qualsiasi contesti. Contesti vivacità ed Hive verranno creati automaticamente automaticamente quando si esegue la prima cella di codice. È possibile iniziare importando i tipi di necessari per questo scenario. A tale scopo, incollare il frammento di codice seguente in una cella e premere **MAIUSC + INVIO**.

        from pyspark.sql.types import *
        
    Ogni volta che si esegue un processo in Jupyter, il titolo della finestra del browser web verrà visualizzato uno stato **(disponibilità)** accanto al titolo del blocco appunti. Verrà anche visualizzato un cerchio in tinta unita accanto al testo **PySpark** nell'angolo superiore destro. Una volta completato il processo, questo verrà modificato in un cerchio vuoto.

     ![Stato di un processo di blocco appunti Jupyter] (./media/data-lake-store-hdinsight-hadoop-use-portal/hdispark.jupyter.job.status.png "Stato di un processo di blocco appunti Jupyter")

4. Caricare dati di esempio in una tabella temporanea utilizzando il file **HVAC.csv** che sono stati copiati nell'account archivio Lake dati. È possibile accedere ai dati nella finestra account archivio Lake dati utilizzando il modello di URL seguente.

        adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

    In una cella vuota, incollare l'esempio seguente, sostituire **MYDATALAKESTORE** con il nome dell'account archivio Lake dati e premere **MAIUSC + INVIO**. In questo esempio Registra i dati in una tabella temporanea denominata **hvac**.

        # Load the data
        hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/hvac/HVAC.csv")
        
        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
        
        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
        
        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
        
        # Register the data fram as a table to run queries against
        hvacdf.registerTempTable("hvac")

5. Poiché si utilizza un kernel PySpark, è ora possibile direttamente eseguire una query SQL nella tabella temporanea **hvac** appena creata utilizzando il `%%sql` speciale. Per ulteriori informazioni sul `%%sql` speciale, nonché altri magics disponibile con kernel PySpark vedere [disponibile nei blocchi appunti Jupyter con i cluster ad HDInsight. X](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).
        
        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

5. Al termine il processo, viene visualizzato il seguente output tabulare per impostazione predefinita.

    ![Output di tabella del risultato della query] (./media/data-lake-store-hdinsight-hadoop-use-portal/tabular.output.png "Output di tabella del risultato della query")

    È anche possibile visualizzare i risultati in anche altre visualizzazioni. Ad esempio, un grafico ad area per lo stesso output risulterebbe simile al seguente.

    ![Grafico ad aree del risultato della query] (./media/data-lake-store-hdinsight-hadoop-use-portal/area.output.png "Grafico ad aree del risultato della query")


6. Al termine dell'esecuzione dell'applicazione, è necessario arrestare il blocco appunti per liberare le risorse. A tale scopo, dal menu **File** nel blocco appunti, fare clic su **Chiudi e interrompere**. Questo verrà arrestato e chiudere il blocco appunti.

## <a name="use-data-lake-store-in-a-storm-topology"></a>Usare dati Lake archivio in una topologia eccesso

È possibile usare l'archivio Lake dati per scrivere i dati da una topologia eccesso. Per istruzioni su come ottenere questo scenario, vedere [Usare Azure Lake di archivio dati eccesso Apache con HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## <a name="use-data-lake-store-with-hbase-clusters"></a>Usare dati Lake Store con i cluster HBase

Con i cluster HBase, è possibile utilizzare archivio Lake dati come una spazio di archiviazione predefinito, nonché spazio di archiviazione aggiuntivo. Per eseguire questa operazione:

1.  **Percorso di dati HBase**e **l'Origine dati** selezionare **Dati Lake archivio** .
2.  Selezionare il nome dell'archivio Lake dati che si vuole usare oppure crearne uno nuovo.
3.  Infine, specificare la **Cartella principale HBase** all'interno dell'archivio Lake dati. Se l'account di archivio di dati Lake non ha una cartella radice, crearne uno nuovo.

    ![HBase con dati Lake archivio] (./media/data-lake-store-hdinsight-hadoop-use-portal/hbase-data-lake-store.png "Creare un gruppo di risorse Azure")

### <a name="considerations-when-using-data-lake-store-as-default-storage-for-hbase-clusters"></a>Considerazioni relative all'utilizzo di archivio di dati Lake come spazio di archiviazione predefinito per i cluster HBase

* È possibile utilizzare lo stesso account archivio Lake dati per più di un cluster di HBase. Tuttavia, la **Cartella principale HBase** fornito per il cluster (passaggio 4 di acquisizione schermo sopra) deve essere univoco. È **necessario non** utilizzare la stessa cartella radice tra due cluster HBase diversi.
* Anche se si utilizza account archivio dati Lake come spazio di archiviazione predefinito, i file di log cluster HBase rimangono archiviati in Azure lo spazio di archiviazione BLOB (WASB) associato al cluster. Questo viene evidenziato nella casella blu sopra l'acquisizione della schermata.



## <a name="see-also"></a>Vedere anche

* [PowerShell: Creare un cluster di HDInsight per l'uso dei dati Lake archivio](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
