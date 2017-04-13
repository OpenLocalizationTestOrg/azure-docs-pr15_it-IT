<properties
    pageTitle="Informazioni dettagliate remote desktop Risoluzione dei problemi | Microsoft Azure"
    description="Rivedere istruzioni dettagliate su risoluzione dei problemi errori desktop remoto in cui è possibile aggiungere a un macchine virtuali di Windows Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"
    keywords="non è possibile connettersi a desktop remoto, risolvere i problemi di desktop remoto, desktop remoto non riesce a connettersi, errori desktop remoto, remote desktop Risoluzione dei problemi, problemi relativi al desktop remoti
"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Istruzioni dettagliate su risoluzione dei problemi di connessione desktop remoto per macchine virtuali di Windows in Azure

In questo articolo vengono fornite procedure dettagliate sulla risoluzione dei problemi per diagnosticare e correggere gli errori di Desktop remoto complessi per basato su Windows Azure macchine virtuali.

> [AZURE.IMPORTANT] Per eliminare più comuni errori di Desktop remoto, assicurarsi di leggere [l'articolo sulla risoluzione dei problemi di base per Desktop remoto](virtual-machines-windows-troubleshoot-rdp-connection.md) prima di continuare.

È possibile riscontrare un messaggio di errore Desktop remoto che non sono simili a uno dei messaggi di errore specifico coperti nella [Guida alla risoluzione dei problemi del Desktop remoto di base](virtual-machines-windows-troubleshoot-rdp-connection.md). Seguire questi passaggi per determinare il motivo per cui il client Desktop remoto (RDP) non riesce a connettersi al servizio RDP nella macchina virtuale Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Se necessaria ulteriore assistenza in qualsiasi momento in questo articolo, è possibile contattare i Azure esperti di [Azure MSDN e nei forum di Overflow dello Stack](https://azure.microsoft.com/support/forums/). In alternativa, è possibile inoltre archiviare una richiesta di assistenza Azure. Passare al [sito di supporto di Azure](https://azure.microsoft.com/support/options/) e fare clic su **Ottenere supporto**. Per informazioni sull'uso di supporto di Azure, leggere le [Domande frequenti sul supporto di Microsoft Azure](https://azure.microsoft.com/support/faq/).


## <a name="components-of-a-remote-desktop-connection"></a>Componenti di una connessione Desktop remoto

Connessione RDP coinvolge i componenti seguenti:

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_0.png)

Prima di procedere, potrebbe essere utile per esaminare mentalmente cosa è cambiato dopo l'ultima connessione Desktop remoto corretta per la macchina virtuale. Per esempio:

- L'indirizzo IP pubblico del servizio cloud che contiene la macchina virtuale (denominata anche l'indirizzo IP virtuale [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) o la macchina virtuale è cambiata. L'errore RDP potrebbe essere la cache del client DNS ha ancora il *vecchio indirizzo IP* registrato per il nome DNS. Svuotare la cache del client DNS e riprovare la macchina virtuale. O provare a connettersi direttamente con l'indirizzo VIP nuovo.
- Si usa un'applicazione di terze parti per gestire le connessioni Desktop remoto invece di usare la connessione generata dal portale di Azure. Verificare che la configurazione dell'applicazione include la porta TCP corretta per il traffico Desktop remoto. È possibile controllare questa porta per una macchina virtuale classica nel [portale di Azure](https://portal.azure.com), facendo clic su impostazioni di Virtual Machine > i punti finali.


## <a name="preliminary-steps"></a>Operazioni preliminari

Prima di procedere alla risoluzione dettagliata

- Controllare lo stato della macchina virtuale in Azure portale classico o il portale di Azure per eventuali problemi.
- Seguire la [procedura di correzione rapidi per gli errori comuni delle RDP nella Guida alla risoluzione dei problemi di base](virtual-machines-windows-troubleshoot-rdp-connection.md#quick-troubleshooting-steps).


Provare a riconnettersi al supporto virtuale tramite Desktop remoto dopo la procedura seguente.


## <a name="detailed-troubleshooting-steps"></a>Procedura dettagliata sulla risoluzione dei problemi

Il client Desktop remoto potrebbe non essere possibile raggiungere il servizio Desktop remoto nella macchina virtuale Azure a causa di problemi origini seguenti:

- [Computer client Desktop remoto](#source-1-remote-desktop-client-computer)
- [Dispositivo di bordo intranet dell'organizzazione](#source-2-organization-intranet-edge-device)
- [Endpoint del servizio cloud e accedere a elenco di controllo)](#source-3-cloud-service-endpoint-and-acl)
- [Gruppi di sicurezza di rete](#source-4-network-security-groups)
- [Basato su Windows Azure macchine Virtuali](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Origine 1: Computer client Desktop remoto

Verificare che il computer inserirvi connessioni Desktop remoto a un altro in locale, computer basato su Windows.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_1.png)

Se non è possibile cercare le impostazioni seguenti nel computer in uso:

- Impostazione del firewall locale che blocca il traffico Desktop remoto.
- Software proxy client che impedisce le connessioni Desktop remoto è stato installato in locale.
- Installate software che impedisce le connessioni Desktop remoto di monitoraggio di rete.
- Altri tipi di software di sicurezza che monitorare il traffico o abilitare o disabilitare tipi specifici di traffico che impedisce le connessioni Desktop remoto.

In questi casi, è temporaneamente disattivare il software e provare a connettersi a un computer locale tramite Desktop remoto. Se è possibile scoprire la causa effettiva in questo modo, con l'amministratore di rete per correggere le impostazioni di software per consentire le connessioni Desktop remoto.

## <a name="source-2-organization-intranet-edge-device"></a>Origine 2: Dispositivo organizzazione intranet bordo

Verificare che un computer direttamente connesso a Internet può connettersi Desktop remoto macchina virtuale Azure.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_2.png)

Se non si dispone di un computer in cui è connesso a Internet, creare e provare con una nuova macchina virtuale Azure in un servizio cloud o gruppo di risorse. Per ulteriori informazioni, vedere [creare una macchina virtuale che esegue Windows in Azure](virtual-machines-windows-hero-tutorial.md). È possibile eliminare la macchina virtuale e il gruppo di risorse o servizio cloud, dopo il test.

Se è possibile creare una connessione Desktop remoto con un computer direttamente connessi a Internet, controllare il dispositivo di bordo organizzazione intranet per:

- Un firewall interno devono bloccare le connessioni HTTPS a Internet.
- Un server proxy le connessioni Desktop remoto.
- Intrusione o in rete monitoraggio software in esecuzione in dispositivi di rete bordo che impedisce le connessioni Desktop remoto.

Collaborare con l'amministratore di rete per correggere le impostazioni del dispositivo bordo intranet dell'organizzazione per consentire connessioni Desktop remoto basato su HTTPS a Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Origine 3: Endpoint del servizio Cloud e ACL

Per le macchine virtuali create con il modello di distribuzione classica, verificare che un altro macchine Virtuali di Azure che è nello stesso servizio cloud o virtuali inserirvi connessioni Desktop remoto per le macchine Virtuali di Azure.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_3.png)

> [AZURE.NOTE] Per macchine virtuali create in Gestione risorse, andare al [origine 4: gruppi di sicurezza di rete](#source-4-network-security-groups).

Se non si dispone di un altro computer virtuale nel servizio cloud stesso o virtuali, crearne uno. Seguire la procedura descritta in [creare una macchina virtuale che esegue Windows in Azure](virtual-machines-windows-hero-tutorial.md). Eliminare la macchina virtuale test dopo il completamento del test.

Se non è possibile connettersi tramite Desktop remoto a una macchina virtuale nella stessa servizio cloud o virtuali, verificare di queste impostazioni:

- Configurazione dell'endpoint per il traffico Desktop remoto in macchine Virtuali di destinazione: la porta TCP privata dell'endpoint deve corrispondere la porta TCP su cui sono in attesa Servizi Desktop remoto di Virtual Machine (impostazione predefinita è 3389).
- ACL per l'endpoint di traffico Desktop remoto in macchine Virtuali di destinazione: ACL consentono di specificare consentito o negato il traffico in ingresso da Internet in base all'indirizzo IP di origine. ACL non configurati correttamente è possibile impedire il traffico in ingresso Desktop remoto all'endpoint. Selezionare l'ACL per verificare che il traffico in ingresso dagli indirizzi IP pubblici del proxy o altri server perimetrale consentito. Per ulteriori informazioni, vedere [che cos'è una rete controllo elenco di accesso?](../virtual-network/virtual-networks-acl.md)

Per verificare se l'endpoint rappresenta l'origine del problema, rimuovere l'endpoint corrente e crearne uno nuovo, la scelta di una porta casuale nell'intervallo da 49152-65535 per il numero di porta esterna. Per ulteriori informazioni, vedere [come configurare i punti finali a una macchina virtuale](virtual-machines-windows-classic-setup-endpoints.md).

## <a name="source-4-network-security-groups"></a>Origine 4: Gruppi di sicurezza di rete

Gruppi di sicurezza di rete consentire a un controllo più dettagliato del traffico consentito in ingresso e in uscita. È possibile creare regole che si estendono subnet e cloud services in una rete virtuale Azure. Controllare le regole gruppo di sicurezza di rete per assicurarsi che sia consentito il traffico Desktop remoto da Internet:

- Nel portale di Azure, selezionare la macchina virtuale
- Fare clic su **tutte le impostazioni** | **interfacce di rete** e selezionare l'interfaccia di rete.
- Fare clic su **tutte le impostazioni** | **gruppo di sicurezza di rete** e selezionare il gruppo di sicurezza di rete.
- Fare clic su **tutte le impostazioni** | **regole di protezione in ingresso** e assicurarsi di disporre di una regola che consente di RDP porta TCP 3389.
    - Se non si dispone di una regola, fare clic su **Aggiungi** per creare una regola. Immettere **TCP** per il protocollo e **3389** per l'intervallo di porta di destinazione.
    - Verificare che l'azione è impostata su **Consenti** e fare clic su OK per salvare la nuova regola in entrata.


Per ulteriori informazioni, vedere [che cos'è un gruppo di sicurezza di rete (NSG)?](../virtual-network/virtual-networks-nsg.md)

## <a name="source-5-windows-based-azure-vm"></a>Origine 5: Basato su Windows macchine Virtuali Azure

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_5.png)

Utilizzare il [pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) per vedere se l'errore è dovuto Azure macchina virtuale stessa. Se il pacchetto di diagnostica non riesce a risolvere il problema di **connettività RDP a una macchina virtuale Azure (riavvio necessario)** , seguire le istruzioni fornite in [questo articolo](virtual-machines-windows-reset-rdp.md). In questo articolo consente di reimpostare il servizio Desktop remoto sul computer virtuale:

- Abilitare la regola predefinito "Desktop remoto" Windows Firewall (porta TCP 3389).
- Attivare le connessioni Desktop remoto, impostare il valore del Registro di sistema di HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections su 0.

Provare la connessione dal proprio computer. Se ancora non è possibile connettersi tramite Desktop remoto, consultare i possibili problemi seguenti:

- Il servizio Desktop remoto non è in esecuzione in macchine Virtuali di destinazione.
- Il servizio Desktop remoto non è in ascolto sulla porta TCP 3389.
- Una regola in uscita che impedisce il traffico Desktop remoto Windows Firewall o un altro firewall locale.
- Intrusione o in rete monitoraggio software in esecuzione nel computer virtuale Azure impedisce connessioni Desktop remoto.

Per le macchine virtuali create con il modello di distribuzione classica, è possibile usare una sessione remota di PowerShell Azure alla macchina virtuale Azure. Prima di tutto, è necessario installare un certificato per servizio cloud hosting della macchina virtuale. Passare a [Configura sicura PowerShell accesso remoto in macchine virtuali di Azure](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) e scaricare il file di script **InstallWinRMCertAzureVM.ps1** al computer locale.

Se non è già successivamente, installare PowerShell Azure. Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Aprire un prompt dei comandi PowerShell di Azure, quindi cambiare la cartella corrente al percorso del file di script **InstallWinRMCertAzureVM.ps1** . Per eseguire uno script di PowerShell di Azure, è necessario impostare il criterio di esecuzione corretto. Eseguire il comando **Get-ExecutionPolicy** per determinare il livello di criteri corrente. Per informazioni sull'impostazione del livello appropriato, vedere [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Successivamente, immettere il nome dell'abbonamento Azure, il nome del servizio cloud e il nome del computer virtuale (la rimozione di < e > caratteri), quindi eseguire questi comandi.

    $subscr="<Name of your Azure subscription>"
    $serviceName="<Name of the cloud service that contains the target virtual machine>"
    $vmName="<Name of the target virtual machine>"
    .\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

È possibile ottenere il nome dell'abbonamento corretto della proprietà _SubscriptionName_ della visualizzazione del comando **Get-AzureSubscription** . È possibile ottenere il nome del servizio cloud per la macchina virtuale dalla colonna _nome_ nella visualizzazione del comando **Get-AzureVM** .

Verificare di avere il nuovo certificato. Aprire un snap-in certificati per l'utente corrente e controllare nella cartella **Autorità di certificazione radice attendibili** . Verrà visualizzato un certificato con il nome DNS del servizio cloud nella colonna rilasciato a (esempio: cloudservice4testing.cloudapp.net).

Successivamente, avviare una sessione remota di PowerShell Azure mediante questi comandi.

    $uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
    $creds = Get-Credential
    Enter-PSSession -ConnectionUri $uri -Credential $creds

Dopo aver immesso le credenziali di amministratore valido, verrà visualizzata sarà simile alla richiesta di Azure PowerShell seguente:

    [cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

La prima parte di questo messaggio è il nome di servizio cloud che contiene la destinazione macchine Virtuali, che potrebbero essere diverse da "cloudservice4testing.cloudapp.net". È ora possibile emettere Azure PowerShell i comandi per questo servizio cloud esaminare i problemi menzionati e correggere la configurazione.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Per correggere manualmente i Servizi Desktop remoto TCP porta di attesa

Se non si riesce a eseguire il [pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) per il problema di **connettività RDP a una macchina virtuale Azure (riavvio necessario)** , al prompt dei comandi della sessione di PowerShell Azure remoto, eseguire il comando.

    Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

La proprietà del numero di porta indicato il numero di porta corrente. Se necessario, modificare il Desktop remoto porta indietro numero per il valore predefinito (3389) tramite questo comando.

    Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Verificare che la porta è stata modificata in 3389 tramite questo comando.

    Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Chiudere la sessione remota di PowerShell Azure tramite questo comando.

    Exit-PSSession

Verificare che l'endpoint Desktop remoto per la macchina virtuale Azure anche utilizzi porte TCP 3398 come porta interna. Riavviare la macchina virtuale Azure e provare la connessione Desktop remoto.


## <a name="additional-resources"></a>Risorse aggiuntive

[Pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Come reimpostare la password o il servizio Desktop remoto per macchine virtuali di Windows](virtual-machines-windows-reset-rdp.md)

[Come installare e configurare PowerShell Azure](../powershell-install-configure.md)

[Risolvere i problemi di connessioni Secure Shell (SSH) in un computer virtuale Azure basato su Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Risolvere i problemi di accesso a un'applicazione in esecuzione in una macchina virtuale Azure](virtual-machines-linux-troubleshoot-app-connection.md)
