<properties
   pageTitle="Creare un utilizzando un modello in Gestione risorse di bilanciamento del carico interno | Microsoft Azure"
   description="Informazioni su come creare un bilanciamento del carico interno utilizzando un modello in Gestione risorse"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="create-an-internal-load-balancer-using-a-template"></a>Creare un utilizzando un modello di bilanciamento del carico interno

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modello di distribuzione classica](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Distribuire il modello utilizzando fare clic su per la distribuzione

Nell'esempio di modello disponibile nell'archivio pubblico utilizza un file di parametro che contiene il valore predefinito valori utilizzati per generare lo scenario descritto in precedenza. Per distribuire il modello con fare clic su come distribuire, seguire [questo collegamento](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer), fare clic su **Distribuisci in Azure**, sostituire i valori di parametro predefiniti, se necessario e seguire le istruzioni nel portale.

## <a name="deploy-the-template-by-using-powershell"></a>Distribuire il modello tramite PowerShell

Per distribuire il modello scaricato tramite PowerShell, seguire la procedura seguente.

1. Se non è mai utilizzato Azure PowerShell, vedere [come installare e configurare PowerShell Azure](../../articles/powershell-install-configure.md) e seguire le istruzioni fino alla fine per eseguire l'accesso a Azure e selezionare l'abbonamento.
2. Scaricare il file di parametri sul disco locale.
3. Modificare il file e salvarlo.
4. Eseguire il cmdlet **New-AzureRmResourceGroupDeployment** per creare un gruppo di risorse utilizzando il modello.

        New-AzureRmResourceGroupDeployment -Name TestRG -Location westus `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
            -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Distribuire il modello tramite CLI Azure

Per distribuire il modello tramite CLI Azure, seguire la procedura seguente.

1. Se non è mai utilizzato CLI Azure, vedere [installare e configurare CLI Azure](../../articles/xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si seleziona l'account Azure e la sottoscrizione.
2. Eseguire il comando **configurazione azure modalità** per passare alla modalità di gestione risorse, come illustrato di seguito.

        azure config mode arm

    Ecco l'output previsto per il comando precedente:

        info:    New mode is arm

3. Aprire il file di parametro, selezionare il contenuto e salvarlo in un file nel computer. In questo esempio è stato salvato il file di parametri per *parameters.json*.

4. Eseguire il comando **distribuzione del gruppo di azure creare** per distribuire la nuova interno bilanciamento del carico mediante i file di modello e un parametro scaricato e modificato in precedenza. Elenco visualizzato dopo l'output illustra i parametri utilizzati.

        azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json

## <a name="next-steps"></a>Passaggi successivi

[Configurare una modalità di distribuzione carico bilanciamento utilizzando affinità dell'indirizzo IP di origine](load-balancer-distribution-mode.md)

[Configurare le impostazioni di timeout di inattività TCP per il servizio di bilanciamento del carico](load-balancer-tcp-idle-timeout.md)



