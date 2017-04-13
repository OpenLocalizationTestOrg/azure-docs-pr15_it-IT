<properties
    pageTitle="Usare PowerShell per gestire le risorse Bus di servizio e hub evento | Microsoft Azure"
    description="Uso di PowerShell per creare e gestire le risorse Bus di servizio e gli hub di evento"
    services="service-bus,event-hubs"
    documentationCenter=".NET"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="use-powershell-to-manage-service-bus-and-event-hubs-resources"></a>Usare PowerShell per gestire le risorse Bus di servizio e gli hub di evento

Microsoft Azure PowerShell è un ambiente di script che è possibile utilizzare per controllare e automatizzare la distribuzione e gestione dei servizi Azure. In questo articolo descrive come usare PowerShell per eseguire il provisioning e gestire le entità Bus di servizio, ad esempio gli spazi dei nomi, code e hub evento una console di Azure PowerShell locale.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario disporre le operazioni seguenti:

- Un abbonamento Azure. Azure è una piattaforma basata su abbonamento. Per ulteriori informazioni su come ottenere una sottoscrizione, vedere [le opzioni di acquisto][], [membro offre][]o [account gratuito][].

- Un computer con PowerShell Azure. Per istruzioni, vedere [installare e configurare Azure PowerShell][].

- Conoscenza generale di script di PowerShell, NuGet pacchetti e .NET Framework.

## <a name="include-a-reference-to-the-net-assembly-for-service-bus"></a>Includere un riferimento all'assembly .NET per Bus di servizio

Sono disponibili un numero limitato di cmdlet di PowerShell per la gestione dei Bus di servizio. Per eseguire il provisioning entità che non viene esposto tramite i cmdlet esistenti, è possibile utilizzare il client .NET per Bus di servizio all'interno di PowerShell facendo riferimento il [pacchetto NuGet Bus di servizio].

Assicurarsi che lo script possibile individuare l'assembly **Microsoft.ServiceBus.dll** , che viene installato con il pacchetto NuGet. Per essere flessibili, lo script esegue le operazioni seguenti:

1. Determina il percorso da cui è stato richiamato.
2. Attraversa il percorso finché rileva una cartella denominata `packages`. Questa cartella viene creata durante l'installazione di pacchetti NuGet.
3. Le ricerche in modo ricorsivo il `packages` cartella per un assembly denominato **Microsoft.ServiceBus.dll**.
4. Riferimento all'assembly, in modo che i tipi sono disponibili per successivi utilizzi.

Ecco come questa procedura è implementata in uno script di PowerShell:

```powershell

try
{
    # IMPORTANT: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}

```

## <a name="provision-a-service-bus-namespace"></a>Effettuare il provisioning di uno spazio dei nomi Bus di servizio

Quando si lavora con spazi dei nomi Bus di servizio, esistono due cmdlet è possibile utilizzare invece di .NET SDK: [Get-AzureSBNamespace][] e [AzureSBNamespace di nuovo][].

In questo esempio crea alcune variabili locali in script. `$Namespace` and `$Location`.

- `$Namespace`è il nome di spazio dei nomi Bus di servizio con cui si desidera utilizzare.
- `$Location`Identifica il centro di dati in cui è provisioning lo spazio dei nomi.
- `$CurrentNamespace`Archivia lo spazio dei nomi di riferimento che si recuperare (o creare).

In uno script effettivo `$Namespace` e `$Location` possono essere passati come parametri.

Questa parte dello script esegue le operazioni seguenti:

1. Tentativo di recuperare uno spazio dei nomi di servizio Bus con il nome specificato.
2. Se si trova lo spazio dei nomi, viene segnalato che cos'è stato trovato.
3. Se lo spazio dei nomi non viene trovato, crea lo spazio dei nomi e quindi recupera lo spazio dei nomi appena creato.

    ``` powershell

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
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```
Per eseguire il provisioning altre entità Bus di servizio, creare un'istanza di `NamespaceManager` oggetto da SDK. È possibile utilizzare il cmdlet [Get-AzureSBAuthorizationRule][] per recuperare una regola di autorizzazione viene utilizzata per fornire una stringa di connessione. In questo esempio viene archiviato un riferimento di `NamespaceManager` istanza nel `$NamespaceManager` variabile. Lo script in un secondo momento utilizza `$NamespaceManager` effettuare il provisioning di altre entità.

    ``` powershell
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    # Create the NamespaceManager object to create the Event Hub
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
    ```

## <a name="provisioning-other-service-bus-entities"></a>Il provisioning di altre entità Bus di servizio

Per eseguire il provisioning altre entità, ad esempio code, argomenti e hub di evento, è possibile utilizzare l' [API .NET per Bus di servizio][]. Esempi più dettagliati, incluse altre entità, vengono fatto riferimento alla fine di questo articolo.

### <a name="create-an-event-hub"></a>Creare un Hub di evento

Questa parte dello script crea quattro altre variabili locali. Queste variabili vengono utilizzate per creare un'istanza di un `EventHubDescription` oggetto. Lo script esegue le operazioni seguenti:

1. Utilizzo di `NamespaceManager` oggetto, verificare se l'Hub di evento identificate da `$Path` esiste.
2. Se non esiste, creare un `EventHubDescription` e passarlo alla `NamespaceManager` classe `CreateEventHubIfNotExists` metodo.
3. Dopo aver stabilito che l'Hub di evento è disponibile, creare un gruppo di consumer tramite `ConsumerGroupDescription` e `NamespaceManager`.

    ``` powershell

    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"

    # Check if the Event Hub already exists
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

### <a name="create-a-queue"></a>Creare una coda

Per creare una coda o un argomento, eseguire un controllo dello spazio dei nomi come sezione precedente.

    if ($NamespaceManager.QueueExists($Path))
    {
        Write-Output "The [$Path] queue already exists in the [$Namespace] namespace."
    }
    else
    {
        Write-Output "Creating the [$Path] queue in the [$Namespace] namespace..."
        $QueueDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.QueueDescription -ArgumentList $Path
        if ($AutoDeleteOnIdle -ge 5)
        {
            $QueueDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
        }
        if ($DefaultMessageTimeToLive -gt 0)
        {
            $QueueDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
        }
        if ($DuplicateDetectionHistoryTimeWindow -gt 0)
        {
            $QueueDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
        }
        $QueueDescription.EnableBatchedOperations = $EnableBatchedOperations
        $QueueDescription.EnableDeadLetteringOnMessageExpiration = $EnableDeadLetteringOnMessageExpiration
        $QueueDescription.EnableExpress = $EnableExpress
        $QueueDescription.EnablePartitioning = $EnablePartitioning
        $QueueDescription.ForwardDeadLetteredMessagesTo = $ForwardDeadLetteredMessagesTo
        $QueueDescription.ForwardTo = $ForwardTo
        $QueueDescription.IsAnonymousAccessible = $IsAnonymousAccessible
        if ($LockDuration -gt 0)
        {
            $QueueDescription.LockDuration = [System.TimeSpan]::FromSeconds($LockDuration)
        }
        $QueueDescription.MaxDeliveryCount = $MaxDeliveryCount
        $QueueDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
        $QueueDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
        $QueueDescription.RequiresSession = $RequiresSession
        if ($EnablePartitioning)
        {
            $QueueDescription.SupportOrdering = $False
        }
        else
        {
            $QueueDescription.SupportOrdering = $SupportOrdering
        }
        $QueueDescription.UserMetadata = $UserMetadata
        $NamespaceManager.CreateQueue($QueueDescription);
    }

### <a name="create-a-topic"></a>Creare un argomento

    if ($NamespaceManager.TopicExists($Path))
    {
        Write-Output "The [$Path] topic already exists in the [$Namespace] namespace."
    }
    else
    {
        Write-Output "Creating the [$Path] topic in the [$Namespace] namespace..."
        $TopicDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.TopicDescription -ArgumentList $Path
        if ($AutoDeleteOnIdle -ge 5)
        {
            $TopicDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
        }
        if ($DefaultMessageTimeToLive -gt 0)
        {
            $TopicDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
        }
        if ($DuplicateDetectionHistoryTimeWindow -gt 0)
        {
            $TopicDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
        }
        $TopicDescription.EnableBatchedOperations = $EnableBatchedOperations
        $TopicDescription.EnableExpress = $EnableExpress
        $TopicDescription.EnableFilteringMessagesBeforePublishing = $EnableFilteringMessagesBeforePublishing
        $TopicDescription.EnablePartitioning = $EnablePartitioning
        $TopicDescription.IsAnonymousAccessible = $IsAnonymousAccessible
        $TopicDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
        $TopicDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
        if ($EnablePartitioning)
        {
            $TopicDescription.SupportOrdering = $False
        }
        else
        {
            $TopicDescription.SupportOrdering = $SupportOrdering
        }
        $TopicDescription.UserMetadata = $UserMetadata
        $NamespaceManager.CreateTopic($TopicDescription);
    }

## <a name="next-steps"></a>Passaggi successivi

In questo articolo fornita con un flusso di lavoro di base per il provisioning di entità Bus di servizio tramite PowerShell. Sebbene esistano un numero limitato di cmdlet di PowerShell per la gestione dei Bus di servizio di messaggistica entità, facendo riferimento all'assembly Microsoft.ServiceBus.dll, praticamente qualsiasi operazione è possibile eseguire con le raccolte di client .NET che è inoltre possibile eseguire in uno script di PowerShell.

Ulteriori esempi sono disponibili in questi post di blog:

- [Come creare code, argomenti e le sottoscrizioni che utilizzano uno script di PowerShell Bus di servizio](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Come creare un Namespace Bus di servizio e un Hub di evento utilizzando uno script di PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Alcuni script predefiniti sono inoltre disponibili per il download:

- [Script di PowerShell Bus Service](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[le opzioni di acquisto]: http://azure.microsoft.com/pricing/purchase-options/
[membro offerte]: http://azure.microsoft.com/pricing/member-offers/
[account gratuito]: http://azure.microsoft.com/pricing/free-trial/
[Pacchetto NuGet Bus di servizio]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[Nuovo AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API di .NET per Bus di servizio]: https://msdn.microsoft.com/en-us/library/azure/mt419900.aspx
[Installare e configurare PowerShell Azure]: ../powershell-install-configure.md
