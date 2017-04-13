<properties
    pageTitle="  Pubblicare contenuto tramite il portale di Azure | Microsoft Azure"
    description="In questa esercitazione sono illustrati i passaggi della pubblicazione di contenuto con il portale di Azure."
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
    ms.date="10/24/2016"
    ms.author="juliako"/>

# <a name="publish-content-with-the-azure-portal"></a>Pubblicare contenuto tramite il portale di Azure

> [AZURE.SELECTOR]
- [Portale](media-services-portal-publish.md)
- [.NET](media-services-deliver-streaming-content.md)
- [RESTO](media-services-rest-deliver-streaming-content.md)

## <a name="overview"></a>Panoramica

> [AZURE.NOTE] Per completare questa esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 

Per fornire all'utente con un URL che può essere utilizzato per flusso o il download del contenuto, è innanzitutto necessario "pubblica" le risorse mediante la creazione di un indicatore di posizione. Indicatori consentono di accedere ai file contenuti nel bene. Servizi multimediali sono supportati due tipi di indicatori: 

- Streaming Locator (OnDemandOrigin), utilizzato per lo streaming adattata (ad esempio, per flusso trattino MPEG, HLS o Smooth Streaming). Per creare un flusso locator le risorse deve contenere un file di ISM. 
- Indicatori (SA) utilizzati per il recapito di video tramite download graduale graduale.


È possibile utilizzare per la riproduzione Smooth Streaming risorse un URL di trasmissione ha il seguente formato.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Per creare un HLS streaming URL, aggiungere (formato = m3u8 aapl) all'URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Per creare un trattino MPEG streaming URL, aggiungere (formato = csf di tempo mpd) all'URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Un URL SA ha il seguente formato.

    {blob container name}/{asset name}/{file name}/{SAS signature}

Per ulteriori informazioni, vedere [Panoramica sul contenuto di recapito](media-services-deliver-content-overview.md).

>[AZURE.NOTE] Se il portale è stato utilizzato per creare indicatori prima di marzo 2015, sono stati creati Locator con una data di scadenza di due anni.  

Per aggiornare la data di scadenza su un indicatore di posizione, usare [resto](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) o API di [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) . Nota Quando si aggiorna la data di scadenza di un indicatore di posizione SA, l'URL viene modificato.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Usare il portale per la pubblicazione di un bene

Per utilizzare il portale per pubblicare una risorsa, eseguire le operazioni seguenti:

1. Nel [portale di Azure](https://portal.azure.com/), selezionare il proprio account di servizi multimediali di Windows Azure.
1. Selezionare **Impostazioni** > **risorse**.
1. Selezionare la risorsa che si desidera pubblicare.
1. Fare clic sul pulsante **pubblica** .
1. Selezionare il tipo di locator.
2. Premere **Aggiungi**.

    ![Pubblicare](./media/media-services-portal-vod-get-started/media-services-publish1.png)

L'URL verrà aggiunto all'elenco degli **URL pubblicato**.

## <a name="play-content-from-the-portal"></a>Riproduzione di contenuto tramite il portale

Il portale di Azure rappresenta un lettore di contenuto che è possibile utilizzare per testare il video.

Fare clic sul video desiderato e quindi fare clic sul pulsante **Riproduci** .

![Pubblicare](./media/media-services-portal-vod-get-started/media-services-play.png)

Alcune considerazioni:

- Verificare che il video è stato pubblicato.
- Il **lettore multimediale** viene riprodotto da quello predefinito streaming endpoint. Se si desidera riprodurre da un endpoint di trasmissione non predefinito, fare clic su per copiare l'URL e utilizzare un altro lettore. Ad esempio, [Servizi multimediali di Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
- L'endpoint flusso da cui sta eseguendo il flusso deve essere in esecuzione.  
- Per eseguire il flusso da un endpoint di flusso, è necessario aggiungere almeno un'unità di trasmissione. Per ulteriori informazioni, vedere [questo](media-services-portal-scale-streaming-endpoints.md) argomento.   

##<a name="next-steps"></a>Passaggi successivi

Esaminare i servizi multimediali di percorsi formativi.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


