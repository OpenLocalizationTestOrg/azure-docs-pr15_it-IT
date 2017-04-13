<properties 
   pageTitle="ExpressRoute circuiti e domini di routing | Microsoft Azure"
   description="Questa pagina offre una panoramica di ExpressRoute circuiti e i domini di routing."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="expressroute-circuits-and-routing-domains"></a>ExpressRoute circuiti e domini di routing

 È necessario richiedere un *circuito ExpressRoute* per la connessione dell'infrastruttura locale a Microsoft tramite un provider di integrazione applicativa. Nella figura seguente offre una rappresentazione logica di connessione tra il WAN e Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

## <a name="expressroute-circuits"></a>Circuiti ExpressRoute

Un *circuito ExpressRoute* rappresenta una connessione logica tra l'infrastruttura di locale e servizi cloud Microsoft tramite un provider di integrazione applicativa. È possibile ordinare più circuiti ExpressRoute. Ogni circuito può essere nelle stesse o in altre aree e può essere collegato a loro locale mediante il provider di connessione diverso. 

Circuiti ExpressRoute non fanno riferimento a qualsiasi entità fisiche. Un circuito viene identificato una standard che GUID chiamato come una chiave di servizio (tasto s). La chiave del servizio è l'unica informazione scambiata di Microsoft, il provider di integrazione applicativa e si. Il tasto s non è un segreto per motivi di sicurezza. Esiste un mapping 1:1 tra un circuito ExpressRoute e il tasto s.

Un circuito ExpressRoute può includere fino a tre peerings indipendente: Azure privato pubblico, Azure e Microsoft. Ogni peering è una coppia di BGP indipendente sessioni di esse configurati ridondante per la disponibilità elevata. C'è 1: n (1 < = N < = 3) il mapping tra un circuito ExpressRoute e il routing di domini. Un circuito ExpressRoute può contenere uno, due o tutti i tre peerings abilitato per circuito ExpressRoute.
 
Ogni circuito ha una larghezza di banda fisso (50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 GB/s, 10 GB/s) e sia associato a un provider di integrazione applicativa e il percorso peering. La larghezza di banda si seleziona essere condivisi tra tutte le peerings per il circuito. 

### <a name="quotas-limits-and-limitations"></a>Le quote di limiti e limitazioni

Limiti e le quote predefinite applicano per ogni circuito ExpressRoute. Fare riferimento alla pagina [abbonamento Azure e limiti del servizio, le quote e vincoli](../azure-subscription-service-limits.md) per informazioni aggiornate sulle quote.

## <a name="expressroute-routing-domains"></a>Domini di routing ExpressRoute

Un circuito ExpressRoute ha più domini routing associati: Azure privato pubblico, Azure e Microsoft. Ognuno dei domini routing è configurato in modo identico su una coppia di router (attivo attivo o la condivisione di carico configurazione) per la disponibilità elevata. Servizi di Azure sono classificati come *Azure pubblico* e *Azure personali* per rappresentare IP schemi di indirizzamento.


![](./media/expressroute-circuit-peerings/expressroute-peerings.png)


### <a name="private-peering"></a>Peering privato

Azure calcolare servizi, vale a dire macchine virtuali (IaaS) e servizi cloud PaaS (), che vengono distribuiti all'interno di una rete virtuale possono essere connessi mediante il dominio peering privato. Dominio peering privato viene considerato attendibile estensione della rete di base in Microsoft Azure. È possibile configurare la connessione bidirezionale tra la rete principale e reti virtuali Azure (VNets). Questo peering consente di connettersi a macchine virtuali e servizi direttamente nel loro indirizzi IP privati cloud.  

È possibile connettersi più di una rete virtuale al dominio peering privato. Rivedere la [pagina Domande frequenti](expressroute-faqs.md) per informazioni sui limiti e limitazioni. È possibile visitare la pagina [abbonamento Azure e limiti del servizio, le quote e vincoli](../azure-subscription-service-limits.md) per informazioni aggiornate sui limiti.  Fare riferimento alla pagina del [Routing](expressroute-routing.md) per informazioni dettagliate sulla configurazione del routing.

### <a name="public-peering"></a>Peering pubblico

Servizi, ad esempio lo spazio di archiviazione di Azure, database SQL e siti Web sono disponibili in indirizzi IP. In privato, è possibile connettersi ai servizi ospitati in indirizzi IP pubblici, tra cui VIP dei servizi cloud, tramite il dominio di routing peering pubblico. È possibile connettere il dominio peering pubblico per la rete Perimetrale e connettersi a tutti i servizi di Azure indirizzi IP pubblici da sulla WAN senza la connessione tramite internet. 

Connettività inizia sempre da sulla WAN ai servizi Microsoft Azure. Servizi di Microsoft Azure non sarà possibile avviare le connessioni alla rete aziendale tramite questo dominio di routing. Una volta peering pubblico è attivata, sarà possibile connettersi a tutti i servizi di Azure. Abbiamo non consente di selezionare in modo selettivo per il quale si annunciare route per i servizi. È possibile esaminare l'elenco dei prefissi che è annunciare all'utente tramite questo peering nella pagina [Il intervalli di indirizzi IP di Microsoft Azure Data Center](http://www.microsoft.com/download/details.aspx?id=41653) . La pagina viene aggiornata ogni settimana.

È possibile definire filtri route personalizzato all'interno della rete di utilizzare solo le route che è necessario. Fare riferimento alla pagina del [Routing](expressroute-routing.md) per informazioni dettagliate sulla configurazione del routing. È possibile definire filtri route personalizzato all'interno della rete di utilizzare solo le route che è necessario. 

Vedere la [pagina Domande frequenti](expressroute-faqs.md) per ulteriori informazioni sui servizi tramite il dominio di routing peering pubblico è supportati. 
 
### <a name="microsoft-peering"></a>Microsoft peering

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

La connettività a tutti gli altri Microsoft online services (ad esempio servizi di Office 365) sarà tramite il Microsoft peering. Attivare la connettività bidirezionale tra servizi cloud WAN e Microsoft tramite il dominio di routing peering di Microsoft. È necessario connettersi ai servizi cloud Microsoft solo su indirizzi IP pubblici che appartengono dall'utente o il provider di integrazione applicativa ed è necessario rispettare tutte le regole definite. Vedere la pagina [Prerequisiti ExpressRoute](expressroute-prerequisites.md) per ulteriori informazioni.

Vedere la [pagina Domande frequenti](expressroute-faqs.md) per altre informazioni sui servizi supportati, i costi e dei dettagli di configurazione. Vedere la pagina [Percorsi ExpressRoute](expressroute-locations.md) per informazioni sull'elenco di provider di integrazione applicativa offre servizi di supporto peering Microsoft.

## <a name="routing-domain-comparison"></a>Confronto tra dominio di routing

Nella tabella seguente contiene un confronto di tre domini routing.

||**Peering privato**|**Peering pubblico**|**Microsoft Peering**|
|---|---|---|---|
|**Max. prefissi # supportati per peering**|Per impostazione predefinita, 10.000 con ExpressRoute Premium 4000|200|200|
|**Intervalli di indirizzi IP supportati**|Tutti gli indirizzi IPv4 valido all'interno del WAN.|Indirizzi IPv4 di proprietà dall'utente o il provider di integrazione applicativa.|Indirizzi IPv4 di proprietà dall'utente o il provider di integrazione applicativa.|
|**COME numerici requisiti**|Privata e pubblica come numeri. È necessario essere i proprietari del pubblico come numero se si sceglie di utilizzare uno. | Privata e pubblica come numeri. Tuttavia, è necessario provare la proprietà di indirizzi IP pubblici.| Privata e pubblica come numeri. Tuttavia, è necessario provare la proprietà di indirizzi IP pubblici.|
|**Indirizzi IP interfaccia routing**|Indirizzi IP pubblico e RFC1918|Indirizzi IP registrati per l'utente registri routing.| Indirizzi IP registrati per l'utente registri routing.|
|**Supporto Hash MD5**| Sì|Sì|Sì|

È possibile scegliere di attivare uno o più domini routing come parte del loro circuito ExpressRoute. È possibile scegliere di tutti i domini routing mettere in VPN stessa se si desidera combinarle in un unico dominio di routing. È anche possibile inserire loro in domini diversi routing, simili al diagramma. Configurazione consigliata è peering privato è connesso direttamente alla rete principale che il pubblico e collegamenti di peering Microsoft sono connessi per la rete Perimetrale.
 
Se si sceglie di tutte le sessioni di peering tre, è necessario disporre tre coppie di sessioni BGP (una coppia per ogni tipo di peering). Le coppie di sessione BGP forniscono un collegamento ad alta disponibilità. Se si esegue la connessione tramite i provider di livello 2 connettività, prendersi la responsabilità di configurare e gestire il routing. Sono disponibili ulteriori consultando i [flussi di lavoro](expressroute-workflows.md) per la configurazione ExpressRoute.

## <a name="next-steps"></a>Passaggi successivi

- Trovare un provider di servizi. Vedere [posizioni e i provider di servizi ExpressRoute](expressroute-locations.md).
- Verificare che siano soddisfatti tutti i prerequisiti. Vedere la sezione [Prerequisiti ExpressRoute](expressroute-prerequisites.md).
- Configurare la connessione ExpressRoute.
    - [Creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
    - [Configurare routing (circuito peerings)](expressroute-howto-routing-classic.md)
    - [Creare un collegamento a un circuito ExpressRoute un VNet](expressroute-howto-linkvnet-classic.md)
