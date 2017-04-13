<properties
    pageTitle="Connettersi al Database SQL utilizzando Java con JDBC in Windows | Microsoft Azure"
    description="Presenta un esempio di codice Java che è possibile utilizzare per connettersi al Database SQL Azure. Nell'esempio vengono utilizzati JDBC e viene eseguita in un computer client di Windows."
    services="sql-database"
    documentationCenter=""
    authors="LuisBosquez"
    manager="jhubbard"
    editor="genemi"/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="lbosq"/>


# <a name="connect-to-sql-database-by-using-java-with-jdbc-on-windows"></a>Connettersi al Database SQL utilizzando Java con JDBC in Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


In questo argomento viene presentato un esempio di codice Java che è possibile utilizzare per connettersi al Database SQL Azure. Nell'esempio di linguaggio si basa sul linguaggio Development Kit (JDK) versione 1.8. Nell'esempio si connette a un Database di SQL Azure tramite il driver JDBC.

## <a name="step-1--configure-development-environment"></a>Passaggio 1: Configurare l'ambiente di sviluppo

[Configurare l'ambiente di sviluppo per lo sviluppo di linguaggio](https://msdn.microsoft.com/library/mt720658.aspx)

## <a name="step-2-create-a-sql-database"></a>Passaggio 2: Creare un database SQL

Vedere la [pagina Introduzione a](sql-database-get-started.md) per informazioni su come creare un database.  

## <a name="step-3-get-connection-string"></a>Passaggio 3: Ottenere una stringa di connessione

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] Se si utilizza il driver JTDS JDBC, quindi è necessario aggiungere "ssl = Richiedi" per l'URL della connessione stringa ed è necessario impostare l'opzione seguente per JVM "-Djsse.enableCBCProtection=false". Questa opzione JVM disattiva una soluzione per un problema di protezione, dunque, è importante che i rischi impliciti prima di impostare questa opzione.

## <a name="step-4-run-sample-code"></a>Passaggio 4: Eseguire il codice di esempio

* [Prova pratica la connessione a SQL mediante Java](https://msdn.microsoft.com/library/mt720656.aspx)

## <a name="next-steps"></a>Passaggi successivi

* Visitare il [Centro per sviluppatori di linguaggio](/develop/java/).
* Esaminare la [Panoramica di sviluppo di Database SQL](sql-database-develop-overview.md)
* Ulteriori informazioni su [Microsoft JDBC Driver di SQL Server](https://msdn.microsoft.com/library/mt484311.aspx)

## <a name="additional-resources"></a>Risorse aggiuntive 

* [Modelli di progettazione per applicazioni multi-tenant SaaS con Database SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Esplorare tutte le [funzionalità di Database SQL](https://azure.microsoft.com/services/sql-database/)
