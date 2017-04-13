<properties
   pageTitle="Creare cluster HDInsight con Azure dati Lake archivio tramite PowerShell | Azure"
   description="Usare PowerShell Azure per creare e utilizzare i cluster HDInsight con Lake di dati di Azure"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="nitinme"/>

# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-powershell"></a>Creare un cluster di HDInsight con archivio di Lake dati tramite PowerShell Azure

> [AZURE.SELECTOR]
- [Nel portale](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Utilizzo di PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)
- [Gestione risorse](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Informazioni su come usare PowerShell Azure per configurare un cluster di HDInsight (Hadoop, HBase o eccesso) con l'accesso all'archivio Lake dati di Azure. Alcune considerazioni importanti per questa versione:

* **Per i cluster (Linux) e cluster Hadoop/eccesso (Windows e Linux)**, l'archivio Lake dati può essere usata solo come un account di spazio di archiviazione aggiuntivo. L'account di archiviazione predefinito per i quali cluster continuerà a essere Azure lo spazio di archiviazione BLOB (WASB).

* **Per HBase cluster (Windows e Linux)**, l'archivio Lake dati può essere utilizzata come spazio di archiviazione predefinito o spazio di archiviazione aggiuntivo.

> [AZURE.NOTE] Ecco alcuni punti importanti da tenere presente. 
> 
> * Opzione per creare cluster HDInsight con l'accesso ai dati Lake Store è disponibile solo per le versioni HDInsight 3.2 e 3.4 (per eccesso, Hadoop e HBase cluster in Windows, nonché Linux). Per i cluster di motori su Linux, questa opzione è disponibile solo nei cluster 3.4 HDInsight.
>
> * Come detto in precedenza, archivio Lake dati è disponibile come spazio di archiviazione predefinito per alcuni tipi di grafico (HBase) e spazio di archiviazione aggiuntivo per gli altri tipi di grafico (Hadoop, motori, eccesso). Uso di archivio dati Lake come un account di spazio di archiviazione aggiuntivo non ha effetti prestazioni o la possibilità di lettura/scrittura per lo spazio di archiviazione dal cluster. In uno scenario in archivio Lake dati viene utilizzata come ulteriore spazio di archiviazione, file correlati a cluster (ad esempio, i registri e così via) vengono salvati spazio di archiviazione predefinito (BLOB Azure), mentre i dati che si desidera elaborare possono essere creati in un account di archivio di dati Lake.


In questo articolo è effettuare il provisioning di un cluster di Hadoop con dati Lake Store come spazio di archiviazione aggiuntivo.

La configurazione HDInsight per lavorare con dati Lake archivio tramite PowerShell prevede i passaggi seguenti:

* Creare un archivio di Lake dati di Azure
* Configurare l'autenticazione per l'accesso basato sui ruoli all'archivio dati Lake
* Creare cluster HDInsight con l'autenticazione a Lake archivio
* Eseguire un processo di test sul cluster

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Azure PowerShell 1.0 o versione successiva**. Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

- **Windows SDK**. È possibile installarlo da [qui](https://dev.windows.com/en-us/downloads). Utilizzare per creare un certificato di sicurezza.

- **Principale del servizio di azure Active Directory**. Procedura descritta in questa esercitazione le istruzioni su come creare un'entità servizio in Azure Active Directory. Tuttavia, è necessario essere un amministratore di Azure Active Directory per poter creare un'entità servizio. Se si è un amministratore di Azure Active Directory, è possibile ignorare questo prerequisito e procedere con l'esercitazione.
    
    **Se non è un amministratore di Azure Active Directory**, non sarà possibile eseguire i passaggi necessari per creare un'entità servizio. In questo caso, l'amministratore di Azure Active Directory innanzitutto necessario creare un'entità servizio prima di creare un cluster di HDInsight con dati Lake archivio. Inoltre, il capitale servizio deve essere creato utilizzando un certificato, come descritto in [creare un servizio dell'entità con certificato](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate). 


## <a name="create-an-azure-data-lake-store"></a>Creare un archivio di Lake dati di Azure

Seguire questa procedura per creare un archivio di Lake dati.

1. Dal desktop, aprire una nuova finestra di PowerShell Azure e immettere il frammento di codice seguente. Quando viene richiesto di effettuare l'accesso, assicurarsi che l'accesso un tipo di abbonamento admininistrators/proprietario:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    >[AZURE.NOTE] Se si riceve un messaggio di errore simile a `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` durante la registrazione il provider di risorse archivio Lake dati, è possibile che il subsrcription non whitelisted per archivio Lake dati di Azure. Verificare che si attiva l'abbonamento Azure per l'anteprima pubblica di archivio di dati Lake seguendo queste [istruzioni](data-lake-store-get-started-portal.md#signup).

3. Un account Azure dati Lake Store è associato a un gruppo di risorse Azure. Avviare la creazione di un gruppo di risorse Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Creare un gruppo di risorse Azure] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Creare un gruppo di risorse Azure")

2. Creare un account Azure dati Lake Store. Il nome dell'account specificato deve contenere solo numeri e lettere minuscole.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Creare un account Azure dati Lake] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Creare un account Azure dati Lake")

3. Verificare che l'account è stato creato correttamente.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    L'output per questa operazione deve essere **vera**.

4. Caricare alcuni dati di esempio Lake di dati di Azure. Useremo questo più avanti in questo articolo per verificare che i dati sono accessibili da un cluster di HDInsight. Se si sta cercando alcuni dati di esempio caricare, è possibile ottenere la cartella **Ambulanza dati** dal [Repository fra Lake dei dati di Azure](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).


        $myrootdir = "/"
        Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configurare l'autenticazione per l'accesso basato sui ruoli all'archivio dati Lake

Ogni abbonamento Azure è associata a un Azure Active Directory. Utenti e i servizi che accedono alle risorse della sottoscrizione utilizzando il portale classica Azure o API di gestione risorse Azure necessario eseguire l'autenticazione con tale Azure Active Directory. Accedere a servizi e abbonamenti Azure tramite l'assegnazione di ruolo appropriato in una risorsa di Azure.  Per i servizi, un'entità servizio identifica il servizio di Azure Active Directory (AAD). In questa sezione viene illustrato come concedere un'applicazione di servizio, ad esempio HDInsight, l'accesso a una risorsa Azure (l'account Azure dati Lake archivio creata in precedenza), creare un'entità servizio per l'applicazione e assegnare ruoli a tale tramite PowerShell Azure.

Per configurare l'autenticazione di Active Directory per Lake di dati di Azure, è necessario eseguire le operazioni seguenti.

* Creare un certificato autofirmato
* Creare un'applicazione in Azure Active Directory e un'entità di servizio

### <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato

Assicurarsi di avere installato prima di procedere con la procedura descritta in questa sezione [Windows SDK](https://dev.windows.com/en-us/downloads) . È necessario avere anche creato una directory, ad esempio **C:\mycertdir**, in cui verrà creato il certificato.

1. Nella finestra di PowerShell, passare al percorso in cui è installato Windows SDK (in genere, `C:\Program Files (x86)\Windows Kits\10\bin\x86` e utilizzare [MakeCert] [ makecert] utilità per creare un certificato autofirmato e una chiave privata. Usare i comandi seguenti.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir
        $startDate = (Get-Date).ToString('MM/dd/yyyy')
        $endDate = (Get-Date).AddDays(365).ToString('MM/dd/yyyy')

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -b $startDate -e $endDate -r -len 2048

    Verrà richiesto di immettere la password della chiave privata. Dopo l'esecuzione del comando correttamente, verrà visualizzato un **CertFile.cer** e **myKey** nella directory certificato specificato.

4. Utilizzare [Pvk2Pfx] [ pvk2pfx] utilità per convertire i file PVK e CER MakeCert creato in un file. pfx. Eseguire il comando seguente.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Quando richiesto immettere la password della chiave privata specificato in precedenza. Il valore specificato per il parametro **- ordine di acquisto** contiene la password associata al file. pfx. Dopo il comando viene completato correttamente, verrà visualizzata anche CertFile.pfx nella directory certificato specificato.

###  <a name="create-an-azure-active-directory-and-a-service-principal"></a>Creare un Azure Active Directory e un'entità di servizio

In questa sezione, attenersi alla procedura per creare un servizio principale per un'applicazione di Azure Active Directory, assegnare un ruolo al servizio principale e autenticare come principale del servizio, fornendo un certificato. Eseguire i comandi seguenti per creare un'applicazione di Azure Active Directory.

1. Incollare i cmdlet seguenti nella finestra della console PowerShell. Verificare che il valore specificato per la proprietà **- DisplayName** sia univoco. Inoltre, i valori per **- Home page** e **- IdentiferUris** sono valori segnaposto e non vengono verificati.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
                    -DisplayName "HDIADL" `
                    -HomePage "https://contoso.com" `
                    -IdentifierUris "https://mycontoso.com" `
                    -KeyValue $credential  `
                    -KeyType "AsymmetricX509Cert"  `
                    -KeyUsage "Verify"  `
                    -StartDate $startDate  `
                    -EndDate $endDate

        $applicationId = $application.ApplicationId

2. Creare un'entità del servizio mediante l'ID dell'applicazione.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id

3. Concedere l'accesso dell'entità servizio file/cartella archivio Lake dati accessibile dal cluster HDInsight. Il frammento di codice seguente consente di accedere alla radice del conto dati Lake Store.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All

    Se viene richiesto, immettere **Y** per confermare.

## <a name="create-an-hdinsight-cluster-with-authentication-to-data-lake-store"></a>Creare un cluster di HDInsight con l'autenticazione a Lake archivio

In questa sezione viene creato un cluster di HDInsight Hadoop. In questa versione cluster HDInsight e archivio dati Lake devono essere nello stesso percorso (USA orientale 2).

1. Iniziare con il recupero ID tenant di sottoscrizione. È necessario che in un secondo momento.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. In questa versione, per un cluster Hadoop archivio Lake dati può essere usata solo come un ulteriore spazio di archiviazione per il cluster. Spazio di archiviazione predefinito sarà comunque i BLOB Azure dello spazio di archiviazione (WASB). Affermativo, è necessario creare prima di tutto l'account di archiviazione e contenitori di archiviazione necessari per il cluster.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $containerName -Context $destContext

3. Creare il cluster HDInsight. Utilizzare i cmdlet seguenti.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $rdpCredentials = Get-Credential

        New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.2" -RdpCredential $rdpCredentials -RdpAccessExpiry (Get-Date).AddDays(14) -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Al termine il cmdlet correttamente, verrà visualizzato un risultato simile:

        Name                      : hdiadlcluster
        Id                        : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/hdiadlgroup/providers/Mi
                                    crosoft.HDInsight/clusters/hdiadlcluster
        Location                  : East US 2
        ClusterVersion            : 3.2.7.707
        OperatingSystemType       : Windows
        ClusterState              : Running
        ClusterType               : Hadoop
        CoresUsed                 : 16
        HttpEndpoint              : hdiadlcluster.azurehdinsight.net
        Error                     :
        DefaultStorageAccount     :
        DefaultStorageContainer   :
        ResourceGroup             : hdiadlgroup
        AdditionalStorageAccounts :

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Eseguire i processi di test su cluster HDInsight usi l'archivio Lake dati

Dopo aver configurato un cluster di HDInsight, è possibile eseguire test processi sul cluster per verificare che il cluster HDInsight possa accedere archivio Lake dati. A tale scopo, si eseguirà un processo di Hive di esempio che crea una tabella utilizzando i dati di esempio caricati in precedenza all'archivio dati Lake.

### <a name="for-a-linux-cluster"></a>Per un cluster Linux

In questa sezione verranno SSH in cluster e di esecuzione di una query di Hive di esempio. Windows non è disponibile un client SSH incorporato. È consigliabile utilizzare **PuTTY**, che può essere scaricato dal [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per ulteriori informazioni sull'uso di PuTTY, vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Una volta connessa, avviare CLI Hive utilizzando il comando seguente:

        hive

2. Usa CLI, immettere le istruzioni seguenti per creare una nuova tabella denominata **veicoli** utilizzando i dati di esempio nell'archivio Lake dati:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    Verrà visualizzato un output simile al seguente:

        1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
        1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
        1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
        1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
        1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
        1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
        1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
        1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
        1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
        1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


### <a name="for-a-windows-cluster"></a>Per un cluster di Windows

Utilizzare i cmdlet seguenti per eseguire la query Hive. In questa query verrà creato una tabella dai dati nell'archivio Lake dati e quindi eseguire una query di selezione della tabella creata.

    $queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"

    $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

    $hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

    Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials

Questo avrà l'output seguente. **ExitValue** pari a 0 nell'output suggerisce che il processo è stato completato correttamente.

    Cluster         : hdiadlcluster.
    HttpEndpoint    : hdiadlcluster.azurehdinsight.net
    State           : SUCCEEDED
    JobId           : job_1445386885331_0012
    ParentId        :
    PercentComplete :
    ExitValue       : 0
    User            : admin
    Callback        :
    Completed       : done

Recuperare l'output dal processo utilizzando il seguente cmdlet:

    Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials

L'output processo simile al seguente:

    1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
    1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
    1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
    1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
    1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
    1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
    1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
    1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
    1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
    1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


## <a name="access-data-lake-store-using-hdfs-commands"></a>Accesso dati Lake archivio usando i comandi HDFS

Dopo aver configurato il cluster HDInsight per utilizzare dati Lake archivio, è possibile utilizzare i comandi di shell HDFS per accedere all'archivio.

### <a name="for-a-linux-cluster"></a>Per un cluster Linux

In questa sezione è verrà SSH nel cluster ed eseguire i comandi HDFS. Windows non è disponibile un client SSH incorporato. È consigliabile utilizzare **PuTTY**, che può essere scaricato dal [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per ulteriori informazioni sull'uso di PuTTY, vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Una volta connessa, il comando seguente HDFS file System per visualizzare un elenco di file di archivio di Lake dati.

    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Verranno visualizzate il file caricato in precedenza all'archivio Lake dati.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

È inoltre possibile utilizzare il `hdfs dfs -put` comando caricare alcuni file all'archivio Lake dati e quindi utilizzare `hdfs dfs -ls` per verificare se i file sono stati caricati correttamente.


### <a name="for-a-windows-cluster"></a>Per un cluster di Windows

1. Effettuare l'accesso con il nuovo [Portale Azure](https://portal.azure.com).

2. Fare clic su **Sfoglia**e quindi fare clic sul cluster HDInsight creato **cluster HDInsight**.

3. In e il cluster fare clic su **Desktop remoto**e quindi scegliere **Connetti**e il **Desktop remoto** .

    ![Remote in cluster HDI] (./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png "Creare un gruppo di risorse Azure")

    Quando richiesto, immettere le credenziali fornite per l'utente desktop remoto.

4. Nella sessione remota avviare Windows PowerShell e utilizzare i comandi di file System HDFS per elencare i file nell'archivio Lake dati di Azure.

        hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

    Verranno visualizzate il file caricato in precedenza all'archivio Lake dati.

        15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
        Found 1 items
        -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv

    È inoltre possibile utilizzare il `hdfs dfs -put` comando caricare alcuni file all'archivio Lake dati e quindi utilizzare `hdfs dfs -ls` per verificare se i file sono stati caricati correttamente.

## <a name="see-also"></a>Vedere anche

* [Portale: Creare un cluster di HDInsight per l'uso dei dati Lake archivio](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
