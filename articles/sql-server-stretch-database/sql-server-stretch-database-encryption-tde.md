<properties
   pageTitle="Abilitare la crittografia dati trasparente (TDE) per Database di SQL Server estensione su Azure | Microsoft Azure"
   description="Abilitare la crittografia dati trasparente (TDE) per Database di SQL Server estensione su Azure"
   services="sql-server-stretch-database"
   documentationCenter=""
   authors="douglaslMS"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-server-stretch-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/14/2016"
   ms.author="douglaslMS"/>

# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Attivare la crittografia dati trasparente (TDE) per Database estensione su Azure
> [AZURE.SELECTOR]
- [Portale di Azure](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

Crittografia dati trasparente (TDE) consente di evitare il rischio di attività dannoso eseguendo in tempo reale crittografia e decrittografia del database, backup associati e file di registro delle transazioni inattivi senza apportare modifiche all'applicazione.

TDE Crittografa lo spazio di archiviazione di un intero database utilizzando una chiave simmetrica denominata la chiave di crittografia del database. La chiave di crittografia di database è protetto da un certificato server incorporato. Il certificato server incorporato sia univoco per ogni server Azure. Microsoft Ruota automaticamente i certificati almeno ogni 90 giorni. Per una descrizione generale del TDE, vedere [La crittografia dati trasparente (TDE)].

##<a name="enabling-encryption"></a>Abilitazione della crittografia

Per abilitare TDE per un Azure database in cui archiviazione i dati viene eseguita la migrazione da un database di SQL Server abilitato zoom, eseguire le operazioni seguenti:

1. Aprire il database nel [portale di Azure](https://portal.azure.com)
2. In e il database, fare clic sul pulsante **Impostazioni**
3. Selezionare l'opzione di **crittografia dati trasparente**![][1]
4. Selezionare l'impostazione **in** e quindi selezionare **Salva**
![][2]


##<a name="disabling-encryption"></a>Disabilitazione della crittografia

Per disattivare TDE per un Azure database in cui archiviazione i dati viene eseguita la migrazione da un database di SQL Server abilitato zoom, eseguire le operazioni seguenti:

1. Aprire il database nel [portale di Azure](https://portal.azure.com)
2. In e il database, fare clic sul pulsante **Impostazioni**
3. Selezionare l'opzione di **crittografia dati trasparente**
4. Selezionare l'impostazione **disattivare** e quindi selezionare **Salva**




<!--Anchors-->
[Crittografia dati trasparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
