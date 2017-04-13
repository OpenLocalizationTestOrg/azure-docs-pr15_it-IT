<properties 
    pageTitle="Esempi di Azure Cache Redis | Microsoft Azure" 
    description="Informazioni su come usare Azure Redis Cache" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="sdanie"/>

# <a name="azure-redis-cache-samples"></a>Esempi di Cache Redis Azure 

In questo argomento fornisce un elenco di esempi di Azure Redis Cache, dettagliata relativa agli scenari, ad esempio la connessione a una cache, lettura e scrittura dati da una cache e utilizzare i provider di Cache Redis ASP.NET. Alcuni esempi sono progetti scaricabili e alcune fornire istruzioni dettagliate e includere i frammenti di codice ma non creare un collegamento a un progetto scaricabile.

## <a name="hello-world-samples"></a>Esempi di Hello world

Negli esempi di questa sezione mostrano le nozioni di base di connettersi a un'istanza di Azure Redis Cache e leggere e scrivere dati nella cache con una vasta gamma di lingue e Redis client.

Nell'esempio [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) viene illustrato come eseguire alcune operazioni di cache mediante il client di .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .

In questo esempio viene illustrato come:

-   Utilizzare diverse opzioni di connessione
-   Leggere e scrivere oggetti da e verso la cache utilizzando operazioni icona del e asincrone
-   Utilizzare i comandi MGET/MSET Redis per restituire valori delle chiavi specificate
-   Eseguire operazioni di transazione Redis
-   Lavorare con gli elenchi di Redis e set ordinato
-   Archiviare gli oggetti .NET con serializzatori JsonConvert
-   Utilizzare set Redis per implementare tagging
-   Lavorare con i Cluster Redis

Per ulteriori informazioni, vedere la documentazione [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) su github e per altri scenari di utilizzo, vedere [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests) unit test.

[Come usare Azure Redis Cache con Python](cache-python-get-started.md) viene illustrato come iniziare a utilizzare Azure Redis Cache utilizzando Python e il client [redis copia](https://github.com/andymccurdy/redis-py) .

[Rilevare oggetti .NET nella cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) Mostra un modo per serializzare oggetti .NET in modo che è possibile scrivere inserirle e leggerle da un'istanza di Azure Redis Cache. 

## <a name="use-redis-cache-as-a-scale-out-backplane-for-aspnet-signalr"></a>Utilizzare Redis Cache come scalabilità base per SignalR ASP.NET

Nell'esempio di [Utilizzare Redis Cache come scalabilità base per ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) viene illustrato come è possibile utilizzare Azure Redis Cache come una base SignalR. Per ulteriori informazioni sulla base, vedere [Scalabilità SignalR con Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="redis-cache-customer-query-sample"></a>Esempio di query cliente Cache redis

In questo esempio viene confronta le prestazioni tra accesso ai dati da una cache e accesso ai dati dallo spazio di archiviazione persistenza. In questo esempio include due progetti.

-   [Demo Redis Cache come migliorare le prestazioni, la memorizzazione nella cache dei dati](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-   [Inizio del Database e Cache per la demo](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Lo stato sessione ASP.NET e la cache di Output

Nell'esempio di [Utilizzare Azure Redis Cache per archiviare SessionState ASP.NET e OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) viene illustrato come è possibile utilizzare Azure Redis Cache per archiviare la sessione ASP.NET e Cache di Output mediante il provider di SessionState e OutputCache per Redis.

## <a name="manage-azure-redis-cache-with-maml"></a>Gestione Cache Redis Azure con MAML

Nell'esempio [gestire Azure Redis Cache utilizzando le raccolte di gestione di Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) viene illustrato come è possibile utilizzare le raccolte di gestione di Azure per gestire - (creare / aggiornare / eliminare) la Cache. 

## <a name="custom-monitoring-sample"></a>Esempio di monitoraggio personalizzata

Nell'esempio di [Access Redis il monitoraggio della Cache dei dati](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) viene illustrato come accedere monitoraggio dei dati per la Cache Redis Azure all'esterno di portale di Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Un duplicato Twitter stile scritto utilizzando PHP e Redis

L'esempio [Retwis](https://github.com/SyntaxC4-MSFT/retwis) è il Redis Hello World. È un duplicato di social network Twitter stile minimo scritto utilizzando Redis e PHP utilizzando il client [Predis](https://github.com/nrk/predis) . Il codice sorgente è progettato per essere molto semplice e allo stesso tempo per visualizzare diverse Redis strutture di dati.

## <a name="bandwidth-monitor"></a>Monitor della larghezza di banda

Nell'esempio [monitor della larghezza di banda](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) consente di controllare la larghezza di banda sul client. Misurare la larghezza di banda, eseguire l'esempio nel computer client cache, effettuare chiamate alla cache e osservare la larghezza di banda segnalato dall'esempio monitor della larghezza di banda.
