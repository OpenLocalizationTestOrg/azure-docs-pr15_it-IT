<properties
   pageTitle="Dallo ExpressRoute circuiti classica di Manager delle risorse | Microsoft Azure"
   description="Questa pagina offre una panoramica di cosa è necessario conoscere bridging classica e i modelli di distribuzione di Manager delle risorse."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>

# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Dallo ExpressRoute circuiti classica il modello di distribuzione di Manager delle risorse

In questo articolo viene fornita una panoramica di significato spostare un circuito Azure ExpressRoute da classica al modello di distribuzione di Manager delle risorse di Azure.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-classic-rm-include.md)]

È possibile utilizzare un unico circuito ExpressRoute per connettersi a reti virtuali distribuiti in visualizzazione classica e i modelli di distribuzione di Manager delle risorse. Un circuito ExpressRoute, indipendentemente da come è stata creata a questo punto è possibile collegare a reti virtuali su entrambi i modelli di distribuzione.

![Un circuito ExpressRoute collegata a reti virtuali su entrambi i modelli di distribuzione](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>Circuiti ExpressRoute che vengono creati nel modello di distribuzione classica

Circuiti ExpressRoute che vengono creati nel modello di distribuzione classica devono essere spostati nel modello di distribuzione di Manager delle risorse prima di tutto per abilitare la connettività per la visualizzazione classica e i modelli di distribuzione di Manager delle risorse. Non è presente la perdita di connettività o interruzione quando viene spostata una connessione. Tutti i collegamenti di rete circuito a virtuale nel modello di distribuzione classico (all'interno della stessa sottoscrizione e cross-subscription) vengono mantenuti.

Al termine dello spostamento, circuito ExpressRoute è simile, esegue e aspetto esattamente un circuito ExpressRoute creata nel modello di distribuzione Manager delle risorse. È ora possibile creare connessioni a reti virtuali nel modello di distribuzione Manager delle risorse.

Dopo un ExpressRoute circuito è stata spostata nel modello di distribuzione di Manager delle risorse, è possibile gestire il ciclo di vita del circuito ExpressRoute solo utilizzando il modello di distribuzione di Manager delle risorse. Questo significa che è possibile eseguire operazioni come aggiunta, aggiornamento/eliminazione peerings aggiornamento proprietà circuito (ad esempio la larghezza di banda SKU e tipo di fatturazione) e l'eliminazione circuiti solo nel modello di distribuzione di Manager delle risorse. Fare riferimento alla sezione sotto circuiti che vengono creati nel modello di distribuzione Manager delle risorse per ulteriori informazioni su come è possibile gestire l'accesso a entrambi i modelli di distribuzione.

Non è necessario includono il provider di integrazione applicativa per eseguire la migrazione.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Circuiti ExpressRoute che vengono creati nel modello di distribuzione Manager delle risorse

È possibile abilitare circuiti ExpressRoute che vengono creati nel modello di distribuzione Manager delle risorse per essere accessibili da entrambi i modelli di distribuzione. È possibile attivare qualsiasi circuito ExpressRoute nell'abbonamento a cui accedere da entrambi i modelli di distribuzione.

- Circuiti ExpressRoute che sono stati creati nel modello di distribuzione Manager delle risorse non dispone dell'accesso al modello di distribuzione classico per impostazione predefinita.
- Circuiti ExpressRoute che sono stati spostati dal modello di distribuzione classico per il modello di distribuzione di manager delle risorse sono accessibili per entrambi i modelli per impostazione predefinita.
- Un circuito ExpressRoute sempre abbia accesso al modello di distribuzione di Manager delle risorse, indipendentemente dal fatto che siano state create in Gestione risorse o al modello di distribuzione classica. Questo significa che è possibile creare connessioni a reti virtuali create nel modello di distribuzione Manager delle risorse da seguire le istruzioni su [come creare un collegamento reti virtuali](expressroute-howto-linkvnet-arm.md).
- Accesso al modello di distribuzione classica dipende dal parametro **allowClassicOperations** circuito ExpressRoute.

>[AZURE.IMPORTANT] Applicano tutte le quote descritte nella pagina [limiti di servizio](../azure-subscription-service-limits.md) . Se ad esempio un circuito standard può avere al massimo 10 virtuale collegamenti/connessioni di rete tra la visualizzazione classica e i modelli di distribuzione di Manager delle risorse.


## <a name="controlling-access-to-the-classic-deployment-model"></a>Controllare l'accesso al modello di distribuzione classica

È possibile abilitare un singolo circuito ExpressRoute creare un collegamento a reti virtuali in entrambi i modelli di distribuzione impostando il parametro **allowClassicOperations** del circuito ExpressRoute.

Impostazione **allowClassicOperations** consente di creare un collegamento reti virtuali da per entrambi i modelli di circuito ExpressRoute. È possibile collegare a reti virtuali nel modello di distribuzione classica da seguire indicazioni su [come creare un collegamento reti virtuali nel modello di distribuzione classica](expressroute-howto-linkvnet-classic.md). È possibile collegare a reti virtuali nel modello di distribuzione Manager delle risorse da seguire indicazioni su [come creare un collegamento reti virtuali nel modello di distribuzione Manager delle risorse](expressroute-howto-linkvnet-arm.md).

Impostazione **allowClassicOperations** FALSE blocca l'accesso al circuito dal modello di distribuzione classica. Tuttavia, tutti i collegamenti virtuali nel modello di distribuzione classica vengono mantenuti. In questo caso, il circuito ExpressRoute non è visibile nel modello di distribuzione classica.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Operazioni supportate nel modello di distribuzione classica

Le seguenti operazioni classiche sono supportate su un circuito ExpressRoute se **allowClassicOperations** è impostato su TRUE:

 - Ottenere informazioni circuito ExpressRoute
 - Collegamenti virtuali creazione/aggiornamento/get/eliminazione a reti virtuali classiche
 - Autorizzazioni di collegamento Crea/Aggiorna/get/Elimina virtuali connettività per la sottoscrizione

È possibile eseguire le seguenti operazioni classiche quando **allowClassicOperations** è impostato su TRUE:

 - Creare/aggiornamento/get/Elimina bordo Gateway Protocol (BGP) peerings per Azure pubblico privato, Azure e peerings Microsoft
 - Eliminare circuiti ExpressRoute

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Comunicazioni tra la visualizzazione classica e i modelli di distribuzione di Manager delle risorse

Circuito ExpressRoute si comporta come un collegamento tra la visualizzazione classica e i modelli di distribuzione di Manager delle risorse. Traffico tra macchine virtuali di reti virtuali nel modello di distribuzione classica e quelli disponibili nelle reti virtuali per i flussi di modello Manager delle risorse distribuzione tramite ExpressRoute se entrambe le reti virtuali sono collegate allo stesso circuito di ExpressRoute.

Velocità di trasmissione aggregata è limitato dalla capacità effettiva del gateway virtuali. Il traffico non immettere le reti o reti del provider del servizio di integrazione applicativa in tal caso. Flusso di traffico tra le reti virtuali è completamente contenuto all'interno della rete Microsoft.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Accesso al pubblico Azure e risorse peering Microsoft

È possibile continuare ad accedere alle risorse in genere accessibile tramite peering pubblico Azure e Microsoft peering senza interruzioni.  

## <a name="whats-supported"></a>Che cos'è supportata

Questa sezione descrive sulle caratteristiche supportate per circuiti ExpressRoute:

 - È possibile utilizzare un unico circuito ExpressRoute per accedere a reti virtuali distribuiti in visualizzazione classica e i modelli di distribuzione di Manager delle risorse.
 - È possibile spostare un circuito ExpressRoute dalla visualizzazione classica nel modello di distribuzione di Manager delle risorse. Dopo lo spostamento circuito ExpressRoute è simile, ritiene e si comporta come qualsiasi altro circuito ExpressRoute creata nel modello di distribuzione Manager delle risorse.
 - È possibile spostare solo il circuito ExpressRoute. Impossibile spostare circuito collegamenti, reti virtuali e gateway VPN tramite questa operazione.
 - Dopo un ExpressRoute circuito è stata spostata nel modello di distribuzione di Manager delle risorse, è possibile gestire il ciclo di vita del circuito ExpressRoute solo utilizzando il modello di distribuzione di Manager delle risorse. Questo significa che è possibile eseguire operazioni come aggiunta, aggiornamento/eliminazione peerings aggiornamento proprietà circuito (ad esempio la larghezza di banda SKU e tipo di fatturazione) e l'eliminazione circuiti solo nel modello di distribuzione di Manager delle risorse.
 - Circuito ExpressRoute si comporta come un collegamento tra la visualizzazione classica e i modelli di distribuzione di Manager delle risorse. Traffico tra macchine virtuali di reti virtuali nel modello di distribuzione classica e quelli disponibili nelle reti virtuali per i flussi di modello Manager delle risorse distribuzione tramite ExpressRoute se entrambe le reti virtuali sono collegate allo stesso circuito di ExpressRoute.
 - Connettività per la sottoscrizione è supportata in visualizzazione classica e i modelli di distribuzione di Manager delle risorse.

## <a name="whats-not-supported"></a>Operazioni non supportate

Questa sezione descrive cosa non è supportata per circuiti ExpressRoute:

 - Dallo circuito collegamenti, gateway e reti virtuali classica il modello di distribuzione di Manager delle risorse.
 - Gestire il ciclo di vita di un circuito ExpressRoute dal modello di distribuzione classica.
 - Supporto basato sui ruoli di controllo di accesso (RBAC) per il modello di distribuzione classica. È possibile eseguire un circuito dei controlli RBAC nel modello di distribuzione classica. Qualsiasi coadministrator o un amministratore della sottoscrizione è possibile collegare o scollegare reti virtuali al circuito.

## <a name="configuration"></a>Configurazione

Seguire le istruzioni sono descritti nel [spostare un circuito ExpressRoute da classico per il modello di distribuzione di Manager delle risorse](expressroute-howto-move-arm.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni flusso di lavoro, vedere [il provisioning di circuito ExpressRoute stati circuito e flussi di lavoro](expressroute-workflows.md).
- Per configurare la connessione ExpressRoute:

    - [Creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md)
    - [Configurare la distribuzione](expressroute-howto-routing-arm.md)
    - [Creare un collegamento a un circuito ExpressRoute una rete virtuale](expressroute-howto-linkvnet-arm.md)
