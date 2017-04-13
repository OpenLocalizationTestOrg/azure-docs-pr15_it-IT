<properties 
    pageTitle="Esercitazione: Creare una pipeline con copia attività utilizzando l'API .NET | Microsoft Azure" 
    description="In questa esercitazione, creare una pipeline di Azure Data Factory con un'attività di copia tramite l'API di .NET." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="10/27/2016" 
    ms.author="spelluru"/>

# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Esercitazione: Creare una pipeline con attività di copia tramite l'API di .NET
> [AZURE.SELECTOR]
- [Panoramica e i prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Copia guidata](data-factory-copy-data-wizard-tutorial.md)
- [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Modelli di Azure Manager delle risorse](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)


In questa esercitazione viene illustrato come creare e monitorare una factory di dati di Azure tramite l'API di .NET. La pipeline di factory dati utilizza un'attività di copia per copiare i dati dallo spazio di archiviazione Blob Azure al Database SQL Azure.

L'attività di copia esegue lo spostamento dei dati di Azure Data Factory. L'attività è basata su un servizio disponibile a livello globale che è possibile copiare i dati tra diversi archivi di dati in modo sicuro, affidabile e scalabilità. Vedere l'articolo per informazioni dettagliate sull'attività Copia [Le attività di spostamento dei dati](data-factory-data-movement-activities.md) .   

> [AZURE.NOTE] 
> In questo articolo non comprende tutte le API .NET Factory di dati. Per informazioni dettagliate sui dati Factory .NET SDK, vedere [Data Factory.NET API Reference](https://msdn.microsoft.com/library/mt415893.aspx) . 

## <a name="prerequisites"></a>Prerequisiti
- Verranno illustrate [esercitazione panoramica e i prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per ottenere una panoramica dell'esercitazione e completare i passaggi **prerequisito** . 
- Visual Studio 2012 o 2013 o 2015
- Scaricare e installare [Azure.NET SDK](http://azure.microsoft.com/downloads/)
- Azure PowerShell. Seguire istruzioni nell'articolo [come installare e configurare Azure PowerShell](../powershell-install-configure.md) per installare PowerShell Azure nel computer in uso. Utilizzare Azure PowerShell per creare un'applicazione di Azure Active Directory.

### <a name="create-an-application-in-azure-active-directory"></a>Creare un'applicazione di Azure Active Directory
Creare un'applicazione di Azure Active Directory, creare un'entità servizio per l'applicazione e assegnare il ruolo di **Collaboratore Factory dati** .  

1. Avviare **PowerShell**. 
1. Eseguire il comando seguente e immettere il nome utente e password utilizzati per accedere al portale di Azure.
    
        Login-AzureRmAccount   
2. Eseguire il comando seguente per visualizzare tutte le sottoscrizioni per l'account.

        Get-AzureRmSubscription 
3. Eseguire il seguente comando per selezionare l'abbonamento a cui si desidera utilizzare. Sostituire ** &lt;NameOfAzureSubscription** &gt; con il nome del proprio abbonamento Azure. 

        Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext

    > [AZURE.IMPORTANT] Nota verso il basso **SubscriptionId** e **TenantId** dall'output di questo comando. 
4. Creare un gruppo di risorse Azure denominato **ADFTutorialResourceGroup** eseguendo il seguente comando di PowerShell.  

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Se il gruppo di risorse esiste già, specificare se aggiornarlo (Y) o mantenerlo come (N). 

    Se si usa un gruppo di risorse diverso, è necessario utilizzare il nome del gruppo di risorse al posto di ADFTutorialResourceGroup in questa esercitazione.
5. Creare un'applicazione di Azure Active Directory. 

        $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"

    Se viene visualizzato l'errore seguente, specificare un URL diverso ed eseguire nuovamente il comando. 

        Another object with the same value for property identifierUris already exists.

6. Creare l'annuncio principale del servizio. 

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

7. Aggiungere il ruolo di **Collaboratore Factory dati** principale del servizio. 

        New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

8. Ottenere l'ID dell'applicazione.

        $azureAdApplication

    Nota verso il basso l'ID dell'applicazione (**ID applicazione** dall'output).

È necessario disporre dopo quattro valori da questa procedura: 

- ID tenant
- ID abbonamento
- ID applicazione 
- Password (specificata nel primo)   

## <a name="walkthrough"></a>Procedura dettagliata
1. Utilizzando Visual Studio 2012/2013/2015, creare un'applicazione console c# .NET.
    1. Avviare **Visual Studio** 2012/2013/2015.
    2. Fare clic su **File**, scegliere **Nuovo**e fare clic su **progetto**.
    3. Espandere **modelli**e selezionare **Visual c#**. In questa procedura dettagliata, si utilizza c#, ma è possibile utilizzare qualsiasi linguaggio .NET.
    4. Selezionare **Applicazione Console** dall'elenco dei tipi di progetto a destra.
    5. Immettere il nome **DataFactoryAPITestApp** .
    6. Selezionare **C:\ADFGetStarted** relativa al percorso.
    7. Fare clic su **OK** per creare il progetto.
2. Fare clic su **Strumenti**, scegliere **Gestione pacchetti Nuget**e fare clic su **Gestione pacchetti Console**.
3.  Nella **Console di gestione pacchetti**, eseguire la procedura seguente: 
    1.  Eseguire il comando seguente per installare pacchetto Factory dati:`Install-Package Microsoft.Azure.Management.DataFactories`       
    2.  Eseguire il comando seguente per installare il pacchetto di Azure Active Directory (Active Directory API è utilizzare il codice):`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
6. Aggiungere la sezione seguente **appSetttings** al file **App** . Queste impostazioni vengono utilizzate dal metodo di supporto: **GetAuthorizationHeader**. 

    Sostituire i valori per ** &lt;ID applicazione di&gt;**, ** &lt;Password&gt;**, ** &lt;ID abbonamento&gt;**, e ** &lt;tenant ID&gt; ** con valori personalizzati. 

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
            </startup>
            <appSettings>
                <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
                <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
                <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

                <add key="ApplicationId" value="your application ID" />
                <add key="Password" value="Password you used while creating the AAD application" />
                <add key="SubscriptionId" value= "Subscription ID" />
                <add key="ActiveDirectoryTenantId" value="Tenant ID" />
            </appSettings>
        </configuration>
6. Aggiungere le seguenti istruzioni **using** al file di origine (Program.cs) nel progetto.

        using System.Threading;
        using System.Configuration;
        using System.Collections.ObjectModel;
        
        using Microsoft.Azure.Management.DataFactories;
        using Microsoft.Azure.Management.DataFactories.Models;
        using Microsoft.Azure.Management.DataFactories.Common.Models;
        
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure;
6. Aggiungere il codice seguente che crea un'istanza della classe **DataPipelineManagementClient** per il metodo **principale** . Utilizzare questo oggetto per creare una factory di dati, un servizio collegato, set di dati di input e di output e una pipeline. È inoltre possibile utilizzare questo oggetto per monitorare le sezioni di un set di dati in fase di esecuzione.    

            // create data factory management client
            string resourceGroupName = "ADFTutorialResourceGroup";
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials =
                new TokenCloudCredentials(
                    ConfigurationManager.AppSettings["SubscriptionId"],
                    GetAuthorizationHeader());

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

    > [AZURE.IMPORTANT] 
    > Sostituire il valore di **resourceGroupName** con il nome del gruppo risorse Azure. 
    > 
    > Aggiornare nome di dati (**dataFactoryName**) sia univoco. Nome della factory dati deve essere univoco globale. Vedere l'argomento relativo a [Dati Factory - regole di denominazione](data-factory-naming-rules.md) per le regole di denominazione per gli elementi di dati Factory. 

7. Aggiungere il codice seguente che crea una **factory dati** al metodo **Main** .

            // create a data factory
            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties() { }
                    }
                }
            );

8. Aggiungere il codice seguente che consente di creare un' **archiviazione Azure collegato servizio** al metodo **Main** . 

    > [AZURE.IMPORTANT] Sostituire **storageaccountname** e **accountkey** con nome e la chiave del proprio account di archiviazione Azure. 

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );
9. Aggiungere il codice seguente che consente di creare un **SQL Azure collegato servizio** al metodo **Main** .
 
    > [AZURE.IMPORTANT] Sostituire **nomeserver**, **NomeDatabase**, **nome utente**e **password** con nomi del server SQL Azure, database, utente e password.  

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure SQL Database linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureSqlLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                        )
                    }
                }
            );
10. Aggiungere il codice seguente che crea **set di dati di input e di output** per il metodo **principale** . 

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "DatasetBlobSource";
            string Dataset_Destination = "DatasetAzureSqlDestination";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,


            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        Structure = new List<DataElement>()
                        {
                            new DataElement() { Name = "FirstName", Type = "String" },
                            new DataElement() { Name = "LastName", Type = "String" }
                        },
                        LinkedServiceName = "AzureStorageLinkedService",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });


            Console.WriteLine("Creating output dataset of type: Azure SQL");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            Structure = new List<DataElement>()
                            {
                                new DataElement() { Name = "FirstName", Type = "String" },
                                new DataElement() { Name = "LastName", Type = "String" }
                            },
                            LinkedServiceName = "AzureSqlLinkedService",
                            TypeProperties = new AzureSqlTableDataset()
                            {
                                TableName = "emp"
                            },

                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

11. Aggiungere il codice seguente che **Crea e attiva una pipeline** al metodo **Main** . Questa pipeline ha **CopyActivity** che accetta **BlobSource** come origine e **BlobSink** come sink.

            // create a pipeline
            Console.WriteLine("Creating a pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipeline";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Demo Pipeline for data transfer between blobs",

                    // Initial value for pipeline's active period. With this, you won't need to set slice status
                    Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "BlobToAzureSql",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    TypeProperties = new CopyActivity()
                                    {
                                        Source = new BlobSource(),
                                        Sink = new BlobSink()
                                        {
                                            WriteBatchSize = 10000,
                                            WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                        }
                                    }
                                }
                            },
                        }
                    }
                }); 

12. Aggiungere il codice seguente al metodo **principale** per ottenere lo stato di una sezione di dati del set di dati di output. Esiste solo sezione previsto in questo esempio.   
 
            // Pulling status within a timeout threshold
            DateTime start = DateTime.Now;
            bool done = false;

            while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
            {
                Console.WriteLine("Pulling the slice status");
                // wait before the next status check
                Thread.Sleep(1000 * 12);

                var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
                    new DataSliceListParameters()
                    {
                        DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                        DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
                    });

                foreach (DataSlice slice in datalistResponse.DataSlices)
                {
                    if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
                    {
                        Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                        done = true;
                        break;
                    }
                    else
                    {
                        Console.WriteLine("Slice status is: {0}", slice.State);
                    }
                }
            }

14. Aggiungere il codice riportato di seguito per ottenere eseguire dettagli per una sezione di dati per il metodo **principale** .

            Console.WriteLine("Getting run details of a data slice");

            // give it a few minutes for the output slice to be ready
            Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
            Console.ReadKey();

            var datasliceRunListResponse = client.DataSliceRuns.List(
                    resourceGroupName,
                    dataFactoryName,
                    Dataset_Destination,
                    new DataSliceRunListParameters()
                    {
                        DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                    }
                );

            foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
            {
                Console.WriteLine("Status: \t\t{0}", run.Status);
                Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
                Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
                Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
                Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
                Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
                Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
            }

            Console.WriteLine("\nPress any key to exit.");
            Console.ReadKey();

13. Aggiungere il metodo di supporto seguenti utilizzato dal metodo **principale** per la classe di **programma** .  
 
        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    ClientCredential credential = new ClientCredential(ConfigurationManager.AppSettings["ApplicationId"], ConfigurationManager.AppSettings["Password"]);
                    result = context.AcquireToken(resource: ConfigurationManager.AppSettings["WindowsManagementUri"], clientCredential: credential);
                }
                catch (Exception threadEx)
                {
                    Console.WriteLine(threadEx.Message);
                }
            });

            thread.SetApartmentState(ApartmentState.STA);
            thread.Name = "AcquireTokenThread";
            thread.Start();
            thread.Join();

            if (result != null)
            {
                return result.AccessToken;
            }

            throw new InvalidOperationException("Failed to acquire token");
        }  


15. In Esplora soluzioni espandere il progetto (**DataFactoryAPITestApp**) e fare clic su **Aggiungi riferimento**rapida **riferimenti**. Selezionare la casella di controllo per assembly "**Configuration**" e fare clic su **OK**. 
16. Creare l'applicazione console. Scegliere **Genera** dal menu di scelta e fare clic su **Compila soluzione**. 
16. Verificare che sia presente almeno un file nel contenitore di **adftutorial** in archiviazione blob Azure. In caso contrario, creare file **Emp.txt** nel blocco note con il seguente contenuto e caricare il contenitore adftutorial.

        John, Doe
        Jane, Doe
     
17. Eseguire l'esempio facendo clic su **Debug** -> **Avvia debug** dal menu. Quando viene visualizzata la **Guida eseguire dettagli di una sezione di dati**, attendere qualche minuto e premere **INVIO**. 
18. Utilizzare il portale di Azure per verificare che la factory di dati **APITutorialFactory** viene creato con gli elementi seguenti: 
    - Collegato servizio: **LinkedService_AzureStorage** 
    - Set di dati: **DatasetBlobSource** e **DatasetBlobDestination**.
    - Pipeline: **PipelineBlobSample** 
18. Verificare che i record due dipendente vengono creati nella tabella "**emp**" nel database di SQL Azure specificato.

## <a name="next-steps"></a>Passaggi successivi

- Leggere l'articolo, [Le attività di spostamento dei dati](data-factory-data-movement-activities.md) che fornisce informazioni dettagliate sull'attività copia utilizzato nell'esercitazione.
- Per informazioni dettagliate sui dati Factory .NET SDK, vedere [Data Factory.NET API Reference](https://msdn.microsoft.com/library/mt415893.aspx) . In questo articolo non comprende tutte le API .NET Factory di dati. 

 
