<properties
   pageTitle="Crittografia dati trasparente SQL Data warehouse (portale) | Microsoft Azure"
   description="Crittografia dati trasparente (TDE) SQL Data warehouse"
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

# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Iniziare con la crittografia di dati trasparente (TDE) SQL Data warehouse

> [AZURE.SELECTOR]
- [Cenni preliminari sulla sicurezza](sql-data-warehouse-overview-manage-security.md)
- [Autenticazione](sql-data-warehouse-authentication.md)
- [Crittografia (Portal)](sql-data-warehouse-encryption-tde.md)
- [Crittografia (Transact-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permssions"></a>Autorizzazioni necessarie

Per abilitare la crittografia dati trasparente (TDE), è necessario essere un amministratore o un membro del ruolo dbmanager.

## <a name="enabling-encryption"></a>Abilitazione della crittografia

Per attivare TDE per SQL Data Warehouse, seguire la procedura seguente:

1. Aprire il database nel [portale di Azure](https://portal.azure.com)
2. In e il database, fare clic sul pulsante **Impostazioni**
3. Selezionare l'opzione di **crittografia dati trasparente**![][1]
4. Selezionare l'impostazione **in**![][2]
5. Selezionare **Salva**
![][3]  

## <a name="disabling-encryption"></a>Disabilitazione della crittografia

Per disattivare TDE per SQL Data Warehouse, seguire la procedura seguente:

1. Aprire il database nel [portale di Azure](https://portal.azure.com)
2. In e il database, fare clic sul pulsante **Impostazioni**
3. Selezionare l'opzione di **crittografia dati trasparente**![][1]
4. Selezionare l'impostazione **disattivato**![][4]
5. Selezionare **Salva**
![][5]  

## <a name="encryption-dmvs"></a>Crittografia viste

La crittografia può essere confermata con le viste seguenti:

- [Sys. Databases]
- [Sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[Sys. Databases]: http://msdn.microsoft.com/library/ms178534.aspx
[Sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
