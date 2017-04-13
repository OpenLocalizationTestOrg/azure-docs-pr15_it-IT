<properties
   pageTitle="Iniziare a creare un Internet affiancate di bilanciamento del carico in modalità classica tramite PowerShell | Microsoft Azure"
   description="Informazioni su come creare un servizio di bilanciamento del carico in modalità classica tramite PowerShell è connessa a Internet"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Iniziare a creare un bilanciamento del carico (classico) PowerShell è connessa a Internet

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione classica. È inoltre possibile [imparare a creare un servizio di bilanciamento del carico Gestione risorse di Azure è connessa a Internet](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## <a name="set-up-load-balancer-using-powershell"></a>Configurazione di bilanciamento del carico tramite PowerShell

Per configurare un bilanciamento del carico tramite powershell, seguire la procedura seguente:

1. Se non è mai utilizzato Azure PowerShell, vedere [come installare e configurare PowerShell Azure](../../articles/powershell-install-configure.md) e seguire le istruzioni fino alla fine per eseguire l'accesso a Azure e selezionare l'abbonamento.


2. Dopo aver creato una macchina virtuale, è possibile utilizzare i cmdlet di PowerShell per aggiungere un servizio di bilanciamento del carico a una macchina virtuale all'interno del servizio cloud stesso.

Nell'esempio seguente verrà aggiunto che un bilanciamento del carico set denominato "webfarm" nel cloud servizio "mytestcloud" (o myctestcloud.cloudapp.net), aggiungere gli endpoint per il servizio di bilanciamento del carico in macchine virtuali denominate "web1" e "web2". Bilanciamento del carico riceve il traffico di rete sulla porta 80 e bilancia il carico tra le macchine virtuali definite dall'endpoint locale (in questo caso porta 80) utilizzando TCP.


### <a name="step-1"></a>Passaggio 1
Creare un endpoint di bilanciamento del carico per la prima macchina virtuale "web1"

    Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

### <a name="step-2"></a>Passaggio 2

Creare un altro endpoint per la seconda macchina virtuale "web2" utilizzando lo stesso nome di set di bilanciamento carico

    Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Rimuovere una macchina virtuale da un servizio di bilanciamento del carico

È possibile utilizzare AzureEndpoint Rimuovi per rimuovere un endpoint macchina virtuale dal servizio di bilanciamento del carico

    Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM

## <a name="next-steps"></a>Passaggi successivi

È anche possibile [iniziare la creazione di un servizio di bilanciamento del carico interno](load-balancer-get-started-ilb-classic-ps.md) e configurare il tipo della [modalità di distribuzione](load-balancer-distribution-mode.md) per un comportamento il traffico di rete bilanciamento di carico di especific.

Se l'applicazione necessita di mantenere le connessioni attiva per i server dietro un bilanciamento del carico, è possibile comprendere a fondo [le impostazioni di timeout di inattività TCP per un bilanciamento del carico](load-balancer-tcp-idle-timeout.md). Può risultare utile per conoscere il comportamento della connessione inattivo quando si utilizza Bilanciamento del carico di Azure.

