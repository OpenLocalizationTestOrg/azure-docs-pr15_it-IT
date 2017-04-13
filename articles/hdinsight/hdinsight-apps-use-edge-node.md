<properties
    pageTitle="Usare i nodi di un bordo vuoto in HDInsight | Microsoft Azure"
    description="Procedura per aggiungere un nodo del bordo ampty cluster HDInsight che può essere utilizzato come client e per le applicazioni di HDInsight/host di test."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    tags="azure-portal"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="use-empty-edge-nodes-in-hdinsight"></a>Usare i nodi di un bordo vuoto in HDInsight

Informazioni su come aggiungere un nodo del bordo vuota in un cluster di HDInsight basati su Linux. Un nodo del bordo vuoto è una macchina virtuale Linux con gli stessi strumenti client installato e configurato come il headnodes, ma senza servizi hadoop in esecuzione. È possibile utilizzare il nodo del bordo per l'accesso del cluster, la verifica delle applicazioni client e le applicazioni client di hosting. 

È possibile aggiungere un nodo del bordo vuoto a un cluster HDInsight esistente, in un cluster di nuovo quando si crea il cluster. Per aggiungere un nodo del bordo vuoto utilizzando il modello di gestione di risorse Azure.  Nell'esempio seguente viene illustrato come farlo utilizzando un modello:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "[variables('clusterApiVersion')]",
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "[parameters('edgeNodeSize')]"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Come mostrato nell'esempio, è possibile chiamare facoltativamente un' [azione script](hdinsight-hadoop-customize-cluster-linux.md) per eseguire un'ulteriore configurazione, ad esempio l'installazione di [Tonalità Apache](hdinsight-hadoop-hue-linux.md) in nodo del bordo.

Dopo aver creato un nodo del bordo, è possibile connettersi al livello di nodo bordo utilizzando SSH ed eseguire gli strumenti client per accedere al cluster Hadoop in HDInsight.

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Aggiungere un nodo del bordo a un cluster esistente

In questa sezione, utilizzare un modello di Manager delle risorse per aggiungere un nodo del bordo a un cluster HDInsight esistente.  In [GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode), è possibile trovare il modello di Manager delle risorse. Il modello di gestione risorse richiama uno script di azione script che si trova in https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. Lo script non esegue alcuna azione.  Si tratta per illustrare azione script chiamante da un modello di Manager delle risorse.

**Per aggiungere un nodo del bordo vuoto a un cluster esistente**

1. Creare un cluster di HDInsight se non hai ancora.  Vedere [Hadoop esercitazione: iniziare a utilizzare basati su Linux Hadoop in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Fare clic sull'immagine seguente per accedere a Azure e aprire il modello di gestione di risorse Azure nel portale di Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Configurare le proprietà seguenti:

    - Nome cluster: Immettere il nome di un cluster HDInsight esistente.
    - EDGENODESIZE: Selezionare uno dei formati macchine Virtuali.
    - EDGENODEPREFIX: Il valore predefinito è **Nuovo**.  Usa il valore predefinito, il nome del nodo di lato è **Nuovo edgenode**.  È possibile personalizzare il prefisso dal portale. È anche possibile personalizzare il nome completo del modello.


4. Fare clic su **OK** per salvare le modifiche.
5. Nel **gruppo di risorse**, selezionare un gruppo di risorse.
6. Fare clic su **note legali revisione**e quindi fare clic su **Acquista**.
7. Selezionare **Aggiungi a dashboard**e quindi fare clic su **Crea**.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Aggiungere un nodo del bordo durante la creazione di un cluster

In questa sezione, utilizzare un modello di Manager delle risorse per creare cluster HDInsight con un nodo del bordo.  In un [contenitore di spazio di archiviazione Blob Azure](http://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json)pubblico, è possibile trovare il modello di Manager delle risorse. Il modello di gestione risorse richiama uno script di azione script che si trova in https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. Lo script non esegue alcuna azione.  Si tratta per illustrare azione script chiamante da un modello di Manager delle risorse.

**Per aggiungere un nodo del bordo vuoto a un cluster esistente**

1. Creare un cluster di HDInsight se non hai ancora.  Vedere [Hadoop esercitazione: iniziare a utilizzare basati su Linux Hadoop in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Fare clic sull'immagine seguente per accedere a Azure e aprire il modello di gestione di risorse Azure nel portale di Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Configurare le proprietà seguenti:
        
    - Nome cluster: Immettere un nome per il nuovo cluster creare.
    - CLUSTERLOGINUSERNAME: Immettere il nome dell'utente Hadoop HTTP.  Il nome predefinito è **admin**.
    - CLUSTERLOGINPASSWORD: Immettere la password dell'utente Hadoop HTTP.
    - SSHUSERNAME: Immettere il nome dell'utente SSH. Il nome predefinito è **sshuser**.
    - SSHPASSWORD: Immettere la password dell'utente SSH.
    - POSIZIONE: Immettere il percorso del nome del gruppo di risorse, cluster e l'account di archiviazione predefinito.
    - CLUSTERTYPE: Il valore predefinito è **hadoop**.
    - CLUSTERWORKERNODECOUNT: Il valore predefinito è 2.
    - EDGENODESIZE: Selezionare uno dei formati macchine Virtuali.
    - EDGENODEPREFIX: Il valore predefinito è **Nuovo**.  Usa il valore predefinito, il nome del nodo di lato è **Nuovo edgenode**.  È possibile personalizzare il prefisso dal portale. È anche possibile personalizzare il nome completo del modello.

4. Fare clic su **OK** per salvare le modifiche.
5. Nel **gruppo di risorse**, immettere un nuovo nome gruppo di risorse.
6. Fare clic su **note legali revisione**e quindi fare clic su **Acquista**.
7. Selezionare **Aggiungi a dashboard**e quindi fare clic su **Crea**. 


## <a name="access-an-edge-node"></a>Accedere a un nodo del bordo

Il nodo del bordo ssh endpoint è <EdgeNodeName>. <ClusterName>-ssh.azurehdinsight.net:22.  Ad esempio nuovo-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Il nodo del bordo viene visualizzato come un'applicazione nel portale di Azure.  Il portale fornisce le informazioni per accedere al nodo bordo utilizzando SSH.

**Per verificare l'endpoint SSH nodo bordo**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il cluster HDInsight con un nodo del bordo.
3. Fare clic su **applicazioni** e il raggruppamento. Risulta nodo del bordo.  Il nome predefinito è **Nuovo edgenode**.
4. Fare clic su nodo del bordo. Risulta endpoint SSH.

**Usare Hive nel nodo del bordo**

5. Usare SSH per connettersi al nodo del bordo.  Vedere [usare SSH con basati su Linux Hadoop in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md) o [utilizzare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
6. Dopo aver connesso al livello di nodo bordo utilizzando SSH, usare il comando seguente per aprire la console Hive:

        hive
7. Eseguire il comando seguente per visualizzare le tabelle Hive del cluster:

        show tables;

## <a name="delete-an-edge-node"></a>Eliminare un nodo del bordo

È possibile eliminare un nodo del bordo dal portale di Azure.

**Per accedere a un nodo del bordo**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il cluster HDInsight con un nodo del bordo.
3. Fare clic su **applicazioni** e il raggruppamento. Viene visualizzato un elenco dei nodi di bordo.  
4. Pulsante destro del mouse nodo del bordo che si desidera eliminare e quindi fare clic su **Elimina**.
5. Fare clic su **Sì** per confermare.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite come aggiungere un nodo del bordo e su come accedere al nodo di bordo. Per ulteriori informazioni, vedere gli articoli seguenti:

- [HDInsight installare applicazioni](hdinsight-apps-install-applications.md): informazioni su come installare un'applicazione di HDInsight per i cluster.
- [Installare applicazioni personalizzate HDInsight](hdinsight-apps-install-custom-applications.md): informazioni su come distribuire un'applicazione di HDInsight non pubblicata in HDInsight.
- [Pubblicare HDInsight applicazioni](hdinsight-apps-publish-applications.md): informazioni su come pubblicare delle applicazioni HDInsight personalizzate Azure Marketplace.
- [MSDN: installare un'applicazione di HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): informazioni su come definire le applicazioni di HDInsight.
- [HDInsight basati su Linux personalizzare cluster tramite Script azione](hdinsight-hadoop-customize-cluster-linux.md): informazioni su come utilizzare azione Script per installare altre applicazioni.
- [In base a creare Linux Hadoop cluster in HDInsight utilizzare i modelli di Manager delle risorse](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informazioni su come modelli di Manager delle risorse per creare cluster HDInsight chiamata.

