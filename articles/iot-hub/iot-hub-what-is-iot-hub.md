<properties
 pageTitle="Panoramica di IoT Hub Azure | Microsoft Azure"
 description="Panoramica del servizio di Azure IoT Hub: che cos'è iot hub, connettività dei dispositivi, internet di modelli di comunicazione cose e modello di comunicazione assistito servizio"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/25/2016"
 ms.author="dobett"/>

# <a name="what-is-azure-iot-hub"></a>Che cos'è Azure IoT Hub?

Introduzione a Hub IoT Azure. In questo articolo viene fornita una panoramica di Azure IoT Hub e descrive il motivo per cui è necessario utilizzare questo servizio per implementare una soluzione di Internet di elementi (IoT). Azure IoT Hub è un servizio completamente gestito che consente la comunicazione sicura e attendibile bidirezionale tra milioni di dispositivi IoT e una soluzione back-end. Hub IoT Azure:

- Fornisce affidabile dispositivo nel cloud e messaggistica scala cloud al dispositivo.
- Consente di proteggere le comunicazioni con le credenziali di sicurezza per dispositivo e di controllo dell'accesso.
- Fornisce un controllo esteso per la connettività dei dispositivi e gli eventi di gestione delle identità dispositivo.
- Include le librerie di dispositivo per le lingue e piattaforme più popolari.

L'articolo [confronto di IoT Hub e hub di evento] [ lnk-compare] descrive le principali differenze tra questi due servizi e i vantaggi dell'utilizzo IoT Hub nelle soluzioni IoT in evidenza.

![Azure IoT Hub come gateway cloud in internet della soluzione di elementi][img-architecture]

> [AZURE.NOTE] Per informazioni dettagliate sulla IoT architettura, vedere l' [Architettura di riferimento di Microsoft Azure IoT][lnk-refarch].

## <a name="iot-device-connectivity-challenges"></a>Problemi di connettività dei dispositivi IoT

IoT Hub e le librerie di dispositivo consentono per soddisfare le esigenze di come in modo affidabile e sicuro connettere dispositivi alla soluzione back-end. Dispositivi IoT:

- Sono spesso di sistemi incorporati senza operatore umano.
- Può essere postazioni remote, in cui l'accesso fisico è costosa.
- Può essere solo raggiungibile tramite soluzione back-end.
- Limitato power e risorse di elaborazione.
- Potrebbe essere la connettività di rete intermittente, lenta o costosa.
- Potrebbe essere necessario utilizzare i protocolli di applicazione proprietario, personalizzato o specifici di settore.
- Possono essere create usando un elevato numero di piattaforme hardware e software più comuni.

Oltre ai requisiti sopra, qualsiasi soluzione IoT deve anche offrire scala, sicurezza e affidabilità. Il set di requisiti di connettività è difficile e richiede molto tempo implementare quando si utilizzano tecnologie tradizionali, ad esempio contenitori web e messaggistica Broker.

## <a name="why-use-azure-iot-hub"></a>Perché usare Azure IoT Hub

IoT Hub Azure consente di risolvere i problemi di connettività dei dispositivi nei modi seguenti:

-   **Per dispositivo autenticazione e connettività protetta**. È possibile effettuare il provisioning di ogni dispositivo con la propria [chiave di protezione] [ lnk-devguide-security] per poter connettersi IoT Hub. [Registro di sistema identità IoT Hub] [ lnk-devguide-identityregistry] archivia le chiavi e dell'identità dei dispositivi in una soluzione. Back-end una soluzione è possibile aggiungere singoli dispositivi per consentire o meno gli elenchi per abilitare il controllo completo accesso per i dispositivi.

-   **Monitoraggio delle operazioni di integrazione applicativa dispositivo**. È possibile ricevere registri operazioni dettagliate sulle operazioni di gestione di dispositivi identità e gli eventi di connettività di dispositivo. Questa funzionalità di monitoraggio consente la soluzione IoT identificare i problemi di connettività, ad esempio dispositivi che tentano di connettersi con credenziali errate, inviare messaggi troppo frequentemente o rifiutare tutti i messaggi di cloud al dispositivo.

-   **Una vasta gamma di librerie di dispositivo**. [Azure IoT dispositivo SDK] [ lnk-device-sdks] sono disponibili e supportati per diverse lingue e piattaforme - C per molti distribuzioni Linux, Windows e sistemi operativi in tempo reale. Azure IoT dispositivo SDK supportano anche linguaggi gestiti, ad esempio c#, Java e JavaScript.

-   **Estensibilità e i protocolli IoT**. Se la soluzione non è possibile utilizzare le raccolte di dispositivo, IoT Hub espone protocollo pubblico che consente di utilizzare a livello nativo MQTT v3.1.1, HTTP 1.1 o 1.0 AMQP protocolli dispositivi. È anche possibile estendere IoT Hub per fornire il supporto per i protocolli personalizzati per:

    - Creazione di un gateway di campo con [Azure IoT Gateway SDK] [ lnk-gateway-sdk] in grado di convertire il protocollo personalizzato a uno dei tre protocolli comprensibile IoT Hub. 
    - Personalizzazione del [gateway protocollo Azure IoT][protocol-gateway], un componente Apri origine che viene eseguita nel cloud.

-   **Scala**. IoT Hub Azure scale milioni di dispositivi collegati simultaneo e milioni di eventi al secondo.

Questi vantaggi sono generici a numerosi modelli di comunicazione. IoT Hub attualmente consente di implementare modelli di comunicazione specifici seguenti:

-   **Acquisizione di dispositivo nel cloud basato su eventi.** IoT Hub in modo affidabile è in grado di ricevere milioni di eventi al secondo dai dispositivi. Può elaborare loro nel percorso critico tramite un motore di processore degli eventi. Può inoltre memorizzare loro esistente nel percorso freddo per l'analisi. IoT Hub conserva i dati dell'evento per sette giorni per garantire l'elaborazione affidabile e assorbono picchi il carico.

-   * *Messaggistica affidabile cloud per dispositivo (o *comandi*). * * soluzione back-end possono utilizzare IoT Hub per inviare messaggi con una garanzia di recapito una volta al minimo a singoli dispositivi. Ciascun messaggio dispone di una singola impostazione time to live e back-end possono richiedere conferme di recapito e scadenza. Queste conferme garantiscono visibilità completa nel ciclo di vita di un messaggio cloud al dispositivo. È quindi possibile implementare logica aziendale che include le operazioni da eseguire su dispositivi.

-   **Caricare file e dati memorizzati nella cache sensore nel cloud.** I dispositivi possono caricare file archiviazione Azure con URI SA gestiti automaticamente da IoT Hub. IoT Hub può generare notifiche all'arrivo di nuovi file nel cloud per abilitare il back-end per l'elaborazione.

## <a name="gateways"></a>Gateway

Un gateway in una soluzione IoT è in genere un [gateway protocollo] [ lnk-gateway] che viene distribuito in cloud o un [gateway campo] [ lnk-field-gateway] che viene distribuito con i dispositivi. Gateway di protocollo esegue traduzione protocollo, ad esempio MQTT a AMQP. Un gateway di campo possono eseguire analitica sul bordo, prendere decisioni urgenti per ridurre la latenza, fornire servizi di gestione di dispositivi, applicare la protezione e i vincoli di privacy e anche eseguire la conversione protocollo. Entrambi i tipi di gateway fungono da intermediari tra i dispositivi e il tuo fulcro IoT.

Un gateway di campo è diverso da un dispositivo instradare il traffico semplice (ad esempio un periferica NAT o un firewall) perché consente di eseguire in genere la gestione del flusso di accesso e le informazioni della soluzione un ruolo attivo.

Una soluzione può includere protocollo e campo gateway.

## <a name="how-does-iot-hub-work"></a>Funzionamento IoT Hub

IoT Hub Azure implementa [assistito servizio comunicazioni] [ lnk-service-assisted-pattern] motivo da interporre le interazioni tra i dispositivi e le soluzioni di back-end. L'obiettivo di comunicazione assistito servizio è stabilire attendibile, percorsi di comunicazione bidirezionali tra un sistema di controllo, ad esempio IoT Hub e dispositivi speciali distribuiti in attendibili spazio fisico. Il modello stabilisce principi seguenti:

- Protezione ha la precedenza su tutte le altre funzionalità.
- Dispositivi non accettano informazioni relative alla rete indesiderati. Un dispositivo stabilisce tutte le connessioni e indirizza in modo solo in uscita. Per un dispositivo ricevere un comando da back-end, il dispositivo deve regolarmente avviare una connessione per verificare la presenza di comandi in sospeso per l'elaborazione.
- Dispositivi devono solo connettersi o stabilire indirizza a servizi noti che sono peered, ad esempio IoT Hub.
- Il percorso di comunicazione tra dispositivo e servizio o tra dispositivo e al gateway è protetta al livello protocollo dell'applicazione.
- Autenticazione e l'autorizzazione a livello di sistema dipendono delle identità per dispositivo. Apportano le credenziali di accesso e autorizzazioni quasi istantaneamente revocabile.
- La comunicazione bidirezionale per i dispositivi che eseguono la connessione occasionalmente a causa di potenza o la connettività preoccupazioni mediante tenendo comandi e delle notifiche di dispositivo fino a quando non si connette un dispositivo per riceverle. IoT Hub mantiene code specifico di dispositivo per i comandi che invia.
- Applicazione payload dati vengono protetti separatamente per il transito protetto da un gateway a un determinato servizio.

Il settore mobile è utilizzato il modello di comunicazione assistito servizio scala enorme per implementare i servizi di notifica push, ad esempio [Windows Push Notification Services][lnk-wns], [Google Cloud messaggistica][lnk-google-messaging], [Apple Push Notification Service]e[lnk-apple-push].

IoT Hub è supportata su percorso peering pubblico del ExpressRoute.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come IoT Hub Azure consente basate su standard di gestione di dispositivi IoT per poter gestire, configurare e aggiornare i dispositivi in modalità remota, vedere [gestione di dispositivi Panoramica di Azure IoT Hub][lnk-device-management].

Per implementare le applicazioni client su una vasta gamma di piattaforme di dispositivi hardware e sistemi operativi, è possibile usare il dispositivo IoT SDK. Il dispositivo IoT SDK includono librerie che semplificano telemetria invio a un hub IoT e ricevere comandi cloud al dispositivo. Quando si utilizza il SDK, è possibile scegliere da diversi protocolli di rete per comunicare con IoT Hub. Per ulteriori informazioni, vedere le [informazioni dispositivo SDK][lnk-device-sdks].

Per iniziare a scrivere codice e l'esecuzione di alcuni esempi, vedere la [Guida introduttiva a IoT Hub] [ lnk-get-started] esercitazione.

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Servizio assistito comunicazioni, post di blog di Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-device-management]: iot-hub-device-management-overview.md
