<properties
    pageTitle="Sui dischi e dischi rigidi virtuali per macchine virtuali Windows | Microsoft Azure"
    description="Informazioni sui concetti di base di dischi e macchine virtuali dischi rigidi virtuali per Windows Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="about-disks-and-vhds-for-azure-virtual-machines"></a>Sui dischi e dischi rigidi virtuali per macchine virtuali di Azure

Come qualsiasi altro computer, macchine virtuali in Azure utilizzare dischi come posizione per archiviare un sistema operativo, applicazioni e dati. Tutte le macchine virtuali Azure avere almeno due dischi: un disco di sistema operativo Windows e un temporaneo. Il disco di sistema operativo viene creato da un'immagine e il disco di sistema operativo e nell'immagine sono virtuali rigido dischi rigidi archiviati in un account di archiviazione Azure. Macchine virtuali possono avere uno o più dischi di dati, vengono inoltre memorizzati come dischi rigidi virtuali. In questo articolo è anche disponibile per [macchine virtuali Linux](virtual-machines-linux-about-disks-vhds.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



## <a name="operating-system-disk"></a>Sistema operativo disco

Ogni macchina virtuale ha un disco di sistema operativo collegato. Ha registrato come un'unità SATA ed etichettata con l'unità c per impostazione predefinita. Il disco ha una capacità massima di 1023 gigabyte (GB). 

##<a name="temporary-disk"></a>Disco temporaneo

Il disco temporaneo verrà creato automaticamente. Il disco temporaneo viene segnalato come unità d: per impostazione predefinita e viene utilizzato per l'archiviazione Pagefile. 

Le dimensioni del disco temporaneo variano in base alla dimensione della macchina virtuale. Per ulteriori informazioni, vedere [macchine virtuali di dimensioni per Windows](virtual-machines-windows-sizes.md).

>[AZURE.WARNING] Non memorizzare dati nel disco temporaneo. Fornisce archiviazione temporanea per applicazioni e processi e viene usata per archiviare i dati, ad esempio pagina o scambiare file solo. Per modificare il mapping del disco a una lettera di unità diversa, vedere [modificare la lettera del disco temporaneo Windows](virtual-machines-windows-classic-change-drive-letter.md).

Per ulteriori informazioni su come Azure viene utilizzato il disco temporaneo, vedere [informazioni sull'unità temporanea in macchine virtuali di Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Disco di dati

Un disco dati è un disco rigido virtuale collegato a una macchina virtuale per l'archiviazione dei dati dell'applicazione o altri dati che è necessario mantenere. Dati dischi registrati come unità SCSI e sono contrassegnati con una lettera che si è scelto.  Ogni disco dati ha una capacità massima di 1023 GB. Le dimensioni della macchina virtuale determinano quanti dischi dati è possibile allegare e il tipo di spazio di archiviazione è possibile utilizzare per ospitare i dischi.

>[AZURE.NOTE] Per ulteriori informazioni sulla capacità di macchine virtuali, vedere [macchine virtuali di dimensioni per Windows](virtual-machines-windows-sizes.md).

Azure crea un disco di sistema operativo quando si crea una macchina virtuale da un'immagine. Se si usa un'immagine che include i dischi dei dati, Azure crea anche dischi di dati quando viene creato la macchina virtuale. In caso contrario, aggiungere dischi dati dopo aver creato la macchina virtuale.

È possibile aggiungere dischi di dati a una macchina virtuale in qualsiasi momento da **associare** il disco alla macchina virtuale. È possibile utilizzare un disco rigido virtuale che è stato caricato o copiato nel proprio account di archiviazione o uno che Azure creato automaticamente. Associare un disco dati associato il disco rigido virtuale file macchina virtuale inserendo un 'leasing' sul disco rigido virtuale in modo che non potranno essere eliminata dallo spazio di archiviazione mentre è ancora collegata.

## <a name="about-vhds"></a>Sui dischi rigidi virtuali

Dischi rigidi virtuali usati in Azure sono i file vhd archiviati come BLOB di pagine in un account di archiviazione standard o premium in Azure. Per ulteriori informazioni sui blob di pagine, vedere [informazioni sui blocco BLOB e BLOB di pagine](https://msdn.microsoft.com/library/ee691964.aspx). Per ulteriori informazioni sull'archiviazione premium, vedere [archiviazione Premium: spazio di archiviazione High-performance per carichi di lavoro di Azure macchina virtuale](../storage/storage-premium-storage.md).

Azure supporta il formato di disco rigido virtuale disco rigido. Formato fisso disposto il disco logico lineare all'interno del file in modo tale offset disco X archiviata offset blob X. Un piccolo piè di pagina alla fine del blob sono descritte le proprietà del file. Spesso, rifiuti formato fisso in base allo spazio perché la maggior parte dei dischi grandi intervalli inutilizzati presenti. Tuttavia, Azure memorizza VHD in un formato caricato in memoria bassa, in modo che si ricevono i vantaggi dei dischi fissi e dinamici nello stesso momento. Per ulteriori informazioni, vedere [Guida introduttiva a dischi rigidi virtuali](https://technet.microsoft.com/library/dd979539.aspx).

Tutti i file con estensione vhd Azure che si desidera utilizzare come origine per creare dischi o immagini sono di sola lettura. Quando si crea un disco o un'immagine, Azure fa copie di file con estensione vhd. Queste copie possono essere sola lettura o di lettura e scrittura, a seconda di come si usa il disco rigido virtuale.

Quando si crea una macchina virtuale da un'immagine, Azure crea un disco per la macchina virtuale che rappresenta una copia del file VHD di origine. Per evitare eliminazioni accidentali, Azure posizionata un leasing su qualsiasi file con estensione vhd origine che viene utilizzato per creare un'immagine, un disco di sistema operativo o un disco dati.

Prima di poter eliminare un file con estensione vhd origine, è necessario rimuovere il leasing eliminando il disco o l'immagine. È possibile eliminare la macchina virtuale, disco rigido del sistema operativo e il file con estensione vhd origine in un'unica eliminando la macchina virtuale e l'eliminazione di tutti associati dischi. Tuttavia, l'eliminazione di un file VHD che rappresenta un'origine per un disco dati richiede diversi passaggi in un ordine di set. Prima di tutto scollegare il disco dal computer virtuale, quindi eliminare il disco e quindi eliminare il file con estensione vhd.

>[AZURE.WARNING] Se si elimina un file con estensione vhd origine dallo spazio di archiviazione o Elimina l'account di archiviazione, Microsoft non è possibile recuperare i dati dell'utente.



## <a name="next-steps"></a>Passaggi successivi
-  [Allegare un disco](virtual-machines-windows-attach-disk-portal.md) per aggiungere ulteriore spazio di archiviazione per la macchina virtuale.
-  [Caricare un'immagine macchine Virtuali di Windows Azure](virtual-machines-windows-upload-image.md) da utilizzare durante la creazione di una nuova macchina virtuale.
-  [Modificare la lettera del disco temporaneo Windows](virtual-machines-windows-classic-change-drive-letter.md) in modo che l'applicazione può utilizzare l'unità d: per i dati.
