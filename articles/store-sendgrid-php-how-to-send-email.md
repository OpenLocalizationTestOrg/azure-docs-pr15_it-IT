<properties 
    pageTitle="Come utilizzare il servizio di posta elettronica SendGrid (PHP) | Microsoft Azure" 
    description="Informazioni su come inviare posta elettronica con il servizio di posta elettronica SendGrid in Azure. Esempi di codice scritti in PHP." 
    documentationCenter="php" 
    services="" 
    manager="sendgrid" 
    editor="mollybos" 
    authors="thinkingserious"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/30/2014" 
    ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com"/>
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Come utilizzare il servizio di posta elettronica SendGrid da PHP

Questa guida viene illustrato come eseguire attività comuni di programmazione con il servizio di posta elettronica SendGrid in Azure. Gli esempi sono scritti in PHP.
Gli scenari coperti includono **la costruzione di posta elettronica**, **invio di posta elettronica**e **aggiungere allegati**. Per ulteriori informazioni su SendGrid e invio di posta elettronica, vedere la sezione [Passaggi successivi](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>Che cos'è il servizio di posta elettronica SendGrid?

SendGrid è un [servizio di posta elettronica basato su cloud] che offre affidabile [recapito della posta elettronica transazione], scalabilità e in tempo reale analitica insieme API flessibile che semplificano l'integrazione personalizzata. Scenari di utilizzo SendGrid comuni includono:

-   Inviare automaticamente conferme ai clienti
-   Amministrazione di distribuzione sono elencati per l'invio di clienti volantini di e mensile e offerte speciali
-   La raccolta metriche in tempo reale per le operazioni come la posta elettronica bloccati e tempi di risposta del cliente
-   Creazione di report per identificare le tendenze
-   Inoltro di richieste dei clienti
- Notifiche tramite posta elettronica dall'applicazione in uso

Per ulteriori informazioni, vedere [https://sendgrid.com][].

## <a name="create-a-sendgrid-account"></a>Creare un Account SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Utilizzo SendGrid dall'applicazione PHP

Uso SendGrid in un'applicazione di Azure PHP non richiede alcuna speciale codifica o configurazione. Poiché SendGrid è un servizio, è possibile accedervi nello stesso modo da un'applicazione cloud che può da un'applicazione locale.

## <a name="how-to-send-an-email"></a>Procedura: inviare un messaggio di posta elettronica

È possibile inviare posta elettronica tramite SMTP o l'API Web fornita da SendGrid.

### <a name="smtp-api"></a>API SMTP

Per inviare posta elettronica tramite l'API di SMTP SendGrid, utilizzare *Mailer Swift*, una libreria basata su componenti per l'invio di messaggi di posta elettronica da applicazioni PHP. È possibile scaricare la raccolta *Swift Mailer* da [http://swiftmailer.org/download][] v5.3.0 (utilizzare [compositore] installare Mailer Swift). Invio di posta elettronica con la raccolta consiste nel creare le istanze della <span class="auto-style2">Swift\_SmtpTransport</span>, <span class="auto-style2">Swift\_Mailer</span>, e <span class="auto-style2">Swift\_messaggio</span> classi, impostare le proprietà appropriate e chiamare il <span class="auto-style2">Swift\_Mailer::send</span> metodo.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = "<html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>";
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

### <a name="web-api"></a>Dell'API Web

Utilizzare [voltare funzione PHP][] per inviare posta elettronica utilizzando l'API Web SendGrid.

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@contoso.com',
       );
       
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

API Web del SendGrid è molto simile a un API REST, anche se non è effettivamente un'API REST poiché nella maggior parte delle chiamate, sia visualizzato e verbi POST possono essere utilizzati in modo intercambiabile.

## <a name="how-to-add-an-attachment"></a>Procedura: aggiungere un allegato

### <a name="smtp-api"></a>API SMTP

Invio di un allegato tramite l'API SMTP prevede una riga di codice per lo script di esempio per l'invio tramite posta elettronica Swift Mailer aggiuntiva.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = "<html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>";

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     
     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';
     
     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - ";
          print_r($failures);
     }

Riga di codice aggiuntivo è il seguente:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

Questa riga di codice chiama il metodo di allegare il <span class="auto-style2">Swift\_messaggio</span> oggetto e viene utilizzato il metodo statico <span class="auto-style2">fromPath</span> sul <span class="auto-style2">Swift\_allegato</span> classe per recuperare e allegare un file a un messaggio.

### <a name="web-api"></a>Dell'API Web

Invio di un allegato tramite l'API Web è molto simile all'invio di un messaggio di posta elettronica tramite l'API Web. Tuttavia, si noti che nell'esempio che segue, la matrice di parametri deve contenere questo elemento:

    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

Esempio:

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';
     
     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@contoso.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );
     
     print_r($params);
     
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Procedura: utilizzare i filtri per abilitare Analitica, verifica e piè di pagina

SendGrid offre funzionalità di posta elettronica aggiuntivi mediante l'utilizzo di filtri di' '. Queste sono impostazioni che possono essere aggiunti a un messaggio di posta elettronica per attivare funzionalità specifiche, ad esempio abilitare fare clic su verifica, Google analitica, abbonamento verifica e così via.

Utilizzando la proprietà di filtri, filtri possono essere applicati a un messaggio. Ogni filtro ha specificato un hash contenente impostazioni specifiche del filtro. Nell'esempio seguente attiva il filtro di piè di pagina e specifica un messaggio che verrà aggiunto alla fine del messaggio di posta elettronica.
In questo esempio verrà utilizzato [sendgrid php raccolta].
Utilizzare [compositore] per installare una raccolta:
    
    php composer.phar require sendgrid/sendgrid 2.1.1

Esempio:    

    <?php
     /*
      * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
      */
     include "vendor/autoload.php";

     $email = new SendGrid\Email();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');

     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');

     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');

     // Set all of the above variables
     $email->setTos($toList);
     $email->addSubstitution('-name-', $nameList);
     $email->addSubstitution('-time-', $timeList);

     // Specify that this is an initial contact message
     $email->addCategory("initial");

     // You can optionally setup individual filters here, in this example, we have 
     // enabled the footer filter
     $email->addFilter('footer', 'enable', 1);
     $email->addFilter('footer', "text/plain", "Thank you for your business");
     $email->addFilter('footer', "text/html", "Thank you for your business");

     // The subject of your email
     $subject = 'Example SendGrid Email';

     // Where is this message coming from. For example, this message can be from 
     // support@yourcompany.com, info@yourcompany.com
     $from = 'someone@example.com';

     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = 'john@contoso.com';

     # Create the body of the message (a plain-text and an HTML version). 
     # text is your plain-text email 
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed

     /*
      * Note the variable substitution here =)
      */
     $text = "
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred";

     $html = "
     <html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.

     Regards,

     Fred<br>
     </p>
     </body>
     </html>";

     // set subject
     $email->setSubject($subject);

     // attach the body of the email
     $email->setFrom($from);
     $email->setHtml($html);
     $email->addTo($to);
     $email->setText($text);

     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';

     // Create SendGrid object
     $sendgrid = new SendGrid($username, $password);

     // send message
     $response = $sendgrid->send($email);

     print_r($response);

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base del servizio di posta elettronica SendGrid, seguire questi collegamenti per altre informazioni.

-   Documentazione SendGrid: <https://sendgrid.com/docs>
-   Raccolta SendGrid PHP: <https://github.com/sendgrid/sendgrid-php>
-   Offerta speciale SendGrid per i clienti di Azure: <https://sendgrid.com/windowsazure.html>

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori PHP](/develop/php/).


  [https://sendgrid.com]: https://sendgrid.com
  [https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
  [special offer]: https://www.sendgrid.com/windowsazure.html
  [Packaging and Deploying PHP Applications for Azure]: http://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
  [http://swiftmailer.org/download]: http://swiftmailer.org/download
  [funzione curvatura]: http://php.net/curl
  [servizio di posta elettronica basato sul cloud]: https://sendgrid.com/email-solutions
  [recapito della posta elettronica transazione]: https://sendgrid.com/transactional-email
  [raccolta sendgrid php]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
  [Compositore]: https://getcomposer.org/download/
