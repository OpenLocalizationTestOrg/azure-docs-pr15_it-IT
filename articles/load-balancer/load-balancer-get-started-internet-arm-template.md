<properties
   pageTitle="Creare una rete Internet affiancate in Gestione risorse utilizzando un modello di bilanciamento del carico | Microsoft Azure"
   description="Informazioni su come creare un sistema di bilanciamento del carico in Gestione risorse utilizzando un modello è connessa a Internet"
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

# <a name="creating-an-internet-facing-load-balancer-using-a-template"></a>Creazione di un servizio di bilanciamento del carico utilizzando un modello è connessa a Internet

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione di Manager delle risorse. È inoltre possibile [imparare a creare un modello distribuzione classica di bilanciamento del carico è connessa a Internet](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Distribuire il modello utilizzando fare clic su per la distribuzione

Nell'esempio di modello disponibile nell'archivio pubblico utilizza un file di parametro che contiene il valore predefinito valori utilizzati per generare lo scenario descritto in precedenza. Per distribuire il modello con fare clic su come distribuire, seguire [questo collegamento](http://go.microsoft.com/fwlink/?LinkId=544801), fare clic su **Distribuisci in Azure**, sostituire i valori di parametro predefiniti, se necessario e seguire le istruzioni nel portale.

## <a name="deploy-the-template-by-using-powershell"></a>Distribuire il modello tramite PowerShell

Per distribuire il modello scaricato tramite PowerShell, seguire la procedura seguente.

1. Se non è mai utilizzato Azure PowerShell, vedere [come installare e configurare PowerShell Azure](../../articles/powershell-install-configure.md) e seguire le istruzioni fino alla fine per eseguire l'accesso a Azure e selezionare l'abbonamento.

2. Eseguire il cmdlet **New-AzureRmResourceGroupDeployment** per creare un gruppo di risorse utilizzando il modello.

        New-AzureRmResourceGroupDeployment -Name TestRG -Location uswest `
            -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
            -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Distribuire il modello tramite CLI Azure

Per distribuire il modello tramite CLI Azure, seguire la procedura seguente.

1. Se non è mai utilizzato CLI Azure, vedere [installare e configurare CLI Azure](../../articles/xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si seleziona l'account Azure e la sottoscrizione.
2. Eseguire il comando **configurazione azure modalità** per passare alla modalità di gestione risorse, come illustrato di seguito.

        azure config mode arm

    Ecco l'output previsto per il comando precedente:

        info:    New mode is arm

3. Dal browser passare al [Modello di Guida introduttiva](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules), copiare il contenuto del file json e incollare in un nuovo file nel computer. Per questo scenario, si preferisce copiare i valori sotto in un file denominato **c:\lb\azuredeploy.parameters.json**.
4. Eseguire il cmdlet **distribuzione del gruppo di azure creare** per la distribuzione di bilanciamento del nuovo mediante i file di modello e un parametro scaricato e modificato in precedenza. Elenco visualizzato dopo l'output illustra i parametri utilizzati.

        azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' -e 'c:\lb\azuredeploy.parameters.json'

## <a name="next-steps"></a>Passaggi successivi

[Per iniziare la configurazione di un servizio di bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurare una modalità di distribuzione carico di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni di timeout di inattività TCP per il servizio di bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
