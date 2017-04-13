<properties 
    pageTitle="Come usare gli hub di notifica con PHP" 
    description="Informazioni su come usare gli hub di notifica di Azure un PHP back-end." 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="php" 
    ms.devlang="php" 
    ms.topic="article" 
    ms.date="06/07/2016" 
    ms.author="yuaxu"/>

# <a name="how-to-use-notification-hubs-from-php"></a>Come usare gli hub di notifica da PHP
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

È possibile accedere a tutte le funzionalità di notifica hub da un linguaggio/PHP/trascrizione back-end tramite l'interfaccia di notifica Hub resto, come descritto nell'argomento MSDN [API REST hub di notifica](http://msdn.microsoft.com/library/dn223264.aspx).

In questo argomento questo webinar mostra come:

* Creare un client resto per le funzionalità di notifica hub in PHP;
* Eseguire l' [esercitazione introduttiva Get](notification-hubs-ios-apple-push-notification-apns-get-started.md) per la piattaforma mobile della scelta effettuata, se la parte di back-end in PHP.

## <a name="client-interface"></a>Interfaccia client
L'interfaccia del client principale può fornire gli stessi metodi che sono disponibili in [.NET notifica hub SDK](http://msdn.microsoft.com/library/jj933431.aspx), sarà possibile convertire direttamente tutte le esercitazioni ed esempi attualmente disponibili in questo sito e reso disponibile da alla community su internet.

È possibile trovare il codice disponibile nel [campione spaziale PHP resto].

Ad esempio, per creare un client:

    $hub = new NotificationHub("connection string", "hubname"); 

Per inviare un iOS nativa notifica:
    
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);

## <a name="implementation"></a>Implementazione
Se non è non è già stata eseguita, eseguire le [esercitazioni introduttiva Get] fino alla sezione ultima nel punto in cui è necessario implementare back-end.
Inoltre, se si desidera che è possibile utilizzare il codice dall' [esempio spaziale resto PHP] e passare direttamente alla sezione [completata l'esercitazione](#complete-tutorial) .

Tutti i dettagli per implementare un intero spaziale resto sono disponibili sul [sito Web MSDN](http://msdn.microsoft.com/library/dn530746.aspx). In questa sezione verranno illustrate l'implementazione di PHP di passaggi principali necessari per accedere a endpoint resto hub notifica:

1. Analizzare la stringa di connessione
2. Generare il token di autorizzazione
3. Eseguire la chiamata HTTP

### <a name="parse-the-connection-string"></a>Analizzare la stringa di connessione

Ecco la classe principale implementazione del client, il cui costruttore che consente di analizzare la stringa di connessione:

    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";
    
        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;
    
        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;
    
            $this->parseConnectionString($connectionString);
        }
    
        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }
    
            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }


### <a name="create-security-token"></a>Creare token di sicurezza
I dettagli del processo di creazione di token di sicurezza sono disponibili [qui](http://msdn.microsoft.com/library/dn495627.aspx).
Il metodo seguente deve essere aggiunto alla classe **NotificationHub** per creare il token in base all'URI della richiesta corrente e le credenziali estratte dalla stringa di connessione.

    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }

### <a name="send-a-notification"></a>Invia una notifica
Prima di tutto, possiamo definire una classe che rappresenta una notifica.

    class Notification {
        public $format;
        public $payload;
    
        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;
    
        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }
    
            $this->format = $format;
            $this->payload = $payload;
        }
    }

Questa classe è un contenitore per un corpo nativo notifica o un insieme di proprietà in caso di una notifica di modello e un insieme di intestazioni che contiene le proprietà di piattaforma specifiche (ad esempio Apple scadenza proprietà e le intestazioni WNS) e formato (piattaforma nativa o modello).

Fare riferimento alla [documentazione API REST hub di notifica](http://msdn.microsoft.com/library/dn495827.aspx) e formati dei piattaforme notifica specifica per tutte le opzioni disponibili.

Grazie a questa classe, possiamo ora scrivere invia metodi di notifica all'interno della classe **NotificationHub** .

    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "gcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }
        
        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 

I metodi indicati inviano una richiesta HTTP POST all'endpoint /messages dell'hub di notifica, con le intestazioni per inviare la notifica e il corpo corretto.

##<a name="complete-tutorial"></a>Completare l'esercitazione
A questo punto è possibile completare l'esercitazione Introduzione mediante l'invio della notifica da un PHP back-end.

Inizializzare il client di notifica hub (sostituire il nome di stringa e hub di connessione le istruzioni riportate nell' [esercitazione introduttiva Get]):

    $hub = new NotificationHub("connection string", "hubname"); 

Aggiungere il codice invia a seconda la piattaforma per dispositivi mobili di destinazione.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store e Windows Phone 8.1 (non Silverlight)

    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);

### <a name="ios"></a>iOS

    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);

### <a name="android"></a>Android
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("gcm", $message);
    $hub->sendNotification($notification, null);

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 e 8.1 Silverlight

    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);


### <a name="kindle-fire"></a>Kindle Fire
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);

Eseguire il codice PHP producono ora una notifica visualizzate in un dispositivo di destinazione.


## <a name="next-steps"></a>Passaggi successivi
In questo argomento è stato illustrato come creare un semplice client di linguaggio resto hub di notifica. Da qui è possibile:

* Scaricare l'intero [campione spaziale resto PHP], che contiene il codice precedente.
* Ulteriori informazioni su come hub di notifica tagging caratteristica in [esercitazione ultime notizie]
* Informazioni sull'invio di notifiche per singoli utenti in [esercitazione notifica utenti]

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori PHP](/develop/php/).

[Esempio di spaziale PHP resto]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Esercitazione introduttiva Get]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
 
