<properties
 pageTitle="Guida per sviluppatori - controllare l'accesso a IoT Hub | Microsoft Azure"
 description="Guida per sviluppatori di Azure IoT Hub - come controllare l'accesso a IoT Hub e gestire la sicurezza"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="control-access-to-iot-hub"></a>Controllare l'accesso alle IoT Hub

## <a name="overview"></a>Panoramica

In questo articolo vengono descritte le opzioni per proteggere il tuo fulcro IoT. IoT Hub utilizza *le autorizzazioni* per concedere l'accesso per ogni endpoint hub IoT. Autorizzazioni limitano l'accesso a un hub IoT basato sulla funzionalità.

Questo articolo vengono illustrati:

- Autorizzazioni diverse che è possibile concedere a un dispositivo o back-end app per accedere a un centro di raccolta IoT.
- Il processo di autenticazione e i token che viene utilizzato per verificare le autorizzazioni.
- Come le credenziali dell'ambito per limitare l'accesso a risorse specifiche.
- Supporto IoT Hub per certificati x. 509.
- Meccanismi di autenticazione di dispositivo personalizzati che utilizzano registri di identità dispositivo esistente o schemi di autenticazione.

### <a name="when-to-use"></a>Quando utilizzare

È necessario disporre delle autorizzazioni appropriate per accedere a tutti i punti finali IoT Hub. Ad esempio, un dispositivo deve includere un token che contiene le credenziali di sicurezza insieme a ogni messaggio che viene inviato all'IoT Hub.

## <a name="access-control-and-permissions"></a>Le autorizzazioni e controllare l'accesso

È possibile concedere [le autorizzazioni](#iot-hub-permissions) nei modi seguenti:

* **Livello di hub condivisi i criteri di accesso**. Criteri di accesso condiviso possono concedere qualsiasi combinazione di [autorizzazioni](#iot-hub-permissions). È possibile definire i criteri nel [portale di Azure][lnk-management-portal], o a livello di programmazione tramite il [provider di risorse IoT Hub API REST][lnk-resource-provider-apis]. Un hub IoT appena creato è i seguenti criteri predefiniti:

    - **iothubowner**: criteri con tutte le autorizzazioni.
    - **servizio**: criteri di autorizzazione ServiceConnect.
    - **dispositivo**: criteri di autorizzazione DeviceConnect.
    - **registryRead**: criteri di autorizzazione RegistryRead.
    - **registryReadWrite**: criteri con autorizzazioni di RegistryRead e RegistryWrite.


* **Le credenziali di sicurezza per dispositivo**. Ogni IoT Hub contiene un [Registro di sistema di dispositivo identità][lnk-identity-registry]. Per ogni dispositivo in questo registro di sistema, è possibile configurare le credenziali di sicurezza per concedere autorizzazioni di **DeviceConnect** nell'ambito per gli endpoint dispositivo corrispondente.

Ad esempio, in una soluzione IoT tipica:

- Componente per la gestione dispositivi utilizza il criterio *registryReadWrite* .
- Il componente processore eventi utilizza i criteri di *servizio* .
- Il componente di logica di runtime dispositivo business utilizza il criterio di *servizio* .
- Singoli dispositivi connettono tramite le credenziali memorizzate nel Registro di sistema dell'hub IoT identità.

## <a name="authentication"></a>Autenticazione

IoT Hub Azure concede l'accesso agli endpoint verificando un token con i criteri di accesso condiviso e le credenziali di sicurezza del Registro di sistema di identità di dispositivo.

Le credenziali di sicurezza, ad esempio simmetrici tasti, non vengono mai inviate in rete.

> [AZURE.NOTE] Il provider di risorse Azure IoT Hub è protetto tramite l'abbonamento Azure sono tutti i provider di [Gestione risorse di Azure][lnk-azure-resource-manager].

Per ulteriori informazioni su come creare e utilizzare i token di sicurezza, vedere [token di sicurezza IoT Hub][lnk-sas-tokens].

### <a name="protocol-specifics"></a>Specifiche di protocollo

Ogni protocollo supportato, ad esempio MQTT, AMQP e HTTP, trasporti token in modi diversi.

Quando si utilizza MQTT, il pacchetto di connettersi con l'ID della periferica come ClientId, {iothubhostname} / {deviceId} nel campo nome utente e un token SA nella casella Password. {iothubhostname} dovrebbe essere CName completo dell'hub IoT (ad esempio contoso.azure devices.net).

Quando si usa [AMQP][lnk-amqp], IoT Hub supporta [SASL normale] [ lnk-sasl-plain] e [AMQP delle attestazioni-sicurezza basata sui -][lnk-cbs].

Se si utilizza AMQP sulle attestazioni in base a protezione, lo standard specifica la modalità di trasmissione questi token.

Per SASL normale, può essere il **nome utente** :

* `{policyName}@sas.root.{iothubName}`Se Usa token a livello di hub.
* `{deviceId}@sas.{iothubname}`Se Usa i token nell'ambito dispositivo.

In entrambi i casi, il campo password contiene il token, come descritto nei [token di sicurezza IoT Hub][lnk-sas-tokens].

HTTP autenticazione viene implementata facilmente individuabili perché includono un token valido nell'intestazione della richiesta di **autorizzazione** .

#### <a name="example"></a>Esempio

Nome utente (DeviceId è maiuscole e minuscole):`iothubname.azure-devices.net/DeviceId`

Password (genera sa con Esplora risorse dispositivo):`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] [Azure IoT Hub SDK] [ lnk-sdks] generare automaticamente i token quando ci si connette al servizio. In alcuni casi, gli SDK non supportano tutti i protocolli o tutti i metodi di autenticazione.

### <a name="special-considerations-for-sasl-plain"></a>Considerazioni speciali per SASL normale

Quando si usa SASL normale con AMQP, un client che si connette a un hub IoT possono utilizzare un singolo token per ogni connessione TCP. Quando scade il token, la connessione TCP disconnette dal servizio e avvia una riconnessione. Questo comportamento, mentre non un problema per un componente di back-end dell'applicazione, è molto dannoso per un'applicazione sul dispositivo per i motivi seguenti:

*  Gateway in genere connettersi per conto di molti dispositivi. Quando si usa SASL normale, hanno creare una connessione TCP distinta per ogni dispositivo che si connette a un hub IoT. Questo scenario notevolmente aumenta il consumo di energia e risorse di rete e aumenta la latenza di ogni connessione dispositivo.
* Dispositivi con risorse limitate influire negativamente sono interessati da un maggiore uso delle risorse per riconnettersi dopo ogni scadenza token.

## <a name="scope-hub-level-credentials"></a>Credenziali hub a livello di ambito

È possibile definire l'ambito di criteri di sicurezza a livello di hub creando token con URI di una risorsa con restrizioni. Ad esempio, l'endpoint per inviare messaggi di dispositivo nel cloud da un dispositivo è **/devices/ {deviceId} / messaggi/eventi**. È possibile usare anche un criterio di accesso condiviso a livello di hub con le autorizzazioni **DeviceConnect** per firmare un token di cui resourceURI **/devices/ {deviceId}**. Questo approccio crea un token che è disponibile solo per inviare messaggi per conto di dispositivo **deviceId**.

Questo meccanismo è simile a [criteri editore evento hub][lnk-event-hubs-publisher-policy]e consente di implementare i metodi di autenticazione personalizzato.

## <a name="security-tokens"></a>Token di sicurezza

IoT Hub utilizza token di sicurezza per autenticare i dispositivi e servizi per evitare l'invio di tasti in rete. Inoltre, i token di sicurezza sono limitati nell'ambito e validità temporale. [Azure IoT Hub SDK] [ lnk-sdks] generare automaticamente i token senza alcuna configurazione particolare. Tuttavia, alcuni scenari, richiedono all'utente di generare e utilizzare direttamente i token di sicurezza. Sono incluse l'uso diretto della MQTT, AMQP o HTTP lati o implementazione del modello di servizio token, come illustrato in [autenticazione dispositivo personalizzata][lnk-custom-auth].

IoT Hub consente inoltre dispositivi eseguire l'autenticazione con IoT Hub certificati [x. 509][lnk-x509]. 

### <a name="security-token-structure"></a>Struttura del token di sicurezza
Utilizzare i token di sicurezza per concedere delimitata ora accesso ai dispositivi e i servizi in IoT Hub funzionalità specifica. Per assicurarsi che solo i servizi e dispositivi autorizzati possono connettersi, token di sicurezza devono essere firmati con una chiave di criteri di accesso condiviso o una chiave simmetrica memorizzata con un'identità di dispositivo del Registro di sistema di identità.

Un token di cui è stato effettuato l'accesso con un accesso condiviso criteri chiave concede l'accesso a tutte le funzionalità associate le autorizzazioni dei criteri di accesso condiviso. Mano, un token firmato con una chiave simmetrico dell'identità di un dispositivo concede solo l'autorizzazione **DeviceConnect** per l'identità di dispositivo associato.

Il token di sicurezza è nel formato seguente:

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Questi sono i valori previsti:

| Valore | Descrizione |
| ----- | ----------- |
| {firma} | Una stringa di firma HMAC SHA256 del modulo: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: la chiave è decodificare da base 64 e utilizzata come chiave per eseguire il calcolo SHA256 HMAC. |
| {resourceURI} | Prefisso URI (in base alla segmento) gli endpoint che è possibile accedere con questo token, a partire da nome host del hub IoT (nessun protocol). Per esempio`myHub.azure-devices.net/devices/device1` |
| {scadenza} | Stringhe di UTF8 per numero di secondi dopo l'ora UTC 00:00:00 periodo del 1 ° gennaio 1970. |
| {URL-codificato-resourceURI} | Inferiore caso la codifica URL della risorsa minuscolo URI |
| {policyName} | Il nome del criterio di accesso condiviso a cui fa riferimento il token. Presente nel caso di token che fa riferimento alle credenziali di dispositivo del Registro di sistema. |

**Nota sulla prefisso**: prefisso URI viene calcolato dal segmento e non di un carattere. Ad esempio `/a/b` è un prefisso per `/a/b/c` ma non per `/a/bc`.

Il frammento Node seguente mostra una funzione denominata **generateSasToken** che calcola il token dagli input `resourceUri, signingKey, policyName, expiresInMins`. Nelle sezioni successive viene illustrato come inizializzare input diversi per i casi di utilizzo di token diverso.

    var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
        resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();

        // Set expiration in seconds
        var expires = (Date.now() / 1000) + expiresInMins * 60;
        expires = Math.ceil(expires);
        var toSign = resourceUri + '\n' + expires;

        // Use crypto
        var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
        hmac.update(toSign);
        var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

        // Construct autorization string
        var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
        + base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        return token;
    };

Come un confronto, si trova il codice Python equivalente per generare un token di sicurezza:

    from base64 import b64encode, b64decode
    from hashlib import sha256
    from time import time
    from urllib import quote_plus, urlencode
    from hmac import HMAC

    def generate_sas_token(uri, key, policy_name, expiry=3600):
        ttl = time() + expiry
        sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
        print sign_key
        signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

        rawtoken = {
            'sr' :  uri,
            'sig': signature,
            'se' : str(int(ttl))
        }

        if policy_name is not None:
            rawtoken['skn'] = policy_name

        return 'SharedAccessSignature ' + urlencode(rawtoken)

> [AZURE.NOTE] Poiché il tempo di validità del token viene convalidata computer IoT Hub, è importante che deriva dell'orologio del computer che genera il token di essere minimo.

### <a name="use-sas-tokens-in-a-device-client"></a>Usare i token SA in un client per dispositivi

Esistono due modi per ottenere le autorizzazioni di **DeviceConnect** con IoT Hub con token di sicurezza: utilizzare una [chiave di dispositivo simmetrico dal Registro di sistema di identità dispositivo](#use-a-symmetric-key-in-the-identity-registry)oppure utilizzare una [chiave di criteri di accesso condiviso](#use-a-shared-access-policy).

Ricordare che tutte le funzionalità accessibili da dispositivi viene esposto alla progettazione di endpoint con prefisso `/devices/{deviceId}`.

> [AZURE.IMPORTANT] L'unico modo che IoT Hub autentica un dispositivo specifico Usa il tasto simmetrico identità dispositivo. In casi quando un criterio di accesso condiviso viene utilizzato per accedere alle funzionalità di dispositivo, la soluzione è necessario il componente rilasciare il token di sicurezza come un componente secondario attendibile.

Gli endpoint dispositivo esposto sono (indipendentemente dalla protocol):

| Punto finale | Funzionalità |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | Inviare messaggi di dispositivo nel cloud. |
| `{iot hub host name}/devices/{deviceId}/devicebound` | Ricevere messaggi cloud al dispositivo. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Utilizzare una metrica chiave del Registro di sistema di identità

Quando si utilizza tasto simmetrico dell'identità di un dispositivo per generare un token di nomecriterio (`skn`) elemento del token viene omesso.

Ad esempio, un token creato per accedere a tutte le funzionalità di dispositivo deve avere parametri seguenti:

* URI risorsa: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chiave di firma: qualsiasi simmetrico chiave per la `{device id}` identità
* Nessun nome criterio
* in qualsiasi momento scadenza.

Un esempio di utilizzo della funzione nodo sopra dovrebbero essere:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

Il risultato, che consente l'accesso a tutte le funzionalità per periferica 1, dovrebbero essere:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] È possibile generare un token protetto tramite lo strumento di .NET [Esplora dispositivi][lnk-device-explorer].

### <a name="use-a-shared-access-policy"></a>Utilizzare un criterio di accesso condiviso

Quando si crea un token da un criterio di accesso condiviso, i criteri di assegnare un nome campo `skn` deve essere impostata sul nome del criterio utilizzato. È anche necessario che il criterio concede l'autorizzazione **DeviceConnect** .

I due scenari principali per l'uso di criteri di accesso condiviso per accedere alle funzionalità di dispositivo sono:

* [cloud gateway protocollo][lnk-endpoints],
* [servizi di token] [ lnk-custom-auth] utilizzato per implementare schemi di autenticazione personalizzati.

Poiché il criterio di accesso condiviso potenzialmente può concedere l'accesso per connettersi con qualsiasi dispositivo, è importante utilizzare la risorsa corretta URI quando si creano i token di sicurezza. Questa operazione è particolarmente importante per i servizi token che sono necessario definire l'ambito di token da un dispositivo specifico con URI della risorsa. In questo punto è meno rilevante per i gateway protocollo come che sono già che filtra il traffico per tutti i dispositivi.

Ad esempio, un servizio token utilizzando il criterio di accesso condiviso già creati denominato **dispositivo** è necessario creare un token con i parametri seguenti:

* URI risorsa: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chiave di firma: uno dei tasti del `device` criteri,
* nome del criterio: `device`,
* in qualsiasi momento scadenza.

Un esempio di utilizzo della funzione nodo sopra dovrebbero essere:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

Il risultato, che consente l'accesso a tutte le funzionalità per periferica 1, dovrebbero essere:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

Gateway di protocollo è possibile utilizzare lo stesso token per tutti i dispositivi sufficiente impostare la risorsa URI a `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Usare i componenti del servizio token di sicurezza

Componenti del servizio possono generare solo token di sicurezza utilizzando i criteri di accesso condiviso concedere le autorizzazioni appropriate, come descritto in precedenza.

Di seguito sono indicate le funzioni di servizio esposte sugli endpoint:

| Punto finale | Funzionalità |
| ----- | ----------- |
| `{iot hub host name}/devices` | Creare, aggiornare, recuperare ed eliminare le identità dispositivo. |
| `{iot hub host name}/messages/events` | Ricevere messaggi di dispositivo nel cloud. |
| `{iot hub host name}/servicebound/feedback` | Ricevere commenti e suggerimenti per i messaggi cloud al dispositivo. |
| `{iot hub host name}/devicebound` | Inviare messaggi cloud al dispositivo. |

Ad esempio, un servizio generazione utilizzando il criterio di accesso condiviso già creati denominato **registryRead** necessario creare un token con i parametri seguenti:

* URI risorsa: `{IoT hub name}.azure-devices.net/devices`,
* chiave di firma: uno dei tasti del `registryRead` criteri,
* nome del criterio: `registryRead`,
* in qualsiasi momento scadenza.

    endpoint var = "myhub.azure-devices.net/devices";   var policyName = 'dispositivo';   var policyKey = …;

    var token = generateSasToken (endpoint, policyKey, policyName, 60);

Il risultato, che concede l'accesso per leggere tutte le identità di dispositivo, dovrebbero essere:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead

## <a name="supported-x509-certificates"></a>Certificati x. 509 supportati

È possibile utilizzare qualsiasi certificato x. 509 per eseguire l'autenticazione di un dispositivo con IoT Hub. Sono inclusi:

-   **Un certificato x. 509 esistente**. Un dispositivo potrebbe già essere disponibile un certificato x. 509 è associato. Il dispositivo è possibile utilizzare il certificato per l'autenticazione con IoT Hub.

-   **Un certificato X-509 generato automatica e autofirmato**. Un produttore del dispositivo o distributore interni possa generare i certificati e memorizzare la chiave privata corrispondente (e certificato) nel dispositivo. È possibile utilizzare strumenti, ad esempio [OpenSSL] [ lnk-openssl] e [Windows SelfSignedCertificate] [ lnk-selfsigned] utilità a tale scopo.

-   **Certificato x. 509 firmato CA**. È anche possibile utilizzare un certificato x. 509 generato e firmato da un'autorità di certificazione (CA) per identificare un dispositivo e un dispositivo con IoT Hub di autenticazione.

Un dispositivo può utilizzare un certificato x. 509 o un token di sicurezza per l'autenticazione, ma non entrambe.

### <a name="register-an-x509-client-certificate-for-a-device"></a>Registrare un certificato client x. 509 per un dispositivo

[Azure IoT Service SDK per c#] [ lnk-service-sdk] (versione 1.0.8+) supporta la registrazione di un dispositivo che utilizza un certificato client x. 509 per l'autenticazione. Altre API, ad esempio importazione/esportazione dei dispositivi supportano anche 509 di client.

### <a name="c-support"></a>C\# supporto

La classe **RegistryManager** fornisce una modalità di programmazione per registrare un dispositivo. In particolare, i metodi **AddDeviceAsync** e **UpdateDeviceAsync** abilitare un utente registrare e aggiornare un dispositivo del Registro di sistema Iot Hub identità dispositivo. Questi due metodi richiedere un'istanza di **dispositivo** come input. La classe **dispositivo** include una proprietà di **autenticazione** che consente di specificare identificazioni personali del certificato x. 509 primario e secondario. L'identificazione personale rappresenta un hash SHA-1 del certificato x. 509 (archiviato utilizzando la codifica DER binario). Gli utenti hanno la possibilità di specificare un'identificazione personale principale o un'identificazione personale secondaria o entrambi. Identificazioni personali principali e secondarie sono supportati per le situazioni attivazione certificato.

> [AZURE.NOTE] IoT Hub non richiede o archiviare il certificato client x. 509 intero, solo l'identificazione personale.

Ecco un esempio C\# frammento di codice per registrare un dispositivo che utilizza un certificato client x. 509:

```
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-client-certificate-during-runtime-operations"></a>Usare un certificato client x. 509 durante le operazioni di runtime

Il [dispositivo di Azure IoT SDK per .NET] [ lnk-client-sdk] (versione 1.0.11+) supporta l'utilizzo di certificati client x. 509.

### <a name="c-support"></a>C\# supporto

La classe **DeviceAuthenticationWithX509Certificate** supporta la creazione di istanze  **DeviceClient** utilizzando un certificato client x. 509.

Ecco un frammento di codice di esempio:

```
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>Autenticazione dispositivo personalizzati

È possibile utilizzare l' IoT Hub [il Registro di sistema di dispositivo identità] [ lnk-identity-registry] di configurare le credenziali di sicurezza per dispositivo e accedere a controllo utilizzando [i token][lnk-sas-tokens]. Tuttavia, se la soluzione IoT ha già un investimento significativo in uno schema del Registro di sistema e/o l'autenticazione di identità dispositivo personalizzato, è possibile integrare questa infrastruttura esistente con IoT Hub mediante la creazione di un *servizio token di*. In questo modo, è possibile usare altre caratteristiche IoT della soluzione.

Un servizio token di è un servizio cloud personalizzato. Utilizza un IoT Hub *criterio di accesso condiviso* con le autorizzazioni **DeviceConnect** per creare i token *nell'ambito dispositivo* . Questi token abilitare un dispositivo per la connessione a un centro di raccolta IoT.

  ![Passaggi del modello di servizio token][img-tokenservice]

Ecco i passaggi principali del modello di servizio token:

1. Creare un criterio di accesso Hub IoT condiviso con autorizzazioni di **DeviceConnect** per il tuo fulcro IoT. È possibile creare questo criterio nel [portale di Azure] [ lnk-management-portal] o a livello di programmazione. Il servizio token di utilizza questo criterio per firmare i token creato.
2. Quando un dispositivo richiede l'accesso il tuo fulcro IoT, richiede un token con segno dal servizio token. Il dispositivo può eseguire l'autenticazione con uno schema di dispositivo personalizzati identità del Registro di sistema/autenticazione per determinare l'identità di dispositivo che usa il servizio token di per creare il token.
3. Il servizio token di restituisce un token. Viene creato tramite il token `/devices/{deviceId}` come `resourceURI`, con `deviceId` come dispositivo l'autenticazione. Il servizio token di utilizza il criterio di accesso condiviso per creare il token.
4. Il dispositivo utilizza il token direttamente con hub IoT.

> [AZURE.NOTE] È possibile utilizzare la classe .NET [SharedAccessSignatureBuilder] [ lnk-dotnet-sas] o la classe Java [IotHubServiceSasToken] [ lnk-java-sas] per creare un token del servizio token.

Il servizio token di impostare la scadenza token in base alle esigenze. Quando scade il token, hub IoT server la connessione al dispositivo. Quindi, il dispositivo deve richiedere un nuovo token dal servizio token. Se si usa una scadenza breve, in questo modo aumenta il carico sul dispositivo e il servizio token di.

Per un dispositivo per la connessione a un centro di raccolta, è comunque necessario aggiungere il del Registro di sistema IoT Hub identità dispositivo, anche se il dispositivo utilizza un token e non è una chiave di dispositivo per la connessione. Di conseguenza, è possibile continuare a usare il controllo di accesso per dispositivo attivando o disattivando identità dispositivo nel [Registro di sistema identità IoT Hub] [ lnk-identity-registry] quando il dispositivo autentica con un token. Consente di ridurre i rischi di utilizzando i token con i tempi di scadenza lungo.

### <a name="comparison-with-a-custom-gateway"></a>Confronto con un gateway personalizzato

Il modello di servizio token è il modo consigliato per implementare uno schema del Registro di sistema/autenticazione identità personalizzata con IoT Hub. È consigliabile in quanto IoT Hub continua a gestire la maggior parte del traffico soluzione. Tuttavia, esistono casi in cui lo schema di autenticazione personalizzato in modo è collegato con il protocollo che è necessario un servizio elaborazione tutto il traffico (*gateway personalizzati*). Un esempio è [protezione TLS (Transport Layer) e chiavi già condivise (PSKs)][lnk-tls-psk]. Per ulteriori informazioni, vedere il [gateway protocollo] [ lnk-protocols] argomento.

## <a name="reference-topics"></a>Argomenti della Guida di riferimento:

Gli argomenti della Guida di riferimento seguenti offrono ulteriori informazioni sul controllo dell'accesso per il tuo fulcro IoT.

## <a name="iot-hub-permissions"></a>Autorizzazioni IoT Hub

Nella tabella seguente elenca le autorizzazioni che è possibile utilizzare per controllare l'accesso a un centro di raccolta IoT.

| Autorizzazione            | Note |
| --------------------- | ----- |
| **RegistryRead**      | Concede l'accesso al Registro di sistema di identità dispositivo in lettura. Per ulteriori informazioni, vedere [il Registro di sistema di dispositivo identità][lnk-identity-registry]. |
| **RegistryReadWrite** | Concede accesso lettura e scrivono per il Registro di sistema di identità di dispositivo. Per ulteriori informazioni, vedere [il Registro di sistema di dispositivo identità][lnk-identity-registry]. |
| **ServiceConnect**    | Concede l'accesso alle comunicazioni e monitoraggio endpoint servizio esposto cloud. Ad esempio, concede l'autorizzazione ai servizi cloud di back-end per ricevere messaggi di dispositivo nel cloud, inviare messaggi cloud al dispositivo e recuperare la conferma di recapito corrispondente. |
| **DeviceConnect**     | Concede l'accesso per gli endpoint di comunicazione rivolta dispositivo. Ad esempio, concede l'autorizzazione per inviare messaggi di dispositivo nel cloud e ricevere messaggi cloud al dispositivo. Questa autorizzazione è utilizzata da dispositivi. |

## <a name="additional-reference-material"></a>Materiale di riferimento aggiuntivo

Altri argomenti nella Guida gli sviluppatori includono:

- [I punti finali IoT Hub] [ lnk-endpoints] descrive i vari endpoint esposti ogni hub IoT per le operazioni di gestione e runtime.
- [Limitazione e le quote di] [ lnk-quotas] descrive le quote che si applicano al servizio IoT Hub e il comportamento limitazione aspettarsi quando si utilizza il servizio.
- [IoT Hub dispositivo e servizio SDK] [ lnk-sdks] elenchi lingua vari SDK è un utilizzo quando si sviluppano dispositivo e servizio applicazioni che interagiscono con IoT Hub.
- [Linguaggio di query per due facce, metodi e processi] [ lnk-query] viene descritto il linguaggio di query è possibile utilizzare per recuperare le informazioni dall'IoT Hub relativi ai due facce dispositivo, metodi e processi.
- [Supporto IoT Hub MQTT] [ lnk-devguide-mqtt] sono disponibili ulteriori informazioni sul supporto IoT Hub per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è stato illustrato controllare l'accesso IoT Hub, potrebbero interessare gli argomenti della Guida per gli sviluppatori seguenti:

- [Utilizzare due facce di dispositivo per sincronizzare stato e configurazioni][lnk-devguide-device-twins]
- [Richiamare un metodo diretto in un dispositivo][lnk-devguide-directmethods]
- [Pianificare processi in più dispositivi][lnk-devguide-jobs]

Se si vuole provare alcuni dei concetti descritti in questo articolo, che potrebbero interessare le seguenti esercitazioni IoT Hub:

- [Guida introduttiva di Azure IoT Hub][lnk-getstarted-tutorial]
- [Come inviare messaggi cloud per dispositivo con IoT Hub][lnk-c2d-tutorial]
- [Modalità di elaborazione dei messaggi di IoT Hub dispositivo nel cloud][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
