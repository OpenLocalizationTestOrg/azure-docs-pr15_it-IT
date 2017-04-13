<properties 
   pageTitle="Risolvere i problemi di routing - portale | Microsoft Azure"
   description="Informazioni su come risolvere i problemi di route nel modello di distribuzione Manager delle risorse Azure tramite il portale di Azure."
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

# <a name="troubleshoot-routes-using-the-azure-portal"></a>Risolvere i problemi di route tramite il portale di Azure

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

1. Accedere al portale di Azure all'https://portal.azure.com.
2. Fare clic su **altri servizi**e quindi fare clic su **macchine virtuali** nell'elenco visualizzato.
3. Selezionare una macchina virtuale per risolvere i problemi nell'elenco visualizzato e viene visualizzata una pala macchine Virtuali con le opzioni.
4. Fare clic su **diagnosi & assistere** e quindi selezionare un problema comune. In questo esempio, **è possibile collegare le macchine Virtuali di Windows** sia selezionata. 

    ![](./media/virtual-network-routes-troubleshoot-portal/image1.png)

5. Passaggi appaiono sotto il problema, come illustrato nella figura seguente: 

    ![](./media/virtual-network-routes-troubleshoot-portal/image2.png)

    Fare clic su *route efficace* nell'elenco delle procedure consigliate.

6. Verrà visualizzata e **l'indirizza efficace** , come illustrato nella figura seguente:

    ![](./media/virtual-network-routes-troubleshoot-portal/image3.png)

    Se la macchina virtuale è solo una scheda di rete, sia selezionata per impostazione predefinita. Se si dispone di più NIC, selezionare la scheda di rete per il quale si desidera visualizzare le route efficace.

    >[AZURE.NOTE] Se la macchina virtuale associata con la scheda di rete non è in esecuzione, indirizza efficace non verrà visualizzato. Solo le prime 200 route effettive vengono visualizzate nel portale. Per un elenco completo, fare clic su **Scarica**. È possibile filtrare ulteriormente i risultati del file CSV scaricato.

    Si noti quanto segue nell'output:
    - **Origine**: indica il tipo di route. Indirizza sistema vengono visualizzati come *impostazione predefinita*, UDRs vengono visualizzati come *utente* e al gateway indirizza (statica o BGP) vengono visualizzati come *VPNGateway*.
    - **Stato**: indica lo stato del ciclo di efficaci. I valori possibili sono *attivi* o *non validi*.
    - **AddressPrefixes**: consente di specificare il prefisso della route efficace in notazione CIDR. 
    - **nextHopType**: indica l'hop successivo per instradare specificato. Valori possibili sono *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*o *Null*. Un valore *Null* per **nextHopType** in un UDR può indicare route non valida. Ad esempio, se **nextHopType** è *VirtualAppliance* e la rete virtuale accessorio macchine Virtuali non è stato effettuato il provisioning/esecuzione. Se **nextHopType** è *VPNGateway* e non esiste alcun gateway viene completato il provisioning/esecuzione in VNet specificato, il percorso potrebbe risultare non valido.
    
7. Non esiste nessuna route elencata per il VNet *WestUS VNET3* (prefisso 10.10.0.0/16) dalla *WestUS VNet1* (prefisso 10.9.0.0/16) nell'immagine nel passaggio precedente. Nell'immagine seguente viene visualizzato il collegamento di peering in stato *disconnesso* :
    
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    Il collegamento bidirezionale per il peering è errata, che spiega perché VM1 Impossibile connettersi al VM3 in VNet *WestUS VNet3* .

8. L'immagine seguente mostra le route dopo aver stabilito il collegamento di peering bidirezionale:

    ![](./media/virtual-network-routes-troubleshoot-portal/image5.png)

Per ulteriori scenari di risoluzione dei problemi per il tunneling forzata e instradare la valutazione, consultare la sezione [Considerazioni](virtual-network-routes-troubleshoot-portal.md#Considerations) di questo articolo.

### <a name="view-effective-routes-for-a-network-interface"></a>Visualizzazione route efficace per un'interfaccia di rete

Se traffico di rete impatto per una particolare interfaccia di rete (NIC), è possibile visualizzare un elenco completo di route efficace direttamente in una scheda di rete. Per visualizzare le route aggregazione vengono applicate a una scheda, completare la procedura seguente:

1. Accedere al portale di Azure all'https://portal.azure.com.
2. Fare clic su **altri servizi**e quindi fare clic su **interfacce di rete**
3. Ricerca nell'elenco per il nome di una scheda di rete o selezionarlo nell'elenco visualizzato. In questo esempio **VM1 NIC1** sia selezionata.
4. Selezionare **indirizza efficace** nell'e **interfaccia di rete** , come illustrato nella figura seguente:
   
    ![](./media/virtual-network-routes-troubleshoot-portal/image6.png)

    Impostazioni predefinite di **ambito** all'interfaccia di rete selezionata.

    ![](./media/virtual-network-routes-troubleshoot-portal/image7.png)


### <a name="view-effective-routes-for-a-route-table"></a>Visualizzazione route efficace per una tabella di routing

Quando si modificano indirizza definite dall'utente (UDRs) in una tabella di route, è necessario verificare l'impatto delle route si aggiunge una macchina virtuale specifica. Una tabella di route può essere associata a un numero qualsiasi di subnet. È ora possibile visualizzare tutte le route efficace per tutte le NIC applicata una tabella di route specificato, senza dover passare contesto da e tabella il percorso specificato.

In questo esempio viene specificato un UDR (*UDRoute*) in una tabella di route (*UDRouteTable*). Questa route invia tutto il traffico Internet da *Subnet1* in VNet *WestUS VNet1* tramite un rete virtuale accessorio (NVA) *Subnet2* di VNet stesso. Nella figura seguente è illustrata la route:

![](./media/virtual-network-routes-troubleshoot-portal/image8.png)

Per visualizzare le route aggregazione per una tabella di route, completare la procedura seguente:

1. Accedere al portale di Azure all'https://portal.azure.com.
2. Fare clic su **altri servizi**e quindi fare clic su **tabelle la distribuzione**
3. Ricerca nell'elenco per la tabella di route che si desidera vedere aggregazione route per e selezionarlo. In questo esempio, **UDRouteTable** sia selezionata. Viene visualizzata una pala per la tabella route selezionata, come illustrato nella figura seguente:

    ![](./media/virtual-network-routes-troubleshoot-portal/image9.png)

4. Selezionare **Indirizza efficace** e il **tabella Route** . L' **ambito** è impostata su tabella route selezionata.
5. Una tabella di route può essere applicata a più subnet. Selezionare la **Subnet** che si desidera esaminare dall'elenco. In questo esempio, **Subnet1** sia selezionata.
6. Selezionare un' **interfaccia di rete**. Sono elencate tutte le NIC connessione alla subnet selezionata. In questo esempio **VM1 NIC1** sia selezionata.

    ![](./media/virtual-network-routes-troubleshoot-portal/image10.png)

    >[AZURE.NOTE] Se la scheda non è associata a una macchina virtuale in esecuzione, non vengono visualizzati indirizza efficace.

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
    - Attendere alcuni secondi e riprovare se si tratta di un collegamento di peering appena definito. Occasionalmente richiede più tempo per propagarsi indirizza a tutte le interfacce di rete in una subnet.
    - Regole gruppo di sicurezza di rete (NSG) possono influire sui flussi di traffico. Per ulteriori informazioni, vedere l'articolo di [Risolvere i problemi di gruppi di sicurezza di rete](virtual-network-nsg-troubleshoot-portal.md) .
