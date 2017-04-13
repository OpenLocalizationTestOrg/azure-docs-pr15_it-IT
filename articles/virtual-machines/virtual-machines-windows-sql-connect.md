<properties
    pageTitle="Connettersi a una macchina virtuale SQL Server (Manager delle risorse) | Microsoft Azure"
    description="Informazioni su come connettersi a SQL Server in esecuzione in una macchina virtuale in Azure. Questo argomento viene utilizzato il modello di distribuzione classica. Gli scenari diversa a seconda della configurazione di rete e il percorso del client."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"    
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth" />

# <a name="connect-to-a-sql-server-virtual-machine-on-azure-resource-manager"></a>Connettersi a una macchina virtuale SQL Server in Azure (Manager delle risorse)

> [AZURE.SELECTOR]
- [Manager delle risorse](virtual-machines-windows-sql-connect.md)
- [Classica](virtual-machines-windows-classic-sql-connect.md)

## <a name="overview"></a>Panoramica

In questo argomento viene descritto come connettersi all'istanza di SQL Server in esecuzione in una macchina virtuale Azure. Vengono descritti alcuni [scenari di connettività generale](#connection-scenarios) e quindi vengono [fornite procedure dettagliate per la configurazione di connettività di SQL Server in macchine Virtuali un Azure](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modello di distribuzione classica. Per visualizzare la versione classica di questo articolo, vedere [connettersi a un SQL Server virtuale computer Azure classica](virtual-machines-windows-classic-sql-connect.md).

Se invece si desidera utilizzare una panoramica completa di provisioning e la connettività, vedere [il Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Scenari di connessione

Il modo in cui che un client si connette a SQL Server in esecuzione in una macchina virtuale varia a seconda della posizione del client e la configurazione di rete e computer. Questi scenari includono:

- [Connettersi a SQL Server tramite internet](#connect-to-sql-server-over-the-internet)
- [Connettersi a SQL Server nella stessa rete virtuale](#connect-to-sql-server-in-the-same-virtual-network)

### <a name="connect-to-sql-server-over-the-internet"></a>Connettersi a SQL Server tramite Internet

Se si desidera connettere il motore di database di SQL Server da Internet, esistono alcuni passaggi richiesti, come configurare il firewall, abilitare l'autenticazione di SQL e configurare il gruppo di sicurezza di rete è necessario disporre di una regola gruppo di sicurezza di rete per consentire il traffico TCP sulla porta 1433.

Se si usa il portale di effettuare il provisioning di un'immagine di macchina virtuale di SQL Server Manager delle risorse, la procedura seguente vengono eseguita automaticamente quando si seleziona **pubblico** per l'opzione di connettività SQL:

![Opzione di connettività SQL pubblico durante il provisioning](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Se non si tratta uno durante il provisioning, quindi è possibile configurare manualmente SQL Server e macchine virtuali seguendo i [passaggi da eseguire in questo articolo per configurare manualmente la connettività](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

>[AZURE.NOTE] L'immagine di macchina virtuale per SQL Server Express edition non attiva automaticamente il protocollo TCP/IP. Per Express edition, è necessario utilizzare Gestione configurazione SQL Server per [attivare manualmente il protocollo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) dopo aver creato la macchina virtuale.

Al termine, tutti i client con accesso a internet è possibile connettersi all'istanza di SQL Server specificando l'indirizzo IP pubblico della macchina virtuale o etichetta del DNS assegnata a tale indirizzo IP. Se la porta di SQL Server è 1433, non è necessario specificare nella stringa di connessione.

    "Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Anche se in questo modo la connettività per i client tramite internet, non implica che tutti gli utenti possono connettersi a SQL Server. All'esterno è necessario il nome utente corretto e la password client. Per rafforzare la sicurezza, è possibile evitare la ben nota porta 1433. Ad esempio, se si SQL Server configurato per l'ascolto sulla porta 1500 e stabilito un firewall appropriato e regole di gruppo di sicurezza di rete, per connettersi aggiungendo il numero di porta per il nome del Server come illustrato nell'esempio seguente:

    "Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

>[AZURE.NOTE] È importante tenere presente che quando si utilizza questa tecnica per comunicare con SQL Server, tutti i dati in uscita dalla data center del Azure è soggetto a normale [prezzo trasferimenti di dati in uscita](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Connettersi a SQL Server nella stessa rete virtuale

[Virtuali](../virtual-network/virtual-networks-overview.md) consente altri scenari. È possibile connettersi macchine virtuali nella stessa rete virtuale, anche se tali macchine virtuali presenti in gruppi di risorse diversi. E con una [VPN da sito](../vpn-gateway/vpn-gateway-site-to-site-create.md), è possibile creare un'architettura ibrida che si connette macchine virtuali con computer e le reti locale.

Reti virtuali è inoltre possibile aggiungere le macchine virtuali Azure a un dominio. Questo è l'unico modo per utilizzare l'autenticazione di Windows per SQL Server. Altri scenari di connessione richiedono l'autenticazione di SQL con nomi utente e password.

Se si usa il portale di effettuare il provisioning di un'immagine di macchina virtuale di SQL Server Manager delle risorse, le regole firewall appropriata per la comunicazione sulla rete virtuale sono il programma di installazione quando si seleziona **privato** per l'opzione di connettività SQL. Se non si tratta uno durante il provisioning, quindi è possibile configurare manualmente SQL Server e macchine virtuali seguendo i [passaggi da eseguire in questo articolo per configurare manualmente la connettività](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm). Ma se si intende configurare un ambiente di dominio e l'autenticazione di Windows, non è necessario utilizzare la procedura descritta in questo articolo per configurare l'autenticazione di SQL e gli account di accesso. Non anche necessario configurare le regole gruppo di sicurezza di rete per l'accesso tramite internet.

>[AZURE.NOTE] L'immagine di macchina virtuale per SQL Server Express edition non attiva automaticamente il protocollo TCP/IP. Per Express edition, è necessario utilizzare Gestione configurazione SQL Server per [attivare manualmente il protocollo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) dopo aver creato la macchina virtuale.

Supponendo che sono state configurate DNS della rete virtuale, è possibile connettersi all'istanza di SQL Server specificare il nome del computer di macchine Virtuali di SQL Server nella stringa di connessione. Inoltre, nell'esempio seguente presuppone che l'autenticazione di Windows anche è stato configurato e che l'utente è stato concesso l'accesso all'istanza di SQL Server.

    "Server=mysqlvm;Integrated Security=true"

Si noti che in questo scenario, è inoltre possibile specificare l'indirizzo IP del macchina virtuale.

## <a name="steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm"></a>Procedura per la configurazione manuale di connettività di SQL Server in macchine Virtuali un Azure

La procedura seguente viene illustrato come installare manualmente la connettività all'istanza di SQL Server e facoltativamente è possibile connettersi tramite internet con SQL Server Management Studio (Express). Si noti che molte di queste operazioni vengono eseguite automaticamente quando si selezionano le opzioni di connettività di SQL Server appropriate nel portale.

Prima di connettersi all'istanza di SQL Server da un'altra macchine Virtuali o da internet, è necessario completare le attività seguenti come descritto nelle sezioni che seguono:

- [Aprire le porte TCP in Windows firewall](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurare SQL Server per ascoltare il protocollo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurare SQL Server per l'autenticazione in modalità misti](#configure-sql-server-for-mixed-mode-authentication)
- [Creare gli accessi l'autenticazione di SQL Server](#create-sql-server-authentication-logins)
- [Configurare una regola gruppo di sicurezza di rete in ingresso](#configure-a-network-security-group-inbound-rule-for-the-vm)
- [Configurare un'etichetta di DNS per l'indirizzo IP pubblico](#configure-a-dns-label-for-the-public-ip-address)
- [Connettersi al motore di Database da un altro computer](#connect-to-the-database-engine-from-another-computer)

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Passaggi successivi

Per istruzioni insieme a questa procedura di connettività di provisioning, vedere [il Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md).

[Esplorare il percorso formativo](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) per SQL Server in macchine virtuali di Azure.

Per altri argomenti correlati all'esecuzione di SQL Server in macchine virtuali di Azure, vedere [SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).
