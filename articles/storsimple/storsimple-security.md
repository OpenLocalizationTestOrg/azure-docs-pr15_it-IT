<properties 
   pageTitle="Protezione StorSimple | Microsoft Azure" 
   description="Descrive le caratteristiche di sicurezza e privacy proteggere il servizio StorSimple, dispositivi e i dati in locale e nel cloud." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="05/03/2016"
   ms.author="v-sharos"/>

# <a name="storsimple-security-and-data-protection"></a>StorSimple sicurezza e protezione dei dati

## <a name="overview"></a>Panoramica

Protezione costituisce un problema principale per chi è l'adozione di una nuova tecnologia, soprattutto quando la tecnologia viene utilizzata con dati riservati o proprietari. Durante la valutazione tecnologie diverse, è necessario prendere in considerazione aumento dei rischi e i costi per la protezione dei dati. Microsoft Azure StorSimple fornisce sia una soluzione di privacy e sicurezza per la protezione dei dati, che assicura: 

- **Riservatezza** -solo entità autorizzate possono visualizzare i dati. 
- **Integrità** -solo autorizzati entità può modificare o eliminare i dati.

La soluzione Microsoft Azure StorSimple è costituito da quattro componenti principali che interagiscono tra loro:

- **Servizio di gestione StorSimple incluso in Microsoft Azure** : il servizio di gestione che consente di configurare e il provisioning StorSimple del dispositivo.
- **Dispositivo StorSimple** – un dispositivo fisico installato nel centro dati. Tutti gli host e i client che generano dati connettersi al dispositivo StorSimple e il dispositivo gestisce i dati che viene spostato nel cloud Azure in base alle esigenze.
- **Client/host connessi al dispositivo** : I client dell'infrastruttura di connettersi al dispositivo StorSimple e generare dati che devono essere protetti.
- **Spazio di archiviazione cloud** – la posizione nel cloud Azure memorizzazione dei dati.

Nelle sezioni seguenti vengono descrivono le funzionalità di sicurezza StorSimple che consentono di proteggere ognuno dei dipendenti questi componenti e i dati archiviati su di essi. Include inoltre un elenco di domande a cui è possibile avere sulla protezione di Microsoft Azure StorSimple e le risposte corrispondenti.

## <a name="storsimple-manager-service-protection"></a>Protezione di servizio StorSimple Manager

Il servizio di gestione StorSimple è un servizio di gestione ospitato in Microsoft Azure e usate per gestire tutti i dispositivi di StorSimple che l'organizzazione ha acquistati. È possibile accedere al servizio Gestione StorSimple usando le credenziali dell'organizzazione per accedere al portale di classica Azure tramite un web browser. 

Accesso al servizio di gestione di StorSimple è necessario che l'organizzazione ha un abbonamento Azure che include StorSimple. L'abbonamento determina le caratteristiche che è possibile accedere al portale classico Azure. Se l'organizzazione non ha un abbonamento a Azure e si desidera ottenere ulteriori informazioni, vedere [effettuare l'iscrizione per Azure come un'organizzazione](../active-directory/sign-up-organization.md). 

Poiché il servizio di gestione di StorSimple è ospitato in Azure, è protetto dalle funzionalità di sicurezza di Azure. Per ulteriori informazioni sulle funzionalità di protezione fornito da Microsoft Azure, visitare il [Centro protezione di Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Protezione dispositivo StorSimple

Il dispositivo StorSimple è un dispositivo di archiviazione ibrido locale contenente (SSD) e dischi rigidi (dischi rigidi), insieme alla controller ridondanti e funzionalità di failover automatico. Controller di gestiscono l'archiviazione livelli, immissione i dati usati (o attivo) attualmente sull'archiviazione locale (in StorSimple dispositivo o locale server), mentre si sposta meno dati utilizzati di frequente nel cloud.

Solo autorizzati StorSimple dispositivi sono consentiti per l'iscrizione al servizio di gestione StorSimple creata nell'abbonamento Azure. Per autorizzare un dispositivo, è necessario registrare essa con il servizio di gestione StorSimple fornendo la chiave di registrazione del servizio. La chiave di registrazione del servizio è una chiave casuale a 128 bit generata nel portale di classica Azure. 

![Chiave di registrazione del servizio](./media/storsimple-security/ServiceRegistrationKey.png)

Per informazioni su come ottenere una chiave di registrazione del servizio, passa a [passaggio 2: ottenere il codice di registrazione servizio](storsimple-deployment-walkthrough.md#step-2-get-the-service-registration-key).

La chiave di registrazione del servizio è una chiave long contenente 100 caratteri. È possibile copiare la chiave e salvarlo in un file di testo in un percorso sicuro in modo che è possibile utilizzare per autorizzare altri dispositivi in base alle esigenze. Se la chiave di registrazione del servizio viene persa dopo aver registrato il primo dispositivo, è possibile generare una nuova chiave dal servizio di gestione StorSimple. Questa operazione non modificherà il funzionamento di dispositivi esistenti. 

Dopo aver registrato un dispositivo, vengono utilizzati token di comunicare con Microsoft Azure. La chiave di registrazione del servizio non viene utilizzata dopo la registrazione di dispositivo.

> [AZURE.NOTE] È consigliabile rigenerare la chiave di registrazione del servizio dopo ogni utilizzo.

## <a name="protect-your-storsimple-solution-via-passwords"></a>Proteggere la soluzione StorSimple tramite password

Le password sono un aspetto importante di protezione del computer e vengono utilizzate spesso nella soluzione StorSimple per garantire che i dati siano accessibili per gli utenti autorizzati. StorSimple consente di configurare le password seguenti:

- Password di amministratore di StorSimple dispositivo
- Destinate a utenti le password iniziatore e destinazione di autenticazione CHAP (Handshake Protocol)
- StorSimple Snapshot Gestione password

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell per StorSimple e la password di amministratore del dispositivo StorSimple

Windows PowerShell per StorSimple è un'interfaccia della riga di comando che è possibile utilizzare la gestione del dispositivo StorSimple. Windows PowerShell per StorSimple include caratteristiche che consentono di eseguire la registrazione del dispositivo, configurare l'interfaccia di rete nel dispositivo, installare determinati tipi di aggiornamenti, risolvere i problemi del dispositivo, accedere alla sessione di supporto e modificare lo stato del dispositivo. È possibile accedere Windows PowerShell per StorSimple connettendosi a console seriale del dispositivo oppure usando Windows PowerShell remota.

Gestione remota di PowerShell può essere eseguita su HTTPS o HTTP. Se è abilitata la gestione remota su HTTPS, sarà necessario scaricare il certificato di gestione remota dal dispositivo e installarlo nel client remoto. Per ulteriori informazioni sulla gestione remota di PowerShell, passare alla [connessione remota al dispositivo StorSimple](storsimple-remote-connect.md).

Dopo avere utilizzato Windows PowerShell per StorSimple per connettere il dispositivo, sarà necessario fornire la password di amministratore di dispositivo per accedere al dispositivo.

![Password di amministratore di dispositivo](./media/storsimple-security/DeviceAdminPW.png)

Prendere in considerazione le procedure consigliate seguenti:

- Gestione remota è disattivata per impostazione predefinita. È possibile utilizzare il servizio di gestione di StorSimple per attivarlo. Per garantire la migliore protezione, accesso remoto deve essere attivato solo durante il periodo di tempo che in realtà è necessario.
- Se si cambia la password, sarà necessario informare tutti gli utenti di accesso remoto in modo che non si verifica una perdita di connettività imprevisto.
- Il servizio di gestione di StorSimple non è possibile recuperare le password esistenti: è possibile reimpostare solo loro. È consigliabile memorizzare tutte le password in un luogo protetto in modo che non si dispone reimpostare una password se si dimentica. Se è necessario reimpostare una password, sarà necessario informare tutti gli utenti prima di ripristinare. 

L'interfaccia di Windows PowerShell è possibile accedere tramite una connessione seriale al dispositivo. È anche possibile accedervi in remoto utilizzando HTTP o HTTPS, che forniscono rafforzare la sicurezza. HTTPS fornisce un livello di sicurezza superiore rispetto alla sequenza o connessione HTTP. Tuttavia, per utilizzare HTTPS, è necessario installare prima di tutto un certificato del computer client che avranno accesso il dispositivo. È possibile scaricare il certificato di accesso remoto dalla pagina di configurazione dispositivo nel servizio di gestione StorSimple. Se il certificato per l'accesso remoto viene perso, è necessario scaricare un nuovo certificato e propagate a tutti i client che sono autorizzati a utilizzare Gestione remota.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Destinate a utenti le password iniziatore e destinazione di autenticazione CHAP (Handshake Protocol)

CHAP è uno schema di autenticazione utilizzato dal dispositivo StorSimple per convalidare l'identità del client remoti. La verifica si basa su una password condivisa. CHAP può essere unidirezionale (unidirezionale) o comune (bidirezionale). Con questo protocollo unidirezionale, la destinazione (il dispositivo StorSimple) autentica iniziatore (host). CHAP reciproco o inverso richiede che la destinazione autenticazione dell'iniziatore e quindi l'iniziatore eseguire l'autenticazione di destinazione. Il StorSimple può essere configurati per usare uno dei metodi.

Quando si configura CHAP, tenere presente quanto segue:

- Il nome utente CHAP deve contenere meno di 233 caratteri.
- La password CHAP deve essere compreso tra 12 e 16 caratteri. Tentativo di usare un nome utente più o la password verrà generato un errore di autenticazione dell'host di Windows.
- È possibile utilizzare la stessa password per l'iniziatore CHAP e la destinazione CHAP.
- Dopo aver impostato la password, possono essere modificata ma non può essere recuperato. Se si cambia la password, sarà necessario informare tutti gli utenti di accesso remoto in modo che è possibile connettersi al dispositivo StorSimple.

Per ulteriori informazioni su CHAP e su come configurare per la soluzione StorSimple, passare alla [Configurazione CHAP del dispositivo StorSimple](storsimple-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>StorSimple Snapshot Gestione password

StorSimple Snapshot Manager è uno snap-in Microsoft Management Console (MMC) che utilizza gruppi volume e il servizio di copia di Windows Volume ombreggiatura generi backup coerenti con l'applicazione. Inoltre, è possibile utilizzare StorSimple Snapshot Manager per creare duplicato e pianificazioni di backup o ripristino volumi.

Quando si configura un dispositivo da usare StorSimple Snapshot Manager, verrà richiesto di immettere la password StorSimple Snapshot Manager. Questa password viene impostata prima di tutto in Windows PowerShell per StorSimple durante la registrazione. La password possa anche essere impostata e modificata dal servizio di gestione StorSimple. Questa password autentica la periferica con StorSimple Snapshot Manager.

![StorSimple Snapshot Gestione password](./media/storsimple-security/SnapshotMgrPassword.png)

La password StorSimple Snapshot Manager deve essere 14 e 15 caratteri e deve contenere 3 o più di una combinazione di caratteri maiuscoli, minuscoli, numerici e speciali. Dopo aver impostato la password StorSimple Snapshot Manager, possono essere modificata, ma non possono essere recuperato. Se si cambia la password, assicurarsi di notificare tutti gli utenti remoti.

Per ulteriori informazioni sulla gestione di Snapshot di StorSimple, passare a [che cos'è Gestione Snapshot StorSimple?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Procedure consigliate

È consigliabile utilizzare le seguenti linee guida per garantire che le password StorSimple siano ben protetto e sicuro:

- Cambiare le password ogni tre mesi. Modificare la password viene applicata una volta all'anno.
- Utilizzare password complesse. Per ulteriori informazioni, vedere [creare password complesse](http://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/)e proteggerle.
- Usa sempre password diverse per i meccanismi di accesso diverso; ognuna delle password specificata deve essere univoco.
- Non si condividono le password con tutti gli utenti che non è autorizzato ad accedere al dispositivo StorSimple.
- Non Leggi la propria password in primo piano o suggerimento il formato di una password.
- Se si sospetta che un account o la password è stata compromessa, segnalare il problema del reparto protezione.
- Indicazione tutte le password informazioni riservate e informazioni riservate. 

## <a name="storsimple-data-protection"></a>Protezione dei dati StorSimple

In questa sezione descrive le caratteristiche di protezione StorSimple proteggere i dati in transito e dati memorizzati.

Come descritto in altre sezioni, la password vengono utilizzata per autorizzare e autenticazione degli utenti prima che possono accedere alla soluzione StorSimple. Altre considerazioni sulla protezione protegge dati da utenti non autorizzati mentre il relativo trasferimento tra i sistemi di archiviazione e l'archiviazione. Le sezioni seguenti descrivono le funzionalità di protezione dei dati disponibili con StorSimple.

> [AZURE.NOTE] Deduplication offre una protezione aggiuntiva per i dati archiviati nel dispositivo StorSimple e lo spazio di archiviazione di Microsoft Azure. Quando sono deduplicated dati, gli oggetti dati vengono memorizzati separatamente dai metadati utilizzato per eseguire il mapping e accedervi: nessun contesto di spazio di archiviazione disponibile per ricostruire i dati in base a nome file, sistema di file o della struttura del volume.

## <a name="protect-data-flowing-through-the-service"></a>Proteggere i dati che passano attraverso il servizio

Lo scopo principale del servizio di gestione di StorSimple è per gestire e configurare il dispositivo StorSimple. Il servizio di gestione StorSimple viene eseguito in Microsoft Azure. Si utilizza il portale classico Azure immettere i dati di configurazione di dispositivi e quindi Microsoft Azure viene utilizzato il servizio di gestione di StorSimple per inviare i dati al dispositivo. StorSimple utilizza un sistema di asimmetrici coppie di parole chiave per garantire che una violazione del servizio di Azure non comporta la violazione di informazioni archiviate. 

![Crittografia dei dati di volo](./media/storsimple-security/DataEncryption.png)

Il sistema di chiave asimmetrico consente di proteggere i dati che attraversano il servizio come indicato di seguito:

1. Certificato di crittografia dati che utilizza un asimmetrico pubblico e privato coppia di chiavi viene generato sul dispositivo e viene utilizzata per proteggere i dati. I tasti vengono generati quando è stato registrato il primo dispositivo. 
2. Le chiavi di certificato di crittografia vengono esportate in un file di scambio di informazioni personali (PFX) protetto da chiave di crittografia dati servizio, ovvero una chiave a 128 bit sicura generata in modo casuale dal dispositivo prima durante la registrazione.
3. Chiave pubblica del certificato in modo sicuro è disponibile per il servizio di gestione StorSimple e la chiave privata rimane con il dispositivo.
4. Dati immettere il servizio sono crittografati tramite pubblico chiave e decrittografare utilizzando la chiave privata archiviata nel dispositivo, verificare che il servizio di Azure non è possibile decrittografare i dati che scorre al dispositivo.

La chiave di crittografia di servizio dati viene generata sul primo dispositivo che è stato registrato con il servizio. Tutti i dispositivi successivi sono registrati con il servizio è necessario utilizzare la chiave di crittografia servizio dati. 

> [AZURE.IMPORTANT] 
> 
> È fondamentale per creare una copia della chiave di crittografia dati servizio e salvarlo in un percorso sicuro. Una copia della chiave di crittografia servizio dati deve venire archiviata in modo che è possibile accedervi da una persona autorizzata e che possono essere facilmente comunicato all'amministratore del dispositivo.
>
> Se la chiave di crittografia di servizio dati viene persa, può essere utile un addetto al supporto Microsoft per recuperarlo purché si disponga di almeno un dispositivo in uno stato online. È consigliabile modificare la chiave di crittografia di servizio dati dopo il recupero. Per istruzioni, passare alla sezione [Modifica della chiave di crittografia di servizio dati](storsimple-service-dashboard.md#change-the-service-data-encryption-key).

È possibile modificare la chiave di crittografia di servizio dati e il certificato di crittografia dati corrispondenti selezionando l'opzione **Modifica chiave di crittografia di servizio dati** nel dashboard di servizio. Per assicurarsi che la protezione dei dati non è stata compromessa, è necessario utilizzare un dispositivo StorSimple fisico per modificare la chiave di crittografia di servizio dati. Modificare le chiavi di crittografia richiede l'aggiornamento tutti i dispositivi con la nuova chiave. Di conseguenza, è consigliabile modificare la chiave quando tutti i dispositivi sono online. Se i dispositivi in modalità offline, le chiavi possono essere modificate in un momento diverso. I dispositivi con i tasti obsoleti saranno ancora possibile eseguire i backup, ma non sarà possibile ripristinare i dati fino a quando non viene aggiornato il tasto. Per ulteriori informazioni, passare a [usare il dashboard di servizio StorSimple Manager](storsimple-service-dashboard.md).

La chiave di crittografia di servizio dati e il certificato di crittografia di dati non scada. Tuttavia, è consigliabile modificare la chiave di crittografia servizio dati una volta all'anno per evitare compromesso chiave.

## <a name="protect-data-at-rest"></a>Proteggere i dati inattivi

Il dispositivo StorSimple gestisce i dati da archiviare nei livelli in locale e nel cloud, a seconda di frequenza di utilizzo. Tutti i computer host connessi al dispositivo inviano dati al dispositivo, che consente di spostare quindi i dati nel cloud, in base alle esigenze. Dati vengono trasferiti dal dispositivo nel cloud in modo sicuro tramite Internet. Ogni dispositivo ha una destinazione iSCSI che replica tutti i volumi condivisi sul dispositivo. Tutti i dati vengono crittografati prima di inviarlo al cloud lo spazio di archiviazione. 

![Chiave di crittografia di spazio di archiviazione cloud](./media/storsimple-security/CloudStorageEncryption.png)

Per garantire la sicurezza e l'integrità dei dati spostati nel cloud, StorSimple consente di definire chiavi di crittografia di spazio di archiviazione cloud come indicato di seguito:

- Specificare la chiave di crittografia di spazio di archiviazione cloud quando si crea un contenitore di volume. Il tasto può essere modificato o aggiunte in un secondo momento. 
- Tutti i volumi in un contenitore di volume condividono la stessa chiave di crittografia. Se si vuole un formato diverso di crittografia di un volume specifico, è consigliabile creare un nuovo contenitore volume per ospitare tale volume.
- Quando si immette la chiave di crittografia di spazio di archiviazione cloud nel servizio di gestione StorSimple, la chiave è crittografata utilizzando la parte pubblica della chiave di crittografia servizio dati e quindi inviato al dispositivo.
- La chiave di crittografia di spazio di archiviazione cloud non archiviata in un punto qualsiasi nel servizio ed è noto solo al dispositivo.
- Una chiave di crittografia di spazio di archiviazione cloud è facoltativo. È possibile inviare dati crittografati presso l'host al dispositivo.

### <a name="additional-security-best-practices"></a>Procedure consigliate rafforzare la sicurezza

- Suddividere il traffico: isolare il SAN iSCSI dal traffico utente nella rete aziendale, distribuzione di una rete completamente separata e usando VLAN in cui isolamento fisico non è un'opzione. Una rete dedicata per l'archiviazione iSCSI verrà garantire la sicurezza e le prestazioni dei dati aziendali più importanti. Combinazione il traffico di archiviazione e utente LAN aziendale non è consigliabile e può incrementare la latenza e causare errori di rete.

- Per la sicurezza di rete sul lato host, utilizzare le interfacce di rete che supporta il protocollo TCP/IP trasferire Engine (TOE). VALUTAZIONE riduce carico CPU mediante l'elaborazione TCP sulla scheda di rete.

## <a name="protect-data-via-storage-accounts"></a>Proteggere i dati tramite gli account di archiviazione

Le sottoscrizioni di Microsoft Azure è possono creare uno o più account di archiviazione. (Un account di archiviazione include uno spazio dei nomi univoco per l'uso con i dati archiviati nel cloud Azure) Accesso a un account di archiviazione dipende dai tasti di abbonamento e accesso associati a quell'account lo spazio di archiviazione. 

Quando si crea un account di archiviazione, Microsoft Azure genera due tasti 512 bit lo spazio di archiviazione, uno dei quali viene utilizzato per l'autenticazione quando il dispositivo StorSimple accede account di archiviazione. Si noti che solo uno di questi tasti in uso. Altra chiave viene mantenuta riserva, che consente di ruotare le chiavi periodicamente. Per ruotare tasti, attivare la chiave secondaria e quindi eliminare la chiave primaria. È possibile creare una nuova chiave da usare durante la rotazione successiva. (Per motivi di sicurezza, Data Center molti richiedono rotazione delle chiavi). 

È consigliabile eseguire queste procedure consigliate per la rotazione chiave:

- È necessario ruotare chiavi per l'account archiviazione regolarmente per assicurare che l'account di archiviazione non è accessibile a utenti non autorizzati.
- Periodicamente, l'amministratore di Azure cambiare o rigenerare la chiave primaria o secondaria utilizzando la sezione dello spazio di archiviazione del portale di classica Azure per accedere direttamente all'account di archiviazione.


## <a name="protect-data-via-encryption"></a>Proteggere i dati tramite la crittografia

StorSimple utilizza i seguenti algoritmi di crittografia per proteggere i dati archiviati in o in viaggio tra i componenti della soluzione StorSimple.

| Algoritmo | Lunghezza della chiave | Protocolli/applicazioni/commenti |
| --------- | ---------- | ------------------------------- |
| RSA       | 2048       | Versione 1.5 RSA PKCS 1 viene utilizzato dal portale dei classico Azure per crittografare i dati di configurazione che viene inviati al dispositivo: ad esempio, le credenziali del, configurazione di dispositivi StorSimple, account e cloud chiavi di crittografia di spazio di archiviazione, lo spazio di archiviazione. |
| AES       | 256        | AES con CBC viene usato per crittografare la parte pubblica della chiave di crittografia servizio dati prima di inviarlo al portale di classica Azure dal dispositivo StorSimple. È anche utilizzato dal dispositivo StorSimple per crittografare i dati prima che i dati vengono inviati all'account di archiviazione cloud. |


## <a name="storsimple-virtual-device-security"></a>Sicurezza dei dispositivi virtuale StorSimple

[AZURE.INCLUDE [storsimple virtual device security](../../includes/storsimple-virtual-device-security.md)]

## <a name="frequently-asked-questions-faq"></a>Domande frequenti domande frequenti

Ecco alcune domande e risposte su sicurezza e Microsoft Azure StorSimple.

**Q:** Il servizio è compromessa. Che cosa devono essere i passaggi successivi?

**A:** Modificare immediatamente la chiave di crittografia di servizio dati e i tasti di account di archiviazione per l'account di archiviazione utilizzato per i dati. Per ulteriori informazioni, vedere: 

- [Modificare la chiave di crittografia di servizio dati](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [Rotazione delle chiavi degli account di archiviazione](storsimple-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**Q:** Si dispone di un dispositivo StorSimple che richiede di chiave di registrazione del servizio. Come recuperare il?

**A:** Questo tasto è stato creato al momento della creazione prima di tutto il servizio di gestione StorSimple. Quando si usa il servizio di gestione di StorSimple per connettere il dispositivo, è possibile utilizzare la pagina Guida introduttiva del servizio per visualizzare o rigenerare la chiave di registrazione del servizio. Generare una nuova chiave di registrazione di servizio non viene influenzata dai dispositivi registrati esistenti. Per ulteriori informazioni, vedere:

- [Visualizzare o rigenerare la chiave di registrazione del servizio](storsimple-service-dashboard.md#view-or-regenerate-the-service-registration-key)

**Q:** Ho perso il chiave di crittografia di servizio dati. Cosa si fare?

**A:** Contattare il supporto tecnico Microsoft. È possibile accedere a una sessione di supporto nel dispositivo e assistenza per recuperare la chiave (purché almeno un dispositivo sia online). Immediatamente dopo avere ottenuto la chiave di crittografia dati del servizio, modificare, per assicurarsi che la nuova chiave sia noto solo all'utente. Per ulteriori informazioni, vedere:

- [Modificare la chiave di crittografia di servizio dati](storsimple-service-dashboard.md#change-the-service-data-encryption-key)

**Q:**  Autorizzato un dispositivo per una modifica chiave di crittografia del servizio di dati, ma non è stato avviato il processo di modifica della chiave. Cosa dovrei fare?

**A:** Se l'intervallo di timeout è scaduto, sarà necessario riautorizzare il dispositivo per la modifica chiave di crittografia servizio di dati e avviare il processo di nuovo.

**Q:**  Dopo aver modificato la chiave di crittografia di servizio dati, ma non non sarà possibile aggiornare altri dispositivi entro 4 ore. È necessario ricominciare?

**A:** Il periodo di tempo di 4 ore è solo per avviare la modifica. Dopo aver iniziato il processo di aggiornamento sul dispositivo StorSimple autorizzato, l'autorizzazione è valida fino a quando non vengono aggiornati tutti i dispositivi.

**Q:** L'amministratore StorSimple ha lasciato la società. Cosa dovrei fare?

**A:** Modificare e reimpostare la password che consentono di accedere al dispositivo StorSimple e modificare la chiave di crittografia servizio dati per garantire che le nuove informazioni non è noto a personale non autorizzato. Per ulteriori informazioni, vedere:

- [Utilizzare il servizio di gestione di StorSimple per modificare la password storsimple](storsimple-change-passwords.md)
- [Modificare la chiave di crittografia di servizio dati](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [Configurare CHAP del dispositivo StorSimple](storsimple-configure-chap.md)

**Q:** Desidera fornire la password StorSimple Snapshot Manager su un host che si connette al dispositivo StorSimple, ma la password non è disponibile. Cosa posso fare?

**A:** Se si è dimenticata la password, è necessario crearne uno nuovo. Quindi, assicurarsi di informare tutti gli utenti che la password è stata modificata e che aggiorni i client per utilizzare la nuova password. Per ulteriori informazioni, vedere:

- [Cambiare la password StorSimple Snapshot Manager](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)
- [Eseguire l'autenticazione di un dispositivo](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**Q:** Il certificato per l'accesso remoto a Windows PowerShell per StorSimple è stato modificato nel dispositivo. Come è possibile aggiornare il client di accesso remoto?

**A:** È possibile scaricare il nuovo certificato dal servizio StorSimple Manager e quindi fornire l'installazione nell'archivio certificati del client di accesso remoto. Per ulteriori informazioni, vedere:

- [Cmdlet di importazione certificato](https://technet.microsoft.com/library/hh848630.aspx)

**Q:** È se protetti i dati gestore StorSimple compromesso di servizio?

**A:** Dati di configurazione del servizio sono sempre crittografati con la chiave pubblica quando si visualizza in un web browser. Poiché il servizio non ha accesso alla chiave privata, il servizio non sarà possibile vedere tutti i dati. Se il servizio di gestione di StorSimple è compromessa, non è alcun effetto, non sono presenti chiavi memorizzate nel servizio StorSimple Manager.

**Q:** Se un utente accede al certificato di crittografia dati, saranno dati compromesso?

**A:** Microsoft Azure Archivia chiave di crittografia dati del cliente (file. pfx) in un formato crittografato. Poiché il file. pfx è crittografato e il servizio StorSimple non è disponibile la chiave di crittografia del servizio dati decrittografare il file. pfx, è sufficiente ottenere accesso al file. pfx non esporre le informazioni riservate.

**Q:** Cosa accade se un'entità governativa richiede Microsoft per i dati?

**A:** Poiché il servizio di tutti i dati vengono crittografati e con il dispositivo viene mantenuta la chiave privata, l'entità governativa necessario chiedere al cliente per i dati. 

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il dispositivo StorSimple](storsimple-deployment-walkthrough.md).
 
