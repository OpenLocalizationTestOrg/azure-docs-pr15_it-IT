<properties
    pageTitle="Applicazione in locale/cloud ibrida (.NET) | Microsoft Azure"
    description="Informazioni su come creare un'applicazione di in locale/cloud ibrida .NET tramite relay Bus di servizio Azure."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# <a name="net-on-premisescloud-hybrid-application-using-azure-service-bus-relay"></a>Applicazione in locale/cloud ibrida .NET tramite un inoltro Bus servizio Azure

## <a name="introduction"></a>Introduzione

In questo articolo viene descritto come creare un'applicazione di cloud ibrida con Microsoft Azure e Visual Studio. L'esercitazione si presuppone che nessun già esperienza nell'uso di Azure. In meno di 30 minuti, si avrà un'applicazione che utilizza risorse Azure più alto e in esecuzione nel cloud.

Si apprenderà:

-   Informazioni su come creare o adattare un servizio web esistente per il consumo da una soluzione web.
-   Come utilizzare il servizio di Azure servizio Bus inoltro per condividere i dati tra un'applicazione Azure e un servizio web ospitato altrove.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="how-the-service-bus-relay-helps-with-hybrid-solutions"></a>Come relay Service Bus agevola le soluzioni ibride

Soluzioni aziendali sono in genere composti da una combinazione di codice personalizzato scritto affrontare esigenze nuove e univoco e le funzionalità esistenti forniti da soluzioni e i sistemi già presenti.

Progettisti di soluzioni iniziano a usare il cloud per la gestione più semplice dei requisiti di scala e ridurre i costi operativi. In questo modo, si trova che le risorse di servizio che desiderano sfruttare come blocchi predefiniti per le soluzioni sono all'interno del firewall aziendale e disconnettersi da facile raggiungano per l'accesso per la soluzione cloud. Molti servizi interni sono progettati o non ospitati in modo possono essere esposti facilmente al margine della rete aziendale.

Relay Service Bus è progettato per il caso di utilizzo dei servizi web di Windows Communication Foundation (WCF) esistente e accessibilità tali servizi in modo sicuro alle soluzioni che si trovano all'esterno del perimetro aziendale senza richiedere modifiche intrusive l'infrastruttura di rete aziendale. Ad esempio servizi di inoltro Bus di servizio sono ancora ospitati in ambiente esistente, ma viene delegato in attesa di posta in arrivo sessioni e le richieste di Bus di servizio cloud ospitato. Servizio Bus protegge inoltre i servizi dall'accesso non autorizzato mediante l'autenticazione di [Firma di Access condiviso](../service-bus-messaging/service-bus-sas-overview.md) (SA).

## <a name="solution-scenario"></a>Soluzione

In questa esercitazione, è necessario creare un sito Web ASP.NET che consente di visualizzare un elenco dei prodotti nella pagina di inventario del prodotto.

![][0]

L'esercitazione presuppone che si dispone di informazioni sul prodotto in un sistema locale esistente e utilizza relay Service Bus raggiunga a quel sistema. Questa situazione viene simulata da un servizio web che viene eseguita in un'applicazione console semplice e supportato da un set di in memoria dei prodotti. Sarà possibile eseguire questa applicazione console nel proprio computer e distribuire il ruolo web in Azure. In questo modo, verrà visualizzato come ruolo web in esecuzione in Data Center del Azure eseguiranno una chiamata effettivamente nel computer, anche se il computer quasi sicuramente si troverà dietro almeno un firewall e un livello di conversione indirizzi di rete.

Di seguito è una schermata della pagina iniziale dell'applicazione web completata.

![][1]

## <a name="set-up-the-development-environment"></a>Configurare l'ambiente di sviluppo

Prima di sviluppo di applicazioni Azure, tutti gli strumenti e configurare l'ambiente di sviluppo.

1.  Installare Azure SDK per .NET dalla pagina di [ottenere strumenti e SDK][] .

2.  Fare clic su **Installa SDK** per la versione di Visual Studio in uso. La procedura descritta in questa esercitazione utilizza Visual Studio 2015.

4.  Quando viene richiesto di eseguire o salvare il programma di installazione, fare clic su **Esegui**.

5.  **Installazione guidata piattaforma Web**, fare clic su **installazione** e procedere con l'installazione.

6.  Al termine dell'installazione, sarà necessario tutto il necessario per iniziare a sviluppare l'app. il SDK include strumenti che consentono di semplificare lo sviluppo di applicazioni Azure in Visual Studio. Se non è installato Visual Studio, SDK installa anche la gratuito Visual Studio Express.

## <a name="create-a-namespace"></a>Creare uno spazio dei nomi

Per iniziare a usare le caratteristiche di Bus di servizio di Azure, è innanzitutto necessario creare uno spazio dei nomi del servizio. Uno spazio dei nomi fornisce un contenitore per indirizzare le risorse Bus di servizio all'interno dell'applicazione.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Creazione di un server locale

Creare innanzitutto un sistema di catalogo di prodotti locale (fittizio). Sarà semplice; è possibile vedere come che rappresenta un sistema di catalogo locale effettivo del prodotto con un'area di servizio che si sta tentando di integrare.

Il progetto è un'applicazione di console di Visual Studio e viene utilizzato il [pacchetto Azure servizio Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) per includere le raccolte di Bus di servizio e le impostazioni di configurazione.

### <a name="create-the-project"></a>Creare il progetto

1.  Uso dei privilegi di amministratore, avviare Microsoft Visual Studio. Per avviare Visual Studio con privilegi di amministratore, pulsante destro del mouse sull'icona dell'applicazione di **Visual Studio** e quindi fare clic su **Esegui come amministratore**.

2.  In Visual Studio, nel menu **File** fare clic su **Nuovo**e quindi fare clic su **progetto**.

3.  Dai **Modelli installati**in **c#**, fare clic su **Applicazione Console**. Nella casella **nome** digitare il nome **ProductsServer**:

    ![][11]

4.  Fare clic su **OK** per creare il progetto **ProductsServer** .

7.  Se è già stato installato il responsabile di pacchetto NuGet per Visual Studio, andare al passaggio successivo. In caso contrario, visitare [NuGet][] e fare clic su [Installa NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Seguire le istruzioni visualizzate per installare il responsabile di pacchetto NuGet, quindi riavviare Visual Studio.

7.  In Esplora soluzioni fare clic sul progetto **ProductsServer** e quindi fare clic su **Gestisci pacchetti NuGet**.

8.  Fare clic sulla scheda **Sfoglia** , quindi cercare `Microsoft Azure Service Bus`. Fare clic su **installazione**e accettare le condizioni di utilizzo.

    ![][13]

    Si noti che ora si fa riferimento assembly client richiesto.

9.  Aggiungere una nuova classe per il contratto di prodotto. In Esplora soluzioni fare clic sul progetto **ProductsServer** e fare clic su **Aggiungi**e quindi fare clic su **classe**.

10. Nella casella **nome** digitare il nome **ProductsContract.cs**. Fare clic su **Aggiungi**.

11. In **ProductsContract.cs**, sostituire la definizione dello spazio dei nomi con il codice seguente, che definisce il contratto per il servizio.

    ```
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;
    
        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }
    
        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();
    
        }
    
        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

12. In Program.cs, sostituire la definizione dello spazio dei nomi con il codice seguente, che consente di aggiungere il servizio profili e host dei file.

    ```
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;
    
        // Implement the IProducts interface.
        class ProductsService : IProducts
        {
    
            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };
    
            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }
    
        }
    
        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();
    
                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();
    
                sh.Close();
            }
        }
    }
    ```

13. In Esplora soluzioni fare doppio clic sul file **App** per aprirlo nell'editor di Visual Studio. Nella parte inferiore della ** &lt;sistema. ServiceModel&gt; ** elemento (ma si trovano all'interno &lt;sistema. ServiceModel&gt;), aggiungere il codice XML seguente. Assicurarsi di sostituire *yourServiceNamespace* con il nome di spazio dei nomi e *yourKey* insieme al tasto SA recuperata in precedenza dal portale di:

    ```
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
14. Ancora nell'App, nel ** &lt;appSettings&gt; ** elemento, la stringa di connessione valore con la stringa di connessione in precedenza ottenuto dal portale di sostituzione. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

14. Premere **Ctrl + MAIUSC + B** o dal menu **Compila** fare clic su **Compila soluzione** per generare l'applicazione e verificare l'accuratezza del proprio lavoro finora.

## <a name="create-an-aspnet-application"></a>Creare un'applicazione ASP.NET

In questa sezione si creerà una semplice applicazione ASP.NET che visualizza i dati recuperati dal servizio prodotto.

### <a name="create-the-project"></a>Creare il progetto

1.  Verificare che Visual Studio sia in esecuzione con privilegi di amministratore.

2.  In Visual Studio, nel menu **File** fare clic su **Nuovo**e quindi fare clic su **progetto**.

3.  Dai **Modelli installati**in **c#**, fare clic su **Applicazione Web ASP.NET**. Nome del progetto **ProductsPortal**. Fare clic su **OK**.

    ![][15]

4.  Nell'elenco **Selezionare un modello** , fare clic su **MVC**. 

6.  Selezionare la casella per **Host nel cloud**.

    ![][16]

5. Fare clic sul pulsante **Cambia autenticazione** . Nella finestra di dialogo **Modifica autenticazione** fare clic su **Nessuna autenticazione**e quindi fare clic su **OK**. Per questa esercitazione si distribuisce un'app non è necessario un account di accesso utente.

    ![][18]

6.  Nella sezione **Microsoft Azure** della finestra di dialogo **Nuovo progetto ASP.NET** , assicurarsi che sia selezionata **ospitato nel cloud** e che **Il servizio di App** sia selezionata nell'elenco a discesa.

    ![][19]

7. Fare clic su **OK**. 

8. A questo punto è necessario configurare Azure risorse per una nuova app web. Eseguire tutti i passaggi nella sezione [Configura Azure risorse per una nuova app web](../app-service-web/web-sites-dotnet-get-started.md#configure-azure-resources-for-a-new-web-app). Tornare a questa esercitazione, quindi procedere con il passaggio successivo.

5.  In Esplora soluzioni rapida **modelli** fare clic su **Aggiungi**e quindi fare clic su **classe**. Nella casella **nome** digitare il nome **Product.cs**. Fare clic su **Aggiungi**.

    ![][17]

### <a name="modify-the-web-application"></a>Modifica dell'applicazione web

1.  Nel file Product.cs in Visual Studio, sostituire la definizione dello spazio dei nomi esistente con il codice seguente.

    ```
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
        public class Product
        {
            public string Id { get; set; }
            public string Name { get; set; }
            public string Quantity { get; set; }
        }
    }
    ```

2.  In Esplora soluzioni espandere la cartella **controller** , quindi fare doppio clic sul file **HomeController.cs** per aprirlo in Visual Studio.

3. In **HomeController.cs**, sostituire la definizione dello spazio dei nomi esistente con il codice seguente.

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;
    
        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

3.  In Esplora soluzioni espandere la cartella Views\Shared, quindi fare doppio clic su **cshtml** per aprirlo nell'editor di Visual Studio.

5.  Modificare tutte le occorrenze di **Applicazione ASP.NET** ai **prodotti del LITWARE**.

6. Rimuovere i collegamenti **Home**, **sulle**e **contatto** . Nell'esempio seguente, eliminare il codice evidenziato.

    ![][41]

7.  In Esplora soluzioni espandere la cartella Views\Home, quindi fare doppio clic su **Index.cshtml** per aprirlo nell'editor di Visual Studio.
    Sostituire l'intero contenuto del file con il codice riportato di seguito.

    ```
    @model IEnumerable<ProductsWeb.Models.Product>
    
    @{
            ViewBag.Title = "Index";
    }
    
    <h2>Prod Inventory</h2>
    
    <table>
            <tr>
                <th>
                    @Html.DisplayNameFor(model => model.Name)
                </th>
                  <th></th>
                <th>
                    @Html.DisplayNameFor(model => model.Quantity)
                </th>
            </tr>
    
    @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Name)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Quantity)
                </td>
            </tr>
    }
    
    </table>
    ```

9.  Per verificare l'accuratezza del proprio lavoro finora, è possibile premere **Ctrl + MAIUSC + B** per compilare il progetto.


### <a name="run-the-app-locally"></a>Eseguire l'app in locale

Eseguire l'applicazione per verificare il funzionamento.

1.  Assicurarsi che **ProductsPortal** sia il progetto attivo. Pulsante destro del mouse sul nome del progetto in Esplora soluzioni e scegliere **Imposta come progetto di avvio**.
2.  In Visual Studio, premere F5.
3.  L'applicazione deve apparire in esecuzione in un browser.

    ![][21]

## <a name="put-the-pieces-together"></a>Unire le parti

Il passaggio successivo consiste agganciare server locale prodotti con l'applicazione ASP.NET.

1.  Se non è già aperto, in Visual Studio aprire nuovamente il progetto **ProductsPortal** creata nella sezione [creare un'applicazione ASP.NET](#create-an-aspnet-application) .

2.  Simile al passaggio nella sezione "Creare un Server locale", aggiungere il pacchetto NuGet ai riferimenti del progetto. In Esplora soluzioni fare clic sul progetto **ProductsPortal** e quindi fare clic su **Gestisci pacchetti NuGet**.

3.  Cercare "Servizio Bus" e selezionare l'elemento **Bus di servizio di Microsoft Azure** . Quindi completare l'installazione e chiudere la finestra di dialogo.

4.  In Esplora soluzioni fare clic sul progetto **ProductsPortal** e quindi fare clic su **Aggiungi**, quindi **Elemento esistente**.

5.  Passare al file **ProductsContract.cs** dal progetto console **ProductsServer** . Fare clic su per evidenziare ProductsContract.cs. Fare clic sulla freccia accanto ad **Aggiungi**, quindi fare clic su **Aggiungi come collegamento**.

    ![][24]

6.  Ora aprire il file **HomeController.cs** nell'editor di Visual Studio e sostituire la definizione dello spazio dei nomi con il codice riportato di seguito. Assicurarsi di sostituire *yourServiceNamespace* con il nome del servizio dello spazio dei nomi e *yourKey* con la chiave SA. In questo modo il client per chiamare il servizio locale, restituendo il risultato della chiamata.

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Linq;
        using System.ServiceModel;
        using System.Web.Mvc;
        using Microsoft.ServiceBus;
        using Models;
        using ProductsServer;
    
        public class HomeController : Controller
        {
            // Declare the channel factory.
            static ChannelFactory<IProductsChannel> channelFactory;
    
            static HomeController()
            {
                // Create shared access signature token credentials for authentication.
                channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                    "sb://yourServiceNamespace.servicebus.windows.net/products");
                channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                    TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                        "RootManageSharedAccessKey", "yourKey") });
            }
    
            public ActionResult Index()
            {
                using (IProductsChannel channel = channelFactory.CreateChannel())
                {
                    // Return a view of the products inventory.
                    return this.View(from prod in channel.GetProducts()
                                     select
                                         new Product { Id = prod.Id, Name = prod.Name,
                                             Quantity = prod.Quantity });
                }
            }
        }
    }
    ```

7.  In Esplora soluzioni fare doppio clic la soluzione **ProductsPortal** (assicurarsi di fare doppio clic soluzione, non il progetto). Fare clic su **Aggiungi**, quindi fare clic su **Progetto esistente**.

8.  Passare al progetto **ProductsServer** e quindi fare doppio clic sul file di soluzione **ProductsServer.csproj** per aggiungerlo.

9.  **ProductsServer** deve essere in esecuzione per visualizzare i dati in **ProductsPortal**. In Esplora soluzioni fare doppio clic soluzione **ProductsPortal** e fare clic su **proprietà**. Viene visualizzata la finestra di dialogo **Pagine delle proprietà** .

10. Sul lato sinistro fare clic su **Progetto di avvio**. Sul lato destro fare clic su **più progetti di avvio**. Assicurarsi che **ProductsServer** **ProductsPortal** vengono visualizzati e, in tale ordine con **avviare** impostare come azione per entrambi.

      ![][25]

11. Sempre nella finestra di dialogo **proprietà** fare clic su **Dipendenze progetto** sul lato sinistro.

12. Nell'elenco di **progetti** , fare clic su **ProductsServer**. Assicurarsi che sia **ProductsPortal** **non** è selezionata.

14. Nell'elenco di **progetti** , fare clic su **ProductsPortal**. Verificare che sia selezionato **ProductsServer** . 

    ![][26]

15. Fare clic su **OK** nella finestra di dialogo **Pagine delle proprietà** .

## <a name="run-the-project-locally"></a>Eseguire il progetto in locale

Per testare l'applicazione in locale, in Visual Studio premere **F5**. Il server locale (**ProductsServer**) dovrebbe iniziare prima e quindi l'applicazione **ProductsPortal** dovrebbe iniziare in una finestra del browser. Questa volta, si noterà che l'inventario dei prodotti sono elencati i dati recuperati dal sistema locale di servizio prodotto.

![][10]

Premere **aggiornare** la pagina **ProductsPortal** . Ogni volta che si aggiorna la pagina, verrà visualizzata l'app di server visualizzare un messaggio quando `GetProducts()` da **ProductsServer** chiamato.

Chiudere entrambe le applicazioni prima di procedere con il passaggio successivo.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Distribuire il progetto ProductsPortal in un'app web Azure

Il passaggio successivo consiste nel convertire front-end **ProductsPortal** in un'app web Azure. Prima di tutto, distribuire il progetto **ProductsPortal** , eseguire tutti la procedura descritta nella sezione [Distribuisci project web App web Azure](../app-service-web/web-sites-dotnet-get-started.md#deploy-the-web-project-to-the-azure-web-app). Una volta completata la distribuzione, tornare in questa esercitazione e procedere con il passaggio successivo.

> [AZURE.NOTE] È possibile visualizzato un messaggio di errore nella finestra del browser quando il progetto web **ProductsPortal** venga avviato automaticamente dopo la distribuzione. È previsto e si verifica perché l'applicazione **ProductsServer** non è ancora in esecuzione.

Copiare l'URL dell'app web distribuito come sarà necessario l'URL nel passaggio successivo. È inoltre possibile ottenere questo URL nella finestra di attività del servizio di Azure App in Visual Studio:

![][9] 

### <a name="set-productsportal-as-web-app"></a>Impostare ProductsPortal come web app

Prima di eseguire l'applicazione nel cloud, è necessario assicurarsi che **ProductsPortal** viene avviato dall'interno Visual Studio come un'app web.

1. In Visual Studio, fare clic sul progetto **ProjectsPortal** e quindi fare clic su **proprietà**.

3. Nella colonna sinistra, fare clic su **Web**.

5. Nella sezione **Azione di avvio** , fare clic sul pulsante **Avvia URL** e nella casella di testo immettere l'URL per un'app web distribuite in precedenza; ad esempio `http://productsportal1234567890.azurewebsites.net/`.

    ![][27]

6. Dal menu **File** in Visual Studio, fare clic su **Salva tutto**.

7. Dal menu di compilazione in Visual Studio, fare clic su **Rigenera soluzione**.

## <a name="run-the-application"></a>Eseguire l'applicazione

2.  Premere F5 per creare ed eseguire l'applicazione. Il server locale (l'applicazione console **ProductsServer** ) dovrebbe iniziare prima e quindi l'applicazione **ProductsPortal** dovrebbe iniziare in una finestra del browser, come illustrato nella schermata seguente. Si noti nuovamente l'inventario dei prodotti sono elencati i dati recuperati dal sistema locale di servizio prodotto che consente di visualizzare i dati in web app. Controllare l'URL per assicurarsi che **ProductsPortal** sia in esecuzione nel cloud, come un'app web Azure. 

    ![][1]

    > [AZURE.IMPORTANT] L'applicazione di console **ProductsServer** deve essere in esecuzione e in grado di fornire i dati all'applicazione **ProductsPortal** . Se il browser viene visualizzato un messaggio di errore, attendere alcuni secondi più **ProductsServer** caricare e visualizzare il messaggio seguente. Premere **Aggiorna** nel browser.

    ![][37]

3. Indietro nel browser, premere **aggiornare** la pagina **ProductsPortal** . Ogni volta che si aggiorna la pagina, verrà visualizzata l'app di server visualizzare un messaggio quando `GetProducts()` da **ProductsServer** chiamato.

    ![][38]

## <a name="next-steps"></a>Passaggi successivi  

Per ulteriori informazioni su Bus di servizio, vedere le risorse seguenti:  

* [Bus di servizio Azure][sbwacom]  
* [Come usare servizio Bus code][sbwacomqhowto]  


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Ottenere gli strumenti e SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
  [19]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-6.png
  [9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png
  
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png


  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

