<properties 
    pageTitle="Script di PowerShell per creare una risorsa applicazione approfondimenti" 
    description="Automatizzare la creazione di risorse approfondimenti applicazione." 
    services="application-insights" 
    documentationCenter="windows"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/19/2016" 
    ms.author="awills"/>

#  <a name="powershell-script-to-create-an-application-insights-resource"></a>Script di PowerShell per creare una risorsa applicazione approfondimenti

*Informazioni dettagliate sui applicazione è in anteprima.*

Quando si desidera eseguire il monitoraggio una nuova applicazione, o una nuova versione di un'applicazione, con [Informazioni dettagliate sui applicazione di Visual Studio](https://azure.microsoft.com/services/application-insights/), impostare una nuova risorsa in Microsoft Azure. Questa risorsa è nel punto in cui i dati di telemetria dell'App sono analizzati e visualizzati. 

È possibile automatizzare la creazione di una nuova risorsa tramite PowerShell.

Ad esempio, se si sviluppa un'app per dispositivi mobili, è probabile che, in qualsiasi momento, vi sono diverse versioni pubblicate dell'applicazione in uso per i clienti. Non si desidera ottenere i risultati di telemetria da diverse versioni ordine corretto. Viene visualizzato in modo che il processo di compilazione per creare una nuova risorsa per ogni compilazione.

## <a name="script-to-create-an-application-insights-resource"></a>Script per creare una risorsa applicazione approfondimenti

Vedere le specifiche pertinenti cmdlet:

* [Nuovo AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [Nuovo AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)


*Script di PowerShell*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Add-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 
# - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource

$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} `
  -ResourceType "Microsoft.Insights/Components" `
  -Location "Central US" `
  -PropertyObject @{"Type"="ASP.NET"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


#Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Cosa fare con il iKey

Ogni risorsa viene identificata in base alla chiave strumentazione (iKey). Il iKey è l'output dello script di creazione delle risorse. Script di compilazione dovrebbe fornire che iKey a SDK approfondimenti applicazione incorporato nell'app.

Esistono due modi per rendere disponibile il iKey a SDK:
  
* In [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
 * `<instrumentationkey>`*iKey*`</instrumentationkey>`
* O nel [codice di inizializzazione](app-insights-api-custom-events-metrics.md): 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`



## <a name="see-also"></a>Vedere anche

* [Creare approfondimenti applicazione e risorse di test web dai modelli](app-insights-powershell.md)
* [Configurare il monitoraggio di diagnostica Windows Azure con PowerShell](app-insights-powershell-azure-diagnostics.md) 
* [Impostare avvisi tramite PowerShell](app-insights-powershell-alerts.md)

 