<properties
    pageTitle="Associazione di funzioni Twilio Azure | Microsoft Azure"
    description="Informazioni su come utilizzare le associazioni Twilio con le funzioni di Azure."
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
    ms.date="10/20/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-twilio-output-binding"></a>Associazione di output Twilio funzioni Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

In questo articolo viene spiegato come configurare e utilizzare le associazioni Twilio con le funzioni di Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Azure funzioni supporta Twilio output associazioni per abilitare le funzioni inviare messaggi di testo SMS con alcune righe di codice e un account [Twilio](https://www.twilio.com/) . 
 

## <a name="functionjson-for-azure-notification-hub-output-binding"></a>associazione di output Function.JSON per Hub di notifica di Azure

Il file function.json fornisce le proprietà seguenti:

- `name`: Nome variabile utilizzato nel codice della funzione per il messaggio di testo Twilio SMS.
- `type`: deve essere impostata su *"twilioSms"*.
- `accountSid`: Questo valore deve essere impostato per il nome di un'impostazione di App che contiene il Sid Account Twilio.
- `authToken`: Questo valore deve essere impostato per il nome di un'impostazione di App che contiene il token di autenticazione Twilio.
- `to`: Impostare il numero di telefono che il testo SMS verrà inviato a questo valore.
- `from`: Impostare il numero di telefono che il testo SMS inviato da questo valore.
- `direction`: deve essere impostata su *"Indietro"*.
- `body`: Questo valore può essere utilizzato per codificare il messaggio SMS se non è necessario impostare in modo dinamico il codice per la funzione. 

 
Esempio function.json:

    {
      "type": "twilioSms",
      "name": "message",
      "accountSid": "TwilioAccountSid",
      "authToken": "TwilioAuthToken",
      "to": "+1704XXXXXXX",
      "from": "+1425XXXXXXX",
      "direction": "out",
      "body": "Azure Functions Testing"
    }


## <a name="example-c-queue-trigger-with-twilio-output-binding"></a>Associazione di output trigger esempio c# coda con Twilio

#### <a name="synchronous"></a>Icona del

In questo esempio icona del relativo a un trigger di coda di archiviazione Azure utilizza un parametro esterno per inviare un messaggio di testo a un cliente che ha effettuato un ordine.

    #r "Newtonsoft.Json"
    #r "Twilio.Api"

    using System;
    using Newtonsoft.Json;
    using Twilio;

    public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        dynamic order = JsonConvert.DeserializeObject(myQueueItem);
        string msg = "Hello " + order.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the SMSMessage variable.
        message = new SMSMessage();

        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        message.Body = msg;
        message.To = order.mobileNumber;
    }

#### <a name="asynchronous"></a>Asincrono

In questo esempio asincrono relativo a un trigger di coda di archiviazione Azure invia un messaggio di testo a un cliente che ha effettuato un ordine.

    #r "Newtonsoft.Json"
    #r "Twilio.Api"
     
    using System;
    using Newtonsoft.Json;
    using Twilio;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        dynamic order = JsonConvert.DeserializeObject(myQueueItem);
        string msg = "Hello " + order.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the SMSMessage variable.
        SMSMessage smsText = new SMSMessage();

        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        smsText.Body = msg;
        smsText.To = order.mobileNumber;
        
        await message.AddAsync(smsText);
    }


## <a name="example-nodejs-queue-trigger-with-twilio-output-binding"></a>Associazione di output trigger coda Node di esempio con Twilio

In questo esempio node invia un messaggio di testo a un cliente che ha effettuato un ordine.

    module.exports = function (context, myQueueItem) {
        context.log('Node.js queue trigger function processed work item', myQueueItem);
    
        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        var msg = "Hello " + myQueueItem.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the message binding.
        context.bindings.message = {};
    
        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        context.bindings.message = {
            body : msg,
            to : myQueueItem.mobileNumber
        };
    
        context.done();
    };

## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
