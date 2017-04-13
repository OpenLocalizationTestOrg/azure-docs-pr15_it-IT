<properties
   pageTitle="Configurare Bilanciamento del carico per SQL sempre | Microsoft Azure"
   description="Configurazione di bilanciamento del carico per lavorare con SQL sempre e come sfruttare powershell per creare di bilanciamento del carico per l'implementazione di SQL"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="configure-load-balancer-for-sql-always-on"></a>Configurare Bilanciamento del carico per SQL sempre

Disponibilità di SQL Server AlwaysOn è possibile eseguire con ILB. Gruppo di disponibilità è la soluzione high-end di SQL Server ad alta disponibilità e ripristino di emergenza. Comunicare ascoltatore gruppo disponibilità consente alle applicazioni a cui connettersi facilmente replica primaria, indipendentemente dal numero replica nella configurazione client.

Il nome di comunicare ascoltatore (DNS) sia associato a un indirizzo IP di bilanciamento del carico e di bilanciamento del carico di Azure indirizza il traffico in ingresso solo al server primario del set.

È possibile utilizzare ILB supporto per gli endpoint SQL Server AlwaysOn (comunicare ascoltatore). È ora controllare l'accessibilità di comunicare ascoltatore e scegliere l'indirizzo IP di bilanciamento del carico da subnet specifica della rete virtuale (VNet).

Tramite ILB su comunicare ascoltatore, l'endpoint SQL server (ad esempio Server = tcp:ListenerName, 1433; Database = DatabaseName) accessibile solo dai:

- Servizi e macchine virtuali nella stessa rete virtuale
- Servizi e macchine virtuali dalla rete locale connesso
- Servizi e macchine virtuali da VNets interconnessi

![ILB_SQLAO_NewPic](./media/load-balancer-configure-sqlao/sqlao1.png)

Figura 1 - AlwaysOn SQL configurati con bilanciamento del carico esposto a Internet

## <a name="add-internal-load-balancer-to-the-service"></a>Aggiungere il servizio di bilanciamento del carico interno

1. Nell'esempio seguente, è possibile configurare una rete virtuale che contiene una subnet denominata "Subnet-1":

        Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc

2. Aggiungere i punti finali di bilanciamento del carico per ILB in ogni macchina virtuale

        Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –
        DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

        Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Nell'esempio precedente, si dispone di 2 macchine Virtuali denominato "sqlsvc1" e "sqlsvc2" in esecuzione nel cloud service "Sqlsvc". Dopo aver creato il ILB con parametro "DirectServerReturn", aggiungere i punti finali di bilanciamento del carico ILB per consentire a SQL configurare i listener per i gruppi di disponibilità.

Per ulteriori informazioni su SQL AlwaysOn, vedere [configurare un bilanciamento del carico interno per un gruppo di disponibilità AlwaysOn in Azure](../virtual-machines/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>Vedere anche

[Per iniziare la configurazione di un servizio di bilanciamento del carico è connessa a Internet](load-balancer-get-started-internet-arm-ps.md)

[Per iniziare la configurazione di un servizio di bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurare una modalità di distribuzione carico di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni di timeout di inattività TCP per il servizio di bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
