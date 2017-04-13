<properties
    pageTitle="Utilizzare i servizi Mobile, in che modo facilita servizio App?"
    description="Informazioni su quali vantaggi introduce App servizio per i progetti di servizi mobili esistenti."
    services="app-service\mobile"
    documentationCenter="ios"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="getting-started"> </a>Utilizzare servizi Mobile, in che modo facilita servizio App?

##<a name="overview"></a>Panoramica
Il servizio Mobile esistente sicuro e rimarrà supportato. Esistono tuttavia diversi vantaggi del *Servizio di App Azure* piattaforma fornisce per l'app per dispositivi mobili che non sono attualmente disponibili con i servizi Mobile:

- Offerta più semplice, più semplice e conveniente per le applicazioni che includono i client mobili e web
- Nuove caratteristiche di host inclusi i processi Web, record CNAME personalizzato migliore monitoraggio
- Integrazione completa con il traffico Manager
- Connettività a risorse locale e VPN utilizzando VNet oltre alle connessioni ibrido
- Monitoraggio, avvisi e risoluzione dei problemi per l'app tramite NewRelic o AppInsights
- Spettro più completo dell'oggetto sottostante calcolare le risorse e prezzi
- Ridimensionamento automatico predefinita, il bilanciamento del carico e il monitoraggio delle prestazioni.
- Predefiniti di gestione temporanea, le funzionalità di backup, ripristino e il test di produzione

## <a name="new-hosting-features"></a>Nuove caratteristiche di hosting
Nel *Servizio App Azure* dell' *App Mobile* back-end codice viene eseguito nello stesso contenitore di Web App e API App. Come tale è possibile sfruttare tutte le caratteristiche in questo contenitore, tra cui alcuni di quelli che non sono attualmente presenti nei servizi mobili:

- Aggiungere logica di back-end continuo tramite Web processi
- Assicurarsi che il codice di back-end sempre è in esecuzione
- Utilizzare record CNAME personalizzati per fornire nomi descrittivi e stabili per gli endpoint back-end per dispositivi mobili
- Geografico scala dell'applicazione con il traffico Manager
- Includere le raccolte e pacchetti desiderato.
- (Per .NET) Sfruttare le funzionalità di ASP.NET, tra cui MVC
- (Per Node) Sfruttare le eventuali pura libreria JavaScript dell'ecosistema nodo, incluse le librerie MVC comuni.

##<a name="access-on-premises-data-using-vnet"></a>Accesso locale dati tramite VNet
Con i servizi Mobile oggi già utilizzare connessioni ibrido per accedere alle risorse locale. Esistono tuttavia situazioni in cui è preferibile una soluzione VPN. Con *Il servizio di Azure App* è possibile utilizzare Azure VNet per il codice di back-end App Mobile.

##<a name="use-your-favorite-backend-language"></a>Usare la lingua preferita back-end
*Servizio di App Azure* offre più completo e più ampio supporto per le piattaforme ASP.NET e Node, incluso l'accesso ai runtime più recente.

##<a name="set-up-automatic-scale"></a>Configurare la scala automatica
Con i servizi di dispositivi mobili, tutte le istanze del codice back-end erano in esecuzione in macchine virtuali di piccole dimensioni. *Servizio di App Azure* consente di selezionare le dimensioni delle macchine virtuali da un insieme di opzioni più ampio. È possibile ridimensionare rapidamente verso l'alto o indietro per gestire qualsiasi carico cliente in arrivo, in base a vari criteri di misurazione delle prestazioni.

##<a name="be-in-the-know"></a>Nel "che"
Rispondere a problemi in tempo reale con gli avvisi per automaticamente una notifica che il team e il monitoraggio. Integrare analitica app avanzate e le funzionalità monitoraggio nuovo Relic e AppInsights per arricchire approfondita sulle prestazioni l'app per dispositivi mobili. Con *Il servizio di Azure App* è possibile impostare ora avvisi in base a serie misurazione delle prestazioni, a livello di programmazione e tramite il portale di Azure.

##<a name="keep-your-assets-safe"></a>Proteggere le risorse
Backup automatico di database back-end. Il codice e i dati è protetti da emergenza e facilmente ripristinata, consentendo di gestire le attività aziendali con fiducia.

##<a name="ready-stage-go"></a>Pronti, sul passaggio, passare!
Con *Il servizio di App Azure* è ora possibile creare più verifiche privato e ambienti per l'App per dispositivi mobili di gestione temporanea. Utilizzarli per eseguire test prima di distribuire. Scambio di produzione con senza tempi di inattività. App Web vengono precaricato, garantire la migliore esperienza dei clienti.

È possibile iniziare a trarre vantaggio da *App servizio* per il servizio Mobile esistente, seguendo l' [esercitazione](app-service-mobile-migrating-from-mobile-services.md).

