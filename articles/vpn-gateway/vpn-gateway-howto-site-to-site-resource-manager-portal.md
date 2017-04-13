<properties
   pageTitle="Creare una rete virtuale con una connessione VPN da sito tramite il portale di Azure e Gestione risorse di Azure | Microsoft Azure"
   description="Come creare VNet utilizzando il modello di distribuzione di Manager delle risorse e connetterlo al computer locale locale rete mediante una connessione di gateway S2S VPN."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-portal"></a>Creare un VNet con una connessione al sito tramite il portale di Azure

> [AZURE.SELECTOR]
- [Gestione risorse - portale Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Gestione risorse - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Classico: portale classica](vpn-gateway-site-to-site-create.md)


In questo articolo illustra la creazione di una rete virtuale e una connessione di gateway da sito VPN alla rete locale utilizzando il modello di distribuzione di gestione di risorse Azure e il portale di Azure. Le connessioni al sito possono essere usate per tra locali e ibride configurazioni.

![Diagramma](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/s2srmportal.png)


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelli di distribuzione e i metodi per le connessioni al sito

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Nella tabella seguente mostra i metodi per le configurazioni di siti e modelli di distribuzione attualmente disponibile. Quando un articolo con i passaggi di configurazione è disponibile, è collegare direttamente a da questa tabella.

[AZURE.INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurazioni aggiuntive 

Se si desidera connettere VNets, ma non viene creato una connessione a una posizione locale, vedere [configurare una connessione VNet a VNet](vpn-gateway-vnet-vnet-rm-ps.md). Se si desidera aggiungere una connessione da sito a un VNet che contiene già una connessione, vedere [aggiungere una connessione S2S a VNet con una connessione di gateway VPN esistente](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di disporre gli elementi seguenti prima di iniziare la configurazione:

- Un dispositivo VPN e chi è in grado di configurarlo. Vedere [informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md). Se non ha familiarità con la configurazione del dispositivo VPN o si ha familiarità con l'indirizzo IP intervalli nella configurazione di rete le in locale, è necessario per la coordinazione con qualcuno in grado di fornire i dettagli dell'utente.

- Esternamente esposto pubblico indirizzo IP del dispositivo VPN. Non è possibile individuare l'indirizzo IP dietro al NAT.
    
- Un abbonamento Azure. Se si dispone già di un abbonamento a Azure, è possibile attivare i [vantaggi dell'abbonato MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o segno backup di un [account gratuito](http://azure.microsoft.com/pricing/free-trial/).

### <a name="values"></a>Valori di configurazione di esempio per questa esercitazione


Quando si utilizza la procedura seguente come esercizio, è possibile utilizzare i valori di configurazione di esempio:

- **VNet nome:** TestVNet1
- **Spazio di indirizzi:** 10.11.0.0/16 e 10.12.0.0/16
- **Subnet:**
    - Front-end: 10.11.0.0/24
    - Back-end: 10.12.0.0/24
    - GatewaySubnet: 10.12.255.0/27
- **Gruppo risorse:** TestRG1
- **Posizione:** Stati Uniti orientali
- **Al Server DNS:** 8.8.8.8
- **Nome gateway:** VNet1GW
- **IP pubblico:** VNet1GWIP
- **Tipo VPN:** In base la distribuzione
- **Tipo di connessione:** Sito a sito (IPsec)
- **Tipo di gateway:** VPN
- **Nome del Gateway rete locale:** Sito2
- **Nome connessione:** VNet1toSite2


## <a name="CreatVNet"></a>1. creare una rete virtuale 

Se si dispone già di un VNet, verificare che le impostazioni siano compatibili con la struttura del gateway VPN. Prestare particolare attenzione alle eventuali subnet che potrebbero risultare sovrapposti con altre reti. Se si dispone sovrapposti subnet, la connessione non funzionerà correttamente. Se il VNet è configurato con i dati corretti, è possibile iniziare la procedura nella sezione [specificare un server DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Per creare una rete virtuale

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## <a name="subnets"></a>2. aggiungere subnet e spazio aggiuntivo di indirizzi

È possibile aggiungere spazio aggiuntivo di indirizzi e subnet per il VNet dopo averlo creato.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

## <a name="dns"></a>3. specificare un server DNS

### <a name="to-specify-a-dns-server"></a>Per specificare un server DNS

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>4. creare una subnet gateway

Prima di connettersi alla rete aziendale virtuale a un gateway, è innanzitutto necessario creare subnet gateway per la rete virtuale a cui si desidera connettersi. Se possibile, è consigliabile creare una subnet gateway mediante un blocco CIDR di /28 o /27 per fornire indirizzi IP sufficiente per soddisfare requisiti aggiuntivi successive alla configurazione.

Se si sta creando questa configurazione come esercizio, vedere questi [valori](#values) durante la creazione di subnet gateway.

### <a name="to-create-a-gateway-subnet"></a>Per creare una subnet gateway


[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. creare un gateway di rete virtuale

Se si sta creando questa configurazione come esercizio, è possibile fare riferimento ai [valori di configurazione di esempio](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Per creare un gateway di rete virtuale

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>6. creare un gateway di rete locale

Il gateway di rete locale' ' fa riferimento alla posizione locale. Specificare un nome per il quale Azure può fare riferimento a tale per il gateway di rete locale. 

Se si sta creando questa configurazione come esercizio, è possibile fare riferimento ai [valori di configurazione di esempio](#values).

### <a name="to-create-a-local-network-gateway"></a>Per creare un gateway di rete locale

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="VPNDevice"></a>7. configurare il dispositivo VPN

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>8. creare una connessione VPN da sito

Creare la connessione VPN da sito tra il gateway virtuali e il dispositivo VPN. Assicurarsi di sostituire i valori con uno personalizzato. La chiave condivisa deve corrispondere al valore utilizzato per la configurazione di dispositivi VPN. 

Prima di iniziare questa sezione, verificare che il gateway virtuali e gateway di rete locale terminata la creazione. Se si sta creando questa configurazione come esercizio, vedere questi [valori](#values) quando si crea la connessione.

### <a name="to-create-the-vpn-connection"></a>Per creare la connessione VPN


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## <a name="VerifyConnection"></a>9. verificare la connessione VPN

È possibile verificare la connessione VPN nel portale o tramite PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>Passaggi successivi

- Una volta completata la connessione, è possibile aggiungere macchine virtuali alle tue reti virtuale. Vedere le macchine virtuali [percorso formativo](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) per altre informazioni.

- Per informazioni su BGP, vedere la [Panoramica BGP](vpn-gateway-bgp-overview.md) e [su come configurare BGP](vpn-gateway-bgp-resource-manager-ps.md).
