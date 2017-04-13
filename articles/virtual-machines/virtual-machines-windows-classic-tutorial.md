<properties
    pageTitle="Creare una macchina virtuale nel portale di classica | Microsoft Azure"
    description="Creare una macchina virtuale di Windows nel portale di classica Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="cynthn"/>

# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>Creare una macchina virtuale che esegue Windows nel portale di classica Azure

> [AZURE.SELECTOR]
- [Portale classica Azure](virtual-machines-windows-classic-tutorial.md)
- [PowerShell: Distribuzione classica](virtual-machines-windows-classic-create-powershell.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Informazioni su come [eseguire questa procedura utilizzando il modello di distribuzione di Manager delle risorse](virtual-machines-windows-hero-tutorial.md) tramite il **portale di Azure nuovo**. 

In questa esercitazione viene illustrato come creare una Azure macchine () in esecuzione Windows nel portale di classica Azure. Useremo di un'immagine di Windows Server, ad esempio, ma è solo una delle numerose immagini che Azure offre. Si noti che le opzioni di immagine dipendono l'abbonamento. Immagini desktop di Windows, ad esempio, potrebbero essere disponibile per gli abbonati MSDN.

In questa sezione viene illustrato come utilizzare l'opzione **Dalla raccolta** nel portale di classica Azure per creare la macchina virtuale. Questa opzione consente di altre opzioni di configurazione rispetto all'opzione **Creazione rapida** . Ad esempio, se si vuole partecipare a una macchina virtuale a una rete, è necessario usare l'opzione **Dalla raccolta** .

È anche possibile creare macchine virtuali utilizzando [le proprie immagini](virtual-machines-windows-classic-createupload-vhd.md). Per informazioni su questo e altri metodi, vedere [diversi modi per creare una macchina virtuale di Windows](virtual-machines-windows-creation-choices.md).



## <a name="video-walkthrough"></a>Procedura dettagliata video

Ecco una procedura dettagliata di questa esercitazione.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Creare la macchina virtuale

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una macchina virtuale utilizzando il modello di distribuzione di Manager delle risorse](virtual-machines-windows-hero-tutorial.md) nel portale di Azure nuovo. 

- Accedere al computer virtuale. Per ulteriori informazioni, vedere [accedere a una macchina virtuale che eseguono Windows Server](virtual-machines-windows-classic-connect-logon.md).

- Collegare un disco per memorizzare i dati. È possibile allegare dischi vuoti e dischi che contengono dati. Per ulteriori informazioni, vedere [metodo per allegare un disco di dati a una macchina virtuale di Windows creato con il modello di distribuzione classica](virtual-machines-windows-classic-attach-disk.md).
