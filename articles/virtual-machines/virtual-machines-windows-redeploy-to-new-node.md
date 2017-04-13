<properties 
    pageTitle="Ridistribuire macchine virtuali di Windows | Microsoft Azure" 
    description="In questo articolo viene descritto come ridistribuire macchine virtuali di Windows per limitare i problemi di connessione RDP." 
    services="virtual-machines-windows" 
    documentationCenter="virtual-machines" 
    authors="iainfoulds" 
    manager="timlt"
    tags="azure-resource-manager,top-support-issue" 
/>
    

<tags 
    ms.service="virtual-machines-windows" 
    ms.devlang="na" 
    ms.topic="support-article" 
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure" 
    ms.date="09/19/2016" 
    ms.author="iainfou" 
/>


# <a name="redeploy-virtual-machine-to-new-azure-node"></a>Ridistribuire macchina virtuale al nuovo livello di nodo Azure

Se si dispone di stato con difficoltà risoluzione dei problemi Remote RDP (Desktop) può contribuire connessione o applicazione l'accesso a basato su Windows Azure macchine (), ridistribuire la macchina virtuale. Quando si ridistribuisce una macchina virtuale, sposta la macchina virtuale in un nuovo nodo all'interno dell'infrastruttura di Azure e quindi si torna nella mantenendo tutte le opzioni di configurazione e le risorse associate. In questo articolo viene illustrato come ridistribuire una macchina virtuale tramite PowerShell Azure o il portale di Azure.

> [AZURE.NOTE] Dopo che si ridistribuisce una macchina virtuale, si perde il disco temporaneo e gli indirizzi IP dinamici associati interfaccia virtuali vengono aggiornati. 

## <a name="using-azure-powershell"></a>Utilizzo di PowerShell Azure

Assicurarsi di disporre di più recente di Azure PowerShell 1. x installata nel computer. Per ulteriori informazioni, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Utilizzare questo comando di PowerShell Azure per ridistribuire la macchina virtuale:

    Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## <a name="next-steps"></a>Passaggi successivi
Se si sono verificati problemi durante la connessione per la macchina virtuale, è possibile trovare informazioni sulla [risoluzione dei problemi di connessioni RDP](virtual-machines-windows-troubleshoot-rdp-connection.md) o [RDP dettagliate risoluzione dei problemi](virtual-machines-windows-detailed-troubleshoot-rdp.md). Se non si accede a un'applicazione di una macchina virtuale, è inoltre possibile leggere i [problemi di risoluzione dei problemi delle applicazioni](virtual-machines-windows-troubleshoot-app-connection.md).