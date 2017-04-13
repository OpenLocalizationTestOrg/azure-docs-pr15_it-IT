<properties
   pageTitle="Utilizzare Hadoop maialino con Desktop remoto in HDInsight | Microsoft Azure"
   description="Informazioni su come utilizzare il comando maialino per eseguire istruzioni sull'alfabeto latino maialino da una connessione Desktop remoto a un cluster basato su Windows Hadoop in HDInsight."
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

#<a name="run-pig-jobs-from-a-remote-desktop-connection"></a>Eseguire i processi di maialino da una connessione Desktop remoto

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Questo documento offre una procedura dettagliata per il comando maialino per eseguire un cluster basato su Windows HDInsight istruzioni sull'alfabeto latino maialino da una connessione Desktop remoto. Maialino latino consente di creare applicazioni MapReduce descrivendo le trasformazioni di dati, invece di eseguire il mapping e ridurre le funzioni.

In questo documento, viene illustrato come

##<a id="prereq"></a>Prerequisiti

Per completare la procedura descritta in questo articolo, è necessario quanto segue.

* Un cluster basato su Windows HDInsight (Hadoop in HDInsight)

* Un computer client con Windows 10, Windows 8 o Windows 7

##<a id="connect"></a>Connettersi con Desktop remoto

Attivare Desktop remoto per il cluster HDInsight e quindi connettersi seguendo le istruzioni disponibili in [connessione a cluster HDInsight mediante RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="pig"></a>Usare il comando maialino

2. Dopo avere una connessione Desktop remoto, avviare la **riga di comando Hadoop** mediante l'icona sul desktop.

2. Per avviare il comando maialino, utilizzare le operazioni seguenti:

        %pig_home%\bin\pig

    Verrà visualizzata con una `grunt>` prompt dei comandi.

3. Immettere l'istruzione seguente:

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    Questo comando Carica il contenuto del file sample.log nel file di log. È possibile visualizzare il contenuto del file mediante il comando seguente:

        DUMP LOGS;

4. Trasformare i dati tramite l'applicazione di un'espressione regolare per estrarre il livello di registrazione da ogni record:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    È possibile utilizzare **Dettagli** per visualizzare i dati dopo la trasformazione. In questo caso, `DUMP LEVELS;`.

5. Continuare ad applicare trasformazioni usando le istruzioni seguenti. Usare `DUMP` per visualizzare il risultato della trasformazione di dopo ogni passaggio.

    <table>
    <tr>
    <th>Istruzione</th><th>Risultato</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = livelli del filtro da LOGLEVEL non è null.</td><td>Rimuove le righe che contengono un valore null per il livello di registrazione e memorizzare i risultati in FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = FILTEREDLEVELS gruppo da LOGLEVEL;</td><td>Raggruppare le righe dal livello di registrazione e memorizzare i risultati in GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FREQUENZA = foreach GROUPEDLEVELS generare gruppo come LOGLEVEL, conta. numeri (FILTEREDLEVELS. LOGLEVEL) come CONTARE;</td><td>Crea un nuovo set di dati che contiene ogni log univoco valore livello e quante volte si verifica. Questo è archiviato in frequenza</td>
    </tr>
    <tr>
    <td>RISULTATO = ordine di frequenza da conteggio desc;</td><td>Ordina i livelli del registro per conteggio (decrescente) e archivia nel risultato</td>
    </tr>
    </table>

6. È inoltre possibile salvare i risultati di una trasformazione utilizzando il `STORE` istruzione. Ad esempio, il seguente comando salverà la `RESULT` alla directory **/example/data/pigout** nel contenitore di spazio di archiviazione predefinito per il cluster:

        STORE RESULT into 'wasbs:///example/data/pigout'

    > [AZURE.NOTE] I dati vengono archiviati nella directory specificata nei file denominato **nnnnn parte**. Se la directory esiste già, si riceverà un messaggio di errore.

7. Per uscire dal prompt noioso, immettere l'istruzione seguente.

        QUIT;

###<a name="pig-latin-batch-files"></a>File batch sull'alfabeto latino maialino

È anche possibile utilizzare il comando maialino per eseguire latino maialino contenuto in un file.

3. Dopo aver chiuso la richiesta di noioso, aprire **il blocco note** e creare un nuovo file denominato **pigbatch.pig** nella directory **% PIG_HOME %** .

4. Digitare o incollare le righe seguenti nel file **pigbatch.pig** e quindi salvarlo:

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. Utilizzare le operazioni seguenti per eseguire il file **pigbatch.pig** utilizzando il comando maialino.

        pig %PIG_HOME%\pigbatch.pig

    Dopo avere completato la procedura batch, verrà visualizzato l'output seguente, che deve essere uguale quando utilizza `DUMP RESULT;` in precedenza:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="summary"></a>Riepilogo

Come si può notare, il comando maialino consente di in modo interattivo eseguire operazioni MapReduce oppure processi maialino latino vengono memorizzati in un file batch.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su maialino in HDInsight:

* [Utilizzare maialino con Hadoop in HDInsight](hdinsight-use-pig.md)

Per informazioni su altri modi è possibile utilizzare le Hadoop su HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

* [Utilizzare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)
