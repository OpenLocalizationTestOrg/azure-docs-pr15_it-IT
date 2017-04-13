<properties 
   pageTitle="Programma di installazione server iSCSI matrice virtuale StorSimple | Microsoft Azure"
   description="In questo articolo viene descritto come eseguire l'installazione e configurazione iniziali, registrare il server di iSCSI StorSimple e completare la configurazione di dispositivi."
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
   ms.date="07/18/2016"
   ms.author="alkohli" />


# <a name="deploy-storsimple-virtual-array--set-up-your-virtual-device-as-an-iscsi-server"></a>Distribuire StorSimple virtuali in forma di matrice: configurare il dispositivo virtuale come server iSCSI

![flusso del processo di configurazione iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Panoramica

In questa esercitazione di distribuzione si applica alla versione di disponibilità generale (GA) marzo 2016 in esecuzione Microsoft Azure StorSimple virtuale in forma di matrice (noto anche come il dispositivo virtuale locale StorSimple o il dispositivo virtuale StorSimple). In questa esercitazione viene descritto come eseguire l'installazione e configurazione iniziali, registrare il server di iSCSI StorSimple, completare la configurazione di dispositivi e quindi creare, installare, inizializzare e formattare i volumi sul server iSCSI periferica virtuale StorSimple. Le informazioni di installazione StorSimple in questo articolo si applicano solo a matrici virtuali StorSimple. 

Le procedure descritte di seguito entro 30 minuti su 1 ora per completare. Le informazioni pubblicate in questo articolo si applicano solo a matrici virtuali StorSimple.

## <a name="setup-prerequisites"></a>Prerequisiti di installazione

Prima di configurare e configurare il dispositivo virtuale StorSimple, verificare quanto segue:

- Si dispone il provisioning di un dispositivo virtuale e connessi come descritto in [Distribuire StorSimple matrice virtuale - provisioning una matrice virtuale in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) o [Distribuire StorSimple matrice virtuale - provisioning una matrice virtuale in VMware](storsimple-ova-deploy2-provision-vmware.md).

- Si dispone della chiave di registrazione servizio dal servizio di gestione StorSimple creato per la gestione di dispositivi virtuali StorSimple. Per ulteriori informazioni, vedere **passaggio 2: ottenere il codice di registrazione servizio** [distribuire StorSimple virtuale](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key)matrice - preparare il portale.

- Se si tratta il dispositivo virtuale secondo o successivo che si sta registrando con un servizio di gestione StorSimple esistente, è necessario disporre della chiave di crittografia di servizio dati. Questa chiave generata quando il primo dispositivo è stato registrato con questo servizio. Se è stata persa questa chiave, vedere **ottenere la chiave di crittografia di dati di servizio** in [uso l'interfaccia utente Web per amministrare la matrice virtuale StorSimple](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Procedura di configurazione 

Utilizzare le seguenti istruzioni dettagliate per installare e configurare il dispositivo virtuale StorSimple:

-  [Passaggio 1: Completare la configurazione dell'interfaccia utente web locale e registrare il dispositivo](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
-  [Passaggio 2: Completare la configurazione di dispositivo](#step-2-complete-the-required-device-setup)
-  [Passaggio 3: Aggiungere un volume](#step-3-add-a-volume)
-  [Passaggio 4: Installare, inizializzare e formattare un volume](#step-4-mount-initialize-and-format-a-volume)  

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Passaggio 1: Completare la configurazione dell'interfaccia utente web locale e registrare il dispositivo 

#### <a name="to-complete-the-setup-and-register-the-device"></a>Per completare la configurazione e registrare il dispositivo

1. Aprire una finestra del browser e connettersi al web dell'interfaccia utente digitando:

    `https://<ip-address of network interface>`

    Utilizzare l'URL di connessione indicato nel passaggio precedente. Verrà visualizzato un messaggio di errore che indica che non esiste un problema con il certificato di sicurezza del sito Web. Fare clic su **continua a questa pagina web**.

    ![Errore di certificato di sicurezza](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)

2. Accedere a web dell'interfaccia utente del dispositivo virtuale come **StorSimpleAdmin**. Immettere la password di amministratore di dispositivo che è stata modificata nel passaggio 3: avviare il dispositivo virtuale in [Distribuire StorSimple matrice virtuale - disposizione un dispositivo virtuale in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) o [Distribuire StorSimple matrice virtuale - disposizione un dispositivo virtuale in VMware](storsimple-ova-deploy2-provision-vmware.md).

    ![Pagina di accesso](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)

3. Sarà accedere alla pagina **Home** . Questa pagina illustra le varie impostazioni necessarie per configurare e registrare il dispositivo virtuale con il servizio di gestione StorSimple. Si noti che **le impostazioni di rete**, **le impostazioni proxy Web**e **le impostazioni dell'ora** sono facoltativi. Le impostazioni necessarie sola sono **Impostazioni dispositivo** e **Cloud**.

    ![Home page](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)

4. Nella pagina **impostazioni di rete** in **interfacce di rete**dati 0 verranno configurati automaticamente dell'utente. Ogni interfaccia di rete per impostazione predefinita per ottenere un indirizzo IP automaticamente (DHCP). Di conseguenza, un indirizzo IP, subnet e gateway verrà assegnati automaticamente (per IPv4 e IPv6).

    Come si prevede di distribuire il dispositivo come un server iSCSI (a cui effettuare il provisioning di archiviazione a blocchi), è consigliabile disabilitare l'opzione **automaticamente un indirizzo IP di accedere** e configurare gli indirizzi IP statici.

    ![Pagina Impostazioni di rete](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)

    Se è stato aggiunto più di una rete durante il provisioning del dispositivo, è possibile configurare loro qui. Nota che è possibile configurare l'interfaccia di rete come IPv4 solo o come IPv4 e IPv6. IPv6 non sono supportate solo le configurazioni.

5. I server DNS sono necessari perché vengono utilizzate quando il dispositivo tenta di comunicare con il provider di servizi di archiviazione cloud o per risolvere il dispositivo in base al nome, se è configurato come un file server. Nella pagina **impostazioni di rete** in **server DNS**:

    1. Un server DNS primario e secondario verrà configurato automaticamente. Se si sceglie di configurare gli indirizzi IP statici, è possibile specificare i server DNS. Disponibilità elevata, è consigliabile configurare un primario e un server DNS secondario.

    2. Fare clic su **Applica**. Questa operazione verrà applicata e convalidare le impostazioni di rete.

6. Nella pagina **Impostazioni dispositivo** :

    1. Assegnare un **nome** univoco per il dispositivo. Questo nome può contenere caratteri di 1-15 e può contenere lettere, numeri e trattini.

    2. Fare clic sull'icona **server iSCSI** ![icona server iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image7.png) per il **tipo** di dispositivo che si sta creando. Un server iSCSI consente di spazio di archiviazione blocco.

    3. Specificare se si vuole che il dispositivo di dominio. Se il dispositivo è un server iSCSI, quindi si accede al dominio è facoltativo. Se si decide di non unire il server iSCSI a un dominio, fare clic su **Applica**, attendere che le impostazioni da applicare e quindi andare al passaggio successivo.

        Se si desidera aggiungere il dispositivo a un dominio. Immettere un **nome di dominio**e quindi fare clic su **Applica**.

        > [AZURE.NOTE] Per l'aggiunta del server iSCSI a un dominio, assicurarsi che la matrice virtuale nella propria unità organizzativa (OU) per Microsoft Azure Active Directory e nessun gruppo oggetti criteri () vengono applicati a tale.

    5. Verrà visualizzata una finestra di dialogo. Immettere le credenziali di dominio nel formato specificato. Fare clic sull'icona di controllo ![Selezionare l'icona](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Verranno verificate le credenziali di dominio. Se le credenziali sono corrette, si verrà visualizzato un messaggio di errore.

        ![credenziali](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)

    6. Fare clic su **Applica**. Questa operazione verrà applicata e convalidare le impostazioni del dispositivo.
 
7. Configurare, facoltativamente, il server di proxy web. Configurazione proxy web è facoltativa, tenere presente che se si utilizza un proxy web, è possibile solo configurarlo qui.

    ![configurare proxy web](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)

    Nella pagina **Web proxy** :

    1. Specificare l' **URL del proxy Web** in questo formato: *http://host-IP indirizzo* o *il numero di FDQN:Port*. Si noti che non sono supportati URL HTTPS.

    2. Specificare **l'autenticazione** di **base** o **Nessuno**.

    3. Se si utilizza l'autenticazione, sarà anche necessario specificare un **nome utente** e **Password**.

    4. Fare clic su **Applica**. Per convalidare e applicare le impostazioni del proxy web configurata.
 
8. Facoltativamente, configurare le impostazioni di ora del dispositivo, ad esempio fuso orario e il server NTP primario e secondario. Server NTP necessari perché il dispositivo deve sincronizzare l'ora in modo che possa autenticazione con il provider di servizi cloud.

    ![Impostazioni dell'ora](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)

    Nella pagina **impostazioni dell'ora** :

    1. Dall'elenco a discesa selezionare il **fuso orario** in base alla posizione geografica in cui si desidera distribuire il dispositivo sia installato. Il fuso orario predefinito per il dispositivo è PST. Il dispositivo utilizzerà il fuso orario per tutte le operazioni pianificate.

    2. Specificare un **server NTP primario** per il dispositivo oppure accettare il valore predefinito di time.windows.com. Assicurarsi che la rete consenta il traffico NTP passare dal centro dati a Internet.

    3. Se si desidera specificare un **server NTP secondario** del dispositivo.

    4. Fare clic su **Applica**. Per convalidare e applicare le impostazioni di tempo configurato.

9. Configurare le impostazioni di cloud per il dispositivo. In questo passaggio si completare la configurazione del dispositivo locale e quindi registrare il dispositivo con il servizio di gestione StorSimple.

    1. Immettere il **codice di registrazione di servizio** ottenuto in **passaggio 2: ottenere il codice di registrazione servizio** [distribuire StorSimple virtuale](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key)matrice - preparare il portale.

    2. Se il dispositivo prima che si sta registrando con questo servizio non, sarà necessario fornire la **chiave di crittografia di servizio dati**. Questa chiave è necessaria con la chiave di registrazione del servizio per registrare altri dispositivi con il servizio di gestione StorSimple. Per ulteriori informazioni, vedere per [ottenere la chiave di crittografia di servizio dati](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) del Web locale dell'interfaccia utente.

    3. Fare clic su **Registra**. Questa operazione verrà riavviare il dispositivo. Potrebbe essere necessario attendere 2-3 minuti prima che il dispositivo sia registrato correttamente. Dopo il riavvio il dispositivo, sarà accedere alla pagina di accesso.

       ![Registrare dispositivo](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)

10. Tornare al portale di classica Azure. Nella pagina **dispositivi** , verificare che il dispositivo è connesso al servizio cercando lo stato. Lo stato dispositivo è necessario che sia **Attiva**.

    ![Pagina dispositivi](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>Passaggio 2: Completare la configurazione di dispositivo

Per completare la configurazione del dispositivo del dispositivo StorSimple, è necessario:

- Selezionare un account di archiviazione da associare il dispositivo.

- Scegliere le impostazioni di crittografia per i dati inviati nel cloud.

Nel portale di classica Azure per completare la configurazione di dispositivo, procedere come segue.

#### <a name="to-complete-the-minimum-device-setup"></a>Per completare la configurazione di periferica minimi

1. Nella pagina **dispositivi** selezionare il dispositivo appena creata. Questo dispositivo verrebbe visualizzati come **attivo**. Fare clic sulla freccia accanto il nome del dispositivo e quindi fare clic su **Quick Start**.

    ![Pagina dispositivi](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)

2. Fare clic su **configurazione di dispositivi completo** per avviare la configurazione guidata dispositivo.

    ![Configurazione guidata dispositivo](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)

3. Nella procedura guidata dispositivo Configura nella pagina **Impostazioni di base** , eseguire le operazioni seguenti:

   1. Specificare un account di archiviazione per l'uso con il dispositivo. In questa sottoscrizione, è possibile selezionare un account di archiviazione esistente dall'elenco a discesa oppure è possibile specificare **Aggiungi altre** per scegliere un account da una sottoscrizione diversa.

   2. Definire le impostazioni di crittografia per tutti i dati inattivi che verranno inviati nel cloud. (StorSimple utilizza la crittografia AES 256). Per crittografare i dati, selezionare la casella di controllo **Abilita crittografia di archiviazione cloud** . Immettere una crittografia di spazio di archiviazione cloud di 32 caratteri. Immettere nuovamente il codice Product Key per confermarla.

   3. Fare clic sull'icona di controllo ![Selezionare l'icona](./media/storsimple-ova-deploy3-iscsi-setup/image15.png).

    ![Impostazioni di base](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)

    Le impostazioni verranno aggiornate. Dopo aggiornamento delle impostazioni correttamente, pulsante Impostazioni dispositivo completa non sarà disponibile. Si tornerà alla pagina **Guida introduttiva** di dispositivo.                                                        

>[AZURE.NOTE]È possibile modificare tutte le altre impostazioni dispositivo in qualsiasi momento, accedere alla pagina **Configura** .

## <a name="step-3-add-a-volume"></a>Passaggio 3: Aggiungere un volume

Nel portale di classica Azure per creare un volume, procedere come segue.

#### <a name="to-create-a-volume"></a>Per creare un volume

1. Nella pagina **Guida introduttiva** periferica, fare clic su **Aggiungi un volume**. Verrà avviata l'aggiunta guidata volume.

2. Nella casella Aggiungi una procedura guidata volume, in **Impostazioni di base**, eseguire le operazioni seguenti:

    1. Specificare un nome univoco per il volume. Il nome deve essere una stringa contenente i caratteri da 3 a 127.

    2. Fornire una descrizione per il volume. La descrizione consenta di identificare i proprietari di volume.

    3. Selezionare un tipo di utilizzo per il volume. Il tipo di utilizzo può essere **volume Tiered** o **localmente aggiunte volume.** (**Tiered volume** è l'impostazione predefinita). Per carichi di lavoro che richiedono garanzie locale, latenza bassa e prestazioni più elevate, selezionare **bloccato localmente** **volume**. Per tutti gli altri dati, selezionare **Tiered** **volume**.

        Un volume locale bloccato thickly viene completato il provisioning e garantisce che i dati principali del volume rimangono nel dispositivo e non soppressione nel cloud. Se si crea un volume locale bloccato, il dispositivo verificherà lo spazio disponibile su livelli locali per effettuare il provisioning di un volume di dimensione richiesta. Creazione di un volume locale bloccato possono richiedere venga dati esistenti dal dispositivo nel cloud e il tempo necessario per creare il volume potrebbe essere lungo. Il tempo totale dipende dalle dimensioni del volume di provisioning, la larghezza di banda di rete disponibili e i dati nel dispositivo.

        Un volume a più livelli invece eseguono viene completato il provisioning e può essere creato molto rapidamente. Quando si crea un volume a più livelli, viene eseguito il provisioning di circa il 10% dello spazio a livello locale e viene eseguito il provisioning di 90% dello spazio nel cloud. Ad esempio, se è stato effettuato il provisioning un volume di 1 TB, 100 GB risiede nello spazio locale e 900 GB da utilizzare nel cloud quando i livelli di dati. Ciò a sua implica che se esaurirsi tutto lo spazio locale nel dispositivo, non è possibile eseguire il provisioning di una condivisione a più livelli (perché 10% non saranno disponibili).

    4. Specificare la capacità di provisioning per il volume. Si noti che la capacità specificata deve essere inferiore alla capacità disponibile. Se si sta creando un volume a più livelli, la dimensione deve essere compreso tra 500 GB e TB 5. Per un volume locale bloccato, specificare la dimensione di volume tra 50 GB e 500 GB. Utilizzare la capacità disponibile come una Guida per il provisioning di un volume. Se la capacità locale disponibile è 0 GB, quindi non sarà possibile effettuare il provisioning di un locale bloccata o un volume a più livelli.

        ![Impostazioni di base](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)

    5. Fare clic sull'icona freccia ![icona freccia](./media/storsimple-ova-deploy3-iscsi-setup/image18.png) Per passare alla pagina successiva.

3. Nella pagina **Impostazioni aggiuntive** , aggiungere un nuovo record di controllo di accesso (ACR):

    1. Specificare un **nome** per il ACR.

    2. In **nome iniziatore iSCSI**, specificare la iSCSI completo nome iSCSI dell'host del Windows. Se non si dispone di IQN, passare a [Appendice r: Get IQN di un host Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).

    3. È consigliabile attivare una copia di backup predefinito selezionando la casella di controllo **attiva una copia di backup predefinito per il volume** . Backup predefinito verrà creato un criterio che esegue 22:30 ogni giorno (ora dispositivo) e crea uno snapshot cloud del volume.

        ![impostazioni aggiuntive](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)

    4. Fare clic sull'icona di controllo ![Selezionare l'icona](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Verrà avviato il processo di creazione del volume. Verrà visualizzato un messaggio di stato simile al seguente.

        ![messaggio di stato](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)

        Verrà creato un volume con le impostazioni specificate. Per impostazione predefinita, il backup e il monitoraggio verrà abilitati per il volume.

    5. Per verificare che il volume è stato creato correttamente, passare alla pagina **volumi** . Verrà visualizzato il volume nell'elenco.

        ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Passaggio 4: Installare, inizializzare e formattare un volume

Eseguire la procedura seguente per installare, inizializzare e formattare i volumi StorSimple su un host Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Per installare, inizializzare e formattare un volume

1. Avviare l'iniziatore Microsoft.

2. Nella finestra **proprietà iniziatore iSCSI** , nella scheda **individuazione** fare clic su **Scopri Portal**.

    ![alla scoperta di portale](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)

3. Nella finestra di dialogo **Scopri portale destinazione** fornire l'indirizzo IP dell'interfaccia di rete iSCSI e quindi fare clic su **OK**.

    ![Indirizzo IP](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)

4. Nella finestra **proprietà iniziatore iSCSI** nella scheda **destinazioni** individuare le **destinazioni Discovered**. (Ogni volume sarà un valore di destinazione individuati.) Lo stato dispositivo dovrebbero essere visualizzate come **inattivo**.

    ![destinazioni individuate](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)

5. Selezionare un dispositivo di destinazione e quindi fare clic su **Connetti**. Una volta connesso il dispositivo, lo stato dovrebbe essere **connesso**. (Per ulteriori informazioni sull'utilizzo iniziatore Microsoft, vedere [installazione e configurazione di Microsoft iSCSI iniziatore] [1].

    ![Selezionare il dispositivo di destinazione](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)

6. Scegliere l'host di Windows, premere il tasto Logo Windows + X e quindi fare clic su **Esegui**.

7. Nella finestra di dialogo **Esegui** digitare **diskmgmt**. Fare clic su **OK**e verrà visualizzata la finestra di dialogo **Gestione disco** . Nel riquadro destro vengono visualizzati i volumi sull'host.

8. Nella finestra **Gestione disco** volumi collegati verranno visualizzato come illustrato nella figura seguente. Rapida volume rilevato scegliendo il nome del disco e quindi fare clic su **Online**.

    ![Gestione disco](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)

9. Pulsante destro del mouse e selezionare **Inizializzare disco**.

    ![inizializzare disco 1](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)

10. Nella finestra di dialogo selezionare i dischi da inizializzare e quindi fare clic su **OK**.

    ![inizializzare disco 2](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)

11. Verrà avviata la creazione guidata nuovo Volume semplice. Selezionare una dimensione su disco e quindi fare clic su **Avanti**.

    ![Creazione guidata nuovo volume 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)

12. Assegnare una lettera di unità al volume e quindi fare clic su **Avanti**.

    ![Creazione guidata nuovo volume 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)

13. Immettere i parametri per formattare il volume. **In Windows Server, è supportata solo NTFS.** Impostare l'Australia a 64 KB. Fornire un'etichetta per il volume. È una procedura consigliata per questo nome sia uguale al nome del volume specificato in un dispositivo virtuale StorSimple. Fare clic su **Avanti**.

    ![Creazione guidata nuovo volume 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)

14. Verificare i valori per il volume e quindi fare clic su **Fine**.

    ![Creazione guidata nuovo volume 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)

    I volumi verranno visualizzati come **Online** nella pagina **Gestione disco** .

    ![volumi in linea](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come utilizzare l'interfaccia web locale per [amministrare la matrice virtuale StorSimple](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Appendice a: ottenere IQN di un host Windows Server

Eseguire la procedura seguente per ottenere il iSCSI completo nome iSCSI di un host di Windows che esegue Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Per ottenere IQN di un host di Windows

1. Avviare l'iniziatore Microsoft l'host di Windows.

2. Nella finestra **proprietà iniziatore iSCSI** , nella scheda **configurazione** selezionare e copiare la stringa del campo **Nome iniziatore** .

    ![proprietà iniziatore iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)

2. Salvare la stringa.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



