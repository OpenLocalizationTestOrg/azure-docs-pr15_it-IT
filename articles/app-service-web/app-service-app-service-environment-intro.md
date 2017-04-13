<properties 
    pageTitle="Introduzione all'ambiente di servizio di App" 
    description="Le informazioni sulla funzionalità di ambiente servizio App che fornisce le unità della scala sicura, aggiungono VNet, dedicato per l'esecuzione di tutte le app." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016"
    ms.author="stefsch"/>

# <a name="introduction-to-app-service-environment"></a>Introduzione all'ambiente di servizio di App

## <a name="overview"></a>Panoramica ##
Un ambiente App è un [premio] [ PremiumTier] service opzione piano di servizio App Azure che offre un ambiente completamente isolato e dedicato per l'esecuzione in modo sicuro le applicazioni di servizio App Azure scala elevato, inclusi [Web Apps][WebApps], [App Mobile][MobileApps]e le [API App][APIApps].  

Servizio di App ambienti sono ideali per carichi di lavoro di applicazione che richiedono:

- Scala molto elevata
- Isolamento e accesso alla rete protetto

I clienti possono creare più ambienti di servizio di App in una singola regione Azure, oltre che in più regioni Azure.  In questo modo lo strumento ideale per la scala orizzontale livelli dell'applicazione di minore di stato per carichi di lavoro elevati RPS App servizio ambienti.

Gli ambienti servizio App sono isolati applicazioni solo un singolo cliente e sempre distribuiti in una rete virtuale.  Clienti dispone di un controllo preciso sul sia il traffico di rete in ingresso e in uscita applicazione e applicazioni possono connessioni ad alta velocità protette tramite reti virtuali alle risorse aziendali in locale.

Tutti gli articoli e la modalità-per informazioni su del App servizio ambienti sono disponibili in [file Leggimi per gli ambienti di servizio dell'applicazione](../app-service/app-service-app-service-environments-readme.md).

Per una panoramica di come App servizio ambienti abilitare alta e proteggere accesso alla rete, vedere [Approfondita AzureCon] [ AzureConDeepDive] in ambienti servizio App!

Per un approfondito in utilizzando la scala orizzontale più App servizio ambienti vedere l'articolo su come configurare un [consumi sull'ambiente distribuito geografico app][GeodistributedAppFootprint].

Per vedere come è stato configurato l'architettura di sicurezza illustrata nella AzureCon analisi approfondita, vedere l'articolo sull'implementazione di un' [architettura di sicurezza in livelli](app-service-app-service-environment-layered-security.md) con App servizio ambienti.

App in esecuzione in ambienti servizio App può avere il loro accesso gestita da dispositivi padre, ad esempio i firewall dell'applicazione web (WAF).  L'articolo su come [configurare un WAF per gli ambienti servizio App](app-service-app-service-environment-web-application-firewall.md) è descritta in questo scenario. 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="dedicated-compute-resources"></a>Risorse di elaborazione dedicato ##
Tutte le risorse di calcolo in un ambiente App sono riservate esclusivamente per una singola sottoscrizione e un ambiente App possono essere configurato con risorse di elaborazione fino a cinquanta (50) in accesso esclusivo da una singola applicazione.

Un ambiente App è costituito da un pool di risorse di elaborazione front-end, nonché pool di risorse uno a tre lavoro calcolo. 

Il pool front-end contiene risorse di elaborazione responsabile della terminazione SSL come bilanciamento del carico anche automatica delle richieste di app all'interno di un ambiente App. 

Ogni pool di lavoro contiene risorse di elaborazione a [Piani di servizio App][AppServicePlan], che a sua volta contenere uno o più applicazioni di servizio App Azure.  Poiché in un ambiente App possono essere fino a tre pool di lavoro diverso, si dispone della flessibilità necessaria per scegliere risorse di elaborazione diverso per ogni pool di lavoro.  

Ad esempio, consente di creare un pool di lavoro con meno potenti risorse di elaborazione per piani di servizio App destinato App sviluppo o test.  Un pool di lavoro secondo (o persino terzo) è possibile utilizzare più potenti risorse di elaborazione destinate piani di servizio App eseguire app di produzione.

Per informazioni dettagliate sulla quantità di risorse di elaborazione disponibili per i pool front-end e di lavoro, vedere [come configurare un ambiente App][HowToConfigureanAppServiceEnvironment].  

Per informazioni dettagliate sulla disponibili calcolare le dimensioni delle risorse è supportate in un ambiente App, consultare i [Prezzi assistenza App] [ AppServicePricing] pagina ed esaminare le opzioni disponibili per gli ambienti servizio App in Premium prezzi livello.

## <a name="virtual-network-support"></a>Supporto di rete virtuale ##
Un ambiente App possono essere creato in **dei due** virtuali un Manager delle risorse di Azure, **o** una distribuzione classica modello virtuali ([altre informazioni sulle reti virtuali][MoreInfoOnVirtualNetworks]).  Poiché un ambiente App è sempre presente in una rete virtuale e con maggiore precisione all'interno di una subnet di una rete virtuale, è possibile sfruttare le funzionalità di protezione di reti virtuali per controllare entrambe le comunicazioni di rete in ingresso e in uscita.  

Un ambiente App possono essere Internet affiancate con un indirizzo IP pubblico o interno affiancate con un indirizzo di Azure interno carico bilanciamento (ILB).

È possibile utilizzare [i gruppi di sicurezza rete] [ NetworkSecurityGroups] per limitare le comunicazioni di rete in ingresso alla subnet in cui si trova un ambiente App.  In questo modo è possibile eseguire App dietro dispositivi padre e servizi, ad esempio i firewall dell'applicazione web e provider SaaS di rete.

App spesso anche necessario accedere alle risorse aziendali, ad esempio database interni e servizi web.  Un approccio comune consiste nel rendere disponibile solo per il traffico di rete interno che scorre all'interno di una rete virtuale Azure questi endpoint.  Una volta un ambiente App appartiene alla stessa rete virtuale come i servizi interni, App in esecuzione nell'ambiente di accedervi, inclusi i punti finali raggiungibili mediante [Da sito] [ SiteToSite] e [Azure ExpressRoute] [ ExpressRoute] connessioni.

Per ulteriori informazioni sul funzionamento delle App servizio ambienti con reti virtuali e le reti locale, consultare i seguenti articoli nel [Architettura di rete][NetworkArchitectureOverview], [Controllare il traffico in ingresso][ControllingInboundTraffic]e [In modo sicuro la connessione a back-end][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Guida introduttiva

Per iniziare a utilizzare App servizio ambienti, vedere [Modalità per creare un'App ambiente del servizio][HowToCreateAnAppServiceEnvironment]

Tutti gli articoli e la modalità-per per gli ambienti servizio App sono disponibili nel [file Leggimi per gli ambienti di servizio dell'applicazione](../app-service/app-service-app-service-environments-readme.md).

Per ulteriori informazioni sulla piattaforma Azure App servizio, vedere [Servizio App Azure][AzureAppService].

Per una panoramica dell'architettura di rete ambiente servizio App, vedere la [Panoramica di architetture di rete] [ NetworkArchitectureOverview] articolo.

Per informazioni dettagliate sull'utilizzo di un ambiente App con ExpressRoute, vedere l'articolo seguente su [strada e App servizio ambienti][NetworkConfigDetailsForExpressRoute].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->

 
