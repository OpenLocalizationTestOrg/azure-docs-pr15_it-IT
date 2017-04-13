
<properties 
    pageTitle="Gestione delle risorse ed entità correlate con i servizi di supporto SDK .NET" 
    description="Informazioni su come gestire le attività ed entità correlate con Media Services SDK per .NET." 
    authors="juliako" 
    manager="dwrede" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016"
    ms.author="juliako"/>


#<a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Gestione delle risorse ed entità correlate con i servizi di supporto SDK .NET


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-manage-entities.md)
- [RESTO](media-services-rest-manage-entities.md)


In questo argomento viene illustrato come completare le attività di gestione dei servizi multimediali seguenti:

- Ottenere un riferimento di risorse 
- Ottenere un riferimento di processo 
- Elenco di tutte le risorse 
- Elenca i processi e risorse 
- Elenco tutti i criteri di accesso 
- Elencare tutti Locator
- Enumerazione di raccolte di grandi dimensioni di entità
- Eliminazione di un bene 
- Eliminare un processo 
- Eliminare un criterio di accesso 

##<a name="prerequisites"></a>Prerequisiti 

Vedere [configurare l'ambiente](media-services-set-up-computer.md)

##<a name="get-an-asset-reference"></a>Ottenere un riferimento di risorse

Un'attività frequente è per ottenere un riferimento a una risorsa esistente in servizi di supporto. Nell'esempio seguente mostra come è possibile ottenere un riferimento di risorse dalla raccolta risorse del server contesto oggetto, in base a una risorsa ID.
Nell'esempio seguente viene utilizzata una query Linq per ottenere un riferimento a un oggetto IAsset esistente.

    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();
    
        return asset;
    }

##<a name="get-a-job-reference"></a>Ottenere un riferimento di processo

Quando si lavora con attività nel codice servizi multimediali di elaborazione, è spesso necessario ottenere un riferimento a un processo esistente in base a un ID. Nell'esempio seguente viene illustrato come ottenere un riferimento a un oggetto IJob dalla raccolta processi.
Visualizzazione potrebbe essere necessario ottenere un riferimento di processo all'avvio di un processo di codifica lunga ed è necessario controllare lo stato del processo su un thread. In casi come questo, quando viene restituito da un thread, è necessario recuperare un riferimento a un processo di aggiornamento.

    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();
    
        return job;
    }

##<a name="list-all-assets"></a>Elenco di tutte le risorse

Man mano che aumenta il numero dei beni che si dispone di spazio di archiviazione, può essere utile visualizzare un elenco delle risorse. Nell'esempio seguente viene illustrato come scorrere l'insieme di risorse dell'oggetto di contesto server. Con ogni risorsa, nell'esempio di codice scritto anche alcuni dei relativi valori di proprietà alla console. Ad esempio, ogni risorsa può contenere molti file multimediali. Nell'esempio scrive tutti i file associati a ogni risorsa.



    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);
    
        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();
    
        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");
    
            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }
    
        // Display output in console.
        Console.Write(builder.ToString());
    }

##<a name="list-jobs-and-assets"></a>Elenca i processi e risorse

Un'attività correlata importante consiste nel risorse elenco con il loro lavoro associato in servizi di supporto. Nell'esempio seguente viene illustrato come ogni oggetto IJob dell'elenco e quindi per ogni processo Visualizza le proprietà relative al processo, tutte le attività correlate, tutti di input attività e tutte le risorse di output. Il codice in questo esempio può essere utile per molte altre attività. Ad esempio, se si desidera elencare i beni di output da uno o più processi codifica è stata eseguita in precedenza, il codice viene illustrato come accedere l'output. Quando si dispone di un riferimento a una risorsa di output, quindi è possibile distribuire il contenuto ad altri utenti o applicazioni tramite download o fornendo gli URL. 

Per ulteriori informazioni sulle opzioni per l'esecuzione di attività, vedere [Risorse recapitare con Media Services SDK per .NET](media-services-deliver-streaming-content.md).

    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);
    
        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();
    
        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");
    
    
            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }
    
            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {
    
                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }
    
            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }
    
        }
    
        // Display output in console.
        Console.Write(builder.ToString());
    }

##<a name="list-all-access-policies"></a>Elenco tutti i criteri di accesso

Servizi di supporto, è possibile definire un criterio di accesso in una risorsa o i file. Un criterio di accesso consente di definire le autorizzazioni per un file o di un bene (tipo di accesso e la durata). In codice servizi multimediali, in genere è possibile definire un criterio di accesso per la creazione di un oggetto IAccessPolicy e quindi associarlo a una risorsa esistente. Creare un oggetto ILocator, che consente di fornire accesso diretto alle risorse di servizi di supporto. Progetto di Visual Studio relativa a questa serie di documentazione contenuti diversi esempi di codice che illustrano come creare e assegnare criteri di accesso e indicatori alle risorse.

Nell'esempio seguente viene illustrato come elencare tutti i criteri di accesso sul server e viene visualizzato il tipo di autorizzazioni associate a ogni. Un altro modo utile per visualizzare i criteri di accesso è per visualizzare un elenco di tutti gli oggetti ILocator sul server e quindi per ogni locator, è possibile elencare i criteri di accesso associati utilizzando la relativa proprietà AccessPolicy.

    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");
    
        }
    }

##<a name="list-all-locators"></a>Elencare tutti Locator

Un indicatore di posizione è un URL che fornisce un percorso diretto per accedere a una risorsa, insieme alle autorizzazioni per la risorsa definiti dal criterio di accesso associati del locator. Ogni risorsa può avere un insieme di oggetti ILocator associata nella relativa proprietà Locator. Il contesto server è installato anche un insieme di indicatori che contiene tutti Locator.

Nell'esempio seguente sono elencati tutti Locator nel server. Per ogni locator che mostra l'Id per il relativo criterio entrate e access. Visualizza anche il tipo di autorizzazioni, la data di scadenza e il percorso completo per la risorsa.

Si noti che il percorso di individuazione di un bene solo un URL di base per la risorsa. Per creare un percorso diretto ai singoli file che è possibile individuare un utente o un'applicazione, il codice necessario aggiungere il percorso del file specifici per il percorso locator. Per ulteriori informazioni su come eseguire questa operazione, vedere l'argomento [Recapitare beni con Media Services SDK per .NET](media-services-deliver-streaming-content.md).

    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }

## <a name="enumerating-through-large-collections-of-entities"></a>Enumerazione di raccolte di grandi dimensioni di entità

Quando si eseguono query entità, non esiste un limite di 1000 entità restituite contemporaneamente perché pubblico resto v2 limita i risultati della query ai risultati di 1000. È necessario utilizzare ignorare i commenti durante l'enumerazione raccolte di grandi dimensioni di entità. 
    
La funzione seguente consente di scorrere tutti i processi l'Account di servizi di supporto fornito. Servizi multimediali restituisce 1000 processi processi insieme. Utilizza la funzione di salta e prendere per assicurarsi che tutti i processi vengono enumerati (nel caso in cui si dispone di più di 1000 processi nell'account).
    
    static void ProcessJobs()
    {
        try
        {
    
            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;
    
            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }
    
                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }

##<a name="delete-an-asset"></a>Eliminazione di un bene

Nell'esempio seguente elimina una risorsa.

    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();
    
        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");
    
    }

##<a name="delete-a-job"></a>Eliminare un processo

Per eliminare un processo, è necessario verificare lo stato del processo, come indicato nella proprietà State. Processi di fine o all'annullamento possono essere eliminati, mentre i processi in altri Stati, ad esempio in coda, programmata o elaborazione, è necessario annullare prima di tutto e quindi possono essere eliminati.
Nell'esempio seguente mostra un metodo per l'eliminazione di un processo di controllo degli stati di processo ed eliminando quando lo stato è completato o annullato. Questo codice dipende dalla sezione precedente in questo argomento per ottenere un riferimento a un processo: ottenere un riferimento di processo.

    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;
    
        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);
    
            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }
    
        }
    }


##<a name="delete-an-access-policy"></a>Eliminare un criterio di accesso

Nell'esempio seguente viene illustrato come ottenere un riferimento a un criterio di accesso in base a un criterio di Id e quindi eliminare il criterio.

    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();
    
        policy.Delete();
    
    }
    


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
