
<properties
    pageTitle="Risolvere i problemi di raccolte cloud RemoteApp - creazione | Microsoft Azure"
    description="Informazioni su come risolvere i problemi di errori di creazione di RemoteApp cloud insieme"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="troubleshoot-creating-remoteapp-cloud-collections"></a>Risolvere i problemi di creazione RemoteApp cloud raccolte

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Se si sono verificati problemi durante la creazione di un insieme di cloud, vedere le informazioni seguenti.

## <a name="your-image-is-invalid"></a>L'immagine non è valido ##
Se viene visualizzato un messaggio come "GoldImageInvalid" quando si è in attesa per Azure effettuare il provisioning della raccolta, significa che l'immagine di modello non soddisfa [definito requisiti immagine](remoteapp-imagereqs.md). Accedere e quindi leggere tali [requisiti](remoteapp-imagereqs.md), correggere l'immagine e provare a creare la raccolta.

## <a name="common-errors-seen-in-the-azure-management-portal"></a>Errori comuni nel portale di gestione di Azure

    DNS server could not be reached
    ProvisioningTimeout

Cloud raccolte spesso errori durante la creazione a causa di si usano immagini personalizzate.  Se viene visualizzato uno degli errori sopra e si usa un'immagine personalizzata per creare la raccolta, verificare quanto segue:

- Assicurarsi che l'immagine personalizzata caricata soddisfi i requisiti di immagine.
- In genere il problema comune è che l'immagine non è stato correttamente preparata con Sysprep.  
- Verificare l'immagine è possibile eseguire l'avvio all'interno di Hyper-V o provare a creare un VM IAAS direttamente nell'abbonamento Azure utilizzando l'immagine. Se la macchina virtuale non riesce a eseguire l'avvio e non inizino, quindi in genere indica che l'immagine personalizzata non è stato preparato correttamente.  Verificare l'immagine personalizzata è stato creato seguendo come creare un'immagine di modello personalizzato per RemoteApp

Se si utilizza una delle immagini Microsoft incluse con l'abbonamento, provare a creare la raccolta. Se il problema persiste, contattare il supporto Microsoft.

    PlatformImageTrialModeOnly

Se viene visualizzato questo errore significa che è stato aggiornato a un account ma si sta tentando di usare un'immagine forniti da Microsoft che è valida solo durante la modalità di valutazione del servizio. In questo caso, provare a creare la raccolta di cloud nuovamente, ma accertarsi di specificare l'immagine corretta.
