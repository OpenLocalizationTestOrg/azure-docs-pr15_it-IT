<properties
    pageTitle="Gestire Bus di servizio con PowerShell | Microsoft Azure"
    description="Gestire Bus di servizio con gli script di PowerShell"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="sethm"/>

# <a name="manage-service-bus-with-powershell"></a>Gestire Bus di servizio con PowerShell

## <a name="overview"></a>Panoramica

Microsoft Azure PowerShell è un ambiente di script che è possibile utilizzare per controllare e automatizzare la distribuzione e gestione dei carichi di lavoro di Azure. In questo articolo descrive come usare PowerShell per eseguire il provisioning e gestire le entità Bus di servizio, ad esempio gli spazi dei nomi, code e hub evento una console di Azure PowerShell locale.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questo articolo, è necessario disporre i prerequisiti seguenti:

- Un abbonamento Azure. Azure è una piattaforma basata su abbonamento. Per ulteriori informazioni su come ottenere una sottoscrizione, vedere [Le opzioni di acquisto][], [Membro offre][]o [Versione di valutazione gratuita][].

- Un computer con PowerShell Azure. Per istruzioni, vedere [installare e configurare Azure PowerShell][].

- Conoscenza generale di script di PowerShell, NuGet pacchetti e .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Aggiunta di un riferimento all'assembly .NET per Bus di servizio

Sono disponibili un numero limitato di cmdlet di PowerShell per la gestione dei Bus di servizio. Per eseguire il provisioning entità che non viene esposto tramite i cmdlet esistenti, è possibile utilizzare il client .NET per Bus di servizio nel [pacchetto NuGet Bus di servizio][].

Assicurarsi che lo script possibile individuare l'assembly **Microsoft.ServiceBus.dll** , che viene installato con il pacchetto NuGet. Per essere flessibili, lo script esegue le operazioni seguenti:

1. Determina il percorso in cui è stato richiamato.
2. Attraversa il percorso finché rileva una cartella denominata `packages`. Questa cartella viene creata durante l'installazione di pacchetti NuGet.
3. Le ricerche in modo ricorsivo il `packages` cartella per un assembly denominato **Microsoft.ServiceBus.dll**.
4. Riferimento all'assembly, in modo che i tipi sono disponibili per successivi utilizzi.

Ecco come questa procedura è implementata in uno script di PowerShell:

```
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error "Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script."
}
```

## <a name="provision-a-service-bus-namespace"></a>Effettuare il provisioning di uno spazio dei nomi Bus di servizio

Due dei cmdlet di PowerShell supportano le operazioni di spazio dei nomi Bus di servizio. Invece di API SDK .NET, è possibile utilizzare [Get-AzureSBNamespace][] e [AzureSBNamespace di nuovo][].

In questo esempio crea alcune variabili locali in script. `$Namespace` and `$Location`.

- `$Namespace`è il nome dello spazio dei nomi Bus di servizio con cui si desidera utilizzare.
- `$Location`Identifica il centro di dati in cui lo script disposizioni lo spazio dei nomi.
- `$CurrentNamespace`Archivia lo spazio dei nomi di riferimento che lo script recupera (o crea).

In uno script effettivo `$Namespace` e `$Location` possono essere passati come parametri.

Questa parte dello script esegue le operazioni seguenti:

1. Tentativo di recuperare uno spazio dei nomi di servizio Bus con il nome specificato.
2. Se si trova lo spazio dei nomi, viene segnalato che cos'è stato trovato.
3. Se lo spazio dei nomi non viene trovato, crea lo spazio dei nomi e quindi recupera lo spazio dei nomi appena creato.

    ```
    $Namespace = "MyServiceBusNS"
    $Location = "West US"
    
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
    
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
    }
    else
    {
        Write-Host "The [$Namespace] namespace does not exist."
        Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```

Per eseguire il provisioning altre entità Bus di servizio, creare un'istanza della classe [NamespaceManager][] da SDK.
È possibile utilizzare il cmdlet [Get-AzureSBAuthorizationRule][] per recuperare una regola di autorizzazione viene utilizzata per fornire una stringa di connessione. È possibile archiviare un riferimento alla `NamespaceManager` istanza nel `$NamespaceManager` variabile. Verrà usata `$NamespaceManager` più avanti in script per effettuare il provisioning di altre entità.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-other-service-bus-entities"></a>Il provisioning di altre entità Bus di servizio

Per effettuare il provisioning di altre entità, ad esempio code, argomenti e hub di evento, utilizzare l' [API .NET per Bus di servizio][]. Questo articolo è incentrato solo su hub di evento, ma la procedura per altre entità è simile. Inoltre, esempi più dettagliati, incluse altre entità, vengono fatto riferimento alla fine di questo articolo.

Questa parte dello script crea quattro altre variabili locali. Queste variabili vengono utilizzate per creare un'istanza di un `EventHubDescription` oggetto. Lo script esegue le operazioni seguenti:

1. Utilizzo di `NamespaceManager` oggetto, verificare se l'Hub di evento identificate da `$Path` esiste.
2. Se non esiste, creare un `EventHubDescription` e passarlo alla `NamespaceManager` classe `CreateEventHubIfNotExists` metodo.
3. Dopo aver stabilito che l'Hub di evento è disponibile, creare un gruppo di consumer tramite `ConsumerGroupDescription` e `NamespaceManager`.

    ```
    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"
        
    # Check to see if the Event Hub already exists
    if ($NamespaceManager.EventHubExists($Path))
    {
        Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
        $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
        $EventHubDescription.PartitionCount = $PartitionCount
        $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
        $EventHubDescription.UserMetadata = $UserMetadata
        $EventHubDescription.Path = $Path
        $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
        Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
    }
        
    # Create the consumer group if it doesn't exist
    Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
    $ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
    $ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
    $NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
    Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
    ```

## <a name="migrate-a-namespace-to-another-azure-subscription"></a>Eseguire la migrazione di uno spazio dei nomi in un'altra sottoscrizione Azure

La sequenza di comandi seguente sposta uno spazio dei nomi da una sottoscrizione di Azure a altra. Per eseguire questa operazione, lo spazio dei nomi deve essere già attiva e l'utente che esegue i comandi di PowerShell deve essere un amministratore di origine e destinazione abbonamenti.

```
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo fornito una struttura di base per il provisioning di entità Bus di servizio tramite PowerShell. Qualsiasi elemento che è possibile eseguire utilizzando le raccolte di client .NET, è possibile anche eseguire in uno script di PowerShell.

Sono disponibili ulteriori esempi su tali post di blog:

- [Come creare code, argomenti e le sottoscrizioni che utilizzano uno script di PowerShell Bus di servizio](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Come creare un Namespace Bus di servizio e un Hub di evento utilizzando uno script di PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Alcuni script predefiniti sono inoltre disponibili per il download:
- [Script di PowerShell Bus Service](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[Le opzioni di acquisto]: http://azure.microsoft.com/pricing/purchase-options/
[Membro offerte]: http://azure.microsoft.com/pricing/member-offers/
[Versione di valutazione gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Installare e configurare PowerShell Azure]: ../powershell-install-configure.md
[Pacchetto NuGet Bus di servizio]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[Nuovo AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API di .NET per Bus di servizio]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.aspx
[NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
