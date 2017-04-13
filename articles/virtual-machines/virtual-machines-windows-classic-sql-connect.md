<properties
    pageTitle="Connettersi a una macchina virtuale SQL Server (classico) | Microsoft Azure"
    description="Informazioni su come connettersi a SQL Server in esecuzione in una macchina virtuale in Azure. Questo argomento viene utilizzato il modello di distribuzione classica. Gli scenari diversa a seconda della configurazione di rete e il percorso del client."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="jroth" />

# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Connettersi a una macchina virtuale SQL Server in Azure (distribuzione classica)

> [AZURE.SELECTOR]
- [Manager delle risorse](virtual-machines-windows-sql-connect.md)
- [Classica](virtual-machines-windows-classic-sql-connect.md)

## <a name="overview"></a>Panoramica

In questo argomento viene descritto come connettersi all'istanza di SQL Server in esecuzione in una macchina virtuale Azure. Vengono descritti alcuni [scenari di connettività generale](#connection-scenarios) e quindi vengono [fornite procedure dettagliate per la configurazione di connettività di SQL Server in macchine Virtuali un Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Se si utilizza macchine virtuali di Manager delle risorse, vedere [connettersi a un SQL Server virtuale computer Azure Gestione risorse](virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Scenari di connessione

Il modo in cui che un client si connette a SQL Server in esecuzione in una macchina virtuale varia a seconda della posizione del client e la configurazione di rete e computer. Questi scenari includono:

- [Connettersi a SQL Server lo stesso servizio cloud](#connect-to-sql-server-in-the-same-cloud-service)
- [Connettersi a SQL Server tramite internet](#connect-to-sql-server-over-the-internet)
- [Connettersi a SQL Server nella stessa rete virtuale](#connect-to-sql-server-in-the-same-virtual-network)

>[AZURE.NOTE] Prima di connettersi con uno di questi metodi, è necessario seguire i [passaggi da eseguire in questo articolo per configurare la connettività](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Connettersi a SQL Server lo stesso servizio cloud

È possibile creare più macchine virtuali nel servizio cloud stesso. Per comprendere questo scenario macchine virtuali, vedere [come connettere macchine virtuali con un servizio di rete o su cloud virtuale](virtual-machines-windows-classic-connect-vms.md#connect-vms-in-a-standalone-cloud-service). Questo scenario è un client in un computer virtuale tenta di connettersi a SQL Server in esecuzione su un altro computer virtuale nel servizio cloud stesso.

In questo scenario, è possibile connettersi tramite il macchine Virtuali **nome** (indicata anche come **Nome del Computer** o **nome host** nel portale). Questo è il nome specificato per la macchina virtuale durante la creazione. Ad esempio, se è assegnato il SQL VM **mysqlvm**, un client macchine Virtuali nel servizio cloud stesso è possibile utilizzare questa stringa di connessione per la connessione:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Connettersi a SQL Server tramite Internet

Se si desidera connettere il motore di database di SQL Server da Internet, è necessario creare un endpoint macchina virtuale per la comunicazione TCP in ingresso. Questo passaggio configurazione Azure indirizza il traffico di porte TCP in ingresso a una porta TCP che è possibile accedere al computer virtuale.

Per connettersi tramite internet, è necessario usare il nome della macchina virtuale DNS e il numero di porta endpoint macchine Virtuali (configurato più avanti in questo articolo). Per trovare il nome del DNS, passare al portale di Azure e selezionare **macchine virtuali (classica)**. Selezionare la macchina virtuale. Il **nome DNS** viene visualizzato nella sezione **Overview** .

Si consideri ad esempio una macchina virtuale classica denominata **mysqlvm** con un nome DNS del **mysqlvm7777.cloudapp.net** e un endpoint macchine Virtuali di **57500**. Supponendo che la connettività opportunamente configurata, la stringa di connessione seguenti consente di accedere al computer virtuale ovunque su internet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Anche se in questo modo la connettività per i client tramite internet, non implica che tutti gli utenti possono connettersi a SQL Server. All'esterno è necessario il nome utente corretto e la password client. Per rafforzare la sicurezza, non usare la ben nota porta 1433 per l'endpoint pubblico macchina virtuale. E se possibile, è possibile aggiungere un ACL per l'endpoint per limitare il traffico solo ai client sia autorizzato. Per ulteriori informazioni sull'utilizzo di ACL con i punti finali, vedere [gestione ACL in un punto finale](virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint).

>[AZURE.NOTE] È importante tenere presente che quando si utilizza questa tecnica per comunicare con SQL Server, tutti i dati in uscita dalla data center del Azure è soggetto a normale [prezzo trasferimenti di dati in uscita](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Connettersi a SQL Server nella stessa rete virtuale

[Virtuali](../virtual-network/virtual-networks-overview.md) consente altri scenari. È possibile connettersi macchine virtuali nella stessa rete virtuale, anche se tali macchine virtuali presenti nei servizi cloud diverso. E con una [VPN da sito](../vpn-gateway/vpn-gateway-site-to-site-create.md), è possibile creare un'architettura ibrida che si connette macchine virtuali con computer e le reti locale.

Reti virtuali è inoltre possibile aggiungere le macchine virtuali Azure a un dominio. Questo è l'unico modo per utilizzare l'autenticazione di Windows per SQL Server. Altri scenari di connessione richiedono l'autenticazione di SQL con nomi utente e password.

Se si intende configurare un ambiente di dominio e l'autenticazione di Windows, non è necessario utilizzare la procedura descritta in questo articolo per configurare l'endpoint pubblico o l'autenticazione di SQL Server e gli account di accesso. In questo scenario, è possibile connettersi all'istanza di SQL Server specificare il nome del macchine Virtuali di SQL Server nella stringa di connessione. Nell'esempio seguente si presuppone che l'autenticazione di Windows anche è stato configurato e che l'utente è stato concesso l'accesso all'istanza di SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Procedura per la configurazione della connettività di SQL Server in macchine Virtuali un Azure

La procedura seguente viene illustrato come connettersi all'istanza di SQL Server tramite internet con SQL Server Management Studio (Express). Tuttavia, gli stessi passaggi si applicano per rendere accessibile per le applicazioni in esecuzione entrambi locale e in Azure la macchina virtuale di SQL Server.

Prima di connettersi all'istanza di SQL Server da un'altra macchine Virtuali o da internet, è necessario completare le attività seguenti come descritto nelle sezioni che seguono:

- [Creare un endpoint TCP per la macchina virtuale](#create-a-tcp-endpoint-for-the-virtual-machine)
- [Aprire le porte TCP in Windows firewall](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurare SQL Server per ascoltare il protocollo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurare SQL Server per l'autenticazione in modalità misti](#configure-sql-server-for-mixed-mode-authentication)
- [Creare gli accessi l'autenticazione di SQL Server](#create-sql-server-authentication-logins)
- [Determinare il nome DNS della macchina virtuale](#determine-the-dns-name-of-the-virtual-machine)
- [Connettersi al motore di Database da un altro computer](#connect-to-the-database-engine-from-another-computer)

Nel diagramma seguente verrà riepilogato il percorso di connessione:

![Connessione a una macchina virtuale di SQL Server](../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[AZURE.INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Classic Steps](../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Passaggi successivi

Se si prevede anche usare disponibilità AlwaysOn per disponibilità e il ripristino di emergenza, è consigliabile implementare un comunicare ascoltatore. I database client si connettono a comunicare ascoltatore anziché direttamente a una delle istanze di SQL Server. Comunicare ascoltatore indirizza client per la replica principale del gruppo di disponibilità. Per ulteriori informazioni, vedere [configurare un comunicare ascoltatore ILB per i gruppi di disponibilità AlwaysOn in Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

È importante visualizzare tutte le procedure consigliate di sicurezza per SQL Server in esecuzione in un computer virtuale Azure. Per ulteriori informazioni, vedere [Considerazioni sulla protezione per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-security.md).

[Esplorare il percorso formativo](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) per SQL Server in macchine virtuali di Azure. 

Per altri argomenti correlati all'esecuzione di SQL Server in macchine virtuali di Azure, vedere [SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).
