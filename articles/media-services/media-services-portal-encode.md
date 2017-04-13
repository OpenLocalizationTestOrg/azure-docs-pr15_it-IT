<properties
    pageTitle="Codificare un bene mediante Media Encoder Standard con il portale di Azure | Microsoft Azure"
    description="In questa esercitazione è illustrata la procedura di codifica di un bene mediante Media Encoder Standard con il portale di Azure."
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


# <a name="encode-an-asset-using-media-encoder-standard-with-the-azure-portal"></a>Codificare un bene mediante Media Encoder Standard con il portale di Azure

> [AZURE.NOTE] Per completare questa esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 

Quando si lavora con servizi multimediali di Windows Azure uno i motivi più comuni è l'esecuzione di velocità adattata streaming per i clienti. Servizi multimediali sono supportati i seguenti velocità adattata streaming tecnologie: HTTP Live Streaming (HLS), Smooth Streaming, MPEG trattino e unità 10K (per solo licenziatari a questo punto Adobe/accesso). Per preparare i video per velocità adattata streaming, è necessario codificare il video di origine in file con più velocità. Utilizzare il codificatore **Media Encoder Standard** per codificare i video.  

Servizi multimediali vengono forniti anche imballaggio dinamico che consente di eseguire il MP4s con più velocità nei seguenti formati di trasmissione: trattino MPEG, HLS, Smooth Streaming o unità 10K, senza che sia necessario creare un nuovo pacchetto in questi formati di flusso. Con imballaggio dinamico è sufficiente archiviare e pagare per i file nel formato di archiviazione e servizi multimediali la compilazione e fornire la risposta appropriata in base a richieste da un client.

Per sfruttare imballaggio dinamico, è necessario eseguire le operazioni seguenti:

- Codificare il file di origine in una serie di file MP4 multi-velocità (la procedura di codifica vengono illustrata più avanti in questa sezione).
- È possibile ottenere almeno un'unità di trasmissione per l'endpoint flusso dal quale si prevede di recapito del contenuto. Per ulteriori informazioni, vedere [configurare i punti finali flussi](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

Per ridimensionare l'elaborazione di elementi multimediali, vedere [questo](media-services-portal-scale-media-processing.md) argomento.

## <a name="encode-with-the-azure-portal"></a>Codificare il portale di Azure

In questa sezione vengono descritti i passaggi da eseguire per codificare il contenuto con Media Encoder Standard.

1.  Nel [portale di Azure](https://portal.azure.com/), selezionare il proprio account di servizi multimediali di Windows Azure.
2.  Nella finestra **Impostazioni** selezionare **risorse**.  
2.  Nella finestra di **risorse** , selezionare la risorsa che si desidera codificare.
3.  Fare clic sul pulsante **codifica** .
4.  Nella finestra di **un bene codifica** selezionare processore "Media Encoder Standard" e un predefinito. Ad esempio, se si conosce il video input ha una risoluzione di 1920x1080 pixel, è possibile utilizzare il "H264 velocità più 1080p" preimpostato. Per ulteriori informazioni sulle impostazioni predefinite, vedere [questo](https://msdn.microsoft.com/library/azure/mt269960.aspx) articolo, è importante selezionare l'impostazione predefinita più appropriato per l'input video. Se si dispone di un video a bassa risoluzione (640x360), quindi non si devono utilizzare il valore predefinito "H264 velocità più 1080p" preimpostato.
    
    Per facilitare la gestione, è disponibile un'opzione di modifica il nome di un bene output e il nome del processo.
        
    ![Codificare beni](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Premere **creare**.


##<a name="next-step"></a>Passaggio successivo

È possibile monitorare codifica processo lo stato di avanzamento con il portale di Azure, come descritto in [questo](media-services-portal-check-job-progress.md) articolo.  

##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


