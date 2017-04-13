<properties 
    pageTitle="Azure code e servizio Bus code - confrontati e contrasto | Microsoft Azure"
    description="Consente di analizzare le differenze e le similarità tra due tipi di code offerte da Azure."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="tbd"
    ms.date="09/23/2016"
    ms.author="sethm" />

# <a name="azure-queues-and-service-bus-queues---compared-and-contrasted"></a>Azure code e servizio Bus code - confrontati e contrasto

In questo articolo analizza le differenze e le similarità tra i due tipi di code attualmente offerte da Microsoft Azure: code code Azure e Bus di servizio. Se si utilizza queste informazioni, è possibile confrontare e tecnologie rispettivi e sarà possibile prendere una decisione più informata sulla soluzione migliore soddisfa le proprie esigenze.

## <a name="introduction"></a>Introduzione

Microsoft Azure supporta due tipi di meccanismi di coda: **Code Azure** e **servizio Bus**.

**Azure code**, che fanno parte dell'infrastruttura di [archiviazione Azure](https://azure.microsoft.com/services/storage/) , portare in primo piano una semplice interfaccia basati su REST Get/inserire/anteprima, fornendo messaggistica affidabile e permanente all'interno e tra i servizi.

**Servizio Bus code** fanno parte di un'infrastruttura di [Azure messaggistica](https://azure.microsoft.com/services/service-bus/) più ampia che supporta l'accodamento e pubblicazione/sottoscrizione, remoti di servizio Web e i motivi di integrazione. Per ulteriori informazioni sulle code Bus di servizio, argomenti/sottoscrizioni e inoltro, vedere [Panoramica della messaggistica Bus di servizio](service-bus-messaging-overview.md).

Sebbene esistano contemporaneamente entrambe le tecnologie di accodamento, code Azure sono stati introdotti prima di tutto come meccanismo di archiviazione dedicato basato su servizi di archiviazione Azure. Code Bus di servizio sono progettate nella parte superiore dell'infrastruttura "negoziate messaggistica" esercitare progettato per integrare applicazioni o componenti di applicazioni che includano più protocolli di comunicazione, i contratti di dati, i domini e/o ambienti di rete.

In questo articolo confronta le tecnologie di due code offerte da Azure esaminando le differenze tra il comportamento e l'implementazione delle funzionalità offerte da ciascuno. L'articolo fornisce indicazioni per la scelta quali caratteristiche potrebbero meglio soddisfare le esigenze di sviluppo di applicazioni.

## <a name="technology-selection-considerations"></a>Considerazioni sulla selezione di tecnologia

Code code Azure e Bus di servizio da implementazioni di Accodamento servizio attualmente offerto in Microsoft Azure. Un set di funzionalità leggermente diverso, ovvero è possibile scegliere una delle due o utilizzare entrambe, in base alle esigenze della soluzione particolare o business/tecnici problemi sono ciascuno.

Stabilire quale tecnologia Accodamento adatta allo scopo di una determinata soluzione, gli sviluppatori e progettisti di soluzioni necessario prendere in considerazione i suggerimenti riportati di seguito. Per ulteriori informazioni, vedere la sezione successiva.

Gli sviluppatori/progettista soluzione, **è consigliabile utilizzare code Azure** quando:

- L'applicazione dovrà memorizzare oltre 80 GB dei messaggi in una coda, in cui i messaggi di avere una durata inferiore a 7 giorni.

- L'applicazione desidera tenere traccia dello stato per l'elaborazione di un messaggio all'interno di coda. Ciò è utile se si blocca lavoro elaborazione di un messaggio. Un lavoro successive quindi possibile utilizzare queste informazioni per continuare da dove interrotto il lavoro precedente.

- È necessario registri lato server di tutte le operazioni eseguite le code.

Gli sviluppatori/progettista soluzione, **è consigliabile utilizzare code Bus del servizio** quando:

- La soluzione deve essere in grado di ricevere i messaggi senza dover polling della coda. Con Bus di servizio, è possibile ottenere questo tramite il polling lungo operazione utilizzando i protocolli basati su TCP che supporta Bus di servizio di ricezione.

- La soluzione richiede una coda per fornire un garantito first-in-first-out il recapito ordinato (FIFO).

- Si desidera un'esperienza simmetrica in Azure e Windows Server (cloud privato). Per ulteriori informazioni, vedere [Servizio Bus per Windows Server](https://msdn.microsoft.com/library/dn282144.aspx).

- La soluzione deve essere in grado di supportare il rilevamento automatico duplicato.

- Si desidera che l'applicazione per elaborare i messaggi come paralleli lunga flussi (messaggi sono associati a un flusso utilizzando la proprietà [ID sessione](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) nel messaggio). In questo modello, tutti i nodi dall'applicazione che tentano di flussi, anziché i messaggi. Dato un flusso a un nodo utilizzato, il nodo possibile esaminare lo stato di flusso dello stato dell'applicazione tramite le transazioni.

- La soluzione richiede comportamento transazione e atomicità durante l'invio o ricezione più messaggi da una coda.

- La caratteristica di time to live (TTL) del carico di lavoro specifici dell'applicazione può superare il periodo di 7 giorni.

- L'applicazione gestisce i messaggi che possono superare 64 KB ma si limitano approccio non probabile 256 KB.

- Gestire un requisito per fornire un modello di accesso basato sui ruoli code e diritti/autorizzazioni diverse per i mittenti e destinatari.

- Le dimensioni di coda non crescerà superiori a 80 GB.

- Si desidera utilizzare il protocollo di messaggistica AMQP 1.0 basate su standard. Per ulteriori informazioni su AMQP, vedere [Panoramica AMQP Bus del servizio](./service-bus-amqp-overview.md).

- Previsti un'eventuale la migrazione da comunicazioni basate su coda punto a un modello di exchange di messaggio che consente l'integrazione di ricevitori aggiuntive (abbonati), ognuno dei quali riceve copie indipendenti di alcuni o tutti i messaggi inviati alla coda. Il secondo si riferisce alla funzionalità di pubblicazione/sottoscrizione a livello nativo fornito da Bus di servizio.

- La soluzione di messaggistica deve essere in grado di supportare garanzia di recapito "Nella maggior parte a una sola volta" senza la necessità di creare i componenti aggiuntivi dell'infrastruttura.

- Si desidera essere in grado di pubblicare e utilizzare batch dei messaggi.

- Si richiede l'integrazione completa con stack di comunicazioni di Windows Communication Foundation (WCF) in .NET Framework.

## <a name="comparing-azure-queues-and-service-bus-queues"></a>Confronto tra code code Azure e Bus di servizio

Le tabelle nelle sezioni seguenti offrono un raggruppamento logico delle caratteristiche di coda e permettono di confrontare a colpo d'occhio, le funzionalità disponibili in sia code Azure e Bus di servizio.

## <a name="foundational-capabilities"></a>Funzionalità di base

In questa sezione contiene un confronto di alcune delle funzionalità di Accodamento fondamentali fornite da code code Azure e Bus di servizio.

|Criteri di confronto|Code Azure|Code Bus Service|
|---|---|---|
|Ordinamento garanzia|**No** <br/><br>Per ulteriori informazioni, vedere la nota prima nella sezione "Ulteriori informazioni".</br>|**Sì - First In First Out (FIFO)**<br/><br>(mediante l'utilizzo di sessioni di messaggistica)|
|Garanzia di recapito|**Volta al minimo**|**Volta al minimo**<br/><br/>**Volta nella maggior parte**|
|Supporto di operazione atomica|**No**|**Sì**<br/><br/>|
|Ricevere comportamento|**Il blocco non**<br/><br/>(viene completato immediatamente se non viene trovata alcuna nuovo messaggio)|**Blocco con/senza timeout**<br/><br/>(offerte long polling o ["Tecnica programma"](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Il blocco non**<br/><br/>(mediante l'utilizzo di .NET API gestita solo)|
|API push stile|**No**|**Sì**<br/><br/>[OnMessage](https://msdn.microsoft.com/library/azure/jj908682.aspx) e **OnMessage** sessioni API .NET.|
|Modalità di ricezione|**Anteprima e leasing**|**Anteprima e blocco**<br/><br/>**Ricevere ed Elimina**|
|Modalità di accesso esclusivo|**In base leasing**|**In base a blocco**|
|Leasing/blocco durata|**30 secondi (impostazione predefinita)**<br/><br/>**7 giorni (max)** (È possibile rinnovare o rilasciare leasing un messaggio usando [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) API.)|**60 secondi (impostazione predefinita)**<br/><br/>È possibile rinnovare un blocco di messaggi utilizzando [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) API.|
|Leasing/blocco precisione|**Livello di messaggio**<br/><br/>(ciascun messaggio può avere un valore di timeout diversi, è possibile aggiornare in base alle esigenze durante l'elaborazione del messaggio, utilizzando [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) API)|**Livello di coda**<br/><br/>(ogni coda con una precisione di blocco applicata a tutti i messaggi, ma è possibile rinnovare il blocco tramite [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) API).|
|In batch ricevere|**Sì**<br/><br/>(in modo esplicito specificando il conteggio dei messaggi quando il recupero dei messaggi, fino a un massimo di 32 messaggi)|**Sì**<br/><br/>(in modo implicito per consentire una proprietà di pre- recupero o in modo esplicito mediante l'utilizzo di transazioni)|
|Invia in batch|**No**|**Sì**<br/><br/>(mediante l'utilizzo di transazioni o sul lato client in batch)|

### <a name="additional-information"></a>Ulteriori informazioni

- I messaggi in code Azure sono in genere first-in-first-out, ma in alcuni casi può essere ordine; ad esempio, la durata del timeout di un messaggio visibilità scadenza (ad esempio, in seguito a un'applicazione client subisca un arresto anomalo durante l'elaborazione). Quando scade il timeout di visibilità, il messaggio diventa visibile nella coda di un altro lavoro per rimuoverlo. A questo punto, il messaggio appena visibile potrebbe essere inserito nella coda (a da rimuovere dalla coda nuovamente) dopo un messaggio originariamente Accodamento dopo tale voce.

- Se si sta già utilizzando lo spazio di archiviazione BLOB o tabelle e iniziare a usare code, si è certi 99,9% di disponibilità. Se si usa BLOB o tabelle con code Bus di servizio, sarà necessario disponibilità inferiore.

- Il modello FIFO garantito code Bus di servizio richiede l'uso di sessioni di messaggistica. Nel caso in cui l'applicazione determina l'arresto anomalo durante l'elaborazione di un messaggio ricevuto in modalità **anteprima e blocco** , la volta successiva che un ricevitore coda accetta una sessione di messaggistica, verrà avviata con il messaggio di errore dopo la scadenza del periodo time to live (TTL).

- Azure code sono progettate per supportare scenari Accodamento standard, ad esempio separazione componenti di applicazioni per aumentare la scalabilità e tolleranza per gli errori, caricare il livellamento e la creazione dei flussi di lavoro di processo.

- Code Bus di servizio di supporto garanzia di recapito *Una volta al minimo* . Inoltre, *Una volta nella maggior parte* semantic possono essere supportati con lo stato della sessione per archiviare lo stato dell'applicazione e utilizzando le transazioni in modo separato ricevano i messaggi e aggiornare lo stato della sessione.

- Azure code forniscono un modello di programmazione uniforme e coerenza in code, tabelle e BLOB: per gli sviluppatori e per team operativi.

- Code Service Bus forniscono supporto per le transazioni locali nel contesto di una sola coda.

- La modalità di **ricezione ed eliminare** supportata dal servizio Bus offre la possibilità di ridurre il conteggio delle operazioni messaggistica e costi associati, in cambio garanzia di consegna pedice.

- Azure code forniscono leasing la possibilità di estendere leasing per i messaggi. In questo modo colleghi di mantenere breve leasing ai messaggi. In questo modo, se si blocca un lavoro, il messaggio può essere rapidamente elaborato nuovamente lavoro un'altra. Inoltre, un lavoro estendere leasing su un messaggio se è necessario elaborarlo più tempo leasing corrente.

- Code Azure offrono un timeout visibilità che è possibile impostare sul può interfacciarsi o annullamento dell'accodamento di un messaggio. Inoltre, è possibile aggiornare un messaggio con valori di leasing diverso in fase di esecuzione e aggiornare i valori per diversi tra i messaggi nella stessa coda. Timeout di blocco Bus di servizio sono definiti nei metadati coda. Tuttavia, è possibile rinnovare il blocco utilizzando il metodo [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) .

- Il timeout massimo per un blocco di operazione di ricezione nelle code Bus di servizio è 24 giorni. Tuttavia, basate su REST timeout dispone di un valore massimo di 55 secondi.

- Sul lato client in batch fornite dal servizio Bus consente ai client coda in batch più messaggi in un'operazione di invio singola. Il batch è disponibile solo per le operazioni di invio asincrono.

- Caratteristiche quali ceiling TB 200 di Azure code (altro quando si virtualizzazione account) e un numero illimitato rendano una piattaforma ideale per i provider di SaaS.

- Code Azure offrono flessibile ed efficienti delegata meccanismo di controllo di accesso.

## <a name="advanced-capabilities"></a>Funzionalità avanzate

In questa sezione Confronta le funzionalità avanzate fornite da code code Azure e Bus di servizio.

|Criteri di confronto|Code Azure|Code Bus Service|
|---|---|---|
|Recapito pianificato|**Sì**|**Sì**|
|Lettere inattivi automatico|**No**|**Sì**|
|Aumento del valore time to live coda|**Sì**<br/><br/>(tramite aggiornamento sul posto di timeout di visibilità)|**Sì**<br/><br/>(fornito tramite una funzione API dedicata)|
|Danneggiare supporto dei messaggi|**Sì**|**Sì**|
|Aggiornamento sul posto|**Sì**|**Sì**|
|Registro delle transazioni sul lato server|**Sì**|**No**|
|Metriche di archiviazione|**Sì**<br/><br/>**Minuto metriche**: fornisce metriche in tempo reale per API di disponibilità, indica, chiamano conteggi, conteggi di errori e altro ancora, tutto in tempo reale (aggregati al minuto e segnalati entro pochi minuti da nell'ambiente di produzione. Per ulteriori informazioni, vedere [Sull'archiviazione Analitica metriche](https://msdn.microsoft.com/library/azure/hh343258.aspx).|**Sì**<br/><br/>(query in blocco chiamando [GetQueues](https://msdn.microsoft.com/library/azure/hh293128.aspx))|
|Gestione dello stato|**No**|**Sì**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx)|
|Inoltro automatico del messaggio|**No**|**Sì**|
|Elimina funzione coda|**Sì**|**No**|
|Gruppi di messaggi|**No**|**Sì**<br/><br/>(mediante l'utilizzo di sessioni di messaggistica)|
|Stato dell'applicazione per ogni gruppo di messaggi|**No**|**Sì**|
|Rilevamento duplicati|**No**|**Sì**<br/><br/>(configurabili sul lato mittente)|
|Integrazione di WCF|**No**|**Sì**<br/><br/>(offre le associazioni WCF della casella)|
|Integrazione di WF|**Personalizzato**<br/><br/>(richiede la creazione di un'attività WF personalizzata)|**Nativo**<br/><br/>(offre attività WF della casella)|
|Esplorazione di gruppi di messaggi|**No**|**Sì**|
|Recupero di sessioni di messaggistica per ID|**No**|**Sì**|

### <a name="additional-information"></a>Ulteriori informazioni

- Entrambe le tecnologie Accodamento abilitare un messaggio per la pianificazione di recapito in un secondo momento.

- Inoltro automatico coda consente a migliaia di code inoltro automatico dei messaggi a una coda singola, dalla quale applicazione di destinazione utilizza il messaggio. È possibile usare questo meccanismo per garantire la protezione, controllare il flusso e isolare lo spazio di archiviazione tra publisher ogni messaggio.

- Code Azure forniscono supporto per l'aggiornamento di contenuto del messaggio. È possibile utilizzare questa funzionalità per mantenere le informazioni sullo stato e gli aggiornamenti di stato di avanzamento incrementale nel messaggio in modo che possa essere elaborato dal punto di ultima noto arresto, anziché iniziare da zero. Con le code Bus di servizio, è possibile abilitare lo stesso scenario mediante l'utilizzo di sessioni di messaggistica. Sessioni consentono di salvare e recuperare lo stato di elaborazione dell'applicazione (mediante [SetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx) e [GetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx)).

- [Inattivo lettere](service-bus-dead-letter-queues.md), che è supportata solo da code Bus di servizio, può essere utile per isolare i messaggi che non può essere eseguiti dall'applicazione di destinazione o quando i messaggi non raggiungano la destinazione a causa di scaduto time to live (TTL) proprietà. Il valore TTL consente di specificare quanto tempo rimane nella coda di un messaggio. Con Bus di servizio, il messaggio verrà spostato a una coda speciale chiamata $DeadLetterQueue quando scade il periodo di TTL.

- Per trovare i messaggi "danneggiati" code di Azure, quando un messaggio di annullamento dell'accodamento l'applicazione esamina la proprietà **[DequeueCount](https://msdn.microsoft.com/library/azure/dd179474.aspx)** del messaggio. Se **DequeueCount** supera una determinata soglia, l'applicazione sposta il messaggio a una coda definita dall'applicazione "lettera inattiva".

- Code Azure consentono di ottenere un registro dettagliato di tutte le operazioni eseguite coda, come metriche anche come aggregate. Entrambe le opzioni sono utili per il debug e informazioni sulle modalità di utilizzo Azure code dell'applicazione. Sono inoltre utili per ottimizzare le prestazioni dell'applicazione e per ridurre i costi di utilizzo delle code.

- Il concetto di "sessioni" supportati dal servizio Bus Abilita i messaggi che appartengono a un determinato gruppo logico essere associati a un destinatario specificato, che a sua volta crea un'affinità mi piace sessione tra i messaggi e i rispettivi ricevitori. È possibile abilitare questa funzionalità avanzate di servizio Bus impostando la proprietà [ID sessione](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) in un messaggio. Ricevitori quindi ascolto un ID sessione specifici e ricevere messaggi che condividono l'identificatore di sessione specificato.

- La funzionalità di rilevamento duplicazione supportata da code Bus di servizio automatico rimuove duplicati messaggi inviati a una coda o un argomento, in base al valore della proprietà [ID messaggio](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) .

## <a name="capacity-and-quotas"></a>Capacità e le quote

In questa sezione Confronta code code Azure e Bus di servizio dal punto di vista della [capacità e le quote](service-bus-quotas.md) che potrebbero essere valide.

|Criteri di confronto|Code Azure|Code Bus Service|
|---|---|---|
|Dimensione massima della coda|**200 TB**<br/><br/>(limitato a una singolo capacità di account)|**Almeno 1 GB a 80 GB**<br/><br/>(definito durante la creazione di una coda e [attivazione partizione](service-bus-partitioning.md) : vedere la sezione "Ulteriori informazioni")|
|Dimensione massima dei messaggi|**64 KB**<br/><br/>(48 KB quando si utilizza la codifica **base 64** )<br/><br/>Azure supporta i messaggi di grandi dimensioni combinando code e BLOB: a quel punto è possibile accodare un massimo di 200GB per un singolo elemento.|**256 KB** o **MB 1**<br/><br/>(inclusi intestazione e corpo, dimensione massima di intestazione: 64 KB).<br/><br/>Dipende dal [livello di servizio](service-bus-premium-messaging.md).|
|Numero massimo messaggi TTL|**7 giorni**|**`TimeSpan.Max`**|
|Numero massimo di code|**Illimitato**|**10.000**<br/><br/>(per ogni servizio dello spazio dei nomi, è possibile aumentare)|
|Numero massimo di client simultanee|**Illimitato**|**Illimitato**<br/><br/>(il limite di connessioni simultanee 100 solo per la comunicazione basata su protocollo TCP)|

### <a name="additional-information"></a>Ulteriori informazioni

- Servizio Bus impone limiti di dimensioni coda. Le dimensioni massime coda specificata al momento della creazione della coda e possono avere un valore compreso tra 1 e 80 GB. Se il valore di dimensione coda impostato al momento della creazione della coda viene raggiunta, verranno rifiutati altri messaggi in arrivo e un'eccezione verrà ricevuta dal codice chiamante. Per ulteriori informazioni sugli obiettivi di Bus di servizio, vedere [Le quote Bus di servizio](service-bus-quotas.md).

- È possibile creare code Bus di servizio dimensioni 1, 2, 3, 4 o 5 GB (il valore predefinito è 1 GB). Con partizione attivata (ovvero l'impostazione predefinita), Bus di servizio crea 16 partizioni per ogni GB specificato. Pertanto, se si crea una coda a 5 GB dimensioni, con 16 partizioni le dimensioni massime coda diventano (5 * 16) = 80 GB. È possibile visualizzare la dimensione massima del partizionata coda o argomento controllando in entrata nel [portale di Azure][].

- Con le code di Azure, se il contenuto del messaggio non è sicuro per XML, quindi deve essere codificata **base 64** . Se si **base 64**-codificare il messaggio, payload utente possono essere fino a 48 KB anziché 64 KB.

- Con le code Bus di servizio, ogni messaggio archiviato in una coda è costituito da due parti: un'intestazione e un corpo. Le dimensioni totali del messaggio non possono superare la dimensione massima dei messaggi è supportata dal livello di servizio.

- Quando i clienti a comunicare con code Bus di servizio tramite il protocollo TCP, il numero massimo di connessioni simultanee a una coda di servizio Bus singola è limitato a 100. Questo numero verrà condivise tra mittenti e destinatari. Se si raggiunge la quota, verranno rifiutate le successive richieste per le connessioni aggiuntive e un'eccezione verrà ricevuta dal codice chiamante. Non limitazione delle client che si connettono a code utilizzando API basata su resto.

- Se si richiedono più di 10.000 code in un unico spazio dei nomi Bus di servizio, è possibile contattare il team di supporto Azure e richiedere un aumento. Per ridimensionare oltre 10.000 code con Bus di servizio, è anche possibile creare ulteriori spazi dei nomi tramite il [portale di Azure][].

## <a name="management-and-operations"></a>Gestione e operazioni

In questa sezione Confronta le funzionalità di gestione di code code Azure e Bus di servizio.

|Criteri di confronto|Code Azure|Code Bus Service|
|---|---|---|
|Protocollo di gestione|**Posiziona HTTP/HTTPS**|**Posiziona HTTPS**|
|Protocollo Runtime|**Posiziona HTTP/HTTPS**|**Posiziona HTTPS**<br/><br/>**AMQP 1.0 Standard (TCP con TLS)**|
|API gestita di .NET|**Sì**<br/><br/>(Spazio di archiviazione Client API gestito .NET)|**Sì**<br/><br/>(Gestito .NET negoziate API di messaggistica)|
|C++ nativo|**Sì**|**No**|
|API Java|**Sì**|**Sì**|
|API PHP|**Sì**|**Sì**|
|Node API|**Sì**|**Sì**|
|Supporto di metadati non autorizzato|**Sì**|**No**|
|Regole di denominazione coda|**Massimo di 63 caratteri**<br/><br/>(Le lettere di un nome di coda devono essere minuscole).|**Fino a 260 caratteri**<br/><br/>I nomi e i percorsi di coda sono distinzione tra maiuscole e.|
|Funzione lunghezza coda Get|**Sì**<br/><br/>(Valore approssimato se i messaggi scadono oltre il valore TTL senza da eliminare.)|**Sì**<br/><br/>(Valore esatto, nel momento).|
|Funzione di anteprima|**Sì**|**Sì**|

### <a name="additional-information"></a>Ulteriori informazioni

- Code Azure forniscono supporto per gli attributi non autorizzati che possono essere applicati alla descrizione coda nel modulo coppie nome/valore.

- Entrambe le tecnologie coda offrono la possibilità di leggere un messaggio senza dover bloccarla, che può essere utile per l'implementazione di uno strumento di Esplora risorse/browser coda.

- Servizio Bus .NET negoziate messaggistica API fattore bidirezionale connessioni TCP per migliorare le prestazioni rispetto al resto su HTTP e supporta il protocollo standard AMQP 1.0.

- Nomi di Azure code può essere lungo 3 63 caratteri, può contenere lettere, numeri e trattini. Per ulteriori informazioni, vedere [denominazione code e i metadati](https://msdn.microsoft.com/library/azure/dd179349.aspx).

- I nomi delle code Bus di servizio possono essere fino a 260 caratteri e disporre le regole di denominazione meno restrittive. Servizio Bus coda nomi possono contenere lettere, numeri, punti, trattini e caratteri di sottolineatura.

## <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

In questa sezione vengono illustrate le funzionalità di autenticazione e l'autorizzazione supportate da code code Azure e Bus di servizio.

|Criteri di confronto|Code Azure|Code Bus Service|
|---|---|---|
|Autenticazione|**Chiave simmetrico**|**Chiave simmetrico**|
|Modello di sicurezza|Delega per l'accesso tramite i token SA.|ASSOCIAZIONI DI PROTEZIONE|
|Federazione di provider di identità|**No**|**Sì**|

### <a name="additional-information"></a>Ulteriori informazioni

- Ogni richiesta a una delle tecnologie Accodamento deve essere autenticato. Non sono supportate le code pubbliche con accesso anonimo. Usa [sa](service-bus-sas-overview.md), è possibile risolvere questo scenario pubblicando un SA in sola lettura, sa di sola lettura o anche un SA accesso completo.

- Lo schema di autenticazione fornito da Azure code comporta l'utilizzo di una chiave simmetrica, ossia un HMAC messaggio autenticazione codice HMAC (), calcolate con algoritmo SHA-256 e codificata come una stringa di **base 64** . Per ulteriori informazioni sul protocollo rispettivo, vedere [l'autenticazione per i servizi di archiviazione Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx). Le code Bus di servizio supportano un modello simile tasti simmetrico. Per ulteriori informazioni, vedere [Autenticazione tramite firma accesso condiviso con Bus di servizio](service-bus-shared-access-signature-authentication.md).

## <a name="cost"></a>Costo

In questa sezione Confronta code code Azure e Bus di servizio dal punto di vista costo.

|Criteri di confronto|Code Azure|Code Bus Service|
|---|---|---|
|Costo transazione coda|**$0.0036**<br/><br/>(per le 100.000 transazioni)|**Livello di base**: **$0,05**<br/><br/>(per le milioni operazioni)|
|Operazioni fatturabili|**Tutti**|**Invio/ricezione solo**<br/><br/>(Nessun addebito per altre operazioni)|
|Transazioni inattive|**Fatturabili**<br/><br/>(La ricerca a una coda vuota viene conteggiata come transazione fatturabile.)|**Fatturabili**<br/><br/>(Ricezione rispetto a una coda vuota è considerata un messaggio fatturabile).|
|Costo di archiviazione|**$0,07**<br/><br/>(per GB al mese)|**$0,00**|
|Costi di trasferimento di dati in uscita|**$0,12-0.19**<br/><br/>(A seconda del tipo geography.)|**$0,12-0.19**<br/><br/>(A seconda del tipo geography.)|

### <a name="additional-information"></a>Ulteriori informazioni

- Trasferimenti di dati vengono addebitati in base alla quantità totale di dati lasciando i Data Center di Azure tramite internet in un determinato periodo di fatturazione.

- Trasferimenti di dati tra servizi Azure che si trovano all'interno dell'area stessa non sono soggetti a spese.

- In questo modo, tutti i trasferimenti di dati in ingresso non sono soggetti a spese.

- Dato il supporto di polling lungo, utilizzo code Bus di servizio può essere conveniente in situazioni in cui è richiesto il recapito di bassa latenza.

>[AZURE.NOTE] Tutti i costi sono soggetti a modifiche. In questa tabella rispecchia il prezzo corrente e non include le offerte che possono essere attualmente disponibili. Per informazioni aggiornate sui prezzi di Azure, vedere la pagina [Azure prezzi](https://azure.microsoft.com/pricing/) . Per ulteriori informazioni sui prezzi Bus di servizio, vedere [servizio Bus prezzi](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="conclusion"></a>Conclusioni

Ottenere una maggiore comprensione delle due tecnologie, sarà possibile prendere una decisione più consapevole tecnologia coda da utilizzare e il momento. La decisione su quando utilizzare code Azure o servizi Bus code chiaramente dipende da diversi fattori. Questi fattori possono strette alle proprie esigenze dell'applicazione e la relativa architettura. Se l'applicazione utilizza già funzionalità principali di Microsoft Azure, è preferibile scegliere code Azure, soprattutto se si richiede la comunicazione di base e la messaggistica tra servizi o code necessità che possono essere maggiore di 80 GB dimensioni.

Poiché code Bus di servizio offrono numerose caratteristiche avanzate, ad esempio le sessioni, le transazioni, duplicare rilevamento automatico inattivi lettere e permanenti pubblicazione/sottoscrizione funzionalità possono essere scelta preferita se si sta creando un'applicazione ibrida o se l'applicazione in caso contrario richiede queste caratteristiche.

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti offrono altre indicazioni e informazioni sull'utilizzo di code Azure o servizi Bus code.

- [Come usare servizio Bus code](service-bus-dotnet-get-started-with-queues.md)
- [Come utilizzare il servizio di archiviazione coda](../storage/storage-dotnet-how-to-use-queues.md)
- [Procedure consigliate per miglioramenti delle prestazioni con Bus di servizio negoziate messaggistica](service-bus-performance-improvements.md)
- [Introduzione a code e degli argomenti Bus di servizio Azure](http://www.code-magazine.com/article.aspx?quickid=1112041)
- [Guida per gli sviluppatori per Bus di servizio](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
- [Mediante il servizio di Accodamento in Azure](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)
- [Informazioni sulla fatturazione di archiviazione Azure – della larghezza di banda, le transazioni e capacità](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

[Portale di Azure]: https://portal.azure.com
 
