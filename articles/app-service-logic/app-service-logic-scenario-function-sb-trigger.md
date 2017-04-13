<properties
   pageTitle="Scenario di app logica: creare un trigger di Azure funzioni servizio Bus | Microsoft Azure"
   description="Utilizzare le funzioni di Azure per creare un trigger di Bus di servizio per un'app di logica"
   services="logic-apps,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/23/2016"
   ms.author="jehollan"/>

# <a name="logic-app-scenario-create-an-azure-service-bus-trigger-by-using-azure-functions"></a>Scenario di app logica: creare un trigger di Azure servizio Bus utilizzando le funzioni di Azure

È possibile utilizzare funzioni di Azure per creare un trigger per un'app logica quando è necessario distribuire una lunga comunicare ascoltatore o un'attività. Ad esempio, è possibile creare una funzione che verrà ascolto una coda e quindi attivare immediatamente un'app di logica come trigger push.

## <a name="build-the-logic-app"></a>Creare app logica

In questo esempio, si dispone di una funzione in esecuzione per ogni applicazione di logica che deve essere attivata. Prima di tutto, creare un'app di logica che include un trigger di richiesta HTTP. La funzione chiama tale endpoint ogni volta che si riceve un messaggio di coda.  

1. Creare una nuova app logica; Selezionare il trigger **ricezione manuale: quando una richiesta HTTP** .  
   Facoltativamente, è possibile specificare uno schema JSON da utilizzare con il messaggio coda utilizzando uno strumento come [jsonschema.net](http://jsonschema.net). Incollare lo schema nel trigger. In questo modo la finestra di progettazione comprendere la forma dei dati e più facilmente flusso proprietà tramite il flusso di lavoro.
1. Aggiungere le operazioni aggiuntive che si desidera che venga eseguita dopo aver ricevuto un messaggio di coda. Ad esempio, inviare un messaggio di posta elettronica tramite Office 365.  
1. Salvare l'app logica per generare l'URL di callback per il trigger per questa app logica. Nella scheda trigger verrà visualizzata l'URL.

![Il callback URL visualizzato nella scheda trigger][1]

## <a name="build-the-function"></a>Compilare la funzione.

È necessario creare una funzione che funga trigger e ascoltare coda.

1. Nel [portale di funzioni di Azure](https://functions.azure.com/signin), selezionare **Nuova funzione**e quindi selezionare il modello **ServiceBusQueueTrigger - c#** .

    ![Portale funzioni Azure][2]

2. Configurare la connessione alla coda Bus di servizio (che verrà utilizzato il servizio di Azure Bus SDK `OnMessageReceive()` comunicare ascoltatore).
3. Scrivere una funzione semplice per chiamare il punto finale app logica (creato in precedenza) utilizzando il messaggio coda come trigger. Ecco un esempio completo di una funzione. Nell'esempio viene utilizzato un `application/json` tipo di contenuto del messaggio, ma è possibile modificarlo se necessario.

   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";

   public static void Run(string myQueueItem, TraceWriter log)
   {

       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

Per verificare, aggiungere un messaggio di coda tramite uno strumento come [Servizio Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer). Vedere app logica fire subito dopo la funzione riceve il messaggio.

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG
