<properties
   pageTitle="Utilizzo della Cache Redis Azure con Java | Microsoft Azure"
    description="Guida introduttiva di Azure Redis Cache utilizzando Java"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor=""/>

<tags
    ms.service="cache"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/24/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-java"></a>Utilizzo della Cache Redis Azure con Java

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure consente di Cache Redis di accedere a un oggetto dedicato Redis cache gestita da Microsoft. La cache è possibile accedere da qualsiasi applicazione all'interno di Microsoft Azure.

In questo argomento viene illustrato come iniziare a utilizzare Azure Redis Cache utilizzando linguaggio.

## <a name="prerequisites"></a>Prerequisiti

[Jedis](https://github.com/xetorthio/jedis) - client Java per Redis

In questa esercitazione utilizza Jedis, ma è possibile utilizzare qualsiasi client Java elencati [http://redis.io/clients](http://redis.io/clients).

## <a name="create-a-redis-cache-on-azure"></a>Creare una cache Redis su Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Recuperare i tasti di accesso o nome host

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-the-non-ssl-endpoint"></a>Abilitare l'endpoint non SSL

Alcuni client Redis non supportano SSL e per impostazione predefinita, [la porta SSL non è disattivata per le nuove istanze di Azure Redis Cache](cache-configure.md#access-ports). Al momento della loro client [Jedis](https://github.com/xetorthio/jedis) non supporta SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]




## <a name="add-something-to-the-cache-and-retrieve-it"></a>Aggiungere un elemento alla cache e recuperarla

    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    /* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
    public class App
    {
      public static void main( String[] args )
      {
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>Passaggi successivi

- [Attivare la diagnostica cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) in modo da poter [monitorare](https://msdn.microsoft.com/library/azure/dn763945.aspx) l'integrità della cache.
- Leggere ufficiale [Redis documentazione](http://redis.io/documentation).

