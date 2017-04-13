<properties
     pageTitle="Usare il portale di Azure per creare un IoT Hub | Microsoft Azure"
     description="Una panoramica su come creare e gestire gli hub di Azure IoT tramite il portale di Azure"
     services="iot-hub"
     documentationCenter=""
     authors="dominicbetts"
     manager="timlt"
     editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/30/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-the-azure-portal"></a>Creare un hub IoT tramite il portale di Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]


## <a name="introduction"></a>Introduzione

Questo articolo descrive come trovare il servizio IoT Hub nel portale di Azure e su come creare e gestire gli hub IoT.

## <a name="where-to-find-iot-hubs"></a>Dove trovare IoT hub

Esistono vari modi in cui è possibile trovare IoT hub.

1. **+ Nuovo**: **Azure IoT Hub** è un servizio IoT e sono disponibili nella categoria **Internet di elementi**, in **+ Nuovo**, simile ad altri servizi.

2. Hub IoT anche accessibili tramite il Marketplace come servizio eroe in **Internet di elementi**.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

È possibile creare un hub IoT nei modi seguenti:

- Creazione di un hub IoT tramite l'opzione **+ Nuovo** conduce ad e l'illustrato nella schermata successiva. La procedura per creare l'hub IoT tramite questo metodo e il marketplace è identica.

- Creazione di un hub IoT attraverso il Marketplace: fare clic su **Crea** verrà aperta una pala identica a e il precedente per un'esperienza **+ Nuovo** utente. Nelle sezioni successive elencare i diversi passaggi per la creazione di un hub IoT.

### <a name="choose-the-name-of-the-iot-hub"></a>Scegliere il nome dell'hub IoT

Per creare un hub IoT, è necessario assegnare un nome hub. Questo nome deve essere univoco negli hub. La duplicazione dei hub non è consentita su back-end, è consigliabile che questo hub denominato in modo possibile.

### <a name="choose-the-pricing-tier"></a>Scegliere il livello prezzo

È possibile scegliere tra quattro livelli: **gratuito**, **Standard 1** e **2 Standard**e **S3 Standard**. Il livello gratuito consente di essere connessi a hub IoT e a un massimo 8.000 messaggi al giorno solo 500 dispositivi.

**S1 Standard**: IoT hub S1 edition è progettato per le soluzioni IoT che dispongono di un numero elevato di dispositivi generazione piccole quantità di dati al dispositivo. Ogni unità dell'edizione S1 consente fino a 400.000 messaggi al giorno tra tutti i dispositivi connessi.

**S2 Standard**: IoT Hub S2 edition è progettato per le soluzioni IoT in cui dispositivi generano grandi quantità di dati. Ogni unità dell'edizione S2 consente ai messaggi fino a 6 milioni al giorno tra tutti i dispositivi connessi.

**S3 Standard**: IoT Hub S3 edition è progettato per le soluzioni IoT che generano grandi quantità di dati. Ogni unità dell'edizione S3 consente fino a 300 milioni di messaggi al giorno tra tutti i dispositivi connessi.

![][4]

> [AZURE.NOTE] IoT Hub consente solo un hub gratuito per abbonamento Azure.

### <a name="iot-hub-units"></a>Unità hub IoT

Un'unità di hub IoT include un determinato numero di messaggi al giorno. Il numero totale di messaggi supportati per l'hub è il numero di unità moltiplicato per il numero di messaggi al giorno per quel livello. Ad esempio, se si vuole hub IoT per supportare ingresso d' 700.000 messaggi, selezionare due unità di livello S1.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Dispositivo per le partizioni cloud e gruppo di risorse

È possibile modificare il numero delle partizioni per un hub IoT. Partizioni predefinite sono impostate su 4; Tuttavia, è possibile scegliere un numero differente di partizioni da un elenco a discesa.

Gruppi di risorse, non è necessario creare in modo esplicito un gruppo di risorse vuoto. Quando si crea una risorsa, è possibile scegliere di creare un nuovo gruppo di risorse o utilizzare un gruppo di risorse esistenti.

![][5]

### <a name="choose-subscriptions"></a>Scegliere le sottoscrizioni

IoT Hub Azure compare automaticamente l'elenco delle sottoscrizioni Azure a cui è collegato l'account utente. È possibile scegliere una delle opzioni di seguito per associare hub IoT tale abbonamento Azure.

### <a name="choose-the-location"></a>Scegliere la posizione

L'opzione di posizione comprende un elenco delle aree in cui viene offerto IoT Hub. IoT Hub è disponibile per la distribuzione nei seguenti percorsi: Australia est, Australia Sudest, Asia orientale, Asia sudorientale, Nord Europa, Europa occidentale, Giappone est, ovest, Giappone Contattaci est, Contattaci ovest.

### <a name="create-the-iot-hub"></a>Creare hub IoT

Dopo aver complete tutte le soluzioni suggerite, hub IoT è pronto per essere creato. Fare clic su **Crea** per avviare il processo di back-end di creazione di questo hub IoT con opzioni specifiche e la distribuzione nella posizione specificata.

È possibile richiedere alcuni minuti per hub IoT da creare come richiede tempo per la distribuzione di back-end che venga eseguita nei server posizione appropriata.

## <a name="change-the-settings-of-the-iot-hub"></a>Modificare le impostazioni di hub IoT

È possibile modificare le impostazioni di un hub IoT esistente dopo averlo creato da e il IoT Hub.

![][8]

**Criteri di accesso condiviso**: questi criteri definiscono le autorizzazioni per i dispositivi e i servizi a cui connettersi IoT Hub. È possibile accedere a questi criteri, fare clic su **Condivisi i criteri di accesso** in **Generale**. In questo blade, è possibile modificare i criteri esistenti o aggiungere un nuovo criterio.

### <a name="create-a-policy"></a>Creazione di un criterio

- Fare clic su **Aggiungi** per aprire una pala. Immettere qui il nuovo nome dei criteri e le autorizzazioni che si desidera associare questo criterio, come illustrato nella figura seguente.

    Sono disponibili numerose autorizzazioni di accesso che possono essere associati a questi criteri condivisi. I primi due criteri, **Registro di sistema leggere** e **scrivere del Registro di sistema**, concessione leggere e diritti di accesso di scrittura per l'archivio di identità dispositivo o del Registro di sistema di identità. Scelta dell'opzione di scrittura automaticamente sceglie anche l'opzione di lettura.

    I criteri **servizio connettersi** concedono autorizzazioni di accesso endpoint lato cloud, ad esempio il gruppo consumer per la connessione a hub IoT i servizi. I criteri **dispositivo connettersi** concedono le autorizzazioni per l'invio e ricezione di messaggi su endpoint sul dispositivo dell'hub IoT.

- Fare clic su **Crea** per aggiungere il nuovo creato criteri all'elenco esistente.

![][10]

## <a name="messaging"></a>Messaggistica

Fare clic su **messaggistica** per visualizzare un elenco di messaggistica proprietà per l'hub IoT da modificare. Esistono due tipi principali di proprietà che è possibile modificare o copiare: **Cloud al dispositivo** e **dispositivo cloud**.

- Impostazioni **cloud dispositivo** : questa impostazione ha due impostazioni secondarie: **Cloud per dispositivo TTL** (time to live) e l' **ora di conservazione** per i messaggi. Hub IoT viene creata, entrambe queste impostazioni vengono create con un valore predefinito di un'ora. Per modificare questi valori, utilizzare i dispositivi di scorrimento o digitare i valori.

- Impostazioni **dispositivo cloud** : questa impostazione ha diverse impostazioni secondarie, alcune delle quali denominato/quando vengono assegnati hub IoT verrà creato e possono essere copiati solo in altre impostazioni secondarie personalizzabili. Queste impostazioni sono elencate nella sezione successiva.

**Le partizioni**: questo valore viene impostato quando l'hub IoT verrà creato e può essere modificata tramite questa impostazione.

**Endpoint e nome compatibile con evento Hub**: hub IoT il quando viene creato, un Hub di evento viene creato internamente che potrebbe essere necessario accedere a determinate condizioni. Questo nome compatibile con evento Hub ed endpoint non può essere personalizzato ma è disponibile per l'utilizzo tramite il pulsante **Copia** .

**Tempo di conservazione**: impostare un giorno per impostazione predefinita, ma può essere personalizzato per gli altri valori utilizzando l'elenco a discesa. Questo valore è espresso in giorni per dispositivo cloud e non in ore, così come l'impostazione simile per il Cloud al dispositivo.

**Gruppi di consumatori**: gruppi di consumatori sono un'impostazione simile a altri sistemi di messaggistica che possono essere utilizzati per estrarre dati in modi specifici a cui connettersi IoT Hub di altre applicazioni o servizi. Ogni hub IoT viene creato con un gruppo di consumer predefinito. Tuttavia, è possibile aggiungere o eliminare gruppi di consumatori per l'hub IoT.

> [AZURE.NOTE] Non è possibile modificare o eliminare il gruppo di consumer predefinito.

![][11]

## <a name="pricing-and-scale"></a>Prezzi e scala

È possibile modificare il prezzo di un hub IoT esistente tramite le impostazioni di **prezzi** , con le eccezioni seguenti:

- Nella implementazione corrente un hub IoT con uno SKU gratuito non è possibile modificare livelli in uno dei SKU a pagamento o viceversa.
- Nella sottoscrizione Azure solo può essere un hub IoT livello gratuito.

![][12]

Spostamento da un livello superiore (S2 o S3) abbassare di livello (S1 o S2) è consentito solo quando il numero di messaggi inviati per tale giorno non è in conflitto. Ad esempio, se il numero di messaggi al giorno supera 400.000, quindi il livello di hub IoT può essere modificato. Tuttavia, se si modifica a livello di S1 hub è limitata del giorno.

## <a name="delete-the-iot-hub"></a>Eliminare l'hub IoT

È possibile passare a hub IoT che si desidera eliminare facendo clic su **Sfoglia**e quindi scegliendo hub appropriato da eliminare. Fare clic sul pulsante **Elimina** sotto il nome dell'hub per eliminare l'hub.

## <a name="next-steps"></a>Passaggi successivi

Seguire questi collegamenti per ulteriori informazioni sulla gestione di Azure IoT Hub:

- [In blocco gestire dispositivi IoT][lnk-bulk]
- [Metriche di utilizzo][lnk-metrics]
- [Monitoraggio delle operazioni][lnk-monitor]

Per esplorare ulteriormente le funzionalità di IoT Hub, vedere:

- [Guida per sviluppatori][lnk-devguide]
- [La simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]
- [Proteggere la soluzione IoT da zero backup][lnk-securing]


  [4]: ./media/iot-hub-create-through-portal/create-iothub.png
  [5]: ./media/iot-hub-create-through-portal/location1.png
  [8]: ./media/iot-hub-create-through-portal/portal-settings.png
  [10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
  [11]: ./media/iot-hub-create-through-portal/messaging-settings.png
  [12]: ./media/iot-hub-create-through-portal/pricing-error.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md