<properties
   pageTitle="App logica contenuto digitare gestione | Microsoft Azure"
   description="Capire come App logica gestisce tipi di contenuto nella progettazione e runtime"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-content-type-handling"></a>Logica App contenuto digitare gestione

Esistono diversi tipi di contenuto che può passano un'App logica - inclusi JSON, XML, file flat e dati binari.  Tutti i tipi di contenuto sono supportati, alcune vengano interpretati a livello nativo tramite il motore di App logica mentre gli altri utenti possono richiedere il cast o le conversioni in base alle esigenze.  L'articolo seguente per descrivere come il motore gestisce diversi tipi di contenuto e come possono essere correttamente gestiti in base alle esigenze.

## <a name="content-type-header"></a>Intestazione di tipo di contenuto

Per avviare semplice, diamo un'occhiata due `Content-Types` che non è richiesta una conversione o cast da utilizzare in un'App di logica - `application/json` e `text/plain`.

### <a name="applicationjson"></a>Applicazione/json

Motore di flusso di lavoro si basa sul `Content-Type` intestazione da HTTP chiama per determinare la gestione appropriata.  Qualsiasi richiesta con il tipo di contenuto `application/json` verranno archiviati e gestiti come JSON oggetto.  Inoltre, è possibile analizzare contenuto JSON per impostazione predefinita senza il cast.  Pertanto una richiesta di intestazione del tipo di contenuto `application/json ` come il seguente:

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

in un flusso di lavoro con un'espressione come è possibile analizzare `@body('myAction')['foo'][0]` per ottenere un valore (in questo caso, `bar`).  Non è necessario alcun cast aggiuntive.  Se si lavora con i dati JSON, ma non si sono presente un'intestazione specificata, è possibile manualmente eseguirne il cast all'uso di JSON il `@json()` funzione (ad esempio: `@json(triggerBody())['foo']`).

### <a name="textplain"></a>Testo

Simile a `application/json`, messaggi HTTP ricevuti con il `Content-Type` intestazione `text/plain` verranno archiviati in formato non elaborato è.  Inoltre, se incluso in delle azioni successive senza il cast la richiesta verrà inviato anche con un `Content-Type`: `text/plain` intestazione.  Ad esempio, se l'utilizzo di un file flat venga visualizzato il contenuto HTTP seguente:

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

as `text/plain`.  Se nell'azione successiva è stato inviato come corpo della convocazione un'altra (`@body('flatfile')`), la richiesta avrebbe un `text/plain` intestazione tipo di contenuto.  Se si lavora con dati di testo normale, ma non si sono presente un'intestazione specificata, è possibile impostarli manualmente all'utilizzo di testo il `@string()` funzione (ad esempio: `@string(triggerBody())`)

### <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Applicazione/xml e Application/octet-stream e le funzioni di conversione

Motore di App logica sempre manterrà o meno la `Content-Type` che è stato ricevuto sulla richiesta HTTP risposta.  Che cosa significa che è se un contenuto verrà ricevuto con `Content-Type` di `application/octet-stream`, incluso quello di un'azione successive con senza cast comporterà una richiesta in uscita con `Content-Type`: `application/octet-stream`.  In questo modo il motore possono guaruntee dati non saranno persi durante lo spostamento all'interno del flusso di lavoro.  Tuttavia, lo stato di azione (ingressi e uscite) sono archiviate in un oggetto JSON come scorra in tutto il flusso di lavoro.  Ciò significa per mantenere alcuni tipi di dati, il motore verrà convertire il contenuto in una stringa di base 64 binario codificato con metadati appropriati che consente di mantenere entrambe `$content` e `$content-type` -che vengono convertiti automaticamente.  È possibile convertire anche manualmente tra tipi di contenuto tramite integrato in funzioni convertitore:

* `@json()`-viene eseguito il cast dati`application/json`
* `@xml()`-viene eseguito il cast dati`application/xml`
* `@binary()`-viene eseguito il cast dati`application/octet-stream`
* `@string()`-viene eseguito il cast dati`text/plain`
* `@base64()`-Converte i contenuti in una stringa di base 64
* `@base64toString()`-Converte una stringa con codificata base 64 in`text/plain`
* `@base64toBinary()`-Converte una stringa con codificata base 64 in`application/octet-stream`
* `@encodeDataUri()`-codifica una stringa sotto forma di matrice byte dataUri
* `@decodeDataUri()`-Consente di decodificare un dataUri in una matrice di byte

Ad esempio, se è stato ricevuto una richiesta HTTP con `Content-Type`: `application/xml` di:

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

È possibile eseguirne il cast e usare in un secondo momento con simile al `@xml(triggerBody())`, o all'interno di una funzione come `@xpath(xml(triggerBody()), '/CustomerName')`.

### <a name="other-content-types"></a>Tipi di altro contenuto

Altri tipi di contenuto supportati e siano compatibili con un'App di logica, ma può richiedere manualmente il recupero del corpo del messaggio decodificando la `$content`.  Ad esempio, se crittografate attivazione disattivazione di una `application/x-www-url-formencoded` richiesta simile a quanto segue:

```
CustomerName=Frank&Address=123+Avenue
```

Poiché non testo normale o JSON verrà archiviato nell'azione come:

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

In `$content` è payload codificato come stringa base 64 per mantenere tutti i dati.  Poiché attualmente non è presente una funzione nativa per dati del modulo, non è ancora possibile utilizzare questi dati all'interno di un flusso di lavoro accedendo manualmente i dati con una funzione come `@string(body('formdataAction'))`.  Se desidera che la richiesta in uscita a anche la `application/x-www-url-formencoded` intestazione tipo di contenuto, si potrebbe semplicemente aggiungere questo nel corpo di azione senza il cast come `@body('formdataAction')`.  Tuttavia, questo funziona solo se il parametro solo nel corpo di `body` input.  Se si tenta di eseguire `@body('formdataAction')` all'interno di un `application/json` richiesta verrà visualizzato un errore di runtime come verrà inviato il corpo codificato.
