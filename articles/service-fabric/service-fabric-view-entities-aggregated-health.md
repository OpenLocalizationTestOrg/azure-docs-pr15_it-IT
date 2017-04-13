<properties
   pageTitle="Come visualizzare le entità Azure servizio tessuti aggregato integrità | Microsoft Azure"
   description="In questo articolo viene descritto come eseguire una query, visualizzare e valutare integrità aggregato le entità Azure servizio tessuti, tramite query generali e di integrità."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="oanapl"/>

# <a name="view-service-fabric-health-reports"></a>Visualizza report integrità servizio tessuti
Azure servizio tessuti introduce un [modello di integrità](service-fabric-health-introduction.md) che comprende entità integrità sistema componenti e watchdog può inviare una segnalazione condizioni locali che si siano controllando. [Integrità archiviare](service-fabric-health-introduction.md#health-store) aggrega tutti i dati di stato per determinare se entità sono corretti.

All'esterno della casella cluster verrà inserito i report di stato provenienti dai componenti del sistema. Altre informazioni su [report di stato di sistema di usare per la risoluzione](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Servizio tessuti sono disponibili diversi metodi per ottenere lo stato delle entità aggregato:

- [Servizio tessuti Explorer](service-fabric-visualizing-your-cluster.md) o altri strumenti di visualizzazione

- Query di integrità (tramite PowerShell, API o resto)

- Generale una query che restituiscono un elenco di entità che dispongono dell'integrità come una proprietà (tramite PowerShell, API o resto)

Per dimostrare queste opzioni, utilizzare un cluster locale con cinque nodi. Accanto al **tessuti: / sistema** applicazione (che è presente all'esterno della casella), sono distribuiti in altre applicazioni. Una di queste applicazioni è **tessuti: / WordCount**. Questa applicazione contiene un servizio informazioni sullo stato configurato con sette. Perché sono presenti solo cinque nodi, i componenti del sistema visualizzato un avviso che si trova la partizione sotto il conteggio di destinazione.

```xml
<Service Name="WordCountService">
    <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
      <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
    </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Integrità servizio tessuti Explorer
Servizio tessuti Explorer offre una rappresentazione visiva del cluster. Nell'immagine seguente è possibile vedere che:

- L'applicazione **tessuti: / WordCount** è rossa (in errore) perché contiene un evento di errore riportato dal **MyWatchdog** per la **disponibilità**di proprietà.

- Uno dei relativi servizi **tessuti: / WordCount/WordCountService** giallo (in avviso). Poiché il servizio è configurato con sette, essi non tutti inseriti, sono presenti solo cinque nodi. Anche se non è possibile vederlo, partizione di servizio è gialla a causa il rapporto di sistema. Partizione gialla attiva il servizio giallo.

- Il cluster è rosso a causa dell'applicazione rosso.

La valutazione utilizza criteri predefiniti da manifesti applicazione e il raggruppamento. Sono criteri rigidi e non tollerano qualsiasi errore.

Visualizzazione del cluster con Esplora risorse tessuti servizio:

![Visualizzazione del cluster con Esplora risorse tessuti servizio.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE] Per ulteriori informazioni su [Esplora tessuti servizio](service-fabric-visualizing-your-cluster.md).

## <a name="health-queries"></a>Query di integrità
Servizio tessuti espone query integrità per tutti i [tipi di entità](service-fabric-health-introduction.md#health-entities-and-hierarchy)supportati. È possibile accedervi tramite l'API (i metodi disponibili in **FabricClient.HealthManager**), i cmdlet di PowerShell e resto. Queste query restituiscono le informazioni dello stato di completamento dell'entità: integrità aggregato, eventi relativi allo stato di entità, stati di integrità figlio (se applicabile) e valutazioni non corretti quando l'entità non è integro.

> [AZURE.NOTE] Un'entità integrità viene restituita quando è completamente popolato nell'archivio di integrità. L'entità deve essere attivo (non eliminati) e dispone di un report di sistema. L'entità padre nella catena della gerarchia devono inoltre includere i report di sistema. Se nessuna di queste condizioni non è soddisfatta, le query di integrità restituiranno un'eccezione che illustra il motivo per cui non viene restituito l'entità.

Le query di integrità necessario passare l'identificatore di entità, che dipende dal tipo di entità. Le query accettano parametri dei criteri di integrità facoltativo. Se non viene specificato alcun criterio di integrità, vengono utilizzati i [criteri di integrità](service-fabric-health-introduction.md#health-policies) dal manifesto cluster o applicazione per la valutazione. Le query accettano anche i filtri per la restituzione solo parziale figli o eventi, ossia quelli che rispettano ai filtri specificati.

> [AZURE.NOTE] Sul lato server vengono applicati i filtri di output in modo che le dimensioni di risposta messaggio vengono ridotte. È consigliabile che è possibile utilizzare i filtri di output per limitare i dati restituiti anziché applicare filtri sul lato client.

Integrità un'entità contiene:

- Lo stato di integrità aggregato dell'entità. Calcolato dall'archivio di stato in base a report di stato delle entità, stati di integrità figlio (se applicabile) e criteri di integrità. Per ulteriori informazioni sulla [valutazione dell'integrità di entità](service-fabric-health-introduction.md#entity-health-evaluation).  

- Eventi di stato sull'entità.

- Insieme di stati di integrità di tutti gli elementi figlio per l'entità che possono avere figli. Gli stati di integrità includono gli identificatori di entità e lo stato di integrità aggregato. Per ottenere integrità completo per un elemento figlio, chiamare lo stato di query per il tipo di entità figlio e passare l'identificatore figlio.

- Valutazioni non corretti che puntano al report che lo ha attivato lo stato dell'entità, se l'entità non è integro.

## <a name="get-cluster-health"></a>Ottenere integrità cluster
Restituisce lo stato dell'entità cluster e contiene gli stati di integrità dei nodi (elementi figlio del cluster) e le applicazioni. Input:

- [Facoltativo] Criterio di integrità cluster utilizzato per valutare i nodi e gli eventi cluster.

- [Facoltativo] Integrità dei criteri mapping delle applicazioni, con i criteri di integrità utilizzati per ignorare i criteri di manifesto applicazione.

- [Facoltativo] Filtri per le applicazioni di specificare le voci, nodi ed eventi di interesse e devono essere restituiti nel risultato (ad esempio solo degli errori o avvisi e gli errori). Tutti gli eventi, i nodi e le applicazioni vengono utilizzate per la valutazione dell'integrità di entità aggregato, indipendentemente dal filtro.

### <a name="api"></a>API
Per ottenere integrità cluster, creare un `FabricClient` e chiamare il metodo [GetClusterHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) nella relativa **HealthManager**.

La seguente chiamata riceve lo stato di cluster:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Il codice seguente ottiene lo stato di cluster utilizzando un criterio di integrità cluster personalizzati e i filtri per i nodi e le applicazioni. Crea [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx), che contiene le informazioni di input.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Cmdlet per ottenere lo stato di cluster è [Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx). Prima di tutto, connettersi al cluster utilizzando il cmdlet [ServiceFabricCluster Connetti](https://msdn.microsoft.com/library/mt125938.aspx) .

Lo stato del cluster è cinque nodi, l'applicazione di sistema e tessuti: / WordCount configurato come descritto.

Il seguente cmdlet Ottiene integrità cluster tramite criteri di integrità predefinito. Lo stato di integrità aggregato è avviso, in quanto la struttura: / WordCount applicazione è in avviso. Si noti come le valutazioni integre forniscono informazioni dettagliate sulle condizioni che lo ha attivato lo stato aggregato.

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=false.


NodeHealthStates        :
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

Il seguente cmdlet di PowerShell Ottiene lo stato del cluster utilizzando un criterio di applicazione personalizzata. Vengono filtrati i risultati per ottenere solo errore o applicazioni di avviso e nodi. Di conseguenza, non nodi vengono restituiti, così come sono tutti integri. Solo la struttura: / applicazione WordCount rispetta il filtro di applicazioni. Poiché il criterio personalizzato specifica da tenere in considerazione gli avvisi come errori per la struttura: / applicazione WordCount, l'applicazione viene valutata come errore e pertanto è il cluster.

```powershell
PS c:\> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error"


AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

### <a name="rest"></a>RESTO
È possibile ottenere integrità cluster con una [richiesta GET](https://msdn.microsoft.com/library/azure/dn707669.aspx) o una [richiesta POST](https://msdn.microsoft.com/library/azure/dn707696.aspx) che include i criteri di integrità descritti nel corpo.

## <a name="get-node-health"></a>Ottenere integrità nodo
Restituisce lo stato di un'entità nodo e sono presenti gli eventi di integrità segnalati per il nodo. Input:

- [Required] Il nome di nodo che identifica il nodo.

- [Facoltativo] Impostazioni di criteri integrità cluster utilizzate per la valutazione dell'integrità.

- [Facoltativo] Filtri per gli eventi che specificano le voci di interesse e devono essere restituiti nel risultato (ad esempio solo degli errori o avvisi e gli errori). Tutti gli eventi vengono utilizzati per la valutazione dell'integrità di entità aggregato, indipendentemente dal filtro.

### <a name="api"></a>API
Per ottenere integrità nodo tramite l'API, creare un `FabricClient` e chiamare il metodo [GetNodeHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getnodehealthasync.aspx) nella relativa HealthManager.

Il codice seguente ottiene lo stato di nodo per il nome del nodo specificato:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Il codice seguente ottiene lo stato di nodo per il nome del nodo specificato e nel filtro di eventi e criteri personalizzati che transita [NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Cmdlet per ottenere lo stato di nodo è [Get-ServiceFabricNodeHealth](https://msdn.microsoft.com/library/mt125937.aspx). Prima di tutto, connettersi al cluster utilizzando il cmdlet [ServiceFabricCluster Connetti](https://msdn.microsoft.com/library/mt125938.aspx) .
Il seguente cmdlet Ottiene lo stato di nodo tramite criteri di integrità predefinito:

```powershell
PS C:\> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 6
                        SentAt                : 3/22/2016 7:47:56 PM
                        ReceivedAt            : 3/22/2016 7:48:19 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:48:19 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Il seguente cmdlet Ottiene lo stato di tutti i nodi del cluster:

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_2                     Ok
_Node_0                     Ok
_Node_1                     Ok
_Node_3                     Ok
_Node_4                     Ok
```

### <a name="rest"></a>RESTO
È possibile ottenere integrità nodo con una [richiesta GET](https://msdn.microsoft.com/library/azure/dn707650.aspx) o una [richiesta POST](https://msdn.microsoft.com/library/azure/dn707665.aspx) che include i criteri di integrità descritti nel corpo.

## <a name="get-application-health"></a>Guida dell'integrità
Restituisce lo stato di un'entità dell'applicazione. Contiene gli stati di integrità dell'applicazione distribuita e gli elementi figlio del servizio. Input:

- [Required] Il nome dell'applicazione (URI) che identifica l'applicazione.

- [Facoltativo] Il criterio di integrità applicazione utilizzato per ignorare i criteri di manifesto applicazione.

- [Facoltativo] Filtri per gli eventi, servizi e applicazioni per indicare le voci di interesse e devono essere restituiti nel risultato (ad esempio solo degli errori o avvisi e gli errori). Tutti gli eventi, i servizi e applicazioni vengono utilizzate per la valutazione dell'integrità di entità aggregato, indipendentemente dal filtro.

### <a name="api"></a>API
Per ottenere dell'integrità, creare un `FabricClient` e chiamare il metodo [GetApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getapplicationhealthasync.aspx) nella relativa HealthManager.

Il codice seguente ottiene lo stato di applicazione corrispondente al nome dell'applicazione specificata (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Il codice seguente ottiene lo stato di applicazione per il nome dell'applicazione specificato (URI), con i filtri e criteri personalizzati specificati tramite [ApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.applicationhealthquerydescription.aspx).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Cmdlet per ottenere lo stato dell'applicazione è [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx). Prima di tutto, connettersi al cluster utilizzando il cmdlet [ServiceFabricCluster Connetti](https://msdn.microsoft.com/library/mt125938.aspx) .

Il cmdlet seguente restituisce lo stato della **tessuti: / WordCount** applicazione:

```powershell
PS c:\>
PS C:\WINDOWS\system32>  Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Ok
                                  SequenceNumber        : 131031545225930951
                                  SentAt                : 3/22/2016 9:08:42 PM
                                  ReceivedAt            : 3/22/2016 9:08:42 PM
                                  TTL                   : Infinite
                                  Description           : Availability checked successfully, latency ok
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 8:55:39 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Il seguente cmdlet di PowerShell passa criteri personalizzati. Filtra anche figli ed eventi.

```powershell
PS C:\> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>RESTO
È possibile ottenere dell'integrità con una [richiesta GET](https://msdn.microsoft.com/library/azure/dn707681.aspx) o una [richiesta POST](https://msdn.microsoft.com/library/azure/dn707643.aspx) che include i criteri di integrità descritti nel corpo.

## <a name="get-service-health"></a>Ottenere l'integrità dei servizi
Restituisce lo stato di un'entità servizio. Contiene gli stati di integrità partizione. Input:

- [Required] Il nome del servizio (URI) che identifica il servizio.

- [Facoltativo] Il criterio di integrità applicazione utilizzato per ignorare il criterio manifesto dell'applicazione.

- [Facoltativo] Filtri per gli eventi e le partizioni per indicare le voci di interesse e devono essere restituiti nel risultato (ad esempio solo degli errori o avvisi e gli errori). Tutti gli eventi e le partizioni vengono utilizzate per la valutazione dell'integrità di entità aggregato, indipendentemente dal filtro.

### <a name="api"></a>API
Per ottenere l'integrità dei servizi tramite l'API, creare un `FabricClient` e chiamare il metodo [GetServiceHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getservicehealthasync.aspx) nella relativa HealthManager.

Nell'esempio seguente ottiene lo stato di un servizio con il nome di servizio specificato (URI):

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Il codice seguente ottiene l'integrità dei servizi per il nome di servizio specificato URI (), specificando i filtri e criteri personalizzati tramite [ServiceHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.servicehealthquerydescription.aspx):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Cmdlet per ottenere l'integrità dei servizi è [Ottenere ServiceFabricServiceHealth](https://msdn.microsoft.com/library/mt125984.aspx). Prima di tutto, connettersi al cluster utilizzando il cmdlet [ServiceFabricCluster Connetti](https://msdn.microsoft.com/library/mt125938.aspx) .

Il seguente cmdlet Ottiene l'integrità dei servizi tramite criteri di integrità predefinito:

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB',
                        Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                        ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 10
                        SentAt                : 3/22/2016 7:56:53 PM
                        ReceivedAt            : 3/22/2016 7:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        HealthState           : Warning
                        SequenceNumber        : 131031547693687021
                        SentAt                : 3/22/2016 9:12:49 PM
                        ReceivedAt            : 3/22/2016 9:12:49 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        fabric:/WordCount/WordCountService Secondary Partition a1f83a35-d6bf-4d39-b90d-28d15f39599b could not be placed, possibly,
                        due to the following constraints and properties:  
                        Placement Constraint: N/A
                        Depended Service: N/A

                        Constraint Elimination Sequence:
                        ReplicaExclusionStatic eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        ReplicaExclusionDynamic eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        ReplicaExclusionStatic:
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status:
                        None/None

                        ReplicaExclusionDynamic:
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status:
                        None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
```

### <a name="rest"></a>RESTO
È possibile ottenere l'integrità dei servizi con una [richiesta GET](https://msdn.microsoft.com/library/azure/dn707609.aspx) o una [richiesta POST](https://msdn.microsoft.com/library/azure/dn707646.aspx) che include i criteri di integrità descritti nel corpo.

## <a name="get-partition-health"></a>Ottenere integrità partizione
Restituisce lo stato di un'entità partizione. Contiene gli stati di integrità di replica. Input:

- [Required] ID (GUID) che identifica la partizione di partizione.

- [Facoltativo] Il criterio di integrità applicazione utilizzato per ignorare il criterio manifesto dell'applicazione.

- [Facoltativo] Filtri per gli eventi e repliche per indicare le voci di interesse e devono essere restituiti nel risultato (ad esempio solo degli errori o avvisi e gli errori). Tutti gli eventi e repliche vengono utilizzate per la valutazione dell'integrità di entità aggregato, indipendentemente dal filtro.

### <a name="api"></a>API
Per ottenere integrità partizione tramite l'API, creare un `FabricClient` e chiamare il metodo [GetPartitionHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getpartitionhealthasync.aspx) nella relativa HealthManager. Per specificare i parametri facoltativi, creare [PartitionHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.partitionhealthquerydescription.aspx).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
Cmdlet per ottenere lo stato di partizione è [Get-ServiceFabricPartitionHealth](https://msdn.microsoft.com/library/mt125869.aspx). Prima di tutto, connettersi al cluster utilizzando il cmdlet [ServiceFabricCluster Connetti](https://msdn.microsoft.com/library/mt125938.aspx) .

Il seguente cmdlet Ottiene lo stato per tutte le partizioni della **tessuti: / WordCount/WordCountService** servizio:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 131031502143040223
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844060
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844059
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844061
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844058
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 76
                        SentAt                : 3/22/2016 7:57:26 PM
                        ReceivedAt            : 3/22/2016 7:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTO
È possibile ottenere integrità partizione con una [richiesta GET](https://msdn.microsoft.com/library/azure/dn707683.aspx) o una [richiesta POST](https://msdn.microsoft.com/library/azure/dn707680.aspx) che include i criteri di integrità descritti nel corpo.

## <a name="get-replica-health"></a>Guida dell'integrità della replica
Restituisce l'integrità di una replica di informazioni sullo stato del servizio o un'istanza senza informazioni sullo stato del servizio. Input:

- [Required] Partizione ID (GUID) e replica ID che identifica la replica.

- [Facoltativo] Parametri di applicazione dell'integrità dei criteri a utilizzato per ignorare i criteri di manifesto applicazione.

- [Facoltativo] Filtri per gli eventi che specificano le voci di interesse e devono essere restituiti nel risultato (ad esempio solo degli errori o avvisi e gli errori). Tutti gli eventi vengono utilizzati per la valutazione dell'integrità di entità aggregato, indipendentemente dal filtro.

### <a name="api"></a>API
Per ottenere lo stato di replica tramite l'API, creare un `FabricClient` e chiamare il metodo [GetReplicaHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getreplicahealthasync.aspx) nella relativa HealthManager. Per specificare parametri avanzati, utilizzare [ReplicaHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.replicahealthquerydescription.aspx).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
Cmdlet per ottenere lo stato di replica è [Get-ServiceFabricReplicaHealth](https://msdn.microsoft.com/library/mt125808.aspx). Prima di tutto, connettersi al cluster utilizzando il cmdlet [ServiceFabricCluster Connetti](https://msdn.microsoft.com/library/mt125938.aspx) .

Il seguente cmdlet Ottiene lo stato della replica principale per tutte le partizioni del servizio:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
ReplicaId             : 131031502143040223
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131031502145556748
                        SentAt                : 3/22/2016 7:56:54 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTO
È possibile ottenere dell'integrità della replica con una [richiesta GET](https://msdn.microsoft.com/library/azure/dn707673.aspx) o una [richiesta POST](https://msdn.microsoft.com/library/azure/dn707641.aspx) che include i criteri di integrità descritti nel corpo.

## <a name="get-deployed-application-health"></a>Ottenere applicazione distribuita integrità
Restituisce lo stato di un'applicazione distribuita in un'entità nodo. Contiene gli stati di integrità servizio distribuito pacchetto. Input:

- [Required] Il nome dell'applicazione (URI) e il nodo nome (string) che identificano applicazione distribuita.

- [Facoltativo] Il criterio di integrità applicazione utilizzato per ignorare i criteri di manifesto applicazione.

- [Facoltativo] Filtri per gli eventi e di pacchetti di servizio distribuito che specificano le voci di interesse e devono essere restituiti nel risultato (ad esempio solo degli errori o avvisi e gli errori). Tutti gli eventi e servizio distribuito pacchetti vengono utilizzati per la valutazione dell'integrità di entità aggregato, indipendentemente dal filtro.

### <a name="api"></a>API
Per ottenere lo stato di un'applicazione distribuita in un nodo tramite l'API, creare un `FabricClient` e chiamare il metodo [GetDeployedApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync.aspx) nella relativa HealthManager. Per specificare i parametri facoltativi, utilizzare [DeployedApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedapplicationhealthquerydescription.aspx).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
Cmdlet per ottenere lo stato di applicazione distribuita è [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx). Prima di tutto, connettersi al cluster utilizzando il cmdlet [ServiceFabricCluster Connetti](https://msdn.microsoft.com/library/mt125938.aspx) . Per scoprire dove viene distribuita un'applicazione, eseguire [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) e osservare gli elementi figlio applicazione distribuita.

Il seguente cmdlet Ottiene lo stato della **tessuti: / WordCount** applicazione distribuita in **_Node_2**.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_2


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_2
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131031502143710698
                                     SentAt                : 3/22/2016 7:56:54 PM
                                     ReceivedAt            : 3/22/2016 7:57:12 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTO
È possibile ottenere integrità applicazione distribuita con una [richiesta GET](https://msdn.microsoft.com/library/azure/dn707644.aspx) o una [richiesta POST](https://msdn.microsoft.com/library/azure/dn707688.aspx) che include i criteri di integrità descritti nel corpo.

## <a name="get-deployed-service-package-health"></a>Ottenere distribuito integrità dei servizi del pacchetto
Restituisce lo stato di un'entità pacchetto servizio distribuito. Input:

- [Required] Il nome dell'applicazione (URI), nome del nodo (stringa) e il servizio manifesto nome (stringa) che identificano il pacchetto di servizio distribuito.

- [Facoltativo] Il criterio di integrità applicazione utilizzato per ignorare il criterio manifesto dell'applicazione.

- [Facoltativo] Filtri per gli eventi che specificano le voci di interesse e devono essere restituiti nel risultato (ad esempio solo degli errori o avvisi e gli errori). Tutti gli eventi vengono utilizzati per la valutazione dell'integrità di entità aggregato, indipendentemente dal filtro.

### <a name="api"></a>API
Per ottenere lo stato di un pacchetto di servizio distribuito tramite l'API, creare un `FabricClient` e chiamare il metodo [GetDeployedServicePackageHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync.aspx) nella relativa HealthManager. Per specificare i parametri facoltativi, utilizzare [DeployedServicePackageHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedservicepackagehealthquerydescription.aspx).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
Cmdlet per ottenere lo stato di pacchetto servizio distribuito è [Get-ServiceFabricDeployedServicePackageHealth](https://msdn.microsoft.com/library/mt163525.aspx). Prima di tutto, connettersi al cluster utilizzando il cmdlet [ServiceFabricCluster Connetti](https://msdn.microsoft.com/library/mt125938.aspx) . Per visualizzare in cui viene distribuita un'applicazione, eseguire [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) e osservare le applicazioni. Per visualizzare il servizio di pacchetti sono in un'applicazione, esaminare gli elementi figlio pacchetto servizio distribuito nell'output di [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx) .

Il seguente cmdlet Ottiene lo stato del pacchetto di servizio **WordCountServicePkg** del **tessuti: / WordCount** applicazione distribuita in **_Node_2**. L'entità ha **System.Hosting** report per pacchetto service esito negativo e punto di ingresso attivazione e registrazione tipo di servizio positiva.

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : _Node_2
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 131031502301306211
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 131031502301568982
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 131031502314788519
                        SentAt                : 3/22/2016 7:57:11 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTO
È possibile ottenere distribuito pacchetto integrità dei servizi con una [richiesta GET](https://msdn.microsoft.com/library/azure/dn707677.aspx) o una [richiesta POST](https://msdn.microsoft.com/library/azure/dn707689.aspx) che include i criteri di integrità descritti nel corpo.

## <a name="health-chunk-queries"></a>Query di blocco dell'integrità
Le query di blocco dell'integrità possono restituire elementi figlio cluster multilivello (ricorsivo), per i filtri di input. Supporta i filtri avanzati che consentono flessibilità esprimere quali specifici elementi figlio da restituire identificate da identificatore univoco o altri identificatore del gruppo e/o stato di integrità. Per impostazione predefinita, non è incluso, anziché i comandi di integrità che includono sempre figlio di primo livello elementi figlio.

Le [query di integrità](service-fabric-view-entities-aggregated-health.md#health-queries) restituiscono solo figlio di primo livello dell'entità specificata per filtri necessari. Per ottenere gli elementi figlio dell'elemento figlio, è necessario chiamare integrità aggiuntive API per ogni entità di interesse. Analogamente, per ottenere lo stato di entità specifiche, è necessario chiamare una integrità API per ogni entità desiderata. La query di blocco avanzata di filtro consente di richiedere più elementi di interesse in una query, ridurre la dimensione dei messaggi e il numero di messaggi.

Il valore della query blocco è che è possibile ottenere lo stato di integrità per più entità cluster (potenzialmente tutti cluster entità partire dal primo livello necessario) in una chiamata. È possibile esprimere query complesse integrità, ad esempio:

- Restituire solo le applicazioni all'errore e per le applicazioni includono tutti i servizi di avviso | errore. Per i servizi restituiti, includere tutte le partizioni.

- Restituisce solo lo stato delle 4 applicazioni, specificato mediante i relativi nomi.

- Restituisce solo lo stato delle applicazioni di un tipo di applicazione desiderata.

- Restituire entità tutti distribuita in un nodo. Restituisce tutte le applicazioni, tutti distribuiti applicazioni sul nodo specificato e tutti i pacchetti di servizio distribuito su tale nodo.

- Restituire ogni replica all'errore. Restituisce tutte le applicazioni, servizi, le partizioni e solo le repliche all'errore.

- Restituire tutte le applicazioni. Per un servizio specificato, includere tutte le partizioni.

La query di blocco dell'integrità attualmente non viene esposto solo per l'entità cluster. Restituisce un blocco di integrità cluster, che contiene:

- Lo stato di integrità cluster aggregato.

- Elenco di blocco dello stato dell'integrità dei nodi che rispettare i filtri di input.

- Elenco integrità dello stato di blocco delle applicazioni che rispettare i filtri di input. Ogni blocco di stato di integrità di applicazione contiene un elenco di blocco con tutti i servizi che rispettano i filtri di input e un elenco di blocco con tutte le applicazioni distribuite che rispettare i filtri. Procedura analoga per gli elementi figlio di servizi e applicazioni. In questo modo, tutte le entità del cluster possono essere potenzialmente restituite se richiesto, in modo gerarchico.

### <a name="cluster-health-chunk-query"></a>Query di blocco dell'integrità cluster
Restituisce lo stato dell'entità cluster e sono presenti i blocchi di stato integrità gerarchico figli necessari. Input:

- [Facoltativo] Criterio di integrità cluster utilizzato per valutare i nodi e gli eventi cluster.

- [Facoltativo] Integrità dei criteri mapping delle applicazioni, con i criteri di integrità utilizzati per ignorare i criteri di manifesto applicazione.

- [Facoltativo] Filtri per i nodi e le applicazioni di specificare le voci di interesse e devono essere restituiti nel risultato. I filtri siano per un gruppo o un entità di entità o sono applicabili a tutte le entità a quel livello. L'elenco di filtri può contenere un filtro generico e/o filtri per gli identificatori specifici alle singole entità restituiti dalla query. Se è vuoto, gli elementi figlio non vengono restituiti per impostazione predefinita.
Altre informazioni sui filtri [NodeHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.nodehealthstatefilter.aspx) e [ApplicationHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthstatefilter.aspx). In modo ricorsivo l'applicazione filtri può specificare filtri avanzati per gli elementi figlio.

Il risultato di blocco include gli elementi figlio che rispettano i filtri.

Attualmente, il blocco non restituiti valutazioni non corretti o eventi di entità. Che è possono ottenere informazioni aggiuntive utilizzando la query di integrità cluster esistente.

### <a name="api"></a>API
Per ottenere blocco integrità cluster, creare un `FabricClient` e chiamare il metodo [GetClusterHealthChunkAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync.aspx) nella relativa **HealthManager**. È possibile passare in [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthchunkquerydescription.aspx) per descrivere i criteri di integrità e filtri avanzati.

Il codice seguente ottiene cluster integrità blocco con i filtri avanzati.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Cmdlet per ottenere lo stato di cluster è [Get-ServiceFabricClusterChunkHealth](https://msdn.microsoft.com/library/mt644772.aspx). Prima di tutto, connettersi al cluster utilizzando il cmdlet [ServiceFabricCluster Connetti](https://msdn.microsoft.com/library/mt125938.aspx) .

Il codice seguente ottiene nodi solo se si tratta di errore ad eccezione di un nodo specifico, deve sempre essere restituito.

```xml
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters

HealthState                  : Error
NodeHealthStateChunks        :
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

Il seguente cmdlet Ottiene cluster blocco con i filtri di applicazione.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters

HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks :
                                   TotalCount            : 1

                                   ServiceName           : fabric:/WordCount/WordCountService
                                   HealthState           : Error
                                   PartitionHealthStateChunks :
                                       TotalCount            : 1

                                       PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                                       HealthState           : Error
                                       ReplicaHealthStateChunks :
                                           TotalCount            : 5

                                           ReplicaOrInstanceId   : 131031502143040223
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844060
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844059
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844061
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844058
                                           HealthState           : Error
```

Il cmdlet seguente restituisce tutte le entità distribuite in un nodo.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 1

                                       ServiceManifestName   : FAS
                                       HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 2

                                       ServiceManifestName   : WordCountServicePkg
                                       HealthState           : Ok

                                       ServiceManifestName   : WordCountWebServicePkg
                                       HealthState           : Ok
```

### <a name="rest"></a>RESTO
È possibile ottenere cluster integrità blocco con una [richiesta GET](https://msdn.microsoft.com/library/azure/mt656722.aspx) o una [richiesta POST](https://msdn.microsoft.com/library/azure/mt656721.aspx) che include criteri di integrità e filtri avanzati descritti nel corpo.

## <a name="general-queries"></a>Query generale
Le query generali restituiscono un elenco di entità servizio tessuti di un tipo specificato. Vengono esposti tramite l'API (tramite i metodi di **FabricClient.QueryManager**), i cmdlet di PowerShell e resto. Queste query eseguirà l'aggregazione sottoquery più componenti. Uno di essi è l' [integrità archiviare](service-fabric-health-introduction.md#health-store), in cui inserisce lo stato di integrità aggregato per ogni risultato della query.  

> [AZURE.NOTE] Query generale restituito lo stato di integrità aggregato dell'entità e che contengono dati integrità RTF. Se un'entità non è integro, è possibile eseguire query dell'integrità per ottenere tutte le informazioni di stato, incluse le valutazioni non corretti, stati di integrità figlio ed eventi di.

Se le query generali restituiscono uno stato di integrità sconosciuto per un'entità, è possibile che l'archivio di integrità non dispone di dati sull'entità. È inoltre possibile che una sottoquery per l'archivio di integrità non riuscita (ad esempio, si è verificato un errore di comunicazione o è stato limitato l'archivio di integrità). Completamento con una query di integrità per l'entità. Se la sottoquery errori temporanee, ad esempio problemi di rete, è possibile che venga completata questa query completamento. Possono anche offrono ulteriori dettagli dall'archivio di integrità sui perché l'entità non viene esposto.

Le query contenenti **HealthState** per entità sono:

- Elenco dei nodi: restituisce i nodi di elenco in cluster (tentativo).
  - API: [FabricClient.QueryClient.GetNodeListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getnodelistasync.aspx)
  - PowerShell: Get-ServiceFabricNode
- Elenco di applicazioni: restituisce l'elenco delle applicazioni del cluster (tentativo).
  - API: [FabricClient.QueryClient.GetApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getapplicationlistasync.aspx)
  - PowerShell: Get-ServiceFabricApplication
- Elenco di servizi: restituisce l'elenco dei servizi in un'applicazione (tentativo).
  - API: [FabricClient.QueryClient.GetServiceListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getservicelistasync.aspx)
  - PowerShell: Get-ServiceFabricService
- Elenco partizione: restituisce l'elenco delle partizioni in un servizio (tentativo).
  - API: [FabricClient.QueryClient.GetPartitionListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getpartitionlistasync.aspx)
  - PowerShell: Get-ServiceFabricPartition
- Elenco di replica: restituisce l'elenco delle repliche in una partizione (tentativo).
  - API: [FabricClient.QueryClient.GetReplicaListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getreplicalistasync.aspx)
  - PowerShell: Get-ServiceFabricReplica
- Distribuzione elenco di applicazioni: restituisce l'elenco delle applicazioni distribuite su un nodo.
  - API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync.aspx)
  - PowerShell: Get-ServiceFabricDeployedApplication
- Distribuito elenco pacchetto di servizi: restituisce l'elenco di pacchetti in un'applicazione distribuita.
  - API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync.aspx)
  - PowerShell: Get-ServiceFabricDeployedApplication

> [AZURE.NOTE] Alcune delle query restituiscono i risultati. Invio di queste query è riportato un elenco derivato dalla [PagedList<T>](https://msdn.microsoft.com/library/azure/mt280056.aspx). Se i risultati non corrispondono a un messaggio, viene restituita solo una pagina e un ContinuationToken che tiene traccia del punto in cui è stata interrotta enumerazione. È necessario continuare a chiamare la stessa query e passare il token di continuazione dalla query precedente per ottenere risultati successivi.

### <a name="examples"></a>Esempi

Il codice seguente ottiene il coinvolgimento di applicazioni del cluster:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Il seguente cmdlet Ottiene i dettagli dell'applicazione per la struttura: / applicazione WordCount. Si noti che lo stato di integrità avviso.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

Il seguente cmdlet Ottiene i servizi con uno stato di integrità di avviso visualizzato:

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## <a name="cluster-and-application-upgrades"></a>Aggiornamenti cluster e applicazione
Durante l'aggiornamento monitorato del cluster e applicazione, tessuti servizio verifica dell'integrità per garantire che tutti gli elementi siano corretti. Se un'entità è danneggiata come esaminati tramite criteri di integrità configurato, l'aggiornamento si applica criteri specifici di aggiornamento per determinare l'azione successiva. L'aggiornamento potrebbe essere sospesa per consentire l'interazione utente (ad esempio correggere le condizioni di errore o modifica di criteri) o potrebbe automaticamente ripristinare la precedente versione funzionante.

Durante l'aggiornamento di *cluster* , è possibile ottenere lo stato di aggiornamento cluster. Lo stato di aggiornamento include le valutazioni non corretti, scegliere cosa è integro nel cluster. Se l'aggiornamento viene annullata a causa di problemi di integrità, lo stato degli aggiornamento memorizza l'ultimi motivi non corretti. Queste informazioni consente agli amministratori di provare a causa del problema dopo l'aggiornamento all'annullamento o interrotto.

Analogamente, durante l'aggiornamento *dell'applicazione* le valutazioni non corretti sono contenute in stato di aggiornamento dell'applicazione.

Di seguito è riportato lo stato di aggiornamento di applicazioni per un'infrastruttura modificata: / applicazione WordCount. Errore viene quindi una delle relative repliche. L'aggiornamento è in sequenza in quanto i controlli di integrità non sono soddisfatti.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Altre informazioni sull' [aggiornamento dell'applicazione di servizio tessuti](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Utilizzare le valutazioni dell'integrità per la risoluzione
Ogni volta che si verifica un problema con i cluster oppure un'applicazione, esaminare lo stato cluster o applicazione per individuare qual è il problema. Le valutazioni integre forniscono dettagli su cosa attivate uno stato corrente. Se necessario, è possibile drill-down entità figlio non corretti per identificare la causa principale.

> [AZURE.NOTE] Le valutazioni integre mostrano che il primo motivo l'entità viene valutato allo stato di integrità corrente. Potrebbero essere presenti più altri eventi che attivano questo stato, ma non vengono essere applicate le valutazioni. Per ottenere ulteriori informazioni, drill-down entità dell'integrità per determinare tutti i report non corretti in cluster.

## <a name="next-steps"></a>Passaggi successivi
[Report di stato di sistema di utilizzo per la risoluzione dei](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Aggiungere report di stato servizio tessuti personalizzato](service-fabric-report-health.md)

[Come segnalare e verificare l'integrità del servizio](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorare e diagnosticare servizi localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Aggiornamento dell'applicazione di servizio tessuti](service-fabric-application-upgrade.md)
