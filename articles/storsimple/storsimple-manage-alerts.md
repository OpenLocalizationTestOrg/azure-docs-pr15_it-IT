<properties 
   pageTitle="Visualizzare e gestire gli avvisi StorSimple | Microsoft Azure"
   description="Descrive le condizioni di avviso StorSimple e gravità, come configurare le notifiche di avviso e come usare il servizio di gestione di StorSimple per gestire gli avvisi."
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
   ms.workload="NA"
   ms.date="10/18/2016"
   ms.author="anbacker" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-alerts"></a>Utilizzare il servizio di gestione StorSimple per visualizzare e gestire gli avvisi StorSimple

## <a name="overview"></a>Panoramica

Sulla scheda **avvisi** nel servizio di gestione StorSimple offre un modo per la revisione e deselezionare StorSimple correlate al dispositivo avvisi in tempo reale. Da questa scheda è possibile monitorare centralmente i problemi di integrità dei dispositivi StorSimple e la soluzione Microsoft Azure StorSimple globale.

In questa esercitazione viene condizioni comuni degli avvisi, livelli di gravità degli avvisi e come configurare le notifiche di avviso. Inoltre, include tabelle avviso di riferimento rapido che consentono di individuare un avviso specifico rapidamente e rispondere in modo appropriato.

![Pagina avvisi](./media/storsimple-manage-alerts/HCS_AlertsPage.png)

## <a name="common-alert-conditions"></a>Condizioni di avviso comuni

Il dispositivo StorSimple genera avvisi in risposta a una serie di condizioni. Di seguito sono i tipi di condizioni di avviso più comuni:

- **Problemi hardware** – gli avvisi informare lo stato dell'hardware. Consentono di stabilire se è necessarie aggiornamento del firmware, se un'interfaccia di rete ha problemi o se si è verificato un problema con una delle unità dati.

- **Problemi di connettività** : gli avvisi si verificano in caso di difficoltà di trasferimento dei dati. Problemi di comunicazione possono verificarsi durante il trasferimento di dati da e account Azure dello spazio di archiviazione o per mancanza di connessione tra i dispositivi e il servizio di gestione StorSimple. Problemi di comunicazione sono alcune delle più difficili da correggere perché sono presenti molti punti di errore. È sempre prima di tutto necessario verificare che la connettività di rete e accesso a Internet siano disponibili prima di procedere alla risoluzione dei problemi più avanzate. Per informazioni sulla risoluzione dei problemi, vedere [risoluzione dei problemi con il cmdlet Test connessione](storsimple-troubleshoot-deployment.md).

- **Problemi di prestazioni** : gli avvisi di questi errori sono causati durante il sistema non è in modo ottimale, ad esempio quando si trova in un carico elevato.

Inoltre, verranno visualizzati gli avvisi relativi a sicurezza, aggiornamenti o errori dei processi.

## <a name="alert-severity-levels"></a>Livelli di gravità degli avvisi

Avvisi sono diversi livelli di gravità, a seconda l'impatto avrà la situazione degli avvisi e l'esigenza di una risposta all'avviso. I livelli di gravità sono:

- **Critica** – questo avviso è in risposta a una condizione che influenzano le prestazioni di successo del sistema di posta. Azione è necessario per verificare che StorSimple servizio non verrà interrotto.

- **Avviso** : questa condizione potrebbe diventano critica se non è stato risolto. Esaminare la situazione e intraprendere alcuna azione necessari per cancellare il problema.

- **Informazioni** di questo avviso contiene informazioni che possono essere utile per rilevamento e gestione del sistema.

## <a name="configure-alert-settings"></a>Configurare le impostazioni degli avvisi

È possibile scegliere se si desidera ricevere una notifica tramite posta elettronica di condizioni di avviso per ognuno dei dispositivi StorSimple. Inoltre, è possibile identificare gli altri destinatari di notifica degli avvisi, immettere gli indirizzi di posta elettronica nella casella **altri destinatari di posta elettronica** , separati da punti e virgola.

>[AZURE.NOTE] È possibile immettere un massimo di 20 indirizzi di posta elettronica al dispositivo.

Dopo aver attivato la notifica tramite posta elettronica per un dispositivo, i membri dell'elenco di notifiche verranno visualizzato un messaggio di posta elettronica ogni volta che si verifica un avviso critico. I messaggi inviati da *storsimple-alerts-noreply@mail.windowsazure.com* e verranno descritte la condizione dell'avviso. I destinatari possono fare clic su **Annulla sottoscrizione** per se stessi rimuovere dall'elenco di notifiche di posta elettronica.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Per attivare la notifica di messaggio di posta elettronica degli avvisi per un dispositivo

1. Passare a **dispositivi** > **Configura** per il dispositivo.

2. In **Impostazioni degli avvisi**, impostare le opzioni seguenti:

    1. Nella casella **Invia messaggio di notifica** , selezionare **Sì**.

    2. Nel campo **amministratori dei servizi di posta elettronica** selezionare **Sì** se si desidera che l'amministratore di servizio e tutti gli CO-amministratori ricevono le notifiche di avviso.

    3. Nel campo **altri destinatari di posta elettronica** immettere gli indirizzi di posta elettronica di tutti gli altri destinatari che riceveranno le notifiche di avviso. Immettere i nomi nel formato *someone@somewhere.com*. Utilizzare un punto e virgola per separare gli indirizzi di posta elettronica. È possibile configurare un massimo di 20 indirizzi di posta elettronica al dispositivo. 

        ![Configurazione di notifica degli avvisi](./media/storsimple-manage-alerts/AlertNotify.png)

3. Per inviare una notifica tramite posta elettronica di prova, fare clic sull'icona freccia accanto a **Invia messaggio di test**. Il servizio di gestione StorSimple verrà visualizzati i messaggi di stato in inoltra la notifica di test. 

4. Quando viene visualizzato il messaggio seguente, fare clic su **OK**. 

    ![Avvisi testare e-mail di notifica inviata](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig3.png)

    >[AZURE.NOTE] Se non è possibile inviare il messaggio di notifica di prova, il servizio di gestione StorSimple verrà visualizzato un messaggio appropriato. Fare clic su **OK**, attendere qualche minuto e riprovare a inviare di nuovo il messaggio di notifica di test. 

## <a name="view-and-track-alerts"></a>Visualizzare e registrare gli avvisi

Dashboard di servizio di gestione StorSimple offre una rapida occhiata il numero di avvisi nei dispositivi, elementi disposti per gravità.

![Dashboard di avvisi](./media/storsimple-manage-alerts/admin_alerts_dashboard.png)

Scegliere il livello di gravità verrà visualizzata la scheda **avvisi** . I risultati includono solo gli avvisi che corrispondono a tale livello di gravità.

![Report di avvisi per tipo di avviso](./media/storsimple-manage-alerts/admin_alerts_scoped.png)

Facendo clic su un avviso nell'elenco vengono fornite informazioni dettagliate per l'avviso, tra cui l'ora dell'ultima l'avviso è stato segnalato, il numero di occorrenze di un avviso sul dispositivo e l'azione consigliata per risolvere l'avviso di. Se si tratta di un avviso relativo a hardware, verrà anche identificato il componente hardware.

![Esempio di avviso hardware](./media/storsimple-manage-alerts/admin_alerts_hardware.png)

Se è necessario inviare le informazioni relative al supporto Microsoft, è possibile copiare i dettagli degli avvisi per un file di testo. Dopo aver seguito il suggerimento e risolvere la condizione di avviso locale, è consigliabile deselezionare l'avviso dal dispositivo selezionare l'avviso nella scheda **avvisi** e facendo clic su **Cancella**. Per cancellare più avvisi, selezionare ogni avviso, fare clic su qualsiasi colonna ad eccezione della colonna di **avviso** e quindi fare clic su **Cancella** dopo aver selezionato tutti gli avvisi da cancellare. Si noti che alcuni avvisi siano deselezionate automaticamente quando il problema viene risolto o il sistema aggiorna l'avviso con le nuove informazioni.

Quando si fa clic su **Cancella**, si avrà l'opportunità di fornire commenti relativi all'avviso e i passaggi che è stata eseguita per risolvere il problema. Alcuni eventi verranno eliminati dal sistema se un altro evento attivato con nuove informazioni. In questo caso, si verrà visualizzato il messaggio seguente.

![Messaggio di avviso deselezionare](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Ordinamento e revisione avvisi

Può risultare più efficiente per eseguire report sugli avvisi in modo che è possibile esaminare e deselezionarle in gruppi. Sulla scheda **avvisi** è inoltre possibile visualizzare fino a 250 avvisi. Se è stato superato il numero di avvisi, non tutti gli avvisi verranno visualizzati nella visualizzazione predefinita. È possibile combinare i campi seguenti per personalizzare gli avvisi di visualizzazione:

- **Stato** : È possibile visualizzare gli avvisi **attivo** o **deselezionata** . Avvisi attivi vengono comunque attivati nel sistema, mentre avvisi deselezionati sono stati cancellati manualmente da un amministratore o a livello di programmazione deselezionati in quanto la condizione dell'avviso di aggiornamento del sistema con le nuove informazioni.

- **Gravità** – è possibile visualizzare gli avvisi di tutti i livelli di gravità (informazioni critiche, avvisi,) o solo determinati gravità, ad esempio avvisi solo critici.

- **Origine** – è possibile visualizzare gli avvisi da tutte le fonti o limitare gli avvisi per quelli forniti dal servizio o uno o tutti i dispositivi.

- **Intervallo di tempo** , specificando le date **da** e **a** e timestamp, è possibile esaminare gli avvisi durante il periodo di tempo che si è interessati.

## <a name="alerts-quick-reference"></a>Guida di riferimento rapido gli avvisi

Le tabelle seguenti elencano alcuni degli avvisi di Microsoft Azure StorSimple che possono verificarsi, nonché suggerimenti e informazioni aggiuntive se disponibili. Gli avvisi di dispositivo StorSimple rientrano in una delle categorie seguenti:

- [Avvisi di connettività cloud](#cloud-connectivity-alerts)

- [Avvisi cluster](#cluster-alerts)

- [Avvisi di ripristino di emergenza](#disaster-recovery-alerts)

- [Avvisi hardware](#hardware-alerts)

- [Avvisi di errore di processo](#job-failure-alerts)

- [Avvisi volume localmente bloccata](#locally-pinned-volume-alerts)

- [Avvisi di rete](#networking-alerts)

- [Avvisi di prestazioni](#performance-alerts)

- [Avvisi di sicurezza](#security-alerts)

- [Il supporto degli avvisi di pacchetto](#support-package-alerts)

- [Aggiornare gli avvisi](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Avvisi di connettività cloud

|Testo dell'avviso|Evento|Ulteriori informazioni / azioni consigliate|
|:---|:---|:---|
|Non è possibile stabilire la connettività a <*nome delle credenziali cloud*>.|Non è possibile connettersi all'account di archiviazione.|Sembra che potrebbe essersi verificato un problema di integrazione applicativa con il dispositivo. Eseguire il `Test-HcsmConnection` cmdlet di Windows PowerShell Interface per StorSimple in un dispositivo per identificare e risolvere il problema. Se le impostazioni siano corrette, il problema potrebbe essere con le credenziali dell'account di archiviazione per il quale è stato generato l'avviso. In questo caso, utilizzare la `Test-HcsStorageAccountCredential` cmdlet per determinare se esistono problemi che possono essere risolti.<ul><li>Controllare le impostazioni di rete.</li><li>Verificare le credenziali dell'account di archiviazione.</li></ul>|
|Abbiamo non ricevuto heartbeat dal dispositivo per gli ultimi minuti <*numero*>.|Impossibile connettersi al dispositivo.|Sembra che si verifica un problema di integrazione applicativa con il dispositivo. Utilizzare la `Test-HcsmConnection` cmdlet di Windows PowerShell Interface per StorSimple in un dispositivo per identificare e risolvere il problema o contattare l'amministratore di rete.|

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>Comportamento di StorSimple quando si verifica un errore di integrazione applicativa cloud

Cosa succede se si verifica un errore di connettività cloud per dispositivo StorSimple in fase di produzione?

Se la connettività cloud non riesce in un dispositivo di produzione StorSimple, quindi in base allo stato del dispositivo, può verificarsi quanto segue: 

- **Per i dati locali nel dispositivo**: per un po' di tempo non ci sarà alcuna interruzione del servizio e lettura continueranno a essere rappresentate. Tuttavia, come il numero di IOs in sospeso aumenta e supera un limite, si legge possono iniziare a esito negativo. 

    A seconda della quantità di dati in un dispositivo, le operazioni di scrittura continuerà anche che venga eseguita per le poche ore dopo l'interruzione della connettività cloud. Le operazioni di scrittura verranno quindi determinare un rallentamento e infine iniziare a esito negativo se viene interrotta la connessione cloud per alcune ore. (Esiste temporanea sul dispositivo per i dati che deve essere inserito nel cloud. Quest ' area viene svuotata quando vengono inviati i dati. Se la connettività non riesce, non verranno inviati altri dati in quest ' area dello spazio di archiviazione nel cloud e IO non riuscirà.)   

 
- **Per i dati nel cloud**: per la maggior parte degli errori di connettività cloud, viene restituito un errore. Dopo la connessione è stata ripristinata, il file IOs ripresa senza che sia portare online il volume. In alcuni casi l'intervento potrebbe essere necessario ripristinare il volume online dal portale di classica Azure. 
 
- **Per gli snapshot cloud in corso**: l'operazione viene ripetuta più volte all'interno di 4-5 ore e se non viene ripristinata la connettività, snapshot cloud avrà esito negativo.


### <a name="cluster-alerts"></a>Avvisi cluster

|Testo dell'avviso|Evento|Ulteriori informazioni / azioni consigliate|
|:---|:---|:---|
|Dispositivo non è riuscita su <*nome del dispositivo*>.|Dispositivo è in modalità di manutenzione.|Dispositivo non riuscita tramite l'immissione o uscire dalla modalità di manutenzione. Questo è normale e non è richiesta alcuna azione. Dopo avere riconosciuto questo avviso, cancellarlo dalla pagina avvisi.|
|Dispositivo non è riuscita su <*nome del dispositivo*>.|Software o firmware del dispositivo è appena stato aggiornato.|Si è verificato caso di errore cluster a causa di un aggiornamento. Questo è normale e non è richiesta alcuna azione. Dopo avere riconosciuto questo avviso, cancellarlo dalla pagina avvisi.|
|Dispositivo non è riuscita su <*nome del dispositivo*>.|Controller è stato chiuso o si riavvia.|Dispositivo riuscita sul controller attivo è stato arrestato o riavviato dall'amministratore. È richiesta alcuna azione. Dopo avere riconosciuto questo avviso, cancellarlo dalla pagina avvisi.|
|Dispositivo non è riuscita su <*nome del dispositivo*>.|Failover pianificato.|Verificare che questo sia stato pianificato in caso di errore. Dopo avere eseguito l'azione appropriata, deselezionare questo avviso nella pagina avvisi.|
|Dispositivo non è riuscita su <*nome del dispositivo*>.|Failover non pianificato.|StorSimple è progettato per il recupero automatico dagli failover non previsti. Se viene visualizzato un numero elevato di questi avvisi, contattare il supporto Microsoft.|
|Dispositivo non è riuscita su <*nome del dispositivo*>.|Causa altri/sconosciuto.|Se viene visualizzato un numero elevato di questi avvisi, contattare il supporto Microsoft. Dopo aver risolto il problema, deselezionare questo avviso nella pagina avvisi.|
|Un servizio di dispositivo critici segnala lo stato come non riuscita.|Errore di servizio DataPath. |Contattare il supporto Microsoft per assistenza.|
|Indirizzo IP virtuale per l'interfaccia di rete <*dati #*> segnala lo stato come non riuscita. |Causa altri/sconosciuto. |Condizioni temporanee in alcuni casi possono causare gli avvisi. In questo caso, quindi questo avviso verrà automaticamente eliminato in un secondo momento. Se il problema persiste, contattare il supporto Microsoft.|
|Indirizzo IP virtuale per l'interfaccia di rete <*dati #*> segnala lo stato come non riuscita.|Nome dell'interfaccia: <*dati #*> indirizzo IP <IP address> Impossibile portare online è stato rilevato un indirizzo IP duplicato sulla rete. |Assicurarsi che l'indirizzo IP duplicato viene rimosso dalla rete oppure riconfigurare l'interfaccia con un indirizzo IP diverso.|


### <a name="disaster-recovery-alerts"></a>Avvisi di ripristino di emergenza

|Testo dell'avviso|Evento|Ulteriori informazioni / azioni consigliate|
|:---|:---|:---|
|Operazioni di ripristino potrebbero non consentono di ripristinare le impostazioni per il servizio. Dati di configurazione dispositivo sono incoerente per alcuni dispositivi.|Dati incoerenti rilevato dopo il ripristino di emergenza.|Dati crittografati il servizio non è sincronizzati con quello sul dispositivo. Autorizzare il dispositivo <*nome del dispositivo*> da Gestione StorSimple per avviare il processo di sincronizzazione. Utilizzare l'interfaccia di Windows PowerShell per StorSimple per eseguire il `Restore-HcsmEncryptedServiceData` su cmdlet <*nome del dispositivo*> del dispositivo, fornire la vecchia password come input per questo cmdlet per ripristinare il profilo di sicurezza. Eseguire il `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet per l'aggiornamento della chiave di crittografia di servizio dati. Dopo avere eseguito l'azione appropriata, deselezionare questo avviso nella pagina avvisi.|


### <a name="hardware-alerts"></a>Avvisi hardware

|Testo dell'avviso|Evento|Ulteriori informazioni / azioni consigliate|
|:---|:---|:---|
|Componente hardware <*ID componente*> segnala lo stato come <*stato*>.||Condizioni temporanee in alcuni casi possono causare gli avvisi. In caso affermativo, questo avviso verrà cancellato in un secondo momento. Se il problema persiste, contattare il supporto Microsoft.|
|Controller passiva non funziona correttamente.|Il controller (secondario) passivo non funziona.|Il dispositivo è operativo, ma uno dei controller non funziona correttamente. Provare a riavviare tale controller. Se il problema persiste, contattare il supporto Microsoft.|

### <a name="job-failure-alerts"></a>Avvisi di errore di processo

|Testo dell'avviso|Evento|Ulteriori informazioni / azioni consigliate|
|:---|:---|:---|
|Copia di backup del <*ID gruppo volume origine*> non è riuscita.|Processo di backup.|Problemi di connettività potrebbero impedire l'operazione di backup da completato. Se sono presenti non sono presenti problemi di connettività, è possibile raggiunto il numero massimo di backup. Eliminare tutti i backup che non sono più necessari e ripetere l'operazione. Dopo avere eseguito l'azione appropriata, deselezionare questo avviso nella pagina avvisi.|
|Non è possibile duplicato di <*gli ID elemento backup origine*> a <*numeri seriali di destinazione volume*>.|Processo di duplicato.|Aggiornare l'elenco di backup per verificare che il backup è ancora valido. Se il backup è valido, è possibile che problemi di connettività del cloud impediscono correttamente completare l'operazione duplicato. Se sono presenti non sono presenti problemi di connettività, si potrebbe raggiunto il limite di archiviazione. Eliminare tutti i backup che non sono più necessari e ripetere l'operazione. Dopo avere eseguito azione appropriata per risolvere il problema, deselezionare questo avviso nella pagina avvisi.|
|Ripristino di <*gli ID elemento backup origine*> non riuscito.|Ripristinare un processo non è riuscita.|Aggiornare l'elenco di backup per verificare che il backup è ancora valido. Se il backup è valido, è possibile che problemi di connettività del cloud impediscono l'operazione di ripristino da completato. Se sono presenti non sono presenti problemi di connettività, si potrebbe raggiunto il limite di archiviazione. Eliminare tutti i backup che non sono più necessari e ripetere l'operazione. Dopo avere eseguito azione appropriata per risolvere il problema, deselezionare questo avviso nella pagina avvisi.|

### <a name="locally-pinned-volume-alerts"></a>Avvisi volume localmente bloccata

|Testo dell'avviso|Evento|Ulteriori informazioni / azioni consigliate|
|:---|:---|:---|
|Creazione del volume locale <*il nome del volume*> non riuscita.| Il processo di creazione del volume non riuscita. <*Messaggio di errore corrispondente al codice di errore non riuscito*>.|Problemi di connettività potrebbero impedire l'operazione di creazione di spazio da completato. Locale bloccati volumi thickly vengono effettuato il provisioning e il processo di creazione spazio prevede venga volumi a più livelli nel cloud. Se sono presenti non sono presenti problemi di connettività, si potrebbe sono esaurite spazio locale nel dispositivo. Determinare l'esistenza di spazio sul dispositivo prima di ritentare l'operazione.|
|Espansione del volume locale <*il nome del volume*> non è riuscita.|Il processo di modifica volume non riuscita a causa di <*messaggio di errore corrispondente al codice di errore non riuscito*>.|Problemi di connettività potrebbero impedire l'operazione di espansione del volume da completato. Locale bloccati volumi thickly vengono effettuato il provisioning e il processo di estensione lo spazio esistente prevede venga volumi a più livelli nel cloud. Se sono presenti non sono presenti problemi di connettività, si potrebbe sono esaurite spazio locale nel dispositivo. Determinare l'esistenza di spazio sul dispositivo prima di ritentare l'operazione.|
|Conversione del volume <*il nome del volume*> non riuscita.|Il processo di conversione volume per convertire il tipo di volume da localmente bloccata a più livelli non riuscito.|Conversione del volume da tipo localmente aggiunta a più livelli potrebbe non essere completata. Assicurarsi che non siano non sono presenti problemi di connettività per impedire l'operazione di completamento. Per la risoluzione dei problemi di connettività passare alla [risoluzione dei problemi con il cmdlet Test HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Il volume localmente bloccato originale ora è stato contrassegnato come volume a più livelli dal momento che alcuni dati dal volume localmente bloccato sono stati inseriti vuoti nel cloud durante la conversione. Il volume a più livelli risultante è ancora che occupa spazio locale nel dispositivo che non possa essere recuperato per indicare i volumi locali futuri.<br>Risolvere i problemi di connettività, deselezionare l'avviso e convertire il volume il tipo di volume localmente bloccata originale per assicurarsi che tutti i dati viene resa disponibili in locale.|
|Conversione del volume <*il nome del volume*> non riuscita.|Il processo di conversione volume per convertire il tipo di volume a più livelli a localmente aggiunte non è riuscita.|Conversione del volume da tipo a più livelli a localmente aggiunte non riuscita. Assicurarsi che non siano non sono presenti problemi di connettività per impedire l'operazione di completamento. Per la risoluzione dei problemi di connettività passare alla [risoluzione dei problemi con il cmdlet Test HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Il volume a più livelli originale ora contrassegnato come volume localmente bloccato come parte del processo di conversione continua ad avere dati che risiedono nel cloud, mentre lo spazio thickly provisioning sul dispositivo per il volume non è più possibile recuperato per indicare i volumi locali futuri.<br>Risolvere i problemi di connettività, deselezionare l'avviso e convertire il volume il tipo di volume a più livelli originale in modo locale thickly viene completato il provisioning del dispositivo è espressamente.|
|Quasi consumo spazio locale per snapshot locale di <*nome del gruppo volume*>|Snapshot locale per criteri di backup breve potrebbe esaurirsi spazio e Invalidate per evitare errori di scrittura host.|Snapshot locale frequenti insieme a un varianza quotazioni massime in volumi associato a questo gruppo di criteri di backup causano spazio locale nel dispositivo devono essere utilizzate rapidamente. Eliminare qualsiasi istantanee locale che non sono più necessari. Inoltre, aggiornare le pianificazioni snapshot locale per questo criterio a istantanee locale meno frequenti e assicurarsi che cloud sono snapshot regolarmente backup. Se queste operazioni non vengono eseguite, spazio locale per questi snapshot breve potrebbe essere esaurito e il sistema automaticamente eliminarli per assicurarsi che scrive host continuino a essere elaborati correttamente.|
|Snapshot locale per <*nome del gruppo volume*> sono state invalidate.|Snapshot locale per <*nome del gruppo volume*> sono state invalidate e quindi eliminate perché sono state superiori ai spazio locale nel dispositivo.|Per assicurare che questo non ricorrente in futuro, rivedere le pianificazioni snapshot locale per questo criterio di backup ed eliminare qualsiasi istantanee locale che non sono più necessari. Snapshot locale frequenti insieme a un varianza quotazioni massime in volumi associato a questo gruppo di criteri di backup potrebbero causare spazio locale nel dispositivo devono essere utilizzate rapidamente.|
|Ripristino di <*gli ID elemento backup origine*> non riuscito.|Il processo di ripristino non riuscita.|Se sono aggiunte in locale o una combinazione di volumi localmente bloccati o a più livelli in questo criterio backup, aggiornare l'elenco di backup per verificare che il backup è ancora valido. Se il backup è valido, è possibile che problemi di connettività del cloud impediscono l'operazione di ripristino da completato. I volumi localmente bloccati ripristinati come parte di questo gruppo snapshot non dispone di tutti i relativi dati scaricati sul dispositivo e, se si dispone di una combinazione di volumi a più livelli e localmente inclusi in questo gruppo snapshot, non saranno sincronizzati tra loro. Per completare l'operazione di ripristino, eseguire i volumi in questo gruppo nell'host e ripetere l'operazione di ripristino. Si noti che le modifiche ai dati che sono state eseguite durante il processo di ripristino volume andranno persi.|

### <a name="networking-alerts"></a>Avvisi di rete

|Testo dell'avviso|Evento|Ulteriori informazioni / azioni consigliate|
|:---|:---|:---|
|Impossibile avviare StorSimple servizi.|Errore DataPath |Se il problema persiste, contattare il supporto Microsoft.|
|Indirizzo IP duplicato rilevato per 'Data0'.| |Il sistema ha rilevato un conflitto per indirizzo IP '10.0.0.1'. Risorsa di rete 'Data0' sul dispositivo *<device1>* è offline. Assicurarsi che l'indirizzo IP non è utilizzato da qualsiasi altra entità di questa rete. Per risolvere i problemi di rete, passare alla [risoluzione dei problemi con il cmdlet Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Per risolvere il problema, contattare l'amministratore di rete. Se il problema persiste, contattare il supporto Microsoft. |
|Indirizzi IPv4 (o IPv6) per 'Data0' sono offline.| |Risorsa di rete 'Data0' con indirizzo IP '10.0.0.1'. e lunghezza '22' sul dispositivo prefisso *<device1>* è offline. Assicurarsi che le porte di commutazione a cui è connesso questo interfaccia siano operative. Per risolvere i problemi di rete, passare alla [risoluzione dei problemi con il cmdlet Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
 

### <a name="performance-alerts"></a>Avvisi di prestazioni

|Testo dell'avviso|Evento|Ulteriori informazioni / azioni consigliate|
|:---|:---|:---|
|Il carico di dispositivo ha superato <*soglia*>.|Più lenta previsto tempi di risposta.|Il dispositivo report di utilizzo in ingresso/uscita carichi. Ciò potrebbe causare il dispositivo a non funzionare correttamente come dovrebbe. Esaminare i carichi di lavoro sono associati al dispositivo e determinare se presenti che potrebbero essere spostati in un altro dispositivo o che non sono più necessarie.<br>Per conoscere lo stato corrente, passare a [usare il servizio di gestione di StorSimple per controllare il dispositivo](storsimple-monitor-device.md)|

### <a name="security-alerts"></a>Avvisi di sicurezza

|Testo dell'avviso|Evento|Ulteriori informazioni / azioni consigliate|
|:---|:---|:---|
|Sessione di supporto Microsoft è iniziato.|Terze parti di accedere alla sessione di supporto.|Confermare che l'accesso è autorizzato. Dopo avere eseguito l'azione appropriata, deselezionare questo avviso nella pagina avvisi.|
|Password per <*elemento*> scadrà <*periodo di tempo*>.|Se ci si avvicina la scadenza delle password.|Cambiare la password prima della scadenza.|
|Informazioni sulla configurazione di protezione mancante per <*ID elemento*>.||I volumi associati a questo contenitore volume non è possibile usare per replicare la configurazione di StorSimple. Per assicurarsi che i dati vengono memorizzati in modo sicuro, si consiglia di eliminare il contenitore volume e i volumi associati al contenitore di volume. Dopo avere eseguito l'azione appropriata, deselezionare questo avviso nella pagina avvisi.|
|<*numero*> tentativi di accesso non riuscito per <*ID elemento*>.|Tentativi di accesso non più.|Il dispositivo sia attacco o un utente autorizzato sta tentando di connettersi con una password errata.<ul><li>Contattare gli utenti autorizzati e verificare che questi tentativi utilizzato da un mittente attendibile. Se si continua a essere visualizzato un numero elevato di tentativi di accesso non riuscito, è consigliabile disattivare la gestione remota e contattare l'amministratore di rete. Dopo avere eseguito l'azione appropriata, deselezionare questo avviso nella pagina avvisi.</li><li>Verificare che le istanze di gestione di Snapshot siano configurate con la password corretta. Dopo avere eseguito l'azione appropriata, deselezionare questo avviso nella pagina avvisi.</li></ul>Per ulteriori informazioni, passare alla sezione [Modifica la password di un dispositivo scaduto](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password).|
|Uno o più errori durante la modifica della chiave di crittografia di servizio dati.||Si sono verificati errori rilevati durante la modifica della chiave di crittografia di servizio dati. Dopo avere risolto le condizioni di errore, eseguire la `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet di Windows PowerShell Interface per StorSimple sul dispositivo per il servizio di aggiornamento. Se il problema persiste, contattare il supporto tecnico Microsoft. Dopo aver risolto il problema, deselezionare questo avviso nella pagina avvisi.|

### <a name="support-package-alerts"></a>Il supporto degli avvisi di pacchetto

|Testo dell'avviso|Evento|Ulteriori informazioni / azioni consigliate|
|:---|:---|:---|
|Creazione del pacchetto di supporto non è riuscita.|StorSimple Impossibile generare il pacchetto.|Ripetere l'operazione. Se il problema persiste, contattare il supporto Microsoft. Dopo aver risolto il problema, deselezionare questo avviso nella pagina avvisi.|

### <a name="update-alerts"></a>Aggiornare gli avvisi

|Testo dell'avviso|Evento|Ulteriori informazioni / azioni consigliate|
|:---|:---|:---|
|Aggiornamento rapido installato.|Aggiornamento di software/firmware completato.|L'aggiornamento rapido installato nel dispositivo.|
|Manualmente gli aggiornamenti disponibili.|Notifica degli aggiornamenti disponibili.|Utilizzare l'interfaccia di Windows PowerShell per StorSimple in un dispositivo per installare gli aggiornamenti. <br>Per ulteriori informazioni, vedere per [aggiornare il dispositivo StorSimple 8000 serie](storsimple-update-device.md).|
|Sono disponibili nuovi aggiornamenti.|Notifica degli aggiornamenti disponibili.|È possibile installare questi aggiornamenti dalla pagina **manutenzione** o tramite l'interfaccia di Windows PowerShell per StorSimple nel dispositivo. <br>Per ulteriori informazioni, vedere per [aggiornare il dispositivo StorSimple 8000 serie](storsimple-update-device.md).|
|Impossibile installare gli aggiornamenti.|Aggiornamenti non installati correttamente.|Il sistema non è possibile installare gli aggiornamenti. È possibile installare questi aggiornamenti dalla pagina **manutenzione** o tramite l'interfaccia di Windows PowerShell per StorSimple nel dispositivo. Se il problema persiste, contattare il supporto Microsoft. <br>Per ulteriori informazioni, vedere per [aggiornare il dispositivo StorSimple 8000 serie](storsimple-update-device.md).|
|Se non è possibile controllare automaticamente la disponibilità di nuovi aggiornamenti.|Controllo automatico non è riuscito.|È possibile controllare manualmente di nuovi aggiornamenti dalla pagina **manutenzione** .|
|Nuovo agente di Windows Update Agent disponibili.|Notifica di aggiornamenti disponibili.|Agente di Windows Update più recente di scaricare e installare dall'interfaccia di Windows PowerShell.|
|Versione del firmware componente <*ID componente*> non corrisponde con hardware.|Aggiornamenti di firmware non installati.|Contattare il supporto tecnico Microsoft.|

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sugli [errori StorSimple e risoluzione dei problemi relativi a un dispositivo operativo](storsimple-troubleshoot-operational-device.md).

