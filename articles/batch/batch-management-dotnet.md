<properties
    pageTitle="Account di gestione delle risorse con Batch Management .NET | Microsoft Azure"
    description="Creare, eliminare e modificare le risorse account Azure Batch con la raccolta Batch Management .NET."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/19/2016"
    ms.author="marsma"/>

# <a name="manage-azure-batch-accounts-and-quotas-with-batch-management-net"></a>Gestione di quote con Batch Management .NET e account Azure Batch

> [AZURE.SELECTOR]
- [Portale di Azure](batch-account-create-portal.md)
- [Gestione batch .NET](batch-management-dotnet.md)

È possibile ridurre il sovraccarico manutenzione nelle applicazioni Azure Batch utilizzando il [Batch Management.NET] [ api_mgmt_net] raccolta per automatizzare la creazione di account Batch, l'eliminazione, la gestione delle chiavi e individuazione di quota.

- **Creare ed eliminare account Batch** all'interno di un'area. Se in un fornitore software indipendente (), ad esempio, fornire un servizio per i clienti in cui ogni è assegnato un conto Batch separato per scopi di fatturazione, è possibile aggiungere funzionalità di creazione e l'eliminazione di account per il portale dei clienti.
- **Recupera e Rigenera account chiavi** a livello di programmazione per uno degli account Batch. Questo consente di conformità ai criteri di sicurezza che applicare l'attivazione periodici o scadenza delle chiavi account. Quando si hanno più account Batch in diverse aree geografiche Azure, automazione di questo processo di attivazione aumenta l'efficienza della soluzione.
- **Controllare le quote di account** e prendere supporto per la versione di valutazione ed errore determinazione quali account Batch quali limiti. Selezionando le quote di account prima di avviare processi, creazione di pool o aggiunta di nodi di calcolo, è possibile regolare tempestiva dove o quando calcolare queste risorse vengono create. È possibile determinare quali account richiedono quota aumenta prima assegnazione delle risorse aggiuntive in tali account.
- **Combinare le funzionalità di altri servizi Azure** per un'esperienza di gestione completi - utilizzando Batch Management .NET [Azure Active Directory][aad_about]e la [Gestione di risorse di Azure] [ resman_overview] insieme nella stessa applicazione. Con queste caratteristiche e le API, è possibile fornire un'esperienza di autenticazione prive di attrito, la possibilità di creare ed eliminare gruppi di risorse e le funzionalità descritti in precedenza per una soluzione di gestione-to-end.

> [AZURE.NOTE] Durante questo articolo è incentrato sulla gestione di account Batch, le chiavi e le quote di livello di programmazione, è possibile eseguire molte di queste attività tramite il [portale di Azure][azure_portal]. Per ulteriori informazioni, vedere [creare un account Azure Batch tramite il portale di Azure](batch-account-create-portal.md) e [le quote e i limiti per il servizio di Azure Batch](batch-quota-limit.md).

## <a name="create-and-delete-batch-accounts"></a>Creare ed eliminare account Batch

Come detto, uno dei principali caratteristiche dell'API di gestione Batch consiste nel creare ed eliminare account Batch in un'area di Azure. Per eseguire questa operazione, utilizzare [BatchManagementClient.Account.CreateAsync] [ net_create] e [DeleteAsync][net_delete], o controparti icona del.

Frammento di codice seguente viene creato un account, ottiene il nuovo account creato dal servizio Batch e quindi Elimina. In questo frammento di codice e gli altri utenti in questo articolo `batchManagementClient` è un'istanza completamente inizializzata di [BatchManagementClient][net_mgmt_client].

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [AZURE.NOTE] Applicazioni che utilizzano la raccolta Batch Management .NET e la classe BatchManagementClient richiedono l'accesso **amministratore del servizio** o **coadministrator** per l'abbonamento a cui appartiene l'account di Batch da gestire. Per ulteriori informazioni, vedere la sezione di [Azure Active Directory](#azure-active-directory) e [AccountManagement] [ acct_mgmt_sample] codice di esempio.

## <a name="retrieve-and-regenerate-account-keys"></a>Recuperare e rigenerare chiavi account

Ottenere account primario e secondario chiavi da qualsiasi account Batch all'interno di abbonamento tramite [ListKeysAsync][net_list_keys]. È possibile rigenerare le chiavi utilizzando [RegenerateKeyAsync][net_regenerate_keys].

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [AZURE.TIP] È possibile creare un flusso di lavoro di connessione semplificata per le applicazioni di gestione. Prima di tutto, ottenere una chiave di account per l'account Batch che si desidera gestire con [ListKeysAsync][net_list_keys]. Quindi, utilizzare questo tasto all'inizializzazione della libreria .NET Batch [BatchSharedKeyCredentials] [ net_sharedkeycred] classe, che viene utilizzato durante l'inizializzazione [BatchClient][net_batch_client].

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Controllo Azure sottoscrizione e le quote di account Batch

Sottoscrizioni di Azure e singoli Azure servizi come Batch tutti hanno quote predefinito per limitare il numero di determinate entità al loro interno. Per le quote predefinite per gli abbonamenti Azure, vedere [abbonamento Azure e limiti del servizio, le quote e vincoli](./../azure-subscription-service-limits.md). Per le quote di predefinita del servizio di Batch, vedere [le quote e i limiti per il servizio di Azure Batch](batch-quota-limit.md). Tramite la raccolta Batch Management .NET, è possibile controllare le quote nelle applicazioni. In questo modo è possibile prendere decisioni allocazione prima di aggiungere un account o calcolare come pool di risorse e nodi di calcolo.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Selezionare una sottoscrizione Azure per conto delle quote del Batch

Prima di creare un account di Batch in un'area, è possibile verificare l'abbonamento Azure per verificare se in grado di aggiungere un account in tale area.

Frammento di codice riportata di seguito, viene utilizzata [BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts] per ottenere un insieme di tutti gli account Batch all'interno di una sottoscrizione. Dopo che sono state ricevute questa raccolta, è determinare il numero di account è presenti nell'area di destinazione. Successivamente si utilizza [BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions] per ottenere la quota di account Batch e determinare in quell'area è possibile creare il numero di account (se presente).

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

Nel frammento di codice riportati sopra, `creds` è un'istanza di [TokenCloudCredentials][azure_tokencreds]. Per visualizzare un esempio di creazione questo oggetto, vedere [AccountManagement] [ acct_mgmt_sample] esempio di codice GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Selezionare un account di Batch per le quote di risorse di calcolo

Prima di aumentare le risorse di elaborazione della soluzione Batch, è possibile controllare per assicurarsi che le risorse che si desidera allocare non superano le quote dell'account. Frammento di codice seguenti, si procederà alla stampa le informazioni di quota per l'account Batch denominato `mybatchaccount`. Nell'applicazione, è possibile utilizzare queste informazioni per determinare se l'account può gestire le risorse aggiuntive da creare.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT] Sebbene esistano quote predefinite per i servizi e abbonamenti Azure, molti di questi limiti possono essere generati da una richiesta nel [portale di Azure][azure_portal]. Per istruzioni sull'aumento delle quote di account il Batch, ad esempio, vedere [le quote e i limiti per il servizio di Azure Batch](batch-quota-limit.md) .

## <a name="batch-management-net-azure-ad-and-resource-manager"></a>Batch gestione .NET, Azure Active Directory e Manager delle risorse

Quando si lavora con la raccolta Batch Management .NET, di solito anche utilizza [Azure Active Directory] [ aad_about] (Azure Active Directory) e la [Gestione di risorse di Azure][resman_overview]. Il progetto di esempio illustrato di seguito utilizza Azure Active Directory e Gestione risorse mentre viene illustrato come l'API di .NET Management Batch.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure stesso utilizza Azure Active Directory per l'autenticazione di relativi ai clienti, amministratori dei servizi e gli utenti dell'organizzazione. Nel contesto di .NET Management Batch, utilizzare Azure Active Directory per eseguire l'autenticazione di un abbonamento amministratore o condivisa. In questo modo la raccolta di gestione di query al servizio Batch ed eseguire le operazioni descritte in questo articolo.

Nel progetto di esempio illustrato di seguito Azure [Active Directory Authentication Library] [ aad_adal] (ADAL) viene utilizzato per richiedere all'utente per le proprie credenziali di Microsoft. Quando vengono fornite le credenziali di amministratore o coadministrator servizio, l'applicazione possibile per un elenco delle sottoscrizioni, ossia la query Azure e possono creare ed eliminare gruppi di risorse e account Batch.

### <a name="resource-manager"></a>Manager delle risorse

Quando si crea account Batch con la raccolta Batch Management .NET, è in genere creano loro all'interno di un [gruppo di risorse][resman_overview]. È possibile creare il gruppo di risorse a livello di programmazione tramite [ResourceManagementClient] [ resman_client] classe in [Gestione risorse .NET] [ resman_api] raccolta. Oppure è possibile aggiungere un account a un gruppo di risorse esistente creato in precedenza tramite il [portale di Azure][azure_portal].

## <a name="sample-project-on-github"></a>Progetto di esempio in GitHub

Per informazioni Batch Management .NET in azione, consultare [AccountManagment] [ acct_mgmt_sample] progetto di esempio in GitHub. Questa applicazione console illustra la creazione e l'utilizzo della [BatchManagementClient] [ net_mgmt_client] e [ResourceManagementClient][resman_client]. Viene inoltre l'utilizzo di Azure [Active Directory Authentication Library] [ aad_adal] (ADAL), che è necessario per entrambi i client.

Per eseguire l'applicazione di esempio correttamente, è necessario innanzitutto registrare con Azure Active Directory tramite il portale di Azure. Seguire i passaggi indicati nella sezione [aggiunta di un'applicazione](../active-directory/active-directory-integrating-applications.md#adding-an-application) nelle [applicazioni di integrazione con Azure Active Directory] [ aad_integrate] per registrare l'applicazione di esempio all'interno il proprio account del Directory predefinita. Assicurarsi di selezionare l' **Applicazione Client nativi** per il tipo di applicazione ed è possibile specificare qualsiasi URI valido (ad esempio `http://myaccountmanagementsample`) per **Reindirizzare URI**- non è necessario essere un endpoint di tipo real.

Dopo aver aggiunto l'applicazione, delegare l'autorizzazione di **Accesso Azure servizio Management come organizzazione** per l'applicazione di *API di gestione del servizio di Windows Azure* nelle impostazioni dell'applicazione nel portale:

![Autorizzazioni per l'applicazione nel portale di Azure][2]

> [AZURE.TIP] **API di gestione del servizio di Windows Azure** non viene visualizzata sotto *le autorizzazioni per le altre applicazioni*, fare clic su **Aggiungi applicazione**, selezionare **API di gestione del servizio di Windows Azure**, quindi fare clic sul pulsante di segno di spunta. Delegato autorizzazioni come specificato in precedenza.

Dopo aver aggiunto l'applicazione come descritto in precedenza, aggiornare `Program.cs` in [AccountManagment] [ acct_mgmt_sample] progetto di esempio con l'applicazione URI reindirizzare e ID Client. Nella scheda della **configurazione** dell'applicazione, è possibile trovare questi valori:

![Configurazione dell'applicazione nel portale di Azure][3]

[AccountManagment] [ acct_mgmt_sample] applicazione di esempio illustra le operazioni seguenti:

1. Acquisire un token di sicurezza da Azure Active Directory utilizzando [ADAL][aad_adal]. Se non è già stato effettuato l'utente, viene richiesto per le proprie credenziali di Azure.
2. Utilizzando il token di sicurezza ottenuto da Azure Active Directory, creare un [SubscriptionClient] [ resman_subclient] a query Azure per un elenco delle sottoscrizioni associati all'account. In questo modo, selezione di una sottoscrizione se più disponibili.
3. Creare un oggetto di credenziali associato all'abbonamento selezionato.
4. Creare [ResourceManagementClient] [ resman_client] utilizzando le credenziali.
5. Utilizzare [ResourceManagementClient] [ resman_client] per creare un gruppo di risorse.
6. Utilizzare [BatchManagementClient] [ net_mgmt_client] eseguire diverse operazioni in blocco account:
  - Creare un account Batch nel nuovo gruppo di risorse.
  - È possibile ottenere il nuovo account creato dal servizio di Batch.
  - Stampare i tasti di account per il nuovo account.
  - Rigenerare una nuova chiave primaria per l'account.
  - Stampare le informazioni di quota per l'account.
  - Stampare le informazioni di quota per la sottoscrizione.
  - Stampare tutti gli account all'interno della sottoscrizione.
  - Eliminare account appena creato.
7. Eliminare il gruppo di risorse.

Prima di eliminare il gruppo di account e delle risorse Batch appena creato, è possibile esaminare entrambi nel [portale di Azure][azure_portal]:

![Portale Azure visualizzato il gruppo di risorse e account Batch][1]
<br />
*Portale Azure la visualizzazione di nuovo gruppo di risorse e account Batch*

[aad_about]: ../active-directory/active-directory-whatis.md "Che cos'è Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scenari di autenticazione per Azure Active Directory"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrazione di applicazioni con Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspxs
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
