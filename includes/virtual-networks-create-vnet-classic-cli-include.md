## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Come creare un VNet classica usa CLI Azure

È possibile utilizzare CLI Azure per gestire le risorse Azure dal prompt dei comandi da qualsiasi computer che esegue Windows, Linux o OS x. Per creare un VNet tramite CLI Azure, seguire la procedura seguente.

1. Se non è mai utilizzato CLI Azure, vedere [installare e configurare CLI Azure](../articles/xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si seleziona l'account Azure e la sottoscrizione.
2. Eseguire il comando **Crea rete azure vnet** per creare un VNet e una subnet, come illustrato di seguito. Elenco visualizzato dopo l'output illustra i parametri utilizzati.

            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    
    Output previsto:

            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    - **-vnet**. Nome della VNet da creare. Per questo scenario, *TestVNet*
    - **-e (o - spazio di indirizzi)**. Spazio di indirizzi VNet. Per questo scenario, *192.168.0.0*
    - **-i (o - cidr)**. Maschera di rete in formato CIDR. Per questo scenario, *16*.
    - **- n (o --nome subnet**). Nome della prima subnet. Per questo scenario, *front-end*.
    - **-p (o - subnet start ip)**. Indirizzo IP iniziale per subnet o spazio di indirizzi subnet. Per questo scenario, *192.168.1.0*.
    - **-r (o - subnet cidr)**. Maschera di rete in formato CIDR per subnet. Per questo scenario, *24*.
    - **-l (o - posizione)**. Azure area geografica in cui verrà creato il VNet. Per questo scenario, *Centrale USA*.

3. Eseguire il comando **Crea rete azure vnet subnet** per creare una subnet, come illustrato di seguito. Elenco visualizzato dopo l'output illustra i parametri utilizzati.

            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    
    Ecco l'output previsto per il comando precedente:

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    - **-t (o il nome - vnet**. Nome del VNet in cui verrà creata alla subnet. Per questo scenario, *TestVNet*.
    - **-n (o - nome)**. Nome della nuova subnet. Per questo scenario, *back-end*.
    - **-(o - prefisso indirizzo)**. Blocco CIDR subnet. Quattro il nostro scenario, *192.168.2.0/24*.

4. Eseguire il comando **rete azure vnet Mostra** per visualizzare le proprietà di nuovo vnet, come illustrato di seguito.

            azure network vnet show

    Ecco l'output previsto per il comando precedente:

            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK
