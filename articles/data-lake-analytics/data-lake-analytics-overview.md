<properties 
   pageTitle="Panoramica di Microsoft Azure dati Lake Analitica | Azure" 
   description="Dati Lake Analitica è un servizio di calcolo di Azure dati che consente di utilizzare dati per supportare le attività aziendali con informazioni dettagliate sui acquisita dai dati nel cloud, indipendentemente da dove e indipendentemente dalle dimensioni. Dati Lake Analitica abilita questa operazione in più semplice, più scalable ed economico modo possibile. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Panoramica di Microsoft Azure dati Lake Analitica

## <a name="what-is-azure-data-lake-analytics"></a>Che cos'è Azure dati Lake Analitica?

Azure dati Lake Analitica è un nuovo servizio progettato per facilitare analitica di dati. Questo consente di servizio di concentrarsi su scrittura, in esecuzione e la gestione dei processi, piuttosto che operativo infrastruttura distribuita. Invece di distribuzione, configurazione e ottimizzazione di hardware, è possibile scrivere query per trasformare i dati ed estrarre preziose informazioni. Il servizio analitica può gestire processi di qualsiasi scala istantaneamente impostando semplicemente la connessione per la potenza di è necessario. Pagando solo per il lavoro quando viene eseguito rendendo conveniente. Il servizio analitica supporta Azure Active Directory e consentire agli semplicemente gestire l'accesso e sui ruoli, integrati con il sistema di identità locale. Include inoltre U-SQL, un linguaggio che combina i vantaggi di SQL con la potenza esplicita del codice utente. Runtime distribuito scalable U-SQL consente di analizzare in modo efficiente i dati nell'archivio e in SQL Server Azure, Database SQL Azure e Azure SQL Data Warehouse.


## <a name="key-capabilities"></a>Funzionalità principali

- **Adattamento dinamico** 

    Dati Lake Analitica è progettato novo scala cloud e le prestazioni.  In modo dinamico, esegue il provisioning di risorse e consente di eseguire analitica terabyte o persino Exabyte dei dati. Al termine del processo, esso venti verso il basso risorse automaticamente e pagare solo per la potenza di elaborazione utilizzata. Aumentare o diminuire le dimensioni dei dati archiviati o la quantità di calcolo usato, non è necessario riscrittura codice. Questo consente di concentrarsi su logica aziendale sola e non su come elaborare e archiviare grandi set di dati. 

- **Sviluppare più rapidamente, eseguire il debug e ottimizzare più intelligente con strumenti familiari**

    Dati Lake Analitica con integrazione con Visual Studio, in modo che è possibile utilizzare strumenti noti per eseguire il debug e ottimizzare il codice. Visualizzazioni dei processi U SQL consentono di visualizzare la modalità di esecuzione di codice in scala, che è possibile identificare facilmente le prestazioni bottiglia e ottimizzare i costi. 

- **U-SQL: semplice e familiare extensible e potenti**

    Dati Lake Analitica include U-SQL, un linguaggio di query che si estende la natura semplice, familiare dichiarativa di SQL con la potenza esplicita di c#. Il linguaggio SQL U integrato sullo stesso runtime distribuito che potenza i sistemi di dati all'interno di Microsoft. Milioni di sviluppatori SQL e .NET possono ora elaborare e analizzare tutti i relativi dati con le competenze che hanno già.

- **Integrazione diretta con gli investimenti IT**

    Dati Lake Analitica consentono di identità, gestione, sicurezza e data warehouse investimenti IT esistenti. Questo semplifica la gestione di dati e facile estendere le applicazioni di dati corrente. Dati Lake Analitica è integrato con Active Directory per le autorizzazioni e gestione degli utenti e viene fornito con il controllo e il monitoraggio incorporato.

- **Conveniente e conveniente**

    Dati Lake Analitica è una soluzione per l'esecuzione di carichi di lavoro di dati. Pagando in base al processo durante l'elaborazione di dati. Nessun hardware, licenze o accordi specifiche del servizio di supporto sono necessari. Il sistema scala automaticamente verso l'alto o verso il basso durante il processo viene avviata e completata, vale a dire mai pagare per più funzioni è necessario. 

- **Funziona con tutti i dati di Azure**

    Dati Lake Analitica possibile collaborare con un numero di origini dati di Azure: archiviazione Blob Azure, database SQL Azure e naturalmente dati Lake Analitica appositamente ottimizzata per lavorare con Azure dati Lake Store - fornire elevato delle prestazioni, velocità e parallelizzazione i carichi di lavoro di dati.

## <a name="see-also"></a>Vedere anche

- Guida introduttiva
    - [Guida introduttiva a Analitica Lake dati nel portale di Azure](data-lake-analytics-get-started-portal.md)
    - [Guida introduttiva a Analitica Lake dati tramite PowerShell Azure](data-lake-analytics-get-started-powershell.md)
    - [Guida introduttiva a dati Lake Analitica utilizzando Azure .NET SDK](data-lake-analytics-get-started-net-sdk.md)
    - [Sviluppare script U SQL mediante dati Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Guida introduttiva a linguaggio Azure dati Lake Analitica U-SQL](data-lake-analytics-u-sql-get-started.md)
    
- U-SQL e sviluppo
    - [Guida introduttiva a linguaggio Azure dati Lake Analitica U-SQL](data-lake-analytics-u-sql-get-started.md)
    - [Utilizzare le funzioni finestra U SQL per i processi di Azure dati Lake Analitica](data-lake-analytics-use-window-functions.md)
    - [Sviluppare operatori definiti dall'utente U SQL per i processi di dati Lake Analitica](data-lake-analytics-u-sql-develop-user-defined-operators.md)
    
- Gestione
    - [Gestire Azure dati Lake Analitica nel portale di Azure](data-lake-analytics-manage-use-portal.md)
    - [Gestire Azure dati Lake Analitica tramite PowerShell Azure](data-lake-analytics-manage-use-powershell.md)
    - [Monitorare e risolvere i processi di Azure dati Lake Analitica nel portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
    - [Accesso ai registri di diagnostica per Azure dati Lake Analitica](data-lake-analytics-diagnostic-logs.md)

- Esercitazione-to-end
    - [Utilizzare Tutorial interattivi Analitica Lake dati di Azure](data-lake-analytics-use-interactive-tutorials.md)
    - [Analizzare i registri di sito Web usando Analitica Lake dati di Azure](data-lake-analytics-analyze-weblogs.md)

- Indicare si ritiene che
    - [Commentare backlog documentazione](data-lake-analytics-documentation-backlog.md)
    - [Inviare una richiesta di funzionalità](http://aka.ms/adlafeedback)
    - [È possibile ottenere assistenza nei forum](http://aka.ms/adlaforums)


