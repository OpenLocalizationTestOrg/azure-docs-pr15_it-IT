<properties
    pageTitle="Il processo di scienze Team dei dati in azione: utilizzo SQL Data Warehouse | Microsoft Azure"
    description="Processo Analitica avanzate e la tecnologia in azione"  
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
    ms.date="06/24/2016"
    ms.author="bradsev;hangzh;weig"/>


# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>Il processo di scienze Team dei dati in azione: utilizzo SQL Data Warehouse

In questa esercitazione si guidano l'utente tramite la creazione e la distribuzione di un modello utilizzando SQL Data Warehouse (SQL DW) di apprendimento per un set di dati disponibile al pubblico, ossia il set di dati di [Roma Taxi viaggi](http://www.andresmh.com/nyctaxitrips/) . Il modello di classificazione binario impostato prevede o meno un suggerimento è pagato un viaggio e modelli per la classificazione multiclass e regressione sono descritti che prevede la distribuzione per gli importi di suggerimento pagati.

La procedura segue il flusso di lavoro di [Team dati scienza processo (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) . Ecco come configurare un ambiente di scienze di dati, come caricare i dati in SQL DW e come usare SQL DW o un blocco appunti IPython per esplorare i dati e tecnico funzionalità da modello. È quindi viene illustrato come creare e distribuire un modello con Azure apprendimento.


## <a name="dataset"></a>Il set di dati di Roma Taxi viaggi

I dati di andata e ritorno Taxi Roma è costituito da circa 20GB di file CSV compressi (~ 48GB non compressi), viaggi singoli 173 milioni e le tariffe registrazioni pagato per ogni viaggio. Ogni record di andata e ritorno include posizioni ritiro e deposito e ora, numero di licenze hacker resi anonimi (driver) e il numero di medallion (id univoco del taxi). I dati copre viaggi nell'anno 2013 e viene forniti il set di dati di due seguenti per ogni mese:

1. Il file **trip_data.csv** contiene i dettagli di viaggio, ad esempio il numero di persone, ritiro e punti dropoff, la durata del viaggio e durata del viaggio. Di seguito sono riportati alcuni record di esempio:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Il file **trip_fare.csv** contiene i dettagli degli tariffa pagata per ogni viaggio, ad esempio tipo di pagamento, Importo tariffa, supplemento e le imposte, suggerimenti e pedaggio e l'importo totale pagato. Di seguito sono riportati alcuni record di esempio:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La **chiave univoca** utilizzata per partecipare di andata e ritorno\_dati e andata e ritorno\_tariffa è costituito da tre campi seguenti:

- medallion,
- modificare\_licenza e
- Applica\_datetime.

## <a name="mltasks"></a>Tre tipi di attività di stima di indirizzi

Abbiamo formulare tre problemi di previsione in base alla *suggerimento\_importo* per illustrare i tre tipi di attività di modellazione:

1. **Classificazione binaria**: prevedere o meno un suggerimento è stato pagato per un viaggio, ad esempio un *suggerimento\_importo* che è maggiore rispetto a 0 è illustrato un esempio positivo, mentre un *suggerimento\_importo* di $0 è illustrato un esempio negativo.

2. **Classificazione multiclass**: prevedere l'intervallo di suggerimento pagato per il viaggio. Si divide il *suggerimento\_importo* in cinque bin o classi:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. **Attività di regressione**: per stimare la quantità di suggerimento pagato per un viaggio.  


## <a name="setup"></a>Configurare l'ambiente di scienze dati Azure per analitica avanzate

Per configurare l'ambiente di scienze di dati di Azure, seguire questa procedura.

**Creare il proprio account di archiviazione blob Azure**

- Durante il provisioning proprio archiviazione blob Azure, scegliere un percorso geografico per l'archiviazione blob Azure in o più vicino possibile a **Sud centrale USA**, ossia in cui sono archiviati dati Taxi Roma. I dati verranno copiati utilizzando AzCopy dal contenitore di spazio di archiviazione blob pubblica in un contenitore nell'account di archiviazione. Lo spazio di archiviazione blob Azure è vicino sud Central US, più veloce verrà completata l'attività (passaggio 4).
- Per creare il proprio account Azure dello spazio di archiviazione, seguire i passaggi descritti negli [account di archiviazione su Azure](../storage/storage-create-storage-account.md). Assicurarsi di prendere note sui valori seguenti lo spazio di archiviazione le credenziali dell'account, mentre saranno necessari più avanti in questa procedura dettagliata.

  - **Nome dell'Account di archiviazione**
  - **Chiave Account lo spazio di archiviazione**
  - **Nome del contenitore** (che si desidera i dati archiviati in archiviazione blob Azure)

**Preparare l'istanza DW SQL Azure.**
Seguire la documentazione [Crea un Data Warehouse SQL](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) per effettuare il provisioning di un'istanza di SQL Data Warehouse. Assicurarsi che il formato alle seguenti credenziali SQL Data Warehouse che verranno utilizzate nei passaggi successivi.

  - **Nome del server**: <server Name>. database.windows.net
  - **Nome SQLDW (Database)**
  - **Nome utente**
  - **Password**

**Installare Visual Studio 2015 e SQL Server Data Tools.** Per ulteriori informazioni, vedere [installare Visual Studio 2015 e/o SSDT (SQL Server Data Tools) per SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Connettere il DW SQL Azure con Visual Studio.** Per ulteriori informazioni, vedere i passaggi 1 e 2 in [connessione nell'archivio di dati di SQL Azure con Visual Studio](../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

>[AZURE.NOTE] Eseguire la query SQL seguente per il database che è stato creato in SQL Data Warehouse (invece di query specificata nel passaggio 3 dell'argomento Connetti) per **creare una chiave master**.

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Creare un'area di lavoro di apprendimento Azure in abbonamento Azure.** Per ulteriori informazioni, vedere [creare un'area di lavoro di apprendimento Azure](machine-learning-create-workspace.md).

## <a name="getdata"></a>Caricare i dati in SQL Data Warehouse

Aprire una console di comando di Windows PowerShell. Eseguire il seguente PowerShell file che si condividono con l'utente in Github in una directory locale che specificano con il parametro script di comandi per scaricare l'esempio SQL *- DestDir*. È possibile modificare il valore del parametro *-DestDir* in qualsiasi directory locale. Se non esiste *- DestDir* , verrà creato dallo script di PowerShell.

>[AZURE.NOTE] Quando si esegue il seguente script di PowerShell se la directory *DestDir* richiede privilegi di amministratore per creare o per la scrittura, potrebbe essere necessario **Esegui come amministratore** .

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Dopo la corretta esecuzione, la cartella di lavoro corrente viene modificato in *- DestDir*. Dovrebbe essere possibile vedere schermata come il seguente:

![][19]

Nei *-DestDir*, eseguire il seguente script di PowerShell in modalità di amministratore:

    ./SQLDW_Data_Import.ps1

Quando viene eseguito lo script di PowerShell per la prima volta, verrà chiesto di immettere le informazioni provenienti dal DW di SQL Azure e dell'account di archiviazione blob Azure. Dopo avere completato questo script di PowerShell esecuzione per la prima volta, le credenziali di input verrà sono stati scritti in un file di configurazione SQLDW.conf nella cartella di lavoro presenta. Esegui futuri di questo file di script di PowerShell ha la possibilità di leggere che tutte necessarie parametri da questo file di configurazione. Se è necessario modificare alcuni parametri, è possibile scegliere per i parametri sullo schermo al prompt dei comandi di input eliminando il file di configurazione e immettere i valori dei parametri come richiesto oppure per modificare i valori dei parametri modificando il file SQLDW.conf nella directory *- DestDir* .

>[AZURE.NOTE] Per impedire un conflitto di nome schema con quelli già presenti nel DW di SQL Azure, durante la lettura di parametri direttamente dal file SQLDW.conf, un numero casuale a 3 cifre viene aggiunto al nome dello schema dal file SQLDW.conf come il nome dello schema predefinito per ogni esecuzione. Lo script di PowerShell può richiedere un nome di schema: è possibile specificare il nome discrezione utente.

Il file di **script di PowerShell** completa le attività seguenti:

- **Scarica e installa AzCopy**, se AzCopy non è già installato

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
            Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }

- **Copia i dati al proprio account di archiviazione blob privato** del pubblico BLOB con AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"


- **Il caricamento dei dati utilizzando Polybase (eseguendo LoadDataToSQLDW.sql) per il DW di SQL Azure** dal proprio account di archiviazione blob private con i comandi seguenti.

    - Creare uno schema

            EXEC (''CREATE SCHEMA {schemaname};'');

    - Creare una credenziale database nell'ambito

            CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
            WITH IDENTITY = ''asbkey'' ,
            Secret = ''{StorageAccountKey}''

    - Creare un'origine dati esterna per un blob Azure dello spazio di archiviazione

            CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
            WITH
            (
                TYPE = HADOOP,
                LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
                CREDENTIAL = {KeyAlias}
            )
            ;

            CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
            WITH
            (
                TYPE = HADOOP,
                LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
                CREDENTIAL = {KeyAlias}
            )
            ;

    - Creare un formato di file esterno per un file csv. Dati non compressi e i campi sono separati con il carattere barra verticale.

            CREATE EXTERNAL FILE FORMAT {csv_file_format}
            WITH
            (   
                FORMAT_TYPE = DELIMITEDTEXT,
                FORMAT_OPTIONS  
                (
                    FIELD_TERMINATOR ='','',
                    USE_TYPE_DEFAULT = TRUE
                )
            )
            ;

    - Creare tariffa esterno e le tabelle di andata e ritorno Roma taxi set di dati in archiviazione blob Azure.

            CREATE EXTERNAL TABLE {external_nyctaxi_fare}
            (
                medallion varchar(50) not null,
                hack_license varchar(50) not null,
                vendor_id char(3),
                pickup_datetime datetime not null,
                payment_type char(3),
                fare_amount float,
                surcharge float,
                mta_tax float,
                tip_amount float,
                tolls_amount float,
                total_amount float
            )
            with (
                LOCATION    = ''/nyctaxifare/'',
                DATA_SOURCE = {nyctaxi_fare_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12     
            )  


            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                medallion varchar(50) not null,
                hack_license varchar(50)  not null,
                vendor_id char(3),
                rate_code char(3),
                store_and_fwd_flag char(3),
                pickup_datetime datetime  not null,
                dropoff_datetime datetime,
                passenger_count int,
                trip_time_in_secs bigint,
                trip_distance float,
                pickup_longitude varchar(30),
                pickup_latitude varchar(30),
                dropoff_longitude varchar(30),
                dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12         
            )

    - Caricare i dati di tabelle esterne in archiviazione blob Azure SQL Data Warehouse

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Creare una tabella di dati di esempio (NYCTaxi_Sample) e inserire dati dalla selezione delle query SQL alle tabelle di andata e ritorno e tariffa. (Alcuni passaggi di questa procedura dettagliata deve utilizzare questa tabella di esempio).

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

L'area geografica degli account di archiviazione influenza i tempi di caricamento.

>[AZURE.NOTE] A seconda della posizione geografica del proprio account di archiviazione blob privato, il processo di copia dei dati da un blob pubblico al proprio account di archiviazione privato può richiedere circa 15 minuti, o anche più, e il processo di caricamento dei dati dal proprio account di archiviazione per le DW di SQL Azure potrebbe richiedere 20 minuti o più.  

È necessario decidere quali eseguire se si dispone di file di origine e destinazione duplicati.

>[AZURE.NOTE] Se il file CSV file da copiare da archiviazione blob pubblico al proprio account di archiviazione blob privato già esistenti nel proprio account di archiviazione blob privato, AzCopy verrà chiesto se si desidera sovrascriverli. Se si desidera sovrascriverli, **n** , quando viene richiesto di input. Se si vuole sovrascrivere **tutte** le loro è input **al** quando richiesto. È inoltre possibile input **y** per sovrascrivere i file con estensione csv singolarmente.

![Tracciare 21 #][21]

È possibile utilizzare i propri dati. Se i dati nel computer locale nell'applicazione reali, è possibile usare ancora AzCopy per caricare i dati in locale allo spazio di archiviazione blob Azure privato. È necessario modificare la posizione di **origine** , `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, nel comando AzCopy del file di script di PowerShell nella directory locale contenente i dati.


>[AZURE.TIP] Se i dati sono già in archiviazione blob Azure privato reali nell'applicazione in uso, è possibile ignorare il passaggio AzCopy dello script di PowerShell e caricare i dati direttamente DW SQL Azure. Sarà necessario ulteriori modifiche dello script per personalizzare il formato dei dati.


Questo script di Powershell anche collega le informazioni DW SQL Azure i file di dati di esempio esplorazione SQLDW_Explorations.sql, SQLDW_Explorations.ipynb e SQLDW_Explorations_Scripts.py in modo che questi tre file sono pronti per essere provato istantaneamente termine lo script di PowerShell.

Dopo il completamento, si vedrà schermata come il seguente:

![][20]

## <a name="dbexplore"></a>Esplorazione dei dati e Progettazione funzionalità warehouse dati di SQL Azure

In questa sezione è eseguire esplorazione dei dati e generazione caratteristica eseguendo query SQL Azure SQL DW direttamente utilizzando **Visual Studio Data Tools**. Tutte le query SQL utilizzate in questa sezione sono disponibili dello script di esempio denominato *SQLDW_Explorations.sql*. Il file è stato scaricato già nella directory locale mediante lo script di PowerShell. È inoltre possibile recuperare da [Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Ma il file in Github tali informazioni non sono DW SQL Azure collegate.

Connettere il DW di SQL Azure con Visual Studio con il nome di accesso DW SQL e la password e aprire **Esplora oggetti SQL** per confermare che il database e le tabelle sono state importate. Recuperare file *SQLDW_Explorations.sql* .

>[AZURE.NOTE] Per aprire un editor di query in parallelo dati Warehouse (PDW), utilizzare il comando **Nuova Query** mentre è selezionato il PDW in **Esplora oggetti SQL**. Editor di query SQL standard non è supportata dagli PDW.

Ecco il tipo di dati esplorazione e funzionalità generazione operazioni in questa sezione:

- Esplorare le distribuzioni di dati di alcuni campi diversi Windows ora.
- Provare a utilizzare qualità dei dati dei campi latitudine e longitudine.
- Generare in base a etichette di classificazione binario e multiclass il **suggerimento\_importo**.
- Generare caratteristiche e calcolo/Confronta distanza andata e ritorno.
- Unire due tabelle ed estrarre un campione casuale che verrà utilizzato per la creazione di modelli.

### <a name="data-import-verification"></a>Importare i dati di verifica

Queste query offrono una rapida verifica il numero di righe e colonne nelle tabelle popolate in precedenza mediante blocco parallelo del Polybase importano,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Output:** È necessario ottenere 173,179,759 righe e 14 colonne.

### <a name="exploration-trip-distribution-by-medallion"></a>Esplorazione: Distribuzione di andata e ritorno da medallion

In questo esempio di query identifica medallions (taxi numeri) completati più di 100 trip all'interno di un periodo di tempo specificato. La query da trarre vantaggio da access tabella partizionata, poiché è subordinato dallo schema di partizione di **ritiro\_datetime**. Query il set completo di dati, rende utilizzare della tabella partizionata e/o indicizzare analisi.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Output:** La query restituisca una tabella con righe specificando 13,369 medallions (taxi) e il numero di andata e ritorno effettuata da essi 2013. Ultima colonna contiene il conteggio del numero di viaggi completata.

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Esplorazione: Distribuzione di andata e ritorno medallion e hack_license

In questo esempio identifica medallions (taxi numeri) e hack_license numeri (driver) completati più di 100 trip all'interno di un periodo di tempo specificato.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Output:** La query restituisca una tabella con 13,369 righe specificando il 13,369 Auto/driver ID che è stata completata più tale trip 100 in 2013. Ultima colonna contiene il conteggio del numero di viaggi completata.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Valutazione della qualità dei dati: verificare i record con longitudine non corretta e/o latitudine

In questo esempio esamina se i campi longitudine e/o latitudine contenere un valore non valido (gradi in radianti devono essere compreso tra -90 e 90) o (0; 0) le coordinate.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Output:** La query restituisce 837,467 viaggi contenenti campi longitudine e/o latitudine non validi.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Esplorazione: Inclinato e distribuzione di viaggi non inclinato

In questo esempio viene rilevato il numero di trip che sono state inclinato e il numero che non sono state inclinato in un periodo di tempo specificato (o nel set di dati completo se per l'anno completo come configurare la qui). Questa distribuzione riflette la distribuzione di etichetta binario in un secondo momento da utilizzare per una modellazione di classificazione binario.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Output:** La query deve restituire la frequenza di suggerimento seguenti per l'anno 2013: 90,447,622 inclinato e inclinato non 82,264,709.

### <a name="exploration-tip-classrange-distribution"></a>Esplorazione: Distribuzione di classe/intervallo suggerimento

In questo esempio calcola la distribuzione di intervalli di suggerimento in un determinato periodo di tempo (o nel set di dati completo se per l'anno completo). Verrà visualizzata la distribuzione delle classi etichetta che verrà utilizzato in un secondo momento per una modellazione di classificazione multiclass.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Output:**

|tip_class  | tip_freq |
| --------- | -------|
|1  | 82230915 |
|2  | 6198803 |
|3  | 1932223 |
|0  | 82264625 |
|4  | 85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Esplorazione: Calcolare e confrontare distanza andata e ritorno

In questo esempio converte longitudine ritiro e deposito e latitudine alla area geografica SQL punti, calcola la distanza di viaggio utilizzando SQL geography punti differenza e restituisce un campione dei risultati per il confronto. Nell'esempio limita i risultati in coordinate valide solo tramite la query di valutazione della qualità dati descritti in precedenza.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
        DECLARE @distance decimal(28, 10)
        -- Convert to radians
        SET @Lat1 = @Lat1 / 57.2958
        SET @Long1 = @Long1 / 57.2958
        SET @Lat2 = @Lat2 / 57.2958
        SET @Long2 = @Long2 / 57.2958
        -- Calculate distance
        SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
        --Convert to miles
        IF @distance <> 0
        BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
        END
        RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>Progettazione di funzionalità mediante le funzioni SQL

Funzioni di SQL in alcuni casi possono essere un'opzione efficiente di progettazione di funzionalità. In questa procedura dettagliata è stata definita una funzione SQL per calcolare la distanza tra le posizioni ritiro e dropoff diretta. È possibile eseguire il seguente script SQL in **Visual Studio Data Tools**.

Ecco lo script SQL che definisce la funzione distanza.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
        DECLARE @distance decimal(28, 10)
        -- Convert to radians
        SET @Lat1 = @Lat1 / 57.2958
        SET @Long1 = @Long1 / 57.2958
        SET @Lat2 = @Lat2 / 57.2958
        SET @Long2 = @Long2 / 57.2958
        -- Calculate distance
        SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
        --Convert to miles
        IF @distance <> 0
        BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
        END
        RETURN @distance
    END
    GO

Ecco un esempio di chiamare questa funzione per generare caratteristiche nella query SQL:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Output:** La query generata una tabella (con 2,803,538 righe) con ritiro e la termini di dropoff di latitudine e longitudine e la distanza diretta corrispondente in miglia. Ecco i risultati per prima di tutto 3 righe:

||pickup_latitude | pickup_longitude    | dropoff_latitude |dropoff_longitude | DirectDistance |
|---| --------- | -------|-------| --------- | -------|
|1  | 40.731804 | -74.001083 | 40.736622 | -73.988953 | .7169601222 |
|2  | 40.715794 | -74,010635 | 40.725338 | -74.00399 | .7448343721 |
|3  | 40.761456 | -73.999886 | 40.766544 | -73.988228 | 0.7037227967 |



### <a name="prepare-data-for-model-building"></a>Preparare i dati per la compilazione del modello

Le operazioni seguenti query join la **nyctaxi\_viaggio** e **nyctaxi\_tariffa** tabelle, genera una classificazione binario etichetta **inclinato**, un'etichetta di classificazione multi-classe **suggerimento\_classe**, dal set di dati uniti completo estrae un campione. Il campione è possibile recuperare un sottoinsieme di trip in base al tempo prelievo.  Questa query possibile copiata quindi incollata direttamente nella finestra di [Azure Machine Learning Studio](https://studio.azureml.net) [Importa dati] [ import-data] modulo per l'acquisizione di dati diretto dall'istanza di database SQL Azure. La query esclude i record con non corretti (0; 0) le coordinate.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Quando si è pronti passare alla apprendimento Azure, è possibile:  

1. Salvare la query SQL finale per estrarre e i dati di esempio e copia e Incolla la query direttamente in [Importa dati] [ import-data] modulo di Azure apprendimento, o
2. Mantenere i dati campione e ingegneria si prevede di utilizzare per la creazione di una nuova tabella di SQL DW modello e utilizzare la nuova tabella nella finestra [Importa dati] [ import-data] modulo di Azure apprendimento. Lo script di PowerShell nel passaggio precedente è eseguita questa operazione. È possibile leggere direttamente da questa tabella nel modulo Importa dati.


## <a name="ipnb"></a>Esplorazione dei dati e progettazione caratteristica nel blocco appunti IPython

In questa sezione, si eseguirà esplorazione dei dati e la generazione di funzionalità mediante entrambi Python e query SQL su DW SQL creata in precedenza. Un blocco appunti IPython di esempio denominato **SQLDW_Explorations.ipynb** e un file di script Python **SQLDW_Explorations_Scripts.py** sono stati scaricati nella directory locale. Sono inoltre disponibili in [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Questi due file sono identici in script Python. Il file di script Python è disponibile nel caso in cui non è un server IPython blocco appunti. Questi due file Python esempio sono progettati in **Python 2.7**.

Informazioni necessarie DW SQL Azure nel campione blocco appunti IPython e il file di script Python scaricata nel computer locale sono state collegate tramite lo script di PowerShell in precedenza. Sono eseguibile senza alcuna modifica.

Se già stata configurata un'area di lavoro AzureML, è possibile caricare il blocco appunti IPython di esempio per il servizio di blocco appunti IPython AzureML direttamente e iniziare a eseguirlo. Ecco i passaggi da caricare in blocco appunti IPython AzureML servizio:

1. Accedere all'area di lavoro AzureML, fare clic su "Studio" nella parte superiore e fare clic su "Blocchi appunti" sul lato sinistro della pagina web.

    ![Tracciare #22][22]

2. Fare clic su "Nuovo" nell'angolo inferiore sinistro della pagina web e selezionare "Python 2". Specificare un nome per il blocco appunti e fare clic sul segno di spunta per creare il nuovo blocco appunti IPython vuoto.

    ![Tracciare #23][23]

3. Fare clic sul simbolo di "Jupyter" nell'angolo superiore sinistro del nuovo blocco appunti IPython.

    ![Tracciare #24][24]

4. Trascinare e rilasciare il blocco appunti IPython campione alla pagina della **struttura** del servizio di blocco appunti IPython AzureML e fare clic su **Carica**. Quindi, nell'esempio che ipython blocco appunti verrà caricato al servizio di blocco appunti IPython AzureML.

    ![Tracciare #25][25]

Per eseguire l'esempio Python o blocco appunti IPython file, Python seguenti sono necessari pacchetti di script. Se si usa il servizio di blocco appunti IPython AzureML, questi pacchetti sono stati preinstallati.

    - pandas
    - numpy
    - matplotlib
    - pyodbc
    - PyTables

La sequenza consigliata durante la creazione di soluzioni di analisi avanzate in AzureML con grandi quantità di dati è il seguente:

- Leggere un piccolo esempio dei dati in una cornice di dati in memoria.
- Eseguire alcune visualizzazioni e analisi utilizzando i dati campione.
- Sperimentare progettazione caratteristica utilizzando i dati campione.
- Esplorazione dei dati più grande, la modifica dei dati e funzionalità, utilizzare Python per eseguire query SQL direttamente su DW SQL.
- Decidere la dimensione del campione sia adatto per la compilazione del modello di apprendimento Azure.

Seguenti sono pochi esplorazione dei dati, la visualizzazione dei dati e funzionalità ingegneria esempi. Sono disponibili ulteriori analisi di dati nel blocco appunti IPython di esempio e il file di script di esempio Python.

### <a name="initialize-database-credentials"></a>Inizializzare le credenziali del database

Inizializzare le impostazioni di connessione di database in variabili seguenti:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Creare la connessione al database

Ecco la stringa di connessione che consente di creare la connessione al database.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Numero di righe e colonne nella tabella < nyctaxi_trip >

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Numero totale di righe = 173179759  
- Numero totale di colonne = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxifare"></a>Numero di righe e colonne nella tabella < nyctaxi_fare >

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Numero totale di righe = 173179759  
- Numero totale di colonne = 11

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>Lettura di un campione di dati di piccole dimensioni di Database SQL Data Warehouse

    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Tempo per leggere che la tabella di esempio è 14.096495 secondi.  
Numero di righe e colonne recuperati = (1000, 21).

### <a name="descriptive-statistics"></a>Statistica descrittiva

A questo punto si è pronti esplorare i dati campione. Iniziamo con esamina alcune Statistica descrittiva per il **viaggio\_distanza** (o tutti gli altri campi si sceglie di specificare).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Visualizzazione: Esempio di tracciato casella

Successivamente si osserva il tracciato casella per la distanza di andata e ritorno visualizzare il quantiles.

    df1.boxplot(column='trip_distance',return_type='dict')

![Tracciare #1][1]

### <a name="visualization-distribution-plot-example"></a>Visualizzazione: Esempio distribuzione del tracciato

Posti che visualizzare la distribuzione e un istogramma per la distanza campionati di andata e ritorno.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Tracciare #2][2]

### <a name="visualization-bar-and-line-plots"></a>Visualizzazione: Barre e a linee posti

In questo esempio abbiamo bin la distanza di viaggio in cinque classi e visualizzare i risultati binning.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

È possibile tracciare la distribuzione di bin indicati in una barra o linee tracciato con:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Tracciare #3][3]

e

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Tracciare #4][4]

### <a name="visualization-scatterplot-examples"></a>Visualizzazione: Esempi di Scatterplot

Questo webinar Mostra grafico a dispersione tra **viaggio\_ora\_in\_sec** e **viaggio\_distanza** per controllare se è presente qualsiasi correlazione

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Tracciare #6][6]

Allo stesso modo è possibile controllare la relazione tra **tasso\_codice** e **viaggio\_distanza**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Tracciare #8][8]


### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Esplorazione dei dati in dati campionati mediante query SQL nel blocco appunti IPython

In questa sezione vengono illustrati distribuzioni di dati utilizzando i dati campione che viene mantenuti nella nuova tabella creato in precedenza. Si noti che è possono eseguire analisi simili utilizzando le tabelle originale.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Esplorazione: Segnalazione del numero di righe e colonne nella tabella campionata

    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Esplorazione: Inclinato/non raggiunto distribuzione

    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Esplorazione: Distribuzione suggerimento per la classe

    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Esplorazione: Traccia la distribuzione di suggerimento per classe

    tip_class_dist['tip_freq'].plot(kind='bar')

![Tracciare 26 #][26]


#### <a name="exploration-daily-distribution-of-trips"></a>Esplorazione: Distribuzione giornaliera di viaggi

    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Esplorazione: Distribuzione di andata e ritorno per medallion

    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Esplorazione: Distribuzione di andata e ritorno in base alla licenza medallion e hacker

    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Esplorazione: Distribuzione di tempo di andata e ritorno

    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Esplorazione: Distribuzione di distanza di andata e ritorno

    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Esplorazione: Distribuzione dei tipi di pagamento

    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verificare il formato finale della tabella featurized

    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Creare modelli di apprendimento Azure

È ora sono pronti per riprendere la compilazione del modello e distribuzione di modelli di [Apprendimento Azure](https://studio.azureml.net). I dati sono pronti per essere utilizzato in uno dei problemi stima identificati in precedenza, vale a dire:

1. **Classificazione binaria**: prevedere o meno un suggerimento è stato pagato per un viaggio.

2. **Classificazione multiclass**: prevedere l'intervallo di suggerimento pagato, in base alle classi definite in precedenza.

3. **Attività di regressione**: per stimare la quantità di suggerimento pagato per un viaggio.  



Per iniziare l'esercizio modellazione, accedere all'area di lavoro di **Apprendimento Azure** . Se non è ancora stata creata l'area di lavoro di apprendimento, vedere [creare un'area di lavoro di Azure ML](machine-learning-create-workspace.md).

1. Per iniziare a utilizzare apprendimento Azure, vedere [Novità Azure Machine Learning Studio?](machine-learning-what-is-ml-studio.md)

2. Accedere al [computer Azure formazione Studio](https://studio.azureml.net).

3. La pagina iniziale di Studio offre un'ampia varietà di informazioni, video, esercitazioni, i collegamenti per il riferimento di moduli e altre risorse. Per ulteriori informazioni sull'apprendimento Azure, consultare la [Documentazione Azure Centro risorse](https://azure.microsoft.com/documentation/services/machine-learning/).

Una prova di formazione tipica prevede i passaggi seguenti:

1. Creare una prova **+ Nuovo** .
2. Recuperare i dati in Azure ML.
3. Pre-elaborazione, la trasformazione e modificare i dati in base alle esigenze.
4. Generare caratteristiche in base alle esigenze.
5. Dividere i dati in formazione, convalida/test set di dati (o set di dati distinto per ogni).
6. Selezionare uno o più algoritmi di apprendimento computer a seconda del problema di risorse per la risoluzione. Ad esempio, la classificazione binaria, classificazione multiclass e regressione.
7. Formare uno o più modelli utilizzando il set di dati di formazione.
8. Punteggio il set di convalida dei dati con i modelli di esperti.
9. Valutare i modelli per calcolare le metriche pertinenti per il problema di risorse.
10. Regolare con maggiore precisione i modelli e selezionare il modello migliore per la distribuzione.

In questa esercitazione è abbiamo già esaminati e progettato i dati in SQL Data Warehouse e deciso la dimensione del campione per acquisire in Azure ML. Ecco la procedura per creare uno o più dei modelli di stima:

1. Recuperare i dati in ML Azure utilizzando i [Dati di importazione] [ import-data] modulo, disponibile nella sezione **Input di dati e l'Output** . Per ulteriori informazioni, vedere [Importare dati] [ import-data] pagina riferimento modulo.

    ![Azure ML Importa dati][17]

2. Selezionare **Il Database di SQL Azure** come **origine dati** nel pannello **proprietà** .

3. Immettere il nome del database DNS nel campo **nome del server di Database** . Formato:`tcp:<your_virtual_machine_DNS_name>,1433`

4. Immettere il **nome del Database** nel campo corrispondente.

5. Immettere il *nome utente di SQL* in **nome account utente**e la *password* nella casella **password dell'account utente Server**.

6. Selezionare l'opzione **accetta qualsiasi certificato server** .

7. Nell'area di testo di modifica di **una query di Database** , incollare la query che estrae il necessario campi (inclusi gli eventuali campi calcolati, ad esempio le etichette) del database e verso il basso esempi i dati per la dimensione del campione desiderato.

Un esempio di una prova di classificazione binario si leggono dati direttamente dal database SQL Data Warehouse è nella figura seguente (ricordarsi di sostituire la tabella nomi nyctaxi_trip e nyctaxi_fare il nome dello schema e i nomi di tabella è utilizzata la procedura dettagliata). Esperimenti simili possono essere creati per la classificazione multiclass e problemi di regressione.

![Azure treno ML][10]

> [AZURE.IMPORTANT] Nei dati di modellazione estrazione e campionamento esempi di query incluse nelle sezioni precedenti, **tutte le etichette per gli esercizi tre modellazione sono incluse nella query**. Un passaggio importante (obbligatorio) in ognuna delle esercizi modellazione è per **escludere** le etichette non necessarie per i due problemi e qualsiasi altro **le perdite di destinazione**. Ad esempio, quando si usa classificazione binaria, utilizzare l' etichetta **inclinato** ed escludere i campi **suggerimento\_classe**, **suggerimento\_importo**, e **totale\_importo**. Questi ultimi sono perdite di destinazione poiché implicano il suggerimento pagato.
>
> Per escludere le colonne non necessarie o perdite di destinazione, è possibile utilizzare le [Colonne selezionate in set di dati] [ select-columns] modulo o [Modificare i metadati][edit-metadata]. Per ulteriori informazioni, vedere [Selezionare colonne nel set di dati] [ select-columns] e [Modificare i metadati] [ edit-metadata] fanno riferimento a pagine.

## <a name="mldeploy"></a>Distribuire modelli di apprendimento Azure

Quando il modello è pronto, è possibile distribuirlo facilmente come un servizio web direttamente da di prova. Per ulteriori informazioni sulla distribuzione dei servizi web ML Azure, vedere [distribuire un servizio web apprendimento Azure](machine-learning-publish-a-machine-learning-web-service.md).

Per distribuire un nuovo servizio web, è necessario:

1. Creare un punteggio prova.
2. Distribuire il servizio web.

Per creare un punteggio prova da una prova di formazione **completato** , fare clic su **Crea punteggio PROVARE** nella barra delle azioni inferiore.

![Punteggio Azure][18]

Azure apprendimento tenterà di creare un punteggio prova in base ai componenti di prova formazione. In particolare, è necessario:

1. Salvare il modello di formazione e rimuovere i moduli di formazione su modello.
2. Identificare una logica **porta di input** per rappresentare lo schema di dati di input previsti.
3. Identificare una logica **porta di output** per rappresentare lo schema di output servizio web previsto.

Dopo avere creato la prova punteggio, esaminarla e adattare in base alle esigenze. Una tipica rettifica consiste nel sostituire il set di dati input e/o una query con quello che esclude i campi di etichette, come questi non saranno disponibili quando si chiama il servizio. È anche consigliabile ridurre le dimensioni del set di dati input e/o query per alcuni record, sufficiente per indicare lo schema di input. Per la porta di output, viene in genere per escludere i campi di input tutti e includere solo le **Etichette delle totalizza** e **Totalizza probabilità** nell'output tramite le [Colonne selezionate in set di dati] [ select-columns] modulo.

Nella figura seguente viene fornito un esempio Punteggio prova. Quando si è pronti per distribuire, fare clic sul pulsante **Pubblica servizio WEB** nella barra delle azioni inferiore.

![Pubblicare ML Azure][11]


## <a name="summary"></a>Riepilogo
In breve cosa abbiamo fatto in questa esercitazione, è stato creato un ambiente di scienze dati Azure, lavorato un ampio set di dati pubblici, impiegando attraverso il processo di scienze dati del Team, dal acquisizione dei dati per modellare formazione e alla distribuzione di un servizio web apprendimento Azure.

### <a name="license-information"></a>Per informazioni sulla licenza

In questa procedura dettagliata di esempio e il relativo accompagnamento script e IPython notebook(s) vengono condivisi da Microsoft in licenza MIT. Selezionare il file License nella directory del codice di esempio in GitHub per altri dettagli.

## <a name="references"></a>Riferimenti

• [Andrés Monroy Roma Taxi trip pagina di Download](http://www.andresmh.com/nyctaxitrips/)  
• [Impedire Taxi di andata e ritorno dati di Roma da Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [Roma Taxi e Limousine Commissione ricerche e statistiche](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sqldw-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sqldw-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlscoring.png
[19]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_load_data.png
[21]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/machine-learning-data-science-process-sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
