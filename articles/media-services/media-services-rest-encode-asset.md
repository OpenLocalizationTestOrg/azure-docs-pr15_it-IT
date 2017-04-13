<properties 
    pageTitle="Come codificare un bene mediante Media Encoder Standard | Microsoft Azure" 
    description="Informazioni su come utilizzare Media Encoder Standard per la codifica di contenuti multimediali sui servizi di supporto. Esempi di codice utilizzano API REST." 
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
    ms.date="09/19/2016"
    ms.author="juliako"/>


#<a name="how-to-encode-an-asset-using-media-encoder-standard"></a>Come codificare un bene mediante Media Encoder Standard


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
- [RESTO](media-services-rest-encode-asset.md)
- [Portale](media-services-portal-encode.md)

##<a name="overview"></a>Panoramica
Per distribuire video digitali tramite internet Comprimi gli elementi multimediali. File video digitali sono molto grandi e potrebbero essere troppo grandi per offrire tramite internet o per i dispositivi visualizzare correttamente i clienti. Codifica è il processo di compressione audio e video in modo che i clienti possono visualizzare i file multimediali.

Processi di codifica sono una delle operazioni di trasformazione più comune in servizi di supporto. Creare processi di codifica per convertire i file multimediali da una codifica a un'altra. Quando si codifica, è possibile utilizzare il codificatore di predefiniti servizi multimediali (Media Encoder Standard). È inoltre possibile utilizzare un codificatore fornito da un partner di servizi multimediali; Encoder di terze parti sono disponibili tramite Azure Marketplace. È possibile specificare i dettagli della codifica attività utilizzando stringhe preimpostate definite per il codificatore o file di configurazione preimpostata. Per visualizzare i tipi di predefiniti disponibili, vedere [Attività predefiniti per Media Encoder Standard](http://msdn.microsoft.com/library/mt269960).

Ogni processo può avere una o più attività in base al tipo di elaborazione che si desidera eseguire. Tramite l'API REST, è possibile creare processi e le attività correlate in uno dei due modi:

- Attività possono essere definito all'interno del testo tramite la proprietà di spostamento di attività in entità di processo, o
- durante l'elaborazione batch OData.


È consigliabile codificare sempre i file mezzanine in una velocità adattata MP4 impostare e quindi si converte il set per il formato desiderato grazie [Imballaggio dinamico](media-services-dynamic-packaging-overview.md). Per sfruttare imballaggio dinamico, è prima necessario ottenere almeno un'unità di trasmissione su richiesta per l'endpoint flusso dal quale si prevede di recapito del contenuto. Per ulteriori informazioni, vedere [come servizi multimediali di scala](media-services-portal-manage-streaming-endpoints.md).

Se le risorse di output sono lo spazio di archiviazione crittografato, è necessario configurare i criteri di recapito bene. Per ulteriori informazioni vedere [configurazione bene recapito criteri](media-services-rest-configure-asset-delivery-policy.md).


>[AZURE.NOTE]Prima di fare riferimento a processori multimediali, verificare di avere il supporto corretto processore ID. Per ulteriori informazioni, vedere [Ottenere processori multimediali](media-services-rest-get-media-processor.md).

##<a name="create-a-job-with-a-single-encoding-task"></a>Creare un processo con una singola attività codifica

>[AZURE.NOTE] Quando si utilizza l'API REST di servizi multimediali, le considerazioni seguenti:
>
>Quando si accede a entità in servizi di supporto, è necessario impostare i campi specifici dell'intestazione e i valori nelle convocazioni HTTP. Per ulteriori informazioni, vedere [il programma di installazione per lo sviluppo di API REST servizi multimediali](media-services-rest-how-to-use.md).

>Dopo aver completato la connessione a https://media.windows.net, si riceverà un reindirizzamento 301 specificando un altro URI di servizi di supporto. È necessario effettuare chiamate successive al nuovo URI come descritto nella [connessione ai servizi multimediali utilizzando l'API REST](media-services-rest-connect-programmatically.md).
>
>Quando si utilizzando JSON e specificando per utilizzare la parola chiave **__metadata** nella richiesta (ad esempio, per fa riferimento a un oggetto collegato) è necessario impostare l'intestazione **accetta** in [formato JSON dettagliato](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): accetta: applicazione/json; odata = dettagliato.

Nell'esempio seguente viene illustrato come creare e pubblicare un processo con un che attività impostata su codificare un video in una specifica risoluzione e qualità. Durante la codifica con Media Encoder Standard, è possibile utilizzare impostazioni predefinite di configurazione di attività specificati [qui](http://msdn.microsoft.com/library/mt269960).

Richiesta:

POST https://media.windows.net/API/Jobs tipo di contenuto HTTP/1.1: applicazione/json; odata = accetta dettagliata: applicazione/json; odata = DataServiceVersion dettagliato: MaxDataServiceVersion 3.0: x-ms-versione 3.0: autorizzazione 2.11: titolare <token value> 
 x-ms-client-richiesta-id: 00000000-0000-0000-0000-000000000000 Host: media.windows.net

    
    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Multiple Bitrate 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Risposta:
    
    HTTP/1.1 201 Created

    . . . 

###<a name="set-the-output-assets-name"></a>Impostare il nome della risorsa output

Nell'esempio seguente viene illustrato come impostare l'attributo assetName:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

##<a name="considerations"></a>Considerazioni

- Proprietà TaskBody necessario utilizzare XML letterale per definire il numero di input o output risorse che verranno utilizzate dall'attività. L'argomento attività contiene la definizione dello Schema XML per i dati XML.
- Nella definizione TaskBody ogni interna valore <inputAsset> e <outputAsset> devono essere impostate come JobInputAsset(value) o JobOutputAsset(value).
- Un'attività può contenere più risorse di output. Uno JobOutputAsset(x) può essere usata solo una volta come esito di un'attività in un processo.
- È possibile specificare JobInputAsset o JobOutputAsset come una risorsa di input di un'attività.
- Attività non devono formano un ciclo.
- Il parametro del valore che si passa a JobInputAsset o JobOutputAsset rappresenta il valore di indice per una risorsa. Le attività correnti sono definite nelle proprietà di navigazione InputMediaAssets e OutputMediaAssets nella definizione di entità del processo. 
- Poiché servizi multimediali di si basa su OData v3, i singoli beni insiemi di proprietà di spostamento InputMediaAssets e OutputMediaAssets vengono fatto riferimento tramite un "__metadata: uri" coppia nome-valore.
- InputMediaAssets corrisponde a uno o più attività è stato creato in servizi di supporto. OutputMediaAssets vengono creati dal sistema. Una risorsa esistente non fanno riferimento.
- OutputMediaAssets può essere denominata utilizzando l'attributo assetName. Se l'attributo non è presente, quindi sul nome della OutputMediaAsset sarà qualsiasi il valore di testo interno del <outputAsset> elemento è con un suffisso del valore di nome processo o il valore di Id di processo (nel caso in cui non è definita la proprietà Name). Ad esempio, se si imposta un valore per assetName a "Campione", la proprietà di nome OutputMediaAsset da essere impostata su "Esempio". Tuttavia, se non è stato impostato un valore per assetName, ma il nome del processo "NewJob" è stata impostata, il nome OutputMediaAsset sarebbe "_NewJob JobOutputAsset (valore)". 


##<a name="create-a-job-with-chained-tasks"></a>Creare un processo attività concatenate

In molti scenari di applicazioni gli sviluppatori desiderano creare una serie di attività di elaborazione. Servizi di supporto, è possibile creare una serie di attività collegate. Ogni attività consente di eseguire i passaggi di elaborazione diversi e utilizzare processori multimediali diversi. Le attività concatenate possono consegnare una risorsa da un'attività a un'altra, eseguire una sequenza lineare di attività sulla bene. Tuttavia, le attività eseguite in un processo non è necessario essere in una sequenza. Quando si crea un'attività collegata, gli oggetti **ITask** concatenati vengono creati in un singolo oggetto **IJob** .

>[AZURE.NOTE] Attualmente non è disponibile un limite di 30 attività per ogni processo. Se è necessario per bicicletta più di 30 attività, creare più di un processo per contenere le attività.


    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


###<a name="considerations"></a>Considerazioni

Per abilitare concatenazione attività:

- Un processo deve avere almeno 2 attività
- Devono essere presenti almeno un'attività a cui input è output di un'altra attività nel processo.

## <a name="use-odata-batch-processing"></a>Utilizzare l'elaborazione batch OData 

Nell'esempio seguente viene illustrato come utilizzare l'elaborazione batch OData per creare un processo e attività. Per informazioni sull'elaborazione batch, vedere [Elaborazione Batch Open Data Protocol (OData)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).
 
    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net
    
    
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d
    
    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    
    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}
    
    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    POST https://media.windows.net/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    
    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
 


## <a name="create-a-job-using-a-jobtemplate"></a>Creare un processo utilizzando un JobTemplate


Durante l'elaborazione di più risorse utilizzando un insieme comune di attività, JobTemplates sono utili per specificare le attività predefiniti, ordine delle attività e così via.

Nell'esempio seguente viene illustrato come creare un JobTemplate con un elemento in linea TaskTemplate definiti. Il TaskTemplate utilizza la Media Encoder Standard come la MediaProcessor codificare file di risorse. Tuttavia, altri MediaProcessors potrebbero essere utilizzate anche. 


    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net

    
    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]A differenza di altre entità di servizi di supporto, è necessario definire un nuovo identificatore GUID per ogni TaskTemplate e inserirla nelle taskTemplateId e proprietà Id nel corpo della richiesta. Lo schema di identificazione del contenuto deve seguire lo schema descritto in identificare Azure Media Services entità. Inoltre, JobTemplates non possono essere aggiornati. Se, tuttavia, è necessario creare una nuova con le modifiche aggiornate.
 

In caso contrario, viene restituita la risposta seguente:
    
    HTTP/1.1 201 Created
    
    . . .


Nell'esempio seguente viene illustrato come creare un processo che fa riferimento un JobTemplate Id:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net

    
    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
     

In caso contrario, viene restituita la risposta seguente:
    
    HTTP/1.1 201 Created
    
    . . . 



##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-steps"></a>Passaggi successivi
Dopo aver scoperto come creare un processo per codificare un assset, passare all'argomento [Come per controllare processi lo stato di avanzamento con i servizi di supporto](media-services-rest-check-job-progress.md) .


##<a name="see-also"></a>Vedere anche

[Ottenere processori multimediali](media-services-rest-get-media-processor.md)
