<properties 
    pageTitle="Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con il portale di Azure | Microsoft Azure" 
    description="Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure tramite il portale di Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="08/29/2016"
    ms.author="sstein"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-the-azure-portal"></a>Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con il portale di Azure


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


In questo articolo viene illustrato come avviare il controllo a un Database SQL secondario con il [portale di Azure](http://portal.azure.com). Per configurare la replica geografico, vedere [Configurare geografico-replica di Database SQL Azure](sql-database-geo-replication-portal.md).


## <a name="initiate-a-failover"></a>Avviare caso di errore

Il database secondario può essere cambiato per principale.  

1. In Esplora [portale Azure](http://portal.azure.com) al database primario in collaborazione replica geografico.
2. Scegliere **tutte le impostazioni**e il Database SQL > **Replica geografico**.
3. Nell'elenco **dei secondari** , selezionare il database che si desidera principale nuovo e fare clic su **Failover**.

    ![failover][2]

4. Fare clic su **Sì** per iniziare il failover.

Il comando passerà immediatamente il database secondario al ruolo di principale. 

Esiste un breve periodo in cui entrambi i database non sono disponibili (sull'ordine di secondi da 0 a 25) mentre i ruoli sono cambiati. Se il database primario include più database secondari, il comando verrà automaticamente riconfigurare altri database secondari per connettersi al nuovo principale. L'intera operazione dovrebbe eseguire meno di un minuto per completare in condizioni normali. 

>[AZURE.NOTE] Se il principale è in linea e il commit delle transazioni quando il comando perdita di dati può verificarsi.


## <a name="next-steps"></a>Passaggi successivi   

- Dopo il failover, verificare che i requisiti di autenticazione per server e database configurati per il nuovo database primario. Per informazioni dettagliate, vedere [protezione del Database di SQL dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).
- Per informazioni su ripristino in seguito emergenza utilizzando geografico Active-replica, tra cui pre e registrare della procedura di ripristino ed eseguire un drill di ripristino di emergenza, vedere [Esercitazioni di ripristino di emergenza](sql-database-disaster-recovery.md)
- Per un post di blog di Sasha Nosov sulle Active geografico replica vedere [riflettore su nuove funzionalità di replica geografico](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Per informazioni sulla progettazione di applicazioni cloud per l'utilizzo della replica geografico attiva, vedere [Progettazione di applicazioni cloud per la continuità aziendale geografico replica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Per informazioni sull'utilizzo della replica geografico attivo con pool di database flessibile, vedere [strategie di ripristino di emergenza Pool flessibile](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Per una panoramica del continurity business, vedere [Panoramica di continuità aziendale](sql-database-business-continuity.md)




<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png
