<properties
    pageTitle="La progettazione dell'infrastruttura di rete di emergenza | Microsoft Azure"
    description="In questo articolo vengono illustrate considerazioni sulla progettazione di rete per il ripristino del sito di Azure"
    services="site-recovery"
    documentationCenter=""
    authors="prateek9us"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/19/2016"
    ms.author="pratshar"/>

#  <a name="designing-your-network-infrastructure-for-disaster-recovery"></a>La progettazione dell'infrastruttura di rete di emergenza

In questo articolo è richiesto per professionisti IT che sono responsabili della progettazione, implementazione e supporto continuità aziendale e infrastruttura di emergenza ripristino (BCDR) e che vogliono sfruttarle Microsoft Azure sito ripristino automatico per supportare e migliorare i servizi BCDR. Questo documento vengono illustrate le considerazioni pratiche per la distribuzione di server System Center Virtual Machine Manager, Pro e contro di allungato subnet e subnet failover e come strutturare il ripristino di emergenza a siti virtuali di Microsoft Azure.

## <a name="overview"></a>Panoramica

[Azure sito ripristino automatico](https://azure.microsoft.com/services/site-recovery/) è un servizio di Microsoft Azure che coordina la protezione e il ripristino delle applicazioni virtualizzate per business continuità il ripristino (BCDR). In questo documento è destinato a Guida il lettore il processo di progettazione le reti, impostazione dello stato attivo sull'architettura intervalli di indirizzi IP e subnet nel sito di ripristino di emergenza durante la replica macchine () utilizzando il ripristino del sito.

Inoltre, in questo articolo viene illustrato come il ripristino del sito consente architettura e implementare un Data Center virtuale multisito per supportare i servizi di BCDR in fase di test o di emergenza.

Nel mondo nel punto in cui tutti gli utenti si aspetta connettività 24/7, è più importante mai per mantenere l'infrastruttura e applicazioni e in esecuzione. Lo scopo di continuità aziendale e il ripristino di emergenza (BCDR) è per ripristinare i componenti non riusciti in modo che l'organizzazione possibile riprendere le normali operazioni. Sviluppo di strategie di ripristino di emergenza di occuparsi di eventi poco probabile, devastanti è molto complesso. Ciò è dovuto difficoltà inerenti di prevedere il futuro, in particolare in relazione ad eventi improbabili e il costo elevato per fornire le misure adeguate di protezione contro catastrofi significative. 

Fondamentale per BCDR pianificazione, ripristino ora obiettivo (RTO) e il ripristino punto obiettivo (rilasciato) devono essere definiti come parte di un piano di recupero. Quando correttive centro dati situato il, utilizzando il ripristino del sito di Azure, i clienti possono rapidamente (RTO bassa) connettere le macchine virtuali replicate situate nell'interfaccia di dati secondaria o Microsoft Azure con perdita di dati minima (rilasciato bassa). 

Failover è possibile grazie automatico che inizialmente copia designate macchine virtuali dal centro dati principale al centro dati secondario o su Azure (a seconda lo scenario) e quindi aggiorna periodicamente le repliche. Durante la pianificazione dell'infrastruttura, struttura della rete deve essere considerato come bottiglia potenziale che possono impedire la corretta è società riunione RTO e rilasciato obiettivi.  

Quando gli amministratori prevede di distribuire una soluzione di ripristino di emergenza, una delle domande in prestando attenzione ai è come la macchina virtuale è raggiungibile dopo il failover, viene completato. Ripristino automatico di sistema consente all'amministratore di scegliere la rete in cui verrebbero connessi una macchina virtuale dopo il failover. Se il sito principale è gestito da un server VMM questa operazione viene eseguita mediante il Mapping di rete. Per ulteriori informazioni, vedere [Preparazione per la connessione di rete](site-recovery-network-mapping.md) .

Durante la progettazione della rete per il sito di ripristino, l'amministratore è disponibili due opzioni:

- Utilizzare un intervallo di indirizzi IP per la rete al sito di ripristino. In questo scenario la macchina virtuale dopo failover verrà visualizzato un nuovo indirizzo IP e l'amministratore ha eseguire un aggiornamento DNS. Per saperne di più su come effettuare il DNS aggiornamento [qui](site-recovery-vmm-to-vmm.md#test-your-deployment) 
- Utilizzare stesso intervallo di indirizzi IP per la rete presso il ripristino. In alcuni scenari amministratori preferiscono mantenere gli indirizzi IP che hanno nel sito principale anche dopo il failover. In uno scenario normale un amministratore ha aggiornare le route per indicare la nuova posizione degli indirizzi IP. Ma in uno scenario in cui una VLAN allungata viene distribuita tra principale e i siti di ripristino, mantenendo gli indirizzi IP per macchine virtuali assume la forma di un'opzione interessante. Mantenere lo stesso IP indirizzi consente di semplificare il processo di ripristino accettando fuori sede qualsiasi rete correlati post failover passaggi.


Quando gli amministratori prevede di distribuire una soluzione di ripristino di emergenza, una delle domande in cui i è come le applicazioni sarà raggiungibile una volta completato il failover. Applicazioni moderne dipendono quasi sempre networking in una certa, pertanto fisica lo spostamento di che un servizio da un sito a un altro rappresenta un'operazione impegnativa social network. Esistono due modi principali per cui il problema viene gestito in soluzioni di ripristino di emergenza. Il primo approccio consiste nel Gestisci indirizzi IP fissi. Nonostante i servizi di spostamento e i server di hosting in posizioni fisiche diverse applicazioni eseguire la configurazione di indirizzi IP con loro nella nuova posizione. Il secondo approccio comporta completamente l'indirizzo IP durante la transizione nel sito recuperato. Ogni approccio presenta diverse varianti di implementazione vengono riepilogate sotto.

Durante la progettazione della rete per il sito di ripristino, l'amministratore è disponibili due opzioni:

## <a name="option-1-retain-ip-addresses"></a>Opzione 1: Mantenere gli indirizzi IP 

Da una prospettiva di processo di ripristino di emergenza, utilizzando IP fisso indirizzi sembra essere il metodo più semplice per implementare ma ha un numero di potenziali problemi che in pratica l'approccio meno comuni. Il ripristino del sito Azure consente di mantenere gli indirizzi IP in tutti gli scenari. Prima di uno decide di trattenere IP, appropriato attenzione ai vincoli che impone le funzionalità di failover. Analizzate i fattori che consentono di prendere una decisione per mantenere gli indirizzi IP, o meno. Può essere ottenuto in due modi, utilizzando una subnet allungata o in caso di errore subnet completo.

### <a name="stretched-subnet"></a>Subnet esteso

Di seguito alla subnet è disponibile contemporaneamente in principale e posizioni di DR. In altre parole indica che è possibile spostare un server e la configurazione IP (livello 3) per il secondo sito e la rete indirizza il traffico nella nuova posizione automaticamente. Si tratta di affrontare dal punto di vista server semplice ma presenta diversi problemi:

- Dal punto di vista (livello di dati collegamento) di livello 2, sarà necessario apparecchiature di rete in grado di gestire una VLAN allungata, ma questo è diventato minore di un problema in quanto è ora ampiamente disponibile. Il problema secondo e più difficile è che allontanando VLAN il dominio guasto potenziali viene estesa a entrambi i siti, essenzialmente a un singolo punto di errore. Mentre si tratta di una condizione improbabile, questa operazione è stata che una trasmissione eccesso avviato ma non può essere isolato. È stato trovato misti opinioni quest'ultimo problema e sono visibili corretta implementazione di molti nonché "è non verrà implementata mai questa tecnologia qui".
- Subnet allungato non è possibile se si usa Microsoft Azure come sito DR.


### <a name="subnet-failover"></a>Failover subnet

È possibile implementare failover subnet per ottenere i vantaggi della soluzione subnet allungato descritto in precedenza senza allontanare alla subnet in più siti. Qui qualsiasi subnet specificato è presente nel sito 1 o 2 di sito, ma non in entrambi i siti contemporaneamente. Per mantenere lo spazio di indirizzi IP invariato, è possibile disporre a livello di programmazione per l'infrastruttura di router spostare le subnet da un sito. In uno scenario di failover che subnet sposta con associato protetto macchine virtuali. Il principale svantaggio di questo approccio è in caso di errore che è necessario spostare l'intera subnet, potrebbe essere OK ma può influire le considerazioni granularità failover. 

Esaminiamo come un'azienda fittizia denominata Contoso è in grado di replicare relativa macchine virtuali in un percorso di ripristino durante l'esito negativo su intera subnet. Prima di tutto esamineremo come Contoso è in grado di gestire le subnet, mentre la replica macchine virtuali tra due locale percorsi e quindi verranno illustrati subnet failover funzionamento quando [che Azure viene utilizzato come il sito di ripristino di emergenza](#failover-to-azure).

#### <a name="failover-to-a-secondary-on-premises-site"></a>Il controllo a un sito secondario in locale

Analizzate uno scenario in cui è importante mantenere l'indirizzo IP di ciascuna delle macchine virtuali e fail-over subnet completa insieme. Sul sito principale sono in esecuzione in subnet 192.168.1.0/24 le applicazioni. Quando si verifica il failover, tutte le macchine virtuali che fanno parte di questa subnet verrà eseguite al sito di ripristino e mantenere gli indirizzi IP. Indirizza sarà necessario modificare in modo appropriato per riflettere il fatto che tutte le macchine virtuali che appartiene alla subnet 192.168.1.0/24 a questo punto sono state spostate al sito di ripristino. 

Nella figura seguente indirizza tra sito principale e ripristino sito, terzo sito e sito principale e terzo sito e il ripristino sarà necessario modificare in modo appropriato. 

Le immagini seguenti Mostra le subnet prima il failover. Subnet 192.168.0.1/24 è attivo nel sito principale prima il failover e diventa attiva del sito di ripristino dopo il failover 

![Prima di Failover](./media/site-recovery-network-design/network-design2.png)

Prima di failover


Nell'immagine seguente mostra le reti e subnet dopo il failover.
    
![Dopo il Failover](./media/site-recovery-network-design/network-design3.png)

Dopo il failover

Nel sito secondario è locale e si utilizza un server VMM per gestire l'allocazione di risorse di rete in base al flusso di lavoro seguente quando si abilita la protezione per una macchina virtuale specifica, ripristino automatico di sistema:

- Ripristino assegna un indirizzo IP per ogni interfaccia di rete del computer virtuale dal pool di indirizzi IP statico definito nella rete pertinenti per ogni istanza di System Center VMM.
- Se l'amministratore definisce stesso pool di indirizzi IP per la rete nel sito di ripristino del pool di indirizzi IP della rete nel sito principale, mentre allocazione l'indirizzo IP alla macchina virtuale replica automatico da allocare lo stesso indirizzo IP a quello della macchina virtuale principale.  L'indirizzo IP riservato in VMM ma non impostare come failover IP. Appena prima del failover Failover IP.

![Mantenere l'indirizzo IP](./media/site-recovery-network-design/network-design4.png)
    
Figura 5

Figura 5 mostra le impostazioni TCP/IP Failover per la macchina virtuale replica (nella console di Hyper-V). Queste impostazioni da popolati prima che la macchina virtuale viene avviata dopo caso di errore

Se lo stesso IP non è disponibile, ripristino da allocare alcuni altri indirizzi IP disponibili dal pool di indirizzi IP definito. 

Dopo la macchina virtuale è abilitata per la protezione è possibile utilizzare le seguenti script di esempio per verificare l'indirizzo IP allocato alla macchina virtuale. Lo stesso IP da impostare come Failover IP e assegnato a macchina virtuale al momento del failover:

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

>[AZURE.NOTE] In uno scenario in macchine virtuali di utilizzare DHCP, la gestione degli indirizzi IP non è completamente compreso il controllo automatico di. Un amministratore deve assicurarsi che il server DHCP che servono gli indirizzi IP del sito di ripristino può fungere dallo stesso intervallo di quella del sito principale.

#### <a name="failover-to-azure"></a>Il controllo a Azure

Azure sito ripristino automatico consente a Microsoft Azure da utilizzare come un sito di ripristino di emergenza per le macchine virtuali. In questo caso, è necessario gestire con uno più vincolo. 

Esaminiamo uno scenario in una società fittizia denominata banca ha infrastruttura locale che ospita le applicazioni line-of business e ospita le applicazioni per dispositivi mobili in Azure. Connessione tra macchine virtuali bancario Woodgrove nel server Azure e locale viene fornita da un sito a sito (S2S) virtuale Private Network (VPN). S2S VPN consente virtuali della banca Azure visto come un'estensione della rete locale della banca. Questo tipo di comunicazione è abilitato per VPN S2S tra banca bordo e Azure virtuali. Woodgrove desidera utilizzare il ripristino automatico replicare i carichi di lavoro in esecuzione nel Data Center di Azure. Questa opzione soddisfa le esigenze di Woodgrove, che richiede un'opzione DR economica ed è possibile archiviare dati in ambienti cloud pubblico. Woodgrove ha di occuparsi di applicazioni e configurazioni che dipendono da indirizzi IP a livello di codice, pertanto hanno l'esigenza di mantenere gli indirizzi IP per le applicazioni dopo il ripristino nel Azure.

Woodgrove ha deciso di assegnare indirizzi IP degli intervalli di indirizzi IP (172.16.1.0/24, 172.16.2.0/24) per le risorse in esecuzione in Azure.

Per Woodgrove per poter replicare relative macchine virtuali di Azure mantenendo gli indirizzi IP, è necessario creare una rete virtuale Azure. Estensione della rete locale deve essere in modo che le applicazioni possono facilmente il controllo del sito locale a Azure. Azure consente di aggiungere la connettività VPN da sito, nonché punto al sito per le reti virtuali create in Azure. Quando si configura la connessione al sito, rete Azure consente di indirizzare il traffico verso la posizione locale (Azure denominandolo rete locale) solo se l'intervallo di indirizzi IP è diverso dall'intervallo di indirizzi IP locale, perché Azure non supporta l'estensione subnet.  Ciò significa che se si dispone di un 192.168.1.0/24 subnet locale, non è possibile aggiungere una rete locale 192.168.1.0/24 nella rete Azure. È previsto in quanto non conosce Azure non sono presenti macchine virtuali attive nella subnet e la creazione di subnet solo a fini DR. Per poter inviare correttamente il traffico di rete all'esterno di una rete Azure subnet nella rete e la rete locale deve non sono in conflitto. 

![Prima del Failover Subnet](./media/site-recovery-network-design/network-design7.png)

Prima di failover

Per informazioni su come Woodgrove soddisfare i requisiti aziendali, è necessario implementare i flussi di lavoro seguenti:

- Creare una rete aggiuntiva, possiamo chiamarlo rete di ripristino, in cui verranno creati virtuali sopra non è riuscita.
- Per assicurarsi che l'indirizzo IP per una macchina virtuale viene mantenuto dopo caso di errore, fare clic sulla scheda Configura in proprietà macchine Virtuali, specificare lo stesso IP che la macchina virtuale è locale e quindi fare clic su Salva. Quando viene terminata la macchina virtuale, il ripristino del sito di Azure assegna IP fornito la macchina virtuale. 

![Proprietà di rete](./media/site-recovery-network-design/network-design8.png)

Dopo il failover viene attivato e macchine virtuali vengono create nella rete ripristino con IP desiderato, è possibile stabilire la connettività a questa rete utilizzando un [Vnet a Vnet connessione](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Se necessario questa azione è possibile creare uno.  Come illustrato nella sezione precedente sul failover subnet, anche per il controllo a Azure, indirizza avrebbero dovuto essere modificati in modo appropriato per indicare che 192.168.1.0/24 a questo punto è stato spostato in Azure. 

![Dopo il Failover Subnet](./media/site-recovery-network-design/network-design9.png)

Dopo il failover

Se non si ha una rete di Azure' ' come illustrato nella figura riportata sopra. È possibile creare una connessione vpn da un sito tra il "Sito principale" e "Ripristino Network" dopo il failover.  


## <a name="option-2-changing-ip-addresses"></a>Opzione 2: Modifica degli indirizzi IP

Questo approccio potrebbe essere più diffusi in base a quali finora. Utilizza il formato di modifica dell'indirizzo IP di ogni macchina virtuale che partecipa al failover. Un rimborso di questo approccio richiede la rete in ingresso ' informazioni ' che l'applicazione al IPx è ora in IPy. Anche se IPx e IPy sono i nomi logici, voci DNS in genere devono essere modificate o svuotato all'interno della rete e voci memorizzate nella cache nelle tabelle di rete sono state aggiornate o svuotato, pertanto potrebbe essere visualizzato un tempo di inattività in base a come è stata impostata l'infrastruttura DNS. L'evitando utilizzando i valori TTL bassi nel caso di applicazioni intranet e utilizzando [Manager il traffico di Azure con ripristino automatico di sistema](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) per le applicazioni basati su internet

### <a name="changing-the-ip-addresses---illustration"></a>Modificare gli indirizzi IP - illustrazione

Analizzate lo scenario in cui si prevede di utilizzare diversi IP vari principale e i siti di ripristino. Nell'esempio seguente viene anche abbiamo una terza sito nel punto in cui le applicazioni installate in principale o ripristino è possibile accedere al sito.

![IP diversi - prima del Failover](./media/site-recovery-network-design/network-design10.png)

Figura 11

Nella figura 11 sono disponibili alcune applicazioni ospitate in subnet 192.168.1.0/24 subnet nel sito principale e sono stati configurati per sorgere nel sito del ripristino in subnet 172.16.1.0/24 dopo caso di errore. Indirizza connessioni/rete VPN è state configurate correttamente in modo che tutti e tre i siti possano accedere a altro.
 
Come figura 12 mostra, dopo il ripristino in una o più applicazioni, caso verranno ripristinati nella subnet ripristino. In questo caso è non stiamo è vincolati a failover intera subnet nello stesso momento. Non sono necessarie modifiche di riconfigurare indirizza VPN o in rete. Caso di errore e alcuni aggiornamenti DNS renderà sempre applicazioni accessibili. Se il DNS è configurato per consentirli macchine virtuali da registrare se stessi utilizzando il nuovo PI quando deve iniziare dopo caso di errore. 

![IP diversi - dopo il Failover](./media/site-recovery-network-design/network-design11.png)

Figura 12

Dopo aver errore sopra la macchina virtuale replica potrebbe essere un indirizzo IP che non è lo stesso indirizzo IP del computer virtuale principale. Macchine virtuali aggiorneranno i server DNS che utilizzano dopo l'avvio. Le voci DNS in genere devono essere modificate o svuotato all'interno della rete e voci memorizzate nella cache nelle tabelle di rete sono state aggiornate o svuotato, pertanto non è insolito affrontare i tempi di inattività mentre queste modifiche avranno luogo. È possibile evitando questo problema:

- Utilizzando i valori TTL bassi per le applicazioni intranet.
- Applicazioni basate su con Azure il traffico Manager ripristino automatico di sistema per internet.
- Tramite il seguente script all'interno di un piano di ripristino per aggiornare il Server DNS per garantire un aggiornamento tempestivo (lo script non è necessario se la registrazione dinamica DNS è configurata)

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


### <a name="changing-the-ip-addresses--dr-to-azure"></a>Modificare gli indirizzi IP – DR in Azure

Post di blog [Impostazione dell'infrastruttura di rete per Microsoft Azure come un sito di ripristino di emergenza](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) viene illustrato come configurare l'infrastruttura di rete Azure necessario quando mantenendo gli indirizzi IP non è un requisito. Inizia con che descrive l'applicazione e quindi esaminare le modalità di installazione locale social network e Azure e quindi concludere con come eseguire test caso di errore e pianificato in caso di errore.



## <a name="next-steps"></a>Passaggi successivi

[Informazioni](site-recovery-network-mapping.md) sul ripristino del sito mappe reti di origine e destinazione quando un server VMM utilizzato per gestire il sito principale. 
