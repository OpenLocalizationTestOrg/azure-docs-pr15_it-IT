<properties
   pageTitle="Distribuire una macchina virtuale con un indirizzo IP pubblico statico utilizzando un modello in Gestione risorse | Microsoft Azure"
   description="Informazioni su come distribuire macchine virtuali con un indirizzo IP pubblico statico utilizzando un modello in Gestione risorse"
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
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-a-template"></a>Distribuire una macchina virtuale con un indirizzo IP pubblico statico utilizzando un modello

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modello di distribuzione classica.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="public-ip-resources-in-a-template-file"></a>Risorse IP pubbliche in un file modello

È possibile visualizzare e scaricare il [modello di esempio](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json).

La sezione seguente è illustrata la definizione della risorsa IP pubblica, in base allo scenario precedente.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('webVMSetting').pipName]",
        "location": "[variables('location')]",
        "properties": {
          "publicIPAllocationMethod": "Static"
        },
        "tags": {
          "displayName": "PublicIPAddress - Web"
        }
      },

Si noti la proprietà **publicIPAllocationMethod** , che è impostata su *statico*. Questa proprietà può essere *dinamico* (valore predefinito) o *statico*. Verrà impostata su garanzie statiche che non viene mai modificato l'indirizzo IP pubblico assegnato.

La sezione riportata di seguito mostra l'associazione dell'indirizzo IP pubblico con un'interfaccia di rete.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[variables('webVMSetting').nicName]",
        "location": "[variables('location')]",
        "tags": {
          "displayName": "NetworkInterface - Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
          "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
          "ipConfigurations": [
            {
              "name": "ipconfig1",
              "properties": {
                "privateIPAllocationMethod": "Static",
                "privateIPAddress": "[variables('webVMSetting').ipAddress]",
                "publicIPAddress": {
                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
                },
                "subnet": {
                  "id": "[variables('frontEndSubnetRef')]"
                }
              }
            }
          ]
        }
      },

Si noti la proprietà **publicIPAddress** che puntano **Id** di una risorsa denominata **variables('webVMSetting').pipName**. Che è il nome della risorsa IP pubblico illustrato sopra.

Infine, interfaccia rete viene elencato nella proprietà **networkProfile** di macchine Virtuali da creare.

      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Distribuire il modello utilizzando fare clic su per la distribuzione

Nell'esempio di modello disponibile nell'archivio pubblico utilizza un file di parametro che contiene il valore predefinito valori utilizzati per generare lo scenario descritto in precedenza. Per distribuire questo modello di utilizzo fare clic su per la distribuzione, fare clic su **Distribuisci in Azure** nel file Readme.md per il modello di [macchina virtuale con PIP statico](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP) . Sostituire i valori di parametro predefiniti se lo si desidera e immettere valori per i parametri vuoti.  Seguire le istruzioni nel portale per creare una macchina virtuale con un indirizzo IP pubblico.

## <a name="deploy-the-template-by-using-powershell"></a>Distribuire il modello tramite PowerShell

Per distribuire il modello scaricato tramite PowerShell, seguire la procedura seguente.

1. Se non è mai utilizzato Azure PowerShell, vedere [come installare e configurare PowerShell Azure](../powershell-install-configure.md) e seguire le istruzioni nei passaggi da 1 a 3.

2. In una console di PowerShell, eseguire il cmdlet **New-AzureRmResourceGroup** per creare un nuovo gruppo di risorse, se necessario. Se si dispone già di un gruppo di risorse creato, andare al passaggio 3.

        New-AzureRmResourceGroup -Name PIPTEST -Location westus

    Output previsto:

        ResourceGroupName : PIPTEST
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. In una console di PowerShell, eseguire il cmdlet **New-AzureRmResourceGroupDeployment** per distribuire il modello.

        New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
            -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
            -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json

    Output previsto:

        DeploymentName    : DeployVM
        ResourceGroupName : PIPTEST
        ProvisioningState : Succeeded
        Timestamp         : <Deployment date> <Deployment time>
        Mode              : Incremental
        TemplateLink      :
                            Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
                            ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
                            ContentVersion : 1.0.0.0

        Parameters        :
                            Name                      Type                       Value     
                            ========================  =========================  ==========
                            vnetName                  String                     WTestVNet
                            vnetPrefix                String                     192.168.0.0/16
                            frontEndSubnetName        String                     FrontEnd  
                            frontEndSubnetPrefix      String                     192.168.1.0/24
                            storageAccountNamePrefix  String                     iaasestd  
                            stdStorageType            String                     Standard_LRS
                            osType                    String                     Windows   
                            adminUsername             String                     adminUser
                            adminPassword             SecureString                         

        Outputs           :

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Distribuire il modello tramite CLI Azure

Per distribuire il modello tramite CLI Azure, seguire la procedura seguente.

1. Se non è mai utilizzato CLI Azure, seguire la procedura descritta in articolo [installare e configurare CLI Azure](../xplat-cli-install.md) e quindi la procedura per la connessione CLI all'abbonamento nella sezione "Utilizzare accesso azure per eseguire l'autenticazione in modo interattivo" dell'articolo [connettersi a un abbonamento Azure dall'interfaccia della riga di comando Azure Azure CLI ()](../xplat-cli-connect.md) .
2. Eseguire il comando **configurazione azure modalità** per passare alla modalità di gestione risorse, come illustrato di seguito.

        azure config mode arm

    Ecco l'output previsto per il comando precedente:

        info:    New mode is arm

3. Aprire il [file di parametro](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json), selezionare il relativo contenuto e salvarlo in un file nel computer. In questo esempio, i parametri vengono salvati in un file denominato *parameters.json*. Modificare i valori dei parametri all'interno del file se lo si desidera, ma almeno, è consigliabile modificare il valore per il parametro adminPassword in una password complessa e univoca.

4. Eseguire il cmdlet **distribuzione del gruppo di azure creare** per distribuire VNet nuovo utilizzando i file di modello e un parametro scaricato e modificato in precedenza. Il comando seguente, sostituire <path> con il percorso è stato salvato il file a. 

        azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e <path>\parameters.json

    Output previsto (liste di valori di parametro utilizzati):

        info:    Executing command group create
        + Getting resource group PIPTEST2
        + Creating resource group PIPTEST2
        info:    Created resource group PIPTEST2
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/<Subscription ID>/resourceGroups/PIPTEST2
        data:    Name:                PIPTEST2
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
