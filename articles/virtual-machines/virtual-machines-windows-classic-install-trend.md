<properties
    pageTitle="Installare Trend Micro complete Security in una macchina virtuale | Microsoft Azure"
    description="Questo articolo descrive come installare e configurare la protezione Trend Micro in una macchina virtuale creata con il modello di distribuzione classica Azure."
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


# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Come installare e configurare Trend Micro complete Security come servizio in una macchina virtuale di Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

In questo articolo viene illustrato come installare e configurare Trend Micro complete Security come servizio su un nuovo o esistente macchine () che eseguono Windows Server. Protezione completa come servizio include protezione dal malware, un firewall, un sistema di prevenzione intrusione e il monitoraggio dell'integrità.

Il client viene installato con estensione di protezione tramite l'agente di macchine Virtuali. In una nuova macchina virtuale è installare l'agente di macchine Virtuali insieme agente di sicurezza completa. In una macchina virtuale esistente che non dispone di agente di macchine Virtuali, è necessario scaricare e installare prima. In questo articolo è descritta entrambi i casi.

Se si dispone di un abbonamento esistente da Trend Micro per una soluzione locale, è possibile utilizzare per proteggere le macchine virtuali Azure. Se non si è un cliente ancora, è possibile iscriversi per una sottoscrizione di valutazione. Per ulteriori informazioni su questa soluzione, vedere il blog di Trend Micro post [Microsoft Azure macchine Virtuali protezione agente di estensione per complete](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Installare l'agente di sicurezza completa in una nuova macchina virtuale

Il [portale classica Azure](http://manage.windowsazure.com) consente di installare l'agente di macchine Virtuali e l'estensione di sicurezza Trend Micro quando si utilizza l'opzione **Dalla raccolta** per creare la macchina virtuale. Se si sta creando una sola macchina virtuale, tramite il portale è un modo semplice per aggiungere la protezione da Trend Micro.

Questa opzione **Dalla raccolta** verrà visualizzata una creazione guidata che consente di impostare la macchina virtuale. Utilizzare l'ultima pagina della procedura guidata per installare l'agente di macchine Virtuali e l'estensione di protezione Trend Micro. Per istruzioni generali, vedere [creare una macchina virtuale che esegue Windows nel portale di classica Azure](virtual-machines-windows-classic-tutorial.md). Quando si riceve fino all'ultima pagina della procedura guidata, eseguire le operazioni seguenti:

1.  In **Macchine Virtuali agente**, selezionare **Installa agente macchine Virtuali**.

2.  In **Estensioni di protezione**, selezionare **Agente di protezione di tendenza Micro complete**.

    ![Installare l'agente di macchine Virtuali e di sicurezza completa](./media/virtual-machines-windows-classic-install-trend/InstallVMAgentandTrend.png)

3.  Fare clic sul segno di spunta per creare la macchina virtuale.

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Installare l'agente di sicurezza completa in una macchina virtuale esistente

Per installare l'agente in una macchina virtuale esistente, è necessario quanto segue:

- Il modulo di Azure PowerShell, versione 0.8.2 o versioni successive, installato nel computer locale. È possibile controllare la versione di PowerShell Azure installati utilizzando il **azure Get-modulo | versione formato tabella** comando. Per istruzioni e un collegamento all'ultima versione, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md). Accedere a un abbonamento Azure mediante `Add-AzureAccount`.

- Agente di macchine Virtuali installato nel computer virtuale di destinazione.

Prima di tutto, verificare che l'agente di macchine Virtuali sia già installato. Immettere il nome del servizio cloud e il nome del computer virtuale e quindi eseguire i comandi seguenti al prompt dei comandi di PowerShell Azure livello di amministratore. Sostituire tutti gli elementi all'interno di virgolette, inclusi i caratteri < e >.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Se non si conosce il servizio cloud e il nome macchina virtuale, eseguire **Get-AzureVM** per visualizzare le informazioni per tutte le macchine virtuali nell'abbonamento corrente.

Se il comando di **scrittura host** restituisce **True**, l'agente di macchine Virtuali è installato. Se viene restituito **False**, vedere le istruzioni e un collegamento per il download nel blog Azure post [agente macchine Virtuali ed estensioni - parte 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Se è installato l'agente di macchine Virtuali, eseguire questi comandi.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Passaggi successivi

Bastano pochi minuti per l'agente di inizio durante l'installazione. In seguito, è necessario attivare complete la protezione del computer virtuale in modo che può essere gestita da un responsabile protezione completa. Per ulteriori istruzioni, vedere:

- Articolo di tendenza di questa soluzione [Instant-On Cloud Security per Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
- Uno [script di Windows PowerShell di esempio](http://go.microsoft.com/fwlink/?LinkId=404100) per configurare il computer virtuale
- [Istruzioni](http://go.microsoft.com/fwlink/?LinkId=404099) per il campione

## <a name="additional-resources"></a>Risorse aggiuntive

[Come effettuare l'accesso a una macchina virtuale che eseguono Windows Server]

[Azure macchine Virtuali estensioni e funzionalità]


<!--Link references-->
[Come effettuare l'accesso a una macchina virtuale che eseguono Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Azure macchine Virtuali estensioni e funzionalità]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
