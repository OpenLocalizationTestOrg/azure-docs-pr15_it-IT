<properties
   pageTitle="Raccogliere i registri tramite diagnostica Azure | Microsoft Azure"
   description="In questo articolo viene descritto come configurare Azure diagnostica per raccogliere i registri da un cluster di tessuti di servizio in esecuzione in Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="toddabel"/>


# <a name="collect-logs-by-using-azure-diagnostics"></a>Raccogliere i registri tramite diagnostica Azure

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Quando si esegue un cluster di Azure servizio tessuti, è utile anche per raccogliere i registri da tutti i nodi in una posizione centrale. Con i log in una posizione centrale consente di analizzare e risolvere i problemi del cluster o problemi delle applicazioni e servizi in esecuzione in tale cluster.

Un modo per caricare e raccogliere i log consiste nell'utilizzare l'estensione di diagnostica di Azure, che consente di caricare i registri archiviazione Azure. I registri non sono utili direttamente in archiviazione. Ma è possibile utilizzare un processo esterno per leggere gli eventi dallo spazio di archiviazione e archiviarle in un prodotto, ad esempio [Log Analitica](../log-analytics/log-analytics-service-fabric.md), [Ricerca flessibile](service-fabric-diagnostic-how-to-use-elasticsearch.md)o un'altra soluzione log di analisi.

## <a name="prerequisites"></a>Prerequisiti
È necessario utilizzare questi strumenti per eseguire alcune operazioni in questo documento:

* [Diagnostica Windows Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (relativi ai servizi Cloud Azure ma è buone informazioni ed esempi)
* [Gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md)
* [PowerShell Azure](../powershell-install-configure.md)
* [Azure client Manager delle risorse](https://github.com/projectkudu/ARMClient)
* [Modelli di Azure Manager delle risorse](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)


## <a name="log-sources-that-you-might-want-to-collect"></a>Origini dei log che è possibile raccogliere
- **Servizio tessuti registri**: emesso dalla piattaforma canali individua eventi di Windows (ETW) ed EventSource standard. Registri possono essere uno dei numerosi tipi:
  - Gli eventi operativi: i registri per le operazioni che esegue la piattaforma servizio tessuti. Esempi di creazione di applicazioni e servizi, modifica stato del nodo e informazioni sull'aggiornamento.
  - [Operatori affidabili gli eventi del modello di programmazione](service-fabric-reliable-actors-diagnostics.md)
  - [Servizi affidabili gli eventi del modello di programmazione](service-fabric-reliable-services-diagnostics.md)
- **Gli eventi dell'applicazione**: eventi generati dal codice del servizio e scritto da utilizzando la classe di supporto EventSource fornita nei modelli di Visual Studio. Per ulteriori informazioni su come scrivere i registri dall'applicazione in uso, vedere [Monitor e diagnosticare i servizi di una configurazione di sviluppo di computer locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).


## <a name="deploy-the-diagnostics-extension"></a>Distribuire l'estensione di diagnostica
Il primo passo per la raccolta di log è per distribuire l'estensione di diagnostica su ciascuna delle macchine virtuali cluster servizio tessuti. L'estensione di diagnostica consente di raccogliere i registri in ogni macchina virtuale e li carica in account di archiviazione specificato. La procedura dipendono leggermente se si usa il portale di Azure e gestione di risorse di Azure. La procedura anche varia in base a seconda della distribuzione fa parte di creazione di cluster o per un cluster già esistente. Osserviamo i passaggi per ogni scenario.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-the-portal"></a>Distribuire l'estensione di diagnostica durante la creazione di cluster tramite il portale
Per distribuire l'estensione di diagnostica in macchine virtuali del cluster durante la creazione di cluster, utilizzare il pannello di impostazioni di diagnostica illustrato nell'immagine seguente. Per abilitare la raccolta degli eventi operatori affidabili o servizi affidabili, assicurarsi che diagnostica è impostata **su** (impostazione predefinita). Dopo aver creato il cluster, non è possibile modificare queste impostazioni tramite il portale.

![Impostazioni di diagnostica Azure nel portale per la creazione di cluster](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

Il supporto di *richiede* del team di supporto Azure registri per facilitare la risoluzione di eventuali richieste di assistenza creati. Questi registri vengono memorizzati in uno degli account di archiviazione creati nel gruppo di risorse e sono raccolti in tempo reale. Le impostazioni di diagnostica configurano eventi a livello di applicazione. Questi eventi includono [Gli operatori affidabile](service-fabric-reliable-actors-diagnostics.md) eventi, eventi [Servizi affidabili](service-fabric-reliable-services-diagnostics.md) e alcuni eventi di servizio tessuti a livello di sistema per essere archiviati in archiviazione Azure.

Prodotti, ad esempio [Ricerca flessibile](service-fabric-diagnostic-how-to-use-elasticsearch.md) o un processo personalizzato possono ottenere gli eventi dall'account di archiviazione. Non è attualmente per filtrare o accudire gli eventi che vengono inviati alla tabella. Se non implementare un processo per rimuovere gli eventi della tabella, la tabella continuerà ad aumentare.

Quando si crea un cluster tramite il portale, si consiglia di scaricare il modello *prima di scegliere * *OK** * per creare il cluster. Per informazioni dettagliate, vedere [configurare un cluster di servizio tessuti utilizzando un modello di gestione risorse di Azure](service-fabric-cluster-creation-via-arm.md). È necessario il modello per apportare modifiche in un secondo momento, perché non è possibile apportare alcune modifiche tramite il portale.

È possibile esportare modelli dal portale tramite la procedura seguente. Tuttavia, questi modelli possono essere più difficili da usare perché si potrebbe contenere valori null in cui mancano informazioni richieste.

1. Aprire il gruppo di risorse.
2. Selezionare **le impostazioni** per visualizzare il pannello di impostazioni.
3. Selezionare **distribuzioni** per visualizzare il pannello Cronologia di distribuzione.
4. Selezionare una distribuzione per visualizzare i dettagli della distribuzione.
5. Selezionare **Esporta modello** per visualizzare il pannello modello.
6. Selezionare **Salva file** per esportare un file ZIP che contiene il modello e i file di PowerShell.

Dopo avere esportato i file, è necessario apportare una modifica. Modificare il file parameters.json e rimuovere l'elemento **adminPassword** . In questo modo la richiesta la password durante l'esecuzione di script di distribuzione. Quando si esegue lo script di distribuzione, è necessario risolvere valori di parametro null.

Per utilizzare il modello scaricato per aggiornare una configurazione:

1. Estrarre il contenuto in una cartella nel computer locale.
2. Modificare il contenuto per riflettere la nuova configurazione.
3. Avvio di PowerShell e passare alla cartella in cui è stato estratto il contenuto.
4. Eseguire **deploy.ps1** e compilare l'ID di abbonamento, il nome del gruppo risorse (utilizzare lo stesso nome per aggiornare la configurazione) e un nome univoco distribuzione.


### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Distribuire l'estensione di diagnostica durante la creazione di cluster tramite Gestione risorse di Azure
Per creare un cluster tramite Gestione risorse, è necessario aggiungere la configurazione di diagnostica JSON al modello di gestione risorse cluster completa prima di creare il cluster. Sono disponibili un modello di gestione risorse di esempio macchine Virtuali di cinque cluster con configurazione di diagnostica aggiunta come parte del nostro esempi di modelli di Manager delle risorse. È possibile visualizzarlo nel percorso seguente nella raccolta di esempi di Azure: [cinque cluster con l'esempio di modello Gestione risorse di diagnostica](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad).

Per visualizzare le impostazioni di diagnostica nel modello di Manager delle risorse, aprire il file azuredeploy.json e cercare **IaaSDiagnostics**. Per creare un cluster di questo modello, selezionare il pulsante **Distribuisci in Azure** disponibile facendo clic sul collegamento precedente.

In alternativa, è possibile scaricare l'esempio di Manager delle risorse, apportare le modifiche e creare un cluster con il modello modificato utilizzando il `New-AzureRmResourceGroupDeployment` comando in una finestra di PowerShell Azure. Vedere il seguente codice per i parametri passati al comando. Per informazioni dettagliate su come distribuire un gruppo di risorse tramite PowerShell, vedere l'articolo [distribuire un gruppo di risorse con il modello di gestione risorse di Azure](../resource-group-template-deploy.md).

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>Distribuire l'estensione di diagnostica a un cluster esistente
Se si dispone di un cluster esistente che non dispone di diagnostica distribuita o se si desidera modificare una configurazione esistente, è possibile aggiungere o aggiornare il. Modificare il modello di Manager delle risorse che consentono di creare cluster esistente o scaricare il modello dal portale di come descritto in precedenza. Modificare il file template.json eseguendo le operazioni seguenti.

Aggiungere una nuova risorsa di archiviazione per il modello aggiungendo alla sezione risorse.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Aggiungere quindi alla sezione parametri subito dopo le definizioni di account di archiviazione, tra `supportLogStorageAccountName` e `vmNodeType0Name`. Sostituire il testo segnaposto *nome dell'account di archiviazione qui* con il nome dell'account di archiviazione.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Quindi, aggiornare la `VirtualMachineProfile` sezione del file template.json aggiungendo il codice seguente all'interno della matrice estensioni. Assicurarsi di aggiungere un punto e virgola all'inizio o alla fine, a seconda di dove viene inserito.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Dopo aver modificato il file template.json come descritto, ripubblicare il modello di Manager delle risorse. Se il modello è stato esportato, eseguire il file deploy.ps1 Ripubblica il modello. Dopo aver distribuito, assicurarsi che **ProvisioningState** sia **stata eseguita correttamente**.


## <a name="update-diagnostics-to-collect-and-upload-logs-from-new-eventsource-channels"></a>Aggiornare i test di diagnostica per raccogliere e caricare i registri di nuovo EventSource canali
Per aggiornare la diagnostica per raccogliere i registri da nuovi canali EventSource che rappresentano una nuova applicazione che si sta per distribuire, eseguire le stesse operazioni come [sezione precedente](#deploywadarm) per la configurazione di diagnostica per un cluster esistente.

Aggiornare il `EtwEventSourceProviderConfiguration` sezione nel file template.json per aggiungere le voci per aggiornare i canali EventSource nuovi prima di applicare la configurazione utilizzando il `New-AzureRmResourceGroupDeployment` comando PowerShell. Il nome dell'origine eventi è definito come parte del codice nel file ServiceEventSource.cs generata Visual Studio.

Ad esempio, se l'origine di evento è denominata Eventsource personale, aggiungere il codice riportato di seguito per inserire gli eventi da Eventsource personali in una tabella denominata MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Per raccogliere contatori delle prestazioni o i registri eventi, modificare il modello di gestione risorse utilizzando gli esempi forniti in [Crea una macchina virtuale di Windows con il monitoraggio e diagnostica utilizzando un modello di gestione risorse di Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md). Ripubblicare il modello di Manager delle risorse.

## <a name="next-steps"></a>Passaggi successivi
Per comprendere in modo più dettagliato quali eventi avrà un aspetto la risoluzione dei problemi, vedere gli eventi di diagnostica visualizzati per [Gli operatori affidabile](service-fabric-reliable-actors-diagnostics.md) e [Servizi affidabili](service-fabric-reliable-services-diagnostics.md).


## <a name="related-articles"></a>Articoli correlati
* [Informazioni su come raccogliere contatori o ai registri tramite l'estensione di diagnostica](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)
* [Soluzione di servizio tessuti in Analitica Log](../log-analytics/log-analytics-service-fabric.md)
