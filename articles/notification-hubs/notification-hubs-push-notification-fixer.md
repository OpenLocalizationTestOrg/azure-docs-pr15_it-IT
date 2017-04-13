<properties 
    pageTitle="Notifica Azure hub - linee guida per la diagnosi" 
    description="Linee guida su come diagnosticare i problemi comuni con gli hub di notifica di Azure." 
    services="notification-hubs" 
    documentationCenter="Mobile" 
    authors="ysxu" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="NA" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs---diagnosis-guidelines"></a>Azure notifica hub - linee guida per la diagnosi

##<a name="overview"></a>Panoramica

Domande più frequenti è ascoltare dai clienti di Azure notifica hub è modalità per determinare il motivo per cui non vedono una notifica inviata dal loro back-end applicazione venga visualizzato uno dei dispositivi client - dove e perché sono state rimosse le notifiche e come risolvere il problema. In questo articolo verranno esaminate tra i vari motivi perché notifiche scada o non finiscano nei dispositivi. Esamineremo anche tramite modi in cui è possibile analizzare e individuare la causa principale. 

Prima di tutto, è fondamentale per comprendere come hub di notifica di Azure inserisce notifiche per i dispositivi.
![][0]

In un flusso di notifica tipico invia il messaggio viene inviato dal **back-end dell'applicazione** di **Azure notifica Hub (NH)** che a sua volta esegue operazioni su tutte le registrazioni prendere in considerazione i tag configurato e le espressioni di tag per determinare "destinazioni" ad esempio tutte le attività che devono ricevere una notifica push. Le registrazioni possono occupare una o tutte le piattaforme supportate - iOS, Google, Windows, Windows Phone, dispositivo Kindle e Baidu per Android Cina. Una volta stabilite le destinazioni, NH quindi push notifiche, dividere in più batch delle registrazioni, per la piattaforma del dispositivo specifico **Push Notification Service (PNS)** - ad esempio APN per Apple, GCM per Google e così via. NH autentica con i rispettivi PNS in base alle credenziali impostate nel portale classica di Azure nella pagina Configura notifica Hub. Il PNS inoltra quindi le notifiche alle rispettive **sui dispositivi client**. Verrà visualizzata la piattaforma consigliata per la ricezione delle notifiche push e si noti che la parte finale della recapito entrerà in vigore tra la piattaforma PNS e il dispositivo in modo. Pertanto abbiamo quattro componenti principali - *client*, *applicazione back-end*, *Azure notifica hub (NH)* e *i servizi di notifica Push (PNS)* e uno di questi può causare le notifiche tramite il collegamento. Ulteriori informazioni su questa architettura è disponibile nella [Notifica hub Panoramica].

Errore per la ricezione delle notifiche può verificarsi durante la prova iniziale/gestione temporanea fase che potrebbe indicare un problema di configurazione o può verificarsi in produzione in cui tutte o alcune delle notifiche stia ottenendo eliminati che indica un'applicazione più approfondita o messaggistica problema motivo. Nella sezione sotto esamineremo diversi scenari di notifiche ignorati che vanno dal comuni per il tipo rari, che alcuni dei quali può risultare più evidenti e alcuni altri non molto. 

##<a name="azure-notifications-hub-mis-configuration"></a>Configurazione errata di Azure Hub di notifiche 

È necessario autenticarsi nel contesto di applicazione dello sviluppatore la possibilità di inviare notifiche PNS rispettivi Azure hub di notifica. Ciò è possibile dello sviluppatore creazione di un account di sviluppo e la piattaforma rispettiva (Google, Apple, Windows e così via) e quindi la registrazione loro applicazione in cui ricevono credenziali che devono essere configurati nel portale nella sezione configurazione hub di notifica. Se delle notifiche tramite, dovrebbe essere primo passaggio per garantire che le credenziali corrette sono configurate nell'Hub notifica loro corrispondenza con l'applicazione creata sotto il proprio account di sviluppo specifici della piattaforma. L' [Guida introduttiva esercitazioni] si troverà utile per l'invio tramite questo processo in modo passo dopo passo. Ecco alcune configurazioni errate comuni:

1. **Generale**
 
    una) effettuare assicurarsi che il nome di hub notifica (senza errori di battitura) è la stessa:

    - Nel punto in cui si sta registrando dal client, 
    - Nel punto in cui si intende inviare notifiche da back-end,  
    - Nel punto in cui sono state configurate le credenziali PNS e 
    - Con credenziali sa è stato configurato sul client e back-end. 
        
    b) assicurarsi che l'opzione che si usano le stringhe di configurazione corrette SA il client e back-end dell'applicazione. Come regola, è necessario utilizzare **DefaultListenSharedAccessSignature** nel client e **DefaultFullSharedAccessSignature** nel back-end dell'applicazione (che consente di essere in grado di inviare la notifica di NH)

2. **Configurazione di Apple Push Notification Service (APNS)**
 
    È necessario mantenere due hub - uno per produzione e un altro per il testing allo scopo. Di conseguenza, durante il caricamento di che si intende utilizzare in modalità sandbox ambiente a un hub distinto e il certificato che si intende utilizzare in produzione a un hub distinto. Non tentano di caricare diversi tipi di attestati e certificati allo stesso hub come potrebbe causare problemi di comunicazione verso il basso la riga. Se è necessario in una posizione in cui è stato caricato inavvertitamente diversi tipi di certificato allo stesso hub, è consigliabile eliminare hub e ricominciare dall'inizio. Se per qualche motivo non si è possibile eliminare l'hub quindi almeno, è necessario eliminare tutte le registrazioni esistenti dall'hub. 

3. **Configurazione di Google Cloud messaggistica (GCM)** 

    ) assicurarsi che l'opzione che si desidera abilitare "Google Cloud messaggistica per Android" nel progetto cloud. 
    
    ![][2]
    
    b) assicurarsi che l'opzione Crea una chiave di Server"" durante la ricezione delle credenziali quali NH verrà utilizzato per eseguire l'autenticazione con GCM. 
    
    ![][3]
    
    c) rendere assicurarsi che sia stato configurato "ID progetto" nel client è un'entità interamente numerica che è possibile ottenere nel dashboard:
    
    ![][1]

##<a name="application-issues"></a>Problemi delle applicazioni

1) **Tag / Tag espressioni**

Se si usa i tag o le espressioni tag segmento al pubblico, è sempre possibile che quando si intende inviare la notifica, non esiste destinazione viene trovato in base alle espressioni tag/tag specificate nella chiamata Invia. È consigliabile esaminare le registrazioni per assicurarsi che i tag corrispondono quando si invia una notifica e quindi verifica la conferma di notifica solo dai client con le registrazioni. Ad esempio Se si invia una notifica con tag "Sport" tutte le registrazioni con NH eseguite con segnala tag "Politica", non verrà inviato a qualsiasi dispositivo. Un caso complesso può comportare nel punto in cui è registrato solo con "Tag A" o "Tag B", ma durante l'invio di notifiche, le espressioni di tag di destinazione "Tag & & Tag a B". Nella sezione suggerimenti di diagnosticare automaticamente modi in cui è possibile esaminare le registrazioni insieme i contrassegni che hanno. 

2) **Problemi di modello**

Se si utilizzano modelli, verificare che si stanno seguendo le istruzioni riportate nella [Guida di modello]. 

3) **Registrazioni non valide**

Supponendo che l'Hub di notifica è stato configurato correttamente e tutte le espressioni di tag/tag sono state utilizzate correttamente risultante trovare destinazioni valido a cui è necessario inviare le notifiche, NH genera più elaborazione batch in parallelo - ogni batch invio di messaggi da un set di registrazioni. 

> [AZURE.NOTE] Poiché facciamo l'elaborazione in parallelo, è non garantisce l'ordine in cui verranno recapitate le notifiche. 

A questo punto Azure notifiche Hub è ottimizzato per un modello di recapito messaggio "una volta in molti". Questo significa che si tenta di una ripetizione cessazione in modo che nessun notifiche vengono inviate più volte a un dispositivo. A tale scopo è esaminare le registrazioni e assicurarsi che solo un messaggio viene inviato per identificatore del dispositivo prima di inviare il messaggio effettivamente la PNS. Come ogni batch viene inviata a PNS, che a sua accetti e convalida le registrazioni, è possibile che il PNS rileva un messaggio di errore con uno o più delle registrazioni in batch, viene restituito un errore di Azure NH e si arresta in modo da eliminare completamente il batch di elaborazione. Questa operazione è particolarmente vera con APN che utilizza un protocollo flusso TCP. Anche se è in corso ottimizzate per all'estrema dopo consegna, in questo caso è rimuovere la registrazione errore dal database e quindi ripetere il recapito per il resto dei dispositivi del batch.

È possibile ottenere informazioni sugli errori per il tentativo di recapito non riuscito contro una registrazione utilizzando le API REST di Azure notifica hub: [per ogni messaggio telemetria: ottenere telemetria messaggio di notifica](https://msdn.microsoft.com/library/azure/mt608135.aspx) e [PNS Feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx). Vedere [SendRESTExample](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample) , ad esempio codice.

##<a name="pns-issues"></a>Problemi PNS

Dopo il messaggio di notifica è stato ricevuto da PNS rispettivi si trova la sua responsabilità per il recapito della notifica al dispositivo. Azure hub di notifica non è compresa nell'immagine qui e non ha alcun controllo quando o se la notifica verrà recapitato al dispositivo. Poiché i servizi di notifica di piattaforma sono molto affidabili, le notifiche comandi sono particolarmente utili per raggiungere i dispositivi in pochi secondi dalla PNS. Se il PNS è limitazione Azure notifica hub applicare la strategia di un'interruzione esponenziale e se la PNS rimane non è raggiungibile per 30 minuti quindi esiste un criterio nella posizione in cui scadenza e rilasciare in modo definitivo i messaggi. 

Se un PNS tenta di inviare una notifica, ma il dispositivo è offline, la notifica è archiviata dal PNS per un periodo di tempo limitato e recapitata al dispositivo quando non è più disponibile. Solo una notifica recente per una determinata app è archiviata. Se più notifiche quando il dispositivo è offline, ogni nuova notifica, la notifica precedente verranno ignorati. Questo comportamento di mantenere la più recente notifica viene considerato coalescing le notifiche di APNS e compressione di GCM (che usa una chiave di compressione). Se il dispositivo rimane offline per troppo tempo, le notifiche vengono memorizzate per renderla verranno ignorate. Origine - [indicazioni APN] & [indicazioni GCM]

Con Azure notifica hub - è possibile passare una chiave riunire più tramite un'intestazione HTTP utilizzando il modello generico `SendNotification` API (ad esempio per .NET SDK- `SendNotificationAsync`) che accetta anche intestazioni HTTP che vengono passate come consiste nel PNS rispettivi. 

##<a name="self-diagnose-tips"></a>Diagnosticare automaticamente suggerimenti

Qui verranno esaminati i vari percorsi per la diagnosi e root causano problemi Hub di notifica:

###<a name="verify-credentials"></a>Verificare le credenziali

1. **Portale per sviluppatori PNS**

    Verificare le rispettive PNS portale per sviluppatori di (APN, GCM, WNS e così via) con la [Guida introduttiva esercitazioni].

2. **Portale classica Azure**

    Fare clic sulla scheda Configura rivedere e confrontare le credenziali con quelle ottenute dal portale per sviluppatori di PNS. 

    ![][4]

###<a name="verify-registrations"></a>Verificare le registrazioni

1. **Visual Studio**

    Se si utilizza Visual Studio per lo sviluppo è possibile connettersi a Microsoft Azure e visualizzare e gestire un insieme di Azure servizi inclusi Hub di notifiche da "Esplora Server". Questa operazione è particolarmente utile per l'ambiente di sviluppo/test. 

    ![][9]

    È possibile visualizzare e gestire tutte le registrazioni nel tuo fulcro ben suddivisi per piattaforma nativa o registrazione di modello, eventuali tag, PNS identificatore, id di registrazione e la data di scadenza. È anche possibile modificare una registrazione in tempo reale - è utile ad esempio se si desidera modificare i tag. 

    ![][8]
 
    > [AZURE.NOTE] La funzionalità di Visual Studio per modificare le registrazioni deve essere utilizzata solo durante dev/test con un numero limitato di registrazioni. Se è necessario risolvere le registrazioni in blocco, è consigliabile utilizzare la funzionalità di registrazione di esportazione/importazione descritto qui di seguito - [Esportazione/importazione registrazioni] (https://msdn.microsoft.com/library/dn790624.aspx)

2. **Esplora Bus di servizio**

    Molti clienti utilizzano ServiceBus soluzioni descritte in questo documento - [ServiceBus Explorer] per visualizzare e gestire il relativo hub di notifica. Si tratta di un progetto open source disponibile code.microsoft.com - [codice ServiceBus Explorer]

###<a name="verify-message-notifications"></a>Verificare le notifiche di messaggi

1. **Portale classica Azure**

    È possibile accedere alla scheda "Debug" per inviare le notifiche di test per i clienti senza la necessità di back-end qualsiasi servizio alto e in esecuzione. 

    ![][7]

2. **Visual Studio**

    È anche possibile inviare le notifiche di test da volevano realizzare di Visual Studio:

    ![][10]

    È possibile leggere informazioni sulla funzionalità explorer Visual Studio notifica Hub Azure, 
    
    - [Panoramica di Visual Studio Server Explorer]
    - [Post di Blog Esplora Server VS - 1]
    - [Post di Blog Esplora Server VS - 2]

###<a name="debug-failed-notifications-review-notification-outcome"></a>Eseguire il debug notifiche non riuscite / esaminare i risultati di notifica

**Proprietà EnableTestSend**

Quando si invia una notifica tramite hub di notifica, inizialmente sufficiente Ottiene messe per NH per l'elaborazione per scoprire tutte le destinazioni e quindi infine NH invia la PNS. Questo errore indica che quando si utilizza API REST o i client SDK, il risultato restituito della chiamata Invia solo significa che il messaggio è completata nella coda con Hub di notifica. Non offre alcune informazioni su cosa è successo quando NH ottenuto non hanno un limite inviare il messaggio a PNS. Se la notifica non è arrivato al dispositivo client, è possibile che durante il tentativo di NH di recapitare il messaggio a PNS si è verificato un errore, ad esempio le dimensioni di payload superato il massimo consentito tramite il PNS o le credenziali configurate nel NH non valido e così via. Per ottenere un quadro errori PNS, è stata introdotta una proprietà denominata [EnableTestSend caratteristica]. Questa proprietà viene attivata automaticamente quando si inviano messaggi di prova dal portale o client Visual Studio e pertanto consente di visualizzare informazioni dettagliate debug. È possibile utilizzare questa tramite API secondo l'esempio di .NET SDK in cui è disponibile ora e si verrà aggiunto a tutti i client SDK alla fine. Per utilizzare questa con la chiamata al resto, è sufficiente aggiungere un parametro stringa di query denominato "test" alla fine della chiamata invia ad esempio 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*Esempio (.NET SDK)*
 
Si supponga di che utilizzare .NET SDK per inviare una notifica di tipo avviso popup nativo:

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
 
`result.State`verrà semplicemente stato `Enqueued` al termine dell'esecuzione senza qualsiasi approfondita Dov'è il push. È ora possibile utilizzare il `EnableTestSend` proprietà booleana durante l'inizializzazione di `NotificationHubClient` e ottenere informazioni dettagliate sullo stato relative agli errori di PNS durante l'invio della notifica. La chiamata di invio richiederà più tempo per restituire perché è restituiti solo dopo che NH ha inviato la notifica a PNS per determinare il risultato. 
 
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);
    
    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);
    
    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }

*Esempio di Output*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong
 
Questo messaggio indica uno dei due credenziali non valide sono configurate in hub notifica o un problema con le registrazioni dell'hub e il corso consigliato, è possibile eliminare la registrazione e consentire il cliente doverlo ricreare prima di inviare il messaggio. 
 
> [AZURE.NOTE] Si noti che l'uso di questa proprietà in modo rilevante è limitato e pertanto è necessario utilizzare solo questo nell'ambiente di sviluppo/test con numero limitato di registrazioni. È solo inviare notifiche debug ai 10 dispositivi. Abbiamo anche un limite di elaborazione debug invia a 10 al minuto. 

###<a name="review-telemetry"></a>Rivedere telemetria 

1. **Utilizzare Azure portale classica**

    Il portale consente di ottenere una panoramica di tutte le attività nell'Hub di notifica. 
    
    ) dalla scheda "dashboard" è possibile visualizzare una visualizzazione aggregata delle registrazioni, le notifiche, nonché gli errori per ogni piattaforma. 
    
    ![][5]
    
    b) è anche possibile aggiungere molti altri parametri specifici di piattaforma dalla scheda "Monitor" per esaminare più approfondita particolarmente eventuali errori specifici nelle PNS restituiti quando NH tenta di inviare la notifica per la PNS. 
    
    ![][6]
    
    c) si devono iniziare con esaminare le **I messaggi in arrivo**, **Le operazioni di registrazione**, **Notifiche riuscite** e quindi passare alla sezione per ogni scheda piattaforma per controllare gli errori specifici PNS. 
    
    d) se si ha l'hub di notifica quindi verrà visualizzato l'errore di autenticazione PNS è configurato correttamente con le impostazioni di autenticazione. Verrà visualizzata una buona indicazione per controllare le credenziali PNS. 

2) **L'accesso**

Altri dettagli sul- 

- [Accesso telemetria livello di programmazione]
- [Accesso di telemetria tramite esempio API] 

> [AZURE.NOTE] Telemetria diverse funzionalità relative ad esempio **Le registrazioni di esportazione/importazione** **Telemetria accesso tramite API** e così via sono disponibili solo nel livello Standard. Se si tenta di usare queste caratteristiche se ci si trova in disponibile o livello base si otterrà messaggio dell'eccezione per questo effetto durante l'uso di SDK e un HTTP 403 (accesso negato) quando si tenta di usarle direttamente dall'API REST. Assicurarsi che sia stato spostato fino a Standard a più livelli tramite il portale classica di Azure.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png
 
<!-- LINKS -->
[Panoramica di hub di notifica]: notification-hubs-push-notification-overview.md
[Esercitazioni introduttive]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Guida di modello]: https://msdn.microsoft.com/library/dn530748.aspx 
[Indicazioni APN]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[Indicazioni GCM]: http://developer.android.com/google/gcm/adv.html
[Esportazione/importazione registrazioni]: http://msdn.microsoft.com/library/dn790624.aspx
[Esplora ServiceBus]: http://msdn.microsoft.com/library/dn530751.aspx
[Codice ServiceBus Explorer]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Panoramica di Visual Studio Server Explorer]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Post di Blog Esplora Server VS - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Post di Blog Esplora Server VS - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[Caratteristica EnableTestSend]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[Accesso telemetria livello di programmazione]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Accesso di telemetria tramite esempio API]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

 