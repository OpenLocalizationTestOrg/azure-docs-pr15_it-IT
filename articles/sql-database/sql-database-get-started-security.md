<properties
    pageTitle="Esercitazione di Database SQL: Guida introduttiva di sicurezza"
    description="Informazioni su come creare account utente per accedere e gestire un database."
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/17/2016"
    ms.author="carlrab"/>

# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>Esercitazione di Database SQL: creare database SQL di account utente di accedere e gestire un database


> [AZURE.SELECTOR]
- [Esercitazione introduttiva Get](sql-database-get-started-security.md)
- [Concedere l'accesso](sql-database-manage-logins.md)

In questa esercitazione si imparerà a utilizzare SQL Server Management Studio (SQL Server Management Studio) per:

- Accedere al Database SQL utilizzando un account di accesso a livello di server principale.
- Creare un account utente di Database SQL.
- Un Database SQL concedere agli utenti [le autorizzazioni di db_owner](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0).
- Connettersi a un database SQL con un account utente che non è un'entità a livello di server.

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]


[AZURE.INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]


## <a name="next-steps"></a>Passaggi successivi
Ora che si aver completato questa esercitazione di Database SQL e creato un account utente e delle autorizzazioni utente account dbo, si è pronti per ulteriori informazioni sulla [protezione di Database SQL](sql-database-manage-logins.md).


