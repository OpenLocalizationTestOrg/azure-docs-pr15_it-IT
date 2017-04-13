<properties 
    pageTitle="Come creare e gestire la Cache Redis Azure tramite l'interfaccia della riga di comando di Azure (Azure CLI) | Microsoft Azure" 
    description="Informazioni su come installare CLI Azure in qualsiasi piattaforma, come utilizzare per connettersi al proprio account Azure e come creare e gestire una cache Redis da CLI Azure." 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-command-line-interface-azure-cli"></a>Come creare e gestire la Cache Redis Azure tramite l'interfaccia della riga di comando di Azure (Azure CLI)

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [CLI Azure](cache-manage-cli.md)

CLI Azure è un ottimo modo per gestire l'infrastruttura di Azure da qualsiasi piattaforma. In questo articolo viene illustrato come creare e gestire le istanze di Azure Redis Cache usa CLI Azure.

## <a name="prerequisites"></a>Prerequisiti

Per creare e gestire le istanze di Azure Redis Cache usa CLI Azure, è necessario completare la procedura seguente.

-   È necessario disporre di un account Azure. Se non si dispone di uno, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.
-   [Installare CLI Azure](../xplat-cli-install.md).
-   Connettere l'installazione di Azure CLI con un account Azure personale o con un lavoro o dell'istituto di istruzione account Azure e accedere dal CLI Azure utilizzando il `azure login` comando. Per comprendere le differenze e si sceglie, vedere [connettersi a un abbonamento Azure dall'interfaccia della riga di comando Azure Azure CLI ()](../xplat-cli-connect.md).
-   Prima di eseguire una delle seguenti comandi, passare CLI Azure in modalità di gestione risorse eseguendo la `azure config mode arm` comando. Per ulteriori informazioni, vedere [impostare la modalità di gestione risorse di Azure](../xplat-cli-azure-resource-manager.md#set-the-azure-resource-manager-mode).

## <a name="redis-cache-properties"></a>Redis le proprietà della Cache

Le proprietà seguenti vengono utilizzate durante la creazione e aggiornamento di istanze Redis Cache.

| Proprietà            | Opzione                                  | Descrizione                                                                                                                                                                                                                                          |
|---------------------|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nome                | -n, --nome                              | Nome della Cache Redis.                                                                                                                                                                                                                             |
| gruppo di risorse      | -g, --gruppo di risorse                    | Nome del gruppo di risorse.                                                                                                                                                                                                                          |
| posizione            | -l, --posizione                          | Posizione in cui creare cache.                                                                                                                                                                                                                            |
| dimensioni                | + z, --dimensioni                              | Dimensione della Cache Redis. Valori validi: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]                                                                                                                                                                  |
| SKU                 | -x, - sku                               | Redis SKU. Deve essere uno dei: [base, Standard, Premium]                                                                                                                                                                                             |
| EnableNonSslPort    | -e, - enable-non--porta ssl               | Proprietà EnableNonSslPort della Cache di Redis. Aggiungere il flag se si desidera abilitare la porta SSL Non per la cache                                                                                                                                    |
| Redis configurazione | -c, --configurazione redis               | Redis configurazione. Immettere una stringa in formato JSON di chiavi di configurazione e valori di seguito. Formato: "{" ":""," ":" "}"                                                                                                                                     |
| Redis configurazione | -f, --file di configurazione redis          | Redis configurazione. Immettere il percorso di un file contenente le chiavi di configurazione e i valori di seguito. Formato per la voce del file: {"": "","": ""}                                                                                                                |
| Conteggio condiviso         | -r, --conteggio condiviso                       | Numero di Shards creare in una Cache Cluster Premium con il servizio cluster.                                                                                                                                                                               |
| Rete virtuale     | -v, --rete virtuale                   | Durante l'hosting la cache in un VNET, specifica l'esatta ARM ID risorsa della rete virtuale per distribuire la cache di redis in. Esempio di formato: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| tipo di chiave            | -t, --tipo di chiave                          | Tipo di chiave per il rinnovo. Valori validi: [principale, secondario]                                                                                                                                                                                             |
| StaticIP            | -p, --ip statico < ip statico >             | Durante l'hosting la cache in un VNET, specifica l'indirizzo IP in subnet per la cache. Se non viene specificato, viene effettuata una scelta dell'utente da una subnet.                                                                                                |
| Subnet              | t, --subnet<subnet>                    | Durante l'hosting la cache in un VNET, specifica il nome della subnet in cui si desidera distribuire la cache.                                                                                                                                                    |
| VirtualNetwork      | -v, --rete virtuale <-virtuali > | Durante l'hosting la cache in un VNET, specifica l'esatta ARM ID risorsa della rete virtuale per distribuire la cache di redis in. Esempio di formato: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Abbonamento        | -s, --sottoscrizione                      | Identificatore di sottoscrizione.                                                                                                                                                                                                                         |

## <a name="see-all-redis-cache-commands"></a>Visualizzare tutti i comandi Redis Cache

Per visualizzare tutti i comandi Redis Cache e i relativi parametri, utilizzare la `azure rediscache -h` comando.

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Redis Cache(s)
    help:
    help:    Create a Redis Cache
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Redis Cache
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Redis Caches within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Redis Cache
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Redis Cache
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Redis Cache
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-a-redis-cache"></a>Creare una Cache Redis

Per creare una Cache Redis, utilizzare il comando seguente:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Per ulteriori informazioni su questo comando, eseguire la `azure rediscache create -h` comando.

    C:\>azure rediscache create -h
    help:    Create a Redis Cache
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-redis-cache"></a>Eliminare una Cache Redis esistente

Per eliminare una Cache Redis, usare il comando seguente:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Per ulteriori informazioni su questo comando, eseguire la `azure rediscache delete -h` comando.

    C:\>azure rediscache delete -h
    help:    Delete an existing Redis Cache
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>Elenco di tutte le cache Redis all'interno dell'abbonamento o di un gruppo di risorse

Per visualizzare tutte le cache Redis all'interno dell'abbonamento o di un gruppo di risorse, utilizzare il comando seguente:

    azure rediscache list [options]

Per ulteriori informazioni su questo comando, eseguire la `azure rediscache list -h` comando.

    C:\>azure rediscache list -h
    help:    List all Redis Caches within your Subscription or Resource Group
    help:
    help:    Usage: rediscache list [options]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="show-properties-of-an-existing-redis-cache"></a>Visualizzare le proprietà di una Cache Redis esistente

Per visualizzare le proprietà di una Cache Redis esistente, usare il comando seguente:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Per ulteriori informazioni su questo comando, eseguire la `azure rediscache show -h` comando.

    C:\>azure rediscache show -h
    help:    Show properties of an existing Redis Cache
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>
## <a name="change-settings-of-an-existing-redis-cache"></a>Modificare le impostazioni di una Cache Redis esistente

Per modificare le impostazioni di una Cache Redis esistente, usare il comando seguente:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Per ulteriori informazioni su questo comando, eseguire la `azure rediscache set -h` comando.

    C:\>azure rediscache set -h
    help:    Change settings of an existing Redis Cache
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>Rinnovare la chiave di autenticazione per una Cache Redis esistente

Per rinnovare la chiave di autenticazione per una Cache Redis esistente, usare il comando seguente:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Specificare `Primary` o `Secondary` per `key-type`.

Per ulteriori informazioni su questo comando, eseguire la `azure rediscache renew-key -h` comando.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Redis Cache
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>Tasti di elenco principale e secondaria di una Cache Redis esistente

Elenco primario e secondario tasti di una Cache Redis esistente, usare il comando seguente:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Per ulteriori informazioni su questo comando, eseguire la `azure rediscache list-keys -h` comando.

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
