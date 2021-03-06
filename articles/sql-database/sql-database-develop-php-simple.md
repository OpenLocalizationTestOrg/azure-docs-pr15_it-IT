<properties
    pageTitle="Connettersi al Database SQL tramite PHP su Windows | Microsoft Azure"
    description="Presenta un esempio di programma PHP che si connette al Database SQL Azure da un client di Windows e vengono forniti collegamenti ai componenti software necessari richiesti dal client."
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="php"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="meetb"/>


# <a name="connect-to-sql-database-by-using-php-on-windows"></a>Connettersi al Database SQL tramite PHP su Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


In questo argomento viene illustrato come è possibile connettersi al Database SQL Azure da un'applicazione client scritta in PHP viene eseguito su Windows.

## <a name="step-1--configure-development-environment"></a>Passaggio 1: Configurare l'ambiente di sviluppo

[Configurare l'ambiente di sviluppo per lo sviluppo PHP](https://msdn.microsoft.com/library/mt720663.aspx)

## <a name="step-2-create-a-sql-database"></a>Passaggio 2: Creare un database SQL

Vedere la [pagina Introduzione a](sql-database-get-started.md) per informazioni su come creare un database di esempio.  È importante che seguire la Guida per creare un **modello di database AdventureWorks**. Negli esempi viene illustrati di seguito funzionano solo con lo **schema di AdventureWorks**.


## <a name="step-3-get-connection-details"></a>Passaggio 3: Ottenere informazioni di connessione

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## <a name="step-4-run-sample-code"></a>Passaggio 4: Eseguire il codice di esempio

* [Prova pratica la connessione a SQL utilizzando PHP](https://msdn.microsoft.com/library/mt720665.aspx)
* [Connettersi resiliently SQL con PHP](https://msdn.microsoft.com/library/mt720667.aspx)


## <a name="next-steps"></a>Passaggi successivi

* Esaminare la [Panoramica di sviluppo di Database SQL](sql-database-develop-overview.md)
* Ulteriori informazioni su [Microsoft PHP Driver di SQL Server](https://msdn.microsoft.com/library/dn865013.aspx)
* Per ulteriori informazioni sull'installazione di PHP e l'utilizzo, vedere [Accesso ai database di SQL Server con PHP](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx).

## <a name="additional-resources"></a>Risorse aggiuntive 

* [Modelli di progettazione per applicazioni multi-tenant SaaS con Database SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Esplorare tutte le [funzionalità di Database SQL](https://azure.microsoft.com/services/sql-database/)
