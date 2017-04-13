<properties
    pageTitle="Diversi modi per creare una VM Linux | Microsoft Azure"
    description="Conoscere i diversi metodi per creare una macchina virtuale Linux su Azure, compresi i collegamenti a strumenti e le esercitazioni per ogni metodo."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>Diversi modi per creare una macchina virtuale Linux in Azure

Si dispone della flessibilità necessaria in Azure per creare una Linux macchine () utilizzando strumenti e i flussi di lavoro comodo per l'utente. In questo articolo sono riepilogate le differenze ed esempi relativi alla creazione nelle macchine virtuali Linux.


## <a name="azure-cli"></a>CLI Azure 

CLI Azure è disponibile per le piattaforme tramite un pacchetto di npm, purché distro pacchetti o contenitore Docker. È possibile leggere informazioni su [come installare e configurare CLI Azure](../xplat-cli-install.md). Le seguenti esercitazioni forniscono esempi sull'uso di CLI Azure. Ogni articolo per informazioni dettagliate sui comandi di avvio rapido CLI visualizzati:

- [Creare una VM Linux da CLI Azure per sviluppo e test](virtual-machines-linux-quick-create-cli.md)
    - Nell'esempio seguente viene creato un VM CoreOS utilizzando una chiave pubblica denominata `azure_id_rsa.pub`:

    ```bash
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
        --image-urn CoreOS
    ```

- [Creare una VM Linux protetta utilizzando un modello di Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
    - Nell'esempio seguente viene creata una macchina virtuale utilizzando un modello archiviato in GitHub:

    ```bash
    azure group create --name TestRG --location WestUS 
        --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```

- [Creare un ambiente Linux completo tramite CLI Azure](virtual-machines-linux-create-cli-complete.md)
    - Include la creazione di un servizio di bilanciamento del carico e più macchine virtuali di un set di disponibilità.

- [Aggiungere un disco una VM Linux](virtual-machines-linux-add-disk.md)
    - Nell'esempio seguente aggiunge un disco 5Gb per una macchina virtuale esistente denominata `TestVM`:

    ```bash
    azure vm disk attach-new --resource-group TestRG --vm-name TestVM \
        --size-in-GB 5
    ```

## <a name="azure-portal"></a>Portale di Azure

Il [portale di Azure](https://portal.azure.com) consente di creare rapidamente una macchina virtuale poiché niente da installare nel sistema. Utilizzare il portale di Azure per creare la macchina virtuale:

- [Creare una VM Linux tramite il portale di Azure](virtual-machines-linux-quick-create-portal.md) 
- [Collegare un disco tramite il portale di Azure](virtual-machines-linux-attach-disk-portal.md)


## <a name="operating-system-and-image-choices"></a>Sistema operativo e le opzioni dell'immagine
Quando si crea una macchina virtuale, si scelta un'immagine in base al sistema operativo che si desidera eseguire. Azure e partner offrono molte immagini, alcune delle quali include le applicazioni e strumenti preinstallati. In alternativa, caricare una delle proprie immagini (vedere [la sezione seguente](#use-your-own-image)).

### <a name="azure-images"></a>Immagini di Azure
Utilizzare la `azure vm image` comandi CLI per visualizzare il contenuto disponibile per publisher, versione distro e build.

Elenco autori disponibili come indicato di seguito:

```bash
azure vm image list-publishers --location WestUS
```

Elenco prodotti disponibili (offerte) per un determinato autore come indicato di seguito:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Elencare gli SKU disponibili (distro versioni) di un'offerta specificato come indicato di seguito:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Elenca tutte le immagini disponibili per una determinata release segue:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Per altri esempi di esplorazione e l'utilizzo di immagini disponibili, vedere [Naviga e selezionare macchina virtuale Azure immagini con CLI Azure](virtual-machines-linux-cli-ps-findimage.md).

Il `azure vm quick-create` e `azure vm create` comandi dispongono di alias è possibile usare per accedere rapidamente alle distros più comuni e le versioni più recenti. Utilizzo degli alias è spesso più rapido rispetto a specificare l'autore, offerta, SKU e versione ogni volta che si crea una macchina virtuale:

| Alias     | Publisher | Offerta        | SKU         | Versione |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | Centos       | 7.2         | più recente  |
| CoreOS    | CoreOS    | CoreOS       | Stabile      | più recente  |
| Debian    | credativ  | Debian       | 8           | più recente  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | più recente  |
| RHEL      | Red Hat    | RHEL         | 7.2         | più recente  |
| SLES      | SLES      | SLES         | SP1 12      | più recente  |
| UbuntuLTS | Canonici | UbuntuServer | 14.04.4-LTS | più recente  |

### <a name="use-your-own-image"></a>Utilizzare la propria immagine

Se si richiedono personalizzazioni specifiche, è possibile utilizzare un'immagine in base a una macchina virtuale Azure esistente mediante *l'acquisizione di* quella macchina virtuale. È anche possibile caricare un'immagine creata in locale. Per ulteriori informazioni sulle distros supportati e su come usare le proprie immagini, vedere gli articoli seguenti:

- [Azure approvato distribuzioni](virtual-machines-linux-endorsed-distros.md)

- [Informazioni per le distribuzioni non approvato](virtual-machines-linux-create-upload-generic.md)

- Informazioni su [come acquisire una macchina virtuale Linux come modello Manager delle risorse](virtual-machines-linux-capture-image.md).
    - Avvio rapido i comandi di esempio per acquisire una macchina virtuale esistente:

    ```bash
    azure vm deallocate --resource-group TestRG --vm-name TestVM
    azure vm generalize --resource-group TestRG --vm-name TestVM
    azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
    ```

## <a name="next-steps"></a>Passaggi successivi

- Creare una VM Linux dal [portale](virtual-machines-linux-quick-create-portal.md), con [CLI](virtual-machines-linux-quick-create-cli.md)o utilizzando un [modello di gestione risorse di Azure](virtual-machines-linux-cli-deploy-templates.md).

- Dopo aver creato una VM Linux, [aggiungere un disco dati](virtual-machines-linux-add-disk.md).

- Azioni rapide per [reimpostare una password o chiavi SSH e gestire gli utenti](virtual-machines-linux-using-vmaccess-extension.md)
