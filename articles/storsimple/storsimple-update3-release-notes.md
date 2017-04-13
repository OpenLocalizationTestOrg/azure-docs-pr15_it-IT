<properties 
   pageTitle="Note sulla versione StorSimple 8000 serie Update 3 | Microsoft Azure"
   description="Descrive le nuove funzionalità, problemi e soluzioni alternative per l'aggiornamento StorSimple 8000 serie 3."
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
   ms.date="10/14/2016"
   ms.author="alkohli" />

# <a name="storsimple-8000-series-update-3-release-notes"></a>Note sulla versione StorSimple 8000 serie Update 3  

## <a name="overview"></a>Panoramica

Note sulla versione seguenti descrivono le nuove caratteristiche e identificare i problemi critici aperti per l'aggiornamento StorSimple 8000 serie 3. Contengono anche un elenco degli aggiornamenti software StorSimple inclusi in questa versione. 

Aggiornamento 3 può essere applicato a qualsiasi dispositivo StorSimple eseguito 2.2 aggiornamento Release (GA) o aggiornamento 0,1. La versione per dispositivi associata a 3 di aggiornamento è 6.3.9600.17759.

Esaminare le informazioni contenute nelle note sulla versione prima di distribuire l'aggiornamento della soluzione StorSimple.

>[AZURE.IMPORTANT]
> 
> - Aggiornamento 3 ha dispositivo software, driver LSI e firmware e Storport Spaceport Aggiorna. Bastano circa 1,5-2 ore per installare l'aggiornamento. 

> - Per le nuove versioni, non sarà possibile visualizzare gli aggiornamenti immediatamente perché facciamo graduale degli aggiornamenti. Attendere alcuni giorni e quindi cercare gli aggiornamenti anche questi descritte diventeranno disponibili breve.


## <a name="whats-new-in-update-3"></a>Novità di aggiornamento 3

I seguenti miglioramenti chiavi e correggere gli errori sono stati apportati 3 di aggiornamento.

 
- **Automatico spazio richiesta modifiche** : avviare l'aggiornamento 3, algoritmi di recupero spazio eseguito sul controller di standby del sistema risultante esecuzione più veloce. Per ulteriori informazioni sulle porte necessari per lavorare con il recupero di spazio, fare riferimento ai [requisiti di rete StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

- **Miglioramenti delle prestazioni** -3 di aggiornamento sono state migliorate le prestazioni di lettura e scrittura nel cloud.

- **Miglioramenti relativi alla migrazione** : In questa versione, diverse correzioni di bug e miglioramenti sono stati eseguiti per la funzione di migrazione da dispositivi serie 5000/7000 8000 nei dispositivi serie. Per ulteriori informazioni su come utilizzare la funzione di migrazione, passare alla [migrazione da dispositivo serie 5000/7000 dispositivo serie 8000](https://www.microsoft.com/en-us/download/details.aspx?id=47322). 

- **Monitoraggio delle correzioni relative a** : In questa versione, bug correlati al monitoraggio grafici, dashboard del servizio e dashboard dispositivo sono stati corretti.


## <a name="issues-fixed-in-update-3"></a>Problemi risolti nell'aggiornamento 3

Le tabelle seguenti viene fornito un riepilogo dei problemi che sono stati risolti Update 3.    

| No | Caratteristica                                    | Problema                                                                                                                                                                                                                                                                                        | Si applica a dispositivo fisico | Si applica a dispositivo virtuale |
|----|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|---------------------------|
| 1  | Migrazione dei dati lato host                      | Nella versione precedente, l'accessorio Cloud StorSimple avrebbe offline durante la migrazione dei dati lato host. Questo problema viene risolto in questa versione.                                                | No                        | Sì                        |
| 2  | Volumi localmente bloccati                     | Nella versione precedente, si sono verificati problemi relativi agli errori i/o, errori di conversione volume e datapath errori per indicare i volumi localmente bloccati. Questi problemi sono state causati radice e fissa in questa versione.                | Sì                        | No                       |
| 3  | Monitoraggio                                    | Si sono verificati più i problemi relativi ai report unità e il monitoraggio e grafici di dashboard dispositivo posizione di visualizzazione di informazioni non corrette per indicare i volumi localmente bloccati. Questi problemi sono corretti in questa versione. | Sì                         | No                       |
| 4  | / O scrive piena                          | Quando si utilizza StorSimple per carichi di lavoro associate ai scrive piena, l'utente da eseguire in un bug poco frequente nel punto in cui il set di lavoro è stata vengano a più livelli nel cloud. Questo problema è stato risolto in questa versione. | Sì                        | Sì                       |
| 5  | Copia di backup                                     | In alcuni casi rari, nelle versioni precedenti di software, quando l'utente ha eseguito il backup di un duplicato remoto, si verificano errori cloud e generato un errore che si dell'operazione. In questa versione, il problema viene risolto e l'operazione ha esito positivo.             | Sì                        | Sì                       |
| 6  | Criteri di backup                                     | In alcuni casi rari, nelle versioni precedenti del software, si è verificato un errore relative all'eliminazione dei criteri di backup. Questo problema viene risolto in questa versione.       | Sì                        | Sì                       |



## <a name="known-issues-in-update-3"></a>Problemi noti di aggiornamento 3

La tabella seguente contiene un riepilogo dei problemi noti di questa versione.

| No. | Caratteristica | Problema | Commenti / soluzione | Si applica a dispositivo fisico | Si applica a dispositivo virtuale |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Base disco | In rari, se la maggior parte dei dischi in simbolo EBOD di un dispositivo 8600 disconnessi risultante in base Nessun disco, quindi pool di archiviazione andrà offline. Rimarrà offline anche se i dischi riconnessione. | È necessario riavviare il dispositivo. Se il problema persiste, contattare il supporto Microsoft per la procedura successiva. | Sì | No |
| 2 | ID controller non corretti | Quando viene eseguita la sostituzione controller, controller 0 vengano visualizzate come controller di 1. Durante la sostituzione dei controller, quando l'immagine viene caricata dal nodo peer ID controller può essere visualizzata inizialmente come ID. del controller peer In alcuni casi il questo comportamento può essere visualizzato anche dopo il riavvio del sistema. | Non è necessario eseguire alcuna azione. Questa situazione risolverà stesso una volta completata la sostituzione controller. | Sì | No |
| 3 | Account di archiviazione | Utilizzo del servizio di archiviazione per eliminare l'account di archiviazione è uno scenario non supportato. Questa operazione comporta a situazioni in cui non è possibile recuperare dati utente.|  | Sì | Sì |
| 4 | Dispositivo failover | Failover più di un contenitore di volume dalla stessa periferica di origine per diversi dispositivi non è supportato. Il controllo da un unico dispositivo inattivo a più dispositivi consentono di rendere i contenitori volume il primo non è riuscita sul dispositivo perde la proprietà dei dati. Dopo un failover, questi contenitori volume verranno visualizzata o hanno un funzionamento diverso quando vengono visualizzati nel portale di classica Azure. | | Sì | No |
| 5 | Installazione | Durante la scheda StorSimple per l'installazione di SharePoint, è necessario specificare un indirizzo IP dispositivo affinché installa per completare correttamente.    | | Sì | No |
| 6 | Proxy Web | Se la configurazione del proxy web include HTTPS come protocollo specificato, la comunicazione dispositivo-servizio avrà effetto sul e passa offline il dispositivo. Verranno generati anche pacchetti di supporto del processo di risorse significativa nel dispositivo. | Assicurarsi che l'URL del proxy web disponga HTTP come protocollo specificato. Per ulteriori informazioni, passare al [proxy web di configurazione del dispositivo](storsimple-configure-web-proxy.md). | Sì | No |
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
| 20 |Volumi localmente bloccati | Se si tenta di convertire un volume a più livelli (creato e duplicato con aggiornamento 1.2 o versioni precedente) in un volume locale bloccato e il dispositivo è spazio quasi esaurito o c'è un servizio cloud, la clone(s) possono essere danneggiati.| Questo problema si verifica solo con volumi che sono state create e sono duplicato con pre-aggiornamento 2.1 software. Deve trattarsi di uno scenario poco frequente.|
| 21 | Conversione del volume | Non vengono aggiornate ACRs associati a un volume durante l'esecuzione di una conversione del volume (a più livelli a localmente aggiunte o viceversa). Aggiornamento di ACRs potrebbe causare il danneggiamento dei dati. | Se necessario, aggiornare ACRs prima della conversione volume e non apportare ulteriori aggiornamenti ACR durante la conversione. |
| 22 | Aggiornamenti | Quando si applica l'aggiornamento 3, la pagina **manutenzione** nel portale di classica Azure verrà visualizzato il messaggio seguente correlato a aggiornamento 2: "serie 8000 StorSimple aggiornamento 2 include la possibilità di Microsoft per tempestiva raccogliere informazioni del log dal dispositivo quando si rileva potenziali problemi". È così, in quanto indica che il dispositivo è siano aggiornato con 2 di aggiornamento. Dopo avere inserito il dispositivo succeesfully aggiornate alla versione 3 di aggiornamento, il messaggio non sarà più visibile. | Questo comportamento verrà risolto nelle versioni future. | Sì | No |


## <a name="controller-and-firmware-updates-in-update-3"></a>Aggiornamenti di controller e firmware 3 di aggiornamento

In questa versione è LSI aggiornamenti driver e firmware. Per ulteriori informazioni su come installare il driver LSI e aggiornamenti del firmware, vedere [installare l'aggiornamento 3](storsimple-install-update-3.md) sul dispositivo StorSimple.

 
## <a name="virtual-device-updates-in-update-3"></a>Aggiornamenti di dispositivo virtuale in Update 3

Questo aggiornamento non può essere applicato all'accessorio Cloud StorSimple (noto anche come il dispositivo virtuale). Saranno necessario creare nuovi dispositivi virtuali. 


## <a name="next-step"></a>Passaggio successivo

Informazioni su come [installare l'aggiornamento 3](storsimple-install-update-3.md) in un dispositivo StorSimple.
