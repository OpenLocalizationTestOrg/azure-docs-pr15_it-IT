<properties
    pageTitle="Configurare un comunicare ascoltatore ILB per sempre sui gruppi di disponibilità | Microsoft Azure"
    description="In questa esercitazione utilizza risorse create con il modello di distribuzione classica e crea un sempre nella disponibilità gruppo comunicare ascoltatore in Azure usando un bilanciamento di carico interno (ILB)."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Configurare un comunicare ascoltatore ILB per sempre nella disponibilità i gruppi in Azure

> [AZURE.SELECTOR]
- [Comunicare ascoltatore interno](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Comunicare ascoltatore esterni](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## <a name="overview"></a>Panoramica

In questo argomento viene illustrato come configurare un comunicare ascoltatore per un sempre nel gruppo di disponibilità usando un **Interno carico bilanciamento (ILB)**.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Per configurare un comunicare ascoltatore ILB per un gruppo di disponibilità sempre attiva nel modello di Manager delle risorse, vedere [configurare un servizio di bilanciamento del carico interno per un gruppo di disponibilità sempre attiva in Azure](virtual-machines-windows-portal-sql-alwayson-int-listener.md).


Il gruppo di disponibilità possono contenere repliche sono locale solo solo Azure o estesi locale e Azure per le configurazioni ibride. Azure repliche possono trovarsi all'interno dell'area stessa oppure in più regioni utilizzando più reti virtuali (VNets). I passaggi seguenti presuppongono già [configurato un gruppo di disponibilità](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) , ma non è stato configurato un comunicare ascoltatore.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Istruzioni e limitazioni per listener interno
Tenere presente le seguenti linee guida sulla comunicare ascoltatore gruppo disponibilità in Azure utilizzando ILB:

- Comunicare ascoltatore gruppo disponibilità è supportato in Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.

- Sola comunicare ascoltatore gruppo interno disponibilità è supportata per ogni servizio cloud, poiché comunicare ascoltatore è configurato per il ILB e non esiste un solo ILB per ogni servizio cloud. Tuttavia, è possibile creare più listener esterni. Per ulteriori informazioni, vedere [configurare un comunicare esterno ascoltatore sempre sulla disponibilità per i gruppi di in Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

- Non è supportata per creare un comunicare ascoltatore interno nel servizio cloud stesso in cui è disponibile anche un esterni comunicare ascoltatore utilizzo pubblico VIP del servizio cloud.

## <a name="determine-the-accessibility-of-the-listener"></a>Determinare l'accessibilità di comunicare ascoltatore

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

In questo articolo è incentrato sulla creazione di un comunicare ascoltatore che utilizza un **Interno carico bilanciamento (ILB)**. Se è necessario comunicare ascoltatore un pubblico/esterno, vedere la versione di questo articolo che viene descritta la procedura per la configurazione di [comunicare ascoltatore esterni](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Creare i punti finali macchine Virtuali di bilanciamento del carico con server diretto restituito

Per ILB, è innanzitutto necessario creare il servizio di bilanciamento del carico interno. A tale scopo in script riportato di seguito.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Per **ILB**, è necessario assegnare un indirizzo IP statico. Prima di tutto, esaminare la configurazione di VNet corrente eseguendo il comando seguente:

        (Get-AzureVNetConfig).XMLConfiguration

1. Prendere nota del nome di **Subnet** per subnet che contiene le macchine virtuali che ospitano repliche. Questa verrà utilizzata nel parametro **$SubnetName** dello script.

1. Prendere nota del nome **VirtualNetworkSite** e iniziale **AddressPrefix** per la subnet che contiene le macchine virtuali che ospitano repliche. Cercare un indirizzo IP disponibile passando entrambi i valori per il comando **AzureStaticVNetIP Test** e analisi **AvailableAddresses**. Ad esempio, se il VNet è stato denominato *MyVNet* e un indirizzo di subnet intervallo che ha avviato in *172.16.0.128*, il comando seguente da elencare gli indirizzi disponibili:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses

1. Scegliere uno degli indirizzi disponibili e utilizzare nel parametro **$ILBStaticIP** dello script di esempio.

3. Copiare lo script di PowerShell seguente in un editor di testo e impostare i valori delle variabili in base al proprio ambiente (si noti che le impostazioni predefinite sono state ricevute le autorizzazioni per alcuni parametri). Si noti che distribuzioni esistenti che utilizzano affinità gruppi non potranno aggiungere ILB. Per ulteriori informazioni sui requisiti di ILB, vedere [Bilanciamento del carico interno](../load-balancer/load-balancer-internal-overview.md). Inoltre, se il gruppo di disponibilità si estende aree Azure, è necessario eseguire lo script una volta in ogni Data Center per il servizio cloud e nodi che si trovano in tale data center.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load balanced endpoint for each node in $AGNodes using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

1. Dopo aver impostato le variabili, copiare lo script editor di testo nella sessione di PowerShell Azure per eseguirlo. Se viene richiesto include ancora >>, digitare di nuovo invio per verificare che lo script viene avviato. Nota

>[AZURE.NOTE] Il portale classico Azure non supporta il bilanciamento del carico interno al momento, in modo che non si vedranno i ILB o i punti finali nel portale di classica Azure. Tuttavia, **Get-AzureEndpoint** restituisce un indirizzo IP interno, se il bilanciamento del carico è in esecuzione su di esso. In caso contrario, restituisce null.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Verificare che sia installato KB2854082 se necessario

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Aprire le porte del firewall in nodi di gruppo di disponibilità

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Creare comunicare ascoltatore gruppo disponibilità

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Per ILB, è necessario usare l'indirizzo IP del bilanciamento del carico interno (ILB) creata in precedenza. Utilizzare il seguente script per ottenere l'indirizzo IP in PowerShell.

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

1. In uno dei macchine virtuali, copiare lo script di PowerShell per il sistema operativo in un editor di testo e impostare le variabili ai valori indicato in precedenza.

    Per Windows Server 2012 o versioni successive è possibile usare il seguente script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
        
    Per Windows Server 2008 R2 usare il seguente script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255
    

1. Una volta impostano le variabili, apre una finestra di Windows PowerShell privilegi elevati, quindi Copia lo script editor di testo e incollarla sessione di PowerShell Azure per eseguirlo. Se viene richiesto include ancora >>, digitare di nuovo invio per verificare che lo script viene avviato.

2. Ripetere questa procedura su ogni macchina virtuale. Questo script consente di configurare la risorsa indirizzo IP con l'indirizzo IP del servizio cloud e imposta altri parametri come la porta sondaggio. Quando la risorsa indirizzo IP è in linea, quindi rispondere al polling sulla porta sondaggio dal punto finale di bilanciamento del carico creato in precedenza in questa esercitazione.

## <a name="bring-the-listener-online"></a>Porta comunicare ascoltatore online

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Elementi di completamento

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Test comunicare ascoltatore gruppo disponibilità (nello stesso VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]
