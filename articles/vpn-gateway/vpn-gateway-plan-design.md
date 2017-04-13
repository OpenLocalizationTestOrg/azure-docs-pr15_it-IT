<properties 
   pageTitle="Pianificazione di Gateway VPN e progettazione | Microsoft Azure"
   description="Informazioni sui Gateway VPN pianificazione e progettazione per tra locale, ibrida e connessioni VNet-VNet"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc"/>

# <a name="planning-and-design-for-vpn-gateway"></a>Pianificazione e progettazione per VPN Gateway

Pianificazione e configurazione del cross locale e configurazioni VNet-VNet può essere semplice o complessa, a seconda delle esigenze di rete. In questo articolo illustra considerazioni sulla base di pianificazione e progettazione.

## <a name="planning"></a>Pianificazione


### <a name="compare"></a>Opzioni di connettività tra locale

Se si desidera connettersi ai siti locale a una rete, si dispone di tre diversi modi per farlo: da sito, punto al sito ed ExpressRoute. Confrontare le connessioni locali tra diversi disponibili. L'opzione che scelta dipende da diversi motivi, ad esempio:


- Quali tipi di velocità richiede la soluzione?
- Si vuole comunicare su Internet tramite VPN sicura o tramite una connessione privata?
- Si dispone di un indirizzo IP pubblico disponibile per l'uso?
- Si prevede di utilizzare un dispositivo VPN? In caso affermativo, è compatibile?
- Ci si sta collegando pochi computer o si desidera una connessione permanente del sito?
- Quali tipi di gateway VPN sono necessario per la soluzione che si desidera creare?
- Il gateway SKU è necessario utilizzare?


Nella tabella seguente consentono di stabilire l'opzione ottimale di connettività per la soluzione.


[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]



### <a name="gwrequire"></a>Requisiti del gateway di tipo VPN e SKU

[AZURE.INCLUDE [vpn-gateway-gwsku](../../includes/vpn-gateway-gwsku-include.md)]

Per ulteriori informazioni su gateway SKU, vedere [impostazioni del Gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

#### <a name="aggregate-throughput-by-sku-and-vpn-type"></a>Velocità di trasmissione aggregata in base al tipo SKU e VPN

Nella tabella seguente mostra i tipi di gateway e la velocità di aggregata stimata. La velocità di aggregata stimata può essere un fattori determinanti per la progettazione.
Prezzi si differenziano tra gateway SKU. Per informazioni sui prezzi, vedere [Prezzi Gateway VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). In questa tabella si applica alle Manager delle risorse e modelli di distribuzione classica.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

#### <a name="supported-configurations-by-sku-and-vpn-type"></a>Configurazioni supportate in base al tipo SKU e VPN

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 

### <a name="wf"></a>Flusso di lavoro

L'elenco seguente illustra il flusso di lavoro comune per la connettività cloud:

1.  Progettare e pianificare la topologia di connettività ed elencare gli spazi di indirizzi per tutte le reti a cui connettersi.
2.  Creare una rete virtuale Azure. 
3.  Creare un gateway VPN per la rete virtuale.
4.  Creare e configurare le connessioni a reti locale o altre reti virtuale (in base alle esigenze).
5.  Creare e configurare una connessione punto a sito per il gateway VPN Azure (in base alle esigenze).
 

## <a name="design"></a>Struttura

### <a name="topologies"></a>Topologie di connessione

Iniziare esaminando i diagrammi vedere l'articolo [Su Gateway VPN](vpn-gateway-about-vpngateways.md) . L'articolo contiene diagrammi di base, modelli di distribuzione per ogni topologia (Manager delle risorse o classica) e quali distribuzione degli strumenti è possibile utilizzare per distribuire la configurazione.   

### <a name="designbasics"></a>Nozioni fondamentali sulla progettazione

Nelle sezioni seguenti vengono descritte le basi di gateway VPN. Inoltre, valutare la possibilità di [rete limitazioni dei servizi](../articles/azure-subscription-service-limits.md#networking-limits).


#### <a name="subnets"></a>Sulle subnet

Durante la creazione di connessioni, è necessario prendere in considerazione gli intervalli di subnet. Non possono essere sovrapposti intervalli di indirizzi subnet. Una subnet sovrapposta è quando una rete virtuale o una posizione locale contiene lo stesso spazio indirizzo contenente l'altra posizione. Questo significa che è necessario ai progettisti della rete per la rete locale locale ritagliare un intervallo per è possibile utilizzare per l'indirizzo IP Azure indirizzamento spazio/subnet. È necessario di spazio di indirizzi che non è in uso nella rete locale locale. 

Come evitare sovrapposti subnet è importante anche quando si lavora con le connessioni a VNet a VNet. Se si sovrappongono le subnet e un indirizzo IP è presente in invio e destinazione VNets, VNet-VNet connessioni esito negativo. Azure non è possibile distribuire i dati a altri VNet perché l'indirizzo di destinazione fa parte di VNet invio. 

Gateway VPN richiedono subnet specifica denominata subnet un gateway. Tutte le subnet gateway devono essere denominate GatewaySubnet per il corretto funzionamento. Assicurarsi di non assegnare un nome subnet gateway a un nome diverso e non distribuire macchine virtuali o qualsiasi altro elemento alla subnet gateway. Vedere [subnet Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>Sui gateway di rete locale

Il gateway di rete locale in genere fa riferimento alla posizione locale. Nel modello di distribuzione classico, il gateway di rete locale viene considerato come un sito di rete locale. Quando si configura un gateway di rete locale, assegnarle un nome, specificare l'indirizzo IP pubblico del dispositivo VPN locale e specificare i prefissi indirizzo che si trovano nella posizione locale. Azure esamina i prefissi di indirizzo di destinazione per il traffico di rete, consulta la configurazione è stato specificato per il gateway di rete locale e instrada pacchetti di conseguenza. È possibile modificare questi prefissi indirizzo in base alle esigenze. Per ulteriori informazioni, vedere [gateway di rete locale](vpn-gateway-about-vpn-gateway-settings.md#lng).


#### <a name="gwtype"></a>Informazioni sui tipi di gateway

Selezionare il tipo di gateway corretta per la topologia è fondamentale. Se si seleziona il tipo di problema, è possibile che il gateway non funzionerà correttamente. Il tipo di gateway specifica come il gateway stesso si connette ed è un'impostazione di configurazione necessarie per il modello di distribuzione di Manager delle risorse.

I tipi di gateway sono:

- VPN
- ExpressRoute

#### <a name="connectiontype"></a>Informazioni sui tipi di connessione

Ogni configurazione richiede un tipo di connessione specifiche. I tipi di connessione sono:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient


#### <a name="vpntype"></a>Informazioni sui tipi di VPN

Ogni configurazione richiede un tipo specifico di VPN. Se si desidera combinare due configurazioni, ad esempio la creazione di una connessione a siti e una connessione punto a sito VNet stesso, è necessario utilizzare un tipo di VPN che soddisfi i requisiti entrambi connessione.

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)] 

Le tabelle seguenti illustrano il tipo di VPN come eseguire il mapping a ciascuna configurazione di connessione. Verificare che il tipo di VPN per il gateway corrisponde alla configurazione che si desidera creare. 


[AZURE.INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)] 

### <a name="devices"></a>Dispositivi VPN per le connessioni al sito

Per configurare una connessione a siti, indipendentemente dal modello di distribuzione, è necessario quanto segue:

- Un dispositivo VPN compatibile con gateway VPN Azure
- Un indirizzo IP IPv4 pubblico che non è una NAT

È necessario hanno esperienza di configurazione del dispositivo VPN oppure un utente che è possibile configurare il dispositivo dell'utente. Per ulteriori informazioni sui dispositivi VPN, vedere [dispositivi sulle VPN](vpn-gateway-about-vpn-devices.md). L'articolo di dispositivi VPN contiene informazioni sui dispositivi convalidati, requisiti per i dispositivi che non sono stati convalidati e collegamenti a documenti di configurazione dispositivo se disponibile.

### <a name="forcedtunnel"></a>Valutare la possibilità di routing tunnel forzato

Per la maggior parte delle configurazioni, è possibile configurare tunneling forzata. Tunneling forzata consente di reindirizzamento o "forza" tutto il traffico Internet associazione alla propria posizione locale tramite un tunnel VPN da sito per ispezione e il controllo. Questo è un requisito di protezione critici per IT aziendali la maggior parte dei criteri. 

Senza tunneling forzata, il traffico Internet associazione da nelle macchine virtuali di Azure sarà sempre scorrere dall'infrastruttura di rete Azure direttamente fuori a Internet, senza l'opzione per consentire di esaminare o controllare il traffico. Accesso a Internet non autorizzato può comportare potenzialmente divulgazione o altri tipi di violazioni della protezione.

**Forzata tunneling diagramma**

![Connessione forzata Tunneling] (./media/vpn-gateway-plan-design/forced-tunnel.png "forzata tunneling")

In entrambi i modelli di distribuzione e l'utilizzo di strumenti diversi, è possibile configurare una connessione tunneling forzata. Nella tabella seguente per ulteriori informazioni, vedere. In questa tabella viene aggiornata appena nuovi articoli, nuovi modelli di distribuzione e altri strumenti disponibili per la configurazione. Quando un articolo è disponibile, è collega direttamente dalla tabella.

[AZURE.INCLUDE [vpn-gateway-table-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 



## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli [Domande frequenti su Gateway VPN](vpn-gateway-vpn-faq.md) e [Su Gateway VPN](vpn-gateway-about-vpngateways.md) per altre informazioni semplificare la struttura.

Per ulteriori informazioni sulle impostazioni di gateway specifico, vedere [Sulle impostazioni del Gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).




