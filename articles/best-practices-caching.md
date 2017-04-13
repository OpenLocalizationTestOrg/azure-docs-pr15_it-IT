<properties
   pageTitle="La memorizzazione nella cache indicazioni | Microsoft Azure"
   description="Indicazioni sulla memorizzazione nella cache per migliorare le prestazioni e scalabilità."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2016"
   ms.author="masashin"/>


# <a name="caching-guidance"></a>Indicazioni per la memorizzazione nella cache

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

La memorizzazione nella cache è una tecnica comune ha lo scopo di migliorare le prestazioni e scalabilità del sistema. A tale scopo, temporaneamente copia di dati di frequente allo spazio di archiviazione rapida che si trova Chiudi per l'applicazione. Se non è più vicino alla posizione dell'applicazione rispetto all'origine della memorizzazione dei dati rapido, quindi la memorizzazione nella cache può migliorare significativamente i tempi di risposta per le applicazioni client, è necessario gestire più rapidamente i dati.

La memorizzazione nella cache è più efficace quando un'istanza di client legge ripetutamente gli stessi dati, in particolare se tutte le condizioni seguenti si applicano all'archivio dati originale:
- Rimane relativamente statico.
- È lenta rispetto alla velocità della cache.
- È soggetto a un livello elevato di conflitto.
- Questa è lontano latenza di rete può causare l'accesso a essere lenta.

## <a name="caching-in-distributed-applications"></a>La memorizzazione nella cache nelle applicazioni distribuite

Applicazioni distribuite in genere implementano una o entrambe le seguenti strategie quando la memorizzazione nella cache dei dati:

- Utilizzo di una cache privata, in cui i dati sono contenuti in locale nel computer in cui è in esecuzione un'istanza di un'applicazione o servizio.
- Utilizzo di una cache condivisa che funge da un'origine comune che può accedere più processi e/o computer.

In entrambi i casi, la memorizzazione nella cache può essere eseguito sul lato client e/o sul lato server. La cache lato client viene eseguita dal processo che fornisce l'interfaccia utente per un sistema, ad esempio un web browser o applicazione desktop.
Memorizzazione nella cache sul lato server viene eseguita dal processo che offre servizi di integrazione in esecuzione in modalità remota.

### <a name="private-caching"></a>La memorizzazione nella cache privata

Il tipo di base della cache è un archivio in memoria. Ha contenuti nello spazio di indirizzi di un singolo processo e accedervi direttamente il codice viene eseguito nel processo. Questo tipo di cache è molto rapido accedere. Offre un modo estremamente efficace per l'archiviazione piccole quantità di dati statici, dal momento che le dimensioni di una cache sono generalmente vincolata il volume di memoria disponibile nel computer in cui risiede il processo.

Se è necessario per ulteriori informazioni rispetto a quanto fisica possibile memorizzate nella cache, è possibile scrivere dati memorizzati nella cache per il file system locale. Sarà più lenta accedere a dati contenuti in memoria, ma dovrebbero essere ancora più veloce e più affidabile il recupero dei dati in una rete.

Se si dispone di più istanze di un'applicazione che utilizza questo modello contemporaneamente in esecuzione, ciascuna istanza dell'applicazione ha la propria cache indipendente che contiene la propria copia dei dati.

Considerare una cache come uno snapshot dei dati originali in un determinato momento in passato. Se non si tratta statica, è probabile che le istanze di applicazione diverso contenere diverse versioni dei dati nelle cache. Di conseguenza, la stessa query eseguita da queste istanze può restituire risultati diversi, come illustrato nella figura 1.

![Utilizzo di una cache in memoria in diverse istanze di un'applicazione](media/best-practices-caching/Figure1.png)

_Figura 1: Utilizzo di una cache in memoria in diverse istanze di un'applicazione_

### <a name="shared-caching"></a>Condiviso la memorizzazione nella cache

Utilizzo di una cache condivisa consente di risolvere problemi che potrebbero essere diversi dati in ogni cache, che può verificarsi con la memorizzazione nella cache in memoria. La memorizzazione nella cache condivisa garantisce che istanze diverse dell'applicazione stessa visualizzazione dei dati memorizzati nella cache. A tale scopo, individuare la cache in un percorso diverso, in genere ospitato come parte di un servizio separato, come illustrato nella figura 2.

![Utilizzo di una cache condivisa](media/best-practices-caching/Figure2.png)

_Figura 2: Utilizzo di una cache condivisa_

Particolarmente importante dell'approccio memorizzazione nella cache condiviso è scalabilità che fornisce. Molti servizi cache condivisa implementati utilizzando un cluster di server e utilizzano software che distribuisce i dati all'interno del cluster in modo trasparente. Un'istanza di applicazione invia semplicemente una richiesta per il servizio cache.
L'infrastruttura sottostante è responsabile di determinare la posizione dei dati memorizzati nella cache del cluster. È possibile adattare la cache aggiungendo più server.

Esistono due svantaggi principali dell'approccio memorizzazione nella cache condiviso:
- La cache è minore di accedere perché non è più conservato localmente per ogni istanza dell'applicazione.
- La necessità di implementare un servizio separato della cache può aggiungere complessità alla soluzione.

## <a name="considerations-for-using-caching"></a>Considerazioni per l'uso di memorizzazione nella cache

Le sezioni seguenti descrivono in modo più dettagliato le considerazioni per la progettazione e utilizzo di una cache.

### <a name="decide-when-to-cache-data"></a>Decidere quando i dati nella cache

La memorizzazione nella cache notevolmente migliorare le prestazioni e scalabilità disponibilità. Altri dati che si sono e maggiore del numero di utenti che devono accedere a questi dati diventano i vantaggi della memorizzazione nella cache maggiore. Ciò avviene perché la memorizzazione nella cache riduce la latenza e conflitto associato alla gestione di volumi elevati di richieste simultanee nell'archivio dati originale.

Ad esempio un database potrebbe supportare un numero limitato di connessioni simultanee. Recuperare dati da una cache condivisa, tuttavia, invece di database sottostante consente per un'applicazione client accedere ai dati anche se il numero delle connessioni disponibili è esaurito attualmente. Inoltre, se il database è disponibile, applicazioni client potrebbero essere possibile continuare con i dati contenuti nella cache.

Valutare la possibilità di memorizzazione nella cache dei dati che leggere frequentemente ma modificati raramente (ad esempio, i dati con una percentuale maggiore di operazioni di lettura quelle di scrittura). Tuttavia, non è consigliabile utilizzare la cache come archivio autorevole di informazioni riservate. Se, tuttavia, assicurarsi che tutte le modifiche che l'applicazione non è possibile rimanere modificano vengono sempre salvate in un archivio di dati permanenti. Ciò significa che se non è disponibile la cache, l'applicazione è possibile continuare a funzionare con l'archivio di dati e da non perdere informazioni importanti.

### <a name="determine-how-to-cache-data-effectively"></a>Determinare la modalità di memorizzazione nella cache dei dati in modo efficace

Il tasto all'uso di una cache in modo efficace per determinare i dati più adatte alla cache e la memorizzazione nella cache al momento appropriato. I dati possono essere aggiunti alla cache su richiesta per la prima volta che recuperarla da un'applicazione. Ciò significa che l'applicazione deve recuperare i dati di una sola volta dall'archivio dati e che l'accesso successive può essere soddisfatte utilizzando la cache.

In alternativa, una cache può essere parzialmente o completamente popolata con dati in anticipo, in genere all'avvio dell'applicazione (un approccio noto come seeding). Tuttavia, potrebbe non essere opportuno implementare seeding per una cache di grandi dimensioni perché questo approccio è possibile imporre un carico inaspettato ad alto l'archivio dati originale all'avvio dell'applicazione in esecuzione.

Spesso un'analisi delle tendenze consente di decidere se per parzialmente o completamente prepopolata una cache e per scegliere i dati nella cache. Ad esempio, può essere utile avviare la cache con i dati del profilo statiche degli utenti per i clienti che utilizzano regolarmente l'applicazione (ad esempio ogni giorno), ma non per i clienti che utilizzano l'applicazione solo una volta alla settimana.

Memorizzazione nella cache in genere funziona bene con dati che non è modificabile o che vengono modificati raramente. Esempi di informazioni di riferimento, ad esempio prodotto e le informazioni sui prezzi in un'applicazione di e-commerce o risorse statiche condivise costose costruire. Alcuni o tutti i dati possono essere caricati nella cache all'avvio dell'applicazione per ridurre al minimo richiesta di risorse e per migliorare le prestazioni. Può inoltre essere opportuno disporre di un processo in background che consente di aggiornare periodicamente riferimento dati memorizzati nella cache per assicurarsi siano sempre aggiornati o che si aggiorna la cache quando fanno riferimento a dati le modifiche.

La memorizzazione nella cache è meno utile per i dati dinamici, anche se esistono alcune eccezioni a questa considerazione (vedere la sezione della cache dei dati estremamente dinamici più avanti in questo articolo per ulteriori informazioni). Modifica dei dati originali regolarmente, le informazioni memorizzate nella cache più aggiornate molto rapidamente o il sovraccarico di sincronizzazione della cache con l'archivio dati originale riduce l'efficacia di memorizzazione nella cache.

Si noti che una cache non è necessario includere i dati completi per un'entità. Ad esempio, se un elemento di dati rappresenta un oggetto multivalore, ad esempio un cliente bancario con un nome, indirizzo e saldo, alcuni di questi elementi potrebbero rimanere statico (ad esempio nome e indirizzo), mentre gli altri utenti (ad esempio saldo) potrebbe essere più dinamici. In questi casi può essere utile memorizzare nella cache le parti statiche dei dati e recuperare (o calcolare) le restanti informazioni quando è necessaria.

È consigliabile eseguire analisi test e l'uso delle prestazioni per determinare se il caricamento di pre- popolazione o su richiesta di cache o una combinazione di entrambi, è appropriato. La decisione basare la volatilità e il modello di utilizzo dei dati. Analisi di utilizzo e prestazioni della cache è particolarmente importante nelle applicazioni che riscontrare sovraccarico e devono essere scalabilità. In scenari scalabili, ad esempio, può essere utile per avviare la cache per ridurre il carico l'archivio dati picco.

Memorizzazione nella cache può anche essere utilizzato per evitare la ripetizione calcoli durante l'esecuzione dell'applicazione. Se un'operazione di trasformazione dei dati o esegue un calcolo complesso, è possibile salvare i risultati dell'operazione nella cache. Se lo stesso calcolo è necessario in seguito, l'applicazione è sufficiente recuperare i risultati dalla cache.

Un'applicazione può modificare dati contenuti in una cache. Tuttavia, è consigliabile ritenere della cache come archivio dati temporaneo che potrebbe non vengono visualizzati in qualsiasi momento. Non memorizzare dati importanti nella cache di sola lettura. Assicurarsi di mantenere le informazioni nell'archivio dati originale anche. Questo errore indica se la cache non è disponibile, ridurre il rischio di perdere dati.

### <a name="cache-highly-dynamic-data"></a>Cache dei dati estremamente dinamici

Quando si archiviano modificando rapidamente informazioni in un archivio di dati permanenti, è possibile imporre un sovraccarico del sistema. Si consideri ad esempio un dispositivo che continuamente i report di stato o alcuni altri misura. Se un'applicazione decide di non memorizzare nella cache questi dati sulla base informazioni memorizzate nella cache saranno quasi sempre aggiornate, la stessa considerazione potrebbe essere vera quando l'archiviazione e recupero di queste informazioni dall'archivio dati. Il tempo che necessario per salvare e recuperare i dati, potrebbero essere cambiate.

In caso di questo, prendere in considerazione i vantaggi dell'archiviazione informazioni dinamiche direttamente nella cache anziché nell'archivio di dati permanenti. Se i dati sono non critici e non richiedono il controllo, quindi non è importante se la modifica occasionale venga persa.

### <a name="manage-data-expiration-in-a-cache"></a>Gestire scadenza dati nella cache

Nella maggior parte dei casi, dati contenuti in una cache sono una copia dei dati contenuti nell'archivio dati originale. I dati nell'archivio dati originali potrebbero essere modificati dopo che è stata memorizzata nella cache, causando diventano obsoleti i dati memorizzati nella cache. Molti sistemi di memorizzazione nella cache consentono di configurare la cache per scadere dati e ridurre il periodo per cui i dati potrebbero essere aggiornati.

Quando scadono dati memorizzati nella cache, che è stata rimossa dalla cache e l'applicazione deve recuperare i dati dall'archivio dati originale (è possibile inserire le informazioni appena recuperati indietro nella cache). È possibile impostare un criterio di scadenza predefinito quando si configura la cache. Molti servizi cache, è possibile stabilire anche la scadenza per i singoli oggetti quando si archiviano a livello di programmazione nella cache.
Alcune cache consentono di specificare il periodo di validità come valore assoluto o un valore variabile che causa l'elemento da rimuovere dalla cache se non è disponibile nell'intervallo di tempo specificato. Questa impostazione sostituisce qualsiasi criterio di scadenza a livello di cache, ma solo per gli oggetti specificati.

> [AZURE.NOTE] Valutare la possibilità di scadenza per la cache e gli oggetti in essa contenuti con attenzione. Se si apportano troppo breve, consente di ridurre i vantaggi dell'utilizzo della cache oggetti scadrà troppo rapidamente. Se si apportano periodo troppo lungo, si rischia di dati diventare non aggiornati.

È inoltre possibile che la cache potrebbe riempire se sono consentito che possono essere residenti entro una lunga esperienza i dati. In questo caso, tutte le richieste per aggiungere nuovi elementi alla cache potrebbero causare alcuni elementi forzare la rimozione di un processo noto come eliminazione. Servizi di cache in genere rimuovere dati in base alla (LRU) utilizzato meno di recente, ma in genere è possibile ignorare questo criterio ed evitare che gli elementi da rimuovere. Tuttavia, si adotta questo approccio, potrebbe causare il superamento della memoria è disponibile nella cache. Un'applicazione che tenta di aggiungere un elemento alla cache avrà esito negativo con un'eccezione.

Alcune implementazioni memorizzazione nella cache possono fornire criteri di eliminazione aggiuntive. Esistono diversi tipi di criteri di eliminazione. Sono inclusi:
- Criteri usati di recente (in si prevede che i dati non saranno necessari nuovo).
- Criteri first in first out (dati meno recenti viene rimosso prima di tutto).
- Un criterio di rimozione espliciti in base a un evento con trigger (ad esempio i dati modificati).

### <a name="invalidate-data-in-a-client-side-cache"></a>Invalidate dati in una cache lato client

Dati contenuti in una cache lato client sono in genere considerati all'esterno presso il servizio che fornisce i dati al client. Un servizio non è possibile forzare direttamente un client per aggiungere o rimuovere le informazioni da una cache lato client.

Ciò significa che è possibile che un client che utilizza una cache configurata in modo non corretto per continuare a usare le informazioni non aggiornate. Ad esempio, se i criteri di scadenza della cache non sono implementati correttamente, un client potrebbe utilizzare informazioni non aggiornate memorizzati nella cache locale quando le informazioni dell'origine dati originale è sono modificato.

Se si sta creando un'applicazione web che fornisce dati tramite una connessione HTTP, è possibile forzare un client web (ad esempio un browser o proxy web) in modo implicito per recuperare le informazioni più recenti. È possibile farlo se una risorsa viene aggiornata da una modifica in URI di quella risorsa. URI di una risorsa client Web in genere utilizzano come chiave nella cache lato client, pertanto se URI assume l'aspetto, il client web ignora qualsiasi precedentemente memorizzati nella cache versioni di una risorsa e recupera invece la nuova versione.

## <a name="managing-concurrency-in-a-cache"></a>Gestione della concorrenza nella cache

Cache spesso sono progettati per essere condivisi da più istanze di un'applicazione. Ogni istanza dell'applicazione può leggere e modificare dati memorizzati nella cache. Di conseguenza, i codice a barre contemporanea che si verificano con qualsiasi archivio di dati condivisi applicate anche a una cache. In situazioni in cui un'applicazione deve modificare i dati che viene mantenuti nella cache, potrebbe essere necessario accertarsi che gli aggiornamenti effettuati da una sola istanza dell'applicazione non sovrascrivano le modifiche apportate da un'altra istanza.

A seconda della natura dei dati e la probabilità di conflitti, è possibile adottare uno dei due metodi per concorrenza:

- __Durate ottimistiche.__ Immediatamente prima dell'aggiornamento dei dati, l'applicazione consente di verificare se i dati nella cache è sono modificato dopo che è stata recuperata. Se i dati sono ancora lo stesso, è possibile effettuare la modifica. In caso contrario, è necessario decidere se aggiornare l'applicazione. (La logica business che questa decisione sarà specifici dell'applicazione.) Questo approccio è adatto per situazioni in cui siano poco frequenti aggiornamenti o dove si trovano raramente conflitti.
- __Pessimistica.__ Quando il recupero dei dati, l'applicazione lo blocca nella cache per impedirne la modifica di un'altra istanza. Questo processo assicura che non si verificano conflitti, ma anche in grado di bloccare altre istanze necessari per elaborare gli stessi dati. Concorrenza pessimistica può influire sulla scalabilità di una soluzione ed è consigliata solo per le operazioni breve. Questo approccio potrebbe essere appropriato per situazioni in cui conflitti maggiormente, soprattutto se un'applicazione aggiorna più elementi nella cache e necessario verificare che queste modifiche vengono applicate in modo coerente.

### <a name="implement-high-availability-and-scalability-and-improve-performance"></a>Disponibilità e scalabilità elevate per implementare e migliorare le prestazioni

Evitare l'utilizzo di una cache come archivio principale dei dati. Questo è il ruolo dell'archivio dati originale da cui viene popolata la cache. Archivio dati originale è responsabile per garantire la persistenza dei dati.

Prestare attenzione a non introdurre dipendenze critiche sulla disponibilità di un servizio di cache condivisa nelle soluzioni. Un'applicazione dovrebbe essere possibile continuare a funzionare se il servizio che fornisce cache condivisa non è disponibile. L'applicazione non si blocca o esito negativo durante l'attesa riprendere il servizio cache.

Di conseguenza, è necessario preparare l'applicazione per verificare la disponibilità del servizio cache e passare all'archivio dati originale, se la cache non è accessibile. [Motivo interruttore](http://msdn.microsoft.com/library/dn589784.aspx) è utile per la gestione di questo scenario. È possibile ripristinare il servizio che fornisce la cache e dopo diventa disponibile, è possibile ricompilata la cache durante la lettura dei dati modulo archivio dati originale, seguendo una strategia, ad esempio il [motivo riposo Cache](http://msdn.microsoft.com/library/dn589799.aspx).

Tuttavia, è possibile che un impatto scalabilità del sistema se l'applicazione ricorre all'archivio dati originale quando la cache è temporaneamente non disponibile.
Quando viene recuperata l'archivio di dati, archivio dati originale Impossibile implementano con le richieste di dati, generando timeout e connessioni non riuscite.

È possibile implementare una cache locale e privata in ogni istanza di un'applicazione, con la cache condivisa accederanno a tutte le istanze dell'applicazione. Quando l'applicazione recupera un elemento, è possibile verificare prima di tutto nella cache locale, quindi in condiviso nella cache e infine nei dati di archiviare. La cache locale può essere compilata utilizzando i dati nella cache condivisa o del database, se la cache condivisa non è disponibile.

Questa opzione richiede la configurazione per impedire che la cache locale diventano obsoleti rispetto alla cache condivisa. Tuttavia, la cache locale si comporta come buffer se cache condivisa non è raggiungibile. Figura 3 mostra la struttura.

![Utilizzo di una cache locale, privata con una cache condivisa](media/best-practices-caching/Caching3.png)
_Figura 3: utilizzo di una cache locale, privata con una cache condivisa_

Per supportare cache di grandi dimensioni che contengono dati relativamente lunga durati, alcuni servizi cache offrono un'opzione di disponibilità che implementa failover automatico se la cache non è più disponibile. Questo approccio implica in genere la replica i dati memorizzati nella cache memorizzato su un server di cache primaria su un server cache secondaria e passaggio al server secondario in caso di errore del server primario o la connettività viene persa.

Per ridurre la latenza associato a scrivere a più destinazioni, la replica sul server secondario potrebbe verificarsi in modo asincrono scritti dati la cache sul server primario. Questo approccio comporta la possibilità che alcune informazioni memorizzate nella cache potrebbero andare perse in caso di errore, ma la percentuale di questi dati deve essere piccola rispetto alle dimensioni complessiva della cache.

Se una cache condivisa è grande, potrebbe essere utile dividere i dati memorizzati nella cache per i nodi per ridurre le possibilità di conflitto e migliorare la scalabilità. Molti cache condivise supportano la possibilità di aggiungere in modo dinamico (e rimuovere) nodi e ribilanciare i dati in partizioni. Questo approccio può implicare cluster, in cui l'insieme dei nodi viene presentato alle applicazioni client come cache continua, singola. Internamente, tuttavia, i dati si discostino tra i nodi seguendo una strategia di distribuzione predefiniti che distribuisce uniformemente il carico. Il [documento indicazioni partizione dati](http://msdn.microsoft.com/library/dn589795.aspx) del sito Web Microsoft sono disponibili ulteriori informazioni sulle possibili strategie partizioni.

Cluster, è anche possibile aumentare la disponibilità della cache. Se un nodo non riesce, il resto della cache è ancora possibile accedere.
Funzionalità viene spesso utilizzata in combinazione con replica e failover. Ogni nodo può essere replicata e la replica può essere rapidamente in linea se il nodo ha esito negativo.

Molti leggere e le operazioni di scrittura saranno probabilmente implicano l'invio di singoli valori di dati o oggetti. Tuttavia, in alcuni casi potrebbe essere necessario memorizzare o recuperare rapidamente volumi elevati di dati.
Ad esempio seeding una cache può comportare la scrittura di centinaia o migliaia di elementi nella cache. Un'applicazione potrebbe essere anche recuperare un numero elevato di elementi correlati dalla cache come parte della richiesta della stessa.

Molti cache su larga scala forniscono operazioni batch a tale scopo. Questo consente a un'applicazione client creare un pacchetto di un volume elevato di elementi in una singola richiesta e consente di ridurre il sovraccarico associato all'esecuzione di un numero elevato di richieste di piccole dimensioni.

## <a name="caching-and-eventual-consistency"></a>Coerenza eventuale e memorizzazione nella cache

Per il modello di riposo cache per l'uso, l'istanza dell'applicazione che consente di compilare la cache deve avere accesso alla versione più recente e coerenza dei dati. In un sistema che implementa eventuale coerenza (ad esempio un archivio di dati replicato) potrebbe non essere le maiuscole/minuscole.

Una sola istanza di un'applicazione potrebbe modificare un elemento di dati e invalidate versione memorizzati nella cache di tale elemento. Un'altra istanza dell'applicazione potrebbe tenta di leggere questo elemento da una cache, causando accesso alla cache, in modo che legge i dati dall'archivio dati e lo aggiunge alla cache. Tuttavia, se l'archivio di dati non è stato completamente sincronizzato con sé, istanza dell'applicazione Impossibile leggere e popolare la cache con il valore precedente.

Per ulteriori informazioni sulla gestione la coerenza dei dati, vedere la pagina di [Introduzione la coerenza dei dati](http://msdn.microsoft.com/library/dn589800.aspx) del sito Web Microsoft.

### <a name="protect-cached-data"></a>Proteggere i dati memorizzati nella cache

Indipendentemente dal servizio cache usare, è necessario proteggere i dati contenuti nella cache dall'accesso non autorizzato. Ci sono due motivi principali:

- Informativa sulla privacy dei dati nella cache
- Informativa sulla privacy di dati man mano che scorra tra la cache e l'applicazione che utilizza la cache

Per proteggere i dati nella cache, il servizio cache possibile implementare un meccanismo di autenticazione che è necessario che le applicazioni di specificare le informazioni seguenti:
- Quali identità possono accedere ai dati nella cache.
- Quali operazioni (lettura e scrittura) che possono essere eseguite tali identità.

Per ridurre il sovraccarico che sono associate alla lettura e la scrittura dei dati, dopo un'identità è stata concessa scrittura e/o accesso in lettura alla cache, che identità è possibile utilizzare qualsiasi tipo di dati nella cache.

Se è necessario limitare l'accesso ai sottoinsiemi di dati memorizzati nella cache, è possibile eseguire una delle operazioni seguenti:

- Dividere la cache partizioni (mediante server diversa per la cache) e solo concedere l'accesso alle identità per le partizioni che devono essere consentiti da utilizzare.
- Crittografare i dati in ogni sottoinsieme con chiavi diverse e fornire le chiavi di crittografia solo alle identità che devono avere accesso a ogni sottoinsieme. Un'applicazione client potrebbe comunque in grado di recuperare tutti i dati nella cache, ma solo sarà possibile decrittografare i dati per il quale dispone i tasti.

È anche necessario proteggere i dati come scorra da e verso la cache. A questo scopo dipendono la funzionalità di protezione di infrastruttura di rete che utilizzano le applicazioni client per connettersi alla cache. Se la cache viene implementata mediante un server sul posto nella stessa organizzazione che ospita le applicazioni client, quindi isolamento della rete stessa potrebbe non è necessario eseguire alcune operazioni aggiuntive. Se la cache si trova in modalità remota e richiede una connessione TCP o HTTP in una rete pubblica (ad esempio Internet), è possibile implementare SSL.

## <a name="considerations-for-implementing-caching-with-microsoft-azure"></a>Considerazioni per l'implementazione di memorizzazione nella cache con Microsoft Azure

Azure offre la Cache di Redis Azure. Si tratta di un'implementazione dell'origine aperta cache Redis che viene eseguita come servizio in un Data Center di Azure. Fornisce un servizio di memorizzazione nella cache che è possibile accedervi da qualsiasi applicazione di Azure, se l'applicazione è implementata come un servizio cloud, un sito Web o all'interno di una macchina virtuale Azure. Cache possono essere condivisi dalle applicazioni client con la chiave di accesso appropriato.

Azure Cache Redis è una soluzione di memorizzazione nella cache le massime prestazioni che fornisce disponibilità, scalabilità e sicurezza. In genere viene eseguito come servizio distribuiti in uno o più computer dedicato. Il tentativo di memorizzare le informazioni presenti possibile in memoria per garantire un accesso rapido. Questa architettura deve fornire latenza bassa e alta velocità riducendo la necessità di eseguire operazioni dei / o lente.

 Azure Cache Redis è compatibile con molte delle varie API utilizzati dalle applicazioni client. Se si dispone di applicazioni che utilizzano già Azure Redis Cache sistema locale, la Cache di Redis Azure fornisce un percorso di migrazione rapida per la memorizzazione nella cache nel cloud.

> [AZURE.NOTE] Azure offre il servizio Cache gestito. Questo servizio è basato sul modulo di Azure servizio tessuti Cache. Consente di creare una cache distribuita che può essere condivisi dalle applicazioni strettamente collegati. La cache è ospitata nel server le massime prestazioni in esecuzione in un Data Center di Azure.
Tuttavia, questa opzione non è più consigliata ed è disponibile solo per supportare le applicazioni esistenti che sono stata realizzate per usarlo. Per lo sviluppo di nuovo, usare Azure Redis Cache.
>
> Inoltre, Azure supporta la memorizzazione nella cache in ruolo. Questa caratteristica consente di creare una cache specifico in un servizio cloud.
La cache è ospitata da istanze di un ruolo web o lavoro e accessibile solo in base ai ruoli che funzionano come parte della stessa unità di distribuzione del servizio cloud. (Un'unità di distribuzione è l'insieme di istanze del ruolo che vengono distribuite come un servizio cloud su un'area specifica). La cache è raggruppata e tutte le istanze del ruolo all'interno della stessa unità di distribuzione che ospita la cache diventano parte dello stesso cluster di cache. Tuttavia, questa opzione non è più consigliata ed è disponibile solo per supportare le applicazioni esistenti che sono stata realizzate per usarlo. Per lo sviluppo di nuovo, usare Azure Redis Cache.
>
> Servizio Cache gestite Azure e Azure In ruolo Cache attualmente sono previste per il fondo pensione su 16 novembre 2016.
Si consiglia di migrare Azure Redis Cache in preparazione per questo pensionistico. Per ulteriori informazioni, visitare la pagina   [che cos'è l'offerta Azure Redis Cache e quali dimensioni utilizzare?](redis-cache/cache-faq.md#what-redis-cache-offering-and-size-should-i-use) del sito Web Microsoft.


### <a name="features-of-redis"></a>Caratteristiche di Redis

 Redis è più di un server di cache semplice. Fornisce un database in memoria distribuito con un insieme di comandi completo che supporta molti scenari comuni. Descritti più avanti in questo documento, la sezione utilizzando Redis memorizzazione nella cache. In questa sezione sono riepilogate alcune delle caratteristiche chiave che fornisce Redis.

### <a name="redis-as-an-in-memory-database"></a>Redis come un database in memoria

Redis supporta sia operazioni lettura e scrittura. In Redis, è possano protetto scrive un errore di sistema tramite viene memorizzata periodicamente un file snapshot locale o in un file di log solo accodamento. Non si tratta maiuscole/minuscole in molti cache (che deve essere considerato archivi transitori dati).

 Tutte le operazioni scrittura sono asincrone e non vengono bloccano client da leggere e scrivere dati. Quando Redis esecuzione, legge i dati da file snapshot o log e viene utilizzato per creare la cache in memoria. Per ulteriori informazioni, vedere [Redis persistenza](http://redis.io/topics/persistence) nel sito Web Redis.

> [AZURE.NOTE] Redis non garantisce che tutte le operazioni scrittura verranno salvate in caso di errore, ma nella peggiore potrebbero perdere solo alcuni secondi patrimonio dei dati. Tenere presente che una cache non è destinata a fungere da un'origine dati rilevanti e viene eseguita delle applicazioni di utilizzo della cache per assicurarsi che i dati critici vengono salvati correttamente a un archivio di dati appropriato. Per ulteriori informazioni, vedere [una serie di riposo cache](http://msdn.microsoft.com/library/dn589799.aspx).

#### <a name="redis-data-types"></a>Redis tipi di dati

Redis è un valore di chiave, in cui i valori possono contenere tipi semplici o strutture di dati complessi, ad esempio hash, elenchi e imposta. Supporta un insieme di operazioni atomiche su questi tipi di dati. Chiavi possono essere permanenti o contrassegnate con un limitato a durata, quindi il tasto e il valore corrispondente vengono automaticamente rimossi dalla cache. Per ulteriori informazioni sulle chiavi Redis e i valori, visitare la pagina [Introduzione a Redis astrazioni e tipi di dati](http://redis.io/topics/data-types-intro) nel sito Web Redis.

#### <a name="redis-replication-and-clustering"></a>Redis replica e cluster

Redis supporta la replica principale/secondario per assicurare la disponibilità e gestire la velocità di trasmissione. Scrivere operazioni a un nodo principale Redis vengono replicate a uno o più nodi subordinati. Le operazioni di lettura possono essere rappresentate nello schema o una qualsiasi delle forme subordinate.

In caso di una partizione di rete, le forme subordinate possono continuare a fornire i dati e quindi in modo trasparente sincronizzare nuovamente con lo schema quando viene ripristinata la connessione. Per ulteriori informazioni, visitare la pagina di [replica](http://redis.io/topics/replication) nel sito Web Redis.

Redis vengono forniti anche cluster, che consente di suddividere i dati in shards su server e distribuire il carico in modo trasparente. Questa caratteristica migliora la scalabilità, perché è possibile aggiungere nuovi server Redis e aumentano i dati suddiviso come le dimensioni della cache.

Inoltre, ogni server del cluster possono essere replicate tramite replica principale/secondario. In questo modo la disponibilità in ogni nodo del cluster. Per ulteriori informazioni sul servizio cluster e sharding, visitare la [pagina dell'esercitazione cluster Redis](http://redis.io/topics/cluster-tutorial) sul sito Web Redis.

### <a name="redis-memory-use"></a>Redis utilizzo della memoria

Una cache Redis ha una dimensione limitata dipende dalle risorse disponibili nel computer host. Quando si configura un server Redis, è possibile specificare la maggior quantità di memoria che può utilizzare. È inoltre possibile configurare una chiave in una cache Redis a hanno una scadenza, trascorso il quale viene automaticamente rimosso dalla cache. Questa caratteristica consente di evitare la cache in memoria si vecchi o obsoleti nei dati.

Come si riempie memoria, Redis possibile automaticamente rimuovere chiavi e i valori seguendo un numero di criteri. Il valore predefinito è LRU (almeno usati), ma è anche possibile selezionare altri criteri, ad esempio la rimozione di tasti in modo casuale o la disattivazione di eliminazione completamente (in quali, maiuscole e minuscole tenta di aggiungere elementi a fail cache se è completo). Pagina [Con Redis come una cache LRU](http://redis.io/topics/lru-cache) fornisce altre informazioni.

### <a name="redis-transactions-and-batches"></a>Redis le transazioni e batch

Redis consente a un'applicazione client inviare una serie di operazioni di leggere e scrivere dati nella cache come una transazione atomica. Tutti i comandi nella transazione garantire che vengano eseguite in sequenza e i comandi eseguiti da altri client simultanee non verranno interwoven tra di esse.

Tuttavia, non sono transazioni true come un database relazionale in cui eseguire tali operazioni. Elaborazione delle transazioni è costituito da due fasi, ossia il primo è quando i comandi vengono accodati e il secondo è quando vengono eseguiti i comandi. Durante la fase di accodamento comando, i comandi che compongono la transazione vengono inviati dai client. Se qualche tipo di errore si verifica a questo punto (ad esempio un errore di sintassi o il numero di parametri errato) Redis quindi Rifiuta per l'elaborazione dell'intera transazione e lo elimina.

Durante la fase di esecuzione, Redis esegue ogni comando in coda in sequenza. Se un comando non riesce in questa fase, Redis continua con il comando successivo in coda e non ripristina gli effetti di tutti i comandi che sono già stati eseguiti. Questo tipo di transazione semplificato consente di mantenere le prestazioni ed evitare problemi di prestazioni causati da un conflitto.

Redis implementare un modulo di blocco ottimistica per aiuto per mantenere la coerenza. Per informazioni dettagliate sulle transazioni e blocchi con Redis, visitare la [pagina transazioni](http://redis.io/topics/transactions) nel sito Web Redis.

Redis supporta anche il batch meno di richieste di tipo. Il protocollo Redis utilizzate dai client per inviare comandi a un server Redis consente ai client inviare una serie di operazioni come parte della richiesta della stessa. Questo consente di ridurre la frammentazione del pacchetto sulla rete. Durante l'elaborazione batch, viene eseguita ogni comando. Se uno qualsiasi di questi comandi non valido, vengono respinti (che non accade con una transazione), ma verranno eseguiti gli altri comandi. Non è inoltre disponibile nessuna garanzia sull'ordine di elaborazione delle comandi nel batch.

### <a name="redis-security"></a>Redis sicurezza

Redis occupano esclusivamente della accesso rapido ai dati ed è progettato per l'esecuzione all'interno di un ambiente attendibile accessibili solo tramite client attendibili. Redis supporta un modello di sicurezza limitati in base a autenticazione tramite password. (È possibile rimuovere completamente l'autenticazione anche se non è consigliabile questo.)

Tutti i client autenticati hanno la stessa password globale e avere accesso alle stesse risorse. Se è necessaria protezione di accesso più completa, è necessario implementare il proprio livello di sicurezza che precede il server Redis e tutte le richieste client devono passare tramite questo livello aggiuntivo. Redis non deve essere esposto direttamente ai client non attendibile o non autenticati.

È possibile limitare l'accesso ai comandi disattivarli o rinominando loro (e fornendo solo i privilegi client con i nuovi nomi).

Redis non supporta direttamente qualsiasi forma crittografia dei dati, in modo che tutte le codifiche deve essere eseguita da applicazioni client. Inoltre, Redis non fornisce qualsiasi forma di protezione del trasporto. Se è necessario proteggere i dati come passa dalla rete, è consigliabile implementare un proxy SSL.

Per ulteriori informazioni, visitare la pagina [Redis protezione](http://redis.io/topics/security) nel sito Web Redis.

> [AZURE.NOTE] Azure Cache Redis fornisce un livello di sicurezza mediante il quale i client si connettono. I server Redis sottostanti non vengono esposti alla rete pubblica.

### <a name="using-the-azure-redis-cache"></a>Utilizzo della cache Redis Azure

La Cache di Redis Azure consente di accedere a Redis server in esecuzione su server ospitato presso un Data Center Azure; funge da una facciata che fornisce il controllo di accesso e la sicurezza. È possibile eseguire il provisioning di una cache tramite il portale di gestione di Azure. Il portale rappresenta un numero di configurazioni predefinite, che vanno dalla cache 53GB in esecuzione come servizio dedicato che supporta le comunicazioni SSL (per privacy) e replica principale/secondario con un contratto di servizio di disponibilità 99,9%, fino a 250 MB nella cache senza replica (senza garanzie di disponibilità) in esecuzione su hardware condiviso.

Tramite il portale di gestione di Azure, è possibile inoltre configurare i criteri di eliminazione della cache e controllare l'accesso alla cache mediante l'aggiunta di utenti ai ruoli forniti; Proprietario, collaboratore, utilità per la lettura. Questi ruoli definiscono le operazioni che i membri possono eseguire. Ad esempio, i membri del ruolo proprietario hanno poter controllare completamente la cache (inclusi sicurezza) e il relativo contenuto, i membri del ruolo di collaboratore possono leggere e scrivere informazioni nella cache e i membri del ruolo Lettore possono recuperare solo i dati dalla cache.

La maggior parte delle attività amministrative vengono eseguite tramite il portale di gestione di Azure, e per questo motivo molti dei comandi amministrativi disponibili nella versione standard di Redis non sono disponibili, compresa la possibilità di modificare la configurazione a livello di programmazione, arrestare il server Redis configurare secondari aggiuntive o forzare la Salva dati su disco.

Portale di gestione Azure include un grafico semplice che consente di eseguire il monitoraggio delle prestazioni della cache. Ad esempio, è possibile visualizzare il numero di connessioni viene effettuato, il numero di richieste eseguite, il volume di lettura e scrittura, e il numero della cache visitate confronto errori cache. Utilizzare queste informazioni, è possibile determinare l'efficacia della cache e, se necessario passa a un'altra configurazione o modifica i criteri di eliminazione. Inoltre, è possibile creare avvisi che inviano messaggi di posta elettronica a un amministratore se uno o più parametri fondamentali compresi all'esterno di un intervallo previsto. Ad esempio, se il numero di errori cache supera un valore specificato nell'ultima ora, un amministratore potrebbe ricevere un avviso durante la cache potrebbe essere troppo piccola o dati potrebbero essere da rimuovere troppo rapidamente.

È inoltre possibile monitorare CPU, memoria e l'uso di rete per la cache.

Per ulteriori informazioni ed esempi in cui viene illustrato come creare e configurare una Cache Redis Azure, visitare la pagina [Introduzione ad Azure Redis Cache](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) sul blog di Azure.

## <a name="caching-session-state-and-html-output"></a>Memorizzazione nella cache sessione e l'output HTML

Se è la creazione di ASP.NET web che esegue utilizzando ruoli web Azure, è possibile salvare le informazioni sullo stato di sessione e l'output HTML in una Cache Redis Azure le applicazioni. Il Provider di stato sessione per Azure Redis Cache consente di condividere le informazioni sulla sessione tra diverse istanze di un'applicazione web ASP.NET ed è molto utile nelle web farm situazioni in cui affinità client / server non è disponibile e la memorizzazione nella cache dati sessione in memoria non appropriato.

Mediante il Provider di stato sessione con Azure Redis Cache offre vari vantaggi, tra cui:

- È possibile condividere lo stato di sessione tra un numero elevato di istanze di un'applicazione web ASP.NET, fornendo la scalabilità
- Supporta accesso controllato e simultanea per gli stessi dati di stato sessione per più lettori e writer singolo, e
- È possibile utilizzare la compressione per risparmiare memoria e migliorare le prestazioni di rete.

Per ulteriori informazioni visitare la pagina [ASP.NET Session State Provider per Azure Redis Cache](redis-cache/cache-aspnet-session-state-provider.md) del sito Web Microsoft.

> [AZURE.NOTE] Non usare il Provider di stato della sessione per Azure Redis Cache per le applicazioni ASP.NET eseguite all'esterno dell'ambiente Azure. La latenza di accesso alla cache all'esterno di Azure consente di eliminare le prestazioni di memorizzazione nella cache dei dati.

Analogamente, il Provider di Cache di Output per Azure Redis Cache consente di salvare le risposte HTTP generate da un'applicazione web ASP.NET. Mediante il Provider di Cache di Output con Azure Redis Cache può migliorare i tempi di risposta di applicazioni che il rendering di output HTML complessi. istanze dell'applicazione generazione risposte simili inserirvi utilizzano i frammenti di output condiviso nella cache, invece di generare il codice HTML output afresh.  Per ulteriori informazioni visitare la pagina [Provider della Cache di Output ASP.NET per Azure Redis Cache](redis-cache/cache-aspnet-output-cache-provider.md) del sito Web Microsoft.

### <a name="azure-redis-cache"></a>Azure Redis cache

Azure Redis Cache consente di accedere ai server Redis che ospitato presso un Data Center di Azure. Funge da una facciata che fornisce il controllo di accesso e la sicurezza. È possibile eseguire il provisioning di una cache tramite il portale di Azure.

Il portale rappresenta un numero di configurazioni predefinite. Questi sono compresi tra una cache 53 GB in esecuzione come servizio dedicato che supporta le comunicazioni SSL (per privacy) e replica principale/secondario con un contratto di servizio di disponibilità 99,9%, fino a una cache 0 MB 25 senza replica (senza garanzie di disponibilità) in esecuzione su hardware condiviso.

Tramite il portale di Azure, è possibile inoltre configurare i criteri di eliminazione della cache e controllare l'accesso alla cache mediante l'aggiunta di utenti ai ruoli disponibili.  Questi ruoli che definiscono le operazioni che i membri possono eseguire, includono proprietario, collaboratori e utilità per la lettura. Ad esempio, i membri del ruolo proprietario hanno poter controllare completamente la cache (inclusi sicurezza) e il relativo contenuto, i membri del ruolo di collaboratore possono leggere e scrivere informazioni nella cache e i membri del ruolo Lettore possono recuperare solo i dati dalla cache.

La maggior parte delle attività amministrative vengono eseguite tramite il portale di Azure. Per questo motivo, molti dei comandi amministrativi sono disponibili nella versione standard di Redis non sono disponibili, compresa la possibilità di modificare la configurazione a livello di programmazione, arrestare il server Redis, configurare ulteriori forme subordinate o forzare la salvare dati su disco.

Portale di Azure include un grafico semplice che consente di eseguire il monitoraggio delle prestazioni della cache. Ad esempio, è possibile visualizzare il numero di connessioni effettuate, il numero di richieste viene eseguita, il volume di lettura e scrittura e il numero di accessi e errori cache. Usa queste informazioni, è possibile determinare l'efficacia della cache e se necessario, passare a un'altra configurazione o modificare i criteri di eliminazione.

Inoltre, è possibile creare avvisi che inviano messaggi di posta elettronica a un amministratore se uno o più parametri fondamentali compresi all'esterno di un intervallo previsto. Ad esempio, è consigliabile avvisare un amministratore se il numero di errori cache supera un valore specificato nell'ultima ora, in quanto significa che la cache potrebbe essere troppo piccola o dati potrebbero essere da rimuovere troppo rapidamente.

È inoltre possibile monitorare l'utilizzo di rete per la cache, CPU e memoria.

Per ulteriori informazioni ed esempi in cui viene illustrato come creare e configurare una Cache Redis Azure, visitare la pagina [Introduzione ad Azure Redis Cache](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) sul blog di Azure.

## <a name="caching-session-state-and-html-output"></a>Memorizzazione nella cache sessione e l'output HTML

Se si sta creando applicazioni web ASP.NET che esegue utilizzando ruoli web Azure, è possibile salvare sessione stato informazioni e output HTML in una Cache Redis Azure. Il provider di stato sessione per Azure Redis Cache consente di condividere le informazioni sulla sessione tra diverse istanze di un'applicazione web ASP.NET ed è molto utile nelle web farm situazioni in cui affinità client / server non è disponibile e la memorizzazione nella cache dati sessione in memoria non appropriato.

Mediante il provider di stato sessione con Azure Redis Cache offre vari vantaggi, tra cui:

- Lo stato della sessione di condivisione con un numero elevato di istanze di applicazioni web ASP.NET.
- Fornire la scalabilità.
- Accesso controllato e simultanea per gli stessi dati di stato sessione di supporto per più lettori e singolo processo di scrittura.
- Utilizzo di compressione per risparmiare memoria e migliorare le prestazioni di rete.

Per ulteriori informazioni, visitare la pagina [provider dello stato di sessione ASP.NET per Azure Redis Cache](redis-cache/cache-aspnet-session-state-provider.md) del sito Web Microsoft.

> [AZURE.NOTE] Non usare il provider di stato della sessione per Azure Redis Cache con applicazioni ASP.NET eseguite all'esterno dell'ambiente Azure. La latenza di accesso alla cache all'esterno di Azure consente di eliminare le prestazioni di memorizzazione nella cache dei dati.

Analogamente, il provider di cache di output per Azure Redis Cache consente di salvare le risposte HTTP generate da un'applicazione web ASP.NET. Mediante il provider di cache di output con Azure Redis Cache, è possibile migliorare i tempi di risposta di applicazioni che il rendering di output HTML complessi. Istanze dell'applicazione che generano risposte simili inserirvi utilizzare i frammenti di output condiviso nella cache, invece di generare il codice HTML output afresh. Per ulteriori informazioni, visitare la pagina [provider della cache di output ASP.NET per Azure Redis Cache](redis-cache/cache-aspnet-output-cache-provider.md) del sito Web Microsoft.

## <a name="building-a-custom-redis-cache"></a>Creazione di una cache Redis personalizzata

Azure Cache Redis funge da una facciata ai server Redis sottostante. Attualmente supporta un insieme fisso di configurazioni ma non sono disponibili per il raggruppamento Redis. Se si richiede una configurazione avanzata che non rientrano nella cache Azure Redis (ad esempio una cache maggiore di 53 GB) è possibile creare e ospitare il proprio server Redis utilizzando macchine virtuali di Azure.

In questo modo un processo potenzialmente complesso potrebbe essere necessario creare più macchine virtuali di agire come nodi master e subordinati se si desidera implementare la replica. Inoltre, se si desidera creare un cluster, è necessario più schemi e server subordinati. Una topologia di replica cluster minimo che fornisce un numero elevato di disponibilità e scalabilità comprende almeno sei macchine virtuali organizzate in tre coppie di server/secondaria (un cluster deve contenere almeno tre nodi master).

Ogni coppia / secondaria deve trovarsi vicini per limitare la latenza. Tuttavia, ogni set di coppie di parole può essere eseguito in diversi Data Center Azure disponibile in diverse aree geografiche, se si desidera individuare dati memorizzati nella cache Chiudi per le applicazioni di più facile usarlo. La pagina [In esecuzione Redis in una macchina virtuale Linux CentOS in Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) del sito Web Microsoft illustra un esempio che illustra come creare e configurare un nodo Redis in esecuzione come una macchina virtuale Azure.

[AZURE.NOTE] Si noti che se si implementa cache Redis in questo modo, è responsabile per il monitoraggio, la gestione e protezione del servizio.

## <a name="partitioning-a-redis-cache"></a>Partizione una cache Redis

Partizione la cache comporta la suddivisione della cache tra più computer. Questa struttura offre i seguenti vantaggi utilizzando un server di cache singolo, tra cui:

- La creazione di una cache è molto più grande che possono essere memorizzati in un unico server.
- La distribuzione dei dati su server, miglioramento della disponibilità. Se un server non riesce o risulta inaccessibile, i dati che contiene non è disponibile, ma i dati sui server restanti possibile accedervi. Per una cache, questo non è fondamentale, perché i dati memorizzati nella cache solo una copia dei dati contenuti in un database temporanea. I dati memorizzati in un server che diventa inaccessibile possono invece memorizzati in un server diverso.
- Diffusione il carico su server, migliorando così le prestazioni e scalabilità.
- Dati Geolocating chiudere agli utenti di accedervi, riducendo la latenza.

Per una cache, la forma più comune delle partizioni è sharding. In questa strategia ogni partizione (o condiviso) è una cache Redis vero e proprio. Dati vengono richiesto di una partizione specifica utilizzando la logica sharding, che può usare diversi approcci per distribuire i dati. [Motivo Sharding](http://msdn.microsoft.com/library/dn589797.aspx) sono disponibili ulteriori informazioni sull'implementazione sharding.

Per implementare partizioni in una cache Redis, è possibile eseguire uno dei metodi seguenti:

- _Distribuzione query sul lato server._ In questa tecnica, un'applicazione client invia una richiesta di uno dei server Redis che compongono la cache (probabilmente del server più vicino). Ogni server Redis archivia i metadati che descrivono la partizione che contiene e contiene anche informazioni su cui si trovano le partizioni in altri server. Il server Redis esamina la richiesta del client. Se possibile risolvere localmente, verranno eseguite l'operazione. In caso contrario inoltra la richiesta al server appropriato. Questo modello implementato da Redis cluster e descritto dettagliatamente più avanti nella pagina [Redis esercitazione cluster](http://redis.io/topics/cluster-tutorial) nel sito Web Redis. Redis cluster è trasparente alle applicazioni client e server Redis aggiuntivi possono essere aggiunti a cluster (e dati nuovamente distribuiti) senza riconfigurare i client.

- _Sul lato client divisione._ In questo modello, l'applicazione client contiene una logica (eventualmente in forma di una raccolta) che reindirizza al server Redis appropriato. Questo approccio è utilizzabile con Azure Redis Cache. Creare più Azure Redis cache (uno per ogni partizione di dati) e implementare la logica sul lato client, che indirizza le richieste alla cache corretta. Se la combinazione di partizione cambia (se aggiuntive Azure Redis cache vengono create, ad esempio), le applicazioni client potrebbero essere necessario riconfigurare.

- _Assistito proxy partizioni._ In questo schema il client applicazioni invia le richieste in un servizio proxy intermedia che comprende come sono suddiviso i dati e instrada la richiesta al appropriato Redis server. Questo approccio è utilizzabile anche con Azure Redis Cache. il servizio proxy può essere implementato come un servizio cloud Azure. Questo approccio è necessario un livello di complessità per implementare il servizio e le richieste di possono richiedere più tempo per eseguire rispetto all'utilizzo del client partizioni.

La pagina [partizionamento: come dividere i dati tra più istanze di Redis](http://redis.io/topics/partitioning) sul Redis sito Web fornisce ulteriori informazioni sull'implementazione delle partizioni con Redis.

### <a name="implement-redis-cache-client-applications"></a>Implementare le applicazioni client di cache Redis

Redis supporta le applicazioni client scritte in numerosi linguaggi di programmazione. Se si creano nuove applicazioni utilizzando .NET Framework, si consiglia consiste nell'utilizzare la libreria di client StackExchange.Redis. Questa raccolta contiene un modello a oggetti .NET Framework che estrae i dettagli per la connessione a un server Redis, invio di comandi e la ricezione di risposte. È disponibile in Visual Studio come pacchetto NuGet. È possibile utilizzare la stessa raccolta per connettersi a una Cache Redis Azure o una cache Redis personalizzata ospitati in una macchina virtuale.

Per connettersi a un server Redis utilizzare statica `Connect` metodo del `ConnectionMultiplexer` classe. La connessione creata da questo metodo è progettata per essere utilizzato per tutta la durata dell'applicazione client e la stessa connessione può essere usata dal più thread. Non riconnettere e disconnettere ogni volta che si esegue un'operazione di Redis perché questo può ridurre le prestazioni.

È possibile specificare i parametri di connessione, ad esempio l'indirizzo dell'host Redis e la password. Se si utilizza Azure Redis Cache, la password è la chiave primaria o secondaria generati per Azure Redis Cache tramite il portale di gestione di Azure.

Dopo aver connesso al server Redis, è possibile ottenere un quadratino sul database Redis che funge da cache. Connessione Redis fornisce il `GetDatabase` metodo per eseguire questa operazione. È quindi possibile recuperare elementi dalla cache e archiviare i dati nella cache utilizzando il `StringGet` e `StringSet` metodi. Questi metodi aspettarsi una chiave come parametro e restituire l'elemento nella cache che contiene un valore corrispondente (`StringGet`) o aggiungere l'elemento alla cache con questo tasto (`StringSet`).

A seconda della posizione del server Redis molte operazioni comportare alcune latenza durante una richiesta di trasmissione nel server e viene restituita una risposta al client. La libreria StackExchange fornisce versioni asincrone di molti dei metodi esposti per applicazioni client di rimanere attivo. Questi metodi supportano il [Modello asincrono basato su attività](http://msdn.microsoft.com/library/hh873175.aspx) in .NET Framework.

Frammento di codice riportato di seguito viene illustrato un metodo denominato `RetrieveItem`. Viene illustrato un'implementazione del modello di riposo cache in base a Redis e la libreria StackExchange. Il metodo accetta un valore di chiave stringa e un tentativo di recuperare la voce corrispondente dalla cache di Redis chiamando il `StringGetAsync` metodo (la versione asincrona di `StringGet`).

Se l'elemento non viene trovato, vengono recuperato dall'origine dati sottostante tramite il `GetItemFromDataSourceAsync` metodo (un metodo locale e non fa parte della raccolta StackExchange). Viene quindi aggiunto alla cache utilizzando il `StringSetAsync` metodo in modo che possa essere recuperato in modo più rapido successivo.

```csharp
// Connect to the Azure Redis cache
ConfigurationOptions config = new ConfigurationOptions();
config.EndPoints.Add("<your DNS name>.redis.cache.windows.net");
config.Password = "<Redis cache key from management portal>";
ConnectionMultiplexer redisHostConnection = ConnectionMultiplexer.Connect(config);
IDatabase cache = redisHostConnection.GetDatabase();
...
private async Task<string> RetrieveItem(string itemKey)
{
    // Attempt to retrieve the item from the Redis cache
    string itemValue = await cache.StringGetAsync(itemKey);

    // If the value returned is null, the item was not found in the cache
    // So retrieve the item from the data source and add it to the cache
    if (itemValue == null)
    {
        itemValue = await GetItemFromDataSourceAsync(itemKey);
        await cache.StringSetAsync(itemKey, itemValue);
    }

    // Return the item
    return itemValue;
}
```

Il `StringGet` e `StringSet` metodi non sono limitati per il recupero o la memorizzazione di valori stringa. Possono accettare qualsiasi elemento che viene serializzato come una matrice di byte. Se è necessario salvare un oggetto .NET, è possibile serializzare come flusso e utilizzare la `StringSet` metodo per scrivere la cache.

Analogamente, è possibile leggere un oggetto dalla cache utilizzando il `StringGet` metodo e deserializzazione come oggetto .NET. Il codice seguente mostra un insieme di metodi di estensione per l'interfaccia IDatabase (la `GetDatabase` metodo di una connessione Redis restituisce un `IDatabase` oggetto) e codice di esempio che utilizza questi metodi per leggere e scrivere un `BlogPost` oggetto alla cache:

```csharp
public static class RedisCacheExtensions
{
    public static async Task<T> GetAsync<T>(this IDatabase cache, string key)
    {
        return Deserialize<T>(await cache.StringGetAsync(key));
    }

    public static async Task<object> GetAsync(this IDatabase cache, string key)
    {
        return Deserialize<object>(await cache.StringGetAsync(key));
    }

    public static async Task SetAsync(this IDatabase cache, string key, object value)
    {
        await cache.StringSetAsync(key, Serialize(value));
    }

    static byte[] Serialize(object o)
    {
        byte[] objectDataAsStream = null;

        if (o != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream())
            {
                binaryFormatter.Serialize(memoryStream, o);
                objectDataAsStream = memoryStream.ToArray();
            }
        }

        return objectDataAsStream;
    }

    static T Deserialize<T>(byte[] stream)
    {
        T result = default(T);

        if (stream != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream(stream))
            {
                result = (T)binaryFormatter.Deserialize(memoryStream);
            }
        }

        return result;
    }
}
```

Il codice seguente illustra un metodo denominato `RetrieveBlogPost` che utilizza questi metodi di estensione per leggere e scrivere un serializzabile `BlogPost` oggetto alla cache del modello di riposo cache:

```csharp
// The BlogPost type
[Serializable]
private class BlogPost
{
    private HashSet<string> tags = new HashSet<string>();

    public BlogPost(int id, string title, int score, IEnumerable<string> tags)
    {
        this.Id = id;
        this.Title = title;
        this.Score = score;
        this.tags = new HashSet<string>(tags);
    }

    public int Id { get; set; }
    public string Title { get; set; }
    public int Score { get; set; }
    public ICollection<string> Tags { get { return this.tags; } }
}
...
private async Task<BlogPost> RetrieveBlogPost(string blogPostKey)
{
    BlogPost blogPost = await cache.GetAsync<BlogPost>(blogPostKey);
    if (blogPost == null)
    {
        blogPost = await GetBlogPostFromDataSourceAsync(blogPostKey);
        await cache.SetAsync(blogPostKey, blogPost);
    }

    return blogPost;
}
```

Redis supporta comando pipeline se un'applicazione client invia più richieste asincrone. Redis può eseguire multiplexing richieste utilizzando la stessa connessione anziché ricevere e rispondere ai comandi in una sequenza strict.

Questo approccio consente di ridurre la latenza, in modo più efficiente della rete. Frammento di codice seguente viene illustrato un esempio che recupera i dettagli di due clienti contemporaneamente. Il codice invia due richieste e quindi elaborazione alcuni altri (non illustrata) prima in attesa di ricevere i risultati. Il `Wait` metodo dell'oggetto cache è simile a .NET Framework `Task.Wait` metodo:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
var task1 = cache.StringGetAsync("customer:1");
var task2 = cache.StringGetAsync("customer:2");
...
var customer1 = cache.Wait(task1);
var customer2 = cache.Wait(task2);
```

La pagina [Azure Redis Cache documentazione](https://azure.microsoft.com/documentation/services/cache/) del sito Web Microsoft sono disponibili ulteriori informazioni su come scrivere applicazioni client che è possono utilizzare la Cache di Redis Azure. Ulteriori informazioni sono disponibili nella [pagina utilizzo di base](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) sul sito Web StackExchange.Redis.

La pagina del sito stesso [tubazioni e multiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) fornisce altre informazioni sulle operazioni asincrone e pipeline con Redis e la libreria StackExchange.  La sezione successiva in questo articolo, utilizzando Redis la memorizzazione nella cache, sono disponibili alcuni esempi di tecniche più avanzate che è possibile applicare ai dati contenuti in una cache Redis.

## <a name="using-redis-caching"></a>Utilizzo della cache Redis

L'uso di più semplice di Redis per la memorizzazione nella cache preoccupazioni è coppie di parole chiave-valore in cui il valore è una stringa di lunghezza non autorizzato che può contenere dati binari non interpretata. (È essenzialmente una matrice di byte che possono essere considerati come stringa). In questo scenario è stato illustrato nelle applicazioni client di implementare Redis Cache sezione in precedenza in questo articolo.

Si noti che le chiavi contengono anche non interpretati dati, è possibile usarne alcuna informazione binaria come chiave. Il più la chiave è, tuttavia, più spazio necessario per archiviare e il il tempo impiegato per eseguire operazioni di ricerca. Per facilità di utilizzo e la semplicità di manutenzione, progettare con attenzione lo spazio delle chiavi e utilizzare i tasti significativi (ma non dettagliato).

Ad esempio tasti strutturati, ad esempio "cliente: 100" per rappresentare la chiave per il cliente con ID 100 anziché semplicemente "100". Questa combinazione consente di distinguere i valori che memorizzano diversi tipi di dati. Ad esempio, è possibile anche utilizzare il tasto "ordini: 100" per rappresentare la chiave per l'ordine con ID 100.

Oltre a unidimensionale stringhe binarie, un valore in una coppia di chiave valore Redis può contenere anche più strutturato informazioni, inclusi gli elenchi, imposta (ordinate e non ordinati) ed esegue l'hashing. Redis fornisce un insieme di comandi completa in grado di modificare questi tipi e molti di questi comandi sono disponibili per le applicazioni di .NET Framework tramite una libreria client, ad esempio StackExchange. Pagina [Introduzione a Redis astrazioni e tipi di dati](http://redis.io/topics/data-types-intro) nel sito Web Redis offre una panoramica più dettagliata di questi tipi e i comandi che è possibile utilizzare per modificarli.

In questa sezione sono riepilogate alcune casi di utilizzo comune per questi tipi di dati e i comandi.

### <a name="perform-atomic-and-batch-operations"></a>Eseguire atomica e operazioni batch

Redis supporta una serie di operazioni get e set atomiche ai valori stringa. Queste operazioni rimuovere i rischi di competizione possibili che possono verificarsi quando si usa separata `GET` e `SET` comandi. Operazioni disponibili includono:

- `INCR`, `INCRBY`, `DECR`, e `DECRBY`, quale eseguire operazioni di incremento e decremento atomiche su valori di dati numerici integer. La libreria StackExchange fornisce versioni di overload di `IDatabase.StringIncrementAsync` e `IDatabase.StringDecrementAsync` metodi per eseguire queste operazioni e restituire il valore risultante memorizzato nella cache. Frammento di codice riportato di seguito viene illustrato come utilizzare questi metodi:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  await cache.StringSetAsync("data:counter", 99);
  ...
  long oldValue = await cache.StringIncrementAsync("data:counter");
  // Increment by 1 (the default)
  // oldValue should be 100

  long newValue = await cache.StringDecrementAsync("data:counter", 50);
  // Decrement by 50
  // newValue should be 50
  ```

- `GETSET`, che recupera il valore è associato alla chiave e impostato su un nuovo valore. Raccolta StackExchange rende disponibili tramite l'operazione di `IDatabase.StringGetSetAsync` metodo. Il frammento di codice seguente è illustrato un esempio di questo metodo. Questo codice restituisce il valore corrente associato con la chiave "dati: contatore" dell'esempio precedente. Quindi Ripristina il valore di questo tasto nuovamente a zero, tutti come parte della stessa operazione:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  string oldValue = await cache.StringGetSetAsync("data:counter", 0);
  ```

- `MGET`e `MSET`, che può restituire o modificare un set di valori stringa come un'unica operazione. Il `IDatabase.StringGetAsync` e `IDatabase.StringSetAsync` overload metodi per supportare questa funzionalità, come illustrato nell'esempio seguente:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  // Create a list of key-value pairs
  var keysAndValues =
      new List<KeyValuePair<RedisKey, RedisValue>>()
      {
          new KeyValuePair<RedisKey, RedisValue>("data:key1", "value1"),
          new KeyValuePair<RedisKey, RedisValue>("data:key99", "value2"),
          new KeyValuePair<RedisKey, RedisValue>("data:key322", "value3")
      };

  // Store the list of key-value pairs in the cache
  cache.StringSet(keysAndValues.ToArray());
  ...
  // Find all values that match a list of keys
  RedisKey[] keys = { "data:key1", "data:key99", "data:key322"};
  RedisValue[] values = null;
  values = cache.StringGet(keys);
  // values should contain { "value1", "value2", "value3" }
  ```

È anche possibile combinare più operazioni in una sola transazione Redis come descritto nella sezione Redis transazioni e dei batch in precedenza in questo articolo. Raccolta StackExchange fornisce supporto per le transazioni attraverso il `ITransaction` interfaccia.

Si crea un `ITransaction` oggetto utilizzando il `IDatabase.CreateTransaction` metodo. Richiamare comandi per la transazione utilizzando i metodi disponibili per la `ITransaction` oggetto.

Il `ITransaction` interfaccia consente di accedere a un insieme di metodi che è simile a quelle accede il `IDatabase` interfaccia, tranne per il fatto che tutti i metodi sono asincroni. Ciò significa che sono solo eseguite quando il `ITransaction.Execute` viene richiamato. Valore restituito dalla `ITransaction.Execute` metodo indica se la transazione è stata creata correttamente (true) o se non è riuscito (false).

Frammento di codice seguente mostra un esempio che due contatori incrementi e diminuisce come parte della stessa transazione:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
ITransaction transaction = cache.CreateTransaction();
var tx1 = transaction.StringIncrementAsync("data:counter1");
var tx2 = transaction.StringDecrementAsync("data:counter2");
bool result = transaction.Execute();
Console.WriteLine("Transaction {0}", result ? "succeeded" : "failed");
Console.WriteLine("Result of increment: {0}", tx1.Result);
Console.WriteLine("Result of decrement: {0}", tx2.Result);
```

Ricordare che le transazioni Redis a differenza delle transazioni nel database relazionali. Il `Execute` metodo accoda semplicemente tutti i comandi che compongono la transazione per l'esecuzione e se uno di questi non è corretto della transazione viene interrotta. Se tutti i comandi sono stati accodati correttamente, ogni comando viene eseguito in modo asincrono.

Se i comandi non riesce, gli altri continuare comunque l'elaborazione. Se è necessario verificare che un comando è stato completato correttamente, è necessario recuperare i risultati del comando utilizzando la proprietà di **risultato** dell'attività corrispondente, come illustrato nell'esempio precedente. Leggere la proprietà **risultato** bloccare il thread chiamante fino al completamento dell'attività.

Per ulteriori informazioni, vedere la pagina [transazioni nel Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) nel sito Web StackExchange.Redis.

Quando si eseguono operazioni batch, è possibile utilizzare il `IBatch` interfaccia della raccolta StackExchange. Questa interfaccia consente di accedere a un insieme di metodi simili a quelli accessibili tramite il `IDatabase` interfaccia, tranne per il fatto che tutti i metodi sono asincroni.

Si crea un `IBatch` oggetto utilizzando il `IDatabase.CreateBatch` metodo e quindi eseguire il batch utilizzando il `IBatch.Execute` metodo, come illustrato nell'esempio seguente. Questo codice semplicemente imposta un valore stringa, incrementi e riduce lo stessa contatori utilizzati nell'esempio precedente e vengono visualizzati i risultati:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
IBatch batch = cache.CreateBatch();
batch.StringSetAsync("data:key1", 11);
var t1 = batch.StringIncrementAsync("data:counter1");
var t2 = batch.StringDecrementAsync("data:counter2");
batch.Execute();
Console.WriteLine("{0}", t1.Result);
Console.WriteLine("{0}", t2.Result);
```

È importante tenere presente che a differenza di una transazione, se un comando in un batch non riesce perché è corretto, gli altri comandi ancora verifichino. Il `IBatch.Execute` metodo non restituisce alcuna indicazione di esito positivo o negativo.

### <a name="perform-fire-and-forget-cache-operations"></a>Eseguire fire e dimenticare operazioni cache

Redis supporta fire e dimenticare operazioni con contrassegni di comando. In questo caso, il client semplicemente avvia un'operazione ma non ha alcun interesse nel risultato e non attendere che il comando per il completamento. Nell'esempio seguente viene descritto come eseguire il comando /incr come un fire e dimenticare operazione:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
await cache.StringSetAsync("data:key1", 99);
...
cache.StringIncrement("data:key1", flags: CommandFlags.FireAndForget);
```

### <a name="specify-automatically-expiring-keys"></a>Specificare le chiavi automaticamente alla scadenza

Quando si archivia un elemento in una cache Redis, è possibile specificare un timeout dopo che l'elemento verrà automaticamente rimosso dalla cache. È inoltre possibile cercare quanto più tempo dispone di una chiave prima della scadenza utilizzando il `TTL` comando. Questo comando è disponibile per le applicazioni StackExchange utilizzando il `IDatabase.KeyTimeToLive` metodo.

Frammento di codice riportato di seguito viene illustrato come impostare una scadenza di 20 secondi su una chiave e la durata rimanente della chiave di query:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration time of 20 seconds
await cache.StringSetAsync("data:key1", 99, TimeSpan.FromSeconds(20));
...
// Query how much time a key has left to live
// If the key has already expired, the KeyTimeToLive function returns a null
TimeSpan? expiry = cache.KeyTimeToLive("data:key1");
```

È inoltre possibile impostare la scadenza su una specifica data e ora tramite il comando di scadenza, è disponibile nella raccolta StackExchange come la `KeyExpireAsync` metodo:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration date of midnight on 1st January 2015
await cache.StringSetAsync("data:key1", 99);
await cache.KeyExpireAsync("data:key1",
    new DateTime(2015, 1, 1, 0, 0, 0, DateTimeKind.Utc));
...
```

> _Suggerimento:_ È possibile rimuovere manualmente un elemento dalla cache utilizzando il comando DEL disponibile attraverso la raccolta di StackExchange come la `IDatabase.KeyDeleteAsync` metodo.

### <a name="use-tags-to-cross-correlate-cached-items"></a>Usare i contrassegni per cross-correlazione elementi memorizzati nella cache

Un set di Redis è un insieme di più elementi che condividono una singola chiave. È possibile creare una serie tramite il comando SADD. È possibile recuperare gli elementi in una serie tramite il comando SMEMBERS. Il comando SADD con di implementazione nella libreria StackExchange il `IDatabase.SetAddAsync` metodo e il SMEMBERS comando con il `IDatabase.SetMembersAsync` metodo.

È anche possibile combinare set esistenti per creare nuovi set utilizzando le SDIFF (differenza di set), TUBICINO (set intersezione) e i comandi SUNION (unione set). Raccolta StackExchange unisce queste operazioni nel `IDatabase.SetCombineAsync` metodo. Il primo parametro a questo metodo consente di specificare l'operazione di impostazione per l'esecuzione.

Frammenti di codice seguenti mostrano come set possono essere utili per rapidamente l'archiviazione e recupero di insiemi di elementi correlati. Questo codice utilizza il `BlogPost` tipo descritta nella sezione implementare Redis Cache le applicazioni Client in precedenza in questo articolo.

A `BlogPost` oggetto contiene quattro campi, ovvero un ID, un titolo, un punteggio di classificazione e un insieme di tag. Primo frammento di codice seguente mostra i dati di esempio viene utilizzati per popolare un elenco c# di `BlogPost` oggetti:

```csharp
List<string[]> tags = new List<string[]>()
{
    new string[] { "iot","csharp" },
    new string[] { "iot","azure","csharp" },
    new string[] { "csharp","git","big data" },
    new string[] { "iot","git","database" },
    new string[] { "database","git" },
    new string[] { "csharp","database" },
    new string[] { "iot" },
    new string[] { "iot","database","git" },
    new string[] { "azure","database","big data","git","csharp" },
    new string[] { "azure" }
};

List<BlogPost> posts = new List<BlogPost>();
int blogKey = 0;
int blogPostId = 0;
int numberOfPosts = 20;
Random random = new Random();
for (int i = 0; i < numberOfPosts; i++)
{
    blogPostId = blogKey++;
    posts.Add(new BlogPost(
        blogPostId,               // Blog post ID
        string.Format(CultureInfo.InvariantCulture, "Blog Post #{0}",
            blogPostId),          // Blog post title
        random.Next(100, 10000),  // Ranking score
        tags[i % tags.Count]));   // Tags--assigned from a collection
                                  // in the tags list
}
```

È possibile archiviare i contrassegni per ogni `BlogPost` oggetto come un set di una cache Redis e associare l'ID di ogni set di `BlogPost`. In questo modo un'applicazione di trovare rapidamente tutti i contrassegni che appartengono a un post di blog specifico. Per attivare la ricerca nella direzione opposta e trovare tutti i post di blog che condividono un tag specifico, è possibile creare un'altra coppia che contiene il post di blog che fa riferimento l'ID di tag nella chiave:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Tags are easily represented as Redis Sets
foreach (BlogPost post in posts)
{
    string redisKey = string.Format(CultureInfo.InvariantCulture,
        "blog:posts:{0}:tags", post.Id);
    // Add tags to the blog post in Redis
    await cache.SetAddAsync(
        redisKey, post.Tags.Select(s => (RedisValue)s).ToArray());

    // Now do the inverse so we can figure how which blog posts have a given tag
    foreach (var tag in post.Tags)
    {
        await cache.SetAddAsync(string.Format(CultureInfo.InvariantCulture,
            "tag:{0}:blog:posts", tag), post.Id);
    }
}
```

Queste strutture consentono di eseguire molte query comuni in modo efficace. Ad esempio, è possibile trovare e visualizzare tutti i contrassegni per il post di blog 1 alla seguente:

```csharp
// Show the tags for blog post #1
foreach (var value in await cache.SetMembersAsync("blog:posts:1:tags"))
{
    Console.WriteLine(value);
}
```

È possibile trovare tutti i contrassegni comuni a blog pubblicare post di blog e 1 2 eseguendo un'operazione di intersezione insieme, come indicato di seguito:

```csharp
// Show the tags in common for blog posts #1 and #2
foreach (var value in await cache.SetCombineAsync(SetOperation.Intersect, new RedisKey[]
    { "blog:posts:1:tags", "blog:posts:2:tags" }))
{
    Console.WriteLine(value);
}
```

E si possono trovare tutti i post di blog che contengono un tag specifico:

```csharp
// Show the ids of the blog posts that have the tag "iot".
foreach (var value in await cache.SetMembersAsync("tag:iot:blog:posts"))
{
    Console.WriteLine(value);
}
```

### <a name="find-recently-accessed-items"></a>Ricerca di recente elementi

Un'attività comune delle applicazioni è per trovare più di recente elementi. Ad esempio, un sito blog essere utile visualizzare informazioni sulla lettura più recente post di blog.

È possibile implementare questa funzionalità, usando un elenco di Redis. Un elenco di Redis contiene più elementi che condividono la stessa chiave. L'elenco funge da coda doppia. È possibile inserire elementi a un'estremità dell'elenco utilizzando il LPUSH (push a sinistra) e i comandi RPUSH (push destra). È possibile recuperare gli elementi da uno dei due fine dell'elenco, utilizzare i comandi LPOP e RPOP. È inoltre possibile restituire un insieme di elementi, utilizzare i comandi LRANGE e DISPONI.

I frammenti di codice seguenti mostrano come è possibile eseguire queste operazioni utilizzando la libreria StackExchange. Questo codice utilizza il `BlogPost` tipo degli esempi precedenti. Mentre un post di blog viene letto da un utente, la `IDatabase.ListLeftPushAsync` metodo inserisce il titolo del post di blog in un elenco associato insieme al tasto "blog:recent_posts" nella cache Redis.

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:recent_posts";
BlogPost blogPost = ...; // Reference to the blog post that has just been read
await cache.ListLeftPushAsync(
    redisKey, blogPost.Title); // Push the blog post onto the list
```

Durante la lettura altri post di blog, i titoli vengono inseriti nello stesso elenco. Nell'elenco è ordinato in sequenza in cui sono stati aggiunti i titoli. Lettura più recente post di blog sono verso la fine dell'elenco a sinistra. (Se il post di blog stesso lettura venga ripetuto, avrà più voci nell'elenco.)

È possibile visualizzare i titoli dei post di lettura più recente utilizzando il `IDatabase.ListRange` metodo. Questo metodo consente di accedere alla chiave che contiene l'elenco, un punto di partenza e un punto finale. Il codice seguente recupera i titoli dei post di blog (voci da 0 a 9) 10 all'estremità più a sinistra dell'elenco:

```csharp
// Show latest ten posts
foreach (string postTitle in await cache.ListRangeAsync(redisKey, 0, 9))
{
    Console.WriteLine(postTitle);
}
```

Si noti che la `ListRangeAsync` metodo non rimuove gli elementi dall'elenco. A questo scopo è possibile utilizzare il `IDatabase.ListLeftPopAsync` e `IDatabase.ListRightPopAsync` metodi.

Per impedire che l'elenco crescita tempo indefinito, è possibile riforma periodicamente gli elementi da tagliare l'elenco. Il frammento di codice seguente viene illustrato come rimuovere tutti ma i cinque più a sinistra gli elementi dall'elenco:

```csharp
await cache.ListTrimAsync(redisKey, 0, 5);
```

### <a name="implement-a-leader-board"></a>Implementare una bacheca carattere di riempimento

Per impostazione predefinita, gli elementi in un set non vengono inseriti in un ordine specifico. È possibile creare un set ordinato utilizzando il comando ZADD (la `IDatabase.SortedSetAdd` metodo nella libreria di StackExchange). Ordinare gli elementi con un valore numerico chiamato un punteggio, viene fornito come parametro per il comando.

Frammento di codice riportato aggiunge il titolo di un post di blog in un elenco ordinato. In questo esempio, ogni post di blog è installato anche un campo di punteggio che contiene la classificazione del post di blog.

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:post_rankings";
BlogPost blogPost = ...; // Reference to a blog post that has just been rated
await cache.SortedSetAddAsync(redisKey, blogPost.Title, blogpost.Score);
```

È possibile recuperare i titoli dei post di blog e i punteggi in senso crescente punteggio utilizzando il `IDatabase.SortedSetRangeByRankWithScores` metodo:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(redisKey))
{
    Console.WriteLine(post);
}
```

> [AZURE.NOTE] Raccolta StackExchange vengono forniti anche i `IDatabase.SortedSetRangeByRankAsync` metodo, che vengono restituiti i dati nell'ordine di punteggio, ma non restituire i punteggi.

È anche possibile recuperare gli elementi in ordine decrescente di punteggi e limitare il numero di elementi restituiti fornendo parametri aggiuntivi per il `IDatabase.SortedSetRangeByRankWithScoresAsync` metodo. Nell'esempio seguente vengono visualizzati i titoli e i punteggi delle prime 10 classificati i post di blog:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(
                               redisKey, 0, 9, Order.Descending))
{
    Console.WriteLine(post);
}
```

Nell'esempio seguente viene utilizzato il `IDatabase.SortedSetRangeByScoreWithScoresAsync` metodo, è possibile utilizzare per limitare gli elementi che vengono restituiti a quelli che si trovano all'interno di un determinato punteggio intervallo:

```csharp
// Blog posts with scores between 5000 and 100000
foreach (var post in await cache.SortedSetRangeByScoreWithScoresAsync(
                               redisKey, 5000, 100000))
{
    Console.WriteLine(post);
}
```

### <a name="message-by-using-channels"></a>Messaggio di utilizzo dei canali

Oltre alle funge da una cache dei dati, un server Redis è messaggistica tramite un meccanismo di prestazioni elevate editore/sottoscrizione. Applicazioni client possono sottoscrivere un canale e altre applicazioni o servizi pubblicare messaggi per il canale. Applicazioni in sottoscrizione riceveranno quindi questi messaggi e possono elaborare loro.

Redis fornisce il comando SOTTOSCRIVI alle applicazioni client di utilizzare per la sottoscrizione ai canali. Questo comando è previsto il nome di uno o più canali in cui l'applicazione per accettare i messaggi. La libreria StackExchange include il `ISubscription` interfaccia, che consente a un'applicazione di .NET Framework per la sottoscrizione e pubblicare i canali.

Si crea un `ISubscription` oggetto utilizzando il `GetSubscriber` metodo della connessione al server Redis. Quindi ascoltare i messaggi in un canale utilizzando il `SubscribeAsync` metodo dell'oggetto. Nell'esempio seguente viene illustrato come sottoscrivere un canale denominato "dei messaggi: blogPosts":

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
await subscriber.SubscribeAsync("messages:blogPosts", (channel, message) =>
{
    Console.WriteLine("Title is: {0}", message);
});
```

Il primo parametro per il `Subscribe` metodo è il nome del canale. Questo nome le stesse convenzioni utilizzati da chiavi nella cache. Il nome può contenere dati binari, anche se è consigliabile utilizzare le stringhe relativamente breve e significative per assicurare la gestibilità e prestazioni ottimali.

Si noti inoltre che lo spazio dei nomi utilizzata dai canali è distinto da quello utilizzato da tasti. Di conseguenza, che è possibile utilizzare i canali e chiavi che hanno lo stesso nome, anche se questo potrebbe codice dell'applicazione più difficile da gestire.

Il secondo parametro è un delegato di azione. Questo delegato viene eseguito in modo asincrono ogni volta che viene visualizzato un nuovo messaggio sul canale. In questo esempio semplicemente il messaggio nella console (il messaggio conterrà il titolo di un post di blog).

Per pubblicare in un canale, un'applicazione può usare il comando Redis pubblica. La libreria StackExchange fornisce il `IServer.PublishAsync` metodo per eseguire questa operazione. Frammento di codice successivo viene illustrato come pubblicare un messaggio per il canale "dei messaggi: blogPosts":

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
BlogPost blogpost = ...;
subscriber.PublishAsync("messages:blogPosts", blogPost.Title);
```

Ci sono diversi punti che è necessario conoscere sul meccanismo di pubblicazione/sottoscrizione:

- Gli abbonati a più possono sottoscrivere lo stesso canale e quest'ultimo tutte riceverà i messaggi pubblicati per il canale.
- Gli abbonati a solo messaggi pubblicati dopo che hanno sottoscritto. Canali non vengono memorizzati e dopo la pubblicazione di un messaggio, l'infrastruttura di Redis inserisce il messaggio a ciascun sottoscrittore e quindi lo rimuove.
- Per impostazione predefinita, i messaggi vengono ricevuti da sottoscrittori nell'ordine in cui sono stati inviati. In un sistema altamente attivo con un numero elevato di messaggi e molti autori e sottoscrizione, recapito sequenza messaggi garantito può rallentare le prestazioni del sistema. Se ogni messaggio è indipendente e l'ordine è importante, è possibile abilitare elaborazione simultanea dal sistema Redis, che può contribuire a migliorare la velocità di risposta. È possibile ottenere questo in un client StackExchange mediante l'impostazione PreserveAsyncOrder della connessione utilizzato dal server di sottoscrizione falso:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
redisHostConnection.PreserveAsyncOrder = false;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
```

## <a name="related-patterns-and-guidance"></a>Indicazioni e modelli correlati

Il modello seguente potrebbe essere anche i contenuti pertinenti per lo scenario durante l'implementazione di memorizzazione nella cache nelle applicazioni:

- [Una serie di riposo cache](http://msdn.microsoft.com/library/dn589799.aspx): questo modello viene descritto come caricare i dati su richiesta in una cache da un archivio di dati. Questo modello consente anche di mantenere la coerenza tra i dati contenuti nella cache e i dati nell'archivio dati originale.
- [Motivo Sharding](http://msdn.microsoft.com/library/dn589797.aspx) fornisce informazioni sull'implementazione partizione orizzontale per contribuire a migliorare scalabilità quando l'archiviazione e l'accesso volumi elevati di dati.

## <a name="more-information"></a>Ulteriori informazioni

- La pagina di [classe MemoryCache](http://msdn.microsoft.com/library/system.runtime.caching.memorycache.aspx) del sito Web Microsoft
- La pagina di [Azure Redis Cache documentazione](https://azure.microsoft.com/documentation/services/cache/) del sito Web Microsoft
- La pagina delle [Domande Cache Redis Azure](redis-cache/cache-faq.md) del sito Web Microsoft
- La pagina di [modello di configurazione](http://msdn.microsoft.com/library/windowsazure/hh914149.aspx) del sito Web Microsoft
- La pagina di [Modello asincrono basato su attività](http://msdn.microsoft.com/library/hh873175.aspx) nel sito Web di Microsoft
- La pagina [tubazioni e multiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) repo StackExchange.Redis GitHub
- La pagina [Redis persistenza](http://redis.io/topics/persistence) nel sito Web Redis
- La [pagina di replica](http://redis.io/topics/replication) nel sito Web Redis
- La pagina nel sito Web Redis [Redis esercitazione cluster](http://redis.io/topics/cluster-tutorial)
- Il [partizionamento: come dividere i dati tra più istanze di Redis](http://redis.io/topics/partitioning) pagina del sito Web Redis
- Pagina [Con Redis come una Cache LRU](http://redis.io/topics/lru-cache) sul sito Web Redis
- La pagina [transazioni](http://redis.io/topics/transactions) nel sito Web Redis
- La pagina [Redis protezione](http://redis.io/topics/security) del sito Web Redis
- Pagina [Introduzione ad Azure Redis Cache](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) sul blog di Azure
- La pagina del sito Web Microsoft [In esecuzione Redis in una macchina virtuale Linux CentOS in Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx)
- La pagina del [provider di stato sessione ASP.NET per Azure Redis Cache](redis-cache/cache-aspnet-session-state-provider.md) del sito Web Microsoft
- La pagina di [provider della cache di output ASP.NET per Azure Redis Cache](redis-cache/cache-aspnet-output-cache-provider.md) del sito Web Microsoft
- La pagina [All'indirizzo Redis astrazioni e tipi di dati](http://redis.io/topics/data-types-intro) del sito Web Redis
- La pagina di [utilizzo di base](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) sul sito Web StackExchange.Redis
- La pagina [transazioni nel Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) repo StackExchange.Redis
- La [Guida partizione di dati](http://msdn.microsoft.com/library/dn589795.aspx) del sito Web Microsoft
