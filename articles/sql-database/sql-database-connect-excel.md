<properties
    pageTitle="Connettere Excel al Database SQL | Microsoft Azure"
    description="Informazioni su come connettersi database di SQL Azure nel cloud di Microsoft Excel. Importare dati in Excel per reporting ed esplorazione dei dati."
    services="sql-database"
    keywords="connettersi a sql di excel, importazione di dati in excel"
    documentationCenter=""
    authors="joseidz"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/05/2016"
    ms.author="joseidz"/>


# <a name="sql-database-tutorial-connect-excel-to-an-azure-sql-database-and-create-a-report"></a>Esercitazione di Database SQL: connettere Excel a un database SQL Azure e creare un report

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SQL SERVER MANAGEMENT STUDIO](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Informazioni su come connettere Excel a un database SQL nel cloud in modo che è possibile importare i dati e creare tabelle e grafici in base ai valori del database. In questa esercitazione che si desidera impostare la connessione tra Excel e una tabella di database, salvare il file che archivia dati e le informazioni di connessione per Excel e quindi creare un grafico pivot i valori del database.

Prima di iniziare, è necessario un database SQL di Azure. Se non si dispone di uno, vedere [creare il primo database di SQL](sql-database-get-started.md) per ottenere un database con dati di esempio alto e in esecuzione in pochi minuti. In questo articolo verrà importare dati di esempio in Excel da tale articolo, ma è possibile eseguire operazioni simili sui propri dati.

È necessario anche una copia di Excel. In questo articolo viene utilizzato [Microsoft Excel 2016](https://products.office.com/en-US/).

## <a name="connect-excel-to-a-sql-database-and-create-an-odc-file"></a>Connettere Excel a un database SQL e creare un file odc

1.  Per connettersi al database SQL di Excel, aprire Excel e creare una nuova cartella di lavoro o aprire una cartella di lavoro di Excel esistente.

2.  Nella barra dei menu nella parte superiore della pagina fare clic su **dati**, fare clic su **Da altre origini**e quindi fare clic su **Da SQL Server**.

    ![Seleziona origine dati: connettere Excel al database SQL.](./media/sql-database-connect-excel/excel_data_source.png)

    Verrà visualizzata la connessione guidata dati.

3.  Nella finestra di dialogo **connessione al Server di Database** digitare il Database di SQL **nome del Server** che si desidera connettersi nel modulo <*nomeserver*>**. database.windows.net**. Ad esempio **adworkserver.database.windows.net**.

4.  In **credenziali di accesso**, fare clic su **utilizza il seguente nome utente e la Password**, digitare il **Nome utente** e **Password** impostare per il server di Database SQL quando è stata creata e quindi fare clic su **Avanti**.

    ![Digitare le credenziali di accesso e del nome del server](./media/sql-database-connect-excel/connect-to-server.png)

    > [AZURE.TIP] A seconda dell'ambiente di rete, potrebbe non essere possibile connettersi o si potrebbe perdere la connessione se il server di Database SQL non consentire il traffico verso l'indirizzo IP del client. Accedere al [portale di Azure](https://portal.azure.com/), fare clic su SQL Server, fare clic sul server, fare clic su firewall in impostazioni e aggiungere l'indirizzo IP del client. Per informazioni dettagliate, vedere [come configurare le impostazioni del firewall](sql-database-configure-firewall-settings.md) .

5. Nella finestra di dialogo **Seleziona Database e tabella** selezionare il database che si desidera utilizzare nell'elenco e quindi fare clic sulle tabelle o viste da utilizzare (abbiamo scelto **vGetAllCategories**), quindi scegliere **Avanti**.

    ![Selezionare un database e tabella.](./media/sql-database-connect-excel/select-database-and-table.png)

    Verrà visualizzata la finestra di dialogo **Salva File di connessione dati e di fine** , in cui è fornire informazioni sul file Office database connection (*. odc) utilizzato da Excel. È possibile lasciare le impostazioni predefinite o personalizzare le opzioni desiderate.

6. È possibile lasciare le impostazioni predefinite, ma prendere nota del **Nome del File** in particolare. Una **Descrizione**, un **Nome descrittivo**e **Parole chiave di ricerca** che consentono di, tenendo presente che altri utenti di cosa si connette a e trovare la connessione. Se si desidera che le informazioni di connessione archiviate in file odc in modo che consente di aggiornare quando la connessione e quindi fare clic su **Fine**, fare clic su **sempre tenta di utilizzare questo file per aggiornare i dati** .

    ![Salvare un file odc](./media/sql-database-connect-excel/save-odc-file.png)

    Verrà visualizzata la finestra di dialogo **Importa dati** .

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importare i dati in Excel e creare un grafico pivot
Ora che è stata stabilita la connessione e il file creato con dati e informazioni sulla connessione, si sta leggendo per importare i dati.

1. Nella finestra di dialogo **Importa dati** fare clic sull'opzione desiderata per presentare i dati nel foglio di lavoro e quindi fare clic su **OK**. Si è scelto di **grafico pivot**. È anche possibile scegliere di creare un **nuovo foglio di lavoro** o Aggiungi **questi dati per un modello di dati**. Per ulteriori informazioni sui modelli di dati, vedere [creare un modello di dati in Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). Fare clic su **proprietà** per esplorare le informazioni relative al file odc creato nel passaggio precedente e scegliere le opzioni per l'aggiornamento dei dati.

    ![Scelta del formato per i dati in Excel](./media/sql-database-connect-excel/import-data.png)

    Il foglio di lavoro è ora disponibile una tabella pivot vuota e un grafico.

8. In **Campi tabella pivot**, selezionare tutte le caselle per i campi che si desidera visualizzare.

    ![Configurare i report del database.](./media/sql-database-connect-excel/power-pivot-results.png)

> [AZURE.TIP]Se si desidera connettersi al database altre cartelle di lavoro di Excel e fogli di lavoro, fare clic su **dati**, fare clic su **connessioni**, fare clic su **Aggiungi**, scegliere la connessione creata dall'elenco e quindi fare clic su **Apri**.
> ![Aprire una connessione da un'altra cartella di lavoro](./media/sql-database-connect-excel/open-from-another-workbook.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [connettersi al Database SQL di SQL Server Management Studio](sql-database-connect-query-ssms.md) per la ricerca avanzata e l'analisi.
- Informazioni sui vantaggi del [pool flessibile](sql-database-elastic-pool.md).
- Informazioni su come [creare un'applicazione web che si connette al Database SQL di back-end](../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
