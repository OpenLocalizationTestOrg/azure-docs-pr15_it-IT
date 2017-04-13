<properties
    pageTitle="Provider di Cache della cache di Output ASP.NET"
    description="Informazioni su come memorizzare nella cache di Output delle pagine ASP.NET utilizzando la Cache Redis Azure"
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
    ms.date="09/27/2016"
    ms.author="sdanie" />

# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>Provider di Cache di Output ASP.NET per Azure Redis Cache

Redis Provider della Cache di Output è un meccanismo di fuori del processo di archiviazione dei dati nella cache di output. Questi dati sono specifico per le risposte HTTP complete (pagina la cache di output). Il provider di collega il nuovo output cache extensibility punto di provider che è stata introdotta in ASP.NET 4.

Per utilizzare il Provider di Cache di Output Redis, configurare prima di tutto la cache e quindi configurare l'applicazione ASP.NET utilizzando il pacchetto Redis Output Cache Provider NuGet. In questo argomento vengono fornite istruzioni sulla configurazione dell'applicazione per utilizzare il Provider di Cache di Output Redis. Per ulteriori informazioni sulla creazione e configurazione di un'istanza di Azure Redis Cache, vedere [creare una cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Archiviare output delle pagine ASP.NET nella cache

Per configurare un'applicazione client mediante il pacchetto Redis Output Cache Provider NuGet Visual Studio, fare clic sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**.

![Azure Redis Cache gestire pacchetti NuGet](./media/cache-aspnet-output-cache-provider/redis-cache-manage-nuget-menu.png)

Digitare **RedisOutputCacheProvider** nella casella di testo di ricerca, selezionare tra i risultati e fare clic su **Installa**.

![Azure Redis Provider della Cache di Output Cache](./media/cache-aspnet-output-cache-provider/redis-cache-page-output-provider.png)

Il pacchetto Redis Output Cache Provider NuGet dipende il pacchetto StackExchange.Redis.StrongName. Se non è presenta nel progetto che verrà installato il pacchetto StackExchange.Redis.StrongName. Si noti che oltre il pacchetto di StackExchange.Redis.StrongName sicuro è disponibile anche la versione non-sicuro StackExchange.Redis. Se il progetto utilizza la versione di StackExchange.Redis non-sicuro, è necessario disinstallare prima o dopo l'installazione del pacchetto Redis Output Cache Provider NuGet, in caso contrario è verrà visualizzato conflitti di nome di un progetto. Per ulteriori informazioni su questi pacchetti, vedere [configurare .NET cache client](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Pacchetto NuGet download e aggiunge i riferimenti di assembly richiesto e la sezione seguente nel file config che contiene la configurazione necessaria per l'applicazione ASP.NET per utilizzare il Provider di Cache di Output Redis.

    <caching>
      <outputCachedefault Provider="MyRedisOutputCache">
        <providers>
          <!--
          <add name="MyRedisOutputCache"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
          -->
          <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
        </providers>
      </outputCache>
    </caching>

La sezione commentata viene fornito un esempio degli attributi e impostazioni di esempio per ogni attributo.

Configurare gli attributi con i valori del blade cache nel portale di Microsoft Azure e configurare gli altri valori in base alle esigenze. Per istruzioni sull'accesso le proprietà della cache, vedere [configurare Redis le impostazioni della cache](cache-configure.md#configure-redis-cache-settings).

-   **host** : specificare il punto finale della cache.
-   **porta** : usare la porta non SSL o il SSL la porta, a seconda delle impostazioni ssl.
-   **accessKey** : utilizzare la chiave primaria o secondaria per la cache.
-   **ssl** -true se si desidera rendere sicure le comunicazioni cache/client con ssl; in caso contrario false. Assicurarsi di specificare la porta corretta.
    -   La porta SSL non è disattivata per impostazione predefinita per nuove cache. Specificare true per questa impostazione per utilizzare la porta SSL. Per ulteriori informazioni sull'attivazione della porta non SSL, vedere la sezione di [Accesso a porte](cache-configure.md#access-ports) nella sezione [Configura una cache](cache-configure.md) .
-   **ID** -specificato il database da utilizzare per i dati di output nella cache. Se non viene specificato, viene utilizzato il valore predefinito di 0.
-   **NomeApplicazione** – chiavi sono archiviate in redis come <AppName>_<SessionId>Data. In questo modo più applicazioni di condividere la stessa chiave. Questo parametro è facoltativo e se non si forniscono viene utilizzato un valore predefinito.
-   **connectionTimeoutInMilliseconds** : questa impostazione consente di ignorare l'impostazione connectTimeout nel client StackExchange.Redis. Se non viene specificato, viene utilizzata l'impostazione di connectTimeout di 5000. Per ulteriori informazioni, vedere [il modello di configurazione StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
-   **operationTimeoutInMilliseconds** : questa impostazione consente di ignorare l'impostazione syncTimeout nel client StackExchange.Redis. Se non viene specificato, viene utilizzata l'impostazione di syncTimeout di 1000. Per ulteriori informazioni, vedere [il modello di configurazione StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Aggiungere un'OutputCache a ogni pagina per il quale si desidera memorizzare nella cache di output.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

In questo esempio il pagina memorizzata nella cache dati rimarranno nella cache per 60 secondi e una versione diversa di pagina nella cache per ogni combinazione di parametro. Per ulteriori informazioni sull'OutputCache, vedere [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837).

Una volta che vengono eseguite le operazioni seguenti, l'applicazione è configurata per utilizzare il Provider di Cache di Output Redis.

## <a name="next-steps"></a>Passaggi successivi

Estrarre il [Provider di stato sessione ASP.NET per Azure Redis Cache](cache-aspnet-session-state-provider.md).
