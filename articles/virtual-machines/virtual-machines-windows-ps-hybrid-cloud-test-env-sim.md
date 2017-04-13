<properties 
    pageTitle="Ambiente di testing ibrido simulata cloud | Microsoft Azure" 
    description="Creare un ambiente ibrido simulata cloud IT pro o il test di sviluppo, con due reti virtuali Azure e una connessione VNet a VNet." 
    services="virtual-machines-windows" 
    documentationCenter="" 
    authors="JoeDavies-MSFT" 
    manager="timlt" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="josephd"/>

# <a name="set-up-a-simulated-hybrid-cloud-environment-for-testing"></a>Configurare un ambiente ibrido simulata di cloud per il testing

In questo articolo si passaggi per la creazione di un ambiente ibrido simulata di cloud con Microsoft Azure usando due reti virtuale Azure. Ecco la configurazione risultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)

Questa simula un ambiente ibrido di produzione cloud ed è costituita da:

- Rete locale simulata e semplificata inserito in una rete virtuale Azure (la rete virtuale esercitazione).
- Una rete virtuale locale tra simulata ospitata in Azure (TestVNET).
- Connessione VNet a VNet tra le due reti virtuale.
- Controller di dominio secondario nella rete virtuale TestVNET.

Fornisce che una base e avvio comuni dei punti di cui è possibile:

- Sviluppare e testare applicazioni in un ambiente ibrido simulata di cloud.
- Creare configurazioni di test del computer, alcune all'interno della rete virtuale esercitazione e alcune all'interno della rete virtuale TestVNET, per simulare ibrido basato sul cloud IT carichi di lavoro.

Esistono quattro fasi principali per configurare l'ambiente di test ibrido cloud:

1.  Configurare la rete virtuale esercitazione.
2.  Creare la rete virtuale tra locale.
3.  Creare una connessione VPN VNet a VNet.
4.  Configurare DC2. 

Questa configurazione richiede una sottoscrizione Azure. Se si ha un abbonamento MSDN o Visual Studio, vedere [Azure mensile carta di credito per gli abbonati a Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Macchine virtuali e i gateway virtuali Azure comportano un costo monetario in corso quando vengono eseguite. Il costo è effettuato con il sito Web MSDN o pagato abbonamento. Un gateway VPN Azure viene implementato come un insieme di due macchine virtuali di Azure. Per ridurre al minimo i costi, creare l'ambiente di testing ed eseguire il test necessari e dimostrazione presto.

## <a name="phase-1-configure-the-testlab-virtual-network"></a>Fase 1: Configurare la rete virtuale esercitazione

Utilizzare le istruzioni nella sezione [configurazione di Base ambiente di prova](https://technet.microsoft.com/library/mt771177.aspx) per configurare i computer DC1, APP1 e CLIENT1 nella rete virtuale Azure denominata esercitazione. 

Successivamente, avviare un prompt dei comandi PowerShell di Azure.

> [AZURE.NOTE] Il comando seguente imposta usare PowerShell Azure 1.0 e versioni successive.

Accedere al proprio account.

    Login-AzureRMAccount

È possibile ottenere il nome dell'abbonamento tramite il comando seguente.

    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Impostare l'abbonamento Azure. Usare lo stesso abbonamento è stato utilizzato per creare la configurazione di base nella fase 1. Sostituire tutti gli elementi all'interno di offerte, incluse le < e > caratteri, con il nome corretto.

    $subscr="<subscription name>"
    Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Aggiungere una subnet gateway per l'esercitazione virtuali della configurazione di base, che verrà utilizzata per ospitare il gateway Azure.

    $rgName="<name of your resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $vnet=Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name TestLab
    Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 10.255.255.248/29 -VirtualNetwork $vnet
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

Successivamente, richiedere un indirizzo IP pubblico da assegnare al gateway per la rete virtuale esercitazione.

    $gwpip=New-AzureRmPublicIpAddress -Name TestLab_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

Creare il gateway.

    $vnet=Get-AzureRmVirtualNetwork -Name TestLab -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name TestLab_GWConfig -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 
    New-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Tenere presente che nuovo gateway può impiegare più di 20 minuti per creare.

Dal portale di Azure nel computer locale, connettersi a DC1 con le credenziali CORP\User1. Per configurare il dominio CORP in modo che i computer e i relativi utenti utilizzano controller di dominio locale per l'autenticazione, eseguire questi comandi da un prompt dei comandi di Windows PowerShell amministrativo su DC1.

    New-ADReplicationSite -Name "TestLab" 
    New-ADReplicationSite -Name "TestVNET"
    New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
    New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

Si tratta della configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph1.png)
 
## <a name="phase-2-create-the-testvnet-virtual-network"></a>Fase 2: Creare la rete virtuale TestVNET

Prima di tutto, creare la rete virtuale TestVNET e proteggere con un gruppo di sicurezza di rete.

    $rgName="<name of the resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $locShortName="<Azure location name from $locName in all lowercase letters with spaces removed. Example:  westus>"
    $testSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix 192.168.0.0/24
    $gatewaySubnet=New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 192.168.255.248/29
    New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix 192.168.0.0/16 -Subnet $testSubnet,$gatewaySubnet –DNSServer 10.0.0.4
    $rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
    New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
    $vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestVNET
    $nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
    Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet" -AddressPrefix 192.168.0.0/24 -NetworkSecurityGroup $nsg

Successivamente, richiedere un indirizzo IP pubblico allocare al gateway per la rete virtuale TestVNET e creare il gateway.

    $gwpip=New-AzureRmPublicIpAddress -Name TestVNET_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $vnet=Get-AzureRmVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name "TestVNET_GWConfig" -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
    New-AzureRmVirtualNetworkGateway -Name "TestVNET_GW" -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Si tratta della configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph2.png)
 
##<a name="phase-3-create-the-vnet-to-vnet-connection"></a>Fase 3: Creare la connessione VNet-VNet

Prima di tutto, ottenere una chiave già condivisa casuale, crittografia avanzata, 32 caratteri dall'amministratore di rete o protezione. In alternativa, è possibile utilizzare le informazioni in [Crea una stringa casuale per una chiave già condivisa IPsec](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) per ottenere una chiave già condivisa.

Successivamente, utilizzare questi comandi per creare la connessione VPN VNet-VNet, che può richiedere del tempo per completare.

    $sharedKey="<pre-shared key value>"
    $gwTestLab=Get-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName
    $gwTestVNET=Get-AzureRmVirtualNetworkGateway -Name TestVNET_GW -ResourceGroupName $rgName
    New-AzureRmVirtualNetworkGatewayConnection -Name TestLab_to_TestVNET -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestLab -VirtualNetworkGateway2 $gwTestVNET -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey
    New-AzureRmVirtualNetworkGatewayConnection -Name TestVNET_to_TestLab -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestVNET -VirtualNetworkGateway2 $gwTestLab -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey

Dopo alcuni minuti, è necessario stabilire la connessione.

Si tratta della configurazione corrente.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph3.png)
 
## <a name="phase-4-configure-dc2"></a>Fase 4: Configurare DC2

Creare una macchina virtuale per DC2. Utilizzare questi comandi al prompt dei comandi PowerShell di Azure nel computer locale.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<the storage account name for the base configuration>"
    $vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.0.4
    $vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Collegare al nuovo computer virtuale DC2 dal portale di Azure.

Configurare una regola di Windows Firewall per consentire il traffico per verificare la connettività di base. Da un livello di amministratore di Windows PowerShell Prompt dei comandi in DC2, eseguire questi comandi.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc1.corp.contoso.com

Il comando ping dovrebbe produrre quattro risposte dall'indirizzo IP 10.0.0.4. Si tratta di un test del traffico attraverso la connessione VNet a VNet.

Aggiungere il disco dati aggiuntivi su DC2 come nuovo volume con la lettera f.

1.  Nel riquadro sinistro di Server Manager, fare clic su **File e servizi di archiviazione**e quindi fare clic su **dischi**.
2.  Nel riquadro contenuto, nel gruppo **dischi** fare clic su **disco 2** (con **partizione** impostata su **sconosciuto**).
3.  Fare clic su **attività**e quindi fare clic su **Nuovo Volume**.
4.  Nella prima pagina della creazione guidata Volume di iniziare, fare clic su **Avanti**.
5.  Nella pagina Seleziona il server e del disco, fare clic su **disco 2**e quindi fare clic su **Avanti**. Quando richiesto, fare clic su **OK**.
6.  In specificare le dimensioni della pagina volume, fare clic su **Avanti**.
7.  In assegna a una pagina di unità lettera o una cartella, fare clic su **Avanti**.
8.  Nella pagina Impostazioni di sistema selezionare il file, fare clic su **Avanti**.
9.  Nella pagina Conferma selezioni, fare clic su **Crea**.
10. Al termine, fare clic su **Chiudi**.

Configurare quindi DC2 come controller di dominio replica per il dominio corp.contoso.com. Eseguire questi comandi dal prompt dei comandi di Windows PowerShell su DC2.

    Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
    Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Si noti che venga chiesto di fornire la password CORP\User1 e una password di ripristino servizi modalità (Directory) e il riavvio DC2.

Ora che la rete virtuale TestVNET è il proprio server DNS (DC2), è necessario configurare la rete virtuale TestVNET per utilizzare questo server DNS.

1.  Nel riquadro sinistro del portale di Azure, fare clic sull'icona di reti virtuali e quindi fare clic su **TestVNET**.
2.  Nella scheda **Impostazioni** fare clic su **server DNS**.
3.  In **server DNS primario**, digitare **192.168.0.4** per sostituire 10.0.0.4.
4.  Fare clic su **Salva**.

Si tratta della configurazione corrente. 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)
 
Ambiente ibrido simulata cloud è ora pronto per la verifica.

## <a name="next-step"></a>Passaggio successivo

- Impostare una [basato sul web, applicazione line-of business](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md) in un ambiente.
