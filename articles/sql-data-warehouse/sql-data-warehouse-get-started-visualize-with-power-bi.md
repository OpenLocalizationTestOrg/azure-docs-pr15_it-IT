<properties
   pageTitle="Visualizzare i dati di SQL Data Warehouse con Power BI Microsoft Azure"
   description="Visualizzare i dati di SQL Data Warehouse con Power BI"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor="" />

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="lodipalm;barbkess;sonyama" />

# <a name="visualize-data-with-power-bi"></a>Visualizzare i dati con Power BI

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Apprendimento Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md) 

In questa esercitazione viene illustrato come usare Power BI per connettersi a SQL Data Warehouse e creare alcune visualizzazioni di base.

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## <a name="prerequisites"></a>Prerequisiti

Per esaminare in questa esercitazione, è necessario:

- SQL Data Warehouse precaricato con il database AdventureWorksDW. Per eseguire il provisioning questa operazione, vedere [creare un Data Warehouse SQL][] e scegliere per caricare i dati di esempio. Se è già presente un data warehouse ma non dispone di dati di esempio, è possibile [caricare i dati di esempio manualmente][].


## <a name="1-connect-to-your-database"></a>1. connettersi al database

Per aprire Power BI e connettersi al database di AdventureWorksDW:

1. Accedere al [portale di Azure][].
2. Fare clic su **database SQL** e scegliere il database AdventureWorks SQL Data Warehouse.

    ![Individuare il database][1]

3. Fare clic sul pulsante 'Apri ripiegato'.

    ![Pulsante di Power BI][2]

4. Viene visualizzato la pagina di connessione SQL Data Warehouse visualizzando l'indirizzo web del database. Fare clic su Avanti.

    ![Connessione di Power BI][3]

6. Immettere il server di SQL Azure nome utente e password e verranno completamente connessa al database SQL Data Warehouse.

    ![Accedi Power BI][4]

7. Una volta effettuata l'iscrizione di Power BI, scegliere il set di dati AdventureWorksDW in e il a sinistra. Verrà aperto il database.

    ![Apri AdventureWorksDW di Power BI][5]



## <a name="2-create-a-report"></a>2. creare un report

A questo punto si è pronti a usare Power BI per analizzare i dati di esempio AdventureWorksDW. Per eseguire l'analisi, AdventureWorksDW dispone di una visualizzazione denominata AggregateSales. Questa visualizzazione include alcuni metriche chiave per analizzare le vendite della società.

1. Per creare una mappa di importo delle vendite in base al codice postale, nel riquadro destro campi, fare clic sulla visualizzazione AggregateSales per espanderlo. Fare clic su colonne PostalCode e SalesAmount per selezionarli.

    ![Power BI selezionare AggregateSales][6]

    Power BI riconosce automaticamente questo dati geografici e inserirlo in una mappa dell'utente.

    ![Mappa di Power BI][7]

2. In questo passaggio consente di creare un grafico a barre che mostra l'importo delle vendite per reddito cliente. Per creare questo passare alla visualizzazione AggregateSales espansa. Fare clic sul campo SalesAmount. Trascinare il campo reddito cliente a sinistra e rilasciarlo nell'asse.

    ![Seleziona asse di Power BI][8]

    Grafico a barre vengono spostate a sinistra.

    ![Barra Power BI][9]

3. In questo passaggio consente di creare un grafico a linee che mostra l'importo delle vendite per data dell'ordine. Per creare questo passare alla visualizzazione AggregateSales espansa. Fare clic su SalesAmount e OrderDate. Le visualizzazioni colonna selezionare l'icona di grafico a linee. Questa è la prima icona nella seconda riga in visualizzazioni.

    ![Grafico a linee selezionare Power BI][10]

    A questo punto si dispone di un rapporto che mostra tre diverse visualizzazioni dei dati.

    ![Riga di Power BI][11]

È possibile salvare lo stato di avanzamento in qualsiasi momento facendo clic su **File** e selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi
Ora che è stato assegnato alcuni istanti per riscaldamento con i dati di esempio, vedere come [sviluppare][], [caricare][]o [eseguire la migrazione][]. O da un'occhiata il [sito Web di Power BI][].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[eseguire la migrazione]: sql-data-warehouse-overview-migrate.md
[sviluppare]: sql-data-warehouse-overview-develop.md
[caricare]: sql-data-warehouse-overview-load.md
[caricare i dati di esempio manualmente]: sql-data-warehouse-load-sample-databases.md
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Creare un Data Warehouse SQL]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Portale di Azure]: https://portal.azure.com/
[Sito Web di Power BI]: http://www.powerbi.com/
