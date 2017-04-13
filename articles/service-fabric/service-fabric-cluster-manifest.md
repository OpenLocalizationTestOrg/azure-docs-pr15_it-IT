<properties
   pageTitle="Configurare il cluster autonoma | Microsoft Azure"
   description="In questo articolo viene descritto come configurare il prodotto autonomo o cluster servizio tessuti privata."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="dkshir"/>


# <a name="configuration-settings-for-standalone-windows-cluster"></a>Impostazioni di configurazione per cluster di Windows autonomo

In questo articolo viene descritto come configurare un cluster di tessuti servizio autonomo utilizzando il file _**ClusterConfig.JSON**_ . Per specificare le informazioni, ad esempio i nodi di servizio tessuti e loro indirizzi IP, i diversi tipi dei nodi in cluster, le configurazioni di protezione, nonché la topologia della rete in termini di guasto/aggiornamento domini per il cluster autonoma, è possibile utilizzare il file.

Quando il [download del pacchetto di tessuti servizio autonomo](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), alcuni esempi dei file ClusterConfig.JSON vengono scaricati nel computer in uso. I campioni di *DevCluster* nei nomi consente di creare un cluster con tutti e tre i nodi nello stesso computer, ad esempio nodi logici. Disconnettersi da questi, almeno un nodo deve essere contrassegnato come un nodo principale. Questo cluster è utile per un ambiente di sviluppo o test e non è supportato come un cluster di produzione. I campioni di *MultiMachine* nei nomi consente di creare un cluster di qualità di produzione, con ogni nodo su un computer separato. Il numero dei nodi primari per questi cluster verrà impostato a [livello di affidabilità](#reliability).

1. *ClusterConfig.Unsecure.DevCluster.JSON* e *ClusterConfig.Unsecure.MultiMachine.JSON* viene illustrato come creare un cluster di produzione o test non protetto rispettivamente. 
    
2. *ClusterConfig.Windows.DevCluster.JSON* e *ClusterConfig.Windows.MultiMachine.JSON* viene illustrato come creare cluster di produzione o test proteggere la [sicurezza di Windows](service-fabric-windows-cluster-windows-security.md).

3. *ClusterConfig.X509.DevCluster.JSON* e *ClusterConfig.X509.MultiMachine.JSON* viene illustrato come creare cluster di produzione o test protette tramite [X509 sicurezza basata su certificato](service-fabric-windows-cluster-x509-security.md). 


Ora esamineremo le varie sezioni di un file di _**ClusterConfig.JSON**_ come sotto.

## <a name="general-cluster-configurations"></a>Configurazioni cluster generale
Questo è descritta specifiche configurazioni cluster generali, come illustrato nel frammento JSON riportata di seguito.

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2015-01-01-alpha",

È possibile assegnare qualsiasi nome descrittivo per il cluster tessuti servizio mediante l'assegnazione della variabile di **nome** . **ClusterConfigurationVersion** è il numero di versione del cluster; è necessario portarla ogni volta che si aggiorna il cluster servizio tessuti. Tuttavia, è necessario lasciare **apiVersion** il valore predefinito.


<a id="clusternodes"></a>
## <a name="nodes-on-the-cluster"></a>Nodi nel cluster
È possibile configurare i nodi il cluster tessuti servizio utilizzando la sezione **nodi** , come illustrato nel frammento di codice seguente.

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Un cluster di servizio tessuti deve contenere almeno 3 nodi. È possibile aggiungere più nodi di questa sezione per la configurazione. Nella tabella seguente vengono illustrate le impostazioni di configurazione per ogni nodo.

|**Configurazione di un nodo**|**Descrizione**|
|-----------------------|--------------------------|
|nodeName|È possibile assegnare un nome descrittivo per il nodo.|
|indirizzo IP|Trovare l'indirizzo IP del nodo, aprire una finestra di comando e digitare `ipconfig`. Prendere nota dell'indirizzo IPV4 e assegnare alla variabile **indirizzo IP** .|
|nodeTypeRef|Ogni nodo può essere assegnata a un tipo di nodo diverso. I [tipi di nodo](#nodetypes) sono definiti nella sezione seguente.|
|faultDomain|Domini guasto consentono agli amministratori di cluster definire i nodi fisici possono avere esito negativo allo stesso tempo a causa di dipendenze fisiche condivise.|
|upgradeDomain|Aggiornamento domini descrivono set dei nodi arrestare per gli aggiornamenti di servizio tessuti allo stesso momento. È possibile scegliere quali nodi per assegnare al quale eseguire l'aggiornamento domini, mentre non è limitati da qualsiasi requisiti fisici.| 


## <a name="cluster-properties"></a>Proprietà cluster

Sezione **proprietà** del ClusterConfig.JSON viene utilizzata per configurare il cluster come indicato di seguito.

<a id="reliability"></a>
### <a name="reliability"></a>Affidabilità 
La sezione **reliabilityLevel** definisce il numero di copie dei servizi che possono essere eseguiti sui nodi principali del cluster. In questo modo aumenta l'affidabilità di questi servizi e quindi il cluster. È possibile impostare questa variabile su *minimo*, *argento*, *oro* o *Platinum* per 3, 5, 7 o 9 copie di questi servizi rispettivamente. Vedere un esempio riportato di seguito.

    "reliabilityLevel": "Bronze",
    
Si noti che poiché un nodo principale viene eseguita un'unica copia dei servizi, sarà necessario un minimo di 3 nodi principali per *minimo*, 5 per *argento*7 per *oro* e 9 per i livelli di affidabilità *Platinum* .


### <a name="diagnostics"></a>Diagnostica
La sezione **diagnosticsStore** consente di configurare i parametri per consentire di diagnostica e risoluzione degli errori di nodo o cluster, come illustrato nel seguente frammento. 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

I **metadati** sono una descrizione di diagnostica il cluster e possono essere impostato per l'installazione e configurazione. Queste variabili sono utili per raccogliere ETW registri traccia, anomalo, nonché contatori. Per ulteriori informazioni sui registri di traccia ETW, vedere [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) ed [ETW Tracing](https://msdn.microsoft.com/library/ms751538.aspx) . Tutti i log inclusi [arresto anomalo di immagini](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) e [contatori](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) possono essere indirizzati alla cartella **connectionString** nel computer in uso. È anche possibile utilizzare *AzureStorage* per l'archiviazione di diagnostica. Per un frammento di esempio, vedere di seguito.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Sicurezza 
La sezione **sicurezza** è necessaria per un cluster di tessuti servizio autonomo protetto. Il frammento seguente mostra una parte di questa sezione.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

I **metadati** sono una descrizione del cluster sicura e possono essere impostato per l'installazione e configurazione. La **ClusterCredentialType** e **ServerCredentialType** determinare il tipo di sicurezza che consente di implementare cluster e i nodi. Possono essere impostati che uno dei due *X509* per un titolo basata su certificati o *Windows* per un titolo basate su Azure Active Directory. Il resto della sezione **sicurezza** verrà impostato sul tipo di sicurezza. Leggere [basate su certificati di protezione in un cluster di prodotto autonomo](service-fabric-windows-cluster-x509-security.md) o [Windows in un cluster autonoma](service-fabric-windows-cluster-windows-security.md) per informazioni su come compilare il resto della sezione **sicurezza** .


<a id="nodetypes"></a>
### <a name="node-types"></a>Tipi di nodo
La sezione **nodeTypes** descrive il tipo dei nodi che contiene il cluster. Come illustrato nel frammento riportato di seguito, è necessario specificare il tipo di almeno un nodo per un cluster. 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpointPort": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "applicationPorts": {
            "startPort": "20575",
            "endPort": "20605"
        },
        "ephemeralPorts": {
            "startPort": "20606",
            "endPort": "20861"
        },
        "isPrimary": true
    }]

Il **nome** è il nome descrittivo per questo tipo di nodo specifico. Per creare un nodo di questo tipo di nodo, assegnare il nome descrittivo per la variabile **nodeTypeRef** per tale nodo come indicati in [precedenza](#clusternodes). Per ogni tipo di nodo, definire gli endpoint di connessione che verranno utilizzati. È possibile scegliere qualsiasi numero di porta per gli endpoint di connessione, purché non provocano conflitti con endpoint del cluster. In un cluster di più nodi, si verificherà una o più nodi principali (ad esempio **isPrimary** impostato su *true*), a seconda [**reliabilityLevel**](#reliability). Leggere le [Considerazioni sulla pianificazione di servizio tessuti cluster capacità](service-fabric-cluster-capacity.md) per informazioni sui valori **nodeTypes** e **reliabilityLevel** e per sapere quali sono principale e i tipi di nodo principale non. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Endpoint utilizzato per configurare i tipi di nodo

- *clientConnectionEndpointPort* è la porta utilizzata dal client per connettersi al cluster, quando si usa il client API. 
- *clusterConnectionEndpointPort* è la porta in cui i nodi di comunicano con loro.
- *leaseDriverEndpointPort* è la porta utilizzata dal driver leasing cluster per scoprire se i nodi sono ancora attivi. 
- *serviceConnectionEndpointPort* è la porta utilizzata dalle applicazioni e servizi installati in un nodo per comunicare con il client di servizi tessuti in tale nodo.
- *httpGatewayEndpointPort* è la porta utilizzata da Esplora aree tessuti di servizio per connettersi al cluster.
- *ephemeralPorts* sono le [porte dinamiche usate dal sistema operativo](https://support.microsoft.com/kb/929851). Servizio tessuti verrà usata una parte di questi elementi come porte applicazione e il rimanente sarà disponibile per il sistema operativo. L'intervallo verranno inoltre mappati all'intervallo esistente presenta nel sistema operativo, in modo che per tutti gli scopi, è possibile utilizzare gli intervalli dati nei file JSON di esempio. È necessario assicurarsi che la differenza tra l'inizio e le porte fine è almeno 255. 
- *applicationPorts* sono le porte utilizzate dalle applicazioni di servizio tessuti. Deve trattarsi di un sottoinsieme della *ephemeralPorts*, sufficientemente per coprire il requisito di endpoint delle applicazioni. Servizio tessuti utilizzerà questi ogni volta che nuove porte necessari, nonché occuparsi di apertura del firewall per le porte. 
- *reverseProxyEndpointPort* è un endpoint proxy inverso facoltativo. Per ulteriori informazioni, vedere [Servizio tessuti invertire Proxy](service-fabric-reverseproxy.md) . 


### <a name="other-settings"></a>Altre impostazioni
La sezione **fabricSettings** consente di impostare la directory radice per tessuti servizio dati e i registri. È possibile personalizzare questi solo durante la creazione di cluster iniziale. Per un frammento di esempio di questa sezione, vedere di seguito.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

È consigliabile utilizzando un'unità non OS come la FabricDataRoot e FabricLogRoot come offre che maggiore affidabilità contro OS determina l'arresto anomalo. Si noti che se si vuole personalizzare solo la radice di dati, quindi la radice log verrà inserita un livello sotto la radice di dati.


## <a name="next-steps"></a>Passaggi successivi

Dopo avere inserito un intero documento ClusterConfig.JSON configurato per l'installazione e configurazione cluster autonoma, è possibile distribuire il cluster seguendo l'articolo [creare un cluster di Azure servizio tessuti locale o nel cloud](service-fabric-cluster-creation-for-windows-server.md) e passare alla [visualizzazione il cluster con Esplora risorse tessuti servizio](service-fabric-visualizing-your-cluster.md).


