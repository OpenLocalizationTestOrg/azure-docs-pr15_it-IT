<properties 
    pageTitle="Come creare un processore Media | Microsoft Azure" 
    description="Informazioni su come creare un componente di processore elementi multimediali per la codifica, convertire il formato, crittografare o decrittografare contenuti multimediali per servizi multimediali di Windows Azure. Esempi di codice scritto in c# e utilizzano Media Services SDK per .NET." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
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

##<a name="get-media-processor"></a>Ottenere supporto processore

Il metodo seguente viene illustrato come ottenere un'istanza di processore media. Esempio di codice presuppone l'utilizzo di una variabile a livello di modulo denominata **Context** per fare riferimento al contesto server come descritto nella sezione [come: connettersi a Media servizi a livello di programmazione](media-services-dotnet-connect-programmatically.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
        return processor;
    }


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-steps"></a>Passaggi successivi

Dopo aver scoperto come ottenere un'istanza di processore media, passare all'argomento [come codificare una risorsa](media-services-dotnet-encode-with-media-encoder-standard.md) che illustra come usare Media Encoder Standard per codificare una risorsa.


