<properties
   pageTitle="Risorse di automazione soluzioni OMS | Microsoft Azure"
   description="Soluzioni in OMS conterrà in genere runbook in Azure automazione per automatizzare processi, ad esempio la raccolta e l'elaborazione dei dati di monitoraggio.  In questo articolo viene descritto come includere runbook e le relative risorse correlate in una soluzione."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/19/2016"
   ms.author="bwren" />

# <a name="automation-resources-in-oms-solutions-preview"></a>Risorse di automazione soluzioni OMS (Preview)

>[AZURE.NOTE]Si tratta documentazione preliminare per la creazione di soluzioni di gestione in OMS che sono attualmente in anteprima. Qualsiasi schema descritto di seguito è soggetta a modifiche.   

[Soluzioni di gestione in OMS](operations-management-suite-solutions.md) conterrà in genere runbook in Azure automazione per automatizzare processi, ad esempio la raccolta e l'elaborazione dei dati di monitoraggio.  Oltre a runbook, gli account di automazione include beni, ad esempio le variabili e le pianificazioni che supportano runbook usati nella soluzione.  In questo articolo viene descritto come includere runbook e le relative risorse correlate in una soluzione.

>[AZURE.NOTE]Negli esempi inclusi in questo articolo utilizzano parametri e variabili che sono necessari o comuni per soluzioni per la gestione e descritte soluzioni di [Gestione](operations-management-suite-solutions-creating.md) di creazione di operazioni di gestione famiglia di prodotti (OMS) 


## <a name="prerequisites"></a>Prerequisiti
In questo articolo si presuppone che si ha già familiarità con la modalità per [creare una soluzione di gestione](operations-management-suite-solutions-creating.md) e la struttura di un file di soluzione.

## <a name="samples"></a>Esempi
È possibile ottenere modelli di esempio Manager delle risorse per le risorse di automazione dai [modelli di Guida introduttiva in GitHub](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates).

## <a name="automation-account"></a>Account di automazione
Tutte le risorse di automazione di Azure sono contenute in un [account di automazione](../automation/automation-security-overview.md#automation-account-overview).  Come descritto in [account di automazione e area di lavoro OMS](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account) l'account di automazione non è incluso in soluzione di gestione ma occorre prima la soluzione viene installata.  Se non è disponibile, l'installazione di soluzione avrà esito negativo.

Il nome del proprio account di automazione è il nome di ogni risorsa di automazione.  A tale scopo della soluzione con il parametro **nome account** come illustrato nell'esempio seguente di una risorsa runbook.
    
    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbook
[Automazione di Azure runbook](../automation/automation-runbook-types.md) risorse con un tipo di **Microsoft.Automation/automationAccounts/runbooks** e dispongono le proprietà nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| runbookType | Specifica i tipi di dal runbook. <br><br> Script - script di PowerShell <br>PowerShell - PowerShell del flusso di lavoro <br> GraphPowerShell - runbook script di PowerShell con interfaccia grafica <br> GraphPowerShellWorkflow - PowerShell grafica del flusso di lavoro runbook   |
| logProgress | Specifica se devono essere generati [record lo stato di avanzamento](../automation/automation-runbook-output-and-messages.md) dal runbook. |
| logVerbose  | Specifica se [dettagliato record](../automation/automation-runbook-output-and-messages.md) devono essere generati dal runbook. |
| Descrizione | Descrizione facoltativa per dal runbook. |
| publishContentLink | Specifica il contenuto dal runbook. <br><br>URI - Uri del contenuto dal runbook.  Sarà un file ps1 per runbook PowerShell e Script e un file esportato con interfaccia grafica runbook per runbook un grafico.  <br> versione: versione dal runbook per il proprio tenerne traccia. |

Esempio di una risorsa runbook è inferiore.  In questo caso, viene recuperato dal runbook dalla [Raccolta di PowerShell](https://www.powershellgallery.com).

    "name": "[concat(parameters('accountName'), '/Start-AzureV2VMs'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]"
    ],
    "tags": { },
    "properties": {
        "runbookType": "PowerShell",
        "logProgress": "true",
        "logVerbose": "true",
        "description": "",
        "publishContentLink": {
            "uri": "https://www.powershellgallery.com/api/v2/items/psscript/package/Update-ModulesInAutomationToLatestVersion/1.0/Start-AzureV2VMs.ps1",
            "version": "1.0"
        }
    }

### <a name="starting-a-runbook"></a>Avviare un runbook
Esistono due metodi per avviare un runbook in una soluzione di gestione.

- Per iniziare dal runbook quando si installa la soluzione, creare una [risorsa di processo](#automation-jobs) come descritto di seguito.
- Per iniziare dal runbook alla programmazione di un, creare una [pianificazione delle risorse](#schedules) , come descritto di seguito. 


## <a name="automation-jobs"></a>Processi di automazione
Per avviare un runbook durante l'installazione della soluzione di gestione, si crea una risorsa **lavoro** .  Risorse di processo con un tipo di **Microsoft.Automation/automationAccounts/jobs** e dispongono le proprietà nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| runbook    | Solo **nome** entità con nome dal runbook.  |
| parametri | Entità per ogni parametro necessario affinché dal runbook. |


Il processo include il nome runbook e i valori dei parametri devono essere inviati dal runbook.  Il processo deve [dipendono](operations-management-suite-solutions-creating.md#resources) dal runbook che viene avviato dopo dal runbook deve essere creato prima del lavoro.  È anche possibile creare dipendenze in altri processi runbook che deve essere completata prima di quello corrente.

Il nome di una risorsa lavoro deve contenere un GUID che in genere assegnato da un parametro.  È possibile leggere altre informazioni sui parametri GUID soluzioni di [creazione di operazioni di gestione famiglia di prodotti (OMS)](operations-management-suite-solutions-creating.md#parameters).  

Di seguito è illustrato un esempio di una risorsa di processo che avvia un runbook durante l'installazione della soluzione di gestione.  Uno altri runbook deve essere completata prima di questo uno si avvia, quindi presenta dipendenze sui processi per tale runbook.  I dettagli per il processo vengono forniti tramite i parametri e variabili, piuttosto che viene specificato direttamente.

    {
        "name": "[concat(parameters('accountName'), '/', parameters('startVmsRunbookGuid'))]",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "location": "[parameters('regionId')]",
        "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]",
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('initialPrepRunbookGuid'))]"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "[variables('startVmsRunbookName')]"
            },
            "parameters": {
                "AzureConnectionAssetName": "[variables('AzureConnectionAssetName')]",
                "ResourceGroupName": "[variables('ResourceGroupName')]"
            }
        }
    }


## <a name="certificates"></a>Certificati
[Automazione di Azure certificati](../automation/automation-certificates.md) con un tipo di **Microsoft.Automation/automationAccounts/certificates** e disporre le proprietà nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| base64Value   | Valore di base 64 per il certificato. |
| identificazione personale    | Identificazione personale per il certificato. |

Esempio di una risorsa di certificato è inferiore.

    "name": "[concat(parameters('accountName'), '/MyCertificate')]",
    "type": "certificates",
    "apiVersion": "2015-01-01-preview",
    "location": "[parameters('regionId')]",
    "tags": {},
    "dependsOn": [
    ],
    "properties": {
        "base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
        "thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
    }

## <a name="credentials"></a>Credenziali
[Automazione di Azure credenziali](../automation/automation-credentials.md) con un tipo di **Microsoft.Automation/automationAccounts/credentials** e disporre le proprietà nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| nome utente   | Nome utente per le credenziali. |
| password   | Password per le credenziali. |

Esempio di una risorsa di credenziali è inferiore.

    "name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "userName": "User01",
        "password": "password"
    }


## <a name="schedules"></a>Pianificazioni
[Automazione di Azure pianificazioni](../automation/automation-schedules.md) con un tipo di **Microsoft.Automation/automationAccounts/schedules** e disporre le proprietà nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| Descrizione | Descrizione facoltativa per la programmazione. |
| ora di inizio   | Specifica l'ora di inizio di una pianificazione di un oggetto DateTime. Se può essere convertito in un valore DateTime valido, è possibile specificare una stringa. |
| isEnabled   | Specifica se la programmazione è abilitata. |
| intervallo    | Il tipo di intervallo per la programmazione.<br><br>giorno<br>ora |
| frequenza   | Frequenza con cui la pianificazione dovrebbe essere attivato in numero di giorni o ore. |

Esempio di una risorsa di pianificazione è inferiore.

    "name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
    "type": "microsoft.automation/automationAccounts/schedules",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Schedule for StartByResourceGroup runbook",
        "startTime": "10/30/2016 12:00:00",
        "isEnabled": "true",
        "interval": "day",
        "frequency": "1"
    }


## <a name="variables"></a>Variabili
[Automazione di Azure variabili](../automation/automation-variables.md) con un tipo di **Microsoft.Automation/automationAccounts/variables** e disporre le proprietà nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| Descrizione | Descrizione facoltativa per la variabile. |
| isEncrypted | Specifica se la variabile deve essere crittografata. |
| tipo        | Tipo di dati per la variabile. |
| valore       | Valore per la variabile. |

Esempio di una risorsa variabile è inferiore.

    "name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
    "type": "microsoft.automation/automationAccounts/variables",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Description for the variable.",
        "isEncrypted": "true",
        "type": "string",
        "value": "'This is a string value.'"
    }


## <a name="modules"></a>Moduli
Non è necessario definire [moduli globali](../automation/automation-integration-modules.md) utilizzati dai runbook poiché sarà sempre disponibile la soluzione di gestione.  È necessario includere una risorsa per il modulo utilizzato per il runbook e dal runbook deve dipendere la risorsa di modulo per assicurarsi che venga creato prima dal runbook.

[Moduli di integrazione](../automation/automation-integration-modules.md) con un tipo di **Microsoft.Automation/automationAccounts/modules** e hanno le proprietà nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| contentLink | Specifica il contenuto del modulo. <br><br>URI - Uri del contenuto dal runbook.  Sarà un file ps1 per runbook PowerShell e Script e un file esportato con interfaccia grafica runbook per runbook un grafico.  <br> versione: versione dal runbook per il proprio tenerne traccia. |

Esempio di una risorsa di modulo è inferiore.

    {       
        "name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
        "type": "Microsoft.Automation/automationAccounts/modules",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "properties": {
            "contentLink": {
                "uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
            }
        }
    }

### <a name="updating-modules"></a>Aggiornamento di moduli
Se si aggiorna una soluzione di gestione che include una runbook che utilizza una pianificazione e la nuova versione della soluzione è un nuovo modulo utilizzato da tale runbook, dal runbook può usare la versione precedente del modulo.  Si deve includere runbook seguenti nella soluzione e creare un processo per eseguirli prima di altri runbook.  In questo modo che tutti i moduli vengono aggiornati come obbligatorio prima che vengano caricati i runbook.

- [Aggiornamento ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) assicura che tutti i moduli utilizzati dal runbook della soluzione sono la versione più recente.  
- [Gestione di MS ReRegisterAutomationSchedule](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) verranno registrare tutte le risorse di programmazione per assicurarsi che la runbook collegati con i moduli più recenti.


Di seguito è un campione di elementi necessari di una soluzione per supportare l'aggiornamento del modulo.
    
    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]


## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere una visualizzazione a una soluzione](operations-management-suite-solutions-resources-views.md) per visualizzare i dati raccolti.