<properties 
   pageTitle="Risoluzione di macchine virtuali e istanze del ruolo"
   description="Assegnare un nome a scenari di risoluzione per Azure IaaS, le soluzioni ibride, tra servizi cloud diverso, Active Directory e l'utilizzo di server DNS "
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="telmos" />

# <a name="name-resolution-for-vms-and-role-instances"></a>Risoluzione dei nomi per macchine virtuali e istanze del ruolo

A seconda di come si usa Azure per ospitare IaaS, PaaS e le soluzioni ibride, potrebbe essere necessario consentire le macchine virtuali e istanze del ruolo creati per comunicare con loro. Anche se questo tipo di comunicazione può essere eseguito utilizzando indirizzi IP è molto più semplice da utilizzare nomi che possono essere facilmente identificati e non vengono modificati. 

Quando è necessario risolvere i nomi di dominio in indirizzi IP interni istanze del ruolo e macchine virtuali ospitate in Azure, possono utilizzare uno dei due metodi:

- [Risoluzione dei nomi forniti Azure](#azure-provided-name-resolution)

- [Risoluzione dei nomi utilizzando server DNS](#name-resolution-using-your-own-dns-server) (il inviare query ai server DNS forniti Azure) 

Il tipo di risoluzione dei nomi di che utilizzare dipende come le macchine virtuali e istanze del ruolo necessitano comunicare con loro.

**Nella tabella seguente illustra gli scenari e soluzioni di risoluzione dei nomi corrispondenti:**

| **Scenario:** | **Soluzione** | **Suffisso** |
|--------------|--------------|----------|
| Risoluzione dei nomi tra istanze del ruolo o macchine virtuali nella stessa servizio cloud o nella rete virtuale | [Risoluzione dei nomi forniti Azure](#azure-provided-name-resolution)| nome host o il FQDN |
| Risoluzione dei nomi tra istanze del ruolo o macchine virtuali presenti in diverse reti virtuali | Cliente gestiti i server DNS inoltro query tra vnets per la risoluzione da Azure (proxy DNS).  vedere [risoluzione dei nomi utilizzando server DNS](#name-resolution-using-your-own-dns-server)| Nome di dominio completo solo |
| Risoluzione dei nomi di computer e servizio locale da istanze del ruolo o macchine virtuali di Azure | Cliente gestiti i server DNS (controller di dominio, ad esempio in locale, controller di dominio locale di sola lettura o un DNS secondario sincronizzati mediante trasferimenti).  Vedere [risoluzione dei nomi utilizzando server DNS](#name-resolution-using-your-own-dns-server)|Nome di dominio completo solo |
| Risoluzione dei nomi host Azure dal computer locale | Inoltro di query a un server proxy DNS gestite cliente in vnet corrispondente, il server proxy inoltra query Azure per la risoluzione. Vedere [risoluzione dei nomi utilizzando server DNS](#name-resolution-using-your-own-dns-server)| Nome di dominio completo solo |
| Invertire il DNS dell'interno IP | [Risoluzione dei nomi utilizzando server DNS](#name-resolution-using-your-own-dns-server) | n/d |
| Risoluzione dei nomi tra macchine virtuali o istanze del ruolo Trova servizi cloud diverso, non in una rete virtuale| Non applicabile. Connessione tra macchine virtuali e istanze del ruolo nei servizi cloud diverse non è supportata all'esterno di una rete virtuale.| n/d |



## <a name="azure-provided-name-resolution"></a>Risoluzione dei nomi forniti Azure

Insieme risoluzione dei nomi DNS pubblici, Azure offre la risoluzione dei nomi interno per macchine virtuali e istanze del ruolo che si trovano all'interno della stessa rete virtuale o un servizio cloud.  Macchine virtuali/istanze in un servizio cloud di condividere lo stesso suffisso DNS (in modo che il solo nome host è sufficiente) ma cloud diverso di reti virtuali classica servizi hanno suffissi diversi per DNS in modo che il nome di dominio completo è necessario per la risoluzione dei nomi tra servizi cloud diverso.  In reti virtuali nel modello di distribuzione Manager delle risorse, il suffisso DNS è coerenza in rete virtuale (in modo che non è necessaria il FQDN) e i nomi DNS possono essere assegnati a schede di rete e macchine virtuali. Anche se la risoluzione dei nomi forniti Azure non richiede alcuna configurazione, non è adatta per tutti gli scenari di distribuzione, come illustrato nella tabella precedente.

> [AZURE.NOTE] Nel caso dei ruoli web e di lavoro, è inoltre possibile accedere agli indirizzi IP interni delle istanze di ruolo in base al numero di nome e l'istanza del ruolo tramite l'API REST di Azure servizio gestione. Per ulteriori informazioni, vedere [Riferimento API REST al servizio Gestione](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### <a name="features-and-considerations"></a>Caratteristiche e le considerazioni

**Caratteristiche:**

- Facilità di utilizzo: nessuna configurazione è necessario per la risoluzione dei nomi forniti Azure.

- Il servizio di risoluzione nome fornito Azure è altamente disponibile, per evitare la necessità di creare e gestire cluster dei server DNS.

- Utilizzare in combinazione con i server DNS per risolvere nomi host locale e Azure.

- Risoluzione dei nomi viene fornito tra le istanze ruolo macchine virtuali all'interno del servizio cloud stesso senza necessità di un nome di dominio completo.

- Risoluzione dei nomi viene fornito tra macchine virtuali di reti virtuali che utilizzano il modello di distribuzione di Manager delle risorse, senza necessità di dominio completo. Reti virtuali nel modello di distribuzione classica richiedono il FQDN per la risoluzione dei nomi di servizi cloud diverso. 

- È possibile utilizzare nomi host che meglio descrivono le distribuzioni, invece di utilizzo dei nomi generato automaticamente.

**Considerazioni:**

- Il suffisso DNS creato Azure non può essere modificato.

- Non è possibile registrare manualmente i propri record.

- WINS e NetBIOS non sono supportate. (Non è possibile vedere nelle macchine virtuali in Esplora risorse.)

- Nome host devono essere compatibile con DNS (devono utilizzare solo 0-9-z e "-" e non può iniziare o terminare con un '-'. Vedere RFC 3696 sezione 2).

- Il traffico di query DNS è limitato per ogni macchina virtuale. Questa operazione non deve avere un impatto maggior parte delle applicazioni.  Se si osserva la limitazione delle richieste, assicurarsi che la cache lato client è abilitata.  Per ulteriori informazioni, vedere [ottenere il massimo da forniti Azure risoluzione dei nomi](#Getting-the-most-from-Azure-provided-name-resolution).

- Solo macchine virtuali nei servizi 180 cloud prima registrate per ogni virtuali in un modello di distribuzione classica. Questo non vale per reti virtuali in modelli di distribuzione di Manager delle risorse.


### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Ottenere il massimo dalla risoluzione dei nomi forniti Azure
**La cache lato client:**

Non tutte le query DNS deve essere inviato in rete.  La cache lato client consente di ridurre la latenza e migliorare la capacità di recupero blips di rete per la risoluzione delle query DNS ricorrente da una cache locale.  I record DNS contengono un Time To Live (TTL) che consente la cache archiviare i record per più a lungo possibile senza impatto validità record, in modo che la cache lato client è adatta per la maggior parte dei casi.

Il valore predefinito Client DNS Windows dispone di una cache DNS predefinita.  Alcuni Linux distros non includere nella selezione la memorizzazione nella cache per impostazione predefinita, è consigliabile che è aggiungerne uno per ogni VM Linux (dopo aver verificato che non vi è una cache locale già).

Esistono diversi DNS memorizzazione nella cache pacchetti diversi disponibili, ad esempio dnsmasq, ecco i passaggi per installare dnsmasq in distros più comuni:

- **Ubuntu (utilizza resolvconf)**:
    - installare solo il pacchetto di dnsmasq ("sudo installa apt get dnsmasq").
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

> [AZURE.NOTE] Il pacchetto di 'dnsmasq' è solo una delle numerose cache DNS disponibili per Linux.  Prima di usarlo, verificare l'idoneità per esigenze specifiche e che non sia installata alcun altre cache.

**Tentativi di lato client:**

Il DNS è principalmente un protocollo UDP.  Come il protocollo UDP non garantisce il recapito dei messaggi, ritentare viene gestita nel protocollo DNS.  Ogni client DNS (sistema operativo) può presentare ritentare diversa a seconda di preferenza creatori:

 - Sistemi operativi Windows riprovare successive a 1 secondo e successivamente in un'altra 2, 4 e un altro 4 secondi. 
 - Tentativi di installazione predefinito Linux dopo 5 secondi.  Si consiglia di assegnare il tentativo di 5 volte a intervalli secondo 1.  

Per verificare le impostazioni correnti in una VM Linux, 'gatti /etc/resolv.conf' e osservare la riga 'opzioni', ad esempio:

    options timeout:1 attempts:5

Il file resolv.conf è in genere generato automaticamente e non possono essere modificato.  I passaggi specifici per aggiungere la riga 'opzioni' variano a seconda distro:

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
Esistono varie situazioni in cui le esigenze di risoluzione nome è possono passare oltre le funzionalità offerte da Azure, ad esempio quando usando il servizio Active Directory o quando si richiede la risoluzione DNS tra reti virtuali (vnets).  Per coprire questi scenari, Azure offre la possibilità di usare i server DNS.  

I server DNS all'interno di una rete virtuale consente di inoltrare le query DNS a sistemi di risoluzione ricorsiva di Azure per risolvere nomi host all'interno della rete virtuale.  Ad esempio, un dominio controller di in esecuzione in Azure possono rispondere alle query DNS per i domini e inoltrare tutte le altre query in Azure.  In questo modo macchine virtuali visualizzare le risorse locali (tramite CD) e nome host forniti Azure (tramite il server d'inoltro).  Accesso ai sistemi di risoluzione ricorsiva di Azure viene fornito tramite l'indirizzo IP virtuale 168.63.129.16.

Inoltro DNS anche attivato vnet tra la risoluzione DNS e i computer locale risolvere nomi host forniti Azure.  Per risolvere hostname una Virtual Machine, il server DNS macchine Virtuali deve trovarsi nella stessa rete virtuale ed essere configurato per inoltrare hostname query in Azure.  Come suffisso DNS è diverso in ogni vnet, è possibile utilizzare le regole di inoltro condizionale per inviare query DNS a vnet corretta per la risoluzione.  Nella figura seguente mostra due vnets e una rete locale eseguendo la risoluzione DNS vnet tra con questo metodo.  Server d'inoltro DNS esempio è disponibile nella [raccolta modelli di Guida introduttiva di Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) e [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

![Vnet tra DNS](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Quando si utilizza la risoluzione dei nomi forniti Azure, un suffisso DNS interno (*. internal.cloudapp.net) sono disponibili per ogni macchina virtuale tramite DHCP.  In questo modo hostname risoluzione presenti nell'area internal.cloudapp.net i record di nome host.  Quando si usa soluzione risoluzione nome, il suffisso IDN non viene fornito macchine virtuali perché interferisce con altre architetture DNS (ad esempio scenari dominio).  Se, tuttavia viene assegnato un segnaposto non funziona (reddog.microsoft.com).  

Se necessario, è possibile determinare il suffisso DNS interno tramite PowerShell o l'API:

-  Per le reti virtuali in modelli di distribuzione di Manager delle risorse, il suffisso è disponibile la risorsa [scheda di rete](https://msdn.microsoft.com/library/azure/mt163668.aspx) o mediante il cmdlet [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) .    
-  In modelli di distribuzione classica, è disponibile tramite la chiamata [Ottenere API di distribuzione](https://msdn.microsoft.com/library/azure/ee460804.aspx) o il suffisso la [AzureVM Get-Debug](https://msdn.microsoft.com/library/azure/dn495236.aspx) cmdlet.


Se l'inoltro di query in Azure non alle proprie esigenze, sarà necessario fornire soluzione DNS.  Soluzione DNS sarà necessario:

-  Fornire una risoluzione hostname appropriato, ad esempio tramite [DNS dinamico](virtual-networks-name-resolution-ddns.md).  Nota Se Usa DNS dinamico potrebbe essere necessario disattivare lo scavenging record DNS leasing DHCP di Azure sono molto lungo e scavenging può rimuovere i record DNS in modo anomalo. 
-  Fornire una risoluzione ricorsiva appropriato per consentire la risoluzione dei nomi di dominio esterno.
-  Essere accessibili (porte TCP e UDP sulla porta 53) dai client che serve e sarà possibile accedere a internet.
-  Proteggere da access da internet, per ridurre i rischi dagli agenti esterni.

> [AZURE.NOTE] Per ottenere prestazioni ottimali, quando si usa macchine virtuali di Azure come server DNS, IPv6 deve essere disattivata e un [Livello di istanza IP pubblico](virtual-networks-instance-level-public-ip.md) devono essere assegnate ogni macchina virtuale al server DNS.  Se si sceglie di utilizzare Windows Server come server DNS, [in questo articolo](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) fornisce le ottimizzazioni e analisi delle prestazioni aggiuntive.


### <a name="specifying-dns-servers"></a>Specificare i server DNS

Quando si usa il proprio server DNS, Azure offre la possibilità di specificare più server DNS per virtuali o per l'interfaccia di rete (Manager delle risorse) / (classico) servizio cloud.  I server DNS specificati per l'interfaccia/rete di servizi cloud ottengono la precedenza rispetto a quelle specificate per la rete virtuale.

> [AZURE.NOTE] Proprietà connessione di rete, ad esempio al server DNS IP, non deve essere modificato direttamente in macchine virtuali di Windows come possono ottenere cancellati durante servizio correzione quando la scheda di rete virtuale viene sostituita. 


Quando si usa il modello di distribuzione di Manager delle risorse, è possono specificare i server DNS nel portale, API/modelli ([vnet](https://msdn.microsoft.com/library/azure/mt163661.aspx), [nic](https://msdn.microsoft.com/library/azure/mt163668.aspx)) o PowerShell ([vnet](https://msdn.microsoft.com/library/mt603657.aspx), [nic](https://msdn.microsoft.com/library/mt619370.aspx)).

Quando si usa il modello di distribuzione classica, è possono specificare i server DNS per la rete virtuale nel portale o [il file di *Configurazione della rete* ](https://msdn.microsoft.com/library/azure/jj157100).  Per i servizi cloud, i server DNS vengono specificati tramite [il file di *Configurazione del servizio* ](https://msdn.microsoft.com/library/azure/ee758710) o PowerShell ([AzureVM nuovo](https://msdn.microsoft.com/library/azure/dn495254.aspx)).

> [AZURE.NOTE] Se si modificano le impostazioni di DNS per una macchina rete/virtuale che si è già distribuito, è necessario riavviare ogni interessato Virtual Machine per le modifiche abbiano effetto.


## <a name="next-steps"></a>Passaggi successivi

Modello di distribuzione Manager delle risorse:

- [Creare o aggiornare una rete virtuale](https://msdn.microsoft.com/library/azure/mt163661.aspx)
- [Creazione o aggiornamento di una scheda di rete](https://msdn.microsoft.com/library/azure/mt163668.aspx)
- [Nuovo AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)
- [Nuovo AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)

 
Modello di distribuzione classica:

- [Schema di configurazione del servizio di Azure](https://msdn.microsoft.com/library/azure/ee758710)
- [Schema di configurazione della rete virtuale](https://msdn.microsoft.com/library/azure/jj157100)
- [Configurare una rete virtuale tramite un File di configurazione della rete](virtual-networks-using-network-configuration-file.md) 

