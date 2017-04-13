<properties
    pageTitle="Analizzare le prestazioni del bordo in Azure CDN | Microsoft Azure"
    description="Analizzare le prestazioni del nodo di bordo in Microsoft Azure CDN. Bordo prestazioni Analitica Mostra l'utilizzo di traffico e la larghezza di banda granulare informazioni per la rete CDN."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analizzare le prestazioni del nodo di bordo in rete CDN di Microsoft Azure

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Panoramica
Analitica prestazioni bordo mostra l'utilizzo di traffico e la larghezza di banda granulare informazioni per la rete CDN. Queste informazioni possono quindi usate per generare statistiche tendenze, che consentono di ottenere informazioni su come le risorse vengono memorizzati nella cache e recapitato per i clienti. A sua volta, in modo da formare una strategia su come ottimizzare il recapito del contenuto e per determinare quali problemi dovrebbero essere affrontato al meglio la rete CDN. Di conseguenza, non solo è saranno in grado di migliorare le prestazioni di recapito di dati, ma sarà possibile per ridurre i costi CDN.

> [AZURE.NOTE] Tutti i report utilizzano la notazione UTC/GMT quando si specifica una data/ora.

## <a name="reports-and-log-collection"></a>Raccolta di log e report

Rete CDN attività dati devono essere raccolti dal modulo di bordo prestazioni Analitica prima può generare report su di esso. Questo processo di raccolta si verifica dopo un giorno che copre le attività che ha avuto luogo durante il giorno precedente. Ciò significa che le statistiche del report rappresentano un esempio delle statistiche del giorno al momento è stata elaborata e non necessariamente contenere il set completo di dati per il giorno corrente. La funzione principale di questi report consiste nel valutare le prestazioni. Non devono essere utilizzate per ai fini di fatturazione o statistiche numeriche esatte.

> [AZURE.NOTE] I dati non elaborati da cui vengono generate report bordo prestazioni analitico sono disponibili per almeno 90 giorni.

## <a name="dashboard"></a>Dashboard

Dashboard di bordo prestazioni Analitica tiene traccia il traffico di rete CDN corrente e nella cronologia tramite un grafico e statistiche. Utilizzare questo dashboard per individuare le tendenze recenti e a lungo termine sulle prestazioni del traffico di rete CDN per il proprio account.

Questo dashboard è costituito da:

* Un grafico interattivo che consente la visualizzazione di metriche chiave e le tendenze.
* Sequenza temporale che fornisce un'idea dei modelli a lungo termine per le tendenze e metriche chiave.
* Metriche chiave informazioni statistiche e su come la rete CDN migliora il traffico di sito misurati sulla base complessivo delle prestazioni, utilizzo e l'efficienza.

### <a name="accessing-the-edge-performance-dashboard"></a>Accesso ai dashboard di prestazioni di bordo

1. Scegliere il pulsante **Gestisci** e il profilo CDN.

    ![Pulsante Gestisci blade profilo CDN](./media/cdn-edge-performance/cdn-manage-btn.png)

    Portale di gestione di rete CDN apre.

2. Al passaggio del mouse sulla scheda **Analitica** , quindi al passaggio del mouse sopra il riquadro a comparsa **Bordo prestazioni Analitica** .  Fare clic su **Dashboard**.

    Viene visualizzato nel dashboard di bordo nodo analitica.

### <a name="chart"></a>Grafico

Il dashboard contiene un grafico che tiene traccia di una metrica nel periodo di tempo selezionato nella sequenza temporale visualizzato direttamente di sotto di esso.  Viene visualizzata una sequenza temporale grafici fino all'ultima due anni di attività CDN direttamente sotto il grafico.

#### <a name="using-the-chart"></a>Utilizzando il grafico

* Per impostazione predefinita, venga rappresentato i il tasso di rendimento cache per gli ultimi 30 giorni.
* Questo grafico viene generato dai dati fascicolati su base giornaliera.
* Passaggio del mouse su un giorno nel grafico a linee indicano una data e il valore della metrica a tale data.
* Fare clic su evidenziare i fine settimana per attivare o disattivare una sovrapposizione delle barre di verticale grigio chiaro che rappresentano i fine settimana nel grafico. Questo tipo di sovrapposizione è utile per l'identificazione di modelli di traffico in fine settimana.
* Fare clic su Visualizza un anno fa per attivare o disattivare una sovrapposizione della attività dell'anno precedente per lo stesso periodo di tempo nel grafico. Questo tipo di confronto fornisce approfondimenti modelli di utilizzo CDN a lungo termine. Nell'angolo superiore destro del grafico contiene una legenda che indica la codifica a colori per ogni grafico a linee.

#### <a name="updating-the-chart"></a>Aggiornare il grafico

* Intervallo di tempo: Effettuare una delle operazioni seguenti:
    * Selezionare il paese desiderato nella sequenza temporale. Il grafico verrà aggiornato con i dati che corrisponde al periodo di tempo selezionato.
    * Fare doppio clic sul grafico per visualizzare tutti i dati cronologici disponibili fino a un massimo di due anni.
* Unità di misura metriche: Fare clic sull'icona di grafico che viene visualizzata accanto a metrica desiderata. Il grafico e la sequenza temporale verrà aggiornati con i dati per la metrica corrispondente.


### <a name="key-metrics-and-statistics"></a>Statistiche e metriche chiave

#### <a name="efficiency-metrics"></a>Metrica l'efficienza

Lo scopo di queste metriche è per verificare se è possibile migliorare l'efficienza della cache. I principali vantaggi derivati l'efficienza della cache sono:

* Riduzione del carico dal server di origine che potrebbe rimandare a:
    * Prestazioni server web.
    * Ridurre i costi operativi.
* Migliorata accelerazione recapito dati dal momento che verranno servite più richieste direttamente dalla rete CDN.

Campo | Descrizione
------|------------
Efficienza della cache | Indica la percentuale di dati trasferiti inviata dalla cache. Questo misure metriche quando una versione memorizzata nella cache del contenuto richiesto stata installata direttamente dalla rete CDN (server perimetrali) richiedente (ad esempio, browser web)
Premere tasso | Indica la percentuale di richieste che sono state dalla cache. Questo misure metriche quando una versione memorizzata nella cache del contenuto richiesto è stata fornita direttamente dalla rete CDN (server perimetrali) a richiedente (ad esempio, browser web).
% di byte Remote - Nessuna configurazione della Cache | Indica la percentuale di traffico inviata dal server di origine per la rete CDN (server perimetrali) che non verrà memorizzata in seguito la funzionalità di Cache Bypass (motore di regole HTTP).
% di byte Remote - della Cache scaduti | Indica la percentuale di traffico inviata dal server di origine per la rete CDN (server perimetrali) come risultato rinnovo contenuto non aggiornato.

#### <a name="usage-metrics"></a>Metriche di utilizzo

Lo scopo di queste metriche è consentono di comprendere le misure di riduzione dei costi seguenti:

* Ridurre i costi operativi tramite la rete CDN.
* Riduzione delle spese CDN tramite l'efficienza della cache e la compressione.

> [AZURE.NOTE] Il traffico volume numeri rappresentano il traffico che è stato usato nei calcoli di rapporti e percentuali e potrebbe visualizzare solo una parte del traffico totale per i clienti di volumi elevati.

Campo | Descrizione
------|------------
Media byte in uscita | Indica il numero medio di byte trasferiti ogni richiesta sia servita dalla rete CDN (server perimetrali) al richiedente (ad esempio, browser web).
Nessun tasso Byte di configurazione della Cache | Indica la percentuale di traffico servita dalla rete CDN (server perimetrali) al richiedente (ad esempio, browser web) che non verrà memorizzato a causa di funzionalità della Cache di esclusione.
Tasso di Byte compresso | Indica la percentuale di traffico dalla rete CDN (server perimetrali) a richiedente (ad esempio, browser web) in un formato compresso.
Byte in uscita | Indica la quantità di dati, in byte, che sono stati recapitati da CDN (server perimetrali) al richiedente (ad esempio, browser web).  
Byte | Indica la quantità di dati, in byte, inviata dal richiedente (ad esempio, browser web) per la rete CDN (server perimetrali).
Byte remota | Indica la quantità di dati, in byte, inviate dal server di origine CDN e cliente della rete CDN (server perimetrali).

#### <a name="performance-metrics"></a>Dati sulle prestazioni

Lo scopo di queste metriche è per tenere traccia delle prestazioni di rete CDN per il traffico.

Campo | Descrizione
------|------------
Velocità di trasferimento | Indica la velocità media in cui è stato trasferito dalla rete CDN contenuto a un richiedente.
Durata | Indica il tempo medio in millisecondi, impiegato per recapitare un bene per un richiedente (ad esempio, browser web).
Frequenza richieste compresso | Indica la percentuale di visite che sono stati recapitati da CDN (server perimetrali) al richiedente (ad esempio, browser web) in un formato compresso.
Tasso di errori 4xx | Indica la percentuale di visite che ha generato un codice di stato 4xx.
Tasso di errore 5xx | Indica la percentuale di visite che ha generato un codice di stato 5xx.
Visite | Indica il numero di richieste per il contenuto CDN.

#### <a name="secure-traffic-metrics"></a>Proteggere il traffico metriche

Lo scopo di queste metriche è a tenere traccia delle prestazioni di rete CDN per il traffico HTTPS.

Campo | Descrizione
------|------------
Efficienza Proteggi la Cache | Indica la percentuale di dati trasferiti per le richieste HTTPS che sono state soddisfatte dalla cache. Questo misure metriche quando una versione memorizzata nella cache del contenuto richiesto è stata fornita direttamente dalla rete CDN (server perimetrali) a richiedente (ad esempio, browser web) su HTTPS.
Tasso di trasferimento sicuro | Indica la velocità media cui contenuto è stato trasferito dalla rete CDN (server perimetrali) richiedente (ad esempio, il server web) su HTTPS.
Durata sicura Media | Indica il tempo medio in millisecondi, impiegato per recapitare un bene per un richiedente (ad esempio, browser web) su HTTPS.
Proteggere accessi | Indica il numero di richieste HTTPS per il contenuto CDN.
Proteggere byte in uscita | Indica la quantità di traffico HTTPS, in byte, che sono stati recapitati da CDN (server perimetrali) al richiedente (ad esempio, browser web).

## <a name="reports"></a>Report

Ogni rapporto in questo modulo contiene un grafico e statistiche per l'utilizzo della larghezza di banda e il traffico per diversi tipi di metriche (ad esempio codici di stato HTTP, codici di stato della cache, richiesta URL, ecc.). Queste informazioni possono essere utilizzate per approfondita resi come contenuto per i clienti e per ottimizzare il comportamento di rete CDN per migliorare le prestazioni di recapito di dati.

### <a name="accessing-the-edge-performance-reports"></a>Accesso ai rapporti di prestazioni di bordo

1. Scegliere il pulsante **Gestisci** e il profilo CDN.

    ![Pulsante Gestisci blade profilo CDN](./media/cdn-edge-performance/cdn-manage-btn.png)

    Portale di gestione di rete CDN apre.

2. Al passaggio del mouse sulla scheda **Analitica** , quindi al passaggio del mouse sopra il riquadro a comparsa **Bordo prestazioni Analitica** .  Fare clic su **oggetto di grandi dimensioni HTTP**.

    Viene visualizzata la schermata di report di bordo nodo analitica.

Report | Descrizione
-------|------------
Riepilogo giornaliero | Consente di visualizzare le tendenze del traffico giornaliera in un periodo di tempo specificato. Il grafico ogni barra rappresenta una data specifica. La dimensione della barra indica il numero totale dei risultati che si sono verificati in tale data.
Riepilogo oraria | Consente di visualizzare le tendenze del traffico ogni ora in un periodo di tempo specificato. Ogni barra il grafico rappresenta le singole ore in una data specifica. La dimensione della barra indica il numero totale dei risultati che si sono verificati in quell'ora.
Protocolli | Consente di visualizzare la suddivisione del traffico tra i protocolli HTTP e HTTPS. Grafico ad anello indica la percentuale di visite che si sono verificati per ogni tipo di protocollo.
Metodi HTTP | Consente di una panoramica delle quali HTTP metodi vengono utilizzati per richiedere i dati. In genere, i metodi di richiesta HTTP più comuni sono GET, testa e POST. Grafico ad anello indica la percentuale di visite che si sono verificati per ogni tipo di metodo della richiesta HTTP.
URL | Contiene un grafico che mostra le prime 10 URL richiesto. Viene visualizzata una barra per ogni URL. L'altezza della barra indica quante risultati generati URL specifico nel periodo di tempo coperto dal report. Statistiche per i primi 100 richiesta che gli URL vengano visualizzati direttamente sotto il grafico.
Record CNAME | Contiene un grafico che mostra nella parte superiore estesi a 10 record CNAME consente di richiedere risorse per il tempo di un report. Statistiche per i primi 100 richiesto che CNAME vengono visualizzati sotto questo grafico.
Origini diverse | Contiene un grafico che mostra la rete CDN 10 superiore o il server di origine cliente da cui risorse sono stati richiesti in un determinato periodo di tempo. Statistiche per i primi 100 richiesta CDN o cliente server di origine vengono visualizzati sotto questo grafico. Server di origine cliente vengono identificati dal nome definito nella casella nome della Directory.
Geografico POP | Mostra la quantità del traffico viene instradata una determinato punto di-presenza (POP). Abbreviazione di tre lettere rappresenta un POP rete CDN.
Client | Contiene un grafico che mostra i client di 10 principali richieste risorse su un periodo di tempo specificato. Per quanto riguarda il report, tutte le richieste provenienti dallo stesso indirizzo IP sono considerate dallo stesso client. Statistiche di 100 clienti principali vengono visualizzate direttamente sotto il grafico. Questo report è utile per determinare i modelli di attività di download per i client superiore.
Stati cache | Fornisce una descrizione dettagliata del comportamento della cache, che può visualizzare approcci per migliorare l'esperienza utente finale complessiva. Poiché le prestazioni migliori provengono da accessi, è possibile ottimizzare velocità di recapito dei dati, la riduzione a icona errori cache e accessi scaduto.
Nessuno dei dettagli | Contiene un grafico che mostra i 10 URL superiore per risorse per il quale validità del contenuto della cache non è stato archiviato in un determinato periodo di tempo. Statistiche per gli 100 URL superiore per questi tipi di risorse vengono visualizzate direttamente sotto il grafico.
Dettagli CONFIG_NOCACHE | Contiene un grafico che mostra gli URL primi 10 per risorse non memorizzati nella cache a causa di configurazione della rete CDN del cliente. Questi tipi di risorse sono state soddisfatte direttamente dal server di origine. Statistiche per gli 100 URL superiore per questi tipi di risorse vengono visualizzate direttamente sotto il grafico.
Dettagli UNCACHEABLE | Contiene un grafico che mostra gli 10 URL principali per risorse che non può essere memorizzati nella cache a causa di dati di richiesta dell'intestazione. Statistiche per gli 100 URL superiore per questi tipi di risorse vengono visualizzate direttamente sotto il grafico.
Dettagli TCP_HIT | Contiene un grafico che mostra i 10 URL superiore per risorse che vengono immediatamente dalla cache. Statistiche per gli 100 URL superiore per questi tipi di risorse vengono visualizzate direttamente sotto il grafico.
Dettagli TCP_MISS | Contiene un grafico che mostra gli 10 URL principali per risorse che lo stato della cache di TCP_MISS. Statistiche per gli 100 URL superiore per questi tipi di risorse vengono visualizzate direttamente sotto il grafico.
Dettagli TCP_EXPIRED_HIT | Contiene un grafico che mostra gli 10 URL superiore per le risorse non aggiornati inviata direttamente da POP. Statistiche per gli 100 URL superiore per questi tipi di risorse vengono visualizzate direttamente sotto il grafico.
Dettagli TCP_EXPIRED_MISS | Contiene un grafico che mostra i 10 URL superiore per le risorse non aggiornati per cui una nuova versione doveva essere recuperati dal server di origine. Statistiche per gli 100 URL superiore per questi tipi di risorse vengono visualizzate direttamente sotto il grafico.
Dettagli TCP_CLIENT_REFRESH_MISS | Contiene un grafico a barre che visualizza gli URL primi 10 per risorse recuperati da un server di origine a causa di una richiesta di cache no dal client. Statistiche per gli 100 URL superiore per questi tipi di richieste vengono visualizzate direttamente sotto il grafico.
Tipi di client per richiesta | Indica il tipo di richieste effettuate da client HTTP (ad esempio, i browser). Questo report include un grafico ad anello che fornisce un'idea da come vengono gestite richieste. Visualizzazione delle informazioni della larghezza di banda e il traffico per ogni tipo di richiesta sotto il grafico.
Agente utente | Contiene un grafico a barre visualizzati gli agenti 10 utente principali per richiedere al contenuto attraverso il nostro CDN. In genere, un agente utente è un web browser, media player o un browser del telefono cellulare. Statistiche per gli agenti 100 utente superiore vengono visualizzate direttamente sotto il grafico.
Riferimenti | Contiene un grafico a barre visualizzazione principali 10 riferimenti per il contenuto tramito il nostro CDN. Un riferimento è in genere, l'URL della pagina web o della risorsa che collega al contenuto. Vengono fornite informazioni dettagliate sotto il grafico per riferimenti primi 100.
Tipi di compressione | Contiene un grafico ad anello che suddivide risorse richieste, se sono stati compressi per i server perimetrali. La percentuale di risorse compressi è ripartita in base al tipo di compressione utilizzato. Sotto il grafico vengono fornite informazioni dettagliate per ogni tipo di compressione e stato.
Tipi di file | Contiene un grafico a barre che visualizza i tipi di 10 file principali richiesti tramite la rete CDN per il proprio account. Per quanto riguarda il report, un tipo di file dipende dall'estensione del bene e tipo di elemento multimediale Internet (ad esempio,. HTML \[testo/html\],. htm \[testo/html\], aspx \[testo/html\], ecc.). Sotto il grafico vengono fornite informazioni dettagliate per i tipi di 100 file superiore.
File univoci | Contiene un grafico che traccia il numero totale di risorse univoci che sono stati richiesti in un giorno specifico in un determinato periodo di tempo.
Riepilogo Auth token | Contiene un grafico a torta che fornisce una breve panoramica su se risorse richieste sono protetti tramite l'autenticazione basata su Token. Elementi protetti vengono visualizzati nel grafico in base ai risultati del loro autenticazione tentativo.
Token Auth negare dettagli | Contiene un grafico a barre che consente di visualizzare le richieste di 10 principali che non hanno ottenute a causa di autenticazione basata su Token.
Codici di risposta HTTP | Fornisce un riepilogo dei codici di stato HTTP (ad esempio, 200 OK, 403 accesso negato, 404 non viene trovato, ecc) che sono stati recapitati ai client HTTP tramite il server perimetrali. Un grafico a torta consente di valutare rapidamente come sono state soddisfatte le risorse. Dati statistici dettagliati sono disponibili per ogni codice di risposta sotto il grafico.
404 errori | Contiene un grafico a barre che consente di visualizzare le richieste di 10 principali che hanno generato un codice di risposta 404 non trovato.
403 errori | Contiene un grafico a barre che consente di visualizzare le richieste di 10 principali che hanno generato un codice di risposta 403 accesso negato. Un codice di risposta 403 accesso negato si verifica quando una richiesta negata da un server di origine dei clienti o un edge server nel nostro POP.
4xx errori | Contiene un grafico a barre che consente di visualizzare le richieste di 10 principali che hanno generato un codice di risposta dell'intervallo 400. 403 sono esclusi da questo report non viene trovato e 404 codici di risposta non consentito. In genere, un codice di risposta 4xx si verifica quando una richiesta negata in seguito a un errore di client.
504 errori | Contiene un grafico a barre che consente di visualizzare le richieste di 10 principali che hanno generato un codice di risposta Timeout Gateway 504. Un codice di risposta Timeout Gateway 504 si verifica quando si verifica un timeout durante un proxy HTTP comunicare con un altro server. Per quanto riguarda il nostro CDN, un codice di risposta Timeout Gateway 504 in genere si verifica quando un edge server non è possibile stabilire la comunicazione con un server di origine del cliente.
502 errori | Contiene un grafico a barre che consente di visualizzare le richieste di 10 principali che hanno generato un codice di risposta 502 Gateway non valido. Un codice di risposta 502 Gateway non valido quando si verifica un errore di protocollo HTTP tra un server e un proxy HTTP. Per quanto riguarda il nostro CDN, un codice di risposta 502 Gateway non valido in genere si verifica quando un server di origine cliente restituisce una risposta non valida per un edge server. Una risposta non è valida se non è stato analizzare o se è incompleto.
5xx errori | Contiene un grafico a barre che consente di visualizzare le richieste di 10 principali che hanno generato un codice di risposta dell'intervallo 500.  Esclusi da questo report sono 502 Gateway non valido e 504 codici di risposta Timeout Gateway.

## <a name="see-also"></a>Vedere anche
* [Panoramica di Azure CDN](cdn-overview.md)
* [Statistiche in tempo reale in rete CDN di Microsoft Azure](cdn-real-time-stats.md)
* [Override di comportamento HTTP predefinito tramite il motore di regole](cdn-rules-engine.md)
* [Report HTTP avanzate](cdn-advanced-http-reports.md)
