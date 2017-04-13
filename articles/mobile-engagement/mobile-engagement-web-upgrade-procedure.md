<properties
    pageTitle="Procedure di aggiornamento Mobile coinvolgimento Web SDK Azure | Microsoft Azure"
    description="Gli ultimi aggiornamenti e procedure di Web SDK per Azure Mobile coinvolgimento"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="piyushjo" />


# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Procedure di aggiornamento Mobile coinvolgimento Web SDK Azure

Se è già stata integrata una versione precedente di Azure Mobile coinvolgimento Web SDK nell'applicazione web, è necessario tenere presente quanto segue quando si aggiorna il SDK.

Se non si è eseguito più versioni di Mobile coinvolgimento Web SDK, potrebbe essere necessario eseguire alcune procedure durante il processo di aggiornamento. Ad esempio, se esegue la migrazione da 1.4.0 a 1.6.0, prima di tutto seguire le procedure per l'aggiornamento da 1.4.0 1.5.0. Seguire le procedure per l'aggiornamento da 1.5.0 1.6.0.

Qualsiasi versione l'aggiornamento da, sostituire una versione precedente di file azure-engagement.js con la versione più recente del file.

## <a name="upgrade-from-121-to-200"></a>Aggiornamento da 1.2.1 a 2.0.0

In questa sezione viene descritto come eseguire la migrazione di un'integrazione Mobile coinvolgimento Web SDK dal servizio di Capptain, offerto dai Capptain SAS, a un'app di Azure Mobile coinvolgimento. Se si esegue la migrazione da una versione precedente, consultare il sito Web Capptain per prima la migrazione a 1.2.1 e quindi applicare le procedure seguenti.

Questa versione di Mobile coinvolgimento Web SDK non supporta Samsung Smart TV, TV Opera, webOS o la caratteristica di mano.

>[AZURE.IMPORTANT] Capptain e Azure Mobile coinvolgimento non sono lo stesso servizio. La procedura seguente viene evidenziato solo come eseguire la migrazione dell'applicazione client. Migrazione Mobile coinvolgimento Web SDK nell'app verrà non eseguire la migrazione dei dati da un server Capptain a un server di impegno Mobile.

### <a name="javascript-files"></a>File JavaScript

Sostituire il file capptain-sdk.js con file azure-engagement.js e aggiornare di conseguenza l'importazione di script.

### <a name="remove-capptain-reach"></a>Rimuovere portata Capptain

Questa versione di Mobile coinvolgimento Web SDK non supporta la funzionalità di mano. Se Capptain portata è integrato con l'applicazione, è necessario rimuoverlo.

Rimuovere l'importazione di raggiungere CSS dalla pagina ed eliminare il file CSS correlati (capptain-reach.css, per impostazione predefinita).

Eliminare le seguenti risorse portata: l'immagine di chiusura (capptain-close.png, per impostazione predefinita) e le icone di marchio (capptain--icona di notifica, per impostazione predefinita).

Rimuovere l'interfaccia utente raggiungere per le notifiche nell'app. Il layout predefinito è simile alla seguente:

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

Rimuovere l'interfaccia utente raggiungere per testo e gli annunci web e sondaggi. Il layout predefinito è simile alla seguente:

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

Rimuovere il `reach` oggetto dalla configurazione, se esistente. Simile al seguente:

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

Rimuovere qualsiasi altra personalizzazione di mano, ad esempio le categorie.

### <a name="remove-deprecated-apis"></a>Rimuovere API obsolete

Alcune API da Capptain sono obsoleti in Mobile coinvolgimento Web SDK.

Rimuovere le chiamate a seguenti API: `agent.connect`, `agent.disconnect`, `agent.pause`, e `agent.sendMessageToDevice`.

Rimuovere tutte le istanze di callback indicati di seguito dalla configurazione Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, e `onPushMessageReceived`.

### <a name="configuration"></a>Configurazione

Coinvolgimento mobile utilizza una stringa di connessione per configurare gli identificatori di SDK, ad esempio, l'identificatore dell'applicazione.

Sostituire l'ID dell'applicazione con la stringa di connessione. Si noti che l'oggetto globale per la configurazione di SDK le modifiche dal `capptain` a `azureEngagement`.

Prima della migrazione:

    window.capptain = {
      appId: ...,
      [...]
    };

Dopo la migrazione:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

La stringa di connessione per l'applicazione viene visualizzata nel portale di Azure.

### <a name="javascript-apis"></a>API JavaScript

L'oggetto JavaScript globale `window.capptain` è stato rinominato `window.azureEngagement` , ma è possibile utilizzare il `window.engagement` alias per le chiamate API. È possibile utilizzare l'alias per definire la configurazione di SDK.

Ad esempio, `capptain.deviceId` diventa `engagement.deviceId`, `capptain.agent.startActivity` diventa `engagement.agent.startActivity`e così via.
