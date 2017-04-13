<properties
   pageTitle="Implementare un'architettura di rete ibrido con Azure e locale VPN | Microsoft Azure"
   description="Come implementare un'architettura di rete protetta da sito che si estende su una rete virtuale Azure e una rete locale connesso tramite una connessione VPN."
   services=""
   documentationCenter="na"
   authors="RohitSharma-pnp"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="roshar"/>

# <a name="implementing-a-hybrid-network-architecture-with-azure-and-on-premises-vpn"></a>Implementare un'architettura di rete ibrido con Azure e VPN locale

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Questo articolo illustra un insieme di procedure consigliate per l'estensione di una rete locale su Azure con un sito a sito private virtuali (VPN). Flusso del traffico tra la rete locale e una rete virtuale Azure (VNet) tramite un tunnel VPN IPSec. Questa architettura è adatta per applicazioni ibride con le caratteristiche seguenti:

- Parti dell'applicazione eseguire locale mentre altri vengono eseguiti in Azure.

- Il traffico tra hardware in locale e nel cloud è probabile che sia chiaro oppure, è opportuno scambi leggermente estesa latenza per della flessibilità elaborazione del cloud.

- La rete "Extended" costituisce un sistema chiuso. Non esiste alcun percorso diretto da Internet VNet Azure.

- Connessione degli utenti alla rete locale per usare i servizi ospitati in Azure. Collegamento tra la rete locale e i servizi in esecuzione in Azure è trasparente agli utenti.

Esempi di scenari appropriati per il profilo:

- Applicazioni line-of-business utilizzate all'interno dell'organizzazione, in cui parte della funzionalità è stata eseguita la migrazione nel cloud.

- Sviluppo/laboratorio carichi di lavoro.

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione: [Gestione risorse di Azure] [ resource-manager-overview] e classica. Questo progetto utilizza Manager delle risorse, Microsoft consiglia per le distribuzioni di nuove.

## <a name="architecture-diagram"></a>Diagramma dell'architettura

Nel diagramma seguente evidenzia i componenti di questa architettura:

> Un documento di Visio che include questo diagramma architettura è disponibile per il download all' [area download Microsoft][visio-download]. Il diagramma è "rete ibrido - VPN" pagina.

![[0]][0]

- **Rete locale.** Si tratta di una rete di computer e dispositivi, la connessione tramite una rete LAN privata in esecuzione all'interno dell'organizzazione.

- **Accessorio VPN.** Si tratta di un dispositivo o servizio che fornisce connettività esterne alla rete locale. Accessorio VPN sia un dispositivo hardware o può essere una soluzione di software, ad esempio il Routing e remoto accesso servizio () in Windows Server 2012.

> [AZURE.NOTE] Per un elenco dei dispositivi VPN supportati e informazioni sulla configurazione di dispositivi VPN selezionate per la connessione a un Gateway VPN Azure, vedere le istruzioni per il dispositivo appropriato nell' [elenco dei dispositivi VPN supportati da Azure][vpn-appliance].

- **Applicazione di livelli cloud.** Si tratta dell'applicazione presente nel Azure. Può includere più livelli, con più subnet la connessione tramite servizi di bilanciamento del carico Azure. Il traffico in ogni subnet può essere soggetto a regole definite mediante i [Gruppi di sicurezza di Azure rete (NSGs)][azure-network-security-group]. Per ulteriori informazioni, vedere [Guida introduttiva a Microsoft Azure sicurezza][getting-started-with-azure-security].

> [AZURE.NOTE] In questo articolo vengono illustrati l'applicazione cloud come una singola entità. Vedere [esecuzione di un'architettura di livelli in Azure] [ implementing-a-multi-tier-architecture-on-Azure] per informazioni dettagliate.

- **[Rete virtuale (VNet)][azure-virtual-network].** Applicazione cloud e i componenti per il Gateway VPN Azure si trovano nella stessa VNet.

- **[Gateway VPN Azure][azure-vpn-gateway].** Il servizio gateway VPN consente di connettere il VNet alla rete locale tramite un accessorio VPN. Per ulteriori informazioni, vedere [connettersi a una rete locale a una rete Microsoft Azure][connect-to-an-Azure-vnet]. Il Gateway VPN comprende gli elementi seguenti:

  - **Gateway di rete virtuale.** Si tratta di una risorsa che fornisce un dispositivo VPN virtuale per la VNet. È responsabile per instradare il traffico di rete locale per la VNet.

  - **Gateway di rete locale.** Si tratta di un'estrazione di accessorio VPN locale. Tramite il gateway è indirizzato il traffico di rete dall'applicazione cloud alla rete locale.

  - **Connessione.** Proprietà che specificano il tipo di connessione (IPSec) e la chiave condivisi con l'accessorio VPN locale per crittografare il traffico la connessione.

  - **Subnet gateway.** Il gateway virtuali viene mantenuto nella propria subnet, è soggetto a vari requisiti, come descritto nella sezione suggerimenti.

- **Servizio di bilanciamento del carico interno.** Il traffico di rete da Gateway VPN viene indirizzato all'applicazione cloud tramite un servizio di bilanciamento del carico interno. Bilanciamento del carico si trova nella subnet front-end dell'applicazione.

## <a name="recommendations"></a>Consigli

Azure offre molte risorse diverse e tipi di risorse, in modo che l'architettura di riferimento può essere completato il provisioning molti modi diversi. Sono disponibili un modello di gestione di risorse Azure per installare l'architettura di riferimento che segue questi suggerimenti. Se si sceglie di creare il proprio architettura di riferimento è necessario seguire queste raccomandazioni, a meno che non si dispone di un requisito specifico non appartenente a un suggerimento.

### <a name="vnet-and-gateway-subnet"></a>Subnet VNet e al Gateway

Creare un VNet Azure per l'esenzione dei componenti nel cloud. Spazio di indirizzi VNet Azure deve essere sufficiente per gli indirizzi utilizzati in macchine virtuali e subnet il VNet. Assicurarsi che lo spazio di indirizzi VNet disponga di spazio sufficiente per la crescita se altre macchine virtuali saranno probabilmente necessarie in futuro. Spazio di indirizzi di VNet deve non si sovrappongono con la rete locale. Il diagramma precedente, ad esempio, utilizza la 10.20.0.0/16 spazio indirizzo per la VNet.

Creare una subnet denominata _GatewaySubnet_, con un intervallo di indirizzi di /27. Subnet è richiesto dal gateway virtuali e l'assegnazione di 32 indirizzi a questa subnet consente di impedire l'esecuzione e limiti delle dimensioni possibili gateway in futuro. Evitare l'inserimento di subnet nel centro lo spazio di indirizzi. Buona norma è impostare lo spazio di indirizzi della subnet gateway all'estremità superiore dello spazio di indirizzi VNet. Nell'esempio illustrato nella figura viene utilizzato 10.20.255.224/27.  Una procedura veloce per calcolare il CIDR è il seguente:

1. Impostare i bit variabili nello spazio di indirizzi di VNet a 1, fino a bit viene utilizzato in base alla subnet gateway, quindi impostare i bit rimanenti su 0.

2. Convertire i bit risultanti in decimale ed esprimere come uno spazio indirizzo con il set di lunghezza prefisso a ottenere le dimensioni della subnet gateway.

Ad esempio, per VNet con un indirizzo IP di 10.20.0.0/16, applicare il passaggio 1 # diventa 10.20.0b11111111.0b11100000.  Che la conversione in decimale ed esprimendo nel momento in uno spazio indirizzo produce 10.20.255.224/27

> [AZURE.WARNING] Non distribuire altre macchine virtuali o istanze del ruolo alla subnet gateway. Inoltre, non assegnare un NSG alla subnet, come causerà il gateway non funzionerà.

### <a name="virtual-network-gateway"></a>Gateway di rete virtuale

Allocare un indirizzo IP pubblico per il gateway di rete virtuale.

Creare il gateway virtuali subnet Gateway e assegnare l'indirizzo IP pubblico appena allocato. Utilizzare il tipo di gateway che corrisponde maggiormente ai propri requisiti e che è attivata per il dispositivo VPN:

Creare un [gateway in base a criteri] [ policy-based-routing] se è necessario controllare strettamente come le richieste vengono indirizzate. in base a criteri, ad esempio prefissi degli indirizzi. Gateway in base a criteri di usare il routing statico e funzionano solo con le connessioni al sito.

Creare un [gateway basati su route] [ route-based-routing] se ci si connette alla rete locale utilizzando configurazione, supporta anche connessioni più siti o area geografica tra o implementare connessioni VNet-VNet (inclusi indirizza che scorrono VNets più). In base a instradare gateway utilizzano il routing dinamico per il traffico diretto tra reti. Possono tollerare errori nel percorso di rete migliore route statiche, poiché tentano route alternative. Gateway basate su route è possibile anche ridurre il sovraccarico di gestione, poiché indirizza potrebbe non devono essere aggiornati manualmente quando cambiano gli indirizzi di rete.

Per un elenco dei dispositivi VPN supportati, vedere [dispositivi sulle VPN per le connessioni da sito VPN Gateway][vpn-appliances].

> [AZURE.NOTE] Dopo aver creato il gateway, non è possibile modificare tra tipi di gateway senza eliminare e ricreare il gateway.

Selezionare lo SKU di Gateway VPN Azure che corrisponde maggiormente ai propri requisiti di velocità. Azure Gateway VPN è disponibile in tre SKU illustrato nella tabella seguente. Ogni SKU fornisce diversi di trasmissione, [in base alle tariffe, effettuati] [ azure-gateway-charges] in base alla quantità di tempo in cui viene eseguito il provisioning il gateway e disponibile.

| SKU | Produttività VPN | Tunnel max IPSec|
|-----|----------------|------------------|
| Base | 100 Mbps | 10 |
| Standard | 100 Mbps | 10 |
| Prestazioni elevate | 200 Mbps | 30 |

> [AZURE.NOTE] SKU base non è compatibile con Azure ExpressRoute. È possibile [modificare lo SKU] [ changing-SKUs] dopo aver creato il gateway.

Creare regole di distribuzione per subnet gateway del traffico in ingresso diretto applicazione da gateway per il servizio di bilanciamento del carico interno anziché consentire richieste passare direttamente alle macchine virtuali di implementano dell'applicazione.

### <a name="on-premises-network-connection"></a>Connessione di rete locale

Creare un gateway di rete locale. Specificare l'indirizzo IP pubblico dell'apparecchio VPN locale e lo spazio di indirizzi della rete locale. Si noti che l'accessorio VPN locale deve avere un indirizzo IP pubblico accessibili tramite il gateway di rete locale in Azure VPN Gateway. Non è possibile individuare il dispositivo VPN dietro un dispositivo NAT.

Creare una connessione al sito per il gateway virtuali e il gateway di rete locale. Selezionare il tipo di connessione a siti (IPSec) e specificare la chiave condivisa. La crittografia da sito con il Gateway VPN Azure si basa sul protocollo IPSec, utilizzando chiavi condivise per l'autenticazione. Specificare la chiave quando si crea il Gateway VPN Azure. È necessario configurare il dispositivo di VPN che esegue locale con la stessa chiave. Altri metodi di autenticazione non sono attualmente supportati.

Assicurarsi che l'infrastruttura di routing è configurato per inoltrare le richieste per gli indirizzi di VNet Azure al dispositivo VPN in locale.

Aprire le porte necessarie all'applicazione cloud nella rete locale.

Verificare la connessione per verificare che:

- Accessorio VPN locale correttamente indirizza il traffico all'applicazione cloud tramite il Gateway VPN Azure.

- Il VNet correttamente indirizza il traffico alla rete locale.

- Il traffico non consentito in entrambe le direzioni è bloccato correttamente.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

È possibile ottenere scalabilità verticale limitata spostando dal Basic o SKU di Gateway VPN Standard lo SKU VPN prestazioni elevato.

Per VNets che prevede un volume elevato di traffico VPN, è consigliabile distribuire i carichi di lavoro diversi in VNets minori separata e configura un Gateway VPN per ognuno di essi.

È possibile suddividere il VNet orizzontalmente o verticalmente. Per suddividere in senso orizzontale, spostare alcuni casi macchine Virtuali da ogni livello in subnet di VNet nuovo. Il risultato è che ogni VNet ha la stessa struttura e funzionalità. Per suddividere in verticale, riprogettare ogni livello per dividere la funzionalità in diverse aree logiche (ad esempio, la gestione degli ordini, fatturazione, la gestione degli account cliente e così via). Ogni area funzionale può essere inserito nella propria VNet.

La replica di un controller di dominio Active Directory locale la VNet e l'implementazione di DNS in VNet, possono aiutare a limitare il protezione e amministrazione del flusso di traffico locali al cloud.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Se è necessario assicurarsi che la rete locale rimane disponibile per il Gateway VPN Azure, implementare un cluster di failover per il Gateway VPN locale.

Se l'organizzazione ha più siti locale, creare [connessioni più siti] [ vpn-gateway-multi-site] a uno o più VNets Azure. Questo approccio richiede (basato su route) il routing dinamico, verificare che il Gateway VPN locale supporta questa funzionalità.

Vedere [contratto di servizio per Gateway VPN] [ sla-for-vpn-gateway] per informazioni dettagliate sul contratto di servizio Gateway VPN.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Eseguire il monitoraggio delle informazioni di diagnostica da elettrodomestici VPN locale. Questo processo dipende dalle funzionalità fornita dall'accessorio VPN. Ad esempio, se si utilizza il Routing e accesso remoto in Windows Server 2012, è necessario abilitare [la registrazione di configurazione][rras-logging].

Usare [Gateway VPN Azure diagnostica] [ gateway-diagnostic-logs] per acquisire informazioni sui problemi di connettività. I registri possono essere utilizzati per tenere traccia delle informazioni, ad esempio l'origine e destinazione di connessione richieste, il protocollo utilizzato e come è stata stabilita la connessione (o perché non è riuscito).

Monitorare i registri operativi del Gateway VPN Azure utilizzando i registri di controllo disponibili nel portale di Azure. File di log distinti sono disponibili per il gateway di rete locale, il gateway di rete Azure e la connessione. Queste informazioni possono essere usate per tenere traccia delle modifiche apportate al gateway e possono essere utile se un gateway precedentemente funziona si interrompe per qualche motivo.

![[2]][2]

Controllare la connettività e tenere traccia degli eventi di errore di integrazione applicativa. È possibile utilizzare un pacchetto di monitoraggio, ad esempio [Nagios] [ nagios] per acquisire e inviare una segnalazione queste informazioni.

## <a name="security-considerations"></a>Considerazioni sulla protezione

Generare una chiave condivisa diversa per ogni gateway VPN. Utilizzare una chiave condivisa avanzata elaborate tentativi.

> [AZURE.NOTE] Attualmente, è possibile utilizzare Azure chiave archivio al gateway VPN Azure chiavi condivise.

Assicurarsi che il dispositivo di VPN locale utilizza un metodo di crittografia compatibile [con il Gateway VPN Azure][vpn-appliance-ipsec]. Per il routing basato sui criteri, il Gateway VPN Azure supporta algoritmi AES256, AES128 e 3DES. In base route gateway supporta AES256 e 3DES.

Se il dispositivo di VPN locale si trova in una rete perimetrale che dispone di un firewall tra la rete perimetrale e Internet, è necessario configurare [le regole firewall aggiuntivo] [ additional-firewall-rules] per consentire la connessione VPN da sito.

Se l'applicazione di VNet invia dati a Internet, è possibile [implementare tunneling forzata] [ forced-tunneling] per indirizzare tutto il traffico Internet associazione tramite la rete locale. Questo approccio consente di controllare le richieste in uscita effettuate dall'applicazione dalla infrastruttura locale.

> [AZURE.NOTE] Tunneling forzata può influire sulla connettività a servizi Azure (ad esempio il servizio di archiviazione) e gestione delle licenze di Windows.

## <a name="troubleshooting-considerations"></a>Considerazioni sulla risoluzione dei problemi

> [AZURE.NOTE] Visitare il Routing e Blog accesso remoto per informazioni generali sulla [risoluzione di errori comuni relativi a VPN][troubleshooting-vpn-errors].

Se il traffico non riesce a scorrere la connessione VPN, verificare quanto segue:

### <a name="on-premises-vpn-appliance"></a>Accessorio VPN locale:

**Accessorio VPN locale funzioni correttamente.**

Controllo qualsiasi file di registro generati dall'accessorio VPN per gli errori e gli errori. Il percorso di queste informazioni variano in base al dispositivo. Ad esempio, se si utilizza configurazione in Windows Server 2012, è possibile utilizzare il comando PowerShell seguente per visualizzare informazioni sugli eventi di errore per il servizio di configurazione:

```
Get-EventLog -LogName System -EntryType Error -Source RemoteAccess | Format-List -Property *
```

La proprietà di _messaggio_ di ogni voce viene fornita una descrizione dell'errore. Alcuni esempi comuni sono:

- Impossibilità di connettersi, probabilmente a causa di un indirizzo IP specificato per il Gateway VPN Azure nella configurazione dell'interfaccia di rete VPN RRAS:

```
EventID            : 20111
MachineName        : on-prem-vm
Data               : {41, 3, 0, 0}
Index              : 14231
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A Demand Dial connection to the remote
                     interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                     successfully because of the  following error: The network connection between your computer and
                     the VPN server could not be established because the remote server is not responding. This could
                     be because one of the network devices (e.g, firewalls, NAT, routers, etc) between your computer
                     and the remote server is not configured to allow VPN connections. Please contact your
                     Administrator or your service provider to determine which device may be causing the problem.
Source             : RemoteAccess
ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, The network connection between
                     your computer and the VPN server could not be established because the remote server is not
                     responding. This could be because one of the network devices (e.g, firewalls, NAT, routers, etc)
                     between your computer and the remote server is not configured to allow VPN connections. Please
                     contact your Administrator or your service provider to determine which device may be causing the
                     problem.}
InstanceId         : 20111
TimeGenerated      : 3/18/2016 1:26:02 PM
TimeWritten        : 3/18/2016 1:26:02 PM
UserName           :
Site               :
Container          :
```

- La chiave condivisa errata viene specificata nella configurazione dell'interfaccia di rete VPN RRAS:

```
EventID            : 20111
MachineName        : on-prem-vm
Data               : {233, 53, 0, 0}
Index              : 14245
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A Demand Dial connection to the remote
                     interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                     successfully because of the  following error: IKE authentication credentials are unacceptable

Source             : RemoteAccess
ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, IKE authentication credentials are
                     unacceptable
                     }
InstanceId         : 20111
TimeGenerated      : 3/18/2016 1:34:22 PM
TimeWritten        : 3/18/2016 1:34:22 PM
UserName           :
Site               :
Container          :
```

È inoltre possibile ottenere informazioni del registro eventi sui tentativi di connettersi tramite il servizio di configurazione tramite il comando PowerShell seguente:

```
Get-EventLog -LogName Application -Source RasClient | Format-List -Property *
```

In caso di errore per la connessione, il registro conterrà gli errori con un aspetto simili al seguente:

```
EventID            : 20227
MachineName        : on-prem-vm
Data               : {}
Index              : 4203
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : CoId={B4000371-A67F-452F-AA4C-3125AA9CFC78}: The user SYSTEM dialed a connection named
                     AzureGateway which has failed. The error code returned on failure is 809.
Source             : RasClient
ReplacementStrings : {{B4000371-A67F-452F-AA4C-3125AA9CFC78}, SYSTEM, AzureGateway, 809}
InstanceId         : 20227
TimeGenerated      : 3/18/2016 1:29:21 PM
TimeWritten        : 3/18/2016 1:29:21 PM
UserName           :
Site               :
Container          :
```

**È il VPN accessorio correttamente il routing del traffico attraverso il Gateway VPN Azure?**

Utilizzare uno strumento, ad esempio [PsPing] [ psping] per verificare la connettività e routing attraverso il gateway VPN. Ad esempio, per testare la connettività da un computer locale in un server web sul VNet, eseguire il comando seguente (sostituire `<<web-server-address>>` con l'indirizzo del server web):

```
PsPing -t <<web-server-address>>:80
```

Se il computer locale possa indirizzare il traffico verso il server web, è necessario verificare output simile al seguente:

```
D:\PSTools>psping -t 10.20.0.5:80

PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2014 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.20.0.5:80:
Infinite iterations (warmup 1) connecting test:
Connecting to 10.20.0.5:80 (warmup): 6.21ms
Connecting to 10.20.0.5:80: 3.79ms
Connecting to 10.20.0.5:80: 3.44ms
Connecting to 10.20.0.5:80: 4.81ms

  Sent = 3, Received = 3, Lost = 0 (0% loss),
  Minimum = 3.44ms, Maximum = 4.81ms, Average = 4.01ms
```

Se il computer locale non è possibile comunicare con la destinazione specificata, verrà visualizzato i messaggi come segue:

```
D:\PSTools>psping -t 10.20.1.6:80

PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2014 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.20.1.6:80:
Infinite iterations (warmup 1) connecting test:
Connecting to 10.20.1.6:80 (warmup): This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80:
  Sent = 3, Received = 0, Lost = 3 (100% loss),
  Minimum = 0.00ms, Maximum = 0.00ms, Average = 0.00ms
```

**Firewall locale consentire il traffico VPN pass-through? Sono stati aperti porte corrette?**

Verificare che il dispositivo di VPN locale utilizza un metodo di crittografia compatibile [con il Gateway VPN Azure][vpn-appliance].

Per il routing basato sui criteri, il Gateway VPN Azure supporta algoritmi AES256, AES128 e 3DES. In base route gateway supporta AES256 e 3DES.

### <a name="azure-vnet-gateway"></a>Azure VNet Gateway:

Esaminare i [registri diagnostici Gateway VPN Azure] [ gateway-diagnostic-logs] i potenziali problemi.

**Sono i Gateway VPN Azure e accessorio VPN locale configurato con la stessa chiave di autenticazione condivisa?**

È possibile visualizzare la chiave condivisa archiviata dal Gateway VPN Azure tramite il comando CLI Azure seguente:

```
azure network vpn-connection shared-key show <<resource-group>> <<vpn-connection-name>>
```

Usare il comando appropriato per il dispositivo di VPN locale per mostrare la chiave condivisa configurata per tale accessorio.

Verificare che la subnet _GatewaySubnet_ tenendo che Azure VPN Gateway non è associato a un NSG.

È possibile visualizzare i dettagli di subnet tramite il comando CLI Azure seguente:

```
azure network vnet subnet show -g <<resource-group>> -e <<vnet-name>> -n GatewaySubnet
```

Assicurarsi che non esiste alcun campo dati non denominato _id di gruppo di sicurezza di rete_. Nell'esempio seguente mostra i risultati, ad esempio di _GatewaySubnet_ che contiene un NSG assegnate (_Gruppo di Gateway VPN_). In questo modo evitare che il gateway non funziona correttamente se sono presenti tutte le regole definite per questo NSG:

```
C:\>azure network vnet subnet show -g profx-prod-rg -e profx-vnet -n GatewaySubnet
    info:    Executing command network vnet subnet show
    + Looking up virtual network "profx-vnet"
    + Looking up the subnet "GatewaySubnet"
    data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/virtualNetworks/profx-vnet/subnets/GatewaySubnet
    data:    Name                            : GatewaySubnet
    data:    Provisioning state              : Succeeded
    data:    Address prefix                  : 10.20.3.0/27
    data:    Network Security Group id       : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/networkSecurityGroups/VPN-Gateway-Group
    info:    network vnet subnet show command OK
```

**Sono macchine virtuali in VNet Azure configurati per consentire il traffico proveniente da di fuori di VNet? Selezionare tutte le regole NSG associate subnet contenente queste macchine virtuali.**

È possibile visualizzare tutte le regole NSG tramite il comando CLI Azure seguente:

```
azure network nsg show -g <<resource-group>> -n <<nsg-name>>
```

**È stato disconnesso il Gateway VPN Azure?**

È possibile utilizzare il comando seguente Azure PowerShell per controllare lo stato corrente della connessione VPN Azure. Il `<<connection-name>>` parametro è il nome della connessione VPN Azure che collega il gateway virtuali e gateway locale.

```
Get-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> - ResourceGroupName <<resource-group>>
```

Frammenti seguenti evidenziare l'output generato se il gateway è connesso (cognome) e interrompe la connessione (il secondo esempio):

```
PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection -ResourceGroupName profx-prod-rg

AuthorizationKey           :
VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
VirtualNetworkGateway2     :
LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
Peer                       :
ConnectionType             : IPsec
RoutingWeight              : 0
SharedKey                  : ####################################
ConnectionStatus           : Connected
EgressBytesTransferred     : 55254803
IngressBytesTransferred    : 32227221
ProvisioningState          : Succeeded
...
```

```
PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection2 -ResourceGroupName profx-prod-rg

AuthorizationKey           :
VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
VirtualNetworkGateway2     :
LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
Peer                       :
ConnectionType             : IPsec
RoutingWeight              : 0
SharedKey                  : ####################################
ConnectionStatus           : NotConnected
EgressBytesTransferred     : 0
IngressBytesTransferred    : 0
ProvisioningState          : Succeeded
...
```

### <a name="host-vm-configuration-network-bandwidth-utilization-and-application-performance"></a>Configurazione di macchine Virtuali host, utilizzo della larghezza di banda di rete e le prestazioni dell'applicazione:

Verificare che il firewall nel sistema operativo guest in esecuzione in macchine virtuali di Azure nella subnet siano configurati correttamente per consentire il traffico consentito da intervalli di indirizzi IP locale.

**È disponibile per il Gateway VPN Azure il volume del traffico raggiunto il limite della larghezza di banda?**

Utensili dipendono gli strumenti disponibili per il dispositivo di VPN che esegue locale. Ad esempio, se si utilizza configurazione in Windows Server 2012, è possibile utilizzare Performance Monitor per tenere traccia del volume di dati vengono ricevuti e trasmessi tramite una connessione VPN; utilizzo dell'oggetto _RAS totale_ , selezionare contatori _Byte ricevuti/Sec_ e _Byte trasmessi/Sec_ :

![[3]][3]

È necessario confrontare i risultati con la larghezza di banda disponibile per il gateway VPN (100 Mbps per le Basic SKU Standard e 200 Mbps per lo SKU prestazioni elevate):

![[4]][4]

**Sono le macchine virtuali in VNet Azure lento? Vengono che sono overload, sono sufficienti degli indicatori per gestire il carico, esistono tutti-bilanciamento configurati correttamente?**

Questa operazione richiede [l'acquisizione e l'analisi di informazioni diagnostiche][azure-vm-diagnostics]. È possibile esaminare i risultati tramite il portale di Azure, ma molti strumenti di terze parti sono anche disponibili che forniscono indicazioni dettagliate sul dati sulle prestazioni.

**È l'applicazione efficiente l'utilizzo delle risorse cloud?**

Codice di applicazione strumento eseguito su ogni macchina virtuale per determinare se le applicazioni effettuano al meglio le risorse. È possibile utilizzare strumenti, ad esempio [Applicazione approfondimenti] [ application-insights] consentono di eseguire queste attività.

## <a name="solution-deployment"></a>Distribuzione di soluzioni

Se si dispone di un'infrastruttura locale già configurata con un dispositivo VPN, è possibile distribuire l'architettura di riferimento procedendo come segue:

1. Fare clic con il pulsante destro sul pulsante e selezionare uno dei due "Apri collegamento in una nuova scheda" o "Apri collegamento in un'altra finestra":  
[![Distribuire Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn%2Fazuredeploy.json)

2. Attendere che il collegamento per aprire il portale di Azure, attenersi alla seguente procedura: 
    - Il nome del **gruppo di risorse** è già definito nel file di parametri, quindi selezionare **Crea nuovo** e immettere `ra-hybrid-vpn-rg` nella casella di testo.
    - Selezionare l'area nella casella a discesa **posizione** .
    - Non modificare il **Modello radice Uri** o caselle di testo **Parametro radice Uri** .
    - Esaminare le condizioni e quindi fare clic sulla casella di controllo **che accetto i termini e condizioni indicate in precedenza** .
    - Fare clic sul pulsante di **acquisto** .

3. Attendere che la distribuzione completare.

## <a name="next-steps"></a>Passaggi successivi

- [L'installazione di ulteriori controller di dominio Active Directory locale utilizzando macchine virtuali in un VNet Azure][installing-ad].

- [Linee guida per la distribuzione di Windows Server Active Directory in macchine virtuali di Azure][deploying-ad].

- [Creazione di un server DNS in un VNet][creating-dns].

- [Configurazione e sulla gestione dei record DNS in un VNet][configuring-dns].

- [Utilizzo di apparecchiature di sicurezza locale Stormshield rete su una connessione VPN][stormshield].

- [Implementare un'architettura di rete ibrido con Azure ExpressRoute][expressroute].

<!-- links -->

[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[arm-templates]: ../resource-group-authoring-templates.md
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-virtual-network]: ../virtual-network/virtual-networks-overview.md
[vpn-appliance]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[azure-vpn-gateway]: https://azure.microsoft.com/services/vpn-gateway/
[azure-gateway-charges]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[azure-network-security-group]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[vpn-gateway-multi-site]: ../vpn-gateway/vpn-gateway-multi-site.md
[policy-based-routing]: https://en.wikipedia.org/wiki/Policy-based_routing
[route-based-routing]: https://en.wikipedia.org/wiki/Static_routing
[network-security-group]: ../virtual-network/virtual-networks-nsg.md
[sla-for-vpn-gateway]: https://azure.microsoft.com/support/legal/sla/vpn-gateway/v1_0/
[additional-firewall-rules]: https://technet.microsoft.com/library/dn786406.aspx#firewall
[nagios]: https://www.nagios.org/
[azure-vpn-gateway-diagnostics]: http://blogs.technet.com/b/keithmayer/archive/2014/12/18/diagnose-azure-virtual-network-vpn-connectivity-issues-with-powershell.aspx
[ping]: https://technet.microsoft.com/library/ff961503.aspx
[tracert]: https://technet.microsoft.com/library/ff961507.aspx
[psping]: http://technet.microsoft.com/sysinternals/jj729731.aspx
[nmap]: http://nmap.org
[changing-SKUs]: https://azure.microsoft.com/blog/azure-virtual-network-gateway-improvements/
[gateway-diagnostic-logs]: http://blogs.technet.com/b/keithmayer/archive/2015/12/07/step-by-step-capturing-azure-resource-manager-arm-vnet-gateway-diagnostic-logs.aspx
[troubleshooting-vpn-errors]: https://blogs.technet.microsoft.com/rrasblog/2009/08/12/troubleshooting-common-vpn-related-errors/
[rras-logging]: https://www.petri.com/enable-diagnostic-logging-in-windows-server-2012-r2-routing-and-remote-access
[create-on-prem-network]: https://technet.microsoft.com/library/dn786406.aspx#routing
[create-azure-vnet]: ../virtual-network/virtual-networks-create-vnet-classic-cli.md
[azure-vm-diagnostics]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[application-insights]: ../application-insights/app-insights-overview-usage.md
[forced-tunneling]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-forced-tunneling/
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[vpn-appliances]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[installing-ad]: ../active-directory/active-directory-install-replica-active-directory-domain-controller.md
[deploying-ad]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[creating-dns]: https://blogs.msdn.microsoft.com/mcsuksoldev/2014/03/04/creating-a-dns-server-in-azure-iaas/
[configuring-dns]: ../virtual-network/virtual-networks-manage-dns-in-vnet.md
[stormshield]: https://azure.microsoft.com/marketplace/partners/stormshield/stormshield-network-security-for-cloud/
[vpn-appliance-ipsec]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec-parameters
[expressroute]: ./guidance-hybrid-network-expressroute.md
[naming conventions]: ./guidance-naming-conventions.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/deploy-reference-architecture.sh
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetwork.parameters.json
[virtualNetworkGateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetworkGateway.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./media/blueprints/hybrid-network-vpn.png "Struttura di una distribuzione ibrida di rete che si estendono le locali e cloud infrastrutture"
[1]: ./media/guidance-hybrid-network-vpn/partitioned-vpn.png "Partizioni VNet per migliorare la scalabilità"
[2]: ./media/guidance-hybrid-network-vpn/audit-logs.png "Nel portale di Azure dei registri"
[3]: ./media/guidance-hybrid-network-vpn/RRAS-perf-counters.png "Contatori delle prestazioni per controllare il traffico di rete VPN"
[4]: ./media/guidance-hybrid-network-vpn/RRAS-perf-graph.png "Grafico delle prestazioni rete VPN esempio"