<properties 
    pageTitle="Operazioni di lunga di polling | Microsoft Azure" 
    description="In questo argomento viene illustrato come sondaggio operazioni di lunga." 
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
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="delivering-live-streaming-with-azure-media-services"></a>L'esecuzione di Live Streaming con i servizi multimediali di Azure

##<a name="overview"></a>Panoramica

Servizi multimediali di Microsoft Azure offre API che inviare richieste ai servizi di supporto per avviare operazioni (ad esempio: creare, avviare, interrompere o eliminare un canale). Queste operazioni sono lunga.

Media Services .NET SDK fornisce API che invia la richiesta e attendere il completamento dell'operazione (internamente, le API sono polling per stato operazione alcuni intervalli). Ad esempio, quando si chiama canale. Start (), viene restituito dopo che viene avviato il canale. È anche possibile usare la versione asincrona: attesa canale. StartAsync() (per informazioni su modello asincrono basato su attività, vedere [toccare](https://msdn.microsoft.com/library/hh873175(v=vs.110).aspx)). API che inviare una richiesta di operazione e quindi sondaggio per lo stato fino al completamento dell'operazione sono denominate "polling metodi". Questi metodi (in particolare la versione asincrono) sono consigliati per applicazioni rich client e/o servizi informazioni sullo stato.

Esistono scenari in cui un'applicazione non è possibile attendere per una richiesta http lunga e desidera sondaggio per lo stato di avanzamento di operazione manualmente. Un tipico esempio sarebbe un browser interazione con un servizio web senza informazioni sullo stato: quando il browser richiede di creare un canale, il servizio web avvia un'operazione lunga e restituisce l'ID di operazione nel browser. Il browser può essere chiesto quindi il servizio web per ottenere lo stato di operazione in base all'ID. Media Services .NET SDK fornisce API che sono utili per questo scenario. Queste API sono denominate "non polling metodi".
"Non polling metodi" sono i seguenti criteri di denominazione: inviare*invece*operazione (ad esempio SendCreateOperation). Inviare i metodi di operazione*invece*restituiscono l'oggetto **IOperation** ; l'oggetto restituito contiene informazioni che possono essere utilizzate per tenere traccia dell'operazione. I metodi Invia*invece*OperationAsync restituiscono **attività<IOperation>**.

Le classi seguenti supportano attualmente, non polling metodi: **canale**, **StreamingEndpoint**e **programma**.

Per un sondaggio di stato dell'operazione, utilizzare il metodo **GetOperation** sulla classe **OperationBaseCollection** . Utilizzare i seguenti intervalli per controllare lo stato di operazione: per le operazioni di **canale** e **StreamingEndpoint** , utilizzare 30 secondi. per le operazioni di **programma** , utilizzare 10 secondi.


##<a name="example"></a>Esempio

Nell'esempio seguente viene definita una classe denominata **ChannelOperations**. La definizione della classe può essere un punto di partenza per la definizione di classe del servizio web. Per semplicità, negli esempi seguenti utilizzano le versioni non asincrone dei metodi.

Nell'esempio illustrato anche come client di utilizzare questa classe.

###<a name="channeloperations-class-definition"></a>Definizione di classe ChannelOperations

    /// <summary> 
    /// The ChannelOperations class only implements 
    /// the Channel’s creation operation. 
    /// </summary> 
    public class ChannelOperations
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;
    
        public ChannelOperations()
        {
                _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
                _context = new CloudMediaContext(_cachedCredentials);    }
    
        /// <summary>  
        /// Initiates the creation of a new channel.  
        /// </summary>  
        /// <param name="channelName">Name to be given to the new channel</param>  
        /// <returns>  
        /// Operation Id for the long running operation being executed by Media Services. 
        /// Use this operation Id to poll for the channel creation status. 
        /// </returns> 
        public string StartChannelCreation(string channelName)
        {
            var operation = _context.Channels.SendCreateOperation(
                new ChannelCreationOptions
                {
                    Name = channelName,
                    Input = CreateChannelInput(),
                    Preview = CreateChannelPreview(),
                    Output = CreateChannelOutput()
                });
    
            return operation.Id;
        }
    
        /// <summary> 
        /// Checks if the operation has been completed. 
        /// If the operation succeeded, the created channel Id is returned in the out parameter.
        /// </summary> 
        /// <param name="operationId">The operation Id.</param> 
        /// <param name="channel">
        /// If the operation succeeded, 
        /// the created channel Id is returned in the out parameter.</param>
        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
        public bool IsCompleted(string operationId, out string channelId)
        {
            IOperation operation = _context.Operations.GetOperation(operationId);
            bool completed = false;
    
            channelId = null;
    
            switch (operation.State)
            {
                case OperationState.Failed:
                    // Handle the failure. 
                    // For example, throw an exception. 
                    // Use the following information in the exception: operationId, operation.ErrorMessage.
                    break;
                case OperationState.Succeeded:
                    completed = true;
                    channelId = operation.TargetEntityId;
                    break;
                case OperationState.InProgress:
                    completed = false;
                    break;
            }
            return completed;
        }
    
    
        private static ChannelInput CreateChannelInput()
        {
            return new ChannelInput
            {
                StreamingProtocol = StreamingProtocol.RTMP,
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelPreview CreateChannelPreview()
        {
            return new ChannelPreview
            {
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelOutput CreateChannelOutput()
        {
            return new ChannelOutput
            {
                Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
            };
        }
    }

###<a name="the-client-code"></a>Il codice client

    ChannelOperations channelOperations = new ChannelOperations();
    string opId = channelOperations.StartChannelCreation("MyChannel001");
    
    string channelId = null;
    bool isCompleted = false;
    
    while (isCompleted == false)
    {
        System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
        isCompleted = channelOperations.IsCompleted(opId, out channelId);
    }
    
    // If we got here, we should have the newly created channel id.
    Console.WriteLine(channelId);
 


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
