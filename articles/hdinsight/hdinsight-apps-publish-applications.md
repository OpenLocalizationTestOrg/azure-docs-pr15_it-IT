<properties
    pageTitle="Pubblicare applicazioni HDInsight | Microsoft Azure"
    description="Informazioni su come creare e pubblicare le applicazioni di HDInsight."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/18/2016"
    ms.author="jgao"/>

# <a name="publish-hdinsight-applications-into-the-azure-marketplace"></a>Pubblicare le applicazioni di HDInsight in Azure Marketplace

Un'applicazione HDInsight è un'applicazione che gli utenti possono installare in un cluster basati su Linux HDInsight. Queste applicazioni da Microsoft, fornitori di software indipendenti (ISV) o per se stessi. In questo articolo si imparerà pubblicare un'applicazione di HDInsight in Azure Marketplace.  Per informazioni generali sulla pubblicazione in Azure Marketplace, vedere [pubblicare un'offerta di Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

HDInsight applicazioni utilizzano il modello di *Visualizzare il proprio licenza (BYOL)* , dove applicazione provider è responsabile della gestione delle licenze l'applicazione agli utenti finali e gli utenti finali vengono addebitati solo da Azure per le risorse che creano, ad esempio il cluster HDInsight e macchine virtuali/nodi. In questa fase, fatturazione per l'applicazione non viene eseguita tramite Azure.

Altra applicazione HDInsight correlati articolo:

- [HDInsight installare applicazioni](hdinsight-apps-install-applications.md): informazioni su come installare un'applicazione di HDInsight per i cluster.
- [Installare applicazioni personalizzate HDInsight](hdinsight-apps-install-custom-applications.md): informazioni su come installare e testare applicazioni HDInsight personalizzate.

 
## <a name="prerequisites"></a>Prerequisiti

Per inviare l'applicazione personalizzata Marketplace, è necessario avere creato e testare l'applicazione personalizzata. Vedere gli articoli seguenti:

- [Installare applicazioni personalizzate HDInsight](hdinsight-apps-install-custom-applications.md): informazioni su come installare e testare applicazioni HDInsight personalizzate.

È necessario avere registrare anche l'account di sviluppo. Vedere [pubblicare un'offerta di Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) e [creare un account Microsoft per sviluppatori](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## <a name="define-application"></a>Definizione dell'applicazione

Sono necessari due passaggi per la pubblicazione alle applicazioni di Azure Marketplace.  Prima di definire un file di **createUiDef.json** per indicare quali cluster l'applicazione è compatibile con; e quindi si pubblica il modello dal portale di Azure. Di seguito è riportato un file di createUiDef.json.

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


|Campo  | Descrizione   | Valori possibili|
|-------|---------------|----------------|
|tipi di  | I tipi di cluster che l'applicazione è compatibile con.    |Hadoop, HBase, eccesso, motori (o qualsiasi combinazione di questi)|
|livelli  | I livelli di cluster che l'applicazione è compatibile con.    |Standard, Premium (o entrambe)|
|versioni|  I tipi di cluster HDInsight che l'applicazione è compatibile con.    |3.4|

## <a name="package-application"></a>Applicazione del pacchetto

Creare un file zip contenente tutti i file necessari per installare le applicazioni di HDInsight. È necessario il file zip [nell'applicazione di pubblicazione](#publish-application).

- [createUiDefinition.json](#define-application).
- mainTemplate.json. Visualizzare un esempio su [Installazione applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md).

    >[AZURE.IMPORTANT] Il nome dei nomi di script di installazione di applicazione deve essere univoco per un determinato cluster con il formato seguente. Inoltre qualsiasi installare e disinstallare le azioni script dovrà essere idempotente, ovvero gli script può essere chiamato repeatly durante la produzione lo stesso risultato.
    
    >   nome":" [concatena ("v0 di installazione tonalità ','-', uniquestring('applicationName')]"
        
    >Si noti sono presenti tre parti il nome dello script:
        
    >   1. Prefisso nome script che comprende il nome dell'applicazione o un nome i contenuti pertinenti per l'applicazione.
    >   2. Risposte "-" per migliorare la leggibilità.
    >   3. Funzioni stringa univoca con il nome dell'applicazione come parametro.

    >   Un esempio è le estremità sopra la diventando: tonalità-installa-v0-4wkahss55hlas nell'elenco di azioni script persistente. Per un payload JSON di esempio, vedere [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).

- Script di tutte le necessarie.

> [AZURE.NOTE] I file dell'applicazione (inclusi file dell'applicazione web eventuale) può trovarsi in qualsiasi endpoint accessibile al pubblico.

## <a name="publish-application"></a>Pubblicare applicazione

Per pubblicare un'applicazione di HDInsight, procedere come segue:

1. Accedere al [portale di pubblicazione di Azure](https://publish.windowsazure.com/).
2. Fare clic su **modelli di soluzioni** da sinistra per creare un nuovo modello di soluzione.
3. Immettere un titolo e quindi fare clic su **Crea un nuovo modello di soluzione**.
3. Fare clic su **Centro per sviluppatori di creare account e partecipare al programma Azure** per registrare la propria azienda, se non è fatto.  Vedere [creare un account Microsoft per sviluppatori](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
4. Fare clic su **Definisci alcune topologie per iniziare**. Un modello di soluzione è un "parent" a tutte le topologie. È possibile definire più topologie in un modello di offerta/soluzione. Quando viene inserita un'offerta di gestione temporanea, viene inserito con tutti i relative topologie. 
4. Immettere un nome di topologia e quindi fare clic sul segno.
5. Immettere una nuova versione e quindi fare clic sul segno.
6. Caricare il file zip preparato [nell'applicazione di pacchetto](#package-application).  
7. Fare clic su **richiesta di certificazione**. Il team di certificazione Microsoft verrà esaminare i file e certificare la topologia.

## <a name="next-steps"></a>Passaggi successivi

- [HDInsight installare applicazioni](hdinsight-apps-install-applications.md): informazioni su come installare un'applicazione di HDInsight per i cluster.
- [Installare applicazioni personalizzate HDInsight](hdinsight-apps-install-custom-applications.md): informazioni su come distribuire un'applicazione di HDInsight non pubblicata in HDInsight.
- [HDInsight basati su Linux personalizzare cluster tramite Script azione](hdinsight-hadoop-customize-cluster-linux.md): informazioni su come utilizzare azione Script per installare altre applicazioni.
- [In base a creare Linux Hadoop cluster in uso dei modelli di gestione risorse di Azure HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informazioni su come modelli di Manager delle risorse per creare cluster HDInsight chiamata.
- [Utilizzare i nodi bordo vuoto HDInsight](hdinsight-apps-use-edge-node.md): informazioni su come utilizzare un nodo del bordo vuoto per l'accesso HDInsight cluster, verifica delle applicazioni HDInsight e hosting di applicazioni HDInsight.

