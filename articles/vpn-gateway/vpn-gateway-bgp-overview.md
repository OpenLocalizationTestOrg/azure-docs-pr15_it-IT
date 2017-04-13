<properties
   pageTitle="Panoramica delle BGP con gateway VPN Azure | Microsoft Azure"
   description="In questo articolo viene fornita una panoramica BGP con Azure VPN gateway."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/16/2016"
   ms.author="yushwang"/>

# <a name="overview-of-bgp-with-azure-vpn-gateways"></a>Panoramica delle BGP con gateway VPN Azure

In questo articolo viene fornita una panoramica del supporto BGP (bordo Gateway Protocol) in Azure VPN gateway.

## <a name="about-bgp"></a>Sulle BGP

BGP è il protocollo routing standard comunemente usato in Internet per lo scambio di informazioni su routing e accessibilità tra due o più reti. Se utilizzata nel contesto di reti virtuali Azure, BGP consente il gateway VPN Azure e i dispositivi VPN locali, denominati BGP colleghi o vicini per exchange "indirizza" che informano entrambi gateway sulla disponibilità e accessibilità per tali prefissi elaborata i gateway o router coinvolto. BGP è inoltre possibile abilitare il routing transito tra più reti mediante propagazione indirizza che un gateway BGP le apprende da un peer BGP a tutti gli altri peer BGP.
 
### <a name="why-use-bgp"></a>Perché usare BGP?

BGP è una funzionalità facoltativa che è possibile usare con gateway VPN basate su Route Azure. Inoltre, assicurarsi che i dispositivi VPN locale supportano BGP prima di attivare la caratteristica. È possibile continuare a usare gateway VPN Azure e i dispositivi VPN locale senza BGP. È equivalente all'opzione route statiche (senza BGP) *e* utilizzando il routing dinamico con BGP tra Azure e le reti.

Esistono diversi vantaggi e nuove funzionalità con BGP:

#### <a name="support-automatic-and-flexible-prefix-updates"></a>Supporta gli aggiornamenti automatici e flessibile il prefisso

Con BGP, è necessario dichiarare un prefisso minimo a un determinato peer BGP sul tunnel IPsec S2S VPN. Può essere assuma un prefisso host (/ 32) dell'indirizzo IP peer BGP del dispositivo VPN locale. È possibile controllare quali locale prefissi di rete che si desidera annunciare Azure per consentire la rete virtuale Azure accedere.
    
È anche possibile annunciare una maggiore prefissi contenenti alcuni dei prefissi degli indirizzi del VNet, ad esempio un grande privato spazio degli indirizzi IP (ad esempio, 10.0.0.0/8). Nota Se i prefissi non possono essere uguali con uno qualsiasi dei prefissi VNet. Tali route identici per i prefissi VNet verranno rifiutati.

>[AZURE.IMPORTANT] Attualmente, pubblicità la route predefinita (0.0.0.0/0) al gateway VPN Azure non sarà possibile. Vengono forniti ulteriori aggiornamenti dopo questa funzionalità è abilitata.

#### <a name="support-multiple-tunnels-between-a-vnet-and-an-on-premises-site-with-automatic-failover-based-on-bgp"></a>Supporta più tunnel tra un VNet e un sito locale con failover automatico in base a BGP

È possibile stabilire più connessioni tra i VNet Azure e i dispositivi VPN locale nella stessa posizione. Questa funzionalità sono disponibili più tunnel (i percorsi) tra le due reti una configurazione attivo attivo. Se uno dei tunnel disconnessi, il traffico si sposterà automaticamente tunnel rimanente route corrispondenti verranno ritirate tramite BGP.
    
Nel diagramma seguente viene mostrato un esempio di questa impostazione disponibilità:
    
![Più percorsi attivi](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

#### <a name="support-transit-routing-between-your-on-premises-networks-and-multiple-azure-vnets"></a>Supporto tecnico transito routing tra le reti locale e più VNets di Azure

BGP consente più gateway per informazioni e propagare prefissi da diverse reti, se sono direttamente o indirettamente connessi. Questo metodo consente transito routing con gateway Azure VPN tra siti locale o su più reti virtuali Azure.
    
Il diagramma seguente illustra un esempio di una topologia hop più percorsi multipli che possono transito il traffico tra le due reti locale attraverso gateway VPN Azure all'interno di Networks Microsoft:

![Transito hop multiple](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="bgp-faqs"></a>Domande frequenti BGP


[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 




## <a name="next-steps"></a>Passaggi successivi

Vedere [Guida introduttiva a BGP su gateway VPN Azure](./vpn-gateway-bgp-resource-manager-ps.md) per la procedura per configurare BGP per il cross locale e connessioni VNet a VNet.

