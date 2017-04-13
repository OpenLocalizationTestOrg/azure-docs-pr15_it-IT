<properties 
    pageTitle="Come effettuare una chiamata telefonica da Twilio (Java) | Microsoft Azure" 
    description="Informazioni su come effettuare una chiamata telefonica da una pagina web utilizzando Twilio in un'applicazione Java in Azure." 
    services="" 
    documentationCenter="java" 
    authors="devinrader" 
    manager="twilio" 
    editor="mollybos"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="microsofthelp@twilio.com"/>

# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Come effettuare una chiamata telefonica utilizzando Twilio in un'applicazione Java su Azure 

Nell'esempio seguente mostra come è possibile utilizzare Twilio per effettuare una chiamata da una pagina web ospitato in Azure. L'applicazione risultante richiederà l'utente per i valori telefonata, come illustrato nella schermata seguente.

![Modulo di Azure chiamata tramite Twilio e Java][twilio_java]

È necessario eseguire le operazioni seguenti per utilizzare il codice in questo argomento:

1. Acquisire un account Twilio e l'autenticazione token. Per iniziare a utilizzare Twilio, valutare prezzi in [http://www.twilio.com/pricing][twilio_pricing]. È possibile iscriversi al [https://www.twilio.com/try-twilio][try_twilio]. Per informazioni sull'API fornito da Twilio, vedere [http://www.twilio.com/api][twilio_api].
2. Ottenere Twilio VASO. In [https://github.com/twilio/twilio-java][twilio_java_github], è possibile scaricare le origini GitHub e creare il proprio VASO o scaricare un VASO predefinito (con o senza dipendenze).
Il codice in questo argomento è stato scritto utilizzando VASO TwilioJava 3.3.8 con relazioni predefinita.
3. Aggiungere il percorso di compilazione di linguaggio VASO.
4. Se si utilizza Eclisse per creare l'applicazione di linguaggio, includere il Twilio JAR nel file di distribuzione dell'applicazione (WAR) funzionalità del Eclisse distribuzione assembly. Se non si usa Eclisse per creare l'applicazione di linguaggio, assicurarsi che la Twilio JAR inclusi nello stesso ruolo Azure di applicazione Java e aggiunto al percorso della classe dell'applicazione.
5. Assicurarsi che il keystore cacerts contiene il certificato di autorità di certificazione sicura Equifax con MD5 ID digitale 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (il numero seriale 35:DE:F4:CF e l'ID digitale SHA1 D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Questo è il certificato di certificazione (CA) certificato per [https://api.twilio.com] [ twilio_api_service] servizio, ossia quando si utilizza Twilio APIs. Per informazioni sull'aggiunta di questo certificato all'archivio cacert del JDK, vedere [aggiunta di un certificato per l'archivio certificati di linguaggio CA][add_ca_cert].

Inoltre, avere familiarità con le informazioni di [creazione di un Hello World applicazione mediante il Toolkit di Azure per Eclisse][azure_java_eclipse_hello_world], o con altre tecniche per l'hosting di applicazioni Java in Azure se non si usa Eclisse, è consigliabile.

## <a name="create-a-web-form-for-making-a-call"></a>Creare una maschera web per effettuare una chiamata

Il codice seguente viene illustrato come creare una maschera web per recuperare i dati utente per effettuare una chiamata. Ai fini di questo esempio, è stato creato un nuovo progetto web dynamic, denominato **TwilioCloud**e **callform.jsp** è stato aggiunto come un file JSP.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Creare il codice per effettuare la chiamata
Il codice seguente, che viene chiamato quando l'utente completa il modulo visualizzato dal callform.jsp, crea il messaggio di chiamata e genera la chiamata. Ai fini di questo esempio, il file JSP è denominato **makecall.jsp** ed è stata aggiunta al progetto **TwilioCloud** . (Utilizzare l'account Twilio e l'autenticazione token anziché i valori di segnaposto assegnati a **accountSID** e **authToken** il codice riportato di seguito).

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";
     
         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");
     
         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");
    
         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");
     
         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");
     
         // Create a URL using the Twilio message and the user-entered text.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;
     
         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");
    
         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);
     
         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Oltre a effettuare la chiamata, makecall.jsp consente di visualizzare l'endpoint Twilio, versione API e lo stato di chiamata. Un esempio è l'immagine riportata di seguito:

![Risposta chiamata Azure utilizzando Twilio e Java][twilio_java_response]

## <a name="run-the-application"></a>Eseguire l'applicazione
Ecco i passaggi necessari per eseguire l'applicazione. Dettagli per questa procedura, vedere [creazione di un Hello World applicazione mediante il Toolkit di Azure per Eclisse][azure_java_eclipse_hello_world].

1. Esportare il WAR TwilioCloud nella cartella di Azure **approot** . 
2. Modificare **startup.cmd** per decomprimere il WAR TwilioCloud.
3. Compilare l'applicazione per l'emulatore di calcolo.
4. Avviare la distribuzione nell'emulatore di calcolo.
5. Aprire un browser ed eseguire **http://localhost:8080/TwilioCloud/callform.jsp**.
6. Immettere i valori nel modulo, fare clic su **effettuare la chiamata**e quindi visualizzare i risultati in makecall.jsp.

Quando si è pronti distribuire Azure, ricompilare per la distribuzione nel cloud, la distribuzione di Azure ed eseguire http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp nel browser (sostituire il valore per *your_hosted_name*).

## <a name="next-steps"></a>Passaggi successivi
Questo codice è stato fornito per indicare la funzionalità di base usando Twilio Java in Azure. Prima di distribuire in Azure in produzione, è consigliabile aggiungere ulteriori la gestione degli errori o altre caratteristiche. Per esempio:

* Invece di usare una maschera web, è possibile utilizzare BLOB Azure dello spazio di archiviazione o Database SQL per archiviare i numeri di telefono e il testo di chiamata. Per informazioni sull'utilizzo di BLOB Azure dello spazio di archiviazione in linguaggio, vedere [come utilizzare il servizio di archiviazione Blob da Java][howto_blob_storage_java]. Per informazioni sull'uso di Database SQL di linguaggio, vedere [Utilizzo di Database di SQL in linguaggio][howto_sql_azure_java].
* È possibile utilizzare **RoleEnvironment.getConfigurationSettings** per recuperare l'ID account Twilio e token di autenticazione dalle impostazioni di configurazione della distribuzione, anziché i valori in makecall.jsp a livello di codice. Per informazioni sulla classe **RoleEnvironment** , vedere [utilizzo della libreria di Runtime del servizio di Azure in JSP] [ azure_runtime_jsp] e documentazione del pacchetto di Runtime del servizio di Azure [http://dl.windowsazure.com/javadoc][azure_javadoc].
* Il codice makecall.jsp assegna un URL forniti Twilio, [http://twimlets.com/message][twimlet_message_url], per la variabile **Url** . Questo URL fornisce una risposta Twilio Markup Language (TwiML) che informa Twilio come procedere con la chiamata. Ad esempio, può contenere TwiML che viene restituito un ** &lt;pronunciare&gt; ** verbo di testo che viene pronunciato al destinatario di chiamata. Invece di usare l'URL forniti Twilio, è possibile creare il proprio servizio per rispondere alla richiesta di Twilio; Per ulteriori informazioni, vedere [come utilizzare Twilio per vocali e le funzionalità di SMS in linguaggio][howto_twilio_voice_sms_java]. Sono disponibili ulteriori informazioni su TwiML in [http://www.twilio.com/docs/api/twiml][twiml]e altre informazioni sul ** &lt;pronunciare&gt; ** e altri comandi Twilio sono disponibili in [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Leggere le indicazioni sulla protezione Twilio in [https://www.twilio.com/docs/security][twilio_docs_security].

Per ulteriori informazioni su Twilio, vedere [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Vedere anche
* [Come usare Twilio per vocali e le funzionalità di SMS in Java][howto_twilio_voice_sms_java]
* [Aggiunta di un certificato nell'archivio certificati CA Java][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: http://msdn.microsoft.com/library/windowsazure/hh690944.aspx
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: http://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: http://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
