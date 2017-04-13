<properties
pageTitle="Come eliminare un cluster HDInsight | Azure"
description="Informazioni sui diversi modi che è possibile eliminare un cluster di HDInsight."
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

#<a name="how-to-delete-an-hdinsight-cluster"></a>Come eliminare un cluster di HDInsight

HDInsight cluster fatturazione viene avviato dopo un cluster viene creato e si arresta quando il cluster viene eliminato ed è proporzionale al minuto, pertanto è sempre necessario eliminare il cluster quando non è più in uso. In questo documento si imparerà a eliminare un cluster tramite il portale di Azure, PowerShell Azure e CLI Azure.

> [AZURE.IMPORTANT] Eliminare un cluster di HDInsight, gli account di archiviazione Azure associati al cluster non viene eliminato. In questo modo è possibile mantenere e riutilizzare i dati memorizzati dal cluster.

##<a name="azure-portal"></a>Portale di Azure

1. Accesso al [portale di Azure](https://portal.azure.com) e selezionare il cluster HDInsight. Se il cluster HDInsight non viene aggiunta al dashboard, è possibile cercarlo per nome utilizzando il campo di ricerca (icona lente di ingrandimento), sul lato destro della barra di spostamento.

    ![funzione di ricerca](./media/hdinsight-delete-cluster/navbar.png)

2. Una volta e l'aperto per il cluster, selezionare l'icona di __eliminazione__ . Quando richiesto, selezionare __Sì__ per eliminare il cluster.

    ![icona Elimina](./media/hdinsight-delete-cluster/deletecluster.png)

##<a name="azure-powershell"></a>PowerShell Azure

Da un prompt di PowerShell, usare il comando seguente per eliminare il cluster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Sostituire __nome cluster__ con il nome del cluster HDInsight.

##<a name="azure-cli"></a>CLI Azure

Da un prompt, utilizzare le operazioni seguenti per eliminare il cluster:

    azure hdinsight cluster delete CLUSTERNAME
    
Sostituire __nome cluster__ con il nome del cluster HDInsight.
