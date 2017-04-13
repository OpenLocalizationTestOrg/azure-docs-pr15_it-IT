<properties
    pageTitle="Usare PowerShell per creare avvisi per i servizi di Azure | Microsoft Azure"
    description="Usare PowerShell per creare gli avvisi di Azure, che possono attivare le notifiche o automazione quando sono soddisfatte le condizioni specificate."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="robb"/>

# <a name="use-powershell-to-create-alerts-for-azure-services"></a>Usare PowerShell per creare avvisi per i servizi di Azure

> [AZURE.SELECTOR]
- [Portale](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Panoramica

In questo articolo viene illustrato come configurare avvisi Azure tramite PowerShell.  

È possibile ricevere un avviso in base al monitoraggio metriche per o gli eventi sul provider di servizi Azure.

- **Valori metriche** - allarmi quando il valore di una metrica specificata supera una soglia che assegnare in entrambe le direzioni. Vale a dire sia attiva quando condizione prima di tutto e quindi in un secondo momento quando che condizione è non sia più soddisfatta.    
- **Eventi del log di attività** : consente di attivare un avviso a *ogni* evento o, solo se sono presenti un determinato numero di eventi.

È possibile configurare un avviso per eseguire le operazioni seguenti quando viene attivato:

- invio di notifiche tramite posta elettronica per l'amministratore di servizio e coamministratori
- inviare posta elettronica in altri messaggi di posta elettronica specificato.
- chiamare un webhook
- avviare l'esecuzione di un runbook Azure (solo dal portale di Azure)

È possibile configurare e ottenere informazioni sulle regole di avviso utilizzo

- [Portale di Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [interfaccia di riga di comando (CLI)](insights-alerts-command-line-interface.md)
- [API REST Monitor Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)


Per ulteriori informazioni, è sempre possibile digitare ```get-help``` e quindi il comando di PowerShell si cercano informazioni.

## <a name="create-alert-rules-in-powershell"></a>Creare regole di avviso in PowerShell

1. Accedere a Azure.   

    ```PowerShell
    Login-AzureRmAccount

    ```

2. Ottenere un elenco delle sottoscrizioni è disponibile. Verificare che sta lavorando con l'abbonamento a destra. In caso contrario, impostarlo su quello giusto utilizzando l'output da `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

3.  Per elencare le regole esistenti in un gruppo di risorse, utilizzare il comando seguente:

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

4. Per creare una regola, è necessario disporre di informazioni importanti varie prima di tutto. 
    - **ID risorsa** per la risorsa che si desidera impostare un avviso per
    - **Metriche definizioni** disponibili per tale risorsa

    Un modo per ottenere l'ID risorsa consiste nell'utilizzare il portale di Azure. Supponendo che la risorsa è già stata creata, selezionarlo nel portale. In e il successivo, scegliere *proprietà* nella sezione *Impostazioni* . ID risorsa è un campo e il successivo. Un altro modo consiste nell'utilizzare [Esplora risorse Azure](https://resources.azure.com/).

    È un id risorsa di esempio per un'app web

    ```
    /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
    ```

    È possibile utilizzare `Get-AzureRmMetricDefinition` per visualizzare l'elenco di tutte le definizioni metriche per una risorsa specifica.

    ```PowerShell
    Get-AzureRmMetricDefinition -ResourceId <resource_id>
    ```

    Nell'esempio seguente genera una tabella con la metrica nome e l'unità per l'unità di misura metriche.

    ```PowerShell
    Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

    ```
    Eseguendo Get-MetricDefinitions è disponibile un elenco completo delle opzioni disponibili per ottenere AzureRmMetricDefinition.


5. Nell'esempio seguente consente di impostare un avviso per una risorsa del sito web. Avviso trigger ogni volta che riceve il traffico in modo coerente per 5 minuti e nuovamente quando non riceve il traffico per 5 minuti.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```

6. Per creare webhook o inviare posta elettronica quando genera un avviso, creare innanzitutto il messaggio di posta elettronica e/o webhooks. Quindi immediatamente creare la regola in un secondo momento con-tag azioni e come illustrato nell'esempio seguente. Non è possibile associare webhook o messaggi di posta elettronica con già create le regole tramite PowerShell.


    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```


7. Per creare un avviso che attiva una condizione specifica nel log delle attività, utilizzare i comandi disponibili nel seguente formato

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmLogAlertRule -Name myLogAlertRule -Location "East US" -ResourceGroup myresourcegroup -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup resourcegroupbeingmonitored -Actions $actionEmail, $actionWebhook
    ```

    Invece - corrisponde a un tipo di evento nel log delle attività. Ad esempio *Microsoft.Compute/virtualMachines/delete* e *microsoft.insights/diagnosticSettings/write*.

    È possibile utilizzare il comando di PowerShell [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) per ottenere un elenco di operationNames possibili. In alternativa, è possibile usare il portale di Azure per limitare le attività e trovare specifico oltre operazioni che si desidera creare un avviso per. Operazioni mostrate nella visualizzazione grafico log di nomi descrittivi. Ricerca in JSON per la voce ed estrarre il valore invece.   

8. Verificare che gli avvisi siano stati creati correttamente esaminando le singole regole.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```

9. Eliminare gli avvisi. Questi comandi eliminare le regole create in precedenza in questo articolo.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di monitoraggio Azure](monitoring-overview.md) inclusi i tipi di informazioni è possibile raccogliere e monitorare.
* Ulteriori informazioni sulla [configurazione webhooks negli avvisi](insights-webhooks-alerts.md).
* Ulteriori informazioni su [Azure automazione runbook](..\automation\automation-starting-a-runbook.md).
* È possibile ottenere una [Panoramica di raccogliere i registri diagnostici](monitoring-overview-of-diagnostic-logs.md) per raccogliere metriche ad alta frequenza dettagliate del servizio.
* È possibile ottenere una [Panoramica dell'insieme di metriche](insights-how-to-customize-monitoring.md) per assicurarsi che il servizio è disponibile e rispondere.
