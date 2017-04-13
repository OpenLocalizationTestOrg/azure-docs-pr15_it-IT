<properties
   pageTitle="Visualizzare le operazioni di distribuzione con Azure CLI | Microsoft Azure"
   description="In questo articolo viene descritto come utilizzare CLI Azure per rilevare i problemi di distribuzione di Manager delle risorse."
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
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-cli"></a>Visualizza le operazioni di distribuzione con CLI Azure

> [AZURE.SELECTOR]
- [Portale](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Se è stato ricevuto un messaggio di errore durante la distribuzione di risorse in Azure, si desidera visualizzare altri dettagli che sono state eseguite le operazioni di distribuzione. Azure CLI sono disponibili comandi che consentono di individuare gli errori e determinare le correzioni possibili.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Per evitare errori, è possibile convalidare il modello e infrastruttura prima della distribuzione. È anche possibile accedere ulteriore richiesta e risposta informazioni durante la distribuzione che può essere utile in un secondo momento per la risoluzione dei problemi. Per informazioni su convalida e informazioni di registrazione richiesta e risposta, vedere [distribuire un gruppo di risorse con Gestione risorse di Azure modello](resource-group-template-deploy-cli.md).

## <a name="use-audit-logs-to-troubleshoot"></a>I registri di controllo per la risoluzione

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Per visualizzare gli errori per una distribuzione, utilizzare la procedura seguente:

1. Per visualizzare il log di controllo, eseguire il comando **log di azure gruppo Visualizza** . È possibile includere il **-distribuzione ultimo** opzione per recuperare solo il registro per la distribuzione più recente.

        azure group log show ExampleGroup --last-deployment

2. Il comando **Mostra registro del gruppo azure** restituisce moltissime informazioni. Per la risoluzione dei problemi, in genere si desidera focalizzare l'attenzione sulle operazioni che non è riuscita. Il seguente script utilizzata l'opzione **- json** e l'utilità JSON [jq](https://stedolan.github.io/jq/) per cercare il log degli errori di distribuzione.

        azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'
        
        {
        "claims": {
          "aud": "https://management.core.windows.net/",
          "iss": "https://sts.windows.net/<guid>/",
          "iat": "1442510510",
          "nbf": "1442510510",
          "exp": "1442514410",
          "ver": "1.0",
          "http://schemas.microsoft.com/identity/claims/tenantid": "{guid}",
          "http://schemas.microsoft.com/identity/claims/objectidentifier": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
          "puid": "XXXXXXXXXXXXXXXX",
          "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",
          "altsecid": "1:example.com:XXXXXXXXXXX",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string="">",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
          "name": "Tom FitzMacken",
          "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
          "groups": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
          "wids": "{guid}",
          "appid": "{guid}",
          "appidacr": "0",
          "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
          "http://schemas.microsoft.com/claims/authnclassreference": "1",
          "ipaddr": "000.000.000.000"
        },
        "properties": {
          "statusCode": "Conflict",
          "statusMessage": "{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"Target\":null,\"Details\":[{\"Message\":\"Website with given name
            mysite already exists.\"},{\"Code\":\"Conflict\"},{\"ErrorEntity\":{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"ExtendedCode\":
            \"54001\",\"MessageTemplate\":\"Website with given name {0} already exists.\",\"Parameters\":[\"mysite\"],\"InnerErrors\":null}}],\"Innererror\":null}"
        },
        ...

    È possibile visualizzare **le proprietà** sono disponibili informazioni json sull'operazione non riuscito.

    È possibile utilizzare il **-dettagliato** e **- vv** opzioni per visualizzare altre informazioni dai log.  Utilizzare la **-dettagliato** opzione per visualizzare la procedura le operazioni elaborata in `stdout`. Per la cronologia di una richiesta completa, utilizzare l'opzione **- vv** . I messaggi spesso aiutare fondamentale sulla causa di eventuali errori.

3. Per evidenziare nel messaggio di stato per le voci non riuscite, utilizzare il comando seguente:

        azure group log show ExampleGroup --json | jq -r ".[] | select(.status.value == \"Failed\") | .properties.statusMessage"


## <a name="use-deployment-operations-to-troubleshoot"></a>Utilizzare le operazioni di distribuzione per la risoluzione

1. È possibile ottenere lo stato generale di una distribuzione con il comando **distribuzione di azure gruppo Mostra** . Nell'esempio seguente la distribuzione non è riuscita.

        azure group deployment show --resource-group ExampleGroup --name ExampleDeployment
        
        info:    Executing command group deployment show
        + Getting deployments
        data:    DeploymentName     : ExampleDeployment
        data:    ResourceGroupName  : ExampleGroup
        data:    ProvisioningState  : Failed
        data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
        data:    Mode               : Incremental
        data:    Name             Type    Value
        data:    ---------------  ------  ------------
        data:    siteName         String  ExampleSite
        data:    hostingPlanName  String  ExamplePlan
        data:    siteLocation     String  West US
        data:    sku              String  Free
        data:    workerSize       String  0
        info:    group deployment show command OK

2. Per visualizzare il messaggio per operazioni non riuscite per una distribuzione, usare:

        azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json  | jq ".[] | select(.properties.provisioningState == \"Failed\") | .properties.statusMessage.Message"


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come risolvere gli errori di distribuzione specifico, vedere [risolvere errori comuni durante la distribuzione delle risorse in Azure Gestione risorse Azure](resource-manager-common-deployment-errors.md).
- Per informazioni sull'utilizzo dei log di controllo per monitorare altri tipi di azioni, vedere [controllare le operazioni con Gestione risorse](resource-group-audit.md).
- Per convalidare la distribuzione prima dell'esecuzione, vedere [distribuire un gruppo di risorse con Gestione risorse di Azure modello](resource-group-template-deploy.md).
