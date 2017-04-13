<properties 
   pageTitle="Automatizzare DR per condivisioni file StorSimple utilizzando il ripristino del sito di Azure | Microsoft Azure"
   description="Vengono illustrati i passaggi e procedure consigliate per la creazione di una soluzione di ripristino di emergenza per condivisioni file ospitati in StorSimple lo spazio di archiviazione."
   services="storsimple"
   documentationCenter="NA"
   authors="vidarmsft"
   manager="syadav"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/16/2016"
   ms.author="vidarmsft" />

# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Soluzione di emergenza automatica utilizzando il ripristino del sito di Azure per condivisioni file ospitate in StorSimple

## <a name="overview"></a>Panoramica

Microsoft Azure StorSimple è una soluzione di archiviazione cloud ibrida che risolve le complessità di dati non strutturati comunemente associate a condivisioni file. StorSimple utilizza lo spazio di archiviazione cloud come un'estensione della soluzione locale e automaticamente i dati di livelli in più spazio di archiviazione locale e lo spazio di archiviazione cloud. Integrati protezione dei dati, locali e cloud istantanee, Elimina la necessità di un'infrastruttura di archiviazione difficili.

[Il ripristino del sito di Azure](../site-recovery/site-recovery-overview.md) è un servizio basato su Azure che funzionalità di emergenza ripristino () tramite coordinazione replica, failover e ripristino di macchine virtuali. Il ripristino del sito Azure supporta un numero di tecnologie di replica in modo coerente replicare, proteggere e diretta esito negativo su macchine virtuali e alle applicazioni di nuvole pubblico/privato o ospitate.

Usa il ripristino del sito di Azure, replica macchina virtuale e funzionalità snapshot cloud StorSimple, è possibile proteggere l'ambiente di server completo del file. In caso di interruzione, è possibile utilizzare un singolo clic per visualizzare le condivisioni file online in Azure in pochi minuti.

In questo documento viene illustrato dettagliatamente come è possibile creare una soluzione di ripristino di emergenza per le condivisioni file ospitate nel sistema di archiviazione StorSimple ed eseguire pianificata e non pianificata e testare failover utilizzando un piano di ripristino di un solo clic. In pratica, viene illustrato come è possibile modificare il piano di ripristino nell'archivio il ripristino del sito di Azure attivare StorSimple failover durante scenari di emergenza. Inoltre, descrive configurazioni supportate e i prerequisiti. In questo documento si presuppone che si ha familiarità con le nozioni di base di architetture StorSimple e il ripristino del sito di Azure.

## <a name="supported-azure-site-recovery-deployment-options"></a>Opzioni di distribuzione supportate il ripristino del sito di Azure

I clienti possono distribuire file server come server fisici o macchine () in esecuzione su Hyper-V o VMware e creare condivisioni file dai volumi separati dalla StorSimple lo spazio di archiviazione. Azure il ripristino del sito è possibile proteggere distribuzioni fisiche e virtuali a uno dei due un sito secondario o su Azure. I dettagli di una soluzione DR con Azure del sito di ripristino per un file server che macchine Virtuali ospitati in Hyper-V e nelle condivisioni file allo spazio di memorizzazione StorSimple illustrati in questo documento. Altri scenari in cui il file server macchine Virtuali si trova in una VM VMware o un computer fisico possono essere implementate in modo analogo.

## <a name="prerequisites"></a>Prerequisiti

Implementazione di una soluzione di ripristino di emergenza fare clic su uno che utilizza il ripristino del sito di Azure per condivisioni file ospitate in StorSimple lo spazio di archiviazione sono previsti i prerequisiti seguenti:

-   Server di Windows Server 2012 R2 File che macchine Virtuali ospitato su Hyper-V o VMware o un computer locale

-   StorSimple archiviazione dispositivo locali registrate con Azure StorSimple manager

-   Accessorio Cloud StorSimple creati in gestore StorSimple Azure (questa operazione può essere mantenuta arresto stato)

-   Nelle condivisioni file ospitate in volumi configurati con il dispositivo StorSimple

-   [Archivio di servizi il ripristino del sito di azure](../site-recovery/site-recovery-vmm-to-vmm.md) creato in un abbonamento a Microsoft Azure

Inoltre, se Azure è il sito di ripristino, eseguire lo [strumento di valutazione di Azure macchina virtuale per la preparazione per](http://azure.microsoft.com/downloads/vm-readiness-assessment/) in macchine virtuali per garantire che siano compatibili con i servizi di macchine virtuali di Azure e il ripristino del sito di Azure.

Per evitare la latenza problemi (questo possono comportare costi più elevati), assicurarsi di creare il StorSimple Cloud accessorio, account di automazione e lo spazio di archiviazione degli account nella stessa regione.

## <a name="enable-dr-for-storsimple-file-shares"></a>Abilitare DR per condivisioni file StorSimple  

Ogni componente dell'ambiente locale deve essere protetto per consentire il ripristino e completare la replica. In questa sezione viene descritto come:

-   Impostare la replica di Active Directory e DNS (facoltativo)

-   Usare il ripristino del sito di Azure per consentire la protezione del file server macchine Virtuali

-   Attivare la protezione dei volumi StorSimple

-   Configurare la rete

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Impostare la replica di Active Directory e DNS (facoltativo)

Se si desidera proteggere il computer che eseguono Active Directory e DNS in modo che siano disponibili nel sito DR, è necessario proteggerle in modo esplicito (in modo che i file server sono accessibili dopo fail su con l'autenticazione). Sono disponibili due opzioni consigliate in base alla complessità dell'ambiente locale del cliente.

#### <a name="option-1"></a>Opzione 1

Se si dispone di un numero limitato di applicazioni, un controller di dominio per l'intero sito locale e verrà non funziona sull'intero sito, quindi è consigliabile usare replica il ripristino del sito di Azure replicare macchina controller di dominio in un sito secondario (questa opzione è disponibile per a siti e siti di Azure).

#### <a name="option-2"></a>Opzione 2

Se il cliente è un numero elevato di applicazioni, sia in esecuzione un insieme di strutture di Active Directory e su alcune applicazioni non dalla funziona alla volta, quindi è consigliabile la configurazione di un controller di dominio nel sito DR (è possibile che un sito secondario o in Azure).

Consultare soluzione [automatizzato DR per Active Directory e DNS utilizzando il ripristino del sito di Azure](../site-recovery/site-recovery-active-directory.md) per ottenere istruzioni durante la creazione di un controller di dominio disponibile nel sito di DR. Per il resto di questo documento, si supponga di che un controller di dominio è disponibile nel sito di DR.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Usare il ripristino del sito di Azure per consentire la protezione del file server macchine Virtuali

In questo passaggio verrà preparare l'ambiente locale file server, creare e preparare un archivio il ripristino del sito di Azure e Abilita protezione di file della macchina virtuale.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Per preparare l'ambiente di server file locale

1.  Impostare il **controllo dell'Account utente** per **non ricevere una notifica**. Questa operazione è necessaria in modo che è possibile utilizzare gli script di automazione Azure per connettere le destinazioni iSCSI dopo fail sovrapposto il ripristino del sito di Azure.

    1.  Premere il tasto Windows + Q e cercare **controllo dell'account utente**.

    2.  Selezionare **impostazioni di controllo Account utente di modifica**.

    3.  Trascinare la barra verso **Notificare mai**.

    4.  Fare clic su **OK** e quindi selezionare **Sì** quando richiesto.

        ![](./media/storsimple-dr-using-asr/image1.png)

1.  Installare l'agente di macchine Virtuali su ciascuno dei file server macchine virtuali. Questa operazione è necessaria in modo che possono eseguire script di automazione Azure il volume su macchine virtuali.

    1.  [Scaricare l'agente](http://aka.ms/vmagentwin) `C:\\Users\\<username>\\Downloads`.

    2.  Aprire Windows PowerShell in modalità di amministratore (Esegui come amministratore) e quindi immettere il seguente comando per passare al percorso di download:

        `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

        > [AZURE.NOTE] A seconda della versione può cambiare il nome del file.

1.  Fare clic su **Avanti**.

2.  Accettare i **Termini del contratto** e quindi fare clic su **Avanti**.

3.  Fare clic su **Fine**.


1.  Creare cartelle condivise con volumi separati dalla StorSimple lo spazio di archiviazione. Per ulteriori informazioni, vedere [usare il servizio di gestione di StorSimple per gestire i volumi](storsimple-manage-volumes.md).

    1.  Nelle macchine virtuali locale, premere il tasto Windows + Q e cercare **iSCSI**.

    2.  Selezionare **iniziatore**.

    3.  Selezionare la scheda **configurazione** e copiare il nome dell'iniziatore.

    4.  Accedere al [portale classica Azure](https://manage.windowsazure.com/).

    5.  Selezionare la scheda **StorSimple** e quindi selezionare il servizio di gestione StorSimple che contiene il dispositivo fisico.

    6.  Creare contenitori volume e quindi creare volumi. (Questi volumi sono per la condivisione di file in file server macchine virtuali). Copiare il nome dell'iniziatore e assegnare un nome appropriato per i record di controllo di accesso quando si creano i volumi.

    7.  Selezionare la scheda **Configura** e Nota verso il basso l'indirizzo IP del dispositivo.

    8.  Nelle macchine virtuali locale, passare di nuovo alla **iniziatore** e immettere l'indirizzo IP nella sezione connessione rapida. Fare clic su **Connessione rapida** (il dispositivo deve essere connesso).

    9.  Aprire il portale di gestione di Azure e selezionare la scheda **volumi e dispositivi** . Fare clic su **configurazione automatica**. Deve essere visualizzato il volume appena creata.

    10. Nel portale di fare clic sulla scheda **dispositivi** e quindi selezionare **creare un nuovo dispositivo virtuale.** (Verrà utilizzato il dispositivo virtuale in caso di errore caso). La nuova periferica virtuale può trovarsi in uno stato offline per evitare i costi aggiuntivi. Per disconnettere il dispositivo virtuale, passare alla sezione **macchine virtuali** del portale e chiuderlo.

    11. Tornare in macchine virtuali locale e aprire Gestione disco (premere il tasto Windows + X e selezionare **Gestione disco**).

    12. Si noterà alcuni dischi aggiuntive (a seconda del numero di volumi creati). Pulsante destro del mouse al primo, selezionare **Inizializzare disco**e fare clic su **OK**. Rapida della sezione **Unallocated** , selezionare **Nuovo Volume semplice**, assegnare una lettera e completare la procedura guidata.

    13. Ripetere il passaggio l per tutti i dischi. È ora possibile visualizzare tutti i dischi in **Questo PC** in Esplora risorse.

    14. Utilizzare il ruolo Servizi File e lo spazio di archiviazione per creare condivisioni file su questi volumi.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Per creare e preparare un archivio il ripristino del sito di Azure

Fare riferimento alla [documentazione per il ripristino del sito di Azure](../site-recovery/site-recovery-hyper-v-site-to-azure.md) per iniziare a utilizzare il ripristino del sito di Azure prima di proteggere il file server macchine Virtuali.

#### <a name="to-enable-protection"></a>Per attivare la protezione

1.  Disconnettersi target iSCSI da macchine virtuali locale che si desidera proteggere tramite il ripristino del sito di Azure:

    1.  Premere il tasto Windows + Q e cercare **iSCSI**.

    2.  Selezionare **l'impostazione iniziatore**.

    3.  Scollegare il dispositivo StorSimple che si è connessi in precedenza. In alternativa, è possibile passare disattivare il file server per alcuni minuti quando si abilita la protezione.

    > [AZURE.NOTE] In questo modo, condivisioni file per essere temporaneamente non disponibile

1.  [Attivare la protezione macchina virtuale](../site-recovery/site-recovery-hyper-v-site-to-azure.md##step-6-enable-replication) del file server macchine Virtuali dal portale del ripristino del sito di Azure.

2.  Quando viene avviata la sincronizzazione iniziale, è possibile riconnettersi nuovamente la destinazione. Passare all'iniziatore, selezionare il dispositivo StorSimple e fare clic su **Connetti**.

3.  Quando è stata completata la sincronizzazione e lo stato della macchina virtuale è **protetta**, selezionare la macchina virtuale, selezionare la scheda **Configura** e aggiornare la rete della macchina virtuale di conseguenza (si tratta della rete non riuscito su VM(s) faranno parte della). Se la rete non è visibile, significa che la sincronizzazione è ancora il problema.

### <a name="enable-protection-of-storsimple-volumes"></a>Attivare la protezione dei volumi StorSimple

Se non è stata selezionata l'opzione **attiva una copia di backup predefinito per il volume** per i volumi StorSimple, passare ai **Criteri di Backup** del servizio di gestione StorSimple e creare un criterio di backup adatto per tutti i volumi. È consigliabile impostare la frequenza di backup per l'obiettivo di punto di ripristino (rilasciato) che si desidera visualizzare per l'applicazione.

### <a name="configure-the-network"></a>Configurare la rete

Per il file server macchine Virtuali, configurare le impostazioni di rete nel ripristino del sito di Azure in modo che le reti macchine Virtuali connessi alla rete DR corretta dopo il failover.

È possibile selezionare la macchina virtuale nel **Cloud VMM** o il **Gruppo di protezione** per configurare le impostazioni di rete, come illustrato nella figura seguente.

![](./media/storsimple-dr-using-asr/image2.png)

## <a name="create-a-recovery-plan"></a>Creare un piano di ripristino

È possibile creare un piano di ripristino di ripristino automatico di sistema per automatizzare il processo di failover condivisioni file. Se si verifica un'interruzione, sarà possibile visualizzare le condivisioni file di qualche minuto con un semplice clic. Per abilitare questa automazione, è necessario un account di automazione Azure.

#### <a name="to-create-the-account"></a>Per creare l'account

1.  Accedere al portale di classica Azure e passare alla sezione **automazione** .

1.  Creare un nuovo account di automazione. Mantenere la stessa geografico o l'area geografica in cui sono stati creati i StorSimple Cloud accessorio e gli account di archiviazione.

2.  Fare clic su **Nuovo** &gt; **Servizi App** &gt; **automazione** &gt; **Runbook** &gt; **Dalla raccolta** per l'account di automazione importare tutti i runbook necessari.

    ![](./media/storsimple-dr-using-asr/image3.png)

1.  Aggiungere runbook seguenti dal riquadro di **Emergenza** nella raccolta:

    -   Esito negativo su contenitori volume StorSimple

    -   Eliminazione dei volumi StorSimple dopo Test Failover (TFO)

    -   Collegare volumi StorSimple dispositivo dopo il failover

    -   Avviare dispositivo virtuale StorSimple

    -   Disinstallare l'estensione di script personalizzati in macchine Virtuali di Azure

        ![](./media/storsimple-dr-using-asr/image4.png)


1.  Pubblicare tutti gli script selezionando dal runbook nell'account automazione e passare alla scheda **autore** . Dopo questo passaggio, la scheda **runbook** viene visualizzata come segue:

     ![](./media/storsimple-dr-using-asr/image5.png)

1.  Nella finestra account automazione fare clic sulla scheda **risorse** , fare clic su **Aggiungi impostazione** &gt; **Add Credential**e aggiungere le credenziali di Azure, assegnare un nome risorsa AzureCredential.

    Usare le credenziali di Windows PowerShell. Deve trattarsi di credenziali che contiene un nome utente ID Org e una password con accesso all'abbonamento Azure e con autenticazione a più fattori disabilitato. È necessario eseguire l'autenticazione per conto dell'utente durante il failover e per visualizzare i volumi server file nel sito di DR.

1.  Nella finestra account di automazione, selezionare la scheda **attività** e quindi fare clic su **Aggiungi impostazione** &gt; **Aggiungi variabile** e aggiungere le seguenti variabili. È possibile scegliere di crittografare queste risorse. Queste variabili sono ripristino specifici di piano. Se il ripristino prevede (che verrà creato nel passaggio successivo) nome è piano di verifica, quindi le variabili dovrebbero essere StorSimRegKey piano di verifica, piano di verifica AzureSubscriptionName e così via.

    -   *RecoveryPlanName* **-StorSimRegKey**: la chiave di registrazione per il servizio di gestione StorSimple.

    -   *RecoveryPlanName* **-AzureSubscriptionName**: il nome della sottoscrizione Azure.

    -   *RecoveryPlanName* **-ResourceName**: il nome della risorsa StorSimple che contiene il dispositivo StorSimple.

    -   *RecoveryPlanName* **-DeviceName**: il dispositivo che contiene un errore.

    -   *RecoveryPlanName* **-TargetDeviceName**: il StorSimple Cloud accessorio in cui i contenitori sono un errore.

    -   *RecoveryPlanName* **-VolumeContainers**: stringa separati da virgola di contenitori volume presenti nel dispositivo che richiedono un errore; ad esempio, volcon1, volcon2, volcon3.

    -   RecoveryPlanName**-TargetDeviceDnsName**: il nome del dispositivo di destinazione (disponibili nella sezione **macchina virtuale** : il nome del servizio è lo stesso nome DNS).

    -   *RecoveryPlanName* **-StorageAccountName**: il nome dell'account di archiviazione in cui verrà archiviato lo script (che deve essere eseguito sul volume su macchine Virtuali). Può trattarsi di tutti gli account lo spazio di archiviazione che dispone di spazio per archiviare temporaneamente lo script.

    -   *RecoveryPlanName* **-StorageAccountKey**: il tasto di scelta per l'account di archiviazione precedente.

    -   *RecoveryPlanName* **-ScriptContainer**: il nome del contenitore in cui lo script verrà archiviato nel cloud. Se non è presente il contenitore, verrà creato.

    -   *RecoveryPlanName* **-VMGUIDS**: su protezione una macchina virtuale, il ripristino del sito di Azure assegna ogni macchina virtuale un ID univoco che forniscono i dettagli su macchine Virtuali. Per ottenere il VMGUID, selezionare la scheda **Servizi di recupero** e quindi fare clic su **Elementi protetti** &gt; **Gruppi di protezione** &gt; **macchine** &gt; **proprietà**. Se si dispone di più macchine virtuali, aggiungere i GUID come stringa separati da virgola.

    -   *RecoveryPlanName* **-AutomationAccountName** : il nome dell'account di automazione in cui è stato aggiunto il runbook e le risorse.

    Ad esempio, se il nome del piano di ripristino fileServerpredayRP, quindi sulla scheda **risorse** dovrebbero essere visualizzate come indicato di seguito dopo aver aggiunto tutte le risorse.

    ![](./media/storsimple-dr-using-asr/image6.png)


1.  Passare alla sezione dei **Servizi di recupero** e selezionare l'archivio il ripristino del sito di Azure creata in precedenza.

2.  Selezionare la scheda **Ripristino plan di messaggistica unificata** e creare un nuovo piano di ripristino come indicato di seguito:

    un.  Specificare un nome e selezionare il **Gruppo di protezione**appropriato.

    b.  Selezionare le macchine virtuali dal gruppo di protezione che si desidera includere nel piano del ripristino.

    c.  Dopo il ripristino viene creato piano, selezionarla per aprire la visualizzazione di personalizzazione di piano di ripristino.

    d.  Selezionare **tutti arresto gruppi**, fare clic su **Script**e scegliere **Aggiungi uno script sul lato principale prima della chiusura gruppo tutti**.

    e.  Selezionare l'account di automazione (in cui è stata aggiunta la runbook) e quindi selezionare runbook **esito negativo contenitori Volume di StorSimple sopra** .

    f.  Fare clic su **gruppo 1: avviare**, scegliere **macchine virtuali di**e aggiungere le macchine virtuali che devono essere protetti nel piano del ripristino.

    g.  Fare clic su **gruppo 1: avviare**, scegliere **Script**e aggiungere tutti gli script seguenti nell'ordine in due fasi **dopo 1 gruppo** .

    - Inizio-StorSimple--dispositivo virtuale runbook
    - Esito negativo runbook contenitori volume di StorSimple sopra
    - Montaggio volumi dopo failover runbook
    - Disinstallare personalizzata-script-estensione runbook

1.  Aggiungere un'azione manuale dopo gli script di 4 indicati nella stessa **gruppo 1: post-procedura** sezione. Questa azione è il punto in cui è possibile verificare che tutto funzioni correttamente. Questa azione deve essere aggiunto solo come parte del test failover (pertanto solo selezionare **Test Failover** casella di controllo).

2.  Dopo l'azione manuale, aggiungere lo script di pulizia utilizzando la stessa procedura utilizzata per altri runbook. Salvare il piano di ripristino.

    > [AZURE.NOTE] Durante l'esecuzione di test caso di errore, è necessario verificare tutti gli elementi durante il passaggio manuale azione i volumi StorSimple duplicati sul dispositivo vengono eliminati durante la pulizia al termine dell'azione manuale.

    ![](./media/storsimple-dr-using-asr/image7.png)

## <a name="perform-a-test-failover"></a>Eseguire test caso di errore

Fare riferimento alla guida complementare [Soluzione Active Directory DR](../site-recovery/site-recovery-active-directory.md) per considerazioni specifiche per Active Directory durante il failover test. Il programma di installazione locale non verrà modificata tutto in caso di failover test. I volumi StorSimple collegati a macchina virtuale locale vengono duplicati all'accessorio Cloud StorSimple in Azure. Viene visualizzata una macchina virtuale a scopo di testing in Azure e i volumi duplicati sono collegati alle macchina virtuale.

#### <a name="to-perform-the-test-failover"></a>Per eseguire il test failover

1.  Nel portale di Azure classico selezionare l'archivio di ripristino del sito.

1.  Fare clic sul piano di ripristino creato per il file server macchine Virtuali.

2.  Fare clic su **Test Failover**.

3.  Selezionare la rete virtuale per avviare il processo di failover test.

    ![](./media/storsimple-dr-using-asr/image8.png)

1.  Quando l'ambiente secondario è attivata, è possibile eseguire la convalida.

2.  Dopo aver completata la convalida, fare clic su **Convalida completata**. L'ambiente di testing failover verrà eliminato e l'operazione TFO dovrà essere completata.

## <a name="perform-an-unplanned-failover"></a>Eseguire un failover non pianificato

Durante un failover non pianificato volumi StorSimple non riusciti sul dispositivo virtuale, una replica macchine Virtuali essere visualizzata in Azure e i volumi sono collegati alle macchina virtuale.

#### <a name="to-perform-an-unplanned-failover"></a>Per eseguire un failover non pianificato

1.  Nel portale di Azure classico selezionare l'archivio di ripristino del sito.

1.  Fare clic sul piano di ripristino creato per file server macchine Virtuali.

2.  Fare clic su **Failover** e quindi selezionare **Failover non pianificato**.

    ![](./media/storsimple-dr-using-asr/image9.png)

1.  Selezionare la rete di destinazione e quindi fare clic sull'icona di controllo ✓ per avviare il processo di failover.

## <a name="perform-a-planned-failover"></a>Eseguire pianificato in caso di errore

Durante il pianificati in caso di errore, locale nel file server che chiusura normale di macchine Virtuali e un cloud backup dei volumi sul dispositivo StorSimple istantanea. I volumi StorSimple non riusciti sul dispositivo virtuale, una replica macchine Virtuali è visualizzata in Azure e i volumi sono collegati alle macchina virtuale.

#### <a name="to-perform-a-planned-failover"></a>Per eseguire pianificato in caso di errore

1.  Nel portale di Azure classico selezionare l'archivio di ripristino del sito.

1.  Fare clic sul piano di ripristino creato per il file server macchine Virtuali.

2.  Fare clic su **Failover** e quindi selezionare **Failover pianificato**.

3.  Selezionare la rete di destinazione e quindi fare clic sull'icona di controllo ✓ per avviare il processo di failover.

## <a name="perform-a-failback"></a>Eseguire un failback

Durante l'opzione, contenitori volume StorSimple vengono eseguiti su dispositivo fisico dopo viene accettata una copia di backup.

#### <a name="to-perform-a-failback"></a>Per eseguire un failback

1.  Nel portale di Azure classico selezionare l'archivio di ripristino del sito.

1.  Fare clic sul piano di ripristino creato per il file server macchine Virtuali.

2.  Fare clic su **Failover** e selezionare **failover pianificata** o **failover non pianificato**.

3.  Fare clic su **Cambia orientamento**.

4.  Selezionare la sincronizzazione di dati appropriato e opzioni per la creazione di macchine Virtuali.

5.  Fare clic sull'icona di controllo ✓ per avviare il processo di failback.

    ![](./media/storsimple-dr-using-asr/image10.png)

## <a name="best-practices"></a>Procedure consigliate

### <a name="capacity-planning-and-readiness-assessment"></a>Valutazione della capacità di pianificazione e preparazione


#### <a name="hyper-v-site"></a>Sito di Hyper-V

Usare lo [strumento di pianificazione capacità utente](http://www.microsoft.com/download/details.aspx?id=39057) per progettare il server, lo spazio di archiviazione e infrastruttura di rete per l'ambiente di replica Hyper-V.

#### <a name="azure"></a>Azure

È possibile eseguire lo [strumento di valutazione di Azure macchina virtuale](http://azure.microsoft.com/downloads/vm-readiness-assessment/) in macchine virtuali per garantire che siano compatibili con macchine virtuali di Azure e servizi di recupero del sito di Windows Azure. Lo strumento di verifica per la preparazione per controlla configurazioni macchine Virtuali e un avviso quando le configurazioni non sono compatibili con Azure. Ad esempio, viene emesso un avviso se un'unità c: è maggiore di 127 GB.


Pianificazione della capacità è costituita da almeno due processi importanti:

-   Mapping locale macchine virtuali di Hyper-V alle dimensioni delle macchine Virtuali di Azure (ad esempio A6, A7, A8 e A9).

-   Determinare la larghezza di banda Internet necessario.

## <a name="limitations"></a>Limitazioni

- Al momento solo 1 StorSimple dispositivo possa essere non è riuscita sopra (un unico accessorio Cloud StorSimple). Lo scenario di un file server che si estende su più dispositivi StorSimple non è ancora supportato.

- Se viene visualizzato un messaggio di errore durante l'attivazione di protezione per una macchina virtuale, assicurarsi che è stato scollegato destinazioni iSCSI.

- Tutti i contenitori di volume raggruppate a causa di criteri di backup che si estendono su contenitori volume verranno eseguiti su insieme.

- Tutti i volumi contenitori volume scelto verranno eseguiti su.

- Volumi che consentono di raggiungere più di 64 TB non possono essere non riusciti su capacità massima di un unico accessorio Cloud StorSimple 64 TB.

- Se si verifica un errore di failover pianificato/non pianificato e macchine virtuali vengono create in Azure, quindi non ripulire macchine virtuali. Se, tuttavia, eseguire un failback. Se si elimina macchine virtuali quindi macchine virtuali locale non possono essere attivate nuovamente.

- Dopo aver caso di errore, se non si può vedere i volumi, passare alle macchine virtuali, aprire Gestione disco, l'azione e renderli disponibili online.

- In alcuni casi, le lettere di unità nel sito di emergenza potrebbero essere diverse dalle lettere in locale. In questo caso, sarà necessario risolvere manualmente il problema al termine di failover.

- Autenticazione a più fattori deve essere disattivata per le credenziali di Azure che viene immesso nella finestra account di automazione come una risorsa. Se questa autenticazione non è disabilitata, gli script non saranno possibile per l'esecuzione automatica e il piano di ripristino avrà esito negativo.

- Timeout del processo di failover: StorSimple di script scadrà se failover della contenitori volume richiede più tempo rispetto al limite del ripristino del sito di Azure per script (attualmente 120 minuti).

- Processo di backup timeout: script StorSimple il timeout se il backup di volumi richiede più tempo rispetto al limite del ripristino del sito di Azure per script (attualmente 120 minuti).
 
    > [AZURE.IMPORTANT] Eseguire il backup manuale dal portale di Azure e quindi eseguire di nuovo il piano di ripristino.

- Duplicare timeout del processo: script StorSimple il timeout se la duplicazione dei volumi richiede più tempo rispetto al limite del ripristino del sito di Azure per script (attualmente 120 minuti).

- Definire l'errore di sincronizzazione: StorSimple di script errori fuori che indica che il backup hanno avuto esito positivo anche se il backup ha esito positivo nel portale. Potrebbe essere dovuto per questo che ora del dispositivo StorSimple potrebbero non essere sincronizzato con l'ora corrente del fuso orario.
 
    > [AZURE.IMPORTANT] Sincronizzare l'ora del dispositivo con l'ora corrente del fuso orario.

- Errore di failover accessorio: StorSimple di script potrebbe non riuscire se esiste un failover accessorio quando viene eseguito il piano di ripristino.
    
    > [AZURE.IMPORTANT] Al termine failover accessorio, eseguire nuovamente il piano di ripristino.

## <a name="summary"></a>Riepilogo

Usa il ripristino del sito di Azure, è possibile creare un piano di ripristino di emergenza automatizzato completo per un file server macchine Virtuali verificano condivisioni file ospitate in StorSimple lo spazio di archiviazione. È possibile avviare il failover in pochi secondi da qualsiasi posizione in caso di un interruzioni e ottenere l'applicazione funzionanti in pochi minuti.
