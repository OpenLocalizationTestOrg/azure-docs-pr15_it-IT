<properties
    pageTitle="Monitoraggio app nel servizio App Azure"
    description="Informazioni su come eseguire il monitoraggio delle applicazioni di servizio App Azure tramite il portale di Azure."
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="byvinyal"/>

# <a name="how-to-monitor-apps-in-azure-app-service"></a>Procedura: monitorare le app nel servizio App Azure

[Servizio di App](http://go.microsoft.com/fwlink/?LinkId=529714) offre funzionalità di monitoraggio incorporata nel [Portale di Azure](https://portal.azure.com).
Consente di esaminare **le quote** e **metriche** per un'app, nonché il piano di servizio di App, sulla configurazione **degli avvisi** e anche **il ridimensionamento** automaticamente in base a queste metriche.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>Informazioni sulle quote e metriche

### <a name="quotas"></a>Quote

Le applicazioni contenute nel servizio di App sono soggetti a determinati *limiti* per le risorse che possono utilizzare. I limiti sono definiti dal **piano di servizio App** associato con l'app.

Se l'applicazione è ospitata in un piano **gratuito** o **condiviso** , i limiti per le risorse che possa utilizzare l'app sono definiti dalle **quote**.

Se l'applicazione è ospitata in una **base**, **Standard** o **Premium** piano e quindi i limiti nelle risorse che possono utilizzare impostazione il **dimensioni** (piccola, Media, grande) e il **conteggio delle istanze** (1, 2, 3,...) del **piano di servizio di App**.

Di seguito sono riportate **le quote** per app **gratuito** o **condiviso** :

* **CPU(short)**
   * Quantità di CPU consentita per questa applicazione in un intervallo di 3 minuti. La quota imposta nuovamente ogni 3 minuti.
* **CPU(Day)**
   * Importo totale della CPU consentita per questa applicazione in un giorno. La quota imposta nuovamente ogni 24 ore mezzanotte ora UTC.
* **Memoria**
   * Importo totale di memoria consentita per questa applicazione.
* **Larghezza di banda**
   * Importo totale della larghezza di banda in uscita consentito per questa applicazione in un giorno.
   La quota imposta nuovamente ogni 24 ore mezzanotte ora UTC.
* **File System**
   * Importo totale di spazio di archiviazione consentito.

La quota sola applicabile alle App ospitati in **base** **Standard** e **Premium** piani è **file System**.

Ulteriori informazioni sulle funzionalità disponibili per i diversi SKU di servizio di App e le quote specifiche, limiti disponibili qui: [Limiti del servizio di abbonamento Azure](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>Imposizione di quota

Se un'applicazione del relativo uso supera la **CPU (forma breve)**, verrà interrotti **CPU (giorno)**o quota **della larghezza di banda** quindi l'applicazione fino a quando la quota imposta nuovamente. Durante questo periodo, tutte le richieste in arrivo verranno generato un **HTTP 403**.
![][http403]

In caso di superamento della quota di **memoria** applicazione, l'applicazione sarà riavviato.

Se viene superata la quota di **file System** , qualsiasi scrittura operazione avrà esito negativo, inclusi la scrittura dei log.

Le quote di possono essere maggiore o rimosso dall'applicazione aggiornando il piano di servizio di App.

### <a name="metrics"></a>Metrica

**Metriche** fornite informazioni sulle app o il comportamento del piano di servizio di App.

Per un' **applicazione**, la disponibili sono:

* **Tempo medio di risposta**
   * Tempo medio per l'app elaborare le richieste in millisecondi.
* **Media della memoria insieme di lavoro**
   * Importo medio di memoria in MIB utilizzato dall'applicazione.
* **Tempo CPU**
   * La quantità di CPU in secondi consumata dall'app. Per ulteriori informazioni su questo vedere metrica: [percentuale di CPU ora vs CPU](#cpu-time-vs-cpu-percentage)
* **Dati In**
   * La quantità di posta in arrivo della larghezza di banda consumata dall'app in MIB.
* **Dati indietro**
   * La quantità di posta in uscita della larghezza di banda consumata dall'app in MIB.
* **2xx http**
   * Numero di richieste risultante in un codice di stato http > = 200 ma < 300.
* **3xx http**
   * Numero di richieste risultante in un codice di stato http > = 300 ma < 400.
* **HTTP 401**
   * Numero di richieste risultante nel codice di stato 401 HTTP.
* **HTTP 403**
   * Numero di richieste risultante nel codice di stato HTTP 403.
* **HTTP 404**
   * Numero di richieste risultante nel codice di stato HTTP 404.
* **HTTP 406**
   * Numero di richieste risultante nel codice di stato HTTP 406.
* **4xx http**
   * Numero di richieste risultante in un codice di stato http > = 400 ma < 500.
* **Errori di Server http**
   * Numero di richieste risultante in un codice di stato http > = 500 ma < 600.
* **Working set della memoria**
   * Quantità corrente di memoria utilizzata dall'applicazione in MIB.
* **Richieste di**
   * Numero totale di richieste indipendentemente dalla loro codice di stato HTTP risultante.

Per un **piano di servizio App**metriche disponibili sono:

>[AZURE.NOTE] Metriche di piano di servizio di App disponibili solo per i piani di **base**, **Standard** e **Premium** SKU.

* **Percentuale di CPU**
   * CPU Media utilizzata in tutte le istanze del piano.
* **Percentuale di memoria**
   * Memoria Media utilizzata in tutte le istanze del piano.
* **Dati In**
   * La media in arrivo larghezza di banda utilizzata in tutte le istanze del piano.
* **Dati indietro**
   * Media in uscita larghezza di banda utilizzata in tutte le istanze del piano.
* **Coda del disco**
   * Numero medio di sia leggere e scrittura le richieste accodate sullo spazio di archiviazione. Una coda del disco elevato è un'indicazione di un'applicazione che potrebbe rallentare a causa di un numero eccessivo disco.
* **Lunghezza coda http**
   * Numero medio di richieste HTTP di occupare nella coda prima sono soddisfatte. Un alto o in aumento HTTP coda è un indicatore di un piano in condizioni di carico elevato.

### <a name="cpu-time-vs-cpu-percentage"></a>Percentuale di CPU ora vs CPU
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Esistono 2 metriche che riflettono l'utilizzo della CPU. **Tempo CPU** e la **percentuale di CPU**

**Tempo CPU** è utile per le applicazioni ospitate nei piani **gratuito** o **Shared** poiché uno dei loro quote è definito in minuti CPU utilizzati dall'applicazione.

**Percentuale di CPU** invece è utile per le applicazioni ospitate nei piani di **base**, **standard** e **premium** dal momento che possono essere scalabilità e questa metrica è molto probabile dell'utilizzo complessivo in tutte le istanze.

##<a name="metrics-granularity-and-retention-policy"></a>Metrica granularità e criteri di conservazione

Metrica per un'applicazione e un piano di servizio app è connessi e aggregata dal servizio con i criteri di conservazione e granularità seguenti:

 * Metrica granularità **minuto** vengono mantenuta per **48 ore**
 * Metrica granularità **ora** vengono mantenuta per **30 giorni**
 * Metrica granularità **giorno** vengono mantenuta per **90 giorni**

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Monitorare le quote e metriche nel portale di Azure.

È possibile esaminare lo stato delle diverse **quote** **metriche** che interessano un'applicazione nel [Portale di Azure](https://portal.azure.com).

![][quotas]
**Le quote** sono disponibili in Impostazioni >**le quote**. Dell'esperienza utente consente di esaminare: (1) il nome di quote (2) l'intervallo di reimpostazione, (3) il limite corrente e valore (4) corrente.

![][metrics]
**Metrica** si può accedere direttamente da e delle risorse. È inoltre possibile personalizzare il grafico: (1) **fare clic** su e selezionare (2) **Modifica grafico**.
Da qui è possibile modificare l' **intervallo di tempo**(3), (4) **tipo di grafico**e (5) **metriche** da visualizzare.  

Altre informazioni sulle metriche qui: [Metrica servizio Monitor](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Avvisi e Autoscale
Metriche per un piano di servizio App o App può essere collegato agli avvisi, per ulteriori informazioni, vedere [ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

Le applicazioni di servizio App ospitate in base, standard o premium App piani di servizio di supporto **autoscale**. In questo modo è possibile configurare le regole per monitorare le metriche di piano di servizio di App e possono aumentare o diminuire il conteggio delle istanze fornire ulteriori risorse in base alle esigenze o salvataggio denaro quando l'applicazione è provisioning in eccesso. È possibile acquisire familiarità con ridimensionamento automatico qui: [come scala](../monitoring-and-diagnostics/insights-how-to-scale.md) e qui [procedure consigliate per il ridimensionamento automatico Monitor Azure](../monitoring-and-diagnostics/insights-autoscale-best-practices.md)

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="whats-changed"></a>Novità
* Per una Guida per la modifica da siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sulla esistente servizi di Windows Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
