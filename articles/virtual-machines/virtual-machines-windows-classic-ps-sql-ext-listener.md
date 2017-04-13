<properties
    pageTitle="Configurare un comunicare ascoltatore esterni per sempre in gruppi disponibilità | Microsoft Azure"
    description="In questa esercitazione illustra i passaggi della creazione di un sempre nella disponibilità gruppo comunicare ascoltatore in Azure è accessibile dall'esterno utilizzando l'indirizzo IP virtuale pubblico del servizio cloud associato."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/12/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Configurare un comunicare ascoltatore esterni per sempre nella disponibilità i gruppi in Azure

> [AZURE.SELECTOR]
- [Comunicare ascoltatore interno](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Comunicare ascoltatore esterni](virtual-machines-windows-classic-ps-sql-ext-listener.md)

In questo argomento viene illustrato come configurare un comunicare ascoltatore per un sempre nella disponibilità gruppo accessibili via internet. Ciò è possibile associando indirizzo **Pubblico virtuale indirizzi IP** del servizio cloud a comunicare ascoltatore.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Il gruppo di disponibilità possono contenere repliche sono locale solo solo Azure o estesi locale e Azure per le configurazioni ibride. Azure repliche possono trovarsi all'interno dell'area stessa oppure in più regioni utilizzando più reti virtuali (VNets). I passaggi seguenti presuppongono già [configurato un gruppo di disponibilità](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) , ma non è stato configurato un comunicare ascoltatore.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Istruzioni e limitazioni per i listener esterni

Quando si distribuisce usando l'indirizzo VIP pubico del servizio cloud, tenere presente le seguenti linee guida sul comunicare ascoltatore gruppo disponibilità in Azure:

- Comunicare ascoltatore gruppo disponibilità è supportato in Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.

- L'applicazione client deve trovarsi in un servizio cloud diverso da quello che contiene il gruppo di disponibilità macchine virtuali. Azure non supporta l'invio diretto al server con client e server nel servizio cloud stesso.

- Per impostazione predefinita, i passaggi descritti in questo articolo viene illustrato come configurare una comunicare ascoltatore per utilizzare l'indirizzo IP virtuale (VIP) di servizio cloud. Tuttavia, è possibile prenotare e creare più indirizzi VIP del servizio cloud. In questo modo è possibile utilizzare la procedura descritta in questo articolo per creare più listener ogni associati a un diverso VIP. Per informazioni su come creare più indirizzi VIP, vedere [Più VIP per ogni servizio cloud](../load-balancer/load-balancer-multivip.md).

- Se si sta creando un comunicare ascoltatore per un ambiente ibrido, la rete locale deve avere la connettività a Internet oltre VPN da sito con la rete virtuale Azure. In caso di Azure subnet, comunicare ascoltatore gruppo disponibilità è raggiungibile solo dall'indirizzo IP pubblico del servizio cloud rispettivi.

- Non è supportata per creare un comunicare esterni ascoltatore nello stesso servizio cloud, in cui è disponibile anche un interno comunicare ascoltatore uso interno carico bilanciamento (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Determinare l'accessibilità di comunicare ascoltatore

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

In questo articolo è incentrato sulla creazione di un comunicare ascoltatore che utilizza **bilanciamento del carico esterno**. Se si vuole comunicare un ascoltatore privato alla rete virtuale, vedere la versione di questo articolo che viene descritta la procedura per la configurazione di [comunicare ascoltatore con ILB](virtual-machines-windows-classic-ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Creare i punti finali macchine Virtuali di bilanciamento del carico con server diretto restituito

Bilanciamento del carico esterno utilizza virtuale l'indirizzo IP virtuale pubblico del servizio cloud che ospita le macchine virtuali. Pertanto non è necessaria creare o configurare il servizio di bilanciamento del carico in questo caso.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Copiare lo script di PowerShell seguente in un editor di testo e impostare i valori delle variabili in base al proprio ambiente (valori predefiniti sono state ricevute le autorizzazioni per alcuni parametri). Si noti che se il gruppo di disponibilità si estende aree Azure, è necessario eseguire lo script una volta in ogni Data Center per il servizio cloud e nodi che si trovano in tale data center.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas

        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

1. Dopo aver impostato le variabili, copiare lo script editor di testo nella sessione di PowerShell Azure per eseguirlo. Se viene richiesto include ancora >>, digitare di nuovo invio per verificare che lo script viene avviato.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Verificare che sia installato KB2854082 se necessario

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Aprire le porte del firewall in nodi di gruppo di disponibilità

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Creare comunicare ascoltatore gruppo disponibilità

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Per bilanciamento del carico esterno, è necessario ottenere l'indirizzo IP pubblico virtuale del servizio cloud che contiene le repliche. Accedere al portale classico Azure. Passare al servizio cloud che contiene il gruppo di disponibilità macchine Virtuali. Aprire la visualizzazione di **Dashboard** .

3. Annotare l'indirizzo indicato in **indirizzo IP virtuale pubblico (VIP)**. Se la soluzione si estende VNets, ripetere questo passaggio per ogni servizio cloud che contiene una macchina virtuale che ospita una replica.

4. In uno dei macchine virtuali, copiare lo script di PowerShell seguente in un editor di testo e impostare le variabili ai valori indicato in precedenza.

        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service

        Import-Module FailoverClusters

        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.

        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255


1. Una volta impostano le variabili, apre una finestra di Windows PowerShell privilegi elevati, quindi Copia lo script editor di testo e incollarla sessione di PowerShell Azure per eseguirlo. Se viene richiesto include ancora >>, digitare di nuovo invio per verificare che lo script viene avviato.

1. Ripetere questa procedura su ogni macchina virtuale. Questo script consente di configurare la risorsa indirizzo IP con l'indirizzo IP del servizio cloud e imposta altri parametri come la porta sondaggio. Quando la risorsa indirizzo IP è in linea, quindi rispondere al polling sulla porta sondaggio dal punto finale di bilanciamento del carico creato in precedenza in questa esercitazione.

## <a name="bring-the-listener-online"></a>Porta comunicare ascoltatore online

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Elementi di completamento

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Test comunicare ascoltatore gruppo disponibilità (nello stesso VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Test comunicare ascoltatore gruppo disponibilità (su internet)

Per accedere a comunicare ascoltatore dall'esterno della rete virtuale, è necessario utilizzare Bilanciamento del carico esterno/pubblico (descritto in questo argomento) invece di ILB, che è accessibile da solo all'interno di VNet stesso. Nella stringa di connessione, specificare il nome del servizio cloud. Ad esempio, se si dispone di un servizio cloud con il nome *mycloudservice*, l'istruzione sqlcmd dovrebbero essere come indicato di seguito:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

A differenza dell'esempio precedente, è necessario utilizzare l'autenticazione di SQL in quanto il chiamante non è possibile utilizzare l'autenticazione di windows tramite internet. Per ulteriori informazioni, vedere [sempre nel gruppo di disponibilità in macchine Virtuali di Azure: scenari di connettività Client](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Quando si utilizza l'autenticazione di SQL, assicurarsi di creare lo stesso account in replica. Per ulteriori informazioni sulla risoluzione dei problemi di accesso dei gruppi di disponibilità, vedere [come eseguire il mapping degli account di accesso o utilizzare contenuto utente del database SQL per connettersi a sé e mappare ai database di disponibilità](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Se le repliche sempre nella si trovano in subnet diverse, client specificare **MultisubnetFailover = True** nella stringa di connessione. Il risultato tentativi di connessione in parallelo alle repliche in subnet diverse. Si noti che questo scenario include una distribuzione di sempre nel gruppo di disponibilità tra regione.

## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]
