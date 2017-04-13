<properties
   pageTitle="Risolvere i problemi con report di stato di sistema | Microsoft Azure"
   description="Descrive il report di stato inviato da componenti di Azure servizio tessuti e il relativo utilizzo per cluster di risoluzione dei problemi o problemi delle applicazioni."
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

# <a name="use-system-health-reports-to-troubleshoot"></a>Report di stato di sistema di utilizzo per la risoluzione dei

Azure tessuti servizio componenti report all'esterno della casella in tutte le entità del cluster. [Integrità archiviare](service-fabric-health-introduction.md#health-store) crea ed Elimina entità in base ai report di sistema. Vengono inoltre organizzati in una gerarchia che acquisisce interazioni entità.

> [AZURE.NOTE] Per comprendere i concetti relativi alle condizioni, per saperne di più al [modello di integrità servizio tessuti](service-fabric-health-introduction.md).

Report di stato di sistema fornire visibilità nei cluster e problemi di contrassegno tramite integrità e la funzionalità dell'applicazione. Per applicazioni e servizi, report di stato di sistema verificare che entità sono implementate e corretto dal punto di vista dell'infrastruttura di servizio. I report non forniscono qualsiasi il monitoraggio dell'integrità della logica business del servizio o il rilevamento dei processi bloccati. Servizi degli utenti è possono migliorare i dati di integrità con informazioni specifiche per la logica.

> [AZURE.NOTE] Report di stato watchdog saranno visibili solo *dopo* i componenti del sistema esistente oppure creare un'entità. Quando si elimina un'entità, l'archivio di integrità Elimina automaticamente tutti i report di stato è associati. Lo stesso avviene quando viene creata una nuova istanza dell'entità (ad esempio, viene creata una nuova istanza di replica di servizi). Tutti i report associati all'istanza precedente vengono eliminati e cancellati dall'archivio.

Il componente del sistema report identificato dall'origine che inizia con la "**System.**" prefisso. Watchdog non è possibile utilizzare lo stesso prefisso per le origini, come report con parametri non validi vengono rifiutati.
Diamo un'occhiata alcuni report di sistema per comprendere l'evento che genera e su come risolvere i problemi possibili che rappresentano.

> [AZURE.NOTE] Servizio tessuti continua aggiungere report condizioni di interesse che consentono di migliorare la visibilità sullo novità il cluster e l'applicazione.

## <a name="cluster-system-health-reports"></a>Report di stato di sistema cluster
L'entità dell'integrità cluster viene automaticamente creata nell'archivio di integrità. Se tutti gli elementi funziona correttamente, non dispone di un report di sistema.

### <a name="neighborhood-loss"></a>Perdita di risorse
**System.Federation** segnala un errore quando rileva una perdita di risorse. Il report è stata inviata da singoli nodi e l'ID di nodo sia incluso nel nome della proprietà. Risorse di una perdita dell'intero anello tessuti servizio, è probabile che in genere due eventi (entrambi i lati del report distanza). Se più Villaggi vengono persi, esistono altri eventi.

Il report specifica il timeout di leasing globale come la durata. Il report viene inviato nuovamente ogni metà della durata TTL come la condizione rimane attiva. L'evento viene rimosso automaticamente alla scadenza. Rimuovere quando comportamento scaduto assicura che il report viene pulito dall'archivio integrità correttamente, anche se il nodo report verso il basso.

- **SourceId**: System.Federation
- **Proprietà**: inizia con **risorse** e vengono fornite informazioni nodo
- **Passaggi successivi**: esaminare perché le risorse viene perso (ad esempio, controllare le comunicazioni tra nodi cluster).

## <a name="node-system-health-reports"></a>Report di stato di sistema di nodo
**System.FM**, che rappresenta il servizio di gestione di Failover, è l'autorità che gestisce le informazioni sui nodi di cluster. Ogni nodo deve avere un report da System.FM con il proprio stato. Entità nodo vengono rimossi quando lo stato di nodo viene rimosso (vedere [RemoveNodeStateAsync](https://msdn.microsoft.com/library/azure/mt161348.aspx)).

### <a name="node-updown"></a>Nodo crescenti/decrescenti
System.FM riporta come OK quando il nodo viene aggiunto squillo (è in esecuzione). Viene segnalato un errore quando il nodo partenza inoltrare la chiamata (è verso il basso, uno per l'aggiornamento o semplicemente perché non è riuscita). Gerarchia dell'integrità creata dall'archivio di integrità interviene su entità distribuita in correlazione con i report di nodo System.FM. Considera il nodo padre virtuale di tutte le entità distribuite. Le entità distribuite in tale nodo vengono esposti tramite query se il nodo viene segnalato come disponibile da System.FM, con la stessa istanza dell'istanza associati alle entità. Quando System.FM indica che il nodo non funziona o si riavvia (una nuova istanza), l'archivio di integrità pulisce automaticamente le entità distribuite che possono trovarsi solo sul nodo verso il basso o sull'occorrenza precedente del nodo.

- **SourceId**: System.FM
- **Proprietà**: stato
- **Passaggi successivi**: se il nodo verso il basso per un aggiornamento, deve sorgere nuovamente dopo aver aggiornato. In questo caso, lo stato di integrità deve essere cambiato nuovo su OK. Se il nodo non torni o non riesce, indagini più il problema.

Nell'esempio seguente viene visualizzato l'evento System.FM con uno stato di integrità di OK per nodo:

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### <a name="certificate-expiration"></a>Scadenza certificato
**System.FabricNode** segnala un avviso quando i certificati usati dal nodo sono accanto alla scadenza. Esistono tre certificati per nodo: **Certificate_cluster**, **Certificate_server**e **Certificate_default_client**. Quando la scadenza è almeno due settimane, lo stato di integrità di report è OK. Quando la scadenza è all'interno di due settimane, il tipo di report è un avviso. TTL gli eventi è infinito e vengono rimossi quando un nodo lascia il cluster.

- **SourceId**: System.FabricNode
- **Proprietà**: inizia con **certificato** e sono presenti altre informazioni sul tipo di certificato
- **Passaggi successivi**: aggiornare i certificati qualora siano accanto alla scadenza.

### <a name="load-capacity-violation"></a>Caricare violazione capacità
Il servizio di bilanciamento del carico tessuti segnala un avviso se rileva una violazione di capacità di nodo.

 - **SourceId**: System.PLB
 - **Proprietà**: inizia con **la capacità**
 - **Passaggi successivi**: controllare fornite metriche e visualizzare la capacità corrente del nodo.

## <a name="application-system-health-reports"></a>Report di stato del sistema dell'applicazione
**System.CM**, che rappresenta il servizio di gestione Cluster, è l'autorità che gestisce le informazioni relative a un'applicazione.

### <a name="state"></a>Stato
System.CM riporta come OK quando l'applicazione è stata creata o aggiornata. Comunica l'archivio di stato quando l'applicazione è stata eliminata, in modo che può essere rimosso dall'archivio.

- **SourceId**: System.CM
- **Proprietà**: stato
- **Passaggi successivi**: se l'applicazione è stata creata, includere il rapporto di stato gestione Cluster. In caso contrario, verificare lo stato dell'applicazione tramite l'esecuzione di una query (ad esempio, il cmdlet di PowerShell * *Get-ServiceFabricApplication NomeApplicazione *NomeApplicazione** *).

Nell'esempio seguente mostra l'evento di stato sul **tessuti: / WordCount** applicazione:

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## <a name="service-system-health-reports"></a>Report di stato di sistema di servizio
**System.FM**, che rappresenta il servizio di gestione di Failover, è l'autorità che gestisce le informazioni sui servizi.

### <a name="state"></a>Stato
System.FM riporta come OK quando il servizio è stato creato. Elimina l'entità dall'archivio di integrità quando il servizio è stato eliminato.

- **SourceId**: System.FM
- **Proprietà**: stato

L'esempio seguente mostra l'evento di stato del servizio **tessuti: / WordCount/WordCountService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### <a name="unplaced-replicas-violation"></a>Violazione di repliche non spostati
**System.PLB** segnala un avviso se non viene trovata una posizione per una o più replica di servizi. Il report viene rimosso quando scade.

- **SourceId**: System.FM
- **Proprietà**: stato
- **Passaggi successivi**: controllare i vincoli di servizio e lo stato corrente della posizione.

Nell'esempio seguente mostra una violazione di un servizio configurato con 7 destinazione in un cluster con 5 nodi:

```xml
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService


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
                        SequenceNumber        : 131032232425505477
                        SentAt                : 3/23/2016 4:14:02 PM
                        ReceivedAt            : 3/23/2016 4:14:03 PM
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
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="partition-system-health-reports"></a>Report di stato di sistema partizione
**System.FM**, che rappresenta il servizio di gestione di Failover, è l'autorità che gestisce le partizioni del servizio di informazioni.

### <a name="state"></a>Stato
System.FM riporta come OK quando la partizione è stata creata e integrità. Elimina l'entità dall'archivio di integrità quando la partizione viene eliminata.

Se la partizione è sotto il conteggio di replica minima, viene segnalato un errore. Se la partizione non è sotto il conteggio di replica minima, ma si trova sotto il conteggio di replica di destinazione, viene segnalato un avviso. Se la partizione è la perdita di base, System.FM segnalato un errore.

Altri eventi importanti includono un messaggio di avviso durante la riconfigurazione richiede più tempo del previsto e durante la compilazione richiede più tempo del previsto. Previsto volte per la compilazione e riconfigurazione possono essere configurati in base a scenari di servizi. Ad esempio, se dispone di un servizio terabyte dello stato, ad esempio Database SQL, la compilazione richiede più tempo per un servizio con una piccola quantità di stato.

- **SourceId**: System.FM
- **Proprietà**: stato
- **Passaggi successivi**: se lo stato di integrità non è consentito, è possibile che alcune repliche non sono stati creati, aperti o alzati di livello principale o secondario correttamente. In molti casi, la causa è un errore di servizio in implementazione Apri o Modifica ruolo.

Nell'esempio seguente mostra una partizione integro:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

Nell'esempio seguente mostra lo stato di una partizione è inferiore al conteggio di replica di destinazione. Il passaggio successivo consiste nell'eseguire la descrizione della partizione, che mostra come configurarlo: **MinReplicaSetSize** due e **TargetReplicaSetSize** sette. Ottenere il numero dei nodi del cluster: cinque. Pertanto in questo caso, due repliche non è possibile trovarsi.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### <a name="replica-constraint-violation"></a>Violazione del vincolo replica
**System.PLB** segnala un avviso se rileva una violazione del vincolo duplicata e non è possibile inserire repliche della partizione.

- **SourceId**: System.PLB
- **Proprietà**: inizia con **ReplicaConstraintViolation**

## <a name="replica-system-health-reports"></a>Report di stato di sistema di replica
**System.RA**, che rappresenta il componente Agente riconfigurazione, è l'autorità per lo stato di replica.

### <a name="state"></a>Stato
**System.RA** riporta come OK quando la replica è stata creata.

- **SourceId**: System.RA
- **Proprietà**: stato

Nell'esempio seguente mostra una replica integro:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### <a name="replica-open-status"></a>Stato aperto replica
La descrizione di questo rapporto di stato contiene l'ora di inizio (Coordinated Universal Time) quando la chiamata API è stata chiamata.

**System.RA** segnala un avviso se la replica aperta richiede più tempo del periodo configurato (impostazione predefinita: 30 minuti). Se l'API influisce sulla disponibilità del servizio, il report viene generato in modo più rapido (a un intervallo configurabile, non prevede un valore predefinito di 30 secondi). Il tempo include il tempo impiegato per Apri replica e aprire il servizio. La proprietà diventa OK se è stato completato l'Apri.

- **SourceId**: System.RA
- **Proprietà**: **ReplicaOpenStatus**
- **Passaggi successivi**: se lo stato di integrità non è consentito, provare a utilizzare perché la replica aperta richiede più tempo del previsto.

### <a name="slow-service-api-call"></a>Ridurre la velocità di chiamata API del servizio
Report **System.RAP** e **System.Replicator** un avviso se una chiamata al codice servizio utente richiede più tempo configurato. L'avviso verrà svuotato quando termine della chiamata.

- **SourceId**: System.RAP o System.Replicator
- **Proprietà**: il nome dell'API lenta. La descrizione vengono fornite informazioni dettagliate relative al tempo che l'API è stato in sospeso.
- **Passaggi successivi**: esaminare perché la chiamata richiede più tempo del previsto.

Nell'esempio seguente mostra una partizione la perdita di base e le operazioni indagini effettuate per stabilire il motivo. Una replica è stato di integrità avviso, in modo che viene visualizzato il relativo stato. Mostra che il funzionamento del servizio richiede più tempo del previsto, un evento segnalato dal System.RAP. Dopo le informazioni ricevute, è necessario esaminare il codice di servizio e analizzare non esiste. In questo caso, l'implementazione di **RunAsync** del servizio informazioni sullo stato genera un'eccezione non gestita. Le repliche sono riciclo, in modo che non sarà possibile visualizzare tutte le repliche in stato di avviso. È possibile ripetere visualizzato lo stato di integrità e cercare le differenze tra l'ID replica. In alcuni casi, i tentativi possono che vengano indicazioni.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

Quando si avvia l'applicazione difettoso nel, le finestre di eventi di diagnostica mostrano eccezione da RunAsync:

![Eventi di diagnostica Visual Studio 2015: errore RunAsync in tessuti: / HelloWorldStatefulApplication.][1]

Eventi di diagnostica Visual Studio 2015: errore di RunAsync **tessuti: / HelloWorldStatefulApplication**.

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### <a name="replication-queue-full"></a>Coda di replica completa
**System.Replicator** segnala un avviso se la coda di replica è piena. Nella finestra principale, in genere infatti una o più repliche secondarie sono lente riconoscendo operazioni. Nel database secondario, questo in genere si verifica quando il servizio è lento applicare le operazioni. Il messaggio di avviso è deselezionata quando la coda non è più completa.

- **SourceId**: System.Replicator
- **Proprietà**: **PrimaryReplicationQueueStatus** o **SecondaryReplicationQueueStatus**in base al ruolo di replica

### <a name="slow-naming-operations"></a>Operazioni di denominazione lente

**System.NamingService** report integrità sul replica primaria quando un'operazione di denominazione richiede più tempo accettabile. Esempi di operazioni di denominazione sono [CreateServiceAsync](https://msdn.microsoft.com/library/azure/mt124028.aspx) o [DeleteServiceAsync](https://msdn.microsoft.com/library/azure/mt124029.aspx). Altri metodi sono disponibili nella sezione FabricClient, ad esempio in [metodi di gestione del servizio](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.aspx) o [metodi di gestione delle proprietà](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.propertymanagementclient.aspx).

> [AZURE.NOTE] Il servizio di denominazione risolve i nomi dei servizi in una posizione nel cluster e consente agli utenti di gestire proprietà e i nomi dei servizi. Si tratta di un'infrastruttura di servizio suddiviso mantenuta servizio. Una delle partizioni rappresenta il proprietario autorità contenente metadati relativi a tutti i nomi di servizio tessuti e i servizi. I nomi di servizio tessuti sono mappati a partizioni diverse, denominate partizioni nome proprietario, in modo che il servizio può essere esteso. Per ulteriori informazioni su [servizio di denominazione](service-fabric-architecture.md).

Quando un'operazione di denominazione richiede più tempo del previsto, l'operazione è contrassegnata con un report di avviso sulla *replica principale della partizione di servizio di denominazione che serve l'operazione*. Se l'operazione ha esito positivo, viene cancellato l'avviso. Se l'operazione ha un errore, il rapporto di stato include dettagli sull'errore.

- **SourceId**: System.NamingService
- **Proprietà**: inizia con prefisso **Duration_** e identifica l'operazione lenta e il nome dell'infrastruttura di servizio in cui viene applicata l'operazione. Ad esempio, se creare servizio tessuti nome: applicazione/servizio impiega troppo tempo, la proprietà è Duration_AOCreateService.fabric:/MyApp/MyService. AO fa riferimento al ruolo di partizione di denominazione per questo nome e l'operazione.
- **Passaggi successivi**: controllo perché l'operazione di denominazione non riesce. Ogni operazione può avere diverse cause. Ad esempio, eliminare servizio sia bloccato su un nodo perché l'applicazione host mantiene un arresto anomalo su un nodo a causa di un bug utente nel codice servizio.

L'esempio seguente viene mostrata un'operazione di servizio di creazione. L'operazione ha più di durata configurata. AO tentativi e invia lavoro su No. NON completata l'ultima operazione con Timeout. In questo caso la stessa replica è primaria per il AO e alcun ruolo.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>Report di stato di sistema DeployedApplication
**System.Hosting** è l'autorità su entità distribuita.

### <a name="activation"></a>Attivazione
System.Hosting riporta come OK quando un'applicazione è stata attivata sul nodo. In caso contrario, viene segnalato un errore.

- **SourceId**: System.Hosting
- **Proprietà**: l'attivazione, inclusa la versione di distribuzione
- **Passaggi successivi**: se l'applicazione è danneggiato, provare a utilizzare il motivo per cui l'attivazione non riuscita.

Nell'esempio seguente mostra l'attivazione:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Download
**System.Hosting** segnala un errore se si verifica un errore di download del pacchetto dell'applicazione.

- **SourceId**: System.Hosting
- **Proprietà**: * *Download:*RolloutVersion***
- **Passaggi successivi**: esaminare motivo del mancato il download del nodo.

## <a name="deployedservicepackage-system-health-reports"></a>Report di stato di sistema DeployedServicePackage
**System.Hosting** è l'autorità su entità distribuita.

### <a name="service-package-activation"></a>Attivazione del pacchetto del servizio
Se l'attivazione del pacchetto di servizio sul nodo ha esito positivo, System.Hosting report come OK. In caso contrario, viene segnalato un errore.

- **SourceId**: System.Hosting
- **Proprietà**: attivazione
- **Passaggi successivi**: esaminare il motivo per cui l'attivazione non riuscita.

### <a name="code-package-activation"></a>Attivazione del pacchetto di codice
**System.Hosting** riporta come OK per ogni pacchetto di codice se l'attivazione viene eseguita correttamente. Se l'attivazione non riesce, viene segnalato un avviso in base alla configurazione. Se termina con un messaggio di errore maggiore configurato **CodePackageHealthErrorThreshold** **CodePackage** non riesce ad attivare oppure hosting segnalato un errore. Se un pacchetto di servizio contiene più pacchetti di codice, viene generato un report di attivazione per ognuna di esse.

- **SourceId**: System.Hosting
- **Proprietà**: utilizza il prefisso **CodePackageActivation** e contiene il nome del pacchetto codice e il punto di ingresso come * *CodePackageActivation:*CodePackageName*:*Il punto di ingresso/SetupEntryPoint* ** (ad esempio **CodePackageActivation:Code:SetupEntryPoint**)

### <a name="service-type-registration"></a>Registrazione del tipo di servizio
Se il tipo di servizio è stato registrato, **System.Hosting** report come OK. Viene segnalato un errore se la registrazione non è stata effettuata nel tempo (in base alla configurazione utilizzando **ServiceTypeRegistrationTimeout**). Se il tipo di servizio viene annullata la registrazione dal nodo, in questo modo è stato chiuso in fase di esecuzione. Hosting segnala un avviso.

- **SourceId**: System.Hosting
- **Proprietà**: utilizza il prefisso **ServiceTypeRegistration** e contiene il nome del tipo di servizio (ad esempio **ServiceTypeRegistration:FileStoreServiceType**)

Nell'esempio seguente mostra un pacchetto di servizio distribuito integro:

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Download
**System.Hosting** segnala un errore se si verifica un errore di download del pacchetto di servizio.

- **SourceId**: System.Hosting
- **Proprietà**: * *Download:*RolloutVersion***
- **Passaggi successivi**: esaminare motivo del mancato il download del nodo.

### <a name="upgrade-validation"></a>Convalida aggiornamento
**System.Hosting** segnala un errore se convalida durante l'aggiornamento ha esito negativo o se l'aggiornamento ha esito negativo sul nodo.

- **SourceId**: System.Hosting
- **Proprietà**: utilizza il prefisso **FabricUpgradeValidation** e contiene la versione di aggiornamento
- **Descrizione**: errore di puntamento

## <a name="next-steps"></a>Passaggi successivi
[Visualizza report integrità servizio tessuti](service-fabric-view-entities-aggregated-health.md)

[Come segnalare e verificare l'integrità del servizio](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorare e diagnosticare servizi localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Aggiornamento dell'applicazione di servizio tessuti](service-fabric-application-upgrade.md)
