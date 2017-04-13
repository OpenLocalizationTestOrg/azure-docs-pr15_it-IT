<properties 
    pageTitle="Hub evento frequenti domande frequenti | Microsoft Azure"
    description="Hub evento domande frequenti."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/01/2016"
    ms.author="sethm" />

# <a name="event-hubs-faq"></a>Evento hub domande frequenti

Hub di evento fornisce assunzione di grandi dimensioni, persistenza ed elaborazione degli eventi di dati da origini dati ad alta velocità e/o milioni di dispositivi. Quando viene utilizzata con gli argomenti e code Bus di servizio, evento hub consente persistente distribuzioni di comando e controllo per gli scenari [Internet di elementi (IoT)](https://azure.microsoft.com/services/iot-hub/) .

In questo articolo vengono illustrate le informazioni sui prezzi e fornisce le risposte ad alcune domande frequenti sull'hub evento:

## <a name="pricing-information"></a>Informazioni sui prezzi

Per informazioni complete sui prezzi hub di evento, vedere [informazioni sui prezzi hub evento](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="how-are-event-hubs-ingress-events-calculated"></a>Come vengono calcolati i eventi di ingresso hub di evento

Ogni evento inviato a un Hub di evento viene conteggiato come un messaggio fatturabile. Un *evento di ingresso* è definito come un'unità di dati che sono minore o uguale a 64 KB. Qualsiasi evento che risulta maggiore o uguale a 64 KB di dimensioni viene considerato un evento fatturabile. Se l'evento è maggiore di 64 KB, il numero di eventi fatturabili viene calcolato secondo la dimensione dell'evento, in multipli di 64 KB. Ad esempio un evento di 8 KB inviato a Hub evento viene effettuato come un evento, ma un messaggio di 96 KB inviato a Hub evento viene effettuato come due eventi.

Gli eventi utilizzati da un Hub di evento, come nonché operazioni di gestione e controllano le chiamate, ad esempio punti di controllo, non vengono conteggiati come eventi di ingresso fatturabili ma attribuzione fino a ammortizzato di unità effettiva.

## <a name="what-are-event-hubs-throughput-units"></a>Quali sono le unità di velocità evento hub?

Selezionare in modo esplicito evento hub velocità unità tramite il portale di Azure o modelli di gestione delle risorse hub evento. Unità velocità applicare a tutti gli hub di evento in uno spazio dei nomi di evento hub e ogni unità effettiva dà lo spazio dei nomi per le funzionalità seguenti:

- Configurazione a 1 MB di ingresso eventi (inviato a un Hub di evento), ma non superiore a 1000 eventi di ingresso, le operazioni di gestione o controllo in un secondo API chiamate al secondo.

- Fino a 2 MB al secondo di uscita eventi (utilizzati da un Hub di evento).

- Fino a 84 GB di spazio di archiviazione di evento (sufficiente per il periodo di conservazione di 24 ore predefinito).

Unità di velocità hub evento vengono addebitate ogni ora, in base al numero massimo di unità selezionato durante l'ora specificato.

## <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Come vengono applicati i limiti di unità effettiva hub di evento

Se la velocità di ingresso totale o la velocità di evento in entrata totale su tutti gli hub di evento in uno spazio dei nomi supera consentito di unità effettiva aggregazione, mittenti saranno filtrati e visualizzati messaggi di errore che indica che è stata superata la quota di ingresso.

Se la velocità di uscita totale o il tasso di uscita evento totale su tutti gli hub di evento in uno spazio dei nomi supera consentito di unità effettiva aggregazione, ricevitori sono limitati e visualizzati messaggi di errore che indica che è stata superata la quota di uscita. Le quote di ingresso e in uscita vengono applicate separatamente, in modo che nessun mittente può causare il consumo di eventi a un rallentamento né possibile un ricevitore impedire degli eventi inviato a un Hub di evento.

Si noti che la selezione di unità effettiva indipendentemente dal numero di partizioni hub evento. Anche se ogni partizione offre una velocità massima di 1 MB per secondo ingresso (con un massimo di 1000 eventi al secondo) e 2 MB al secondo uscita, non esiste senza costi fissi per le partizioni stessi. Le spese sono per le unità di velocità aggregato in tutti gli hub di evento in uno spazio dei nomi hub evento. Con questo modello, è possibile creare partizioni sufficienti per supportare il carico massimo previsto per i sistemi, senza incorrere in base alle tariffe unità qualsiasi velocità fino a carico eventi sul sistema effettivamente richiede i valori più alti velocità e senza dover modificare la struttura e architettura dei sistemi come il caricamento del sistema aumenta.

## <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>È previsto un limite al numero di unità di velocità che possono essere selezionati?

Non c'è il limite predefinito di 20 unità di velocità per spazio dei nomi. È possibile richiedere una quota superiore di unità di velocità presentando un ticket di supporto. Oltre il limite di unità 20 velocità bundle sono disponibili in unità di velocità 20 e 100. Si noti che l'utilizzo di più di 20 unità effettiva rimuove la possibilità di modificare il numero di unità di velocità senza l'archiviazione di un ticket di supporto.

## <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Esiste un costo per conservare gli eventi di evento hub per più di 24 ore?

Il livello evento hub Standard consentire conservazione messaggio periodi più di 24 ore per un massimo di 30 giorni. Se le dimensioni del numero totale di eventi stored superano l'ammortizzato lo spazio di archiviazione per il numero di unità di velocità selezionato (84 GB unitario velocità), le dimensioni che supera l'ammortizzato viene calcolata il tasso di spazio di archiviazione Blob Azure pubblicato. Ammortizzato lo spazio di archiviazione in ogni unità di velocità copre tutti i costi di spazio di archiviazione per i periodi di conservazione di 24 ore (impostazione predefinita) anche se l'unità di velocità viene utilizzata ammortizzato ingresso massimo.

## <a name="what-is-the-maximum-retention-period"></a>Che cos'è il periodo di conservazione massimo?

Livello hub Standard evento supporta attualmente un periodo di conservazione massimo di 7 giorni. Si noti che gli hub di evento non sono destinati come archivio dati permanente. Periodi di memorizzazione superiore alle 24 ore sono indicate per scenari in cui è consigliabile la riproduzione di un flusso di eventi in sistemi stesso. ad esempio, per formare o verificare un nuovo modello di apprendimento machine sui dati esistenti.

## <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Come viene calcolata e addebitata la dimensione di archiviazione evento hub?

Le dimensioni totali di tutti gli eventi archiviate, inclusi il sovraccarico interno per le intestazioni di evento o strutture su disco lo spazio di archiviazione in tutti gli hub di evento, viene misurata nell'intera giornata. Alla fine della giornata, viene calcolata la dimensione di archiviazione punta. Il giorno ammortizzato lo spazio di archiviazione viene calcolato in base al numero minimo di unità di velocità selezionati durante il giorno (ogni unità velocità fornisce una tolleranza di 84 GB). Se la dimensione totale supera il calcolo ammortizzato lo spazio di archiviazione giornaliera, l'archiviazione in eccesso fatturazione con tassi di spazio di archiviazione Blob Azure (alla tariffa **Localmente lo spazio di archiviazione ridondanti** ).

## <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-event-hubs-and-service-bus-queuestopics"></a>È possibile usare una connessione AMQP per inviare e ricevere dall'hub di eventi e code Service Bus gli argomenti della?

Sì, come hub di eventi, code e gli argomenti sono stesso spazio dei nomi. Di conseguenza, è possibile implementare bidirezionale, gestita connettività a molti dispositivi con latenza subsecond, in modo conveniente e scalabilità.

## <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>I costi della connessione gestito si applicano a un evento hub?

Per i mittenti, i costi della connessione si applicano solo quando si utilizza il protocollo AMQP. Non esistono spese connessione per l'invio di eventi tramite HTTP, indipendentemente dal numero di invio sistemi o dispositivi. Se si prevede di utilizzare AMQP (ad esempio, per ottenere più efficiente il flusso di eventi o per consentire la comunicazione bidirezionale in scenari di controllo e comando IoT), fare riferimento alla pagina [informazioni sui prezzi Bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/) per informazioni su cosa si intende una connessione gestita e come si sono a consumo.

## <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Che cos'è la differenza tra i livelli Standard ed evento hub base?

Livello hub Standard evento offre funzionalità oltre a funzionalità disponibile in evento hub base e alcuni sistemi competitive. Queste funzionalità includono periodi di memorizzazione di più di 24 ore e la possibilità di usare una connessione AMQP per inviare comandi a un numero elevato di dispositivi con latenza subsecond, nonché di inviare telemetria da tali dispositivi hub evento. Per l'elenco di caratteristiche, vedere [informazioni sui prezzi hub evento](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="geographic-availability"></a>Disponibilità geografiche

Hub di evento è disponibile nelle aree seguenti:

|Geografico|Aree|
|---|---|
|Stati Uniti|Centrale USA, Stati Uniti orientali, Stati Uniti orientali 2, Sud centrale USA, US ovest|
|Europa|Europa Nord America, Europa occidentale|
|Area Asia Pacifico|Asia orientale, Asia sudorientale|
|Giappone|Giappone est, ovest Giappone|
|Brasile|Brasile sud|
|Australia|Australia orientale, Sudest Australia|

## <a name="support-and-sla"></a>Contratto di servizio e supporto

Supporto tecnico per evento hub è disponibile tramite il [forum della community](https://social.msdn.microsoft.com/forums/azure/home). Supporto di gestione di fatturazione e abbonamento disponibile senza costi.

Per ulteriori informazioni sul contratto di servizio, vedere la pagina [I contratti di servizio](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'hub di evento, vedere gli articoli seguenti:

- [Panoramica di hub di evento][].
- Un' [applicazione di esempio che utilizza evento hub][]completa.

[Cenni preliminari sui hub di eventi]: event-hubs-overview.md
[applicazione di esempio che utilizza hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
