<properties
   pageTitle="Utilizzo dati piattaforma Studio del Redgate per caricare dati nell'archivio di dati SQL | Microsoft Azure"
   description="Informazioni sull'utilizzo dati piattaforma Studio del Redgate per scenari di warehouse di dati."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/13/2016"
   ms.author="mausher;barbkess"/>


# <a name="load-data-with-redgate-data-platform-studio"></a>Caricare i dati con Redgate dati piattaforma Studio

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)
- [Dati Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

In questa esercitazione viene illustrato come utilizzare [Dati piattaforma Studio del Redgate](http://www.red-gate.com/products/azure-development/data-platform-studio/) (DPS) per spostare i dati da un Server SQL locale nell'archivio di dati di SQL Azure. Dati piattaforma Studio applica le correzioni e le ottimizzazioni, più appropriato in modo che è il modo più rapido per iniziare a utilizzare SQL Data Warehouse.

> [AZURE.NOTE] [Redgate](http://www.red-gate.com) è un partner Microsoft utilizza che offre diversi strumenti di SQL Server. Questa caratteristica di Studio piattaforma dati resa disponibile gratuitamente per l'utilizzo di commerciale e non commerciale.

## <a name="before-you-begin"></a>Prima di iniziare
### <a name="create-or-identify-resources"></a>Creare o identificare le risorse

Prima di iniziare questa esercitazione, è necessario che:

- **Database di SQL Server in locale**: I dati da importare nell'archivio di dati SQL devono provenire da un computer locale SQL Server (versione 2008 R2 o versione successiva). Studio piattaforma dati non è possibile importare i dati direttamente da un Database di SQL Azure o dai file di testo.
- **Account di archiviazione di Azure**: dati piattaforma Studio fasi i dati in archiviazione Blob Azure prima di caricare nell'archivio di dati SQL. Account di archiviazione necessario utilizzare il modello di distribuzione "Manager delle risorse" (impostazione predefinita) piuttosto che il modello di distribuzione "Classico". Se non si dispone di un account di archiviazione, informazioni su come creare un account di archiviazione. 
- **SQL Data Warehouse**: in questa esercitazione si sposta i dati da SQL Server locale SQL Data Warehouse, in modo che è necessario avere un data warehouse online. Se non si dispone già di un data warehouse, informazioni su come creare un Data Warehouse SQL Azure.

> [AZURE.NOTE] Se l'account di archiviazione e la data warehouse vengono creati nella stessa regione sono migliorare le prestazioni.

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>Passaggio 1: Accedere a dati piattaforma Studio con il proprio account Azure
Aprire il web browser e passare al sito Web di [Studio piattaforma dati](https://www.dataplatformstudio.com/) . Accedere con lo stesso account Azure utilizzati per creare deposito account e dati. Se l'indirizzo di posta elettronica è associato un lavoro o account dell'istituto di istruzione e un account Microsoft, assicurarsi di scegliere l'account che dispone dell'accesso alle risorse.

> [AZURE.NOTE] Se si tratta del primo utilizzo Studio piattaforma dati, viene chiesto di concedere all'applicazione l'autorizzazione per gestire le risorse Azure.

## <a name="step-2-start-the-import-wizard"></a>Passaggio 2: Avviare l'importazione guidata
Nella schermata principale DPS selezionare Importa collegamento Azure SQL Data Warehouse per avviare l'importazione guidata.

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>Passaggio 3: Installare il Gateway di Studio piattaforma dati
Per connettersi al database di SQL Server in locale, è necessario installare il Gateway DPS. Il gateway è un agente client che consente di accedere al proprio ambiente locale, estrae i dati e carica al proprio account di archiviazione. I dati non attraversano mai server del Redgate. Per installare il Gateway:

1.  Fare clic sul collegamento **Creare Gateway**
2. Scaricare e installare il Gateway tramite il programma di installazione disponibili

![][2]

> [AZURE.NOTE] Il Gateway può installato nel computer con accesso alla rete al database di SQL Server di origine. Accede al database di SQL Server mediante l'autenticazione di Windows con le credenziali dell'utente corrente.

Dopo aver installato, lo stato del Gateway sia impostato su connesso ed è possibile selezionare Avanti.

## <a name="step-4-identify-the-source-database"></a>Passaggio 4: Identificare il database di origine
Nella casella di testo *Nome Server* immettere il nome del server che ospita i database e selezionare **Avanti**. Selezionare il database che si desidera importare i dati dal menu a discesa.

![][3]

DPS esamina il database selezionato per le tabelle da importare. Per impostazione predefinita, DPS Importa tutte le tabelle del database. Selezionare o deselezionare le tabelle, espandere il collegamento di tutte le tabelle. Selezionare il pulsante Avanti per spostarsi in avanti.

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>Passaggio 5: Scegliere un account di archiviazione per organizzare i dati
DPS viene richiesto il percorso per i dati sul passaggio. Scegliere un account esistente di spazio di archiviazione dall'abbonamento e selezionare **Avanti**.

> [AZURE.NOTE] Creare un contenitore di blob nuovo account di archiviazione scelto DPS verrà utilizzare una cartella distinta per ogni importazione.

![][4]

## <a name="step-6-select-a-data-warehouse"></a>Passaggio 6: Selezionare un data warehouse
Selezionare quindi un database [SQL di Azure Data Warehouse](http://aka.ms/sqldw) online per importare dati in. Dopo avere selezionato il database, è necessario immettere le credenziali per connettersi al database e selezionare **Avanti**.

![][5]

> [AZURE.NOTE] DPS unisce le tabelle di dati di origine nell'archivio di dati. DPS Visualizza un avviso se il nome della tabella richiede per sovrascrivere le tabelle esistenti nel data warehouse. È possibile eliminare tutti gli oggetti esistenti nel data warehouse da scattare Elimina tutti gli oggetti esistenti prima dell'importazione.

## <a name="step-7-import-the-data"></a>Passaggio 7: Importare i dati
DPS conferma che si desidera importare i dati. È sufficiente fare clic sul pulsante di importazione inizio per avviare l'importazione dei dati.

![][6]

DPS consente di visualizzare una visualizzazione che mostra lo stato di avanzamento di estrazione e caricare i dati di SQL Server in locale e lo stato di avanzamento dell'importazione nell'archivio di dati SQL.

![][7]

Al termine dell'importazione, DPS consente di visualizzare un riepilogo di importazione dei dati e un rapporto di modificare le correzioni di compatibilità sono state eseguite.

![][8]

## <a name="next-steps"></a>Passaggi successivi
Per esplorare i dati all'interno di SQL Data Warehouse, prima di tutto la visualizzazione:

- [Query SQL Azure Data Warehouse (Visual Studio)][]
- [Visualizzare i dati con Power BI][]

Per ulteriori informazioni su dati piattaforma Studio del Redgate:

- [Visitare la home page DPS](http://www.dataplatformstudio.com/)
- [Offre una dimostrazione di DPS su Channel9](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

Per una panoramica su altri modi per eseguire la migrazione e caricare i dati in SQL Data Warehouse vedere:

- [Eseguire la migrazione della soluzione nell'archivio di dati SQL][]
- [Caricare i dati nell'archivio di dati di SQL Azure](./sql-data-warehouse-overview-load.md)

Per ulteriori suggerimenti di sviluppo, vedere [Panoramica di sviluppo SQL Data Warehouse](./sql-data-warehouse-overview-develop.md).

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Query SQL Azure Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualizzare i dati con Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Eseguire la migrazione della soluzione nell'archivio di dati SQL]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md