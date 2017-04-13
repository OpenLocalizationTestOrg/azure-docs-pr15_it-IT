<properties 
    pageTitle="Creare e gestire i processi di Database flessibile tramite PowerShell" 
    description="PowerShell usato per gestire i pool di Database SQL Azure" 
    services="sql-database" documentationCenter=""  
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="create-and-manage-a-sql-database-elastic-database-jobs-using-powershell-preview"></a>Creare e gestire i processi di database flessibile un Database SQL tramite PowerShell (preview)

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)



APIs PowerShell per i **processi di Database flessibile** (in modalità anteprima) consentono di definire un gruppo di database in cui verranno eseguita script. In questo articolo viene illustrato come creare e gestire i **processi di Database flessibile** uso dei cmdlet di PowerShell. Vedere [Panoramica di processi flessibile](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Prerequisiti di
* Un abbonamento Azure. Per una versione di valutazione gratuita, vedere [versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Un set di database creati con gli strumenti di Database flessibile. Vedere [Guida introduttiva a strumenti Database flessibile](sql-database-elastic-scale-get-started.md).
* Azure PowerShell. Per informazioni dettagliate, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).
* **Processi di Database flessibile** Pacchetto di PowerShell: vedere [processi di installazione di Database flessibile](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Selezionare l'abbonamento Azure

Per selezionare l'abbonamento è necessario l'abbonamento Id (**-SubscriptionId**) o l'abbonamento di assegnare un nome (**-SubscriptionName**). Se si hanno più abbonamenti è possibile eseguire il cmdlet **Get-AzureRmSubscription** e copiare le informazioni di sottoscrizione desiderato dal set di risultati. Dopo avere inserito le informazioni sull'abbonamento, eseguire i cmdlet seguenti per impostare la sottoscrizione come impostazione predefinita, vale a dire la destinazione per la creazione e la gestione dei processi:

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

[PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) è consigliato per l'utilizzo come sviluppare ed eseguire gli script di PowerShell contro i processi di Database flessibile.

## <a name="elastic-database-jobs-objects"></a>Oggetti di processi Database flessibile

Nella tabella seguente elenca gli tutti i tipi di oggetto di **Database flessibile processi** oltre la descrizione e pertinenti PowerShell APIs.

<table style="width:100%">
  <tr>
    <th>Tipo di oggetto</th>
    <th>Descrizione</th>
    <th>API PowerShell correlate</th>
  </tr>
  <tr>
    <td>Credenziali</td>
    <td>Nome utente e password per la connessione al database per l'esecuzione di script o dell'applicazione di DACPACs. <p>La password verrà crittografata prima di inviare e archiviare nel database flessibile processi di Database.  La password decrittografia dal servizio flessibile processi di Database tramite le credenziali creato e caricati dallo script di installazione.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>AzureSqlJobCredential nuovo</p><p>Set AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Script</td>
    <td>Script di Transact-SQL da utilizzare per l'esecuzione in database.  Lo script deve essere creato per essere idempotente poiché il servizio tenterà di esecuzione di script su errori.
    </td>
    <td>
    <p>AzureSqlJobContent Get</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>Nuovo AzureSqlJobContent</p>
    <p>Set AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Applicazione di livello dati </a> pacchetto da applicare tra database.

    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Nuovo AzureSqlJobContent</p>
    <p>Set AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Database destinazione</td>
    <td>Nome di server e database che punta a un Database di SQL Azure.

    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Nuovo AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Destinazione mappa condiviso</td>
    <td>Combinazione di un database di destinazione e credenziali da utilizzare per determinare le informazioni memorizzate all'interno di una mappa di flessibile Database condiviso.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>AzureSqlJobTarget nuovo</p>
    <p>Set AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Destinazione insieme personalizzato</td>
    <td>Gruppo definito dei database utilizzato per l'esecuzione.</td>
    <td>
    <p>AzureSqlJobTarget Get</p>
    <p>Nuovo AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Destinazione figlio insieme personalizzato</td>
    <td>Destinazione del database che fa riferimento da un insieme personalizzato.</td>
    <td>
    <p>AzureSqlJobChildTarget aggiungere</p>
    <p>Rimuovi AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Processo</td>
    <td>
    <p>Definizione di parametri per un processo che può essere utilizzato per impostare un trigger esecuzione o per soddisfare una programmazione.</p>
    </td>
    <td>
    <p>AzureSqlJob Get</p>
    <p>AzureSqlJob nuovo</p>
    <p>Set AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>Esecuzione del processo</td>
    <td>
    <p>Contenitore delle attività necessarie per soddisfare esecuzione di uno script o applicare una DACPAC a un valore di destinazione utilizzando le credenziali per le connessioni di database con errori gestita in base a un criterio di esecuzione.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Inizio AzureSqlJobExecution</p>
    <p>Interrompi AzureSqlJobExecution</p>
    <p>Attendi AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Esecuzione del processo attività</td>
    <td>
    <p>Sola unità di lavoro per soddisfare un processo.</p>
    <p>Se un'attività del processo non è in grado di correttamente execute, verrà registrato il messaggio dell'eccezione risultante e una nuova attività di processo corrispondente verrà creata ed eseguita in base ai criteri di esecuzione specificato.</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>AzureSqlJobExecution Start</p>
    <p>Interrompi AzureSqlJobExecution</p>
    <p>AzureSqlJobExecution attesa</p>
  </tr>

<tr>
    <td>Criteri di esecuzione di processo</td>
    <td>
    <p>I controlli del processo intervalli tra tentativi, limiti Riprova e timeout di esecuzione.</p>
    <p>Flessibile processi di Database include il criterio esecuzione del processo predefinito che essenzialmente infiniti tentativi errori dei processi attività con backoff esponenziale intervalli di tra ogni Riprova.</p>
    </td>
    <td>
    <p>AzureSqlJobExecutionPolicy Get</p>
    <p>AzureSqlJobExecutionPolicy nuovo</p>
    <p>Set AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>Programmazione</td>
    <td>
    <p>Ora in base a specifiche per l'esecuzione di avranno luogo a intervalli ricorrenti o in una sola volta.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>Nuovo AzureSqlJobSchedule</p>
    <p>AzureSqlJobSchedule set</p>
    </td>
  </tr>

<tr>
    <td>Trigger di processo</td>
    <td>
    <p>Mapping tra un processo e una programmazione per l'esecuzione del processo trigger in base alla programmazione.</p>
    </td>
    <td>
    <p>Nuovo AzureSqlJobTrigger</p>
    <p>Rimuovi AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Processi di Database flessibile supportati raggruppare tipi
Il processo viene eseguito script Transact-SQL (Transact-SQL) o dell'applicazione di DACPACs in un gruppo di database. Quando viene inviato un processo da eseguire in un gruppo di database, il processo "espande" l'in processi figlio nel punto in cui ogni esegue esecuzione richiesto con un solo database nel gruppo. 
 
Esistono due tipi di gruppi che è possibile creare: 

* Gruppo [Mappa condiviso](sql-database-elastic-scale-shard-map-management.md) : quando un processo viene inviato a una mappa condiviso di destinazione, il processo query mappa condiviso per determinare il set di shards corrente e quindi crea figlio processi per ogni condiviso nella mappa condiviso.
* Gruppo di raccolta personalizzato: personalizzato definiti set di database. Quando un processo è destinato a un insieme personalizzato, Crea figlio processi per ogni database al momento dell'insieme personalizzato.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Per impostare il Database flessibile processi di connessione

Una connessione deve essere impostata su processi *database controllo* prima di utilizzare processi API. Eseguire questo cmdlet attiva una finestra delle credenziali di richiedere il nome utente e la password creata durante l'installazione di processi di Database flessibile. Tutti gli esempi forniti in questo argomento si presuppongono che il primo passaggio è già stato eseguito.

Aprire una connessione per i processi di Database flessibile:

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Credenziali crittografate all'interno di processi Database flessibile

Le credenziali database possono essere inserite nel processi *database controllo* con la relativa password crittografata. È necessario archiviare le credenziali per attivare processi in un secondo momento (con pianificazioni).
 
Crittografia tramite un certificato creato come parte dello script di installazione. Lo script di installazione crea e carica il certificato nel servizio Cloud Azure per la decrittografia delle password crittografata memorizzate. Il servizio Cloud di Azure memorizza in un secondo momento la chiave pubblica all'interno del processi *database di controllo* che consente l'interfaccia API di PowerShell o portale classica Azure crittografare la password fornita senza il certificato sia installato in locale.
 
Le password delle credenziali sono crittografate e protetto da utenti di accesso in sola lettura flessibile processi a oggetti di Database. È possibile che un utente con accesso in lettura / scrittura agli oggetti di processi di Database flessibile dannoso per estrarre una password. Le credenziali sono progettate per essere riutilizzati tra esecuzioni. Le credenziali vengono passate al database di destinazione per stabilire connessioni. Non esistono attualmente restrizioni per i database di destinazione utilizzati per ciascuna credenziale, utente non autorizzato Impossibile aggiungere una destinazione di database per un database in controllo dell'utente non autorizzato. L'utente successivamente Impossibile avviare un processo l'assegnazione del database per ottenere la password della credenziale.

Le procedure consigliate per i processi di Database flessibile includono:

* Limitare l'uso delle API per singoli utenti attendibili.
* Credenziali devono disporre dei privilegi minimi necessari per eseguire le attività del processo.  Ulteriori informazioni possono essere visti all'interno di questo articolo di MSDN di SQL Server [autorizzazioni](https://msdn.microsoft.com/library/bb669084.aspx) .

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Per creare una credenziale crittografata per l'esecuzione di processo in database

Per creare nuove credenziali crittografate, il [**cmdlet Get-Credential**](https://technet.microsoft.com/library/hh849815.aspx) richiede un nome utente e la password che è possibile passare al [**cmdlet New-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346063.aspx).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Per aggiornare le credenziali

Quando si cambia la password, utilizzare il [**cmdlet Set-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346062.aspx) e impostare il parametro **CredentialName** .

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Per definire una destinazione di mappa flessibile Database condiviso

Per eseguire un processo in tutti i database in un set di condiviso (creato tramite [libreria client Database flessibile](sql-database-elastic-database-client-library.md)), utilizzare una mappa condiviso con quello di destinazione di database. In questo esempio richiede un'applicazione sharded creata con la raccolta di client Database flessibile. Vedere [Guida introduttiva a esempio strumenti Database flessibile](sql-database-elastic-scale-get-started.md).

Il database di gestione della mappa condiviso deve essere impostato come destinazione database e quindi la mappa specifiche condiviso deve essere specificata come un valore di destinazione.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Creare uno Script T-SQL per l'esecuzione in database

Quando si creano script T-SQL per l'esecuzione, è altamente consigliati per creare in modo che siano [idempotenti](https://en.wikipedia.org/wiki/Idempotence) e flessibile da errori. Processi di Database flessibile tenterà di esecuzione di uno script ogni volta che esecuzione rileva un errore, indipendentemente dalla classificazione dell'errore.

Utilizzare il [**cmdlet New-AzureSqlJobContent**](https://msdn.microsoft.com/library/mt346085.aspx) per creare e salvare uno script per l'esecuzione e impostare i parametri **- ContentName presente** e **- CommandText** .

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>Creare un nuovo script da un file
Se lo script T-SQL è stato definito all'interno di un file, consente di importare lo script:

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Per aggiornare uno script T-SQL per l'esecuzione in database  

Questo script di PowerShell aggiorna il testo del comando T-SQL per uno script esistente.

Impostare le seguenti variabili in modo da rispecchiare la definizione di script desiderato da impostare:

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>Per aggiornare la definizione di uno script esistente

    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Per creare un processo per eseguire uno script in una mappa condiviso

Questo script di PowerShell avvia un processo per l'esecuzione di uno script in ogni condiviso in una mappa di condiviso scalabilità flessibile.

Impostare le seguenti variabili in modo da rispecchiare di script desiderato e di destinazione:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>Per eseguire un processo 

Questo script di PowerShell esegue un processo esistente:

Aggiornare la variabile seguente per riflettere il nome desiderato per avere eseguito:

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution
 
## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Per recuperare lo stato di una singola esecuzione dei processi

Utilizzare il [**cmdlet Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) e impostare il parametro **JobExecutionId** per visualizzare lo stato dell'esecuzione del processo.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Utilizzare il cmdlet **Get-AzureSqlJobExecution** stesso con il parametro **IncludeChildren** per visualizzare lo stato di esecuzioni figlio, vale a dire lo stato specifico per ogni esecuzione del processo ogni database di destinazione mediante il processo.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Per visualizzare lo stato tra più esecuzioni del processo

Il [**cmdlet Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) ha più parametri facoltativi che possono essere utilizzati per visualizzare più esecuzioni, filtrate tramite i parametri indicati. Di seguito sono illustrati alcuni modi possibili per utilizzare Get-AzureSqlJobExecution:

Recuperare tutte le esecuzioni di processo di livello superiore attivo:

    Get-AzureSqlJobExecution

Recuperare tutte le esecuzioni processo di livello superiore, incluse le esecuzioni processi inattivi:

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

## <a name="to-retrieve-failures-within-job-task-executions"></a>Per recuperare gli errori all'interno di esecuzioni delle attività

L' **oggetto JobTaskExecution** include una proprietà per il ciclo di vita dell'attività insieme a una proprietà del messaggio. Se esecuzione di attività del processo non è riuscita, il messaggio verrà impostata sul messaggio dell'eccezione risultante e il relativo stack il ciclo di vita verrà impostata su *non riuscito* . Se un processo non è riuscita, è importante visualizzare i dettagli dell'attività di processo che non è riuscita per un determinato processo.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>In attesa per l'esecuzione completare il processo

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
* Riprova coefficiente Backoff intervallo: Coefficiente utilizzato per calcolare l'intervallo tra tentativi successivo.  Viene utilizzata la seguente formula: (intervallo tentativi iniziale) * Math.pow (intervallo Backoff coefficiente (), (numero di tentativi) - 2). 
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
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
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

Processi di Database flessibile supporta le richieste di annullamento dei processi.  Se i processi del Database flessibile rileva una richiesta di annullamento per un processo in fase di esecuzione, tenterà di interrompere il processo.

Esistono diversi modi che flessibile Database processi possono essere eseguite un annullamento:

1. Annulla attualmente in esecuzione attività: se viene rilevato un annullamento durante un'attività è in esecuzione, un annullamento verrà tentato all'interno di aspetto attualmente in esecuzione dell'attività.  Ad esempio: se c'è una lunga query viene eseguita quando si tenta di annullamento, verrà tentato di annullare la query.
2. Annullamento di tentativi di attività: se un annullamento viene rilevato dal thread di controllo prima di un'attività viene avviata per l'esecuzione, il thread di controllo verrà evitare di avviare l'attività e dichiarare la richiesta annullata.

Se è richiesto un annullamento del processo per un processo padre, la richiesta di annullamento verrà utilizzato il valore per il processo di padre e per tutti i processi figlio.
 
Per inviare una richiesta di annullamento, utilizzare il [**cmdlet Stop AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346053.aspx) e impostare il parametro **JobExecutionId** .

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Per eliminare un processo e la cronologia dei processi in modo asincrono

Processi di Database flessibile supporta asincrono eliminazione dei processi. Un processo può essere contrassegnato per l'eliminazione e il sistema elimina il processo e tutta la cronologia di processo termine del processo di tutte le esecuzioni. Il sistema non interromperà automaticamente le esecuzioni processo attivo.  

Richiamare [**AzureSqlJobExecution Interrompi**](https://msdn.microsoft.com/library/mt346053.aspx) per annullare le esecuzioni processo attivo.

Per attivare l'eliminazione di processo, utilizzare il [**cmdlet AzureSqlJob Rimuovi**](https://msdn.microsoft.com/library/mt346083.aspx) e impostare il parametro **specificare nome di processo** .

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
 
## <a name="to-create-a-custom-database-target"></a>Per creare una database personalizzato di destinazione

È possibile definire le destinazioni database personalizzato per l'esecuzione diretta o per l'inclusione all'interno di un gruppo di database personalizzato. Ad esempio, in quanto **pool flessibile Database** non sono ancora direttamente supportati tramite PowerShell APIs, è possibile creare una destinazione di database personalizzato e di destinazione insieme database personalizzato che include tutti i database nel pool di.

Impostare le seguenti variabili per riflettere le informazioni sul database desiderato:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Per creare una raccolta di destinazione database personalizzato

Utilizzare il cmdlet [**New-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) per definire una destinazione insieme database personalizzati per abilitare l'esecuzione tra più destinazioni database definito. Dopo aver creato un gruppo di database, database possono essere associati destinazione insieme personalizzato.

Impostare le variabili seguenti per riflettere la configurazione di destinazione raccolta personalizzata desiderata:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Per aggiungere i database a un valore di destinazione insieme database personalizzato

Per aggiungere un database a una specifica raccolta personalizzato utilizzare il cmdlet [**AzureSqlJobChildTarget Aggiungi**](https://msdn.microsoft.comlibrary/mt346064.aspx) .

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Rivedere i database all'interno di un valore di destinazione insieme database personalizzato

Utilizzare il cmdlet [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) per recuperare i database figlio all'interno di un valore di destinazione insieme database personalizzato. 
 
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Creare un processo per eseguire uno script in un valore di destinazione insieme database personalizzato

Utilizzare il cmdlet [**New-AzureSqlJob**](https://msdn.microsoft.com/library/mt346078.aspx) per creare un processo in base a un gruppo di database definita dalla destinazione insieme database personalizzato. Processi di Database flessibile verranno espandere il processo in più processi figlio ognuno corrispondente a un database associato la destinazione della raccolta database personalizzato e assicurarsi che lo script viene eseguito in tutti i database. Nuovo, è importante che gli script siano idempotenti essere adattabile alle tentativi.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Raccolta di dati in database

È possibile utilizzare un processo per eseguire una query in un gruppo di database e inviare i risultati a una tabella specifica. Dopo aver fatto per visualizzare i risultati della query da ogni database, è possibile effettuarvi la tabella. In questo modo un metodo asincrono per eseguire una query in molti database. Tentativi vengono gestiti automaticamente tramite tentativi.

La tabella di destinazione specificata verrà creata automaticamente se non esiste ancora. La nuova tabella corrispondenti allo schema del set di risultati restituiti. Se uno script restituisce più set di risultati, processi di Database flessibile verranno inviati solo la prima tabella di destinazione.

Il seguente script di PowerShell esegue uno script e raccoglie i risultati in una tabella specificata. Questo script si presuppone che sia stato creato uno script T-SQL che restituisce un singolo set di risultati e che sia stata creata una destinazione insieme database personalizzato.

Questo script utilizza il cmdlet [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) . Impostare i parametri di destinazione esecuzione script e credenziali:

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

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Per creare e avviare un processo per gli scenari di raccolta dati

Questo script utilizza il cmdlet [**Start AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346055.aspx) .
 
    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>Per pianificare un trigger di esecuzione del processo

Il seguente script di PowerShell possono essere usate per creare una pianificazione ricorrente. Questo script utilizza un intervallo di minuti, ma [**AzureSqlJobSchedule nuovo**](https://msdn.microsoft.com/library/mt346068.aspx) supporta anche parametri - DayInterval, - HourInterval, - MonthInterval e - WeekInterval. Possono essere create le pianificazioni da eseguire una sola volta per il passaggio - unica.

Creare una nuova programmazione:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>Per avviare un processo eseguito in un calendario

Per eseguire un processo in base a un calendario, è possibile definire un trigger di processo. Il seguente script di PowerShell possono essere usate per creare un trigger di processo.

Utilizzare [AzureSqlJobTrigger nuovo](https://msdn.microsoft.com/library/mt346069.aspx) e impostare le seguenti variabili in modo che corrispondano alla pianificazione e del processo desiderato:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    –JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Per rimuovere un'associazione programmata per interrompere l'esecuzione sulla programmazione processo

Per interrompere l'esecuzione del processo ricorrenti tramite un trigger di processo, è possibile rimuovere il trigger di processo. Rimuovere un trigger di processo per interrompere un processo di eseguire in base a una programmazione utilizzando il [**cmdlet AzureSqlJobTrigger Rimuovi**](https://msdn.microsoft.com/library/mt346070.aspx).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Recuperare i trigger di processo associati a un calendario

Per ottenere e visualizzare i trigger di processo registrati per la programmazione di un determinato periodo di tempo, è possibile utilizzare il seguente script di PowerShell.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Per recuperare i trigger di processo associata a un processo 

Utilizzare [Get-AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346067.aspx) per ottenere e visualizzare le pianificazioni contenente un processo registrato.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Per creare un'applicazione di livello di dati (DACPAC) per l'esecuzione in database

Per creare un DACPAC, vedere [le applicazioni di livello di dati](https://msdn.microsoft.com/library/ee210546.aspx). Per distribuire un DACPAC, utilizzare il [cmdlet New-AzureSqlJobContent](https://msdn.microsoft.com/library/mt346085.aspx). Il DACPAC deve essere accessibile al servizio. È consigliabile caricare una DACPAC creato allo spazio di archiviazione di Azure e creare una [Firma di Access condiviso](../storage/storage-dotnet-shared-access-signature-part-1.md) per la DACPAC.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Per aggiornare un'applicazione di livello di dati (DACPAC) per l'esecuzione in database

Per indirizzare al nuovo URI, è possibile aggiornare DACPACs esistente registrato all'interno di processi di Database flessibile. Utilizzare il [**cmdlet Set-AzureSqlJobContentDefinition**](https://msdn.microsoft.com/library/mt346074.aspx) per aggiornare l'URI DACPAC su un DACPAC registrati esistente:

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Per creare un processo per applicare un'applicazione di livello di dati (DACPAC) in database

Dopo aver creato una DACPAC all'interno di flessibile processi di Database, un processo può essere creato per applicare la DACPAC in un gruppo di database. Il seguente script di PowerShell possono essere usate per creare un processo DACPAC in una raccolta personalizzata dei database:

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
