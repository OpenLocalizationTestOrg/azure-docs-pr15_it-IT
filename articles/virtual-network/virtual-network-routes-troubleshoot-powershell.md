<properties 
   pageTitle="Risolvere i problemi di routing - PowerShell | Microsoft Azure"
   description="Informazioni su come risolvere i problemi di route nel modello di distribuzione Manager delle risorse Azure tramite PowerShell Azure."
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

# <a name="troubleshoot-routes-using-azure-powershell"></a>Risolvere i problemi di indirizza tramite PowerShell Azure

> [AZURE.SELECTOR]
- [Portale di Azure](virtual-network-routes-troubleshoot-portal.md)
- [PowerShell](virtual-network-routes-troubleshoot-powershell.md)

Se si verificano problemi di connettività di rete in o dal Azure virtuale macchina, indirizza può influire sui flussi di traffico macchine Virtuali. In questo articolo viene fornita una panoramica delle funzionalità di diagnostica per route consentono di risolvere il problema.

Tabelle di route sono associate alla subnet e inefficaci su tutte le interfacce di rete (NIC) in tale subnet. I tipi seguenti di route possono essere applicati a ogni interfaccia di rete:

- **Route di sistema:** Per impostazione predefinita, ogni subnet creato in una rete virtuale Azure (VNet) sono le tabelle route di sistema che consentono il traffico VNet locale, il traffico locale tramite gateway VPN e il traffico Internet. Per VNets peered sono disponibili anche route di sistema.
- **BGP indirizza:** Propagato alle interfacce di rete tramite ExpressRoute o connessioni VPN da sito. Per ulteriori informazioni sul routing BGP, leggere gli articoli [BGP con gateway VPN](../vpn-gateway/vpn-gateway-bgp-overview.md) e [Panoramica di ExpressRoute](../expressroute/expressroute-introduction.md) .
- **Indirizza definite dall'utente (UDR):** Se si utilizza rete dispositivi di rete o sono tunneling imposto il traffico a una rete locale tramite VPN da sito, è possibile che si indirizza definite dall'utente (UDRs) associati a tabella route subnet. Se non si ha familiarità con UDRs, leggere l'articolo [indirizza definite dall'utente](virtual-networks-udr-overview.md#user-defined-routes) .

Con le route vari che possono essere applicate a un'interfaccia di rete, può essere difficile determinare quali indirizza aggregazione inefficaci. Per informazioni su come risolvere i problemi di connettività di rete macchine Virtuali, è possibile visualizzare tutte le route efficace per un'interfaccia di rete nel modello di distribuzione Manager delle risorse di Azure.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>Utilizzo delle route efficace per risolvere i problemi di flusso del traffico macchine Virtuali

In questo articolo viene lo scenario seguente come esempio viene illustrato come risolvere i problemi route efficace per un'interfaccia di rete:

Una macchina virtuale (*VM1*) connessi al VNet (*VNet1*, prefisso: 10.9.0.0/16) non riesce a connettersi a un VM(VM3) in un nuovo peered VNet (*VNet3*, 10.10.0.0/16 prefisso). Sono disponibili UDRs o BGP non indirizza applicata all'interfaccia di rete VM1 NIC1 connesso a macchina virtuale, vengono applicate solo le route di sistema.

In questo articolo viene spiegato come determinare la causa dell'errore di connessione, utilizzando la funzionalità indirizza efficace nel modello di distribuzione di Azure la gestione delle risorse.
Mentre nell'esempio vengono utilizzate solo le route di sistema, gli stessi passaggi possono essere utilizzati per determinare gli errori delle connessioni in ingresso e in uscita rispetto a qualsiasi tipo di routing.

>[AZURE.NOTE] Se la macchina virtuale ha più NIC associate, selezionare indirizza efficace per ognuna delle schede di diagnosticare i problemi di connettività di rete e da una macchina virtuale.

### <a name="view-effective-routes-for-a-virtual-machine"></a>Visualizzazione route efficace per una macchina virtuale

Per visualizzare le route aggregazione vengono applicate a una macchina virtuale, completare la procedura seguente:

### <a name="view-effective-routes-for-a-network-interface"></a>Visualizzazione route efficace per un'interfaccia di rete

Per visualizzare le route aggregazione vengono applicate all'interfaccia di rete, completare la procedura seguente:

1.  Avviare una sessione di PowerShell Azure o effettuare l'accesso Azure. Se non si ha familiarità con PowerShell di Azure, leggere l'articolo [come installare e configurare Azure PowerShell](../powershell-install-configure.md) .

2.  Il comando seguente restituisce tutte le route applicate a un'interfaccia di rete denominata *VM1 NIC1* nel gruppo di risorse *RG1*.

        Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1

    >[AZURE.TIP] Se non si conosce il nome dell'interfaccia di rete, digitare il comando seguente per recuperare i nomi di tutte le interfacce di rete in un group.* delle risorse

        Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name

    L'output seguente è simile all'output per ciascuna route applicato alla subnet A che NIC sia connesso:

        Name :
        State : Active
        AddressPrefix : {10.9.0.0/16}
        NextHopType : VNetLocal
        NextHopIpAddress : {}

        Name :
        State : Active
        AddressPrefix : {0.0.0.0/16}
        NextHopType : Internet
        NextHopIpAddress : {}

    Si noti quanto segue nell'output:
    - **Nome**: nome della route efficace può essere vuoto, se non è specificato per route definite dall'utente. 
    - **Stato**: indica lo stato del ciclo di efficaci. I valori possibili sono "Attivo" o "Non valido"
    - **AddressPrefixes**: consente di specificare il prefisso della route efficace in notazione CIDR. 
    - **nextHopType**: indica l'hop successivo per instradare specificato. Valori possibili sono *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*o *Null*. Un valore *Null* per **nextHopType** in un UDR può indicare route non valida. Ad esempio, se **nextHopType** è *VirtualAppliance* e la rete virtuale accessorio macchine Virtuali non è stato effettuato il provisioning/esecuzione. Se **nextHopType** è *VPNGateway* e non esiste alcun gateway viene completato il provisioning/esecuzione in VNet specificato, la route potrebbe risultare non valida.
    - **NextHopIpAddress**: consente di specificare l'indirizzo IP del successivo passaggio della route efficace.
    
    Il comando seguente restituisce le route in una più facile da visualizzare nella tabella:

        Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table

    L'output di seguito è parte dell'output ricevuto per lo scenario descritto in precedenza:

        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {0.0.0.0/0} Internet {}
    

3. Non esiste nessuna route elencata per la *WestUS VNet3* VNet (prefisso 10.10.0.0/16)* * da *WestUS VNet1* (prefisso 10.9.0.0/16) nell'output del passaggio precedente. Come mostrato nella figura seguente, il collegamento di peering VNet con VNet *WestUS VNet3* è nello stato *disconnesso* .
    
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    Il collegamento bidirezionale per il peering è errata, che spiega perché VM1 Impossibile connettersi al VM3 in VNet *WestUS VNet3* . Ripetere l'installazione un collegamento di peering VNet bidirezionale per *WestUS VNet1* e *WestUS VNet3* VNets. L'output restituito dopo il collegamento di peering VNet è avviato correttamente seguente:

        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
        
    Dopo aver determinato il problema, è possibile aggiungere, rimuovere, o modificare indirizza e instradare tabelle. Digitare il comando seguente per visualizzare un elenco di comandi usati per eseguire questa operazione:

        Get-Help *-AzureRmRouteConfig

## <a name="considerations"></a>Considerazioni

Alcuni aspetti da tenere presenti quando il controllo dell'elenco di route restituito:

- Il routing è basato su più lunga prefisso Confronta (LPM) tra UDRs, indirizza BGP e di sistema. Se esistono più route con la stessa corrispondenza LPM, sia selezionata una route in base a origine nell'ordine seguente:
    - Distribuzione definite dall'utente
    - Distribuzione BGP
    - Distribuzione di sistema (impostazione predefinita)

    Cicli di efficace, è possibile vedere soltanto efficace route che corrispondono a LPM in base a tutte le route disponibile. Mostrando come route effettivamente vengono valutate per una determinata scheda di rete, questo rende molto più semplice per la risoluzione route specifiche che possono influire sulle connettività da/verso la macchina virtuale.

- Se si dispone di UDRs e invia il traffico a un dispositivo virtuale (NVA), di rete con *VirtualAppliance* come **nextHopType**, verificare che l'inoltro IP è stato attivato NVA riceve il traffico o pacchetti vengono ignorati. 
- Se è abilitato la forzati tunneling, tutto il traffico Internet in uscita instradato nella distribuzione locale. RDP/SSH da Internet per la macchina virtuale potrebbe non funzionare con questa impostazione, a seconda di come i locale gestisce il traffico. 
  Tunneling forzata può essere attivato:
    - Se tramite VPN da sito, impostando una route definite dall'utente (UDR) con nextHopType come Gateway VPN
    - Se una route predefinita è annunciata su BGP
- Per il traffico peering VNet per il corretto funzionamento, deve esistere una route di sistema con **nextHopType** *VNetPeering* per intervallo prefisso del VNet peered. Se non esiste ad esempio un percorso e il collegamento di peering VNet sia corretto:
    - Attendere alcuni secondi e riprovare in caso di un collegamento di peering appena definito. Occasionalmente richiede più tempo per propagarsi indirizza a tutte le interfacce di rete in una subnet.
    - Regole gruppo di sicurezza di rete (NSG) possono influire sui flussi di traffico. Per ulteriori informazioni, vedere l'articolo di [Risolvere i problemi di gruppi di sicurezza di rete](virtual-network-nsg-troubleshoot-powershell.md) .
