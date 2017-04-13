<properties
    pageTitle="Accedere Analitica dati HTTP Raccoglitore API | Microsoft Azure"
    description="Per aggiungere dati JSON POST all'archivio Log Analitica da qualsiasi client che possono chiamare API REST, è possibile utilizzare l'API di raccolta dati di registro Analitica HTTP. In questo articolo viene descritto come utilizzare l'API e è esempi su come pubblicare i dati utilizzando diversi linguaggi di programmazione."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="bwren"/>


# <a name="log-analytics-http-data-collector-api"></a>Registro Analitica dati HTTP Raccoglitore API

Quando si utilizza l'API di Raccoglitore Azure Log Analitica HTTP dati, è possibile aggiungere dati POST JavaScript Object Notation (JSON) all'archivio Log Analitica da qualsiasi client che possono chiamare API REST. Con questo metodo, è possibile inviare i dati dalle applicazioni di terze parti o dagli script, ad esempio dal runbook di automazione di Azure.  

## <a name="create-a-request"></a>Creare una richiesta

Le due tabelle elencano gli attributi necessari per ogni richiesta all'API Raccoglitore Log Analitica HTTP dati. Vengono descritte ogni attributo dettagliatamente più avanti in questo articolo.

### <a name="request-uri"></a>URI richiesta

| Attributo | Proprietà |
|:--|:--|
| Metodo | Inserisci |
| URI | https://\<IDCliente\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Tipo di contenuto | applicazione/json |

### <a name="request-uri-parameters"></a>Parametri URI richiesta
| Parametro | Descrizione |
|:--|:--|
| ID cliente  | Identificatore univoco per l'area di lavoro di Microsoft operazioni Management Suite. |
| Risorsa    | Il nome della risorsa API: api/log. |
| Versione API | La versione dell'API da utilizzare con la richiesta. Attualmente è 2016-04-01. |

### <a name="request-headers"></a>Le intestazioni di richiesta
| Intestazione | Descrizione |
|:--|:--|
| Autorizzazione | La firma di autorizzazione. In un secondo momento nell'articolo, è possibile leggere informazioni sulla creazione di un'intestazione SHA256 HMAC. |
| Tipo di log | Specificare il tipo di record di dati che viene inviati. Attualmente, il tipo di registro supporta solo caratteri alfabetici. Non supporta valori numerici o caratteri speciali. |
| Data di ms x | Data in cui è stata elaborata la richiesta, in formato RFC 1123. |
| campo generate ora | Il nome di un campo dati che contiene il timestamp dell'elemento di dati. Se si specifica un campo il relativo contenuto vengono utilizzato per **TimeGenerated**. Se questo campo viene omesso, il valore predefinito per **TimeGenerated** è l'ora in cui verrà acquisito il messaggio. Il contenuto del campo messaggio deve seguire il formato ISO 8601 aaaa-MM-DDThh:mm:ssZ. |


## <a name="authorization"></a>Autorizzazione

Qualsiasi richiesta all'API Raccoglitore Log Analitica HTTP dati deve includere un'intestazione di autorizzazione. Per eseguire l'autenticazione di una richiesta, è necessario accedere la richiesta con primario o la chiave secondaria per l'area di lavoro che effettua la richiesta. Passare quindi alla firma come parte della richiesta.   

Ecco il formato per l'intestazione di autorizzazione:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* è l'identificatore univoco per l'area di lavoro di operazioni Management Suite. *La firma* è [Basato su Hash messaggio autenticazione codice HMAC ()](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) che è impostata dalla richiesta e quindi calcolato utilizzando l' [algoritmo SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Quindi è codificare utilizzando la codifica base 64.

Utilizzare questo formato codificare stringa **SharedKey** firma:

```
StringToSign = VERB + "\n" +
               Content-Length + "\n" +
               Content-Type + "\n" +
               x-ms-date + "\n" +
               "/api/logs";
```

Ecco un esempio di una stringa di firma:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Dopo aver la stringa di firma, codificare utilizzando l'algoritmo HMAC SHA256 stringa con codifica UTF-8 e quindi codificare il risultato come base 64. Utilizzare questo formato:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Gli esempi nelle sezioni successive avere il codice di esempio per creare un'intestazione di autorizzazione.

## <a name="request-body"></a>Corpo della richiesta

Il corpo del messaggio deve essere in JSON. In questo formato deve contenere uno o più record con le coppie di nome e il valore di proprietà:

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

È possibile batch più record in una singola richiesta utilizzando il formato seguente. Tutti i record devono essere dello stesso tipo di record.

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
},
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

## <a name="record-type-and-properties"></a>Proprietà e tipo di record

Per definire un tipo di record personalizzati quando si inviano dati tramite l'API di raccolta dati di registro Analitica HTTP. Al momento non è possibile scrivere dati in tipi di record esistenti che sono stati creati altri tipi di dati e le soluzioni. Registro Analitica legge i dati in arrivo e quindi crea proprietà che corrispondono ai tipi di dati dei valori immessi dall'utente.

Ogni richiesta all'API Analitica Log deve includere un'intestazione di **Tipo di registro** con il nome del tipo di record. Il suffisso **_CL** viene aggiunto automaticamente il nome immesso per distinguerlo dagli altri tipi di log come un file di log personalizzato. Ad esempio, se si immette il nome **MyNewRecordType**, Log Analitica crea un record con il tipo **MyNewRecordType_CL**. In questo modo che non sono presenti conflitti tra i nomi di tipo creato dall'utente e quelli spediti soluzioni Microsoft corrente o futuro.

Per identificare il tipo di dati di una proprietà, Log Analitica aggiunge un suffisso al nome della proprietà. Se una proprietà contiene un valore null, la proprietà non è incluso in tale record. Questa tabella sono elencate le proprietà tipo di dati e un suffisso corrispondente:

| Tipo di dati di proprietà | Suffisso |
|:--|:--|
| Stringa    | s |
| Valore booleano   | b |
| Effettuare un doppio    | d |
| Data/ora | t |
| GUID      | _g |


Il tipo di dati utilizzato Analitica Log per ogni proprietà dipende dal fatto che esista il tipo di record per il nuovo record.

- Se il tipo di record non esiste, Log Analitica creata una nuova. Registro Analitica utilizza inferenza JSON per determinare il tipo di dati per ogni proprietà per il nuovo record.
- Se il tipo di record esiste, Analitica Log tenta di creare un nuovo record in base alle proprietà esistenti. Se il tipo di dati per una proprietà del nuovo record non corrisponda e non può essere convertita in tipo esistente o se il record include una proprietà che non esiste, Log Analitica crea una nuova proprietà che contiene il suffisso pertinenti.

Ad esempio, questa voce di presentazione da creare un record con tre proprietà, **number_d**, **boolean_b**e **string_s**:

![Record di esempio 1](media/log-analytics-data-collector-api/record-01.png)

Se questa voce successiva, quindi invio con tutti i valori formattati come stringhe, le proprietà non da modificare. Questi valori possono essere convertiti in tipi di dati esistenti:

![Record di esempio 2](media/log-analytics-data-collector-api/record-02.png)

Tuttavia, se è stata quindi questo invio successivo, Analitica Log è necessario creare la nuova proprietà **boolean_d** e **string_d**. Non è possibile convertire questi valori:

![Record di esempio 3](media/log-analytics-data-collector-api/record-03.png)

Se è stata inviata quindi la voce seguente, prima che il tipo di record è stato creato, Analitica Log è necessario creare un record con tre proprietà **num_successi**, **boolean_s**e **string_s**. In questa voce, ognuno dei valori iniziali verrà formattato come stringa:

![Record di esempio 4](media/log-analytics-data-collector-api/record-04.png)


## <a name="data-limits"></a>Limiti sui dati
Esistono alcune limitazioni attorno i dati inviati per la raccolta di dati Analitica Log API.

- Numero massimo di 30 MB per ogni post all'API di raccolta dati Analitica Log. Si tratta di un limite di dimensioni per un messaggio. Se i dati da un'unica post che supera i 30 MB, è consigliabile dividere i dati fino a blocchi di dimensioni inferiori e inviarli contemporaneamente. 
- Massimo del limite di 32 KB per i valori dei campi. Se il valore del campo è maggiore di 32 KB, i dati vengano troncati. 
- Numero massimo di campi per un determinato tipo consigliato è 50. Si tratta di un limite pratico da una facilità di utilizzo e la prospettiva di esperienza di ricerca.  


## <a name="return-codes"></a>Codici restituiti

Il codice di stato HTTP 202 indica che la richiesta sia stata accettata per l'elaborazione elaborazione non ancora completato. Indica che l'operazione completata.

Questa tabella sono elencate l'insieme completo dei codici di stato che potrebbe fornire il servizio:

| Codice | Stato | Codice di errore | Descrizione |
|:--|:--|:--|:--|
| 202 | Accettato dall'utente |  | La convocazione è stata accettata. |
| 400 | Richiesta non valida | InactiveCustomer | L'area di lavoro è stato chiuso. |
| 400 | Richiesta non valida | InvalidApiVersion | La versione dell'API specificata non riconosciuta dal servizio. |
| 400 | Richiesta non valida | InvalidCustomerId | L'ID dell'area di lavoro specificato non è valido. |
| 400 | Richiesta non valida | InvalidDataFormat | È stato inviato JSON non validi. Corpo della risposta può contenere ulteriori informazioni su come risolvere l'errore. |
| 400 | Richiesta non valida | InvalidLogType | Il tipo di log specificato valori numerici o caratteri speciali contenuti. |
| 400 | Richiesta non valida | MissingApiVersion | La versione dell'API non è stata specificata. |
| 400 | Richiesta non valida | MissingContentType | Il tipo di contenuto non è stato specificato. |
| 400 | Richiesta non valida | MissingLogType | Il tipo di registro valore obbligatorio non è stato specificato. |
| 400 | Richiesta non valida | UnsupportedContentType | Il tipo di contenuto non è stato impostato su **application/json**. |
| 403 | Accesso negato | InvalidAuthorization | Il servizio non è possibile autenticare la richiesta. Verificare che la chiave ID e la connessione dell'area di lavoro siano validi. |
| 500 | Errore interno del Server | UnspecifiedError | Il servizio ha rilevato un errore interno. Ripetere la richiesta. |
| 503 | Servizio non disponibile | ServiceUnavailable | Il servizio non è attualmente disponibile per ricevere le richieste. Ripetere la richiesta. |

## <a name="query-data"></a>Eseguire query sui dati

Per ricercare i dati inviati tramite l'API di raccolta dati di registro Analitica HTTP, cercare i record con **tipo** che è uguale al valore **LogType** che si specificato, aggiunte con **_CL**. Ad esempio, se è stata utilizzata **MyCustomLog**, è necessario restituire tutti i record con **tipo = MyCustomLog_CL**.


## <a name="sample-requests"></a>Richieste di esempio

Nelle sezioni successive sono disponibili esempi su come inviare dati all'API Raccoglitore Log Analitica HTTP dati utilizzando diversi linguaggi di programmazione.

Per ogni campione, eseguire le operazioni seguenti per impostare le variabili per l'intestazione di autorizzazione:

1. Nel portale di operazioni Management Suite, selezionare il riquadro **Impostazioni** e quindi selezionare la scheda **Origini connesse** .
2. A destra **dell'ID dell'area di lavoro**, selezionare l'icona di copia e quindi incollare l'ID come valore della variabile **ID cliente** .
3. A destra della **Chiave primaria**, selezionare l'icona di copia e quindi incollare l'ID come valore della variabile **Chiave condivisa** .

In alternativa, è possibile modificare le variabili per il tipo di log e dati JSON.

### <a name="powershell-sample"></a>Esempio di PowerShell

```
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# Specify a field with the created time for the records
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-OMSData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -fileName $fileName `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-OMSData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>Esempio di c#

```
using System;
using System.Net;
using System.Security.Cryptography;

namespace OIAPIExample
{
    class ApiExample
    {
// An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField1"":""DemoValue3"",""DemoField2"":""DemoValue4""}]";

// Update customerId to your Operations Management Suite workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

// For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

// LogName is name of the event type that is being submitted to Log Analytics
        static string LogName = "DemoExample";

// You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
// Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            string stringToHash = "POST\n" + json.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

// Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

// Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            string url = "https://"+ customerId +".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";
            using (var client = new WebClient())
            {
                client.Headers.Add(HttpRequestHeader.ContentType, "application/json");
                client.Headers.Add("Log-Type", LogName);
                client.Headers.Add("Authorization", signature);
                client.Headers.Add("x-ms-date", date);
                client.Headers.Add("time-generated-field", TimeStampField);
                client.UploadString(new Uri(url), "POST", json);
            }
        }
    }
}
```

### <a name="python-sample"></a>Esempio Python

```
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Operations Management Suite workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code == 202):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

## <a name="next-steps"></a>Passaggi successivi

- Utilizzare [Progettazione](log-analytics-view-designer.md) per creare visualizzazioni personalizzate ai dati che inviano.
