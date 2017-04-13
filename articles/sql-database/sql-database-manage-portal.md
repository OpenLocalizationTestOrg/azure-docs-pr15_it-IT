<properties
    pageTitle="Gestire il Database di SQL Azure tramite il portale di Azure | Microsoft Azure"
    description="Informazioni su come utilizzare il portale di Azure per gestire un database relazionale nel cloud tramite il portale di Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.date="09/19/2016"
    ms.author="sstein"/>


# <a name="managing-azure-sql-databases-using-the-azure-portal"></a>Gestione dei database di SQL Azure tramite il portale di Azure


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-manage-portal.md)
- [SQL SERVER MANAGEMENT STUDIO](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

Il [portale di Azure](https://portal.azure.com/) consente di creare, monitorare e gestire server e database di SQL Azure. Questo articolo offre una rapida descrizione e collegamenti per i dettagli dell'attività più comuni.

## <a name="view-your-azure-sql-databases-servers-and-pools"></a>Visualizzare il database SQL Azure, server e pool

Per visualizzare i servizi di Database SQL disponibili, fare clic su **altri servizi**e digitare **SQL** nella casella di ricerca:

![Database SQL](./media/sql-database-manage-portal/sql-services.png)


## <a name="how-do-i-create-or-view-azure-sql-databases"></a>Come creare o visualizzare i database di SQL Azure

Per aprire e il **database SQL** , fare clic su **database SQL**, quindi fare clic sul database che si desidera utilizzare o fare clic su **Aggiungi +** per creare un database SQL. Per informazioni dettagliate, vedere [creare un database SQL in minuti tramite il portale di Azure](sql-database-get-started.md).


![Database SQL](./media/sql-database-manage-portal/sql-databases.png)


## <a name="how-do-i-create-or-view-azure-sql-servers"></a>Come creare o visualizzare i server di SQL Azure

Per aprire e il **server SQL Server** , fare clic su **SQL Server**, quindi fare clic sul server da utilizzare oppure fare clic su **Aggiungi +** per creare un server SQL. Per informazioni dettagliate, vedere [creare un database SQL in minuti tramite il portale di Azure](sql-database-get-started.md).

![SQL Server](./media/sql-database-manage-portal/sql-servers.png)


## <a name="how-do-i-create-or-view-sql-elastic-pools"></a>Come creare o visualizzare i pool flessibile SQL

Per aprire e il **pool flessibile SQL** , fare clic su **pool flessibile SQL**, quindi fare clic su pool da utilizzare o fare clic su **Aggiungi +** per creare un pool. Per informazioni dettagliate, vedere [creare un pool di database flessibile con il portale di Azure](sql-database-elastic-pool-create-portal.md).

![Pool flessibile SQL](./media/sql-database-manage-portal/elastic-pools.png)



## <a name="how-do-i-update-or-view-sql-database-settings"></a>Come aggiornare o visualizzare le impostazioni di database SQL

Per visualizzare o aggiornare le impostazioni del database, fare clic nella posizione desiderata nella e database SQL:


![Impostazioni di database SQL](./media/sql-database-manage-portal/settings.png)


## <a name="how-do-i-find-a-sql-databases-fully-qualified-server-name"></a>Come è possibile trovare il nome del server completo database SQL?

Per visualizzare il nome del server di database, fare clic su **informazioni generali** sulla e **database SQL** e notare il nome del server:


![Impostazioni di database SQL](./media/sql-database-manage-portal/server-name.png)


## <a name="how-do-i-manage-firewall-rules-to-control-access-to-my-sql-server-and-database"></a>Come è possibile gestire le regole del firewall per controllare l'accesso su SQL server e database?

Per visualizzare, creare o aggiornare le regole firewall, scegliere **Imposta firewall server** e il **database di SQL** . Per informazioni dettagliate, vedere [configurare una regola firewall a livello di server di Database SQL Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md).


![regole firewall](./media/sql-database-manage-portal/sql-database-firewall.png)


## <a name="how-do-i-change-my-sql-database-service-tier-or-performance-level"></a>Come è possibile modificare il SQL database servizio livello o prestazioni livello?


Per aggiornare il livello di livello o le prestazioni del servizio di un database SQL, scegliere **il livello di prezzo (scala DTUs)** e il **database di SQL** . Per informazioni dettagliate, vedere [modificare il livello e prestazioni livello di servizio (prezzi livello) di un database SQL](sql-database-scale-up.md).


![prezzi livelli](./media/sql-database-manage-portal/pricing-tier.png)


## <a name="how-do-i-configure-auditing-and-threat-detection-for-a-sql-database"></a>Come configurare il controllo e delle minacce rilevamento per un database SQL

Per configurare il controllo e rischio rilevamento per un database SQL, scegliere **il rilevamento dei rischi e controllo** e il **database di SQL** . Per informazioni dettagliate, vedere [iniziare a utilizzare il controllo dei database SQL](sql-database-auditing-get-started.md)e [iniziare a utilizzare Individuazione Database SQL](sql-database-threat-detection-get-started.md).


## <a name="how-do-i-configure-dynamic-data-masking-for-a-sql-database"></a>Configurazione di dati dinamici masking per un database SQL

Per configurare dati dinamici masking per un database SQL, scegliere **dati dinamici masking** e il **database di SQL** . Per informazioni dettagliate, vedere [Introduzione a SQL Database dinamico dati Masking](sql-database-dynamic-data-masking-get-started.md).


## <a name="how-do-i-configure-transparent-data-encryption-tde-for-a-sql-database"></a>Configurazione di crittografia dati trasparente (TDE) per un database SQL

Per configurare la crittografia dei dati trasparente per un database SQL, scegliere **crittografia dati trasparente** e il **database di SQL** . Per informazioni dettagliate, vedere [Abilitare TDE in un database tramite il portale](https://msdn.microsoft.com/library/dn948096#Anchor_1).

## <a name="how-do-i-view-or-change-the-max-size-of-a-sql-database"></a>Come visualizzare o modificare la dimensione massima di un database SQL

Per visualizzare o modificare le dimensioni di un database SQL, scegliere **dimensioni del Database** e il **database di SQL** . Aggiornare la dimensione massima di un database mediante la modifica del livello di livello o le prestazioni del servizio. Per informazioni dettagliate, vedere [modificare il livello e prestazioni livello di servizio (prezzi livello) di un database SQL](sql-database-scale-up.md).

## <a name="how-do-i-monitor-and-improve-the-performance-of-a-sql-database"></a>Come monitorare e migliorare le prestazioni di un database SQL

Per monitorare e migliorare le prestazioni caratteristiche di un database SQL, scegliere **Cenni preliminari sulle prestazioni** e il **database di SQL** . Per informazioni dettagliate, vedere [Analisi delle prestazioni di Database SQL](sql-database-performance.md).


## <a name="how-do-i-configure-geo-replication"></a>Configurazione di replica geografico

Per configurare la replica geografico per un database SQL, scegliere **Replica geografico** e il **database di SQL** . Per informazioni dettagliate, vedere [Configurare geografico-replica di Database di SQL Azure con il portale di Azure](sql-database-geo-replication-portal.md).


## <a name="how-do-i-failover-to-a-geo-replicated-sql-database"></a>Come si il controllo a un database SQL replicato geografico?

Per il controllo a secondario replicato geografico, scegliere **Replica geografico** e il **database di SQL** , quindi fare clic su **Failover**. Per informazioni dettagliate, vedere [avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con il portale di Azure](sql-database-geo-replication-failover-portal.md).


## <a name="how-do-i-copy-a-sql-database"></a>Come è possibile copiare un database SQL?

Per copiare un database SQL, fare clic su **Copia** in e il **database di SQL** . Per informazioni dettagliate, vedere [copiare un database di SQL Azure tramite il portale di Azure](sql-database-copy-portal.md).


![Impostazioni di database SQL](./media/sql-database-manage-portal/sql-database-copy.png)

## <a name="how-do-i-archive-an-azure-sql-database-to-a-bacpac-file"></a>Come archiviare un database di SQL Azure in un file BACPAC

Per creare un BACPAC di un database SQL, fare clic su **Esporta** in e il **database di SQL** . Per informazioni dettagliate, vedere [archiviare un database di SQL Azure in un file BACPAC tramite il portale di Azure](sql-database-export.md).


![Esportazione di database SQL](./media/sql-database-manage-portal/sql-database-export.png)



## <a name="how-do-i-restore-a-sql-database-to-a-previous-point-in-time"></a>Come è possibile ripristinare un database SQL in un punto precedente nel tempo?

Per ripristinare un database SQL, fare clic su **Ripristina** nella e **database di SQL** . Per informazioni dettagliate, vedere [ripristinare un Database di SQL Azure in un momento precedente con il portale di Azure](sql-database-point-in-time-restore-portal.md).


![Impostazioni di database SQL](./media/sql-database-manage-portal/sql-database-restore.png)


## <a name="how-do-i-create-an-azure-sql-database-from-a-bacpac-file"></a>Creazione di un database SQL Azure da un file BACPAC

Per creare un database SQL di un file BACPAC, scegliere **database di importazione** e il **server SQL** . Per informazioni dettagliate, vedere [importare un file BACPAC per creare un database SQL Azure](sql-database-import.md).


![Server SQL](./media/sql-database-manage-portal/server-commands.png)


## <a name="how-do-i-restore-a-deleted-sql-database"></a>Come è possibile ripristinare un database SQL di eliminati?

Per ripristinare un database SQL di eliminata, scegliere **l'eliminazione dei database** e il **server SQL** (SQL server che conteneva il database è stato eliminato). Per informazioni dettagliate, vedere [ripristinare un database SQL Azure eliminato tramite il portale di Azure](sql-database-restore-deleted-database-portal.md).

## <a name="how-do-i-delete-a-sql-database"></a>Come eliminare un database SQL

Per eliminare un database SQL, scegliere **Elimina** dal e **database di SQL** . 

![Impostazioni di database SQL](./media/sql-database-manage-portal/sql-database-delete.png)



## <a name="additional-resources"></a>Risorse aggiuntive

- [Database SQL](sql-database-technical-overview.md)
- [Monitorare e gestire un pool di database flessibile con il portale di Azure](sql-database-elastic-pool-manage-portal.md)
