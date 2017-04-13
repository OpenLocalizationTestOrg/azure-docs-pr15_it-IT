<properties 
   pageTitle="Cosa sono route definiti dall'utente e indirizzi IP inoltro?"
   description="Informazioni su come utilizzare route di definite dall'utente (UDR) e indirizzi IP di inoltro per inoltrare il traffico per dispositivi di rete virtuale in Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="what-are-user-defined-routes-and-ip-forwarding"></a>Quali sono le route definite dall'utente e inoltro IP?
Quando si aggiungono macchine () a una rete virtuale (VNet) in Azure, si noterà che le macchine virtuali siano in grado di comunicare con loro tramite la rete, automaticamente. Non è necessario specificare un gateway, anche se le macchine virtuali sono in subnet diverse. Lo stesso avviene per le comunicazioni da macchine virtuali a Internet, e anche ai propri locali nella rete quando una connessione ibrido Azure Data Center presente.

Questo flusso di comunicazione è possibile perché Azure utilizza una serie di route di sistema per definire il flusso di traffico IP. Indirizza sistema controllare il flusso di comunicazione negli scenari seguenti:

- Dalla stessa subnet.
- Da una subnet all'interno di un VNet.
- Da macchine virtuali a Internet.
- Da VNet a un'altra VNet tramite un gateway VPN.
- Da VNet alla rete locale tramite un gateway VPN.

La figura seguente mostra una configurazione semplice con un VNet, due subnet e alcuni macchine virtuali, insieme a cui consentano il traffico IP le route di sistema.

![Route di sistema in Azure](./media/virtual-networks-udr-overview/Figure1.png)

Anche se l'utilizzo di route di sistema facilita il traffico automaticamente per la distribuzione, esistono casi in cui si desidera controllare il routing di pacchetti tramite un dispositivo virtuale. È possibile pertanto creando indirizza definite dall'utente specificare hop successivo per i pacchetti che scorre a una subnet specifica per passare al dispositivo virtuale invece e attivazione dell'inoltro IP per la macchina virtuale in esecuzione come dispositivo virtuale.

La figura seguente mostra un esempio di route definite dall'utente e inoltro IP forzare pacchetti inviata a una subnet da un altro per passare a un dispositivo virtuale in una terza subnet.

![Route di sistema in Azure](./media/virtual-networks-udr-overview/Figure2.png)

>[AZURE.IMPORTANT] Indirizza definite dall'utente vengono applicate solo al traffico da una subnet. non è possibile creare route per specificare come il traffico entra in una subnet da Internet, ad esempio. Inoltre, accessorio che si inoltrano il traffico non può essere nella stessa subnet in cui il traffico è stata creata. Crea sempre una subnet separata per i dispositivi. 

## <a name="route-resource"></a>La distribuzione delle risorse
I pacchetti inoltrati in una rete TCP/IP in base a una tabella di routing definita in ogni nodo sulla rete fisica. Una tabella di route è un insieme di singoli route utilizzato per decidere dove inoltrare pacchetti in base all'indirizzo IP di destinazione. Una route è costituito da:

|Proprietà|Descrizione|Vincoli|Considerazioni|
|---|---|---|---|
| Prefisso dell'indirizzo | CIDR di destinazione a cui viene applicata la route, ad esempio 10.1.0.0/16.|È necessario essere un intervallo CIDR valido che rappresenta gli indirizzi di Internet pubblica, Azure virtuali o Data Center locale.|Verificare che il **prefisso dell'indirizzo** non contenga l'indirizzo per l' **indirizzo hop successivo**, in caso contrario i pacchetti entrerà in un ciclo contenuto dal sito di origine per il passaggio successivo senza raggiunga la destinazione. |
| Tipo di hop successivo | Il tipo di hop Azure il pacchetto deve essere inviato. | Deve essere uno dei seguenti valori: <br/> **Rete virtuale**. Rappresenta la rete locale virtuale. Ad esempio, se si dispone di due subnet, 10.1.0.0/16 e 10.2.0.0/16 nella stessa rete virtuale, la distribuzione per ogni subnet della tabella avrà un valore di hop successivo di *Rete virtuale*. <br/> **Gateway di rete virtuale**. Rappresenta un Gateway VPN S2S Azure. <br/> **Internet**. Rappresenta il gateway Internet predefinito fornito dall'infrastruttura di Azure. <br/> **Dispositivo virtuale**. Rappresenta un dispositivo virtuale che è stato aggiunto alla rete virtuale Azure. <br/> **Nessuno**. Rappresenta un problema di colore nero. Non verrà inoltrati i pacchetti inoltrati a un problema di colore nero.| Valutare la possibilità di utilizzo di un tipo **Nessuno** per smettere di pacchetti per adattarlo a una destinazione specificata. | 
| Indirizzo hop successivo | L'indirizzo hop successivo contiene l'indirizzo IP pacchetti devono essere inoltrati a. Valori hop successivi sono consentiti solo in indirizza dove il tipo di hop successivo è *Dispositivo virtuale*.| Deve essere un indirizzo IP raggiungibile all'interno della rete virtuale in cui è applicata Route definite dall'utente. | Se l'indirizzo IP rappresenta una macchina virtuale, verificare che si abilita [inoltro IP](#IP-forwarding) in Azure per la macchina virtuale. |

In Azure PowerShell alcuni valori "NextHopType" hanno nomi diversi:
- Rete virtuale è VnetLocal
- Gateway di rete virtuale è VirtualNetworkGateway
- Dispositivo virtuale è VirtualAppliance
- Internet è Internet
- Nessuno è None

### <a name="system-routes"></a>Route di sistema
Ogni subnet creato in una rete virtuale viene automaticamente associato a una tabella di route che contiene le regole di route di sistema seguenti:

- **Regola Vnet locale**: questa regola viene creata automaticamente per ogni subnet in una rete virtuale. Specifica che è presente un collegamento diretto tra macchine virtuali nella VNet e non esiste alcun intermedio hop successivo.
- **Regola locale**: questa regola si applica a tutto il traffico destinato all'intervallo di indirizzi in locale e utilizza gateway VPN come destinazione hop successiva.
- **Regola Internet**: questo gestisce regola tutto il traffico destinato a Internet pubblica (indirizzo prefisso 0.0.0.0/0) e viene utilizzato il gateway internet infrastruttura come hop successivo per tutto il traffico destinata a Internet.

### <a name="user-defined-routes"></a>Indirizza definite dall'utente
Per gli ambienti la maggior parte delle è necessario solo route sistema già definite da Azure. Tuttavia, è necessario creare una tabella di route e aggiungere uno o più percorsi in casi specifici, ad esempio:

- Forzare connettersi a Internet tramite la rete locale.
- Uso dei dispositivi di rete, nell'ambiente Azure.

Negli scenari, sarà necessario creare una tabella di route e aggiungervi indirizza definite dall'utente. È possibile creare più tabelle la distribuzione e la stessa tabella route può essere associata a una o più subnet. E ogni subnet solo possono essere associati a una tabella singola route. Tutte le macchine virtuali di servizi cloud in uso una subnet nella tabella route associati a tale subnet.

Subnet si basano su route di sistema fino a ottenere una route associato alla subnet. Dopo che è presente un'associazione, routing viene eseguito in base in più lunga prefisso Confronta (LPM) tra indirizza definite dall'utente e route di sistema. Se è presente più di una route con la stessa corrispondenza LPM è selezionata una route in base a origine nell'ordine seguente:

1. Route definita utente
1. Distribuzione BGP (quando viene utilizzata ExpressRoute)
1. Distribuzione di sistema

Per informazioni su come creare route definite dall'utente, vedere [come creare indirizza ed Enable IP Forwarding in Azure](virtual-network-create-udr-arm-template.md).

>[AZURE.IMPORTANT] Indirizza definite dall'utente vengono applicate solo ai servizi cloud e macchine virtuali di Azure. Ad esempio, se si desidera aggiungere un dispositivo virtuale firewall tra la rete locale e Azure, sarà necessario creare una route definite dall'utente per le tabelle di Azure route che inoltra tutto il traffico passando allo spazio di indirizzi locale per il dispositivo virtuale. È inoltre possibile aggiungere una route definite dall'utente (UDR) GatewaySubnet da inoltrare tutto il traffico in locale in Azure tramite il dispositivo virtuale. Questo è un recente.

### <a name="bgp-routes"></a>Indirizza BGP
Se si dispone di una connessione ExpressRoute tra la rete locale e Azure, è possibile abilitare BGP propagare route dalla rete locale in Azure. Queste route BGP vengono usate nello stesso modo come route di sistema e indirizza definite dall'utente in ogni subnet Azure. Per ulteriori informazioni, vedere [Introduzione ExpressRoute](../expressroute/expressroute-introduction.md).

>[AZURE.IMPORTANT] È possibile configurare l'ambiente di Azure per usare forza tunnel su rete locale mediante la creazione di una route definita utente per 0.0.0.0/0 subnet che utilizza il gateway VPN come hop successivo. Tuttavia, funziona solo se si utilizza un gateway VPN, non ExpressRoute. Per ExpressRoute, tunneling forzata viene configurato tramite BGP.

## <a name="ip-forwarding"></a>Inoltro IP
Come descritto in precedenza, uno dei motivi principali per creare una route definita utente consiste nell'inoltrare il traffico verso un dispositivo virtuale. Un dispositivo virtuale è semplicemente una macchina virtuale che viene eseguita in un'applicazione per gestire il traffico di rete in alcuni casi, ad esempio un firewall o un dispositivo NAT.

Questo dispositivo virtuale macchine Virtuali devono essere in grado di ricevere il traffico in ingresso non indirizzati a se stesso. Per consentire una macchina virtuale per ricevere il traffico indirizzato ad altre destinazioni, è necessario abilitare inoltro IP per la macchina virtuale. Si tratta di un Azure impostazione, non nel sistema operativo guest.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare route nel modello di distribuzione Manager delle risorse](virtual-network-create-udr-arm-template.md) e associarli a subnet. 
- Informazioni su come [creare route nel modello di distribuzione classica](virtual-network-create-udr-classic-ps.md) e associarli a subnet.
