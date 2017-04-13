<properties 
    pageTitle="Introduzione al flusso Analitica | Microsoft Azure" 
    description="Informazioni sulle flusso Analitica, un servizio che consente di analizzare i dati del flusso da Internet di elementi (IoT) in tempo reale." 
    keywords="analitica come un servizio gestito servizi, elaborazione di flusso, streaming analitica, che cos'è analitica flusso"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>


# <a name="what-is-stream-analytics"></a>Che cos'è Analitica flusso?

Azure flusso Analitica è un motore di elaborazione completamente gestita e conveniente eventi in tempo reale che consente di sbloccare complete informazioni dettagliate sui dati. Flusso Analitica semplifica impostare i calcoli analitici in tempo reale in streaming da dispositivi, sensori, siti web, social media, applicazioni, sistemi di infrastruttura e altre dei dati.

Con pochi clic nel portale di Azure, è possibile creare un processo di flusso Analitica specificando l'origine di input di dati multimediali sink output per i risultati del processo di e una trasformazione dati espresso in linguaggio SQL. È possibile monitorare e regolare la scala/velocità del processo di nel portale di Azure scalare da pochi kilobyte a un gigabyte o più degli eventi elaborati al secondo.

Flusso Analitica si basa su anni di lavoro di Microsoft Research per lo sviluppo di altamente ottimizzati streaming motori per l'elaborazione di urgenti, oltre che le integrazioni di lingua per le specifiche intuitive di tali.

## <a name="what-can-i-use-stream-analytics-for"></a>Cosa è possibile usare Analitica flusso per
Grandi quantità di dati vengono trasferiti ad alta velocità rete oggi. Le organizzazioni che possono elaborare e agire su questo flusso dei dati in tempo reale notevolmente possono migliorare l'efficienza e distinguere tra loro sul mercato. Scenari di analitica streaming in tempo reale sono disponibili in tutti i settori: analisi trading azionario personalizzate e in tempo reale e avvisi offerti da società di servizi finanziari; rilevamento di frodi in tempo reale; servizi di protezione di dati e dell'identità; acquisizione affidabile e analisi dei dati generati da sensori e attuatori incorporati in oggetti fisici (Internet di elementi o IoT); analitica clickstream Web; e le applicazioni di gestione (CRM) relazione cliente inviare gli avvisi quando è ridotto analisi utilizzo software all'interno di un intervallo di tempo. Aziende siano cercando il modo più flessibile, affidabile e conveniente per eseguire tali analisi dei dati in tempo reale del flusso di eventi a buon fine nel mondo competitivo business moderno.

## <a name="key-capabilities-and-benefits"></a>Vantaggi e funzionalità principali
-   **Facilità di utilizzo:** Flusso Analitica supporta un modello di query semplice e dichiarativo per descrivere le trasformazioni. Per ottimizzare per semplificarne l'utilizzo, Analitica flusso utilizza un valore variant T-SQL ed elimina la necessità per i clienti di gestire i problemi tecnici del flusso di sistemi di elaborazione. Nell'editor di query nel browser, utilizzando il [linguaggio di query flusso Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx) , viene visualizzato IntelliSense senso completamento automatico per rapidamente e facilmente implementare ora serie query, inclusi temporale basate su join, aggregazioni in finestre, filtri temporali e altre operazioni comuni, ad esempio join, aggregazioni, previsioni e filtri. Inoltre, il test rispetto a un file di dati di esempio di query nel browser consente lo sviluppo rapido e iterativo.  

-   **Scalabilità:** Flusso Analitica è in grado di gestire la velocità effettiva eventi di fino a 1GB/secondo. Integrazione con [Gli hub evento Azure](https://azure.microsoft.com/services/event-hubs/) e [Azure IoT hub](https://azure.microsoft.com/services/iot-hub/) consentire l'acquisizione milioni di eventi al secondo proveniente da dispositivi collegati, clickstreams, la soluzione e file di registro, per citarne alcune. Per eseguire questa operazione, flusso Analitica utilizza la funzionalità di partizione di hub di evento, che è possibile ottenere 1 MB/s per partizione. Gli utenti sono in grado di suddividere il calcolo in un numero di passaggi logici all'interno della definizione di query, ognuna con la possibilità di essere ulteriormente suddivisi per aumentare la scalabilità.  

-   **L'affidabilità, ripetibilità e ripristino rapido:** Un servizio gestito nel cloud, flusso Analitica consente di evitare perdite di dati e continuità aziendale in caso di errori attraverso le funzionalità di ripristino predefiniti. La possibilità di mantenere internamente lo stato, il servizio fornisce risultati ripetibili garantire che è possibile archiviare eventi e riapplicare in futuro, sempre visualizzato lo stesso risultato di elaborazione. In questo modo ai clienti di tornare indietro nel tempo e provare a utilizzare i calcoli quando si esegue l'analisi delle cause principali, analisi di simulazione e così via.  

-   **Costo:** Come un servizio cloud, flusso Analitica ottimizzata per offrire agli utenti molto basso costo per procedere e gestione di soluzioni in tempo reale analitica. Il servizio è progettato per il pagamento durante il lavoro in base sull'utilizzo dell'unità di flusso e la quantità di dati elaborati dal sistema di. L'uso deriva in base al volume di eventi elaborati e la quantità di grande potenza viene completato il provisioning all'interno del cluster per gestire i rispettivi processi di flusso Analitica.  

-   **Fanno riferimento a dati:** Flusso Analitica consente agli utenti di specificare e utilizzare dati di riferimento. Può trattarsi di dati cronologici o dati semplicemente non di flusso che vengono modificati raramente nel tempo. Il sistema semplifica l'utilizzo di dati di riferimento considerati come qualsiasi altro flusso di evento in arrivo di collaborare con altri flussi di eventi caricamento in tempo reale per eseguire trasformazioni.  

-   **Funzioni definite dall'utente:** Flusso Analitica con integrazione con Azure apprendimento per definire le chiamate di funzione nel servizio di apprendimento come parte di una query flusso Analitica. Consente di espandere le funzionalità di flusso Analitica per sfruttare le soluzioni di apprendimento Azure esistenti. Per ulteriori informazioni, esaminare l' [esercitazione integrazione di apprendimento](stream-analytics-machine-learning-integration-tutorial.md).

-   **Connettività:** Flusso Analitica connesso direttamente a hub evento Azure e Azure IoT hub di acquisizione di flusso e il servizio Blob Azure per acquisire i dati cronologici. I risultati possono essere scritti dal flusso Analitica per lo spazio di archiviazione BLOB o tabelle, DB di SQL Azure, archivi Lake dati di Azure, DocumentDB, hub di evento, Azure servizio Bus argomenti o code e Power BI, in cui può quindi essere visualizzato, ulteriormente elaborata dai flussi di lavoro, usata in analitica batch tramite [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) o rielaborare come una serie di eventi. Quando si utilizza evento hub è possibile comporre più Analitica flusso insieme ad altre origini dati e i motori di elaborazione senza perdere la natura trasmissione dei calcoli.  

## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
È stato introdotto di flusso Analitica, un servizio gestito per lo streaming analitica su dati provenienti da Internet di elementi. Per ulteriori informazioni su questo servizio, vedere:

- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)

