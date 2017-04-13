<properties
   pageTitle="Requisiti di sistema StorSimple | Microsoft Azure"
   description="Descrive, reti, requisiti software e disponibilità e procedure consigliate per una soluzione di Microsoft Azure StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/31/2016"
   ms.author="alkohli"/>

# <a name="storsimple-software-high-availability-and-networking-requirements"></a>Software StorSimple, disponibilità e requisiti di rete

## <a name="overview"></a>Panoramica

Introduzione a Microsoft Azure StorSimple. In questo articolo vengono illustrati i requisiti di sistema importanti e le procedure consigliate per il dispositivo StorSimple e per i client di spazio di archiviazione che accedono il dispositivo. È consigliabile esaminare attentamente le informazioni prima di distribuire il sistema StorSimple e quindi fare riferimento a tale in base alle esigenze durante la distribuzione e operazione successiva.

I requisiti di sistema includono:

- **Requisiti software per i client di spazio di archiviazione** - descrive i sistemi operativi supportati e requisiti aggiuntivi per tali sistemi operativi.
- **Requisiti per il dispositivo di StorSimple di rete** - fornisce informazioni sulle porte che devono essere aperte nel firewall per consentire il traffico di gestione iSCSI o cloud.
- **Requisiti di disponibilità per StorSimple** - vengono illustrati i requisiti di disponibilità e le procedure consigliate per il computer host e dispositivo StorSimple. 


## <a name="software-requirements-for-storage-clients"></a>Requisiti software per i client di spazio di archiviazione

I seguenti requisiti software disponibili per i client di spazio di archiviazione che accedere al dispositivo StorSimple.

| Sistemi operativi supportati | Versione richiesta | Requisiti aggiuntive/note |
| --------------------------- | ---------------- | ------------- |
| Windows Server              | 2008 R2 SP1, 2012, 2012R2 |StorSimple iSCSI volumi sono supportati per l'uso nelle solo i tipi di disco di Windows seguenti:<ul><li>Volume semplice sul disco di base</li><li>Semplice e speculare volume disco dinamico</li></ul>Se si utilizza un volume iSCSI StorSimple, sono supportati il provisioning sottile di Windows Server 2012 e caratteristiche ODX.<br><br>StorSimple possono creare eseguono provisioning e completamente provisioning volumi. Non sarà possibile creare volumi parzialmente provisioning.<br><br>La formattazione di un volume eseguono provisioning può richiedere molto tempo. È consigliabile eliminare il volume e quindi crearne uno nuovo anziché la formattazione. Tuttavia, se il problema persiste preferisce riformattare un volume:<ul><li>Eseguire il seguente comando prima di riformattato per evitare ritardi richiesta spazio: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Al termine la formattazione, utilizzare il comando seguente per abilitare nuovamente il recupero di spazio:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Applicare la correzione di Windows Server 2012 come descritto [nell'articolo della Knowledge 2878635](https://support.microsoft.com/kb/2870270) nel computer Windows Server.</li></ul></li></ul></ul> Se si sta configurando StorSimple Snapshot responsabile o scheda StorSimple per SharePoint, passare ai [requisiti Software per componenti facoltativi](#software-requirements-for-optional-components).|
| VMWare ESX | 5.5 e 6.0 | È supportata con VMWare vSphere come iSCSI client. Funzionalità di blocco VAAI è supportato con VMware vSphere nei dispositivi StorSimple.
| Linux RHEL/CentOS | 5, 6 e 7 | Supporto per Linux iSCSI client con le versioni iniziatore iSCSI Apri 5, 6 e 7. |
| Linux | SUSE Linux 11 | |
 > [AZURE.NOTE] IBM AIX non è attualmente supportata con StorSimple.

## <a name="software-requirements-for-optional-components"></a>Requisiti software per componenti facoltativi

I seguenti requisiti software disponibili per i componenti StorSimple facoltativi (StorSimple Snapshot Manager e scheda StorSimple per SharePoint).

| Componente | Piattaforma host | Requisiti aggiuntive/note |
| --------------------------- | ---------------- | ------------- |
| Gestione di Snapshot StorSimple | Windows Server 2008 R2 SP1, 2012, 2012R2 | Utilizzo di StorSimple Snapshot Manager in Windows Server è richiesto per backup o ripristino di dischi dinamici speculari e per i backup applicazione coerente.<br> Gestione di Snapshot StorSimple è supportato solo in Windows Server 2008 R2 SP1 (64 bit), Windows 2012 R2 e Windows Server 2012.<ul><li>Se si Usa finestra Server 2012, è necessario installare .NET 3.5 – 4.5 prima di installare StorSimple Snapshot Manager.</li><li>Se si utilizza Windows Server 2008 R2 SP1, è necessario installare Windows Management Framework 3.0 prima di installare StorSimple Snapshot Manager.</li></ul> |
| Scheda StorSimple per SharePoint | Windows Server 2008 R2 SP1, 2012, 2012R2 |<ul><li>Scheda StorSimple per SharePoint è disponibile solo in SharePoint 2010 e SharePoint 2013.</li><li>RBS richiede SQL Server Enterprise Edition, versione 2008 R2 o 2012.</li></ul>|

## <a name="networking-requirements-for-your-storsimple-device"></a>Requisiti per il dispositivo di StorSimple di rete

Il dispositivo StorSimple è un dispositivo bloccato. Tuttavia, le porte necessario aprire nel firewall per consentire il traffico di gestione iSCSI e cloud. Nella tabella seguente elenca le porte che devono essere aperte nel firewall. In questa tabella, *in* *entrata* o in si intende la direzione in cui le richieste in arrivo accedere il dispositivo. *Indietro* o *in uscita* si intende la direzione in cui il dispositivo StorSimple invia dati esternamente, oltre alla distribuzione: ad esempio, in uscita a Internet.

| Numero di porta<sup>1,2</sup> | Avanti o indietro | Ambito porta | Obbligatorio | Note |
|------------------------|-----------|------------|----------|-------|
|TCP 80 (HTTP)<sup>3</sup>|  In uscita |  WAN | No |<ul><li>Porta in uscita viene usata per l'accesso a Internet per recuperare gli aggiornamenti.</li><li>Il proxy web in uscita è configurabile dall'utente.</li><li>Per consentire gli aggiornamenti del sistema, questa porta deve essere aperta per controller fissato IP.</li></ul> |
|TCP 443 (HTTPS)<sup>3</sup>| In uscita | WAN | Sì |<ul><li>Porta in uscita usata per accedere ai dati nel cloud.</li><li>Il proxy web in uscita è configurabile dall'utente.</li><li>Per consentire gli aggiornamenti del sistema, questa porta deve essere aperta per controller fissato IP.</li><li>Questa porta viene usata anche in entrambi i controller per simultanea.</li></ul>|
|UDP 53 (DNS) | In uscita | WAN | In alcuni casi; vedere le note. |È necessario solo se si usa un server DNS basato su Internet. |
| UDP 123 (NTP) | In uscita | WAN | In alcuni casi; vedere le note. |È necessario solo se si usa un server NTP basati su Internet. |
| TCP 9354 | In uscita | WAN | Sì |Porta in uscita usata dal dispositivo StorSimple per comunicare con il servizio di gestione StorSimple. |
| 3260 (iSCSI) | In | LAN | No | Questa porta viene utilizzata per accedere ai dati su iSCSI.|
| 5985 | In | LAN | No | Porta in ingresso viene utilizzata da Gestione Snapshot StorSimple per comunicare con il dispositivo StorSimple.<br>Questa porta viene usata anche quando è in modalità remota connettersi a Windows PowerShell per StorSimple su HTTP. |
| 5986 | In | LAN | No | Questa porta viene utilizzata quando è in modalità remota connettersi a Windows PowerShell per StorSimple su HTTPS. |

<sup>1</sup> non porte in ingresso devono essere aperte su Internet.

<sup>2</sup> se più porte eseguire una configurazione di gateway, l'ordine di traffico in uscita indirizzati sarà determinato in base all'ordine di routing porta in [porta routing](#routing-metric), descritto di seguito.

<sup>3</sup> controller fissato IP nel dispositivo StorSimple deve essere routing e grado di connettersi a Internet. Indirizzi IP fissi vengono utilizzati per gestire gli aggiornamenti al dispositivo. Se i controller di dispositivo non riesce a connettersi a Internet tramite gli indirizzi IP fissi, non sarà possibile aggiornare il dispositivo StorSimple.

> [AZURE.IMPORTANT] Verificare che il firewall non modificare o decrittografare il traffico SSL tra il dispositivo di StorSimple e Azure.

### <a name="url-patterns-for-firewall-rules"></a>Modelli di URL per le regole firewall

Gli amministratori di rete spesso possono configurare le regole firewall avanzato sulla base di modelli di URL per filtrare l'in ingresso e il traffico in uscita. Il dispositivo StorSimple e il servizio di gestione StorSimple dipendono dalle altre applicazioni di Microsoft, ad esempio Bus di servizio Azure, controllo di accesso di Azure Active Directory, gli account di archiviazione e server Microsoft Update. I modelli di URL associati a queste applicazioni possono essere utilizzati per configurare le regole del firewall. È importante tenere presente che non è possano modificare i modelli di URL associati a queste applicazioni. Sarà necessario a sua volta l'amministratore di rete monitorare e aggiornare le regole del firewall per il StorSimple come e, se necessario.

È consigliabile impostare le regole del firewall per il traffico in uscita, in base a StorSimple fissato liberamente indirizzi IP, nella maggior parte dei casi. Tuttavia, è possibile utilizzare le informazioni seguenti per impostare le regole firewall avanzato necessari per creare ambienti protetti.

> [AZURE.NOTE] Il dispositivo (origine) IP impostare sempre a tutte le interfacce di rete. La destinazione che IP deve essere impostato su [intervalli di indirizzi IP di Azure Data Center](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).

#### <a name="url-patterns-for-azure-portal"></a>Modelli di URL per il portale di Azure
| Modello di URL                                                      | Componente/funzionalità                                           | IP di dispositivo                           |
|------------------------------------------------------------------|---------------------------------------------------------------|-----------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`   | Servizio di gestione StorSimple<br>Servizio controllo di accesso<br>Bus di servizio Azure| Interfacce di rete basate su cloud        |
|`https://*.backup.windowsazure.com`|Registrazione periferica| DATI 0|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Revoca certificato |Interfacce di rete basate su cloud |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Gli account di archiviazione Azure e il monitoraggio | Interfacce di rete basate su cloud        |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Server di Microsoft Update<br>                             | Controller fissato solo indirizzi IP               |
| `http://*.deploy.akamaitechnologies.com`                         |Rete CDN Akamai |Controller fissato solo indirizzi IP   |
| `https://*.partners.extranet.microsoft.com/*`                    | Pacchetto di supporto                                                  | Interfacce di rete basate su cloud        |

#### <a name="url-patterns-for-azure-government-portal"></a>Modelli di URL per il portale di Azure per la pubblica amministrazione
| Modello di URL                                                      | Componente/funzionalità                                           | IP di dispositivo                           |
|------------------------------------------------------------------|---------------------------------------------------------------|-----------------------------------------|
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`   | Servizio di gestione StorSimple<br>Servizio controllo di accesso<br>Bus di servizio Azure| Interfacce di rete basate su cloud        |
|`https://*.backup.windowsazure.us`|Registrazione periferica| DATI 0|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Revoca certificato |Interfacce di rete basate su cloud |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Gli account di archiviazione Azure e il monitoraggio | Interfacce di rete basate su cloud        |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Server di Microsoft Update<br>                             | Controller fissato solo indirizzi IP               |
| `http://*.deploy.akamaitechnologies.com`                         |Rete CDN Akamai |Controller fissato solo indirizzi IP   |
| `https://*.partners.extranet.microsoft.com/*`                    | Pacchetto di supporto                                                  | Interfacce di rete basate su cloud        |

### <a name="routing-metric"></a>Metrica di routing

Metrica di routing è associata alle interfacce e gateway che inviano i dati per le reti specificate. Metrica di routing viene utilizzato il protocollo di routing per calcolare il percorso migliore a una destinazione specificata, se viene a conoscenza che sono presenti più percorsi per la stessa destinazione. Il valore più basso la metrica di routing, maggiore sarà la preferenza.

Nel contesto di StorSimple, se più interfacce di rete e gateway sono configurati per il traffico del canale metriche di routing accedono Riproduci per determinare l'ordine relativo in cui verranno abituarsi le interfacce. Metriche di routing non possono essere modificate dall'utente. Tuttavia, è possibile utilizzare il `Get-HcsRoutingTable` cmdlet per stampare la tabella routing (e metriche) in un dispositivo StorSimple. Ulteriori informazioni sui cmdlet Get-HcsRoutingTable [StorSimple risoluzione dei problemi di](storsimple-troubleshoot-deployment.md)distribuzione.

I routing algoritmi metrici sono diversi a seconda della versione di software in esecuzione sul dispositivo StorSimple.

**Versioni prima dell'aggiornamento 1**

Sono inclusi versioni software prima dell'aggiornamento 1, ad esempio GA, 0,1, 0,2 o 0,3 release. L'ordine in base a metriche di routing è come indicato di seguito:

   *Ultima configurato l'interfaccia di rete 10 GbE > interfaccia di rete di altri 10 GbE > ultimo configurato l'interfaccia di rete 1 GbE > interfaccia di rete altre GbE di 1*


**Versioni a partire da 1 di aggiornamento e prima dell'aggiornamento 2**

Sono incluse le versioni di software, ad esempio 1, 1.1 o 1.2. L'ordine in base a metriche di routing è deciso quanto segue:

   *DATI 0 > ultimo configurato l'interfaccia di rete 10 GbE > interfaccia di rete di altri 10 GbE > ultimo configurato 1 interfaccia di rete GbE > interfaccia di rete altre GbE 1*

   Nell'aggiornamento 1, viene effettuata la metrica di routing dei dati 0 più basso; di conseguenza, tutto il cloud traffico instradato dati 0. Se sono presenti più di un'interfaccia di rete basate su cloud sul dispositivo StorSimple, prendere nota dell'oggetto.


**Versioni a partire dall'aggiornamento 2**

Aggiornamento 2 presenta diversi miglioramenti correlate alla rete e le metriche di routing sono stata modificata. Il comportamento può essere spiegato come indicato di seguito.

- Un set di valori predefiniti sono stati assegnati alle interfacce di rete.   

- Valutare la possibilità di una tabella di esempio riportata di seguito con valori assegnati alle diverse interfacce di rete quando cloud-attivati o disabilitazione di cloud, ma con un gateway configurato. Nota i valori assegnati qui solo i valori di esempio.


  	| Interfaccia di rete | Basate su cloud | Cloud disabilitata con gateway |
  	|-----|---------------|---------------------------|
  	| Dati 0  | 1            | -                        |
  	| Dati 1  | 2            | 20                       |
  	| Dati 2  | 3            | 30                       |
  	| Dati 3  | 4            | 40                       |
  	| Dati 4  | 5            | 50                       |
  	| Dati 5  | 6            | 60                       |


- L'ordine in cui il traffico cloud instradato tramite le interfacce di rete è:

    *Dati 0 > dati 1 > Data 2 > dati 3 > dati 4 > dati 5*

    Ciò è dovuto nell'esempio seguente.

    Valutare la possibilità di un dispositivo StorSimple con due interfacce di rete basate su cloud, dati 0 e 5 di dati. Dati 1 a 4 dati disabilitata cloud sono, ma sono un gateway configurato. L'ordine in cui il traffico instradato dispositivo sarà:

    *Dati 0 (1) > dati 5 (6) > dati 1 (20) > dati 2 (30) > dati 3 (40) > dati 4 (50)*

    *nel punto in cui i numeri tra parentesi indicano i rispettive metriche di routing.*

    Se non riesce dati 0, il traffico cloud verrà instradato a 5 di dati. Dato che un gateway viene configurato in tutte le altre reti, se i dati 0 e 5 dati si verifica un errore, il traffico cloud verrà elaborata Data 1.


- Se un'interfaccia di rete basate su cloud non riesce, verranno visualizzate 3 tentativi con un ritardo 30 secondi per connettersi all'interfaccia. Se tutti i tentativi hanno esito negativo, il traffico è indirizzato al successiva disponibile basate su cloud interfaccia definito dalla tabella di routing. Se tutti di rete basate su cloud interfacce fail, il dispositivo riuscirà sopra al controller di altri (Nessun riavvio in questo caso).

- Se si verifica un errore VIP per un'interfaccia di rete iSCSI, si verificherà 3 tentativi con un ritardo di 2 secondi. Questo comportamento è rimasto lo stesso da versioni precedenti. Se tutte le interfacce di rete iSCSI non riescono, caso di errore controller verificherà (accompagnate da riavviare il computer).


- Sul dispositivo StorSimple viene inoltre generato un avviso quando si verifica un errore VIP. Per ulteriori informazioni, vedere [avviso di riferimento rapido](storsimple-manage-alerts.md).

- In termini di tentativi di iSCSI avrà la precedenza su cloud.

    Tenere presente quanto segue: StorSimple di un dispositivo ha due interfacce di rete abilitate, i dati 0 e 1 di dati. Dati 0 sono abilitato al cloud mentre Data 1 entrambi cloud e abilitato iSCSI. Altre interfacce di rete nel dispositivo in uso non sono abilitate per cloud o iSCSI.

    Se ha esito negativo dati 1, dato è l'interfaccia di rete iSCSI ultima, verrà creato in caso di errore controller alla Data 1 in altro controller.


### <a name="networking-best-practices"></a>Procedure consigliate di rete

Oltre ai requisiti di reti sopra, per ottimizzare le prestazioni della soluzione StorSimple rispettare le procedure consigliate seguenti:

- Assicurarsi che il dispositivo StorSimple ha una larghezza di banda Mbps 40 dedicato (o più) disponibili in qualsiasi momento. La larghezza di banda non deve essere condivise (o allocazione dovrebbe essere garantito mediante l'utilizzo di criteri QoS) con altre applicazioni.

- Verificare la connettività di rete a Internet è disponibile in qualsiasi momento. Sporadiche o origini non affidabili connessioni Internet a dispositivi, non inclusi connessione a Internet qualsiasi, verranno generato in una configurazione non supportata.

- Isolare il traffico iSCSI e cloud verificano dedicato interfacce di rete in un dispositivo per l'accesso iSCSI e cloud. Per ulteriori informazioni, vedere come [modificare le interfacce di rete](storsimple-modify-device-config.md#modify-network-interfaces) in un dispositivo StorSimple.

- Non utilizzare una configurazione collegamento aggregazione controllo Protocol (LACP) per le interfacce di rete. Si tratta di una configurazione non supportata.


## <a name="high-availability-requirements-for-storsimple"></a>Requisiti di disponibilità per StorSimple

La piattaforma hardware incluso con la soluzione StorSimple include caratteristiche di disponibilità e l'affidabilità che offrono una base per un'infrastruttura di archiviazione di disponibilità, tolleranza nel centro dati. Esistono tuttavia requisiti e procedure consigliate che devono essere conformi per garantire la disponibilità della soluzione StorSimple. Prima di distribuire StorSimple, esaminare attentamente i seguenti requisiti e procedure consigliate per StorSimple dispositivi e computer host connesso.

Per ulteriori informazioni sul monitoraggio e gestione dei componenti hardware del dispositivo StorSimple, passare a [usare il servizio di gestione di StorSimple per monitorare lo stato e componenti hardware](storsimple-monitor-hardware-status.md) e [sostituzione di componenti hardware StorSimple](storsimple-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Requisiti di disponibilità e le procedure per il dispositivo StorSimple

Esaminare le informazioni seguenti con attenzione per assicurare la disponibilità del dispositivo StorSimple.

#### <a name="pcms"></a>PCMs

Dispositivi StorSimple includono power ridondanti, a caldo e raffreddamento moduli (PCMs). Ogni PCM capacità sia sufficiente per fornire il servizio per l'intero chassis. Per garantire disponibilità, è necessario installare entrambe PCMs.

- Connettere il PCMs fonti di alimentazione per garantire disponibilità se si verifica un errore di alimentazione.
- Se un PCM non riesce, richiedere immediatamente in sostituzione.
- Rimuovere un PCM non riuscito solo quando ha la sostituzione e si è pronti per installarlo.
- Non rimuovere entrambi PCMs contemporaneamente. Il modulo PCM include il modulo di riserva. Rimozione di entrambe le PCMs verrà generato un arresto senza protezione batteria e lo stato del dispositivo non verranno salvata. Per ulteriori informazioni sulla pila, passare a [gestire il modulo di riserva](storsimple-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Moduli controller

Dispositivi StorSimple includono controller ridondanti, a caldo moduli. I moduli controller funzionano in modo attivo/passivo. In qualsiasi momento, un modulo di controller è attivo e fornisce servizio, mentre l'altro modulo controller è passivo. Il modulo controller passiva è acceso ed è operativo se il modulo controller active non funziona o viene rimosso. Ogni modulo controller capacità sia sufficiente per fornire il servizio per l'intero chassis. Entrambi i moduli controller devono essere installati per assicurare la disponibilità elevata.

- Verificare che entrambi i moduli controller vengono installati in qualsiasi momento.

- Se un modulo controller non riesce, richiedere immediatamente in sostituzione.

- Rimuovere un modulo controller non riuscito solo quando ha la sostituzione e si è pronti per installarlo. La rimozione di un modulo per lunghi periodi di tempo viene influenzata al flusso d'aria e quindi il raffreddamento del sistema.

- Assicurarsi che le connessioni di rete per entrambi i moduli controller sono identiche e le interfacce di rete connessa hanno una configurazione di rete identiche.

- Se un modulo controller ha esito negativo o debba essere sostituita, assicurarsi che l'altro modulo controller sia lo stato attivo prima di sostituire il modulo controller non riuscito. Per verificare che sia attivo un controller, passare a [identificare controller attivo nel dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- Non rimuovere entrambi i moduli controller nello stesso momento. Se controller caso di errore non è in corso, non chiudere il modulo controller standby o rimuoverlo dal telaio.

- Dopo aver caso di errore controller, attendere almeno cinque minuti prima di rimuovere uno dei due modulo controller.

#### <a name="network-interfaces"></a>Interfacce di rete

StorSimple dispositivo controller moduli ogni sono quattro 1 GB e due 10 interfacce di rete Gigabit Ethernet.

- Assicurarsi che le connessioni di rete per entrambi i moduli controller sono identiche e interfacce di rete che si è connessi le interfacce di modulo controller di disporre di una configurazione di rete identiche.

- Se possibile, distribuire le connessioni di rete tra diverse opzioni per assicurare la disponibilità di servizio in caso di un errore di dispositivi di rete.

- Quando scollegare l'unico o l'ultimo rimanente abilitato iSCSI interfaccia (con indirizzi IP assegnato), disattivare l'interfaccia prima di tutto e quindi scollegare i cavi. Se l'interfaccia è scollegato prima di tutto, quindi causerà controller attivo venga reindirizzato a controller passivo. Se il controller passivo ha anche le interfacce corrispondenti scollegate, entrambi i controller riavviare prima di liquidazione in un controller più volte.

- Connettere almeno due interfacce dati alla rete da ogni modulo controller.

- Se è stata attivata due 10 interfacce GbE, distribuire quelli tra diverse opzioni.

- Se possibile, utilizzare MPIO sui server per assicurarsi che il server tollerabile un collegamento, reti o errore dell'interfaccia.

Per ulteriori informazioni sulle reti il dispositivo per la disponibilità e prestazioni ottimali, passare a [installare il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) o [dispositivo 8600 StorSimple](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSDs e dischi rigidi

Dispositivi StorSimple includono dischi stato a tinta unita (SSDs) e dischi rigidi (dischi rigidi) sono protetti tramite riflesso spazi. Utilizzo di spazi speculari assicura che il dispositivo in grado di tollerare l'errore di uno o più SSDs o dischi rigidi.

- Assicurarsi che siano installati tutti i moduli SSD e sul disco rigido.

- Se un SSD o sul disco rigido non riesce, richiedere immediatamente in sostituzione.

- Se un SSD o sul disco rigido non funziona o sostituzione, assicurarsi che si rimuove solo i SSD o sul disco rigido che richiede di sostituzione.

- Non rimuovere più SSD o sul disco rigido dal sistema in qualsiasi punto nel tempo.
Un errore di 2 o più dischi di determinati tipo (disco rigido, SSD) o errori consecutivi all'interno di un intervallo di tempo breve potrebbero causare la perdita di dati disfunzione e potenziale sistema. In questo caso, [contattare il supporto Microsoft](storsimple-contact-microsoft-support.md) per assistenza.

- Durante la sostituzione, monitorare lo **Stato di Hardware** nella pagina **manutenzione** per le unità nelle SSDs e dischi rigidi. Lo stato di segno di spunta verde indica che i dischi integro o OK, mentre un punto esclamativo rosso puntano indica un errore SSD o sul disco rigido.

- È consigliabile configurare snapshot cloud per tutti i volumi che è necessario per la protezione in caso di un errore di sistema.

#### <a name="ebod-enclosure"></a>Simbolo EBOD

Modello di dispositivo StorSimple 8600 include un simbolo esteso insieme di dischi (EBOD) oltre a simbolo principale. Un EBOD contiene controller di EBOD e dischi rigidi (dischi rigidi) sono protetti tramite riflesso spazi. Utilizzo di spazi speculari assicura che il dispositivo in grado di tollerare l'errore di uno o più dischi rigidi. Simbolo EBOD sia connesso a simbolo principale tramite cavi SA ridondanti.

- Verificare che entrambi EBOD simbolo controller i moduli, sia sa cavi e tutte le unità disco rigido sono installate in qualsiasi momento.

- Se un modulo di controller simbolo EBOD non riesce, richiedere immediatamente in sostituzione.

- Se un modulo di controller simbolo EBOD non riesce, verificare che l'altro modulo controller sia attivo prima di sostituire il modulo non riuscito. Per verificare che sia attivo un controller, passare a [identificare controller attivo nel dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- Durante la sostituzione dei modulo un controller EBOD, controllare continuamente lo stato del componente nel servizio di gestione StorSimple accedendo **manutenzione** > **stato Hardware**.

- Se un cavo SA richiede sostituzione (supporto Microsoft deve essere coinvolti per eseguire tale operazione) o non riesce, assicurarsi che rimuovere solo il cavo sa che richiede di sostituzione.

- Non contemporaneamente rimuovere entrambi cavi SA dal sistema in qualsiasi punto nel tempo.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Consigli disponibilità per il computer host

Esaminare attentamente queste procedure consigliate per assicurare la disponibilità di host connessi al dispositivo StorSimple.

- Configurare StorSimple con [configurazioni cluster di file di due nodi server][1]. Per la rimozione di singoli punti di errore e la creazione di ridondanza sul lato host, l'intera soluzione diventa disponibilità.

- Usa sempre disponibile (CA) condivide disponibile con Windows Server 2012 (piccole e medie imprese 3.0) per la disponibilità elevata durante il failover dei controller di archiviazione. Per ulteriori informazioni sulla configurazione cluster di server file e nelle condivisioni sempre disponibile con Windows Server 2012, consultare la [demo video](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sui limiti di sistema StorSimple](storsimple-limits.md).
- [Informazioni su come distribuire la soluzione StorSimple](storsimple-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx
