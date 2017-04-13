<properties
    pageTitle="Creazione di set di scala macchina virtuale uso dei cmdlet di PowerShell | Microsoft Azure"
    description="Iniziare a creare e gestire i set di scala macchina virtuale Azure prima uso dei cmdlet di PowerShell di Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="danielsollondon"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="danielsollondon"/>

# <a name="creating-virtual-machine-scale-sets-using-powershell-cmdlets"></a>Creazione di set di scala macchina virtuale uso dei cmdlet di PowerShell

Questo è un esempio di come creare un Set(VMSS) scala macchina virtuale, viene creato un VMSS dei 3 nodi, con tutte le reti e lo spazio di archiviazione associato.

## <a name="first-steps"></a>Primi passi
Assicurarsi di avere installato il modulo di Azure PowerShell più recente, che conterrà i cmdlet PowerShell necessari per gestire e creare VMSS.
Accedere agli strumenti di riga di comando [di seguito](http://aka.ms/webpi-azps) per l'ultima versione disponibile Azure i moduli.

Per trovare VMSS correlati cmdlet, utilizzare la stringa di ricerca \*VMSS\*.

## <a name="creating-a-vmss"></a>Creazione di un VMSS

##### <a name="create-resource-group"></a>Creare gruppo di risorse

```
$loc = 'westus';
$rgname = 'mynewrgwu';
  New-AzureRmResourceGroup -Name $rgname -Location $loc -Force;
```

##### <a name="create-storage-account"></a>Creare Account di archiviazione

Impostare il tipo di account di archiviazione / nome.

```
$stoname = 'sto' + $rgname;
$stotype = 'Standard_LRS';
 New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $stoname -Location $loc -SkuName $stotype -Kind "Storage";

$stoaccount = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $stoname;
```

#### <a name="create-networking-vnet--subnet"></a>Creare Networking (VNET / Subnet)

##### <a name="subnet-specification"></a>Specifica della subnet

```
$subnetName = 'websubnet'
  $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix "10.0.0.0/24";
```

##### <a name="vnet-specification"></a>VNET specifica

```
$vnet = New-AzureRmVirtualNetwork -Force -Name ('vnet' + $rgname) -ResourceGroupName $rgname -Location $loc -AddressPrefix "10.0.0.0/16" -Subnet $subnet;
$vnet = Get-AzureRmVirtualNetwork -Name ('vnet' + $rgname) -ResourceGroupName $rgname;

#In this case below we assume the new subnet is the only one, note difference if you have one already or have adjusted this code to more than one subnet.
$subnetId = $vnet.Subnets[0].Id;
```

##### <a name="create-public-ip-resource-to-allow-external-access"></a>Creare risorsa IP pubblico per consentire l'accesso esterno

Questo sarà associato al per il servizio di bilanciamento del carico.

```
$pubip = New-AzureRmPublicIpAddress -Force -Name ('pubip' + $rgname) -ResourceGroupName $rgname -Location $loc -AllocationMethod Dynamic -DomainNameLabel ('pubip' + $rgname);
$pubip = Get-AzureRmPublicIpAddress -Name ('pubip' + $rgname) -ResourceGroupName $rgname;
```

##### <a name="create-and-configure-load-balancer"></a>Creare e configurare il servizio di bilanciamento del carico

```
$frontendName = 'fe' + $rgname
$backendAddressPoolName = 'bepool' + $rgname
$probeName = 'vmssprobe' + $rgname
$inboundNatPoolName = 'innatpool' + $rgname
$lbruleName = 'lbrule' + $rgname
$lbName = 'vmsslb' + $rgname

#Bind Public IP to Load Balancer
$frontend = New-AzureRmLoadBalancerFrontendIpConfig -Name $frontendName -PublicIpAddress $pubip
```

##### <a name="configure-load-balancer"></a>Configurazione di bilanciamento del carico
Crea back-end indirizzo Pool di configurazione, questo verrà condiviso da NIC di macchine virtuali in VMSS.

```
$backendAddressPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name $backendAddressPoolName
```

Impostare condizioni di carico non bilanciate verifica porta, modificare le impostazioni in base alle esigenze dell'applicazione.

```
$probe = New-AzureRmLoadBalancerProbeConfig -Name $probeName -RequestPath healthcheck.aspx -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
```

Creare regole NAT per la connessione di rete indirizzato diretta (non carico bilanciato) alle macchine virtuali VMSS tramite bilanciamento caricare, nota seguente viene illustrato come utilizzare RDP, solo per dimostrazione e metodi VNET interni dovrebbero essere usati per RDP'ing per questi server.

```
$frontendpoolrangestart = 3360
$frontendpoolrangeend = 3370
$backendvmport = 3389
$inboundNatPool = New-AzureRmLoadBalancerInboundNatPoolConfig -Name $inboundNatPoolName -FrontendIPConfigurationId `
$frontend.Id -Protocol Tcp -FrontendPortRangeStart $frontendpoolrangestart -FrontendPortRangeEnd $frontendpoolrangeend -BackendPort $backendvmport;
```

Creare la caricare bilanciato regola, in questo esempio mostra carica bilanciamento del carico porte 80 richieste, usare le impostazioni nei passaggi precedenti.

```
$protocol = 'Tcp'
$feLBPort = 80
$beLBPort = 80

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name $lbruleName `
-FrontendIPConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -Protocol $protocol -FrontendPort $feLBPort -BackendPort $beLBPort `
-IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -Verbose;
```

Creazione di bilanciamento del carico con la configurazione.

```
$actualLb = New-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname -Location $loc `
-FrontendIpConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -LoadBalancingRule $lbrule -InboundNatPool $inboundNatPool -Verbose;
```

Verifica impostazioni kg, controllare il bilanciamento del carico porta configurazioni, nota, non verranno visualizzate le regole in entrata NAT fino a quando non vengono create le macchine Virtuali nel VMSS.

```
$expectedLb = Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname
```

##### <a name="configure-and-create-vmss"></a>Configurare e creare VMSS

Si noti che in questo esempio infrastruttura Mostra come distribuire di installazione e ridurre il traffico web attraverso il VMSS, ma le immagini di macchine virtuali qui specificato non dispone di uno o più servizi web installati.

```
#specify VMSS Name
$vmssName = 'vmss' + $rgname;

##specify VMSS specific details
$adminUsername = 'azadmin';
$adminPassword = "Password1234!";

$PublisherName = 'MicrosoftWindowsServer'
$Offer         = 'WindowsServer'
$Sku          = '2012-R2-Datacenter'
$Version       = 'latest'
$vmNamePrefix = 'winvmss'

$vhdContainer = "https://" + $stoname + ".blob.core.windows.net/" + $vmssName;

###add an extension
$extname = 'BGInfo';
$publisher = 'Microsoft.Compute';
$exttype = 'BGInfo';
$extver = '2.1';
```

Associare NIC di bilanciamento del carico e Subnet

```
$ipCfg = New-AzureRmVmssIPConfig -Name 'nic' `
-LoadBalancerInboundNatPoolsId $actualLb.InboundNatPools[0].Id `
-LoadBalancerBackendAddressPoolsId $actualLb.BackendAddressPools[0].Id `
-SubnetId $subnetId;
```

Crea VMSS configurazione

```
#Specify number of nodes
$numberofnodes = 3

$vmss = New-AzureRmVmssConfig -Location $loc -SkuCapacity $numberofnodes -SkuName 'Standard_A2' -UpgradePolicyMode 'automatic' `
  	| Add-AzureRmVmssNetworkInterfaceConfiguration -Name $subnetName -Primary $true -IPConfiguration $ipCfg `
  	| Set-AzureRmVmssOSProfile -ComputerNamePrefix $vmNamePrefix -AdminUsername $adminUsername -AdminPassword $adminPassword `
  	| Set-AzureRmVmssStorageProfile -Name 'test' -OsDiskCreateOption 'FromImage' -OsDiskCaching 'None' `
    -ImageReferenceOffer $Offer -ImageReferenceSku $Sku -ImageReferenceVersion $Version `
    -ImageReferencePublisher $PublisherName -VhdContainer $vhdContainer `
  	| Add-AzureRmVmssExtension -Name $extname -Publisher $publisher -Type $exttype -TypeHandlerVersion $extver -AutoUpgradeMinorVersion $true
```

Creare Config VMSS

```
New-AzureRmVmss -ResourceGroupName $rgname -Name $vmssName -VirtualMachineScaleSet $vmss -Verbose;
```

Dopo aver creato il VMSS. È possibile verificare la connessione a singoli macchina virtuale utilizzando RDP in questo esempio:

```
VM0 : pubipmynewrgwu.westus.cloudapp.azure.com:3360
VM1 : pubipmynewrgwu.westus.cloudapp.azure.com:3361
VM2 : pubipmynewrgwu.westus.cloudapp.azure.com:3362
```
