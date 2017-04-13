<properties
    pageTitle="Creare le funzionalità per i dati in un cluster di Hadoop l'utilizzo delle query Hive | Microsoft Azure"
    description="Esempi di query Hive che generano caratteristiche nei dati archiviati in un cluster di Azure HDInsight Hadoop."
    services="machine-learning"
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


#<a name="create-features-for-data-in-an-hadoop-cluster-using-hive-queries"></a>Creare le funzionalità per i dati in un cluster di Hadoop l'utilizzo delle query Hive

In questo documento viene illustrato come creare caratteristiche per i dati memorizzati in un cluster di Azure HDInsight Hadoop l'utilizzo delle query Hive. Queste query Hive utilizzano incorporata Hive funzioni definite dall'utente (funzioni definite dall'utente), gli script per il quale sono disponibili.

Operazioni necessarie per creare caratteristiche possono essere elevati di memoria. Le prestazioni delle query Hive più critica in tal caso e possono essere migliorate per determinati parametri per l'ottimizzazione. L'ottimizzazione dei parametri illustrata nella sezione finale.

Esempi di query che vengono presentati sono specifici per i [Dati di andata e ritorno Taxi Roma](http://chriswhong.com/open-data/foil_nyc_taxi/) scenari sono disponibili anche in [Github archivio](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Queste query già schema dati specificato e sono pronte per essere inviato per l'esecuzione. Nella sezione finale vengono descritte anche parametri che gli utenti possono ottimizzare in modo che le prestazioni delle query Hive possono essere migliorate.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]Questo **menu** fornisce collegamenti ad argomenti che descrivono come creare caratteristiche per i dati in ambienti diversi. Questa attività è un passaggio di [Team dati scienza processo (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="prerequisites"></a>Prerequisiti
In questo articolo si presuppone che sia:

* Creare un account di archiviazione Azure. Se è necessario istruzioni, vedere [creare un account di archiviazione Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Il provisioning di un cluster Hadoop personalizzato con il servizio HDInsight.  Se è necessario istruzioni, vedere [Personalizzare Azure HDInsight Hadoop cluster per Analitica avanzate](machine-learning-data-science-customize-hadoop-cluster.md).
* I dati sono stati caricati alle tabelle Hive cluster di Azure HDInsight Hadoop. In caso contrario, seguire [creare e caricare i dati alle tabelle Hive](machine-learning-data-science-move-hive-tables.md) per caricare dati alle tabelle Hive prima di tutto.
* Accesso remoto al cluster attivato. Se è necessario istruzioni, vedere [accesso Hadoop Cluster testa nodi](machine-learning-data-science-customize-hadoop-cluster.md#headnode).


##<a name="hive-featureengineering"></a>Generazione di funzionalità

In questa sezione sono descritti diversi esempi di modi in cui è possano generare caratteristiche l'utilizzo delle query Hive. Dopo aver creato caratteristiche aggiuntive, è possibile aggiungerli come colonne alla tabella esistente o creare una nuova tabella con le caratteristiche aggiuntive e le chiavi primarie, che è possibile collegare quindi con la tabella originale. Ecco gli esempi presentati:

1. [Frequenza in base a caratteristica generazione](#hive-frequencyfeature)
2. [Rischi di variabili per categoria nella classificazione binaria](#hive-riskfeature)
3. [Estrarre caratteristiche da Datetime Field](#hive-datefeatures)
4. [Estrarre caratteristiche campo di testo](#hive-textfeatures)
5. [Calcolare distanza tra le coordinate GPS](#hive-gpsdistance)

###<a name="hive-frequencyfeature"></a>Frequenza in base a caratteristica generazione

Può essere utile calcolare la frequenza dei livelli di una variabile per categoria o la frequenza di alcune combinazioni di livelli da più variabili per categoria. Gli utenti possono usare il seguente script per calcolare le frequenza:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


###<a name="hive-riskfeature"></a>Rischi di variabili per categoria nella classificazione binaria

In binaria classificazione, è necessario convertire variabili per categoria non numerici in caratteristiche numerici quando i modelli usati solo richiedere caratteristiche numerici. In tal caso, sostituire ogni livello non numerici con un rischio numerico. In questa sezione, questo webinar mostra alcune query Hive generico calcolare i valori di rischio (probabilità log) di una variabile per categoria.


        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

In questo esempio, le variabili `smooth_param1` e `smooth_param2` siano impostati per arrotondare i valori di rischio calcolati a partire dai dati. Rischi dispone di un intervallo compreso tra -Inf e Inf. Rischi > 0 indica che la probabilità che l'obiettivo è uguale a 1 è maggiore di 0,5.

Dopo i rischi per la tabella viene calcolata, gli utenti possono assegnare valori di rischio a una tabella mediante l'unione con la tabella di rischio. La query di unione Hive è stata illustrata nella sezione precedente.

###<a name="hive-datefeatures"></a>Estrarre caratteristiche da Datetime Fields

Hive viene fornito con un set di funzioni definite dall'utente per l'elaborazione di campi datetime. Nell'Hive, il formato datetime predefinito è "gg / MM-AAAA 00:00:00" ('1970 01 / 01 12:21:32 ", ad esempio). In questa sezione è visualizzare esempi in cui estraggono il giorno del mese, il mese di un campo di data e ora e altri esempi in cui convertire una stringa di data in un formato diverso da quello predefinito formattare il formato predefinito per una stringa di Data.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Questa query Hive presuppone che la *& #60; campo Data e ora >* è il formato di data e ora predefiniti.

Se un campo di data e ora non è presente nel formato predefinito, è necessario convertire il campo di data e ora in timestamp Unix prima di tutto e quindi si converte il timbro dell'ora Unix in una stringa di data in formato predefinito. Una volta datetime predefiniti formato, gli utenti possono applicare datetime incorporata funzioni definite dall'utente per estrarre caratteristiche.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

In questa query, se la *& #60; campo Data e ora >* è il modello come *26/03/2015 12:04:39*, il *' & #60; motivo del campo Data e ora >'* dovrebbe essere `'MM/dd/yyyy HH:mm:ss'`. Per provare l'effetto, gli utenti possono eseguire

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

*Hivesampletable* in questa query preinstallato in tutti i cluster di Azure HDInsight Hadoop per impostazione predefinita quando vengono effettuato il provisioning i cluster.


###<a name="hive-textfeatures"></a>Estrarre caratteristiche da campi di testo

Quando la tabella Hive contiene un campo di testo che contiene una stringa di parole racchiuse tra gli spazi, la query seguente estrae la lunghezza della stringa e il numero di parole nella stringa.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

###<a name="hive-gpsdistance"></a>Calcolare distanza tra insiemi di coordinate GPS

La query indicata in questa sezione può essere applicata direttamente ai dati di andata e ritorno Taxi Roma. Lo scopo di questa query è illustrato come applicare un funzioni matematiche incorporato in Hive per generare le funzionalità.

I campi utilizzati in questa query sono le coordinate GPS dei percorsi di ritiro e dropoff, denominate *ritiro\_longitudine*, *ritiro\_latitudine*, *dropoff\_longitudine*, e *dropoff\_latitudine*. La query che calcolano la distanza diretta tra le coordinate ritiro e dropoff sono:

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

Sono disponibili le equazioni matematiche che calcolano la distanza tra due coordinate GPS nel sito di <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Tipo mobile script</a> autore Peter Lapisu. Nel suo Javascript, la funzione `toRad()` è sufficiente *lat_or_lon*pi greco/180*, che converte i gradi in radianti. In questo caso, *lat_or_lon * è latitudine o longitudine. Poiché Hive non fornisce la funzione `atan2`, ma fornisce la funzione `atan`, il `atan2` funzione implementata dal `atan` funzione nella query Hive precedente utilizzando la definizione fornita in <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Creare l'area di lavoro](./media/machine-learning-data-science-create-features-hive/atan2new.png)

L'elenco completo degli Hive incorporata funzioni definite dall'utente sono disponibili nella sezione **Funzioni** <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>).  

## <a name="tuning"></a>Argomenti avanzati: consente di ottimizzare Hive parametri per migliorare la velocità delle Query

Le impostazioni predefinite dei parametri di Hive cluster potrebbe non essere adatto per le query Hive e i dati che l'elaborazione di query. In questa sezione vengono illustrati alcuni parametri che è possono ottimizzare gli utenti che consentono di migliorare le prestazioni delle query Hive. Gli utenti devono aggiungere il parametro ottimizzazione delle query prima le query di elaborazione dei dati.

1. **Spazio heap linguaggio**: per le query che includono partecipare grandi set di dati o l'elaborazione di record lunghi, **heap spazio quasi esaurito** corrisponde a uno di errore comune. Può essere ottimizzata mediante l'impostazione di parametri *mapreduce.map.java.opts* e *mapreduce.task.io.sort.mb* per i valori desiderati. Ecco un esempio:

        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;


    Questo parametro viene allocata 4GB di spazio heap Java e consente di ordinamento più efficiente assegnando più memoria per renderla. È consigliabile per la riproduzione con queste allocazioni se sono presenti errori relativi allo spazio heap qualsiasi processo.

2. **Dimensione del blocco DFS** : questo parametro imposta l'unità minima di dati archiviati nel file system. Ad esempio, se la dimensione del blocco DFS è 128MB, quindi tutti i dati di dimensioni minore e fino a 128MB vengono archiviati in un unico blocco, mentre quelli maggiori di 128MB reso disponibile blocchi aggiuntivi. Se si sceglie una dimensione di blocco molto piccolo, costi generali di grandi dimensioni in Hadoop poiché il nodo name previsti elaborare molte altre richieste per trovare il blocco pertinenti relative al file. Impostazione consigliata quando relative gigabyte (o più grande) dati:

        set dfs.block.size=128m;

3. **Ottimizzazione partecipare operazione in Hive** : mentre le operazioni di join in framework mapping e riduzione in genere eseguita in fase di ridurre, in alcuni casi è possono ottenere profitti enorme pianificando join in fase di mappa (denominata anche "mapjoins"). Per indirizzare Hive per eseguire questa operazione ogni volta che è possibile, è possibile impostare:

        set hive.auto.convert.join=true;

4. **Specificare il numero di temporaneo hive** : Hadoop mentre consente all'utente di impostare il numero di riduttori, il numero di temporaneo è in genere non è impostata dall'utente. Una presa che consente a che un certo controllo questo numero, è necessario scegliere variabili Hadoop, *mapred.min.split.size* e *mapred.max.split.size* come le dimensioni di ogni attività mappa dipende dalla:

        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

    In genere, il valore predefinito di *mapred.min.split.size* è uguale a 0, che *mapred.max.split.size* **Long.MAX** e di *dfs.block.size* 64 MB. Come è possibile vedere, dimensioni dei dati, questi parametri "impostando" per l'ottimizzazione loro consente di ottimizzare il numero di temporaneo utilizzato.

5. Di seguito sono riportate alcune altre ulteriori **Opzioni avanzate** per ottimizzare le prestazioni di Hive. Questi consentono di impostare la memoria allocata per eseguire il mapping e ridurre le attività e può essere utili per l'ottimizzazione delle prestazioni. Tenere presente che non può essere maggiore della dimensione di memoria fisica di ogni nodo lavoro cluster Hadoop *mapreduce.reduce.memory.mb* .

        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;
