<properties
    pageTitle="Guida introduttiva di processi di database flessibile"
    description="sull'utilizzo dei processi di database flessibile"
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="ddove"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="ddove" />

# <a name="getting-started-with-elastic-database-jobs"></a>Guida introduttiva di processi di Database flessibile

Processi di Database flessibile (preview) per il Database di SQL Azure consente di affidabilità eseguire gli script T-SQL che si estendono su più database durante il tentativo di fornire le garanzie per il completamento eventuale automaticamente. Per ulteriori informazioni sulla caratteristica di processo flessibile Database, vedere la [pagina Panoramica caratteristica](sql-database-elastic-jobs-overview.md).

In questo argomento estende l'esempio trovato nella [Guida introduttiva a strumenti Database flessibile](sql-database-elastic-scale-get-started.md). Al termine, sarà necessario: informazioni su come creare e gestire i processi per la gestione di un gruppo di database correlati. Non è necessario utilizzare gli strumenti di scalabilità flessibile per sfruttare i vantaggi dei processi flessibile.

## <a name="prerequisites"></a>Prerequisiti

Scaricare ed eseguire la [Guida introduttiva a esempio strumenti Database flessibile](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Creare un condiviso manager mappa mediante l'app di esempio

Di seguito viene creata una mappa condiviso manager insieme shards diverse, seguito da inserimento di dati nel shards. Se si dispone già configurato con dati sharded shards, è possibile ignorare i passaggi seguenti e passare alla sezione successiva.

1. Creare ed eseguire l'applicazione di esempio **Guida introduttiva a strumenti Database flessibile** . Seguire i passaggi fino al passaggio 7 nella sezione [scaricare ed eseguita l'applicazione di esempio](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). Al termine del passaggio 7, verrà visualizzato il prompt dei comandi seguenti:

    ![prompt dei comandi][1]

2.  Nella finestra di comando, digitare "1" e premere **INVIO**. Crea la condiviso manager mappa e aggiunge due shards al server. Digitare "3", quindi premere **INVIO**. Ripetere questa azione di quattro volte. Consente di inserire le righe di dati di esempio nel shards.

3.  Il [Portale di Azure](https://portal.azure.com) deve essere visualizzata tre nuovi database nel server v12:

    ![Conferma di Visual Studio][2]

    A questo punto si creerà un insieme di database personalizzato che riflette tutti i database nella mappa condiviso. In questo modo è creare ed eseguire un processo di aggiungere una nuova tabella in shards.

Di seguito in genere è necessario creare un valore di destinazione mappa condiviso, utilizzando il cmdlet **New-AzureSqlJobTarget** . Il database di gestione della mappa condiviso deve essere impostato come destinazione database e quindi la mappa condiviso specifico viene specificata come un valore di destinazione. Se, tuttavia, che verranno enumerare tutti i database nel server e aggiungere i database per la nuova raccolta personalizzata ad eccezione dei database master.

##<a name="creates-a-custom-collection-and-adds-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Crea un insieme personalizzato e aggiunge tutti i database nel server di destinazione personalizzato insieme ad eccezione di schema.


    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    $dbsinserver | %{
    $currentdb = $_.DatabaseName 
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason
                
        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target." 
        }

        else
        {
            throw $_
        }
    
    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
}
    
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Creare uno Script T-SQL per l'esecuzione in database

    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

##<a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Creare il processo per eseguire uno script tra il gruppo personalizzato di database

    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job


##<a name="execute-the-job"></a>Eseguire il processo 

Il seguente script di PowerShell può essere utilizzato per eseguire un processo esistente:

Aggiornare la variabile seguente per riflettere il nome desiderato per avere eseguito:

    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution
 
## <a name="retrieve-the-state-of-a-single-job-execution"></a>Recuperare lo stato di un singolo dell'esecuzione del processo

Utilizzare il cmdlet **Get-AzureSqlJobExecution** stesso con il parametro **IncludeChildren** per visualizzare lo stato di esecuzioni figlio, vale a dire lo stato specifico per ogni esecuzione del processo ogni database di destinazione mediante il processo.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="view-the-state-across-multiple-job-executions"></a>Visualizzare lo stato di più esecuzioni

Il cmdlet **Get-AzureSqlJobExecution** ha più parametri facoltativi che possono essere utilizzati per visualizzare più esecuzioni, filtrate tramite i parametri indicati. Di seguito sono illustrati alcuni modi possibili per utilizzare Get-AzureSqlJobExecution:

Recuperare tutte le esecuzioni di processo di livello superiore attivo:

    Get-AzureSqlJobExecution

Recuperare tutte le esecuzioni di processo di livello superiore, incluse le esecuzioni processi inattivi:

    Get-AzureSqlJobExecution -IncludeInactive

Recuperare tutte le esecuzioni processo figlio di un ID di esecuzione processo fornito, incluse le esecuzioni processi inattivi:

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

Recuperare tutte le esecuzioni processo create con una programmazione / processo combinazione, inclusi i processi inattivi:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Recuperare tutti i processi di destinazione una mappa condiviso specificato, inclusi i processi inattivi:

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

Recuperare tutti i processi di destinazione di un insieme personalizzato specificato, inclusi i processi inattivi:

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
Recuperare l'elenco di esecuzioni attività all'interno di esecuzione di un processo specifico:

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Recuperare i dettagli di esecuzione delle attività di processo:

Il seguente script di PowerShell può essere utilizzato per visualizzare i dettagli di un processo dell'esecuzione delle attività, che risulta particolarmente utile quando il debug degli errori di esecuzione.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="retrieve-failures-within-job-task-executions"></a>Recuperare gli errori all'interno di esecuzioni delle attività

L'oggetto JobTaskExecution include una proprietà per il ciclo di vita dell'attività insieme a una proprietà del messaggio. Se esecuzione di attività del processo non è riuscita, il messaggio verrà impostata sul messaggio dell'eccezione risultante e il relativo stack il ciclo di vita verrà impostata su *non riuscito* . Se un processo non è riuscita, è importante visualizzare i dettagli dell'attività di processo che non è riuscita per un determinato processo.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="waiting-for-a-job-execution-to-complete"></a>In attesa per l'esecuzione completare il processo

Attendere completare un'attività di processo, è possibile utilizzare il seguente script di PowerShell:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Creazione di un criterio di esecuzione personalizzata

Processi di Database flessibile supporta la creazione di criteri di esecuzione personalizzati che possono essere applicati all'avvio di processi.
  
Criteri di esecuzione attualmente consentono per la definizione di:

* Nome: L'identificatore per il criterio di esecuzione.
* Timeout del processo: Tempo prima che un processo viene annullato dai processi di Database flessibile.
* Intervallo iniziale: L'intervallo di attesa prima del primo tentativo.
* Massimo intervallo: Terminazione di tentativi da utilizzare.
* Tentativi intervallo Backoff coefficiente: Coefficiente utilizzato per calcolare l'intervallo tra tentativi successivo.  Viene utilizzata la seguente formula: (intervallo tentativi iniziale) * Math.pow (intervallo Backoff coefficiente (), (numero di tentativi) - 2). 
* Numero massimo di tentativi: Il numero massimo di tentativi tenta di eseguire all'interno di un processo.

Il criterio di esecuzione predefinito utilizza i seguenti valori:

* Nome: Esecuzione criterio
* Timeout del processo: 1 settimana
* Intervallo iniziale: 100 millisecondi
* Massimo intervallo: 30 minuti
* Riprovare coefficiente di intervallo: 2
* Numero massimo di tentativi: 2.147.483.647

Creare criteri di esecuzione desiderato:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>Aggiornare un criterio di esecuzione personalizzata

Aggiornare i criteri di esecuzione desiderata per l'aggiornamento:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
 
## <a name="cancel-a-job"></a>Annullare un processo

Processi di Database flessibile supporta le richieste di annullamento processi.  Se i processi del Database flessibile rileva una richiesta di annullamento per un processo in fase di esecuzione, tenterà di interrompere il processo.

Esistono diversi modi che flessibile processi di Database può eseguire una cancellazione:

1. Annullamento attualmente in esecuzione attività: se viene rilevato un annullamento mentre è in esecuzione di un'attività, un annullamento verrà tentato all'interno dell'aspetto attualmente in esecuzione dell'attività.  Ad esempio: se c'è una lunga query viene eseguita quando si tenta di annullamento, verrà tentato di annullare la query.
2. Annullamento attività tentativi: Se un annullamento viene rilevato dal thread di controllo prima di un'attività viene avviata per l'esecuzione, il thread di controllo verrà evitare di avviare l'attività e dichiarare la richiesta annullata.

Se è richiesto un annullamento del processo per un processo padre, la richiesta di annullamento verrà utilizzato il valore per il processo di padre e per tutti i processi figlio.
 
Per inviare una richiesta di annullamento, utilizzare il cmdlet **Stop AzureSqlJobExecution** e impostare il parametro **JobExecutionId** .

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Eliminare un processo di base al nome e cronologia del processo

Processi di Database flessibile asincrono eliminazione dei processi è supportata. Un processo può essere contrassegnato per l'eliminazione e il sistema elimina il processo e tutta la cronologia di processo termine del processo di tutte le esecuzioni. Il sistema non interromperà automaticamente le esecuzioni processo attivo.  

Se, tuttavia, Interrompi AzureSqlJobExecution devono essere richiamati per annullare le esecuzioni processo attivo.

Per attivare l'eliminazione di processo, utilizzare il cmdlet **Rimuovi AzureSqlJob** e impostare il parametro **specificare nome di processo** .

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
 
## <a name="create-a-custom-database-target"></a>Creare un database personalizzato di destinazione
I siti di destinazione di database personalizzato può essere definite nel processi Database flessibile che possono essere utilizzati per l'esecuzione direttamente o per l'inclusione di un gruppo di database personalizzato. Poiché **i pool flessibile Database** non sono ancora direttamente supportati tramite APIs PowerShell, è sufficiente creare una destinazione database personalizzato e destinazione insieme database personalizzato che include tutti i database nel pool.

Impostare le seguenti variabili per riflettere le informazioni sul database desiderato:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="create-a-custom-database-collection-target"></a>Creare un insieme di destinazione database personalizzato
Un valore di destinazione insieme database personalizzato può essere definita per abilitare l'esecuzione tra più destinazioni database definito. Dopo la creazione di un gruppo di database, database possono essere associati alla destinazione insieme personalizzato.

Impostare le variabili seguenti per riflettere la configurazione di destinazione raccolta personalizzata desiderata:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="add-databases-to-a-custom-database-collection-target"></a>Aggiungere database a un valore di destinazione insieme database personalizzato

I siti di destinazione di database possono essere associati a destinazioni insieme database personalizzato per creare un gruppo di database. Ogni volta che viene creato un processo che è destinato a un valore di destinazione insieme database personalizzato, si espande per i database associati al gruppo al momento dell'esecuzione di destinazione.

Aggiungere il database desiderato per una raccolta specifica personalizzata:

    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Rivedere i database all'interno di un valore di destinazione insieme database personalizzato

Utilizzare il cmdlet **Get-AzureSqlJobTarget** per recuperare i database figlio all'interno di un valore di destinazione insieme database personalizzato. 
 
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Creare un processo per eseguire uno script in un valore di destinazione insieme database personalizzato

Utilizzare il cmdlet **New-AzureSqlJob** per creare un processo rispetto a un gruppo di database definita dalla destinazione insieme database personalizzato. Flessibile processi di Database verranno espandere il processo in più processi figlio ciascuna corrispondente a un database associato la destinazione della raccolta database personalizzato e assicurarsi che lo script viene eseguito in tutti i database. Nuovo, è importante che gli script siano idempotenti essere flessibile a tentativi.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Raccolta di dati in database

**Database flessibile processi** supporta l'esecuzione di una query in un gruppo di database e i risultati vengono inviati alla tabella del database specificato. Dopo aver fatto per visualizzare i risultati della query da ogni database, è possibile cercare la tabella. Fornisce un meccanismo asincrono per eseguire una query in molti database. Gestione dei casi di errore, ad esempio uno dei database temporaneamente non disponibile tramite tentativi automaticamente.

La tabella di destinazione specificata verrà creata automaticamente se non esiste ancora, schema del set di risultati restituiti corrispondente. Se l'esecuzione di script restituisce più set di risultati, processi di Database flessibile verranno inviati solo il primo a tabella di destinazione specificata.

Il seguente script di PowerShell può essere utilizzato per eseguire uno script la raccolta i risultati in una tabella specificata. Questo script presuppone che sia stato creato uno script T-SQL che restituisce un singolo set di risultati e un valore di destinazione insieme database personalizzato è stata creata.

Impostare le operazioni seguenti per rispecchiare i script desiderato, le credenziali e destinazione esecuzione:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Creare e avviare un processo per gli scenari di raccolta dati
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Creare una pianificazione per l'esecuzione dei processi utilizzando un trigger di processo

Il seguente script di PowerShell può essere utilizzato per creare una pianificazione ricorrente. Questo script utilizza un intervallo di un minuto, ma AzureSqlJobSchedule nuovo supporta anche parametri - DayInterval, - HourInterval, - MonthInterval e - WeekInterval. Possono essere create le pianificazioni da eseguire una sola volta per il passaggio - unica.

Creare una nuova programmazione:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime 
    Write-Output $schedule

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Creare un trigger di processo per eseguire un processo in un calendario

Per eseguire un processo in base a un calendario, è possibile definire un trigger di processo. Il seguente script di PowerShell possono essere usate per creare un trigger di processo.

Impostare le seguenti variabili in modo che corrispondano alla pianificazione e del processo desiderato:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName –JobName $jobName
    Write-Output $jobTrigger

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Rimuovere un'associazione programmata per interrompere l'esecuzione sulla programmazione processo

Per interrompere l'esecuzione del processo ricorrenti tramite un trigger di processo, è possibile rimuovere il trigger di processo. Rimuovere un trigger di processo per interrompere un processo di eseguire in base a una programmazione utilizzando il cmdlet **AzureSqlJobTrigger Rimuovi** .

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName





## <a name="import-elastic-database-query-results-to-excel"></a>Importare i risultati della query di database flessibile in Excel

 È possibile importare i risultati da di una query a un file di Excel.

1. Avviare Excel 2013.
2.  Passare alla barra multifunzione di **dati** .
3.  Fare clic su **Da altre origini** e fare clic su **Da SQL Server**.

    ![Importazione di Excel da altre origini][5]
4.  Nella **Connessione guidata dati** digitare le credenziali di accesso e del nome del server. Fare clic su **Avanti**.
5.  Nella finestra di dialogo **Selezionare il database che contiene i dati desiderati**, selezionare il database **ElasticDBQuery** .
6.  Selezionare la tabella **clienti** nella visualizzazione elenco e fare clic su **Avanti**. Fare clic su **Fine**.
7.  Nel modulo **Importa dati** , in **specificare come visualizzare i dati nella cartella di lavoro**, selezionare **tabella** e fare clic su **OK**.

Tutte le righe dalla tabella **clienti** , archiviato in diversi shards popolare nel foglio di Excel.

## <a name="next-steps"></a>Passaggi successivi
È ora possibile utilizzare le funzioni dei dati di Excel. Utilizzare la stringa di connessione con il nome del server, nome del database e le credenziali per connettersi strumenti di integrazione di Business Intelligence e i dati nel database di query flessibile. Assicurarsi che sia supportato SQL Server come origine dati per lo strumento. Fare riferimento al database di query flessibile e tabelle esterne come qualsiasi altro database di SQL Server e tabelle di SQL Server che si connette a con lo strumento.

### <a name="cost"></a>Costo
Non esiste costi aggiuntivi per utilizzare la funzione di query di Database flessibile. In questo momento questa caratteristica è disponibile solo nei database premium come punto finale, tuttavia, il shards possono essere di qualsiasi livello di servizio.

Per informazioni sui prezzi vedere [Dettagli prezzi Database di SQL](https://azure.microsoft.com/pricing/details/sql-database/).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
