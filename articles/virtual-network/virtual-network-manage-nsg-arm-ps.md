<properties 
   pageTitle="Gestire NSGs tramite PowerShell in Gestione risorse | Microsoft Azure"
   description="Informazioni su come gestire esistente NSGs tramite PowerShell in Gestione risorse"
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
   ms.date="03/14/2016"
   ms.author="jdial" />

# <a name="manage-nsgs-using-powershell"></a>Gestire NSGs tramite PowerShell

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modello di distribuzione classica.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="retrieve-information"></a>Recuperare le informazioni

È possibile visualizzare il NSGs esistente, recuperare le regole per un NSG esistente e scoprire quali risorse è associato a un NSG.

### <a name="view-existing-nsgs"></a>Visualizzare NSGs esistente
Per visualizzare tutti i NSGs esistenti in una sottoscrizione, eseguire la `Get-AzureRmNetworkSecurityGroup` cmdlet come illustrato di seguito.

    Get-AzureRmNetworkSecurityGroup

Risultato previsto:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                         
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
    
    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
                            
    Name                 : WEB1
    ResourceGroupName    : RG101
    Location             : eastus2
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG101/providers/M
                           icrosoft.Network/networkSecurityGroups/WEB1
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]


Per visualizzare l'elenco dei NSGs in un gruppo di risorse specifico, eseguire la `Get-AzureRmNetworkSecurityGroup` cmdlet come illustrato di seguito. 

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG

Output previsto:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                         
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
    
    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
         
### <a name="list-all-rules-for-an-nsg"></a>Elenco di tutte le regole per un NSG

Per visualizzare le regole di un NSG denominato **NSG FrontEnd**, eseguire la `Get-AzureRmNetworkSecurityGroup` cmdlet come illustrato di seguito. 

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules

Output previsto:
    
    Name                     : rdp-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                        Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow RDP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 100
    Direction                : Inbound
    
    Name                     : web-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                        Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow HTTP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 80
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 101
    Direction                : Inbound

>[AZURE.NOTE] È inoltre possibile utilizzare `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` per visualizzare un elenco di regole predefinite da **Front-end NSG** NSG.

### <a name="view-nsgs-associations"></a>Visualizzare le associazioni NSGs

Per visualizzare ciò che risorse NSG **Front-end NSG** è associato a, eseguire la `Get-AzureRmNetworkSecurityGroup` cmdlet come illustrato di seguito.

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd

Cercare le proprietà **NetworkInterfaces** e **subnet** , come illustrato di seguito:

    NetworkInterfaces    : []
    Subnets              : [
                             {
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                               "IpConfigurations": []
                             }
                           ]

Nell'esempio precedente, il NSG non è associato a tutte le interfacce di rete (NIC) ed è associata a una subnet denominata **front-end**.

## <a name="manage-rules"></a>Gestire le regole

È possibile aggiungere regole per un NSG esistente, modificare le regole esistenti e rimuovere le regole.

### <a name="add-a-rule"></a>Aggiungere una regola

Per aggiungere una regola che consenta il traffico **in ingresso** alla porta **443** da qualsiasi computer a **Front-end NSG** NSG, seguire la procedura seguente.

1. Eseguire il `Get-AzureRmNetworkSecurityGroup` cmdlet per recuperare NSG esistente e archiviarlo in una variabile, come illustrato di seguito.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Eseguire il `Add-AzureRmNetworkSecurityRuleConfig` cmdlet, come illustrato di seguito.

        Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange * `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443

3. Per salvare le modifiche apportate al NSG, eseguire la `Set-AzureRmNetworkSecurityGroup` cmdlet come illustrato di seguito.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Output previsto con le regole di protezione:

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "*",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="change-a-rule"></a>Modificare una regola

Per modificare la regola creata in precedenza per consentire il traffico in ingresso da **Internet** solo, seguire la procedura seguente.

1. Eseguire il `Get-AzureRmNetworkSecurityGroup` cmdlet per recuperare NSG esistente e archiviarlo in una variabile, come illustrato di seguito.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Eseguire il `Set-AzureRmNetworkSecurityRuleConfig` cmdlet, come illustrato di seguito.

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange Internet `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443

3. Per salvare le modifiche apportate al NSG, eseguire la `Set-AzureRmNetworkSecurityGroup` cmdlet come illustrato di seguito.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Output previsto con le regole di protezione:

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="delete-a-rule"></a>Eliminare una regola

1. Eseguire il `Get-AzureRmNetworkSecurityGroup` cmdlet per recuperare NSG esistente e archiviarlo in una variabile, come illustrato di seguito.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Eseguire il `Remove-AzureRmNetworkSecurityRuleConfig` cmdlet, come illustrato di seguito.

        Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule

3. Per salvare le modifiche apportate al NSG, eseguire la `Set-AzureRmNetworkSecurityGroup` cmdlet come illustrato di seguito.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Output previsto con solo le regole di protezione, si noti la **regola https** non sia più presente:

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 }
                               ]

## <a name="manage-associations"></a>Gestire le associazioni

È possibile associare un NSG a subnet e schede di rete. È possibile annullare l'associazione anche tra un NSG da una risorsa a che è associata.

### <a name="associate-an-nsg-to-a-nic"></a>Associare un NSG a una scheda

Per associare **Front-end NSG** NSG a **TestNICWeb1** NIC, seguire la procedura seguente.

1. Eseguire il `Get-AzureRmNetworkSecurityGroup` cmdlet per recuperare NSG esistente e archiviarlo in una variabile, come illustrato di seguito.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Eseguire il `Get-AzureRmNetworkInterface` cmdlet per recuperare la scheda di rete esistente e archiviarlo in una variabile, come illustrato di seguito.

        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1

3. Impostare la proprietà **NetworkSecurityGroup** della variabile **NIC** al valore della variabile **NSG** , come illustrato di seguito.

        $nic.NetworkSecurityGroup = $nsg

4. Per salvare le modifiche apportate alla scheda, eseguire la `Set-AzureRmNetworkInterface` cmdlet come illustrato di seguito.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Output previsto che mostra solo la proprietà **NetworkSecurityGroup** :

        NetworkSecurityGroup : {
                                 "SecurityRules": [],
                                 "DefaultSecurityRules": [],
                                 "NetworkInterfaces": [],
                                 "Subnets": [],
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                               }

### <a name="dissociate-an-nsg-from-a-nic"></a>Annullare l'associazione tra un NSG da una scheda di rete

Per annullare l'associazione tra **Front-end NSG** NSG da **TestNICWeb1** NIC, seguire la procedura seguente.

1. Eseguire il `Get-AzureRmNetworkSecurityGroup` cmdlet per recuperare NSG esistente e archiviarlo in una variabile, come illustrato di seguito.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Eseguire il `Get-AzureRmNetworkInterface` cmdlet per recuperare la scheda di rete esistente e archiviarlo in una variabile, come illustrato di seguito.

        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1

3. Impostare la proprietà **NetworkSecurityGroup** della variabile **NIC** su **$null**, come illustrato di seguito.

        $nic.NetworkSecurityGroup = $null

4. Per salvare le modifiche apportate alla scheda, eseguire la `Set-AzureRmNetworkInterface` cmdlet come illustrato di seguito.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Output previsto che mostra solo la proprietà **NetworkSecurityGroup** :

        NetworkSecurityGroup : null

### <a name="dissociate-an-nsg-from-a-subnet"></a>Annullare l'associazione tra un NSG da una subnet

Per annullare l'associazione tra **Front-end NSG** NSG dalla subnet **front-end** , seguire la procedura seguente.

1. Eseguire il `Get-AzureRmVirtualNetwork` cmdlet per recuperare VNet esistente e archiviarlo in una variabile, come illustrato di seguito.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet

2. Eseguire il `Get-AzureRmVirtualNetworkSubnetConfig` cmdlet per recuperare subnet **front-end** e archiviarlo in una variabile, come illustrato di seguito.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 

3. Impostare la proprietà **NetworkSecurityGroup** della variabile **subnet** a **$null**, come illustrato di seguito.

        $subnet.NetworkSecurityGroup = $null

4. Per salvare le modifiche apportate alla subnet, eseguire la `Set-AzureRmVirtualNetwork` cmdlet come illustrato di seguito.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Output previsto che mostra solo le proprietà della subnet **front-end** . Si noti che non è disponibile una proprietà per **NetworkSecurityGroup**:

            ...
            Subnets           : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                      },
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                      }
                                    ],
                                    "ProvisioningState": "Succeeded"
                                  },
                                    ...
                                ]

### <a name="associate-an-nsg-to-a-subnet"></a>Associare un NSG a una subnet

Per associare nuovamente **Front-end NSG** NSG alla subnet **FronEnd** , seguire la procedura seguente.

1. Eseguire il `Get-AzureRmVirtualNetwork` cmdlet per recuperare VNet esistente e archiviarlo in una variabile, come illustrato di seguito.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet

2. Eseguire il `Get-AzureRmVirtualNetworkSubnetConfig` cmdlet per recuperare subnet **front-end** e archiviarlo in una variabile, come illustrato di seguito.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 

1. Eseguire il `Get-AzureRmNetworkSecurityGroup` cmdlet per recuperare NSG esistente e archiviarlo in una variabile, come illustrato di seguito.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

3. Impostare la proprietà **NetworkSecurityGroup** della variabile **subnet** a **$null**, come illustrato di seguito.

        $subnet.NetworkSecurityGroup = $nsg

4. Per salvare le modifiche apportate alla subnet, eseguire la `Set-AzureRmVirtualNetwork` cmdlet come illustrato di seguito.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Output che mostra solo la proprietà **NetworkSecurityGroup** della subnet **FrontEnd** previsto:

        ...
        "NetworkSecurityGroup": {
                                  "SecurityRules": [],
                                  "DefaultSecurityRules": [],
                                  "NetworkInterfaces": [],
                                  "Subnets": [],
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                }
        ...

## <a name="delete-an-nsg"></a>Eliminare un NSG

È possibile eliminare un NSG solo se non è associata a tutte le risorse. Per eliminare un NSG, seguire la procedura seguente.

1. Per controllare le risorse associate a un NSG, eseguire la `azure network nsg show` come mostrato nella [visualizzazione NSGs associazioni](#View-NSGs-associations).
2. Se il NSG è associato a qualsiasi NIC, eseguire la `azure network nic set` come illustrato negli [Dissociate un NSG da una scheda di rete](#Dissociate-an-NSG-from-a-NIC) per ciascuna NIC. 
3. Se il NSG è associato a qualsiasi subnet, eseguire la `azure network vnet subnet set` come illustrato nella [Dissociate un NSG da una subnet](#Dissociate-an-NSG-from-a-subnet) per ogni subnet.
4. Per eliminare il NSG, eseguire la `Remove-AzureRmNetworkSecurityGroup` cmdlet come illustrato di seguito.

        Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force

    >[AZURE.NOTE] Il **-forza** parametro assicura che non è necessario confermare l'eliminazione.

## <a name="next-steps"></a>Passaggi successivi

- [Attivare la registrazione](virtual-network-nsg-manage-log.md) per NSGs.