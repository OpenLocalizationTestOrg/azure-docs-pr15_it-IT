<properties
   pageTitle="Configurare sempre sul gruppo di disponibilità listener: Microsoft Azure"
   description="Configurare la disponibilità gruppo listener sul modello di gestione risorse di Azure, usando un sistema di bilanciamento del carico interno con uno o più indirizzi IP."
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="10/20/2016"
   ms.author="MikeRayMSFT"/>

# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Configurare uno o più sempre nella disponibilità gruppo listener - Gestione risorse 

In questo argomento viene illustrato come eseguire due operazioni:

- Creare un servizio di bilanciamento del carico interno per i gruppi di disponibilità di SQL Server uso dei cmdlet di PowerShell.

- Aggiungere altri indirizzi IP a un bilanciamento del carico per supportare più di un gruppo di disponibilità di SQL Server. 

In SQL Server un comunicare ascoltatore gruppo disponibilità è un nome di rete virtuale che i client si connettono a per accedere a un database della replica primario o secondario. In macchine virtuali Azure, un servizio di bilanciamento del carico contiene l'indirizzo IP per comunicare ascoltatore. Bilanciamento del carico indirizza il traffico all'istanza di SQL Server in ascolto sulla porta sondaggio. Nella maggior parte dei casi, un gruppo di disponibilità utilizza un servizio di bilanciamento del carico interno. Un servizio di bilanciamento del carico interno Azure può contenere uno o più indirizzi IP. Tutti gli indirizzi IP utilizza una porta verifica specifico. In questo documento viene illustrato come usare PowerShell per creare un nuovo bilanciamento del carico o aggiungere gli indirizzi IP a un di bilanciamento del carico esistente per i gruppi di disponibilità di SQL Server. 

La possibilità di assegnare più indirizzi IP a un servizio di bilanciamento del carico interno è le novità in Azure ed è disponibile solo in modello di gestione delle risorse. Per completare questa attività, è necessario disporre di un gruppo di disponibilità di SQL Server in macchine virtuali Azure nel modello di Manager delle risorse. Entrambe le macchine virtuali di SQL Server deve appartenere allo stesso set di disponibilità. È possibile utilizzare il [modello di Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) per creare automaticamente il gruppo di disponibilità in Gestione risorse di Azure. Questo modello crea automaticamente il gruppo di disponibilità, tra cui il servizio di bilanciamento del carico interno dell'utente. Se si preferisce, è possibile [configurare manualmente un gruppo di disponibilità AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

In questo argomento è necessario che i gruppi di disponibilità sono già configurati.  

Argomenti correlati includono:

- [Configurare la disponibilità AlwaysOn in macchine Virtuali Azure (grafica)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   

- [Configurare una connessione VNet a VNet tramite Gestione risorse di Azure e PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Configurare Windows Firewall

Configurare Windows Firewall per consentire l'accesso a SQL Server. È necessario configurare il firewall per consentire connessioni TCP per l'uso di porte mediante l'istanza di SQL Server, come la porta utilizzata per la ricerca di comunicare ascoltatore. Per informazioni dettagliate, vedere [configurare un Firewall di Windows per l'accesso al motore di Database](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Creare una regola in entrata per la porta di SQL Server e per la porta sondaggio.

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Script di esempio: Creare un sistema di bilanciamento del carico interno con PowerShell

> [AZURE.NOTE] Se è stato creato il gruppo di disponibilità con il [modello di Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) il carico non è necessario completare questo passaggio. 

Il seguente script di PowerShell crea un servizio di bilanciamento del carico interno, consente di configurare le regole di bilanciamento del carico e imposta un indirizzo IP per il servizio di bilanciamento del carico. Per eseguire lo script, aprire Windows PowerShell ISE e incollare lo script nel riquadro dello Script. Usare `Login-AzureRMAccount` per l'accesso a PowerShell. Se si hanno più abbonamenti Azure, utilizzare `Select-AzureRmSubscription ` per impostare l'abbonamento. 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the Front End configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the Back End configuration

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a>Script di esempio: aggiungere un indirizzo IP a un servizio di bilanciamento del carico esistente con PowerShell

Per utilizzare più di un gruppo di disponibilità, usare PowerShell per aggiungere l'indirizzo IP a un servizio di bilanciamento del carico esistente. Tutti gli indirizzi IP richiedono il proprio bilanciamento del carico regola, verifica porta e porta anteriore.

La porta front-end è la porta applicazioni utilizzano per connettersi all'istanza di SQL Server. Indirizzi IP per i gruppi di disponibilità diversi è possono utilizzare la stessa porta front-end.

>[AZURE.NOTE] Per i gruppi di disponibilità di SQL Server, ogni indirizzo IP è necessaria una porta verifica specifico. Se, ad esempio un indirizzo IP su un bilanciamento del carico non utilizza verifica porta 59999, altri indirizzi IP di tale servizio di bilanciamento del carico possono utilizzare verifica porta 59999. 

- Per informazioni su bilanciamento del carico limiti vedere **anteriore privato terminare IP per bilanciamento del carico** in [Rete limiti - Gestione risorse di Azure](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

- Per informazioni sulla disponibilità gruppo limita vedere [restrizioni (disponibilità gruppi)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Il seguente script aggiunge un nuovo indirizzo IP a un servizio di bilanciamento del carico esistente. Aggiornare le variabili per l'ambiente. Il ILB utilizza la porta di ascolto per la porta front-end di bilanciamento del carico. Questa porta può essere la porta SQL Server è in attesa. Per le istanze predefinite di SQL Server, questa è la porta 1433. Regola per un gruppo di disponibilità di bilanciamento del carico richiede un indirizzo IP mobile (diretto server restituito) in modo che la porta back-end è identica a quella front-end.

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
```



## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurare il cluster per utilizzare l'indirizzo IP di bilanciamento carico 

Il passaggio successivo consiste nel configurare comunicare ascoltatore sul cluster e visualizzare comunicare ascoltatore online. A tale scopo, eseguire le operazioni seguenti: 

1. Creare comunicare ascoltatore gruppo disponibilità in del cluster  

2. Porta comunicare ascoltatore online

## <a name="1-create-the-availability-group-listener-on-the-failover-cluster"></a>1. creare il comunicare ascoltatore gruppo disponibilità del cluster

In questo passaggio, aggiungere un punto di accesso client del cluster con Failover Cluster Manager e quindi usare PowerShell per configurare la risorsa per utilizzare la porta sondaggio. 

1. Utilizzare RDP per connettere la macchina virtuale Azure che ospita la replica principale. 

2. Aprire Gestione Cluster di Failover.

3. Selezionare il nodo **reti** e prendere nota del nome di rete cluster. Utilizzare questo nome nella `$ClusterNetworkName` variabili dello script di PowerShell.

4. Espandere il nome del cluster e quindi fare clic su **ruoli**.

5. Nel riquadro di **ruoli** , pulsante destro del mouse sul nome del gruppo disponibilità e quindi selezionare **Aggiungi risorsa** > **Punto di accesso Client**.

6. Nella casella **nome** , creare un nome per il nuovo comunicare ascoltatore, quindi fare doppio clic su **Avanti** e quindi fare clic su **Fine**. Non visualizzare comunicare ascoltatore o la risorsa online a questo punto.
 
    Il nome del nuovo comunicare ascoltatore è il nome di rete utilizzate dalle applicazioni per connettersi al database nel gruppo di disponibilità di SQL Server.

7. Fare clic sulla scheda **risorse** , quindi espandere il punto di accesso Client appena creato. Pulsante destro del mouse la risorsa IP e scegliere Proprietà. Prendere nota del nome dell'indirizzo IP. Questo nome in verrà utilizzato il `$IPResourceName` variabili dello script di PowerShell.

8. In **Indirizzo IP** fare clic su **Indirizzo IP statico** e impostare l'indirizzo IP statico per lo stesso indirizzo utilizzato quando si imposta l'indirizzo IP di bilanciamento carico nel portale di Azure. 

9. Disattivare NetBIOS per questo indirizzo e fare clic su **OK**. Ripetere questo passaggio per ogni risorsa IP se la soluzione si estende su più VNets Azure. 

10. Rendere la risorsa gruppo di disponibilità di SQL Server dipende l'indirizzo IP. Destra fare clic su risorsa in Gestione cluster, verrà visualizzata nella scheda **risorse** in **Altre risorse**. 

11. In nodo cluster che ospita attualmente la replica principale, aprire una finestra di PowerShell ISE e incollare i comandi seguenti in un nuovo script. Nella scheda **dipendenze** , fare clic sul nome di comunicare ascoltatore.
 
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>

    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```
 
6. Aggiornare le variabili ed eseguire lo script di PowerShell per configurare l'indirizzo IP e porte per comunicare ascoltatore nuovo.

 >[AZURE.NOTE] Se il server SQL in aree separate, è necessario eseguire due volte lo script di PowerShell. La prima volta usare il nome di rete cluster, nome risorsa IP cluster e caricare indirizzo IP di bilanciamento del primo gruppo di risorse. La seconda volta usare il nome di rete cluster, nome risorsa IP cluster e caricare indirizzo IP di bilanciamento del secondo gruppo di risorse.

Il cluster è ora disponibile una risorsa di comunicare ascoltatore gruppo disponibilità.

## <a name="2-bring-the-listener-online"></a>2. porta comunicare ascoltatore online

Risorsa di comunicare ascoltatore gruppo disponibilità configurato, è possibile trasferire anche comunicare ascoltatore online in modo che le applicazioni possono connettersi ai database nel gruppo di disponibilità con comunicare ascoltatore.

1. Passare a gestione Cluster di Failover. Espandere **i ruoli** e quindi selezionare il gruppo di disponibilità. Nella scheda **risorse** , pulsante destro del mouse sul nome di comunicare ascoltatore e scegliere **proprietà**.

1. Fare clic sulla scheda **relazioni** . Se sono presenti più risorse nell'elenco, verificare che gli indirizzi IP o meno e, dipendenze. Fare clic su **OK**.

1. Pulsante destro del mouse sul nome di comunicare ascoltatore e fare clic su **Connetti**.

1. Una volta comunicare ascoltatore sia connesso a Internet, nella scheda **risorse** destro del mouse sul gruppo di disponibilità e fare clic su **proprietà**.

1. Creare una dipendenza sulla risorsa nome comunicare ascoltatore (non nome risorse indirizzo IP). Fare clic su **OK**.

1. Avviare SQL Server Management Studio e connettersi a replica primaria.

1. Passare alla **disponibilità AlwaysOn alta** | **disponibilità gruppi** | **gruppo di disponibilità listener**. 

1. Viene visualizzato il nome di comunicare ascoltatore creato in Gestione Cluster di Failover. Pulsante destro del mouse sul nome di comunicare ascoltatore e scegliere **proprietà**.

1. Nella casella **porta** specificare il numero di porta per comunicare ascoltatore gruppo disponibilità utilizzando $EndpointPort utilizzato in precedenza (1433 era l'impostazione predefinita), quindi fare clic su **OK**.

Ora è un gruppo di disponibilità di SQL Server in macchine virtuali Azure in esecuzione in modalità di gestione risorse. 

## <a name="test-the-connection-to-the-listener"></a>Verificare la connessione a comunicare ascoltatore

Per verificare la connessione:

1. RDP a SQL Server nella stessa rete virtuale, ma non dispone di replica. Può trattarsi di altri SQL Server del cluster.

2. L'utilità **sqlcmd** per verificare la connessione. Ad esempio, il seguente script stabilisce una connessione **sqlcmd** replica primaria tramite comunicare ascoltatore con l'autenticazione di Windows:

    ```
    sqlmd -S <listenerName> -E
    ```

    Se una porta diverso da quello predefinito è utilizzata da comunicare ascoltatore porta (1433), specificare la porta nella stringa di connessione. Ad esempio, il seguente comando sqlcmd si connette a un comunicare ascoltatore porta 1435: 
    
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

La connessione SQLCMD si connetterà automaticamente a qualsiasi istanza di SQL Server ospita la replica principale. 

>[AZURE.NOTE] Assicurarsi che la porta specificata sia aperta sul firewall di entrambi i server SQL. Entrambi i server richiedono una regola in entrata per la porta TCP in uso. Per ulteriori informazioni, vedere [aggiunta o Modifica regola Firewall](http://technet.microsoft.com/library/cc753558.aspx) . 

## <a name="guidelines-and-limitations"></a>Linee guida e le limitazioni

Tenere presente le seguenti linee guida sulla comunicare ascoltatore gruppo disponibilità in Azure uso interno bilanciamento del carico:

- Con un sistema di bilanciamento del carico interno solo accedere comunicare ascoltatore all'interno della stessa rete virtuale.

## <a name="for-more-information"></a>Per ulteriori informazioni

Per ulteriori informazioni, vedere [configurare sempre nella disponibilità di gruppo in macchine Virtuali di Azure manualmente](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

### <a name="powershell-cmdlets"></a>Cmdlet di PowerShell

Utilizzare i cmdlet di PowerShell seguenti per creare un bilanciamento del carico interno per macchine virtuali di Azure.

- `New-AzureRmLoadBalancer`Crea un bilanciamento del carico. Per ulteriori informazioni, vedere [AzureRmLoadBalancer di nuovo](http://msdn.microsoft.com/library/mt619450.aspx) . 

- `New-AzureRMLoadBalancerFrontendIpConfig`Crea una configurazione IP front-end per un bilanciamento del carico. Per ulteriori informazioni, vedere [AzureRMLoadBalancerFrontendIpConfig di nuovo](http://msdn.microsoft.com/library/mt603510.aspx) .

- `New-AzureRmLoadBalancerRuleConfig`Crea una configurazione regola per un bilanciamento del carico. Per ulteriori informazioni, vedere [AzureRmLoadBalancerRuleConfig di nuovo](http://msdn.microsoft.com/library/mt619391.aspx) . 

- `New-AzureRMLoadBalancerBackendAddressPoolConfig`Crea una configurazione pool indirizzo back-end per un bilanciamento del carico. Per ulteriori informazioni, vedere [AzureRmLoadBalancerBackendAddressPoolConfig di nuovo](http://msdn.microsoft.com/library/mt603791.aspx) . 

- `New-AzureRmLoadBalancerProbeConfig`Crea una configurazione di ricerca per un bilanciamento del carico. Per ulteriori informazioni, vedere [AzureRmLoadBalancerProbeConfig di nuovo](http://msdn.microsoft.com/library/mt603847.aspx) .

Se è necessario rimuovere un bilanciamento del carico da un gruppo di risorse Azure, utilizzare `Remove-AzureRmLoadBalancer`. Per ulteriori informazioni, vedere [Rimuovi AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx).
