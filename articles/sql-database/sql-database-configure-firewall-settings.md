<properties
    pageTitle="Configurare una regola di Database SQL server a livello firewall | Microsoft Azure"
    description="Informazioni su come configurare il firewall per gli indirizzi IP che accedono a server SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="BYHAM"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article" 
    ms.date="08/30/2016"
    ms.author="rickbyh;carlrab"/>


# <a name="configure-an-azure-sql-database-server-level-firewall-rule-using-the-azure-portal"></a>Configurare una regola firewall a livello di server di Database SQL Azure tramite il portale di Azure


> [AZURE.SELECTOR]
- [Panoramica](sql-database-firewall-configure.md)
- [Portale di Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)

Azure SQL server utilizza le regole del firewall per consentire le connessioni al server e database. È possibile definire impostazioni del livello di server e database firewall per lo schema o in un database utente in SQL Azure logico server server in modo selettivo consentire l'accesso al database. In questo argomento vengono illustrate le regole del firewall a livello di server.

> [AZURE.IMPORTANT] Per consentire alle applicazioni comuni di connettersi al server SQL Azure, è necessario attivare connessioni Azure. Per comprendere il funzionamento delle regole firewall, vedere [come configurare un firewall di server SQL Azure \- Panoramica](sql-database-firewall-configure.md). Se invece si creano connessioni all'interno del limite di Azure cloud, è necessario aprire alcune porte TCP aggiuntive. Per ulteriori informazioni, vedere il **V12 di Database SQL: esterne vs all'interno di** sezione delle [porte 1433 per ADO.NET 4.5 e V12 di Database SQL](sql-database-develop-direct-route-ports-adonet-v12.md)

**Recommendation:** Usare le regole firewall a livello di server per gli amministratori e dopo avere molti database con gli stessi requisiti di accesso e non si desidera dedicare troppo tempo a configurare singolarmente ogni database. Si consiglia di utilizzare le regole firewall a livello di database ogni volta che è possibile migliorare la protezione e alla portabilità del database.

[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gestire le regole firewall a livello di server esistenti tramite il portale di Azure

Ripetere i passaggi per gestire le regole del firewall a livello di server.

- Per aggiungere computer in uso, fare clic su Aggiungi client IP.
- Per aggiungere altri indirizzi IP, digitare il nome regola, indirizzo IP iniziale e indirizzo IP finale.
- Per modificare una regola esistente, fare clic su uno dei campi nella regola e modificare.
- Per eliminare una regola esistente, posizionare il puntatore sulla regola finché non viene visualizzata la X alla fine della riga. Fare clic sulla X per rimuovere la regola.

Fare clic su **Salva** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

Per istruzioni sulla articolo su come utilizzare Transact-SQL per creare regole del firewall a livello di server e di database, vedere [regole del firewall di livello di server e database di configurare il Database SQL Azure mediante T-SQL](sql-database-configure-firewall-settings-tsql.md). 

Per come articoli sulla creazione di regole del firewall a livello di server con altri metodi, vedere: 

- [Configurare le regole firewall a livello di server Database SQL Azure con PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurare le regole firewall a livello di server di Database SQL Azure tramite l'API REST](sql-database-configure-firewall-settings-rest.md)

Per un'esercitazione sulla creazione di un database, vedere [creare un database SQL in minuti tramite il portale di Azure](sql-database-get-started.md).
Per informazioni su come connettersi a un database SQL Azure da Apri origine o applicazioni di terze parti, vedere [Client introduttiva esempi di codice al Database SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Per capire come passare al database, vedere [gestire la sicurezza di access e login database](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Risorse aggiuntive

- [La protezione del database](sql-database-security.md)
- [Centro protezione per il motore di Database SQL Server e Database SQL Azure](https://msdn.microsoft.com/library/bb510589)


<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 
