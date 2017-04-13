<properties
    pageTitle="Distribuire modelli con PowerShell in pila Azure | Microsoft Azure"
    description="Informazioni su come distribuire una macchina virtuale utilizzando un modello di Manager delle risorse e PowerShell."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-powershell"></a>Distribuzione di modelli in pila Azure tramite PowerShell

Usare PowerShell per distribuire modelli di gestione di risorse Azure la prova pratica dello Stack di Azure.  Modelli di gestione risorse distribuire ed effettuare il provisioning di tutte le risorse per l'applicazione in un'operazione su una singola e coordinata.

## <a name="run-azurerm-powershell-cmdlets"></a>Eseguire i cmdlet di AzureRM PowerShell

In questo esempio, eseguire uno script per distribuire una macchina virtuale in Azure Stack prova utilizzando un modello di Manager delle risorse.  Prima di procedere, verificare di che avere [installato e configurato PowerShell](azure-stack-connect-powershell.md)  

Il disco rigido virtuale utilizzato in questo modello di esempio è un'immagine di marketplace predefinita (Windows Server del 2012-R2-centro).

1.  Passare a <http://aka.ms/AzureStackGitHub>, cercare il modello **101-semplici-windows-macchine virtuali** e salvarla in uno dei seguenti percorsi: c:\\modelli\\azuredeploy-101-semplici-windows-vm.json.

2.  In PowerShell, eseguire il seguente script di distribuzione. Sostituire *il nome utente* e la *password* con il nome utente e la password. Degli impieghi successivi, aumentare il valore per il parametro *$myNum* evitare di sovrascrivere la distribuzione.

    ```PowerShell
        # Set Deployment Variables
        $myNum = "001" #Modify this per deployment
        $RGName = "myRG$myNum"
        $myLocation = "local"

        # Create Resource Group for Template Deployment
        New-AzureRmResourceGroup -Name $RGName -Location $myLocation

        # Deploy Simple IaaS Template
        New-AzureRmResourceGroupDeployment `
            -Name myDeployment$myNum `
            -ResourceGroupName $RGName `
            -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
            -NewStorageAccountName mystorage$myNum `
            -DnsNameForPublicIP mydns$myNum `
            -AdminUsername <username> `
            -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
            -VmName myVM$myNum `
            -WindowsOSVersion 2012-R2-Datacenter
    ```

3.  Aprire il portale di Azure Stack, fare clic su **Sfoglia**, fare clic su **macchine virtuali**e cercare la nuova macchina virtuale (*myDeployment001*).

## <a name="video-example-hybrid-virtual-machine-deployment"></a>Esempio di video: distribuzione ibrida di macchina virtuale

[AZURE.VIDEO microsoft-azure-stack-tp1-poc-hybrid-vm-deployment]

## <a name="next-steps"></a>Passaggi successivi

[Distribuire modelli con Visual Studio](azure-stack-deploy-template-visual-studio.md)
