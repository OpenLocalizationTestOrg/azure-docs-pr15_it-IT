<properties
   pageTitle="Distribuire StorSimple virtuale matrice 3 - configurare il dispositivo come file server virtuale"
   description="In questa esercitazione terza nella distribuzione di matrice virtuale StorSimple chiesto di configurare un dispositivo virtuale come file server."
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
   ms.date="05/26/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---set-up-as-file-server"></a>Distribuire StorSimple virtuale matrice: impostare la come file server

![](./media/storsimple-ova-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introduzione 

Questo articolo si applica alla versione di Microsoft Azure StorSimple virtuale in forma di matrice (noto anche come dispositivo virtuale locale StorSimple o periferica virtuale StorSimple) in esecuzione marzo 2016 disponibilità generale (GA). In questo articolo viene descritto come eseguire l'installazione e configurazione iniziali, registrare il server di file StorSimple, completare la configurazione di dispositivi e creare e connettersi a condivisioni piccole e medie imprese. Questo articolo è il cognome della serie di esercitazioni per la distribuzione necessari per distribuire completamente la matrice virtuale come un file server o su un server iSCSI.

Il processo di installazione e configurazione può richiedere circa 10 minuti per completare.


## <a name="setup-prerequisites"></a>Prerequisiti di installazione

Prima di configurare e configurare il dispositivo virtuale StorSimple, verificare quanto segue:

-   Si dispone il provisioning di un dispositivo virtuale e connessi come descritto nella [disposizione una matrice virtuale StorSimple in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) o [una matrice virtuale StorSimple in VMware provisioning](storsimple-ova-deploy2-provision-vmware.md).

-   Si dispone della chiave di registrazione servizio dal servizio di gestione StorSimple creato per la gestione di dispositivi virtuali StorSimple. Per ulteriori informazioni, vedere [passaggio 2: ottenere il codice di registrazione servizio](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) per StorSimple matrice virtuale.

-   Se si tratta il dispositivo virtuale secondo o successivo che si sta registrando con un servizio di gestione StorSimple esistente, è necessario disporre della chiave di crittografia di servizio dati. Questa chiave generata quando il primo dispositivo è stato registrato con questo servizio. Se è stata persa questa chiave, vedere [ottenere la chiave di crittografia dati del servizio](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) per la matrice virtuale StorSimple.

## <a name="step-by-step-setup"></a>Procedura di configurazione

Utilizzare le seguenti istruzioni dettagliate per installare e configurare il dispositivo virtuale StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Passaggio 1: Completare la configurazione dell'interfaccia utente web locale e registrare il dispositivo 


#### <a name="to-complete-the-setup-and-register-the-device"></a>Per completare la configurazione e registrare il dispositivo

1.  Aprire una finestra del browser e connettersi al web locale dell'interfaccia utente. Tipo: 

    `https://<ip-address of network interface>`

    Utilizzare l'URL di connessione indicato nel passaggio precedente. Verrà visualizzato un messaggio di errore che indica che si verifica un problema con il certificato di sicurezza del sito Web. Fare clic su **Continua per questa pagina Web**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image2.png)

1.  Accedere a web dell'interfaccia utente del dispositivo virtuale come **StorSimpleAdmin**. Immettere la password di amministratore di dispositivo che è stata modificata nel passaggio 3: avviare il dispositivo virtuale in [disposizione una matrice virtuale StorSimple in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) o in [una matrice virtuale StorSimple in VMware provisioning](storsimple-ova-deploy2-provision-vmware.md).

    ![](./media/storsimple-ova-deploy3-fs-setup/image3.png)

1.  Sarà accedere alla pagina **Home** . Questa pagina illustra le varie impostazioni necessarie per configurare e registrare il dispositivo virtuale con il servizio di gestione StorSimple. Si noti che **le impostazioni di rete**, **le impostazioni proxy Web**e **le impostazioni dell'ora** sono facoltativi. Le impostazioni necessarie sola sono **Impostazioni dispositivo** e **Cloud**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image4.png)

1.  Nella pagina **impostazioni di rete** in **interfacce di rete**dati 0 verranno configurati automaticamente dell'utente. Ogni interfaccia di rete per impostazione predefinita per ottenere automaticamente l'indirizzo IP (DHCP). Di conseguenza, un indirizzo IP, subnet e gateway verrà assegnati automaticamente (per IPv4 e IPv6).

    ![](./media/storsimple-ova-deploy3-fs-setup/image5.png)

    Se è stato aggiunto più di una rete durante il provisioning del dispositivo, è possibile configurare loro qui. Nota che è possibile configurare l'interfaccia di rete come IPv4 solo o come IPv4 e IPv6. IPv6 non sono supportate solo le configurazioni.

1.  I server DNS sono necessari perché vengono utilizzate quando il dispositivo tenta di comunicare con il provider di servizi di archiviazione cloud o per risolvere il dispositivo in base al nome quando configurato come un file server. Nella pagina **impostazioni di rete** in **server DNS**:

    1.  Un server DNS primario e secondario verrà configurato automaticamente. Se si sceglie di configurare gli indirizzi IP statici, è possibile specificare i server DNS. Disponibilità elevata, è consigliabile configurare un primario e un server DNS secondario.

    2.  Fare clic su **Applica**. Questa operazione verrà applicata e convalidare le impostazioni di rete.

2.  Nella pagina **Impostazioni dispositivo** :

    1.  Assegnare un **nome** univoco per il dispositivo. Questo nome può contenere caratteri di 1-15 e può contenere lettere, numeri e trattini.

    2.  Fare clic sull'icona di **File server** ![](./media/storsimple-ova-deploy3-fs-setup/image6.png) per il **tipo** di dispositivo che si sta creando. Un file server è possibile creare cartelle condivise.

    3.  Mentre il dispositivo è un file server, sarà necessario aggiungere il dispositivo a un dominio. Immettere un **nome di dominio**.

    1.  Fare clic su **Applica**.

2.  Verrà visualizzata una finestra di dialogo. Immettere le credenziali di dominio nel formato specificato. Fare clic sull'icona di controllo. Verranno verificate le credenziali di dominio. Se le credenziali sono corrette, si verrà visualizzato un messaggio di errore.

    ![](./media/storsimple-ova-deploy3-fs-setup/image7.png)

1.  Fare clic su **Applica**. Questa operazione verrà applicata e convalidare le impostazioni del dispositivo.

    ![](./media/storsimple-ova-deploy3-fs-setup/image8.png)

    > [AZURE.NOTE]
    > 
    > Assicurarsi che la matrice virtuale nella propria unità organizzativa (OU) per Active Directory e nessun gruppo oggetti criteri () vengono applicati a tale o ereditati. Criteri di gruppo possono installare applicazioni, ad esempio il software antivirus in matrice virtuale StorSimple. Installare software aggiuntivo non è supportata e possono causare il danneggiamento dei dati. 

1.  Configurare, facoltativamente, il server di proxy web. Configurazione proxy web è facoltativa, tenere presente che se si utilizza un proxy web, è possibile solo configurarlo qui.

    ![](./media/storsimple-ova-deploy3-fs-setup/image9.png)

    Nella pagina **proxy Web** :

    1.  Specificare l' **URL del proxy Web** in questo formato: *http://&lt;indirizzo IP host o FDQN&gt;: il numero di porta*. Si noti che non sono supportati URL HTTPS.

    2.  Specificare **l'autenticazione** di **base** o **Nessuno**.

    3.  Se mediante l'autenticazione, sarà anche necessario specificare un **nome utente** e **Password**.

    4.  Fare clic su **Applica**. Per convalidare e applicare le impostazioni del proxy web configurata.

1.  Facoltativamente, configurare le impostazioni di ora del dispositivo, ad esempio fuso orario e il server NTP primario e secondario. Server NTP necessari perché il dispositivo deve sincronizzare l'ora in modo che possa autenticazione con il provider di servizi cloud.

    ![](./media/storsimple-ova-deploy3-fs-setup/image10.png)

    Nella pagina **impostazioni dell'ora** :

    1.  Nell'elenco a discesa selezionare il **fuso orario** in base alla posizione geografica in cui si desidera distribuire il dispositivo sia installato. Il fuso orario predefinito per il dispositivo è PST. Il dispositivo utilizzerà il fuso orario per tutte le operazioni pianificate.

    2.  Specificare un **server NTP primario** per il dispositivo oppure accettare il valore predefinito di time.windows.com. Assicurarsi che la rete consenta il traffico NTP passare dal centro dati a Internet.

    3.  Se si desidera specificare un **server NTP secondario** del dispositivo.

    4.  Fare clic su **Applica**. Per convalidare e applicare le impostazioni di tempo configurato.

1.  Configurare le impostazioni di cloud per il dispositivo. In questo passaggio si completare la configurazione del dispositivo locale e quindi registrare il dispositivo con il servizio di gestione StorSimple.

    1.  Immettere il **codice di registrazione di servizio** ottenuto in [passaggio 2: ottenere il codice di registrazione servizio](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) per StorSimple matrice virtuale.

    2.  Ignorare questo passaggio se si tratta di dispositivo prima la registrazione con il servizio e passare alla sezione successiva. Se il dispositivo prima che si sta registrando con questo servizio non, sarà necessario fornire la **chiave di crittografia di servizio dati**. Questa chiave è necessaria con la chiave di registrazione del servizio per registrare altri dispositivi con il servizio di gestione StorSimple. Per ulteriori informazioni, vedere per ottenere la [chiave di crittografia di servizio dati](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) del Web locale dell'interfaccia utente.

    3.  Fare clic su **Registra**. Questa operazione verrà riavviare il dispositivo. Potrebbe essere necessario attendere 2-3 minuti prima che il dispositivo sia registrato correttamente. Dopo il riavvio il dispositivo, sarà accedere alla pagina di accesso.

        ![](./media/storsimple-ova-deploy3-fs-setup/image13.png)
    

1.  Tornare al portale di classica Azure. Nella pagina **dispositivi** , verificare che il dispositivo è connesso al servizio cercando lo stato. Lo stato dispositivo è necessario che sia **Attiva**.

![](./media/storsimple-ova-deploy3-fs-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>Passaggio 2: Completare la configurazione di dispositivo

Per completare la configurazione del dispositivo del dispositivo StorSimple, è necessario:

-   Selezionare un account di archiviazione da associare il dispositivo.

-   Scegliere le impostazioni di crittografia per i dati inviati nel cloud.

Nel [portale classica Azure](https://manage.windowsazure.com/) per completare la configurazione di dispositivo, procedere come segue.

#### <a name="to-complete-the-minimum-device-setup"></a>Per completare la configurazione di periferica minimi

1.  Nella pagina di **dispositivi** , selezionare il dispositivo che appena creato. Questo dispositivo verrebbe visualizzati come **attivo**. Fare clic sulla freccia in base al nome di dispositivo e quindi fare clic su **Quick Start**.

2.  Fare clic su **configurazione di dispositivi completo** per avviare la configurazione guidata dispositivo.

3.  Nella configurazione guidata dispositivo nella pagina **Impostazioni di base** , eseguire le operazioni seguenti:

    1.  Specificare un account di archiviazione per l'uso con il dispositivo. È possibile selezionare un account di archiviazione esistente nella sottoscrizione nell'elenco a discesa o specificare **Aggiungi altre** per scegliere un account da una sottoscrizione diversa.

    2.  Definire le impostazioni di crittografia per tutti i dati a riposo (crittografia AES) che verrà inviati nel cloud. Per crittografare i dati, selezionare la casella combinata per **abilitare chiave di crittografia di spazio di archiviazione cloud**. Immettere una crittografia di spazio di archiviazione cloud di 32 caratteri. Immettere nuovamente il codice Product Key per confermarla. Verrà utilizzata una chiave AES 256 bit con la chiave definite dall'utente per la crittografia.

    3.  Fare clic sull'icona di controllo ![](./media/storsimple-ova-deploy3-fs-setup/image15.png).

        ![](./media/storsimple-ova-deploy3-fs-setup/image16.png)

Le impostazioni verranno aggiornate. Dopo aggiornamento delle impostazioni correttamente, pulsante Impostazioni dispositivo completo verrà visualizzato in grigio. Si tornerà alla pagina **Guida introduttiva** di dispositivo.

 ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)


> [AZURE.NOTE]                                                              
>
> È possibile modificare tutte le altre impostazioni dispositivo in qualsiasi momento, accedere alla pagina **Configura** .

## <a name="step-3-add-a-share"></a>Passaggio 3: Aggiungere una condivisione

Nel [portale classica Azure](https://manage.windowsazure.com/) per creare una condivisione, procedere come segue.

#### <a name="to-create-a-share"></a>Per creare una condivisione

1.  Nella pagina **Guida introduttiva** periferica, fare clic su **Aggiungi una condivisione**. Verrà avviata la procedura guidata Condividi Aggiungi.

    ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)

1.  Nella pagina **Impostazioni di base** , eseguire le operazioni seguenti:

    1.  Specificare un nome univoco per la condivisione. Il nome deve essere una stringa contenente i caratteri da 3 a 127.

    2.  (Facoltativo) Fornire una descrizione per la condivisione. La descrizione consenta di identificare i proprietari di condivisione.

    3.  Selezionare un tipo di utilizzo per la condivisione. Il tipo di utilizzo può essere **Tiered** o **aggiunte in locale**, relativi a più livelli predefinito. Per carichi di lavoro che richiedono garanzie locale, latenza bassa e prestazioni più elevate, selezionare una condivisione **localmente aggiunte** . Per tutti gli altri dati, selezionare una condivisione **Tiered** .

    Una condivisione locale bloccata thickly viene completato il provisioning e garantisce che i dati presenti nella condivisione principali rimangono locali nel dispositivo e non soppressione nel cloud. Una condivisione a più livelli invece è eseguono il provisioning. Quando si crea una condivisione a più livelli, viene eseguito il provisioning di 10% dello spazio a livello locale e viene eseguito il provisioning di 90% dello spazio nel cloud. Ad esempio, se è stato effettuato il provisioning un volume di 1 TB, 100 GB risiede nello spazio locale e 900 GB verranno utilizzati nel cloud quando i livelli di dati. In questo caso, a sua volta esaurirsi tutto lo spazio locale nel dispositivo, non è possibile di provisioning a una condivisione a più livelli.

1.  Specificare la capacità di provisioning per la condivisione. Si noti che la capacità specificata deve essere inferiore alla capacità disponibile. In una condivisione a più livelli, le dimensioni di condivisione devono essere compreso tra 500 GB e 20 TB. Per una condivisione locale bloccata, specificare la dimensione di condivisione tra 50 GB e 2 TB. Utilizzare la capacità disponibile come una Guida per effettuare il provisioning di una condivisione. Se la capacità locale disponibile è 0 GB, quindi non sarà possibile effettuare il provisioning di condivisioni locale o su più livelli.

    ![](./media/storsimple-ova-deploy3-fs-setup/image18.png)

1.  Fare clic sull'icona freccia ![](./media/storsimple-ova-deploy3-fs-setup/image19.png) per passare alla pagina successiva.

1.  Nella pagina **Impostazioni aggiuntive** assegnare le autorizzazioni per l'utente o il gruppo che accede alla condivisione. Specificare il nome dell'utente o gruppo di utenti in *<john@contoso.com>* formato. È consigliabile utilizzare un gruppo di utenti (anziché un singolo utente) per consentire i privilegi di amministratore accedere a queste azioni. Dopo aver assegnato le autorizzazioni di seguito, è possibile utilizzare quindi Esplora risorse per modificare le autorizzazioni.

    ![](./media/storsimple-ova-deploy3-fs-setup/image20.png)

1.  Fare clic sull'icona di controllo ![](./media/storsimple-ova-deploy3-fs-setup/image21.png). Verrà creata una condivisione con le impostazioni specificate. Per impostazione predefinita, il backup e il monitoraggio verrà abilitati per la condivisione.

## <a name="step-4-connect-to-the-share"></a>Passaggio 4: Connettere alla condivisione

È ora necessario connettersi alla condivisione creata nel passaggio precedente. Eseguire questa procedura sull'host Windows Server.

#### <a name="to-connect-to-the-share"></a>Per connettersi alla condivisione

1.  Premere ![](./media/storsimple-ova-deploy3-fs-setup/image22.png) + r. Nella finestra Esegui, specificare la * \\ * come percorso, sostituendo *nome del file server* con il nome di dispositivo che è stata assegnata al server di file. Fare clic su **OK**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image23.png)

2.  Verrà aperta la Esplora risorse. A questo punto dovrebbe essere possibile le azioni che è stato creato in modo da visualizzare le cartelle. Selezionare e fare doppio clic su una condivisione (cartella) per visualizzare il contenuto.

    ![](./media/storsimple-ova-deploy3-fs-setup/image24.png)

3.  È possibile aggiungere file a queste condivisioni ed eseguire un backup.

![icona del video](./media/storsimple-ova-deploy3-fs-setup/video_icon.png) **Video disponibile**

Guardare il video per scoprire come configurare e registrare una matrice virtuale StorSimple come un file server.

> [AZURE.VIDEO configure-a-storsimple-virtual-array]

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come utilizzare l'interfaccia web locale per [amministrare la matrice virtuale StorSimple](storsimple-ova-web-ui-admin.md).
