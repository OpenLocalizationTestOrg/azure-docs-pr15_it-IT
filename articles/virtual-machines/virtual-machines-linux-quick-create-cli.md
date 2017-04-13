<properties
   pageTitle="Creare una VM Linux su Azure tramite CLI | Microsoft Azure"
   description="Creare una VM Linux su Azure tramite CLI."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="v-livech"/>


# <a name="create-a-linux-vm-on-azure-by-using-the-cli"></a>Creare una VM Linux su Azure tramite CLI

In questo articolo viene illustrato come distribuire rapidamente una Linux macchine () in Azure utilizzando il `azure vm quick-create` comando nell'interfaccia Azure della riga di comando (CLI). Il `quick-create` comando distribuisce una macchina virtuale all'interno di un'infrastruttura sicura di base che è possibile utilizzare per prototipo o testare rapidamente un concetto. Richiede l'articolo:

- un account Azure ([ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- [Azure CLI](../xplat-cli-install.md) ha eseguito l'accesso con `azure login`.

- la modalità di gestione di risorse Azure _deve essere_ Azure CLI `azure config mode arm`.

È possibile distribuire anche rapidamente un VM Linux tramite il [portale di Azure](virtual-machines-linux-quick-create-portal.md).

## <a name="quick-commands"></a>Comandi rapidi

Nell'esempio seguente viene illustrato come distribuire un VM CoreOS e allegare la chiave Secure Shell (SSH) (degli argomenti potrebbero essere diversi):

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q CoreOS
```

## <a name="detailed-walkthrough"></a>Descrizione dettagliata

La procedura riportata di seguito è un VM UbuntuLTS viene distribuito, passo dopo passo, con le spiegazioni per le istruzioni dettagliate.

## <a name="vm-quick-create-aliases"></a>Macchine Virtuali rapidi: consente di creare alias

Un modo rapido per scegliere una distribuzione consiste nell'utilizzare gli alias di Azure CLI mappati le distribuzioni del sistema operativo più comuni. Nella tabella seguente elenca gli alias (dal CLI Azure versione 0,10). Tutte le distribuzioni che utilizzano `quick-create` impostazioni predefinite per macchine virtuali supportati dagli unità (SSD) dello spazio di archiviazione, che offre accesso al disco più velocemente provisioning e prestazioni elevate. (Questi alias rappresentano una piccola parte distribuzioni disponibili in Azure. Trova altre immagini in Azure Marketplace per [cercare un'immagine in PowerShell](virtual-machines-linux-cli-ps-findimage.md), [sul web](https://azure.microsoft.com/marketplace/virtual-machines/)o [caricare un'immagine personalizzata](virtual-machines-linux-create-upload-generic.md)).

| Alias     | Publisher | Offerta        | SKU         | Versione |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | CentOS       | 7.2         | più recente  |
| CoreOS    | CoreOS    | CoreOS       | Stabile      | più recente  |
| Debian    | credativ  | Debian       | 8           | più recente  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | più recente  |
| RHEL      | Red Hat    | RHEL         | 7.2         | più recente  |
| UbuntuLTS | Canonici | Server Ubuntu | 14.04.4-LTS | più recente  |

Le sezioni seguenti Usa il `UbuntuLTS` alias per l'opzione **ImageURN** (`-Q`) per distribuire un Server di risultati Ubuntu 14.04.4.

Precedente `quick-create` riportato di seguito viene evidenziato solo il `-M` contrassegno per identificare la chiave pubblica SSH da caricare durante la disattivazione della password SSH, in modo richiesto per gli argomenti seguenti:

- nome del gruppo risorse (qualsiasi stringa è in genere appropriato per il primo gruppo di risorse Azure)
- Nome macchina virtuale
- percorso (`westus` o `westeurope` sono predefiniti buone)
- Linux (per comunicare Azure che il sistema operativo desiderato)
- nome utente

Nell'esempio seguente consente di specificare tutti i valori in modo che senza ulteriormente richiesta necessaria. Se si dispone di un `~/.ssh/id_rsa.pub` come un ssh rsa formato file chiave pubblica, vale come:

```bash
azure vm quick-create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--admin-username myAdminUser \
--ssh-public-file ~/.ssh/id_rsa.pub \
--image-urn UbuntuLTS
```

L'output dovrebbe essere simile al seguente blocco di output:

```bash
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "myVM"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "myVM"
+ Looking up the VM "myVM"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM
data:      User Name                     :myAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

## <a name="log-in-to-the-new-vm"></a>Accedere alla nuova macchina virtuale

Accedere alle macchine Virtuali utilizzando l'indirizzo IP pubblico elencato nell'output. È anche possibile utilizzare il nome di dominio completo (FQDN) che è presente nell'elenco:

```bash
ssh -i ~/.ssh/id_rsa.pub ahmet@138.91.247.29
```

La procedura di accesso dovrebbe essere simile alla seguente blocco di output:

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

myAdminUser@myVM:~$
```

## <a name="next-steps"></a>Passaggi successivi

Il `azure vm quick-create` comando è il modo per distribuire rapidamente una macchina virtuale in modo che è possibile accedere a una shell bash e iniziare a lavorare. Tuttavia, l'utilizzo `vm quick-create` non fornisce un controllo completo né indica consentono di creare un ambiente più complesso.  Per distribuire un VM Linux personalizzate per l'infrastruttura, è possibile seguire uno degli articoli seguenti:

- [Utilizzare un modello di gestione risorse Azure per creare una distribuzione specifica](virtual-machines-linux-cli-deploy-templates.md)
- [Creare l'ambiente personalizzato per un VM Linux usando i comandi CLI Azure direttamente](virtual-machines-linux-create-cli-complete.md)
- [Creare un SSH protetto in modo esclusivo Linux macchine Virtuali in Azure uso dei modelli](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

È inoltre possibile [utilizzare il `docker-machine` driver Azure con diversi comandi per creare rapidamente una VM Linux come host docker](virtual-machines-linux-docker-machine.md).
