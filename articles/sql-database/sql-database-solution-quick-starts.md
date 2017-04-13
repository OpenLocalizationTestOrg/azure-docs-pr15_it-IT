<properties
   pageTitle="Avvio rapido soluzione di Database SQL Azure | Microsoft Azure"
   description="Informazioni sulle soluzioni di Database SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="09/06/2016"
   ms.author="carlrab"/>

# <a name="explore-azure-sql-database-solution-quick-starts"></a>Esplorare avvio rapido soluzione di Database SQL Azure

In questo articolo viene presentata una panoramica di avvio veloce di Azure SQL Database soluzione. Queste guide rapide si trovano nel repository esempi GitHub SQL Server e viene illustrato l'utilizzo del Database di SQL in una soluzione completa basata su scenari reali. Per semplici esercitazioni che illustrano l'utilizzo di una particolare funzionalità di Database SQL, vedere [esercitazioni esplorare il Database SQL Azure](sql-database-explore-tutorials.md).

## <a name="try-the-wingtiptickets-demo-and-hands-on-lab"></a>Provare la demo WingTipTickets ed esercitazione pratica

La demo [WingTipTickets di Database SQL Azure](https://github.com/microsoft/wingtiptickets) ed esercitazione pratica illustrato l'utilizzo di un Database SQL Azure e applicazione di esempio in base a ricerche Azure utilizzato per vendere i ticket concerto.


## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools"></a>Raccogliere e monitorare i dati di utilizzo delle risorse tra più pool

[Soluzione Guida introduttiva: telemetria Pool flessibile tramite PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) offre una soluzione per la raccolta e controllarne l'utilizzo delle risorse di Database SQL più pool di un abbonamento. Dopo aver configurato un numero elevato di database di una sottoscrizione, è complesso per monitorare ogni pool flessibile separatamente.

Per risolvere il problema, è possibile combinare i cmdlet di PowerShell di Database SQL e query T-SQL per la raccolta di dati di utilizzo delle risorse dal pool di più e i database. Consente di controllare e analizzare in modo più efficiente Uso risorse.

Questa Guida introduttiva fornisce un set di script di PowerShell e query T-SQL insieme documentazione qual è la soluzione e la relativa implementazione.

## <a name="get-started-with-elastic-database-in-an-saas-scenario"></a>Guida introduttiva a Database flessibile in uno scenario di SaaS

 [Soluzione Guida introduttiva: dashboard personalizzato Pool flessibile per SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) offre una soluzione per uno scenario come-a-soluzione Software (SaaS) che utilizza la funzionalità di Database flessibile di Database SQL per fornire un conveniente e scalabilità database back-end per un'applicazione di SaaS.

In questa soluzione, verificherà tramite l'implementazione di un'app web. Questa applicazione web consente di visualizzare il carico creato in un database flessibile da un generatore di carico che utilizza un dashboard personalizzato che integrano il portale di Azure.

Questa Guida introduttiva fornisce un generatore di carico e monitoraggio app web insieme la documentazione relativa a che cosa significa l'app e come utilizzarlo.

## <a name="create-an-azure-sql-database-by-using-code-first-development-and-the-entity-framework"></a>Creare un database SQL Azure con sviluppo Code First e Framework entità

Il video e un esempio di [Codice prima in un nuovo Database](https://msdn.microsoft.com/data/jj193542.aspx) offre un'introduzione allo sviluppo Code First destinato a un nuovo database. Questo scenario è destinato a un database che non esiste, ma che verranno creati prima di codice. In alternativa, lo scenario consente di creare un database vuoto in cui il primo codice vengono aggiunte le nuove tabelle.

Codice prima di tutto consente di definire il modello utilizzando le classi c# o Visual Basic .NET. È possibile eseguire ulteriori facoltativi tramite gli attributi le classi e proprietà o tramite un'API intuitiva.

## <a name="integrate-elastic-database-tools-into-an-entity-framework-application"></a>Integrazione strumenti Database flessibile in un'applicazione di entità Framework

La [libreria client Database flessibile con entità Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) vengono illustrate le modifiche che è necessario apportare a un'applicazione di entità Framework per l'integrazione con [strumenti Database flessibile](sql-database-elastic-scale-get-started.md). Lo stato attivo è la composizione [condiviso mappare gestione](sql-database-elastic-scale-shard-map-management.md) e [del routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) con l'entità Framework codice primo approccio.

[Codice prima di tutto per un nuovo database di esempio per EF](http://msdn.microsoft.com/data/jj193542.aspx) viene utilizzato come esempio operativo nell'intera in questo esempio. Il codice di esempio associato a questo documento fa parte del set di strumenti di Database flessibile di campioni negli esempi di codice di Visual Studio.

## <a name="integrate-elastic-database-tools-with-row-level-security"></a>Integrazione strumenti Database flessibile con protezione a livello di riga

[Applicazioni multi-tenant con strumenti Database flessibile e protezione a livello di riga](sql-database-elastic-tools-multi-tenant-row-level-security.md) sono visualizzate le modifiche che è necessario apportare a un'applicazione di entità Framework per l'integrazione [strumenti Database flessibile](sql-database-elastic-scale-get-started.md) con [protezione a livello di riga](https://msdn.microsoft.com/library/dn765131). In questo esempio viene illustrato come utilizzare queste tecnologie per creare un'applicazione di un livello di dati scalabilità che supporta shards multi-tenant.

È tramite SqlClient ADO.NET o Framework entità. In questo esempio estende la [libreria client Database flessibile con entità Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) mediante l'aggiunta di supporto per i database condiviso multi-tenant.
Viene creata una semplice applicazione console per la creazione di blog e tutti i post, con quattro tenant e due database condiviso multi-tenant.

## <a name="create-online-surveys-with-the-tailspin-surveys-application"></a>Creazione di sondaggi in linea con l'applicazione Tailspin sondaggi

Questa [applicazione di esempio Tailspin sondaggi](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) è un'applicazione web multi-tenant, denominata sondaggi, che consente agli utenti di creare sondaggi in linea. Nell'esempio risolve alcuni problemi relativi alla chiave sulla gestione delle identità utente in un'applicazione multi-tenant, tra cui per l'abbonamento, autenticazione, autorizzazione e i ruoli di app.

## <a name="learn-about-the-latest-security-features-of-sql-database-with-the-contoso-clinic-demo-application"></a>Informazioni sulle funzionalità di protezione più recente di Database SQL con l'applicazione di Contoso corso Demo

Questa [applicazione Contoso corso Demo](https://github.com/Microsoft/azure-sql-security-sample) illustra le caratteristiche di protezione più recente di Database SQL.

## <a name="next-steps"></a>Passaggi successivi

[Esplorare le esercitazioni di Database SQL Azure](sql-database-explore-tutorials.md)
