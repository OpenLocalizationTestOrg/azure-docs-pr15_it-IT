<properties 
   pageTitle="Visualizzare e gestire gli avvisi di matrice virtuale StorSimple | Microsoft Azure"
   description="Descrive le condizioni di avviso matrice virtuale StorSimple e gravità e come usare il servizio di gestione di StorSimple per gestire gli avvisi."
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
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-alerts-for-the-storsimple-virtual-array"></a>Utilizzare il servizio di gestione StorSimple per visualizzare e gestire avvisi per la matrice virtuale StorSimple

## <a name="overview"></a>Panoramica

Sulla scheda **avvisi** nel servizio di gestione StorSimple offre un modo per la revisione e deselezionare gli avvisi correlati matrice virtuale StorSimple in tempo reale. Da questa scheda è possibile monitorare centralmente i problemi di integrità delle matrici virtuale StorSimple (noto anche come StorSimple locale dispositivi virtuali) e la soluzione Microsoft Azure StorSimple globale.

In questa esercitazione viene descritto come configurare le notifiche, le condizioni di avviso comuni, livelli di gravità degli avvisi e come visualizzare e registrare gli avvisi. Inoltre, include tabelle avviso di riferimento rapido che consentono di individuare un avviso specifico rapidamente e rispondere in modo appropriato.

![Pagina avvisi](./media/storsimple-ova-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Configurare le impostazioni degli avvisi

È possibile scegliere se si desidera ricevere una notifica tramite posta elettronica di condizioni di avviso per ognuno dei dispositivi virtuali StorSimple. Inoltre, è possibile identificare gli altri destinatari di notifica degli avvisi, immettere gli indirizzi di posta elettronica nella casella **altri destinatari di posta elettronica** , separati da punti e virgola.

>[AZURE.NOTE] È possibile immettere un massimo di 20 indirizzi di posta elettronica per dispositivo virtuale.

Dopo aver attivato la notifica tramite posta elettronica per un dispositivo virtuale, i membri dell'elenco di notifiche verranno visualizzato un messaggio di posta elettronica ogni volta che si verifica un avviso critico. I messaggi inviati da *storsimple-alerts-noreply@mail.windowsazure.com* e verranno descritte la condizione dell'avviso. I destinatari possono fare clic su **Annulla sottoscrizione** per se stessi rimuovere dall'elenco di notifiche di posta elettronica.

#### <a name="to-enable-email-notification-of-alerts-for-a-virtual-device"></a>Per attivare la notifica di messaggio di posta elettronica degli avvisi per un dispositivo virtuale

1. Passare a **dispositivi** > **configurazione** per il dispositivo virtuale. Passare alla sezione **impostazioni degli avvisi** .

    ![impostazioni degli avvisi](./media/storsimple-ova-manage-alerts/alerts2.png)

2. In **impostazioni degli avvisi**, impostare le opzioni seguenti:

    1. Nella casella **Invia messaggio di notifica** , selezionare **Sì**.

    2. Nel campo **amministratori dei servizi di posta elettronica** selezionare **Sì** se si desidera che l'amministratore di servizio e tutti gli CO-amministratori ricevono le notifiche di avviso.

    3. Nel campo **altri destinatari di posta elettronica** immettere gli indirizzi di posta elettronica di tutti gli altri destinatari che riceveranno le notifiche di avviso. Immettere i nomi nel formato *someone@somewhere.com*. Utilizzare un punto e virgola per separare gli indirizzi di posta elettronica. È possibile configurare un massimo di 20 indirizzi di posta elettronica per dispositivo virtuale. 

        ![configurazione di notifica degli avvisi](./media/storsimple-ova-manage-alerts/alerts3.png)

3. Nella parte inferiore della pagina, fare clic su **Salva** per salvare la configurazione.

4. Per inviare una notifica tramite posta elettronica di prova, fare clic sull'icona freccia accanto a **Invia messaggio di test**. Il servizio di gestione StorSimple verrà visualizzati i messaggi di stato in inoltra la notifica di test. 

5. Quando viene visualizzato il messaggio seguente, fare clic su **OK**. 

    ![Avvisi testare e-mail di notifica inviata](./media/storsimple-ova-manage-alerts/alerts4.png)

    >[AZURE.NOTE] Se non è possibile inviare il messaggio di notifica di prova, il servizio di gestione StorSimple verrà visualizzato un messaggio appropriato. Fare clic su **OK**, attendere qualche minuto e riprovare a inviare di nuovo il messaggio di notifica di test. 

    Messaggio di notifica di test sarà simile al seguente.

    ![Esempio di messaggio di posta elettronica test avvisi](./media/storsimple-ova-manage-alerts/alerts5.png)

## <a name="common-alert-conditions"></a>Condizioni di avviso comuni

La matrice virtuale StorSimple genera avvisi in risposta a una serie di condizioni. Di seguito sono i tipi di condizioni di avviso più comuni:

- **Problemi di connettività** : gli avvisi si verificano in caso di difficoltà di trasferimento dei dati. Problemi di comunicazione possono verificarsi durante il trasferimento di dati da e account Azure dello spazio di archiviazione o per mancanza di connessione tra i dispositivi virtuali e il servizio di gestione StorSimple. Problemi di comunicazione sono alcune delle più difficili da correggere perché sono presenti molti punti di errore. È sempre prima di tutto necessario verificare che la connettività di rete e accesso a Internet siano disponibili prima di procedere alla risoluzione dei problemi più avanzate. Per informazioni sulle porte e le impostazioni del firewall, vedere [requisiti di sistema StorSimple matrice virtuale](storsimple-ova-system-requirements.md). Per informazioni sulla risoluzione dei problemi, vedere [risoluzione dei problemi con il cmdlet Test connessione](storsimple-troubleshoot-deployment.md).

- **Problemi di prestazioni** : gli avvisi di questi errori sono causati durante il sistema non è in modo ottimale, ad esempio quando si trova in un carico elevato.

Inoltre, verranno visualizzati gli avvisi relativi a sicurezza, aggiornamenti o errori dei processi.

## <a name="alert-severity-levels"></a>Livelli di gravità degli avvisi

Avvisi sono diversi livelli di gravità, a seconda l'impatto avrà la situazione degli avvisi e l'esigenza di una risposta all'avviso. I livelli di gravità sono:

- **Critica** – questo avviso è in risposta a una condizione che influenzano le prestazioni di successo del sistema di posta. Azione è necessario per verificare che StorSimple servizio non verrà interrotto.

- **Avviso** : questa condizione potrebbe diventano critica se non è stato risolto. Esaminare la situazione e intraprendere alcuna azione necessari per cancellare il problema.

- **Informazioni** di questo avviso contiene informazioni che possono essere utile per rilevamento e gestione del sistema.

## <a name="view-and-track-alerts"></a>Visualizzare e registrare gli avvisi

Dashboard di servizio di gestione StorSimple offre una rapida occhiata il numero di avvisi nei dispositivi virtuali elementi disposti per gravità.

![Dashboard di avvisi](./media/storsimple-ova-manage-alerts/alerts6.png)

Scegliere il livello di gravità verrà visualizzata la scheda **avvisi** . I risultati includono solo gli avvisi che corrispondono a tale livello di gravità.

![Report di avvisi per tipo di avviso](./media/storsimple-ova-manage-alerts/alerts7.png)

Facendo clic su un avviso nell'elenco vengono fornite informazioni dettagliate per l'avviso, tra cui l'ora dell'ultima l'avviso è stato segnalato, il numero di occorrenze di un avviso sul dispositivo e l'azione consigliata per risolvere l'avviso di.

Se è necessario inviare le informazioni relative al supporto Microsoft, è possibile copiare i dettagli degli avvisi per un file di testo. Dopo aver seguito il suggerimento e risolvere la condizione di avviso locale, è consigliabile deselezionare l'avviso da selezionare l'avviso nella scheda **avvisi** e facendo clic su **Cancella**. Per cancellare più avvisi, selezionare ogni avviso, fare clic su qualsiasi colonna ad eccezione della colonna di **avviso** e quindi fare clic su **Cancella** dopo aver selezionato tutti gli avvisi da cancellare. Si noti che alcuni avvisi siano deselezionate automaticamente quando il problema viene risolto o il sistema aggiorna l'avviso con le nuove informazioni.

Quando si fa clic su **Cancella**, si avrà l'opportunità di fornire commenti relativi all'avviso e i passaggi che è stata eseguita per risolvere il problema. 

![commenti degli avvisi](./media/storsimple-ova-manage-alerts/clear-alert.png)

Fare clic sull'icona di controllo ![icona di controllo](./media/storsimple-ova-manage-alerts/check-icon.png) Per salvare i commenti.

Alcuni eventi verranno eliminati dal sistema se un altro evento attivato con nuove informazioni. In questo caso, si verrà visualizzato il messaggio seguente.

![Messaggio di avviso deselezionare](./media/storsimple-ova-manage-alerts/alerts8.png)

## <a name="sort-and-review-alerts"></a>Ordinamento e revisione avvisi

Sulla scheda **avvisi** è possibile visualizzare gli avvisi fino a 250. Se è stato superato il numero di avvisi, non tutti gli avvisi verranno visualizzati nella visualizzazione predefinita. È possibile combinare i campi seguenti per personalizzare gli avvisi di visualizzazione:

- **Stato** : È possibile visualizzare gli avvisi **attivo** o **deselezionata** . Avvisi attivi vengono comunque attivati nel sistema, mentre gli avvisi deselezionati sono stati cancellati manualmente da un amministratore o a livello di programmazione deselezionati perché il sistema aggiornato la condizione dell'avviso con le nuove informazioni.

- **Gravità** – è possibile visualizzare gli avvisi di tutti i livelli di gravità (informazioni critiche, avvisi,) o solo determinati gravità, ad esempio avvisi solo critici.

- **Origine** – è possibile visualizzare gli avvisi da tutte le fonti o limitare gli avvisi per quelli forniti dal servizio o uno o tutti i dispositivi virtuali.

- **Intervallo di tempo** , specificando le date **da** e **a** e timestamp, è possibile esaminare gli avvisi durante il periodo di tempo che si è interessati.

## <a name="alerts-quick-reference"></a>Guida di riferimento rapido gli avvisi

Le tabelle seguenti elencano alcuni degli avvisi di Microsoft Azure StorSimple che possono verificarsi, nonché suggerimenti e informazioni aggiuntive se disponibili. Gli avvisi di dispositivo virtuale StorSimple rientrano in una delle categorie seguenti:

- [Avvisi di connettività cloud](#cloud-connectivity-alerts)

- [Avvisi di configurazione](#configuration-alerts)

- [Avvisi di errore di processo](#job-failure-alerts)

- [Avvisi di prestazioni](#performance-alerts)

- [Avvisi di sicurezza](#security-alerts)

- [Aggiornare gli avvisi](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Avvisi di connettività cloud

|Testo dell'avviso|Evento|Ulteriori informazioni / azioni consigliate|
|:---|:---|:---|
|Dispositivo *<device name>* non è connessa al cloud.|Il dispositivo denominato non riesce a connettersi al cloud. |Impossibile connettersi al cloud. Ciò potrebbe essere dovuto a una delle operazioni seguenti:<ul><li>Potrebbe essersi verificato un problema con le impostazioni di rete nel dispositivo.</li><li>Potrebbe essersi verificato un problema con le credenziali dell'account di archiviazione.</li></ul>Per ulteriori informazioni sulla risoluzione dei problemi di connettività, passare al [web locale dell'interfaccia utente](storsimple-ova-web-ui-admin.md) del dispositivo.|


### <a name="configuration-alerts"></a>Avvisi di configurazione

|Testo dell'avviso|Evento|Ulteriori informazioni / azioni consigliate|
|:---|:---|:---|
|Configurazione di dispositivo virtuale locale non supportata.|Lentezza nelle prestazioni.|Riduzione delle prestazioni potrebbe causare la configurazione corrente. Assicurarsi che il server soddisfi i requisiti minimi di configurazione. Per ulteriori informazioni, passare ai [Requisiti di StorSimple virtuale in forma di matrice](storsimple-ova-system-requirements.md). 
|Sono quasi esaurito spazio su disco provisioning in <*nome del dispositivo*>.|Avviso di spazio su disco.|Spazio su disco provisioning insufficiente. Per liberare spazio, provare a spostare carichi di lavoro a un altro volume o la condivisione o eliminazione di dati.

### <a name="job-failure-alerts"></a>Avvisi di errore di processo

|Testo dell'avviso|Evento|Ulteriori informazioni / azioni consigliate|
|:---|:---|:---|
|Backup di <*nome del dispositivo*> non è stato completato.|Errore di processo di backup.|Impossibile creare una copia di backup. È possibile utilizzare una delle operazioni seguenti:<ul><li>Problemi di connettività potrebbero impedire l'operazione di backup da completato. Assicurarsi che non siano non sono presenti problemi di connettività. Per ulteriori informazioni sulla risoluzione dei problemi di connettività, passare al [web locale dell'interfaccia utente](storsimple-ova-web-ui-admin.md) per il dispositivo virtuale.</li><li>È stato raggiunto il limite di archiviazione disponibile. Per liberare spazio, è consigliabile eliminare tutti i backup che non sono più necessari.</li></ul> Risolvere i problemi, deselezionare l'avviso e ripetere l'operazione.|
|Ripristino di <*nome del dispositivo*> non è stato completato.|Ripristinare un errore del processo.|Impossibile ripristinare dal backup. È possibile utilizzare una delle operazioni seguenti:<ul><li>Elenco di backup potrebbe non essere valido. Aggiornare l'elenco per verificare che sia ancora valido.</li><li>Problemi di connettività potrebbero impedire l'operazione di ripristino da completato. Assicurarsi che non siano non sono presenti problemi di connettività.</li><li>È stato raggiunto il limite di archiviazione disponibile. Per liberare spazio, è consigliabile eliminare tutti i backup che non sono più necessari.</li></ul>Risolvere i problemi, deselezionare l'avviso e ripetere l'operazione di ripristino.|
|Non è stato completato duplicato di <*nome del dispositivo*>.|Duplicare errore del processo.|Impossibile creare un duplicato. È possibile utilizzare una delle operazioni seguenti:<ul><li>Elenco di backup potrebbe non essere valido. Aggiornare l'elenco per verificare che sia ancora valido.</li><li>Problemi di connettività potrebbero impedire l'operazione Clona da completato. Assicurarsi che non siano non sono presenti problemi di connettività.</li><li>È stato raggiunto il limite di archiviazione disponibile. Per liberare spazio, è consigliabile eliminare tutti i backup che non sono più necessari.</li></ul>Risolvere i problemi, deselezionare l'avviso e ripetere l'operazione.|

### <a name="performance-alerts"></a>Avvisi di prestazioni

|Testo dell'avviso|Evento|Ulteriori informazioni / azioni consigliate|
|:---|:---|:---|
|Si verificano ritardi imprevisti in trasferimenti di dati.|Trasferimento dati lento.|Quando si superano le destinazioni scalabilità del servizio di archiviazione, si verificano errori di limitazioni. Il servizio di archiviazione responsabile per garantire che non solo client o tenant può usare il servizio rispetto alle altre. Per ulteriori informazioni sulla risoluzione dei problemi account Azure dello spazio di archiviazione, passare a [Monitor, diagnosticare e risolvere i problemi di spazio di archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).
|La caratteristica di prenotazione locale spazio su disco <*nome del dispositivo*> insufficiente.|Tempo di risposta.|10% della dimensione del provisioning totale per <*nome del dispositivo*> riservato nel dispositivo locale e ora su insufficiente spazio riservato. Il carico di lavoro <*nome del dispositivo*> genera una maggiore percentuale di varianza o la potrebbe recente migrazione una grande quantità di dati. Questo può comportare prestazioni ridotte. È possibile utilizzare una delle operazioni seguenti per risolvere il problema:<ul><li>Aumentare la larghezza di banda cloud al dispositivo.</li><li>Ridurre o spostare carichi di lavoro in un altro volume o la condivisione.</li></ul>

### <a name="security-alerts"></a>Avvisi di sicurezza

|Testo dell'avviso|Evento|Ulteriori informazioni / azioni consigliate|
|:---|:---|:---|
|Password per <*nome del dispositivo*> scadrà <*numero*> giorni.|Avviso di password.| La password scadrà tra < numero < giorni. Valutare la possibilità di modificare la password. Per ulteriori informazioni, passare alla sezione [Modifica la password di amministratore del dispositivo StorSimple matrice virtuale](storsimple-ova-change-device-admin-password.md).

### <a name="update-alerts"></a>Aggiornare gli avvisi

|Testo dell'avviso|Evento|Ulteriori informazioni / azioni consigliate|
|:---|:---|:---|
|Nuovi aggiornamenti disponibili per il dispositivo.|Sono disponibili aggiornamenti per la matrice virtuale StorSimple.|Nella pagina **manutenzione** , è possibile installare nuovi aggiornamenti.|
|Impossibile non cercare nuovi aggiornamenti <*nome del dispositivo*>.|Aggiornare l'errore. |Si è verificato un errore durante l'installazione di nuovi aggiornamenti. È possibile installare manualmente gli aggiornamenti. Se il problema persiste, contattare il [Supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).|


## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulle StorSimple virtuale matrice](storsimple-ova-overview.md).


