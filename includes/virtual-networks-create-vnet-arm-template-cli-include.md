## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>Distribuire il modello ARM tramite CLI Azure

Per distribuire il modello ARM scaricato tramite CLI Azure, seguire la procedura seguente.

1. Se non è mai utilizzato CLI Azure, vedere [installare e configurare CLI Azure](../articles/xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si seleziona l'account Azure e la sottoscrizione.
2. Eseguire il **`azure config mode`** comando per passare alla modalità di gestione risorse, come illustrato di seguito.

        azure config mode arm

    Ecco l'output previsto per il comando precedente:

        info:    New mode is arm

3. Se necessario, eseguire la **`azure group create`** per creare un nuovo gruppo di risorse, come illustrato di seguito. Si noti l'output del comando. Elenco visualizzato dopo l'output illustra i parametri utilizzati. Per ulteriori informazioni sui gruppi di risorse, vedere [Panoramica di gestione risorse Azure](../articles/resource-group-overview.md).

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

4. Eseguire il **`azure group deployment create`** cmdlet per distribuire VNet nuovo utilizzando il modello e il parametro file scaricato e modificato in precedenza. Elenco visualizzato dopo l'output illustra i parametri utilizzati.

        azure group deployment create -g TestRG -n TestVNetDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

    Ecco l'output previsto per il comando precedente:

        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "TestVNetDeployment"
        + Registering providers
        info:    Registering provider microsoft.network
        + Waiting for deployment to complete
        data:    DeploymentName     : TestVNetDeployment
        data:    ResourceGroupName  : TestRG
        data:    ProvisioningState  : Succeeded
        data:    Timestamp          : 2015-08-14T21:56:11.152759Z
        data:    Mode               : Incremental
        data:    Name           Type    Value
        data:    -------------  ------  --------------
        data:    location       String  Central US
        data:    vnetName       String  TestVNet
        data:    addressPrefix  String  192.168.0.0/16
        data:    subnet1Prefix  String  192.168.1.0/24
        data:    subnet1Name    String  FrontEnd
        data:    subnet2Prefix  String  192.168.2.0/24
        data:    subnet2Name    String  BackEnd
        info:    group deployment create command OK

    - **-g (o - gruppo di risorse)**. Nome del gruppo di risorse VNet nuovo verrà creato.
    - **-f (o --file di modello)**. Percorso al file di modello ARM.
    - **-e (o --file dei parametri)**. Percorso al file di parametri ARM.

5. Eseguire il **`azure network vnet show`** comando per visualizzare le proprietà di nuovo vnet, come illustrato di seguito.

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
