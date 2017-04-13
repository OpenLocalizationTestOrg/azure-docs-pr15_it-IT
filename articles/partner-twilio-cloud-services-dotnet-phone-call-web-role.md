<properties 
    pageTitle="Come effettuare una chiamata telefonica da Twilio (.NET) | Microsoft Azure" 
    description="Informazioni su come effettuare una chiamata telefonica e inviare un messaggio SMS con il servizio Twilio API in Azure. Esempi di codice scritti in .NET." 
    services="" 
    documentationCenter=".net" 
    authors="devinrader" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="05/04/2016" 
    ms.author="microsofthelp@twilio.com"/>




# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Come effettuare una chiamata telefonica usando Twilio in un ruolo web su Azure

Questa guida viene illustrato come utilizzare Twilio per effettuare una chiamata da una pagina web ospitato in Azure. L'applicazione risultante richiede all'utente per i valori telefonata, come illustrato nella schermata seguente.

![Modulo di Azure chiamata utilizzando Twilio e ASP.NET][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Prerequisiti

È necessario eseguire le operazioni seguenti per utilizzare il codice in questo argomento:

1. Acquisire un account Twilio e l'autenticazione token. Per iniziare a utilizzare Twilio, effettuare l'iscrizione al [https://www.twilio.com/try-twilio][try_twilio]. È possibile valutare prezzi in [http://www.twilio.com/pricing][twilio_pricing]. Per informazioni sull'API fornito da Twilio, vedere [http://www.twilio.com/voice/api][twilio_api].
2. Aggiungere il proprio ruolo web raccolta .NET Twilio. Vedere "per aggiungere raccolte Twilio al progetto di ruolo web" più avanti in questo argomento.

È necessario avere familiarità con la creazione di un ruolo web di base su Azure.

## <a name="howtocreateform"></a>Procedura: creare una maschera web per effettuare una chiamata

<a id="use_nuget"></a>Per aggiungere raccolte Twilio al progetto ruolo web:

1.  Aprire la soluzione in Visual Studio.
2.  Fare clic su **riferimenti**.
3.  Fare clic su **Gestisci NuGet pacchetti**.
4.  Fare clic su **Online**.
5.  Nella finestra di ricerca online, digitare *twilio*.
6.  Fare clic su **Installa** il pacchetto Twilio.

Il codice seguente viene illustrato come creare una maschera web per recuperare i dati utente per effettuare una chiamata. In questo esempio viene creato un ruolo web ASP.NET denominato **TwilioCloud** .

    <%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
        AutoEventWireup="true" CodeBehind="Default.aspx.cs"
        Inherits="WebRole1._Default" %>

    <asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
    </asp:Content>
    <asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
        <div>
            <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
            </asp:BulletedList>
        </div>
        <div>
            <p>Fill in all fields and click <b>Make this call</b>.</p>
            <div>
                To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
                Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
                <asp:Button ID="callpage" runat="server" Text="Make this call"
                    onclick="callpage_Click" />
            </div>
        </div>
    </asp:Content>

## <a id="howtocreatecode"></a>Procedura: creare il codice per effettuare la chiamata
Il codice seguente, che viene chiamato quando l'utente completa il modulo, crea il messaggio di chiamata e genera la chiamata. In questo esempio, il codice viene eseguito nel gestore eventi onclick del pulsante nella maschera. (Utilizzare l'account Twilio e l'autenticazione token anziché i valori di segnaposto assegnati a **accountSID** e **authToken** il codice riportato di seguito).

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;
    using Twilio;

    namespace WebRole1
    {
        public partial class _Default : System.Web.UI.Page
        {
            protected void Page_Load(object sender, EventArgs e)
            {

            }

            protected void callpage_Click(object sender, EventArgs e)
            {
                // Call porcessing happens here.

                // Use your account SID and authentication token instead of
                // the placeholders shown here.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Instantiate an instance of the Twilio client.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Retrieve the account, used later to retrieve the
                Twilio.Account account = client.GetAccount();
                string APIversuion = client.ApiVersion;
                string TwilioBaseURL = client.BaseUrl;

                this.varDisplay.Items.Clear();
                if (this.toNumber.Text == "" || this.message.Text == "")
                {
                    this.varDisplay.Items.Add(
                            "You must enter a phone number and a message.");
                }
                else
                {
                    // Retrieve the values entered by the user.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Create a URL using the Twilio message and the user-entered
                    // text. You must replace spaces in the user's text with '%20'
                    // to make the text suitable for a URL.
                    String Url = "http://twimlets.com/message?Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Display the endpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Twilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values.                    
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Place the call.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

La chiamata e vengono visualizzati l'endpoint Twilio, versione API e lo stato di chiamata. Schermata seguente è illustrato un esempio di esecuzione l'output.

![Risposta chiamata Azure utilizzando Twilio e ASP.NET][twilio_dotnet_basic_form_output]

Sono disponibili ulteriori informazioni su TwiML in [http://www.twilio.com/docs/api/twiml][twiml]. Ulteriori informazioni su &lt;pronunciare&gt; e altri comandi Twilio sono disponibili in [http://www.twilio.com/docs/api/twiml/say][twilio_say].

## <a id="nextsteps"></a>Passaggi successivi
Questo codice è stato fornito per indicare la funzionalità di base usando Twilio in un ruolo web ASP.NET in Azure. Prima di distribuire in Azure in produzione, è consigliabile aggiungere ulteriori la gestione degli errori o altre caratteristiche. Per esempio:

* Invece di usare una maschera web, è possibile utilizzare lo spazio di archiviazione Blob Azure o un'istanza di Database SQL Azure per archiviare i numeri di telefono e il testo di chiamata. Per informazioni sull'utilizzo di BLOB Azure, vedere [come utilizzare il servizio di archiviazione Blob Azure .NET][howto_blob_storage_dotnet]. Per informazioni sull'uso di Database SQL, vedere [come utilizzare il Database di SQL Azure nelle applicazioni .NET][howto_sql_azure_dotnet].
* È possibile utilizzare RoleEnvironment.getConfigurationSettings per recuperare l'ID account Twilio e token di autenticazione dalle impostazioni di configurazione della distribuzione, anziché i valori in una maschera a livello di codice. Per informazioni sulla classe RoleEnvironment, vedere [Namespace Microsoft.WindowsAzure.ServiceRuntime][azure_runtime_ref_dotnet].
* Leggere le indicazioni sulla protezione Twilio in [https://www.twilio.com/docs/security][twilio_docs_security].
* Ulteriori informazioni su Twilio [all'https://www.twilio.com/docs][twilio_docs].

##<a name="seealso"></a>Vedere anche
* [Come usare Twilio per vocali e le funzionalità di SMS da Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: http://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
