<properties
   pageTitle="Installare MongoDB in una macchina virtuale Linux | Microsoft Azure"
   description="Informazioni su come installare e configurare MongoDB in un computer virtuale Linux in Azure utilizzando il modello di distribuzione di Manager delle risorse."
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
   ms.date="09/29/2016"
   ms.author="iainfou"/>

# <a name="install-and-configure-mongodb-on-a-linux-vm-in-azure"></a>Installare e configurare MongoDB su una macchina virtuale Linux in Azure
[MongoDB](http://www.mongodb.org) sui database NoSQL Apri origine e prestazioni elevate popolari. In questo articolo viene illustrato come installare e configurare MongoDB su una VM Linux in Azure utilizzando il modello di distribuzione di Manager delle risorse. Sono riportati alcuni esempi che illustrano in dettaglio come per:

- [Installare e configurare un'istanza di MongoDB base manualmente](#manually-install-and-configure-mongodb-on-a-vm)
- [Creare un'istanza MongoDB base utilizzando un modello di Manager delle risorse](#create-basic-mongodb-instance-on-centos-using-a-template)
- [Creare una complessa MongoDB sharded cluster con replica imposta utilizzando un modello di Manager delle risorse](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="prerequisites"></a>Prerequisiti
In questo articolo è necessario quanto segue:

- un account Azure ([ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)).
- [Azure CLI](../xplat-cli-install.md) ha eseguito l'accesso con`azure login`
- la CLI Azure *deve essere* in modalità di gestione di risorse Azure utilizzando`azure config mode arm`


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Installare e configurare MongoDB su una macchina virtuale manualmente
MongoDB [istruzioni di installazione](https://docs.mongodb.com/manual/administration/install-on-linux/) per distros Linux inclusi Red Hat / CentOS, SUSE, Ubuntu e Debian. Nell'esempio seguente viene creato un `CoreOS` macchine Virtuali mediante una chiave SSH archiviata in `.ssh/azure_id_rsa.pub`. Rispondere alle istruzioni visualizzate per nome dell'account di archiviazione, nome DNS e le credenziali di amministratore:

```bash
azure vm quick-create --ssh-publickey-file .ssh/azure_id_rsa.pub --image-urn CentOS
```

Accedere a macchine Virtuali utilizzando l'indirizzo IP pubblico visualizzata alla fine della fase di creazione di macchine Virtuali precedente:

```bash
ssh ops@40.78.23.145
```

Per aggiungere le origini di installazione per MongoDB, creare un `yum` file di archivio come descritto di seguito:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.2.repo
```

Aprire il file repo MongoDB per la modifica. Aggiungere le righe seguenti:

```bash
[mongodb-org-3.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.2/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.2.asc
```

Installare con MongoDB `yum` come indicato di seguito:

```bash
sudo yum install -y mongodb-org
```

Per impostazione predefinita, SELinux viene applicata sulle immagini CentOS che impedisce l'accesso al MongoDB. Installare gli strumenti di gestione dei criteri e configurare SELinux per consentire MongoDB operare sulla porta predefinita 27017 come indicato di seguito. 

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Avviare il servizio MongoDB come indicato di seguito:

```bash
sudo service mongod start
```

Verificare l'installazione di MongoDB connettendosi tramite locale `mongo` client:

```bash
mongo
```

Testare l'istanza MongoDB aggiungendo alcuni dati e quindi la ricerca:

```
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Se lo si desidera, configurare MongoDB per l'avvio automatico durante un riavvio del sistema:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Creare l'istanza MongoDB base su CentOS utilizzando un modello
È possibile creare un'istanza di MongoDB base in una singola macchina virtuale CentOS utilizzando il modello di Guida introduttiva di Azure seguenti da Github. Questo modello utilizza l'estensione di Script personalizzato per Linux per aggiungere un `yum` repository a macchine Virtuali CentOS appena creato e quindi installare MongoDB.

- [Istanza di base MongoDB CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Nell'esempio seguente viene creato un gruppo di risorse con il nome `myResourceGroup` nel `WestUS` area geografica. Immettere i valori come indicato di seguito:

```bash
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [AZURE.NOTE] CLI Azure tornare a una richiesta all'interno di alcuni secondi di creazione di distribuzione, ma sono necessari alcuni minuti per completare l'installazione e configurazione. Controllare lo stato della distribuzione con `azure group deployment show myResourceGroup`, immettere il nome del gruppo di risorse di conseguenza. Attendere il `ProvisioningState` Mostra 'Completato' prima di tentare di SSH per la macchina virtuale.

Al termine della distribuzione, SSH per la macchina virtuale. Ottenere l'indirizzo IP dell'uso di macchine Virtuali di `azure vm show` comando come illustrato nell'esempio seguente:

```bash
azure vm show --resource-group myResourceGroup --name myVM
```

Verso la fine dell'output, il `Public IP address` viene visualizzato. SSH per la macchina virtuale con l'indirizzo IP delle macchine Virtuali:

```bash
ssh ops@138.91.149.74
```

Verificare l'installazione di MongoDB connettendosi tramite locale `mongo` client come descritto di seguito:

```bash
mongo
```

Testare l'istanza aggiungendo alcuni dati e la ricerca come indicato di seguito:

```
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Creare un Cluster di Sharded MongoDB complessa sul CentOS utilizzando un modello
È possibile creare un cluster di sharded MongoDB complesso utilizzando il modello Guida introduttiva di Azure seguenti Github. Questo modello segue le [procedure consigliate cluster sharded MongoDB](https://docs.mongodb.com/manual/core/sharded-cluster-components/) per fornire ridondanza e disponibilità. Il modello crea due shards, con tre nodi in ogni set di replica. Anche creato una configurazione server replica set con tre nodi, più due `mongos` server router per garantire la coerenza con le applicazioni da nel shards.

- [MongoDB Sharding Cluster su CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [AZURE.WARNING] Distribuzione di questo cluster sharded MongoDB complesso richiede più di 20 core, che è in genere il numero di base predefinito per ogni area geografica per una sottoscrizione. Aprire una richiesta di supporto Azure per aumentare il numero di base.

Nell'esempio seguente viene creato un gruppo di risorse con il nome `myResourceGroup` nel `WestUS` area geografica. Immettere i valori come indicato di seguito:

```bash
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [AZURE.NOTE] CLI Azure tornare a una richiesta all'interno di alcuni secondi di creazione di distribuzione, ma l'installazione e configurazione può richiedere più di un'ora per completare. Controllare lo stato della distribuzione con `azure group deployment show myResourceGroup`, regolare di conseguenza il nome del gruppo di risorse. Attendere il `ProvisioningState` Mostra 'Completato' prima di connettersi alle macchine virtuali.


## <a name="next-steps"></a>Passaggi successivi
In questi esempi, è possibile connettersi all'istanza MongoDB localmente da macchina virtuale. Se si desidera connettersi all'istanza MongoDB da un'altra macchine Virtuali o della rete, assicurarsi di appropriato [vengono create le regole gruppo di sicurezza di rete](virtual-machines-linux-nsg-quickstart.md).

Per ulteriori informazioni sulla creazione di modelli, vedere [Panoramica di gestione di risorse Azure](../azure-resource-manager/resource-group-overview.md).

I modelli di gestione di risorse Azure utilizzano l'estensione di Script personalizzati per scaricare ed eseguire script nelle macchine virtuali. Per ulteriori informazioni, vedere [utilizzando l'estensione di Script personalizzati Azure con macchine virtuali Linux](virtual-machines-linux-extensions-customscript.md).