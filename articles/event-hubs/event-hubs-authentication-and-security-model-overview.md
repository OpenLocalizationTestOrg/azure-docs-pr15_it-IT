<properties 
    pageTitle="Panoramica del modello di autenticazione e la sicurezza hub evento | Microsoft Azure"
    description="Evento hub l'autenticazione e la sicurezza Cenni preliminari sul modello."
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
    ms.date="08/16/2016"
    ms.author="sethm;clemensv" />

# <a name="event-hubs-authentication-and-security-model-overview"></a>Panoramica del modello l'autenticazione e la sicurezza evento hub

Il modello di sicurezza evento hub soddisfa i requisiti seguenti:

- Solo i dispositivi che presentano credenziali valide possono inviare dati a un Hub di evento.
- Un dispositivo non è possibile rappresentare un altro dispositivo.
- Un dispositivo non autorizzato può essere bloccato da inviare dati a un Hub di evento.

## <a name="device-authentication"></a>Autenticazione dei dispositivi

Il modello di sicurezza hub evento si basa su una combinazione di token [Condiviso accesso firma (SA)](../service-bus-messaging/service-bus-shared-access-signature-authentication.md) e *gli autori di eventi*. Un autore di eventi definisce un endpoint virtuale per un Hub di evento. Impostazioni di pubblicazione utilizzabili solo per inviare messaggi a un Hub di evento. Non è possibile ricevere messaggi da un autore.

In genere, un Hub evento utilizza un editore per dispositivo. Tutti i messaggi inviati a uno qualsiasi di autori per un Hub di evento sono in coda all'interno di tale Hub evento. Autori abilitare il controllo dell'accesso diffusamente e limitazione.

Ogni dispositivo viene assegnato un token univoco, caricato nel dispositivo. I token vengono prodotti in modo che ogni token univoco concede l'accesso a un autore univoco diverso. Un dispositivo che possiede un token può essere inviate solo a un editore, ma non altri publisher. Se più dispositivi condividono lo stesso token, ognuno di questi dispositivi condivide un autore.

Sebbene non sia consigliato, è possibile dotare dispositivi con i token emessi concedere l'accesso diretto a un Hub di evento. Qualsiasi dispositivo che contiene questo token può inviare messaggi direttamente a tale Hub evento. Ad esempio un dispositivo non sarà soggetto a limitazione. Inoltre, il dispositivo non può essere disattivato da inviare a tale Hub evento.

Tutti i token sono firmati con una chiave SA. In genere, tutti i token sono firmati con la stessa chiave. Dispositivi non sono a conoscenza della chiave; In questo modo dispositivi token di fabbricazione.

### <a name="create-the-sas-key"></a>Creare la chiave SA

Quando si crea un evento hub spazio dei nomi, Azure evento hub genera una chiave di SA 256 bit denominata **RootManageSharedAccessKey**. Presente concede chiave inviare, ascoltare e gestione di diritti per lo spazio dei nomi. È possibile creare ulteriori tasti. Si consiglia di generare una chiave che concede autorizzazioni Invia all'hub di evento specifico. Per il resto di questo argomento, verrà considerato uguale denominato questo tasto `EventHubSendKey`.

Nell'esempio seguente crea una chiave Invia solo quando si creano Hub evento:

```
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event Hub with a SAS rule that enables sending to that Event Hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Generare i token

È possibile generare i token utilizzando la chiave SA. È necessario creare solo un token per dispositivo. È possibile produrre token quindi utilizzando il metodo seguente. Tutti i token vengono generati mediante la chiave **EventHubSendKey** . Ogni token viene assegnato un URI univoco.

```
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Quando si chiama questo metodo, l'URI deve essere specificato come `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Per tutti i token è identica, ad eccezione di URI `PUBLISHER_NAME`, che deve essere diverso per ogni token. Se possibile, `PUBLISHER_NAME` rappresenta l'ID del dispositivo che riceve il token.

Questo metodo genera un token con la struttura seguente:

```
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

Ora di scadenza token è espresso in secondi da 1 gennaio 1970. Di seguito è illustrato un esempio di un token:

```
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

In genere i token hanno un ciclo di vita che è simile a questo o supera la durata del dispositivo. Se il dispositivo è in grado di ottenere un nuovo token, è possono utilizzare i token con una durata più breve.

### <a name="devices-sending-data"></a>Dispositivi invio dei dati

Dopo avere creati i token, viene eseguito il provisioning di tutti i dispositivi con il proprio token univoco.

Quando il dispositivo invia dati a un Hub di evento, il dispositivo tag il relativo token con la richiesta di invio. Per evitare che un pirata informatico intercettazione e sottraendo il token, le comunicazioni tra il dispositivo e Hub evento devono essere compresa su un canale crittografato.

### <a name="blacklisting-devices"></a>Dispositivi di bloccaggio

In caso di furto un token da un utente non autorizzato, il pirata informatico può rappresentare il dispositivo furto di cui token. Inizializzazione della disabilitazione di un dispositivo inutilizzabile il dispositivo fino a quando il dispositivo viene assegnato un nuovo token che utilizza un autore diverso.

## <a name="authentication-of-back-end-applications"></a>Autenticazione di applicazioni back-end

Per eseguire l'autenticazione applicazioni back-end che utilizzano dati generati da dispositivi, evento hub utilizza un modello di sicurezza che è simile al modello usato per argomenti Bus di servizio. Un gruppo di consumer hub evento corrisponde a un abbonamento a un argomento Bus di servizio. Un cliente può creare un gruppo di consumer se la richiesta per creare il gruppo consumer è accompagnata da un token che concede la gestione dei privilegi per l'Hub di evento o per lo spazio dei nomi a cui appartiene l'Hub di evento. Un client è autorizzato a utilizzare i dati da un gruppo di consumer se la richiesta di ricezione è accompagnata da un token che concede i diritti di ricezione in tale gruppo consumer, Hub evento o lo spazio dei nomi a cui appartiene l'Hub di evento.

La versione corrente del servizio Bus non supporta le regole SA per gli abbonamenti singoli. Lo stesso vale per i gruppi di consumer hub evento. Verrà aggiunto il supporto di SA per entrambe le caratteristiche in futuro.

In assenza di autenticazione SA per i gruppi di singoli consumatori, è possibile usare i tasti di SA per proteggere tutti i gruppi di consumer con una chiave comune. In questo modo un'applicazione di utilizzare dati da uno qualsiasi dei gruppi consumer di un Hub di evento.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'evento hub, visitare i seguenti argomenti:

- [Cenni preliminari sui hub di eventi]
- Un' [coda soluzione di messaggistica] mediante code Bus di servizio.
- Un' [applicazione di esempio che utilizza evento hub]completa.

[Cenni preliminari sui hub di eventi]: event-hubs-overview.md
[applicazione di esempio che utilizza hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[in coda soluzione di messaggistica]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 
