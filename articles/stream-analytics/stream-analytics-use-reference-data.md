<properties
    pageTitle="Usare le tabelle di dati e la ricerca dei riferimenti in flusso Analitica | Microsoft Azure"
    description="Usare dati di riferimento in una query flusso Analitica"
    keywords="tabella di ricerca, dati di riferimento"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="using-reference-data-or-lookup-tables-in-a-stream-analytics-input-stream"></a>Usare le tabelle di dati o della ricerca riferimento in un flusso di input flusso Analitica

Dati di riferimento (noto anche come una tabella di ricerca) sono un set di dati limitato statica o rallentano la modifica in natura, utilizzato per eseguire una ricerca o per correlare con il flusso di dati. Per rendere l'utilizzo di dati di riferimento nel processo di Azure flusso Analitica generalmente utilizzerà un [Riferimento dati Join](https://msdn.microsoft.com/library/azure/dn949258.aspx) nella Query. Flusso Analitica utilizza archiviazione Blob Azure come livello di spazio di archiviazione dei dati di riferimento e con riferimento Azure Data Factory dati possono essere trasformati e/o copiati allo spazio di archiviazione Blob Azure, per l'uso dei dati di riferimento, [in base a qualsiasi numero di cloud e archivi dati locali](../data-factory/data-factory-data-movement-activities.md). Dati di riferimento sono simile a quello come una sequenza di BLOB (definito nella configurazione di input) in ordine crescente di data/ora specificata in nome blob. Supporta **solo** l'aggiunta alla fine della sequenza mediante una data/ora **maggiore** di quello specificato dall'ultimo blob nella sequenza.

## <a name="configuring-reference-data"></a>Configurazione dei dati di riferimento

Per configurare i dati di riferimento, è innanzitutto necessario creare un input è del tipo di **Dati di riferimento**. Nella tabella riportata di seguito vengono illustrate ogni proprietà che è necessario fornire durante la creazione di input di dati di riferimento con la relativa descrizione:

<table>
<tbody>
<tr>
<td>Nome della proprietà</td>
<td>Descrizione</td>
</tr>
<tr>
<td>Alias di input</td>
<td>Un nome descrittivo da utilizzare nella query processo per fare riferimento a questo input.</td>
</tr>
<tr>
<td>Account di archiviazione</td>
<td>Il nome dell'account di archiviazione in cui si trovano i file di archivio blob. Se nella stessa sottoscrizione Analitica flusso di lavoro, è possibile selezionare dall'elenco a discesa verso il basso.</td>
</tr>
<tr>
<td>Chiave Account lo spazio di archiviazione</td>
<td>Segreto associato all'account di archiviazione. Se l'account di archiviazione è nello stesso abbonamento come il processo di flusso Analitica viene immesso automaticamente.</td>
</tr>
<tr>
<td>Contenitore di spazio di archiviazione</td>
<td>I contenitori forniscono un raggruppamento logico per BLOB archiviati nel servizio di Microsoft Azure Blob. Quando si carica un blob al servizio Blob, è necessario specificare un contenitore per tale blob.</td>
</tr>
<tr>
<td>Motivo percorso</td>
<td>Il percorso del file utilizzato per individuare il BLOB all'interno del contenitore specificato. All'interno del percorso, è possibile specificare una o più istanze delle variabili di 2 seguenti:<BR>{date}, {time}<BR>Esempio 1: products/{date}/{time}/product-list.csv<BR>Esempio 2: products/{date}/product-list.csv
</tr>
<tr>
<td>Formato di data [facoltativo]</td>
<td>Se è stata utilizzata {date} all'interno del criterio percorso specificato, è possibile selezionare il formato di data in cui sono organizzati i file dall'elenco a discesa dei formati supportati. Esempio: Gg/MM/aaaa</td>
</tr>
<tr>
<td>Formato ora [facoltativo]</td>
<td>Se è stata utilizzata {time} all'interno del criterio percorso specificato, è possibile selezionare il formato di ora in cui sono organizzati i file dall'elenco a discesa dei formati supportati. Esempio: HH</td>
</tr>
<tr>
<td>Formato di serializzazione evento</td>
<td>Per assicurarsi che le query nel modo desiderato, è necessario sapere quale formato di serializzazione Analitica flusso di lavoro che si usa per i flussi di dati in arrivo. Per i dati di riferimento, i formati supportati sono CSV e JSON.</td>
</tr>
<tr>
<td>Codifica</td>
<td>UTF-8 è l'unico formato di codifica supportato in questa fase</td>
</tr>
</tbody>
</table>

## <a name="generating-reference-data-on-a-schedule"></a>Generazione di dati di riferimento in una pianificazione

Se i dati di riferimento sono un set di dati di modificato lenta, il supporto per l'aggiornamento di riferimento dati sono abilitati specificando un motivo percorso nella configurazione di input utilizzando la data di {} e {ora} token di sostituzione. Flusso Analitica acquisirà le definizioni di dati di riferimento aggiornato in base a questo modello di percorso. Ad esempio, un modello di `sample/{date}/{time}/products.csv` con un formato di data di tempo e un **"YYYY-MM-DD"** formato **"Hh"** indica Analitica flusso per sollevare il blob aggiornato `sample/2015-04-16/17:30/products.csv` nelle 17.30 in aprile 2015 16 UTC il fuso orario.

> [AZURE.NOTE] Attualmente processi flusso Analitica cercare l'aggiornamento di blob solo quando l'ora codificato in nome blob Sposta l'ora del computer. Ad esempio il processo cercherà `sample/2015-04-16/17:30/products.csv` non appena possibile ma n precedenza rispetto le 17.30 in aprile 2015 16 UTC il fuso orario. Verrà *mai* aspetto per un file con un tempo codificato precedente a quello precedente individuate.
> 
> Ad esempio Dopo il processo trova il blob `sample/2015-04-16/17:30/products.csv` l'espressione ignorerà tutti i file con una data codificato in precedenza le 17.30 aprile 2015 16 pertanto se un ricevuti in ritardo `sample/2015-04-16/17:25/products.csv` blob viene creato nello stesso contenitore il processo non verrà utilizzate.
> 
> Allo stesso modo se `sample/2015-04-16/17:30/products.csv` viene generato solo ore 10:03 aprile 2015 16 ma alcun blob di con una data precedente non è presente nel contenitore, il processo verrà utilizzare questo file iniziando 10:03 PM aprile 2015 16 e usare i dati di riferimento precedente nel frattempo.
> 
> Un'eccezione quando il processo deve rielaborare dati indietro nel tempo o il processo è il primo avvio. Ora di inizio che il processo sta cercando il più recente blob prodotto prima il processo ora di inizio specificata. In questo modo per verificare che sia presente un set di dati di riferimento **non vuoto** quando si avvia il processo. Se non è possibile trovarne uno, il processo verrà visualizzata la diagnostica seguente: `Initializing input without a valid reference data blob for UTC time <start time>`.


[Azure dati Factory](https://azure.microsoft.com/documentation/services/data-factory/) può essere utilizzata per poter gestire le attività di creazione di BLOB aggiornato richieste dal flusso Analitica per aggiornare le definizioni di dati di riferimento. Dati Factory è un servizio di integrazione di dati basate su cloud che coordina e consente di automatizzare lo spostamento e trasformazione dei dati. Dati Factory supporta [la connessione a un numero elevato di basato sul cloud e archivi dati locali](../data-factory/data-factory-data-movement-activities.md) e spostamento di dati con facilità a intervalli regolari specificate. Per ulteriori informazioni e istruzioni dettagliate su come configurare una pipeline di dati Factory per generare i dati di riferimento per Analitica flusso che viene aggiornata in base alla pianificazione predefiniti, consultare anche in questo [esempio GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## <a name="tips-on-refreshing-your-reference-data"></a>Suggerimenti per l'aggiornamento dei dati di riferimento ##

1. Sovrascrivere BLOB di dati di riferimento non impedirà Analitica flusso ricaricare il blob e in alcuni casi è possibile che il processo di esito negativo. Il modo consigliato per modificare i dati di riferimento consiste nell'aggiungere un nuovo blob tramite il contenitore stesso motivo percorso definito nell'input processo e utilizzare una data/ora **maggiore** di quello specificato dall'ultimo blob nella sequenza.
2.  Dati di riferimento BLOB non vengono ordinati al momento del blob "Data ultima modifica", ma solo dalla data specificata nel blob e l'ora assegnare un nome utilizzando la data di {} e {ora} sostituzioni.
3.  In alcuni casi, che un processo necessario tornare indietro nel tempo, pertanto BLOB di dati di riferimento non devono essere modificato o eliminati.

## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
È stato introdotto di flusso Analitica, un servizio gestito per lo streaming analitica su dati provenienti da Internet di elementi. Per ulteriori informazioni su questo servizio, vedere:

- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
