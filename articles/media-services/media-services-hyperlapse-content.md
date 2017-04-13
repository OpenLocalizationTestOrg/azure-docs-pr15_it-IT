<properties
    pageTitle="I file di supporto Hyperlapse con Hyperlapse Media Azure | Microsoft Azure"
    description="Azure Hyperlapse Media crea video smussati tempo trascorso dal contenuto prima persona o azione fotocamera. In questo argomento viene illustrato come utilizzare indicizzatore Media."
    services="media-services"
    documentationCenter=""
    authors="asolanki"
    manager="johndeu"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/19/2016"  
    ms.author="adsolank"/>


# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>I file di supporto Hyperlapse con Hyperlapse Media Azure

Azure Hyperlapse Media è una Media processore (programma minimo) che consente di creare curve video tempo trascorso dal contenuto prima persona o azione fotocamera.  Pari livello basato sul cloud [Microsoft Research desktop Hyperlapse Pro](http://aka.ms/hyperlapse)e telefonica Hyperlapse Mobile Hyperlapse Microsoft per servizi multimediali di Windows Azure utilizza la scala enorme della piattaforma l'elaborazione di Azure Media servizi multimediali di scalare orizzontalmente ed eseguire blocco Hyperlapse l'elaborazione.

>[AZURE.IMPORTANT]Microsoft Hyperlapse è progettato per funzionare meglio in prima persona contenuto con una fotocamera mobile.  Sebbene ripresa della fotocamera è ancora possibile continua a funzionare, le prestazioni e qualità del processore Azure Media Hyperlapse Media non è possibile garantire per altri tipi di contenuto.  Per altre informazioni su Microsoft Hyperlapse per servizi multimediali di Windows Azure e vedere alcuni video di esempio, consultare il [post di blog introduttivo](http://aka.ms/azurehyperlapseblog) dall'anteprima pubblico.

Un Hyperlapse Media Azure processo accetta come input un file di risorse MP4, MOV o WMV insieme a un file di configurazione che specifica i frame del video devono essere tempo trascorso e per quali velocità (ad esempio di prime 10.000 frame x 2).  Il risultato è un rendering delle immagini stabilizzata e tempo trascorso dell'input video.

Per gli ultimi aggiornamenti di Azure Media Hyperlapse, vedere [Servizi multimediali di blog](https://azure.microsoft.com/blog/topics/media-services/).

## <a name="hyperlapse-an-asset"></a>Hyperlapse un bene

È innanzitutto necessario caricare un file di input desiderato in servizi multimediali di Windows Azure.  Per ulteriori informazioni sui concetti fondamentali per il durante il caricamento e la gestione di contenuto, leggere l' [articolo di gestione dei contenuti](media-services-portal-vod-get-started.md).

###  <a id="configuration"></a>Impostazione predefinita di configurazione per Hyperlapse

Dopo aver aggiunto il contenuto account servizi multimediali, sarà necessario creare il predefinito di configurazione.  Nella tabella seguente illustra i campi specificato dall'utente:

 Campo | Descrizione
-------|-------------
StartFrame|Il frame in cui deve iniziare l'elaborazione di Hyperlapse Microsoft.
NumFrames|Il numero di frame per l'elaborazione
Velocità|Fattori da utilizzare per velocizzare il video di input.

Di seguito è illustrato un esempio di un file di configurazione conforme allo standard in formato XML e JSON:

**XML predefinito:**

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>

**JSON predefinito:**

    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }

###  <a id="sample_code"></a>Microsoft Hyperlapse con AMS .NET SDK

Il metodo seguente consente di caricare un file multimediale come una risorsa e crea un processo con Azure Media Hyperlapse Media processore.

> [AZURE.NOTE] È necessario avere un CloudMediaContext nell'ambito con il nome "contesto" per poter utilizzare questo codice.  Per ulteriori informazioni, leggere l' [articolo di gestione dei contenuti](media-services-dotnet-get-started.md).

> [AZURE.NOTE] L'argomento stringa "hyperConfig" deve essere una configurazione conforme preimpostata JSON o XML come descritto in precedenza.

bool statico RunHyperlapseJob (stringa input, output della stringa, stringa hyperConfig) {/ / creare bene con bene IAsset file di input = contesto. Risorse. CreateAssetAndUploadSingleFile (input, "Input Hyperlapse", AssetCreationOptions.None);

attirare l'attenzione istanze del Pannello di gestione di Azure Media Hyperlapse Pannello di gestione IMediaProcessor = context. MediaProcessors. GetLatestMediaProcessorByName ("Media Azure Hyperlapse");

creare processi con processo IJob Hyperlapse = context. Processi. Creare (Format ("Hyperlapse {0}", input));

Se (String.IsNullOrEmpty(hyperConfig)) {/ / configurazione non può essere restituito false vuota;}

hyperConfig = File.ReadAllText(hyperConfig);

HyperlapseTask ITask = processo. Tasks.AddNew ("Hyperlapse attività", pannello di gestione, hyperConfig, TaskOptions.None); hyperlapseTask.InputAssets.Add(asset); hyperlapseTask.OutputAssets.AddNew ("Hyperlapse output", AssetCreationOptions.None);


processo. Submit ();

Creare la stampa lo stato di avanzamento e le query attività attività progressPrintTask = Task(() Nuovo = > {

IJob jobQuery = null; eseguire {var progressContext = contesto; jobQuery = progressContext.Jobs. In (j = > j.Id = = processo. ID). First (); WriteLine (stringa. Formato ("\t \t {1 \} {0} {2}", Now, jobQuery.State, jobQuery.Tasks[0]. Stato di avanzamento)); Thread.Sleep(10000); } mentre (jobQuery.State! = JobState.Finished & & jobQuery.State! = JobState.Error & & jobQuery.State! = JobState.Canceled); }); progressPrintTask.Start();

            Task progressJobTask = job.GetExecutionProgressTask(
                                                 CancellationToken.None);
            progressJobTask.Wait();

            // If job state is Error, the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                Console.WriteLine(string.Format("Error: {0}. {1}",
                                                error.Code,
                                                error.Message));  
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }

### <a id="file_types"></a>Tipi di File supportati

- MP4
- MOV
- WMV



##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="related-links"></a>Collegamenti correlati

[Panoramica di Analitica dei servizi Media Azure](media-services-analytics-overview.md)

[Demo di elementi multimediali Analitica Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
