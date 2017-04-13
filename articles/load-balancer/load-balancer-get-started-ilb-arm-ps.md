<properties
   pageTitle="Creare un bilanciamento del carico interno tramite PowerShell in Gestione risorse | Microsoft Azure"
   description="Informazioni su come creare un bilanciamento del carico interno tramite PowerShell in Gestione risorse"
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

# <a name="create-an-internal-load-balancer-using-powershell"></a>Creare un bilanciamento del carico interno tramite PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modello di distribuzione classica](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


La procedura seguente viene illustrato come creare una gestione risorse Azure con PowerShell di bilanciamento del carico interno. Azure Gestione risorse, gli elementi per creare un sistema di bilanciamento del carico interno sono configurati singolarmente e quindi combinano per creare un bilanciamento del carico.

È necessario creare e configurare i seguenti oggetti per distribuire un bilanciamento del carico:

- Primo piano configurazione IP end - consente di configurare l'indirizzo IP privato per il traffico di rete in ingresso
- Pool di indirizzi back-end - configurerà le interfacce di rete che riceveranno il traffico di bilanciamento del carico provenienti da pool IP front-end
- Bilanciamento del carico regole - configurazione porta locale per il servizio di bilanciamento del carico e origine.
- Esegue la ricerca: consente di configurare la ricerca di stato di integrità per le istanze di macchina virtuale.
- In ingresso regole NAT - configurare le regole di porta per accedere direttamente a una delle istanze di macchina virtuale.

È possibile ottenere ulteriori informazioni su componenti di bilanciamento del carico con Gestione risorse Azure [Azure gestione di risorse di supporto per bilanciamento del carico](load-balancer-arm.md).

La procedura seguente viene illustrato come configurare un bilanciamento del carico tra due macchine virtuali.


## <a name="setup-powershell-to-use-resource-manager"></a>PowerShell di configurazione per utilizzare Gestione risorse

Assicurarsi che la versione di produzione più recente del modulo di Azure per PowerShell e di avere il programma di installazione di PowerShell correttamente accedere all'abbonamento Azure.

### <a name="step-1"></a>Passaggio 1

        Login-AzureRmAccount

### <a name="step-2"></a>Passaggio 2

Controllare le sottoscrizioni per l'account

        Get-AzureRmSubscription

È necessario autentica con le credenziali.<BR>

### <a name="step-3"></a>Passaggio 3

Scegliere quale delle sottoscrizioni Azure da utilizzare. <BR>


        Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="create-resource-group-for-load-balancer"></a>Creare il gruppo di risorse per bilanciamento del carico

### <a name="step-4"></a>Passaggio 4

Creare un nuovo gruppo di risorse (ignorare questo passaggio se Usa un gruppo di risorse esistente)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specificano un percorso. Viene utilizzato come posizione predefinita per le risorse in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un bilanciamento del carico utilizzerà stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato "NRP RG" e un percorso "Ovest Contattaci".

## <a name="create-virtual-network-and-a-private-ip-address-for-front-end-ip-pool"></a>Creare virtuali e un indirizzo IP privato per pool IP front-end


### <a name="step-1"></a>Passaggio 1

Crea una subnet per la rete virtuale e l'assegna a variabile $backendSubnet

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

Creare una rete virtuale:

    $vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

Crea rete virtuale e vengono aggiunti alla subnet essere kg-subnet alla rete virtuale NRPVNet e assegna a variabile $vnet



## <a name="create-front-end-ip-pool-and-backend-address-pool"></a>Creare pool di indirizzi IP di Front end e back-end pool di indirizzi

Configurazione di un pool di indirizzi IP front-end per la posta in arrivo carico bilanciamento rete il traffico e back-end pool di indirizzi ricevere il carico bilanciato il traffico.

### <a name="step-1"></a>Passaggio 1

Creare un pool di indirizzi IP front-end usando l'indirizzo IP privato 10.0.2.5 per il 10.0.2.0/24 subnet quale sarà l'endpoint il traffico di rete in arrivo.

    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id

### <a name="step-2"></a>passaggio 2

Configurare un pool di indirizzi back-end utilizzato per ricevere il traffico in ingresso dal pool IP front-end:

    $beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## <a name="create-lb-rules-nat-rules-probe-and-load-balancer"></a>Creare regole kg, le regole NAT, verifica e bilanciamento del carico

Dopo aver creato il pool di indirizzi IP front-end e il pool di indirizzi back-end, sarà necessario creare le regole di cui fa parte la risorsa di bilanciamento del carico:

### <a name="step-1"></a>Passaggio 1

    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

     $lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


L'esempio precedente è creazione gli elementi seguenti:

- Regola NAT che tutto il traffico in ingresso alla porta 3441 verrà indirizzati alla porta 3389.
- seconda regola NAT che tutto il traffico in ingresso alla porta 3442 verrà indirizzati alla porta 3389.
- una regola di bilanciamento carico che verrà caricato saldo tutti traffico sulla porta pubblico 80 alla porta locale 80 nel pool di indirizzi back-end.
- una regola verifica che verificherà lo stato di integrità per percorso "HealthProbe.aspx"



### <a name="step-2"></a>Passaggio 2

Creare il bilanciamento del carico sommare tutti gli oggetti (regole NAT, le regole di bilanciamento carico, configurazioni di ricerca):

    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe


## <a name="create-network-interfaces"></a>Creare le interfacce di rete

Dopo aver creato il servizio di bilanciamento del carico interno, è necessario definire le interfacce di rete riceverà il traffico di rete di bilanciamento del carico in arrivo, le regole NAT e verifica. Interfaccia di rete in questo caso è configurato singolarmente e può essere assegnata a una macchina virtuale in un secondo momento.


### <a name="step-1"></a>Passaggio 1


Ottenere la rete virtuale delle risorse e subnet per creare le interfacce di rete:

    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet


In questo passaggio consente di creare un'interfaccia di rete che appartengono al pool di carico bilanciamento back-end e associare la prima regola NAT per RDP per l'interfaccia di rete:

    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### <a name="step-2"></a>Passaggio 2

Creare una seconda interfaccia di rete chiamata kg-Nic2 essere:

Questa procedura viene creata una seconda interfaccia di rete, l'assegnazione allo stesso pool di back-end di bilanciamento carico e associare la seconda regola NAT creati per RDP:

     $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

Il risultato finale mostrerà le operazioni seguenti:

    $backendnic1

Output previsto:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
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
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3"></a>Passaggio 3

Usare il comando Aggiungi AzureRmVMNetworkInterface per assegnare la scheda a una macchina virtuale.

È possibile trovare istruzioni dettagliate per creare una macchina virtuale e assegnare a una scheda seguendo la documentazione: [creare una macchina virtuale Azure tramite PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md).

Se si dispone già di una macchina virtuale creata, è possibile aggiungere l'interfaccia di rete con la procedura seguente:

#### <a name="step-1"></a>Passaggio 1

Caricare la risorsa di bilanciamento del carico in una variabile (se si è già fatto che ancora). La variabile utilizzata è denominata $lb e utilizzare gli stessi nomi dalla risorsa di bilanciamento del carico creato in precedenza.

    $lb= Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG

#### <a name="step-2"></a>Passaggio 2

Caricare la configurazione di back-end a una variabile.

    $backend= Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### <a name="step-3"></a>Passaggio 3

Caricare l'interfaccia di rete già creato in una variabile. il nome della variabile utilizzato è $ NIC. Il nome dell'interfaccia di rete utilizzato è lo stesso dell'esempio precedente.

    $nic=Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG

#### <a name="step-4"></a>Passaggio 4

Modificare la configurazione di back-end nell'interfaccia di rete.

    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### <a name="step-5"></a>Passaggio 5

Salvare l'oggetto dell'interfaccia di rete.

    Set-AzureRmNetworkInterface -NetworkInterface $nic

Dopo l'aggiunta di un'interfaccia di rete al pool di back-end bilanciamento di carico, viene avviato riceve il traffico di rete in base alle regole per la risorsa di bilanciamento del carico di bilanciamento del carico.

## <a name="update-an-existing-load-balancer"></a>Aggiornare un servizio di bilanciamento del carico esistente


### <a name="step-1"></a>Passaggio 1

Usa il bilanciamento del carico dell'esempio precedente, assegnare carico bilanciamento oggetto alla variabile $slb utilizzando Get-AzureRmLoadBalancer

    $slb=get-azureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### <a name="step-2"></a>Passaggio 2

Nell'esempio seguente si aggiungerà una nuova regola NAT in ingresso utilizzando porta 8181 e 81 in front-end per il pool di back-end a un servizio di bilanciamento del carico esistente

    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### <a name="step-3"></a>Passaggio 3

Salvare la nuova configurazione utilizzando Set AzureLoadBalancer

    $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>Rimuovere un bilanciamento del carico

Usare il comando AzureRmLoadBalancer Rimuovi per eliminare un bilanciamento del carico creato in precedenza denominata "NRP kg" in un gruppo di risorse denominato "NRP RG"

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] È possibile utilizzare facoltativa passare - forza per evitare la richiesta per l'eliminazione.



## <a name="next-steps"></a>Passaggi successivi

[Configurare una modalità di distribuzione carico di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni di timeout di inattività TCP per il servizio di bilanciamento del carico](load-balancer-tcp-idle-timeout.md)