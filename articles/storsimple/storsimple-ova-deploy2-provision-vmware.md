<properties
   pageTitle="Distribuire matrice virtuale StorSimple - provisioning in VMware"
   description="In questa esercitazione seconda serie di distribuzione matrice virtuale StorSimple implica il provisioning di un dispositivo virtuale in VMware."
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
   ms.date="04/12/2016"
   ms.author="alkohli"/>


# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-vmware"></a>Distribuire matrice virtuale StorSimple - effettuare il provisioning di una matrice virtuale in VMware

![](./media/storsimple-ova-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Panoramica 
In questa esercitazione provisioning si applica alla versione StorSimple matrici virtuale (noto anche come dispositivi virtuali locale StorSimple o dispositivi virtuali StorSimple) in esecuzione marzo 2016 disponibilità generale (GA). In questa esercitazione viene descritto come effettuare il provisioning e connettersi a una matrice virtuale StorSimple in un sistema di host che eseguono VMware ESXi 5.5 e versioni successive. Questo articolo si applica alla distribuzione di matrici virtuali StorSimple portal classica Azure, nonché Cloud di Microsoft Azure per enti pubblici.

Si necessari privilegi di amministratore per eseguire il provisioning e connettersi a un dispositivo virtuale. La configurazione di provisioning e iniziale può richiedere circa 10 minuti per completare.


## <a name="provisioning-prerequisites"></a>Prerequisiti di provisioning

Qui sono disponibili i prerequisiti per effettuare il provisioning di un dispositivo virtuale in un sistema di host che eseguono VMware ESXi 5.5 e versioni successive.

### <a name="for-the-storsimple-manager-service"></a>Per il servizio di gestione StorSimple

Prima di iniziare, verificare che:

-   È stata completata tutti i passaggi in [preparare l'ambiente portale per StorSimple matrice virtuale](storsimple-ova-deploy1-portal-prep.md).

-   Scaricato l'immagine del dispositivo virtuale per VMware dal portale di Azure. Per ulteriori informazioni, vedere [passaggio 3: scaricare l'immagine di dispositivo virtuale](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

### <a name="for-the-storsimple-virtual-device"></a>Per il dispositivo virtuale StorSimple 

Prima di distribuire un dispositivo virtuale, verificare che:

-   È possibile accedere a un sistema di host che esegue Hyper-V (2008 R2 o versioni successive) che può essere utilizzato per una disposizione un dispositivo.

-   Il sistema host è in grado di dedicare le risorse seguenti per eseguire il provisioning dispositivo virtuale:

    -   Almeno 4 core.

    -   Almeno 8 GB di RAM.

    -   Un'interfaccia di rete.

    -   Un disco virtuale 500 GB per i dati di sistema.

### <a name="for-the-network-in-datacenter"></a>Per la rete in Data Center 

Prima di iniziare, verificare che:

-   È stato esaminato i requisiti di rete per distribuire un dispositivo virtuale StorSimple e configurato la rete Data Center in base ai requisiti. Per ulteriori informazioni, vedere [requisiti di sistema StorSimple matrice virtuale](storsimple-ova-system-requirements.md).

## <a name="step-by-step-provisioning"></a>Procedura dettagliata di provisioning 

Per eseguire il provisioning e connettersi a un dispositivo virtuale, sarà necessario eseguire la procedura seguente:

1.  Verificare che il sistema host sia risorse sufficienti per soddisfare i requisiti minimi dispositivo virtuale.

2.  Effettuare il provisioning di un dispositivo virtuale nel hypervisor.

3.  Iniziare il dispositivo virtuale e l'indirizzo IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Passaggio 1: Garantire sistema host soddisfi i requisiti minimi dispositivo virtuale

Per creare un dispositivo virtuale, sarà necessario:

-   Accesso a un sistema di host di VMware ESXi Server 5.5 e versioni successive.

-   VMware vSphere client nel sistema di gestire l'host ESXi.

    -   Un minimo di 4 core.

    -   Almeno 8 GB di RAM.

    -   Un'interfaccia di rete connessi alla rete in grado di instradare il traffico a Internet. La larghezza di banda Internet minima dovrebbe essere 5 Mbps per consentire di lavoro ottimale del dispositivo.

    -   Un disco virtuale 500 GB per i dati.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Passaggio 2: Effettuare il provisioning di un dispositivo virtuale in hypervisor

Per eseguire il provisioning di un dispositivo virtuale nel hypervisor, procedere come segue.

1.  Copiare l'immagine di dispositivo virtuale nel sistema. Questa è l'immagine scaricati tramite il portale classico Azure. 
    1.  Assicurarsi che questo sia il file di immagine più recente è stato scaricato. Se è stato scaricato l'immagine in precedenza, scaricarlo nuovamente per verificare di disporre l'immagine più recente. L'immagine più recente ha due file (invece di uno).
    2.  Prendere nota del percorso in cui è stata copiata l'immagine come si prevede di utilizzare questo in un secondo momento nella procedura.

2.  Accedere al server ESXi usando il client vSphere. È necessario disporre dei privilegi di amministratore per creare una macchina virtuale.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image1.png)

1.  Nel client vSphere, nella sezione inventario nel riquadro a sinistra, selezionare il ESXi Server.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image2.png)

1.  È prima di tutto verrà caricare il file VMDK al server ESXi. Passare alla scheda **configurazione** nel riquadro destro. In **Hardware**, selezionare **lo spazio di archiviazione**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image3.png)

1.  Nel riquadro destro, in **archivi dati**, selezionare l'archivio di dati in cui si desidera caricare il file VMDK. Archivio dati deve avere spazio sufficiente per il disco del sistema operativo e dati.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image4.png)

1.  Fare clic e selezionare **Sfoglia archivio dati**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image5.png)

1.  Verrà visualizzata una finestra **Del Browser di archivio dati** .

    ![](./media/storsimple-ova-deploy2-provision-vmware/image6.png)

1.  Nella barra degli strumenti, fare clic su ![](./media/storsimple-ova-deploy2-provision-vmware/image7.png) l'icona per creare una nuova cartella. Specificare il nome della cartella e prendere nota di esso. Utilizzare il nome della cartella in un secondo momento durante la creazione di una macchina virtuale (scelta consigliata consigliata). Fare clic su **OK**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image8.png)

1.  La nuova cartella verrà visualizzato nel riquadro a sinistra del **Browser di archivio dati**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image9.png)

1.  Fare clic sull'icona di caricamento ![](./media/storsimple-ova-deploy2-provision-vmware/image10.png) e selezionare **Carica File**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image11.png)

1.  È ora necessario individuare e scegliere file VMDK scaricati. Si verificherà due file. Selezionare un file da caricare.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image12m.png)

1.  Fare clic su **Apri**. Verrà ora avviato il caricamento di file VMDK all'archivio dati specificato. Possono richiedere alcuni minuti per il file da caricare.


1.  Al termine del caricamento, verrà visualizzato il file di archivio dati nella cartella che è stato creato. 

    ![](./media/storsimple-ova-deploy2-provision-vmware/image14.png)

    È ora necessario caricare il file VMDK secondo all'archivio dati stesso.

1.  Tornare alla finestra del client vSphere. Con server ESXi selezionata, pulsante destro del mouse e selezionare **nuova macchina virtuale**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image15.png)

1.  Verrà visualizzata una finestra **Crea nuova macchina virtuale** . Nella pagina **configurazione** , selezionare l'opzione **personalizzata** . Fare clic su **Avanti**.
    ![](./media/storsimple-ova-deploy2-provision-vmware/image16.png)

2.  Nella pagina **nome e posizione** , specificare il nome del computer virtuale. Questo nome deve corrispondere il nome della cartella (procedura consigliata) specificata in precedenza nel passaggio 8.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image17.png)

1.  Nella pagina **dello spazio di archiviazione** selezionare un archivio di dati che si desidera utilizzare per eseguire il provisioning di una macchina virtuale.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image18.png)

1.  Nella pagina **Versione macchina virtuale** selezionare **versione macchina virtuale: 8**. Si noti che le versioni 8 a 11 sono supportate.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image19.png)

1.  Nella pagina **sistema operativo Guest** , selezionare il **sistema operativo Guest** di **Windows**. Per la **versione**dall'elenco a discesa selezionare **Microsoft Windows Server 2012 (64 bit)**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image20.png)

1.  Nella pagina **CPU** regolare il **numero di socket virtuale** e il **numero di core per socket virtuale** in modo che il **numero totale di core** 4 (o più). Fare clic su **Avanti**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image21.png)

1.  Nella pagina di **memoria** , specificare 8 GB (o più) di RAM. Fare clic su **Avanti**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image22.png)

1.  Nella pagina **rete** specificare il numero delle interfacce di rete. I requisiti minimi sono un'interfaccia di rete.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image23.png)

1.  Nella pagina **Controller SCSI** accettare i valori predefiniti **controller LSI logica SA**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image24.png)

1.  Nella pagina **Seleziona un disco** , scegliere **Usa un disco virtuale esistente**. Fare clic su **Avanti**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image25.png)

1.  Nella pagina **Seleziona disco esistente** nel **Percorso su disco**, fare clic su **Sfoglia**. Verrà aperta una finestra di dialogo **Sfoglia archivi dati** . Passare al percorso in cui è stata caricata il VMDK. Verrà visualizzato un solo file di archivio dati come due file caricato inizialmente sono stati uniti. Selezionare il file e fare clic su **OK**. Fare clic su **Avanti**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image26.png)

1.  Nella pagina **Opzioni avanzate di** accettare i valori predefiniti e fare clic su **Avanti**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image27.png)

1.  Nella pagina **pronti a completare** , rivedere tutte le impostazioni associate la nuova macchina virtuale. Selezionare **Modifica impostazioni macchina virtuale prima del completamento**. Fare clic su **Continua**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image28.png)

1.  Nella pagina **Macchine virtuali di proprietà** nella scheda **Hardware** individuare hardware del dispositivo. Selezionare **nuovo disco rigido**. Fare clic su **Aggiungi**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image29.png)

1.  Verrà visualizzata la finestra di **Installazione Hardware** . Nella pagina **Tipo di dispositivo** , in **scegliere il tipo di dispositivo che si desidera aggiungere**, selezionare **sul disco rigido** e fare clic su **Avanti**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image30.png)

1.  Nella pagina **Seleziona un disco** , scegliere **Crea un nuovo disco virtuale**. Fare clic su **Avanti**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image31.png)

1.  Nella pagina **Crea un disco** , modificare la **Dimensione del disco** a 500 GB (o più). Sotto **Il Provisioning di disco**, selezionare **Sottile provisioning**. Fare clic su **Avanti**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image32.png)

1.  Nella pagina **Opzioni avanzate** accettare i valori predefiniti.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image33.png)

1.  Nella pagina **pronti a completare** esaminare le opzioni del disco. Fare clic su **Fine**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image34.png)

1.  A questo punto si tornerà alla pagina proprietà macchina virtuale. Un nuovo disco rigido viene aggiunta alla macchina virtuale. Fare clic su **Fine**.
  
    ![](./media/storsimple-ova-deploy2-provision-vmware/image35.png)

2.  Con il computer virtuale selezionato nel riquadro destro, passare alla scheda **Riepilogo** . Rivedere le impostazioni per la macchina virtuale.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image36.png)

A questo punto viene completato il provisioning macchina virtuale. Il passaggio successivo consiste nel power nel computer in uso e ottenere l'indirizzo IP.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Passaggio 3: Avviare il dispositivo virtuale e ottenere l'indirizzo IP

Eseguire la procedura seguente per avviare il dispositivo virtuale e la connessione.

#### <a name="to-start-the-virtual-device"></a>Per avviare il dispositivo virtuale

1.  Avviare il dispositivo virtuale. In vSphere Configuration Manager, nel riquadro a sinistra, selezionare il dispositivo e pulsante destro del mouse per visualizzare il menu di scelta rapida. Selezionare **Power** e quindi **accendere**. Questa operazione deve power sulla macchina virtuale. È possibile visualizzare lo stato nel riquadro **Attività recenti** nella parte inferiore del client vSphere.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image37.png)

1.  Le attività di configurazione richiederà pochi minuti per completare. Dopo il dispositivo è in esecuzione, passare alla scheda **Console** . Invia Ctrl + Alt + Canc per accedere al dispositivo. In alternativa, è possibile posizionare il cursore sulla finestra e premere Ctrl + Alt + ins. L'utente predefinito è *StorSimpleAdmin* e la password predefinita è *Password1*.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image38.png)

1.  Per motivi di sicurezza, la password di amministratore del dispositivo scade al primo accesso. Verrà richiesto di cambiare la password.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image39.png)

1.  Immettere una password che contenga almeno 8 caratteri. La password deve contenere 3 di 4 di questi requisiti: caratteri maiuscoli, minuscoli, numerici e speciali. Immettere nuovamente la password per confermarla. L'utente verrà informato che la password è stata modificata.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image40.png)

1.  Dopo la password viene modificata, è possibile riavviare il dispositivo virtuale. Attendere il riavvio del computer completare. Console di Windows PowerShell del dispositivo potrebbe essere visualizzata insieme a un indicatore di stato.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image41.png)

1.  Procedura 6-8 si applica solo all'avvio in un ambiente non DHCP. Se si è in un ambiente DHCP, ignorare questi passaggi e andare al passaggio 9. Se è stato avviato il dispositivo in ambiente non DHCP, verrà visualizzata la schermata seguente. 

    ![](./media/storsimple-ova-deploy2-provision-vmware/image42m.png)

    È ora necessario configurare la rete.

1.  Utilizzare la `Get-HcsIpAddress` comando per elencare le interfacce di rete attivate nel dispositivo virtuale. Se il dispositivo ha una singola interfaccia di rete abilitata, il nome predefinito assegnato a questa interfaccia è `Ethernet`.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image43m.png)

1.  Utilizzare la `Set-HcsIpAddress` cmdlet per configurare la rete. Come illustrato di seguito:


    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-ova-deploy2-provision-vmware/image44.png)

1.  Dopo aver completata la configurazione iniziale e si è avviata il dispositivo, verrà visualizzato il testo dell'intestazione di dispositivo. Prendere nota dell'indirizzo IP e l'URL visualizzato il testo dell'intestazione per gestire il dispositivo. Utilizzare questo indirizzo IP per connettersi all'interfaccia del dispositivo virtuale web e completare l'installazione locale e la registrazione.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image45.png)


1. (Facoltativo) Eseguire questo passaggio solo se si distribuisce il dispositivo nel Cloud per enti pubblici. Si verrà ora attiva la modalità di Stati Uniti Federal Information Processing Standard (FIPS) nel dispositivo. Lo standard FIPS 140 definisce algoritmi approvati per l'utilizzo per sistemi di computer per enti pubblici Federal IT per la protezione dei dati riservati.
    1. Per attivare la modalità FIPS, eseguire il cmdlet seguente:
        
        `Enter-HcsFIPSMode`

    2. Dopo aver attivato la modalità FIPS in modo che compaiano convalida crittografia, riavviare il dispositivo.

        > [AZURE.NOTE] È possibile attivare o disattivare la modalità FIPS nel dispositivo. Alternate dispositivo tra la modalità FIPS e non FIPS non è supportata.


Se il dispositivo non soddisfa i requisiti minimi di configurazione, verrà visualizzato un errore nel testo dell'intestazione (come illustrato di seguito). È necessario modificare la configurazione del dispositivo in modo che abbia risorse adeguate per soddisfare i requisiti minimi. È possibile riavviare e connettersi al dispositivo. Fare riferimento ai requisiti minimi di configurazione in [passaggio 1: assicurarsi che il sistema host soddisfi i requisiti minimi dispositivo virtuale](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-vmware/image46.png)

Se si affrontare qualsiasi altro tipo di errore durante la configurazione iniziale tramite l'interfaccia web locale, consultare i seguenti flussi di lavoro [Gestione la matrice virtuale StorSimple tramite l'interfaccia web locale](storsimple-ova-web-ui-admin.md).

-   Eseguire test di diagnostica per [risolvere i problemi di installazione dell'interfaccia utente web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Pacchetto log genera e visualizzare i file di log](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Passaggi successivi

-   [Impostare la matrice virtuale StorSimple come file server](storsimple-ova-deploy3-fs-setup.md)

-   [Impostare la matrice virtuale StorSimple come server iSCSI](storsimple-ova-deploy3-iscsi-setup.md)

