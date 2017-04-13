<properties
    pageTitle="Iniziare con la distribuzione di contenuti su richiesta utilizzando .NET | Azure"
    description="In questa esercitazione sono illustrati i passaggi dell'implementazione di un'applicazione di distribuzione di contenuti su richiesta con servizi multimediali di Windows Azure con .NET."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/17/2016"
    ms.author="juliako"/>


# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Iniziare con la distribuzione di contenuti su richiesta utilizzando .NET SDK

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

>[AZURE.NOTE]
> Per completare questa esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 
 
##<a name="overview"></a>Panoramica 

In questa esercitazione sono illustrati i passaggi dell'implementazione di un'applicazione di distribuzione di contenuti punti (VoD) tramite servizi multimediali Azure (AMS) SDK per .NET.


L'esercitazione introduce il flusso di lavoro di servizi multimediali base e gli oggetti di programmazione più comuni e attività necessarie per lo sviluppo di servizi di supporto. Al termine dell'esercitazione, sarà possibile flusso o il download gradualmente un file multimediale di esempio caricati, con codifica e scaricati.

## <a name="what-youll-learn"></a>Si apprenderanno

L'esercitazione viene illustrato come eseguire le operazioni seguenti:

1.  Creare un account di servizi multimediali (tramite il portale di Azure).
2.  Configurare endpoint flusso (tramite il portale di Azure).
3.  Creare e configurare un progetto Visual Studio.
5.  Connettersi all'account di servizi di supporto.
6.  Creare un nuovo bene e caricare un file video.
7.  Codificare il file di origine in una serie di file MP4 velocità adattata.
8.  Pubblicare la risorsa e ottenere l'URL per il download di flusso e avanzato.
9.  Testare la riproduzione di contenuti.

## <a name="prerequisites"></a>Prerequisiti

Sono necessari per completare l'esercitazione.

- Per completare questa esercitazione, è necessario un account Azure. 
    
    Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](/pricing/free-trial/?WT.mc_id=A261C142F). È possibile ottenere crediti che possono essere utilizzati per provare a utilizzare i servizi di Azure a pagamento. Anche dopo il crediti vengono utilizzati, è possibile mantenere l'account e utilizzo delle caratteristiche, ad esempio la caratteristica Web Apps nel servizio di Azure App e servizi Azure gratuiti.
- Sistemi operativi: Windows 8 o versione successiva, Windows 2008 R2, Windows 7.
- .NET framework 4.0 o versione successiva
- Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express) o versioni successive.


##<a name="download-sample"></a>Download di esempio

Accedere ed eseguire un campione da [qui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Creare un account di servizi multimediali di Windows Azure tramite il portale di Azure

La procedura descritta in questa sezione viene illustrato come creare un account AMS.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **+ nuova** > **Media + CDN** > **Servizi multimediali**.

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

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Configurare i punti finali flussi tramite il portale di Azure

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

##<a name="create-and-configure-a-visual-studio-project"></a>Creare e configurare un progetto Visual Studio

1. Creare una nuova applicazione Console c# in Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 SP1. Immettere il **nome**, **percorso**e **Nome soluzione**e quindi fare clic su **OK**.

2. Utilizzare il pacchetto NuGet [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) per installare **Azure Media servizi .NET SDK estensioni**.  Estensioni di file multimediali servizi .NET SDK è un insieme di metodi di estensione e le funzioni di supporto che verranno semplificare il codice e renderlo più semplice sviluppare con i servizi di supporto. Installazione di questo pacchetto, anche installa **Media Services.NET SDK** e aggiunge tutte le altre dipendenze necessari.

3. Aggiungere un riferimento a assembly Configuration. Questo assembly contiene la classe **System.Configuration.ConfigurationManager** utilizzato per accedere ai file di configurazione, ad esempio, App.

4. Aprire il file App (aggiungere file al progetto se non è stato aggiunto per impostazione predefinita) e aggiungere una sezione *appSettings* al file. Impostare i valori per i servizi multimediali di Windows Azure account e nome chiave account, come illustrato nell'esempio seguente. Per ottenere il nome dell'account e la chiave, accedere al [portale di Azure](https://portal.azure.com/) e selezionare il proprio account AMS. Scegliere **Impostazioni** > **tasti**. Le finestre di chiavi Gestisci Mostra il nome dell'account e chiavi primarie e secondarie viene visualizzato.

        <configuration>
        ...
          <appSettings>
            <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
            <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
          </appSettings>
          
        </configuration>

5. Sovrascrivere le istruzioni **using** esistenti all'inizio del file Program.cs con il codice riportato di seguito.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
        

6. Creare una nuova cartella all'interno della directory di progetti e copiare un file MP4 o WMV che si desidera codificare e flusso o il download gradualmente. In questo esempio viene utilizzato il percorso "C:\VideoFiles".

##<a name="connect-to-the-media-services-account"></a>Connettersi all'account di servizi multimediali

Quando si utilizza servizi multimediali con .NET, è necessario utilizzare la classe **CloudMediaContext** per la maggior parte dei servizi multimediali di attività di programmazione: la connessione all'account di servizi di supporto. creazione, aggiornamento, accesso ed eliminazione i seguenti oggetti: risorse, file di risorse, processi, criteri di accesso, Locator e così via.

Sovrascrivere la classe predefinita del programma con il codice riportato di seguito. Il codice viene illustrato come leggere i valori di connessione dal file di App e come creare l'oggetto **CloudMediaContext** per connettersi ai servizi di supporto. Per ulteriori informazioni sulla connessione a servizi multimediali, vedere [connessione a servizi multimediali con Media Services SDK per .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

La funzione **Main** chiama metodi che verranno definiti più avanti in questa sezione.

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

##<a name="create-a-new-asset-and-upload-a-video-file"></a>Creare un nuovo bene e caricare un file video

In servizi multimediali, è possibile caricare (o acquisizione) i file digitali in una risorsa. Entità **Asset** possono contenere video, audio, immagini, raccolte anteprime, testo, tracce e sottotitoli codificati file (e i metadati relativi a questi file.)  Una volta i file vengono caricati, il contenuto è archiviato in modo sicuro nel cloud per l'ulteriore elaborazione e trasmissione. I file del bene denominati **File di risorse**.

Metodo **UploadFile** definito sotto chiamate **CreateFromFile** (definito in .NET SDK estensioni). **CreateFromFile** crea una nuova attività in cui il file di origine specificato verrà caricato.

Il metodo **CreateFromFile** accetta **AssetCreationOptions** che consente di specificare una delle opzioni di creazione di risorse seguenti:

- **Nessuno** - crittografia non viene utilizzata. Si tratta del valore predefinito. Si noti che, quando si utilizza questa opzione, il contenuto non protetta durante il transito o inattivi in archiviazione.
Se si prevede di eseguire un MP4 tramite download graduale, usare questa opzione.
- **StorageEncrypted** - usare questa opzione per crittografare il contenuto Cancella localmente utilizzando la crittografia-256 bit avanzate crittografia AES (Standard), che quindi carica allo spazio di archiviazione di Azure in cui è archiviato crittografata inattivi. Risorse è protetti con crittografia di archiviazione sono automaticamente in formato non crittografati e inseriti in un file system crittografato prima di codifica e facoltativamente nuovamente crittografati prima di caricare nuovamente come una nuova risorsa di output. Il caso di utilizzo principale per la crittografia di spazio di archiviazione è quando si desidera proteggere i file multimediali di input di alta qualità con crittografia inattivi su disco.
- **CommonEncryptionProtected** - usare questa opzione se si sta caricando contenuto già crittografato e protetto con PlayReady DRM (ad esempio Smooth Streaming protetto con PlayReady DRM) o la crittografia comuni.
- **EnvelopeEncryptionProtected** : usare questa opzione se si sta caricando HLS crittografate con AES. Si noti che i file di essere stati codificati e crittografati da trasformare Manager.

Il metodo **CreateFromFile** consente di specificare un callback per segnalare l'avanzamento di caricamento del file.

Nell'esempio seguente è specificare **Nessuno** per le opzioni di risorse.

Aggiungere il metodo seguente alla classe Program.

    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }


##<a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Codificare il file di origine in una serie di file MP4 adattata velocità

Dopo il caricamento di risorse in servizi multimediali, può essere media codificato, transmuxed dal apposto filigrana e così via, prima che venga recapitata ai client. Queste attività sono pianificate ed eseguite su più istanze del ruolo di sfondo per garantire prestazioni e disponibilità elevate. Queste attività sono denominate processi e ogni processo è composto da attività atomica che il lavoro effettivo nel file di risorse.

Come è stato detto in precedenza, quando si lavora con servizi multimediali di Windows Azure, uno dei motivi più comuni offre velocità adattata streaming per i clienti. Servizi multimediali in modo dinamico possibile comprimere una serie di file di velocità adattata MP4 in uno dei formati seguenti: HTTP Live Streaming (HLS), Smooth Streaming, MPEG trattino e unità 10K (per solo licenziatari a questo punto Adobe/accesso).

Per sfruttare imballaggio dinamico, è necessario eseguire le operazioni seguenti:

- Codificare o trascodifica il mezzanine (origine) file in una serie di file Smooth Streaming velocità adattata o adattata velocità MP4.  
- È possibile ottenere almeno un'unità di trasmissione per l'endpoint flusso dal quale si prevede di recapito del contenuto.

Il codice seguente viene illustrato come inviare un processo di codifica. Il processo contiene un'attività specifica effettuare il file mezzanine in un set di velocità adattata MP4s utilizza **Media Encoder Standard**. Il codice invia il lavoro e che venga fino al completamento.

Una volta completato il processo, sarà flusso le risorse o il download gradualmente file MP4 creati in seguito ricorrere.
Si noti che non è necessario che più di 0 unità flusso risieda per gradualmente scaricare file MP4.

Aggiungere il metodo seguente alla classe Program.

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {
    
        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.
    
        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "H264 Multiple Bitrate 720p",
            asset,
            "Adaptive Bitrate MP4",
            options);
    
        Console.WriteLine("Submitting transcoding job...");
    
    
        // Submit the job and wait until it is completed.
        job.Submit();
    
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;
    
        Console.WriteLine("Transcoding job finished.");
    
        IAsset outputAsset = job.OutputMediaAssets[0];
    
        return outputAsset;
    }

##<a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Pubblicare la risorsa e ottenere l'URL per il download di flusso e avanzato

Per flusso o il download di un bene, è innanzitutto necessario "pubblicarlo" mediante la creazione di un indicatore di posizione. Indicatori consentono di accedere ai file contenuti nel bene. Servizi multimediali sono supportati due tipi di indicatori: OnDemandOrigin Locator, utilizzato per il flusso multimediale (ad esempio MPEG trattino, HLS o Smooth Streaming) e indicatori di Access della firma (SA), utilizzato per il download di file multimediali.

Dopo aver creato il Locator, è possibile creare gli URL che consentono di flusso o scaricare i file.


Un URL di trasmissione per Smooth Streaming è nel formato seguente:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Un URL di trasmissione HLS è nel formato seguente:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Un URL di trasmissione per MPEG trattino è nel formato seguente:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Un URL SA utilizzato per scaricare i file sono nel formato seguente:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Estensioni dei file multimediali servizi .NET SDK forniscono metodi di supporto comodo che restituiscono URL formattato risorsa pubblicato.

Il codice seguente usa .NET SDK Extensions per creare indicatori e per ottenere streaming e download graduale URL. Inoltre, il codice viene illustrato come scaricare i file in una cartella locale.

Aggiungere il metodo seguente alla classe Program.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

##<a name="test-by-playing-your-content"></a>Testare la riproduzione di contenuti  

Dopo aver eseguito il programma definito nella sezione precedente, nella finestra della console verranno visualizzati gli URL simili al seguente.

URL di trasmissione adattativo:

Smooth Streaming

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG TRATTINO

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

URL di download graduale (audio e video).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Per eseguire il flusso video, utilizzare [i servizi multimediali di Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Per testare il download graduale, incollare un URL in un browser (ad esempio, Internet Explorer, Chrome o Safari).


##<a name="next-steps-media-services-learning-paths"></a>Passaggi successivi: Servizi multimediali percorsi formativi

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


### <a name="looking-for-something-else"></a>Non che si stava cercando?

Se questo argomento non contiene risultati desiderati, non è un elemento o in un altro modo non soddisfa le proprie esigenze, specificare un feedback utilizzando il thread Disqus riportata di seguito.


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Portal]: http://manage.windowsazure.com/
