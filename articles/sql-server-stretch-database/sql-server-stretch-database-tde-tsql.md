<properties
   pageTitle="Abilitare la crittografia dati trasparente (TDE) per Database di SQL Server estensione su Azure TSQL | Microsoft Azure"
   description="Abilitare la crittografia dati trasparente (TDE) per Database di SQL Server estensione su TSQL Azure"
   services="sql-server-stretch-database"
   documentationCenter=""
   authors="douglaslMS"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-server-stretch-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/14/2016"
   ms.author="douglaslMS"/>

# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Attivare la crittografia dati trasparente (TDE) per Database estensione su Azure (Transact-SQL)
> [AZURE.SELECTOR]
- [Portale di Azure](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

Crittografia dati trasparente (TDE) consente di evitare il rischio di attività dannoso eseguendo in tempo reale crittografia e decrittografia del database, backup associati e file di registro delle transazioni inattivi senza apportare modifiche all'applicazione.

TDE Crittografa lo spazio di archiviazione di un intero database utilizzando una chiave simmetrica denominata la chiave di crittografia del database. La chiave di crittografia di database è protetto da un certificato server incorporato. Il certificato server incorporato sia univoco per ogni server Azure. Microsoft Ruota automaticamente i certificati almeno ogni 90 giorni. Per una descrizione generale del TDE, vedere [La crittografia dati trasparente (TDE)].

##<a name="enabling-encryption"></a>Abilitazione della crittografia

Per abilitare TDE per un Azure database in cui archiviazione i dati viene eseguita la migrazione da un database di SQL Server abilitato zoom, eseguire le operazioni seguenti:

1. Connettersi al database *master* nel server Azure che ospita i database utilizzando un account di accesso che non è un amministratore o un membro del ruolo **dbmanager** nel database master
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

##<a name="disabling-encryption"></a>Disabilitazione della crittografia

Per disattivare TDE per un Azure database in cui archiviazione i dati viene eseguita la migrazione da un database di SQL Server abilitato zoom, eseguire le operazioni seguenti:

1. Connettersi al database *master* utilizzando un account di accesso che non è un amministratore o un membro del ruolo **dbmanager** nel database master
2. Eseguire l'istruzione seguente per crittografare il database.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

##<a name="verifying-encryption"></a>Verifica della crittografia

Per verificare lo stato di crittografia per un database di Azure che contiene i dati da un database di SQL Server abilitato zoom, eseguire le operazioni seguenti:

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


<!--Anchors-->
[Crittografia dati trasparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
