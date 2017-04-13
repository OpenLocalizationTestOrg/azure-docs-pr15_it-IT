<properties
   pageTitle="Aggiunta di un gateway VNet a una rete virtuale per ExpressRoute tramite Gestione risorse e PowerShell | Microsoft Azure"
   description="In questo articolo illustra l'aggiunta di un gateway Vnet a un già creato Manager delle risorse VNet per ExpressRoute"
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
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
   ms.author="charwen"/>

# <a name="configure-a-virtual-network-gateway-for-expressroute-using-resource-manager-and-powershell"></a>Configurare un gateway di rete virtuale per ExpressRoute tramite Gestione risorse e PowerShell


> [AZURE.SELECTOR]
- [PowerShell - Manager delle risorse](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell - classico](expressroute-howto-add-gateway-classic.md)


In questo articolo consentono all'utente la procedura per aggiungere, ridimensionare e rimuovere un gateway di rete virtuale (VNet) per un VNet già esistente. La procedura per questa configurazione sono specificamente per VNets che sono stati creati usando il **modello di distribuzione di Manager delle risorse** e che sarà possibile utilizzare in una configurazione ExpressRoute. 

**Informazioni sui modelli di distribuzione di Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="before-beginning"></a>Prima di iniziare

Verificare che siano installati i cmdlet di PowerShell Azure necessari per questa configurazione (1.0.2 o versioni successive). Se non è stato installato i cmdlet, è necessario farlo prima di iniziare la procedura di configurazione. Per ulteriori informazioni sull'installazione di Azure PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

    
## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il gateway VNet, è possibile collegare le VNet a un circuito ExpressRoute. Fare clic sul [collegamento virtuali a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).
