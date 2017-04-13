<properties
    pageTitle="Configurazione e logica App limiti | Microsoft Azure"
    description="Panoramica dei limiti di servizio e dei valori di configurazione disponibili per le app logica."
    services="logic-apps"
    documentationCenter=".net,nodejs,java"
    authors="jeffhollan"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>

# <a name="logic-app-limits-and-configuration"></a>Logica App limiti e configurazione

Di seguito sono le informazioni sui limiti correnti e i dettagli di configurazione per Azure logica app.

## <a name="limits"></a>Limiti

### <a name="http-request-limits"></a>Limiti di richiesta HTTP

Questi sono limiti per una singola chiamata di richiesta e/o il connettore HTTP

#### <a name="timeout"></a>Timeout

|Nome|Limite|Note|
|----|----|----|
|Timeout della richiesta|1 minuto|Un [modello asincrono](app-service-logic-create-api-app.md) o [fino a ciclo continuo](app-service-logic-loops-and-scopes.md) può compensare in base alle esigenze|

#### <a name="message-size"></a>Dimensione dei messaggi

|Nome|Limite|Note|
|----|----|----|
|Dimensione dei messaggi|50 MB|Alcuni connettori e le API non supportino 50MB.  Richiesta di trigger supporta fino a 25MB|
|Limite di valutazione di espressioni|131.072 caratteri|`@concat()`, `@base64()`, `string` non può essere più lunga|

#### <a name="retry-policy"></a>Ripetere i criteri

|Nome|Limite|Note|
|----|----|----|
|Tentativi|4|Configurare con [Riprova parametro criteri](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Tentativi max|1 ora|Configurare con [Riprova parametro criteri](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Tentativi min|20 min|Configurare con [Riprova parametro criteri](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|

### <a name="run-duration-and-retention"></a>Criteri di conservazione e durata dell'esecuzione

Questi sono i limiti per un'app di logica singola esecuzione.

|Nome|Limite|Note|
|----|----|----|
|Durata esecuzione|90 giorni||
|Conservazione dello spazio di archiviazione|90 giorni|Si tratta dall'ora di inizio fase|
|Intervallo di ricorrenza min|sec 15||
|Intervallo di ricorrenza max|500 giorni||


### <a name="looping-and-debatching-limits"></a>Un ciclo e debatching limiti

Questi sono limiti per un'app di logica singola esecuzione.

|Nome|Limite|Note|
|----|----|----|
|Elementi ForEach|5.000|È possibile utilizzare l' [azione di query](../connectors/connectors-native-query.md) per filtrare matrici più grandi in base alle esigenze|
|Fino a iterazioni|10.000||
|Elementi SplitOn|5.000||
|Parallelismo ForEach|20|È possibile impostare un sequenza foreach aggiungendo `"operationOptions": "Sequential"` per il `foreach` azione|


### <a name="throughput-limits"></a>Limiti di velocità

Questi sono limiti per un'istanza di app singola logica. 

|Nome|Limite|Note|
|----|----|----|
|Trigger al secondo|100|Possibile distribuire i flussi di lavoro in più applicazioni in base alle esigenze|

### <a name="definition-limits"></a>Limiti di definizione

Questi sono limiti per la definizione di app singola logica.

|Nome|Limite|Note|
|----|----|----|
|Azioni in ForEach|1|È possibile aggiungere flussi di lavoro annidate per estendere il supporto in base alle esigenze|
|Azioni per flusso di lavoro|60|È possibile aggiungere flussi di lavoro annidate per estendere il supporto in base alle esigenze|
|Consentito annidamento di azione|5|È possibile aggiungere flussi di lavoro annidate per estendere il supporto in base alle esigenze|
|Flussi di regione per abbonamento|1000||
|Trigger per ogni flusso di lavoro|10||
|Numero massimo caratteri per espressione|8.192||
|Max `trackedProperties` dimensione in caratteri|16.000|
|`action`/`trigger`limite di nome|80||
|`description`limite di lunghezza|256||
|`parameters`limite|50||
|`outputs`limite|10||

## <a name="configuration"></a>Configurazione

### <a name="ip-address"></a>Indirizzo IP

Chiamate da un [connettore](../connectors/apis-list.md) vengono recapitata dall'indirizzo IP specificati di seguito.

Chiamate da un'app di logica direttamente (ad esempio tramite [HTTP](../connectors/connectors-native-http.md) o [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)) può essere accompagnato da qualsiasi [Intervalli di indirizzi IP del Data Center del Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

|Area geografica App logica|IP in uscita|
|-----|----|
|Australia orientale|40.126.251.213|
|Australia sudorientale|40.127.80.34|
|Brasile sud|191.232.38.129|
|India centrale|104.211.98.164|
|Centrale USA|40.122.49.51|
|Asia orientale|23.99.116.181|
|Stati Uniti orientali|191.237.41.52|
|Stati Uniti orientali 2|104.208.233.100|
|Giappone est|40.115.186.96|
|Giappone ovest|40.74.130.77|
|America del Nord centrale USA|65.52.218.230|
|Europa Nord America|104.45.93.9|
|Sud centrale USA|104.214.70.191|
|Area Asia sudorientale|13.76.231.68|
|India sud|104.211.227.225|
|Europa occidentale|40.115.50.13|
|India ovest|104.211.161.203|
|Usa ovest|104.40.51.248|


## <a name="next-steps"></a>Passaggi successivi  

- Per iniziare a utilizzare App logica, seguire l'esercitazione [creare un'App di logica](app-service-logic-create-a-logic-app.md) .  
- [Visualizzare esempi e scenari comuni](app-service-logic-examples-and-scenarios.md)
- [È possibile automatizzare processi aziendali con logica di App](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Informazioni su come integrare i sistemi con logica di App](http://channel9.msdn.com/Events/Build/2016/P462)