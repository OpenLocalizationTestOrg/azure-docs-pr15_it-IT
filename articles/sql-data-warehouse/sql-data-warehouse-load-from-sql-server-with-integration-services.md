<properties
   pageTitle="Caricare i dati da SQL Server in Azure SQL dati Warehouse (SSIS) | Microsoft Azure"
   description="Viene illustrato come creare un pacchetto di SQL Server Integration Services (SSIS) per spostare dati da una vasta gamma di origini dati di SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;sonyama;barbkess"/>

# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>Caricare i dati da SQL Server in Azure SQL dati Warehouse (SSIS)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)


Creare un pacchetto di SQL Server Integration Services (SSIS) per caricare i dati da SQL Server nell'archivio di dati di SQL Azure. È facoltativamente possibile ristrutturazione, trasformare e ripulire i dati quando passano attraverso il flusso di dati SSIS.

In questa esercitazione, sarà necessario:

- Creare un nuovo progetto di Integration Services in Visual Studio.
- Connettersi a origini dati, inclusi SQL Server (come origine) e SQL Data Warehouse (come destinazione).
- Progettare un pacchetto SSIS che carica i dati dal sito di origine nella destinazione.
- Eseguire il pacchetto SSIS per caricare i dati.

In questa esercitazione utilizza SQL Server come origine dati. SQL Server potrebbe essere in esecuzione in locale o in una macchina virtuale Azure.

## <a name="basic-concepts"></a>Concetti di base

Il pacchetto è l'unità di lavoro in SSIS. Pacchetti correlati sono raggruppati in progetti. Creare progetti e i pacchetti di progettazione in Visual Studio con SQL Server Data Tools. Il processo di progettazione è un processo visivo in cui si trascinare e rilasciare componenti dalla casella degli strumenti all'area di progettazione, connetterle e impostarne le proprietà. Dopo aver completato il pacchetto, è possibile distribuirlo facoltativamente a SQL Server di gestione completa, monitoraggio e sicurezza.

## <a name="options-for-loading-data-with-ssis"></a>Opzioni per il caricamento dei dati di SSIS

SQL Server Integration Services (SSIS) è un set di strumenti flessibile che fornisce una vasta gamma di opzioni per la connessione e il caricamento dei dati nell'archivio di dati di SQL.

1. Utilizzare una destinazione NET ADO per connettersi a SQL Data Warehouse. In questa esercitazione viene utilizzata una destinazione NET ADO poiché presenta le opzioni di configurazione minor numero.
2. Utilizzare una destinazione OLE DB per connettersi a SQL Data Warehouse. Questa opzione può offrire prestazioni leggermente superiori rispetto alla destinazione di rete ADO.
3. Usare l'attività di caricare Blob Azure per organizzare i dati in archiviazione Blob Azure. Utilizzare l'attività SSIS Esegui SQL per uno script Polybase che carica i dati in SQL Data Warehouse di avvio. Questa opzione fornisce le massime prestazioni delle tre opzioni elencate di seguito. Per ottenere l'attività caricare Blob Azure, scaricare il [Microsoft SQL Server 2016 Integration Services Feature Pack per Azure][]. Per ulteriori informazioni su Polybase, vedere [Guida PolyBase][].

## <a name="before-you-start"></a>Prima di iniziare

Per esaminare in questa esercitazione, è necessario:

1. **SQL Server Integration Services (SSIS)**. SSIS è un componente di SQL Server e richiede una versione di valutazione o una versione con licenza di SQL Server. Per ottenere una versione di valutazione di SQL Server 2016 Preview, vedere [Le valutazioni di SQL Server][].
2. **Visual Studio**. Per ottenere l'edizione di Community gratuito di Visual Studio 2015, vedere [Visual Studio Community][].
3. **SQL Server Data Tools per Visual Studio (SSDT)**. Per ottenere SQL Server Data Tools per Visual Studio 2015, vedere [SQL Download di Server Data Tools (SSDT)][].
4. **Dati di esempio**. In questa esercitazione utilizza dati di esempio archiviati in SQL Server database di esempio AdventureWorks come dati di origine nel SQL Data Warehouse deve per essere caricata. Per ottenere il database di esempio AdventureWorks, vedere [Database di esempio AdventureWorks 2014][].
5. **Autorizzazioni e database A SQL Data Warehouse**. In questa esercitazione si connette a un'istanza di SQL Data Warehouse e carica i dati al suo interno. È necessario disporre delle autorizzazioni per creare una tabella e caricare i dati.
6. **Una regola del firewall**. È necessario creare una regola in SQL Data Warehouse con l'indirizzo IP del computer locale prima che è possibile caricare dati nell'archivio di dati SQL.

## <a name="step-1-create-a-new-integration-services-project"></a>Passaggio 1: Creare un nuovo progetto di Integration Services

1. Avviare Visual Studio 2015.
2. Nel menu **File** , selezionare nuovo **| Progetto**.
3. Individuare il **installato | Modelli | Funzionalità di Business Intelligence | Servizi di integrazione** tipi di progetto.
4. Selezionare **progetto di Integration Services**. Fornire i valori per **nome** e un **percorso**e quindi selezionare **OK**.

Verrà aperta e crea un nuovo progetto di Integration Services (SSIS). Quindi Visual Studio verrà visualizzata la finestra di progettazione per il pacchetto SSIS nuovo singolo (package. dtsx) nel progetto. Viene visualizzato aree dello schermo seguenti:

- A sinistra **della casella degli strumenti** dei componenti SSIS.
- Al centro, area di progettazione, con più schede. È in genere utilizzare almeno il **Flusso di controllo** e le schede **Del flusso di dati** .
- A destra, in **Esplora soluzioni** e i riquadri di **proprietà** .

    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>Passaggio 2: Creare il flusso di dati di base

1. Trascinare un'attività del flusso di dati da casella degli strumenti al centro della finestra di progettazione (nella scheda **Flusso di controllo** ).

    ![][02]

2. Fare doppio clic su attività di flusso di dati per passare alla scheda flusso di dati.
3. Dall'elenco di altre origini nella casella degli strumenti, trascinare un'origine ADO.NET nell'area di progettazione. Con la scheda origine ancora selezionata, cambiarne il nome di **origine di SQL Server** nel riquadro delle **proprietà** .
4. Nell'elenco di altre destinazioni nella casella degli strumenti, trascinare una destinazione ADO.NET nell'area di progettazione in origine ADO.NET. Con la scheda destinazione ancora selezionata, cambiarne il nome alla **destinazione DW SQL** nel riquadro delle **proprietà** .

    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>Passaggio 3: Configurare la scheda origine

1. Fare doppio clic sulla scheda origine per aprire l' **Editor origine HTML ADO.NET**.

    ![][03]

2. Nella scheda **Gestione connessione** dell' **Editor origine HTML ADO.NET**, fare clic sul pulsante **Nuovo** accanto all'elenco di **Gestione connessione ADO.NET** per aprire la finestra di dialogo **Configura gestione connessione ADO.NET** e creare impostazioni di connessione per il database di SQL Server da cui questa esercitazione carica i dati.

    ![][04]

3. Nella finestra di dialogo **Configura gestione connessione ADO.NET** , fare clic sul pulsante **Nuovo** per aprire la finestra di dialogo **Gestione connessione** e creare una nuova connessione dati.

    ![][05]

4. Nella finestra di dialogo **Gestione connessione** , eseguire le operazioni seguenti.

    1. Per **Provider**, selezionare il Provider di dati SqlClient.
    2. Per **nome Server**immettere il nome di SQL Server.
    3. Nella sezione **accesso al server** , selezionare o immettere le informazioni di autenticazione.
    4. Nella sezione **connessione al database** , selezionare il database di esempio AdventureWorks.
    5. Fare clic su **Test connessione**.
    
        ![][06]
    
    6. Nella finestra di dialogo che contiene i risultati del test di connessione, fare clic su **OK** per tornare alla finestra di dialogo **Gestione connessione** .
    7. Nella finestra di dialogo **Gestione connessione** fare clic su **OK** per tornare alla finestra di dialogo **Configura gestione connessione ADO.NET** .
 
5. Nella finestra di dialogo **Configura gestione connessione ADO.NET** , fare clic su **OK** per tornare all' **Editor origine HTML ADO.NET**.
6. Nell' **Editor origine HTML ADO.NET**, nella casella **nome della tabella o la visualizzazione** , selezionare **SalesOrderDetail** .

    ![][07]

7. Fare clic su **Anteprima** per visualizzare le prime 200 righe di dati nella tabella di origine nella finestra di dialogo **Anteprima risultati della Query** .

    ![][08]

8. Nella finestra di dialogo **Anteprima risultati della Query** fare clic su **Chiudi** per tornare all' **Editor origine HTML ADO.NET**.
9. **Editor origine HTML ADO.NET**fare clic su **OK** per completare la configurazione dell'origine dati.

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>Passaggio 4: Connettere adattatore di origine alla scheda destinazione

1. Selezionare la scheda origine nella finestra di progettazione.
2. Selezionare la freccia blu che si estende dalla scheda di origine e trascinarlo all'editor di destinazione finché non si posiziona nella posizione desiderata.

    ![][10]

    In un pacchetto SSIS tipico, utilizzare un numero di altri componenti dalla casella degli strumenti SSIS tra l'origine e destinazione per ristrutturazione, trasformare e filtrare i dati quando passano attraverso il flusso di dati SSIS. Per mantenere più semplice possibile in questo esempio, ci si sta connettendo l'origine direttamente alla destinazione.

## <a name="step-5-configure-the-destination-adapter"></a>Passaggio 5: Configurare la scheda di destinazione

1. Fare doppio clic sulla scheda destinazione per aprire l' **Editor di destinazione ADO.NET**.

    ![][11]

2. Nella scheda **Gestione connessione** dell' **Editor di destinazione ADO.NET**, fare clic sul pulsante **Nuovo** accanto all'elenco di **Gestione connessione** per aprire la finestra di dialogo **Configura gestione connessione ADO.NET** e creare impostazioni di connessione per il database Warehouse di dati di SQL Azure in cui in questa esercitazione carica i dati.
3. Nella finestra di dialogo **Configura gestione connessione ADO.NET** , fare clic sul pulsante **Nuovo** per aprire la finestra di dialogo **Gestione connessione** e creare una nuova connessione dati.
4. Nella finestra di dialogo **Gestione connessione** , eseguire le operazioni seguenti.
    1. Per **Provider**, selezionare il Provider di dati SqlClient.
    2. Per **nome Server**immettere il nome SQL Data Warehouse.
    3. Nella sezione **accesso al server** , selezionare **l'autenticazione di utilizzare SQL Server** e immettere le informazioni di autenticazione.
    4. Nella sezione **connessione al database** , selezionare un database SQL Data Warehouse esistente.
    5. Fare clic su **Test connessione**.
    6. Nella finestra di dialogo che contiene i risultati del test di connessione, fare clic su **OK** per tornare alla finestra di dialogo **Gestione connessione** .
    7. Nella finestra di dialogo **Gestione connessione** fare clic su **OK** per tornare alla finestra di dialogo **Configura gestione connessione ADO.NET** .
5. Nella finestra di dialogo **Configura gestione connessione ADO.NET** , fare clic su **OK** per tornare all' **Editor di destinazione ADO.NET**.
6. Nell' **Editor di destinazione ADO.NET**, fare clic su **Nuovo** accanto all'elenco di **utilizzare una tabella o visualizzazione** per aprire la finestra di dialogo **Crea tabella** per creare una nuova tabella di destinazione con un elenco di colonna corrispondente nella tabella di origine.

    ![][12a]

7. Nella finestra di dialogo **Crea tabella** , eseguire le operazioni seguenti.

    1. Modificare il nome della tabella di destinazione in **SalesOrderDetail**.
    2. Rimuovere la colonna **rowguid** . Il tipo di dati **uniqueidentifier** non è supportato in SQL Data Warehouse.
    3. Modificare il tipo di dati della colonna **LineTotal** in **denaro**. Tipo di dati **decimal** non è supportato in SQL Data Warehouse. Per informazioni sui tipi di dati supportati, vedere [CREATE TABLE (Warehouse di dati di SQL Azure, Parallel Data Warehouse)][].
    
        ![][12b]
    
    4. Fare clic su **OK** per creare la tabella e tornare all' **Editor di destinazione ADO.NET**.

8. Nell' **Editor di destinazione ADO.NET**, selezionare la scheda **mapping** per visualizzare il mapping tra le colonne di origine e colonne nella destinazione.

    ![][13]

9. Fare clic su **OK** per completare la configurazione dell'origine dati.

## <a name="step-6-run-the-package-to-load-the-data"></a>Passaggio 6: Eseguire il pacchetto per caricare i dati

Eseguire il pacchetto facendo clic sul pulsante **Start** sulla barra degli strumenti o selezionando una delle opzioni di **esecuzione** del menu **Debug** .

Il pacchetto inizia per l'esecuzione, vengono visualizzate delle rotelline Vortice gialle per indicare attività, nonché il numero di righe elaborate finora.

![][14]

Quando il pacchetto è terminata, viene visualizzato segni di spunta verdi per indicare esito positivo, nonché il numero totale di righe di dati caricati dall'origine alla destinazione.

![][15]

Congratulazioni! È stato usato correttamente SQL Server Integration Services per caricare i dati nell'archivio di dati di SQL Azure.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni relative al flusso di dati SSIS. Iniziare qui: [Flusso di dati][].
- Informazioni su come eseguire il debug e risoluzione dei problemi il diritto di pacchetti nell'ambiente di progettazione. Iniziare qui: [Risoluzione dei problemi di strumenti per lo sviluppo di pacchetto][].
- Informazioni su come distribuire i progetti SSIS e i pacchetti di Integration Services Server o in un'altra posizione di archiviazione. Iniziare qui: [distribuzione di progetti e di pacchetti][].

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[Guida di PolyBase]: https://msdn.microsoft.com/library/mt143171.aspx
[Scaricare SQL Server Data Tools (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[CREARE una tabella (SQL Azure Data Warehouse, Parallel Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Flusso di dati]: https://msdn.microsoft.com/library/ms140080.aspx
[Risoluzione dei problemi di strumenti per lo sviluppo di pacchetto]: https://msdn.microsoft.com/library/ms137625.aspx
[Distribuzione di progetti e di pacchetti]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Servizi di integrazione di Microsoft SQL Server 2016 Feature Pack per Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[Valutazioni di SQL Server]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Community di Visual Studio]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[Database di esempio AdventureWorks 2014]: https://msftdbprodsamples.codeplex.com/releases/view/125550
