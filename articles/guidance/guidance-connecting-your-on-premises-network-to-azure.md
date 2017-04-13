<properties
   pageTitle="La connessione di rete locale in Azure | Microsoft Azure"
   description="Spiega che vengono confrontati i vari metodi disponibili per la connessione ai servizi cloud Microsoft, ad esempio Azure, Office 365 e Dynamics CRM Online."
   services=""
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags=""/>
<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="jdial"/>
   
# <a name="connecting-your-on-premises-network-to-azure"></a>La connessione di rete locale in Azure

Microsoft offre diversi tipi di servizi cloud. Mentre è possibile connettersi a tutti i servizi Internet pubblica, è anche possibile connettersi ad alcuni dei servizi tramite un tunnel rete privata virtuale (VPN) tramite Internet o su una connessione diretta privata a Microsoft. In questo articolo consente di determinare quale opzione di integrazione applicativa verrà adatta alle proprie esigenze in base ai tipi di servizi cloud Microsoft che utilizzano. La maggior parte delle organizzazioni utilizzano più tipi di connessione descritti di seguito.

## <a name="connecting-over-the-public-internet"></a>La connessione Internet pubblica

Questo tipo di connessione consente di accedere ai servizi cloud Microsoft direttamente su Internet, come illustrato di seguito.

![Internet] (./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

La connessione è in genere il primo tipo usato per connettersi ai servizi cloud Microsoft. Nella tabella riportata di seguito sono elencati i vantaggi e svantaggi di questo tipo di connessione.



| **Vantaggi**| **Considerazioni**|
|---------|---------|
|Non richiede alcuna modifica alla rete locale di tutti i dispositivi client avere accesso illimitato a tutti gli indirizzi IP e porte su Internet.|Se il traffico è spesso crittografato tramite HTTPS, è possibile intercettare durante il transito poiché scorre Internet pubblica.|
|Connettersi a tutti i servizi cloud Microsoft esposti a Internet.|Latenza imprevisto perché la connessione attraversa Internet.|
|Utilizza la connessione Internet esistente.||
|Non richiede la gestione dei dispositivi di integrazione applicativa.||

La connessione non ha connettività o i costi di larghezza di banda dal momento che si usa la connessione Internet esistente. 

## <a name="connecting-with-a-point-to-site-connection"></a>Connessione con una connessione punto a sito

Questo tipo di connessione consente di accedere per alcuni servizi cloud Microsoft tramite un tunnel SSTP Secure Socket Tunneling Protocol () tramite Internet, come illustrato di seguito.

![P2S] Connessione (./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "punto da sito")

La connessione è effettuata per la connessione Internet esistente, ma richiede l'uso di un Gateway VPN Azure. Nella tabella riportata di seguito sono elencati i vantaggi e svantaggi di questo tipo di connessione.

| **Vantaggi**| **Considerazioni**|
|---------|---------|
|Non richiede alcuna modifica alla rete locale di tutti i dispositivi client avere accesso illimitato a tutti gli indirizzi IP e porte su Internet.|Se il traffico è crittografato tramite IPSec, è possibile intercettare durante il transito poiché scorre Internet pubblica.|
|Utilizza la connessione Internet esistente.|Latenza imprevisto perché la connessione attraversa Internet.|
|Velocità massimo di 200 Mb/s per ciascun gateway.|È necessario creare e gestire le connessioni separate tra ogni dispositivo nella rete locale e ogni gateway a che ogni dispositivo deve connettersi.|
|Può essere utilizzato per connettersi ai servizi Azure che possono essere collegati a un reti virtuali Azure (VNet), ad esempio macchine virtuali di Azure e servizi Cloud Windows Azure.|Richiede minime amministrazione in corso di un Gateway VPN Azure.|
||Non può essere usato per connettersi a Microsoft Office 365 o a Dynamics CRM Online.
||Non può essere usato per connettersi ai servizi Azure che non possono essere connesso a un VNet.|

Acquisire familiarità con il servizio [Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) , i [prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway)e trasferimento dati in uscita [prezzi](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Connessione con una connessione a siti

Questo tipo di connessione consente di accedere per alcuni servizi cloud Microsoft tramite un tunnel IPSec tramite Internet, come illustrato di seguito.

![S2S] (./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "Connessione a siti")

La connessione è effettuata per la connessione Internet esistente, ma richiede l'uso di un Gateway VPN Azure con i prezzi associati e il prezzo di trasferimento dati in uscita. Nella tabella riportata di seguito sono elencati i vantaggi e svantaggi di questo tipo di connessione.

| **Vantaggi**| **Considerazioni**|
|---------|---------|
|Tutti i dispositivi nella rete locale possono comunicare con i servizi di Azure connessi a una VNet in modo che non è necessario configurare le singole connessioni per ogni dispositivo.|Se il traffico è crittografato tramite IPSec, è possibile intercettare durante il transito poiché scorre Internet pubblica.|
|Utilizza la connessione Internet esistente.|Latenza imprevisto perché la connessione attraversa Internet.|
|Può essere utilizzato per connettersi ai servizi Azure che possono essere connesso a un VNet, ad esempio macchine virtuali e servizi Cloud.|È necessario configurare e gestire un convalidato dispositivo VPN * locale.|
|Velocità massimo di 200 Mb/s per ciascun gateway.|Richiede minime amministrazione in corso di un Gateway VPN Azure.|
|Possibile forzare il traffico in uscita avviato da macchine virtuali cloud tramite la rete locale di ispezione e la registrazione con route definite dall'utente o bordo Gateway Protocol (BGP) * *.|Non può essere usato per connettersi a Microsoft Office 365 o a Dynamics CRM Online.|
||Non può essere usato per connettersi ai servizi Azure che non possono essere connesso a un VNet.|
||Se si utilizzano servizi che consentono di avviare le connessioni al dispositivi locale e i criteri di sicurezza lo richiede, potrebbe essere necessario un firewall tra la rete locale e Azure.|

- * Visualizzare un elenco di [convalida dispositivi VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#validated-vpn-devices).
- * * Ulteriori informazioni sull'utilizzo di [route definite dall'utente](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) o [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) forzare il routing Azure VNets a un dispositivo locale.

## <a name="connecting-with-a-dedicated-private-connection"></a>Connessione con una connessione privata dedicata

Questo tipo di connessione consente di accedere a tutti i servizi cloud Microsoft tramite una connessione privata dedicata a Microsoft che non attraversano Internet, come illustrato di seguito.

![ER] (./media/guidance-connecting-your-on-premises-network-to-azure/er.png "Connessione ExpressRoute")

La connessione richiede l'uso del servizio ExpressRoute e una connessione a un provider di integrazione applicativa. Nella tabella riportata di seguito sono elencati i vantaggi e svantaggi di questo tipo di connessione.

| **Vantaggi**| **Considerazioni**|
|---------|---------|
|Il traffico non è possibile intercettare durante il transito Internet pubblica poiché viene utilizzata una connessione dedicata tramite un provider di servizi.|È necessario gestire router locale.|
|Larghezza di banda fino a 10 Gb/s per circuito ExpressRoute e la velocità effettiva fino a 2 Gb/s per ogni gateway.|Richiede una connessione a un provider di integrazione applicativa dedicata.|
|Latenza prevedibile poiché si tratta di una connessione dedicata a Microsoft che non attraversano Internet.|Può richiedere minime amministrazione in corso di una o più gateway VPN Azure (se si connette il circuito alla VNets).|
|Non richiede comunicazioni crittografate, anche se è possibile crittografare il traffico, se lo si desidera.| Se si sta utilizzando servizi cloud che avvia connessioni dispositivi locale, potrebbe essere necessario un firewall tra la rete locale e Azure.|
|Connettersi direttamente a tutti i servizi cloud Microsoft, con alcune eccezioni *.|Richiede il protocollo NAT (NAT) di indirizzi IP locale immettere il data center di Microsoft per i servizi che non possono essere connesso a VNet.* *|
|Possibile forzare il traffico in uscita avviato da macchine virtuali cloud tramite la rete locale di ispezione e la registrazione utilizzando BGP.|

- * Visualizzare un [elenco dei servizi](../expressroute/expressroute-faqs.md#supported-services) che non possono essere usate con ExpressRoute. L'abbonamento Azure deve essere approvato per connettersi a Office 365.  Vedere l'articolo [Azure ExpressRoute per Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) per informazioni dettagliate.
- * * Ulteriori informazioni su come ExpressRoute [NAT](../expressroute/expressroute-nat.md) requisiti.

Ulteriori informazioni su [ExpressRoute](../expressroute/expressroute-introduction.md)relative associato [prezzi](https://azure.microsoft.com/pricing/details/expressroute)e [i provider di integrazione applicativa](../expressroute/expressroute-locations.md#connectivity-provider-locations).

## <a name="additional-considerations"></a>Altre considerazioni

- Diverse opzioni indicate sono diversi limiti massimi che supportano per le connessioni VNet, le connessioni gateway e altri criteri. È consigliabile esaminare il Azure [limiti di rete](../azure-subscription-service-limits.md#networking-limits) per comprendere se uno di questi effetti in termini di tipi di connettività che si sceglie di utilizzare. 
- Se si prevede di collegare un gateway di una connessione VPN da sito alla stessa VNet come gateway ExpressRoute, è necessario acquisire familiarità con importanti vincoli prima di tutto. Vedere l'articolo [configurare ExpressRoute e connessioni di dominio al sito](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) per altri dettagli.

## <a name="next-steps"></a>Passaggi successivi

Le risorse seguenti illustrano come implementare i tipi di connessione contemplati in questo articolo.

-   [Implementare una connessione punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
-   [Implementare una connessione a siti](guidance-hybrid-network-vpn.md)
-   [Implementare una connessione privata dedicata](guidance-hybrid-network-expressroute.md)
-   [Implementare una connessione privata dedicata con una connessione a siti di disponibilità](guidance-hybrid-network-expressroute-vpn-failover.md)
