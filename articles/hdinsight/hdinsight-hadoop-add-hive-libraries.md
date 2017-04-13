<properties
pageTitle="Aggiungere librerie di Hive durante la creazione di cluster HDInsight | Azure"
description="Informazioni su come aggiungere librerie di Hive (file vaso,) a un cluster di HDInsight durante la creazione di cluster."
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
ms.date="09/20/2016"
ms.author="larryfr"/>

#<a name="add-hive-libraries-during-hdinsight-cluster-creation"></a>Aggiungere librerie di Hive durante la creazione di cluster HDInsight

Se si dispongono di raccolte che si usano di frequente con Hive nel HDInsight, il documento contiene informazioni sull'utilizzo di un'azione Script per pre-caricare le librerie durante la creazione di cluster. In questo modo, le raccolte disponibili a livello globale nell'Hive (senza la necessità di utilizzare [Aggiungere JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) per caricarli.)

##<a name="how-it-works"></a>Come funziona

Quando si crea un cluster, è possibile specificare se si desidera un'azione Script che esegue uno script nei nodi del cluster durante la creazione. Lo script in questo documento accetta un solo parametro, un percorso WASB che contiene le librerie (memorizzate come file vaso) che saranno già caricate.

Durante la creazione di cluster, lo script enumerati i file, li copia per il `/usr/lib/customhivelibs/` directory su nodi di testa e di lavoro, quindi le aggiunge al `hive.aux.jars.path` proprietà nel `core-site.xml` file. Nei cluster basati su Linux anche viene aggiornato il `hive-env.sh` file con il percorso del file.

> [AZURE.NOTE] Con le azioni di script in questo articolo, le relative raccolte rende disponibile negli scenari seguenti:
>
> * __HDInsight basati su Linux__ - quando si usa __Hive della riga di comando__, __WebHCat__ (Templeton) e __HiveServer2__.
> * __HDInsight basato su Windows__ - quando si usa __l'Hive della riga di comando__ e __WebHCat__ (Templeton).

##<a name="the-script"></a>Lo script

__Percorso di script__

Per i __cluster basati su Linux__: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Per i __cluster basato su Windows__: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

__Requisiti__

* Gli script devono essere applicati a entrambi i __nodi di testa__ e i __nodi di lavoro__.

* Vasetti da che si desidera installare devono venire archiviati in archiviazione Blob Azure in un __singolo contenitore__. 

* Account di archiviazione che contiene la raccolta di file vaso __deve__ essere collegato a cluster HDInsight durante la creazione. Può essere eseguita in uno dei due modi:

    * La in un contenitore per l'account di archiviazione predefinito per il cluster.
    
    * La in un contenitore in un contenitore di archiviazione collegato. Nel portale, ad esempio, è possibile utilizzare __Facoltativi__, __gli account di archiviazione collegato__ da aggiungere ulteriore spazio di archiviazione.

* Come parametro per l'azione Script, è necessario specificare il percorso WASB il contenitore. Ad esempio, se la JAR sono archiviate in un contenitore denominato __librerie__ in un account di archiviazione denominato __la risorsa mystorage__, il parametro deve essere __wasbs://libs@mystorage.blob.core.windows.net/__.

    > [AZURE.NOTE] In questo documento presuppone dispone sia già stato creato un account di archiviazione, blob contenitore e caricare i file. 
    >
    > Se non è creato un account di archiviazione, è possibile eseguire tramite il [portale di Azure](https://portal.azure.com). È quindi possibile utilizzare un'utilità, ad esempio [Esplora archivi Azure](http://storageexplorer.com/) per creare un nuovo contenitore nella finestra account e caricare i file.

##<a name="create-a-cluster-using-the-script"></a>Creare un cluster utilizzando lo script

> [AZURE.NOTE] La procedura seguente crea un nuovo cluster basati su Linux HDInsight. Per creare un nuovo cluster basato su Windows, selezionare __Windows__ come cluster OS durante la creazione del cluster e utilizzare lo script di Windows (PowerShell) anziché lo script bash.
> 
> Per creare un cluster questo script, è possibile usare PowerShell Azure o HDInsight .NET SDK. Per ulteriori informazioni sull'uso di questi metodi, vedere [personalizzare HDInsight cluster con le azioni Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Avviare il provisioning di un cluster utilizzando la procedura descritta in [cluster di provisioning HDInsight su Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), ma non completate il provisioning.

2. Nella e **Facoltativi** , selezionare **Le azioni Script**e fornire le informazioni come illustrato di seguito:

    * __Nome__: immettere un nome descrittivo per l'azione script.
    * __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh
    * __Testa__: selezionare questa opzione
    * __Lavoro__: selezionare questa opzione.
    * __ZOOKEEPER__: lasciare vuoto il campo.
    * __Parametri__: immettere l'indirizzo WASB nell'account contenitore e lo spazio di archiviazione che contiene il JAR. Ad esempio __wasbs://libs@mystorage.blob.core.windows.net/__.

3. Nella parte inferiore delle **Azioni di Script**, utilizzare il pulsante **selezione** per salvare la configurazione.

4. Nella e **Facoltativi** , selezionare __Gli account di archiviazione collegato__ e selezionare il collegamento __Aggiungi chiave di archiviazione__ . Selezionare l'account di archiviazione che contiene il JAR e quindi utilizzare i pulsanti __Selezionare__ per salvare le impostazioni e tornare e il __Facoltativi__ .

5. Usare il pulsante **Selezionare** nella parte inferiore della stessa e **Facoltativi** per salvare le informazioni di configurazione facoltativo.

6. Continuare il provisioning del cluster come descritto in [cluster di provisioning HDInsight su Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

Al termine della creazione di cluster, sarà possibile usare JAR aggiunti tramite questo script dall'Hive senza la necessità di utilizzare il `ADD JAR` istruzione.

##<a name="next-steps"></a>Passaggi successivi

In questo documento hanno appreso di aggiungere librerie Hive contenute nei file di vaso in un cluster di HDInsight durante la creazione di cluster. Per ulteriori informazioni sull'uso delle Hive, vedere [Usare Hive con HDInsight](hdinsight-use-hive.md)
