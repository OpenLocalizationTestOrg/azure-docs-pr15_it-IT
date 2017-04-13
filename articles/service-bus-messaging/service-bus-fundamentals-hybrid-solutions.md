<properties 
    pageTitle="Bus di servizio Azure | Microsoft Azure" 
    description="Introduzione all'utilizzo Bus di servizio per connettere le applicazioni Azure per altro software." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/31/2016" 
    ms.author="sethm"/>

# <a name="azure-service-bus"></a>Bus di servizio Azure

Se un'applicazione o un servizio viene eseguito nel cloud, o in locale, è spesso necessario interagire con altre applicazioni o servizi. Per fornire ampiamente utile per eseguire questa operazione, Microsoft Azure offre Bus di servizio. In questo articolo viene esaminata questa tecnologia, che spiega che cos'è e perché è consigliabile utilizzarlo.

## <a name="service-bus-fundamentals"></a>Nozioni fondamentali su Bus di servizio

Contattare il situazioni diverse per diversi stili di comunicazione. In alcuni casi, consentendo alle applicazioni di inviare e ricevere messaggi tramite una coda semplice è la soluzione migliore. In altri casi, una coda normale non è sufficiente; una coda con meccanismo di pubblicazione e sottoscrizione è migliore. In alcuni casi, tutti gli elementi sono realmente necessari è una connessione tra le applicazioni. code non sono obbligatori. Servizio Bus fornisce tutte le tre opzioni di attivazione delle applicazioni di interagire in vari modi.

Servizio Bus è un servizio cloud multi-tenant, il che significa che il servizio viene condiviso da più utenti. Ogni utente, ad esempio uno sviluppatore di applicazioni, viene creato un *spazio dei nomi*, quindi definisce i meccanismi di comunicazione che Anna deve nello spazio dei nomi. Figura 1 mostra l'aspetto.

![][1]
 
**Figura 1: Servizio Bus fornisce un servizio multi-tenant per la connessione di applicazioni tramite il cloud.**

All'interno di uno spazio dei nomi, è possibile usare uno o più istanze di quattro meccanismi di comunicazione diversi, ognuno dei quali si connette applicazioni in modo diverso. Sono disponibili i seguenti:

- *Code*, consentire la comunicazione unidirezionale. Ogni coda funge da intermediario (a volte chiamato un *gestore*) che archivia i messaggi inviati fino a quando non vengono ricevuti. Ogni messaggio viene ricevuto da un singolo destinatario.
- *Gli argomenti*, che consentono la comunicazione unidirezionale con *abbonamenti*- un singolo argomento possono hanno più abbonamenti. Ad esempio una coda di un argomento funge da un gestore, ma ogni abbonamento facoltativamente possibile utilizzare un filtro per ricevere solo i messaggi che soddisfano criteri specifici.
- *Inoltro*, che consentono la comunicazione bidirezionali. Diversamente dagli argomenti e code, un inoltro non archivia i messaggi in corso; non è un gestore. Se, tuttavia, sufficiente li passa all'applicazione di destinazione.

Quando si crea una coda, l'argomento o inoltro, si assegnare un nome. In combinazione con la chiamata dello spazio dei nomi, questo nome crea un identificatore univoco dell'oggetto. Applicazioni possono fornire questo nome al servizio Bus, quindi utilizzare tale coda, l'argomento o inoltro di comunicare con un altro. 

Per usare uno di questi oggetti in uno scenario di inoltro, applicazioni di Windows possono utilizzare Windows Communication Foundation (WCF). Per code e gli argomenti della applicazioni di Windows possono utilizzare definiti Bus di servizio di messaggistica API. Per rendere più semplice da utilizzare dalle applicazioni di Windows non questi oggetti, Microsoft offre SDK per Java, Node e altre lingue. È anche possibile accedere code e argomenti utilizzando le API REST tramite HTTP. 

È importante tenere presente che anche se servizio Bus stesso viene eseguito nel cloud (vale a dire Azure Center Microsoft), applicazioni che utilizzano possono essere eseguite ovunque. È possibile utilizzare Bus di servizio per la connessione di applicazioni in esecuzione in Azure, ad esempio, o in esecuzione all'interno di Data Center. È possibile anche utilizzare per connettersi a un'applicazione in esecuzione in Azure o in un'altra piattaforma cloud con un'applicazione locale o Tablet e telefoni. È anche possibile connettersi a un'applicazione centrale o tra loro elettrodomestici, sensori e altri dispositivi. Servizio Bus è un meccanismo di comunicazione nel cloud accessibile praticamente ovunque. Come si usa dipende cos'è necessario eseguire le applicazioni.

## <a name="queues"></a>Code

Si supponga che si desidera connettere due applicazioni utilizzando una coda Bus di servizio. Questa situazione è illustrato nella figura 2.

![][2]
 
**Figura 2: Code Bus di servizio forniscono accodamento asincrono unidirezionale.**

Il processo è semplice: un mittente invia un messaggio a una coda Bus di servizio e un ricevitore rileva i messaggi in un secondo momento. Una coda può avere solo un singolo destinatario, come illustrato nella figura 2. In alternativa, è possano per saperne di più applicazioni dalla stessa coda. In quest'ultimo caso, ogni messaggio viene letto dal sola destinatario. Per un servizio multi-cast, è necessario utilizzare un argomento.

Ogni messaggio contiene due parti: un insieme di proprietà, ogni una coppia chiave/valore e il corpo del messaggio binario. Modalità di utilizzo dipende da cosa un'applicazione tenta di eseguire. Ad esempio, un'applicazione di inviare un messaggio relativo recente vendita può includere le proprietà *Venditore = "Ava"* e *Importo = 10000*. Corpo del messaggio potrebbe contenere un'immagine digitalizzata del contratto di vendita o, se questo non è disponibile, basta rimanere vuoto.

Un ricevitore leggere un messaggio da una coda Bus di servizio in due modi diversi. La prima opzione chiamata *ReceiveAndDelete*rimuove un messaggio dalla coda ed eliminarlo immediatamente. Questa operazione è semplice, ma se il destinatario si blocca prima che venga completato l'elaborazione del messaggio, il messaggio andranno perso. Perché è stato rimosso dalla coda, nessun altro ricevitore possibilità di accedervi. 

La seconda opzione, *PeekLock*è utilizzata per risolvere il problema. Ad esempio **ReceiveAndDelete**lettura **PeekLock** rimuove un messaggio dalla coda. Non verranno eliminati, tuttavia il messaggio. Se, tuttavia, blocca il messaggio, rendendo invisibile ad altri destinatari, quindi aspetta per uno dei tre eventi:

- Se il destinatario elabora correttamente il messaggio, chiama **completamento**e coda Elimina il messaggio. 
- Se il destinatario decide che non può elaborare correttamente il messaggio, chiamate **abbandonare**. Coda quindi rimuove il blocco dal messaggio e rende disponibile agli altri destinatari.
- Se il destinatario chiama nessuna di queste all'interno di un periodo di tempo configurabile (per impostazione predefinita, 60 secondi), coda presuppone che il ricevitore non è riuscito. In questo caso, si comporta come se fosse stato chiamato il ricevitore **abbandonare**rendere disponibile il messaggio agli altri destinatari.

Si noti che può verificarsi qui: stesso recapitare il messaggio potrebbe due volte, ad esempio a due diversi destinatari. Le applicazioni che utilizzano code Bus di servizio devono essere preparate per l'oggetto. Per semplificare il rilevamento duplicati, che ogni messaggio ha una proprietà **ID messaggio** univoca che per impostazione predefinita rimarrà invariato indipendentemente da come numero di volte il messaggio viene letto da una coda. 

Code sono utili in alcune situazioni. Consentono alle applicazioni di comunicare anche quando entrambe non sono in esecuzione contemporaneamente, è quello che è particolarmente utile con batch e applicazioni per dispositivi mobili. Una coda con più ricevitori vengono forniti anche bilanciamento del carico automatico, dal momento che i messaggi inviati sono suddivisi in questi ricevitori.

## <a name="topics"></a>Argomenti

Utile così come sono, code non sono sempre la soluzione ideale. In alcuni casi servizio Bus argomenti sono disponibili più efficaci. Figura 3 illustra questo concetto.

![][3]
 
**Figura 3: In base al filtro che specifica di un'applicazione di sottoscrizione, è possibile ricevere alcuni o tutti i messaggi inviati a un argomento Bus di servizio.**

Un *argomento* è simile in molti modi a una coda. Mittenti invia messaggi a un argomento nello stesso modo che inviano messaggi a una coda e i messaggi hanno un aspetto lo stesso di code. La differenza è che argomenti Abilita ogni applicazione di destinazione creare il proprio *abbonamento* mediante la definizione di un *filtro*. Sottoscrizione verrà visualizzato solo i messaggi che soddisfano il filtro. Figura 3, ad esempio, viene visualizzato un mittente e a un argomento con gli abbonati a tre, ognuna con il proprio filtro:

- Sottoscrittore 1 riceve solo i messaggi contenenti la proprietà *Venditore = "Ava"*.
- Sottoscrittore 2 riceve i messaggi contenenti la proprietà *Venditore = "Trascrizione"* e/o contiene una proprietà di *Importo* il cui valore è maggiore di 100.000. Forse trascrizione è il responsabile delle vendite, in modo che desidera vedere propria vendite e tutte le vendite grande indipendentemente dal fatto che si.
- Sottoscrittore 3 ha configurato il filtro su *True*, il che significa che riceve tutti i messaggi. Ad esempio, l'applicazione potrebbe essere responsabile della gestione di un audit trail e pertanto è necessario visualizzare tutti i messaggi.

Come con code, gli abbonati a un argomento possono leggere i messaggi con **ReceiveAndDelete** o **PeekLock**. Diversamente da quanto succede code, tuttavia, è possibile ricevuto un singolo messaggio inviato a un argomento tramite più abbonamenti. Questo approccio, ossia *pubblicare e sottoscrivere* (o *pub/sub*), è utile quando più applicazioni sono interessate gli stessi messaggi. Definendo il filtro di destro, ogni sottoscrittore può accedere istantaneamente solo la parte del flusso di messaggi da visualizzare.

## <a name="relays"></a>Inoltro

Code e argomenti comunicazioni unidirezionale asincrono tramite un gestore. Il traffico scorre solo in una direzione e nessuna connessione diretta tra mittenti e destinatari. Ma cosa fare se non si desidera? Si supponga che le applicazioni necessitano a inviare e ricevere messaggi, o forse per aggiungere un collegamento diretto tra di esse e non è necessario un gestore per archiviare i messaggi. Per indirizzare scenari, ad esempio, Service Bus fornisce *Inoltra*, come illustrato nella figura 4.

![][4]
 
**Figura 4: Inoltro Bus di servizio consente la comunicazione bidirezionali icona del tra le applicazioni.**

La domanda a domande su inoltro è questa: perché utilizzare uno? Anche se non necessarie code, perché rendere le applicazioni di comunicare tramite un servizio cloud anziché solo interagire direttamente? La risposta è che parlare direttamente può essere difficile quanto si potrebbe pensare.

Si supponga che si desidera connettere due applicazioni locale, sia in esecuzione all'interno di data center aziendale. Ognuna di queste applicazioni dietro un firewall e ogni Data Center probabilmente utilizza NAT (NAT). Il firewall blocca posta in arrivo dati in tutte le porte e NAT implica che il computer che è in esecuzione ogni applicazione non è disponibile un indirizzo IP fisso che è possibile contattare direttamente dall'esterno il Data Center. Senza alcune informazioni aggiuntive, la connessione di queste applicazioni tramite internet è un problema.

Può essere utile un inoltro Bus di servizio. Per comunicare direzioni tramite un inoltro, ogni applicazione stabilisce una connessione in uscita TCP con Bus di servizio e quindi tiene aperto. Tutte le comunicazioni tra le due applicazioni percorre queste connessioni. Poiché ogni connessione è stata stabilita all'interno del centro dati, il firewall consente il traffico in ingresso per ogni applicazione senza aprire nuove porte. Questo approccio ottiene anche il problema NAT, in quanto ogni applicazione con un endpoint coerenza nel cloud in tutta la comunicazione. Tramite lo scambio di dati tramite l'inoltro, le applicazioni possono evitare i problemi che in caso contrario consentirebbe di comunicazione difficile. 

Per utilizzare servizio Bus inoltro, applicazioni si basano su Windows Communication Foundation (WCF). Servizio Bus fornisce le associazioni WCF che rendono più semplice per le applicazioni di Windows interagire tramite inoltro. Applicazioni che utilizzano già WCF possono in genere solo specificare uno di questi associazioni e comunicare con loro tramite un inoltro. Diversamente dagli argomenti e code, tuttavia, l'utilizzo inoltro dalle applicazioni di Windows non, anche se possibili, richiede alcune attività di programmazione; non vengono fornite librerie standard.

Diversamente dagli argomenti e code, le applicazioni non creano in modo esplicito inoltro. Se, tuttavia, quando un'applicazione che desidera ricevere messaggi stabilisce una connessione TCP con Bus di servizio, viene creato automaticamente un inoltro. Quando si elimina la connessione, viene eliminato l'inoltro. Per attivare un'applicazione trovare inoltro creato da uno specifico listener, Service Bus fornisce un registro di sistema che consente alle applicazioni individuare un inoltro specifica in base al nome.

Inoltro è la soluzione ideale quando è necessario comunicazione diretta tra le applicazioni. Si consideri ad esempio un sistema di prenotazione delle compagnie aeree in esecuzione in un data center locale accessibili da chioschi di controllo, dispositivi mobili e altri computer. Applicazioni in esecuzione su tutti i sistemi potevano inoltro Bus di servizio nel cloud per comunicare, nel punto in cui potrebbe essere in esecuzione.

## <a name="summary"></a>Riepilogo

La connessione di applicazioni è sempre stata parte di sviluppo di soluzioni complete, l'impostazione dell'intervallo di scenari che richiedono applicazioni e servizi per comunicare con loro per ingrandire le dimensioni di altre applicazioni e i dispositivi connessi a Internet. Grazie a tecnologie basate su cloud per ottenere questo tramite code, argomenti e inoltro, Bus di servizio l'obiettivo di semplificare questa funzione essenziale implementare e più diffusi.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni fondamentali di Azure servizio Bus, seguire questi collegamenti per altre informazioni.

- Come usare [code Bus di servizio](service-bus-dotnet-get-started-with-queues.md)
- Come usare [argomenti Bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- Come usare [Bus di servizio di inoltro](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md)
- [Esempi di Bus di servizio](service-bus-samples.md)

[1]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_01_architecture.png
[2]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_02_queues.png
[3]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[4]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_04_relay.png
