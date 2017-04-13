<properties
    pageTitle="Modellazione rappresentato nella ricerca Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Informazioni sui modelli di progettazione comuni per le applicazioni SaaS multi-tenant durante l'uso di ricerca di Azure."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="10/26/2016"
    ms.author="ashmaka"/>

# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Progettare modelli per le applicazioni SaaS multi-tenant e ricerca di Azure

Un'applicazione multi-tenant corrisponde a uno che fornisce gli stessi servizi e funzionalità a qualsiasi numero di tenant che non è possibile visualizzare o condividere i dati di qualsiasi altro tenant. In questo documento vengono illustrate le strategie di isolamento tenant Applications multi-tenant integrato con Azure ricerca.

## <a name="azure-search-concepts"></a>Azure concetti di ricerca
Come una soluzione di ricerca come servizio ricerca Azure consente agli sviluppatori di aggiungere ricerca RTF esperienze alle applicazioni senza la gestione di qualsiasi infrastruttura o diventa un esperto nella ricerca. Dati vengono caricati al servizio e quindi archiviati nel cloud. Usa semplici richieste all'API di ricerca di Azure, i dati possono essere modificati e la ricerca. Una panoramica del servizio sono disponibili in [questo articolo](http://aka.ms/whatisazsearch). Prima di esaminare i modelli di progettazione, è importante conoscere alcuni concetti di ricerca di Azure.

### <a name="search-services-indexes-fields-and-documents"></a>Servizi di ricerca, indici, campi e i documenti
Quando si usa ricerca Azure, uno sottoscrive un _servizio di ricerca_. Come dati vengono caricati alla ricerca di Azure, questa viene archiviata in un _indice_ all'interno del servizio di ricerca. È possibile che un numero di indici all'interno di un singolo servizio. Per utilizzare i concetti già acquisiti di database, il servizio di ricerca può essere paragonato a un database mentre gli indici all'interno di un servizio possono essere paragonati a tabelle di un database.

Ogni indice all'interno di un servizio di ricerca dispone il proprio schema, definito da un numero di personalizzabili _campi_. Dati vengono aggiunti a un indice di ricerca di Azure sotto forma di singoli _documenti_. Ogni documento deve essere caricato in un determinato indice e deve essere schema dell'indice. Quando si cercano dati tramite ricerca Azure, le query di ricerca full-text vengono eseguite un indice specifico.  Per confrontare questi concetti a quelle di un database, campi possono essere paragonati a colonne in una tabella e i documenti possono essere paragonati a righe.

### <a name="scalability"></a>Scalabilità
Qualsiasi servizio di ricerca di Azure in Standard [prezzi livello](https://azure.microsoft.com/pricing/details/search/) può ridurre in due dimensioni: spazio di archiviazione e la disponibilità.
* _Le partizioni_ possono essere aggiunti per aumentare lo spazio di archiviazione di un servizio di ricerca.
* _Repliche_ possono essere aggiunti a un servizio per aumentare la velocità di richieste di tipo in grado di gestire un servizio di ricerca.

Aggiunta o la rimozione partizioni e repliche nella consentirà la capacità del servizio di ricerca per aumentare con la quantità di dati e le richieste di applicazione il traffico. Affinché un servizio di ricerca ottenere una [contratto di servizio](https://azure.microsoft.com/support/legal/sla/search/v1_0/)di lettura, richiede l'esecuzione di due repliche. In ordine di un servizio ottenere un [contratto di servizio](https://azure.microsoft.com/support/legal/sla/search/v1_0/)di lettura e scrittura, sarà necessario tre repliche.


### <a name="service-and-index-limits-in-azure-search"></a>Limiti di servizio e indice di ricerca di Azure
Esistono alcuni diversi [prezzi livelli](https://azure.microsoft.com/pricing/details/search/) nella ricerca di Azure, ognuno dei livelli è diversi [limiti e le quote di](search-limits-quotas-capacity.md). Alcuni di questi limiti sono al livello del servizio, alcune sono a livello di indice e alcune sono a livello di partizione.


|                                  | Base     | Standard1   | Standard2   | Standard3   | HD Standard3  |
|----------------------------------|-----------|-------------|-------------|-------------|---------------|
| Massime repliche per ogni servizio     | 3         | 12          | 12          | 12          | 12            |
| Massime partizioni per ogni servizio   | 1         | 12          | 12          | 12          | 1             |
| Unità massime ricerca (repliche * partizioni) per ogni servizio | 3         | 36          | 36          | 36          | 36 (max 3 partizioni)            |
| Numero massimo documenti per ogni servizio    | 1 milione | 180 milioni | 720 milioni | 1.4 miliardi | 600 milioni   |
| Massimo dello spazio di archiviazione per ogni servizio      | 2 GB      | 300 GB      | 1.2 TB      | 2,4 TB      | 600 GB        |
| Numero massimo documenti per partizione  | 1 milione | 15 milioni  | 60 milioni  | 120 milioni | 200 milioni   |
| Spazio di archiviazione massimo per partizione    | 2 GB      | 25 GB       | 100 GB      | 200 GB      | 200 GB        |
| Indici massimo per ogni servizio      | 5         | 50          | 200         | 200         | 3000 (max 1000 indici/partizione)          |


#### <a name="s3-high-density"></a>S3 Ad alta densità '
Nel livello prezzo S3 della ricerca di Azure, esiste un'opzione per la modalità (alta densità) progettata specificamente per scenari multi-tenant. In molti casi, è necessario un numero elevato di tenant più piccoli in un unico servizio di ottenere i vantaggi dell'efficienza semplicità e il costo di supporto.

S3 HD consente per gli indici small molti comprimere sotto la gestione di un servizio di ricerca solo dai commerciali la possibilità di ridurre gli indici utilizzando le partizioni per la possibilità di ospitare più indici in un unico servizio.

In concreto potrebbe avere un servizio S3 tra 1 e 200 indici che insieme potrebbero contenere fino a 1,4 miliardi documenti. Invece un HD S3 consentirebbe singoli indici per l'invio solo fino a 1 milione di documenti, ma è possibile gestire fino a 1000 indici per partizione (fino a 3000 per ogni servizio) con un numero intero documento di 200 milioni per partizione (fino a 600 milioni per ogni servizio).



## <a name="considerations-for-multitenant-applications"></a>Considerazioni per le applicazioni multi-tenant
Applicazioni multi-tenant devono distribuire in modo efficiente risorse tra i tenant mantenendo un livello di privacy tra vari tenant. Esistono alcune considerazioni durante la progettazione architettura per tale applicazione:

* _Tenant isolamento:_ Gli sviluppatori dell'applicazione devono adottare misure appropriate per garantire che nessun tenant non autorizzato o indesiderato accesso ai dati di altro tenant. Oltre la prospettiva di privacy dei dati, strategie di isolamento tenant richiedono una gestione efficace di risorse condivise e protezione da altre istanze disturbate.
* _Cloud delle risorse costo:_ Come per qualsiasi altra applicazione, le soluzioni software devono rimanere costo competitivo come componente di un'applicazione multi-tenant.
* _Facilità di operazioni:_ Quando si sviluppano un'architettura multi-tenant, l'impatto sulle operazioni dell'applicazione e complessità è importante. Ricerca Azure dispone di un [contratto di servizio 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* _Dei consumi sull'ambiente globale:_ Applicazioni multi-tenant potrebbero essere necessario fornire in modo efficace tenant distribuiti in tutto il mondo.
* _Scalabilità:_ Gli sviluppatori di applicazioni prendere in considerazione come le riconciliate tra mantenere un livello sufficientemente basso di complessità dell'applicazione e progettazione dell'applicazione in scala con numero di tenant e le dimensioni dei tenant dati e il carico di lavoro.

Ricerca Azure offre alcuni limiti che possono essere utilizzati per isolare dei tenant dati e il carico di lavoro.

## <a name="modeling-multitenancy-with-azure-search"></a>Modellazione rappresentato con ricerca Azure
In caso di uno scenario multi-tenant, lo sviluppatore dell'applicazione implica l'uso di uno o più servizi di ricerca e dividere i tenant tra servizi, indici o entrambi. Ricerca Azure è riportati alcuni motivi comuni durante la modellazione di uno scenario multi-tenant:

1. _Indice per ogni tenant:_ Ogni tenant è il proprio indice all'interno di un servizio di ricerca è condivise con gli altri tenant.
1. _Servizio al tenant:_ Ogni tenant è il proprio servizio di ricerca di Azure dedicato, che offre più alto livello di separazione dei dati e il carico di lavoro.
1. _Combinazione di entrambi:_ Tenant di dimensioni maggiori e più attivo vengono assegnate servizi dedicati mentre tenant minori assegnati singoli indici all'interno di servizi condivisi.

## <a name="1-index-per-tenant"></a>1. indicizzare per tenant
![Immagine del modello di indice per tenant](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

In un modello di indice per tenant più tenant occupano un singolo servizio di ricerca di Azure in cui ogni tenant è proprio indice.

Tenant ottenere isolamento dei dati, poiché tutte le richieste di ricerca e le operazioni di documento vengono generate livello di un indice di ricerca di Azure. Livello di applicazione, è a conoscenza necessità per indirizzare il traffico dei tenant diversi per gli indici corretta durante anche la gestione delle risorse a livello del servizio in tutti i tenant.

Un attributo chiave del modello di indice per ogni tenant è la possibilità per gli sviluppatori di applicazione oversubscription la capacità di un servizio di ricerca tra tenant dell'applicazione. Se il tenant dispone di una distribuzione uniforme del carico di lavoro, la combinazione ottimale di tenant può essere distribuita tra gli indici del servizio di ricerca a contenere un numero di tenant particolarmente attivo, risorse mentre viene servita contemporaneamente un long tail tenant meno attivi. Il compromesso è l'impossibilità del modello di gestire le situazioni in cui ogni tenant contemporaneamente è altamente attivo.

Il modello di indice per ogni tenant è base per un modello di costo variabile, in cui un intero servizio di ricerca di Azure è stato acquistato all'inizio e successivamente compilati con tenant. In questo modo capacità inutilizzata di designare per versioni di valutazione e gli account gratuiti.

Per le applicazioni con una presenza globale, il modello di indice per tenant potrebbe non essere più efficiente. Se il tenant dell'applicazione è distribuito in tutto il mondo, un servizio distinto potrebbe essere necessario per ogni area geografica di cui è possibile duplicare i costi per ognuno di essi.

Ricerca Azure consente la scala di singoli indici e il numero totale di indici aumentare le dimensioni. Se un prezzi appropriato livello scelto, partizioni e repliche possono essere aggiunti al servizio di ricerca intera quando un particolare indice all'interno del servizio cresce troppo grande in termini di spazio di archiviazione o il traffico.

Se il numero totale di indici cresce troppo grande per un singolo, un altro servizio deve eseguire il provisioning adattato nuovo tenant. Se gli indici sono necessario essere spostato tra i servizi di ricerca con l'aggiunta di nuovi servizi, deve essere copiati manualmente da un indice a altra durante la ricerca di Azure non consente di un indice spostare i dati dall'indice.


## <a name="2-service-per-tenant"></a>2. servizio per ogni tenant
![Immagine del modello di servizio per tenant](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

In un'architettura di servizio per tenant, ogni tenant è il proprio servizio di ricerca.

In questo modello, l'applicazione raggiunge il massimo livello di isolamento per il tenant. Ogni servizio ha dedicato lo spazio di archiviazione e la velocità effettiva per la gestione di richiesta di ricerca, nonché API diverso.

Per le applicazioni nel punto in cui ogni tenant ha una traccia di grandi dimensioni o il carico di lavoro se la variabilità piccola tenant per tenant, il modello di servizio per ogni tenant è una scelta efficace come risorse non sono condivisi tra carichi di lavoro di diverse tenant.

Un servizio per modello tenant offre il vantaggio di un modello di costi fissi, prevedibili. Non esiste alcun investimento iniziale di un servizio di ricerca intero fino a quando non esiste un tenant di riempimento, ma il costo per ogni tenant è superiore a un modello di indice per tenant.

Il modello di servizio per ogni tenant è una soluzione ideale per applicazioni con una presenza globale. Con limitazioni geografiche distribuito tenant, è facile disporre di servizio del tenant ogni paese appropriato.

Problemi legati alla scalabilità questo modello si verificano quando tenant singoli copriranno al servizio. Ricerca Azure attualmente non supporta l'aggiornamento a livello di prezzo di un servizio di ricerca, in modo che tutti i dati avrebbero dovuto essere copiati manualmente a un nuovo servizio.

## <a name="3-mixing-both-models"></a>3. la combinazione di entrambi i modelli
Un altro modello per la modellazione rappresentato è combinazione strategie di indice per tenant e servizio per tenant.

Combinando due schemi tenant più grande di un'applicazione può occupare servizi dedicati mentre situazioni long tail di tenant meno attivi di piccole dimensioni può occupare indici in un servizio condiviso. Questo modello consente agli tenant più grande di in modo coerente prestazioni dal servizio durante proteggere tenant più piccoli da eventuali altre istanze disturbate.

Tuttavia, l'implementazione di questa strategia si basa prospettiva prevedere quali tenant richiederà un servizio dedicato rispetto a un indice analitico in un servizio condiviso. Maggiore complessità dell'applicazione con la necessità di gestire entrambi gli ambienti di questi modelli "multitenancy".

## <a name="achieving-even-finer-granularity"></a>Per ottenere anche maggiore granularità
I modelli di progettazione sopra per creare gli scenari multi-tenant nella ricerca Azure presuppongono un ambito uniforme nel punto in cui ogni tenant è un'intera istanza di un'applicazione. Tuttavia, applicazioni in alcuni casi possono gestire numerosi ambiti più piccoli.

Se i modelli di servizio per tenant e indice per tenant non sono sufficientemente piccoli ambiti, è possibile modellare un indice per ottenere un grado ancora più preciso di dettaglio.

Per un singolo indice hanno un comportamento diverso per gli endpoint client diverso, un campo può essere aggiunti a un indice che definisce un determinato valore per ogni cliente possibili. Ogni volta che un client chiama Azure Cerca per eseguire una query o modificare un indice, il codice dall'applicazione client specifica il valore appropriato per il campo utilizzando la funzionalità di [filtro](https://msdn.microsoft.com/library/azure/dn798921.aspx) della ricerca di Azure in fase di query.

Questo metodo può essere utilizzato per ottenere una funzionalità di account utente separati, livelli di autorizzazione separati e separare completamente applicazioni.

## <a name="next-steps"></a>Passaggi successivi
Ricerca Azure è una soluzione interessante per diverse applicazioni, [altre informazioni sui potenti funzionalità del servizio](http://aka.ms/whatisazsearch). Quando si valuta i vari modelli di progettazione per le applicazioni multi-tenant, prendere in considerazione i [vari livelli dei prezzi](https://azure.microsoft.com/pricing/details/search/) e i rispettivi [limiti di servizio](search-limits-quotas-capacity.md) per personalizzare migliore Azure ricerca in modo da adattarlo carichi di lavoro dell'applicazione e architetture di tutte le dimensioni.

Domande sulla ricerca di Azure e scenari multi-tenant possono essere indirizzate al azuresearch_contact@microsoft.com.
