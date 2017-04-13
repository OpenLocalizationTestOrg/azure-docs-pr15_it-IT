<properties
   pageTitle="Testabilità: Servizio comunicazioni | Microsoft Azure"
   description="La comunicazione al servizio è un punto di integrazione critico di un'applicazione di servizio tessuti. In questo articolo vengono illustrate considerazioni sulla progettazione e tecniche di testing."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/06/2016"
   ms.author="vturecek"/>

# <a name="service-fabric-testability-scenarios-service-communication"></a>Scenari di testabilità tessuti servizio: servizio di comunicazione

Microservices e superficie orientata ai servizi architettonica stili naturalmente in Azure servizio tessuti. In questi tipi di architetture distribuite, applicazioni microservice componenti sono in genere composti da più servizi che è necessario comunicare con loro. Nei casi persino più semplici, in genere sarà necessario almeno un servizio web senza informazioni sullo stato e un servizio di archiviazione dati informazioni sullo stato che devono comunicare.

La comunicazione al servizio è un punto di integrazione critico di un'applicazione, in quanto ogni servizio presenta una API remote ad altri servizi. Utilizzo di un insieme di limiti API che prevede in genere i/o richiede particolare attenzione, con una buona quantità di test e convalida.

Sono disponibili numerosi aspetti relativi alla quando questi limiti di servizio sono collegati in un sistema distribuito:

 - *Protocollo*. Si utilizzerà HTTP per maggiore interoperabilità o un protocollo binario personalizzato da ottimizzare la velocità?
 - *Gestione degli errori*. Come vengono gestiti gli errori permanenti e temporanei? Cosa succede quando si sposta un servizio in un nodo diverso?
 - *Timeout e latenza*. Nelle applicazioni a più livelli, la modalità di ogni livello di servizio Gestione latenza in pila e all'utente?

Se si usa uno dei componenti di comunicazione servizio incorporato forniti dal servizio tessuti o si creano personalizzata, le interazioni tra i servizi di test è fondamentale per garantire flessibilità nell'applicazione.

## <a name="prepare-for-services-to-move"></a>Preparare l'ambiente per spostare dei servizi

Istanze del servizio possono spostarsi nel tempo. Questa operazione è particolarmente vera quando sono configurati con metriche di carico per bilanciamento del carico personalizzati ottimale delle risorse. Servizio tessuti sposta le istanze del servizio per ottimizzare la disponibilità anche durante gli aggiornamenti, failover, scalabilità e altre situazioni che si verificano per tutta la durata di un sistema distribuito.

Come i servizi di spostarsi all'interno del cluster, i clienti e altri servizi devono essere preparati per gestire due scenari quando vengono comunicare a un servizio:

- La replica di istanza o una partizione di servizio è stato spostato dopo l'ultimo parlato a esso. Si tratta di una parte di un ciclo di vita di servizio normale e dovrebbe essere previsto verificarsi durante la durata dell'applicazione.
- La replica di istanza o una partizione di servizio è in corso lo spostamento. Anche se nel servizio tessuti si verifica molto rapidamente failover di un servizio da un nodo a altro, può essere un ritardo nella disponibilità se il componente di comunicazione del servizio è lento iniziare.

Gestione scenari normalmente è importante per un sistema senza problemi. A tale scopo, tenere presente che:

- Ogni servizio che può essere collegato a ha un *indirizzo* che è in ascolto (ad esempio HTTP o WebSockets). Quando si sposta un'istanza del servizio o una partizione, viene modificato il punto finale indirizzo. (Sposta in un altro nodo con un indirizzo IP diverso). Se si sta utilizzando i componenti di comunicazione integrate, gestiti nuovamente risoluzione di indirizzi servizio dell'utente.
- È possibile che un aumento temporaneo latenza del servizio all'avvio istanza servizio la relativa comunicare ascoltatore del nuovo. Ciò dipende il servizio apre come rapidamente comunicare ascoltatore dopo lo spostamento istanza del servizio.
- Tutte le connessioni esistenti è necessario essere chiuso e riaperto dopo il servizio di apertura in un nuovo nodo. Riavvio o arresto regolare nodo consente ora per le connessioni esistenti arrestare normalmente.

### <a name="test-it-move-service-instances"></a>Eseguire il test: spostare le istanze di servizio

Utilizzando strumenti testabilità dell'infrastruttura di servizio, è possibile creare uno scenario di test per verificare queste situazioni in diversi modi:

1. Spostare replica primaria del servizio informazioni sullo stato.

    È possibile spostare replica primaria di una partizione di servizio informazioni sullo stato per diversi motivi. Consente di replica primaria di una partizione specifica per vedere come il provider di servizi di rispondere per lo spostamento modo molto controllato di destinazione.

    ```powershell

    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService

    ```

2. Interrompere un nodo.

    Quando un nodo viene interrotto, viene spostato tessuti servizio tutte le istanze del servizio o partizioni che sono state su tale nodo a uno degli altri nodi disponibili nel cluster. Consente di verificare una situazione in cui un nodo si perdono il cluster e tutte le istanze di servizio e repliche su tale nodo sono necessario spostare.

    È possibile interrompere un nodo utilizzando il cmdlet di PowerShell **Interrompi ServiceFabricNode** :

    ```powershell

    PS > Restart-ServiceFabricNode -NodeName Node_1

    ```

## <a name="maintain-service-availability"></a>Mantenere la disponibilità di servizio

Come una piattaforma tessuti servizio è progettato per fornire disponibilità dei servizi. Ma in casi sottostante i problemi di infrastruttura possono causare indisponibilità. È importante verificare anche per gli scenari seguenti.

Informazioni sullo stato servizi utilizzano un sistema di base per replicare lo stato di disponibilità. Questo errore indica che una base di repliche deve essere disponibile per eseguire le operazioni di scrittura. Raramente, ad esempio un errore hardware diffuso, una base di repliche potrebbe non essere disponibile. In questi casi, non sarà possibile eseguire le operazioni di scrittura, ma sarà comunque possibile eseguire le operazioni di lettura.

### <a name="test-it-write-operation-unavailability"></a>Eseguire il test: scrivere indisponibilità operazione

Usando gli strumenti di testabilità in tessuti servizio, è possibile inserire un errore che provoca perdita di base come test. Anche se questo scenario è raro, è importante che client e i servizi che dipendono da un servizio informazioni sullo stato sono pronto per gestire le situazioni in cui non è possibile apportare scrivere richieste. È anche importante il servizio informazioni sullo stato tenere presente questa possibilità che è possibile normalmente comunicare ai chiamanti.

Si può provocare perdita di base utilizzando il cmdlet di PowerShell **Richiama ServiceFabricPartitionQuorumLoss** :

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

In questo esempio abbiamo impostato `QuorumLossMode` a `QuorumReplicas` per indicare che si desidera indurre perdita di base senza chiudere ogni replica. In questo modo, le operazioni di lettura sono comunque. Per testare uno scenario in cui non è disponibile un'intera partizione, è possibile impostare questo parametro `AllReplicas`.

## <a name="next-steps"></a>Passaggi successivi

[Ulteriori informazioni sulle azioni testabilità](service-fabric-testability-actions.md)

[Altre informazioni sugli scenari testabilità](service-fabric-testability-scenarios.md)
