<properties
    pageTitle="Creare una macchina virtuale di Windows con il monitoraggio e diagnostica utilizzando il modello di gestione risorse Azure | Microsoft Azure"
    description="Utilizzare un modello di gestione delle risorse Azure per creare una nuova macchina virtuale di Windows con estensione diagnostica Windows Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="sbtron"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="saurabh"/>

# <a name="create-a-windows-virtual-machine-with-monitoring-and-diagnostics-using-azure-resource-manager-template"></a>Creare una macchina virtuale di Windows con il monitoraggio e diagnostica utilizzando il modello di gestione risorse di Azure

L'estensione di diagnostica Azure fornisce il monitoraggio e funzionalità di diagnostica in un Windows base Azure macchina virtuale. È possibile abilitare queste funzionalità sul computer virtuale includendo l'estensione come parte del modello di gestione delle risorse azure. Vedere [Creazione di modelli di gestione risorse Azure con estensioni macchine Virtuali](virtual-machines-windows-extensions-authoring-templates.md) per ulteriori informazioni sull'inclusione di qualsiasi interno come parte di un modello di macchina virtuale. Questo articolo descrive come è possibile aggiungere l'estensione di Azure diagnostica a un modello di macchina virtuale di windows.  
  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Aggiungere l'estensione di Azure diagnostica per la definizione di risorse macchine Virtuali 

Per abilitare l'estensione di diagnostica una macchina virtuale Windows è necessario aggiungere l'estensione come risorsa macchine Virtuali nel modello di gestione delle risorse.

Per una gestione risorse semplice macchina virtuale in base aggiungere la configurazione di estensione per la matrice di *risorse* per la macchina virtuale: 

    "resources": [
                {
                    "name": "Microsoft.Insights.VMDiagnosticsSettings",
                    "type": "extensions",
                    "location": "[resourceGroup().location]",
                    "apiVersion": "2015-06-15",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
                    ],
                    "tags": {
                        "displayName": "AzureDiagnostics"
                    },
                    "properties": {
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "type": "IaaSDiagnostics",
                        "typeHandlerVersion": "1.5",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
                        },
                        "protectedSettings": {
                            "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                        }
                    }
                }
            ]


Un'altra convenzione comune aggiungere la configurazione dell'estensione in corrispondenza del nodo di risorse radice del modello anziché definirla nodo risorse della macchina virtuale. Con questo approccio è necessario specificare una relazione gerarchica tra l'estensione e la macchina virtuale con i valori di *nome* e *tipo* . Per esempio: 
  
    "name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
    "type": "Microsoft.Compute/virtualMachines/extensions",

L'estensione è sempre associato la macchina virtuale, è possibile direttamente definire direttamente nodo risorsa della macchina virtuale o definire il livello di base e utilizzare la convenzione di denominazione gerarchica per associarlo con la macchina virtuale.

Per macchina virtuale scala imposta la configurazione delle estensioni è specificata nella proprietà *extensionProfile* di *VirtualMachineProfile*.
   
La proprietà di *publisher* con il valore di **Microsoft.Azure.Diagnostics** e la proprietà *tipo* con il valore di **IaaSDiagnostics** identificano l'estensione di Azure diagnostica.

Il valore della proprietà *name* può essere utilizzato per fare riferimento all'articolo estensione nel gruppo di risorse. Impostazione specificamente per **Microsoft.Insights.VMDiagnosticsSettings** consente di identificare facilmente il Azure classica portale portale garantire che i grafici monitoraggio visualizzate correttamente nel portale di classica Azure.

*TypeHandlerVersion* specifica la versione dell'estensione che desidera utilizzare. L'impostazione *autoUpgradeMinorVersion* versione secondaria **vero** garantisce che verrà visualizzato l'ultima versione secondaria dell'estensione che è disponibile. Si consiglia di impostare sempre *autoUpgradeMinorVersion* sia sempre **true** in modo che viene sempre visualizzato come utilizzare l'estensione di diagnostica disponibili più recente con tutte le nuove caratteristiche e le correzioni di bug. 

L'elemento *Impostazioni* contiene le proprietà di configurazione dell'estensione che può essere impostato e leggere nuovamente l'estensione (definito talvolta configurazione pubblica). La proprietà *xmlcfg* contiene configurazione basati su xml per i registri di diagnostica contatori così raccolti dall'agente di diagnostica. Per ulteriori informazioni sullo schema xml, vedere [Schema di configurazione di diagnostica](https://msdn.microsoft.com/library/azure/dn782207.aspx) . In genere è per archiviare la configurazione xml effettivo come una variabile nel modello di gestione di risorse Azure e quindi concatenate e base 64 li codifica per impostare il valore per *xmlcfg*. Vedere la sezione su [variabili di configurazione di diagnostica](#diagnostics-configuration-variables) per comprendere ulteriori informazioni su come archiviare i dati xml in variabili. La proprietà *storageAccount* specifica il nome dell'account di archiviazione in cui verranno trasferiti dati di diagnostica. 
 
Le proprietà in *protectedSettings* (a volte denominato configurazione privata) possono essere impostate ma non possono essere letti nuovamente dopo l'impostazione. In sola lettura natura *protectedSettings* risulta utile per l'archiviazione delle informazioni riservate come la chiave account lo spazio di archiviazione in cui verranno scritti i dati di diagnostica.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Specifica l'account di archiviazione di diagnostica come parametri 

Frammento di diagnostica estensione json riportato sopra prende in considerazione due parametri *existingdiagnosticsStorageAccountName* ed *existingdiagnosticsStorageResourceGroup* per specificare l'account di archiviazione di diagnostica in cui verranno archiviati i dati di diagnostica. Se si specifica l'account di archiviazione di diagnostica come un parametro rende facile cambiare l'account di archiviazione di diagnostica in ambienti diversi, ad esempio può essere necessario usare un account di archiviazione diagnostica diversi per la verifica e una diversa per la distribuzione di produzione.  

        "existingdiagnosticsStorageAccountName": {
            "type": "string",
            "metadata": {
        "description": "The name of an existing storage account to which diagnostics data will be transfered."
            }        
        },
        "existingdiagnosticsStorageResourceGroup": {
            "type": "string",
            "metadata": {
        "description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
            }
        }

È buona norma specificare un account di archiviazione di diagnostica in un gruppo di risorse diverso da quello del gruppo di risorse per la macchina virtuale. Un gruppo di risorse può essere considerato un'unità di distribuzione con il proprio ciclo di vita, una macchina virtuale può essere distribuita e ridistribuire come nuovi aggiornamenti configurazioni vengono applicati a essa, ma è possibile continuare a memorizzare i dati di diagnostica nello stesso account di archiviazione tra tali distribuzioni macchina virtuale. Con l'account di archiviazione in un'altra risorsa Abilita l'account di archiviazione accettare dati provenienti da varie distribuzioni rendendo più semplice per la risoluzione dei problemi nelle varie versioni.

>[AZURE.NOTE] Se si crea un modello di macchina virtuale di windows da Visual Studio l'account di archiviazione predefinito potrebbe impostare come utilizzare lo stesso account di archiviazione in cui viene caricata la macchina virtuale disco rigido virtuale. In modo da semplificare l'installazione e configurazione iniziali di macchina virtuale. È opportuno nuovamente includere il modello per l'utilizzo di un account di archiviazione diversi che può essere passato come parametro. 

## <a name="diagnostics-configuration-variables"></a>Variabili di configurazione di diagnostica
 
Frammento di diagnostica estensione json riportato sopra definisce una variabile *accountid* per semplificare la Guida la chiave account di archiviazione per l'archiviazione di diagnostica:   
    
    "accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"


La proprietà *xmlcfg* per l'estensione di diagnostica viene definita mediante più variabili che concatenate. I valori delle variabili sono in formato xml in modo che deve essere eseguito in modo corretto per l'impostazione di variabili json.

Di seguito viene descritto il codice xml configurazione diagnostica raccoglie le contatori livello di sistema standard insieme alcuni registri eventi di windows e registri dell'infrastruttura di diagnostica. Sono state escape e formattato correttamente in modo che la configurazione può essere incollata direttamente nella sezione variabili del modello. Vedere lo [Schema di configurazione di diagnostica](https://msdn.microsoft.com/library/azure/dn782207.aspx) per un esempio più umano leggibile del codice xml configurazione.
    
        "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
        "wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
        "wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
        "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"

Il nodo xml definizione metriche nella configurazione precedente è un elemento di configurazione importanti poiché consente di definire come di aggregato e archiviati i contatori delle prestazioni definiti in precedenza nel xml nel nodo *PerformanceCounter* . 

> [AZURE.IMPORTANT] Queste metriche unità i grafici e gli avvisi di monitoraggio nel portale di Azure.  Il nodo **metriche** con *resourceID* **MetricAggregation** da includere nella configurazione di diagnostica per la macchina virtuale se si desidera visualizzare i dati di monitoraggio macchine Virtuali nel portale di Azure. 

Di seguito è illustrato un esempio di codice xml di definizione dati: 

        <Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>

L'attributo *resourceID* identifica la macchina virtuale nell'abbonamento. Assicurarsi di usare le funzioni subscription() e resourceGroup() in modo che il modello aggiorna automaticamente i valori in base alla sottoscrizione e si distribuisce in gruppo di risorse.

Se si siano creando più macchine virtuali in un ciclo sarà necessario inserire il valore *resourceID* con una funzione copyIndex() per distinguere correttamente ogni macchina virtuale singoli. Il valore di *xmlCfg* può essere aggiornato a tale scopo come indicato di seguito:  

    "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 

Il valore di MetricAggregation di *PT1H* e *PT1M* indicare un'aggregazione in un minuto e un'aggregazione in un'ora.

## <a name="wadmetrics-tables-in-storage"></a>Tabelle di WADMetrics di spazio di archiviazione

La configurazione di metriche sopra genererà tabelle nell'account di archiviazione di diagnostica con le convenzioni di denominazione seguenti:

- **WADMetrics** : prefisso Standard per tutte le tabelle WADMetrics
- **PT1H** o **PT1M** : indica che la tabella contiene aggregare i dati su 1 ora o minuto
- **P10D** : indica la tabella conterrà dati per 10 giorni che vanno dalla quando la tabella avviata la raccolta di dati
- **V2S** : costante stringa
- **aaaammgg** : la data di inizio in cui la tabella la raccolta di dati

Esempio: *WADMetricsPT1HP10DV2S20151108* conterrà dati metriche aggregati più di un'ora per avviare 2015 di novembre 11 10 giorni    

Ogni tabella WADMetrics conterrà le colonne seguenti:

- **PartitionKey**: il partitionkey sia impostata in base al valore *resourceID* per identificare la risorsa macchina virtuale. per ad esempio: 002Fsubscriptions:<subscriptionID>: 002FresourceGroups:002F<ResourceGroupName>: 002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
- **RowKey** : segue il formato <Descending time tick>:<Performance Counter Name>. Il calcolo dei segni di graduazione time decrescente in segni di graduazione tempo massimo meno l'ora di inizio del periodo di aggregazione. Ad esempio Se il periodo di esempio iniziato in 2015 di novembre 10 di 00:00Hrs UTC quindi il calcolo: DateTime.MaxValue.Ticks - (DateTime(2015,11,10,0,0,0,DateTimeKind.Utc) nuovo. Segni di graduazione). Per la memoria chiave riga del contatore delle prestazioni byte disponibili verrà rappresentati come: 2519551871999999999__:005CMemory:005CAvailable:0020 byte
- **CounterName** : È il nome del contatore delle prestazioni. Corrisponde a *counterSpecifier* definito nella configurazione xml.
- **Massimo** : il valore massimo del contatore delle prestazioni durante il periodo di aggregazione.
- **Minima** : il valore minimo del contatore delle prestazioni durante il periodo di aggregazione.
- **Totale** : la somma di tutti i valori del contatore delle prestazioni segnalati durante il periodo di aggregazione.
- **Conteggio** : il numero totale di valori segnalati del contatore delle prestazioni.
- **Media** : il valore Media (totale) del contatore delle prestazioni durante il periodo di aggregazione.


## <a name="next-steps"></a>Passaggi successivi

- Per un modello di esempio completo di una macchina virtuale di Windows con estensione diagnostica vedere [201 macchine virtuali-monitoraggio-diagnostica-estensione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
- Distribuire il modello di gestione delle risorse tramite [PowerShell Azure](virtual-machines-windows-ps-manage.md) o [riga di comando di Azure](virtual-machines-linux-cli-deploy-templates.md)
- Ulteriori informazioni sulla [creazione di modelli di gestione risorse di Azure](../resource-group-authoring-templates.md)







