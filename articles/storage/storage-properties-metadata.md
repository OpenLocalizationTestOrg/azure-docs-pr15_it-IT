<properties
    pageTitle="Impostare e recuperare proprietà e dei metadati per gli oggetti in archiviazione Azure | Microsoft Azure"
    description="Archiviare metadati personalizzati per gli oggetti nel sistema di archiviazione Azure e impostare e recuperare proprietà-sistema."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="set-and-retrieve-properties-and-metadata"></a>Impostare e recuperare proprietà e dei metadati #

## <a name="overview"></a>Panoramica

Oggetti in proprietà-sistema di supporto per lo spazio di archiviazione di Azure e i metadati definite dall'utente, oltre ai dati che contengono:

*   **Proprietà-sistema.** Proprietà-sistema sono presenti in ogni risorsa lo spazio di archiviazione. Alcuni di essi possono essere letti o impostati, mentre altre sono di sola lettura. Dietro le quinte, alcune proprietà-sistema corrispondono a determinati intestazioni HTTP standard. La raccolta di client di archiviazione Azure mantiene tale funzionalità.  

*   **Metadati definiti dall'utente.** Metadati definiti dall'utente sono basato su metadati che specificano in una determinata risorsa, in forma di una coppia di valore di nome. È possibile utilizzare i metadati per memorizzare valori aggiuntivi con una risorsa di archiviazione. Questi valori siano relativi a solo le proprie esigenze e non influiscono comportamento della risorsa.  

Recupero di valori di proprietà e i metadati di una risorsa di archiviazione è un processo in due passaggi. Prima di poter leggere questi valori, è necessario recuperare in modo esplicito loro utilizzando il metodo **FetchAttributes** .

> [AZURE.IMPORTANT] I valori di proprietà e i metadati per una risorsa di archiviazione non vengono popolati a meno che non si chiama uno dei metodi **FetchAttributes** . 

## <a name="setting-and-retrieving-properties"></a>Impostazione e il recupero delle proprietà

Per recuperare i valori di proprietà, chiamare il metodo **FetchAttributes** nel blob o contenitore per popolare le proprietà e quindi leggere i valori.

Per impostare proprietà su un oggetto, specificare il valore di proprietà e quindi il metodo **SetProperties** .

Nell'esempio seguente viene creato un contenitore e scrive alcuni dei valori delle proprietà in una finestra della console:

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    
    //Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

## <a name="setting-and-retrieving-metadata"></a>Impostazione e il recupero dei metadati

È possibile specificare i metadati come uno o più coppie di valore di nome a una risorsa blob o contenitore. Per impostare i metadati, aggiungere coppie di nome per la raccolta di **metadati** per la risorsa, quindi il metodo **SetMetadata** consente di salvare i valori per il servizio.

> [AZURE.NOTE] Il nome dei metadati deve rispettare le convenzioni di denominazione per gli identificatori di c#.
 
Nell'esempio seguente imposta metadati in un contenitore. Un valore è impostato con metodo **Add** dell'insieme. L'altro valore è impostato utilizzando la sintassi implicito chiave/valore. Entrambe sono valide.

    public static void AddContainerMetadata(CloudBlobContainer container)
    {
        //Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        //Set the container's metadata.
        container.SetMetadata();
    }

Per recuperare i metadati, chiamare il metodo **FetchAttributes** nel blob o contenitore per popolare l'insieme di **metadati** , quindi leggere i valori, come illustrato nell'esempio riportato di seguito.

    public static void ListContainerMetadata(CloudBlobContainer container)
    {
        //Fetch container attributes in order to populate the container's properties and metadata.
        container.FetchAttributes();

        //Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }

## <a name="see-also"></a>Vedere anche  

- [Raccolta di Client di archiviazione Azure per riferimento .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [Azure libreria Client lo spazio di archiviazione per il pacchetto di .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) 
