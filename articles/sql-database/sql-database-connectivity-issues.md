<properties
    pageTitle="Correggere un errore di connessione SQL, l'errore temporaneo | Microsoft Azure"
    description="Informazioni su come risolvere i problemi, diagnosi ed evitare un errore SQL connessione o temporaneo in Database SQL Azure. "
    keywords="connessione a SQL, stringa di connessione, problemi di connettività, errore temporaneo, errore di connessione"
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="daleche"/>


# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>Risoluzione dei problemi, diagnosi ed evitare errori di connessione SQL e temporanee per Database SQL

In questo articolo viene descritto come evitare, risolvere i problemi, diagnosi e alleviare errori di connessione e temporanee che l'applicazione client rileva quando interagisce con Database di SQL Azure. Informazioni su come configurare logica di ripetizione, creare la stringa di connessione e modificare altre impostazioni di connessione.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Temporaneo errori (errori temporanei)

Un errore temporaneo - inoltre temporanea guasto - ha una causa principale che non appena lo risolve. Un'occasionale causa errori temporanei è quando il sistema di Azure rapidamente si sposta risorse hardware migliore di bilanciamento del carico vari carichi di lavoro. Maggior parte degli eventi riconfigurazione spesso completati in meno di 60 secondi. Durante questo periodo di tempo riconfigurazione, potrebbe essere problemi di connettività al Database SQL Azure. Applicazioni che si connettono a Database SQL Azure devono essere compilate prevedono questi errori temporanei, gestirli tramite l'implementazione della logica di Riprova riscontrati nel codice, anziché con la loro visualizzazione agli utenti come gli errori dell'applicazione.

Se il programma client utilizza ADO.NET, il programma verrà comunicato sull'errore temporaneo per la generazione di un **SqlException**. La proprietà **numero** può essere confrontata con l'elenco di errori temporanei nella parte superiore dell'argomento: [codici di errore SQL per le applicazioni client di Database SQL](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Connessione e comando

Si verrà riprovare a eseguire la connessione di SQL o stabilire nuovamente, a seconda delle operazioni seguenti:

* **Si verifica un errore temporaneo durante un tentativo di connessione**: ripetere la connessione dopo ritardare per alcuni secondi.

* **Si verifica un errore temporaneo durante un comando di query SQL**: il comando non deve essere ripetuto immediatamente. Se, tuttavia, dopo un ritardo, stabilire la connessione deve essere appena. Quindi è possibile ripetere il comando.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

### <a name="retry-logic-for-transient-errors"></a>Ripetere la logica per errori temporanei


Programmi client che subire occasionalmente un errore temporaneo sono più efficaci se contengono logica di ripetizione.


Quando l'applicazione comunica con Database di SQL Azure tramite un 3 ° middleware di terze parti, sapere con il fornitore se middleware contiene logica riprova per errori temporanei.

<a id="principles-for-retry" name="principles-for-retry"></a>

#### <a name="principles-for-retry"></a>Principi per tentativi


- Ripetere un tentativo di apertura di una connessione se l'errore è temporaneo.


- Un'istruzione SQL SELECT viene interrotta con un errore temporaneo non ripetere direttamente.
 - Se, tuttavia, stabilire una nuova connessione e quindi riprovare a selezionare.


- Quando un'istruzione SQL UPDATE non riesce con un errore temporaneo, è necessario stabilire una connessione aggiornata prima ritentata l'aggiornamento.
 - La logica di ripetizione è necessario assicurarsi che la transazione intero database effettuate oppure che l'intera transazione verrà ripristinata.


#### <a name="other-considerations-for-retry"></a>Altre considerazioni per tentativi


- Un programma batch che viene avviato automaticamente dopo ore di lavoro e che verrà completata prima mattina, investire molto pazienti con tempo intervalli di tempo tra il numero di tentativi.


- La tendenza umana a dopo aver troppo lungo Attendi è necessario definire un programma di interfaccia utente.
 - La soluzione non deve tentativo ogni pochi secondi, in quanto tale criterio può intasare al sistema di richieste.


#### <a name="interval-increase-between-retries"></a>Aumento dell'intervallo tra tentativi



È consigliabile posticipare per 5 secondi prima del primo tentativo. La ripetizione dopo un ritardo inferiore i rischi di 5 secondi confondere servizio cloud. Per ogni tentativi successivi il ritardo deve essere incrementato esponenziale, fino a un massimo di 60 secondi.

Una discussione del *periodo di blocco* per client che utilizzano ADO.NET è disponibile in [SQL Server Connection pool (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

È anche consigliabile impostare un numero massimo di tentativi prima che il programma autonomo termina.


#### <a name="code-samples-with-retry-logic"></a>Esempi di codice con logica di ripetizione


Esempi di codice con logica di Riprova in una vasta gamma di linguaggi di programmazione, sono disponibili in:

- [Raccolte di connessioni per Database SQL e SQL Server](sql-database-libraries.md)


<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

#### <a name="test-your-retry-logic"></a>Testare la logica di ripetizione


Per testare la logica di Riprova, è necessario simulare o causare un errore che possono essere corretti durante il programma è ancora in esecuzione.


##### <a name="test-by-disconnecting-from-the-network"></a>Eseguire il test, disconnettersi dalla rete.


Per testare la logica riprova è possibile disconnettere il computer client dalla rete durante l'esecuzione del programma. L'errore sarà:

- **SqlException. Number** = 11001
- Messaggio: "non host sconosciuto"


Come parte del primo tentativo di tentativi, il programma possibile correggere l'errore di ortografia e quindi il tentativo di connessione.


Per rendere pratica, scollegare il computer dalla rete prima di avviare il programma. Quindi il programma riconosce un parametro fase di esecuzione per il programma:

1. Aggiungere temporaneamente 11001 all'elenco degli errori da considerare come temporaneo.
2. Provare la connessione prima come di consueto.
3. Dopo l'errore viene rilevata, rimuovere 11001 dall'elenco.
4. Visualizzare un messaggio che indica l'utente di collegare il computer alla rete.
 - Posizionare il puntatore ulteriormente esecuzione utilizzando il metodo **console. ReadLine** o una finestra di dialogo con un pulsante OK. Viene premuto il tasto INVIO dopo che il computer collegato alla rete.
5. Ritenta per la connessione, che prevede la riuscita.


##### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Testare digitato in modo errato il nome del database durante la connessione


Il programma può deliberatamente errato il nome utente prima del primo tentativo di connessione. L'errore sono:

- **SqlException. Number** = 18456
- Messaggio: "accesso non riuscito per utente 'WRONG_MyUserName'."


Come parte del primo tentativo di tentativi, il programma possibile correggere l'errore di ortografia e quindi il tentativo di connessione.


Per rendere pratica, il programma può riconoscere un parametro fase di esecuzione che causa il programma per:

1. Aggiungere temporaneamente 18456 all'elenco degli errori da considerare come temporaneo.
2. Aggiungere deliberatamente 'WRONG_' il nome utente.
3. Dopo l'errore viene rilevata, rimuovere 18456 dall'elenco.
4. Rimuovere 'WRONG_' dal nome dell'utente.
5. Ritenta per la connessione, che prevede la riuscita.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

### <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parametri SqlConnection .NET per tentativo di connessione


Se il programma client si connette al Database di SQL Azure con .NET Framework categoria **SqlConnection**, è necessario utilizzare .NET 4.6.1 o versione successiva in modo che è possibile sfruttare la funzionalità di tentativi di connessione. Informazioni dettagliate della caratteristica sono disponibili [qui](http://go.microsoft.com/fwlink/?linkid=393996).


<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->


Quando si creano la [stringa di connessione](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) per l'oggetto **SqlConnection** , è necessario coordinare valori tra i parametri seguenti:

- ConnectRetryCount &nbsp; &nbsp; *(valore predefinito è 1. Intervallo è compreso tra 0 e 255)*
- ConnectRetryInterval &nbsp; &nbsp; *(il valore predefinito è 1 secondo. Intervallo da 1 a 60.)*
- Timeout della connessione &nbsp; &nbsp; *(impostazione predefinita è 15 secondi. Intervallo è compreso tra 0 e 2147483647)*


In particolare, i valori scelti devono semplificare il seguente di uguaglianza true:

- Timeout della connessione = ConnectRetryCount * ConnectionRetryInterval

Ad esempio, se il conteggio = 3 e intervallo = 10 secondi, un timeout di solo 29 secondi non abbastanza sarà il sistema abbastanza tempo per il relativo Riprova 3 ° e finale nella connessione: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Connessione e comando


I parametri **ConnectRetryCount** e **ConnectRetryInterval** consentono all'oggetto **SqlConnection** ripetere l'operazione di connessione senza avvisare o richiedere l'intervento del programma, ad esempio restituire il controllo al programma. I tentativi possono verificarsi nelle situazioni seguenti:

- mySqlConnection.Open chiamata
- chiamata al metodo mySqlConnection.Execute

Non è abbastanza particolare. Se si verifica un errore temporaneo durante l'esecuzione di *query* , l'oggetto **SqlConnection** non ripetere l'operazione di connettersi e sicuramente non Riprova la query. Tuttavia, **SqlConnection** controlla molto rapidamente la connessione prima dell'invio di query per l'esecuzione. Se il controllo rapido viene rilevato un problema di connessione, **SqlConnection** tentativi dell'operazione di connessione. Se il tentativo ha avuto esito positivo, si query viene inviata per l'esecuzione.


#### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>ConnectRetryCount deve essere combinato con logica riprova dell'applicazione?

Si supponga che l'applicazione ha ritentare personalizzato efficaci. Può ripetere l'operazione Connetti 4 volte. Se si aggiunge **ConnectRetryInterval** e **ConnectRetryCount** = 3 alla stringa di connessione, si aumenta il numero di tentativi a 4 * 3 = 12 tentativi. Potrebbe non si desidera che un numero elevato di tentativi.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-azure-sql-database"></a>Connessioni a Database SQL Azure

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Connessione: Stringa di connessione


La stringa di connessione necessaria per la connessione al Database SQL Azure è leggermente diversa dalla stringa di connessione a Microsoft SQL Server. È possibile copiare la stringa di connessione per il database dal [Portale di Azure](https://portal.azure.com/).


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Connessione: Indirizzo IP


È necessario configurare il server di Database SQL per accettare comunicazioni da indirizzo IP del computer che ospita il programma client. È possibile modificare le impostazioni del firewall tramite il [Portale di Azure](https://portal.azure.com/).


Se si dimentica configurare l'indirizzo IP, l'applicazione non viene eseguito con un messaggio di errore utile in cui è indicato l'indirizzo IP necessaria.


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


Per ulteriori informazioni, vedere: [How to: configurare le impostazioni del firewall nel Database di SQL](sql-database-configure-firewall-settings.md)


<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Connessione: porte


In genere è sufficiente per assicurarsi che la porta 1433 sia aperta per le comunicazioni in uscita, nel computer che ospita programma client.


Ad esempio, quando il programma client è ospitato in un computer Windows, Windows Firewall nell'host consente di aprire la porta 1433:


1. Aprire il pannello di controllo
2. &gt;Tutti gli elementi di Pannello di controllo
3. &gt;Windows Firewall
4. &gt;Impostazioni avanzate
5. &gt;Regole in uscita
6. &gt;Azioni
7. &gt;Nuova regola


Se il programma client è ospitato su una Azure macchine (), è consigliabile leggere:<br/>[Porte 1433 per ADO.NET 4.5 e V12 di Database SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


Per informazioni generali sulla configurazione dell'indirizzo IP e porte, vedere: [firewall di Database SQL Azure](sql-database-firewall-configure.md)


<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>Connessione: ADO.NET 4.6.1


Se il programma utilizza le classi ADO.NET come **SqlConnection** per connettersi al Database SQL Azure, è consigliabile utilizzare .NET Framework versione 4.6.1 o versione successiva.


ADO.NET 4.6.1:

- Per il Database di SQL Azure, è maggiore affidabilità quando si apre una connessione tramite il metodo di **SqlConnection.Open** . Il metodo **Open** incorpora migliori basata Riprova in risposta a problemi temporanei, per alcuni errori all'interno del periodo di timeout della connessione.
- Supporta il pool di connessioni. Sono inclusi una verifica efficiente funzionante l'oggetto di connessione che consente l'applicazione.



Quando si utilizza un oggetto di connessione da un pool di connessioni, è consigliabile che il programma di temporaneamente chiudere la connessione non subito usarla. Riaprire una connessione non è costosa è la modalità di creazione di una nuova connessione.


Se si utilizza ADO.NET 4.0 o versioni precedenti, è consigliabile eseguire l'aggiornamento a ADO.NET ultima.

- Prima del 2015 novembre, è possibile [scaricare ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx).


<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostica

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostica: Verificare l'utilità è possono connettersi


Se il programma non riesce a connettersi al Database SQL Azure, diagnostica è per provare a connettersi con un'utilità. L'utilità ideale connette usando la stessa raccolta che usa il programma.


In qualsiasi computer Windows, provare a eseguire queste utilità:

- SQL Server Management Studio (ssms.exe), che si connette tramite ADO.NET.
- SQLCMD.exe, si connette tramite [ODBC](http://msdn.microsoft.com/library/jj730308.aspx).


Una volta connessa, verificare se una query SQL SELECT breve funziona.


<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostica: Controllare le porte aperte


Si supponga che tentativi di connessione hanno esito negativo a causa di problemi di porta. Nel computer è possibile eseguire un'utilità per comunicare per le configurazioni di porta.


Su Linux seguenti utilità potrebbero risultare utili:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`
 - (Modificare il valore di esempio per l'indirizzo IP).


In Windows [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) utilità può essere utile. Ecco un'esecuzione di esempio che query situazione porta in un server di Database SQL Azure e che è stata eseguita in un computer portatile:


```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnostico: Gli errori di registro


Può succedere che meglio è diagnosticare un problema intermittente del rilevamento automatico di un modello generale su giorni o settimane.


Il client può facilitare una diagnosi effettuando l'accesso a tutti gli errori che incontra. Potrebbe essere possibile correlare le voci di log con dati di Database SQL Azure registri stesso internamente errore.


Enterprise raccolta 6 (EntLib60) offre classi gestito .NET per consentire la registrazione:

- [5 - semplice come cadere in un file di Log: utilizzare il blocco di applicazione di registrazione](http://msdn.microsoft.com/library/dn440731.aspx)


<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostica: Esaminare file di registro di sistema per gli errori


Ecco alcune istruzioni Transact-SQL selezionare tale log delle query di errore e altre informazioni.


| Query di log | Descrizione |
| :-- | :-- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` | La visualizzazione [sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) sono disponibili informazioni sui singoli eventi, tra cui alcuni che possono causare errori temporanei o problemi di connettività.<br/><br/>È possibile ideale correlare valori **ora_avvio** o **end_time** con le informazioni quando il programma client si sono verificati problemi.<br/><br/>**Suggerimento:** È necessario connettersi al database **master** per eseguire questo strumento. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` | La visualizzazione di [sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) offre aggregati conteggi dei tipi di eventi, per diagnostica aggiuntivo.<br/><br/>**Suggerimento:** È necessario connettersi al database **master** per eseguire questo strumento. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostica: Ricerca per gli eventi problema del Registro di Database SQL


È possibile cercare sugli eventi di problema nel registro del Database di SQL Azure. Provare a eseguire l'istruzione SELECT Transact-SQL seguente nel database **master** :


```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>Alcune righe restituite da sys.fn_xe_telemetry_blob_target_read_file


È quindi come potrebbe apparire una riga restituita. I valori null riportati spesso non sono null in altre righe.


```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Raccolta di Enterprise 6


Enterprise raccolta 6 (EntLib60) è una struttura di classi .NET che consente di implementare affidabile client di servizi cloud, uno dei quali è il servizio di Database SQL Azure. È possibile individuare argomenti dedicati a ogni area in cui è possibile supportare EntLib60 visitando prima:

- [Raccolta di Enterprise 6: aprile 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)


Logica di ripetizione per la gestione degli errori temporanei è un'area in cui EntLib60 consentono di:

- [4 - perseverance, segreto del luogo tutti: utilizzare il blocco di applicazione di gestione degli errori temporanei](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)


> [AZURE.NOTE] Il codice sorgente per EntLib60 è disponibile per pubblico [scaricare](http://go.microsoft.com/fwlink/p/?LinkID=290898). Microsoft, non prevede di apportare ulteriori EntLib degli aggiornamenti delle caratteristiche o gli aggiornamenti di manutenzione.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Classi EntLib60 per errori temporanei e riprovare


Le classi EntLib60 seguenti sono particolarmente utili per ritentare. Tutti si tratta o sotto lo spazio dei nomi **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

*Spazio dei nomi* *Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling* *:*

- **RetryPolicy** classe
 - Metodo **denominato**


- **ExponentialBackoff** classe


- **SqlDatabaseTransientErrorDetectionStrategy** classe


- **ReliableSqlConnection** classe
 - Metodo **ExecuteCommand**


Spazio dei nomi **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- **AlwaysTransientErrorDetectionStrategy** classe

- **NeverTransientErrorDetectionStrategy** classe


Qui sono disponibili collegamenti a informazioni EntLib60:

- Gratuita [prenotare Download: Guida per gli sviluppatori alla libreria di Microsoft Enterprise, 2a edizione](http://www.microsoft.com/download/details.aspx?id=41145)

- Procedure consigliate: [Riprova indicazioni generali](../best-practices-retry-general.md) sono eccellente informazioni dettagliate sulla logica di ripetizione.

- Download di NuGet della [Raccolta Enterprise - gestione guasto temporaneo blocco applicazione 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: Il blocco di registrazione


- Il blocco di registrazione è una soluzione configurabile e flessibile che consente di:
 - Creare e archiviare i messaggi di log in un'ampia varietà di posizioni.
 - Categorizzare e filtrare i messaggi.
 - Raccogliere informazioni contestuali dei requisiti di registrazione utili per il debug e analisi, oltre che per il controllo e generale.


- Il blocco di registrazione estrae la funzionalità di registrazione dalla destinazione log in modo che il codice dell'applicazione è coerenza, indipendentemente dalla posizione e tipo di archivio di registrazione di destinazione.


Per informazioni dettagliate, vedere: [5 - come semplice come posizionate disattivare un Log: utilizzare il blocco di applicazione registrazione](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Codice sorgente del metodo IsTransient EntLib60


Successivamente, dalla classe **SqlDatabaseTransientErrorDetectionStrategy** è il codice sorgente c# per il metodo di **IsTransient** . Il codice sorgente illustra gli errori sono stati considerati temporanei e l'opportunità di tentativi, a partire da aprile 2013.

Numerose righe **//comment** sono stati rimossi da questa copia per mettere in risalto la leggibilità.


```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## <a name="next-steps"></a>Passaggi successivi

- Per risolvere altri problemi di connessione di Database SQL Azure comuni, vedere [risoluzione dei problemi di connessione al Database SQL Azure](sql-database-troubleshoot-common-connection-issues.md).

- [SQL Server pool di connessioni (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)


- [*Tentativo in corso* è che un Apache 2.0 concesso in licenza generica raccolta ripetizione, scritta in **Python**per semplificare le attività dell'aggiunta di comportamento Riprova a qualsiasi tipo.](https://pypi.python.org/pypi/retrying)
