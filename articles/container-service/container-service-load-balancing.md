<properties
   pageTitle="Caricare il bilanciamento dei contenitori in un cluster di Azure contenitore servizio | Microsoft Azure"
   description="Il bilanciamento del carico in più contenitori in un cluster di Azure contenitore servizio."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contenitori, servizi di Micro controller di dominio/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="rogardle"/>

# <a name="load-balance-containers-in-an-azure-container-service-cluster"></a>Caricare il bilanciamento dei contenitori in un cluster di servizio contenitore Azure

In questo articolo verrà illustrato come creare un bilanciamento del carico interno in un controller di dominio/OS gestite Azure contenitore servizio Marathon kg. In questo modo sarà possibile riduzione delle applicazioni in senso orizzontale. Consentirà anche in modo da sfruttare l'agente pubblico e privato cluster inserendo i servizi di bilanciamento del carico sul cluster pubblico e i contenitori di applicazione cluster privata.

## <a name="prerequisites"></a>Prerequisiti

[Distribuire un'istanza di Azure contenitore servizio](container-service-deployment.md) con tipo orchestrator cc/OS e [Assicurarsi che il client è possibile connettersi al cluster](container-service-connect.md). 

## <a name="load-balancing"></a>Il bilanciamento del carico

Esistono due livelli di bilanciamento del carico cluster contenitore servizio che verranno create: 

  1. Bilanciamento del carico Azure fornisce punti di ingresso pubblico (quelli che gli utenti finali verrà raggiunto). Questo viene fornito automaticamente dal servizio contenitore Azure ed è, per impostazione predefinita, configurato per esporre porte 80 e 443 8080.
  2. Il servizio di bilanciamento del carico Marathon (marathon kg) indirizza le richieste in ingresso istanze contenitore che tali richieste. Come si scalare contenitori fornire il servizio web, è necessario un adattamento dinamico marathon kg. Questo servizio di bilanciamento del carico non viene fornito per impostazione predefinita del servizio di contenitore, ma è molto semplice per l'installazione.

## <a name="marathon-load-balancer"></a>Servizio di bilanciamento del carico Marathon

Servizio di bilanciamento del carico Marathon in modo dinamico consente di riconfigurare base della contenitori che è stato distribuito. È inoltre flessibile la perdita di un contenitore o un agente - se in tal caso, Apache Mesos riavvio semplicemente il contenitore in un' posizione e verrà adattare marathon kg.

Per installare il bilanciamento del carico Marathon è possibile usare uno dei due controller di dominio/sistema operativo web dell'interfaccia utente o la riga di comando.

### <a name="install-marathon-lb-using-dcos-web-ui"></a>Installare Marathon kg utilizzo dell'interfaccia utente Web cc/OS

  1. Fare clic su 'Universo'
  2. Cercare "Marathon kg"
  3. Fare clic su "Installa"

![L'installazione di marathon kg tramite l'interfaccia Web cc/OS](./media/dcos/marathon-lb-install.png)

### <a name="install-marathon-lb-using-the-dcos-cli"></a>Installare kg Marathon usa CLI cc/OS

Dopo aver installato CLI cc/OS e verifica della compatibilità è possibile connettersi al cluster, eseguire il comando seguente dal computer client:

```bash
dcos package install marathon-lb
```

Questo comando installa automaticamente il servizio di bilanciamento del carico nel cluster di agenti pubblico.

## <a name="deploy-a-load-balanced-web-application"></a>Distribuire un carico bilanciato applicazione Web

Ora che è stata creata pacchetto marathon kg, è possibile distribuire un contenitore di applicazione che si desidera il bilanciamento del carico. In questo esempio verrà installato un server web semplice utilizzando le seguenti operazioni:

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}

```

  * Impostare il valore di `HAProxy_0_VHOST` per il FQDN di bilanciamento del carico per gli agenti. Si tratta del modulo `<acsName>agents.<region>.cloudapp.azure.com`. Ad esempio, se si crea un cluster di servizio contenitore con nome `myacs` nell'area `West US`, il nome di dominio completo sarebbe `myacsagents.westus.cloudapp.azure.com`. È possibile trovare questo riconoscibili bilanciamento del carico con il nome "agente" quando si cercano tra le risorse nel gruppo di risorse creato per il servizio contenitore nel [portale di Azure](https://portal.azure.com).
  * Impostare il servicePort a una porta > = 10.000. Consente di identificare il servizio eseguiti in questo contenitore - marathon kg utilizza per identificare i servizi che è necessario bilanciare.
  * Impostare il `HAPROXY_GROUP` etichetta "esterna".
  * Impostare `hostPort` su 0. Questo errore indica che Marathon arbitrariamente allocazione di una porta disponibile.
  * Impostare `instances` al numero di istanze che si desidera creare. Si può sempre scalare tali alto e verso il basso in un secondo momento.

È importante noing che per impostazione predefinita che Marathon verrà distribuito in cluster private, significa che la distribuzione sopra dovranno essere accessibile solo tramite il servizio di bilanciamento del carico, che è in genere il comportamento che si desiderano.

### <a name="deploy-using-the-dcos-web-ui"></a>Distribuire tramite l'interfaccia utente Web cc/OS

  1. Visitare la pagina Marathon http://localhost/marathon (dopo aver impostato la [tunnel SSH](container-service-connect.md) e fare clic su`Create Appliction`
  2. Nel `New Application` fare clic su finestra di dialogo `JSON Mode` in alto a destra
  3. Incollare JSON precedente nell'editor
  4. Fare clic su`Create Appliction`

### <a name="deploy-using-the-dcos-cli"></a>Distribuire utilizzando CLI cc/OS

Per la distribuzione dell'applicazione con CLI cc/OS è sufficiente copiare JSON precedente in un file denominato `hello-web.json`ed eseguire:

```bash
dcos marathon app add hello-web.json
```

## <a name="azure-load-balancer"></a>Bilanciamento del carico Azure

Per impostazione predefinita, bilanciamento del carico Azure espone porte 80, 8080 e 443. Se si usa uno dei tre porte (come in questo esempio precedente), quindi non è necessario eseguire alcuna operazione. Dovrebbe essere raggiunto nome di dominio completo dell'agente di bilanciamento del carico, ossia e ogni volta che si aggiornano, si verificherà uno dei server tre web in modo circolari. Tuttavia, se si usa una porta diversa, è necessario aggiungere una regola circolari e una ricerca di bilanciamento del carico per la porta che è stato utilizzato. È possibile farlo dal [CLI Azure](../xplat-cli-azure-resource-manager.md), con i comandi `azure network lb rule create` e `azure network lb probe create`. È possibile anche eseguire questa operazione tramite il portale di Azure.


## <a name="additional-scenarios"></a>Altri scenari

È possibile che uno scenario in cui si usa domini diversi per esporre diversi servizi. Per esempio:

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292  
mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321

A tale scopo, estrarre [host virtuali](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/), che consentono di associare domini kg marathon specifici percorsi.

In alternativa, è possibile esporre porte diverse e li riassociare al servizio corretto dietro marathon kg. Per esempio:

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423  
Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432


## <a name="next-steps"></a>Passaggi successivi

Vedere la documentazione cc/OS per attivato ulteriori [marathon kg](https://dcos.io/docs/1.7/usage/service-discovery/marathon-lb/).
