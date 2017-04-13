<properties
    pageTitle="HBase esercitazione: Introduzione a HBase in Hadoop | Microsoft Azure"
    description="Seguire questa esercitazione HBase per iniziare a usare Apache HBase con Hadoop in HDInsight. Creare tabelle da shell HBase ed eseguire query utilizzando Hive."
    keywords="Apache hbase, hbase, hbase shell hbase esercitazione"
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="jgao"/>



# <a name="hbase-tutorial-get-started-using-apache-hbase-with-windows-based-hadoop-in-hdinsight"></a>Esercitazione HBase: iniziare a usare Apache HBase con basato su Windows Hadoop in HDInsight

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Informazioni su come creare cluster HBase in HDInsight, creare tabelle HBase e le tabelle di query utilizzando Apache Hive. Per informazioni generali HBase, vedere [Panoramica di HDInsight HBase][hdinsight-hbase-overview].

Le informazioni contenute in questo documento sono specifiche di cluster HDInsight basato su Windows. Per informazioni sui cluster basato su Windows, usare il selettore di tabulazione nella parte superiore della pagina per passare.

> [AZURE.NOTE] HBase (versione 0.98.0) in HDInsight basato su Windows è disponibile solo per uso con i cluster 3.1 HDInsight (in base alla Apache Hadoop e filati 2.4.0). Per informazioni sulla versione, vedere [quali sono le novità in versioni cluster Hadoop fornite da HDInsight?][hdinsight-versions]

## <a name="before-you-begin"></a>Prima di iniziare

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Prima di iniziare questa esercitazione HBase, è necessario disporre le operazioni seguenti:

- **Abbonamento A Microsoft Azure**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Una workstation** con Visual Studio 2013 o versione successiva: per ottenere istruzioni, vedere [Installare Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

### <a name="access-control-requirements"></a>Requisiti di controllo accesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Creare HBase cluster

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Per creare un cluster HBase tramite il portale di Azure**

1. Accedere al [portale di Azure][azure-management-portal].
2. Fare clic su **Nuovo** o **+** in alto a sinistra angolo e quindi fare clic su **dati + Analitica**, **HDInsight**.
3. Immettere i valori seguenti:

    - **Nome cluster** - immettere un nome per identificare il cluster.
    - **Tipo di cluster** - selezionare **HBase**.
    - **Sistema operativo del cluster** - selezionare **Windows**.  Per la creazione di HBase basati su Linux cluster, vedere [HBase esercitazione: iniziare a usare Apache HBase con Hadoop in HDInsight (Linux)](hdinsight-hbase-tutorial-get-started-linux.md).
    - **Versione** : selezionare una versione HBase.
    - **Abbonamento** , selezionare l'abbonamento Azure utilizzato per la creazione di questo cluster.
    - **Gruppo di risorse** - creare un nuovo gruppo di risorse Azure o selezionarne uno esistente. Per ulteriori informazioni, vedere [Panoramica di gestione risorse di Azure](azure-resource-manager/resource-group-overview.md)
    - **Credenziali** - per cluster basato su Windows, è possibile creare un utente cluster (detta anche HTTP utente, l'utente del servizio web HTTP) e un utente Desktop remoto. Fare clic su **Attiva Desktop remoto** per aggiungere le credenziali utente desktop remoto. La sezione successiva richiede RDP.
    - **Origine dati** - creare un nuovo account di archiviazione Azure o selezionare un account di archiviazione Azure esistente da utilizzare come il sistema di file predefinito per il cluster. Il percorso di account di archiviazione predefinito determina la posizione del percorso cluster. L'account di archiviazione predefinito e il cluster devono posizionare nell'interfaccia di dati stesso.
    - **Nodo prezzi livelli** - selezionare il numero di area Server per il cluster HBase

        > [AZURE.WARNING] Per elevata disponibilità dei servizi HBase, è necessario creare un cluster che include almeno **tre** nodi. In questo modo che, se un nodo viene interrotto, le aree di dati HBase disponibili negli altri nodi.

        > Se si formazione HBase, sempre scegliere 1 per la dimensione del cluster ed eliminare il cluster dopo ogni uso per ridurre il costo.

    - **Configurazione opzionali** - virtuali configurare Azure, configurare le azioni di Script e aggiungere gli account di spazio di archiviazione aggiuntivo.

4. Fare clic su **Crea**.

>[AZURE.NOTE] Dopo l'eliminazione di un cluster di HBase, è possibile creare un altro cluster HBase con lo stesso account di archiviazione predefinito e il contenitore di blob predefinito. Il nuovo cluster preleverà tabelle HBase create cluster originale. Per evitare le incoerenze, è consigliabile disattivare le tabelle HBase prima di eliminare il cluster.

## <a name="create-tables-and-insert-data"></a>Creare tabelle e inserire i dati

Attualmente, esistono due modi per accedere a HBase. In questa sezione viene illustrato l'utilizzo di shell HBase. La sezione seguente viene illustrato l'utilizzo di .NET SDK.

Per la maggior parte delle persone, i dati vengono visualizzati in formato tabulare:

![hdinsight hbase tabulare][img-hbase-sample-data-tabular]

In HBase implementazione di BigTable, gli stessi dati è simile alla:

![hdinsight hbase bigtable dati][img-hbase-sample-data-bigtable]

È necessario essere comunque utile dopo aver completato la procedura successiva.  

**Per utilizzare shell HBase**

1. Utilizzare RDP per connettersi al cluster HBase in HDInsight. Per istruzioni RDP, vedere [gestire Hadoop cluster nel portale di Azure HDInsight][hdinsight-manage-portal].
2. La sessione RDP, fare clic su collegamento **riga di comando Hadoop** presente sul desktop.
3. Aprire la shell HBase:

        cd %HBASE_HOME%\bin
        hbase shell

4. Creare un HBase con due gruppi di colonna:

        create 'Contacts', 'Personal', 'Office'
        list
5. Inserire alcuni dati:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'

    ![hdinsight hadoop hbase shell][img-hbase-shell]

6. Ottenere una sola riga

        get 'Contacts', '1000'

    Verranno visualizzati gli stessi risultati ottenuti come con il comando analisi poiché c'è solo una riga.

    Per ulteriori informazioni sullo schema di tabella Hbase, vedere [Introduzione alla progettazione di Schema HBase][hbase-schema]. Per visualizzare altri comandi HBase, vedere [Guida di riferimento Apache HBase][hbase-quick-start].


6. Uscire da di shell

        exit

**Eseguire operazioni di massa Carica dati nella tabella HBase contatti**

HBase include diversi metodi di caricamento dei dati in tabelle. Per ulteriori informazioni, vedere [il caricamento di massa](http://hbase.apache.org/book.html#arch.bulk.load).


File di dati di esempio è stato caricato in un contenitore blob pubblico wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt. Il contenuto del file di dati è:

    8396    Calvin Raji     230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu        646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie        508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson    674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller   397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile    592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee       870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes      599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander 670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander   998-555-0171    230-555-0200    771 Northridge Drive

È possibile creare un file di testo e caricare il file per il proprio account di archiviazione se si desidera. Per istruzioni, vedere [caricare dati per i processi di Hadoop in HDInsight][hdinsight-upload-data].

> [AZURE.NOTE] Questa procedura utilizza la tabella HBase di contatti creata nella procedura precedente.

1. All'interno della sessione RDP, fare clic sul collegamento **della riga di comando Hadoop** disponibile sul desktop.
2. Modificare directory:

        cd %HBASE_HOME%\bin

3. Eseguire il seguente comando per trasformare il file di dati StoreFiles e archivio in un percorso specificato da Dimporttsv.bulk.output:

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Eseguire il seguente comando per caricare i dati da /example/data/storeDataFileOutput alla tabella HBase:

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. È possibile aprire la shell HBase e usare il comando analisi per visualizzare un elenco il contenuto della tabella.



## <a name="use-hive-to-query-hbase-tables"></a>Utilizzare Hive alle tabelle di query HBase

È possibile eseguire query di dati archiviati in HBase mediante Hive. In questa sezione Crea una tabella Hive mappato alla tabella HBase che viene utilizzato per i dati nella tabella HBase della query.

**Per aprire il dashboard cluster**

1. Passare alla **https://<HDInsight Cluster Name>.azurehdinsight.net/**.
5. Immettere il nome utente dell'account utente Hadoop e la password. Il nome utente predefinito è **admin** e la password è è stata immessa durante il processo di creazione. Verrà aperta una nuova scheda del browser.
6. Fare clic su **Editor Hive** nella parte superiore della pagina. L'Editor Hive è simile alla seguente:

    ![Dashboard di cluster HDInsight.][img-hdinsight-hbase-hive-editor]

**Per eseguire query Hive**

1. Immettere il seguente script di HiveQL in Hive Editor e fare clic su **Invia** per creare una tabella Hive associato alla tabella HBase. Assicurarsi che creato la tabella di esempio per fare riferimento in precedenza in questa esercitazione utilizza shell HBase prima di eseguire questa istruzione.

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

    Attendere gli aggiornamenti di **stato** **completato**.

2. Immettere il seguente script di HiveQL in Hive Editor e quindi fare clic su **Invia**. La query Hive query i dati nella tabella HBase:

        SELECT count(*) FROM hbasecontacts;

4. Per recuperare i risultati della query Hive, fare clic sul collegamento **Visualizza dettagli** nella finestra della **Sessione di processo** termine dell'esecuzione il processo. Si verificherà un solo file di output processo poiché viene inserito un record nella tabella HBase.




**Per individuare il file di output**

1. Nella Console di Query, fare clic su **File Browser**.
2. Fare clic sull'account di archiviazione Azure che viene utilizzato come il sistema di file predefinito per il cluster HBase.
3. Fare clic sul nome di cluster di HBase. Il nome cluster viene utilizzato il contenitore di account Azure dello spazio di archiviazione predefinito.
4. Fare clic su **utente**e quindi fare clic su **amministratore**. (È il nome dell'utente Hadoop).
6. Fare clic sul nome di processo con l'ora **Ultima modifica** corrispondente l'esecuzione quando la query selezionare Hive.
4. Fare clic su **stdout**. Salvare il file e aprire il file con il blocco note. Si verificherà un file di output.

    ![HDInsight HBase Hive Editor File Browser][img-hdinsight-hbase-file-browser]

## <a name="use-the-net-hbase-rest-api-client-library"></a>Utilizzare la libreria di client API REST HBase .NET

È necessario scaricare la raccolta di client API REST HBase per .NET da GitHub e compilare il progetto in modo che è possibile utilizzare HBase .NET SDK. La procedura seguente include le istruzioni per l'attività.

1. Creare una nuova applicazione di Visual Studio Windows Desktop Console c#.
2. Aprire la Console di gestione di pacchetti NuGet facendo clic su **Strumenti** > **Gestione pacchetti NuGet** > **Console di gestione pacchetti**.
3. Eseguire il seguente comando NuGet nella console di:

        Install-Package Microsoft.HBase.Client

5. Aggiungere le seguenti istruzioni **using** nella parte superiore del file:

        using Microsoft.HBase.Client;
        using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Sostituire la funzione **Main** con le operazioni seguenti:

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version.
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

            //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
            Scanner scanSettings = new Scanner()
            {
                batch = 10,
                startRow = BitConverter.GetBytes(25),
                endRow = BitConverter.GetBytes(35)
            };

            ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
            CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
            {
                foreach (CellSet.Row row in next.rows)
                {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
                }
            }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. Impostare le prime tre variabili nella funzione **Main** .
8. Premere **F5** per eseguire l'applicazione.

## <a name="check-cluster-status"></a>Controllare lo stato di cluster

HBase in HDInsight viene fornito con un'interfaccia utente Web per il monitoraggio cluster. Tramite l'interfaccia utente Web, è possibile richiedere statistiche o informazioni sulle aree.

Per aprire l'interfaccia utente Web, è necessario RDP in cluster, quindi fare clic sul collegamento dell'interfaccia utente Web Info HMaster sul desktop oppure utilizzare l'URL seguente in un web browser:

    http://zookeeper[0-2]:60010/master-status

In un cluster di disponibilità, è disponibile un collegamento al corrente attivo HBase nodo principale in cui si trova l'interfaccia utente Web.

##<a name="delete-the-cluster"></a>Eliminare il cluster
Per evitare le incoerenze, è consigliabile disattivare le tabelle HBase prima di eliminare il cluster.
[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="whats-next"></a>Che cos'è successiva?
In questa esercitazione HBase per HDInsight è stato illustrato come creare un cluster di HBase e su come creare tabelle e visualizzare i dati di tali tabelle da shell HBase. È inoltre appreso come utilizzare una query Hive sui dati in tabelle HBase e su come utilizzare le API di resto HBase c# per creare una tabella di HBase e recuperare i dati della tabella.

Per ulteriori informazioni, vedere:

- [Panoramica di HDInsight HBase][hdinsight-hbase-overview].
HBase è un database di NoSQL Apri origine, quest'ultimo è basato su Hadoop che fornisce l'accesso casuale e la coerenza sicura per grandi quantità di dati non strutturati e semistructured.
- [Creare cluster HBase su Azure virtuali][hdinsight-hbase-provision-vnet].
Con l'integrazione di rete virtuale, è possibile distribuire cluster HBase alla stessa rete virtuale delle applicazioni in modo che le applicazioni possono comunicare con HBase direttamente.
- [Replica di configurare HBase in HDInsight](hdinsight-hbase-geo-replication.md). Informazioni su come configurare la replica HBase tra due Data Center Azure.
- [Analizzare valutazione in Twitter con HBase in HDInsight][hbase-twitter-sentiment].
Informazioni su come eseguire [analisi di valutazione in](http://en.wikipedia.org/wiki/Sentiment_analysis) tempo reale di dati utilizzando HBase in un cluster di Hadoop in HDInsight.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-bigtable.png
