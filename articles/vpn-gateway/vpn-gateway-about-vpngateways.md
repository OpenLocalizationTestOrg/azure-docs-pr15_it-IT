<properties 
   pageTitle="Su Gateway VPN | Microsoft Azure"
   description="Informazioni sulle connessioni VPN Gateway per le reti virtuali Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc" />

# <a name="about-vpn-gateway"></a>Su Gateway VPN


Per inviare il traffico di rete tra Azure reti virtuali e le posizioni in locale e anche tra reti virtuali all'interno di Azure (VNet-VNet) viene utilizzato un gateway di rete virtuale. Quando si configura un gateway VPN, è necessario creare e configurare un gateway virtuali e una connessione di rete virtuale gateway.

Nel modello di distribuzione di Manager delle risorse, quando si crea una risorsa di gateway virtuali, specificare le impostazioni diverse. Tra le impostazioni necessarie '-GatewayType'. Esistono due tipi di gateway virtuali: Vpn ed ExpressRoute. 

Quando il traffico di rete viene inviato in una connessione privata dedicata, utilizzare il tipo di gateway 'ExpressRoute'. Questa operazione si intende anche come gateway ExpressRoute. Quando il traffico di rete è crittografato tramite una connessione pubblica, utilizzare il tipo di gateway 'Vpn'. Questo processo viene definito un gateway VPN. Sito a sito punto al sito e connessioni VNet-VNet utilizzano un gateway VPN.

Ogni virtuali possono contenere solo un gateway di rete virtuale per ogni tipo di gateway. Ad esempio, può avere una rete virtuale gateway che usa - GatewayType ExpressRoute e quello che usa - GatewayType Vpn. In questo articolo è incentrato principalmente su Gateway VPN. Per ulteriori informazioni su ExpressRoute, vedere la [Panoramica tecnica ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="pricing"></a>Prezzi

[AZURE.INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)] 


## <a name="gateway-skus"></a>SKU di gateway

[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

Per ulteriori informazioni su gateway SKU, vedere [Gli SKU di Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

### <a name="estimated-aggregate-throughput-by-sku"></a>Velocità effettiva aggregata prevista da SKU

Nella tabella seguente mostra i tipi di gateway e la velocità di aggregata stimata. In questa tabella si applica alle Manager delle risorse e modelli di distribuzione classica.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

## <a name="configuring-a-vpn-gateway"></a>Configura un Gateway VPN

Quando si configura un gateway VPN, le istruzioni che si usa dipendono il modello di distribuzione utilizzati per creare la rete virtuale. Ad esempio, se è stato creato il VNet utilizzando il modello di distribuzione classica, utilizzare le linee guida e le istruzioni per il modello di distribuzione classico per creare e configurare le impostazioni del gateway VPN. Per ulteriori informazioni sui modelli di distribuzione, vedere [gestione di informazioni sulle risorse e modelli di distribuzione classica](../resource-manager-deployment-model.md).

Una connessione di gateway VPN si basa su più risorse che sono configurate con impostazioni specifiche. La maggior parte delle risorse possono essere configurata separatamente, anche se devono essere configurati in un determinato ordine in alcuni casi. È possibile avviare la creazione e configurazione di risorse utilizzando uno strumento di configurazione, ad esempio il portale di Azure. Quindi in un secondo momento è possibile passare a un altro strumento, ad esempio PowerShell per configurare ulteriori risorse o per modificare le risorse esistenti se applicabile. Al momento non è possibile configurare ogni impostazione della risorsa e delle risorse nel portale di Azure. Le istruzioni disponibili in articoli per ogni topologia di connessione specificare quando è necessaria una uno strumento di configurazione specifiche. Per informazioni sulle singole risorse e le impostazioni per il Gateway VPN, vedere [impostazioni su Gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).

Nelle sezioni seguenti sono contengano tabelle che elencano:

- modello di distribuzione disponibili
- strumenti di configurazione disponibili
- collegamenti per accedere direttamente a un articolo, se disponibile

Utilizzare i diagrammi e le descrizioni per selezionare la topologia di connessione in modo che corrispondano ai propri requisiti. È possibile visualizzare i diagrammi topologie previsto principale, ma è possibile creare più complesse utilizzando i diagrammi come linee guida.

## <a name="site-to-site-and-multi-site"></a>In siti e più siti

### <a name="site-to-site"></a>Da sito

Una connessione di gateway VPN a siti (S2S) è una connessione tramite tunnel VPN IPsec/IKE (IKEv1 o IKEv2). Questo tipo di connessione richiede un dispositivo VPN disponibile in locale che contiene un indirizzo IP pubblico assegnato e non si trova dietro al NAT. Connessioni S2S possono essere usate per tra locali e ibride configurazioni.   

![Connessione S2S] (./media/vpn-gateway-about-vpngateways/demos2s.png "da sito")


### <a name="multi-site"></a>Più siti

È possibile creare e configurare una connessione di gateway VPN tra il VNet e più reti locale. Quando si utilizzano più connessioni, è necessario utilizzare un tipo di RouteBased VPN (gateway dinamico per VNets classica). Perché un VNet può contenere solo un gateway VPN, tutte le connessioni attraverso il gateway condividono della larghezza di banda. Spesso si tratta di una connessione "più siti".
 

![Connessione di più siti] (./media/vpn-gateway-about-vpngateways/demomulti.png "più siti")

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Modelli di distribuzione e i metodi per a siti e più siti

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 

## <a name="vnet-to-vnet"></a>VNet-VNet

La connessione di rete virtuale a un'altra rete virtuale (VNet-VNet) è simile alla connessione un VNet in un punto del sito locale. Entrambi i tipi di connettività per utilizzano un gateway VPN per fornire un tunnel protetto tramite IPsec/IKE. È anche possibile combinare VNet a VNet la comunicazione con configurazioni di connessione di più siti. Consente di stabilire topologie di rete che combinano la connettività tra locale con la connettività di rete tra virtuale.

Può essere VNets che ci si connette:

- nelle stesse o in altre aree
- in abbonamenti stessa o di un'altro 
- nei modelli di distribuzione diversi stesso


![VNet a VNet connessione] (./media/vpn-gateway-about-vpngateways/demov2v.png "vnet-vnet")

#### <a name="connections-between-deployment-models"></a>Connessioni tra i modelli di distribuzione

Azure ha due modelli di distribuzione: classica e Manager delle risorse. Se è stato utilizzato Azure per un certo tempo, è probabile che sia macchine virtuali di Azure e ruoli istanza in esecuzione in una VNet classica. Il più recente macchine virtuali e istanze del ruolo potrebbero essere in esecuzione in una VNet creati in Gestione risorse. È possibile creare una connessione tra VNets per consentire le risorse in un VNet per comunicare direttamente con le risorse in un altro.

#### <a name="vnet-peering"></a>Peering VNet

È possibile utilizzare VNet peering per creare la connessione di rete virtuale soddisfi alcuni requisiti. VNet peering non utilizza un gateway di rete virtuale. Per ulteriori informazioni, vedere [VNet peering](../virtual-network/virtual-network-peering-overview.md).


### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Modelli di distribuzione e i metodi per VNet a VNet

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 


## <a name="point-to-site"></a>Punto al sito

Una connessione di gateway VPN punto da sito (P2S) consente di creare una connessione protetta alla rete virtuale da un computer client. P2S è una connessione VPN SSTP (Secure Socket Tunneling Protocol). P2S non richiedono un dispositivo VPN o un indirizzo IP pubblico per l'uso. Per stabilire la connessione VPN, partire dal computer client. Questa soluzione è utile quando si desidera connettersi il VNet da una posizione remota, ad esempio da casa o una conferenza o quando è necessario solo alcuni client necessari per connettersi a un VNet. Connessioni P2S è utilizzabile in combinazione con le connessioni S2S tramite lo stesso gateway VPN, purché tutti i requisiti di configurazione per entrambe le connessioni siano compatibili.


Connessione ![punto da sito] (./media/vpn-gateway-about-vpngateways/demop2s.png "punto al sito")

### <a name="deployment-models-and-methods-for-point-to-site"></a>Modelli di distribuzione e i metodi per punto al sito

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 


## <a name="expressroute"></a>ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

In una connessione ExpressRoute un gateway di rete virtuale è configurato con il tipo di gateway 'ExpressRoute' anziché 'Vpn'. Per ulteriori informazioni su ExpressRoute, vedere la [Panoramica tecnica ExpressRoute](../expressroute/expressroute-introduction.md).


## <a name="site-to-site-and-expressroute-coexisting-connections"></a>Connessioni di dominio al sito ed ExpressRoute

ExpressRoute è una connessione diretta, dedicata da sulla WAN (non su Internet) a Microsoft Services, tra cui Azure. Da sito VPN traffico viaggia crittografato tramite Internet. La possibilità di configurare le connessioni VPN da sito ed ExpressRoute per la stessa rete virtuale offre molti vantaggi.

È possibile configurare una connessione VPN da sito come un percorso sicuro failover per ExpressRoute o utilizzare VPN da sito per connettersi ai siti che non fanno parte della rete, ma che esegue la connessione tramite ExpressRoute. Si noti che sono necessari due gateway virtuali per la rete virtuale stesso, utilizzando una - GatewayType Vpn e l'altro utilizzando - GatewayType ExpressRoute.


![Connessione Coexist] (./media/vpn-gateway-about-vpngateways/demoer.png "expressroute site2site")


### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Modelli di distribuzione e i metodi per S2S ed ExpressRoute

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)] 


## <a name="next-steps"></a>Passaggi successivi

Pianificare la configurazione di gateway VPN. Vedere [pianificazione di Gateway VPN e progettazione](vpn-gateway-plan-design.md) e [la connessione di rete locale in Azure](../guidance/guidance-connecting-your-on-premises-network-to-azure.md).








 
