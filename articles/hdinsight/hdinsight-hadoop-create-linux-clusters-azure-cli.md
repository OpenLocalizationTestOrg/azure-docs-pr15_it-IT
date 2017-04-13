<properties
    pageTitle="Creare cluster Hadoop, HBase o eccesso su Linux in HDInsight utilizzando CLI Azure multipiattaforma | Microsoft Azure"
    description="Informazioni su come creare cluster basati su Linux HDInsight mediante CLI Azure multipiattaforma, Gestione risorse Azure modelli e le API REST di Azure. È possibile specificare il tipo di grafico (Hadoop, HBase o eccesso) o utilizzare gli script per installare componenti personalizzati."
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
    ms.date="09/20/2016"
    ms.author="larryfr"/>

#<a name="create-linux-based-clusters-in-hdinsight-using-the-azure-cli"></a>Creare cluster basati su Linux in uso CLI Azure HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

CLI Azure è un'utilità della riga di comando multipiattaforma che consente di gestire i servizi di Windows Azure. Può essere utilizzato, insieme a modelli di gestione delle risorse di Azure, per creare un cluster di HDInsight, insieme agli account di archiviazione associato e altri servizi.

Modelli di gestione delle risorse Azure sono documenti JSON che descrivono un __gruppo di risorse__ e tutte le risorse al suo interno (ad esempio HDInsight.) Questo approccio basato sul modello consente di definire tutte le risorse necessarie per HDInsight in un modello. Consente inoltre di gestire le modifiche al gruppo intero tramite __distribuzioni__, quale applicare le modifiche all'intero gruppo.

La procedura descritta in questo documento illustrata il processo di creazione di un nuovo cluster HDInsight utilizzando CLI Azure e un modello.

> [AZURE.IMPORTANT] La procedura descritta in questo documento utilizza il numero predefinito dei nodi di lavoro (4) per un cluster di HDInsight. Se si prevede di più di 32 nodi di lavoro (durante la creazione di cluster o modificando il cluster), è necessario selezionare una dimensione di nodo principale con almeno 8 core e 14 GB di ram.
>
> Per ulteriori informazioni su costi associati e le dimensioni massime nodo, vedere [prezzi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="prerequisites"></a>Prerequisiti

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __Azure CLI__. La procedura descritta in questo documento ultimo sono stata testata con Azure CLI versione 0.10.1.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 


### <a name="access-control-requirements"></a>Requisiti di controllo accesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="log-in-to-your-azure-subscription"></a>Accedere al proprio abbonamento Azure

Seguire la procedura descritta in [connessione a un abbonamento Azure dall'interfaccia della riga di comando Azure Azure CLI ()](../xplat-cli-connect.md) e connettersi al proprio abbonamento utilizzando il metodo di __accesso__ .

##<a name="create-a-cluster"></a>Creare un cluster

Dopo l'installazione e configurazione di CLI Azure, la procedura seguente deve essere eseguita da un prompt dei comandi, shell o sessione terminal.

1. Per eseguire l'autenticazione all'abbonamento Azure, usare il comando seguente:

        azure login

    Viene chiesto di specificare il nome e la password. Se si hanno più abbonamenti Azure, utilizzare `azure account set <subscriptionname>` per impostare l'abbonamento che utilizzano i comandi CLI Azure.

3. Passare alla modalità di gestione di risorse Azure utilizzando il comando seguente:

        azure config mode arm

4. Creare un gruppo di risorse. In questo gruppo di risorse conterrà cluster HDInsight e lo spazio di archiviazione account associato.

        azure group create groupname location
        
    * Sostituire __NomeGruppo__ con un nome univoco per il gruppo. 
    * Sostituire __posizione__ con la propria area geografica che si desidera creare il gruppo. 
    
        Per un elenco di percorsi validi, utilizzare la `azure location list` comando e quindi utilizzare uno dei percorsi di colonna __del nome__ .

5. Creare un account di archiviazione. Questo account di archiviazione da utilizzare come spazio di archiviazione predefinito per il cluster HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
        
     * Sostituire __NomeGruppo__ con il nome del gruppo creato nel passaggio precedente.
     * Sostituire __posizione__ nella stessa posizione utilizzata nel passaggio precedente. 
     * Sostituire __storagename__ con un nome univoco per l'account di archiviazione.
     
     > [AZURE.NOTE] Per ulteriori informazioni sui parametri utilizzati in questo comando, utilizzare `azure storage account create -h` per visualizzare la Guida per questo comando.

5. Recuperare la chiave utilizzata per accedere all'account di archiviazione.

        azure storage account keys list -g groupname storagename
        
    * Sostituire __NomeGruppo__ con il nome del gruppo di risorse.
    * Sostituire __storagename__ con il nome dell'account di archiviazione.
    
    Salvare il valore __chiave__ per __chiave1__i dati restituiti.

6. Creare un cluster di HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Sostituire __NomeGruppo__ con il nome del gruppo di risorse.

    * Sostituire __Hadoop__ con il tipo di grafico che si desidera creare. Ad esempio `Hadoop`, `HBase`, `Storm` o `Spark`.

        > [AZURE.IMPORTANT] HDInsight cluster siano disponibili numerosi tipi corrispondono al carico di lavoro o la tecnologia che il cluster è ottimizzato per. Non esiste alcun metodo supportato per creare un cluster che combina più tipi, ad esempio eccesso e HBase in un cluster. 

    * Sostituire __posizione__ nella stessa posizione utilizzata nei passaggi precedenti.

    * Sostituire __storagename__ con il nome dell'account di archiviazione.

    * Sostituire __proprietà storagekey__ insieme al tasto ottenuto nel passaggio precedente. 

    * Per la `--defaultStorageContainer` parametro, utilizzare lo stesso nome si usano per il cluster.

    * Sostituire __amministratore__ e __httppassword__ con il nome e la password che si desidera utilizzare per l'accesso al cluster tramite HTTPS.

    * Sostituire __sshuser__ e __sshuserpassword__ con il nome utente e la password che si desidera utilizzare per l'accesso al cluster utilizzando SSH

    Possono richiedere alcuni minuti per il processo di creazione cluster alla fine. In genere circa 15.

##<a name="next-steps"></a>Passaggi successivi

Ora che è stato creato correttamente un cluster di HDInsight usa CLI Azure, utilizzare le operazioni seguenti per informazioni su come lavorare con i cluster:

###<a name="hadoop-clusters"></a>Hadoop cluster

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Utilizzare maialino con HDInsight](hdinsight-use-pig.md)
* [Utilizzare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>HBase cluster

* [Guida introduttiva a HBase in HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Sviluppo di applicazioni Java per HBase in HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>Cluster eccesso

* [Sviluppare topologie Java per eccesso in HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilizzare i componenti di Python in eccesso in HDInsight](hdinsight-storm-develop-python-topology.md)
* [Distribuire e monitorare topologie con eccesso su HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)
