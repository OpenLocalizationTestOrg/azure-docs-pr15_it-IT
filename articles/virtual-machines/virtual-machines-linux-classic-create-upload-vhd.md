<properties
    pageTitle="Creare e caricare un VHD Linux | Microsoft Azure"
    description="Creare e caricare un disco rigido virtuale Azure (disco rigido virtuale) con il modello di distribuzione classica che contiene il sistema operativo Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Creare e caricare un disco rigido virtuale che contiene il sistema operativo Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]È inoltre possibile [caricare un'immagine disco personalizzata tramite Gestione risorse di Azure](virtual-machines-linux-upload-vhd.md).

In questo articolo viene illustrato come creare e caricare un disco rigido virtuale (disco rigido virtuale) in modo che è possibile utilizzare come la propria immagine per creare macchine virtuali in Azure. Informazioni su come preparare il sistema operativo in modo che è possibile utilizzare per creare più macchine virtuali in base a tale immagine. 

>  [AZURE.NOTE] Se si dispone di qualche minuto, Aiutaci a migliorare la documentazione di supporto virtuale Linux Azure accettando [sondaggio rapido](https://aka.ms/linuxdocsurvey) dei propri interessi. Tutte le risposte aiutano Microsoft a Guida viene visualizzato il proprio lavoro.

## <a name="prerequisites"></a>Prerequisiti
In questo articolo si presuppone che sia gli elementi seguenti:

- **Sistema operativo Linux installato in un file con estensione vhd** - è stato installato una [distribuzione Linux approvato Azure](virtual-machines-linux-endorsed-distros.md) (o visualizzare le [informazioni per le distribuzioni non approvato](virtual-machines-linux-create-upload-generic.md)) a un disco virtuale nel formato di disco rigido virtuale. Per creare una macchina virtuale e disco rigido virtuale sono disponibili diversi strumenti:
    - Installare e configurare [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](http://www.linux-kvm.org/page/RunningKVM), prestando attenzione a utilizzare disco rigido virtuale come il formato immagine. Se necessario, è possibile [convertire un'immagine](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando `qemu-img convert`.
    - È anche possibile utilizzare Hyper-V [in Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [in Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Nel nuovo formato VHDX non è supportato in Azure. Quando si crea una macchina virtuale, specificare disco rigido virtuale come formato. Se necessario, è possibile convertire dischi VHDX all'uso di disco rigido virtuale [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) cmdlet di PowerShell. Azure ulteriormente, non supporta il caricamento di dischi rigidi virtuali dinamici, pertanto è necessario convertire tali dischi in dischi rigidi virtuali statici prima di caricare. È possibile utilizzare gli strumenti, ad esempio [Azure utilità disco rigido virtuale per passare](https://github.com/Microsoft/azure-vhd-utils-for-go) da convertire dischi dinamici durante il processo di caricamento di Azure.

- **Interfaccia di riga di comando azure** - installare l' ultima [interfaccia riga di comando di Azure](../virtual-machines-command-line-tools.md) per caricare il disco rigido virtuale.

<a id="prepimage"> </a>
## <a name="step-1-prepare-the-image-to-be-uploaded"></a>Passaggio 1: Preparare l'immagine da caricare

Azure supporta diversi distribuzioni Linux (vedere [Distribuzioni approvato](virtual-machines-linux-endorsed-distros.md)). I seguenti articoli guidano come preparare le varie distribuzioni Linux supportate in Azure. Dopo aver completato la procedura descritta in Guide seguenti, sono disponibili qui dopo avere inserito un file disco rigido virtuale che è pronto per caricare in Azure:

- **[Distribuzioni basate su centOS](virtual-machines-linux-create-upload-centos.md)**
- **[Linux Debian](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Linux Oracle](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Altra - distribuzioni Non approvato](virtual-machines-linux-create-upload-generic.md)**

> [AZURE.NOTE] La piattaforma Azure contratto di servizio si applica a macchine virtuali in esecuzione il sistema operativo Linux solo quando una delle distribuzioni avallate viene usata con i dettagli di configurazione come indicato nella sezione "versioni supportate' in [Linux nelle distribuzioni Azure-Endorsed](virtual-machines-linux-endorsed-distros.md). Tutte le distribuzioni Linux nella raccolta di immagini Azure sono avallate distribuzioni con la configurazione necessaria.

Vedere anche le **[Note sull'installazione Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** per suggerimenti più generale su preparazione Linux immagini di Azure.


<a id="connect"> </a>
## <a name="step-2-prepare-the-connection-to-azure"></a>Passaggio 2: Preparare la connessione al Azure

Verificare che si usa CLI Azure nel modello di distribuzione classico (`azure config mode asm`), quindi accedere al proprio account:

```
azure login
```


<a id="upload"> </a>
## <a name="step-3-upload-the-image-to-azure"></a>Passaggio 3: Caricare l'immagine in Azure

È necessario un account di archiviazione per caricare il file disco rigido virtuale. È possibile scegliere di un account di archiviazione esistente o [crearne uno nuovo](../storage/storage-create-storage-account.md).

Usare CLI Azure per caricare l'immagine utilizzando il comando seguente:

```bash
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

Nell'esempio precedente:

- **BlobStorageURL** è l'URL per l'account di archiviazione che si prevede di utilizzare
- **YourImagesFolder** è il contenitore all'interno dell'archiviazione blob in cui si desidera archiviare le immagini
- **VHDName** è l'etichetta visualizzata nel portale per identificare il disco rigido virtuale.
- **PathToVHDFile** è il percorso completo e il nome del file con estensione vhd nel computer in uso.

Di seguito viene illustrato un esempio completo:

```bash
azure vm image create UbuntuLTS `
    --blob-url https://teststorage.blob.core.windows.net/vhds/UbuntuLTS.vhd `
    --os Linux /home/ahmet/UbuntuLTS.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>Passaggio 4: Creare una macchina virtuale dall'immagine
Si crea un utilizzando macchine Virtuali `azure vm create` nello stesso modo una macchina virtuale normale. Specificare il nome assegnato l'immagine nel passaggio precedente. Nell'esempio seguente è usare il nome dell'immagine **UbuntuLTS** specificato nel passaggio precedente:

```bash
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "DeployedUbuntu" UbuntuLTS
```

Per creare il proprio macchine virtuali, specificare il proprio nome utente + password, percorso, nome DNS e nome dell'immagine.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere [informazioni di riferimento Azure CLI per il modello di Azure distribuzione classica](../virtual-machines-command-line-tools.md).

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload
