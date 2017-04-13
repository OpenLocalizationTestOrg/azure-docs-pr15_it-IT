<properties
    pageTitle="Rete CDN criterio nell'estensione servizi multimediali di cache"
    description="In questo argomento fornisce una panoramica di una rete CDN criterio nell'estensione servizi multimediali di cache."
    services="media-services,cdn"
    documentationCenter=".NET"
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="juliako"/>
 
#<a name="cdn-caching-policy-in-media-services-extension"></a>Rete CDN criterio nell'estensione servizi multimediali di cache

Servizi multimediali di Azure fornisce basata su HTTP Streaming adattata e download graduale. Il flusso in base a HTTP è scalabilità con vantaggi della memorizzazione nella cache proxy e livelli di rete CDN, nonché la cache sul lato client. Streaming endpoint offre funzionalità trasmissione generale e anche configurazione per le intestazioni di cache HTTP. I punti finali flusso imposta il controllo della Cache HTTP: max-age e intestazioni di scadenza. È possibile ottenere altre informazioni per le intestazioni di cache HTTP da [w3](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

##<a name="default-caching-headers"></a>Intestazioni predefinite memorizzazione nella cache

Per impostazione predefinita punti finali flusso applicano le intestazioni di cache del giorno 3 per dati flussi su richiesta (frammenti di elementi multimediali effettivo/blocchi) e manifest(playlist). Per lo streaming live, i punti finali flussi applicano le intestazioni di cache del giorno 3 per i dati (frammenti di elementi multimediali effettivo/blocchi) e 2 secondi nella cache intestazione per le richieste di manifest(playlist). Quando si trasforma programma live in (archivio live) su richiesta quindi applicano intestazioni cache flusso su richiesta.

##<a name="azure-cdn-integration"></a>Integrazione di rete CDN Azure

Servizi multimediali di Azure fornisce [CDN integrato](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) per gli endpoint streaming. Intestazioni dei controlli di cache applica nello stesso modo come i punti finali flussi al CDN abilitato i punti finali flussi. Azure ottimali CDN streaming endpoint configurato valori cache per definire la durata degli oggetti internamente memorizzati nella cache e anche questo valore viene utilizzato per impostare il recapito intestazioni cache. Quando usare CDN Abilita i punti finali flussi non è consigliabile impostare i valori della cache è ridotta. L'impostazione di valori più bassi verrà riduzione delle prestazioni e ridurre il vantaggio di rete CDN. Non è consentito impostare intestazioni cache minori di 600 secondi per CDN abilitato i punti finali flussi.

>[AZURE.IMPORTANT] Integrazione di servizi multimediali Azure con Azure CDN è implementata nella **Rete CDN di Azure da Verizon**.  Se si desidera utilizzare **CDN Azure da Akamai** per servizi multimediali di Windows Azure, è necessario [configurare manualmente il punto finale](cdn-create-new-endpoint.md).  Per ulteriori informazioni sulle funzionalità di rete CDN di Azure, vedere la [Panoramica CDN](cdn-overview.md).

##<a name="configuring-cache-headers-with-azure-media-services"></a>Configurazione delle intestazioni di cache con servizi multimediali di Windows Azure

Per configurare i valori di intestazione della cache, è possibile usare il portale di gestione di Azure o API dei servizi multimediali di Azure.

1. Per configurare le intestazioni di cache mediante Gestione portale fare riferimento alla sezione [come gestire i punti finali flusso](../media-services/media-services-portal-manage-streaming-endpoints.md) la configurazione dell'Endpoint Streaming.
2. API REST, [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl)dei servizi Media Azure.
3. Servizi multimediali Azure di Microsoft .NET SDK [StreamingEndpointCacheControl proprietà](http://go.microsoft.com/fwlink/?LinkId=615302).

##<a name="cache-configuration-precedence-order"></a>Ordine di precedenza di configurazione della cache

1. Valore della cache di servizi di supporto configurati Azure esegue l'override di valore predefinito.
2. Se non c'è alcun configurazione manuale, si applica valori predefiniti.
3. Dalla cache di 2 secondi predefinito intestazioni si applica al live streaming manifest(playlist) indipendentemente dalla configurazione Azure Media o lo spazio di archiviazione di Azure e si esegue l'override di questo valore non è disponibile.
