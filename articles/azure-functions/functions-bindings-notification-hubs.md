<properties
    pageTitle="Associazione di funzioni notifica Hub Azure | Microsoft Azure"
    description="Imparare a utilizzare l'associazione di Azure notifica Hub nelle funzioni di Azure."
    services="functions"
    documentationCenter="na"
    authors="wesmc7777"
    manager="erikre"
    editor=""
    tags=""
    keywords="funzioni di Azure, funzioni, elaborazione di eventi, calcolo dinamico, architettura senza server"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="10/27/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-notification-hub-output-binding"></a>Associazione di output Azure Hub di notifica di funzioni

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

In questo articolo viene descritto come configurare e associazioni di Azure notifica Hub codice nelle funzioni di Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Le funzioni è possono inviare le notifiche push utilizza un Hub di notifica di Azure configurati con alcune righe di codice. Tuttavia, l'Hub di notifica di Azure deve essere configurato per la piattaforma notifiche servizi (PNS) che si desidera utilizzare. Per ulteriori informazioni su come configurare un Hub di notifica di Azure e lo sviluppo di applicazioni client di registrano per ricevere notifiche, vedere [Guida introduttiva a hub di notifica](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) e scegliere la piattaforma del client di destinazione nella parte superiore.

Le notifiche inviate possono essere notifiche native o le notifiche di modello. Notifiche native piattaforme notifica specifico come configurato nel `platform` proprietà di associazione output. Una notifica di modello può essere utilizzata per più piattaforme.   

## <a name="notification-hub-output-binding-properties"></a>Proprietà di associazione di notifica hub output

Il file function.json fornisce le proprietà seguenti:

- `name`: Nome variabile utilizzato nel codice della funzione per il messaggio di notifica hub.
- `type`: deve essere impostata su *"notificationHub"*.
- `tagExpression`: Le espressioni tag consentono di specificare che le notifiche di essere recapitati in un set di dispositivi che siano registrati per ricevere notifiche che corrispondono all'espressione tag.  Per ulteriori informazioni, vedere [Routing e tag di espressioni](../notification-hubs/notification-hubs-tags-segment-push-message.md).
- `hubName`: Nome della risorsa hub notifica nel portale di Azure.
- `connection`: La stringa di connessione deve essere una stringa di connessione di **Impostazione dell'applicazione** imposta il valore di *DefaultFullSharedAccessSignature* per l'hub di notifica.
- `direction`: deve essere impostata su *"Indietro"*. 
- `platform`: La proprietà piattaforma indica la piattaforma notifica gli obiettivi di notifica. Deve essere uno dei seguenti valori:
    - `template`: È la piattaforma predefinita se la proprietà piattaforma viene specificata l'associazione di output. Notifiche modello possono essere utilizzate per qualsiasi piattaforma configurato nel Hub notifica Azure. Per ulteriori informazioni sull'utilizzo dei modelli in generale per inviare le notifiche per piattaforme diverse con un Hub di notifica di Azure, vedere [modelli](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).
    - `apns`: Servizio di notifica Push di Apple. Per ulteriori informazioni sulla configurazione di hub di notifica per APN e ricevere la notifica in un'applicazione client, vedere [invio di notifiche push per iOS con gli hub di notifica di Azure](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md) 
    - `adm`: [Dispositivo Amazon messaggistica](https://developer.amazon.com/device-messaging). Per ulteriori informazioni sulla configurazione di hub di notifica per ADM e ricevere la notifica in un'app di dispositivo Kindle, vedere [Guida introduttiva di hub di notifica per le applicazioni di dispositivo Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md) 
    - `gcm`: [Messaggistica Google Cloud](https://developers.google.com/cloud-messaging/). Firebase Cloud messaggistica è la nuova versione GCM, è anche possibile. Per ulteriori informazioni sulla configurazione di hub di notifica per GCM/FCM e ricevere la notifica in un'applicazione client Android, vedere [invio di notifiche push per Android con gli hub di notifica di Azure](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)
    - `wns`: [Servizi di notifica Push di Windows](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) , Windows piattaforme di destinazione. Windows Phone 8.1 e versioni successive è supportata anche da WNS. Per ulteriori informazioni sulla configurazione di hub di notifica per WNS e ricevere la notifica in un'app universale Windows piattaforma (UWP), vedere [Introduzione al notifica hub per Windows universale piattaforma App](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
    - `mpns`: [Servizio di notifica Push di Microsoft](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx). Questa piattaforma supporta precedenti piattaforme di Windows Phone e Windows Phone 8. Per ulteriori informazioni sulla configurazione di hub di notifica per MPNS e ricevere la notifica in un'app di Windows Phone, vedere [invio di notifiche push con gli hub di notifica di Azure nel Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)
 
Esempio function.json:

    {
      "bindings": [
        {
          "name": "notification",
          "type": "notificationHub",
          "tagExpression": "",
          "hubName": "my-notification-hub",
          "connection": "MyHubConnectionString",
          "platform": "gcm",
          "direction": "out"
        }
      ],
      "disabled": false
    }

## <a name="notification-hub-connection-string-setup"></a>Setup stringa di connessione hub di notifica

Per utilizzare un output hub di notifica di associazione, è necessario configurare la stringa di connessione per l'hub. Ciò è possibile nella scheda *integrazione* selezionando l'hub di notifica o crearne uno nuovo. 

È possibile aggiungere manualmente una stringa di connessione per un hub esistente mediante l'aggiunta di una stringa di connessione per *DefaultFullSharedAccessSignature* per l'hub di notifica. Questa stringa di connessione fornisce l'autorizzazione di accesso funzione per inviare messaggi di notifica. Il valore di stringa di connessione *DefaultFullSharedAccessSignature* accessibili dal pulsante **tasti** in e il principale della risorsa hub notifica nel portale di Azure. Per aggiungere manualmente una stringa di connessione per l'hub, utilizzare la procedura seguente: 

1. Scegliere e il **funzione app** del portale di Azure **le impostazioni dell'App funzione > passare a impostazioni servizio App**.

2. In e **l'Impostazioni** , fare clic su **Impostazioni applicazione**.

3. Scorrere fino alla sezione **stringhe di connessione** e aggiungere una voce denominata *DefaultFullSharedAccessSignature* valore per l'hub di notifica. Modificare il tipo su **personalizzata**.
4. Fare riferimento il nome della stringa di connessione nelle associazioni di output. Simile a **MyHubConnectionString** utilizzato nell'esempio precedente.

## <a name="native-notification-examples"></a>Esempi di notifica nativo

#### <a name="apns-native-notifications-with-c-queue-triggers"></a>Notifiche native APN con c# coda trigger

In questo esempio viene illustrato come utilizzare tipi definiti nella [Libreria di Microsoft Azure notifica hub](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) per inviare una notifica APN nativa. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The JSON format for a native APNS notification is ...
        // { "aps": { "alert": "notification message" }}  

        log.Info($"Sending APNS notification of a new user");   
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                            user.name + ")\" }}";
        log.Info($"{apnsNotificationPayload}");
        await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
    }

#### <a name="gcm-native-notifications-with-c-queue-triggers"></a>Notifiche native GCM con c# coda trigger

In questo esempio viene illustrato come utilizzare tipi definiti nella [Libreria di Microsoft Azure notifica hub](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) per inviare una notifica GCM nativa. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The JSON format for a native GCM notification is ...
        // { "data": { "message": "notification message" }}  

        log.Info($"Sending GCM notification of a new user");    
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                            user.name + ")\" }}";
        log.Info($"{gcmNotificationPayload}");
        await notification.AddAsync(new GcmNotification(gcmNotificationPayload));       
    }

#### <a name="wns-native-notifications-with-c-queue-triggers"></a>Notifiche native WNS con c# coda trigger

In questo esempio viene illustrato come utilizzare tipi definiti nella [Libreria di Microsoft Azure notifica hub](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) per inviare una notifica di tipo avviso popup WNS nativa. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The XML format for a native WNS toast notification is ...
        // <?xml version="1.0" encoding="utf-8"?>
        // <toast>
        //   <visual>
        //     <binding template="ToastText01">
        //       <text id="1">notification message</text>
        //     </binding>
        //   </visual>
        // </toast>

        log.Info($"Sending WNS toast notification of a new user");  
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                        "<toast><visual><binding template=\"ToastText01\">" +
                                            "<text id=\"1\">" + 
                                                "A new user wants to be added (" + user.name + ")" + 
                                            "</text>" +
                                        "</binding></visual></toast>";

        log.Info($"{wnsNotificationPayload}");
        await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));       
    }


## <a name="template-notification-examples"></a>Esempi di modelli di notifica

#### <a name="template-example-for-nodejs-timer-triggers"></a>Esempio di modello per i trigger timer Node 

In questo esempio invia una notifica per una [registrazione modello](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) che contiene `location` e `message`.

    module.exports = function (context, myTimer) {
        var timeStamp = new Date().toISOString();
       
        if(myTimer.isPastDue)
        {
            context.log('Node.js is running late!');
        }
        context.log('Node.js timer trigger function ran!', timeStamp);  
        context.bindings.notification = {
            location: "Redmond",
            message: "Hello from Node!"
        };
        context.done();
    };

#### <a name="template-example-for-f-timer-triggers"></a>Esempio di modello per i trigger timer F #

In questo esempio invia una notifica per una [registrazione modello](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) che contiene `location` e `message`.

    let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
        notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]


#### <a name="template-example-using-an-out-parameter"></a>Esempio di modello con un parametro out 

In questo esempio invia una notifica per una [registrazione modello](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) che contiene un `message` segnaposto nel modello.

    using System;
    using System.Threading.Tasks;
    using System.Collections.Generic;
     
    public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
    }
     
    private static IDictionary<string, string> GetTemplateProperties(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["message"] = message;
        return templateProperties;
    }

#### <a name="template-example-with-asynchronous-function"></a>Esempio di modello con la funzione asincrona

Se si utilizza codice asincrono, i parametri non consentiti. In questo caso utilizzare `IAsyncCollector` per restituire la notifica di modello. Il codice seguente è illustrato un esempio asincrono del codice precedente. 

    using System;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        log.Info($"Sending Template Notification to Notification Hub");
        await notification.AddAsync(GetTemplateProperties(myQueueItem));    
    }
    
    private static IDictionary<string, string> GetTemplateProperties(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["user"] = "A new user wants to be added : " + message;
        return templateProperties;
    }

#### <a name="template-example-using-json"></a>Esempio di modello utilizzando JSON 

In questo esempio invia una notifica per una [registrazione modello](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) che contiene un `message` segnaposto nel modello utilizzando una stringa JSON valida.

    using System;
     
    public static void Run(string myQueueItem,  out string notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
    }


#### <a name="template-example-using-notification-hubs-library-types"></a>Esempio di modello utilizzo dei tipi di raccolta hub di notifica

In questo esempio viene illustrato come utilizzare tipi definiti nella [Libreria di Microsoft Azure notifica hub](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 


    #r "Microsoft.Azure.NotificationHubs"

    using System;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
     
    public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
    {
       log.Info($"C# Queue trigger function processed: {myQueueItem}");
       notification = GetTemplateNotification(myQueueItem);
    }

    private static TemplateNotification GetTemplateNotification(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["message"] = message;
        return new TemplateNotification(templateProperties);
    }

## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
