<properties
   pageTitle="Gestione risorse Cluster di servizio tessuti - affinità | Microsoft Azure"
   description="Panoramica della configurazione affinità per tessuti servizi"
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

# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Configurare e utilizzare affinità servizio nelle tessuti servizio

Affinità è un controllo fornito principalmente per semplificare la transizione di applicazioni su più grande al mondo cloud e microservices. Detto che può anche essere utilizzato in alcuni casi come ottimizzazione legittimi per migliorare le prestazioni dei servizi, anche se questo può avere effetti.

Supponiamo che si sta caricarli un'app più grande o a quello appena non è stata progettata con microservices in considerazione per tessuti servizio. La durata della transizione è effettivamente comune e abbiamo utilizzato diversi clienti (interni ed esterni) in questo caso. È necessario innanzitutto sollevare l'intera App nell'ambiente, ottenerlo pacchetto e in esecuzione. È quindi avviare suddividere in diversi servizi di dimensioni inferiori che tutti parlare tra loro.

È un "Oops...". "Oops" in genere suddivise in queste categorie:

1. Alcuni componenti X nell'app su disponesse una relazione per componente Y ed è attivato solo quelle in servizi distinti. Poiché questi a questo punto viene eseguito in diversi nodi del cluster, che siano interrotti.
2.  Questi aspetti comunicano tramite (locale denominato pipes | memoria condivisa | file su disco), ma sono realmente necessari per poter aggiornare in modo indipendente per velocizzare l'un attimo. Dipendenza disco verrà rimossa in un secondo momento.
3.  Tutto funziona correttamente, ma si scopre che questi due componenti sono effettivamente molto semplici/sensibili alle prestazioni. Quando vengono spostate nei servizi separati complessivamente le prestazioni dell'applicazione tanked o latenza viene aumentata. Di conseguenza, globale della richiesta non soddisfa le aspettative.

In questi casi si non vuole perdere il lavoro refactoring e non si vuole tornare alla monolite, ma è necessario un certo senso di località. Questa operazione verrà mantenuta finché non è possibile riprogettare i componenti alla possibilità di lavorare come servizi o fino a quando non è in grado di risolvere le aspettative dei clienti di un altro modo, se possibile.

cosa fare? È anche possibile tentare di attivare affinità.

## <a name="how-to-configure-affinity"></a>Come configurare l'affinità
Per impostare l'affinità, definire una relazione affinità tra due diversi servizi. È possibile pensare affinità come "posizionando il mouse" un servizio in un'altra e dire "questo servizio eseguire solo in cui che sia in esecuzione." In alcuni casi si parla affinità come una relazione padre/figlio (in cui si posiziona il figlio padre). Affinità garantisce che le istanze di un servizio o repliche vengono inserite nella stessa nodi come repliche o istanze di un altro.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## <a name="different-affinity-options"></a>Opzioni di affinità diversi
Affinità è rappresentato tramite uno dei numerosi schemi di correlazione e ha due diverse modalità. La modalità più comune di affinità è la cosiddetta NonAlignedAffinity. In NonAlignedAffinity le istanze di diversi servizi o repliche vengono inserite nei nodi stesso. Altre modalità è AlignedAffinity. Affinità allineati è utile solo con i servizi di informazioni sullo stato. Configurazione dei due servizi informazioni sullo stato per allineato affinità garantisce che i colori di tali servizi vengono inseriti nella stessa nodi come tra loro. Fa in modo ogni coppia di database secondari per tali servizi collocare nei nodi stesso. È anche possibile (anche se meno comune) per configurare NonAlignedAffinity per i servizi di informazioni sullo stato. Per NonAlignedAffinity repliche diverse dei due servizi informazioni sullo stato sarebbero collocate nella stessa nodi, ma non si tentato di allineare i colori o dei secondari.

![Modalità di affinità e relativi effetti][Image1]

### <a name="best-effort-desired-state"></a>Stato basata bene accolta migliore
Esistono alcune differenze tra affinità e architetture su. Molti dei quali sono perché una relazione affinità programmazione migliore. I servizi in una relazione affinità sono sostanzialmente diverse entità che può avere esito negativo e spostate in modo indipendente. Sono inoltre cause per perché interruzione di una relazione affinità. Ad esempio limiti di capacità nel punto in cui solo alcuni degli oggetti servizi inclusi nella relazione affinità possono essere visualizzati in un determinato nodo. In questi casi anche se esiste una relazione affinità sul posto, in essa non è possibile applicare a causa di altri vincoli. Se è possibile applicare tutti gli altri vincoli e affinità in un secondo momento la violazione del vincolo affinità verrà corretti automaticamente.  

### <a name="chains-vs-stars"></a>Catene e stelle
Oggi non siamo in grado di serie di modelli di relazioni di affinità. Che cosa significa che un servizio figlio in una relazione tra affinità non può essere un elemento padre in un'altra relazione affinità. Se si vuole modellare questo tipo di relazione, è necessario in modo efficace modello come una stella, anziché una catena. Per eseguire questa operazione, figlio più basso da essere padre invece all'elemento padre del bambino "centrale". A seconda della disposizione dei servizi, questa operazione può richiedere la creazione di un servizio "segnaposto" come elemento padre per diversi elementi figlio.

![Catene e stelle nel contesto di affinità relazioni][Image2]

Un altro aspetto importante sulle relazioni di affinità oggi è che sono direzionali. Questo errore indica che la regola "affinità" applica solo che l'elemento figlio sia dove si trova l'elemento padre. Se ad esempio l'elemento padre improvvisamente ha esito negativo su in un altro nodo quindi Gestione risorse Cluster non viene effettivamente pensare che esiste un problema finché non rileva che l'elemento figlio non si trova con un elemento padre; la relazione non viene applicata immediatamente.

### <a name="partitioning-support"></a>Supporto partizione
L'ultimo a proposito di affinità è tale affinità relazioni non sono supportate in cui è suddiviso padre. Si tratta di un elemento che si può supporta alla fine, ma attualmente non è consentita.

## <a name="next-steps"></a>Passaggi successivi
- Per ulteriori informazioni sulle altre opzioni disponibili per la configurazione dei servizi estrazione obbligatoria l'argomento in altre configurazioni Manager delle risorse Cluster disponibili [informazioni sulla configurazione dei servizi](service-fabric-cluster-resource-manager-configure-services.md)
- Motivi molti casi in cui gli utenti utilizzano affinità, ad esempio limitando servizi a un piccolo set di computer e si tenta di aggregare il caricamento di un insieme di servizi, sono supportati migliore tra gruppi di applicazioni. Estrarre [I gruppi di applicazioni](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png
