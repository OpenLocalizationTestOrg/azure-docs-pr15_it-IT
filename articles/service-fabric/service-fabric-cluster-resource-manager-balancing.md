<properties
   pageTitle="Bilanciamento del carico il Cluster con Gestione risorse Azure servizio tessuti Cluster | Microsoft Azure"
   description="Introduzione a bilanciamento del carico il cluster il servizio tessuti Cluster Gestione risorse."
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

# <a name="balancing-your-service-fabric-cluster"></a>Bilanciamento del carico il cluster tessuti servizio
Gestione risorse Cluster tessuti servizio consente reporting carico dinamico, reagendo con le modifiche apportate nel cluster, per la correzione del violazioni dei vincoli e riequilibrare cluster se necessario. Ma con quale frequenza esegue questi aspetti e cosa attiva? Sono disponibili alcuni controlli correlati all'oggetto.

Il primo set di controlli all'interno di bilanciamento del carico sono un insieme di timer. Questi timer determinano con quale frequenza Gestione risorse Cluster esamina lo stato del cluster per le operazioni che dovranno essere risolti. Esistono tre diverse categorie di lavoro, ognuna con le proprie timer corrispondente. Sono:

1.  Posizionamento: questa fase occupa inserire le informazioni sullo stato repliche o istanze senza informazioni sullo stato mancano. È descritta nuovi servizi e la gestione di informazioni sullo stato repliche o istanze senza informazioni sullo stato che non hanno superato ed essere necessario ricrearle. Eliminazione e il rilascio repliche o istanze anche gestiti qui.
2.  Vincolo controlla: questa fase verifica e corregge violazioni dei vincoli di posizionamento diverse (regole) all'interno del sistema. Esempi di regole sono operazioni come garantire che i nodi non sono sulla capacità e che vengano soddisfatti i vincoli di posizionamento del servizio (ulteriori informazioni su queste versioni successive).
3.  Bilanciamento del carico: questa fase viene verificato se riequilibrare preventivo è necessaria in base al livello desiderato configurato del saldo per diverse metriche e in caso affermativo tenta di individuare una disposizione del cluster che più bilanciato.

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>Configurazione di passaggi di gestione delle risorse Cluster e timer
Ognuno dei diversi tipi di correzioni inserirvi Gestione risorse Cluster verrà controllata da un altro timer che determina la frequenza. Ad esempio, se si vuole solo di occuparsi di immissione nuova carichi di lavoro di servizio del cluster ogni ora (in batch in alto), ma si desidera regolare bilanciamento controlla ogni pochi secondi, è possibile configurare il comportamento. Quando viene generato ogni timer, l'attività viene programmata. Per impostazione predefinita Manager delle risorse analizza il relativo stato e applica gli aggiornamenti (batch tutte le modifiche apportate dall'ultimo analisi, ad esempio osservando che un nodo verso il basso) ogni 1/10 di un secondo set la posizione e vincolo selezionare contrassegni ogni secondo e il bilanciamento del carico contrassegnare ogni 5 secondi.

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Oggi è solo eseguire una di queste azioni alla volta, in sequenza (che rappresenta il motivo per cui si fa riferimento a queste configurazioni come "intervalli minimi")). Si tratta in modo che, ad esempio abbiamo già risposto a tutte le richieste in sospeso per creare nuove repliche prima di procedere per il cluster di bilanciamento. Come è possibile visualizzare gli intervalli di tempo predefinito specificati, è possibile esaminare e controllo per tutte le operazioni occorre eseguire molto spesso, il che significa che l'insieme di modifiche vengano apportate alla fine di ogni passaggio è in genere più piccolo: non è stiamo esaminare ore delle modifiche apportate in cluster e si tenta di correggere tutti contemporaneamente, si sta tentando di gestire operazioni più o meno, in tempo reale, ma con alcuni batch quando vengono eseguite numerose operazioni nel contemporaneamente. In questo modo la risorsa tessuti Service manager molto più efficiente agli elementi che si verificano nel cluster.

Durante la maggior parte di queste attività sono semplici (se sono presenti violazioni dei vincoli, correzione, se sono presenti servizi da creare, crearle), la gestione di risorse Cluster richiede anche alcune informazioni aggiuntive per determinare se il cluster bilanciato. Per cui è possibile utilizzare due altri elementi di configurazione: *Soglie di bilanciamento del carico* e *Le soglie di attività*.

## <a name="balancing-thresholds"></a>Bilanciamento del carico soglie
Soglia di bilanciamento del carico è il controllo principale per l'attivazione riequilibrare preventivo (ricordare che il timer è solo per con quale frequenza devono controllare la gestione di risorse Cluster, non significa che verrà eseguita alcuna). La soglia di bilanciamento del carico consente di definire come bilanciato cluster deve essere per una specifica metrica in ordine per Gestione risorse Cluster da prendere in considerazione viene bilanciato e bilanciamento del carico trigger.

Soglie di bilanciamento del carico sono definite alla scala cronologica per metrica come parte della definizione di cluster. Per ulteriori informazioni sull'estrazione metriche [in questo articolo](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

La soglia di bilanciamento del carico per una metrica è il rapporto. Se la quantità di carico sul nodo più caricato diviso per la quantità di carico sul nodo minimo caricato supera questo numero, quindi il cluster risulta bilanciato e bilanciamento del carico verrà attivata la volta successiva che il gestore di risorse Cluster verifica (per impostazione predefinita, mai 5 secondi, come gestito da MinLoadBalancingInterval, mostrata sopra).

![Esempio di bilanciamento del carico soglia][Image1]

In questo esempio semplice ogni servizio utilizza un'unità di alcune unità di misura metriche. Nell'esempio superiore è "5" carico massimo in un nodo e il valore minimo è 2. Supponiamo che la soglia di bilanciamento del carico per la metrica è 3. Di conseguenza, nell'esempio superiore, il cluster risulta bilanciate e non bilanciamento del carico verrà attivata quando il gestore di risorse Cluster controlla (poiché il rapporto del cluster è 5/2 = 2,5 e che è minore di soglia di bilanciamento del carico specificata di 3).

Nell'esempio inferiore il carico massimo in un nodo è 10, mentre il valore minimo è 2, risultante in un rapporto di 5. In questo modo il cluster superato la soglia di bilanciamento del carico progettata pari a 3 per tale unità di misura metriche. Di conseguenza, un'esecuzione rebalancing globale sarà programmata nuovamente il bilanciamento del timer viene attivato. Si noti che il fatto che viene persa una ricerca di bilanciamento del carico non sono significativo sposterà: in alcuni casi il cluster è bilanciato ma non è possibile migliorare la situazione - ma in situazioni come questa occorrenza (almeno per impostazione predefinita) alcuni il carico verrà distribuito quasi sicuramente nodo 3. Si noti che poiché non si sta utilizzando un approccio generici alcune carico può essere inoltre distribuito per il nodo 2 poiché che comporterebbe la riduzione delle differenze tra i nodi complessive, ma si aspetta che la maggior parte del carico da flusso per nodo 3.

![Bilanciamento del carico soglia esempio azioni][Image2]

Nota che visualizzato sotto la soglia di bilanciamento del carico non sia un obiettivo esplicito – soglie di bilanciamento del carico sono solo un *trigger* che indica la gestione di risorse Cluster che dovrebbe essere nel cluster per determinare quali miglioramenti possibile apportare, se presenti.

## <a name="activity-thresholds"></a>Soglie di attività
In alcuni casi, anche se i nodi sono relativamente bilanciati, la quantità *totale* di caricamento del cluster è bassa. Può trattarsi semplicemente a causa l'ora del giorno o perché il cluster nuove e solo recupero avviato automaticamente. In entrambi i casi, è consigliabile non dedicare troppo tempo a bilanciamento del carico cluster poiché c'è molto ridotta che si otterrebbe, si saranno sufficiente spesa rete ed elaborazione risorse per spostare i diversi elementi, senza apportare alcuna differenza assoluto. Perché si desidera evitare questa operazione, non esiste un altro controllo all'interno di gestione risorse, noto come soglie di attività, che consente di specificare un limite inferiore assoluto per l'attività: se nessun nodo ha almeno carico eccessivo quindi bilanciamento del carico non verrà attivata anche se viene raggiunta la soglia bilanciamento del carico.

Ad esempio si supponga che sono disponibili i report con i totali seguenti per consumo su questi nodi. Si supponga inoltre che si mantengono la soglia di bilanciamento del carico pari a 3 per questa metrica, ma ora abbiamo anche un limite di attività di 1536. Nel caso prima, mentre il cluster è bilanciato per la soglia di bilanciamento del carico alcun nodo non soddisfa tale soglia minimo di attività, pertanto abbiamo Esci da soli elementi. Nell'esempio inferiore nodo 1 è superato la soglia di attività, in modo che verrà eseguita bilanciamento del carico (poiché sono superato il limite di bilanciamento del carico e la soglia di attività per l'unità di misura metriche)

![Esempio di soglia attività][Image3]

Come soglie di bilanciamento del carico soglie attività sono definiti per metrica tramite la definizione di cluster:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

Si noti che le soglie di bilanciamento del carico e attività sono entrambe collegati alla metrica - bilanciamento del carico deve essere attivati solo se vengono superate soglie di bilanciamento del carico e attività per la stessa metrica. In questo modo, se si supera la soglia di bilanciamento del carico per la memoria e la soglia di attività per la CPU, bilanciamento del carico non attiverà, purché non vengano superate le soglie rimanenti (soglia bilanciamento del carico per la CPU) e soglia di attività per la memoria.

## <a name="balancing-services-together"></a>Bilanciamento del carico di servizi
È interessante notare è che se il cluster è bilanciato o non è una decisione tutto, ma il modo in cui è passare sulle ripristinarlo spostamento repliche singolo servizio e le istanze intorno a. Senso, giusto? Se barre in pila memoria in un nodo, più repliche o possono contribuire istanze, pertanto può essere necessario spostare le informazioni sullo stato repliche o istanze senza informazioni sullo stato che utilizzano l'unità di misura metriche interessato, bilanciato.

In alcuni casi anche se un cliente eseguiranno una chiamata Contattaci backup o un file se si ha spostato un ticket che indica che un servizio che non è stato bilanciato. Come può accadere che un servizio ottiene spostato anche se sono stati bilanciati tutti i parametri del servizio, anche perfettamente in tal caso, al momento di altro squilibrio? Vediamo!

Richiedere, ad esempio quattro servizi Service1, Service2, Service3 e Service4. Service1 report contro metriche Metric1 e Metric2, Service2 rispetto a Metric2 e Mmetric3 Service3 rispetto a Metric3 e Metric4 e Service4 contro alcuni Metric99 metriche. Sicuramente è possibile vedere nel punto in cui verranno qui. Esiste una catena! Dal punto di vista della gestione delle risorse Cluster, non si hanno quattro servizi indipendenti, si dispone di un insieme di servizi correlati (Service1, Service2 e Service3) e quello che è disattivata nella propria.

![Bilanciamento del carico di servizi][Image4]

È possibile che può causare un squilibrio Metric1 repliche o istanze che appartengono a Service3 per spostarsi all'interno. In genere questi movimenti sono molto limitati, ma può essere maggiore a seconda esattamente come bilanciato Metric1 ottenuto e modifiche sono state necessarie del cluster per correggere l'errore. È anche possibile affermare con certezza che squilibrio nelle metriche 1, 2 o 3 non causerà mai i movimenti del Service4, non vi sarà nessun punto dopo lo spostamento repliche o istanze che appartengono a Service4 intorno può eseguire assolutamente alcuna operazione per gli effetti in termini il saldo di metriche 1, 2 o 3.

Gestione risorse Cluster le cifre automaticamente le quali servizi sono correlati, poiché servizi sono state aggiunte, rimosso, o stato loro modificato configurazione metriche, ad esempio, tra le due esecuzioni di bilanciamento del carico Service2 potrebbero avere stati modificati per rimuovere Metric2. Catena tra Service1 e Service2 interrotto. A questo punto anziché due gruppi di servizi, sono disponibili tre:

![Bilanciamento del carico di servizi][Image5]

## <a name="next-steps"></a>Passaggi successivi
- Sono come il gestore di risorse del servizio Cluster tessuti gestione è affidata a consumo e capacità del cluster. Per ulteriori informazioni sulle loro e come configurarli estrarre [in questo articolo](service-fabric-cluster-resource-manager-metrics.md)
- Spostamento dei costi sono un modo per la segnalazione di Manager delle risorse Cluster che determinati servizi siano più costosi spostare rispetto ad altri. Per ulteriori informazioni sui costi di spostamento, fare riferimento al [presente articolo](service-fabric-cluster-resource-manager-movement-cost.md)
- Gestione risorse Cluster sono diverse limita che è possibile configurare per rallentare la varianza del cluster. Non è in genere necessarie, ma se sono necessarie informazioni relative ad esse [qui](service-fabric-cluster-resource-manager-advanced-throttling.md)


[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
