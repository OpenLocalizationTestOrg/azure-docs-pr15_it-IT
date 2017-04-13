<properties
    pageTitle="Hub di notifica Azure - domande frequenti domande frequenti"
    description="Domande frequenti sulla progettazione/implementazione delle soluzioni su hub di notifica"
    services="notification-hubs"
    documentationCenter="mobile"
    authors="ysxu"
    manager="erikre"
    keywords="notifiche push, le notifiche push, le notifiche push iOS, le notifiche push android, push ios, android push"
    editor="" />

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="yuaxu" />

#<a name="push-notifications-with-azure-notification-hubs---frequently-asked-questions"></a>Notifiche con gli hub di notifica Azure - push domande frequenti

##<a name="general"></a>Generale
###<a name="1---what-is-the-price-model-for-notification-hubs"></a>1. che cos'è il modello di prezzo per hub notifica?
Notifica hub è disponibile in tre livelli:

* **Free** - ottenere fino a 1 milione push per abbonamento al mese.
* **Base** - get 10 milioni push per abbonamento mese come una previsione, con le opzioni di crescita quota.
* **Standard** - ottenere 10 milioni push per abbonamento mese come una previsione, con quota aumentare opzioni più capabilties di telemetria RTF.

Sono disponibili i dettagli più recenti nella pagina [Prezzi hub di notifica] . I prezzi stabilito a livello di abbonamento e in base al numero di inchieste di notifica push in modo che non è importante quanti spazi dei nomi o notifica hub è stato creato in abbonamento Azure.

Livello **gratuito** è disponibile a scopo di sviluppo con nessuna garanzia contratto di servizio. Questo livello potrebbe essere un buon punto di partenza per coloro che per esplorare le funzionalità delle notifiche push hub di notifica di Azure, potrebbe non essere ottimale per il supporto per le applicazioni di grandi dimensioni.

**Base** & livelli**Standard** disponibili per l'uso di produzione con le seguenti funzionalità chiave attivato *solo per lo Standard a più livelli*:

- *Telemetria RTF* - offerta hub notifica al numero di funzionalità per esportare i dati di telemetria, nonché le informazioni di registrazione di notifica per la visualizzazione offline e l'analisi.
- *Affitto multiple* - ideale se si sta creando un'app per dispositivi mobili con gli hub di notifica per supportare più tenant. In questo modo è possibile impostare le credenziali di servizi di notifica Push (PNS) livello dello spazio Hub di notifica per l'app e quindi è possibile isolare il tenant fornendo loro singoli hub in questo spazio dei nomi comune. In questo modo la semplicità di manutenzione mentre mantenere i tasti SA Invia e Ricevi le notifiche push dall'hub notifica separati per ogni tenant garantire sovrapposizione non cross-tenant.
- *Programmata Push* - consente di pianificare le notifiche push, in cui deve essere successivamente messe mandate.
- *Importazione in blocco* - consente di importare le registrazioni in blocco.

###<a name="2---what-is-the-notification-hubs-sla"></a>2. che cos'è il contratto di servizio di notifica hub?
Per livelli di **base** e **Standard** hub di notifica, è garantire che almeno 99,9% di tempo, opportunamente configurato le applicazioni saranno in grado di inviare le notifiche push o eseguire operazioni di gestione di registrazione rispetto a un Hub di notifica distribuito all'interno di un livello supportato. Per ulteriori informazioni sul contratto di servizio, visitare la pagina [Contratto di servizio di notifica hub] .

> [AZURE.NOTE] Ci sono garanzie contratto di servizio per il segmento tra il servizio di notifica di piattaforma e il dispositivo dall'hub notifica dipendono dal provider di servizi esterni piattaforma recapitare la notifica push al dispositivo.

###<a name="3---which-customers-are-using-notification-hubs"></a>3. clienti che usano gli hub di notifica.
Sono disponibili numerosi clienti utilizzando notifica hub con pochi quelli degni di nota seguenti:

* Sochi 2014 – 100s di gruppi di interesse, milioni di 3 + dispositivi, notifica 150 milioni inviato in 2 settimane. [CaseStudy - Sochi]
* Skanska - [CaseStudy - Skanska]
* Volte Seattle - [CaseStudy - Seattle volte]
* Mural.ly - [CaseStudy - Mural.ly]
* 7Digital - [CaseStudy - 7Digital]
* Bing App-10s milioni di dispositivi, invio di notifiche milioni di 3/giorno.

###<a name="4-how-do-i-upgrade-or-downgrade-my-notification-hubs-to-change-my-service-tier"></a>4. come aggiornare o eseguire il downgrade personale hub di notifica per modificare il livello di servizio
Accedere al [Portale classica di Azure], fare clic su Bus di servizio e scegliere lo spazio dei nomi quindi l'hub di notifica. Nella scheda scala, sarà possibile modificare il livello di servizio di notifica hub.

![](./media/notification-hubs-faq/notification-hubs-classic-portal-scale.png)

##<a name="design--development"></a>Progettazione e sviluppo
###<a name="1---which-server-side-platforms-do-you-support"></a>1. quali piattaforme sul lato server è supportata?
Sono disponibili [esempi completi] e SDK per .NET, Java, PHP, Python, Node in modo che un back-end app da impostare per comunicare con gli hub di notifica tramite qualsiasi di queste piattaforme. Notifica hub API dipendono dal resto interfacce in modo che è possibile scegliere di direttamente parlare con quelli invece se non si vuole aggiungere una relazione aggiuntiva. Per ulteriori informazioni sono disponibili nella pagina [NH - API REST] .

###<a name="2---which-client-platforms-do-you-support"></a>2. quali piattaforme client supportate?
Sono supportate le notifiche push invio al [sistema operativo Apple iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Universale di Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android Cina (tramite Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) & [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), piattaforme [App Chrome](notification-hubs-chrome-push-notifications-get-started.md) e [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari) . Per un elenco completo di Guida introduttiva esercitazioni affrontare invio delle notifiche push per le piattaforme, visitare la pagina [NH - Guida introduttiva esercitazioni] .

###<a name="3---do-you-support-smsemailweb-notifications"></a>3. supportano le notifiche tramite posta elettronica/SMS/web?
Notifica hub è progettato principalmente per inviare notifiche a App per dispositivi mobili utilizzando le piattaforme sopra elencate. La possibilità di inviare posta elettronica o SMS avvisi; non sono ancora disponibili tuttavia piattaforme di terze parti che forniscono queste funzionalità possono essere integrate con gli hub di notifica di inviare le notifiche push nativo tramite [App Mobile Azure].

Notifica hub anche non forniscono un push nel browser notifica recapito servizio-the-pronte. I clienti possono scegliere di implementare questo utilizzando SignalR sopra le piattaforme supportate sul lato server. Se si desidera utilizzare per l'invio di notifiche alle App browser in sandbox Chrome, vedere l' [esercitazione App Chrome].

###<a name="4---what-is-the-relation-between-azure-mobile-apps-and-azure-notification-hubs-and-when-do-i-use-what"></a>4. che cos'è la relazione tra le app Mobile Azure e Azure notifica hub e quando si usa cosa?
Se si dispone di un esistente app per dispositivi mobili di back-end e si desidera aggiungere la funzionalità Invia notifiche push è possibile utilizzare gli hub di notifica di Azure. Se si desidera impostare il back-end di app per dispositivi mobili da zero è necessario considerare uso App Mobile Azure. Un'App Mobile Azure esegue automaticamente il provisioning di un Hub di notifica per poter inviare facilmente le notifiche push back-end app per dispositivi mobili. Prezzi per le App per dispositivi mobili Azure include le spese di base per un Hub di notifica e si paga solo quando superano i push inclusi. Ulteriori informazioni sui costi sono disponibili nella pagina [App servizio prezzi] .

###<a name="5---how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>5. quanti dispositivi è possibile supportare se si invia le notifiche push tramite hub notifica?
Fare riferimento alla pagina di [Notifica hub prezzi] per informazioni dettagliate sul numero di dispositivi supportati.

Per alcuni scenari, se è necessario il supporto per più di 10.000.000 dispositivi registrati, [contattarci](https://azure.microsoft.com/overview/contact-us/) direttamente e si consente di ridimensionare la soluzione.

###<a name="6---how-many-push-notifications-can-i-send-out"></a>6. possibile il numero di notifiche push inviano?
A seconda del livello selezionato Azure verrà automaticamente scalare in base al numero di notifiche attraversano il sistema.

>[AZURE.NOTE] Il costo di utilizzo generale possibile salire in base al numero di notifiche push trasferiti. Verificare che siano a conoscenza dei limiti di livello esistente illustrati nella pagina [Prezzi hub di notifica] .

I clienti esistenti utilizza hub di notifica per inviare milioni di notifiche push ogni giorno. Non è necessario eseguire alcuna operazione speciale per ridimensionare il push notifiche raggiungono, purché si usano gli hub di notifica di Azure.

###<a name="7---how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>7. il periodo di tempo si impiega per inviare notifiche per raggiungere il dispositivo push?
Azure notifica hub in grado di elaborare almeno **notifica push 1 milione invia un minuto** in una normale utilizzare scenario in cui il caricamento della posta in arrivo è abbastanza uniforme e non spikey natura. Questo tasso può variare a seconda del numero di tag, natura degli invii posta in arrivo e altri fattori esterni.

Durante il periodo di consegna, il servizio in grado calcolare destinazioni per piattaforma e la distribuzione dei messaggi per i servizi di consegna di notifica push rispettivi basati su espressioni tag/tag registrati. D'ora in poi è responsabilità dei servizi delle notifiche Push (PNS) per inviare la notifica al dispositivo.

Un PNS non garantisce qualsiasi contratto di servizio per il recapito delle notifiche; Tuttavia, in genere la maggior parte delle notifiche push vengono inviati ai dispositivi di destinazione nell'arco di qualche minuto (in genere entro i limiti di 10 minuti) dal momento in cui vengono inviate alla piattaforma. È possibile che alcuni valori erratici che possono richiedere più tempo.

>[AZURE.NOTE] Azure notifica hub dispone di un criterio per eliminare le notifiche push che non sono possibile consegnare per la PNS in 30 minuti. Il ritardo può verificarsi per diversi motivi, più comunemente perché il PNS è la limitazione per l'applicazione.

###<a name="8---is-there-any-latency-guarantee"></a>8. esiste qualsiasi garanzia latenza?
A causa della natura delle notifiche push (che vengono inviati da un servizio di notifica Push esterni, specifico della piattaforma), non ci sono garanzie latenza. In genere, la maggior parte delle notifiche push venga recapitato entro un paio di minuti.

###<a name="9---what-are-the-considerations-i-need-to-take-into-account-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>9. quali sono le considerazioni che è necessario prendere in considerazione per la progettazione di una soluzione con spazi dei nomi e gli hub di notifica?

####<a name="mobile-appenvironment"></a>App/ambiente per dispositivi mobili

* Dovrebbe essere presente un Hub di notifica per app per dispositivi mobili per ambiente.
* In uno scenario multi-tenant ogni tenant deve avere un hub separato.
* È non necessario condividere mai stesso Hub di notifica tra ambienti di prova e di produzione come questo può causare problemi verso il basso la riga durante l'invio di notifiche. ad esempio Apple offre endpoint Sandbox e Push di produzione con ognuno con credenziali separate.
* Per impostazione predefinita, è possibile inviare le notifiche di test per i dispositivi registrati tramite il portale di Azure o il componente Azure integrato in Visual Studio. Il limite è impostato su 10 dispositivi selezionati in modo casuale dal pool di registrazione.

>[AZURE.NOTE] Se hub è stato configurato originariamente con un certificato sandbox Apple e quindi riconfigurare per usare un certificato di produzione di Apple, il vecchio token di dispositivo da più valide con il nuovo certificato e causare inserisce l'esito negativo. È consigliabile separare di produzione e ambienti di prova e utilizzare diversi hub per ambienti diversi.

####<a name="pns-credentials"></a>Credenziali PNS

Se è stata registrata un'app per dispositivi mobili con del portale per sviluppatori della piattaforma (ad esempio Apple o Google e così via) si otterrà un'app token di sicurezza e identificatore back-end un'app deve fornire ai servizi di notifica Push della piattaforma per poter inviare le notifiche push per i dispositivi. Questi token di sicurezza che possono essere sotto forma di certificati (ad esempio, per sistema operativo Apple iOS o Windows Phone) o i codici di sicurezza (Google Android, Windows e così via) devono essere configurati in hub di notifica. Questa operazione è stata completata in genere a livello di hub notifica ma può essere eseguita anche a livello dello spazio dei nomi in uno scenario multi-tenant.

####<a name="namespaces"></a>Spazi dei nomi

Per il raggruppamento di distribuzione, è possibile utilizzare gli spazi dei nomi.  Può inoltre essere utilizzato per rappresentare tutti gli hub di notifica per tutti i tenant della stessa applicazione nello scenario multi-tenant.

####<a name="geo-distribution"></a>Distribuzione geografico

Distribuzione geografico non sempre critica in scenari di notifica push. È va notato che vari Push Notification Services (ad esempio APN, GCM e così via), che per i dispositivi, offrendo le notifiche push, non siano uniformemente distribuita.

Se si dispone di un'applicazione che viene utilizzata in tutto il mondo, è possibile creare diversi hub in diversi spazi dei nomi a trarre vantaggio dalla disponibilità di un servizio di notifica hub in diverse aree geografiche Azure in tutto il mondo.

>[AZURE.NOTE] Questo aumenta il costo di gestione - soprattutto per le registrazioni, in modo che questo non è consigliabile e deve essere eseguito solo se è necessario un espliciti.

###<a name="10--should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>10. fare registrazioni dalla back-end app o direttamente tramite client dispositivi?
Le registrazioni da back-end app sono utili quando è necessario eseguire l'autenticazione client prima di creare la registrazione o quando si dispone di contrassegni che devono essere creati o modificati dall'app back-end in base a una logica di app. Per informazioni dettagliate, è possibile leggere informazioni nelle pagine di [indicazioni per la registrazione di back-end] e [back-end registrazione indicazioni - 2] .

###<a name="11--what-is-the-push-notification-delivery-security-model"></a>11. che cos'è il modello di sicurezza recapito di notifica push?
Azure hub di notifica di utilizzare una [Firma di Access condiviso (SA)](../storage/storage-dotnet-shared-access-signature-part-1.md)-basato sul modello di sicurezza. È possibile utilizzare i token associazioni di protezione a livello radice dello spazio dei nomi o al livello di dettaglio hub di notifica. Questi token SA può essere impostato con le regole di autorizzazione diversi, ad esempio Invia messaggio autorizzazioni, ascoltare le autorizzazioni di notifica e così via. Per ulteriori informazioni sono disponibili nel [modello di sicurezza NH] documento.

###<a name="12--how-should-i-handle-sensitive-payload-in-the-push-notifications"></a>12. come è consigliabile gestiscono payload riservati nelle notifiche push?
Tutte le notifiche vengono inviate ai dispositivi di destinazione, della piattaforma Push Notification Services (PNS). Quando un mittente invia una notifica a un hub di notifica di Azure è elaborare e passare la notifica PNS rispettivi.

Tutte le connessioni da parte del mittente per gli hub di notifiche Azure e quindi la PNS utilizzano HTTPS.

>[AZURE.NOTE] Azure hub le notifiche non registra payload del messaggio in alcun modo.

Per l'invio payload sensibili tuttavia è consigliabile un motivo Push sicura in cui il mittente offre una notifica 'ping' con un identificatore di messaggio con il dispositivo senza payload sensibili e quando l'app nel dispositivo riceve questo payload, è possibile chiamare una API sicura direttamente per recuperare i dettagli del messaggio. Una Guida su come implementare il modello descritto in precedenza è disponibile nella pagina [NH - sicura Push esercitazione] .

##<a name="operations"></a>Operazioni
###<a name="1---what-is-the-disaster-recovery-dr-story"></a>1. che cos'è la sequenza di ripristino di emergenza (DR)?
Sono disponibili copertura di emergenza metadati nostro estremità (nome Hub di notifica, stringa di connessione e altre informazioni critiche). Quando viene attivato uno scenario di DR, i dati le registrazioni sono **solo segmento** dell'infrastruttura di notifica hub che verrà persa. È necessario implementare una soluzione per popolare nuovamente i dati nel post-ripristino di hub nuovo.

- *Passaggio 1* - creare un Hub di notifica secondario in un centro di dati diversi. È possibile creare questo in tempo reale al momento dell'evento DR oppure è possibile crearne uno da ottenere passare. Non rende gran parte di una differenza opzione scelta perché il provisioning di notifica Hub è un processo relativamente veloce nell'ordine di alcuni secondi. Un dall'inizio che protegge dall'evento DR impatto le funzionalità di gestione, pertanto è l'opzione fortemente consigliato.

- *Passaggio2* - idrato Hub notifica secondario con le registrazioni dall'Hub notifica principale. Non è consigliabile provare a gestire le registrazioni in entrambi gli hub e provare a sincronizzare loro in tempo reale come le registrazioni sono disponibile a breve, in genere che anche a causa di tendenza inerente delle registrazioni scadenza ha dato esito positivo sul lato PNS. Notifica hub pulire loro man mano che si ricevono commenti e suggerimenti PNS sulle registrazioni scadute o non valide.  

Suggerimento consiste nell'usare un back-end app cui:

- Mantiene un determinato insieme di registrazioni al termine, in modo che è possibile eseguire un inserimento di massa hub notifica secondario in caso di emergenza

**OR**

- Ottiene una normale immagine delle registrazioni dall'hub primario come backup e quindi in un blocco inserirne NH secondario.

>[AZURE.NOTE] La funzionalità di esportazione/importazione registrazioni disponibile nel livello Standard è descritto nel documento di [Esportazione/importazione le registrazioni] .

Se non è disponibile un back-end, quindi all'avvio dell'app in uno qualsiasi dei dispositivi di destinazione, verrà eseguita una nuova registrazione nell'Hub notifica secondario e infine secondario Hub di notifica avranno tutti i dispositivi attivi registrati.

Lo svantaggio è che vi sono un periodo di tempo in cui è sono aperto di App su dispositivi quando non si riceveranno notifiche.

###<a name="2---is-there-any-audit-log-capability"></a>2. è disponibile alcuna capacità di log di controllo?
Tutte le operazioni di gestione delle notifiche hub passare ai registri operazione esposte nel [Portale classica Azure].

##<a name="monitoring--troubleshooting"></a>Monitoraggio e risoluzione dei problemi
###<a name="1---what-troubleshooting-capabilities-are-available"></a>1. quali funzionalità di risoluzione dei problemi sono disponibili?
Hub notifica Azure offrono diverse caratteristiche per eseguire risoluzione dei problemi comuni, in particolare nello scenario più comune delle notifiche ignorati. Visualizzare i dettagli in nostro [NH - risoluzione dei problemi] white paper.

###<a name="2---what-telemetry-features-are-available"></a>2. quali caratteristiche di telemetria sono disponibili?
Visualizzazione dei dati di telemetria nel [Portale classica Azure]Azure consente di hub di notifica. Dettagli delle statistiche disponibili sono disponibili nella pagina [NH - metriche] .

>[AZURE.NOTE] Notifiche riuscite indicano solo le notifiche push sono state recapitate al servizio di notifica Push esterni (ad esempio APN per Apple, GCM per Google e così via). In questo caso, PNS per recapitare la notifica ai dispositivi di destinazione. In genere, la PNS non visibili metriche di recapito a terze parti.  

Sono disponibili anche la possibilità di esportare i dati di telemetria a livello di programmazione (nel livello **Standard** ). Vedere [NH - esempio metriche] per informazioni dettagliate.

[Portale classica Azure]: https://manage.windowsazure.com
[Hub di notifica prezzi]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notifica hub contratto di servizio]: http://azure.microsoft.com/support/legal/sla/
[CaseStudy - Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[CaseStudy - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[CaseStudy - Seattle volte]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[CaseStudy - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[CaseStudy - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH - API REST]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - esercitazioni introduttive]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Esercitazione App Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Indicazioni per la registrazione di back-end]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Indicazioni per la registrazione di back-end - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Modello di sicurezza NH]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[NH - Push sicura esercitazione]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[NH - risoluzione dei problemi]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[NH - metriche]: https://msdn.microsoft.com/library/dn458822.aspx
[NH - esempio metriche]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Le registrazioni esportazione/importazione]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[esempi completi]: https://github.com/Azure/azure-notificationhubs-samples
[Azure App per dispositivi mobili]: https://azure.microsoft.com/en-us/services/app-service/mobile/
[Servizio di App prezzi]: https://azure.microsoft.com/en-us/pricing/details/app-service/
