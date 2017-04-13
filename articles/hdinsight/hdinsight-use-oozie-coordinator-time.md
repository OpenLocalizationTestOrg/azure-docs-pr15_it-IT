<properties
    pageTitle="Usare basate sul tempo coordinatore Hadoop Oozie in HDInsight | Microsoft Azure"
    description="Utilizzare basate sul tempo coordinatore Hadoop Oozie HDInsight, un servizio dati. Informazioni su come definire flussi di lavoro Oozie e i coordinatori e inviare i processi."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jgao"/>


# <a name="use-time-based-oozie-coordinator-with-hadoop-in-hdinsight-to-define-workflows-and-coordinate-jobs"></a>Utilizzare basate sul tempo coordinatore Oozie con Hadoop in HDInsight per definire i flussi di lavoro e processi di coordinate

In questo articolo verrà spiegato come definire i flussi di lavoro e i coordinatori e come attivare i processi di coordinatore, in base all'ora. È comunque utile elaborata [Usare Oozie con HDInsight] [ hdinsight-use-oozie] prima di leggere questo articolo. Oltre a Oozie, è anche possibile pianificare processi di utilizzo dei dati di Azure. Per informazioni su Factory di dati di Azure, vedere [usare maialino e Hive con dati Factory](../data-factory/data-factory-data-transformation-activities.md).

> [AZURE.NOTE] In questo articolo richiede un cluster basato su Windows HDInsight. Per informazioni sull'utilizzo di Oozie, inclusi i processi basate sul tempo, in un cluster basato su Linux, vedere [Usare Oozie con Hadoop per definire ed eseguire un flusso di lavoro basati su Linux HDInsight](hdinsight-use-oozie-linux-mac.md)

##<a name="what-is-oozie"></a>Che cos'è Oozie

Apache Oozie è un sistema di flusso di lavoro/coordinazione che consente di gestire processi di Hadoop. È integrato con stack Hadoop e supporta processi Hadoop per Apache MapReduce, maialino Apache, Apache Hive e Apache Sqoop. Può inoltre essere utilizzato per pianificare processi che sono specifici per un sistema, ad esempio programmi Java o script di shell.

Nella figura seguente mostra il flusso di lavoro che verrà implementato:

![Diagramma di flusso di lavoro][img-workflow-diagram]

Il flusso di lavoro contiene due azioni:

1. Un'azione Hive esegue uno script HiveQL per contare le occorrenze di ogni tipo di livello di registrazione in un file di log log4j. Ogni log log4j è costituito da una linea di campi che contiene un campo [livello di registrazione] per visualizzare il tipo e la gravità, ad esempio:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    L'output di script Hive è simile a:

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Per ulteriori informazioni su Hive, vedere [Usare Hive con HDInsight][hdinsight-use-hive].

2.  Un'azione Sqoop consente di esportare l'output di azione HiveQL a una tabella in un database SQL Azure. Per ulteriori informazioni su Sqoop, vedere [Usare Sqoop con HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE] Per le versioni Oozie supportate nei cluster HDInsight, vedere [quali sono le novità in versioni cluster fornite da HDInsight?] [hdinsight-versions].


##<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Un lavoro utilizzando Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **HDInsight un cluster**. Per informazioni sulla creazione di un cluster di HDInsight, vedere [creare HDInsight cluster][hdinsight-provision], o [inizia con HDInsight][hdinsight-get-started]. È necessario disporre i dati seguenti per accedere all'interno dell'esercitazione:

    <table border = "1">
    <tr><th>Proprietà cluster</th><th>Nome della variabile di Windows PowerShell</th><th>Valore</th><th>Descrizione</th></tr>
    <tr><td>Nome cluster HDInsight</td><td>$clusterName</td><td></td><td>Cluster HDInsight in cui verrà eseguito in questa esercitazione.</td></tr>
    <tr><td>HDInsight cluster username</td><td>$clusterUsername</td><td></td><td>Nome utente cluster HDInsight. </td></tr>
    <tr><td>Password utente cluster HDInsight </td><td>$clusterPassword</td><td></td><td>La password dell'utente cluster HDInsight.</td></tr>
    <tr><td>Nome dell'account di archiviazione Azure</td><td>$storageAccountName</td><td></td><td>Un account di archiviazione Azure disponibile per il cluster HDInsight. Per questa esercitazione, usare l'account di archiviazione predefinito specificato durante il processo di provisioning cluster.</td></tr>
    <tr><td>Nome del contenitore di Blob Azure</td><td>$containerName</td><td></td><td>In questo esempio, utilizzare il contenitore di spazio di archiviazione Blob Azure utilizzato per il file system cluster HDInsight predefinito. Per impostazione predefinita, ha lo stesso nome cluster HDInsight.</td></tr>
    </table>

- **Database di SQL Azure**. È necessario configurare una regola del firewall per il server di Database SQL consentire l'accesso dalla workstation. Per istruzioni sulla creazione di SQL Azure del database e configurare il firewall, vedere [Introduzione all'utilizzo di database SQL Azure][sqldatabase-get-started]. In questo articolo fornisce un script di Windows PowerShell per la creazione della tabella di database SQL Azure necessari per questa esercitazione.

    <table border = "1">
    <tr><th>Proprietà di database SQL</th><th>Nome della variabile di Windows PowerShell</th><th>Valore</th><th>Descrizione</th></tr>
    <tr><td>Nome del server di database SQL</td><td>$sqlDatabaseServer</td><td></td><td>Server di database SQL in cui Sqoop Esporta dati. </td></tr>
    <tr><td>Nome di accesso di database SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Nome di accesso di Database SQL.</td></tr>
    <tr><td>Password di accesso al database SQL</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Password di accesso al Database SQL.</td></tr>
    <tr><td>Nome del database SQL</td><td>$sqlDatabaseName</td><td></td><td>Database di SQL Azure in cui Sqoop Esporta dati. </td></tr>
    </table>

    > [AZURE.NOTE] Per impostazione predefinita un database SQL Azure consente connessioni da servizi di Windows Azure, ad esempio Azure HDInsight. Se questa impostazione firewall è disabilitata, è necessario abilitare dal portale di Azure. Per istruzioni sulla creazione di un Database SQL e configurare le regole firewall, vedere [creare e configurare Database SQL][sqldatabase-get-started].


> [AZURE.NOTE] Fill-in valori nelle tabelle. Sarà comunque utile per l'interfaccia in questa esercitazione.


##<a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Definire Oozie del flusso di lavoro e lo script HiveQL correlato

Definizioni di flussi di lavoro Oozie scritte in hPDL (processo definizione linguaggio XML). Il nome di file predefinito del flusso di lavoro è *workflow*.  Si verrà salvare il file di flusso di lavoro in locale e quindi distribuirlo cluster HDInsight tramite PowerShell Azure più avanti in questa esercitazione.

L'azione Hive del flusso di lavoro chiama un file di script HiveQL. Il file di script contiene tre HiveQL istruzioni:

1. **Istruzione DROP tabella** viene eliminata la tabella Hive log4j.
2. **Istruzione CREATE TABLE nel** crea una tabella, esterna Hive log4j che punta al percorso del file di registro log4j.
3.  **Il percorso del file di log log4j**. Delimitatore dei campi ",". Il delimitatore di riga predefinito è "\n". Una tabella Hive esterna viene utilizzata per evitare il file di dati viene rimosso dalla posizione originale, nel caso in cui si desidera eseguire il flusso di lavoro Oozie più volte.
3. **Inserire SOVRASCRIVERE l'istruzione** contare le occorrenze di ogni tipo di livello di registrazione dalla tabella Hive log4j e l'output viene salvato in una posizione di archiviazione Blob Azure.

**Nota**: esiste un problema noto riscontrato percorso Hive. Questo problema verrà eseguita quando si invia un processo Oozie. Le istruzioni per la risoluzione del problema sono disponibili nella pagina Wiki di TechNet: [HDInsight Hive errore: Impossibile rinominare][technetwiki-hive-error].

**Per definire il file di script HiveQL da chiamare il flusso di lavoro**

1. Creare un file di testo con il contenuto seguente:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Esistono tre variabili utilizzate nello script:

    - ${hiveTableName}
    - ${hiveDataFolder}
    - ${hiveOutputFolder}

    File di definizione del flusso di lavoro (workflow in questa esercitazione) passerà questi valori a questo script HiveQL in fase di esecuzione.

2. Salvare il file come **C:\Tutorials\UseOozie\useooziewf.hql** utilizzando la codifica ANSI (ASCII). Utilizzare il blocco note se l'editor di testo non è disponibile questa opzione. Il file di script verrà distribuito al cluster HDInsight in un secondo momento nell'esercitazione.



**Per definire un flusso di lavoro**

1. Creare un file di testo con il contenuto seguente:

        <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
            <start to = "RunHiveScript"/>

            <action name="RunHiveScript">
                <hive xmlns="uri:oozie:hive-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.job.queue.name</name>
                            <value>${queueName}</value>
                        </property>
                    </configuration>
                    <script>${hiveScript}</script>
                    <param>hiveTableName=${hiveTableName}</param>
                    <param>hiveDataFolder=${hiveDataFolder}</param>
                    <param>hiveOutputFolder=${hiveOutputFolder}</param>
                </hive>
                <ok to="RunSqoopExport"/>
                <error to="fail"/>
            </action>

            <action name="RunSqoopExport">
                <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.compress.map.output</name>
                            <value>true</value>
                        </property>
                    </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveOutputFolder}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\001"</arg>
                </sqoop>
                <ok to="end"/>
                <error to="fail"/>
            </action>

            <kill name="fail">
                <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
            </kill>

           <end name="end"/>
        </workflow-app>

    Esistono due azioni definite del flusso di lavoro. L'azione avvia a è *RunHiveScript*. Se l'operazione viene eseguita *OK*, l'azione successiva è *RunSqoopExport*.

    Il RunHiveScript sono diverse variabili. Quando si invia il processo di Oozie dalla propria workstation tramite PowerShell di Azure, si passerà i valori.

    <table border = "1">
    <tr><th>Variabili del flusso di lavoro</th><th>Descrizione</th></tr>
    <tr><td>${jobTracker}</td><td>Specificare l'URL del Registro di processo Hadoop. Utilizzare <strong>jobtrackerhost:9010</strong> in HDInsight cluster versione 3.0 e 2.0.</td></tr>
    <tr><td>${nameNode}</td><td>Specificare l'URL del nodo di nome Hadoop. Utilizzare la wasbs di sistema di file predefinito: / / di indirizzi, ad esempio <i>wasbs: / /&lt;nomecontenitore&gt;@&lt;storageAccountName&gt;. blob.core.windows.net</i>.</td></tr>
    <tr><td>${Coda}</td><td>Specifica il nome della coda inviate per il processo. Utilizzare <strong>predefinito</strong>.</td></tr>
    </table>


    <table border = "1">
    <tr><th>Hive azione variabile</th><th>Descrizione</th></tr>
    <tr><td>${hiveDataFolder}</td><td>Directory di origine per il comando Crea tabella Hive.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>La cartella di output per l'istruzione INSERT SOVRASCRIVERE.</td></tr>
    <tr><td>${hiveTableName}</td><td>Il nome della tabella Hive che fa riferimento ai file di dati log4j.</td></tr>
    </table>


    <table border = "1">
    <tr><th>Variabile di azione Sqoop</th><th>Descrizione</th></tr>
    <tr><td>${sqlDatabaseConnectionString}</td><td>Stringa di connessione di Database SQL.</td></tr>
    <tr><td>${sqlDatabaseTableName}</td><td>Tabella di database SQL Azure in cui verranno esportati i dati.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>La cartella di output per l'istruzione Hive inserire SOVRASCRIVERE. Questa è la stessa cartella per l'esportazione Sqoop (esportazione dir).</td></tr>
    </table>

    Per ulteriori informazioni su Oozie del flusso di lavoro e utilizzare le azioni del flusso di lavoro, vedere [la documentazione Apache Oozie 4.0] [ apache-oozie-400] (per la versione cluster di HDInsight 3.0) o [la documentazione Apache Oozie 3.3.2] [ apache-oozie-332] (la versione cluster HDInsight 2.1).

2. Salvare il file come **C:\Tutorials\UseOozie\workflow.xml** utilizzando la codifica ANSI (ASCII). Utilizzare il blocco note se l'editor di testo non è disponibile questa opzione.

**Per definire coordinatore**

1. Creare un file di testo con il contenuto seguente:

        <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
           <action>
              <workflow>
                 <app-path>${wfPath}</app-path>
              </workflow>
           </action>
        </coordinator-app>

    Sono disponibili cinque variabili utilizzate nel file di definizione:

  	| Variabile          | Descrizione |
  	| ------------------|------------ |
  	| ${coordFrequency} | Tempo processo. Frequenza viene sempre espresso in minuti. |
  	| ${coordStart}     | Ora di inizio processo. |
  	| ${coordEnd}       | Ora di fine processo. |
  	| ${coordTimezone}  | Con nessuna ora legale (in genere rappresentato tramite UTC) Oozie processi di coordinatore fisso fuso orario. Fuso orario corrente viene definito il "fuso orario elaborazione Oozie". |
  	| ${wfPath}         | Il percorso per il workflow.  Se il nome del file del flusso di lavoro non è il nome di file predefinito (workflow. XML), è necessario specificarlo. |

2. Salvare il file come **C:\Tutorials\UseOozie\coordinator.xml** utilizzando la codifica ANSI (ASCII). Utilizzare il blocco note se l'editor di testo non è disponibile questa opzione.

##<a name="deploy-the-oozie-project-and-prepare-the-tutorial"></a>Distribuire il progetto Oozie e preparare l'esercitazione

Eseguire uno script di PowerShell Azure per eseguire le operazioni seguenti:

- Copiare lo script HiveQL (useoozie.hql) allo spazio di archiviazione Blob Azure, wasbs:///tutorials/useoozie/useoozie.hql.
- Copiare workflow wasbs:///tutorials/useoozie/workflow.xml.
- Copiare coordinator.xml wasbs:///tutorials/useoozie/coordinator.xml.
- Copiare il file di dati (/ example/data/sample.log) a wasbs:///tutorials/useoozie/data/sample.log.
- Creare una tabella di database SQL Azure per l'archiviazione dei dati di esportazione Sqoop. Il nome della tabella è *log4jLogCount*.

**Comprendere HDInsight lo spazio di archiviazione**

HDInsight utilizza archiviazione Blob Azure per l'archiviazione di dati. wasbs: / / è l'implementazione Microsoft del Hadoop distribuita file system (HDFS) in archiviazione Blob Azure. Per ulteriori informazioni, vedere [lo spazio di archiviazione Blob Azure usare con HDInsight][hdinsight-storage].

Durante il provisioning di un cluster di HDInsight, un account di archiviazione Blob Azure e di un contenitore specifico da tale account come file system predefinito, ad esempio in HDFS. Oltre a questo account di archiviazione, è possibile aggiungere ulteriore spazio di archiviazione account dalla stessa sottoscrizione di Azure o da diversi abbonamenti Azure durante il processo di provisioning. Per istruzioni sull'aggiunta di account di spazio di archiviazione aggiuntivo, vedere [cluster di provisioning HDInsight][hdinsight-provision]. Per semplificare lo script di PowerShell Azure utilizzato in questa esercitazione, tutti i file sono archiviati nel contenitore di sistema di file predefinito che si trova in */tutorials/useoozie*. Per impostazione predefinita, in questo contenitore ha lo stesso nome come nome del cluster HDInsight.
La sintassi è:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE] Solo il *wasb: / /* sintassi supportata nella HDInsight cluster versione 3.0. Le versioni precedenti *consentire: / /* sintassi sono supportata in HDInsight 2.1 e 1.6 cluster, ma non è supportato in cluster HDInsight 3.0.

> [AZURE.NOTE] Il wasb: / / percorso è un percorso virtuale. Per ulteriori informazioni vedere [lo spazio di archiviazione Blob Azure usare con HDInsight][hdinsight-storage].

Un file memorizzato nel contenitore di sistema di file predefinito è possibile accedervi da HDInsight utilizzando uno dei seguenti URI (ad esempio sto usando workflow. XML):

    wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasbs:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Se si vuole accedere al file direttamente dall'account di archiviazione, il nome blob per il file è:

    tutorials/useoozie/workflow.xml

**Comprendere le tabelle Hive interne ed esterne**

Esistono alcune operazioni che è necessario conoscere sulle tabelle interne ed esterne Hive:

- Il comando Crea tabella crea una tabella interna, noto anche come una tabella gestita. Il file di dati deve trovarsi nel contenitore predefinito.
- Il comando Crea tabella si sposta il file di dati /hive/warehouse/<TableName> cartella nel contenitore predefinito.
- Il comando creazione di tabelle esterne crea una tabella esterna. Il file di dati può trovarsi all'esterno del contenitore predefinito.
- Il comando creazione di tabelle esterne non si sposta il file di dati.
- Il comando creazione di tabelle esterne non consente di eventuali sottocartelle della cartella specificata nella clausola posizione. Questo è il motivo perché l'esercitazione viene creata una copia del file sample.log.

Per ulteriori informazioni, vedere [HDInsight: Hive interni ed esterni tabelle introduttivo][cindygross-hive-tables].

**Per preparare l'esercitazione**

1. Aprire Windows PowerShell ISE (nella schermata Start di Windows 8, digitare **PowerShell_ISE**e quindi fare clic su **Windows PowerShell ISE**. Per ulteriori informazioni, vedere [Start di Windows PowerShell in Windows 8 e Windows][powershell-start]).
2. Nel riquadro inferiore, eseguire il seguente comando per connettersi al proprio abbonamento Azure:

        Add-AzureAccount

    Verrà richiesto di immettere le credenziali dell'account Azure. Questo metodo di aggiunta di una connessione di abbonamento scade e dopo 12 ore, sarà necessario eseguire il cmdlet di nuovo.

    > [AZURE.NOTE] Se si hanno più abbonamenti Azure e l'abbonamento predefinito non è quello che si desidera utilizzare, utilizzare il cmdlet <strong>Selezionare AzureSubscription</strong> per selezionare un abbonamento.

3. Copiare il seguente script nel riquadro dello script e quindi impostare le variabili primi sei:

        # WASB variables
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<BlobStorageContainerName>"

        # SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"  
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  
        $sqlDatabaseTableName = "log4jLogsCount"

        # Oozie files for the tutorial
        $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
        $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
        $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

        # WASB folder for storing the Oozie tutorial files.
        $destFolder = "tutorials/useoozie"  # Do NOT use the long path here


    Per ulteriori descrizioni delle variabili, vedere la sezione relativa ai [Prerequisiti](#prerequisites) in questa esercitazione.

3. Aggiungere lo script nel riquadro dello script le operazioni seguenti:

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        function uploadOozieFiles()
        {
            Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
            Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
            Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
            Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
        }

        function prepareHiveDataFile()
        {
            Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
            Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
        }

        function prepareSQLDatabase()
        {
            # SQL query string for creating log4jLogsCount table
            $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [Level] [nvarchar](10) NOT NULL,
                    [Total] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
                (
                [Level] ASC
                )
                )"

            #Create the log4jLogsCount table
            Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
            $conn = New-Object System.Data.SqlClient.SqlConnection
            $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
            $conn.open()
            $cmd = New-Object System.Data.SqlClient.SqlCommand
            $cmd.connection = $conn
            $cmd.commandtext = $cmdCreateLog4jCountTable
            $cmd.executenonquery()

            $conn.close()
        }

        # upload workflow.xml, coordinator.xml, and ooziewf.hql
        uploadOozieFiles;

        # make a copy of example/data/sample.log to example/data/log4j/sample.log
        prepareHiveDataFile;

        # create log4jlogsCount table on SQL database
        prepareSQLDatabase;

4. Fare clic su **Esegui Script** oppure premere **F5** per eseguire lo script. L'output sarà simile a:

    ![Output di preparazione dell'esercitazione][img-preparation-output]

##<a name="run-the-oozie-project"></a>Eseguire il progetto Oozie

PowerShell Azure attualmente non fornisce i cmdlet per la definizione di processi Oozie. È possibile utilizzare il cmdlet **RestMethod richiama** per richiamare servizi web Oozie. L'API Oozie web services è un'API di JSON resto HTTP. Per ulteriori informazioni sui servizi web Oozie API, vedere [la documentazione Apache Oozie 4.0] [ apache-oozie-400] (per la versione cluster di HDInsight 3.0) o [la documentazione Apache Oozie 3.3.2] [ apache-oozie-332] (la versione cluster HDInsight 2.1).

**Per inviare un processo Oozie**

1. Aprire Windows PowerShell ISE (nella schermata Start di Windows 8, digitare **PowerShell_ISE**e quindi fare clic su **Windows PowerShell ISE**. Per ulteriori informazioni, vedere [Start di Windows PowerShell in Windows 8 e Windows][powershell-start]).

3. Copiare il seguente script nel riquadro dello script e quindi impostare le variabili prima di tutto 14 (tuttavia, ignorare **$storageUri**).

        #HDInsight cluster variables
        $clusterName = "<HDInsightClusterName>"
        $clusterUsername = "<HDInsightClusterUsername>"
        $clusterPassword = "<HDInsightClusterUserPassword>"

        #Azure Blob storage (WASB) variables
        $storageAccountName = "<StorageAccountName>"
        $storageContainerName = "<BlobContainerName>"
        $storageUri="wasbs://$storageContainerName@$storageAccountName.blob.core.windows.net"

        #Azure SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  

        #Oozie WF/coordinator variables
        $coordStart = "2014-03-21T13:45Z"
        $coordEnd = "2014-03-21T13:45Z"
        $coordFrequency = "1440"    # in minutes, 24h x 60m = 1440m
        $coordTimezone = "UTC"  #UTC/GMT

        $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
        $waitTimeBetweenOozieJobStatusCheck=10

        #Hive action variables
        $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
        $hiveTableName = "log4jlogs"
        $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
        $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

        #Sqoop action variables
        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
        $sqlDatabaseTableName = "log4jLogsCount"

        $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    Per ulteriori descrizioni delle variabili, vedere la sezione relativa ai [Prerequisiti](#prerequisites) in questa esercitazione.

    $coordstart e $coordend sono il flusso di lavoro inizio e ora di fine. Per determinare l'ora UTC/GMT, cercare "ora utc" su bing.com. Il $coordFrequency è la frequenza in minuti per eseguire il flusso di lavoro.

3. Aggiungere la seguente lo script. Questa parte definisce payload Oozie:

        #OoziePayload used for Oozie web service submission
        $OoziePayload =  @"
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>

           <property>
               <name>nameNode</name>
               <value>$storageUrI</value>
           </property>

           <property>
               <name>jobTracker</name>
               <value>jobtrackerhost:9010</value>
           </property>

           <property>
               <name>queueName</name>
               <value>default</value>
           </property>

           <property>
               <name>oozie.use.system.libpath</name>
               <value>true</value>
           </property>

           <property>
               <name>oozie.coord.application.path</name>
               <value>$oozieWFPath</value>
           </property>

           <property>
               <name>wfPath</name>
               <value>$oozieWFPath</value>
           </property>

           <property>
               <name>coordStart</name>
               <value>$coordStart</value>
           </property>

           <property>
               <name>coordEnd</name>
               <value>$coordEnd</value>
           </property>

           <property>
               <name>coordFrequency</name>
               <value>$coordFrequency</value>
           </property>

           <property>
               <name>coordTimezone</name>
               <value>$coordTimezone</value>
           </property>

           <property>
               <name>hiveScript</name>
               <value>$hiveScript</value>
           </property>

           <property>
               <name>hiveTableName</name>
               <value>$hiveTableName</value>
           </property>

           <property>
               <name>hiveDataFolder</name>
               <value>$hiveDataFolder</value>
           </property>

           <property>
               <name>hiveOutputFolder</name>
               <value>$hiveOutputFolder</value>
           </property>

           <property>
               <name>sqlDatabaseConnectionString</name>
               <value>&quot;$sqlDatabaseConnectionString&quot;</value>
           </property>

           <property>
               <name>sqlDatabaseTableName</name>
               <value>$SQLDatabaseTableName</value>
           </property>

           <property>
               <name>user.name</name>
               <value>admin</value>
           </property>

        </configuration>
        "@

    >[AZURE.NOTE] La differenza rispetto al file di payload invio del flusso di lavoro è la variabile **oozie.coord.application.path**. Quando si invia un processo del flusso di lavoro, si utilizza **oozie.wf.application.path** .

4. Aggiungere la seguente lo script. Questa parte controlla lo stato dei servizi web Oozie:

        function checkOozieServerStatus()
        {
            Write-Host "Checking Oozie server status..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $oozieServerSatus = $jsonResponse[0].("systemMode")
            Write-Host "Oozie server status is $oozieServerSatus..."

            if($oozieServerSatus -notmatch "NORMAL")
            {
                Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
                exit 1
            }
        }

5. Aggiungere la seguente lo script. Questa parte crea un processo Oozie:

        function createOozieJob()
        {
            # create Oozie job
            Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
            Write-Host "`n--------`n$OoziePayload`n--------"
            $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
            $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose

            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $oozieJobId = $jsonResponse[0].("id")
            Write-Host "Oozie job id is $oozieJobId..."

            return $oozieJobId
        }

    > [AZURE.NOTE] Quando si invia un processo del flusso di lavoro, è necessario effettuare un altro servizio web per avviare il processo dopo aver creato il processo di chiamata. In questo caso, il processo di coordinatore viene attivato da ora. Il processo verrà avviato automaticamente.

6. Aggiungere la seguente lo script. Questa parte controlla lo stato del processo di Oozie:

        function checkOozieJobStatus($oozieJobId)
        {
            # get job status
            Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

            Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
            $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")

            while($JobStatus -notmatch "SUCCEEDED|KILLED")
            {
                Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
                Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
                $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
                $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
                $JobStatus = $jsonResponse[0].("status")
            }

            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
            if($JobStatus -notmatch "SUCCEEDED")
            {
                Write-Host "Check logs at http://headnode0:9014/cluster for detais."
                exit -1
            }
        }

7. (Facoltativo) Aggiungere la seguente lo script.

        function listOozieJobs()
        {
            Write-Host "Listing Oozie jobs..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds

            write-host "Job ID                                   App Name        Status      Started                         Ended"
            write-host "----------------------------------------------------------------------------------------------------------------------------------"
            foreach($job in $response.workflows)
            {
                Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
            }
        }

        function ShowOozieJobLog($oozieJobId)
        {
            Write-Host "Showing Oozie job info..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds
            write-host $response
        }

        function killOozieJob($oozieJobId)
        {
            Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
            $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
            $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
        }

7. Aggiungere lo script le operazioni seguenti:

        checkOozieServerStatus
        # listOozieJobs
        $oozieJobId = createOozieJob($oozieJobId)
        checkOozieJobStatus($oozieJobId)
        # ShowOozieJobLog($oozieJobId)
        # killOozieJob($oozieJobId)

    Rimuovere i segni di # se si desidera eseguire altre funzioni.

7. Se il cluster HDinsight versione 2.1, sostituire "https://$clusterName.azurehdinsight.net:443/oozie/v2/" con "https://$clusterName.azurehdinsight.net:443/oozie/v1/". Versione cluster HDInsight 2.1 non non supporta la versione 2 dei servizi web.

7. Fare clic su **Esegui Script** oppure premere **F5** per eseguire lo script. L'output sarà simile a:

    ![Esercitazione eseguire output del flusso di lavoro][img-runworkflow-output]

8. Connettersi al Database di SQL per visualizzare i dati esportati.

**Per controllare il log degli errori di processo**

Per risolvere i problemi di un flusso di lavoro, il file di log Oozie sono disponibili in C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log da headnode cluster. Per informazioni su RDP, vedere [amministrazione HDInsight cluster tramite il portale di Azure][hdinsight-admin-portal].

**Per eseguire di nuovo l'esercitazione**

Eseguire nuovamente il flusso di lavoro, è necessario eseguire le operazioni seguenti:

- Eliminare il file di output di script Hive.
- Eliminare i dati nella tabella log4jLogsCount.

Ecco un script di Windows PowerShell di esempio che è possibile usare:

    $storageAccountName = "<AzureStorageAccountName>"
    $containerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()


##<a name="next-steps"></a>Passaggi successivi
In questa esercitazione, è stato illustrato come definire un flusso di lavoro Oozie e un coordinatore Oozie e come eseguire un processo di coordinatore Oozie tramite PowerShell Azure. Per ulteriori informazioni, vedere gli articoli seguenti:

- [Guida introduttiva a HDInsight][hdinsight-get-started]
- [Utilizzo dell'archiviazione Blob Azure HDInsight][hdinsight-storage]
- [Amministrare HDInsight tramite PowerShell Azure][hdinsight-admin-powershell]
- [Caricare dati HDInsight][hdinsight-upload-data]
- [Utilizzare Sqoop con HDInsight][hdinsight-use-sqoop]
- [Usare Hive con HDInsight][hdinsight-use-hive]
- [Utilizzare maialino con HDInsight][hdinsight-use-pig]
- [Sviluppare programmi Java MapReduce per HDInsight][hdinsight-develop-java-mapreduce]



[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563


[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-develop-java-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png  

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
