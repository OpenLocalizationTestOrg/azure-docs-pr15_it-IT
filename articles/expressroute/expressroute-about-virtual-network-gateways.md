<properties 
   pageTitle="Sui gateway virtuali ExpressRoute | Microsoft Azure"
   description="Informazioni su gateway virtuali per ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/03/2016"
   ms.author="cherylmc" />

# <a name="about-virtual-network-gateways-for-expressroute"></a>Sui gateway virtuali per ExpressRoute


Un gateway virtuali viene utilizzato per inviare il traffico di rete tra Azure reti virtuali e posizioni locale. Quando si configura una connessione ExpressRoute, è necessario creare e configurare un gateway virtuali e una connessione di rete virtuale gateway.

Quando si crea un gateway virtuali, specificare le impostazioni diverse. Uno delle impostazioni necessarie specifica se il gateway da utilizzare per il traffico ExpressRoute o VPN da sito. Nel modello di distribuzione di Manager delle risorse, l'impostazione è '-GatewayType'.

Quando il traffico di rete viene inviato in una connessione privata dedicata, utilizzare il tipo di gateway 'ExpressRoute'. Questa operazione si intende anche come gateway ExpressRoute. Quando il traffico di rete viene inviato crittografato in Internet pubblica, utilizzare il tipo di gateway 'Vpn'. Questo processo viene definito un gateway VPN. Sito a sito punto al sito e connessioni VNet-VNet utilizzano un gateway VPN. 

Ogni virtuali possono contenere solo un gateway di rete virtuale per ogni tipo di gateway. Ad esempio, può avere una rete virtuale gateway che usa - GatewayType Vpn e quello che usa - GatewayType ExpressRoute. Questo articolo analizza il gateway di rete virtuale ExpressRoute.

## <a name="gwsku"></a>SKU di gateway

[AZURE.INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Se si desidera aggiornare il gateway a un gateway più potente SKU, nella maggior parte dei casi è possibile utilizzare il cmdlet di PowerShell 'Ridimensionamento AzureRmVirtualNetworkGateway'. Questo funzionerà per gli aggiornamenti a SKU HighPerformance e Standard. Tuttavia, per eseguire l'aggiornamento a SKU UltraPerformance, è necessario ricreare il gateway.

###  <a name="aggthroughput"></a>Stimata velocità aggregazione da gateway SKU


Nella tabella seguente mostra i tipi di gateway e la velocità di aggregata stimata. In questa tabella si applica alle Manager delle risorse e modelli di distribuzione classica.

[AZURE.INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)] 


## <a name="resources"></a>Cmdlet di PowerShell e le API REST

Per ulteriori risorse tecniche e requisiti di sintassi specifico quando si utilizzano le API REST e i cmdlet di PowerShell per le configurazioni gateway virtuali, vedere le pagine seguenti:

|**Classica** | **Manager delle risorse**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle configurazioni di connessione disponibili, vedere [Panoramica di ExpressRoute](expressroute-introduction.md) . 







 
