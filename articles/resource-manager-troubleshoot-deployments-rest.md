<properties
   pageTitle="Visualizzare le operazioni di distribuzione con API REST | Microsoft Azure"
   description="In questo articolo viene descritto come utilizzare il REST API di Azure Manager delle risorse per rilevare i problemi di distribuzione di Manager delle risorse."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-resource-manager-rest-api"></a>Visualizza le operazioni di distribuzione con REST API di Azure Manager delle risorse

> [AZURE.SELECTOR]
- [Portale](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Se è stato ricevuto un messaggio di errore durante la distribuzione di risorse in Azure, si desidera visualizzare altri dettagli che sono state eseguite le operazioni di distribuzione. API REST fornisce le operazioni che consentono di individuare gli errori e determinare correzioni possibili.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Per evitare errori, è possibile convalidare il modello e infrastruttura prima della distribuzione. È anche possibile accedere ulteriore richiesta e risposta informazioni durante la distribuzione che può essere utile in un secondo momento per la risoluzione dei problemi. Per informazioni su convalida e informazioni di registrazione richiesta e risposta, vedere [distribuire un gruppo di risorse con Gestione risorse di Azure modello](resource-group-template-deploy-rest.md).

## <a name="troubleshoot-with-rest-api"></a>Risolvere i problemi con la API REST

1. Distribuire le risorse con l'operazione di [creazione di una distribuzione di modello](https://msdn.microsoft.com/library/azure/dn790564.aspx) . Per mantenere le informazioni che potrebbero essere utili per il debug, impostare la proprietà **debugSetting** nella richiesta JSON per **requestContent** e/o **responseContent**. 

        PUT https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",      
              },
              "debugSetting": {
                "detailLevel": "requestContent, responseContent"
              }
            }
          }

    Per impostazione predefinita, il valore di **debugSetting** è impostato su **Nessuno**. Quando si specifica il valore di **debugSetting** , valutare attentamente il tipo di informazioni che si sta passando durante la distribuzione. Per la registrazione di informazioni sulla richiesta o risposta è possibile esporre potenzialmente riservate i dati recuperati tramite le operazioni di distribuzione. 

2. È possibile ottenere informazioni sulla distribuzione con l'operazione di [ottenere informazioni su una distribuzione di modello](https://msdn.microsoft.com/library/azure/dn790565.aspx) .

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}

    In risposta, tenere presente gli elementi **provisioningState** , **correlationId** e di **errore** . **CorrelationId** viene utilizzato per tenere traccia degli eventi correlati e può essere utile quando si lavora con supporto tecnico per la risoluzione dei problemi.
    
        { 
          ...
          "properties": {
            "provisioningState":"Failed",
            "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
            ...
            "error":{
              "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
              "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
            }  
          }
        }

3. È possibile ottenere informazioni sulle operazioni di distribuzione con l'operazione di [tutte le operazioni di distribuzione di modelli di elenco](https://msdn.microsoft.com/library/azure/dn790518.aspx) . 

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}

    La risposta conterrà richiesta e risposta informazioni in base a quanto è specificato nella proprietà **debugSetting** durante la distribuzione.
    
        {
          ...
          "properties": 
          {
            ...
            "request":{
              "content":{
                "location":"West US",
                "properties":{
                  "accountType": "Standard_LRS"
                }
              }
            },
            "response":{
              "content":{
                "error":{
                  "message":"Conflict","code":"Conflict"
                }
              }
            }
          }
        }

4. Ottenere gli eventi dai registri di controllo per la distribuzione con l'operazione [Elenca gli eventi di gestione di un abbonamento](https://msdn.microsoft.com/library/azure/dn931934.aspx) .

        GET https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version={api-version}&$filter={filter-expression}&$select={comma-separated-property-names}


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come risolvere gli errori di distribuzione specifico, vedere [risolvere errori comuni durante la distribuzione delle risorse in Azure Gestione risorse Azure](resource-manager-common-deployment-errors.md).
- Per informazioni sull'utilizzo dei log di controllo per monitorare altri tipi di azioni, vedere [controllare le operazioni con Gestione risorse](resource-group-audit.md).
- Per convalidare la distribuzione prima dell'esecuzione, vedere [distribuire un gruppo di risorse con Gestione risorse di Azure modello](resource-group-template-deploy.md).
