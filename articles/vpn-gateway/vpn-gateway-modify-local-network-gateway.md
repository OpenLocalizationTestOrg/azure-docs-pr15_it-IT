<properties
   pageTitle="Modificare i prefissi di indirizzi IP di rete locale gateway e indirizzi IP di gateway | Microsoft Azure"
   description="In questo articolo illustra le modifica prefissi di indirizzi IP per il gateway di rete locale"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="cherylmc"/>

# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modificare le impostazioni del gateway di rete locale tramite PowerShell

Può succedere che le impostazioni per la modifica di AddressPrefix o GatewayIPAddress gateway rete locale. Le istruzioni riportate di seguito consente di modificare le impostazioni di gateway di rete locale. È anche possibile modificare queste impostazioni nel portale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare
    
È necessario installare l'ultima versione dei cmdlet di Manager delle risorse PowerShell Azure. Per ulteriori informazioni sull'installazione i cmdlet di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) .

## <a name="to-modify-ip-address-prefixes"></a>Per modificare l'indirizzo IP prefissi

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address"></a>Per modificare l'indirizzo IP del gateway

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Passaggi successivi

È possibile verificare la connessione di gateway. Vedere [verificare una connessione di gateway](vpn-gateway-verify-connection-resource-manager.md).

