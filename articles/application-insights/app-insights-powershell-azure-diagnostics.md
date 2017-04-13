<properties
    pageTitle="Utilizzo di PowerShell per la configurazione dell'applicazione approfondimenti in un Azure | Microsoft Azure"
    description="Automatizzare la configurazione di diagnostica Azure barra verticale a informazioni dettagliate sui applicazione."
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/17/2015"
    ms.author="awills"/>

# <a name="using-powershell-to-set-up-application-insights-for-an-azure-web-app"></a>Uso di PowerShell per configurare applicazione approfondimenti per un'app web Azure

[Microsoft Azure](https://azure.com) può essere [configurato per l'invio di Azure diagnostica](app-insights-azure-diagnostics.md) per [Informazioni dettagliate sui applicazione di Visual Studio](app-insights-overview.md). I test di diagnostica si riferiscono ai servizi Cloud Windows Azure e macchine virtuali di Azure. Complementari telemetria inviati dall'interno dell'app utilizzando SDK approfondimenti applicazione. Come parte di automatizzare il processo di creazione di nuove risorse in Azure, è possibile configurare diagnostica tramite PowerShell.

## <a name="azure-template"></a>Modello di Azure

Se l'app web viene eseguita in Azure e si creano le risorse utilizzando un modello di gestione risorse di Azure, è possibile configurare approfondimenti applicazione aggiungendo questo nodo risorse:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource`-un nome per la risorsa applicazione approfondimenti
* `myWebAppName`-l'id dell'applicazione web


## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Attivare l'estensione del diagnostica come parte della distribuzione di un servizio Cloud

Il `New-AzureDeployment` cmdlet dispone di un parametro `ExtensionConfiguration`, che utilizza una matrice di configurazioni di diagnostica. Questi possono essere creati mediante la `New-AzureServiceDiagnosticsExtensionConfig` cmdlet. Per esempio:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Attivare l'estensione del diagnostica in un servizio Cloud esistente

In un servizio esistente, utilizzare `Set-AzureServiceDiagnosticsExtension`.

```ps
 
    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>Ottenere la configurazione di estensione diagnostica corrente

```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>Rimuovere l'estensione di diagnostica

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Se è abilitata l'estensione di diagnostica utilizzando `Set-AzureServiceDiagnosticsExtension` o `New-AzureServiceDiagnosticsExtensionConfig` senza il parametro ruolo, quindi è possibile rimuovere l'estensione utilizzando `Remove-AzureServiceDiagnosticsExtension` senza il parametro ruolo. Se il parametro ruolo è stato utilizzato quando si abilita l'estensione quindi necessario anche utilizzarlo quando si rimuove l'estensione.

Per rimuovere l'estensione di diagnostica da ogni ruolo singoli:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>Vedere anche

* [Eseguire il monitoraggio Azure Cloud Services App con informazioni dettagliate sui applicazione](app-insights-cloudservices.md)
* [Inviare diagnostica Windows Azure a informazioni dettagliate sui applicazione](app-insights-azure-diagnostics.md)
* [Automatizzare la configurazione di avvisi](app-insights-powershell-alerts.md)

