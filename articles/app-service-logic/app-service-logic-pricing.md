<properties 
    pageTitle="App logica prezzi modello | Microsoft Azure" 
    description="Informazioni dettagliate sul funzionamento dei prezzi nelle App logica" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="klam"/>

# <a name="logic-apps-pricing-model"></a>App logica prezzi modello

App logica Azure consente di ridimensionare ed eseguire i flussi di lavoro di integrazione nel cloud.  Di seguito sono informazioni dettagliate sulla fatturazione e prezzi piani per le applicazioni di logica.

## <a name="consumption-pricing"></a>Consumo prezzi

Appena creato App logica di utilizzare un piano di consumo. Modello a prezzi consumo App logica di pagare solo ciò che si utilizza.  Logica App non è limitato quando si usa un piano di consumo.
Tutte le azioni eseguite in una sequenza di un'istanza di app logica sono a consumo.

### <a name="what-are-action-executions"></a>Quali sono le esecuzioni azione?

Ogni passaggio in una definizione di app logica è un'azione.  Sono inclusi i trigger, passaggi del flusso di controllo come condizioni, ambiti per ogni cicli fino a cicli, le chiamate ai connettori e le chiamate a azioni native.
I trigger sono azioni appena speciale che sono progettate per creare una nuova istanza di un'app di logica quando si verifica un evento specifico.  Sono disponibili numerose opzioni diverse per i trigger che potrebbero influire su come l'app logica è a consumo.

-   **Polling trigger** – questo trigger continuamente polling un endpoint finché non viene ricevuto un messaggio che soddisfa i criteri per la creazione di una nuova istanza di un'app di logica.  L'intervallo di polling può essere configurato nel trigger nella finestra di progettazione logica app.  Ogni richiesta di polling, anche se non viene creata una nuova istanza di un'app di logica, verrà considerato come un'esecuzione delle azioni.

-   **Trigger Webhook** -questo trigger di attendere un client per inviare una richiesta su un particolare endpoint.  Ogni richiesta inviata all'endpoint webhook conteggiata come un'esecuzione di azioni. La richiesta e trigger HTTP Webhook sono entrambe trigger webhook.

-   **Trigger di ricorrenza** : questo trigger verrà creata una nuova istanza dell'app logica in base all'intervallo di ricorrenza configurato nel trigger.  Un trigger di ricorrenza, ad esempio, può essere configurato per eseguire ogni 3 giorni o persino minuto.

Le esecuzioni trigger possono essere visualizzate in e il risorsa logica App nella parte Trigger cronologia.

Tutte le azioni eseguite, se si trattasse di riusciti o non sono a consumo come un'esecuzione di azioni.  Azioni che sono state ignorate a causa di una condizione non o che non è stata eseguita perché l'app logica terminata prima del completamento non vengono conteggiati come le esecuzioni azione.

Operazioni eseguite all'interno di cicli vengono conteggiati per ogni iterazione del ciclo.  Ad esempio una singola azione in una per ogni iterazione Esegui ciclo continuo in un elenco di 10 elementi verranno contate come il numero di voci dell'elenco (10) moltiplicato per il numero di azioni nel ciclo (1) e uno per l'avvio del ciclo che, in questo esempio sarebbero (10 * 1) + 1 = 11 esecuzioni azione.

Logica di applicazioni disattivati non è possibile specificare le nuove istanze create e pertanto durante il periodo di tempo che sono disabilitate verrà non ottenere addebitata.  Occorre tenere presente che dopo aver disattivato un'app logica potrebbe richiedere un po' di tempo per le istanze di disattivazione prima completamente disattivato.

## <a name="app-service-plans"></a>Servizio di App plan di messaggistica unificata

Piani di servizio App non sono più necessari per creare un'App di logica.  È anche possibile fare riferimento un piano di servizio App con un'app logica esistente.  App logica creato in precedenza a un piano di servizio App continueranno a funzionare come al solito in cui, a seconda del piano scelta, verrà visualizzato limitato dopo un numero di esecuzioni giorno è superato e non verrà effettuato tramite l'indicatore di esecuzione di azioni.

Piani di servizio App e le esecuzioni azione consentita giornaliera:

| |Liberare/condivisi/Basic|Standard|Premium|
|---|---|---|---|
|Esecuzioni di azione al giorno| 200|10.000|50.000|

### <a name="convert-from-consumption-to-app-service-plan-pricing"></a>Convertire l'abbonamento da consumo al piano di servizio App prezzi

Per fare riferimento a un piano di servizio App per un consumo logica App, è possibile renderla [eseguire il seguente script di PowerShell](https://github.com/logicappsio/ConsumptionToAppServicePlan).  Assicurarsi che è necessario prima gli [Strumenti di Azure PowerShell](https://github.com/Azure/azure-powershell) installato.

``` powershell
Param(
    [string] $AppService_RG = '<app-service-resource-group>',
    [string] $AppService_Name = '<app-service-name>',
    [string] $LogicApp_RG = '<logic-app-resource-group>',
    [string] $LogicApp_Name = '<logic-app-name>',
    [string] $subscriptionId = '<azure-subscription-id>'
)

Login-AzureRmAccount 
$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId
$appserviceplan = Get-AzureRmResource -ResourceType "Microsoft.Web/serverFarms" -ResourceGroupName $AppService_RG -ResourceName $AppService_Name
$logicapp = Get-AzureRmResource -ResourceType "Microsoft.Logic/workflows" -ResourceGroupName $LogicApp_RG -ResourceName $LogicApp_Name

$sku = @{
    "name" = $appservicePlan.Sku.tier;
    "plan" = @{
      "id" = $appserviceplan.ResourceId;
      "type" = "Microsoft.Web/ServerFarms";
      "name" = $appserviceplan.Name  
    }
}

$updatedProperties = $logicapp.Properties | Add-Member @{sku = $sku;} -PassThru

$updatedLA = Set-AzureRmResource -ResourceId $logicapp.ResourceId -Properties $updatedProperties -ApiVersion 2015-08-01-preview
```

### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>Convertire l'abbonamento da App servizio pianificare prezzi a consumo

Per modificare un'App di logica che ha un piano App servizio associato a un modello di consumo rimuovere il riferimento al piano di servizio di App nella definizione di logica App.  Può essere eseguita con una chiamata a un cmdlet di PowerShell:

`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`

## <a name="pricing"></a>Prezzi

Per ulteriori informazioni sui prezzi vedere [Logica App prezzi](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Passaggi successivi

- [Una panoramica delle App logica][whatis]
- [Creare la prima app logica][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: app-service-logic-what-are-logic-apps.md
[create]: app-service-logic-create-a-logic-app.md

