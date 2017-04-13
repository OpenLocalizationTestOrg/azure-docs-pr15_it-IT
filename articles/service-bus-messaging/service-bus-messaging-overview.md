<properties
    pageTitle="Cenni preliminari sulla messaggistica Bus di servizio | Microsoft Azure"
    description="Servizio Bus messaggistica: recapito dati flessibile nel cloud"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="sethm"/>


# <a name="service-bus-messaging-flexible-data-delivery-in-the-cloud"></a>Bus di servizio di messaggistica: recapito dati flessibile nel cloud

Bus di servizio di Microsoft Azure è un servizio di recapito informazioni attendibili. Lo scopo di questo servizio è per semplificare la comunicazione. Quando due o più parti desiderino scambio di informazioni, è necessario un meccanismo di comunicazione. Servizio Bus è un meccanismo di comunicazione gestito o di terze parti. Si tratta in modo simile a un servizio postale reali. Servizi postali facilitano inviare diversi tipi di lettere e di pacchetti con una serie di garanzie di consegna, in un punto qualsiasi nel mondo.

Analogamente alle servizio postale l'esecuzione di lettere, Bus di servizio è recapito flessibili informazioni dal mittente e il destinatario. Il servizio di messaggistica garantisce che le informazioni recapitate anche se entrambe le parti non sono mai sia online nello stesso momento o se non sono disponibili in contemporanea esatta. In questo modo, messaggistica è simile all'invio di una lettera, mentre non negoziate comunicazioni sono simile all'applicazione di una chiamata telefonica (o modalità di utilizzo di una chiamata telefonica per essere - prima di chiamata in attesa e chiamante ID, che sono molto più come messaggistica gestito).

Il mittente del messaggio può anche richiedere una varietà di caratteristiche di recapito incluse le transazioni, il rilevamento duplicati, scadenza basate sul tempo e batch. Questi modelli sono anche analogie postale: ripetere recapito, firma necessaria, modifica indirizzo o il richiamo.

Servizio Bus supporta due modelli di messaggistica distinti: *inoltro* e *gestito messaggistica*.

## <a name="service-bus-relay"></a>Servizio Bus inoltro

Componente di [inoltro](../service-bus-relay/service-bus-relay-overview.md) del servizio Bus è un servizio centralizzato (ma altamente bilanciamento del carico) che supporta diversi diversi protocolli di trasporto e standard dei servizi Web. Sono inclusi SOAP WS-, * e anche resto. Il [servizio di inoltro](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md) sono disponibili numerose opzioni di integrazione applicativa inoltro diversi e consente di negoziare connessioni peer-to-peer dirette quando è possibile. Servizio Bus ottimizzato per gli sviluppatori di .NET che utilizzano Windows Communication Foundation (WCF), con entrambi per quanto riguarda le prestazioni e facilità di utilizzo e offre accesso completo per il servizio di inoltro tramite le interfacce SOAP e altre applicazioni. Questo consente eventuali SOAP e il resto ambiente di programmazione per l'integrazione con Bus di servizio.

Il servizio di inoltro supporta messaggistica unidirezionale tradizionale, richiesta di messaggistica e la messaggistica peer-to-peer. Supporta anche la distribuzione di evento in ambito Internet per attivare pubblicazione sottoscrizione scenari e comunicazione bidirezionali socket per migliorare l'efficienza punto maggiore. Nel modello di messaggi inoltrato, un servizio locale si connette al servizio di inoltro tramite una porta in uscita e crea un socket bidirezionale per la comunicazione collegato a un indirizzo rendezvous specifico. Il cliente può quindi comunicare con il servizio locale mediante l'invio di messaggi per il servizio di inoltro destinate a indirizzo rendezvous. Il servizio di inoltro verrà quindi "inoltrare" messaggi al servizio locale tramite socket bidirezionale già presenti. Il client non è necessario una connessione diretta al servizio locale e non è necessario sapere dove si trova il servizio e le porte in ingresso non è necessario il servizio locale aperta sul firewall.

Si avvia la connessione tra il servizio locale e il servizio di inoltro, utilizzando una famiglia di prodotti di associazioni di inoltro"WCF". Dietro le quinte, le associazioni di inoltro eseguire il mapping di trasporto elementi associazione progettati per creare i componenti di canale WCF che si integrano con Bus di servizio nel cloud.

Servizio Bus inoltro offre molti vantaggi, ma è necessario il server e client per entrambe online nello stesso momento per inviare e ricevere messaggi. Non si tratta ottimale per la comunicazione HTTP stile, in cui le richieste potrebbero non essere in genere lunga durate, né per i client di connettersi solo in alcuni casi, ad esempio browser, applicazioni per dispositivi mobili e così via. Negoziate messaggistica supporta disaccoppiati comunicazioni e presenta dei vantaggi; client e server possono connettersi quando necessario ed eseguire le operazioni in modo asincrono.

## <a name="brokered-messaging"></a>Negoziate messaggistica

A differenza di combinazione inoltro [gestito messaggistica](service-bus-queues-topics-subscriptions.md) possono essere considerati come asincrone o "temporaneamente in disaccoppiato". Produttori (mittenti) e dei consumatori (ricevitori) non è necessario essere online nello stesso momento. L'infrastruttura di messaggistica affidabile vengono memorizzati i messaggi in "gestore" (ad esempio una coda) fino a quando non la parte che è pronta per riceverle. In questo modo componenti dell'applicazione distribuita la disconnessione, uno liberamente; ad esempio, per la manutenzione o a causa di un arresto anomalo del componente senza influenzare l'intero sistema. Inoltre, applicazione di destinazione potrebbe contenere solo in linea a determinati orari del giorno, ad esempio un sistema di gestione dell'inventario solo è necessario eseguire alla fine della giornata lavorativa.

I componenti di base dell'infrastruttura di messaggistica gestito Bus di servizio sono code, argomenti e le sottoscrizioni.  La differenza principale è che argomenti supportano funzionalità di pubblicazione/sottoscrizione che possono essere usate per sofisticate basato sul contenuto routing e il recapito logica, incluso l'invio a più destinatari. Questi componenti scenari nuovo asincrono messaggistica, ad esempio la separazione temporale pubblicano/sottoscrizione e il bilanciamento del carico. Per ulteriori informazioni su queste entità messaggistica, vedere [sottoscrizioni, argomenti e code Bus di servizio](service-bus-queues-topics-subscriptions.md).

Come per l'infrastruttura di inoltro, la funzionalità di messaggistica gestita viene fornita per ai linguaggi WCF e .NET Framework per, nonché tramite resto.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla messaggistica Bus di servizio, vedere gli argomenti seguenti.

- [Nozioni fondamentali su Bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
- [Abbonamenti, argomenti e code Bus di servizio](service-bus-queues-topics-subscriptions.md)
- [Come usare code Bus di servizio](service-bus-dotnet-get-started-with-queues.md)
- [Come usare le sottoscrizioni e gli argomenti della Bus di servizio](./service-bus-dotnet-how-to-use-topics-subscriptions.md)
 
