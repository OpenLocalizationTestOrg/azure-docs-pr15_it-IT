<properties
    pageTitle="Eseguire un processo Hadoop utilizzando DocumentDB e HDInsight | Microsoft Azure"
    description="Informazioni su come eseguire un processo semplice Hive, maialino e MapReduce con DocumentDB e Azure HDInsight."
    services="documentdb"
    authors="dennyglee"
    manager="jhubbard"
    editor="mimig"
    documentationCenter=""/>


<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="denlee"/>

#<a name="DocumentDB-HDInsight"></a>Eseguire un processo Hadoop utilizzando DocumentDB e HDInsight

In questa esercitazione viene illustrato come eseguire [Apache Hive][apache-hive], [Apache maialino][apache-pig]e [Apache Hadoop] [ apache-hadoop] MapReduce processi su Azure HDInsight con connettore Hadoop del DocumentDB. Connettore Hadoop del DocumentDB consente DocumentDB funga da un'origine e di sink per i processi Hive, maialino e MapReduce. In questa esercitazione verrà utilizzato DocumentDB come origine dati e di destinazione per i processi di Hadoop.

Al termine di questa esercitazione, sarà possibile rispondere alle domande seguenti:

- Come si caricano i dati da DocumentDB tramite un processo Hive, maialino o MapReduce?
- Come memorizzare I dati in DocumentDB tramite un processo Hive, maialino o MapReduce

Si consiglia di iniziare, guardare il video seguente, in cui viene eseguito un un processo Hive utilizzando DocumentDB e HDInsight.

> [AZURE.VIDEO use-azure-documentdb-hadoop-connector-with-azure-hdinsight]

Quindi, tornare a questo articolo, in cui si riceverà i dettagli completi su come è possibile eseguire processi analitica sui dati DocumentDB.

> [AZURE.TIP] In questa esercitazione si presuppone che esista già esperienza nell'uso Hadoop Apache, Hive e/o maialino. Se ha familiarità con Hadoop Apache, Hive e maialino, si consiglia di visitare la [documentazione Apache Hadoop][apache-hadoop-doc]. Anche in questa esercitazione si presuppone che precedente esperienza con DocumentDB e di avere un account DocumentDB. Se si ha familiarità con DocumentDB o non si dispone di un account DocumentDB, estrarre la [Guida introduttiva] [ getting-started] pagina.

Non è possibile completare l'esercitazione e si desidera semplicemente ottenere gli script di PowerShell di esempio completo per Hive, maialino e MapReduce? Non è un problema, ottenerli [qui][documentdb-hdinsight-samples]. Il download contiene anche i file hql, maialino e java per questi esempi.

## <a name="NewestVersion"></a>Versione più recente

<table border='1'>
    <tr><th>Versione Hadoop Connector</th>
        <td>1.2.0</td></tr>
    <tr><th>Uri script</th>
        <td>https://portalcontent.BLOB.Core.Windows.NET/scriptaction/documentdb-Hadoop-Installer-v04.ps1</td></tr>
    <tr><th>Data modificata</th>
        <td>26/04/2016</td></tr>
    <tr><th>Versioni HDInsight supportati</th>
        <td>3.1, 3.2</td></tr>
    <tr><th>Log delle modifiche</th>
        <td>Aggiornato DocumentDB Java SDK per 1.6.0</br>
            Aggiunta del supporto per le raccolte partizionate come origine e sink</br>
        </td></tr>
</table>

## <a name="Prerequisites"></a>Prerequisiti
Prima di seguire le istruzioni in questa esercitazione, verificare di disporre le operazioni seguenti:

- Un account DocumentDB, un database e i documenti all'interno di una raccolta. Per ulteriori informazioni, vedere [Introduzione a DocumentDB][getting-started]. Importare dati di esempio nel proprio account DocumentDB con [DocumentDB importare strumento][documentdb-import-data].
- Velocità effettiva. Lettura e scrittura da HDInsight verrà contate verso l'unità richiesta assegnato per le raccolte. Per ulteriori informazioni, vedere [velocità Provisioned, unità di richiesta e operazioni di database][documentdb-manage-throughput].
- Capacità per una stored procedure aggiuntiva all'interno di ogni output insieme. Stored procedure vengono utilizzate per trasferire i documenti risultanti. Per ulteriori informazioni, vedere [le raccolte e la velocità effettiva provisioning][documentdb-manage-document-storage].
- Capacità per i documenti risultante da processi Hive, maialino o MapReduce. Per ulteriori informazioni, vedere [prestazioni e capacità di gestire DocumentDB][documentdb-manage-collections].
- [*Facoltativo*] Capacità per un ulteriore insieme. Per ulteriori informazioni, vedere [indice sovraccarico e archiviazione di documenti Provisioned][documentdb-manage-document-storage].

> [AZURE.WARNING] Per evitare la creazione di una nuova raccolta durante uno dei processi, è possibile stampare i risultati in stdout, salvare l'output per il contenitore WASB oppure specificare un insieme già esistente. Nel caso si specifica un insieme esistente, verranno creati i nuovi documenti all'interno della raccolta e documenti già esistenti solo interesserà se esiste un conflitto *di ID*. **Il connettore sovrascriverà automaticamente i documenti esistenti con conflitti di id**. È possibile disattivare questa caratteristica impostando l'opzione upsert su false. Se si verifica un conflitto upsert è false, il processo di Hadoop avrà esito negativo; viene rilevato un errore di conflitto id.


## <a name="ProvisionHDInsight"></a>Passaggio 1: Creare un nuovo cluster HDInsight
In questa esercitazione utilizza azione Script dal portale di Azure per personalizzare il cluster HDInsight. In questa esercitazione, il portale di Azure verrà usata per creare il cluster HDInsight. Per istruzioni su come utilizzare i cmdlet di PowerShell o HDInsight .NET SDK, consultare anche i [cluster personalizzare HDInsight tramite Script azione] [ hdinsight-custom-provision] articolo.

1. Accedere al [portale di Azure][azure-portal].

2. Fare clic su **+ Nuovo** nella parte superiore barra di spostamento sinistra, cercare **HDInsight** sulla barra di ricerca principali e il nuovo.

3. **HDInsight** pubblicati da **Microsoft** verrà visualizzato nella parte superiore dei risultati. Fare clic su di esso e quindi fare clic su **Crea**.

4. Nel nuovo HDInsight Cluster creare blade, immettere il **Nome del Cluster** e selezionare l' **abbonamento** che si desidera effettuare il provisioning di questa risorsa in.

    <table border='1'>
        <tr><td>Nome cluster</td><td>Nome del cluster.<br/>
   Nome DNS deve iniziare e terminare con un carattere alfanumerico e possono contenere trattini.<br/>
   Il campo deve essere una stringa da 3 a 63 caratteri.</td></tr>
        <tr><td>Nome dell'abbonamento</td>
            <td>Se si dispone di più di una sottoscrizione di Azure, selezionare l'abbonamento contenenti il cluster HDInsight. </td></tr>
    </table>

5. Fare clic su **Seleziona tipo di grafico** e impostare le proprietà seguenti per i valori specificati.

    <table border='1'>
        <tr><td>Tipo di grafico</td><td><strong>Hadoop</strong></td></tr>
        <tr><td>Livello di cluster</td><td><strong>Standard</strong></td></tr>
        <tr><td>Sistema operativo</td><td><strong>Windows</strong></td></tr>
        <tr><td>Versione</td><td>versione più recente</td></tr>
    </table>

    A questo punto, fare clic su **Seleziona**.

    ![Fornire Hadoop HDInsight dettagli cluster iniziale][image-customprovision-page1]

6. Fare clic su **credenziali** per impostare l'accesso e le credenziali di accesso remoto. Scegliere il **nome utente di accesso Cluster** e la **Password di accesso Cluster**.

    Se si vuole remote nel cluster, selezionare *Sì* nella parte inferiore della stessa e e specificare un nome utente e password.

7. Fare clic su **Origine dati** per impostare la posizione principale per l'accesso ai dati. Scegliere il **Metodo di selezione** e specificare un account di archiviazione già esistente o crearne uno nuovo.

8. Nella e stesso, specificare un **Contenitore predefinito** e un **percorso**. E fare clic su **Seleziona**.

    > [AZURE.NOTE] Selezionare un percorso vicino paese DocumentDB account per migliorare le prestazioni

8. Fare clic su **prezzi** per selezionare il numero e il tipo di nodi. È possibile mantenere la configurazione predefinita e ridurre il numero dei nodi di lavoro in un secondo momento.

9. Fare clic su **configurazione facoltativa**e quindi **le azioni Script** e il facoltativi.

    Nel riquadro azioni Script immettere le informazioni seguenti per personalizzare il cluster HDInsight.

    <table border='1'>
        <tr><th>Proprietà</th><th>Valore</th></tr>
        <tr><td>Nome</td>
            <td>Specificare un nome per l'azione script.</td></tr>
        <tr><td>Script URI</td>
            <td>Specificare l'URI per lo script richiamato per personalizzare il grafico.</br></br>
   Inserisci: </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</strong>.</td></tr>
        <tr><td>Testa</td>
            <td>Fare clic sulla casella di controllo per eseguire lo script di PowerShell sul nodo testa.</br></br>
            <strong>Selezionare questa casella di controllo</strong>.</td></tr>
        <tr><td>Lavoro</td>
            <td>Fare clic sulla casella di controllo per eseguire lo script di PowerShell sul nodo lavoro.</br></br>
            <strong>Selezionare questa casella di controllo</strong>.</td></tr>
        <tr><td>Zookeeper</td>
            <td>Fare clic sulla casella di controllo per eseguire lo script di PowerShell sul Zookeeper.</br></br>
            <strong>Non è necessario</strong>.
            </td></tr>
        <tr><td>Parametri</td>
            <td>Specificare i parametri, se richiesto dallo script.</br></br>
            <strong>Parametri non necessari</strong>.</td></tr>
    </table>

10. Creare un nuovo **Gruppo di risorse** o Usa un gruppo di risorse esistente in abbonamento Azure.

11. A questo punto, selezionare **Aggiungi a dashboard** per tenere traccia della distribuzione e fare clic su **Crea**!

## <a name="InstallCmdlets"></a>Passaggio 2: Installare e configurare PowerShell Azure

1. Installare PowerShell Azure. Le istruzioni riportate [di seguito][powershell-install-configure].

    > [AZURE.NOTE] In alternativa, solo per le query Hive, è possibile utilizzare Editor di Hive online dell'HDInsight. A tale scopo, accedere al [Portale di Azure][azure-portal], fare clic su **HDInsight** nel riquadro sinistro per visualizzare un elenco dei cluster HDInsight. Fare clic su cluster che si desidera eseguire query Hive e quindi fare clic su **Console di Query**.

2. Aprire l'ambiente di Scripting integrato Azure PowerShell:
    - In un computer che esegue Windows 8 o Windows Server 2012 o versioni successive, è possibile utilizzare la ricerca incorporata. Dalla schermata Start digitare **powershell ise** e premere **INVIO**.
    - Un computer che esegue una versione precedente a Windows 8 o Windows Server 2012, usare il menu Start. Dal menu Start, digitare **il prompt dei comandi** nella casella di ricerca e quindi nell'elenco dei risultati, fare clic su **prompt dei comandi**. Il comando al prompt dei comandi digitare **powershell_ise** e premere **INVIO**.

3. Aggiungere l'Account Azure.
    1. Nel riquadro della Console digitare **Aggiungi AzureAccount** e premere **INVIO**.
    2. Digitare l'indirizzo di posta elettronica associato all'abbonamento Azure e fare clic su **Continua**.
    3. Digitare la password per l'abbonamento Azure.
    4. Fare clic su **Accedi**.

4. Nel diagramma seguente identifica le parti più importanti dell'ambiente gli script di PowerShell Azure.

    ![Diagramma per PowerShell Azure][azure-powershell-diagram]

## <a name="RunHive"></a>Passaggio 3: Eseguire un processo Hive utilizzando DocumentDB e HDInsight

> [AZURE.IMPORTANT] Tutte le variabili indicate da < > devono essere compilate utilizzando le impostazioni di configurazione.

1. Impostare le variabili seguenti nel riquadro di Script di PowerShell.

        # Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide the HDInsight cluster name where you want to run the Hive job.
        $clusterName = "<HDInsightClusterName>"

2. <p>Innanzitutto si crea la stringa di query. È necessario scrivere una query di Hive che accetta timestamp generato dal sistema di tutti i documenti (_ts) e ID univoci (_rid) da una raccolta DocumentDB calcola tutti i documenti per il minuto e archivia i risultati nuovamente in una nuova raccolta DocumentDB.</p>

    <p>Prima di tutto creare una tabella Hive dall'insieme di DocumentDB. Aggiungere il frammento di codice seguente Script di PowerShell riquadro <strong>dopo</strong> il frammento di codice da #1. Assicurarsi di includere facoltativo DocumentDB.query parametro t Annulla i documenti a solo _ts e _rid.</p>

    > [AZURE.NOTE]**DocumentDB.inputCollections assegnare un nome non è stato commesso un errore.** Sì, si consente l'aggiunta di più raccolte come input: </br>

        '*DocumentDB.inputCollections*' = '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*' A1A</br> The collection names are separated without spaces, using only a single comma.


        # Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "drop table DocumentDB_timestamps; "  +
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " +
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  Successivamente, creare una tabella Hive per la raccolta di output. Le proprietà del documento di output sarà il mese, giorno, ora, minuto e il numero totale di occorrenze.

    > [AZURE.NOTE]**Ancora, per la denominazione DocumentDB.outputCollections non è un errore.** Sì, si consente l'aggiunta di più raccolte come un output: </br>
'*DocumentDB.outputCollections*'='*\<nome della raccolta DocumentDB Output 1\>*,*\<nome della raccolta DocumentDB Output 2\>*' </br> I nomi di insieme sono separati senza spazi, con una singola virgola. </br></br>
Documenti sarà circolari distribuita in più raccolte. Un batch di documenti verrà archiviato in una raccolta, quindi un secondo batch dei documenti verranno archiviati nella raccolta successiva e così via.

        # Create a Hive table for the output data to DocumentDB.
        $queryStringPart2 = "drop table DocumentDB_analytics; " +
                              "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                              "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " +
                              "tblproperties ( " +
                                  "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                  "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                  "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                  "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "

4. Infine, si calcolare i documenti per mese, giorno, ore e minuti e inserire nuovamente i risultati l'output della tabella Hive.

        # GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "

5. Aggiungere il seguente frammento di script per creare una definizione di processo Hive dalla query precedente.

        # Create a Hive job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

    È inoltre possibile utilizzare l'opzione - parametro File consente di specificare un file di script HiveQL in HDFS.

6. Aggiungere il frammento di codice seguente per salvare l'ora di inizio e inviare il processo di Hive.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7. Aggiungere quanto segue necessario attendere il completamento del processo Hive.

        # Wait for the Hive job to complete.
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8. Aggiungere le operazioni seguenti per stampare l'output standard e l'ora di inizio e fine.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **Eseguire** il nuovo script! **Fare clic** sul pulsante execute verde.

10. Controllare i risultati. Accedere al [portale di Azure][azure-portal].
    1. Nel riquadro sinistro fare clic su <strong>Sfoglia</strong> . </br>
    2. Fare clic su <strong>tutti gli elementi</strong> in alto a destra del pannello Sfoglia. </br>
    3. Individuare e fare clic su <strong>Account DocumentDB</strong>. </br>
    4. Individuare quindi l' <strong>DocumentDB Account</strong>, quindi <strong>DocumentDB Database</strong> e la <strong>Raccolta DocumentDB</strong> associato alla raccolta di output specificata nella query Hive.</br>
    5. Infine, fare clic su <strong>Document Explorer</strong> sotto <strong>Gli strumenti di sviluppo</strong>.</br></p>

    Verranno visualizzati i risultati della query Hive.

    ![Risultati della query hive][image-hive-query-results]

## <a name="RunPig"></a>Passaggio 4: Eseguire un processo maialino con DocumentDB e HDInsight

> [AZURE.IMPORTANT] Tutte le variabili indicate da < > devono essere compilate utilizzando le impostazioni di configurazione.

1. Impostare le variabili seguenti nel riquadro di Script di PowerShell.

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"

2. <p>Innanzitutto si crea la stringa di query. È necessario scrivere una query di maialino che accetta timestamp generato dal sistema di tutti i documenti (_ts) e ID univoci (_rid) da una raccolta DocumentDB calcola tutti i documenti per il minuto e archivia i risultati nuovamente in una nuova raccolta DocumentDB.</p>
    <p>Prima di tutto, caricare i documenti da DocumentDB HDInsight. Aggiungere il frammento di codice seguente Script di PowerShell riquadro <strong>dopo</strong> il frammento di codice da #1. Assicurarsi di aggiungere una query di DocumentDB per il parametro di query DocumentDB facoltativo tagliare i documenti a solo _ts e _rid.</p>

    > [AZURE.NOTE]Sì, si consente l'aggiunta di più raccolte come input: </br>
'*\<Nome della raccolta Input DocumentDB 1\>*,*\<nome della raccolta Input DocumentDB 2\>*'</br> I nomi di insieme sono separati senza spazi, con una singola virgola. </b>

    Documenti sarà circolari distribuita in più raccolte. Un batch di documenti verrà archiviato in una raccolta, quindi un secondo batch dei documenti verranno archiviati nella raccolta successiva e così via.

        # Load data from DocumentDB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  Successivamente si calcolare i documenti per il mese, giorno, ora, minuto e il numero totale di occorrenze.

        # GROUP BY minute and COUNT entries for each.
        $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                            "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                            "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "

4. Infine, si archiviazione dei risultati nella nuova raccolta di output.

    > [AZURE.NOTE]Sì, si consente l'aggiunta di più raccolte come un output: </br>
'\<Nome della raccolta DocumentDB Output 1\>,\<nome della raccolta DocumentDB Output 2\>'</br> I nomi di insieme sono separati senza spazi, con una singola virgola.</br>
Documenti sarà circolari distribuita in più raccolte. Un batch di documenti verrà archiviato in una raccolta, quindi un secondo batch dei documenti verranno archiviati nella raccolta successiva e così via.

        # Store output data to DocumentDB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "

5. Aggiungere il seguente frammento di script per creare una definizione di processo maialino dalla query precedente.

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

    È inoltre possibile utilizzare l'opzione - parametro File consente di specificare un file di script maialino in HDFS.

6. Aggiungere il frammento di codice seguente per salvare l'ora di inizio e inviare il processo di maialino.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

7. Aggiungere quanto segue necessario attendere il completamento del processo maialino.

        # Wait for the Pig job to complete.
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8. Aggiungere le operazioni seguenti per stampare l'output standard e l'ora di inizio e fine.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **Eseguire** il nuovo script! **Fare clic** sul pulsante execute verde.

10. Controllare i risultati. Accedere al [portale di Azure][azure-portal].
    1. Nel riquadro sinistro fare clic su <strong>Sfoglia</strong> . </br>
    2. Fare clic su <strong>tutti gli elementi</strong> in alto a destra del pannello Sfoglia. </br>
    3. Individuare e fare clic su <strong>Account DocumentDB</strong>. </br>
    4. Individuare quindi l' <strong>DocumentDB Account</strong>, quindi <strong>DocumentDB Database</strong> e la <strong>Raccolta DocumentDB</strong> associato alla raccolta di output specificata nella query maialino.</br>
    5. Infine, fare clic su <strong>Document Explorer</strong> sotto <strong>Gli strumenti di sviluppo</strong>.</br></p>

    Verranno visualizzati i risultati della query maialino.

    ![Risultati della query maialino][image-pig-query-results]

## <a name="RunMapReduce"></a>Passaggio 5: Eseguire un processo MapReduce utilizzando DocumentDB e HDInsight

1. Impostare le variabili seguenti nel riquadro di Script di PowerShell.

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name

2. È necessario eseguire un processo MapReduce che conta il numero di occorrenze per ogni proprietà documento dalla raccolta DocumentDB. Aggiungere questo script frammento **dopo** il frammento di codice precedente.

        # Define the MapReduce job.
        $TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

    > [AZURE.NOTE] TallyProperties v01.jar viene fornito con l'installazione personalizzata del connettore Hadoop DocumentDB.

3. Aggiungere il seguente comando per inviare il processo di MapReduce.

        # Save the start time and submit the job.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    Oltre la definizione del processo MapReduce, inoltre possibile specificare il nome di cluster HDInsight nel punto in cui si desidera eseguire il processo di MapReduce e le credenziali. Inizio-AzureHDInsightJob è una chiamata asincrona. Per verificare il completamento dell'operazione, utilizzare il cmdlet *AzureHDInsightJob di attesa* .

4. Aggiungere il seguente comando per controllare gli errori di esecuzione del processo di MapReduce.

        # Get the job output and print the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

5. **Eseguire** il nuovo script! **Fare clic** sul pulsante execute verde.

6. Controllare i risultati. Accedere al [portale di Azure][azure-portal].
    1. Nel riquadro sinistro fare clic su <strong>Sfoglia</strong> .
    2. Fare clic su <strong>tutti gli elementi</strong> in alto a destra del pannello Sfoglia.
    3. Individuare e fare clic su <strong>Account DocumentDB</strong>.
    4. Quindi, trovare l' <strong>DocumentDB Account</strong>, quindi <strong>DocumentDB Database</strong> e la <strong>Raccolta DocumentDB</strong> associato alla raccolta di output specificata nel processo di MapReduce.
    5. Infine, fare clic su <strong>Document Explorer</strong> sotto <strong>Gli strumenti di sviluppo</strong>.

    Verranno visualizzati i risultati del processo di MapReduce.

    ![Risultati della query MapReduce][image-mapreduce-query-results]

## <a name="NextSteps"></a>Passaggi successivi

Congratulazioni! È appena stato eseguito i primo processi Hive, maialino e MapReduce utilizzando DocumentDB Azure e HDInsight.

Abbiamo Apri origine il connettore Hadoop. Se si è interessati, può contribuire in [GitHub][documentdb-github].

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Sviluppare un'applicazione Java con Documentdb][documentdb-java-application]
- [Sviluppare programmi Java MapReduce per Hadoop in HDInsight][hdinsight-develop-deploy-java-mapreduce]
- [Iniziare a usare Hadoop con Hive in HDInsight per l'analisi di utilizzo del ricevitore per dispositivi mobili][hdinsight-get-started]
- [Utilizzare MapReduce con HDInsight][hdinsight-use-mapreduce]
- [Usare Hive con HDInsight][hdinsight-use-hive]
- [Utilizzare maialino con HDInsight][hdinsight-use-pig]
- [Personalizzare i cluster HDInsight tramite Script azione][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-portal]: https://portal.azure.com/
[azure-powershell-diagram]: ./media/documentdb-run-hadoop-with-hdinsight/azurepowershell-diagram-med.png

[documentdb-hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[documentdb-github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[documentdb-manage-collections]: documentdb-manage.md#Collections
[documentdb-manage-document-storage]: documentdb-manage.md#IndexOverhead
[documentdb-manage-throughput]: documentdb-manage.md#ProvThroughput
[documentdb-import-data]: documentdb-import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md#powershell
[hdinsight-develop-deploy-java-mapreduce]: ../hdinsight/hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight/hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-storage]: ../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight-use-mapreduce]: ../hdinsight/hdinsight-use-mapreduce.md
[hdinsight-use-pig]: ../hdinsight/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page1.png
[image-hive-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: ../powershell-install-configure.md
