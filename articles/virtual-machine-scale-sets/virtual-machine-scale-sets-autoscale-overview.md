<properties
    pageTitle="Ridimensionamento automatico e macchina virtuale scalare set | Microsoft Azure"
    description="Informazioni sull'uso di diagnostica e risorse autoscale automaticamente le dimensioni macchine virtuali in un set di scala."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="davidmu"/>

# <a name="automatic-scaling-and-virtual-machine-scale-sets"></a>Ridimensionamento automatico e macchina virtuale scalare set

Il ridimensionamento automatico di macchine virtuali in un set di scala è la creazione o eliminazione di computer nel set di in base alle esigenze in modo che corrispondano requisiti relativi alle prestazioni. Man mano che aumenta il volume di lavoro, un'applicazione può richiedere risorse aggiuntive per poter eseguire in modo efficace attività.

Il ridimensionamento automatico è un processo automatico che consente di ridurre il sovraccarico di gestione. Riducendo il sovraccarico, non è necessario eseguire il monitoraggio delle prestazioni del sistema continuamente o decidere come gestire le risorse. Il ridimensionamento è un processo flessibile. Altre risorse possono essere aggiunti come aumenta il carico, ma come domanda delle riduzioni delle tariffe risorse possono essere rimossa per ridurre al minimo i costi e gestire i livelli di prestazioni.

Impostare il ridimensionamento automatico in una scala impostata utilizzando un modello di gestione risorse di Azure, PowerShell di Azure, CLI Azure o il portale di Azure.

## <a name="set-up-scaling-by-using-resource-manager-templates"></a>Impostare le proporzioni con i modelli di Manager delle risorse

Invece di distribuzione e gestione di ogni risorsa dell'applicazione separatamente, utilizzare un modello che distribuisce tutte le risorse in un'operazione su una singola e coordinata. Nel modello di risorse dell'applicazione sono definite e vengono specificati i parametri di distribuzione per ambienti diversi. Il modello è composto da JSON ed espressioni che è possibile utilizzare per creare i valori per la distribuzione. Per ulteriori informazioni, esaminare [i modelli di creazione condivisa Manager delle risorse di Azure](../resource-group-authoring-templates.md).

Specificare l'elemento capacità nel modello:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

Capacità identifica il numero di macchine virtuali nella finestra di impostazione. È possibile modificare manualmente la capacità distribuendo un modello con un valore diverso. Se si distribuisce un modello per modificare solo la capacità, è possibile includere solo l'elemento SKU con la capacità aggiornata.

Modificare automaticamente la capacità di una scala impostata utilizzando la combinazione di risorse autoscaleSettings e l'estensione di diagnostica.

### <a name="configure-the-azure-diagnostics-extension"></a>Configurare l'estensione di diagnostica Azure

Adattamento automatico può essere eseguita solo se la raccolta metriche ha esito positivo in ogni macchina virtuale nel set di scala L'estensione di diagnostica Azure fornisce le funzionalità di monitoraggio e diagnostica che soddisfa le esigenze di raccolta metriche della risorsa autoscale. È possibile installare l'estensione come parte del modello di Manager delle risorse.

In questo esempio mostra le variabili utilizzati nel modello per configurare l'estensione di diagnostica:

    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
    "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Thread Count\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

Parametri sono disponibili quando si distribuisce il modello. In questo esempio, il nome dell'account di archiviazione memorizzazione dei dati e il nome del set di scala da cui raccolti sono incluse. Anche in questo esempio, Windows Server verrà raccolte solo il numero di Thread contatore delle prestazioni. Tutti i contatori di prestazioni disponibili in Windows o Linux possono essere utilizzati per raccogliere informazioni di diagnostica e possono essere inclusi nella configurazione dell'estensione.

In questo esempio viene illustrata la definizione dell'estensione nel modello:

    "extensionProfile": {
      "extensions": [
        {
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
              "storageAccount": "[variables('diagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
              "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
              "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
              "storageAccountEndPoint": "https://core.windows.net"
            }
          }
        }
      ]
    }

Quando viene eseguita l'estensione di diagnostica, i dati vengono raccolti in una tabella in cui si trova nell'account di archiviazione specificato. Nella tabella WADPerformanceCounters, è possibile trovare i dati raccolti:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### <a name="configure-the-autoscalesettings-resource"></a>Configurare la risorsa autoScaleSettings

La risorsa autoscaleSettings utilizza le informazioni dall'estensione di diagnostica per decidere se aumentare o ridurre il numero di macchine virtuali nel set di scala.

In questo esempio viene illustrata la configurazione della risorsa nel modello:

    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 650
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 550
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
      }
    }

Nell'esempio precedente, vengono create due regole per la definizione di operazioni di ridimensionamento automatiche. La prima regola definisce l'azione scalabilità e la seconda regola l'azione di scala. Le regole vengono specificati questi valori:

- **metricName** - questo valore è uguale il contatore delle prestazioni definiti nella variabile wadperfcounter per l'estensione di diagnostica. Nell'esempio precedente, viene usato contatore Thread Count.  
- **metricResourceUri** - questo valore è l'identificatore di risorsa del set di scala macchina virtuale. Questo identificatore contiene il nome del gruppo di risorse, il nome del provider di risorse e il nome della scala imposta le dimensioni.
- **timeGrain** : questo valore è granularità dei valori che sono stati raccolti. Nell'esempio precedente, i dati raccolti in un intervallo di un minuto. Questo valore viene usato con finestra.
- **statistica** : questo valore determina il modo in cui vengono combinate le metriche per adattare l'operazione di ridimensionamento automatico. I valori possibili sono: Media, Min, Max.
- **finestra** – questo valore è l'intervallo di tempo in cui vengono raccolti i dati dell'istanza. Deve essere compreso tra 5 minuti e 12 ore.
- **aggregazione temporale** – questo valore determina come devono essere combinati i dati raccolti nel tempo. Il valore predefinito è Media. I valori possibili sono: Media, minimo, massimo, all'ultimo, totale, conteggio.
- l' **operatore** : questo valore è l'operatore viene utilizzato per confrontare i dati metrici e la soglia. I valori possibili sono: è uguale a, NotEquals, maggiore, GreaterThanOrEqual, LessThan, LessThanOrEqual.
- **soglia** : questo valore è un valore che attiva l'azione scala. Assicurarsi di fornire una sufficiente differenza tra la soglia per l'azione scalabilità e la soglia per l'azione di scala. Se si impostano i valori in modo che corrisponda, il sistema anticipa modifica costante, che impedisce l'implementazione di un'azione proporzioni. Ad esempio, non funziona se si impostano entrambe a 600 thread nell'esempio precedente.
- **direzione** : questo valore determina l'azione che viene eseguita quando si ottiene il valore di soglia. I valori possibili sono aumentare o diminuire.
- **tipo** : questo valore è il tipo di azione che dovrebbe essere eseguita e deve essere impostata su ChangeCount.
- **valore** : questo valore è il numero di macchine virtuali che vengono aggiunti o eliminati dall'elenco scala. Questo valore deve essere maggiore o uguale a 1.
- **raffreddamento** – questo valore è la quantità di tempo di attesa dopo l'ultima operazione di ridimensionamento, prima che venga eseguita l'azione successiva. Questo valore deve essere compreso tra un minuto e una settimana.

A seconda delle prestazioni in uso, alcuni degli elementi di configurazione del modello vengono utilizzati in modo leggermente diverso. Nell'esempio precedente, il contatore delle prestazioni è conteggio Thread, il valore di soglia è 650 per un'azione scalabilità e il valore di soglia è 550 per l'azione di scala. Se si utilizza un contatore, ad esempio % tempo processore, il valore di soglia è impostato sulla percentuale di utilizzo della CPU che determina un'operazione di ridimensionamento.

Quando viene creato un carico nelle macchine virtuali che attiva un'azione scala, la capacità del set di viene aumentata in base al valore nel modello. Ad esempio, in una scala set nel punto in cui la capacità è impostata su 3 e il valore dell'azione scala è impostato su 1:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

Quando viene creato il carico che causa il numero di thread Media passare sopra la soglia di 650:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

Viene attivata un'azione scalabilità che causa la capacità del set di uno aumentato:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

E una macchina virtuale viene aggiunto al set di scala a:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

Dopo un periodo di raffreddamento pari a cinque minuti, se il numero medio di thread nei computer rimane 600, viene aggiunto al set di un altro computer. Se il numero di thread medio rimane sotto 550, la capacità del set di scala viene ridotto di uno e un computer viene rimosso dall'insieme.

## <a name="set-up-scaling-using-azure-powershell"></a>Impostare le proporzioni dei caratteri tramite PowerShell Azure

Per visualizzare esempi di utilizzo di PowerShell per impostare il ridimensionamento automatico, osservare [Azure Monitor PowerShell quick start esempi](../monitoring-and-diagnostics/insights-powershell-samples.md).

## <a name="set-up-scaling-using-azure-cli"></a>Impostare il ridimensionamento CLI Azure

Per visualizzare esempi di utilizzo Azure CLI per impostare il ridimensionamento automatico, osservare [Monitor Azure multipiattaforma CLI quick start esempi](../monitoring-and-diagnostics/insights-cli-samples.md).

## <a name="set-up-scaling-using-the-azure-portal"></a>Impostare le proporzioni dei caratteri tramite il portale di Azure

Per visualizzare un esempio di tramite il portale di Azure per impostare il ridimensionamento automatico, osservare [Crea un Set di scala macchina virtuale tramite il portale di Azure](virtual-machine-scale-sets-portal-create.md).

## <a name="investigate-scaling-actions"></a>Provare a utilizzare azioni proporzioni

- [Portale di azure]() - attualmente è possibile ottenere una quantità di informazioni tramite il portale limitata.
- [Esplora risorse azure]() - questo strumento più appropriato per l'esplorazione lo stato corrente del set di scala. Seguire questo percorso e verrà visualizzata la visualizzazione di istanza del set di scala creato: abbonamenti > {abbonamento} > resourceGroups > {il gruppo di risorse} > provider > Microsoft.Compute > virtualMachineScaleSets > {set di scala} > virtualMachines
- Azure - PowerShell usare questo comando per ottenere informazioni:

        Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
        Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput

- Connettere la macchina virtuale jumpbox come si farebbe un altro computer e quindi è possibile accedere in remoto macchine virtuali in scala impostare per monitorare singoli processi.

## <a name="next-steps"></a>Passaggi successivi

- Osservare [automaticamente le dimensioni macchine in un Set di scala macchina virtuale](virtual-machine-scale-sets-windows-autoscale.md) per visualizzare un esempio di come creare una scala impostare con il ridimensionamento automatico configurato.
- Trovare esempi di Azure Monitor in [Azure Monitor PowerShell quick start esempi](../monitoring-and-diagnostics/insights-powershell-samples.md) di funzionalità di monitoraggio
- Informazioni sulle caratteristiche di notifica di [usare le azioni autoscale per inviare posta elettronica e webhook notifiche di avviso in Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md).
- Informazioni su come [registri di controllo Usa per l'invio di notifiche di avviso di posta elettronica e webhook nel Monitor Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
- Informazioni sugli [scenari avanzati autoscale](./virtual-machine-scale-sets-advanced-autoscale.md).
