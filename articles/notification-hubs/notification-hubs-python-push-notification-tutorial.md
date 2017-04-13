<properties 
    pageTitle="Come usare gli hub di notifica con Python" 
    description="Informazioni su come usare gli hub di notifica di Azure un Python back-end." 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu"
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="python" 
    ms.devlang="php" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="how-to-use-notification-hubs-from-python"></a>Come usare gli hub di notifica da Python
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]
        
È possibile accedere a tutte le funzionalità di notifica hub da un linguaggio/PHP/Python/trascrizione back-end tramite l'interfaccia di notifica Hub resto, come descritto nell'argomento MSDN [API REST hub di notifica](http://msdn.microsoft.com/library/dn223264.aspx).

> [AZURE.NOTE] Questo è un esempio di implementazione di riferimento per l'implementazione di Invia notifica di Python e non è ufficiale supportati SDK Python Hub di notifiche.
>
> In questo esempio vengono descritti i Python 3.4.

In questo argomento questo webinar mostra come:

* Creare un client resto per le funzionalità di notifica hub in Python.
* Invio di notifiche tramite l'interfaccia di Python alle API REST Hub di notifica. 
* È possibile ottenere un'immagine della risposta alla richiesta HTTP resto/a scopo didattico/debug. 

È possibile eseguire l' [esercitazione introduttiva Get](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) per la piattaforma mobile della scelta effettuata, se la parte di back-end in Python.

> [AZURE.NOTE] L'ambito del campione è limitato solo per l'invio di notifiche e non è una gestione di registrazione.

## <a name="client-interface"></a>Interfaccia client
L'interfaccia del client principale può fornire gli stessi metodi che sono disponibili in [.NET notifica hub SDK](http://msdn.microsoft.com/library/jj933431.aspx). È possibile convertire direttamente tutte le esercitazioni ed esempi attualmente disponibili in questo sito e reso disponibile da alla community su internet.

È possibile trovare il codice disponibile nel [campione spaziale Python resto].

Ad esempio, per creare un client:

    isDebug = True
    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
    
Per inviare una notifica di tipo avviso popup di Windows:
    
    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)
    
## <a name="implementation"></a>Implementazione
Se non è non è già stata eseguita, eseguire le [esercitazioni introduttiva Get] fino alla sezione ultima nel punto in cui è necessario implementare back-end.

Tutti i dettagli per implementare un intero spaziale resto sono disponibili sul [sito Web MSDN](http://msdn.microsoft.com/library/dn530746.aspx). In questa sezione verranno illustrate l'implementazione di Python di passaggi principali necessari per accedere a endpoint resto hub di notifica e invio di notifiche

1. Analizzare la stringa di connessione
2. Generare il token di autorizzazione
3. Invia una notifica tramite API REST HTTP

### <a name="parse-the-connection-string"></a>Analizzare la stringa di connessione

Ecco la classe principale implementazione del client, il cui costruttore analizza la stringa di connessione:

    class NotificationHub:
        API_VERSION = "?api-version=2013-10"
        DEBUG_SEND = "&test"
    
        def __init__(self, connection_string=None, hub_name=None, debug=0):
            self.HubName = hub_name
            self.Debug = debug
    
            # Parse connection string
            parts = connection_string.split(';')
            if len(parts) != 3:
                raise Exception("Invalid ConnectionString.")
    
            for part in parts:
                if part.startswith('Endpoint'):
                    self.Endpoint = 'https' + part[11:]
                if part.startswith('SharedAccessKeyName'):
                    self.SasKeyName = part[20:]
                if part.startswith('SharedAccessKey'):
                    self.SasKeyValue = part[16:]


### <a name="create-security-token"></a>Creare token di sicurezza
I dettagli del processo di creazione di token di sicurezza sono disponibili [qui](http://msdn.microsoft.com/library/dn495627.aspx).
I metodi seguenti devono essere aggiunti alla classe **NotificationHub** per creare il token in base all'URI della richiesta corrente e le credenziali estratte dalla stringa di connessione.

    @staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))

    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)

    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest

    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### <a name="send-a-notification-using-http-rest-api"></a>Invia una notifica tramite API REST HTTP
Primo, consentono di utilizzare definire una classe che rappresenta una notifica.

    class Notification:
        def __init__(self, notification_format=None, payload=None, debug=0):
            valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
            if not any(x in notification_format for x in valid_formats):
                raise Exception(
                    "Invalid Notification format. " +
                    "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")
    
            self.format = notification_format
            self.payload = payload
    
            # array with keynames for headers
            # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
            # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
            # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
            self.headers = None

Questa classe è un contenitore per un corpo nativo notifica o un insieme di proprietà in caso di una notifica di modello, un insieme di intestazioni che contiene le proprietà di piattaforma specifiche (ad esempio Apple scadenza proprietà e le intestazioni WNS) e formato (piattaforma nativa o modello).

Fare riferimento alla [documentazione API REST hub di notifica](http://msdn.microsoft.com/library/dn495827.aspx) e formati dei piattaforme notifica specifica per tutte le opzioni disponibili.

Ora con questa classe, è possibile scrivere invia metodi di notifica all'interno della classe **NotificationHub** .

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")

        connection.request('POST', url, payload, headers)
        response = connection.getresponse()

        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")

        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

        connection.close()

    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload

        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }

        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression

        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})

        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)

        self.make_http_request(url, payload_to_send, headers)

    def send_apple_notification(self, payload, tags=""):
        nh = Notification("apple", payload)
        self.send_notification(nh, tags)

    def send_gcm_notification(self, payload, tags=""):
        nh = Notification("gcm", payload)
        self.send_notification(nh, tags)

    def send_adm_notification(self, payload, tags=""):
        nh = Notification("adm", payload)
        self.send_notification(nh, tags)

    def send_baidu_notification(self, payload, tags=""):
        nh = Notification("baidu", payload)
        self.send_notification(nh, tags)

    def send_mpns_notification(self, payload, tags=""):
        nh = Notification("windowsphone", payload)

        if "<wp:Toast>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'toast', 'X-NotificationClass': '2'}
        elif "<wp:Tile>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'tile', 'X-NotificationClass': '1'}

        self.send_notification(nh, tags)

    def send_windows_notification(self, payload, tags=""):
        nh = Notification("windows", payload)

        if "<toast>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/toast'}
        elif "<tile>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/tile'}
        elif "<badge>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/badge'}

        self.send_notification(nh, tags)

    def send_template_notification(self, properties, tags=""):
        nh = Notification("template", properties)
        self.send_notification(nh, tags)

I metodi indicati inviano una richiesta HTTP POST all'endpoint /messages dell'hub di notifica, con le intestazioni per inviare la notifica e il corpo corretto.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Utilizzo di proprietà debug per attivare la registrazione dettagliata
Attivazione di proprietà debug durante l'inizializzazione Hub notifica verranno scritte le informazioni di registrazione dettagliate sulla richiesta HTTP e Dettagli risposta come messaggio di notifica dettagliato Invia risultato. Sono state recentemente aggiunte questa proprietà denominata [notifica hub TestSend proprietà](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx) che restituisce informazioni dettagliate sul risultato invia notifica. Per usarlo - inizializzare utilizzando le operazioni seguenti:

    hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

Notifica Hub Invia richiesta URL HTTP viene aggiunto con una stringa di query "test" di conseguenza. 

##<a name="complete-tutorial"></a>Completare l'esercitazione
A questo punto è possibile completare l'esercitazione Introduzione mediante l'invio della notifica da un Python back-end.

Inizializzare il client di notifica hub (sostituire il nome di stringa e hub di connessione le istruzioni riportate nell' [esercitazione introduttiva Get]):

    hub = NotificationHub("myConnectionString", "myNotificationHubName")

Aggiungere il codice invia a seconda la piattaforma per dispositivi mobili di destinazione. In questo esempio aggiunge anche i metodi di livello superiori per attivare le notifiche di inviare in base alla piattaforma, ad esempio send_windows_notification per windows. send_apple_notification (per apple) e così via. 

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store e Windows Phone 8.1 (non Silverlight)

    wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
    hub.send_windows_notification(wns_payload)

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 e 8.1 Silverlight

    hub.send_mpns_notification(toast)

### <a name="ios"></a>iOS

    alert_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_apple_notification(alert_payload)

### <a name="android"></a>Android
    gcm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_gcm_notification(gcm_payload)

### <a name="kindle-fire"></a>Kindle Fire
    adm_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_adm_notification(adm_payload)

### <a name="baidu"></a>Baidu
    baidu_payload = {
        'data':
            {
                'msg': 'Hello!'
            }
    }
    hub.send_baidu_notification(baidu_payload)

Eseguire il codice Python producono una notifica visualizzate in un dispositivo di destinazione.

## <a name="examples"></a>Esempi:

### <a name="enabling-debug-property"></a>Attivazione debug proprietà
Quando si attiva debug contrassegno durante l'inizializzazione di NotificationHub si vedrà dettagliata richiesta HTTP e immagine della risposta, nonché NotificationOutcome simile al seguente nel punto in cui è possibile capire quali intestazioni HTTP vengono passate nella richiesta e il tipo di risposta HTTP è stato ricevuto dall'Hub notifica:    ![][1]

Si vedrà dettagliate ad esempio, il risultato di Hub di notifica 

- Quando il messaggio viene inviato correttamente al servizio di notifica Push. 
    
        <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>

- Se si sono verificati alcuna destinazione per le notifiche push probabilmente sarà per visualizzare le operazioni seguenti nella risposta (che indica che si sono verificati alcuna registrazione trovati per recapitare la notifica, probabilmente perché le registrazioni aveva alcuni tag non corrispondenti)

        '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'

### <a name="broadcast-toast-notification-to-windows"></a>Trasmissione di notifica di tipo avviso popup per Windows 

Notare le intestazioni di ottenere mandate quando si invia una notifica di tipo avviso popup trasmissione al client di Windows. 

    hub.send_windows_notification(wns_payload)

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Inviare notifica specificando un tag (o espressione contrassegno)

Si noti l'intestazione HTTP contrassegni che viene aggiunto alla richiesta HTTP (nell'esempio seguente, stiamo inviando la notifica solo alle registrazioni con payload "Sport")

    hub.send_windows_notification(wns_payload, "sports")

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Inviare notifica specificando più tag

Si noti come intestazione HTTP tag cambia quando vengono inviati più tag. 
    
    tags = {'sports', 'politics'}
    hub.send_windows_notification(wns_payload, tags)

![][4]

### <a name="templated-notification"></a>Notifica basato su modelli

Si noti che l'intestazione HTTP formato cambia e il corpo payload viene inviata come parte del corpo della richiesta HTTP:

**Sul lato client - modello registrato**

        var template =
                        @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
            
**Sul lato server - invio del payload**
        
        template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
        hub.send_template_notification(template_payload)

![][5]


## <a name="next-steps"></a>Passaggi successivi
In questo argomento è stato illustrato come creare un semplice client resto Python per hub di notifica. Da qui è possibile:

* Scaricare l'intero [campione spaziale Python resto], che contiene il codice precedente.
* Ulteriori informazioni su come hub di notifica tagging caratteristica nell' [esercitazione ultimi aggiornamenti]
* Ulteriori informazioni sulla caratteristica per i modelli di hub di notifica nell' [esercitazione localizzazione News]

<!-- URLs -->
[Esempio di Python resto spaziale]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Esercitazione introduttiva Get]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Interruzione dell'esercitazione di News]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Localizzazione esercitazione News]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
 
