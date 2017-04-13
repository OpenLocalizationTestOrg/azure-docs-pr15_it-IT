<properties
pageTitle="Eseguire la migrazione da HDInsight basato su Windows a basati su Linux HDInsight | Azure"
description="Informazioni su come eseguire la migrazione da un cluster HDInsight basato su Windows a un cluster basati su Linux HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/28/2016"
ms.author="larryfr"/>

#<a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Eseguire la migrazione da un cluster HDInsight basato su Windows a un cluster basato su Linux

HDInsight basato su Windows offre un modo semplice per utilizzare Hadoop nel cloud, si potrebbe scoprire che è necessario un cluster basato su Linux per sfruttare le tecnologie necessari per la soluzione e strumenti. Molti aspetti ecosistema Hadoop vengono sviluppati in sistemi basati su Linux e alcune potrebbero non essere disponibile per l'uso con HDInsight basato su Windows. Inoltre, molti libri, video e materiale di formazione si presuppone che si usa un sistema Linux quando si lavora con Hadoop.

In questo documento vengono fornite informazioni dettagliate sulle differenze tra HDInsight in Windows e Linux e indicazioni su come eseguire la migrazione carichi di lavoro esistente in un cluster basato su Linux.

> [AZURE.NOTE] HDInsight cluster utilizzare Ubuntu a lungo termine supporto (risultati) del sistema operativo per i nodi del cluster. Per informazioni sulla versione di Ubuntu disponibili con HDInsight, insieme ad altre informazioni di controllo delle versioni componente, vedere [versioni componenti HDInsight](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Attività successive alla migrazione

Il flusso di lavoro per la migrazione è come indicato di seguito.

![Diagramma di flusso di lavoro di migrazione](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1.  Leggere ogni sezione di questo documento per comprendere le modifiche che potrebbero essere necessari per la migrazione del flusso di lavoro esistente, processi e così via in un cluster di Linux.

2.  Creare un cluster basato su Linux come ambiente assurance test/qualità. Per ulteriori informazioni sulla creazione di un cluster basato su Linux, vedere [cluster basati su Linux creare in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3.  Copiare processi esistenti, origini dati e sink nel nuovo ambiente. Visualizzare i dati di copia nella sezione ambiente di test per altri dettagli.

4.  Eseguire il test di convalida per assicurarsi che i processi funzionino come previsto nel nuovo cluster.

Dopo aver verificato che tutto funzioni come previsto, pianificare i tempi di inattività per la migrazione. Durante il tempo di inattività, eseguire le operazioni seguenti.

1.  Eseguire il backup temporanei dati memorizzati localmente nei nodi del cluster. Ad esempio, se i dati archiviati direttamente in un nodo principale.

2.  Eliminare il cluster basato su Windows.

3.  Creare un cluster basato su Linux utilizzando allo stesso archivio di dati predefinito utilizzato cluster basato su Windows. In questo modo il nuovo cluster continuare a lavorare con i dati di produzione esistenti.

4.  Importare i dati temporanei che è stato eseguito il backup.

5.  Inizio processi/continuare con il nuovo cluster di elaborazione.

### <a name="copy-data-to-the-test-environment"></a>Copiare i dati per l'ambiente di testing

Sono disponibili molti metodi per copiare i dati e processi, tuttavia i due descritte in questa sezione sono i metodi più semplici di direttamente Sposta i file in un cluster di test.

#### <a name="hdfs-dfs-copy"></a>Copia HDFS DFS

È possibile utilizzare il comando Hadoop HDFS copiare direttamente dati dall'archivio per il cluster di produzione esistente, allo spazio di archiviazione per un nuovo cluster di test utilizzando la procedura seguente.

1. Trovare le informazioni dello spazio di archiviazione contenitore account e predefinito per il cluster esistente. È possibile eseguire questa operazione utilizzando il seguente script di PowerShell Azure.

        $clusterName="Your existing HDInsight cluster name"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
        write-host "Default container: $clusterInfo.DefaultStorageContainer"

2. Seguire i passaggi descritti in cluster basati su Linux creare HDInsight documento per creare un nuovo ambiente di testing. Interrompere prima di creare il grafico e selezionare invece **Facoltativi**.

3. Selezionare **Gli account di archiviazione collegate**e il facoltativi.

4. Selezionare **Aggiungi una chiave di spazio di archiviazione**e quando richiesto, selezionare l'account di archiviazione restituiti dallo script di PowerShell nel passaggio 1. Fare clic su **Seleziona** su ogni blade per consentirne la chiusura. Infine, creare il cluster.

5. Dopo aver creato il cluster, connettersi utilizzando **SSH.** Se non ha familiarità con l'uso di SSH con HDInsight, vedere uno degli articoli seguenti.

    * [Usare SSH con basati su Linux HDInsight dai client Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * [Usare SSH con basati su Linux HDInsight dai client Linux, Unix e Mac](hdinsight-hadoop-linux-use-ssh-unix.md)

6. Da sessione SSH, usare il comando seguente per copiare il file dell'account di archiviazione collegato al nuovo account di archiviazione predefinito. Sostituire contenitore e dell'ACCOUNT con il contenitore e informazioni sull'account restituiti dallo script di PowerShell nel passaggio 1. Sostituire il percorso di dati con il percorso di un file di dati.

        hdfs dfs -cp wasbs://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location

    [AZURE.NOTE] Se la struttura di directory che contiene i dati non esiste sull'ambiente di test, è possibile creare tramite il comando seguente.

        hdfs dfs -mkdir -p /new/path/to/create

    Il `-p` parametro consente la creazione di tutte le directory nel percorso.

#### <a name="direct-copy-between-azure-storage-blobs"></a>Copia diretta tra Azure archiviazione BLOB

In alternativa, è consigliabile utilizzare il `Start-AzureStorageBlobCopy` cmdlet di PowerShell Azure copiare BLOB tra gli account di archiviazione all'esterno di HDInsight. Per ulteriori informazioni, vedere come gestire sezione BLOB Azure dell'uso di PowerShell Azure con lo spazio di archiviazione di Azure.

##<a name="client-side-technologies"></a>Tecnologie sul lato client

In generale, tecnologie sul lato client, ad esempio [i cmdlet di PowerShell di Azure](../powershell-install-configure.md), [CLI Azure](../xplat-cli-install.md) o [.NET SDK per Hadoop](https://hadoopsdk.codeplex.com/) continuerà a funzionano con i cluster basati su Linux, come si basano su API REST che sono le stesse in entrambi i tipi di cluster del sistema operativo.

##<a name="server-side-technologies"></a>Tecnologie sul lato server

Nella tabella seguente vengono fornite indicazioni su migrazione componenti lato server che sono costituite da finestre specifiche.

| Se si utilizza questa tecnologia... | Operazione da eseguire... |
| ----- | ----- |
| **PowerShell** (script sul lato server, incluse le azioni Script utilizzato durante la creazione di cluster) | Riscrittura come Bash script. Per le azioni di Script, vedere [basati su Linux personalizzare HDInsight con le azioni Script](hdinsight-hadoop-customize-cluster-linux.md) e lo [sviluppo di azione Script per HDInsight basati su Linux](hdinsight-hadoop-script-actions-linux.md). |
| **CLI Azure** (script sul lato server) | Mentre CLI Azure è disponibile su Linux, essa non vengono preinstallato nei nodi di testa cluster HDInsight. Se è necessaria per gli script sul lato server, vedere [installare CLI Azure](../xplat-cli-install.md) per informazioni sull'installazione in piattaforme basate su Linux. |
| **Componenti di .NET** | .NET non è completamente supportata nei cluster basati su Linux HDInsight. Basati su Linux eccesso nella HDInsight cluster creati dopo il supporto di 28/10/2017 topologie c# eccesso utilizzando il framework degli SCP.NET. Ulteriore supporto per .NET verrà aggiunti in futuro aggiornamenti. |
| **Componenti di Win32 o altre tecnologie solo Windows** | Indicazioni dipendono dal componente o tecnologia; è possibile trovare una versione compatibile con Linux o potrebbe essere necessario trovare una soluzione alternativa o riscrittura questo componente. |

##<a name="cluster-creation"></a>Creazione di cluster

In questa sezione vengono fornite informazioni sulle differenze tra la creazione di cluster.

### <a name="ssh-user"></a>SSH utente

Cluster HDInsight basati su Linux usano il protocollo **Secure Shell (SSH)** per consentire l'accesso remoto al cluster nodi. Diversamente da quanto succede cluster basati su Desktop remoto per Windows, la maggior parte dei client SSH non forniscono un'esperienza utente con interfaccia grafica, ma fornisce della riga di comando che consente di eseguire comandi sul cluster. Alcuni client (ad esempio [MobaXterm](http://mobaxterm.mobatek.net/),) offrono un browser di sistema grafica file oltre a una riga di comando remoto.

Durante la creazione di cluster, è necessario specificare un utente SSH e una **password** o **certificato di chiave pubblica** per l'autenticazione.

È consigliabile utilizzare il certificato di chiave pubblica, in quanto è più sicura rispetto all'utilizzo di una password. Autenticazione certificato funziona generarne una firma pubblico/privato e quindi fornire la chiave pubblica durante la creazione del cluster. Durante la connessione al server utilizzando SSH, la chiave privata sul client fornisce l'autenticazione per la connessione.

Per ulteriori informazioni sull'utilizzo di SSH con HDInsight, vedere:

- [Usare SSH con HDInsight dai client Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- [Usare SSH con HDInsight dai client Linux, Unix e OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

### <a name="cluster-customization"></a>Personalizzazione di cluster

**Azioni di script** usato con i cluster basati su Linux devono essere scritte in Bash script. Mentre le azioni Script può essere utilizzate durante la creazione di cluster, per cluster basati su Linux possono anche essere utilizzato per eseguire la personalizzazione dopo un cluster è verso l'alto e in esecuzione. Per ulteriori informazioni, vedere [basati su Linux personalizzare HDInsight con le azioni Script](hdinsight-hadoop-customize-cluster-linux.md) e lo [sviluppo di azione Script per HDInsight basati su Linux](hdinsight-hadoop-script-actions-linux.md).

Un'altra funzione di personalizzazione è **avvio**. Per i cluster di Windows, consente di specificare il percorso del librerie aggiuntive per l'utilizzo con Hive. Dopo la creazione di cluster sono automaticamente disponibili per l'uso con le query Hive senza la necessità di utilizzare queste raccolte `ADD JAR`.

Avvio di cluster basati su Linux non fornisce questa funzionalità. Utilizzare invece azione script descritte nelle [raccolte aggiungere Hive durante la creazione di cluster](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Reti virtuali

HDInsight basato su Windows cluster funzionano solo con le reti virtuali classico, mentre i cluster basati su Linux HDInsight richiedono reti virtuali Manager delle risorse. Se si dispone di risorse in una rete virtuale classica che cluster Linux HDInsight devono connettersi a, vedere [connessione di una rete virtuale classica a una rete virtuale Manager delle risorse](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Per ulteriori informazioni sui requisiti di configurazione per l'uso di reti virtuali Azure con HDInsight, vedere [funzionalità estendere HDInsight tramite una rete virtuale](hdinsight-extend-hadoop-virtual-network.md).

##<a name="management-and-monitoring"></a>Gestione e il monitoraggio

Molte web interfacce utente è stata utilizzata con HDInsight basato su Windows, ad esempio cronologia processi o l'interfaccia utente di filati, sono disponibili tramite Ambari. Inoltre, la visualizzazione di Hive Ambari offre un modo per eseguire query Hive tramite un web browser. L'interfaccia utente Web Ambari è disponibile nei cluster basati su Linux in https://CLUSTERNAME.azurehdinsight.net.

Per ulteriori informazioni sull'utilizzo di Ambari, vedere i documenti seguenti:

- [Ambari Web](hdinsight-hadoop-manage-ambari.md)

- [API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Avvisi Ambari

Ambari dispone di un sistema di avviso che può indicare potenziali problemi con il cluster. Avvisi vengono visualizzati come voci gialle o rosse nell'interfaccia utente Web Ambari, ma è possibile recuperare anche tramite l'API REST.

> [AZURE.IMPORTANT] Gli avvisi Ambari indicano che in questo caso *può* essere un problema, non che in questo caso *è* verificato un problema. Ad esempio, che venga visualizzato un avviso che non è possibile accedere HiveServer2, anche se è possibile accedere normalmente.
>
> Numero di avvisi implementata come query basati su intervalli in un servizio e aspettarsi una risposta all'interno di un intervallo di tempo specifico. Per consentire l'avviso non necessariamente significa che il servizio verso il basso, semplicemente che non restituisca risultati entro i tempi previsti.

In generale, è opportuno valutare se è stati che si verificano per un periodo, un avviso o rispecchia problemi dell'utente che in precedenza sono stati segnalati con il cluster prima di eseguire azioni su di esso.

##<a name="file-system-locations"></a>Posizioni dei file di sistema

Il file system cluster Linux è disposto in modo diverso da cluster HDInsight basato su Windows. Utilizzare la tabella seguente per individuare comune i file.

| È necessario trovare... | Si trova... |
| ----- | ----- |
| Configurazione | `/etc`. Per esempio`/etc/hadoop/conf/core-site.xml` |
| File di log | `/var/logs` |
| Piattaforma dati Hortonworks (HDP) | `/usr/hdp`. Esistono due directory disponibile in questo caso, ovvero la versione corrente di HDP (ad esempio `2.2.9.1-1`,) e `current`. Il `current` directory contiene symbolic collegamenti a file e directory che si trovano nella directory di numero di versione e viene fornita come un modo comodo per accedere ai file HDP rispetto alla versione numero cambierà man mano che la versione HDP viene aggiornata. |
| Hadoop streaming.jar | `/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

In generale, se si conosce il nome del file, è possibile utilizzare il comando seguente da una sessione SSH per trovare il percorso del file:

    find / -name FILENAME 2>/dev/null

È anche possibile utilizzare caratteri jolly con il nome del file. Ad esempio `find / -name *streaming*.jar 2>/dev/null` restituirà il percorso dei file di vaso contenenti la parola streaming come parte del nome del file.

##<a name="hive-pig-and-mapreduce"></a>Hive, maialino e MapReduce

Maialino e MapReduce carichi di lavoro sono molto simili nei cluster basati su Linux, la differenza principale se si utilizza Desktop remoto per connettersi a un cluster basato su Windows ed eseguire processi, si utilizzerà SSH con i cluster basati su Linux.

- [Utilizzare maialino con SSH](hdinsight-hadoop-use-pig-ssh.md)

- [Utilizzare MapReduce con SSH](hdinsight-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

Il diagramma seguente vengono fornite indicazioni sulla migrazione i carichi di lavoro di Hive.

| Basato su Windows utilizza... | In basati su Linux... |
| ----- | ----- |
| **Editor hive** | [Visualizzazione hive Ambari](hdinsight-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;`Per abilitare Tez | Tez è il motore di esecuzione predefinito per i cluster basati su Linux, in modo che l'istruzione set non è più necessaria. |
| File CMD o script nel server richiamato come parte di un processo Hive | Utilizzare gli script Bash |
| `hive`comando dal desktop remoto | Utilizzare [Beeline](hdinsight-hadoop-use-hive-beeline.md) o [Hive da una sessione SSH](hdinsight-hadoop-use-hive-ssh.md) |

##<a name="storm"></a>Eccesso

| Basato su Windows utilizza... | In basati su Linux... |
| ----- | ----- |
| Dashboard eccesso | Dashboard di eccesso non è disponibile. Vedere [distribuire e gestire il topologie su basati su Linux HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md) per informazioni su come inviare topologie |
| Eccesso dell'interfaccia utente | L'interfaccia utente eccesso è disponibile in https://CLUSTERNAME.azurehdinsight.net/stormui |
| Visual Studio per creare, distribuire e gestire topologie c# o ibrido | Visual Studio può essere utilizzato per creare, distribuire e gestire c# (SCP.NET) o topologie ibrido in eccesso basati su Linux nei cluster HDInsight creati dopo 28/10/2017. |

##<a name="hbase"></a>HBase

Nei cluster basati su Linux è padre znode per HBase `/hbase-unsecure`. È necessario impostare questa configurazione per tutti i client Java applicazioni che utilizzano API Java HBase nativa.

Per un client di esempio che consente di impostare questo valore, vedere [creare un'applicazione basato su Java HBase](hdinsight-hbase-build-java-maven.md) .

##<a name="spark"></a>Motori

Motori cluster erano disponibili nei cluster di Windows durante l'anteprima; Tuttavia, per versione ad è disponibile solo con i cluster basati su Linux. Non è un percorso di migrazione da un cluster di anteprima ad basato su Windows a un cluster di motori basati su Linux release.

##<a name="known-issues"></a>Problemi noti

### <a name="azure-data-factory-custom-net-activities"></a>Azure Factory di dati .NET le attività personalizzate

Azure Factory di dati .NET le attività personalizzate non sono attualmente supportate nei cluster basati su Linux HDInsight. Se, tuttavia, utilizzare uno dei metodi seguenti per implementare attività personalizzate come parte della pipeline alimentatore automatico.

-   Eseguire le attività .NET nel pool di Azure Batch. Vedere la sezione servizio usare Azure Batch collegati di [Usa attività personalizzata in una pipeline di Azure Data Factory](../data-factory/data-factory-use-custom-activities.md#AzureBatch)

-   Implementare l'attività come attività MapReduce. Per ulteriori informazioni, vedere [Richiamare MapReduce programmi dal produttore di dati](../data-factory/data-factory-map-reduce.md) .

### <a name="line-endings"></a>Terminazioni di riga

In generale, terminazioni nei sistemi basato su Windows utilizzano CRLF, mentre i sistemi basati su Linux utilizzano nuova riga. Se si prodotti agricoli o si aspetta, dati con terminazioni CRLF, potrebbe essere necessario modificare il produttori o consumatori per lavorare con le terminazioni di riga nuova riga.

Ad esempio utilizzando Azure PowerShell per eseguire una query HDInsight in un cluster basato su Windows restituirà dati con CRLF. La stessa query con un cluster basato su Linux restituirà nuova riga. In molti casi, questo è irrilevante consumer dei dati, ma deve essere analizzato prima della migrazione a un cluster basato su Linux.

Se si dispone di script che verrà eseguito direttamente nei nodi cluster Linux (ad esempio script Python utilizzata con Hive o un processo MapReduce), è necessario utilizzare sempre nuova riga come la fine della riga. Se si usa CRLF, è possibile riscontrare errori durante l'esecuzione di script in un cluster basato su Linux.

Se si è sicuri che gli script non contengono stringhe con caratteri CR incorporati, è possibile in blocco modificare le terminazioni di linea utilizzando uno dei metodi seguenti:

-   **Se si dispone di script che si intende caricamento al cluster**, utilizzare le seguenti istruzioni di PowerShell per modificare le terminazioni di linea da CRLF a nuova riga prima di caricare lo script in cluster.

        $original_file ='c:\path\to\script.py'
        $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
        [IO.File]::WriteAllText($original_file, $text)

-   **Se si dispone di script che sono già presenti nell'archivio utilizzata dal cluster**, è possibile utilizzare il comando seguente da una sessione SSH per cluster basato su Linux per modificare lo script.

        hdfs dfs -get wasbs:///path/to/script.py oldscript.py
        tr -d '\r' < oldscript.py > script.py
        hdfs dfs -put -f script.py wasbs:///path/to/script.py

##<a name="next-steps"></a>Passaggi successivi

-   [Informazioni su come creare cluster basati su Linux HDInsight](hdinsight-hadoop-provision-linux-clusters.md)

-   [Connettersi a un cluster basato su Linux utilizzando SSH da un client di Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

-   [Connettersi a un cluster basato su Linux utilizzando SSH da un client Linux, Unix o Mac](hdinsight-hadoop-linux-use-ssh-unix.md)

-   [Gestire un cluster basato su Linux usando Ambari](hdinsight-hadoop-manage-ambari.md)
