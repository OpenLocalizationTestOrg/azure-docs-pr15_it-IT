<properties 
    pageTitle="Utilizzo di .NET SDK per accedere API di servizio coinvolgimento di Azure Mobile" 
    description="Viene descritto come utilizzare Mobile coinvolgimento .NET SDK per accedere a API del servizio di Azure Mobile coinvolgimento"        
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>Utilizzo di .NET SDK per accedere API di servizio coinvolgimento di Azure Mobile

Azure Mobile coinvolgimento esposto un insieme di API per la gestione di dispositivi, portata/Push campagne e così via. Per interagire con queste API, è possibile utilizzare è un [file Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) che è possibile utilizzare con gli strumenti per generare SDK per la lingua preferita. È consigliabile usare lo strumento [AutoRest](https://github.com/Azure/AutoRest) per generare il SDK dal file Swagger. 

Abbiamo creato .NET SDK in modo analogo che consente di interagire con queste API usando un spaziale c# e non è necessario eseguire la stato token di autenticazione e aggiornare manualmente.  

In questo esempio passa attraverso il set di passaggi da seguire per utilizzare .NET SDK:

1. Prima di tutto, è necessario configurare l'autenticazione per l'API utilizzando Azure Active Directory come descritto [di seguito](mobile-engagement-api-authentication.md#authentication). Al termine della procedura, è necessario disporre un valido **SubscriptionId**, **TenantId**, **ID applicazione** e **segreto**. 

2. Verrà usata una semplice applicazione Console di Windows per illustrare l'utilizzo di .NET SDK con lo scenario di creazione di una campagna annuncio. Pertanto aprire Visual Studio e creare un' **Applicazione Console**.   

3. Successivamente è necessario scaricare .NET SDK, disponibile come **Raccolta gestione coinvolgimento di Microsoft Azure** nella raccolta Nuget [qui](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/).
Se si sta installando la Nuget da Visual Studio, è necessario assicurarsi di avere controllo contrassegnato l'opzione **Includi preliminare** durante la ricerca per il pacchetto:

    ![][1]

4. Nel `Program.cs` file, aggiungere gli spazi dei nomi seguenti:

        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;

5. Successivamente è necessario definire le costanti seguenti che verrà usata per l'autenticazione e interazione con l'App coinvolgimento Mobile in cui si sta creando la campagna annuncio:

        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";

        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";

        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";

6. Definire il `EngagementManagementClient` variabile che verrà utilizzato per chiamare i metodi di SDK coinvolgimento Mobile:

        static EngagementManagementClient engagementClient; 

7. Aggiungere le operazioni seguenti per il `Main` metodo:

        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();

                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }

8. Definire il metodo seguente che si occupa di inizializzazione il `EngagementManagementClient` autenticazione prima di tutto e quindi associando stesso con l'App coinvolgimento Mobile in cui si intende creare campagna annuncio:

        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
            
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;

            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }

    > [AZURE.IMPORTANT] Si noti che è necessario usare il **Nome di risorsa App** definito nel portale di gestione Azure per il parametro NomeApplicazione. 

9. Infine, definire il metodo CreateCampaign che consente di risolvere utilizzando EngagementClient inizializzato in precedenza per creare un semplice **in qualsiasi momento** & **solo notifica** campagna con un titolo e un messaggio: 

        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/en-us/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );

            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/en-us/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }

10. Eseguire l'app console per visualizzare le operazioni seguenti nella creazione della campagna:

    **Id campagna '159' è stato creato correttamente e si trova in stato di 'bozza'**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
