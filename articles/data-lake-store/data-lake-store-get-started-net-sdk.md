<properties
   pageTitle="Usare dati Lake archivio .NET SDK per lo sviluppo di applicazioni | Microsoft Azure"
   description="Utilizzare Azure dati Lake archivio .NET SDK per lo sviluppo di applicazioni"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-net-sdk"></a>Guida introduttiva a archivio Lake Azure utilizzando .NET SDK

> [AZURE.SELECTOR]
- [Portale](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Linguaggio SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node](data-lake-store-manage-use-nodejs.md)

Informazioni su come utilizzare [Azure dati Lake archivio .NET SDK](https://msdn.microsoft.com/library/mt581387.aspx) per eseguire operazioni di base, ad esempio durante la creazione di cartelle, caricare e scaricare i file di dati e così via. Per ulteriori informazioni sui dati Lake, vedere [Archivio Lake dati di Azure](data-lake-store-overview.md).

## <a name="prerequisites"></a>Prerequisiti

* **Visual Studio 2013 o 2015**. Le istruzioni seguenti utilizzano Visual Studio 2015.

* **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **Account azure dati Lake Store**. Per istruzioni su come creare un account, vedere [Guida introduttiva a archivio Lake dati di Azure](data-lake-store-get-started-portal.md)

* **Creare un'applicazione di Azure Active Directory**. Utilizzare l'applicazione di Azure Active Directory per autenticare l'applicazione di archivio di dati Lake con Azure Active Directory. Sono disponibili diversi metodi per l'autenticazione con Azure Active Directory, che sono **al servizio**o **l'autenticazione degli utenti finali** . Per ulteriori informazioni su come eseguire l'autenticazione e istruzioni, vedere [autentica con dati Lake Store con Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Creare un'applicazione .NET

1. Aprire Visual Studio e creare un'applicazione console.

2. Dal menu **File** fare clic su **Nuovo**e quindi fare clic su **progetto**.

3. Dal **Nuovo progetto**, digitare o selezionare i valori seguenti:

  	| Proprietà | Valore                       |
  	|----------|-----------------------------|
  	| Categoria | Modelli e Visual c# / Windows |
  	| Modello | Applicazione console         |
  	| Nome     | CreateADLApplication        |

4. Fare clic su **OK** per creare il progetto.

5. Aggiungere i pacchetti Nuget al progetto.

    1. Pulsante destro del mouse sul nome del progetto in Esplora soluzioni e fare clic su **Gestisci pacchetti NuGet**.
    2. Nella scheda **Gestione pacchetti Nuget** ed assicurarsi che sia **origine pacchetto** è impostato su **nuget.org** sia selezionata la casella di controllo **Includi definitiva** .
    3. Cercare e installare i pacchetti NuGet seguenti:

        * `Microsoft.Azure.Management.DataLake.Store`-Questa esercitazione utilizza v0.12.5 anteprima.
        * `Microsoft.Azure.Management.DataLake.StoreUploader`-Questa esercitazione utilizza v0.10.6 anteprima.
        * `Microsoft.Rest.ClientRuntime.Azure.Authentication`-Questa esercitazione utilizza v2.2.8 anteprima.

        ![Aggiungere un'origine Nuget] (./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Creare un nuovo account Azure dati Lake")

    4. Chiudere il **pacchetto Nuget Manager**.

6. Aprire **Program.cs**, eliminare il codice esistente e quindi includere le seguenti istruzioni per aggiungere i riferimenti agli spazi dei nomi.

        using System;
        using System.Threading;
        
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;

7. Dichiarare le variabili, come illustrato di seguito e fornire i valori per nome archivio Lake dati e il nome del gruppo risorse già esistenti. Inoltre, verificare che il percorso e nome del file che qui deve essere presente nel computer. Aggiungere il frammento di codice seguenti dopo le dichiarazioni di spazio dei nomi.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                
                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";
                    
                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

In sezioni rimanente dell'articolo, è possibile vedere come utilizzare i metodi di .NET disponibili per eseguire operazioni, ad esempio l'autenticazione, il caricamento di file e così via.

## <a name="authentication"></a>Autenticazione

### <a name="if-you-are-using-end-user-authentication-recommended-for-this-tutorial"></a>Se si utilizza l'autenticazione dell'utente finale (scelta consigliata per questa esercitazione)

Utilizzare questo con Azure Active Directory "Native Client" applicazione esistente; disponibile per il riportata di seguito. Per informazioni su come completare più velocemente in questa esercitazione, è consigliabile che utilizzare questo approccio.

    // User login via interactive popup
    // Use the client ID of an existing AAD "Native Client" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(domain, activeDirectoryClientSettings).Result;

Paio di cose da sapere su questo frammento di codice precedente.

* Per informazioni su come completare più velocemente l'esercitazione, viene utilizzato il frammento di un un annuncio Azure dominio e i client ID che è disponibile per impostazione predefinita per tutte le sottoscrizioni Azure. Pertanto, è possibile **utilizzare il frammento come-nell'applicazione**.
* Tuttavia, se si desidera utilizzare il proprio ID client dominio e applicazione di Azure Active Directory, è necessario creare un'applicazione nativa di Azure Active Directory quindi usare il dominio di Azure Active Directory, ID client e reindirizzare URI per l'applicazione creata. Per istruzioni, vedere [creare un'applicazione di Directory attiva](../resource-group-create-service-principal-portal.md#create-an-active-directory-application) .

>[AZURE.NOTE] Le istruzioni disponibili nei collegamenti disponibili per un'applicazione web di Azure Active Directory. Tuttavia, la procedura è esattamente gli stessi anche se si è scelto di creare un'applicazione client nativi invece. 

### <a name="if-you-are-using-service-to-service-authentication-with-client-secret"></a>Se si utilizza l'autenticazione di servizio servizio con segreto del client 

Il frammento di codice seguente possono essere usati per eseguire l'autenticazione dell'applicazione in modo non interattivo, usando il client segreta / chiave per un'identità di applicazione / service. Utilizzare un' [applicazione di "Web App" Azure Active Directory](../resource-group-create-service-principal-portal.md)esistente.

    // Service principal / appplication authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;

### <a name="if-you-are-using-service-to-service-authentication-with-certificate"></a>Se si utilizza l'autenticazione di servizio servizio con certificato

Come terza opzione, il frammento di codice seguente può utilizzato per eseguire l'autenticazione dell'applicazione in modo non interattivo, utilizzando il certificato per un capitale applicazione / service. Utilizzare un' [applicazione di "Web App" Azure Active Directory](../resource-group-create-service-principal-portal.md)esistente.

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;

## <a name="create-client-objects"></a>Creare gli oggetti client

Il frammento di codice seguente crea il client di account e file System di archivio di dati Lake oggetti che vengono utilizzati per le richieste al servizio.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds);
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

    _adlsClient.SubscriptionId = _subId;

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Elenco di tutti gli account di archivio Lake dati all'interno di una sottoscrizione

Il frammento di codice seguente sono elencati tutti gli account di archivio Lake dati all'interno di una sottoscrizione di Azure specificato.

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List();
        var accounts = new List<DataLakeStoreAccount>(response);
        
        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }
        
        return accounts;
    }

## <a name="create-a-directory"></a>Creare una directory

Frammento seguente viene illustrato un `CreateDirectory` metodo che è possibile utilizzare per creare una directory all'interno di un account di archivio di dati Lake.

    // Create a directory
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

## <a name="upload-a-file"></a>Caricare un file

Frammento seguente viene illustrato un `UploadFile` metodo che è possibile utilizzare per caricare file in un account di archivio di dati Lake.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

`DataLakeStoreUploader`supporta ricorsiva caricamento e download tra un percorso file locale e un percorso di file di archivio di dati Lake.    

## <a name="get-file-or-directory-info"></a>Ottenere informazioni sul file o una directory

Frammento seguente viene illustrato un `GetItemInfo` metodo che è possibile utilizzare per recuperare le informazioni relative a un file o directory disponibile nell'archivio Lake dati. 

    // Get file or directory info
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

## <a name="list-file-or-directories"></a>File di elenco o una directory

Frammento seguente viene illustrato un `ListItem` metodo utilizzabile per visualizzare un elenco di file e directory in un account di archivio di dati Lake.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## <a name="concatenate-files"></a>File concatena

Frammento seguente viene illustrato un `ConcatenateFiles` metodo da utilizzare per concatenare file. 

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## <a name="append-to-a-file"></a>Aggiungere a un file

Frammento seguente viene illustrato un `AppendToFile` utilizza il metodo aggiungere dati a un file già archiviato in un account di archivio di dati Lake.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));
        
        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## <a name="download-a-file"></a>Scaricare un file

Frammento seguente viene illustrato un `DownloadFile` metodo che consente di scaricare un file da un account di archivio di dati Lake.

    // Download file
    public static void DownloadFile(string srcPath, string destPath)
    {
        var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
        var fileStream = new FileStream(destPath, FileMode.Create);
        
        stream.CopyTo(fileStream);
        fileStream.Close();
        stream.Close();
    }

## <a name="next-steps"></a>Passaggi successivi

- [Proteggere i dati nell'archivio dati Lake](data-lake-store-secure-data.md)
- [Usare dati di Azure Lake Analitica con dati Lake archivio](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usare Azure HDInsight con dati Lake archivio](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Archivio Lake di dati .NET SDK riferimento](https://msdn.microsoft.com/library/mt581387.aspx)
- [Archivio dati Lake resto riferimento](https://msdn.microsoft.com/library/mt693424.aspx)
