<properties
    pageTitle="Utilizzando l'estensione di macchine Virtuali Docker per Linux su Azure"
    description="Descrizione Docker e le estensioni macchine virtuali di Azure e viene illustrato come creare a livello di programmazione macchine virtuali in Azure host docker dalla riga di comando Usa CLI Azure."
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
    ms.date="08/29/2016"
    ms.author="rasquill"/>

# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>Utilizzando l'estensione di macchine Virtuali Docker comuni della riga di comando interfaccia (CLI Azure)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]



In questo argomento viene illustrato come creare una macchina virtuale con l'estensione di macchine Virtuali Docker dalla modalità di gestione (asm) servizio in Azure CLI su qualsiasi piattaforma. [Docker](https://www.docker.com/) corrisponde a uno dei metodi di virtualizzazione più comuni che utilizza [Linux contenitori](http://en.wikipedia.org/wiki/LXC) piuttosto che macchine virtuali allo scopo di isolamento dei dati e computing sulle risorse condivise. È possibile utilizzare l'estensione macchine Virtuali Docker e [Azure Linux agente](virtual-machines-linux-agent-user-guide.md) per creare una macchina virtuale Docker contenente un numero qualsiasi di contenitori per le applicazioni in Azure. Per visualizzare una descrizione generale della contenitori e i vantaggi associati, vedere [Docker elevato livello Lavagna](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).


##<a name="how-to-use-the-docker-vm-extension-with-azure"></a>Come utilizzare l'estensione di macchine Virtuali Docker con Azure
Per utilizzare l'estensione macchine Virtuali Docker con Azure, è necessario installare una versione dell' [interfaccia della riga di comando Azure](https://github.com/Azure/azure-sdk-tools-xplat) (Azure CLI) superiori a 0.8.6 (come della loro la versione corrente 0.10.0). È possibile installare CLI Azure Mac, Linux e Windows.


La procedura completa di utilizzare Docker su Azure è semplice:

+ Installare nel computer da cui si desidera controllare Azure CLI Azure e le dipendenze (in Windows, sarà in una distribuzione Linux in esecuzione come macchina virtuale)
+ Utilizzare i comandi di Azure CLI Docker per creare un host macchine Virtuali Docker in Azure
+ Utilizzare i comandi Docker locali per gestire i contenitori di Docker in macchine Virtuali il Docker in Azure.


### <a name="install-the-azure-command-line-interface-azure-cli"></a>Installare il Azure interfaccia della riga (comando Azure)

Per installare e configurare CLI Azure, vedere [come installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md). Per confermare l'installazione, digitare `azure` al prompt dei comandi e dopo un breve momento dovrebbero essere visualizzati gli elementi grafici Azure CLI ASCII, che elenca i comandi di base disponibili. Se l'installazione funziona correttamente, sarà possibile digitare `azure help vm` e verificare che uno dei comandi elencati "docker".

> [AZURE.NOTE] Docker include strumenti per Windows, [Docker computer](https://docs.docker.com/installation/windows/), è anche possibile utilizzare per automatizzare la creazione di un client docker che è possibile utilizzare per lavorare con macchine virtuali di Azure come host docker.

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Connessione di Azure CLI al proprio Account Azure
Prima di poter usare CLI Azure è necessario associare le credenziali dell'account Azure CLI Azure sulla piattaforma. La sezione [come connettersi al proprio abbonamento Azure](../xplat-cli-connect.md) viene illustrato come scaricare e importare il file **publishsettings** o associare il CLI Azure con l'id dell'organizzazione.

> [AZURE.NOTE] Esistono alcune differenze nel comportamento quando si utilizza uno o altri metodi di autenticazione, in modo assicurarsi di leggere il documento per comprendere le funzionalità.

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Installare Docker e utilizzare l'estensione di macchine Virtuali Docker per Azure
Seguire le [istruzioni di installazione Docker](https://docs.docker.com/installation/#installation) per installare Docker in locale nel computer in uso.

Per utilizzare Docker con una macchina virtuale Azure, l'immagine Linux utilizzata per la macchina virtuale deve avere installato l' [Agente di macchine Virtuali di Azure Linux](virtual-machines-linux-agent-user-guide.md) . Attualmente sono disponibili solo due tipi di immagini che forniscono seguente:

+ Un'immagine Ubuntu dalla raccolta di immagini Azure o

+ Immagine Linux personalizzata creato con l'agente di macchine Virtuali Linux di Azure installato e configurato. Per ulteriori informazioni su come creare una VM Linux personalizzato con l'agente di macchine Virtuali di Azure, vedere [Agenti macchine Virtuali di Azure Linux](virtual-machines-linux-agent-user-guide.md) .

### <a name="using-the-azure-image-gallery"></a>Usando la raccolta di immagini di Azure

Da una sessione Terminal o Bash, utilizzare il seguente comando CLI Azure per individuare l'immagine Ubuntu più recente nella raccolta macchine Virtuali da utilizzare digitando

`azure vm image list | grep Ubuntu-14_04`

e selezionare uno dei nomi di immagine, ad esempio `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`e usare il comando seguente per creare una nuova macchina virtuale utilizzando l'immagine.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

dove:

+ * &lt;nome macchine virtuali cloudservice&gt; * è il nome della macchina virtuale che diventerà il computer host contenitore Docker in Azure

+  * &lt;nome utente&gt; * è costituito dal nome utente dell'utente radice predefinito della macchina virtuale

+ * &lt;password&gt; * contiene la password dell'account *username* che soddisfa gli standard di complessità per Azure

> [AZURE.NOTE] Attualmente, una password deve essere di almeno 8 caratteri, contengono una lettera minuscola e una lettera maiuscola e un numero e un carattere speciale, ad esempio uno dei caratteri seguenti: `!@#$%^&+=`. No, il periodo alla fine della frase precedente non è un carattere speciale.

Se il comando ha esito positivo, verrà visualizzato il seguente, a seconda della precisi argomenti e opzioni che è stato utilizzato:

![](./media/virtual-machines-linux-classic-cli-use-docker/dockercreateresults.png)

> [AZURE.NOTE] La creazione di una macchina virtuale può richiedere alcuni minuti, ma dopo è stato eseguito il provisioning (il valore di stato è `ReadyRole`) avvia il daemon Docker (il servizio Docker) ed è possibile connettersi all'host contenitore Docker.

Per testare la macchina virtuale Docker è stato creato in Azure, digitare

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

dove * &lt;macchine virtuali-nome--usato&gt; * è il nome del computer virtuale che è stato utilizzato nella chiamata al `azure vm docker create`. Verrà visualizzata sarà simile alla seguente, che indica che la macchina virtuale Host Docker è alto e in esecuzione in Azure e in attesa per i comandi. 

A questo punto è possibile provare a connettersi tramite il client docker per ottenere informazioni (in alcune installazioni client Docker, ad esempio che su Mac, è necessario utilizzare `sudo`):

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

Per essere certi che si tratti di funzionare, è possibile esaminare la macchina virtuale per l'estensione Docker:

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Autenticazione macchine Virtuali Host docker

Oltre a creare macchine Virtuali Docker il `azure vm docker create` comando crea automaticamente anche i certificati necessari per consentire al computer client Docker per connettersi all'host contenitore Azure mediante HTTPS e i certificati sono archiviati nel computer client e host, in base alle esigenze. Successivi tentativi certificati esistenti sono riutilizzati e condivise con il nuovo host.

Per impostazione predefinita, i certificati vengono inseriti nella `~/.docker`, e Docker sarà configurato per l'esecuzione su porta **2376**. Se si preferisce utilizzare una porta diversa o directory, quindi è possibile utilizzare una delle operazioni seguenti `azure vm docker create` le opzioni della riga di comando per configurare il contenitore Docker ospitare macchine Virtuali di usare un'altra porta o certificati diversi per la connessione client:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

Daemon Docker nell'host è configurato per attendere e autenticare connessioni client sulla porta specificata utilizzando i certificati generati dalla `azure vm docker create` comando. Il computer client deve avere i certificati per accedere all'host dei Docker.

> [AZURE.NOTE] Un host in rete in esecuzione senza i certificati sarà esposto a tutti gli utenti che possono essere per connettersi al computer. Prima di modificare la configurazione predefinita, in modo chiaro i rischi per il computer e delle applicazioni.

## <a name="next-steps"></a>Passaggi successivi

* Si è pronti passare al [Manuale dell'utente Docker] e utilizzare le macchine Virtuali Docker. Per creare una macchina virtuale abilitato Docker nel portale di nuovo, vedere [come utilizzare l'estensione di macchine Virtuali Docker con il portale].

* L'estensione macchine Virtuali di Azure Docker supporta anche Docker scrive, che utilizza un file YAML dichiarativo per creare un'applicazione di modellare sviluppo negli ambienti e generare una distribuzione coerenza. Vedere [Guida introduttiva a Docker e scrittura per definire ed eseguire un'applicazione contenitore multiple in un computer virtuale Azure].  

<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

[How to use the Docker VM Extension with Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]: #Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
[Come usare l'estensione di macchine Virtuali Docker con il portale]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Guida dell'utente docker]: https://docs.docker.com/userguide/
 
[Guida introduttiva a Docker e scrittura per definire ed eseguire un'applicazione contenitore multiple in un computer virtuale Azure]:virtual-machines-linux-docker-compose-quickstart.md