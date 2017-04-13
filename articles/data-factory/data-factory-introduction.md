<properties 
    pageTitle="Introduzione ai dati Factory, un servizio di integrazione dati | Microsoft Azure" 
    description="Informazioni sulle novità di Azure Data Factory: un servizio di integrazione dati cloud che coordina e automatiche spostamento e trasformazione dei dati." 
    keywords="integrazione dei dati, l'integrazione di dati cloud, che cos'è factory dati azure"
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="09/22/2016" 
    ms.author="shlo"/>

# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>Introduzione al servizio di Azure Data Factory, un servizio di integrazione di dati nel cloud

## <a name="what-is-azure-data-factory"></a>Che cos'è Azure Data Factory? 
Dati Factory è un servizio di integrazione di dati basate su cloud che coordina e consente di automatizzare lo **spostamento** e **trasformazione** dei dati. È possibile creare soluzioni di integrazione dati tramite il servizio Factory dati che è possano acquisire dati da diversi archivi dati, i dati/processo di trasformazione e pubblicare i risultati per gli archivi dati. 

Servizio Factory dati consente di creare pipeline di dati da spostano e trasformano i dati e quindi eseguire la pipeline alla programmazione di un determinato (ora, giornaliera, settimanale e così via). Inoltre, sono disponibili visualizzazioni avanzate per visualizzare le dipendenze tra la pipeline di dati e sulla derivazione dell'e monitorare tutte le pipeline di dati da una singola visualizzazione unificata per individuare facilmente problemi e configurazione monitoraggio degli avvisi.

![Diagramma: Panoramica su Factory Data, un servizio di integrazione dati](./media/data-factory-introduction/what-is-azure-data-factory.png)
**Figure1.** Acquisire dati da varie origini dati, preparare, trasformazione e analizzare i dati e quindi pubblicare pronte per l'uso dati per il consumo.

## <a name="pipelines-and-activities"></a>Tubazioni e attività
In una soluzione Data Factory, verrà creata una o più dati **pipeline**. Una pipeline è un raggruppamento logico delle attività. Vengono utilizzati per le attività di gruppo in un'unità che insieme eseguono un'attività. 

**Attività di** definire le azioni da eseguire sui dati. Ad esempio, utilizzare un'attività di copia per copiare i dati dall'archivio di dati in un altro archivio dati. Analogamente, è possibile utilizzare un'attività Hive che esegue una query Hive in un cluster di Azure HDInsight trasformare o analizzare i dati. Dati Factory supporta due tipi di attività: attività di spostamento dei dati e attività di trasformazione dati. 
  
## <a name="data-movement-activities"></a>Attività di spostamento dei dati 
[AZURE.INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Vedere l'articolo [Le attività di spostamento dei dati](data-factory-data-movement-activities.md) per altri dettagli. 

## <a name="data-transformation-activities"></a>Attività di trasformazione dati
[AZURE.INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

Vedere l'articolo [Le attività di trasformazione dati](data-factory-data-transformation-activities.md) per altri dettagli.

Se è necessario spostare dati da/verso un dati memorizzata che attività copia non supportano, o trasformare i dati utilizzando la logica, creare un' **attività personalizzata .NET**. Per informazioni dettagliate sulla creazione e utilizzo di un'attività personalizzata, vedere [Usa attività personalizzata in una pipeline di Azure Data Factory](data-factory-use-custom-activities.md).

## <a name="linked-services"></a>Servizi collegati
Servizi collegati definiscono le informazioni necessarie per Data Factory per connettersi a risorse esterne (esempi: archiviazione di Azure, SQL Server Azure HDInsight in locale). Servizi collegati vengono usati per due motivi nella Factory dati:

- Per rappresentare un **archivio dati** inclusi, ma non solo a un Server SQL locale, database Oracle, file di condivisione o un account di archiviazione Blob Azure. Vedere la sezione [attività spostamento dei dati](data-factory-data-movement-activities.md) per un elenco degli archivi di dati supportati. 
- Per rappresentare una **calcolare delle risorse** che possono contenere l'esecuzione di un'attività. Ad esempio, l'attività HDInsightHive viene eseguita in un cluster di HDInsight Hadoop. Vedere la sezione [attività di trasformazione dati](data-factory-data-transformation-activities.md) per un elenco di ambienti di elaborazione supportati. 

## <a name="datasets"></a>Set di dati 
Servizi collegati collegamento archivi dati su una factory di dati di Azure. Set di dati rappresentano strutture di dati con gli archivi dati. Ad esempio, un servizio di archiviazione di Azure collegato fornisce informazioni di connessione per Data Factory per connettersi a un account di archiviazione Azure. Un set di dati Blob Azure specifica il contenitore blob e la cartella dell'archivio Blob Azure in cui la pipeline deve letti i dati. Analogamente, un servizio di SQL Azure collegato fornisce informazioni sulla connessione per un database SQL Azure e un set di dati di SQL Azure viene specificata la tabella che contiene i dati.   

## <a name="relationship-between-data-factory-entities"></a>Relazione tra entità Factory dati
Dati Factory sono pochi chiave entità che collaborano per definire input e generare l'output dei dati, l'elaborazione di eventi e la pianificazione e le risorse necessarie per eseguire il flusso di dati desiderato.

![Diagramma: Data Factory, un servizio di integrazione dati cloud - concetti](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**Figura 2.** Relazioni tra servizio set di dati, attività, Pipeline e collegato

Con quattro semplici concetti di servizi collegati, set di dati, attività e pipeline, si è pronti iniziare. È possibile [creare la pipeline di prima](data-factory-build-your-first-pipeline.md). 

## <a name="supported-regions"></a>Aree supportate
Attualmente, è possibile creare factory dati nelle aree **Ovest Stati Uniti**, **Stati Uniti orientali**e **Nord Europa** . Tuttavia, una factory di dati possibile accedere archivi dati e servizi in altre aree di Azure per spostare i dati tra gli archivi dati di elaborazione o elaborazione di dati con i servizi di elaborazione. 

Azure Data Factory non vengono archiviati i dati. Consente di creare flussi di basati sui dati per la gestione di spostamento dei dati tra [supportati archivi dati](data-factory-data-movement-activities.md#supported-data-stores) ed elaborazione dei dati tramite [servizi di elaborazione](data-factory-compute-linked-services.md) in altre aree geografiche o in un ambiente locale. Consente inoltre a [monitorare e gestire i flussi di lavoro](data-factory-monitor-manage-pipelines.md) utilizzando sia livello di programmazione e dell'interfaccia utente. 

Anche se è disponibile solo **Ovest degli Stati Uniti**, **Stati Uniti orientali**e aree **Nord Europa** Azure Data Factory, il servizio per lo spostamento dei dati nella Factory dati è disponibile [a livello globale](data-factory-data-movement-activities.md#global) in diverse aree geografiche. In caso di un archivio dati dietro un firewall quindi un [Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) installati in ambiente locale sposta invece i dati. 

Ad esempio, si presuppone che gli ambienti di calcolo, ad esempio Azure HDInsight cluster e apprendimento Azure sono in esecuzione fuori area Europa occidentale. Si creare e utilizzare un'istanza di Azure Data Factory europei Nord America e usarlo per pianificare processi negli ambienti di calcolo in Europa occidentale. Bastano pochi millisecondi per Data Factory avviare il processo nel proprio ambiente di elaborazione ma non di modificare l'orario per l'esecuzione del processo al proprio ambiente informatico.

Si desidera che Azure Data Factory in ogni area geografica supportato da Azure in futuro.
  
## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come creare factory di dati con pipeline di dati, seguire le istruzioni dettagliate dell'esercitazioni elencate di seguito. 

Esercitazione | Descrizione
-------- | -----------
[Creare una pipeline di dati che elabora i dati utilizzando Hadoop cluster](data-factory-build-your-first-pipeline.md) | In questa esercitazione si creano i prima factory Azure dati con una pipeline di dati tali **dati processi** eseguendo Hive script in un cluster di Azure HDInsight (Hadoop). |
[Creare una pipeline di dati per spostare i dati tra due archivi dati cloud](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | In questa esercitazione viene creato una factory di dati con una pipeline che **consente di spostare dati** dallo spazio di archiviazione Blob al database SQL.
[Creare una pipeline di dati per spostare i dati tra un archivio di dati in locale e un archivio di dati cloud tramite Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) | In questa esercitazione si creano fabbrica dati con una pipeline che **consente di spostare dati** da un database di SQL Server **locale** a un archivio blob Azure. Come parte della procedura dettagliata, installare e configurare il Gateway di gestione dati nel computer in uso. 
