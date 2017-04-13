<properties 
    pageTitle="Spostare i dati di SQL Server in un computer virtuale Azure | Azure" 
    description="Spostare i dati da file flat o da un Server SQL locale a SQL Server in macchine Virtuali di Azure." 
    services="machine-learning" 
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
    ms.date="09/14/2016" 
    ms.author="bradsev" /> 

# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Spostare i dati di SQL Server in un computer virtuale Azure

In questo argomento vengono illustrate le opzioni per spostare i dati da un file flat (formati TSV o CSV) o da un Server SQL locale a SQL Server in un computer virtuale Azure. Le attività seguenti per spostare i dati nel cloud fanno parte del processo di scienze di dati del Team.

Per un argomento che illustra le opzioni per spostare i dati a un Database di SQL Azure per l'apprendimento, vedere [spostare i dati a un Database di SQL Azure per l'apprendimento Azure](machine-learning-data-science-move-sql-azure.md).

**Menu** collegamenti ad argomenti che descrivono come l'acquisizione di dati in altri ambienti di destinazione in cui i dati vengono memorizzati e durante il processo di scienza dati (TDSP) del Team, di elaborazione seguenti.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


Nella tabella seguente vengono riepilogate le opzioni per spostare i dati di SQL Server in un computer virtuale Azure.

<b>ORIGINE</b> |<b>DESTINAZIONE: SQL Server in macchine Virtuali di Azure</b> |
------------------ |-------------------- |
<b>File flat</b> |1. <a href="#insert-tables-bcp">utilità per la copia in blocco della riga di comando (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">Query SQL di inserimento di massa</a><br> 3. <a href="#sql-builtin-utilities">grafica utilità incorporate in SQL Server</a>
<b>SQL Server locale</b> | 1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">distribuire un Database di SQL Server per una procedura guidata macchine Virtuali di Microsoft Azure</a><br> 2. <a href="#export-flat-file">esportare in un File flat</a><br> 3. <a href="#sql-migration">Migrazione guidata Database di SQL</a> <br> 4. <a href="#sql-backup">database back backup e ripristino</a><br>

Si noti che il documento si presuppone che comandi SQL vengono eseguiti da SQL Server Management Studio o Visual Studio Database Explorer.

> [AZURE.TIP] In alternativa, è possibile utilizzare [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) per creare e pianificare una pipeline che consente di spostare dati una macchina virtuale Server SQL in Azure. Per ulteriori informazioni, vedere [copiare i dati con Azure Data Factory (attività Copia)](../data-factory/data-factory-data-movement-activities.md).


## <a name="prereqs"></a>Prerequisiti
In questa esercitazione si suppone che:

* Un **abbonamento Azure**. Se non si dispone di una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Un **account di archiviazione Azure**. Utilizzare un account di archiviazione Azure per archiviare i dati in questa esercitazione. Se non si dispone di un account di archiviazione Azure, vedere l'articolo [creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) . Dopo aver creato l'account di archiviazione, sarà necessario ottenere la chiave account usata per accedere allo spazio di archiviazione. Vedere [visualizzazione, copia e lo spazio di archiviazione rigenera i tasti di scelta](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Provisioning **SQL Server in una macchina virtuale Azure**. Per ulteriori informazioni, vedere [configurare una macchina virtuale Azure SQL Server come server di blocco appunti IPython per analitica avanzate](machine-learning-data-science-setup-sql-server-virtual-machine.md).
* Installato e configurato **Azure PowerShell** in locale. Per ulteriori informazioni, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).


## <a name="filesource_to_sqlonazurevm"></a>Spostamento di dati da un'origine file flat a SQL Server in macchine Virtuali un Azure

Se i dati in un file flat (ordinato in un formato di righe/colonne), può essere spostata in macchine Virtuali di SQL Server in Azure tramite i metodi seguenti:

1. [Utilità per la copia in blocco della riga di comando (BCP)](#insert-tables-bcp) 
2. [Query SQL di inserimento in blocco](#insert-tables-bulkquery)
3. [Grafica utilità incorporate in SQL Server (importazione/esportazione, SSIS)](#sql-builtin-utilities)


### <a name="insert-tables-bcp"></a>Utilità per la copia in blocco della riga di comando (BCP)

BCP è uno strumento della riga di comando installato con SQL Server ed è uno dei modi più veloce per spostare i dati. Funziona per tutte le varianti di SQL Server tre (SQL Server locale, SQL Azure e macchine Virtuali di SQL Server in Azure). 

> [AZURE.NOTE]**Nel punto in cui i dati per devono essere BCP?**  
> Sebbene non sia obbligatorio, il file contenente dati di origine che si trovano nello stesso computer server SQL di destinazione consente di velocizzare trasferimenti (rete velocità vs disco locale velocità IO). È possibile spostare i file flat contenente i dati nel computer in cui è installato SQL Server tramite vari file copia strumenti, ad esempio [AZCopy](../storage/storage-use-azcopy.md), [Esplora archivi Azure](http://storageexplorer.com/) o windows Copia/Incolla tramite RDP Remote Desktop Protocol ().

1. Assicurarsi che il database e le tabelle vengono create nel database di SQL Server di destinazione. Ecco un esempio di come eseguire tale utilizzando il `Create Database` e `Create Table` comandi:

        CREATE DATABASE <database_name>
    
        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        ) 
    
2. Generare del formato di file che descrive lo schema per la tabella mediante il comando seguente dalla riga di comando del computer in cui è installato bcp.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n` 

3. Inserire i dati nel database utilizzando il comando bcp come indicato di seguito. Elaborare dalla riga di comando supponendo che SQL Server sia installato nello stesso computer:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Ottimizzazione BCP inserisce** Vedere l'articolo seguente ['Linee guida per l'importazione in blocco ottimizzazione'](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) ottimizzare tali inserimenti.


### <a name="insert-tables-bulkquery-parallel"></a>Parallelo viene inserito per lo spostamento di dati più veloce

Se i dati da spostare sono elevati, è possibile accelerare operazioni eseguendo contemporaneamente più comandi BCP in parallelo in uno Script di PowerShell.

> [AZURE.NOTE]**Acquisizione dati** Per ottimizzare i dati di caricamento per set di dati di grandi, suddividere le tabelle di database fisici e logici con più tabelle di gruppi e partizione. Per ulteriori informazioni sulla creazione e il caricamento dei dati alle tabelle di partizione, vedere [Tabelle delle partizioni SQL carico in parallelo](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).


Lo script di PowerShell di esempio seguente viene illustrato in parallelo viene inserito tramite bcp:
    
    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
           bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

            #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
            #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n 
      }
    

    # Background processing of all partitions
    for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
    {
      Write-Debug "Submit loading partition # $i"
      Start-Job $ScriptBlock -Arg $i      
    }
    

    # Wait for it all to complete
    While (Get-Job -State "Running")
    {
      Start-Sleep 10
      Get-Job
    }
    
    # Getting the information back from the jobs
    Get-Job | Receive-Job
    Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>Query SQL di inserimento in blocco

[Query SQL di inserimento di massa](https://msdn.microsoft.com/library/ms188365) può essere utilizzato per importare dati nel database dai file in base a righe/colonne (tipi supportati illustra sono[Dati di preparare per blocco esportare o importare (SQL Server)](https://msdn.microsoft.com/library/ms188609)) argomento. 

Ecco alcuni esempi dei comandi per l'inserimento di massa sono come indicato di seguito:  

1. Analizzare i dati e impostare le opzioni personalizzate prima di importare per assicurarsi che il database di SQL Server prende in considerazione lo stesso formato per tutti i campi speciali, ad esempio date. Ecco un esempio di come impostare il formato di data come giorno di mese anno (se i dati contengono la data in formato anno-mese-giorno):

        SET DATEFORMAT ymd; 
    
2. Importare dati tramite le istruzioni di importazione in blocco:

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH 
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )
      

### <a name="sql-builtin-utilities"></a>Utilità incorporate in SQL Server

È possibile usare SQL Server integrazioni Services (SSIS) per importare dati in macchine Virtuali di SQL Server in Azure da un file flat. SSIS è disponibile nei due ambienti di studio. Per informazioni dettagliate, vedere [ambienti di Studio e Integration Services (SSIS)](https://technet.microsoft.com/library/ms140028.aspx):

- Per informazioni dettagliate su SQL Server Data Tools, vedere [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
- Per informazioni sull'importazione/esportazione guidata, vedere [SQL Server importazione / esportazione guidata](https://msdn.microsoft.com/library/ms141209.aspx)


## <a name="sqlonprem_to_sqlonazurevm"></a>Spostamento di dati da SQL Server locale a SQL Server in una macchina virtuale Azure

È inoltre possibile utilizzare le seguenti strategie di migrazione:

1. [Distribuire un Database SQL Server per una procedura guidata macchine Virtuali di Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Esportare in File Flat](#export-flat-file) 
3. [Migrazione guidata Database di SQL](#sql-migration)
4. [Database back backup e ripristino](#sql-backup)

Descritti delle operazioni seguenti:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Distribuire un Database SQL Server per una procedura guidata macchine Virtuali di Microsoft Azure

**Distribuire un Database di SQL Server per una procedura guidata macchine Virtuali di Microsoft Azure** è un modo semplice e consigliato per spostare i dati da un'istanza di SQL Server locale a SQL Server in macchine Virtuali un Azure. Per istruzioni dettagliate, nonché una descrizione delle altre alternative, vedere [eseguire la migrazione di un database di SQL Server in macchine Virtuali un Azure](../virtual-machines/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Esportare in File Flat

Vari metodi possono essere utilizzati per massa esportare dati da un Server SQL locale, come illustrato nell'argomento di [massa importazione ed esportazione di dati (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) . Questo documento riguarderà la programma di copia in blocco (scelta) come esempio. Dopo l'esportazione in un file flat, possono essere importato in un altro SQL server tramite l'importazione in blocco. 

1. Esportare i dati da SQL Server locale in un File con l'utilità bcp come indicato di seguito

    `bcp dbname..tablename out datafile.tsv -S  servername\sqlinstancename -T -t \t -t \n -c`

2. Creare il database e la tabella in macchine Virtuali di SQL Server Azure tramite il `create database` e `create table` allo schema della tabella esportato nel passaggio 1.

3. Creare un file in formato per descrivere lo schema della tabella dati esportazione/importazione di. Dettagli del file in formato sono descritti in [creare un formato di File (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Formattare la generazione di file durante l'esecuzione di BCP dal computer SQL Server 

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n 

    Formattare la generazione di file durante l'esecuzione BCP in modalità remota su un Server SQL 

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
        
    
4. Utilizzare uno dei metodi descritti nella sezione [Spostare dati da File di origine](#filesource_to_sqlonazurevm) per spostare i dati in file flat a SQL Server.

### <a name="sql-migration"></a>Migrazione guidata Database di SQL

[Migrazione guidata Database di SQL Server](http://sqlazuremw.codeplex.com/) offre un modo semplice per spostare i dati tra due istanze di SQL server. Consente all'utente di eseguire il mapping lo schema dei dati tra origini e tabelle di destinazione, scegliere tipi di colonna e diverse altre funzionalità. Viene utilizzato in blocco copia di dietro le quinte. Di seguito è riportata una schermata della schermata iniziale per la procedura guidata la migrazione del Database di SQL.  

![Migrazione guidata SQL Server][2]

### <a name="sql-backup"></a>Database back backup e ripristino

SQL Server supporta: 

1. [Database back backup e ripristinare le funzionalità](https://msdn.microsoft.com/library/ms187048.aspx) (entrambi a un file locale o bacpac esportare in blob) e [Le applicazioni di livello dati](https://msdn.microsoft.com/library/ee210546.aspx) (con bacpac). 
2. Possibilità di creare direttamente macchine virtuali di SQL Server in Azure con un database copiato o copia a un database SQL Azure esistente. Per ulteriori informazioni, vedere [utilizzare Copia guidata Database](https://msdn.microsoft.com/library/ms188664.aspx). 

Schermata della parte posteriore Database backup/ripristino opzioni da SQL Server Management Studio è illustrato di seguito.

![Strumento di importazione SQL Server][1]

## <a name="resources"></a>Risorse

[Eseguire la migrazione di un Database di SQL Server in una macchina virtuale Azure](../virtual-machines/virtual-machines-windows-migrate-sql.md)

[SQL Server in macchine virtuali di Azure Panoramica](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png
