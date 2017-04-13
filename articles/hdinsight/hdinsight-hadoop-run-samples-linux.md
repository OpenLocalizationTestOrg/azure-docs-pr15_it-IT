<properties
    pageTitle="Eseguire Hadoop MapReduce esempi su basati su Linux HDInsight | Microsoft Azure"
    description="Iniziare a usare gli esempi MapReduce con HDInsight basati su Linux. Consente di connettersi al cluster SSH, quindi usare il comando Hadoop per eseguire i processi di esempio."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="larryfr"/>




#<a name="run-the-hadoop-samples-in-hdinsight"></a>Eseguire gli esempi Hadoop in HDInsight

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Cluster HDInsight basati su Linux offrono un set di campioni di MapReduce che può essere utilizzato per acquisire familiarità con processi Hadoop MapReduce. Nel documento, vedere esempi disponibili e scorrere in esecuzione alcune di esse.

##<a name="prerequisites"></a>Prerequisiti

- **Azure un abbonamento**: vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

- **In base A Linux HDInsight cluster**: vedere [Introduzione all'utilizzo di Hadoop con Hive in HDInsight su Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

- **Un SSH client**: per informazioni sull'utilizzo di SSH con HDInsight, vedere gli articoli seguenti:

    - [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="the-samples"></a>Gli esempi ##

**Posizione**: gli esempi si trovano sul cluster HDInsight in **/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar**

**Contenuto**: negli esempi seguenti sono contenuti nell'archivio di:

- **aggregatewordcount**: un'aggregazione in base a programma mappa/ridurre che conta le parole nei file di input
- **aggregatewordhist**: un'aggregazione in base a programma mappa/ridurre che calcola l'istogramma delle parole nei file di input
- **bbp**: un programma mappa/ridurre che utilizza Plouffe di Borwein Bailey per calcolare cifre a precisione di pi greco
- **dbcount**: un processo di esempio che esegue il conteggio dei registri del pageview archiviati in un un database
- **distbbp**: un programma mappa/ridurre che utilizza una formula BBP tipo per calcolare bit esatta di pi greco
- **GREP**: un programma mappa/ridurre che conta le corrispondenze di espressioni regolari di input
- **join**: un processo che ha effetto un join supera ordinati, identico suddiviso set di dati
- **multifilewc**: un processo che esegue il conteggio parole dal file diverse
- **pentomino**: mappa/ridurre il riquadro deposizione programma per trovare le soluzioni ai problemi pentomino
- **pi greco**: un programma mappa/ridurre che restituisce una stima Pi usando un periodo di quasi Monte metodo Carlo
- **randomtextwriter**: un programma mappa/ridurre che scrive 10 GB di dati testuali casuali per nodo
- **randomwriter**: un programma mappa/ridurre che scrive 10 GB di dati casuali per nodo
- **secondarySort**: un esempio che definisce un ordinamento secondario per la riduzione
- **ordinamento**: un programma mappa/ridurre che ordina i dati scritti dal writer casuale
- **Sudoku**: un Risolutore sudoku
- **teragen**: generare dati per il terasort
- **terasort**: eseguire il terasort
- **teravalidate**: controllo dei risultati di terasort
- **WordCount**: un programma mappa/ridurre che conta le parole nei file di input
- **wordmean**: un programma mappa/ridurre che conta la lunghezza media delle parole nei file di input
- **wordmedian**: un programma mappa/ridurre che conta il numero mediano le parole nei file di input
- **wordstandarddeviation**: un programma mappa/ridurre che calcola la deviazione standard della lunghezza delle parole nei file di input

**Codice sorgente**: codice sorgente per questi esempi è incluso nel cluster HDInsight in **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples**

> [AZURE.NOTE] Il `2.2.4.9-1` nel percorso è la versione della piattaforma dati Hortonworks per il cluster HDInsight e possono cambiare quando vengono aggiornati HDInsight.

## <a name="how-to-run-the-samples"></a>Come eseguire gli esempi ##

1. Connettersi a HDInsight utilizzando SSH come descritto nei seguenti articoli:

    - [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Dal `username@#######:~$` Chiedi conferma, utilizzare il comando seguente per elencare gli esempi:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

    Questo genera l'elenco di esempio dalla sezione precedente del documento.

3. Utilizzare il comando seguente per ottenere assistenza un campione specifico. In questo caso, l'esempio **wordcount** :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount

    Visualizzato il messaggio seguente:

        Usage: wordcount <in> [<in>...] <out>

    Indica che è possibile specificare più percorsi di input per i documenti di origine. Il percorso finale è posizione in cui è archiviato l'output (conteggio delle parole nei documenti di origine).

4. Per contare tutte le parole in blocchi appunti di Leonardo Da Vinci, fornite come dati di esempio con i cluster, utilizzare il seguente:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount

    Input per questo processo viene letto da **wasbs:///example/data/gutenberg/davinci.txt**.

    In output per questo esempio verrà archiviato **wasbs: / / / esempio/dati/davinciwordcount**.

    > [AZURE.NOTE] Come indicato nella Guida di esempio wordcount, è inoltre possibile specificare più file di input. Ad esempio `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` conta le parole in davinci.txt e ulysses.txt.

5. Al termine del processo, utilizzare il comando seguente per visualizzare l'output:

        hdfs dfs -cat /example/data/davinciwordcount/*

    Concatena tutti i file di output prodotti dal processo e visualizzarle. In questo esempio di base è un solo file, tuttavia se sono stati rilevati più questo comando da scorrere tutti gli elementi.

    L'output è simile al seguente:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Ogni riga rappresenta una parola e quante volte si è verificato nei dati di input.

## <a name="sudoku"></a>Sudoku

Nell'esempio Sudoku è istruzioni sull'utilizzo di un po' inutile; "Includono puzzle nella riga di comando".

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) è un puzzle logica costituito dallo nove 3x3 griglie. Alcune celle nella griglia contengono numeri, mentre altre sono vuoti e l'obiettivo è risolvere per le celle vuote. Questo collegamento per ulteriori informazioni sul puzzle, ma lo scopo di questo esempio è risolvere per le celle vuote. Pertanto il nostro input deve essere un file nel formato seguente:

- Nove righe delle nove colonne

- Ogni colonna deve contenere un numero o `?` (che indica una cella vuota)

- Le celle sono separate da uno spazio

È possibile costruire puzzle Sudoku; non è possibile ripetere un numero in una colonna o riga. Esiste un esempio cluster HDInsight sia impostata correttamente. È si trova in **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta** e contiene le operazioni seguenti:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

> [AZURE.NOTE] Il `2.2.4.9-1` parte del percorso può essere modificata quando gli aggiornamenti effettuati in cluster HDInsight.

Per eseguire questa operazione tramite l'esempio Sudoku, usare il comando seguente:

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/2.2.9.1-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta

I risultati dovrebbero essere simili al seguente:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi-"></a>Pi greco (π)

Nell'esempio di pi greco viene utilizzato un statistiche (periodo di quasi Monte Carlo) metodo per calcolare il valore di pi greco. Punti inseriti in modo casuale all'interno di un'unità sono compresi quadrato anche in un cerchio figurare all'interno di tale quadrato con una probabilità uguale all'area del cerchio, pi greco/4. Il valore di pi greco può essere stimato rispetto al valore del 4R, dove R è il rapporto tra il numero di punti che si trovano all'interno del cerchio e il numero totale di punti che si trovano entro la radice quadrata. Più grande all'esempio di punti utilizzati più facilmente la stima è.

Il mapping per questo esempio viene generato un numero di punti in modo casuale all'interno di un quadrato unità e quindi viene contato il numero di tali punti che si trovano all'interno del cerchio.

Il riduttore quindi si accumula punti per la proprietà temporaneo contati e calcola il valore di pi greco da 4R formule, in cui R è il rapporto tra il numero di punti contati all'interno del cerchio e il numero totale di punti che si trovano entro la radice quadrata.

Utilizzare il comando seguente per eseguire questo esempio. Utilizza 16 mappe con 10.000.000 esempi per calcolare il valore di pi greco:

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000

Valore restituito da questa dovrebbe essere simile a **3.14159155000000000000**. Per i riferimenti, primi 10 cifre decimali di pi greco sono 3.1415926535.

##<a name="10gb-greysort"></a>10GB Greysort

GraySort è un ordinamento benchmark cui metrica è il tasso di ordinamento (TB/minuto) che è possibile ottenere durante l'ordinamento molto grandi quantità di dati, in genere un 100TB minimo.

In questo esempio viene usata una limitata 10GB di dati in modo che possa essere eseguita relativamente rapidamente. Utilizza le applicazioni di MapReduce sviluppate da Owen O'Malley e Arun Murthy che hanno vinto annuale benchmark di ordinamento terabyte generale ("daytona") di 2009 con un tasso di 0.578 TB/min (100 TB in minuti 173). Per ulteriori informazioni su questo e altri benchmark ordinamento, vedere il sito [Sortbenchmark](http://sortbenchmark.org/) .

In questo esempio Usa tre serie di applicazioni MapReduce:

- **TeraGen**: un programma MapReduce che genera righe di dati per l'ordinamento

- **TeraSort**: esempi i dati di input e Usa MapReduce per ordinare i dati in un ordine di totale

    TeraSort è un ordinamento standard delle funzioni MapReduce, ad eccezione di un partitioner personalizzato che utilizza un elenco ordinato delle chiavi N-1 campionati che definiscono l'intervallo di chiave per ogni ridurre. In particolare, tutte le chiavi tali che esempio [i-1] < = chiave < esempio [i] vengono inviati per ridurre i. Questo garanzie che gli output di ridurre i sono tutti minore di output di ridurre i + 1.

- **TeraValidate**: un programma MapReduce che verifica che l'output viene ordinato globalmente

    Crea una mappa per ogni file nella directory di output e ogni mapping assicura che ogni chiave è minore o uguale a quella precedente. La funzione mappa genera anche i record delle chiavi nome e il cognome di ogni file e la funzione Riduci garantisce il prima chiave del file i oltre l'ultima chiave i-1 file. Gli eventuali problemi indicati come esito della riduzione con i tasti che non sono in ordine.

Utilizzare la seguente procedura per generare i dati, ordinare e quindi convalidare l'output:

1. Generare 10GB di dati, verranno archiviati allo spazio di archiviazione predefinito del cluster HDInsight in **wasbs: / / / esempio / / 10GB-Ordina-input di dati**:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input

    Il `-Dmapred.map.tasks` indica Hadoop quante attività mappa da utilizzare per il processo. I due parametri finali indicare il processo di creazione di 10GB di dati e archiviarlo in **wasbs: / / / esempio / / 10GB-Ordina-input di dati**.

2. Per ordinare i dati, utilizzare il comando seguente:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output

    Il `-Dmapred.reduce.tasks` indica Hadoop quanti ridurre le attività da utilizzare per il processo. I due parametri finali sono solo i percorsi di input e di output per i dati.

3. Utilizzare le operazioni seguenti per convalidare i dati generati dall'ordinamento:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate

##<a name="next-steps"></a>Passaggi successivi ##

In questo articolo è stato descritto come eseguire gli esempi inclusi con i cluster basati su Linux HDInsight. Per esercitazioni sull'utilizzo di maialino, Hive e MapReduce con HDInsight, vedere gli argomenti seguenti:

* [Utilizzare maialino con Hadoop in HDInsight][hdinsight-use-pig]
* [Usare Hive con Hadoop in HDInsight][hdinsight-use-hive]
* [Utilizzare MapReduce con Hadoop in HDInsight] [hdinsight-use-mapreduce]



[hdinsight-errors]: hdinsight-debug-jobs.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
