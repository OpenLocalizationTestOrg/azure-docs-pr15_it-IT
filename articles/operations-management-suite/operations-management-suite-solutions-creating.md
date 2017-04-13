<properties
   pageTitle="Creazione di soluzioni per la gestione di operazioni di gestione famiglia di prodotti (OMS) | Microsoft Azure"
   description="Soluzioni di gestione estendono la funzionalità di operazioni di gestione famiglia di prodotti (OMS), fornendo scenari di gestione dei pacchetti che i clienti possono aggiungere alla propria area di lavoro OMS.  In questo articolo vengono fornite informazioni dettagliate su come è possibile creare soluzioni di gestione da utilizzare nel proprio ambiente o resi disponibile per i clienti."
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
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="creating-management-solutions-in-operations-management-suite-oms-preview"></a>Creazione di soluzioni per la gestione di operazioni di gestione famiglia di prodotti (OMS) (versione di anteprima)

>[AZURE.NOTE]Si tratta documentazione preliminare per la creazione di soluzioni di gestione in OMS che sono attualmente in anteprima. Qualsiasi schema descritto di seguito è soggetta a modifiche.  

Soluzioni di gestione estendono la funzionalità di operazioni di gestione famiglia di prodotti (OMS), fornendo scenari di gestione dei pacchetti che i clienti possono aggiungere alla propria area di lavoro OMS.  Questo articolo fornisce informazioni dettagliate sulla creazione di soluzioni personalizzate gestione che è possibile utilizzare nel proprio ambiente o rendere disponibili per i clienti tramite alla community.

## <a name="planning-your-management-solution"></a>Pianificazione della soluzione di gestione
Soluzioni di gestione in OMS includano più risorse di supporto di uno scenario di gestione specifico.  Quando si pianifica la soluzione, è necessario concentrarsi sullo scenario che si sta tentando di raggiungere e tutte le risorse necessarie per il supporto.  Ogni soluzione deve essere autonoma e definire ogni risorsa che richiede l'esecuzione, anche se una o più risorse definite anche da altre soluzioni.  Quando si installa una soluzione di gestione, ogni risorsa viene creato a meno che non esiste già ed è possibile definire cosa accade alle risorse quando si rimuove una soluzione.  

Ad esempio, una soluzione di gestione potrebbe contenere un [Azure automazione runbook](../automation/automation-intro.md) che raccoglie i dati all'archivio Log Analitica utilizzando una [pianificazione](../automation/automation-schedules.md) e una [visualizzazione](../log-analytics/log-analytics-view-designer.md) in cui sono disponibili diverse visualizzazioni dei dati raccolti.  La stessa pianificazione può essere utilizzata da un'altra soluzione.  Come autore di soluzione di gestione, definire tutte le tre risorse, ma specificare che la runbook e visualizzazione verranno rimossi automaticamente quando la soluzione viene rimossa.    Verrà inoltre definire una pianificazione ma specificare che deve rimanere in posizione se la soluzione sono state rimosse da altra soluzione nel caso in cui è stato ancora in uso.

## <a name="management-solution-files"></a>File di soluzione di gestione
Soluzioni di gestione sono implementate come [modelli di gestione delle risorse](../resource-manager-template-walkthrough.md).  L'attività principale imparare a creare soluzioni per la gestione di formazione su come [creare un modello](../resource-group-authoring-templates.md).  In questo articolo fornisce dettagli univoci dei modelli utilizzati per le soluzioni e su come definire tipico Esplora risorse.

La struttura di base di un file di soluzione di gestione è diverso da quello di un [Modello di gestione risorse](resource-group-authoring-templates.md#template-format) riportato di seguito.  Ognuna delle sezioni seguenti vengono descritti gli elementi di primo livello ed e il relativo contenuto in una soluzione.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parametri

[Parametri](../resource-group-authoring-templates.md#parameters) sono valori che richiedono da parte dell'utente quando si installa la soluzione di gestione.  Sono disponibili parametri standard contenenti tutte le soluzioni ed è possibile aggiungere altri parametri in base alle esigenze per la soluzione specifica.  Come gli utenti fornire i valori dei parametri quando si installa la soluzione dipenderà il parametro specifico e la modalità di installazione della soluzione.


Quando si installa la soluzione di gestione [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-solutions) e [modelli di Guida introduttiva di Azure](operations-management-suite-solutions.md#finding-and-installing-solutions) viene richiesto di selezionare un [account di automazione e area di lavoro OMS](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account).  Questi vengono utilizzati per popolare i valori di tutti i parametri standard.  L'utente non viene richiesto di fornire direttamente i valori per i parametri standard, ma viene richiesto di fornire i valori per i parametri aggiuntivi.

Quando si installa la soluzione [un altro metodo](operations-management-suite-solutions.md#finding-and-installing-solutions), forniscono un valore per tutti i parametri standard e tutti gli altri parametri.

Di seguito è riportato un parametro di esempio.

    "Daily Start Time": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

Nella tabella seguente vengono descritti gli attributi di un parametro.

| Attributo | Descrizione |
|:--|:--|
| tipo        | Tipo di dati per il parametro. Controllo di input visualizzato per l'utente dipende dal tipo di dati.<br><br>bool - elenco a discesa<br>stringa - Casella di testo<br>int - casella di testo<br>SecureString - campo Password<br> |
| categoria    | Categoria facoltativo per il parametro.  Parametri della stessa categoria sono raggruppati. |
| controllo     | Funzionalità aggiuntive per i parametri di stringa.<br><br>viene visualizzato DateTime - controllo Datetime.<br>GUID - valore Guid viene generato automaticamente e il parametro non è visualizzato. |
| Descrizione | Descrizione facoltativa per il parametro.  Visualizzate in un fumetto informazioni accanto al parametro. |


### <a name="standard-parameters"></a>Parametri standard
La tabella seguente elenca i parametri standard per tutte le soluzioni di gestione.  Questi valori vengono inseriti per l'utente anziché chiedere loro quando è installata la soluzione dall'Azure Marketplace o modelli Guida introduttiva.  L'utente deve specificare i valori desiderati se la soluzione è installata con un altro metodo.

>[AZURE.NOTE]L'interfaccia utente di Azure Marketplace e modelli di Guida introduttiva prevede che i nomi dei parametri descritti nella tabella.  Se si usa i nomi dei parametri diversi quindi all'utente verrà richiesto per tali e non vengono verranno popolati automaticamente.


| Parametro | Tipo | Descrizione |
|:--|:--|:--|
| nome account       | stringa |  Nome dell'account Azure automazione. |
| pricingTier       | stringa | Prezzi livello di area di lavoro Analitica Log e account Azure automazione. |
| regionId          | stringa | Area dell'account Azure automazione. |
| NomeSoluzione      | stringa | Nome della soluzione. |
| workspaceName     | stringa | Registra il nome dell'area di lavoro Analitica. |
| workspaceRegionId | stringa | Area dell'area di lavoro Log Analitica. |





### <a name="sample"></a>Esempio
Di seguito è un'entità di parametro di esempio per una soluzione.  Sono inclusi tutti i parametri standard e due parametri aggiuntivi nella stessa categoria.

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "A valid Azure Automation account name"
            }
        },
        "workspaceRegionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        },
        "jobIdGuid": {
        "type": "string",
            "metadata": {
                "description": "GUID for a runbook job",
                "control": "guid",
                "category": "Schedule"
            }
        },
        "startTime": {
            "type": "string",
            "metadata": {
                "description": "Time for starting the runbook.",
                "control": "datetime",
                "category": "Schedule"
            }
        }


Viene fatto riferimento a valori di parametro in altri elementi della soluzione con la sintassi **parametri ('nome parametro')**.  Ad esempio, per accedere al nome dell'area di lavoro, utilizzare **parameters('workspaceName')** 

## <a name="variables"></a>Variabili

L'elemento **variabili** include i valori che verrà utilizzato il resto della soluzione di gestione.  Questi valori non vengono esposti all'utente installando la soluzione.  Servono fornire all'autore con un'unica posizione in cui è possibile gestire i valori che possono essere utilizzati più volte in tutta la soluzione. È necessario inserire i valori alla soluzione nel variabili anziché hardcoded loro nell'elemento **risorse** .  Questo rende più leggibile il codice ed è possibile modificare facilmente questi valori nelle versioni successive.

Di seguito è illustrato un esempio di un elemento di **variabili** con parametri tipici nelle soluzioni.

    "variables": { 
        "SolutionVersion": "1.1", 
        "SolutionPublisher": "Contoso", 
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Viene fatto riferimento a valori delle variabili tramite la soluzione con la sintassi delle **variabili ('' nome variabile)**.  Ad esempio, per accedere alla variabile NomeSoluzione, utilizzare **variables('solutionName')** 


## <a name="resources"></a>Risorse

L'elemento **risorse** definisce diverse risorse incluse in soluzione di gestione.  Questo sarà la parte più grande e più complessa del modello.  Le risorse sono definite con la struttura seguente.  

    "resources": [
        {
            "name": "<name-of-the-resource>",           
            "apiVersion": "<api-version-of-resource>",
            "type": "<resource-provider-namespace/resource-type-name>",     
            "location": "<location-of-resource>",
            "tags": "<name-value-pairs-for-resource-tagging>",
            "comments": "<your-reference-notes>",
            "dependsOn": [
                "<array-of-related-resource-names>"
            ],
            "properties": "<unique-settings-for-the-resource>",
            "resources": [
                "<array-of-child-resources>"
            ]
        }
    ]

### <a name="dependencies"></a>Dipendenze
Gli elementi **dependsOn** specifica una [relazione](../resource-group-define-dependencies.md) in un'altra risorsa.  Quando si installa la soluzione, non viene creata una risorsa fino a tutte le relative dipendenze sono state create.  Ad esempio, la soluzione può [avviare un runbook](operations-management-suite-solutions-resources-automation.md#runbooks) quando viene installato tramite una [risorsa di processo](operations-management-suite-solutions-resources-automation.md#automation-jobs).  La risorsa processo sarebbe dipenda la risorsa runbook per assicurarsi che venga creato dal runbook prima il processo è stato creato.

### <a name="oms-workspace-and-automation-account"></a>Area di lavoro OMS e account di automazione
Soluzioni di gestione richiedono un' [area di lavoro OMS](../log-analytics/log-analytics-manage-access.md) per contenere le visualizzazioni e un [account di automazione](../automation/automation-security-overview.md#automation-account-overview) per contenere runbook e le relative risorse.  Devono essere disponibili prima che le risorse della soluzione vengono create e non devono essere definite in soluzione stessa.  L'utente verrà [specificare un'area di lavoro e account](operations-management-suite-solutions.md#oms-workspace-and-automation-account) quando si distribuisce la soluzione, ma l'autore è necessario considerare i seguenti punti.


## <a name="solution-resource"></a>Risorse della soluzione
Ogni soluzione richiede una voce di risorsa nell'elemento **risorse** che definisce la soluzione stessa.  Questo avrà un tipo di **Microsoft.OperationsManagement/solutions**.  Di seguito è illustrato un esempio di una risorsa soluzione.  Nelle sezioni seguenti sono descritti i relativi elementi diversi.

    "name": "[concat(variables('SolutionName'), '[ ' ,parameters('workspacename'), ' ]')]",
    "location": "[parameters('workspaceRegionId')]",
    "tags": { },
    "type": "Microsoft.OperationsManagement/solutions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]",
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
    ]
    "properties": {
        "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
        "referencedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]"
        ],
        "containedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
        ]
    },
    "plan": {
        "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
        "Version": "[variables('SolutionVersion')]",
        "product": "AzureSQLAnalyticSolution",
        "publisher": "[variables('SolutionPublisher')]",
        "promotionCode": ""
    }

### <a name="solution-name"></a>Nome soluzione
Il nome della soluzione deve essere univoco nell'abbonamento Azure. Il valore consigliato da utilizzare è la seguente:  Utilizza una variabile denominata **NomeSoluzione** per il nome di base e il parametro **workspaceName** per assicurarsi che il nome sia univoco.

    [concat(variables('SolutionName'), ' [' ,parameters('workspaceName'), ']')]

Questo viene risolto in un nome simile al seguente.

    My Solution Name [MyWorkspace]
 

### <a name="dependencies"></a>Dipendenze
La risorsa soluzione deve avere una [dipendenza](../resource-group-define-dependencies.md) in tutte le altre risorse della soluzione poiché devono esistere prima di poter creare la soluzione.  Tale scopo è necessario aggiungere una voce per ogni risorsa nell'elemento **dependsOn** .


### <a name="properties"></a>Proprietà
La risorsa soluzione ha le proprietà nella tabella seguente.  Sono incluse le risorse a cui fa riferimento e contenuto dalla soluzione che definisce la modalità di gestione della risorsa dopo avere installata la soluzione.  Ogni risorsa della soluzione deve essere elencato nel **referencedResources** o la proprietà **containedResources** .

| Proprietà | Descrizione |
|:--|:--|
| workspaceResourceId | ID dell'area di lavoro OMS nel modulo * <Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<nome dell'area di lavoro\>*. |
| referencedResources   | Elenco delle risorse nella soluzione che non devono essere rimossi quando la soluzione viene rimossa. |
| containedResources   | Elenco delle risorse nella soluzione che deve essere rimosso quando la soluzione viene rimossa. |

Nell'esempio precedente è per una soluzione con un runbook, programmazione e visualizzazione.  La pianificazione e runbook sono *cui viene fatto riferimento* nell'elemento **proprietà** in modo che non vengono rimosse quando la soluzione viene rimossa.  La visualizzazione è *contenuto* in modo che è stata rimossa quando la soluzione viene rimossa.


### <a name="plan"></a>Piano
L'entità **piano** della risorsa soluzione include le proprietà nella tabella seguente. 

| Proprietà | Descrizione |
|:--|:--|
| nome          | Nome della soluzione. |
| Versione       | Versione della soluzione in base all'autore. |
| prodotto       | Stringa univoca per identificare la soluzione. |
| Publisher     | Autore della soluzione. |


## <a name="other-resources"></a>Altre risorse
È possibile ottenere i dettagli ed esempi di risorse comuni a soluzioni di gestione nei seguenti articoli.

- [Visualizzazioni e dashboard](operations-management-suite-solutions-resources-views.md)
- [Risorse di automazione](operations-management-suite-solutions-resources-automation.md)

## <a name="testing-a-management-solution"></a>Una soluzione di gestione di test
Prima di distribuire la soluzione di gestione, è consigliabile verificare tramite [Test AzureRmResourceGroupDeployment](../resource-group-template-deploy.md#deploy-with-powershell).  Questa operazione verrà convalidato il file di soluzione e consentono di che identificare gli eventuali problemi prima di distribuirlo.



## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate dei [modelli di creazione condivisa Manager delle risorse di Azure](../resource-group-authoring-templates.md).
- Ricerca di [Modelli di Guida introduttiva di Azure](https://azure.microsoft.com/documentation/templates) per esempi dei diversi modelli di Manager delle risorse.
- Visualizzare i dettagli per [l'aggiunta di visualizzazioni per una soluzione di gestione](operations-management-suite-solutions-resources-views.md).
- Visualizzare i dettagli per [l'aggiunta di risorse di automazione per una soluzione di gestione](operations-management-suite-solutions-resources-automation.md).
 