<properties
   pageTitle="Presentare catalogo U-SQL di Azure dati Lake Analitica | Azure"
   description="Presentare catalogo Azure dati Lake Analitica U-SQL"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="use-u-sql-catalog"></a>Usare SQL U catalogo

Viene utilizzato il catalogo U SQL per strutturare i dati e il codice in modo che possano essere condivisi da script U-SQL. Il catalogo consente le massime prestazioni possibili con dati di Azure dati Lake.

Ogni account Azure dati Lake Analitica ha un solo catalogo U SQL è associato. Non è possibile eliminare il catalogo U-SQL. Attualmente cataloghi U SQL non può essere condivisa tra gli account di archivio di dati Lake.

Ogni catalogo U SQL contiene un database denominato **Master**. Non è possibile eliminare il Database di schema.  Ogni catalogo U SQL può contenere più altri database.

Database SQL di U contiene:

- Assembly: condividere il codice .NET tra script U-SQL.
- Funzioni di valori di tabella: condividere il codice SQL U tra script U-SQL.
- Tabelle: condividere dati tra gli script U-SQL.
- Schemi - condividere schemi di tabelle tra script U-SQL.

## <a name="manage-catalogs"></a>Gestire i cataloghi
Ogni account Azure dati Lake Analitica ha un account di Azure dati Lake archivio predefinito è associato. L'account di archivio di dati Lake è definito come account predefinito archivio Lake dati. Catalogo SQL U verrà archiviato in account di archivio di dati Lake predefinito nella cartella dei contenuti. Non eliminare i file nella cartella contenuti.

### <a name="use-azure-portal"></a>Usare il portale di Azure

Vedere [portale di gestione dati Lake Analitica](data-lake-analytics-manage-use-portal.md#view-u-sql-catalog)


### <a name="use-data-lake-tools-for-visual-studio"></a>Usare dati Lake Tools per Visual Studio.

È possibile utilizzare dati Lake Tools per Visual Studio per gestire il catalogo.  Per ulteriori informazioni sugli strumenti, vedere [Utilizzo di dati Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

**Per gestire il catalogo**

1. Aprire Visual Studio e connettersi a azure. Per ulteriori informazioni, vedere [connettersi a Azure](data-lake-analytics-data-lake-tools-get-started.md#connect-to-azure).
1. Aprire **Esplora Server** , premere **CTRL + ALT + S**.
2. Da **Esplora Server**espandere **Azure**, **Dati Lake Analitica**, l'account di dati Lake Analitica, **database**e quindi espandere **master**.



    - Per aggiungere un nuovo Database, rapida **Database**e quindi fare clic su **Crea Database**.
    - Per aggiungere un nuovo assembly, rapida **assembly**e quindi fare clic su **Registra Assembly**.
    - Per aggiungere un nuovo schema, rapida **schemi**e quindi fare clic su "Crea Schema * *.
    - Per aggiungere una nuova tabella, rapida **tabelle**e quindi fare clic su "" creare tabella * *.
    - Per aggiungere una nuova funzione di valori di tabella, vedere [operatori per i processi di dati Lake Analitica definite dall'utente di sviluppare U-SQL](data-lake-analytics-u-sql-develop-user-defined-operators.md).


![Individuare i cataloghi U SQL Visual Studio](./media/data-lake-analytics-use-u-sql-catalog/data-lake-analytics-browse-catalogs.png)


## <a name="see-also"></a>Vedere anche

- Guida introduttiva
    - [Guida introduttiva a Analitica Lake dati tramite il portale di Azure](data-lake-analytics-get-started-portal.md)
    - [Guida introduttiva a Analitica Lake dati tramite PowerShell Azure](data-lake-analytics-get-started-powershell.md)
    - [Guida introduttiva a dati Lake Analitica utilizzando Azure .NET SDK](data-lake-analytics-get-started-net-sdk.md)
    - [Sviluppare script U SQL mediante dati Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Guida introduttiva a linguaggio Azure dati Lake Analitica U-SQL](data-lake-analytics-u-sql-get-started.md)

- U-SQL e sviluppo
    - [Guida introduttiva a linguaggio Azure dati Lake Analitica U-SQL](data-lake-analytics-u-sql-get-started.md)
    - [Utilizzare le funzioni finestra U SQL per i processi di Azure dati Lake Analitica](data-lake-analytics-use-window-functions.md)
    - [Sviluppare operatori definiti dall'utente U SQL per i processi di dati Lake Analitica](data-lake-analytics-u-sql-develop-user-defined-operators.md)

- Gestione
    - [Gestire Azure dati Lake Analitica tramite il portale di Azure](data-lake-analytics-manage-use-portal.md)
    - [Gestire Azure dati Lake Analitica tramite PowerShell Azure](data-lake-analytics-manage-use-powershell.md)
    - [Monitorare e risolvere i processi di Azure dati Lake Analitica tramite il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- Esercitazione-to-end
    - [Utilizzare Tutorial interattivi Analitica Lake dati di Azure](data-lake-analytics-use-interactive-tutorials.md)
    - [Analizzare i registri di sito Web usando Analitica Lake dati di Azure](data-lake-analytics-analyze-weblogs.md)
