<properties
    pageTitle=" Scala streaming endpoint con il portale di Azure | Microsoft Azure"
    description="In questa esercitazione sono illustrati i passaggi della scala i punti finali flussi con il portale di Azure."
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


# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Ridimensionare i punti finali flussi con il portale di Azure

##<a name="overview"></a>Panoramica

> [AZURE.NOTE] Per completare questa esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 

Quando si lavora con servizi multimediali di Windows Azure uno i motivi più comuni è l'esecuzione di video tramite velocità adattata streaming per i clienti. Servizi multimediali sono supportati i seguenti velocità adattata streaming tecnologie: HTTP Live Streaming (HLS), Smooth Streaming, MPEG trattino e unità 10K (per solo licenziatari a questo punto Adobe/accesso).

Servizi multimediali di fornisce imballaggio dinamico che consente di eseguire la velocità in bit adattata contenuto MP4 codificato in streaming formati supportati da servizi multimediali (trattino MPEG, HLS, Smooth Streaming, unità 10K) just-in-time, senza che sia necessario archiviare versioni pre-nel pacchetto di ciascuno di questi formati di flusso.

Per sfruttare imballaggio dinamico, è necessario eseguire le operazioni seguenti:

- Codificare il file mezzanine (origine) in una serie di file di velocità adattata MP4 (codifica passaggi illustrati più avanti in questa esercitazione).  
- Creare almeno un'unità di flusso per la *trasmissione endpoint* da cui si prevede di recapito del contenuto. La procedura seguente viene illustrato come cambiare il numero di unità di trasmissione.

Con imballaggio dinamico è sufficiente archiviare e pagare per i file nel formato di archiviazione e servizi multimediali la compilazione e fornire la risposta appropriata in base a richieste da un client.

Inoltre, è possibile controllare la capacità del servizio di trasmissione Endpoint per gestire l'aumento della larghezza di banda esigenze modificando l'unità di trasmissione. Si consiglia di assegnare una o più unità di scala per le applicazioni nell'ambiente di produzione. Unità di trasmissione fornirgli entrambe le capacità di uscita dedicato che può essere acquistato in incrementi di 200 Mbps e altre funzionalità quali funzionalità che include: [imballaggio dinamico](media-services-dynamic-packaging-overview.md), l'integrazione di rete CDN e configurazione avanzata. Per ulteriori informazioni, vedere [i punti finali flussi di gestire il portale di Azure](media-services-portal-manage-streaming-endpoints.md).

## <a name="scale-streaming-endpoints"></a>Ridimensionare i punti finali flussi

Per creare e modificare il numero di unità riservate di flusso, eseguire le operazioni seguenti:

1. Nel [portale di Azure](https://portal.azure.com/), selezionare il proprio account di servizi multimediali di Windows Azure.
2. Nella finestra **Impostazioni** selezionare **i punti finali flusso**.
3. Fare clic sull'endpoint di flusso che si desidera ridimensionare. 
4. Spostare il dispositivo di scorrimento per specificare il numero di unità di trasmissione
 
![Endpoint trasmissione](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

Le considerazioni seguenti:

- L'allocazione di qualsiasi flusso unità nuova può richiedere circa il 20 minuti. 
- Attualmente, passando da qualsiasi valore positivo di streaming unità alla nessuno, disattivare streaming su richiesta per fino a un'ora.
- Il numero massimo di unità specificate per il periodo di 24 ore viene utilizzato il calcolo del costo. Per informazioni sui prezzi informazioni dettagliate, vedere [Dettagli prezzi servizi di supporto](http://go.microsoft.com/fwlink/?LinkId=275107).

##<a name="next-steps"></a>Passaggi successivi

Esaminare i servizi multimediali di percorsi formativi.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


