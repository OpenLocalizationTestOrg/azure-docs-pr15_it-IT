<properties
   pageTitle="Requisiti di sistema StorSimple matrice virtuale"
   description="Conoscere il software e i requisiti di rete per la matrice virtuale StorSimple"
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
   ms.workload="NA"
   ms.date="10/17/2016"
   ms.author="alkohli"/>

# <a name="storsimple-virtual-array-system-requirements"></a>Requisiti di sistema StorSimple matrice virtuale

## <a name="overview"></a>Panoramica

In questo articolo vengono illustrati i requisiti di sistema importanti per la matrice di virtuali di Microsoft Azure StorSimple (noto anche come StorSimple locale virtuale o dispositivo virtuale StorSimple) e per i client di spazio di archiviazione accedere alla matrice. È consigliabile esaminare attentamente le informazioni prima di distribuire il sistema StorSimple e quindi fare riferimento a tale in base alle esigenze durante la distribuzione e operazione successiva.

I requisiti di sistema includono:

-   **Requisiti software per i client di spazio di archiviazione** - descrive le piattaforme di virtualizzazione supportati, web browser, iniziatori iSCSI, i client piccole e medie imprese, requisiti minimi dispositivo virtuale e requisiti aggiuntivi per tali sistemi operativi.

-   **Requisiti per il dispositivo di StorSimple di rete** - fornisce informazioni sulle porte che devono essere aperte nel firewall per consentire il traffico di gestione iSCSI o cloud.

Informazioni sui requisiti di sistema StorSimple pubblicate in questo articolo si applica solo a matrici virtuali StorSimple.

- Per i dispositivi di 8000 serie, passare ai [requisiti di sistema per il dispositivo di serie 8000 StorSimple](storsimple-system-requirements.md).
 
- Per i dispositivi di serie 7000, passare ai [requisiti di sistema del dispositivo serie 5000 7000 StorSimple](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).


## <a name="software-requirements"></a>Requisiti software

I requisiti software per includono le informazioni sul web browser supportati, le versioni di piccole e medie imprese, piattaforme di virtualizzazione e i requisiti minimi dispositivo virtuale.

### <a name="supported-virtualization-platforms"></a>Piattaforme di virtualizzazione supportati


| **Hypervisor** | **Versione**                          |
|----------------|--------------------------------------|
| Hyper-V        | Windows Server 2008 R2 SP1 e versioni successive |
| VMware ESXi    | 5,5 e versioni successive                        |

### <a name="virtual-device-requirements"></a>Requisiti di dispositivo virtuale

| **Componente**                                | **Requisito**            |
|----------------------------------------------|----------------------------|
| Minimo numero di processori virtuali (Core) | 4                          |
| Memoria minima (RAM)                         | 8 GB                       |
| Spazio su disco<sup>1</sup>                       | Disco OS - 80 GB <br></br>Disco dati - 500 GB a 8 TB|
| Numero minimo di interfacce di rete       | 1                          |
| Valore minimo di larghezza di banda Internet<sup>2</sup>       | 5 Mbps                     |

<sup>1</sup> - sottile viene completato il provisioning

<sup>2</sup> - requisiti di rete possono variare a seconda la modifica dei dati giornaliera. Ad esempio, se un dispositivo deve eseguire il backup 10 GB o più modifiche durante la giornata, quindi il backup giornaliero tramite una connessione Mbps 5 potrebbe richiedere fino a 4,25 ore (se i dati potrebbero non essere compresso o deduplicated).

### <a name="supported-web-browsers"></a>Web browser supportati

| **Componente**     | **Versione** | **Requisiti aggiuntive/note** |
|-------------------|-----------------|-----------------------------------|
| Microsoft Edge    | Versione più recente  |                                   |
| Internet Explorer | Versione più recente  | Testati con Internet Explorer 11  |
| Google Chrome     | Versione più recente  | Testati con Chrome 46             |

### <a name="supported-storage-clients"></a>Client supportati lo spazio di archiviazione 

I seguenti requisiti software disponibili per gli iniziatori iSCSI per accedere la matrice virtuale StorSimple (configurato come server iSCSI).

| **Sistemi operativi supportati** | **Versione richiesta** | **Requisiti aggiuntive/le note** |
| --------------------------- | ---------------- | ------------- |
| Windows Server              | 2008 R2 SP1, 2012, 2012R2 |StorSimple possono creare eseguono provisioning e completamente provisioning volumi. Non sarà possibile creare volumi parzialmente provisioning. StorSimple iSCSI volumi sono supportati solo per: <ul><li>Volumi semplici su dischi di base di Windows.</li><li>NTFS di Windows per la formattazione di un volume.</li>|


I seguenti requisiti software disponibili per i client di piccole e medie imprese che accedono la matrice virtuale StorSimple (configurato come un file server).

| **Versione di piccole e medie imprese** |
|-------------|
| Piccole e medie imprese 2. x     |
| PICCOLE E MEDIE IMPRESE 3.0     |
| 3.02 PICCOLE E MEDIE IMPRESE    |

> [AZURE.IMPORTANT] Non copiare o archiviare i file protetti da Windows File System (crittografia) al server di file StorSimple matrice virtuale. verrà creato in una configurazione non supportata. 

## <a name="networking-requirements"></a>Requisiti di rete 

Nella tabella seguente elenca le porte che devono essere aperte nel firewall per consentire di iSCSI, piccole e medie imprese, cloud o il traffico di gestione. In questa tabella, *in* *entrata* o in si intende la direzione in cui le richieste in arrivo accedere il dispositivo. *Indietro* o *in uscita* si intende la direzione in cui il dispositivo StorSimple invia dati esternamente, oltre alla distribuzione: ad esempio, in uscita a Internet.

| **Porta n.<sup>1</sup>** | **Avanti o indietro** | **Ambito porta** | **Obbligatorio**              | **Note**                                                                                                            |
|--------------------------|---------------|----------------|---------------------------|----------------------------------------------------------------------------------------------------------------------|
| TCP 80 (HTTP)            | In uscita           | WAN            | No                        | Porta in uscita viene usata per l'accesso a Internet per recuperare gli aggiornamenti. <br></br>Il proxy web in uscita è configurabile dall'utente. |
| TCP 443 (HTTPS)          | In uscita           | WAN            | Sì                       | Porta in uscita usata per accedere ai dati nel cloud. <br></br>Il proxy web in uscita è configurabile dall'utente. |
| UDP 53 (DNS)             | In uscita           | WAN            | In alcuni casi; vedere le note. | È necessario solo se si usa un server DNS basato su Internet. <br></br> **Nota**: se si distribuisce un file server, è consigliabile usare server DNS locale.|
| UDP 123 (NTP)            | In uscita           | WAN            | In alcuni casi; vedere le note. | È necessario solo se si usa un server NTP basati su Internet.<br></br> **Nota:** Se si distribuisce un file server, è consigliabile sincronizzare l'ora con controller di dominio Active Directory.  |
| TCP 80 (HTTP)           | In            | LAN            | Sì                       | Questa è la porta in ingresso per l'interfaccia utente locale nel dispositivo, StorSimple per gestione locale. <br></br> **Nota**: accesso dell'interfaccia utente locale su HTTP verrà automaticamente reindirizzati a HTTPS.|
| TCP 443 (HTTPS)          | In            | LAN            | Sì                       | Questa è la porta in ingresso per l'interfaccia utente locale nel dispositivo, StorSimple per gestione locale.|
| TCP 3260 (iSCSI)         | In            | LAN            | No                        | Questa porta viene utilizzata per accedere ai dati su iSCSI.|

<sup>1</sup> non porte in ingresso devono essere aperte su Internet.

> [AZURE.IMPORTANT] Verificare che il firewall non modificare o decrittografare il traffico SSL tra il dispositivo di StorSimple e Azure.


### <a name="url-patterns-for-firewall-rules"></a>Modelli di URL per le regole firewall 

Gli amministratori di rete spesso possono configurare le regole firewall avanzato sulla base di modelli di URL per filtrare l'in ingresso e il traffico in uscita. La matrice virtuale e il servizio di gestione StorSimple dipendono dalle altre applicazioni di Microsoft, ad esempio Bus di servizio Azure, controllo di accesso di Azure Active Directory, gli account di archiviazione e server Microsoft Update. I modelli di URL associati a queste applicazioni possono essere utilizzati per configurare le regole del firewall. È importante tenere presente che non è possano modificare i modelli di URL associati a queste applicazioni. Sarà necessario a sua volta l'amministratore di rete monitorare e aggiornare le regole del firewall per il StorSimple come e, se necessario. 

È consigliabile impostare le regole del firewall per il traffico in uscita, in base a StorSimple fissato liberamente indirizzi IP, nella maggior parte dei casi. Tuttavia, è possibile utilizzare le informazioni seguenti per impostare le regole firewall avanzato necessari per creare ambienti protetti.

> [AZURE.NOTE] 
> 
> - Il dispositivo (origine) IP impostare sempre a tutte le interfacce di rete basate su cloud. 
> - La destinazione che IP deve essere impostato su [intervalli di indirizzi IP di Azure Data Center](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


| Modello di URL                                                      | Componente/funzionalità                                           |
|------------------------------------------------------------------|---------------------------------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`   | Servizio di gestione StorSimple<br>Servizio controllo di accesso<br>Bus di servizio Azure|
|`http://*.backup.windowsazure.com`|Registrazione periferica|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Revoca certificato |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Gli account di archiviazione Azure e il monitoraggio |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Server di Microsoft Update<br>                        |
| `http://*.deploy.akamaitechnologies.com`                         |Rete CDN Akamai |
| `https://*.partners.extranet.microsoft.com/*`                    | Pacchetto di supporto                                                  |
| `http://*.data.microsoft.com `                   | Servizio di telemetria in Windows, vedere [aggiornamento dell'esperienza dei clienti e telemetria diagnostica](https://support.microsoft.com/en-us/kb/3068708)                                                  |

## <a name="next-step"></a>Passaggio successivo

-   [Preparare il portale per distribuire la matrice virtuale StorSimple](storsimple-ova-deploy1-portal-prep.md)
