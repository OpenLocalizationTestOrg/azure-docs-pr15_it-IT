<properties
   pageTitle="Architettura service affidabile | Microsoft Azure"
   description="Panoramica dell'architettura Service affidabile per i servizi di informazioni sullo stato e senza stati"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/30/2016"
   ms.author="alanwar"/>

# <a name="architecture-for-stateful-and-stateless-reliable-services"></a>Architettura delle informazioni sullo stato e senza stato servizi affidabili

Potrebbe essere un Azure tessuti affidabile servizio informazioni sullo stato o senza informazioni sullo stato. Ogni tipo di servizio viene eseguita all'interno di un'architettura specifica. Queste architetture sono descritti in questo articolo.
Vedere la [Panoramica Service affidabile](service-fabric-reliable-services-introduction.md) per ulteriori informazioni sulle differenze tra i servizi di informazioni sullo stato e senza stati.

## <a name="stateful-reliable-services"></a>Informazioni sullo stato servizi affidabili

### <a name="architecture-of-a-stateful-service"></a>Architettura di un servizio informazioni sullo stato
![Diagramma dell'architettura di un servizio informazioni sullo stato](./media/service-fabric-reliable-services-platform-architecture/reliable-stateful-service-architecture.png)

### <a name="stateful-reliable-service"></a>Informazioni sullo stato Service affidabile

Un servizio informazioni sullo stato affidabile possibile derivano dalla classe StatefulService o StatefulServiceBase. Entrambe le classi base vengono forniti dal servizio tessuti. Offrono vari livelli di supporto e astrazione per il servizio informazioni sullo stato di interagire con tessuti servizio - e faccia parte di un servizio all'interno del cluster servizio tessuti.

StatefulService deriva da StatefulServiceBase. StatefulServiceBase più flessibile di servizi, ma richiede ulteriori informazioni di elementi interni dell'infrastruttura di servizio.
Per ulteriori informazioni sulle specifiche di servizi di scrittura, utilizzando le classi StatefulService e StatefulServiceBase, vedere la [Panoramica Service affidabile](service-fabric-reliable-services-introduction.md) e [Service affidabile avanzate per l'uso](service-fabric-reliable-services-advanced-usage.md) .

Entrambe le classi base gestiscono il ciclo di vita e il ruolo dell'implementazione del servizio. L'implementazione del servizio può eseguire l'override di metodi virtuali di una classe, se l'implementazione del servizio include operazioni da eseguire tali punti nel ciclo di vita di implementazione del servizio, oppure se si desidera creare un oggetto di comunicare ascoltatore comunicazioni. Nota Sebbene implementazione del servizio può implementare un oggetto di comunicare ascoltatore comunicazioni esposizione ICommunicationListener, nella figura precedente, comunicare ascoltatore comunicazioni è implementata dal servizio tessuti, come l'implementazione del servizio utilizza un comunicare ascoltatore comunicazioni implementato dal servizio tessuti.

Un servizio affidabile informazioni sullo stato utilizza il responsabile stato affidabile per usufruire delle raccolte affidabile. Raccolte affidabile sono strutture dati locali altamente disponibili per il servizio, ossia, sono sempre disponibili, indipendentemente dal servizio failover. Ogni tipo di raccolta affidabile è implementata da un provider di stato affidabile.
Per ulteriori informazioni sulle raccolte affidabile, vedere [informazioni generali sulle raccolte affidabile](service-fabric-reliable-services-reliable-collections.md).

### <a name="reliable-state-manager-and-state-providers"></a>Responsabile stato affidabile e provider dello stato

Il responsabile stato affidabile è l'oggetto che gestisce il provider dello stato affidabile. Dispone le funzionalità per creare, eliminare, enumerare e assicurarsi che i provider di stato affidabile siano persistenti e disponibilità. Un'istanza del provider di stato affidabile rappresenta un'istanza di una struttura di dati permanenti e disponibilità, ad esempio un dizionario o a una coda.

Ogni provider stato affidabile presenta un'interfaccia che viene utilizzata da un servizio informazioni sullo stato per interagire con il provider dello stato affidabile. Ad esempio, viene utilizzato IReliableDictionary per interagire con il dizionario affidabile, mentre IReliableQueue viene utilizzato per interagire con coda affidabile. Tutti i provider di stato affidabile implementano l'interfaccia IReliableState.

Il responsabile stato affidabile ha un'interfaccia denominata IReliableStateManager, che consente l'accesso a tale da un servizio informazioni sullo stato. Interfacce per i provider di stato affidabile vengono restituite tramite IReliableStateManager.

Il responsabile stato affidabile utilizza un'architettura plug-in, in modo che i nuovi tipi di raccolte affidabile possono essere collegati in modo dinamico.

Il dizionario affidabile e coda affidabile si basano l'implementazione di un archivio differenziale prestazioni elevate, versione.

### <a name="transactional-replicator"></a>Transazione replica

Il componente di replica transazione è responsabile per garantire che lo stato di un servizio (lo stato all'interno il responsabile stato affidabile e le raccolte affidabile) sia coerenza in ogni replica che esegue il servizio. Garantisce inoltre che lo stato verranno salvato nel registro. Interfacce di gestione dello stato affidabile con replica transazione tramite un meccanismo privato.

Transazione replica utilizza un protocollo di rete per comunicare lo stato con sé dell'istanza del servizio in modo che ogni replica abbiano informazioni aggiornate sullo stato.

Replica transazione utilizza un file di log per mantenere le informazioni sullo stato in modo che le informazioni sullo stato viene conservata processo o nodo determina l'arresto anomalo. L'interfaccia nel log consiste nell'utilizzare un meccanismo privato.

### <a name="log"></a>Log

Il componente log fornisce un archivio permanente le massime prestazioni ottimizzate per la scrittura a rotante o mercati dischi.  La struttura del log è per l'archiviazione permanente (ad esempio dischi rigidi) di diventare locali per i nodi che eseguono il servizio informazioni sullo stato. In questo modo per latenza bassa e alta velocità rispetto a persistente archiviazione remota, che non è locale per il nodo.

Il componente log utilizza più file di log. Esiste un file di log condiviso a livello di nodo utilizzate ogni replica come può fornire la latenza più bassa e più alta velocità per l'archiviazione dei dati relativi allo stato. Per impostazione predefinita i log condiviso viene inserito nella cartella di lavoro nodo servizio tessuti, ma possono essere configurato per collocare in un'altra posizione, ideale su un disco riservato solo il log condiviso. Ogni replica per il servizio ha anche un file di log dedicato e log dedicato viene inserito nella directory di lavoro del servizio. Non esiste alcun modo per configurare il log dedicato devono essere posizionate in un percorso diverso.

Log condiviso è un'area di transizione per informazioni sullo stato della replica, mentre il file di log dedicato rappresenta la destinazione finale nel punto in cui viene mantenuta. Nella finestra di progettazione, informazioni sullo stato scritte nei file di log condiviso e quindi lentamente spostate nel file di log dedicata in background. In questo modo, la scrittura nel log condiviso avrebbe la latenza più bassa e più alta velocità che consente al servizio rendere più rapidamente lo stato di avanzamento.

Lettura e scrittura nel log condiviso vengono eseguite tramite IO diretto a allocato precedentemente spazio sul disco rigido per il file di log condiviso. Per consentire un uso ottimale di spazio su disco nell'unità con i log dedicato, il file di log dedicato viene creato come un file caricato in memoria bassa NTFS. Si noti che in questo modo sarà overprovisioning di spazio su disco e il sistema operativo mostrerà i file di log dedicato utilizzando molto spazio su disco rispetto a quello effettivamente utilizzato.

Oltre a un'interfaccia utente-modalità minima nel log, viene registrata come un driver in modalità kernel. Eseguendo come un driver in modalità kernel, il log può fornire le massime prestazioni a tutti i servizi che ne fanno uso.

Per ulteriori informazioni sulla configurazione di log, vedere [Servizi affidabili informazioni sullo stato di configurazione](service-fabric-reliable-services-configuration.md).

## <a name="stateless-reliable-service"></a>Service affidabile senza informazioni sullo stato

### <a name="architecture-of-a-stateless-service"></a>Architettura di un servizio senza informazioni sullo stato
![Diagramma dell'architettura di un servizio senza informazioni sullo stato](./media/service-fabric-reliable-services-platform-architecture/reliable-stateless-service-architecture.png)

### <a name="stateless-reliable-service"></a>Service affidabile senza informazioni sullo stato

Implementazioni senza informazioni sullo stato del servizio derivano dalla classe StatelessService o StatelessServiceBase. La classe StatelessServiceBase consente una maggiore flessibilità rispetto alla classe StatelessService.
Entrambe le classi base gestiscono il ciclo di vita e il ruolo di un servizio.

L'implementazione del servizio può eseguire l'override di metodi virtuali della classe base se il servizio presenta operazioni da eseguire tali punti nel ciclo di vita del servizio, o se si desidera creare un oggetto di comunicare ascoltatore comunicazioni. Si noti anche se il servizio può implementare un oggetto di comunicare ascoltatore comunicazioni esposizione ICommunicationListener, nella figura precedente, comunicare ascoltatore comunicazioni è implementata dal servizio tessuti, come l'implementazione del servizio utilizza un comunicare ascoltatore comunicazioni implementato dal servizio tessuti.

Per ulteriori informazioni sulle specifiche di scrittura services utilizzando le classi StatelessService e StatelessServiceBase, vedere la [Panoramica Service affidabile](service-fabric-reliable-services-introduction.md) e [Service affidabile avanzate per l'uso](service-fabric-reliable-services-advanced-usage.md) .

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su tessuti servizio, vedere:

[Panoramica del servizio affidabile](service-fabric-reliable-services-introduction.md)

[Guida introduttiva](service-fabric-reliable-services-quick-start.md)

[Informazioni generali sulle raccolte affidabile](service-fabric-reliable-services-reliable-collections.md)

[Service affidabile utilizzo avanzato](service-fabric-reliable-services-advanced-usage.md)

[Configurazione del servizio affidabile](service-fabric-reliable-services-configuration.md)  
