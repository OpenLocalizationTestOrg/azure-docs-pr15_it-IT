<properties 
   pageTitle="Controllare il routing e utilizzare dispositivi di rete in Gestione risorse utilizzando un modello | Microsoft Azure"
   description="Informazioni su come controllare la distribuzione e gestione dispositivi di rete in Azure risorse utilizzando un modello"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="jdial" />

#<a name="create-user-defined-routes-udr-in-resource-manager-by-using-a-template"></a>Creare route di definite dall'utente (UDR) in Gestione risorse utilizzando un modello

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione di Manager delle risorse. 

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

## <a name="udr-resources-in-a-template-file"></a>Risorse UDR in un file modello

È possibile visualizzare e scaricare il [modello di esempio](https://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR).

La sezione seguente mostra la definizione di front-end UDR nel file **azuredeploy-vnet-nsg-udr.json** , in base allo scenario sopra.

    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/routeTables",
    "name": "[parameters('frontEndRouteTableName')]",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "UDR - FrontEnd"
    },
    "properties": {
      "routes": [
        {
          "name": "RouteToBackEnd",
          "properties": {
            "addressPrefix": "[parameters('backEndSubnetPrefix')]",
            "nextHopType": "VirtualAppliance",
            "nextHopIpAddress": "[parameters('vmaIpAddress')]"
          }
        }
      ]


Per associare UDR alla subnet front-end, è necessario modificare la definizione di subnet nel modello e usare l'id di riferimento per la UDR.

    "subnets": [
        "name": "[parameters('frontEndSubnetName')]",
        "properties": {
          "addressPrefix": "[parameters('frontEndSubnetPrefix')]",
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('frontEndNSGName'))]"
          },
          "routeTable": {
              "id": "[resourceId('Microsoft.Network/routeTables', parameters('frontEndRouteTableName'))]"
          }
        },
        ...]

Si noti lo stesso di back-end NSG e subnet back-end nel modello.

È anche necessario assicurarsi che la macchina virtuale **FW1** disponga IP proprietà attivata nella scheda di rete che verrà utilizzato per ricevere e inoltrare i pacchetti di inoltro. La sezione seguente mostra la definizione della NIC FW1 nel file udr.json di nsg azuredeploy, in base allo scenario precedente.

    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    "tags": {
      "displayName": "NetworkInterfaces - DMZ"
    },
    "name": "[concat(variables('fwVMSettings').nicName, copyindex(1))]",
    "dependsOn": [
      "[concat('Microsoft.Network/publicIPAddresses/', variables('fwVMSettings').pipName, copyindex(1))]",
      "[concat('Microsoft.Resources/deployments/', 'vnetTemplate')]"
    ],
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "enableIPForwarding": true,
            "privateIPAllocationMethod": "Static",
            "privateIPAddress": "[concat('192.168.0.',copyindex(4))]",
            "publicIPAddress": {
              "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('fwVMSettings').pipName, copyindex(1)))]"
            },
            "subnet": {
              "id": "[variables('dmzSubnetRef')]"
            }
          }
        }
      ]
    },
    "copy": {
      "name": "fwniccount",
      "count": "[parameters('fwCount')]"
    }

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Distribuire il modello ARM tramite fare clic su per la distribuzione

Nell'esempio di modello disponibile nell'archivio pubblico utilizza un file di parametro che contiene il valore predefinito valori utilizzati per generare lo scenario descritto in precedenza. Per distribuire il modello con fare clic su come distribuire, seguire [questo collegamento](https://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR), fare clic su **Distribuisci in Azure**, sostituire i valori di parametro predefiniti, se necessario e seguire le istruzioni nel portale.

## <a name="deploy-the-arm-template-by-using-powershell"></a>Distribuire il modello ARM tramite PowerShell

Per distribuire il modello ARM scaricato tramite PowerShell, seguire la procedura seguente.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Se non è mai utilizzato Azure PowerShell, vedere [come installare e configurare PowerShell Azure](../powershell-install-configure.md) e seguire le istruzioni fino alla fine per eseguire l'accesso a Azure e selezionare l'abbonamento.

2. Eseguire il `New-AzureRmResourceGroup` cmdlet per creare un gruppo di risorse.

        New-AzureRmResourceGroup -Name TestRG -Location westus

3. Eseguire il `New-AzureRmResourceGroupDeployment` cmdlet per distribuire il modello.

        New-AzureRmResourceGroupDeployment -Name DeployUDR -ResourceGroupName TestRG `
            -TemplateUri https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.json `
            -TemplateParameterUri https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.parameters.json            

    Output previsto:

        ResourceGroupName : TestRG
        Location          : westus
        ProvisioningState : Succeeded
        Tags              : 
        Permissions       : 
                            Actions  NotActions
                            =======  ==========
                            *                  
                            
        Resources         : 
                            Name                Type                                     Location
                            ==================  =======================================  ========
                            ASFW                Microsoft.Compute/availabilitySets       westus  
                            ASSQL               Microsoft.Compute/availabilitySets       westus  
                            ASWEB               Microsoft.Compute/availabilitySets       westus  
                            FW1                 Microsoft.Compute/virtualMachines        westus  
                            SQL1                Microsoft.Compute/virtualMachines        westus  
                            SQL2                Microsoft.Compute/virtualMachines        westus  
                            WEB1                Microsoft.Compute/virtualMachines        westus  
                            WEB2                Microsoft.Compute/virtualMachines        westus  
                            NICFW1              Microsoft.Network/networkInterfaces      westus  
                            NICSQL1             Microsoft.Network/networkInterfaces      westus  
                            NICSQL2             Microsoft.Network/networkInterfaces      westus  
                            NICWEB1             Microsoft.Network/networkInterfaces      westus  
                            NICWEB2             Microsoft.Network/networkInterfaces      westus  
                            NSG-BackEnd         Microsoft.Network/networkSecurityGroups  westus  
                            NSG-FrontEnd        Microsoft.Network/networkSecurityGroups  westus  
                            PIPFW1              Microsoft.Network/publicIPAddresses      westus  
                            PIPSQL1             Microsoft.Network/publicIPAddresses      westus  
                            PIPSQL2             Microsoft.Network/publicIPAddresses      westus  
                            PIPWEB1             Microsoft.Network/publicIPAddresses      westus  
                            PIPWEB2             Microsoft.Network/publicIPAddresses      westus  
                            UDR-BackEnd         Microsoft.Network/routeTables            westus  
                            UDR-FrontEnd        Microsoft.Network/routeTables            westus  
                            TestVNet            Microsoft.Network/virtualNetworks        westus  
                            testvnetstorageprm  Microsoft.Storage/storageAccounts        westus  
                            testvnetstoragestd  Microsoft.Storage/storageAccounts        westus  
                            
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>Distribuire il modello ARM tramite CLI Azure

Per distribuire il modello ARM tramite CLI Azure, seguire la procedura seguente.

1. Se non è mai utilizzato CLI Azure, vedere [installare e configurare CLI Azure](../xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si seleziona l'account Azure e la sottoscrizione.
2. Eseguire il `azure config mode` comando per passare alla modalità di gestione risorse, come illustrato di seguito.

        azure config mode arm

    Ecco l'output previsto per il comando precedente:

        info:    New mode is arm

3. Dal browser passare al **https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.parameters.json**, copiare il contenuto del file json e incollare in un nuovo file nel computer. Per questo scenario, si preferisce copiare i valori sotto in un file denominato **c:\udr\azuredeploy.parameters.json**.

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "fwCount": {
              "value": 1
            },
            "webCount": {
              "value": 2
            },
            "sqlCount": {
              "value": 2
            }
          }
        }

4. Eseguire il cmdlet **Crea gruppo azure** per distribuire VNet nuovo utilizzando i file di modello e un parametro scaricato e modificato in precedenza. Elenco visualizzato dopo l'output illustra i parametri utilizzati.

        azure group create -n TestRG -l westus --template-uri 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.json' -e 'c:\udr\azuredeploy.parameters.json'

    Output previsto:

        info:    Executing command group create
        info:    Getting resource group TestRG
        info:    Updating resource group TestRG
        info:    Updated resource group TestRG
        info:    Initializing template configurations and parameters
        info:    Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:    
        info:    group create command OK

5. Eseguire il comando **Mostra gruppo azure** per visualizzare le risorse create nel nuovo gruppo di risorse. 

        azure group show TestRG

    Risultato previsto
        
        info:    Executing command group show
        info:    Listing resource groups
        info:    Listing resources for the group
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:    Resources:
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASFW
        data:      Name    : ASFW
        data:      Type    : availabilitySets
        data:      Location: westus
        data:      Tags    : displayName=AvailabilitySet - DMZ
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASSQL
        data:      Name    : ASSQL
        data:      Type    : availabilitySets
        data:      Location: westus
        data:      Tags    : displayName=AvailabilitySet - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASWEB
        data:      Name    : ASWEB
        data:      Type    : availabilitySets
        data:      Location: westus
        data:      Tags    : displayName=AvailabilitySet - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/FW1
        data:      Name    : FW1
        data:      Type    : virtualMachines
        data:      Location: westus
        data:      Tags    : displayName=VMs - DMZ
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/SQL1
        data:      Name    : SQL1
        data:      Type    : virtualMachines
        data:      Location: westus
        data:      Tags    : displayName=VMs - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/SQL2
        data:      Name    : SQL2
        data:      Type    : virtualMachines
        data:      Location: westus
        data:      Tags    : displayName=VMs - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WEB1
        data:      Name    : WEB1
        data:      Type    : virtualMachines
        data:      Location: westus
        data:      Tags    : displayName=VMs - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WEB2
        data:      Name    : WEB2
        data:      Type    : virtualMachines
        data:      Location: westus
        data:      Tags    : displayName=VMs - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1
        data:      Name    : NICFW1
        data:      Type    : networkInterfaces
        data:      Location: westus
        data:      Tags    : displayName=NetworkInterfaces - DMZ
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL1
        data:      Name    : NICSQL1
        data:      Type    : networkInterfaces
        data:      Location: westus
        data:      Tags    : displayName=NetworkInterfaces - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL2
        data:      Name    : NICSQL2
        data:      Type    : networkInterfaces
        data:      Location: westus
        data:      Tags    : displayName=NetworkInterfaces - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1
        data:      Name    : NICWEB1
        data:      Type    : networkInterfaces
        data:      Location: westus
        data:      Tags    : displayName=NetworkInterfaces - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2
        data:      Name    : NICWEB2
        data:      Type    : networkInterfaces
        data:      Location: westus
        data:      Tags    : displayName=NetworkInterfaces - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd
        data:      Name    : NSG-BackEnd
        data:      Type    : networkSecurityGroups
        data:      Location: westus
        data:      Tags    : displayName=NSG - Front End
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
        data:      Name    : NSG-FrontEnd
        data:      Type    : networkSecurityGroups
        data:      Location: westus
        data:      Tags    : displayName=NSG - Remote Access
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPFW1
        data:      Name    : PIPFW1
        data:      Type    : publicIPAddresses
        data:      Location: westus
        data:      Tags    : displayName=PublicIPAddresses - DMZ
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPSQL1
        data:      Name    : PIPSQL1
        data:      Type    : publicIPAddresses
        data:      Location: westus
        data:      Tags    : displayName=PublicIPAddresses - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPSQL2
        data:      Name    : PIPSQL2
        data:      Type    : publicIPAddresses
        data:      Location: westus
        data:      Tags    : displayName=PublicIPAddresses - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPWEB1
        data:      Name    : PIPWEB1
        data:      Type    : publicIPAddresses
        data:      Location: westus
        data:      Tags    : displayName=PublicIPAddresses - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPWEB2
        data:      Name    : PIPWEB2
        data:      Type    : publicIPAddresses
        data:      Location: westus
        data:      Tags    : displayName=PublicIPAddresses - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd
        data:      Name    : UDR-BackEnd
        data:      Type    : routeTables
        data:      Location: westus
        data:      Tags    : displayName=Route Table - Back End
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd
        data:      Name    : UDR-FrontEnd
        data:      Type    : routeTables
        data:      Location: westus
        data:      Tags    : displayName=UDR - FrontEnd
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:      Name    : TestVNet
        data:      Type    : virtualNetworks
        data:      Location: westus
        data:      Tags    : displayName=VNet
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/testvnetstorageprm
        data:      Name    : testvnetstorageprm
        data:      Type    : storageAccounts
        data:      Location: westus
        data:      Tags    : displayName=Storage Account - Premium
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/testvnetstoragestd
        data:      Name    : testvnetstoragestd
        data:      Type    : storageAccounts
        data:      Location: westus
        data:      Tags    : displayName=Storage Account - Simple
        data:    
        data:    Permissions:
        data:      Actions: *
        data:      NotActions: 
        data:    
        info:    group show command OK

>[AZURE.TIP] Se tutte le risorse non viene visualizzata, eseguire la `azure group deployment show` comando per assicurarsi che lo stato di provisioning della distribuzione è *riuscita*.