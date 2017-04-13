<properties 
    pageTitle="Note sulla versione 0.1 di aggiornamento 8000 StorSimple | Microsoft Azure"
    description="Descrive le nuove funzionalità e le correzioni, problemi aperti e soluzioni alternative disponibili per ottobre 2014 sulla versione di Microsoft Azure StorSimple (aggiornamento 0,1)."
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
    ms.date="09/21/2016"
    ms.author="alkohli" />

# <a name="storsimple-8000-series-update-01-release-notes--october-2014"></a>Note sulla versione 0.1 di StorSimple 8000 serie aggiornamento: ottobre 2014  

## <a name="overview"></a>Panoramica

Note sulla versione seguenti identificare i problemi di Apri critici StorSimple 8000 serie aggiornamento 0,1 rilasciato in ottobre 2014. Contengono anche un elenco degli aggiornamenti di software e firmware del StorSimple inclusi in questa versione. Questa è la prima versione dopo la StorSimple 8000 serie versione è stata effettuata in genere disponibile in luglio 2014 e corrisponde alla versione del software 6.3.9600.17312.  

È consigliabile cercare e applicare gli aggiornamenti disponibili subito dopo aver installato il dispositivo. È anche possibile attivare gli aggiornamenti automatici per scaricare e installare gli aggiornamenti priorità alta da Microsoft, non appena sono disponibili. Per ulteriori informazioni, vedere come [aggiornare il dispositivo StorSimple](storsimple-update-device.md).  

Esaminare le informazioni contenute nelle note sulla versione prima di distribuire gli aggiornamenti della soluzione StorSimple.  

>[AZURE.IMPORTANT]
> 
-   Utilizzare il servizio di gestione StorSimple e non Windows PowerShell per StorSimple per installare gli aggiornamenti di ottobre.  
-   Gli aggiornamenti in genere richiedere circa 3 ore.  
-   La versione di ottobre di StorSimple non contiene tutti gli aggiornamenti al dispositivo virtuale StorSimple. È comunque possibile applicare gli aggiornamenti di Windows disponibili, tra cui correzioni recenti, ma non si vedranno una modifica nella versione per il dispositivo virtuale.  

Assicurarsi che siano soddisfatti i prerequisiti seguenti prima dell'aggiornamento del dispositivo StorSimple.  

- Verificare che entrambi i controller di dispositivo sono in esecuzione prima che la disponibilità di aggiornamenti. Se uno dei due controller non è in esecuzione, l'elemento digitalizzato. Per verificare che i controller funzionante, passare allo **Stato di Hardware** sotto la pagina **manutenzione** . Se sono presenti componenti che **necessità attenzione**, contattare il supporto Microsoft prima di continuare.  
- Assicurarsi che IP fissi per entrambi Controller 0 e 1 Controller sono routing e connettersi a Internet utilizzati per la manutenzione gli aggiornamenti al dispositivo. È possibile utilizzare il [cmdlet Test connessione](https://technet.microsoft.com/library/hh849808.aspx) per effettuare il ping indirizzo noto all'esterno della rete, ad esempio outlook.com, per verificare che il controller dispone di connettività di rete esterna.  
- Assicurarsi che le porte in uscita necessari siano disponibili in un dispositivo StorSimple per le comunicazioni in uscita. Per ulteriori informazioni, vedere [requisiti per il dispositivo di StorSimple di rete](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).  
- Se il software o dispositivo antecedente 6.3.9600.17312 (aggiornamento di ottobre 2014), disabilitare le porte dati 2 e 3 dati se abilitata, prima di iniziare l'aggiornamento. Se si lascia la dati 2 o 3 dati porte abilitate per l'applicazione dell'aggiornamento, potrebbe controller dispositivo passa alla modalità di ripristino. Si noti che, quando si disattivano le interfacce di rete, tutti i volumi associati impostare la modalità offline e i/o verrà interrotto per la durata dell'aggiornamento.  

## <a name="whats-new-in-the-october-release"></a>Quali sono le novità della versione di ottobre

Questo aggiornamento include i miglioramenti seguenti:

- È ora possibile usare il servizio di gestione StorSimple dell'interfaccia utente per gestire i controller di dispositivo. Gestione azioni includono riavviare, arresto, o attivare un controller. Per ulteriori informazioni, passare a [gestire StorSimple controller di dispositivo](storsimple-manage-device-controller.md).  
- È possibile pianificare l'allocazione della larghezza di banda WAN in base alle combinazioni giorno della settimana e ora del giorno. In questo modo è possibile ottimizzare l'utilizzo della larghezza di banda WAN durante le ore. Modelli di larghezza di banda diversi sono consentiti per i contenitori volume diverso. Per ulteriori informazioni, passare a [gestire i modelli di larghezza di banda StorSimple](storsimple-manage-bandwidth-templates.md).  
- È possibile configurare le notifiche di posta elettronica per avvisare tempestivamente l'amministratori e altri problemi eventualmente imminenti o esistente. Per ulteriori informazioni, vedere [configurare le impostazioni degli avvisi](storsimple-manage-alerts.md#configure-alert-settings).  

## <a name="issues-fixed-in-the-october-release"></a>Problemi risolti nella versione ottobre


La tabella seguente contiene un riepilogo dei problemi risolti in questo aggiornamento.  

| No. | Caratteristica | Problema | Si applica a dispositivo fisico | Si applica a dispositivo virtuale |
|-----|---------|-------|---------------------------------|--------------------------------|
| 1 | Interfacce di rete | Nella versione precedente, le interfacce di rete dati 2 e 3 dati sono stati scambiati nel software. È stato risolto in questo aggiornamento. Deselezionare le impostazioni e disabilitare le interfacce di rete prima di installare l'aggiornamento. Dopo aver installato l'aggiornamento, sarà necessario riconfigurare queste interfacce. | Sì | No |
| 2 | Pacchetto di supporto | Nella versione precedente, se è stato eseguito il cmdlet di Windows PowerShell **HcsSupportPackage Esporta** per recuperare i registri della scheda base Management Controller (BMC), l'operazione non riuscita con il seguente messaggio di avviso: "l'operazione ha avuto esito positivo sul controller, ma non sul controller peer a causa errori seguenti. Verificare che sia integro peer e se il nodo corrente può connettersi al peer." Questo problema viene risolto ora. | Sì | No |
| 3 | Dispositivo failover | Nella versione precedente, durante la possibilità di dati incoerenti se un processo **alla scoperta di backup** non è riuscita durante caso di errore dispositivo. Questo problema viene risolto ora. | Sì | No |
| 4 | Dispositivo failover | Nella versione precedente, dopo caso di errore dispositivo, l'esecuzione di backup erano visibili ma il contenitore volume associato non è presenta nel dispositivo di destinazione. Questo problema viene risolto ora. | Sì | No |
| 5 | Dispositivo failover | Nella versione precedente, si è verificato un errore nell'enumerazione di backup cloud durante l'operazione di ripristino del Registro di sistema che potrebbe causare potenzialmente incoerenze nei dati se si sono verificati problemi di connettività del cloud. | Sì | No |
| 6 | Aggiornamento del firmware | Nella versione precedente, il processo di aggiornamento del firmware dispositivo non è riuscita e visualizzato un messaggio di errore che indicata i cmdlet non riconoscibili e che l'aggiornamento non riuscito di conseguenza. Il controller causa quindi in modalità di ripristino. Questo problema viene risolto ora. | Sì | No |
| 7 | Installazione | Errori il dispositivo non viene creata l'immagine correttamente durante l'installazione a questo punto sono stati corretti. | Sì | No |
| 8 | Reimposta factory | A questo punto è possibile ignorare facoltativamente controlla firmware Reimposta factory. Si tratta di un cambiamento rispetto alla versione precedente. | Sì | No |
| 9 | Reimposta factory | Nella versione precedente, durante l'esecuzione di un cmdlet di reimpostazione factory, controlli di versione firmware sono stati apportati solo per alcuni componenti hardware. Controlli aggiuntivi firmware sono stati apportati dopo il primo riavvio del processo, che potrebbe causare il ripristino di esito negativo. Questa correzione assicura che tutti i controlli firmware effettuati quando la factory Reimposta cmdlet viene eseguito e prima che il sistema prima riavviare il computer. | Sì | No |
| 10 | Rotazione delle chiavi account lo spazio di archiviazione | Il cmdlet **Richiama HcsmServiceDataEncryptionKeyChange** utilizzato per ruotare i tasti di account di archiviazione ora richiede all'utente di immettere la chiave di crittografia di servizio dati. Si tratta di un cambiamento rispetto alla versione precedente in cui la chiave di crittografia dati del servizio è stata passata come parametro all'interno del testo. | Sì | No |
| 11 | Failback entro 24 ore | Durante il recupero di emergenza, la pulizia del dispositivo di origine non sia stata eseguita correttamente, causando failback l'esito negativo. È stato risolto in questa versione. | Sì | No |

## <a name="known-issues-in-the-october-release"></a>Problemi noti della versione di ottobre

La tabella seguente contiene un riepilogo dei problemi noti di questa versione.

| No. | Caratteristica | Problema | Commenti/soluzione | Si applica a dispositivo fisico | Si applica a dispositivo virtuale |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Reimposta factory | In alcuni casi, quando si esegue il ripristino di factory, il dispositivo StorSimple potrebbe essere bloccato e visualizzare il messaggio seguente: **Reimposta su factory è in corso (fase 8)**. Si verifica questo evento se si preme CTRL + C, mentre il cmdlet è in corso. | Dopo aver iniziato la reimpostazione factory non premere CTRL + C. Se si ha già in questo stato, contattare il supporto Microsoft per la procedura successiva. | Sì | No |
| 2 | Reimposta factory | Eseguire non factory reimpostare un dispositivo StorSimple che viene aggiornato da GA ad ottobre 2014 rilasciare. | Questa operazione funziona solo se è installata una patch. Contattare il supporto Microsoft per ottenere questa patch necessaria. | Sì | No | 
| 3 | Base disco | In rari, se la maggior parte dei dischi in simbolo EBOD di un dispositivo 8600 disconnessi risultante in base Nessun disco, quindi pool di archiviazione sarà offline. Rimarrà non in linea anche se i dischi riconnessione. | È necessario riavviare il dispositivo. Se il problema persiste, contattare il supporto Microsoft per la procedura successiva. | Sì | No |
| 4 | Cloud snapshot errori | In alcuni casi l'uno snapshot cloud potrebbe non riuscire con l'errore **raggiunto il limite backup massimo**. Se si superano 255 duplicati online nello stesso dispositivo dallo stesso volume originale che è stato eliminato. | | Sì | Sì |
| 5 | ID controller non corretti | Quando viene eseguita la sostituzione controller, controller 0 vengano visualizzate come controller di 1. Durante la sostituzione dei controller, quando l'immagine viene caricata dal nodo peer ID controller può essere visualizzata inizialmente come ID. del controller peer In alcuni casi il questo comportamento può essere visualizzato anche dopo il riavvio del sistema. |Non è necessario eseguire alcuna azione. Questa situazione risolverà stesso una volta completata la sostituzione controller. | Sì | No |
| 6 | Dispositivo monitoraggio grafici | Nel servizio di gestione StorSimple, i grafici di monitoraggio di dispositivo non funzionano quando base o autenticazione NTLM è attivata nella configurazione del server proxy per il dispositivo. | Modificare la configurazione del proxy web per il dispositivo sia registrato presso il servizio StorSimple Manager in modo che l'autenticazione è impostato su Nessuno. A tale scopo, eseguire il Windows PowerShell per cmdlet Set-HcsWebProxy StorSimple. | Sì | Sì |
| 7 | Account di archiviazione | Utilizzo del servizio di archiviazione per eliminare l'account di archiviazione è uno scenario non supportato. Questa operazione comporta a situazioni in cui non è possibile recuperare dati utente. | | Sì | Sì |
| 8 | Dispositivo failover | Failover più di un contenitore di volume dalla stessa periferica di origine per diversi dispositivi non è supportato. | Il controllo da un unico dispositivo inattivo a più dispositivi consentono di rendere i contenitori volume il primo non è riuscita sul dispositivo perde la proprietà dei dati. Dopo un failover, questi contenitori volume verranno visualizzata o hanno un funzionamento diverso quando vengono visualizzati nel portale di classica Azure. | Sì | No |
| 9 | Installazione | Durante la scheda StorSimple per l'installazione di SharePoint, è necessario specificare un indirizzo IP dispositivo affinché installa per completare correttamente.    | | Sì | No |
| 10 | Proxy Web | Se la configurazione del proxy web include HTTPS come protocollo specificato, la comunicazione al servizio di dispositivi interessata e passa offline il dispositivo. Verranno generati anche pacchetti di supporto del processo di risorse significativa nel dispositivo. | Assicurarsi che l'URL del proxy web disponga HTTP come protocollo specificato. Ulteriori informazioni su come [configurare proxy web per il dispositivo](storsimple-configure-web-proxy.md). | Sì | No |
| 11 | Proxy Web | Se si configura e Abilita proxy web in un dispositivo registrato, sarà necessario riavviare controller attivo nel dispositivo. | | Sì | No |
| 12 | Latenza elevata cloud e carico di lavoro elevato i/o | Quando il dispositivo StorSimple rileva una combinazione di latenza elevata cloud (ordine di secondi) e il carico di lavoro i/o elevato, i volumi dispositivo passare a una riduzione e i / o potrebbe non riuscire con un errore di "dispositivo non pronto". | È necessario riavviare il controller di dispositivo o eseguire il controllo un dispositivo a risolvere questa situazione manualmente. | Sì | No |

## <a name="physical-device-updates-in-the-october-release"></a>Dispositivo fisico aggiornamenti di ottobre release

Quando sono applicati gli aggiornamenti in un dispositivo fisico, la versione del software cambierà a 6.3.9600.17312. Se non diversamente specificato, queste note sulla versione si applicano a tutti i modelli di dispositivo StorSimple. Per ulteriori informazioni su questi aggiornamenti, vedere [software accessorio fisica ottobre 2014 aggiornamento per Microsoft Azure StorSimple accessorio](http://support.microsoft.com/kb/2986997).  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-october-release"></a>Allegato seriale controller SCSI (SA) e firmware aggiornamenti di ottobre di rilascio

Questa versione aggiorna il driver e firmware sul controller SA del dispositivo fisico. Per ulteriori informazioni sul controller SA aggiornamento, vedere [aggiornare per controller LSI SA in Microsoft Azure StorSimple accessorio ottobre 2014](http://support.microsoft.com/kb/2987020).   

In questa versione riguarda anche un aggiornamento del firmware cumulativo in grado di risolvere i problemi di affidabilità con i componenti hardware dispositivo. Per ulteriori informazioni sull'aggiornamento del firmware, vedere [aggiornamento per Microsoft Azure StorSimple accessorio firmware di ottobre 2014](http://support.microsoft.com/kb/2987015).  

## <a name="virtual-device-updates-in-the-october-release"></a>Dispositivo virtuale aggiornamenti di ottobre release

In questa versione non contiene gli aggiornamenti disponibili per il dispositivo virtuale. Applicazione dell'aggiornamento, la versione del software di un dispositivo virtuale rimarrà invariato.
 
