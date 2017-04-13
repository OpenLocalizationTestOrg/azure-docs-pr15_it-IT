<properties
   pageTitle="Verificare una connessione di gateway | Microsoft Azure"
   description="In questo articolo viene illustrato come verificare una connessione di gateway nel modello di distribuzione Manager delle risorse"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="verify-a-gateway-connection"></a>Verificare una connessione di gateway

È possibile verificare la connessione di gateway in vari modi. In questo articolo illustra come verificare lo stato di una connessione di gateway di gestione risorse tramite il portale Azure e tramite PowerShell.


## <a name="verify-using-powershell"></a>Verificare l'utilizzo di PowerShell

È necessario installare l'ultima versione dei cmdlet di Manager delle risorse PowerShell Azure. Per informazioni sull'installazione dei cmdlet di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md). Per ulteriori informazioni sull'uso dei cmdlet Gestione risorse, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

### <a name="step-1-log-in-to-your-azure-account"></a>Passaggio 1: Accedere al proprio account Azure

1. Aprire la console di PowerShell con privilegi elevati e connettersi all'account.

        Login-AzureRmAccount

2. Controllare le sottoscrizioni per l'account.

        Get-AzureRmSubscription 

3. Specificare l'abbonamento a cui si desidera utilizzare.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="step-2-verify-your-connection"></a>Passaggio 2: Verificare la connessione


[AZURE.INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)] 


## <a name="verify-using-the-azure-portal"></a>Verificare tramite il portale di Azure

[AZURE.INCLUDE [verify connection portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)] 


## <a name="next-steps"></a>Passaggi successivi

- È possibile aggiungere macchine virtuali alle tue reti virtuale. Vedere [creare una macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md) per la procedura.

