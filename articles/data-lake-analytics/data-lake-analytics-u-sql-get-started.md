<properties
   pageTitle="Sviluppare script U SQL mediante dati Lake Tools per Visual Studio | Azure"
   description="Informazioni su come installare dati Lake Tools per Visual Studio, come sviluppare e gli script di test U-SQL. "
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

# <a name="tutorial-get-started-with-azure-data-lake-analytics-u-sql-language"></a>Esercitazione: Introduzione a linguaggio Azure dati Lake Analitica U-SQL

U-SQL è un linguaggio che include i vantaggi di SQL con la potenza esplicita di codice per l'elaborazione di tutti i dati la scala. Funzionalità di query distribuita scalable U-SQL consente di analizzare in modo efficiente i dati nell'archivio e in archivi relazionali, ad esempio Database SQL Azure.  Consente al processo dati non strutturati applicando schema sul leggere, inserire logica personalizzata e dell'utente e includono estensibilità per abilitare correttamente granulare controllo su come eseguire in scala. Per ulteriori informazioni sulla filosofia di progettazione U SQL, vedere questo [post di blog di Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Esistono alcune differenze da SQL ANSI o T-SQL. Ad esempio, le parole chiave, ad esempio selezionare devono essere maiuscole.

'S linguaggio del tipo di sistema ed espressione all'interno le clausole select, in cui si trovano c# predicati e così via.
Questo significa che i tipi di dati sono i tipi di c# e i tipi di dati utilizzano semantica c# NULL e le operazioni di confronto all'interno di un predicato sintassi c# (ad esempio, un = = "foo").  Questo significa anche, che i valori siano completi oggetti .NET, che consente di utilizzare facilmente qualsiasi metodo operare su oggetto (ad esempio "f o o o". Split(' ')).

Per ulteriori informazioni, vedere [Informazioni di riferimento U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

###<a name="prerequisites"></a>Prerequisiti

È necessario completare [Esercitazione: sviluppare script U SQL mediante dati Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

Nell'esercitazione, è stato eseguito un processo dati Lake Analitica con lo script U SQL seguente:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();

Questo script non dispone di tutte le operazioni di trasformazione. Legge dal file di origine denominato **SearchLog.tsv**, schematizes essa e restituisce il set di righe in un file denominato **SearchLog-primo-u-sql.csv**.

Si noti il punto interrogativo accanto al tipo di dati del campo Durata. Ciò significa che il campo Durata potrebbe essere null.

Alcuni concetti e trovare lo script le parole chiave:

- **Variabili del set di righe**: ogni espressione di query che restituisce un set di righe può essere assegnate a una variabile. U SQL segue il modello di denominazione T-SQL variabile, ad esempio **@searchlog** dello script.
    Nota l'assegnazione non forzare l'esecuzione. Viene semplicemente il valore di expression e offre la possibilità di sviluppo espressioni più complesse.
- **Estrarre** offre la possibilità di definire uno schema di lettura. Lo schema viene specificato un nome di colonna e c# coppia di tipo nome per ogni colonna. Viene utilizzata una cosiddetta **estrazione**, ad esempio, per estrarre file tsv **Extractors.Tsv()** . È possibile sviluppare utilità di estrazione personalizzata.
- **OUTPUT** accetta un set di righe e serializza. Il Outputters.Csv() output di un file delimitati da virgole in una posizione specificata. È anche possibile sviluppare Outputters personalizzato.
- Si noti che due percorsi sono percorsi relativi. È anche possibile utilizzare percorsi assoluti.  Per esempio

        adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Per accedere ai file nella finestra account di archiviazione collegate, è necessario utilizzare percorso assoluto.  La sintassi per i file memorizzati in account di archiviazione Azure collegato è:

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Contenitore di Blob Azure con BLOB pubblico o le autorizzazioni di accesso contenitori pubblico non sono attualmente supportati.

## <a name="use-scalar-variables"></a>Usare variabili scalari

È possibile utilizzare variabili scalari anche per semplificare la manutenzione di script. Script SQL U precedente può essere scritta anche come indicato di seguito:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Trasformare i set di righe

Consente di **Selezionare** per trasformare set di righe:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

La clausola WHERE utilizza [c# espressione booleana](https://msdn.microsoft.com/library/6a71f45d.aspx). È possibile utilizzare il linguaggio delle espressioni c# per eseguire le espressioni e funzioni. È anche possibile eseguire il filtro più complesse combinando loro con congiunzioni logici (e) e disjunctions (ORs).

Il seguente script utilizza il metodo DateTime.Parse() e un insieme.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();

Si noti che la seconda query funzionamento sul risultato del primo set di righe e quindi il risultato è una composizione dei due filtri. È inoltre possibile riutilizzare un nome di variabili e i nomi inclusi nell'ambito lessicale.

## <a name="aggregate-rowsets"></a>Aggrega set di righe

U-SQL offre **Familiarità **ORDER BY**, GROUP BY** e le aggregazioni.

La query seguente trova la durata totale per regione e quindi visualizzato nella parte superiore 5 durate in ordine.

Set di righe SQL U non mantengono l'ordine per la query successiva. Per ordinare un output, pertanto è necessario aggiungere ORDER BY all'istruzione di OUTPUT, come illustrato di seguito:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Clausola SQL U ORDER BY deve essere combinato con la clausola recupero in un'espressione di seleziona.

Clausola SQL U che può essere utilizzato per restringere l'output ai gruppi che soddisfano la condizione HAVING:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## <a name="join-data"></a>Unire i dati

U SQL fornisce operatori join comuni, ad esempio INNER JOIN, a sinistra/destra/FULL OUTER JOIN, SEMI JOIN, per partecipare non solo le tabelle ma qualsiasi set di righe (anche quelle ottenute da file).

Il seguente script di join searchlog con un log impressione annuncio e offre gli annunci pubblicitari la stringa di query per una data specificata.

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();

    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;

    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


U-SQL supporta solo la sintassi di join conforme ANSI: predicato Rowset1 JOIN Rowset2 via. La vecchia sintassi da Rowset1, predicato Rowset2 nel punto in cui non è supportata.
Il predicato in un JOIN è necessario che un join di uguaglianza e senza espressione. Se si desidera utilizzare un'espressione, aggiungerla a clausola select del set di righe precedente. Se si desidera eseguire un confronto diverso, è possibile spostare nella clausola WHERE.


## <a name="create-databases-table-valued-functions-views-and-tables"></a>Creare database, funzioni con valori di tabella, visualizzazioni e tabelle

U-SQL consente di usare i dati nel contesto di un database e lo schema. Pertanto non è necessario sempre leggere o scrivere ai file.

Ogni script U SQL viene eseguito con un database predefinito (master) e lo schema predefinito (DBO) come contesto predefinito. È possibile creare il proprio database e/o schema. Per modificare il contesto, utilizzare l'istruzione **utilizzare** per modificare il contesto.


### <a name="create-a-table-valued-function-tvf"></a>Creare una funzione con valori di tabella (TVF)

In script SQL U precedente, ripetuti utilizzando estrarre la lettura dello stesso file di origine. Funzione con valori di tabella SQL U consente di racchiudere i dati da riutilizzare in futuro.   

Lo script seguente crea una TVF chiamato *Searchlog()* nel database predefinito e schema:

    DROP FUNCTION IF EXISTS Searchlog;

    CREATE FUNCTION Searchlog()
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;

Lo script seguente viene illustrato come utilizzare TVF definito nello script precedente:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-views"></a>Creare visualizzazioni

Se si dispone di un'espressione di query che si desidera estrarre e non si desidera aggiungere un parametro, è possibile creare una visualizzazione anziché una funzione con valori di tabella.

Lo script seguente crea una visualizzazione denominata *SearchlogView* nel database predefinito e schema:

    DROP VIEW IF EXISTS SearchlogView;

    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

Lo script seguente viene illustrato come utilizzare la vista definita:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-tables"></a>Creare tabelle

Analogamente a tabella di database relazionali, U-SQL consente di creare una tabella con uno schema predefinito o creare una tabella e dedurre lo schema dalla query che inserisce nella tabella (noto anche come CREATE TABLE AS SELECT o CTAS).

Il seguente script di creare un database e due tabelle:

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SeachLogDb
    USE DATABASE SearchLogDb;

    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;

    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,

                INDEX sl_idx CLUSTERED (UserId ASC)
                    PARTITIONED BY HASH (UserId)
    );

    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC)
                PARTITIONED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### <a name="query-tables"></a>Tabelle di query

È possibile ricercare le tabelle (create nello script precedente) nello stesso modo si query rispetto ai file di dati. Invece di creare un set di righe utilizzando estratto, è ora possibile solo fare riferimento al nome della tabella.

Lo script di trasformazione utilizzato in precedenza viene modificato per leggere le tabelle:

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Si noti che non è attualmente possibile eseguire un'istruzione SELECT in una tabella nello stesso script lo script in cui si crea tale tabella.


##<a name="conclusion"></a>Conclusioni

Che cos'è coperto nell'esercitazione è una piccola parte di U-SQL. A causa dell'ambito di questa esercitazione, essa non può coprire tutte le operazioni, ad esempio:

- Usare CROSS APPLY per decomprimere parti di stringhe, matrici e mappe in righe.
- Operare partizionato set di dati (insiemi di file e le tabelle partizionate).
- Sviluppare operatori definiti dall'utente, ad esempio le utilità di estrazione, outputters, processori, aggregatori definite dall'utente in c#.
- Usare le funzioni di finestre U-SQL.
- Gestire il codice SQL U con le viste, stored procedure e funzioni con valori di tabella.
- Eseguire il codice personalizzato non autorizzato nei nodi del elaborazione.
- Connettersi al database SQL Azure e query federate tra loro e i dati di SQL U e Lake di dati di Azure.

## <a name="see-also"></a>Vedere anche

- [Panoramica di Microsoft Azure dati Lake Analitica](data-lake-analytics-overview.md)
- [Sviluppare script U SQL mediante dati Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Utilizzo delle funzioni finestra U SQL per i processi di Azure dati Lake Analitica](data-lake-analytics-use-window-functions.md)
- [Monitorare e risolvere i processi di Azure dati Lake Analitica nel portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="let-us-know-what-you-think"></a>Indicare si ritiene che

- [Inviare una richiesta di funzionalità](http://aka.ms/adlafeedback)
- [È possibile ottenere assistenza nei forum](http://aka.ms/adlaforums)
- [Inviare commenti e suggerimenti su SQL U](http://aka.ms/usqldiscuss)
