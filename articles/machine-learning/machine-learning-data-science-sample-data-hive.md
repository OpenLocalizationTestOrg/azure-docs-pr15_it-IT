<properties
    pageTitle="Esempio di dati in tabelle Azure HDInsight Hive | Microsoft Azure"
    description="Verso il basso il campionamento dei dati nelle tabelle Hive Azure HDInsight (Hadopop)"
    services="machine-learning,hdinsight"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="hangzh;bradsev" />

# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Dati di esempio in tabelle Azure HDInsight Hive

In questo articolo è viene descritto come campioni verso il basso dati archiviati in tabelle Azure HDInsight Hive l'utilizzo delle query Hive. Vengono illustrati i metodi di campioni normalmente usati tre:

* Sondaggio uniforme
* Sondaggio dai gruppi
* Esempio stratificata

**Perché i dati di esempio?**
Se il set di dati che si intende analizzare grandi dimensioni, in genere è consigliabile campioni verso il basso i dati per ridurre le dimensioni inferiori ma representative e più gestibili. In questo modo si facilita informazioni sui dati, ricerca e progettazione di funzionalità. Il proprio ruolo del processo di scienze dei dati del Team è consentire la creazione di prototipi veloce delle funzioni di elaborazione dei dati e modelli di apprendimento automatico.

**Menu** collegamenti ad argomenti che descrivono come i dati da vari ambienti di spazio di archiviazione di esempio seguenti.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Questa attività di esempio è un passaggio di [Team dati scienza processo (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="how-to-submit-hive-queries"></a>Come inviare query Hive
Query hive possono essere inviate dalla console di riga di comando Hadoop sul nodo principale del cluster Hadoop. A tale scopo, accedere a nodo principale del cluster Hadoop, aprire la console di riga di comando Hadoop e inviare le query Hive da tale posizione. Per ulteriori informazioni sull'invio di query Hive nella console di riga di comando Hadoop, vedere [come inviare query Hive](machine-learning-data-science-move-hive-tables.md#submit).

## <a name="uniform"></a>Sondaggio uniforme
Sondaggio uniforme indica che un rischio uguale di campionato ogni riga nel set di dati. Può essere implementato mediante l'aggiunta di un RAND campo aggiuntivo al set di dati in query "select" interna e la query "select" outer tale condizione su quel campo casuale.

Ecco un esempio di query:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

In questo caso, `<sample rate, 0-1>` specifica le proporzioni di record da utenti di esempio.

## <a name="group"></a>Sondaggio dai gruppi

Quando i dati per categoria campioni, può essere necessario includere o escludere tutte le istanze di un valore specifico di una variabile per categoria. Si tratta di cosa si intende per "sondaggio dal gruppo".
Ad esempio, se si dispone di una variabile di tipo "Stato" per categoria, che ha valori NY, MA, CA, NJ, PA e così via, cui si vuole record dello stesso stato sempre insieme, se sono vengono campionati o meno.

Ecco un esempio di query che esempi dal gruppo:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>Esempio stratificata

Sondaggio è propagazione rispetto a una variabile per categoria quando campioni ottenuti sono presenti valori di che per categoria che si trovano nella stessa proporzione come la popolazione padre da cui provengono gli esempi. Nel caso dell'esempio stesso come sopra, si supponga che i dati includono popolazioni secondari dagli Stati, ad esempio NJ ha 100 osservazioni, NY deve 60 osservazioni e WA previsti 300 osservazioni. Se si specifica il tasso di campioni stratificate a 0,5, il campione ottenuta deve essere circa 50, 30 e 150 osservazioni di NJ, NY e WA rispettivamente.

Ecco un esempio di query:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
            rank() over (partition by state order by rand()) as state_rank
        from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Per informazioni sui metodi campioni più avanzati che sono disponibili in Hive, vedere [LanguageManual campioni](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).
