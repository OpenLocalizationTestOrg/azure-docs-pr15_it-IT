<properties
    pageTitle="Creare un'immagine di Azure RemoteApp in base a una macchina virtuale Azure | Microsoft Azure"
    description="Informazioni su come creare un'immagine per Azure RemoteApp, a partire da una macchina virtuale Azure."
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



# <a name="create-a-azure-remoteapp-image-based-on-an-azure-virtual-machine"></a>Creare un'immagine di Azure RemoteApp basata su una macchina virtuale Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

È possibile creare immagini di Azure RemoteApp (che contengono le app che condividere nella raccolta) da una macchina virtuale Azure. È anche possibile scegliere di utilizzare un'immagine di macchina virtuale che abbiamo aggiunto alla raccolta di immagini macchine Virtuali di Azure che soddisfi i requisiti di immagine RemoteApp di Azure, è possibile utilizzare tale immagine macchine Virtuali come punto di partenza per il proprio macchine Virtuali, se necessario. Basta cercare l'immagine "Windows Server Host sessione Desktop remoto" nella raccolta.

Sono disponibili due passaggi per creare la propria immagine in base a una macchina virtuale Azure: creare l'immagine e quindi caricarlo dalla raccolta di macchine Virtuali di Azure RemoteApp di Azure.

## <a name="create-a-custom-image-based-on-an-azure-vm"></a>Creare un'immagine personalizzata in base a una macchina virtuale Azure

Utilizzare questa procedura per creare un'immagine in base a una macchina virtuale Azure.

1. Creare una macchina virtuale Azure. È possibile utilizzare il "Windows Server Host sessione Desktop remoto" o "Windows Server Remote Desktop sessione Host con Microsoft Office 365 ProPlus" immagine dalla raccolta di immagini Azure macchina virtuale. In questa figura requisiti tutti Azure RemoteApp modello immagine.

    Per informazioni dettagliate, vedere [creare una macchina virtuale che esegue Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

2. Connettere la macchina virtuale e installare e configurare le app che si desidera condividere tramite RemoteApp. Assicurarsi di eseguire tutte le altre configurazioni di Windows necessari affinché le app.

    Per informazioni dettagliate, vedere [come accedere a una macchina virtuale che eseguono Windows Server](../virtual-machines/virtual-machines-windows-classic-connect-logon.md).

3. Se si utilizza una delle immagini di Windows Server Host sessione Desktop remoto, esiste un script di convalida inclusi che assicura che la macchina virtuale soddisfa RemoteApp pre-reqs. Per eseguire script, fare doppio clic su **ValidateRemoteAppImage** sul desktop. Assicurarsi che tutti gli errori segnalati dallo script sono corretti prima di procedere con il passaggio successivo.

4. SYSPREP generalizzare e acquisire un'immagine. Per istruzioni, vedere [come acquisire una macchina virtuale di Windows da utilizzare come modello](../virtual-machines/virtual-machines-windows-classic-capture-image.md) .



## <a name="import-the-image-into-the-azure-remoteapp-image-library"></a>Importare l'immagine dalla raccolta di immagini RemoteApp di Azure

Utilizzare la procedura seguente per importare la nuova immagine in Azure RemoteApp:

1. Nella scheda **Le immagini dei modelli** :
    - Se non si dispone di nessuna immagine esistente, fare clic su **Carica o importare un'immagine di modello**.
    - Se si dispone già almeno un'immagine, fare clic su **+** per aggiungere una nuova immagine.

2. Selezionare **Importa un'immagine da macchine virtuali** libreria e quindi fare clic su **Avanti**.

3. Nella pagina successiva, selezionare l'immagine personalizzata dall'elenco e confermare seguito i passaggi elencati al momento della creazione dell'immagine. Fare clic su **Avanti**.
4. Immettere un nome per la nuova immagine RemoteApp e selezionare il percorso, quindi fare clic sul segno di spunta per avviare il processo di importazione.

> [AZURE.NOTE] È possibile importare immagini da qualsiasi luogo Azure supportato da macchine virtuali di Azure in un punto qualsiasi Azure supportato da Azure RemoteApp. A seconda delle posizioni dell'importazione può richiedere fino a 25 minuti.

A questo punto si è pronti creare il nuovo insieme sia un insieme di [cloud](remoteapp-create-cloud-deployment.md) o [ibrida](remoteapp-create-hybrid-deployment.md), a seconda delle esigenze.
