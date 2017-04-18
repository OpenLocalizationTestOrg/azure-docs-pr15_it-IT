<properties
    pageTitle="Configurare sempre nel gruppo di disponibilità in macchine Virtuali di Azure - classico"
    description="Creare un gruppo di disponibilità sempre attiva con macchine virtuali di Azure. In questa esercitazione principalmente utilizza l'interfaccia utente e strumenti invece di scripting."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="mikeray" />

# <a name="configure-always-on-availability-group-in-azure-vm---classic"></a>Configurare sempre nel gruppo di disponibilità in macchine Virtuali di Azure - classico

> [AZURE.SELECTOR]
- [Manager delle risorse: modello](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Manager delle risorse: manuale](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Classica: interfaccia utente](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Classica: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


In questa esercitazione-to-end viene illustrato come implementare gruppi di disponibilità con SQL Server sempre in esecuzione in macchine virtuali di Azure.

Al termine dell'esercitazione, la soluzione SQL Server sempre attiva in Azure è costituita dagli elementi seguenti:

- Una rete virtuale che contiene più subnet, tra cui front-end e una subnet back-end

- Un controller di dominio con un dominio di Active Directory (AD)

- Due macchine virtuali Server SQL distribuito alla subnet back-end e al dominio di Active Directory

- Un cluster WSFC 3 nodi con il modello di base della maggior parte nodo

- Un gruppo di disponibilità con due icona del commit di un database di disponibilità

Nella figura seguente è una rappresentazione grafica della soluzione.

![Architettura di laboratorio di test per AG in Azure](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

Si noti che questa è una possibile configurazione. Ad esempio, è possibile ridurre il numero di macchine virtuali per un gruppo di replica di due disponibilità per risparmiare in ore di elaborazione di Azure utilizzando controller di dominio come il riferimento di condivisione file di base in un cluster WSFC nodo 2. Questo metodo riduce il conteggio di macchine Virtuali di una parte della configurazione precedente.

In questa esercitazione si presuppone che le operazioni seguenti:

- Si dispone già di un account Azure.

- Si sa come effettuare il provisioning di una macchina virtuale Server SQL classica dalla raccolta macchina virtuale utilizzando l'interfaccia grafica.

- Si dispone già di dubbio gruppi sempre sulla disponibilità. Per ulteriori informazioni, vedere [Sempre nella disponibilità gruppi (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Se si è interessati in utilizzo sempre sulla disponibilità dei gruppi con SharePoint, vedere anche [Configurare SQL Server 2012 sempre via disponibilità gruppi per SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

## <a name="create-the-virtual-network-and-domain-controller-server"></a>Creare la rete virtuale e Server Controller di dominio

Iniziare con un nuovo account di valutazione di Azure. Dopo aver completato la configurazione dell'account, dovrebbe essere nella schermata iniziale del portale di classica Azure.

1. Fare clic sul pulsante **Nuovo** nell'angolo inferiore sinistro della pagina, come illustrato di seguito.

    ![Fare clic su nuovo nel portale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)

1. Fare clic su **Servizi di rete**, quindi fare clic su **Virtuali** e quindi fare clic su **Crea personalizzato**, come illustrato di seguito.

    ![Creare virtuali](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)

1. Nella finestra di dialogo **Crea rete virtuale A** creare una nuova rete virtuale spostandosi le pagine con le impostazioni seguenti. 

  	|Pagina|Impostazioni|
|---|---|
|Dettagli sulla rete virtuale|**NOME = ContosoNET**<br/>**AREA = US ovest**|
|I server DNS e la connettività VPN|Nessuno|
|Spazi di indirizzi virtuali|Nella figura riportata di seguito vengono illustrate le impostazioni: ![Creare virtuali](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png)|

1. Creare quindi macchine Virtuali si userà il dominio controller di. Fare clic su **Nuovo** , quindi **calcolare**, quindi **macchina virtuale**e quindi **Dalla raccolta**, come illustrato di seguito.

    ![Creare una macchina virtuale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)

1. Nella finestra di dialogo **Crea una macchina virtuale** configurare una nuova macchina virtuale spostandosi le pagine con le impostazioni seguenti. 

  	|Pagina|Impostazioni|
|---|---|
|Selezionare il sistema operativo macchina virtuale|Data Center di Windows Server 2012 R2|
|Configurazione della macchina virtuale|**Versione data** = (ultima versione)<br/>**Nome macchina virtuale** = ContosoDC<br/>**Livello** = STANDARD<br/>**Dimensione** = A2 (2 core)<br/>**Nuovo nome utente** = AzureAdmin<br/>**Nuova PASSWORD** = Contoso! 000<br/>**Conferma** = Contoso! 000|
|Configurazione della macchina virtuale|**Servizio CLOUD** = crea un nuovo servizio cloud<br/>**Nome DNS del servizio CLOUD** = un nome di un servizio cloud univoci<br/>**Nome DNS** = un nome univoco (ex: ContosoDC123)<br/>**Regione/gruppo AFFINITÀ/virtuale di rete** = ContosoNET<br/>**Subnet rete virtuale** = Back(10.10.2.0/24)<br/>**ACCOUNT di archiviazione** = utilizza un account generato automaticamente lo spazio di archiviazione<br/>**Imposta la disponibilità** = (nessuno)|
|Opzioni di macchina virtuale|Utilizzare le impostazioni predefinite|

Dopo aver configurato la nuova macchina virtuale, attendere che la macchina virtuale da provsioned. Questo processo richiede un certo tempo per completare e se si fa clic sulla scheda **macchina virtuale** nel portale di classica Azure, è possibile vedere Stati ciclo ContosoDC partire **(Provisioning)** **interrotto**, **iniziale**, **esecuzione (Provisioning)**e infine **in esecuzione**.

Il server controller di dominio a questo punto è configurato correttamente. Punto, configurare il dominio di Active Directory sul server controller di dominio.

## <a name="configure-the-domain-controller"></a>Configurare il Controller di dominio

Nei passaggi seguenti configurare la macchina ContosoDC come controller di dominio per corp.contoso.com.

1. Nel portale, selezionare la macchina **ContosoDC** . Nella scheda **Dashboard** , fare clic su **Connetti** per aprire un file RDP per l'accesso desktop remoto.

    ![Connettersi al computer Vritual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)

1. Accedere con la configurazione account di amministratore (**\AzureAdmin**) e la password (**Contoso! 000**).

1. Per impostazione predefinita, verrà visualizzato nel dashboard di **Server Manager** .

1. Fare clic sul collegamento **Aggiungi ruoli e funzionalità** nel dashboard.

    ![Esplora server aggiungere ruoli](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)

1. Selezionare **Avanti** finché non viene visualizzato nella sezione **Ruoli del Server** .

1. Selezionare i ruoli di **Servizi di dominio Active Directory** e **Al Server DNS** . Quando richiesto, aggiungere ulteriori funzionalità richiesti da questi ruoli.

    >[AZURE.NOTE] Verrà visualizzato un'avviso che non esiste alcun indirizzo IP statico di convalida. Se si sta verificando la configurazione, fare clic su Continua. Per scenari di produzione [usare PowerShell per impostare l'indirizzo IP del computer del controller di dominio](./virtual-network/virtual-networks-reserved-private-ip.md).

    ![Finestra di dialogo ruoli aggiunta](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)

1. Fino a raggiungere la sezione di **Conferma** , fare clic su **Avanti** . Selezionare la casella di controllo **automaticamente se richiesto, riavviare il server di destinazione** .

1. Fare clic su **Installa**.

1. Dopo la funzionalità di completamento dell'installazione, tornare al dashboard di **Server Manager** .

1. Selezionare l'opzione nuovo **Active Directory** nel riquadro sinistro.

1. Fare clic sul collegamento **ulteriori** sulla barra di avviso giallo.

    ![Finestra di dialogo directory Active Directory in macchine Virtuali di Server DNS](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)

1. Nella colonna **azione** della finestra di dialogo **Tutti i dettagli attività Server** , fare clic su **Alza di livello il server a controller di dominio**.

1. **Guidata configurazione di servizi di dominio di Active Directory**, utilizzare i valori seguenti:

  	|Pagina|Impostazione|
|---|---|
|Configurazione di distribuzione|**Aggiungere una nuova foresta** = selezionato<br/>**Nome di dominio radice** = corp.contoso.com|
|Opzioni Controller di dominio|**Password** = Contoso! 000<br/>**Confermare la Password** = Contoso! 000|

1. Fare clic su **Avanti** per passare tra le altre pagine della procedura guidata. Nella pagina **Controllo prerequisiti** verificare visualizzato il messaggio seguente: **stati soddisfatti tutti i controlli di prerequisiti**. Si noti che è necessario esaminare i messaggi di avviso applicabile, ma è possibile continuare con l'installazione.

1. Fare clic su **Installa**. La macchina virtuale **ContosoDC** verrà automaticamente riavviato.

## <a name="configure-domain-accounts"></a>Configurare gli account di dominio

Passaggi successivi configurare gli account Active Directory (AD) per successivi utilizzi.

1. Accedere al computer **ContosoDC** .

1. In **Server Manager** selezionare **Strumenti** e quindi fare clic su **Centro amministrativo di Active Directory**.

    ![Centro di amministrazione di Active Directory](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)

1. Nel **Centro di amministrazione di Active Directory** selezionare **corp (locale)** dal riquadro di sinistra.

1. Nel riquadro **attività** destro, selezionare **Nuovo** e quindi fare clic su **utente**. Usare le impostazioni seguenti:

  	|Impostazione|Valore|
|---|---|
|**Nome**|Installare|
|**Utente SamAccountName**|Installare|
|**Password**|Contoso! 000|
|**Confermare la password**|Contoso! 000|
|**Altre opzioni di password**|Selezionata|
|**Nessuna scadenza password**|Selezionata|

1. Fare clic su **OK** per creare l'utente **installare** . Questo account verrà utilizzato per configurare del cluster e il gruppo di disponibilità.

1. Creare due altri utenti con gli stessi passaggi: **CORP\SQLSvc1** e **CORP\SQLSvc2**. Questi account verranno utilizzati per le istanze di SQL Server. È necessario assegnare le autorizzazioni necessarie per la configurazione di Windows servizio Failover cluster (WSFC) **CORP\Install** .

1. Nel **Centro di amministrazione di Active Directory**, selezionare **corp (locale)** nel riquadro sinistro. Nel riquadro **attività** destra, quindi scegliere **proprietà**.

    ![Proprietà CORP utente](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)

1. Selezionare **le estensioni**e quindi fare clic sul pulsante **Avanzate** nella scheda **sicurezza** .

1. Nella finestra di dialogo **Impostazioni di sicurezza avanzate per corp** . Fare clic su **Aggiungi**.

1. Fare clic su **Seleziona un'entità**. Cercare **CORP\Install**. Fare clic su **OK**.

1. Selezionare le autorizzazioni di **lettura tutte le proprietà** e **Crea Computer oggetti** .

    ![Autorizzazioni utente corp](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)

1. Fare clic su **OK**e quindi fare clic su **OK** . Chiudere la finestra Proprietà corp.

Dopo aver completato la configurazione di Active Directory e gli oggetti utente, creare macchine virtuali di tre SQL Server e per il dominio, aggiungere i.

## <a name="create-the-sql-server-vms"></a>Creare macchine virtuali di Server SQL

Creare tre macchine virtuali, tra cui un nodo WSFC e macchine virtuali di due SQL Server. Per creare ognuna delle macchine virtuali, tornare al portale di classica Azure fare clic su **Nuovo**, **calcolare**, **macchina virtuale**e quindi **Dalla raccolta**. Quindi utilizzare i modelli nella tabella seguente per creare le macchine virtuali.

|Pagina|VM1|VM2|VM3|
|---|---|---|---|
|Selezionare il sistema operativo macchina virtuale|**Data Center di Windows Server 2012 R2**|**SQL Server 2014 RTM Enterprise**|**SQL Server 2014 RTM Enterprise**|
|Configurazione della macchina virtuale|**Versione data** = (ultima versione)<br/>**Nome macchina virtuale** = ContosoWSFCNode<br/>**Livello** = STANDARD<br/>**Dimensione** = A2 (2 core)<br/>**Nuovo nome utente** = AzureAdmin<br/>**Nuova PASSWORD** = Contoso! 000<br/>**Conferma** = Contoso! 000|**Versione data** = (ultima versione)<br/>**Nome macchina virtuale** = ContosoSQL1<br/>**Livello** = STANDARD<br/>**Dimensione** = A3 (4 core)<br/>**Nuovo nome utente** = AzureAdmin<br/>**Nuova PASSWORD** = Contoso! 000<br/>**Conferma** = Contoso! 000|**Versione data** = (ultima versione)<br/>**Nome macchina virtuale** = ContosoSQL2<br/>**Livello** = STANDARD<br/>**Dimensione** = A3 (4 core)<br/>**Nuovo nome utente** = AzureAdmin<br/>**Nuova PASSWORD** = Contoso! 000<br/>**Conferma** = Contoso! 000|
|Configurazione della macchina virtuale|**Servizio CLOUD** = nome DNS del servizio Cloud univoco creato in precedenza (ex: ContosoDC123)<br/>**Regione/gruppo AFFINITÀ/virtuale di rete** = ContosoNET<br/>**Subnet rete virtuale** = Back(10.10.2.0/24)<br/>**ACCOUNT di archiviazione** = utilizza un account generato automaticamente lo spazio di archiviazione<br/>**Impostare la disponibilità** = crea una disponibilità impostare<br/>**Nome del SET di disponibilità** = SQLHADR|**Servizio CLOUD di** = nome DNS del servizio Cloud univoco creato in precedenza (ex: ContosoDC123)<br/>**Regione/gruppo AFFINITÀ/virtuale di rete** = ContosoNET<br/>**Subnet rete virtuale** = Back(10.10.2.0/24)<br/>**ACCOUNT di archiviazione** = utilizza un account generato automaticamente lo spazio di archiviazione<br/>**Impostare la disponibilità** = SQLHADR (è inoltre possibile configurare la disponibilità imposta dopo aver creato il computer. Tutti i tre computer devono essere assegnati al set di disponibilità SQLHADR.)|**Servizio CLOUD** = nome DNS del servizio Cloud univoco creato in precedenza (ex: ContosoDC123)<br/>**Regione/gruppo AFFINITÀ/virtuale di rete** = ContosoNET<br/>**Subnet rete virtuale** = Back(10.10.2.0/24)<br/>**ACCOUNT di archiviazione** = utilizza un account generato automaticamente lo spazio di archiviazione<br/>**Impostare la disponibilità** = SQLHADR (è inoltre possibile configurare la disponibilità imposta dopo aver creato il computer. Tutti i tre computer devono essere assegnati al set di disponibilità SQLHADR.)|
|Opzioni di macchina virtuale|Utilizzare le impostazioni predefinite|Utilizzare le impostazioni predefinite|Utilizzare le impostazioni predefinite|

<br/>

>[AZURE.NOTE] La configurazione precedente suggerisce macchine virtuali di livello STANDARD, poiché macchine di livello base non supportano l'endpoint di bilanciamento del carico necessarie per creare in un secondo momento listener un gruppo di disponibilità. Inoltre, le dimensioni di computer suggerite di seguito sono pensate per verifica disponibilità gruppi in macchine virtuali di Azure. Per prestazioni ottimali in carichi di lavoro di produzione, vedere Suggerimenti per la configurazione di [prestazioni procedure consigliate per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-performance.md)e le dimensioni di computer SQL Server.

Una volta completamente il provisioning di tre macchine virtuali, è necessario aggiungerli al dominio **corp.contoso.com** e concedere diritti amministrativi CORP\Install ai computer. Per eseguire questa operazione, utilizzare la procedura seguente per ognuna delle tre macchine virtuali.

1. Prima di tutto, modificare l'indirizzo del server DNS preferito. Avviare il download di file (RDP) desktop remoto di ogni Virtual Machine alla directory locale selezionando la macchina virtuale nell'elenco e fare clic sul pulsante **Connetti** . Per selezionare una macchina virtuale, fare clic in un punto qualsiasi tranne la prima cella nella riga, come illustrato di seguito.

    ![Scaricare il File RDP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)

1. Avviare il file RDP è stato scaricato e accedere a macchine Virtuali mediante la configurazione account di amministratore (**BUILTIN\AzureAdmin**) e la password (**Contoso! 000**).

1. Una volta è effettuato l'accesso, verrà visualizzato nel dashboard di **Server Manager** . Nel riquadro a sinistra, fare clic su **Server locale** .

1. Selezionare il collegamento **indirizzo IPv4 assegnato da DHCP, IPv6 abilitato** .

1. Nella finestra **Connessioni** di rete, selezionare l'icona di rete.

    ![Modificare i Server DNS preferito macchine Virtuali](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)

1. Nella barra dei comandi, fare clic su **modificare le impostazioni della connessione** (a seconda delle dimensioni della finestra, potrebbe essere necessario fare clic sulla doppia freccia a destra per visualizzare questo comando).

1. Selezionare **Internet Protocol versione 4 (TCP/IPv4)** e fare clic su proprietà.

1. Selezionare Usa il server DNS seguente indirizzi e specificare **10.10.2.4** nel **server DNS preferito**.

1. L' indirizzo **10.10.2.4** è l'indirizzo assegnato a una macchina virtuale subnet 10.10.2.0/24 in una rete virtuale Azure e quella macchina virtuale è **ContosoDC**. Per verificare l'indirizzo IP **ContosoDC**del, usare **nslookup contosodc** prompt dei comandi, come illustrato di seguito.

    ![Utilizzo di NSLOOKUP per trovare l'indirizzo IP per controller di dominio](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)

1. Fare clic su O**K** e quindi su **Chiudi** per salvare le modifiche. Si è ora possibile aggiungere la macchina virtuale a **corp.contoso.com**.

1. Tornare nella finestra di **Server locale** , fare clic sul collegamento di **gruppo di lavoro** .

1. Nella sezione **Nome del Computer** , fare clic su **Cambia**.

1. Selezionare la casella di controllo di **dominio** e digitare **corp.contoso.com** nella casella di testo. Fare clic su **OK**.

1. Nella finestra di dialogo **Protezione di Windows** popup, specificare le credenziali per l'account di amministratore di dominio predefinito (**CORP\AzureAdmin**) e la password (**Contoso! 000**).

1. Quando viene visualizzato il messaggio "Iniziale per il dominio corp.contoso.com", fare clic su **OK**.

1. Fare clic su **Chiudi**e quindi fare clic su **Riavvia** nella finestra di dialogo popup.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-vm"></a>Aggiungere l'utente Corp\Install come amministratore in ogni macchina virtuale:

1. Attendere finché non si riavvia la macchina virtuale e quindi avviare il file RDP nuovamente per l'accesso a macchine Virtuali utilizzando l'account **BUILTIN\AzureAdmin** .

1. In **Server Manager** selezionare **Strumenti**e quindi fare clic su **Gestione Computer**.

    ![Gestione computer](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)

1. Nella finestra **Gestione Computer** espandere **utenti e gruppi locali**e quindi selezionare **i gruppi**.

1. Fare doppio clic sul gruppo **Administrators** .

1. Nella finestra di dialogo **Proprietà-Administrators** fare clic sul pulsante **Aggiungi** .

1. Immettere il nome dell'utente **CORP\Install**e quindi fare clic su **OK**. Quando richiesto per le credenziali, usare l'account **AzureAdmin** con la **Contoso! 000** password.

1. Fare clic su **OK** per chiudere la finestra di dialogo **Proprietà di amministratore** .

### <a name="add-the-failover-clustering-feature-to-each-vm"></a>Aggiungere la funzionalità di **Failover cluster** a ogni macchina virtuale.

1. Il dashboard di **Server Manager** fare clic su **Aggiungi ruoli e funzionalità**.

1. In **Aggiungi ruoli e guidata funzionalità**, fare clic su **Avanti** fino alla pagina **caratteristiche** .

1. Selezionare **il cluster di Failover**. Quando richiesto, aggiungere altre caratteristiche di dipendenti.

    ![Aggiungere Failover cluster caratteristica a macchine Virtuali](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)

1. Fare clic su **Avanti**e quindi fare clic su **Installa** nella pagina di **Conferma** .

1. Una volta completato l'installazione della caratteristica **Failover cluster** , fare clic su **Chiudi**.

1. Disconnettersi da macchina virtuale.

1. Ripetere i passaggi descritti in questa sezione per tutti i tre server, ossia **ContosoWSFCNode**, **ContosoSQL1**e **ContosoSQL2**.

A questo punto viene completato il provisioning macchine virtuali SQL Server e in esecuzione, ma vengono installate con SQL Server con le opzioni predefinite.

## <a name="create-the-wsfc-cluster"></a>Creare il Cluster WSFC

In questa sezione, si crea il cluster WSFC contenenti il gruppo di disponibilità creato in un secondo momento. A questo punto, si devono eseguite le operazioni seguenti per ognuna delle tre macchine virtuali di uso di cluster WSFC:

- Completamente effettuato il provisioning in Azure

- Macchine Virtuali uniti al dominio

- Aggiunto **CORP\Install** al gruppo Administrators locale

- Aggiungere una funzionalità Failover cluster

Prima che è possibile partecipare a cluster WSFC tutte queste condizioni siano prerequisiti in ogni macchina virtuale.

Si noti inoltre che la rete virtuale Azure non hanno un comportamento nello stesso modo come una rete locale. È necessario creare il cluster nell'ordine seguente:

1. Creare un cluster di nodo singolo su uno dei nodi (**ContosoSQL1**).

1. Modificare l'indirizzo IP cluster da un indirizzo IP inutilizzato (**10.10.2.101**).

1. Portare online il nome del cluster.

1. Aggiungere i nodi (**ContosoSQL2** e **ContosoWSFCNode**).

Seguire la procedura seguente per eseguire queste attività che consente di configurare completamente il cluster.

1. Avviare il file RDP per **ContosoSQL1** e accedere usando l'account di dominio **CORP\Install**.

1. Il dashboard di **Server Manager** selezionare **Strumenti**e quindi fare clic su **Gestione Cluster di Failover**.

1. Nel riquadro sinistro, destro **Gestione Cluster di Failover**e quindi fare clic su **Crea un Cluster**, come illustrato di seguito.

    ![Creare Cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)

1. Nella creazione guidata Cluster di creare un cluster di un nodo spostandosi le pagine con le impostazioni seguenti:

  	|Pagina|Impostazioni|
|---|---|
|Prima di iniziare|Utilizzare le impostazioni predefinite|
|Selezionare i server|Digitare **ContosoSQL1** nel **nome del server di invio** e fare clic su **Aggiungi**|
|Avviso di convalida|Selezionare **No. I non richiedono il supporto Microsoft per il cluster e pertanto non si desidera eseguire il test di convalida. Quando fa clic su Avanti, continuare con la creazione del cluster**.|
|Punto di accesso per l'amministrazione Cluster|Tipo **Cluster1** nella **casella Nome Cluster**|
|Conferma|Usare le impostazioni predefinite a meno che non si utilizza spazi di archiviazione. Vedere la nota che segue questa tabella.|

    >[AZURE.WARNING] Se si utilizza [Spazi di archiviazione](https://technet.microsoft.com/library/hh831739), che raggruppa più dischi nel pool di archiviazione, è necessario deselezionare la casella di controllo **Aggiungi tutto lo spazio di archiviazione idoneo per il cluster** nella pagina di **Conferma** . Se non si deseleziona questa opzione, dischi virtuali verranno disconnesso durante il processo di cluster. Di conseguenza, sono anche non apparirà in Gestione disco o Explorer fino a quando non vengono rimossi gli spazi di archiviazione da cluster e ricollegato tramite PowerShell.

1. Nel riquadro sinistro, espandere **Gestione Cluster di Failover**e quindi fare clic su **Cluster1.corp.contoso.com**.

1. Nel riquadro centrale, scorrere fino alla sezione **Risorse principali Cluster** ed espandere la **nome: Clutser1** dettagli. Verrà visualizzato il **nome** e le risorse **Indirizzo IP** nello stato **non riuscito** . La risorsa indirizzo IP non è possibile portare online il cluster viene assegnato lo stesso indirizzo IP del computer, ovvero un indirizzo duplicato.

1. Pulsante destro del mouse della risorsa **Indirizzo IP** e quindi fare clic su **proprietà**.

    ![Proprietà cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)

1. Selezionare **Indirizzo IP statico** e specificare **10.10.2.101** nella casella di testo indirizzo. Scegliere **OK**.

1. Nell'area **Risorse di base Cluster** rapida **nome: Cluster1** e fare clic su **Connetti**. Attendere fino a entrambe le risorse sono online. Quando la risorsa nome cluster in linea, aggiornare il server di controller di dominio con un nuovo account Active Directory. Questo account Active Directory verrà utilizzato per eseguire il servizio di gruppo istogramma raggruppato disponibilità in un secondo momento.

1. Infine, aggiungere altri nodi cluster. Nella struttura del browser rapida **Cluster1.corp.contoso.com** e fare clic su **Aggiungi nodo**, come illustrato di seguito.

    ![Aggiungere nodo al Cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)

1. **Nodo guidata**fare clic su **Avanti**. Nella pagina **Selezione server** aggiungere **ContosoSQL2** e **ContosoWSFCNode** all'elenco digitando il nome del server in **nome server di invio** e quindi fare clic su **Aggiungi**. Al termine, fare clic su **Avanti**.

1. Nella pagina di **Avviso di convalida** , fare clic su **No** (in uno scenario di produzione è necessario eseguire i test di convalida). Scegliere **Avanti**.

1. Nella pagina di **Conferma** , fare clic su **Avanti** per aggiungere i nodi.

    >[AZURE.WARNING] Se si utilizza [Spazi di archiviazione](https://technet.microsoft.com/library/hh831739), che raggruppa più dischi nel pool di archiviazione, è necessario deselezionare la casella di controllo **Aggiungi tutto lo spazio di archiviazione idoneo al cluster** . Se non si deseleziona questa opzione, dischi virtuali verranno disconnesso durante il processo di cluster. Di conseguenza, sono anche non apparirà in Gestione disco o Explorer fino a quando non vengono rimossi gli spazi di archiviazione da cluster e ricollegato tramite PowerShell.

1. Una volta aggiungono i nodi al cluster, fare clic su **Fine**. Gestione Cluster di failover vengono visualizzati che il cluster ha tre nodi e visualizzarlo nel contenitore di **nodi** .

1. Abbandonare la sessione desktop remoto.

## <a name="prepare-the-sql-server-instances-for-availability-group"></a>Preparare le istanze di SQL Server per il gruppo di disponibilità

In questa sezione verranno effettuate le operazioni seguenti per **ContosoSQL1** e **contosoSQL2**:

- Aggiungere un account di accesso per **Il sistema operativo NT** con un insieme all'istanza di SQL Server predefinito di autorizzazioni necessarie

- Aggiungere **CORP\Install** come ruolo sysadmin all'istanza di SQL Server predefinito

- Aprire il firewall per l'accesso remoto di SQL Server

- Attivare la caratteristica sempre in gruppi disponibilità

- Modificare l'account di servizio SQL Server in **CORP\SQLSvc1** e **CORP\SQLSvc2**, rispettivamente

Queste azioni possono essere eseguite in qualsiasi ordine. Tuttavia, la procedura indicata di seguito illustrano le esercitazioni nell'ordine. Seguire la procedura per **ContosoSQL1** e **ContosoSQL2**:

1. Se non è stato effettuato fuori la sessione desktop remoto per la macchina virtuale, farlo.

1. Avviare i file RDP per **ContosoSQL1** e **ContosoSQL2** e accedere come **BUILTIN\AzureAdmin**.

1. Aggiungere innanzitutto **Il sistema operativo NT** per gli account di accesso di SQL Server e con autorizzazioni necessarie. Avviare **SQL Server Management Studio**.

1. Fare clic su **Connetti** a connettersi all'istanza di SQL Server predefinito.

1. In **Esplora oggetti**espandere **protezione**e quindi espandere **gli account di accesso**.

1. Pulsante destro del mouse login **NT il sistema operativo** e scegliere **proprietà**.

1. Nella pagina **entità a protezione diretta** per il server locale, selezionare **autorizzazioni** per le autorizzazioni seguenti e fare clic su **OK**.

    - Modificare un gruppo di disponibilità

    - Connettere SQL

    - Visualizzazione dello stato

1. Aggiungere quindi **CORP\Install** come ruolo **sysadmin** nell'istanza di SQL Server predefinito. In **Esplora oggetti**rapida **gli account di accesso** e fare clic su **Nuovo account di accesso**.

1. Digitare **CORP\Install** nel **nome di accesso**.

1. Nella pagina **Ruoli Server** selezionare **sysadmin**. Scegliere **OK**. Dopo aver creato l'account di accesso, è possibile visualizzare, espandere **gli account di accesso** in **Esplora oggetti**.

1. Punto, creare una regola del firewall per SQL Server. Schermata **Start** di avvio **Di Windows Firewall con sicurezza avanzata**.

1. Nel riquadro a sinistra, selezionare **Regole in entrata**. Nel riquadro destro fare clic su **Nuova regola**.

1. Nella pagina **Tipo di regola** , selezionare **programmi**, quindi fare clic su **Avanti**.

1. Nella pagina **programma** selezionare **il percorso del programma** e digitare %ProgramFiles%\Microsoft **Server\MSSQL12 SQL. MSSQLSERVER\MSSQL\Binn\sqlservr.exe** nella casella di testo (se si stanno seguendo queste istruzioni, ma l'uso di SQL Server 2012, directory di SQL Server è **MSSQL11. MSSQLSERVER**). Fare clic su **Avanti**.

1. Nella pagina **azione** mantenere **Consenti la connessione** selezionata e fare clic su **Avanti**.

1. Nella pagina del **profilo** , accettare le impostazioni predefinite e fare clic su **Avanti**.

1. Nella pagina **nome** specificare un nome di regola, ad esempio **SQL Server (applicazione regola)** nella casella di testo **nome** , quindi fare clic su **Fine**.

1. Successivamente, si abilita la caratteristica di **Sempre nella disponibilità i gruppi** . Schermata **Start** di avviare **Gestione configurazione SQL Server**.

1. Nella struttura del browser, fare clic su **Servizi di SQL Server**, quindi rapida il servizio **SQL Server (MSSQLSERVER)** e fare clic su **proprietà**.

1. Fare clic sulla scheda **Sempre sulla disponibilità** , quindi selezionare **Abilita sempre nella disponibilità gruppi**, come illustrato di seguito e quindi fare clic su **Applica**. Fare clic su **OK** nella finestra di dialogo popup e non chiudere la finestra Proprietà. Si verrà riavviare il servizio SQL Server dopo aver modificato l'account di servizio.

    ![Abilitare sempre in gruppi di disponibilità](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)

1. Successivamente si modifica l'account di servizio SQL Server. Fare clic **Sulla** scheda connessione, quindi digitare **CORP\SQLSvc1** (per **ContosoSQL1**) o **CORP\SQLSvc2** (per **ContosoSQL2**) nella casella **Nome dell'Account**, quindi inserire e confermare la password e quindi fare clic su **OK**.

1. Nella finestra popup, fare clic su **Sì** per riavviare il servizio SQL Server. Dopo il riavvio il servizio SQL Server, le modifiche apportate nella finestra delle proprietà inefficaci.

1. Disconnettersi da macchine virtuali.

## <a name="create-the-availability-group"></a>Creare il gruppo di disponibilità

A questo punto si è pronti configurare un gruppo di disponibilità. Di seguito è una struttura di cui si intrattengono:

- Creare un nuovo database (**MyDB1**) in **ContosoSQL1**

- Eseguire un backup completo e un backup del registro delle transazioni del database

- Ripristinare la versione completa e backup del log di **ContosoSQL2** con l'opzione **NORECOVERY**

- Creare il gruppo di disponibilità (**AG1**) con icona del commit, failover automatico e leggibile repliche secondarie

### <a name="create-the-mydb1-database-on-contososql1"></a>Creare il database MyDB1 su ContosoSQL1:

1. Se non è stato già eseguito fuori sessioni desktop remoto per **ContosoSQL1** e **ContosoSQL2**, farlo.

1. Avviare il file RDP per **ContosoSQL1** e accedere come **CORP\Install**.

1. In **Esplora File**, in * *c:\**, creare una directory denominata * *backup**. Utilizzare l'utilizzo della directory per eseguire il backup e ripristino del database.

1. Pulsante destro del mouse nella directory di nuova, scegliere **Condividi con**e quindi fare clic su **persone specifiche**, come illustrato di seguito.

    ![Creare una cartella di Backup](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)

1. Aggiungere **CORP\SQLSvc1** e concedere l'autorizzazione di **Lettura/scrittura** , quindi aggiungere **CORP\SQLSvc2** e concedere l'autorizzazione di **lettura** , come illustrato di seguito e quindi fare clic su **Condividi**. Una volta completato il processo di condivisione file, fare clic su **Chiudi**.

    ![Concedere le autorizzazioni per cartella di Backup](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)

1. Creare quindi il database. Dal menu **Start** avviare **SQL Server Management Studio**, quindi fare clic su **Connetti** a connettersi all'istanza di SQL Server predefinito.

1. In **Esplora oggetti**rapida **database** e fare clic su **Nuovo Database**.

1. In **Nome Database**digitare **MyDB1**, quindi fare clic su **OK**.

### <a name="take-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>Eseguire un backup di MyDB1 completo e ripristino per ContosoSQL2:

1. Successivamente, si esegue il backup completo del database. In **Esplora oggetti**espandere **database**, quindi rapida **MyDB1**, quindi fare clic su **attività**e quindi fare clic su **Backup**.

1. Nella sezione **origine** mantenere **tipo Backup** è impostato su **completo**. Nella sezione di **destinazione** , fare clic su **Rimuovi** per rimuovere il percorso di file predefinito per il file di backup.

1. Nella sezione **destinazione** fare clic su **Aggiungi**.

1. Nella casella **Nome File** digitare ** \\ContosoSQL1\backup\MyDB1.bak**. Quindi fare clic su **OK**e quindi fare clic su **OK** per il backup del database. Al termine dell'operazione di backup, fare clic su **OK** per chiudere la finestra di dialogo.

1. Successivamente, è scrivere un registro delle transazioni backup del database. In **Esplora oggetti**espandere **database**, quindi rapida **MyDB1**, quindi fare clic su **attività**e quindi fare clic su **Backup**.

1. In tipo di **Backup** selezionare **Log delle transazioni**. Mantenere il percorso del file di **destinazione** impostato su quello specificato in precedenza, fare clic su **OK**. Al termine dell'operazione di backup, fare di nuovo clic su **OK** .

1. Successivamente, ripristinare il backup di log completo e transazione nel **ContosoSQL2**. Avviare il file RDP per **ContosoSQL2** e accedere come **CORP\Install**. Lasciare aperta la sessione desktop remoto per **ContosoSQL1** .

1. Dal menu **Start** avviare **SQL Server Management Studio**, quindi fare clic su **Connetti** a connettersi all'istanza di SQL Server predefinito.

1. In **Esplora oggetti**rapida **database** e fare clic su **Ripristina Database**.

1. Nella sezione **origine** selezionare **dispositivo**e fare clic su **…** pulsante.

1. In **Selezionare i dispositivi di backup**, fare clic su **Aggiungi**.

1. In percorso di file di Backup, digitare \\ContosoSQL1\backup, quindi fare clic su Aggiorna, quindi selezionare MyDB1.bak, quindi fare clic su OK e quindi fare clic su OK. Viene visualizzato il backup completo e set di backup del registro del backup da ripristinare riquadro.

1. Passare alla pagina delle opzioni, quindi selezionare Ripristina con NORECOVERY in stato di recupero e quindi fare clic su OK per ripristinare il database. Al termine dell'operazione di ripristino, fare clic su OK.

### <a name="create-the-availability-group"></a>Creare il gruppo di disponibilità:

1. Tornare alla sessione desktop remota per **ContosoSQL1**. In **Esplora oggetti** di SQL Server Management Studio, rapida **Sempre sulla disponibilità** e fare clic su **Creazione guidata nuovo gruppo di disponibilità**, come illustrato di seguito.

    ![Avvia Creazione guidata nuovo gruppo di disponibilità](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)

1. Nella pagina **Introduzione** fare clic su **Avanti**. Nella pagina **Specificare il nome gruppo disponibilità** digitare **AG1** nella **casella Nome gruppo di disponibilità**, quindi scegliere **Avanti** .

    ![Creazione guidata nuovo AG, specificare il nome di AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)

1. Nella pagina **Seleziona database** selezionare **MyDB1** e fare clic su **Avanti**. Il database soddisfi i requisiti necessari per un gruppo di disponibilità poiché sono stati almeno un backup completo della replica primaria previsto.

    ![Creazione guidata nuovo AG, selezionare database](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)

1. Nella pagina **Specificare repliche** , fare clic su **Aggiungi Replica**.

    ![Creazione guidata nuovo AG, specificare repliche](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)

1. Finestra di dialogo **connessione al Server** appare. Digitare **ContosoSQL2** in **nome Server**, quindi fare clic su **Connetti**.

    ![Creazione guidata nuovo AG, connettersi al Server](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)

1. Tornare nella pagina **Specificare repliche** viene visualizzato **ContosoSQL2** elencati in **Repliche disponibili**. Configurare le repliche come illustrato di seguito. Al termine, fare clic su **Avanti**.

    ![Creazione guidata nuovo AG, specificare repliche (completo)](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)

1. Nella pagina **Selezionare la sincronizzazione dei dati iniziale** , selezionare **Partecipa a solo** e fare clic su **Avanti**. È già stata eseguita la sincronizzazione dei dati manualmente quando si ha eseguito il backup completo e transazioni su **ContosoSQL1** e li ripristinato nel **ContosoSQL2**. Se, tuttavia, è possibile scegliere di non eseguire il backup e ripristino del database e selezionare **completo** per consentire la creazione guidata nuovo gruppo di disponibilità di eseguire la sincronizzazione dei dati. Tuttavia, questa operazione è sconsigliata per i database di dimensioni molto grandi che si trovano in alcune organizzazioni.

    ![Creazione guidata nuovo AG, selezionare la sincronizzazione dei dati iniziale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)

1. Nella pagina **convalida** , fare clic su **Avanti**. Questa pagina è simile al seguente. Esiste un avviso per la configurazione di comunicare ascoltatore perché non è stato configurato un comunicare ascoltatore gruppo disponibilità. È possibile ignorare l'avviso, poiché questa esercitazione non si configura un comunicare ascoltatore. Per configurare comunicare ascoltatore al termine di questa esercitazione, vedere [configurare un comunicare ascoltatore ILB sempre sulla disponibilità per i gruppi di in Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

    ![Creazione guidata nuovo AG di convalida](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)

1. Nella pagina di **Riepilogo** , fare clic su **Fine**e quindi attendere che la procedura guidata Configura il nuovo gruppo di disponibilità. Nella pagina **stato** fare clic su **altri dettagli** per visualizzare lo stato di avanzamento dettagliata. Dopo aver completata la procedura guidata, controllare la pagina **dei risultati** per verificare che il gruppo di disponibilità è stato creato, come illustrato di seguito, quindi fare clic su **Chiudi** per chiudere la procedura guidata.

    ![Creazione guidata nuovo AG, i risultati](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)

1. In **Esplora oggetti**espandere **Sempre sulla disponibilità**, quindi espandere **I gruppi di disponibilità**. Viene visualizzato il nuovo gruppo di disponibilità in questo contenitore. Pulsante destro del mouse **AG1 (principale)** e fare clic su **Mostra Dashboard**.

    ![Mostra AG Dashboard](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)

1. **Sempre nel Dashboard** dovrebbe essere simile a quello illustrato di seguito. È possibile visualizzare le repliche, la modalità di failover di ogni replica e lo stato di sincronizzazione.

    ![Dashboard AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)

1. Tornare al **Server Manager**, selezionare **Strumenti**e quindi avviare **Gestione Cluster di Failover**.

1. Espandere **Cluster1.corp.contoso.com**e quindi espandere **servizi e applicazioni**. Selezionare **i ruoli** e che il gruppo di ruoli di disponibilità **AG1** è stati creati. Si noti che AG1 non dispone di qualsiasi indirizzo IP per il database client possono connettersi al gruppo di disponibilità, perché non è stato configurato un comunicare ascoltatore. È possibile connettersi direttamente al nodo principale per le operazioni di lettura e scrittura e il nodo secondario per le query di sola lettura.

    ![In Gestione Cluster di Failover AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

>[AZURE.WARNING] Non provare reindirizzare il gruppo di disponibilità da Gestione Cluster di Failover. Tutte le operazioni di failover devono essere eseguite all'interno **Sempre nel Dashboard** di SQL Server Management Studio. Per ulteriori informazioni, vedere [restrizioni sul utilizzando il WSFC gestione Cluster di Failover dei gruppi di disponibilità](https://msdn.microsoft.com/library/ff929171.aspx).

## <a name="next-steps"></a>Passaggi successivi
È stato ora correttamente implementato SQL Server sempre attive mediante la creazione di un gruppo di disponibilità in Azure. Per configurare un comunicare ascoltatore per il gruppo di disponibilità, vedere [configurare un comunicare ascoltatore ILB sempre sulla disponibilità per i gruppi di in Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

Per altre informazioni sull'uso di SQL Server in Azure, vedere [SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).
