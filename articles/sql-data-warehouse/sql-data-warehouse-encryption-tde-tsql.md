<properties
   pageTitle="Crittografia dati trasparente SQL Data warehouse (Transact-SQL) | Microsoft Azure"
   description="Crittografia dati trasparente (TDE) SQL Data warehouse (Transact-SQL)"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="get-started-with-transparent-data-encryption-tde"></a>Iniziare con la crittografia dati trasparente (TDE)


> [AZURE.SELECTOR]
- [Cenni preliminari sulla sicurezza](sql-data-warehouse-overview-manage-security.md)
- [Autenticazione](sql-data-warehouse-authentication.md)
- [Crittografia (Portal)](sql-data-warehouse-encryption-tde.md)
- [Crittografia (Transact-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permssions"></a>Autorizzazioni necessarie

Per abilitare la crittografia dati trasparente (TDE), è necessario essere un amministratore o un membro del ruolo dbmanager.

## <a name="enabling-encryption"></a>Abilitazione della crittografia

Seguire questa procedura per attivare TDE per SQL Data Warehouse:

1. Connettersi al database *master* nel server che ospita i database utilizzando un account di accesso che non è un amministratore o un membro del ruolo **dbmanager** nel database master
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Disabilitazione della crittografia

Seguire questa procedura per disattivare TDE per SQL Data Warehouse:

1. Connettersi al database *master* utilizzando un account di accesso che non è un amministratore o un membro del ruolo **dbmanager** nel database master
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [AZURE.NOTE] Prima di apportare modifiche alle impostazioni TDE deve riprendere un Data Warehouse di SQL in pausa.

## <a name="verifying-encryption"></a>Verifica della crittografia

Per verificare lo stato di crittografia ai SQL Data Warehouse, seguire la procedura seguente:

1. Connettersi al database *master* o istanza utilizzando un account di accesso che non è un amministratore o un membro del ruolo **dbmanager** nel database master
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Un risultato ```1``` indica un database crittografato ```0``` indica un database non crittografato.

## <a name="encryption-dmvs"></a>Crittografia viste  

- [Sys. Databases][] 
- [Sys.dm_pdw_nodes_database_encryption_keys][]


<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[Sys. Databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[Sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
