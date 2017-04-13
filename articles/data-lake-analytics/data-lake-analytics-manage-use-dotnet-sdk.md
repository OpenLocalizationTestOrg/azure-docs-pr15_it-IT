<properties 
   pageTitle="Gestire Azure dati Lake Analitica utilizzando Azure .NET SDK | Azure" 
   description="Informazioni su come gestire i processi di dati Lake Analitica, origini dati, gli utenti. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/23/2016"
   ms.author="jgao"/>

# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Gestire Azure dati Lake Analitica utilizzando Azure .NET SDK

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Informazioni su come gestire gli account Azure dati Lake Analitica, origini dati, gli utenti e processi utilizzando Azure .NET SDK. Per visualizzare gli argomenti della gestione usare altri strumenti, fare clic su Seleziona la scheda precedente.

**Prerequisiti**

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).


<!-- ################################ -->
<!-- ################################ -->


## <a name="connect-to-azure-data-lake-analytics"></a>Connettersi a dati di Azure Lake Analitica

È necessario pacchetti Nuget seguenti:

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common 
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre


Nell'esempio seguente viene illustrato come connettersi a Azure e gli account Analitica Lake dati esistenti in abbonamento Azure dell'elenco.

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

            private static DataLakeAnalyticsAccountManagementClient _adlaClient;

            private static void Main(string[] args)
            {

                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = SUBSCRIPTIONID;

                var adlaAccounts = ListADLAAccounts();

                Console.WriteLine("You have %i Data Lake Analytics account(s).", adlaAccounts.Count);
                for (int i = 0; i < adlaAccounts.Count; i ++)
                {
                    Console.WriteLine(adlaAccounts[i].Name);
                }

                System.Console.WriteLine("Press ENTER to continue");
                System.Console.ReadLine();
            }

            public static ServiceClientCredentials AuthenticateAzure(
            string domainName,
            string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                return accounts;
            }
        }
    }


## <a name="manage-accounts"></a>Gestire gli account

Prima di eseguire qualsiasi processi dati Lake Analitica, è necessario disporre di un account di dati Lake Analitica. Diversamente da quanto succede Azure HDInsight non pagare per un account Analitica quando non è un processo.  Pagando per volta quando viene eseguito un processo.  Per ulteriori informazioni, vedere [Panoramica di Azure dati Lake Analitica](data-lake-analytics-overview.md).  

###<a name="create-accounts"></a>Creare gli account

È necessario disporre un gruppo di gestione delle risorse Azure e un account di archivio Lake dati prima di eseguire l'esempio seguente.

Il codice seguente viene illustrato come creare un gruppo di risorse:

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

Il codice seguente viene illustrato come creare un account di archivio Lake dati:

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

Il codice seguente viene illustrato come creare un account Analitica Lake dati:

    var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
    var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
    var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: location);
    var adlaAccount = _adlaClient.Account.Create(resourceGroupName, adlaAccountName, adlaParameters);

###<a name="list-accounts"></a>Elenco conti

Vedere [connettersi a dati di Azure Lake Analitica](#connect_to_azure_data_lake_analytics).

###<a name="find-an-account"></a>Trovare un account

Una volta che viene visualizzato un oggetto di un elenco di dati Lake Analitica account, è possibile utilizzare le operazioni seguenti per trovare un account:

    Predicate<DataLakeAnalyticsAccount> accountFinder = (DataLakeAnalyticsAccount a) => { return a.Name == adlaAccountName; };
    var myAdlaAccount = adlaAccounts.Find(accountFinder);

###<a name="delete-data-lake-analytics-accounts"></a>Eliminare dati Lake Analitica account

Frammento di codice seguente consente di eliminare un account Analitica Lake dati:

    _adlaClient.Account.Delete(resourceGroupName, adlaAccountName);

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Gestire le origini dati di account

Dati Lake Analitica supporta al momento le origini dati seguenti:

- [Lo spazio di archiviazione di dati di Azure Lake](../data-lake-store/data-lake-store-overview.md)
- [Spazio di archiviazione Azure](../storage/storage-introduction.md)

Quando si crea un account Analitica, è necessario designare un account Azure dati Lake archiviazione l'account di archiviazione predefinito. L'account di archivio di dati Lake predefinito viene utilizzato per archiviare processo metadati e processo i log di controllo. Dopo aver creato un account Analitica, è possibile aggiungere altri account di archiviazione dei dati Lake e/o account Azure archiviazione. 

### <a name="find-the-default-data-lake-store-account"></a>Trovare l'account di archivio di dati Lake predefinito

Vedere trovare un account in questo articolo per individuare il conto dati Lake Analitica. Usare quindi le operazioni seguenti:

    string adlaDefaultDataLakeStoreAccountName = myAccount.Properties.DefaultDataLakeStoreAccount;


## <a name="use-azure-resource-manager-groups"></a>Utilizzare i gruppi di Manager delle risorse di Azure

Le applicazioni sono in genere costituite da molti componenti, ad esempio un'app web, database, server di database, lo spazio di archiviazione e 3 ° servizi di terze parti. Gestione risorse di Azure consente di gestire le risorse dell'applicazione come un gruppo, indicato come un gruppo di risorse Azure. È possibile distribuire, aggiornare, monitorare o eliminare tutte le risorse per l'applicazione in un'operazione su una singola e coordinata. Si usa un modello per la distribuzione e il modello è possibile utilizzare per ambienti diversi, ad esempio test di prova e di produzione. È possibile rendere più chiara la fatturazione per l'organizzazione visualizzando i costi di ricalcolo per l'intero gruppo. Per ulteriori informazioni, vedere [Panoramica di gestione risorse Azure](../azure-resource-manager/resource-group-overview.md). 

Un servizio dati Lake Analitica può includere i componenti seguenti:

- Account Analitica Lake di dati di Azure
- Account di archiviazione di Azure dati Lake predefinito necessari
- Account di archiviazione aggiuntivo Azure dati Lake
- Altri account di archiviazione Azure

È possibile creare tutti questi componenti in un unico gruppo di gestione delle risorse per renderli più semplice da gestire.

![Spazio di archiviazione e dell'account Analitica Lake di dati di azure](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Un account di dati Lake Analitica e gli account di archiviazione dipendenti devono essere inseriti nell'interfaccia di dati di Azure stesso.
Il gruppo di gestione delle risorse, tuttavia può trovarsi in un centro di dati diversi.  

##<a name="see-also"></a>Vedere anche 

- [Panoramica di Microsoft Azure dati Lake Analitica](data-lake-analytics-overview.md)
- [Guida introduttiva a Analitica Lake dati tramite il portale di Azure](data-lake-analytics-get-started-portal.md)
- [Gestire Azure dati Lake Analitica tramite il portale di Azure](data-lake-analytics-manage-use-portal.md)
- [Monitorare e risolvere i processi di Azure dati Lake Analitica tramite il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

