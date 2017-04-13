<properties
   pageTitle="Panoramica del modello di programmazione affidabile servizio tessuti | Microsoft Azure"
   description="Informazioni sul modello di programmazione Service affidabile dell'infrastruttura di servizio e iniziare a scrivere il proprio provider di servizi."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="vturecek; mani-ramaswamy"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="masnider;vturecek"/>

# <a name="reliable-services-overview"></a>Cenni preliminari sui servizi affidabile
Azure tessuti servizio semplifica la scrittura e la gestione dei servizi affidabile e senza stato. Verrà illustrate in questo documento:

- Il modello di programmazione affidabile servizi per i servizi e senza stati.
- Le opzioni che è necessario effettuare durante la scrittura di un servizio affidabile.
- Alcuni esempi di quando utilizzare servizi affidabili e modalità di scrittura e scenari.

Servizi affidabili corrisponde a uno dei modelli di programmazione disponibili nel servizio tessuti. Per ulteriori informazioni sul modello di programmazione affidabile operatori, vedere [Introduzione al servizio tessuti affidabile operatori](service-fabric-reliable-actors-introduction.md).

Nel servizio tessuti, un servizio è composto da della configurazione, il codice dell'applicazione e, facoltativamente, provincia.

Servizio tessuti gestisce la durata dei servizi, da il provisioning e distribuzione tramite l'aggiornamento e l'eliminazione, tramite [la gestione delle applicazioni di servizio tessuti](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Informazioni sui servizi affidabili?
Servizi affidabili offre un modello di programmazione semplice ed efficace principale che consentono di esprimere attività più importanti per l'applicazione. Con i servizi affidabile modello di programmazione, è possibile:

- Per i servizi di informazioni sullo stato, il modello di programmazione affidabile servizi consente di in modo coerente e affidabile archiviare il diritto di stato all'interno del servizio tramite raccolte affidabile. Si tratta di una serie di classi di insiemi disponibilità nota a chiunque abbia utilizzato c# raccolte semplici. In genere, i servizi necessari sistemi esterni per la gestione dello stato affidabile. Con le raccolte affidabile, è possibile archiviare lo stato accanto al calcolo con la stessa disponibilità e l'affidabilità che è stata ormai si aspettano da disponibili archivi esterni e con i miglioramenti latenza aggiuntivi che forniscono condivisa individuazione dell'elaborazione e dello stato.

- Un semplice modello per l'esecuzione di codice si presenta come vengono usate per modelli di programmazione. Il codice ha un punto di ingresso ben definiti e facilmente gestione ciclo di vita.

- Un modello di comunicazione collegabili. Utilizzare il trasporto preferito, ad esempio HTTP con [API Web](service-fabric-reliable-services-communication-webapi.md), WebSockets, protocolli TCP personalizzati e così via. Servizi affidabili offrono alcune interessanti di opzioni è possibile utilizzare oppure è possibile fornire.

## <a name="what-makes-reliable-services-different"></a>Che cosa rende diversi servizi affidabili?
Servizi affidabili in tessuti servizio è la differenza tra servizi scritto prima. Servizio tessuti offre l'affidabilità, disponibilità, la coerenza e scalabilità.  

- **L'affidabilità**, ossia il servizio rimarranno backup anche in ambienti origini non affidabili nel punto in cui i computer potrebbero non funzionare oppure premere problemi di rete.

- **Disponibilità**, ossia il servizio sarà raggiungibile e tempestivo. (Non le implicazioni che non può contenere servizi che non sono stati trovati o raggiunto dall'esterno.)

- **Scalabilità**- servizi sono separate da hardware specifico appropriati e possono Ingrandisci o Riduci in base alle esigenze mediante l'aggiunta o rimozione dell'hardware o risorse virtuali. Servizi facilmente sono suddivisi, in particolare in caso informazioni sullo stato, per garantire che indipendente parti del servizio possano ridimensionare e rispondere agli errori in modo indipendente. Infine, tessuti servizio incoraggino servizi da leggere e consente di migliaia di servizi di effettuare il provisioning in un unico processo, invece di richiedere o dedicare intere istanze del sistema operativo a una singola istanza particolari carichi di lavoro.

- **La coerenza**, ossia tutte le informazioni memorizzate in questo servizio può sicuramente coerente (si applica solo ai servizi informazioni sullo stato - informazioni su questo in un secondo momento)

## <a name="service-lifecycle"></a>Ciclo di vita del servizio
Se il servizio è stato o senza informazioni sullo stato, servizi affidabili offrono un ciclo di vita semplice che consente di inserire rapidamente nel codice e iniziare a.  Sono disponibili solo uno o due metodi che è necessario implementare per ottenere il servizio funzionanti.

- **CreateServiceReplicaListeners/CreateServiceInstanceListeners** - si tratta in cui il servizio definisce stack di comunicazioni che si desidera utilizzare. Stack di comunicazione, ad esempio [API Web](service-fabric-reliable-services-communication-webapi.md), è quello definito gli endpoint del servizio (come client verranno raggiungerlo) o un endpoint in ascolto. Definisce inoltre come i messaggi visualizzati finiscono per interagire con il resto del codice del servizio.

- **RunAsync** - si tratta in cui il servizio viene eseguita la logica business. Il token di annullamento fornito è un segnale per quando è consigliabile interrompere compatibili. Ad esempio, se si dispone di un servizio che è necessario estrarre i messaggi dalla coda affidabile e la loro elaborazione continuamente, è nel punto in cui si è accadrebbe compatibili.

### <a name="service-startup"></a>Avvio del servizio

Gli eventi importanti nel ciclo di vita di un servizio affidabile sono:

1. Sia impostata l'oggetto di servizio (l'aspetto che deriva dal servizio senza informazioni sullo stato o informazioni sullo stato).

2. Il `CreateServiceReplicaListeners` / `CreateServiceInstanceListeners` viene chiamato, consentirà al servizio di restituire uno o più listener comunicazioni di propria scelta.
  - Si noti che questa è facoltativa, anche se la maggior parte dei servizi verranno esposto alcuni endpoint direttamente.

3. Dopo aver creato il listener di comunicazione, viene aperto.
  - Comunicazione listener abbiano un metodo denominato `OpenAsync()`, vale a dire a questo punto e che restituisce l'indirizzo di attesa per il servizio. Se il servizio affidabile utilizza uno dei ICommunicationListeners predefiniti, questo viene gestito automaticamente.

4. Dopo avere aperto, comunicare ascoltatore comunicazioni di `RunAsync()` chiamato il servizio principale.
  - Si noti che `RunAsync()` è facoltativo. Se il servizio esegue tutto il proprio lavoro direttamente in risposta all'utente solo chiamate, non è necessario per poter implementare `RunAsync()`.

### <a name="service-shutdown"></a>Chiusura del servizio

Quando il servizio arrestato (per essere eliminato, aggiornato o spostato) l'ordine di chiamata viene riflesso: prima di tutto il token di annullamento contenute in `RunAsync()` viene annullato; quindi `CloseAsync()` denominata nel listener di comunicazione.

Esistono alcuni aspetti importanti da tenere presente per arresto per i servizi di informazioni sullo stato:

- Servizio tessuti non promuove un'altra replica del servizio allo stato principale fino a `CloseAsync` e `RunAsync` ha restituito. Se si utilizza un comunicare ascoltatore comunicazione integrate, il `CloseAsync` metodo viene gestito automaticamente.

- Mentre nel restituzione da questi metodi non è presente alcun limite di tempo, immediatamente perdere la possibilità di scrittura per le raccolte affidabile e pertanto non è possibile completare tutte le operazioni reali. È consigliabile reimpostare il più presto possibile dopo aver ricevuto la richiesta di annullamento.

## <a name="example-services"></a>Servizi di esempio
Si conosce questo modello di programmazione, esaminiamo un rapido due diversi servizi per visualizzare in che modo questo insieme.

### <a name="stateless-reliable-services"></a>Servizi affidabili senza informazioni sullo stato
Un servizio senza informazioni sullo stato è una posizione in cui non è in forma letterale stati gestito all'interno del servizio o lo stato presenza è completamente disponibile e non richiede la sincronizzazione, replica, persistenza o disponibilità.

Si consideri ad esempio una calcolatrice che non ha memoria e riceve tutti i termini e sulle operazioni da eseguire in una sola volta.

In questo caso, RunAsync() del servizio può essere vuoto, poiché non esiste alcun sfondo-elaborazione dell'attività che il servizio deve eseguire. Quando viene creato il servizio di calcolo, verrà restituito un ICommunicationListener (ad esempio l' [API Web](service-fabric-reliable-services-communication-webapi.md)) che consente di aprire un endpoint in ascolto su una porta. Questo endpoint in ascolto verranno collegare i vari metodi (esempio: "Aggiungi (n1, n2)") che definiscono API pubblica della calcolatrice.

Quando si effettua una chiamata da un client, viene richiamato il metodo appropriato e il servizio di calcolatrice esegue le operazioni su dati e restituisce il risultato. Essa non archiviazione uno stato.

Non archiviare uno stato interno rende questo strumento di calcolo di esempio molto semplice. Ma la maggior parte dei servizi non sono realmente senza stati. Se, tuttavia, si Esternalizza lo stato su un altro archivio. (Ad esempio, qualsiasi applicazione web che si basa su come mantenere lo stato della sessione in un archivio di backup o cache non è completamente privo di stato.)

Un tipico esempio di come servizi senza informazioni sullo stato vengono usati tessuti servizio è possibile eseguire un front-end esposti API pubblico per un'applicazione web. Il servizio front-end parla quindi ai servizi informazioni sullo stato per completare una richiesta dell'utente. In questo caso, le chiamate dai client vengono richiesto di una porta nota, ad esempio 80, in cui il servizio senza informazioni sullo stato è in attesa. Questo servizio senza informazioni sullo stato riceve la chiamata e determina se la chiamata da una fonte attendibile, come nonché il servizio è destinato a.  Il servizio senza informazioni sullo stato quindi inoltra la chiamata a una partizione corretta del servizio informazioni sullo stato e attesa di una risposta. Quando il servizio senza informazioni sullo stato riceve una risposta, risponde al client originale.

### <a name="stateful-reliable-services"></a>Informazioni sullo stato servizi affidabili
Un servizio informazioni sullo stato è quello che deve avere una parte dello stato di permanenza coerenti e presenta l'ordine per il servizio alla funzione. Valutare la possibilità di un servizio che costantemente consente di calcolare una media mobile di un valore in base agli aggiornamenti che riceve. A tale scopo, devono essere la serie corrente di richieste in arrivo, che è necessario processo, nonché la media corrente. Qualsiasi servizio che recupera, elabora e memorizza le informazioni in un archivio esterno (ad esempio un Azure blob o della tabella archivio oggi) è stato. Mantiene solo il relativo stato nell'archivio dello stato esterno.

La maggior parte dei servizi archiviano oggi il proprio stato esternamente, poiché l'archivio esterno quali fornisce affidabilità, disponibilità, scalabilità e la coerenza per tale stato. Nel servizio tessuti, servizi di informazioni sullo stato non sono necessari per memorizzare il proprio stato esternamente. Servizio tessuti si occupa di tali requisiti per il codice di servizio e lo stato del servizio.

Supponiamo che si desidera scrivere un servizio che riceve le richieste per una serie di conversioni che devono essere eseguite su un'immagine e l'immagine che deve essere convertito.  Per questo servizio restituirà (supponga API Web) comunicare ascoltatore comunicazioni che apre la porta di comunicazione e consente ad esempio invii tramite un'API `ConvertImage(Image i, IList<Conversion> conversions)`. In questa API il servizio potrebbe richiedere le informazioni e archiviare la richiesta nella coda affidabile e tornare alcuni token al client in modo che potrebbe tenere traccia della richiesta (poiché le richieste potrebbero richiedere alcuni minuti).

In questo servizio RunAsync potrebbe essere più complessi. Il servizio ha un ciclo all'interno il RunAsync che recupera le richieste di IReliableQueue, esegue le conversioni elencate e memorizzare i risultati in IReliableDictionary, in modo che quando si torna al client, che possono ottenere le immagini convertite. Per assicurare che anche se si ha esito negativo dell'immagine non viene perso, questo servizio affidabile da estrarre dalla coda, eseguire le conversioni e archiviare il risultato di una transazione. In questo caso, il messaggio viene effettivamente rimosso solo dalla coda e i risultati vengono memorizzati nel dizionario risultato dopo aver complete le conversioni. Se un elemento non riesce al centro (ad esempio il computer che è in esecuzione questa istanza del codice), la richiesta rimane nella coda in attesa di elaborazione nuovamente.

Una nota su questo servizio consiste può sembrare un servizio .NET normale. L'unica differenza è che le strutture di dati in uso (IReliableQueue e IReliableDictionary) vengono fornite da tessuti di servizio e pertanto effettuate altamente affidabile, disponibile e coerente.

## <a name="when-to-use-reliable-services-apis"></a>Quando utilizzare affidabile API dei servizi
Se una delle operazioni seguenti caratterizzare le esigenze di servizio dell'applicazione, è necessario considerare attendibile API dei servizi:

- È necessario garantire il comportamento dell'applicazione in più unità di stato (ad esempio ordini e articoli).

- Stato dell'applicazione possibile modellare naturalmente come dizionari affidabile e code.

- Lo stato deve essere disponibilità con access bassa latenza.

- L'applicazione deve controllare la concorrenza o granularità operazioni transazione tra una o più raccolte affidabile.

- Si desidera gestire le comunicazioni o controllare la combinazione di partizione per il servizio.

- Il codice necessita di un ambiente di runtime thread libero.

- L'applicazione deve dinamicamente creare o eliminare dizionari affidabili o code in fase di esecuzione.

- È necessario a livello di programmazione controllare forniti tessuti servizio di backup e ripristinare le funzionalità per lo stato * del servizio.

- L'applicazione deve gestire cronologia delle modifiche per le unità di stato *.

- Si desidera sviluppare o utilizzare i provider di stato personalizzato con terze parti sviluppate *.

> [AZURE.NOTE] * Caratteristiche disponibili in disponibilità generale SDK.


## <a name="next-steps"></a>Passaggi successivi
+ [Guida introduttiva a servizi affidabile](service-fabric-reliable-services-quick-start.md)
+ [Servizi affidabili utilizzo avanzato](service-fabric-reliable-services-advanced-usage.md)
+ [Il modello di programmazione affidabile operatori](service-fabric-reliable-actors-introduction.md)
