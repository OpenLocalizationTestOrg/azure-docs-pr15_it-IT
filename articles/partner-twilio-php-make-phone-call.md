<properties
    pageTitle="Come effettuare una chiamata telefonica da Twilio (PHP) | Microsoft Azure"
    description="Informazioni su come effettuare una chiamata telefonica e inviare un messaggio SMS con il servizio Twilio API in Azure. Gli esempi sono per applicazione PHP."
    documentationCenter="php"
    services=""
    authors="devinrader"
    manager="twilio"
    editor="mollybos"/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="11/25/2014"
    ms.author="microsofthelp@twilio.com"/>

# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Come effettuare una chiamata telefonica usando Twilio in un'applicazione PHP su Azure

Nell'esempio seguente mostra come è possibile utilizzare Twilio per effettuare una chiamata da una pagina web PHP ospitato in Azure. L'applicazione risultante richiederà l'utente per i valori telefonata, come illustrato nella schermata seguente.

![Modulo di Azure chiamata tramite Twilio e PHP][twilio_php]

È necessario eseguire le operazioni seguenti per utilizzare il codice in questo argomento:

1. Acquisire un account Twilio e l'autenticazione token. Per iniziare a utilizzare Twilio, valutare prezzi in [http://www.twilio.com/pricing][twilio_pricing]. È possibile iscriversi per un versione di valutazione account [https://www.twilio.com/try-twilio][try_twilio]. Per informazioni sull'API fornito da Twilio, vedere [http://www.twilio.com/api][twilio_api].
2. Ottenere la [raccolta di Twilio per PHP](https://github.com/twilio/twilio-php) o installarlo come pacchetto PERE. Per ulteriori informazioni, vedere il [file Leggimi](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Installare SDK Azure per PHP. Per una panoramica del SDK e istruzioni sull'installazione, vedere [configurare Azure SDK per PHP][setup_php_sdk].

## <a name="create-a-web-form-for-making-a-call"></a>Creare una maschera web per effettuare una chiamata

Il codice HTML seguente viene illustrato come creare una pagina web (**callform.html**) che recupera i dati degli utenti per effettuare una chiamata:

    <html>
    <head>
        <title>Automated call form</title>
    </head>
    <body>
    <h1>Automated Call Form</h1>
    <p>Fill in all fields and click <b>Make this call</b>.</p>
    <form action="makecall.php" method="post">
    <table>
        <tr>
            <td>To:</td>
            <td><input type="text" size=50 name="callTo" value=""></td>
        </tr>
        <tr>
            <td>From:</td>
            <td><input type="text" size=50 name="callFrom" value=""></td>
        </tr>
        <tr>
            <td>Call message:</td>
            <td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
        </tr>
        <tr>
            <td colspan=2><input type="submit" value="Make this call"></td>
        </tr>
    </table>
    </form>
    <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Creare il codice per effettuare la chiamata
Il codice seguente viene illustrato come creare una pagina web (**makecall.php**) chiamato quando si invia il modulo visualizzato dal **callform.html**. Il codice riportato di seguito viene creato il messaggio di chiamata e genera la chiamata. (Utilizzare l'account Twilio e l'autenticazione token anziché i valori di segnaposto assegnati a **$sid** e **$token** il codice riportato di seguito).

    <html>
    <head><title>Making call...</title></head>
    <body>
    <p>Your call is being made.</p>

    <?php
    require_once 'Services/Twilio.php';

    $sid = "your_account_sid";
    $token = "your_authentication_token";

    $from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
    $to_number = $_POST['callTo'];
    $message = $_POST['callText'];

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    $call = $client->account->calls->create(
        $from_number,
        $to_number,
        'http://twimlets.com/message?Message='.urlencode($message)
    );

    echo "Call status: ".$call->status."<br />";
    echo "URI resource: ".$call->uri."<br />";
    ?>
    </body>
    </html>

Oltre a effettuare la chiamata, **makecall.php** vengono visualizzati alcuni metadati di chiamata (esempio riportato nella figura riportata di seguito). Per ulteriori informazioni sui metadati di chiamata, vedere [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Risposta chiamata Azure utilizzando Twilio e PHP][twilio_php_response]

## <a name="run-the-application"></a>Eseguire l'applicazione
Il passaggio successivo consiste nel distribuire l'applicazione ai siti Web di Azure. I seguenti articoli contengono le informazioni per creare un sito Web e la distribuzione di codice con fra, FTP o WebMatrix (anche se non tutte le informazioni in ogni articolo sono rilevante):

* [Creare un sito Web di Azure PHP MySQL e distribuire utilizzando fra][website-git]
* [Creare un sito Web Azure PHP MySQL e distribuire tramite FTP][website-ftp]

## <a name="next-steps"></a>Passaggi successivi
Questo codice è stato fornito per indicare la funzionalità di base usando Twilio in PHP in Azure. Prima di distribuire in Azure in produzione, è consigliabile aggiungere ulteriori la gestione degli errori o altre caratteristiche. Per esempio:

* Invece di usare una maschera web, è possibile utilizzare BLOB Azure dello spazio di archiviazione o Database SQL per archiviare i numeri di telefono e il testo di chiamata. Per informazioni sull'uso di BLOB Azure dello spazio di archiviazione in PHP, vedere [Utilizzo di archiviazione di Azure con applicazioni PHP][howto_blob_storage_php]. Per informazioni sull'uso di Database SQL in PHP, vedere [Utilizzo di Database SQL con applicazioni PHP][howto_sql_azure_php].
* Il codice **makecall.php** utilizza URL forniti Twilio ([http://twimlets.com/message][twimlet_message_url]) per fornire una risposta Twilio Markup Language (TwiML) che informa Twilio come procedere con la chiamata. Ad esempio, può contenere TwiML che viene restituito un `<Say>` verbo di testo che viene pronunciato al destinatario di chiamata. Invece di usare l'URL forniti Twilio, è possibile creare il proprio servizio per rispondere alla richiesta di Twilio; Per ulteriori informazioni, vedere [come utilizzare Twilio per vocali e le funzionalità di SMS in PHP][howto_twilio_voice_sms_php]. Sono disponibili ulteriori informazioni su TwiML in [http://www.twilio.com/docs/api/twiml][twiml]e altre informazioni sul `<Say>` e altri comandi Twilio sono disponibili in [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Leggere le indicazioni sulla protezione Twilio in [https://www.twilio.com/docs/security][twilio_docs_security].

Per ulteriori informazioni su Twilio, vedere [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Vedere anche
* [Come usare Twilio per vocali e le funzionalità di SMS in PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[setup_php_sdk]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[website-git]: ./web-sites/web-sites-php-mysql-deploy-use-git.md
[website-ftp]: ./web-sites/web-sites-php-mysql-deploy-use-ftp.md
[twilio_php_github]: https://github.com/twilio/twilio-php
