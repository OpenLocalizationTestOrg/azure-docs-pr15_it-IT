<properties
    pageTitle="Consente di creare avvisi per i servizi di Azure multipiattaforma interfaccia della riga di comando (CLI) | Microsoft Azure"
    description="Utilizzare l'interfaccia della riga di comando per creare gli avvisi di Azure, che possono attivare le notifiche o automazione quando sono soddisfatte le condizioni specificate."
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
    ms.date="10/24/2016"
    ms.author="robb"/>

# <a name="use-the-cross-platform-command-line-interface-cli-to-create-alerts-for-azure-services"></a>Consente di creare avvisi per i servizi di Azure multipiattaforma interfaccia della riga di comando (CLI)

> [AZURE.SELECTOR]
- [Portale](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Panoramica

In questo articolo viene illustrato come configurare avvisi Azure utilizzando l'interfaccia della riga di comando (CLI).

>[AZURE.NOTE] Monitor Azure è il nuovo nome per ciò che era definito "Azure approfondimenti" fino a 25 ° settembre 2016. Tuttavia, gli spazi dei nomi e quindi comandi riportati di seguito contengono ancora "che".

È possibile ricevere un avviso in base al monitoraggio metriche per o gli eventi sul provider di servizi Azure.

- **Valori metriche** - allarmi quando il valore di una metrica specificata supera una soglia che assegnare in entrambe le direzioni. Vale a dire sia attiva quando condizione prima di tutto e quindi in un secondo momento quando che condizione è non sia più soddisfatta.    
- **Eventi del log di attività** : consente di attivare un avviso a *ogni* evento o, solo se sono presenti un determinato numero di eventi.

È possibile configurare un avviso per eseguire le operazioni seguenti quando viene attivato:

- invio di notifiche tramite posta elettronica per l'amministratore di servizio e coamministratori
- inviare posta elettronica in altri messaggi di posta elettronica specificato.
- chiamare un webhook
- avviare l'esecuzione di un runbook Azure (solo dal portale di Azure al momento)

È possibile configurare e ottenere informazioni sulle regole di avviso utilizzo

- [Portale di Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [interfaccia di riga di comando (CLI)](insights-alerts-command-line-interface.md)
- [API REST Monitor Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)


È sempre possibile ricevere assistenza per i comandi digitando un comando e inserendo - Guida alla fine. Per esempio:

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>Creare regole di avviso tramite CLI

1. Eseguire i prerequisiti o effettuare l'accesso Azure. Vedere [esempi di Azure Monitor CLI](insights-cli-samples.md). In breve, installare CLI ed eseguire questi comandi. Vengono aggiornate è eseguito l'accesso, visualizzare il tipo di sottoscrizione si sta usando e prepararsi per eseguire i comandi di Azure Monitor.


    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2.  Per elencare le regole esistenti in un gruppo di risorse, utilizzare seguenti modulo **approfondimenti azure avvisi regola elenco** *[opzioni] &lt;resourceGroup&gt; *

    ```console
    azure insights alerts rule list myresourcegroupname

    ```
3. Per creare una regola, è necessario disporre di informazioni importanti varie prima di tutto.
    - **ID risorsa** per la risorsa che si desidera impostare un avviso per
    - **Metriche definizioni** disponibili per tale risorsa

    Un modo per ottenere l'ID risorsa consiste nell'utilizzare il portale di Azure. Supponendo che la risorsa è già stata creata, selezionarlo nel portale. In e il successivo, scegliere *proprietà* nella sezione *Impostazioni* . *ID risorsa* è un campo e il successivo. Un altro modo consiste nell'utilizzare [Esplora risorse Azure](https://resources.azure.com/).

    È un id risorsa di esempio per un'app web

    ```console
    /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
    ```

    Per ottenere un elenco delle unità metriche disponibili per i dati per l'esempio precedente delle risorse, utilizzare il comando CLI seguente:  

    ```console
    azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
    ```

    _PT1M_ è granularità misura disponibile (1-minuti). Uso granularità diversi offre diverse opzioni metriche.


4. Per creare una regola di avviso unità di misura metriche, utilizzare un comando nel formato seguente:

    **approfondimenti Azure avvisi regola unità di misura metriche impostare** *[opzioni] &lt;NomeRegola&gt; &lt;percorso&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;operatore&gt; &lt;soglia&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt; *

    Nell'esempio seguente consente di impostare un avviso per una risorsa del sito web. Avviso trigger ogni volta che riceve il traffico in modo coerente per 5 minuti e nuovamente quando non riceve il traffico per 5 minuti.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```

5. Per creare webhook o inviare posta elettronica quando viene generato un avviso, creare innanzitutto il messaggio di posta elettronica e/o webhooks. Creare la regola immediatamente in un secondo momento. Non è possibile associare webhook o messaggi di posta elettronica con già creati regole utilizzando CLI.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```


6. Per creare un avviso che viene attivata per una determinata condizione nel log delle attività, usare il modulo:

    **set di regole avvisi approfondimenti log** *[opzioni] &lt;NomeRegola&gt; &lt;percorso&gt; &lt;resourceGroup&gt; &lt;invece&gt; *

    Per esempio

    ```console
    azure insights alerts rule log set myActivityLogRule eastus myresourceGroupName Microsoft.Storage/storageAccounts/listKeys/action
    ```

    L'invece corrisponde a un tipo di evento per una voce nel log delle attività. Ad esempio *Microsoft.Compute/virtualMachines/delete* e *microsoft.insights/diagnosticSettings/write*.

    È possibile utilizzare il comando di PowerShell [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) per ottenere un elenco di operationNames possibili. In alternativa, è possibile usare il portale di Azure per limitare le attività e trovare specifico oltre operazioni che si desidera creare un avviso per. Operazioni mostrate nella visualizzazione grafico log di nomi descrittivi. Ricerca in JSON per la voce ed estrarre il valore invece.   

7. È possibile verificare che gli avvisi siano stati creati in modo corretto controllando in una singola regola.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```

8. Per eliminare le regole, utilizzare un comando della maschera:

    **approfondimenti avvisi regola eliminare** [opzioni] &lt;resourceGroup&gt; &lt;NomeRegola&gt;

    Questi comandi eliminare le regole create in precedenza in questo articolo.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```



## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di monitoraggio Azure](monitoring-overview.md) inclusi i tipi di informazioni è possibile raccogliere e monitorare.
* Ulteriori informazioni sulla [configurazione webhooks negli avvisi](insights-webhooks-alerts.md).
* Ulteriori informazioni su [Azure automazione runbook](..\automation\automation-starting-a-runbook.md).
* È possibile ottenere una [Panoramica di raccogliere i registri diagnostici](monitoring-overview-of-diagnostic-logs.md) per raccogliere metriche ad alta frequenza dettagliate del servizio.
* È possibile ottenere una [Panoramica dell'insieme di metriche](insights-how-to-customize-monitoring.md) per assicurarsi che il servizio è disponibile e rispondere.
