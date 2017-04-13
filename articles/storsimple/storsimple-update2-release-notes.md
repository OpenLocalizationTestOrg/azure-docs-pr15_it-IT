<properties 
   pageTitle="Note sulla versione StorSimple 8000 serie aggiornamento 2 | Microsoft Azure"
   description="Descrive le nuove funzionalità, problemi e soluzioni alternative per StorSimple 8000 serie aggiornamento 2."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-update-2-release-notes"></a>Note sulla versione StorSimple 8000 serie aggiornamento 2  

## <a name="overview"></a>Panoramica

Note sulla versione seguenti descrivono le nuove caratteristiche e identificare i problemi critici aperti per StorSimple 8000 serie aggiornamento 2. Contengono anche un elenco del software StorSimple, driver e aggiornamenti firmware disco inclusi in questa versione. 

Aggiornamento 2 può essere applicato a qualsiasi dispositivo StorSimple eseguito l'aggiornamento 1.2 di rilascio (GA) o aggiornamento 0,1. La versione per dispositivi associata aggiornamento 2 è 6.3.9600.17673.

Esaminare le informazioni contenute nelle note sulla versione prima di distribuire l'aggiornamento della soluzione StorSimple.

>[AZURE.IMPORTANT]
> 
- Bastano circa 7 4 ore per installare questo aggiornamento (inclusi gli aggiornamenti di Windows). 
- Software, driver LSI e aggiornamenti firmware SSD aggiornamento 2.
- Per le nuove versioni, non sarà possibile visualizzare gli aggiornamenti immediatamente perché facciamo graduale degli aggiornamenti. Attendere alcuni giorni e quindi cercare gli aggiornamenti anche questi descritte diventeranno disponibili breve.


## <a name="whats-new-in-update-2"></a>Quali sono le novità nell'aggiornamento 2

Aggiornamento 2 introduce funzionalità seguenti.

- **In locale aggiunte volumi** : nelle versioni precedenti della serie 8000 StorSimple, blocchi di dati sono stati a più livelli nel cloud in base all'utilizzo. Si è non verificato in alcun modo per garantire che blocchi da mantenere locale. Nell'aggiornamento 2, quando si crea un volume degli scambi, è possibile designare un volume come dati inclusi in locale e primari da tale volume non essere livelli nel cloud. Snapshot di volumi localmente bloccati verrà copiato ancora nel cloud per il backup in modo che il cloud può essere usato per scopi di ripristino di emergenza e mobilità dei dati. È inoltre possibile modificare il tipo di volume (vale a dire Converti livelli volumi in volumi bloccati in locale e a più livelli volumi Converti localmente aggiunte a). 

- **Miglioramenti della periferica virtuale StorSimple** – in precedenza, la serie 8000 StorSimple posizionato il dispositivo virtuale come una soluzione di ripristino o sviluppo/test di emergenza. Si è verificato un solo modello di dispositivo virtuale (modello 1100). Aggiornamento 2 include due modelli del dispositivo virtuale. 

     - 8010 (noto in precedenza 1100) – Nessun cambiamento; viene utilizzata l'archiviazione standard Azure ha una capacità di 30 TB.
     - 8020 – ha una capacità di 64 TB e viene utilizzata l'archiviazione di Azure Premium per migliorare le prestazioni.

    Esiste un singolo disco rigido virtuale per entrambi i modelli periferica virtuale (8010/8020). Al primo avvio di dispositivo virtuale rileva i parametri di piattaforma e si applica la versione del modello corretto.

- **Miglioramenti della rete** – aggiornamento 2 contiene i seguenti miglioramenti di reti:

     - Più schede di rete può essere abilitate per nel cloud, in modo che il failover può verificarsi se si verifica un errore di una scheda di rete.
     - Miglioramenti di routing, con metriche fissi per il cloud abilitato blocchi.
     - In linea tentativi di risorse non riuscite prima caso di errore.
     - Nuovi avvisi per errori del servizio.

- **Miglioramenti apportati all'aggiornamento** -aggiornamento 1.2 e versioni precedente, la serie 8000 StorSimple è stato aggiornato tramite due canali: Windows Update per cluster, iSCSI e così via e Microsoft Update per i file binari e firmware.
    Aggiornamento 2 utilizza Microsoft Update per tutti i pacchetti di aggiornamento. Questo dovrebbe portare a tempi patch o eseguendo failover. 

- **Aggiornamenti del firmware** : seguenti sono inclusi gli aggiornamenti del firmware:
    - LSI: lsi_sas2.sys versione del prodotto 2.00.72.10
    - Solo SSD (non aggiornamenti del disco rigido): XMGG, XGEG, KZ50, F6C2 e VR08

- **Supporto preventivo** -2 di aggiornamento consente a Microsoft in modo da estrarre informazioni diagnostiche aggiuntive dal dispositivo. Quando il nostro team operazioni identifica dispositivi che si verificano problemi, siamo migliore per raccogliere informazioni dal dispositivo e diagnosticare i problemi. **Accettando aggiornamento 2, ci Consenti fornire questo supporto preventivo**.    
 

## <a name="issues-fixed-in-update-2"></a>Problemi risolti nell'aggiornamento 2

Le tabelle seguenti viene fornito un riepilogo dei problemi corretti in aggiornamenti 2.    

| No. | Caratteristica | Problema | Si applica a dispositivo fisico | Si applica a dispositivo virtuale |
|-----|---------|-------|--------------------------------|--------------------------------|
| 1 | Interfacce di rete | Dopo l'aggiornamento a aggiornamento 1, il servizio di gestione StorSimple segnalato che non è riuscito porte Data2 e Data3 in un controller. Questo problema è stato risolto. | Sì | No |
| 2 | Aggiornamenti | Dopo l'aggiornamento a aggiornamento 1, gli avvisi acustici avviso durante il portale classico Azure in più dispositivi. Questo problema è stato risolto. | Sì | No |
| 3 | Autenticazione Openstack | Quando si utilizza Openstack come provider di servizi cloud, può anche venire visualizzato un messaggio di errore che la stringa di autenticazione cloud è troppo lunga. Questo problema è stato risolto. | Sì | No |


## <a name="known-issues-in-update-2"></a>Problemi noti di aggiornamento 2

La tabella seguente contiene un riepilogo dei problemi noti di questa versione.

| No. | Caratteristica | Problema | Commenti / soluzione | Si applica a dispositivo fisico | Si applica a dispositivo virtuale |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Base disco | In rari, se la maggior parte dei dischi in simbolo EBOD di un dispositivo 8600 disconnessi risultante in base Nessun disco, quindi pool di archiviazione andrà offline. Rimarrà non in linea anche se i dischi riconnessione. | È necessario riavviare il dispositivo. Se il problema persiste, contattare il supporto Microsoft per la procedura successiva. | Sì | No |
| 2 | ID controller non corretti | Quando viene eseguita la sostituzione controller, controller 0 vengano visualizzate come controller di 1. Durante la sostituzione dei controller, quando l'immagine viene caricata dal nodo peer ID controller può essere visualizzata inizialmente come ID. del controller peer In alcuni casi il questo comportamento può essere visualizzato anche dopo il riavvio del sistema. | Non è necessario eseguire alcuna azione. Questa situazione risolverà stesso una volta completata la sostituzione controller. | Sì | No |
| 3 | Account di archiviazione | Utilizzo del servizio di archiviazione per eliminare l'account di archiviazione è uno scenario non supportato. Questa operazione comporta a situazioni in cui non è possibile recuperare dati utente.|  | Sì | Sì |
| 4 | Dispositivo failover | Failover più di un contenitore di volume dalla stessa periferica di origine per diversi dispositivi non è supportato. Il controllo da un unico dispositivo inattivo a più dispositivi consentono di rendere i contenitori volume il primo non è riuscita sul dispositivo perde la proprietà dei dati. Dopo un failover, questi contenitori volume verranno visualizzata o hanno un funzionamento diverso quando vengono visualizzati nel portale di classica Azure. | | Sì | No |
| 5 | Installazione | Durante la scheda StorSimple per l'installazione di SharePoint, è necessario specificare un indirizzo IP dispositivo affinché installa per completare correttamente.    | | Sì | No |
| 6 | Proxy Web | Se la configurazione del proxy web include HTTPS come protocollo specificato, la comunicazione al servizio di dispositivi interessata e passa offline il dispositivo. Verranno generati anche pacchetti di supporto del processo di risorse significativa nel dispositivo. | Assicurarsi che l'URL del proxy web disponga HTTP come protocollo specificato. Per ulteriori informazioni, passare al [proxy web di configurazione del dispositivo](storsimple-configure-web-proxy.md). | Sì | No |
| 7 | Proxy Web | Se si configura e Abilita proxy web in un dispositivo registrato, sarà necessario riavviare controller attivo nel dispositivo. | | Sì | No |
| 8 | Latenza elevata cloud e carico di lavoro elevato i/o | Quando il dispositivo StorSimple rileva una combinazione di latenza elevata cloud (ordine di secondi) e il carico di lavoro i/o elevato, i volumi dispositivo passare a una riduzione e i / o potrebbe non riuscire con un errore di "dispositivo non pronto". | È necessario riavviare il controller di dispositivo o eseguire il controllo un dispositivo a risolvere questa situazione manualmente. | Sì | No |
| 9 | PowerShell Azure | Quando si utilizza il cmdlet StorSimple **Get-AzureStorSimpleStorageAccountCredential & #124; Selezionare oggetto - attesa prima di tutto 1 -** per selezionare il primo oggetto in modo che è possibile creare un nuovo oggetto **VolumeContainer** , il cmdlet restituisce tutti gli oggetti. | Disporre il cmdlet tra parentesi nel modo seguente: **(Get-Azure-StorSimpleStorageAccountCredential) & #124; Selezionare oggetto - 1 prima di tutto - attesa** | Sì | Sì |
| 10| Migrazione | Quando vengono passati più contenitori volume per la migrazione, greco per il backup più recente è accurata solo per il primo contenitore volume. Inoltre, la migrazione parallela inizierà dopo la migrazione delle prime 4 backup nel primo contenitore di volume. | È consigliabile migrare contenitore volume uno alla volta. | Sì | No |
| 11| Migrazione | Dopo il ripristino volumi non vengono aggiunti al criteri di backup o al gruppo disco virtuale. | È necessario aggiungere questi volumi a un criterio di backup per la creazione di backup. | Sì | Sì |
| 12| Migrazione | Una volta completata la migrazione, il dispositivo serie 5000/7000 non deve accedere i contenitori di dati migrati. | È consigliabile eliminare i contenitori di dati migrati dopo la migrazione completa e approvata. | Sì | No |
| 13| Clona e DR | Un dispositivo StorSimple esegue l'aggiornamento di 1 non è possibile duplicare o eseguire il ripristino di emergenza a un dispositivo che esegue pre-aggiornamento 1. | È necessario aggiornare il dispositivo di destinazione per aggiornare 1 per consentire queste operazioni | Sì | Sì |
| 14 | Migrazione | Backup della configurazione per la migrazione potrebbe non riuscire in un dispositivo di serie 5000 7000 quando sono presenti gruppi volume senza volumi associato. | Eliminare tutti i gruppi di volume vuota senza volumi associati e quindi riprovare a eseguire il backup di configurazione.| Sì | No |
| 15 | Cmdlet di PowerShell Azure e volumi localmente bloccati | Non è possibile creare un volume locale bloccato tramite i cmdlet di PowerShell Azure. (Verrà livelli qualsiasi volume creato tramite PowerShell Azure) |Utilizzare sempre il servizio di gestione di StorSimple per configurare volumi localmente bloccati.| Sì | No |
| 16 |Spazio disponibile per i volumi localmente bloccati | Se si elimina un volume locale bloccato, lo spazio disponibile per i nuovi volumi potrebbe non essere aggiornato immediatamente. Il servizio di gestione StorSimple Aggiorna spazio locale disponibile circa ogni ora.| Attendere un'ora prima che si tenta di creare il nuovo volume. | Sì | No |
| 17 | Volumi localmente bloccati | Il processo di ripristino espone il backup temporaneo snapshot nel catalogo di Backup, ma solo per la durata del processo di ripristino. Inoltre, esposto un gruppo di disco virtuale con prefisso **tmpCollection** nella pagina **Criteri di Backup** , ma solo per la durata del processo di ripristino. | Questo problema può verificarsi se il processo di ripristino solo in locale sia bloccato volumi o una combinazione di volumi localmente bloccati e a più livelli. Se il processo di ripristino include solo i volumi a più livelli, quindi il problema non si verificherà. Non è necessario alcun intervento dell'utente. | Sì | No |
| 18 | Volumi localmente bloccati | Se si annulla un processo di ripristino e controller caso di errore si verifica immediatamente in un secondo momento, il processo di ripristino mostrerà **Failed** anziché **annullato**. Se un processo di ripristino non riesce e controller caso di errore si verifica subito in un secondo momento, il processo di ripristino mostrerà **annullato** invece **non è riuscita**. | Questo problema può verificarsi se il processo di ripristino solo in locale sia bloccato volumi o una combinazione di volumi localmente bloccati e a più livelli. Se il processo di ripristino include solo i volumi a più livelli, quindi il problema non si verificherà. Non è necessario alcun intervento dell'utente. | Sì | No |
| 19 |Volumi localmente bloccati | Se si annulla un processo di ripristino o se il ripristino non riesce e si verificherà caso di errore controller, un processo di ripristino aggiuntive viene visualizzato nella pagina **processi** . | Questo problema può verificarsi se il processo di ripristino solo in locale sia bloccato volumi o una combinazione di volumi localmente bloccati e a più livelli. Se il processo di ripristino include solo i volumi a più livelli, quindi il problema non si verificherà. Non è necessario alcun intervento dell'utente. | Sì | No |
| 20 |Volumi localmente bloccati | Se si tenta di convertire un volume a più livelli (creato e duplicato con aggiornamento 1.2 o versioni precedente) in un volume locale bloccato e il dispositivo è spazio quasi esaurito o c'è un servizio cloud, la clone(s) possono essere danneggiati.| Questo problema si verifica solo con volumi che sono state create e sono duplicato con pre-aggiornamento 2 software. Deve trattarsi di uno scenario poco frequente.|
| 21 | Conversione del volume | Non vengono aggiornate ACRs associati a un volume durante l'esecuzione di una conversione del volume (a più livelli a localmente aggiunte o viceversa). Aggiornamento di ACRs potrebbe causare il danneggiamento dei dati. | Se necessario, aggiornare ACRs prima della conversione volume e non apportare ulteriori aggiornamenti ACR durante la conversione. |

## <a name="controller-and-firmware-updates-in-update-2"></a>Aggiornamenti di controller e firmware nell'aggiornamento 2

Questa versione aggiorna il driver firmware e del disco nel dispositivo.
 
- Per ulteriori informazioni su firmware LSI aggiornamento, vedere l'articolo della Microsoft Knowledge base 3121900. 
- Per ulteriori informazioni su firmware disco aggiornamento, vedere l'articolo della Microsoft Knowledge base 3121899.
 
## <a name="virtual-device-updates-in-update-2"></a>Aggiornamenti di dispositivo virtuale nell'aggiornamento 2

Questo aggiornamento non possa essere applicato al dispositivo virtuale. Saranno necessario creare nuovi dispositivi virtuali. 

## <a name="next-step"></a>Passaggio successivo

Informazioni su come [installare l'aggiornamento 2](storsimple-install-update-2.md) in un dispositivo StorSimple.
