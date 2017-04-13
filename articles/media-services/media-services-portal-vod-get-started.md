<properties
    pageTitle=" Iniziare con la distribuzione di contenuti su richiesta tramite il portale di Azure | Microsoft Azure"
    description="In questa esercitazione sono illustrati i passaggi dell'implementazione di un servizio di distribuzione di contenuti punti (VoD) base con l'applicazione Servizi multimediali di Azure (AMS) tramite il portale di Azure."
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
    ms.topic="get-started-article"
    ms.date="08/30/2016"
    ms.author="juliako"/>


# <a name="get-started-with-delivering-content-on-demand-using-the-azure-portal"></a>Iniziare con la distribuzione di contenuti su richiesta tramite il portale di Azure

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

In questa esercitazione sono illustrati i passaggi dell'implementazione di un servizio di distribuzione di contenuti punti (VoD) base con l'applicazione Servizi multimediali di Azure (AMS) tramite il portale di Azure.

> [AZURE.NOTE] Per completare questa esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 

In questa esercitazione include le seguenti operazioni:

1.  Creare un account di servizi multimediali di Windows Azure.
2.  Configurare endpoint flusso.
1.  Caricare un file video.
1.  Codificare il file di origine in una serie di file MP4 velocità adattata.
1.  Pubblicare le risorse e get streaming e download graduale URL.  
1.  Riprodurre il contenuto.


## <a name="create-an-azure-media-services-account"></a>Creare un account di servizi multimediali di Windows Azure

La procedura descritta in questa sezione viene illustrato come creare un account AMS.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **+ nuova** > **Web + Mobile** > **Servizi multimediali**.

    ![Creazione di servizi multimediali](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. **Creare** account servizi multimediali immettere valori obbligatori.

    ![Creazione di servizi multimediali](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. In **Nome Account**immettere il nome del nuovo account AMS. Il nome di un account di servizi multimediali è tutto minuscole caratteri alfanumerici senza spazi e caratteri da 3 a 24.
    2. In abbonamento, scegliere tra i diversi abbonamenti Azure che è possibile accedere a.
    
    2. Nel **Gruppo di risorse**, selezionare la risorsa nuova o esistente.  Un gruppo di risorse è una raccolta di risorse che condividono ciclo di vita, autorizzazioni e criteri. Altre informazioni [di seguito](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. In **posizione**, selezionare la località geografica viene usata per archiviare i record dei metadati ed elementi multimediali per il proprio account di servizi di supporto. Quest'area viene utilizzata per elaborare e trasmettere file multimediali. Solo le aree di servizi multimediali disponibili vengono visualizzate nella casella di riepilogo a discesa. 
    
    3. In **Account di archiviazione**, selezionare un account di archiviazione per fornire l'archiviazione blob del contenuto multimediale dal proprio account di servizi di supporto. È possibile selezionare un account di archiviazione esistente nella stessa area geografica dell'account di servizi multimediali oppure è possibile creare un account di archiviazione. Viene creato un nuovo account di archiviazione nella stessa regione. Le regole per i nomi degli account di archiviazione sono gli stessi account servizi di supporto.

        Altre informazioni sull'archiviazione [di seguito](storage-introduction.md).

    4. Selezionare **Aggiungi a dashboard** per visualizzare l'avanzamento della distribuzione account.
    
7. Fare clic su **Crea** nella parte inferiore del modulo.

    Una volta l'account è stata creata, viene modificato lo stato sia in **esecuzione**. 

    ![Impostazioni di servizi di supporto](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Per gestire il proprio account AMS (ad esempio, caricare video, codificare risorse, monitorare l'avanzamento di processo) utilizzare la finestra **Impostazioni** .

## <a name="manage-keys"></a>La gestione delle chiavi

È necessario il nome dell'account e le informazioni chiave primarie a livello di programmazione accesso all'account di servizi di supporto.

1. Nel portale di Azure, selezionare il proprio account. 

    Sul lato destro viene visualizzata la finestra di **Impostazioni** . 

2. Nella finestra **Impostazioni** selezionare **chiavi**. 

    Windows **Gestisci chiavi** Mostra il nome dell'account e chiavi primarie e secondarie viene visualizzato. 
3. Fare clic sul pulsante Copia per copiare i valori.
    
    ![Tasti di servizi multimediali di Microsoft](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="configure-streaming-endpoints"></a>Configurare i punti finali flussi

Quando si lavora con servizi multimediali di Windows Azure uno i motivi più comuni è l'esecuzione di video tramite velocità adattata streaming per i clienti. Servizi multimediali sono supportati i seguenti velocità adattata streaming tecnologie: HTTP Live Streaming (HLS), Smooth Streaming, MPEG trattino e unità 10K (per solo licenziatari a questo punto Adobe/accesso).

Servizi multimediali di fornisce imballaggio dinamico, che consente di eseguire la velocità in bit adattata contenuto MP4 codificato in streaming formati supportati da servizi multimediali (trattino MPEG, HLS, Smooth Streaming, unità 10K) just-in-time, senza che sia necessario archiviare versioni pre-nel pacchetto di ciascuno di questi formati di flusso.

Per sfruttare imballaggio dinamico, è necessario eseguire le operazioni seguenti:

- Codificare il file mezzanine (origine) in una serie di file di velocità adattata MP4 (codifica passaggi illustrati più avanti in questa esercitazione).  
- Creare almeno un'unità di flusso per la *trasmissione endpoint* da cui si prevede di recapito del contenuto. La procedura seguente viene illustrato come cambiare il numero di unità di trasmissione.

Con dinamico imballaggio, è sufficiente archiviare e pagare per i file nel formato di archiviazione e servizi multimediali consente di creare e serve la risposta appropriata in base a richieste da un client.

Per creare e modificare il numero di unità riservate di flusso, eseguire le operazioni seguenti:


1. Nella finestra **Impostazioni** fare clic sui **punti finali flusso**. 

2. Fare clic su predefinito streaming endpoint. 

    Viene visualizzata la finestra **Predefinito STREAMING informazioni relative all'ENDPOINT** .

3. Per specificare il numero di unità di trasmissione, spostare il cursore **Streaming unità** .

    ![Unità di trasmissione](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Fare clic sul pulsante **Salva** per salvare le modifiche.

    >[AZURE.NOTE]L'allocazione di qualsiasi unità nuova può richiedere fino a 20 minuti.

## <a name="upload-files"></a>Caricare i file

Flusso di video tramite servizi multimediali di Windows Azure, è necessario caricare i video di origine, li codifica in più bitrate e pubblicare il risultato. In questa sezione viene descritto il primo passaggio. 

1. Nella finestra di **impostazione** , fare clic su **risorse**.

    ![Caricare i file](./media/media-services-portal-vod-get-started/media-services-upload.png)

3. Fare clic sul pulsante **Carica** .

    Viene visualizzata la finestra **Carica una risorsa video** .

    >[AZURE.NOTE] Non esiste nessuna limitazione delle dimensioni di file.
    
4. Individuare il video del computer desiderato, selezionarlo e scegliere OK.  

    Avvia il caricamento ed è possibile visualizzare lo stato di avanzamento sotto il nome del file.  

Dopo avere completato il caricamento, viene visualizzato il nuovo bene elencato nella finestra di **risorse** . 

## <a name="encode-assets"></a>Codificare beni

Quando si lavora con servizi multimediali di Windows Azure uno i motivi più comuni è l'esecuzione di velocità adattata streaming per i clienti. Servizi multimediali sono supportati i seguenti velocità adattata streaming tecnologie: HTTP Live Streaming (HLS), Smooth Streaming, MPEG trattino e unità 10K (per solo licenziatari a questo punto Adobe/accesso). Per preparare i video per velocità adattata streaming, è necessario codificare il video di origine in file con più velocità. Utilizzare il codificatore **Media Encoder Standard** per codificare i video.  

Servizi multimediali vengono forniti anche imballaggio dinamico, che consente di eseguire il MP4s con più velocità nei seguenti formati di trasmissione: trattino MPEG, HLS, Smooth Streaming o unità 10K, senza dover ricreare il pacchetto in questi formati di flusso. Con dinamico imballaggio, è sufficiente archiviare e pagare per i file nel formato di archiviazione e servizi multimediali consente di creare e serve la risposta appropriata in base a richieste da un client.

Per sfruttare imballaggio dinamico, è necessario eseguire le operazioni seguenti:

- Codificare il file di origine in una serie di file MP4 multi-velocità (la procedura di codifica vengono illustrata più avanti in questa sezione).
- È possibile ottenere almeno un'unità di trasmissione per l'endpoint flusso dal quale si prevede di recapito del contenuto. Per ulteriori informazioni, vedere [configurare i punti finali flussi](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

### <a name="to-use-the-portal-to-encode"></a>Usare il portale codificare

In questa sezione vengono descritti i passaggi da eseguire per codificare il contenuto con Media Encoder Standard.

1.  Nella finestra **Impostazioni** selezionare **risorse**.  
2.  Nella finestra di **risorse** , selezionare la risorsa che si desidera codificare.
3.  Fare clic sul pulsante **codifica** .
4.  Nella finestra di **un bene codifica** selezionare processore "Media Encoder Standard" e un predefinito. Ad esempio, se si conosce il video input ha una risoluzione di 1920x1080 pixel, è possibile utilizzare il "H264 velocità più 1080p" preimpostato. Per ulteriori informazioni sulle impostazioni predefinite, vedere [questo](https://msdn.microsoft.com/library/azure/mt269960.aspx) articolo, è importante selezionare l'impostazione predefinita più appropriato per l'input video. Se si dispone di un video a bassa risoluzione (640x360), quindi non si devono utilizzare il valore predefinito "H264 velocità più 1080p" preimpostato.
    
    Per facilitare la gestione, è disponibile un'opzione di modifica il nome di un bene output e il nome del processo.
        
    ![Codificare beni](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Premere **creare**.

### <a name="monitor-encoding-job-progress"></a>Controllare lo stato di avanzamento di codifica processo

Per controllare lo stato di avanzamento del processo di codifica, fare clic su **Impostazioni** (nella parte superiore della pagina) e quindi selezionare **processi**.

![Processi](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Pubblicazione di contenuto

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

>[AZURE.NOTE] Se il portale è stato utilizzato per creare indicatori prima di marzo 2015, sono stati creati Locator con una data di scadenza di due anni.  

Per aggiornare la data di scadenza su un indicatore di posizione, usare [resto](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) o API di [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) . Quando si aggiorna la data di scadenza di un indicatore di posizione SA, l'URL viene modificato.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Usare il portale per la pubblicazione di un bene

Per utilizzare il portale per pubblicare una risorsa, eseguire le operazioni seguenti:

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

##<a name="next-steps"></a>Passaggi successivi

Esaminare i servizi multimediali di percorsi formativi.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


