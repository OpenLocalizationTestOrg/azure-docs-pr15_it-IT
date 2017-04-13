<properties
pageTitle="Limitare l'accesso HDInsight ai dati di utilizzo delle firme di Access condiviso"
description="Informazioni su come utilizzare le firme di Access condiviso per limitare l'accesso HDInsight ai dati archiviati in BLOB Azure dello spazio di archiviazione."
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
ms.date="10/11/2016"
ms.author="larryfr"/>

#<a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-with-hdinsight"></a>Utilizzare le firme di Access condiviso lo spazio di archiviazione Azure per limitare l'accesso ai dati con HDInsight

HDInsight utilizza BLOB Azure dello spazio di archiviazione per l'archiviazione di dati. Mentre HDInsight devono avere accesso completo a quello utilizzato come spazio di archiviazione predefinito per il cluster, è possibile limitare le autorizzazioni per i dati archiviati in altri BLOB utilizzato dal cluster. Ad esempio, può essere necessario impostare alcuni dati di sola lettura. È possibile eseguire questa operazione utilizzando firme accesso condiviso.

Firme di Access condiviso (SA) sono una funzionalità di account di archiviazione Azure che consente di limitare l'accesso ai dati. Ad esempio, concessione dell'accesso di sola lettura ai dati. In questo documento si imparerà a utilizzare SA per abilitare l'accesso in lettura e solo elenco in un contenitore di blob da HDInsight.

##<a name="requirements"></a>Requisiti

* Una sottoscrizione di Azure

* C# o Python. Codice di esempio c# viene fornito una soluzione Visual Studio.

    * Visual Studio deve essere versione 2013 o 2015
    
    * Python deve essere 2.7 o versione successiva

* Un HDInsight basati su Linux cluster OR [Azure PowerShell] [ powershell] -se si dispone di un cluster basati su Linux esistente, è possibile utilizzare Ambari per aggiungere una firma di Access condiviso al cluster. In caso contrario, è possibile utilizzare Azure PowerShell per creare un nuovo cluster e aggiungere una firma di Access condiviso durante la creazione di cluster.

* File di esempio da [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Questo repository è le operazioni seguenti:

    * Un progetto di Visual Studio che è possibile creare un contenitore di spazio di archiviazione, criterio archiviato e SA per l'utilizzo con HDInsight
    
    * Uno script Python che è possibile creare un contenitore di spazio di archiviazione, criterio archiviato e SA per l'utilizzo con HDInsight
    
    * Script di PowerShell che è possibile creare un nuovo cluster HDInsight e configurarlo per utilizzare le associazioni di protezione.

##<a name="shared-access-signatures"></a>Accesso condiviso firme

Esistono due tipi di firme accesso condiviso:

* Ad hoc: l'ora di inizio, ora di scadenza e le autorizzazioni per le SA sono tutti specificato sull'URI SAS (o implicita, nel caso in cui viene omesso ora di inizio).

* Criterio di accesso memorizzate: un criterio di accesso archiviate definito in un contenitore di risorse - contenitore blob, tabella, coda o condivisione di file - e può essere usato per gestire i vincoli per una o più firme di accesso condiviso. Quando si associa un SA a un criterio di accesso archiviate, le associazioni di protezione eredita i vincoli - autorizzazioni - definite per il criterio di accesso archiviate, ora di inizio e ora di scadenza.

La differenza tra le due forme è importante per uno scenario di chiave: revoca. Una SA è un URL, in modo che chiunque riesca a ottenere le SA usarla, indipendentemente dal fatto che hanno chiesto per iniziare. Se viene pubblicato un SA, può essere utilizzato da chiunque nel mondo. Una SA distribuito è valida fino a quando non viene eseguita una delle quattro aspetti:

1. L'ora di scadenza specificata nelle SA viene raggiunta specificata.

2. L'ora di scadenza specificata sul criterio di accesso archiviate le SA fare riferimento a raggiungere (se si fa riferimento a un criterio di accesso archiviate, e se specifica un'ora di scadenza). È possibile che sia perché dell'intervallo o perché è stato modificato il criterio di accesso archiviate per avere un'ora di scadenza in precedenza, è possibile revocare le associazioni di protezione.

3. Viene eliminato il criterio di accesso archiviate le associazioni di sicurezza a cui fa riferimento, che è possibile revocare le associazioni di protezione. Si noti che se si ricrea il criterio di accesso memorizzata con lo stesso nome, tutti i token SA esistenti saranno nuovamente validi in base alle autorizzazioni associate a tale criterio di accesso archiviate (presupponendo che non ha superato la scadenza sulle SA). Se si intende revocare le associazioni di protezione, assicurarsi di usare un nome diverso se si ricrea il criterio di accesso con un'ora di scadenza in futuro.

4. La chiave account utilizzati per creare le SA rigenerazione. Se si utilizza questa operazione, tutti i componenti di applicazione tramite la chiave account l'esito negativo per l'autenticazione finché vengono aggiornate per utilizzare l'altra chiave account valido o la chiave account appena rigenerazione.

> [AZURE.IMPORTANT] Una firma di accesso condiviso URI è associata con la chiave account usata per creare la firma e associato archiviati criterio di accesso (se presente). Se non viene specificato alcun criterio di accesso archiviate, per modificare la chiave account è l'unico modo per revocare una firma di accesso condiviso. 

Si consiglia di utilizzare sempre i criteri di accesso archiviate, in modo che è possibile revocare le firme o estendere la data di scadenza in base alle esigenze. La procedura descritta in questo documento, usare archiviati i criteri di accesso per generare SA.

Per ulteriori informazioni sulle firme di Access condiviso, vedere [informazioni sul modello di SA](../storage/storage-dotnet-shared-access-signature-part-1.md).

##<a name="create-a-stored-policy-and-generate-a-sas"></a>Creazione di un criterio archiviato e generare un SA

Attualmente è necessario creare un criterio archiviato a livello di programmazione. È possibile trovare c# e Python ad esempio la creazione di un criterio archiviato e SA in [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature).

###<a name="create-a-stored-policy-and-sas-using-c"></a>Creare un criterio archiviato e che utilizzano C\#

1. Aprire la soluzione in Visual Studio.

2. In Esplora soluzioni fare clic su progetto __SASToken__ e scegliere __proprietà__.

3. Selezionare __Impostazioni__ e aggiungere valori per le voci seguenti:

    * StorageConnectionString: La stringa di connessione per l'account di archiviazione che si desidera creare un criterio archiviato e SA per. Il formato deve essere `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` in `myaccount` è il nome del proprio account di archiviazione e `mykey` è la chiave per l'account di archiviazione.
    
    * Nomecontenitore: Il contenitore nell'account di archiviazione che si desidera limitare l'accesso a.
    
    * SASPolicyName: Il nome da utilizzare per il criterio archiviato che verrà creato.
    
    * FileToUpload: Percorso di un file caricati al contenitore.
    
4. Eseguire il progetto. Verrà visualizzata una finestra console e verranno visualizzate le informazioni simile al seguente dopo le associazioni di sicurezza:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
        
    Salvare il token di criteri sa come sarà necessario seguente quando si associa il cluster HDInsight account di archiviazione. È necessario anche il nome dell'account di archiviazione e il nome del contenitore.
    
###<a name="create-a-stored-policy-and-sas-using-python"></a>Creare un criterio archiviato e che utilizzano Python

1. Aprire il file SASToken.py e modificare i valori seguenti:

    * criteri\_nome: il nome da utilizzare per il criterio archiviato che verrà creato.
    
    * spazio di archiviazione\_account\_nome: il nome del proprio account di archiviazione.
    
    * spazio di archiviazione\_account\_chiave: la chiave per l'account di archiviazione.
    
    * spazio di archiviazione\_contenitore\_nome: il contenitore nell'account di archiviazione che si desidera limitare l'accesso a.
    
    * esempio\_file\_percorso: il percorso di un file caricati al contenitore

2. Eseguire lo script. Al termine dello script verrà visualizzato il token SA simile al seguente:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
    
    Salvare il token di criteri sa come sarà necessario seguente quando si associa il cluster HDInsight account di archiviazione. È necessario anche il nome dell'account di archiviazione e il nome del contenitore.
    
##<a name="use-the-sas-with-hdinsight"></a>Usare le SA con HDInsight

Quando si crea un cluster di HDInsight, è necessario specificare un account di archiviazione principale ed è possibile specificare gli account di spazio di archiviazione aggiuntivo. Entrambi i metodi di aggiunta di spazio di archiviazione richiedono l'accesso completo ai contenitori utilizzati e gli account di archiviazione.

Per utilizzare una firma di Access condiviso per limitare l'accesso a un contenitore, è necessario aggiungere una voce personalizzata per la configurazione __dei siti principali__ per il cluster.

* Per i cluster __basato su Windows__ o __basati su Linux__ HDInsight, è possibile eseguire durante la creazione di cluster tramite PowerShell.

* Per cluster HDInsight __basati su Linux__ , si cambia la configurazione dopo la creazione di cluster utilizzando Ambari.

###<a name="create-a-new-cluster-that-uses-the-sas"></a>Creare un nuovo cluster che utilizza le associazioni di protezione

Un esempio di creazione di un cluster di HDInsight che utilizza le SA è incluso nel `CreateCluster` directory dell'archivio. Per utilizzarla, utilizzare la procedura seguente:

1. Aprire la `CreateCluster\HDInsightSAS.ps1` dei file in un editor di testo e modificare i valori seguenti all'inizio del documento.

        # Replace 'mycluster' with the name of the cluster to be created
        $clusterName = 'mycluster'
        # Valid values are 'Linux' and 'Windows'
        $osType = 'Linux'
        # Replace 'myresourcegroup' with the name of the group to be created
        $resourceGroupName = 'myresourcegroup'
        # Replace with the Azure data center you want to the cluster to live in
        $location = 'North Europe'
        # Replace with the name of the default storage account to be created
        $defaultStorageAccountName = 'mystorageaccount'
        # Replace with the name of the SAS container created earlier
        $SASContainerName = 'sascontainer'
        # Replace with the name of the SAS storage account created earlier
        $SASStorageAccountName = 'sasaccount'
        # Replace with the SAS token generated earlier
        $SASToken = 'sastoken'
        # Set the number of worker nodes in the cluster
        $clusterSizeInNodes = 2
        
    Ad esempio modificare `'mycluster'` il nome del cluster che si desidera creare. I valori SA devono corrispondere i valori nei passaggi precedenti durante la creazione di un account di archiviazione e token SA.
    
    Dopo avere modificato i valori, salvare il file.

1. Aprire un nuovo prompt dei comandi PowerShell di Azure. Se si ha familiarità con PowerShell di Azure, o non è stato installato, vedere [installare e configurare Azure PowerShell][powershell].

2. Dal prompt dei comandi, utilizzare le operazioni seguenti per eseguire l'autenticazione all'abbonamento Azure:

        Login-AzureRmAccount
    
    Quando richiesto, accedere con l'account per l'abbonamento Azure.
    
    Se l'accesso è associato a più abbonamenti Azure, potrebbe essere necessario utilizzare `Select-AzureRmSubscription` per selezionare l'abbonamento a cui si desidera utilizzare.

2. Dal prompt dei comandi, passare alla directory di `CreateCluster` directory che contiene il file HDInsightSAS.ps1. Usare quindi le operazioni seguenti per eseguire lo script
        
        .\HDInsightSAS.ps1
    
    Durante l'esecuzione di script, verrà registrato output al prompt dei comandi PowerShell durante la creazione della risorsa gli account di gruppo e lo spazio di archiviazione. Verrà quindi chiesto di immettere il nome dell'utente HTTP per il cluster HDInsight. Questo è l'account utente utilizzato per proteggere l'accesso HTTP/s al cluster.
    
    Se si sta creando un cluster basato su Linux, verrà richiesto anche SSH nome dell'account utente e password. Viene utilizzato all'account di accesso remoto al cluster.
    
    > [AZURE.IMPORTANT] Quando viene richiesto di HTTP/s o SSH utente nome e la password, è necessario fornire una password che soddisfi i criteri seguenti:
    >
    > - Deve contenere almeno 10 caratteri
    > - Deve contenere almeno una cifra
    > - Deve contenere almeno un carattere non alfanumerici
    > - Deve contenere almeno un angolo o la lettera minuscola


Richiederà un po' di tempo per questo script al completamento, in genere circa 15 minuti. Al termine dello script senza errori, il cluster è stato creato.

###<a name="update-an-existing-cluster-to-use-the-sas"></a>Aggiornare un cluster esistente per utilizzare le associazioni di protezione

Se si dispone di un cluster basati su Linux esistente, è possibile aggiungere le associazioni di protezione per la configurazione __del sito principale__ utilizzando la procedura seguente:

1. Aprire il web Ambari dell'interfaccia utente per il cluster. L'indirizzo per la pagina è https://YOURCLUSTERNAME.azurehdinsight.net. Quando richiesto, eseguire l'autenticazione a cluster utilizzando il nome dell'amministratore (amministratore) e la password utilizzati durante la creazione del cluster.

2. Partire dal lato sinistro del web Ambari dell'interfaccia utente, selezionare __HDFS__ e quindi selezionare la scheda __configurazioni__ al centro della pagina.

3. Selezionare la scheda __Avanzate__ e quindi scorrere fino a individuare la sezione __siti principali personalizzati__ .

4. Espandere la sezione __siti principali personalizzati__ , quindi scorrere fino alla fine e selezionare il collegamento __Aggiungi proprietà...__ . Per i campi __chiave__ e il __valore__ , utilizzare i valori seguenti:

    * __Chiave__: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
    
    * __Valore__: il SA restituito dall'applicazione c# o Python è stata eseguita in precedenza
    
    Sostituire __NOMECONTENITORE__ con il nome del contenitore che è stato utilizzato con l'applicazione c# o SA. Sostituire __STORAGEACCOUNTNAME__ con il nome dell'account di archiviazione che è stato utilizzato.

5. Fare clic sul pulsante __Aggiungi__ per salvare questo tasto e valore, quindi fare clic sul pulsante __Salva__ per salvare le modifiche di configurazione. Quando richiesto, aggiungere una descrizione della modifica ("aggiunta SA lo spazio di archiviazione dell'accesso", ad esempio) e quindi fare clic su __Salva__.

    Quando sono state completate le modifiche, fare clic su __OK__ .

    > [AZURE.IMPORTANT] Consente di salvare le modifiche di configurazione, ma è necessario riavviare diversi servizi affinché le modifiche abbiano effetto.

6. Nell'interfaccia utente, Ambari web selezionare __HDFS__ dall'elenco a sinistra e quindi selezionare __Riavviare tutti__ dall'elenco a destra a discesa __Azioni del servizio__ . Quando richiesto, selezionare __Attiva modalità di manutenzione__ e quindi selezionare __Conform riavviare tutti".

    Ripetere questa procedura per le voci MapReduce2 e filati dall'elenco sul lato sinistro della pagina.

7. Una volta questi riavvio, selezionare ognuno di essi e disattivare la modalità di manutenzione dal __Servizio azioni__ a discesa.

##<a name="test-restricted-access"></a>Verificare l'accesso con restrizioni

Per verificare di avere limitato l'accesso, utilizzare i metodi seguenti:

* Per i cluster HDInsight __basato su Windows__ , utilizzare Desktop remoto per connettersi al cluster. Per ulteriori informazioni, vedere [la connessione a HDInsight utilizzando RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp) .

    Una volta connessa, usare l'icona __della riga di comando Hadoop__ sul desktop per aprire un prompt dei comandi.

* Per cluster HDInsight __basati su Linux__ , usare SSH per connettersi al cluster. Vedere una delle operazioni seguenti per informazioni sull'utilizzo di SSH con i cluster basati su Linux:

    * [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
Una volta connessa al cluster, utilizzare la procedura seguente per verificare che è possibile solo in lettura e l'elenco elementi nella pagina account di archiviazione SA:

1. Dal prompt dei comandi, digitare quanto segue. Sostituire __SASCONTAINER__ con il nome del contenitore creato per l'account di archiviazione SA. Sostituire __SASACCOUNTNAME__ con il nome dell'account di archiviazione utilizzato per le associazioni di sicurezza:

        hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    
    Per ottenere un elenco il contenuto del contenitore, che deve includere il file che è stato caricato quando è stato creato il contenitore e SA.
    
2. Utilizzare le operazioni seguenti per verificare che è possibile leggere il contenuto del file. Sostituire il __SASCONTAINER__ e __SASACCOUNTNAME__ del passaggio precedente. Sostituire __FILENAME__ con il nome del file visualizzato nel comando precedente:

        hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
        
    Questa operazione verrà elencati il contenuto del file.
    
3. Per scaricare il file nel file system locale, utilizzare il seguente:

        hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    
    Questa operazione verrà scaricare il file in un file locale __TestFile__.

4. Per caricare il file locale in un nuovo file denominato __testupload.txt__ nell'archivio SA, utilizzare il seguente:

        hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    
    Si riceverà un messaggio simile al seguente:
    
        put: java.io.IOException
        
    Questo errore si verifica perché la posizione di archiviazione solo lettura + elenco. Consente di inserire i dati in spazio di archiviazione predefinito per il cluster, è possibile scrivere le operazioni seguenti:
    
        hdfs dfs -put testfile.txt wasbs:///testupload.txt
        
    In questo caso, l'operazione dovrebbe essere completata correttamente.
    
##<a name="troubleshooting"></a>Risoluzione dei problemi

###<a name="a-task-was-canceled"></a>Un'attività è stata annullata

__Sintomi__: quando si crea un cluster utilizzando lo script di PowerShell, si potrebbe ricevere il messaggio di errore seguente:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

__Causa__: questo errore può verificarsi se si usa una password per l'utente di amministrazione/HTTP per il cluster o (per basati su Linux cluster,) all'utente SSH.

__Risoluzione__: utilizzare una password che soddisfano i criteri seguenti:

- Deve contenere almeno 10 caratteri
- Deve contenere almeno una cifra
- Deve contenere almeno un carattere non alfanumerici
- Deve contenere almeno un angolo o la lettera minuscola

##<a name="next-steps"></a>Passaggi successivi

Dopo avere appreso come aggiungere spazio di archiviazione di accesso limitato al cluster HDInsight, informazioni su altri modi per gestire i dati sul cluster:

* [Usare Hive con HDInsight](hdinsight-use-hive.md)

* [Utilizzare maialino con HDInsight](hdinsight-use-pig.md)

* [Utilizzare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[powershell]: ../powershell-install-configure.md
