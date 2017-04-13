<properties
   pageTitle="Azure architettura delle informazioni di riferimento - IaaS: Creazione di un insieme di strutture di Active Directory delle risorse in Azure | Microsoft Azure"
   description="Informazioni su come creare un dominio trusted di Active Directory in Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
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

# <a name="creating-a-active-directory-directory-services-adds-resource-forest-in-azure"></a>Creazione di un insieme di strutture di risorse di servizi di Directory Active Directory (somma) in Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

In questo articolo viene descritto come creare un dominio Active Directory in Azure che è distinto dal ma attendibile per i domini della foresta locale.

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione: [Gestione risorse] [ resource-manager-overview] e classica. L'architettura di riferimento utilizza Manager delle risorse, Microsoft consiglia per le distribuzioni di nuove.

Un'organizzazione che esegue Active Directory (AD) locale potrebbe essere un insieme di strutture che comprende molti domini diversi. Ad esempio, è possibile creare singoli domini per diversi reparti o suborganizations o nuovi domini potrebbero essere stati aggiunti come risultato di acquisizione o fusione di altre organizzazioni. È possibile usare i domini per fornire isolamento tra le aree funzionali che devono essere mantenuti separate, probabilmente per motivi di sicurezza, ma è possibile condividere informazioni tra i domini per stabilire relazioni di trust.

Le organizzazioni che utilizzano domini separati possono trarre vantaggio Azure spostando una o più di questi domini nel cloud. In alternativa, un'organizzazione potrebbe essere necessario mantenere tutte le risorse cloud in modo logico distinte dai tali tenuti locale e memorizzare informazioni sulle risorse cloud nella propria directory, in un dominio anche conservato nel cloud.

È possibile implementare Active Directory in Azure in diversi modi, come descritto in articoli [Estensione Active Directory per Azure] [ extending-ad-to-azure] e [Implementazione di Azure Active Directory][implementing-aad]. In questo documento è incentrata su uno scenario specifico: creazione di un dominio nel cloud distinta dai domini contenuti in locale, ma che può presentare una relazione di attendibilità con i domini locale. 

Casi di utilizzo tipico per questa architettura includono:

- Gestione di separazione di sicurezza per gli oggetti e dell'identità dei contenuti nel cloud.

- Eseguire la migrazione di singoli domini locali al cloud.

## <a name="architecture-diagram"></a>Diagramma dell'architettura

Nel diagramma seguente evidenzia i componenti principali in questa architettura (*fare clic su per applicare lo zoom avanti*). Per ulteriori informazioni sugli elementi inattivo, leggere [l'implementazione di un'architettura di rete ibrido sicura in Azure] [ implementing-a-secure-hybrid-network-architecture] e [implementare un'architettura di rete sicura ibrido con accesso a Internet in Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]:

[! [0]][0]

- **Rete locale.** La rete locale contiene il proprio domini e strutture di Active Directory.

- **Server di Active Directory.** Questi sono controller di dominio l'implementazione di servizi directory (AD DS) in esecuzione come macchine virtuali nel cloud. Questi server ospitano da un insieme di strutture contenente uno o più domini, separati da tali trovare locale.

- **Relazione di trust unidirezionale.** Nel diagramma viene riportato un trust unidirezionale da quello del dominio nel cloud per il dominio locale. Questa relazione consente agli utenti locali accedere alle risorse del dominio nel cloud, ma non viceversa. Si tratta di un caso comune. Tuttavia, è possibile creare un trust bidirezionale se cloud utenti richiedono anche l'accesso alle risorse locale.

- **Subnet Directory attiva.** I server Active Directory sono ospitati in una subnet separata. Le regole NSG proteggere i server Active Directory e offrono un firewall contro il traffico da origini non previsti.

- **Subnet livello Web**, Business livello alla **subnet**e **subnet livello di dati**. Queste subnet ospitano il server e i componenti che eseguono applicazioni nel cloud. Per ulteriori informazioni, vedere [Esecuzione macchine virtuali per un'architettura di livelli in Azure][running-VMs-for-an-N-tier-architecture-on-Azure]. Le risorse e macchine virtuali subnet si trovano all'interno del dominio cloud.

- **Gateway azure**. Il gateway Azure fornisce una connessione tra la rete locale e VNet Azure. Può trattarsi di una [connessione VPN] [ azure-vpn-gateway] o [Azure ExpressRoute][azure-expressroute]. Per ulteriori informazioni, vedere [implementazione di un'architettura di rete ibrido sicura in Azure][implementing-a-secure-hybrid-network-architecture].

## <a name="recommendations"></a>Consigli

In questa sezione fornisce un elenco di suggerimenti basati sui principali componenti necessari per implementare l'architettura di base. Queste raccomandazioni coprono:

- Aggiunge impostazioni, e

- Configurazione di relazione di attendibilità.

Potrebbe essere requisiti aggiuntivi o diversi da quelli descritti di seguito. È possibile utilizzare gli elementi in questa sezione come punto di partenza per considerare come personalizzare l'architettura per il proprio sistema.

### <a name="adds-recommendations"></a>Aggiunge consigli

Per indicazioni specifiche sull'implementazione di Active Directory nel cloud, fare riferimento al documento [Estensione Active Directory per Azure][extending-ad-to-azure]. L'articolo [linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure] [ ad-azure-guidelines] contiene informazioni aggiuntive.

### <a name="trust-recommendations"></a>Protezione consigli

I domini locali sono contenuti in una foresta diversa dall'elenco domini nel cloud. Per abilitare l'autenticazione degli utenti in locale nel cloud, i domini nel cloud devono attendibile il dominio di accesso dell'insieme di strutture locale. Analogamente, se la cloud fornisce un dominio di accesso per gli utenti esterni, potrebbe essere necessario per la foresta locale considerare attendibile il dominio cloud.

È possibile stabilire relazioni di trust il livello di strutture mediante la [creazione di trust][creating-forest-trusts], o a livello di dominio creando [trust esterni][creating-external-trusts]. Un trust consente di creare una relazione tra tutti i domini di due insiemi di strutture. Protezione di livello un dominio esterno crea solo una relazione tra due domini specificati. È necessario creare solo trust livello di dominio esterno tra i domini in diversi insiemi di strutture.

Possono essere trust unidirezionale (unidirezionale) o bidirezionale (bidirezionale):

- Trust unidirezionale consente agli utenti in un dominio o foresta (noto come dominio di *posta in arrivo* o nella foresta) per accedere alle risorse contenute in un altro (il dominio *in uscita* o foresta). 

- Bidirezionale consente agli utenti di dominio o struttura accedere alle risorse contenute in altro.

Nella tabella seguente vengono riepilogate le configurazioni di protezione per alcuni scenari semplice:

| Scenario: | Protezione locale | Protezione cloud |
|----------|-------------------|-------------|
| Gli utenti accedono alle risorse nel cloud, in locale ma non viceversa | Unidirezionale in ingresso | Unidirezionale in uscita |
| Gli utenti nel cloud richiedono l'accesso a risorse presenti in locale, ma non viceversa | Unidirezionale in uscita | Unidirezionale in ingresso |
| Gli utenti nel cloud e locale richiede l'accesso per le risorse utilizzate nel cloud e locale | Bidirezionale, in ingresso e in uscita | Bidirezionale, in ingresso e in uscita |

## <a name="security-considerations"></a>Considerazioni sulla protezione

Livello trust sono transitivi. Se si stabilisce un trust tra un insieme di strutture locale e un insieme di strutture nel cloud, questa protezione viene esteso ad altri domini nuovi creati in una foresta. Se si utilizzano domini per separare per motivi di sicurezza, considerare la creazione di relazioni di trust solo a livello di dominio. Trust a livello di dominio non sono transitivi.

Per motivi di sicurezza specifici di Active Directory, vedere la sezione *Considerazioni sulla protezione* di [Estensione Active Directory in Azure][extending-ad-to-azure].

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Implementare almeno due controller di dominio per ogni dominio. In questo modo la replica automatica tra server. Creare una disponibilità impostare per macchine virtuali funge da server Active Directory gestisce ogni dominio. Assicurarsi che siano almeno due server nella finestra di impostazione. 

Inoltre, è necessario designare uno o più server in ogni dominio come [master operazioni in standby] [ standby-operations-masters] nel caso in cui si verifica un errore di connettività a un server che svolge un ruolo.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Active Directory è automaticamente scalable controller di dominio che fanno parte dello stesso dominio. Le richieste vengono distribuite in tutti i controller all'interno di un dominio. È possibile aggiungere un altro dominio, e la vengono sincronizzate automaticamente con il dominio. Non si configura un bilanciamento del carico separata per indirizzare il traffico in controller all'interno del dominio. Assicurarsi che tutti i controller di dominio risorse di memoria e spazio di archiviazione sufficienti per gestire il database di dominio. Impostare le stesse dimensioni di tutti i controller di dominio macchine virtuali.

## <a name="management-and-monitoring-considerations"></a>Gestione e considerazioni sul monitoraggio

Per informazioni sulla gestione e considerazioni sul monitoraggio, vedere le sezioni equivalente in [Estensione Active Directory per Azure][extending-ad-to-azure]. 

Per ulteriori informazioni, vedere [Monitoraggio di Active Directory][monitoring_ad]. È possibile installare strumenti, ad esempio [Microsoft Systems Center] [ microsoft_systems_center] su un server di monitoraggio subnet gestione consentono di eseguire queste attività. 

## <a name="solution-components"></a>Componenti della soluzione

Soluzione uno script di esempio, [Distribuisci ReferenceArchitecture.ps1][solution-script], è disponibile che è possibile utilizzare per implementare l'architettura di seguito i suggerimenti descritti in questo articolo. Questo script utilizza Gestione risorse Azure modelli. I modelli sono disponibili come un set di elementi costitutivi, ognuno dei quali esegua un'azione specifica, ad esempio creare un VNet o configurare un NSG. Scopo dello script è gestire la distribuzione dei modelli.

I modelli sono parametri, con i parametri contenuti nel file JSON separati. È possibile modificare i parametri in questi file per configurare la distribuzione in base alle proprie esigenze. Non è necessario modificare i modelli stessi. Non è possibile modificare gli schemi degli oggetti nei file di parametro.

Quando si modificano i modelli, creare oggetti che seguono le convenzioni di denominazione descritte in [Consigliati convenzioni di denominazione per le risorse di Azure][naming-conventions].

Soluzione di esempio crea e configura un ambiente nel cloud che implementa un dominio denominato *treyresearch.com*. Questo ambiente comprende il gateway VPN subnet e server, rete Perimetrale, livello web, il livello aziendale e componenti di livello di accesso ai dati, somma e il livello di gestione. Soluzione di esempio include anche una configurazione facoltativa per la creazione di un ambiente locale simulata con il proprio dominio, *contoso.com*. La soluzione include gli script che stabilire una relazione di attendibilità tra questi domini che consente agli utenti locali accedere agli oggetti nel dominio di *treyresearch.com* nel cloud.

Nelle sezioni seguenti vengono descrivono gli elementi di in locale e cloud configurazioni.

### <a name="on-premises-components"></a>Componenti locale

>[AZURE.NOTE] Questi componenti non sono lo stato attivo principale dell'architettura descritta in questo documento e vengono forniti semplicemente per assegnare un'opportunità per testare l'ambiente cloud in modo sicuro, piuttosto che utilizzare un ambiente di produzione reale. Per questo motivo, in questa sezione sono elencati solo i file di parametro chiave. È possibile modificare le impostazioni, ad esempio gli indirizzi IP o le dimensioni delle macchine virtuali, ma è consigliabile lasciare molti degli altri parametri invariati.

Questo ambiente comprende un insieme di strutture di Active Directory del dominio *contoso.com* . Il dominio contiene due server aggiunge con gli indirizzi IP 192.168.0.4 e 192.168.0.5. Questi due server anche eseguire il servizio DNS. L'account di amministratore locale in entrambe le macchine virtuali è denominato `testuser` con password `AweS0me@PW`. Inoltre, la configurazione configura automaticamente un gateway VPN per la connessione a VNet nel cloud. È possibile modificare la configurazione modificando i seguenti file JSON disponibile nell' [**parametri/locale**] [ on-premises-folder] cartella:

- ** [virtualNetwork.parameters.json][on-premises-vnet-parameters]**. Questo file definisce lo spazio di indirizzi di rete per l'ambiente locale.

- ** [virtualMachines adds.parameters.json][on-premises-virtualmachines-adds-parameters]**. Il file contiene la configurazione per le macchine virtuali locale aggiunge servizi di hosting. Per impostazione predefinita, vengono create due macchine virtuali *v2 di DS3 Standard* .

- ** [virtualNetworkGateway.parameters.json] [ on-premises-virtualnetworkgateway-parameters] ** e ** [connection.parameters.json][on-premises-connection-parameters]**. Questi file contengono le impostazioni per la connessione VPN al gateway VPN Azure nel cloud, tra cui la chiave condivisa da utilizzare per proteggere il traffico passando il gateway.

Gli altri file nella cartella contengono le informazioni di configurazione usate per creare il dominio locale (*contoso.com*) utilizzando questa infrastruttura. Consente di installare somma, il programma di installazione DNS si crea l'insieme di strutture locale.

La soluzione Usa anche il seguente script denominato [posta in arrivo trust.ps1][incoming-trust], che viene eseguita in un computer nel dominio locale:

```Powershell
# Run the following powershell script in ra-adtrust-onpremise-ad-vm1 (ip 192.168.0.4)

$TrustedDomainName = "contoso.com"
#$TrustedDomainDnsIpAddresses = "192.168.0.4,192.168.0.5"

$TrustingDomainName = "treyresearch.com"
$TrustingDomainDnsIpAddresses = "10.0.4.4,10.0.4.5"

$ForwardIpAddress  = $TrustingDomainDnsIpAddresses
$ForwardDomainName = $TrustingDomainName

$IpAddresses = @()
foreach($address in $ForwardIpAddress.Split(',')){
    $IpAddresses += [IPAddress]$address.Trim()
}
Add-DnsServerConditionalForwarderZone -Name "$ForwardDomainName" -ReplicationScope "Domain" -MasterServers $IpAddresses

netdom trust $TrustingDomainName /d:$TrustedDomainName /add
```

Questo script consente di aggiungere gli indirizzi IP dei server Active Directory nel cloud (vedere la sezione successiva) per il servizio DNS locale e quindi utilizza [netdom] [ netdom] comando per creare un trust unidirezionale in ingresso da quello del dominio nel cloud (*treyresearch.com*).

### <a name="cloud-components"></a>Componenti del cloud

Questi componenti il nucleo di questa architettura. Utenti che configurazione dell'infrastruttura per il dominio *treyresearch.com* e creare le relazioni di trust con *tale dominio locale* . [**Parametri/azure**] [ azure-folder] cartella contiene i file di parametro seguenti per la configurazione di questi componenti:

- ** [virtualNetwork.parameters.json][vnet-parameters]**. Questo file definisce struttura del VNet per macchine virtuali e altri componenti nel cloud. Include impostazioni, ad esempio il nome, spazio di indirizzi, subnet e gli indirizzi dei server DNS necessari. Gli indirizzi DNS indicati nella Guida di riferimento in questo esempio gli indirizzi IP dei server DNS locali e il server DNS Azure predefinito. Modificare questi indirizzi per fare riferimento a installazione DNS se non si sta utilizzando l'ambiente locale di esempio:
    
    ```json
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-adtrust-vnet",
        "resourceGroup": "ra-adtrust-network-rg",
        "addressPrefixes": [
          "10.0.0.0/16"
        ],
        "subnets": [
          {
            "name": "dmz-private-in",
            "addressPrefix": "10.0.0.0/27"
          },
          {
            "name": "dmz-private-out",
            "addressPrefix": "10.0.0.32/27"
          },
          {
            "name": "dmz-public-in",
            "addressPrefix": "10.0.0.64/27"
          },
          {
            "name": "dmz-public-out",
            "addressPrefix": "10.0.0.96/27"
          },
          {
            "name": "mgmt",
            "addressPrefix": "10.0.0.128/25"
          },
          {
            "name": "GatewaySubnet",
            "addressPrefix": "10.0.255.224/27"
          },
          {
            "name": "web",
            "addressPrefix": "10.0.1.0/24"
          },
          {
            "name": "biz",
            "addressPrefix": "10.0.2.0/24"
          },
          {
            "name": "data",
            "addressPrefix": "10.0.3.0/24"
          },
          {
            "name": "adds",
            "addressPrefix": "10.0.4.0/27"
          }
        ],
        "dnsServers": [
          "10.0.4.4",
          "10.0.4.5",
          "168.63.129.16"
        ]
      }
    }
    ```

- ** [virtualMachines adds.parameters.json ] [ virtualmachines-adds-parameters] **. In questo file Configura macchine virtuali in esecuzione aggiunge nel cloud. La configurazione è costituito da due macchine virtuali. Modificare il nome dell'utente amministratore e la password nel `virtualMachineSettings` sezione ed è facoltativamente possibile modificare le dimensioni di macchine Virtuali in base ai requisiti del dominio:

    Per ulteriori informazioni, vedere [Estensione Active Directory in Azure][extending-ad-to-azure].
    
    ```json
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-adtrust-ad",
        "computerNamePrefix": "aad",
        "size": "Standard_DS3_v2",
        "osType": "Windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "false",
            "subnetName": "adds",
            "privateIPAllocationMethod": "Static",
            "startingIPAddress": "10.0.4.4",
            "enableIPForwarding": false,
            "dnsServers": [
            ],
            "isPrimary": "true"
          }
        ],
        "imageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2012-R2-Datacenter",
          "version": "latest"
        },
        "dataDisks": {
          "count": 1,
          "properties": {
            "diskSizeGB": 127,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [
        ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": "ra-adtrust-as"
        }
      }
    },
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-adtrust-vnet",
        "resourceGroup": "ra-adtrust-network-rg"
      }
    },
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 2,
        "vmCount": 2,
        "vmStartIndex": 1
      }
    }
    ```

- ** [aggiungere-aggiunge-dominio-controller.parameters.json][add-adds-domain-controller-parameters]**. Il file contiene le impostazioni per la creazione del dominio *treyresearch.com* che si estendono per il server aggiunge. Utilizza le estensioni personalizzate che stabilire il dominio e aggiungere i server aggiunge. A meno che non è necessario creare ulteriori server aggiunge (nel qual caso è necessario aggiungerle alla `vms` in forma di matrice), modificare i nomi da quello predefinito o per creare un dominio con un nome diverso non è necessario modificare il file.

- ** [virtualNetworkGateway.parameters.json][virtualnetworkgateway-parameters]**. Il file contiene le impostazioni usate per creare il gateway VPN Azure nel cloud usati per connettersi alla rete locale. È necessario modificare il `sharedKey` valore nel `connectionsSettings` in modo che corrispondano che il dispositivo VPN locale. Per ulteriori informazioni, vedere [implementazione di un'architettura di rete ibrido con Azure e VPN locale][hybrid-azure-on-prem-vpn].

- ** [private.parameters.json di rete perimetrale] [ dmz-private-parameters] ** e ** [public.parameters.json di rete perimetrale ] [ dmz-public-parameters] **. Questi file configurare in ingresso (pubblico) e in uscita (privati) lati delle macchine virtuali che costituiscono la rete Perimetrale, protezione dei server nel cloud. Per ulteriori informazioni su questi elementi e la configurazione, vedere [implementazione di una rete Perimetrale tra Azure e Internet][implementing-a-secure-hybrid-network-architecture-with-internet-access].

- ** [bilanciatore del carico web.parameters.json][loadBalancer-web-parameters]**, ** [bilanciatore del carico biz.parameters.json][loadBalancer-biz-parameters]**, e ** [bilanciatore del carico data.parameters.json][loadBalancer-data-parameters]**. Questi file parametri contengono specifiche macchine Virtuali per i livelli di accesso web, business e dati e configurare servizi di bilanciamento del carico per ogni livello. Si tratta di macchine virtuali che ospitano i web App e database ed eseguono i carichi di lavoro di business per l'organizzazione. Macchine virtuali nel livello web vengono aggiunti al dominio nel cloud tramite le impostazioni specificate nel ** [web-macchine virtuali-dominio-join.parameters.json] [ web-vm-domain-join-parameters] ** file.

    Ogni file contiene due set di parametri di configurazione. Il `virtualMachineSettings` sezione definisce macchine virtuali che ospitano il servizio ADFS nel cloud. Per impostazione predefinita, lo script crea due di queste macchine virtuali nello stesso set di disponibilità. Nei seguenti frammenti di mostrano le parti pertinenti del file *web.parameters.json bilanciatore del carico* :

    ```json
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-adtrust-web",
        "computerNamePrefix": "web",
        "size": "Standard_DS1_v2",
        "osType": "windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "false",
            "subnetName": "web",
            "privateIPAllocationMethod": "Dynamic",
            "isPrimary": "true",
            "enableIPForwarding": false,
            "dnsServers": [ ]
          }
        ],
        "imageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2012-R2-Datacenter",
          "version": "latest"
        },
        "dataDisks": {
          "count": 1,
          "properties": {
            "diskSizeGB": 128,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [ ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": "ra-adtrust-web-vm-as"
        }
      }
    },
    ...
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 2,
        "vmCount": 2,
        "vmStartIndex": 1
      }
    }
    ````
    È possibile modificare le dimensioni e il numero di macchine virtuali in ogni livello in base alle proprie esigenze.

    Il `loadBalancerSettings` sezione vengono fornite la descrizione del servizio di bilanciamento del carico per queste macchine virtuali. Bilanciamento del carico passa il traffico che viene visualizzata in porte 80 (HTTP) e 443 (HTTPS) a uno o di macchine virtuali. 

    >[AZURE.NOTE] La regola per la porta 80 utilizza una connessione TCP anziché HTTP. Ciò avviene perché l'installazione di IIS nel livello web è configurato per supportare l'autenticazione di Windows solo. Autenticazione anonima è disattivata. Tentativo di *ping* porte 80 tramite una connessione HTTP non riesce con un errore 401 (non autorizzato), mentre si usa una connessione TCP appena rileva se è attiva la porta:

    ```json
    "loadBalancerSettings": {
      "value": {
        "name": "ra-adtrust-web-lb",
        "frontendIPConfigurations": [
          {
            "name": "ra-adtrust-web-lb-fe",
            "loadBalancerType": "internal",
            "internalLoadBalancerSettings": {
              "privateIPAddress": "10.0.1.254",
              "subnetName": "web"
            }
          }
        ],
        "backendPools": [
          {
            "name": "ra-adtrust-web-lb-bep",
            "nicIndex": 0
          }
        ],
        "loadBalancingRules": [
          {
            "name": "http-rule",
            "frontendPort": 80,
            "backendPort": 80,
            "protocol": "Tcp",
            "backendPoolName": "ra-adtrust-web-lb-bep",
            "frontendIPConfigurationName": "ra-adtrust-web-lb-fe",
            "probeName": "http-probe",
            "enableFloatingIP": false
          },
          {
            "name": "https-rule",
            "frontendPort": 443,
            "backendPort": 443,
            "protocol": "Tcp",
            "backendPoolName": "ra-adtrust-web-lb-bep",
            "frontendIPConfigurationName": "ra-adtrust-web-lb-fe",
            "probeName": "https-probe",
            "enableFloatingIP": false
          }
        ],
        "probes": [
          {
            "name": "http-probe",
            "port": 80,
            "protocol": "Tcp",
            "requestPath": null
          },
          {
            "name": "https-probe",
            "port": 443,
            "protocol": "Tcp",
            "requestPath": null
          }
        ],
        "inboundNatRules": [ ]
      }
    }
    ```

- ** [virtualMachines mgmt.parameters.json][virtualMachines-mgmt-parameters]**. Il file contiene la configurazione della casella ponticello/gestione macchine virtuali. È possibile solo ottenere l'accesso e accesso amministrativo alle macchine virtuali nel web, business e i livelli dati nella finestra di spostamento. Per impostazione predefinita, lo script crea una singola *Standard_DS1_v2* macchine Virtuali, ma è possibile modificare il file per creare macchine virtuali di ingrandire o aggiuntive se il carico di lavoro gestione è probabile che sia significativa.

La configurazione usa anche [in uscita trust.ps1] [ outgoing-trust] script illustrato di seguito per creare un trust unidirezionale in uscita con il dominio *contoso.com* :

```powershell
# prerequiste: 
# You need to first run incoming-trust.ps1 in ra-adtrust-onpremise-ad-vm1 (ip 192.168.0.4)
# Then,
# Run the following powershell script in ra-adtrust-ad-vm1 (ip 10.0.4.4)

$TrustedDomainName = "contoso.com"
$TrustedDomainDnsIpAddresses = "192.168.0.4,192.168.0.5"

#$TrustingDomainName = "treyresearch.com"
#$TrustingDomainDnsIpAddresses = "10.0.4.4,10.0.4.5"

$ForwardIpAddress  = $TrustedDomainDnsIpAddresses
$ForwardDomainName = $TrustedDomainName

$IpAddresses = @()
foreach($address in $ForwardIpAddress.Split(',')){
    $IpAddresses += [IPAddress]$address.Trim()
}
Add-DnsServerConditionalForwarderZone -Name "$ForwardDomainName" -ReplicationScope "Domain" -MasterServers $IpAddresses

#netdom trust $TrustingDomainName /d:$TrustedDomainName /add
```

Questo script è simile al *posta in arrivo trust.ps1* descritto in precedenza. Aggiunge gli indirizzi IP locale nel server Active Directory per il servizio DNS locale e quindi utilizza [netdom] [ netdom] comando per creare la relazione di attendibilità in uscita.

## <a name="solution-deployment"></a>Distribuzione di soluzioni

La soluzione prende in considerazione i prerequisiti seguenti:

- Si ha un abbonamento di Azure esistente in cui è possibile creare gruppi di risorse.

- Scaricato e installato build più recente di Azure Powershell. Vedere [qui] [ azure-powershell-download] per le istruzioni.

Per eseguire lo script che consente di distribuire la soluzione:

1. Spostare la cartella desiderata nel computer locale e creare sottocartelle seguenti:

    - Script

    - Script/parametri

    - Script/parametri/locale

    - Script/parametri/Azure

2. Scaricare [Distribuisci ReferenceArchitecture.ps1] [ solution-script] file nella cartella di script

3. Scaricare il contenuto di [parametri/locale] [ on-premises-folder] alla cartella script/parametri/locale:

4. Scaricare il contenuto di [parametri/azure] [ azure-folder] alla cartella script/parametri/Azure.

5. Modificare il file di distribuzione ReferenceArchitecture.ps1 nella cartella script e modificare le righe seguenti per specificare i gruppi di risorse che devono essere creati o utilizzati per contenere le risorse create dallo script:

    ```powershell
    # Azure Onpremise Deployments
    $onpremiseNetworkResourceGroupName = "ra-adtrust-onpremise-rg"

    # Azure ADDS Deployments
    $azureNetworkResourceGroupName = "ra-adtrust-network-rg"
    $workloadResourceGroupName = "ra-adtrust-workload-rg"
    $securityResourceGroupName = "ra-adtrust-security-rg"
    $addsResourceGroupName = "ra-adtrust-adds-rg"
    ```

6. Modificare i file di parametro nelle cartelle di script/parametri/Azure e script/parametri/locale. Aggiornare i riferimenti di gruppo di risorse in questi file in modo che corrispondano i nomi dei gruppi di risorse assegnati alle variabili nel file ReferenceArchitecture.ps1 Distribuisci. La tabella seguente mostra i file di parametro fanno riferimento a quale gruppo di risorse. I gruppi di risorse *ra-adfs carico di lavoro rg* *ra-adfs sicurezza rg*, *ra-adfs-aggiunge-rg*, *ra-adfs adfs rg*e *ra-adfs proxy rg* vengono utilizzati solo dello script di PowerShell e non si verificano nei file di parametro.

  	|Gruppo di risorse|File di parametro|
  	|--------------|--------------|
  	|Ra-adtrust locale rg|parameters\onpremise\connection.Parameters.JSON<br /> parameters\onpremise\virtualMachines-ADDS.Parameters.JSON<br />parameters\onpremise\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\onpremise\virtualNetwork.Parameters.JSON<br />parameters\onpremise\virtualNetworkGateway.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON
  	|Ra-adtrust rete rg|parameters\onpremise\connection.Parameters.JSON<br />parameters\azure\dmz-private.Parameters.JSON<br />parameters\azure\dmz-public.Parameters.JSON<br />parameters\azure\loadBalancer-Biz.Parameters.JSON<br />parameters\azure\loadBalancer-Data.Parameters.JSON<br />parameters\azure\loadBalancer-Web.Parameters.JSON<br />parameters\azure\virtualMachines-ADDS.Parameters.JSON<br />parameters\azure\virtualMachines-Mgmt.Parameters.JSON<br />parameters\azure\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\azure\virtualNetwork.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON (*due occorrenze*)

    Inoltre, impostare la configurazione per i locali e cloud componenti, come descritto nei [Componenti della soluzione] [ solution-components] sezione.

7. Aprire una finestra di PowerShell di Azure, passare alla cartella script ed eseguire il comando seguente:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```

    Sostituire `<subscription id>` con l'ID di abbonamento Azure.

    Per `<location>`, specificare un'area di Azure, ad esempio `eastus` o `westus`.

    Il `<mode>` parametro può avere uno dei seguenti valori:

    - `Onpremise`, per creare l'ambiente locale simulata.

    - `Infrastructure`, creare l'infrastruttura di VNet e passare casella nel cloud.

    - `CreateVpn`, creare gateway virtuali Azure e connettersi alla rete locale.

    - `AzureADDS`, per costruire macchine virtuali funge da server aggiunge, installare Active Directory a queste macchine virtuali e creare il dominio nel cloud.

    - `WebTier`, che consente di creare web macchine virtuali a più livelli e bilanciamento del carico.

    - `Prepare`, che consente di eseguire tutte le attività precedente. **Questo è consigliabile se si sta creando una distribuzione completamente nuova e non si dispone di un'infrastruttura locale.**

    - `Workload`Per creare il livello di dati aziendali e macchine virtuali e servizi di bilanciamento del carico. Non sono incluse come parte di queste macchine virtuali di `Prepare` opzione.

    >[AZURE.NOTE] Se si usa il `Prepare` opzione, lo script necessario alcune ore per il completamento.

8.  Se si utilizza la configurazione di esempio in locale:

    1. Connettersi alla casella ponticello (*vm1 ra-adtrust-gestione* nel gruppo di risorse *ra-adtrust protezione rg* ). Eseguire l'accesso come *testuser* con password *AweS0me@PW*.

    2.  Nella casella passa aprire una sessione RDP nella macchina virtuale prima nel dominio *contoso.com* (dominio locale). Questa macchina virtuale è l'indirizzo IP 192.168.0.4. Il nome utente è *contoso\testuser* con password *AweS0me@PW*.

    3. Scaricare la [posta in arrivo trust.ps1] [ incoming-trust] script ed eseguirlo per creare il trust in ingresso dal dominio *treyresearch.com* .

9. Se si usa un'infrastruttura locale:

    1. Scaricare la [posta in arrivo trust.ps1] [ incoming-trust] script.

    2. Modificare lo script e sostituire il valore di `$TrustedDomainName` variabile con il nome del proprio dominio.

    3. Eseguire lo script.

10. Dalla casella ponticello, connettere la macchina virtuale prima nel dominio di *treyresearch.com* (il dominio nel cloud). Questa macchina virtuale è l'indirizzo IP 10.0.4.4. Il nome utente è *treyresearch\testuser* con password *AweS0me@PW*.

11. Scaricare [trust.ps1 in uscita] [ outgoing-trust] script ed eseguirlo per creare il trust in ingresso dal dominio *treyresearch.com* . Se si usa il proprio computer locale, modificare lo script prima di tutto. Impostare il `$TrustedDomainName` variabile il nome del dominio locale e specificare gli indirizzi IP dei server Active Directory per il dominio, nel `$TrustedDomainDnsIpAddresses` variabile.

12. In un computer locale, eseguire i passaggi descritti nell'articolo [verificare un Trust] [ verify-a-trust] per determinare se la relazione di attendibilità è stata configurata correttamente tra i domini *contoso.com* e *treyresearch.com* . Potrebbe essere necessario attendere alcuni minuti dopo aver completato la procedura precedente prima la protezione può essere convalidati.

I passaggi facoltativi rimanenti viene illustrato come determinare se il trust del dominio funziona come previsto. Questa procedura è necessario disporre l'accesso a un computer di sviluppo con Visual Studio installati.

1.  Nella finestra di Azure PowerShell eseguire il comando seguente per creare il livello di web se si dispone non configurarlo in precedenza (utilizzando il `Prepare` opzione):

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> WebTier
    ```

    Questo comando Crea livello web e aggiunge le macchine virtuali al dominio *treyresearch.com* .

2. Utilizzando Visual Studio nel computer di sviluppo, creare un'applicazione Web ASP.NET denominata *TreyResearchWebApp*. Utilizzo di .NET Framework 4.5.2.

3. Selezionare il modello *MVC* e modificare l'autenticazione per *L'autenticazione di Windows*. Non lo si crea un servizio di App nel cloud.

3. Creare ed eseguire l'applicazione per verificare l'autenticazione. Verificare che il nome utente Windows corrente visualizzata nella barra dei menu nella parte superiore della pagina verso destra.

4. Chiudere Internet Explorer.

5. Nella finestra *Esplora soluzioni* fare clic sul progetto TreyResearchWebApp, fare clic su *pubblica*.

6. Nella finestra *Pubblica sito Web* fare clic su *personalizzato*. Creare un profilo personalizzato denominato *TreyResearchWebApp*.

7. Nella pagina *connessione* , impostare il *metodo di pubblicazione* al *Sistema di File* e specificare una cartella denominata *TreyResearchWebApp*, disponibile nella posizione desiderata nel computer di sviluppo.

8. Nella pagina *Impostazioni* impostare la *configurazione* di *rilascio*.

9. Nella pagina di *Anteprima* , fare clic su *pubblica*.

10. Connettersi a ogni macchina virtuale nel livello web, a sua volta (tramite la casella ponticello) e le seguenti operazioni. Indirizzi IP del web livello macchine virtuali sono 10.0.1.4 e 10.0.1.5. Per entrambe le macchine virtuali nomeutenteè *treyresearch\testuser* con password *AweS0me@PW*:

    1. Copiare la cartella *TreyResearchWebApp* e il relativo contenuto dal computer di sviluppo *C:\inetpub* nella cartella.

    2. Tramite la console di Gestione Internet Information Services (IIS), passare al *sito Web Sites\Default* nel computer.

    3. Nel riquadro *Azioni* , fare clic su *Impostazioni di base*e modificare il percorso fisico del sito web di *%SystemDrive%\inetpub\TreyResearchWebApp*.

    4. Nel riquadro *Visualizzazione funzionalità* fare doppio clic su *autenticazione*. Verificare che sia abilitato *L'autenticazione di Windows* e *Autenticazione anonima* è disattivata.

11. da un computer locale, aprire Internet Explorer e individuare il sito web all'indirizzo http://10.0.1.254 (è l'indirizzo di bilanciamento del carico di livello web).

12. Nella finestra di dialogo *Protezione di Windows* , immettere le credenziali di un utente nel dominio locale. Specificare un nome utente che non esiste nel dominio di *treyresearch* . Se si utilizza l'ambiente locale simulata prima creare un utente nel dominio *contoso* e specificare le credenziali dell'utente.

13. Quando viene visualizzata la home page, verificare che il dominio corretto e il nome utente vengano visualizzati nella barra dei menu nella parte superiore della pagina verso destra.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle procedure consigliate per [l'estensione del dominio aggiunge locale in Azure][adds-extend-domain]

- Informazioni sulle procedure consigliate per la [creazione di un'infrastruttura ADFS] [ adfs] in Azure.

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[adfs]: ./guidance-identity-adfs.md
[adds-extend-domain]: ./guidance-identity-adds-extend-domain.md
[extending-ad-to-azure]: ./guidance-identity-adds-extend-domain.md
[implementing-aad]: ./guidance-identity-aad.md
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-hybrid-network-architecture-with-vpn]: ./guidance-hybrid-network-vpn.md
[running-VMs-for-an-N-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[best_practices_ad_password_policy]: https://technet.microsoft.com/magazine/ff741764.aspx
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[creating-forest-trusts]: https://technet.microsoft.com/library/cc816810(v=ws.10).aspx
[creating-external-trusts]: https://technet.microsoft.com/library/cc816837(v=ws.10).aspx
[naming-conventions]: ./guidance-naming-conventions.md
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[hybrid-azure-on-prem-vpn]: ./guidance-hybrid-network-vpn.md
[verify-a-trust]: https://technet.microsoft.com/library/cc753821.aspx
[netdom]: https://technet.microsoft.com/library/cc835085.aspx
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/Deploy-ReferenceArchitecture.ps1
[on-premises-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adds-trust/parameters/onpremise
[on-premises-vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualNetwork.parameters.json
[on-premises-virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualMachines-adds.parameters.json
[on-premises-virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualNetworkGateway.parameters.json
[on-premises-connection-parameters]: https://github.com/mspnp/reference-architectures/blob/master/guidance-identity-adds-trust/parameters/onpremise/connection.parameters.json
[incoming-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/extensions/incoming-trust.ps1
[outgoing-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/extensions/outgoing-trust.ps1
[azure-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adds-trust/parameters/azure
[vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualNetwork.parameters.json
[virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualMachines-adds.parameters.json
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/add-adds-domain-controller.parameters.json
[virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualNetwork.parameters.json
[dmz-private-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/dmz-private.parameters.json
[dmz-public-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/dmz-public.parameters.json
[loadBalancer-web-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-web.parameters.json
[loadBalancer-biz-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-biz.parameters.json
[loadBalancer-data-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-data.parameters.json
[virtualMachines-mgmt-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualMachines-mgmt.parameters.json
[web-vm-domain-join-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/web-vm-domain-join.parameters.json
[solution-components]: [#solution_components]
[0]: ./media/guidance-identity-aad-resource-forest/figure1.png "Architettura della rete ibrido con i domini di Active Directory distinti protetta"