<properties
    pageTitle="Codici di errore SQL - errore di connessione di database | Microsoft Azure"
    description="Informazioni sui codici di errore SQL per le applicazioni client di Database SQL, ad esempio comuni errori di connessione di database, problemi di copia del database e di errori generali. "
    keywords="codice errore SQL, linguaggio sql per access, errore di connessione di database, i codici di errore sql"
    services="sql-database"
    documentationCenter=""
    authors="annemill"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="annemill"/>


# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-error-and-other-issues"></a>Codici di errore SQL per le applicazioni client di Database SQL: errore di connessione e altri problemi di Database


<!--
Old Title on MSDN:  Error Messages (Azure SQL Database)
ShortId on MSDN:  ff394106.aspx
Dx 4cff491e-9359-4454-bd7c-fb72c4c452ca
-->


In questo articolo elenca i codici di errore SQL per le applicazioni client di Database SQL, inclusi errori di connessione di database, errori temporanei (denominati anche problemi temporanei), gli errori di governance delle risorse, problemi di copia del database, pool flessibile e altri errori. La maggior parte delle categorie specifiche per il Database di SQL Azure e non si applicano a Microsoft SQL Server.

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Errori di connessione di database, errori temporanei e altri errori temporanei

Nella tabella seguente descrive i codici di errore SQL per gli errori di perdita di connessione e altri errori temporanei che possono verificarsi quando si tenta di accedere a Database SQL. Per ottenere esercitazioni introduttiva su come connettersi al Database SQL Azure, vedere [connessione al Database SQL Azure](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Più comuni errori di connessione di database ed errori guasto temporaneo

L'infrastruttura di Azure è in grado di dinamicamente riconfigurare server quando si verificano carichi di lavoro nel servizio di Database SQL.  Questo comportamento dinamico potrebbe causare il programma client modificano la connessione al Database SQL. Questo tipo di condizione di errore viene definito un *errore temporaneo*.

Se il programma client contiene logica di ripetizione, può provare a ristabilire la connessione dopo aver dato ora guasto temporaneo correzione stesso.  È consigliabile posticipare per 5 secondi prima il primo tentativo. La ripetizione dopo un ritardo inferiore i rischi di 5 secondi confondere servizio cloud. Per ogni tentativi successivi il ritardo deve essere incrementato esponenziale, fino a un massimo di 60 secondi.

Errori temporanei manifesto in genere in uno dei seguenti messaggi di errore dai programmi client:

- Database < db_name > sul server < Azure_instance > non è attualmente disponibile. Provare la connessione in un secondo momento. Se il problema persiste, contattare il supporto clienti e fornire l'ID di analisi della sessione di < ID_sessione >

- Database < db_name > sul server < Azure_instance > non è attualmente disponibile. Provare la connessione in un secondo momento. Se il problema persiste, contattare il supporto clienti e fornire l'ID di analisi della sessione di < ID_sessione >. (Microsoft SQL Server, errore: 40613)

- Una connessione esistente forzare la è stata chiusa dall'host remoto.

- System.Data.Entity.Core.EntityCommandExecutionException: Si è verificato un errore durante l'esecuzione di definizione dei comandi. Vedere l'eccezione interna per informazioni dettagliate. ---> System.Data.SqlClient.SqlException: si è verificato un errore a livello di trasporto quando si ricevono risultati dal server. (provider: il Provider di sessione: 19 - connessione fisica non è possibile utilizzare)

Per esempi di codice della logica di ripetizione, vedere:

- [Raccolte di connessioni per Database SQL e SQL Server](sql-database-libraries.md) 
- [Azioni per la risoluzione di errori di connessione e temporaneo nel Database di SQL](sql-database-connectivity-issues.md)

Una discussione del *periodo di blocco* per client che utilizzano ADO.NET è disponibile in [SQL Server Connection pool (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Codici di errore guasto temporaneo

Gli errori seguenti sono temporanei e ripetere nella logica dell'applicazione 

| Codice di errore | Gravità | Descrizione |
| ---: | ---: | :--- |
| 4060 | 16 | Impossibile aprire il database "% & #x2a; ls" richiesto dall'account di accesso. L'account di accesso non è riuscita. |
|40197|17|Il servizio ha rilevato un errore durante l'elaborazione della richiesta. Riprovare. Codice di errore %d.<br/><br/>Viene visualizzato questo errore quando il servizio in basso a causa di software o aggiornamenti hardware, errori hardware o altri problemi di failover. Il codice di errore (%d) incorporato all'interno del messaggio di errore 40197 sono disponibili ulteriori informazioni sul tipo di errore o failover che si sono verificati. Alcuni esempi di errore codici incorporati all'interno del messaggio di errore 40197 sono 40020, 40143, 40166 e 40540.<br/><br/>Riconnettersi al server di Database SQL di connetterà automaticamente è a una copia corretta del database. L'applicazione deve intercettare 40197, log degli errori del codice di errore incorporati (%d) all'interno del messaggio per la risoluzione dei problemi e provare a riconnettersi al Database SQL fino a quando le risorse sono disponibili e nuovamente la connessione.|
|40501|20|Il servizio è occupato. Ripetere la richiesta dopo 10 secondi. Operazioni non consentite ID: %ls. Codice: %d.<br/><br/>*Nota:* Per ulteriori informazioni, vedere:<br/>• [Limiti delle risorse di Database SQL azure](sql-database-resource-limits.md).
|40613|17|Database '% & #x2a; ls' sul server '% & #x2a; ls' non è attualmente disponibile. Provare la connessione in un secondo momento. Se il problema persiste, contattare il supporto clienti e fornire l'ID di analisi della sessione di '% & #x2a; ls'.|
|49918|16|Non è possibile elaborare richiesta. Non è sufficiente risorse per l'elaborazione di richiesta.<br/><br/>Il servizio è occupato. Ripetere la richiesta in un secondo momento. |
|49919|16|Processo non è possibile creare o aggiornare richiesta. Troppo molti creare o aggiornare operazioni in corso per abbonamento "% ld".<br/><br/>Il servizio è occupato elaborazione più creare o aggiornare le richieste di abbonamento o il server. Le richieste sono bloccate per l'ottimizzazione delle risorse. Query [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) per operazioni in sospeso. Attendere fino a in sospeso crea o le richieste di aggiornamento sono state completate o eliminare una delle richieste in sospeso e ripetere la richiesta in un secondo momento. |
|49920|16|Non è possibile elaborare richiesta. Troppe operazioni in corso per abbonamento "% ld".<br/><br/>Il servizio è occupato più richieste per l'abbonamento. Le richieste di sono bloccate per l'ottimizzazione delle risorse. Query [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) dello stato dell'operazione. Attendere fino a quando non è in sospeso le richieste di sono state completate o eliminare una delle richieste in sospeso e ripetere la richiesta in un secondo momento. |

## <a name="database-copy-errors"></a>Errori di copia del database

Gli errori seguenti possono essere rilevati durante la copia di un database nel Database di SQL Azure. Per ulteriori informazioni, vedere [copiare un Database di SQL Azure](sql-database-copy.md).


|Codice di errore|Gravità|Descrizione|
|---:|---:|:---|
|40635|16|Client con indirizzo IP '% & #x2a; ls' è temporaneamente disabilitato.|
|40637|16|Creare copia del database è disattivato.|
|40561|16|Copia del database non è riuscita. Database di origine o di destinazione non esiste.|
|40562|16|Copia del database non è riuscita. Il database di origine è stato eliminato.|
|40563|16|Copia del database non è riuscita. Il database di destinazione è stato eliminato.|
|40564|16|Copia del database non è riuscita a causa di un errore interno. Eliminare il database di destinazione e riprovare.|
|40565|16|Copia del database non è riuscita. Non più di 1 Copia database contemporaneamente la stessa origine è consentita. Eliminare il database di destinazione e riprovare.|
|40566|16|Copia del database non è riuscita a causa di un errore interno. Eliminare il database di destinazione e riprovare.|
|40567|16|Copia del database non è riuscita a causa di un errore interno. Eliminare il database di destinazione e riprovare.|
|40568|16|Copia del database non è riuscita. Database di origine è diventato non disponibile. Eliminare il database di destinazione e riprovare.|
|40569|16|Copia del database non è riuscita. Database di destinazione è diventato non disponibile. Eliminare il database di destinazione e riprovare.|
|40570|16|Copia del database non è riuscita a causa di un errore interno. Eliminare il database di destinazione e riprovare.|
|40571|16|Copia del database non è riuscita a causa di un errore interno. Eliminare il database di destinazione e riprovare.|

## <a name="resource-governance-errors"></a>Errori di governance delle risorse

I seguenti errori sono causati da un uso eccessivo di risorse mentre si lavora con Database di SQL Azure. Per esempio:

- Una transazione è stata aperta troppo a lungo.
- Una transazione è blocchi troppi.
- Un'applicazione viene utilizzato troppa memoria.
- Utilizzo di un'applicazione è troppo `TempDb` spazio.

Argomenti correlati:

* Sono disponibili informazioni più dettagliate qui: [limiti delle risorse di Database SQL Azure](sql-database-resource-limits.md)

|Codice di errore|Gravità|Descrizione|
|---:|---:|:---|
|10928|20|ID risorsa: %d. È stato raggiunto il limite di %s per il database perché è %d. Per ulteriori informazioni, vedere [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>ID risorsa indica la risorsa che ha raggiunto il limite. Per thread di lavoro, l'ID risorsa = 1. Per le sessioni, l'ID risorsa = 2.<br/><br/>*Nota:* Per ulteriori informazioni su questo errore e su come risolvere il problema, vedere:<br/>• [Limiti delle risorse Database SQL azure](sql-database-resource-limits.md). |
|10929|20|ID risorsa: %d. %S minimo garanzia è %d, il limite massimo è %d e l'utilizzo corrente per il database è %d. Tuttavia, il server è attualmente occupato per supportare le richieste di maggiore di %d per il database. Per ulteriori informazioni, vedere [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). In caso contrario, provare più tardi.<br/><br/>ID risorsa indica la risorsa che ha raggiunto il limite. Per thread di lavoro, l'ID risorsa = 1. Per le sessioni, l'ID risorsa = 2.<br/><br/>*Nota:* Per ulteriori informazioni su questo errore e su come risolvere il problema, vedere:<br/>• [Limiti delle risorse di Database SQL azure](sql-database-resource-limits.md).|
|40544|20|Il database ha raggiunto la quota di dimensioni. Partizione o eliminazione di dati, eliminare indici oppure consultare la documentazione per le possibili soluzioni.|
|40549|16|Sessione è terminata perché si dispone di una transazione lunga. Provare a ridurre le transazioni.|
|40550|16|La sessione è stata terminata perché è acquisito troppi blocchi. Provare a lettura o la modifica di un numero minore di righe in una sola transazione.|
|40551|16|La sessione è stata terminata a causa di un numero eccessivo `TEMPDB` l'uso. Provare a modificare la query per ridurre l'utilizzo di spazio di tabella temporanea.<br/><br/>*Suggerimento:* Se si usano oggetti temporanei, risparmiare spazio nel `TEMPDB` database mediante la rimozione di oggetti temporanei dopo che non sono più necessari per la sessione.|
|40552|16|La sessione è stata terminata a causa di utilizzo spazio log transazioni eccessivo. Provare a modificare un numero minore di righe in una sola transazione.<br/><br/>*Suggerimento:* Se si esegue in blocco consente di inserire utilizzando il `bcp.exe` utilità o `System.Data.SqlClient.SqlBulkCopy` per la classe, provare a usare il `-b batchsize` o `BatchSize` opzioni per limitare il numero di righe copiate nel server in ogni transazione. Per ricreare un indice con la `ALTER INDEX` istruzione, provare a utilizzare il `REBUILD WITH ONLINE = ON` opzione.|
|40553|16|La sessione è stata terminata per l'utilizzo di memoria eccessivo. Provare a modificare la query per l'elaborazione di righe inferiore.<br/><br/>*Suggerimento:* Ridurre il numero di `ORDER BY` e `GROUP BY` operazioni nel codice Transact-SQL consente di ridurre i requisiti di memoria della query.|

## <a name="elastic-pool-errors"></a>Errori pool flessibile

Gli errori seguenti riguardano la creazione e utilizzo Elastics pool.

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX_RESOURCE | Pool di flessibile ha raggiunto il limite di archiviazione. L'utilizzo di spazio di archiviazione per il pool flessibile non può superare (%d) MB. | Limite di spazio pool flessibile in MB. | Provare a scrivere dati in un database quando è stato raggiunto il limite di archiviazione del pool di flessibile. | Per aumentare DTUs del pool di flessibile se possibile per aumentare il limite di archiviazione, ridurre lo spazio di archiviazione usata dai singoli database all'interno del pool flessibile oppure rimuovere database dal pool di flessibile. |
| 10929 | EX_USER | %S minimo garanzia è %d, il limite massimo è %d e l'utilizzo corrente per il database è %d. Tuttavia, il server è attualmente occupato per supportare le richieste di maggiore di %d per il database. Vedere [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) per ricevere assistenza. In caso contrario, provare più tardi. | Le funzioni min DTU un database. Max DTU per ogni database | Il numero totale di colleghi simultanei (richieste) in tutti i database nel pool di flessibile tentato di superano il limite di pool. | Per aumentare DTUs del pool di flessibile se possibile per aumentare il limite di lavoro oppure rimuovere database dal pool di flessibile. |
| 40844 | EX_USER | Database '%ls' sul Server '%ls' è un database di edition '%ls' in un pool di flessibile e non è possibile specificare una relazione di copia continua. | nome del database, database edition, nome del server | Un comando StartDatabaseCopy per una db non premium in un pool flessibile. | Prossimamente |
| 40857 | EX_USER | Pool flessibile per server non viene trovato: '%ls', nome del pool flessibile: '%ls'. | nome del server. nome del pool flessibile | Pool flessibile specificato non esiste nel server specificato. | Specificare un nome di pool flessibile valida. |
| 40858 | EX_USER | Pool flessibile '%ls' esiste già nel server: '%ls' | nome del pool flessibile, nome del server | Il server logico specificato contiene già del pool flessibile specificato. | Specificare nuovo nome pool flessibile. |
| 40859 | EX_USER | Pool flessibile non supporta il livello di servizio '%ls'. | livello di servizio pool flessibile | Livelli di servizio specificato non sono supportati per il provisioning pool flessibile. | Fornire l'edizione corretta o lasciare vuoto il campo per utilizzare il livello di servizio predefinito livello di servizio. |
| 40860 | EX_USER | Combinazione di obiettivo '%ls' '%ls' e il servizio di pool flessibile è valida. | nome del pool flessibile; nome assuma livello del servizio | Flessibile obiettivo pool e il servizio è possibile specificare insieme solo se l'obiettivo di servizio viene specificato come 'ElasticPool'. | Specificare combinazione corretta del pool flessibile e obiettivo di servizio. |
| 40861 | EX_USER | L'edizione di database ' %. *! non può essere diverso da quello del livello di servizio pool flessibile che è ' %.* ls'. | edizione di database, il livello di servizio pool flessibile | L'edizione di database è diverso dal livello di servizio pool flessibile. | Non specificare un'edizione di database che è diversa da quello del livello di servizio pool flessibile.  Si noti che non è necessario specificare l'edizione di database. |
| 40862 | EX_USER | Nome del pool flessibile deve essere se viene specificato l'obiettivo di servizio pool flessibile. | Nessuno | Obiettivo servizio pool flessibile non identifica un pool flessibile. | Specificare il nome del pool flessibile se utilizza l'obiettivo di servizio pool flessibile. |
| 40864 | EX_USER | DTUs per il pool flessibile deve contenere almeno (%d) DTUs per livello di servizio ' % * ls'. | DTUs per flessibile pool. livello di servizio pool flessibile. | Tentativo di impostare DTUs per il pool flessibile sotto il limite minimo. | Ripetere l'impostazione di DTUs per l'elastici del pool di almeno limite minimo. |
| 40865 | EX_USER | DTUs per il pool flessibile non può superare (%d) DTUs per livello di servizio ' % * ls'. | DTUs per flessibile pool. livello di servizio pool flessibile. | Tentativo di impostare DTUs per il pool flessibile sopra il limite massimo. | Ripetere l'impostazione DTUs per il pool flessibile non deve superare il limite massimo. |
| 40867 | EX_USER | DTU max per ogni database deve essere eseguita almeno (%d) per il livello di servizio ' % * ls'. | Numero massimo DTU un database. livello di servizio pool flessibile | Tentativo di impostare max DTU per ogni database sotto il limite supportato. | È consigliabile utilizzare il livello di servizio pool flessibile che supporta l'impostazione desiderata. |
| 40868 | EX_USER | DTU max per ogni database non può superare (%d) per il livello di servizio ' % * ls'. | Max DTU un database. livello di servizio pool flessibile. | Tentativo di impostare max DTU per ogni database oltre il limite supportato. | È consigliabile utilizzare il livello di servizio pool flessibile che supporta l'impostazione desiderata. |
| 40870 | EX_USER | Min DTU per ogni database non può superare (%d) per il livello di servizio ' % * ls'. | Le funzioni min DTU un database. livello di servizio pool flessibile. | Tentativo di impostare min DTU per ogni database superano il limite supportato. | È consigliabile utilizzare il livello di servizio pool flessibile che supporta l'impostazione desiderata. |
| 40873 | EX_USER | Il numero di database (%d) e le funzioni min DTU per ogni database (%d) non può superare DTUs del pool di flessibile (%d). | Numero di database in pool flessibile. Le funzioni min DTU un database. DTUs del pool flessibile. | Il tentativo di specificare le funzioni min DTU per i database nel pool di flessibile che supera DTUs del pool di flessibile. | Aumentare DTUs del pool flessibile o Riduci min DTU per ogni database o ridurre il numero di database nel pool di flessibile. |
| 40877 | EX_USER | Un pool flessibile non può essere eliminato, a meno che non contiene tutti i database. | Nessuno | Pool di flessibile contiene uno o più database e pertanto non potranno essere eliminate. | Rimuovere i database dal pool di flessibile per eliminarla. |
| 40881 | EX_USER | Pool di flessibile ' % * ls ha raggiunto il limite di conteggio di database.  Il limite di conteggio di database per il pool flessibile non può superare (%d) per un pool flessibile con DTUs (%d). | Nome del pool flessibile. numero massimo di database del pool flessibile, e DTUs per pool di risorse. | Tentativo di creare o aggiungere database al pool flessibile quando è stato raggiunto il limite di conteggio database del pool di flessibile. | Per aumentare DTUs del pool di flessibile se possibile per aumentare il limite di database oppure rimuovere database dal pool di flessibile. |
| 40889 | EX_USER | La DTUs o il limite di archiviazione per il pool flessibile ' % * ls' non può essere ridotta poiché che non consente sufficiente spazio di archiviazione per i database. | Nome del pool flessibile. | Il tentativo di ridurre il limite di archiviazione del pool di flessibile sotto il relativo uso dello spazio di archiviazione. | Provare a ridurre l'utilizzo di spazio di archiviazione di singoli database nel pool di flessibile o rimuovere i database del pool per ridurre il DTUs o il limite di archiviazione. |
| 40891 | EX_USER | Min DTU per ogni database (%d) non può superare max DTU per ogni database (%d). | Le funzioni min DTU un database. DTU max per ogni database. | Tentativo di impostare min DTU per ogni database di dimensioni superiore a max DTU per ogni database. | Verificare che il valore minimo DTU per database non superi max DTU per ogni database. |
| TBD | EX_USER | Le dimensioni dello spazio di archiviazione per un singolo database in un pool flessibile non possono superare le dimensioni massime consentite per ' % * ls' service pool flessibile di livello. | livello di servizio pool flessibile | Dimensioni massime per il database superano le dimensioni massime consentite per il livello di servizio pool flessibile. | Impostare la dimensione massima del database entro i limiti delle dimensioni massime consentite per il livello di servizio pool flessibile. |

Argomenti correlati:

* [Creare un pool di database flessibile (c#)](sql-database-elastic-pool-create-csharp.md) 
* [Gestire un pool di database flessibile (c#)](sql-database-elastic-pool-manage-csharp.md). 
* [Creare un pool di database flessibile (PowerShell)](sql-database-elastic-pool-create-powershell.md) 
* [Monitorare e gestire un pool di database flessibile (PowerShell)](sql-database-elastic-pool-manage-powershell.md).

## <a name="general-errors"></a>Errori generali

Gli errori seguenti non rientrano in tutte le categorie precedente.

|Codice di errore|Gravità|Descrizione|
|---:|---:|:---|
|15006|16|<AdministratorLogin>non è un valore valido perché contiene caratteri non validi.|
|18452|14|Impossibile eseguire l'accesso. L'account di accesso è stato inviato da un dominio non attendibile e non possono essere usate con Windows authentication.%. e #x2a; ls (Login Windows non sono supportati in questa versione di SQL Server.)|
|18456|14|Impossibile eseguire l'accesso per utente "% #x2a;ls'.%. & #x2a ls % & #x2a; ls (l'accesso non riuscito per utente"% & #x2a; ls". Impossibile modificare la password. Modifica della password durante l'accesso non è supportata in questa versione di SQL Server.)|
|18470|14|Impossibile eseguire l'accesso per utente "% & #x2a; ls". Motivo: L'account è disabled.%. & #x2a; ls|
|40014|16|Più database non è possibile usare la stessa transazione.|
|40054|16|Le tabelle senza un indice cluster non sono supportate in questa versione di SQL Server. Creare un indice raggruppato e riprovare.|
|40133|15|Questa operazione non è supportata in questa versione di SQL Server.|
|40506|16|SID specificato non è valido per questa versione di SQL Server.|
|40507|16|' % & #x2a; non è possibile richiamare ls' con i parametri in questa versione di SQL Server.|
|40508|16|Istruzione uso non è supportata per spostarsi tra i database. Utilizzare una nuova connessione per connettersi a un altro database.|
|40510|16|Istruzione '% & #x2a; ls' non è supportato in questa versione di SQL Server|
|40511|16|Funzione incorporata '% & #x2a; ls' non è supportato in questa versione di SQL Server.|
|40512|16|Funzionalità obsolete '%ls' non è supportata in questa versione di SQL Server.|
|40513|16|Server variabile '% & #x2a; ls' non è supportato in questa versione di SQL Server.|
|40514|16|'%ls' non è supportata in questa versione di SQL Server.|
|40515|16|Riferimento al nome del database e/o server in '% & #x2a; ls' non è supportato in questa versione di SQL Server.|
|40516|16|Oggetti temp globali non sono supportati in questa versione di SQL Server.|
|40517|16|Opzione parola chiave o una dichiarazione "% & #x2a; ls" non è supportato in questa versione di SQL Server.|
|40518|16|Comando DBCC '% & #x2a; ls' non è supportato in questa versione di SQL Server.|
|40520|16|A protezione diretta classe "% S_MSG" non è supportato in questa versione di SQL Server.|
|40521|16|A protezione diretta classe "% S_MSG" non è supportata nell'ambito del server in questa versione di SQL Server.|
|40522|16|Tipo di identità '% & #x2a; ls' del database non è supportato in questa versione di SQL Server.|
|40523|16|Creazione di '% & #x2a; ls' utente implicita non è supportata in questa versione di SQL Server. Creare in modo esplicito all'utente prima di usarlo.|
|40524|16|Tipo di dati '% & #x2a; ls' non è supportato in questa versione di SQL Server.|
|40525|16|CON '%.ls' non è supportato in questa versione di SQL Server.|
|40526|16|' % & #x2a; provider di set di righe dei ls non supportati in questa versione di SQL Server.|
|40527|16|Server collegati non sono supportati in questa versione di SQL Server.|
|40528|16|Gli utenti non è possibile mappare i certificati, le chiavi asimmetriche o login di Windows in questa versione di SQL Server.|
|40529|16|Funzione incorporata '% & #x2a; ls' in rappresentazione contesto non è supportato in questa versione di SQL Server.|
|40532|11|Non è possibile aprire server "% & #x2a; ls" richiesto dall'account di accesso. L'account di accesso non è riuscita.|
|40553|16|La sessione è stata terminata a causa l'utilizzo di memoria eccessivo. Provare a modificare la query per elaborare meno righe.<br/><br/>*Nota:* Ridurre il numero di `ORDER BY` e `GROUP BY` operazioni nel codice Transact-SQL contribuisce a ridurre i requisiti di memoria della query.|
|40604|16|Impossibile non CREATE/ALTER DATABASE perché verrebbe superato la quota del server.|
|40606|16|Collegamento di database non è supportata in questa versione di SQL Server.|
|40607|16|Login Windows non sono supportati in questa versione di SQL Server.|
|40611|16|Server possono includere al massimo 128 regole del firewall definite.|
|40614|16|Indirizzo IP iniziale regola del firewall non può superare indirizzo IP finale.|
|40615|16|Non è possibile aprire server '{0}' richiesto dall'account di accesso. Client con indirizzo IP '{1}' non è autorizzato ad accedere al server.  Per abilitare l'accesso, utilizzare il portale di Database SQL o eseguire sp_set_firewall_rule nel database master per creare una regola per l'indirizzo IP o l'intervallo di indirizzi.  Può richiedere fino a cinque minuti rendere effettiva la modifica.|
|40617|16|Il nome della regola firewall che inizia con <rule name> è troppo lungo. Lunghezza massima è 128.|
|40618|16|Il nome della regola firewall non può essere vuoto.|
|40620|16|L'accesso non riuscito per utente "% & #x2a; ls". Impossibile modificare la password. Modifica della password durante l'accesso non è supportata in questa versione di SQL Server.|
|40627|20|Operazione su server '{0}' e il database '{1 \}' è in corso. Attendere alcuni minuti e riprovare.|
|40630|16|Convalida della password non è riuscita. La password non soddisfa i requisiti perché è troppo breve.|
|40631|16|La password specificata è troppo lunga. La password deve avere non superare i 128 caratteri.|
|40632|16|Convalida della password non è riuscita. La password non soddisfa i requisiti perché non è sufficientemente complessa.|
|40636|16|Non è possibile usare un nome di database riservato '% & #x2a; ls' in questa operazione.|
|40638|16|Id abbonamento non validi < id abbonamento >. Sottoscrizione non esiste.|
|40639|16|Richiesta non è conforme allo schema: <schema error>.|
|40640|20|Il server ha rilevato un errore inaspettato.|
|40641|16|Nella posizione specificata non è valida.|
|40642|17|Il server è occupato. Provare un secondo momento.|
|40643|16|Il valore di intestazione x-ms-versione specificato è valido.|
|40644|14|Impossibile autorizzare l'accesso all'abbonamento specificato.|
|40645|16|NomeServer <servername> non possono essere vuoti o null. Si possono essere eseguita solo di lettere minuscole 'a'-'z', il numero 0-9 e il trattino. Il trattino non cliente potenziale o ricerca del nome.|
|40646|16|ID abbonamento non può essere vuoto.|
|40647|16|Abbonamento < id abbonamento non ha nomeserver.|
|40648|17|Troppe richieste sono state effettuate. Riprovare più tardi.|
|40649|16|Tipo di contenuto non valido specificato. È supportata solo applicazione/xml.|
|40650|16|Sottoscrizione < id abbonamento > non esiste o non è pronto per l'operazione.|
|40651|16|Impossibile creare server perché la sottoscrizione < id abbonamento > è disattivata.|
|40652|16|Non è possibile spostare o creare server. Abbonamento < id abbonamento > supera la quota server.|
|40671|17|Errore di comunicazione tra il gateway e il servizio di gestione. Riprovare più tardi.|
|40852|16|Impossibile aprire il database ' %. *ls' sul server ' %.* ls' richiesto dall'account di accesso. Accesso al database è consentito solo con una stringa di connessione protetto. Per accedere a questo database, modificare le stringhe di connessione per contenere sicuro nel server FQDN - "nome server".database.windows .net deve essere modificato in "nome server".database. `secure`. windows.net.|
|45168|16|Il sistema di SQL Azure è in condizioni di carico e inserisce un limite superiore sull'operazioni CRUD DB simultanee per un singolo server (ad esempio, creare database). Il server specificato nel messaggio di errore ha superato il numero massimo di connessioni simultanee. E riprovare.|
|45169|16|Il sistema SQL azure è in condizioni di carico e inserisce un limite superiore al numero di operazioni CRUD server simultanee per una singola sottoscrizione (ad esempio, creare server). L'abbonamento specificato nel messaggio di errore ha superato il numero massimo di connessioni simultanee e negata la richiesta. E riprovare.|

## <a name="related-links"></a>Collegamenti correlati

- [Linee guida e le limitazioni generale di Database SQL Azure](sql-database-general-limitations.md)
- [Limiti di risorse del Database di SQL Azure](sql-database-resource-limits.md)
