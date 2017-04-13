<properties
   pageTitle="Azure contenitore contenitore gestione tramite l'API REST | Microsoft Azure"
   description="Distribuire contenitori a un cluster di Azure contenitore servizio Mesos tramite l'API REST Marathon."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenitori, Micro-servizi, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="timlt"/>

# <a name="container-management-through-the-rest-api"></a>Gestione dei contenitori tramite l'API REST

CC/OS offre un ambiente per la distribuzione e proporzioni dei caratteri raggruppate carichi di lavoro, mentre si astraendo hardware sottostante. Nella parte superiore di controller di dominio/OS, non esiste un quadro che gestisce la pianificazione e l'esecuzione di carichi di lavoro di calcolo.

Sebbene Framework siano disponibili per molti carichi di lavoro comuni, questo documento descrive come è possibile creare e modificare la scala distribuzioni contenitore utilizzando Marathon. Prima di eseguire questi esempi, è necessario un cluster di controller di dominio/OS configurato nel servizio di Azure contenitore. È necessario disporre di connettività remota a questo cluster. Per ulteriori informazioni su questi elementi, vedere gli articoli seguenti:

- [Distribuire un cluster di servizio contenitore Azure](container-service-deployment.md)
- [Connessione a un cluster di servizio contenitore Azure](container-service-connect.md)

Dopo essersi connessi al cluster servizio contenitore Azure, è possibile accedere tramite http://localhost:local porta controller di dominio/sistema operativo e le API REST correlate. Gli esempi di questo documento presuppongono che siano tunneling sulla porta 80. Ad esempio, è possibile contattarlo endpoint Marathon `http://localhost/marathon/v2/`. Per ulteriori informazioni sulle varie API, vedere la documentazione Mesosphere per l' [API Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) e l' [API Chronos](https://mesos.github.io/chronos/docs/api.html)e la documentazione Apache per l' [API utilità di pianificazione Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Raccogliere informazioni da controller di dominio/sistema operativo e Marathon

Prima di distribuire i contenitori al cluster controller di dominio/OS, raccogliere alcune informazioni relative al cluster controller di dominio/OS, ad esempio i nomi e lo stato corrente di agenti cc/OS. A tale scopo, eseguire una query di `master/slaves` endpoint dell'API REST cc/OS. Se tutto va bene, verrà visualizzato un elenco di agenti cc/OS e diverse proprietà per ogni.

```bash
curl http://localhost/mesos/master/slaves
```

A questo punto, utilizzare la Marathon `/apps` endpoint per cercare le attuali distribuzioni applicazione al cluster controller di dominio/OS. Se si tratta di un nuovo cluster, verrà visualizzata una stringa vuota per le applicazioni.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Distribuire un contenitore Docker formattato

Distribuire formattato Docker contenitori tramite Marathon tramite un file JSON che descrive la distribuzione desiderata. Nell'esempio seguente viene verrà distribuito il contenitore Nginx, porta associazione 80 dell'agente di cc/OS alla porta 80 del contenitore. Si noti inoltre che la proprietà 'acceptedResourceRoles' è impostata su 'slave_public'. Il contenitore verrà distribuito a un agente del set di scala agente pubblico.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Per distribuire un contenitore formattato Docker, creare file JSON o usare l'esempio fornito al [demo Azure contenitore servizio](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Archiviarlo in un percorso accessibile. Successivamente, per distribuire il contenitore, eseguire il comando seguente. Specificare il nome del file JSON.

```
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

L'output sarà simile alla seguente:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

A questo punto, se si esegue una query Marathon, Applications Edition, questa nuova richiesta verrà visualizzato nell'output.

```
curl localhost/marathon/v2/apps
```

## <a name="scale-your-containers"></a>Ridimensionare i contenitori

Utilizzare l'API Marathon per scalare o modificare la scala nelle distribuzioni dell'applicazione. Nell'esempio precedente è stato distribuito una sola istanza di un'applicazione. Di seguito scalare questo esterna a tre istanze di un'applicazione. A tale scopo, creare un file JSON utilizzando il testo seguente JSON e archiviarlo in un percorso accessibile.

```json
{ "instances": 3 }
```

Eseguire il seguente comando per scalabilità dell'applicazione.

>[AZURE.NOTE] URI saranno http://localhost/marathon/v2/apps/ e quindi l'ID dell'applicazione. Se si sta utilizzando l'esempio Nginx fornito in questo caso, che l'URI sarebbe http://localhost/marathon/v2/apps/nginx.

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Infine, eseguire una query endpoint Marathon per le applicazioni. Vedrete che ora sono presenti tre contenitori Nginx.

```
curl localhost/marathon/v2/apps
```

## <a name="use-powershell-for-this-exercise-marathon-rest-api-interaction-with-powershell"></a>Usare PowerShell per questa esercitazione: interazione API REST Marathon con PowerShell

È possibile eseguire queste azioni stesso utilizzando i comandi di PowerShell in un sistema di Windows.

Per raccogliere le informazioni relative al cluster controller di dominio/OS, ad esempio nomi agente e lo stato dell'agente, eseguire il comando seguente.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Distribuire formattato Docker contenitori tramite Marathon tramite un file JSON che descrive la distribuzione desiderata. Nell'esempio seguente viene verrà distribuito il contenitore Nginx, porta associazione 80 dell'agente di cc/OS alla porta 80 del contenitore.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Creare un file JSON oppure utilizzare l'esempio fornito al [demo Azure contenitore servizio](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Archiviarlo in un percorso accessibile. Successivamente, per distribuire il contenitore, eseguire il comando seguente. Specificare il nome del file JSON.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Utilizzare l'API Marathon per scalare o modificare la scala nelle distribuzioni dell'applicazione. Nell'esempio precedente è stato distribuito una sola istanza di un'applicazione. Di seguito scalare questo esterna a tre istanze di un'applicazione. A tale scopo, creare un file JSON utilizzando il testo seguente JSON e archiviarlo in un percorso accessibile.

```json
{ "instances": 3 }
```

Eseguire il seguente comando per scalabilità dell'applicazione.

> [AZURE.NOTE] URI saranno http://localhost/marathon/v2/apps/ e quindi l'ID dell'applicazione. Se si sta utilizzando l'esempio Nginx fornita di seguito, l'URI sarà http://localhost/marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Passaggi successivi

- [Ulteriori informazioni sull'endpoint Mesos HTTP]( http://mesos.apache.org/documentation/latest/endpoints/).
- [Ulteriori informazioni sulla API REST Marathon]( https://mesosphere.github.io/marathon/docs/rest-api.html).
