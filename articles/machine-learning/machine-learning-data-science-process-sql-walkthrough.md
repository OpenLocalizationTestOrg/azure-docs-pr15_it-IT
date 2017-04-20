<properties
    pageTitle="Il processo di Science Team dei dati in azione: utilizzo di SQL Server | Microsoft Azure"
    description="Processo Analitica avanzate e tecnologia in azione"  
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
    ms.author="fashah;bradsev"/>


# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Il processo di Science Team dei dati in azione: utilizzo di SQL Server

In questa esercitazione, si procedura Creazione e la distribuzione di un modello di apprendimento utilizzando SQL Server e un set di dati disponibile pubblicamente - il set di dati [Roma Taxi viaggi](http://www.andresmh.com/nyctaxitrips/) . La procedura seguente un flusso di lavoro science dati standard: acquisire ed esplorare i dati, caratteristiche engineer per facilitare l'apprendimento, quindi creare e distribuire un modello.


## <a name="dataset"></a>Descrizione di set di dati di viaggi Taxi delle Roma

I dati delle Roma Taxi Trip sono di circa 20GB di file CSV compressi (~ 48GB non compressi), che comprende 173 milioni singoli viaggi e le tariffe pagato per ogni accesso. Ogni record in viaggio include il percorso di prelievo e da organizzare e ora, hack resi anonimi (driver) licenza numero e medallion (id univoco del taxi). I dati vengono illustrati tutti i viaggi nell'anno 2013 e vengono forniti nei seguenti due set di dati per ogni mese:

1. 'trip_data' CSV contiene informazioni dettagliate di viaggio, ad esempio il numero di viaggiatori, prelievo e punti dropoff, la durata del viaggio e durata del viaggio. Ecco alcuni record di esempio:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Trip_fare' ' CSV contiene i dettagli di tariffa pagato per ogni accesso, ad esempio il tipo di pagamento, quantità tariffa, supplemento e tasse, suggerimenti e i numeri a tariffa e la quantità totale di pagamento. Ecco alcuni record di esempio:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La chiave univoca per partecipare a trip\_dati e trip\_tariffa è composta dai campi: medallion le maggiori\_prelievo e titolo\_datetime.

## <a name="mltasks"></a>Esempi di attività di stima

Si verranno formulare tre problemi stima in base la *suggerimento\_quantità*, vale a dire:

1. Classificazione binaria: prevedere o meno un suggerimento è stato pagato per viaggio, vale a dire un *suggerimento\_quantità* che è maggiore di $0 è un esempio positivo, mentre un *suggerimento\_quantità* di $0 è un esempio negativo.

2. Classificazione multiclass: per prevedere l'intervallo di suggerimento pagato per viaggio. Dividendo le *suggerimento\_quantità* in cinque Cestini o classi:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. Attività di regressione: per stimare la quantità di suggerimento pagata per un viaggio.  


## <a name="setup"></a>Ambiente di science dati Up the Azure per analitica avanzate

Come si può notare dalla Guida alla [Pianificazione dell'ambiente](machine-learning-data-science-plan-your-environment.md) , sono disponibili diverse opzioni per l'utilizzo con il set di dati delle Roma Taxi viaggi in Azure:

- Lavorare con i dati BLOB Azure quindi modello apprendimento Azure
- Caricare i dati in un database di SQL Server, quindi modello apprendimento Azure

In questa esercitazione verrà dimostrato importazione di massa parallelo dei dati a SQL Server, esplorazione dei dati, caratteristica engineering e verso il basso campionamento utilizzando SQL Server Management Studio e su come utilizzando blocco appunti IPython. [Script di esempio](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) e [IPython ai blocchi appunti](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) condivisi in GitHub. Un blocco appunti IPython di esempio per i dati nei blob Azure è disponibile anche nello stesso percorso.

Per impostare l'ambiente Azure dati Science:

1. [Creare un account di archiviazione](../storage/storage-create-storage-account.md)

2. [Creare un'area di lavoro di apprendimento Azure](machine-learning-create-workspace.md)

3. [Eseguire il provisioning di una macchina virtuale Science di dati](machine-learning-data-science-setup-sql-server-virtual-machine.md), che fungerà da anche un Server SQL server un blocco appunti IPython.

    > [AZURE.NOTE] Gli script di esempio e IPython ai blocchi appunti verranno scaricate alla macchina virtuale dati Science durante il processo di installazione. Al termine dell'esecuzione dello script di post-installazione macchina virtuale, i campioni saranno nella raccolta documenti del VM:  
    > - Script di esempio:`C:\Users\<user_name>\Documents\Data Science Scripts`  
    > - Esempi di blocchi appunti IPython:`C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
    > dove `<user_name>` nome account di accesso di Windows del VM. Si farà riferimento alle cartelle esempio come **Script di esempio** ed **Esempi di blocchi appunti IPython**.


In base alle dimensioni set di dati, percorso origine dati e dell'ambiente di destinazione Azure selezionato, questo scenario è simile a [Scenario \#5: assegnare il set di dati di grandi dimensioni in un file locale, SQL Server in macchine Virtuali di Azure](../machine-learning-data-science-plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Ottenere i dati dall'origine pubblica

Per ottenere il set di dati [Roma Taxi viaggi](http://www.andresmh.com/nyctaxitrips/) dalla posizione pubblica, si può utilizzare uno dei metodi descritti in [Spostare dati da archiviazione Blob Azure](machine-learning-data-science-move-azure-blob.md) per copiare i dati per la nuova macchina virtuale.

Per copiare i dati utilizzando AzCopy:

1. Accedere alla macchina virtuale (VM)

2. Creare una nuova directory nel disco di dati della macchina virtuale (Nota: non utilizzare il disco temporaneo disponibile con la macchina virtuale come un disco dati).

3. In una finestra del prompt dei comandi eseguire la riga di comando Azcopy seguente, sostituendo < path_to_data_folder > con la cartella di dati creata in (2):

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

    Al termine dell'esecuzione di AzCopy, un totale di 24 compresso file CSV (12 per viaggio\_dati e 12 per viaggio\_tariffa) deve essere nella cartella dati.

4. Decomprimere i file scaricati. Prendere nota della cartella in cui si trovano i file non compressi. Questa cartella sarà possibile denominata il < percorso\_a\_dati\_file\>.

## <a name="dbload"></a>Blocco Importa dati nel Database di SQL Server

Migliorare le prestazioni di caricamento/trasferimento grandi quantità di dati a un database SQL e query successive utilizzando _partizionati tabelle e visualizzazioni_. In questa sezione verranno eseguiti le istruzioni descritte in [Parallelo blocco dati importazione utilizzando SQL tabelle delle partizioni](machine-learning-data-science-parallel-load-sql-partitioned-tables.md) per creare un nuovo database e caricare i dati in tabelle partizionate in parallelo.

1. Mentre si è connessi a di una macchina virtuale, avviare **SQL Server Management Studio**.

2. Connetti con l'autenticazione di Windows.

    ![SQL Server Management Studio connettersi][12]

3. Se non ancora aver modificato la modalità di autenticazione di SQL Server e creato un nuovo utente di accesso SQL, aprire il file di script denominato **modificare\_auth.sql** nella cartella di **Script di esempio** . Modificare il nome utente predefinito e la password. Fare clic su **! Eseguire** sulla barra degli strumenti per eseguire lo script.

    ![Esecuzione dello Script][13]

4. Verificare e/o modifica di SQL Server database e registro cartelle predefinite per garantire che verranno memorizzate in un disco dati nuovi database. L'immagine di SQL Server VM ottimizzata per i carichi di datawarehousing è preconfigurata con dischi dati e di registro. Se la macchina virtuale non è stato incluso un disco dati e aggiunto nuovi dischi rigidi virtuali durante il processo di installazione macchina virtuale, modificare le cartelle predefinite nel modo seguente:

    - Il nome di SQL Server nel riquadro sinistro pulsante destro del mouse e scegliere **proprietà**.

        ![Proprietà SQL Server][14]

    - Selezionare **Le impostazioni del Database** nell'elenco **Selezionare una pagina** a sinistra.

    - Verificare e/o modificare i **percorsi predefiniti del Database** per i percorsi di **Dischi dati** desiderato. Si tratta di cui si trovano i nuovi database se creati con le impostazioni di percorso predefinito.

        ![Valori predefiniti del Database SQL][15]  

5. Per creare un nuovo database e un set di filegroup per contenere le tabelle partizionate, aprire lo script di esempio **creare\_db\_default.sql**. Lo script verrà creato un nuovo database denominato **TaxiNYC** e 12 filegroup nel percorso dei dati predefinito. Ogni filegroup conterrà un mese di viaggio\_dati e trip\_presentare dati. Modificare il nome del database, se lo si desidera. Fare clic su **! Eseguire** per eseguire lo script.

6. Successivamente, creare due tabelle di partizione, uno per viaggio\_data e l'altro per viaggio\_tariffa. Aprire lo script di esempio **creare\_partizionata\_Table**, che verrà:

    - Creare una funzione di partizione per suddividere i dati in base al mese.
    - Creare uno schema di partizione per mappare i dati di ogni mese per un gruppo di file diverso.
    - Creare due tabelle partizionato sia mappate allo schema di partizione: **nyctaxi\_trip** conterrà il viaggio\_dati e **nyctaxi\_tariffa** conterrà il viaggio\_presentare dati.

    Fare clic **. Eseguire** per eseguire lo script e creare tabelle partizionate.

7. Nella cartella **Script di esempio** , sono disponibili due script di PowerShell di esempio fornito per illustrare consente di importare parallelo blocco di dati alle tabelle di SQL Server.

    - **bcp\_parallelo\_generic.ps1** è uno script generico ai dati di importazione in blocco in parallelo in una tabella. Modificare lo script per impostare le variabili di input e di destinazione, come indicato nelle righe di commento nello script.
    - **bcp\_parallelo\_nyctaxi.ps1** è una versione preconfigurata dello script generico e consente di caricare entrambe le tabelle per i dati delle Roma Taxi viaggi.  

8. Pulsante destro del mouse la **bcp\_parallelo\_nyctaxi.ps1** eseguire gli script nome e scegliere **modificare** per aprirlo in PowerShell. Esaminare le variabili preimpostate e modificare in base al nome del database selezionato, cartella dati di input, cartella di destinazione del registro e percorsi per i file di esempio formato **nyctaxi_trip.xml** e **nyctaxi\_fare.xml** (disponibile nella cartella **Script di esempio** ).

    ![Importazione di massa dei dati][16]

    È inoltre possibile selezionare la modalità di autenticazione, valore predefinito è l'autenticazione di Windows. Fare clic sulla freccia verde sulla barra degli strumenti per l'esecuzione. Lo script avvia 24 operazioni di importazione in blocco in parallelo, 12 per ogni tabella partizionata. Si può monitorare lo stato dell'importazione di dati, aprire la cartella di dati di SQL Server predefinita come set precedente.

9. Lo script di PowerShell segnala di inizio e di fine. Quando tutti in blocco le importazioni complete, viene indicato l'ora di fine. La cartella di destinazione registro per verificare che le importazioni di massa hanno avuto esito positivo, ad esempio, non gli errori segnalati nella cartella di destinazione del log di controllo.

10. Il database è ora pronto per l'esplorazione, engineering caratteristiche e altre operazioni in base alle esigenze. Poiché le tabelle sono partizionate in base al **prelievo\_datetime** campo, le query che includono **prelievo\_datetime** utilizzeranno le condizioni nella clausola **WHERE** della combinazione di partizione di.

11. In **SQL Server Management Studio**utilizzando lo script di esempio fornito **esempio\_queries.sql**. Per eseguire una delle query di esempio, evidenziare le righe della query, quindi fare clic su **! Eseguire** nella barra degli strumenti.

12. I dati delle Roma Taxi viaggi viene caricati in due tabelle diverse. Per migliorare le operazioni di join, è consigliabile per indicizzare le tabelle. Lo script di esempio **creare\_partizionata\_index.sql** consente di creare indici partizionati sulla chiave join composito **medallion le maggiori\_licenza e prelievo\_datetime**.

## <a name="dbexplore"></a>Caratteristica Engineering in SQL Server e l'esplorazione dei dati

In questa sezione vengono eseguiti la generazione di funzionalità e l'esplorazione dei dati mediante l'esecuzione di query SQL direttamente in **SQL Server Management Studio** utilizzando il database di SQL Server creato in precedenza. Uno script di esempio denominato **esempio\_queries.sql** è contenuta nella cartella **Script di esempio** . Modificare lo script per modificare il nome del database, se è diverso da quello predefinito: **TaxiNYC**.

In questa esercitazione verranno:

- Connettersi a **SQL Server Management Studio** utilizzando l'autenticazione di Windows o autenticazione di SQL Server e il nome di accesso SQL e la password.
- Esplora le distribuzioni di dati di alcuni campi nelle finestre di tempo diversi.
- Provare a utilizzare la qualità dei dati dei campi latitudine e longitudine.
- Generare etichette binario e multiclass classificazione in base la **suggerimento\_quantità**.
- Generare caratteristiche e le distanze trip compute/compare.
- Unire due tabelle ed estrarre un campione casuale che verrà utilizzato per la creazione di modelli.

Quando sei pronto a passare a apprendimento Azure, è possibile:  

1. Salvare la query SQL finale per estrarre e i dati di esempio e la query, copia e Incolla direttamente in un [Importa dati] [ import-data] modulo di Azure l'apprendimento, o
2. La presenza si intende utilizzare per il modello di creazione di una nuova tabella di database e la nuova tabella di [Dati di importazione] dei dati inclusi nel campione e a reengineering[ import-data] modulo di apprendimento Azure.

In questa sezione verranno salvando le query finale per estrarre e i dati di esempio. Il secondo metodo è illustrato nella sezione [esplorazione dei dati e funzionalità di progettazione in blocco appunti IPython](#ipnb) .

Per una rapida verifica del numero di righe e colonne nelle tabelle compilato in precedenza mediante l'importazione di massa parallelo,

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>: Esplorazione Distribuzione di viaggio in base al medallion

In questo esempio vengono identificati medallion (taxi numeri) con più di 100 viaggi all'interno di un determinato periodo di tempo. La query potrebbe risultare vantaggiosa accesso tabella partizionata poiché è condizionato dallo schema di partizione di **prelievo\_datetime**. L'esecuzione di query al set di dati completi consentirà anche utilizzare della tabella partizionata e/o indicizzare analisi.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Nell'esplorazione: Distribuzione di viaggio medallion e hack_license

    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Valutare la qualità dei dati: Verifica dei record con longitudine non corretta e/o latitudine

In questo esempio analizza se i campi longitudine e/o latitudine contenere un valore non valido (gradi in radianti devono essere compreso tra -90 e 90), o (0, 0) le coordinate.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Nell'esplorazione: Distribuzione Tipped e non inclinato viaggi

In questo esempio viene rilevato il numero di viaggi che sono stati inclinato e non inclinato in un momento specifico periodo (o il set di dati completi se per l'anno di completo). Questa distribuzione riflette la distribuzione di etichetta binario in seguito da utilizzare per la modellazione classificazione binari.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Esplorazione: Suggerimenti per la distribuzione di classe/intervallo

In questo esempio consente di calcolare la distribuzione degli intervalli di comandi in un determinato periodo di tempo (o il set di dati completi se per l'anno di completo). Questa è la distribuzione delle classi etichetta che verrà utilizzato per la modellazione classificazione multiclass più avanti.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

#### <a name="exploration-compute-and-compare-trip-distance"></a>Esplorazione: Calcolare e confrontare distanza di viaggio

Nell'esempio seguente la longitudine prelievo e da organizzare e latitudine alla geografia SQL points, calcola la distanza di viaggio utilizzando SQL geography punti differenza e restituisce un campione casuale dei risultati per il confronto. L'esempio che limita i risultati in corrispondenza delle coordinate valido solo utilizzando la query di valutazione di qualità dati descritto in precedenza.

    SELECT
    pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
    ,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
    ,trip_distance
    ,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
    FROM nyctaxi_trip
    tablesample(0.01 percent)
    WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### <a name="feature-engineering-in-sql-queries"></a>Caratteristica Engineering nelle query SQL

La query etichetta generazione e Geografia conversione nell'esplorazione possono essere utilizzate anche per la generazione di etichette/funzionalità rimuovendo il conteggio parte. Esempi SQL engineering funzionalità aggiuntive sono disponibili nella sezione [esplorazione dei dati e funzionalità di progettazione in blocco appunti IPython](#ipnb) . È più efficiente per eseguire la funzionalità di generazione query nel set di dati completi o un sottoinsieme di grandi dimensioni di utilizzando query SQL che vengono eseguiti direttamente nell'istanza di database di SQL Server. Le query possono essere eseguite in **SQL Server Management Studio**, IPython blocco appunti o qualsiasi strumento/ambiente di sviluppo che può accedere al database locale o in remoto.

#### <a name="preparing-data-for-model-building"></a>Preparazione dei dati per la compilazione del modello

Join di query seguenti la **nyctaxi\_trip** e **nyctaxi\_tariffa** tabelle, genera una classificazione binario etichetta **inclinato**, un'etichetta di classificazione multi-classe **suggerimento\_classe**, dal set di dati completi unite estrae un campione di % 1. Questa query possibile copiata quindi incollata direttamente in [Azure automatica Learning Studio](https://studio.azureml.net) [Importa dati] [ import-data] modulo per l'inserimento di dati direttamente dall'istanza di database di SQL Server in Azure. La query esclude i record con non corretto (0, 0) le coordinate.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 percent)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>Caratteristica Engineering nel blocco appunti IPython e l'esplorazione dei dati

In questa sezione vengono eseguiti esplorazione dei dati e la generazione di funzionalità mediante SQL e Python query sul database di SQL Server creata in precedenza. Viene fornito un blocco appunti IPython esempio denominato **machine-Learning-data-science-process-sql-story.ipynb** nella cartella **IPython esempi di blocchi appunti** . In questo blocco appunti è disponibile anche in [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

La sequenza consigliata quando si utilizzano dati di grandi dimensioni è il seguente:

- Lettura di un campione di piccole dimensioni dei dati in una cornice di dati in memoria.
- Eseguire alcuni effetti grafici e analisi di utilizzo dei dati inclusi nel campione.
- Sperimentare engineering funzionalità con i dati inclusi nel campione.
- Per più grande esplorazione dei dati, la modifica dei dati ed engineering funzionalità, utilizzare Python per emettere le query SQL direttamente sul database di SQL Server nella macchina virtuale Azure.
- Stabilire la dimensione del campione da utilizzare per la compilazione del modello di apprendimento Azure.

Quando si è pronti per procedere con l'apprendimento Azure, è possibile:  

1. Salvare la query SQL finale per estrarre e i dati di esempio e la query, copia e Incolla direttamente in un [Importa dati] [ import-data] modulo di apprendimento Azure. Questo metodo è illustrato nella sezione [Modelli di creazione in apprendimento Azure](#mlmodel) .    
2. Mantenere i dati inclusi nel campione e a reengineering si intende utilizzare per il modello di creazione di una nuova tabella di database, quindi utilizzare la nuova tabella di [Importazione dati] [ import-data] modulo.

Di seguito sono alcuni esplorazione dei dati, la visualizzazione dei dati e funzionalità di esempi di progettazione. Per ulteriori esempi, vedere il blocco appunti IPython SQL di esempio nella cartella **IPython esempi di blocchi appunti** .

#### <a name="initialize-database-credentials"></a>Inizializzare le credenziali del Database

Inizializzare le impostazioni di connessione di database alle variabili seguenti:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Creare una connessione al Database

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Numero di righe e colonne nella tabella nyctaxi_trip

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('nyctaxi_trip')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('nyctaxi_trip')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Numero totale di righe = 173179759  
- Numero totale di colonne = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Lettura di un campione di dati di piccole dimensioni dal Database di SQL Server

    t0 = time.time()

    query = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (0.05 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Tempo per leggere che la tabella di esempio è 6.492000 secondi  
Numero di righe e colonne recuperati = (84952, 21)

#### <a name="descriptive-statistics"></a>Statistiche descrittive

È ora pronti per esplorare i dati inclusi nel campione. Si inizia con osservando Statistica descrittiva per il **trip\_distanza** (o qualsiasi altra) campo/i:

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Per la visualizzazione: Esempio di tracciato casella

Successivamente viene esaminata tracciato casella per la distanza di viaggio visualizzare il quantiles

    df1.boxplot(column='trip_distance',return_type='dict')

![Vengono tracciati in #1][1]

#### <a name="visualization-distribution-plot-example"></a>Per la visualizzazione: Esempio del tracciato di distribuzione

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Vengono tracciati in #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Per la visualizzazione: Barra e osservazione di riga

In questo esempio viene bin la distanza di viaggio in cinque collocazioni e visualizzare i risultati binning.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

È possibile tracciare la distribuzione di collocazione precedente in una barra o nella riga tracciato come indicato di seguito

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Vengono tracciati in #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Vengono tracciati in #4][4]

#### <a name="visualization-scatterplot-example"></a>Per la visualizzazione: Esempio Scatterplot

Viene indicato grafico a dispersione tra **trip\_tempo\_in\_sec** e **trip\_distanza** per se è disponibile alcun correlazione

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Vengono tracciati in #6][6]

Allo stesso modo è possibile controllare la relazione tra **frequenza\_codice** e **trip\_distanza**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Vengono tracciati in #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Un campionamento secondario di dati in SQL

Quando il modello di dati la preparazione per la creazione in [Azure automatica Learning Studio](https://studio.azureml.net), è possibile decidere nella **query SQL da utilizzare direttamente nel modulo Importa dati** o mantenere i dati a Reengineering e campione in una nuova tabella, è possibile utilizzare in [Importa dati] [ import-data] modulo con un semplice * *Selezionare* FROM < il\_nuovo\_tabella\_nome >**.

In questa sezione si creerà una nuova tabella in modo che dati campione e a reengineering. Viene fornito un esempio di una query SQL direttamente per la compilazione del modello nella sezione [esplorazione dei dati e funzionalità di progettazione in SQL Server](#dbexplore) .

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Un esempio di creazione tabella e la popola con 1% delle tabelle unite. DROP Table prima se esistente.

In questa sezione si uniscono le tabelle **nyctaxi\_trip** e **nyctaxi\_tariffa**, estrarre un campione 1% e mantenere i dati inclusi nel campione in un nuovo nome tabella **nyctaxi\_uno\_%**:

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
        INTO nyctaxi_one_percent
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (1 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
        AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Esplorazione dei dati tramite query SQL in blocco appunti IPython

In questa sezione vengono illustrati le distribuzioni di dati utilizzando i dati di % 1 viene eseguito permanente nella nuova tabella che è stato creato in precedenza. Si noti che analisi simile possono essere eseguite utilizzando le tabelle originale, se lo si desidera utilizzano **TABLESAMPLE** per limitare l'esplorazione di esempio o limitare i risultati per un periodo di tempo specificato utilizzando il **prelievo\_datetime** partizioni, come illustrato nella sezione [esplorazione dei dati e funzionalità di progettazione in SQL Server](#dbexplore) .

#### <a name="exploration-daily-distribution-of-trips"></a>: Esplorazione Distribuzione giornaliera di viaggi

    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Nell'esplorazione: Distribuzione di viaggio per medallion

    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Generazione di funzionalità tramite query SQL in blocco appunti IPython

In questa sezione, verrà generato nuove etichette e caratteristiche direttamente tramite query SQL, che viene applicato la tabella di esempio % 1 creato nella sezione precedente.

#### <a name="label-generation-generate-class-labels"></a>Generazione di etichette: Generare le etichette di classe

Nell'esempio seguente viene generato due set di etichette da utilizzare per la modellazione:

1. Binario classe etichette **inclinato** (prevedere se viene assegnato un suggerimento)
2. Etichette multiclass **suggerimento\_classe** (prevedere la collocazione di suggerimento o l'intervallo)

        nyctaxi_one_percent_add_col = '''
            ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
        '''

        cursor.execute(nyctaxi_one_percent_add_col)
        cursor.commit()

        nyctaxi_one_percent_update_col = '''
            UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''

        cursor.execute(nyctaxi_one_percent_update_col)
        cursor.commit()

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Caratteristica Progettazione tecnica: Count caratteristiche per colonne di categoria

In questo esempio consente di trasformare un campo per categoria in un campo numerico attraverso la sostituzione di ogni categoria con il conteggio dei casi di data.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B AS
        (
            SELECT medallion, hack_license,
                SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
                SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
            FROM nyctaxi_one_percent
            GROUP BY medallion, hack_license
        )

        UPDATE nyctaxi_one_percent
        SET nyctaxi_one_percent.cmt_count = B.cmt_count,
            nyctaxi_one_percent.vts_count = B.vts_count
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion AND A.hack_license = B.hack_license
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Caratteristica Progettazione tecnica: Bin funzionalità per le colonne numerici

In questo esempio consente di trasformare un campo numerico continuo in intervalli categoria predefinita, transform, ovvero i campi numerici in un campo per categoria.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
        (
            SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
            NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
        )

        UPDATE nyctaxi_one_percent
        SET trip_time_bin = B.BinNumber
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion
        AND A.hack_license = B.hack_license
        AND A.pickup_datetime = B.pickup_datetime
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Caratteristica Progettazione tecnica: Estrarre funzionalità posizione da latitudine e longitudine decimale

In questo esempio si articola rappresentazione decimale di un campo latitudine e/o longitudine in più campi area di granularità diverse, ad esempio, paese, city, città, blocco e così via. Si noti che i nuovi campi geo non mappati ai percorsi effettivi. Per informazioni sui percorsi geocode mapping, vedere [Servizi REST di Bing Maps](https://msdn.microsoft.com/library/ff701710.aspx).

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent
        ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
            l5 varchar(3), l6 varchar(3), l7 varchar(3)
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET l1=round(pickup_longitude,0)
            , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
            , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
            , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
            , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
            , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
            , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verificare il modulo finale della tabella featurized

    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Ora siamo pronti continuare la compilazione del modello e distribuzione di modelli di [Apprendimento Azure](https://studio.azureml.net). I dati siano pronti per uno dei problemi stima identificati in precedenza, vale a dire:

1. Classificazione binaria: per prevedere o meno un suggerimento è pagato un viaggio.

2. Classificazione multiclass: per prevedere l'intervallo di suggerimento pagato, in base alle classi definite in precedenza.

3. Attività di regressione: per stimare la quantità di suggerimento pagata per un viaggio.  


## <a name="mlmodel"></a>Creazione dei modelli in Azure apprendimento

Per iniziare la modellazione esercizio, accedere all'area di lavoro apprendimento Azure. Se non è ancora stato creato un computer apprendimento dell'area di lavoro, vedere [creare un'area di lavoro di apprendimento automatica Azure](machine-learning-create-workspace.md).

1. Per iniziare con Azure l'apprendimento, vedere [Novità Azure automatica Learning Studio?](machine-learning-what-is-ml-studio.md)

2. Accedere al [computer Azure apprendimento Studio](https://studio.azureml.net).

3. Studio Home page offre una notevole mole di informazioni, video, esercitazioni, collegamenti per il riferimento di moduli e altre risorse. Per ulteriori informazioni su Azure l'apprendimento, consultare il [Centro di Azure documentazione di apprendimento](https://azure.microsoft.com/documentation/services/machine-learning/).

Un esperimento formazione tipica comprende le operazioni seguenti:

1. Creare un esperimento **+ NEW** .
2. Ottenere i dati per l'apprendimento Azure.
3. Pre-elaborazione, trasformazione e modificare i dati in base alle esigenze.
4. Generare funzionalità in base alle esigenze.
5. Suddividere i dati in formazione, convalida/test set di dati (o set di dati separato per ogni).
6. Selezionare uno o più algoritmi di apprendimento macchina a seconda del problema di apprendimento per risolvere. Ad esempio, la classificazione binaria, classificazione multiclass e regressione.
7. Istruire uno o più modelli utilizzando il set di dati di formazione.
8. Punteggio il set di dati di convalida con i modelli adeguatamente formati.
9. Valutare i modelli per calcolare le metriche rilevanti per il problema di apprendimento.
10. Selezionare il modello migliore per distribuire correttamente ottimizzare i modelli.

In questa esercitazione aver già esaminate e progettati i dati di SQL Server e la dimensione del campione deciso di caricamento di apprendimento Azure. Per creare una o più dei modelli stima che si è deciso:

1. Ottenere i dati per Azure apprendimento utilizzando [Importa dati] [ import-data] module, disponibile nella sezione **dati di Input e Output** . Per ulteriori informazioni, vedere [Importare dati] [ import-data] pagina riferimento al modulo.

    ![Apprendimento Azure Importa dati][17]

2. Selezionare **Il Database di SQL Azure** come **origine dati** nel riquadro **proprietà** .

3. Immettere il nome DNS del database nel campo **nome del server Database** . Formato:`tcp:<your_virtual_machine_DNS_name>,1433`

4. Immettere il **nome del Database** nel campo corrispondente.

5. Immettere il **nome utente di SQL** nel **aqccount nome del Server utente e la password di **Server utente account password * *.

6. Controllare l'opzione **accetta un certificato del server** .

7. Nell'area di testo modifica **query di Database** , incollare la query che estrae i necessari campi (inclusi tutti i campi calcolati, ad esempio le etichette) del database e verso il basso campionamento dei dati per la dimensione del campione desiderato.

Un esempio di un esperimento classificazione binario lettura di dati direttamente dal database di SQL Server è nella figura seguente. È possibile costruire sperimentazione simile per la classificazione multiclass e problemi di regressione.

![Macchina Azure Learning treno][10]

> [AZURE.IMPORTANT] Nei dati di modellazione l'estrazione e campionamento delle esempi di query forniti nelle sezioni precedenti, **tutte le etichette di esercitazioni tre modellazione sono inclusi nella query**. Un aspetto importante (obbligatorio) in ognuna delle esercitazioni modellazione deve **escludere** le etichette non necessari per i due problemi e le altre **perdite di destinazione**. Per ad esempio, se si utilizza la classificazione binary, utilizzare l' etichetta **inclinato** ed escludere i campi **suggerimento\_classe**, **suggerimento\_quantità**, e **totale\_quantità**. Quest'ultima non è perdite di destinazione poiché implicano il suggerimento pagate.
>
> Per escludere le colonne non necessarie e/o perdite di destinazione, è possibile utilizzare la [Selezione di colonne nel set di dati] [ select-columns] modulo o la [Modifica dei metadati][edit-metadata]. Per ulteriori informazioni, vedere [Selezione di colonne nel set di dati] [ select-columns] e [Modificare metadati] [ edit-metadata] pagine di riferimento.

## <a name="mldeploy"></a>Distribuzione di modelli di apprendimento Azure

Quando il modello è pronto, è possibile distribuire facilmente lo come servizio web direttamente da esperimento. Per ulteriori informazioni sulla distribuzione di servizi web di apprendimento Azure, vedere [distribuzione di un servizio web di apprendimento Azure](machine-learning-publish-a-machine-learning-web-service.md).

Per distribuire un nuovo servizio web, è necessario:

1. Creare un esperimento punteggio.
2. Distribuire il servizio web.

Per creare un esperimento punteggio da un esperimento formazione **completato** , fare clic su **Crea punteggio SPERIMENTARE** barra inferiore delle azioni.

![Punteggio Azure][18]

Apprendimento Azure tenterà di creare un esperimento punteggio in base ai componenti dell'esperimento formazione. In particolare, verrà:

1. Salvare il modello di formazione e rimuovere i moduli di formazione del modello.
2. Identificare un **input porta** logica per rappresentare lo schema di dati di input previsto.
3. Identificare un logica **porta di output** per rappresentare lo schema di output del servizio web previsto.

Quando si crea il punteggio esperimento, verificare e adattare in base alle esigenze. Sostituire il set di dati di input e/o query con uno che consente di escludere i campi etichetta, come queste non saranno disponibili quando viene chiamato il servizio è un esempio tipico di regolazione. È inoltre consigliabile ridurre le dimensioni del set di dati di input e/o query di alcuni record, sufficiente per indicare lo schema di input. Per la porta di output, viene in genere per escludere i campi di tutti gli input e includere solo le **Etichette catalogato** e **Catalogato probabilità** nell'output utilizzando la [Selezione di colonne nel set di dati] [ select-columns] modulo.

Un esempio di punteggio esperimento è nella figura seguente. Quando si è pronti per la distribuzione, fare clic sul pulsante **Pubblica servizio WEB** nella barra delle azioni inferiore.

![Pubblicare l'apprendimento Azure][11]

In breve, in questa esercitazione dettagliata è stato creato un ambiente Azure dati science, ha collaborato con un dataset pubblico grande completamente dall'acquisizione dei dati al modello di formazione e la distribuzione di un servizio web di apprendimento Azure.

### <a name="license-information"></a>Informazioni sulla licenza

Procedura dettagliata in questo esempio, il relativo accompagnano script e IPython notebook(s) siano condivisi da Microsoft in base alla licenza MIT. Controllare il file License nella directory del codice di esempio in GitHub per ulteriori informazioni.

### <a name="references"></a>Riferimenti

• [Andrés Monroy Roma Taxi viaggi pagina di Download](http://www.andresmh.com/nyctaxitrips/)  
• [Impedire i dati di viaggio del Roma Taxi da Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [Roma Taxi e Limousine Commissione ricerca e delle statistiche](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sql-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sql-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sql-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sql-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sql-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sql-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
