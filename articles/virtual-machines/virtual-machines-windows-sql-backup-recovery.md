<properties
    pageTitle="Backup e ripristino per SQL Server | Microsoft Azure"
    description="Considerazioni sulla copia di backup e ripristino per i database di SQL Server in macchine virtuali di Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth" />

# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Eseguire il backup e ripristino per SQL Server in macchine virtuali di Azure

## <a name="overview"></a>Panoramica

Il backup dei dati nel database di SQL Server è una parte importante della strategia per la protezione dalla perdita di dati a causa errori applicazione o utente. Ciò avviene in modo uniforme per SQL Server in esecuzione in Azure macchine ().

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Per SQL Server in esecuzione in macchine virtuali di Azure, è possibile utilizzare nativo backup e ripristino tecniche utilizzando dischi collegati per la destinazione dei file di backup. Tuttavia, è un limite per il numero di dischi che è possibile allegare a una macchina virtuale Azure, in base alle [dimensioni della macchina virtuale](virtual-machines-linux-sizes.md). È inoltre disponibile il sovraccarico di Gestione disco da prendere in considerazione.

A partire da SQL Server 2014, è possibile eseguire il backup e ripristinare allo spazio di archiviazione Blob di Microsoft Azure. SQL Server 2016 fornisce anche miglioramenti per questa opzione. Inoltre, per i file di database archiviati in archiviazione Blob di Microsoft Azure, SQL Server 2016 fornisce un'opzione di backup quasi istantanei e ripristino rapido tramite snapshot Azure. In questo articolo viene fornita una panoramica di queste opzioni e altre informazioni sono disponibili in [SQL Server Backup e ripristino con il servizio di archiviazione Blob di Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

>[AZURE.NOTE] Per una descrizione delle opzioni per il backup dei database di dimensioni molto grandi, vedere [Multi-Terabyte SQL Server Database di Backup strategie per macchine virtuali di Azure](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx).

Nelle sezioni seguenti includono informazioni specifiche per le diverse versioni di SQL Server supportati in una macchina virtuale Azure.

## <a name="sql-server-virtual-machines"></a>Macchine virtuali SQL Server

Se l'istanza di SQL Server è in esecuzione una macchina virtuale Azure, i file di database si trovano già sui dischi di dati di Azure. Questi dischi dinamica in archiviazione Blob Azure. I motivi per il backup del database e gli approcci si necessari in modo leggermente cambia. Tenere presente quanto segue. 

- Non è necessario eseguire il backup del database per garantire la protezione contro hardware o media perché Microsoft Azure fornisce tale protezione come parte del servizio di Microsoft Azure.

- È comunque necessario eseguire il backup del database, per garantire protezione contro errori dell'utente o per l'archiviazione, normativi o scopi amministrativi.

- È possibile archiviare il file di backup direttamente in Azure. Per ulteriori informazioni, vedere le sezioni seguenti che forniscono istruzioni per le diverse versioni di SQL Server.

## <a name="sql-server-2016"></a>SQL Server 2016

Microsoft SQL Server 2016 supporta la funzionalità di [backup e ripristino con BLOB Azure](https://msdn.microsoft.com/library/jj919148.aspx) disponibili in SQL Server 2014. Ma include anche i miglioramenti seguenti:

| Miglioramento 2016               | Dettagli                          |
|---------------------|-------------------------------|
| **Striping**              | Per eseguire il backup a archiviazione blob Microsoft Azure, SQL Server 2016 supporta il backup su più BLOB per abilitare il backup dei database di grandi dimensioni, fino a un massimo di 12,8 TB.      |
| **Backup snapshot**                | Tramite l'utilizzo di Azure istantanee, il Backup di File di SQL Server istantanea fornisce quasi istantaneo backup e ripristino rapido per il file di database archiviato utilizzando il servizio di archiviazione Blob Azure. Questa funzionalità consente di semplificare il backup e ripristinare i criteri. Snapshot di file di backup supporta anche punto di ripristino di tempo. Per ulteriori informazioni, vedere [Backup Snapshot per i file di Database in Azure](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx).   |
| **Gestire la pianificazione del Backup**            | SQL Server gestito Backup in Azure supporta ora le pianificazioni personalizzate. Per ulteriori informazioni, vedere [SQL Server gestito Backup a Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx).   |

Per un'esercitazione delle funzionalità di SQL Server 2016 quando si Usa archiviazione Blob Azure, vedere [Esercitazione: usare il servizio di archiviazione Blob di Microsoft Azure con database di SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx).

## <a name="sql-server-2014"></a>SQL Server 2014

SQL Server 2014 include i miglioramenti seguenti:

1. **Eseguire il backup e ripristino per Azure**:

 - *Backup di SQL Server su URL* è ora disponibile il supporto di SQL Server Management Studio. L'opzione per eseguire il backup su Azure è ora disponibile quando si utilizza Backup o ripristino attività o creazione guidata piano di manutenzione in SQL Server Management Studio. Per ulteriori informazioni, vedere [Backup di SQL Server all'URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
 - *Gestite Backup di SQL Server in Azure* include nuove funzionalità che consente la gestione di backup automatica. Questa operazione è particolarmente utile per l'automazione di gestione di backup per istanze di SQL Server 2014 in esecuzione in un computer Azure. Per ulteriori informazioni, vedere [SQL Server gestito Backup a Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx).
 - *Copia di Backup automatico* fornisce automazione aggiuntivi per attivare automaticamente *Gestiti Backup su SQL Server in Azure* su tutti i database nuovi o esistenti per una macchina virtuale Server SQL di Azure. Per ulteriori informazioni, vedere [Copia di Backup automatico per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-automated-backup.md).
 - Per una panoramica di tutte le opzioni per il Backup di SQL Server 2014 in Azure, vedere [SQL Server Backup e ripristino con il servizio di archiviazione Blob di Microsoft Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).

1. **Crittografia**: SQL Server 2014 supporta la crittografia dei dati quando si crea una copia di backup. Supporta diversi algoritmi di crittografia e l'utilizzo asimmetrici, una chiave o osf un certificato. Per ulteriori informazioni, vedere [La crittografia di Backup](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx).

## <a name="sql-server-2012"></a>SQL Server 2012

Per informazioni dettagliate su SQL Server Backup e ripristino in SQL Server 2012, vedere [eseguire il Backup e ripristino del database di SQL Server (SQL Server 2012)](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx).

A partire da SQL Server 2012 SP1 sull'aggiornamento cumulativo 2, è possibile effettuare il backup e ripristinare dal servizio di archiviazione Blob Azure. Questa caratteristica può essere utilizzata per eseguire il backup database di SQL Server su un Server SQL in esecuzione in una macchina virtuale Azure o un'istanza locale. Per ulteriori informazioni, vedere [SQL Server Backup e ripristino con il servizio di archiviazione Blob Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Alcuni dei vantaggi dell'utilizzo del servizio di archiviazione Blob Azure figurano la possibilità di ignorare il limite di 16 disco per dischi collegati, semplificarne la gestione, la disponibilità diretta dei file di backup da un'altra istanza dell'istanza di SQL Server in esecuzione in una macchina virtuale Azure o un'istanza locale per la migrazione o ripristino di emergenza. Per un elenco completo dei vantaggi all'uso di un servizio di archiviazione blob Azure per i backup di SQL Server, vedere la sezione *vantaggi* di [SQL Server Backup e ripristino con il servizio di archiviazione Blob Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Per informazioni sulla risoluzione dei problemi e procedure consigliate, vedere [eseguire il Backup e ripristinare le procedure consigliate (servizio di archiviazione Blob Azure)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx).

## <a name="sql-server-2008"></a>SQL Server 2008

Per SQL Server Backup e ripristino in SQL Server 2008 R2, vedere [backup e ripristino di database in SQL Server (SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx).

Per SQL Server Backup e ripristino in SQL Server 2008, vedere [backup e ripristino di database SQL Server (SQL Server 2008)](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx).

## <a name="next-steps"></a>Passaggi successivi

Se si prevede la distribuzione di SQL Server in macchine Virtuali un Azure, è possibile trovare indicazioni provisioning nell'esercitazione seguente: [il Provisioning di un computer di virtuale SQL Server in Azure Gestione risorse Azure](virtual-machines-windows-portal-sql-server-provision.md).

Sebbene backup e ripristino può essere utilizzati per eseguire la migrazione dei dati, esistono potenzialmente semplice percorsi di migrazione di dati di SQL Server in macchine Virtuali un Azure. Per una descrizione completa delle opzioni di migrazione e suggerimenti, vedere [migrazione di un Database di SQL Server in macchine Virtuali un Azure](virtual-machines-windows-migrate-sql.md).

Esaminare altre [risorse per l'esecuzione di SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).
