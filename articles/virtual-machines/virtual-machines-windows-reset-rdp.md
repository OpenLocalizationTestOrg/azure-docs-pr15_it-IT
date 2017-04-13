<properties
    pageTitle="Reimpostare la password o configurazione Desktop remoto in una macchina virtuale di Windows | Microsoft Azure"
    description="Informazioni su come reimpostare la password di un account o Servizi Desktop remoto su una macchina virtuale di Windows tramite il portale di Azure e Azure PowerShell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Come reimpostare la password di accesso in una macchina virtuale di Windows o il servizio Desktop remoto

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Se non è possibile connettersi a una Windows macchine (), è possibile reimpostare la password di amministratore locale o reimpostare la configurazione di Servizi Desktop remoto. È possibile usare il portale di Azure o l'estensione accesso macchine Virtuali di Azure PowerShell per reimpostare la password. Se si utilizza PowerShell, verificare che il più recente PowerShell modulo installato nel computer in uso ed effettuato l'accesso all'abbonamento Azure. Per informazioni dettagliate, informazioni, [vedere installare e configurare Azure PowerShell](../powershell-install-configure.md).

> [AZURE.TIP] È possibile controllare la versione di PowerShell che è stato installato utilizzando`Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version`

## <a name="windows-vms-in-resource-manager-deployment-model"></a>Macchine virtuali di Windows nel modello di distribuzione di Manager delle risorse

### <a name="azure-portal"></a>Portale di Azure
Selezionare la macchina virtuale facendo clic su **Sfoglia** > **macchine virtuali** > *la macchina virtuale di Windows* > **tutte le impostazioni** > **reimpostare la password**. Verrà visualizzato e il reimpostazione password:

![Pagina la reimpostazione della password](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

Immettere il nome utente e la nuova password, quindi fare clic su **Salva**. Provare a connettersi nuovamente la macchina virtuale.

### <a name="vmaccess-extension-and-powershell"></a>Estensione VMAccess e PowerShell

Assicurarsi che è installato Azure PowerShell 1.0 o versioni successive ed effettuato per l'account utilizzando il `Login-AzureRmAccount` cmdlet.

#### <a name="reset-the-local-administrator-account-password"></a>**Reimpostare la password dell'account di amministratore locale**

È possibile reimpostare il nome utente o password di amministratore tramite il comando di PowerShell [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) .

Creare le credenziali dell'account di amministratore locale utilizzando il comando seguente:

    $cred=Get-Credential

Se si digita un nome diverso da quello corrente, il seguente comando estensione VMAccess Rinomina account dell'amministratore locale, assegna la password per l'account e problemi di un evento di disconnessione Desktop remoto. Se l'account di amministratore locale è disattivato, l'estensione VMAccess opzione è stata abilitata.

Utilizzare l'estensione di access macchine Virtuali per impostare le nuove credenziali come indicato di seguito:

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" `
        -Location WestUS -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"


Sostituire `myRG`, `myVM`, `myVMAccess`e il percorso con valori i contenuti pertinenti per l'installazione e configurazione.


#### <a name="reset-the-remote-desktop-service-configuration"></a>**Reimpostare la configurazione di Servizi Desktop remoto**

È possibile ripristinare la macchina virtuale accesso remoto utilizzando [Set AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) o [AzureRmVMAccessExtension Set](https://msdn.microsoft.com/library/mt619447.aspx)come indicato di seguito. (Sostituire il `myRG`, `myVM`, `myVMAccess` e un percorso con valori personalizzati.)

    Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
        -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"

O:<br>

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0


> [AZURE.TIP] Entrambi i comandi aggiungere un nuovo agente di access macchine Virtuali denominato la macchina virtuale. In qualsiasi momento, una macchina virtuale consentito solo un unico agente di accesso di macchine Virtuali. Per impostare l'accesso macchine Virtuali agente proprietà correttamente, rimuovere l'agente di accesso impostato in precedenza, utilizzando `Remove-AzureRmVMAccessExtension` o `Remove-AzureRmVMExtension`. A partire da Azure PowerShell versione 1.2.2, è possibile evitare questo passaggio quando si usa `Set-AzureRmVMExtension` con un `-ForceRerun` opzione. Quando si usa `-ForceRerun`, assicurarsi di usare lo stesso nome per l'agente di access macchine Virtuali come set tramite il comando precedente.

Se non è ancora possibile connettersi in remoto alla macchina virtuale, vedere altre procedure per provare a [risolvere i problemi di Desktop remoto connessioni a una macchina virtuale Azure basato su Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).


## <a name="windows-vms-in-the-classic-deployment-model"></a>Macchine virtuali di Windows nel modello di distribuzione classica

### <a name="azure-portal"></a>Portale di Azure

Per macchine virtuali create utilizzando il modello di distribuzione classica, è possibile usare il [portale di Azure](https://portal.azure.com) per reimpostare il servizio Desktop remoto. Fare clic su: **Sfoglia** > **(classica) macchine virtuali di** > *la macchina virtuale di Windows* > **Reimposta remoto**. Verrà visualizzata la pagina seguente.

![Pagina di configurazione RDP Ripristina](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

È anche possibile provare a reimpostare il nome e la password dell'account dell'amministratore locale. Fare clic su: **Sfoglia** > **(classica) macchine virtuali di** > *la macchina virtuale di Windows* > **tutte le impostazioni** > **reimpostare la password**. Verrà visualizzata la pagina seguente.

![Pagina la reimpostazione della password](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Dopo avere immesso il nuovo nome utente e la password, fare clic su **Salva**.

### <a name="vmaccess-extension-and-powershell"></a>Estensione VMAccess e PowerShell

Verificare che l'agente di macchine Virtuali è installato nel computer virtuale. L'estensione VMAccess non è necessario installare prima di poter usare, come l'agente di macchine Virtuali è disponibile. Verificare che l'agente di macchine Virtuali sia già installato utilizzando il comando seguente. (Sostituire "myCloudService" e "myVM", i nomi del servizio cloud e le macchine Virtuali, rispettivamente. Consultare l'articolo questi nomi eseguendo `Get-AzureVM` senza parametri.)

    $vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
    write-host $vm.VM.ProvisionGuestAgent

Se il comando di **scrittura host** viene visualizzato **True**, l'agente di macchine Virtuali è installato. Se viene visualizzato **False**, vedere le istruzioni e un collegamento per il download di post di blog Azure [agente macchine Virtuali ed estensioni - parte 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) .

Se la macchina virtuale è stato creato tramite il portale, verificare se `$vm.GetInstance().ProvisionGuestAgent` restituisce **True**. In caso contrario, è possibile impostare tramite questo comando:

    $vm.GetInstance().ProvisionGuestAgent = $true

Questo comando consente di evitare l'errore seguente quando si esegue il comando **Set AzureVMExtension** nella procedura seguente: "Agente di provisioning Guest deve essere abilitata sull'oggetto macchine Virtuali prima di impostare IaaS VM accesso estensione."

#### <a name="reset-the-local-administrator-account-password"></a>**Reimpostare la password dell'account di amministratore locale**

Creare una credenziali di accesso con il nome dell'account amministratore locale corrente e una nuova password e quindi eseguire la `Set-AzureVMAccessExtension` come indicato di seguito.

    $cred=Get-Credential
    Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

Se si digita un nome diverso da quello corrente, l'estensione VMAccess Rinomina account dell'amministratore locale, assegna la password per l'account e problemi di un Desktop remoto disconnessione. Se l'account di amministratore locale è disattivato, l'estensione VMAccess opzione è stata abilitata.

Questi comandi reimpostano anche la configurazione di Servizi Desktop remoto.

#### <a name="reset-the-remote-desktop-service-configuration"></a>**Reimpostare la configurazione di Servizi Desktop remoto**

Per reimpostare la configurazione di Servizi Desktop remoto, eseguire il comando seguente:

    Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

L'estensione VMAccess viene eseguita due comandi sul computer virtuale:

- `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

Questo comando consente il gruppo di Windows Firewall incorporato che consente il traffico Desktop remoto in ingresso, che utilizza la porta TCP 3389.

- `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

Questo comando di fDenyTSConnections del Registro di sistema valore impostato su 0, per consentire le connessioni Desktop remoto.


## <a name="next-steps"></a>Passaggi successivi

Se l'estensione di access macchine Virtuali di Azure non risponde e non si riesce a reimpostare la password, è possibile [reimpostare la password di Windows locale non in linea](virtual-machines-windows-reset-local-password-without-agent.md). Questo metodo è un processo più avanzato e richiede la connessione del disco rigido virtuale della macchina virtuale un problema a un'altra macchina virtuale. Seguire la procedura descritta in questo articolo prima di tutto e tentare soltanto il metodo di reimpostazione password non in linea come ultima risorsa.

[Caratteristiche e le estensioni macchine Virtuali azure](virtual-machines-windows-extensions-features.md)

[Connettersi a una macchina virtuale Azure con RDP o SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale Azure basato su Windows](virtual-machines-windows-troubleshoot-rdp-connection.md)
