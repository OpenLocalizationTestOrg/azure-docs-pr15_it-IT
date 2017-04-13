<properties 
    pageTitle="Ambiente servizio App | Microsoft Azure" 
    description="Che cos'è un ambiente Azure App? Introduzione all'ambiente di servizi di App." 
    keywords="ambiente servizio applicazione Azure, virtuali, protezione della rete"
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

# <a name="app-service-environment-documentation"></a>Documentazione di ambiente del servizio di App

Un ambiente App è un [premio] [ PremiumTier] service opzione piano di servizio App Azure che offre un ambiente completamente isolato e dedicato per l'esecuzione in modo sicuro le applicazioni di servizio App Azure scala elevato, inclusi [Web Apps][WebApps], [App Mobile][MobileApps]e le [API App][APIApps].  

Servizio di App ambienti sono ideali per carichi di lavoro di applicazione che richiedono:

- Scala molto elevata
- Isolamento e accesso alla rete protetto

I clienti possono creare più ambienti di servizio di App in una singola regione Azure, oltre che in più regioni Azure.  In questo modo lo strumento ideale per la scala orizzontale livelli dell'applicazione di minore di stato per carichi di lavoro elevati RPS App servizio ambienti.

Gli ambienti servizio App sono isolati applicazioni solo un singolo cliente e sempre distribuiti in una rete virtuale.  Hanno un controllo preciso sul sia il traffico di rete in ingresso e in uscita applicazione con i [gruppi di sicurezza di rete][NetworkSecurityGroups].  Le applicazioni possono anche connessioni ad alta velocità protette tramite reti virtuali alle risorse aziendali in locale.

App spesso necessario accedere alle risorse aziendali, ad esempio database interni e servizi web.  App in esecuzione in ambienti servizio App possono accedere alle risorse raggiungibile mediante [Da sito] [ SiteToSite] VPN e [Azure ExpressRoute] [ ExpressRoute] connessioni.

* [Che cos'è un ambiente App?](../app-service-web/app-service-app-service-environment-intro.md)
* [Creazione di un ambiente servizio App](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Creazione di applicazioni in un ambiente App](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Creazione e utilizzo di un servizio di bilanciamento del carico interno con ambienti servizio App](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [Configurare un ambiente servizio App](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [Ridimensionamento delle App in un ambiente App](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [Protezione di rete e architettura](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## <a name="how-tos"></a>Come del

[AZURE.INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]


## <a name="videos"></a>Video
[AZURE.VIDEO azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps]

[AZURE.VIDEO microsoft-ignite-2015-running-enterprise-web-and-mobile-apps-on-azure-app-service]


<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
