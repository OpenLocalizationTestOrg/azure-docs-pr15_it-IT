<properties
   pageTitle="Servizio di gestione di risorse Cluster tessuti: spostamento dei costi | Microsoft Azure"
   description="Panoramica dei costi di spostamento per i servizi di infrastruttura di servizio"
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

# <a name="service-movement-cost-for-influencing-cluster-resource-manager-choices"></a>Costo di spostamento di servizio per modificare opzioni di gestione delle risorse Cluster
Fattori importanti da considerare quando si sta tentando di determinare quali modifiche da apportare a un cluster e il punteggio di una soluzione è il costo totale di ottenere tale soluzione.

Spostamento di servizio istanze o repliche costi CPU ora e rete della larghezza di banda minimo. Per i servizi di informazioni sullo stato, costa anche la quantità di spazio su disco che è necessario creare una copia dello stato prima di arrestare repliche precedenti. Chiaramente desiderata per ridurre al minimo il costo di qualsiasi soluzione di gestione delle risorse Cluster di Azure servizio tessuti insorge con. Ma anche non si vuole ignorare soluzioni notevolmente migliore per ottimizzare l'allocazione di risorse del cluster.

Manager delle risorse cluster sono due modi per il calcolo dei costi e servirsi anche quando si tenta di gestire il cluster in base alle altre obiettivi. Il primo è quando Manager delle risorse Cluster prevede un nuovo layout per il cluster, viene contato ogni spostamento da verificare. In caso di semplice, se si riceve due soluzioni con analoghe saldo totale complessivo (punteggio) alla fine, quindi scattare quella con il costo più basso (numero totale di spostamenti).

Questo funziona bene. Ma con predefinito o carichi statici, non viene in genere in qualsiasi sistema complessa che consente di spostare tutti sono uguali. Alcuni sono potrebbero essere molto più costosa.

## <a name="changing-a-replicas-move-cost-and-factors-to-consider"></a>Modifica della replica Sposta costo e fattori da considerare
Come con reporting carico (un'altra funzionalità di gestione di risorse di Cluster), assegnare il servizio di un metodo di apprendimento reporting come costosa il servizio consiste nello spostare in un determinato momento.

Codice:

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

MoveCost contiene quattro livelli: Zero, bassa, Media e alta. Si tratta di uno a altro, ad eccezione di Zero. Zero significa che lo spostamento di una replica è gratuita e non deve conteggiati rispetto al punteggio della soluzione. Impostare il costo di spostamento su Alta *non* garantisce che la replica non è possibile spostare, semplicemente che non è possibile spostare a meno che non esiste un buon motivo per.

![Spostare costo come un fattore per la scelta repliche per lo spostamento][Image1]

MoveCost consente di trovare le soluzioni che causano l'interruzione minima del complessivo e sono più semplice ottenere durante comunque essere recapitata in saldo equivalente. Concetto di un servizio di costo può essere relativo a molti aspetti. Di seguito sono riportati i fattori più comuni per il calcolo del costo di spostamento:

- La quantità di dati che contiene il servizio per spostare o stato.
- Il costo di disconnessione dei client. Il costo di spostare una replica principale è in genere maggiore il costo di spostare una replica secondaria.
- Il costo di interrompere un'operazione in corso. Livello di memorizzare alcune operazioni i dati o operazioni eseguite in risposta a una chiamata client sono costose. Dopo un certo punto, non si vuole interromperle se non si dispone. Pertanto per la durata dell'operazione, aumentarne il costo per ridurre la probabilità che consente di spostare la replica di servizi o istanza. Al termine dell'operazione, è inserirlo tornare alla visualizzazione normale.

## <a name="next-steps"></a>Passaggi successivi
- Gestore di risorse del servizio tessuti Cluster utilizza metriche per gestire il consumo e capacità del cluster. Per ulteriori informazioni sulle metriche e su come configurarli, estrarre [il consumo di gestione delle risorse e carico nel servizio tessuti con metriche](service-fabric-cluster-resource-manager-metrics.md).
- Per informazioni su come la gestione di risorse Cluster gestisce e bilancia carico del cluster, consultare [il cluster tessuti servizio di bilanciamento](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
