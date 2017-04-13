<properties
   pageTitle="Creare cluster Hadoop basato su Windows in uso CLI Azure HDInsight"
    description="Informazioni su come creare cluster per Azure HDInsight con Azure CLI."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-cli"></a>Creare cluster Hadoop basato su Windows in uso CLI Azure HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Informazioni su come creare cluster HDInsight usa CLI Azure. Per la creazione di altri cluster caratteristiche e strumenti fare clic su Seleziona scheda nella parte superiore di questa pagina oppure [metodi di creazione Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

##<a name="prerequisites"></a>Prerequisiti:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Prima di iniziare le istruzioni fornite in questo articolo, è necessario disporre le operazioni seguenti:

- **Abbonamento azure**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Azure CLI**.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

### <a name="access-control-requirements"></a>Requisiti di controllo accesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="connect-to-azure"></a>Connettersi a Azure

Per connettersi a Azure, usare il comando seguente:

    azure login

Per ulteriori informazioni sull'autenticazione tramite un account aziendale o dell'istituto di istruzione, vedere [connettersi a un abbonamento Azure da CLI Azure](../xplat-cli-connect.md).

Per passare alla modalità ARM, usare il comando seguente:

    azure config mode arm

Per assistenza, utilizzare l'opzione **-h** .  Per esempio:

    azure hdinsight cluster create -h

##<a name="create-clusters"></a>Creare cluster

È necessaria una gestione risorse Azure (ARM) e un account di archiviazione Blob Azure prima di creare un cluster di HDInsight. Per creare un cluster di HDInsight, è necessario specificare le operazioni seguenti:

- **Gruppo di risorse Azure**: account A dati Lake Analitica deve essere creato all'interno di un gruppo di risorse di Azure. Gestione risorse di Azure consente di gestire le risorse dell'applicazione come un gruppo. È possibile distribuire, aggiornare o eliminare tutte le risorse per l'applicazione in un'operazione su una singola e coordinata.

    Per visualizzare un elenco di gruppi di risorse nell'abbonamento:

        azure group list

    Per creare un nuovo gruppo di risorse:

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Nome cluster HDInsight**

- **Posizione**: una delle aree di dati di Azure che supporta i cluster HDInsight. Per un elenco delle posizioni supportati, vedere [prezzi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

- **Account di archiviazione predefinito**: HDInsight utilizza un contenitore di spazio di archiviazione Blob Azure come file system predefinito. Prima di creare un cluster di HDInsight, è necessario un account di archiviazione Azure.

    Per creare un nuovo account di archiviazione Azure:

        azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS

    > [AZURE.NOTE]Account di archiviazione necessario collocato con HDInsight nell'interfaccia di dati.
    > Il tipo di account di archiviazione non può essere ZRS, perché ZRS non supporta tabella.

    Per informazioni sulla creazione di un account di archiviazione Azure tramite il portale di Azure, vedere [creare], gestire o eliminare un account di archiviazione [azure-creare-storageaccount].

    Se è già presente un account di archiviazione ma non conoscono il nome dell'account e la chiave account, è possibile utilizzare i comandi seguenti per recuperare le informazioni:

        -- Lists Storage accounts
        azure storage account list
        -- Shows a Storage account
        azure storage account show "<Storage Account Name>"
        -- Lists the keys for a Storage account
        azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

    Per informazioni dettagliate su come ottenere le informazioni tramite il portale di Azure, vedere la sezione "Gestire il proprio account di archiviazione" degli [account di archiviazione su Azure](../storage/storage-create-storage-account#manage-your-storage-account).

- **Contenitore Blob predefinita (facoltativo)**: il comando **cluster azure hdinsight create** crea il contenitore se non esiste. Se si sceglie di creare il contenitore in anticipo, è possibile utilizzare il comando seguente:

    creare il contenitore di archiviazione Azure - nome account "<Storage Account Name>"-chiave account <Storage Account Key> [nomecontenitore]

Dopo avere inserito l'account di archiviazione preparato, si è pronti creare un cluster:


    azure hdinsight cluster create -g <Resource Group Name> -c <HDInsight Cluster Name> -l <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --workerNodeCount 2 --headNodeSize Large --workerNodeSize Large --defaultStorageAccountName <Azure Storage Account Name>.blob.core.windows.net --defaultStorageAccountKey "<Default Storage Account Key>" --defaultStorageContainer <Default Blob Storage Container> --userName admin --password "<HTTP User Password>" --rdpUserName <RDP Username> --rdpPassword "<RDP User Password" --rdpAccessExpiry "03/01/2016"


##<a name="create-clusters-using-configuration-files"></a>Creare cluster di utilizzo dei file di configurazione
In genere, si crea un cluster di HDInsight, eseguire i processi su di esso e quindi eliminare il cluster per ridurre il costo. La riga di comando offre la possibilità di salvare le configurazioni in un file, in modo da poterla riutilizzare in ogni volta che si crea un cluster.  

    azure hdinsight config create [options ] <Config File Path> <overwirte>
    azure hdinsight config add-config-values [options] <Config File Path>
    azure hdinsight config add-script-action [options] <Config File Path>

Esempio: Creare un file di configurazione che contiene un'azione script per eseguire quando si crea un cluster.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"
    azure hdinsight cluster create --configurationPath "C:\myFiles\configFile.config"

##<a name="create-clusters-with-script-action"></a>Creare cluster con azione script

Creare un file di configurazione che contiene un'azione script per eseguire quando si crea un cluster.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"

Creare un cluster con un'azione script

    azure hdinsight cluster create -g myarmgroup01 -l westus -y Windows --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01


Per informazioni di azione script generali, vedere [personalizzare HDInsight cluster tramite Script azione (Linux)](hdinsight-hadoop-customize-cluster.md).


## <a name="create-clusters-using-arm-templates"></a>Creare cluster di utilizzare i modelli ARM

È possibile utilizzare CLI per creare cluster chiamando modelli ARM. Vedere [distribuire con Azure CLI](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-azure-cli).

## <a name="see-also"></a>Vedere anche

- [Guida introduttiva di Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - informazioni su come iniziare a lavorare con i cluster HDInsight
- [Inviare Hadoop processi a livello di programmazione](hdinsight-submit-hadoop-jobs-programmatically.md) - informazioni su come inviare a livello di programmazione processi a HDInsight
- [Gestire i cluster Hadoop in uso CLI Azure HDInsight](hdinsight-administer-use-command-line.md)
- [Usando CLI Azure per Mac, Linux e Windows e la gestione dei servizi Azure](../virtual-machines-command-line-tools.md)
