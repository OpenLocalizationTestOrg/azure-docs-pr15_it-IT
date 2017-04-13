<properties 
   pageTitle="Guide di risoluzione dei problemi di Azure impegno per dispositivi mobili" 
   description="Guida alla risoluzione dei problemi per Azure impegno per dispositivi mobili" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Azure impegno per dispositivi mobili - Guida alla risoluzione dei

## <a name="introduction"></a>Introduzione
La Guida alla risoluzione dei problemi seguente utili per comprendere meglio le cause principali di alcuni problemi comune e verrà consentono di risolvere autonomamente. 

## <a name="general"></a>Generale

In generale, è necessario assicurarsi sempre le operazioni seguenti:

1. Verificare che si è fuori tutti i passaggi necessari per l'integrazione come descritto in corso [Introduzione esercitazioni](mobile-engagement-windows-store-dotnet-get-started.md) delle
2. Si utilizza la versione più recente della piattaforma SDK. 
3. Testare su un dispositivo effettivo e un emulatore sono specifici per emulatore solo alcuni problemi. 
4. Non si raggiunge eventuali limiti/limita da Mobile coinvolgimento indicate [di seguito](../azure-subscription-service-limits.md)
5. Se non si è possibile connettersi a back-end servizio coinvolgimento Mobile o vedere dati non vengono caricati continuamente quindi assicurarsi che non siano senza interventi di assistenza in corso selezionando [qui](https://azure.microsoft.com/status/)

## <a name="monitor-issues"></a>Problemi relativi al 'Monitor'

### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>Non viene visualizzato il dispositivo compare nella scheda Monitor
Scheda Monitor mostra i dispositivi connessi al piattaforma Mobile coinvolgimento in tempo reale. Se il debug di un emulatore e un dispositivo, è necessario verificare almeno una sessione di seguito. Se l'app è stata distribuita, quindi verrà visualizzato l'indicatore di sessioni attive riflettere i dispositivi connessi alla piattaforma in tempo reale. 

Se non si visualizzano il dispositivo nella scheda Monitor è probabilmente un problema di integrazione SDK. Alcune operazioni da eseguire per risolvere i problemi comuni sono i seguenti:

1. Verificare che si usa la stringa di connessione corretto in app per dispositivi mobili e che provenga dalla sezione tasti SDK e non la sezione chiavi API. La stringa di connessione si connette l'app per dispositivi mobili per l'istanza dell'app Mobile impegno in cui verrà visualizzato il dispositivo nella scheda Monitor. 
2. Per piattaforma Windows - se la pagina esegue l'override di `OnNavigatedTo` metodo, assicurarsi di chiamare `base.OnNavigatedTo(e)`.
3. Se si integra coinvolgimento Mobile in un'app per dispositivi mobili esistente, quindi è anche possibile assicurarsi che non vengono eliminati tutti i passaggi esaminando la procedura di integrazione avanzata [qui](mobile-engagement-windows-store-integrate-engagement.md)
4. Assicurarsi che si intende inviare almeno una schermata/attività sostituendo la pagina con EngagementActivity a seconda della piattaforma che si lavora come descritto nella [Guida introduttiva esercitazioni](mobile-engagement-windows-store-dotnet-get-started.md).

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>Scheda Monitor che mostra una sessione viene visualizzato anche quando ho disconnessa o chiusa app / emulatore. 
Se si è l'unico connesso a questo punto la piattaforma e si utilizza un emulatore per aprire l'app tratta probabilmente quirks emulatore. In generale, è necessario assicurarsi che è tornare alla schermata principale dell'emulatore per la sessione di app disconnettere correttamente. Inoltre, su piattaforma Windows, durante il debug con Visual Studio, potrebbe essere necessario verificare che in Visual Studio, passare alla barra dei menu di **Eventi del ciclo di vita** si fa clic su **sospensione** realmente chiudere la sessione. Per informazioni dettagliate, vedere [l'esercitazione di Windows](mobile-engagement-windows-store-dotnet-get-started.md) . 

## <a name="analytics-issues"></a>Problemi di 'Analitica'

### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>Posso non viene visualizzato uno qualsiasi dei dati o aggiornare i dati nella scheda Analitica 
Dati Analitica viene ricalcolati a intervalli regolari e, possono essere necessari fino a 24 ore per l'aggiornamento. Questi dati non sono in tempo reale e si vedrà aggiornata all'interno di questo periodo di tempo di 24 ore.
Verificare tuttavia che si sta inviando i almeno una schermata o un'attività a back-end piattaforma di uno dei due override almeno una pagina con `EngagementActivity` o di chiamare `SendActivity` in modo esplicito. 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>Data/ora acquisito in modo non corretto per un dispositivo vengono visualizzate nella scheda Analitica
Periodo di tempo per Analitica è in base alla data le impostazioni degli utenti dei dispositivi. Verificare che il dispositivo è la data impostata correttamente. 

## <a name="segment-issues"></a>Problemi di 'Segmento'

### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>Dopo aver creato un segmento e viene visualizzata in grigio o non vengono visualizzate qualsiasi tipo di dati
Creazione di segmento non è in tempo reale al momento. Viene calcolata nello stesso momento mentre i dati analitica vengono aggregati e in modo che potrebbe richiedere fino a 24 ore. Controllare nuovamente in un secondo momento ma nel frattempo è inoltre necessario accertarsi che l'App per dispositivi mobili sono effettivamente inviando i dati in base ai quali si sono i segmenti. Ad esempio Se un evento indicato "foo" non viene inviato da un dispositivo mobile quindi sarebbero tutti i dati per un segmento creato con EventName segmento = foo come criterio. Controllare inoltre l'integrazione di SDK per assicurarsi che l'app per dispositivi mobili invia i dati in modo corretto. 

## <a name="reach-or-push-notifications-issues"></a>Problemi 'Raggiunga' o delle notifiche Push

### <a name="my-push-messages-are-not-being-delivered"></a>I messaggi di push non vengono inviati 

1. Provare a inviare notifiche a un dispositivo di test prima di tutto per fare in modo che tutti i componenti - app per dispositivi mobili, SDK e il servizio siano collegati correttamente ed è possibile recapitare le notifiche push. 
2. Invia sempre la più semplice 'notifica fuori dell'app' prima di tutto tramite una campagna di cui non è stata pianificata e non dispone di un criterio di gruppo di destinatari specificato. Si tratta nuovamente per verificare il corretto funzionamento di connettività di notifica. 
3. Se si verificano problemi in il recapito delle notifiche nell'app anche è un valido punto di partenza per provare a inviare prima di tutto una notifica di fuori dell'app. 
4. Assicurarsi che la 'nativo Push' è configurato correttamente per l'app per dispositivi mobili. A seconda della piattaforma ma verrà riguarda chiavi (Android, Windows) o certificati (iOS). Vedere [interfaccia utente - impostazioni](mobile-engagement-user-interface-settings.md)
5. Disconnettersi da app notifiche potrebbero essere bloccate anche con l'utente tramite il sistema operativo per dispositivi mobili in modo assicurarsi che non si tratta le maiuscole/minuscole. 
6. Assicurarsi che non si sta impostando l'opzione *Ignora pubblico push verrà inviato agli utenti tramite l'API* nella sezione **campagna** di una campagna portata perché in questo modo che le notifiche push solo possono essere inviate tramite API. 
7. Verificare che si sta verificando campagna push con dispositivo sia connesso tramite WIFI e telefono rete operatore per eliminare la connessione di rete come origine possibili dei problemi.
8. Assicurarsi che il sistema data/ora nell'emulatore sia corretta poiché qualsiasi dispositivo non è sincronizzato verrà anche interferisce con del servizio notifica Push per la ricezione delle notifiche. 

Più specifici della piattaforma risoluzione dei problemi di istruzioni riportate di seguito:

1. **iOS** 

    - Assicurarsi che i certificati sono valide e in corso per iOS delle notifiche Push. 
    - Verificare che si sta configurando correttamente un certificato di *produzione* nell'app Mobile coinvolgimento. 
    - Verificare che si stanno verificando in un *dispositivo reale, fisica.* Simulatore di iOS non è possibile elaborare messaggi push.
    - Assicurarsi che l'identificatore di raggruppamento è configurato correttamente in app per dispositivi mobili. Vedere le istruzioni riportate [di seguito](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)
    - Durante la verifica, utilizzare la distribuzione "Ad Hoc" nel profilo di provisioning per dispositivi mobili. Non sarà possibile ricevere una notifica se l'app viene compilato utilizzando "Debug"

2. **Android**

    - Assicurarsi di avere specificato il numero di progetto corretto nel file AndroidManifest.xml dell'app per dispositivi mobili seguita da carattere \n. 
    
            <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
        
    - Assicurarsi che non sono mancanti o non configurato tutte le autorizzazioni nel file manifesto Android 
    - Assicurarsi che il numero di progetto per aggiungere l'app client dallo stesso account in cui è stato ottenuto il tasto Server GCM. Qualsiasi mancata corrispondenza tra i due impedirà il push di in uscita. 
    - Se si ricevono le notifiche di sistema ma non in-app tramite revisione [specificare un'icona per la sezione notifiche](mobile-engagement-android-get-started.md) come probabile che non si specifica l'icona corretta nel file manifesto Android. 
    - Se si invia una notifica BigPicture, quindi garantire che se si dispone di immagini esterne server quindi devono essere in grado di supportare HTTP "OTTIENI" e "Capo".

3. **Windows**
    
    - Assicurarsi che è associato l'app un'app di Windows Store valida. In Visual Studio - è necessario fare clic con il pulsante destro del progetto e selezionare l'opzione "Associare App con archivio" e selezionare l'app che è stato creato in Windows Store. L'app di Windows Store dovrebbe essere lo stesso nel punto in cui è stato ottenuto le credenziali push nativi per configurare nel portale di impegno Mobile.
    - Se si ricevono le notifiche push di fuori dell'app, ma non app notifiche con `EngagementOverlay` integrazione quindi assicurarsi che esiste un elemento di griglia radice della pagina. Usa EngagementOverlay il primo elemento "Griglia" trova nel file xaml per aggiungere web due visualizzazioni nella pagina. Se si desidera individuare nel punto in cui verranno impostate visualizzazioni web, è possibile definire una griglia denominata "EngagementGrid", ad esempio, tuttavia è necessario assicurarsi che c'è sufficiente altezza e larghezza per il web successiva di due visualizzazioni che viene visualizzata la notifica e il seguente annuncio notifica all'interno dell'applicazione:
        
            <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>Dopo aver creato un notifica push/annuncio/campagna e anche dopo che l'utente ha inviato la notifica, viene visualizzato come 'Active'. Che cosa significa? 
**Campagna** creata in coinvolgimento Mobile è chiamato in modo poiché si tratta di un significato di notifica push lunga come nuovi dispositivi connettono alla piattaforma impegno per dispositivi mobili, viene inviate automaticamente notifica che configurare qui, come soddisfano il criterio che si imposta la campagna. Si tratta non una configurazione ripresa singola notifica uno. È necessario manualmente fare clic sul pulsante **Fine** per terminare la campagna in modo da non inviare notifiche ulteriormente. 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>Dopo aver creato una campagna push e si ricevono le notifiche correttamente, tuttavia ogni volta che apre l'App, viene visualizzato la stessa notifica anche quando è stato prese in considerazione prima? 
Questo problema può verificarsi durante la verifica e se si usa emulatori o alcuni test framework come TestFlight. Cosa succede Ecco che in ogni app eseguire istanza, sia durante l'acquisizione di un nuovo DeviceID e inviarlo al nostro back-end che causa la piattaforma Mobile impegno considerare come un nuovo dispositivo e invio della notifica. 

## <a name="getting-support"></a>Ottenere supporto tecnico

Se non si riesce a risolvere il problema se stessi, è possibile:

1. Cercare il problema in thread esistenti nel forum StackOverflow e [forum MSDN](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement) e se non quindi porre una domanda non esiste. 
2. Se si trova una caratteristica mancante quindi aggiungere/voto della richiesta in al [forum Uservoicehttp](https://feedback.azure.com/forums/285737-mobile-engagement/)
3. Se si ha Microsoft supportano aperto un intervento del supporto fornendo i dettagli seguenti: 
    - ID abbonamento Azure
    - Piattaforma (ad esempio, iOS, Android e così via)
    - ID applicazione
    - ID campagna (per i problemi di notifica push)
    - ID dispositivo
    - Versione mobile coinvolgimento SDK (ad esempio SDK Android v2.1.0)
    - Dettagli dell'errore con messaggio di errore e scenario
