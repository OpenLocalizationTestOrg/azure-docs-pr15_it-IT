<properties 
    pageTitle="Eseguire la migrazione di cache per Redis | Microsoft Azure"
    description="Informazioni su come eseguire la migrazione delle applicazioni di servizio Cache gestiti in modo che Azure Redis Cache"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags 
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="09/30/2016"
    ms.author="sdanie" />

# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>Eseguire la migrazione dal servizio Cache gestita da memorizzare nella Cache Redis Azure

Migrazione di applicazioni che utilizzano Azure gestite Cache servizio per Azure Redis Cache può essere eseguita con modifiche minime per l'applicazione, a seconda delle funzionalità del servizio di Cache gestito utilizzate dall'applicazione memorizzazione nella cache. Durante l'API non sono esattamente la stessa sono simili e gran parte del codice esistente che utilizza gestite servizio Cache per accedere a una cache può essere riutilizzato con modifiche minime. In questo argomento viene illustrato come apportare le modifiche di applicazione per la migrazione delle applicazioni di servizio Cache gestiti utilizzare Azure Redis Cache e configurazione necessarie e vengono visualizzati come alcune delle caratteristiche di Azure Redis Cache possono essere utilizzate per implementare la funzionalità di una cache del servizio di Cache gestito.

## <a name="migration-steps"></a>Passaggi di migrazione

La procedura seguente è necessario eseguire la migrazione di un'applicazione di servizio Cache gestiti utilizzare Azure Redis Cache.

-   Eseguire il mapping caratteristiche gestite servizio Cache nella Cache Redis Azure
-   Scegliere un'offerta di Cache
-   Creare una Cache
-   Configurare i client di Cache
    -   Rimuovere la configurazione del servizio di Cache gestiti
    -   Configurare un client di cache utilizzando il pacchetto NuGet StackExchange.Redis
-   Eseguire la migrazione di codice gestito servizio Cache
    -   Connettersi alla cache utilizzando la classe ConnectionMultiplexer
    -   Tipi di dati di base di Access nella cache
    -   Utilizzare gli oggetti .NET nella cache
-   Spostare lo stato sessione ASP.NET e la cache di Output nella Cache Redis Azure 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>Eseguire il mapping caratteristiche gestite servizio Cache nella Cache Redis Azure

Azure gestite servizio Cache e Azure Redis Cache sono simili ma implementare alcune delle loro funzionalità in modi diversi. In questa sezione vengono descritte alcune delle differenze e vengono fornite indicazioni per l'implementazione delle funzionalità del servizio di Cache gestiti nella Cache Redis Azure.

|Funzionalità di servizio Cache gestiti|Supporto per il servizio Cache gestito|Supporto Redis Cache Azure|
|---|---|---|
|Cache denominate|La cache predefinita è configurata e nella cache Standard e Premium offerte, fino a nove altre cache denominate possono essere configurate se lo si desidera.|Azure Redis cache contengono un numero configurabile di database (impostazione predefinita di 16) che possono essere utilizzati per implementare una funzionalità simile alle denominato cache. Per ulteriori informazioni, vedere [configurazione di server predefinito Redis](cache-configure.md#default-redis-server-configuration).|
|Disponibilità|Fornisce disponibilità per gli elementi nella cache nelle offerte di cache Standard e Premium. Se gli elementi vengono persi a causa di un errore, copie di backup di elementi nella cache sono ancora disponibili. Operazioni di scrittura nella cache secondaria vengono eseguite in modo sincrono.|Disponibilità è disponibile nelle offerte di cache Standard e Premium che hanno una configurazione principale/Replica di due nodi (ogni condiviso in una cache Premium ha una coppia di principale/replica). Operazioni di scrittura alla replica di vengono eseguite in modo asincrono. Per ulteriori informazioni, vedere [Azure Redis Cache prezzi](https://azure.microsoft.com/pricing/details/cache/).|
|Notifiche|Consente ai client di ricevere notifiche asincrone quando si verificano una varietà di operazioni cache nella cache denominata.|Applicazioni client consente Redis. pub/sub o [le notifiche di spazio delle chiavi](cache-configure.md#keyspace-notifications-advanced-settings) per ottenere una funzionalità simile alle notifiche.|
|Cache locale|Memorizza una copia di oggetti memorizzati nella cache locale nel client per un accesso molto veloce.|Applicazioni client necessario implementare questa funzionalità utilizzando un dizionario o una struttura di dati simili.|
|Criteri di eliminazione|Nessuno o LRU. Il criterio predefinito è LRU.|Azure Cache Redis supporta i seguenti criteri di eliminazione: volatili lru, allkeys lru, volatili casuale, casuale, allkeys volatili-ttl, noeviction. Il criterio predefinito è volatili lru. Per ulteriori informazioni, vedere [configurazione di server predefinito Redis](cache-configure.md#default-redis-server-configuration).|
|Criteri di scadenza|I criteri di scadenza predefinita sono assoluto e l'intervallo di scadenza predefinita è dieci minuti. Sono disponibili anche i criteri di scorrimento e mai.|Per impostazione predefinita gli elementi nella cache non scadono, ma può essere configurata una scadenza alla scala cronologica per scrittura utilizzando cache set di overload. Per ulteriori informazioni, vedere [aggiungere e recuperare oggetti dalla cache](cache-dotnet-how-to-use-azure-redis-cache.md#add-and-retrieve-objects-from-the-cache).|
|Aree geografiche e Tagging|Le aree sono sottogruppi per gli elementi memorizzati nella cache. Aree supportano anche l'annotazione degli elementi della cache con stringhe descrittive aggiuntive denominate tag. Aree supportano la possibilità di eseguire operazioni di ricerca in tutti gli elementi con tag in quell'area. Tutti gli elementi in un'area si trovano all'interno di un singolo nodo cluster cache.|Una cache Redis è costituito da un singolo nodo (a meno che non è abilitato Redis cluster) in modo che non si applica il concetto di aree di servizio Cache gestito. Redis operazioni con caratteri jolly e supporta la ricerca durante il recupero delle chiavi in modo tag descrittivi possono essere incorporati i nomi delle chiavi e utilizzati per recuperare gli elementi in un secondo momento. Per un esempio di implementare una soluzione tagging utilizzando Redis, vedere [l'implementazione della cache tagging in combinazione con Redis](http://stackify.com/implementing-cache-tagging-redis/).|
|Serializzazione|Cache gestita supporta NetDataContractSerializer, BinaryFormatter e l'utilizzo di serializzatori personalizzati. Il valore predefinito è NetDataContractSerializer.|È responsabilità dell'applicazione client per serializzare oggetti .NET prima di inserirli nella cache, con la scelta del serializzatore sviluppatore dell'applicazione client. Per ulteriori informazioni e il codice di esempio, vedere [lavorare con gli oggetti .NET nella cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).|
| Emulatore cache | Cache gestita fornisce un emulatore cache locale. | Cache Redis Azure non dispone di un emulatore, ma è possibile [eseguire la compilazione di MSOpenTech di redis-server.exe localmente](cache-faq.md#cache-emulator) per assicurare un'esperienza emulatore. |

## <a name="choose-a-cache-offering"></a>Scegliere un'offerta di Cache

Microsoft Azure Redis Cache è disponibile in livelli seguenti:

-   **Base** – singolo nodo. Più dimensioni fino a 53 GB.
-   **Standard** : due nodo principale/duplicata. Più dimensioni fino a 53 GB. 99,9 CONTRATTO DI SERVIZIO %.
-   **Premium** : due nodo principale/duplicata con shards fino a 10. Più dimensioni da 6 GB a 530 GB (contatta Microsoft per altre informazioni). Tutte le funzionalità di livello Standard e maggior supporto incluso per [Redis cluster](cache-how-to-premium-clustering.md) [Redis persistenza](cache-how-to-premium-persistence.md)e [Virtuali Azure](cache-how-to-premium-vnet.md). 99,9 CONTRATTO DI SERVIZIO %.

Ogni livello diverso in termini di caratteristiche e prezzi. Le caratteristiche disponibili più avanti in questa Guida e per ulteriori informazioni sui prezzi, vedere [Dettagli prezzi Cache](https://azure.microsoft.com/pricing/details/cache/).

Punto di partenza per la migrazione consiste nel selezionare la dimensione che corrisponde alle dimensioni della cache del servizio di Cache gestito precedente e quindi verso l'alto o verso il basso in base ai requisiti dell'applicazione. Per ulteriori informazioni sulla scelta l'offerta di Azure Redis Cache destra, vedere [quali offerta Redis Cache e le dimensioni è opportuno usare?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Creare una Cache

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Configurare i client di Cache

Una volta creata e configurata la cache, il passaggio successivo consiste nel rimuovere la configurazione del servizio di Cache gestite e aggiungere Aggiungi la configurazione della Cache Redis Azure e riferimenti in modo che i client cache possono accedere la cache.

-   Rimuovere la configurazione del servizio di Cache gestiti
-   Configurare un client di cache utilizzando il pacchetto NuGet StackExchange.Redis

### <a name="remove-the-managed-cache-service-configuration"></a>Rimuovere la configurazione del servizio di Cache gestiti

Prima che le applicazioni client possono essere configurate per Azure Redis Cache, la configurazione del servizio di Cache gestito esistente e riferimenti ad assembly deve essere rimosso da disinstallare il pacchetto gestite NuGet servizio Cache.

Per disinstallare il pacchetto gestite NuGet servizio Cache, fare clic sul progetto client in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**. Selezionare il nodo **pacchetti installati** e digitare W**indowsAzure.Caching** nella casella Cerca pacchetti installati. Selezionare **Windows** **Azure Cache** (o **Windows** **Azure memorizzazione nella cache** a seconda della versione del pacchetto NuGet), fare clic su **Disinstalla**e quindi fare clic su **Chiudi**.

![Disinstallare pacchetto NuGet servizio Cache gestita Azure](./media/cache-migrate-to-redis/IC757666.jpg)

La disinstallazione del pacchetto gestite NuGet servizio Cache rimuove gli assembly gestiti servizio Cache e le voci gestite servizio Cache dell'App config dell'applicazione client. Perché alcune impostazioni personalizzate non possono essere rimossi quando si disinstalla il pacchetto NuGet, aprire config o App e assicurarsi che gli elementi seguenti vengono completamente rimossi.

Assicurarsi che la `dataCacheClients` voce è stata rimossa dalla `configSections` elemento. Non rimuovere l'intero `configSections` elemento; rimuovere solo le `dataCacheClients` voce, se presenta.

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
    </configSections>

Assicurarsi che la `dataCacheClients` sezione viene rimosso. Il `dataCacheClients` sezione sarà simile alla seguente.

    <dataCacheClients>
      <dataCacheClientname="default">
        <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
        <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
        <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="true">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>

Dopo la configurazione del servizio di Cache gestite viene rimosso, è possibile configurare il client della cache come descritto nella sezione seguente.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Configurare un client di cache utilizzando il pacchetto NuGet StackExchange.Redis

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Eseguire la migrazione di codice gestito servizio Cache

L'API per il client di cache StackExchange.Redis è simile al servizio Cache gestito. In questa sezione viene fornita una panoramica delle differenze.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Connettersi alla cache utilizzando la classe ConnectionMultiplexer

Nel servizio Cache gestite connessioni alla cache sono state gestite dalla `DataCacheFactory` e `DataCache` classi. In Azure Redis Cache, le connessioni vengono gestite dalla `ConnectionMultiplexer` classe.

Aggiungere la seguente istruzione nella parte superiore di qualsiasi file dal quale si vuole accedere alla cache.

    using StackExchange.Redis
                                
Se questo spazio dei nomi non viene risolto, assicurarsi di avere aggiunto il pacchetto StackExchange.Redis NuGet come descritto in [configurare i client di cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

>[AZURE.NOTE] Si noti che il client StackExchange.Redis richiede .NET Framework 4 o versioni successive.

Per connettersi a un'istanza di Azure Redis Cache, chiamare statica `ConnectionMultiplexer.Connect` metodo e passare il punto finale e la chiave. Un approccio per la condivisione di un `ConnectionMultiplexer` istanza dell'applicazione deve essere una proprietà statica che restituisce un'istanza connessa, simile al seguente. In questo modo affidabile inizializzare una sola connessa `ConnectionMultiplexer` istanza. In questo esempio `abortConnect` è impostato su false, che indica che la chiamata avrà esito positivo anche se non è possibile stabilire una connessione nella cache. Una caratteristica di `ConnectionMultiplexer` che automaticamente verrà ripristinare la connessione alla cache una volta il problema di rete o altre cause sono stati risolti.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

Da e il **Redis Cache** per l'istanza della cache, è possono ottenere l'endpoint cache, i tasti e porte. Per ulteriori informazioni, vedere [le proprietà della Cache Redis](cache-configure.md#properties).

Dopo la connessione, restituire un riferimento al database cache Redis chiamando il `ConnectionMultiplexer.GetDatabase` metodo. L'oggetto restituito dalla `GetDatabase` metodo è un oggetto pass-through semplice e non è necessario essere archiviati.

    IDatabase cache = Connection.GetDatabase();
    
    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);
    
    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Il client StackExchange.Redis utilizza il `RedisKey` e `RedisValue` tipi per l'accesso e archiviazione di elementi nella cache. Questi tipi di eseguire il mapping su più semplici tipi di lingua, inclusi stringa e spesso non vengono utilizzati direttamente. Redis stringhe il tipo di base del valore Redis che possono contenere molti tipi di dati, inclusi i flussi di binari serializzati, e mentre non è possibile utilizzare direttamente il tipo, utilizzare i metodi che contengono `String` nel nome. Per i tipi di dati più semplici archiviare e recuperare elementi dalla cache utilizzando il `StringSet` e `StringGet` metodi, a meno che non si archiviano raccolte o altri tipi di dati Redis nella cache. 

`StringSet`e `StringGet` sono molto simili al servizio Cache gestite `Put` e `Get` metodi, con una principali differenza che prima di configurare e ottenere un oggetto .NET nella cache è necessario serializzare prima di tutto. 

Quando si chiama `StringGet`, se è presente l'oggetto, viene restituito e in caso contrario, viene restituito null. In questo caso è possibile recuperare il valore dell'origine dati desiderato e memorizzare nella cache per l'uso successivo. Tale valore è noto come criterio di riposo cache.

Per specificare la scadenza di un elemento nella cache, utilizzare la `TimeSpan` parametro di `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

Azure Cache Redis possibile collaborare con gli oggetti .NET, nonché i tipi di dati di base, ma prima di un oggetto .NET può essere memorizzati nella cache deve essere serializzato. Verrà visualizzata la responsabilità dello sviluppatore di applicazioni. In questo modo per sviluppatori per la scelta del serializzatore. Per ulteriori informazioni e il codice di esempio, vedere [lavorare con gli oggetti .NET nella cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>Spostare lo stato sessione ASP.NET e la cache di Output nella Cache Redis Azure

Azure Cache Redis sono provider per lo stato sessione ASP.NET e della cache di Output di pagina. Per eseguire la migrazione dell'applicazione che utilizza le versioni gestite servizio Cache dei provider elencati, rimuovere prima le sezioni esistenti dal file Web. config e quindi configurare le versioni di Azure Redis Cache dei provider. Per ulteriori informazioni sull'utilizzo di provider di servizi di Azure Redis Cache ASP.NET, vedere [ASP.NET Session State Provider per Azure Redis Cache](cache-aspnet-session-state-provider.md) e [Provider della Cache di Output ASP.NET per Azure Redis Cache](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Passaggi successivi

Esplorare la [documentazione di Azure Redis Cache](https://azure.microsoft.com/documentation/services/cache/) per esercitazioni, esempi, video e altro ancora.

