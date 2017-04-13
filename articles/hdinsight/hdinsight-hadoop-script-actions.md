<properties
    pageTitle="Script di sviluppo di azione con HDInsight | Microsoft Azure"
    description="Informazioni su come personalizzare cluster Hadoop con azione Script. Azione di script può essere utilizzata per installare software aggiuntivo in esecuzione in un cluster di Hadoop o per modificare la configurazione delle applicazioni installate in un cluster."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Sviluppare script azione Script per i cluster basato su HDInsight Windows

Informazioni su come scrivere script azione Script per HDInsight. Per informazioni sull'uso di script Script azione, vedere [personalizzare HDInsight cluster tramite Script azione](hdinsight-hadoop-customize-cluster.md). Per l'articolo stesso scritto per i cluster basati su Linux HDInsight, vedere [script sviluppare Script azione per HDInsight](hdinsight-hadoop-script-actions-linux.md).

> [AZURE.NOTE] Le informazioni contenute in questo documento sono specifiche di cluster HDInsight basato su Windows. Per informazioni sull'utilizzo di azioni di script con i cluster basato su Windows, vedere [sviluppo di azione di Script con HDInsight (Linux)](hdinsight-hadoop-script-actions-linux.md).


Azione di script può essere utilizzata per installare software aggiuntivo in esecuzione in un cluster di Hadoop o per modificare la configurazione delle applicazioni installate in un cluster. Azioni di script sono script che eseguono nei nodi del cluster quando vengono distribuite HDInsight cluster e vengono eseguiti una volta nodi del cluster completare la configurazione di HDInsight. Un'azione script viene eseguita in privilegi dell'account di amministratore di sistema e fornisce diritti di accesso completo ai nodi cluster. Ogni cluster può essere fornito con un elenco delle azioni di script da eseguire nell'ordine in cui vengono specificati.

> [AZURE.NOTE] Se si verifica il messaggio di errore seguente:
>
>     System.Management.Automation.CommandNotFoundException; ExceptionMessage : The term 'Save-HDIFile' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
> È poiché non include i metodi di supporto.  Vedere [metodi di supporto per script personalizzati](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).

## <a name="sample-scripts"></a>Script di esempio

Per la creazione di cluster HDInsight nel sistema operativo Windows, l'azione Script è uno script di PowerShell Azure. Di seguito è riportato un esempio di script per configurare i file di configurazione del sito:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

Lo script accetta quattro parametri, il nome del file di configurazione, la proprietà che si desidera modificare il valore che si desidera impostare, e una descrizione. Per esempio:

    hive-site.xml hive.metastore.client.socket.timeout 90

Questi parametri verranno impostare il valore di hive.metastore.client.socket.timeout a 90 nel file hive site.xml.  Il valore predefinito è 60 secondi.

Questo script di esempio sono disponibili anche in [https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight offre diversi script per installare componenti aggiuntivi in cluster HDInsight:

Nome | Script
----- | -----
**Installare motori** | https://hdiconfigactions.BLOB.Core.Windows.NET/sparkconfigactionv03/Spark-Installer-v03.ps1. Vedere [installare e utilizzare riguardo nei cluster HDInsight][hdinsight-install-spark].
**Installare R** | https://hdiconfigactions.BLOB.Core.Windows.NET/rconfigactionv02/r-Installer-v02.ps1. Vedere [installazione e uso R su cluster HDInsight][hdinsight-r-scripts].
**Installare Solr** | https://hdiconfigactions.BLOB.Core.Windows.NET/solrconfigactionv01/solr-Installer-v01.ps1. Vedere [installare e utilizzare cluster di Solr su HDInsight](hdinsight-hadoop-solr-install.md).
- **Installare Giraph** | https://hdiconfigactions.BLOB.Core.Windows.NET/giraphconfigactionv01/giraph-Installer-v01.ps1. Vedere [installare e utilizzare cluster di Giraph su HDInsight](hdinsight-hadoop-giraph-install.md).

Azione di script può essere distribuite dal portale di Azure, PowerShell Azure o tramite HDInsight .NET SDK.  Per ulteriori informazioni, vedere [personalizzare HDInsight cluster tramite Script azione][hdinsight-cluster-customize].

> [AZURE.NOTE] Gli script di esempio funzionano solo con la versione cluster HDInsight 3.1 o versioni successive. Per ulteriori informazioni sulle versioni di cluster HDInsight, vedere [HDInsight cluster versioni](hdinsight-component-versioning.md).





## <a name="helper-methods-for-custom-scripts"></a>Metodi di supporto per script personalizzati

Metodi di supporto di script azione sono utilità che è possibile usare durante la scrittura di script personalizzati. Questi sono definiti in [https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1)e possono essere inclusi in script tramite le operazioni seguenti:

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

Ecco i metodi di supporto forniti da questo script:

Metodo di supporto | Descrizione
-------------- | -----------
**Salva HDIFile** | Scaricare un file dalla risorsa identificatore URI (Uniform specificato) in un percorso sul disco locale è associato il nodo macchine Virtuali di Azure assegnato al cluster.
**Espandere HDIZippedFile** | Estrarre un file compresso.
**Richiamare HDICmdScript** | Eseguire uno script da cmd.exe.
**Scrittura HDILog** | Scrivere l'output dello script personalizzato utilizzato per un'azione script.
**Get-servizi** | È possibile ottenere un elenco dei servizi in esecuzione nel computer in cui viene eseguito lo script.
**Get-Service** | Con il nome di servizio specifico come input, ottenere informazioni dettagliate per uno specifico servizio (il nome del servizio, ID processo, stato e così via) nel computer in cui viene eseguito lo script.
**Get-HDIServices** | È possibile ottenere un elenco di servizi HDInsight in esecuzione nel computer in cui viene eseguito lo script.
**Get-HDIService** | Con il nome di servizio HDInsight specifico come input, ottenere informazioni dettagliate per uno specifico servizio (il nome del servizio, ID processo, stato e così via) nel computer in cui viene eseguito lo script.
**Get-ServicesRunning** | È possibile ottenere un elenco di servizi in esecuzione nel computer in cui viene eseguito lo script.
**Get-ServiceRunning** | Verificare se un servizio specifico (per nome) sia in esecuzione nel computer in cui viene eseguito lo script.
**Get-HDIServicesRunning** | È possibile ottenere un elenco di servizi HDInsight in esecuzione nel computer in cui viene eseguito lo script.
**Get-HDIServiceRunning** | Verificare se un servizio HDInsight specifico (per nome) sia in esecuzione nel computer in cui viene eseguito lo script.
**Get-HDIHadoopVersion** | È possibile ottenere la versione di Hadoop installato nel computer in cui viene eseguito lo script.
**Test IsHDIHeadNode** | Verificare se il computer in cui viene eseguito lo script in un nodo principale.
**Test IsActiveHDIHeadNode** | Verificare se il computer in cui viene eseguito lo script in un nodo principale attivo.
**Test IsHDIDataNode** | Verificare se il computer in cui viene eseguito lo script in un nodo di dati.
**Modifica HDIConfigFile** | Modificare il file di configurazione hive-site.xml, core site.xml, hdfs site.xml, mapred site.xml o filati site.xml.


## <a name="best-practices-for-script-development"></a>Procedure consigliate per lo sviluppo di script

Quando si sviluppa uno script personalizzato per un cluster di HDInsight, sono disponibili diverse procedure consigliate da tenere presenti:

- Controllo per la versione Hadoop

    Solo HDInsight versione 3.1 (Hadoop 2,4) e versioni successive supportano tramite Script azione per installare componenti personalizzati in un cluster. Negli script personalizzati, utilizzare il metodo di supporto **Get-HDIHadoopVersion** per verificare la versione Hadoop prima di procedere con l'esecuzione di altre attività nello script.


- Fornire stabili collegamenti alle risorse script

    Gli utenti devono verificare che tutti gli script e altri elementi usati nella personalizzazione di un cluster restano disponibili per l'intera durata del cluster e che le versioni dei file non cambiano per la durata. Queste risorse sono necessari se il nuovo immagini dei nodi del cluster è necessario. La procedura consigliata consiste nello scaricare e archiviare tutti gli elementi in un account di archiviazione che consente di controllare l'utente. Può essere l'account di archiviazione predefinito o uno degli account di spazio di archiviazione aggiuntive specificato al momento dell'installazione per un cluster personalizzato.
    Motori e R personalizzato esempi cluster forniti nella documentazione, ad esempio, è stata introdotta una copia locale delle risorse di questo account di archiviazione: https://hdiconfigactions.blob.core.windows.net/.


- Verificare che lo script di personalizzazione cluster idempotenti

    Si devono prevede che i nodi di un cluster di HDInsight saranno ricreata l'immagine nel corso della durata di cluster. Script di personalizzazione cluster viene eseguito ogni volta che un cluster viene ricreata l'immagine. Questo script deve progettato per essere idempotente nel senso che al nuovo immagini, lo script deve assicurarsi che il cluster venga restituito allo stesso personalizzato stato che si trovava subito dopo lo script eseguito per la prima volta quando il cluster è stato creato personalmente. Ad esempio, se uno script personalizzato installata un'applicazione D:\AppLocation eseguire il primo, quindi in ogni fase di esecuzione successive al nuovo immagini, script controllare se l'applicazione è presente nel percorso D:\AppLocation prima di procedere con un'altra procedura per lo script.


- Installare componenti personalizzati nella posizione ottimale

    Dopo aver configurato nodi cluster ricreata l'immagine, l'unità di risorsa C:\ e unità di sistema D:\ può essere riformattati, causando la perdita di dati e applicazioni installate in tali unità. Questo problema può avere anche se un nodo Azure macchine () che fa parte del cluster si arresta e viene sostituito da un nuovo nodo. È possibile installare componenti sull'unità D:\ o nel percorso C:\apps sul cluster. Tutte le altre posizioni nell'unità C:\ sono riservati. Specificare la posizione in applicazioni o librerie siano installati nello script di personalizzazione di cluster.


- Assicurarsi di disponibilità di architettura cluster

    HDInsight dispone di un'architettura attivo passivo disponibilità elevata, in cui un nodo principale è in modalità attiva (nel punto in cui i servizi di HDInsight sono in esecuzione) e nodo principale è in modalità standby (in cui HDInsight servizi non vengono eseguiti). I nodi cambiare la modalità attiva e passiva se HDInsight servizi vengono interrotti. Se un'azione script viene utilizzata per installare i servizi in entrambi i nodi testa disponibilità elevata, tenere presente che il meccanismo di failover HDInsight non saranno in grado di automaticamente questi servizi installati dall'utente. Servizi installate in modo da utente nei nodi di testa HDInsight che devono essere disponibilità hanno meccanismi propri failover se in modalità attiva passiva oppure essere in modalità attivo attivo.

    Un comando di azione Script HDInsight viene eseguito su entrambi i nodi testa quando il ruolo di testa nodo viene specificato come valore nel parametro *ClusterRoleCollection* . Quando si progetta uno script personalizzato, dunque, verificare che lo script è presente il programma di installazione. Non è consigliabile utilizzare un problema nel punto in cui gli stessi servizi sono installati e avviati su entrambi i nodi testa e finiscono concorrenti tra loro. Inoltre, tenere presente che dati andranno persi durante l'acquisizione nuovamente immagini, in modo software installato tramite l'azione Script deve essere flessibile a tali eventi. Le applicazioni devono progettate per funzionare con dati disponibili distribuiti in numerosi nodi. Si noti che un massimo di 1/5 dei nodi in un cluster possono essere ricostruzione dell'immagine nello stesso momento.


- Configurare i componenti personalizzati per l'utilizzo di archiviazione Blob Azure

    I componenti personalizzati che installano nei nodi del cluster potrebbero avere una configurazione predefinita all'utilizzo dell'archiviazione Hadoop Distributed File System (HDFS). Modificare la configurazione per utilizzare invece archiviazione Blob Azure. In un cluster ricreare l'immagine, il file system HDFS Ottiene formattato e verranno perse tutti i dati sono archiviati. Utilizzo di archiviazione Blob Azure invece garantisce che i dati verranno mantenuti.

## <a name="common-usage-patterns"></a>Modelli di utilizzo comune

In questa sezione fornisce indicazioni sull'implementazione di alcuni dei modelli di utilizzo comuni che potrebbero verificarsi durante la scrittura di script personalizzato.

### <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

In fase di sviluppo di script azione, è spesso si sentirà la necessità di impostare variabili di ambiente. Uno scenario con più probabile è ad esempio, quando si scarica un file binario da un sito esterno, installare nel cluster e aggiungere il percorso in cui è installato per la variabile di ambiente "PATH". Il frammento di codice seguente viene illustrato come impostare le variabili di ambiente in script personalizzato.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

L'istruzione imposta la variabile di ambiente **MDS_RUNNER_CUSTOM_CLUSTER** al valore "true" e viene impostata l'ambito della variabile a livello di computer. In alcuni casi è importante che le variabili di ambiente siano impostate nell'ambito appropriato, computer o dell'utente. Fare riferimento [qui] [ 1] per ulteriori informazioni sull'impostazione delle variabili.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Accesso alle posizioni in cui sono memorizzati gli script personalizzati

A uno script utilizzati per personalizzare un cluster deve essere l'account di archiviazione predefinito per il cluster o in un contenitore di sola lettura pubblico in un altro account di archiviazione. Se lo script accede alle risorse si trova in un' posizione questi necessario accessibile al pubblico (almeno pubblico sola lettura). È consigliabile, ad esempio accedere a un file e salvarlo con il comando SaveFile HDI.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

In questo esempio, è necessario assicurarsi che il contenitore 'somecontainer' nell'account di archiviazione 'somestorageaccount' sia accessibile al pubblico. In caso contrario, lo script verrà che venga generata un'eccezione "Non trovato" ed esito negativo.

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Passare il cmdlet AzureRmHDInsightScriptAction Aggiungi parametri

Per passare al cmdlet AzureRmHDInsightScriptAction aggiungere più parametri, è necessario formattare il valore di stringa per contenere tutti i parametri per lo script. Per esempio:

    "-CertifcateUri wasbs:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

o

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Eccezione per la distribuzione cluster non riuscito

Se si desidera ricevere una notifica accuratamente di fatto che cluster di personalizzazione non è riuscita come previsto, è importante che venga generata un'eccezione e non la creazione di cluster. Ad esempio, è consigliabile elaborare un file se presente e gestire il caso di errore in cui il file non esiste. In tal modo che il termine dell'esecuzione correttamente e lo stato del cluster viene definito correttamente. Il frammento di codice seguente viene illustrato un esempio di come eseguire questa operazione:

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

In questo frammento di codice, se il file non esiste, da portare a uno stato in cui lo script effettivamente normalmente dopo aver stampato il messaggio di errore alla chiusura e il cluster raggiunge stato esecuzione presupponendo "" completato il processo di personalizzazione di cluster. Se si vuole essere in modo accurato una notifica del fatto che cluster essenzialmente personalizzazione non è riuscita come previsto a causa di un file mancante, è preferibile che venga generata un'eccezione e non il passaggio di personalizzazione cluster. A tale scopo è necessario utilizzare il frammento di codice di esempio seguente.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Elenco di controllo per la distribuzione di un'azione script
Ecco i passaggi effettuati durante la preparazione per la distribuzione di questi script:

1. Inserire i file che contengono script personalizzati in un percorso in cui si è accessibile dai nodi del cluster durante la distribuzione. Può trattarsi di una delle predefinito o altri account di archiviazione specificato al momento dell'installazione cluster o qualsiasi altro contenitore di spazio di archiviazione accessibili.
2. Aggiungere controlli in script per assicurarsi che eseguono idempotently, in modo che lo script può essere eseguito più volte sullo stesso nodo.
3. Utilizzare il cmdlet di PowerShell Azure **Output di scrittura** per la stampa STDOUT, nonché STDERR. Non utilizzare **Host di scrittura**.
4. Utilizzare una cartella di file temporanei, ad esempio $env: TEMP per mantenere il file scaricato utilizzato dagli script e quindi pulire loro dopo esecuzione script.
5. Installare il software personalizzato solo a livello D:\ o C:\apps. Altri percorsi sull'unità c non devono essere utilizzati sono riservati. Si noti che l'installazione di file nell'unità c all'esterno della cartella C:\apps potrebbe causare errori di installazione durante nuovamente le immagini del nodo.
6. Nel caso in cui sono stati modificati le impostazioni a livello del sistema operativo o file di configurazione del servizio Hadoop, può essere necessario riavviare servizi HDInsight in modo che sia possibile selezionare le eventuali impostazioni di livello di sistema operativo, ad esempio le variabili di ambiente impostare gli script.

## <a name="debug-custom-scripts"></a>Eseguire il debug di script personalizzati

I log degli errori di script archiviati insieme altri output, l'account di archiviazione predefinito specificato per il cluster al momento della creazione. I log sono archiviati in una tabella con il nome *u < \cluster-name-fragment >< \time-stamp > setuplog*. Questi sono registri aggregati contenenti i record di tutti i nodi (nodo principale e nodi di lavoro) sul quale viene eseguito lo script del cluster.
Un modo semplice per controllare i registri consiste nell'utilizzare HDInsight Tools per Visual Studio. Per installare gli strumenti, vedere [Introduzione all'utilizzo di strumenti di Visual Studio Hadoop per HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#install-hdinsight-tools-for-visual-studio)

**Per controllare il registro utilizzando Visual Studio**

1. Aprire Visual Studio.
2. Fare clic su **Visualizza**e quindi fare clic su **Esplora Server**.
3. Pulsante destro del mouse "Azure", fare clic su Connetti a **Microsoft Azure sottoscrizioni**e quindi immettere le credenziali.
4. Espandere **lo spazio di archiviazione**, l'account di archiviazione Azure utilizzato come file system predefinito, **tabelle**e quindi fare doppio clic sul nome della tabella.


È possibile anche remoto nei nodi del cluster per visualizzare entrambi gli STDOUT e STDERR di script personalizzati. I log su ciascun nodo sono specifici solo a tale nodo e connessi **C:\HDInsightLogs\DeploymentAgent.log**. Questi file di log registrano tutti gli output di script personalizzati. Frammento di log di esempio per un'azione script motori è simile alla seguente:

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


In questo registro è evidente che è stata eseguita l'azione di script motori nella macchina virtuale denominata HEADNODE0 e che non sono state generate eccezioni durante l'esecuzione.

Nel caso in cui si verifica un errore di esecuzione, l'output che descrive l'anche essere contenuto in questo file di log. Le informazioni fornite in questi registri dovrebbero essere utili per il debug dello script problemi che possono verificarsi.


## <a name="see-also"></a>Vedere anche

- [Personalizzare i cluster HDInsight tramite Script azione][hdinsight-cluster-customize]
- [Installare e usare motori nei cluster HDInsight][hdinsight-install-spark]
- [Installare e usare R nei cluster HDInsight][hdinsight-r-scripts]
- [Installare e utilizzare i cluster Solr su HDInsight](hdinsight-hadoop-solr-install.md).
- [Installare e utilizzare i cluster Giraph su HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-r-scripts]: hdinsight-hadoop-r-scripts.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
