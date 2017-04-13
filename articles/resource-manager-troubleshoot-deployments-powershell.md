<properties
   pageTitle="Visualizzare le operazioni di distribuzione con PowerShell | Microsoft Azure"
   description="Descrive come usare PowerShell Azure per rilevare i problemi di distribuzione di Manager delle risorse."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/14/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-powershell"></a>Visualizza le operazioni di distribuzione con PowerShell Azure

> [AZURE.SELECTOR]
- [Portale](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

È possibile visualizzare le operazioni per una distribuzione tramite PowerShell Azure. Si desidera più visualizzare le operazioni quando si riceve un messaggio di errore durante la distribuzione in modo che questo articolo è incentrato sulla visualizzazione delle operazioni che non sono riuscita. PowerShell fornisce i cmdlet che consentono di trovare gli errori e determinare potenziali correzioni facilmente.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Per evitare errori, è possibile convalidare il modello e infrastruttura prima della distribuzione. È anche possibile accedere ulteriore richiesta e risposta informazioni durante la distribuzione che può essere utile in un secondo momento per la risoluzione dei problemi. Per informazioni su convalida e informazioni di registrazione richiesta e risposta, vedere [distribuire un gruppo di risorse con Gestione risorse di Azure modello](resource-group-template-deploy.md).

## <a name="use-deployment-operations-to-troubleshoot"></a>Utilizzare le operazioni di distribuzione per la risoluzione

1. Per ottenere lo stato generale di una distribuzione, utilizzare il comando **Get-AzureRmResourceGroupDeployment** . È possibile filtrare i risultati per solo le distribuzioni che non sono riuscita.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    Che restituisce distribuzioni non riuscite nel formato seguente:
        
        DeploymentName          : Microsoft.Template
        ResourceGroupName       : ExampleGroup
        ProvisioningState       : Failed
        Timestamp               : 6/14/2016 9:55:21 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                    Name                Type                 Value
                    ===============     ===================  ==========
                    storageAccountName  String               tfstorage9855
                    adminUsername       String               tfadmin
                    adminPassword       SecureString
                    dnsNameforLBIP      String               dns
                    vmNamePrefix        String               myVM
                    imagePublisher      String               MicrosoftWindowsServer
                    imageOffer          String               WindowsServer
                    imageSKU            String               2012-R2-Datacenter
                    lbName              String               myLB
                    nicNamePrefix       String               nic
                    publicIPAddressName String               myPublicIP
                    vnetName            String               myVNET
                    vmSize              String               Standard_D1

        Outputs                 :
        DeploymentDebugLogLevel :

2. Ogni distribuzione è in genere costituita da più operazioni, con ogni operazione che rappresenta un passaggio del processo di distribuzione. Per scoprire cosa è verificato un problema con una distribuzione, in genere è necessario visualizzare i dettagli le operazioni di distribuzione. È possibile visualizzare lo stato delle operazioni con **Get-AzureRmResourceGroupDeploymentOperation**.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName Microsoft.Template
        
    Che restituisce più operazioni con ognuna di esse nel formato seguente:
        
        Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
        OperationId    : A3EB2DA598E0A780
        Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                         duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                         serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
        PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                         serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}

3. Per ottenere ulteriori informazioni sulle operazioni non riuscite, recuperare le proprietà per le operazioni con stato **non riuscito** .

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
        
    Che restituisce tutte le operazioni non riuscito con ognuna di esse nel formato seguente:
        
        provisioningOperation : Create
        provisioningState     : Failed
        timestamp             : 2016-06-14T21:54:55.1468068Z
        duration              : PT3.1449887S
        trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
        serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
        statusCode            : BadRequest
        statusMessage         : @{error=}
        targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                                Microsoft.Network/publicIPAddresses/myPublicIP;
                                resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}

    Annotare l'ID di verifica per l'operazione. Utilizzare che nel passaggio successivo per concentrarsi su una determinata operazione.

4. Per ottenere il messaggio di stato di una determinata operazione non riuscito, utilizzare il comando seguente:

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
        
    Che restituisce:
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## <a name="use-audit-logs-to-troubleshoot"></a>I registri di controllo per la risoluzione

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Per visualizzare gli errori per una distribuzione, utilizzare la procedura seguente:

1. Per recuperare le voci di log, eseguire il comando **Get-AzureRmLog** . È possibile utilizzare i parametri **ResourceGroup** e **stato** per restituire solo gli eventi che non è riuscito per un gruppo di risorse singola. Se non si specifica ora di inizio e fine, le voci per l'ultima ora.
Ad esempio, per recuperare le operazioni non riuscite per ultima ora eseguire:

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    È possibile specificare un intervallo di tempo specifico. Nell'esempio successivo esamineremo le azioni non riuscite per l'ultimo giorno. 

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -Status Failed
      
    In alternativa, è possibile impostare un'esatta inizio e ora di fine per azioni non riuscite:

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. Se il comando restituisce troppi proprietà e le voci, è possibile concentrare l'attenzione controllo per il recupero della proprietà di **proprietà** . Verrà incluso anche il parametro **DetailedOutput** per visualizzare i messaggi di errore.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -DetailedOutput).Properties
        
    Che restituisce le proprietà delle voci di log nel formato seguente:
        
        Content
        -------
        {} 
        {[statusCode, BadRequest], [statusMessage, {"error":{"code":"DnsRecordInUse","message":"DNS record dns.westus.clouda...
        {[statusCode, BadRequest], [serviceRequestId, a426f689-5d5a-448d-a2f0-9784d14c900a], [statusMessage, {"error":{"code...

3. Di seguito in base a questi risultati, concentrarsi sul secondo elemento. È possibile perfezionare ulteriormente i risultati esaminando il messaggio di stato per tale voce.

        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput -StartTime (Get-Date).AddDays(-1)).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
        
    Che restituisce:
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}



## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come risolvere gli errori di distribuzione specifico, vedere [risolvere errori comuni durante la distribuzione delle risorse in Azure Gestione risorse Azure](resource-manager-common-deployment-errors.md).
- Per informazioni sull'utilizzo dei log di controllo per monitorare altri tipi di azioni, vedere [controllare le operazioni con Gestione risorse](resource-group-audit.md).
- Per convalidare la distribuzione prima dell'esecuzione, vedere [distribuire un gruppo di risorse con Gestione risorse di Azure modello](resource-group-template-deploy.md).

