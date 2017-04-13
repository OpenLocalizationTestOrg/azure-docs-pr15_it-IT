<properties
   pageTitle="Transazioni distribuite tra database cloud"
   description="Panoramica flessibile le transazioni del Database con Database SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="torsteng"
   manager="jhubbard"
   editor="torsteng"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="sql-database"
   ms.date="05/27/2016"
   ms.author="torsteng"/>

# <a name="distributed-transactions-across-cloud-databases"></a>Transazioni distribuite tra database cloud

Le transazioni del database flessibile per il Database di SQL Azure (SQL DB) consentono di eseguire operazioni che si estendono su più database nel database di SQL. Le transazioni del database flessibile per SQL DB sono disponibili per le applicazioni di .NET tramite ADO .NET e integrano con la familiare esperienza di programmazione utilizzando le classi [System. Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) . Per ottenere la raccolta, vedere [.NET Framework 4.6.1 (programma di installazione Web)](https://www.microsoft.com/download/details.aspx?id=49981).

In locale, ad esempio uno scenario richiesti in genere in esecuzione Microsoft Distributed Transaction coordinatore (MSDTC). Poiché MSDTC non è disponibile per l'applicazione di piattaforma come servizio in Azure, la possibilità di coordinare transazioni distribuite è ora stati direttamente integrata DB SQL. Le applicazioni possono connettersi a un Database di SQL per avviare le transazioni distribuite e uno dei database in modo trasparente dovrà coordinare transazione distribuita, come illustrato nella figura seguente. 

  ![Transazioni distribuite con Database di SQL Azure con le transazioni del database flessibile ][1]

## <a name="common-scenarios"></a>Scenari comuni

Le transazioni del database flessibile per SQL DB consentono alle applicazioni di apportare modifiche specifiche ai dati archiviati in più database SQL. L'anteprima è incentrata su esperienze sviluppo lato client in c# e .NET. È prevista un'esperienza sul lato server mediante T-SQL per un secondo momento.  
Le transazioni del database flessibile destinato a situazioni seguenti:

* Le applicazioni più database in Azure: con questo scenario, dati verticalmente suddiviso tra diversi database nel database di SQL in modo che diversi tipi di dati si trovano sul database diversi. Alcune operazioni richiedono le modifiche apportate ai dati che si trova in due o più database. L'applicazione utilizza le transazioni del database flessibile per coordinare il database le modifiche e garantire atomicità.

* Applicazioni di database sharded in Azure: con questo scenario, il livello di dati utilizza la [libreria client Database flessibile](sql-database-elastic-database-client-library.md) o self-sharding per orizzontalmente dividere i dati tra molti database nel database di SQL. Un caso di utilizzo visibili è la necessità di eseguire modifiche specifiche per un'applicazione multi-tenant sharded quando modifiche estesi tenant. Pensare, ad esempio il trasferimento da un tenant a un altro, sia che si trovano nel database diversi. Un secondo caso è diffusamente sharding in base alle esigenze di capacità per un tenant di grandi dimensioni che a sua volta in genere implica che alcune operazioni atomiche deve allontanare tra diversi database utilizzati per il tenant stesso. Un terzo caso è atomici aggiornamenti per fare riferimento ai dati che vengono replicati in database. Essere coordinate atomiche operazioni transazione, lungo le linee ora tra diversi database utilizzando l'anteprima.
Le transazioni del database flessibile usare commit in per garantire atomicità transazione vari database. Si tratta di una soluzione ideale per le transazioni che includono meno di 100 database alla volta in una sola transazione. Questi limiti non vengono applicati, ma uno aspettarsi prestazioni e successo flessibile le transazioni del database a subire quando superano questi limiti.


## <a name="installation-and-migration"></a>Installazione e la migrazione

Le funzionalità flessibile le transazioni del database nel database di SQL vengono fornite tramite aggiornamenti apportati alle librerie di .NET System.Data.dll e System.Transactions.dll. DLL garantire che commit in due fasi è eventualmente affinché atomicità. Per avviare lo sviluppo di applicazioni che utilizzano le transazioni del database flessibile, installare [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) o versione successiva. Durante l'esecuzione in una versione precedente di .NET framework, le transazioni avrà esito negativo alzare di livello a una transazione distribuita e verrà generata un'eccezione.

Dopo l'installazione, è possibile utilizzare l'API di transazione distribuita in Transactions con le connessioni al database SQL. Se si dispone di applicazioni di MSDTC con queste API, dopo l'installazione di 4.6.1 ricostruire semplicemente applicazioni esistenti per 4.6 .NET Framework. Se i progetti destinazione 4.6 .NET, utilizzano automaticamente DLL aggiornata rispetto alla nuova versione di Framework e transazione distribuita che API chiamate in combinazione con le connessioni al database SQL ora avrà esito positivo.

Tenere presente che le transazioni del database flessibile non richiede l'installazione di MSDTC. Se, tuttavia, le transazioni del database flessibile sono gestite direttamente da e all'interno di DB SQL. Questo semplifica notevolmente scenari cloud poiché una distribuzione di MSDTC non è necessaria utilizzare le transazioni distribuite con DB SQL. Sezione 4 spiegato in modo più dettagliato come distribuire le transazioni del database flessibile e richiesto .NET framework insieme alle applicazioni cloud di Azure.

## <a name="development-experience"></a>Ambiente di sviluppo

### <a name="multi-database-applications"></a>Applicazioni di database multiple

Il codice di esempio seguente viene usata la familiare esperienza di programmazione con .NET Transactions. La classe TransactionScope stabilisce una transazione di ambiente in .NET. (Una "transazione" è quello che si trova nel thread corrente). Tutte le connessioni aperte all'interno di TransactionScope partecipano alla transazione. Se fa parte di un database diverso, la transazione viene automaticamente elevata a una transazione distribuita. Il risultato della transazione verrà controllato mediante l'impostazione dell'ambito da completare per indicare il commit.

    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }

### <a name="sharded-database-applications"></a>Applicazioni di database sharded
 
Le transazioni del database flessibile per DB SQL supportano anche coordinare transazioni distribuite nel punto in cui è utilizzare il metodo OpenConnectionForKey della libreria client database flessibile per aprire le connessioni dati in scala indietro livello. Valutare la possibilità di casi in cui è necessario per garantire la coerenza delle modifiche tra diversi i valori di chiave sharding diversi. Connessioni a shards che ospita i valori di chiave sharding diversi vengono negoziate utilizzando OpenConnectionForKey. In generale, le connessioni possono essere diverso shards in modo da garantire garanzie transazione richiede una transazione distribuita. Nell'esempio seguente viene illustrato questo approccio. Si presuppone che una variabile denominata shardmap viene utilizzata per rappresentare una mappa condiviso dalla raccolta di client di database flessibile:

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }
        
        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>Installazione di .NET per servizi Cloud Windows Azure

Azure offre diverse offerte alle applicazioni .NET host. Un confronto delle offerte diversi è disponibile in [servizio App Azure, Cloud Services e macchine virtuali di confronto](../app-service-web/choose-web-site-cloud-service-vm.md). Se il sistema operativo guest dell'offerta è minore di .NET 4.6.1 necessari per le transazioni flessibile, è necessario aggiornare il sistema operativo guest a 4.6.1. 

Per i servizi di App Azure, aggiornamenti per il sistema operativo guest attualmente non sono supportati. Per macchine virtuali di Azure, è sufficiente accedere la macchina virtuale ed eseguire il programma di installazione per .NET framework più recente. Per i servizi Cloud di Azure, è necessario includere l'installazione di una versione più recente di .NET in attività avvio della distribuzione. I concetti e i passaggi indicati nella [Installare .NET su un ruolo di servizio Cloud](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Si noti che il programma di installazione per .NET 4.6.1 potrebbe richiedere ulteriore spazio di archiviazione temporaneo durante il processo di avvio automatico servizi cloud Azure più il programma di installazione per .NET 4.6. Per garantire la corretta installazione, è necessario aumentare temporaneo spazio di archiviazione per il servizio cloud Azure nel file ServiceDefinition.csdef nella sezione LocalResources e le impostazioni dell'ambiente dell'attività iniziale, come illustrato nell'esempio seguente:

    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
    
## <a name="transactions-across-multiple-servers"></a>Transazioni su più server

Le transazioni del database flessibile sono supportate nei Database SQL Azure diversi server logici. Quando le transazioni attraversano i limiti del server logico, server partecipanti prima di tutto necessario essere immessa in una relazione reciproca comunicazioni. Dopo averlo creata la relazione di comunicazione, qualsiasi database in uno dei due server può partecipare transazioni flessibile con i database di altro server. Con le transazioni che interessano più di due server logici, una relazione di comunicazione deve essere in posizione per qualsiasi coppia di server logici.

Utilizzare i seguenti cmdlet di PowerShell per gestire le relazioni di comunicazione tra i server per le transazioni del database flessibile:

* **Nuovo AzureRmSqlServerCommunicationLink**: utilizzare questo cmdlet per creare una nuova relazione di comunicazione tra due server logici nel database di SQL Azure. La relazione è simmetrico che entrambi i server possono avviare transazioni con l'altro server.
* **Get-AzureRmSqlServerCommunicationLink**: utilizzare questo cmdlet per recuperare le relazioni esistenti di comunicazione e le relative proprietà.
* **Rimuovi AzureRmSqlServerCommunicationLink**: utilizzare questo cmdlet per rimuovere una relazione di comunicazione. 


## <a name="monitoring-transaction-status"></a>Monitorare lo stato di transazione

Utilizzare dinamico Gestione viste nel database di SQL per monitorare lo stato e lo stato di avanzamento delle transazioni del database flessibile in corso. Tutte le viste correlate alle transazioni sono rilevanti per le transazioni distribuite nel database di SQL. È possibile trovare corrispondente all'interno delle viste qui: [transazione correlati viste a gestione dinamica e funzioni (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).
 
Sulle sono particolarmente utili:

* **sys.dm\_tran\_active\_transazioni**: elenchi transazioni attive e il relativo stato. La colonna UOW (unità di lavoro) consente di identificare le transazioni figlio diversi appartenenti alla stessa transazione distribuita. Tutte le transazioni all'interno della stessa transazione distribuita eseguono lo stesso valore UOW. Vedere la [documentazione DMV](https://msdn.microsoft.com/library/ms174302.aspx) per altri dettagli.
* **sys.dm\_tran\_database\_transazioni**: fornisce informazioni aggiuntive sulle transazioni, ad esempio la posizione della transazione nel registro. Vedere la [documentazione DMV](https://msdn.microsoft.com/library/ms186957.aspx) per altri dettagli.
* **sys.dm\_tran\_blocchi**: fornisce informazioni sui blocchi attualmente utilizzate da transazioni in corso. Vedere la [documentazione DMV](https://msdn.microsoft.com/library/ms190345.aspx) per altri dettagli.

## <a name="limitations"></a>Limitazioni 

Per le transazioni del database flessibile nel database di SQL valide attualmente le limitazioni seguenti:

* Sono supportate solo le transazioni su database in SQL DB. Altri [X / aprire XA](https://en.wikipedia.org/wiki/X/Open_XA) provider di risorse e database esterno DB SQL non è possibile partecipare le transazioni del database flessibile. Questo significa che le transazioni del database flessibile non è possibile estendere su locale SQL Server e database di SQL Azure. Per le transazioni distribuite in locale, continuare a usare MSDTC. 
* Sono supportate solo le transazioni coordinate client da un'applicazione .NET. Il supporto di lato server per T-SQL, ad esempio iniziare DISTRIBUTED TRANSACTION è pianificata, ma non è ancora disponibile. 
* Sono supportati solo ai database di V12 DB di SQL Azure.
* Transazioni in tutti i servizi WCF non sono supportate. Ad esempio, si dispone di un metodo di servizio WCF che esegue una transazione. Quando la chiamata in un ambito di transazione non viene eseguito come un [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="additional-resources"></a>Risorse aggiuntive

Non ancora usando funzioni di database flessibile per le applicazioni Azure? Consultare la [Documentazione](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/). Per eventuali domande, vedere raggiungere Contattaci nel [forum di Database SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) e le richieste di funzionalità, aggiungerle al [Database SQL di area feedback](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png



