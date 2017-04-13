<properties
    pageTitle="Note per i componenti Hadoop Azure HDInsight | Microsoft Azure"
    description="Note sulla versione più recente e le versioni dei componenti Hadoop per Azure HDInsight. È possibile ottenere suggerimenti di sviluppo e dettagli per Hadoop, eccesso Apache e HBase."
    services="hdinsight"
    documentationCenter=""
    editor="cgronlun"
    manager="jhubbard"
    authors="nitinme"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="nitinme"/>


# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Note sulla versione per i componenti Hadoop Azure HDInsight

## <a name="notes-for-10262016-release-of-r-server-on-hdinsight"></a>Note per 26/10/2016 versione Server R HDInsight

- R Server di provisioning cluster HDInsight è stata semplificata.
- R Server HDInsight è ora disponibile come regolare HDInsight "R Server" cluster tipo e non è più installato come applicazione HDInsight distinta. Il nodo del bordo e i file binari R Server vengono ora effettuato il provisioning come parte della distribuzione cluster R Server. Migliora la velocità e l'affidabilità di provisioning. Modello prezzo per Server R viene aggiornata automaticamente.
- Prezzo di tipo cluster Server R ora dipende dal livello Standard prezzo più prezzo supplemento R Server. Livello Premium sarà ora riservato per le caratteristiche Premium disponibili per i tipi di grafico diverso e non verranno utilizzata per tipo di cluster di Server R. Questa modifica non influisce su prezzi efficace di Server R, si trasforma solo la modalità di presentazione gli addebiti nella fattura. Tutti i cluster di Server R esistenti continueranno a funzionare e modelli di ARM continuerà a funzionare fino a quando non si noti anche. **Si consiglia di aggiornare le distribuzioni script per utilizzare nuovo modello ARM.**


## <a name="notes-for-08302016-release-of-r-server-on-hdinsight"></a>Note per 30/08/2016 versione Server R HDInsight

I numeri di versione completa di cluster basati su Linux HDInsight distribuiti in questa versione:

|HDI |Versione cluster HDI   |HDP |Compilazione HDP   |Compilazione Ambari |
|----|----------------------|----|------------|-------------|
|3.2 |3.2.1000.0.8268980    |2.2 |2.2.9.1-19  |2.2.1.12-4   |
|3.3 |3.3.1000.0.8268980    |2.3 |2.3.3.1-25  |2.2.1.12-4   |
|3.4 |3.4.1000.0.8269383    |2,4 |2.4.2.4-5   |2.2.1.12-4   |

I numeri di versione completa di cluster basato su Windows HDInsight distribuiti in questa versione:

|HDI |Versione cluster HDI   |HDP |Compilazione HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.1033.2559206   |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.1033.2559206    |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.1033.2559206    |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.1033.2559206    |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.1033.2559206    |2.3 |2.3.3.1-25    |

## <a name="notes-for-08172016-release-of-r-server-on-hdinsight"></a>Note per 17/08/2016 versione Server R HDInsight

- R Server 8.0.5 – principalmente una versione di correzione. Vedere le [Note sulla versione Server R](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes) per altre informazioni. 
- Pacchetto di AzureML del nodo di bordo: [questo pacchetto R](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) Abilita i modelli di R la pubblicazione e utilizzata in un servizio web ML Azure.  Vedere la sezione ["rendono operativi di un modello di"](hdinsight-hadoop-r-server-overview.md#operationalize-a-model) dell'articolo ["Panoramica di R Server in HDInsight"](hdinsight-hadoop-r-server-overview.md) per altre informazioni.
- Linux le relazioni tra le [prime 100 più popolari R i pacchetti](https://github.com/metacran/cranlogs) – questi Linux le dipendenze dei pacchetti vengono ora preinstallati.  
- Opzione per utilizzare repo CRAN quando aggiunta R pacchetti per i nodi di dati. Vedere la sezione ["Pacchetti di installazione R"](hdinsight-hadoop-r-server-get-started.md#install-r-packages) dell'articolo ["Introduzione all'utilizzo di Server R HDInsight"](hdinsight-hadoop-r-server-get-started.md) per altre informazioni.
- Maggiore affidabilità di Server R provisioning quando vengono creati i cluster.


## <a name="notes-for-08012016-release-of-hdinsight"></a>Note sulla versione 08/01/2016 di HDInsight

I numeri di versione completa di cluster basati su Linux HDInsight distribuiti in questa versione:

|HDI |Versione cluster HDI   |HDP |Compilazione HDP   |Compilazione Ambari |
|----|----------------------|----|------------|-------------|
|3.2 |3.2.1000.0.8028416    |2.2 |2.2.9.1-19  |2.2.1.12-4   |
|3.3 |3.3.1000.0.8028416    |2.3 |2.3.3.1-25  |2.2.1.12-4   |
|3.4 |3.4.1000.0.8053402    |2,4 |2.4.2.4-5   |2.2.1.12-4   |

I numeri di versione completa di cluster basato su Windows HDInsight distribuiti in questa versione:

|HDI |Versione cluster HDI   |HDP |Compilazione HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.1005.2488842   |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.1005.2488842    |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.1005.2488842    |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.1005.2488842    |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.1005.2488842    |2.3 |2.3.3.1-25    |

In questa versione sono i seguenti aggiornamenti.

| Titolo                                           | Descrizione                                          | Area interessata (ad esempio servizio, componente o SDK) | Tipo di grafico (ad esempio motori, Hadoop, HBase o eccesso) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Modifiche apportate ai cluster 3.4 HDInsight | Il valore predefinito per le configurazioni hive seguenti vengono modificati per migliorare le prestazioni <ul><li>`hive.vectorized.execution.reduce.enabled=true`</li><li>`hive.tez.min.partition.factor=1f`</li><li>`hive.tez.max.partition.factor=3f`</li><li>`tez.shuffle-vertex-manager.min-src-fraction=0.9`</li><li>`tez.shuffle-vertex-manager.max-src-fraction=0.95`</li><li>`tez.runtime.shuffle.connect.timeout= 30000`</li></ul>| Servizio    | Tutti| N/D|
| In questa versione sono incluse seguenti correzioni | HIVE 13632, HIVE-12897,HIVE-12907,HIVE-12908,HIVE-12988,HIVE-13510,HIVE-13572,HIVE-13716,HIVE-13726,HIVE-12505,HIVE-13632,HIVE-13661,HIVE-13705,HIVE-13743,HIVE-13810,HIVE-13857,HIVE-13902,HIVE-13911,HIVE-13933| Servizio    | Tutti| N/D

## <a name="notes-for-07142016-release-of-hdinsight"></a>Note sulla versione 14/07/2016 di HDInsight

I numeri di versione completa di cluster basati su Linux HDInsight distribuiti in questa versione:

|HDI |Versione cluster HDI   |HDP |Compilazione HDP   |Compilazione Ambari |
|----|----------------------|----|------------|-------------|
|3.2 |3.2.1000.0.7932505    |2.2 |2.2.9.1-11  |2.2.1.12-2   |
|3.3 |3.3.1000.0.7932505    |2.3 |2.3.3.1-18  |2.2.1.12-2   |
|3.4 |3.4.1000.0.7933003    |2,4 |2.4.2.0     |2.2.1.12-2   |

I numeri di versione completa di cluster basato su Windows HDInsight distribuiti in questa versione:

|HDI |Versione cluster HDI   |HDP |Compilazione HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.989.2441725    |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.989.2441725     |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.989.2441725     |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.989.2441725     |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.989.2441725     |2.3 |2.3.3.1-21    |

## <a name="notes-for-07072016-release-of-hdinsight"></a>Note sulla versione 07/07/2016 di HDInsight

I numeri di versione completa di cluster basati su Linux HDInsight distribuiti in questa versione:

|HDI |Versione cluster HDI   |HDP |Compilazione HDP   |
|----|----------------------|----|------------|
|3.2 |3.2.1000.0.7864996    |2.2 |2.2.9.1-11  |
|3.3 |3.3.1000.0.7864996    |2.3 |2.3.3.1-18  |
|3.4 |3.4.1000.0.7861906    |2,4 |2.4.2.0     |

I numeri di versione completa di cluster basato su Windows HDInsight distribuiti in questa versione:

|HDI |Versione cluster HDI   |HDP |Compilazione HDP     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.977.2413853    |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.977.2413853     |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.977.2413853     |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.977.2413853     |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.977.2413853     |2.3 |2.3.3.1-21    |

In questa versione sono i seguenti aggiornamenti.

| Titolo                                           | Descrizione                                          | Area interessata (ad esempio servizio, componente o SDK) | Tipo di grafico (ad esempio motori, Hadoop, HBase o eccesso) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| [Strumenti di HDInsight per IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md) | Plug-in IntelliJ IDEA per i cluster HDInsight Spark ora è integrato con Azure Toolkit per IntelliJ. Supporta v2.9.1 Azure SDK, SDK linguaggio più recenti e include tutte le funzionalità di autonoma HDInsight Plugin per IntelliJ.| Strumenti    | Motori| N/D|
| [Strumenti di HDInsight per Eclisse](hdinsight-apache-spark-eclipse-tool-plugin.md) | Azure Toolkit per Eclisse supporta ora cluster HDInsight Spark. In questo modo le caratteristiche seguenti. <ul><li>Creare e scrivere un'applicazione di motori facilmente in Scala e Java con prima classe supporta l'utilizzo di IntelliSense, formattazione automatica, il controllo degli errori e così via.</li><li>Verificare l'applicazione di motori in locale.</li><li>Inviare i processi a cluster HDInsight Spark e recuperare i risultati.</li><li>Accedere a Azure e accedere a tutti i cluster di motori associati le proprie sottoscrizioni Azure.</li><li>Passare tutte le risorse di archiviazione associato del cluster HDInsight Spark.</li></ul>| Strumenti    | Motori| N/D

A partire da questa versione, abbiamo modificato il criterio di patch del sistema operativo guest per i cluster basati su Linux HDInsight. L'obiettivo del nuovo criterio è di ridurre il numero di riavvio a causa di patch. Il nuovo criterio continuerà a patch macchine () cluster Linux ogni lunedì o giovedì partire dal 12 AM UTC in modo sfalsato tra i nodi del cluster specificato. Tuttavia, qualsiasi macchine Virtuali specificato verranno riavviato solo al massimo una volta ogni 30 giorni a causa di patch del sistema operativo guest. Inoltre, il primo riavvio per un cluster appena creato non accade prima di 30 giorni dalla data di creazione cluster.

>[AZURE.NOTE] Queste modifiche vengono applicate a cluster appena creato uguale o maggiore di questa versione.

## <a name="notes-for-06062016-release-of-hdinsight"></a>Note sulla versione 06/06/2016 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

|HDP    |Versione HDI    |Versione ad  |Numero di Build Ambari    |Numero di Build HDP|
|-------|---------------|---------------|-----------------------|----------------|
|2.3    |3.3.1000.0.7702215|    1.5.2|  2.2.1.8-2|  2.3.3.1-18|
|2,4    |3.4.1000.0.7702224|    1.6.1|  2.2.1.8-2|  2.4.2.0|


In questa versione sono i seguenti aggiornamenti.

| Titolo                                           | Descrizione                                          | Area interessata (ad esempio servizio, componente o SDK) | Tipo di grafico (ad esempio motori, Hadoop, HBase o eccesso) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Motori in HDInsight sono generalmente disponibile | In questa versione porta miglioramenti della disponibilità, scalabilità e la produttività per aprire origine ad Apache su HDInsight. <ul><li>Avanguardia disponibilità contratto di servizio del 99,9%, semplificando così il adatto per enterprise carichi di lavoro complessi.</li><li>Livello di archiviazione Scalable utilizzando archivio Lake dati di Azure.</li><li>Strumenti di produttività per ogni fase di sviluppo e l'esplorazione dei dati. I blocchi appunti Jupyter con personalizzate kernel ad attivare esplorazione dei dati interattive, l'integrazione con i dashboard di Business Intelligence come Power BI, Tableau e Qlik è utile per la condivisione dei dati veloci e la segnalazione continua, plug-in IntelliJ è affidabile per lo sviluppo di elementi di codice a lungo termine e il debug.</li></ul>| Servizio    | Motori| N/D|
| Strumenti di HDInsight per IntelliJ | Si tratta di un plug-in IntelliJ IDEA per i cluster HDInsight Spark. In questo modo le caratteristiche seguenti.<ul><li>Creare e scrivere un'applicazione di motori facilmente in Scala e Java con prima classe supporta l'utilizzo di IntelliSense, formattazione automatica, il controllo degli errori e così via.</li><li>Verificare l'applicazione di motori in locale.</li><li>Inviare i processi a cluster HDInsight Spark e recuperare i risultati.</li><li>Accedere a Azure e accedere a tutti i cluster di motori associati le proprie sottoscrizioni Azure.</li><li>Passare tutte le risorse di archiviazione associato del cluster HDInsight Spark.</li><li>Esplorare tutti la cronologia processi e le informazioni sul processo per il cluster HDInsight Spark.</li><li>Eseguire il debug processi motori in remoto dal computer desktop.</li></ul>| Strumenti    | Motori| N/D

## <a name="notes-for-05132016-release-of-hdinsight"></a>Note sulla versione 05/13/2016 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight (Windows) 2.1.10.875.2159884 (1.3.12.0-01795 HDP - invariato)
* HDInsight (Windows) 3.0.6.875.2159884 (2.0.13.0-2117 HDP - invariato)
* HDInsight (Windows) 3.1.4.922.2266903 (2.1.15.0-2374 HDP - invariato)
* HDInsight (Windows) 3.2.7.922.2266903 (HDP 2.2.9.1-11)
* HDInsight (Windows) 3.3.0.922.2266903 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.2.1000.0.7565644 (HDP 2.2.9.1-11)
* HDInsight (Linux) 3.3.1000.0.7565644 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.4.1000.0.7548380 (HDP 2.4.2.0)

In questa versione sono i seguenti aggiornamenti.

| Titolo                                           | Descrizione                                          | Area interessata (ad esempio servizio, componente o SDK) | Tipo di grafico (ad esempio motori, Hadoop, HBase o eccesso) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Riguardo aggiornamento versione e altre correzioni di bug | In questa versione aggiornata la versione ad cluster HDInsight alla 1.6.1 e consente di risolvere altri bug| Servizio    | Motori| N/D

## <a name="notes-for-04112016-release-of-hdinsight"></a>Note sulla versione 11/04/2016 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight (Windows) 2.1.10.889.2191206 (1.3.12.0-01795 HDP - invariato)
* HDInsight (Windows) 3.0.6.889.2191206 (2.0.13.0-2117 HDP - invariato)
* HDInsight (Windows) 3.1.4.889.2191206 (2.1.15.0-2374 HDP - invariato)
* HDInsight (Windows) 3.2.7.889.2191206 (HDP 2.2.9.1-10)
* HDInsight (Windows) 3.3.0.889.2191206 (HDP 2.3.3.1-16-invariato)
* HDInsight (Linux) 3.2.1000.0.7339916 (HDP 2.2.9.1-10)
* HDInsight (Linux) 3.3.1000.0.7339916 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.4.1000.0.7338911 (HDP 2.4.1.1-3)
* SDK 1.5.8

In questa versione sono i seguenti aggiornamenti.

| Titolo                                           | Descrizione                                          | Area interessata (ad esempio servizio, componente o SDK) | Cluster di tipo (ad esempio Hadoop, HBase o eccesso) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Problemi di aggiornamento metastore personalizzato per 3.4 HDI | La creazione di cluster verrebbe se è stata usata una metastore personalizzato utilizzato in una versione inferiore di un altro cluster HDInsight. Si tratta a causa di un errore di script aggiornamento ora corretti| Creazione di cluster    | Tutti | N/D
| Inserire il ripristino di un arresto anomalo | Offre flessibilità stato processo per qualsiasi processo inviato tramite inserire il | Affidabilità | Motori su Linux| N/D
| Contenuto Jupyter HA | Consente di salvare e caricare Jupyter contenuto del blocco appunti da e verso l'account di archiviazione associato al cluster. Per ulteriori informazioni, vedere [disponibili per i blocchi appunti Jupyter. X](hdinsight-apache-spark-jupyter-notebook-kernels.md).| Blocchi appunti | Motori su Linux| N/D
| Rimozione di hiveContext nei blocchi appunti Jupter | Usare `%%sql` strumento invece di `%%hive` speciale. SqlContext corrisponde al hiveContext. Per ulteriori informazioni, vedere [disponibili per i blocchi appunti Jupyter. X](hdinsight-apache-spark-jupyter-notebook-kernels.md)| Blocchi appunti    | Motori cluster su Linux| N/D
| Rimozione di versioni precedenti di motori | Una versione precedente ad 1.3.1 verrà rimossi dal servizio in 5/31 | Servizio | Motori cluster in Windows | N/D

## <a name="notes-for-03292016-release-of-hdinsight"></a>Note sulla versione 29/03/2016 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight (Windows) 2.1.10.875.2159884 (1.3.12.0-01795 HDP - invariato)
* HDInsight (Windows) 3.0.6.875.2159884 (2.0.13.0-2117 HDP - invariato)
* HDInsight (Windows) 3.1.4.875.2159884 (2.1.15.0-2374 HDP - invariato)
* HDInsight (Windows) 3.2.7.875.2159884 (2.2.9.1-7 HDP - invariato)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (2.2.9.1-8 HDP - invariato)
* HDInsight (Linux) 3.3.1000.0.7193255 (2.3.3.1-7 HDP - invariato)
* HDInsight (Linux) 3.4.1000.0.7195842 (HDP 2.4.1.0-327)
* SDK 1.5.8

In questa versione sono i seguenti aggiornamenti.

| Titolo                                           | Descrizione                                          | Area interessata (ad esempio servizio, componente o SDK) | Cluster di tipo (ad esempio Hadoop, HBase o eccesso) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versione 3.4 HDInsight aggiunta e versioni HDP aggiornate per tutti i cluster HDInsight | Con questa versione, abbiamo aggiunto v3.4 HDInsight (in base HDP 2,4) e sono aggiornati anche altre versioni HDP. Note sulla versione 2,4 HDP sono disponibili [qui](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html) e altre informazioni sull'HDInsight versioni possono essere trovato [qui](hdinsight-component-versioning.md).| Servizio    | Tutti i cluster Linux| N/D
| HDInsight Premium | HDInsight è ora disponibile in due categorie - Standard e Premium. HDInsight Premium è attualmente in anteprima e disponibile solo per Hadoop e motori cluster su Linux. Per ulteriori informazioni vedere [di seguito](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium).| Servizio    | Hadoop e motori su Linux| N/D
| Server Microsoft R | HDInsight Premium fornisce Microsoft R Server che può essere incluso con i cluster Hadoop e motori su Linux. Per ulteriori informazioni, vedere [Panoramica di R Server HDInsight](hdinsight-hadoop-r-server-overview.md).| Servizio    | Hadoop e motori su Linux| N/D
| Motori 1.6.0 | 3.4 HDInsight cluster includono ora ad 1.6.0| Servizio    | Motori cluster su Linux| N/D
| Miglioramenti di blocco appunti Jupyter | I blocchi appunti Jupyter disponibili con i cluster di motori ora forniscono ad altre x. Includono anche miglioramenti come utilizzo di % % speciale, visualizzazione automatica e integrazione con le raccolte di visualizzazione Python (ad esempio matplotlib). Per ulteriori informazioni, vedere [disponibili per i blocchi appunti Jupyter. X](hdinsight-apache-spark-jupyter-notebook-kernels.md). | Servizio | Motori cluster su Linux | N/D

## <a name="notes-for-03222016-release-of-hdinsight"></a>Note sulla versione 22/03/2016 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight (Windows) 2.1.10.875.2159884 (1.3.12.0-01795 HDP - invariato)
* HDInsight (Windows) 3.0.6.875.2159884 (2.0.13.0-2117 HDP - invariato)
* HDInsight (Windows) 3.1.4.875.2159884 (2.1.15.0-2374 HDP - invariato)
* HDInsight (Windows) 3.2.7.875.2159884 (2.2.9.1-7 HDP - invariato)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (2.2.9.1-8 HDP - invariato)
* HDInsight (Linux) 3.3.1000.0.7193255 (2.3.3.1-7 HDP - invariato)
* SDK 1.5.8

In questa versione sono i seguenti aggiornamenti.

| Titolo                                           | Descrizione                                          | Area interessata (ad esempio servizio, componente o SDK) | Cluster di tipo (ad esempio Hadoop, HBase o eccesso) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versioni HDInsight aggiornate per tutti i cluster HDInsight | Con questa versione, aggiornamenti versioni HDInsight per tutti i cluster HDInsight| Servizio    | Tutti| N/D


## <a name="notes-for-03102016-release-of-hdinsight"></a>Note sulla versione 10/03/2016 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight (Windows) 2.1.10.859.2123216 (1.3.12.0-01795 HDP - invariato)
* HDInsight (Windows) 3.0.6.859.2123216 (2.0.13.0-2117 HDP - invariato)
* HDInsight (Windows) 3.1.4.859.2123216 (2.1.15.0-2374 HDP - invariato)
* HDInsight (Windows) 3.2.7.859.2123216 (HDP 2.2.9.1-7)
* HDInsight (Windows) 3.3.0.859.2123216 (2.3.3.1-5 HDP - invariato)
* HDInsight (Linux) 3.2.1000.7076817 (HDP 2.2.9.1-8)
* HDInsight (Linux) 3.3.1000.7076817 (HDP 2.3.3.1-7)
* SDK 1.5.8

In questa versione sono i seguenti aggiornamenti.

| Titolo                                           | Descrizione                                          | Area interessata (ad esempio servizio, componente o SDK) | Cluster di tipo (ad esempio Hadoop, HBase o eccesso) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versioni HDInsight aggiornate per tutti i cluster HDInsight | Con questa versione, aggiornamenti versioni HDInsight per tutti i cluster HDInsight| Servizio    | Tutti| N/D

## <a name="notes-for-01272016-release-of-hdinsight"></a>Note sulla versione 27/01/2016 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight (Windows) 2.1.10.817.2028315 (1.3.12.0-01795 HDP - invariato)
* HDInsight (Windows) 3.0.6.817.2028315 (2.0.13.0-2117 HDP - invariato)
* HDInsight (Windows) 3.1.4.817.2028315 (2.1.15.0-2374 HDP - invariato)
* HDInsight (Windows) 3.2.7.817.2028315 (HDP 2.2.9.1-1)
* HDInsight (Windows) 3.3.0.817.2028315 (2.3.3.1-5 HDP - invariato)
* HDInsight (Linux) 3.2.1000.4072335 (HDP 2.2.9.1-1)
* HDInsight (Linux) 3.3.1000.4072335 (HDP 2.3.3.1-1)
* SDK 1.5.8

In questa versione sono i seguenti aggiornamenti.

| Titolo                                           | Descrizione                                          | Area interessata (ad esempio servizio, componente o SDK) | Cluster di tipo (ad esempio Hadoop, HBase o eccesso) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versioni HDInsight aggiornate per tutti i cluster HDInsight | Con questa versione, aggiornamenti versioni HDInsight per tutti i cluster HDInsight| Servizio    | Tutti| N/D

## <a name="notes-for-12022015-release-of-hdinsight"></a>Note sulla versione 12/02/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight (Windows) 2.1.10.763.1931434 (1.3.12.0-01795 HDP - invariato)
* HDInsight (Windows) 3.0.6.763.1931434 (2.0.13.0-2117 HDP - invariato)
* HDInsight (Windows) 3.1.4.763.1931434 (2.1.15.0-2374 HDP - invariato)
* HDInsight (Windows) 3.2.7.763.1931434 (2.2.7.1-34 HDP - invariato)
* HDInsight (Windows) 3.3.1000.0 (HDP 2.3.3.1-5)
* HDInsight (Linux) 3.2.1000.0.6392801 (2.2.7.1-34 HDP - invariato)
* HDInsight (Linux) 3.3.1000.0 (HDP 2.3.3.0-3039)
* SDK 1.5.8

In questa versione sono i seguenti aggiornamenti.

| Titolo                                           | Descrizione                                          | Area interessata (ad esempio servizio, componente o SDK) | Cluster di tipo (ad esempio Hadoop, HBase o eccesso) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versione 3.3 HDInsight aggiunta e versioni HDP aggiornate per tutti i cluster HDInsight | Con questa versione, abbiamo aggiunto 3.3 HDInsight (in base HDP 2.3) e sono aggiornati anche altre versioni HDP. Note sulla versione 2.3 HDP sono disponibili [qui](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html) e altre informazioni sull'HDInsight versioni possono essere trovato [qui](hdinsight-component-versioning.md).| Servizio    | Tutti| N/D

## <a name="notes-for-11302015-release-of-hdinsight"></a>Note sulla versione 30/11/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight (Windows) 2.1.10.757.1923908 (1.3.12.0-01795 HDP - invariato)
* HDInsight (Windows) 3.0.6.757.1923908 (2.0.13.0-2117 HDP - invariato)
* HDInsight (Windows) 3.1.4.757.1923908 (2.1.15.0-2374 HDP - invariato)
* HDInsight (Windows) 3.2.7.757.1923908 (HDP 2.2.7.1-34)
* HDInsight (Linux) 3.2.1000.0.6392801 (HDP 2.2.7.1-34)
* SDK 1.5.8

In questa versione sono i seguenti aggiornamenti.

| Titolo                                           | Descrizione                                          | Area interessata (ad esempio servizio, componente o SDK) | Cluster di tipo (ad esempio Hadoop, HBase o eccesso) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versioni HDInsight aggiornate per tutti i cluster HDInsight e versioni HDP per i cluster HDInsight 3,2 (Windows e Linux) | Con questa versione sono state aggiornate le versioni HDInsight e HDP | Servizio    | Tutti| N/D


## <a name="notes-for-10272015-release-of-hdinsight"></a>Note sulla versione 27/10/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight (Windows) 2.1.10.726.1866228 (1.3.12.0-01795 HDP - invariato)
* HDInsight (Windows) 3.0.6.726.1866228 (2.0.13.0-2117 HDP - invariato)
* HDInsight (Windows) 3.1.4.726.1866228 (2.1.15.0-2374 HDP - invariato)
* HDInsight (Windows) 3.2.7.726.1866228 (HDP 2.2.7.1-33)
* HDInsight (Linux) 3.2.1000.0.6035701 (HDP 2.2.7.1-33)
* SDK 1.5.8

In questa versione sono i seguenti aggiornamenti.

| Titolo                                           | Descrizione                                          | Area interessata (ad esempio servizio, componente o SDK) | Cluster di tipo (ad esempio Hadoop, HBase o eccesso) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versioni HDInsight aggiornate per tutti i cluster HDInsight (Windows e Linux) | Con questa versione sono state aggiornate le versioni HDInsight e HDP | Servizio    | Tutti| N/D
| Fisso Jupyter per Windows i cluster con i cluster di lettere maiuscole | Cluster che aveva nomi DNS specificati in lettere maiuscole avuto problemi di blocchi appunti Jupyter a causa di un controllo di richiesta di origine. Lo strumento Correggi è stato per modificare il nome DNS per la configurazione del Jupyter in lettere minuscole. | Servizio    | Motori HDInsight (Windows)| N/D


## <a name="notes-for-10202015-release-of-hdinsight"></a>Note sulla versione 20/10/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.716.1846990 (Windows) (1.3.12.0-01795 HDP - invariato)
* HDInsight 3.0.6.716.1846990 (Windows) (2.0.13.0-2117 HDP - invariato)
* HDInsight 3.1.4.716.1846990 (Windows) (HDP 2.1.16.0-2374)
* HDInsight 3.2.7.716.1846990 (Windows) (HDP 2.2.7.1-0004)
* HDInsight 3.2.1000.0.5930166 (Linux) (HDP 2.2.7.1-0004)
* SDK 1.5.8

In questa versione sono i seguenti aggiornamenti.

| Titolo                                           | Descrizione                                          | Area interessata (ad esempio servizio, componente o SDK) | Cluster di tipo (ad esempio Hadoop, HBase o eccesso) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versione HDP predefinita impostata su HDP 2.2 | La versione predefinita per i cluster di HDInsight Windows viene modificata in HDP 2.2. HDInsight versione 3.2 (HDP 2.2) sono in genere disponibili sin in febbraio 2015. Questa modifica capovolge solo la versione di cluster predefinita, quando non è stata effettuata una selezione esplicita durante il provisioning cluster tramite il portale di Azure, i cmdlet di PowerShell o SDK. | Servizio    | Tutti| N/D                  |
|Le modifiche in formato del nome macchina virtuale per la distribuzione di più HDInsight su Linux cluster in un'unica rete virtuale | Supporto per la distribuzione di più cluster HDInsight Linux in un'unica rete virtuale viene aggiunto in questa versione. Durante il processo, il formato dei nomi di macchina virtuale del cluster è stato modificato da headnode\*, workernode\* e zookeepernode\* a hn\*, wn\*e zk\* rispettivamente. Non è consigliabile impostare una dipendenza diretta il formato dei nomi di macchina virtuale, poiché si tratta di soggetti a modifiche. Utilizzare "hostname -f" sul computer locale o Ambari APIs per determinare l'elenco degli host e il mapping dei componenti di host. È possibile trovare altre informazioni su [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md) e [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md). | Servizio | HDInsight cluster su Linux | N/D |
| Modifiche alla configurazione | A questo punto sono abilitate le configurazioni seguenti per cluster HDInsight 3.1: <ul><li>tez.yarn.ATS.Enabled e yarn.log.server.url. In questo modo il Server di sequenza temporale di applicazioni e registro del server per poter usare i registri.</li></ul>Per i cluster HDInsight 3.2, sono state modificate le configurazioni seguenti: <ul><li>MapReduce.fileoutputcommitter.Algorithm.Version è stata impostata su 2. In questo modo utilizzo della versione V2 del FileOutputCommitter.</li></ul> | Servizio | Tutti | N/D |


## <a name="notes-for-09092015-release-of-hdinsight"></a>Note sulla versione 09/09/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.675.1768697 (1.3.12.0-01795 HDP - invariato)
* HDInsight 3.0.6.675.1768697 (2.0.13.0-2117 HDP - invariato)
* HDInsight 3.1.4.675.1768697 (2.1.15.0-2334 HDP - invariato)
* HDInsight 3.2.6.675.1768697 (2.2.6.1-0012 HDP - invariato)
* SDK 1.5.8

In questa versione sono i seguenti aggiornamenti.

| Titolo                                           | Descrizione                                          | Area interessata (ad esempio servizio, componente o SDK) | Cluster di tipo (ad esempio Hadoop, HBase o eccesso) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versioni HDInsight aggiornate per tutti i cluster HDInsight | Con questa versione sono state aggiornate le versioni HDInsight | Servizio    | Tutti| N/D                  |

## <a name="notes-for-07312015-release-of-hdinsight"></a>Note sulla versione 31/07/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.640.1695824 (1.3.12.0-01795 HDP - invariato)
* HDInsight 3.0.6.640.1695824 (2.0.13.0-2117 HDP - invariato)
* HDInsight 3.1.4.640.1695824 (2.1.15.0-2334 HDP - invariato)
* HDInsight 3.2.6.640.1695824 (2.2.6.1-0012 HDP - invariato)
* SDK 1.5.8

In questa versione sono i seguenti aggiornamenti.

| Titolo                                           | Descrizione                                          | Area interessata (ad esempio servizio, componente o SDK) | Cluster di tipo (ad esempio Hadoop, HBase o eccesso) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Correggere motori del flusso di lavoro di cluster nodo nuovamente immagini | Correzione di un problema che causa motori nodi cluster per non recupera dopo la nuova immagine | Servizio    | Motori| N/D                  |


## <a name="notes-for-07312015-release-of-hdinsight"></a>Note sulla versione 31/07/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.635.1684502 (1.3.12.0-01795 HDP - invariato)
* HDInsight 3.0.6.635.1684502 (2.0.13.0-2117 HDP - invariato)
* HDInsight 3.1.4.635.1684502 (2.1.15.0-2334 HDP - invariato)
* HDInsight 3.2.6.635.1684502 (2.2.6.1-0012 HDP - invariato)
* SDK 1.5.8

In questa versione sono i seguenti aggiornamenti.

| Titolo                                           | Descrizione                                          | Area interessata (ad esempio servizio, componente o SDK) | Cluster di tipo (ad esempio Hadoop, HBase o eccesso) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versioni HDInsight aggiornate per tutti i cluster HDInsight | Con questa versione sono state aggiornate le versioni HDInsight | Servizio    | Tutti| N/D                  |


## <a name="notes-for-07072015-release-of-hdinsight"></a>Note sulla versione 07/07/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.610.1630216 (1.3.12.0-01795 HDP - invariato)
* HDInsight 3.0.6.610.1630216 (2.0.13.0-2117 HDP - invariato)
* HDInsight 3.1.4.610.1630216 (2.1.15.0-2334 HDP - invariato)
* HDInsight 3.2.4.610.1630216 (HDP 2.2.6.1-0012)
* SDK 1.5.8


In questa versione sono i seguenti aggiornamenti.

| Titolo                                           | Descrizione                                          | Area interessata (ad esempio servizio, componente o SDK) | Cluster di tipo (ad esempio Hadoop, HBase o eccesso) | JIRA (se applicabile) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versioni HDP aggiornate per i cluster di 3,2 HDInsight | Con questa versione 3.2 HDInsight distribuisce HDP 2.2.6.1-0012 | Servizio    | Tutti                                                 | N/D                  |


## <a name="notes-for-06262015-release-of-hdinsight"></a>Note sulla versione 26/06/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.601.1610731 (1.3.12.0-01795 HDP - invariato)
* HDInsight 3.0.6.601.1610731 (2.0.13.0-2117 HDP - invariato)
* HDInsight 3.1.4.601.1610731 (2.1.15.0-2334 HDP - invariato)
* HDInsight 3.2.4.601.1610731 (HDP 2.2.6.1-0011)
* SDK 1.5.8


In questa versione sono i seguenti aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Cluster di tipo (ad esempio Hadoop, HBase o eccesso)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Versioni HDP aggiornate per i cluster di 3,2 HDInsight</td>
<td>Con questa versione 3.2 HDInsight distribuisce HDP 2.2.6.1</td>
<td>Servizio</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

</table>

## <a name="notes-for-06182015-release-of-hdinsight"></a>Note della versione di HDInsight 18/06/2015

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.596.1601657 (1.3.12.0-01795 HDP - invariato)
* HDInsight 3.0.6.596.1601657 (2.0.13.0-2117 HDP - invariato)
* HDInsight 3.1.4.596.1601657 (HDP 2.1.15.0-2334)
* HDInsight 3.2.4.596.1601657 (HDP 2.2.6.1-0002)
* SDK 1.5.8


In questa versione sono i seguenti aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Cluster di tipo (ad esempio Hadoop, HBase o eccesso)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Porte HTTPS aggiuntive aperte</td>
<td>Il servizio cloud aperta 5 porte 8001 a 8005 cluster ad esempio in https://<clustername>.azurehdinsight.net:8001/. Le richieste di questi URL vengono autenticate utilizzando lo stesso meccanismo di password di autenticazione di base come porta 443. Queste porte associare alla stessa porta headnode attivo. Azioni di script possono essere utilizzate per rendere il servizio clienti ascolto queste porte headnode e traceroute all'esterno del cluster.</td>
<td>Servizio cloud</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Problema di casuale MapReduce intermittente for 3,2 HDInsight</td>
<td>Correggere una condizione di competizione rari e intermittente nella mappa ridurre casuale sui cluster di grandi dimensioni con errori di attività occasionale. Per ulteriori informazioni, vedere <a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE 6361</a> .</td>
<td>Principali Hadoop</td>
<td>Tutti</td>
<td><a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE 6361</a></td>
</tr>

<tr>
<td>Passare alla più recente linguaggio Azure SDK 2.2 per HDInsight 3,2</td>
<td>Spostato alla versione più recente di Azure SDK per Java utilizzato dal driver WASB. Note sulla versione per lo stesso sono disponibili all'https://github.com/Azure/azure-storage-java/blob/master/ChangeLog.txt SDK più recente di ha alcuni correzioni.</td>
<td>Principali Hadoop</td>
<td>Tutti</td>
<td><a href="https://issues.apache.org/jira/browse/HADOOP-11959" target="_blank">HADOOP 11959</a></td>
</tr>

<tr>
<td>Passare alla HDP 2.1.15 per i cluster 3.1 HDInsight</td>
<td>Note sulla versione Hortonworks per la versione sono disponibili <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.15-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.15.html" target="_blank">qui</a>.</td>
<td>HDP</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

</table>

## <a name="notes-for-06042015-release-of-hdinsight"></a>Note sulla versione 06/04/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.583.1575584 (1.3.12.0-01795 HDP - invariato)
* HDInsight 3.0.6.583.1575584 (2.0.13.0-2117 HDP - invariato)
* HDInsight 3.1.3.583.1575584 (2.1.12.1-0003 HDP - invariato)
* HDInsight 3.2.4.583.1575584 (HDP 2.2.6.1-1)
* SDK 1.5.8


In questa versione sono i seguenti aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Cluster di tipo (ad esempio Hadoop, HBase o eccesso)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Risolvere per errore di 502 gateway non valido per i cluster eccesso</td>
<td>In questa versione consente di correggere un errore che interessano l'invio di processi API che generano il sito Web sia verso il basso dopo il riavvio.</td>
<td>Servizio</td>
<td>Eccesso</td>
<td>N/D</td>
</tr>

</table>

## <a name="notes-for-06012015-release-of-hdinsight"></a>Note sulla versione 06/01/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.577.1563827 (1.3.12.0-01795 HDP - invariato)
* HDInsight 3.0.6.577.1563827 (2.0.13.0-2117 HDP - invariato)
* HDInsight 3.1.3.577.1563827 (2.1.12.1-0003 HDP - invariato))
* HDInsight 3.2.4.577.1563827 (2.2.6.0-2800 HDP - invariato)
* SDK 1.5.8


In questa versione sono i seguenti aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Cluster di tipo (ad esempio Hadoop, HBase o eccesso)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Varie correzioni di bug</td>
<td>In questa versione risolve bug correlati alle cluster il provisioning.</td>
<td>Servizio</td>
<td>Tutti i tipi di grafico</td>
<td>N/D</td>
</tr>

</table>

## <a name="notes-for-05272015-release-of-hdinsight"></a>Note sulla versione 27/05/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 3.2.4.570.1554102 (HDP 2.2.6.0-2800)
* Le altre versioni di cluster e SDK non vengono installati come parte di questa versione.


In questa versione sono i seguenti aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Cluster di tipo (ad esempio Hadoop, HBase o eccesso)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>2.2 HDP update</td>
<td>In questa versione di 3,2 HDInsight contiene HDP 2.2.6 e offre diverse importanti correzioni di bug a HDInsight. Note sulla versione completa è disponibile in <a href="http://dev.hortonworks.com.s3.amazonaws.com/HDPDocuments/HDP2/HDP-2.2.6/HDP_RelNotes_v226/index.html">HDP 2.2.6 note sulla versione</a>.</td>
<td>HDP</td>
<td>Tutti i tipi di grafico</td>
<td>N/D</td>
</tr>

<tr>
<td>Passare alla configurazione di memoria predefinita filati contenitore</td>
<td>In questo aggiornamento predefinito memoria disponibile per i contenitori di filati (yarn.nodemanager.resource.memory mb e yarn.scheduler.maximum allocazione mb), avviata da Gestione nodo viene aumentato a 5632MB. In precedenza è stato ridotto a MB 4608 ma in base a vari esecuzione del processo, il nuovo valore deve offrire meglio l'affidabilità e le prestazioni alla maggior parte dei processi, pertanto è migliore predefinito. Come di consueto, se è una dipendenza critica in questa configurazione di memoria, impostarlo in modo esplicito durante la creazione del cluster.</td>
<td>HDP</td>
<td>Tutti i tipi di grafico</td>
<td>N/D</td>
</tr>

<tr>
<td>Uniformità configurazione predefinito per i cluster HBase ed eccesso</td>
<td>Questo aggiornamento consente di ripristinare Hbase ed eccesso cluster per poter utilizzare gli stessi valori filati configurazioni come cluster Hadoop. A tale scopo di uniformità tra tutti i tipi di grafico.</td>
<td>HDP</td>
<td>HBase, eccesso</td>
<td>N/D</td>
</tr>

</table>

## <a name="notes-for-05202015-release-of-hdinsight"></a>Note sulla versione 20/05/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.564.1542093 (1.3.12.0-01795 HDP - invariato)
* HDInsight 3.0.6.564.1542093 (2.0.13.0-2117 HDP - invariato)
* HDInsight 3.1.3.564.1542093 (HDP 2.1.12.1-0003)
* HDInsight 3.2.4.564.1542093 (HDP 2.2.4.6-2)
* SDK 1.5.8

In questa versione sono i seguenti aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Cluster di tipo (ad esempio Hadoop, HBase o eccesso)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Supporto EventHub SCP.NET</td>
<td>I pacchetti cluster aggiornati per eccesso HDInsight portare nuove caratteristiche in SCP.NET. A questo punto si avrà accesso alle nuove API in Generatore di topologia che rendono più semplice da utilizzare EventHubSpout o linguaggio Spouts. È necessario aggiornare il client SCP.NET SDK per lavorare con i cluster di nuovo come i contratti sono stati aggiornati. Per informazioni dettagliate la nuova API, versione e sull'utilizzo delle note (inclusi correzioni di bug) vedere il file Leggimi incluso nel pacchetto nuget SCP.NET.</td>
<td>VS utensili</td>
<td>Eccesso 3,2 HDInsight cluster</td>
<td>N/D</td>
</tr>

<tr>
<td>Aggiornamento del driver JDBC</td>
<td>Aggiornare il driver con la versione di SQL Server supportati nei sqljdbc_4.1.5605.100.</td>
<td>Metastore</td>
<td>Tutti</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-04272015-release-of-hdinsight"></a>Note sulla versione 27/04/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.537.1486660 (1.3.12.0-01795 HDP - invariato)
* HDInsight 3.0.6.537.1486660 (2.0.13.0-2117 HDP - invariato)
* HDInsight 3.1.3.537.1486660 (2.1.12.0-2329 HDP - invariato)
* HDInsight 3.2.3.537.1486660 (HDP 2.2.2.2-4)
* SDK 1.5.8

In questa versione sono i seguenti aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Cluster di tipo (ad esempio Hadoop, HBase o eccesso)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Correggere dipendenza DLL</td>
<td>Rimuove dipendenza da HDInsight Framework Unit Test.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Correzione dei bug per condizione di competizione</td>
<td>Un cluster di creare richiesta adesso attesa nella richiesta di caricamento per essere accettato prima di polling sullo stato</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>
</table>

## <a name="notes-for-04142015-release-of-hdinsight"></a>Note sulla versione 14/04/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.521.1453250 (1.3.12.0-01795 HDP - invariato)
* HDInsight 3.0.6.521.1453250 (2.0.13.0-2117 HDP - invariato)
* HDInsight 3.1.3.521.1453250 (2.1.12.0-2329 HDP - invariato)
* HDInsight 3.2.3.525.1459730 (HDP 2.2.2.2-2)
* SDK 1.5.6

In questa versione sono i seguenti aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Cluster di tipo (ad esempio Hadoop, HBase o eccesso)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Correzioni di bug Tez</td>
<td>Risoluzioni per Apache TEZ 2214 e TEZ 1923 sono inclusi in questa versione di 3,2 HDI. Queste informazioni sono necessarie specificamente per le query determinati Hive su Tez che richiedono in modo casuale una notevole quantità di dati.
</td>
<td>HDP</td>
<td>Hadoop</td>
<td><a href="https://issues.apache.org/jira/browse/TEZ-2214">TEZ 2214</a></br><a href="https://issues.apache.org/jira/browse/TEZ-1923">TEZ 1923</a></td>
</tr>
</table>

## <a name="notes-for-04062015-release-of-hdinsight"></a>Note sulla versione 04/06/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.521.1453250 (1.3.12.0-01795 HDP - invariato)
* HDInsight 3.0.6.521.1453250 (2.0.13.0-2117 HDP - invariato)
* HDInsight 3.1.3.521.1453250 (2.1.12.0-2329 HDP - invariato)
* HDInsight 3.2.3.521.1453250 (HDP 2.2.2.2-1)
* SDK 1.5.6

In questa versione sono i seguenti aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Cluster di tipo (ad esempio Hadoop, HBase o eccesso)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>HDInsight .NET SDK 1.5.6</td>
<td>Aggiornamenti per rimuovere alcune classi interni per HDInsight su Linux.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Raccolta Avro 1.5.6</td>
<td>Aggiunta <b>KnownTypeAttribute del</b> metodo <b>GetAllKnownTypes</b>. Fisso NullReferenceException quando un tipo di è null per GetAllKnownTypes metodo.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Correzioni di bug</td>
<td>Varie correzioni di bug al servizio</td>
<td>Servizio</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

</table>
<br>

## <a name="notes-for-04012015-release-of-hdinsight"></a>Note sulla versione 04/01/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.513.1431705 (HDP 1.3.12.0-01795)
* HDInsight 3.0.6.513.1431705 (HDP 2.0.13.0-2117)
* HDInsight 3.1.3.513.1431705 (HDP 2.1.12.0-2329)
* HDInsight 3.2.3.513.1431705 (HDP 2.2.2.1-2600)
* SDK 1.5.5

In questa versione sono i seguenti aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Cluster di tipo (ad esempio Hadoop, HBase o eccesso)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Possibilità di abilitare/disabilitare le credenziali di desktop remote dal cluster di Windows tramite .NET SDK</td>
<td>Livello di programmazione supporto per abilitare o disabilitare le credenziali RDP sui cluster di Windows.</td>
<td>SDK</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Possibilità di abilitare delle credenziali di desktop remote nei cluster mentre che viene effettuato il provisioning</td>
<td>Livello di programmazione supporto per le credenziali desktop remote per consentire al cluster della creazione. Consente di rimuovere il processo in due passaggi per la prima di tutto il provisioning del cluster e quindi attivare desktop remoto.</td>
<td>SDK</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Python aggiornato a 2.7.8</td>
<td>Consente di correggere Python aggiornati sui cluster HDInsight a Python 2.7.8, che contiene alcuni importante sicurezza per le versioni HDInsight 2.1, 3.0, 3.1 e 3.2</td>
<td>Servizio</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Modifica della configurazione filati</td>
<td>Modificato filati configurazione yarn.resourcemanager.max completata-applicazioni a 1000 per tutti i tipi di grafico per le versioni HDInsight 3.1 e 3.2. Questo valore controlla solo l'elenco delle applicazioni completate nell'interfaccia utente di filati. Per ottenere informazioni sulle applicazioni che sono stati inviati prima dell'elenco di applicazioni visualizzate nell'interfaccia utente, è possibile accedere direttamente al Server della cronologia.</td>
<td>FILATI</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Ridimensionamento dei nodi in un cluster di HBase</td>
<td>Cluster HBase ora consentire il ridimensionamento dei nodi (alto e verso il basso) per le versioni HDInsight 3.1 e 3.2</td>
<td>Servizio</td>
<td>HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>Aggiornamento JDBC</td>
<td>Driver SQL JDBC l'aggiornamento alla versione sqljdbc_4.0.2206.100 per HDInsight versione 3.2. Questa versione contiene miglioramenti della protezione importanti.</td>
<td>HDP</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Aggiornamento della configurazione JVM</td>
<td>Aggiornato JVM configurazione networkaddress.cache.ttl a 300 secondi rispetto al valore predefinito di -1 per HDInsight versioni 3.1 e 3.2. Questo valore configurazione controlla i criteri di memorizzazione nella cache per ricerche di nomi ha esito positivo dal servizio di nome. Consente di correggere un errore correlato a ingrandimento o riduzione HBase cluster.</td>
<td>Servizio</td>
<td>HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>Eseguire l'aggiornamento allo spazio di archiviazione di Azure SDK per Java 2.0</td>
<td>HDInsight versione 3.2 in modo da usare la versione più recente di Azure archiviazione SDK per Java. Questa pagina contiene più importanti correzioni di bug su 0.6.0 corrente versione.</td>
<td>HDP</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Aggiornamento a codice sorgente Apache WASB</td>
<td>HDInsight versione 3.2 ora utilizza le ultime codice per il driver di file System WASB da Apache Hadoop. Con questa modifica, il driver WASB a questo punto viene fornito come un vaso separata. Questo è semplicemente una modifica di confezioni per i prodotti e non contiene le modifiche al comportamento del driver WASB. Il nome del file VASO è 2.6.0.jar di azure hadoop.</td>
<td>HDP</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>JAR gli aggiornamenti di nome del File in 3,2 HDInsight</td>
<td>L'aggiornamento a HDInsight versione 3.2 include diverse correzioni di bug e alcuni vasetti da interni incluso come parte di HDP sono stati aggiornati. Si noti che questi file VASO interni il pacchetto HDP e non per l'utilizzo diretto da applicazioni del cliente. Applicazioni devono creare un pacchetto la propria versione della vasetti da in modo che un aggiornamento per la JAR interno HDP non interrompere applicazioni del cliente.</td>
<td>HDP</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

</table>
<br>

## <a name="notes-for-03032015-release-of-hdinsight"></a>Note sulla versione 03/03/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.488.1375841 (1.3.9.0-01351 HDP - invariato)
* HDInsight 3.0.6.488.1375841 (2.0.9.0-2097 HDP - invariato)
* HDInsight 3.1.3.488.1375841 (2.1.10.0-2290 HDP - invariato)
* HDInsight 3.2.3.488.1375841 (HDP-2.2.10.0-2340 - invariato)
* SDK 1.5.0 (invariato)

In questa versione sono i seguenti aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Cluster di tipo (ad esempio Hadoop, HBase o eccesso)</th>
<th>JIRA</th>
</tr>


<tr>
<td>Maggiore affidabilità</td>
<td>Abbiamo apportato correzioni consentono al servizio di scalare meglio con l'aumento del carico per la creazione di cluster.</td>
<td>Servizio</td>
<td>Tutti</td>
<td>N/D</td>
</tr>



</table>
<br>

## <a name="notes-for-02182015-release-of-hdinsight"></a>Note della versione di HDInsight 18/02/2015

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.471.1342507 (1.3.9.0-01351 HDP - invariato)
* HDInsight 3.0.6.471.1342507 (2.0.9.0-2097 HDP - invariato)
* HDInsight 3.1.3.471.1342507 (2.1.10.0-2290 HDP - invariato)
* HDInsight 3.2.3.471.1342507 (HDP-2.2.10.0-2340)
* SDK 1.5.0

In questa versione sono i seguenti aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Cluster di tipo (ad esempio Hadoop, HBase o eccesso)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>3.2 HDInsight cluster</td>
<td>Hadoop 2.6/HDP2.2 è disponibile con i cluster di 3,2 HDInsight. La presentazione contiene aggiornamenti più importanti apportati a tutti i componenti di Apri origine. Per ulteriori informazioni, vedere Novità di HDInsight e <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html" target="_blank">note sulla versione di HDP 2.2.0.0</a>.</td>
<td>Software open source</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>HDinsight su Linux (Preview)</td>
<td>È possibile distribuire cluster su Ubuntu Linux. Per ulteriori informazioni, vedere Introduzione a HDInsight su Linux.</td>
<td>Servizio</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Disponibilità generale eccesso</td>
<td>Cluster Apache eccesso sono in genere disponibili. Per ulteriori informazioni, vedere Introduzione all'utilizzo eccesso in HDInsight.</td>
<td>Servizio</td>
<td>Eccesso</td>
<td>N/D</td>
</tr>

<tr>
<td>Dimensioni di macchina virtuale</td>
<td>Azure HDInsight è disponibile in altri tipi di macchina virtuale e le dimensioni. HDInsight possono utilizzare A2 alle dimensioni A7 progettate per scopi generali; Nodi D serie che portare in primo piano unità mercata (SSDs) e processori più veloci percentuale di 60; e le dimensioni di A8 e A9 contenenti InfiniBand al supporto per configurazione rapida della rete.</td>
<td>Servizio</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Cluster proporzioni dei caratteri</td>
<td>È possibile modificare il numero dei nodi di dati per un cluster di HDInsight in esecuzione senza dover eliminare o ricrearlo. Solo i tipi di cluster Hadoop Query e Apache eccesso sono attualmente questa caratteristica, ma il supporto per HBase Apache tipo cluster breve consiste nell'eseguire. Per ulteriori informazioni, vedere gestire HDInsight cluster.</td>
<td>Servizio</td>
<td>Hadoop, eccesso</td>
<td>N/D</td>
</tr>

<tr>
<td>Strumenti di Visual Studio</td>
<td>Oltre a completi utensili per eccesso Apache, gli utensili per Apache Hive in Visual Studio sono stato aggiornato per includere il completamento, convalida locali e il supporto di debug migliorato. Per ulteriori informazioni, vedere Introduzione a HDInsight Hadoop strumenti di Visual Studio.</td>
<td>Utensili</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Connettore Hadoop per DocumentDB</td>
<td>Connettore Hadoop per DocumentDB, è possibile eseguire aggregazioni complesse, l'analisi e modifiche sui documenti JSON schema meno archiviati in raccolte DocumentDB o più account di database. Per ulteriori informazioni e un'esercitazione, vedere eseguire Hadoop processi utilizzando DocumentDB e HDInsight.</td>
<td>Servizio</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Correzioni di bug</td>
<td>Abbiamo fatto vari correzioni di bug secondarie per i servizi di HDInsight. È previsto alcun modifiche di comportamento per i clienti.</td>
<td>Servizio</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

</table>
<br>

## <a name="notes-for-02062015-release-of-hdinsight"></a>Note sulla versione 02/06/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.463.1325367 (1.3.9.0-01351 HDP - invariato)
* HDInsight 3.0.6.463.1325367 (2.0.9.0-2097 HDP - invariato)
* HDInsight 3.1.2.463.1325367 (HDP 2.1.10.0-2290)
* N/D SDK

In questa versione sono i seguenti aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Cluster di tipo (ad esempio Hadoop, HBase o eccesso)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Correzioni di bug</td>
<td>Abbiamo fatto vari correzioni di bug secondarie per i servizi di HDInsight. È previsto alcun modifiche di comportamento per i clienti.</td>
<td>Servizio</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Aggiornamento di manutenzione HDP 2.1</td>
<td>3.1 HDInsight viene aggiornato per distribuire HDP 2.1.10.0. Per ulteriori informazioni, vedere <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.10/bk_releasenotes_hdp_2.1/content/ch_relnotes-HDP-2.1.10.html" target="_blank">le note sulla versione HDP-2.1.10</a>. </td>
<td>Software open source</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Aggiornamenti binari HDP</td>
<td>Esistono alcuni file VASO in HBase per i file sono stati aggiornati i nomi. Questi file VASO vengono utilizzati internamente HBase, in modo che non si prevede che i clienti dipendono i nomi di questi file VASO. Sono inclusi:
<ul>
<li>./lib/jetty-6.1.26.hwx.jar</li>
<li>./lib/jetty-sslengine-6.1.26.hwx.jar</li>
<li>./lib/jetty-util-6.1.26.hwx.jar</li>
</ul>
</td>
<td>Software open source</td>
<td>HBase</td>
<td>N/D</td>
</tr>

</table>
<br>

## <a name="notes-for-1292015-release-of-hdinsight"></a>Note sulla versione 29/1/2015 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.455.1309616 (1.3.9.0-01351 HDP - invariato)
* HDInsight 3.0.6.455.1309616 (2.0.9.0-2097 HDP - invariato)
* HDInsight 3.1.2.455.1309616 (2.1.9.0-2196 HDP - invariato)
* N/D SDK

In questa versione sono i seguenti aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Area interessata (ad esempio servizio, componente o SDK)</p></th>
<th>Cluster di tipo (ad esempio Hadoop, HBase o eccesso)</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>

<td>Correzioni di bug</td>
<td>Abbiamo fatto alcune importanti correzioni di bug che migliorano le prestazioni dei cluster HDInsight durante l'aggiornamento di Azure.</td>
<td>Servizio</td>
<td>Tutti</td>
<td>N/D</td>
</tr>



</table>
<br>

## <a name="notes-for-152015-release-of-hdinsight"></a>Note per 5/1/2015 versione HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione:

* HDInsight 2.1.10.420.1246118 (1.3.9.0-01351 HDP - invariato)
* HDInsight 3.0.6.420.1246118 (2.0.9.0-2097 HDP - invariato)
* HDInsight 3.1.2.420.1246118 (2.1.9.0-2196 HDP - invariato)


In questa versione sono i seguenti aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di grafico</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Esempi di analisi delle tendenze Twitter e suggerimenti in base Mahout filmato</td>
<td><p>In questa versione, la console di HDInsight Query dispone di due campioni aggiuntive:</p>

<p><b>Analisi delle tendenze Twitter</b><br>
API pubbliche fornite da siti come Twitter sono un'utile origine dati per l'analisi e le tendenze popolari in base. In questa esercitazione, informazioni su come usare Hive per ottenere un elenco di utenti Twitter che ha inviato la maggior parte dei TWEET contenente una determinata parola. </p>

<p><b>Suggerimento mahout filmato</b><br>
Apache Mahout è una raccolta di apprendimento Apache Hadoop. Mahout contiene algoritmi per l'elaborazione dei dati (ad esempio filtri, classificazione e cluster). In questa esercitazione, utilizzare un modulo di suggerimento per generare suggerimenti filmato basati su filmati visualizzato amici.</p></td>
<td>Console di query</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Valore predefinito per una configurazione Hive: hive.auto.convert.join.noconditionaltask.size</td>
<td><p>Questa configurazione dimensioni si applica a join mappa convertito automaticamente. Il valore rappresenta la somma delle dimensioni delle tabelle che possono essere convertite in mappe hash che rientrano in memoria. Nelle versioni precedenti, questo valore maggiore rispetto al valore predefinito di 10 MB a 128 MB. Tuttavia, il nuovo valore 128 MB causa processi generare errori dovuti a causa di memoria insufficiente. In questa versione consente di ripristinare il valore predefinito 10 MB. I clienti comunque possono scegliere di ignorare questo valore durante la creazione di cluster concesso loro query e le dimensioni di tabella. Per ulteriori informazioni su questa impostazione e su come eseguire l'override, vedere <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">Join automatico ottimizzare conversione</a> nella documentazione Hortonworks. </p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>N/D</td>
</tr>

</table>
<br>

## <a name="notes-for-12232014-release-of-hdinsight"></a>Note sulla versione 23/12/2014 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione sono:

* HDInsight 2.1.10.420.1246783 (versione HDP invariato)
* HDInsight 3.0.6.420.1246783 (versione HDP invariato)
* HDInsight 3.1.1.420.1246783 (versione HDP invariato)

In questa versione sono i seguenti aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di grafico</th>
<th>JIRA (se applicabile)</th>
</tr>


<tr>
<td>Errori di creazione cluster intermittente a causa di carico eccessivo</td>
<td><p>Algoritmo migliorato per il download di pacchetti HDP durante la creazione di cluster consente sia più efficace gestione degli errori a causa di carico eccessivo.</p></td>
<td>Servizio</td>
<td>Hadoop, Hbase, eccesso</td>
<td>N/D</td>
</tr>



</table>
<br>

## <a name="notes-for-12182014-release-of-hdinsight"></a>Note sulla versione 18/12/2014 di HDInsight

In questa versione sono l'aggiornamento dei componenti seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di grafico</th>
<th>JIRA (se applicabile)</th>
</tr>

<tr>
<td><a href = "hdinsight-hadoop-customize-cluster.md" target="_blank">Personalizzazione di cluster Avalability generale</a></td>
<td><p>Personalizzazione offre la possibilità di personalizzare i cluster di Azure HDInsight con i progetti che sono disponibili da ecosistema Apache Hadoop. Con questa nuova funzionalità, è possibile provare e distribuire progetti Hadoop Azure HDInsight. Questa opzione è attivata tramite la funzionalità di **Script azione** , che possa modificare cluster Hadoop in modi non autorizzati tramite script personalizzati. La personalizzazione è disponibile in tutti i tipi di cluster HDInsight inclusi Hadoop, HBase ed eccesso. Per illustrare le potenzialità di questa funzionalità, è stata descritte la procedura per installare più diffusi <a href = "hdinsight-hadoop-spark-install.md" target="_blank">motori</a>, <a href = "hdinsight-hadoop-r-scripts.md" target="_blank">R</a>, <a href = "hdinsight-hadoop-solr-install.md" target="_blank">Solr</a>e <a href = "hdinsight-hadoop-giraph-install.md" target="_blank">Giraph</a> moduli. In questa versione aggiunti anche la funzionalità per i clienti specificare le azioni di script personalizzato tramite il portale Azure fornisce linee guida e procedure consigliate su come creare azioni di script personalizzati utilizzando metodi di supporto e vengono fornite indicazioni su come verificare l'azione script. </p></td>
<td>Disponibilità generale di funzionalità</td>
<td>Tutti</td>
<td>N/D</td>
</tr>


</table>
<br>

## <a name="notes-for-12052014-release-of-hdinsight"></a>Note sulla versione 05/12/2014 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione sono:

* HDInsight 2.1.9.406.1221105 (HDP 1.3.9.0-01351)
* HDInsight 3.0.5.406.1221105 (HDP 2.0.9.0-2097)
* HDInsight 3.1.1.406.1221105 (HDP 2.1.9.0-2196)
* HDInsight SDK n/d

In questa versione sono i seguenti componenti di aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di grafico</th>
<th>JIRA (se applicabile)</th>
</tr>

<tr>
<td>Correzione: intermittente errore durante l'aggiunta di un numero elevato di partizioni a una tabella in un DDL Hive </td>
<td><p>Se si verifica un errore di connessione intermittente con il database metastore Hive quando si aggiungono molte delle partizioni a una tabella Hive, DDL Hive può avere esito negativo. L'istruzione seguente sarà visibile nel log degli errori Hive se si verifica questo errore: </p><p>"Errore [main]: ql. Driver (SessionState.java:printError(547)) - non è riuscita: errore di esecuzione, il codice restituito 1 da org.apache.hadoop.hive.ql.exec.DDLTask. MetaException (message:java.lang.RuntimeException: commitTransaction è stato chiamato ma openTransactionCalls = 0. Probabilmente indica che ci siano bilanciate chiamate a openTransaction/commitTransaction)"</p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>HIVE-482 (si tratta di un JIRA interno, non possono essere indicato esternamente. Nei casi indicati di seguito per riferimento.)</td>
</tr>

<tr>
<td>Correzione: blocco occasionale nella Console di Query HDInsight</td>
<td>In questo caso, l'istruzione seguente può essere visualizzato nel log delle WebHCat per il processo di avvio WebHCat: <p>"org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException): Impossibile caricare il file di cronologia {wasb url per il file di cronologia} "</p></td>
<td>WebHCat</td>
<td>Hadoop</td>
<td>HIVE-482 (si tratta di un JIRA interno, non possono essere indicato esternamente. Nei casi indicati di seguito per riferimento.)</td>
</tr>

<tr>
<td>Correzione: il raccoglitore occasionale della latenza di query Hbase</td>
<td>In questo caso, gli utenti noterà un picco occasionale di 3 secondi la latenza delle query Hbase. </td>
<td>HDInsight Cluster Gateway</td>
<td>HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>HDP JAR modifiche ai nomi di file</td>
<td>Per HDI cluster versione 3.0, esiste un paio di modifiche ai file VASO interni installati da HDP. alla banchina 6.1.26.jar è stata sostituita con alla banchina 6.1.26.hwx.jar. 6.1.26.jar di util alla banchina è stato sostituito con 6.1.26.hwx.jar di util alla banchina. Le modifiche applicate ai progetti Hadoop, Mahout, WebHCat e Oozie.</td>
<td>Hadoop, Mahout, WebHCat, Oozie</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>

</table>
<br>


## <a name="notes-for-11212014-release-of-hdinsight"></a>Note sulla versione 21/11/2014 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione sono:

* HDInsight 2.1.9.382.1169709 (nessun cambiamento rispetto 14/11/2014)
* HDInsight 3.0.5.382.1169709 (nessun cambiamento rispetto 14/11/2014)
* HDInsight 3.1.1.382.1169709 (nessun cambiamento rispetto 14/11/2014)
* HDINsight SDK 1.4.0

In questa versione sono i seguenti componenti di aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di grafico</th>
<th>JIRA (se applicabile)</th>
</tr>

<tr>
<td>Accesso ai registri applicazioni</td>
<td>Possibilità di a livello di programmazione consente di enumerare le applicazioni eseguite nei cluster di e per scaricare i contenuti pertinenti registri contenitore specifiche o applicazione per facilitare il debug di applicazioni problematiche.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Possibilità di specificare il nome di area geografica in IHdInsightClient.DeleteCluster </td>
<td>Azure HDInsight SDK consente di specificare un nome di area quando si utilizza **DeleteCluster**. In questo modo sbloccare clienti e che ha due risorse con stesso nome in diverse aree geografiche non ha potuto eliminare uno di essi.</td>
<td>SDK</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>ClusterDetails.DeploymentId</td>
<td>L'oggetto **ClusterDetails** restituisce un campo **DeploymentID** che rappresenta un identificatore univoco per il cluster. Garantisce l'univocità tra tentativi di creazione cluster con gli stessi nomi.</td>
<td>SDK</td>
<td>Tutti</td>
<td>N/D</td>
</tr>
</table>
<br>

## <a name="notes-for-11142014-release-of-hdinsight"></a>Note sulla versione 14/11/2014 di HDInsight

I numeri di versione completa di cluster HDInsight distribuiti in questa versione sono:

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

In questa versione contiene i seguenti nuove funzionalità, gli aggiornamenti dei componenti e correzioni di bug.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di grafico</th>
<th>JIRA (se applicabile)</th>
</tr>

<tr>
<td>Azione di script (Preview)</td>
<td>Anteprima della caratteristica di personalizzazione cluster che consente di modificare gli Hadoop cluster in modi non autorizzati tramite script personalizzati. Con questa caratteristica, gli utenti possono sperimentare e distribuire progetti disponibili da ecosistema Apache Hadoop ai cluster di Azure HDInsight. Questa caratteristica di personalizzazione è disponibile in tutti i tipi di cluster HDInsight, inclusi Hadoop, HBase ed eccesso.</td>
<td>Nuova caratteristica</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Processi predefiniti disponibili per l'analisi di log di siti Web e lo spazio di archiviazione Azure</td>
<td>La Console di Query HDInsight dispone di una raccolta Guida introduttiva che supporta le soluzioni che funzionano sui dati o sui dati di esempio.
<p>**Soluzioni che utilizzino i dati**:<br>
Abbiamo creato processi per alcuni scenari di analisi dei dati più comuni per fornire un punto di partenza per la creazione di soluzioni personalizzate. È possibile utilizzare i dati con il processo per verificarne il funzionamento. Quando si è pronti, utilizzare quanto appreso creare una soluzione basata processo predefinito.</p>
<p>**Soluzioni che sta lavorando dati di esempio**:<br>
Informazioni su come lavorare con HDInsight esaminando alcuni scenari di base (ad esempio, l'analisi dei dati dei sensori e registri web). Si apprenderà come utilizzare HDInsight per l'analisi dei dati e come è possibile connettersi altre applicazioni e servizi questi dati. La visualizzazione di dati mediante la connessione a Microsoft Excel fornisce un esempio di potenza di questo approccio.</p></td>
<td>Console di query</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Correzione di perdita della memoria in Templeton</td>
<td>Memoria in Templeton che i clienti che hanno avuto lunga cluster oppure sono stati inviare 100s del processo di interessate richiede che un secondo è stato risolto. Il problema indicato come Templeton 5xx errori e per risolvere il problema è stato necessario riavviare il servizio. Per risolvere il problema non è più necessaria.</td>
<td>Templeton</td>
<td>Tutti</td>
<td>https://Issues.Apache.org/jira/Browse/HADOOP-11248</td>
</tr>
</table>
<br>


**Nota**: per illustrare le nuove funzionalità disponibili per la personalizzazione di cluster, sono stati descritte le procedure tramite Script azione per installare vivacità ed R moduli in un cluster. Per ulteriori informazioni, vedere:

* [Installare e usare i 1.0 nei cluster HDInsight](hdinsight-hadoop-spark-install.md)
* [Installare e usare R nei cluster HDInsight Hadoop](hdinsight-hadoop-r-scripts.md)




## <a name="notes-for-11072014-release-of-hdinsight"></a>Note sulla versione 11/07/2014 di HDInsight

I numeri di versione completa per i cluster HDInsight che distribuito con questa versione sono:

* HDInsight 2.1 2.1.9.374.1153876
* HDInsight 3.0 3.0.5.374.1153876
* HDInsight 3.1 3.1.1.374.1153876

In questa versione sono i seguenti componenti di aggiornamenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di grafico</th>
<th>JIRA (se applicabile)</th>
</tr>

<tr>
<td>HDP 2.1.7</td>
<td>In questa versione è basata su Hortonworks dati piattaforma (HDP) 2.1.7. Per ulteriori informazioni, vedere <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html" target="_blank">Note sulla versione di HDP 2.1.7</a>.</td>
<td>HDP</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Server di filati sequenza temporale</td>
<td>Il Server di sequenza temporale filati (noto anche come l'applicazione cronologia Server generico) è stato abilitato per impostazione predefinita. Il Server di sequenza temporale fornisce informazioni generiche sulle applicazioni completate (ad esempio ID applicazione, nome dell'applicazione, lo stato dell'applicazione, ora di invio dell'applicazione e ora di completamento dell'applicazione).

Queste informazioni applicazione possono recuperate dal nodo principale accedendo URI http://headnodehost:8188 o eseguendo il comando filati: filati applicazione-elenco-appStates tutti.

Queste informazioni possono essere recuperate anche in modalità remota se un API REST in https://{ClusterDnsName}. azurehdinsight.NET/WS/v1/applicationhistory/.

Per informazioni più dettagliate, vedere <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Filati sequenza temporale Server</a>.</td>
<td>Servizio, filati</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>ID distribuzione cluster</td>
<td>A partire da SDK versione 1.3.3.1.5426.29232, gli utenti possono accedere a un ID univoco per ogni cluster rilasciato HDInsight. Questo consente agli utenti di capire istanze univoche di cluster quando un nome DNS riutilizzato in creare o eliminare scenari.</td>
<td>SDK</td>
<td>Tutti</td>
<td>N/D</td>
</tr>
</table>
<br>

**Nota**: bug impedito il numero di versione completa siano visualizzati nel portale di o la restituzione da SDK o da Windows PowerShell è stato risolto in questa versione.

## <a name="notes-for-10152014-release"></a>Note sulla versione 15/10/2014

In questa versione di aggiornamento rapido corretta memoria in Templeton che gli utenti piena di Templeton interessati. In alcuni casi, gli utenti che esercitare Templeton modo intensivo vedrà errori indicati come 500 codici di errore perché le richieste non avrebbe memoria insufficiente per eseguire. Soluzione alternativa per questo problema è stato necessario riavviare il servizio Templeton. Questo problema è stato risolto.


## <a name="notes-for-1072014-release"></a>Note sulla versione 7/10/2014

* Quando si Usa endpoint Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", il campo *host_name* restituisce il nome di dominio completo (FQDN) del nodo anziché solo il nome host. Ad esempio, invece di restituire "**headnode0**", si riceve il FQDN "**headnode0. { ClusterDNS} .azurehdinsight .net**". Questa modifica è stata necessaria per agevolare gli scenari in cui è possono distribuire più tipi di grafico (ad esempio HBase e Hadoop) in una rete virtuale. Si verifica questo evento, ad esempio, quando si utilizza HBase come piattaforma back-end per Hadoop.

* Sono disponibili nuove impostazioni di memoria per la distribuzione predefinita del cluster HDInsight. Precedente impostazioni predefinite della memoria non è stato adeguatamente account per le linee guida per il numero di core CPU in fase di distribuzione. Le nuove impostazioni di memoria devono fornire i valori predefiniti migliori (in base alle indicazioni Hortonworks). Per cambiare queste, fare riferimento alla documentazione di riferimento SDK sulla modifica della configurazione cluster. Nella tabella seguente vengono classificate le nuove impostazioni di memoria utilizzati da HDInsight cluster predefinito 4 CPU core (contenitore 8). (I valori utilizzati nelle versioni precedenti vengono forniti anche parenthetically.)

<table border="1">
<tr><th>Componente</th><th>Allocazione della memoria</th></tr>
<tr><td> assegnazione di yarn.Scheduler.Minimum</td><td>768 MB (in precedenza 512 MB)</td></tr>
<tr><td> assegnazione di yarn.Scheduler.Maximum</td><td>6144 MB (invariato)</td></tr>
<tr><td>yarn.NodeManager.Resource.Memory</td><td>6144 MB (invariato)</td></tr>
<tr><td>MapReduce.Map.Memory</td><td>768 MB (in precedenza 512 MB)</td></tr>
<tr><td>MapReduce.Map.java.OPTS</td><td>sceglie =-Xmx512m (in precedenza - Xmx410m)</td></tr>
<tr><td>MapReduce.reduce.Memory</td><td>1536 MB (in precedenza 1024 MB)</td></tr>
<tr><td>MapReduce.reduce.java.OPTS</td><td>sceglie =-Xmx1024m (in precedenza - Xmx819m)</td></tr>
<tr><td>yarn.app.MapReduce.AM.Resource</td><td>768 MB (in precedenza 1024 MB)</td></tr>
<tr><td>yarn.app.MapReduce.AM.Command</td><td>sceglie =-Xmx512m (in precedenza - Xmx819m)</td></tr>
<tr><td>MapReduce.Task.IO.Sort</td><td>256 MB (in precedenza 200 MB)</td></tr>
<tr><td>tez.AM.Resource.Memory</td><td>1536 MB (invariato)</td></tr>

</table><br>

Per ulteriori informazioni sulle impostazioni di configurazione di memoria utilizzato da filati e MapReduce sulla piattaforma dati Hortonworks utilizzato da HDInsight, vedere [Determinare le impostazioni di configurazione di HDP memoria](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html). Hortonworks fornito anche uno strumento per calcolare le impostazioni della memoria corretto.

Per quanto riguarda PowerShell Azure e il messaggio di errore HDInsight SDK: "*Cluster non è configurato per l'accesso HTTP services*":

* Questo errore viene segnalato un [problema di compatibilità](https://social.msdn.microsoft.com/Forums/azure/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight) noto che può essere dovuta una differenza tra la versione di SDK HDInsight o PowerShell Azure e la versione del cluster. Cluster creato nel 8/15 o versioni successive sono supportati in nuove funzionalità di provisioning in reti virtuali. Ma questa funzionalità non vengano interpretata correttamente da versioni precedenti di HDInsight SDK o Azure PowerShell. Il risultato è un errore in alcune operazioni di invio del processo. Se si utilizza HDInsight SDK API o i cmdlet di PowerShell Azure (**Usare AzureRmHDInsightCluster** o **AzureRmHDInsightHiveJob richiama**) per inviare i processi, queste operazioni potrebbero non riuscire con il messaggio di errore "*Cluster <clustername> non è configurato per l'accesso HTTP services*." O (a seconda dell'operazione), è possibile ottenere altri messaggi di errore, ad esempio "*non è possibile connettersi a cluster*".

* Questi problemi di compatibilità vengono risolti nelle versioni più recenti di PowerShell Azure HDInsight SDK. È consigliabile SDK HDInsight l'aggiornamento alla versione 1.3.1.6 o versioni successivi e Azure strumenti PowerShell alla versione 0.8.8 o versione successiva. È possibile accedere SDK più recente di HDInsight da [](http://nuget.codeplex.com/wikipage?title=Getting%20Started) e gli strumenti di PowerShell Azure come [installare e configurare Azure PowerShell](../powershell-install-configure.md).



## <a name="notes-for-9122014-release-of-hdinsight-31"></a>Note per/9/12/2014 versione 3.1 HDInsight

* In questa versione è basata su Hortonworks dati piattaforma (HDP) 2.1.5. Per un elenco dei bug corretti in questa versione, vedere la pagina [fisso in questa versione](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) del sito Hortonworks.
* Nella cartella raccolte maialino è stato modificato il file "avro-mapred-1.7.4.jar" a "avro-mapred-1.7.4-hadoop2.jar". Il contenuto del file contiene una correzione secondaria che unificatore. È consigliabile che i clienti non effettuare una dipendenza diretta sul nome del file VASO per evitare interruzioni alla ridenominazione di file.


## <a name="notes-for-8212014-release"></a>Note sulla versione 21/8/2014

* Si aggiungono le seguenti operazioni WebHCat (HIVE 7155), che consente di impostare il limite di memoria predefinito per un processo controller Templeton a 1 GB. (Il valore predefinito precedente è stato 512 MB).

     templeton.Mapper.Memory.MB (= 1024)

    * Questa modifica consente di risolvere l'errore seguente che determinate query Hive fosse eseguito a causa dei limiti di memoria inferiori: "Contenitore esegue oltre ai limiti di memoria fisica".
    * Per ripristinare le impostazioni predefinite precedente, è possibile impostare questo valore su 512 tramite PowerShell Azure al momento della creazione cluster utilizzando il comando seguente:

        Aggiungere AzureRmHDInsightConfigValues-base@{"templeton.mapper.memory.mb"="512";}


* Il nome host del ruolo zookeeper è stato modificato in *zookeeper*. Questo problema riguarda la risoluzione dei nomi all'interno del cluster, ma non influisce API REST esterne. Se si dispone di componenti che utilizzano il nome host *zookeepernode* , è necessario aggiornarli in modo da usare di nuovo nome. I nuovi nomi dei tre nodi zookeeper sono:
    * zookeeper0
    * zookeeper1
    * zookeeper2
* Matrice di supporto versione HBase viene aggiornata. Solo HDInsight versione 3.1 (HBase versione 0,98) è supportata per produzione per HBase carichi di lavoro. Versione 3.0 (che era disponibile per l'anteprima) non è possibile spostare in avanti.

## <a name="notes-about-clusters-created-prior-to-8152014"></a>Note sulle cluster create con versioni precedenti di 15/8/2014

Un messaggio di errore PowerShell Azure o HDInsight SDK "Cluster <clustername> non è configurato per l'accesso HTTP services" (o a seconda dell'operazione, altri i messaggi di errore ad esempio: "Impossibile connettersi al cluster") può verificarsi a causa di una differenza PowerShell Azure o SDK HDInsight e un cluster. Cluster creato nel 8/15 o versioni successive sono supportati in nuove funzionalità di provisioning in reti virtuali. Questa funzionalità non interpretata correttamente da versioni precedenti di PowerShell Azure o SDK HDInsight, creando così errori di operazioni di invio del processo. Se si utilizza HDInsight SDK API o Azure PowerShell cmdlet (ad esempio usare AzureRmHDInsightCluster o AzureRmHDInsightHiveJob richiama) per inviare i processi, queste operazioni potrebbero non riuscire con uno dei messaggi di errore descritti.

Questi problemi di compatibilità vengono risolti nelle versioni più recenti di PowerShell Azure HDInsight SDK. È consigliabile SDK HDInsight l'aggiornamento alla versione 1.3.1.6 o versioni successivi e Azure strumenti PowerShell alla versione 0.8.8 o versione successiva. È possibile accedere a SDK più recente di HDInsight da [NuGet][nuget-link]. È possibile accedere agli strumenti di PowerShell Azure tramite [Installazione guidata piattaforma Web Microsoft][webpi-link].


## <a name="notes-for-7282014-release"></a>Note sulla versione 28/7/2014

* **HDInsight disponibili in nuove aree**: abbiamo espanso presenza geografica HDInsight a tre aree. HDInsight clienti possono creare cluster in queste aree geografiche:
    * Asia orientale
    * America del Nord centrale USA
    * Sud centrale USA
* HDInsight versione 1.6 (1.1 HDP e Hadoop 1.0.3) e HDInsight versione 2.1 (HDP1.3 e Hadoop 1.2) vengono vengano rimossi dal portale di Azure. È possibile continuare a creare cluster Hadoop per queste versioni utilizzando il cmdlet di PowerShell di Azure, [Nuovo AzureRmHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx) o tramite [SDK HDInsight](http://msdn.microsoft.com/library/azure/dn469975.aspx). Fare riferimento alla pagina del [controllo delle versioni componente HDInsight](hdinsight-component-versioning.md) per ulteriori informazioni.
* Modifiche Hortonworks dati piattaforma (HDP) in questa versione:

<table border="1">
<tr><th>HDP</th><th>Modifiche</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>Nessuna modifica</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>Nessuna modifica</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ["3.4.5.2.1.3.2-0002"]</td></tr>


</table><br>

## <a name="notes-for-6242014-release"></a>Note sulla versione 24/6/2014

Questa versione contiene miglioramenti al servizio HDInsight:

* **Disponibilità HDP 2.1**: 3.1 HDInsight, che contiene HDP 2.1, generalmente disponibile e la versione predefinita per i cluster di nuovo.
* **HBase – miglioramenti portale Azure**: stiamo HBase cluster disponibili in anteprima. È possibile creare cluster HBase dal portale con pochi clic. 

Con HBase, è possibile creare una varietà di carichi di lavoro in tempo reale in HDInsight, da siti Web interattivi che funzionano con grandi set di dati per i servizi di archiviazione dei dati di telemetria e sensore dal milioni di punti finali. Il passaggio successivo, è possibile analizzare i dati in questi carichi di lavoro con i processi di Hadoop e ciò è possibile in HDInsight tramite PowerShell Azure e il dashboard di cluster Hive.

### <a name="apache-mahout-preinstalled-on-hdinsight-31"></a>Mahout Apache preinstallato in HDInsight 3.1

 [Mahout](http://hortonworks.com/hadoop/mahout/) è preinstallato nel cluster HDInsight 3.1 Hadoop, in modo che è possibile eseguire processi Mahout senza la necessità di configurazione cluster aggiuntive. Ad esempio, è possibile remote in un cluster Hadoop tramite protocollo RDP (Remote Desktop) e senza passaggi aggiuntivi, è possibile eseguire il comando Hello World Mahout seguente:

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Per una descrizione più completa di questa procedura, vedere la documentazione per l' [Esempio Breiman](https://mahout.apache.org/users/classification/breiman-example.html) nel sito Web Apache Mahout.


### <a name="hive-queries-can-use-tez-in-hdinsight-31"></a>Query hive utilizzabili Tez HDInsight 3.1

Hive 0.13 è disponibile in HDInsight 3.1 ed è in grado di eseguire query utilizzando Tez, possono essere utilizzati per sostanziale aumento delle prestazioni.
Tez non è attiva per impostazione predefinita per le query Hive. Per utilizzarla, è necessario acconsentire esplicitamente. È possibile abilitare Tez eseguendo il frammento di codice seguenti:

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks ha pubblicato una descrizione dettagliata Hive query miglioramenti delle prestazioni con Tez distribuiti in benchmark standard. Per informazioni dettagliate, vedere [benchmark Apache Hive 13 per Hadoop dell'organizzazione](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/).

Per ulteriori informazioni sull'utilizzo Hive con Tez, vedere [Hive su Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

###<a name="global-availability"></a>Disponibilità globale
Con la versione di HDInsight su Hadoop 2.2, Microsoft ha reso HDInsight disponibile in tutte le aree geografiche principali in Azure è disponibile. In particolare, Data Center di Europa e Asia sudorientale Ovest sono state introdotte online. In questo modo i clienti di individuare cluster in un Data Center vicino e potenzialmente in un'area di requisiti di conformità simili.


###<a name="dos--donts-between-cluster-versions"></a>Accorgimenti e tra versioni cluster

**Metastores Oozie usato con un cluster HDInsight 3.1 sono non compatibili con i cluster HDInsight 2.1, e non possono essere utilizzate con questa versione precedente**.

Un database di metastore Oozie personalizzato distribuito con un cluster 3.1 HDInsight non può essere riutilizzato con un cluster 2.1 HDInsight. Ciò avviene anche se il metastore ha avuto origine con un cluster 2.1 HDInsight. Questo scenario non è supportato perché lo schema metastore viene aggiornato quando si utilizza un cluster di 3.1 HDInsight, in modo che non è più compatibile con metastore necessari affinché cluster 2.1 HDInsight. Qualsiasi tentativo di riutilizzare un metastore Oozie utilizzati con un cluster HDInsight 3.1 verrà inutilizzabile cluster 2.1 HDInsight.

**Oozie metastores non può essere condiviso tra cluster.**

Oozie metastores sono associati a specifici cluster e non possono essere condivisi tra cluster.

###<a name="breaking-changes"></a>Ultime modifiche

**Sintassi di prefisso**: solo il "wasbs: / /" sintassi è supportata in 3.0 cluster e 3.1 HDInsight. Le versioni precedenti "consentire: / /" sintassi sono supportata in HDInsight 2.1 e 1.6 cluster, ma non è supportato in 3.0 cluster o 3.1 HDInsight. Ciò significa che tutti i processi inviati a un 3.1 HDInsight o cluster 3.0 utilizzano in modo esplicito i "consentire: / /" sintassi avrà esito negativo. La "wasbs: / /" sintassi da utilizzare in sostituzione. Inoltre, i processi inviati a qualsiasi 3.1 HDInsight o 3.0 cluster che vengono creati con un metastore esistente che contiene riferimenti espliciti alle risorse utilizzando il "consentire: / /" sintassi avrà esito negativo. Questi metastores devono essere ricreate utilizzando il "wasbs: / /" sintassi per le risorse indirizzo.


**Porte**: sono state modificate le porte utilizzate dal servizio HDInsight. I numeri di porta che sono stati utilizzati sono state all'interno dell'intervallo di porte temporanee del sistema operativo Windows. Porte vengono assegnate automaticamente un intervallo temporaneo predefinito per le comunicazioni basate su protocollo Internet breve. La nuova serie di numeri di porta servizio Hortonworks dati piattaforma (HDP) consentiti è esterno all'intervallo per evitare di creare conflitti che potrebbero verificarsi con le porte utilizzate da servizi in esecuzione sul nodo principale. I nuovi numeri di porta non dovrebbero causare le ultime modifiche. I numeri utilizzati sono i seguenti:

 **HDInsight 1.6 (HDP 1.1)**
<table border="1">
<tr><th>Nome</th><th>Valore</th></tr>
<tr><td>DFS.http.Address</td><td>namenodehost:30070</td></tr>
<tr><td>DFS.datanode.Address</td><td>0.0.0.0:30010</td></tr>
<tr><td>DFS.datanode.http.Address</td><td>0.0.0.0:30075</td></tr>
<tr><td>DFS.datanode.Ipc.Address</td><td>0.0.0.0:30020</td></tr>
<tr><td>DFS.Secondary.http.Address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.Tracker.http.Address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.Task.Tracker.http.Address</td><td>0.0.0.0:30060</td></tr>
<tr><td>MapReduce.History.Server.http.Address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.Port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.1 e 3.0 (HDP 2.1 e 2.0)**
<table border="1">
<tr><th>Nome</th><th>Valore</th></tr>
<tr><td>indirizzo DFS.namenode.http</td><td>namenodehost:30070</td></tr>
<tr><td>indirizzo DFS.namenode.HTTPS</td><td>headnodehost:30470</td></tr>
<tr><td>DFS.datanode.Address</td><td>0.0.0.0:30010</td></tr>
<tr><td>DFS.datanode.http.Address</td><td>0.0.0.0:30075</td></tr>
<tr><td>DFS.datanode.Ipc.Address</td><td>0.0.0.0:30020</td></tr>
<tr><td>indirizzo DFS.namenode.Secondary.http</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.NodeManager.WebApp.Address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.Port</td><td>30111</td></tr>
</table><br>

###<a name="dependencies"></a>Dipendenze

Le dipendenze seguenti sono stati aggiunti in HDInsight 3. x (HDP2.x):

* servlet guice
* optiq-core
* javax.inject
* attivazione
* jsr305
* geronimo-jaspic_1.0_spec
* lug-slf4j
* linguaggio xmlbuilder
* Ant
* compilatore Commons
* api jdo
* Commons math3
* paranamer
* implementazione di jaxb
* stringtemplate
* eigenbase xom
* Jersey servlet
* exec Commons
* api jaxb
* server di tutto alla banchina
* janino
* xercesImpl
* optiq avatica
* jta
* proprietà eigenbase
* groovy tutti
* hamcrest-core
* posta elettronica
* linq4j
* jpam
* Jersey client
* aopalliance
* geronimo-annotation_1.0_spec
* pulsante di visualizzazione ant
* Jersey guice
* API XML
* api stax
* ASM commons
* ASM albero
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni-all
* velocità
* jettison
* reattiva java
* alla banchina tutti
* creativecommons.org dbcp

Le dipendenze seguenti non esistono più in HDInsight 3. x (HDP2.x):

* jdeb
* KFS
* sqlline
* Edera
* aspectjrt
* JSON
* principali
* api jdo2
* avro mapred
* datanucleus enhancer
* JSP
* api di registrazione Creative Commons
* Commons matematiche
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* reattiva

###<a name="version-changes"></a>Modifiche alla versione

Le seguenti modifiche versione sono state apportate tra HDInsight 2. x (HDP1.x) e HDInsight 3. x (HDP2.x):

* metrica-core: ['2.1.2'] -> ["3.0.0']
* derbynet: ['10.4.2.0'] -> ["10.10.1.1']
* datanucleus: ['rdbms-3.0.8'] -> ["rdbms-3.2.9']
* compilatore jasper: ['5.5.12'] -> ["5.5.23"]
* Log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ["10.10.1.1']
* httpcore: ['4.2.4'] -> ["4.2.5']
* HSQLDB: ['1.8.0.10'] -> ["2.0.0']
* jets3t: ['0.6.1'] -> ["0.9.0"]
* linguaggio protobuf: ['2.4.1'] -> ["2.5.0"]
* Derby: ['10.4.2.0'] -> ["10.10.1.1']
* jasper: ['runtime-5.5.12'] -> ["runtime-5.5.23']
* Commons daemon: ['1.0.1'] -> ["1.0.13"]
* datanucleus-core: ['3.0.9'] -> ["3.2.10']
* jdo di api datanucleus: ['3.0.7'] -> ["3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ["3.4.5.2.1.3.0-1948"]
* bonecp: ['0.7.1.RELEASE'] -> ["
* 0.8.0.RELEASE']


### <a name="drivers"></a>Driver
Il driver di Database Connnectivity JDBC (Java) per SQL Server viene utilizzato internamente HDInsight e non viene usato per azioni esterne. Se si vuole connettere a HDInsight mediante la connettività ODBC (Open Database), utilizzare il driver ODBC Hive Microsoft. Per ulteriori informazioni, vedere [Connettere Excel a HDInsight con il Driver ODBC Hive Microsoft](hdinsight-connect-excel-hive-odbc-driver.md).


### <a name="bug-fixes"></a>Correzioni di bug

Con questa versione, è stato aggiornato seguenti HDInsight versioni diverse correzioni di bug:

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)


## <a name="hortonworks-release-notes"></a>Note sulla versione Hortonworks

Note sulla versione per le piattaforme di dati di Hortonworks (HDPs) utilizzati dal cluster a versione HDInsight sono disponibili nei seguenti percorsi:

* HDInsight versione 3.1 utilizza una distribuzione Hadoop basato su [Piattaforma dati Hortonworks 2.1.7][hdp-2-1-7]. Questo è il cluster Hadoop predefinito creato al portale di Azure dopo 7/11/2014. 3.1 HDInsight cluster creati prima della [Piattaforma dati Hortonworks 2.1.1] utilizzato in base a 7/11/2014[hdp-2-1-1]

* HDInsight versione 3.0 utilizza una distribuzione Hadoop basato su [Hortonworks dati piattaforma 2.0][hdp-2-0-8].

* HDInsight versione 2.1 utilizza una distribuzione Hadoop basato su [Hortonworks dati piattaforma 1.3][hdp-1-3-0].

* HDInsight versione 1.6 utilizza una distribuzione Hadoop basato su [Hortonworks dati piattaforma 1.1][hdp-1-1-0].

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[nuget-link]: https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/

[webpi-link]: http://go.microsoft.com/?linkid=9811175&clcid=0x409

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
 
