<properties
    pageTitle="Creare una VM Linux utilizzando un modello di Azure | Microsoft Azure"
    description="Creare una VM Linux su Azure utilizzando un modello di gestione di risorse Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/24/2016"
    ms.author="v-livech"/>

# <a name="create-a-linux-vm-using-an-azure-template"></a>Creare una VM Linux utilizzando un modello di Azure

In questo articolo viene illustrato come distribuire rapidamente una macchina virtuale Linux su Azure utilizzando un modello di Azure.  Richiede l'articolo:

- un account Azure ([ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- [Azure CLI](../xplat-cli-install.md) ha eseguito l'accesso con `azure login`.

- la modalità di gestione di risorse Azure _deve essere_ Azure CLI `azure config mode arm`.

È possibile distribuire un modello di Linux VM anche rapidamente tramite il [portale di Azure](virtual-machines-linux-quick-create-portal.md).

## <a name="quick-command-summary"></a>Comando rapido riepilogo

```bash
azure group create \
-n myResourceGroup \
-l westus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>Descrizione dettagliata

Modelli consentono di creare macchine virtuali in Azure con impostazioni che si desidera personalizzare durante il lancio, impostazioni, ad esempio nomi utente e nome host. In questo articolo è stiamo avvio di un modello di Azure che utilizzano un VM Ubuntu insieme a un gruppo di sicurezza di rete (NSG) con porta 22 aperto per SSH.

Modelli di gestione risorse Azure sono file JSON che possono essere usati per eseguire semplici operazioni variante come avviare un VM Ubuntu come completato in questo articolo.  Modelli di Azure possono essere utilizzati anche per costruire configurazioni complesse di Azure di interi ambienti come uno stack di distribuzione di test, sviluppo o di produzione.

## <a name="create-the-linux-vm"></a>Creare la macchina virtuale Linux

Nell'esempio seguente viene illustrato come chiamare `azure group create` per creare un gruppo di risorse e distribuire un VM Linux protetti SSH nello stesso momento con [questo modello di gestione risorse di Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Tenere presente che dell'esempio è necessario utilizzare nomi univoci per il proprio ambiente. Questo esempio viene usata `myResourceGroup` come nome del gruppo risorse e `myVM` come nome macchina virtuale.

```bash
azure group create \
--name myResourceGroup \
--location westus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

L'output dovrebbe essere simile al seguente blocco di output:

```bash
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Questo esempio distribuita una macchina virtuale mediante la `--template-uri` parametro.  È anche possibile scaricare o creare un modello in locale e passare il modello tramite il `--template-file` parametro di un percorso al file del modello come argomento. CLI Azure viene richiesto per i parametri necessari affinché il modello.

## <a name="next-steps"></a>Passaggi successivi

Eseguire una ricerca nella [raccolta modelli](https://azure.microsoft.com/documentation/templates/) per scoprire quali Framework app per distribuire successivo.
