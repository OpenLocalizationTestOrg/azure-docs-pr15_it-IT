<properties
    pageTitle="L'indicizzazione di file multimediali con indicizzatore Media Azure"
    description="Azure Media indicizzatore consente di rendere il contenuto dei file multimediali che supportano le ricerche e per generare una trascrizione full-text per sottotitoli codificati e parole chiave. In questo argomento viene illustrato come utilizzare indicizzatore Media."
    services="media-services"
    documentationCenter=""
    authors="Asolanki"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/12/2016"   
    ms.author="adsolank;juliako;johndeu"/>


# <a name="indexing-media-files-with-azure-media-indexer"></a>L'indicizzazione di file multimediali con indicizzatore Media Azure


Azure Media indicizzatore consente di rendere il contenuto dei file multimediali che supportano le ricerche e per generare una trascrizione full-text per sottotitoli codificati e parole chiave. È possibile elaborare file uno multimediale o più file multimediali in un batch.  

>[AZURE.IMPORTANT] Durante l'indicizzazione del contenuto, assicurarsi di usare i file multimediali che hanno molto chiara sintesi e riconoscimento vocale (senza musica di sottofondo, rumore, effetti o fruscio microfono). Alcuni esempi di contenuto appropriato sono: registrato riunioni, lezioni o presentazioni. Il contenuto seguente potrebbe non essere utilizzato per indicizzare: filmati, programmi TV, tutti gli elementi presenti con audio misto e gli effetti audio, in modo non corretto registrato contenuto con rumore (fruscio).


Un processo di indicizzazione può generare l'output seguenti:

- Chiuso didascalia file nei formati seguenti: **SAMI**, **TTML**e **WebVTT**.

    File di sottotitoli codificati includono un tag denominato Recognizability, quali punteggi come riconoscibile sintesi e riconoscimento vocale del video di origine è un processo di indicizzazione in base.  È possibile usare il valore di Recognizability ai file di output dello schermo per facilità di utilizzo. Un punteggio implica risultati indicizzazione scarsa qualità audio.
- File di parole chiave (XML).
- Audio indicizzazione file blob (AIB) per l'utilizzo con SQL server.

    Per ulteriori informazioni, vedere [Utilizzo dei file AIB con Azure Media indicizzatore e SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).


In questo argomento viene illustrato come creare i processi di indicizzazione da **indicizzare una risorsa** e **indicizzare più file**.

Per gli ultimi aggiornamenti di Azure Media indicizzatore, vedere [Servizi multimediali di blog](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Utilizzo dei file di configurazione e manifesto per attività di indicizzazione

È possibile specificare altri dettagli per le attività di indicizzazione utilizzando una configurazione di attività. Ad esempio, è possibile specificare i metadati da utilizzare per il file multimediali. Metadati viene utilizzato dal motore di lingua per espandere la terminologia e migliorano la precisione del riconoscimento vocale.  Si è anche possibile specificare i file di output desiderato.

È anche possibile elaborare contemporaneamente più file multimediali tramite un file manifesto.

Per ulteriori informazioni, vedere [Attività predefiniti per Azure Media indicizzatore](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Indice di un bene

Il metodo seguente consente di caricare un file multimediale come una risorsa e crea un processo per indicizzare le risorse.

Si noti che, se non viene specificato alcun file di configurazione, sul file multimediale verrà indicizzato con tutte le impostazioni predefinite.

    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }  
<!-- __ -->
### <a id="output_files"></a>File di output

Per impostazione predefinita, un processo di indicizzazione genera i file di output seguenti. I file verranno archiviati nel primo bene di output.

Quando sono presenti più di un file di supporto di input, indicizzatore verrà generato un file manifesto per l'output di processo, denominata 'JobResult.txt'. Per ogni file multimediale di input, AIB risultante, SAMI, TTML, WebVTT e i file di parole chiave, vengono in sequenza numerati e denominati utilizzando "Alias".

Nome del file | Descrizione
----------|------------
__InputFileName.aib__ | File blob indicizzazione audio. <br /><br /> File audio indicizzazione Blob (AIB) è un file binario che può essere eseguita la ricerca in Microsoft SQL server tramite ricerca full-text.  Il file AIB offre maggiori vantaggi rispetto file didascalia semplici, perché contiene alternative per ogni parola, consentendo un'esperienza di ricerca avanzata. <br/> <br/>Richiede l'installazione del componente aggiuntivo SQL indicizzatore in un computer in esecuzione Microsoft SQL server 2008 o versione successiva. Ricerca AIB utilizzando Microsoft SQL server full-text search offre risultati di ricerca più precisi rispetto alla ricerca i file di sottotitoli codificati generati da WAMI. In questo modo la AIB contiene parola alternative il suono simile mentre i file di sottotitoli codificati contengono la parola confidenza più alta per ogni segmento dell'audio. Se la ricerca di parole pronunciate è di importanza upmost, è consigliabile usare insieme AIB In con Microsoft SQL Server.<br/><br/> Per scaricare il componente aggiuntivo, fare clic su <a href="http://aka.ms/indexersql">Componenti aggiuntivi di SQL Azure Media indicizzatore</a>. <br/><br/>È anche possibile utilizzare i motori di ricerca, ad esempio Apache Lucene/Solr indicizzare semplicemente il video in base alle sottotitoli e i file XML di parole chiave, ma verrà generato meno accuratezza dei risultati della ricerca.
__InputFileName.smi__<br />__InputFileName.ttml__<br />__InputFileName.vtt__ |File di didascalia (CC) chiusi nei formati SAMI, TTML e WebVTT.<br/><br/>Possono essere utilizzati per rendere accessibile agli utenti con disability udito file audio e video.<br/><br/>File didascalia chiusi includono un tag denominato <b>Recognizability</b> quali punteggi in base a un processo di indicizzazione come riconoscibile sintesi e riconoscimento vocale nell'origine video è.  È possibile usare il valore di <b>Recognizability</b> ai file di output dello schermo per facilità di utilizzo. Un punteggio implica risultati indicizzazione scarsa qualità audio.
__InputFileName.kw.xml<br />InputFileName.info__ |File di parole chiave e informazioni. <br/><br/>File di parole chiave è un file XML che contiene parole chiave estratte da contenuto vocale con frequenza e informazioni di offset. <br/><br/>File di informazioni è un file di testo che contiene informazioni granulare ogni termine riconosciuto. La prima riga è speciale e contiene il punteggio Recognizability. Ogni riga successiva è riportato un elenco delimitato da tabulazione i dati seguenti: inizio, ora, data/ora fine, parola o frase, confidenza. Secondi sono indicati i tempi e la fiducia viene espresso come numero compreso tra 0-1. <br/><br/>Riga di esempio: "1,20 1,45 word 0.67" <br/><br/>Questi file possono essere utilizzati per diversi scopi, ad esempio, per eseguire analitica sintesi e riconoscimento vocale, o esposti motori di ricerca come Bing, Google o Microsoft SharePoint per i file multimediali più facilmente individuabili o persino usato per distribuire annunci più pertinenti.
__JobResult.txt__ |Manifesto di output, presenta solo quando l'indicizzazione più file, che contiene le informazioni seguenti:<br/><br/><table border="1"><tr><th>File di input</th><th>Alias</th><th>MediaLength</th><th>Errore</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/>



Se non indicizzati tutti i file di supporto di input, il processo di indicizzazione avrà esito negativo con codice errore 4000. Per ulteriori informazioni, vedere [codici di errore](#error_codes).

## <a name="index-multiple-files"></a>Indicizzare più file

Il metodo seguente carica più file multimediali come una risorsa e crea un processo per tutti questi file in un batch di indice.

Un file con estensione lst manifesto verrà create e il caricamento in asset. Il file manifesto contiene l'elenco di tutti i file di risorse. Per ulteriori informazioni, vedere [Attività predefiniti per Azure Media indicizzatore](https://msdn.microsoft.com/library/dn783454.aspx).

    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }

### <a name="partially-succeeded-job"></a>Processo eseguito parzialmente

Se non indicizzati tutti i file di supporto di input, il processo di indicizzazione avrà esito negativo con codice errore 4000. Per ulteriori informazioni, vedere [codici di errore](#error_codes).


Vengono generati gli stessi output (riusciti processi). È possibile fare riferimento al file manifesto di output per individuare i file di input vengono eseguiti, in base ai valori di colonna errore. File non verranno generati per file di input che non è riuscita, AIB risultante, SAMI, TTML, WebVTT e parole chiave.

### <a id="preset"></a>Impostazione predefinita di attività per indicizzatore Media Azure

Grazie a un'attività facoltativa preimpostata insieme all'attività, è possibile personalizzare l'elaborazione di Azure Media indicizzatore.  Di seguito viene descritto il formato di file xml di questa configurazione.

Nome | Richiedi | Descrizione
----|----|---
__input__ | FALSO | File di risorse che si desidera indicizzare.</p><p>Azure indicizzatore Media supporta i formati di file multimediali seguenti: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>È possibile specificare il nome del file (s) nell'attributo **nome** o **l'elenco** dell'elemento **input** (come illustrato di seguito). Se non si specifica il file di risorse da indice, viene selezionato il file principale. Se viene impostato alcun file risorsa primaria, il file prima del bene input verrà indicizzato.</p><p>Per specificare il nome del file di risorse, eseguire:<br />`<input name="TestFile.wmv">`<br /><br />È anche possibile indicizzare più bene file contemporaneamente (fino a 10). Per procedere come segue:<br /><br /><ol class="ordered"><li><p>Creare un file di testo (file manifesto) e assegnarle un'estensione lst. </p></li><li><p>Aggiungere un elenco di tutti i nomi di file di risorse del bene input al file manifesto. </p></li><li><p>Aggiungere file thanifest (caricamento) per la risorsa.  </p></li><li><p>Specificare il nome del file manifesto nell'attributo di elenco di input.<br />`<input list="input.lst">`</li></ol><br /><br />Nota: Se si aggiungono più di 10 file al file manifesto, il processo di indicizzazione avrà esito negativo con il codice di errore 2006.
__metadati__ | FALSO | Metadati per i file di risorse specificato utilizzati per l'adeguamento terminologia.  È utile per preparare l'ambiente indicizzatore facilmente riconoscibile parole terminologia non standard, ad esempio nomi propri.<br />`<metadata key="..." value="..."/>` <br /><br />È possibile fornire __i valori__ per predefinite __tasti__. Chiavi seguenti sono attualmente supportate:<br /><br />"titolo" e "descrizione" - utilizzata per l'adeguamento terminologia per modificare la lingua del modello per il lavoro e migliorare la precisione del riconoscimento vocale.  I valori inizializzare ricerche su Internet per trovare i documenti di testo in base al contesto appropriato, utilizzando il contenuto per migliorare il dizionario interno per la durata dell'attività indicizzazione.<br />`<metadata key="title" value="[Title of the media file]" />`<br />`<metadata key="description" value="[Description of the media file] />"`
__caratteristiche__ <br /><br /> Aggiunta della versione 1.2. Attualmente la funzionalità supportata sola è il riconoscimento vocale ("ripristino").| FALSO | La funzionalità di riconoscimento vocale sono i seguenti tasti di impostazioni:<table><tr><th><p>Chiave</p></th>     <th><p>Descrizione</p></th><th><p>Valore di esempio</p></th></tr><tr><td><p>Lingua</p></td><td><p>Linguaggio naturale per essere riconosciuti nel file multimediale.</p></td><td><p>Inglese, spagnolo</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>un elenco dei formati di didascalia output desiderata (se presente) separati da punti e virgola</p></td><td><p>ttml sami; webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Contrassegno booleano che specifica se un file AIB è necessario (per l'utilizzo con SQL Server e il cliente indicizzatore IFilter).  Per ulteriori informazioni, vedere <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">Utilizzo dei file AIB con Azure Media indicizzatore e SQL Server</a>.</p></td><td><p>True. FALSO</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Contrassegno booleano che specifica se non è un file XML di parole chiave.</p></td><td><p>True. False. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Contrassegno booleano che specifica se forzare didascalie complete (indipendentemente dal livello di confidenza).  </p><p>Valore predefinito è false, nel qual caso parole o frasi che inferiore a livello di sicurezza del 50% sono state omesse dall'output finale didascalia e sostituite sui puntini di sospensione ("…").  I puntini di sospensione sono utili per il controllo qualità di didascalia e il controllo.</p></td><td><p>True. False. </p></td></tr></table>

### <a id="error_codes"></a>Codici di errore

In caso di errore indicizzatore Media Azure segnalerà eseguire il backup di uno dei seguenti codici di errore:

Codice | Nome | Possibili cause
-----|------|------------------
2000 | Configurazione non valida | Configurazione non valida
2001 | Risorse di input non validi | Manca input attività o risorsa vuoto.
2002 | Manifesto non valido | Manifesto non contiene alcun dato o manifesto contiene elementi non validi.
2003 | Non è possibile scaricare file multimediali | URL non valido nel file manifesto.
2004 | Protocollo non supportato | Protocollo di file multimediali che URL non è supportato.
2005 | Tipo di file non supportati | Tipo di file di supporto di input non è supportata.
2006 | Troppi file di input | Sono disponibili più di 10 file nel manifesto di input.
3000 | Impossibile decodificare file multimediale | Codec di supporto non supportato <br/>o<br/> File multimediale danneggiata. <br/>o<br/> Nessun flusso audio di supporto di input.
4000 | L'indicizzazione batch parzialmente | Alcuni file multimediali di input non è riusciti a essere indicizzate. Per ulteriori informazioni, vedere <a href="#output_files">i file di Output</a>.
altri | Errori interni | Contattare il team di supporto. indexer@microsoft.com


## <a id="supported_languages"></a>Lingue supportate

Attualmente sono supportate le lingue inglese e spagnolo. Per ulteriori informazioni, vedere [il post di blog sulla versione 1.2](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## <a name="related-links"></a>Collegamenti correlati

[Panoramica di Analitica dei servizi Media Azure](media-services-analytics-overview.md)

[Utilizzo dei file AIB con indicizzatore Media Azure e SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[L'indicizzazione di file multimediali con l'anteprima dell'indicizzatore 2 Media Azure](media-services-process-content-with-indexer2.md)
