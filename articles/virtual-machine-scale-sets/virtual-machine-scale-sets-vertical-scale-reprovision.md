<properties
    pageTitle="Ridimensionare verticalmente Azure macchina virtuale scala set | Microsoft Azure"
    description="Come ridurre verticalmente una macchina virtuale in risposta a monitoraggio degli avvisi con l'automazione di Azure"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="madhana"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="guybo"/>

# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Imposta autoscale verticale con scala macchina virtuale

In questo articolo viene descritto come ridimensionare verticalmente Azure [Set scala macchina virtuale](https://azure.microsoft.com/services/virtual-machine-scale-sets/) con o senza un nuovo provisioning del. Per la scala verticale di macchine virtuali che non sono in scala set, consultare [verticalmente scalare Azure macchina virtuale con Azure automazione](../virtual-machines/virtual-machines-windows-vertical-scaling-automation.md).

Scala verticale, noto anche come _scalare_ e _scalare verso il basso_, di conseguenza, aumentare o diminuire le dimensioni di macchine () in risposta a un carico di lavoro. Confrontare questo passaggio con [scalabilità orizzontale](./virtual-machine-scale-sets-autoscale-overview.md), anche detta _scala in_, a seconda del carico di lavoro in cui è stato modificato il numero di macchine virtuali e _scalabilità_ .

Un nuovo provisioning dell'indica che la rimozione di una macchina virtuale esistente e sostituirlo con uno nuovo. Quando si aumentare o diminuire le dimensioni delle macchine virtuali in un Set di scala macchine Virtuali, in alcuni casi si desidera ridimensionare macchine virtuali esistenti e mantenere i dati, mentre in altri casi è necessario distribuire nuove macchine virtuali di nuove dimensioni. Entrambi i casi illustrati in questo documento.

Scalabilità verticale può essere utile quando:

- Un servizio basato su macchine virtuali è utilizzata in (ad esempio i fine settimana). Ridurre le dimensioni di macchine Virtuali, è possibile ridurre i costi mensili.
- Aumento delle dimensioni di macchine Virtuali di supportare più grande richiesta senza creare altre macchine virtuali.

È possibile impostare il ridimensionamento verticale a essere attivati avvisi basati su metriche sulla base del gruppo scala di macchine Virtuali. Quando si attiva l'avviso viene generato un webhook che attiva runbook che è possibile ridimensionare una scala impostare verso l'alto o verso il basso. Ridimensionamento verticale può essere configurato procedendo come segue:

1. Creare un account Azure automazione con la funzionalità di runas.
2. Importare scala verticale automazione Azure runbook per macchine Virtuali scala set in abbonamento.
3. Aggiungere un webhook il runbook.
4. Aggiungere un avviso per la scala macchine Virtuali impostare utilizzando una notifica webhook.

> [AZURE.NOTE] Il ridimensionamento automatico verticale può essere eseguita solo determinati limiti di dimensioni macchine Virtuali. Confrontare le specifiche di ogni dimensione prima di decidere di scalare da una a altra (numero più alto non sempre indica dimensioni maggiori di macchine Virtuali). È possibile scegliere le dimensioni tra le seguenti coppie di dimensioni:

>| Macchine Virtuali dimensioni coppia di scala |   |
|---|---|
|  Standard_A0 | Standard_A11 |
|  Standard_D1 |  Standard_D14 |
|  Standard_DS1 |  Standard_DS14 |
|  Standard_D1v2 |  Standard_D15v2 |
|  Standard_G1 |  Standard_G5 |
|  Standard_GS1 |  Standard_GS5 |

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Creare un Account di automazione Azure con la funzionalità di runas

La prima cosa che occorre fare è creare un account Azure automazione contenenti runbook utilizzato per ridimensionare le istanze di macchine Virtuali scala impostata. [Automazione di Azure](https://azure.microsoft.com/services/automation/) recente introdotta la funzionalità "Esegui come account" che semplifica l'impostazione di configurazione principale del servizio per l'esecuzione automatica di runbook per conto dell'utente molto semplice. Vengono fornite ulteriori informazioni vedere l'articolo seguente:

* [Eseguire l'autenticazione runbook con account Azure Esegui come](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importare runbook scala verticale automazione Azure in abbonamento

Runbook necessario ridimensionare verticalmente i set di scala macchine Virtuali già pubblicati nella raccolta Runbook automazione Azure. Per importare in abbonamento seguire i passaggi descritti in questo articolo:

* [Raccolte di runbook e modulo per l'automazione di Azure](../automation/automation-runbook-gallery.md)

Scegliere l'opzione Sfoglia raccolta dal menu runbook:

![Runbook da importare][runbooks]

Runbook che devono essere importati vengono visualizzati. Selezionare dal runbook in base alle necessità verticale proporzioni dei caratteri con o senza un nuovo provisioning del:

![Raccolta runbook][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Aggiungere un webhook il runbook

Dopo avere importato runbook che è necessario aggiungere un webhook dal runbook in modo che possono essere avviato da un avviso da un Set di scala macchine Virtuali. I dettagli della creazione di un webhook per il Runbook sono descritti in questo articolo:

* [Azure webhooks di automazione](../automation/automation-webhooks.md)

> [AZURE.NOTE] Assicurarsi di copiare webhook URI prima di chiudere la finestra di dialogo webhook come sarà necessario nella sezione successiva.

## <a name="add-an-alert-to-your-vm-scale-set"></a>Aggiungere un avviso per il Set di scala macchine Virtuali

Di seguito è uno script di PowerShell che mostra come aggiungere un avviso per un Set di scala macchine Virtuali. Consultare l'articolo seguente per ottenere il nome dell'unità di misura metriche per generare l'avviso in: [metriche comuni di Azure Monitor il ridimensionamento automatico](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [AZURE.NOTE] Si consiglia di configurare un intervallo di tempo adeguato per l'avviso per evitare la generazione di ridimensionamento verticale e le interruzioni di servizio associato, troppo spesso. Valutare la possibilità di una finestra di almeno 20-30 minuti o più. Valutare la possibilità di scala se è necessario evitare interruzioni orizzontale.

Per ulteriori informazioni su come creare avvisi vedere gli articoli seguenti:

* [Esempi di Guida introduttiva di PowerShell Monitor Azure](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Esempi di introduttive Monitor multipiattaforma CLI Azure](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>Riepilogo

In questo articolo è stato semplici esempi di ridimensionamento verticali. Con questi blocchi predefiniti - account di automazione, runbook, webhooks, gli avvisi, è possibile connettere una vasta gamma di eventi a un insieme di azioni personalizzata.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
