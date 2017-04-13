<properties
    pageTitle="Panoramica di SQL Server in macchine virtuali di Azure | Microsoft Azure"
    description="Informazioni su come eseguire complete edizioni di SQL Server in macchine virtuali di Azure. Recuperare i collegamenti diretti a tutte le immagini di macchine Virtuali di SQL Server e del relativo contenuto."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/19/2016"
    ms.author="jroth"/>

# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Panoramica di SQL Server in macchine virtuali di Azure

In questo argomento descrive le opzioni per l'esecuzione di SQL Server su Azure macchine (), oltre a [collegamenti a immagini portale](#option-1-create-a-sql-vm-with-per-minute-licensing) e una panoramica delle [attività comuni](#manage-your-sql-vm).

>[AZURE.NOTE] Se si ha già familiarità con SQL Server e si vuole semplicemente per informazioni su come distribuire una macchina virtuale Server SQL, vedere [disposizione una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="overview"></a>Panoramica
Se si è un amministratore di database o uno sviluppatore, macchine virtuali di Azure consente di spostare i carichi di lavoro di SQL Server locale e le applicazioni nel cloud. Il video seguente offre una panoramica tecnica di macchine virtuali di SQL Server Azure.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

Il video illustra le aree seguenti:

|Ora|Area|
|---|---|
| 00:21 | Quali sono macchine virtuali di Azure? |
| 01:45 | Sicurezza |
| 02:50 | Connettività |
| 03:30 | Prestazioni e affidabilità dell'archiviazione |
| 05:20 | Dimensioni di macchine Virtuali |
| 05:54 | Disponibilità e contratto di servizio |
| 07:30 | Supporto per la configurazione |
| 08:00 | Monitoraggio |
| 08:32 | Demo: Creare una macchina virtuale di SQL Server 2016 |

>[AZURE.NOTE] Il video è incentrata su SQL Server 2016, ma Azure fornisce le immagini di macchine Virtuali per molte versioni di SQL Server, inclusi 2008, 2012, 2014 e 2016. 

## <a name="scenarios"></a>Scenari
Esistono diversi motivi che è possibile scegliere di ospitare i dati in Azure. Se l'applicazione viene spostato in Azure, migliora le prestazioni e anche spostare i dati. Ma ci sono altri vantaggi. Automaticamente, è possibile accedere a più centri di dati per un globale sulla presenza e ripristino di emergenza. I dati sono inoltre altamente protetta e permanenti.

SQL Server in macchine virtuali di Azure è una delle opzioni per l'archiviazione dei dati relazionali in Azure. È consigliabile per diversi scenari. È consigliabile, ad esempio, configurare la macchina virtuale Azure come Analogamente possibili per un computer di SQL Server locale. Oppure, è consigliabile eseguire ulteriori applicazioni e servizi nello stesso server di database. Esistono due risorse principale che consentono di valutare tramite anche altre considerazioni e scenari:

 - [SQL Server in macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) viene fornita una panoramica delle procedure scenari per l'uso di SQL Server in macchine virtuali di Azure. 
 - [Scegliere un'opzione di SQL Server cloud: Database SQL Azure (PaaS) o SQL Server in macchine virtuali di Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) fornisce un confronto dettagliato tra Database SQL e SQL Server in esecuzione in una macchina virtuale.

## <a name="create-a-new-sql-vm"></a>Creare un nuovo VM SQL
Nelle sezioni seguenti sono disponibili collegamenti direttamente al portale di Azure per le immagini di raccolta macchina virtuale di SQL Server. A seconda delle dimensioni dell'immagine selezionata, è possibile uno retributiva per SQL Server licenze dei costi in base al minuto oppure è possibile importare la propria licenza (BYOL).

Trovare istruzioni dettagliate per questo processo nell'esercitazione, [disposizione una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md). Inoltre, esaminare le [prestazioni ottimali macchine virtuali di SQL Server](virtual-machines-windows-sql-performance.md), che spiega come selezionare le dimensioni del computer appropriato e altre caratteristiche disponibili durante il provisioning.

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>Opzione 1: Creare una VM SQL con licenze al minuto
Nella tabella seguente fornisce una matrice di immagini di SQL Server disponibili nella raccolta macchina virtuale. Fare clic sul collegamento qualsiasi per iniziare a creare un nuovo VM SQL con la versione specificata, edition e sistema operativo.

|Versione|Sistema operativo|Edition|
|---|---|---|
|**SQL 2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Dev](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL 2012 SP3**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## <a name="option-2-create-a-sql-vm-with-an-existing-license"></a>Opzione 2: Creare una VM SQL con una licenza esistente
È anche possibile importare la propria licenza (BYOL). In questo scenario di pagare solo macchine Virtuali senza costi aggiuntivi per la gestione delle licenze di SQL Server. Per utilizzare il proprio licenza, utilizzare la matrice di versioni di SQL Server, edizioni e sistemi operativi seguenti. Nel portale, i nomi seguenti immagine sono preceduti da **{BYOL}**.

|Versione|Sistema operativo|Edition|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

> [AZURE.IMPORTANT] Per utilizzare immagini BYOL VM, è necessario disporre e Microsoft Enterprise Agreement con [Licenza mobilità tramite Software Assurance su Azure](https://azure.microsoft.com/pricing/license-mobility/). È necessario anche una licenza valida per la versione di SQL Server che si desidera utilizzare. È necessario [fornire le informazioni necessarie BYOL a Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) entro **10** giorni di provisioning di una macchina virtuale.

## <a name="manage-your-sql-vm"></a>Gestire le macchine Virtuali SQL
Dopo il provisioning di una macchina SQL Server virtuale, sono disponibili numerose operazioni di gestione facoltativo. In molti aspetti, configurare e gestire SQL Server esattamente come vengono gestiti un'istanza di SQL Server locale. Tuttavia, alcune attività sono specifici di Azure. Nelle sezioni seguenti vengono illustrano alcune di queste aree con collegamenti a ulteriori informazioni.

### <a name="connect-to-the-vm"></a>Connettere la macchina virtuale
Una delle operazioni di gestione più semplice consiste nel connettere la macchina virtuale Server SQL tramite strumenti, ad esempio SQL Server Management Studio (SQL Server Management Studio). Per istruzioni su come connettersi alla nuova macchina virtuale di SQL Server, vedere [connettersi a un SQL Server virtuale computer Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Eseguire la migrazione dei dati
Se si dispone di un database esistente, verranno da spostare che per VM SQL appena provisioning. Per un elenco di opzioni di migrazione e indicazioni, vedere [migrazione di un Database di SQL Server in macchine Virtuali un Azure](virtual-machines-windows-migrate-sql.md).

### <a name="configure-high-availability"></a>Configurare la disponibilità elevata
Se si richiede la disponibilità, prendere in considerazione la configurazione dei gruppi di disponibilità di SQL Server. Questa operazione comporta più macchine virtuali di Azure in una rete virtuale. Portale di Azure dispone di un modello che consente di impostare questa configurazione dell'utente. Per ulteriori informazioni, vedere [configurare un gruppo di disponibilità AlwaysOn in macchine virtuali di gestione risorse di Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). Se si desidera configurare manualmente il gruppo di disponibilità e comunicare ascoltatore associato, vedere [Configurare di disponibilità AlwaysOn in macchine Virtuali di Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Per ulteriori informazioni sulla disponibilità, vedere [disponibilità e ripristino per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md).

### <a name="back-up-your-data"></a>Il backup dei dati
Azure macchine virtuali possono usufruire di [Backup automatico](virtual-machines-windows-sql-automated-backup.md), che consente di creare regolarmente backup del database a archiviazione blob. Utilizzare questa tecnica anche manualmente. Per ulteriori informazioni, vedere [Utilizzare lo spazio di archiviazione Azure per SQL Server Backup e ripristino](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Per una panoramica di tutte le opzioni di backup e ripristino, vedere [eseguire il Backup e ripristino per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-backup-recovery.md).

### <a name="automate-updates"></a>Automatizzare gli aggiornamenti
Azure macchine virtuali è possono utilizzare [Automatizzato patch](virtual-machines-windows-sql-automated-patching.md) per pianificare una finestra di manutenzione per l'installazione di windows importanti e automaticamente gli aggiornamenti di SQL Server.

### <a name="customer-experience-improvement-program-ceip"></a>Programma Analisi utilizzo software (analisi utilizzo software)
L'analisi utilizzo software programma () è attivata per impostazione predefinita. Report periodicamente invia a Microsoft per contribuire a migliorare SQL Server. Non esiste alcun attività di gestione necessari con analisi utilizzo software, a meno che non si desidera disabilitare dopo il provisioning. È possibile personalizzare o disattivare il programma Analisi utilizzo software connettendosi a macchine Virtuali con desktop remoto. Eseguire l'utilità di **errori e SQL Server report di utilizzo** . Seguire le istruzioni per disattivare la segnalazione. 

Per ulteriori informazioni, vedere la sezione Analisi utilizzo software dell'argomento di [Accettare condizioni di licenza](https://msdn.microsoft.com/library/ms143343.aspx) . 

## <a name="next-steps"></a>Passaggi successivi
[Esplorare il percorso formativo](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) per SQL Server in macchine virtuali di Azure.

Altre domande? Prima di tutto, vedere [SQL Server in domande frequenti su macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-faq.md). Ma anche aggiungere le domande e commenti nella parte inferiore di qualsiasi argomenti SQL VM per interagire con Microsoft e della community.
