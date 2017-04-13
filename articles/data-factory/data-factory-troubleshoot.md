<properties 
    pageTitle="Risoluzione dei problemi di Azure Data Factory" 
    description="Informazioni su come risolvere i problemi relativi all'utilizzo di Azure Data Factory." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/31/2016" 
    ms.author="spelluru"/>

# <a name="troubleshoot-data-factory-issues"></a>Risoluzione dei problemi di Factory dati
Questo articolo fornisce suggerimenti sulla risoluzione dei problemi per i problemi quando si usa Azure Data Factory. In questo articolo non sono elencati i possibili problemi quando si usa il servizio, ma vengono illustrate alcune problemi e suggerimenti sulla risoluzione dei problemi generali.   

## <a name="troubleshooting-tips"></a>Suggerimenti sulla risoluzione dei problemi

### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Errore: L'abbonamento non è stato registrato per utilizzare spazio dei nomi 'Microsoft.DataFactory'
Se si riceve questo errore, il provider di risorse Azure Data Factory non è stato registrato nel computer in uso. Eseguire le operazioni seguenti: 

1. Avviare PowerShell Azure. 
2. Accedere al proprio account Azure utilizzando il comando seguente.
        Accesso AzureRmAccount 
3. Eseguire il seguente comando per registrare il provider di Azure Data Factory.
        Register-AzureRmResourceProvider - ProviderNamespace Microsoft.DataFactory

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problema: Non autorizzato errore durante l'esecuzione di un cmdlet Factory dati
Probabilmente non si usa l'account Azure verso destra o l'abbonamento con PowerShell Azure. Utilizzare i cmdlet seguenti per selezionare l'account Azure destra e la sottoscrizione per l'utilizzo con PowerShell Azure. 

1. Accesso AzureRmAccount a usare l'ID utente appropriate e la password
2. Get-AzureRmSubscription - consente di visualizzare tutte le sottoscrizioni per l'account. 
3. Selezionare AzureRmSubscription &lt;nome dell'abbonamento&gt; -selezionare l'abbonamento a destra. Utilizzare la stessa che consente di creare una factory di dati nel portale di Azure.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problema: Impossibile avviare l'installazione di esprimere Gateway di gestione dati dal portale Azure
La configurazione di Express per il Gateway di gestione dati richiede Internet Explorer o un browser compatibili ClickOnce Microsoft. Se la configurazione di esprimere non viene avviato, eseguire una delle operazioni seguenti: 

- Usare un browser compatibile Microsoft ClickOnce o Internet Explorer.

    Se si utilizza Chrome, passare all' [archivio web Chrome](https://chrome.google.com/webstore/), eseguire una ricerca con parole chiave "ClickOnce", scegliere una delle estensioni ClickOnce e installarlo. 
    
    Eseguire la stessa operazione per Firefox (Installa componente aggiuntivo). Fare clic su Apri Menu sulla barra degli strumenti (tre linee orizzontali nell'angolo superiore destro), fare clic su componenti aggiuntivi, eseguire una ricerca con parole chiave "ClickOnce", scegliere una delle estensioni ClickOnce e installarlo. 

- Utilizzare il collegamento di **Configurazione manuale** mostrato sugli e stesso nel portale. Utilizzare questo approccio per scaricare il file di installazione ed eseguire manualmente. Al termine dell'installazione, viene visualizzato nella finestra di dialogo Configurazione di Gateway di gestione dati. Copiare la **chiave** dallo schermo del portale e usarlo in Gestione configurazione per registrare il gateway manualmente con il servizio.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problema: Impossibile connettersi a SQL Server locale 
**Gestione configurazione di Gateway di gestione di dati** di avvio del computer gateway e usare la scheda **risoluzione dei problemi** per verificare la connessione a SQL Server dal computer gateway. Per suggerimenti sulla risoluzione dei problemi di connessione/gateway, vedere [problemi di gateway di risoluzione dei](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) problemi.   
 

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problema: Le sezioni di Input sono in attesa ad mai stato

Le sezioni potrebbero essere in **attesa** di stato per diversi motivi. Uno dei motivi comuni è che la proprietà **esterni** non è impostata su **true**. Qualsiasi set di dati che viene visualizzato di fuori dell'ambito di Azure Data Factory devono essere contrassegnati con proprietà **esterna** . Questa proprietà indica che i dati esterni e non insieme ai qualsiasi pipeline all'interno di factory dati. Le sezioni di dati vengono contrassegnate come **pronti** quando i dati sono disponibili nell'archivio rispettivo. 

Vedere nell'esempio seguente per l'utilizzo della proprietà **esterna** . È possibile specificare **externalData*** quando si imposta esterni su true.

Vedere l'articolo [set di dati](data-factory-create-datasets.md) per altre informazioni relative a questa proprietà.
    
    {
      "name": "CustomerTable",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "MyLinkedService",
        "typeProperties": {
          "folderPath": "MyContainer/MySubFolder/",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          }
        }
      }
    }

Per risolvere l'errore, aggiungere la proprietà **esterni** e la sezione facoltativa **externalData** alla definizione JSON della tabella di input e ricreare la tabella. 

### <a name="problem-hybrid-copy-operation-fails"></a>Problema: Ibrido copia esito negativo
Vedere [risoluzione dei problemi di gateway](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) per i passaggi per la risoluzione dei problemi con la copia da/verso un archivio di dati in locale tramite il Gateway di gestione dati. 

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problema: Su richiesta HDInsight si verifica un errore di provisioning
Quando si usa un servizio di tipo HDInsightOnDemand collegato, è necessario specificare un linkedServiceName che punta a un archivio Blob Azure. Servizio Data Factory utilizza questo spazio di archiviazione per archiviare i file di supporto per il cluster HDInsight su richiesta e registri.  In alcuni casi il provisioning di un cluster di HDInsight su richiesta non riesce con l'errore seguente:

        Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Questo errore indica in genere che la posizione dell'account di archiviazione specificato il linkedServiceName non sia nella stessa posizione centro dati in cui si verifica il provisioning di HDInsight. Esempio: se il produttore dati US ovest e lo spazio di archiviazione Azure negli Stati Uniti orientali, il provisioning su richiesta non riesce negli Stati Uniti ovest.

È disponibile anche una seconda additionalLinkedServiceNames proprietà JSON nel punto in cui gli account di spazio di archiviazione aggiuntivo possono essere specificati nel HDInsight su richiesta. Gli account di spazio di archiviazione aggiuntivo collegato devono essere nello stesso percorso cluster HDInsight o non riesce con lo stesso errore.

### <a name="problem-custom-net-activity-fails"></a>Problema: Personalizzati attività .NET non riesce
Per informazioni dettagliate, vedere [eseguire il Debug pipeline con un'attività personalizzata](data-factory-use-custom-activities.md#debug-the-pipeline) . 

## <a name="use-azure-portal-to-troubleshoot"></a>Consente di risolvere i problemi di Azure portale 

### <a name="using-portal-blades"></a>Uso del portale pale
Per istruzioni, vedere [pipeline Monitor](data-factory-build-your-first-pipeline-using-editor.md#monitor-pipeline) . 

### <a name="using-monitor-and-manage-app"></a>Utilizzo di Monitor e gestire App
Vedere [monitorare e gestire dati factory pipeline utilizzando monitorare e gestire App](data-factory-monitor-manage-app.md) per informazioni dettagliate. 

## <a name="use-azure-powershell-to-troubleshoot"></a>Consente di risolvere i problemi di PowerShell di Azure

### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Usare PowerShell Azure per risolvere un errore  
Per informazioni dettagliate, vedere [pipeline Factory dati Monitor tramite PowerShell Azure](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline) . 


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
 