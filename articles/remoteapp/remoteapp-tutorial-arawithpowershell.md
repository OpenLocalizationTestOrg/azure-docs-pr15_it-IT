<properties
   pageTitle="Utilizzare i cmdlet di PowerShell con Azure RemoteApp | Microsoft Azure"
   description="Informazioni su come utilizzare i cmdlet di Windows PowerShell in Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>



# <a name="use-windows-powershell-cmdlets-with-azure-remoteapp"></a>Utilizzare i cmdlet di Windows PowerShell con RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

 È possibile utilizzare i cmdlet di RemoteApp PowerShell Azure per amministrare e gestire le raccolte. Usare le informazioni seguenti per iniziare.

## <a name="get-the-cmdlets"></a>Ottenere i cmdlet 
-------------
Scaricare i cmdlet di Powershell Azure [qui](http://go.microsoft.com/?linkid=9811175)RemoteApp cmdlet sono incluse al suo interno. 

Consultare la [Guida cmdlet RemoteApp di Azure](https://msdn.microsoft.com/library/mt428031.aspx).

## <a name="configure-azure-cmdlets-to-use-your-subscription"></a>Configurare i cmdlet di Azure per usare l'abbonamento
------------------
Seguire [questa guida](../powershell-install-configure.md) è possibile utilizzare i cmdlet con l'abbonamento Azure.

Per iniziare rapidamente, è possibile utilizzare la procedura seguente:

1.  Scaricare e installare i [cmdlet di PowerShell Azure](http://go.microsoft.com/?linkid=9811175).
2.  Avviare Microsoft Azure PowerShell.
3.  Eseguire **Aggiungi AzureAccount** per eseguire l'autenticazione all'abbonamento Azure. Quando richiesto, immettere il nome utente e password utilizzati per accedere al portale Azure.  
4.  Eseguire **Get-AzureSubscription** per elencare le sottoscrizioni associate al proprio account utente. 
5.  Eseguire **Selezionare AzureSubscription** e specificare il nome dell'abbonamento o l'ID da utilizzare nella console di PowerShell.

Congratulazioni, la console di Azure PowerShell sia configurato e pronto per l'utilizzo. Tenere presente che è necessario ripetere i passaggi da 2 a 5 ogni volta che si avvia la console di Azure PowerShell.  

## <a name="create-a-cloud-collection"></a>Creare una raccolta di cloud
--------------------
È semplice, eseguire il comando seguente:

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

Il comando Pubblica automaticamente le applicazioni di Microsoft Office 365 (Excel, OneNote, Outlook, PowerPoint, Visio e Word).

La creazione della raccolta potrebbe richiedere 30 minuti o più tempo per il completamento. Pertanto, questo comando restituisce un ID di registrazione che è possibile usare come indicato di seguito:


    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

Al termine della raccolta, è possibile aggiungere gli utenti alla raccolta con il comando seguente:

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

E fatto! L'utente dovrebbe essere possibile connettersi all'applicazione tramite il client di Azure RemoteApp disponibile [qui](https://www.remoteapp.windowsazure.com/).

## <a name="available-cmdlets"></a>Cmdlet disponibili
Sono disponibili numerosi altri comandi sufficiente, la documentazione sarà presto subito:

Cmdlet per la raccolta RemoteApp base: 

- Nuovo AzureRemoteAppCollection
- Get-AzureRemoteAppCollection
- Set AzureRemoteAppCollection
- Aggiornamento AzureRemoteAppCollection
- Rimuovi AzureRemoteAppCollection
- Aggiungere AzureRemoteAppUser
- Get-AzureRemoteAppUser
- Rimuovi AzureRemoteAppUser
- Get-AzureRemoteAppSession
- Disconnettere AzureRemoteAppSession
- Richiamare AzureRemoteAppSessionLogoff
- Invia AzureRemoteAppSessionMessage
- Get-AzureRemoteAppProgram
- Get-AzureRemoteAppStartMenuProgram
- Pubblicare AzureRemoteAppProgram
- Annullare la pubblicazione AzureRemoteAppProgram
- Get-AzureRemoteAppCollectionUsageDetails
- Get-AzureRemoteAppCollectionUsageSummary
- Get-AzureRemoteAppPlan

Cmdlet per la rete virtuale RemoteApp:

- Nuovo AzureRemoteAppVNet
- Get-AzureRemoteAppVNet
- Set AzureRemoteAppVNet
- Rimuovi AzureRemoteAppVNet
- Get-AzureRemoteAppVpnDevice
- Get - AzureRemoteAppVpnDeviceConfigScript
- Reimposta AzureRemoteAppVpnSharedKey

Immagine di modello RemoteApp i cmdlet:

- Nuovo AzureRemoteAppTemplateImage
- Get-AzureRemoteAppTemplateImage
- Rinomina AzureRemoteAppTemplateImage
- Rimuovi AzureRemoteAppTemplateImage

Altri cmdlet RemoteApp:

- Get-AzureRemoteAppLocation
- Get-AzureRemoteAppWorkspace
- Set AzureRemoteAppWorkspace
- Get-AzureRemoteAppOperationResult
 
