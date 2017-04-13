<properties
    pageTitle="Come eseguire attività amministrative, ad esempio, reimpostare password dell'amministratore | Microsoft Azure"
    description="Viene descritto come eseguire attività amministrative più comuni nel Database di SQL. Ad esempio, reimpostare la password di amministratore, concessione e la rimozione dell'accesso."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    keywords="reimpostare la password di amministratore"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="how-to-perform-common-administrative-tasks-such-as-resetting-admin-password-in-azure-sql-database"></a>Come eseguire attività amministrative comuni come la reimpostazione della password di amministratore nel Database di SQL Azure
Utilizzare questo argomento per azioni rapide per concedere e rimuovere l'accesso a un database SQL Azure. Per ulteriori informazioni, vedere:

- [Gestione di database e gli account di accesso di Database SQL Azure](sql-database-manage-logins.md)
- [La protezione del database SQL](sql-database-security.md)
- [Centro protezione per il motore di Database SQL Server e Database SQL Azure](https://msdn.microsoft.com/library/bb510589)


[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-reset-admin-password-for-a-logical-server"></a>Per reimpostare la password di amministratore per un server logico

- Nel [Portale di Azure](https://portal.azure.com) fare clic su **SQL Server**, selezionare il server dall'elenco e quindi fare clic su **Reimposta Password**.

## <a name="to-help-make-sure-only-authorized-ip-addresses-are-allowed-to-access-the-server"></a>Per assicurarsi che solo IP autorizzati gli indirizzi sono consentiti per accedere al server
- Vedere [How to: configurare le impostazioni del firewall nel Database di SQL](sql-database-configure-firewall-settings.md).

## <a name="to-create-contained-database-users-in-the-user-database"></a>Per creare gli utenti del database contenuti nel database utente
- Utilizzare l'istruzione [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) e vedere [Contenuto gli utenti del Database - effettua il Database portatili](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="to-authenticate-contained-database-users-by-using-your-azure-active-directory"></a>Per eseguire l'autenticazione gli utenti del database contenuti utilizzando di Azure Active Directory
- Vedere [la connessione al Database SQL mediante l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md).

## <a name="to-create-additional-logins-for-high-privileged-users-in-the-virtual-master-database"></a>Per creare altri account di accesso per gli utenti di privilegi elevati nel database master virtuale
- Utilizzare l'istruzione [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) e vedere la sezione gestione di account di accesso di [gestione dei database e gli account di accesso di Database SQL Azure](sql-database-manage-logins.md) per altri dettagli.
