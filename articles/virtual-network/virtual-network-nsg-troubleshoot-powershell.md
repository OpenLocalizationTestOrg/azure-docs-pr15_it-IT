<properties 
   pageTitle="Risolvere i problemi di gruppi di sicurezza di rete - PowerShell | Microsoft Azure"
   description="Informazioni su come risolvere i problemi di gruppi di sicurezza di rete nel modello di distribuzione Manager delle risorse Azure tramite PowerShell Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="anithaa" />

# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Risolvere i problemi di gruppi di sicurezza di rete con PowerShell Azure

> [AZURE.SELECTOR]
- [Portale di Azure](virtual-network-nsg-troubleshoot-portal.md)
- [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)

Se si verificano problemi di connettività macchine Virtuali configurato gruppi di sicurezza di rete (NSGs) nelle macchine (), in questo articolo viene fornita una panoramica delle funzionalità di diagnostica per NSGs consentono di risolvere il problema.

NSGs consentono di controllare i tipi di traffico che scorrono da e verso il macchine (). NSGs possono essere applicate a subnet in una rete virtuale Azure (VNet), le interfacce di rete (NIC) o entrambi. Le regole efficace applicate a una scheda sono un'aggregazione di regole esistenti in NSGs applicato a una scheda di rete e subnet a che sia connesso. Le regole tra queste NSGs possono talvolta conflitto tra loro ed effetti in termini di connettività di rete di una macchina virtuale.  

È possibile visualizzare tutte le regole di protezione efficace dal NSGs come applicati alle schede della macchina di virtuale. In questo articolo viene illustrato come risolvere i problemi di connettività macchine Virtuali utilizzando le seguenti regole nel modello di distribuzione Manager delle risorse di Azure. Se non si ha familiarità con i concetti VNet e NSG, leggere gli articoli di panoramica [rete virtuale](virtual-networks-overview.md) e [gruppi di sicurezza di rete](virtual-networks-nsg.md) .

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Utilizzo delle regole di protezione efficace per risolvere i problemi di flusso del traffico macchine Virtuali

Scenario che segue è illustrato un esempio di un problema di connessione comuni:

Una macchina virtuale denominata *VM1* fa parte di una subnet denominata *Subnet1* all'interno di un VNet denominato *WestUS VNet1*. Un tentativo di connettersi a macchine Virtuali utilizzando RDP tramite la porta TCP 3389 non riesce. NSGs vengono applicate le NIC *VM1 NIC1* e subnet *Subnet1*. Il traffico alla porta TCP 3389 consentito in NSG associata all'interfaccia di rete *VM1 NIC1*, tuttavia TCP effettuare il ping al hanno esito negativo porta 3389 del VM1.

Mentre in questo esempio Usa la porta TCP 3389, è possibile utilizzare la procedura seguente per determinare gli errori delle connessioni in ingresso e in uscita su qualsiasi porta.

## <a name="detailed-troubleshooting-steps"></a>Modalità di risoluzione dei problemi
Completare la procedura seguente per risolvere i problemi NSGs per una macchina virtuale:

1. Avviare una sessione di PowerShell Azure o effettuare l'accesso Azure. Se non si ha familiarità con PowerShell di Azure, leggere l'articolo [come installare e configurare Azure PowerShell](../powershell-install-configure.md) .

2. Immettere il comando seguente per restituire tutte le regole NSG applicate a una scheda denominata *VM1 NIC1* nel gruppo di risorse *RG1*:

        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1

    >[AZURE.TIP] Se non si conosce il nome di una scheda di rete, immettere il comando seguente per recuperare i nomi di tutte le NIC in un gruppo di risorse: 
    
    >`Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`

    Testo riportato di seguito è riportato un esempio dell'output regole efficace restituito per *NIC1 VM1* NIC:

        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
        
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]

    Tenere presente le seguenti informazioni nell'output:

    - Esistono due sezioni di **NetworkSecurityGroup** : uno è associato a una subnet (*Subnet1*) e uno è associato a una scheda di rete (*VM1 NIC1*). In questo esempio, un NSG è stato applicato a tutti.
    - **Associazione** Mostra la risorsa (subnet o NIC) è associato un NSG specificato. Se la risorsa NSG spostato separato immediatamente prima di eseguire questo comando, potrebbe essere necessario attendere alcuni secondi per la modifica in modo da rispecchiare nell'output del comando. 
    - I nomi delle regole che sono preceduti *defaultSecurityRules*: NSG un quando viene creato, vengono create più regole di sicurezza predefiniti. Non è possibile rimuovere le regole predefinite, ma può essere sostituiti con regole di priorità superiore.
     Leggere l'articolo [Panoramica NSG](virtual-networks-nsg.md#default-rules) per ulteriori informazioni sulle regole di sicurezza predefiniti NSG.
    - **ExpandedAddressPrefix** espande prefissi per NSG predefinita tag. Tag rappresentano più prefissi degli indirizzi. Espansione dei contrassegni che può essere utile quando la risoluzione dei problemi di connettività di macchine Virtuali da/verso prefissi indirizzo specifico. Ad esempio, con VNET peering, tag VIRTUAL_NETWORK viene espansa per visualizzare peered VNet prefissi nell'output precedente.

        >[AZURE.NOTE] Comando solo presentazioni efficaci regole se un NSG è associata a una subnet, una scheda di rete o entrambe. Una macchina virtuale potrebbe essere più schede di rete con diversi NSGs applicati. Per risolvere il problema, eseguire il comando per ciascuna NIC.
        
3. Per ridurre il filtro sul numero più grande di regole NSG, immettere i comandi seguenti per risolvere il problema: 

        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView

    Un filtro per il traffico RDP (porta TCP 3389), viene applicato alla visualizzazione della griglia, come illustrato nella figura seguente:

    ![Elenco delle regole](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
    
4. Come si può notare nella visualizzazione griglia, esistono consentire sia le regole di negazione per RDP. L'output del passaggio 2 indica che la regola *DenyRDP* in NSG applicati alla subnet. Per le regole in entrata NSGs applicati alla subnet vengono elaborate per prime. Se viene trovata una corrispondenza, NSG applicata all'interfaccia di rete non viene elaborato. In questo caso, la regola *DenyRDP* dalla subnet Blocca RDP per la macchina virtuale (**VM1**).

    >[AZURE.NOTE] Una macchina virtuale potrebbe essere più schede di rete collegate. Ogni può essere connesso a una subnet diversa. Poiché i comandi della procedura precedente vengono eseguiti su una scheda di rete, è importante specificare NIC in caso di mancata connettività. Se non si sa, è sempre possibile eseguire i comandi rispetto a ogni NIC collegata al macchina virtuale.

5. Per RDP in VM1, modificare la regola *Negare RDP (3389)* per *Consentire a RDP(3389)* in **Subnet1 NSG** NSG. Verificare che la porta TCP 3389 sia aperta aprendo una connessione RDP verso la macchina virtuale o tramite lo strumento PsPing. Altre informazioni sulle PsPing, leggere la [pagina di download PsPing](https://technet.microsoft.com/sysinternals/psping.aspx)

    È possibile o rimuovere le regole da un NSG in base alle informazioni nell'output del comando seguente:

        Get-Help *-AzureRmNetworkSecurityRuleConfig
        

## <a name="considerations"></a>Considerazioni

Per la risoluzione dei problemi di connettività, tenere presente quanto segue:

- Regole NSG predefinite verranno bloccare l'accesso in ingresso da internet e consentire solo VNet il traffico in ingresso. Regole devono essere aggiunto in modo esplicito per consentire l'accesso in ingresso da Internet, in base alle esigenze.
- Se non esistono regole sicurezza NSG causa la connettività di rete una Virtual Machine per avere esito negativo, il problema potrebbe essere dovuto a:
    - Software firewall in esecuzione all'interno del sistema operativo della macchina virtuale
    - Indirizza configurato per il traffico locale o dispositivi di rete. Nella distribuzione locale tramite il tunneling forzata, è possibile reindirizzare il traffico Internet. Una connessione RDP/SSH da Internet per la macchina virtuale potrebbe non funzionare con questa impostazione, a seconda di come hardware di rete locale gestisce il traffico. Leggere l'articolo [Cicli di risoluzione dei problemi](virtual-network-routes-troubleshoot-powershell.md) per imparare a diagnosticare i problemi di route che possono essere impedendo il flusso di traffico da e verso la macchina virtuale. 
- Se si dispone di peered VNets, per impostazione predefinita, il contrassegno VIRTUAL_NETWORK verrà espanso automaticamente per includere prefissi VNets peered. È possibile visualizzare tali prefissi nell'elenco **ExpandedAddressPrefix** per la risoluzione dei problemi relativi alla connettività peering VNet. 
- Regole di protezione effettiva vengono visualizzate solo se è presente un NSG associato la macchina virtuale NIC e o subnet. 
- Se sono non presenti NSGs associato con la scheda di rete o subnet e si dispone di un indirizzo IP pubblico assegnato alle macchine Virtuali, tutte le porte sarà aperte per l'accesso in ingresso e in uscita. Se la macchina virtuale ha un indirizzo IP pubblico, l'applicazione NSGs al NIC o alla subnet è consigliabile.  
