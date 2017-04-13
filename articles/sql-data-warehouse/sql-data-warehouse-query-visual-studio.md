<properties
   pageTitle="Query SQL Azure Data Warehouse (Visual Studio) | Microsoft Azure"
   description="Query SQL Data Warehouse con Visual Studio."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="sonyama;barbkess"/>

# <a name="query-azure-sql-data-warehouse-visual-studio"></a>Query SQL Azure Data Warehouse (Visual Studio)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Apprendimento Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Utilizzare Visual Studio per query Warehouse di dati di SQL Azure in pochi minuti. Questo metodo utilizza l'estensione di SQL Server Data Tools (SSDT) in Visual Studio. 

## <a name="prerequisites"></a>Prerequisiti

Per utilizzare questa esercitazione, è necessario:

+ Un SQL data warehouse esistente. Per creare uno, vedere [creare un Data Warehouse SQL][].
+ SSDT per Visual Studio. Se si dispone di Visual Studio, probabile che sia già questo. Per istruzioni relative all'installazione e opzioni, vedere [l'installazione di Visual Studio e SSDT][].
+ Il nome completo del server SQL. Per individuare questa operazione, vedere [connettersi a SQL Data Warehouse][].

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. connettersi a un Data Warehouse SQL

1. Aprire Visual Studio 2013 o 2015.
2. Aprire Esplora oggetti SQL Server. A tale scopo, selezionare **Visualizza** > **Esplora oggetti di SQL Server**.

    ![Esplora oggetti SQL Server][1]

3. Fare clic sull'icona **Aggiungi SQL Server** .

    ![Aggiunta di SQL Server][2]

4. Compilare i campi in connessione alla finestra Server.

    ![Connessione al Server][3]

    - **Nome del server**. Immettere il **nome del server** identificati in precedenza.
    - **L'autenticazione**. Selezionare **l'autenticazione di Active Directory**o **l'autenticazione di SQL Server** .
    - **Nome utente** e **Password**. Immettere nome utente e password se è stata selezionata l'opzione autenticazione di SQL Server sopra.
    - Fare clic su **Connetti**.

5. Per esplorare, espandere il server di SQL Azure. È possibile visualizzare i database associati al server. Espandere AdventureWorksDW per visualizzare le tabelle nel database di esempio.

    ![Esplorare AdventureWorksDW][4]

## <a name="2-run-a-sample-query"></a>2. eseguire una query di esempio

Ora che è stata stabilita una connessione al database, è possibile scrivere una query.

1. Pulsante destro del mouse del database in Esplora oggetti di SQL Server.

2. Selezionare **Nuova Query**. Verrà aperta una nuova finestra query.

    ![Nuova query][5]

3. Copiare questa query TSQL nella finestra di query:

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. Eseguire la query. A tale scopo, fare clic sulla freccia verde oppure utilizzare il collegamento seguente: `CTRL` + `SHIFT` + `E`.

    ![Eseguire query][6]

5. Esaminare i risultati della query. In questo esempio, la tabella FactInternetSales include 60398 righe.

    ![Risultati della query][7]

## <a name="next-steps"></a>Passaggi successivi

Ora che è possibile connettersi e query, provare [la visualizzazione dati con ottenere informazioni][].

Per configurare l'ambiente per l'autenticazione di Azure Active Directory, vedere [autentica SQL Data warehouse][].

<!--Arcticles-->
[Connettersi a SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Creare un Data Warehouse SQL]: sql-data-warehouse-get-started-provision.md
[L'installazione di SSDT e Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Eseguire l'autenticazione nell'archivio di dati SQL]: sql-data-warehouse-authentication.md
[la visualizzazione dati con ottenere informazioni]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png
