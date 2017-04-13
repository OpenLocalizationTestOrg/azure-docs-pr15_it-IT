<properties
    pageTitle="Esempi di Guida introduttiva di Azure PowerShell Monitor. | Microsoft Azure"
    description="Usare PowerShell per accedere alle funzionalità di monitoraggio di Azure ad esempio autoscale, avvisi, webhooks e registri delle attività di ricerca."
    authors="kamathashwin"
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
    ms.date="10/26/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor-powershell-quick-start-samples"></a>Esempi di Guida introduttiva di PowerShell Monitor Azure

In questo articolo viene indicato esempio di comandi di PowerShell che consentono di accedere a caratteristiche di Azure Monitor. Monitor Azure consente per servizi Cloud AutoScale, macchine virtuali e Web App e per inviare le notifiche di avviso o di chiamarlo URL web in base ai valori di dati di telemetria configurato.

>[AZURE.NOTE] Monitor Azure è il nuovo nome per ciò che era definito "Azure approfondimenti" fino a 25 ° settembre 2016. Tuttavia, gli spazi dei nomi e quindi comandi riportati di seguito contengono ancora "che".

## <a name="set-up-powershell"></a>Configurare PowerShell
Se non è ancora disponibile, configurare PowerShell per eseguire nel computer. Per ulteriori informazioni, vedere [come installare e configurare PowerShell](../powershell-install-configure.md) .

## <a name="examples-in-this-article"></a>Esempi in questo articolo

Negli esempi in questo articolo viene illustrato come è possibile utilizzare i cmdlet di Azure Monitor. È anche possibile esaminare l'elenco completo di Azure Monitor PowerShell cmdlet al [cmdlet Azure Monitor (informazioni dettagliate sui)](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx).


## <a name="sign-in-and-use-subscriptions"></a>Accedere e utilizzare le sottoscrizioni

Prima di tutto, accedere all'abbonamento Azure.

```PowerShell
Login-AzureRmAccount
```

È necessario effettuare l'accesso. Una volta eseguita, l'Account, vengono visualizzati TenantId e Id abbonamento predefinito. Tutti i cmdlet Azure lavorare nel contesto di abbonamento predefinito. Per visualizzare l'elenco delle sottoscrizioni che è possibile accedere, utilizzare il comando seguente.

```PowerShell
Get-AzureRmSubscription
```

Per modificare il contesto di lavoro a una sottoscrizione diversa, utilizzare il comando seguente.

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-audit-logs-for-a-subscription"></a>Recuperare i log di controllo per una sottoscrizione
Utilizzare la `Get-AzureRmLog` cmdlet.  Di seguito sono riportati alcuni esempi comuni.

È possibile ottenere le voci di log da questa data/ora per presentare:

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Ottenere le voci di log in un intervallo di data e ora:

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Ottenere voci da un determinato gruppo di risorse:

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Ottenere voci da un provider di risorse specifici in un intervallo di data e ora:

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Ottenere tutte le voci di log con un chiamante specifico:

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

Il comando seguente recupera gli ultimi 1000 eventi nel log di controllo:

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog`supporta molti altri parametri. Vedere la `Get-AzureRmLog` Guida di riferimento per altre informazioni.

>[AZURE.NOTE] `Get-AzureRmLog`fornisce solo 15 giorni dalla cronologia. Utilizzando il parametro **- MaxEvents** consente di eseguire una query ultimo N eventi, oltre 15 giorni. Per gli eventi di accesso più di 15 giorni prima, utilizzare l'API REST o SDK (esempio c# mediante il SDK). Se non si include **ora di inizio**, il valore predefinito è **ora fine** meno di un'ora. Se non si include **ora fine**, il valore predefinito è ora corrente. Tutte le ore sono in formato UTC.

## <a name="retrieve-alerts-history"></a>Recuperare cronologia avvisi
Per visualizzare tutti gli eventi degli avvisi, è possibile cercare i registri di Manager delle risorse Azure mediante negli esempi seguenti.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Per visualizzare la cronologia di una regola di avviso specifica, è possibile utilizzare il `Get-AzureRmAlertHistory` cmdlet, passando l'ID risorsa della regola di avviso.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

Il `Get-AzureRmAlertHistory` cmdlet supporta diversi parametri. Ulteriori informazioni, vedere [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).


## <a name="retrieve-information-on-alert-rules"></a>Recuperare le informazioni sulle regole di avviso
Tutti i comandi seguenti agire su un gruppo di risorse denominato "montest".

Visualizzare tutte le proprietà della regola di avviso:

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Recuperare tutti gli avvisi per un gruppo di risorse:

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

Recuperare tutte le regole di avviso impostato per una risorsa di destinazione. Ad esempio, tutte le regole di avviso impostare una macchina virtuale.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule`supporta gli altri parametri. Per ulteriori informazioni, vedere [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) .

## <a name="create-alert-rules"></a>Creare regole di avviso
È possibile utilizzare il `Add-AlertRule` cmdlet per creare, aggiornare o disattivare una regola di avviso.

È possibile creare proprietà di posta elettronica e webhook utilizzando `New-AzureRmAlertRuleEmail` e `New-AzureRmAlertRuleWebhook`, rispettivamente. In cmdlet regola di avviso assegnarli come azioni per la proprietà di **Azioni** della regola di avviso.

La sezione successiva contiene un esempio che illustra come creare una regola di avviso con diversi parametri.

### <a name="alert-rule-on-a-metric"></a>Regola di avviso in un'unità di misura metriche
Nella tabella seguente descrive i parametri e valori utilizzati per creare un avviso utilizzando una metrica.


|parametro|valore|
|---|---|
|Nome|  simpletestdiskwrite|
|Percorso di questa regola di avviso|   Stati Uniti orientali|
|ResourceGroup| montest|
|TargetResourceId|  /Subscriptions/s1/resourceGroups/montest/Providers/Microsoft.COMPUTE/virtualMachines/testconfig|
|MetricName dell'avviso che viene creato|   \PhysicalDisk (totale) \Disk scrittura/sec. Vedere la `Get-MetricDefinitions` cmdlet sotto informazioni su come recuperare i nomi di metriche esatti|
|operatore|  Maggiore|
|Valore di soglia (numero/sec in per questo unità di misura metriche)|    1|
|WindowSize (formato hh)|  00:05:00|
|aggregatore (statistico di metrica, che utilizza Media conteggio, in questo caso)|  Media|
|messaggi di posta elettronica personalizzati (stringa)|'foo@example.com','bar@example.com'|
|inviare posta elettronica ai proprietari e collaboratori lettori|    -SendToServiceOwners|

Creare un'azione di posta elettronica

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Creare un'azione Webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Creare la regola di avviso in unità di misura metriche % CPU in una macchina virtuale classica

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Recuperare la regola di avviso

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Il cmdlet avviso Aggiungi Aggiorna anche la regola se esiste già una regola di avviso per le proprietà. Per disattivare una regola di avviso, includere il parametro **- DisableRule**.

### <a name="alert-on-audit-log-event"></a>Avvisa in evento del log di controllo

>[AZURE.NOTE] Questa caratteristica è ancora in anteprima.

In questo scenario, è necessario inviare posta elettronica quando un sito Web è stato avviato correttamente nel proprio abbonamento nel gruppo di risorse *abhingrgtest123*.

Imposta una regola di posta elettronica

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Imposta una regola webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Creare la regola sull'evento

```PowerShell
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

Recuperare la regola di avviso

```PowerShell
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

Il `Add-AlertRule` cmdlet consente di vari altri parametri. Ulteriori informazioni, vedere [Aggiungere AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Ottenere un elenco dei parametri disponibili per gli avvisi
È possibile utilizzare il `Get-AzureRmMetricDefinition` cmdlet per visualizzare l'elenco di tutte le metriche per una risorsa specifica.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

Nell'esempio seguente genera una tabella con la metrica nome e l'unità di essa.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Un elenco completo delle opzioni disponibili per `Get-AzureRmMetricDefinition` è disponibile in [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).


## <a name="create-and-manage-autoscale-settings"></a>Creare e gestire le impostazioni di AutoScale
Una risorsa, ad esempio un'app Web, macchine Virtuali, servizio Cloud o macchine Virtuali scala impostare può avere solo un'impostazione di autoscale configurata per renderla.
Tuttavia, ogni impostazione autoscale può avere più profili. Ad esempio, uno per un profilo scala basato sulle prestazioni e l'altro di un impiego in base a profilo. Ogni profilo può avere più regole configurate su di esso. Per ulteriori informazioni su Autoscale, vedere [come per scalare automaticamente un'applicazione](../cloud-services/cloud-services-how-to-scale.md).

Ecco i passaggi che verrà usata:

1. Creare regole.
2. Creare profili mapping le regole create in precedenza ai profili.
3. Facoltativo: Creare le notifiche relative a autoscale configurando le proprietà webhook e posta elettronica.
4. Creare un'impostazione autoscale con un nome per la risorsa di destinazione mediante la mappatura di profili e notifiche creata in precedenza.

Nell'esempio seguente mostrano come è possibile creare un'impostazione Autoscale per una scala di macchine Virtuali impostare per un sistema operativo Windows in base a utilizzando la metrica di utilizzo della CPU.

Prima di tutto creare una regola per scalabilità, con l'aumento di conteggio istanza.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 0.01 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```     

Creare una regola per scala aggiuntivo, con una riduzione di conteggio istanza.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 2 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```

Creare un profilo per le regole.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Creare una proprietà webhook.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Creare la proprietà di notifica per l'impostazione autoscale, tra cui posta elettronica e webhook creata in precedenza.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Infine, creare l'impostazione autoscale per aggiungere il profilo appena creato.

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Per ulteriori informazioni sulla gestione delle impostazioni Autoscale, vedere [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Cronologia AutoScale
Nell'esempio seguente mostra come è possibile visualizzare autoscale recenti ed eventi di avviso. Utilizzare la ricerca di log di controllo per visualizzare la cronologia di Autoscale.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

È possibile utilizzare il `Get-AzureRmAutoScaleHistory` cmdlet per recuperare AutoScale cronologia.

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Per ulteriori informazioni, vedere [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Visualizzare i dettagli di un'impostazione autoscale
È possibile utilizzare il `Get-Autoscalesetting` cmdlet per recuperare ulteriori informazioni sull'impostazione autoscale.

Nell'esempio seguente mostra i dettagli di tutte le impostazioni di autoscale nel gruppo di risorse 'myrg1'.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

Nell'esempio seguente mostra i dettagli di tutte le impostazioni di autoscale il gruppo di risorse 'myrg1' e in particolare l'impostazione di autoscale denominata 'MyScaleVMSSSetting'.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Rimuovere un'impostazione autoscale
È possibile utilizzare il `Remove-Autoscalesetting` cmdlet per eliminare un'impostazione autoscale.

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-audit-logs"></a>Gestire i profili di log per i log di controllo

È possibile creare un *profilo log* esportare dati dal log di controllo in un account di archiviazione ed è possibile configurare la conservazione dei dati per renderla. Facoltativamente, è possibile trasmettere anche i dati per l'Hub di evento. Si noti che questa funzionalità è attualmente in anteprima ed è possono creare solo un profilo di log per ogni abbonamento. È possibile utilizzare i cmdlet seguenti con l'abbonamento corrente per creare e gestire i profili di log. È anche possibile scegliere un particolare abbonamento. Sebbene PowerShell per impostazione predefinita per l'abbonamento corrente, è sempre possibile modificare tale utilizzando `Set-AzureRmContext`. È possibile configurare i registri di controllo per la distribuzione dei dati a qualsiasi account di archiviazione o l'evento Hub all'interno di tale sottoscrizione. Dati viene scritto come file blob in formato JSON.

### <a name="get-a-log-profile"></a>Ottenere un profilo di log
Per recuperare i profili di log esistenti, utilizzare la `Get-AzureRmLogProfile` cmdlet.

### <a name="add-a-log-profile-without-data-retention"></a>Aggiungere un profilo log senza la conservazione dei dati

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Rimuovere un profilo di log

```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Aggiungere un profilo di log con la conservazione dei dati

È possibile specificare la proprietà **- RetentionInDays** con il numero di giorni, come un intero positivo, in cui i dati vengono mantenuti.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Aggiungere il profilo log con criteri di conservazione ed EventHub
Oltre all'account di archiviazione il routing dei dati, è possibile anche inoltrarla a un Hub di evento. Si noti che in questa versione di anteprima e lo spazio di archiviazione è obbligatoria configurazione account ma configurazione evento Hub è facoltativa.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Configurare i registri di diagnostica
Molti servizi Azure forniscono registri aggiuntivi e telemetria, inclusi i gruppi di sicurezza di Azure rete, servizi di bilanciamento del carico Software, chiave archivio, servizi di ricerca di Windows Azure, e App logica e può essere configurati per salvare i dati nel proprio account di archiviazione Azure. L'operazione può essere eseguita solo a un livello di risorsa e l'account di archiviazione deve essere presenta nella stessa regione la risorsa di destinazione in cui è configurato l'impostazione di diagnostica.

### <a name="get-diagnostic-setting"></a>Ottenere impostazione diagnostica

```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Disabilitare l'impostazione di diagnostica

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Impostazione diagnostica senza criteri di conservazione

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Impostazione diagnostica con criteri di conservazione

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Impostazione diagnostica con criteri di conservazione per una categoria specifica del log

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```
