<properties
    pageTitle="Genera indicazioni utilizzando Mahout e basate su Linux HDInsight | Microsoft Azure"
    description="Informazioni su come utilizzare la libreria di apprendimento Apache Mahout per generare consigli filmato con HDInsight basati su Linux (Hadoop)."
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
    ms.date="08/30/2016"
    ms.author="larryfr"/>

#<a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight"></a>Genera indicazioni filmato utilizzando Apache Mahout con basati su Linux Hadoop in HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Informazioni su come utilizzare la libreria con Azure HDInsight apprendimento automatico [Apache Mahout](http://mahout.apache.org) per generare consigli filmato.

Mahout è un [computer formazione] [ ml] raccolta per Apache Hadoop. Mahout contiene algoritmi per l'elaborazione dei dati, ad esempio, classificazione, cluster. In questo articolo si utilizzerà un motore recommendation per generare suggerimenti filmato basati sui film che hanno visto amici.

> [AZURE.NOTE] La procedura descritta in questo documento richiesta un Hadoop basati su Linux cluster HDInsight. Per informazioni sull'utilizzo di Mahout con un cluster basato su Windows, vedere [suggerimenti filmato genera utilizzando Apache Mahout con basato su Windows Hadoop in HDInsight](hdinsight-mahout.md)

##<a name="prerequisites"></a>Prerequisiti

* Hadoop basati su Linux cluster HDInsight. Per informazioni sulla creazione di una, vedere [Introduzione all'utilizzo basati su Linux Hadoop in HDInsight][getstarted]

##<a name="mahout-versioning"></a>Controllo delle versioni mahout

Per ulteriori informazioni sulla versione di Mahout incluso con i cluster HDInsight, vedere [versioni HDInsight e Hadoop componenti](hdinsight-component-versioning.md).

> [AZURE.WARNING] Sebbene sia possibile caricare una versione diversa di Mahout cluster HDInsight, solo i componenti forniti con il cluster HDInsight sono completamente supportati e supporto Microsoft aiuta a isolare e risolvere i problemi relativi a questi componenti.
>
> Componenti personalizzati riceveranno supporto per le misure che consentono di risolvere il problema. Questo può comportare la risoluzione del problema o in cui viene richiesto di effettuare canali disponibili per le tecnologie Apri origine in cui si trova esperienza completa per tale tecnologia. Ad esempio, sono disponibili numerosi siti della community che possono essere usati, ad esempio: [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Progetti Apache dovranno siti di progetto in [http://apache.org](http://apache.org), ad esempio: [Hadoop](http://hadoop.apache.org/), [motori](http://spark.apache.org/).

##<a name="recommendations"></a>Suggerimenti di informazioni

Una delle funzioni fornito da Mahout è un motore recommendation. Questo motore accetta i dati nel formato di `userID`, `itemId`, e `prefValue` (preferenza agli utenti per l'elemento). Mahout quindi possibile eseguire analisi di CO-occorrenza per determinare: _gli utenti che dispongono di una preferenza per un elemento dispongono anche di una preferenza per questi altri elementi_. Mahout determina con preferenze elemento mi piace, che possono essere utilizzate per suggerimenti degli utenti.

Di seguito è illustrato un esempio estremamente semplice che utilizza filmati:

* __CO-occorrenza__: Fausto, Alice e Roberto mi _attraverso stella_, _Il Empire scioperi indietro_e _tornare del Jedi_. Mahout determina che gli utenti che ad esempio uno di questi filmati anche come gli altri due.

* __CO-occorrenza__: Davide e Alice anche apprezzamento per _Il alieni fantasma_, _attacco i duplicati_e _ritorsioni del Sith_. Mahout determina che gli utenti che mi i tre filmati precedenti anche come questi tre.

* __Suggerimento analogie__: Fausto perché apprezzamento per i primi tre filmati, Mahout esamina filmati che altri utenti mediante Preferenze simili apprezzamento, ma Fausto non è controllate (mi/classificazione). In questo caso, Mahout consiglia _Il alieni fantasma_, _attacco i duplicati_e _ritorsioni del Sith_.

###<a name="understanding-the-data"></a>Informazioni sui dati

In modo appropriato, [Ricerche GroupLens] [ movielens] fornisce i dati di valutazione per i filmati in un formato compatibile con Mahout. Questi dati sono disponibili in spazio di archiviazione predefinito del cluster in `/HdiSamples/HdiSamples/MahoutMovieData`.

Esistono due file, `moviedb.txt` (informazioni sui filmati,) e `user-ratings.txt`. Il file ratings.txt utente viene utilizzato durante l'analisi, mentre moviedb.txt viene utilizzata per fornire informazioni testo descrittivo quando si visualizzano i risultati dell'analisi.

I dati contenuti nel ratings.txt utente presenta una struttura di `userID`, `movieID`, `userRating`, e `timestamp`, che indica classificazione di un filmato come elevata ogni utente. Ecco un esempio dei dati:


    196 242 3   881250949
    186 302 3   891717742
    22  377 1   878887116
    244 51  2   880606923
    166 346 1   886397596

##<a name="run-the-analysis"></a>Eseguire l'analisi

Per eseguire il processo di recommendation, usare il comando seguente:

    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp

> [AZURE.NOTE] Il processo può richiedere alcuni minuti e possono essere eseguite più MapReduce processi.

##<a name="view-the-output"></a>Visualizzare l'output

1. Al termine del processo, utilizzare il comando seguente per visualizzare l'output generato:

        hdfs dfs -text /example/data/mahoutout/part-r-00000

    L'output verrà visualizzata come segue:

        1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    La prima colonna è la `userID`. I valori contenuti in ' ["e"]' sono `movieId`:`recommendationScore`.

2. È possibile utilizzare l'output, insieme a moviedb.txt, per visualizzare altre informazioni da usare. Prima di tutto, è necessario copiare i file in locale utilizzando i comandi seguenti:

        hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
        hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .

    Questa operazione verrà copiato i dati di output in un file denominato **recommendations.txt** nella directory corrente, insieme ai file di dati di filmato.

3. Utilizzare il comando seguente per creare un nuovo script Python cercare i nomi dei filmati per i dati nell'output consigli:

        nano show_recommendations.py

    Quando si apre l'editor, utilizzare la seguente come il contenuto del file:

        #!/usr/bin/env python

        import sys

        if len(sys.argv) != 5:
                print "Arguments: userId userDataFilename movieFilename recommendationFilename"
                sys.exit(1)

        userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

        print "Reading Movies Descriptions"
        movieFile = open(movieFilename)
        movieById = {}
        for line in movieFile:
                tokens = line.split("|")
                movieById[tokens[0]] = tokens[1:]
        movieFile.close()

        print "Reading Rated Movies"
        userDataFile = open(userDataFilename)
        ratedMovieIds = []
        for line in userDataFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        ratedMovieIds.append((tokens[1],tokens[2]))
        userDataFile.close()

        print "Reading Recommendations"
        recommendationFile = open(recommendationFilename)
        recommendations = []
        for line in recommendationFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        movieIdAndScores = tokens[1].strip("[]\n").split(",")
                        recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
                        break
        recommendationFile.close()

        print "Rated Movies"
        print "------------------------"
        for movieId, rating in ratedMovieIds:
                print "%s, rating=%s" % (movieById[movieId][0], rating)
        print "------------------------"

        print "Recommended Movies"
        print "------------------------"
        for movieId, score in recommendations:
                print "%s, score=%s" % (movieById[movieId][0], score)
        print "------------------------"

    Premere **Ctrl + X**, **Y**e infine **INVIO** per salvare i dati.

3. Per rendere il file eseguibile, utilizzare il comando seguente:

        chmod +x show_recommendations.py

4. Eseguire lo script Python. Le operazioni seguenti si presuppone che ci si trova nella directory in cui sono stati scaricati tutti i file:

        ./show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt

    Questa operazione si presenterà suggerimenti generati per utente ID 4.

    * Il file **ratings.txt utente** viene utilizzato per recuperare filmati che l'utente ha valutato
    * Il file **moviedb.txt** viene utilizzato per recuperare i nomi dei filmati
    * **Recommendations.txt** viene utilizzato per recuperare i consigli filmato per questo utente

    L'output di questo comando sarà simile alla seguente:

        Reading Movies Descriptions
        Reading Rated Movies
        Reading Recommendations
        Rated Movies
        ------------------------
        Mimic (1997), rating=3
        Ulee's Gold (1997), rating=5
        Incognito (1997), rating=5
        One Flew Over the Cuckoo's Nest (1975), rating=4
        Event Horizon (1997), rating=4
        Client, The (1994), rating=3
        Liar Liar (1997), rating=5
        Scream (1996), rating=4
        Star Wars (1977), rating=5
        Wedding Singer, The (1998), rating=5
        Starship Troopers (1997), rating=4
        Air Force One (1997), rating=5
        Conspiracy Theory (1997), rating=3
        Contact (1997), rating=5
        Indiana Jones and the Last Crusade (1989), rating=3
        Desperate Measures (1998), rating=5
        Seven (Se7en) (1995), rating=4
        Cop Land (1997), rating=5
        Lost Highway (1997), rating=5
        Assignment, The (1997), rating=5
        Blues Brothers 2000 (1998), rating=5
        Spawn (1997), rating=2
        Wonderland (1997), rating=5
        In & Out (1997), rating=5
        ------------------------
        Recommended Movies
        ------------------------
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        Rock, The (1996), score=5.0
        ------------------------

##<a name="delete-temporary-data"></a>Eliminare i dati temporanei

Processi mahout non rimuovere dati temporanei create durante l'elaborazione del processo. Il `--tempDir` il processo di esempio per isolare i file temporanei in un percorso specifico per l'eliminazione semplice viene specificato. Per rimuovere i file temporanei, utilizzare il comando seguente:

    hdfs dfs -rm -f -r /temp/mahouttemp

> [AZURE.WARNING] Se si desidera eseguire nuovamente il comando, è necessario eliminare anche la directory di output. Per eliminare la directory, utilizzare il seguente:
>
> ```hdfs dfs -rm -f -r /example/data/mahoutout```

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato illustrato come utilizzare Mahout, scoprire altri modi di utilizzo dei dati in HDInsight:

* [Hive con HDInsight](hdinsight-use-hive.md)
* [Maialino con HDInsight](hdinsight-use-pig.md)
* [MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 
