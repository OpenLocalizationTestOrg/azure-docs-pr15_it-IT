<properties
    pageTitle="Allegare un disco dati a una VM Linux | Microsoft Azure"
    description="Come collegare disco dati nuovo o esistente a un VM Linux nel portale di Azure utilizzando il modello di distribuzione di Manager delle risorse."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cynthn"/>

# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Come collegare un disco dati a una VM Linux nel portale di Azure

In questo articolo viene illustrato come allegare dischi nuovi ed esistenti a una macchina virtuale Linux tramite il portale di Azure. È anche possibile [allegare un disco di dati a una macchina virtuale di Windows nel portale di Azure](virtual-machines-windows-attach-disk-portal.md). Prima di eseguire questa operazione, esaminare questi suggerimenti:

- Le dimensioni della macchina virtuale controlla quante dischi dati è possibile allegare. Per informazioni dettagliate, vedere [le dimensioni per macchine virtuali](virtual-machines-linux-sizes.md).
- Per utilizzare lo spazio di archiviazione Premium, è necessario una macchina virtuale della serie DS o serie GS. È possibile utilizzare dischi dagli account di archiviazione Standard e Premium con queste macchine virtuali. Spazio di archiviazione Premium è disponibile in alcune aree geografiche. Per informazioni dettagliate, vedere [archiviazione Premium: High-Performance lo spazio di archiviazione per carichi di lavoro di Azure macchina virtuale](../storage/storage-premium-storage.md).
- Dischi collegati alle macchine virtuali sono effettivamente VHD in un account di archiviazione Azure. Per informazioni dettagliate, vedere [informazioni sulla dischi e dischi rigidi virtuali per macchine virtuali](virtual-machines-linux-about-disks-vhds.md).
- Per un nuovo disco, non è necessario prima crearla poiché Azure crea quando si allega.
- Per un disco esistente, il file con estensione vhd deve essere disponibile in un account di archiviazione Azure. È possibile utilizzare un VHD è già presente, se non è associata a un altro computer virtuali o caricamento di file personalizzato VHD per l'account di archiviazione.


[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

Una volta aggiunto il disco, è necessario prepararsi per l'utilizzo. Vedere nel sistema operativo del computer virtuale: "come: inizializzare un nuovo disco di dati in Linux" in questo [articolo](virtual-machines-linux-classic-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux).
