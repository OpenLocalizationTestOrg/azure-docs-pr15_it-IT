<properties 
    pageTitle="Servizio Bus prezzi e fatturazione | Microsoft Azure"
    description="Panoramica del servizio Bus struttura dei prezzi."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/06/2016"
    ms.author="sethm" />

# <a name="service-bus-pricing-and-billing"></a>Servizio Bus prezzi e fatturazione

Bus di servizio è disponibile in livelli Basic, Standard e [Premium](service-bus-premium-messaging.md) . È possibile scegliere un livello di servizio per ogni spazio dei nomi del servizio Bus di servizio che si crea e la selezione di livello applicate a tutte le entità create nello spazio dei nomi.

>[AZURE.NOTE] Per informazioni dettagliate sui prezzi Bus di servizio corrente, vedere [Bus di servizio Azure prezzi pagina](https://azure.microsoft.com/pricing/details/service-bus/)e le [Domande frequenti su Bus di servizio](service-bus-faq.md#service-bus-pricing).

Bus di servizio utilizza i seguenti controlli due code di e argomenti/abbonamenti:

1. **Attività di messaggistica**: definito come chiamate API in coda o l'argomento/abbonamento endpoint del servizio. Questo contatore sostituiranno messaggi inviati o ricevuti come l'unità di utilizzo fatturabile per code e argomenti/abbonamenti principale.

2. **Connessioni negoziate**: definito come il numero massimo di connessioni permanenti aprirle contro code, argomenti o sottoscrizioni durante un periodo specificato campioni di un'ora. Questo contatore verrà applicata solo al livello Standard, in cui è possibile aprire ulteriori connessioni (in precedenza, le connessioni sono state limitate a 100 KB per coda/argomento/abbonamento) di una tariffa nominale connessione.

Il livello **Standard** introduce tarato prezzi per le operazioni eseguite con coda e argomenti/abbonamenti risultante basate su volume sconti fino a 80% in più alti livelli di utilizzo. È inoltre disponibile un costo di base di livello Standard pari a €10 al mese, che consente di eseguire operazioni fino a 12,5 milioni al mese senza costi aggiuntivi.

Il livello di **Premium** fornisce isolamento di risorse al livello di CPU e memoria in modo che il carico di lavoro ogni cliente viene eseguito in isolamento. In questo contenitore di risorse è chiamato un *messaggistica unità*. Ogni spazio dei nomi premium allocata almeno un'unità SMS. È possibile acquistare 1, 2 o 4 unità di messaggistica per ogni servizio Bus Premium spazio dei nomi. Una singola carico di lavoro o un'entità può occupare più unità di messaggistica e il numero di unità messaggistica può essere modificato piacimento, sebbene la fatturazione in base alle tariffe tariffa giornaliera o 24 ore. Il risultato è prevedibili prestazioni per le soluzioni basate su Bus di servizio. Non solo è questa prestazioni più prevedibili e disponibile, ma è veloce. Messaggistica servizio Bus Premium Azure si basa sul motore di archiviazione introdotto in Azure evento hub. Grazie alla messaggistica Premium, prestazioni ottimali sono superiore rispetto a livello Standard.

Si noti che le spese di base standard viene addebitata solo una volta al mese per abbonamento Azure. Questo significa che dopo aver creato un unico Standard o Premium livello servizio Bus spazio dei nomi, sarà possibile creare tanti Standard o Premium livello spazi dei nomi aggiuntivi che si vuole in tale stesso abbonamento Azure, senza incorrere aggiuntive in base alle tariffe base.

Tutti Bus di servizio spazi dei nomi esistenti create con versioni precedenti di 1 novembre 2014 automaticamente inseriti in livello Standard. In questo modo si continuare ad accedere a tutte le funzionalità disponibili con Bus di servizio. Successivamente, è possibile utilizzare il [portale classica Azure][] per eseguire il downgrade a livello di base se lo si desidera.

Nella tabella seguente sono riepilogate le differenze funzionale tra i livelli Basic e Standard/Premium.

|Funzionalità|Base|Standard/Premium|
|---|---|---|
|Code|Sì|Sì|
|Messaggi pianificati|Sì|Sì|
|Gli argomenti/abbonamenti|No|Sì|
|Inoltro|No|Sì|
|Transazioni|No|Sì|
|La duplicazione|No|Sì|
|Sessioni|No|Sì|
|Messaggi di grandi dimensioni|No|Sì|
|ForwardTo|No|Sì|
|SendVia|No|Sì|
|Connessioni gestite (incluse)|100 KB per servizio Bus dello spazio dei nomi|1.000 per abbonamento Azure|
|Connessioni gestite (eccesso di dosaggio consentito)|No|Sì (fatturabile)|

## <a name="messaging-operations"></a>Operazioni di SMS

Come parte del nuovo modello di prezzo, fatturazione per argomenti/abbonamenti e code in fase di modifica. Queste entità stanno passando da fatturazione per ogni messaggio per la fatturazione per operazione. "Operazione" fa riferimento a una chiamata API effettuata un endpoint del servizio di coda o l'argomento/abbonamento. Sono incluse operazioni di stato di gestione di invio/ricezione e sessione.

|Tipo di operazione|Descrizione|
|---|---|
|Gestione|Creazione, lettura, aggiornamento, eliminazione (CRUD) contro code o argomenti/abbonamenti.|
|Messaggistica|Inviare e ricevere messaggi con code o argomenti/abbonamenti.|
|Stato della sessione|Ottenere o impostare lo stato della sessione in una coda o argomento/sottoscrizione.|

I prezzi seguenti sono efficienti partire 1 novembre 2014:

|Base|Costo|
|---|---|
|Operazioni|$0.05 per milioni operazioni|

|Standard|Costo|
|---|---|
|Addebito base|$10/ mese|
|Prima di tutto 12,5 milioni operazioni/mese|Incluso|
|12.5-100 milioni operazioni/mese|$0,80 per milioni operazioni|
|operazioni di 100 milioni - 2500 milioni/mese|$0,50 per milioni operazioni|
|Oltre 2500 milioni operazioni/mese|0,20 dollari per milioni operazioni|

|Premium|Costo|
|---|---|
|Giorno|$11.13 tasso unitario messaggio fisso|

## <a name="brokered-connections"></a>Connessioni gestite

*Connessioni Brokered* soddisfare i criteri di utilizzo dei clienti che includono un numero elevato di "in modo permanente connessi" mittenti/ricevitori contro code, argomenti o abbonamenti. In modo permanente connessi mittenti/destinatari sono quelli che si connettono con un diverso da zero AMQP o HTTP riceve un timeout (ad esempio HTTP lungo polling). HTTP mittenti e destinatari con un timeout immediato non generano connessioni gestite.

Gli argomenti/abbonamenti e code era un limite di 100 connessioni simultanee per URL. Combinazione di fatturazione corrente rimuove il limite per URL per code e argomenti/sottoscrizioni e implementazione le quote e misurazione sulle connessioni gestite al spazio dei nomi Bus di servizio e livelli di abbonamento Azure.

Il livello di base include ed è limitato ai 100 connessioni gestite per ogni servizio Bus dello spazio dei nomi. Connessioni sopra il numero verranno rifiutate nel livello di base. Il livello Standard rimuove il limite per nomi e conta l'uso di aggregazione connessione gestito attraverso l'abbonamento Azure. Nel livello Standard 1.000 connessioni gestite per abbonamento Azure potrà essere senza costi (oltre le spese di base) aggiuntivi. Per più di un totale di 1.000 connessioni gestite attraverso Bus di servizio di livello Standard spazi dei nomi in un abbonamento a Azure verrà fatturato una frequenza tarato, come illustrato nella tabella seguente.

|Connessioni gestite (livello Standard)|Costo|
|---|---|
|Prima di 1.000/mese|Incluso con costi di base|
|1.000-100.000/mese|$ pari a 0,03 connessione/mensile|
|100.000-500.000/mese|$0.025 connessione/mensile|
|Per 500.000/mese|$0.015 connessione/mensile|

>[AZURE.NOTE] 1.000 connessioni gestite sono incluse con il livello di messaggistica Standard (tramite le spese di base) e possono essere condiviso tra tutte le code, argomenti e le sottoscrizioni all'interno della sottoscrizione di Azure associata.

<br />

>[AZURE.NOTE] Fatturazione basata sul numero massimo di connessioni simultanee ed è impostata su in quote ogni ora in base a ore 744 al mese.

|Livello Premium
|---|
|Connessioni gestite non vengono addebitate nel livello Premium.|

Per ulteriori informazioni sulle connessioni gestite, vedere la sezione [domande frequenti](#faq) più avanti in questo argomento.

## <a name="relay"></a>Inoltro

Inoltro è disponibili solo in spazi dei nomi di livello Standard. In caso contrario, le quote di prezzi e di una connessione di inoltro rimangono invariate. Questo significa che inoltro continuerà a essere applicata al numero di messaggi (non operazioni) e inoltrare ore.

|Inoltrare i prezzi|Costo|
|---|---|
|Inoltro ore|0,10 dollari per ogni ore 100 inoltro|
|Messaggi|$0,01 per ogni 10.000 messaggi|

## <a name="faq"></a>Domande frequenti

### <a name="how-is-the-relay-hours-meter-calculated"></a>Come viene calcolata l'indicatore di inoltro ore?

Consultare [questo argomento](service-bus-faq.md#how-is-the-relay-hours-meter-calculated).

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>Quali sono negoziate connessioni e come ottenere addebitata per poterli?

Una connessione gestita è definita come una delle operazioni seguenti:

1. Connessione AMQP da un client a una coda Bus di servizio o l'argomento/abbonamento.

2. Una chiamata HTTP per ricevere un messaggio da un argomento Bus di servizio o una coda contenente un valore di timeout di ricezione maggiore di zero.

Spese Bus di servizio per il numero massimo di connessioni gestite simultanee che superano la quantità inclusa (1.000 nel livello Standard). Picchi misurati oraria, in quote dividendo per 744 ore in un mese e sommate nel periodo di fatturazione mensile. La quantità inclusa (1.000 connessioni gestite al mese) viene applicata alla fine del periodo di fatturazione con la somma dei picchi orari ripartiti.

Per esempio:

1. Ognuno dei 10.000 dispositivi si connette tramite una connessione AMQP e riceve i comandi da un argomento Bus di servizio. I dispositivi inviare telemetria eventi a un Hub di evento. Se tutti i dispositivi di connettersi per 12 ore al giorno, gli addebiti connessione seguenti si applicano (oltre alle eventuali altre spese argomento Service Bus): *12 ore* 10.000 connessioni 31 giorni / 744 = 5.000 negoziate connessioni. Dopo la concessione mensile di 1.000 connessioni gestite, sarebbe addebitate 4.000 connessioni gestite, pari a $ pari a 0,03 per ogni connessione gestito, per un totale di $120.

2. 10.000 dispositivi messaggi da una coda di servizio Bus tramite HTTP, che specifica un timeout di diverso da zero. Se tutti i dispositivi di connettersi per 12 ore al giorno, verranno visualizzate le spese di connessione segue (oltre alle altre spese Service Bus): 10.000 ricezione HTTP connessioni *12 ore al giorno* 31 giorni / ore 744 = 5.000 negoziate connessioni.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>I costi della connessione gestito si applicano a code e argomenti/abbonamenti?

Sì. Non esistono spese connessione per l'invio di eventi tramite HTTP, indipendentemente dal numero di invio sistemi o dispositivi. Ricezione di eventi con HTTP utilizzando un timeout maggiore di zero, a volte chiamato "tempo di polling" genera i costi della connessione gestito. Connessioni AMQP generano i costi della connessione gestito indipendentemente dal fatto che le connessioni vengono utilizzate per inviare o ricevere. Si noti che sono consentite 100 connessioni gestite senza costi aggiuntivi in uno spazio dei nomi di base. Questo è il numero massimo di connessioni gestite consentite per la sottoscrizione Azure. I primi 1.000 connessioni gestite attraverso tutti gli spazi di nomi Standard di un abbonamento a Azure sono incluse senza costi aggiuntivi (oltre le spese di base). Poiché questi consentito è sufficienti per coprire molti scenari di messaggistica per servizio, i costi della connessione gestito in genere solo diventano pertinenti se si prevede di utilizzare il polling lungo AMQP o HTTP con un numero elevato di client. ad esempio, per ottenere più efficiente evento streaming o consentire la comunicazione bidirezionali con molti dispositivi o istanze dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sui prezzi Bus di servizio, vedere [Bus di servizio Azure prezzi pagina](https://azure.microsoft.com/pricing/details/service-bus/).

- Per alcune domande frequenti comuni intorno bus di servizio prezzi e fatturazione, vedere [Domande frequenti su Bus di servizio](service-bus-faq.md#service-bus-pricing) .

[Portale classica Azure]: http://manage.windowsazure.com