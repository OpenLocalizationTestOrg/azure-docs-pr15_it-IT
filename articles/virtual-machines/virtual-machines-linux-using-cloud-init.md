<properties
    pageTitle="Utilizzo cloud-inizializzazione per personalizzare una VM Linux durante la creazione di | Microsoft Azure"
    description="Utilizzo di cloud-inizializzazione per personalizzare una VM Linux durante la creazione."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="v-livech"
/>

# <a name="using-cloud-init-to-customize-a-linux-vm-during-creation"></a>Per personalizzare una VM Linux durante la creazione di usare cloud-inizializzazione

In questo articolo illustra come creare uno script cloud-inizializzazione per impostare il nome host, aggiornamenti installati e gestire gli account utente.  Gli script di cloud-inizializzazione sono denominati durante la creazione di macchine Virtuali da Azure CLI.  Richiede l'articolo:

- un account Azure ([ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- [Azure CLI](../xplat-cli-install.md) ha eseguito l'accesso con `azure login`.

- la modalità di gestione di risorse Azure _deve essere_ Azure CLI `azure config mode arm`.

## <a name="quick-commands"></a>Comandi rapidi

Creare uno script init.txt cloud che imposta il nome host, Aggiorna tutti i pacchetti e aggiunge un utente sudo Linux.

```bash
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
Creare un gruppo di risorse per avviare macchine virtuali in.

```bash
azure group create myResourceGroup westus
```

Creare una VM Linux usando cloud-inizializzazione di configurazione durante l'avvio.

```bash
azure vm create \
-g myResourceGroup \
-n myVM \
-l westus \
-y Linux \
-f myVMnic \
-F myVNet \
-P 10.0.0.0/22 \
-j mySubnet \
-k 10.0.0.0/24 \
-Q canonical:ubuntuserver:14.04.2-LTS:latest \
-M ~/.ssh/id_rsa.pub \
-u myAdminUser \
-C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Descrizione dettagliata

### <a name="introduction"></a>Introduzione

Quando si avvia una nuova VM Linux, si ottengono standard Linux VM con niente personalizzate o pronto adatti alle proprie esigenze. [Cloud-inizializzazione](https://cloudinit.readthedocs.org) è una modalità standard per inserire in tale VM Linux, un impostazioni di script o configurazione durante l'avvio per la prima volta.

In Azure, un in tre modi diversi per apportare modifiche in un VM Linux viene distribuito o avviato.

- Inserire gli script utilizzando cloud-inizializzazione.
- Inserire gli script utilizzando l' [Estensione VMAccess](virtual-machines-linux-using-vmaccess-extension.md)Azure.
- Un modello di Azure tramite cloud-inizializzazione.
- Un modello di Azure tramite [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md).

Per inserire script in qualsiasi momento dopo l'avvio:

- SSH per eseguire direttamente i comandi
- Inserire gli script utilizzando [l'Estensione VMAccess](virtual-machines-linux-using-vmaccess-extension.md)di Azure, imperativo o in un modello di Azure
- Strumenti di gestione di configurazione, ad esempio Ansible, sale, Chef e Marionetta.

>[AZURE.NOTE]: VMAccess Extension executes a script as root in the same way using SSH can.  However, using the VM extension enables several features that Azure offers that can be useful depending upon your scenario.

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Disponibilità cloud-inizializzazione in macchine Virtuali di Azure rapidi: consente di creare gli alias immagine:

| Alias     | Publisher | Offerta        | SKU         | Versione | cloud-inizializzazione |
|:----------|:----------|:-------------|:------------|:--------|:-----------|
| CentOS    | OpenLogic | Centos       | 7.2         | più recente  | No         |
| CoreOS    | CoreOS    | CoreOS       | Stabile      | più recente  | Sì        |
| Debian    | credativ  | Debian       | 8           | più recente  | No         |
| openSUSE  | SUSE      | openSUSE     | 13.2        | più recente  | No         |
| RHEL      | Red Hat    | RHEL         | 7.2         | più recente  | No         |
| UbuntuLTS | Canonici | UbuntuServer | 14.04.4-LTS | più recente  | Sì        |

Microsoft sta collaborando con i partner per ottenere cloud-inizializzazione inclusi e l'uso di immagini che forniscono in Azure.

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Aggiunta di uno script cloud-inizializzazione per la creazione di macchine Virtuali con CLI Azure

Per avviare uno script cloud-inizializzazione durante la creazione di una macchina virtuale in Azure, specificare il file cloud-inizializzazione mediante CLI Azure `--custom-data` passare.

Creare un gruppo di risorse per avviare macchine virtuali in.

```bash
azure group create myResourceGroup westus
```

Creare una VM Linux usando cloud-inizializzazione di configurazione durante l'avvio.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Creazione di uno script cloud-inizializzazione per impostare il nome host di una VM Linux

Una delle impostazioni più importanti e più semplice per qualsiasi VM Linux è il nome host. È possibile facilmente impostare questo utilizzando cloud-inizializzazione con questo script.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Script di esempio cloud-inizializzazione denominato `cloud_config_hostname.txt`.

``` bash
#cloud-config
hostname: myservername
```

Durante l'avvio iniziale della macchina virtuale, questo script cloud-inizializzazione imposta il nome host `myservername`.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_hostname.txt
```

Accesso e verificare che il nome host del nuova macchina virtuale.

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>Creazione di uno script inizializzazione cloud per aggiornare Linux

Per la sicurezza, si desidera il VM Ubuntu aggiornare al primo avvio.  Utilizzo di cloud-inizializzazione che farlo con lo script di completamento, a seconda della distribuzione Linux in uso.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Script di esempio cloud-inizializzazione `cloud_config_apt_upgrade.txt` per la famiglia Debian

```bash
#cloud-config
apt_upgrade: true
```

Quando si è avviata Linux, tutti i pacchetti installati vengono aggiornati tramite `apt-get`.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_apt_upgrade.txt
```

Accesso e verificare che tutti i pacchetti vengono aggiornati.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>Creazione di uno script cloud-inizializzazione per aggiungere un utente a Linux

Una delle prime attività in qualsiasi nuova VM Linux è per aggiungere un utente per se stessi o per evitare l'utilizzo di `root`. Chiavi SSH sono procedure consigliate per la sicurezza e per facilità di utilizzo e vengono aggiunti ai `~/.ssh/authorized_keys` file con questo script cloud-inizializzazione.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Script di esempio cloud-inizializzazione `cloud_config_add_users.txt` per Debian famiglia

```bash
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Dopo che ha avviato Linux, tutti gli utenti nell'elenco vengono creati e aggiunti al gruppo sudo.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_add_users.txt
```

Accesso e verificare che l'utente appena creato.

```bash
ssh myVM
cat /etc/group
```

Output

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>Passaggi successivi

Cloud-inizializzazione diventa un modo standard per modificare le VM Linux all'avvio. Azure include inoltre estensioni macchine Virtuali, che consentono di modificare il LinuxVM avvio o mentre è in esecuzione. Ad esempio, è possibile utilizzare VMAccessExtension Azure per ripristinare le informazioni utente o SSH mentre è in esecuzione la macchina virtuale. Con cloud-inizializzazione, sarà necessario riavviare il computer per reimpostare la password.

[Informazioni sulle funzionalità e le estensioni macchina virtuale](virtual-machines-linux-extensions-features.md)

[Gestire gli utenti, SSH e controllo o ripristinare dischi in macchine virtuali Linux Azure utilizzando l'estensione VMAccess](virtual-machines-linux-using-vmaccess-extension.md)
