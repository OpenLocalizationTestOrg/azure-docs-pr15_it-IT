<properties
    pageTitle="Usare Hadoop Sqoop in basati su Linux HDInsight | Microsoft Azure"
    description="Informazioni su come eseguire Sqoop importare ed esportare tra un Hadoop basati su Linux cluster HDInsight e un database SQL Azure."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="larryfr"/>

#<a name="use-sqoop-with-hadoop-in-hdinsight-ssh"></a>Utilizzare Sqoop con Hadoop in HDInsight (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare Sqoop per importare ed esportare tra un cluster HDInsight basati su Linux e i Database di SQL Azure o SQL Server.

> [AZURE.NOTE] La procedura descritta in questo articolo consente di connettersi a un cluster basati su Linux HDInsight SSH. Client Windows inoltre possono utilizzare PowerShell Azure e HDInsight .NET SDK per l'uso con Sqoop nei cluster basati su Linux. Utilizzare il selettore di tabulazione per aprire questi articoli.

##<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Cluster A Hadoop in HDInsight** e un __Database SQL Azure__: I passaggi descritti in questo documento sono in base al cluster e database creati con il documento [Crea cluster e database SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database) . Se si dispone già di un cluster di HDInsight e di Database SQL, è possibile sostituire i valori utilizzati in questo documento.
- **Workstation**: un computer con un client SSH.

##<a name="install-freetds"></a>Installare FreeTDS

1. Utilizzare SSH per connettersi al cluster basati su Linux HDInsight. È l'indirizzo da utilizzare quando ci si connette `CLUSTERNAME-ssh.azurehdinsight.net` e la porta `22`.

    Per ulteriori informazioni sull'utilizzo di SSH per connettersi a HDInsight, vedere i documenti seguenti:

    * **Client Linux, Unix o OS X**: vedere [connettersi a un cluster basati su Linux HDInsight da Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Client Windows**: vedere [connettersi a un cluster basati su Linux HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

3. Usare il comando seguente per installare FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

    FreeTDS verrà utilizzato in vari passaggi per connettersi al Database SQL.

##<a name="create-the-table-in-sql-database"></a>Creare la tabella nel Database SQL

> [AZURE.IMPORTANT] Se si utilizza un cluster di HDInsight e di Database SQL di creato con la procedura descritta in [Crea cluster e database SQL](hdinsight-use-sqoop.md), ignorare i passaggi descritti in questa sezione in quanto il database e tabella sono stati creati durante la procedura descritta in questo documento.

1. Da connessione SSH a HDInsight, utilizzare il comando seguente per connettersi al Database SQL server e creta la tabella che verrà utilizzata nel resto della procedura:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Si riceverà output simile al seguente:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. Nel `1>` richiesto, immettere le righe seguenti:

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
        GO

    Quando il `GO` viene immessa, le istruzioni precedenti verranno valutate. Innanzitutto, viene creata la tabella **mobiledata** , quindi un indice cluster viene aggiunto a essa (richiesto da Database SQL).

    Per verificare che la tabella è stata creata, utilizzare le operazioni seguenti:

        SELECT * FROM information_schema.tables
        GO

    È necessario vedere output simile al seguente:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

8. Immettere `exit` nel `1>` prompt uscire dall'utilità tsql.

##<a name="sqoop-export"></a>Esportazione di Sqoop

3. Da connessione SSH a HDInsight, se il comando seguente per verificare che Sqoop possibilità di visualizzare il Database di SQL:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    In questo modo un elenco di database, incluso il database **sqooptest** creata in precedenza.

4. Per esportare i dati da **hivesampletable** alla tabella **mobiledata** , utilizzare il comando seguente:

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    Questo indica Sqoop per connettersi al Database SQL, al database **sqooptest** ed esportare dati dalla **wasbs: / / / hive/warehouse/hivesampletable** (file fisici per *hivesampletable*,) per la tabella **mobiledata** .

5. Al termine dell'esecuzione del comando, utilizzare le operazioni seguenti per la connessione al database mediante TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Una volta connessa, usare le istruzioni seguenti per verificare che i dati è stati esportati alla tabella **mobiledata** :

        SELECT * FROM mobiledata
        GO

    Verrà visualizzato un elenco di dati della tabella. Tipo di `exit` per uscire dall'utilità tsql.

##<a name="sqoop-import"></a>Importazione di Sqoop

1. Utilizzare le operazioni seguenti per importare dati dalla tabella **mobiledata** nel Database di SQL, per il **wasbs: / / / esercitazioni/usesqoop/importeddata** directory HDInsight:

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    I dati importati avrà campi separati da un carattere di tabulazione e le righe verranno interrotte da un carattere di nuova riga.

2. Una volta completata l'importazione, utilizzare il comando seguente all'elenco dei dati nella nuova directory:

        hadoop fs -text wasbs:///tutorials/usesqoop/importeddata/part-m-00000

##<a name="using-sql-server"></a>Utilizzo di SQL Server

È anche possibile utilizzare Sqoop per importare ed esportare i dati da SQL Server nell'interfaccia di dati o in una macchina virtuale ospitata in Azure. Differenze nell'utilizzo di Database SQL e SQL Server sono:

* HDInsight e SQL Server deve essere sulla stessa rete virtuale di Azure

    > [AZURE.NOTE] HDInsight supporta solo le reti virtuali basata sulla posizione e non è attualmente funziona con reti virtuale gruppo affinità.

    Quando si utilizza SQL Server nel centro dati, è necessario configurare la rete virtuale come *da sito* o un *punto al sito*.

    > [AZURE.NOTE] Per le reti virtuali **punto al sito** , SQL Server deve eseguire il client VPN applicazione di configurazione, è disponibile nel **Dashboard** della configurazione di rete virtuale Azure.

    Per ulteriori informazioni virtuali Azure, vedere [Panoramica di rete virtuale](../virtual-network/virtual-networks-overview.md).

* SQL Server deve essere configurato per consentire l'autenticazione di SQL. Per ulteriori informazioni, vedere [scegliere una modalità di autenticazione](https://msdn.microsoft.com/ms144284.aspx)

* È necessario configurare SQL Server per accettare le connessioni remote. Per ulteriori informazioni, vedere [come risolvere i problemi di connessione al motore di database SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)

* È necessario creare il database **sqooptest** in SQL Server tramite un'utilità, ad esempio **SQL Server Management Studio** o **tsql** - la procedura per l'uso di CLI Azure funziona solo per il Database di SQL Azure

    Istruzioni SQL per creare la tabella **mobiledata** sono simili quelli utilizzati per Database SQL, fatta eccezione per la creazione di un clusterd index - non se è necessario per SQL Server:

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])

* Quando ci si connette a SQL Server da HDInsight, potrebbe essere necessario usare l'indirizzo IP di SQL Server, a meno che non è stato configurato un sistema DNS (Domain Name) per la risoluzione dei nomi nella rete virtuale Azure. Per esempio:

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##<a name="limitations"></a>Limitazioni

* Esporta in blocco - HDInsight basati su con Linux, il connettore Sqoop utilizzato per esportare i dati a Microsoft SQL Server o Database SQL Azure attualmente non supporta inserimenti di massa.

* Divisione in batch - con basati su Linux HDInsight quando si usa il `-batch` passa durante l'esecuzione di inserimenti, Sqoop eseguirà più inserimenti anziché in batch le operazioni di inserimento.

##<a name="next-steps"></a>Passaggi successivi

A questo punto sono appreso come utilizzare Sqoop. Per ulteriori informazioni, vedere:

- [Utilizzare Oozie con HDInsight][hdinsight-use-oozie]: usare Sqoop azione in un flusso di lavoro Oozie.
- [Analizzare i dati dei ritardi volo utilizzando HDInsight][hdinsight-analyze-flight-data]: utilizzare Hive analizzare volo ritardare dati e quindi utilizzare Sqoop per esportare i dati in un database SQL Azure.
- [Caricare dati HDInsight][hdinsight-upload-data]: trovare altri metodi per il caricamento di dati in archiviazione Blob/Azure HDInsight.



[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
