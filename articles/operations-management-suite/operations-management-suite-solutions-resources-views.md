<properties
   pageTitle="Visualizzazioni di soluzioni per la gestione di operazioni gestione famiglia di prodotti (OMS) | Microsoft Azure"
   description="Soluzioni di gestione di operazioni di gestione famiglia di prodotti (OMS) conterrà in genere uno o più visualizzazioni per visualizzare i dati.  In questo articolo viene descritto come esportare una visualizzazione creata da Progettazione visualizzazione e includere in una soluzione di gestione. "
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
   ms.date="10/17/2016"
   ms.author="bwren" />

# <a name="views-in-operations-management-suite-oms-management-solutions-preview"></a>Visualizzazioni di soluzioni per la gestione di operazioni gestione famiglia di prodotti (OMS) (versione di anteprima)

>[AZURE.NOTE]Si tratta documentazione preliminare per la creazione di soluzioni di gestione in OMS che sono attualmente in anteprima. Qualsiasi schema descritto di seguito è soggetta a modifiche.    

[Soluzioni di gestione di operazioni di gestione famiglia di prodotti (OMS)](operations-management-suite-solutions.md) conterrà in genere uno o più visualizzazioni per visualizzare i dati.  In questo articolo viene descritto come esportare una visualizzazione creata da [Progettazione](../log-analytics/log-analytics-view-designer.md) e includere in una soluzione di gestione.  

>[AZURE.NOTE]Negli esempi inclusi in questo articolo utilizzano parametri e variabili che sono necessari o comuni per soluzioni per la gestione e descritte soluzioni di [Gestione](operations-management-suite-solutions-creating.md) di creazione di operazioni di gestione famiglia di prodotti (OMS) 


## <a name="prerequisites"></a>Prerequisiti
In questo articolo si presuppone che si ha già familiarità con la modalità per [creare una soluzione di gestione](operations-management-suite-solutions-creating.md) e la struttura di un file di soluzione.


## <a name="overview"></a>Panoramica

Per includere una visualizzazione in una soluzione di gestione, creare una **risorsa** per renderla nel [file di soluzione](operations-management-suite-solutions-creating.md).  JSON che descrive i dettagli della configurazione della visualizzazione è in genere complessa attraverso e non qualcosa che l'autore di una soluzione tipica sarà in grado di creare manualmente.  Il metodo più comune consiste nel creare la visualizzazione con la [Progettazione](../log-analytics/log-analytics-view-designer.md), esportarle e quindi aggiungere la configurazione dettagliata alla soluzione. 

La procedura di base per aggiungere una visualizzazione a una soluzione è i seguenti.  Ogni passaggio descritto in dettaglio nelle sezioni seguenti.

1. Esportare la visualizzazione in un file.
2. Creare la risorsa di visualizzazione della soluzione.
3. Aggiungere i dettagli della visualizzazione.

## <a name="export-the-view-to-a-file"></a>Esportare la visualizzazione in un file
Seguire le istruzioni nella [Progettazione visualizzazione Analitica Log](../log-analytics/log-analytics-view-designer.md) per esportare una visualizzazione in un file.  Il file esportato sarà nel formato JSON con gli stessi [elementi del file di soluzione](operations-management-suite-solutions-creating.md#management-solution-files).  

L'elemento di **risorse** del file di visualizzazione avrà una risorsa a un tipo di **Microsoft.OperationalInsights/workspaces** che rappresenta l'area di lavoro OMS.  Questo elemento avrà un sottoelemento con un tipo di **visualizzazioni** che rappresenta la visualizzazione e contiene la configurazione dettagliata.  Si verrà copiare i dettagli dell'elemento e quindi copiarlo in una soluzione.


## <a name="create-the-view-resource-in-the-solution"></a>Creare la risorsa di visualizzazione della soluzione
Aggiungere la risorsa visualizzazione seguente all'elemento del file di Esplora **risorse** .  Le variabili descritta di seguito, è necessario aggiungere anche vengono utilizzate.  Si noti che le proprietà del **Dashboard** e **OverviewTile** segnaposto che verranno sovrascritti con le proprietà corrispondenti dal file esportato visualizzazione.
 
    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile": 
        }
    }

Aggiungere le seguenti variabili all'elemento [variabili](operations-management-suite-solutions-creating.md#variables) del file di soluzione e sostituire i valori da quelle per la soluzione.

    "LogAnalyticsApiVersion": "2015-11-01-preview",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."


Si noti che è possibile copiare la risorsa intera vista dal file di visualizzazione esportato, ma è necessario apportare le modifiche seguenti per il funzionamento della soluzione.  

- **Tipo** per la risorsa di visualizzazione deve essere modificato da **visualizzazioni** a **Microsoft.OperationalInsights/workspaces**.
- La proprietà **nome** per la risorsa di visualizzazione deve essere modificata per includere il nome dell'area di lavoro.
- Dipendenza nell'area di lavoro deve essere rimosso dal momento che la risorsa dell'area di lavoro non è definita la soluzione.
- Proprietà **DisplayName** deve essere aggiunto alla visualizzazione.  **Id**, **nome**e **DisplayName** deve corrispondere tutte.
- I nomi dei parametri devono essere modificati in modo che corrisponda set obbligatorio di parametri.
- Variabili devono essere definite nella soluzione e utilizzate nelle proprietà appropriata.

## <a name="add-the-view-details"></a>Aggiungere i dettagli di visualizzazione
La risorsa di visualizzazione nel file esportato visualizzazione conterrà due elementi nell'elemento **proprietà** denominate **Dashboard** e **OverviewTile** che includono la configurazione della visualizzazione dettagliata.  Copiare l'elemento **proprietà** della risorsa visualizzazione nel file di soluzione questi due elementi e il relativo contenuto. 

## <a name="example"></a>Esempio
Ad esempio, nell'esempio seguente viene mostrato un file di soluzione semplice con una visualizzazione.  Puntini di sospensione (…) per il contenuto di **Dashboard** e **OverviewTile** per motivi di spazio vengono visualizzate.


    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
        ]
    }




## <a name="next-steps"></a>Passaggi successivi

- Informazioni su tutti i dettagli della creazione di [soluzioni di gestione](operations-management-suite-solutions-creating.md).
- Includere [runbook automazione della soluzione di gestione](operations-management-suite-solutions-resources-automation.md).