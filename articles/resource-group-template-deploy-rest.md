<properties
   pageTitle="Distribuire le risorse con API REST e modello | Microsoft Azure"
   description="Utilizzare Gestione risorse di Azure e REST API Manager delle risorse per distribuire una risorsa in Azure. Le risorse sono definite in un modello di Manager delle risorse."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Distribuire le risorse con i modelli di Manager delle risorse e REST API Manager delle risorse

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI Azure](resource-group-template-deploy-cli.md)
- [Portale](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

In questo articolo viene illustrato come utilizzare il REST API Manager delle risorse con i modelli di Manager delle risorse per distribuire le risorse in Azure.  

> [AZURE.TIP] Per assistenza con il debug di un messaggio di errore durante l'installazione, vedere:
>
> - [Visualizza le operazioni di distribuzione con API REST](resource-manager-troubleshoot-deployments-rest.md) per informazioni su come ottenere informazioni che consente di risolvere l'errore
> - [Risoluzione dei problemi di errori comuni durante la distribuzione delle risorse in Azure Gestione risorse Azure](resource-manager-common-deployment-errors.md) per informazioni su come risolvere gli errori comuni di distribuzione

Il modello può essere un file locale o un file esterno è disponibile tramite un URI. Quando il modello si trova in un account di archiviazione, è possibile limitare l'accesso al modello e fornire un token di firme (SA) accesso condiviso durante la distribuzione.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>Distribuire con l'API REST
1. Impostare [le intestazioni e i parametri comuni](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common), tra cui token di autenticazione.
2. Se non si dispone di un gruppo di risorse esistente, creare un gruppo di risorse. Specificare l'id di abbonamento, il nome del nuovo gruppo di risorse e percorso in cui è necessario per la soluzione. Per ulteriori informazioni, vedere [creare un gruppo di risorse](https://msdn.microsoft.com/library/azure/dn790525.aspx).

        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
   
3. Convalidare la distribuzione prima di eseguire eseguendo l'operazione di [convalida una distribuzione di modello](https://msdn.microsoft.com/library/azure/dn790547.aspx) . Durante la verifica della distribuzione, fornire parametri esattamente come quando si esegue la distribuzione (illustrata nella sezione successiva).

3. Creare una distribuzione. Specificare l'id di abbonamento, il nome del gruppo di risorse per distribuire, il nome della distribuzione e un collegamento al modello. Per informazioni sul file di modello, vedere [file dei parametri](#parameter-file). Per ulteriori informazioni sulle API REST per creare un gruppo di risorse, vedere [creare una distribuzione di modello](https://msdn.microsoft.com/library/azure/dn790564.aspx). Si noti che la **modalità** è impostata su **incrementale**. Per eseguire una distribuzione completa, impostare **la modalità** su **completata**. Prestare attenzione quando si usa la modalità di completamento come è possibile eliminare inavvertitamente risorse che non sono presenti nel modello scelto.
    
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
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
              }
            }
          }
   
      Se si desidera registrare il contenuto di risposta, richiesta contenuto o entrambe, includere **debugSetting** nella richiesta.

        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }

      È possibile impostare un account di archiviazione per utilizzare un token di firme (SA) accesso condiviso. Per ulteriori informazioni, vedere [Delega di accesso con una firma di Access condiviso](https://msdn.microsoft.com/library/ee395415.aspx).

4. È possibile ottenere lo stato della distribuzione modello. Per ulteriori informazioni, vedere [ottenere informazioni su una distribuzione di modello](https://msdn.microsoft.com/library/azure/dn790565.aspx).

          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Passaggi successivi
- Per un esempio di distribuzione delle risorse tramite la raccolta di client .NET, vedere [risorse di distribuzione mediante un modello e raccolte di .NET](virtual-machines/virtual-machines-windows-csharp-template.md).
- Per definire i parametri nel modello, vedere [creazione di modelli](resource-group-authoring-templates.md#parameters).
- Per indicazioni su come distribuire una soluzione in ambienti diversi, vedere [ambienti di sviluppo e test in Microsoft Azure](solution-dev-test-environments.md).
- Per informazioni dettagliate sull'utilizzo di un riferimento KeyVault per passare valori sicuri, vedere [passare protetti valori durante la distribuzione](resource-manager-keyvault-parameter.md).
