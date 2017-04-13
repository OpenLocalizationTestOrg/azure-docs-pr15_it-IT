<properties
    pageTitle="Creare un'immagine di Azure RemoteApp | Microsoft Azure"
    description="Informazioni sulle opzioni disponibili per la creazione di immagini per RemoteApp di Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="create-an-azure-remoteapp-image"></a>Creare un'immagine RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Azure RemoteApp utilizza le immagini per contenere le app che si condividano con altri utenti. (È acquisire l'immagine e usarlo per creare macchine virtuali - il quali l'accesso agli utenti al momento dell'accesso in Azure RemoteApp) Per creare un insieme di Azure RemoteApp con la scelta delle applicazioni, che si tratti cloud o ibrida, è innanzitutto necessario creare un'immagine con le applicazioni installate. Creare una raccolta che usa l'immagine, assegnare agli utenti per la raccolta e pubblicare App per tali utenti.

Sono disponibili diverse opzioni per la creazione e utilizzo delle immagini. Il [requisito](remoteapp-imagereqs.md) di base per un'immagine è esecuzione Windows Server 2012 R2 ed è installato il ruolo Remote Desktop sessione Host (RDSH). Come si ottiene che è in cui le cose interessanti.

Quando si tratta di immagini, sono disponibili le opzioni seguenti:

- È possibile importare e utilizzare un' [immagine in base a una macchina virtuale Azure](remoteapp-image-on-azurevm.md). Questo è utile per applicazioni line-of-business che richiedono impostazioni personalizzate. È possibile personalizzare l'immagine da usare per l'app.
- È possibile [creare e caricare un'immagine personalizzata](remoteapp-create-custom-image.md). Questo è utile se si dispone già di un'immagine da usare per la distribuzione di Servizi Desktop remoto locale.
- È possibile utilizzare una delle [le immagini dei modelli](remoteapp-images.md) inclusi nell'abbonamento RemoteApp. Queste immagini vengono create e gestiti dal team di RemoteApp e contengono alcune applicazioni standard (ad esempio la famiglia di prodotti Office) che è possibile rendere disponibili agli utenti. Si noti che solo l'immagine di Office 365 Pro Plus può essere utilizzata in un ambiente di produzione.

Indipendentemente da dove viene visualizzato l'immagine o come si crea, si desidera verificare i [requisiti dell'app](remoteapp-appreqs.md) per assicurarsi che l'app funziona correttamente in RemoteApp. Quindi, il passaggio successivo consiste nel creare una raccolta [cloud](remoteapp-create-cloud-deployment.md) o [ibrida](remoteapp-create-hybrid-deployment.md) .
