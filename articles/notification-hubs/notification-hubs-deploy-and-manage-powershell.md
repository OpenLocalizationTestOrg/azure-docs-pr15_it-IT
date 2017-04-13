<properties 
    pageTitle="Distribuire e gestire gli hub notifica tramite PowerShell" 
    description="Come creare e gestire gli hub di notifica tramite PowerShell per l'automazione" 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="powershell" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Distribuire e gestire gli hub notifica tramite PowerShell

##<a name="overview"></a>Panoramica

In questo articolo viene illustrato come utilizzare creare e gestire Azure notifica hub tramite PowerShell. In questo argomento vengono visualizzate le seguenti operazioni di automazione.

+ Creare un Hub di notifica
+ Impostare le credenziali

Se è necessario creare un nuovo servizio bus spazio dei nomi per l'hub di notifica, vedere [Gestire Bus di servizio con PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Gestire le notifiche hub non è supportata direttamente dagli cmdlet inclusi con PowerShell Azure. L'approccio ottimale da PowerShell consiste nel fare riferimento all'assembly Microsoft.Azure.NotificationHubs.dll. L'assembly viene distribuito con il [pacchetto di Microsoft Azure notifica hub NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questo articolo, è necessario disporre le operazioni seguenti:

- Un abbonamento Azure. Azure è una piattaforma basata su abbonamento. Per ulteriori informazioni su come ottenere una sottoscrizione, vedere [Le opzioni di acquisto], [Membro offre]o [Versione di valutazione gratuita].

- Un computer con PowerShell Azure. Per istruzioni, vedere [installare e configurare Azure PowerShell].

- Conoscenza generale di script di PowerShell, NuGet pacchetti e .NET Framework.


## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Aggiunta di un riferimento all'assembly .NET per Bus di servizio

Gestione di Azure notifica hub non è ancora inclusa con i cmdlet di PowerShell in Azure PowerShell. Per eseguire il provisioning hub di notifica, è possibile utilizzare il client .NET nel [pacchetto di Microsoft Azure notifica hub NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Prima di tutto, verificare che lo script consente di individuare l'assembly **Microsoft.Azure.NotificationHubs.dll** , che viene installato con un pacchetto NuGet in un progetto di Visual Studio. Per essere flessibili, lo script esegue le operazioni seguenti:

1. Determina il percorso in cui è stato richiamato.
2. Attraversa il percorso finché rileva una cartella denominata `packages`. Questa cartella viene creata durante l'installazione di pacchetti NuGet per i progetti di Visual Studio.
3. Le ricerche in modo ricorsivo il `packages` cartella per un assembly denominato **Microsoft.Azure.NotificationHubs.dll**.
4. Riferimento all'assembly, in modo che i tipi sono disponibili per successivi utilizzi.

Ecco come questa procedura è implementata in uno script di PowerShell:

``` powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Creare la classe NamespaceManager

Per eseguire il provisioning hub di notifica, creare un'istanza della classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) da SDK. 

È possibile utilizzare il cmdlet [Get-AzureSBAuthorizationRule] incluso con Azure PowerShell per recuperare una regola di autorizzazione viene utilizzata per fornire una stringa di connessione. È possibile archiviare un riferimento alla `NamespaceManager` istanza nel `$NamespaceManager` variabile. Verrà usata `$NamespaceManager` effettuare il provisioning di un hub di notifica.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## <a name="provisioning-a-new-notification-hub"></a>Il provisioning di un nuovo Hub di notifica 

Per eseguire il provisioning di un nuovo hub di notifica, utilizzare l' [API .NET per gli hub di notifica].

In questa parte dello script impostare quattro variabili locali. 

1. `$Namespace`: Impostare il nome dello spazio dei nomi nel punto in cui si desidera creare un hub di notifica.
2. `$Path`: Impostare questo percorso per il nome del nuovo hub di notifica.  Ad esempio, "MyHub".    
3. `$WnsPackageSid`: Questa impostazione per il pacchetto SID per Windows App nella pagina del [Dev Center di Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).
4. `$WnsSecretkey`: Impostare la chiave privata per l'App di Windows nella pagina del [Dev Center di Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).

Queste variabili vengono utilizzate per la connessione allo spazio dei nomi e creare un nuovo Hub di notifica configurato per gestire le notifiche di servizi di notifica di Windows (WNS) con le credenziali WNS per un'App di Windows. Per informazioni su come ottenere il pacchetto SID e la chiave segreta visualizzato, l'esercitazione [Introduzione a hub di notifica](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) . 

+ Il frammento di script utilizza la `NamespaceManager` oggetto per verificare se l'Hub di notifica identificate da `$Path` esistente.

+ Se non esiste, lo script creerà un `NotificationHubDescription` con WNS delle credenziali e passarlo alla `NamespaceManager` classe `CreateNotificationHub` metodo.

``` powershell

$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```




## <a name="additional-resources"></a>Risorse aggiuntive

- [Gestire Bus di servizio con PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [Come creare code, argomenti e le sottoscrizioni che utilizzano uno script di PowerShell Bus di servizio](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Come creare un Namespace Bus di servizio e un Hub di evento utilizzando uno script di PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Alcuni script predefiniti sono inoltre disponibili per il download:
- [Script di PowerShell Bus Service](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)
 

[Le opzioni di acquisto]: http://azure.microsoft.com/pricing/purchase-options/
[Membro offerte]: http://azure.microsoft.com/pricing/member-offers/
[Versione di valutazione gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Installare e configurare PowerShell Azure]: ../powershell-install-configure.md
[API di .NET per gli hub di notifica]: https://msdn.microsoft.com/library/azure/mt414893.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
 
