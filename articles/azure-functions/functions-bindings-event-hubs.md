<properties
    pageTitle="Associazioni di funzioni evento Hub Azure | Microsoft Azure"
    description="Informazioni su come utilizzare le associazioni di Azure evento Hub nelle funzioni di Azure."
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
    ms.date="10/17/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-event-hub-bindings"></a>Associazioni di funzioni evento Hub Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

In questo articolo viene descritto come configurare e associazioni di [Azure evento Hub](../event-hubs/event-hubs-overview.md) di codice per le funzioni di Azure. Funzioni di Azure supportano le associazioni trigger e output per Azure evento hub.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Se ha familiarità con gli hub di eventi di Azure, vedere [Panoramica di Azure evento Hub](../event-hubs/event-hubs-overview.md).

## <a name="azure-event-hub-trigger-binding"></a>Hub evento Azure attivare l'associazione

Per rispondere a un evento inviato a un flusso di eventi hub di evento, è possibile utilizzare un trigger di Azure evento Hub. È necessario disporre dell'accesso in lettura hub evento per impostare un trigger di associazione.

#### <a name="functionjson-for-event-hub-trigger-binding"></a>impostare un trigger Function.JSON per evento Hub associazione

Il file *function.json* per un trigger di Azure evento Hub consente di specificare le proprietà seguenti:

- `type`: Deve essere impostata su *eventHubTrigger*.
- `name`: Il nome della variabile utilizzato nel codice della funzione per il messaggio di hub evento. 
- `direction`: Deve essere impostata su *in*. 
- `path`: Il nome dell'hub evento.
- `consumerGroup`: Si tratta di una proprietà facoltativa utilizzata per impostare il [gruppo consumer](../event-hubs-overview.md#consumer-groups) utilizzato per sottoscrivere eventi nell'hub. Se omesso, il `$Default` consumer gruppo viene utilizzato. 
- `connection`: Il nome di un'impostazione di app che contiene la stringa di connessione per lo spazio dei nomi che si trova l'hub di evento in. Copiare la stringa di connessione facendo clic sul pulsante **Informazioni di connessione** per lo spazio dei nomi, hub evento.  Questa stringa di connessione deve avere almeno le autorizzazioni di lettura per l'attivazione di trigger.

        {
          "bindings": [
            {
              "type": "eventHubTrigger",
              "name": "myEventHubMessage",
              "direction": "in",
              "path": "MyEventHub",
              "connection": "myEventHubReadConnectionString"
            }
          ],
          "disabled": false
        }

#### <a name="azure-event-hub-trigger-c-example"></a>Esempio di trigger c# Azure Hub di evento
 
Usa function.json esempio precedente, il corpo del messaggio dell'evento verrà registrato con il funzione codice c# riportata di seguito:
 
    using System;
    
    public static void Run(string myEventHubMessage, TraceWriter log)
    {
        log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
    }

#### <a name="azure-event-hub-trigger-f-example"></a>Esempio di trigger F # Azure Hub di evento

Usa function.json esempio precedente, il corpo del messaggio dell'evento verrà registrato con il codice F # funzione riportata di seguito:

    let Run(myEventHubMessage: string, log: TraceWriter) =
        log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)

#### <a name="azure-event-hub-trigger-nodejs-example"></a>Trigger di evento Hub Azure Node esempio
 
Usa function.json esempio precedente, il corpo del messaggio dell'evento verrà registrato con il codice della funzione node riportata di seguito:
 
    module.exports = function (context, myEventHubMessage) {
        context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
        context.done();
    };


## <a name="azure-event-hub-output-binding"></a>Associazione di output Azure Hub di evento

Un Hub di evento Azure associazione di output viene utilizzato per scrivere eventi in un flusso di eventi hub evento. È necessario disporre dell'autorizzazione Invia a un hub di evento per scrivere eventi. 

#### <a name="functionjson-for-event-hub-output-binding"></a>associazione di output Function.JSON per Hub di evento

Il file *function.json* per un Hub di evento Azure output associazione specifica le proprietà seguenti:

- `type`: Deve essere impostata su *eventHub*.
- `name`: Il nome della variabile utilizzato nel codice della funzione per il messaggio di hub evento. 
- `path`: Il nome dell'hub evento.
- `connection`: Il nome di un'impostazione di app che contiene la stringa di connessione per lo spazio dei nomi che si trova l'hub di evento in. Copiare la stringa di connessione facendo clic sul pulsante **Informazioni di connessione** per lo spazio dei nomi, hub evento.  Questa stringa di connessione deve disporre delle autorizzazioni Invia per inviare il messaggio per il flusso di Hub di evento.
- `direction`: Deve essere impostata su *Indietro*. 

        {
          "type": "eventHub",
          "name": "outputEventHubMessage",
          "path": "myeventhub",
          "connection": "MyEventHubSend",
          "direction": "out"
        }


#### <a name="azure-event-hub-c-code-example-for-output-binding"></a>Azure evento Hub codice c# riportato per l'associazione di output
 
C# funzione codice di esempio seguente viene illustrata la scrittura di un evento in un flusso di eventi Hub evento. Rappresenta questo esempio l'output di evento Hub associazione illustrato in precedenza applicato a un trigger di timer c#.  
 
    using System;
    
    public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
    {
        String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    
        log.Verbose(msg);   
        
        outputEventHubMessage = msg;
    }

#### <a name="azure-event-hub-f-code-example-for-output-binding"></a>Azure evento Hub F # esempio per l'associazione di output

F # funzione codice di esempio seguente viene illustrata la scrittura di un evento in un flusso di eventi Hub evento. Rappresenta questo esempio l'output di evento Hub associazione illustrato in precedenza applicato a un trigger di timer c#.

    let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
        let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
        log.Verbose(msg);
        outputEventHubMessage <- msg;

#### <a name="azure-event-hub-nodejs-code-example-for-output-binding"></a>Esempio di codice evento Hub Node Azure per l'associazione di output
 
Il codice di funzione di esempio Node seguente illustra la scrittura di un evento in un flusso di eventi Hub evento. Rappresenta questo esempio l'output di evento Hub associazione illustrato in precedenza applicato a un trigger di timer Node.  
 
    module.exports = function (context, myTimer) {
        var timeStamp = new Date().toISOString();
        
        if(myTimer.isPastDue)
        {
            context.log('TimerTriggerNodeJS1 is running late!');
        }

        context.log('TimerTriggerNodeJS1 function ran!', timeStamp);   
        
        context.bindings.outputEventHubMessage = "TimerTriggerNodeJS1 ran at : " + timeStamp;
    
        context.done();
    };

## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
