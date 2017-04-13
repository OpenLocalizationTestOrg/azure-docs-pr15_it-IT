<properties
    pageTitle="Non è possibile RDP a una macchina virtuale Azure | Microsoft Azure"
    description="Risolvere i problemi che non è possibile connettersi al computer di virtuale Windows Azure con Desktop remoto"
    keywords="Errore desktop remoto, errore di connessione desktop remoto, non è possibile connettersi a macchine Virtuali, remote desktop Risoluzione dei problemi"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="10/26/2016"
    ms.author="iainfou"/>

# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Risolvere i problemi di connessioni Desktop remoto per una macchina virtuale Azure

La connessione remota protocollo RDP (Desktop) per il basato su Windows Azure macchine () può avere esito negativo per diversi motivi, lasciare è possibile accedere la macchina virtuale. Il problema può essere con il servizio Desktop remoto macchina virtuale, la connessione di rete o il client Desktop remoto nel computer host. In questo articolo vengono illustrati alcuni metodi più comuni per risolvere i problemi di connessione RDP. 

Se necessaria ulteriore assistenza in qualsiasi momento in questo articolo, è possibile contattare esperti Azure su [Overflow dello Stack forum MSDN Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare una richiesta di assistenza Azure. Passare al [sito del supporto tecnico Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Azioni rapide sulla risoluzione dei problemi
Dopo avere istruzioni dettagliate sulla risoluzione dei problemi, provare a riconnessione macchina virtuale:

1. Reimpostare la configurazione Desktop remoto.
2. Selezionare gruppo di sicurezza di rete di regole / Cloud Services i punti finali.
3. Esaminare i registri console macchina virtuale.
4. Verificare l'integrità di risorsa macchina virtuale.
5. Reimpostare la password di macchine Virtuali.
6. Riavviare la macchina virtuale.
7. Ridistribuire la macchina virtuale.

Continuare la lettura se è necessario più dettagliate e spiegazioni.

> [AZURE.TIP] Se il pulsante **Connetti** per la macchina virtuale non è disponibile nel portale e non si è connessi a Azure tramite una connessione [VPN da sito](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) o di [Strada](../expressroute/expressroute-introduction.md) , è necessario creare e assegnare un indirizzo IP pubblico della macchina virtuale prima di poter usare RDP. È possibile leggere informazioni sugli [indirizzi IP pubblici in Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Modalità di risoluzione dei problemi di RDP
È possibile risolvere i problemi di macchine virtuali create con il modello di distribuzione di Manager delle risorse utilizzando uno dei metodi seguenti:

- [Portale azure](#using-the-azure-portal) - ottimo se è necessario rapidamente reimpostare le credenziali dell'utente o configurazione RDP e si non sono installati gli strumenti di Azure.
- [Azure PowerShell](#using-azure-powershell) - se si ha familiarità con la richiesta di PowerShell, reimpostare le credenziali utente o configurazione di RDP utilizzando i cmdlet di PowerShell Azure rapidamente.

È inoltre disponibile la procedura di risoluzione dei problemi macchine virtuali create con il [modello di distribuzione classica](#troubleshoot-vms-created-using-the-classic-deployment-model).


<a id="fix-common-remote-desktop-errors"></a>
## <a name="troubleshoot-using-the-azure-portal"></a>Risoluzione dei problemi tramite il portale di Azure
Dopo ogni passaggio sulla risoluzione dei problemi, provare a riconnettersi per la macchina virtuale. Se non è ancora possibile connettersi, procedere al passaggio successivo.

1. **Reimpostare la connessione RDP**. Questa procedura reimposta la configurazione RDP quando le connessioni Remote sono disabilitate o le regole di Windows Firewall bloccano RDP, ad esempio.

    Selezionare la macchina virtuale nel portale di Azure. Scorrere in basso nel riquadro impostazioni alla sezione **supporto + risoluzione dei problemi** nella parte inferiore dell'elenco. Fare clic sul pulsante **Reimposta password** . Impostare la **modalità** di **reimpostare solo la configurazione** e quindi fare clic sul pulsante di **aggiornamento** :

    ![Reimpostare la configurazione RDP nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-rdp.png)

2. **Le regole di verificare il gruppo di sicurezza di rete**. Questa procedura consente di verificare di avere una regola del gruppo di sicurezza di rete per consentire il traffico RDP. La porta predefinita per RDP è la porta TCP 3389. Una regola per consentire il traffico RDP potrebbe non essere creata automaticamente quando si crea la macchina virtuale.

    Selezionare la macchina virtuale nel portale di Azure. Fare clic su **interfacce di rete** dal riquadro impostazioni.

    ![Visualizza le interfacce di rete per una macchina virtuale nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-network-interfaces.png)

    Selezionare l'interfaccia di rete dall'elenco (è disponibile in genere un solo):

    ![Selezionare l'interfaccia di rete nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-interface.png)

    Selezionare **il gruppo di sicurezza di rete** per visualizzare il gruppo di sicurezza di rete associati l'interfaccia di rete:

    ![Gruppo di sicurezza rete selezionare nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-nsg.png)

    Verificare l'esistenza una regola in entrata che consente il traffico RDP porta TCP 3389. Nell'esempio seguente viene illustrata una regola di protezione valido che consenta il traffico RDP. È possibile vedere `Service` e `Action` siano configurati correttamente:

    ![Verificare la regola RDP NSG nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/verify-nsg-rules.png)

    Se non si dispone di una regola che consente il traffico RDP, [creare una regola gruppo di sicurezza di rete](virtual-machines-windows-nsg-quickstart-portal.md). Consentire la porta TCP 3389.

3. **Diagnostica avvio macchine Virtuali di revisione**. Questa procedura rivede i registri di console macchine Virtuali per determinare se la macchina virtuale segnala un problema. Macchine virtuali non tutti sono diagnostica avvio abilitata, in modo che questa procedura potrebbe essere facoltativa.
    
    Procedure di risoluzione dei problemi specifiche non rientrano nell'ambito di questo articolo, ma possono indicare un problema più ampio che influisce negativamente sulla connettività RDP. Per ulteriori informazioni sulla verifica dei registri console e schermata macchine Virtuali, vedere [Avvio diagnostica per macchine virtuali](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

4. **Verificare l'integrità di risorsa macchina virtuale**. Questa procedura verifica esistono problemi noti con la piattaforma Azure che potrebbe influire negativamente la connettività per la macchina virtuale.

    Selezionare la macchina virtuale nel portale di Azure. Scorrere in basso nel riquadro impostazioni alla sezione **supporto + risoluzione dei problemi** nella parte inferiore dell'elenco. Fare clic sul pulsante **dell'integrità della risorsa** . Una macchina virtuale integro segnala come **disponibili**:

    ![Verificare l'integrità del risorsa macchine Virtuali nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/check-resource-health.png)

5. **Reimpostare le credenziali dell'utente**. Questa procedura reimposta la password di un account di amministratore locale quando si è certi o dimenticato le credenziali.

    Selezionare la macchina virtuale nel portale di Azure. Scorrere in basso nel riquadro impostazioni alla sezione **supporto + risoluzione dei problemi** nella parte inferiore dell'elenco. Fare clic sul pulsante **Reimposta password** . Assicurarsi che sia impostata la **modalità** per **reimpostare la password** e quindi immettere il nome utente e una nuova password. Infine, fare clic sul pulsante di **aggiornamento** :

    ![Reimpostare le credenziali utente nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-password.png)

6. **Riavviare la macchina virtuale**. Questa procedura può correggere eventuali problemi sottostante che presenta la macchina virtuale stesso.

    Selezionare la macchina virtuale nel portale di Azure e fare clic sulla scheda **Panoramica** . Fare clic sul pulsante **riavviare** :

    ![Riavviare la macchina virtuale nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/restart-vm.png)

7. **Ridistribuire la macchina virtuale**. Questa procedura ridistribuisce la macchina virtuale a un altro host all'interno di Azure per correggere i problemi di rete o qualsiasi piattaforma sottostante.

    Selezionare la macchina virtuale nel portale di Azure. Scorrere in basso nel riquadro impostazioni alla sezione **supporto + risoluzione dei problemi** nella parte inferiore dell'elenco. Fare clic sul pulsante **ridistribuire** e quindi fare clic su **ridistribuire**:

    ![Ridistribuire macchine Virtuali nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/redeploy-vm.png)

    Al termine dell'operazione, disco temporanee vengono persi e indirizzi IP dinamici che sono associati a macchina virtuale vengono aggiornati.

Se si sono ancora verificato problemi RDP, è possibile [aprire una richiesta di assistenza](https://azure.microsoft.com/support/options/) o per saperne di [più dettagliati concetti di risoluzione dei problemi di RDP e le procedure](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-using-azure-powershell"></a>Problemi relativi all'utilizzo di PowerShell Azure
Se non è già fatto, [installare e configurare il più recente di Azure PowerShell](../powershell-install-configure.md).

Negli esempi seguenti utilizzano variabili, ad esempio `myResourceGroup`, `myVM`, e `myVMAccessExtension`. Sostituire i nomi di variabili e i percorsi con valori personalizzati.

> [AZURE.NOTE] Per reimpostare le credenziali dell'utente e la configurazione RDP, utilizzando il cmdlet di PowerShell [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) . Negli esempi seguenti `myVMAccessExtension` un nome che specificano come parte del processo. Se si è usato in precedenza il VMAccessAgent, è possibile ottenere il nome dell'estensione esistente usando `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` per controllare le proprietà della macchina virtuale. Per visualizzare il nome, esaminare la sezione 'Estensioni' l'output.

Dopo ogni passaggio sulla risoluzione dei problemi, provare a riconnettersi per la macchina virtuale. Se non è ancora possibile connettersi, procedere al passaggio successivo.

1. **Reimpostare la connessione RDP**. Questa procedura reimposta la configurazione RDP quando le connessioni Remote sono disabilitate o le regole di Windows Firewall bloccano RDP, ad esempio.

    Nell'esempio seguente Reimposta la connessione RDP in una macchina virtuale denominata `myVM` nel `WestUS` posizione e nel gruppo di risorse denominato `myResourceGroup`:

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```

2. **Le regole di verificare il gruppo di sicurezza di rete**. Questa procedura consente di verificare di avere una regola del gruppo di sicurezza di rete per consentire il traffico RDP. La porta predefinita per RDP è la porta TCP 3389. Una regola per consentire il traffico RDP potrebbe non essere creata automaticamente quando si crea la macchina virtuale.

    Prima di tutto assegnare tutti i dati di configurazione per il gruppo di sicurezza di rete per la `$rules` variabile. Nell'esempio seguente viene ottenuta tramite le informazioni sul gruppo di sicurezza di rete denominata `myNetworkSecurityGroup` nel gruppo di risorse denominato `myResourceGroup`:

    ```powershell
    $rules = Get-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```

    A questo punto, visualizzare le regole che vengono configurate per il gruppo di sicurezza di rete. Verificare l'esistenza di una regola per consentire la porta TCP 3389 per le connessioni in ingresso come indicato di seguito:

    ```powershell
    $rules.SecurityRules
    ```

    Nell'esempio seguente viene illustrata una regola di protezione valido che consenta il traffico RDP. È possibile vedere `Protocol`, `DestinationPortRange`, `Access`, e `Direction` siano configurati correttamente:

    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```

    Se non si dispone di una regola che consente il traffico RDP, [creare una regola gruppo di sicurezza di rete](virtual-machines-windows-nsg-quickstart-powershell.md). Consentire la porta TCP 3389.

3. **Reimpostare le credenziali dell'utente**. Questa procedura reimposta la password dell'account administrator locale che specificano quando si è sicuri di o hai dimenticato, le credenziali.

    Prima di tutto, specificare il nome utente e una nuova password assegnando le credenziali per il `$cred` variabile come indicato di seguito:

    ```powershell
    $cred=Get-Credential
    ```

    A questo punto, aggiornare le credenziali su di una macchina virtuale. Nell'esempio seguente aggiorna le credenziali in una macchina virtuale denominata `myVM` nel `WestUS` posizione e nel gruppo di risorse denominato `myResourceGroup`:

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```

4. **Riavviare la macchina virtuale**. Questa procedura può correggere eventuali problemi sottostante che presenta la macchina virtuale stesso.

    Nell'esempio seguente si riavvia la macchina virtuale denominata `myVM` nel gruppo di risorse denominato `myResourceGroup`:

    ```powershell
    Restart-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

5. **Ridistribuire la macchina virtuale**. Questa procedura ridistribuisce la macchina virtuale a un altro host all'interno di Azure per correggere i problemi di rete o qualsiasi piattaforma sottostante.

    Nell'esempio seguente ridistribuisce macchine Virtuali denominata `myVM` nel `WestUS` posizione e nel gruppo di risorse denominato `myResourceGroup`:

    ```powershell
    Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

Se si sono ancora verificato problemi RDP, è possibile [aprire una richiesta di assistenza](https://azure.microsoft.com/support/options/) o per saperne di [più dettagliati concetti di risoluzione dei problemi di RDP e le procedure](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Risolvere i problemi di macchine virtuali create con il modello di distribuzione classica

Dopo ogni passaggio sulla risoluzione dei problemi, provare a riconnettersi a macchina virtuale.

1. **Reimpostare la connessione RDP**. Questa procedura reimposta la configurazione RDP quando le connessioni Remote sono disabilitate o le regole di Windows Firewall bloccano RDP, ad esempio.

    Selezionare la macchina virtuale nel portale di Azure. Fare clic sul **... Ulteriori** , quindi fare clic su **Reimposta accesso remoto**:

    ![Reimpostare la configurazione RDP nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-rdp.png)

2.  **Servizi Cloud verificare i punti finali**. Questa procedura verifica presentano endpoint nei servizi Cloud per consentire il traffico RDP. La porta predefinita per RDP è la porta TCP 3389. Una regola per consentire il traffico RDP potrebbe non essere creata automaticamente quando si crea la macchina virtuale.

    Selezionare la macchina virtuale nel portale di Azure. Fare clic sul pulsante **endpoint** per visualizzare i punti finali attualmente configurati per la macchina virtuale. Verificare l'endpoint che consentano il traffico RDP porta TCP 3389.
    
    Nell'esempio seguente mostra i punti finali validi che consentano il traffico RDP:

    ![Verificare gli endpoint servizi Cloud nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)

    Se non si dispone di un estremo che consente il traffico RDP, [creare un endpoint servizi Cloud](virtual-machines-windows-classic-setup-endpoints.md). Consenti TCP alla porta privata 3389.

3. **Diagnostica avvio macchine Virtuali di revisione**. Questa procedura rivede i registri di console macchine Virtuali per determinare se la macchina virtuale segnala un problema. Macchine virtuali non tutti sono diagnostica avvio abilitata, in modo che questa procedura potrebbe essere facoltativa.
    
    Procedure di risoluzione dei problemi specifiche non rientrano nell'ambito di questo articolo, ma possono indicare un problema più ampio che influisce negativamente sulla connettività RDP. Per ulteriori informazioni sulla verifica dei registri console e schermata macchine Virtuali, vedere [Avvio diagnostica per macchine virtuali](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

4. **Verificare l'integrità di risorsa macchina virtuale**. Questa procedura verifica esistono problemi noti con la piattaforma Azure che potrebbe influire negativamente la connettività per la macchina virtuale.

    Selezionare la macchina virtuale nel portale di Azure. Scorrere in basso nel riquadro impostazioni alla sezione **supporto + risoluzione dei problemi** nella parte inferiore dell'elenco. Fare clic sul pulsante **Dell'integrità della risorsa** . Una macchina virtuale integro segnala come **disponibili**:

    ![Verificare l'integrità del risorsa macchine Virtuali nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-check-resource-health.png)

5. **Reimpostare le credenziali dell'utente**. Questa procedura reimposta la password dell'account administrator locale che specificano quando si è certi o dimenticato le credenziali.

    Selezionare la macchina virtuale nel portale di Azure. Scorrere in basso nel riquadro impostazioni alla sezione **supporto + risoluzione dei problemi** nella parte inferiore dell'elenco. Fare clic sul pulsante **Reimposta password** . Immettere il nome utente e una nuova password. Infine, fare clic sul pulsante **Salva** :

    ![Reimpostare le credenziali utente nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-password.png)

6. **Riavviare la macchina virtuale**. Questa procedura può correggere eventuali problemi sottostante che presenta la macchina virtuale stesso.

    Selezionare la macchina virtuale nel portale di Azure e fare clic sulla scheda **Panoramica** . Fare clic sul pulsante **riavviare** :

    ![Riavviare la macchina virtuale nel portale di Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-restart-vm.png)
    
Se si sono ancora verificato problemi RDP, è possibile [aprire una richiesta di assistenza](https://azure.microsoft.com/support/options/) o per saperne di [più dettagliati concetti di risoluzione dei problemi di RDP e le procedure](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-specific-rdp-errors"></a>Risolvere i problemi di errori specifici nelle RDP
Quando si tenta di connettere la macchina virtuale tramite RDP, è possibile riscontrare un messaggio di errore specifico. Di seguito sono i messaggi di errore più comuni:

- [La sessione remota è stata disconnessa perché non sono presenti server di licenze Desktop remoto disponibile per fornire una licenza](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdplicense).
- [Desktop remoto non è possibile trovare il computer "nome"](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpname).
- Si è verificato un errore [un tipo di autenticazione. Autorità di protezione locale non può essere contattato](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpauth).
- [Errore di protezione di Windows: le credenziali non risolvono](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#wincred).
- [In questo computer non riesce a connettersi al computer remoto](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpconnect).


## <a name="additional-resources"></a>Risorse aggiuntive
Se nessuno di questi errori si sono verificati e non è possibile connettersi a supporto virtuale tramite Desktop remoto, informazioni dettagliate [risoluzione dei problemi di Guida per il Desktop remoto](virtual-machines-windows-detailed-troubleshoot-rdp.md).

- [Pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)
- I problemi di accesso alle applicazioni in esecuzione in una macchina virtuale, vedere [risoluzione dei problemi di accesso a un'applicazione in esecuzione in una macchina virtuale Azure](virtual-machines-linux-troubleshoot-app-connection.md).
- Se si verificano problemi di utilizzo Secure Shell (SSH) per connettersi a un VM Linux in Azure, vedere [risolvere i problemi di SSH connessioni a una macchina virtuale Linux in Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).
