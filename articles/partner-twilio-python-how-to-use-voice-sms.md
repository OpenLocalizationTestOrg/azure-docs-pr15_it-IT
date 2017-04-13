<properties
    pageTitle="Come usare Twilio per vocali e SMS (PHP) | Microsoft Azure"
    description="Informazioni su come effettuare una chiamata telefonica e inviare un messaggio SMS con il servizio Twilio API in Azure. Esempi di codice scritti in PHP."
    services=""
    documentationCenter="python"
    authors="devinrader"
    manager="twilio"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="python" 
    ms.topic="article"
    ms.date="02/19/2015"
    ms.author="MicrosoftHelp@twilio.com"/>





# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Come usare Twilio per vocali e le funzionalità di SMS in PHP
Questa guida viene illustrato come eseguire attività comuni di programmazione con il servizio Twilio API in Azure. Gli scenari coperti includono una telefonata e invio di un messaggio breve messaggio SMS (Service). Per ulteriori informazioni sulla Twilio e l'utilizzo di voce e SMS nelle applicazioni, vedere la sezione [Passaggi successivi](#NextSteps) .

## <a id="WhatIs"></a>Che cos'è Twilio?
Twilio sia ormai futuro delle comunicazioni aziendali, consentendo agli sviluppatori di incorporare vocali, le chiamate VoIP e messaggistica nelle applicazioni. Sono virtualizzazione tutta l'infrastruttura necessari in un ambiente basato sul cloud, globale, esposizione mediante la piattaforma di comunicazioni API Twilio. Le applicazioni sono semplici da creare e scalabilità. Sfrutteranno flessibilità con pagare capacità di passare prezzi e trarre vantaggio da affidabilità cloud.

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente all'applicazione di inviare e ricevere messaggi di testo. **Twilio Client** consente di effettuare chiamate VoIP da qualsiasi telefono, un tablet o un browser e supporta WebRTC.

## <a id="Pricing"></a>Twilio prezzi e offerte speciali

Azure clienti [offerta speciale] $10 della carta di credito Twilio quando si aggiorna il Twilio Account. Questa credito Twilio possono essere applicata a qualsiasi utilizzo Twilio ($10 carta di credito equivalente per l'invio di un massimo di 1.000 messaggi SMS o la ricezione di un massimo di 1000 minuti vocali in ingresso, a seconda della posizione della destinazione numero e messaggio o una chiamata telefonica). Riscattare questa credito Twilio e Introduzione a: [ahoy.twilio.com/azure].

Twilio è un servizio in. Sono non state tariffe configurazione ed è possibile chiudere l'account in qualsiasi momento. È possibile trovare ulteriori informazioni su [Prezzi Twilio] [twilio_pricing].

## <a id="Concepts"></a>Concetti
L'API Twilio è una API REST che fornisce vocali e le funzionalità SMS, Applications Edition. Librerie client sono disponibili in più lingue. per un elenco, vedere [Librerie API Twilio] [twilio_libraries].

Aspetti chiave dell'API Twilio sono verbi Twilio e Twilio Markup Language (TwiML).

### <a id="Verbs"></a>Verbi Twilio
Utilizza l'API di Twilio verbi; ad esempio, il ** &lt;pronunciare&gt; ** verbo indica Twilio recapitare chiaramente un messaggio in una chiamata.

Di seguito è un elenco di verbi Twilio. Informazioni sulle altre verbi e funzionalità tramite [documentazione di Twilio Markup Language] [http://www.twilio.com/docs/api/twiml].

* ** &lt;Connessione&gt;**: il chiamante si connette a un altro telefono.
* ** &lt;Raccogliere&gt;**: raccoglie cifre numeriche immesse della tastiera telefonica.
* ** &lt;Interruzione&gt;**: termina una chiamata.
* ** &lt;Riprodurre&gt;**: riproduce un file audio.
* ** &lt;Pausa&gt;**: che venga automaticamente per un determinato numero di secondi.
* ** &lt;Record&gt;**: vengono registrate la voce del chiamante e restituisce un URL di un file che contiene la registrazione.
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
Quando si è pronti ottenere un account Twilio, iscriversi al [Provare Twilio] [try_twilio]. È possibile iniziare con un account gratuito e aggiornare il proprio account in un secondo momento.

Quando si effettua l'iscrizione per un account Twilio, si riceve un ID account e un token di autenticazione. Per effettuare chiamate API Twilio entrambe saranno necessarie. Per impedire l'accesso non autorizzato al proprio account, proteggere i token di autenticazione. L'ID account e l'autenticazione token possono essere visualizzati nella [pagina account personale Twilio] [twilio_account], nel campo etichetta **SID di ACCOUNT** e **AUTH TOKEN**, rispettivamente.

## <a id="create_app"></a>Creare un'applicazione PHP
Un'applicazione PHP che utilizza il servizio Twilio ed è in esecuzione in Azure vi è alcuna differenza rispetto a qualsiasi altra applicazione PHP che utilizza il servizio Twilio. Mentre Twilio servizi basati su REST e possono essere chiamati da PHP in diversi modi, in questo articolo verrà focalizzare l'attenzione su come usare i servizi Twilio con [una raccolta di Twilio per PHP da GitHub][twilio_php]. Per ulteriori informazioni sull'utilizzo della raccolta Twilio per PHP, vedere [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Istruzioni dettagliate per la creazione e distribuzione di un'applicazione di Twilio/PHP in Azure sono disponibili come [effettuare un Twilio tramite chiamata telefonica in un'applicazione PHP in Azure][howto_phonecall_php].

## <a id="configure_app"></a>Configurare l'applicazione per usare le raccolte Twilio
È possibile configurare l'applicazione per utilizzare la libreria Twilio per PHP in due modi:

1. Scaricare la raccolta di Twilio per PHP da GitHub ([https://github.com/twilio/twilio-php][twilio_php]) e aggiungere la directory **dei servizi** per l'applicazione.

    -OPPURE-

2. Installare la raccolta di Twilio per PHP come pacchetto PERE. Può essere installato con i comandi seguenti:

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Dopo aver installato la raccolta di Twilio per PHP, è quindi possibile aggiungere un'istruzione **require_once** nella parte superiore dei file PHP per fare riferimento alla libreria:

        require_once 'Services/Twilio.php';

Per ulteriori informazioni, vedere [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Procedura: effettuare una chiamata in uscita
Di seguito viene illustrato come effettuare una chiamata in uscita utilizzando la classe **Services_Twilio** . Questo codice utilizza anche un sito forniti Twilio per restituire la risposta Twilio Markup Language (TwiML). Sostituire i valori per i numeri di telefono **da** e **a** e assicurarsi di verificare il numero di telefono **da** per l'account Twilio prima di eseguire il codice.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

Come detto, questo codice utilizza un sito forniti Twilio per restituire la risposta TwiML. È possibile utilizzare invece il proprio sito per fornire la risposta TwiML; Per ulteriori informazioni, vedere [come fornire risposte TwiML dal proprio sito Web](#howto_provide_twiml_responses).


- **Nota**: per risolvere gli errori di convalida del certificato SSL, vedere [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation]


## <a id="howto_send_sms"></a>Procedura: inviare un messaggio SMS
Di seguito viene illustrato come inviare un messaggio SMS utilizzando la classe **Services_Twilio** . Il numero **da** viene fornito da Twilio per gli account di valutazione per l'invio di messaggi SMS. Il numero **a cui** deve essere verificato per l'account Twilio prima di eseguire il codice.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a id="howto_provide_twiml_responses"></a>Procedura: fornire risposte TwiML nel proprio sito Web
Quando l'applicazione avvia una chiamata all'API Twilio, Twilio invia la richiesta a un URL che si prevede che restituisca una risposta TwiML. Nell'esempio viene utilizzata la condizione Twilio URL [http://twimlets.com/message][twimlet_message_url]. (Mentre TwiML è destinato Twilio, è possibile visualizzare il it nel browser. Ad esempio, fare clic su [http://twimlets.com/message] [ twimlet_message_url] per visualizzare un oggetto vuoto `<Response>` elemento; ad esempio, fare clic su [http://twimlets.com/message?Message%5B0%5D=Hello%20World] [ twimlet_message_url_hello_world] per visualizzare un `<Response>` elemento che contiene un `<Say>` elemento.)

Anziché ricorrere a URL forniti Twilio, è possibile creare un sito che restituisce risposte HTTP. È possibile creare il sito in qualsiasi lingua che restituisce le risposte XML. si presuppone che verrà utilizzato PHP per creare il TwiML.

La pagina PHP seguente restituisce come risultato una risposta TwiML che indica che **Hello World** alla chiamata.

    <?php
        header("content-type: text/xml");
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>
        <Say>Hello world.</Say>
    </Response>

Come si può vedere dell'esempio precedente, la risposta TwiML è semplicemente un documento XML. La raccolta di Twilio per PHP contiene classi che verranno generato TwiML di. Nell'esempio seguente viene prodotta la risposta equivalente come illustrato sopra, ma utilizza il **servizi\_Twilio\_Twiml** classe nella libreria di Twilio per PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Per ulteriori informazioni su TwiML, vedere [https://www.twilio.com/docs/api/twiml][twiml_reference].

Dopo avere creato la pagina PHP configurata per fornire risposte TwiML, utilizzare l'URL della pagina PHP come passato l'URL di `Services_Twilio->account->calls->create` metodo. Ad esempio, se si dispone di un'applicazione Web denominata **MyTwiML** distribuito in un Azure ospitato servizio e il nome della pagina PHP è **mytwiml.php**, l'URL è possibile passare a **Services_Twilio -> account -> chiamate -> Crea** come illustrato nell'esempio seguente:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

Per ulteriori informazioni sull'uso di Twilio in Azure con PHP, vedere [come rendere un Twilio tramite chiamata telefonica in un'applicazione PHP in Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Procedura: utilizzare i servizi Twilio aggiuntive
Oltre a esempi descritti di seguito, Twilio offre API basata sul web che consente di sfruttare le funzionalità Twilio aggiuntiva dall'applicazione di Azure. Per informazioni dettagliate, vedere la [documentazione API Twilio] [twilio_api_documentation].

## <a id="NextSteps"></a>Passaggi successivi
Dopo avere appreso le nozioni di base del servizio Twilio, seguire questi collegamenti per altre informazioni:

* [Linee guida di sicurezza Twilio] [twilio_security_guidelines]
* [Procedura Twilio guide e codice di esempio] [twilio_howtos]
* [Esercitazioni Twilio][twilio_quickstarts]
* [Twilio in GitHub] [twilio_on_github]
* [Parlare con supporto Twilio] [twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html

[howto_phonecall_php]: http://windowsazure.com/documentation/articles/partner-twilio-php-make-phone-call
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

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
