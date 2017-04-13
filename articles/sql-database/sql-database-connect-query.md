<properties
    pageTitle="Connettersi al Database SQL con una query c# | Microsoft Azure"
    description="Scrivere un programma in c# per query oltre a connettersi al database SQL. Informazioni sugli indirizzi IP, le stringhe di connessione, accesso sicuro e gratuito Visual Studio."
    services="sql-database"
    keywords="c# una query di database, c# query, connettersi al database SQL C#"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="stevestein"/>



# <a name="connect-to-a-sql-database-with-visual-studio"></a>Connettersi a un Database SQL con Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SQL SERVER MANAGEMENT STUDIO](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Informazioni su come connettersi a un database di SQL Azure in Visual Studio. 

## <a name="prerequisites"></a>Prerequisiti


Per connettersi a un database SQL utilizzando Visual Studio, è necessario quanto segue: 


- Un database SQL a cui connettersi. In questo articolo viene utilizzato il database di esempio **AdventureWorks** . Per ottenere il database di esempio AdventureWorks, vedere [creare il database di esempio](sql-database-get-started.md).


- Visual Studio 2013 aggiornamento 4 (o versioni successiva). Microsoft offre ora della Community di Visual Studio per *gratuito*.
 - [Community di Visual Studio, download](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Altre opzioni per libero Visual Studio](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)




## <a name="open-visual-studio-from-the-azure-portal"></a>Aprire Visual Studio dal portale di Azure


1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Fare clic su **Altri servizi** > **database SQL**
3. Aprire e il database **AdventureWorks** individuazione e facendo clic su database *AdventureWorks* .

6. Fare clic sul pulsante **Strumenti** nella parte superiore e il database:

    ![Nuova query. Connessione al Database SQL server: SQL Server Management Studio](./media/sql-database-connect-query/tools.png)

7. Fare clic su **Apri in Visual Studio** (se è necessario Visual Studio, fare clic sul collegamento download):

    ![Nuova query. Connessione al Database SQL server: SQL Server Management Studio](./media/sql-database-connect-query/open-in-vs.png)


8. Visual Studio viene aperto con la finestra di **connettersi al Server** già configurata per la connessione al server e database selezionato nel portale.  (Fare clic su **Opzioni** per verificare che sia impostata la connessione al database corretto). Digitare la password di amministratore del server e fare clic su **Connetti**.


    ![Nuova query. Connessione al Database SQL server: SQL Server Management Studio](./media/sql-database-connect-query/connect.png)


8. Se non si dispone di una regola del firewall impostato per l'indirizzo IP del computer, viene visualizzato un messaggio *non riesce a connettersi* qui. Per creare una regola del firewall, vedere [configurare una regola firewall a livello di server di Database SQL Azure](sql-database-configure-firewall-settings.md).


9. Dopo aver collegato correttamente, verrà visualizzata la finestra **Esplora oggetti di SQL Server** con una connessione al database.

    ![Nuova query. Connessione al Database SQL server: SQL Server Management Studio](./media/sql-database-connect-query/sql-server-object-explorer.png)


## <a name="run-a-sample-query"></a>Eseguire una query di esempio

Ora che si stiamo connessi al database, la procedura seguente viene illustrato come eseguire una query semplice:

2. Il pulsante destro del database e quindi selezionare **Nuova Query**.

    ![Nuova query. Connessione al Database SQL server: SQL Server Management Studio](./media/sql-database-connect-query/new-query.png)

3. Nella finestra della query, copiare e incollare il codice riportato di seguito.

        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;

4. Fare clic sul pulsante **Esegui** per eseguire la query:

    ![Success. Connessione al Database SQL server: SVisual Studio](./media/sql-database-connect-query/run-query.png)

## <a name="next-steps"></a>Passaggi successivi

- Apertura di database di SQL in Visual Studio utilizza SQL Server Data Tools. Per ulteriori informazioni, vedere [SQL Server Data Tools](https://msdn.microsoft.com/library/hh272686.aspx).
- Per connettersi a un database SQL di codice, vedere [connettersi al Database SQL utilizzando .NET (c#)](sql-database-develop-dotnet-simple.md).



