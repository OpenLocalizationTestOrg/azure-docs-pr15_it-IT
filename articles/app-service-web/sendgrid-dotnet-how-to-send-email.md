<properties 
    pageTitle="Come utilizzare il servizio di posta elettronica SendGrid (.NET) | Microsoft Azure" 
    description="Informazioni su come inviare posta elettronica con il servizio di posta elettronica SendGrid in Azure. Utilizzare l'API .NET scritti in c# esempi di codice." 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="thinkingserious" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="01/14/2016" 
    ms.author="team-pi@sendgrid.com"/>





# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Come inviare posta elettronica con SendGrid con Azure


## <a name="overview"></a>Panoramica

Questa guida viene illustrato come eseguire attività comuni di programmazione con il servizio di posta elettronica SendGrid in Azure. Gli esempi sono scritti c\#
e utilizzare l'API di .NET. Gli scenari coperti includono **la costruzione di posta elettronica**, **invio di posta elettronica**, **aggiungere allegati**e **utilizzo dei filtri**. Per ulteriori informazioni su SendGrid e invio di posta elettronica, vedere la sezione [passaggi successivi][] .

## <a name="what-is-the-sendgrid-email-service"></a>Che cos'è il servizio di posta elettronica SendGrid?

SendGrid è un [servizio di posta elettronica basato su cloud] che offre affidabile [recapito della posta elettronica transazione], scalabilità e in tempo reale analitica insieme API flessibile che semplificano l'integrazione personalizzata. Scenari di utilizzo SendGrid comuni includono:

-   Inviare automaticamente conferme ai clienti.
-   Amministrazione di distribuzione sono elencati per l'invio di clienti e-volantini mensile e offerte speciali.
-   Raccolta metriche in tempo reale per le operazioni come la posta elettronica bloccato e tempi di risposta del cliente.
-   Creazione di report per identificare le tendenze.
-   Inoltro di richieste di assistenza clienti.
-   L'elaborazione di messaggi di posta elettronica in arrivo.

Per ulteriori informazioni, vedere [https://sendgrid.com](https://sendgrid.com) o la [libreria c#][sendgrid csharp]

## <a name="create-a-sendgrid-account"></a>Creare un account SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Fare riferimento alla libreria di classe .NET SendGrid

Il [pacchetto SendGrid NuGet](https://www.nuget.org/packages/Sendgrid) è il modo più semplice per ottenere l'API SendGrid e per configurare l'applicazione con tutte le dipendenze. NuGet è un'estensione di Visual Studio inclusa in Microsoft Visual Studio 2015 che semplifica l'individuazione installare e aggiornare le raccolte e gli strumenti. 

> [AZURE.NOTE] Per installare NuGet se si esegue una versione precedente a Visual Studio 2015 di Visual Studio, visitare [http://www.nuget.org](http://www.nuget.org)e fare clic sul pulsante **Installa NuGet** .

Per installare il pacchetto SendGrid NuGet nell'applicazione, eseguire le operazioni seguenti:

1.  Creare un nuovo progetto.

    ![Creare un nuovo progetto][create-new-project]

2.  Selezionare un modello.

    ![Selezionare un modello][select-a-template]

3.  In **Esplora soluzioni**rapida **riferimenti**e quindi fare clic su **Gestisci pacchetti NuGet**.

4.  Cercare **SendGrid** e selezionare l'elemento **SendGrid** nell'elenco dei risultati.

    ![Pacchetto SendGrid NuGet][SendGrid-NuGet-package]

5.  Fare clic su **Installa** per completare l'installazione e quindi chiudere la finestra di dialogo.

Libreria di classi .NET del SendGrid rappresenta la cosiddetta **SendGridMail**. Contiene spazi dei nomi seguenti:

-   **SendGridMail** per la creazione e l'uso con gli elementi di posta elettronica.
-   **SendGridMail.Transport** per l'invio di posta elettronica mediante il protocollo **SMTP** o il protocollo HTTP 1.1 con **Web/resto**.

Aggiungere le seguenti dichiarazioni di spazio dei nomi di codice nella parte superiore di qualsiasi C\# file in cui si desidera accedere a livello del servizio di posta elettronica SendGrid.
**System.Net** e **System.Net.Mail** sono spazi dei nomi di .NET Framework sono incluse perché includono tipi in genere si utilizzano con APIs SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="how-to-create-an-email"></a>Procedura: creare un messaggio di posta elettronica

L'oggetto **SendGridMessage** per creare un messaggio di posta elettronica. Dopo aver creato l'oggetto del messaggio, è possibile impostare i metodi e proprietà, inclusi il mittente di posta elettronica, il destinatario di posta elettronica e l'oggetto e corpo del messaggio di posta elettronica.

Nell'esempio seguente viene illustrato come creare un oggetto di posta elettronica completamente compilato:

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();

    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");

    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };

    myMessage.AddTo(recipients);

    myMessage.Subject = "Testing the SendGrid Library";

    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

Per ulteriori informazioni su tutte le proprietà e metodi supportati dal tipo di **SendGrid** , vedere [sendgrid csharp][] su GitHub.

## <a name="how-to-send-an-email"></a>Procedura: inviare un messaggio di posta elettronica

Dopo aver creato un messaggio di posta elettronica, è possibile inviarlo tramite l'API Web forniti da SendGrid. In alternativa, è possibile usare [. RETE integrato in una raccolta](https://sendgrid.com/docs/Code_Examples/csharp.html).

Invio di posta elettronica, è necessario fornire le credenziali dell'account SendGrid (nome utente e password) o chiave dell'API SendGrid. API chiave è il metodo preferito. Se sono necessarie informazioni dettagliate su come configurare i tasti di API, visitare la [documentazione](https://sendgrid.com/docs/Classroom/Send/api_keys.html)

È possibile archiviare le credenziali tramite il portale di Azure facendo clic su Configura e aggiungono coppie chiave/valore in "impostazioni app".

 ![Impostazioni dell'app Azure][azure_app_settings]

 Quindi, è possibile accedervi come indicato di seguito: 
    
    var username = System.Environment.GetEnvironmentVariable("SENDGRID_USERNAME"); 
    var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASSWORD");
    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");

Usando le credenziali:
    
    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);
    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

Uso dell'API chiave:

    var apiKey = "your_sendgrid_api_key";  
    // create a Web transport, using API Key
    var transportWeb = new Web(apiKey);


Negli esempi seguenti viene illustrato come inviare un messaggio usando l'API Web.

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

    // Send the email, which returns an awaitable task.
    transportWeb.DeliverAsync(myMessage);

    // If developing a Console Application, use the following
    // transportWeb.DeliverAsync(mail).Wait();

## <a name="how-to-add-an-attachment"></a>Procedura: aggiungere un allegato

Chiamando il metodo **AddAttachment** e specificare il nome e il percorso del file da allegare, è possano aggiungere allegati a un messaggio.
È possibile includere più allegati da questo metodo una volta per ogni file che si desidera allegare. Nell'esempio seguente viene illustrato come aggiungere un allegato a un messaggio:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
È anche possibile aggiungere allegati da dei dati **flusso**. Possono essere eseguita tramite il metodo stesso come sopra **AddAttachment**ma passando il flusso dei dati e il nome del file che indichi come il messaggio desiderato. In questo caso è necessario aggiungere la libreria di System.IO.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


## <a name="how-to-use-apps-to-enable-footers-tracking-and-analytics"></a>Procedura: utilizzare App per attivare analitica, verifica e piè di pagina

SendGrid offre funzionalità di posta elettronica aggiuntivi mediante l'utilizzo di App. Queste sono impostazioni che possono essere aggiunti a un messaggio di posta elettronica per attivare la funzionalità specifica, ad esempio fare clic su verifica, Google analitica, abbonamento verifica, e così via. Per un elenco completo delle App, vedere [Le impostazioni dell'App][].

App possono essere applicate ai messaggi di posta elettronica **SendGrid** metodi implementata come parte della classe **SendGrid** .

Negli esempi seguenti viene illustrato il piè di pagina e fare clic su Verifica filtri:

### <a name="footer"></a>Piè di pagina

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### <a name="click-tracking"></a>Fare clic su verifica

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Procedura: utilizzare i servizi SendGrid aggiuntivi

SendGrid offre API basata sul web e webhooks che consente di sfruttare le funzionalità SendGrid aggiuntiva dall'applicazione di Azure. Per informazioni dettagliate, vedere la [documentazione API SendGrid][].

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base del servizio di posta elettronica SendGrid, seguire questi collegamenti per altre informazioni.

*   C SendGrid\# repo libreria: [csharp sendgrid][]
*   La documentazione dell'API SendGrid: <https://sendgrid.com/docs>
*   SendGrid offerta speciale per i clienti di Azure: [https://sendgrid.com](https://sendgrid.com)

  [Passaggi successivi]: #next-steps
  [What is the SendGrid Email Service?]: #whatis
  [Create a SendGrid Account]: #createaccount
  [Reference the SendGrid .NET Class Library]: #reference
  [How to: Create an Email]: #createemail
  [How to: Send an Email]: #sendemail
  [How to: Add an Attachment]: #addattachment
  [How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
  [How to: Use Additional SendGrid Services]: #useservices
  
  [special offer]: https://www.sendgrid.com/windowsazure.html
  
  [create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/create_new_project.png
  [select-a-template]: ./media/sendgrid-dotnet-how-to-send-email/select_a_template.png
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid_nuget.png
  [azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/app_settings.png
  [sendgrid csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [Impostazioni dell'App]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [Documentazione dell'API SendGrid]: https://sendgrid.com/docs
  
  [servizio di posta elettronica basato sul cloud]: https://sendgrid.com/email-solutions
  [recapito della posta elettronica transazione]: https://sendgrid.com/transactional-email
 
