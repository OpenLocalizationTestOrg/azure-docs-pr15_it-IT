<properties
 pageTitle="Come gestire la scadenza del contenuto in servizi Cloud/App Web di Windows Azure, ASP.NET e IIS nella rete CDN di Azure | Microsoft Azure"
 description="Viene illustrato come gestire la scadenza del contenuto di servizio cloud in rete CDN di Azure"
 services="cdn"
 documentationCenter=".NET"
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="09/19/2016"
 ms.author="casoper"/>

# <a name="how-to-manage-expiration-of-azure-web-appscloud-services-aspnet-or-iis-content-in-azure-cdn"></a>Come gestire la scadenza del contenuto in servizi Cloud/App Web di Windows Azure, ASP.NET o IIS nella rete CDN di Azure

> [AZURE.SELECTOR]
- [Azure Web App/Cloud Services ASP.NET o IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Servizio di archiviazione blob di Azure](cdn-manage-expiration-of-blob-content.md)

Possono memorizzati nella cache di file da un server web di origine accessibili in rete CDN di Azure fino allo scadere il time to live (TTL).  Il valore TTL viene determinato in base all' [intestazione *Cache-Control* ](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) nella risposta HTTP dal server di origine.  In questo articolo viene descritto come impostare `Cache-Control` intestazioni per Azure Web Apps, servizi Cloud Windows Azure, le applicazioni e siti Internet Information Services, ognuno dei quali sono configurate in modo analogo.

>[AZURE.TIP] È possibile non impostare nessun TTL su un file.  In questo caso, Azure CDN applica automaticamente un TTL predefinito di sette giorni.
>
>Per ulteriori informazioni sul funzionamento di Azure CDN per velocizzare l'accesso ai file e altre risorse, vedere la [Panoramica CDN Azure](./cdn-overview.md).

## <a name="setting-cache-control-headers-in-configuration"></a>Impostazione delle intestazioni dei controlli di Cache nella configurazione

Per contenuto statico, ad esempio immagini e fogli di stile, è possibile controllare la frequenza di aggiornamento modificando i file **applicationHost** o **config** dell'applicazione web.  Consente di impostare l'elemento **system.webServer\staticContent\clientCache** nel file di configurazione di `Cache-Control` intestazione per il contenuto. Per **config**, le impostazioni di configurazione vengono influenzata dalle tutti gli elementi nella cartella e tutte le sottocartelle, a meno che non ignorare il livello di sottocartella.  Ad esempio, è possibile impostare un valore predefinito time to live nella radice di tutto il contenuto statico memorizzati nella cache per 3 giorni ma ha una sottocartella che contiene il contenuto di altre variabile con le impostazioni della cache di 6 ore.  Per **applicationHost**, tutte le applicazioni nel sito avrà effetto sul, ma possono essere sovrascritti in **file delle applicazioni** .

Mostra XML ed esempio di impostazione **clientCache** per specificare una durata massima di 3 giorni seguenti:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Specificare **UseMaxAge** aggiunge un `Cache-Control: max-age=<nnn>` intestazione per la risposta in base al valore specificato nell'attributo **CacheControlMaxAge** . È il formato dell'intervallo di tempo per l'attributo **cacheControlMaxAge** <days>. <hours>:<min>:<sec>. Per ulteriori informazioni sul nodo **clientCache** , vedere [Cache Client <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Impostazione delle intestazioni dei controlli di Cache nel codice

Per le applicazioni ASP.NET, è possibile impostare la rete CDN di comportamento della cache a livello di programmazione impostando la proprietà **HttpResponse** . Per ulteriori informazioni sulla proprietà **HttpResponse** , vedere [Proprietà HttpResponse](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) e [Classe HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Se si desidera memorizzare nella cache il contenuto dell'applicazione in ASP.NET a livello di programmazione, assicurarsi che il contenuto viene contrassegnato come essere memorizzata nella cache mediante l'impostazione HttpCacheability al *pubblico*. Inoltre, assicurarsi che sia impostata una convalida della cache. Convalida della cache può essere rappresentato da una data ultima modifica timestamp impostata chiamando SetLastModified o un valore etag impostare chiamando SetETag. Facoltativamente, è inoltre possibile specificare una scadenza cache chiamando SetExpires oppure è possibile utilizzare predefinito della cache dei requisiti tipici del descritte in questo documento.  

Ad esempio, da memorizzare nella cache del contenuto per un'ora, aggiungere quanto segue:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>Passaggi successivi

- [Leggere i dettagli sull'elemento **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
- [Leggere la documentazione per la proprietà **HttpResponse**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
- [Leggere la documentazione per la **Classe HttpCachePolicy**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  
