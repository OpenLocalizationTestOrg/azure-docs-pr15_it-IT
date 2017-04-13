<properties
   pageTitle="Pubblici e privati gli indirizzi IP in Gestione risorse di Azure | Microsoft Azure"
   description="Informazioni sulle pubblici e privati gli indirizzi IP in Gestione risorse di Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="ip-addresses-in-azure"></a>Indirizzi IP in Azure
È possibile assegnare indirizzi IP alle risorse Azure per comunicare con altre risorse Azure, la rete locale e Internet. Esistono due tipi di indirizzi IP che è possibile usare in Azure:

- **Indirizzi IP pubblico**: utilizzato per la comunicazione con Internet, inclusi i servizi di pubblico Azure
- **Indirizzi IP privati**: utilizzato per la comunicazione all'interno di una rete virtuale Azure (VNet) e le in locale di rete quando si utilizza un gateway VPN o circuito ExpressRoute per estendere la rete in Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modello di distribuzione classica](virtual-network-ip-addresses-overview-classic.md).

Se si ha familiarità con il modello di distribuzione classico, controllare il [differenze tra classica indirizzi IP e Gestione risorse](virtual-network-ip-addresses-overview-classic.md#Differences-between-Resource-Manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Indirizzi IP pubblici
Indirizzi IP pubblici consentono Azure alle risorse di comunicare con i servizi Internet e Azure pubblico, ad esempio [Azure Redis Cache](https://azure.microsoft.com/services/cache/), [Hub di evento Azure](https://azure.microsoft.com/services/event-hubs/), [database SQL](../sql-database/sql-database-technical-overview.md)e [lo spazio di archiviazione Azure](../storage/storage-introduction.md).

Gestione risorse di Microsoft Azure, un indirizzo [IP pubblico](resource-groups-networking.md#public-ip-address) è una risorsa con le relative proprietà. È possibile associare una risorsa indirizzo IP pubblica a tutte le risorse seguenti:

- Macchine virtuali (macchine Virtuali)
- Servizi di bilanciamento del carico esposto a Internet
- Gateway VPN
- Gateway per applicazioni

### <a name="allocation-method"></a>Metodo di allocazione
Esistono due metodi in cui un indirizzo IP è assegnato una risorsa IP *pubblico* - *statica*o *dinamica* . Il metodo di allocazione predefinito è *dinamico*, in cui un indirizzo IP **non** è assegnata al momento della creazione. Se, tuttavia, l'indirizzo IP pubblico allocata quando si avvia (o creare) la risorsa associata (ad esempio un bilanciamento del carico o macchine Virtuali). L'indirizzo IP viene rilasciato quando si arresta (o eliminare) la risorsa. In questo modo l'indirizzo IP da modificare quando si interrompe e avviare una risorsa.

Per garantire che l'indirizzo IP per la risorsa associata rimane inalterato, è possibile impostare in modo esplicito il metodo di allocazione in *statico*. In questo caso un indirizzo IP viene assegnato immediatamente. Viene rilasciato solo quando si elimina la risorsa o modifica il metodo di allocazione in *dinamico*.

>[AZURE.NOTE] Anche quando si imposta il metodo di allocazione su *statica*, non è possibile specificare l'indirizzo IP effettivo assegnato alla *risorsa IP pubblico*. Se, tuttavia, viene ricevuto da un pool di indirizzi IP disponibili nel percorso Azure in che la risorsa viene creata.

Indirizzi IP pubblici statici vengono utilizzati negli scenari seguenti:

- Gli utenti finali è necessario aggiornare le regole del firewall per comunicare con le risorse Azure.
- Risoluzione dei nomi DNS, in cui una modifica dell'indirizzo IP richiedono l'aggiornamento di un record.
- Le risorse Azure comunicano con altre applicazioni o servizi che utilizzano un modello di sicurezza basati sull'indirizzo IP.
- Utilizzare i certificati SSL collegati a un indirizzo IP.

>[AZURE.NOTE] L'elenco di intervalli di indirizzi IP da cui indirizzi IP pubblici (dinamico/statico) vengono assegnati alle risorse Azure viene pubblicato in [intervalli di indirizzi IP di Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="dns-hostname-resolution"></a>Risoluzione di nome host DNS
È possibile specificare un'etichetta di nome di dominio DNS per una risorsa IP pubblica, che consente di creare un mapping per *domainnamelabel*. *percorso*. cloudapp.azure.com all'indirizzo IP pubblico nei server DNS gestiti Azure. Ad esempio, se si crea una risorsa IP pubblica con **contoso** come un *domainnamelabel* negli **Stati Uniti ovest** Azure *posizione*, il nome di dominio completo **contoso.westus.cloudapp.azure.com** risolverà all'indirizzo IP pubblico della risorsa. È possibile usare il nome di dominio completo per creare un record CNAME di dominio personalizzato che punta all'indirizzo IP pubblico in Azure.

>[AZURE.IMPORTANT] Ogni etichetta di nome di dominio creato deve essere univoco all'interno di posizione Azure.  

### <a name="virtual-machines"></a>Macchine virtuali
È possibile associare un indirizzo IP pubblico a [Windows](../virtual-machines/virtual-machines-windows-about.md) o [Linux](../virtual-machines/virtual-machines-linux-about.md) macchine Virtuali mediante l'assegnazione alla relativa **interfaccia di rete**. In caso di un'interfaccia di rete più macchine Virtuali, è possibile assegnarlo all'interfaccia di rete *principale* solo. È possibile assegnare un dinamico o un indirizzo IP pubblico per una macchina virtuale.

### <a name="internet-facing-load-balancers"></a>Servizi di bilanciamento del carico esposto a Internet
È possibile associare un indirizzo IP pubblico con un [Sistema di bilanciamento del carico Azure](../load-balancer/load-balancer-overview.md)mediante l'assegnazione alla configurazione **front-end** bilanciamento del carico. L'indirizzo IP pubblico funge da un bilanciamento del carico indirizzo IP virtuale (VIP). È possibile assegnare un dinamico o un indirizzo IP pubblico a un servizio di bilanciamento del carico front-end. È anche possibile assegnare più indirizzi IP per bilanciamento front-end, che consente di scenari [Con più VIP](../load-balancer/load-balancer-multivip.md) come un ambiente con siti Web basati su SSL.

### <a name="vpn-gateways"></a>Gateway VPN
[Gateway VPN Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md) viene utilizzato per connettere una rete virtuale Azure (VNet) per altri VNets Azure o a una rete locale. È necessario assegnare un indirizzo IP pubblico per la **configurazione di indirizzi IP** per comunicare con la rete remota. Attualmente, è possibile assegnare solo un indirizzo IP pubblico *dinamico* per un gateway VPN.

### <a name="application-gateways"></a>Gateway per applicazioni
È possibile associare un indirizzo IP pubblico con un [Gateway di applicazioni](../application-gateway/application-gateway-introduction.md)di Azure, mediante l'assegnazione per la configurazione del gateway **front-end** . L'indirizzo IP pubblico funge da un indirizzo VIP bilanciamento del carico. Attualmente, è possibile assegnare solo un indirizzo IP pubblico *dinamico* per una configurazione front-end gateway un'applicazione.

### <a name="at-a-glance"></a>In immediata
Nella tabella seguente mostra la proprietà specifica mediante il quale può essere associato a una risorsa principale e i metodi di allocazione possibili (statici o dinamici) che possono essere utilizzati un indirizzo IP pubblico.

|Risorse di primo livello|Associazione di indirizzo IP|Dinamico|Statica|
|---|---|---|---|
|Macchina virtuale|Interfaccia di rete|Sì|Sì|
|Bilanciamento del carico|Configurazione front-end|Sì|Sì|
|Gateway VPN|Configurazione di gateway IP|Sì|No|
|Gateway di applicazioni|Configurazione front-end|Sì|No|

## <a name="private-ip-addresses"></a>Indirizzi IP privati
Indirizzi IP privati consentono Azure alle risorse di comunicare con altre risorse in una [rete virtuale](virtual-networks-overview.md) o a una rete locale attraverso un gateway VPN o ExpressRoute circuito, senza utilizzare un indirizzo IP raggiungibili tramite Internet.

Nel modello di distribuzione di Manager delle risorse di Azure, un indirizzo IP privato è associato a questi tipi di risorse Azure:

- Macchine virtuali
- Servizi di bilanciamento del carico interno (ILBs)
- Gateway per applicazioni

### <a name="allocation-method"></a>Metodo di allocazione
Un indirizzo IP privato allocato dall'intervallo di indirizzi della subnet a cui la risorsa è collegata. L'intervallo di indirizzi della subnet stesso fa parte di intervallo di indirizzi della VNet.

Esistono due metodi in cui è assegnato un indirizzo IP privato: *statica*o *dinamica* . Il metodo di allocazione predefinito è *dinamico*, in cui l'indirizzo IP è allocato automaticamente dal subnet della risorsa (tramite DHCP). Questo indirizzo IP può cambiare quando si interrompe e avviare la risorsa.

È possibile impostare il metodo di allocazione su *statica* per assicurarsi che l'indirizzo IP rimane inalterato. In questo caso, è necessario specificare un indirizzo IP che fa parte di subnet della risorsa.

Indirizzi IP privati statici sono utilizzati per:

- Macchine virtuali che fungono da controller di dominio o server DNS.
- Risorse che richiedono le regole del firewall utilizzando indirizzi IP.
- Risorse accessibili da altre applicazioni/risorse tramite un indirizzo IP.

### <a name="virtual-machines"></a>Macchine virtuali
Un indirizzo IP privato viene assegnato all' **interfaccia di rete** di [Windows](../virtual-machines/virtual-machines-windows-about.md) o [Linux](../virtual-machines/virtual-machines-linux-about.md) macchine Virtuali. In caso di un'interfaccia di rete più macchine Virtuali ogni interfaccia ottiene un indirizzo IP privato assegnato. È possibile specificare il metodo di allocazione come dinamico o statico per un'interfaccia di rete.

#### <a name="internal-dns-hostname-resolution-for-vms"></a>Risoluzione di nome host DNS interna (per macchine virtuali)
Tutte le macchine virtuali di Azure sono configurate con [i server DNS gestiti Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) per impostazione predefinita, a meno che non è configurato in modo esplicito i server DNS personalizzati. Questi server DNS forniscono la risoluzione dei nomi interno per macchine virtuali che si trovano all'interno di VNet stesso.

Quando si crea una macchina virtuale, viene aggiunto un mapping per nome host dell'indirizzo IP privato ai server DNS gestiti Azure. In caso di un'interfaccia di rete più macchine Virtuali, il nome host siano associati all'indirizzo IP privato dell'interfaccia di rete principale.

Macchine virtuali configurate con i server DNS gestiti Azure saranno in grado di risolvere nomi host di tutte le macchine virtuali all'interno di loro VNet ai relativi indirizzi IP privati.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Gateway di applicazioni e servizi di bilanciamento del carico interno (ILB)
È possibile assegnare un indirizzo IP privato per la configurazione **front-end** di un [Bilanciamento del carico interno Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) o un [Gateway di applicazioni Azure](../application-gateway/application-gateway-introduction.md). Questo indirizzo IP privato funge da endpoint interno, accessibile solo alle risorse all'interno della rete virtuale (VNet) e reti remote connessi al VNet. È possibile assegnare un indirizzo IP privato statico o dinamico sia la configurazione front-end.

### <a name="at-a-glance"></a>In immediata
Nella tabella seguente mostra la proprietà specifica mediante il quale può essere associato a una risorsa principale e i metodi di allocazione possibili (statici o dinamici) che possono essere utilizzati un indirizzo IP privato.

|Risorse di primo livello|Associazione di indirizzi IP|Dinamico|Statica|
|---|---|---|---|
|Macchina virtuale|Interfaccia di rete|Sì|Sì|
|Bilanciamento del carico|Configurazione front-end|Sì|Sì|
|Gateway di applicazioni|Configurazione front-end|Sì|Sì|

## <a name="limits"></a>Limiti

L'elenco completo dei [limiti per la rete](azure-subscription-service-limits.md#networking-limits) in Azure sono indicati i limiti imposti su indirizzi IP. Questi limiti sono all'area geografica per l'abbonamento. È possibile [contattare il supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per aumentare i limiti predefiniti entro i limiti massimo in base alle esigenze aziendali.

## <a name="pricing"></a>Prezzi

Indirizzi IP pubblici, potrebbero essere una tariffa nominale. Per ulteriori informazioni sui prezzi di indirizzi IP in Azure, rivedere la pagina [prezzi indirizzo IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

## <a name="next-steps"></a>Passaggi successivi
- [Distribuire una macchina virtuale con un indirizzo IP pubblico statico tramite il portale di Azure](virtual-network-deploy-static-pip-arm-portal.md)
- [Distribuire una macchina virtuale con un indirizzo IP pubblico statico utilizzando un modello](virtual-network-deploy-static-pip-arm-template.md)
- [Distribuire una macchina virtuale con un indirizzo IP privato statico tramite il portale di Azure](virtual-networks-static-private-ip-arm-pportal.md)
