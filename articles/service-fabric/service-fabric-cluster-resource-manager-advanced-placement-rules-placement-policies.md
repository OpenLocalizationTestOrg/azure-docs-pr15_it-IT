<properties
   pageTitle="Gestione risorse Cluster di servizio tessuti - dei criteri di posizionamento | Microsoft Azure"
   description="Panoramica dei criteri di posizionamento aggiuntivo e regole per i servizi di infrastruttura servizio"
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

# <a name="placement-policies-for-service-fabric-services"></a>Criteri di posizionamento per i servizi di infrastruttura servizio
Sono disponibili molti diversi ulteriori regole che potrebbe essere necessario caring sulle se il cluster tessuti servizio è occupato geografica di distanza, ad esempio più Data Center o le aree di Azure, o se l'ambiente si estende su più aree del controllo geopolitiche (o alcuni casi in cui si dispone di limiti legali o criteri che interessano o la distanza correlate hanno un impatto prestazioni/latenza effettivo). La maggior parte delle operazioni è possibile configurare le proprietà di nodo e i vincoli di posizionamento, ma alcune sono più complessi. Per rendere più semplice operazioni fornite queste commmands aggiuntive. Proprio come con altri vincoli di posizionamento, posizionamento possono essere configurati per una singola istanza di servizio per denominato.

## <a name="specifying-invalid-domains"></a>Specificare domini non validi
Criteri di posizionamento InvalidDomain consentono di specificare che un determinato dominio guasto è valido per il carico di lavoro. Questo criterio assicura che un determinato servizio non viene mai eseguito in un'area specifica, ad esempio per motivi di criteri geopolitici o aziendale. È possibile specificare più domini non validi tramite criteri separati.

![Esempio di dominio non valido][Image1]

Codice:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Specificare domini necessari
Criteri di posizionamento dominio richiesto richiedono che tutte le informazioni sullo stato repliche o istanze di senza informazioni sullo stato del servizio per il servizio sia presente nel dominio specificato. È possibile specificare più domini necessari tramite criteri separati.

![Esempio di dominio necessari][Image2]

Codice:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas"></a>Specificare un dominio preferito per replica principale
Il dominio primario preferito è un controllo interessante, in quanto consente di selezione del dominio di errore in cui deve essere inserito primario se è possibile farlo. Dopo aver verificato la integro primario finirà in questo dominio. Se il dominio o fail replica primaria o arrestare per qualche motivo che primario verrà migrato in un'altra posizione. Se questo percorso non è nel dominio di preferito, quindi laddove possibile la gestione di risorse Cluster verrà spostarlo di nuovo al dominio preferito. Naturalmente questa impostazione senso solo per i servizi di informazioni sullo stato. Questo criterio è particolarmente utile nelle cluster estese su Azure aree geografiche o più Data Center. In questi casi si usa tutti i percorsi per la ridondanza ma si desidera inserire repliche primarie in una posizione specifica per fornire latenza inferiore per le operazioni che passare all'oggetto primario (scrive e anche per impostazione predefinita tutte le operazioni lettura vengono rappresentate in primario).

![Failover e domini primari Preferiti][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replicas-to-be-distributed-among-all-domains-and-disallowing-packing"></a>Richiesta di replica da distribuito tra tutti i domini e compressione disabilitando
È possibile specificare un altro criterio è per richiedere informazioni di replica da sempre distribuito tra i domini guasto disponibili. Questa operazione verrà eseguita per impostazione predefinita nella maggior parte dei casi in cui il cluster è integro, tuttavia esistono degenerati casi in cui repliche per una determinata partizione potrebbe essere necessario temporaneamente compressi in un singolo guasto o aggiornamento del dominio. Ad esempio, si supponga che anche se il cluster dispone 9 nodi nei domini di errore 3 (0, 1 e 2) e il servizio ha 3 repliche, i nodi che sono stati utilizzati per tali replica guasto domini 1 e 2 arrestato e a causa di problemi di capacità nessuno dei nodi questi domini validi. Se tessuti servizio utilizzato per creare sostituzioni per tali replica, la gestione di risorse Cluster avrebbe inserirli nel dominio di errore 0, ma che crea una situazione in cui è violazione il vincolo di dominio. Aumenta anche la probabilità che il set di replica intero potrebbe andare perso (se dovessero essere permananently perdita FD 0). (Per ulteriori informazioni sui vincoli e vincolo priorità in genere, consultare [questo argomento](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities) )

Se si è mai visto un avviso di sicurezza come "bilanciamento del carico ha rilevato violazione di un vincolo per questo duplicata: tessuti: /<some service name> partizione secondaria <some partition ID> violazione del vincolo: FaultDomain" si è verificato questa condizione o qualcosa di simile. In genere queste situazioni sono temporanee, i nodi non rimangono verso il basso lungo, o se quello ed è necessario creare sostituzioni ci sono altri nodi nei domini guasto corretto validi, ma sono disponibili alcuni carichi di lavoro che si intrattengono rapporti commerciali disponibilità per il rischio di perdere tutto la rispettiva replica. È possibile ripetere l'operazione specificando i criteri "RequireDomainDistribution", che verranno garantisce che nessun due repliche dalla stessa partizione mai consentite nello stesso dominio guasto o l'aggiornamento.

Alcuni carichi di lavoro avrebbe invece il numero di destinazione di repliche (copie di stato) in qualsiasi momento (scommesse da eventuali problemi di dominio totale e sapere che sono in genere possibile ripristinare lo stato locale), che altri utenti da adottare invece il tempo di inattività anteriori rischiare le preoccupazioni correttezza e dataloss. Poiché la maggior parte dei carichi di lavoro di produzione eseguito con più di 3, il valore predefinito è non richiedono distribuzione di dominio e attendere che il bilanciamento del carico e failover gestire casi normalmente anche se questo significa che temporaneamente un dominio con più repliche comprimere al suo interno.

Codice:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

A questo punto, è possibile usare queste configurazioni per i servizi di un cluster che non è stata occupate limitazioni geografiche? Assicurarsi che è possibile! Ma non c'è un ottimo motivo troppo, in particolare le configurazioni di dominio necessari, non validi e preferito evitare a meno che non è in esecuzione in realtà un cluster con limitazioni geografiche spanning - rende non abbia senso per tentare di forzare un determinato carico di lavoro per l'esecuzione in un unico rack o preferisca alcune segmento del cluster locale rispetto a un altro, a meno che non esiste diversi tipi di hardware o carico di lavoro segmentazione il problema , e tali casi possono essere gestiti tramite vincoli di posizionamento normale.

## <a name="next-steps"></a>Passaggi successivi
- Per ulteriori informazioni sulle altre opzioni disponibili per la configurazione dei servizi estrazione obbligatoria l'argomento in altre configurazioni Manager delle risorse Cluster disponibili [informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
