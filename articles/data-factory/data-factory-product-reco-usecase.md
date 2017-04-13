<properties 
    pageTitle="Dati Factory Use Case - consigli sui prodotti" 
    description="Informazioni su un caso di utilizzo implementato tramite Azure Data Factory insieme altri servizi." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/01/2016" 
    ms.author="shlo"/>

# <a name="use-case---product-recommendations"></a>Use Case - consigli sui prodotti 

Azure Data Factory corrisponde a uno dei molti servizi utilizzati per implementare la famiglia di Business Intelligence Cortana di acceleratori.  Vedere [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics) pagina per informazioni dettagliate su questo gruppo. In questo documento vengono descritte un caso di utilizzo comune Azure utenti già risolti e implementato tramite Azure Data Factory e altri servizi componenti di Business Intelligence Cortana.

## <a name="scenario"></a>Scenario:

Rivenditori online, in genere è utile attirare clienti possono acquistare prodotti dal loro presentazione con i prodotti che sono maggiormente interessare e più probabili acquistare. A tale scopo, è necessario personalizzare l'esperienza online dell'utente tramite consigli sui prodotti personalizzate per l'utente specifico rivenditori online. Queste raccomandazioni personalizzate sono in base alle corrente e nella cronologia acquisti i dati sul comportamento, informazioni sui prodotti, marchi nuovi e dati segmentazione su prodotti e dei clienti.  Inoltre, forniscono i suggerimenti di prodotto utente in base all'analisi del comportamento l'utilizzo complessivo da tutti gli utenti combinate.

L'obiettivo di questi rivenditori consiste nel Ottimizza per le conversioni di utente fare clic su di vendita e ottenere maggiore fatturato.  Fornendo consigli product contestuale basati sul comportamento in base agli interessi dei clienti e le azioni che è possibile ottenere la conversione. In questo caso di utilizzo, serve rivenditori online come esempio di azienda che per ottimizzare i propri clienti. Tuttavia, tali principi per qualsiasi attività che desidera esercitare suoi clienti intorno propri prodotti e servizi e migliorare l'esperienza di acquisto dei clienti con consigli sui prodotti personalizzate.

## <a name="challenges"></a>Problemi

Esistono diversi problemi che faccia rivenditori online quando si tenta di implementare questo tipo di use case. 

Prima di tutto, è necessario acquisiti dati di forme e le dimensioni massime diverse da più origini dati, entrambi locale e nel cloud. Questi dati includono dati sui prodotti, i dati sul comportamento precedenti dei clienti e dati utente quando l'utente passa il sito di rivendita online. 

Consigli product personalizzata secondo devono essere adeguatamente veloce e accurato calcolati e previsti. Oltre al prodotto, marchio e clienti comportamento e browser di dati, i rivenditori online anche necessario includere commenti su Acquisti precedenti tenere in considerazione la determinazione delle raccomandazioni prodotto migliore per l'utente. 

Infine, i suggerimenti devono essere immediatamente risultato finali all'utente di fornire un'esplorazione semplice e acquisto esperienza e consigli più recenti e rilevanti. 

Infine, rivenditori necessario misurare l'efficacia delle loro approccio registrando complessiva offerta speciale e fare clic su alla conversione delle vendite successi incrociate e la modifica alla loro consigli futuri.

## <a name="solution-overview"></a>Panoramica della soluzione

In questo caso di utilizzo di esempio è stato risolto e implementato dagli utenti reale Azure con Azure Data Factory e altri servizi componenti di Business Intelligence di Cortana, inclusi [HDInsight](https://azure.microsoft.com/services/hdinsight/) e [Power BI](https://powerbi.microsoft.com/).

Rivenditore online utilizza un archivio Blob Azure, un server SQL locale, DB di SQL Azure e un mart dati relazionali come le opzioni di archiviazione dei dati in tutto il flusso di lavoro.  L'archivio blob contiene informazioni sui clienti, i dati sul comportamento dei clienti e dati informazioni sui prodotti. I dati di informazioni prodotto includono informazioni sul marchio prodotto e un prodotto catalogo archiviati in locale in un data warehouse SQL. 

Tutti i dati vengono combinati e inseriti in un sistema di recommendation prodotto per offrire i consigli personalizzati in base agli interessi dei clienti e alle azioni, mentre l'utente accede prodotti del catalogo nel sito Web. I clienti vedere anche prodotti correlati al prodotto che quelli visualizzati in base a criteri di utilizzo complessivo sito Web non associate a uno degli utenti.

![diagramma Use case](./media/data-factory-product-reco-usecase/diagram-1.png)

GB di file di log web non elaborati vengono generate ogni giorno dal sito Web del produttore online come file semistrutturati. I file di log web non elaborati e le informazioni del catalogo cliente e prodotto vengono acquisiti regolarmente in un'archiviazione Blob Azure con spostamento dei dati a livello globale distribuito dati Factory come servizio. I file di log non elaborato per il giorno sono suddivisi (per anno e mese) in archiviazione blob per lo spazio di archiviazione a lungo termine.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) viene utilizzato per dividere i file di registro non elaborati nell'archivio blob ed elaborare i registri acquisiti in scala utilizzando script Hive e maialino. Web partizionato registra dati quindi elaborati per estrarre gli input necessari per un sistema di suggerimento per generare i suggerimenti di prodotto personalizzate di apprendimento.

Il sistema di recommendation usato per l'apprendimento automatico in questo esempio è una piattaforma recommendation da [Apache Mahout](http://mahout.apache.org/)apprendimento automatico Apri origine.  Per lo scenario, è possibile applicare qualsiasi [Apprendimento Azure](https://azure.microsoft.com/services/machine-learning/) o un modello personalizzato.  Il modello Mahout viene utilizzato per prevedere le analogie tra le voci nel sito Web in base a criteri di utilizzo complessivo e generare i consigli personalizzati in base a un utente specifico.

Infine, set di risultati di consigli sui prodotti personalizzate viene spostato un mart dati relazionali consumo tramite il sito Web rivenditore.  Il set di risultati potrebbe anche essere accedervi direttamente dall'archiviazione blob da un'altra applicazione oppure spostato archivi aggiuntivi per altri utenti privati e casi di utilizzo.

## <a name="benefits"></a>Vantaggi

Ottimizzazione strategia recommendation prodotto e allineamento agli obiettivi aziendali, la soluzione soddisfatto commerciale del rivenditore online e obiettivi di marketing. Inoltre, sono in grado di rendono operativi e gestire il flusso di lavoro recommendation prodotto in modo efficiente, affidabile e conveniente. L'approccio tutta semplicità per poter aggiornare il modello e ottimizzare l'efficacia basata sulle misure di successi fare clic su alla conversione delle vendite. Utilizzando Azure Data Factory, erano abbandonare la gestione delle risorse di tempo e costosa cloud manuale e spostare la gestione delle risorse cloud su richiesta. Pertanto sono in grado di risparmiare tempo e denaro e ridurre il tempo per la distribuzione della soluzione. Visualizzazioni di derivazione dei dati e l'integrità dei servizi operativi diventato semplice visualizzare e risolvere i problemi con la gestione dell'interfaccia utente disponibile dal portale di Azure e il monitoraggio Factory dati intuitivo. A questo punto, la soluzione è possibile programmare e gestita in modo che dati terminata in modo affidabile generati e recapitati agli utenti e dati e le dipendenze di elaborazione vengono gestite automaticamente senza intervento.

Grazie a questa esperienza di acquisto personalizzata, rivenditore online creato un cliente più competitivo, interessante l'esperienza e pertanto aumentare la soddisfazione del cliente di vendita e generale.



  