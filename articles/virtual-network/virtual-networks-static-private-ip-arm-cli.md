<properties 
   pageTitle="Come impostare un indirizzo IP privato statico in modalità ARM utilizzando CLI | Microsoft Azure"
   description="Informazioni sulle IP statico (DIP) e come possono essere gestiti in modalità ARM utilizzando CLI"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
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

# <a name="how-to-set-a-static-private-ip-address-in-azure-cli"></a>Come impostare un indirizzo IP statico privato in CLI Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione di Manager delle risorse. È inoltre possibile [gestire l'indirizzo IP statico privato nel modello di distribuzione classica](virtual-networks-static-private-ip-classic-cli.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

I comandi di Azure CLI di esempio seguenti si aspettano un ambiente semplice già stato creato. Se si desidera eseguire i comandi così come sono visualizzati nel documento, creare innanzitutto l'ambiente di testing descritto in [creazione di un vnet](virtual-networks-create-vnet-arm-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Come specificare un indirizzo IP statico privato durante la creazione di una macchina virtuale
Per creare una macchina virtuale denominata *DNS01* in subnet *front-end* di un VNet denominato *TestVNet* con un indirizzo IP statico privato di *192.168.1.101*, seguire la procedura seguente:

1. Se non è mai utilizzato CLI Azure, vedere [installare e configurare CLI Azure](../xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si seleziona l'account Azure e la sottoscrizione.

2. Eseguire il comando **configurazione azure modalità** per passare alla modalità di gestione risorse, come illustrato di seguito.

        azure config mode arm

    Output previsto:

        info:    New mode is arm

3. Eseguire il **pubblico-ip di rete azure creare** per creare un indirizzo IP pubblico per la macchina virtuale. Elenco visualizzato dopo l'output illustra i parametri utilizzati.

        azure network public-ip create -g TestRG -n TestPIP -l centralus
    
    Output previsto:

        info:    Executing command network public-ip create
        + Looking up the public ip "TestPIP"
        + Creating public ip address "TestPIP"
        + Looking up the public ip "TestPIP"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
        data:    Name                            : TestPIP
        data:    Type                            : Microsoft.Network/publicIPAddresses
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Allocation method               : Dynamic
        data:    Idle timeout                    : 4
        info:    network public-ip create command OK

    - **-g (o - gruppo di risorse)**. Nome del gruppo di risorse in che l'indirizzo IP pubblico verrà creato.
    - **-n (o - nome)**. Nome dell'indirizzo IP pubblico.
    - **-l (o - posizione)**. Azure area geografica in cui verrà creato l'indirizzo IP pubblico. Per questo scenario, *centralus*.

3. Eseguire il comando **Crea rete azure nic** per creare una scheda di rete con un indirizzo IP privato statico. Elenco visualizzato dopo l'output illustra i parametri utilizzati.

        azure network nic create -g TestRG -n TestNIC -l centralus -a 192.168.1.101 -m TestVNet -k FrontEnd

    Output previsto:

        + Looking up the network interface "TestNIC"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC"
        + Looking up the network interface "TestNIC"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
        data:    Name                            : TestNIC
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.101
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK

    - **-(o - indirizzo ip privato)**. Indirizzo IP statico privato per la scheda di rete.
    - **-m (o --nome di vnet subnet)**. Nome del VNet in cui verrà creato la scheda di rete.
    - **-k (o --nome subnet)**. Nome della subnet in cui verrà creato la scheda di rete.

4. Eseguire il comando **azure macchine virtuali create** per creare la macchina virtuale utilizzando l'indirizzo IP pubblico e NIC creato in precedenza. Elenco visualizzato dopo l'output illustra i parametri utilizzati.

        azure vm create -g TestRG -n DNS01 -l centralus -y Windows -f TestNIC -i TestPIP -F TestVNet -j FrontEnd -o vnetstorage -q bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 -u adminuser -p AdminP@ssw0rd

    Output previsto:

        info:    Executing command vm create
        + Looking up the VM "DNS01"
        info:    Using the VM Size "Standard_A1"
        warn:    The image "bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2" will be used for VM
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account vnetstorage
        + Looking up the NIC "TestNIC"
        info:    Found an existing NIC "TestNIC"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "TestNIC"
        info:    Found public ip parameters, trying to setup PublicIP profile
        + Looking up the public ip "TestPIP"
        info:    Found an existing PublicIP "TestPIP"
        info:    Configuring identified NIC IP configuration with PublicIP "TestPIP"
        + Updating NIC "TestNIC"
        + Looking up the NIC "TestNIC"
        + Creating VM "DNS01"
        info:    vm create command OK

    - **-y (o tipo di sistema operativo-)**. Tipo di sistema operativo per la macchina virtuale di *Windows* o *Linux*.
    - **-f (o nic-nome)**. Nome della scheda NIC utilizzerà la macchina virtuale.
    - **-i (o --nome di ip pubblico)**. Nome dell'indirizzo IP pubblico utilizzerà la macchina virtuale.
    - **-F (o - vnet nome)**. Nome del VNet in cui verrà creata la macchina virtuale.
    - **-j (o nomi di subnet-vnet)**. Nome della subnet in cui verrà creata la macchina virtuale.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Come recuperare statico privato indirizzo IP per una macchina virtuale

Per visualizzare le informazioni di indirizzo IP private statiche per macchine Virtuali creati con lo script precedente, eseguire il seguente comando CLI Azure e osservare i valori per *metodo di allocazione IP privati* e *l'indirizzo IP privato*:

    azure vm show -g TestRG -n DNS01

Output previsto:

    info:    Executing command vm show
    + Looking up the VM "DNS01"
    + Looking up the NIC "TestNIC"
    + Looking up the public ip "TestPIP
    data:    Id                              :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    ProvisioningState               :Succeeded
    data:    Name                            :DNS01
    data:    Location                        :centralus
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:
    data:    Storage Profile:
    data:      Source image:
    data:        Id                          :/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/services/images/bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
    data:
    data:      OS Disk:
    data:        OSType                      :Windows
    data:        Name                        :cli08d7bd987a0112a8-os-1441774961355
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :https://vnetstorage2.blob.core.windows.net/vhds/cli08d7bd987a0112a8-os-1441774961355vhd
    data:
    data:    OS Profile:
    data:      Computer Name                 :DNS01
    data:      User Name                     :adminuser
    data:      Windows Configuration:
    data:        Provision VM Agent          :true
    data:        Enable automatic updates    :true
    data:
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    data:          Primary                   :true
    data:          MAC Address               :00-0D-3A-90-1A-A8
    data:          Provisioning State        :Succeeded
    data:          Name                      :TestNIC
    data:          Location                  :centralus
    data:            Private IP alloc-method :Static
    data:            Private IP address      :192.168.1.101
    data:            Public IP address       :40.122.213.159
    info:    vm show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Come rimuovere un indirizzo IP statico privato da una macchina virtuale
È possibile rimuovere un indirizzo IP statico privato da una scheda di rete in Azure CLI per Manager delle risorse. È necessario creare una nuova scheda di rete che utilizza un indirizzo IP dinamico, rimuovere NIC precedente dal macchina virtuale e quindi aggiungere il nuovo pulsante macchina virtuale. Per modificare la scheda di rete per la macchina virtuale utilizzato int eh comandi precedenti, seguire la procedura seguente.
    
1. Eseguire il comando **Crea rete azure nic** per creare un nuovo NIC utilizzando allocazione IP dinamica. Si noti come non è necessario specificare l'indirizzo IP questa volta.

        azure network nic create -g TestRG -n TestNIC2 -l centralus -m TestVNet -k FrontEnd

    Output previsto:

        info:    Executing command network nic create
        + Looking up the network interface "TestNIC2"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC2"
        + Looking up the network interface "TestNIC2"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
        data:    Name                            : TestNIC2
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.6
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK

2. Eseguire il comando **macchine virtuali azure impostare** per modificare la scheda di rete utilizzato da macchina virtuale.

        azure vm set -g TestRG -n DNS01 -N TestNIC2

    Output previsto:

        info:    Executing command vm set
        + Looking up the VM "DNS01"
        + Looking up the NIC "TestNIC2"
        + Updating VM "DNS01"
        info:    vm set command OK

3. Se si desidera, eseguire il comando **di rete azure nic delete** per eliminare la scheda precedente.

        azure network nic delete -g TestRG -n TestNIC --quiet

    Output previsto:

        info:    Executing command network nic delete
        + Looking up the network interface "TestNIC"
        + Deleting network interface "TestNIC"
        info:    network nic delete command OK

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Come aggiungere un indirizzo IP statico privato a una macchina virtuale esistente
Per aggiungere un indirizzo IP statico privato alla scheda utilizzata da macchine Virtuali creati con lo script precedente, eseguire il comando seguente:

    azure network nic set -g TestRG -n TestNIC2 -a 192.168.1.101

Output previsto:

    info:    Executing command network nic set
    + Looking up the network interface "TestNIC2"
    + Updating network interface "TestNIC2"
    + Looking up the network interface "TestNIC2"
    data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
    data:    Name                            : TestNIC2
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : centralus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-90-29-25
    data:    Enable IP forwarding            : false
    data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    IP configurations:
    data:      Name                          : NIC-config
    data:      Provisioning state            : Succeeded
    data:      Private IP address            : 192.168.1.101
    data:      Private IP Allocation Method  : Static
    data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sugli indirizzi [IP pubblico riservato](virtual-networks-reserved-public-ip.md) .
- Informazioni sugli indirizzi [IP pubblico (ILPIP) a livello di istanza](virtual-networks-instance-level-public-ip.md) .
- Consultare le [API REST IP riservato](https://msdn.microsoft.com/library/azure/dn722420.aspx).
