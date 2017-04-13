<properties 
   pageTitle="Controllare il routing e utilizzare i dispositivi di rete con PowerShell nel modello di distribuzione classica | Microsoft Azure"
   description="Informazioni su come controllare il routing VNets tramite PowerShell nel modello di distribuzione classica"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Controllare il routing e utilizzare i dispositivi di rete (classico) tramite PowerShell

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione classica.

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Nell'esempio PowerShell Azure comandi seguenti richiedono un ambiente semplice già stato creato in base allo scenario precedente. Se si desidera eseguire i comandi così come sono visualizzati nel documento, creare l'ambiente illustrato in [creare un VNet (classica) tramite PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Creare UDR per subnet front-end
Per creare la tabella e la distribuzione necessari per la subnet front-end in base allo scenario sopra, seguire la procedura seguente.

3. Eseguire il **`New-AzureRouteTable`** cmdlet per creare una tabella di route per subnet front-end.

        New-AzureRouteTable -Name UDR-FrontEnd `
            -Location uswest `
            -Label "Route table for front end subnet"

    Output:

        Name         Location   Label                          
        ----         --------   -----                          
        UDR-FrontEnd West US    Route table for front end subnet

4. Eseguire il **`Set-AzureRoute`** cmdlet per creare una route della tabella creata in precedenza per inviare tutto il traffico destinato alla subnet back-end (192.168.2.0/24) per **FW1** macchine Virtuali (192.168.0.4).
    
        Get-AzureRouteTable UDR-FrontEnd `
            |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

    Output:

        Name     : UDR-FrontEnd
        Location : West US
        Label    : Route table for frontend subnet
        Routes   : 
                   Name                 Address Prefix    Next hop type        Next hop IP address
                   ----                 --------------    -------------        -------------------
                   RouteToBackEnd       192.168.2.0/24    VirtualAppliance     192.168.0.4  

5. Eseguire il **`Set-AzureSubnetRouteTable`** cmdlet per associare la tabella route creato in precedenza con la subnet **front-end** .

        Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
            -SubnetName FrontEnd `
            -RouteTableName UDR-FrontEnd
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Creare UDR per subnet back-end
Per creare la tabella e la distribuzione necessari per la subnet di back-end in base allo scenario sopra, seguire la procedura seguente.

3. Eseguire il **`New-AzureRouteTable`** cmdlet per creare una tabella di route per subnet back-end.

        New-AzureRouteTable -Name UDR-BackEnd `
            -Location uswest `
            -Label "Route table for back end subnet"

4. Eseguire il **`Set-AzureRoute`** cmdlet per creare una route della tabella creata in precedenza per inviare tutto il traffico destinato alla subnet front-end (192.168.1.0/24) per **FW1** macchine Virtuali (192.168.0.4).

        Get-AzureRouteTable UDR-BackEnd `
            |Set-AzureRoute -RouteName RouteToFrontEnd -AddressPrefix 192.168.1.0/24 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

5. Eseguire il **`Set-AzureSubnetRouteTable`** cmdlet per associare la tabella route creato in precedenza con la subnet **back-end** .

        Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
            -SubnetName BackEnd `
            -RouteTableName UDR-BackEnd

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Attivare l'inoltro IP nella macchina virtuale FW1
Per abilitare l'inoltro nella macchina virtuale FW1 IP, seguire la procedura seguente.

1. Eseguire il **`Get-AzureIPForwarding`** cmdlet per controlla lo stato di inoltro IP

        Get-AzureVM -Name FW1 -ServiceName TestRGFW `
            | Get-AzureIPForwarding

    Output:

        Disabled

2. Eseguire il **`Set-AzureIPForwarding`** comando per attivare l'inoltro IP per *FW1* macchine Virtuali.

        Get-AzureVM -Name FW1 -ServiceName TestRGFW `
            | Set-AzureIPForwarding -Enable
