<properties 
    pageTitle="Creare un'app web in Azure che si connette a MongoDB installato in un computer virtuale" 
    description="Un'esercitazione che illustra come utilizzare fra per distribuire un'app ASP.NET al servizio di App Azure, connessa a MongoDB una macchina virtuale Azure."
    tags="azure-portal" 
    services="app-service\web, virtual-machines" 
    documentationCenter=".net" 
    authors="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="02/29/2016" 
    ms.author="cephalin"/>


# <a name="create-a-web-app-in-azure-that-connects-to-mongodb-running-on-a-virtual-machine"></a>Creare un'app web in Azure che si connette a MongoDB installato in un computer virtuale

Usa fra, è possibile distribuire un'applicazione ASP.NET per Azure App servizio Web Apps. In questa esercitazione si creerà un semplice MVC ASP.NET front-end applicazione di elenco di attività che si connette a un database MongoDB installato in un computer virtuale in Azure.  [MongoDB] [ MongoDB] è un database NoSQL prestazioni popolari Apri origine. Dopo l'esecuzione e testare l'applicazione ASP.NET nel computer di sviluppo, si verrà caricare un'applicazione di App del servizio Web App tramite operazioni.

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.


## <a name="background-knowledge"></a>Conoscenze ##

Conoscenza delle operazioni seguenti è utile per questa esercitazione, anche se non è necessario:

* C# driver per MongoDB. Per ulteriori informazioni sullo sviluppo di applicazioni c# contro MongoDB, vedere MongoDB [CSharp lingua centro][MongoC#LangCenter]. 
* Framework di applicazione web ASP.NET. È possibile tutte le informazioni su [sito Web ASP.net][ASP.NET].
* Framework di applicazione web ASP .NET MVC. È possibile tutte le informazioni su [sito Web ASP.NET MVC][MVCWebSite].
* Azure. Per iniziare la lettura di [Azure][WindowsAzure].

## <a name="prerequisites"></a>Prerequisiti ##

- [Visual Studio Express 2013 per Web]  [ VSEWeb] o [Visual Studio 2013] [VSUlt]
- [Azure SDK per .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)
- Un abbonamento attivo a Microsoft Azure

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<a id="virtualmachine"></a> 
## <a name="create-a-virtual-machine-and-install-mongodb"></a>Creare una macchina virtuale e installare MongoDB ##

In questa esercitazione si presuppone che una macchina virtuale è stato creato in Azure. Dopo aver creato la macchina virtuale è necessario installare MongoDB sul computer virtuale:

* Per creare una macchina virtuale di Windows e installare MongoDB, vedere [Installare MongoDB in un computer virtuale che eseguono Windows Server in Azure][InstallMongoOnWindowsVM].


Dopo aver creato la macchina virtuale in Azure e installato MongoDB, è importante ricordare il nome DNS del computer virtuale ("testlinuxvm.cloudapp.net", ad esempio) e la porta esterna per MongoDB specificato nell'endpoint.  Sarà necessario queste informazioni in un secondo momento nell'esercitazione.

<a id="createapp"></a>
## <a name="create-the-application"></a>Creare l'applicazione ##

In questa sezione verranno creare un'applicazione ASP.NET denominata "Elenco attività personali" utilizzando Visual Studio e verrà eseguita una distribuzione iniziale a Azure App servizio Web Apps. Si eseguirà l'applicazione in locale, ma verrà connettere la macchina virtuale in Azure e usare l'istanza MongoDB creato disponibili.

1. In Visual Studio, fare clic su **Nuovo progetto**.

    ![Inizio pagina nuovo progetto][StartPageNewProject]

1. Nella finestra **Nuovo progetto** , nel riquadro sinistro selezionare **Visual c#**e quindi selezionare **Web**. Nel riquadro centrale selezionare **Applicazione Web ASP.NET**. Nella parte inferiore, assegnare un nome al progetto "MyTaskListApp" e quindi fare clic su **OK**.

    ![Finestra di dialogo Nuovo progetto][NewProjectMyTaskListApp]

1. Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare **MVC**e quindi fare clic su **OK**.

    ![Selezionare il modello MVC][VS2013SelectMVCTemplate]

1. Se non è già stato effettuato l'accesso in Microsoft Azure, verrà richiesto di accedere. Seguire le istruzioni per accedere a Azure.
2. Una volta è effettuato l'accesso, è possibile iniziare la configurazione di un'app web di servizio di App. Specificare il **nome Web App**, **piano di servizio App**, **gruppo di risorse**e **Opzioni internazionali**e quindi fare clic su **Crea**.

    ![](./media/web-sites-dotnet-store-data-mongodb-vm/VSConfigureWebAppSettings.png)

1. Dopo il progetto completamento della creazione, attendere che l'applicazione web da creare nel servizio App Azure come indicato nella finestra di **Azure App servizio attività** . Quindi fare clic su **Pubblica MyTaskListApp per ora questa App Web**.

1. Fare clic su **pubblica**.

    ![](./media/web-sites-dotnet-store-data-mongodb-vm/VSPublishWeb.png)

    Dopo l'applicazione di ASP.NET predefinita viene pubblicato in Azure App servizio Web Apps, verrà avviato nel browser.

## <a name="install-the-mongodb-c-driver"></a>Installare il driver MongoDB c#

MongoDB offre supporto lato client per le applicazioni c# tramite un driver, è necessario installare nel computer di sviluppo locale. Il driver c# è disponibile tramite NuGet.

Per installare il driver MongoDB c#:

1. In **Esplora soluzioni**fare clic sul progetto **MyTaskListApp** e selezionare **Gestione NuGetPackages**.

    ![Gestire pacchetti NuGet][VS2013ManageNuGetPackages]

2. Nella finestra **Gestisci pacchetti NuGet** nel riquadro sinistro fare clic su **Online**. Nella casella di **Ricerca Online** a destra, digitare "mongodb.driver".  Fare clic su **Installa** per installare il driver.

    ![Cercare MongoDB c# Driver][SearchforMongoDBCSharpDriver]

3. Fare clic sui **accetto** per accettare le condizioni di licenza Inc 10gen.

4. Dopo che il driver è installato, fare clic su **Chiudi** .
    ![MongoDB c# Driver installato][MongoDBCsharpDriverInstalled]


Il driver MongoDB c# è stato installato.  Riferimenti alle librerie di **MongoDB.Bson**, **MongoDB.Driver**e **MongoDB.Driver.Core** sono stati aggiunti al progetto.

![Riferimenti MongoDB c# Driver][MongoDBCSharpDriverReferences]

## <a name="add-a-model"></a>Aggiungere un modello ##
In **Esplora soluzioni**rapida della cartella di *modelli* e **aggiungere** una nuova **classe** e denominarla *TaskModel.cs*.  In *TaskModel.cs*, sostituire il codice esistente con il codice seguente:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MongoDB.Bson.Serialization.Attributes;
    using MongoDB.Bson.Serialization.IdGenerators;
    using MongoDB.Bson;
    
    namespace MyTaskListApp.Models
    {
        public class MyTask
        {
            [BsonId(IdGenerator = typeof(CombGuidGenerator))]
            public Guid Id { get; set; }
    
            [BsonElement("Name")]
            public string Name { get; set; }
    
            [BsonElement("Category")]
            public string Category { get; set; }
    
            [BsonElement("Date")]
            public DateTime Date { get; set; }
    
            [BsonElement("CreatedDate")]
            public DateTime CreatedDate { get; set; }
    
        }
    }

## <a name="add-the-data-access-layer"></a>Aggiungere il livello di accesso ai dati ##
In **Esplora soluzioni**fare clic *MyTaskListApp* progetto e **aggiungere** una **Nuova cartella** denominata *DAL*.  Fare clic sulla cartella *DAL* e **aggiungere** una nuova **classe**. Assegnare un nome file della classe *Dal.cs*.  In *Dal.cs*, sostituire il codice esistente con il codice seguente:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using MongoDB.Bson;
    using System.Configuration;
    
    
    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            private MongoServer mongoServer = null;
            private bool disposed = false;
    
            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net"
            private string connectionString = "mongodb://mongodbsrv20151211.cloudapp.net";
    
            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";
    
            // Default constructor.        
            public Dal()
            {
            }
    
            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    var collection = GetTasksCollection();
                    return collection.Find(new BsonDocument()).ToList();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask>();
                }
            }
    
            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                var collection = GetTasksCollectionForEdit();
                try
                {
                    collection.InsertOne(task);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }
    
            private IMongoCollection<MyTask> GetTasksCollection()
            {
                MongoClient client = new MongoClient(connectionString);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
    
            private IMongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoClient client = new MongoClient(connectionString);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
    
            # region IDisposable
    
            public void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
    
            protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                        if (mongoServer != null)
                        {
                            this.mongoServer.Disconnect();
                        }
                    }
                }
    
                this.disposed = true;
            }
    
            # endregion
        }
    }

## <a name="add-a-controller"></a>Aggiungere un controller ##
Aprire il file *Controllers \ HomeController.cs* in **Esplora soluzioni** e sostituire il codice esistente con le operazioni seguenti:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.Mvc;
    using MyTaskListApp.Models;
    using System.Configuration;
    
    namespace MyTaskListApp.Controllers
    {
        public class HomeController : Controller, IDisposable
        {
            private Dal dal = new Dal();
            private bool disposed = false;
            //
            // GET: /MyTask/
    
            public ActionResult Index()
            {
                return View(dal.GetAllTasks());
            }
    
            //
            // GET: /MyTask/Create
    
            public ActionResult Create()
            {
                return View();
            }
    
            //
            // POST: /MyTask/Create
    
            [HttpPost]
            public ActionResult Create(MyTask task)
            {
                try
                {
                    dal.CreateTask(task);
                    return RedirectToAction("Index");
                }
                catch
                {
                    return View();
                }
            }
    
            public ActionResult About()
            {
                return View();
            }
    
            # region IDisposable
    
            new protected void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
    
            new protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                        this.dal.Dispose();
                    }
                }
    
                this.disposed = true;
            }
    
            # endregion
    
        }
    }

## <a name="set-up-the-styles"></a>Impostare gli stili ##
Per modificare il titolo nella parte superiore della pagina, aprire la *Views\Shared\\cshtml* dei file in **Esplora soluzioni** e sostituire "Nome applicazione" nell'intestazione della barra di spostamento con "Applicazione elenco attività personale" in modo che avrà l'aspetto seguente:

    @Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

Per impostare il menu dell'elenco attività, aprire il file *\Views\Home\Index.cshtml* e sostituire il codice esistente con il codice seguente:
    
    @model IEnumerable<MyTaskListApp.Models.MyTask>
    
    @{
        ViewBag.Title = "My Task List";
    }
    
    <h2>My Task List</h2>
    
    <table border="1">
        <tr>
            <th>Task</th>
            <th>Category</th>
            <th>Date</th>
            
        </tr>
    
    @foreach (var item in Model) {
        <tr>
            <td>
                @Html.DisplayFor(modelItem => item.Name)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Category)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Date)
            </td>
            
        </tr>
    }
    
    </table>
    <div>  @Html.Partial("Create", new MyTaskListApp.Models.MyTask())</div>


Per aggiungere la possibilità di creare una nuova attività, pulsante destro del mouse la *Views\Home\\ * cartella e **aggiungere** una **visualizzazione**.  Nome della visualizzazione *Crea*. Sostituire il codice con le operazioni seguenti:

    @model MyTaskListApp.Models.MyTask
    
    <script src="@Url.Content("~/Scripts/jquery-1.10.2.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
    <script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
    
    @using (Html.BeginForm("Create", "Home")) {
        @Html.ValidationSummary(true)
        <fieldset>
            <legend>New Task</legend>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Name)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Name)
                @Html.ValidationMessageFor(model => model.Name)
            </div>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Category)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Category)
                @Html.ValidationMessageFor(model => model.Category)
            </div>
    
            <div class="editor-label">
                @Html.LabelFor(model => model.Date)
            </div>
            <div class="editor-field">
                @Html.EditorFor(model => model.Date)
                @Html.ValidationMessageFor(model => model.Date)
            </div>
    
            <p>
                <input type="submit" value="Create" />
            </p>
        </fieldset>
    }

**Esplora** dovrebbe risultare analoga alla seguente:

![Esplora soluzioni][SolutionExplorerMyTaskListApp]

## <a name="set-the-mongodb-connection-string"></a>Impostare la stringa di connessione MongoDB ##
In **Esplora soluzioni**, aprire il file *DAL/Dal.cs* . Trovare la riga di codice seguente:

    private string connectionString = "mongodb://<vm-dns-name>";

Sostituire `<vm-dns-name>` con il nome DNS in esecuzione MongoDB creato nel passaggio [creare una macchina virtuale e installare MongoDB][] di questa esercitazione.  Per trovare il nome DNS del computer virtuale, accedere al portale di Azure, selezionare **macchine virtuali**e trovare **Il nome DNS**.

Se il nome del DNS del computer virtuale è "testlinuxvm.cloudapp.net" e MongoDB è in attesa sulla porta predefinita 27017, la riga di stringa di connessione di codice si presenterà:

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

Se l'endpoint macchina virtuale specifica una porta esterna diversa per MongoDB, è possibile specificare la porta nella stringa di connessione:

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

Per ulteriori informazioni sulle stringhe di connessione MongoDB, vedere [connessioni][MongoConnectionStrings].

## <a name="test-the-local-deployment"></a>Verificare la distribuzione locale ##

Per eseguire l'applicazione del computer di sviluppo, selezionare **Avvia debug** dal menu **Debug** oppure premere **F5**. IIS Express viene avviato e un browser si apre e avvia home page dell'applicazione.  È possibile aggiungere una nuova attività, verrà aggiunti al database MongoDB in esecuzione nel computer virtuale in Azure.

![Applicazione elenco attività][TaskListAppBlank]

## <a name="publish-to-azure-app-service-web-apps"></a>Pubblicare su Azure App servizio Web App

In questa sezione verranno pubblicare le modifiche Azure App servizio Web Apps.

1. In Esplora soluzioni fare **MyTaskListApp** di nuovo e fare clic su **pubblica**.
2. Fare clic su **pubblica**.

    Verrà visualizzata un'app web in esecuzione in Azure App servizio e accesso al database MongoDB in macchine virtuali di Azure.

## <a name="summary"></a>Riepilogo ##

A questo punto sono state realizzate correttamente l'applicazione ASP.NET in Azure App servizio Web Apps. Per visualizzare l'applicazione web:

1. Accedere al portale Azure.
2. Fare clic su **App Web**. 
3. Selezionare un'applicazione web nell'elenco di **Applicazioni Web** .

Per ulteriori informazioni sullo sviluppo di applicazioni c# contro MongoDB, vedere [CSharp lingua Center][MongoC#LangCenter]. 

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 

<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnWindowsVM]: ../virtual-machines/virtual-machines-windows-classic-install-mongodb.md
[VSEWeb]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
[VSUlt]: http://www.microsoft.com/visualstudio/eng/2013-downloads

<!-- IMAGES -->


[StartPageNewProject]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
[NewProjectMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
[VS2013SelectMVCTemplate]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
[VS2013DefaultMVCApplication]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
[VS2013ManageNuGetPackages]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
[SearchforMongoDBCSharpDriver]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
[MongoDBCsharpDriverInstalled]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
[MongoDBCSharpDriverReferences]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
[SolutionExplorerMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
[TaskListAppBlank]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
[WAWSCreateWebSite]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
[WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
[Image9]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
[Image10]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
[Image11]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png

<!-- TOC BOOKMARKS -->
[Creare una macchina virtuale e installare MongoDB]: #virtualmachine
[Create and run the My Task List ASP.NET application on your development computer]: #createapp
[Create an Azure web site]: #createwebsite
[Deploy the ASP.NET application to the web site using Git]: #deployapp
 