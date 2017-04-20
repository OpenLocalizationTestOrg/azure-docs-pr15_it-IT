
# <a name="azure-and-internet-of-things"></a>Azure e operazioni su Internet

Per Microsoft Azure e Internet di elementi (IoT). In questo articolo vengono introdotti un'architettura della soluzione IoT che descrive le caratteristiche comuni di una soluzione IoT che è possibile distribuire mediante servizi di Azure. Soluzioni IoT richiedono la protezione, la comunicazione bidirezionale tra dispositivi, probabilmente la numerazione in milioni e un back-end di soluzione. Back-end una soluzione, ad esempio, potrebbe utilizzare analitica automatizzato, predittiva per Scopri informazioni dal flusso di evento dispositivo al cloud.

IoT Hub Azure è un blocco predefinito chiave quando si implementa questa architettura della soluzione IoT mediante servizi di Azure. Famiglia di prodotti IoT fornisce le implementazioni complete ed end-to-end di questa architettura per scenari IoT specifici. Per esempio: 

- La soluzione *di monitoraggio remote* consente di monitorare lo stato dei dispositivi, ad esempio macchine distributore. 
- La soluzione di *manutenzione predittiva* consente di prevedere esigenze di gestione dei dispositivi, ad esempio pompe in remote stazioni pompaggio ed evitare tempi di inattività non pianificata.

## <a name="iot-solution-architecture"></a>Architettura della soluzione IoT

Nel diagramma seguente viene illustrata una tipica architettura della soluzione IoT. Il diagramma non include i nomi di tutti i servizi specifici Azure, ma vengono descritti gli elementi principali di un'architettura della soluzione IoT generico. In questa architettura, i dispositivi IoT raccolgono i dati inviati a un gateway cloud. Il gateway cloud rende disponibili i dati di elaborazione da altri servizi back-end in cui vengono recapitati i dati di altre applicazioni line-of-business o agli operatori umani mediante un dashboard o un altro dispositivo presentazione.

![Architettura della soluzione IoT][img-solution-architecture]

> [AZURE.NOTE] Per informazioni più dettagliate dell'architettura IoT, vedere [Architettura di riferimento di Microsoft Azure IoT][lnk-refarch].

### <a name="device-connectivity"></a>Connettività dei dispositivi

In questa architettura della soluzione IoT, dispositivi inviano telemetria, ad esempio letture sensore da un'espansione pompaggio a un endpoint cloud dell'archiviazione e di elaborazione. In uno scenario di manutenzione predittiva, il back-end può utilizzare il flusso di dati sensore per determinare se un pump specifico richiede una manutenzione. Dispositivi possono inoltre riceveranno e risponderanno alle comandi cloud a device per leggere i messaggi da un endpoint cloud. Ad esempio, nello scenario relativo a manutenzione predittiva soluzione back-end può inviare comandi a altre pompe nell'espansione pompaggio per avviare subito prima della scadenza iniziare a verificare che il responsabile di manutenzione può iniziare a dopo aver raggiunto la manutenzione del reindirizzamento dei flussi di.

Uno dei maggiori sfide IoT progetti è come connettersi in modo affidabile e in modo protetto i dispositivi a soluzione back-end. Dispositivi IoT hanno caratteristiche diverse rispetto alle altre client, ad esempio i browser e App per dispositivi mobili. IoT dispositivi:

- Sono spesso sistemi incorporati senza operatore risorse umane.
- Possono essere distribuiti in sedi remote, dove l'accesso fisico è costosa.
- Potrebbe essere raggiungibili soltanto tramite soluzioni back-end. Non esiste alcun altro modo per interagire con il dispositivo.
- Potrebbe non dispongono di alimentazione e risorse di elaborazione.
- Potrebbe essere la connettività di rete intermittente, lenta o costosa.
- Potrebbe essere necessario utilizzare protocolli applicativi proprietario, personalizzato o specifiche del settore.
- Possono essere creati utilizzando un elevato numero di piattaforme hardware e software più comuni.

Oltre ai requisiti, qualsiasi soluzione IoT deve inoltre offrono scalabilità, sicurezza e l'affidabilità. Il set di requisiti di integrazione applicativa è disco rigido e richiedere molte risorse per implementare utilizzando tecnologie tradizionali, ad esempio contenitori web e Broker di messaggistica. IoT Hub Azure e gli SDK dispositivo IoT rendono più semplice implementare le soluzioni che soddisfano questi requisiti.

Un dispositivo può comunicare direttamente con un endpoint di gateway cloud o se il dispositivo è possibile utilizzare uno dei protocolli di comunicazione che supporta il gateway cloud, è possibile connettersi tramite un gateway intermedio. Ad esempio, il [gateway di protocollo Azure IoT] [ lnk-protocol-gateway] grado di effettuare protocollo se dispositivi non possono utilizzare uno dei protocolli che supporta l'IoT Hub.

### <a name="data-processing-and-analytics"></a>Elaborazione dei dati e analitica

Nel cloud, un IoT soluzione back end è dove si verifica la maggior parte dell'elaborazione dei dati, ad esempio il filtro e aggregazione di telemetria e il routing ad altri servizi. La soluzione IoT back end:

- Riceve telemetria in scala dal dispositivo e determina la modalità di elaborazione e memorizzare i dati. 
- Consente di inviare i comandi dal cloud per dispositivo specifico.
- Offre funzionalità che consentono di provisioning dei dispositivi e per controllare quali dispositivi sono autorizzati a connettersi all'infrastruttura di registrazione.
- Consente di tenere traccia dello stato dei dispositivi e monitorare le loro attività.

Nello scenario relativo manutenzione predittiva soluzione back-end archivia i dati di telemetria cronologica. Back-end è possibile utilizzare questi dati da utilizzare per identificare modelli che indicano la manutenzione è in scadenza in un pump specifico.

Soluzioni IoT possono includere cicli di commenti automatica. Ad esempio, possibile identificare un modulo analitica back-end di telemetria che la temperatura di un dispositivo specifico si trova sopra normali livelli operativi. La soluzione può quindi inviare un comando al dispositivo, indicando di intraprendere un'azione correttiva.

### <a name="presentation-and-business-connectivity"></a>Connettività per la presentazione e business

Il livello di integrazione applicativa presentazione e business consente agli utenti di interagire con la soluzione IoT e i dispositivi. Consente agli utenti di visualizzare e analizzare i dati raccolti dai dispositivi. Queste visualizzazioni possono essere rappresentato del dashboard o i report di Business Intelligence possibile visualizzare entrambi i dati cronologici o vicino dati in tempo reale. Ad esempio, un operatore può controllare allo stato del station pompaggio specifico e avvisi generati dal sistema di. Questo livello consente inoltre l'integrazione di IoT soluzione back-end con le applicazioni line-of-business esistenti possono usufruire di processi aziendali dell'organizzazione o flussi di lavoro. Ad esempio, la soluzione di manutenzione predittiva possibile integrare con un sistema di pianificazione che libri engineer per visitare un espansione pompaggio quando la soluzione identifica un pump che necessitano di manutenzione.

![Dashboard di soluzione IoT][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
