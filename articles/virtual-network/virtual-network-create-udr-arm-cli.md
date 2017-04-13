<properties 
   pageTitle="Controllare il routing e utilizzare i dispositivi di rete in Gestione risorse utilizzando CLI Azure | Microsoft Azure"
   description="Informazioni su come controllare il routing e utilizzare i dispositivi di rete utilizzando CLI Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

#<a name="create-user-defined-routes-udr-in-the-azure-cli"></a>Creare route (UDR) definite dall'utente in CLI Azure

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione di Manager delle risorse. È anche possibile [creare UDRs nel modello di distribuzione classica](virtual-network-create-udr-classic-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

I comandi di Azure CLI di esempio seguenti si aspettano un ambiente semplice già creato in base allo scenario precedente. Se si desidera eseguire i comandi così come sono visualizzati nel documento, prima di tutto creare l'ambiente di testing distribuendo [questo modello](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), fare clic su **Distribuisci in Azure**, sostituire i valori di parametro predefiniti, se necessario e seguire le istruzioni nel portale.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Creare UDR per subnet front-end
Per creare la tabella e la distribuzione necessari per la subnet front-end in base allo scenario sopra, seguire la procedura seguente.

3. Eseguire il **`azure network route-table create`** comando per creare una tabella di route per subnet front-end.

        azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest

    Output:

        info:    Executing command network route-table create
        info:    Looking up route table "UDR-FrontEnd"
        info:    Creating route table "UDR-FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    Name                            : UDR-FrontEnd
        data:    Type                            : Microsoft.Network/routeTables
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        info:    network route-table create command OK

    Parametri:
    - **-g (o - gruppo di risorse)**. Nome del gruppo di risorse in cui verrà creato il UDR. Per questo scenario, *TestRG*.
    - **-l (o - posizione)**. Azure area geografica in cui verrà creato il nuovo UDR. Per questo scenario, *westus*.
    - **-n (o - nome)**. Nome per il nuovo UDR. Per questo scenario, *Front-end UDR*.

4. Eseguire il **`azure network route-table route create`** comando per creare una route della tabella creata in precedenza per inviare tutto il traffico destinato alla subnet back-end (192.168.2.0/24) per **FW1** macchine Virtuali (192.168.0.4).

        azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4

    Output:

        info:    Executing command network route-table route create
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd/routes/RouteToBackEnd
        data:    Name                            : RouteToBackEnd
        data:    Provisioning state              : Succeeded
        data:    Next hop type                   : VirtualAppliance
        data:    Next hop IP address             : 192.168.0.4
        data:    Address prefix                  : 192.168.2.0/24
        info:    network route-table route create command OK

    Parametri:
    - **-r (o --nome di tabella route)**. Nome della tabella route nel punto in cui verrà aggiunta la route. Per questo scenario, *Front-end UDR*.
    - **-(o - prefisso indirizzo)**. Prefisso dell'indirizzo per la subnet in cui i pacchetti sono indirizzati a. Per questo scenario, *192.168.2.0/24*.
    - **-y (o - tipo di hop Avanti)**. Tipo di oggetto traffico verrà inviato a. Valori possibili sono *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*o *Nessuno*.
    - **-p (o --indirizzo ip di hop Avanti**). Indirizzo IP del passaggio successivo. Per questo scenario, *192.168.0.4*.

5. Eseguire il **`azure network vnet subnet set`** comando per associare la tabella route creata in precedenza con la subnet **front-end** .

        azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd

    Output:

        info:    Executing command network vnet subnet set
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        info:    Setting subnet "FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    Route Table                     : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    IP configurations:
        data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
        igurations/ipconfig1
        data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
        igurations/ipconfig1
        data:    
        info:    network vnet subnet set command OK

    Parametri:
    - **-e (o - vnet nome)**. Nome del VNet alla subnet in cui si trova. Per questo scenario, *TestVNet*.
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Creare UDR per subnet back-end
Per creare la tabella e la distribuzione necessari per la subnet di back-end in base allo scenario sopra, seguire la procedura seguente.

1. Eseguire il **`azure network route-table create`** comando per creare una tabella di route per subnet back-end.

        azure network route-table create -g TestRG -n UDR-BackEnd -l westus

4. Eseguire il **`azure network route-table route create`** comando per creare una route della tabella creata in precedenza per inviare tutto il traffico destinato alla subnet front-end (192.168.1.0/24) per **FW1** macchine Virtuali (192.168.0.4).

        azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4

5. Eseguire il **`azure network vnet subnet set`** comando per associare la tabella route creata in precedenza con la subnet **back-end** .

        azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd

## <a name="enable-ip-forwarding-on-fw1"></a>Attivare l'inoltro IP in FW1
Per abilitare l'inoltro NIC utilizzato da **FW1**IP, seguire la procedura seguente.

1. Eseguire il **`azure network nic show`** comando e osservare il valore per **abilitare IP inoltro**. È necessario impostare su *false*.

        azure network nic show -g TestRG -n NICFW1

    Output:
        
        info:    Executing command network nic show
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : false
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic show command OK

2. Eseguire il **`azure network nic set`** comando per attivare l'inoltro IP.

        azure network nic set -g TestRG -n NICFW1 -f true

    Output:

        info:    Executing command network nic set
        info:    Looking up the network interface "NICFW1"
        info:    Updating network interface "NICFW1"
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : true
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic set command OK

    Parametri:

    - **-f (o - Abilita ip inoltro)**. *true* o *false*.
