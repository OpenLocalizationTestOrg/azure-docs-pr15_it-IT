<properties
    pageTitle="HBase esercitazione: introduzione con i cluster basati su Linux HBase Hadoop | Microsoft Azure"
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
    ms.topic="get-started-article"
    ms.date="10/19/2016"
    ms.author="jgao"/>



# <a name="hbase-tutorial-get-started-using-apache-hbase-with-linux-based-hadoop-in-hdinsight"></a>Esercitazione HBase: iniziare a usare Apache HBase con basati su Linux Hadoop in HDInsight 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Informazioni su come creare un cluster di HBase in HDInsight, creare tabelle HBase ed eseguire query su tabelle utilizzando Hive. Per informazioni generali HBase, vedere [Panoramica di HDInsight HBase][hdinsight-hbase-overview].

Le informazioni contenute in questo documento sono specifiche di cluster basati su Linux HDInsight. Per informazioni sui cluster basato su Windows, usare il selettore di tabulazione nella parte superiore della pagina per passare.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione HBase, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Secure Shell(SSH)](hdinsight-hadoop-linux-use-ssh-unix.md). 
- [voltare](http://curl.haxx.se/download.html).

### <a name="access-control-requirements"></a>Requisiti di controllo accesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Creare HBase cluster

La procedura seguente utilizza un modello di gestione di risorse Azure per creare un cluster di basati su Linux HBase versione 3.4 e account Azure lo spazio di archiviazione predefinito dipendente. Per comprendere i parametri utilizzati nella procedure e altri metodi di creazione cluster, vedere [cluster basati su Linux creare Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Fare clic sull'immagine seguente per aprire il modello nel portale di Azure. Il modello si trova in un contenitore di blob pubblico. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Da e il **distribuzione personalizzata** , immettere quanto segue:

    - **Abbonamento**: selezionare l'abbonamento Azure che verrà utilizzato per creare il cluster.
    - **Gruppo risorse**: creare un nuovo gruppo di gestione delle risorse Azure o utilizzarne uno esistente.
    - **Posizione**: specificare il percorso del gruppo di risorse. 
    - **Nome cluster**: immettere un nome per il cluster HBase che verrà creato.
    - **Nome di accesso cluster e la password**: il nome di accesso predefinito è **amministratore**.
    - **SSH nome utente e password**: il nome utente predefinito è **sshuser**.  È possibile rinominarla.
     
    Gli altri parametri sono facoltativi.  
    
    Ogni cluster dispone di una relazione di account di archiviazione Blob Azure. Dopo aver eliminato un cluster, i dati vengono mantenuti nell'account di archiviazione. Il nome dell'account cluster lo spazio di archiviazione predefinito è il nome del cluster con "store" aggiunto. È hardcoded nella sezione modello variabili.
        
3. Selezionare **Accetto i termini e condizioni indicate in precedenza**e quindi fare clic su **Acquista**. Bastano circa 20 minuti per creare un cluster.


>[AZURE.NOTE] Dopo l'eliminazione di un cluster di HBase, è possibile creare un altro cluster HBase utilizzando lo stessa contenitore blob predefinito. Il nuovo cluster preleverà tabelle HBase create cluster originale. Per evitare le incoerenze, è consigliabile disattivare le tabelle HBase prima di eliminare il cluster.

## <a name="create-tables-and-insert-data"></a>Creare tabelle e inserire i dati

È possibile utilizzare SSH per la connessione a cluster HBase e quindi utilizzare HBase Shell per creare tabelle HBase, inserire i dati e query. Per informazioni sull'utilizzo SSH, vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md) e [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
 

Per la maggior parte delle persone, i dati vengono visualizzati in formato tabulare:

![HDInsight HBase tabulare][img-hbase-sample-data-tabular]

In HBase implementazione di BigTable, gli stessi dati è simile alla:

![HDInsight HBase bigtable dati][img-hbase-sample-data-bigtable]

Può essere preferibile dopo aver completato la procedura successiva.  


**Per utilizzare shell HBase**

1. Da SSH, eseguire il comando seguente:

        hbase shell

4. Creare un HBase con le famiglie di due colonne:

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

    Verranno visualizzati gli stessi risultati ottenuti con il comando analisi poiché c'è solo una riga.

    Per ulteriori informazioni sullo schema di tabella HBase, vedere [Introduzione alla progettazione di Schema HBase][hbase-schema]. Per visualizzare altri comandi HBase, vedere [Guida di riferimento Apache HBase][hbase-quick-start].

6. Uscire da di shell

        exit



**Eseguire operazioni di massa Carica dati nella tabella HBase contatti**

HBase include diversi metodi di caricamento dei dati in tabelle.  Per ulteriori informazioni, vedere [il caricamento di massa](http://hbase.apache.org/book.html#arch.bulk.load).


File di dati di esempio è stato caricato in un contenitore blob pubblico *wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*.  Il contenuto del file di dati è:

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

> [AZURE.NOTE] Questa procedura viene utilizzata la tabella di HBase di contatti creata nella procedura precedente.

1. Da SSH, eseguire il seguente comando per trasformare il file di dati StoreFiles e archivio in un percorso specificato da Dimporttsv.bulk.output:.  Se trovano in HBase Shell, utilizzare il comando Esci per uscire da.

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Eseguire il seguente comando per caricare i dati da /example/data/storeDataFileOutput alla tabella HBase:

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. È possibile aprire la shell HBase e usare il comando analisi per visualizzare un elenco il contenuto della tabella.



## <a name="use-hive-to-query-hbase"></a>Consente di query HBase Hive

È possibile eseguire query di dati in tabelle HBase utilizzando Hive. In questa sezione Crea una tabella Hive mappato alla tabella HBase che viene utilizzato per i dati nella tabella HBase della query.

1. Aprire **PuTTY**e connettersi al cluster.  Vedere le istruzioni della procedura precedente.
2. Aprire la shell Hive.

       hive
3. Eseguire il seguente script HiveQL per creare una tabella Hive associato alla tabella HBase. Assicurarsi che sia stato creato la tabella di esempio per fare riferimento in precedenza in questa esercitazione utilizza shell HBase prima di eseguire questa istruzione.

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. Eseguire il seguente script HiveQL per i dati nella tabella HBase della query:

        SELECT count(*) FROM hbasecontacts;

## <a name="use-hbase-rest-apis-using-curl"></a>Utilizzare le API REST HBase utilizzando curvatura

> [AZURE.NOTE] Quando si usa curvatura o altre comunicazioni di resto con WebHCat, è necessario autenticare le richieste di fornire il nome utente e la password per l'amministratore di cluster HDInsight. È anche necessario utilizzare il nome del cluster come parte della risorsa identificatore URI (Uniform) utilizzato per inviare le richieste al server.
>
> Per i comandi in questa sezione, sostituire **nomeutente** con l'utente per eseguire l'autenticazione al cluster e sostituire **PASSWORD** con la password per l'account utente. Sostituire **nome cluster** con il nome del cluster.
>
> API REST è protetto tramite [l'autenticazione di base](http://en.wikipedia.org/wiki/Basic_access_authentication). È consigliabile eseguire sempre le richieste tramite HTTP sicura (HTTPS) per garantire che le credenziali vengono inviate in modo sicuro nel server.

1. Dalla riga di comando, utilizzare il comando seguente per verificare che sia possibile connettersi al cluster HDInsight:

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

    Si riceverà una risposta simile al seguente:

        {"status":"ok","version":"v1"}

    I parametri utilizzati in questo comando sono i seguenti:

    * **-u** - il nome utente e la password utilizzati per autenticare la richiesta.
    * **Della lettera G** - indica che si tratta di una richiesta GET.

2. Usare il comando seguente per visualizzare un elenco di tabelle HBase esistenti:

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. Utilizzare il comando seguente per creare una nuova tabella HBase con due gruppi di colonna:

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
        -v

    Lo schema viene specificato nel formato JSon.

4. Usare il comando seguente per inserire alcuni dati:

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"Row\":{\"key\":\"MTAwMA==\",\"Cell\":{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}}}" \
        -v

    È necessario base 64 codificare i valori specificati nel parametro -d.  Nell'esempio:

    - MTAwMA = =: 1000
    - UGVyc29uYWw6TmFtZQ = =: personale: nome
    - Sm9obiBEb2xl: John Dole

    [chiave di riga false](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) consente di inserire più valori (in batch).

5. Utilizzare il comando seguente per ottenere una riga:

        curl -u <UserName>:<Password> \
        -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
        -H "Accept: application/json" \
        -v

Per ulteriori informazioni su HBase resto, vedere [Guida di riferimento HBase Apache](https://hbase.apache.org/book.html#_rest).

## <a name="check-cluster-status"></a>Controllare lo stato di cluster

HBase in HDInsight viene fornito con un'interfaccia utente Web per il monitoraggio cluster. Tramite l'interfaccia utente Web, è possibile richiedere statistiche o informazioni sulle aree.

SSH possono essere utilizzate anche per tunnel richieste locali, ad esempio richieste web, al cluster HDInsight. La richiesta quindi instradata alla risorsa richiesta come se fosse originari sul nodo principale cluster HDInsight. Per ulteriori informazioni, vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel).

**Per stabilire un SSH tunneling sessione**

1. Aprire **PuTTY**.  
2. Se una chiave SSH durante la creazione dell'account utente durante il processo di creazione, è necessario eseguire il passaggio successivo per selezionare la chiave privata da utilizzare per l'autenticazione al cluster:

    Nella **categoria**, espandere **connessione**, espandere **SSH**e selezionare **Auth**. Infine, fare clic su **Sfoglia** e selezionare il file .ppk che contiene la chiave privata.

3. Nella **categoria**, fare clic su **sessione**.
4. Una delle opzioni di base per lo schermo PuTTY sessione, immettere i valori seguenti:

    - **Nome host**: l'indirizzo SSH del campo server del nome Host (o l'indirizzo IP) HDInsight. L'indirizzo SSH è il nome del grafico, quindi **-ssh.azurehdinsight.net**. Ad esempio *miocluster ssh.azurehdinsight.net*.
    - **Porta**: 22. La porta headnode primaria ssh è 22.  
5. Nella sezione **categoria** a sinistra della finestra di dialogo espandere **connessione**, espandere **SSH**e quindi fare clic su **tunnel**.
6. Specificare le seguenti informazioni sulle opzioni del controllo della maschera di inoltro porta SSH:

    - **Porta di origine** - porta sul client che si desidera inoltrare. Ad esempio 9876.
    - **Dinamico** - proxy SOCKS dinamiche consente il routing.
7. Fare clic su **Aggiungi** per aggiungere le impostazioni.
8. Fare clic su **aprire** nella parte inferiore della finestra di dialogo per aprire un collegamento SSH.
9. Quando richiesto, accedere al server con un account SSH. Per stabilire una sessione SSH e abilitare il tunnel.

**Per trovare il FQDN di zookeepers con Ambari**

1. Passare a https://<ClusterName>.azurehdinsight.net/.
2. Immettere le credenziali dell'account utente cluster due volte.
3. Nel menu a sinistra, fare clic su **zookeeper**.
4. Fare clic su uno dei tre collegamenti **ZooKeeper Server** dall'elenco di riepilogo.
5. Copiare **il nome host**. Ad esempio zk0-CLUSTERNAME.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net.

**Per configurare un programma client (Firefox) e controllare lo stato di cluster**

1. Aprire Firefox.
2. Fare clic sul pulsante **Apri Menu** .
3. Fare clic su **Opzioni**.
4. Fare clic su **Avanzate**, fare clic su **rete**e quindi fare clic su **Impostazioni**.
5. Selezionare **configurazione manuale del proxy**.
6. Immettere i valori seguenti:

    - **Socks Host**: host locale
    - **Porta**: utilizzare la stessa porta configurato nel Putty SSH questa.  Ad esempio 9876.
    - **SOCKS v5**: (selezionata)
    - **DNS remoto**: (selezionata)
7. Fare clic su **OK** per salvare le modifiche.
8. Passare a http://&lt;nome di dominio completo di un ZooKeeper >: 60010/schema-stato.

In un cluster di disponibilità, si noterà un collegamento al corrente attivo HBase nodo principale in cui si trova l'interfaccia utente Web.

##<a name="delete-the-cluster"></a>Eliminare il cluster

Per evitare le incoerenze, è consigliabile disattivare le tabelle HBase prima di eliminare il cluster.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione HBase per HDInsight è stato illustrato come creare un cluster di HBase e su come creare tabelle e visualizzare i dati di tali tabelle da shell HBase. Inoltre appreso come utilizzare una query Hive sui dati nelle tabelle HBase e su come utilizzare le API di resto HBase c# per creare una tabella di HBase e recuperare i dati della tabella.

Per ulteriori informazioni, vedere:

- [Panoramica di HDInsight HBase][hdinsight-hbase-overview]: HBase è un database di NoSQL Apri origine, quest'ultimo è basato su Hadoop che fornisce l'accesso casuale e la coerenza sicura per grandi quantità di dati non strutturati e semistructured.


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
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png
