<properties
   pageTitle="Creare host Docker in Azure con il computer Docker | Microsoft Azure"
   description="Descrive l'utilizzo di un Docker per creare host docker in Azure."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" />

# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Creare host Docker in Azure con il computer Docker

Esecuzione di contenitori [Docker](https://www.docker.com/) richiede un host macchina virtuale in esecuzione daemon docker.
In questo argomento viene descritto come utilizzare il comando [docker computer](https://docs.docker.com/machine/) per creare nuove macchine virtuali Linux, configurato con il daemon Docker in esecuzione in Azure. 

**Nota:** 
- *In questo articolo dipende dalla versione di computer docker 0.7.0 o versione successiva*
- *I contenitori di Windows sarà supportati tramite docker computer nel prossimo futuro*

## <a name="create-vms-with-docker-machine"></a>Creare macchine virtuali con il computer Docker

Creare docker macchine virtuali host in Azure con la `docker-machine create` comando utilizzando il `azure` driver. 

Il driver Azure sarà necessario il proprio ID abbonamento. È possibile utilizzare per recuperare l'abbonamento di Azure [CLI Azure](xplat-cli-install.md) o un [Portale di Azure](https://portal.azure.com) . 

**Tramite il portale di Azure**
- Selezionare sottoscrizioni dalla pagina di spostamento a sinistra e copiare id abbonamento.

**Usa CLI Azure**
- Tipo di ```azure account list``` e copiare l'id di sottoscrizione.

Tipo di `docker-machine create --driver azure` per visualizzare le opzioni e valori predefiniti.
È anche possibile visualizzare la [documentazione del Driver di Azure Docker](https://docs.docker.com/machine/drivers/azure/) per altre informazioni. 

Nell'esempio seguente si basa sui valori predefiniti, ma, se lo si desidera aperto porta 80 in macchine Virtuali per l'accesso a internet. 

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-open-port 80 mydockerhost
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Scegliere un host docker con docker computer
Dopo avere inserito una voce di docker computer per l'host, è possibile impostare l'host predefinito durante l'esecuzione di comandi docker.
##<a name="using-powershell"></a>Utilizzo di PowerShell

```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

##<a name="using-bash"></a>Uso Bash

```bash
eval $(docker-machine env MyDockerHost)
```

È ora possibile eseguire i comandi docker rispetto all'host specificato

```
docker ps
docker info
```

## <a name="run-a-container"></a>Eseguire un contenitore

Con un host configurato, è ora possibile eseguire un semplice server web per verificare se l'host è stata configurata in modo corretto.
Di seguito è utilizzare un'immagine nginx standard, specificare che deve attendere sulla porta 80 e se si riavvia host macchine Virtuali, il contenitore verrà riavviare anche (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

L'output avrà un aspetto simile al seguente:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>Verificare il contenitore

Esaminare i contenitori di esecuzione usando `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

E verificare il contenitore in esecuzione, digitare `docker-machine ip <VM name>` per trovare l'indirizzo IP da immettere nel browser:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Contenitore ngnix in esecuzione](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

##<a name="summary"></a>Riepilogo
Con il computer docker è il provisioning host docker Azure per la convalida di host docker singoli.
Per l'hosting della contenitori di produzione, vedere il [Servizio contenitore Azure](http://aka.ms/AzureContainerService)

Per lo sviluppo di applicazioni .NET con Visual Studio, vedere [Docker Tools per Visual Studio](http://aka.ms/DockerToolsForVS)
