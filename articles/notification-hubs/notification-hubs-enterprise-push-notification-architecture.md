<properties
    pageTitle="Notifica hub - architettura Push aziendale"
    description="Indicazioni sull'uso di Azure notifica hub in un ambiente aziendale"
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="enterprise-push-architectural-guidance"></a>Architettura di push Enterprise

Aziende siano oggi gradualmente spostando verso la creazione di applicazioni per dispositivi mobili per le versioni utenti finali (esterna) o per i dipendenti (interni). Devono avere sistemi back-end esistenti nella posizione che si tratti di mainframe o alcune applicazioni line che devono essere integrati con l'architettura di applicazione per dispositivi mobili. Questa Guida verrà illustrate modo migliore per eseguire questa integrazione consigliare possibili soluzioni per gli scenari comuni.

Un requisito di frequente è per l'invio di notifica push agli utenti tramite le proprie applicazioni mobili quando si verifica un evento di interesse nei sistemi back-end. Ad esempio un cliente bancario con app bancarie della banca sul proprio iPhone desidera ricevere una notifica quando viene effettuata una carta di debito sopra un determinato periodo dal proprio account o una rete Intranet in cui un dipendente del reparto finanze con un'app per l'approvazione del budget nel suo Windows Phone vuole ricevere una notifica quando ricevere una richiesta di approvazione.

Il conto bancario o l'elaborazione di approvazione problema può essere eseguita in un sistema di back-end che è necessario avviare un push all'utente. Potrebbero essere presenti più tali sistemi di back-end che compilare tutti lo stesso tipo di logica per implementare push quando un evento genera una notifica. La complessità qui riguarda l'integrazione di diversi sistemi di back-end insieme a un sistema di push singola nel punto in cui gli utenti finali potrebbe aver sottoscritto notifiche diverse e possono anche essere più applicazioni per dispositivi mobili, ad esempio nel caso di App per dispositivi mobili intranet in un'applicazione per dispositivi mobili può essere utile ricevere notifiche da più tali sistemi di back-end. I sistemi di back-end non fonti di informazioni o informazioni utili di semantica/tecnologia push in modo che in genere, una soluzione di comune è stata per introdurre un componente di polling i sistemi di back-end per tutti gli eventi di interesse ed è responsabile per l'invio di messaggi push al client.
Di seguito verrà discusso su una soluzione migliore utilizzo Bus di servizio Azure - modello argomento/sottoscrizione semplificherà mentre si effettua la soluzione scalable.

Ecco architettura generale della soluzione (GRG con App per dispositivi mobili più ma applicabile quando è presente un solo app per dispositivi mobili)

## <a name="architecture"></a>Architettura

![][1]

La parte tasto architettonica diagramma è Bus di servizio Azure che fornisce argomenti/abbonamenti modello (ulteriori informazioni su di esso nella [programmazione servizio Bus Pub/Sub]) di programmazione. Il ricevitore, in questo caso è back-end per dispositivi mobili (in genere [Servizio Mobile Azure], che verrà avviata push per l'App per dispositivi mobili) non riceve messaggi direttamente da sistemi back-end, ma se, tuttavia si dispone di un livello di astrazione intermedio fornito da [Azure servizio Bus] che consente di back-end per dispositivi mobili ricevere i messaggi da uno o più sistemi di back-end. Un argomento di Bus servizio deve essere creato per ogni i sistemi di back-end, ad esempio Account, risorse Umane, finanza che sono praticamente "argomenti" di interesse che verrà avviata messaggi da inviare come notifica push. I sistemi di back-end possa inviare messaggi a questi argomenti. Un back-end Mobile possibile sottoscrivere uno o più argomenti quali mediante la creazione di una sottoscrizione Bus di servizio. Questo danno back-end per dispositivi mobili per ricevere una notifica dal sistema back-end corrispondente. Dispositivi mobili back-end continua a ascoltare i messaggi nel loro abbonamenti e come arriva un messaggio si trasforma e Invia come notifica per il proprio hub di notifica. Hub di notifica e infine recapitato a app per dispositivi mobili. Per riepilogare i componenti principali, sono presenti:

1. Sistemi di back-end (sistemi line/Legacy)
    - Crea servizio Bus argomento
    - Invia messaggio
2. Back-end per dispositivi mobili
    - Crea sottoscrizione assistenza
    - Messaggio (da sistema back-end)
    - Invia notifica ai client (tramite Hub di notifica di Azure)
3. Applicazione per dispositivi mobili
    - Riceve e Mostra notifica

###<a name="benefits"></a>Vantaggi:

1. La separazione tra il mittente (sistemi back-end) e al destinatario (app/servizio mobile tramite notifica Hub) consente ai sistemi di back-end aggiuntivi integrati con modifiche minime.
2. In questo modo anche lo scenario di App per dispositivi mobili più la possibilità di ricevere gli eventi da uno o più sistemi di back-end.  

## <a name="sample"></a>Esempio:

###<a name="prerequisites"></a>Prerequisiti
È necessario completare le seguenti esercitazioni per acquisire con i concetti, nonché creazione comune e i passaggi di configurazione:

1. [Programmazione servizio Bus Pub/Sub] - vengono descritti i dettagli relativi all'utilizzo con Bus argomenti/sottoscrizioni di assistenza, come creare uno spazio dei nomi per contenere argomenti/sottoscrizioni, come Invia e Ricevi messaggi da essi.
2. [Notifica hub - Windows universale esercitazione] - viene descritto come configurare un'app di Windows Store e utilizzare gli hub di notifica per registrare e quindi ricevere notifiche.

###<a name="sample-code"></a>Codice di esempio

Il codice di esempio completo è disponibile nella [Notifica Hub esempi]. Verrà suddiviso in tre componenti:

1. **EnterprisePushBackendSystem**

    un. Il progetto viene utilizzato il pacchetto Nuget *WindowsAzure.ServiceBus* e dipende dal [servizio Bus Pub/Sub programmazione].

    b. Si tratta di una semplice c# console app per simulare un sistema line che avvia il messaggio a app per dispositivi mobili.

        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the topic where we will send notifications
            CreateTopic(connectionString);

            // Send message
            SendMessage(connectionString);
        }

    c. `CreateTopic`viene utilizzato per creare l'argomento Bus di servizio in cui viene inviato messaggi.

        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already

            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }

    d. `SendMessage`viene utilizzato per inviare i messaggi in questo argomento Bus di servizio. Qui stiamo inviando semplicemente un insieme di messaggi all'argomento periodicamente per il campione. In genere si verificherà un sistema di back-end che verrà inviati messaggi quando si verifica un evento.

        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

            // Sends random messages every 10 seconds to the topic
            string[] messages =
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };

            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);

                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }

2. **ReceiveAndSendNotification**

    un. Il progetto utilizza pacchetti *WindowsAzure.ServiceBus* e *Microsoft.Web.WebJobs.Publish* Nuget e dipende dal [servizio Bus Pub/Sub programmazione].

    b. Si tratta di un altro c# console app che verrà eseguita come un [WebJob Azure] poiché previsti per l'esecuzione continua per ascoltare i messaggi provenienti da sistemi line/back-end. Questo faranno parte del back-end per dispositivi mobili.

        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the subscription which will receive messages
            CreateSubscription(connectionString);

            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }

    c. `CreateSubscription`viene utilizzato per creare una sottoscrizione Bus di servizio per l'argomento in cui il sistema di back-end verrà inviati messaggi. A seconda di uno scenario business questo componente creerà una o più sottoscrizioni agli argomenti corrispondenti (ad esempio, alcune possono messaggi ricevuti dal sistema delle risorse Umane, alcuni dal sistema finanze e così via)

        static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }

    d. ReceiveMessageAndSendNotification viene utilizzato per leggere il messaggio l'argomento mediante la sottoscrizione e se la lettura ha esito positivo per essere inviate all'applicazione per dispositivi mobili con gli hub di notifica di Azure creare quindi una notifica (nello scenario di esempio una notifica di tipo avviso popup nativo di Windows).

        static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");

            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);

            Client.Receive();

            // Continuously process messages received from the subscription
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");

                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);

                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            }
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }

    e. Per la pubblicazione seguente come un **WebJob**, fare clic con il pulsante destro sulla soluzione in Visual Studio e selezionare **Pubblica come WebJob**

    ![][2]

    f. Selezionare il profilo di pubblicazione e creare un nuovo sito Web Azure se non esiste già che conterrà questo WebJob e dopo avere inserito il sito Web quindi **pubblica**.

    ![][3]

    g. Configurare il processo per essere "Continuamente eseguire" in modo che quando si accede al [Portale classica Azure] verrà visualizzato il seguente:

    ![][4]


3. **EnterprisePushMobileApp**

    un. Si tratta di un'applicazione di Windows Store che riceverà le notifiche di tipo avviso popup dal WebJob in esecuzione come parte del back-end per dispositivi mobili e visualizzarlo. Questo è basato su [Notifica hub - esercitazione universale di Windows].  

    b. Assicurarsi che l'applicazione è attivata la ricezione delle notifiche di tipo avviso popup.

    c. Assicurarsi che seguenti hub di notifica di codice per la registrazione è chiamato in App avvio (dopo la sostituzione di *HubName* e *DefaultListenSharedAccessSignature*:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### <a name="running-sample"></a>Esecuzione di esempio:

1. Assicurarsi che il WebJob è in esecuzione correttamente e programmato "Continuamente eseguire".
2. Eseguire **EnterprisePushMobileApp** che verrà avviata l'app di Windows Store.
3. Eseguire l'applicazione console **EnterprisePushBackendSystem** che consente di simulare back-end Line e verrà avviato l'invio di messaggi e si devono le notifiche di tipo avviso popup visualizzata analoga alla seguente:

    ![][5]

4. I messaggi sono stati inviati originariamente Bus di servizio ad argomenti in cui è stato controllato dagli abbonamenti Bus di servizio nel processo di Web. Dopo aver ricevuto un messaggio, una notifica creata e inviata a app per dispositivi mobili. È possibile esaminare i registri WebJob per confermare l'elaborazione quando si passa il collegamento log in [Azure classica portale] per il lavoro Web:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Esempi di Hub di notifica]: https://github.com/Azure/azure-notificationhubs-samples
[Servizio Mobile Azure]: http://azure.microsoft.com/documentation/services/mobile-services/
[Bus di servizio Azure]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Programmazione servizio Bus Pub/Sub]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[WebJob Azure]: http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/
[Notifica hub - esercitazione universale di Windows]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Portale classica Azure]: https://manage.windowsazure.com/
