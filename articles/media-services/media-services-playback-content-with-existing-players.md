<properties 
    pageTitle="La riproduzione del contenuto | Microsoft Azure" 
    description="In questo argomento sono elencati i lettori esistenti che è possibile utilizzare per la riproduzione del contenuto." 
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
    ms.date="10/12/2016" 
    ms.author="juliako"/>


#<a name="playing-your-content-with-existing-players"></a>La riproduzione di contenuti con lettori esistenti

Servizi multimediali di Azure supporta numerosi formati di flussi comuni, ad esempio Smooth Streaming, HTTP Live Streaming e MPEG-tratto. In questo argomento contiene collegamenti ad lettori esistenti che è possibile utilizzare per testare i flussi.

>[AZURE.NOTE]Riproduci in modo dinamico pacchetto o il contenuto in modo dinamico crittografato, assicurarsi di ottenere almeno un'unità di trasmissione per l'endpoint flusso da cui si intende distribuire il contenuto. Per informazioni sul ridimensionamento unità di trasmissione, vedere: [come adattare unità di trasmissione](media-services-portal-manage-streaming-endpoints.md).

### <a name="the-azure-portal-media-services-content-player"></a>Azure portale Servizi multimediali contenuto Windows Media player

Il portale di **Azure** rappresenta un lettore di contenuto che è possibile utilizzare per testare il video.

Fare clic sul video desiderato (assicurarsi sia stato [pubblicato](media-services-portal-publish.md)) e fare clic sul pulsante **Riproduci** nella parte inferiore del portale.

Alcune considerazioni:

- **Servizi multimediali** viene riprodotto da quello predefinito streaming endpoint. Se si desidera riprodurre da un endpoint di trasmissione non predefinito, utilizzare un altro lettore. Ad esempio [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).


![AMSPlayer][AMSPlayer]

###<a name="azure-media-player"></a>Lettore multimediale Azure

Utilizzare [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) per la riproduzione del contenuto (Cancella o protetto) in uno dei formati seguenti:

- Smooth Streaming
- MPEG TRATTINO
- HLS
- MP4 graduale


###<a name="flash-player"></a>Flash Player

####<a name="aes-encrypted-with-token"></a>Crittografia AES con Token

[http://aestoken.azurewebsites.NET](http://aestoken.azurewebsites.net)

###<a name="silverlight-players"></a>Lettori Silverlight

####<a name="monitoring"></a>Monitoraggio

[http://SMF.cloudapp.NET/HealthMonitor](http://smf.cloudapp.net/healthmonitor)

####<a name="playready-with-token"></a>PlayReady con Token

[http://sltoken.azurewebsites.NET](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Lettori trattino

[http://dashplayer.azurewebsites.NET](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

###<a name="other"></a>Altri

Per verificare gli URL HLS è anche possibile usare:

- **Safari** in un dispositivo iOS o
- **Windows Media Player HLS 3ivx** in Windows.

##<a name="developing-video-players"></a>Sviluppo di lettori video

Per informazioni su come sviluppare i proprio lettori, vedere [lettori di video di sviluppo](media-services-develop-video-players.md)




##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
