<properties 
    pageTitle="Come creare un processore Media | Microsoft Azure" 
    description="Informazioni su come creare un componente di processore elementi multimediali per la codifica, convertire il formato, crittografare o decrittografare contenuti multimediali per servizi multimediali di Windows Azure." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="how-to-get-a-media-processor-instance"></a>Procedura: ottenere un'istanza di processore elementi multimediali


> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [RESTO](media-services-rest-get-media-processor.md)

##<a name="overview"></a>Panoramica

In servizi multimediali di che un processore media è un componente che gestisce un'attività di elaborazione specifico, ad esempio la codifica, formattare la conversione, la crittografia o decrittografia di contenuti multimediali. Generalmente si crea un processore di elementi multimediali durante la creazione di un'attività per codificare, crittografare o convertire il formato di contenuti multimediali.

La tabella seguente contiene il nome e la descrizione di ogni processore supporti disponibili.

Nome processore elementi multimediali|Descrizione|Ulteriori informazioni
---|---|---
Media Encoder Standard|Funzionalità standard per la codifica su richiesta. |[Panoramica e il confronto di Azure nella richiesta Media Encoder](media-services-encode-asset.md)
Flusso di lavoro Media Encoder Premium|Consente di eseguire attività di codifica uso del flusso di lavoro di Media Encoder Premium.|[Panoramica e il confronto di Azure nella richiesta Media Encoder](media-services-encode-asset.md)
Media Azure indicizzatore| Consente di verificare che supportano le ricerche di contenuti e i file multimediali, come generare tracce didascalie sottotitoli e le parole chiave.|[Media Azure indicizzatore](media-services-index-content.md)
Azure Media Hyperlapse (preview)|Consente di arrotondare "irregolarità" in un video con stabilizzazione video. Consente di velocizzare il contenuto in un clip consumo.|[Media Azure Hyperlapse](media-services-hyperlapse-content.md)
Azure Media Encoder|Ammortamento
Spazio di archiviazione decrittografia| Ammortamento|
Media Azure Packager|Ammortamento|
Crittografia Media Azure|Ammortamento|

##<a name="get-mediaprocessor"></a>Ottenere MediaProcessor

>[AZURE.NOTE] Quando si utilizza l'API REST di servizi multimediali, le considerazioni seguenti:
>
>Quando si accede a entità in servizi di supporto, è necessario impostare i campi specifici dell'intestazione e i valori nelle convocazioni HTTP. Per ulteriori informazioni, vedere [il programma di installazione per lo sviluppo di API REST servizi multimediali](media-services-rest-how-to-use.md).

>Dopo aver completato la connessione a https://media.windows.net, si riceverà un reindirizzamento 301 specificando un altro URI di servizi di supporto. È necessario effettuare chiamate successive al nuovo URI come descritto nella [connessione ai servizi multimediali utilizzando l'API REST](media-services-rest-connect-programmatically.md). 


La chiamata resto seguente viene illustrato come ottenere un'istanza di processore multimediali in base al nome (in questo caso, **Media Encoder Standard**). 



    
Richiesta:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.11
    Host: media.windows.net
    
Risposta:
        
    . . .
    
    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-steps"></a>Passaggi successivi

Dopo aver scoperto come ottenere un'istanza di processore media, passare all'argomento [come codificare una risorsa](media-services-rest-get-started.md) che illustra come usare Media Encoder Standard per codificare una risorsa.
