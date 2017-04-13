<properties
   pageTitle="Implementare un'architettura di rete ibrido sicura in Azure | Microsoft Azure"
   description="Come implementare un'architettura di rete ibrido sicura in Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-dmz-between-azure-and-your-on-premises-datacenter"></a>L'implementazione di una rete Perimetrale tra Azure e la data center locale

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

In questo articolo vengono illustrati procedure consigliate per l'implementazione di una rete sicura ibrido che si estende di una rete locale in Azure. L'architettura di riferimento implementata una rete Perimetrale tra una rete locale e una rete virtuale Azure utilizzando route definite dall'utente (UDRs). La rete Perimetrale include accessori virtuale di rete disponibilità (NVAs) che implementano funzionalità di sicurezza, ad esempio firewall e ispezione pacchetti. Tutto il traffico in uscita dal VNet è tunnel vigore a Internet tramite la rete locale in modo che è possibile controllare. 

Questa architettura richiede una connessione per i data center locale implementato tramite un [gateway VPN][ra-vpn], o un [ExpressRoute] [ ra-expressroute] connessione.

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione: [Gestione risorse] [ resource-manager-overview] e classica. L'architettura di riferimento utilizza Manager delle risorse, Microsoft consiglia per le distribuzioni di nuove.

Casi di utilizzo tipico per questa architettura includono:

- Applicazioni ibride in carichi di lavoro Esegui parzialmente locale e in parte in Azure.

- Infrastruttura di Azure che indirizza il traffico in ingresso da locale e internet.

- Applicazioni necessarie per controllare il traffico in uscita. Spesso questo requisito alle normative di molti sistemi commerciali che consente di impedire la divulgazione pubblica delle informazioni personali.

## <a name="architecture-diagram"></a>Diagramma dell'architettura

Nel diagramma seguente evidenzia i componenti principali in questa architettura:

> Un documento di Visio che include questo diagramma architettura è disponibile per il download all' [area download Microsoft][visio-download]. Questo è nella pagina "Rete Perimetrale – privato".

[! [0]][0]

- **Rete locale.** Si tratta di una rete di computer e dispositivi la connessione tramite un privato rete locale implementata in un'organizzazione.

- **Azure virtuali (VNet).** Il VNet ospita l'applicazione e altre risorse in esecuzione nel cloud.

- **Gateway.** Il gateway fornisce la connettività tra router nella rete locale e il VNet.

- **Dispositivo virtuale rete (NVA).** NVA è un termine generico che descrive una macchina virtuale eseguono attività, ad esempio consentire o negare l'accesso come un firewall, ottimizzazione operazioni WAN (inclusa la compressione di rete), routing personalizzata o altre funzionalità di rete. 

- **Livello Web, business e subnet livello di dati.** Si tratta di subnet che ospita le macchine virtuali e i servizi che implementano un'applicazione di livello 3 di esempio in esecuzione nel cloud. Vedere [Macchine virtuali di Windows in esecuzione per un'architettura di livelli in Azure] [ ra-n-tier] per ulteriori informazioni.

- **Indirizza (UDR) definite dall'utente.** [Indirizza definite dall'utente] [ udr-overview] definire il flusso di traffico IP all'interno di Azure VNets. 

> [AZURE.NOTE] In base ai requisiti della connessione VPN, è possibile configurare route bordo Gateway Protocol (BGP) come alternativa eseguire all'utilizzo di UDRs per implementare le regole di inoltro che indirizzare il traffico attraverso la rete locale.

- **Subnet gestione.** Subnet contiene macchine virtuali che implementano funzionalità di gestione e monitoraggio per l'esecuzione di VNet dei componenti. 

## <a name="recommendations"></a>Consigli

Azure offre molte risorse diverse e tipi di risorse, in modo che l'architettura di riferimento può essere completato il provisioning molti modi diversi. Sono disponibili un modello di gestione di risorse Azure per installare l'architettura di riferimento che segue questi suggerimenti. Se si sceglie di creare il proprio architettura di riferimento è necessario seguire queste raccomandazioni, a meno che non si dispone di un requisito specifico non appartenente a un suggerimento.

### <a name="rbac-recommendations"></a>Consigli RBAC

Creare diversi ruoli RBAC per gestire le risorse dell'applicazione. Considerare la creazione di un [ruolo personalizzato] di attrezzi[ rbac-custom-roles] con le autorizzazioni per amministrare l'infrastruttura per l'applicazione. Considerare la creazione di un di amministratore IT centralizzata [ruolo personalizzato] [ rbac-custom-roles] per gestire le risorse di rete e un titolo separato amministratore IT [ruolo personalizzato] [ rbac-custom-roles] per gestire le risorse di rete protetta, ad esempio il NVAs. 

Il ruolo di attrezzi deve includere le autorizzazioni per la distribuzione dei componenti dell'applicazione, nonché i monitor e riavviare macchine virtuali. Il ruolo di amministratore IT centralizzato deve includere le autorizzazioni per eseguire il monitoraggio delle risorse di rete. Nessuno di questi ruoli devono avere accesso alle risorse NVA come deve essere limitata per la protezione ruolo di amministratore IT.

### <a name="resource-group-recommendations"></a>Suggerimenti di gruppo di risorse

Azure risorse, ad esempio macchine virtuali, VNets e servizi di bilanciamento del carico possono essere facilmente gestite raggruppandole in gruppi di risorse. È quindi possibile assegnare i ruoli sopra per ogni gruppo di risorse per limitare l'accesso.

È consigliabile la creazione delle operazioni seguenti:

- Gruppo di risorse contenente le subnet (escluse le macchine virtuali), NSGs e le risorse di gateway per la connessione alla rete locale. Assegnare il ruolo di amministratore IT centralizzato al gruppo di risorse.

- Gruppo di risorse contenente le macchine virtuali per NVAs (incluso il servizio di bilanciamento del carico), casella passa e altre macchine virtuali di gestione e UDR per la subnet gateway che forza tutto il traffico attraverso il NVAs. Assegnare il ruolo di amministratore IT di sicurezza a questo gruppo di risorse.

- Gruppi di risorse separato per ogni livello di applicazione che contengono il bilanciamento del carico e macchine virtuali. Si noti che questo gruppo di risorse non deve includere le subnet per ogni livello. Assegnare il ruolo di attrezzi al gruppo di risorse.

### <a name="virtual-network-gateway-recommendations"></a>Suggerimenti di gateway di rete virtuale

Il traffico locale passa il VNet tramite un gateway di rete virtuale. È consigliabile un [gateway VPN Azure] [ guidance-vpn-gateway] o un [gateway di Azure ExpressRoute][guidance-expressroute]. 

### <a name="nva-recommendations"></a>Consigli NVA

NVAs offrono diversi servizi per gestire e controllare il traffico di rete. Azure Marketplace offre diverse NVAs di terze parti, tra cui:

- [Barracuda Web applicazione Firewall] [ barracuda-waf] e [Barracuda NextGen Firewall][barracuda-nf]

- [Reti coerente VNS3 Firewall/Router/VPN][vns3]

- [Fortinet FortiGate-macchine Virtuali][fortinet]

- [Firewall di applicazione Web SecureSphere][securesphere]

- [Firewall di applicazione Web DenyAll][denyall]

- [Punto di controllo vSEC][checkpoint]

Se nessuna di queste NVAs di terze parti soddisfano i requisiti, è possibile creare un NVA personalizzato utilizzando macchine virtuali. Per un esempio di creazione NVAs personalizzato, vedere rete Perimetrale in questa architettura basata riferimento che consente le funzionalità seguenti:

- Il traffico viene instradato utilizzando [IP inoltro] [ ip-forwarding] nella NIC a.

- Il traffico consentito passano attraverso il NVA solo se si tratta appropriata per farlo. Ogni VM NVA nell'architettura di riferimento è un semplice router Linux con il traffico in ingresso in arrivo sulla rete interfaccia *eth0*e le regole di corrispondenza il traffico in uscita definite da script personalizzati inviato tramite l' interfaccia di rete *eth1*. 

- Il traffico indirizzato alla subnet gestione non passano attraverso il NVAs e il NVAs possono essere configurate solo dalla subnet gestione. Se il traffico alla subnet management è necessaria per essere instradati attraverso il NVAs, non esiste nessuna route alla subnet management per risolvere il NVAs se non si deve.  

- Macchine virtuali per il NVA sono incluse in una disponibilità impostare dietro un bilanciamento del carico. UDR subnet gateway indirizza le richieste di NVA bilanciamento del carico.

Un altro suggerimento per valutare la possibilità di connessione NVAs più serie con ogni NVA eseguire un'operazione di protezione specifico. In questo modo ogni funzione di protezione da gestire alla scala cronologica per NVA. Ad esempio un NVA implementazione di un firewall può trovarsi nella serie con un NVA che esegue servizi di identità. Il compromesso per semplificare la gestione è l'aggiunta di passaggi di rete aggiuntive che potrebbero incrementare la latenza, verificare che questa operazione non influisce su prestazioni dell'applicazione.

### <a name="nsg-recommendations"></a>Consigli NSG

Il gateway VPN espone un indirizzo IP pubblico per la connessione alla rete locale. È consigliabile creare un gruppo di sicurezza di rete (NSG) per le regole in entrata NVA subnet implementazione bloccare tutto il traffico non provenienti dalla rete locale.

È anche consigliabile implementare NSGs per ogni subnet per fornire un secondo livello di protezione contro il traffico in ingresso ignorare un NVA configurato in modo non corretto o disattivato. Ad esempio, alla subnet di livello web nell'architettura di riferimento implementata una NSG con una regola per ignorare tutte le richieste di diversi da quelli ricevuti dalla rete locale (192.168.0.0/16) o la VNet e un'altra regola che ignora tutte le richieste non effettuate sulla porta 80. 

### <a name="internet-access-recommendations"></a>Consigli accesso Internet

[Forza tunnel] [ azure-forced-tunneling] tutto il traffico in uscita a internet tramite la rete locale utilizzando il tunnel VPN da sito e traceroute a internet utilizzando rete indirizzo tranlation (NAT). Per entrambi impedire la diffusione accidentale di tutte le informazioni riservate memorizzate nel livello dati e anche ispezione e il controllo di tutto il traffico in uscita.

> [AZURE.NOTE] Non bloccare completamente il traffico Internet da livelli web, business e applicazione. Se questi livelli utilizzano i servizi di Azure PaaS che si basano su indirizzi IP per la registrazione diagnostica macchine Virtuali, scaricare delle estensioni macchine Virtuali e altre funzionalità. Diagnostica Windows Azure richiede anche che componenti possono leggere e scrivere a un account di archiviazione Azure dipendenti da internet.

Inoltre, è consigliabile verificare il traffico internet in uscita sia tunnel forza correttamente. Se si usa una connessione VPN con il [routing servizio e accesso remoto] [ routing-and-remote-access-service] su un server locale, usare uno strumento, ad esempio [WireShark] [ wireshark] o [Microsoft messaggio Analyzer](https://www.microsoft.com/en-us/download/details.aspx?id=44226).

### <a name="management-subnet-recommendations"></a>Suggerimenti per subnet la gestione

Subnet management contiene una casella ponticello che esegue funzionalità di gestione e monitoraggio. Implementare i seguenti suggerimenti per la casella ponticello:
- Non creare un indirizzo IP pubblico per la casella di spostamento. 
- Creare una route per accedere alla casella ponticello attraverso il gateway in ingresso e implementare un NSG subnet gestione a solo rispondere a richieste da route consentita.
- Limitare l'esecuzione di tutte le attività di gestione protetta nella casella collegamento.

### <a name="nva-recommendations"></a>Consigli NVA

Includere un livello 7 NVA terminare connessioni applicazione livello NVA e la manutenzione affinità con livelli back-end. In questo modo connettività simmetrica in cui il traffico di risposta da livelli back-end restituisce tramite la NVA.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Architettura di riferimento implementato un bilanciamento del carico indirizza il traffico di rete locale a un pool di dispositivi NVA. Come descritto in precedenza, i dispositivi NVA sono macchine virtuali di esecuzione delle regole di routing il traffico di rete e distribuiti in un [set di disponibilità][availability-set]. Questo modello consente di controllare la velocità della NVAs nel tempo e aggiungere dispositivi NVA in risposta a un aumento di carico.

Il gateway SKU VPN standard supporta velocità di fino a 100 MB/s. Alta SKU di prestazioni fornisce un massimo di 200 Mbps. Valutare l'aggiornamento a un gateway ExpressRoute larghezze di banda superiori. ExpressRoute offre la larghezza di banda fino a 10 GB/s con latenza inferiore rispetto a una connessione VPN.

> [AZURE.NOTE] Gli articoli [implementare un'architettura di rete ibrido con Azure e VPN locale] [ guidance-vpn-gateway] e [implementare un'architettura di rete ibrido con Azure ExpressRoute] [ guidance-expressroute] descritti i problemi che circonda la scalabilità dei gateway Azure.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Architettura di riferimento implementato un bilanciamento del carico distribuzione richieste da locale a un pool di dispositivi NVA in Azure. I dispositivi NVA sono macchine virtuali di esecuzione delle regole di routing il traffico di rete e distribuiti in un [set di disponibilità][availability-set]. Bilanciamento del carico regolarmente una verifica dell'integrità implementata in ogni NVA una query e rimuoverà qualsiasi NVAs non risponde del pool. 

Se si usa Azure ExpressRoute per fornire connettività di rete VNet e locale, [configurare un gateway VPN per fornire failover] [ guidance-vpn-failover] se la connessione di ExpressRoute non è più disponibile.

Per informazioni specifiche sul mantenere la disponibilità per le connessioni VPN ed ExpressRoute, vedere gli articoli [implementare un'architettura di rete ibrido con Azure e VPN locale] [ guidance-vpn-gateway] e [implementare un'architettura di rete ibrido con Azure ExpressRoute][guidance-expressroute].

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Tutte le applicazioni e il monitoraggio delle risorse deve essere eseguite selezionando la casella ponticello nella subnet gestione. A seconda dei requisiti dell'applicazione, potrebbe essere necessario aggiungere ulteriori risorse in Monitoraggio subnet gestione, ma una di queste risorse aggiuntive devono accedere di nuovo tramite la casella di spostamento.

Se la connettività di gateway tra la rete locale e Azure verso il basso, è comunque possibile contattare casella passa distribuendo un PIP, aggiungerlo alla casella passa e servizi remoti in da internet.

Subnet di ogni livello nell'architettura di riferimento è protetto tramite le regole NSG e potrebbe essere necessario creare una regola per aprire porta 3389 per l'accesso RDP in macchine virtuali di Windows o 22 per l'accesso SSH in macchine virtuali Linux. Altri strumenti di gestione e monitoraggio possono richiedere le regole per aprire porte aggiuntive.

Se si usa ExpressRoute per garantire la connettività tra il data center locale e Azure, utilizzare il [Toolkit di connettività di Azure (AzureCT)] [ azurect] monitoraggio e la risoluzione dei problemi di connessione.

> [AZURE.NOTE] È possibile trovare informazioni aggiuntive specificamente per la gestione delle connessioni VPN ed ExpressRoute negli articoli di [implementare un'architettura di rete ibrido con Azure e VPN locale] e il monitoraggio[ guidance-vpn-gateway] e [implementare un'architettura di rete ibrido con Azure ExpressRoute][guidance-expressroute].

## <a name="security-considerations"></a>Considerazioni sulla protezione

L'architettura di riferimento consente a più livelli di sicurezza: 

### <a name="routing-all-on-premises-user-requests-through-the-nva"></a>Tutte le richieste di utente locale attraverso la NVA di routing

UDR subnet gateway blocca tutte le richieste di utente diverse da quelle locali. Passate UDR consentito richieste per il NVAs in privato subnet di rete Perimetrale e le richieste vengono passate all'applicazione se sono consentite da regole NVA. Altre route possono essere aggiunto al UDR, ma assicurarsi che inavvertitamente non ignorare la NVAs o bloccare il traffico amministrativi destinati alla subnet gestione.

Bilanciamento del carico che precede il NVAs funge anche da un dispositivo di protezione ignorando il traffico sulle porte che non siano aperte in regole di bilanciamento del carico. I servizi di bilanciamento del carico nell'architettura di riferimento ascoltare solo per le richieste HTTP sulla porta 80 e le richieste HTTPS sulla porta 443. Devono essere descritte le regole aggiuntive per i servizi di bilanciamento del carico e monitorare il traffico per assicurarsi che non esistono problemi sicurezza.

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a>Utilizzo di NSGs per il traffico in blocco/passaggio tra livelli dell'applicazione

Ognuno dei livelli web, business e dati limitare il traffico tra di esse utilizzando NSGs. Vale a dire il livello di business utilizza un NSG per bloccare tutto il traffico che non hanno origine nel livello web e il livello di dati utilizza un NSG per bloccare tutto il traffico che non hanno origine nel livello business. Se si dispone di un requisito per espandere le regole NSG per consentire l'accesso più ampio a questi livelli, valutare questi requisiti contro i rischi di sicurezza. Ogni nuovo percorso in ingresso rappresenta un'opportunità per i dati di danni accidentali o intenzionali perdita o dell'applicazione danni.

### <a name="devops-access"></a>Accesso attrezzi

Limitare le operazioni consentite a ogni livello utilizzando [RBAC] attrezzi[ rbac] per gestire le autorizzazioni. Quando si concedono le autorizzazioni, utilizzare il [principio dei privilegi minimi][security-principle-of-least-privilege]. Registrare tutte le operazioni amministrative ed effettuare controlli regolari per pianificare le modifiche di configurazione sono state.

> [AZURE.NOTE] Per maggiori informazioni, esempi e scenari sulla gestione della protezione di rete con Azure, vedere [servizi cloud Microsoft e la sicurezza della rete][cloud-services-network-security]. Per informazioni dettagliate sulla protezione risorse nel cloud, vedere [Guida introduttiva a Microsoft Azure sicurezza][getting-started-with-azure-security]. Per ulteriori informazioni su problemi di protezione tramite una connessione di gateway Azure, vedere [l'implementazione di un'architettura di rete ibrido con Azure e VPN locale] [ guidance-vpn-gateway] e [implementare un'architettura di rete ibrido con Azure ExpressRoute][guidance-expressroute].

## <a name="solution-deployment"></a>Distribuzione di soluzioni

Una distribuzione per un'architettura di riferimento che implementa queste raccomandazioni è disponibile in Github. L'architettura di riferimento include una rete virtuale (VNet), gruppo di sicurezza di rete (NSG), bilanciamento del carico e due macchine ().

È possibile distribuire l'architettura di riferimento con Windows o macchine virtuali Linux seguendo le indicazioni seguenti: 

1. Fare clic con il pulsante destro sul pulsante e selezionare uno dei due "Apri collegamento in una nuova scheda" o "Apri collegamento in un'altra finestra":  
[![Distribuire Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet%2Fazuredeploy.json)

2. Una volta aperto il collegamento nel portale di Azure, è necessario immettere valori per alcune delle impostazioni: 
    - Il nome del **gruppo di risorse** è già definito nel file di parametri, quindi selezionare **Crea nuovo** e immettere `ra-private-dmz-rg` nella casella di testo.
    - Selezionare l'area nella casella a discesa **posizione** .
    - Non modificare il **Modello radice Uri** o caselle di testo **Parametro radice Uri** .
    - Esaminare le condizioni e quindi fare clic sulla casella di controllo **che accetto i termini e condizioni indicate in precedenza** .
    - Fare clic sul pulsante di **acquisto** .

3. Attendere che la distribuzione completare.

4. Si consiglia di immediatamente modificare entrambi i file di parametro includono hardcoded nome utente e password per tutte le macchine virtuali. Per ogni macchine Virtuali nella distribuzione, selezionarlo nel portale di Azure e quindi fare clic su **Reimposta password** e il **supporto + risoluzione dei problemi** . Selezionare **reimpostare la password** nella casella a discesa **modalità** , quindi selezionare un nuovo **nome utente** e **Password**. Fare clic sul pulsante **Aggiorna** per mantenere.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come implementare una [rete Perimetrale tra Azure e Internet](./guidance-iaas-ra-secure-vnet-dmz.md).
- Informazioni su come implementare un' [architettura di rete ibrido disponibilità](./guidance-hybrid-network-expressroute-vpn-failover.md).

<!-- links -->

[availability-set]: ../virtual-machines/virtual-machines-windows-create-availability-set.md
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[azure-forced-tunneling]: https://azure.microsoft.com/en-gb/documentation/articles/vpn-gateway-forced-tunneling-rm/
[barracuda-nf]: https://azure.microsoft.com/marketplace/partners/barracudanetworks/barracuda-ng-firewall/
[barracuda-waf]: https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf/
[checkpoint]: https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/
[cloud-services-network-security]: https://azure.microsoft.com/documentation/articles/best-practices-network-security/
[denyall]: https://azure.microsoft.com/marketplace/partners/denyall/denyall-web-application-firewall/
[fortinet]: https://azure.microsoft.com/marketplace/partners/fortinet/fortinet-fortigate-singlevmfortigate-singlevm/
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[guidance-expressroute]: ./guidance-hybrid-network-expressroute.md
[guidance-vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[ip-forwarding]: ../virtual-network/virtual-networks-udr-overview.md#IP-forwarding
[ra-expressroute]: ./guidance-hybrid-network-expressroute.md
[ra-n-tier]: ./guidance-compute-n-tier-vm.md
[ra-vpn]: ./guidance-hybrid-network-vpn.md
[ra-vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[rbac]: ../active-directory/role-based-access-control-configure.md
[rbac-custom-roles]: ../active-directory/role-based-access-control-custom-roles.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[routing-and-remote-access-service]: https://technet.microsoft.com/library/dd469790(v=ws.11).aspx
[securesphere]: https://azure.microsoft.com/marketplace/partners/imperva/securesphere-waf-for-azr/
[security-principle-of-least-privilege]: https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1
[udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vns3]: https://azure.microsoft.com/marketplace/partners/cohesive/cohesiveft-vns3-for-azure/
[wireshark]: https://www.wireshark.org/
[0]: ./media/blueprints/hybrid-network-secure-vnet.png "Architettura della rete ibrido protetta"
