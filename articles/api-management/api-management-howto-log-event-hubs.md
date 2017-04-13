<properties 
    pageTitle="Registrazione di eventi a un hub di evento Azure in Azure API Management | Microsoft Azure" 
    description="Informazioni su come registrare gli eventi a un hub di evento Azure in Azure API Management." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Registrazione di eventi a un hub di evento Azure in Azure API Management

Azure hub di evento è un servizio di ingresso scalabilità dati che è in grado di acquisire milioni di eventi al secondo in modo che è possibile elaborare e analizzare grandi quantità di dati prodotti per i dispositivi connessi e le applicazioni. Hub evento funge da "porta anteriore" per una pipeline di eventi e una volta raccolti dati a un hub di evento, può essere trasformato e memorizzati tramite qualsiasi provider in tempo reale analitica o le schede batch/lo spazio di archiviazione. Hub evento separa produzione di un flusso di eventi dal consumo di questi eventi, in modo che gli utenti di evento possano accedere gli eventi nella propria pianificazione.

In questo articolo è correlato al video [Integrare Azure API Management con gli hub di eventi](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) e viene descritto come registrare gli eventi di gestione dell'API con gli hub evento Azure.

## <a name="create-an-azure-event-hub"></a>Creare un Hub evento Azure

Per creare un nuovo evento Hub, accesso al [portale classica Azure](https://manage.windowsazure.com) e fare clic su **Nuovo**->**Servizi App**->**Servizio Bus**->**Evento Hub**->**Creazione rapida**. Immettere il nome di un evento Hub e opzioni internazionali, selezionare un abbonamento e uno spazio dei nomi. Se non è stata creata in precedenza uno spazio dei nomi, è possibile creare uno digitando un nome nella casella di testo **Namespace** . Dopo aver configurate tutte le proprietà, fare clic su **Crea un nuovo evento Hub** per creare l'Hub di evento.

![Creare hub di evento][create-event-hub]

Successivamente, passare alla scheda **configurazione** per il nuovo Hub di eventi e creare due **criteri di accesso condiviso**. Assegnare un nome a un primo **INVIO** e assegnargli autorizzazioni **Invia** .

![Criteri di invio][sending-policy]

Assegnare un nome secondo **la ricezione**di, assegnare le autorizzazioni **ascoltare** e fare clic su **Salva**.

![Ricezione di criteri][receiving-policy]

Ogni criterio di accesso condiviso consente alle applicazioni di inviare e ricevere gli eventi da e verso l'Hub di evento. Per accedere alle stringhe di connessione per questi criteri, passare alla scheda **Dashboard** dell'Hub evento e fare clic su **informazioni di connessione**.

![Stringa di connessione][event-hub-dashboard]

Viene utilizzata la stringa di connessione di **INVIO** durante la registrazione eventi e la stringa di connessione **ricezione** viene utilizzata per il download di eventi dall'Hub evento.

![Stringa di connessione][event-hub-connection-string]

## <a name="create-an-api-management-logger"></a>Creare un registratore di gestione delle API

Dopo aver creato un Hub di evento, il passaggio successivo consiste nel configurare un [Logger](https://msdn.microsoft.com/library/azure/mt592020.aspx) nel servizio di gestione delle API in modo che è possibile registrare eventi all'evento Hub.

Logger gestione API sono configurate tramite l' [API REST di gestione dell'API](http://aka.ms/smapi). Prima di utilizzare l'API REST per la prima volta, esaminare i [Prerequisiti](https://msdn.microsoft.com/library/azure/dn776326.aspx#Prerequisites) e assicurarsi di avere [attivato l'accesso all'API REST](https://msdn.microsoft.com/library/azure/dn776326.aspx#EnableRESTAPI).

Per creare un logger, effettuare una richiesta HTTP inserire utilizzando il modello di URL seguente.

    https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview

-   Sostituire `{your service}` con il nome dell'istanza di servizio di gestione delle API.
-   Sostituire `{new logger name}` con il nome desiderato per il nuovo logger. Si farà riferimento questo nome quando si configurano i criteri di [log a eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)

Aggiungere le intestazioni delle operazioni seguenti per la richiesta.

-   Tipo di contenuto: applicazione/json
-   Autorizzazione: SharedAccessSignature uid =...
    -   Per istruzioni sulla generazione di `SharedAccessSignature` vedere [Autenticazione API REST di Azure API di gestione](https://msdn.microsoft.com/library/azure/dn798668.aspx).

Specificare il corpo della richiesta utilizzando il modello seguente.

    {
      "type" : "AzureEventHub",
      "description" : "Sample logger description",
      "credentials" : {
        "name" : "Name of the Event Hub from the Azure Classic Portal",
        "connectionString" : "Endpoint=Event Hub Sender connection string"
        }
    }

-   `type`deve essere impostata su `AzureEventHub`.
-   `description`fornisce una descrizione facoltativa del logger e può essere una stringa di lunghezza zero se lo si desidera.
-   `credentials`contiene il `name` e `connectionString` dell'Hub di evento Azure.

Quando si effettua la richiesta, se il logger viene creato un codice di stato `201 Created` viene restituito. 

>[AZURE.NOTE] Per altre possibili codici restituiti e i motivi, vedere [creare un Logger](https://msdn.microsoft.com/library/azure/mt592020.aspx#PUT). Per vedere come eseguire altre operazioni, come elenco, aggiornamento ed eliminazione, vedere la documentazione di entità [Logger](https://msdn.microsoft.com/library/azure/mt592020.aspx) .

## <a name="configure-log-to-eventhubs-policies"></a>Configurare i criteri di log a eventhubs

Dopo aver configurato il logger in Gestione API, è possibile configurare i criteri di log a eventhubs per registrare gli eventi desiderati. Nella sezione criteri in ingresso o la sezione relativa ai criteri in uscita, è possibile utilizzare il criterio di registro a eventhubs.

Per configurare i criteri, accessohttp al [portale classica Azure](https://manage.windowsazure.com), passare il servizio di gestione dell'API e fare clic su **portale di publisher** e **Gestione** di accedere al portale di publisher.

![Portale di Publisher][publisher-portal]

Fare clic su **criteri** dal menu Gestione API a sinistra, selezionare il prodotto desiderato e API e fare clic su **Aggiungi criteri**. In questo esempio stiamo aggiungendo un criterio all' **API eco** del prodotto **illimitate** .

![Aggiungere criteri][add-policy]

Posizionare il cursore nel `inbound` criteri della sezione e fare clic sul criterio **Log a EventHub** per inserire la `log-to-eventhub` modello informativa criteri.

![Editor Criteri][event-hub-policy]

    <log-to-eventhub logger-id ='logger-id'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
    </log-to-eventhub>

Sostituire `logger-id` con il nome del logger API Gestione configurato nel passaggio precedente.

È possibile utilizzare qualsiasi espressione che restituisce una stringa come valore per il `log-to-eventhub` elemento. In questo esempio viene registrata una stringa contenente la data e ora, nome del servizio, id richiesta, indirizzo ip richiesta e il nome dell'operazione.

Fare clic su **Salva** per salvare la configurazione dei criteri aggiornato. Non appena viene salvato il criterio è attivo e gli eventi connessi all'hub evento designato.

## <a name="next-steps"></a>Passaggi successivi

-   Ulteriori informazioni su Azure evento hub
    -   [Guida introduttiva di Azure evento hub](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
    -   [Ricevere messaggi con EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
    -   [Hub evento programmazione manuale](../event-hubs/event-hubs-programming-guide.md)
-   Altre informazioni sull'integrazione di gestione delle API e hub di evento
    -   [Riferimento a un'entità logger](https://msdn.microsoft.com/library/azure/mt592020.aspx)
    -   [riferimento log a eventhub criteri](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
    -   [Monitorare l'API con gestione API Azure, hub di eventi e Runscope](api-management-log-to-eventhub-sample.md)    

## <a name="watch-a-video-walkthrough"></a>Guardare una video procedura dettagliata

> [AZURE.VIDEO integrate-azure-api-management-with-event-hubs]


[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png






