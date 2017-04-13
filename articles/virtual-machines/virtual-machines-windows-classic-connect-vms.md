<properties
    pageTitle="Connettere macchine virtuali di Windows in un servizio cloud | Microsoft Azure"
    description="Connettere macchine virtuali di Windows create con il modello di distribuzione classica a un servizio cloud Azure o virtuali."
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
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>Connettere macchine virtuali di Windows create con il modello di distribuzione classica con un servizio di rete o su cloud virtuale

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Macchine virtuali di Windows create con il modello di distribuzione classica assumono sempre una posizione in un servizio cloud. Il servizio cloud funge da contenitore e fornisce un nome DNS pubblico univoco, un indirizzo IP pubblico e un set di punti finali di accedere al computer virtuale tramite Internet. Il servizio cloud può essere in una rete virtuale, ma che non è un requisito. È inoltre possibile [connettersi alle macchine virtuali Linux con un servizio di rete o su cloud virtuale](virtual-machines-linux-classic-connect-vms.md).

Se in una rete virtuale non è un servizio cloud, si tratta di un servizio cloud *autonomo* . Macchine virtuali in un servizio cloud autonomo possono comunicare solo con altre macchine virtuali utilizzando i nomi DNS pubblici degli altri macchine virtuali e il traffico inviati in Internet. Se un servizio cloud si trova in una rete virtuale, macchine virtuali di tale servizio cloud possono comunicare con tutte le altre macchine virtuali di rete virtuale senza inviare il traffico tramite Internet.

Se si inseriscono macchine virtuali nello stesso servizio cloud autonoma, è possibile usare ancora il bilanciamento del carico e dei set di disponibilità. Per informazioni dettagliate, vedere [macchine virtuali di bilanciamento del carico](virtual-machines-windows-load-balance.md) e [gestire la disponibilità di macchine virtuali](virtual-machines-windows-manage-availability.md). Tuttavia, non è possibile organizzare le macchine virtuali subnet o collegare un servizio cloud autonomo alla rete locale. Ecco un esempio:

[AZURE.INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato una macchina virtuale, è consigliabile [aggiungere un disco dati](virtual-machines-windows-classic-attach-disk.md) in modo che i servizi e carichi di lavoro dispone di un percorso per archiviare i dati. 