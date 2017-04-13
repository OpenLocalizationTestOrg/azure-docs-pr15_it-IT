<properties
    pageTitle="Gestire la Cache Redis Azure con PowerShell Azure | Microsoft Azure"
    description="Informazioni su come eseguire attività amministrative per la Cache Redis Azure tramite PowerShell Azure."
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
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="manage-azure-redis-cache-with-azure-powershell"></a>Gestire la Cache Redis Azure con PowerShell Azure

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [CLI Azure](cache-manage-cli.md)

In questo argomento viene si come eseguire attività comuni come creare, aggiornare e ridimensionare le istanze di Azure Redis Cache, come rigenerare i tasti di scelta e su come visualizzare informazioni delle cache. Per un elenco completo dei cmdlet di Azure Redis Cache PowerShell, vedere [cmdlet per la Cache Redis Azure](https://msdn.microsoft.com/library/azure/mt634513.aspx).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)][modello classico](../resource-manager-deployment-model.md#classic-deployment-characteristics).

## <a name="prerequisites"></a>Prerequisiti

Se è già stato installato PowerShell di Azure, è necessario disporre di PowerShell Azure versione 1.0.0 o versione successiva. È possibile controllare la versione di PowerShell Azure installati con questo comando al prompt dei comandi PowerShell di Azure.

    Get-Module azure | format-table version

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Prima di tutto, è necessario accedere a Azure con questo comando.

    Login-AzureRmAccount

Specificare l'indirizzo di posta elettronica del proprio account Azure e la relativa password nella finestra di accesso Microsoft Azure.

Successivamente, se si hanno più abbonamenti Azure, è necessario impostare l'abbonamento Azure. Per visualizzare un elenco delle sottoscrizioni corrente, eseguire il comando.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Per specificare la sottoscrizione, eseguire il comando seguente. Nell'esempio seguente è il nome dell'abbonamento `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Prima di poter utilizzare Windows PowerShell Gestione risorse Azure, è necessario quanto segue:

- Windows PowerShell, versione 3.0 o 4.0. Per trovare la versione di Windows PowerShell, digitare:`$PSVersionTable` e verificare che il valore di `PSVersion` è 3.0 o 4.0. Per installare una versione compatibile, vedere [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Per ottenere informazioni dettagliate per tutti i cmdlet che viene visualizzato in questa esercitazione, utilizzare il cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Ad esempio, per ottenere assistenza per il `New-AzureRmRedisCache` cmdlet, tipo:

    Get-Help New-AzureRmRedisCache -Detailed

### <a name="how-to-connect-to-azure-government-cloud-or-azure-china-cloud"></a>Connessione al Cloud per enti pubblici Azure o Azure Cina Cloud

Per impostazione predefinita di Azure ambiente è `AzureCloud`, che rappresenta l'istanza di area Azure globale. Per connettersi a un'altra istanza, utilizzare la `Add-AzureRmAccount` comando con il `-Environment` o -`EnvironmentName` opzione della riga di comando con l'ambiente desiderato o il nome di ambiente.

Per visualizzare l'elenco di ambienti disponibili, eseguire la `Get-AzureRmEnvironment` cmdlet.

### <a name="to-connect-to-the-azure-government-cloud"></a>Per la connessione al Cloud per enti pubblici Azure

Per connettersi al Cloud per enti pubblici Azure, utilizzare uno dei comandi seguenti.

    Add-AzureRMAccount -EnvironmentName AzureUSGovernment

o

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

Per creare una cache nel Cloud per enti pubblici Azure, utilizzare uno dei seguenti percorsi.

-   USGov Virginia
-   USGov Iowa

Per ulteriori informazioni su Azure Government Cloud, vedere [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) e [Guida di sviluppo di Microsoft Azure per enti pubblici](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Per la connessione al Cloud Cina Azure

Per connettersi al Cloud Cina Azure, utilizzare uno dei comandi seguenti.

    Add-AzureRMAccount -EnvironmentName AzureChinaCloud

o

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

Per creare una cache nel Cloud Cina Azure, utilizzare uno dei seguenti percorsi.

-   Cina est
-   America del Nord Cina

Per ulteriori informazioni su Cloud Cina Azure, vedere [AzureChinaCloud per Azure gestito da 21Vianet in Cina](http://www.windowsazure.cn/).

### <a name="properties-used-for-azure-redis-cache-powershell"></a>Proprietà utilizzate per Azure Redis Cache PowerShell

Nella tabella seguente contiene proprietà e le descrizioni dei parametri usati di frequente durante la creazione e gestione delle istanze di Azure Redis Cache tramite PowerShell Azure.

| Parametro          | Descrizione                                                                                                                                                                                                        | Impostazione predefinita  |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| Nome               | Nome della cache                                                                                                                                                                                                  |          |
| Posizione           | Percorso della cache                                                                                                                                                                                              |          |
| ResourceGroupName  | Nome gruppo di risorse in cui si desidera creare la cache                                                                                                                                                                   |          |
| Dimensioni               | Le dimensioni della cache. I valori validi sono: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2,5 GB, 6 GB, 13 GB, 26 GB, 53 GB                                                                     | ALMENO 1GB      |
| ShardCount         | Il numero di shards per creare durante la creazione di una cache premium con il servizio cluster abilitato. I valori validi sono: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10                                                                                                      |          |
| SKU                | Specifica la SKU della cache. I valori validi sono: base, Standard, Premium                                                                                                                                         | Standard |
| RedisConfiguration | Consente di specificare le impostazioni di configurazione Redis. Per informazioni dettagliate su ognuna delle impostazioni, vedere [proprietà RedisConfiguration](#redisconfiguration-properties) nella tabella seguente. |          |
| EnableNonSslPort   | Indica se la porta SSL non è abilitata.                                                                                                                                                                     | FALSO    |
| MaxMemoryPolicy    | Questo parametro è obsoleto: utilizzare invece RedisConfiguration.                                                                                                                                              |          |
| StaticIP           | Durante l'hosting la cache in un VNET, specifica l'indirizzo IP in subnet per la cache. Se non viene specificato, viene effettuata una scelta dell'utente da una subnet.                                                                                                                     |          |
| Subnet             | Durante l'hosting la cache in un VNET, specifica il nome della subnet in cui si desidera distribuire la cache.                                                                                                                  |          |
| VirtualNetwork     | Durante l'hosting la cache in un VNET, specifica l'ID risorsa della VNET in cui si desidera distribuire la cache.                                                                                                             |          |
| KeyType            | Specifica il tasto di scelta per rigenerare quando il rinnovo i tasti di scelta. I valori validi sono: principale, secondario |  |                                                                                                                                                                                                              |          |


### <a name="redisconfiguration-properties"></a>Proprietà RedisConfiguration

| Proprietà                      | Descrizione                                                                                                          | Prezzi livelli       |
|-------------------------------|----------------------------------------------------------------------------------------------------------------------|---------------------|
| copia di backup RDB abilitato            | Se è abilitata [Redis persistenza dei dati](cache-how-to-premium-persistence.md)                                     | Solo Premium        |
| spazio di archiviazione di RDB-stringa di connessione | La stringa di connessione all'account di archiviazione per [Redis persistenza dei dati](cache-how-to-premium-persistence.md)       | Solo Premium        |
| frequenza di backup RDB          | Frequenza di backup per [Redis persistenza dei dati](cache-how-to-premium-persistence.md)                               | Solo Premium        |
| MaxMemory riservato            | Consente di configurare la [memoria riservata](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) per i processi non cache | Standard e Premium |
| criteri di MaxMemory              | Configura i [criteri di eliminazione](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) per la cache           | Tutti i livelli prezzi   |
| notifica eventi di spazio delle chiavi        | Consente di configurare [le notifiche di spazio delle chiavi](cache-configure.md#keyspace-notifications-advanced-settings)                     | Standard e Premium |
| numero massimo di hash-voci di ziplist      | Configura [ottimizzazione della memoria](http://redis.io/topics/memory-optimization) per i tipi di piccole dimensioni aggregare i dati          | Standard e Premium |
| valore ziplist di max hash        | Configura [ottimizzazione della memoria](http://redis.io/topics/memory-optimization) per i tipi di piccole dimensioni aggregare i dati          | Standard e Premium |
| numero massimo di set-voci di intset        | Configura [ottimizzazione della memoria](http://redis.io/topics/memory-optimization) per i tipi di piccole dimensioni aggregare i dati          | Standard e Premium |
| numero massimo di zset-voci di ziplist      | Configura [ottimizzazione della memoria](http://redis.io/topics/memory-optimization) per i tipi di piccole dimensioni aggregare i dati          | Standard e Premium |
| valore ziplist di max zset        | Configura [ottimizzazione della memoria](http://redis.io/topics/memory-optimization) per i tipi di piccole dimensioni aggregare i dati          | Standard e Premium |
| database                     | Configura il numero di database. Solo al momento della creazione della cache, è possibile configurare questa proprietà.                          | Standard e Premium |

## <a name="to-create-a-redis-cache"></a>Per creare una Cache Redis

Vengono create nuove istanze di Azure Redis Cache utilizzando il cmdlet [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) .

>[AZURE.IMPORTANT] La prima volta che si crea una cache Redis in una sottoscrizione tramite il portale di Azure, il portale registra il `Microsoft.Cache` spazio dei nomi per la sottoscrizione. Se si tenta di creare la cache di Redis prima di una sottoscrizione tramite PowerShell, è prima necessario registrare tale spazio dei nomi utilizzando il comando seguente. in caso contrario cmdlet come `New-AzureRmRedisCache` e `Get-AzureRmRedisCache` esito negativo.
>
>`Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`

Per visualizzare un elenco dei parametri disponibili e le relative descrizioni `New-AzureRmRedisCache`, eseguire il comando seguente.

    PS C:\> Get-Help New-AzureRmRedisCache -detailed
    
    NAME
        New-AzureRmRedisCache
    
    SYNOPSIS
        Creates a new redis cache.
    
    
    SYNTAX
        New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]
    
    
    DESCRIPTION
        The New-AzureRmRedisCache cmdlet creates a new redis cache.
    
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to create.
    
        -ResourceGroupName <String>
            Name of resource group in which to create the redis cache.
    
        -Location <String>
            Location in which to create the redis cache.
    
        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.
    
        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
    
        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
    
        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
    
        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.
    
        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.
    
        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.
    
        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}
    
        -Subnet <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.
    
        -StaticIP <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Per creare una cache con parametri predefiniti, eseguire il comando seguente.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, e `Location` sono parametri obbligatori, ma il resto sono facoltativo e includono valori predefiniti. Eseguire il comando precedente crea un'istanza di Cache Redis Azure SKU Standard con il nome specificato, posizione e gruppo di risorse, che almeno 1 GB di dimensioni con la porta non SSL disabilitata.

Per creare una cache premium, specificare la dimensione di P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26-260 GB in), o P4 (53-530 GB in). Per abilitare il servizio cluster, specificare un conteggio di condiviso mediante la `ShardCount` parametro. Nell'esempio seguente viene creata una cache premium P1 con 3 shards. Una P1 premium cache 6 GB dimensioni, dal momento che sono state specificate tre shards la dimensione totale è 18 GB (3 x 6 GB).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Per specificare i valori per il `RedisConfiguration` parametro, racchiudere i valori all'interno di `{}` come chiave/valore come coppie di parole `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. Nell'esempio seguente viene creata una cache almeno 1 GB standard con `allkeys-random` le notifiche di criteri e spazio delle chiavi maxmemory configurate con `KEA`. Per ulteriori informazioni, vedere [notifiche spazio delle chiavi (impostazioni avanzate)](cache-configure.md#keyspace-notifications-advanced-settings) e [Maxmemory criteri e riservato maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>
## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Per configurare i database dell'impostazione durante la creazione della cache

Il `databases` solo durante la creazione della cache, è possibile configurare l'impostazione. Nell'esempio seguente viene creato un premio P3 cache (26 GB) con 48 database utilizzando il cmdlet [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) .

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Per ulteriori informazioni sulla `databases` proprietà, vedere [configurazione di server predefinito Azure Redis Cache](cache-configure.md#default-redis-server-configuration). Per ulteriori informazioni sulla creazione di una cache utilizzando il cmdlet [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) , vedere la sezione precedente [per creare una Cache Redis](#to-create-a-redis-cache) .

## <a name="to-update-a-redis-cache"></a>Per aggiornare una cache Redis

Azure istanze di Cache Redis vengono aggiornate tramite il cmdlet [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) .

Per visualizzare un elenco dei parametri disponibili e le relative descrizioni `Set-AzureRmRedisCache`, eseguire il comando seguente.

    PS C:\> Get-Help Set-AzureRmRedisCache -detailed
    
    NAME
        Set-AzureRmRedisCache
    
    SYNOPSIS
        Set redis cache updatable parameters.
    
    SYNTAX
        Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]
    
    DESCRIPTION
        The Set-AzureRmRedisCache cmdlet sets redis cache parameters.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to update.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
    
        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
    
        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
    
        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.
    
        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.
    
        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Il `Set-AzureRmRedisCache` cmdlet può essere utilizzato per aggiornare le proprietà, ad esempio `Size`, `Sku`, `EnableNonSslPort`e la `RedisConfiguration` valori. 

Il comando Aggiorna i criteri di maxmemory per la Cache Redis denominato myCache.

    Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>
## <a name="to-scale-a-redis-cache"></a>Per ridimensionare una cache Redis

`Set-AzureRmRedisCache`può essere utilizzato per ridimensionare una cache Azure Redis istanza quando il `Size`, `Sku`, o `ShardCount` si modificano le proprietà. 

>[AZURE.NOTE]Ridimensionamento di una cache tramite PowerShell è soggetto gli stessi limiti e alle linee guida di ridimensionamento di una cache dal portale di Azure. È possibile ridimensionare a un altro livello prezzo con le restrizioni seguenti.
>
>-  Non è possibile ridimensionare da un livello di prezzo superiore a un livello inferiore prezzo.
>    -    Non è possibile ridimensionare da una cache **Premium** per difetto a una **Standard** o una cache di **base** .
>    -    Non è possibile ridimensionare da una cache **Standard** per difetto a una cache di **base** .
>-  È possibile adattare da una cache di **base** a una cache **Standard** , ma non è possibile modificare le dimensioni nello stesso momento. Se è necessaria una dimensione diversa, è possibile eseguire un'operazione di ridimensionamento successiva alla dimensione desiderata.
>-  Da una **base** cache non è possibile ridimensionare direttamente a una cache **Premium** . È necessario ridimensionare di **base** su **Standard** in un'operazione di ridimensionamento e quindi da **Standard** a **Premium** in un'operazione di ridimensionamento successiva.
>-  Non è possibile ridimensionare da una dimensione maggiore verso il basso per il **C0 (250 MB)** dimensioni.
>
>Per ulteriori informazioni, vedere [come scala Azure Redis Cache](cache-how-to-scale.md).

Nell'esempio seguente viene illustrato come ridimensionare una cache denominata `myCache` a una cache 2,5 GB. Si noti che questo comando funziona per base o una cache Standard.

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Dopo che questo comando, viene restituito lo stato della cache (simile alla chiamata `Get-AzureRmRedisCache`). Si noti che la `ProvisioningState` è `Scaling`.

    PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB
    
    
    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

Al termine, l'operazione di ridimensionamento di `ProvisioningState` assume la forma di `Succeeded`. Se è necessario effettuare un'operazione di ridimensionamento successiva, ad esempio la modifica di base a Standard e quindi modificare le dimensioni, è necessario attendere fino a quando l'operazione precedente è stata completata o si riceve un messaggio di errore analogo al seguente.

    Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-a-redis-cache"></a>Per ottenere informazioni su una cache Redis

È possibile recuperare informazioni su una cache utilizzando il cmdlet [Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx) .

Per visualizzare un elenco dei parametri disponibili e le relative descrizioni `Get-AzureRmRedisCache`, eseguire il comando seguente.

    PS C:\> Get-Help Get-AzureRmRedisCache -detailed
    
    NAME
        Get-AzureRmRedisCache
    
    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.
    
    SYNTAX
        Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]
    
    DESCRIPTION
        The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
        specific cache name provided.
    
        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.
    
        If no parameters are given than it will return details about all caches the current subscription.
    
    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
            returns the details for the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Per restituire informazioni su tutte le cache di abbonamento corrente, eseguire `Get-AzureRmRedisCache` senza parametri.

    Get-AzureRmRedisCache

Per restituire informazioni su tutte le cache di un gruppo di risorse specifico, eseguire `Get-AzureRmRedisCache` con la `ResourceGroupName` parametro.

    Get-AzureRmRedisCache -ResourceGroupName myGroup

Per restituire le informazioni di una cache specifica, eseguire `Get-AzureRmRedisCache` con la `Name` parametro contenente il nome della cache e la `ResourceGroupName` parametro con il gruppo di risorse contenente la cache.

    PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
    
    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-a-redis-cache"></a>Per recuperare i tasti di scelta per una cache Redis

Per recuperare i tasti di scelta per la cache, è possibile utilizzare il cmdlet [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx) .

Per visualizzare un elenco dei parametri disponibili e le relative descrizioni `Get-AzureRmRedisCacheKey`, eseguire il comando seguente.

    PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed
    
    NAME
        Get-AzureRmRedisCacheKey
    
    SYNOPSIS
        Gets the accesskeys for the specified redis cache.
    
    
    SYNTAX
        Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]
    
    DESCRIPTION
        The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Per recuperare le chiavi per la cache, chiamare il `Get-AzureRmRedisCacheKey` cmdlet e passare il nome nella cache il nome del gruppo di risorse contenente la cache.

    PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup
    
    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-redis-cache"></a>Rigenerare i tasti di scelta per la cache Redis

Per rigenerare i tasti di scelta per la cache, è possibile utilizzare il cmdlet [New-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634512.aspx) .

Per visualizzare un elenco dei parametri disponibili e le relative descrizioni `New-AzureRmRedisCacheKey`, eseguire il comando seguente.

    PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed
    
    NAME
        New-AzureRmRedisCacheKey
    
    SYNOPSIS
        Regenerates the access key of a redis cache.
    
    SYNTAX
        New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]
    
    DESCRIPTION
        The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.
    
        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    
Per rigenerare la chiave primaria o secondaria per la cache, chiamare il `New-AzureRmRedisCacheKey` cmdlet e passare nel nome del gruppo di risorse e specificare `Primary` o `Secondary` per il `KeyType` parametro. Nell'esempio seguente, il tasto di scelta secondario per una cache rigenerazione.

    PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary
    
    Confirm
    Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    
    
    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-a-redis-cache"></a>Per eliminare una cache Redis

Per eliminare una cache Redis, utilizzare il cmdlet [AzureRmRedisCache Rimuovi](https://msdn.microsoft.com/library/azure/mt634515.aspx) .

Per visualizzare un elenco dei parametri disponibili e le relative descrizioni `Remove-AzureRmRedisCache`, eseguire il comando seguente.

    PS C:\> Get-Help Remove-AzureRmRedisCache -detailed
    
    NAME
        Remove-AzureRmRedisCache
    
    SYNOPSIS
        Remove redis cache if exists.
    
    SYNTAX
        Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>
    
    DESCRIPTION
        The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to remove.
    
        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.
    
        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.
    
        -PassThru
            By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Nell'esempio seguente la cache denominata `myCache` viene rimosso.

    PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
    
    Confirm
    Are you sure you want to remove redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-a-redis-cache"></a>Importare una cache Redis

È possibile importare dati in un'istanza di Azure Redis Cache con la `Import-AzureRmRedisCache` cmdlet.

>[AZURE.IMPORTANT] Importazione/esportazione è disponibile solo per [livello premium](cache-premium-tier-intro.md) cache. Per ulteriori informazioni sull'importazione/esportazione, vedere [importare ed esportare i dati nella Cache Redis Azure](cache-how-to-import-export-data.md).

Per visualizzare un elenco dei parametri disponibili e le relative descrizioni `Import-AzureRmRedisCache`, eseguire il comando seguente.

    PS C:\> Get-Help Import-AzureRmRedisCache -detailed
    
    NAME
        Import-AzureRmRedisCache
    
    SYNOPSIS
        Import data from blobs to Azure Redis Cache.
    
    
    SYNTAX
        Import-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Import-AzureRmRedisCache cmdlet imports data from the specified blobs into Azure Redis Cache.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.
    
        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.
    
        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.
    
        -PassThru
            By default Import-AzureRmRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzureRmRedisCache returns a boolean value indicating the success of the
            operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    

Il comando Importa dati da blob specificato dall'uri SA nella Cache Redis Azure.


    PS C:\>Import-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-a-redis-cache"></a>Esportare una cache Redis

È possibile esportare dati da un'istanza di Azure Redis Cache con la `Export-AzureRmRedisCache` cmdlet.

>[AZURE.IMPORTANT] Importazione/esportazione è disponibile solo per [livello premium](cache-premium-tier-intro.md) cache. Per ulteriori informazioni sull'importazione/esportazione, vedere [importare ed esportare i dati nella Cache Redis Azure](cache-how-to-import-export-data.md).

Per visualizzare un elenco dei parametri disponibili e le relative descrizioni `Export-AzureRmRedisCache`, eseguire il comando seguente.

    PS C:\> Get-Help Export-AzureRmRedisCache -detailed
    
    NAME
        Export-AzureRmRedisCache
    
    SYNOPSIS
        Exports data from Azure Redis Cache to a specified container.
    
    
    SYNTAX
        Export-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Export-AzureRmRedisCache cmdlet exports data from Azure Redis Cache to a specified container.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -Prefix <String>
            Prefix to use for blob names.
    
        -Container <String>
            SAS url of container where data should be exported.
    
        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.
    
        -PassThru
            By default Export-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzureRmRedisCache returns a boolean value indicating the success of the operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


Il comando seguente consente di esportare i dati da un'istanza di Azure Redis Cache nel contenitore specificato dall'uri SA.


        PS C:\>Export-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-a-redis-cache"></a>Per riavviare una cache Redis

È possibile riavviare l'istanza di Azure Redis Cache utilizzando il `Reset-AzureRmRedisCache` cmdlet.

>[AZURE.IMPORTANT] Riavviare il computer è disponibile solo per [livello premium](cache-premium-tier-intro.md) cache. Per ulteriori informazioni sul riavvio la cache, vedere [amministrazione Cache - riavviare il computer](cache-administration.md#reboot).

Per visualizzare un elenco dei parametri disponibili e le relative descrizioni `Reset-AzureRmRedisCache`, eseguire il comando seguente.

    PS C:\> Get-Help Reset-AzureRmRedisCache -detailed
    
    NAME
        Reset-AzureRmRedisCache
    
    SYNOPSIS
        Reboot specified node(s) of an Azure Redis Cache instance.
    
    
    SYNTAX
        Reset-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Reset-AzureRmRedisCache cmdlet reboots the specified node(s) of an Azure Redis Cache instance.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".
    
        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.
    
        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.
    
        -PassThru
            By default Reset-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzureRmRedisCache returns a boolean value indicating the success of the operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    

Il comando seguente riavvia entrambi i nodi della cache specificata.

    
        PS C:\>Reset-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force
    

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'uso di Windows PowerShell con Azure, vedere le risorse seguenti:

- [Documentazione di cmdlet Redis Cache Azure su MSDN](https://msdn.microsoft.com/library/azure/mt634513.aspx)
- [Cmdlet per la gestione risorse Azure](http://go.microsoft.com/fwlink/?LinkID=394765): informazioni su come utilizzare i cmdlet nel modulo AzureResourceManager.
- [Gruppi di uso risorse per gestire le risorse Azure](../resource-group-template-deploy-portal.md): informazioni su come creare e gestire gruppi di risorse nel portale di Azure.
- [Blog di Azure](http://blogs.msdn.com/windowsazure): informazioni sulle nuove caratteristiche di Azure.
- [Blog di Windows PowerShell](http://blogs.msdn.com/powershell): informazioni sulle nuove caratteristiche di Windows PowerShell.
- ["Buongiorno Scripting Guy!" Blog](http://blogs.technet.com/b/heyscriptingguy/): ottenere suggerimenti reali dalla community di Windows PowerShell.
