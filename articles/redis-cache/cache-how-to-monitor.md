<properties 
    pageTitle="Come eseguire il monitoraggio della Cache Redis Azure | Microsoft Azure" 
    description="Informazioni su come monitorare l'integrità e le prestazioni le istanze di Azure Redis Cache" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="sdanie"/>

# <a name="how-to-monitor-azure-redis-cache"></a>Come eseguire il monitoraggio della Cache Redis Azure

Azure Cache Redis offre diverse opzioni per il monitoraggio delle istanze di cache. È possibile visualizzare metriche, aggiungere grafici metriche per il Startboard, personalizzare l'intervallo di data e l'ora di monitoraggio grafici, aggiungere e rimuovere i tipi di grafici metriche e impostare gli avvisi quando vengono soddisfatte determinate condizioni. Questi strumenti consentono di controllare lo stato delle istanze di Azure Redis Cache e consentono di gestire le applicazioni di memorizzazione nella cache.

Quando è attivata diagnostica cache, metriche per le istanze di Azure Redis Cache sono raccolti ogni 30 secondi circa e archiviate in modo che possono essere visualizzati nei grafici metriche e valutate tramite le regole di avviso.

Metriche di cache sono stati raccolti mediante la Redis comando [INFO](http://redis.io/commands/info) . Per ulteriori informazioni sulle informazioni diverse valori utilizzati per ogni cache unità di misura metriche, vedere [segnalazione intervalli e metriche disponibili](#available-metrics-and-reporting-intervals).

Per visualizzare metriche di cache, [Selezionare](cache-configure.md#configure-redis-cache-settings) l'istanza della cache nel [portale di Azure](https://portal.azure.com). Metrica per le istanze di Azure Redis Cache è accessibili nel e **Redis metriche** .

![Redis metriche][redis-cache-redis-metrics-blade]

>[AZURE.IMPORTANT] Se viene visualizzato il seguente messaggio in e il **Redis metriche** , seguire i passaggi indicati nella sezione [attivare cache diagnostica](#enable-cache-diagnostics) per attivare la diagnostica cache.
>
>`Monitoring may not be enabled. Click here to turn on Diagnostics.`

E il **Redis metriche** è grafici di **monitoraggio** che visualizzano metriche di cache. Ogni grafico può essere personalizzato aggiungendo o rimuovendo metriche e la modifica dell'intervallo dei report. Per la visualizzazione e la configurazione di avvisi e le operazioni, e il **Redis Cache** contiene una sezione di **operazioni** che visualizza cache **eventi** e **le regole di avviso**.

## <a name="enable-cache-diagnostics"></a>Attivare la diagnostica cache

Azure Cache Redis offre la possibilità di diagnostica dati archiviati in un account di archiviazione in modo che è possibile utilizzare gli strumenti che si desidera accedere ed elaborare i dati direttamente. Affinché diagnostica cache la raccolta, archiviate e visualizzate nel portale di Azure, un account di archiviazione deve essere configurato. Cache nella stessa regione e abbonamento condividano lo stesso account di archiviazione di diagnostica e quando viene modificata la configurazione viene applicato a tutte le cache nella sottoscrizione in tale area.

Per attivare e configurare la diagnostica cache, passare a e il **Redis Cache** per l'istanza della cache. Se diagnostica non è ancora abilitata, verrà visualizzato un messaggio anziché un grafico di diagnostica.

![Attivare la diagnostica cache][redis-cache-enable-diagnostics]

Fare clic sul messaggio per visualizzare e **l'unità di misura metriche** e fare clic su **impostazioni di diagnostiche** per attivare e configurare le impostazioni di diagnostiche per l'istanza del servizio cache.

![Impostazioni di diagnostica][redis-cache-diagnostic-settings]

![Configurare la diagnostica][redis-cache-configure-diagnostics]

Fare clic **sul** pulsante per attivare la diagnostica cache e visualizzare la configurazione di diagnostica.

Fare clic sulla freccia a destra **dell'Account di archiviazione** per selezionare un account di archiviazione per contenere dati di diagnostica. Per ottenere prestazioni ottimali, selezionare un account di archiviazione nella stessa regione la cache.

Dopo aver configurate le impostazioni di diagnostiche, fare clic su **Salva** per salvare la configurazione. Si noti che potrebbe richiedere qualche minuto rendere effettive le modifiche.

>[AZURE.IMPORTANT] Cache nella stessa regione e sottoscrizione condividono le stesse impostazioni di archiviazione di diagnostica e la configurazione viene modificata (diagnostica attivazione/disattivazione o la modifica dell'account di archiviazione) vengono applicati a tutte le cache nella sottoscrizione in tale area.

Per visualizzare le metriche archiviate, esaminare le tabelle nell'account di archiviazione con i nomi che iniziano con `WADMetrics`. Per ulteriori informazioni sull'accesso metriche archiviate all'esterno di portale di Azure, vedere l'esempio di [dati di Access Redis il monitoraggio della Cache](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) .

>[AZURE.NOTE] Solo i criteri di misurazione vengono memorizzati in account di archiviazione selezionato vengono visualizzati nel portale di Azure. Se si cambia gli account di archiviazione, i dati nella finestra account di archiviazione configurato in precedenza rimarranno disponibili per il download, ma non è visualizzata nel portale di Azure.  

## <a name="available-metrics-and-reporting-intervals"></a>Statistiche disponibili e la segnalazione intervalli

Metrica cache vengono segnalata utilizzando alcuni intervalli di creazione di report, inclusi **ora passata**, **oggi**, **ultima settimana**e **personalizzati**. Alcuni metriche di visualizzare un totale per l'intervallo di creazione di report e **l'unità di misura metriche** per ogni grafico metriche sono visualizzate i valori Media, minimi e massimo per ogni unità di misura metriche nel grafico. 

Ogni unità di misura metriche include due versioni. Unità di misura una metriche misura delle prestazioni per l'intera cache e per la cache che utilizzano il [cluster](cache-how-to-premium-clustering.md)di un'altra versione di unità di misura metriche che include `(Shard 0-9)` le prestazioni di misure nome per un singolo condiviso nella cache. Se ad esempio una cache è 4 shards, `Cache Hits` indica la quantità totale di visite per l'intera cache e `Cache Hits (Shard 3)` è solo i risultati per tale condiviso della cache.

>[AZURE.NOTE] Anche se la cache è inattiva senza applicazioni client active connesso, è possibile riscontrare alcune attività della cache, ad esempio clienti collegati, l'utilizzo di memoria e operazioni eseguite. Questa attività è normale durante l'operazione di un'istanza di Azure Redis Cache.

| Unità di misura metriche            | Descrizione                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Accessi        | Il numero di ricerche chiave esito negativo durante l'intervallo di creazione di report specificato. Esegue il mapping a `keyspace_hits` dalla Redis [informazioni](http://redis.io/commands/info) di comando.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Errori cache      | Il numero di ricerche chiave non riuscite durante l'intervallo di creazione di report specificato. Esegue il mapping a `keyspace_misses` rispetto al comando Redis INFO. Errori cache non necessariamente che si verifica un problema con la cache. Ad esempio, quando si usa il modello di programmazione di riposo cache, un'applicazione effettua una ricerca prima nella cache per un elemento. Se l'elemento non è presente (errata cache), l'elemento è recuperato dal database e aggiunto alla cache per la volta successiva. Errori cache sono comportamento normale per il modello di programmazione di riposo cache. Se il numero di errori cache è supera al previsto, è necessario esaminare la logica dell'applicazione che popola e legge dalla cache. Se gli elementi vengono viene rimosso dalla cache a causa di memoria quindi è possibile che alcuni errori cache, ma è una metrica migliore per verificare la presenza di memoria `Used Memory` o `Evicted Keys`. |
| Client connessi | Il numero di connessioni client alla cache durante l'intervallo di creazione di report specificato. Esegue il mapping a `connected_clients` rispetto al comando Redis INFO. Una volta raggiunto il [limite di connessione](cache-configure.md#default-redis-server-configuration) non riuscirà tentativi di connessione successivi alla cache. Si noti che anche se sono non presenti Nessuna applicazione client attivo, potrebbe comunque essere presenti alcuni istanze di client connessi a causa di processi interni e connessioni.                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Chiavi eliminate      | Il numero di elementi rimosso dalla cache durante l'intervallo di creazione di report specificato a causa di `maxmemory` limite. Esegue il mapping a `evicted_keys` rispetto al comando Redis INFO.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Chiavi scadute      | Il numero di elementi scaduti nella cache durante l'intervallo di creazione di report specificato. Il valore è associato a `expired_keys` rispetto al comando Redis INFO.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Ottiene              | Il numero di operazioni get dalla cache durante l'intervallo di creazione di report specificato. Questo valore è la somma dei seguenti valori comando tutte le informazioni Redis: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, e `cmdstat_getrange`ed equivale alla somma di accessi e non soddisfatte durante l'intervallo di creazione di report.                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Redis carico del Server | La percentuale di cicli in cui il server Redis è occupato di elaborazione e non è in attesa inattivo per i messaggi. Se il contatore raggiunge 100 che significa che il server Redis raggiunto un massimo di prestazioni e la CPU non è possibile elaborare lavorare più velocemente qualsiasi. Se si visualizzano Redis Server condizioni di carico elevato che vengano visualizzate eccezioni di timeout nel client. In questo caso è necessario prendere in considerazione scalabilità o suddividere i dati in più cache.                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Set              | Il numero di operazioni di impostazione per la cache durante l'intervallo di creazione di report specificato. Questo valore è la somma dei seguenti valori comando tutte le informazioni Redis: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange`, e `cmdstat_setnx`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Operazioni totali  | Il numero totale di comandi elaborati dal server cache durante l'intervallo di creazione di report specificato. Il valore è associato a `total_commands_processed` rispetto al comando Redis INFO. Si noti che quando Azure Redis Cache viene utilizzata esclusivamente per pub/sub non vi sono Nessuna metrica per `Cache Hits`, `Cache Misses`, `Gets`, o `Sets`, ma ci sarà `Total Operations` metriche che riflettono l'utilizzo della cache per le operazioni pub/sub.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Memoria utilizzata       | La quantità di memoria cache utilizzata per coppie di parole chiave/valore nella cache in MB durante l'intervallo di creazione di report specificato. Il valore è associato a `used_memory` rispetto al comando Redis INFO. Non include i metadati o frammentazione.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Memoria utilizzata RSS   | La quantità di memoria cache utilizzata in MB durante l'intervallo di creazione di report specificato, comprese frammentazione e i metadati. Il valore è associato a `used_memory_rss` rispetto al comando Redis INFO.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| CPU               | L'utilizzo della CPU del server Azure Redis Cache come percentuale durante l'intervallo di creazione di report specificato. Il valore è associato al sistema operativo `\Processor(_Total)\% Processor Time` contatore delle prestazioni.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Lettura di cache        | La quantità di dati letto dalla cache in megabyte al secondo (MB/s) durante l'intervallo di creazione di report specificato. Questo valore dipende dal schede di rete che supportano la macchina virtuale che ospita la cache e non è Redis specifico. **Questo valore corrisponde alla larghezza di banda di rete utilizzato da questa cache. Se si desidera impostare avvisi per limiti della larghezza di banda di rete sul lato server, quindi crearlo utilizzando questo `Cache Read` contatore. Vedere [la tabella](cache-faq.md#cache-performance) per i limiti della larghezza di banda osservati per cache vari prezzi livelli e le dimensioni.**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Scrittura di cache       | La quantità di dati scritti la cache in megabyte al secondo (MB/s) durante il reporting intervallo. Questo valore dipende dal schede di rete che supportano la macchina virtuale che ospita la cache e non è Redis specifico. Questo valore corrisponde alla larghezza di banda di rete di dati inviati nella cache dal client.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |


## <a name="how-to-view-metrics-and-customize-charts"></a>Come visualizzare metriche e personalizzare i grafici

È possibile visualizzare una panoramica dei criteri di misurazione per la cache nel e **Redis metriche** . Per accedere a **Redis metriche** blade scegliere **tutte le impostazioni** > **Redis metriche**.

![Redis metriche][redis-cache-redis-metrics]


E il **Redis metriche** contiene i grafici seguenti.

| Redis grafico metriche | Metrica visualizzata     |
|------------------|-------------------|
| Lettura di cache e Cache di scrittura | Lettura di cache |
|                            | Scrittura di cache |
| Client connessi      | Client connessi |
| Soddisfatte e non soddisfatte  | Accessi        |
|                  | Errori cache      |
| Comandi totali   | Operazioni totali  |
| Ottiene e set    | Ottiene              |
|                  | Set              |
| Utilizzo della CPU         | CPU           |
| Utilizzo della memoria      | Memoria utilizzata   |
|                   | Memoria utilizzata RSS |
| Redis carico del Server | Carico del server   |
| Conteggio chiavi | Totale chiavi |
|           | Chiavi eliminate |
|           | Chiavi scadute |


Per informazioni più dettagliate dei criteri di misurazione in un grafico specifico e per personalizzare il grafico, fare clic sul grafico da e il **Redis metriche** per visualizzare e **l'unità di misura metriche** per il grafico desiderato.

![Metrica blade][redis-cache-metric-blade]

Tutti gli avvisi che sono impostati su metriche visualizzate in un grafico sono elencati nella parte inferiore della stessa e **unità di misura metriche** per il grafico.

Per aggiungere o rimuovere metriche o modificare l'intervallo di creazione di report, scegliere **Modifica grafico**.

Per aggiungere o rimuovere metriche dal grafico, fare clic sulla casella di controllo accanto al nome della metrica. Per modificare l'intervallo di creazione di report, selezionare l'intervallo desiderato. Per modificare il **tipo di grafico**, fare clic sullo stile desiderato. Una volta apportate le modifiche desiderate, fare clic su **Salva**. 

![Modifica del grafico][redis-cache-edit-chart]

Quando si fa clic su **Salva** le modifiche verranno mantenute fino a quando non si esce e **l'unità di misura metriche** . Quando torna in un secondo momento, verranno visualizzate le unità di misura metriche originale e intervallo di tempo nuovamente. Per ulteriori informazioni sulla personalizzazione dei grafici, vedere [metriche di servizio Monitor](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Per visualizzare le metriche per un periodo di tempo specifico in un grafico, posizionare il mouse su una delle barre specifiche o punti del grafico che corrisponde al tempo desiderato e vengono visualizzate le metriche per tale intervallo.

![Visualizzare i dettagli del grafico][redis-cache-view-chart-details]

## <a name="how-to-monitor-a-premium-cache-with-clustering"></a>Come monitorare una cache premium con il servizio cluster

Cache Premium che hanno [cluster](cache-how-to-premium-clustering.md) abilitato possono contenere fino a 10 shards. Ogni condiviso con il proprio metriche e queste metriche vengono aggregate per fornire metriche per la cache nel suo insieme. Ogni unità di misura metriche include due versioni. Unità di misura una metriche misura delle prestazioni per l'intera cache e un'altra versione di unità di misura metriche che include `(Shard 0-9)` le prestazioni di misure nome per un singolo condiviso nella cache. Se ad esempio una cache è 3 shards, `Cache Hits` indica la quantità totale di visite per l'intera cache e `Cache Hits (Shard 2)` è solo i risultati per tale condiviso della cache.

Ogni grafico monitoraggio consente di visualizzare le metriche di livello superiore per la cache insieme le metriche per ogni condiviso cache.

![Monitor][redis-cache-premium-monitor]

Passando il puntatore del mouse sopra le coordinate consente di visualizzare i dettagli relativi a tale punto nel tempo. 

![Monitor][redis-cache-premium-point-summary]

I valori più grandi sono in genere i valori aggregati per la cache, mentre i valori più bassi sono le singole metriche per il condiviso. Si noti che in questo esempio sono presenti tre shards e accessi alla cache sono distribuiti uniformemente tra il shards.

![Monitor][redis-cache-premium-point-shard]

Per visualizzare ulteriori dettagli fare clic sul grafico per visualizzare una visualizzazione espansa nel e **unità di misura metriche** .

![Monitor][redis-cache-premium-chart-detail]

Per impostazione predefinita ogni grafico include il contatore delle prestazioni cache principale, nonché contatori per shards singoli. È possibile personalizzare questi e il **Grafico di modifica** .

![Monitor][redis-cache-premium-edit]

Per ulteriori informazioni sui contatori prestazioni disponibili, vedere [segnalazione intervalli e metriche disponibili](#available-metrics-and-reporting-intervals).


## <a name="operations-and-alerts"></a>Operazioni e avvisi

La sezione relativa **all'operazioni** e il **Redis Cache** è sezioni **eventi** e **le regole di avviso** .

![Oeprations][redis-cache-operations-events]

Per visualizzare un elenco di operazioni cache recenti, fare clic sul grafico **eventi** per visualizzare e **l'eventi** . Esempi di operazioni includono il recupero e rigenerazione tasti di scelta e l'attivazione e la risoluzione delle regole di avviso. Per ulteriori informazioni su ogni evento, fare clic su evento in calendario e **l'eventi** .

Per ulteriori informazioni sugli eventi, vedere [visualizzare gli eventi e registri di controllo](../monitoring-and-diagnostics/insights-debugging-with-events.md).

La sezione di **regole di avviso** Visualizza il numero di avvisi per l'istanza della cache. Una regola di avviso consente di monitorare l'istanza della cache e ricevere un messaggio di posta elettronica ogni volta che un determinato valore metrico raggiunge la soglia definita nella regola. 

Regole di avviso vengono valutate ogni cinque minuti, quindi quando una regola di avviso è attivata, vengono inviate le notifiche configurate. Le notifiche e attivazioni regola di avviso non vengono elaborate istantaneamente; è possibile che un ritardo di diversi minuti prima che venga attivata una regola di avviso e le notifiche ricevute.

Regole di avviso possono essere visualizzate e impostare e **l'unità di misura metriche** per un grafico di monitoraggio specifico oppure e il **regole di avviso** .

Regole di avviso sono le seguenti proprietà.

| Proprietà regola di avviso                 | Descrizione                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
|-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Risorsa                            | Risorsa valutata dalla regola di avviso. Quando si crea una regola di avviso da una cache Redis, la cache è la risorsa.                                                                                                                                                                                                                                                                                                                                                  |
| Nome                                | Nome che identifica la regola di avviso all'interno dell'istanza corrente di cache.                                                                                                                                                                                                                                                                                                                                                                                       |
| Descrizione                         | Descrizione della regola di avviso.                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Unità di misura metriche                              | Unità di misura metriche da monitorare dalla regola di avviso. Per un elenco di indicatori di cache, vedere segnalazione intervalli e metriche disponibili.                                                                                                                                                                                                                                                                                                                                             |
| Condizione                           | Operatore di condizione per la regola di avviso. Opzioni disponibili sono: maggiore, maggiore o uguale, minore, minore o uguale a                                                                                                                                                                                                                                                                                                                             |
| Soglia                           | Il valore utilizzato per eseguire il confronto con la metrica uso dell'operatore specificato dalla proprietà condizione. A seconda di unità di misura metriche, questo valore può essere byte/secondo byte, % o conteggio.                                                                                                                                                                                                                                                                                     |
| Periodo                              | Specifica il periodo in cui viene utilizzato il valore medio della metrica per il confronto di regola di avviso. Ad esempio, se il periodo si trova nell'ultima ora, il valore medio di unità di misura metriche nell'intervallo di ore precedente viene utilizzato per il confronto. Se si vuole ricevere una notifica quando la soglia verrà soddisfatti a causa di un picco nell'attività, un periodo più breve è appropriato. Per ricevere una notifica quando è sostenuto attività sopra la soglia, utilizzare un periodo più lungo. |
| Servizio di posta elettronica e coamministratori | Quando è true, l'amministratore di servizio e CO-vengono inviati tramite posta elettronica quando si attiva l'avviso.                                                                                                                                                                                                                                                                                                                                                                    |
| Messaggio di posta elettronica amministratore aggiuntive      | Indirizzo di posta elettronica facoltativo amministratore aggiuntivo per ricevere una notifica quando si attiva l'avviso.                                                                                                                                                                                                                                                                                                                                                                    |

Solo una notifica viene inviata per l'attivazione di regola di avviso. Dopo il superamento per una regola e viene inviata una notifica, la regola viene valutata nuovamente finché non la metrica non supera la soglia. Se l'unità di misura metriche successivamente supera la soglia, viene riattivata l'avviso e viene inviata una notifica di nuova.

Per visualizzare tutte le regole di avviso per l'istanza della cache, fare clic sulla parte **regole di avviso** e il **Redis Cache** . Per visualizzare solo le regole di avviso che usano una metrica specifica, passare a e **l'unità di misura metriche** per il grafico che contiene l'unità di misura metriche.

![Regole di avviso][redis-cache-alert-rules]

Per aggiungere una regola di avviso, fare clic su **Aggiungi avviso** e **l'unità di misura metriche** o e il **regole di avviso** . 

Immettere i criteri della regola desiderata e regola **l'Aggiungi un avviso** e fare clic su **OK**. 

![Aggiungere una regola di avviso][redis-cache-add-alert]

>[AZURE.NOTE] Quando viene creata una regola di avviso, fare clic su **Aggiungi avviso** e **l'unità di misura metriche** , solo le metriche visualizzate nel grafico in tale blade vengono visualizzati nell'elenco a discesa **unità di misura metriche** . Quando viene creata una regola di avviso, fare clic su **Aggiungi avviso** e il **regole di avviso** , tutte le metriche di cache sono disponibili nell'elenco a discesa **unità di misura metriche** .

Dopo aver salvata una regola di avviso verrà visualizzato in e il **regole di avviso** oltre che su e **l'unità di misura metriche** per tutti i grafici che visualizzano metrica utilizzata nella regola di avviso. Per modificare una regola di avviso, fare clic sul nome della regola di avviso per la visualizzazione e il **Modifica regola** . Da e il **Modifica regola** può modificare le proprietà della regola, eliminare o disattivare la regola di avviso o riattivare la regola se è stata disattivata in precedenza.

>[AZURE.NOTE] Le modifiche apportate alle proprietà della regola potrebbero richiedere qualche minuto prima che vengono applicati e **l'unità di misura metriche** o e il **regole di avviso** .

Quando una regola di avviso è attivata, viene inviato un messaggio di posta elettronica in base alla configurazione della regola di avviso e viene visualizzata un'icona di avviso in part **regole di avviso** e il **Redis Cache** .

Una regola di avviso è considerata essere risolti quando la condizione dell'avviso non viene valutata true. Dopo la condizione della regola avviso viene risolta, l'icona di avviso verrà sostituito con un segno di spunta. Per informazioni dettagliate su attivazioni degli avvisi e risoluzioni, fare clic sulla parte di **eventi** in e il **Redis Cache** per visualizzare gli eventi e **l'eventi** .

Per ulteriori informazioni sugli avvisi in Azure, vedere [ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

## <a name="metrics-on-the-redis-cache-blade"></a>Metriche nel e Redis Cache

E il **Redis Cache** consente di visualizzare le categorie seguenti di metriche.

-   [Grafici di monitoraggio](#monitoring-charts)
-   [Grafici di utilizzo](#usage-charts)

### <a name="monitoring-charts"></a>Grafici di monitoraggio

La sezione **monitoraggio** ha **visitate e perde** **viene impostato**, **le connessioni**e **Comandi totale** grafici.

![Grafici di monitoraggio][redis-cache-monitoring-part]

I tipi di grafici di **monitoraggio** visualizzare quelle riportate di seguito.

| Controllo grafico | Metriche di cache     |
|------------------|-------------------|
| Soddisfatte e non soddisfatte  | Accessi        |
|                  | Errori cache      |
| Ottiene e set    | Ottiene              |
|                  | Set              |
| Connessioni      | Client connessi |
| Comandi totali   | Operazioni totali  |

Per informazioni sulla visualizzazione le metriche e personalizzare i singoli grafici in questa sezione, vedere la sezione seguente [come visualizzare metriche e personalizzare i grafici metriche](#how-to-view-metrics-and-customize-charts) .

### <a name="usage-charts"></a>Grafici di utilizzo

La sezione **utilizzo** ha **Redis carico del Server**, **L'utilizzo di memoria**, **Larghezza di banda di rete**e **L'utilizzo della CPU** grafici e viene visualizzata anche il **livello di prezzo** per l'istanza della cache.

![Grafici di utilizzo][redis-cache-usage-part]

Visualizza il **livello prezzi** la cache dei prezzi a più livelli e possono essere usati in [scala](cache-how-to-scale.md) la cache a un altro livello prezzo.

I tipi di grafici di **utilizzo** visualizzare quelle riportate di seguito.

| Grafico di utilizzo       | Metriche di cache |
|-------------------|---------------|
| Redis carico del Server | Carico del server   |
| Utilizzo della memoria      | Memoria utilizzata   |
| Larghezza di banda di rete | Scrittura di cache   |
| Utilizzo della CPU         | CPU           |

Per informazioni sulla visualizzazione le metriche e personalizzare i singoli grafici in questa sezione, vedere la sezione seguente [come visualizzare metriche e personalizzare i grafici metriche](#how-to-view-metrics-and-customize-charts) .
  
<!-- IMAGES -->

[redis-cache-enable-diagnostics]: ./media/cache-how-to-monitor/redis-cache-enable-diagnostics.png

[redis-cache-diagnostic-settings]: ./media/cache-how-to-monitor/redis-cache-diagnostic-settings.png

[redis-cache-configure-diagnostics]: ./media/cache-how-to-monitor/redis-cache-configure-diagnostics.png

[redis-cache-monitoring-part]: ./media/cache-how-to-monitor/redis-cache-monitoring-part.png

[redis-cache-usage-part]: ./media/cache-how-to-monitor/redis-cache-usage-part.png

[redis-cache-metric-blade]: ./media/cache-how-to-monitor/redis-cache-metric-blade.png

[redis-cache-edit-chart]: ./media/cache-how-to-monitor/redis-cache-edit-chart.png

[redis-cache-view-chart-details]: ./media/cache-how-to-monitor/redis-cache-view-chart-details.png

[redis-cache-operations-events]: ./media/cache-how-to-monitor/redis-cache-operations-events.png

[redis-cache-alert-rules]: ./media/cache-how-to-monitor/redis-cache-alert-rules.png

[redis-cache-add-alert]: ./media/cache-how-to-monitor/redis-cache-add-alert.png

[redis-cache-premium-monitor]: ./media/cache-how-to-monitor/redis-cache-premium-monitor.png

[redis-cache-premium-edit]: ./media/cache-how-to-monitor/redis-cache-premium-edit.png

[redis-cache-premium-chart-detail]: ./media/cache-how-to-monitor/redis-cache-premium-chart-detail.png

[redis-cache-premium-point-summary]: ./media/cache-how-to-monitor/redis-cache-premium-point-summary.png

[redis-cache-premium-point-shard]: ./media/cache-how-to-monitor/redis-cache-premium-point-shard.png

[redis-cache-redis-metrics]: ./media/cache-how-to-monitor/redis-cache-redis-metrics.png

[redis-cache-redis-metrics-blade]: ./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png



