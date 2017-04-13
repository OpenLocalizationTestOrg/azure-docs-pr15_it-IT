<properties
   pageTitle="L'autenticazione di SQL Azure Data warehouse | Microsoft Azure"
   description="Azure Active Directory (AAD) e SQL Server l'autenticazione nell'archivio di dati di SQL Azure."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="byham"
   manager="barbkess"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/24/2016"
   ms.author="rickbyh;barbkess;sonyama"/>

# <a name="authentication-to-azure-sql-data-warehouse"></a>Autenticazione di SQL Azure Data warehouse

> [AZURE.SELECTOR]
- [Cenni preliminari sulla sicurezza](sql-data-warehouse-overview-manage-security.md)
- [Autenticazione](sql-data-warehouse-authentication.md)
- [Crittografia (Portal)](sql-data-warehouse-encryption-tde.md)
- [Crittografia (Transact-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Per connettersi a SQL Data Warehouse, è necessario passare le credenziali di sicurezza per l'autenticazione. Dopo una connessione, alcune impostazioni di connessione sono configurati come parte di stabilire la sessione di query.  

Per ulteriori informazioni sulla sicurezza e su come attivare le connessioni per il proprio data warehouse, vedere [sicura un database SQL Data warehouse][].

## <a name="sql-authentication"></a>Autenticazione di SQL Server
Per connettersi a SQL Data Warehouse, è necessario fornire le informazioni seguenti:

- NomeServer completo
- Specificare l'autenticazione di SQL
- Nome utente
- Password
- Database predefinito (facoltativo)

Per impostazione predefinita effettuata la connessione al database *master* e non il database utente. Per connettersi al database di utente, è possibile scegliere di eseguire una delle seguenti operazioni:

- Specificare il database predefinito durante la registrazione del server con Esplora risorse oggetto SQL Server in SSDT, SQL Server Management Studio, o nella stringa di connessione dell'applicazione. Ad esempio, includere il parametro InitialCatalog per una connessione ODBC.
- Selezionare il database utente prima di creare una sessione in SSDT.

> [AZURE.NOTE] L'istruzione Transact-SQL **Utilizzo MyDatabase;** non è supportata per la modifica del database per una connessione. Per indicazioni per la connessione a SQL Data Warehouse con SSDT, vedere l'articolo [Query con Visual Studio][] .

## <a name="azure-active-directory-aad-authentication"></a>Autenticazione di Azure Active Directory (AAD)

[Azure Active Directory] [ What is Azure Active Directory] autenticazione è un meccanismo di connessione a Microsoft Azure SQL Data Warehouse tramite identità di Azure Active Directory (Azure Active Directory). Con l'autenticazione di Azure Active Directory, è possibile gestire centralmente le identità degli utenti del database e altri servizi Microsoft in una posizione centrale. Gestione centrale di ID offre una posizione centralizzata per gestire gli utenti SQL Data Warehouse e semplifica la gestione delle autorizzazioni. 

### <a name="benefits"></a>Vantaggi

Vantaggi di Azure Active Directory:

- Fornisce un'alternativa per l'autenticazione di SQL Server.
- Consente di interrompere la proliferazione di identità utente tra server di database.
- Consente la rotazione di password in un'unica posizione
- Gestire le autorizzazioni di database mediante gruppi esterni (AAD).
- Elimina le password archiviazione abilitando l'autenticazione di Windows e altre forme di autenticazione supportati da Azure Active Directory.
- Utenti del database di contenuto viene utilizzato per eseguire l'autenticazione delle identità al livello del database.
- Supporta l'autenticazione basata su token per applicazioni che si connettono a SQL Data Warehouse.
- Supporta l'autenticazione a più fattori tramite autenticazione universale di Active Directory per SQL Server Management Studio. Per una descrizione di autenticazione a più fattori, vedere [supporto di SQL Server Management Studio per Azure Active Directory MFA con Database SQL e SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

> [AZURE.NOTE] Azure Active Directory è ancora relativamente nuovo e presenta alcune limitazioni. Per assicurare la soluzione ideale per l'ambiente di Azure Active Directory, vedere [limiti e le funzionalità di Azure Active Directory][], in particolare considerazioni.

### <a name="configuration-steps"></a>Passaggi di configurazione

Seguire questa procedura per configurare l'autenticazione di Azure Active Directory.

1. Creare e inserire un Azure Active Directory
2. Facoltativo: Associare o modifica di active directory non è attualmente associata all'abbonamento Azure
3. Creare un amministratore di Azure Active Directory per Data Warehouse di SQL Azure.
4. Configurare i computer client
5. Creare gli utenti del database contenuti nel database mappato alle identità di Azure Active Directory
6. Connettersi a un data warehouse utilizzando identità di Azure Active Directory

Attualmente gli utenti di Azure Active Directory non vengono visualizzati in Esplora oggetti SSDT. In alternativa, visualizzare gli utenti in [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
  
### <a name="find-the-details"></a>Trovare i dettagli
- Completare la procedura dettagliata. Procedura dettagliata per configurare l'autenticazione di Azure Active Directory è quasi identica per il Database di SQL Azure e Azure SQL Data Warehouse. Seguire i passaggi dettagliati nell'argomento della [connessione al Database SQL o dati Warehouse da con Azure Active Directory autenticazione di SQL](../sql-database/sql-database-aad-authentication.md).
- Creare database personalizzati ruoli e aggiungere utenti ai ruoli. Quindi concedere le autorizzazioni granulare ai ruoli. Per ulteriori informazioni, vedere [Guida introduttiva di autorizzazioni di motore di Database](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Passaggi successivi

Per avviare la ricerca di data warehouse con Visual Studio e altre applicazioni, vedere [Query con Visual Studio][].

<!-- Article references -->
[Proteggere un database SQL Data warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query con Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]: ../active-directory/active-directory-whatis.md
[Limitazioni e le funzionalità di azure Active Directory]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
