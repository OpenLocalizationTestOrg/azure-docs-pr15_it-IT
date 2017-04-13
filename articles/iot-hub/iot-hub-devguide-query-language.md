<properties
 pageTitle="Guida per sviluppatori - linguaggio di query | Microsoft Azure"
 description="Guida di sviluppo IoT Hub Azure - descrizione del linguaggio di query utilizzato per recuperare informazioni su due facce, metodi e processi dall'hub IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>

# <a name="reference---query-language-for-twins-and-jobs"></a>Riferimento: linguaggio di query per due facce e processi

## <a name="overview"></a>Panoramica

IoT Hub fornisce un linguaggio SQL potente per recuperare le informazioni relative alle [due facce dispositivo] [ lnk-twins] e [processi][lnk-jobs]. In questo articolo vengono:

* Un'introduzione alle funzionalità principali del linguaggio di query dell'IoT Hub, e
* Descrizione dettagliata della lingua.

## <a name="getting-started-with-twin-queries"></a>Guida introduttiva di query doppi

[Due facce dispositivo] [ lnk-twins] può contenere oggetti JSON non autorizzati come tag e proprietà. IoT Hub consente a due facce dispositivo query come singolo documento JSON contenente tutte le informazioni di disponibilità di una copia.
Si supponga ad esempio, che le due facce hub IoT abbiano la struttura seguente:

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                "location": {                                                      
                    "region": "US",                                                  
                    "plant": "Redmond43"                                             
                }                                                                  
            },                                                                   
            "properties": {                                                      
                "desired": {                                                       
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300                                          
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                     
                    },                                                               
                    "$version": 4                                                    
                },                                                                 
                "reported": {                                                      
                    "connectivity": {                                                
                        "type": "cellular"                            
                    },                                                               
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300,                                         
                        "status": "Success"                                            
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                
                    },                                                               
                    "$version": 7                                                    
                }                                                                  
            }                                                                    
        }

IoT Hub espone due facce il dispositivo come un insieme di documento denominato **dispositivi**.
Pertanto la query seguente recupera l'intero set di due facce dispositivo:

        SELECT * FROM devices

> [AZURE.NOTE] [IoT Hub SDK] [ lnk-hub-sdks] supporta questa funzionalità dei risultati di grandi dimensioni.

Gli IoT Hub consentono di recuperare due facce di filtro con condizioni non autorizzate. Ad esempio,

        SELECT * FROM devices
        WHERE tags.location.region = 'US'

Recupera i due facce con il tag **location.region** impostato su **CONTATTACI**.
Operatori booleani e confronti aritmetici supportati, ad esempio

        SELECT * FROM devices
        WHERE tags.location.region = 'US'
            AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60

Recupera tutti due facce che si trova negli Stati Uniti configurato per l'invio di telemetria poco supera il minuto. Per comodità, è anche possibile utilizzare le costanti di matrice con **nella** e operatori **/NIN** (non in). Ad esempio,

        SELECT * FROM devices
        WHERE property.reported.connectivity IN ['wired', 'wifi']

Recupera tutti due facce segnalati wifi o cablata connettività. Fare riferimento alla [clausola WHERE] [ lnk-query-where] sezione per il riferimento completo delle funzionalità di filtro.

Raggruppamento e le aggregazioni sono supportate anche. Ad esempio,

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

Restituisce il conteggio dei dispositivi in ogni stato di configurazione di telemetria.

        [
            {
                "numberOfDevices": 3,
                "status": "Success"
            },
            {
                "numberOfDevices": 2,
                "status": "Pending"
            },
            {
                "numberOfDevices": 1,
                "status": "Error"
            }
        ]

L'esempio precedente illustra una situazione in tre dispositivi segnalati la corretta configurazione, due sono comunque l'applicazione di configurazione e uno segnalato un errore.

### <a name="c-example"></a>Esempio di c#

La funzionalità di query viene esposta tramite il [servizio c# SDK] [ lnk-hub-sdks] nella classe **RegistryManager** .
Ecco un esempio di una query semplice:

        var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
        while (query.HasMoreResults)
        {
            var page = await query.GetNextAsTwinAsync();
            foreach (var twin in page) 
            {
                // do work on twin object
            }
        }

Si noti come si crea un'istanza dell'oggetto di **query** con un formato di pagina (fino a 1000) e quindi più pagine possono essere recuperate chiamando i metodi di **GetNextAsTwinAsync** più volte.
È importante tenere presente che l'oggetto della query espone più **successivo\***, a seconda dell'opzione deserializzazione necessario affinché la query, vale a dire doppio o processo oggetti o Json semplice da utilizzare quando si usa previsioni.

### <a name="node-example"></a>Esempio di nodo

La funzionalità di query viene esposta tramite il [servizio di nodo SDK] [ lnk-hub-sdks] nell'oggetto **Registro di sistema** .
Ecco un esempio di una query semplice:

        var query = registry.createQuery('SELECT * FROM devices', 100);
        var onResults = function(err, results) {
            if (err) {
                console.error('Failed to fetch the results: ' + err.message);
            } else {
                // Do something with the results
                results.forEach(function(twin) {
                    console.log(twin.deviceId);
                });

                if (query.hasMoreResults) {
                    query.nextAsTwin(onResults);
                }
            }
        };
        query.nextAsTwin(onResults);

Si noti come si crea un'istanza dell'oggetto di **query** con un formato di pagina (fino a 1000) e quindi più pagine possono essere recuperate chiamando i metodi di **nextAsTwin** più volte.
È importante tenere presente che l'oggetto della query espone più **successivo\***, a seconda dell'opzione deserializzazione necessario affinché la query, vale a dire doppio o processo oggetti o Json semplice da utilizzare quando si usa previsioni.

### <a name="limitations"></a>Limitazioni

Attualmente, previsioni sono supportati solo quando si utilizzano le aggregazioni, ovvero non aggregato query è possibile utilizzare solo `SELECT *`. Inoltre, l'aggregazione sono supportati solo in combinazione con il raggruppamento.

## <a name="getting-started-with-jobs-queries"></a>Guida introduttiva di query processi

[Processi] [ lnk-jobs] consentono di eseguire operazioni sui set di dispositivi. Ogni doppi dispositivo contiene le informazioni dei processi di cui fa parte di una raccolta denominata **processi**.
In modo logico,

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                ...                                                              
            },                                                                   
            "properties": {                                                      
                ...                                                                 
            },
            "jobs": [
                { 
                    "deviceId": "myDeviceId",
                    "jobId": "myJobId",    
                    "jobType": "scheduleTwinUpdate",            
                    "status": "completed",                    
                    "startTimeUtc": "2016-09-29T18:18:52.7418462",
                    "endTimeUtc": "2016-09-29T18:20:52.7418462",
                    "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
                    "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
                    "outcome": {
                        "deviceMethodResponse": null   
                    }                                         
                },
                ...
            ]                                                             
        }

Questa raccolta è attualmente queriable come **devices.jobs** del linguaggio di query IoT Hub.

Per visualizzare tutti i processi (passati e programmati) che influiscono su un singolo dispositivo, ad esempio, è possibile utilizzare la query seguente:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'

Si noti come questa query fornisce lo stato specifico di dispositivo (ed eventualmente la risposta metodo diretto) di ogni processo restituito.
È inoltre possibile applicare un filtro con le condizioni booleane non autorizzate su tutte le proprietà degli oggetti nella raccolta **devices.jobs** .
Ad esempio, la query seguente:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'
            AND devices.jobs.jobType = 'scheduleTwinUpdate'
            AND devices.jobs.status = 'completed'
            AND devices.jobs.createdTimeUtc > '2016-09-01'

Recupera tutti doppi completato i processi di aggiornamento per dispositivo **myDeviceId** che sono stati creati dopo settembre 2016.

È anche possibile recuperare i risultati per dispositivo di un singolo processo.

        SELECT * FROM devices.jobs
        WHERE devices.jobs.jobId = 'myJobId'

### <a name="limitations"></a>Limitazioni
Al momento non supportano query su **devices.jobs** :

* Previsioni, vale a dire solo `SELECT *` è possibile;
* Condizioni che fanno riferimento a doppi dispositivo oltre alle proprietà di processo, come illustrato sopra.
* Aggregazioni eseguire, ad esempio conteggio, Media, Raggruppa per.

## <a name="basics-of-an-iot-hub-query"></a>Nozioni di base di una query IoT Hub

Clausole di ogni query IoT Hub composto da un'istruzione SELECT e clausole FROM e WHERE facoltativa e GROUP BY Ogni query viene eseguita in una raccolta di documenti JSON, ad esempio, due facce di dispositivo. La clausola FROM indica l'insieme di documenti per scorrere in (**dispositivi** o **devices.jobs**). Quindi, viene applicato il filtro nella clausola WHERE. In caso di aggregazioni, i risultati di questo passaggio sono raggruppati come specificato nella clausola GROUP BY e, per ogni gruppo, una riga viene generata come specificato nella clausola SELECT.

        SELECT <select_list>
        FROM <from_specification>
        [WHERE <filter_condition>]
        [GROUP BY <group_specification>]

## <a name="from-clause"></a>Clausola FROM

La clausola **FROM < from_specification >** può assumere solo due valori: **da dispositivi**a due facce dispositivo query o **da devices.jobs**per i dettagli dei processi per dispositivo query.

## <a name="where-clause"></a>Clausola WHERE

Il **dove < filter_condition >** clausola è facoltativa. Consente di specificare le condizioni che i documenti JSON nella raccolta FROM devono soddisfare per essere inclusi come parte del risultato. Qualsiasi documento JSON deve restituire le condizioni specificate su "true" da includere nel risultato.

Le condizioni consentite sono descritte nella sezione [espressioni e condizioni][lnk-query-expressions].

## <a name="select-clause"></a>Clausola SELECT

La clausola SELECT (**selezionare < selezione >**) è obbligatoria e include i valori recuperati dalla query. Specifica i valori JSON essere usata per generare nuovi oggetti JSON per ogni elemento del sottoinsieme filtrato (e facoltativamente raggruppato) della raccolta da fase di proiezione genera un nuovo oggetto JSON, creato con i valori specificati nella clausola SELECT.

Si tratta di grammatica della clausola SELECT:

        SELECT [TOP <max number>] <projection list>

        <projection_list> ::=
            '*'
            | <projection_element> AS alias [, <projection_element> AS alias]+

        <projection_element> :==
            attribute_name
            | <projection_element> '.' attribute_name
            | <aggregate>

        <aggregate> :==
            count(<projection_element>) | count()
            | avg(<projection_element>) | avg()
            | sum(<projection_element>) | sum()
            | min(<projection_element>) | min()
            | max(<projection_element>) | max()

**nome_attributo** nel punto in cui si riferisce a tutte le proprietà del documento nella raccolta da JSON. Alcuni esempi delle clausole SELECT sono disponibili nella [Guida introduttiva alla query doppi] [ lnk-query-getstarted] sezione.

Al momento le clausole selezione diverse da quello **selezionare \* ** sono supportati solo nelle query di aggregazione in due facce.

## <a name="group-by-clause"></a>Clausola GROUP BY

La clausola **GROUP BY < group_specification >** è un passaggio facoltativo che può essere eseguito dopo il filtro specificato nella clausola WHERE e prima la proiezione specificata nella finestra di selezione. Gruppi di documenti in base al valore dell'attributo. Questi gruppi vengono utilizzati per generare valori aggregati come specificato nella clausola SELECT.

Esempio di una query con GROUP BY è:

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

Sintassi formale per GROUP BY è:

        GROUP BY <group_by_element>
        <group_by_element> :==
            attribute_name
            | < group_by_element > '.' attribute_name

**nome_attributo** nel punto in cui si riferisce a tutte le proprietà del documento nella raccolta da JSON. 

Al momento della clausola GROUP BY è supportata solo quando si eseguono query due facce.

## <a name="expressions-and-conditions"></a>Espressioni e condizioni

Un alto livello, un' *espressione*:

* Restituisce un'istanza di un tipo JSON (ad esempio booleano, numero, stringa, matrice o oggetto), e
* Viene definito per la modifica dei dati provenienti da dispositivo JSON documento e costanti utilizzando le funzioni e gli operatori incorporati.

*Condizioni* sono espressioni che restituiscono un valore booleano, ovvero una costante diversa da quella booleana **true** viene considerato come **false** (inclusa **null**, **non definito**, qualsiasi istanza di oggetto o in una matrice, qualsiasi stringa e chiaramente Boolean **false**).

La sintassi per le espressioni è:

        <expression> ::=
            <constant> |
            attribute_name |
            unary_operator <expression> |
            <expression> binary_operator <expression> |
            <create_array_expression> |
            '(' <expression> ')'

        <constant> ::=
            <undefined_constant>
            | <null_constant> 
            | <number_constant> 
            | <string_constant> 
            | <array_constant> 

        <undefined_constant> ::= undefined
        <null_constant> ::= null
        <number_constant> ::= decimal_literal | hexadecimal_literal
        <string_constant> ::= string_literal
        <array_constant> ::= '[' <constant> [, <constant>]+ ']'

dove:

| Simbolo | Definizione |
| -------------- | -----------------|
| nome_attributo | Qualsiasi proprietà del documento nella raccolta da JSON. |
| UNARY_OPERATOR | Qualsiasi operatore unario indicato nella sezione operatori. |
| binary_operator | Qualsiasi operatore binario in base alla sezione operatori. |
| decimal_literal | Margine di flessibilità espresso in decimale. |
| hexadecimal_literal | Numero espresso dalla stringa "x 0" seguita da una stringa di cifre. |
| stringa letterale | Le stringhe letterali sono stringhe Unicode rappresentate da una sequenza di zero o più caratteri Unicode o sequenze di escape. Stringhe letterali siano racchiusi tra virgolette singole (apostrofo: ") o doppie (virgolette:"). Consentito escape: `\'`, `\"`, `\\`, `\uXXXX` per i caratteri Unicode definiti da 4 cifre esadecimali. |

### <a name="operators"></a>Operatori

Gli operatori seguenti sono supportati:

| Famiglia | Operatori |
| -------------- | -----------------|
| Aritmetica | +,-,*,/,% |
| Logico | E, O MENO |
| Confronto |  =,! =, <>,, < =, > = <> |

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come eseguire query nelle applicazioni utilizzando [IoT Hub SDK][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#getting-started-with-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md