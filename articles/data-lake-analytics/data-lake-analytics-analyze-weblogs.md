<properties
   pageTitle="Analizzare i registri di sito Web con Azure dati Lake Analitica | Azure"
   description="Informazioni su come analizzare i registri di sito Web con dati Lake Analitica. "
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-analyze-website-logs-using-azure-data-lake-analytics"></a>Esercitazione: Analizzare i registri di sito Web usando Analitica Lake dati di Azure

Informazioni su come analizzare i registri di sito Web con dati Lake Analitica, in particolare su come individuare quale riferimenti verificati errori quando si tenta di visitare il sito Web.

>[AZURE.NOTE] Se si desidera visualizzare solo l'applicazione che funziona, consente di risparmiare tempo elaborata [Tutorial interattivi usare Azure dati Lake Analitica](data-lake-analytics-use-interactive-tutorials.md). In questa esercitazione si basa lo stesso scenario e lo stesso codice. Lo scopo di questa esercitazione è per offrire agli sviluppatori l'esperienza di creazione e l'esecuzione di un'applicazione di dati Lake Analitica to end.

## <a name="prerequisites"></a>Prerequisiti:

- **Visual Studio 2015, Visual Studio 2013 aggiornare 4, o Visual Studio 2012 con installato Visual C++**.
- **Microsoft Azure SDK per .NET versione 2.5 o versione successiva**.  Installare Office tramite [installazione guidata piattaforma Web](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Dati Lake Tools per Visual Studio](http://aka.ms/adltoolsvs)**.

    Dopo aver installato dati Lake Tools per Visual Studio, verrà visualizzato un menu **Dati Lake** Visual Studio:

    ![Menu U SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **Knowledge base di dati Lake Analitica e dati Lake Tools per Visual Studio**. Per iniziare, vedere:

    - [Guida introduttiva di Azure dati Lake Analitica nel portale di Azure](data-lake-analytics-get-started-portal.md).
    - [Script di sviluppare U-SQL mediante dati Lake tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

- **Un account di dati Lake Analitica.**  Vedere [creare un account Azure dati Lake Analitica](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).

    Gli strumenti di Lake dati non supporta la creazione di account di dati Lake Analitica.  Pertanto è necessario creare tramite il portale di Azure, Azure PowerShell, .NET SDK o CLI Azure.
- **Caricare i dati di esempio per l'account di dati Lake Analitica.** Vedere [Caricare SearchLog.tsv per l'account di archiviazione dei dati Lake predefinito](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account).

    Per eseguire un processo dati Lake Analitica, sarà necessario alcuni dati. Anche se gli strumenti di Lake dati supporta il caricamento di dati, utilizzare il portale per caricare i dati di esempio per rendere più semplice da seguire questa esercitazione.

## <a name="connect-to-azure"></a>Connettersi a Azure

Prima di creare e testare qualsiasi script U-SQL, è necessario connettersi Azure.

**Per connettersi a dati Lake Analitica**

1. Aprire Visual Studio.
2. Dal menu **Lake dati** fare clic su **Opzioni e le impostazioni**.
4. Fare clic su **Accedi**o **Cambia utente** se un utente ha effettuato l'accesso e seguire le istruzioni.
5. Fare clic su **OK** per chiudere la finestra di dialogo Opzioni e le impostazioni.

**Per visualizzare gli account di dati Lake Analitica**

1. Aprire **Esplora Server** da Visual Studio, premere **CTRL + ALT + S**.
2. Da **Esplora Server**espandere **Azure**e quindi espandere **Dati Lake Analitica**. Si deve visualizzare un elenco degli account dati Lake Analitica se presenti. Non è possibile creare gli account dati Lake Analitica da studio. Per creare un account, vedere [Guida introduttiva di Azure dati Lake Analitica nel portale di Azure](data-lake-analytics-get-started-portal.md) o [Inizia con Azure dati Lake Analitica tramite PowerShell Azure](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Sviluppare applicazione U SQL

Un'applicazione SQL U è prevalentemente uno script U-SQL. Per ulteriori informazioni su U-SQL, vedere [Guida introduttiva a U-SQL](data-lake-analytics-u-sql-get-started.md).

È possibile aggiungere gli operatori definiti dall'utente aggiunta all'applicazione.  Per ulteriori informazioni, vedere [operatori per i processi di dati Lake Analitica definite dall'utente di sviluppare U-SQL](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Per creare e inviare un processo dati Lake Analitica**

1. Dal menu **File** fare clic su **Nuovo**e quindi fare clic su **progetto**.
2. Selezionare il tipo di progetto U-SQL.

    ![nuovo progetto di Visual Studio U SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Fare clic su **OK**. Visual studio crea una soluzione con un file Script.usql.
4. Immettere il seguente script nel file Script.usql:

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN

            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
            RETURN;
        END;

        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            PARTITIONED BY HASH(Year)
        ) AS

        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;

    Per comprendere U-SQL, vedere [Guida introduttiva a linguaggio dati Lake Analitica U-SQL](data-lake-analytics-u-sql-get-started.md).    

5. Aggiungere un nuovo script SQL U al progetto e immettere le operazioni seguenti:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();

6. Passare al primo script U SQL e accanto al pulsante **Invia** , specificare l'account Analitica.
7. Da **Esplora soluzioni**fare clic con il pulsante destro **Script.usql**e quindi fare clic su **Creazione di Script**. Verificare i risultati nel riquadro di Output.
8. Da **Esplora soluzioni**fare clic con il pulsante destro **Script.usql**e quindi fare clic su **Invia Script**.
9. Verificare che l' **Account Analitica** sia quella in cui si vuole eseguire il processo e quindi fare clic su **Invia**. Risultati invio e collegamento processo sono disponibili negli strumenti Lake di dati per finestra Risultati Visual Studio al termine della presentazione.
10. Attendere finché non viene eseguito correttamente al termine del processo.  Se il processo non è riuscita, non è probabile che il file di origine.  Vedere la sezione Prerequisita di questa esercitazione. Per ulteriori informazioni sulla risoluzione dei problemi, vedere [Monitor e risolvere i processi di Azure dati Lake Analitica](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    Una volta completato il processo, risulta la schermata seguente:

    ![analitica lake dati analizza i registri di sito Web blog](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)

11. A questo punto, ripetere i passaggi da 7 a 10 per **Script1.usql**.

>[AZURE.NOTE]Non è possibile leggere o scrivere in una tabella di SQL U che è stata creata o modificata nello stesso script.  Per tale motivo utilizzare due script per questo esempio.

**Per visualizzare l'output di processo**

1. Da **Esplora Server**espandere **Azure**, espandere **Dati Lake Analitica**, espandere account dati Lake Analitica, espandere **Gli account di archiviazione**, rapida l'account di archiviazione dei dati Lake predefinito e quindi fare clic su **Esplora risorse**.
2.  Fare doppio clic su **campioni** per aprire la cartella e quindi fare doppio clic su **output**.
3.  Fare doppio clic su **UnsuccessfulResponsees.log**.
4.  Fare doppio clic il file di output all'interno della visualizzazione grafico del processo per passare direttamente all'output.

## <a name="see-also"></a>Vedere anche

Per iniziare a utilizzare dati Lake Analitica utilizzando strumenti diversi, vedere:

- [Guida introduttiva a Analitica Lake dati nel portale di Azure](data-lake-analytics-get-started-portal.md)
- [Guida introduttiva a Analitica Lake dati tramite PowerShell Azure](data-lake-analytics-get-started-powershell.md)
- [Guida introduttiva a dati Lake Analitica utilizzando .NET SDK](data-lake-analytics-get-started-net-sdk.md)

Per visualizzare altri argomenti di sviluppo:

- [Sviluppare script U SQL mediante dati Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Guida introduttiva a linguaggio Azure dati Lake Analitica U-SQL](data-lake-analytics-u-sql-get-started.md)
- [Sviluppare operatori definiti dall'utente U SQL per i processi di dati Lake Analitica](data-lake-analytics-u-sql-develop-user-defined-operators.md)
