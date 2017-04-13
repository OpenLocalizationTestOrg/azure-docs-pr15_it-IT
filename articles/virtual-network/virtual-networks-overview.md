<properties
   pageTitle="Panoramica di Azure virtuali (VNet)"
   description="Informazioni sulle reti virtuali (VNets) in Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="virtual-network-overview"></a>Panoramica di rete virtuale

Una rete virtuale Azure (VNet) è una rappresentazione della rete nel cloud.  Si tratta di un isolamento logico del cloud Azure dedicato all'abbonamento. È possibile controllare completamente blocchi di indirizzi IP, le impostazioni DNS, criteri di sicurezza e la distribuzione tabelle all'interno di questa rete. È possibile inoltre suddividere il VNet in subnet e avvio Azure IaaS macchine () e/o [servizi Cloud (istanze del ruolo PaaS)](../cloud-services/cloud-services-choose-me.md). Inoltre, è possibile connettersi alla rete virtuale per la rete locale mediante una delle [Opzioni di connettività](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site) disponibili in Azure. In pratica, è possibile espandere la rete in Azure, con il controllo completo sul blocchi di indirizzi IP con il vantaggio di scala aziendale che Azure offre.

Per comprendere meglio VNets, consultare la figura seguente, che mostra una rete locale semplificata.

![Rete locale](./media/virtual-networks-overview/figure01.png)

La figura riportata sopra mostra una rete locale sono connessa a Internet pubblico tramite un router. È inoltre possibile visualizzare un firewall o al router e una rete Perimetrale hosting di un server DNS e un server web farm. La server farm web è il bilanciamento del carico usando un bilanciamento del carico hardware che esposto su Internet e utilizza le risorse da subnet interna. Subnet interna è separata dalla rete Perimetrale un altro firewall e i server Controller di dominio Active Directory host, server di database e server delle applicazioni.

Della stessa rete può essere ospitata in Azure, come illustrato nella figura seguente.

![Azure virtuali](./media/virtual-networks-overview/figure02.png)

Si noti come l'infrastruttura di Azure assume il ruolo di router, che consente l'accesso dal VNet Internet pubblico senza la necessità di qualsiasi configurazione. Firewall possono essere sostituiti dai gruppi di sicurezza rete (NSGs) applicato a ogni subnet singoli. E servizi di bilanciamento del carico fisica vengono sostituiti da internet interne ed esposto bilanciamento in Azure.

>[AZURE.NOTE] Esistono due modalità di distribuzione di Azure: classica (noto anche come servizio di gestione) e Manager delle risorse Azure (ARM). VNets classica Impossibile aggiunto a un gruppo di affinità o creata come un VNet internazionali. Se si dispone di un VNet in un gruppo di affinità, si consiglia di [eseguire la migrazione di essa per un VNet internazionali](virtual-networks-migrate-to-regional-vnet.md).

## <a name="virtual-network-benefits"></a>Vantaggi di rete virtuale

- **Isolamento**. VNets sono completamente isolate rispetto a altro. Che consente di creare selezioni non contigue reti per lo sviluppo, test e produzione che utilizzano gli stessi blocchi indirizzo CIDR.

- **Accesso a Internet**. Tutte le macchine virtuali IaaS e PaaS istanze del ruolo in un VNet possono accedere a Internet pubblica per impostazione predefinita. È possibile controllare l'accesso con i gruppi di sicurezza di rete (NSGs).

- **Accesso a macchine virtuali all'interno di VNet**. Istanze del ruolo PaaS e macchine virtuali IaaS può essere avviate nella stessa rete virtuale e possono connettersi tra loro utilizzando gli indirizzi IP privati, anche se sono in subnet diverse senza che sia necessario configurare un gateway o utilizzare indirizzi IP pubblici.

- **Risoluzione dei nomi**. Azure offre la risoluzione dei nomi interno per macchine virtuali IaaS e istanze del ruolo PaaS distribuite nel VNet. È anche possibile distribuire i server DNS e configurare VNet per usarli.

- **Sicurezza**. Il traffico immissione e chiudere le macchine virtuali e istanze del ruolo PaaS in un VNet può essere controllato utilizzando i gruppi di sicurezza di rete.

- **La connettività**. VNets è possibile connettersi tra loro utilizzando gateway rete o VNet peering. VNets può essere collegato al data center locale tramite reti VPN da sito o Azure ExpressRoute. Per ulteriori informazioni sulla connessione VPN da sito, visitare il [gateway su VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site). Per ulteriori informazioni su ExpressRoute, vedere [Panoramica tecnica ExpressRoute](../expressroute/expressroute-introduction.md). Per ulteriori informazioni su come VNet peering, visitare [VNet peering](virtual-network-peering-overview.md).

    >[AZURE.NOTE] Verificare che si crea un VNet prima di distribuire le macchine virtuali IaaS o istanze del ruolo PaaS all'ambiente di Azure. PROCESSORE basato su macchine virtuali richiedono un VNet e se non si specifica un VNet esistente, Azure crea predefinito VNet che potrebbero avere un conflitto di blocco di indirizzi CIDR con la rete locale. Impossibilità di connettersi il VNet alla rete locale.

## <a name="subnets"></a>Subnet

Subnet è un intervallo di indirizzi IP nel VNet, è possibile dividere un VNet in più subnet per organizzazione e la sicurezza. Macchine virtuali e istanze del ruolo PaaS distribuite alla subnet (stesso o diversa) all'interno di un VNet possono comunicare tra loro senza alcuna configurazione aggiuntiva. È inoltre possibile configurare la distribuzione tabelle e NSGs a una subnet.

## <a name="ip-addresses"></a>Indirizzi IP


Esistono due tipi di indirizzi IP assegnati alle risorse in Azure: *pubblici* e *privati*. Indirizzi IP pubblici Consenti Azure risorse comunicare con Internet e altri servizi di pubblico Azure ad esempio [Azure Redis Cache](https://azure.microsoft.com/services/cache/), [Azure evento hub](https://azure.microsoft.com/documentation/services/event-hubs/). Indirizzi IP privati consente la comunicazione tra le risorse in una rete virtuale, insieme a quelli connessi tramite VPN, senza utilizzare indirizzi IP routing a Internet.

Per ulteriori informazioni sugli indirizzi IP in Azure, visitare [gli indirizzi IP in rete virtuale](virtual-network-ip-addresses-overview-arm.md)

## <a name="azure-load-balancers"></a>Servizi di bilanciamento del carico Azure

Macchine virtuali e servizi cloud in una rete virtuale possono essere esposto a Internet tramite servizi di bilanciamento del carico Azure. Applicazioni aziendali che sono interni che l'accesso solo possono essere il bilanciamento del carico con bilanciamento del carico interno.

- **Servizio di bilanciamento del carico esterno**. È possibile utilizzare un servizio di bilanciamento del carico esterno per fornire disponibilità per macchine virtuali IaaS e PaaS istanze del ruolo accessibili tramite Internet pubblica.

- **Bilanciamento del carico interno**. È possibile utilizzare un servizio di bilanciamento del carico interno per fornire disponibilità per macchine virtuali IaaS e istanze del ruolo PaaS accessibili da altri servizi del VNet.

Per ulteriori informazioni su bilanciamento del carico in Azure, visitare [Carica panoramica di bilanciamento del carico](../load-balancer/load-balancer-overview.md).

## <a name="network-security-group-nsg"></a>Gruppo di sicurezza di rete (NSG)

È possibile creare NSGs per controllare l'accesso alle interfacce di rete (NIC) in ingresso e in uscita macchine virtuali e subnet. Ogni NSG contiene uno o più regole che specificano o meno il traffico viene approvato o respinta in base a indirizzo IP di origine, porta di origine, indirizzo IP di destinazione e porta di destinazione. Per ulteriori informazioni su NSGs, vedere [che cos'è un gruppo di sicurezza di rete](virtual-networks-nsg.md).

## <a name="virtual-appliances"></a>Dispositivi di rete

Un dispositivo virtuale è solo un altro macchine Virtuali del VNet che esegue una funzione di accessorio software in base a, ad esempio firewall, ottimizzazione WAN o intrusione. È possibile creare una route di Azure per instradare il traffico VNet tramite un dispositivo virtuale per usare le funzionalità.

Ad esempio, NSGs utilizzabile per fornire sicurezza nell'ambito del VNet. Tuttavia, NSGs forniscono livello 4 controllo elenco di accesso per i pacchetti in ingresso e in uscita. Se si desidera utilizzare un modello di sicurezza di 7 livelli, è necessario usare un firewall semplice.

Dispositivi di rete dipendono [indirizza e inoltro IP definite dall'utente](virtual-networks-udr-overview.md).

## <a name="limits"></a>Limiti
Esistono limiti al numero di reti virtuali consentito in una sottoscrizione, fare riferimento ai [limiti di Azure rete](../azure-subscription-service-limits.md#networking-limits) per ulteriori informazioni.

## <a name="pricing"></a>Prezzi
Non costa nulla per l'utilizzo di reti virtuali in Azure. Le istanze di elaborazione avviate all'interno di Vnet verranno addebitate tariffe standard come descritto in [Macchine Virtuali di Azure prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/). Il [Gateway VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) e [indirizzi IP] (https://azure.microsoft.com/pricing/details/ip-addresses/) utilizzato nel VNet anche saranno addebitate tariffe standard.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un VNet](virtual-networks-create-vnet-arm-pportal.md) e subnet.
- [Creare una macchina virtuale in un VNet](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Informazioni sulle [NSGs](virtual-networks-nsg.md).
- Informazioni sulle [route e inoltro IP definite dall'utente](virtual-networks-udr-overview.md).
