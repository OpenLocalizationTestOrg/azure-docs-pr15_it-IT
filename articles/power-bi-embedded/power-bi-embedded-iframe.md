<properties
   pageTitle="Come usare Power BI incorporata con resto | Microsoft Azure"
   description="Informazioni su come usare Power BI incorporata con altri "
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="how-to-use-power-bi-embedded-with-rest"></a>Come usare Power BI incorporata con altri


## <a name="power-bi-embedded-what-it-is-and-what-its-for"></a>Power BI incorporati: Che cos'è e novità per
Una panoramica di Power BI incorporata descritto in ufficiale [sito Power BI incorporato](https://azure.microsoft.com/services/power-bi-embedded/), ma diamo un'occhiata veloce prima di passare a informazioni dettagliate sull'uso con altri.

È semplice, realmente. Un ISV spesso desidera utilizzare le visualizzazioni dati dinamici di [Power BI](https://powerbi.microsoft.com) nella rispettiva applicazione come blocchi predefiniti dell'interfaccia utente.

Ma si conosce, incorporamento di un report di Power BI o riquadri nella pagina web è già possibile senza servizio Power BI incorporata Azure tramite l' **API di Power BI**. Quando si desidera condividere i report nell'organizzazione stessa, è possibile incorporare i report con l'autenticazione di Azure Active Directory. L'utente che visualizza i report deve accedere usando il proprio account Azure Active Directory. Quando si desidera condividere i report per tutti gli utenti (inclusi gli utenti esterni), è possibile incorporare semplicemente con l'accesso anonimo.

Ma viene visualizzato, questo semplice incorporare soluzione non è abbastanza soddisfare le esigenze di un'applicazione ISV.
La maggior parte delle applicazioni ISV necessario fornire i dati per i propri clienti, non necessariamente utenti nella propria organizzazione. Ad esempio, se l'esecuzione alcuni servizio per la società e la società B, gli utenti nella società devono solo vedere dati per le proprie società A. Vale a dire affitto multiple è necessaria per il recapito.

L'applicazione ISV potrebbe offrendo anche i propri metodi di autenticazione, ad esempio auth maschere, autenticazione di base e così via Quindi, la soluzione incorporamento dovrà collaborare in modo sicuro con questo metodo di autenticazione esistente. È anche necessario agli utenti di essere in grado di utilizzare queste applicazioni ISV senza acquisto aggiuntiva o licenza di un abbonamento di Power BI.

 **Power BI incorporata** è progettato per con precisione questi tipi di scenari ISV. Così ora che è stata creata in modo tale breve introduzione, iniziamo in dettaglio

È possibile utilizzare .NET \(c#) o SDK Node, di creare facilmente l'applicazione con Power BI incorporato. Ma in questo articolo spiegheremo sul flusso HTTP \(inclusa uso) di Power BI senza SDK. Flusso, è possibile creare l'applicazione **con qualsiasi linguaggio di programmazione**ed è possibile comprendere eccessivamente all'essenza di Power BI incorporato.

## <a name="create-power-bi-workspace-collection-and-get-access-key-provisioning"></a>Raccolta di un'area di lavoro creare Power BI e tasto di scelta rapida get \(Provisioning)
Power BI incorporata corrisponde a uno dei servizi Azure. Solo l'ISV che utilizza portale Azure vengono addebitate commissioni di utilizzo \(per ogni ora sessione utente), e l'utente che visualizza il report non viene addebitata o persino richiede una sottoscrizione a Azure.
Prima di avviare lo sviluppo di applicazioni, è necessario creare la **raccolta di un'area di lavoro di Power BI** tramite il portale di Azure.

Ogni area di lavoro di Power BI incorporata è l'area di lavoro per ogni cliente (tenant) e sarà possibile aggiungere molte aree di lavoro nella raccolta ogni area di lavoro. Ogni raccolta dell'area di lavoro viene utilizzata la stessa chiave di accesso. Effetto, l'insieme di un'area di lavoro è il limite di sicurezza per Power BI incorporato.

![](media\power-bi-embedded-iframe\create-workspace.png)

Dopo il completamento di creare la raccolta di un'area di lavoro, copiare il tasto di scelta dal portale di Azure.

![](media\power-bi-embedded-iframe\copy-access-key.png)

> [AZURE.NOTE] È possibile anche eseguire il provisioning della raccolta di un'area di lavoro e ottenere tasto di scelta rapida tramite API REST. Per ulteriori informazioni, vedere [Power BI risorse Provider API](https://msdn.microsoft.com/library/azure/mt712306.aspx).

## <a name="create-pbix-file-with-power-bi-desktop"></a>Creare file .pbix con Power BI Desktop
Successivamente, è necessario creare la connessione dati e report da incorporare.
Per questa attività è programmazione o codice. Utilizzare semplicemente Power BI Desktop.
In questo articolo non attraversano i dettagli su come usare Power BI Desktop. Se assistenza qui, vedere [Guida introduttiva a Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/). In questo esempio, si userà solo l' [Esempio di analisi di vendita al dettaglio](https://powerbi.microsoft.com/documentation/powerbi-sample-datasets/).

![](media\power-bi-embedded-iframe\power-bi-desktop-1.png)

## <a name="create-a-power-bi-workspace"></a>Creare un'area di lavoro di Power BI

Ora che il provisioning è tutto, entriamo nel mondo creazione dell'area di lavoro di un cliente nella raccolta di un'area di lavoro tramite API REST. Le seguenti HTTP POST richiedere (REST) sta creando la nuova area di lavoro dell'insieme di un'area di lavoro esistente. In questo esempio, il nome della raccolta dell'area di lavoro è **mypbiapp**.
Il tasto di scelta, vengono copiati in precedenza, è sufficiente impostare come **AppKey**. È molto semplice autenticazione!

**Richiesta HTTP**

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces
Authorization: AppKey MpaUgrTv5e...
```

**Risposta HTTP**

```
HTTP/1.1 201 Created
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces
RequestId: 4220d385-2fb3-406b-8901-4ebe11a5f6da

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/$metadata#workspaces/$entity",
  "workspaceId": "32960a09-6366-4208-a8bb-9e0678cdbb9d",
  "workspaceCollectionName": "mypbiapp"
}
```

Restituito **workspaceId** viene utilizzato per le chiamate all'API successive seguenti. L'applicazione è necessario mantenere questo valore.

## <a name="import-pbix-file-into-the-workspace"></a>Importare file di .pbix nell'area di lavoro
Ogni area di lavoro è possibile ospitare un singolo file di Power BI Desktop con un set di dati \(incluse le impostazioni dell'origine dati) e nei report. È possibile importare il file .pbix nell'area di lavoro come nell'esempio seguente. Come si può notare, è possibile caricare file binario di file .pbix mediante MIME multipart http.

Query parametro **datasetDisplayName** è il nome di set di dati per creare il frammento uri **32960a09-6366-4208-a8bb-9e0678cdbb9d** è il workspaceId. Il set di dati creato contiene tutti i dati relativi elementi in .pbix file ad esempio come i dati importati, il puntatore all'origine dati e così via...

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports?datasetDisplayName=mydataset01
Authorization: AppKey MpaUgrTv5e...
Content-Type: multipart/form-data; boundary="A300testx"

--A300testx
Content-Disposition: form-data

{the content (binary) of .pbix file}
--A300testx--
```

Questa operazione di importazione può eseguire per un po' di tempo. Al termine, l'applicazione può richiedere lo stato dell'attività utilizzando l'id di importazione. In questo esempio, l'id di importazione è **4eec64dd-533b-47c3-a72c-6508ad854659**.

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659?tenantId=myorg
RequestId: 658bd6b4-b68d-4ec3-8818-2a94266dc220

{"id":"4eec64dd-533b-47c3-a72c-6508ad854659"}
```

Di seguito richiede stato con l'id di importazione:

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659
Authorization: AppKey MpaUgrTv5e...
```

Se l'attività non viene completata, la risposta HTTP potrebbe essere come segue:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: 614a13a5-4de7-43e8-83c9-9cd225535136

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Publishing",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "name": "mydataset01"
}
```

Se l'attività è completata, la risposta HTTP potrebbe essere analoga alla seguente:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: eb2c5a85-4d7d-4cc2-b0aa-0bafee4b1606

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Succeeded",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "reports": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://app.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c"
    }
  ],
  "datasets": [
    {
      "id": "458e0451-7215-4029-80b3-9627bf3417b0",
      "name": "mydataset01",
      "tables": [
      ],
      "webUrl": "https://app.powerbi.com/datasets/458e0451-7215-4029-80b3-9627bf3417b0"
    }
  ],
  "name": "mydataset01"
}
```

## <a name="data-source-connectivity-and-multi-tenancy-of-data"></a>Connettività per le origini dati \(e più affitto dei dati)
Mentre quasi tutti gli elementi nel file .pbix importati l'area di lavoro, le credenziali per le origini dati non sono. Di conseguenza, quando si usa **modalità DirectQuery**, il report incorporato non può essere visualizzato correttamente. Tuttavia, quando si usa **la modalità di importazione**, è possibile visualizzare il report utilizzando i dati importati esistenti. In questo caso, è necessario impostare le credenziali procedendo come segue mediante le chiamate resto.

Prima di tutto, è necessario ottenere l'origine dati gateway. È possibile sapere **che l'id** di set di dati è l'id restituito precedentemente.

**Richiesta HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.GetBoundGatewayDatasources
Authorization: AppKey MpaUgrTv5e...
```

**Risposta HTTP**

```
GET HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: 574b0b18-a6fa-46a6-826c-e65840cf6e15

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#gatewayDatasources",
  "value": [
    {
      "id": "5f7ee2e7-4851-44a1-8b75-3eb01309d0ea",
      "gatewayId": "ca17e77f-1b51-429b-b059-6b3e3e9685d1",
      "datasourceType": "Sql",
      "connectionDetails": "{\"server\":\"testserver.database.windows.net\",\"database\":\"testdb01\"}"
    }
  ]
}
```

Utilizzo dell'id di gateway restituito e id datasource \(vedere il precedente **gatewayId** e l' **id** del risultato restituito), è possibile modificare le credenziali dell'origine dati, come indicato di seguito:

**Richiesta HTTP**

```
PATCH https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/gateways/ca17e77f-1b51-429b-b059-6b3e3e9685d1/datasources/5f7ee2e7-4851-44a1-8b75-3eb01309d0ea
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "credentialType": "Basic",
  "basicCredentials": {
    "username": "demouser",
    "password": "P@ssw0rd"
  }
}
```

**Risposta HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/octet-stream
RequestId: 0e533c13-266a-4a9d-8718-fdad90391099
```

Produzione, è anche possibile impostare la stringa di connessione diverso per ogni area di lavoro tramite l'API REST. \(ovvero è possibile separare il database per i clienti di ogni.)

Di seguito in fase di modifica la stringa di connessione dell'origine dati tramite resto.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.SetAllConnections
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "connectionString": "data source=testserver02.database.windows.net;initial catalog=testdb02;persist security info=True;encrypt=True;trustservercertificate=False"
}
```

Alternativa, è possibile utilizzare la protezione a livello riga in Power BI incorporato ed è possibile separare i dati per ogni utenti in un rapporto. As a Result, è possibile effettuare il provisioning di ogni report cliente con .pbix stesso \(dell'interfaccia utente, ecc.) origini dati diverse.

> [AZURE.NOTE] Se si usa **la modalità di importazione** anziché **modalità DirectQuery**, è possibile aggiornare i modelli mediante l'API. E origini dati locali tramite gateway di Power BI non sono ancora supportata in Power BI incorporato. Tuttavia, è necessario confermare la tenere sotto controllo il [blog di Power BI](https://powerbi.microsoft.com/blog/) per quali sono le novità e novità in futuro rilascia.

## <a name="authentication-and-hosting-embedding-reports-in-our-web-page"></a>L'autenticazione e ospita report (incorporamento) nella pagina web

In API REST precedente, è possibile utilizzare il tasto di scelta **AppKey** stesso come intestazione di autorizzazione. Perché le chiamate possono essere gestite sul lato server di back-end, è sicura.

Ma quando si incorpora il report nella pagina web, questo tipo di informazioni sulla protezione sarà gestito tramite JavaScript \(front-end). Quindi è necessario proteggere il valore dell'intestazione di autorizzazione. Se il tasto di scelta rapida è rilevato da un utente non autorizzato o codice dannoso, possono chiamare tutte le operazioni con questo tasto.

Quando si incorpora il report nella pagina web, è necessario utilizzare il token calcolato anziché un tasto di scelta rapida a **AppKey**. L'applicazione deve creare OAuth Json Web Token \(JWT) costituito da crediti e la firma digitale calcolata. Come illustrato di seguito, questo JWT OAuth è token delimitato da punto stringa codificata.

![](media\power-bi-embedded-iframe\oauth-jwt.png)

Prima di tutto, è necessario preparare il valore di input, è stato effettuato l'accesso in un secondo momento. Questo valore è stringa dell'url codificata (rfc4648) base 64 json seguenti e questi sono racchiusi tra il punto \(.) carattere. In seguito, viene illustrato come ottenere l'id di report.

> [AZURE.NOTE] Se si desidera utilizzare riga livello di sicurezza (RLS) con Power BI incorporato, è necessario specificare **il nome utente** e **ruoli** in crediti.

```
{
  "typ":"JWT",
  "alg":"HS256"
}
```

```
{
  "wid":"{workspace id}",
  "rid":"{report id}",
  "wcn":"{workspace collection name}",
  "iss":"PowerBISDK",
  "ver":"0.2.0",
  "aud":"https://analysis.windows.net/powerbi/api",
  "nbf":{start time of token expiration},
  "exp":{end time of token expiration}
}
```

Successivamente, è necessario creare la stringa con codificata base 64 di HMAC \(la firma) con SHA256 algoritmo. Questo valore di input con firmato è la stringa precedente.

Infine, è necessario combinare stringa di input valore e nella firma utilizzando periodo \(.) carattere. Stringa completata è il token di app per l'incorporamento di report. Anche se viene rilevato il token di app da utenti malintenzionati, che non è visualizzato un tasto di scelta rapida al originale. Questo token app scadrà rapidamente.

Ecco un esempio PHP per la procedura seguente:

```
<?php
// 1. power bi access key
$accesskey = "MpaUgrTv5e...";

// 2. construct input value
$token1 = "{" .
  "\"typ\":\"JWT\"," .
  "\"alg\":\"HS256\"" .
  "}";
$token2 = "{" .
  "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
  "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
  "\"wcn\":\"mypbiapp\"," . // workspace collection name
  "\"iss\":\"PowerBISDK\"," .
  "\"ver\":\"0.2.0\"," .
  "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
  "\"nbf\":" . date("U") . "," .
  "\"exp\":" . date("U" , strtotime("+1 hour")) .
  "}";
$inputval = rfc4648_base64_encode($token1) .
  "." .
  rfc4648_base64_encode($token2);

// 3. get encoded signature
$hash = hash_hmac("sha256",
    $inputval,
    $accesskey,
    true);
$sig = rfc4648_base64_encode($hash);

// 4. show result (which is the apptoken)
$apptoken = $inputval . "." . $sig;
echo($apptoken);

// helper functions
function rfc4648_base64_encode($arg) {
  $res = $arg;
  $res = base64_encode($res);
  $res = str_replace("/", "_", $res);
  $res = str_replace("+", "-", $res);
  $res = rtrim($res, "=");
  return $res;
}
?>
```

## <a name="finally-embed-the-report-into-the-web-page"></a>Infine, incorporare il report nella pagina web

Per incorporare il report, è necessario ottenere l'url di incorporamento e segnalare **id** tramite l'API REST seguenti.

**Richiesta HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/reports
Authorization: AppKey MpaUgrTv5e...
```

**Risposta HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: d4099022-405b-49d3-b3b7-3c60cf675958

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#reports",
  "value": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c",
      "isFromPbix": false
    }
  ]
}
```

È possibile incorporare il report nell'applicazione web usando il token app precedente.
Se si osserva il codice di esempio successivo, la parte ex è uguale a quella dell'esempio precedente. In quest'ultima parte, in questo esempio viene **embedUrl** \(visualizzare il risultato precedente) in iframe ed è registrazione token app in iframe.

> [AZURE.NOTE] È necessario modificare il valore di id report a uno dei propri. Inoltre, a causa di un bug nel sistema di gestione del contenuto dei tag iframe nel codice di esempio è di lettura in forma letterale. Rimuovere il testo delimitatore dal tag se copiare e incollare il codice di esempio.

```
    <?php
    // 1. power bi access key
    $accesskey = "MpaUgrTv5e...";

    // 2. construct input value
    $token1 = "{" .
      "\"typ\":\"JWT\"," .
      "\"alg\":\"HS256\"" .
      "}";
    $token2 = "{" .
      "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
      "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
      "\"wcn\":\"mypbiapp\"," . // workspace collection name
      "\"iss\":\"PowerBISDK\"," .
      "\"ver\":\"0.2.0\"," .
      "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
      "\"nbf\":" . date("U") . "," .
      "\"exp\":" . date("U" , strtotime("+1 hour")) .
      "}";
    $inputval = rfc4648_base64_encode($token1) .
      "." .
      rfc4648_base64_encode($token2);

    // 3. get encoded signature value
    $hash = hash_hmac("sha256",
        $inputval,
        $accesskey,
        true);
    $sig = rfc4648_base64_encode($hash);

    // 4. get apptoken
    $apptoken = $inputval . "." . $sig;

    // helper functions
    function rfc4648_base64_encode($arg) {
      $res = $arg;
      $res = base64_encode($res);
      $res = str_replace("/", "_", $res);
      $res = str_replace("+", "-", $res);
      $res = rtrim($res, "=");
      return $res;
    }
    ?>
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>Test page</title>
      <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    <body>
      <button id="btnView">View Report !</button>
      <div id="divView">
        <**REMOVE THIS CAPPED TEXT IF COPIED** iframe id="ifrTile" width="100%" height="400"></iframe>
      </div>
      <script>
        (function () {
          document.getElementById('btnView').onclick = function() {
            var iframe = document.getElementById('ifrTile');
            iframe.src = 'https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c';
            iframe.onload = function() {
              var msgJson = {
                action: "loadReport",
                accessToken: "<?=$apptoken?>",
                height: 500,
                width: 722
              };
              var msgTxt = JSON.stringify(msgJson);
              iframe.contentWindow.postMessage(msgTxt, "*");
            };
          };
        }());
      </script>
    </body>
```

Ed ecco il risultato:

![](media\power-bi-embedded-iframe\view-report.png)

In questa fase, Power BI incorporata solo Mostra il report in iframe. Tuttavia, tenere sotto controllo il [Blog di Power BI](). Miglioramenti futuri utilizzi lato client nuova API che verranno automatica inviare le informazioni in iframe, nonché informazioni. Oggetti interessanti!


## <a name="see-also"></a>Vedere anche
- [Autenticazione e l'autorizzazione in Power BI incorporata](power-bi-embedded-app-token-flow.md)
