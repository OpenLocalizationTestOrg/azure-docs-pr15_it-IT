<properties
   pageTitle="Utilizzare Python componenti in una topologia eccesso nella HDinsight | Microsoft Azure"
   description="Informazioni su come utilizzare componenti Python da con eccesso Apache su Azure HDInsight. Viene spiegato come utilizzare i componenti di Python da Java entrambi in base a e Clojure a seconda della topologia eccesso."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Sviluppare topologie Apache eccesso utilizzando Python su HDInsight

Apache eccesso supporta più lingue, anche consente di combinare i componenti di lingue diverse in una topologia. In questo documento si imparerà a utilizzare componenti Python nel linguaggio e basate su Clojure eccesso topologie su HDInsight.

##<a name="prerequisites"></a>Prerequisiti

* Python 2.7 o versione successiva

* Java JDK 1.7 o versione successiva

* [Leiningen](http://leiningen.org/)

##<a name="storm-multi-language-support"></a>Eccesso del supporto multilingue

Eccesso è stato progettato per funzionare con componenti scritti utilizzando qualsiasi linguaggio di programmazione, ma è necessario che i componenti per imparare a utilizzare la [definizione di Thrift per eccesso](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Per Python, viene fornito un modulo come parte del progetto eccesso Apache che consente di interagire con eccesso. È possibile trovare il modulo in [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Poiché il Apache è un processo di linguaggio che viene eseguita in Java Virtual Machine (JVM), componenti scritti in altre lingue vengono eseguiti come sottoprocessi. Bit eccesso in esecuzione in JVM comunica con questi sottoprocessi utilizzando JSON messaggi inviati stdin/stdout. Ulteriori informazioni sulle comunicazioni tra componenti disponibili nella documentazione [protocollo multilingue](https://storm.apache.org/documentation/Multilang-protocol.html) .

###<a name="the-storm-module"></a>Il modulo eccesso

Il modulo eccesso (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py), fornisce i bit necessari per creare componenti Python che funzionano con eccesso.

In questo modo, quali `storm.emit` per generare tuple, e `storm.logInfo` per scrivere i log. È consigliabile leggere il file e comprendere le caratteristiche.

##<a name="challenges"></a>Problemi

Uso del modulo __storm.py__ , è possibile creare spouts Python che utilizzano dati e i bulloni di elaborano dati, ma la definizione di topologia eccesso complessiva che collega le comunicazioni tra componenti ancora scritto utilizzando Java o Clojure. Inoltre, se si usa di linguaggio, è necessario creare anche i componenti di linguaggio fungono da interfaccia ai componenti Python.

Inoltre, poiché cluster eccesso eseguito in modalità distribuita, è necessario assicurarsi che tutti i moduli necessari per i componenti di Python sono disponibili in tutti i nodi di lavoro del cluster. Eccesso non fornisce un modo semplice per eseguire questa operazione per le risorse multilingue, è necessario includere tutte le dipendenze come parte del file vaso per la topologia o installare manualmente le dipendenze su ciascun nodo lavoro cluster.

###<a name="java-vs-clojure-topology-definition"></a>Definizione di linguaggio e Clojure topologia

Di due metodi per definire una topologia, Clojure è di gran lunga il più facile/molto semplice come è possibile direttamente componenti python perchè nella definizione della topologia. Per le definizioni di topologia basato su Java, è necessario definire anche componenti di linguaggio che gestiscono ad esempio la dichiarazione di campi nelle tuple restituite dai componenti Python.

Entrambi i metodi sono descritti in questo documento insieme a esempi di progetti.

##<a name="python-components-with-a-java-topology"></a>Componenti di Python con una topologia di linguaggio

> [AZURE.NOTE] In questo esempio è disponibile in [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) nella directory __JavaTopology__ . Si tratta di un progetto Maven in base a. Se si ha familiarità con Maven, vedere [basato su Java sviluppare topologie con eccesso Apache su HDInsight](hdinsight-storm-develop-java-topology.md) per ulteriori informazioni sulla creazione di un progetto Maven per una topologia eccesso.

Una topologia basato su Java che utilizza Python (o altri componenti di lingua JVM) viene visualizzato inizialmente l'utilizzo dei componenti di linguaggio; ma se si osserva in ognuna delle Java spouts/bulloni, si noterà codice simile al seguente:

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Si tratta in linguaggio richiama Python ed esegue lo script che contiene la logica bulloni effettivo. Linguaggio spouts/bulloni (ad esempio), è sufficiente dichiarare i campi della tupla viene emesso dal componente Python sottostante.

I file Python effettivi vengono archiviati nel `/multilang/resources` directory in questo esempio. Il `/multilang` __pom.xml__fa riferimento directory:

<resources>
    <resource>
        <!-- Where the Python bits are kept -->
        <directory>${basedir} / multilang</directory>
    </resource>
</resources>

Sono inclusi tutti i file di `/multilang` cartella nel vaso generati da questo progetto.

> [AZURE.IMPORTANT] Si noti che solo consente di specificare il `/multilang` directory e non `/multilang/resources`. Eccesso prevede risorse non JVM in un `resources` directory, in modo che viene cercato internamente già. Posizionamento dei componenti in questa cartella consente di riferimento solo in base al nome nel codice di linguaggio. Ad esempio `super("python", "countbolt.py");`. È possibile pensare a questo in eccesso cui vengono visualizzati i `resources` directory come la radice (/) quando si accede a risorse multilingue.
>
> Per il progetto di esempio, il `storm.py` è incluso nella `/multilang/resources` directory.

###<a name="build-and-run-the-project"></a>Creare ed eseguire il progetto

Per eseguire il progetto in locale, usare il comando seguente Maven per generare ed eseguire in modalità locale:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Usare ctrl + c per interrompere il processo.

Per distribuire il progetto in un cluster di HDInsight in esecuzione Apache eccesso, utilizzare la procedura seguente:

1. Creare un vaso uber:

        mvn package

    Verrà creato un file denominato __WordCount - 1.0 SNAPSHOT.jar__ nel `/target` directory per il progetto.

2. Caricare il file vaso cluster Hadoop utilizzando uno dei metodi seguenti:

    * Per cluster HDInsight __basati su Linux__ : usare `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` per copiare il file vaso a cluster, sostituendo nomeutente con il nome utente SSH e nome cluster con il nome di cluster HDInsight.

        Al termine del durante il caricamento di file, connettersi al cluster utilizzando SSH e iniziare a utilizzare la topologia`storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`

    * Per i cluster HDInsight __basato su Windows__ : connettersi a Dashboard eccesso facendo clic su HTTPS://CLUSTERNAME.azurehdinsight.net/ nel browser. Sostituire nome cluster con il proprio nome cluster HDInsight e specificare il nome dell'amministratore e una password quando richiesto.

        Utilizzare la maschera, eseguire le operazioni seguenti:

        * __File Jar__: selezionare __Sfoglia__, quindi selezionare il file __SNAPSHOT.jar di 1.0 WordCount__
        * __Nome della classe__: immettere`com.microsoft.example.WordCount`
        * __Altri parametri__: immettere un nome descrittivo, ad esempio `wordcount` per identificare la topologia

        Infine, selezionare __Invia__ per avviare la topologia.

> [AZURE.NOTE] Una volta avviato, viene eseguita una topologia eccesso fino a interruzione (abbattuti). Per interrompere la topologia, utilizzare la `storm kill TOPOLOGYNAME` comando dalla riga di comando (SSH sessione in un cluster di Linux ad esempio) o tramite l'interfaccia utente eccesso, selezionare la topologia e quindi selezionare il pulsante di __interruzione__ .

##<a name="python-components-with-a-clojure-topology"></a>Componenti di Python con una topologia Clojure

> [AZURE.NOTE] In questo esempio è disponibile in [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) nella directory __ClojureTopology__ .

Questa topologia è stata creata utilizzando [Leiningen](http://leiningen.org) per [creare un nuovo progetto Clojure](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). Dopo che sono state apportate le seguenti modifiche al progetto supporto temporaneo:

* __Project.CLJ__: aggiunta di dipendenze per eccesso ed esclusioni per gli elementi che possono causare un problema quando distribuiti nel server di HDInsight.
* __risorse/risorse__: Leiningen crea un valore predefinito `resources` directory, ma vengono visualizzati i file memorizzati nella tabella a venga aggiunta alla radice del file vaso creato dal progetto ed eccesso prevede i file in una sottodirectory denominata `resources`. In modo che è stata aggiunta una sottodirectory e i file Python vengono archiviati `resources/resources`. In fase di esecuzione, questo verrà trattato come la radice (/) per l'accesso ai componenti Python.
* __src/WordCount/Core.CLJ__: il file contiene la definizione della topologia e viene fatto riferimento dal file __project.clj__ . Per ulteriori informazioni sull'utilizzo di Clojure per definire una topologia eccesso, vedere [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

###<a name="build-and-run-the-project"></a>Creare ed eseguire il progetto

__Per creare ed eseguire il progetto in locale__, utilizzare il comando seguente:

    lein clean, run

Per interrompere la topologia, premere __Ctrl + C__.

__Per creare un uberjar e distribuire a HDInsight__, utilizzare la procedura seguente:

1. Creare un uberjar che contiene la topologia e dipendenze necessarie:

        lein uberjar

    Verrà creato un nuovo file denominato `wordcount-1.0-SNAPSHOT.jar` nel `target\uberjar+uberjar` directory.
    
2. Utilizzare uno dei metodi seguenti per distribuire ed eseguire la topologia a un cluster di HDInsight:

    * __HDInsight basati su Linux__
    
        1. Copiare il file all'uso di nodo principale cluster HDInsight `scp`. Per esempio:
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            Sostituire nomeutente con un utente SSH per il cluster e nome cluster con il proprio nome cluster HDInsight.
            
        2. Una volta il file è stato copiato al cluster, utilizzare SSH per connettersi al cluster e inviare il processo. Per informazioni sull'utilizzo di SSH con HDInsight, vedere una delle operazioni seguenti:
        
            * [Usare SSH con basati su Linux HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [Usare SSH con basati su Linux HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. Una volta connessa, utilizzare le operazioni seguenti per avviare la topologia:
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __HDInsight basato su Windows__
    
        1. Connettersi a Dashboard eccesso facendo clic su HTTPS://CLUSTERNAME.azurehdinsight.net/ nel browser. Sostituire nome cluster con il proprio nome cluster HDInsight e specificare il nome dell'amministratore e una password quando richiesto.

        2. Utilizzare la maschera, eseguire le operazioni seguenti:

            * __File Jar__: selezionare __Sfoglia__, quindi selezionare il file __SNAPSHOT.jar di 1.0 wordcount__
            * __Nome della classe__: immettere`wordcount.core`
            * __Altri parametri__: immettere un nome descrittivo, ad esempio `wordcount` per identificare la topologia

            Infine, selezionare __Invia__ per avviare la topologia.

> [AZURE.NOTE] Una volta avviato, viene eseguita una topologia eccesso fino a interruzione (abbattuti). Per interrompere la topologia, utilizzare la `storm kill TOPOLOGYNAME` comando dalla riga di comando (SSH sessione in un cluster Linux) o tramite l'interfaccia utente eccesso, selezionare la topologia e quindi selezionare il pulsante di __interruzione__ .

##<a name="next-steps"></a>Passaggi successivi

In questo documento è stato illustrato come utilizzare componenti Python da una topologia eccesso. Vedere i documenti per altri modi per usare Python con HDInsight seguenti:

* [Come usare Python per lo streaming MapReduce processi](hdinsight-hadoop-streaming-python.md)
* [Come usare Python utente definiti funzioni utente maialino e Hive](hdinsight-python.md)
