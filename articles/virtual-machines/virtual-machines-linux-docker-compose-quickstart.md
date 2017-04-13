<properties
   pageTitle="Docker e scrittura in una macchina virtuale | Microsoft Azure"
   description="Breve introduzione all'utilizzo di scrittura e Docker in macchine virtuali Linux in Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="iainfou"/>

# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-on-an-azure-virtual-machine"></a>Guida introduttiva a Docker e scrittura per definire ed eseguire un'applicazione contenitore multiple in un computer virtuale Azure

Iniziare a utilizzare Docker e [scrittura](http://github.com/docker/compose) per definire ed eseguire un'applicazione complessa in un computer virtuale Linux in Azure. Con scrittura, utilizzare un file di testo semplice per definire un'applicazione composta da più contenitori Docker. È quindi selezione l'applicazione in un unico comando che consente a tutti gli elementi per distribuire l'ambiente definito. 

Ad esempio, in questo articolo viene illustrato come impostare rapidamente un blog WordPress con un database SQL di MariaDB su un VM Ubuntu back-end. È possibile usare anche scrittura per configurare le applicazioni più complesse.


## <a name="step-1-set-up-a-linux-vm-as-a-docker-host"></a>Passaggio 1: Configurare un VM Linux come host Docker

È possibile utilizzare varie procedure Azure e immagini disponibili o Gestione risorse modelli di Azure Marketplace per creare una VM Linux e impostarla come host Docker. Ad esempio, vedere [utilizzando l'estensione di macchine Virtuali Docker per distribuire l'ambiente](virtual-machines-linux-dockerextension.md) per creare rapidamente una VM Ubuntu con estensione macchine Virtuali di Azure Docker utilizzando un [modello di Guida introduttiva](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Quando si usa l'estensione macchine Virtuali Docker, di una macchina virtuale viene impostata automaticamente come host Docker e scrittura è già installato. Nell'esempio riportato in questo articolo viene illustrato come utilizzare l' [interfaccia della riga di comando Azure per Mac, Linux e Windows](../xplat-cli-install.md) (CLI Azure) in modalità di gestione risorse per creare la macchina virtuale.

Il comando base dal documento precedente crea un gruppo di risorse denominato `myResourceGroup` nel `West US` posizione e lo distribuisce una macchina virtuale con estensione macchine Virtuali di Azure Docker installato:

```bash
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

## <a name="step-2-verify-that-compose-is-installed"></a>Passaggio 2: Verificare che sia installato scrittura

Al termine della distribuzione, SSH per il nuovo host Docker utilizzando il DNS è stato specificato durante la distribuzione assegnare un nome. È possibile utilizzare `azure vm show -g myDockerResourceGroup -n myDockerVM` per visualizzare i dettagli della macchina virtuale, compresi il nome DNS.

Per verificare che sia installato scrittura nella macchina virtuale, eseguire il comando seguente:

```bash
docker-compose --version
```

Viene visualizzato output simile a `docker-compose 1.6.2, build 4d72027`.

>[AZURE.TIP] Se è stato utilizzato un altro metodo per creare un host Docker ed è necessario installare scrittura personalmente, vedere la [documentazione di scrittura](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="step-3-create-a-docker-composeyml-configuration-file"></a>Passaggio 3: Creare un file di configurazione docker compose.yml

Creare un `docker-compose.yml` file, è sufficiente un file di testo, per definire i contenitori Docker per l'esecuzione nella macchina virtuale. Il file specifica l'immagine da eseguire su ciascun contenitore (o può trattarsi di compilazione di un Dockerfile), variabili di ambiente necessarie e le dipendenze, porte e i collegamenti tra i contenitori. Per informazioni dettagliate sulla sintassi file yml, vedere [informazioni di riferimento file scrittura](http://docs.docker.com/compose/yml/).

Creare il `docker-compose.yml` file come indicato di seguito:

```bash
touch docker-compose.yml
```

Utilizzare un editor di testo per aggiungere alcuni dati al file. Nell'esempio seguente viene utilizzato il `vi` editor:

```bash
vi docker-compose.yml
```

Incollare il file di testo nell'esempio seguente. Questa configurazione utilizza le immagini [DockerHub del Registro di sistema](https://registry.hub.docker.com/_/wordpress/) per installare WordPress (il sistema di gestione Apri origine blog e contenuto) e un database SQL di MariaDB di back-end collegato. Immettere il proprio `MYSQL_ROOT_PASSWORD` come indicato di seguito:

```bash
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="step-4-start-the-containers-with-compose"></a>Passaggio 4: Iniziare i contenitori di scrittura

Nella stessa directory come il `docker-compose.yml` file, eseguire il comando seguente (a seconda dell'ambiente, potrebbe essere necessario eseguire `docker-compose` utilizzando `sudo`.):

```bash
docker-compose up -d

```

Il comando Avvia contenitori Docker specificati `docker-compose.yml`. È necessario eseguire un o due minuti per completare questo passaggio. Viene visualizzato output simile al seguente:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

>[AZURE.NOTE] Assicurarsi di usare l'opzione **-d** all'avvio in modo che i contenitori di esecuzione in background continua.

Per verificare che i contenitori sono backup, digitare `docker-compose ps`. Verrà visualizzata simile al seguente:

```bash
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

È ora possibile connettersi a WordPress direttamente in macchine Virtuali sulla porta 80. Aprire un web browser e immettere il nome DNS della macchina virtuale (ad esempio `http://myresourcegroup.westus.cloudapp.azure.com`). Verrà visualizzata la WordPress schermata start, in cui è possibile completare l'installazione e iniziare a utilizzare l'applicazione.

![Schermata start WordPress][wordpress_start]


## <a name="next-steps"></a>Passaggi successivi

* Passare alla [Guida dell'utente di estensione macchine Virtuali Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) per altre opzioni per configurare la macchina virtuale Docker Docker e scrittura. Ad esempio, un'opzione possibile consiste per caricare il file yml scrittura (convertito in JSON) direttamente nella configurazione dell'estensione macchine Virtuali Docker.
* Controllare il [riferimento della riga di comando di scrittura](http://docs.docker.com/compose/reference/) e [manuale dell'utente](http://docs.docker.com/compose/) per altri esempi di creazione e distribuzione App contenitore multiple.
* Utilizzare un modello di gestione risorse di Azure, il proprio o uno contribuito dalla [community](https://azure.microsoft.com/documentation/templates/), per distribuire una macchina virtuale Azure con Docker e un'applicazione configurata con scrittura. Ad esempio, il modello di [distribuzione un blog WordPress con Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) utilizza Docker e scrittura distribuire rapidamente WordPress con un back-end MySQL in un VM Ubuntu.
* Provare a integrazione Docker comporre con un cluster [Particle Swarm Docker](virtual-machines-linux-docker-swarm.md) . Per scenari, vedere [Uso comporre con sciame](https://docs.docker.com/compose/swarm/) .

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png
