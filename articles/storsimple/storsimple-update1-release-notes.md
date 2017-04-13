<properties 
   pageTitle="Note sulla versione 1.2 di aggiornamento serie 8000 StorSimple | Microsoft Azure"
   description="Descrive le nuove funzionalità, problemi e soluzioni alternative per StorSimple 8000 serie aggiornamento 1.2."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-12-release-notes"></a>Note sulla versione StorSimple 8000 serie aggiornamento 1.2  

## <a name="overview"></a>Panoramica

Le note sulla versione seguenti descrivono le nuove caratteristiche e identificare i problemi critici aperti per StorSimple 8000 serie aggiornamento 1.2. Contengono anche un elenco di software StorSimple driver e aggiornamenti firmware disco inclusi in questa versione. 

Aggiornamento 1.2 può essere applicato a qualsiasi dispositivo StorSimple in esecuzione Release (GA), aggiornamento 0,1, aggiornamento 0,2 o aggiornamento 0,3 software. Aggiornamento 1.2 non è disponibile se il dispositivo è in esecuzione 1 aggiorna o Aggiorna 1.1. Se il dispositivo è in esecuzione Release (GA), [contattare il supporto Microsoft](storsimple-contact-microsoft-support.md) per facilitare l'installazione dell'aggiornamento.

La tabella seguente elenca le versioni di software di dispositivo corrispondenti agli aggiornamenti 1, 1.1 e 1.2.

| Se in esecuzione di aggiornamento... | si tratta della versione del software dispositivo. |
|---------------------|---------------------------------------|
| Aggiornamento 1.2          | 6.3.9600.17584                        |
| Aggiornamento 1.1          | 6.3.9600.17521                        |
| Aggiornamento 1.0          | 6.3.9600.17491                        |

Esaminare le informazioni contenute nelle note sulla versione prima di distribuire l'aggiornamento della soluzione StorSimple. Per ulteriori informazioni, vedere come [installare Aggiornamento 1.2 sul dispositivo StorSimple](storsimple-install-update-1.md). 

>[AZURE.IMPORTANT]
> 
- Bastano circa 5 a 10 ore per installare questo aggiornamento (inclusi gli aggiornamenti di Windows). 
- Aggiornamento 1.2 presenta, driver LSI aggiornamenti software e disco firmware. Per installare, seguire le istruzioni di [installazione di aggiornamento 1.2 sul dispositivo StorSimple](storsimple-install-update-1.md).
- Per le nuove versioni, non sarà possibile visualizzare gli aggiornamenti immediatamente perché facciamo graduale degli aggiornamenti. Cercare gli aggiornamenti in pochi giorni nuovamente come questi descritte diventeranno disponibili breve.


## <a name="whats-new-in-update-12"></a>Novità di aggiornamento 1.2

Queste caratteristiche sono state rilasciate prima di tutto con l'aggiornamento 1 reso disponibile per un numero limitato di utenti. Con la versione 1.2 Update, si vedrà la maggior parte degli utenti StorSimple le seguenti nuove caratteristiche e i miglioramenti:

- **Migrazione da serie 5000 7000 8000 nei dispositivi serie** : in questa versione introduce una nuova caratteristica di migrazione che consente di StorSimple 5000 7000 serie accessorio di eseguire la migrazione dei dati in un dispositivo fisico serie 8000 StorSimple o da un dispositivo virtuale. La funzione di migrazione è due proposte di valore chiave:                                                                  

    - **Continuità aziendale**, abilitando la migrazione dei dati esistenti nella serie 8000 accessori serie 5000 7000.
    - **Migliorate funzionalità offerte dei accessori serie 8000**, ad esempio la gestione centralizzata efficiente di più accessori tramite il servizio di gestione StorSimple meglio classe di hardware e aggiornamento del firmware, dispositivi di rete, dei dati e funzionalità di sviluppo futuri.

    Consultare la [Guida alla migrazione](http://www.microsoft.com/download/details.aspx?id=47322) per informazioni dettagliate su come eseguire la migrazione di una serie di 5000 7000 StorSimple in un dispositivo di serie 8000. 

- **Disponibilità nel portale per enti pubblici di Azure** : StorSimple è ora disponibile nel portale di Azure per enti pubblici. Vedere come [distribuire un dispositivo StorSimple nel portale per enti pubblici di Azure](storsimple-deployment-walkthrough-gov.md).

- **Supporto per altri provider di servizi cloud** – altri provider di servizi cloud supportati sono Amazon S3, S3 Amazon con record di risorse, HP e OpenStack (beta).

- **Aggiornamento al più recente API di spazio di archiviazione** : in questa versione, StorSimple è stato aggiornato per le più recenti API del servizio di archiviazione Azure. Dispositivi di serie 8000 StorSimple che eseguono versioni software pre-aggiornamento 1 (versione finale, 0,1, 0,2 e 0,3) utilizza le versioni delle API del servizio di archiviazione Azure antecedenti 17 luglio 2009. Come indicato in aggiornato [annuncio sulla rimozione delle versioni di servizio di archiviazione](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), da 1 agosto 2016, queste API verranno eliminate. È fondamentale applicare StorSimple 8000 serie aggiornamento 1 prima del 1 agosto 2016. Se non si riesce a tale scopo, verranno interrotto StorSimple dispositivi funziona correttamente.

- **Supporto per zona ridondanti dello spazio di archiviazione (ZRS)** – con l'aggiornamento alla versione più recente di API dello spazio di archiviazione, la serie 8000 StorSimple supporterà zona ridondanti dello spazio di archiviazione (ZRS) oltre lo spazio di archiviazione ridondanti in locale (LRS) e lo spazio di archiviazione ridondanti geografico (GRS). Fare riferimento al presente [articolo sulle opzioni di archiviazione Azure ridondanza](../storage/storage-redundancy.md) per informazioni dettagliate ZRS.

- **Enhanced esperienza di distribuzione e aggiornamento iniziale** : In questa versione, l'installazione e i processi di aggiornamento sono state migliorate. L'installazione la configurazione guidata è stato migliorato per fornire commenti e suggerimenti per l'utente se la configurazione della rete e le impostazioni del firewall non sono corrette. Cmdlet di diagnostica aggiuntivi sono stati forniti consentono la risoluzione dei problemi di rete del dispositivo. Vedere [risoluzione dei problemi articolo di distribuzione](storsimple-troubleshoot-deployment.md) per ulteriori informazioni sui nuovi cmdlet di diagnostica per risolvere eventuali problemi.

## <a name="issues-fixed-in-update-12"></a>Problemi risolti nell'aggiornamento 1.2

La tabella seguente contiene un riepilogo dei problemi risolti negli aggiornamenti 1.2, 1.1 e 1.    


| No. | Caratteristica | Problema | Risolti nell'aggiornamento | Si applica a dispositivo fisico | Si applica a dispositivo virtuale |
|-----|---------|-------|-----------------|---------------------------------|--------------------------------|
| 1 | Windows PowerShell per StorSimple | Quando un utente accessibile in remoto il dispositivo StorSimple mediante Windows PowerShell per StorSimple e quindi iniziare la configurazione guidata, un arresto anomalo durante il più presto possibile dati 0 IP è stato inserito. Questo problema a questo punto è corretto nell'aggiornamento 1. | Aggiornamento 1 | Sì | Sì |
| 2 | Reimposta factory | In alcuni casi, quando è stata eseguita la reimpostazione factory, il dispositivo StorSimple è diventato bloccato e visualizzato il messaggio seguente: **Reimposta su factory è in corso (fase 8)**. Questa operazione si è verificato se si preme CTRL + C durante il cmdlet. Questo errore a questo punto è corretto.| Aggiornamento 1 | Sì | No |
| 3 | Reimposta factory | Dopo la reimpostazione una factory controller doppio non riuscito, sono state può procedere con la registrazione di dispositivo. Questo risultato in una configurazione di sistema non supportato. Nell'aggiornamento 1, viene visualizzato un messaggio di errore e la registrazione è bloccata su un dispositivo che è una factory non riuscita reimpostata. | Aggiornamento 1 | Sì | No |
| 4 | Reimposta factory | In alcuni casi, sono stati generati avvisi di mancata corrispondenza positivo false. Non è più gli avvisi di mancata corrispondenza errato verranno generati nei dispositivi che eseguono aggiornamento 1. | Aggiornamento 1 | Sì | No |
| 5 | Reimposta factory | Se il ripristino factory è stata interrotta prima del completamento, il dispositivo in modalità di ripristino e non consente di accedere a Windows PowerShell per StorSimple. Questo errore a questo punto è corretto. | Aggiornamento 1 | Sì | No |
| 6 | Ripristino di emergenza | Un errore di emergenza ripristino () è stato risolto in cui DR avrà esito negativo durante l'individuazione di backup sul dispositivo. | Aggiornamento 1 | Sì | Sì |
| 7 | Monitoraggio LED | In alcuni casi, monitoraggio LED nell'ultima posizione dell'accessorio non ha indicato stato corretto. LED blu è stato disattivato. DATI 1 LED e dati 0 sono state lampeggiante anche quando non sono state configurate queste interfacce. È stato risolto il problema e LED monitoraggio ora indicano lo stato corretto.  | Aggiornamento 1 | Sì | No |
| 8 | Monitoraggio LED | In alcuni casi, dopo l'applicazione di aggiornamento 1 blu chiaro sul controller di active disattivata rendendo difficile distinguere controller attivo. Questo problema è stato risolto in questa versione di patch.| Aggiornamento 1.2 | Sì | No |
| 9 | Interfacce di rete | Nelle versioni precedenti, un dispositivo StorSimple configurato con un gateway non routing può passare alla modalità offline. In questa versione, la metrica di routing per i dati 0 è stata effettuata i valori più bassi; di conseguenza, anche se altre interfacce di rete sono basate su cloud, tutto il traffico cloud dal dispositivo verrà indirizzato tramite dati 0. | Aggiornamento 1 | Sì | Sì | 
| 10 | Esecuzione di backup | Un errore di aggiornamento 1 che ha causato dopo 24 giorni i backup è stato risolto nella versione patch 1.1 aggiornamento. | Aggiornamento 1.1 | Sì | Sì |
| 11 | Esecuzione di backup | Riduzione delle prestazioni per snapshot cloud con tassi di modifica bassa condotto alla visualizzazione bug nelle versioni precedenti. Questo problema è stato risolto in questa versione di patch.| Aggiornamento 1.2 | Sì | Sì |
| 12 | Aggiornamenti | Un errore di aggiornamento 1 che segnalato un aggiornamento non riuscito e causato controller passa alla modalità di ripristino, è stato risolto in questa versione di patch.| Aggiornamento 1.2 | Sì | Sì |


## <a name="known-issues-in-update-12"></a>Problemi noti di aggiornamento 1.2

La tabella seguente contiene un riepilogo dei problemi noti di questa versione.

| No. | Caratteristica | Problema | Commenti/soluzione | Si applica a dispositivo fisico | Si applica a dispositivo virtuale |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Base disco | In rari, se la maggior parte dei dischi in simbolo EBOD di un dispositivo 8600 disconnessi risultante in base Nessun disco, quindi pool di archiviazione sarà offline. Rimarrà non in linea anche se i dischi riconnessione. | È necessario riavviare il dispositivo. Se il problema persiste, contattare il supporto Microsoft per la procedura successiva. | Sì | No |
| 2 | ID controller non corretti | Quando viene eseguita la sostituzione controller, controller 0 vengano visualizzate come controller di 1. Durante la sostituzione dei controller, quando l'immagine viene caricata dal nodo peer ID controller può essere visualizzata inizialmente come ID. del controller peer In alcuni casi il questo comportamento può essere visualizzato anche dopo il riavvio del sistema. | Non è necessario eseguire alcuna azione. Questa situazione risolverà stesso una volta completata la sostituzione controller. | Sì | No |
| 3 | Account di archiviazione | Utilizzo del servizio di archiviazione per eliminare l'account di archiviazione è uno scenario non supportato. Questa operazione comporta a situazioni in cui non è possibile recuperare dati utente. | Sì | Sì |
| 4 | Dispositivo failover | Failover più di un contenitore di volume dalla stessa periferica di origine per diversi dispositivi non è supportato. Il controllo dispositivo da un unico dispositivo inattivo a più dispositivi consentono di rendere i contenitori volume il primo non è riuscita sul dispositivo perde la proprietà dei dati. Dopo un failover, questi contenitori volume verranno visualizzata o hanno un funzionamento diverso quando vengono visualizzati nel portale di classica Azure. | | Sì | No |
| 5 | Installazione | Durante la scheda StorSimple per l'installazione di SharePoint, è necessario specificare un indirizzo IP dispositivo affinché installa per completare correttamente.    | | Sì | No |
| 6 | Proxy Web | Se la configurazione del proxy web include HTTPS come protocollo specificato, la comunicazione al servizio di dispositivi interessata e passa offline il dispositivo. Verranno generati anche pacchetti di supporto del processo di risorse significativa nel dispositivo. | Assicurarsi che l'URL del proxy web disponga HTTP come protocollo specificato. Per ulteriori informazioni, passare al [proxy web di configurazione del dispositivo](storsimple-configure-web-proxy.md). | Sì | No |
| 7 | Proxy Web | Se si configura e Abilita proxy web in un dispositivo registrato, sarà necessario riavviare controller attivo nel dispositivo. | | Sì | No |
| 8 | Latenza elevata cloud e carico di lavoro elevato i/o | Quando il dispositivo StorSimple rileva una combinazione di latenza elevata cloud (ordine di secondi) e il carico di lavoro i/o elevato, i volumi dispositivo passare a una riduzione e i / o potrebbe non riuscire con un errore di "dispositivo non pronto". | È necessario riavviare il controller di dispositivo o eseguire il controllo un dispositivo a risolvere questa situazione manualmente. | Sì | No |
| 9 | PowerShell Azure | Quando si utilizza il cmdlet StorSimple **Get-AzureStorSimpleStorageAccountCredential & #124; Selezionare oggetto - attesa prima di tutto 1 -** per selezionare il primo oggetto in modo che è possibile creare un nuovo oggetto **VolumeContainer** , il cmdlet restituisce tutti gli oggetti. | Disporre il cmdlet tra parentesi nel modo seguente: **(Get-Azure-StorSimpleStorageAccountCredential) & #124; Selezionare oggetto - 1 prima di tutto - attesa** | Sì | Sì |
| 10| Migrazione | Quando vengono passati più contenitori volume per la migrazione, greco per il backup più recente è accurata solo per il primo contenitore volume. Inoltre, la migrazione parallela inizierà dopo la migrazione delle prime 4 backup nel primo contenitore di volume. | È consigliabile migrare contenitore volume uno alla volta. | Sì | No |
| 11| Migrazione | Dopo il ripristino volumi non vengono aggiunti al criteri di backup o al gruppo disco virtuale. | È necessario aggiungere questi volumi a un criterio di backup per la creazione di backup. | Sì | Sì |
| 12| Migrazione | Una volta completata la migrazione, il dispositivo serie 5000/7000 non deve accedere i contenitori di dati migrati. | È consigliabile eliminare i contenitori di dati migrati dopo la migrazione completa e approvata. | Sì | No |
| 13| Clona e DR | Un dispositivo StorSimple che esegue aggiornamento 1 non è possibile duplicare o eseguire il ripristino di emergenza a un dispositivo che esegue pre-aggiornamento 1. | È necessario aggiornare il dispositivo di destinazione per aggiornare 1 per consentire queste operazioni | Sì | Sì |
| 14 | Migrazione | Backup della configurazione per la migrazione potrebbe non riuscire in un dispositivo di serie 5000 7000 quando sono presenti gruppi volume senza volumi associato. | Eliminare tutti i gruppi di volume vuota senza volumi associati e quindi riprovare a eseguire il backup di configurazione.| Sì | No |

## <a name="physical-device-updates-in-update-12"></a>Aggiornamenti di dispositivo fisico in aggiornamento 1.2

Se patch Aggiorna 1.2 viene applicata a un dispositivo fisico (che eseguono versioni prima dell'aggiornamento 1), la versione del software verrà modificato in 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Aggiornamenti di controller e firmware in aggiornamento 1.2

Questa versione aggiorna il driver firmware e del disco nel dispositivo.
 
- Per ulteriori informazioni sull'aggiornamento controller SA, vedere [aggiornamento 1 per controller LSI SA in Microsoft Azure StorSimple accessorio](https://support.microsoft.com/kb/3043005). 

- Per ulteriori informazioni su firmware disco aggiornamento, vedere [firmware disco aggiornamento 1 per Microsoft Azure StorSimple accessorio](https://support.microsoft.com/kb/3063416).
 
## <a name="virtual-device-updates-in-update-12"></a>Aggiornamenti di dispositivo virtuale in aggiornamento 1.2

Questo aggiornamento non possa essere applicato al dispositivo virtuale. Saranno necessario creare nuovi dispositivi virtuali. 

## <a name="next-steps"></a>Passaggi successivi

- [Installare Aggiornamento 1.2 nel dispositivo](storsimple-install-update-1.md).
 
