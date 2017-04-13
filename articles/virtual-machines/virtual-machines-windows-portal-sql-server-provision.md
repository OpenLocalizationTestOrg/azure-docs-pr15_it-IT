<properties
    pageTitle="Effettuare il provisioning di un computer SQL Server virtuale | Microsoft Azure"
    description="Creare e connettersi a una macchina virtuale di SQL Server Azure tramite il portale. In questa esercitazione utilizza la modalità di gestione risorse."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    editor=""
    manager="jhubbard"
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth" />

# <a name="provision-a-sql-server-virtual-machine-in-the-azure-portal"></a>Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure

> [AZURE.SELECTOR]
- [Portale](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

In questa esercitazione-to-end viene illustrato come utilizzare il portale di Azure per effettuare il provisioning di un computer virtuale che esegue SQL Server.

La raccolta di Azure macchine () include diverse immagini che contengono Microsoft SQL Server. Bastano pochi clic, è possibile selezionare una delle immagini SQL VM dalla raccolta ed effettuare il provisioning nel proprio ambiente Azure.

In questa esercitazione, sarà necessario:

- [Selezionare un'immagine SQL VM dalla raccolta](#select-a-sql-vm-image-from-the-gallery)
- [Configurare e creare la macchina virtuale](#configure-the-vm)
- [Aprire la macchina virtuale con Desktop remoto](#open-the-vm-with-remote-desktop)
- [Connettersi a SQL Server in modalità remota](#connect-to-sql-server-remotely)

## <a name="select-a-sql-vm-image-from-the-gallery"></a>Selezionare un'immagine SQL VM dalla raccolta

1. Accedere al [portale di Azure](https://portal.azure.com) usando l'account.

    >[AZURE.NOTE] Se non si dispone di un account Azure, visitare il [periodo di prova gratuito di Azure](https://azure.microsoft.com/pricing/free-trial/).

1. Nel portale di Azure fare clic su **Nuovo**. Il portale si apre e il **Nuovo** . Le risorse macchine Virtuali di SQL Server disponibili nel gruppo **macchine virtuali** di mercato.

1. In e il **Nuovo** , fare clic su **macchine virtuali**.

1. Per visualizzare tutte le immagini disponibili, fare clic su **tutti** in e il **macchine virtuali** .

    ![Azure macchine virtuali Blade](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade.png)

1. In **server di Database**, fare clic su **SQL Server**. Potrebbe essere necessario scorrere verso il basso per individuare il **server di Database**. Esaminare i modelli di SQL Server disponibili.

    ![Raccolta di computer virtuale SQL immagini](./media/virtual-machines-windows-portal-sql-server-provision/virtual-machine-gallery-sql-server.png)

1. Ogni modello identifica una versione di SQL Server e un sistema operativo. Selezionare una di queste immagini dall'elenco. Quindi esaminare e il dettagli che fornisce una descrizione dell'immagine macchina virtuale.

    >[AZURE.NOTE] Immagini SQL VM includono costi per le licenze per SQL Server in cui il prezzo al minuto del macchine Virtuali create. Esiste un'altra opzione per visualizzare il-proprietari-licenza (BYOL) e pagare solo per la macchina virtuale. I nomi di immagine sono preceduti da {BYOL}. Per ulteriori informazioni su questa opzione, vedere [Introduzione a SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

1. In **Selezionare un modello di distribuzione**, verificare che sia selezionata **Manager delle risorse** . Manager delle risorse è il modello di distribuzione consigliato per nuove macchine virtuali. Fare clic su **Crea**.

    ![Creare macchine Virtuali di SQL in Gestione risorse](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## <a name="configure-the-vm"></a>Configurare la macchina virtuale
Sono disponibili cinque pale per la configurazione di una macchina virtuale di SQL Server.

| Passaggio               | Descrizione                          |
|---------------------|-------------------------------|
| **Nozioni di base**              | [Configurare le impostazioni di base](#1-configure-basic-settings)      |
| **Dimensioni**                | [Scegliere le dimensioni del computer virtuale](#2-choose-virtual-machine-size)   |
| **Impostazioni**            | [Configurare le caratteristiche facoltative](#3-configure-optional-features)   |
| **Impostazioni di SQL Server** | [Configurare le impostazioni di SQL server](#4-configure-sql-server-settings) |
| **Riepilogo**             | [Controllare il riepilogo](#5-review-the-summary)            |

## <a name="1-configure-basic-settings"></a>1. configurare le impostazioni di base
Nella e **Nozioni di base** , fornire le informazioni seguenti:

* Immettere una macchina virtuale univoca **nome**.
* Specificare un **nome utente** per l'account di amministratore locale nella macchina virtuale. L'account viene aggiunto anche il ruolo di server **sysadmin** di SQL Server.
* Fornire una **Password**complessa.
* Se si hanno più abbonamenti, verificare che l'abbonamento sia corretta per la nuova macchina virtuale.
* Nella casella di **gruppo di risorse** , digitare un nome per un nuovo gruppo di risorse. In alternativa, per l'utilizzo di una risorsa esistente gruppo fare clic su **Seleziona esistente**. Un gruppo di risorse è una raccolta di risorse correlate in Azure (macchine virtuali, gli account di archiviazione, reti virtuali e così via).

    >[AZURE.NOTE] Con un nuovo gruppo di risorse è utile se si verifica solo o formazione sulle distribuzioni di SQL Server in Azure. Dopo aver terminato il test, eliminare il gruppo di risorse per eliminare automaticamente la macchina virtuale e tutte le risorse associate a tale gruppo di risorse. Per ulteriori informazioni sui gruppi di risorse, vedere [Panoramica di gestione risorse Azure](../azure-resource-manager/resource-group-overview.md).

* Selezionare un **percorso** per la distribuzione.
* Fare clic su **OK** per salvare le impostazioni.

    ![Blade nozioni di base SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## <a name="2-choose-virtual-machine-size"></a>2. scegliere le dimensioni del computer virtuale
Nel passaggio **dimensioni** selezionare una dimensione macchina virtuale in e lo **scegliere una dimensione** . E il viene inizialmente visualizzata dimensioni computer consigliati basate sul modello selezionato. Inoltre, le stime costo mensile per eseguire la macchina virtuale.

![Opzioni relative alle dimensioni di macchine Virtuali di SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

Per carichi di lavoro di produzione, è consigliabile selezionare una dimensione macchina virtuale che supporta [l'Archiviazione Premium](../storage/storage-premium-storage.md). Se non richiede tale livello di prestazioni, utilizzare il pulsante **Visualizza tutto** , che mostra tutte le opzioni di dimensioni computer. Ad esempio, è possibile utilizzare una versione ridotta computer per un ambiente di sviluppo o test.

>[AZURE.NOTE] Per ulteriori informazioni sulla macchina virtuale dimensioni, vedere [le dimensioni per macchine virtuali](virtual-machines-windows-sizes.md). Per informazioni sulle dimensioni dei macchine Virtuali di SQL Server, vedere [prestazioni procedure consigliate per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-performance.md).

Scegliere le dimensioni del computer e quindi fare clic su **Seleziona**.

## <a name="3-configure-optional-features"></a>3. configurare caratteristiche facoltative
Nella e **Impostazioni** configurare Azure lo spazio di archiviazione, rete e il monitoraggio per la macchina virtuale.

- In sistema di **archiviazione**, specificare un **disco digitare** di Standard o Premium (SSD). Spazio di archiviazione Premium è consigliato per carichi di lavoro di produzione.

>[AZURE.NOTE] Se si seleziona Premium (SSD) per una dimensione di computer che non supporta lo spazio di archiviazione Premium, le dimensioni del computer viene modificato automaticamente.  

- In **account di archiviazione**, è possibile accettare il nome dell'account provisioning automaticamente lo spazio di archiviazione. È anche possibile fare clic sull' **account di archiviazione** per scegliere un account esistente e configurare il tipo di account di archiviazione. Per impostazione predefinita, Azure crea un nuovo account di archiviazione con lo spazio di archiviazione ridondante in locale. Per ulteriori informazioni sulle opzioni di archiviazione, vedere [replica di archiviazione Azure](../storage/storage-redundancy.md).

- In **rete**, è possibile accettare i valori di compilazione automatica. È anche possibile fare clic su ogni caratteristica configurare manualmente la **rete virtuale**, alla **Subnet**, **indirizzo IP pubblico**e **Il gruppo di sicurezza di rete**. Per motivi di questa esercitazione, mantenere i valori predefiniti.

- Azure consente **monitoraggio** per impostazione predefinita con lo stesso account di archiviazione per la macchina virtuale. È possibile modificare queste impostazioni descritte di seguito.

- In **impostazione della disponibilità**, specificare un set di disponibilità. Ai fini di questa esercitazione, è possibile selezionare **Nessuno**. Se si prevede di configurare la disponibilità di AlwaysOn di SQL, configurare la disponibilità per evitare di ricreare la macchina virtuale.  Per ulteriori informazioni, vedere [gestire la disponibilità di macchine virtuali](virtual-machines-windows-manage-availability.md).

Una volta completata la configurazione di queste impostazioni, fare clic su **OK**.

## <a name="4-configure-sql-server-settings"></a>4. configurare le impostazioni di SQL server
In e **l'impostazioni di SQL Server** , configurare le impostazioni specifiche e le ottimizzazioni per SQL Server. Le impostazioni che è possibile configurare per SQL Server includono le seguenti.

| Impostazione               |
|---------------------|
| [Connettività](#connectivity)              |
| [Autenticazione](#authentication)                |
| [Configurazione di archiviazione](#storage-configuration)            |
| [Automatico patch](#automated-patching) |
| [Backup automatico](#automated-backup)             |
| [Integrazione di Azure archivio chiave](#azure-key-vault-integration)             |
| [R servizi](#r-services) |

### <a name="connectivity"></a>Connettività
In **connettività SQL**, specificare il tipo di accesso che si desidera l'istanza di SQL Server in questa macchina virtuale. Ai fini di questa esercitazione, selezionare **pubblica (internet)** per consentire le connessioni a SQL Server dal computer o servizi su internet. Con questa opzione è selezionata, Azure configura automaticamente il firewall e il gruppo di sicurezza di rete per consentire il traffico sulla porta 1433.  

![Opzioni di integrazione applicativa SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

Per connettersi a SQL Server tramite internet, è necessario attivare anche l'autenticazione di SQL Server, descritto nella sezione successiva.

>[AZURE.NOTE] È possibile aggiungere altre restrizioni per le comunicazioni di rete di una macchina SQL Server virtuale. È possibile eseguire questa operazione modificando il gruppo di sicurezza di rete dopo aver creata la macchina virtuale. Per ulteriori informazioni, vedere [che cos'è un gruppo di sicurezza di rete (NSG)?](../virtual-network/virtual-networks-nsg.md)

Se si preferisce non abilitato le connessioni al motore di Database tramite internet, scegliere una delle opzioni seguenti:

- **Locale (all'interno solo macchine Virtuali)** per consentire le connessioni a SQL Server solo dall'interno la macchina virtuale.
- **Privato (entro virtuali)** per consentire le connessioni a SQL Server dal computer o servizi nella stessa rete virtuale.

>[AZURE.NOTE] L'immagine di macchina virtuale per SQL Server Express edition non attiva automaticamente il protocollo TCP/IP. Questo vale anche per le opzioni di integrazione applicativa pubblici e privati. Per Express edition, è necessario utilizzare Gestione configurazione SQL Server per [attivare manualmente il protocollo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) dopo aver creato la macchina virtuale.

Scegliendo la connettività più restrittiva che lo scenario consente in generale, migliorare la sicurezza. Ma tutte le opzioni sono protetti tramite le regole gruppo di sicurezza di rete e l'autenticazione di Windows/SQL.

**Porta** per impostazione predefinita 1433. È possibile specificare un numero di porta diverso.
Per ulteriori informazioni, vedere connettersi a una macchina virtuale SQL Server (Manager delle risorse) [| Microsoft Azure](virtual-machines-windows-sql-connect.md).

### <a name="authentication"></a>Autenticazione
Se si richiede l'autenticazione di SQL Server, fare clic su **Attiva** secondo **l'autenticazione di SQL**.

![Autenticazione di SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

>[AZURE.NOTE] Se si prevede di accedere a SQL Server tramite internet (ad esempio l'opzione di connettività per la pubblica), è necessario attivare l'autenticazione di SQL qui. Accesso pubblico a SQL Server richiede l'uso di autenticazione di SQL Server.

Se si abilita l'autenticazione di SQL Server, specificare un **nome di accesso** e una **Password**. Il nome utente è configurato come un accesso di autenticazione di SQL Server e un membro del ruolo server **sysadmin** . Per ulteriori informazioni sulle modalità di autenticazione, vedere [scegliere una modalità di autenticazione](http://msdn.microsoft.com/library/ms144284.aspx) .

Se non si attiva l'autenticazione di SQL Server, è possibile usare l'account Administrator locale nella macchina virtuale per la connessione all'istanza di SQL Server.

### <a name="storage-configuration"></a>Configurazione di archiviazione
Fare clic su **configurazione di archiviazione** per specificare i requisiti di spazio di archiviazione.

![Configurazione di archiviazione SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

>[AZURE.NOTE] Se si seleziona lo spazio di archiviazione Standard, questa opzione non è disponibile. Ottimizzazione di archiviazione automatica è disponibile solo per l'archiviazione Premium.

È possibile specificare requisiti come operazioni di input/output al secondo (IOPs), velocità MB/s e la dimensione di spazio di archiviazione totale. Configurare questi valori utilizzando le scale di scorrimento. Il portale calcola automaticamente il numero di dischi in base a tali requisiti.

Per impostazione predefinita, Azure consente di ottimizzare lo spazio di archiviazione per 5000, 200 MB, secondo e 1 TB di spazio di archiviazione. È possibile modificare queste impostazioni di archiviazione in base a carico di lavoro. In **archiviazione ottimizzate per**, selezionare una delle opzioni seguenti:

- **Generale** è l'impostazione predefinita e supporta la maggior parte dei carichi di lavoro.
- Elaborazione **transazione** consente di ottimizzare lo spazio di archiviazione per carichi di lavoro OLTP database tradizionale.
- **Data warehouse** consente di ottimizzare lo spazio di archiviazione per carichi di lavoro di analisi e creazione di report.

>[AZURE.NOTE] I limiti superiori per i dispositivi di scorrimento variano a seconda delle dimensioni del macchina virtuale selezionata.

### <a name="automated-patching"></a>Automatico patch
**Automatico patch** è attivata per impostazione predefinita. Aggiornamento automatico consente di Azure aggiornare automaticamente il sistema operativo e SQL Server. Specificare un giorno della settimana, ora e la durata di una finestra di manutenzione. Azure esegue patch in questa finestra di manutenzione. La pianificazione della finestra di manutenzione utilizza le impostazioni internazionali macchine Virtuali per volta. Se non si desidera aggiornare automaticamente il sistema operativo e SQL Server Azure, fare clic su **Disattiva**.  

![SQL automatizzato patch](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

Per ulteriori informazioni, vedere [Automatizzato patch per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Backup automatico
Attivare backup automatici del database per tutti i database in **automatizzato di backup**. Backup automatico è disattivato per impostazione predefinita.

Quando si abilita backup automatico SQL, è possibile configurare le operazioni seguenti:

- Periodo di conservazione (giorni) per i backup
- Account di archiviazione da utilizzare per i backup
- Opzione di crittografia e la password per l'esecuzione di backup

Per crittografare il backup, fare clic su **Attiva**. Specificare la **Password**. Azure consente di creare un certificato per crittografare le copie di backup e viene utilizzata la password specificata per la protezione del certificato.

![SQL automatizzato di Backup](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup.png)

 Per ulteriori informazioni, vedere [Copia di Backup automatico per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-automated-backup.md).

### <a name="azure-key-vault-integration"></a>Integrazione di archivio di chiave Azure
Per archiviare informazioni riservate di protezione in Azure per la crittografia, fare clic su **integration Azure archivio chiave** e fare clic su **Attiva**.

![Integrazione di SQL Azure archivio chiave](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

La tabella seguente elenca i parametri necessari per configurare l'integrazione di archivio di Azure chiave.

|PARAMETRO|DESCRIZIONE|ESEMPIO|
|----------|----------|-------|
|**URL di archivio chiave** |La posizione dell'archivio di chiave.|https://contosokeyvault.Vault.Azure.NET/ |
|**Nome dell'entità** |Azure Active Directory nome dell'entità servizio. Questo nome si intende anche come l'ID di Client.  |fde2b411 - 33d 5-4e11-af04eb07b669ccf2|
| **Segreto principale**|Azure Active Directory servizio principale segreto. Il segreto viene anche definito segreto del Client. | 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM =|
|**Nome delle credenziali**|**Nome delle credenziali**: integrazione AKV crea una credenziale all'interno di SQL Server, consentendo macchine Virtuali di accedere all'archivio chiave. Scegliere un nome per queste credenziali.| mycred1|

Per ulteriori informazioni, vedere [Configurare l'integrazione di archivio di Azure chiave per SQL Server in macchine virtuali di Azure](virtual-machines-windows-ps-sql-keyvault.md).

Dopo aver terminato la configurazione delle impostazioni di SQL Server, fare clic su **OK**.

### <a name="r-services"></a>Servizi di R
Per SQL Server 2016 Enterprise edition, è possibile abilitare [Servizi R di SQL Server](https://msdn.microsoft.com/library/mt604845.aspx). In questo modo è possibile utilizzare analitica avanzate con SQL Server 2016. Fare clic su **Attiva** nella e **Impostazioni di SQL Server** .

![Attivare i servizi SQL Server R](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)

>[AZURE.NOTE] Per le immagini di SQL Server che non sono 2016 Enterprise edition, l'opzione per abilitare Servizi R è disattivata.

## <a name="5-review-the-summary"></a>5. controllare il riepilogo
Nella e **Riepilogo** , controllare il riepilogo e fare clic su **OK** per creare SQL Server, gruppo di risorse e risorse specificate per questa macchina virtuale.

È possibile controllare la distribuzione dal portale di azure. Pulsante **notifiche** nella parte superiore della schermata mostra lo stato di base della distribuzione.

>[AZURE.NOTE] Per fornire un'idea su tempi di distribuzione, distribuito un VM SQL all'area degli Stati Uniti orientali con le impostazioni predefinite. Distribuzione di prova ha un totale di 26 minuti per completare. Ma che si verifichino un'alternativa più veloce o più lentamente fase di distribuzione in base a propria area geografica e le impostazioni selezionate.

## <a name="open-the-vm-with-remote-desktop"></a>Aprire la macchina virtuale con Desktop remoto

Per connettere la macchina virtuale con Desktop remoto, procedere come segue:

1. Una volta creata la macchina virtuale Azure, l'icona per la macchina virtuale viene visualizzata nel dashboard Azure. È possibile anche trovare sfogliando le macchine virtuali esistenti. Fare clic su nuova macchina virtuale SQL. Blade **macchina virtuale** consente di visualizzare i dettagli di macchina virtuale.
1. Nella parte superiore e il **computer virtuale** , fare clic su **Connetti**.
1. Il browser scarica un file RDP per la macchina virtuale. Aprire il file RDP.
    ![Desktop remoto per macchine Virtuali di SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-remote-desktop.png)
1. Connessione Desktop remoto per segnalare che non è possibile identificare il server di pubblicazione della connessione remota. Fare clic su **Connetti** per continuare.
1. Nella finestra di dialogo **Protezione di Windows** , fare clic su **utilizza un altro account**.
1. Per tipo di **nome utente** ** \<nome utente >**, dove <user name> è il nome utente specificato quando si configura la macchina virtuale. È necessario aggiungere una barra rovesciata iniziale prima del nome.
1. Digitare la **Password** che è già stato configurato per questa macchina virtuale e quindi fare clic su **OK** per connettersi.
1. Se un'altra finestra di dialogo **Connessione Desktop remoto** chiede se si desidera connettersi, fare clic su **Sì**.

Dopo aver connesso alla macchina virtuale di SQL Server, è possibile avviare SQL Server Management Studio e connettersi con l'autenticazione di Windows con le credenziali di amministratore locale. Se è abilitata l'autenticazione di SQL Server, è anche possibile connettersi con autenticazione di SQL Server tramite l'accesso a SQL e la password che è configurata durante il provisioning.

Accedere al computer di consente di modificare direttamente computer e le impostazioni di SQL Server in base alle esigenze. Ad esempio, si potrebbe configurare le impostazioni del firewall o modificare le impostazioni di configurazione di SQL Server.

## <a name="connect-to-sql-server-remotely"></a>Connettersi a SQL Server in modalità remota

In questa esercitazione, è selezionata l'accesso **pubblico** per la macchina virtuale e **l'Autenticazione di SQL Server**. Queste impostazioni configurate automaticamente la macchina virtuale per consentire le connessioni di SQL Server da qualsiasi client tramite internet (presupponendo che hanno accesso di SQL corretto).

>[AZURE.NOTE] Se non è stata selezionata pubblico durante il provisioning, passaggi aggiuntivi sono necessarie per accedere l'istanza di SQL Server tramite internet. Per ulteriori informazioni, vedere [connettersi a una macchina virtuale di SQL Server](virtual-machines-windows-sql-connect.md).

Nelle sezioni seguenti viene illustrato come connettersi all'istanza di SQL Server in di una macchina virtuale da un altro computer tramite internet.

> [AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'uso di SQL Server in Azure, vedere le [Domande frequenti](virtual-machines-windows-sql-server-iaas-faq.md)e [SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md) .

Per una panoramica video di SQL Server in macchine virtuali di Azure, guardare [macchine Virtuali di Azure è la piattaforma migliore per SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016).

[Esplorare il percorso formativo](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) per SQL Server in macchine virtuali di Azure.
