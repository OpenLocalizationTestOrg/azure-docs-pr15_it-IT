<properties 
    pageTitle="Gestire un pool di database flessibile (PowerShell) | Microsoft Azure" 
    description="Informazioni su come usare PowerShell per gestire un pool di database flessibile."  
    services="sql-database" 
    documentationCenter="" 
    authors="srinia" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="06/22/2016"
    ms.author="srinia"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-powershell"></a>Monitorare e gestire un pool di database flessibile con PowerShell 

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Gestire un [pool di database flessibile](sql-database-elastic-pool.md) uso dei cmdlet di PowerShell. 

I codici di errore comuni, vedere [i codici di errore SQL per le applicazioni client di Database SQL: errore di connessione e altri problemi di Database](sql-database-develop-error-messages.md).

I valori per i pool sono disponibili in [limiti eDTU e lo spazio di archiviazione](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases). 

## <a name="prerequisites"></a>Prerequisiti

* Azure PowerShell 1.0 o superiore. Per informazioni dettagliate, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).
* Pool di database flessibile sono disponibili solo con V12 Database di SQL Server. Se si dispone di un server SQL Database V11 [usare PowerShell per eseguire l'aggiornamento a V12 e creare un pool](sql-database-upgrade-server-portal.md) in un unico passaggio.


## <a name="move-a-database-into-an-elastic-pool"></a>Spostare un database in un pool flessibile

È possibile spostare un database o all'esterno di un pool con [AzureRmSqlDatabase Set](https://msdn.microsoft.com/library/azure/mt619433.aspx). 

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="change-performance-settings-of-a-pool"></a>Modificare le impostazioni delle prestazioni di un pool

Quando si influisce negativamente sulle prestazioni, è possibile modificare le impostazioni del pool di adattato crescita. Utilizzare il cmdlet [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) . Impostare il parametro - Dtu eDTUs al pool. Vedere [limiti eDTU e lo spazio di archiviazione](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases) per i valori possibili.  

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## <a name="get-the-status-of-pool-operations"></a>Visualizzare lo stato della operazioni relative al pool

Creazione di un pool può richiedere tempo. Per verificare lo stato delle operazioni relative al pool inclusi aggiornamenti e la creazione, utilizzare il cmdlet [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) .

    Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## <a name="get-the-status-of-moving-an-elastic-database-into-and-out-of-a-pool"></a>Ottenere lo stato di spostamento di un database flessibile in e disconnettersi da un pool

Spostamento di un database può richiedere tempo. Tenere traccia di uno stato di spostamento utilizzando il cmdlet [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx) .

    Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="get-resource-usage-data-for-a-pool"></a>Recuperare dati di utilizzo delle risorse per un pool

Metriche che possono essere recuperate come percentuale del limite di pool di risorse:   


| Nome metriche | Descrizione |
| :-- | :-- |
| CPU\_% | Media per calcolare l'utilizzo in percentuale del limite del pool. |
| fisica\_dati\_leggere\_% | Utilizzo dei / o medio in percentuale in base al limite di pool. |
| registro\_scrivere\_% | Utilizzo delle risorse di scrittura in percentuale del limite del pool. | 
| DTU\_consumo\_% | Utilizzo di eDTU Media in percentuale del limite di eDTU per il pool | 
| spazio di archiviazione\_% | Calcolare la media di spazio di archiviazione in percentuale del limite di archiviazione del pool. |  
| Worker\_% | Massimo simultanee Worker (richieste) in percentuale in base al limite di pool. |  
| sessioni\_% | Numero massimo di sessioni simultaneo in percentuale in base al limite di pool. | 
| eDTU_limit | Massima del pool flessibile DTU impostazione corrente per questo pool flessibile durante questo intervallo. |
| spazio di archiviazione\_limite | Limite di archiviazione massima del pool flessibile corrente impostazione per il pool flessibile in megabyte durante questo intervallo. |
| eDTU\_utilizzato | Media eDTUs utilizzato dal pool di tale intervallo. |
| spazio di archiviazione\_utilizzato | Spazio di archiviazione medio utilizzato dal pool di in questo intervallo in byte |

**Periodi di granularità/memorizzazione metriche:**

* I dati restituiti granularità 5 minuti.  
* Conservazione dei dati è 35 giorni.  

Questo cmdlet e API limita il numero di righe che possono essere recuperati in una chiamata a 1000 righe (circa 3 giorni di dati granularità 5 minuti). Ma questo comando è possibile chiamare più volte a intervalli di tempo inizio/fine diverso per recuperare i dati più 

Per recuperare le metriche:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  


## <a name="get-resource-usage-data-for-an-elastic-database"></a>Recuperare dati di utilizzo delle risorse per un database flessibile

Queste API sono le stesse le API correnti (V12) utilizzate per monitorare l'utilizzo delle risorse di un database autonomo, ad eccezione di quanto segue semantic. 

Per questa API metriche recuperate vengono espresso come percentuale del per eDTUs max (o estremità equivalente per la metrica sottostante come CPU, IO e così via) impostare per tale pool. Ad esempio, 50% dell'utilizzo di uno di questi indicatori indica che il consumo risorsa specifica è al 50% del limite di estremità di database per la risorsa nel pool di padre per. 

Per recuperare le metriche:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

## <a name="add-an-alert-to-a-pool-resource"></a>Aggiungere un avviso per una risorsa di pool

È possibile aggiungere regole di avviso alle risorse per inviare notifiche tramite posta elettronica o stringhe avvisi per [i punti finali URL](https://msdn.microsoft.com/library/mt718036.aspx) quando la risorsa raggiunge una soglia di utilizzo che sono stati impostati. Utilizzare il cmdlet AzureRmMetricAlertRule Aggiungi. 

> [AZURE.IMPORTANT]Utilizzo delle risorse monitoraggio per i pool flessibile ha un ritardo di almeno 20 minuti. Impostazione degli avvisi di meno di 30 minuti per i pool flessibile non è attualmente supportato. Impostare gli avvisi per pool flessibile con un punto (parametro denominato "-WindowSize" PowerShell API) di meno di 30 minuti non può essere avviata. Assicurarsi che tutti gli avvisi che è possibile definire per i pool flessibile utilizzano un periodo (WindowSize) di 30 minuti o più.

In questo esempio viene aggiunto un avviso per ricevere notifiche quando il consumo eDTU del pool supera determinate soglia.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location =  '<location'                         # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    #$Target Resource ID
    $ResourceID = '/subscriptions/' + $subscriptionId + '/resourceGroups/' +$resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticpools/' + $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # create a unique rule name
    $alertName = $poolName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $ResourceID -MetricName "DTU_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail 

## <a name="add-alerts-to-all-databases-in-a-pool"></a>Aggiungere avvisi per tutti i database in un pool

È possibile aggiungere regole di avviso a tutti i database in un pool flessibile per l'invio di notifiche tramite posta elettronica o stringhe avvisi per [i punti finali URL](https://msdn.microsoft.com/library/mt718036.aspx) quando una risorsa raggiunge una soglia di utilizzo imposta dell'avviso. 

> [AZURE.IMPORTANT] Utilizzo delle risorse monitoraggio per i pool flessibile ha un ritardo di almeno 20 minuti. Impostazione degli avvisi di meno di 30 minuti per i pool flessibile non è attualmente supportato. Impostare gli avvisi per pool flessibile con un punto (parametro denominato "-WindowSize" PowerShell API) di meno di 30 minuti non può essere avviata. Assicurarsi che tutti gli avvisi che è possibile definire per i pool flessibile utilizzano un periodo (WindowSize) di 30 minuti o più.

In questo esempio aggiunge un avviso a tutti i database in un pool per ricevere notifiche quando il consumo DTU del database supera determinate soglia.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location = '<location'                          # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    foreach ($db in $dbList)
    {
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName

    # create a unique rule name
    $alertName = $db.DatabaseName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName  -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $dbResourceId -MetricName "dtu_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

    # drop the alert rule
    #Remove-AzureRmAlertRule -ResourceGroup $resourceGroupName -Name $alertName
    } 



## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools-in-a-subscription"></a>Raccogliere e monitorare i dati di utilizzo delle risorse tra più pool di una sottoscrizione

Dopo aver configurato un numero elevato di database di una sottoscrizione, è complesso per monitorare ogni pool flessibile separatamente. Se, tuttavia, è possibile combinare i cmdlet di PowerShell di database SQL e query T-SQL per raccogliere dati sull'utilizzo delle risorse da più pool e i database per il monitoraggio e analisi di utilizzo delle risorse. Un [esempio di implementazione](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) di un set di questo tipo di script di powershell disponibili nel repository esempi GitHub SQL Server insieme la documentazione sulla funzione e come utilizzarlo.

Per utilizzare questa implementazione di esempio seguire questa procedura elencata di seguito.


1. Scaricare [gli script e la documentazione](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools):
2. Modificare gli script per l'ambiente. Specificare uno o più server in cui sono ospitati pool flessibile.
3. Specificare un database di telemetria in cui sono raccolte metriche verrà archiviato. 
4. Personalizzare lo script per specificare la durata dell'esecuzione degli script.

Un alto livello, gli script di eseguire le operazioni seguenti:

*   Consente di enumerare tutti i server di una sottoscrizione di Azure specificata (o un elenco specifico di server).
*   Esegue un processo di sfondo per ogni server. Il processo viene eseguito in un ciclo a intervalli regolari e raccoglie i dati di telemetria per tutti i pool di server. Carica quindi i dati raccolti nel database di telemetria specificato.
*   Consente di enumerare un elenco di database in ogni pool per raccogliere i dati di utilizzo delle risorse di database. Carica quindi i dati raccolti nel database di telemetria.

È possibile analizzare le metriche raccolte nel database di telemetria per monitorare l'integrità del pool flessibile e i database al suo interno. Lo script installa anche una funzione valore tabella predefinita (TVF) nel database di telemetria utili aggregazione le metriche per un intervallo di tempo specificato. Ad esempio, i risultati della TVF possono essere utilizzati per mostrare "prime N flessibile pool con l'utilizzo di eDTU massimo in un intervallo di tempo specificato." Facoltativamente, è possibile utilizzare strumenti di analisi come Excel o Power BI per eseguire query e analizzare i dati raccolti.

## <a name="example-retrieve-resource-consumption-metrics-for-a-pool-and-its-databases"></a>Esempio: recuperare metriche di consumo delle risorse per un pool e i relativi database

In questo esempio vengono metriche consumo per un determinato pool flessibile e tutti i database. Dati raccolti vengono formattati e scritti in un file CSV formattato. Il file può essere esplorato con Excel.

    $subscriptionId = '<Azure subscription id>'       # Azure subscription ID
    $resourceGroupName = '<resource group name>'             # Resource Group
    $serverName = <server name>                              # server name
    $poolName = <elastic pool name>                          # pool name
        
    # Login to Azure account and select the subscription.
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Get resource usage metrics for an elastic pool for the specified time interval.
    $startTime = '4/27/2016 00:00:00'  # start time in UTC
    $endTime = '4/27/2016 01:00:00'    # end time in UTC
    
    # Construct the pool resource ID and retrive pool metrics at 5 minute granularity.
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime) 
    
    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName
    
    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    $dbMetrics = @()
    foreach ($db in $dbList)
    {
        $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
        $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
    }
    
    #Optionally you can format the metrics and output as .csv file using the following script block.
    $command = {
    param($metricList, $outputFile)

    # Format metrics into a table.
    $table = @()
    foreach($metric in $metricList) { 
      foreach($metricValue in $metric.MetricValues) {
        $sx = New-Object PSObject -Property @{
            Timestamp = $metricValue.Timestamp.ToString()
            MetricName = $metric.Name; 
            Average = $metricValue.Average;
            ResourceID = $metric.ResourceId 
          }
          $table = $table += $sx
      }
    }
    
    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
    }
    
    # Format and output pool metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv
    
    # Format and output database metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv



## <a name="latency-of-elastic-pool-operations"></a>Latenza di operazioni relative al pool flessibile

- Modifica eDTUs min per eDTUs database o max per ogni database in genere viene completata in 5 minuti.
- Modifica eDTUs al pool dipende la quantità totale di spazio utilizzato da tutti i database nel pool. Modifiche calcolare la media di 90 minuti o meno per 100 GB. Ad esempio, se lo spazio totale utilizzato da tutti i database nel pool di è 200 GB, la latenza prevista per la modifica eDTU pool al pool sarà 3 ore o meno.

## <a name="migrate-from-v11-to-v12-servers"></a>Eseguire la migrazione da V11 ai server V12

Cmdlet di PowerShell disponibili iniziare, interrompere o controllare l'aggiornamento a V12 di Database SQL Azure dalla V11 o qualsiasi altra versione pre-V12.

- [Eseguire l'aggiornamento a SQL Database V12 tramite PowerShell](sql-database-upgrade-server-powershell.md)

Per la documentazione della Guida di riferimento su questi cmdlet di PowerShell, vedere:


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Inizio AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Interrompi AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


Il cmdlet Interrompi significa annullare, posizionare il puntatore non. Non è possibile riprendere l'aggiornamento, ad eccezione di avviare nuovamente dall'inizio. Il cmdlet Interrompi Pulisce e rilascia tutte le risorse appropriate.

## <a name="next-steps"></a>Passaggi successivi

- [Creare processi flessibile](sql-database-elastic-jobs-overview.md) Processi flessibile consentono di eseguire qualsiasi numero di database nel pool di script T-SQL.
- Vedere [proporzioni fuori con Database di SQL Azure](sql-database-elastic-scale-introduction.md): utilizzare strumenti database flessibile per scalabilità, spostare i dati della query o creare le transazioni.
