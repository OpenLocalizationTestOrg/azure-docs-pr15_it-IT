<properties 
   pageTitle="Controllare il routing e utilizzare i dispositivi di rete utilizzando CLI Azure nel modello di distribuzione classica | Microsoft Azure"
   description="Informazioni su come controllare il routing VNets utilizzando CLI Azure nel modello di distribuzione classica"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

#<a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Controllare il routing e utilizzare i dispositivi di rete (classico) usa CLI Azure

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione classica. È anche possibile [controllare il routing e utilizzare i dispositivi di rete nel modello di distribuzione Manager delle risorse](virtual-network-create-udr-arm-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

I comandi di Azure CLI di esempio seguenti si aspettano un ambiente semplice già creato in base allo scenario precedente. Se si desidera eseguire i comandi così come sono visualizzati nel documento, creare l'ambiente illustrato in [creare un VNet usa CLI Azure (classica)](virtual-networks-create-vnet-classic-cli.md).

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Creare UDR per subnet front-end
Per creare la tabella e la distribuzione necessari per la subnet front-end in base allo scenario sopra, seguire la procedura seguente.

1. Eseguire il **`azure config mode`** per passare alla modalità classica.

        azure config mode asm

    Output:

        info:    New mode is asm

3. Eseguire il **`azure network route-table create`** comando per creare una tabella di route per subnet front-end.

        azure network route-table create -n UDR-FrontEnd -l uswest

    Output:

        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK

    Parametri:
    - **-l (o - posizione)**. Azure area geografica in cui verrà creato il nuovo NSG. Per questo scenario, *westus*.
    - **-n (o - nome)**. Nome per il nuovo NSG. Per questo scenario, *Front-end NSG*.

4. Eseguire il **`azure network route-table route set`** comando per creare una route della tabella creata in precedenza per inviare tutto il traffico destinato alla subnet back-end (192.168.2.0/24) per **FW1** macchine Virtuali (192.168.0.4).

        azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4

    Output:

        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK

    Parametri:
    - **-r (o --nome di tabella route)**. Nome della tabella route nel punto in cui verrà aggiunta la route. Per questo scenario, *Front-end UDR*.
    - **-(o - prefisso indirizzo)**. Prefisso dell'indirizzo per la subnet in cui i pacchetti sono indirizzati a. Per questo scenario, *192.168.2.0/24*.
    - **-t (o - tipo di hop Avanti)**. Tipo di oggetto traffico verrà inviato a. Valori possibili sono *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*o *Nessuno*.
    - **-p (o --indirizzo ip di hop Avanti**). Indirizzo IP del passaggio successivo. Per questo scenario, *192.168.0.4*.

5. Eseguire il **`azure network vnet subnet route-table add`** comando per associare la tabella route creata in precedenza con la subnet **front-end** .

        azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd

    Output:

        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK 

    Parametri:
    - **-t (o - vnet nome)**. Nome del VNet alla subnet in cui si trova. Per questo scenario, *TestVNet*.
    - **- n (o --nome subnet**. Nome della tabella di route subnet verrà aggiunto al. Per questo scenario, *front-end*.
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Creare UDR per subnet back-end
Per creare la tabella e la distribuzione necessari per la subnet di back-end in base allo scenario sopra, seguire la procedura seguente.

3. Eseguire il **`azure network route-table create`** comando per creare una tabella di route per subnet back-end.

        azure network route-table create -n UDR-BackEnd -l uswest

4. Eseguire il **`azure network route-table route set`** comando per creare una route della tabella creata in precedenza per inviare tutto il traffico destinato alla subnet front-end (192.168.1.0/24) per **FW1** macchine Virtuali (192.168.0.4).

        azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4

5. Eseguire il **`azure network vnet subnet route-table add`** comando per associare la tabella route creata in precedenza con la subnet **back-end** .

        azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd

