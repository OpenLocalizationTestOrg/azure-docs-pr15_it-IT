<properties
    pageTitle="Scegliere uno SKU o prezzi livello per la ricerca di Azure | Microsoft Azure"
    description="Ricerca Azure può effettuare il provisioning in queste SKU: gratuita, base e Standard, in cui Standard è disponibile in varie configurazioni di risorse e livelli di capacità."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="10/24/2016"
    ms.author="heidist"/>

# <a name="choose-a-sku-or-pricing-tier-for-azure-search"></a>Scegliere uno SKU o prezzi livello per la ricerca di Azure

Risultati di ricerca di Azure, un [servizio viene eseguito il provisioning](search-create-service-portal.md) in un livello di prezzo specifico o SKU. Opzioni includono **gratuito**, **base**o **Standard**, in cui **Standard** è disponibile in più configurazioni e capacità. 

Lo scopo di questo articolo è consente di selezionare un livello. Se la capacità del livello risulta troppo basso, sarà necessario eseguire il provisioning di un nuovo servizio al livello superiore e quindi ricaricare gli indici. Esiste un aggiornamento sul posto dello stesso servizio da una SKU a un'altra. 

> [AZURE.NOTE] Dopo aver scelto un livello e [disposizione un servizio di ricerca](search-create-service-portal.md), è possibile aumentare duplicata e partizione conta all'interno del servizio. Per ulteriori informazioni, vedere [livelli di risorse scala per la query e indicizzazione carichi di lavoro](search-capacity-planning.md).

## <a name="how-to-approach-a-pricing-tier-decision"></a>Come approccio una decisione livello prezzi

Risultati di ricerca di Azure, il livello determina la capacità, non disponibilità delle funzionalità. Funzionalità disponibili in genere, ogni livello, incluse le funzionalità di anteprima. L'unica eccezione è disponibile alcun supporto per gli indicizzatori S3 HD.

> [AZURE.TIP] Si consiglia di provisioning sempre un servizio **gratuito** (uno per sottoscrizione con nessuna scadenza) in modo che il relativo immediatamente disponibili per i progetti disattivo. Utilizzare il servizio **gratuito** per la verifica e la valutazione. creare un secondo servizio fatturabile il livello di **base** o **Standard** di produzione o carichi di lavoro test maggiori.

Capacità e i costi dell'esecuzione del servizio passare mano in mano. In questo articolo informazioni è possibile decidere quali SKU offre il giusto equilibrio, ma per uno dei risulti utile, è necessario almeno approssimative stime relative alle operazioni seguenti:

- Numero e la dimensione degli indici che si intende creare
- Numero e dimensioni dei documenti da caricare
- Un'idea del volume di query, in termini di query Per secondo (QPS)

Numero e le dimensioni sono importanti perché limiti massimi raggiunti tramite un limite il conteggio di indici o i documenti in un servizio, o sulle risorse (spazio di archiviazione o repliche) utilizzate dal servizio. Il limite effettivo per il servizio è qualunque viene utilizzato per primo: risorse o oggetti.

Con le stime dei costi mano, la procedura seguente deve semplificare il processo:

- **Passaggio 1** Esaminare le descrizioni SKU sotto per informazioni sulle opzioni disponibili.
- **Passaggio 2** Rispondere alle domande seguenti per ottenere una decisione preliminare.
- **Passaggio 3** Completare le regole esaminando dischi limiti per lo spazio di archiviazione e prezzi.

## <a name="sku-descriptions"></a>Descrizione degli elementi SKU

Nella tabella seguente vengono fornite descrizioni di ogni livello. 

Livello|Scenari principali
----|-----------------
**Libera**|Servizi condivisi, senza costi aggiuntivi, utilizzato per la valutazione, indagini o small carichi di lavoro. Perché è condiviso con altri utenti sottoscrittori, effettiva query e indicizzazione variano in base chi altro sta utilizzando il servizio. Capacità è piccola (50 MB o 3 indici con la 10.000 documenti ogni).
**Base**|Carichi di lavoro di produzione di piccole dimensioni sull'hardware dedicato. Disponibilità. Capacità è fino a 3 repliche e 1 (2 GB).
**S1**|1 standard supporta combinazioni flessibile delle partizioni (12) e repliche (12), usate per carichi di lavoro di produzione medio su hardware dedicato. È possibile assegnare le partizioni e repliche combinazioni supportate da un numero massimo di unità di ricerca fatturabili 36. Questo livello QPS è di circa 15 query al secondo le partizioni sono 25 GB.
**S2**|2 standard esegue carichi di lavoro di produzione maggiori usando le stesse unità di 36 ricerca S1 ma con repliche e partizioni di dimensioni più grandi. Questo livello QPS è di circa 60 query al secondo le partizioni sono 100 GB.
**S3**|3 standard viene eseguito in modo proporzionale più grandi carichi di lavoro di produzione superiore sistemi di fine, in configurazioni di tutte le 12 partizioni o 12 repliche unità di ricerca in 36. Questo livello QPS è superiore a 60 query al secondo le partizioni sono 200 GB. 
**S3 HD**|Densità alta 3 standard è progettato per un numero elevato di indici più piccoli. È possibile impostare un massimo di 3 partizioni, 200 GB. QPS è superiore a 60 query al secondo. 

> [AZURE.NOTE] Replica e partizione massimi vengono addebitate fuori come unità di ricerca (36 unità massime per ogni servizio), che impone un limite inferiore efficace rispetto a cosa implica il valore massimo al valore nominale. Ad esempio, per utilizzare il numero massimo di 12 repliche, potrebbe avere al massimo 3 partizioni (12 * 3 = 36 unità). Analogamente, per usare le partizioni massime, ridurre repliche 3. Vedere [livelli di risorse scala per la query e indicizzazione i carichi di lavoro di Azure ricerca](search-capacity-planning.md) per un grafico in combinazioni consentite.

## <a name="review-limits-per-tier"></a>Limiti di revisione per ogni livello

Il diagramma seguente è un sottoinsieme dei limiti di [Limiti del servizio di ricerca di Azure](search-limits-quotas-capacity.md). Vengono elencati i fattori maggiormente effetti in termini di una decisione SKU. È possibile fare riferimento a questo grafico durante la revisione domande riportate di seguito.

Risorsa|Libera|Base|S1|S2|S3 |S3 HD
---|---|---|---|----|---|----
Contratto di servizio (contratto di servizio)|Nessun <sup>1</sup> |Sì |Sì  |Sì |Sì  |Sì 
Limiti di indice analitico|3|5|50|200|200|1000 <sup>2</sup>
Limiti di documento|10.000 totale|1 milione di servizio|15 milione partizione |60 milioni per partizione|120 milione partizione |1 milione di indice analitico
Massime partizioni|N/D |1 |12  |12 |12|3 <sup>2</sup>
Dimensione partizione|50 MB totale|2 GB per ogni servizio|25 GB per partizione |100 GB per partizione (fino a un massimo di 1,2 TB per ogni servizio)|200 GB per partizione (fino a un massimo di 2,4 TB per ogni servizio)|200 GB (fino a un massimo di 600 GB per ogni servizio)
Massime repliche|N/D |3 |12 |12 |12|12
Query al secondo|N/D|~ 3 per ogni replica|~ 15 / replica|~ 60 per replica|> 60 per replica|> 60 per replica

<sup>1</sup> gratuito e SKU di anteprima non vengono forniti con contratti di servizio. Contratti di servizio vengono applicati quando uno SKU diventa disponibile.

<sup>2</sup> S3 e S3 HD sono supportati dall'infrastruttura identiche ad alta capacità ma ognuno di essi raggiunge il limite massimo in modi diversi. S3 è destinato a un numero minore di indici di dimensioni molto grandi. In questo caso, il limite massimo si limite delle risorse (2,4 TB per ogni servizio). S3 HD è destinato a un numero elevato di indici molto piccoli. 1.000 indici, S3 HD raggiungere i limiti sotto forma di vincoli di indice. Se si è un cliente S3 HD che richiede più di 1.000 indici, contattare il supporto Microsoft per informazioni su come procedere.

## <a name="eliminate-skus-that-dont-meet-requirements"></a>Eliminare SKU che non soddisfano i requisiti 

Le domande seguenti consentono di ottenere la decisione SKU giusta per il carico di lavoro.

1. Si dispone di requisiti di **Livello contratto di servizio** ? Limitare la decisione SKU di base o Anteprima di stampa non Standard.
2. **Il numero degli indici** eseguire è necessario? Una delle variabili principali suddivisione una decisione SKU è il numero di indici supportati da ogni SKU. Il supporto di indice è nettamente diversi livelli nei livelli prezzi inferiori. Requisiti di numero di indici potrebbero essere determinante di una decisione SKU.
3. **Il numero di documenti** verrà caricato in ogni indice? Il numero e le dimensioni dei documenti consente di verificare le dimensioni finali dell'indice. Supponendo che è possibile stimare la dimensione prevista dell'indice, è possibile confrontare tale numero con la dimensione della partizione per SKU estesa del numero di partizioni richieste per archiviare un indice di tale dimensione. 
4. **Che cos'è il carico di query prevista**? Una volta vengano interpretati requisiti di spazio di archiviazione, è consigliabile carichi di lavoro di query. S2 ed entrambi gli SKU S3 offrono velocità vicino equivalente, ma i requisiti di contratto di servizio verranno escludere tutti gli SKU di anteprima. 
5. Se si intende il livello di S2 o S3, determinare se è necessario installare [indicizzatori](search-indexer-overview.md). Indicizzatori non sono ancora disponibili per il livello di S3 HD. Alternativa consiste nell'usare un modello push per gli aggiornamenti di indice, in cui si scrive codice applicazione per inserire un set di dati a un indice.

La maggior parte dei clienti possono regola uno SKU specifico avanti o indietro in base alle relative risposte a queste domande. Se ancora non si sa quale SKU da allegare, è possibile pubblicare domande nei forum MSDN o StackOverflow oppure contattare il supporto di Azure per ulteriori informazioni.

## <a name="decision-validation-does-the-sku-offer-sufficient-storage-and-qps"></a>Decisione convalida: lo SKU offerta sufficiente spazio di archiviazione e QPS?

L'ultimo passaggio, rivedere i [prezzi pagina](https://azure.microsoft.com/pricing/details/search/) e le [sezioni per ogni servizio e - index in limiti di servizio](search-limits-quotas-capacity.md) per verificare le stime rispetto ai limiti di abbonamento e servizio. 

Se il prezzo o lo spazio di archiviazione requisiti sono compreso nell'intervallo, è consigliabile refactoring i carichi di lavoro tra più servizi più piccoli (ad esempio). Nel livello più granulare, Impossibile riprogettare indici per essere inferiori o utilizzare i filtri per rendere più efficiente query.

> [AZURE.NOTE] Requisiti di spazio di archiviazione possono risultare eccessivo ingranditi se i documenti contengono dati estranei. Se possibile, i documenti conterranno solo i dati che supportano le ricerche o i metadati. Dati binari non ricerche e devono essere memorizzati separatamente (ad esempio in una tabella o blob di archiviazione Azure) con un campo nell'indice di un URL di riferimento ai dati esterni. La dimensione massima di un singolo documento è 16 MB (o meno se ci si trova il caricamento di più documenti in una richiesta di massa). Per ulteriori informazioni, vedere [limiti del servizio di ricerca di Azure](search-limits-quotas-capacity.md) .

## <a name="next-step"></a>Passaggio successivo

Dopo aver appreso che SKU è più adatta, continuare con la procedura seguente:

- [Creare un servizio di ricerca nel portale](search-create-service-portal.md)
- [Modificare l'allocazione delle partizioni e di replica da ridimensionare il servizio](search-capacity-planning.md)

