<properties
   pageTitle="Gestione risorse Cluster di servizio tessuti - gruppi di applicazioni | Microsoft Azure"
   description="Panoramica delle funzionalità di gruppo di applicazioni in Gestione risorse servizio tessuti Cluster"
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

# <a name="introduction-to-application-groups"></a>Introduzione ai gruppi di applicazioni
Manager delle risorse Cluster dell'infrastruttura di servizio in genere gestisce le risorse cluster diffusione il carico (rappresentato tramite metriche) in modo uniforme per tutto il cluster. Servizio tessuti gestisce anche la capacità dei nodi in cluster e il cluster nel suo insieme tramite il concetto di capacità. Questa funzionalità è molto utile per molti dei diversi tipi di carichi di lavoro, ma modelli che utilizzano modo intensivo diverse istanze del servizio tessuti applicazione talvolta anticipare requisiti aggiuntivi. Alcuni requisiti aggiuntivi sono in genere:

- Possibilità di prenotare la capacità per i servizi di un'istanza applicazione in un certo numero dei nodi
- Possibilità di limitare il numero totale di nodi che può essere eseguito un determinato insieme di servizi all'interno di un'applicazione
- La definizione di capacità sulla stessa istanza di applicazione per limitare il numero o il consumo di risorse totale dei servizi all'interno

Per questi requisiti, abbiamo sviluppato il supporto per la cosiddetta gruppi di applicazioni.

## <a name="managing-application-capacity"></a>Gestione delle capacità dell'applicazione
Per limitare il numero dei nodi occupate da un'applicazione, nonché il carico totale metrico di che le istanze delle applicazioni nei singoli nodi, è possibile utilizzare la capacità dell'applicazione. Può inoltre essere utilizzato per prenotare le risorse cluster per l'applicazione.

Applicazione capacità può essere impostata per le nuove applicazioni quando sono state create. può essere aggiornato anche per le applicazioni esistenti che sono state create senza specificare la capacità dell'applicazione.

### <a name="limiting-the-maximum-number-of-nodes"></a>Limitare il numero massimo di nodi
Il caso di utilizzo più semplice per la capacità di applicazione è quando un'istanza di applicazione deve essere limitata per un determinato numero massimo di nodi. Se non viene specificato alcun capacità di applicazione, la gestione di risorse Cluster tessuti servizio creerà repliche in base alle regole normale (bilanciamento del carico o deframmentazione), che indica in genere che i servizi verranno distribuiti in tutti i nodi disponibili nel cluster, o se deframmentazione è attivata un certo numero più piccolo ma non autorizzato dei nodi.

Nella figura seguente viene illustrata la posizione potenziale di un'istanza di applicazione senza il numero massimo di nodi definiti e imposta quindi stessa applicazione con un numero massimo di nodi. Si noti che non garanzie apportate sulle quali repliche o le istanze dei quali servizi verranno visualizzato inseriti insieme.

![Definizione di numero massimo di nodi dell'istanza dell'applicazione][Image1]

Nell'esempio sinistro dell'applicazione non è disponibile la capacità dell'applicazione impostare e ha tre servizi. CRM ha digitato la decisione logica di distribuiti ogni replica su sei nodi disponibili per ottenere un equilibrio ottimale nel cluster. Nell'esempio destra, è possibile vedere della stessa applicazione che è vincolato in tre nodi e in cui servizio tessuti CRM ha raggiunto il saldo migliore per la replica di servizi dell'applicazione.

Il parametro che consente di controllare questo comportamento è denominato MaximumNodes. Questo parametro può essere impostato durante la creazione dell'applicazione o aggiornato per un'istanza di applicazione che era già in esecuzione, in cui maiuscole/minuscole servizio tessuti CRM impostano le repliche dei servizi dell'applicazione per il numero massimo definito dei nodi.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;

adUpdate.Metrics.Add(appMetric);
```

## <a name="application-metrics-load-and-capacity"></a>Capacità, carico e metriche di applicazione
Gruppi di applicazioni consentono inoltre di definire metriche associate a un'istanza di applicazione specificato, nonché la capacità dell'applicazione per quanto riguarda i dati. Ad esempio è possibile definire che come molti servizi che si vuole potrebbero essere creati in

Per ogni unità di misura metriche, esistono 2 valori che è possibile impostare per descrivere la capacità per quell ' istanza dell'applicazione:

-   Totale applicazione capacità: rappresenta la capacità totale dell'applicazione di una particolare metrica. Servizio tessuti CRM tenterà di limitare la somma di metriche carichi di servizi dell'applicazione per il valore specificato. Inoltre, se i servizi dell'applicazione già utilizza carico fino a questo limite, Gestione risorse di servizio tessuti Cluster impedirà la creazione di nuovi servizi o partizioni causando carico totale per l'invio tramite questo limite.
-   Massima capacità del nodo-specifica il carico totale massimo per replica di servizi le applicazioni di un singolo nodo. Se il carico totale sul nodo supera la capacità, servizio tessuti CRM tenterà di spostare repliche altri nodi in modo che sia rispettato il vincolo di capacità.

## <a name="reserving-capacity"></a>Prenotazione capacità
Un altro comuni gruppi di applicazioni utilizzato per garantire che le risorse all'interno del cluster sono riservate per un'istanza di applicazione specificato, anche se l'istanza dell'applicazione non ha ancora servizi in esso contenuti o anche se si non utilizzano ancora le risorse. Diamo un'occhiata come funzionano.  

### <a name="specifying-a-minimum-number-of-nodes-and-resource-reservation"></a>Specificare un numero minimo di nodi e prenotazione risorse
La prenotazione di risorse per un'istanza di applicazione, è necessario specificare un paio ulteriori parametri: *MinimumNodes* e *NodeReservationCapacity*

- MinimumNodes - specificare il numero massimo di nodi quali è possono eseguire i servizi all'interno di un'applicazione, è possibile anche specificare il numero minimo di nodi che deve essere eseguita un'applicazione in. Questa impostazione in modo efficace definisce il numero dei nodi che le risorse devono essere riservate nel minimo, per garantire la capacità all'interno del cluster come parte della creazione dell'istanza dell'applicazione.
- NodeReservationCapacity - il NodeReservationCapacity può essere definita per ogni unità di misura metriche all'interno dell'applicazione. Definisce la quantità di carico metrica riservato per l'applicazione in uno dei nodi in cui vengono inserite ogni replica o le istanze dei servizi inclusi.

Esaminiamo un esempio di prenotazione capacità:

![Istanze dell'applicazione che definisce la capacità riservata][Image2]

Nell'esempio a sinistra, le applicazioni non dispone di qualsiasi capacità di applicazione definito. Manager delle risorse di servizio tessuti Cluster viene disposto repliche figlio servizi dell'applicazione e le varianti con quelli da altri servizi (all'esterno dell'applicazione) per garantire un equilibrio del cluster.

Nell'esempio a destra, si supponga che l'applicazione è stato creato con una MinimumNodes impostata su 2, MaximumNodes impostato su 3 e un'applicazione di unità di misura metriche definiti con una prenotazione della capacità di massimo 20, su un nodo di 50 e una capacità di applicazione totale di 100, verrà prenotare la capacità su due nodi per l'applicazione blu tessuti servizio e non consentirà sé cluster di utilizzare tale capacità. Questa capacità di applicazione riservate verrà considerata consumata e calcolato e la capacità di cluster rimanente.

Creazione di un'applicazione con la caratteristica di prenotazione, la gestione di risorse Cluster verrà prenotare la capacità uguale a MinimumNodes * NodeReservationCapacity cluster, ma non verranno prenotare capacità su nodi specifici fino a quando non vengono create e posizionate repliche dei servizi dell'applicazione. In questo modo per flessibilità, dal momento che i nodi vengono scelti per nuova replica solo quando vengono creati. Capacità riservata su un nodo specifico quando almeno una replica viene posizionata su di esso.

## <a name="obtaining-the-application-load-information"></a>Ottenere le informazioni sul caricamento di applicazione
Per ogni applicazione che ha capacità di applicazione definito è possibile ottenere le informazioni relative a carico aggregato svolto repliche dei relativi servizi. Servizio tessuti fornisce le query di PowerShell e API gestita a tale scopo.

Ad esempio carico può essere recuperata mediante il seguente cmdlet di PowerShell:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

L'output della query conterrà le informazioni di base sulla capacità di applicazione che è stata specificata per l'applicazione, ad esempio nodi minimo e massimo. Anche saranno informazioni sul numero dei nodi che l'applicazione è attualmente in uso. In questo modo, per ogni unità di misura metriche carico esistere informazioni su:
- : Metrica nome della metrica.
-   Prenotazione capacità: La capacità Cluster riservata cluster dell'applicazione.
-   Caricamento di applicazione: Carico totale di repliche figlio dell'applicazione.
-   Applicazione capacità: Massima consentita valore carico dell'applicazione.

## <a name="removing-application-capacity"></a>Rimozione di capacità dell'applicazione
Dopo aver impostati i parametri di capacità di applicazione per un'applicazione, può essere rimossa uso dei cmdlet di aggiornamento dell'applicazione API o PowerShell. Per esempio:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Questo comando verrà rimossi tutti i parametri di applicazione capacità dall'applicazione e Gestione risorse di servizio tessuti Cluster inizierà trattare qualsiasi altra applicazione del cluster che non dispone di questi parametri definiti questa applicazione. L'effetto del comando è immediato e gestione di risorse Cluster si elimineranno tutti i parametri di capacità di applicazione per l'applicazione. specificarli nuovamente richiede l'API di applicazione di aggiornamento da chiamare con i parametri appropriati.

## <a name="restrictions-on-application-capacity"></a>Restrizioni sulla capacità dell'applicazione
Esistono diverse restrizioni sui parametri capacità di applicazione che devono essere rispettate. In caso di errori di convalida, la creazione o aggiornamento dell'applicazione verificherà un errore.
Tutti i parametri integer devono essere numeri non negativo.
Inoltre, per singoli parametri restrizioni sono i seguenti:

-   MinimumNodes mai deve essere maggiore di MaximumNodes.
-   Se sono definite capacità per una metrica carico, è necessario seguire queste regole:
  - Nodo prenotazione capacità non deve essere maggiore del numero massimo nodo capacità. Ad esempio, non è possibile limitare la capacità per unità di misura metriche "CPU" sul nodo per 2 unità e provare a prenotare 3 unità su ciascun nodo.
  - Se si specifica MaximumNodes, il prodotto dei MaximumNodes e massima della capacità di nodo non deve essere maggiore di capacità totale dell'applicazione. Se si imposta la capacità di nodo massima per unità di misura metriche carico "CPU" a 8 e si imposta il numero massimo di nodi a 10, ad esempio capacità applicazione totale deve essere maggiore di 80 per la metrica di caricamento.

Le restrizioni vengono applicate durante la creazione dell'applicazione (sul lato client) e durante l'aggiornamento dell'applicazione (sul lato server). Durante la creazione, questo è un esempio di una violazione Cancella dei requisiti dal MaximumNodes < MinimumNodes e il comando avrà esito negativo nel client prima anche inviata la richiesta di cluster tessuti servizio:

``` posh
New-ServiceFabricApplication –Name fabric:/MyApplication1 –MinimumNodes 6 –MaximumNodes 2
```

Esempio di aggiornamento non valido è come indicato di seguito. Se è scrivere un'applicazione esistente e aggiornare numero massimo di nodi su un valore, si passerà l'aggiornamento:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MaximumNodes 2
```

In seguito, è possibile tenta di aggiornare nodi minimi:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MinimumNodes 6
```

Il client non ha sufficiente contesto relativo all'applicazione in modo che consente l'aggiornamento passare al cluster servizio tessuti. Tuttavia, il cluster tessuti servizio verrà convalidato il nuovo parametro insieme i parametri esistenti e avrà esito negativo dell'operazione di aggiornamento poiché i nodi minimo applicatela valore è maggiore del valore per numero massimo di nodi. In questo caso, i parametri di applicazione capacità rimarrà invariati.

Queste limitazioni sono posizionate in posizione nell'ordine affinché Gestione risorse Cluster di essere in grado di fornire la posizione ottimale per replica di servizi delle applicazioni.

## <a name="how-not-to-use-application-capacity"></a>Come evitare di utilizzare la capacità di applicazione

-   Non utilizzare la capacità di applicazione per vincolare un'applicazione di un sottoinsieme specifico dei nodi: sebbene tessuti servizio garantisce che sia rispettato numero massimo di nodi per ogni applicazione che ha capacità di applicazione specificata, gli utenti non è possibile decidere quali nodi verrà creata in. Ciò può essere ottenuto utilizzando vincoli di posizionamento per i servizi.
-   Non utilizzare la capacità di applicazione per assicurarsi che verranno sempre inseriti due servizi della stessa applicazione insieme a tra loro. Questo può essere ottenuto utilizzando relazione affinità tra i servizi e affinità può essere limitata solo ai servizi che in realtà devono essere posizionati tra loro.

## <a name="next-steps"></a>Passaggi successivi
- Per ulteriori informazioni sulle altre opzioni disponibili per la configurazione dei servizi estrazione obbligatoria l'argomento in altre configurazioni Manager delle risorse Cluster disponibili [informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)
- Per scoprire le modalità di gestione risorse Cluster gestisce e bilancia carico del cluster, vedere l'articolo su [bilanciamento del carico](service-fabric-cluster-resource-manager-balancing.md)
- Iniziare dalla parte iniziale e [per un'introduzione a Gestione risorse servizio tessuti Cluster](service-fabric-cluster-resource-manager-introduction.md)
- Per ulteriori informazioni sul funzionano in genere metriche, documentarsi [Servizio tessuti carico metriche](service-fabric-cluster-resource-manager-metrics.md)
- Gestione risorse Cluster sono presenti molte opzioni per la descrizione del cluster. Per informazioni sui estrarre in questo articolo sulla [Descrizione un cluster di tessuti di servizio](service-fabric-cluster-resource-manager-cluster-description.md)


[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
