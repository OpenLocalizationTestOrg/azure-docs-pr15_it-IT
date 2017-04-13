<properties 
  pageTitle="La distribuzione del Registro di sistema proprio Docker privato in Azure | Microsoft Azure"
  description="Descrive come utilizzare Docker del Registro di sistema per ospitare immagini contenitore il servizio di archiviazione Blob Azure."
  services="virtual-machines-linux"
  documentationCenter="virtual-machines"
  authors="ahmetalpbalkan"
  editor="squillace"
  manager="timlt"
  tags="azure-service-management,azure-resource-manager" />

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="multiple"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="09/27/2016" 
  ms.author="ahmetb" />

# <a name="deploying-your-own-private-docker-registry-on-azure"></a>La distribuzione del Registro di sistema proprio Docker privato in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



In questo documento viene Docker il Registro di sistema privato che mostra come è possibile distribuire un'immagine di contenitore Docker del Registro di sistema 2.0 in un Docker privato del Registro di sistema in Microsoft Azure usando archiviazione Blob Azure.

Si presuppone che il documento:

1. Si conosce come usare Docker e Docker immagini per l'archiviazione. (Non? [Informazioni sulle Docker](https://www.docker.com))
2. Si dispone di un server che ha installato modulo Docker. (Non? [Eseguire rapidamente in Azure.](https://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/))


## <a name="what-is-a-private-docker-registry"></a>Che cos'è un registro Docker privato?

Per distribuire le applicazioni nei contenitori nel cloud, creare un'immagine di contenitore Docker e archiviarlo in un punto qualsiasi in modo che può essere utilizzato per se stessi e per altri utenti. 

Creazione di un'immagine contenitore e spedizione nel cloud è facile, ma è un'operazione impegnativa per memorizzare l'immagine generato in modo affidabile. Per questo motivo, Docker offre un servizio centralizzato denominato [Hub Docker] [ docker-hub] per memorizzare contenitore immagini nel cloud e consente di creare contenitori in qualsiasi momento tramite le immagini.

Sebbene [Hub Docker] [ docker-hub] è un servizio a pagamento per l'archiviazione del contenitore di applicazione privato immagini, alle esigenze degli sviluppatori di Docker rispetta e fornisce un set di strumenti Apri origine per archiviare le immagini nel proprio privato Docker del Registro di sistema un firewall o locale senza raggiungere Internet pubblica.
Poiché archiviazione Blob Azure è facile sicura, è possibile usarlo rapidamente come creare e utilizzare un privato Docker del Registro di sistema in Azure che è possibile controllare se stessi.

## <a name="why-should-you-host-a-docker-registry-on-azure"></a>Perché si dovrebbe contenere un registro di sistema Docker su Azure?

Hosting l'istanza Docker del Registro di sistema di Microsoft Azure e archiviare le immagini in archiviazione Blob Azure, è possibile creare vari vantaggi:

**Protezione:** Le immagini Docker non lasciano Azure Data Center, in modo che non passano Internet pubblica come accadrebbe se si utilizza Docker Hub.
  
**Prestazioni:** Le immagini Docker vengono memorizzate all'interno della stessa data center o area geografica come le applicazioni. Di conseguenza, che le immagini verranno inserite più veloce e più affidabile rispetto a Docker Hub.

**Affidabilità:** Mediante il archiviazione Blob Microsoft Azure, è possibile apportare utilizzo di molti le proprietà di spazio di archiviazione alla disponibilità, ridondanza, archiviazione premium (SSDs) e così via.

## <a name="configuring-docker-registry-to-use-azure-blob-storage"></a>Configurazione del Registro di sistema Docker usare archiviazione Blob Azure

(È consigliabile per leggere la [documentazione Docker del Registro di sistema 2.0][del Registro di sistema documenti] prima di procedere.)

È possibile [configurare] [ registry-config] il Registro di sistema Docker in due modi diversi.
Si può:

1. Usare un `config.yml` file. In questo caso è necessario creare un'immagine Docker separata all'inizio di `registry` immagine.
2. Ignorare il file di configurazione predefinito tramite le variabili di ambiente: Ottiene varie attività senza creare e gestire un'immagine Docker separata.

Per semplicità, in questo argomento segue opzione 2, utilizzando le variabili di ambiente.

Per eseguire un registro di sistema Docker istanza che:

* utilizza l'Account di archiviazione di Azure per archiviare le immagini
* in attesa sulla porta della macchina virtuale 5000
* configurare l'autenticazione non sono previsti (scelta non consigliata, vedere la nota riportata di seguito)

è necessario eseguire il seguente comando Docker nel terminale bash (sostituire `<storage-account>` e `<storage-key>` con le credenziali):

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

Dopo il comando viene chiuso, è possibile visualizzare il contenitore l'istanza del Registro di sistema Docker privato eseguendo la `docker ps` comando l'host:

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [AZURE.IMPORTANT] Configurazione della protezione per il Registro di sistema Docker non è descritto in questo documento e il Registro di sistema sarà accessibile a qualsiasi utente senza l'autenticazione per impostazione predefinita se si apre la porta alla porta del Registro di sistema per l'endpoint macchina virtuale o bilanciamento del carico se si utilizza il comando distribuzione precedente.
>
> Leggere [La configurazione del Registro di sistema di Docker] [ registry-config] documentazione per informazioni su come proteggere l'istanza del Registro di sistema e le immagini.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere creato la configurazione del Registro di sistema, è possibile passare usarlo ancora. Iniziare con la docker [documenti del Registro di sistema]. 

[docker-hub]: https://hub.docker.com/
[registry]: https://github.com/docker/distribution
[documenti del Registro di sistema]: http://docs.docker.com/registry/
[registry-config]: http://docs.docker.com/registry/configuration/
 
