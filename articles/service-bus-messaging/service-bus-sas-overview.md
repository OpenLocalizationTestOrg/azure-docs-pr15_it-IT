<properties
    pageTitle="Condivise sulle firme accesso | Microsoft Azure"
    description="Quali sono condivisi firme di Access, come funzionano e come usarle da nodo PHP e c#."
    services="service-bus"
    documentationCenter="na"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/02/2016"
    ms.author="darosa;sethm"/>

# <a name="shared-access-signatures"></a>Accesso condiviso firme

*Condividere le firme di Access* (SA) è il meccanismo di protezione principali per Bus di servizio, inclusi evento hub, negoziate messaggistica (code e argomenti) e inoltro dei messaggi. In questo articolo vengono illustrati firme accesso condiviso, come funzionano e come utilizzarli in modo indipendente dalla piattaforma.

## <a name="overview-of-sas"></a>Panoramica delle associazioni di protezione

Le firme di Access condiviso sono un meccanismo di autenticazione basato su hash sicuro SHA-256 o URI. SA indica un meccanismo molto potente che viene usato da tutti i servizi di Bus di servizio. In realtà, SA ha due componenti: un *Criterio di accesso condiviso* e una *Firma di Access condiviso* (spesso denominate *token*).

Sono disponibili ulteriori informazioni sulle firme di Access condiviso con Bus di servizio di [autenticazione firma accesso condiviso con Bus di servizio](service-bus-shared-access-signature-authentication.md).

## <a name="shared-access-policy"></a>Criterio di accesso condivise

È importante conoscere sa è tutto ha inizio con un criterio. Per ogni criterio, decidere in tre tipi di informazioni: **nome**, **ambito**e **autorizzazioni**. Il **nome** è sufficiente; un nome univoco all'interno di esso. L'ambito è difficile: è l'URI della risorsa in questione. Spazio dei nomi Bus di servizio, l'ambito è il nome di dominio completo (FQDN), ad esempio `https://<yournamespace>.servicebus.windows.net/`.

Le autorizzazioni disponibili per un criterio sono molto facile comprensione:

  + Invia
  + Ascolto
  + Gestire

Dopo aver creato il criterio, verrà assegnata una *Chiave primaria* e *Chiave secondaria*. Si tratta dei tasti di crittografia avanzati. Non andranno perse o una perdita di loro - sempre sarà disponibile nel [portale di Azure][]. È possibile utilizzare uno dei tasti generati e sarà possibile rigenerare loro in qualsiasi momento. Tuttavia, se rigenerare oppure modificare la chiave primaria nei criteri di verranno invalidate le firme di accesso condivise create da essa.

Quando si crea un spazio dei nomi del servizio Bus, un criterio viene creato automaticamente per l'intero spazio dei nomi denominato **RootManageSharedAccessKey**e questo criterio ha tutte le autorizzazioni. Non accedere come **principale**, in modo che non usa questo criterio a meno che non esiste un motivo interessanti. È possibile creare ulteriori criteri nella scheda **configurazione** per lo spazio dei nomi nel portale. È importante nota che un solo livello nel servizio Bus della struttura (spazio dei nomi, coda, Hub di evento, e così via) può contenere solo fino a 12 criteri allegati.

## <a name="shared-access-signature-token"></a>Firma di Access condiviso (token)

Il criterio non è il token di accesso per Bus di servizio. È l'oggetto da cui viene generato il token di accesso - utilizzando la chiave primaria o secondaria. Il token viene generato con attenzione creando una stringa nel formato seguente:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Dove `signature-string` è hash SHA-256 dell'ambito del token (**ambito** come descritto nella sezione precedente) con un CRLF aggiunto e un'ora di scadenza (in secondi dopo il periodo: `00:00:00 UTC` del 1 ° gennaio 1970).

L'hash sarà simile alla seguente pseudo codice e restituisce 32 byte.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

I valori non hashing sono nella stringa di **SharedAccessSignature** in modo che il destinatario è possibile calcolare l'hash con gli stessi parametri, per assicurarsi che restituisca lo stesso risultato. URI specifica l'ambito e il nome della chiave identifica i criteri da utilizzare per calcolare l'hash. È importante dal punto di vista della protezione. Se la firma non corrisponde a quello che calcola il destinatario (servizio Bus), quindi accesso negato. A questo punto è possibile assicurarsi che il mittente ha accesso alla chiave e concedere i diritti specificati nei criteri di.

## <a name="generating-a-signature-from-a-policy"></a>Generazione di una firma da un criterio

Come si effettivamente procedere nel codice? Esaminiamo alcuni di questi piani.

### <a name="nodejs"></a>NodeJS

```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### <a name="java"></a>Java

```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### <a name="php"></a>PHP

```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time();  
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256',             
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
return $token; 
}
```
 
### <a name="c35"></a>C & #35;

```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="using-the-shared-access-signature-at-http-level"></a>Utilizzando la firma di Access condiviso (a livello HTTP)
 
Dopo aver scoperto come creare firme di Access condiviso per qualsiasi entità Bus di servizio, si è pronti eseguire un POST HTTP:

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
    
Tenere presente che funziona con tutti gli elementi. È possibile creare associazioni di protezione di coda, argomento, abbonamento, evento Hub o inoltro. Se si usa identità per publisher per evento hub, è sufficiente aggiungere `/publishers/< publisherid>`.

Se si assegna un mittente o un client un token SA, non hanno la chiave direttamente e non può essere annullata hash per ottenerlo. Di conseguenza, è possibile controllare su cosa possono accedere e come tempo. È importante ricordare è che se si cambia la chiave primaria del criterio, verranno invalidate ricavare eventuali firme di Access condiviso.

## <a name="using-the-shared-access-signature-at-amqp-level"></a>Utilizzando la firma di Access condiviso (a livello AMQP)

Nella sezione precedente è stato illustrato come utilizzare il token sa con una richiesta HTTP POST per inviare dati a Bus di servizio. Come descritto in precedenza, è possibile accedere tramite l'avanzate messaggio Accodamento Protocol (AMQP) che il protocollo preferito da usare per motivi di prestazioni, in molti scenari Bus di servizio. L'utilizzo di token sa con AMQP descritto nel documento [AMQP Claim-Based Security versione 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) è in uso bozza dal 2013 ma ben supportata da Azure oggi.

Prima di iniziare a inviare dati a Bus di servizio, l'autore deve inviare token SA all'interno di un messaggio AMQP a un nodo AMQP ben denominato **$cbs** (è possibile visualizzarlo come coda "speciale" usata dal servizio per acquisire e convalidare tutti i token SA). Impostazioni di pubblicazione devono specificare il campo **Rispondi** all'interno del messaggio AMQP; si tratta del nodo in cui il servizio di risposta da inviare al server di pubblicazione con il risultato della convalida token (un modello semplice richiesta/risposta tra publisher e servizio). Viene creato il nodo di risposta "in tempo reale," relatore sulle "creazione dinamica di nodo remoto", come descritto nella specifica AMQP 1.0. Dopo aver verificato che il token sa è valido, l'autore può andare avanti e iniziare a inviare dati al servizio.

La procedura seguente viene illustrato come inviare il token sa con protocollo AMQP utilizzando la libreria [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) . Questo è utile se non è possibile utilizzare il Service Bus SDK ufficiale (ad esempio su WinRT, .net Framework compatta, .net Framework Micro e Mono) sviluppo c\#. Naturalmente, questa raccolta è utile per comprendere come basata sulle attestazioni sicurezza funziona a livello di AMQP, come illustrato il funzionamento al livello HTTP (con una richiesta HTTP POST e token SA inviato all'interno dell'intestazione "Autorizzazione"). Se non sono necessarie queste conoscenze complete sulle AMQP, è possibile utilizzare il Service Bus SDK ufficiale con .net applicazioni Framework, che vengono eseguite automaticamente.

### <a name="c35"></a>C & #35;

```
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

Il `PutCbsToken()` metodo riceve la *connessione* (istanza di classe AMQP connessione come fornito dalla [raccolta AMQP .NET Lite](https://github.com/Azure/amqpnetlite)) che rappresenta la connessione TCP al servizio e il parametro *sasToken* che token sa da inviare. 

> [AZURE.NOTE] È importante che la connessione viene creata con **meccanismo di autenticazione SASL impostato su esterno** (e non il normale predefinito con il nome utente e la password usata quando non è necessario inviare il token SA).

Successivamente, l'autore crea due collegamenti AMQP per l'invio di token SA e la ricezione di risposta (il risultato di convalida token) dal servizio.

Il messaggio AMQP contiene un insieme di proprietà e altre informazioni a un messaggio semplice. Token sa è il corpo del messaggio (con il relativo costruttore). **"Rispondi"** è impostata su nome del nodo per ricevere il risultato di convalida sul collegamento ricevitore (è possibile cambiarne il nome se si vuole e verrà creato in modo dinamico dal servizio). Le ultime tre proprietà applicazione/personalizzati vengono utilizzate dal servizio per indicare il tipo di operazione è disponibile per l'esecuzione. Come descritto dalla specifica bozza convertibili, devono essere il **nome dell'operazione** ("inserire-token"), il **tipo di token** (in questo caso, "servicebus.windows.net:sastoken") e **"nome" del gruppo di destinatari** a cui il token viene applicata (intera entità).

Dopo l'invio di token SA sul collegamento mittente, è necessario che l'autore lettura la risposta sul collegamento ricevitore. La risposta è un messaggio AMQP semplice con una proprietà dell'applicazione denominato **"codice di stato"** che può contenere gli stessi valori di un codice di stato HTTP. 

## <a name="next-steps"></a>Passaggi successivi

Vedere [Guida di riferimento all'API REST Bus di servizio](https://msdn.microsoft.com/library/azure/hh780717.aspx) per ulteriori informazioni sulle operazioni eseguibili con questi token SA.

Per ulteriori informazioni sull'autenticazione Bus di servizio, vedere [Bus di servizio di autenticazione e l'autorizzazione](service-bus-authentication-and-authorization.md). 

Ulteriori esempi delle associazioni di protezione in c# e JavaScript disponibili in [questo post di blog](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx).

[Portale di Azure]: https://portal.azure.com