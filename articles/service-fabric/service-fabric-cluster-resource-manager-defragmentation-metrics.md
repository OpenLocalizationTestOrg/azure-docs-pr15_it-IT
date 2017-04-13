<properties
   pageTitle="Deframmentazione di metriche di Azure servizio tessuti | Microsoft Azure"
   description="Cenni preliminari usando deframmentazione o imballaggio come una strategia di metriche di tessuti di servizio"
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

# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Deframmentazione di metriche e carico nel servizio tessuti
Gestione risorse Cluster tessuti servizio principalmente riguarda bilanciamento del carico in termini di distribuzione del carico, assicurandosi che tutti i nodi del cluster vengono utilizzati in modo uniforme. Si tratta in genere il layout più sicuro e smartest in termini di sopravvivenza errori poiché garantisce che qualsiasi errore specificato non estrarre l'alcune gran parte di un determinato carico di lavoro. Gestione risorse Cluster tessuti servizio supporta una strategia diversa, ovvero deframmentazione. Deframmentazione indica in genere che invece si tenta di distribuire l'utilizzo di una metrica nel cluster, si bisogna effettivamente tenta di consolidamento in. Si tratta di una fortuna inversione strategia normale – invece di ottimizzazione cluster in base a ridurre la deviazione standard media del carico metrica per una data metrica, iniziamo ottimizzazione per aumenti della deviazione. Ma il motivo per cui si desidera questa strategia?

Se è stato distribuito il carico equamente tra i nodi del cluster è stata contrario su alcune delle risorse contenenti i nodi per l'offerta. In genere si tratta di un problema, ma a volte alcuni carichi di lavoro di creare servizi che sono estremamente grande e utilizzano la maggior parte di un nodo, ad esempio il 75% a 95% di risorse del nodo farebbe dedicato per un'istanza di servizio single o replica. Questo non è un problema, la gestione di risorse Cluster verrà rilevato al momento della creazione del servizio che è necessario riorganizzare cluster per creare spazio per il carico di lavoro di grandi dimensioni e impostare sull'implementazione di tali, ma nel frattempo il carico di lavoro è necessario aspettare di essere pianificate in cluster.

Dato che la programmazione di nuovi carichi di lavoro è in genere almeno un po' latenza riservata, se non eseguire alcuna diverso è possibile talvolta destra soffiare via per i contratti di servizio caso esistano numerosi servizi e stato per spostarsi all'interno, in particolare se sono in genere grandi carichi di lavoro del cluster (e pertanto richiede più tempo per spostarsi all'interno del cluster). In effetti, quando si misurati tempi di creazione delle simulazioni in base a dati reali cluster, visto che se services sono state sufficientemente elevato e il cluster è stato utilizzato piuttosto che la creazione di tali servizi grandi da risultare rallentata e che migliorare questo introducendo i criteri di metriche deframmentazione.

Come file creazione o access potrebbe ottenere rallentavano che se disco rigido del computer è stato frammentato e potrebbe essere possibile rendere più veloce deframmentare l'unità in modo che erano disponibili più grandi blocchi contigui per, è possibile configurare metriche deframmentazione per la gestione risorse Cluster di ridurre il carico dei servizi in nodi di un numero minore in modo che è (quasi) sempre room per i servizi di grandi dimensioni pari , che consente di creare rapidamente. Maggior parte delle persone non sarà necessario, poiché servizi dovrebbero essere piccole e pertanto non è difficile da individuare spazio per poterli, ma se si dispongono di servizi di grandi dimensioni e servono creati rapidamente e si è disposti ad accettare compromessi, ad esempio impactfulness maggiore di errori e alcune risorse da sinistra unutilized in attesa per carichi di lavoro per la pianificazione, la strategia di deframmentazione sia adatta.

La figura seguente offre una rappresentazione visiva dei due gruppi diversi, uno che viene deframmentato e uno che non è. Nel caso non bilanciato, prendere in considerazione i movimenti che sarebbero necessari per posizione uno degli oggetti di servizio più grande, se un'altra sono state create, rispetto al cluster deframmentato, in cui potrebbe posizionato immediatamente sulla nodi 4 e 5.

![Confronto di bilanciamento del carico e deframmentato cluster][Image1]

## <a name="defragmentation-pros-and-cons"></a>Deframmentazione Pro e contro
Quali sono le altri compromessi concettuali? È consigliabile approfondita misura i carichi di lavoro prima di attivare metriche deframmentazione. Ecco una rapida tabella aspetti da considerare:

| Professionisti IT deframmentazione  | Deframmentazione contro |
|----------------------|----------------------|
|Consente la creazione di più veloce di servizi di grandi dimensioni | Concentrati caricare nodi meno, aumentando il conflitto
|Consente di ridurre lo spostamento dei dati durante la creazione    | Errori possono rallentare altri servizi e causare ulteriori varianza
|Consente di RTF descrizione dei requisiti e il recupero di spazio | Configurazione di gestione delle risorse globale più complessa

È possibile combinare Deframmentate e normale metriche nello stesso cluster e Gestione risorse produrrà è consigliato per assicurarsi di disporre di un layout che consolida la maggior parte dei criteri di misurazione deframmentazione come possibile mentre si tenta di allontanare le altre. I risultati che vengono fornite dipenderà il numero di bilanciamento del carico metrica rispetto al numero di metriche deframmentazione e spessori, carichi correnti e così via.

## <a name="configuring-defragmentation-metrics"></a>Configurazione metriche deframmentazione
Configurazione metriche deframmentazione è una decisione globale del cluster e metriche singoli possono essere selezionate per la deframmentazione:

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

## <a name="next-steps"></a>Passaggi successivi
- Gestione risorse Cluster sono presenti molte opzioni per la descrizione del cluster. Per informazioni sui estrarre in questo articolo sulla [Descrizione un cluster di tessuti di servizio](service-fabric-cluster-resource-manager-cluster-description.md)
- Sono come il gestore di risorse del servizio Cluster tessuti gestione è affidata a consumo e capacità del cluster. Per ulteriori informazioni sulle loro e come configurarli estrarre [in questo articolo](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
