<properties 
    pageTitle="Considerazioni sulle prestazioni e ottimizzazione di Azure ricerca | Microsoft Azure" 
    description="Ottimizzare le prestazioni di ricerca di Azure e configurare scala ottimale" 
    services="search" 
    documentationCenter="" 
    authors="LiamCavanagh" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="liamca"/>

# <a name="azure-search-performance-and-optimization-considerations"></a>Considerazioni sulle prestazioni e ottimizzazione di Azure ricerca

Un'esperienza di ricerca ideale è una chiave di successo per molti dispositivi mobili e le applicazioni web. Da immobili, Marketplace Auto ai cataloghi online, utilizzare la ricerca rapida e i risultati pertinenti vengono influenzata dall'esperienza dei clienti. In questo documento è destinato a Guida individuare rapidamente procedure consigliate per la procedura ottenere il massimo vantaggio Azure cercare, in particolare scenari avanzati con sofisticate requisiti per scalabilità, in più lingue supportano o classificazione personalizzata.  Inoltre, il documento illustra elementi interni e illustra approcci che funzionano in modo efficace nelle App clienti reali.

## <a name="performance-and-scale-tuning-for-search-services"></a>Prestazioni e ottimizzazione delle scala per i servizi di ricerca

Stiamo tutti per utilizzato come Bing e Google e prestazioni elevate che offrono motori di ricerca.  Di conseguenza, quando i clienti Usa il web basate su ricerca o applicazione per dispositivi mobili, si verranno aspettano caratteristiche di prestazioni simili.  Quando si ottimizza per le prestazioni della ricerca, è uno dei metodi migliori per concentrarsi su latenza, ovvero il tempo che necessario per completare e restituire i risultati di una query.  Quando si ottimizza per la latenza di ricerca, è importante:

1. Selezionare una latenza di destinazione o intervallo di tempo massimo, che richiedono una ricerca tipica dovrebbe eseguire per completare.

2. Creare e testare un carico di lavoro reale per il servizio di ricerca con un set di dati realistici misurare le tariffe latenza.

3. Iniziare con un numero limitato di query al secondo (QPS) e continuare aumentare il numero di esecuzione del test finché la latenza delle query inserisce sotto la latenza di destinazione definito.  Si tratta di un benchmark importanti per la pianificazione per scala man mano che cresce l'applicazione in uso.

4. Se possibile, riutilizzare connessioni HTTP.  Se si utilizza Azure ricerca .NET SDK, significa che è necessario riutilizzare un'istanza o [SearchIndexClient](https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchindexclient.aspx) e se si utilizza l'API REST, è necessario riutilizzare un singolo HttpClient.
 
Durante la creazione di questi test carichi di lavoro, esistono alcune caratteristiche di ricerca di Azure da tenere presenti:

1. È possibile push in modo ricerca molti una query in una sola volta, le risorse disponibili nel servizio di ricerca di Azure verranno incontrare molte.  In questo caso, sarà possibile visualizzare i codici di risposta HTTP 503.  Per questo motivo, è consigliabile iniziare con vari intervalli di richieste di ricerca per visualizzare le differenze tra i periodi di latenza mentre si aggiungono ulteriori richieste di ricerca.

2. Caricamento di contenuto per la ricerca di Azure impatto le prestazioni complessive e latenza del servizio di ricerca di Azure.  Se si prevede di inviare dati mentre gli utenti eseguono ricerche, è importante sfruttare il carico di lavoro in considerazione i test.

3. Non tutte le query di ricerca verrà eseguita automaticamente allo stesso livello di prestazioni.  Ad esempio un suggerimento di ricerca o per la ricerca di documento generalmente verrà eseguite più velocemente di quanto una query con un numero significativo di facet e filtri.  È consigliabile eseguire varie query che si prevede di visualizzare in considerazione durante la creazione di test.  

4. Variazione di richieste di ricerca è importante perché se si esegue continuamente e le richieste di ricerca stesso, memorizzazione nella cache dei dati verrà avviato conferire un aspetto migliore di potrebbe con una query più disparata impostata prestazioni.

> [AZURE.NOTE] [Il test di Visual Studio carico](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) è molto utile per eseguire il test di valutazione in quanto consente di eseguire le richieste HTTP in base alle esigenze per l'esecuzione di query su ricerca Azure e consente la parallelizzazione delle richieste.

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>Ridimensionamento Azure cercare tariffe query ad alta e limitato richieste

Quando si ricevono le richieste di accelerata troppi o superano le tariffe latenza di destinazione da un carico di query avanzata, è possibile esaminare per ridurre le tariffe latenza in uno dei due modi:

1. **Aumentare repliche:**  Una replica è simile a una copia dei dati che consente di ricerca di Azure a carico con più copie.  Tutto il bilanciamento del carico e la replica dei dati tra le repliche viene gestita dal servizio di ricerca di Azure ed è possibile modificare il numero di repliche allocata per il servizio in qualsiasi momento.  Può allocare fino a 12 repliche in un servizio di ricerca Standard e 3 repliche in un servizio di ricerca di base.  È possibile regolare repliche operazione dal [Portale di Azure](search-create-service-portal.md) o [API di gestione di ricerca di Azure](search-get-started-management-api.md).

2. **Aumentare il livello di ricerca:**  Ricerca di Azure viene fornito un [numero di livelli](https://azure.microsoft.com/pricing/details/search/) e ognuna di questi livelli offre diversi livelli di prestazioni.  In alcuni casi, potrebbe essere così tanti query che il livello che ci si trova nel non può fornire tariffe sufficientemente bassa latenza, anche quando sono il limite massimo repliche.  In questo caso, è consigliabile valutare la possibilità di utilizzo di uno dei livelli di ricerca superiori, ad esempio il livello di Azure ricerca S3 è adatto per gli scenari di un numero elevato di documenti e query estremamente elevati carichi di lavoro.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>Ridimensionamento Azure cercare singole query lenta

Un altro motivo perché tariffe latenza possono essere lente è stato inviato da una singola query impiegando troppo tempo.  In questo caso, aggiunta di repliche non migliora le tariffe latenza.  In questo caso sono disponibili due opzioni:

1. **Aumentare le partizioni** Una partizione è un meccanismo di suddivisione dei dati tra le risorse aggiuntive.  Per questo motivo, quando si aggiunge una seconda partizione Ottiene dividere i dati in due.  Una terza partizione divide l'indice in tre e così via.  Questo è installato anche l'effetto che in alcuni casi, le query lente eseguiranno più rapidamente a causa di parallelizzazione di calcolo.  Sono disponibili alcuni esempi di nel punto in cui abbiamo visto questo parallelizzazione funziona molto bene con le query che hanno query selettività bassa.  Si tratta di query che corrispondono a molti documenti o quando è necessario che faceting fornire conteggi tramite un numero elevato di documenti.  Poiché non esistono molte calcolo necessario per punteggio pertinenza dei documenti o per contare il numero di documenti, aggiunta partizioni aggiuntive consentono di fornire ulteriore calcolo.  

   Può essere un massimo di 12 partizioni nel servizio di ricerca Standard e 1 partizione nel servizio di ricerca di base.  È possibile regolare le partizioni del [Portale di Azure](search-create-service-portal.md) o con l' [API di gestione di ricerca di Azure](search-get-started-management-api.md)da.

2. **Limitare campi cardinalità alta:** Un campo di cardinalità elevato è costituito da un facetable o possibile filtrare campo che contiene un numero significativo di valori univoci e di conseguenza, impiega molte risorse per calcolare i risultati.   Ad esempio, l'impostazione di un campo ID prodotto o la descrizione come facetable/supportano consentirebbe per cardinalità elevata poiché la maggior parte dei valori da documento al documento sono univoca. Se possibile, limitare il numero di campi di cardinalità elevato.

3. **Aumentare il livello di ricerca:**  Spostare fino a un livello superiore di Azure ricerca può essere un altro modo per migliorare le prestazioni delle query lenta.  Ogni livello superiore vengono forniti anche processore CPU e memoria che può avere un impatto positivo sulle prestazioni di query.

## <a name="scaling-for-availability"></a>Modifica delle dimensioni della disponibilità

Repliche non solo ridurre la latenza delle query ma consentono inoltre di disponibilità.  Con una singola replica, è necessario attendere periodici inattività riavvio del server dopo aggiornamenti software o altri eventi manutenzione dei servizi che si verifichi.  Di conseguenza, è importante tenere conto quando l'applicazione richiede disponibilità delle ricerche, le query, oltre al tipo di scrittura (eventi indicizzazione).  Ricerca Azure offre opzioni di contratto di servizio in tutte le offerte di ricerca a pagamento con gli attributi seguenti:

- 2 repliche per disponibilità dei carichi di lavoro di sola lettura (query)
- 3 o più repliche disponibilità elevata di carichi di lavoro di sola lettura (query e indicizzazione)

Per ulteriori informazioni, visitare il [Contratto di servizio ricerca Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Poiché repliche sono copie dei dati, più repliche consente la ricerca di Azure per lavorare il riavvio di manutenzione su uno replica alla volta consentendo query continuare a essere eseguita su sé.  Per questo motivo, è necessario considerare come il tempo di inattività potrebbe influire negativamente le query che è necessario eseguire una meno copia dei dati.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>Ridimensionamento distribuito geografico carichi di lavoro e fornire la ridondanza geografico

Per carichi di lavoro distribuito geografico, si noterà che gli utenti trovano lontano il centro di dati in cui è ospitato il servizio di ricerca di Azure avranno la velocità di latenza.  Per questo motivo, è spesso importante avere più servizi di ricerca in aree incluse nella maggiore prossimità a questi utenti.  Ricerca Azure attualmente non forniscono un metodo automatico di replica geografico gli indici di ricerca di Azure attraverso aree, ma sono disponibili alcune tecniche che possono essere usate che possono semplificare il processo implementare e gestire. Questi sono descritti nelle sezioni successive.

L'obiettivo di un insieme distribuito geografico di servizi di ricerca è disporre di due o più indici disponibili in due o più aree in cui un utente verrà reindirizzato al servizio di ricerca di Azure che fornisce la latenza più bassa, come illustrato in questo esempio:

   ![Incrociati dei servizi per area geografica][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Mantenere sincronizzati i dati più servizi di ricerca di Azure

Sono disponibili due opzioni per mantenere i servizi di ricerca distribuito aggiornati costituiti da utilizzando l' [Indicizzatore di ricerca di Azure](search-indexer-overview.md) o l'API Push (denominato anche l' [API REST di Azure ricerca](https://msdn.microsoft.com/library/dn798935.aspx)).  

### <a name="azure-search-indexers"></a>Ricerca Azure indicizzatori

Se si utilizza l'indicizzatore di ricerca di Azure, si stanno importando già modifiche ai dati da un archivio centrale dati, ad esempio DB di SQL Azure o DocumentDB. Quando si crea una nuovo servizio di ricerca è sufficiente anche creare un nuovo indicizzatore di ricerca di Azure per tale servizio che punta a questo stesso archivio di dati. In questo modo, ogni volta che nuove modifiche entrano nell'archivio dati, vengono quindi indicizzati dagli indicizzatori diversi.  

Ecco un esempio di tale architettura aspetto che avrà.

   ![Singola origine dati con indicizzatore distribuito e le combinazioni di servizio][2]


### <a name="push-api"></a>API push 
Se si utilizza l'API Push Azure ricerca per [aggiornare il contenuto dell'indice di ricerca di Azure](https://msdn.microsoft.com/library/dn798930.aspx), è possibile mantenere aggiornati i diversi servizi di ricerca premendo modifiche a tutti i servizi di ricerca ogni volta che è necessario un aggiornamento.  In tal caso è importante per assicurarsi che gestire casi in cui si verifica un errore di un aggiornamento al servizio di ricerca di uno e uno o più aggiornamenti esito positivo.

## <a name="leveraging-azure-traffic-manager"></a>Utilizzo di gestore del traffico Azure

[Gestione il traffico Azure](../traffic-manager/traffic-manager-overview.md) consente di inoltrare richieste a più siti Web si trova geografico quindi supportati dagli più servizi di ricerca di Windows Azure.  Uno dei vantaggi di gestione del traffico è che è possibile ricercare ricerca Azure per garantire che siano disponibile e indirizzare gli utenti ai servizi di ricerca alternativo in caso di inattività.  Inoltre, se si invia richieste di ricerca a siti Web di Azure, gestore del traffico Azure consente agli utenti di caricare casi saldo nel punto in cui il sito Web è la ma non Azure ricerca.  Ecco un esempio di quali l'architettura che utilizza il traffico Manager.

   ![Incrociati dei servizi per area geografica, con gestore del traffico centrale][3]

## <a name="monitoring-performance"></a>Monitoraggio delle prestazioni

Ricerca Azure offre la possibilità di analizzare e monitorare le prestazioni del servizio tramite [Ricerca il traffico Analitica (STA)](search-traffic-analytics.md). Tramite STA, è possibile registrare facoltativamente operazioni di ricerca singoli, nonché metriche aggregate in un account di archiviazione Azure che possa essere elaborato per l'analisi o visualizzato in Power BI.  Utilizzando la metrica STA, è possibile esaminare le statistiche sulle prestazioni, ad esempio numero medio di query o i tempi di risposta query.  Inoltre, la registrazione di operazioni consente di drill-down dei dettagli delle operazioni di ricerca specifici.

STA è uno strumento efficace per comprendere le tariffe latenza da questo punto di vista di ricerca di Azure.  Poiché le metriche di prestazioni di query ha eseguito l'accesso sono basate sul tempo di una query l'elaborazione di Azure ricerca completa (al momento della che richiesta al momento dell'invio), in grado di utilizzare questo comando per determinare se i problemi di latenza sono relativi al lato servizio di ricerca di Azure o all'esterno del servizio, ad esempio da latenza di rete.  

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui limiti dei livelli e servizi prezzi per ognuno di essi, vedere [limiti del servizio di ricerca di Azure](search-limits-quotas-capacity.md).

Visitare [la pianificazione della capacità](search-capacity-planning.md) di altre informazioni sulle combinazioni di partizione e replica.

Per ulteriori il drill-down sulle prestazioni e per alcune dimostrazioni di come implementare le ottimizzazioni descritte in questo articolo, guardare il video seguente:

> [AZURE.VIDEO azurecon-2015-azure-search-best-practices-for-web-and-mobile-applications]

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png