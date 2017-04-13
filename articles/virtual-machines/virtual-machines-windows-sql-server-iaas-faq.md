<properties
    pageTitle="SQL Server in macchine virtuali Azure domande frequenti su | Microsoft Azure"
    description="In questo articolo vengono fornite le risposte alle domande frequenti sull'esecuzione di SQL Server in macchine virtuali di Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="sql-server-on-azure-virtual-machines-faq"></a>SQL Server in macchine virtuali Azure domande frequenti

In questo argomento vengono fornite le risposte ad alcune domande più frequenti sull'esecuzione di [SQL Server in macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Domande frequenti

1. **Creazione di una macchina virtuale Azure con SQL Server**

    Esistono due modi per eseguire questa operazione. La soluzione più semplice consiste nel creare una macchina virtuale che include SQL Server. Per un'esercitazione su iscrizione Azure e la creazione di una VM SQL dal portale, vedere [disposizione una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md). È inoltre l'opzione di installazione di SQL Server in una macchina virtuale e riutilizzo di una licenza locale con [Licenza mobilità tramite Software Assurance su Azure](https://azure.microsoft.com/pricing/license-mobility/)manualmente.

1. **Che cos'è la differenza tra macchine virtuali di SQL e il servizio di Database SQL?**

    Livello concettuale, che esegue SQL Server in un computer virtuale Azure che non è diverso da SQL Server in esecuzione in un Data Center remoto. [Database SQL](../sql-database/sql-database-technical-overview.md) offre invece database come servizio. Database SQL, non ha accesso ai computer che ospita i database. Per un confronto completo, vedere [scegliere un'opzione di SQL Server cloud: Database SQL Azure (PaaS) o SQL Server in macchine virtuali di Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Come è possibile migrare il database di SQL Server in locale nel cloud?**

    Creare innanzitutto una macchina virtuale Azure con un'istanza di SQL Server. Quindi eseguire la migrazione dei database locale a tale istanza. Per strategie di migrazione dei dati, vedere [eseguire la migrazione di un database di SQL Server a SQL Server in macchine Virtuali un Azure](virtual-machines-windows-migrate-sql.md).

2. **È possibile modificare le funzionalità installate o installare una seconda istanza di SQL Server nella stessa macchina virtuale?**

    Sì. Supporto di installazione di SQL Server si trova in una cartella sull'unità **C** . Eseguire **Setup.exe** da tale posizione per aggiungere nuove istanze di SQL Server o per modificare altre caratteristiche di SQL Server installati nel computer.

3. **Come è l'aggiornamento alla nuova versione/edizione di SQL Server in macchine Virtuali un Azure?**

    Attualmente non esiste un aggiornamento sul posto per SQL Server in esecuzione in una macchina virtuale Azure. Creare una nuova macchina virtuale Azure con la versione/edizione di SQL Server desiderato e quindi eseguire la migrazione dei database nel nuovo server utilizzando standard [tecniche di migrazione di dati](virtual-machines-windows-migrate-sql.md).

4. **Come è possibile installare la copia con licenza di SQL Server in macchine Virtuali un Azure?**

    Copiare il supporto di installazione di SQL Server in macchine Virtuali di Server Windows e quindi installare SQL Server nella macchina virtuale. Per motivi di gestione delle licenze, è necessario disporre [Mobilità licenza tramite Software Assurance su Azure](https://azure.microsoft.com/pricing/license-mobility/).

5. **È necessario pagare i costi SQL di una macchina virtuale se viene utilizzato solo per standby/failover?**

    Se si sta creando VM SQL attraverso la raccolta, è necessario disporre di una licenza distinta per standby VM SQL e i prezzi corrisponde. Se si installa manualmente SQL Server in una macchina virtuale con [Licenza mobilità](https://azure.microsoft.com/pricing/license-mobility/), è possibile avere una sola istanza SQL passiva gratuita per failover. Esaminare i requisiti e restrizioni.

6. **Come vengono applicati gli aggiornamenti e service pack in una macchina virtuale Server SQL?**

    Macchine virtuali consentono di controllare il computer host, incluso quando e come applicare gli aggiornamenti. Per il sistema operativo, è possibile applicare manualmente gli aggiornamenti di windows oppure è possibile attivare un servizio di pianificazione denominato [Automatizzato patch](virtual-machines-windows-classic-sql-automated-patching.md). Automatizzare installazioni applicazione di patch eventuali aggiornamenti contrassegnati come importanti, inclusi gli aggiornamenti di SQL Server in quella categoria. Altri aggiornamenti facoltativi a SQL Server devono essere installati manualmente.

7. **È possibile impostare le configurazioni non visualizzate nella raccolta macchina virtuale (ad esempio Windows 2008 R2 + SQL Server 2012)?**

    No. Per le immagini di raccolta macchina virtuale che includono SQL Server, è necessario selezionare una delle immagini fornite.

9. **Come installa gli strumenti di dati di SQL in macchine Virtuali personale Azure?**

    Scaricare e installare gli strumenti di dati SQL di [Microsoft SQL Server Data Tools - Business Intelligence per Visual Studio 2013 ](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Risorse

Per una panoramica di SQL Server in macchine virtuali di Azure, guardare il video [macchine Virtuali di Azure è la piattaforma migliore per SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016). È inoltre possibile ottenere una buona introduzione nell'argomento, [SQL Server in macchine virtuali di Azure Panoramica](virtual-machines-windows-sql-server-iaas-overview.md).

Altre risorse includono:

- [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md)
- [Eseguire la migrazione di un Database di SQL Server in una macchina virtuale Azure](virtual-machines-windows-migrate-sql.md)
- [Disponibilità e ripristino per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md)
- [Prestazioni procedure consigliate per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-performance.md)
- [Modelli di applicazione e strategie di sviluppo per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)
