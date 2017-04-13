<properties 
    pageTitle="Installare i blocchi appunti Zeppelin per cluster ad Apache su HDInsight Linux | Microsoft Azure" 
    description="Istruzioni dettagliate su come installare e usare i blocchi appunti Zeppelin con i cluster di motori su HDInsight Linux." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="nitinme"/>


# <a name="install-zeppelin-notebooks-for-apache-spark-cluster-on-hdinsight-linux"></a>Installare i blocchi appunti Zeppelin per cluster ad Apache su HDInsight Linux

Informazioni su come installare i blocchi appunti Zeppelin in Apache i cluster e su come usare i blocchi appunti Zeppelin per eseguire ad processi.

> [AZURE.IMPORTANT] I blocchi appunti Zeppelin sono ora disponibili con i cluster motori per impostazione predefinita. Non è necessario installarli in modo esplicito in un cluster di motori più. Per ulteriori informazioni, vedere [usare Zeppelin blocchi appunti con Apache i cluster su HDInsight Linux](hdinsight-apache-spark-zeppelin-notebook.md). 

**Prerequisiti:**

* Prima di iniziare questa esercitazione, è necessario disporre di un abbonamento a Azure. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster di motori di Apache. Per ulteriori informazioni, vedere [creare Apache i cluster in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Un client SSH. Per distribuzioni Linux e Unix o Mac OS X, il `ssh` comando viene fornito con il sistema operativo. Per Windows, è consigliabile [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

    > [AZURE.NOTE] Se si desidera utilizzare diverso da un client SSH `ssh` o PuTTY, consultare la documentazione per i client su come stabilire un tunnel SSH.

* Un browser che può essere configurato per utilizzare un proxy SOCKS

* __(facoltativo)__: un plug-in, ad esempio [FoxyProxy](http://getfoxyproxy.org/,) che è possibile applicare regole che instradare solo specifiche richieste attraverso il tunnel.

    > [AZURE.WARNING] Senza un plug-in, ad esempio FoxyProxy, tutte le richieste eseguite tramite il browser potrebbero essere distribuite attraverso il tunnel. Questo può comportare più lentamente caricamento delle pagine web nel browser.

## <a name="install-zeppelin-on-a-spark-cluster"></a>Installare Zeppelin in un cluster di motori

È possibile installare Zeppelin in un cluster di motori tramite l'azione script. Azione di script utilizza script personalizzati per installare componenti nel cluster che non sono disponibili per impostazione predefinita. È possibile utilizzare lo script personalizzato per l'installazione Zeppelin dal portale di Azure, mediante la funzione HDInsight .NET SDK o tramite PowerShell Azure. È possibile utilizzare lo script per installare Zeppelin o come parte della creazione di cluster o dopo il cluster sia in esecuzione. I collegamenti nelle sezioni seguenti istruzioni su come eseguire questa operazione. 

### <a name="using-the-azure-portal"></a>Tramite il portale di Azure

Per istruzioni su come utilizzare il portale di Azure per eseguire script azione per l'installazione Zeppelin, vedere [personalizzare HDInsight cluster tramite Script azione](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-azure-portal). È necessario apportare alcune modifiche per le istruzioni fornite in questo articolo.

* È necessario utilizzare lo script per installare Zeppelin. Lo script personalizzato per installare Zeppelin in un cluster di motori in HDInsight è disponibile i collegamenti seguenti:
    * Per i motori 1.6.0 cluster-`https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
    * Per i motori 1.5.2 cluster-`https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

* È necessario eseguire l'azione script solo per il headnode.

* Lo script non è necessario tutti i parametri. 

### <a name="using-hdinsight-net-sdk"></a>Mediante HDInsight .NET SDK

Per istruzioni su come usare HDInsight .NET SDK per eseguire l'azione script installare Zeppelin, vedere [personalizzare HDInsight cluster tramite Script azione](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-hdinsight-net-sdk). È necessario apportare alcune modifiche per le istruzioni fornite in questo articolo.

* È necessario utilizzare lo script per installare Zeppelin. Lo script personalizzato per installare Zeppelin in un cluster di motori in HDInsight è disponibile i collegamenti seguenti:
    * Per i motori 1.6.0 cluster-`https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
    * Per i motori 1.5.2 cluster-`https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

* Lo script non è necessario tutti i parametri. 

* Impostare il tipo di grafico che si sta creando motori.

### <a name="using-azure-powershell"></a>Utilizzo di PowerShell Azure

Usare frammento di PowerShell riportato di seguito per creare un cluster di motori su HDInsight Linux con Zeppelin installato. A seconda della versione di cluster ad avere, è necessario aggiornare il frammento di PowerShell riportato di seguito per includere il collegamento allo script personalizzato corrispondente. 

* Per i motori 1.6.0 cluster-`https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
* Per i motori 1.5.2 cluster-`https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


    Login-AzureRMAccount
    
    # PROVIDE VALUES FOR THE VARIABLES
    $clusterAdminUsername="admin"
    $clusterAdminPassword="<<password>>"
    $clusterSshUsername="adminssh"
    $clusterSshPassword="<<password>>"
    $clusterName="<<clustername>>"
    $clusterContainerName=$clusterName
    $resourceGroupName="<<resourceGroupName>>"
    $location="<<region>>"
    $storage1Name="<<storagename>>"
    $storage1Key="<<storagekey>>"
    $subscriptionId="<<subscriptionId>>"
    
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    $passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
    $clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
    $passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
    $clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)
    
    $azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
    $azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
    $azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"
    
    Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"
    
    New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"
 
## <a name="set-up-ssh-tunneling-to-access-a-zeppelin-notebook"></a>Configurare SSH tunneling per accedere a un blocco appunti Zeppelin

Utilizzare tunnel SSH per accedere ai blocchi appunti Zeppelin in esecuzione su cluster ad su HDInsight Linux. La procedura seguente viene illustrato come creare un tunnel SSH utilizzando ssh riga di comando (Linux) e PuTTY (Windows).

### <a name="create-a-tunnel-using-the-ssh-command-linux"></a>Creare un tunnel con il comando SSH (Linux)

Usare il comando seguente per creare un SSH tunnel utilizzando il `ssh` comando. Sostituire __nomeutente__ con un utente SSH per il cluster HDInsight e __nome cluster__ con il nome del cluster HDInsight

    ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

In questo modo si crea una connessione che indirizza il traffico a porta locale 9876 al cluster su SSH. Le opzioni sono:

* **D 9876** - porta locale che indirizza il traffico attraverso il tunnel.

* **C** - Comprimi tutti i dati, poiché il traffico web prevalentemente testo.

* **2** - forza SSH per provare la versione 2 di protocollo.

* **domande** - invisibile.

* **T** - allocazione pseudo tty disabilita, poiché è sufficiente stiamo inoltro di una porta.

* **n** - impedisce la lettura di STDIN, poiché è sufficiente stiamo inoltro di una porta.

* **N** - non viene eseguito un comando remoto, poiché è sufficiente stiamo inoltro di una porta.

* **f** - esecuzione in background.

Se il cluster è configurato con un tasto di SSH, potrebbe essere necessario usare la `-i` parametro e specificare il percorso per la chiave privata SSH.

Una volta al termine del comando, il traffico inviato alla porta 9876 sul computer locale instradati su Secure Sockets Layer (SSL) al cluster sede nodo e origine non esiste.

### <a name="create-a-tunnel-using-putty-windows"></a>Creare un tunnel con PuTTY (Windows)

Utilizzare la procedura seguente per creare un tunnel SSH utilizzando PuTTY.

1. Aprire PuTTY e immettere le informazioni di connessione. Se non ha familiarità con PuTTY, vedere [Utilizzare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md) per informazioni su come usarlo con HDInsight.

2. Nella sezione **categoria** a sinistra della finestra di dialogo espandere **connessione**, espandere **SSH**e quindi selezionare **tunnel**.

3. Specificare le informazioni seguenti nel modulo **Opzioni che controllano l'inoltro di porta SSH** :

    * **Porta di origine** - porta sul client che si desidera inoltrare. Ad esempio **9876**.

    * Indirizzo di **destinazione** - The SSH per il cluster basati su Linux HDInsight. Ad esempio **miocluster ssh.azurehdinsight.net**.

    * **Dinamico** - proxy SOCKS dinamiche consente il routing.

    ![immagine delle opzioni di tunneling](./media/hdinsight-apache-spark-use-zeppelin-notebook/puttytunnel.png)

4. Fare clic su **Aggiungi** per aggiungere le impostazioni e quindi fare clic su **Apri** per aprire un collegamento SSH.

5. Quando richiesto, accedere al server. Per stabilire una sessione SSH e abilitare il tunnel.

### <a name="use-the-tunnel-from-your-browser"></a>Utilizzare il tunnel dal browser

> [AZURE.NOTE] La procedura descritta in questa sezione utilizza il browser FireFox, in quanto è disponibile gratuitamente per sistemi Linux, Unix, Mac OS X e Windows. Altri browser moderni, ad esempio Google Chrome, Microsoft Edge o Apple Safari funzionerà più. Tuttavia, il plug-in FoxyProxy utilizzato in alcuni passaggi potrebbe non essere disponibile per tutti i browser.

1. Configurare il browser per utilizzare **localhost:9876** come proxy **SOCKS v5** . Ecco l'aspetto delle impostazioni di Firefox. Se è stata utilizzata una porta diversa da quella 9876, cambiare la porta a quello usato:

    ![immagine delle impostazioni di Firefox](./media/hdinsight-apache-spark-use-zeppelin-notebook/socks.png)

    > [AZURE.NOTE] Selezione **DNS remoto** risolverà le richieste di sistema DNS (Domain Name) utilizzando il cluster HDInsight. Se non è selezionata, DNS verrà risolto in locale.

2. Verificare che il traffico viene instradato tunnel da vising un sito, ad esempio [http://www.whatismyip.com/](http://www.whatismyip.com/) con le impostazioni proxy abilitata e disabilitata in Firefox. Se le impostazioni sono attivate, l'indirizzo IP sarà per una macchina nel centro di dati di Microsoft Azure.

### <a name="browser-extensions"></a>Estensioni del browser

Durante la configurazione del browser per utilizzare il tunnel funziona, in genere non vuole indirizzare tutto il traffico attraverso il tunnel. Estensioni del browser, ad esempio [FoxyProxy](http://getfoxyproxy.org/) supportano criteri di ricerca per le richieste di URL (FoxyProxy Standard o Plus solo), in modo che venga inviate solo le richieste di URL specifici sul tunnel.

Se è stato installato FoxyProxy Standard, utilizzare la procedura seguente per configurarlo in modo da inoltrare il traffico solo per HDInsight sul tunnel.

1. Aprire l'estensione FoxyProxy nel browser. In Firefox, ad esempio, selezionare l'icona di FoxyProxy accanto al campo indirizzo.

    ![icona foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxy.png)

2. Selezionare **Aggiungi nuovo Proxy**, selezionare la scheda **Generale** e quindi immettere il nome di un proxy di **HDInsightProxy**.

    ![foxyproxy generali](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxygeneral.png)

3. Selezionare la scheda **Dettagli Proxy** e compilare i campi seguenti:

    * **Host o l'indirizzo IP** - si tratta host locale, poiché si sta utilizzando un tunnel SSH nel computer locale.

    * **Porta** - porta utilizzato per il tunnel SSH.

    * **Proxy SOCKS** : selezionare questa opzione per abilitare il browser da utilizzare il tunnel come proxy.

    * **SOCKS v5** - selezionare questa opzione per impostare la versione richiesta per il proxy.

    ![proxy foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxyproxy.png)

4. Selezionare la scheda **Modelli di URL** e quindi selezionare **Aggiungi nuovo motivo**. Utilizzare le operazioni seguenti per definire il modello e quindi fare clic su **OK**:

    * **Nome di una serie** - **zeppelinnotebook** - questo è solo un nome descrittivo per il modello.

    * **Modello di URL** - **\*hn0** * - definisce un motivo che corrisponde al nome di dominio completo interno dell'endpoint in cui sono ospitati i blocchi appunti Zeppelin. Perché i blocchi appunti Zeppelin sono disponibili solo sul headnode0 del cluster e il punto finale è in genere `http://hn0-<string>.internal.cloudapp.net`, utilizzando il modello * *hn0** da assicurarsi che la richiesta viene reindirizzata all'endpoint Zeppelin.

        ![motivo foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxypattern.png)

4. Fare clic su **OK** per aggiungere il proxy e chiudere **Le impostazioni del Proxy**.

5. Nella parte superiore della finestra di dialogo FoxyProxy, cambiare **Modalità selezionare** al **proxy di utilizzo in base alle modelli predefiniti e le priorità**e quindi fare clic su **Chiudi**.

    ![modalità di selezione foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/selectmode.png)

Dopo la procedura seguente, solo le richieste di URL che contengono la stringa __hn0__ instradate sul tunnel SSL. 

## <a name="access-the-zeppelin-notebook"></a>Accedere al blocco appunti Zeppelin

Dopo avere inserito SSH tunneling il programma di installazione, è possibile utilizzare la procedura seguente per accedere a blocchi appunti Zeppelin cluster ad eseguendo la procedura descritta di seguito. In questa sezione, verrà visualizzato come eseguire % sql e istruzioni hive %.

1. Nel web browser, aprire l'endpoint seguente:

        http://hn0-myspar:9995

    * **hn0** indica headnode0
    * **myspar** è primi sei lettere del nome del cluster ad.
    * **9995** è la porta in blocco appunti Zeppelin sono accessibile.

2. Creare un nuovo blocco appunti. Dal riquadro di intestazione, fare clic su **blocco appunti**e quindi fare clic su **Crea nuova nota**.

    ![Creare un nuovo blocco appunti Zeppelin] (./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.createnewnote.png "Creare un nuovo blocco appunti Zeppelin")

    Nella stessa pagina, sotto l'intestazione del **blocco appunti** , verrà visualizzato un nuovo blocco appunti con il nome che inizia con **XXXXXXXXX nota**. Fare clic su nuovo blocco appunti.

3. Nella pagina web per il nuovo blocco appunti, fare clic sull'intestazione e se si desidera modificare il nome del blocco appunti. Premere INVIO per salvare la modifica del nome. Inoltre, assicurarsi che l'intestazione del blocco appunti viene visualizzato uno stato **connesso** nell'angolo superiore destro.

    ![Stato di blocco appunti Zeppelin] (./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.newnote.connected.png "Stato di blocco appunti Zeppelin")

### <a name="run-sql-statements"></a>Eseguire istruzioni SQL

4. Caricare dati di esempio in una tabella temporanea. Quando si crea un cluster di motori in HDInsight, il file di dati di esempio **hvac.csv**, viene copiato nell'account di archiviazione associato in **\HdiSamples\SensorSampleData\hvac**.

    Paragrafo vuoto creati per impostazione predefinita nel nuovo blocco appunti, incollare il frammento di codice seguente.

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
        
        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0), 
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()
        
        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")
        
    Premere **MAIUSC + INVIO** o fare clic sul pulsante **Riproduci** per il paragrafo eseguire il frammento di codice. Lo stato nella parte destra del paragrafo deve svolgersi da pronto in sospeso in esecuzione in completato. L'output viene visualizzata nella parte inferiore dello stesso paragrafo. La schermata risulta simile alla seguente:

    ![Creare una tabella temporanea da dati non elaborati] (./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.loaddDataintotable.png "Creare una tabella temporanea da dati non elaborati")

    È anche possibile specificare un titolo a ogni paragrafo. Da angolo superiore destro fare clic sull'icona **Impostazioni** e quindi fare clic su **Mostra titolo**.

5. È ora possibile eseguire istruzioni SQL ad sulla tabella **hvac** . Incollare la query seguente in un nuovo paragrafo. La query recupera l'ID di creazione e la differenza tra le temperature effettive per ogni la di creazione di una data specificata. Premere **MAIUSC + INVIO**.

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date 
        from hvac
        where date = "6/1/13" 

    L'istruzione **sql %** all'inizio indica il blocco appunti da usare interprete motori SQL. È possibile esaminare gli interpreti definiti dalla scheda **interprete** nell'intestazione del blocco appunti.

    La schermata seguente mostra l'output.

    ![Eseguire un'istruzione SQL ad utilizzando il blocco appunti] (./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Eseguire un'istruzione SQL ad utilizzando il blocco appunti")

     Fare clic su Opzioni di visualizzazione (evidenziate in rettangolo) per passare tra diverse rappresentazioni per lo stesso risultato. Fare clic su **Impostazioni** per indicare che costituiscono la chiave e i valori nell'output. L'acquisizione di schermata sopra utilizza **buildingID** come chiave e la media di **temp_diff** come valore.

    
6. È anche possibile eseguire istruzioni SQL ad tramite variabili nella query. Il frammento di codice successivo viene illustrato come definire una variabile **Temp**, in query con i valori possibili che si desidera eseguire una query con. Quando si esegue prima di tutto la query, un elenco a discesa popolata automaticamente con i valori specificati per la variabile.

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
        from hvac
        where targettemp > "${Temp = 65,65|75|85}" 

    Incollare il frammento in un nuovo paragrafo e premere **MAIUSC + INVIO**. La schermata seguente mostra l'output.

    ![Eseguire un'istruzione SQL ad utilizzando il blocco appunti] (./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Eseguire un'istruzione SQL ad utilizzando il blocco appunti")

    Per le query successive, è possibile selezionare un nuovo valore dall'elenco a discesa ed eseguire nuovamente la query. Fare clic su **Impostazioni** per indicare che costituiscono la chiave e i valori nell'output. Nella schermata precedente utilizza **buildingID** come chiave, la media di **temp_diff** come valore e **targettemp** come gruppo.

7. Riavviare interprete SQL motori per uscire dall'applicazione. Fare clic sulla scheda **interprete** nella parte superiore e per l'interprete motori, fare clic su **Riavvia**.

    ![Riavviare intepreter Zeppelin] (./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Riavviare intepreter Zeppelin")

### <a name="run-hive-statements"></a>Eseguire istruzioni hive

1. Dal blocco appunti Zeppelin fare clic sul pulsante **interprete** .

    ![Aggiornamento Hive interprete] (./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-1.png "Aggiornamento Hive interprete")

2. Per interprete **hive** , fare clic su **Modifica**.

    ![Aggiornamento Hive interprete] (./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-2.png "Aggiornamento Hive interprete")

    Aggiornare le proprietà seguenti.

    * Impostare **default.password** la password specificata per l'utente amministratore durante la creazione di cluster HDInsight Spark.
    * Impostare **default.url** `jdbc:hive2://<spark_cluster_name>.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2`. Sostituire ** \<spark_cluster_name >** con il nome del cluster ad.
    * Impostare il nome dell'utente amministratore specificato durante la creazione del cluster **default.user** . Ad esempio *amministratore*.

3. Fare clic su **Salva** e quando viene richiesto di riavviare interprete hive, fare clic su **OK**.

4. Creare un nuovo blocco appunti ed eseguire l'istruzione seguente per elencare tutte le tabelle hive nel cluster.

        %hive
        SHOW TABLES

    Per impostazione predefinita, un cluster HDInsight dispone di una tabella di esempio denominata **hivesampletable** in modo che verrà visualizzato il seguente output.

    ![Hive output] (./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-3.png "Hive output")

5. Eseguire l'istruzione per elencare i record nella tabella seguente.

        %hive
        SELECT * FROM hivesampletable LIMIT 5

    È necessario un risultato simile al seguente.

    ![Hive output] (./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-4.png "Hive output")

## <a name="seealso"></a>Vedere anche


* [Panoramica: Apache motori su Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenari

* [Motori con BI: eseguire l'analisi dei dati interattive tramite motori in HDInsight con strumenti di Business Intelligence](hdinsight-apache-spark-use-bi-tools.md)

* [Motori di apprendimento: usare i in HDInsight per l'analisi temperatura predefiniti utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motori di apprendimento: usare i in HDInsight per prevedere i risultati del controllo alimentari](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Motori Streaming: Usare motori in HDInsight per la creazione di applicazioni di trasmissione in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)

* [Analisi dei log sito Web tramite motori in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma utilizza Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Eseguire processi in modalità remota in un cluster di motori tramite inserire il](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni

* [Plug-in strumenti di HDInsight per IntelliJ IDEA consente di creare e inviare Scala ad applicazioni](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Utilizzare plug-in strumenti di HDInsight per IntelliJ IDEA il debug delle applicazioni di motori in modalità remota](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Disponibile per blocco appunti Jupyter cluster motori per HDInsight. x](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utilizzare i pacchetti esterni con i blocchi appunti Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installare Jupyter nel computer e connettersi a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire le risorse

* [Gestire le risorse per cluster ad Apache in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tenere traccia e debug processi in esecuzione in un cluster di motori di Apache in HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md 







