<properties
   pageTitle="Azure contenitore contenitore gestione con Particle Swarm Docker | Microsoft Azure"
   description="Distribuire contenitori Particle Swarm Docker nel servizio contenitore Azure"
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

# <a name="container-management-with-docker-swarm"></a>Gestione dei contenitori con Particle Swarm Docker

Sciame docker offre un ambiente per la distribuzione nei contenitori carichi di lavoro in un insieme di host Docker. Docker sciame utilizza l'API Docker nativo. Il flusso di lavoro per la gestione dei contenitori in un Docker Particle Swarm è quasi identica a informazioni su un host singolo contenitore. Questo documento offre semplici esempi di distribuzione nei contenitori carichi di lavoro in un'istanza di Azure contenitore servizio di Particle Swarm Docker. Per la documentazione più approfondita su Particle Swarm Docker, vedere [Docker Particle Swarm su Docker.com](https://docs.docker.com/swarm/).

Prerequisiti per gli esercizi in questo documento:

[Creare un cluster sciame nel servizio contenitore Azure](container-service-deployment.md)

[Connettersi con il cluster sciame nel servizio contenitore Azure](container-service-connect.md)

## <a name="deploy-a-new-container"></a>Distribuire un nuovo contenitore

Per creare un nuovo contenitore di Docker Particle Swarm, utilizzare la `docker run` comando (assicurandosi che è stato aperto un tunnel SSH agli schemi per i prerequisiti sopra). In questo esempio viene creato un contenitore di `yeasy/simple-web` immagine:


```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

Dopo aver creato il contenitore, utilizzare `docker ps` per restituire informazioni sul contenitore. Notare che sia presente l'agente sciame in cui si trova il contenitore:


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

È possibile accedere a questo punto l'applicazione è in esecuzione in questo contenitore mediante il nome del DNS pubblico di bilanciamento del carico di agente sciame. Nel portale di Azure, è possibile trovare queste informazioni:  


![Visitare reale risultati](media/real-visit.jpg)  

Per impostazione predefinita di bilanciamento ha porte 80, 443 e 8080 Apri. Se si desidera connettersi a un'altra porta sarà necessario aprire tale porta nel servizio di bilanciamento del carico Azure per il Pool di agente.

## <a name="deploy-multiple-containers"></a>Distribuire più contenitori

Come vengono avviati più contenitori, eseguendo 'docker Esegui' più volte, è possibile utilizzare il `docker ps` comando per verificare che ospita i contenitori sono in esecuzione in. Nell'esempio seguente, tre contenitori sono suddivisi equamente tre agenti sciame:  


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>Distribuire contenitori utilizzando Docker comporre

È possibile utilizzare Docker comporre per automatizzare la distribuzione e la configurazione di più contenitori. A tale scopo, assicurarsi che sia stato creato un tunnel Secure Shell (SSH) e che è stata impostata la variabile DOCKER_HOST (vedere prerequisiti sopra).

Creare un file di docker compose.yml nel sistema locale. Per eseguire questa operazione, utilizzare questo [esempio](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml).

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Eseguire `docker-compose up -d` per avviare distribuzioni contenitore:


```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Infine, verrà restituito l'elenco dei contenitori di esecuzione. Questo elenco contiene i contenitori distribuite utilizzando Docker comporre:


```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

Naturalmente, è possibile utilizzare `docker-compose ps` per esaminare solo i contenitori definiti nel `compose.yml` file.

## <a name="next-steps"></a>Passaggi successivi

[Ulteriori informazioni sulle Particle Swarm Docker](https://docs.docker.com/swarm/)
