<properties
    pageTitle="Installare un controller di dominio Active Directory replica in Azure | Microsoft Azure"
    description="Esercitazione in cui viene illustrato come installare un controller di dominio da una foresta di Active Directory locale in un computer virtuale Azure."
    services="virtual-network"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="virtual-network"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="curtand"/>


# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Installare un controller di dominio Active Directory replica in una rete virtuale Azure

In questo argomento viene illustrato come installare controller di dominio aggiuntivi (noto anche come replica controller di dominio) per un dominio di Active Directory locale in Azure macchine () in una rete virtuale Azure.

Può inoltre essere utile in questi argomenti correlati:

-  È possibile installare una nuova foresta di Active Directory in una rete virtuale Azure. Per questi passaggi, vedere [installare un nuovo insieme di strutture in una rete virtuale Azure Active Directory](../active-directory/active-directory-new-forest-virtual-machine.md).
-  Per linee guida sull'installazione di servizi di dominio Active Directory (AD DS) in una rete virtuale Azure, vedere [indicazioni per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).


## <a name="scenario-diagram"></a>Diagramma scenario

In questo scenario, gli utenti esterni devono poter accedere applicazioni da eseguire sui server di dominio. Macchine virtuali che eseguono Server delle applicazioni e controller di dominio replica sono installate in una rete virtuale Azure. La rete virtuale possa connessi alla rete locale tramite una connessione [VPN da sito](../vpn-gateway/vpn-gateway-site-to-site-create.md) , come illustrato nella figura seguente, oppure è possibile utilizzare [ExpressRoute](../expressroute/expressroute-locations-providers.md) per una connessione più veloce.

I server delle applicazioni e controller di dominio vengono distribuiti all'interno di servizi cloud separate per distribuire l'elaborazione di calcolo e di [set di disponibilità](../virtual-machines/virtual-machines-windows-manage-availability.md) per migliore tolleranza di errore.
Controller di dominio replicare tra loro e con controller di dominio locale utilizzando replica di Active Directory. Gli strumenti di sincronizzazione non sono necessarie.

![Controller di dominio Active Directory replica pf diagramma un vnet Azure][1]

## <a name="create-an-active-directory-site-for-the-azure-virtual-network"></a>Creare un sito di Active Directory per la rete virtuale Azure

È consigliabile creare un sito di Active Directory che rappresenta l'area di rete corrispondente alla rete virtuale. Che consente di ottimizzare l'autenticazione, replica e altre operazioni di posizione controller di dominio. La procedura seguente viene illustrato come creare un sito e per ulteriori informazioni, vedere [aggiunta di un nuovo sito](https://technet.microsoft.com/library/cc781496.aspx).

1. Aprire siti e servizi Active Directory: **Gestione Server** > **Strumenti** > **siti e servizi Active Directory**.
2. Creare un sito per rappresentare l'area geografica in cui è stato creato un virtuali Azure: fare clic su **siti** > **azione** > **nuovo sito** > digitare il nome del nuovo sito, ad esempio Ovest di Azure Contattaci > selezionare un collegamento di sito > **OK**.
3. Creare una subnet e associare il nuovo sito: fare doppio clic su **siti** > rapida **subnet** > **nuova subnet** > digitare l'intervallo di indirizzi IP di rete virtuale (ad esempio 10.1.0.0/16 nel diagramma scenario) > selezionare il nuovo sito di Azure > **OK**.

## <a name="create-an-azure-virtual-network"></a>Creare una rete virtuale Azure

1. Nel [portale classica Azure](https://manage.windowsazure.com), fare clic su **Nuovo** > **Servizi di rete** > **Virtuali** > **Personalizzato creare** e usare i seguenti valori per completare la procedura guidata.

    Questa pagina della procedura guidata...  | Specificare questi valori
    ------------- | -------------
    **Dettagli sulla rete virtuale**  | <p>Nome: Digitare un nome per la rete virtuale, ad esempio WestUSVNet.</p><p>L'area geografica: Selezionare il paese più vicino.</p>
    **Connettività VPN e DNS**  | <p>I server DNS: Specificare il nome e indirizzo IP di uno o più server DNS locali.</p><p>Connettività: Selezionare **Configura una connessione VPN da sito**.</p><p>Rete locale: specificare una nuova rete locale.</p><p>Se si utilizza ExpressRoute anziché una connessione VPN, vedere [configurare una connessione ExpressRoute tramite un Provider di Exchange](../expressroute/expressroute-locations-providers.md).</p>
    **Connettività al sito**  | <p>Nome: Digitare un nome per la rete locale.</p><p>Indirizzo IP dispositivo VPN: specificare l'indirizzo IP pubblico del dispositivo che verrà connesso alla rete virtuale. Non è possibile individuare il dispositivo VPN dietro al NAT.</p><p>Indirizzo: Specificare gli intervalli di indirizzi per la rete locale (ad esempio 192.168.0.0/16 nel diagramma scenario).</p>
    **Spazi di indirizzi virtuali**  | <p>Spazio di indirizzi: Specificare l'intervallo di indirizzi IP per macchine virtuali che si desidera eseguire la rete virtuale Azure (ad esempio 10.1.0.0/16 nel diagramma scenario). L'intervallo di indirizzo non può coincidere con gli intervalli di indirizzi della rete locale.</p><p>Subnet: Specificare un nome e indirizzo subnet per il server di applicazione (ad esempio front-end, 10.1.1.0/24) e dei controller di dominio (ad esempio back-end, 10.1.2.0/24).</p><p>Fare clic su **Aggiungi subnet gateway**.</p>

2. Punto, configurare il gateway di rete virtuale per creare una connessione VPN da sito protetta. Vedere [configurare un Gateway di rete virtuale](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) per le istruzioni.
3. Creare la connessione VPN da sito tra la nuova rete virtuale e i dispositivi VPN locale. Vedere [configurare un Gateway di rete virtuale](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) per le istruzioni.


## <a name="create-azure-vms-for-the-dc-roles"></a>Creare macchine virtuali di Azure per i ruoli di controller di dominio

Ripetere i passaggi seguenti per creare macchine virtuali per ospitare il ruolo di controller di dominio in base alle esigenze. È necessario distribuire almeno due controller di dominio virtuale per fornire ridondanza e tolleranza di errore. Se la rete virtuale Azure include almeno due controller configurati in modo analogo (vale a dire siano entrambi cataloghi Esegui DNS server, e non contiene qualsiasi ruolo e così via) quindi posizionare le macchine virtuali che eseguono quelli controller di dominio in una disponibilità impostare per migliore tolleranza di errore.
Per creare le macchine virtuali mediante Windows PowerShell anziché l'interfaccia utente, vedere [Usare PowerShell Azure per creare e preconfigurare macchine virtuali basato su Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

1. Nel [portale classica Azure](https://manage.windowsazure.com), fare clic su **Nuovo** > **calcolare** > **macchina virtuale** > **Dalla raccolta**. Usare i valori seguenti per completare la procedura guidata. Accettare il valore predefinito per un'impostazione a meno che un altro valore suggerito o necessario.

    Questa pagina della procedura guidata...  | Specificare questi valori
    ------------- | -------------
    **Scegliere un'immagine**  | Data Center di Windows Server 2012 R2
    **Configurazione della macchina virtuale**  | <p>Nome macchina virtuale: Digitare il nome di una singola etichetta (ad esempio AzureDC1).</p><p>Nome utente: Digitare il nome di un utente. L'utente sarà un membro del gruppo Administrators locale nella macchina virtuale. È necessario questo nome per accedere a macchina virtuale per la prima volta. L'account predefinito denominato Administrator non funzionerà.</p><p>Nuova Password/conferma: Digitare una password</p>
    **Configurazione della macchina virtuale**  | <p>Servizio cloud: Scegliere di <b>creare un nuovo servizio cloud</b> per la macchina virtuale prima e selezionare lo stesso nome servizio cloud quando si creano più macchine virtuali contenenti il ruolo di controller di dominio.</p><p>Nome DNS servizio cloud: Specificare un nome univoco globale</p><p>Regione/gruppo affinità/rete virtuale: specificare il nome di rete virtuale (ad esempio WestUSVNet).</p><p>Account di archiviazione: Scegliere <b>Usa un account generato automaticamente lo spazio di archiviazione</b> per la macchina virtuale prima e quindi selezionare lo stesso nome account lo spazio di archiviazione quando si creano più macchine virtuali contenenti il ruolo di controller di dominio.</p><p>Set di disponibilità: Scegliere di <b>creare un set di disponibilità</b>.</p><p>Disponibilità imposta nome: digitare un nome per la disponibilità set quando si crea la macchina virtuale prima e quindi selezionare stesso nome quando si creano più macchine virtuali.</p>
    **Configurazione della macchina virtuale**  | <p>Selezionare <b>installare l'agente di macchine Virtuali</b> e le estensioni che necessarie.</p>
2. Allegare un disco a ogni macchina virtuale che verrà eseguito il ruolo di server controller di dominio. Il disco è necessario archiviare il database di Active Directory, i registri e SYSVOL. Specificare le dimensioni del disco rigido (ad esempio 10 GB) e lasciare la **Host Cache preferenze** impostata su **Nessuno**. Per istruzioni, vedere [come collegare un disco di dati a una macchina virtuale di Windows](../virtual-machines/virtual-machines-windows-classic-attach-disk.md).
3. Dopo che accede alla macchina virtuale, aprire **Server Manager** > **File e servizi di archiviazione** per creare un volume sul disco con NTFS.
4. Prenotare un indirizzo IP statico per macchine virtuali che eseguiranno il ruolo di controller di dominio. Per prenotare un indirizzo IP statico, scaricare l'installazione guidata piattaforma Web Microsoft e [installare Azure PowerShell](../powershell-install-configure.md) ed eseguire il cmdlet Set-AzureStaticVNetIP. Per esempio:

    ' AzureVM get - nome AzureDC1-nome AzureDC1 | Set AzureStaticVNetIP - indirizzo IP 10.0.0.4 | Aggiornamento AzureVM

Per ulteriori informazioni sull'impostazione di un indirizzo IP statico, vedere [configurare un indirizzo IP statico interno per una macchina virtuale](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Installare Active Directory in macchine virtuali di Azure

Accedere a una macchina virtuale e verificare che è disponibile la connettività attraverso la connessione di rete VPN o ExpressRoute da sito alle risorse sulla rete locale. Quindi installare Active Directory in macchine virtuali di Azure. È possibile utilizzare stesso processo che consente di installare un controller di dominio aggiuntivi sulla rete locale (interfaccia utente, Windows PowerShell o un file di risposta). Come si installa Active Directory, assicurarsi di specificare il nuovo volume relativa al percorso del database Active Directory, i registri e SYSVOL. Se è necessario un aggiornamento su installazione di Active Directory, vedere [Installare servizi di dominio (livello 100)](https://technet.microsoft.com/library/hh472162.aspx) o [installare un Controller di dominio Replica Windows Server 2012 in un dominio esistente (livello 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Riconfigurare i server DNS per la rete virtuale

1. Nel [portale classica Azure](https://manage.windowsazure.com), fare clic sul nome della rete virtuale e quindi fare clic sulla scheda **Configura** di [riconfigurare gli indirizzi IP di server DNS per la rete virtuale](../virtual-network/virtual-networks-manage-dns-in-vnet.md) per utilizzare gli indirizzi IP statici assegnati alla replica di controller di dominio anziché gli indirizzi IP dei server DNS locali.

2. Per garantire che tutte le replica di macchine virtuali controller di dominio della rete virtuale siano configurati con per usare i server DNS nella rete virtuale, fare clic su **macchine virtuali**, fare clic sulla colonna stato per ogni macchina virtuale e quindi scegliere **Riavvia**. Attendere che la macchina virtuale Mostra stato **in esecuzione** prima di provare a eseguire l'accesso al suo interno.

## <a name="create-vms-for-application-servers"></a>Creare macchine virtuali per server delle applicazioni

1. Ripetere i passaggi seguenti per creare macchine virtuali per l'esecuzione come server delle applicazioni. Accettare il valore predefinito per un'impostazione a meno che un altro valore suggerito o necessario.

    Questa pagina della procedura guidata...  | Specificare questi valori
    ------------- | -------------
    **Scegliere un'immagine**  | Data Center di Windows Server 2012 R2
    **Configurazione della macchina virtuale**  | <p>Nome macchina virtuale: Digitare il nome di una singola etichetta (ad esempio AppServer1).</p><p>Nome utente: Digitare il nome di un utente. L'utente sarà un membro del gruppo Administrators locale nella macchina virtuale. È necessario questo nome per accedere a macchina virtuale per la prima volta. L'account predefinito denominato Administrator non funzionerà.</p><p>Nuova Password/conferma: Digitare una password</p>
    **Configurazione della macchina virtuale**  | <p>Servizio cloud: Scegliere di **creare un nuovo servizio cloud** per la macchina virtuale prima e selezionare lo stesso nome servizio cloud quando si creano più macchine virtuali contenenti dell'applicazione.</p><p>Nome DNS servizio cloud: Specificare un nome univoco globale</p><p>Regione/gruppo affinità/rete virtuale: specificare il nome di rete virtuale (ad esempio WestUSVNet).</p><p>Account di archiviazione: Scegliere **Usa un account generato automaticamente lo spazio di archiviazione** per la macchina virtuale prima e quindi selezionare lo stesso nome account lo spazio di archiviazione quando si creano più macchine virtuali contenenti dell'applicazione.</p><p>Set di disponibilità: Scegliere di **creare un set di disponibilità**.</p><p>Disponibilità imposta nome: digitare un nome per la disponibilità set quando si crea la macchina virtuale prima e quindi selezionare stesso nome quando si creano più macchine virtuali.</p>
    **Configurazione della macchina virtuale**  | <p>Selezionare <b>installare l'agente di macchine Virtuali</b> e le estensioni che necessarie.</p>

2. Dopo ogni macchina virtuale viene eseguito il provisioning, accedere e partecipare al dominio. In **Server Manager**, fare clic su **Server locali** > **gruppo di lavoro** > **Cambia...** Selezionare il **dominio** e digitare il nome del dominio locale. Specificare le credenziali di un utente di dominio e quindi riavviare la macchina virtuale per completare l'aggiunta al dominio.

Per creare le macchine virtuali mediante Windows PowerShell anziché l'interfaccia utente, vedere [Usare PowerShell Azure per creare e preconfigurare macchine virtuali basato su Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

Per ulteriori informazioni sull'uso di Windows PowerShell, vedere [Guida introduttiva di Azure cmdlet](https://msdn.microsoft.com/library/azure/jj554332.aspx) e [Di riferimento sui Cmdlet Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx).

## <a name="additional-resources"></a>Risorse aggiuntive

-  [Linee guida per la distribuzione di Windows Server Active Directory in macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
-  [Come caricare controller di dominio locale Hyper-V esistenti in Azure tramite PowerShell Azure](http://support.microsoft.com/kb/2904015)
-  [Installare una nuova foresta di Active Directory in una rete virtuale Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
-  [Azure virtuali](../virtual-network/virtual-networks-overview.md)
-  [Microsoft Azure IaaS professionisti IT: nozioni fondamentali (01) macchina virtuale](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IaaS professionisti IT: (05) reti virtuali creazione e alla connettività tra locale](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [PowerShell Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Cmdlet per la gestione di Azure](https://msdn.microsoft.com/library/azure/jj152841)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
