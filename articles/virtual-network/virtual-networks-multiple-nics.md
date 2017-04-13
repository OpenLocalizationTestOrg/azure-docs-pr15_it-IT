<properties
   pageTitle="Creare una macchina virtuale con più schede di rete"
   description="Informazioni su come creare e configurare macchine virtuali con più schede di rete"
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management,azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="create-a-vm-with-multiple-nics"></a>Creare una macchina virtuale con più schede di rete

È possibile creare macchine () in Azure e collegare ognuna delle proprie macchine virtuali più interfacce di rete (NIC). NIC multipla è un requisito per molti rete dispositivi di rete, ad esempio distribuzione delle applicazioni e le soluzioni di ottimizzazione WAN. NIC Multi vengono forniti anche altre funzionalità di gestione di traffico di rete, inclusi isolamento di traffico tra un front end NIC e il fine NIC o separazione di traffico piano dati dal traffico di piano di gestione.

![NIC multi per macchine Virtuali](./media/virtual-networks-multiple-nics/IC757773.png)

Nella figura riportata sopra mostra una macchina virtuale con tre schede, che ciascuno connesso a una subnet diversa.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]

- VIP esposto a Internet (distribuzioni classiche) è supportato solo sulla scheda NIC "predefinito". Esiste un solo indirizzo VIP all'indirizzo IP della scheda NIC predefinito.
- Al momento non supportati per più macchine virtuali NIC indirizzi istanza livello pubblico IP (LPIP) (distribuzioni classiche).
- L'ordine delle schede dall'interno della macchina virtuale sarà casuale e può inoltre modificare in Azure infrastruttura aggiornamenti. Tuttavia, gli indirizzi IP ed ethernet corrispondente MAC indirizzi verranno rimangono invariati. Si supponga ad esempio **che eth1** con l'indirizzo IP 10.1.0.100 e indirizzo MAC 00-0D-3A-B0-39-0D; Dopo un aggiornamento dell'infrastruttura di Azure e riavviare il computer, può essere modificato in **Eth2**, ma l'indirizzo IP e MAC associazione rimarrà invariato. Quando si è iniziato dal cliente, riavviare l'ordine NIC rimarrà invariato.
- L'indirizzo per ogni scheda in ogni macchina virtuale deve trovarsi in una subnet, più schede di rete in una macchina virtuale singola ogni assegnando indirizzi che si trovano nella stessa subnet.
- Dimensione memoria virtuale determina il numero di schede di rete che è possibile creare per una macchina virtuale. Riferimento [Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) e gli articoli di dimensioni macchine Virtuali [Linux](../virtual-machines/virtual-machines-linux-sizes.md) per determinare quante NIC supportano le dimensioni di ogni macchina virtuale. 

## <a name="network-security-groups-nsgs"></a>Gruppi di sicurezza di rete (NSGs)
In una distribuzione di Manager delle risorse, qualunque NIC in una macchina virtuale possono essere associati a una rete protezione gruppo (NSG), tra le schede su una macchina virtuale che contiene più schede di rete abilitati. Se una scheda di rete è assegnato un indirizzo all'interno di una subnet in cui è associata un NSG alla subnet, quindi le regole NSG di subnet applicano anche a tale scheda di rete. Oltre a associa NSGs subnet, è inoltre possibile associare una scheda di rete con un NSG.

Se una subnet è associata a un NSG e una scheda di rete all'interno di tale subnet singolarmente associata a un NSG, le regole NSG associate vengono applicate nell' **ordine di flusso** in base all'orientamento del traffico passato o ridurre la scheda di rete:

- **Il traffico in ingresso** il cui destinazione è la scheda di rete in questione attraversa prima subnet, attivare le regole NSG di subnet, prima di passare la scheda di rete, quindi attivare le regole di NSG della scheda NIC.
- **Il traffico in uscita** la cui origine è NIC in questione scorra prima di tutto indietro da NIC, attivare le regole NSG della scheda NIC, prima che attraversa alla subnet, quindi attivare le regole di NSG della subnet.

Ulteriori informazioni su come [Gruppi di sicurezza di rete](virtual-networks-nsg.md) e modalità di applicazione in base alle associazioni subnet, macchine virtuali e NIC...

## <a name="how-to-configure-a-multi-nic-vm-in-a-classic-deployment"></a>Come configurare un multiplo NIC VM in una distribuzione classica

Le istruzioni riportate di seguito consente di creare un con più NIC VM contenente 3 NIC: NIC predefinito e due schede di rete aggiuntive. I passaggi di configurazione verranno creata una macchina virtuale configurati in base al frammento di file di configurazione del servizio riportata di seguito:

    <VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
    <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
    … Skip over the remainder section …
    </VirtualNetworkSite>


È necessario i prerequisiti seguenti prima di provare a eseguire i comandi PowerShell di esempio.

- Un abbonamento Azure.
- Una rete virtuale configurata. Per ulteriori informazioni su VNets, vedere [Virtuale panoramica di rete](virtual-networks-overview.md) .
- La versione più recente di Azure PowerShell scaricato e installato. Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Per creare una macchina virtuale con più schede di rete, seguire la procedura seguente:

1. Selezionare un'immagine di macchine Virtuali dalla raccolta immagini macchine Virtuali di Azure. Si noti che le immagini a variazioni frequenti e sono disponibili per l'area geografica. L'immagine specificata nell'esempio seguente modificare o potrebbe non sia nella propria area geografica, sarà necessario specificare l'immagine che è necessario.

        $image = Get-AzureVMImage `
            -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. Creare una configurazione macchina virtuale.

        $vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
            -Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. Creare l'accesso di amministratore predefinito.

        Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
            -Password "<YourAdminPassword>"

1. Aggiungere altre schede di rete per la configurazione di macchine Virtuali.

        Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
            -SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm
        Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
            -SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. Specificare l'indirizzo IP e subnet per la scheda predefinita.

        Set-AzureSubnet -SubnetNames "Frontend" -VM $vm
        Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm

1. Creare la macchina virtuale nella rete virtuale.

        New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE] VNet che è possibile specificare esista già (come indicato nei prerequisiti). Nell'esempio seguente specifica una rete virtuale denominata **MultiNIC VNet**.

## <a name="limitations"></a>Limitazioni

Quando si utilizza la caratteristica di multiplo NIC, le limitazioni seguenti sono applicabili:

- Macchine virtuali di NIC con più devono essere create in reti virtuali Azure (VNets). Macchine virtuali VNet non può essere configurate con più schede.
- Tutte le macchine virtuali di un set di disponibilità necessario utilizzare più NIC oppure singola rete. Non possono essere presenti una combinazione di macchine virtuali NIC multipla e macchine virtuali NIC singolo all'interno di un set di disponibilità. Stessa regole per le macchine virtuali in un servizio cloud.
- Non è possibile configurare una macchina virtuale con singola scheda con più NIC (e viceversa) una volta distribuito, senza eliminare e crearne uno nuovo.


## <a name="secondary-nics-access-to-other-subnets"></a>Accesso NIC secondarie ad altre subnet

Per impostazione predefinita NIC secondaria non essere configurata con un gateway predefinito, a causa di cui il flusso di traffico in NIC secondario sarà limitato per essere della stessa subnet. Se gli utenti desiderano abilitare NIC secondaria comunicare di fuori le proprie subnet, sarà necessario aggiungere una voce nella tabella di routing per configurare il gateway come descritto di seguito.

>[AZURE.NOTE] Macchine virtuali create prima della luglio 2015 abbia un gateway predefinito configurato per tutte le NIC. Il gateway predefinito per NIC secondario non verrà rimosso fino a quando non vengono riavviate queste macchine virtuali. Nei sistemi operativi che utilizzano il modello di routing host vulnerabile, ad esempio Linux, è possibile interrompere la connessione a Internet se il traffico in ingresso e in uscita utilizza schede di rete diverse.

### <a name="configure-windows-vms"></a>Configurare macchine virtuali di Windows

Si supponga di avere una macchina virtuale di Windows con due schede di rete come indicato di seguito:

- Indirizzo IP NIC primario: 192.168.1.4
- Indirizzo IP NIC secondario: 192.168.2.5

Tabella di route IPv4 per questa macchina virtuale sarà simile alla seguente:

    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
          192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
          192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
        192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
          192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
          192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
        192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
            224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
            224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
      255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
      255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
    ===========================================================================

Si noti che la route predefinita (0.0.0.0) è disponibile solo per la rete principale. Non sarà possibile eseguire l'accesso esterno alla subnet per NIC secondario, come indicato di seguito:

    C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

    Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.

Per aggiungere una route predefinita nella scheda di rete secondario, attenersi alla procedura indicata di seguito:

1. Dal prompt dei comandi, eseguire il comando seguente per identificare il numero di indice per la scheda secondaria:

        C:\Users\Administrator>route print
        ===========================================================================
        Interface List
         29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
         27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
          1...........................Software Loopback Interface 1
         14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
         20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
        ===========================================================================

2. Si noti la seconda voce nella tabella, con un indice di 27 (in questo esempio).
3. Dal prompt dei comandi, eseguire il comando **route add** come illustrato di seguito. In questo esempio si specifica 192.168.2.1 come gateway predefinito per la scheda secondaria:

        route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27

4. Per testare la connettività, tornare al prompt dei comandi e provare a effettuare il ping una subnet diversa da NIC secondario come illustrato int eh esempio riportato di seguito:

        C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128

5. È inoltre possibile verificare la tabella di route per controllare la distribuzione appena aggiunta, come illustrato di seguito:

        C:\Users\Administrator>route print

        ...

        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                  0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
                  0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### <a name="configure-linux-vms"></a>Configurare macchine virtuali Linux

Per le macchine virtuali Linux, poiché il comportamento predefinito utilizza host vulnerabile routing, è consigliabile che sono limitate ai flussi di traffico solo all'interno della stessa subnet NIC secondaria. Tuttavia se alcuni scenari richiedono connettività esterno alla subnet, gli utenti devono attivare criteri in base a routing in modo che il traffico in ingresso e in uscita utilizzi la stessa scheda di rete.

## <a name="next-steps"></a>Passaggi successivi

- Distribuire [Macchine virtuali di MultiNIC in uno scenario di applicazioni di livello 2 in una distribuzione di Manager delle risorse](virtual-network-deploy-multinic-arm-template.md).
- Distribuire [Macchine virtuali di MultiNIC in uno scenario di applicazioni di livello 2 in una distribuzione classica](virtual-network-deploy-multinic-classic-ps.md).
