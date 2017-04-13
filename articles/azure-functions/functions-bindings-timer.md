<properties
    pageTitle="Trigger di timer funzioni Azure | Microsoft Azure"
    description="Informazioni su come utilizzare trigger timer nelle funzioni di Azure."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
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
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-timer-trigger"></a>Azure trigger timer di funzioni

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

In questo articolo viene spiegato come configurare i trigger timer nelle funzioni di Azure. Timer attiva funzioni di chiamata in base a una programmazione, una volta o ricorrente.  

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a name="functionjson-for-timer-trigger"></a>Function.JSON per trigger timer

Il file *function.json* fornisce un'espressione di programmazione. Ad esempio la pianificazione seguente viene eseguita la funzione minuto:

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Trigger timer gestisce più istanze scalabilità automaticamente: una sola istanza di una funzione timer particolare verrà eseguito in tutte le istanze.

## <a name="format-of-schedule-expression"></a>Formato dell'espressione di programmazione

L'espressione di pianificazione è un' [espressione CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) che include 6 campi: `{second} {minute} {hour} {day} {month} {day of the week}`. 

Nota molte delle espressioni cron che disponibili online omettono il campo {secondo}, in modo che se si copia da uno di questi sarà necessario modificare per il campo aggiuntivo. 

Ecco alcuni altri programmazione esempi di espressioni:

Per avviare una volta ogni 5 minuti:

```json
"schedule": "0 */5 * * * *"
```

Per avviare una volta nella parte superiore di ogni ora:

```json
"schedule": "0 0 * * * *",
```

Per impostare un trigger ogni due ore:

```json
"schedule": "0 0 */2 * * *",
```

Per avviare una volta ogni ora da 9: 00 PM 5:

```json
"schedule": "0 0 9-17 * * *",
```

Attivare 9:30 AM ogni giorno:

```json
"schedule": "0 30 9 * * *",
```

Attivare 9:30 AM ogni giorno della settimana:

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="timer-trigger-c-code-example"></a>Esempio di codice trigger c# timer

In questo esempio di codice c# scrive un singolo file di log ogni volta che la funzione viene attivata.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
