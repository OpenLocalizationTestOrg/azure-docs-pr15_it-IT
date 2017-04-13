<properties
    pageTitle="Installare un insieme di strutture di Active Directory in una rete virtuale Azure | Microsoft Azure"
    description="Esercitazione in cui viene illustrato come creare una nuova foresta di Active Directory in una macchine () in una rete virtuale Azure."
    services="active-directory, virtual-network"
    keywords="Active directory virtuali, strutture di active directory di installazione, i video di azure active directory "
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    tags=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="install-a-new-active-directory-forest-on-an-azure-virtual-network"></a>Installare una nuova foresta di Active Directory in una rete virtuale Azure

In questo argomento viene illustrato come creare un nuovo ambiente di Windows Server Active Directory in una rete virtuale Azure su una macchine () in una [rete virtuale Azure](../virtual-network/virtual-networks-overview.md). In questo caso, la rete virtuale Azure non è connesso a una rete locale.

Può inoltre essere utile in questi argomenti correlati:

- Per un video che illustra la procedura seguente, vedere [come installare una nuova foresta di Active Directory in una rete virtuale Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
- È possibile [configurare una connessione VPN da sito](../vpn-gateway/vpn-gateway-site-to-site-create.md) e quindi installare una nuova foresta o estendere un insieme di strutture locale a una rete virtuale Azure. Per questi passaggi, vedere [installare un Controller di dominio Replica Active Directory in una rete virtuale Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md).
-  Per linee guida sull'installazione di servizi di dominio Active Directory (AD DS) in una rete virtuale Azure, vedere [indicazioni per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Scenario relativo al diagramma

In questo scenario, gli utenti esterni devono poter accedere applicazioni da eseguire sui server di dominio. Macchine virtuali che eseguono Server delle applicazioni e macchine virtuali che eseguono controller di dominio sono installate installato nel proprio servizio cloud in una rete virtuale Azure. Sono inoltre incluse all'interno di una disponibilità impostare per migliore tolleranza di errore.

![Insieme di strutture Active Directory in un computer virtuale virtuali Azure ][1] 7
## <a name="how-does-this-differ-from-on-premises"></a>Come la seguente differisce dalla locale?

Non c'è molta differenza tra l'installazione di un controller di dominio in Azure e locale. Nella tabella seguente sono elencate le principali differenze.

Per configurare...  | Locale  | Azure virtuali
------------- | -------------  | ------------
**Indirizzo IP del controller di dominio**  | Assegnare indirizzo IP statico nelle proprietà della scheda rete   | Eseguire il cmdlet Set-AzureStaticVNetIP per assegnare un indirizzo IP statico
**Risoluzione di client DNS**  | Impostate preferite e DNS alternativo indirizzo del server di rete scheda dei membri del dominio   | Impostare l'indirizzo del server DNS la proprietà virtuali
**Archiviazione di database di Active Directory**  | Se si desidera modificare la posizione di archiviazione predefinito da C:\  | È necessario modificare la posizione di archiviazione predefinito da C:\



## <a name="create-an-azure-virtual-network"></a>Creare una rete virtuale Azure

1. Accedere al portale di classica Azure.
2. Creare una rete virtuale. Fare clic su **reti** > **Crea una rete virtuale**. Usare i valori nella tabella seguente per completare la procedura guidata.

    Questa pagina della procedura guidata...  | Specificare questi valori
    ------------- | -------------
    **Dettagli sulla rete virtuale**  | <p>Nome: Immettere un nome per la rete virtuale</p><p>L'area geografica: Scegliere la provincia più vicina</p>
    **VPN e DNS**  | <p>Lasciare vuoto il campo server DNS</p><p>Non selezionare l'opzione VPN</p>
    **Spazi di indirizzi virtuali**  | <p>Nome subnet: immettere un nome per la subnet</p><p>IP iniziale: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



## <a name="create-vms-to-run-the-domain-controller-and-dns-server-roles"></a>Creare macchine virtuali per eseguire controller di dominio e ruoli dei server DNS

Ripetere i passaggi seguenti per creare macchine virtuali per ospitare il ruolo di controller di dominio in base alle esigenze. È necessario distribuire almeno due controller di dominio virtuale per fornire ridondanza e tolleranza di errore. Se la rete virtuale Azure include almeno due controller configurati in modo analogo (vale a dire siano entrambi cataloghi Esegui DNS server, e non contiene qualsiasi ruolo e così via) quindi posizionare le macchine virtuali che eseguono quelli controller di dominio in una disponibilità impostare per migliore tolleranza di errore.

Per creare le macchine virtuali mediante Windows PowerShell anziché l'interfaccia utente, vedere [Usare PowerShell Azure per creare e preconfigurare macchine virtuali basato su Windows](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

1. Nel portale classico, fare clic su **Nuovo** > **calcolare** > **macchina virtuale** > **Dalla raccolta**. Usare i valori seguenti per completare la procedura guidata. Accettare il valore predefinito per un'impostazione a meno che un altro valore suggerito o necessario.

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

## <a name="install-windows-server-active-directory"></a>Installare Windows Server Active Directory

Utilizzare la stessa routine per [installare Active Directory](https://technet.microsoft.com/library/jj574166.aspx) utilizzare locale (ovvero, è possibile utilizzare l'interfaccia utente, un file di risposta o di Windows PowerShell). È necessario specificare le credenziali di amministratore per installare una nuova foresta. Per specificare la posizione per il database di Active Directory, i registri e SYSVOL, modificare la posizione di archiviazione predefinito da unità del sistema operativo sul disco dati aggiuntivi che si era connessi macchina virtuale.

Al termine dell'installazione di controller di dominio, connettersi di nuovo la macchina virtuale e accedere al controller di dominio. È necessario ricordare di specificare le credenziali di dominio.

## <a name="reset-the-dns-server-for-the-azure-virtual-network"></a>Reimpostare il server DNS per la rete virtuale Azure

1. Ripristinare le impostazioni di inoltro DNS nel nuovo server controller di dominio/DNS.
  1. In Server Manager, fare clic su **Strumenti** > **DNS**.
  2. In **Gestore DNS**, pulsante destro del mouse sul nome del server DNS e scegliere **proprietà**.
  3. Nella scheda **server d'inoltro** fare clic su indirizzo IP del server di inoltro e fare clic su **Modifica**.  Selezionare l'indirizzo IP e fare clic su **Elimina**.
  4. Fare clic su **OK** per chiudere l'editor, quindi **Ok** per chiudere le proprietà del server DNS.
2. Aggiornare le impostazioni del server DNS per la rete virtuale.
  1. Fare clic su **Reti virtuali** > fare doppio clic su virtuali creato > **Configura** > **server DNS**, digitare il nome e l'indirizzo DIP di uno dei macchine virtuali che esegue il ruolo di server DNS/controller di dominio e fare clic su **Salva**.
  2. Selezionare la macchina virtuale e fare clic su **Riavvia** per attivare la macchina virtuale per configurare le impostazioni del Risolutore DNS con l'indirizzo IP del nuovo server DNS.


## <a name="create-vms-for-domain-members"></a>Creare macchine virtuali per i membri del dominio

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


## <a name="see-also"></a>Vedere anche

-  [Come installare una nuova foresta di Active Directory in una rete virtuale Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
-  [Linee guida per la distribuzione di Windows Server Active Directory in macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Configurare una connessione VPN da sito](../vpn-gateway/vpn-gateway-site-to-site-create.md)
-  [Installare un Controller di dominio Replica Active Directory in una rete virtuale Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)
-  [Microsoft Azure IaaS professionisti IT: nozioni fondamentali (01) macchina virtuale](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IaaS professionisti IT: (05) reti virtuali creazione e alla connettività tra locale](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Panoramica di rete virtuale](../virtual-network/virtual-networks-overview.md)
-  [Come installare e configurare PowerShell Azure](../powershell-install-configure.md)
-  [PowerShell Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Riferimento sui Cmdlet di Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)
-  [Impostare l'indirizzo IP statico macchine Virtuali di Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
-  [Come assegnare indirizzo IP statico a macchine Virtuali di Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
-  [Installare una nuova foresta di Active Directory](https://technet.microsoft.com/library/jj574166.aspx)
-  [Introduzione alla virtualizzazione di servizi (AD DS) di dominio Active Directory (livello 100)](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png
