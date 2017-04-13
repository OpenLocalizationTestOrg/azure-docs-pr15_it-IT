<properties
   pageTitle="Configurare PowerShell per creare una macchina virtuale per il Marketplace | Microsoft Azure"
   description="Istruzioni per la configurazione di PowerShell Azure e usarla come un processo facoltativo flusso per creare macchine Virtuali immagini per distribuire e vendono su Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/04/2016"
   ms.author="hascipio"/>

# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Configurare Azure PowerShell per creare un'offerta per Azure Marketplace
Per informazioni dettagliate su come configurare PowerShell in Azure, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md). Un approccio semplice consiste nell'usare il metodo di certificato, download e Importa un certificato necessario per l'autenticazione. Per ottenere il certificato necessario, utilizzare il cmdlet **Get-AzurePublishSettingsFile** . Quando viene richiesto, salvare il file. Per importare il certificato in una sessione di PowerShell, utilizzare il cmdlet **AzurePublishSettingsFile importazione** .

Per configurare e archiviare le impostazioni di sottoscrizione Microsoft Azure comuni per la sessione di PowerShell, utilizzare i cmdlet **Set-AzureSubscription** e **AzureSubscription selezionare** :

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

Il primo comando associa un account di archiviazione predefinito con l'abbonamento (è necessario per alcune operazioni di provisioning macchine Virtuali).  Il secondo rende l'abbonamento a quella corrente (riconosciuto da altri cmdlet).

## <a name="see-also"></a>Vedere anche
- [Guida introduttiva: come pubblicare un'offerta di Azure Marketplace](marketplace-publishing-getting-started.md)
- [Creazione di un'immagine di macchina virtuale per il Marketplace](marketplace-publishing-vm-image-creation.md)
