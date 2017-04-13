<properties
   pageTitle="Linee guida e le limitazioni generale di Database SQL Azure"
   description="Questa pagina illustra alcune limitazioni generali per il Database di SQL Azure, nonché le aree di interoperabilità e supporto tecnico."
   services="sql-database"
   documentationCenter="na"
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/06/2016"
   ms.author="carlrab" />

# <a name="azure-sql-database-general-limitations-and-guidelines"></a>Linee guida e le limitazioni generale di Database SQL Azure

In questo argomento fornisce limitazioni generali e linee guida per il Database di SQL Azure. Per individuare le quote, la gestione delle risorse e supporto, vedere le [risorse aggiuntive](#additional-guidelines) alla fine di questo argomento.

## <a name="connectivity-and-authentication"></a>Autenticazione e connettività

  - L'autenticazione di Windows non è supportata. Vedere [gestione di database e gli account di accesso di Database SQL Azure](sql-database-manage-logins.md). Tuttavia, autenticazione di Azure Active Directory è supportata con alcune limitazioni. Vedere [la connessione al Database SQL di autenticazione di Azure Active Directory](sql-database-aad-authentication.md).

  - Database SQL di Microsoft Azure supporta dati tabulari flusso (TD) protocollo client 7.3 o versione successiva.

  - Solo le connessioni TCP/IP consentite.

  - Nel browser di SQL Server 2008 SQL Server non è supportato poiché Database SQL di Microsoft Azure non dispone di porte dinamiche solo porte 1433.

## <a name="sql-server-agentjobs"></a>Agente/processi SQL Server

Database SQL di Microsoft Azure non supporta l'agente di SQL Server, ma è possibile utilizzare processi flessibile per eseguire i processi tra uno a molti database. Per ulteriori informazioni sui processi flessibile, vedere [processi flessibile](sql-database-elastic-jobs-overview.md).

## <a name="sql-server-collation-support"></a>Supporto del confronto di SQL Server

Confronto di database predefinito utilizzato dal Database SQL di Microsoft Azure è **SQL_LATIN1_GENERAL_CP1_CI_AS**, in cui **LATIN1_GENERAL** è inglese (Stati Uniti), **CP1** è tabella codici 1252, **CI** è distinzione tra maiuscole e e **come** è accentati. Non è possibile modificare le regole di confronto per i database V12. Per ulteriori informazioni su come impostare le regole di confronto, vedere [FASCICOLA (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="naming-requirements"></a>Requisiti per la denominazione

Alcuni nomi utente non consentiti per motivi di sicurezza. Non è possibile utilizzare i nomi seguenti:

 - **amministratore**
 - **amministratore**
 - **Guest**
 - **radice**
 - **amministratore di sistema**

I nomi per tutti i nuovi oggetti devono rispettare le regole di SQL Server per gli identificatori. Per ulteriori informazioni, vedere [identificatori](https://msdn.microsoft.com/library/ms175874.aspx).

Inoltre, non possono contenere nomi utente e di accesso di \ carattere (l'autenticazione di Windows non è supportato).

## <a name="additional-guidelines"></a>Indicazioni aggiuntive

- Oltre alle limitazioni generali descritte in questo articolo, Database SQL sono le quote di risorse specifiche e limiti in base al **livello di servizio**. Per una panoramica dei livelli di servizio, vedere [livelli di servizio di Database SQL](sql-database-service-tiers.md).

- Per altri limiti del Database SQL, vedere [Limiti delle risorse del Database di SQL Azure](sql-database-resource-limits.md).

- Per la protezione linee guida correlate, vedere [linee guida di protezione del Database di SQL Azure e limitazioni](sql-database-security-guidelines.md).

- Un'altra area correlata circonda la compatibilità con il Database di SQL Azure con le versioni locale di SQL Server, ad esempio SQL Server 2014 e SQL Server 2016. L'ultima versione di V12 di Database SQL Azure apportate numerosi miglioramenti in quest ' area. Per ulteriori informazioni, vedere [Novità di SQL Database V12](sql-database-v12-whats-new.md).

- Per informazioni sulla disponibilità del driver e il supporto per Database SQL, vedere [Le raccolte di connessioni per Database SQL e SQL Server](sql-database-libraries.md).
