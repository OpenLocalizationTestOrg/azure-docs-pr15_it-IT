<properties
    pageTitle="Ridimensionare il processo di flusso Analitica con le funzioni di apprendimento Azure | Microsoft Azure"
    description="Informazioni su come ridimensionare correttamente processi flusso Analitica (partizione, SU quantità e altro ancora) quando si utilizzano funzioni di apprendimento Azure."
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Ridimensionare il processo di flusso Analitica con le funzioni di apprendimento Azure

Può essere molto semplice impostare un processo Analitica flusso ed eseguire alcuni dati di esempio attraversata. Che cos'è necessario creare quando è necessario eseguire la stessa operazione con maggiore volume di dati? Richiede l'esecuzione di imparare a configurare il processo di flusso Analitica, in modo che verrà ridimensionata. In questo documento è dedicata caratteristiche speciali di ridimensionamento processi flusso Analitica con le funzioni di apprendimento. Per informazioni su come ridurre i processi di flusso Analitica in generale vedere l'articolo [processi di ridimensionamento](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Che cos'è una funzione di apprendimento Azure nel flusso Analitica?

Ad esempio una chiamata di funzione normale in linguaggio di query flusso Analitica, è possibile utilizzare una funzione di apprendimento in flusso Analitica. Tuttavia, dietro la scena, le chiamate sono effettivamente Azure Machine Learning Web richieste di assistenza. Servizi web di apprendimento macchina supportano "batch" più righe, ossia minima batch, nella stessa API chiamata al servizio web, per migliorare la produttività complessiva. Vedere gli articoli seguenti per informazioni dettagliate; [Funzioni di apprendimento Azure in flusso Analitica](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) e [Servizi Web di Windows Azure computer risorse](machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs).

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurare un processo di flusso Analitica con le funzioni di apprendimento

Quando si configura una funzione di apprendimento per il processo di flusso Analitica, sono disponibili due parametri per valutare la possibilità di, le dimensioni di batch delle chiamate di funzione apprendimento e le unità Streaming (SUs) viene completato il provisioning del processo di flusso Analitica. Per determinare i valori appropriati per questo motivo, prima di tutto un'occorre tra la latenza e la velocità di trasmissione, vale a dire latenza del processo di flusso Analitica e velocità di ogni SO. SUs può essere sempre aggiunti a un processo per aumentare la velocità di una query flusso Analitica anche partizionata, sebbene SUs aggiuntive aumenta il costo di esecuzione del processo.

Pertanto è importante determinare la *tolleranza* della latenza di in esecuzione di un processo di flusso Analitica. Latenza aggiuntiva esecuzione le richieste di servizi di apprendimento Azure naturalmente aumenterà con dimensioni del batch, che verranno composti la latenza del processo di flusso Analitica. Aumentare le dimensioni del batch consente mano, il processo di flusso Analitica per l'elaborazione di *altri eventi con i *stesso numero * delle richieste di servizio web di apprendimento. Spesso l'aumento della latenza di servizio web di apprendimento è secondari lineare per l'aumento delle dimensioni del batch pertanto è importante considerare le dimensioni del batch più di un servizio web di apprendimento in qualsiasi tipo dato situazione. Dimensione batch predefinita per le richieste di servizio web è 1000 e può essere modificata utilizzando l' [API REST Analitica flusso](https://msdn.microsoft.com/library/mt653706.aspx "API REST Analitica flusso") o il [client di PowerShell per Analitica flusso](stream-analytics-monitor-and-manage-jobs-use-powershell.md "PowerShell client per flusso Analitica").

Dopo aver determinata una dimensione batch, è possibile determinare l'importo delle unità Streaming (SUs), in base al numero di eventi che la funzione deve elaborare al secondo. Per ulteriori informazioni sulle unità Streaming consultare l'articolo [flusso Analitica scala processi](stream-analytics-scale-jobs.md#configuring-streaming-units).

In generale, sono disponibili 20 connessioni simultanee al servizio web apprendimento per ogni 6 SUs, tranne per il fatto che processi di SU 1 e 3 SO otterrà 20 connessioni simultanee anche.  Ad esempio, se il tasso di dati di input è 200.000 eventi al secondo e le dimensioni del batch sono lasciare il valore predefinito di 1000 la latenza del servizio web risultante con batch minima eventi 1000 è 200 ms. Di conseguenza, che tutte le connessioni possono richiedere 5 al servizio web apprendimento in un secondo. Con 20 connessioni, il processo di flusso Analitica può elaborare 20.000 eventi in 200 ms e pertanto 100.000 in un secondo. Pertanto per elaborare 200.000 eventi al secondo, il processo di flusso Analitica deve 40 connessioni simultanee, ossia a 12 SUs. L'illustrazione seguente mostra le richieste dal processo flusso Analitica all'endpoint del servizio web apprendimento: ogni 6 SUs ha 20 connessioni simultanee al servizio web di apprendimento in max.

![Scala flusso Analitica con esempio processo Machine Learning funzioni 2] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Scala flusso Analitica con esempio processo Machine Learning funzioni 2")

In generale, ***B*** per dimensioni del batch, ***g*** per la latenza del servizio web dimensioni batch B in millisecondi, la velocità di un processo di flusso Analitica con SUs ***N*** è:

![Ridimensionare Analitica di flusso con apprendimento automatico Formula funzioni] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Ridimensionare Analitica di flusso con apprendimento automatico Formula funzioni")

Si consideri possono essere 'chiamate max simultanee' sul lato del servizio web apprendimento, è consigliabile per impostare il valore più grande (attualmente 200).

Per ulteriori informazioni sulla rivedere impostazione l' [articolo proporzioni per i servizi Web di risorse computer](../machine-learning/machine-learning-scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Esempio: analisi di valutazione in

Nell'esempio seguente include un processo di flusso Analitica con l'analisi di valutazione in funzione di apprendimento, come descritto nell' [esercitazione integrazione apprendimento Analitica flusso](stream-analytics-machine-learning-integration-tutorial.md).

La query è una semplice completamente partizionata seguita da di **valutazione in** una funzione, come illustrato di seguito:

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery

Valutare la possibilità di uno scenario seguente. con una velocità di 10.000 TWEET al secondo un processo di flusso Analitica deve essere creato per eseguire analisi di valutazione in dei TWEET (eventi). 1 so, Impossibile questo processo flusso Analitica essere in grado di gestire il traffico? Utilizzando la dimensione predefinita del batch di 1000 il processo dovrebbe essere possibile gestire l'input. Ulteriormente la funzione di apprendimento aggiunta deve generare non più di un secondo di latenza, ossia generale latenza predefinito dell'analisi di valutazione in servizio web apprendimento (con una dimensione predefinita del batch di 1000). Latenza **Generale** o -to-end del processo flusso Analitica sarebbe in genere alcuni secondi. Tenere più dettagliate questo processo flusso Analitica, *in particolare* le chiamate di funzione di apprendimento. Con le dimensioni del batch come 1000, velocità di 10.000 eventi sarà necessaria circa 10 richieste servizio web. Anche con 1 so, esistono abbastanza connessioni simultanee per consentire il traffico di input.

Ma cosa accade se il tasso di evento di input viene aumentato x 100 e ora deve elaborare 1.000.000 TWEET al secondo il processo di flusso Analitica? Sono disponibili due opzioni:

1.  Aumentare la dimensione batch o
2.  Suddividere il flusso di input per elaborare gli eventi in parallelo

Con la prima opzione, il processo **latenza** aumenterà.

Con la seconda opzione SUs più sarà necessario eseguire il provisioning e pertanto generare simultanea di più le richieste di servizi web di apprendimento. Questo errore indica che il processo **costo** aumenterà.


Si supponga la latenza dell'analisi di valutazione in servizio web apprendimento 200 ms per batch di eventi di 1000 o di sotto, 250ms per batch di 5.000 eventi, 300 ms per batch di eventi 10.000 o 500 ms per batch di 25.000 eventi.

1. Utilizza la prima opzione, (**non** il provisioning di ulteriori SUs), è possibile aumentare la dimensione batch a **25.000**. Questo a sua volta consentirebbe il processo per l'elaborazione di 1.000.000 eventi con 20 connessioni simultanee al servizio web apprendimento (con una latenza di 500 ms per la chiamata). Pertanto la latenza aggiuntiva del processo di flusso Analitica a causa delle richieste di funzioni di valutazione in contro le richieste di servizi web di apprendimento aumenterebbe da **200 ms** a **500 ms**. Tuttavia, si noti che batch dimensioni **non può** essere maggiore all'infinito come i servizi web di apprendimento richiede le dimensioni di payload di una richiesta di 4 MB o ridurre le richieste di servizio web timeout dopo 100 secondi dell'operazione.
2. Tramite la seconda opzione, le dimensioni del batch viene lasciata 1000, con una latenza di servizio web 200 ms, ogni 20 connessioni simultanee al servizio web sarà in grado di elaborare 1000 eventi *20* 5 = 100.000 al secondo. Per elaborare 1.000.000 eventi al secondo, pertanto il processo sarà necessario 60 SUs. Confrontare la prima opzione, il processo di flusso Analitica consentirebbe ulteriori batch richieste di servizio web, che genera un aumento dei costi.

Di seguito è una tabella per la velocità del processo di flusso Analitica per SUs diversi e le dimensioni di batch (in numero di eventi al secondo).

| dimensioni del batch (latenza ML)  | 500 (200 ms) | 1.000 (200 ms) | 5.000 (250ms) | 10.000 (300 ms) | 25.000 (500 ms) |
|--------|-------------------------|---------------|---------------|----------------|----------------|
| **1 SO** | 2500 | 5.000 | 20.000 | 30.000 | 50.000 |
| **3 SUs** | 2500 | 5.000 | 20.000 | 30.000 | 50.000 |
| **6 SUs** | 2500 | 5.000 | 20.000 | 30.000 | 50.000 |
| **12 SUs** | 5.000 | 10.000 | 40.000 | 60.000 | 100.000 |
| **18 SUs** | 7.500 | 15.000 | 60.000 | 90.000 | 150.000 |
| **24 SUs** | 10.000 | 20.000 | 80.000 | 120.000 | 200.000 |
| **…** | … | … | … | … | … |
| **60 SUs** | 25.000 | 50.000 | 200.000 | 300.000 | 500.000 |

A questo punto, è già una buona conoscenza del funzionano delle funzioni di apprendimento in flusso Analitica. È probabile che anche comprendere che Analitica flusso processi "Estrai" dati da origini dati e ogni "pull" restituisce un batch di eventi per il processo di flusso Analitica per l'elaborazione. Come tale impatto modello pull di apprendimento web richieste di assistenza?

Normalmente, le dimensioni del batch che è impostate per le funzioni di apprendimento sarà esattamente divisibile per il numero di eventi restituiti da ogni processo flusso Analitica "pull". In questo caso si che servizio web di apprendimento verrà chiamato con batch "parziale". In questo modo non sovraccarico processo aggiuntive latenza in eventi riunire più pull per recuperare.

## <a name="new-function-related-monitoring-metrics"></a>Nuove metriche di monitoraggio relativi alla funzione

Nell'area del Monitor di un processo di flusso Analitica, sono state aggiunte tre statistiche aggiuntive di funzione correlati. Sono le richieste di funzioni, funzione eventi e le richieste funzione non è riuscita, come illustrato nella figura seguente.

![Ridimensionare Analitica di flusso con funzioni metriche di apprendimento automatico] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Ridimensionare Analitica di flusso con funzioni metriche di apprendimento automatico")

Sono definiti come indicato di seguito:

**Le richieste di funzioni**: il numero di richieste di funzione.

**Funzione eventi**: il numero di eventi le richieste di funzione.

**Le richieste di funzione di errore**: il numero di richieste di funzione non riuscita.

## <a name="key-takeaways"></a>Materiale chiave  

Per riepilogare i punti principali per scalare un processo di flusso Analitica con le funzioni di apprendimento, è necessario considerare quanto segue:

1.  Il tasso di evento di input
2.  La latenza tollerata per il processo di flusso Analitica in esecuzione (e pertanto le dimensioni del batch delle richieste di servizio web apprendimento)
3.  Il provisioning SUs Analitica flusso e il numero di richieste di servizi web di apprendimento (la funzione correlata costi aggiuntivi)

Una query flusso Analitica completamente partizionata è stata utilizzata come esempio. Se è necessaria una query più complessa [forum Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) è un'ottima risorsa per ottenere assistenza dal team del flusso Analitica.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle flusso Analitica, vedere:

- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
