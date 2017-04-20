# <a name="securing-your-iot-deployment"></a>Protezione della distribuzione IoT

In questo articolo fornisce il livello di dettaglio successivo per la protezione dell'infrastruttura basata su Azure IoT Internet di elementi (IoT). Primo collega a dettagli a livello di implementazione per la configurazione e distribuzione di ogni componente. Fornisce anche nei confronti e scelte tra i vari metodi concorrenti.

Proteggere la distribuzione Azure IoT può essere suddiviso nelle seguenti aree di tre protezione:

- **Sicurezza dei dispositivi**: impostazione della sicurezza del dispositivo IoT mentre viene distribuito nell'ambiente naturale.
- **Protezione di connessione**: verifica tutti i dati scambiati tra il dispositivo IoT e IoT Hub è riservato e manomissione.
- **Protezione cloud**: offre la possibilità di proteggere i dati mentre si sposta tra e archiviata nel cloud.

![Tre aree di protezione][img-overview]

## <a name="secure-device-provisioning-and-authentication"></a>Provisioning dispositivo sicuro e autenticazione

La famiglia di prodotti IoT Azure protegge IoT dispositivi da due metodi seguenti:

- Grazie a una chiave di identità univoca (token di protezione) per ogni dispositivo, che può essere utilizzato dal dispositivo per comunicare con l'IoT Hub.

- [Certificato x. 509] nel dispositivo con[ lnk-x509] e la chiave privata come mezzo per autenticare il dispositivo per l'IoT Hub. Questo metodo di autenticazione assicura che la chiave privata del dispositivo non è noto all'esterno del dispositivo in qualsiasi momento fornendo un livello di sicurezza.

Il metodo di token di sicurezza fornisce l'autenticazione per ogni chiamata effettuata dal dispositivo per IoT Hub associa il tasto simmetrico per ogni chiamata. L'autenticazione basata su x. 509 consente l'autenticazione di un dispositivo IoT livello fisico durante il tentativo di stabilire la connessione TLS. Il metodo basato su token di sicurezza può essere utilizzato senza l'autenticazione di x. 509 che corrisponde a un formato meno sicuro. La scelta tra i due metodi principalmente dipende dalla modalità di protezione il dispositivo autenticazione deve essere e la disponibilità di spazio di archiviazione sicura del dispositivo (per archiviare in modo sicuro della chiave privata).

## <a name="iot-hub-security-tokens"></a>Token di sicurezza IoT Hub

IoT Hub utilizza i token di sicurezza per autenticare i dispositivi e servizi per evitare l'invio delle chiavi nella rete. Inoltre, token di protezione sono limitati nell'ambito e tempo di validità. Azure IoT Hub SDK genera automaticamente i token senza la necessità di configurazioni particolari. Alcuni scenari, tuttavia, necessario all'utente di generare e utilizzare direttamente i token di sicurezza. Queste proprietà includono l'utilizzo diretto di superfici MQTT, AMQP o HTTP o l'implementazione del modello del servizio token.

Ulteriori informazioni sulla struttura del token di sicurezza e il relativo utilizzo sono disponibili negli articoli seguenti:

-   [Struttura di token di sicurezza][lnk-security-tokens]
-   [Utilizzo di token SAS come dispositivo][lnk-sas-tokens]

Ogni IoT Hub ha un [Registro di sistema identità dispositivo] [ lnk-identity-registry] che può essere utilizzato per creare le risorse per ogni dispositivo nel servizio, ad esempio una coda che contiene i messaggi cloud per dispositivo in corso e per consentire l'accesso agli endpoint con connessione dispositivo. Nel Registro di sistema di identità IoT Hub consente l'archiviazione sicura di identità del dispositivo e chiavi di protezione per una soluzione. Singoli utenti o gruppi di identità del dispositivo possono essere aggiunti a un elenco Consenti o un elenco di blocco, controllo completo su accesso al dispositivo. I seguenti articoli forniscono ulteriori informazioni sulla struttura del Registro di sistema di identità dispositivo e supportate operazioni.

[IoT Hub supporta i protocolli, ad esempio MQTT, AMQP e HTTP][lnk-protocols]. Ognuno di essi utilizzano i token di protezione dal dispositivo IoT a IoT Hub in modo diverso:

- AMQP: Protezione di semplice e basata sulle attestazioni AMQP SASL ({policyName}@sas.root.{iothubName} nel caso di token a livello di Hub; {deviceId} in caso di token come ambito dispositivo).

- MQTT: La connessione utilizza pacchetti {deviceId} come {ClientId} {IoThubhostname} / {deviceId} nel campo **nome utente** e un token SAS nel campo **Password** .

- HTTP: Token valido è nell'intestazione della richiesta di autorizzazione.

Registro di sistema IoT Hub dispositivo Identity consente di configurare le credenziali di protezione per i singoli dispositivi e controllo di accesso. Tuttavia, se una soluzione IoT dispone già di un investimento significativo in una [combinazione di registro di sistema e/o autenticazione identità dispositivo personalizzato][lnk-custom-auth], possono essere integrata in un'infrastruttura esistente con IoT Hub mediante la creazione di un token servizio.

### <a name="x509-certificate-based-device-authentication"></a>Autenticazione basata su certificati dispositivo x. 509

L'utilizzo di un [certificato x. 509 basati su dispositivo] [ lnk-use-x509] e il relativo coppia di chiavi pubblica e privata associata consente un'ulteriore autenticazione livello fisico. La chiave privata archiviata in modo sicuro del dispositivo e non è individuabile all'esterno del dispositivo. Certificato x. 509 contiene informazioni sui dispositivi, ad esempio, ID del dispositivo e altri dettagli dell'organizzazione. Viene generata una firma del certificato utilizzando la chiave privata.

Flusso di provisioning di tipo di dispositivo generale:

- Associare un identificatore in un dispositivo fisico – identity di dispositivi e/o certificato x. 509 associato al dispositivo durante dispositivo produzione o la messa in funzione.

- Creare una voce di identità corrispondente nell'IoT Hub – identity dispositivo e informazioni sui dispositivi associato nel Registro di sistema dispositivo Hub IoT.

- Archiviare in modo sicuro identificazione personale del certificato x. 509 nel Registro di sistema dispositivo IoT Hub.

### <a name="root-certificate-on-device"></a>Certificato radice nel dispositivo

Quando si stabilisce una connessione TLS protetta con IoT Hub, il dispositivo IoT autentica IoT Hub utilizzando un certificato radice che fa parte del dispositivo SDK. Per il client C SDK il certificato sia presente nella cartella "\\c\\certificati" nella radice del repo. Anche se questi certificati radice siano lunga durati, comunque possibile scadono o revocati. Se non è possibile in alcun modo di aggiornamento dei certificati sul dispositivo, il dispositivo potrebbe non essere in grado di connettersi successivamente l'IoT Hub (o qualsiasi altro servizio cloud). Disporre di un mezzo per aggiornare il certificato radice, una volta che viene distribuito il dispositivo IoT riduce in modo efficace il rischio.

## <a name="securing-the-connection"></a>Protezione della connessione

Connessione Internet tra il dispositivo IoT e IoT Hub è protetto utilizzando lo standard Transport Layer Security (TLS). Azure IoT supporta [TLS 1.2][lnk-tls12], TLS 1.1 e TLS 1.0, nell'ordine indicato. Supporto per TLS 1.0 è disponibile solo per la compatibilità. È consigliabile utilizzare TLS 1.2 perché offre maggiore protezione.

Famiglia di prodotti IoT Azure supporta pacchetti di crittografia seguenti nell'ordine indicato.

| Famiglia di prodotti di crittografia | Lunghezza |
|--------------|--------|
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 secp384r1 ECDH (0xc028) (EQ. ADFS 7680 bit RSA) | 256    |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256 secp256r1 ECDH (0xc027) (EQ. ADFS 3072 bit RSA) | 128    |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA (0xc014) secp384r1 ECDH (EQ. ADFS 7680 bit RSA)           | 256    |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013) secp256r1 ECDH (EQ. ADFS 3072 bit RSA)           | 128    |
| TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 (0x9d) | 256    |
| TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256 (0x9c) | 128    |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA256 (0x3d) | 256    |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0x3c) | 128    |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA (0x35)    | 256    |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA (0x2f)    | 128    |
| TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA (0xa)    | 112    |

## <a name="securing-the-cloud"></a>Protezione nel cloud

IoT Hub Azure consente la definizione di [criteri di controllo di accesso] [ lnk-protocols] per ogni tasto di sicurezza. Viene utilizzato il set di autorizzazioni seguente per concedere l'accesso a ognuno degli endpoint dell'IoT Hub. Autorizzazioni di limitare l'accesso a un Hub IoT basato sulla funzionalità.

- **RegistryRead**. Concede l'accesso in lettura al Registro di sistema di identità di dispositivo. Per ulteriori informazioni, vedere [Il Registro di sistema di dispositivo identità][lnk-identity-registry].

- **RegistryReadWrite**. Concede accesso lettura e scrivono nel Registro di sistema di identità di dispositivi. Per ulteriori informazioni, vedere [Il Registro di sistema di dispositivo identità][lnk-identity-registry].

- **ServiceConnect**. Concede l'accesso a cloud di comunicazione e monitoraggio endpoint con connessione del servizio. Ad esempio, vengono concesse autorizzazioni ai servizi cloud di back-end per ricevere messaggi dispositivo al cloud, inviare messaggi cloud di dispositivi e recuperare i corrispondenti riconoscimenti di recapito.

- **DeviceConnect**. Concede l'accesso agli endpoint di comunicazione con connessione dispositivo. Ad esempio, concede l'autorizzazione per inviare messaggi dispositivo al cloud e ricevere messaggi cloud al dispositivo. Questa autorizzazione viene utilizzata dai dispositivi.

Esistono due modi per ottenere le autorizzazioni **DeviceConnect** con IoT Hub con [token di protezione][lnk-sas-tokens]: utilizzo di una chiave di identità del dispositivo o una chiave di criteri di accesso condiviso. Inoltre, è importante tenere presente che tutte le funzionalità accessibile dai dispositivi verrà reso disponibile per impostazione predefinita in endpoint con prefisso `/devices/{deviceId}`.

[Componenti del servizio possono essere generati solo i token di sicurezza] [ lnk-service-tokens] utilizzando condivisi i criteri di accesso concedere le autorizzazioni appropriate.

IoT Hub Azure e altri servizi che potrebbero essere la soluzione di consentono la gestione degli utenti con Azure Active Directory.

Dati di caricamento per Azure IoT Hub possono essere utilizzati da diversi servizi, ad esempio Azure flusso Analitica e archiviazione blob Azure. Questi servizi consentono l'accesso alla gestione. Per ulteriori informazioni su questi servizi e disponibili opzioni riportate di seguito:

- [Azure DocumentDB][lnk-docdb]: un servizio database scalabile e indicizzazione completa per i dati semistrutturati utilizzato per gestire i metadati per i dispositivi viene effettuato il provisioning, ad esempio gli attributi, la configurazione e le proprietà di protezione. DocumentDB offre elaborazione ad alte prestazioni e ad alta velocità, indipendente dal schema indicizzazione dei dati e un'interfaccia di query SQL avanzata.

- [Azure Analitica flusso][lnk-asa]: flusso Real-time di elaborazione nel cloud che consente di sviluppare e distribuire una soluzione a costi analitica per scoprire in tempo reale informazioni dai dispositivi, sensori, infrastruttura e applicazioni rapidamente. I dati di questo servizio completamente gestito possono passare a qualsiasi volume ottenere comunque ad alta velocità effettiva, bassa latenza e la resilienza.

- [Servizi di Azure App][lnk-appservices]: una piattaforma cloud per creare potenti web e applicazioni per dispositivi mobili che si connettono ai dati in qualsiasi punto. nel cloud o locale. Creare partecipazione App per dispositivi mobili per iOS, Android e Windows. Integrazione con il Software come servizio (SaaS) e applicazioni aziendali con connettività della casella di decine di servizi basati sul cloud e applicazioni aziendali. Codice in IDE (.NET, NodeJS, PHP, Python o Java) per creare applicazioni web e API più velocemente che mai e della lingua preferita.

- [Logica Apps][lnk-logicapps]: funzionalità di Apps la logica di Azure App Service consente di integrare la soluzione IoT per i sistemi line-of-business esistenti e automatizzare i processi del flusso di lavoro. Logica di App consente agli sviluppatori di progettazione flussi di lavoro avviare da un trigger e quindi eseguire una serie di operazioni, ovvero le regole e le azioni che utilizzano i connettori potenti per l'integrazione con i processi aziendali. Logica di App offre una connettività della casella a una vasta ecosistema SaaS, basata su cloud e le applicazioni in locale.

- [Archiviazione blob Azure][lnk-blob]: archiviazione cloud affidabile ed economiche per i dati da inviare i dispositivi nel cloud.

## <a name="conclusion"></a>Conclusione

Questo articolo vengono fornite informazioni preliminari implementazione dettagli relativi al livello della progettazione e distribuzione di un'infrastruttura IoT utilizzando IoT Azure. Configurazione di ogni componente di protezione è chiave nella protezione dell'infrastruttura IoT complessiva. Le scelte di progettazione disponibili in Azure IoT offrono una certa flessibilità e scelta; Tuttavia, ogni scelta può avere implicazioni di sicurezza. È consigliabile che ognuna delle quali valutato attraverso una valutazione dei rischi/costo.

[img-overview]: media/iot-secure-your-deployment/overview.png

[lnk-security-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure
[lnk-sas-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-as-a-device
[lnk-identity-registry]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-protocols]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-custom-auth]: ../articles/iot-hub/iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: http://www.itu.int/rec/T-REC-X.509-201210-I/en
[lnk-use-x509]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-tls12]: https://tools.ietf.org/html/rfc5246
[lnk-service-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#using-security-tokens-from-service-components
[lnk-docdb]: https://azure.microsoft.com/services/documentdb/
[lnk-asa]: https://azure.microsoft.com/services/stream-analytics/
[lnk-appservices]: https://azure.microsoft.com/services/app-service/
[lnk-logicapps]: https://azure.microsoft.com/services/app-service/logic/
[lnk-blob]: https://azure.microsoft.com/services/storage/
