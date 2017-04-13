<properties 
    pageTitle="Come usare Twilio per vocali e SMS (Java) | Microsoft Azure" 
    description="Informazioni su come effettuare una chiamata telefonica e inviare un messaggio SMS con il servizio Twilio API in Azure. Esempi di codice scritti in linguaggio." 
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

# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Come usare Twilio per vocali e le funzionalità di SMS in Java

Questa guida viene illustrato come eseguire attività comuni di programmazione con il servizio Twilio API in Azure. Gli scenari coperti includono una telefonata e invio di un messaggio breve messaggio SMS (Service). Per ulteriori informazioni sulla Twilio e l'utilizzo di voce e SMS nelle applicazioni, vedere la sezione [Passaggi successivi](#NextSteps) .

## <a id="WhatIs"></a>Che cos'è Twilio?
Twilio è un'API di servizio web telefonia che consente di utilizzare le lingue web esistenti e le competenze compilazione vocali e le applicazioni di SMS. Twilio è un servizio di terze parti (non una caratteristica di Azure e non un prodotto Microsoft).

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente alle applicazioni di effettuare e ricevere messaggi SMS. **Twilio Client** consente alle applicazioni di abilitare la comunicazione vocale utilizzando esistente connessioni Internet, incluse quelle per dispositivi mobili.

## <a id="Pricing"></a>Twilio prezzi e offerte speciali
Informazioni sui prezzi Twilio sono disponibile in [Prezzi Twilio] [twilio_pricing]. Clienti Azure ricevano un' [offerta speciale][special_offer]: una carta di credito gratuito di 1000 testi o 1000 Minute in ingresso. Per iscriversi a questa offerta o ottenere ulteriori informazioni, visitare [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Concetti
L'API Twilio è una API REST che fornisce vocali e le funzionalità SMS, Applications Edition. Librerie client sono disponibili in più lingue. per un elenco, vedere [Librerie API Twilio] [twilio_libraries].

Aspetti chiave dell'API Twilio sono verbi Twilio e Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Verbi Twilio
Utilizza l'API di Twilio verbi; ad esempio, il ** &lt;pronunciare&gt; ** verbo indica Twilio recapitare chiaramente un messaggio in una chiamata. 

Di seguito è un elenco di verbi Twilio.

* ** &lt;Connessione&gt;**: il chiamante si connette a un altro telefono.
* ** &lt;Raccogliere&gt;**: raccoglie cifre numeriche immesse della tastiera telefonica.
* ** &lt;Interruzione&gt;**: termina una chiamata.
* ** &lt;Riprodurre&gt;**: riproduce un file audio.
* ** &lt;Pausa&gt;**: che venga automaticamente per un determinato numero di secondi.
* ** &lt;Record&gt;**: vengono registrate vocale del chiamante e restituisce un URL di un file che contiene la registrazione.
* ** &lt;Reindirizzare&gt;**: trasferisce il controllo di una chiamata o SMS TwiML in un URL diverso.
* ** &lt;Rifiutare&gt;**: rifiuta una chiamata in arrivo per il numero di Twilio senza fatturazione è
* ** &lt;Pronunciare&gt;**: converte sintesi vocale che viene effettuata una chiamata.
* ** &lt;Sms&gt;**: invia un messaggio SMS.

### <a id="TwiML"></a>TwiML
TwiML è un set di istruzioni basato su XML in base ai verbi Twilio informano Twilio dell'elaborazione di una chiamata o SMS.

Se ad esempio TwiML seguenti da convertire il testo **Hello World** in sintesi e riconoscimento vocale.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Quando l'applicazione chiama l'API Twilio, uno dei parametri API è l'URL che restituisce la risposta TwiML. In fase di sviluppo, è possibile utilizzare gli URL forniti Twilio per fornire risposte TwiML utilizzate dalle applicazioni. È anche possibile ospitare il proprio URL per generare le risposte TwiML e un'altra opzione consiste nell'utilizzare l'oggetto **TwiMLResponse** .

Per ulteriori informazioni sulle Twilio verbi relativi attributi e TwiML, vedere [TwiML] [twiml]. Per ulteriori informazioni sulle API Twilio, vedere [API Twilio] [twilio_api].

## <a id="CreateAccount"></a>Creare un Account Twilio
Quando si è pronti per ottenere un account Twilio, iscriversi al [Provare Twilio] [try_twilio]. È possibile iniziare con un account gratuito e aggiornare il proprio account in un secondo momento.

Quando si effettua l'iscrizione per un account Twilio, si riceverà un ID account e un token di autenticazione. Per effettuare chiamate API Twilio entrambe saranno necessarie. Per impedire l'accesso non autorizzato al proprio account, proteggere i token di autenticazione. L'ID account e l'autenticazione token possono essere visualizzati nella [pagina account personale Twilio] [twilio_account], nel campo etichetta **SID di ACCOUNT** e **AUTH TOKEN**, rispettivamente.

## <a id="create_app"></a>Creare un'applicazione Java
1. Ottenere il Twilio JAR e aggiungere il percorso di compilazione di linguaggio e assembly distribuzione WAR. In [https://github.com/twilio/twilio-java][twilio_java], è possibile scaricare le origini GitHub e creare il proprio VASO o scaricare un VASO predefinito (con o senza dipendenze).
2. Assicurarsi che keystore **cacerts** del JDK contiene il certificato di autorità di certificazione sicura Equifax con MD5 ID digitale 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (il numero seriale 35:DE:F4:CF e l'ID digitale SHA1 D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A). Questo è il certificato di certificazione (CA) certificato per [https://api.twilio.com] [ twilio_api_service] servizio, ossia quando si utilizza Twilio APIs. Per informazioni su come assicurare del JDK **cacerts** keystore contiene il certificato CA corretto, vedere [aggiunta di un certificato nell'archivio certificati CA linguaggio][add_ca_cert].

Istruzioni dettagliate per l'utilizzo della raccolta di client Twilio per Java sono disponibili come [effettuare un Twilio tramite chiamata telefonica in un'applicazione Java su Azure][howto_phonecall_java].

## <a id="configure_app"></a>Configurare l'applicazione per usare le raccolte Twilio
All'interno del codice, è possibile aggiungere le istruzioni **import** nella parte superiore dei file di origine per i pacchetti Twilio o le classi da usare nell'applicazione. 

Per i file di origine Java:

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

Per i file di origine Java Server Page (JSP):

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
A seconda di quale Twilio pacchetti o classi da usare, le istruzioni **import** possono essere diverse.

## <a id="howto_make_call"></a>Procedura: effettuare una chiamata in uscita
Di seguito viene illustrato come effettuare una chiamata in uscita utilizzando la classe **CallFactory** . Questo codice utilizza anche un sito forniti Twilio per restituire la risposta Twilio Markup Language (TwiML). Sostituire i valori per i numeri di telefono **da** e **a** e assicurarsi di verificare il numero di telefono **da** per l'account Twilio prima di eseguire il codice.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

Per ulteriori informazioni sui parametri passati al metodo **CallFactory.create** , vedere [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Come detto, questo codice utilizza un sito forniti Twilio per restituire la risposta TwiML. È possibile utilizzare invece il proprio sito per fornire la risposta TwiML; Per ulteriori informazioni, vedere [come fornire risposte TwiML in un'applicazione Java in Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Procedura: inviare un messaggio SMS
Di seguito viene illustrato come inviare un messaggio SMS utilizzando la classe **SmsFactory** . **Dal** numero, **4155992671**, viene fornito da Twilio per gli account di valutazione per l'invio di messaggi SMS. Il numero **a cui** deve essere verificato per l'account Twilio prima di eseguire il codice.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    MessageFactory messageFactory = account.getMessageFactory();
    
    List<NameValuePair> params = new ArrayList<NameValuePair>();
    params.add(new BasicNameValuePair("To", "+14159352345")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("From", "+14158141829")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("Body", "Where's Wallace?"));
    
    Message sms = messageFactory.create(params);
        
Per ulteriori informazioni sui parametri passati al metodo **SmsFactory.create** , vedere [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Procedura: fornire risposte TwiML nel proprio sito Web
Quando l'applicazione avvia una chiamata all'API Twilio, ad esempio tramite il metodo **CallFactory.create** Twilio invia la richiesta a un URL che si prevede che restituisca una risposta TwiML. Nell'esempio viene utilizzata la condizione Twilio URL [http://twimlets.com/message][twimlet_message_url]. (Mentre TwiML deve essere utilizzato dai servizi Web, è possibile visualizzare il TwiML nel browser. Ad esempio, fare clic su [http://twimlets.com/message] [ twimlet_message_url] per visualizzare un oggetto vuoto ** &lt;risposta&gt; ** elemento; ad esempio, fare clic su [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] per visualizzare un ** &lt;risposta&gt; ** elemento che contiene un ** &lt;pronunciare&gt; ** elemento.)

Anziché ricorrere a URL forniti Twilio, è possibile creare il proprio sito di URL che restituisce risposte HTTP. È possibile creare il sito in qualsiasi lingua che restituisce le risposte HTTP. In questo argomento si presuppone che è possibile ospitare l'URL in una pagina JSP.

La pagina JSP seguente restituisce come risultato una risposta TwiML che indica che **Hello World** alla chiamata.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

La pagina JSP seguente restituisce come risultato una risposta TwiML che indica che un testo, con diverse pause e indica che le informazioni sulla versione dell'API Twilio e il nome del ruolo Azure.


    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

Il parametro **ApiVersion** è disponibile in Twilio vocali richieste (non SMS). Per visualizzare la richiesta disponibile parametri per Twilio vocali e le richieste di SMS, vedere <https://www.twilio.com/docs/api/twiml/twilio_request> e <https://www.twilio.com/docs/api/twiml/sms/twilio_request>. La variabile di ambiente **RoleName** è disponibile come parte della distribuzione di Windows Azure. (Se si vuole aggiungere variabili di ambiente personalizzate in modo che possono essere selezionati da **System.getenv**, vedere la sezione variabili di ambiente [varie]impostazioni di configurazione di ruolo[misc_role_config_settings].)

Dopo avere creato la pagina JSP configurata per fornire risposte TwiML, utilizzare l'URL della pagina JSP come URL passati al metodo **CallFactory.create** . Ad esempio, se si dispone di un'applicazione Web denominata MyTwiML distribuito in un Azure ospitato servizio e il nome della pagina JSP è mytwiml.jsp, l'URL da passare alla **CallFactory.create** come illustrato di seguito:

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Per rispondere con TwiML un'altra opzione consiste nell'utilizzare la classe **TwiMLResponse** , che è disponibile nel pacchetto **com.twilio.sdk.verbs** .

Per ulteriori informazioni sull'uso di Twilio in Azure con linguaggio, vedere [come rendere un Twilio tramite chiamata telefonica in un'applicazione Java su Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Procedura: utilizzare i servizi Twilio aggiuntive
Oltre a esempi descritti di seguito, Twilio offre API basata sul web che consente di sfruttare le funzionalità Twilio aggiuntiva dall'applicazione di Azure. Per informazioni dettagliate, vedere la [documentazione API Twilio] [twilio_api_documentation].

## <a id="NextSteps"></a>Passaggi successivi
Dopo aver appreso le nozioni di base del servizio Twilio, seguire questi collegamenti per altre informazioni:

* [Linee guida di sicurezza Twilio] [twilio_security_guidelines]
* [Esempio di codice e di Twilio HowTo] [twilio_howtos]
* [Esercitazioni Twilio][twilio_quickstarts] 
* [Twilio in GitHub] [twilio_on_github]
* [Parlare con supporto Twilio] [twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
