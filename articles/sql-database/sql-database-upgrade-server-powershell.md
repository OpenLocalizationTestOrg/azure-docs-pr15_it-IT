<properties
    pageTitle="Eseguire l'aggiornamento a V12 di Database SQL Azure con PowerShell | Microsoft Azure"
    description="Spiega come eseguire l'aggiornamento a V12 di Database SQL Azure compresa la procedura aggiornare i database Web e Business e come eseguire l'aggiornamento di un server V11 la migrazione dei database direttamente in un pool di database flessibile tramite PowerShell."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="sstein"/>

# <a name="upgrade-to-azure-sql-database-v12-using-powershell"></a>Eseguire l'aggiornamento a V12 di Database SQL Azure con PowerShell


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


SQL Database V12 è la versione più recente in modo che l'aggiornamento a SQL Database V12 è consigliabile.
SQL Database V12 sono inclusi molti [vantaggi rispetto alla versione precedente](sql-database-v12-whats-new.md) :

- Maggiore compatibilità con SQL Server.
- Prestazioni migliorate premium e nuovi livelli di prestazioni.
- [Pool di database flessibile](sql-database-elastic-pool.md).

In questo articolo vengono fornite istruzioni per l'aggiornamento esistente V11 Database di SQL Server e database a V12 di Database SQL.

Durante il processo di aggiornamento a V12, aggiornare i database Web e Business a un nuovo livello di servizio in modo che siano inclusi indicazioni per l'aggiornamento dei database Web e Business.

Inoltre, può essere più economico che l'aggiornamento a singole prestazioni (prezzi livelli) per i database singoli esecuzione della migrazione a un [pool di database flessibile](sql-database-elastic-pool.md) . Pool inoltre semplificano la gestione dei database perché è necessario gestire le impostazioni delle prestazioni per il pool anziché separatamente sulla gestione di livelli di prestazioni di singoli database. Se si dispone di database in più server, è consigliabile spostarli nello stesso server e sfruttare suddivisione in un pool.

È possibile eseguire i passaggi descritti in questo articolo per facilmente la migrazione dei database dal server V11 direttamente nel pool di database flessibile.

Si noti che i database rimarrà connesso e continuare a lavorare nel corso dell'operazione di aggiornamento. Al momento della transizione effettiva per il nuovo livello di prestazioni temporaneo eliminazione delle connessioni al database può verificarsi per una durata molto piccola che è in genere circa 90 secondi, ma può essere quanto più 5 minuti. Se l'applicazione ha [temporaneo la gestione degli errori per interruzioni di connessione](sql-database-connectivity-issues.md) è sufficiente proteggere le connessioni interrotte al termine dell'aggiornamento.

L'aggiornamento a SQL Database V12 non può essere annullata. Dopo l'aggiornamento non è possibile ripristinare il server a V11.

Dopo l'aggiornamento a V12, [servizio livello suggerimenti](sql-database-service-tier-advisor.md) e [consigli pool flessibile](sql-database-elastic-pool-create-portal.md) non immediatamente sarà disponibile fino a quando il servizio è stata eseguita per valutare i carichi di lavoro nel nuovo server. Cronologia recommendation server V11 non si applica ai server V12 in modo che non viene mantenuta.  

## <a name="prepare-to-upgrade"></a>Preparazione dell'aggiornamento

- **Aggiornare tutti i database Web e Business**: usare il portale o usare [PowerShell per aggiornare i database e server](sql-database-upgrade-server-powershell.md).
- **Revisione e sospendere la replica geografico**: se il database di SQL Azure è configurato per la replica geografico è necessario documentare la configurazione corrente e [interrompere la replica geografico](sql-database-geo-replication-portal.md#remove-secondary-database). Al termine dell'aggiornamento riconfigurare il database per la replica geografico.
- **Aprire le porte se si hanno clienti in una macchina virtuale Azure**: se il programma client si connette a SQL Database V12 mentre il client viene eseguito su una Azure macchine (), è necessario aprire gli intervalli di porta 11000 11999 e 14999 14000 nella macchina virtuale. Per informazioni dettagliate, vedere [porte per V12 di Database SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


## <a name="prerequisites"></a>Prerequisiti

Per aggiornare un server a V12 con PowerShell, è necessario disporre di più recente Azure PowerShell installata e in esecuzione. Per informazioni dettagliate, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).


## <a name="configure-your-credentials-and-select-your-subscription"></a>Configurare le credenziali e selezionare l'abbonamento

Per eseguire i cmdlet di PowerShell per l'abbonamento Azure è innanzitutto necessario stabilire l'accesso al proprio account Azure. Eseguire il comando seguente e verrà visualizzata una schermata di accesso immettere le credenziali. Usare lo stesso messaggio di posta elettronica e password utilizzati per accedere al portale di Azure.

    Add-AzureRmAccount

Dopo aver completato l'accesso risulta alcune informazioni sullo schermo che include l'Id è stato effettuato l'accesso con e le sottoscrizioni Azure a che è possibile accedere.

Per selezionare l'abbonamento da utilizzare è necessario l'abbonamento Id (**-SubscriptionId**) o abbonamento assegnare un nome (**-SubscriptionName**). È possibile copiare ottenuto nel passaggio precedente o se si hanno più abbonamenti è possibile eseguire il cmdlet **Get-AzureRmSubscription** e copiare le informazioni desiderate abbonamento set di risultati.

Eseguire il seguente cmdlet con le informazioni sull'abbonamento per impostare l'abbonamento corrente:

    Set-AzureRmContext -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

I comandi seguenti verranno eseguiti la sottoscrizione è appena selezionata in precedenza.

## <a name="get-recommendations"></a>Consigli

Per ottenere il suggerimento per l'aggiornamento del server eseguita il cmdlet seguente:

    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1”

Per ulteriori informazioni, vedere [creare un pool di database flessibile](sql-database-elastic-pool-create-portal.md) e [Database di SQL Azure prezzi consigli livello](sql-database-service-tier-advisor.md).



## <a name="start-the-upgrade"></a>Avviare l'aggiornamento

Per avviare l'aggiornamento del server di eseguire il cmdlet seguente:

    Start-AzureRmSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Quando si esegue questo processo di aggiornamento di comando, verrà avviata. È possibile personalizzare l'output della recommendation e fornire recommendation modificata per questo cmdlet.


## <a name="upgrade-a-server"></a>Aggiornare un server


    # Adding the account
    #
    Add-AzureRmAccount

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION'
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP'
    $ServerName = 'YOUR_SERVER'

    # Selecting the right subscription
    #
    Set-AzureRmContext -SubscriptionName $SubscriptionName

    # Getting the upgrade recommendations
    #
    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName

    # Starting the upgrade process
    #
    Start-AzureRmSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## <a name="custom-upgrade-mapping"></a>Mapping di aggiornamento personalizzato

Se i suggerimenti non sono adatti per il server e il caso aziendale, è possibile scegliere come i database vengono aggiornati e possano mapparli ai database singoli o flessibile.

ElasticPoolCollection e DatabaseCollection sono facoltativi:

    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100
    $elasticPool.DatabaseDtuMin = 0
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”)
    $elasticPool.Name = "elasticpool_1"
    $elasticPool.StorageMb = 800

    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap1.Name = "DBMain1"
    $databaseMap1.TargetEdition = "Standard"
    $databaseMap1.TargetServiceLevelObjective = "S0"

    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap2.Name = "DBMain2"
    $databaseMap2.TargetEdition = "Standard"
    $databaseMap2.TargetServiceLevelObjective = "S2"

    # Starting the upgrade
    #
    Start-AzureRmSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -ServerVersion 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool)



## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>Monitorare i database dopo l'aggiornamento a SQL Database V12


Dopo l'aggiornamento, è consigliabile eseguire il monitoraggio database attivamente per garantire applicazioni eseguono le prestazioni desiderato e ottimizzare l'utilizzo in base alle esigenze.

In aggiunta a singoli database, è possibile monitorare database flessibile pool [tramite il portale](sql-database-elastic-pool-manage-portal.md) di monitoraggio o con [PowerShell](sql-database-elastic-pool-manage-powershell.md)


**Dati consumo delle risorse:** Per i database di base, Standard e Premium dati consumo delle risorse sono disponibili tramite il [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV nel database utente. Questa DMV fornisce accanto alla risorsa in tempo reale consumo informazioni 15 granularità secondo dell'ora precedente dell'operazione. Il consumo di percentuale DTU per un intervallo viene calcolato come consumi percentuale massima delle dimensioni della CPU, IO e registro. Ecco una query per calcolare il consumo di percentuale DTU Media più ora ultima:

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Ulteriori informazioni monitoraggio:

- [Indicazioni di prestazioni di Database SQL azure per singoli database](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Considerazioni di prezzo e prestazioni di un pool di database flessibile](sql-database-elastic-pool-guidance.md).
- [Database di SQL Azure monitoraggio usando le visualizzazioni di gestione dinamica](sql-database-monitoring-with-dmvs.md)



**Avvisi:** Impostare gli avvisi di' ' nel portale di Azure per ricevere una notifica quando il consumo DTU per un database aggiornato si avvicina a determinate alto livello. Gli avvisi di database possono essere configurati nel portale di Azure per diverse metriche di prestazioni come DTU, CPU, IO e registro. Individuare il database e selezionare **le regole di avviso** e **l'Impostazioni** .

Ad esempio, è possibile impostare un avviso di posta elettronica "DTU percentuale" se il valore percentuale DTU medio supera il 75% su 5 minuti. Per ulteriori informazioni su come configurare le notifiche di avviso, fare riferimento a [ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) .



## <a name="next-steps"></a>Passaggi successivi

- [Creare un pool di database flessibile](sql-database-elastic-pool-create-portal.md) e aggiungere alcuni o tutti i database nel pool.
- [Modificare il livello di livello e le prestazioni di servizio del database](sql-database-scale-up.md).



## <a name="related-information"></a>Informazioni correlate

- [Get-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Inizio AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Interrompi AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)
