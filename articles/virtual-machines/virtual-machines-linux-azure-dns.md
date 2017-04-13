<properties 
   pageTitle="Opzioni di risoluzione dei nomi DNS per le macchine virtuali Linux in Azure"
   description="Nome risoluzione scenari per macchine virtuali Linux nel IaaS Azure, tra cui forniti provider di servizi DNS, server DNS e portare il proprio DNS esterno ibrido."
   services="virtual-machines"
   documentationCenter="na"
   authors="RicksterCDN"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/19/2016"
   ms.author="rclaus" />

# <a name="dns-name-resolution-options-for-linux-vms-in-azure"></a>Opzioni di risoluzione dei nomi DNS per le macchine virtuali Linux in Azure

Azure offre la risoluzione dei nomi DNS per impostazione predefinita per tutte le macchine virtuali contenute in una singola rete virtuale. In grado di implementare la propria soluzione di risoluzione DNS nome configurando il proprio provider di servizi DNS nelle macchine virtuali ospitate Azure. Gli scenari seguenti dovrebbero risultare utili scegliere quello che funziona meglio per le esigenze specifiche.

- [Risoluzione dei nomi forniti Azure](#azure-provided-name-resolution)

- [Risoluzione dei nomi utilizzando server DNS](#name-resolution-using-your-own-dns-server) 

Il tipo di risoluzione dei nomi di che utilizzare dipende come le macchine virtuali e istanze del ruolo necessitano comunicare con loro.

**Nella tabella seguente illustra gli scenari e soluzioni di risoluzione dei nomi corrispondenti:**

| **Scenario:** | **Soluzione** | **Suffisso** |
|--------------|--------------|----------|
| Risoluzione dei nomi tra istanze del ruolo o macchine virtuali si trova nella stessa rete virtuale | [Risoluzione dei nomi forniti Azure](#azure-provided-name-resolution)| nome host o il FQDN |
| Risoluzione dei nomi tra istanze del ruolo o macchine virtuali presenti in diverse reti virtuali | Cliente gestiti i server DNS inoltro query tra vnets per la risoluzione da Azure (proxy DNS).  vedere [risoluzione dei nomi utilizzando server DNS](#name-resolution-using-your-own-dns-server)| Nome di dominio completo solo |
| Risoluzione dei nomi di computer e servizio locale da istanze del ruolo o macchine virtuali di Azure | Cliente gestiti i server DNS (ad esempio controller di dominio locale, controller di dominio locale di sola lettura o DNS secondario sincronizzati mediante trasferimenti).  Vedere [risoluzione dei nomi utilizzando server DNS](#name-resolution-using-your-own-dns-server)|Nome di dominio completo solo |
| Risoluzione dei nomi host Azure dal computer locale | Inoltro di query a un server proxy DNS gestite cliente in vnet corrispondente, il server proxy inoltra query Azure per la risoluzione. Vedere [risoluzione dei nomi utilizzando server DNS](#name-resolution-using-your-own-dns-server)| Nome di dominio completo solo |
| Invertire il DNS dell'interno IP | [Risoluzione dei nomi utilizzando server DNS](#name-resolution-using-your-own-dns-server) | n/d |

## <a name="azure-provided-name-resolution"></a>Risoluzione dei nomi forniti Azure

Insieme risoluzione dei nomi DNS pubblici, Azure offre la risoluzione dei nomi interno per macchine virtuali e istanze del ruolo che si trovano all'interno della stessa rete virtuale.  In basato su ARM reti virtuali suffisso DNS è coerenza in rete virtuale (in modo che non è necessaria il FQDN) e i nomi DNS possono essere assegnati a schede di rete e macchine virtuali. Anche se la risoluzione dei nomi forniti Azure non richiede alcuna configurazione, non è adatta per tutti gli scenari di distribuzione, come illustrato nella tabella precedente.

### <a name="features-and-considerations"></a>Caratteristiche e le considerazioni

**Caratteristiche:**

- Facilità di utilizzo: nessuna configurazione è necessaria per la risoluzione dei nomi forniti Azure.

- Il servizio di risoluzione nome fornito Azure è altamente disponibile, per evitare la necessità di creare e gestire cluster dei server DNS.

- Utilizzare insieme server DNS per risolvere nomi host locale e Azure.

- Risoluzione dei nomi viene fornito tra macchine virtuali di reti virtuali senza necessità di dominio completo. 

- È possibile utilizzare nomi host che meglio descrivono le distribuzioni, invece di utilizzo dei nomi generato automaticamente.

**Considerazioni:**

- Il suffisso DNS creato Azure non può essere modificato.

- Non è possibile registrare manualmente i propri record.

- WINS e NetBIOS non sono supportate.

- Nome host devono essere compatibile con DNS (devono utilizzare solo 0-9-z e "-" e non può iniziare o terminare con un '-'. Vedere RFC 3696 sezione 2).

- Il traffico di query DNS è limitato per ogni macchina virtuale. Questa operazione non deve avere un impatto maggior parte delle applicazioni.  Se si osserva la limitazione delle richieste, assicurarsi che la cache lato client è abilitata.  Per ulteriori informazioni, vedere [ottenere il massimo da forniti Azure risoluzione dei nomi](#Getting-the-most-from-Azure-provided-name-resolution).


### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Ottenere il massimo dalla risoluzione dei nomi forniti Azure
**La cache lato client:**

Non tutte le query DNS vengono inviate in rete.  La cache lato client consente di ridurre la latenza e migliorare la capacità di recupero blips di rete per la risoluzione delle query DNS ricorrente da una cache locale.  I record DNS contengono un Time To Live (TTL) che consente la cache archiviare i record per più a lungo possibile senza impatto validità record.  Per questi motivi, la cache lato client è adatta alla maggior parte dei casi.

Alcuni distros Linux non includere la memorizzazione nella cache per impostazione predefinita.  È consigliabile che aggiungere uno per ogni VM Linux (dopo aver verificato che non vi è una cache locale già).

Esistono diversi diversi DNS memorizzazione nella cache pacchetti disponibili, ad esempio dnsmasq, ecco i passaggi per installare dnsmasq in distros più comuni:

- **Ubuntu (utilizza resolvconf)**:
    - installare il pacchetto di dnsmasq ("sudo installa apt get dnsmasq").
- **SUSE (utilizza netconf)**:
    - installare il pacchetto di dnsmasq ("sudo zypper installa dnsmasq") 
    - attivare il servizio dnsmasq ("systemctl Abilita dnsmasq.service") 
    - avviare il servizio dnsmasq ("systemctl start dnsmasq.service") 
    - modificare "/ ecc/sysconfig/network/config" e modificare NETCONFIG_DNS_FORWARDER = "" a "dnsmasq"
    - icona resolv.conf ("netconfig aggiornamento") per impostare la cache del Risolutore DNS locale
- **OpenLogic (utilizza NetworkManager)**:
    - installare il pacchetto di dnsmasq ("sudo slurp installa dnsmasq")
    - attivare il servizio dnsmasq ("systemctl Abilita dnsmasq.service")
    - avviare il servizio dnsmasq ("systemctl start dnsmasq.service")
    - aggiungere "anteporre domain name servers 127.0.0.1;" a "/etc/dhclient-eth0.conf"
    - riavviare il servizio di rete ("rete riavviare il servizio") per impostare la cache come il sistema di risoluzione DNS locale

> [AZURE.NOTE]: Il pacchetto di 'dnsmasq' è solo una delle numerose cache DNS disponibili per Linux.  Prima di usarlo, verificare l'idoneità per esigenze specifiche e che non sia installata alcun altre cache.

**Tentativi di lato client:**

Il DNS è principalmente un protocollo UDP.  Come il protocollo UDP non garantisce il recapito dei messaggi, ritentare viene gestita nel protocollo DNS.  Ogni client DNS (sistema operativo) può presentare ritentare diversa a seconda di preferenza creatori:

 - Sistemi operativi Windows riprovare dopo una secondo e successivamente in un'altra due, quattro e un altro quattro secondi. 
 - Tentativi di installazione predefinito Linux dopo cinque secondi.  È consigliabile modificare questa opzione per riprovare cinque volte in un secondo intervalli.  

Per verificare le impostazioni correnti in una VM Linux, 'gatti /etc/resolv.conf' e osservare la riga 'opzioni', ad esempio:

    options timeout:1 attempts:5

Il file resolv.conf viene generato automaticamente e non va modificato.  I passaggi specifici per aggiungere la riga 'opzioni' variano a seconda distro:

- **Ubuntu** (utilizza resolvconf):
    - aggiungere la riga di opzioni per ' / etc/resolveconf/resolv.conf.d/head' 
    - eseguire "resolvconf -u" per l'aggiornamento
- **SUSE** (utilizza netconf):
    - aggiungere il NETCONFIG_DNS_RESOLVER_OPTIONS "timeout:1 tentativi: 5" = "" parametro in '/ ecc/sysconfig/network/config' 
    - eseguire "netconfig update" per l'aggiornamento
- **OpenLogic** (utilizza NetworkManager):
    - aggiungere 'eco "Opzioni timeout:1 tentativi: 5" ' a ' / etc/NetworkManager/dispatcher.d/11-dhclient' 
    - eseguire "service riavvia rete" per l'aggiornamento

## <a name="name-resolution-using-your-own-dns-server"></a>Risoluzione dei nomi utilizzando server DNS
Esistono diverse situazioni in cui le proprie esigenze risoluzione nome possono andare oltre la funzionalità forniti da Azure, ad esempio quando si richiede la risoluzione DNS tra reti virtuali (vnets).  Per coprire questo scenario, Azure offre la possibilità di usare i server DNS.  

I server DNS all'interno di una rete virtuale consente di inoltrare le query DNS a sistemi di risoluzione ricorsiva di Azure per risolvere nomi host all'interno della rete virtuale.  Ad esempio un server DNS in esecuzione in Azure possa rispondere alle query DNS per i file di zona DNS e inoltrare tutte le altre query in Azure.  In questo modo macchine virtuali visualizzare entrambe le voci nei file di zona e nomi host forniti Azure (tramite il server d'inoltro).  Accesso ai sistemi di risoluzione ricorsiva di Azure viene fornito tramite l'indirizzo IP virtuale 168.63.129.16.

Inoltro DNS anche attivato vnet tra la risoluzione DNS e i computer locale risolvere nomi host forniti Azure.  Per risolvere il problema hostname una Virtual Machine, il server DNS macchine Virtuali deve trovarsi nella stessa rete virtuale ed essere configurato per inoltrare hostname query in Azure.  Come suffisso DNS è diverso in ogni vnet, è possibile utilizzare le regole di inoltro condizionale per inviare query DNS a vnet corretta per la risoluzione.  Nella figura seguente mostra due vnets e una rete locale eseguendo la risoluzione DNS vnet tra con questo metodo:

![Vnet tra DNS](./media/virtual-machines-linux-azure-dns/inter-vnet-dns.png)

Quando si utilizza la risoluzione dei nomi forniti Azure, suffisso DNS interno verrà fornito per ogni macchina virtuale mediante DHCP.  Quando si usa soluzione risoluzione nome, il suffisso non viene fornito macchine virtuali perché interferisce con altre architetture DNS.  Per fare riferimento al computer in base al nome di dominio completo o per configurare il suffisso nelle macchine virtuali, è possibile determinare il suffisso tramite PowerShell o l'API:

-  Per la gestione delle risorse Azure gestite vnets, il suffisso è disponibile attraverso la risorsa [scheda di rete](https://msdn.microsoft.com/library/azure/mt163668.aspx) o è possibile eseguire il comando `azure network public-ip show <resource group> <pip name>` per visualizzare i dettagli dell'indirizzo IP pubblico, incluse il FQDN della scheda NIC.    


Se l'inoltro di query in Azure non alle proprie esigenze, è necessario fornire soluzione DNS.  Soluzione DNS è necessario:

-  Fornire una risoluzione hostname appropriato, ad esempio tramite [DNS dinamico](../virtual-network/virtual-networks-name-resolution-ddns.md).  Nota Se Usa DNS dinamico potrebbe essere necessario disattivare lo scavenging record DNS leasing DHCP di Azure sono molto lungo e scavenging può rimuovere i record DNS in modo anomalo. 
-  Fornire una risoluzione ricorsiva appropriato per consentire la risoluzione dei nomi di dominio esterno.
-  Essere accessibili (porte TCP e UDP sulla porta 53) dai client che serve e sarà possibile accedere a internet.
-  Proteggere da access da internet, per ridurre i rischi dagli agenti esterni.

> [AZURE.NOTE] Per ottenere prestazioni ottimali, quando si usa macchine virtuali di Azure come server DNS, IPv6 deve essere disattivata e un [Livello di istanza IP pubblico](../virtual-network/virtual-networks-instance-level-public-ip.md) devono essere assegnate ogni macchina virtuale al server DNS.  

