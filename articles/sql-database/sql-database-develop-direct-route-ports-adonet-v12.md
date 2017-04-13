<properties 
    pageTitle="Porte 1433 per Database SQL | Microsoft Azure"
    description="Connessioni client da ADO.NET a V12 di Database SQL Azure può succedere che ignorano il proxy e interagiscono direttamente con il database. Porte diverso da 1433 diventano importante."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="" />


<tags 
    ms.service="sql-database" 
    ms.workload="drivers"
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016"
    ms.author="annemill"/>


# <a name="ports-beyond-1433-for-adonet-45-and-sql-database-v12"></a>Porte 1433 per ADO.NET 4.5 e V12 di Database SQL


In questo argomento descrive le modifiche che V12 di Database SQL Azure riporta il comportamento di connessione dei client che utilizzano ADO.NET 4.5 o versione successiva.


## <a name="v11-of-sql-database-port-1433"></a>V11 del Database SQL: porta 1433


Quando il programma client utilizza ADO.NET 4.5 per la connessione e della query con V11 di Database SQL, la sequenza interna è la seguente:


1. ADO.NET tenta di connettersi al Database SQL.

2. ADO.NET utilizza la porta 1433 per chiamare un modulo middleware e middleware si connette al Database SQL.

3. Database SQL di inviare la risposta al middleware, che inoltra la risposta alla ADO.NET alla porta 1433.


**Terminologia:** Vengono descritte sequenza precedente, in cui viene indicato che ADO.NET interagisce con Database di SQL utilizzando *il routing proxy*. Se si è interessati non middleware, abbiamo detto che è stata utilizzata la *route diretta* .


## <a name="v12-of-sql-database-outside-vs-inside"></a>V12 del Database SQL: esterne vs all'interno


Per le connessioni a V12, è necessario richiedere se il programma client viene eseguita *all'esterno* o *interno* il limite di Azure cloud. Le sottosezioni illustrati due scenari comuni.


#### <a name="outside-client-runs-on-your-desktop-computer"></a>*Esterno:* Client eseguibile sul computer desktop


La porta 1433 sia la porta sola deve essere aperta nel computer desktop che ospita l'applicazione client di Database SQL.


#### <a name="inside-client-runs-on-azure"></a>*All'interno:* Client viene eseguito su Azure


Il client viene eseguita all'interno del limite di Azure cloud, verrà usata quando cosa è possibile chiamare una *route diretta* per interagire con il server di Database SQL. Dopo la connessione, altre interazioni tra il client e database implicano l'invio di alcun proxy middleware.


La sequenza è come indicato di seguito:


1. ADO.NET 4,5 (o versioni successive) avvia una breve interazione con il cloud Azure e riceve un numero di porta identificata in modo dinamico.
 - Il numero di porta in modo dinamico identificati è nell'intervallo di 11000 11999 o 14000 14999.

2. ADO.NET quindi si connette al server di Database SQL direttamente con alcun middleware tra.

3. Le query vengono inviate direttamente al database e vengono restituiti risultati direttamente al client.


Verificare che la porta degli intervalli di 11000 11999 e 14999 14000 nel computer client Azure rimangono disponibili per le interazioni client ADO.NET 4.5 con V12 di Database SQL.

- In particolare, porte nell'intervallo devono essere esenti da qualsiasi altro blocchi in uscita.

- Nella macchina virtuale Azure, **Windows Firewall con sicurezza avanzata** controlla le impostazioni della porta.
 - È possibile utilizzare l' [interfaccia utente del firewall](http://msdn.microsoft.com/library/cc646023.aspx) per aggiungere una regola per il quale si specifica il protocollo **TCP** insieme a un intervallo di porte con una sintassi simile **11000 11999**.


## <a name="version-clarifications"></a>Chiarimenti versione


In questa sezione illustra i moniker che fanno riferimento a versioni precedenti del prodotto. Inoltre, sono elencati alcuni associazioni delle versioni tra prodotti.


#### <a name="adonet"></a>ADO.NET


- ADO.NET 4.0 supporta il protocollo td 7.3, ma non 7.4.
- ADO.NET 4,5 e versioni successive supporta il protocollo td 7.4.


#### <a name="sql-database-v11-and-v12"></a>V12 e V11 di Database SQL


In questo argomento vengono evidenziate le differenze di connessione client tra SQL Database V11 e V12.


*Nota:* L'istruzione Transact-SQL `SELECT @@version;` restituisce un valore che iniziano con un numero, ad esempio "11". oppure "12" e tali corrispondono ai nomi di versione di V11 e V12 per Database SQL.


## <a name="related-links"></a>Collegamenti correlati


- ADO.NET 4.6 è stata rilasciata il 20 luglio 2015. Annuncio di un blog del team .NET è disponibile [qui](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).


- ADO.NET 4.5 è stata rilasciata il 15 agosto 2012. Annuncio di un blog del team .NET è disponibile [qui](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
 - È disponibile un post di blog su ADO.NET 4.5.1 [qui](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).


- [Elenco versione protocollo td](http://www.freetds.org/userguide/tdshistory.htm)


- [Panoramica di sviluppo di Database SQL](sql-database-develop-overview.md)


- [Firewall di Database SQL Azure](sql-database-firewall-configure.md)


- [Procedura: configurare le impostazioni del firewall nel Database SQL](sql-database-configure-firewall-settings.md)

