<properties
   pageTitle="Creare un servizio di bilanciamento del carico esposto a Internet in Gestione risorse tramite PowerShell | Microsoft Azure"
   description="Informazioni su come creare un servizio di bilanciamento del carico esposto a Internet in Gestione risorse tramite PowerShell"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
  ms.service="load-balancer"
  ms.devlang="na"
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
   ms.date="10/24/2016"
  ms.author="sewhee" />

# <a name="get-started"></a>Creazione di un servizio di bilanciamento del carico esposto a Internet in Gestione risorse tramite PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione di Manager delle risorse. È inoltre possibile [imparare a creare un bilanciamento del carico esposto a Internet utilizzando il modello di distribuzione classica](load-balancer-get-started-internet-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>Distribuire la soluzione tramite PowerShell Azure

Le procedure seguenti illustrano come creare un servizio di bilanciamento del carico esposto a Internet tramite Gestione risorse di Azure con PowerShell. Azure Gestione risorse di ogni risorsa viene creato e configurato singolarmente e quindi si inserisce per creare un bilanciamento del carico.

È necessario creare e configurare i seguenti oggetti per distribuire un bilanciamento del carico:

- Configurazione IP front-end: contiene pubblico indirizzi IP (PIP) per il traffico di rete in ingresso.
- Pool di indirizzi di back-end: contiene le interfacce di rete (NIC) per le macchine virtuali di ricevere il traffico di rete di bilanciamento del carico.
- Regole di bilanciamento del carico: contiene le regole di cui eseguire il mapping di una porta pubblica del servizio di bilanciamento del carico a una porta nel pool di indirizzi di back-end.
- In ingresso regole NAT: contiene le regole di cui eseguire il mapping di una porta pubblica del servizio di bilanciamento del carico a una porta per una macchina virtuale specifica nel pool di indirizzi di back-end.
- Ricerca: contiene le ricerche integrità utilizzate per verificare la disponibilità delle istanze di macchina virtuale nel pool di indirizzi di back-end.

Per ulteriori informazioni, vedere [Gestione di Azure risorse di supporto per bilanciamento del carico](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurare PowerShell usare Gestione risorse

Accertarsi di avere l'ultima versione di produzione del modulo di gestione di risorse Azure per PowerShell:

1. Accedere a Azure.

        Login-AzureRmAccount

    Immettere le credenziali quando richiesto.

2. Controllare le sottoscrizioni per l'account.

        Get-AzureRmSubscription

3. Scegliere quale delle sottoscrizioni Azure da utilizzare.

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

4. Creare un gruppo di risorse. (Ignorare questo passaggio se si usa un gruppo di risorse esistenti.)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Creare una rete virtuale e un indirizzo IP pubblico per il pool front-end IP

1. Creare una subnet e una rete virtuale.

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

2. Creare una Azure pubblica risorsa indirizzo IP, denominata **PublicIP**che verrà utilizzato da un pool front-end IP con il nome DNS **loadbalancernrp.westus.cloudapp.azure.com**. Il comando seguente viene utilizzato il tipo di allocazione statica.

        $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' –AllocationMethod Static -DomainNameLabel loadbalancernrp

    >[AZURE.IMPORTANT]Bilanciamento del carico utilizza l'etichetta di dominio dell'indirizzo IP pubblico come prefisso per il nome di dominio completo. Questa è la differenza tra il modello di distribuzione classico, che utilizza il servizio cloud come il nome di dominio completo di bilanciamento del carico.
    >In questo esempio il FQDN è **loadbalancernrp.westus.cloudapp.azure.com**.

## <a name="create-a-front-end-ip-pool-and-a-back-end-address-pool"></a>Creare un pool di indirizzi IP front-end e un pool di indirizzi di back-end

1. Creare un pool front-end IP denominato **Kg Frontend** che utilizza la risorsa **PublicIp** .

        $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP

2. Creare un pool di indirizzi di back-end denominato **back-end kg**.

        $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>Creare regole NAT, una regola di bilanciamento del carico, una ricerca e un bilanciamento del carico

In questo esempio viene creato gli elementi seguenti:

- Una regola NAT per tradurre tutto il traffico in ingresso sulla porta 3441 alla porta 3389
- Una regola NAT per tradurre tutto il traffico in ingresso sulla porta 3442 alla porta 3389
- Una regola di verifica per controllare lo stato di integrità di una pagina denominata **HealthProbe.aspx**
- Una regola di bilanciamento carico riconciliare tutto il traffico in ingresso sulla porta 80 alla porta 80 sugli indirizzi nel pool di back-end
- Un bilanciamento del carico che utilizza tutti gli oggetti

Utilizzare la procedura seguente:

1. Creare le regole NAT.

        $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

        $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

2. Creare una verifica dell'integrità. Esistono due modi per configurare un sondaggio:

    Verifica HTTP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    Verifica TCP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2

3. Creare una regola di bilanciamento del carico.

        $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

4. Creare il servizio di bilanciamento del carico usando gli oggetti creati in precedenza.

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe

## <a name="create-nics"></a>Creare schede di rete

Creare le interfacce di rete (o modificare quelli esistenti) e quindi associarli regole NAT, le regole di bilanciamento carico e le ricerche:

1. È possibile ottenere la rete virtuale e una subnet virtuali, in cui è necessario creare le schede di rete.

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet

2. Creare una scheda di rete denominato **essere kg-nic1**e associare con la prima regola NAT e il pool di indirizzi di back-end prima (e solo).

        $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

3. Creare una scheda di rete denominato **essere kg-nic2**e associare alla seconda regola NAT e il pool di indirizzi di back-end prima (e solo).

        $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

4. Selezionare le schede di rete.

        $backendnic1

    Output previsto:

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
                            "PublicIpAddress": {
                                "Id": null
                            },
                            "LoadBalancerBackendAddressPools": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                                }
                            ],
                            "LoadBalancerInboundNatRules": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                                }
                            ],
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :

5. Utilizzare la `Add-AzureRmVMNetworkInterface` cmdlet per assegnare il NIC a macchine virtuali diverse.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Per ulteriori informazioni sulla creazione di una macchina virtuale e l'assegnazione di una scheda di rete, vedere [creare una macchina virtuale Azure tramite PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md).

## <a name="add-the-network-interface-to-the-load-balancer"></a>Aggiungere l'interfaccia di rete per il servizio di bilanciamento del carico

1. Recuperare il servizio di bilanciamento del carico comuni.

    Caricare la risorsa di bilanciamento del carico in una variabile (se si è già fatto che ancora). La variabile è denominata **$lb**. Utilizzare gli stessi nomi dalla risorsa di bilanciamento del carico creato in precedenza.

        $lb= get-azurermloadbalancer –name NRP-LB -resourcegroupname NRP-RG

2. Caricare la configurazione di back-end a una variabile.

        $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

3. Caricare l'interfaccia di rete già creato in una variabile. Il nome della variabile è **$nic**. Il nome dell'interfaccia di rete è identico a quello dell'esempio precedente.

        $nic =get-azurermnetworkinterface –name lb-nic1-be -resourcegroupname NRP-RG

4. Modificare la configurazione di back-end nell'interfaccia di rete.

        $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

5. Salvare l'oggetto dell'interfaccia di rete.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Dopo l'aggiunta di un'interfaccia di rete al pool di back-end bilanciamento di carico, viene avviato riceve il traffico di rete in base alle regole di bilanciamento del carico per tale risorsa di bilanciamento del carico.

## <a name="update-an-existing-load-balancer"></a>Aggiornare un servizio di bilanciamento del carico esistente

1. Utilizzando il bilanciamento del carico dell'esempio precedente, assegnare un oggetto di bilanciamento del carico variabile **$slb** utilizzando `Get-AzureLoadBalancer`.

        $slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

2. Nell'esempio seguente, aggiungere una regola in entrata NAT - tramite porta 8181 e 81 nel pool front-end per il pool di back-end, ossia a un servizio di bilanciamento del carico esistente.

        $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP

3. Salvare la nuova configurazione utilizzando `Set-AzureLoadBalancer`.

        $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>Rimuovere un bilanciamento del carico

Usare il comando `Remove-AzureLoadBalancer` per eliminare un bilanciamento del carico creato in precedenza denominata **NRP kg** in un gruppo di risorse denominato **NRP RG**.

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] È possibile utilizzare il parametro facoltativo **-forza** per evitare la richiesta per l'eliminazione.

## <a name="next-steps"></a>Passaggi successivi

[Per iniziare la configurazione di un servizio di bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurare una modalità di distribuzione carico di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni di timeout di inattività TCP per il servizio di bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
