<properties
    pageTitle="Considerazioni sulla protezione per SQL Server in macchine Azure | Microsoft Azure"
    description="In questo argomento si riferisce alle risorse create con il modello di distribuzione classica e vengono fornite indicazioni generali per la protezione di SQL Server in esecuzione in una macchina virtuale Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
   editor=""    
   tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="06/24/2016"
    ms.author="jroth" />

# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Considerazioni sulla protezione per SQL Server in macchine virtuali di Azure
 
In questo argomento include complessiva linee guida di sicurezza che consentono di stabilire l'accesso sicuro a istanze di SQL Server in macchine Virtuali un Azure. Tuttavia, per garantire la protezione più efficaci per le istanze di database SQL Server in Azure, è consigliabile implementare consigliate per la protezione locale tradizionale oltre alle procedure consigliate per Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Per ulteriori informazioni sulle procedure di protezione SQL Server, vedere [SQL Server 2008 R2 le procedure consigliate - operative e le attività amministrative](http://download.microsoft.com/download/1/2/A/12ABE102-4427-4335-B989-5DA579A4D29D/SQL_Server_2008_R2_Security_Best_Practice_Whitepaper.docx)

Azure conformità con diverse normative del settore e standard che consente di creare una soluzione compatibile con SQL Server in esecuzione in una macchina virtuale. Per informazioni sulla conformità alle normative con Azure, vedere [Centro protezione di Azure](https://azure.microsoft.com/support/trust-center/).

Di seguito è un elenco di suggerimenti di protezione da prendere in considerazione la configurazione e la connessione all'istanza di SQL Server in macchine Virtuali un Azure.

## <a name="considerations-for-managing-accounts"></a>Considerazioni per la gestione degli account:

- Creare un account di amministratore locale univoco che non ha un nome **amministratore**.

- Utilizzare password complesse per tutti gli account. Per ulteriori informazioni su come creare una password complessa, vedere l'articolo [suggerimenti per la creazione di una password](http://windows.microsoft.com/en-us/windows-vista/Tips-for-creating-a-strong-password) .

- Per impostazione predefinita, Azure seleziona l'autenticazione di Windows durante l'installazione di SQL Server virtuale computer. Pertanto, l'accesso **SA** viene disabilitato e dal programma di installazione è stata assegnata una password. È consigliabile che l'account di accesso **dell'amministratore di sistema** non venga utilizzato o attivata. Di seguito sono strategie alternative se non si desidera un accesso SQL:
    - Creare un account SQL che appartengono a sysadmin.
    - Se è necessario utilizzare un account di accesso **dell'amministratore di sistema** , abilitare l'accesso e assegnarle il nome e assegnare una nuova password.
    - Entrambe le opzioni descritte in precedenza è necessario modificare la modalità di autenticazione di **SQL Server e la modalità di autenticazione di Windows**. Per ulteriori informazioni, vedere [Modalità di autenticazione Server di modifica](https://msdn.microsoft.com/library/ms188670.aspx).

## <a name="considerations-for-securing-connections-to-azure-virtual-machine"></a>Considerazioni per la protezione delle connessioni a macchina virtuale Azure:

- Considerare l'utilizzo di [Azure virtuali](../virtual-network/virtual-networks-overview.md) per amministrare macchine virtuali anziché porte RDP pubbliche.

- Utilizzare un [Gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) (NSG) per consentire o impedire il traffico di rete per la macchina virtuale. Se si vuole usare un NSG e disporre di un estremo ACL già, rimuovere l'endpoint ACL. Per informazioni su come eseguire questa operazione, vedere [Gestione ACL () per gli endpoint tramite PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

- Se si usano i punti finali, rimuovere endpoint sulla macchina virtuale se non si utilizza. Per ulteriori informazioni sull'utilizzo di ACL con i punti finali, vedere [gestione ACL in un punto finale](../virtual-network/virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint).

- Abilitare un'opzione di connessione crittografata per un'istanza del motore di Database SQL Server in macchine virtuali di Azure. Configurare l'istanza di SQL server con un certificato autofirmato. Per ulteriori informazioni, vedere [Attivazione di connessioni crittografate al motore di Database](https://msdn.microsoft.com/library/ms191192.aspx) e [Sintassi della stringa di connessione](https://msdn.microsoft.com/library/ms254500.aspx).

- Se le macchine virtuali devono avvenire solo da una rete specifica, utilizzare Windows Firewall per limitare l'accesso a determinati indirizzi IP o subnet di rete.

## <a name="next-steps"></a>Passaggi successivi

Se si intende anche procedure consigliate in relazione alle prestazioni, vedere [Prestazioni procedure consigliate per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-performance.md).

Per altri argomenti correlati all'esecuzione di SQL Server in macchine virtuali di Azure, vedere [SQL Server in macchine virtuali di Azure Panoramica](virtual-machines-windows-sql-server-iaas-overview.md).
