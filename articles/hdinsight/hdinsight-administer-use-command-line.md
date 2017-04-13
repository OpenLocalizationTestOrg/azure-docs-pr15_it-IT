<properties
    pageTitle="Gestire i cluster Hadoop usa CLI Azure | Microsoft Azure"
    description="Come usare CLI Azure per gestire i cluster Hadoop in HDIsight"
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
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Gestire i cluster Hadoop in uso CLI Azure HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Informazioni su come utilizzare l' [interfaccia di Azure riga di comando](../xplat-cli-install.md) per gestire i cluster Hadoop in Azure HDInsight. CLI Azure è implementata in Node. Può essere utilizzato in qualsiasi piattaforma che supporta Node, ad esempio Windows, Mac e Linux.

In questo articolo è descritta solo con CLI Azure HDInsight. Per una Guida generale sull'uso di Azure CLI, vedere [installare e configurare Azure CLI][azure-command-line-tools].

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questo articolo, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Azure CLI** - vedere [installare e configurare CLI Azure](../xplat-cli-install.md) per informazioni sull'installazione e configurazione.
- **Connettersi a Azure**, utilizzando il comando seguente:

        azure login

    Per ulteriori informazioni sull'autenticazione tramite un account aziendale o dell'istituto di istruzione, vedere [connettersi a un abbonamento Azure da CLI Azure](xplat-cli-connect.md).
    
- **Passare alla modalità di gestione risorse di Azure**, utilizzando il comando seguente:

        azure config mode arm

Per assistenza, utilizzare l'opzione **-h** .  Per esempio:

    azure hdinsight cluster create -h
    
##<a name="create-clusters"></a>Creare cluster

Vedere [basati su Linux creare cluster in uso CLI Azure HDInsight](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

##<a name="list-and-show-cluster-details"></a>Elenco e visualizzare i dettagli di cluster
Utilizzare i comandi seguenti per elencare e visualizzare i dettagli di cluster:

    azure hdinsight cluster list
    azure hdinsight cluster show <Cluster Name>

![HDI. CLIListCluster][image-cli-clusterlisting]


##<a name="delete-clusters"></a>Eliminare cluster

Per eliminare un cluster, utilizzare il comando seguente:

    azure hdinsight cluster delete <Cluster Name>

È anche possibile eliminare un cluster eliminando il gruppo di risorse che contiene il cluster. Si noti, verranno eliminate tutte le risorse nel gruppo tra cui l'account di archiviazione predefinito.

    azure group delete <Resource Group Name>

##<a name="scale-clusters"></a>Cluster di scala

Per modificare le dimensioni di cluster Hadoop:

    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## <a name="enabledisable-http-access-for-a-cluster"></a>Attivare o disattivare l'accesso HTTP per un cluster

    azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
    azure hdinsight cluster disable-http-access [options] <Cluster Name>

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Attivare o disattivare l'accesso RDP per un cluster

    azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
    azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


##<a name="next-steps"></a>Passaggi successivi
In questo articolo sono appreso come eseguire diverse attività amministrative di cluster HDInsight. Per ulteriori informazioni, vedere gli articoli seguenti:

* [Amministrare HDInsight tramite il portale di Azure] [hdinsight-admin-portal]
* [Amministrare HDInsight tramite PowerShell Azure] [hdinsight-admin-powershell]
* [Guida introduttiva di Azure HDInsight] [hdinsight-get-started]
* [Come usare CLI Azure] [azure-command-line-tools]


[azure-command-line-tools]: ../xplat-cli-install.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Elenco e visualizzare cluster"
