<properties
    pageTitle="Database SQL di provare: Utilizzo c# per creare un database SQL | Microsoft Azure"
    description="Provare a Database SQL per lo sviluppo di App SQL e c# e creare un Database di SQL Azure con c# utilizzando la raccolta di Database SQL di .NET."
    keywords="Provare a sql, sql c#"   
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="csharp"
   ms.workload="data-management"
   ms.date="10/04/2016"
   ms.author="sstein"/>

# <a name="try-sql-database-use-c-to-create-a-sql-database-with-the-sql-database-library-for-net"></a>Database SQL di provare a: Usare c# per creare un database SQL con la raccolta di Database SQL per .NET


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Informazioni su come utilizzare c# per creare un database SQL Azure con la [Raccolta di gestione di Microsoft Azure SQL per .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). In questo articolo viene descritto come creare un database con SQL e c#. Per creare pool di database flessibile, vedere [creare un pool di database flessibile](sql-database-elastic-pool-create-csharp.md).

La raccolta di gestione Database SQL Azure per .NET fornisce un [Manager delle risorse Azure](../azure-resource-manager/resource-group-overview.md)-API che venga inviato a capo [All'API REST di Database SQL basate su Manager delle risorse](https://msdn.microsoft.com/library/azure/mt163571.aspx)basati su.

>[AZURE.NOTE] Molte nuove funzionalità di Database SQL sono supportate solo se si usa il [modello di distribuzione di gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md), pertanto è necessario utilizzare sempre le ultime **raccolta di gestione Database SQL Azure per .NET ([documenti](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [Pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. Precedenti [librerie basate su modello di distribuzione classica](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) sono supportati solo, per la compatibilità in modo che si consiglia di utilizzare le raccolte di Manager delle risorse in base a più recente.

Per completare la procedura descritta in questo articolo, è necessario quanto segue:

- Un abbonamento Azure. Se è necessario un abbonamento a Azure, è sufficiente fare clic su **ACCOUNT gratuito** nella parte superiore della pagina e quindi tornare alla fine di questo articolo.
- Visual Studio. Per una copia gratuita di Visual Studio, vedere la pagina di [Download di Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .

>[AZURE.NOTE] In questo articolo consente di creare un nuovo database SQL vuoto. Modificare il metodo *CreateOrUpdateDatabase(...)* nell'esempio seguente per copiare i database, ridimensionare database, creare un database in un pool e così via. Per ulteriori informazioni, vedere classi [DatabaseCreateMode](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreatemode.aspx) e [DatabaseProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databaseproperties.aspx) .



## <a name="create-a-console-app-and-install-the-required-libraries"></a>Creare un'app console e installare le librerie necessarie

1. Avviare Visual Studio.
2. Fare clic su **File** > **nuova** > **progetto**.
3. Creare un c# **Applicazione Console** e denominarla: *SqlDbConsoleApp*


Per creare un database SQL con c#, caricare le librerie di gestione necessarie (tramite la [console di gestione pacchetto](http://docs.nuget.org/Consume/Package-Manager-Console)):

1. Fare clic su **Strumenti** > **Manager pacchetto NuGet** > **Console di gestione pacchetti**.
2. Tipo di `Install-Package Microsoft.Azure.Management.Sql –Pre` per installare l' ultima [Raccolta gestione SQL di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).
3. Tipo di `Install-Package Microsoft.Azure.Management.ResourceManager –Pre` per installare la [Libreria di Microsoft Azure Manager delle risorse](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
4. Tipo di `Install-Package Microsoft.Azure.Common.Authentication –Pre` per installare [Microsoft Azure comuni Authentication Library](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication). 



> [AZURE.NOTE] Gli esempi riportati in questo articolo utilizzano un'icona del modulo di ogni richiesta API e bloccano fino al completamento del resto chiamare il servizio sottostante. Sono disponibili i metodi asincrono.


## <a name="create-a-sql-database-server-firewall-rule-and-sql-database---c-example"></a>Creare un server di Database SQL, regola firewall e database SQL - c# esempio

Nell'esempio seguente viene creato un gruppo di risorse, server, regola firewall e un database SQL. Visualizzare, [creare un servizio principale per accedere alle risorse](#create-a-service-principal-to-access-resources) per ottenere il `_subscriptionId, _tenantId, _applicationId, and _applicationSecret` variabili.

Sostituire il contenuto di **Program.cs** con il seguente e aggiornare il `{variables}` con i valori di app (non includere nella selezione la `{}`).


    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbConsoleApp
    {
    class Program
       {
        // For details about these four (4) values, see
        // https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/
        static string _subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _tenantId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationSecret = "{your-password}";

        // Create management clients for the Azure resources your app needs to work with.
        // This app works with Resource Groups, and Azure SQL Database.
        static ResourceManagementClient _resourceMgmtClient;
        static SqlManagementClient _sqlMgmtClient;

        // Authentication token
        static AuthenticationResult _token;

        // Azure resource variables
        static string _resourceGroupName = "{resource-group-name}";
        static string _resourceGrouplocation = "{Azure-region}";

        static string _serverlocation = _resourceGrouplocation;
        static string _serverName = "{server-name}";
        static string _serverAdmin = "{server-admin-login}";
        static string _serverAdminPassword = "{server-admin-password}";

        static string _firewallRuleName = "{firewall-rule-name}";
        static string _startIpAddress = "{0.0.0.0}";
        static string _endIpAddress = "{255.255.255.255}";

        static string _databaseName = "{dbfromcsarticle}";
        static string _databaseEdition = DatabaseEditions.Basic;
        static string _databasePerfLevel = ""; // "S0", "S1", and so on here for other tiers


        static void Main(string[] args)
        {
            // Authenticate:
            _token = GetToken(_tenantId, _applicationId, _applicationSecret);
            Console.WriteLine("Token acquired. Expires on:" + _token.ExpiresOn);

            // Instantiate management clients:
            _resourceMgmtClient = new ResourceManagementClient(new Microsoft.Rest.TokenCredentials(_token.AccessToken));
            _sqlMgmtClient = new SqlManagementClient(new TokenCloudCredentials(_subscriptionId, _token.AccessToken));


            Console.WriteLine("Resource group...");
            ResourceGroup rg = CreateOrUpdateResourceGroup(_resourceMgmtClient, _subscriptionId, _resourceGroupName, _resourceGrouplocation);
            Console.WriteLine("Resource group: " + rg.Id);


            Console.WriteLine("Server...");
            ServerGetResponse sgr = CreateOrUpdateServer(_sqlMgmtClient, _resourceGroupName, _serverlocation, _serverName, _serverAdmin, _serverAdminPassword);
            Console.WriteLine("Server: " + sgr.Server.Id);

            Console.WriteLine("Server firewall...");
            FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);
            Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _databaseEdition, _databasePerfLevel);
            Console.WriteLine("Database: " + dbr.Database.Id);


            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupName, string resourceGroupLocation)
        {
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = resourceGroupLocation,
            };
            resourceMgmtClient.SubscriptionId = subscriptionId;
            ResourceGroup resourceGroupResult = resourceMgmtClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        static ServerGetResponse CreateOrUpdateServer(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverLocation, string serverName, string serverAdmin, string serverAdminPassword)
        {
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = "12.0"
                }
            };
            ServerGetResponse serverResult = sqlMgmtClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }


        static FirewallRuleGetResponse CreateOrUpdateFirewallRule(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string firewallRuleName, string startIpAddress, string endIpAddress)
        {
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = startIpAddress,
                    EndIpAddress = endIpAddress
                }
            };
            FirewallRuleGetResponse firewallResult = sqlMgmtClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
            return firewallResult;
        }



        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string databaseEdition, string databasePerfLevel)
        {
            // Retrieve the server that will host this database
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = DatabaseCreateMode.Default,
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel
                }
            };
            DatabaseCreateOrUpdateResponse dbResponse = sqlMgmtClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }



        private static AuthenticationResult GetToken(string tenantId, string applicationId, string applicationSecret)
        {
            AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/" + tenantId);
            _token = authContext.AcquireToken("https://management.core.windows.net/", new ClientCredential(applicationId, applicationSecret));
            return _token;
        }
      }
    }





## <a name="create-a-service-principal-to-access-resources"></a>Creare un servizio principale per accedere alle risorse

Il seguente script di PowerShell crea l'applicazione di Active Directory (AD) e l'identità di servizio che è necessario eseguire l'autenticazione applicazione c#. Lo script restituisce i valori requisiti per l'esempio c# precedente. Per informazioni dettagliate, vedere [Usare PowerShell Azure per creare un servizio principale per accedere alle risorse](../resource-group-authenticate-service-principal.md).

   
    # Sign in to Azure.
    Add-AzureRmAccount
    
    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.
    
    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"
    
    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret
    
    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    
    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15
    
    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    
    
    # Output the values we need for our C# application to successfully authenticate
    
    Write-Output "Copy these values into the C# sample app"
    
    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret



## <a name="next-steps"></a>Passaggi successivi
Ora che si è provate diverse soluzioni di Database SQL e configurare un database con c#, si è pronti per gli articoli seguenti:

- [Connettersi al Database SQL di SQL Server Management Studio ed eseguire una query di Transact-SQL di esempio](sql-database-connect-query-ssms.md)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Database SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [Classe di database](https://msdn.microsoft.com/library/azure/microsoft.azure.management.sql.models.database.aspx)




<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png
