<properties
   pageTitle="Distribuirle con più NIC utilizzando un modello in Gestione risorse | Microsoft Azure"
   description="Informazioni su come distribuirle con più NIC utilizzando un modello in Gestione risorse"
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
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="deploy-multi-nic-vms-using-a-template"></a>Distribuirle con più NIC utilizzando un modello

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modello di distribuzione classica](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Poiché in questo momento non possono essere macchine virtuali con una singola scheda e macchine virtuali con più schede di rete nello stesso gruppo di risorse, il server di back-end verrà implementata in un gruppo di risorse e tutti gli altri componenti di un altro gruppo di sicurezza. La procedura seguente usa un gruppo di risorse denominato *IaaSStory* per il gruppo di risorse principale e *Back-end IaaSStory* per il server di back-end.

## <a name="prerequisites"></a>Prerequisiti

Prima di distribuire il server di back-end, è necessario distribuire il gruppo di risorse principale con tutte le risorse necessarie per questo scenario. Per distribuire queste risorse, seguire la procedura seguente.

1. Passare alla [pagina il modello](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Nella pagina modello, a destra del **gruppo di risorse padre**, fare clic su **Distribuisci in Azure**.
3. Se necessario, modificare i valori dei parametri, quindi seguire i passaggi nel portale di Azure anteprima per distribuire il gruppo di risorse.

> [AZURE.IMPORTANT] Assicurarsi che i nomi degli account di archiviazione siano univoci. È possibile assegnare i nomi degli account di archiviazione duplicati in Azure.

## <a name="understand-the-deployment-template"></a>Comprendere il modello di distribuzione

Prima di distribuire il modello fornito con questa documentazione, assicurarsi di comprendere le potenzialità. La procedura indicata di seguito offrono un'ampia panoramica dei modelli in questione.

1. Passare alla [pagina il modello](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Fare clic su **azuredeploy.json** per aprire il file del modello.
3. Si noti il parametro *osType* elencato di seguito. Questo parametro viene utilizzato per selezionare quali macchine Virtuali immagine da utilizzare per il server di database, insieme a più del sistema operativo le relative impostazioni.

        "osType": {
          "type": "string",
          "defaultValue": "Windows",
          "allowedValues": [
            "Windows",
            "Ubuntu"
          ],
          "metadata": {
            "description": "Type of OS to use for VMs: Windows or Ubuntu."
          }
        },

4. Scorrere l'elenco di variabili e controllare la definizione per le variabili **dbVMSetting** elencate di seguito. Uno degli elementi della matrice contenuti nella variabile **dbVMSettings** riceve. Se si ha familiarità con la terminologia di sviluppo software, è possibile visualizzare la variabile **dbVMSettings** come hashtable o un dictionay.

        "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"

5. Si supponga che si desidera distribuire macchine virtuali di Windows in esecuzione SQL back-end. Il valore per **osType** da *Windows*e la variabile **dbVMSetting** contiene l'elemento nell'elenco seguente, che rappresenta il primo valore nella variabile **dbVMSettings** .

          "Windows": {
            "vmSize": "Standard_DS3",
            "publisher": "MicrosoftSQLServer",
            "offer": "SQL2014SP1-WS2012R2",
            "sku": "Standard",
            "version": "latest",
            "vmName": "DB",
            "osdisk": "osdiskdb",
            "datadisk": "datadiskdb",
            "nicName": "NICDB",
            "ipAddress": "192.168.2.",
            "extensionDeployment": "",
            "avsetName": "ASDB",
            "remotePort": 3389,
            "dbPort": 1433
          },

6. Si noti **vmSize** contiene il valore *Standard_DS3*. Solo determinate dimensioni macchine Virtuali non consentono l'uso di più schede di rete. È possibile verificare quali dimensioni macchine Virtuali sono più NIC abilitati visitando la [Panoramica NIC multipla](virtual-networks-multiple-nics.md).
7. Scorrere fino a **risorse** e notare il primo elemento. Descrive un account di archiviazione. Questo account di archiviazione verrà utilizzato per la gestione dischi di dati utilizzati per ogni database di macchine Virtuali. In questo scenario, ogni macchine Virtuali di database viene utilizzato un disco OS archiviati in archiviazione normale e due dischi dati archiviati in archiviazione SSD (premium).

        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('prmStorageName')]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "Storage Account - Premium"
          },
          "properties": {
            "accountType": "[parameters('prmStorageType')]"
          }
        },

8. Scorrere fino alla risorsa successiva, come indicato di seguito. Questa risorsa rappresenta NIC usato per l'accesso ai database in ogni database di macchine Virtuali. Si noti l'utilizzo della funzione **Copia** in questa risorsa. Il modello consente di distribuire macchine virtuali di un numero che si desidera, in base al parametro **dbCount** . Pertanto è necessario creare la stessa quantità di schede di rete per l'accesso al database, uno per ogni macchina virtuale.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "NetworkInterfaces - DB DA"
          },
          "copy": {
            "name": "dbniccount",
            "count": "[parameters('dbCount')]"
          },
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Static",
                  "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
                  "subnet": {
                    "id": "[variables('backEndSubnetRef')]"
                  }
                }
              }
            ]
          }
        },

9. Scorrere fino alla risorsa successiva, come indicato di seguito. Questa risorsa rappresenta NIC utilizzato per la gestione in ogni database di macchine Virtuali. Ancora una volta, è necessario uno di questi NIC per ogni database di macchine Virtuali. Si noti l'elemento **networkSecurityGroup** , il collegamento di un NSG che consente l'accesso a RDP/SSH a solo questo NIC.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "NetworkInterfaces - DB RA"
          },
          "copy": {
            "name": "dbniccount",
            "count": "[parameters('dbCount')]"
          },
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "networkSecurityGroup": {
                    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
                  },
                  "privateIPAllocationMethod": "Static",
                  "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
                  "subnet": {
                    "id": "[variables('backEndSubnetRef')]"
                  }
                }
              }
            ]
          }
        },

10. Scorrere fino alla risorsa successiva, come indicato di seguito. Questa risorsa rappresenta una disponibilità imposta deve essere condiviso da tutte le macchine virtuali di database. In questo modo si garantisce che è sempre possibile una macchina virtuale nel set di esecuzione durante la manutenzione.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/availabilitySets",
          "name": "[variables('dbVMSetting').avsetName]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "AvailabilitySet - DB"
          }
        },

11. Scorrere fino alla risorsa successiva. Questa risorsa rappresenta il database macchine virtuali, come illustrato nel primo alcune righe elencate di seguito. Si noti l'utilizzo della funzione **Copia** nuovamente assicurare che vengano create in base al parametro **dbCount** più macchine virtuali. Si noti inoltre insieme **dependsOn** . Vengono elencati due schede di rete sia necessario creare prima la macchina virtuale viene distribuita, oltre a rendere il set di disponibilità e l'account di archiviazione.

          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
          "location": "[variables('location')]",
          "dependsOn": [
            "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
            "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
            "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
            "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
          ],
          "tags": {
            "displayName": "VMs - DB"
          },
          "copy": {
            "name": "dbvmcount",
            "count": "[parameters('dbCount')]"
          },

12. Scorrere verso il basso la risorsa macchina virtuale all'elemento **networkProfile** , come indicato di seguito. Si noti che sono presenti due schede di rete cui si fa riferimento per ogni macchina virtuale. Quando si creano più schede di rete per una macchina virtuale, impostare la proprietà **primaria** di una delle schede su *true*e il resto su *false*.

        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
              "properties": { "primary": true }
            },
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
              "properties": { "primary": false }
            }
          ]
        }
      }

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Distribuire il modello ARM tramite fare clic su per la distribuzione

> [AZURE.IMPORTANT] Assicurarsi che la procedura [Prerequisiti](#Pre-requisites) prima di seguire le istruzioni riportate di seguito.

Nell'esempio di modello disponibile nell'archivio pubblico utilizza un file di parametro che contiene il valore predefinito valori utilizzati per generare lo scenario descritto in precedenza. Per distribuire il modello con fare clic su come distribuire, seguire [questo collegamento](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC), a destra del **gruppo di risorse di back-end (vedere la documentazione)** fare clic su **Distribuisci in Azure**, sostituire i valori di parametro predefiniti, se necessario e seguire le istruzioni nel portale.

La figura seguente mostra il contenuto del nuovo gruppo di risorse dopo la distribuzione.

![Gruppo di risorse back-end](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>Distribuire il modello tramite PowerShell

Per distribuire il modello scaricato tramite PowerShell, seguire la procedura seguente.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. Eseguire il **`New-AzureRmResourceGroup`** cmdlet per creare un gruppo di risorse utilizzando il modello.

        New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
            -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'

    Output previsto:

        ResourceGroupName : IaaSStory-Backend
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  

        Resources         :
                            Name                 Type                                 Location
                            ===================  ===================================  ========
                            ASDB                 Microsoft.Compute/availabilitySets   westus  
                            DB1                  Microsoft.Compute/virtualMachines    westus  
                            DB2                  Microsoft.Compute/virtualMachines    westus  
                            NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                            NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                            NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                            NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                            wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  

        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Distribuire il modello tramite CLI Azure

Per distribuire il modello tramite CLI Azure, seguire la procedura seguente.

1. Se non è mai utilizzato CLI Azure, vedere [installare e configurare CLI Azure](../xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si seleziona l'account Azure e la sottoscrizione.
2. Eseguire il **`azure config mode`** comando per passare alla modalità di gestione risorse, come illustrato di seguito.

        azure config mode arm

    Ecco l'output previsto per il comando precedente:

        info:    New mode is arm

3. Aprire il [file di parametro](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), selezionare il contenuto e salvarlo in un file nel computer. In questo esempio è stato salvato il file di parametri per *parameters.json*.

4. Eseguire il **`azure group deployment create`** cmdlet per distribuire VNet nuovo utilizzando il modello e il parametro file scaricato e modificato in precedenza. Elenco visualizzato dopo l'output illustra i parametri utilizzati.

        azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json

    Output previsto:

        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
