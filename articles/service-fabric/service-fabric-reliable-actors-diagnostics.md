<properties
   pageTitle="Gli operatori diagnostica e monitoraggio | Microsoft Azure"
   description="In questo articolo descrive la diagnostica e il monitoraggio delle caratteristiche di runtime servizio tessuti affidabile operatori, inclusi gli eventi e contatori emessi da essa prestazioni."
   services="service-fabric"
   documentationCenter=".net"
   authors="abhishekram"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/05/2016"
   ms.author="abhisram"/>

# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnostica e il monitoraggio delle prestazioni per gli operatori affidabile
Il runtime operatori affidabile generati [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) eventi e [contatori](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Queste informazioni dettagliate sui come funziona il runtime e agevolare la risoluzione dei problemi e il monitoraggio delle prestazioni.

## <a name="eventsource-events"></a>EventSource eventi
Il nome del provider EventSource per il runtime operatori affidabile è "Microsoft-ServiceFabric-operatori". Gli eventi da origine degli eventi vengano visualizzati nella finestra di [Diagnostica eventi](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quando l'applicazione dell'attore viene [eseguito il debug in Visual Studio](service-fabric-debugging-your-application.md).

Esempi di tecnologie per la raccolta e/o di visualizzazione degli eventi EventSource e gli strumenti sono [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Diagnostica di Azure](../cloud-services/cloud-services-dotnet-diagnostics.md), [Registrazione Semantic](https://msdn.microsoft.com/library/dn774980.aspx)e [Microsoft TraceEvent raccolta](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Parole chiave
Tutti gli eventi che appartengono a EventSource operatori affidabile sono associati a una o più parole. In questo modo il filtro degli eventi che sono stati raccolti. Bit parola chiave elencati di seguito sono definiti.

|Bit|Descrizione|
|---|---|
|0x1|Serie di eventi importanti che riepilogano l'operazione di runtime operatori tessuti.|
|0x2|Gruppo di eventi che descrivono attore chiamate. Per ulteriori informazioni, vedere l' [argomento introduttivo su operatori](service-fabric-reliable-actors-introduction.md#actors).|
|0x4|Gruppo di eventi relativi allo stato dell'attore. Per ulteriori informazioni, vedere l'argomento sulla [gestione dello stato dell'attore](service-fabric-reliable-actors-state-management.md).|
|0x8|Gruppo di eventi correlati alla concorrenza basate su Attiva l'attore. Per ulteriori informazioni, vedere l'argomento sulla [concorrenza](service-fabric-reliable-actors-introduction.md#concurrency).|

## <a name="performance-counters"></a>Contatori delle prestazioni
Il runtime operatori affidabile definirà le seguenti categorie di contatore prestazioni.

|Categoria|Descrizione|
|---|---|
|Servizio tessuti attore|Contatori specifici per tessuti servizio Azure operatori, ad esempio temporali per salvare lo stato dell'attore|
|Metodo attore tessuti del servizio|Contatori specifiche dei metodi implementati da operatori tessuti servizio, ad esempio la frequenza attore viene richiamato un metodo|

Ognuna delle categorie è uno o più contatori.

Per raccogliere e visualizzare dati, è possibile utilizzare l'applicazione di [Windows Performance Monitor](https://technet.microsoft.com/library/cc749249.aspx) che è disponibile per impostazione predefinita nel sistema operativo Windows. [Azure diagnostica](../cloud-services/cloud-services-dotnet-diagnostics.md) è un'altra opzione per la raccolta di dati del contatore prestazioni e caricarlo in tabelle Azure.

### <a name="performance-counter-instance-names"></a>Nomi di istanza contatore prestazioni
Un cluster che contiene un numero elevato di servizi dell'attore o partizioni servizio attore avrà un numero elevato di istanze del contatore prestazioni dell'attore. I nomi delle varianti contatore prestazioni consente di identificare il metodo di [partizione](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) e attore specifico (se applicabile) che è associata l'istanza del contatore delle prestazioni.

#### <a name="service-fabric-actor-category"></a>Categoria servizio tessuti attore
Per la categoria `Service Fabric Actor`, i nomi di istanza contatore siano nel formato seguente:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* è la rappresentazione di stringa dell'ID di partizione tessuti servizio associato all'istanza del contatore delle prestazioni. L'ID di partizione è un GUID e rappresentazione di stringa viene generato tramite il [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) metodo con lo specificatore di formato "D".

*ActorRuntimeInternalID* è la rappresentazione di un numero intero a 64 bit che viene generato dal runtime tessuti operatori per l'uso interno. Incluso nel nome dell'istanza contatore delle prestazioni per garantire l'univocità ed evitare conflitti con altri nomi di istanza contatore prestazioni. Gli utenti non tentare di interpretare questa parte del nome dell'istanza contatore prestazioni.

La seguente è illustrato un esempio di un nome di istanza contatore per un contatore a cui appartiene la `Service Fabric Actor` categoria:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

Nell'esempio precedente, `2740af29-78aa-44bc-a20b-7e60fb783264` è la rappresentazione di stringa dell'ID di partizione tessuti servizio e `635650083799324046` utilizzare l'ID di 64 bit generato per il runtime interno.

#### <a name="service-fabric-actor-method-category"></a>Categoria servizio tessuti attore metodo
Per la categoria `Service Fabric Actor Method`, i nomi di istanza contatore siano nel formato seguente:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*NomeMetodo* è il nome del metodo attore associato all'istanza del contatore delle prestazioni. Il formato del nome del metodo viene determinato in base a una logica di runtime tessuti operatori che distribuisce la leggibilità del nome con i vincoli la lunghezza massima dei nomi delle istanze del contatore delle prestazioni in Windows.

*ActorsRuntimeMethodId* è la rappresentazione di un numero intero a 32 bit che viene generato dal runtime tessuti operatori per l'uso interno. Incluso nel nome dell'istanza contatore delle prestazioni per garantire l'univocità ed evitare conflitti con altri nomi di istanza contatore prestazioni. Gli utenti non tentare di interpretare questa parte del nome dell'istanza contatore prestazioni.

*ServiceFabricPartitionID* è la rappresentazione di stringa dell'ID di partizione tessuti servizio associato all'istanza del contatore delle prestazioni. L'ID di partizione è un GUID e rappresentazione di stringa viene generato tramite il [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) metodo con lo specificatore di formato "D".

*ActorRuntimeInternalID* è la rappresentazione di un numero intero a 64 bit che viene generato dal runtime tessuti operatori per l'uso interno. Incluso nel nome dell'istanza contatore delle prestazioni per garantire l'univocità ed evitare conflitti con altri nomi di istanza contatore prestazioni. Gli utenti non tentare di interpretare questa parte del nome dell'istanza contatore prestazioni.

La seguente è illustrato un esempio di un nome di istanza contatore per un contatore a cui appartiene la `Service Fabric Actor Method` categoria:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

Nell'esempio precedente, `ivoicemailboxactor.leavemessageasync` è il nome del metodo, `2` è l'ID di versione a 32 bit generato per uso interno del runtime `89383d32-e57e-4a9b-a6ad-57c6792aa521` è la rappresentazione di stringa dell'ID di partizione tessuti servizio e `635650083804480486` utilizzare l'ID di 64 bit generato per il runtime interno.

## <a name="list-of-events-and-performance-counters"></a>Elenco degli eventi e contatori delle prestazioni

### <a name="actor-method-events-and-performance-counters"></a>Attore metodo eventi e contatori delle prestazioni
Il runtime operatori affidabile emette gli eventi seguenti relativi ai [metodi attore](service-fabric-reliable-actors-introduction.md#actors).

|Nome dell'evento|ID evento|Livello|Parola chiave|Descrizione|
|---|---|---|---|---|
|ActorMethodStart|7|Dettagliato|0x2|Gli operatori runtime sta per richiamare un metodo attore.|
|ActorMethodStop|8|Dettagliato|0x2|Un metodo attore termine dell'esecuzione. Vale a dire chiamata asincrono del runtime al metodo dell'attore è stata eseguita e completamento dell'attività restituito dal metodo attore.|
|ActorMethodThrewException|9|Avviso|0x3|Eccezione durante l'esecuzione di un metodo attore durante la chiamata asincrono del runtime al metodo dell'attore o durante l'esecuzione dell'attività restituito dal metodo attore. Questo evento indica qualche tipo di errore nel codice attore che deve essere indagini.|

Il runtime operatori affidabile pubblica contatori riportati di seguito correlati all'esecuzione dei metodi attore.

|Nome della categoria|Nome del contatore|Descrizione|
|---|---|---|
|Metodo attore tessuti del servizio|Chiamate/Sec|Numero di volte in cui viene richiamato il metodo di servizio attore al secondo|
|Metodo attore tessuti del servizio|Media millisecondi per ciascuna chiamata|Tempo per eseguire il metodo di servizio attore in millisecondi|
|Metodo attore tessuti del servizio|Eccezioni generate/Sec|Numero di volte in cui l'attore metodo di servizio eccezione al secondo|

### <a name="concurrency-events-and-performance-counters"></a>Concorrenza eventi e contatori delle prestazioni
Il runtime operatori affidabile genera gli eventi seguenti correlati alla [concorrenza](service-fabric-reliable-actors-introduction.md#concurrency).

|Nome dell'evento|ID evento|Livello|Parola chiave|Descrizione|
|---|---|---|---|---|
|ActorMethodCallsWaitingForLock|12|Dettagliato|0x8|Questo evento è scritto all'inizio di ogni nuovo attivare un attore. Contiene il numero di chiamate attore in attesa di acquisire il blocco per attore che applica concorrenza basato su in sospeso.|

Il runtime operatori affidabile pubblica contatori riportati di seguito correlati alla concorrenza.

|Nome della categoria|Nome del contatore|Descrizione|
|---|---|---|
|Servizio tessuti attore|# chiamate attore in attesa di blocco dell'attore|Numero di chiamate attore in attesa di acquisire il blocco per attore che applica concorrenza basato su in sospeso|
|Servizio tessuti attore|Attendere Media millisecondi per ogni blocco|Tempo (in millisecondi) per acquisire il blocco per attore che applica basato su concorrenza|
|Servizio tessuti attore|Blocco dell'attore millisecondi Media|Tempo (in millisecondi) per il quale viene mantenuto il blocco per attore|

### <a name="actor-state-management-events-and-performance-counters"></a>Gli eventi di gestione dello stato dell'attore e contatori delle prestazioni
Il runtime operatori affidabile emette gli eventi seguenti correlati alla [gestione dello stato dell'attore](service-fabric-reliable-actors-state-management.md).

|Nome dell'evento|ID evento|Livello|Parola chiave|Descrizione|
|---|---|---|---|---|
|ActorSaveStateStart|10|Dettagliato|0x4|Gli operatori runtime sta per salvare lo stato dell'attore.|
|ActorSaveStateStop|11|Dettagliato|0x4|Gli operatori runtime completata salvare lo stato dell'attore.|

Il runtime operatori affidabile pubblica contatori riportati di seguito relativi alla gestione dello stato dell'attore.

|Nome della categoria|Nome del contatore|Descrizione|
|---|---|---|
|Servizio tessuti attore|Media millisecondi per salvare lo stato operazione|Tempo impiegato per salvare lo stato dell'attore in millisecondi|
|Servizio tessuti attore|Media millisecondi per operazione di stato di caricamento|Tempo necessario per caricare lo stato dell'attore in millisecondi|

### <a name="events-related-to-actor-replicas"></a>Eventi correlati alle repliche attore
Il runtime operatori affidabile genera gli eventi seguenti relativi alle [repliche attore](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateful-actors).

|Nome dell'evento|ID evento|Livello|Parola chiave|Descrizione|
|---|---|---|---|---|
|ReplicaChangeRoleToPrimary|1|Informativo|0x1|Replica attore modificato ruolo principale. Questo significa che gli operatori per questa partizione verranno creati all'interno di questa replica.|
|ReplicaChangeRoleFromPrimary|2|Informativo|0x1|Replica attore modificato ruolo non primario. Questo significa che gli operatori per questa partizione non verranno creati non è più all'interno di questa replica. Nessun nuove convocazioni verranno recapitate a operatori già stati creati all'interno di questa replica. I soggetti verranno eliminati dopo aver completate tutte le richieste in corso.|

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Eventi di attivazione e disattivazione dell'attore e contatori delle prestazioni
Il runtime operatori affidabile genera gli eventi seguenti correlati [attore attivazione e disattivazione](service-fabric-reliable-actors-lifecycle.md).

|Nome dell'evento|ID evento|Livello|Parola chiave|Descrizione|
|---|---|---|---|---|
|ActorActivated|5|Informativo|0x1|Un attore è stato attivato.|
|ActorDeactivated|6|Informativo|0x1|Un attore è stato disattivato.|

Il runtime operatori affidabile pubblica contatori riportati di seguito correlati a attore attivazione e disattivazione.

|Nome della categoria|Nome del contatore|Descrizione|
|---|---|---|
|Servizio tessuti attore|Calcolare la media di OnActivateAsync millisecondi|Tempo per eseguire il metodo OnActivateAsync in millisecondi|

### <a name="actor-request-processing-performance-counters"></a>Elaborazione della richiesta attore contatori
Quando un client richiama un metodo tramite un oggetto proxy attore, il risultato un messaggio di richiesta inviato in rete al servizio attore. Il servizio elabora il messaggio di richiesta e invia una risposta al client. Il runtime operatori affidabile pubblica contatori delle prestazioni seguenti relativi all'elaborazione del richiesta attore.

|Nome della categoria|Nome del contatore|Descrizione|
|---|---|---|
|Servizio tessuti attore|# di richieste in attesa|Numero di richieste elaborate nel servizio|
|Servizio tessuti attore|Media millisecondi per ogni richiesta|Tempo (in millisecondi dal servizio per l'elaborazione di una richiesta)|
|Servizio tessuti attore|Media millisecondi per la deserializzazione richiesta|Tempo (in millisecondi) per deserializzare il messaggio di richiesta dell'attore ricezione presso il servizio|
|Servizio tessuti attore|Media millisecondi per la serializzazione di risposta|Tempo (in millisecondi) per serializzare il messaggio di risposta dell'attore presso il servizio prima che venga inviata la risposta al client|

## <a name="next-steps"></a>Passaggi successivi
 - [Utilizzo di piattaforma tessuti servizio operatori affidabile](service-fabric-reliable-actors-platform.md)
 - [Documentazione di riferimento attore API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Codice di esempio](https://github.com/Azure/servicefabric-samples)
