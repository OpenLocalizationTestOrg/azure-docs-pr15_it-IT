<properties
   pageTitle="Esempio di rete Perimetrale: creare una rete Perimetrale per proteggere le reti con un Firewall, UDR e NSG | Microsoft Azure"
   description="Creare una rete Perimetrale con un Firewall, definite dall'utente Routing (UDR) e gruppi di sicurezza di rete (NSG)"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Esempio 3: creare una rete Perimetrale per proteggere le reti con un Firewall, UDR e NSG

[Tornare alla sicurezza limite Best Practices pagina][HOME]

In questo esempio verrà creata una rete Perimetrale con un firewall, quattro server windows, il Routing definite dall'utente, IP inoltro e gruppi di sicurezza di rete. Inoltre consentono al ciascuno dei comandi pertinenti per fornire un approfondimento di ogni passaggio. È inoltre disponibile una sezione di uno Scenario di traffico per fornire un'analisi approfondita dettagliate come il traffico procede i livelli di difesa a più livelli di rete Perimetrale. Infine, nei riferimenti di sezione è il codice completo e istruzioni per creare l'ambiente per testare e sperimentare diversi scenari. 

![Rete Perimetrale bidirezionale con NVA NSG e UDR][1]

## <a name="environment-setup"></a>Configurazione dell'ambiente
In questo esempio è un abbonamento che contiene le operazioni seguenti:

- Tre servizi cloud: "SecSvc001", "FrontEnd001" e "BackEnd001"
- Una rete virtuale "CorpNetwork", con tre subnet: "SecNet", "Front-end" e "Back-end"
- Un dispositivo virtuale di rete, in questo esempio un firewall connesso alla subnet SecNet
- Windows Server che rappresenta un server web di applicazioni ("IIS01")
- Server di due finestre che rappresentano applicazione indietro terminare server ("AppVM01", "AppVM02")
- Windows server che rappresenta un server DNS ("DNS01")

Nella sezione riferimenti sotto esiste uno script di PowerShell che creerà la maggior parte dell'ambiente descritta in precedenza. Creazione di macchine virtuali e le reti virtuali, anche se vengono eseguite da script di esempio, non sono descritti in modo dettagliato in questo documento.

Per creare l'ambiente:

  1.    Salvare il file xml di configurazione rete incluso nella sezione riferimenti in base ai nomi, luogo e IP indirizzi in modo che corrisponda lo scenario specificato in
  2.    Aggiornare le variabili di utente di uno script per garantire corrispondenza tra l'ambiente che lo script è deve essere eseguita (abbonamenti, nomi dei servizi, e così via)
  3.    Eseguire lo script di PowerShell

**Nota**: l'area geografica indicato nello script di PowerShell deve corrispondere al paese indicato nel file xml di configurazione della rete.

Una volta lo script viene eseguito correttamente la procedura seguente post-script può essere adottata:

1.  Impostare le regole firewall, questo argomento viene trattato nella sezione seguente: descrizione della regola Firewall.
2.  Facoltativamente nella sezione riferimenti sono due script per configurare il server web e app con un'applicazione web semplice per consentire la verifica con la configurazione di rete Perimetrale.

Una volta lo script viene eseguito correttamente il firewall regole dovrà essere completato, questo argomento viene trattato nella sezione: regole del Firewall.

## <a name="user-defined-routing-udr"></a>Routing (UDR) definite dall'utente
Per impostazione predefinita, le route di sistema seguenti sono definite come:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

Il VNETLocal è sempre prefix(es) indirizzo definito di VNet per la rete specifica (ie verrà modificato da VNet in VNet a seconda di come viene definito ogni VNet specifico). Le rimanenti route di sistema sono statiche e predefiniti come illustrato in precedenza.

Per priorità, cicli di elaborazione tramite il metodo più lunga prefisso Confronta (LPM), pertanto la route più specifica della tabella viene applicato a un indirizzo di destinazione specificata.

Di conseguenza, il traffico (ad esempio per il server DNS01 10.0.2.4) destinato VNet alla relativa destinazione a causa di route 10.0.0.0/16 sarebbe il routing della rete locale (10.0.0.0/16). In altre parole, per 10.0.2.4, route 10.0.0.0/16 è una route più specifica, anche se la 10.0.0.0/8 e 0.0.0.0/0 inoltre possibile applicare, ma poiché sono minore specifico non influiscono il traffico. In questo modo il traffico al 10.0.2.4 verrà hop successivo di VNet locale e semplicemente indirizzare alla destinazione.

Se il traffico destinato a 10.1.1.1, ad esempio, non applica route 10.0.0.0/16, ma il 10.0.0.0/8 sarebbe più specifico e il risultato sarà il traffico ignorati ("nero holed") perché l'hop successivo è Null. 

Se la destinazione non applica a uno dei prefissi Null o prefissi VNETLocal, quindi da seguire meno specifica indirizzare, 0.0.0.0/0 e far uscire a Internet come hop successivo e quindi su bordo internet di Azure.

Se sono presenti due prefissi identici della tabella, di seguito è l'ordine di preferenza in base all'attributo "origine" route:

1.  "VirtualAppliance" = una Route definiti dall'utente manualmente aggiunto alla tabella
2.  "VPNGateway" = una Route dinamiche BGP (se utilizzata con reti ibrido), aggiunta da un protocollo di rete dinamiche, queste route possono cambiare nel tempo come protocollo dinamico vengono visualizzate automaticamente le modifiche in rete peered
3.  "Predefinito" = i cicli di sistema, VNet locale sia le voci statiche come illustrato nella tabella route.

>[AZURE.NOTE] È ora possibile usare utente definito Routing (UDR) con ExpressRoute e gateway VPN per imporre il traffico di croce locale in ingresso e in uscita per essere instradati a un dispositivo virtuale di rete (NVA).

#### <a name="creating-the-local-routes"></a>Creazione di route locali

In questo esempio, sono necessari due tabelle di routing, uno per subnet front-end e back-end. Ogni tabella viene caricata con route statiche appropriate per la subnet specificata. In questo esempio, in ogni tabella sia tre indirizza:

1. Traffico subnet locale con alcun Hop successivo definito per consentire il traffico subnet locale per ignorare il firewall
2. Il traffico di rete virtuale con un Hop successivo definito come firewall, questo sostituisce la regola predefinita che consente il traffico VNet locale instradare direttamente
3. Traffico rimanente tutti (0/0) con un Hop successivo definito come il firewall

Dopo aver create le tabelle di routing vengano associati alle loro subnet. Per subnet front-end routing tabella, una volta creato e associato alla subnet dovrebbe risultare analoga alla seguente:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


In questo esempio vengono utilizzati i comandi seguenti per creare la tabella di route, aggiungere una route definite dall'utente e quindi associare la tabella la distribuzione a una subnet (nota; tutti gli elementi sotto che inizia con un segno di dollaro (ad esempio: $BESubnet) sono variabili definite dall'utente dallo script nella sezione Guida di riferimento di questo documento):

1.  Prima di tutto è necessario creare la tabella di routing base. Questo frammento di codice viene illustrata la creazione della tabella per subnet back-end. Nello script per subnet Frontend anche viene creata una tabella corrispondente.

        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"

2.  Dopo aver creata la tabella route, è possono aggiungere route specifiche definite dall'utente. In questo catturati, tutto il traffico (0.0.0.0/0) verrà inviato tramite il dispositivo virtuale (una variabile $VMIP [0], viene utilizzata per passare l'indirizzo IP assegnato quando il dispositivo virtuale è stato creato in precedenza nello script). In script, nella tabella Frontend anche viene creata una regola corrispondente.

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]

3. La voce di route sopra sostituirà il "0.0.0.0/0" route predefinita, ma la regola 10.0.0.0/16 predefinita ancora esistente che consenta il traffico all'interno di VNet per instradare direttamente alla destinazione e non per il dispositivo virtuale di rete. A corretto questo comportamento regola Segui deve essere aggiunta.

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]

4. A questo punto è possibile scegliere da apportare. Con due route sopra tutto il traffico verrà indirizzato al firewall per la valutazione, anche il traffico all'interno di una singola subnet. Questo potrebbe essere bene accolta, ma per consentire il traffico all'interno di una subnet per instradare localmente senza coinvolgimento dal firewall una terza, è possibile aggiungerli regola molto specifica. Questo percorso stati qualsiasi indirizzo destine per subnet locale sufficiente instradare sono direttamente (NextHopType = VNETLocal).

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal

5.  Infine, con la tabella routing creato e popolato con una route definite dall'utente, la tabella deve ora essere associata a una subnet. In script, la tabella di route front-end anche è associata alla subnet front-end. Ecco lo script di associazione per subnet back-end.

        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
           -SubnetName $BESubnet `
           -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>Inoltro IP
Una caratteristica correlata a UDR, è IP Forwarding. Questa è un'impostazione in un dispositivo virtuale che consente di ricevere il traffico non specificamente indirizzato all'accessorio e quindi inoltrare il traffico alla destinazione finale.

Ad esempio, se il traffico da AppVM01 effettua una richiesta al server DNS01 UDR indirizza seguente per il firewall. Con IP inoltro abilitato, il traffico verso la destinazione DNS01 (10.0.2.4) verrà accettato dall'accessorio (10.0.0.4) e quindi inviato alla destinazione ultimate (10.0.2.4). Senza IP inoltro attivato il Firewall, il traffico non sarà stato accettato dall'accessorio, anche se la tabella route include il firewall come hop successivo. 

>[AZURE.IMPORTANT] È importante ricordare di attivare inoltro IP in combinazione con il Routing definite dall'utente.

Impostazione di inoltro IP è un singolo comando e può essere eseguita in fase di creazione di macchine Virtuali. Per il flusso di questo esempio il frammento di codice è verso la fine dello script e raggruppati con i comandi UDR:

1.  Chiamata all'istanza di macchine Virtuali in questo caso è il dispositivo virtuale, il firewall e abilitare l'inoltro IP (nota; qualsiasi elemento in rosso a partire da un segno di dollaro (ad esempio: $VMName[0]) è una variabile definite dall'utente dallo script nella sezione Guida di riferimento di questo documento. Lo zero tra parentesi quadre, [0], rappresenta la macchina virtuale prima della matrice di macchine virtuali, per lo script di esempio lavorare senza alcuna modifica, la macchina virtuale prima (macchine Virtuali 0) deve essere il firewall):

        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
           Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>Gruppi di sicurezza di rete (NSG)
In questo esempio, un gruppo NSG è integrato e quindi caricato con una singola regola. In questo gruppo viene quindi associato solo alle subnet front-end e back-end (non SecNet). In modo dichiarativo viene creata la regola seguente:

1.  Tutto il traffico (tutte le porte) da Internet per l'intero VNet (tutte le subnet) negato

Sebbene NSGs vengono utilizzati in questo esempio, è scopo principale è come un livello secondario di difesa errata configurazione manuale. Si desidera bloccare tutto il traffico da internet in ingresso sia il front-end o back-end subnet, il traffico deve solo scorrono subnet SecNet per il firewall e quindi se a seconda delle necessità di front-end o back-end subnet. Inoltre, con le regole UDR in posizione, il traffico che trasformarlo in subnet front-end o back-end da indirizzato fuori al firewall (ringraziamento UDR). Il firewall vedrà come un flusso di asimmetrico e da rilasciare il traffico in uscita. In questo modo sono disponibili tre livelli di sicurezza: protezione subnet front-end e back-end; 1) punti finali aperti sul FrontEnd001 e BackEnd001 servizi non cloud, 2) NSGs si nega il traffico da Internet, 3) il traffico asimmetrici eliminazione firewall.

Un aspetto interessante relativo gruppo di sicurezza della rete in questo esempio è che contiene solo una regola, illustrata di seguito, per impedire il traffico internet all'intera rete virtuale che comprende subnet sicurezza. 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

Poiché il NSG solo è associata a subnet front-end e back-end, la regola non viene elaborata per il traffico in ingresso alla subnet di sicurezza. Di conseguenza, anche se la regola NSG indica che non il traffico Internet a qualsiasi indirizzo in VNet, perché il NSG è stato mai associato alla subnet sicurezza, il traffico scorrerà alla subnet di sicurezza.

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName
    
    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>Regole firewall
Sul firewall, regole di inoltro saranno necessario creare. Poiché il firewall del traffico blocco o inoltro tutti in entrata, in uscita e VNet all'interno sono necessari più regole del firewall. Inoltre, tutto il traffico verrà raggiunto l'indirizzo IP pubblico del servizio di sicurezza (su porte diverse), per essere elaborati dal firewall. Procedura consigliata consiste nel diagramma flussi logici prima di configurare le subnet e le regole del firewall per evitare rielaborare in un secondo momento. Nella figura seguente è una visualizzazione logica di regole del firewall per questo esempio:
 
![Visualizzazione logica di regole del Firewall][2]

>[AZURE.NOTE] In base al dispositivo di rete virtuale utilizzato, le porte gestione variano. In questo esempio che viene fatto riferimento a un Firewall di NextGen Barracuda che utilizza le porte 22, 801 e 807. Consultare la documentazione del produttore del dispositivo per individuare le porte esatte utilizzate per la gestione del dispositivo in uso.

### <a name="logical-rule-description"></a>Descrizione della regola logico
Nel diagramma precedente, alla subnet sicurezza non viene visualizzata perché il firewall è l'unica risorsa nella subnet e il diagramma è che mostra le regole del firewall e come logicamente Consenti o Nega flussi di traffico e non il percorso indirizzato effettivo. Inoltre, le porte esterne selezionate per il traffico RDP superiori erano tra porte (8014-8026) e selezionate per allineare un po' con gli ultimi due ottetti dell'indirizzo IP locale per facilitarne la lettura (ad esempio indirizzo del server locale 10.0.1.4 è associato a porta esterna 8014), tuttavia potrebbero essere utilizzate le porte non in conflitto superiore.

In questo esempio, è necessario 7 tipi di regole, questi tipi di regole sono vedere come indicato di seguito:

- Regole esterne (per il traffico in ingresso):
  1.    Regola di gestione del firewall: Questa regola di reindirizzamento App consente il traffico passare alle porte di gestione dell'accessorio virtuale rete.
  2.    Le regole di RDP (per ogni server di windows): gestione dei singoli server tramite RDP modo saranno questi quattro regole (uno per ogni server). Questo potrebbe anche essere inserito in una regola in base alle funzionalità dell'accessorio virtuale rete in uso.
  3.    Regole per il traffico di applicazione: Sono disponibili due regole di traffico applicazione, il primo per il traffico web front-end e il secondo per il traffico di back-end (ad esempio server web a livello di dati). La configurazione di queste regole verrà dipendono l'architettura di rete (in cui vengono inseriti i server) e il traffico flussi (che indica la direzione in cui vengono utilizzati i flussi di traffico e le porte).
      - La prima regola consente il traffico di applicazione effettiva raggiungere il server di applicazione. Mentre le altre regole consentono di sicurezza, gestione e così via, applicazione di regole sono operazioni consentite agli utenti esterni o servizi per le applicazioni di accedere. In questo esempio è un server web singolo sulla porta 80, pertanto una regola di applicazione firewall singolo consente di reindirizzare il traffico in ingresso all'indirizzo IP esterno, per l'indirizzo IP interno del server web. La sessione di traffico reindirizzato da essere NAT doveva server interno.
      - La seconda regola il traffico applicazione è la regola di back-end per consentire al Server Web di comunicare con il server AppVM01 (ma non AppVM02) tramite qualsiasi porta.
- Regole interne (per il traffico interno VNet)
  4.    In uscita a Internet regola: questa regola consentirà il traffico da qualsiasi rete per passare alle reti selezionate. Si tratta in genere una regola predefinita già sul firewall, ma in stato disabilitato. Questa regola deve essere abilitata per questo esempio.
  5.    Regola DNS: Questa regola consente solo il traffico DNS (porta 53) passare al server DNS. Per questo ambiente che la maggior parte del traffico dal front-end nel back-end è bloccato, questa regola consente in particolare DNS da qualsiasi subnet locale.
  6.    Regola subnet in Subnet: questa regola è per consentire a tutti i server su subnet back-end per connettersi a qualsiasi server subnet front-end (ma non viceversa).
- In modalità provvisoria regola (per il traffico che non soddisfa una di queste):
  7.    Per negare tutto il traffico regola: Questo deve essere sempre la regola finale (in termini di priorità) e come tali un traffico passa avrà esito negativo in modo che corrispondano a una delle regole precedenti che verrà rimosso da questa regola. Si tratta di una regola predefinita e in genere è attivata, senza modifiche sono in genere necessari.

>[AZURE.TIP] Nella seconda regola il traffico di applicazione, qualsiasi porta consentita per facilità di questo esempio, in uno scenario reale porta più specifica e intervalli di indirizzi devono essere utilizzati per ridurre la possibilità di attacchi di questa regola.

<br />

>[AZURE.IMPORTANT] Dopo aver create tutte le regole precedenti, è importante rivedere la priorità di ogni regola per assicurarsi che il traffico verrà consentito o negato in base alle esigenze. In questo esempio, le regole sono in ordine di priorità. È facile essere bloccato il firewall a causa di regole erroneamente ordinate. Almeno, assicurarsi che la gestione del firewall stesso è sempre la regola di priorità più alta assoluto.

### <a name="rule-prerequisites"></a>Prerequisiti delle regole
Un prerequisito per in esecuzione il firewall sono endpoint pubblico. Per il firewall per l'elaborazione di traffico gli endpoint pubblici devono essere aperti. Esistono tre tipi di traffico in questo esempio; Traffico RDP 1) il traffico di Gestione controllo il firewall e le regole del firewall, 2) per controllare il server di windows e il traffico applicazione 3). Ecco tre colonne di tipi di traffico in alto a metà di visualizzazione logica di regole del firewall sopra.

>[AZURE.IMPORTANT] Una chiave takeway qui è a tenere presente che **tutto** il traffico venga recapitata attraverso il firewall. In modo a desktop remoto per il server IIS01, anche se il servizio di Cloud Front-End e nella subnet Front-End per accedere a questo server è sarà necessario RDP per il firewall sulla porta 8014 e quindi consentire il firewall indirizzare la richiesta RDP internamente alla porta RDP IIS01. Pulsante "Connetti" del portale Azure non funziona perché non è un percorso RDP diretto a IIS01 (purché il portale può vedere). Di conseguenza, che tutte le connessioni da internet, sono necessario il servizio di sicurezza e una porta, ad esempio secscv001.cloudapp.net:xxxx (riferimento per il mapping di porta esterna e IP interna e la porta nel diagramma precedente).

Un endpoint può essere aperto al momento della creazione di macchine Virtuali o registrare genera come completato nello script di esempio e visualizzate sotto il frammento di codice (nota; qualsiasi elemento a partire da un segno di dollaro (ad esempio: $VMName[$i]) è una variabile definite dall'utente dallo script nella sezione Guida di riferimento di questo documento. "$I" tra parentesi quadre, [$i] rappresenta il numero di matrice di una macchina virtuale specifico in una matrice di macchine virtuali):

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

Sebbene non chiaramente illustrato di seguito per l'utilizzo di variabili, ma i punti finali sono **solo** aperto il servizio Cloud di sicurezza. Si tratta per assicurarsi che tutto il traffico è gestito (indirizzati NAT aveva, eliminare), il firewall.

È necessario che un client di gestione installato in un PC per gestire il firewall e creare le configurazioni necessarie. Vedere la documentazione del firewall (o altri NVA) imparare a gestire il dispositivo. Il resto di questa sezione e la sezione successiva, creazione di regole Firewall, per descrivere la configurazione del firewall, tramite il client di gestione dei fornitori (ad esempio, non il portale di Azure e PowerShell).

Istruzioni per il download di client e la connessione a Barracuda utilizzati in questo esempio sono disponibili qui: [Barracuda gas amministratore](https://techlib.barracuda.com/NG61/NGAdmin)

Dopo aver eseguito l'accesso nel firewall ma prima di creare regole del firewall, sono disponibili due classi oggetto prerequisiti che rendono più semplice; creare le regole Oggetti di rete e servizio.

In questo esempio tre oggetti di rete denominato dovrebbero essere definito (uno per subnet front-end e back-end subnet, anche un oggetto di rete per l'indirizzo IP del server DNS). Per creare una rete denominata; a partire dal dashboard di client Barracuda gas amministratore, passare alla scheda configurazione, nella sezione Configurazione operative fare clic su set di regole, fare clic su "Reti" nel menu oggetti Firewall, quindi fare clic su nuovo nel menu Modifica reti. L'oggetto di rete può essere ora creata, aggiungendo il nome e il prefisso:

![Creare un oggetto di rete front-end][3]
 
Verrà creata una rete denominata subnet front-end, deve creare un oggetto simile per anche alla subnet back-end. Ora le subnet possono più facilmente fare riferimento nome regole del firewall.

Per gli oggetti di Server DNS:

![Creare un oggetto di Server DNS][4]

Questo riferimento indirizzo IP singolo verrà utilizzato in una regola di DNS in un punto successivo del documento.

I secondo oggetti prerequisiti sono servizi. Questi rappresenterà le porte di connessione RDP per ogni server. Poiché l'oggetto servizio RDP esistente è associata alla porta predefinita RDP, 3389, nuovi servizi possono essere creati per consentire il traffico da porte esterne (8014 8026). Nuove porte inoltre è stato aggiunto al servizio RDP esistente, ma per facilitare la dimostrazione, è possibile creare una singola regola per ogni server. Per creare una nuova regola RDP per un server. a partire dal dashboard di client Barracuda gas amministratore, passare alla scheda configurazione, nella sezione configurazione operativa fare clic su set di regole, quindi fare clic su "Servizi" nel menu Firewall oggetti, scorrere verso il basso l'elenco dei servizi e selezionare il servizio "RDP". Pulsante destro del mouse e scegliere Copia, quindi mouse e scegliere Incolla. È ora disponibile un oggetto di servizio RDP Copy1 che possono essere modificati. Pulsante destro del mouse RDP Copy1 e selezionare Modifica, il servizio di modifica oggetto finestra verrà visualizzato un massimo, come illustrato di seguito:

![Copia della regola RDP predefinita][5]

I valori possono essere modificati quindi per rappresentare il servizio RDP per un determinato server. Per AppVM01 la regola di RDP predefinita sopra deve essere modificata per riflettere un nuovo nome del servizio, descrizione ed esterni porta RDP utilizzata nel diagramma figura 8 (Nota: le porte vengono modificate da quello predefinito RDP di 3389 alla porta esterna usata per questo server specifico, nel caso dei AppVM01 porta esterna è 8025) di seguito è riportato il servizio modificato :

![Regola AppVM01][6]
 
Questo processo deve essere ripetuto per creare RDP servizi per i server rimanenti. AppVM02, DNS01 e IIS01. La creazione di questi servizi consentono di rendere la creazione di regole in modo più chiaro e semplice nella sezione successiva.

>[AZURE.NOTE] Un servizio RDP per il Firewall non è necessaria per due motivi; 1) prima il firewall macchine Virtuali è un'immagine di Linux in base a in modo SSH verrebbe utilizzato sulla porta 22 per gestione delle macchine Virtuali anziché RDP e 2) porta 22 e due altre porte gestione consentite nella prima regola gestione descritta di seguito per consentire la connettività di gestione.

### <a name="firewall-rules-creation"></a>Creazione di regole firewall
Esistono tre tipi di regole del firewall utilizzati in questo esempio, hanno icone distinte:

La regola applicazione reindirizzare: ![Icona reindirizzare dell'applicazione][7]

La regola NAT di destinazione: ![Sull'icona NAT di destinazione][8]

La regola passaggio: ![Passare a icona][9]

Ulteriori informazioni su queste regole disponibili nel sito web Barracuda.

Per creare le regole seguenti (o verificare regole predefinito esistenti), partendo dal dashboard client Barracuda gas amministratore, passare alla scheda configurazione, in configurazione operativa sezione, fare clic su set di regole. Griglia denominata, "Principali regole" illustra le regole attive e disattivate su questo firewall. Nell'angolo superiore destro della griglia è un piccolo, verde "+" pulsante, fare clic qui per creare una nuova regola (Nota: il firewall "bloccato" per le modifiche, se viene visualizzato un pulsante contrassegnato "Bloccare" e non si riesce a creare o modificare le regole, fare clic su questo pulsante per il set di regole "sbloccare" e consentire la modifica). Se si desidera modificare una regola esistente, selezionare tale regola, mouse e scegliere Modifica regola.

Una volta create e/o modifica delle regole di, deve essere inseriti il firewall e quindi attivate, se questa operazione non viene eseguita le modifiche alle regole avranno effetto. Il processo di attivazione e push descritto di seguito la descrizione delle regole di dettagli.

Le specifiche di ogni regola necessario per completare in questo esempio sono descritti di seguito:

- **Regola di gestione del firewall**: reindirizzare App questa regola consente il passaggio le porte di gestione dell'accessorio rete virtuale in questo esempio Barracuda NextGen Firewall del traffico. Le porte di gestione sono 801, 807 e facoltativamente 22. Le porte interne ed esterne sono gli stessi (ad esempio senza traduzione porta). Questa regola, il programma di installazione Gestione accesso, è una regola predefinita e attivata per impostazione predefinita (in versione Barracuda NextGen Firewall 6.1).

    ![Regola di gestione del firewall][10]

>[AZURE.TIP] Lo spazio di indirizzi di origine in questa regola è presente, se sono noti gli intervalli di indirizzi IP di gestione, ridurre l'ambito da anche ridurre la possibilità di attacchi alle porte di gestione.

- **RDP regole**: regole questi NAT di destinazione consentirà gestione dei singoli server tramite RDP.
Esistono quattro campi critica necessari per creare la regola:
  1.    Origine – per consentire RDP da qualsiasi luogo, il riferimento "Uno" viene usato nel campo origine.
  2.    Servizio: usare oggetto servizio appropriato, creato in precedenza, in questo caso "AppVM01 RDP", le porte esterne reindirizzare all'indirizzo IP del server locale e alla porta 3386 (la porta predefinita RDP). Questa regola specifica consente l'accesso RDP a AppVM01.
  3.    Destinazione: dovrebbe essere la *porta locale del firewall*, "DHCP 1 locale IP" o eth0 Se Usa IP statico. Il numero ordinale (eth0, eth1 e così via) può essere diverso se il dispositivo di rete con più interfacce locale. Porta per inviare il firewall fuori dal (potrebbe essere identica a quella ricezione), la destinazione indirizzata effettiva è nel campo elenco di destinazione.
  4.    Reindirizzamento: in questa sezione indica il dispositivo virtuale dove infine reindirizzare il traffico. Il reindirizzamento più semplice consiste nell'inserire IP e porte (facoltativo) nel campo elenco di destinazione. Se non si utilizza alcuna porta porta di destinazione su richiesta in ingresso sarà utilizzato (ie senza conversione), se una porta è assegnata la porta sarà anche NAT da oltre l'indirizzo IP di indirizzi.

    ![Regola RDP firewall][11]

    È necessario che un totale di quattro regole RDP creare: 

  	|   Nome regola    | Server  |   Servizio   |  Elenco di destinazione  |
  	|----------------|---------|-------------|---------------|
  	| RDP a IIS01   | IIS01   | IIS01 RDP   | 10.0.1.4:3389 |
  	| RDP-DNS01   | DNS01   | DNS01 RDP   | 10.0.2.4:3389 |
  	| RDP-AppVM01 | AppVM01 | AppVM01 RDP | 10.0.2.5:3389 |
  	| RDP-AppVM02 | AppVM02 | AppVm02 RDP | 10.0.2.6:3389 |
  
>[AZURE.TIP] In quanto l'ambito dei campi di origine e servizi consente di ridurre i rischi. L'ambito più limitato che consentirà funzionalità deve essere utilizzato.

- **Applicazione del traffico regole**: sono disponibili due regole di traffico applicazione, il primo per il traffico web front-end e il secondo per il traffico di back-end (ad esempio server web a livello di dati). Queste regole verranno dipendono l'architettura di rete (in cui vengono inseriti i server) e il traffico flussi (che indica la direzione in cui vengono utilizzati i flussi di traffico e le porte).

    Prima di tutto descritto è la regola front-end per il traffico web:

    ![Regola Web firewall][12]
 
    Questa regola NAT di destinazione consente il traffico delle applicazioni effettivo raggiungere il server di applicazione. Mentre le altre regole consentono di sicurezza, gestione e così via, applicazione di regole sono operazioni consentite agli utenti esterni o servizi per le applicazioni di accedere. In questo esempio è un server web singolo sulla porta 80, pertanto la regola di applicazione firewall singolo consente di reindirizzare il traffico in ingresso all'indirizzo IP esterno, per l'indirizzo IP interno del server web.

    **Nota**: è presente alcuna porta nel campo elenco di destinazione, pertanto la porta in ingresso 80 (o 443 per il servizio selezionato) verrà utilizzata il reindirizzamento del server web. Se il server web è in attesa su un'altra porta, ad esempio la porta 8080, Impossibile aggiornare il campo elenco di destinazione per 10.0.1.4:8080 per consentire il reindirizzamento porta anche.

    La regola di traffico applicazione successiva è la regola di back-end per consentire al Server Web di comunicare con il server AppVM01 (ma non AppVM02) tramite qualsiasi servizio:

    ![Regola AppVM01 firewall][13]

    Questa regola passaggio consente a ogni server IIS nella subnet front-end per raggiungere il AppVM01 (indirizzo IP 10.0.2.5) su qualsiasi porta utilizzando qualsiasi protocollo per accedere ai dati necessari per l'applicazione web.

    In questa schermata un "\<espliciti destinazione\>" viene usato nel campo di destinazione per indicare 10.0.2.5 come destinazione. Questo potrebbe essere denominato espliciti come illustrato o un oggetto di rete (come avveniva i prerequisiti per il server DNS). Verrà visualizzata per l'amministratore del firewall per quale metodo verrà utilizzato. Per aggiungere 10.0.2.5 come un Explict Desitnation, fare doppio clic sulla prima riga vuota sotto \<espliciti destinazione\> e immettere l'indirizzo nella finestra popup.

    Con questa regola passare NAT non è necessaria poiché si tratta di traffico interno, il metodo di connessione è possibile impostare "N SNAT".

    **Nota**: rete di origine in questa regola è una risorsa su subnet FrontEnd se ci saranno uno o un numero specifico noto di server web, una risorsa di rete oggetto è possibile creare per essere più agli indirizzi IP esatti anziché l'intera subnet front-end.

>[AZURE.TIP] Questa regola utilizza il servizio "Qualsiasi" per semplificare l'applicazione di esempio di configurazione e utilizzo, sarà inoltre ICMPv4 (ping) in una singola regola. Tuttavia, questo non è una procedura consigliata. Le porte e protocolli ("servizi") devono restringere al minimo possibili che consente l'operazione di applicazione per ridurre la possibilità di attacchi attraverso questo limite.

<br />

>[AZURE.TIP] Anche se la regola viene un riferimento di destinazione espliciti in uso, un approccio coerente deve essere utilizzato in tutta la configurazione del firewall. È consigliabile che l'oggetto di rete denominato essere utilizzati per supporto e facilitarne la lettura. L'espliciti destinazione viene utilizzata solo per mostrare un metodo di riferimento alternativo e non è in genere consigliata (soprattutto per le configurazioni complesse).

- **In uscita a Internet regola**: passare questa regola consentirà il traffico da qualsiasi rete di origine per passare alle reti di destinazione selezionate. Questa regola è una regola predefinita in genere già sul firewall Barracuda NextGen, ma è stato disabilitato. Facendo clic su questa regola, è possibile accedere al comando Attiva regola. Per aggiungere le due subnet locale che sono state create come riferimenti nella sezione prerequisito di questo documento per l'attributo di origine di questa regola è stata modificata la regola illustrata di seguito.

    ![Regola in uscita firewall][14]

- **Regola DNS**: passare questa regola consente solo il traffico DNS (porta 53) passare al server DNS. Per questo ambiente che la maggior parte del traffico dal front-end nel back-end è bloccato, questa regola consente in particolare DNS.

    ![Regola DNS firewall][15]

    **Nota**: In questa schermata è incluso con il metodo di connessione. Poiché questa regola per IP interno per il traffico di indirizzo IP interno, NATing non è necessaria, questa è impostato su "N SNAT" il metodo di connessione per la regola di passaggio.

- **Regola subnet in Subnet**: passare questa regola è una regola predefiniti che è stata attivata e modificata in modo da consentire a tutti i server sulla subnet back-end per connettersi a qualsiasi server subnet front-end. Questa regola è il traffico tutti interno in modo che il metodo di connessione è possibile impostare nessun SNAT.

    ![Regola VNet all'interno del firewall][16]

    **Nota**: la casella di controllo bidirezionale non è stato archiviato (non è selezionata nella maggior parte delle regole), questo è importante per la regola che rende questa regola "unidirezionale", una connessione può essere avviata dalla subnet back-end per la rete front-end, ma non viceversa. Se è stata selezionata la casella di controllo, questa regola consentirebbe bidirezionale il traffico, dal nostro diagramma logico non si desidera.

- **Regola rifiuto tutto il traffico**: deve sempre essere la regola finale (in termini di priorità) e pertanto se un traffico scorre non riesce a corrisponde a uno dei precedente regole verrà rimosso da questa regola. Si tratta di una regola predefinita e in genere è attivata, senza modifiche sono in genere necessari. 

    ![Regola di negazione firewall][17]

>[AZURE.IMPORTANT] Dopo aver create tutte le regole precedenti, è importante rivedere la priorità di ogni regola per assicurarsi che il traffico verrà consentito o negato in base alle esigenze. In questo esempio, le regole sono nell'ordine che dovrebbero essere visualizzate nella griglia di principali di regole nel Client di gestione Barracuda di inoltro.

## <a name="rule-activation"></a>Attivazione di regola
Con il set di regole modificato le specifiche del diagramma logica, il set di regole deve essere caricato nel firewall e quindi attivato.

![Attivazione di regole firewall][18]
 
Nell'angolo superiore destro del client management sono un cluster di pulsanti. Fare clic sul pulsante "Invia delle modifiche" per inviare le regole di modificate per il firewall e quindi fare clic sul pulsante "Attiva".
 
Con l'attivazione del set di regole firewall compilazione di ambiente in questo esempio è stata completata.

## <a name="traffic-scenarios"></a>Scenari di traffico
>[AZURE.IMPORTANT] Una chiave takeway è a tenere presente che **tutto** il traffico venga recapitata attraverso il firewall. In modo a desktop remoto per il server IIS01, anche se il servizio di Cloud Front-End e nella subnet Front-End per accedere a questo server è sarà necessario RDP per il firewall sulla porta 8014 e quindi consentire il firewall indirizzare la richiesta RDP internamente alla porta RDP IIS01. Pulsante "Connetti" del portale Azure non funziona perché non è un percorso RDP diretto a IIS01 (purché il portale può vedere). Di conseguenza, che tutte le connessioni da internet, sono necessario il servizio di sicurezza e una porta, ad esempio secscv001.cloudapp.net:xxxx.

Per questi scenari, le regole firewall seguenti devono essere disponibili:

1.  Gestione del firewall
2.  RDP a IIS01
3.  RDP a DNS01
4.  RDP a AppVM01
5.  RDP a AppVM02
6.  Traffico di App sul Web
7.  Traffico di App per AppVM01
8.  In uscita a Internet
9.  Front-end per DNS01
10. Traffico interno Subnet (back-end per front-end solo)
11. Rifiuta tutto

Il set di regole firewall effettivo è probabile che avranno molte altre regole oltre a questi, le regole in qualsiasi firewall specificato, sono disponibili anche i numeri di priorità diversi da quelli elencati qui. In questo elenco e numeri associati sono per fornire pertinenza tra le regole di 11 e la priorità relativa tra loro. In altre parole; sul firewall effettivo, il "RDP a IIS01" potrebbero essere regola numero 5, ma, purché si trova sotto la regola "Firewall gestione" e sopra la regola "RDP a DNS01" da allineare allo scopo di questo elenco. L'elenco anche facilitano la sotto scenari consentendo brevità; ad esempio "come regola 9 (DNS)". Anche per brevità, le quattro regole RDP verrà collettivamente chiamato, "regole RDP" quando lo scenario il traffico è correlato a RDP.

Anche tenere presente che gruppi di sicurezza di rete sono in posizione per il traffico internet in ingresso subnet front-end e back-end.

#### <a name="allowed-internet-to-web-server"></a>(Consentito) Internet al Server Web
1.  Pagina di Internet utente richieste HTTP da SecSvc001.CloudApp.Net (Internet affiancate Cloud Service)
2.  Cloud punto finale aperto sulla porta 80 all'interfaccia di firewall 10.0.0.4:80 il traffico di passaggi di servizio
3.  Nessun NSG assegnata alla subnet di sicurezza, così regole del sistema di NSG consentono il traffico a firewall
4.  Il traffico visitate indirizzo IP interno del firewall (10.0.1.4)
5.  Firewall Inizia elaborazione delle regole:
  1.    COME regola 1 (Gestione firmware) non applicare, passare alla regola successiva
  2.    COME regole 2-5 (RDP regole) non applicare, passare alla regola successiva
  3.    COME regola 6 (App: Web) applicare, il traffico è consentito, firewall NAT che 10.0.1.4 (IIS01)
6.  Subnet Frontend Inizia elaborazione delle regole in entrata:
  1.    NSG regola 1 (blocco Internet) non è applicabile (il traffico è stata NAT sarebbe dal firewall, l'indirizzo di origine è ora pertanto il firewall che si trova nella subnet sicurezza e visibili alla subnet Frontend NSG sia il traffico "locale" e quindi autorizzati), passare alla regola successiva
  2.    Regole di NSG predefinite consentire il traffico subnet in subnet, il traffico consentito, Interrompi l'elaborazione delle regole NSG
7.  Iis01 è in attesa per il traffico web, riceve la richiesta e inizia elaborazione della richiesta
8.  Tentativo di iis01 avvia una sessione FTP a AppVM01 subnet back-end
9.  L'itinerario UDR sulla subnet Frontend rende il firewall hop successivo
10. Nessuna regola in uscita subnet front-end, il traffico consentito
11. Firewall Inizia elaborazione delle regole:
  1.    COME regola 1 (Gestione firmware) non applicare, passare alla regola successiva
  2.    COME regola 2-5 (RDP regole) non applicare, passare alla regola successiva
  3.    COME regola 6 (App: Web) non applicare, passare alla regola successiva
  4.    COME regola 7 (App: back-end) applicare, il traffico consentito, firewall inoltra il traffico a 10.0.2.5 (AppVM01)
12. Subnet back-end Inizia elaborazione delle regole in entrata:
  1.    Non applicare, passare alla regola successiva NSG regola 1 (blocco Internet)
  2.    Regole di NSG predefinite consentire il traffico subnet in subnet, il traffico consentito, Interrompi l'elaborazione delle regole NSG
13.  AppVM01 riceve la richiesta e viene avviata la sessione e risponde
14. L'itinerario UDR sulla subnet back-end rende il firewall hop successivo
15. Poiché non esistono regole NSG in uscita nella subnet back-end che è consentita la risposta
16. Perché questo restituisce il traffico in una sessione di Fondazione il firewall passa la risposta al server web (IIS01)
17. Subnet Frontend Inizia elaborazione delle regole in entrata:
  1.    Non applicare, passare alla regola successiva NSG regola 1 (blocco Internet)
  2.    Regole di NSG predefinite consentire il traffico subnet in subnet, il traffico consentito, Interrompi l'elaborazione delle regole NSG
18. Il server IIS riceve la risposta, completa la transazione con AppVM01 e quindi viene completata la creazione di risposta HTTP, questa risposta HTTP viene inviata al richiedente
19. Poiché non esistono regole NSG in uscita nella subnet front-end che è consentita la risposta
20. Risposta HTTP visitate il firewall e poiché si tratta in risposta a una sessione di NAT Fondazione viene accettata, il firewall
21. Il firewall Reindirizza quindi la risposta all'utente di Internet
22. Perché non ci sono in uscita non NSG regole o hop UDR subnet front-end è consentita la risposta e l'utente Internet riceve la pagina web richiesta.

#### <a name="allowed-internet-rdp-to-backend"></a>(Consentito) Internet RDP a back-end
1.  Amministratori di server su internet richiede una sessione RDP alla AppVM01 tramite SecSvc001.CloudApp.Net:8025, dove 8025 è il numero di porta utente assegnato per la regola di "RDP a AppVM01" firewall
2.  Servizio cloud passa il traffico attraverso l'endpoint aperto sulla porta 8025 all'interfaccia firewall 10.0.0.4:8025
3.  Nessun NSG assegnata alla subnet di sicurezza, così regole del sistema di NSG consentono il traffico a firewall
4.  Firewall Inizia elaborazione delle regole:
  1.    COME regola 1 (Gestione firmware) non applicare, passare alla regola successiva
  2.    COME regola 2 (RDP IIS) non applicare, passare alla regola successiva
  3.    COME regola 3 (RDP DNS01) non applicare, passare alla regola successiva
  4.    Applicare la regola firmware 4 (RDP AppVM01), il traffico è consentito, firewall NAT che 10.0.2.5:3386 (porta RDP AppVM01)
5.  Subnet back-end Inizia elaborazione delle regole in entrata:
  1.    NSG regola 1 (blocco Internet) non è applicabile (il traffico è stata NAT sarebbe dal firewall, l'indirizzo di origine è ora pertanto il firewall che si trova nella subnet sicurezza e visibili alla subnet di back-end NSG sia il traffico "locale" e quindi autorizzati), passare alla regola successiva
  2.    Regole di NSG predefinite consentire il traffico subnet in subnet, il traffico consentito, Interrompi l'elaborazione delle regole NSG
6.  AppVM01 è in attesa di traffico RDP e risponde
7.  Con regole non NSG in uscita, applicate le regole predefinite e restituito il traffico consentito
8.  UDR indirizza il traffico in uscita per il firewall come hop successivo
9.  Perché questo restituisce il traffico in una sessione di Fondazione il firewall passa la risposta all'utente di internet
10. Sessione RDP è abilitata
11. AppVM01 richiede le password nome utente

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Consentito) Ricerca di DNS Server Web su server DNS
1.  Web Server, IIS01, esigenze feed di dati in www.data.gov, ma è necessario risolvere l'indirizzo.
2.  Configurazione della rete per gli elenchi di VNet DNS01 (10.0.2.4 subnet back-end) come server DNS primario, IIS01 invia la richiesta DNS per DNS01
3.  UDR indirizza il traffico in uscita per il firewall come hop successivo
4.  Nessun regole NSG in uscita associate alla subnet front-end, il traffico consentito
5.  Firewall Inizia elaborazione delle regole:
  1.    COME regola 1 (Gestione firmware) non applicare, passare alla regola successiva
  2.    COME regola 2-5 (RDP regole) non applicare, passare alla regola successiva
  3.    COME regole 6 e 7 (App regole) non applicare, passare alla regola successiva
  4.    COME regola 8 (a Internet) non applicare, passare alla regola successiva
  5.    Applicare i regola 9 (DNS), il traffico consentito, firewall inoltra il traffico a 10.0.2.4 (DNS01)
6.  Subnet back-end Inizia elaborazione delle regole in entrata:
  1.    Non applicare, passare alla regola successiva NSG regola 1 (blocco Internet)
  2.    Regole di NSG predefinite consentire il traffico subnet in subnet, il traffico consentito, Interrompi l'elaborazione delle regole NSG
7.  Server DNS riceve la richiesta
8.  Server DNS non è presente l'indirizzo di cache e richiede un server DNS principale su internet
9.  UDR indirizza il traffico in uscita per il firewall come hop successivo
10. Nessun NSG le regole in uscita subnet back-end, il traffico consentito
11. Firewall Inizia elaborazione delle regole:
  1.    COME regola 1 (Gestione firmware) non applicare, passare alla regola successiva
  2.    COME regola 2-5 (RDP regole) non applicare, passare alla regola successiva
  3.    COME regole 6 e 7 (App regole) non applicare, passare alla regola successiva
  4.     Applicare i regola 8 (a Internet), il traffico consentito, sessione SNAT fuori al server DNS radice su Internet
12. Server DNS Internet risponde, poiché è stata avviata questa sessione dal firewall, la risposta viene accettata, il firewall
13. Come si tratta di una sessione di fondazione, il firewall inoltra la risposta al server di avvio, DNS01
14. Subnet back-end Inizia elaborazione delle regole in entrata:
  1.    Non applicare, passare alla regola successiva NSG regola 1 (blocco Internet)
  2.    Regole di NSG predefinite consentire il traffico subnet in subnet, il traffico consentito, Interrompi l'elaborazione delle regole NSG
15. Il server DNS riceve e memorizza la risposta e quindi risponde alla richiesta iniziale alla IIS01
16. L'itinerario UDR sulla subnet back-end rende il firewall hop successivo
17. Non in uscita NSG esistono regole subnet back-end, il traffico consentito
18. Si tratta di una sessione di Fondazione sul firewall, la risposta è inoltro delle chiamate, il firewall al server IIS
19. Subnet Frontend Inizia elaborazione delle regole in entrata:
  1.    Nessuna regola NSG che si applica a in ingresso il traffico dalla subnet back-end alla subnet front-end, in modo che nessuno del NSG vengono applicate le regole
  2.    La regola di sistema predefinita traffico tra subnet consentirebbe il traffico in modo che il traffico sia consentito
20. Iis01 riceve la risposta da DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Consentito) Server di back-end front end server
1.  Un amministratore connesso a AppVM02 tramite RDP richiede un file direttamente dal server IIS01 tramite Esplora file
2.  L'itinerario UDR sulla subnet back-end rende il firewall hop successivo
3.  Poiché non esistono regole NSG in uscita nella subnet back-end che è consentita la risposta
4.  Firewall Inizia elaborazione delle regole:
  1.    COME regola 1 (Gestione firmware) non applicare, passare alla regola successiva
  2.    COME regola 2-5 (RDP regole) non applicare, passare alla regola successiva
  3.    COME regole 6 e 7 (App regole) non applicare, passare alla regola successiva
  4.    COME regola 8 (a Internet) non applicare, passare alla regola successiva
  5.    COME regola 9 (DNS) non applicare, passare alla regola successiva
  6.    Applicare i regola 10 (Subnet interne), il traffico consentito, firewall passa il traffico al 10.0.1.4 (IIS01)
5.  Subnet Frontend Inizia elaborazione delle regole in entrata:
  1.    Non applicare, passare alla regola successiva NSG regola 1 (blocco Internet)
  2.    Regole di NSG predefinite consentire il traffico subnet in subnet, il traffico consentito, Interrompi l'elaborazione delle regole NSG
6.  Supponendo che le autorizzazioni e l'autenticazione appropriata, IIS01 accetta la richiesta e risposta
7.  L'itinerario UDR sulla subnet Frontend rende il firewall hop successivo
8.  Poiché non esistono regole NSG in uscita nella subnet front-end che è consentita la risposta
9.  Poiché si tratta di una sessione esistente nel firewall è consentita la risposta e il firewall restituisce la risposta alla AppVM02
10. Subnet back-end Inizia elaborazione delle regole in entrata:
  1.    Non applicare, passare alla regola successiva NSG regola 1 (blocco Internet)
  2.    Regole di NSG predefinite consentire il traffico subnet in subnet, il traffico consentito, Interrompi l'elaborazione delle regole NSG
11. AppVM02 riceve la risposta

#### <a name="denied-internet-direct-to-web-server"></a>(Negato) Internet direttamente al Server Web
1.  Internet utente tenta di accedere al server web, IIS01, tramite il servizio FrontEnd001.CloudApp.Net
2.  Dal momento che non sono presenti endpoint aperta per il traffico HTTP, questo non passano attraverso il servizio Cloud e non raggiunga il server
3.  Se i punti finali erano aperti per qualche motivo, NSG (blocco Internet) sulla subnet front-end da bloccare il traffico
4.  Infine, la route UDR subnet front-end da inviare il traffico in uscita da IIS01 il firewall come hop successivo e il firewall da quanto segue come traffico asimmetrico e rilasciare la risposta in uscita sono quindi almeno tre livelli indipendenti di difesa tra internet e IIS01 tramite il servizio cloud impedire l'accesso non autorizzato/non appropriato.

#### <a name="denied-internet-to-backend-server"></a>(Negato) Internet al Server di back-end
1.  Utente Internet tenta di accedere a un file in AppVM01 tramite il servizio BackEnd001.CloudApp.Net
2.  Dal momento che non sono presenti endpoint aperta per la condivisione di file, questo non passa il servizio Cloud e non raggiunga il server
3.  Se i punti finali erano aperti per qualche motivo, NSG (blocco Internet) Blocca il traffico
4.  Infine, la route UDR da inviare il traffico in uscita da AppVM01 per il firewall come hop successivo e il firewall da quanto segue come traffico asimmetrico e rilasciare la risposta in uscita sono quindi almeno tre livelli indipendenti di difesa tra internet e AppVM01 tramite il servizio cloud impedire l'accesso non autorizzato/inappropriati.

#### <a name="denied-frontend-server-to-backend-server"></a>(Negato) Server front end server di back-end
1.  Si supponga IIS01 è stata compromessa ed è in esecuzione di codice dannoso si tenta di eseguire la scansione il server di back-end subnet.
2.  Il percorso UDR subnet front-end da inviare il traffico in uscita da IIS01 il firewall come hop successivo. Questa non operazione è quello che può essere modificato da macchina virtuale compromessa.
3.  Il firewall da elaborare il traffico, se la convocazione è stata a AppVM01 o al server DNS per le ricerche DNS che il traffico potenzialmente è stato consentito dal firewall (a causa di firmware regole 7 e 9). Tutto il traffico verrebbe bloccato da firmware regola 11 (Nega tutti).
4.  Se avanzate individuazione è stato attivato il firewall (che non viene descritto in questo documento, vedere la documentazione del fornitore per il dispositivo di rete specifiche minacce funzionalità avanzate), anche il traffico consentiti per le regole di inoltro di base descritti in questo documento potrebbe essere evitato il traffico contenuti firme note o motivi che contrassegno una regola di rischio avanzate.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Negato) Ricerca di Internet DNS nel server DNS
1.  Internet utente tenta di cercare un record DNS interno nel DNS01 tramite il servizio BackEnd001.CloudApp.Net 
2.  Dal momento che non sono presenti endpoint aperto per il traffico DNS, questo non passano attraverso il servizio Cloud e non raggiunga il server
3.  Se i punti finali erano aperti per qualche motivo, la regola NSG (blocco Internet) sulla subnet front-end da bloccare il traffico
4.  Infine, la route UDR subnet back-end da inviare il traffico in uscita da DNS01 per il firewall come hop successivo e il firewall da quanto segue come traffico asimmetrico e rilasciare la risposta in uscita sono quindi almeno tre livelli indipendenti di difesa tra internet e DNS01 tramite il servizio cloud impedire l'accesso non autorizzato/non appropriato.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Negato) Internet per l'accesso SQL tramite Firewall
1.  Utente Internet richiede dati SQL SecSvc001.CloudApp.Net (Internet affiancate Cloud Service)
2.  Dal momento che non sono presenti endpoint aperto per SQL, questo non passa il servizio Cloud e non raggiunga il firewall
3.  Se i punti finali SQL erano aperti per qualche motivo, il firewall preferisce iniziare l'elaborazione delle regole:
  1.    COME regola 1 (Gestione firmware) non applicare, passare alla regola successiva
  2.    COME regole 2-5 (RDP regole) non applicare, passare alla regola successiva
  3.    COME regola 6 e 7 (applicazione di regole) non applicare, passare alla regola successiva
  4.    COME regola 8 (a Internet) non applicare, passare alla regola successiva
  5.    COME regola 9 (DNS) non applicare, passare alla regola successiva
  6.    COME regola 10 (Subnet interne) non applicare, passare alla regola successiva
  7.    Applicare i regola 11 (Nega tutti), il traffico è l'elaborazione delle regole bloccate, interrompere


## <a name="references"></a>Riferimenti
### <a name="main-script-and-network-config"></a>Script principale e configurazione di rete
Salvare lo Script completo in un file di script di PowerShell. Salvare la configurazione di rete in un file denominato "NetworkConf2.xml".
Modificare le variabili definite dall'utente in base alle esigenze. Eseguire lo script e quindi seguire le istruzioni configurazione regola Firewall sopra.

#### <a name="full-script"></a>Script completo
Questo script verrà, in base alle variabili definite dall'utente:

1.  Connettersi a un abbonamento a Azure
2.  Creare un nuovo account di archiviazione
3.  Creare un nuovo VNet e tre subnet come definito nel file di configurazione di rete
4.  Creare cinque macchine virtuali; 1 firewall e 4 windows server macchine virtuali
5.  Configurare UDR inclusi:
  1.    Creare due nuove tabelle la distribuzione
  2.    Aggiungere route alle tabelle
  3.    Associare tabelle a subnet appropriata
6.  Attivare l'inoltro IP nel NVA
7.  Configurare NSG inclusi:
  1.    Creazione di un NSG
  2.    Aggiunta di una regola
  3.    Associazione di NSG a subnet appropriata

Questo script di PowerShell deve essere eseguito in locale via che Internet connessi PC o nel server.

>[AZURE.IMPORTANT] Quando si esegue questo script, è possibile avvisi o altri messaggi informativi pop in PowerShell. Solo i messaggi di errore in rosso sono causa problemi.

    <# 
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwading from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA
    
      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).
    
     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.
    
      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4
     
      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4
     
      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6
    
    #>
    
    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()
    
    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section
    
      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    
      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"
    
      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"
    
      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
    
      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"
    
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.
    
        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"
    
        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"
    
        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"
    
    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #
    
      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
    
      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
    
      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
    
    # Validation
    $FatalError = $false
    
    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}
    
    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
    
    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
    
    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
    
    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
    
    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
    
    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }
    
    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan
    
      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"
    
      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal
    
      # Assoicate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName
    
     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
    
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *
    
      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
    
    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
    

#### <a name="network-config-file"></a>File di configurazione di rete
Salvare il file xml con posizione aggiornata e aggiungere il collegamento al file per la variabile di $NetworkConfigFile lo script precedente.

    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Script di applicazione di esempio
Se si desidera installare un'applicazione di esempio per questo e altri esempi di rete Perimetrale, si dispone del collegamento seguente: [Script di applicazione di esempio][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Rete Perimetrale bidirezionale con NVA NSG e UDR"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Visualizzazione logica di regole del Firewall"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Creare un oggetto di rete front-end"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Creare un oggetto di Server DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Copia della regola RDP predefinita"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Regola AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Icona di reindirizzamento dell'applicazione"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Icona NAT di destinazione"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Passare a icona"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Regola di gestione del firewall"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Regola RDP firewall"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Regola Web firewall"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Regola AppVM01 firewall"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Regola in uscita firewall"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Regola DNS firewall"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Regola VNet all'interno del firewall"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Regola di negazione firewall"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Attivazione di regole firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
