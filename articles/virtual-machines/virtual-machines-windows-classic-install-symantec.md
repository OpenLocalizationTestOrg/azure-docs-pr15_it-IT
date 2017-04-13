<properties
    pageTitle="Installare Symantec Endpoint Protection in una macchina virtuale | Microsoft Azure"
    description="Informazioni su come installare e configurare l'estensione di sicurezza Symantec Endpoint Protection in macchine Virtuali Azure nuovo o esistente creato con il modello di distribuzione classica."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="iainfou"/>

# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

In questo articolo viene illustrato come installare e configurare il client Symantec Endpoint Protection su un'esistente macchine () che eseguono Windows Server. Questo è il client completo che include servizi, ad esempio virus e protezione da spyware, il firewall e prevenzione intrusione. Il client viene installato con estensione di protezione tramite l'agente di macchine Virtuali.

Se si dispone di una sottoscrizione esistente da Symantec per una soluzione locale, è possibile utilizzare per la protezione macchine virtuali Azure. Se non si è un cliente ancora, è possibile iscriversi per una sottoscrizione di valutazione. Per ulteriori informazioni su questa soluzione, vedere [Symantec Endpoint Protection piattaforma Microsoft Azure][Symantec]. Questa pagina sono inoltre disponibili collegamenti a informazioni sulle licenze e istruzioni per installare il client se si ha già un cliente Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Installare Symantec Endpoint Protection in una macchina virtuale esistente

Prima di iniziare, è necessario quanto segue:

- Il modulo di Azure PowerShell, versione 0.8.2 o versione successiva, nel computer in uso. È possibile controllare la versione di PowerShell Azure installati con la **azure Get-modulo | versione formato tabella** comando. Per istruzioni e un collegamento all'ultima versione, vedere [come installare e configurare Azure PowerShell][PS]. Accedere a un abbonamento Azure mediante `Add-AzureAccount`.

- L'agente di macchine Virtuali in esecuzione in Azure Virtual Machine.

Prima di tutto, verificare che l'agente di macchine Virtuali sia già installato nel computer virtuale. Immettere il nome del servizio cloud e il nome del computer virtuale e quindi eseguire i comandi seguenti al prompt dei comandi di PowerShell Azure livello di amministratore. Sostituire tutti gli elementi all'interno di virgolette, inclusi i caratteri < e >.

> [AZURE.TIP] Se non si conosce il servizio cloud e i nomi di macchina virtuale, eseguire **Get-AzureVM** per l'elenco dei nomi per tutte le macchine virtuali nell'abbonamento corrente.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Se il comando di **scrittura host** viene visualizzato **True**, l'agente di macchine Virtuali è installato. Se viene visualizzato **False**, vedere le istruzioni e un collegamento per il download nel blog Azure post [agente macchine Virtuali ed estensioni - parte 2][Agent].

Se è installato l'agente di macchine Virtuali, eseguire questi comandi per installare l'agente Symantec Endpoint Protection.

    $Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

    Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Per verificare che l'estensione di sicurezza Symantec è stato installato e sia aggiornato:

1.  Accedere al computer virtuale. Per ulteriori informazioni, vedere [come accedere a una macchina virtuale che eseguono Windows Server][Logon].
2.  Per Windows Server 2008 R2, fare clic su **Start > Symantec Endpoint Protection**. Per Windows Server 2012 o Windows Server 2012 R2, dalla schermata start digitare **Symantec**e quindi fare clic su **Symantec Endpoint Protection**.
3.  Nella scheda **stato** della finestra di **Stato Symantec Endpoint Protection** , applicare gli aggiornamenti o riavviare se necessario.

## <a name="additional-resources"></a>Risorse aggiuntive

[Come accedere a una macchina virtuale che eseguono Windows Server][Logon]

[Caratteristiche e le estensioni macchine Virtuali di azure][Ext]


<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-classic-tutorial.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-windows-classic-connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493