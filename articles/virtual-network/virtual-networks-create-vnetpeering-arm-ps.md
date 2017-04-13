<properties
   pageTitle="Creare VNet Peering uso dei cmdlet di Powershell | Microsoft Azure"
   description="Informazioni su come creare una rete virtuale tramite il portale di Azure in Gestione risorse."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai; annahar"/>

# <a name="create-vnet-peering-using-powershell-cmdlets"></a>Creare VNet Peering uso dei cmdlet di Powershell

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Per creare un VNet peering tramite PowerShell, seguire la procedura seguente:

1. Se non è mai utilizzato Azure PowerShell, vedere [come installare e configurare PowerShell Azure](../powershell-install-configure.md) e seguire le istruzioni fino alla fine per eseguire l'accesso a Azure e selezionare l'abbonamento.

> [AZURE.NOTE] Cmdlet di PowerShell per la gestione dei VNet peering viene fornito con [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. Leggere gli oggetti virtuali:

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
        $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2

3. Per stabilire VNet peering, è necessario creare due collegamenti, uno per ogni direzione. La seguente operazione creerà un collegamento di peering VNet per VNet1 a VNet2 prima di tutto:

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id

    Mostra output:

        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Initiated
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

4. Questo passaggio creerà un collegamento di peering VNet per VNet2 a VNet1:

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id

    Mostra output:

        Name            : LinkToVNet1
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet2
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

5. Dopo aver creato il collegamento di peering VNet, è possibile visualizzare lo stato dei collegamenti seguente:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2

    Mostra output:

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Esistono alcune proprietà configurabili per VNet peering:

  	|Opzione|Descrizione|Impostazione predefinita|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|Spazio di VNet Peer devono essere inclusi come parte del Tag Virtual_network se indirizzo|Sì|
  	|AllowForwardedTraffic|Se il traffico non provenienti da un VNet peered viene accettato o eliminato|No|
  	|AllowGatewayTransit|Consente al peer VNet verrà utilizzato il gateway VNet|No|
  	|UseRemoteGateways|Usare gateway VNet del peer. Peer VNet deve avere configurato un gateway e AllowGatewayTransit selezionato. È possibile utilizzare questa opzione se si è configurato un gateway|No|

    Ogni collegamento VNet peering è l'insieme di proprietà indicate. Ad esempio, è possibile impostare AllowVirtualNetworkAccess su True per il collegamento di peering VNet VNet1 per VNet2 e impostarla su False per il collegamento di peering VNet in altra direzione.

        $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name LinkToVNet2
        $LinktoVNet2.AllowForwardedTraffic = $true
        Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2

    È possibile eseguire Get-AzureRmVirtualNetworkPeering doppie controllare il valore della proprietà dopo la modifica. Dall'output, è possibile vedere che allowforwardedtraffic Modifica set vero dopo aver eseguito il cmdlet precedente.

        Name            : LinkToVNet2
        Id          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic       : True
        AllowGatewayTransit     : False
        UseRemoteGateways       : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

    Dopo aver peering stabilito in questo scenario, dovrebbe essere possibile avviare le connessioni da qualsiasi computer virtuale a qualsiasi macchina virtuale di entrambi VNets. Per impostazione predefinita, VNet peering verrà provisioning degli ACL appropriate per consentire le comunicazioni tra VNets AllowVirtualNetworkAccess è vero. È comunque possibile applicare regole di gruppo (NSG) di sicurezza di rete per bloccare la connettività tra subnet specifiche o macchine virtuali di assumere il controllo di accesso tra due reti virtuale singole.  Per ulteriori informazioni sulla creazione di regole NSG, fare riferimento al presente [articolo](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Per creare VNet peering tra abbonamenti con PowerShell, seguire la procedura seguente:

1. Accedere a Azure con privilegi un utente dell'account per la sottoscrizione A ed eseguire il seguente cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5

    Non si tratta di un requisito peering è possibile stabilire anche se gli utenti singolarmente generano peering richieste per i rispettivi VNets come le richieste corrispondono. Aggiunta di un utente con privilegi di altri VNet come utente VNet locale, è facile eseguire l'installazione.

2. Accedere a Azure con privilegi-dell'utente B account per abbonamento B ed eseguire il seguente cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3

3. Nell'oggetto utente 's sessione di accesso, eseguita il cmdlet seguente:

        $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess

4. Nella sessione di accesso utente-B, eseguire il cmdlet seguente:

        $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess

5. Dopo avere stabilito peering, qualsiasi macchina virtuale nel VNet3 dovrebbe essere possibile comunicare con qualsiasi macchina virtuale nel VNet5.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. In questo scenario, è possibile eseguire i cmdlet di PowerShell riportato di seguito per stabilire il VNet peering.  È necessario impostare la proprietà AllowForwardedTraffic su True e collegare VNET1 HubVNet, che consente il traffico in ingresso dall'esterno lo spazio di indirizzi VNet peering.

        $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

        Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id

2. Dopo avere stabilito peering, è possibile fare riferimento al presente [articolo](virtual-network-create-udr-arm-ps.md) e definire una route definite dall'utente (UDR) per reindirizzare il traffico VNet1 tramite un dispositivo virtuale per usare le funzionalità. Quando si specifica l'indirizzo hop successivo nella route, è possibile impostare l'indirizzo IP del dispositivo virtuale in peer VNet HubVNet. Di seguito è riportato un esempio:

        $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

        $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Per creare un VNet peering tra una rete virtuale classica e un Manager delle risorse Azure virtuali PowerShell, seguire la procedura seguente:

1. Oggetto virtuali per **VNET1**, la rete virtuale Manager delle risorse Azure analogo al seguente:

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

2. Per stabilire VNet peering in questo scenario, è necessario solo un collegamento, in particolare in un collegamento a **VNET1** **VNET2**. Questo passaggio è necessario conoscere l'ID delle risorse. del VNet classica Formato ID del gruppo di risorse è simile alla:

        /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}

    Assicurarsi di sostituire SubscriptionID, ResourceGroupName e VirtualNetworkName con i nomi appropriati.

    Questo risultato può essere ottenuto dal seguente:

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2

3. Una volta VNet peering collegamento viene creato, è possibile visualizzare lo stato del collegamento come illustrato nell'output riportata di seguito:

        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-vnet-peering"></a>Rimuovere VNet Peering

1.  Per rimuovere VNet peering, è necessario eseguire il cmdlet seguente:

        Remove-AzureRmVirtualNetworkPeering  

        Remove both links, using the following commands:

        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2

2. Quando si rimuove un collegamento in un VNET peering, lo stato del collegamento peer andrà alla disconnesso. In questo stato è non è possibile ricreare il collegamento fino a quando non viene modificato lo stato di collegamento peer a segnalazione. È consigliabile che rimuovere entrambi i collegamenti prima di ricreare la VNet peering.
