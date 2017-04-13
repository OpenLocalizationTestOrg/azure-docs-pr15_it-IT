## <a name="how-to-create-a-vnet-using-the-azure-cli"></a>Come creare un VNet usa CLI Azure

È possibile utilizzare CLI Azure per gestire le risorse Azure dal prompt dei comandi da qualsiasi computer che esegue Windows, Linux o OS x. Per creare un VNet tramite CLI Azure, seguire la procedura seguente.

1. Se non è mai utilizzato CLI Azure, vedere [installare e configurare CLI Azure](../articles/xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si seleziona l'account Azure e la sottoscrizione.
2. Eseguire il comando **configurazione azure modalità** per passare alla modalità di gestione risorse, come illustrato di seguito.

        azure config mode arm

    Ecco l'output previsto per il comando precedente:

        info:    New mode is arm

3. Se necessario, eseguire il **gruppo azure crea** per creare un nuovo gruppo di risorse, come illustrato di seguito. Si noti l'output del comando. Elenco visualizzato dopo l'output illustra i parametri utilizzati. Per ulteriori informazioni sui gruppi di risorse, vedere [Panoramica di gestione risorse Azure](../articles/virtual-network/resource-group-overview.md#resource-groups).

        azure group create -n TestRG -l centralus

    Ecco l'output previsto per il comando precedente:

        info:    Executing command group create
        + Getting resource group TestRG
        + Creating resource group TestRG
        info:    Created resource group TestRG
        data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            centralus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

    - **-n (o - nome)**. Nome per il nuovo gruppo di risorse. Per questo scenario, *TestRG*.
    - **-l (o - posizione)**. Azure area geografica in cui verrà creato il nuovo gruppo di risorse. Per questo scenario, *centralus*.

4. Eseguire il comando **Crea rete azure vnet** per creare un VNet e una subnet, come illustrato di seguito. 

        azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

    Ecco l'output previsto per il comando precedente:

        info:    Executing command network vnet create
        + Looking up virtual network "TestVNet"
        + Creating virtual network "TestVNet"
        + Loading virtual network state
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        info:    network vnet create command OK

    - **-g (o - gruppo di risorse)**. Nome del gruppo di risorse in cui verrà creato il VNet. Per questo scenario, *TestRG*.
    - **-n (o - nome)**. Nome della VNet da creare. Per questo scenario, *TestVNet*
    - **-(o - prefissi degli indirizzi)**. Elenco di blocchi CIDR utilizzato per lo spazio di indirizzi VNet. Per questo scenario, *192.168.0.0/16*
    - **-l (o - posizione)**. Azure area geografica in cui verrà creato il VNet. Per questo scenario, *centralus*.

5. Eseguire il comando **Crea rete azure vnet subnet** per creare una subnet, come illustrato di seguito. Si noti l'output del comando. Elenco visualizzato dopo l'output illustra i parametri utilizzati.

        azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

    Ecco l'output previsto per il comando precedente:

        info:    Executing command network vnet subnet create
        + Looking up the subnet "FrontEnd"
        + Creating subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:
        info:    network vnet subnet create command OK

    - **-e (o il nome - vnet**. Nome del VNet in cui verrà creata alla subnet. Per questo scenario, *TestVNet*.
    - **-n (o - nome)**. Nome della nuova subnet. Per questo scenario, *front-end*.
    - **-(o - prefisso indirizzo)**. Blocco CIDR subnet. Quattro il nostro scenario, *192.168.1.0/24*.

6. Ripetere il passaggio 5 per creare altre subnet, se necessario. Per questo scenario, eseguire il comando seguente per creare subnet *back-end* .

        azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. Eseguire il comando **rete azure vnet Mostra** per visualizzare le proprietà di nuovo vnet, come illustrato di seguito.

        azure network vnet show -g TestRG -n TestVNet

    Ecco l'output previsto per il comando precedente:

        info:    Executing command network vnet show
        + Looking up virtual network "TestVNet"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        data:    Subnets:
        data:      Name                          : FrontEnd
        data:      Address prefix                : 192.168.1.0/24
        data:
        data:      Name                          : BackEnd
        data:      Address prefix                : 192.168.2.0/24
        data:
        info:    network vnet show command OK
