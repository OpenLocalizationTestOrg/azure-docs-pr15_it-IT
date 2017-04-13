<properties 
   pageTitle="Connessione ibrido con applicazioni di livello 2 | Microsoft Azure"
   description="Informazioni su come distribuire dispositivi di rete e UDR per creare un ambiente di applicazione a più livelli in Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/05/2016"
   ms.author="jdial" />

# <a name="virtual-appliance-scenario"></a>Scenario relativo al dispositivo virtuale

Scenari comuni tra più grande cliente Azure sono necessario fornire un'applicazione a due livelli esposta a Internet, mentre si consente l'accesso al livello indietro da un data center locale. In questo documento mostrerà uno scenario utilizzando l'utente definito indirizza (UDR), un Gateway VPN e rete dispositivi di rete per distribuire un ambiente di due livelli che soddisfi i requisiti seguenti:

- Applicazione Web deve essere accessibili da Internet pubblica.
- Server Web che ospita l'applicazione deve essere in grado di accedere a un server di back-end dell'applicazione.
- Tutto il traffico da Internet all'applicazione web necessario utilizzare un dispositivo virtuale firewall. Questo dispositivo virtuale verrà utilizzata per il traffico Internet.
- Tutto il traffico passando al server dell'applicazione deve passare attraverso un dispositivo virtuale firewall. Questo dispositivo virtuale verrà utilizzato per l'accesso al server end back-end e accesso provenienti dalla rete locale tramite un Gateway VPN.
- Gli amministratori devono essere in grado di gestire i dispositivi di rete i firewall dal computer locale, utilizzando una terza dispositivo virtuale utilizzato esclusivamente ai fini della gestione del firewall.

Si tratta di uno scenario di rete Perimetrale standard con una rete Perimetrale e una rete protetta. Scenario può essere creato nel Azure mediante NSGs e firewall dispositivi di rete o una combinazione di entrambi. Nella tabella seguente vengono illustrate alcune Pro e contro tra NSGs e firewall dispositivi di rete.

||Professionisti IT|Contro|
|---|---|---|
|NSG|Nessun costo. <br/>Integrare RBAC Azure. <br/>Le regole possono essere create in modelli ARM.|Complessità può variare in ambienti di grandi dimensioni. |
|Firewall|Controllo completo sul piano di dati. <br/>Gestione centrale tramite console firewall.|Costo del firewall semplice. <br/>Non è integrato con Azure RBAC.|

La soluzione seguente utilizza firewall dispositivi di rete per implementare uno scenario di rete rete Perimetrale/protetto.

## <a name="considerations"></a>Considerazioni

È possibile distribuire l'ambiente illustrato sopra Azure usando diverse caratteristiche disponibili oggi, come indicato di seguito.

- **Rete virtuale (VNet)**. Un VNet Azure funziona in modo simile a una rete locale e può essere segmentata in una o più subnet per fornire il traffico isolare e separare dei problemi.
- **Accessorio virtuale**. Partner diverse offrono dispositivi di rete in Azure Marketplace che può essere utilizzato per i tre firewall descritti in precedenza. 
- **Indirizza (UDR) definite dall'utente**. La distribuzione tabelle possono contenere UDRs utilizzato da Azure rete per controllare il flusso di pacchetti all'interno di un VNet. Le tabelle seguenti includono route possono essere applicate a subnet. Una delle caratteristiche più recenti di Azure è la possibilità di applicare una tabella di route a GatewaySubnet, consente di inoltrare tutto il traffico a essere recapitata in VNet Azure da una connessione ibrido in un dispositivo virtuale.
- **Inoltro IP**. Per impostazione predefinita, il motore di rete Azure inoltrare pacchetti a schede di rete virtuale (NIC) solo se l'indirizzo IP di destinazione pacchetto corrisponda all'indirizzo IP NIC. Pertanto, se un UDR definisce un apparecchio virtuale deve essere inviato un pacchetto, il motore di rete Azure da eliminare tale pacchetto. Per garantire che il pacchetto viene recapitato a una macchina virtuale (in questo caso un dispositivo virtuale) non è la destinazione effettiva per il pacchetto, è necessario attivare inoltro IP per il dispositivo virtuale.
- **Gruppi di sicurezza di rete (NSGs)**. Nell'esempio seguente non avvalersi delle NSGs, ma NSGs applicati alla subnet e/o NIC in questa soluzione è possibile utilizzare per filtrare ulteriormente il traffico da e verso tali subnet e schede di rete.


![Connettività IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

In questo esempio è un abbonamento che contiene le operazioni seguenti:

- 2 gruppi di risorse, non è visualizzati nel diagramma. 
    - **ONPREMRG**. Contiene tutte le risorse necessarie per simulare una rete locale.
    - **AZURERG**. Contiene tutte le risorse necessarie per l'ambiente di rete virtuale Azure. 
- VNet denominato **onpremvnet** utilizzato per simulare un data center locale segmentato come indicato di seguito.
    - **onpremsn1**. Subnet contenente una macchine () in esecuzione Ubuntu per simulare un server locale.
    - **onpremsn2**. Subnet contenente una macchina virtuale in esecuzione Ubuntu per simulare un computer locale utilizzato da un amministratore.
- Esiste un firewall virtuale semplice denominato **OPFW** in **onpremvnet** utilizzato per mantenere un tunnel a **azurevnet**.
- Un VNet denominato **azurevnet** segmentato come indicato di seguito.
    - **azsn1**. Subnet firewall esterno utilizzato esclusivamente per il firewall esterno. Tutto il traffico Internet venga recapitata tramite questa subnet. Subnet contiene solo una scheda di rete collegata il firewall esterno.
    - **azsn2**. Subnet front-end che ospita una macchina virtuale in esecuzione come un server web che sarà possibile accedere da Internet.
    - **azsn3**. Subnet back-end che ospita una macchina virtuale che esegue un server di back-end applicazioni accessibili dal server web front-end.
    - **azsn4**. Subnet gestione utilizzato esclusivamente per fornire l'accesso a tutti i dispositivi virtuale firewall gestione. Subnet contiene solo una scheda di rete per ogni dispositivo virtuale firewall utilizzata nella soluzione.
    - **GatewaySubnet**. Subnet connessione ibrido Azure necessari per ExpressRoute e Gateway VPN per fornire la connettività tra Azure VNets e altre reti. 
- Sono disponibili 3 firewall dispositivi di rete nella rete **azurevnet** . 
    - **AZF1**. Firewall esterno esposto a Internet utilizzando una risorsa indirizzo IP pubblica in Azure. È necessario avere a disposizione un modello di Marketplace o direttamente dal produttore del dispositivo, tale disposizioni un dispositivo virtuale di 3-NIC.
    - **AZF2**. Consente di indicare il traffico tra **azsn2** e **azsn3**firewall interno. Questo è un dispositivo virtuale di 3-NIC.
    - **AZF3**. Firewall di gestione accessibili per gli amministratori dal Data Center locale e connessi a una subnet gestione usata per gestire tutti i dispositivi firewall. È possibile trovare 2 NIC modelli di dispositivo virtuale sul mercato o richiedere una direttamente dal produttore del dispositivo.

## <a name="user-defined-routing-udr"></a>Routing (UDR) definite dall'utente

Ciascuna subnet di Azure può essere collegato a una tabella UDR consente di definire come il traffico avviata in subnet è stato distribuito. Se non UDRs sono definiti, Azure utilizza route predefinite per consentire il traffico da una subnet a un altro. Per comprendere meglio UDRs, vedere [informazioni sui percorsi definiti dall'utente e indirizzi IP di inoltro](./virtual-networks-udr-overview.md#ip-forwarding).

Per garantire la comunicazione viene eseguita tramite l'accessorio firewall destra, in base a questo requisito ultimo riportati sopra, è necessario creare route nella tabella che segue UDRs in **azurevnet**.

### <a name="azgwudr"></a>azgwudr

In questo scenario, il traffico solo locali in Azure verrà usato per gestire i firewall connettendosi a **AZF3**e che il traffico deve passare attraverso il firewall interno, **AZF2**. Di conseguenza, un solo percorso è necessario in **GatewaySubnet** come illustrato di seguito.

|Destinazione|Hop successivo|Spiegazione|
|---|---|---|
|10.0.4.0/24|10.0.3.11|Consente il traffico locale raggiungere il firewall di gestione **AZF3**|

### <a name="azsn2udr"></a>azsn2udr

|Destinazione|Hop successivo|Spiegazione|
|---|---|---|
|10.0.3.0/24|10.0.2.11|Consente il traffico alla subnet back-end che ospita il server di applicazione tramite **AZF2**|
|0.0.0.0/0|10.0.2.10|Consente di tutto il traffico devono essere instradati attraverso **AZF1**|

### <a name="azsn3udr"></a>azsn3udr

|Destinazione|Hop successivo|Spiegazione|
|---|---|---|
|10.0.2.0/24|10.0.3.10|Consente il traffico **azsn2** al flusso dal server app al server Web tramite **AZF2**|

È anche necessario creare tabelle route ad subnet in **onpremvnet** per simulare la data center locale.

### <a name="onpremsn1udr"></a>onpremsn1udr

|Destinazione|Hop successivo|Spiegazione|
|---|---|---|
|192.168.2.0/24|192.168.1.4|Consente il traffico **onpremsn2** tramite **OPFW**|

### <a name="onpremsn2udr"></a>onpremsn2udr

|Destinazione|Hop successivo|Spiegazione|
|---|---|---|
|10.0.3.0/24|192.168.2.4|Consente il traffico alla subnet backup in Azure tramite **OPFW**|
|192.168.1.0/24|192.168.2.4|Consente il traffico **onpremsn1** tramite **OPFW**|

## <a name="ip-forwarding"></a>Inoltro IP 

UDR e inoltro IP sono caratteristiche che è possibile usare in combinazione per consentire a dispositivi di rete da utilizzare per controllare il flusso di traffico in un VNet Azure.  Un dispositivo virtuale è semplicemente una macchina virtuale che viene eseguita in un'applicazione per gestire il traffico di rete in alcuni casi, ad esempio un firewall o un dispositivo NAT.

Questo dispositivo virtuale macchine Virtuali devono essere in grado di ricevere il traffico in ingresso non indirizzati a se stesso. Per consentire una macchina virtuale per ricevere il traffico indirizzato ad altre destinazioni, è necessario abilitare inoltro IP per la macchina virtuale. Si tratta di un Azure impostazione, non nel sistema operativo guest. Il dispositivo virtuale ancora deve essere eseguito qualche tipo di applicazione per gestire il traffico in arrivo e inviarlo in modo appropriato.

Per altre informazioni sull'inoltro IP, vedere [informazioni sui percorsi definiti dall'utente e indirizzi IP di inoltro](./virtual-networks-udr-overview.md#ip-forwarding).

Ad esempio, immaginare che un vnet Azure sono presenti le seguenti impostazioni:

- Subnet **onpremsn1** contiene una macchina virtuale denominata **onpremvm1**.
- Subnet **onpremsn2** contiene una macchina virtuale denominata **onpremvm2**.
- Un dispositivo virtuale denominato **OPFW** sia connesso a **onpremsn1** e **onpremsn2**.
- Una route definite dall'utente collegata a **onpremsn1** specifica che tutto il traffico verso **onpremsn2** deve essere inviato al **OPFW**.

A questo punto, se **onpremvm1** tenta di stabilire una connessione con **onpremvm2**, verrà utilizzato il UDR e il traffico verrà inviato all' **OPFW** come hop successivo. Tenere presente che la destinazione del pacchetto effettivo non viene modificata, verrà comunque visualizzato il numero **onpremvm2** rappresenta la destinazione. 

Senza IP inoltro abilitato per **OPFW**, la logica di rete virtuale Azure eliminerà i pacchetti, poiché consente solo i pacchetti devono essere inviati una macchina virtuale se indirizzo IP di Virtual Machine destinazione per il pacchetto.

Con l'inoltro IP, la logica di Azure virtuali inoltra i pacchetti a OPFW, senza cambiare il proprio indirizzo di destinazione originale. **OPFW** necessario gestire i pacchetti e determinare cosa fare con loro.

Per lo scenario sopra per lavorare, è necessario abilitare inoltro IP sulle schede NIC per **OPFW**, **AZF1**, **AZF2**e **AZF3** utilizzati per il routing (tutte le NIC ad eccezione di quelli collegati alla subnet gestione). 

## <a name="firewall-rules"></a>Regole firewall

Come descritto in precedenza, IP Forwarding solo garantisce pacchetti vengono inviati per i dispositivi di rete. Il dispositivo deve ancora decidere come procedere con i pacchetti. Nello scenario, è necessario creare le regole seguenti l'accessori:

### <a name="opfw"></a>OPFW

OPFW rappresenta un dispositivo locale contenente le regole seguenti:

- **La distribuzione**: tutto il traffico verso 10.0.0.0/16 (**azurevnet**) deve essere inviato tramite tunnel **ONPREMAZURE**.
- **Criteri**: consentire il traffico bidirezionale tra **port2** e **ONPREMAZURE**.
 
### <a name="azf1"></a>AZF1

AZF1 rappresenta un dispositivo virtuale Azure contenente le regole seguenti:

- **Criteri**: consentire il traffico bidirezionale tra **port1** e **port2**.

### <a name="azf2"></a>AZF2

AZF2 rappresenta un dispositivo virtuale Azure contenente le regole seguenti:

- **La distribuzione**: tutto il traffico verso 10.0.0.0/16 (**onpremvnet**) deve essere inviato all'indirizzo IP del gateway Azure (ad esempio 10.0.0.1) tramite **port1**.
- **Criteri**: consentire il traffico bidirezionale tra **port1** e **port2**.

## <a name="network-security-groups-nsgs"></a>Gruppi di sicurezza di rete (NSGs)

In questo scenario, NSGs non in uso. Tuttavia, è possibile applicare NSGs a ogni subnet per limitare il traffico in ingresso e in uscita. Ad esempio, è possibile applicare le regole seguenti NSG alla subnet come esterne.

**Posta in arrivo**

- Consentire il traffico TCP da Internet alla porta 80 in qualsiasi macchine Virtuali nella subnet.
- Impedire tutto il traffico Internet.

**In uscita**
- Per tutto il traffico a Internet.

## <a name="high-level-steps"></a>Passaggi di livello elevati

Per distribuire questo scenario, seguire questi passaggi di alto livelli.

1.  Accedere al proprio abbonamento Azure.
2.  Se si desidera distribuire un VNet per simulare la rete locale, effettuare il provisioning di risorse che fanno parte di **ONPREMRG**.
3.  Effettuare il provisioning di risorse che fanno parte di **AZURERG**.
4.  Eseguire il provisioning del tunnel **onpremvnet** a **azurevnet**.
5.  Quando vengono effettuato il provisioning di tutte le risorse, accedere a **onpremvm2** ed effettuare il ping 10.0.3.101 per testare la connettività tra **onpremsn2** e **azsn3**.
