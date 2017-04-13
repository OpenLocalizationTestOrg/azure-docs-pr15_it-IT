<properties 
   pageTitle="Risolvere i problemi di gruppi di sicurezza di rete - portale | Microsoft Azure"
   description="Informazioni su come risolvere i problemi di gruppi di sicurezza di rete nel modello di distribuzione Manager delle risorse Azure tramite il portale di Azure."
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

# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Risolvere i problemi di gruppi di sicurezza di rete tramite il portale di Azure

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

### <a name="view-effective-security-rules-for-a-virtual-machine"></a>Visualizza le regole di protezione efficace per una macchina virtuale

Completare la procedura seguente per risolvere i problemi NSGs per una macchina virtuale:

È possibile visualizzare l'elenco completo delle regole di protezione efficace in una scheda di rete, da macchine Virtuali stesso. È anche possibile aggiungere, modificare ed eliminare regole NSG NIC e subnet da e il regole efficace, se si dispone delle autorizzazioni per eseguire queste operazioni.

1. Accedere al portale di Azure all'https://portal.azure.com.
2. Fare clic su **altri servizi**e quindi fare clic su **macchine virtuali** nell'elenco visualizzato.
3. Selezionare una macchina virtuale per risolvere i problemi nell'elenco visualizzato e viene visualizzata una pala macchine Virtuali con le opzioni.
4. Fare clic su **diagnosi & assistere** e quindi selezionare un problema comune. In questo esempio, **è possibile collegare le macchine Virtuali di Windows** sia selezionata. 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)

5. Passaggi appaiono sotto il problema, come illustrato nella figura seguente: 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)

    Fare clic su *Regole gruppo di sicurezza efficace* nell'elenco delle procedure consigliate.

6. Verrà visualizzata e **l'ottenere le regole di protezione efficace** , come illustrato nella figura seguente:

    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)

    Notare le sezioni seguenti dell'immagine:

    - **Ambito:** Impostare su *VM1*, la macchina virtuale selezionata nel passaggio 3.
    - **Interfaccia di rete:** *VM1 NIC1* sia selezionata. Una macchina virtuale può contenere più interfacce di rete (NIC). Schede di rete può avere regole di protezione efficace univoco. Per risolvere il problema, potrebbe essere necessario visualizzare le regole di protezione efficace per ciascuna NIC.
    - **Associata NSGs:** La scheda di rete e subnet che la scheda di rete è connesso, è possibile utilizzare NSGs. Nella figura, un NSG è stato applicato per la scheda di rete e subnet a che sia connesso. È possibile fare clic sui nomi NSG modificare direttamente le regole nel NSGs.
    - **Scheda VM1 nsg:** L'elenco di regole visualizzate nell'immagine è per NSG applicata alla scheda. Più regole predefinita vengono create da Azure ogni volta che viene creato un NSG. Non è possibile rimuovere le regole predefinite, ma è possibile eseguirne con regole di priorità più alta. Per ulteriori informazioni sulle regole di impostazione predefinita, leggere l'articolo [Panoramica NSG](virtual-networks-nsg.md#default-rules) .
    - **Colonna di destinazione:** Alcune delle regole è associato un testo nella colonna, mentre gli altri utenti hanno prefissi degli indirizzi. Il testo è il nome di tag predefinito applicato alla regola di sicurezza quando è stata creata. I contrassegni sono forniti dal sistema identificatori che rappresentano più prefissi. Selezione di una regola con un tag, ad esempio *AllowInternetOutBound*, sono elencati i prefissi e il **prefissi degli indirizzi** .
    - **Download:** L'elenco delle regole può essere lungo. È possibile scaricare un file csv delle regole per l'analisi non in linea facendo clic su **Download** e il salvataggio del file.
    - **AllowRDP** Regola in entrata: questa regola consente connessioni RDP a macchina virtuale.
7. Fare clic sulla scheda **Subnet1 NSG** per visualizzare le regole efficace da NSG applicati alla subnet, come illustrato nella figura seguente: 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)

    Si noti la regola **in entrata** *denyRDP* . Le regole in entrata applicate alla subnet vengono valutate prima delle regole applicate a interfaccia di rete. Poiché la regola viene applicata alla subnet, la richiesta per connettersi a TCP 3389 ha esito negativo, poiché non viene mai valutata la regola allow nella scheda. 

    La regola *denyRDP* è il motivo perché la connessione RDP ha esito negativo. Per risolvere il problema, rimuoverlo.

    >[AZURE.NOTE]Se la macchina virtuale associata con la scheda di rete non è in esecuzione, o NSGs non sono state applicate al NIC o alla subnet, non vengono visualizzate regole.

8. Per modificare le regole NSG, fare clic su *Subnet1 NSG* nella sezione **NSGs associato** .
   Verrà aperta e il **Subnet1 NSG** . È possibile modificare le regole direttamente facendo clic su **regole di protezione in ingresso**.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)

9. Dopo avere rimosso la regola in entrata *denyRDP* in **Subnet1 NSG** e aggiunta di una regola *allowRDP* , l'elenco di regole efficace è simile all'immagine seguente:

    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)

    Verificare che la porta TCP 3389 sia aperta aprendo una connessione RDP verso la macchina virtuale o tramite lo strumento PsPing. Si può acquisiscono informazioni sul PsPing, leggere la [pagina di download PsPing](https://technet.microsoft.com/sysinternals/psping.aspx).

### <a name="view-effective-security-rules-for-a-network-interface"></a>Visualizza le regole di protezione valide per un'interfaccia di rete

Se il flusso di traffico macchine Virtuali impatto per una scheda specifica, è possibile visualizzare un elenco completo delle regole efficaci per la scheda nel contesto di interfacce di rete, completando la procedura seguente:

1. Accedere al portale di Azure all'https://portal.azure.com.
2. Fare clic su **altri servizi**e quindi fare clic su **interfacce di rete** nell'elenco visualizzato.
3. Selezionare un'interfaccia di rete. Nella figura seguente è selezionata una scheda di rete denominato *VM1 NIC1* .

    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)

    Si noti che l' **ambito** è impostato per l'interfaccia di rete selezionata. Per ulteriori informazioni sulle informazioni aggiuntive visualizzate, leggere passaggio 6 della sezione **NSGs risoluzione dei problemi per una macchina virtuale** di questo articolo.

    >[AZURE.NOTE] Se un NSG viene rimosso dall'interfaccia di rete, alla subnet NSG sono ancora efficaci sulla scheda NIC specificato. In questo caso, l'output da visualizzare soltanto le regole da subnet NSG. Le regole vengono visualizzate solo se la scheda di rete è associato a una macchina virtuale.

4. È possibile modificare direttamente le regole per NSGs associato a una scheda di rete e una subnet. Per informazioni, vedere passaggio 8 della sezione **Visualizza regole di protezione efficace per una macchina virtuale** di questo articolo.

## <a name="view-effective-security-rules-for-a-network-security-group-nsg"></a>Visualizza le regole di protezione efficace per un gruppo di sicurezza di rete (NSG)

Quando si modificano le regole NSG, è necessario verificare l'impatto delle regole si aggiunge una macchina virtuale specifica. È possibile visualizzare un elenco completo delle regole di protezione efficace per tutte le NIC che un determinato NSG viene applicata, senza dover passare contesto da e il NSG specificato. Per risolvere i problemi efficace regole all'interno di un NSG, completare la procedura seguente:

1. Accedere al portale di Azure all'https://portal.azure.com.
2. Fare clic su **altri servizi**e quindi fare clic su **gruppi di sicurezza di rete** nell'elenco visualizzato.
3. Selezionare un NSG. Nella figura seguente è stato selezionato un NSG denominato VM1 nsg.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)

    Notare le sezioni seguenti dell'immagine precedente:

    - **Ambito:** Impostare su NSG selezionata.
    - **Macchina virtuale:** Quando un NSG viene applicata a una subnet, viene applicata a tutte le interfacce di rete associate a tutte le macchine virtuali connesse alla subnet. Questo elenco Mostra tutte le macchine virtuali questo NSG viene applicata a. È possibile selezionare qualsiasi macchina virtuale dall'elenco.

    >[AZURE.NOTE] Se un NSG viene applicata a una vuota subnet, macchine virtuali non essere elencate. Se un NSG viene applicato a una scheda di rete che non è associato a una macchina virtuale, tali NIC non essere elencate anche. 
    - **Interfaccia di rete:** Una macchina virtuale può contenere più interfacce di rete. È possibile selezionare un'interfaccia di rete associata a macchina virtuale selezionata.
    - **AssociatedNSGs:** In qualsiasi momento, una scheda di rete può includere fino a due NSGs efficace, quella applicata alla scheda di rete e l'altro alla subnet. Anche se l'ambito è selezionato come VM1 nsg, se la scheda di rete ha una subnet efficace NSG, l'output sarà visualizzati entrambi NSGs.
4. È possibile modificare direttamente le regole per NSGs associato a una scheda di rete o subnet. Per informazioni, vedere passaggio 8 della sezione **Visualizza regole di protezione efficace per una macchina virtuale** di questo articolo.

Per ulteriori informazioni sulle informazioni aggiuntive visualizzate, leggere passaggio 6 della sezione **Visualizza regole di protezione efficace per una macchina virtuale** di questo articolo.

>[AZURE.NOTE] Se una subnet e NIC possono disporre sola applicate a tali proprietà NSG, un NSG può essere associato a più schede di rete e più subnet.

## <a name="considerations"></a>Considerazioni

Per la risoluzione dei problemi di connettività, tenere presente quanto segue:

- Regole NSG predefinite verranno bloccare l'accesso in ingresso da internet e consentire solo VNet il traffico in ingresso. Regole devono essere aggiunto in modo esplicito per consentire l'accesso in ingresso da Internet, in base alle esigenze.
- Se non esistono regole sicurezza NSG causa la connettività di rete una Virtual Machine per avere esito negativo, il problema potrebbe essere dovuto a:
    - Software firewall in esecuzione all'interno del sistema operativo della macchina virtuale
    - Indirizza configurato per il traffico locale o dispositivi di rete. Nella distribuzione locale tramite il tunneling forzata, è possibile reindirizzare il traffico Internet. Una connessione RDP/SSH da Internet per la macchina virtuale potrebbe non funzionare con questa impostazione, a seconda di come hardware di rete locale gestisce il traffico. Leggere l'articolo [Cicli di risoluzione dei problemi](virtual-network-routes-troubleshoot-powershell.md) per imparare a diagnosticare i problemi di route che possono essere impedendo il flusso di traffico da e verso la macchina virtuale. 
- Se si dispone di peered VNets, per impostazione predefinita, il contrassegno VIRTUAL_NETWORK verrà espanso automaticamente per includere prefissi VNets peered. È possibile visualizzare tali prefissi nell'elenco **ExpandedAddressPrefix** per la risoluzione dei problemi relativi alla connettività peering VNet. 
- Regole di protezione effettiva vengono visualizzate solo se è presente un NSG associato la macchina virtuale NIC e o subnet. 
- Se sono non presenti NSGs associato con la scheda di rete o subnet e si dispone di un indirizzo IP pubblico assegnato alle macchine Virtuali, tutte le porte sarà aperte per l'accesso in ingresso e in uscita. Se la macchina virtuale ha un indirizzo IP pubblico, l'applicazione NSGs al NIC o alla subnet è consigliabile.