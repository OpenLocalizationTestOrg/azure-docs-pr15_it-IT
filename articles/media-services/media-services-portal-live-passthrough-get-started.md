<properties 
    pageTitle="Come eseguire il flusso live con Encoder locale tramite il portale di Azure | Microsoft Azure" 
    description="In questa esercitazione sono illustrati i passaggi della creazione di un canale è configurato per il recapito di pass-through." 
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
    ms.topic="get-started-article"
    ms.date="10/24/2016" 
    ms.author="juliako"/>


#<a name="how-to-perform-live-streaming-with-on-premise-encoders-using-the-azure-portal"></a>Come eseguire flusso in diretta con Encoder locale tramite il portale di Azure

> [AZURE.SELECTOR]
- [Portale]( media-services-portal-live-passthrough-get-started.md)
- [.NET]( media-services-dotnet-live-encode-with-onpremises-encoders.md)
- [RESTO]( https://msdn.microsoft.com/library/azure/dn783458.aspx)

In questa esercitazione illustra i passaggi della tramite il portale di Azure per creare un **canale** è configurato per il recapito di pass-through. 

##<a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione è necessario quanto segue:

- Un account Azure. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Un account di servizi di supporto. Per creare un account di servizi multimediali, vedere [come creare un Account di servizi di supporto](media-services-portal-create-account.md).
- Una webcam. Ad esempio [Wirecast questo codificatore](http://www.telestream.net/wirecast/overview.htm).

Si consiglia di esaminare gli articoli seguenti:

- [Servizi multimediali Azure RTP supportano e Live Encoder](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
- [Panoramica di Live acqua tramite servizi multimediali di Windows Azure](media-services-manage-channels-overview.md)
- [Live streaming con Encoder locale che creano flussi con più velocità](media-services-live-streaming-with-onprem-encoders.md)


##<a id="scenario"></a>Scenario di trasmissione live comune

La procedura seguente descritte le attività per la creazione di applicazioni di trasmissione live comuni che usano i canali che sono configurati per il recapito della pass-through. In questa esercitazione viene illustrato come creare e gestire un canale pass-through e gli eventi live.

1. Connettere una videocamera a un computer. Avviare e configurare un codificatore live locale che restituisce un flusso di RTP o MP4 frammentato con più velocità. Per ulteriori informazioni, vedere [supporto RTP servizi multimediali di Azure e Live Encoder](http://go.microsoft.com/fwlink/?LinkId=532824).
    
    Questo passaggio può anche essere eseguito dopo aver creato il canale.

1. Creare e avviare un canale pass-through.
1. Recuperare il canale acquisizione URL. 

    L'URL di caricamento viene utilizzato dal codificatore di live per inviare il flusso per il canale.
1. Recuperare l'URL di anteprima di canale. 

    Utilizzare questo URL per verificare che il canale correttamente riceva flusso live.

3. Creare un evento/programma live. 

    Quando si usa il portale di Azure, creazione di un evento live crea anche una risorsa. 
      
    >[AZURE.NOTE]Assicurarsi di essere presente almeno un flusso unità riservati per l'endpoint flusso da cui si desidera il contenuto di flusso.
1. Avviare il programma di evento/quando si è pronti avviare il flusso e l'archiviazione.
2. Facoltativamente, il codificatore live può essere segnalato per avviare un annuncio pubblicitario. L'annuncio viene inserito nel flusso di output.
1. Interrompere l'evento/programma ogni volta che si desidera interrompere la trasmissione e archiviazione l'evento.
1. Eliminare l'evento/programma (e facoltativamente bene).     

>[AZURE.IMPORTANT] Esaminare [streaming Live con Encoder locale che creano flussi con più velocità](media-services-live-streaming-with-onprem-encoders.md) per informazioni sui concetti e considerazioni relativi alla trasmissione live con Encoder in locale e i canali pass-through.

##<a name="to-view-notifications-and-errors"></a>Per visualizzare le notifiche e gli errori

Se si desidera visualizzare le notifiche e gli errori prodotti dal portale di Azure, fare clic sull'icona di notifica.

![Notifiche](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

##<a name="configure-streaming-endpoints"></a>Configurare i punti finali flussi 

Servizi multimediali di fornisce imballaggio dinamico, che consente di eseguire il MP4s con più velocità nei seguenti formati di trasmissione: trattino MPEG, HLS, Smooth Streaming o unità 10K, senza dover ricreare il pacchetto in questi formati di flusso. Con imballaggio dinamico è sufficiente archiviare e pagare per i file nel formato di archiviazione e servizi multimediali consente di creare e serve la risposta appropriata in base a richieste da un client.

Per sfruttare imballaggio dinamico, è necessario ottenere almeno un'unità di trasmissione per l'endpoint flusso dal quale si prevede di recapito del contenuto.  

Per creare e modificare il numero di unità riservate di flusso, eseguire le operazioni seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Nella finestra **Impostazioni** fare clic sui **punti finali flusso**. 

2. Fare clic su predefinito streaming endpoint. 

    Viene visualizzata la finestra **Predefinito STREAMING informazioni relative all'ENDPOINT** .

3. Per specificare il numero di unità di trasmissione, spostare il cursore **Streaming unità** .

    ![Unità di trasmissione](./media/media-services-portal-passthrough-get-started/media-services-streaming-units.png)

4. Fare clic sul pulsante **Salva** per salvare le modifiche.

    >[AZURE.NOTE]L'allocazione di qualsiasi unità nuova può richiedere fino a 20 minuti.
    
##<a name="create-and-start-pass-through-channels-and-events"></a>Creare e avviare eventi e i canali pass-through

Un canale è associato a eventi e i programmi che consentono di controllare la pubblicazione e l'archiviazione dei segmenti di un flusso in diretta. I canali di gestire gli eventi. 
    
È possibile specificare il numero di ore che si desidera conservare il contenuto registrato per il programma impostando la lunghezza **Finestra archivio** . Per un massimo di 25 ore, è possibile impostare questo valore da un minimo di 5 minuti. La lunghezza della finestra archivio determina anche la maggior quantità di client ora è possibile cercare indietro nel tempo dalla posizione corrente live. Eventi possono eseguire per il periodo di tempo specificato, ma verrà continuamente eliminato contenuto che si trova dietro la lunghezza della finestra. Questo valore di questa proprietà determina anche la durata delle manifesti client espandibile.

Ogni evento è associata a una risorsa. Per pubblicare l'evento, è necessario creare un indicatore di posizione su richiesta per le risorse associate. Con questo locator consente di generare un URL di trasmissione che è possibile fornire per i clienti.

Un canale supporta fino a tre eventi contemporaneamente in esecuzione in modo che è possibile creare più archivi di stesso flusso di posta in arrivo. In questo modo è possibile pubblicare e archiviare diverse parti di un evento in base alle esigenze. Ad esempio, l'esigenza consiste nell'archiviare 6 ore di un programma di trasmettere solo ultimi 10 minuti. A tale scopo, è necessario creare due programmi contemporaneamente in esecuzione. Un programma è impostato per archiviare 6 ore dell'evento, ma il programma non è stato pubblicato. Un altro programma è impostato per l'archiviazione di 10 minuti e questo programma viene pubblicato.

Evitare di riutilizzare eventi live esistenti. In alternativa, creare e avviare un nuovo evento per ogni evento.

Avviare l'evento quando si è pronti avviare il flusso e l'archiviazione. Interrompere il programma ogni volta che si desidera interrompere la trasmissione e archiviazione l'evento. 

Per eliminare il contenuto archiviato, interrompere ed eliminare l'evento e quindi bene associate. Una risorsa non può essere eliminata se viene utilizzato da un evento. prima di tutto è necessario eliminare l'evento. 

Anche se si interrompere ed elimina l'evento, gli utenti sarebbero in grado di flusso il contenuto archiviato come video su richiesta per finché non si elimina la risorsa.

Se si desidera conservare il contenuto archiviato, ma non è disponibile per il flusso, eliminare locator flusso.

###<a name="to-use-the-portal-to-create-a-channel"></a>Usare il portale per creare un canale 

In questa sezione viene illustrato come utilizzare l'opzione **Creazione rapida** per creare un canale pass-through.

Per ulteriori informazioni sui canali pass-through, vedere [Live streaming con Encoder locale che creare flussi di velocità multipla](media-services-live-streaming-with-onprem-encoders.md).

1. Nel [portale di Azure](https://portal.azure.com/), selezionare il proprio account di servizi multimediali di Windows Azure.
2. Nella finestra **Impostazioni** fare clic su **Live streaming**. 

    ![Guida introduttiva](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
    
    Viene visualizzata la finestra **streaming Live** .

3. Fare clic su **Creazione rapida** per creare un canale pass-through con la RTP acquisizione protocollo.

    Viene visualizzata la finestra di **Creare un nuovo canale** .
4. Specificare un nome per il nuovo canale e fare clic su **Crea**. 

    Verrà creato un canale pass-through con la RTP acquisizione protocollo.

##<a name="create-events"></a>Creare eventi

1. Selezionare un canale in cui si desidera aggiungere un evento.
2. Premere il pulsante **Live evento** .

![Evento](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)


##<a name="get-ingest-urls"></a>Ottenere l'acquisizione URL

Dopo aver creato il canale, è possibile ottenere il caricamento di URL che si fornirà al codificatore live. Il codificatore utilizza questi URL per un flusso live di input.

![Creato](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

##<a name="watch-the-event"></a>Guardare l'evento

Per controllare l'evento, fare clic su **controllo** nel portale di Azure o copiare l'URL di trasmissione e utilizzare un lettore di propria scelta. 
 
![Creato](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

Evento Live ottenere automaticamente convertiti in contenuto su richiesta quando è in esecuzione.

##<a name="clean-up"></a>Pulire

Per ulteriori informazioni sui canali pass-through, vedere [Live streaming con Encoder locale che creare flussi di velocità multipla](media-services-live-streaming-with-onprem-encoders.md).

- Solo quando sono stati interrotti tutti gli eventi/programmi sul canale, è possibile interromperla un canale.  Una volta viene interrotto il canale, non è soggetta le spese. Quando è necessario riavviarla, avrà la stessa acquisizione URL in modo che non sarà necessario riconfigurare il codificatore.
- È possibile eliminare un canale solo quando sono stati eliminati tutti gli eventi live sul canale.

##<a name="view-archived-content"></a>Visualizzare il contenuto archiviato

Anche se si interrompere ed elimina l'evento, gli utenti sarebbero in grado di flusso il contenuto archiviato come video su richiesta per finché non si elimina la risorsa. Una risorsa non può essere eliminata se viene utilizzato da un evento. prima di tutto è necessario eliminare l'evento. 

Per gestire le risorse, selezionare **Impostazioni** e fare clic su **risorse**.

![Risorse](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

##<a name="next-step"></a>Passaggio successivo

Esaminare i servizi multimediali di percorsi formativi.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
