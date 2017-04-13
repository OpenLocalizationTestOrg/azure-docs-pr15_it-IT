<properties 
    pageTitle="Azure impegno per dispositivi mobili - integrazione back-end" 
    description="Connettere Azure Mobile impegno con un back-end di SharePoint per creare le campagne da SharePoint" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement---api-integration"></a>Azure impegno per dispositivi mobili - integrazione API

In un sistema di marketing automatico, la creazione e l'attivazione di campagne di marketing anche automatici. A questo scopo - coinvolgimento Mobile Azure consente di creare tali campagne di marketing automatizzati anche mediante API. 

In genere clienti utilizzano l'interfaccia di front-end Mobile impegno per creare annunci/sondaggi e così via come parte del loro campagne di marketing. Tuttavia appena consolidate le campagne di marketing, è necessario per utilizzare al meglio i dati bloccati nei sistemi back-end (ad esempio un sistema CRM o sistema CMS come SharePoint) in modo che è possibile creare una pipeline completamente automatica che consente di creare campagne di impegno Mobile in modo dinamico in base ai dati che scorre in dai sistemi di back-end. 

![][5]

In questa esercitazione attraversa scenario in cui gli utenti aziendali SharePoint popola un elenco di SharePoint con dati di marketing e un processo automatizzato raccoglie gli elementi dall'elenco e si connette con il sistema di impegno Mobile tramite l'API REST disponibili per creare una campagna di marketing dai dati di SharePoint. 
 
> [AZURE.IMPORTANT] In generale, è possibile utilizzare in questo esempio come punto di partenza per capire meglio come chiamare le API REST di impegno Mobile come descrive i due aspetti fondamentali della chiamata API - parametri di autenticazione e passando. 

## <a name="sharepoint-integration"></a>Integrazione di SharePoint
1. Ecco l'aspetto dell'elenco di SharePoint di esempio. **Titolo**, **categoria**, **NotificationTitle**, **messaggio** e **l'URL** vengono utilizzati per creare l'annuncio. Esiste una colonna denominata **IsProcessed** che viene utilizzato dal processo di automazione di esempio in forma di un programma console. È possibile eseguire questa console programmare come un WebJob Azure in modo che è possibile pianificare o è possibile utilizzare direttamente il flusso di lavoro di SharePoint al programma la creazione e l'attivazione dell'annuncio quando un elemento viene inserito un elenco di SharePoint. In questo esempio è il programma console che attraversa le voci di elenco SharePoint e creare un annuncio di Azure Mobile impegno per ognuno di essi e lo contrassegna infine il contrassegno **IsProcessed** true al momento della creazione annuncio esito negativo.

    ![][1]

2. Si sta utilizzando il codice di esempio *autenticazione remota in SharePoint Online con il modello a oggetti Client* [di seguito](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) per eseguire l'autenticazione con l'elenco di SharePoint.
 
3. Dopo l'autenticazione, abbiamo scorrere le voci di elenco per individuare eventuali elementi creati di recente (che sarà necessario **IsProcessed** = false). 

        static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);

                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();

                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;

                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;

                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";

                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Integrazione con coinvolgimento di dispositivi mobili
1.  Una volta che si trova un elemento che richiede l'elaborazione dei - abbiamo estrarre le informazioni necessarie per creare un annuncio dal voce di elenco e chiamare `CreateAzMECampaign` per la creazione e successivamente `ActivateAzMECampaign` per attivarlo. Si tratta essenzialmente chiamate API REST la chiamata a back-end coinvolgimento Mobile. 

2.  Le API REST di impegno Mobile richiedono un' **intestazione di autenticazione di base combinazione autorizzazione HTTP** costituito il `ApplicationId` e `ApiKey` che si ottiene dal portale di Azure. Verificare che si usa il tasto dalla sezione dei **tasti di api** e *non* dalla sezione dei **tasti di sdk** . 

    ![][2]

        static string CreateAuthZHeader()
        {
            string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
            return BasicAuthzHeaderString;
        }

        static public string EncodeTo64(string toEncode)
        {
            byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
            string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
            return returnValue;
        }  

3. Per la creazione dell'annuncio tipo campagna - fare riferimento alla [documentazione](https://msdn.microsoft.com/library/azure/mt683750.aspx). È necessario assicurarsi che si sta specificando la campagna `kind` come *annuncio* e [payload](https://msdn.microsoft.com/library/azure/mt683751.aspx) e passando come FormUrlEncodedContent. 

        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";

            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
                
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
                
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });

                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }

4. Dopo avere inserito l'annuncio creato, verrà visualizzato il seguente nel portale di impegno Mobile (si noti che lo stato = bozza e attivato = n/d)

    ![][3]

5. `CreateAzMECampaign`Crea una campagna annuncio e restituisce l'Id per il chiamante. `ActivateAzMECampaign`richiede l'Id come parametro per attivare la campagna. 

        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());

                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });

                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }

6. Dopo avere inserito l'annuncio attivata, verrà visualizzato il seguente nel portale di impegno Mobile:

    ![][4]

7. Come viene attivata la campagna, i dispositivi che soddisfano i criteri della campagna inizierà a visualizzare le notifiche. 

8. Si noterà che la voce di elenco contrassegnata con IsProcessed = false è stata impostata su True dopo aver creata la campagna annuncio.  

In questo esempio creata una campagna annuncio semplice specificando prevalentemente le proprietà necessarie. È possibile personalizzare questo come è possibile dal portale di in base alle informazioni [di seguito](https://msdn.microsoft.com/library/azure/mt683751.aspx). 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png


 
