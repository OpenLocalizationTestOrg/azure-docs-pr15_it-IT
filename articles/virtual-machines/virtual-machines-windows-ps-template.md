<properties
    pageTitle="Creare una macchina virtuale con un modello di gestione risorse | Microsoft Azure"
    description="Utilizzare un modello di Manager delle risorse e PowerShell per creare una nuova macchina virtuale di Windows."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>Creare una macchina virtuale di Windows con un modello di Manager delle risorse

In questo articolo viene presentato un modello di gestione di risorse Azure e illustra come usare PowerShell per la distribuzione. Il modello distribuisce una singola macchina virtuale che eseguono Windows Server in una nuova rete virtuale con una singola subnet.

Richiede circa 20 minuti per eseguire la procedura descritta in questo articolo.

> [AZURE.IMPORTANT] Se si desidera la macchina virtuale per far parte di un set di disponibilità, aggiungerla al set di quando si crea la macchina virtuale. Attualmente non è presente un modo per aggiungere una macchina virtuale per una disponibilità imposta dopo averlo creato.

## <a name="step-1-create-the-template-file"></a>Passaggio 1: Creare il file del modello

È possibile creare un modello personalizzato utilizzando le informazioni contenute nei [modelli di creazione condivisa Manager delle risorse di Azure](../resource-group-authoring-templates.md). È inoltre possibile distribuire modelli creati automaticamente da [Azure Guide rapide modelli](https://azure.microsoft.com/documentation/templates/).

1. Aprire un editor di testo e aggiungere l'elemento dello schema obbligatori e l'elemento contentVersion necessari:

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
        }
2. [Parametri](../resource-group-authoring-templates.md#parameters) non sono sempre necessari, ma consentono di valori di input quando si distribuisce il modello. Aggiungere l'elemento parametri e gli elementi figlio dopo l'elemento contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

3. [Variabili](../resource-group-authoring-templates.md#variables) possono essere utilizzate in un modello per specificare i valori possono cambiare frequentemente o valori che devono essere creati da una combinazione di valori di parametro. Aggiungere l'elemento variabili dopo la sezione parametri:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }
        
4. [Risorse](../resource-group-authoring-templates.md#resources) , ad esempio la macchina virtuale, la rete virtuale con l'account di archiviazione sono definiti successivo nel modello. Aggiungere la sezione di risorse dopo la sezione variabili:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvn1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
          
    >[AZURE.NOTE] In questo articolo viene creata una macchina virtuale in esecuzione una versione del sistema operativo Windows Server. Per ulteriori informazioni sulla selezione altre immagini, vedere [Naviga e selezionare macchina virtuale Azure immagini con Windows PowerShell e CLI Azure](virtual-machines-linux-cli-ps-findimage.md).  
            
2. Salvare il file del modello come *VirtualMachineTemplate.json*.

## <a name="step-2-create-the-parameters-file"></a>Passaggio 2: Creare il file di parametri

Per specificare i valori per i parametri di risorse che sono stati definiti nel modello, creare un file di parametri che contiene i valori vengono utilizzati quando si distribuisce il modello.

1. Nell'editor di testo, copiare il contenuto JSON in un nuovo file denominato *Parameters.json*:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] Vedere informazioni sui [requisiti di nome utente e password](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).

2. Salvare il file di parametri.

## <a name="step-3-install-azure-powershell"></a>Passaggio 3: Installare PowerShell Azure

Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md) per informazioni sull'installazione della versione più recente di PowerShell di Azure, selezionare l'abbonamento e l'accesso al proprio account.

## <a name="step-4-create-a-resource-group"></a>Passaggio 4: Creare un gruppo di risorse

Tutte le risorse devono essere distribuite in un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md).

1. È possibile ottenere un elenco delle posizioni disponibili nel punto in cui è possono creare le risorse.

        Get-AzureRmLocation | sort DisplayName | Select DisplayName

2. Sostituire il valore di **$locName** con un percorso nell'elenco, ad esempio **Centrale USA**. Creare la variabile.

        $locName = "location name"
        
3. Sostituire il valore di **$rgName** con il nome del nuovo gruppo di risorse. Creare la variabile e il gruppo di risorse.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    Verrà visualizzato un elemento come in questo esempio:
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

## <a name="step-5-create-the-resources-with-the-template-and-parameters"></a>Passaggio 5: Creare le risorse con il modello e parametri

Sostituire il valore di **$templateFile** con il percorso e il nome del file modello. Sostituire il valore di **$parameterFile** con il percorso e il nome del file di parametri. Creare le variabili e distribuire il modello. 

        $templateFile = "template file"
        $parameterFile = "parameter file"
        New-AzureRmResourceGroupDeployment -ResourceGroupName $rgName -TemplateFile $templateFile -TemplateParameterFile $parameterFile

    You will see something like this:

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString

        Outputs           :

>[AZURE.NOTE] È inoltre possibile distribuire modelli e i parametri da un account di archiviazione Azure. Per ulteriori informazioni, vedere [Uso di PowerShell Azure con lo spazio di archiviazione di Azure](../storage/storage-powershell-guide-full.md).

## <a name="next-steps"></a>Passaggi successivi

- Se si sono verificati problemi con la distribuzione, un passaggio successivo, è possibile esaminare [le distribuzioni di risoluzione dei problemi delle risorse gruppo il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md)
- Informazioni su come gestire la macchina virtuale creata consultando [macchine virtuali di gestione tramite Gestione risorse di Azure e PowerShell](virtual-machines-windows-ps-manage.md).
