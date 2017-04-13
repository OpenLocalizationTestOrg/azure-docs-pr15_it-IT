<properties 
    pageTitle="Domande frequenti | Microsoft Azure" 
    description="Domande frequenti domande frequenti" 
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
    ms.date="09/19/2016" 
    ms.author="juliako"/>


#<a name="frequently-asked-questions"></a>Domande frequenti

##<a name="general-ams-faqs"></a>Domande frequenti AMS generale

D: come ridimensionate indicizzazione

R: le unità riservate sono gli stessi per le attività di codifica e indicizzazione. Seguire le istruzioni [sull'unità riservato codifica scala](media-services-scale-media-processing-overview.md). **Nota** che le prestazioni dell'indicizzatore non viene influenzato dal riservato tipo di unità.

D: è caricati codificato e pubblicato un video. Che cos'è il motivo per il video non viene riprodotto quando si tenta di inoltrarla?

R: uno dei motivi più comuni è che non si dispone di almeno un'unità di trasmissione riservata allocata sull'endpoint flusso da cui si sta tentando di riproduzione.  Seguire le istruzioni [sull'unità riservato Streaming scala](media-services-portal-scale-streaming-endpoints.md).

D: è possibile eseguire la composizione in un flusso live?

R: composizione su flussi live attualmente non disponibili nel servizi multimediali di Windows Azure, pertanto è necessario pre-comporre nel computer in uso.

D: è possibile usare CDN Azure con Live Streaming?

R: Media Services supporta l'integrazione con Azure CDN (per ulteriori informazioni, vedere [come gestire gli endpoint Streaming in un Account di servizi di supporto](media-services-portal-manage-streaming-endpoints.md)).  È possibile utilizzare Live streaming con CDN. Servizi multimediali di Azure consente di output di flusso, HLS e MPEG-tratto smussate. Tutti questi formati utilizzano HTTP per trasferire i dati e ottengono vantaggi della memorizzazione nella cache HTTP. In live streaming effettivo dati audio o video sono diviso a frammenti e memorizzazione nella cache di questo frammenti singoli in rete CDN. Manifesto dati è possibile solo dati deve essere aggiornati. Rete CDN Aggiorna periodicamente i dati.

D: significa supporto Azure servizi supportano immagini archiviazione?

R: se si analizzano solo per la memorizzazione di immagini JPEG o PNG, è consigliabile mantenere quelle in archiviazione Blob Azure. Non è necessario l'inserimento nell'account servizi multimediali a meno che non si desidera mantenerle associato a un Video o Audio risorse. Oppure, se si potrebbe aver bisogno di utilizzare le immagini come sovrapposizioni nel codificatore video. Media Encoder Standard supporta la sovrapposizione immagini nella parte superiore di video, ovvero vengono elencati JPEG e PNG come supportati i formati di input. Per ulteriori informazioni, vedere [Creazione di sovrapposizioni](media-services-custom-mes-presets-with-dotnet.md#overlay).

D: come è possibile copiare risorse da un account di servizi di supporto a un altro.

R: per copiare risorse da un account di servizi multimediali utilizzando .NET, utilizzare il metodo di estensione [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponibile nell'archivio di [Azure Media servizi .NET SDK estensioni](https://github.com/Azure/azure-sdk-for-media-services-extensions/) . Per ulteriori informazioni, vedere [questo](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) thread di forum.

D: quali sono i caratteri supportati per la denominazione di file quando si lavora con AMS?

R: Media Services utilizzerà il valore della proprietà IAssetFile.Name durante la creazione di URL per il contenuto di flusso (ad esempio http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Per questo motivo, non è consentita la codifica percentuale. Il valore della proprietà **Name** non può contenere i seguenti [caratteri percentuale codifica-riservati](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Inoltre, può essere presente solo uno '. " per l'estensione del file.


D: come si connettersi tramite resto?

R: dopo aver completato la connessione a https://media.windows.net, si riceverà un reindirizzamento 301 specificando un altro URI di servizi di supporto. È necessario effettuare chiamate successive al nuovo URI come descritto nella [connessione ai servizi multimediali utilizzando l'API REST](media-services-rest-connect-programmatically.md). 


D: come è possibile ruotare un video durante il processo di codifica.

R: [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) supporta rotazione dagli angoli di 90, 180/270. Il comportamento predefinito è "Auto", in cui il tentativo di rilevare i metadati di rotazione nella posta in arrivo file MP4/MOV e compensa essa. Includere il seguente elemento **origini** a uno del json predefiniti definito [di seguito](http://msdn.microsoft.com/library/azure/mt269960.aspx):
    
    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [
    
    ...




##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
