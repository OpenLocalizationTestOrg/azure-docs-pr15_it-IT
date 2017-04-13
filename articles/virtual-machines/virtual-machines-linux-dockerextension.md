<properties
   pageTitle="Utilizzando l'estensione macchine Virtuali di Azure Docker | Microsoft Azure"
   description="Informazioni su come utilizzare l'estensione macchine Virtuali Docker in modo rapido e sicuro distribuire un ambiente Docker in Azure utilizzare i modelli di Manager delle risorse."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/25/2016"
   ms.author="iainfou"/>

# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Creare un ambiente Docker in Azure utilizzando l'estensione macchine Virtuali Docker
Docker è una gestione dei contenitori comuni e immagini piattaforma che consente di eseguire rapidamente dei contenitori di Linux (e anche Windows). In Azure, esistono diversi modi per distribuire Docker in base alle proprie esigenze. In questo articolo viene illustrato l'utilizzo di modelli di Manager delle risorse di Azure e l'estensione macchine Virtuali Docker. 

Per ulteriori informazioni sui diversi metodi di distribuzione, incluso l'utilizzo di computer Docker e servizi contenitore di Windows Azure, vedere gli articoli seguenti:

- Al prototipo rapidamente un'app, è possibile creare un singolo host Docker utilizzando [Docker macchina](./virtual-machines-linux-docker-machine.md).
- Per gli ambienti di dimensioni maggiori e più stabili, è possibile utilizzare l'estensione macchine Virtuali Docker Azure, che supporta anche [Scrivere Docker](https://docs.docker.com/compose/overview/) per generare distribuzioni contenitore coerenti. Dettagli in questo articolo utilizzando l'estensione macchine Virtuali Docker Azure.
- Per creare ambienti ibridi di produzione, scalable che forniscono altri strumenti di pianificazione e la gestione, è possibile distribuire un [cluster Particle Swarm Docker su servizi di Windows Azure contenitore](../container-service/container-service-deployment.md).


## <a name="azure-docker-vm-extension-overview"></a>Panoramica di estensione macchine Virtuali Docker Azure
L'estensione macchine Virtuali di Azure Docker installa e configura daemon Docker, client Docker e Docker comporre nel Linux macchine (). Utilizzando l'estensione macchine Virtuali Docker Azure, si dispone di più controllo e funzioni rispetto a semplicemente utilizzando Docker computer o la creazione dell'host di Docker personalmente. Queste caratteristiche aggiuntive, ad esempio [Docker comporre](https://docs.docker.com/compose/overview/), verificare l'estensione di macchine Virtuali di Azure Docker adatto per ambienti di produzione o sviluppo sia più efficace.

Modelli di gestione risorse Azure definiscono l'intera struttura dell'ambiente. Modelli consentono di creare e configurare risorse, ad esempio l'host Docker macchine virtuali, lo spazio di archiviazione, controlli di accesso basato sui ruoli (RBAC) e diagnostica. È possibile riutilizzare questi modelli per creare distribuzioni aggiuntive in modo coerente. Per ulteriori informazioni sui modelli e gestione di risorse di Azure, vedere [Panoramica di gestione risorse](../azure-resource-manager/resource-group-overview.md). 


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Distribuire un modello con estensione macchine Virtuali Docker Azure
Di seguito è possibile utilizzare un modello di Guida introduttiva esistente per creare un VM Ubuntu che utilizza l'estensione macchine Virtuali di Azure Docker per installare e configurare l'host Docker. È possibile visualizzare il modello qui: [distribuzione semplice di una macchina virtuale Ubuntu con Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

È necessario [CLI Azure più recente](../xplat-cli-install.md) installato e registrato con la modalità di gestione risorse come indicato di seguito:

```
azure config mode arm
```

Distribuire il modello utilizzando CLI Azure, che specifica il modello di URI. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nel `WestUS` posizione. Utilizzare il proprio nome gruppo di risorse e posizione come indicato di seguito:

```
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Rispondere alle istruzioni visualizzate per assegnare un nome proprio account di archiviazione, specificare un nome utente e password e specificare un nome DNS. L'output è simile alla seguente:

```
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicip
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

Restituisce Azure CLI al prompt dei comandi dopo avere inserito solo alcuni secondi, ma l'host Docker comunque viene creato e configurato per l'estensione macchine Virtuali Docker Azure. Bastano pochi minuti per la distribuzione alla fine. È possibile visualizzare informazioni dettagliate sullo stato di host Docker utilizzando il `azure vm show` comando.

Nell'esempio seguente controlla lo stato di macchine Virtuali denominata `myDockerVM` (il nome predefinito dal modello - non cambia questo nome) nel gruppo di risorse denominato `myResourceGroup`. Immettere il nome del gruppo di risorse che è stato creato nel passaggio precedente:

```bash
azure vm show -g myResourceGroup -n myDockerVM
```

L'output del `azure vm show` comando è simile alla seguente:

```
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicip.westus.cloudapp.azure.com]
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

Nella parte superiore dell'output, viene visualizzato il `ProvisioningState` della macchina virtuale. Quando viene visualizzata `Succeeded`, terminata la distribuzione e sarà possibile SSH per la macchina virtuale.

Verso la fine dell'output, `FQDN` viene visualizzato il nome di dominio completo dell'host Docker. Il nome di dominio completo è consente di eseguire SSH per l'host Docker nei passaggi rimanenti.


## <a name="deploy-your-first-nginx-container"></a>Distribuire il primo contenitore nginx
Al termine della distribuzione, SSH per il nuovo host Docker dal computer locale. Immettere il proprio nome utente e il nome di dominio completo come indicato di seguito:

```bash
ssh ops@mypublicip.westus.cloudapp.azure.com
```

Dopo aver effettuato l'accesso all'host dei Docker, eseguiamo un contenitore nginx:

```
sudo docker run -d -p 80:80 nginx
```

L'output è simile alla seguente come immagine nginx viene scaricata e avviato un contenitore:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Controllare lo stato dei contenitori di esecuzione sull'host Docker come indicato di seguito:

```
sudo docker ps
```

L'output è simile alla seguente, che mostra che il contenitore nginx è in esecuzione e porte TCP 80 e 443 e inoltrati:

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Per visualizzare il contenitore in azione, aprire un web browser e immettere il nome FQDN dell'host Docker:

![Contenitore ngnix in esecuzione](./media/virtual-machines-linux-dockerextension/nginxrunning.png)


## <a name="azure-docker-vm-extension-template-reference"></a>Riferimento a un modello estensione macchine Virtuali Docker Azure
Nell'esempio precedente vengono utilizzate un modello di Guida introduttiva esistente. È inoltre possibile distribuire l'estensione macchine Virtuali Docker Azure con modelli di Manager delle risorse. A tale scopo, aggiungere le operazioni seguenti per i modelli di Manager delle risorse, che definisce il `vmName` della macchina virtuale in modo appropriato:

```
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

È possibile trovare più dettagliata sull'utilizzo dei modelli Manager delle risorse, leggere [Panoramica di gestione di risorse Azure](../azure-resource-manager/resource-group-overview.md).


## <a name="next-steps"></a>Passaggi successivi
Si consiglia di [configurare il daemon Docker porte TCP](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), sulla [protezione Docker](https://docs.docker.com/engine/security/security/)o distribuire contenitori tramite [Docker comporre](https://docs.docker.com/compose/overview/). Per ulteriori informazioni sull'estensione macchine Virtuali di Docker Azure stesso, vedere il [progetto GitHub](https://github.com/Azure/azure-docker-extension/).

Leggere altre informazioni sulle altre opzioni di distribuzione Docker Azure:

- [Utilizzare Docker computer con il driver di Azure](./virtual-machines-linux-docker-machine.md)  
- [Guida introduttiva a Docker e scrittura per definire ed eseguire un'applicazione contenitore multiple in un computer virtuale Azure](virtual-machines-linux-docker-compose-quickstart.md).
- [Distribuire un cluster di servizio contenitore Azure](../container-service/container-service-deployment.md)
