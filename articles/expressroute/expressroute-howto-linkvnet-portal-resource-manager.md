<properties
   pageTitle="Creare un collegamento a un circuito ExpressRoute a una rete virtuale utilizzando il modello di distribuzione di Manager delle risorse e il portale di Azure | Microsoft Azure"
   description="In questo documento offre una panoramica su come collegare reti virtuali (VNets) ExpressRoute circuiti."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc" />

# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Creare un collegamento a un circuito ExpressRoute una rete virtuale

> [AZURE.SELECTOR]
- [Portale di Azure - Gestione risorse](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Manager delle risorse](expressroute-howto-linkvnet-arm.md)
- [PowerShell - classico](expressroute-howto-linkvnet-classic.md)



Questo articolo viene spiegato come collegare reti virtuali (VNets) circuiti Azure ExpressRoute utilizzando il modello di distribuzione di Manager delle risorse e il portale di Azure. Reti virtuali possono essere nello stesso abbonamento o possono essere parte di un'altra sottoscrizione.


**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Prerequisiti di configurazione

- Assicurarsi che siano state esaminate i [Prerequisiti](expressroute-prerequisites.md), [routing requisiti](expressroute-routing.md)e [flussi di lavoro](expressroute-workflows.md) prima di iniziare la configurazione.
- È necessario avere un circuito ExpressRoute attivo.
    - Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e disporre circuito abilitato per il proprio provider di integrazione applicativa.

    - Assicurarsi di avere Azure privato peering configurato per il circuito. Vedere l'articolo [Configura il routing](expressroute-howto-routing-portal-resource-manager.md) per instradare le istruzioni.

    - Assicurarsi che peering privato Azure è configurato e il peering BGP tra la rete e Microsoft è in modo che è possibile abilitare la connettività-to-end.

    - Verificare di disporre di una rete virtuale e un gateway di rete virtuale creata e completamente il provisioning. Seguire le istruzioni per creare un [gateway VPN](../articles/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) (Segui solo i passaggi 1-5).

È possibile collegare fino a 10 reti virtuali a un circuito ExpressRoute standard. Tutte le reti virtuale devono essere nella stessa regione geopolitiche quando si utilizza un circuito ExpressRoute standard. È possibile collegare un reti virtuali all'esterno dell'area geopolitiche di circuito ExpressRoute o connettersi un numero maggiore di reti virtuali il circuito ExpressRoute se è stato attivato il componente aggiuntivo premium ExpressRoute. Controllare le [domande frequenti](expressroute-faqs.md) per altre informazioni sul componente aggiuntivo premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Connettere una rete virtuale nello stesso abbonamento a un circuito


### <a name="to-create-a-connection"></a>Per creare una connessione

1. Assicurarsi che il circuito ExpressRoute e peering privato Azure sono stati configurati correttamente. Seguire le istruzioni fornite in [creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e [Configura il routing](expressroute-howto-routing-arm.md). Il circuito ExpressRoute dovrebbe essere simile nella figura seguente.

    ![ExpressRoute circuito schermata](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)

    >[AZURE.NOTE] Informazioni sulla configurazione di BGP non verrà visualizzata se il provider di livello 3 configurato il peerings. Se il circuito è stato eseguito il provisioning, dovrebbe essere possibile creare connessioni.

2. È ora possibile avviare il provisioning di una connessione per collegare il gateway di rete virtuale per il circuito ExpressRoute. Fare clic su **connessione** > **Aggiungi** per aprire e il **componente connessione** e quindi configurare i valori. Vedere l'esempio di riferimento seguenti.


    ![Aggiungere la schermata connessione](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  


3. Dopo la connessione è stata completata, l'oggetto di connessione verrà visualizzate le informazioni per la connessione.

    ![Schermata di oggetto di connessione](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


### <a name="to-delete-a-connection"></a>Per eliminare una connessione

È possibile eliminare una connessione, fare clic sull'icona **Elimina** sulla e per la connessione.

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Connettere una rete virtuale in una sottoscrizione diversa a un circuito

Al momento non è possibile connettersi reti virtuali attraverso le sottoscrizioni tramite il portale di Azure. Tuttavia, è possibile utilizzare PowerShell per eseguire questa operazione. Vedere l'articolo di [PowerShell](expressroute-howto-linkvnet-arm.md) per altre informazioni.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su ExpressRoute, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md).
