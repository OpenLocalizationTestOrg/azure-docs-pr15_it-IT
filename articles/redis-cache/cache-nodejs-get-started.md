<properties
    pageTitle="Utilizzo della Cache Redis Azure con Node | Microsoft Azure"
    description="Guida introduttiva a Azure Redis Cache utilizzando Node e node_redis."
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>Utilizzo della Cache Redis Azure con Node

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis Cache consente di accedere a una cache Redis sicura e dedicata, gestita da Microsoft. La cache è possibile accedere da qualsiasi applicazione all'interno di Microsoft Azure.

In questo argomento viene illustrato come iniziare a utilizzare Azure Redis Cache utilizzando Node. Per un altro esempio dell'utilizzo della Cache Redis Azure con Node, vedere [creare un'applicazione di Chat Node con Socket.IO su un sito Web Azure](../app-service-web/web-sites-nodejs-chat-app-socketio.md).


## <a name="prerequisites"></a>Prerequisiti

Installare [node_redis](https://github.com/mranney/node_redis):

    npm install redis

In questa esercitazione utilizza [node_redis](https://github.com/mranney/node_redis). Per esempi dell'uso di altri client Node, vedere la documentazione singoli per i client Node elencati [Node Redis client](http://redis.io/clients#nodejs).

## <a name="create-a-redis-cache-on-azure"></a>Creare una cache Redis su Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Recuperare i tasti di accesso o nome host

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Connettersi alla cache utilizzando sicuro SSL

Le build più recente di [node_redis](https://github.com/mranney/node_redis) forniscono supporto per la connessione a Azure Redis Cache tramite SSL. Nell'esempio seguente viene illustrato come connettersi a Azure Redis Cache utilizzando l'endpoint SSL di 6380. Sostituire `<name>` con il nome della cache e `<key>` con la chiave primaria o secondaria come descritto nella sezione precedente [recuperare le chiavi di accesso o nome host](#retrieve-the-host-name-and-access-keys) .

     var redis = require("redis");
    
      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});


## <a name="add-something-to-the-cache-and-retrieve-it"></a>Aggiungere un elemento alla cache e recuperarla

Nell'esempio seguente viene illustrato come connettersi a un'istanza di Azure Redis Cache, archiviare e recuperare un elemento dalla cache. Per ulteriori esempi di utilizzo Redis con il client [node_redis](https://github.com/mranney/node_redis) , vedere [http://redis.js.org/](http://redis.js.org/).

     var redis = require("redis");
    
      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});
    
    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });
    
    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

Output:

    OK
    value


## <a name="next-steps"></a>Passaggi successivi

- [Attivare la diagnostica cache](cache-how-to-monitor.md#enable-cache-diagnostics) in modo da poter [monitorare](cache-how-to-monitor.md) l'integrità della cache.
- Leggere ufficiale [Redis documentazione](http://redis.io/documentation).



