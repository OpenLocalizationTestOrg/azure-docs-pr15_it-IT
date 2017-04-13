<properties
   pageTitle="Sviluppare processi Python MapReduce con HDInsight | Microsoft Azure"
   description="Informazioni su come creare ed eseguire i processi di MapReduce Python nei cluster basati su Linux HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="develop-python-streaming-programs-for-hdinsight"></a>Sviluppare Python streaming programmi per HDInsight

Hadoop fornisce un'API di flusso per MapReduce che consente di scrivere mappa e ridurre funzioni in lingue diverse da linguaggio. In questo articolo si imparerà a utilizzare Python per eseguire operazioni di MapReduce.

> [AZURE.NOTE] Mentre il codice Python in questo documento può essere utilizzato con un cluster HDInsight basato su Windows, la procedura descritta in questo documento è specifica di cluster basati su Linux.

In questo articolo si basa su informazioni ed esempi pubblicati da Michael Noll scrivere [Un'applicazione di MapReduce Hadoop in Python](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/).

##<a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questo articolo, è necessario quanto segue:

* Un Hadoop basati su Linux cluster HDInsight

* Un editor di testo

    > [AZURE.IMPORTANT] Editor di testo deve utilizzare nuova riga di fine riga. Se Usa CRLF, questo causerà errori durante l'esecuzione del processo di MapReduce in cluster basati su Linux HDInsight. Se non si è certi, utilizzare il passaggio facoltativo nella sezione [Eseguire MapReduce](#run-mapreduce) per convertire qualsiasi CRLF alla nuova riga.

* Per i client Windows PuTTY e PSCP. Queste utilità sono disponibili nella <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Pagina di Download PuTTY</a>.

##<a name="word-count"></a>Conteggio parole

In questo esempio, un conteggio di base in word verrà implementata utilizzando un mapping e riduttore. Il mapping degli suddivide frasi in singole parole e il riduttore aggrega le parole e conta per generare l'output.

Diagramma di flusso seguente viene illustrato cosa accade durante la mappa e ridurre le fasi.

![illustrazione di mappa ridurre](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

##<a name="why-python"></a>Perché Python?

Python è un linguaggio di programmazione generale di alto livello che consente ai concetti express in meno righe di codice rispetto a molte altre lingue. Sono recentemente era popolari scienziati dati per la lingua la creazione di prototipi perché la natura interpretata, digitare dinamico e sintassi elegante appropriato per lo sviluppo rapido delle applicazioni.

Python è installato in tutti i cluster HDInsight.

##<a name="streaming-mapreduce"></a>Flusso MapReduce

Hadoop consente di specificare un file che contiene la mappa e ridurre la logica utilizzata da un processo. Requisiti specifici per la mappa e ridurre logica sono:

* **Input**: la mappa e ridurre componenti devono leggere i dati di input da STDIN.

* **Output**: la mappa e ridurre componenti necessario scrivere i dati di output in STDOUT.

* **Formato dati**: I dati utilizzati e prodotto devono essere una coppia di chiave/valore, separata da un carattere di tabulazione.

Python facilmente è in grado di gestire questi requisiti utilizzando il modulo di **sistema** per leggere STDIN e utilizzo di **Stampa** per stampare in STDOUT. Attività rimanenti è semplicemente la formattazione dei dati con una linguetta (`\t`) carattere tra chiave e il valore.

##<a name="create-the-mapper-and-reducer"></a>Creare il mapping degli e riduttore

Il mapping degli e riduttore sono file di testo, in questo caso **mapper.py** e **reducer.py** (per mettere in evidenza che cosa). È possibile creare tali informazioni tramite l'editor di propria scelta.

###<a name="mapperpy"></a>Mapper.py

Creare un nuovo file denominato **mapper.py** e il codice seguente come contenuto:

    #!/usr/bin/env python

    # Use the sys module
    import sys

    # 'file' in this case is STDIN
    def read_input(file):
        # Split each line into words
        for line in file:
            yield line.split()

    def main(separator='\t'):
        # Read the data using read_input
        data = read_input(sys.stdin)
        # Process each words returned from read_input
        for words in data:
            # Process each word
            for word in words:
                # Write to STDOUT
                print '%s%s%d' % (word, separator, 1)

    if __name__ == "__main__":
        main()

Richiedere alcuni minuti di leggere il codice in modo da comprendere risultato.

###<a name="reducerpy"></a>REDUCER.py

Creare un nuovo file denominato **reducer.py** e il codice seguente come contenuto:

    #!/usr/bin/env python

    # import modules
    from itertools import groupby
    from operator import itemgetter
    import sys

    # 'file' in this case is STDIN
    def read_mapper_output(file, separator='\t'):
        # Go through each line
        for line in file:
            # Strip out the separator character
            yield line.rstrip().split(separator, 1)

    def main(separator='\t'):
        # Read the data using read_mapper_output
        data = read_mapper_output(sys.stdin, separator=separator)
        # Group words and counts into 'group'
        #   Since MapReduce is a distributed process, each word
        #   may have multiple counts. 'group' will have all counts
        #   which can be retrieved using the word as the key.
        for current_word, group in groupby(data, itemgetter(0)):
            try:
                # For each word, pull the count(s) for the word
                #   from 'group' and create a total count
                total_count = sum(int(count) for current_word, count in group)
                # Write to stdout
                print "%s%s%d" % (current_word, separator, total_count)
            except ValueError:
                # Count was not a number, so do nothing
                pass

    if __name__ == "__main__":
        main()

##<a name="upload-the-files"></a>Caricare i file

**Mapper.py** e **reducer.py** deve essere sul nodo principale del cluster prima è possibile eseguire. Il modo più semplice per caricarli consiste nell'utilizzare **scp** (**pscp** se si utilizza un client di Windows).

Dal client, nella stessa directory **mapper.py** e **reducer.py**, usare il comando seguente. Sostituire **nomeutente** con un utente SSH e **nome cluster** con il nome del cluster.

    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

I file viene copiata dal sistema locale il nodo principale.

> [AZURE.NOTE] Se è stata usata una password per proteggere l'account SSH, verrà richiesto per la password. Se è stata utilizzata una chiave SSH, è necessario utilizzare la `-i` parametro e il percorso per la chiave privata, ad esempio `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

##<a name="run-mapreduce"></a>Eseguire MapReduce

1. Connettersi al cluster utilizzando SSH:

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] Se è stata usata una password per proteggere l'account SSH, verrà richiesto per la password. Se è stata utilizzata una chiave SSH, è necessario utilizzare la `-i` parametro e il percorso per la chiave privata, ad esempio `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`.

2. (Facoltativo) Se è stato utilizzato un editor di testo che utilizza CRLF come la linea finale durante la creazione dei file mapper.py e reducer.py o non sapere cosa terminazioni di riga editor ottimali, usare i seguenti comandi per convertire le occorrenze di CRLF in mapper.py e reducer.py alla nuova riga.

        perl -pi -e 's/\r\n/\n/g' mappery.py
        perl -pi -e 's/\r\n/\n/g' reducer.py

2. Utilizzare il comando seguente per avviare il processo di MapReduce.

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasbs:///example/data/gutenberg/davinci.txt -output wasbs:///example/wordcountout

    Questo comando è composta dalle parti seguenti:

    * **Hadoop streaming.jar**: utilizzato quando si esegue le operazioni di MapReduce flusso. Interfaccia Hadoop con il codice MapReduce esterno specificati.

    * **-file**: Hadoop indica che i file specificati sono necessari per il processo di MapReduce e devono essere copiati in tutti i nodi di lavoro.

    * **-mapping**: indica il file da utilizzare come il mapping degli Hadoop.

    * **-riduttore**: indica il file da utilizzare come il riduttore Hadoop.

    * **-input**: file di input che è necessario contare le parole da.

    * **-output**: directory scritta per l'output.

        > [AZURE.NOTE] Questa directory verrà creata nel processo.

È necessario vedere un insieme di istruzioni di **informazioni** come iniziato il lavoro e infine l'operazione di **mappa** e **ridurre** visualizzato come percentuali.

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

Al termine, si riceverà informazioni sullo stato del processo.

##<a name="view-the-output"></a>Visualizzare l'output

Una volta completato il processo, utilizzare il comando seguente per visualizzare l'output:

    hdfs dfs -text /example/wordcountout/part-00000

Questa operazione deve essere visualizzato un elenco degli errori ortografici e quante volte la parola si è verificato. Di seguito è un esempio di dati di output:

    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2

##<a name="next-steps"></a>Passaggi successivi

Dopo avere appreso sull'utilizzo di trasmissione dei processi MapRedcue con HDInsight, utilizzare i collegamenti seguenti per esplorare altre modalità per lavorare con Azure HDInsight.

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Utilizzare maialino con HDInsight](hdinsight-use-pig.md)
* [Utilizzare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)
