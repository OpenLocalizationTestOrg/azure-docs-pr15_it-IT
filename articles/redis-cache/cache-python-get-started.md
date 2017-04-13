<properties
    pageTitle="Utilizzo della Cache Redis Azure con Python | Microsoft Azure"
    description="Guida introduttiva di Azure Redis Cache utilizzando Python"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/16/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-python"></a>Utilizzo della Cache Redis Azure con Python

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

In questo argomento viene illustrato come iniziare a utilizzare Azure Redis Cache utilizzando Python.


## <a name="prerequisites"></a>Prerequisiti

Installare [redis copia](https://github.com/andymccurdy/redis-py).


## <a name="create-a-redis-cache-on-azure"></a>Creare una cache Redis su Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Recuperare i tasti di accesso o nome host

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-the-non-ssl-endpoint"></a>Abilitare l'endpoint non SSL

Alcuni client Redis non supportano SSL e per impostazione predefinita, [la porta SSL non è disattivata per le nuove istanze di Azure Redis Cache](cache-configure.md#access-ports). Al momento della loro client [redis copia](https://github.com/andymccurdy/redis-py) non supporta SSL. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## <a name="add-something-to-the-cache-and-retrieve-it"></a>Aggiungere un elemento alla cache e recuperarla


    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'


Sostituire `<name>` con il nome della cache e `key` con il tasto di scelta.


<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
