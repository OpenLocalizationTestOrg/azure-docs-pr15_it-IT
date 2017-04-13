<properties
   pageTitle="Distribuire matrice virtuale StorSimple - provisioning in Hyper-V"
   description="In questa esercitazione seconda nella distribuzione di matrice virtuale StorSimple implica il provisioning di un dispositivo virtuale in Hyper-V."
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
   ms.date="10/11/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-hyper-v"></a>Distribuire matrice virtuale StorSimple - effettuare il provisioning di una matrice virtuale in Hyper-V

![](./media/storsimple-ova-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Panoramica

In questa esercitazione provisioning si applica alla versione di Microsoft Azure StorSimple virtuale matrici (noto anche come dispositivi virtuali locale StorSimple o dispositivi virtuali StorSimple) in esecuzione marzo 2016 disponibilità generale (GA). In questa esercitazione viene descritto come effettuare il provisioning di una matrice virtuale StorSimple in un sistema di host Hyper-V in esecuzione Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2. Questo articolo si applica alla distribuzione di matrici virtuali StorSimple portal classica Azure, nonché Cloud di Microsoft Azure per enti pubblici.

Si necessari privilegi di amministratore per eseguire il provisioning e configurare un dispositivo virtuale. La configurazione di provisioning e iniziale può richiedere circa 10 minuti per completare.


## <a name="provisioning-prerequisites"></a>Prerequisiti di provisioning

Qui sono disponibili i prerequisiti per effettuare il provisioning di un dispositivo virtuale su un sistema di host con Hyper-V in Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2.

### <a name="for-the-storsimple-manager-service"></a>Per il servizio di gestione StorSimple

Prima di iniziare, verificare che:

-   È stata completata tutti i passaggi in [preparare l'ambiente portale per StorSimple matrice virtuale](storsimple-ova-deploy1-portal-prep.md).

-   Scaricato l'immagine del dispositivo virtuale per Hyper-V dal portale di Azure. Per ulteriori informazioni, vedere [passaggio 3: scaricare l'immagine di dispositivo virtuale](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

    > [AZURE.IMPORTANT] Il software in esecuzione su StorSimple virtuale matrice può essere usato solo in combinazione con il servizio di gestione Storsimple.

### <a name="for-the-storsimple-virtual-device"></a>Per il dispositivo virtuale StorSimple

Prima di distribuire un dispositivo virtuale, verificare che:

-   È possibile accedere a un sistema di host che esegue Hyper-V in Windows Server 2008 R2 o versioni successive che possono essere utilizzati per una disposizione un dispositivo.

-   Il sistema host è in grado di dedicare le risorse seguenti per eseguire il provisioning dispositivo virtuale:

    -   Almeno 4 core.

    -   Almeno 8 GB di RAM.

    -   Un'interfaccia di rete.

    -   Un disco virtuale 500 GB per i dati di sistema.

### <a name="for-the-network-in-the-datacenter"></a>Per la rete nel centro dati

Prima di iniziare, verificare i requisiti di reti per distribuire un dispositivo virtuale StorSimple e configurare la rete Data Center in modo appropriato. Per ulteriori informazioni, vedere [requisiti di rete StorSimple matrice virtuale](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Procedura dettagliata di provisioning

Per eseguire il provisioning e connettersi a un dispositivo virtuale, sarà necessario eseguire la procedura seguente:

1.  Verificare che il sistema host sia risorse sufficienti per soddisfare i requisiti minimi dispositivo virtuale.

2.  Effettuare il provisioning di un dispositivo virtuale nel hypervisor.

3.  Iniziare il dispositivo virtuale e l'indirizzo IP.

Tutte le operazioni seguenti sono descritte nelle sezioni seguenti.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements"></a>Passaggio 1: Verificare che il sistema host soddisfi i requisiti minimi dispositivo virtuale

Per creare un dispositivo virtuale, sarà necessario:

-   Il ruolo di Hyper-V installato in Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 SP1.

-   Microsoft Hyper-V Manager su un client di Microsoft Windows connesso all'host.

È necessario assicurarsi che l'hardware sottostante (sistema host) in cui si sta creando il dispositivo virtuale in grado di dedicare le risorse seguenti al dispositivo virtuale:

- Almeno 4 core.
- Almeno 8 GB di RAM.
- Un'interfaccia di rete.
- Un disco virtuale 500 GB per i dati di sistema.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Passaggio 2: Effettuare il provisioning di un dispositivo virtuale in hypervisor

Per eseguire il provisioning di un dispositivo nel hypervisor, procedere come segue.

#### <a name="to-provision-a-virtual-device"></a>Effettuare il provisioning di un dispositivo virtuale

1.  Sull'host Windows Server, copiare l'immagine di dispositivo virtuale in un'unità locale. Questa è l'immagine (disco rigido virtuale o VHDX) che è stato scaricato tramite il portale di Azure. Prendere nota del percorso in cui è stata copiata l'immagine come si prevede di utilizzare questo in un secondo momento nella procedura.

2.  Aprire **Server Manager**. Nell'angolo superiore destro, fare clic su **Strumenti** e selezionare **Gestione di Hyper-V**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image1.png)

    Se si esegue Windows Server 2008 R2, aprire la gestione di Hyper-V. In Server Manager, fare clic su **ruoli > Hyper-V > Gestione di Hyper-V**.

1.  In **Gestione di Hyper-V**nel riquadro ambito il nodo del sistema per aprire il menu di scelta rapida e quindi fare clic su **Nuovo** > **macchina virtuale**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image2.png)

1.  Nella pagina della creazione guidata macchina virtuale **prima di iniziare** , fare clic su **Avanti**.

1.  Nella pagina **percorso e una specifica nome** specificare un **nome** per il dispositivo virtuale. Fare clic su **Avanti**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image4.png)

1.  Nella pagina **specifica di generazione** , scegliere il tipo di immagine di dispositivo e quindi fare clic su **Avanti**. Se si usa Windows Server 2008 R2, non viene visualizzata la pagina.

    * Scegliere **generazione 2** se è stato scaricato un'immagine di .vhdx per Windows Server 2012 o versione successiva.
    * Scegliere **generazione 1** se è stato scaricato un'immagine VHD per Windows Server 2008 R2 o versione successiva.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image5.png)

1.  Nella pagina **assegnare memoria** specificare una **memoria di avvio** di almeno **8192 MB**, non abilitare memoria dinamica e quindi fare clic su **Avanti**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image6.png)

1.  Nella pagina **Configura rete** specificare l'opzione virtuale che sia connesso a Internet e quindi fare clic su **Avanti**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image7.png)

1.  Nella pagina **Connetti virtuale sul disco rigido** , scegliere **Usa un disco rigido virtuale esistente**, specificare la posizione dell'immagine del dispositivo virtuale (.vhdx o VHD) e quindi fare clic su **Avanti**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image8m.png)

1.  Controllare il **Riepilogo** e quindi fare clic su **Fine** per creare la macchina virtuale. Ma non ancora passare direttamente, è necessario aggiungere alcuni core CPU e una seconda unità. 

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image9.png)

1.  Per soddisfare i requisiti minimi, sarà necessario 4 core. Per aggiungere processori virtuali, con il sistema di host selezionato nella finestra di **Gestione di Hyper-V** , nel riquadro di destra sotto l'elenco di **macchine virtuali di**individuare la macchina virtuale che appena creato. Selezionare e destro del mouse sul nome del computer e selezionare **Impostazioni**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image10.png)

1.  Nella pagina **Impostazioni** nel riquadro sinistro fare clic su **ARM**. Nel riquadro destro, impostare **numero di processori virtuali** a 4 (o più). Fare clic su **Applica**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image11.png)

1.  Per soddisfare i requisiti minimi, è necessario anche aggiungere un disco dati virtuale 500 GB. Nella pagina **Impostazioni** :

    1.  Nel riquadro sinistro selezionare **Controller SCSI**.
    2.  Nel riquadro destro, selezionare **Un'unità disco** e fare clic su **Aggiungi**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image12.png)

1.  Nella pagina **disco rigido** , selezionare l'opzione **virtuale sul disco rigido** e fare clic su **Nuovo**. Verrà avviata la **Creazione guidata disco rigido virtuale**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image13.png)

1.  Nella pagina della creazione guidata disco rigido virtuale **prima di iniziare** , fare clic su **Avanti**.

1.  Nella **pagina scegliere il formato del disco**, accettare l'opzione predefinita del formato **VHDX** . Fare clic su **Avanti**. Se si esegue Windows Server 2012 R2 o Windows Server 2008 R2, questa schermata non viene visualizzata.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image15.png)

1.  Nella **pagina scegliere il tipo del disco**, impostare tipo disco rigido virtuale come **espansione dinamica** (scelta consigliata). Se si sceglie il disco di **dimensioni fisse** , oltre che ma potrebbe essere necessario molto tempo. È consigliabile non utilizzare l'opzione **Differencing** . Fare clic su **Avanti**. Si noti che **espansione dinamica** predefinito in Windows Server 2012 R2 e Windows Server 2012. In Windows Server 2008 R2, il valore predefinito è **dimensioni fisse**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image16.png)

1.  Nella pagina **specificare il nome e posizione** , specificare un **nome** , nonché **posizione** (è possibile passare a uno) per il disco di dati. Fare clic su **Avanti**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image17.png)

1.  Nella pagina **Configurazione disco** , selezionare l'opzione **Crea un nuovo disco rigido virtuale vuoto** e specificare le dimensioni come **500 GB** (o più). Mentre 500 GB i requisiti minimi, è sempre possibile effettuare il provisioning di un disco più grande. Si noti che non è possibile espandere o ridurre il disco una volta completato il provisioning. Per ulteriori informazioni sulla dimensione del disco a disposizione, consultare la sezione ridimensionamento nel documento [le procedure consigliate](storsimple-ova-best-practices.md#configuration-best-practices) . Fare clic su **Avanti**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image18.png)

1.  Nella pagina **Riepilogo** rivedere i dettagli del disco dati virtuale e se soddisfatti, fare clic su **Fine** per creare il disco. La procedura guidata verrà chiusa e verrà aggiunto un disco rigido virtuale nel computer in uso.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image19.png)

2.  Si tornerà alla pagina **Impostazioni** . Fare clic su **OK** per chiudere la pagina **Impostazioni** e tornare alla finestra di gestione di Hyper-V.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Passaggio 3: Avviare il dispositivo virtuale e ottenere l'indirizzo IP

Eseguire la procedura seguente per avviare il dispositivo virtuale e la connessione.

#### <a name="to-start-the-virtual-device"></a>Per avviare il dispositivo virtuale

1.  Avviare il dispositivo virtuale.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image21.png)

1.  Dopo il dispositivo è in esecuzione, selezionare il dispositivo di scelta rapida e scegliere **Connetti**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image22.png)

1.  Potrebbe essere necessario attendere 10 5 minuti per il dispositivo sia pronto. Verrà visualizzato un messaggio di stato nella console di per indicare lo stato di avanzamento. Dopo il dispositivo è pronto, passare **all'azione**. Premere `Ctrl + Alt + Delete` agli utenti di accedere al dispositivo virtuale. L'utente predefinito è *StorSimpleAdmin* e la password predefinita è *Password1*.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image23.png)

1.  Per motivi di sicurezza, la password di amministratore del dispositivo scade al primo accesso. Verrà richiesto di cambiare la password.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image24.png)

    Immettere una password che contenga almeno 8 caratteri. La password deve soddisfare almeno 3 fuori i seguenti requisiti di 4: caratteri maiuscoli, minuscoli, numerici e speciali. Immettere nuovamente la password per confermarla. L'utente verrà informato che la password è stata modificata.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image25.png)

1.  Dopo la password viene modificata, può riavviare il dispositivo virtuale. Attendere che il dispositivo per iniziare.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image26.png)

    Console di Windows PowerShell del dispositivo verrà visualizzata insieme a un indicatore di stato.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image27.png)

1.  Procedura 6-8 si applica solo all'avvio in un ambiente non DHCP. Se si è in un ambiente DHCP, ignorare questi passaggi e andare al passaggio 9. Se è stato avviato il dispositivo in ambiente non DHCP, verrà visualizzata la schermata seguente.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image28m.png)

    È ora necessario configurare la rete.

1.  Utilizzare la `Get-HcsIpAddress` comando per elencare le interfacce di rete attivate nel dispositivo virtuale. Se il dispositivo ha una singola interfaccia di rete abilitata, il nome predefinito assegnato a questa interfaccia è `Ethernet`.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image29m.png)

1.  Utilizzare la `Set-HcsIpAddress` cmdlet per configurare la rete. Come illustrato di seguito:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image30.png)

1.  Dopo aver completata la configurazione iniziale e si è avviata il dispositivo, verrà visualizzato il testo dell'intestazione di dispositivo. Prendere nota dell'indirizzo IP e l'URL visualizzato il testo dell'intestazione per gestire il dispositivo. Utilizzare questo indirizzo IP per connettersi all'interfaccia del dispositivo virtuale web e completare l'installazione locale e la registrazione.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image31m.png)



1. (Facoltativo) Eseguire questo passaggio solo se si distribuisce il dispositivo nel Cloud per enti pubblici. Si verrà ora attiva la modalità di Stati Uniti Federal Information Processing Standard (FIPS) nel dispositivo. Lo standard FIPS 140 definisce algoritmi approvati per l'utilizzo per sistemi di computer per enti pubblici Federal IT per la protezione dei dati riservati.
    1. Per attivare la modalità FIPS, eseguire il cmdlet seguente:

        `Enter-HcsFIPSMode`

    2. Dopo aver attivato la modalità FIPS in modo che compaiano convalida crittografia, riavviare il dispositivo.

        > [AZURE.NOTE] È possibile attivare o disattivare la modalità FIPS nel dispositivo. Alternate dispositivo tra la modalità FIPS e non FIPS non è supportata.

Se il dispositivo non soddisfa i requisiti minimi di configurazione, verrà visualizzato un errore nel testo dell'intestazione (come illustrato di seguito). È necessario modificare la configurazione del dispositivo in modo che abbia risorse adeguate per soddisfare i requisiti minimi. È possibile riavviare e connettersi al dispositivo. Fare riferimento ai requisiti minimi di configurazione in [passaggio 1: assicurarsi che il sistema host soddisfi i requisiti minimi dispositivo virtuale](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-hyperv/image32.png)

Se si affrontare qualsiasi altro tipo di errore durante la configurazione iniziale tramite l'interfaccia web locale, consultare i seguenti flussi di lavoro [Gestione la matrice virtuale StorSimple tramite l'interfaccia web locale](storsimple-ova-web-ui-admin.md).

-   Eseguire test di diagnostica per [risolvere i problemi di installazione dell'interfaccia utente web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Pacchetto log genera e visualizzare i file di log](storsimple-ova-web-ui-admin.md#generate-a-log-package).

![icona del video](./media/storsimple-ova-deploy2-provision-hyperv/video_icon.png)  **Video disponibile**

Guardare il video per scoprire come è possibile effettuare il provisioning di una matrice virtuale StorSimple in Hyper-V.

> [AZURE.VIDEO create-a-storsimple-virtual-array]

## <a name="next-steps"></a>Passaggi successivi

-   [Impostare la matrice virtuale StorSimple come file server](storsimple-ova-deploy3-fs-setup.md)

-   [Impostare la matrice virtuale StorSimple come server iSCSI](storsimple-ova-deploy3-iscsi-setup.md)
