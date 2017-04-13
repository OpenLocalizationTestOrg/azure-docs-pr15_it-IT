<properties
    pageTitle="Con estensione macchine Virtuali Docker per Linux | Microsoft Azure"
    description="Descrive Docker e le estensioni macchine virtuali di Azure e su come creare macchine virtuali di Azure host docker utilizzando CLI Azure nel modello di distribuzione classica."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="05/27/2016"
    ms.author="rasquill"/>


# <a name="using-the-docker-vm-extension-with-the-azure-classic-portal"></a>Utilizzando l'estensione di macchine Virtuali Docker con il portale classico Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


[Docker](https://www.docker.com/) corrisponde a uno dei metodi di virtualizzazione più comuni che utilizza [Linux contenitori](http://en.wikipedia.org/wiki/LXC) invece macchine virtuali allo scopo di isolamento dei dati e computing sulle risorse condivise. È possibile utilizzare l'estensione di macchine Virtuali Docker gestito da [Azure Linux agente] per creare una macchina virtuale Docker contenente un numero qualsiasi di contenitori per le applicazioni in Azure.

> [AZURE.NOTE] In questo argomento viene illustrato come creare una macchina virtuale Docker dal portale di classica Azure. Per informazioni su come creare una macchina virtuale Docker nella riga di comando, vedere [come utilizzare l'estensione di macchine Virtuali Docker dall'interfaccia della riga di comando Azure Azure CLI ()]. Per visualizzare una descrizione generale della contenitori e i vantaggi associati, vedere [Docker elevato livello Lavagna](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

## <a name="create-a-new-vm-from-the-image-gallery"></a>Creare una nuova macchina virtuale dalla raccolta di immagini
Il primo passaggio richiede una macchina virtuale Azure da un'immagine di Linux che supporta l'estensione di macchine Virtuali Docker, utilizzando un'immagine Ubuntu 14.04 risultati dalla raccolta di immagini come un'immagine server di esempio e Ubuntu 14.04 Desktop come client. Nel portale, fare clic su **+ Nuovo** nell'angolo inferiore sinistro per creare una nuova istanza di macchine Virtuali e selezionare un'immagine Ubuntu 14.04 risultati le selezioni disponibili o dalla raccolta di immagini completo, come illustrato di seguito.

> [AZURE.NOTE] Al momento solo immagini Ubuntu 14.04 risultati più recente di luglio 2014 supportano l'estensione di macchine Virtuali Docker.

![Creare una nuova immagine Ubuntu](./media/virtual-machines-linux-classic-portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Creare i certificati Docker

Dopo aver creata la macchina virtuale, assicurarsi che Docker sia installato nel computer client. (Per informazioni dettagliate, vedere [le istruzioni di installazione Docker](https://docs.docker.com/installation/#installation)).

Creare i file di certificato e la chiave per la comunicazione Docker in base alle [Docker in esecuzione con https] e metterle nel **`~/.docker`** directory nel computer client.

> [AZURE.NOTE] L'estensione di macchine Virtuali Docker nel portale di attualmente richiede le credenziali con codificata base 64.

Riga di comando, utilizzare **`base64`** o un altro strumento di codifica preferito per creare gli argomenti codifica base 64. Eseguire questa operazione con un semplice insieme di file di certificato e la chiave può essere simile alla seguente:

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>Aggiungere l'estensione di macchine Virtuali Docker
Per aggiungere l'estensione di macchine Virtuali Docker, individuare l'istanza di macchine Virtuali che è stato creato e scorrere fino a **estensioni** e fare clic su esso per visualizzare le estensioni macchine Virtuali, come illustrato di seguito.
> [AZURE.NOTE] Questa funzionalità è supportata solo del portale di anteprima: https://portal.azure.com/

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickExtensions.png)
### <a name="add-an-extension"></a>Aggiungere un'estensione
Fare clic sul **+ Aggiungi** per visualizzare le possibili estensioni macchine Virtuali è possibile aggiungere a questa macchina virtuale.

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickAdd.png)
### <a name="select-the-docker-vm-extension"></a>Selezionare l'estensione di macchine Virtuali Docker
Scegliere l'estensione di macchine Virtuali Docker, viene visualizzata la descrizione di Docker e collegamenti importanti, e quindi fare clic su **Crea** nella parte inferiore per iniziare la procedura di installazione.

![](./media/virtual-machines-linux-classic-portal-use-docker/ChooseDockerExtension.png)

![](./media/virtual-machines-linux-classic-portal-use-docker/CreateButtonFocus.png)
### <a name="add-your-certificate-and-key-files"></a>Aggiungere il certificato e i file di chiave:

I campi del modulo, immettere le versioni codifica base 64 di certificato CA, il certificato Server e della chiave di Server, come illustrato nella figura seguente.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddExtensionFormFilled.png)

> [AZURE.NOTE] Si noti che (come illustrato nell'immagine precedente) il 2376 vengono compilate per impostazione predefinita. È possibile immettere qui qualsiasi endpoint, ma il passaggio successivo, è necessario aprire il punto finale corrispondente. Se si modifica l'impostazione predefinita, assicurarsi che l'apertura del punto finale corrispondente nel passaggio successivo.

## <a name="add-the-docker-communication-endpoint"></a>Aggiungere l'Endpoint di comunicazione Docker
Quando si visualizza il gruppo di risorse che è stata creata, selezionare il gruppo di sicurezza di rete associati di una macchina virtuale e fare clic su **Regole di protezione in ingresso** per visualizzare le regole, come illustrato di seguito.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddingEndpoint.png)

Fare clic su **+ Aggiungi** per aggiungere un'altra regola e nel caso predefinito, immettere un nome per l'endpoint (in questo esempio, **Docker**) e 2376 "intervallo porta di destinazione". Impostare il valore protocollo con **TCP**e fare clic su **OK** per creare la regola.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddEndpointFormFilledOut.png)


## <a name="test-your-docker-client-and-azure-docker-host"></a>Verifica dei Client Docker e Docker Azure Host
Individuare e copiare il nome del dominio della macchina di virtuale e nella riga di comando del computer client, tipo `docker --tls -H tcp://` *dockerextension* `.cloudapp.net:2376 info` (dove *dockerextension* viene sostituito dal sottodominio per la macchina virtuale).

Il risultato dovrebbe essere simile alla seguente:

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

Dopo aver completato la procedura riportata sopra, è ora disponibile un host Docker pienamente in esecuzione in una macchina virtuale Azure, configurata per essere connesso a in modalità remota da altri client.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi

Si è pronti passare al [Manuale dell'utente Docker] e utilizzare le macchine Virtuali Docker. Se si vuole automatizzare la creazione di host Docker in macchine virtuali di Azure tramite interfaccia della riga di comando, vedere [come utilizzare l'estensione di macchine Virtuali Docker dall'interfaccia della riga di comando Azure (Azure CLI)]

<!--Anchors-->
[Create a new VM from the Image Gallery]: #createvm
[Create Docker Certificates]: #dockercerts
[Add the Docker VM Extension]: #adddockerextension
[Test Docker Client and Azure Docker Host]: #testclientandserver
[Next steps]: #next-steps

<!--Image references-->
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Come usare l'estensione di macchine Virtuali Docker dall'interfaccia della riga di comando Azure (Azure CLI)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Agente Linux Azure]: virtual-machines-linux-agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

[Esecuzione Docker con https]: http://docs.docker.com/articles/https/
[Guida dell'utente docker]: https://docs.docker.com/userguide/
