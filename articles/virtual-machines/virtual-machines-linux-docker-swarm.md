<properties
   pageTitle="Introduzione all'utilizzo di docker con sciame su Azure"
   description="In questo articolo viene descritto come creare un gruppo di macchine virtuali con l'estensione di macchine Virtuali Docker e utilizzare sciame per creare un cluster di Docker."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="01/04/2016"
   ms.author="rasquill"/>

# <a name="how-to-use-docker-with-swarm"></a>Come utilizzare docker con sciame

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


In questo argomento viene illustrato un modo semplice per utilizzare [docker](https://www.docker.com/) con [Particle swarm](https://github.com/docker/swarm) per creare un cluster gestito sciame in Azure. Crea quattro macchine virtuali di Azure, uno che funge da gestore sciame e tre come parte del cluster di host docker. Al termine, è possibile utilizzare sciame per vedere il cluster e quindi iniziare a utilizzare docker su di esso. Inoltre, le chiamate di Azure CLI in questo argomento utilizzano la modalità di gestione (asm) del servizio. 

> [AZURE.NOTE] In questo argomento viene usata docker con sciame e la CLI Azure *senza* utilizzare **docker computer** per poter visualizzare dell'interazione tra i diversi strumenti ma restano indipendenti. **computer docker** sono **-Particle swarm** opzioni che consentono di utilizzare **docker computer** per direttamente aggiungere nodi a uno sciame. Ad esempio, vedere la documentazione [docker computer](https://github.com/docker/machine) . Notato **docker computer** che esegue contro macchine virtuali di Azure, vedere [come utilizzare docker computer con Azure](virtual-machines-linux-docker-machine.md).

## <a name="create-docker-hosts-with-azure-virtual-machines"></a>Creare docker host con macchine virtuali di Azure

In questo argomento consente di creare quattro macchine virtuali, ma è possibile utilizzare qualsiasi numero desiderato. Chiamare i seguenti con * &lt;password&gt; * sostituito la password che si è scelto.

    azure vm docker create swarm-master -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-1 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-2 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-3 -l "East US" -e 22 $imagename ops <password>

Al termine sarà possibile utilizzare **macchine virtuali di azure elenco** per vedere le macchine virtuali Azure:

    $ azure vm list | grep "swarm-[mn]"
    data:    swarm-master     ReadyRole           East US       swarm-master.cloudapp.net                               100.78.186.65
    data:    swarm-node-1     ReadyRole           East US       swarm-node-1.cloudapp.net                               100.66.72.126
    data:    swarm-node-2     ReadyRole           East US       swarm-node-2.cloudapp.net                               100.72.18.47  
    data:    swarm-node-3     ReadyRole           East US       swarm-node-3.cloudapp.net                               100.78.24.68  

## <a name="installing-swarm-on-the-swarm-master-vm"></a>L'installazione di sciame nello schema sciame macchine Virtuali

In questo argomento viene utilizzato il [modello di contenitore di installazione dal docker Particle swarm documentazione](https://github.com/docker/swarm#1---docker-image) - ma è possibile anche SSH **sciame schema**. In questo modello, **Particle swarm** viene scaricata come contenitore docker esecuzione sciame. Sotto, si esegue questo passaggio *in modalità remota da nostro portatile utilizzando docker* per connettere lo **schema sciame** macchine Virtuali e comunicare, è possibile utilizzare il comando creazione id cluster **sciame creare**. L'id di cluster è come **Particle swarm** individua i membri del gruppo sciame. (È anche possibile duplicare l'archivio e compilarlo se stessi, che forniscono il controllo completo e attivare il debug.)

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm create
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    36731c17189fd8f450c395db8437befd

L'ultima riga corrisponde all'id cluster; copiarlo in un punto qualsiasi in quanto si verrà utilizzato di nuovo quando si partecipa a nodo macchine virtuali allo schema sciame per creare "sciame". In questo esempio, l'id di cluster è **36731c17189fd8f450c395db8437befd**.

> [AZURE.NOTE] Per chiarire, verrà utilizzato il nostro installazione docker locale per la connessione allo **schema sciame** macchine Virtuali di Azure e istruzione **sciame schema** per scaricare, installare ed eseguire il comando **Crea** che restituisce l'id di cluster usato per scopi di individuazione in un secondo momento.
<!-- -->
> Per verificarlo, eseguire `docker -H tcp://` * &lt;hostname&gt; * ` images` per elencare i processi di contenitore computer **sciame schema** e in un altro nodo per il confronto (perché è stato eseguito il comando sciame precedente con l'opzione **- rm** , al termine che è stato rimosso il contenitore, pertanto utilizzando **docker ps - un** non è possibile restituire un valore).:


        $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        swarm               latest              92d78d321ff2        11 days ago         7.19 MB
        $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        $
<P />
>Se si ha familiarità con **docker**, si saprà che gli altri nodi non dispongano di voci in quanto non sono presenti immagini sono stati scaricati e ancora eseguiti.

## <a name="join-the-node-vms-to-our-docker-cluster"></a>Aggiungere il nodo macchine virtuali il cluster docker

Per ogni nodo elencare le informazioni sull'endpoint usa CLI Azure. Di seguito per farlo, che per l'host docker **sciame-nodo-1** per ottenere porta docker del nodo.

    $ azure vm endpoint list swarm-node-1
    info:    Executing command vm endpoint list
    + Getting virtual machines
    data:    Name    Protocol  Public Port  Private Port  Virtual IP      EnableDirectServerReturn  Load Balanced
    data:    ------  --------  -----------  ------------  --------------  ------------------------  -------------
    data:    docker  tcp       2376         2376          138.91.112.194  false                     No
    data:    ssh     tcp       22           22            138.91.112.194  false                     No
    info:    vm endpoint list command OK


Utilizzando **docker** e `-H` opzione in modo che puntino client docker il nodo macchine Virtuali, aggiungere tale nodo sciame si sta creando passando l'id di cluster e la porta docker del nodo (quest'ultimo utilizzando **- addr**):

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 run -d swarm join --addr=138.91.112.194:2376 token://36731c17189fd8f450c395db8437befd
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    bbf88f61300bf876c6202d4cf886874b363cd7e2899345ac34dc8ab10c7ae924

Che è soddisfacente. Per confermare che **Particle swarm** sia in esecuzione su **sciame-nodo-1** che è digitare:

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 ps -a
        CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
        bbf88f61300b        swarm:latest        "/swarm join --addr=   13 seconds ago      Up 12 seconds       2375/tcp            angry_mclean

Ripetere per tutti gli altri nodi del cluster. In questo caso, facciamo per **sciame nodo 2** e **3 di nodo sciame**.

## <a name="begin-managing-the-swarm-cluster"></a>Iniziare a gestire il cluster sciame

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run -d -p 2375:2375 swarm manage token://36731c17189fd8f450c395db8437befd
    d7e87c2c147ade438cb4b663bda0ee20981d4818770958f5d317d6aebdcaedd5

e quindi è possibile elencare fuori dei nodi del cluster:

    ralph@local:~$ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm list token://73f8bc512e94195210fad6e9cd58986f
    54.149.104.203:2375
    54.187.164.89:2375
    92.222.76.190:2375

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi

Passare a eseguire operazioni sullo sciame. Per cercare ispirazione, vedere [https://github.com/docker/swarm/](https://github.com/docker/swarm/)o forse un [video](https://www.youtube.com/watch?v=EC25ARhZ5bI).

<!-- links -->

[docker-machine-azure]: virtual-machines-linux-docker-machine.md
 
