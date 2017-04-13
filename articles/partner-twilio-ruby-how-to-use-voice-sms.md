<properties 
    pageTitle="Come usare Twilio per vocali e SMS (trascrizione) | Microsoft Azure" 
    description="Informazioni su come effettuare una chiamata telefonica e inviare un messaggio SMS con il servizio Twilio API in Azure. Esempi di codice scritti in trascrizione." 
    services="" 
    documentationCenter="ruby" 
    authors="devinrader" 
    manager="twilio" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ruby" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="MicrosoftHelp@twilio.com"/>





# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Come usare Twilio per vocali e le funzionalità di SMS in trascrizione
Questa guida viene illustrato come eseguire attività comuni di programmazione con il servizio Twilio API in Azure. Gli scenari coperti includono una telefonata e invio di un messaggio breve messaggio SMS (Service). Per ulteriori informazioni sulla Twilio e l'utilizzo di voce e SMS nelle applicazioni, vedere la sezione [Passaggi successivi](#NextSteps) .

## <a id="WhatIs"></a>Che cos'è Twilio?
Twilio è un'API di servizio web telefonia che consente di utilizzare le lingue web esistenti e le competenze compilazione vocali e le applicazioni di SMS. Twilio è un servizio di terze parti (non una caratteristica di Azure e non un prodotto Microsoft).

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente alle applicazioni di effettuare e ricevere messaggi SMS. **Twilio Client** consente alle applicazioni di abilitare la comunicazione vocale utilizzando esistente connessioni Internet, incluse quelle per dispositivi mobili.

## <a id="Pricing"></a>Twilio prezzi e offerte speciali
Informazioni sui prezzi Twilio sono disponibile in [Prezzi Twilio] [twilio_pricing]. Clienti Azure ricevano un' [offerta speciale][special_offer]: una carta di credito gratuito di 1000 testi o 1000 Minute in ingresso. Per iscriversi a questa offerta o ottenere ulteriori informazioni, visitare [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Concetti
L'API Twilio è una API REST che fornisce vocali e le funzionalità SMS, Applications Edition. Librerie client sono disponibili in più lingue. per un elenco, vedere [Librerie API Twilio] [twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML è un set di istruzioni basato su XML che determinano Twilio dell'elaborazione di una chiamata o SMS.

Se ad esempio TwiML seguenti da convertire il testo **Hello World** in sintesi e riconoscimento vocale.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Tutti i documenti TwiML dispongono `<Response>` elementi radice. A questo punto, è possibile Twilio verbi per definire il comportamento dell'applicazione.

### <a id="Verbs"></a>Verbi TwiML
Verbi Twilio sono tag XML che indicano quali Twilio da **eseguire**. Ad esempio, il ** &lt;pronunciare&gt; ** verbo indica Twilio recapitare chiaramente un messaggio in una chiamata. 

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

Per ulteriori informazioni sulle Twilio verbi relativi attributi e TwiML, vedere [TwiML] [twiml]. Per ulteriori informazioni sulle API Twilio, vedere [API Twilio] [twilio_api].

## <a id="CreateAccount"></a>Creare un Account Twilio
Quando si è pronti per ottenere un account Twilio, iscriversi al [Provare Twilio] [try_twilio]. È possibile iniziare con un account gratuito e aggiornare il proprio account in un secondo momento.

Quando si effettua l'iscrizione per un account Twilio, viene visualizzato un numero di telefono gratuito per l'applicazione. Verrà anche visualizzato un account SID e un token di autenticazione. Per effettuare chiamate API Twilio entrambe saranno necessarie. Per impedire l'accesso non autorizzato al proprio account, proteggere i token di autenticazione. L'account SID e token di autenticazione possono essere visualizzati nella [pagina account personale Twilio][twilio_account], nel campo etichetta **SID di ACCOUNT** e **AUTH TOKEN**, rispettivamente.

### <a id="VerifyPhoneNumbers"></a>Verificare i numeri di telefono
Oltre al numero vengono forniti da Twilio, è inoltre possibile verificare i numeri che si controllo (ad esempio il cellulare o abitazione numero di telefono) da utilizzare nelle applicazioni. 

Per informazioni su come verificare un numero di telefono, vedere [Gestire i numeri] [verify_phone].

## <a id="create_app"></a>Creare un'applicazione di trascrizione
Un'applicazione trascrizione che utilizza il servizio Twilio ed è in esecuzione in Azure vi è alcuna differenza rispetto a qualsiasi altra applicazione trascrizione che utilizza il servizio Twilio. Mentre Twilio servizi RESTful e possono essere chiamati da trascrizione in diversi modi, in questo articolo verrà focalizzare l'attenzione su come usare i servizi Twilio con [Twilio libreria di supporto per trascrizione][twilio_ruby].

Prima di tutto, [impostare una nuova macchina virtuale Linux Azure] [ azure_vm_setup] per agire come host per la nuova applicazione web trascrizione. Ignorare i passaggi che comportano la creazione di un'app di guide, configurazione soltanto la macchina virtuale. Verificare che si crea un Endpoint con una porta esterna di 80 e una porta interna di 5000.

Negli esempi seguenti verranno utilizzate [Sinatra][sinatra], un quadro web molto semplice per trascrizione. Ma è possibile utilizzare la libreria di supporto Twilio per trascrizione con qualsiasi altro framework web, inclusi trascrizione su Guide.

SSH nella nuova macchina virtuale e creare una directory per la nuova app. All'interno di tale directory, creare un file denominato Gemfile e copiare il codice seguente:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Nella riga di comando eseguita `bundle install`. Verrà installato con le dipendenze sopra. Creare un file denominato `web.rb`. Questo sarà in cui si trova il codice per un'app web. Incollare il codice seguente al suo interno:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

A questo punto dovrebbe essere possibile eseguire il comando `ruby web.rb -p 5000`. Questo sarà selezione-su un server web small sulla porta 5000. Dovrebbe essere possibile cercare per questa app nel browser, visitare l'URL è configurazione di una macchina virtuale Azure. Una volta per accedere a un'app web nel browser, si è pronti per iniziare a creare un'app Twilio.

## <a id="configure_app"></a>Configurare l'applicazione in uso Twilio
È possibile configurare un'applicazione web per l'utilizzo della raccolta Twilio aggiornando il `Gemfile` per includere la riga:

    gem 'twilio-ruby'

Nella riga di comando eseguire `bundle install`. A questo punto aprire `web.rb` e la riga superiore, compresi:

    require 'twilio-ruby'

È ora possibile iniziare a utilizzare la libreria di supporto Twilio per trascrizione nelle applicazioni web.

## <a id="howto_make_call"></a>Procedura: effettuare una chiamata in uscita
Di seguito viene illustrato come effettuare una chiamata in uscita. Concetti fondamentali includono utilizzo della libreria di supporto Twilio per trascrizione per effettuare chiamate all'API REST e rendering TwiML. Sostituire i valori per i numeri di telefono **da** e **a** e assicurarsi di verificare il numero di telefono **da** per l'account Twilio prima di eseguire il codice.

Aggiungere questa funzione per `web.md`:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);
      
      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end
    
Se aperto-up `http://yourdomain.cloudapp.net/make_call` in un browser che attiverà la chiamata all'API Twilio per effettuare la chiamata telefonica. I primi due parametri in `client.account.calls.create` sono spiegazioni: il numero di chiamata è `from` ed è la chiamata al numero `to`. 

Il terzo parametro (`url`) è l'URL che Twilio le richieste per ottenere istruzioni sulle operazioni da eseguire una volta connessa la chiamata. In questo caso è configurazione un URL (`http://yourdomain.cloudapp.net`) che restituisce un documento TwiML semplice e si utilizza il `<Say>` verbo che consente di eseguire alcune funzionalità di sintesi vocale e pronunciare "Hello scimmia" per la persona che riceve la chiamata.

## <a id="howto_recieve_sms"></a>Procedura: ricevere un messaggio SMS
Nell'esempio precedente viene avviata una chiamata telefonica **in uscita** . Questa volta, utilizzare il numero di telefono che Twilio abbiamo durante l'iscrizione per l'elaborazione di un messaggio SMS **in arrivo** .

Innanzitutto, log aggiuntivo per il [dashboard Twilio][twilio_account]. Fare clic su "Numeri" nel riquadro di spostamento superiore e quindi fare clic sul numero Twilio che sono state fornite. Si noterà due URL che è possibile configurare. URL di richiesta di un URL di richiesta vocali e un SMS. Ecco gli URL che Twilio chiama ogni volta che si effettua una chiamata telefonica o un SMS inviato il numero. L'URL è noto anche come "web hook".

Si vuole elaborare messaggi SMS in entrata, pertanto è possibile aggiornare l'URL per `http://yourdomain.cloudapp.net/sms_url`. Procedere facendo clic su Salva modifiche nella parte inferiore della pagina. A questo punto, eseguire il backup `web.rb` possibile programmare l'applicazione per gestire questo problema:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Dopo aver apportato le modifiche, assicurarsi di riavviare l'applicazione web. A questo punto, estrarre il telefono e inviare un SMS al numero di Twilio. È necessario ottenere rapidamente una risposta SMS che indica che "archivio, grazie per il ping! Estremamente Twilio e Azure! ".

## <a id="additional_services"></a>Procedura: utilizzare i servizi Twilio aggiuntive
Oltre a esempi descritti di seguito, Twilio offre API basata sul web che consente di sfruttare le funzionalità Twilio aggiuntiva dall'applicazione di Azure. Per informazioni dettagliate, vedere la [documentazione API Twilio] [twilio_api_documentation].

### <a id="NextSteps"></a>Passaggi successivi
Dopo aver appreso le nozioni di base del servizio Twilio, seguire questi collegamenti per altre informazioni:

* [Linee guida di sicurezza Twilio] [twilio_security_guidelines]
* [Oggetti Twilio e codice di esempio] [twilio_howtos]
* [Esercitazioni Twilio][twilio_quickstarts] 
* [Twilio in GitHub] [twilio_on_github]
* [Parlare con supporto Twilio] [twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: http://www.windowsazure.com/develop/ruby/tutorials/web-app-with-linux-vm/
