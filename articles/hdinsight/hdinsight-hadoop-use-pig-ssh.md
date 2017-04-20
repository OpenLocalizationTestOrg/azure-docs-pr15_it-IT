<properties
   pageTitle="Utilizzare Hadoop suino con SSH in un cluster HDInsight | Microsoft Azure"
   description="Informazioni su come connettersi a un cluster basati su Linux Hadoop con SSH e quindi utilizzare il comando suino da eseguire in modo interattivo in alfabeto latino le istruzioni o come processo batch."
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

#<a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Esecuzione dei processi suino in un cluster basato su Linux con il comando suino (SSH)

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

In questo documento verrà descritto il processo di connessione a un cluster HDInsight Azure basate su Linux utilizzando Secure Shell (SSH), l'utilizzo del comando suino per l'esecuzione in modalità interattiva, caratteri latini le istruzioni o come processo batch.

Il linguaggio di programmazione da latino consente di descrivere trasformazioni che si applicano i dati di input per produrre l'output desiderato.

> [AZURE.NOTE] Se si ha già familiarità con i server basati su Linux Hadoop, ma sono una novità di HDInsight, vedere [Suggerimenti HDInsight basati su Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Prerequisiti di

Per completare la procedura descritta in questo articolo, è necessario quanto segue.

* Un cluster basati su Linux HDInsight (Hadoop in HDInsight).

* Un client SSH. Linux, Unix e Mac OS deve disporre di un un client SSH. Gli utenti di Windows necessario scaricare un client, ad esempio [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Collegarsi con SSH

Connettersi al nome di dominio completo (FQDN) del cluster HDInsight utilizzando il comando SSH. Il nome FQDN è il nome assegnato al cluster, quindi **. azurehdinsight.net**. Ad esempio, le operazioni seguenti potrebbe connettersi a un cluster denominato **myhdinsight**.

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se la chiave di certificato per l'autenticazione SSH specificate** al momento della creazione del cluster HDInsight, potrebbe essere necessario specificare il percorso della chiave privata nel sistema del client.

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se è stata specificata una password per autenticazione SSH** durante la creazione del cluster HDInsight, è necessario immettere la password quando richiesto.

Per ulteriori informazioni sull'utilizzo di SSH con HDInsight, vedere [Utilizzo SSH con basati su Linux Hadoop su HDInsight da Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>PuTTY (client basato su Windows)

Windows non è disponibile un client SSH incorporato. È consigliabile utilizzare **PuTTY**, che può essere scaricato da [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per ulteriori informazioni sull'utilizzo di PuTTY, vedere [Utilizzo SSH con basati su Linux Hadoop in HDInsight da Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="pig"></a>Utilizzare il comando suino

2. Una volta connessi, per avviare l'interfaccia della riga di comando le (CLI) utilizzando il comando seguente.

        pig

    Dopo l'esecuzione, dovrebbe comparire un `grunt>` prompt dei comandi.

3. Immettere l'istruzione seguente.

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    Questo comando Carica il contenuto del file sample.log in registri. È possibile visualizzare il contenuto del file con le operazioni seguenti.

        DUMP LOGS;

4. Successivamente, trasformare i dati da applicare a un'espressione regolare per estrarre il livello di registrazione da ogni record utilizzando il seguente.

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    È possibile utilizzare **DUMP** per visualizzare i dati dopo la trasformazione. In questo caso, utilizzare `DUMP LEVELS;`.

5. Continuare ad applicare trasformazioni utilizzando le istruzioni seguenti. Utilizzare `DUMP` per visualizzare il risultato della trasformazione dopo ogni passaggio.

    <table>
    <tr>
    <th>Istruzione</th><th>Funzione</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = livelli di filtro da LOGLEVEL non è null.</td><td>Rimuove le righe che contengono un valore null per il livello di registrazione e memorizzare i risultati in FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = FILTEREDLEVELS gruppo da LOGLEVEL;</td><td>Raggruppa le righe dal livello di registrazione e memorizzare i risultati in GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FREQUENZE = foreach GROUPEDLEVELS generare gruppo come LOGLEVEL, COUNT (FILTEREDLEVELS. LOGLEVEL) come CONTARE;</td><td>Crea un nuovo set di dati che contiene ogni registro univoco livello specificato e il numero di volte si verifica. Viene memorizzato in frequenze.</td>
    </tr>
    <tr>
    <td>RISULTATO = ordine frequenze dal conteggio desc,</td><td>Ordina i livelli di registrazione in base al numero (decrescente) e li archivia nel risultato.</td>
    </tr>
    </table>

6. È inoltre possibile salvare i risultati di una trasformazione utilizzando il `STORE` istruzione. Ad esempio, il seguente salva il `RESULT` alla directory **/example/data/pigout** il contenitore dell'archivio predefinito per il cluster.

        STORE RESULT into 'wasbs:///example/data/pigout';

    > [AZURE.NOTE] I dati vengono archiviati nella directory specificata nel file denominato **nnnnn parte**. Se la directory esiste già, si riceverà un errore.

7. Per uscire dal prompt dei comandi noioso, immettere l'istruzione seguente.

        QUIT;

###<a name="pig-latin-batch-files"></a>File batch da latina

È possibile utilizzare il comando suino anche per l'esecuzione in alfabeto latino da contenuti in un file.

3. Dopo aver chiuso la richiesta noioso, utilizzare il seguente comando per pipe STDIN in un file denominato **pigbatch.pig**. Verrà creato il file nella directory principale per l'account di cui che si è connessi a per la sessione SSH.

        cat > ~/pigbatch.pig

4. Digitare o incollare le righe seguenti e quindi utilizzare Ctrl + D al termine.

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. Per eseguire il file **pigbatch.pig** utilizzando il comando suino, utilizzare il seguente.

        pig ~/pigbatch.pig

    Al termine del processo batch, verrà visualizzato il seguente output deve essere uguale a come quando utilizzano `DUMP RESULT;` nei passaggi precedenti.

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="summary"></a>Riepilogo

Come si può notare, il comando suino consente in modo interattivo consentono di eseguire operazioni MapReduce con alfabeto latino da, e su come eseguire istruzioni archiviate in un file batch.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali sul suino HDInsight.

* [Utilizzare suino con Hadoop su HDInsight](hdinsight-use-pig.md)

Per informazioni su altri modi per lavorare con Hadoop in HDInsight.

* [Utilizzare Hive con Hadoop su HDInsight](hdinsight-use-hive.md)

* [Utilizzare MapReduce con Hadoop su HDInsight](hdinsight-use-mapreduce.md)
