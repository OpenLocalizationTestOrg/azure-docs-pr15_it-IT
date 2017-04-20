<properties 
    pageTitle="Parallelo importazione globale di dati mediante tabelle delle partizioni SQL | Microsoft Azure" 
    description="Utilizzo delle tabelle di partizione SQL parallelo Bulk Data Import" 
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
    ms.date="09/19/2016" 
    ms.author="bradsev" /> 

# <a name="parallel-bulk-data-import-using-sql-partition-tables"></a>Utilizzo delle tabelle di partizione SQL parallelo Bulk Data Import

Questo documento viene descritto come creare tabelle partizionate blocco fast parallelo l'importazione di dati a un database di SQL Server. Per big caricamento/trasferimento di dati a un database SQL, importazione dei dati al database SQL e query successive possono essere migliorata utilizzando _partizionati tabelle e visualizzazioni_. 


## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Creare un nuovo database e un insieme di gruppi di file

- [Crea un nuovo database del](https://technet.microsoft.com/library/ms176061.aspx) (se non esiste)
- Aggiungere gruppi di file di database per il database che conterrà i file fisici partizionati

  Nota: Questo è possibile eseguire [Creare DATABASE](https://technet.microsoft.com/library/ms176061.aspx) se la nuova o [ALTER DATABASE](https://msdn.microsoft.com/library/bb522682.aspx) se il database esiste già

- Aggiungere uno o più file (in base alle esigenze) a ogni gruppo di file di database

 > [AZURE.NOTE] Consente di specificare il gruppo di destinazione file che contiene i dati per la partizione e i nomi dei file di database fisico in cui verranno archiviati i dati di filegroup.
 
Nell'esempio seguente viene creato un nuovo database con tre gruppi di file diverso da primario e dei gruppi di registro contenente un file fisico in ogni. I file di database vengono creati nella cartella di dati di SQL Server predefinita, secondo quanto configurato nell'istanza di SQL Server. Per ulteriori informazioni sui percorsi di file predefinito, vedere [Percorsi di File per impostazione predefinita e denominata istanze di SQL Server](https://msdn.microsoft.com/library/ms143547.aspx).

    DECLARE @data_path nvarchar(256);
    SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);
    
    EXECUTE ('
        CREATE DATABASE <database_name>
        ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ), 
        LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')
    
## <a name="create-a-partitioned-table"></a>Creare una tabella partizionata

Creare tabelle partizionate in conformità allo schema di dati, mappato a filegroup database creato nel passaggio precedente. Una volta dati importato per le tabelle partizionate blocco, record verranno distribuiti tra i gruppi di file in base a uno schema di partizione, come descritto di seguito.

**Per creare una tabella di partizione, è necessario:**

- [Crea una funzione di partizione](https://msdn.microsoft.com/library/ms187802.aspx) che definisce l'intervallo di valori/limiti da includere in ogni tabella di partizione singoli, ad esempio, per limitare le partizioni in base al mese (alcuni\_datetime\_campo) nell'anno 2013:

        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

- [Crea uno schema di partizione](https://msdn.microsoft.com/library/ms179854.aspx) che associa ogni intervallo di partizione in funzione partizione a un filegroup fisico, ad esempio:

        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )

  Suggerimento: Per verificare gli intervalli attive in ogni partizione in base alla funzione/schema, eseguire la query seguente:

        SELECT psch.name as PartitionScheme,
            prng.value AS ParitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

- [Creare la tabella partizionata](https://msdn.microsoft.com/library/ms174979.aspx) (s) in base allo schema dei dati e specificare il campo schema e vincoli di partizione consentono di partizionare la tabella, ad esempio:

        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

Per ulteriori informazioni, vedere [creare partizionati tabelle e indici](https://msdn.microsoft.com/library/ms188730.aspx).


## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Importazione di massa i dati per ogni tabella di partizione singoli

- È possibile utilizzare BCP, inserimento di massa o altri metodi, ad esempio [Migrazione guidata di SQL Server](http://sqlazuremw.codeplex.com/). L'esempio fornito viene utilizzato il metodo BCP.

- [Alter database](https://msdn.microsoft.com/library/bb522682.aspx) per modificare la combinazione di registro delle transazioni di registrazione di massa di ridurre il sovraccarico di registrazione, ad esempio:

        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED

- Per accelerare il caricamento dei dati, avviare le operazioni di importazione in blocco in parallelo. Per suggerimenti sulla accelerare blocco importazione dei dati di grandi dimensioni in database di SQL Server, vedere [caricare 1 TB in meno di 1 ora](http://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx).

Lo script di PowerShell seguente è riportato un esempio in parallelo caricamento dei dati tramite BCP.

    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0
    
    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>
       
    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"
   
    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir
      
    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }
    
    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }
    
    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }
    
    Get-Job
    
    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Creare indici per ottimizzare le prestazioni delle query e join

- Se si verranno estrarre i dati per la modellazione da più tabelle, creare indici nelle chiavi di join per migliorare le prestazioni di join.

- [Creare gli indici](https://technet.microsoft.com/library/ms188783.aspx) (in cluster o non cluster) assegnazione stesso filegroup per ogni partizione, ad esempio, per:

        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
In alternativa,

        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)

 > [AZURE.NOTE] È possibile creare indici prima di importare i dati di massa. Creazione di un indice prima dell'importazione di massa rallenta il caricamento dei dati.


## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Processo Analitica avanzate e tecnologia nell'esempio di azione

Per un esempio di questa procedura dettagliata end-to-end utilizzando il processo di Analitica Cortana con un dataset pubblico, vedere [Cortana Analitica processo in azione: utilizzo di SQL Server](machine-learning-data-science-process-sql-walkthrough.md).
 
