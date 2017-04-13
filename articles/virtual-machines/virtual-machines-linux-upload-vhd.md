<properties
    pageTitle="Creare e caricare un'immagine personalizzata Linux | Microsoft Azure"
    description="Creare e caricare un disco rigido virtuale (disco rigido virtuale) in Azure con un'immagine personalizzata Linux utilizzando il modello di distribuzione di Manager delle risorse."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="iainfou"/>

# <a name="upload-and-create-a-linux-vm-from-custom-disk-image"></a>Caricare e creare una VM Linux da immagine disco personalizzato

In questo articolo viene illustrato come caricare un disco rigido virtuale (disco rigido virtuale) in Azure utilizzando il modello di distribuzione di Manager delle risorse e creare macchine virtuali Linux da questa immagine personalizzata. Questa funzionalità consente di installare e configurare un distro Linux alle proprie esigenze e quindi utilizzare tale disco rigido virtuale per creare rapidamente Azure macchine ().

## <a name="quick-commands"></a>Comandi rapidi
Se è necessario eseguire rapidamente attività, i seguenti dettagli della sezione della base comandi per caricare una macchina virtuale in Azure. Ulteriori informazioni e il contesto di ogni passaggio sono disponibili il resto del documento, [iniziare qui](#requirements).

Assicurarsi di avere [CLI Azure](../xplat-cli-install.md) eseguito l'accesso e utilizzo della modalità di gestione risorse:

```bash
azure config mode arm
```

Nell'esempio seguente, sostituire i nomi dei parametri di esempio con valori personalizzati. I nomi dei parametri di esempio inclusi `myResourceGroup`, `mystorageaccount`, e `myimages`.

Creare un gruppo di risorse. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nel `WestUs` percorso:

```bash
azure group create myResourceGroup --location "WestUS"
```

Creare un account di archiviazione per mettere in attesa dischi virtuali. Nell'esempio seguente viene creato un account di archiviazione denominato `mystorageaccount`:

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

Elencare i tasti di scelta per l'account di archiviazione. Prendere nota del `key1`:

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Creare un contenitore all'interno di account di archiviazione tramite la chiave di archiviazione che è stato acquistato. Nell'esempio seguente viene creato un contenitore denominato `myimages` utilizzando il valore di chiave lo spazio di archiviazione da `key1`:

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

Infine, caricare il disco rigido virtuale contenitore che è stato creato. Specificare il percorso locale per il disco rigido virtuale in `/path/to/disk/mydisk.vhd`:

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

È ora possibile creare una macchina virtuale dal disco virtuale caricati [utilizzando un modello di Manager delle risorse](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). È inoltre possibile utilizzare CLI specificando URI sul disco (`--image-urn`). Nell'esempio seguente viene creata una macchina virtuale denominata `myVM` utilizzando il disco virtuale precedentemente caricati:

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

L'account di archiviazione di destinazione deve essere uguale nel punto in cui è stata caricata il disco virtuale. È necessario specificare o risposta viene visualizzato un prompt per tutti i parametri aggiuntivi necessari affinché la `azure vm create` comando, ad esempio virtuali, indirizzo IP pubblico, nome utente e le chiavi SSH. È possibile leggere altre informazioni sui [parametri di gestione di risorse CLI disponibili](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Requisiti
Per completare la procedura seguente, è necessario:

- **Sistema operativo Linux installato in un file con estensione vhd** - installare una [distribuzione Linux approvato Azure](virtual-machines-linux-endorsed-distros.md) (o visualizzare [le informazioni per le distribuzioni non approvato](virtual-machines-linux-create-upload-generic.md)) a un disco virtuale nel formato di disco rigido virtuale. Per creare una macchina virtuale e disco rigido virtuale sono disponibili diversi strumenti:
    - Installare e configurare [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](http://www.linux-kvm.org/page/RunningKVM), prestando attenzione a utilizzare disco rigido virtuale come il formato immagine. Se necessario, è possibile [convertire un'immagine](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) usando `qemu-img convert`.
    - È anche possibile utilizzare Hyper-V [in Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [in Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Nel nuovo formato VHDX non è supportato in Azure. Quando si crea una macchina virtuale, specificare disco rigido virtuale come formato. Se necessario, è possibile convertire dischi VHDX all'uso di disco rigido virtuale [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) cmdlet di PowerShell. Azure ulteriormente, non supporta il caricamento di dischi rigidi virtuali dinamici, pertanto è necessario convertire tali dischi in dischi rigidi virtuali statici prima di caricare. È possibile utilizzare gli strumenti, ad esempio [Azure utilità disco rigido virtuale per passare](https://github.com/Microsoft/azure-vhd-utils-for-go) da convertire dischi dinamici durante il processo di caricamento di Azure.

- Macchine virtuali create da un'immagine personalizzata devono trovarsi nello stesso account di archiviazione dell'immagine stessa
    - Creare un account di archiviazione e un contenitore per contenere macchine virtuali create sia l'immagine personalizzata
    - Dopo aver creato tutte le macchine virtuali, è possibile eliminare l'immagine

Assicurarsi di avere [CLI Azure](../xplat-cli-install.md) eseguito l'accesso e utilizzo della modalità di gestione risorse:

```bash
azure config mode arm
```

Nell'esempio seguente, sostituire i nomi dei parametri di esempio con valori personalizzati. I nomi dei parametri di esempio inclusi `myResourceGroup`, `mystorageaccount`, e `myimages`.


<a id="prepimage"> </a>
## <a name="prepare-the-image-to-be-uploaded"></a>Preparare l'immagine da caricare

Azure supporta diversi distribuzioni Linux (vedere [Distribuzioni approvato](virtual-machines-linux-endorsed-distros.md)). I seguenti articoli guidano come preparare le varie distribuzioni Linux supportate in Azure:

- **[Distribuzioni basate su centOS](virtual-machines-linux-create-upload-centos.md)**
- **[Linux Debian](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Linux Oracle](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Altra - distribuzioni Non approvato](virtual-machines-linux-create-upload-generic.md)**

Vedere anche le **[Note sull'installazione Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** per suggerimenti più generale su preparazione Linux immagini di Azure.

> [AZURE.NOTE] La [piattaforma Azure contratto di servizio](https://azure.microsoft.com/support/legal/sla/virtual-machines/) si applica a macchine virtuali in esecuzione Linux solo quando una delle distribuzioni avallate viene usata con i dettagli di configurazione come indicato nella sezione "versioni supportate' in [Linux nelle distribuzioni Azure-Endorsed](virtual-machines-linux-endorsed-distros.md).


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Gruppi di risorse in modo logico riunire tutte le risorse Azure per supportare le macchine virtuali, ad esempio la rete virtuale e lo spazio di archiviazione. Altre informazioni sui [gruppi di risorse Azure qui](../azure-resource-manager/resource-group-overview.md). Prima di caricare l'immagine disco personalizzata e la creazione di macchine virtuali, è innanzitutto necessario creare un gruppo di risorse. 

Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nel `WestUS` percorso:

```bash
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
Macchine virtuali vengono memorizzate come BLOB di pagine all'interno di un account di archiviazione. Altre informazioni [sull'archiviazione blob Azure qui](../storage/storage-introduction.md#blob-storage). Si crea un account di archiviazione per l'immagine del disco personalizzato e macchine virtuali. Macchine virtuali creati dall'immagine disco personalizzato devono essere nello stesso account di archiviazione dell'immagine.

Nell'esempio seguente viene creato un account di archiviazione denominato `mystorageaccount` nel gruppo di risorse creato in precedenza:

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>Elenco dello spazio di archiviazione account tasti
Azure genera due 512 bit tasti di scelta per ogni account di archiviazione. Questi tasti di scelta vengono utilizzati per l'autenticazione nell'account di archiviazione, ad esempio per eseguire le operazioni di scrittura. Leggere altre informazioni sulla [gestione dell'accesso allo spazio di archiviazione di seguito](../storage/storage-create-storage-account.md#manage-your-storage-account). È possibile visualizzare i tasti di scelta con la `azure storage account keys list` comando.

Visualizzare i tasti di scelta per l'account di archiviazione che è stato creato:

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

L'output è simile a:

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK

```
Prendere nota del `key1` come verrà utilizzato per interagire con l'account di archiviazione nei passaggi successivi.

## <a name="create-a-storage-container"></a>Creare un contenitore di spazio di archiviazione
Nello stesso modo creati directory diverse per organizzare in modo logico nel file system locale, creare contenitori all'interno di un account di archiviazione per organizzare dischi virtuali e immagini. Un account di archiviazione può contenere un numero di contenitori. 

Nell'esempio seguente viene creato un contenitore denominato `myimages`, che specifica il tasto di scelta ottenuto nel passaggio precedente (`key1`):

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>Caricare disco rigido virtuale
A questo punto è possibile caricare effettivamente l'immagine disco personalizzata. Come con tutti i dischi virtuali utilizzati da macchine virtuali, potete caricare e memorizzare immagine del disco personalizzata come blob pagina.

Specificare il tasto di scelta, contenitore è stato creato nel passaggio precedente e quindi il percorso dell'immagine disco personalizzata nel computer locale:

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>Creare macchine Virtuali da immagine personalizzata
Quando si creano macchine virtuali dall'immagine disco personalizzata, specificare l'URI per l'immagine del disco. Assicurarsi che le corrispondenze di account lo spazio di archiviazione di destinazione in cui è archiviato l'immagine disco personalizzata. È possibile creare la macchina virtuale utilizzando il modello CLI Azure o JSON Manager delle risorse.


### <a name="create-a-vm-using-the-azure-cli"></a>Creare una macchina virtuale utilizzando CLI Azure
Specificare la `--image-urn` parametro con il `azure vm create` comando in modo che puntino all'immagine del disco personalizzata. Assicurarsi che `--storage-account-name` corrisponde all'account di archiviazione in cui è archiviato l'immagine disco personalizzata. Non è necessario utilizzare lo stesso contenitore come immagine disco personalizzata per archiviare le macchine virtuali. Assicurarsi di creare eventuali altri contenitori nello stesso modo i passaggi precedenti prima di caricare immagini del disco rigido personalizzato.

Nell'esempio seguente viene creata una macchina virtuale denominata `myVM` dall'immagine disco personalizzata:

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

È necessario specificare o risposta viene visualizzato un prompt per tutti i parametri aggiuntivi necessari affinché la `azure vm create` comando, ad esempio virtuali, indirizzo IP pubblico, nome utente e le chiavi SSH. Altre informazioni sui [parametri di gestione di risorse CLI disponibili](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### <a name="create-a-vm-using-a-json-template"></a>Creare una macchina virtuale utilizzando un modello JSON
Azure modelli di Manager delle risorse sono i file JavaScript Object Notation (JSON) che definiscono l'ambiente che si desidera creare. I modelli sono suddivise per i provider di risorse diversi, ad esempio calcolo o in rete. È possibile utilizzare i modelli esistenti o crearne di nuovi. Altre informazioni [sull'utilizzo di Manager delle risorse e modelli](../azure-resource-manager/resource-group-overview.md).

All'interno di `Microsoft.Compute/virtualMachines` provider del modello, si dispone di un `storageProfile` nodo che contiene i dettagli di configurazione per la macchina virtuale. Sono i due parametri principali per modificare il `image` e `vhd` URI che puntano alle immagine del disco personalizzata e disco virtuale il nuovo Virtual Machine. Di seguito viene illustrato un esempio di JSON per l'uso di un'immagine del disco personalizzato:

```bash
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

È possibile utilizzare [questo modello esistente per creare una macchina virtuale da un'immagine personalizzata](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) o leggere informazioni sulla [creazione di modelli di gestione risorse di Azure](../resource-group-authoring-templates.md). 

Dopo avere creato un modello configurato, si crea la macchine virtuali mediante la `azure group deployment create` comando. Specificare l'URI del modello JSON con la `--template-uri` parametro:

```bash
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

Se si dispone di un file JSON archiviato in locale nel computer in uso, è possibile utilizzare il `--template-file` parametro invece:

```bash
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Passaggi successivi
Dopo aver preparato e caricare il disco virtuale personalizzato, è possibile leggere informazioni [sull'utilizzo di Manager delle risorse e modelli](../azure-resource-manager/resource-group-overview.md). È anche consigliabile per [aggiungere un disco dati](virtual-machines-linux-add-disk.md) nelle macchine virtuali di nuovo. Se si dispone di applicazioni in esecuzione su nelle macchine virtuali che è necessario accedere, assicurarsi di [aprire porte e i punti finali](virtual-machines-linux-nsg-quickstart.md).