<properties 
    pageTitle="Domande frequenti di inoltro | Microsoft Azure"
    description="Fornisce le risposte ad alcune domande frequenti su Azure inoltro."
    services="service-bus"
    documentationCenter="na"
    authors="jtaubensee"
    manager=""
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="jotaub" />

# <a name="relay-faq"></a>Inoltro domande frequenti

In questo articolo fornisce le risposte ad alcune domande frequenti su Microsoft Azure inoltro. È inoltre possibile visitare le [Domande frequenti su supporto di Azure](http://go.microsoft.com/fwlink/?LinkID=185083) per informazioni generali su Azure prezzi e supporto tecnico. Gli argomenti seguenti sono inclusi:

- [Domande di carattere generale su Azure inoltro](#general-questions)
- [Prezzi](#pricing)
- [Quote](#quotas)
- [Gestione della sottoscrizione e spazio dei nomi](#subscription-and-namespace-management)
- [Risoluzione dei problemi](#troubleshooting)

## <a name="general-questions"></a>Domande di carattere generale

### <a name="what-is-azure-relay"></a>Che cos'è l'inoltro di Azure?

Il [servizio di inoltro](relay-what-is-it.md) consente di ospitare trasparente e accedere ai servizi WCF ovunque. In altre parole, in questo modo applicazioni ibride che eseguono in un Data Center Azure e un ambiente aziendale locale.

### <a name="what-is-a-relay-namespace"></a>Che cos'è uno spazio dei nomi di inoltro?

Un [spazio dei nomi](Relay-create-namespace-portal.md) fornisce un contenitore per indirizzare le risorse di inoltro all'interno dell'applicazione. Creazione di una è necessaria utilizzare inoltro e uno dei primi passi nella Guida introduttiva.

## <a name="pricing"></a>Prezzi

In questa sezione fornisce le risposte ad alcune domande frequenti su inoltro struttura dei prezzi. È inoltre possibile visitare le [Domande frequenti sul supporto di Azure](http://go.microsoft.com/fwlink/?LinkID=185083) informazioni generali Microsoft Azure prezzi. Per informazioni complete sui prezzi inoltro, vedere [informazioni sui prezzi Bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="how-do-you-charge-for-relay"></a>Come carica per l'inoltro

Per informazioni complete sui prezzi inoltro, vedere [informazioni sui prezzi Bus di servizio][Pricing overview]. Oltre a prezzi notati, addebitate per trasferimenti di dati a esse associati per uscita all'esterno del centro di dati in cui viene eseguito il provisioning dell'applicazione.

### <a name="what-usage-of-relay-is-subject-to-data-transfer"></a>Quali l'uso di inoltro è tenuto a trasferimenti di dati?

Inoltro include 5 GB di ingresso dati al mese per abbonamento. Non esiste costi aggiuntivi Azure in ingresso/uscita per i dati utilizzati dalle inoltro.

Le spese di dati per l'inoltro per ingresso provenienti da mittenti solo, come inoltro listener comportano un addebito di dati. Ad esempio, se si invia almeno 1 GB, verrà solo fatturato per 1 GB, anche se un comunicare ascoltatore anche ricevuto almeno 1 GB e potrebbe essere all'esterno di Data Center di Azure.

### <a name="how-are-relay-hours-calculated"></a>Calcolare delle ore di inoltro

Inoltro ore vengono addebitate per l'aggiornamento cumulativo periodo di tempo in cui ogni inoltro sono "aprire" in un determinato periodo di fatturazione. Un inoltro viene creata in modo implicito ed aperto in un determinato spazio dei nomi quando un comunicare ascoltatore inoltro innanzitutto si connette all'indirizzo. L'inoltro è chiuso solo quando l'ultimo listener si disconnette dal relativo indirizzo. Di conseguenza, ai fini della fatturazione di che un inoltro sono considerato "aprire" dall'ora comunicare ascoltatore inoltro prima si connette, all'ora che ultima comunicare ascoltatore inoltro disconnette dello spazio dei nomi. In altre parole, un inoltro è considerato aperto ogni volta che uno o più listener inoltro connessi al proprio indirizzo di Bus di servizio.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-given-relay"></a>Cosa fare se si usano più comunicare ascoltatore connesso a un determinato inoltro?

In alcuni casi, un singolo inoltro potrebbe essere più listener connessi. Un inoltro è considerato "aprire" quando è connesso almeno un comunicare ascoltatore inoltro. Aggiungere altri listener all'inoltro della posta genereranno ore inoltro aggiuntive. Il numero di mittenti inoltro (client che richiamare o inviare messaggi all'inoltro) connesso a un inoltro anche non ha alcun effetto sul calcolo delle ore di inoltro.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Come viene calcolata l'indicatore di messaggi di inoltro WCF?

**Questa è applicabile solo all'inoltro WCF e non un costo delle connessioni ibrido**

In generale, vengono calcolati i messaggi di fatturabili di inoltro utilizzando il metodo descritto in precedenza per entità gestita (code, argomenti e abbonamenti). Esistono tuttavia alcune differenze importanti:

Inviare che un messaggio da un inoltro Bus servizio viene considerato come un "completo tramite" Invia a comunicare ascoltatore inoltro che riceve il messaggio, piuttosto che invia al servizio Bus Relay seguita da una consegna a comunicare ascoltatore inoltro. Di conseguenza, una chiamata di servizio di stile risposta convocazione (di fino a 64 KB) rispetto a un comunicare ascoltatore inoltro verrà generato in due messaggi fatturabili: un messaggio fatturabile per la richiesta e un messaggio fatturabile per la risposta (presupponendo che la risposta è anche \<= 64 KB). È diverso dal utilizzando una coda da interporre tra un client a un servizio. In tal caso, lo stesso modello di risposta convocazione richiedono invia una richiesta alla coda, seguita da un'annullamento dell'accodamento/consegna dalla coda al servizio, seguito da invia una risposta a un'altra coda e un'annullamento dell'accodamento/consegna da tale coda al client. Utilizzare la stessa (\<= 64 KB) ipotesi di dimensioni nell'intera, il criterio di coda da pertanto comporterebbe di quattro messaggi fatturabili, due volte il numero di fatturazione per implementare lo stesso modello tramite un inoltro. Naturalmente, esistono vantaggi all'uso di code per ottenere questo modello, ad esempio durata e caricare il livellamento. Questi vantaggi possono giustificare spesa aggiuntiva.

Inoltro vengono aperte utilizzando l'associazione WCF netTCPRelay considera i messaggi non come singoli messaggi ma come un flusso di dati che passano attraverso il sistema. In altre parole, solo il mittente e comunicare ascoltatore avere visibilità il contorno dei singoli messaggi inviati o ricevuti tramite questo collegamento. In questo modo, di inoltro tramite l'associazione netTCPRelay, tutti i dati viene considerato come un flusso per il calcolo dei messaggi fatturabili. In questo caso, Bus di servizio vengono calcolate la quantità totale di dati inviati o ricevuti tramite ogni singolo inoltro alla scala cronologica 5 minuti e divide il totale per 64 KB per determinare il numero di messaggi fatturabili per l'inoltro in questione durante il periodo di tempo.

## <a name="quotas"></a>Quote

|Nome di quota|Ambito|Tipo|Comportamento quando hanno superato|Valore|
|---|---|---|---|---|
|Listener simultanee su un inoltro|Entità|Statica|Le successive richieste per le connessioni aggiuntive verranno rifiutate e verrà ricevuta un'eccezione dal codice chiamante.|25|
|Listener inoltro simultanee|Livello di sistema|Statica|Le successive richieste per le connessioni aggiuntive verranno rifiutate e verrà ricevuta un'eccezione dal codice chiamante.|2.000|
|Connessioni simultanee inoltro per tutti gli endpoint di inoltro in uno spazio dei nomi del servizio|Livello di sistema|Statica|-|5.000|
|Endpoint di inoltro per spazio dei nomi del servizio|Livello di sistema|Statica|-|10.000|
|Dimensione messaggio [NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) e [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx) inoltra|Livello di sistema|Statica|I messaggi in arrivo che superano le quote di questi verranno rifiutati e verrà ricevuta un'eccezione dal codice chiamante.|64KB
|Dimensione messaggio [HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) e [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) inoltra|Livello di sistema|Statica|-|Illimitato|

### <a name="does-relay-have-any-usage-quotas"></a>Inoltro dispone le quote di uso?

Per impostazione predefinita, per qualsiasi cloud servizio Microsoft imposta un'aggregazione quota utilizzo mensile che viene calcolata per tutte le sottoscrizioni del cliente. Poiché siamo consapevoli che potrebbe essere necessario più di questi limiti, contattare il servizio di assistenza clienti in qualsiasi momento in modo che sia possibile comprendere le proprie esigenze e modificare questi limiti in modo appropriato. Per Bus di servizio, le quote di utilizzo aggregate sono i seguenti:

- 5 miliardi messaggi
- 2 milioni inoltro ore

Mentre si riserva il diritto per disattivare un account di clienti che hanno superato le quote di utilizzo in un determinato mese, esamineremo notifiche tramite posta elettronica e apportare diversi tentativi di contattare un cliente prima di eseguire alcuna operazione. I clienti che superano le quote continuerà a essere responsabili degli addebiti che superano le quote.

#### <a name="naming-restrictions"></a>Restrizioni di denominazione

Uno spazio dei nomi di inoltro può essere solo tra 6-50 caratteri.

## <a name="subscription-and-namespace-management"></a>Gestione della sottoscrizione e spazio dei nomi

### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Come eseguire la migrazione uno spazio dei nomi a un'altra sottoscrizione Azure?

È possibile utilizzare i comandi di PowerShell (disponibili nell'articolo [di seguito](../service-bus-messaging/service-bus-powershell-how-to-provision.md#migrate-a-namespace-to-another-azure-subscription)) per spostare uno spazio dei nomi da una sottoscrizione di Azure. Per eseguire l'operazione, lo spazio dei nomi deve essere già attivo. L'utente che esegue i comandi deve essere un amministratore per le sottoscrizioni di origine e destinazione.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-their-suggested-actions"></a>Quali sono alcune eccezioni generate da Azure inoltro API e le azioni suggerite?

Le [eccezioni di inoltro] [ Relay exceptions] descritte alcune eccezioni con azioni suggerite.

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Che cos'è una firma di Access condiviso e le lingue supportano generare una firma?

Le firme di Access condiviso sono un meccanismo di autenticazione basato su SHA-256 hash sicuro o URI. Per informazioni su come generare il proprio firme in nodo, PHP, Java e C\#, vedere l'articolo [Condiviso firme di Access][] .

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Accesso condiviso firme]: service-bus-sas-overview.md

## <a name="next-steps"></a>Passaggi successivi:

- [Creare uno spazio dei nomi](relay-create-namespace-portal.md)
- [Guida introduttiva a .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Guida introduttiva a nodo](relay-hybrid-connections-node-get-started.md)