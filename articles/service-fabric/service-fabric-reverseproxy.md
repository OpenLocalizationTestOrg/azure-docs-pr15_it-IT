<properties
   pageTitle="Servizio Proxy inverso tessuti | Microsoft Azure"
   description="Utilizzare proxy inversi dell'infrastruttura di servizio per la comunicazione con microservices all'interno e all'esterno del cluster"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/04/2016"
   ms.author="vturecek"/>

# <a name="service-fabric-reverse-proxy"></a>Proxy inverso tessuti servizio

Il proxy del servizio tessuti inverso è un'infrastruttura integrata nelle servizio proxy inverso che consente l'indirizzamento microservices cluster tessuti servizio che espongono i punti finali HTTP.

## <a name="microservices-communication-model"></a>Modello di comunicazione Microservices

Microservices nella struttura di servizio in genere eseguire su un sottoinsieme di Virtual Machine del cluster e spostare da macchine uno Virtuali a altro per diversi motivi. In modo che i punti finali per microservices è possono modificare in modo dinamico. Il modello tipico per comunicare con il microservice è il ciclo di risoluzione riportata di seguito,

1. Consente di risolvere il percorso del servizio inizialmente tramite il servizio per la denominazione.
2. Connessione al servizio.
3. Individuare la causa errori delle connessioni e risolvere il percorso del servizio quando necessario.

Questo processo prevede in genere disposizione raccolte comunicazioni lato client in un ciclo di tentativi che implementa criteri del servizio di risoluzione e riprovare.
Per ulteriori informazioni su questo argomento, vedere [la comunicazione con i servizi](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-via-sf-reverse-proxy"></a>Comunicare tramite proxy inverso if
Infrastruttura di servizio proxy inverso viene eseguito su tutti i nodi del cluster. Esegue il processo di risoluzione intero servizio per conto del cliente e quindi inoltra la richiesta del client. In modo client in esecuzione su cluster possono usare le raccolte di comunicazione HTTP sul lato client per comunicare con il servizio di destinazione mediante il proxy inverso if eseguito localmente sullo stesso nodo.

![Comunicazione interna][1]

## <a name="reaching-microservices-from-outside-the-cluster"></a>Raggiungere Microservices all'esterno del cluster
Il modello di comunicazione esterna predefinito per microservices è **consenso esplicito** in ogni servizio per impostazione predefinita non è possibile accedervi direttamente dal clienti esterni. [Servizio di bilanciamento del carico Azure](../load-balancer/load-balancer-overview.md) è un limite di rete tra microservices e clienti esterni, che esegue NAT e inoltra richieste esterne all' interno **creato** i punti finali. Per rendere endpoint del microservice direttamente accessibile ai client esterni, il servizio di bilanciamento del carico di Azure deve essere stato configurato per inoltrare il traffico a ogni porta utilizzato dal servizio del cluster. Inoltre, la maggior parte dei microservices (esp. informazioni sullo stato microservices) non si trovano in tutti i nodi del cluster e possono spostarsi tra i nodi di failover, in modo che in tal caso, il servizio di bilanciamento del carico di Azure non è possibile determinare in modo efficiente il nodo di destinazione replica si trovano per inviare il traffico al.

### <a name="reaching-microservices-via-the-sf-reverse-proxy-from-outside-the-cluster"></a>Raggiungere Microservices tramite proxy inverso IF all'esterno del cluster

Invece di configurazione delle porte singoli del servizio di bilanciamento azure, solo la porta proxy inverso if può essere configurata nel bilanciamento del carico di Azure. In questo modo i clienti all'esterno del cluster raggiungere i servizi all'interno del cluster tramite il proxy inverso senza un configurazioni aggiuntive.

![Comunicazioni esterne][0]

>[AZURE.WARNING] Configurazione di porta del proxy inverso nel sistema di bilanciamento del carico, consente di tutti i servizi micro cluster che espongono un endpoint http, per essere addressible all'esterno del cluster.


## <a name="uri-format-for-addressing-services-via-the-reverse-proxy"></a>Formato URI per indirizzare servizi tramite il proxy inverso

Il proxy inverso utilizza un formato URI specifico per identificare quali partizione di servizio deve inoltrare la richiesta di posta in arrivo per:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&Timeout=<timeout_in_seconds>
```

 - **http:** Proxy inverso può essere configurato per il traffico HTTP o HTTPS. In caso di traffico HTTPS, terminazione SSL si verifica il proxy inverso. Le richieste di inoltro delle chiamate, il proxy inverso ai servizi del cluster sono su http.
 - **Cluster FQDN | IP interno:** Per i client esterni, è possibile configurare il proxy inverso in modo che sia raggiungibile mediante il dominio cluster (ad esempio, mycluster.eastus.cloudapp.azure.com). Per impostazione predefinita che proxy inverso viene eseguito su tutti i nodi, pertanto per il traffico interno può essere raggiunta host locale o su qualsiasi indirizzo IP nodi interno (ad esempio 10.0.0.1).
 - **Porta:** La porta specificata per il proxy inverso. Ad esempio: 19008.
 - **ServiceInstanceName:** Questo è il nome dell'istanza servizio distribuito completo del servizio di cui si sta tentando di raggiungere il "tessuti: /" combinazione. Ad esempio, per raggiungere servizio *tessuti: / applicazione servizio/*, si utilizzerebbero *applicazione/servizio*.
 - **Percorso suffisso:** Si tratta del percorso URL effettivo per il servizio che si desidera connettersi. Ad esempio *myapi/valori/aggiungere/3*
 - **PartitionKey:** Per un servizio partizionato, questa è la chiave calcolata partizione della partizione che si desidera raggiungere. Si noti che questo *non* partizione ID GUID. Questo parametro non è necessario per i servizi con lo schema di partizione singleton.
 - **PartitionKind:** Lo schema di partizione di servizio. Può trattarsi di 'Int64Range' o 'Denominata'. Questo parametro non è necessario per i servizi con lo schema di partizione singleton.
 - **Timeout:**  Specifica il timeout per la richiesta http creato da proxy inverso al servizio per conto di richiesta del client. Il valore predefinito è 60 secondi. Si tratta di un parametro facoltativo.

### <a name="example-usage"></a>Esempio di utilizzo

Ad esempio, di seguito è illustrato servizio **tessuti: applicazione/servizio** che si apre un comunicare ascoltatore HTTP sull'URL seguente:

```
http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Con le risorse seguenti:

 - `/index.html`
 - `/api/users/<userId>`

Se il servizio utilizza la combinazione di partizioni singleton, i parametri di stringa di query *PartitionKey* e *PartitionKind* non sono obbligatori e il servizio raggiungibili tramite il gateway come:

 - Esternamente:`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService`
 - Internamente:`http://localhost:19008/MyApp/MyService`

Se il servizio utilizza la combinazione di partizioni Int64 uniforme, i parametri di stringa di query *PartitionKey* e *PartitionKind* da utilizzare per raggiungere una partizione del servizio:

 - Esternamente:`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
 - Internamente:`http://localhost:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Per raggiungere le risorse esposte dal servizio, è sufficiente posizionare il percorso della risorsa dopo il nome del servizio nell'URL:

 - Esternamente:`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
 - Internamente:`http://localhost:19008/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Il gateway quindi inoltra tali richieste all'URL del servizio:

 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Gestione speciale per la condivisione porta servizi

Il Gateway di applicazione tenta di risolvere un indirizzo del servizio e ripetere la richiesta quando un servizio non è raggiungibile. Questo è uno dei vantaggi principali di gateway, come codice client non è necessario implementare risoluzione servizio e risolvere Esegui ciclo continuo.

Quando un servizio non può essere raggiunto significa in genere che l'istanza del servizio o replica è spostato in un altro nodo come parte del ciclo di vita normale. In questo caso, il gateway che venga visualizzato un errore di connessione di rete indica la disponibilità di un estremo non è più aperto nell'indirizzo originariamente risolto.

Tuttavia, repliche o istanze del servizio possono condividere un processo host e possono inoltre condividere una porta quando ospitato da un server web basate su sys, tra cui:

 - [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
 - [WebListener ASP.NET di base](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
 - [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

In questo caso è probabile che il server web è disponibile nel processo di host e rispondere alle richieste ma l'istanza di servizio risolto o replica non è più disponibile nell'host. In questo caso, il gateway riceveranno una risposta HTTP 404 dal server web. Di conseguenza, un HTTP 404 sono due significato distinto:

 1. L'indirizzo del servizio è corretto, ma la risorsa richiesta dall'utente non esiste.
 2. L'indirizzo del servizio non è corretta e la risorsa richiesta dall'utente può esistere su un nodo diverso.

Nel primo caso si tratta di un normale HTTP 404, che viene considerato un errore di utente. Nel secondo caso, l'utente ha richiesto una risorsa che esiste, tuttavia, il gateway non è riuscito a individuarlo perché è stato spostato il servizio, nel qual caso il gateway deve risolvere l'indirizzo e riprovare.

Il gateway deve un modo per distinguere tra questi due casi. Per effettuare questa distinzione, è necessario un suggerimento dal server.

 - Per impostazione predefinita, il Gateway di applicazione prende in considerazione maiuscole/minuscole #2 e tenta di risolvere ed eseguire nuovamente la richiesta.
 - Per indicare maiuscole/minuscole #1 per il Gateway di applicazione, il servizio deve restituire l'intestazione HTTP seguente:

`X-ServiceFabric : ResourceNotFound`

Questa intestazione di risposta HTTP indica una situazione HTTP 404 normale in cui la risorsa richiesta non esiste e il gateway non tenterà di risolvere l'indirizzo del servizio.

## <a name="setup-and-configuration"></a>Installazione e configurazione
Per attivare il proxy inverso tessuti del servizio per il cluster tramite il [modello di gestione risorse di Azure](./service-fabric-cluster-creation-via-arm.md).

Dopo avere inserito il modello per il cluster che si desidera distribuire (dai modelli di esempio o creando un modello di gestione risorse personalizzati) proxy inverso può essere attivato nel modello dalla procedura seguente.

1. Definire una porta per il proxy inverso nella [sezione parametri](../resource-group-authoring-templates.md) del modello.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19008,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Specificare la porta per tutti gli oggetti di nodo **Cluster** [sezione tipo di risorsa](../resource-group-authoring-templates.md)

    Per apiVersion prima ' 2016-09-01' la porta è identificata dal parametro nome ***httpApplicationGatewayEndpointPort***

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "httpApplicationGatewayEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```

    Per apiVersion nella o dopo ' 2016-09-01' la porta è identificata dal parametro nome ***reverseProxyEndpointPort***

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```

3. Per risolvere il proxy inverso all'esterno del cluster azure, impostare le **regole di bilanciamento carico azure** per la porta specificata nel passaggio 1.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. Per configurare i certificati SSL sulla porta per il proxy inverso, aggiungere il certificato per la proprietà httpApplicationGatewayCertificate **Cluster** [sezione tipo di risorsa](../resource-group-authoring-templates.md)

    Per apiVersion prima ' 2016-09-01' il certificato è identificato dal parametro nome ***httpApplicationGatewayCertificate***

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "httpApplicationGatewayCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```
    Per apiVersion nella o dopo ' 2016-09-01' il certificato è identificato dal parametro nome ***reverseProxyCertificate***
    
    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

## <a name="next-steps"></a>Passaggi successivi
 - Visualizzare un esempio di comunicazione HTTP tra i servizi di un [progetto di esempio in GitHUb](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Chiamate di procedura remota con i servizi remoti di servizi affidabili](service-fabric-reliable-services-communication-remoting.md)

 - [Web API che utilizza OWIN in servizi affidabili](service-fabric-reliable-services-communication-webapi.md)

 - [Comunicazione WCF tramite servizi affidabili](service-fabric-reliable-services-communication-wcf.md)


[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
