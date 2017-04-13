<properties 
    pageTitle="Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con PowerShell | Microsoft Azure" 
    description="Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="08/29/2016"
    ms.author="sstein"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-powershell"></a>Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con PowerShell



> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


In questo articolo viene illustrato come avviare caso di errore pianificato o non pianificato per il Database di SQL con PowerShell. Per configurare la replica geografico, vedere [Configurare geografico-replica di Database SQL Azure](sql-database-geo-replication-powershell.md).



## <a name="initiate-a-planned-failover"></a>Avviare pianificato in caso di errore

Utilizzare il cmdlet **Set-AzureRmSqlDatabaseSecondary** con il parametro **- Failover** per convertire un database secondario per diventare il nuovo database primario abbassare di livello principale esistente per diventare secondario. Questa funzionalità è progettata per pianificati in caso di errore, ad esempio durante esercitazioni di ripristino di emergenza e necessario che il database primario sia disponibile.

Il comando esegue il flusso di lavoro seguente:

1. Passare temporaneamente replica alla modalità icona del. In questo modo tutte le transazioni in sospeso a scaricati secondario.

2. Cambiare i ruoli dei due database in relazione replica geografico.  

Questa sequenza garantisce che i due database vengono sincronizzati prima di passare i ruoli e pertanto non verrà eseguita alcuna perdita di dati. Esiste un breve periodo in cui entrambi i database non sono disponibili (sull'ordine di secondi da 0 a 25) mentre i ruoli sono cambiati. L'intera operazione dovrebbe eseguire meno di un minuto per completare in condizioni normali. Per ulteriori informazioni, vedere [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx).




Questo cmdlet restituirà al termine del processo di passaggio del database secondario a primaria.

Il comando seguente consente di attivare i ruoli del database denominato "mydb" nel server "srv2" in gruppo di risorse "rg2" su principale. Principale originale a cui "db2" è connesso a passerà alla secondario dopo due database sono sincronizzati completamente.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover


> [AZURE.NOTE] In casi è possibile che l'operazione non è possibile completare e possono essere visualizzati non risponda. In questo caso l'utente può chiamare il comando failover forza (failover non pianificato) e accettare la perdita di dati.


## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Avviare un failover non pianificato del database primario per il database secondario


È possibile utilizzare il cmdlet **Set-AzureRmSqlDatabaseSecondary** con i parametri **-Failover** e **- AllowDataLoss** per alzare di livello un database secondario per diventare il nuovo database primario in modo non pianificato forzare la riduzione di livello di primario esistente a diventeranno secondaria quando il database primario non è più disponibile.

Questa funzionalità è progettata per il ripristino di emergenza durante il ripristino di disponibilità del database critico e perdita di dati accettabile. Quando si richiama failover forzato, il database secondario specificato immediatamente diventa il database principale e inizia ad accettare le transazioni in scrittura. Non appena il database primario originale è in grado di riconnettersi con il nuovo database primario dopo l'operazione di failover forzata, un backup incrementale viene accettato al database principale originale e il vecchio database principale è una chiamata effettuato in un database secondario per il nuovo database primario; Successivamente, è semplicemente una replica della nuova principale.

Ma poiché punto nel tempo ripristinare non è supportata nei database secondari, se si desidera dati ripristino approvati al database principale precedente che non è stato replicato nel nuovo database principale, che si deve prevede CSS per ripristinare un database per il backup del log noti.

> [AZURE.NOTE] Se il comando quando principale e secondarie sono online principale precedente diventerà la nuova scheda secondaria immediatamente senza la sincronizzazione dei dati. Se non è primario possono verificarsi commie transazioni quando il comando perdita di dati.


Se il database principale più database secondari viene utilizzato il comando parzialmente avrà esito positivo. Secondario in cui è stato eseguito il comando diventa principale. Principale precedente tuttavia rimarrà principale, ovvero i due colori finiranno in stato incoerente e connessi mediante un collegamento di replica sospeso. L'utente avrà ripristinare manualmente questa configurazione tramite un'API "Rimuovi secondario" in uno di questi database principali.


Il comando seguente consente di attivare i ruoli del database denominato "mydb" su principale quando primario non è disponibile. Principale originale a cui "mydb" è connesso a passerà alla secondario dopo che è in modalità online. A questo punto la sincronizzazione potrebbe causare la perdita di dati.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss




## <a name="next-steps"></a>Passaggi successivi   

- Dopo il failover, verificare che i requisiti di autenticazione per server e database configurati per il nuovo database primario. Per informazioni dettagliate, vedere [protezione del Database di SQL dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).
- Per informazioni su ripristino in seguito emergenza utilizzando geografico Active-replica, tra cui pre e registrare della procedura di ripristino ed eseguire un drill di ripristino di emergenza, vedere [Esercitazioni di ripristino di emergenza](sql-database-disaster-recovery.md)
- Per un post di blog di Sasha Nosov sulle Active geografico replica vedere [riflettore su nuove funzionalità di replica geografico](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Per informazioni sulla progettazione di applicazioni cloud per l'utilizzo della replica geografico attiva, vedere [Progettazione di applicazioni cloud per la continuità aziendale geografico replica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Per informazioni sull'utilizzo della replica geografico attivo con pool di database flessibile, vedere [strategie di ripristino di emergenza Pool flessibile](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Per una panoramica del continurity business, vedere [Panoramica di continuità aziendale](sql-database-business-continuity.md)
