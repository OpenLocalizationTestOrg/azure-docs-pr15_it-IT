<properties 
    pageTitle="Dati di Azure Factory - domande frequenti" 
    description="Domande frequenti su Factory di dati di Azure." 
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
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---frequently-asked-questions"></a>Dati di Azure Factory - domande frequenti

## <a name="general-questions"></a>Domande di carattere generale

### <a name="what-is-azure-data-factory"></a>Che cos'è Azure Data Factory?

Dati Factory è basato su cloud servizio di integrazione dei dati che **consente di automatizzare lo spostamento e trasformazione dei dati**. Come factory eseguibile apparecchiature per acquisire materia e trasformarle in prodotti termine, Data Factory coordina servizi esistenti per la raccolta di dati non elaborati e trasformano in informazioni pronte per l'uso. 
 
Factory dati consente di creare flussi di lavoro basati sui dati per spostare i dati tra sia locale e archivi dati cloud, nonché processo/trasformazione dati tramite i servizi di calcolo, ad esempio Azure HDInsight e Azure dati Lake Analitica. Dopo aver creato una pipeline che esegue l'azione che è necessario, è possibile pianificare l'esecuzione periodicamente (ogni ora, giornaliera, settimanale e così via).   

Per ulteriori informazioni, vedere [Panoramica e concetti](data-factory-introduction.md) . 

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Dove è possibile trovare ulteriori informazioni sui prezzi per Azure Data Factory?

Vedere [la pagina dettagli prezzi Factory di dati] [ adf-pricing-details] per informazioni sui prezzi per Azure Data Factory.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Come iniziare con Azure Data Factory

- Per una panoramica su Factory di dati di Azure, vedere [Introduzione alle Azure Data Factory](data-factory-introduction.md).
- Per un'esercitazione su come **copiare o spostare dati** usando Copia attività, vedere [copiare i dati dallo spazio di archiviazione Blob Azure al Database SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Per un'esercitazione su come **trasformare i dati** utilizzando attività Hive HDInsight. Vedere [i dati di processo mediante l'esecuzione di script Hive cluster Hadoop](data-factory-build-your-first-pipeline.md) 
  
### <a name="what-is-the-data-factorys-region-availability"></a>Che cos'è la disponibilità di area geografica della Factory dati?
Factory dati è disponibile in **Europa Nord America**e **Ovest degli Stati Uniti** . I servizi di elaborazione e di archiviazione utilizzati da factory dati possono essere in altre aree geografiche. Vedere [aree supportati](data-factory-introduction.md#supported-regions). 
 
### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Quali sono i limiti al numero di dati factory/tubazioni/attività/set di dati?
 
Vedere la sezione **Factory limiti sui dati di Azure** dell'articolo [abbonamento Azure e limiti del servizio, le quote e i vincoli](../azure-subscription-service-limits.md#data-factory-limits) .

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Che cos'è l'esperienza di creazione condivisa/sviluppo con il servizio di Azure Data Factory?

È possibile autore/creare factory dati utilizzando una delle operazioni seguenti:

- **Portale di Azure**  
   pale la Factory di dati nel portale di Azure forniscono l'interfaccia utente avanzata per la creazione di servizi Active Directory collegato factory dati. **Dati Factory Editor**, che fa parte del portale, consente di creare facilmente servizi collegati, tabelle, set di dati e pipeline specificando le definizioni di JSON per questi elementi. Per un esempio dell'editor di portale/per creare e distribuire una factory di dati, vedere [creare il prima pipeline di dati tramite il portale di Azure](data-factory-build-your-first-pipeline-using-editor.md) .

- **Visual Studio**  
   è possibile utilizzare Visual Studio per creare una factory di dati di Azure. Per informazioni dettagliate, vedere [creare il prima pipeline di dati utilizzando Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) . 

- **Azure PowerShell**  
   per esercitazione/procedura dettagliata per la creazione di una factory di dati tramite PowerShell, vedere [creare e monitor Azure Data Factory tramite PowerShell Azure](data-factory-build-your-first-pipeline-using-powershell.md) . Visualizzare [Dati di riferimento sui Cmdlet Factory] [ adf-powershell-reference] contenuto nella raccolta di MSDN per una documentazione completa del cmdlet Factory dati.
   
- **Libreria di classi .NET**  
   a livello di programmazione, è possibile creare factory dati utilizzando dati Factory .NET SDK. Vedere [creare, monitorare e gestire factory dati utilizzando .NET SDK](data-factory-create-data-factories-programmatically.md) per una procedura dettagliata di creazione di una factory di dati mediante .NET SDK. Vedere [Libreria di classi Factory dati] [ msdn-class-library-reference] per una documentazione completa di dati Factory .NET SDK.

- **API REST**  
   utilizzare API REST esposti dal servizio Azure Data Factory per creare e distribuire factory di dati. Visualizzare [Dati di riferimento all'API REST Factory] [ msdn-rest-api-reference] per una documentazione completa di API REST Factory dati.
 
- **Modelli di gestione risorse Azure** 
   vedere [Esercitazione: compilare il prima factory Azure dati utilizzando il modello di gestione di risorse Azure](data-factory-build-your-first-pipeline-using-arm.md) dettagli fo. 

### <a name="can-i-rename-a-data-factory"></a>È possibile rinominare una factory dati?
No. Ad altre risorse Azure, non è possibile modificare il nome di un dati Azure. 

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>È possibile spostare una factory di dati da una sottoscrizione di Azure a altro? 
Sì. Usare il pulsante **Sposta** su blade di factory i dati come illustrato nella figura seguente. 

![Sposta dati factory](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Quali sono gli ambienti di elaborazione supportati dal produttore dati?
La tabella seguente contiene un elenco di ambienti calcolo supportati dal produttore dati e le attività che è possono eseguire su di essi. 

| Calcolare ambiente | attività |
| ------------------- | -------- | 
| [HDInsight su richiesta cluster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) o [cluster HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md) [Hive](data-factory-hive-activity.md), [maialino](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) | 
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |  
| [Apprendimento Azure](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) | [Computer attività di apprendimento: esecuzione Batch e delle risorse di aggiornamento](data-factory-azure-ml-batch-execution-activity.md) |
| [Dati di Azure Lake Analitica](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) | [Dati Analitica Lake U-SQL](data-factory-usql-activity.md)
| [SQL azure](data-factory-compute-linked-services.md#azure-sql-linked-service), [SQL Azure Data Warehouse](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) | [Stored Procedure](data-factory-stored-proc-activity.md)

## <a name="activities---faq"></a>Attività - domande frequenti
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Quali sono i diversi tipi di attività che è possibile utilizzare in una pipeline Factory dati? 

- [Attività di spostamento dei dati](data-factory-data-movement-activities.md) per spostare i dati.
- [Attività di trasformazione dati](data-factory-data-transformation-activities.md) processo/Trasforma dati. 

### <a name="when-does-an-activity-run"></a>Quando si esegue un'attività?
Le impostazioni di configurazione della **disponibilità** nella tabella di dati di output determinano durante l'esecuzione dell'attività. Se sono specificati input set di dati, l'attività controlla se vengono soddisfatte tutte le dipendenze di dati di input (che è stato **Pronto** ) prima dell'avvio in esecuzione. 

## <a name="copy-activity---faq"></a>Copiare attività - domande frequenti
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>È preferibile disporre di una pipeline con più attività o una pipeline separata per ogni attività? 
Si suppone pipeline di raggruppare le attività correlate. Se il set di dati per la connessione non è utilizzata da qualsiasi altra attività di fuori della pipeline, è possibile mantenere le attività nella pipeline di uno. In questo modo da non occorre periodi attivi pipeline catena in modo che vengano allineati tra loro. Inoltre, l'integrità dei dati nelle tabelle interne alla pipeline meglio viene mantenuto quando si aggiorna la pipeline. Aggiornamento pipeline essenzialmente interrompe tutte le attività all'interno della pipeline, vengono rimosse e li crea nuovo. Dall'authoring prospettiva, potrebbe anche essere più semplice visualizzare il flusso di dati all'interno delle attività correlate in un unico file JSON per la pipeline.

### <a name="what-are-the-supported-data-stores"></a>Quali sono gli archivi di dati supportati?
[AZURE.INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

### <a name="what-are-the-supported-file-formats"></a>Quali sono i formati di file? 
[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Nel punto in cui viene eseguita l'operazione di copia? 
Vedere la sezione [spostamento dei dati disponibili a livello globale](data-factory-data-movement-activities.md#global) per informazioni dettagliate. In breve, quando è presente un archivio di dati in locale, l'operazione di copia viene eseguita tramite il Gateway di gestione dati nel proprio ambiente locale. E, quando lo spostamento dei dati tra due archivi cloud, nell'area vicino alla posizione geografica stesso sink viene eseguita l'operazione di copia. 


## <a name="hdinsight-activity---faq"></a>HDInsight attività - domande frequenti

### <a name="what-regions-are-supported-by-hdinsight"></a>Quali aree sono supportate da HDInsight?

Nella sezione disponibilità geografici l'articolo seguente: o [Dettagli prezzi HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Regione viene utilizzato da un cluster di HDInsight su richiesta?

Il cluster di HDInsight su richiesta viene creato nella stessa area in cui è presente l'archivio specificato per l'utilizzo con i cluster.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Come è possibile associare gli account di spazio di archiviazione aggiuntivo per il cluster HDInsight?

Se si usa il proprio HDInsight Cluster (BYOC - portare il Cluster personalizzato), vedere gli argomenti seguenti: 

- [Utilizzo di un Cluster di HDInsight con Metastores e gli account di archiviazione alternativo][hdinsight-alternate-storage]
- [Usare gli account di spazio di archiviazione aggiuntivo con Hive HDInsight][hdinsight-alternate-storage-2]

Se si utilizza un cluster su richiesta creati dal servizio dati Factory, specificare gli account di spazio di archiviazione aggiuntivo per il HDInsight collegato servizio in modo che il servizio Factory dati possibile registrarle per proprio conto. Nella definizione del JSON per il servizio collegata su richiesta, utilizzare proprietà **additionalLinkedServiceNames** per specificare gli account di archiviazione alternativo come illustrato nel frammento di JSON seguente:
 
    {
        "name": "MyHDInsightOnDemandLinkedService",
        "properties":
        {
            "type": "HDInsightOnDemandLinkedService",
            "typeProperties": {
                "clusterSize": 1,
                "timeToLive": "00:01:00",
                "linkedServiceName": "LinkedService-SampleData",
                "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
            }
        }
    } 

Nell'esempio precedente, otherLinkedServiceName1 e otherLinkedServiceName2 rappresentano servizi collegati cui definizioni contengono le credenziali necessarie per accedere agli account di archiviazione alternativi cluster HDInsight.

## <a name="slices---faq"></a>Sezioni - domande frequenti

### <a name="why-are-my-input-slices-not-in-ready-state"></a>Perché sono le sezioni di input non nello stato pronto?  
Un errore comune non è proprietà **esterna** impostazione su **true** per il set di dati di input quando i dati di input sono esterni al produttore dati (non prodotto da factory dati). 

Nell'esempio seguente, è necessario impostare **esterni** su true per **dataset1**.  

**DataFactory1** Pipeline 1: dataset1 -> activity1 -> dataset2 -> activity2 -> dataset3 Pipeline 2: dataset3 -> activity3 -> dataset4

Se si dispone di un altro produttore di dati con una pipeline che consente di accedere dataset4 (prodotto dalla pipeline 2 nella factory dati 1), contrassegnare dataset4 come un set di dati esterni in quanto viene visualizzato il set di dati da una factory di dati diversi (DataFactory1, non DataFactory2).  

**DataFactory2**    
Pipeline 1: dataset4 -> activity4 -> dataset5

Se la proprietà esterna è impostata correttamente, verificare se i dati di input esistono nel percorso specificato nella definizione del set di dati input. 

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Come è possibile eseguire una sezione in un secondo momento alla mezzanotte quando la sezione è realizzata giornaliero?
Utilizzare la proprietà **offset** per specificare l'ora in cui si desidera visualizzare la sezione da produrre. Vedere la sezione [disponibilità set di dati](data-factory-create-datasets.md#Availability) per i dettagli di questa proprietà. Ecco un rapido esempio:

    "availability":
    {
        "frequency": "Day",
        "interval": 1,
        "offset": "06:00:00"
    }

Sezioni del giorno partono **6 AM** anziché mezzanotte predefinito.     

### <a name="how-can-i-rerun-a-slice"></a>Come è possibile eseguire nuovamente una sezione?
È possibile eseguire nuovamente una sezione in uno dei modi seguenti: 

- Utilizzare Monitor e Gestisci App per rieseguire una finestra di attività o di sezione. Per istruzioni, vedere [eseguire nuovamente selezionato windows attività](data-factory-monitor-manage-app.md#re-run-selected-activity-windows) .   
- Fare clic su **Esegui** sulla barra di comando e il **Sezione di dati** per la sezione nel portale di Azure.
- Eseguire il cmdlet **Set-AzureRmDataFactorySliceStatus** con stato impostato su **in attesa** per la sezione.   
    
        Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

Vedere [Set-AzureRmDataFactorySliceStatus] [ set-azure-datafactory-slice-status] per informazioni dettagliate sul cmdlet. 

### <a name="how-long-did-it-take-to-process-a-slice"></a>Quanto tempo per l'elaborazione di una sezione?
Utilizzare attività finestra Explorer monitorare e gestire App per conoscere il tempo impiegato per l'elaborazione di una sezione di dati. Per informazioni dettagliate, vedere [Attività finestra Explorer](data-factory-monitor-manage-app.md#activity-window-explorer) . 

È anche possibile eseguire le operazioni seguenti nel portale di Azure:  

1. Fare clic su riquadro di **set di dati** nella e **Produttore dati** dell'ambiente di produzione di dati.
2. Fare clic sul set di dati specifici su e il **set di dati** .
3. Selezionare la sezione che si è interessati dall'elenco di **sezioni recenti** e il **tabella** .
4. Fare clic su attività eseguite dall'elenco di **Attività viene eseguito** e il **Sezione di dati** . 
5. Fare clic su riquadro **proprietà** nella e **Dettagli eseguire attività** . 
6. Verrà visualizzato il campo **durata** con un valore. Questo valore indica il tempo necessario per l'elaborazione della sezione.   

### <a name="how-to-stop-a-running-slice"></a>Come interrompere una sezione in esecuzione?
Se è necessario interrompere la pipeline di esecuzione, è possibile utilizzare i cmdlet di [Sospensione AzureRmDataFactoryPipeline](https://msdn.microsoft.com/library/mt603721.aspx) . Sospendere la pipeline al momento non termina le esecuzioni di sezione in corso. Dopo aver completato le esecuzioni in corso, sezione aggiuntive non viene raccolta.

Se si desidera interrompere immediatamente tutte le esecuzioni, è l'unico modo per eliminare la pipeline e crearne uno nuovo. Se si sceglie di eliminare la pipeline, non è necessario eliminare tabelle e servizi collegati utilizzati dalla pipeline. 


[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx 
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/mt603522.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
 
