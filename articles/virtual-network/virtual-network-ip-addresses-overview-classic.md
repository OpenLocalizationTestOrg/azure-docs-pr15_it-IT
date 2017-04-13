<properties
   pageTitle="Pubblici e privati gli indirizzi IP (classico) in Azure | Microsoft Azure"
   description="Informazioni sulle pubblici e privati gli indirizzi IP in Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="jdial" />

# <a name="ip-addresses-classic-in-azure"></a>Indirizzi IP (classico) in Azure
È possibile assegnare indirizzi IP alle risorse Azure per comunicare con altre risorse Azure, la rete locale e Internet. Esistono due tipi di indirizzi IP è possibile usare in Azure: pubblici e privati.

Indirizzi IP utilizzati per la comunicazione con Internet, inclusi i servizi di Azure pubblico.

Indirizzi IP privati vengono utilizzati per la comunicazione all'interno di una rete virtuale Azure (VNet), un servizio cloud e la rete locale quando si utilizza un gateway VPN o circuito ExpressRoute per estendere la rete in Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Informazioni su come [eseguire questa procedura utilizzando il modello di distribuzione di Manager delle risorse](virtual-network-ip-addresses-overview-arm.md).

## <a name="public-ip-addresses"></a>Indirizzi IP pubblici
Indirizzi IP pubblici consentono Azure alle risorse di comunicare con i servizi Internet e Azure pubblico, ad esempio [Azure Redis Cache](https://azure.microsoft.com/services/cache/), [Hub di evento Azure](https://azure.microsoft.com/services/event-hubs/), [database SQL](../sql-database/sql-database-technical-overview.md)e [lo spazio di archiviazione Azure](../storage/storage-introduction.md).

Un indirizzo IP pubblico è associato ai tipi di risorse seguenti:

- Servizi cloud
- IaaS macchine)
- Istanze del ruolo PaaS
- Gateway VPN
- Gateway per applicazioni

### <a name="allocation-method"></a>Metodo di allocazione
Quando un indirizzo IP pubblico deve essere assegnate a una risorsa Azure, è *in modo dinamico* ricevuto da un pool di indirizzi IP pubblico disponibile all'interno del percorso che della risorsa viene creata. Questo indirizzo IP viene rilasciato quando la risorsa viene interrotta. In caso di un servizio cloud, si verifica questo evento quando vengono interrotti tutte le istanze di ruolo, che può essere evitato utilizzando un indirizzo IP *statico* (riservato) (vedere [Servizi Cloud](#Cloud-services)).

>[AZURE.NOTE] L'elenco di intervalli di indirizzi IP da cui indirizzi IP vengono assegnati alle risorse Azure viene pubblicato in [intervalli di indirizzi IP di Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="dns-hostname-resolution"></a>Risoluzione di nome host DNS
Quando si crea un VM IaaS o un servizio cloud, è necessario specificare un nome DNS servizio cloud che sia univoco su tutte le risorse in Azure. Consente di creare un mapping nei server DNS gestiti Azure per *dnsname*. cloudapp.net all'indirizzo IP pubblico della risorsa. Ad esempio, quando si crea un servizio cloud con il nome DNS servizio cloud di **contoso**, il nome di dominio completo **contoso.cloudapp.net** viene restituito un indirizzo IP pubblico (VIP) del servizio cloud. È possibile usare il nome di dominio completo per creare un record CNAME di dominio personalizzato che punta all'indirizzo IP pubblico in Azure.

### <a name="cloud-services"></a>Servizi cloud
Un servizio cloud ha sempre un indirizzo IP pubblico definito un indirizzo IP virtuale (VIP). In un servizio cloud per associare porte diverse in VIP a porte interne in macchine virtuali e istanze del ruolo all'interno del servizio cloud, è possibile creare i punti finali. 

Un servizio cloud può contenere più macchine virtuali IaaS o istanze del ruolo PaaS, tutti esposte tramite l'indirizzo VIP del servizio cloud stesso. È inoltre possibile assegnare [più VIP in un servizio cloud](../load-balancer/load-balancer-multivip.md), che consente di scenari con più VIP come ambiente con siti Web basati su SSL.

È possibile assicurarsi che l'indirizzo IP pubblico di un servizio cloud rimane lo stesso, anche quando si interrompe tutte le istanze di ruolo, tramite un indirizzo IP pubblico *statico* , denominato [IP riservato](virtual-networks-reserved-public-ip.md). È possibile creare una risorsa di indirizzi IP statica (riservata) in una posizione specifica e assegnarlo a qualsiasi altro servizio cloud in quella posizione. Non è possibile specificare l'indirizzo IP effettivo per l'indirizzo IP riservato, è allocato dal pool di indirizzi IP disponibili nel percorso che è stata creata. Questo indirizzo IP non viene rilasciato finché non viene eliminato in modo esplicito.

(Riservato) pubblici indirizzi IP statici uso comune negli scenari in cui un servizio cloud:

- richiede le regole firewall la configurazione degli utenti finali.
- dipende dall'esterno DNS la risoluzione dei nomi, e un indirizzo IP dinamici richiedono l'aggiornamento di un record.
- fruisce dei servizi web esterni che utilizzano il modello di sicurezza IP in base a.
- utilizza i certificati SSL collegati a un indirizzo IP.

>[AZURE.NOTE] Quando si crea una macchina virtuale classica, viene creato un contenitore *servizio cloud* da Azure, che ha un indirizzo IP virtuale (VIP). Quando tramite portale viene eseguita la creazione, una predefinita RDP o SSH *endpoint* viene configurato tramite il portale in modo che sia possibile connettersi al macchine Virtuali tramite l'indirizzo VIP del servizio cloud. Questo indirizzo VIP del servizio cloud possono riservate, che in modo efficace fornisce un indirizzo IP riservato per connettere la macchina virtuale. È possibile aprire porte aggiuntive configurando più endpoint.

### <a name="iaas-vms-and-paas-role-instances"></a>Macchine virtuali IaaS e PaaS istanze del ruolo
È possibile assegnare un indirizzo IP pubblico direttamente a una IaaS [macchine Virtuali](../virtual-machines/virtual-machines-linux-about.md) o un'istanza di ruolo PaaS all'interno di un servizio cloud. Si parla come livello di istanza pubblico indirizzo IP ([ILPIP](virtual-networks-instance-level-public-ip.md)). Può essere dinamica solo l'indirizzo IP pubblico.

>[AZURE.NOTE] Questa è la differenza tra l'indirizzo VIP del servizio cloud, è un contenitore per macchine virtuali IaaS o PaaS istanze del ruolo, poiché un servizio cloud può contenere più macchine virtuali IaaS o istanze del ruolo PaaS, tutto è esposto tramite l'indirizzo VIP del servizio cloud stesso.

### <a name="vpn-gateways"></a>Gateway VPN
Un [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) può essere utilizzato per connettere un VNet Azure ad altri VNets Azure o reti locale. Un gateway VPN viene assegnato un pubblico IP indirizzo *in modo dinamico*, che consente la comunicazione con la rete remota.

### <a name="application-gateways"></a>Gateway per applicazioni
Per Layer7 bilanciamento del carico per instradare il traffico di rete in base a HTTP, è possibile utilizzare un [gateway di applicazioni](../application-gateway/application-gateway-introduction.md) Azure. Gateway di applicazioni viene assegnato un pubblico IP indirizzo *in modo dinamico*, che funge da VIP bilanciamento del carico.

### <a name="at-a-glance"></a>In sintesi
Nella tabella seguente mostra ogni tipo di risorsa con metodi di allocazione possibili (dinamico/statico) e possibilità di assegnare più indirizzi IP.

|Risorsa|Dinamico|Statica|Più indirizzi IP|
|---|---|---|---|
|Servizio cloud|Sì|Sì|Sì|
|IaaS VM o PaaS istanza del ruolo|Sì|No|No|
|Gateway VPN|Sì|No|No|
|Gateway di applicazioni|Sì|No|No|

## <a name="private-ip-addresses"></a>Indirizzi IP privati
Indirizzi IP privati consentono Azure alle risorse di comunicare con altre risorse in un servizio cloud o a una [rete virtuale](virtual-networks-overview.md)(VNet) o alla rete locale (tramite un gateway VPN oppure ExpressRoute circuito), senza utilizzare un indirizzo IP raggiungibili tramite Internet.

Nel modello di distribuzione classica Azure, è possibile assegnare un indirizzo IP privato per le risorse di Azure seguenti:

- Macchine virtuali IaaS e PaaS istanze del ruolo
- Servizio di bilanciamento del carico interno
- Gateway di applicazioni

### <a name="iaas-vms-and-paas-role-instances"></a>Macchine virtuali IaaS e PaaS istanze del ruolo
Macchine () create con il modello di distribuzione classica assumono sempre una posizione in un servizio cloud, simile a istanze del ruolo PaaS. Il comportamento di indirizzi IP privati pertanto sono simili per queste risorse.

È importante tenere presente che è possibile distribuire un servizio cloud in due modi:

- Come *prodotto autonomo* servizio cloud, in cui non è incluso in una rete virtuale.
- Come parte di una rete virtuale.

#### <a name="allocation-method"></a>Metodo di allocazione
In caso di un servizio cloud *autonoma* , le risorse è possibile ottenere un privato IP indirizzo allocato *dinamicamente* dall'intervallo di indirizzi IP privato Azure Data Center. Può essere utilizzato solo per la comunicazione con altre macchine virtuali all'interno del servizio cloud stesso. Questo indirizzo IP può cambiare quando la risorsa viene interrotta e avviata.

In caso di un servizio cloud distribuito all'interno di una rete virtuale, le risorse ottenere indirizzi IP privato ricevuto dall'intervallo di indirizzi di subnet associato (come specificato nella configurazione di rete). Per le comunicazioni tra tutte le macchine virtuali all'interno di VNet, è possibile utilizzare questo indirizzo IP privato.

Inoltre, in caso di servizi cloud all'interno di un VNet, un indirizzo IP privato allocato *dinamicamente* (tramite DHCP) per impostazione predefinita. È possibile modificare quando la risorsa viene interrotta e avviata. Per garantire che l'indirizzo IP rimane inalterato, è necessario impostare il metodo di allocazione *statica*e specificare un indirizzo IP all'interno dell'intervallo indirizzo corrispondente.

Indirizzi IP privati statici sono utilizzati per:

 - Macchine virtuali che fungono da controller di dominio o server DNS.
 - Macchine virtuali che richiedono le regole del firewall utilizzando indirizzi IP.
 - Macchine virtuali che esegue servizi accessibili da altre applicazioni tramite un indirizzo IP.

#### <a name="internal-dns-hostname-resolution"></a>Risoluzione di nome host DNS interna
Tutte le istanze del ruolo PaaS e macchine virtuali di Azure sono configurate con [i server DNS gestiti Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) per impostazione predefinita, a meno che non è configurato in modo esplicito i server DNS personalizzati. Questi server DNS forniscono la risoluzione dei nomi interno per macchine virtuali e istanze del ruolo che si trovano all'interno del servizio VNet o cloud stesso.

Quando si crea una macchina virtuale, viene aggiunto un mapping per nome host dell'indirizzo IP privato ai server DNS gestiti Azure. In caso di una macchina virtuale multi-NIC, il nome host siano associati all'indirizzo IP privato della scheda di rete principale. Tuttavia, questa informazione sono limitati alle risorse all'interno della stessa servizio cloud o VNet.

In caso di un servizio cloud *autonoma* , sarà possibile risolvere i nomi host di tutte le istanze di macchine virtuali/ruolo all'interno lo stesso servizio cloud. In caso di un servizio cloud all'interno di un VNet, sarà possibile risolvere i nomi host di tutte le istanze di macchine virtuali/ruolo all'interno di VNet.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Gateway di applicazioni e servizi di bilanciamento del carico interno (ILB)
È possibile assegnare un indirizzo IP privato per la configurazione **front-end** di un [Bilanciamento del carico interno Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) o un [Gateway di applicazioni Azure](../application-gateway/application-gateway-introduction.md). Questo indirizzo IP privato funge da endpoint interno, accessibile solo alle risorse all'interno della rete virtuale (VNet) e reti remote connessi al VNet. È possibile assegnare un indirizzo IP privato statico o dinamico sia la configurazione front-end. È inoltre possibile assegnare più indirizzi IP privati per abilitare gli scenari con più vip.

### <a name="at-a-glance"></a>In sintesi
Nella tabella seguente mostra ogni tipo di risorsa con metodi di allocazione possibili (dinamico/statico) e possibilità di assegnare più indirizzi IP privati.

|Risorsa|Dinamico|Statica|Più indirizzi IP|
|---|---|---|---|
|Macchine Virtuali (in un servizio cloud *autonoma* )|Sì|Sì|Sì|
|Istanza del ruolo PaaS (in un servizio cloud *autonoma* )|Sì|No|Sì|
|Istanza di ruolo macchine Virtuali o PaaS (in VNet)|Sì|Sì|Sì|
|Front-end bilanciamento carico interno|Sì|Sì|Sì|
|Front-end di applicazione gateway|Sì|Sì|Sì|

## <a name="limits"></a>Limiti

Nella tabella seguente mostra i limiti imposti su IP indirizzamento in Azure per abbonamento. È possibile [contattare il supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per aumentare i limiti predefiniti entro i limiti massimo in base alle esigenze aziendali.

||Limite predefinito|Limite massimo|
|---|---|---|
|Indirizzi IP pubblici (dinamico)|5|contattare il supporto tecnico|
|Indirizzi IP pubblici riservati|20|contattare il supporto tecnico|
|VIP pubblico per ogni distribuzione (servizio cloud)|5|contattare il supporto tecnico|
|Privato VIP (ILB) per ogni distribuzione (servizio cloud)|1|1|

Assicurarsi di leggere l'elenco completo dei [limiti per la rete](azure-subscription-service-limits.md#networking-limits) in Azure.

## <a name="pricing"></a>Prezzi

Nella maggior parte dei casi, gli indirizzi IP pubblici sono gratuiti. Esiste una tariffa nominale usare aggiuntivi e/o statici indirizzi IP. Assicurarsi di sapere [prezzi struttura per IP pubblico](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Differenze tra Manager delle risorse e distribuzioni classiche
Di seguito è un confronto delle funzionalità di indirizzi IP di Manager delle risorse e il modello di distribuzione classica.

||Risorsa|Classica|Manager delle risorse|
|---|---|---|---|
|**Indirizzo IP pubblico**|MACCHINE VIRTUALI|Definito un ILPIP (solo dinamico)|Definito un indirizzo IP pubblico (dinamico o statico)|
|||Assegnata a un VM IaaS o un'istanza di ruolo PaaS|Associato al pulsante della macchina virtuale|
||Bilanciamento del carico esposto Internet|Definito VIP (dinamico) o IP riservato (statico)|Definito un indirizzo IP pubblico (dinamico o statico)|
|||Assegnato a un servizio cloud|Associato alla configurazione front-end di bilanciamento del carico|
||||
|**Indirizzo IP privato**|MACCHINE VIRTUALI|Al plantule|Definito un indirizzo IP privato|
|||Assegnata a un VM IaaS o un'istanza di ruolo PaaS|Assegnata al pulsante della macchina virtuale|
||Servizio di bilanciamento del carico interno (ILB)|Assegnato a ILB (dinamico o statico)|Assegnate alla configurazione front-end di ILB (statica o dinamica)|

## <a name="next-steps"></a>Passaggi successivi
- [Distribuire una macchina virtuale con un indirizzo IP statico privato](virtual-networks-static-private-ip-classic-pportal.md) tramite il portale classico.
