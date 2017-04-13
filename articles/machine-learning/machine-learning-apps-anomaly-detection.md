<properties 
    pageTitle="Computer risorse app: servizio di rilevamento anomalia | Microsoft Azure" 
    description="API di rilevamento di anomalie è illustrato un esempio integrato con Microsoft Azure apprendimento in grado di rilevare alterazioni nei dati di serie ora con valori numerici che spaziatura uniforme nel tempo." 
    services="machine-learning" 
    documentationCenter="" 
    authors="alokkirpal" 
    manager="jhubbard"
    editor="cgronlun" /> 

<tags 
    ms.service="machine-learning" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="multiple" 
    ms.date="10/11/2016" 
    ms.author="alokkirpal"/>


# <a name="machine-learning-anomaly-detection-service"></a>Servizio di rilevamento di computer risorse anomalia#

##<a name="overview"></a>Panoramica

[API di rilevamento di anomalie](https://datamarket.azure.com/dataset/aml_labs/anomalydetection) è illustrato un esempio integrato con Azure apprendimento in grado di rilevare alterazioni nei dati di serie ora con valori numerici che spaziatura uniforme nel tempo. 

Questa API possibile rilevare i tipi di modelli anomale seguenti nei dati di serie ora:

* **Positivi e negativi tendenze**: ad esempio, quando il monitoraggio dell'utilizzo di memoria per calcolare una linea di tendenza verso l'alto potrebbe essere di interesse come potrebbe essere indicativo di un problema di memoria

* **Le modifiche dell'intervallo dinamico dei valori**: ad esempio, quando si controllano le eccezioni generate da un servizio cloud, eventuali modifiche dell'intervallo dinamico dei valori possono indicare l'instabilità lo stato del servizio, e

* **Picchi e viene inclinato**: ad esempio, quando si controllano il numero di errori di accesso in un servizio o il numero di archiviare in un sito di e-commerce, picchi o DIP potrebbe indicare un comportamento anomalo.

Rilevatori queste risorse computer tenere traccia ad esempio le modifiche ai valori nel tempo e report in corso alle modifiche dei valori come punteggi anomalia. Non richiedono ad-hoc in soglia ottimizzazione e i punteggi dei possono essere usati per controllare tasso positivo falsa. Il rilevamento delle anomalie in API può essere utile in diversi scenari come servizio monitoraggio tenendo traccia di indicatori KPI nel tempo, l'uso monitoraggio tramite metriche, ad esempio un numero di clic, il monitoraggio delle prestazioni tramite contatori come memoria, CPU, legge i file e così via, numero di ricerche nel tempo.

L'offerta di rilevamento di anomalie in viene fornito con strumenti utili per iniziare. 

* L' [applicazione web](http://anomalydetection-aml.azurewebsites.net/) consente di valutare e visualizzare i risultati del rilevamento delle anomalie in API sui dati. 

* Il [codice di esempio](http://adresultparser.codeplex.com/) viene illustrato come accedere all'API e analizzare i risultati in c# a livello di programmazione.

>[AZURE.NOTE]
>Provare **la soluzione IT anomalia approfondimenti** con tecnologia [questa API](https://datamarket.azure.com/dataset/aml_labs/anomalydetection)
>
>Per ottenere questa soluzione to end distribuita all'abbonamento Azure <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank"> **, iniziare da qui >**</a>


##<a name="api-definition"></a>Definizione dell'API

Il servizio fornisce un'API basata su resto su HTTPS che possono essere utilizzati in diversi modi, ad esempio un web o applicazione per dispositivi mobili, R, Python, Excel, e così via.  Inviare i dati della serie ora questo servizio tramite una chiamata API REST e l'esecuzione di una combinazione di tipi tre anomalia descritti in precedenza. Il servizio viene eseguito su piattaforma apprendimento Azure, scale perfettamente alle proprie esigenze aziendali e fornisce i contratti di servizio.

###<a name="headers"></a>Intestazioni
Assicurarsi di includere le intestazioni corrette nella convocazione, sarà simile alla seguente:

    Authorization: Basic <creds>
    Accept: application/json
               
    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

È possibile trovare la chiave account dal proprio account [Azure Data Market](https://datamarket.azure.com/account/keys). 

###<a name="score-api"></a>API di punteggio

L'API punteggio viene utilizzata per l'esecuzione di rilevamento di anomalie in dati serie ora non stagionale. L'API esegue un numero di rilevatori anomalia sui dati e restituisce i punteggi dei anomalia. La figura seguente mostra un esempio di alterazioni in grado di rilevare l'API di punteggio. Modifiche a livello di distinte 2 e 3 picchi questa serie temporale. Punti rossi mostrano il tempo in cui viene rilevata la modifica del livello, mentre i punti neri visualizzati picchi rilevati.

![API di punteggio][1]
    
**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/Score 

**Corpo di richiesta di esempio**

Nella richiesta, si invia una serie temporale (mostrata troncata) con i punti dati di 1/3/2016 tramite 3/10/2016 e i parametri di rilevatori raccoglitore all'API per il rilevamento delle se uno qualsiasi di questi punti dati anomalo. 

    {
      "data": [
        [ "9/21/2014 11:05:00 AM", "3" ],
        [ "9/21/2014 11:10:00 AM", "9.09" ],
        [ "9/21/2014 11:15:00 AM", "0" ]
      ],
      "params": {
        "tspikedetector.sensitivity": "3",
        "zspikedetector.sensitivity": "3",
        "trenddetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "postprocess.tailRows": "2"
      }
    }

Sarà la risposta alla seguente: 

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
      "ADOutput":"{
        "ColumnNames":["Time","Data","TSpike","ZSpike","rpscore","rpalert","tscore","talert"],
        "ColumnTypes":["DateTime","Double","Double","Double","Double","Int32","Double","Int32"],
        "Values":[
          ["9/21/2014 11:10:00 AM","9.09","0","0","-1.07030497733224","0","-0.884548154298423","0"],
          ["9/21/2014 11:15:00 AM","0","0","0","-1.05186237440962","0","-1.173800281031","0"]
        ]
      }"
    }

###<a name="scorewithseasonality-api"></a>ScoreWithSeasonality API

L'API ScoreWithSeasonality viene utilizzata per l'esecuzione di rilevamento di anomalie nella serie temporale contenenti modelli stagionale. Questa API è utile per rilevare deviazioni nei modelli di stagionale.  

La figura seguente mostra un esempio di alterazioni rilevato in una serie temporale stagionale. La serie temporale un raccoglitore (il 1 ° punto nero), due DIP (il punto nero 2a e quella alla fine) e modifica del livello di uno (punto rosso). Si noti che dip al centro la serie temporale e la modifica del livello solo eccessiva dopo la rimozione di componenti stagionali della serie.

![Stagionalità API][2]

**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/ScoreWithSeasonality 

**Corpo di richiesta di esempio**

Nella convocazione di seguito, è inviare una serie temporale (mostrata troncata) con i punti dati di 1/3/2016 tramite 3/10/2016 e i parametri all'API per il rilevamento delle se uno qualsiasi di questi punti dati anomalo. 

    {
      "data": [
        [ "9/21/2014 11:10:00 AM", "9" ],
        [ "9/21/2014 11:15:00 AM", "12" ],
        [ "9/21/2014 11:20:00 AM", "10" ]
      ],
      "params": {
        "preprocess.aggregationInterval": "0",
        "preprocess.aggregationFunc": "mean",
        "preprocess.replaceMissing": "lkv",
        "postprocess.tailRows": "1",
        "zspikedetector.sensitivity": "3",
        "tspikedetector.sensitivity": "3",
        "upleveldetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "trenddetector.sensitivity": "3.25",
        "detectors.historywindow": "500",
        "seasonality.enable": "true",
        "seasonality.transform": "deseason",
        "seasonality.numSeasonality": "1"
      }
    }

Sarà la risposta alla seguente: 

    {
        "odata.metadata": "https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
        "ADOutput": "{
            "ColumnNames":["Time","OriginalData","ProcessedData","TSpike","ZSpike","PScore","PAlert","RPScore","RPAlert","TScore","TAlert"],
            "ColumnTypes":["DateTime","Double","Double","Double","Double","Double","Int32","Double","Int32","Double","Int32"],
            "Values":[
                ["9/21/201411: 20: 00AM","10","10","0","0","-1.30229513613974","0","-1.30229513613974","0","-1.173800281031","0"]
            ]
        }"
    }

###<a name="detectors"></a>Rilevatori

Il rilevamento delle anomalie in API supporta rilevatori in 3 categorie. Sono disponibili informazioni dettagliate sulla specifici parametri di input e di output per ogni rilevamento descritti nella tabella seguente.

|Rilevamento categoria|Rilevamento|Descrizione|Parametri di input|Output
|---|---|---|---|---|
|Rilevatori Raccoglitore|Rilevamento TSpike|Rilevare picchi e DIP in base a quanto i valori vengono dal primo e terzi quartili|*tspikedetector.sensitivity:* accetta valore integer nell'intervallo 1-10 predefinito: 3; I valori più alti grado di rilevare più valori estremi renderlo così meno sensibili|TSpike: valori binari: "1" Se viene rilevato un raccoglitore/dip, '0' in caso contrario|
||Rilevamento ZSpike|Rilevare picchi e DIP in base a di quanto la datapoints sono rispetto alla loro Media|*zspikedetector.sensitivity:* eseguire valore integer compreso nell'intervallo 1-10 predefinito: 3; I valori più alti grado di rilevare più valori estremi rendendo meno sensibili|ZSpike: valori binari: "1" Se viene rilevato un raccoglitore/dip, '0' in caso contrario|
|Rilevamento di tendenza lenta|Rilevamento di tendenza lenta|Rilevare lenta tendenza positiva in base alla riservatezza set|*trenddetector.sensitivity:* soglia punteggio rilevamento (impostazione predefinita: 3,25, 3,25-5 è un intervallo adeguato per selezionare questa opzione da; Maggiore di meno sensibili)|TScore: mobile numero che rappresenta il punteggio anomalia sulla linea di tendenza|
|Modifica del livello rilevatori|Modifica del livello unidirezionale rilevamento|Rilevare livello verso l'alto modificare in base alla riservatezza set|*upleveldetector.sensitivity:* soglia punteggio rilevamento (impostazione predefinita: 3,25, 3,25-5 è un intervallo adeguato per selezionare questa opzione da; Maggiore di meno sensibili)|PScore: numero che rappresenta il punteggio anomalia su verso l'alto in Mobile modifica di livello|
||Rilevamento modifiche livello bidirezionale|Rilevamento di modifica del livello verso l'alto e verso il basso in base alla riservatezza set|*bileveldetector.sensitivity:* soglia punteggio rilevamento (impostazione predefinita: 3,25, 3,25-5 è un intervallo adeguato per selezionare questa opzione da; Maggiore di meno sensibili)|RPScore: mobile numero che rappresenta anomalia punteggio verso l'alto e verso il basso livello verranno modificati

###<a name="parameters"></a>Parametri

Ulteriori informazioni su questi parametri di input sono elencate nella tabella riportata di seguito:

|Parametri di input|Descrizione|Impostazione predefinita|Tipo|Intervallo valido|Intervallo suggerito|
|---|---|---|---|---|---|
|preprocess.aggregationInterval|Intervallo di aggregazione in secondi per l'aggregazione di input serie temporale|0 (viene eseguita alcuna aggregazione)|numero intero|0: ignorare l'aggregazione, > 0 in caso contrario|5 minuti per 1 giorno, dipendente serie temporale
|preprocess.aggregationFunc|Funzione utilizzata per aggregare i dati in AggregationInterval specificato|Media|enumerati|somma, Media, lunghezza|N/D|
|preprocess.replaceMissing|Valori utilizzati per imputare dati mancanti|lkv (noto ultimo valore)|enumerati|zero, lkv, Media|N/D|
|detectors.historyWindow|Cronologia (in numero di punti dati) utilizzata per il calcolo punteggio anomalia|500|numero intero|10-2000|Serie temporale dipendente|
|upleveldetector.Sensitivity|Riservatezza del contenuto per livello verso l'alto modificare rilevamento. |3,25|doppia|Nessuno|3,25 5(Lesser values mean more sensitive)|
|bileveldetector.Sensitivity|Riservatezza del contenuto per bidirezionale modifica del livello di rilevamento. |3,25|doppia|Nessuno|3,25 5(Lesser values mean more sensitive)|
|trenddetector.Sensitivity|Riservatezza del contenuto per rilevamento tendenza positiva. |3,25|doppia|Nessuno|3,25 5(Lesser values mean more sensitive)|
|tspikedetector.Sensitivity |Riservatezza del contenuto per il rilevamento di TSpike|3|numero intero|1-10|3-5(Lesser values mean more sensitive)|
|zspikedetector.Sensitivity |Riservatezza del contenuto per il rilevamento di ZSpike|3|numero intero|1-10 |3-5(Lesser values mean more sensitive)|
|seasonality.Enable|Se sono eseguire analisi di stagionalità|vero|valore booleano|vero, FALSO|Serie temporale dipendente|
|seasonality.numSeasonality |Numero massimo di cicli periodici da rilevare|1|numero intero|1, 2|1-2|
|seasonality.Transform |Se stagionale componenti tendenza devono essere rimosse prima di applicare il rilevamento di anomalie (e)|deseason|enumerati|Nessuna, deseason, deseasontrend|N/D|
|postprocess.tailRows |Numero di punti dati più recenti da conservare nei risultati di output|0|numero intero|0 (mantenere tutti i punti dati), oppure specificare numero di punti per mantenere nei risultati|N/D|

###<a name="output"></a>Output
L'API esegue rilevatori tutti nella serie di dati ora e restituisce punteggi anomalie e gli indicatori di Raccoglitore binario per ogni punto nel tempo. La tabella seguente elenca gli output di API. 

|Output|Descrizione|
|---|---|
|Ora|Indicatori di dati non elaborati o dati aggregati (e/o) figurativi se aggregazione (e/o) mancanti imputazione dati viene applicata|
|OriginalData|Se i valori da dati non elaborati o aggregati (dati e/o) figurativi aggregazione (e/o) mancanti imputazione dati viene applicata|
|ProcessedData|Una delle operazioni seguenti: <ul><li>Regolate fluttuazioni stagionali serie temporale se stagionalità significativa sono state rilevate e deseason opzione selezionata.</li><li>fluttuazioni stagionali corretti e detrended serie temporale se è stata rilevata significativa stagionalità e deseasontrend selezionata</li><li>in caso contrario, è diverso da quello di OriginalData</li>|
|TSpike|Indicatore binario per indicare se viene rilevato un Raccoglitore dal rilevamento TSpike|
|ZSpike|Indicatore binario per indicare se viene rilevato un Raccoglitore dal rilevamento ZSpike|
|Pscore|Un numero mobile che rappresenta il punteggio anomalie nel livello verso l'alto modificare|
|Palert|valore 1/0 che indica l'assenza di un livello verso l'alto modificare anomalie in base a input riservatezza|
|RPScore|Un'anomalia che rappresentano numero mobile punteggio sulla modifica del livello di bidirezionale|
|RPAlert|valore 1/0 che indica il livello bidirezionale è modificare anomalie in base a input riservatezza|
|TScore|Un'anomalia che rappresentano numero mobile punteggio sulla linea di tendenza positivo|
|TAlert|valore 1/0 che indica che ci è un'anomalia tendenza positiva in base a input riservatezza|


L'output può essere analizzato utilizzando un [semplice parser](https://adresultparser.codeplex.com/) : è il codice di esempio che illustra come connettere all'API e analizzare l'output. Alterazioni rilevati possono essere visualizzati in un dashboard e/o passare umani esperti per azioni correttive o integrati con il ticket sistemi.


[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-score.png
[2]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-seasonal.png

 

 
