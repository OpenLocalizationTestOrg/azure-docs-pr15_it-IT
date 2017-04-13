<properties
   pageTitle="Limitazione in Gestione risorse cluster tessuti servizio | Microsoft Azure"
   description="Informazioni su come configurare limita forniti da Gestione risorse servizio tessuti Cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>


# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>La limitazione per il comportamento di gestione risorse servizio tessuti Cluster
Anche se è stata configurata correttamente la gestione di risorse Cluster, cluster possa ottenere interrotta. Ad esempio può essere simultaneo nodo o guasto dominio errori: cosa accadrebbe se che si sono verificati durante l'aggiornamento? Gestione risorse tenterà ottimali per risolvere tutti gli elementi, ma in volte questo può essere necessario tenere presente che una barriera in modo che il cluster avere la possibilità di stabilizzare (nodi che avranno eseguire nuovamente, le condizioni di rete di correzione sole, vengono distribuite bit corretto). Per assistenza con questi tipi di situazioni, la gestione di risorse Cluster tessuti servizio includono limita diverse. Si noti che questi limita abbastanza eccedono e usato in genere non deve essere a meno che non si è verificato alcuni attenzione matematiche avviene attorno la quantità di lavoro in parallelo che in realtà può essere eseguita in cluster, nonché un frequenti necessario rispondere a questi tipi di (sviluppo) eventi riconfigurazione macro non pianificato (AKA: "Molto giorni non valido").

In genere, è consigliabile evitare molto errate giorni tra le altre opzioni (ad esempio codice normali aggiornamenti ed evitare overscheduling per iniziare il cluster) invece di limitazione il cluster per impedire l'utilizzo delle risorse durante per risolvere il problema). Il limita includono valori predefiniti che abbiamo trovato esperienza essere ok valori predefiniti, ma è probabilmente necessario consultare e ottimizzare le loro per il carico effettivo. Mentre non eccessivamente limitare o il caricamento che il cluster è una buona norma che è possibile stabilire che non esistono casi che (fino a quando non è possibile risolvere il problema) in cui è necessario disporre di un paio di limita, anche se significa che il cluster di richiederà più tempo stabilizzare.

##<a name="configuring-the-throttles"></a>Configurazione di limita
Limita inclusi per impostazione predefinita è:

-   GlobalMovementThrottleThreshold: consente di controllare il numero totale dei movimenti del cluster alcuni tempo (definito come GlobalMovementThrottleCountingInterval, valore in secondi)
-   MovementPerPartitionThrottleThreshold: consente di controllare il numero totale dei movimenti per tutte le partizioni servizio alcune tempo (il MovementPerPartitionThrottleCountingInterval, valore in secondi)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

Tenere presente che la maggior parte dei casi che visto in clienti usare queste limita che siano trascorse in quanto sono già in un ambiente di risorse (ad esempio larghezza di banda di rete limitato in singoli nodi o dischi che non sono state fino ai requisiti di replica parallele in cui vengono inseriti su di essi) che consente di gestire tali operazioni non è riuscita o è lenta comunque.  In questi casi clienti sono stati familiarità con la conoscenza che si estendono potenzialmente la quantità di tempo necessario cluster raggiunga stabile, tra cui sapere che potrebbe terminare immediatamente all'affidabilità complessiva inferiore mentre sono stati limitati.

## <a name="next-steps"></a>Passaggi successivi
- Per scoprire le modalità di gestione risorse Cluster gestisce e bilancia carico del cluster, vedere l'articolo su [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)
- Gestione risorse Cluster sono presenti molte opzioni per la descrizione del cluster. Per informazioni sui estrarre in questo articolo sulla [Descrizione un cluster di tessuti di servizio](service-fabric-cluster-resource-manager-cluster-description.md)
