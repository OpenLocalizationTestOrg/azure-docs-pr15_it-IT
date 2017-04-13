<properties
   pageTitle="Distribuire matrice virtuale StorSimple 1: preparazione del portale"
   description="Esercitazione prima di distribuire StorSimple matrice virtuale implica la preparazione del portale"
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
   ms.date="05/24/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---prepare-the-portal"></a>Distribuire StorSimple virtuale matrice - preparare il portale

![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## <a name="overview"></a>Panoramica

Questo articolo si applica alla versione di Microsoft Azure StorSimple virtuale in forma di matrice (noto anche come dispositivo virtuale locale StorSimple o periferica virtuale StorSimple) in esecuzione marzo 2016 disponibilità generale (GA). Questo articolo è il primo della serie di esercitazioni per la distribuzione necessari per distribuire completamente la matrice virtuale come un file server o su un server iSCSI. In questo articolo viene descritta la preparazione necessaria per creare e configurare il servizio di gestione StorSimple prima il provisioning di una matrice virtuale. In questo articolo si collega anche indietro per un elenco di controllo configurazione distribuzione nonché configurazione prerequisiti.

Sono necessari privilegi di amministratore per completare il processo di installazione e configurazione. È consigliabile esaminare l'elenco di controllo di configurazione di distribuzione prima di iniziare. Preparazione del portale richiederà inferiore a 10 minuti.

Le informazioni pubblicate in questo articolo si applicano alla distribuzione di StorSimple virtuale matrici nel portale classica Azure, nonché Cloud di Microsoft Azure per enti pubblici.

### <a name="get-started"></a>Guida introduttiva

Il flusso di lavoro di distribuzione è costituito da preparazione del portale, il provisioning di una matrice virtuale nell'ambiente virtualizzato e completamento dell'installazione. Per iniziare con la distribuzione di matrice virtuale StorSimple come un file server o su un server iSCSI, è necessario consultare le seguenti risorse tabulari (articoli e video).

#### <a name="deployment-articles"></a>Articoli di distribuzione

Consultare i seguenti articoli nella sequenza indicata per distribuire la matrice virtuale StorSimple.

| **#** | **In questo passaggio**                          | **Si esegue questa...**                                                         | **Usare questi documenti.**|
|------|-------------------------------------------|--------------------------------------------------------------------------------|------------------------|
|1.   | **Impostare il portale classico Azure**       | Creare e configurare il servizio di gestione StorSimple prima il provisioning di un dispositivo virtuale StorSimple.  |[Preparare il portale](storsimple-ova-deploy1-portal-prep.md)|
|2.   | **Effettuare il provisioning di matrice virtuale**           | Per Hyper-V, eseguire il provisioning e connettersi a un dispositivo virtuale StorSimple su un sistema di host con Hyper-V in Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2. <br></br> <br></br> Per VMware, eseguire il provisioning e connettersi a un StorSimple locale dispositivo virtuale in un sistema di host con VMware ESXi 5.5 e versioni successive.<br></br>| [Effettuare il provisioning di una matrice virtuale in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [Effettuare il provisioning di una matrice virtuale in VMware](storsimple-ova-deploy2-provision-vmware.md)|
|3.    | **Impostare la matrice virtuale**              | Per il server di file, eseguire l'installazione e configurazione iniziali, registrare il file server StorSimple e completare la configurazione di dispositivi. È quindi possibile eseguire il provisioning condivisioni piccole e medie imprese. <br></br> <br></br> Per il server iSCSI, eseguire l'installazione e configurazione iniziali, registrare il server di iSCSI StorSimple e completare la configurazione di dispositivi. È quindi possibile eseguire il provisioning volumi iSCSI.| [Configurare la matrice virtuale come file server](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[Configurare la matrice virtuale come server iSCSI](storsimple-ova-deploy3-iscsi-setup.md)|

#### <a name="deployment-videos"></a>Video di distribuzione

| **Per eseguire questo passaggio...** |  **Guardare questo video.**|
|----------------|-------------|
| Istruzioni dettagliate su come iniziare con la matrice virtuale StorSimple. | [Guida introduttiva a matrice virtuale StorSimple](https://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array/)|
| Istruzioni dettagliate per effettuare il provisioning di una matrice virtuale StorSimple in Hyper-V.|[Creare una matrice virtuale StorSimple](https://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array/) |
|Istruzioni dettagliate per configurare e registrare una matrice virtuale StorSimple|[Configurare una matrice virtuale StorSimple](https://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array/)|
|Istruzioni dettagliate per creare azioni, condivisioni il backup e ripristinare i dati in una matrice virtuale StorSimple configurato come un file server|[Utilizzare la matrice virtuale StorSimple](https://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array/)|
|Istruzioni dettagliate per failover e ripristino di emergenza di una matrice virtuale StorSimple|[StorSimple matrice virtuale di emergenza](https://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery/)

Ora è possibile iniziare a configurare il portale classico Azure.

## <a name="configuration-checklist"></a>Elenco di controllo di configurazione

L'elenco di controllo configurazione descrive le informazioni da raccogliere prima di configurare il software in un dispositivo StorSimple. Preparazione di queste informazioni anticipo consente di semplificare il processo di distribuzione dispositivo StorSimple nel proprio ambiente. A seconda che il dispositivo virtuale StorSimple verrà distribuito come un file server o su un server iSCSI, sarà necessario uno dei seguenti elenchi di controllo.

-   Scaricare l' [elenco di controllo StorSimple matrice virtuale File Server configurazione](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).

-   Scaricare la [matrice virtuale StorSimple iSCSI elenco di controllo configurazione Server](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Prerequisiti di

Qui sono disponibili i prerequisiti di configurazione per il servizio di gestione StorSimple, il dispositivo virtuale StorSimple e la rete Data Center.

### <a name="for-the-storsimple-manager-service"></a>Per il servizio di gestione StorSimple

Prima di iniziare, verificare che:

-   È l'account Microsoft con le credenziali di accesso.

-   È l'account di archiviazione di Microsoft Azure con credenziali di accesso.

-   L'abbonamento a Microsoft Azure deve essere abilitata per il servizio di gestione StorSimple.

### <a name="for-the-storsimple-virtual-device"></a>Per il dispositivo virtuale StorSimple

Prima di distribuire un dispositivo virtuale, verificare che:

-   È possibile accedere a un sistema di host che esegue Hyper-V in Windows Server 2008 R2 o versioni successive o VMware (ESXi 5,5 o versioni successive) che può essere utilizzati per una disposizione un dispositivo.

-   Il sistema host è in grado di dedicare le risorse seguenti per eseguire il provisioning dispositivo virtuale:

    -   Almeno 4 core.

    -   Almeno 8 GB di RAM.

    -   Un'interfaccia di rete.

    -   Un disco virtuale 500 GB per i dati di sistema.

### <a name="for-the-datacenter-network"></a>Per la rete Data Center

Prima di iniziare, verificare che:

-   La rete nel centro dati è configurata in base ai requisiti di reti del dispositivo StorSimple. Per ulteriori informazioni, vedere [Requisiti di sistema StorSimple virtuale in forma di matrice](storsimple-ova-system-requirements.md).

-   Il dispositivo virtuale StorSimple ha una larghezza di banda Internet Mbps 5 dedicato (o più) disponibili in qualsiasi momento. La larghezza di banda non dovrebbe essere condivisa con altre applicazioni.

## <a name="step-by-step-preparation"></a>Preparazione dettagliata

Utilizzare le seguenti istruzioni dettagliate per preparare il portale per il servizio di gestione StorSimple.

## <a name="step-1-create-a-new-service"></a>Passaggio 1: Creare un nuovo servizio

Una singola istanza del servizio di gestione StorSimple può gestire più dispositivi StorSimple 1200. Per creare una nuova istanza del servizio di gestione StorSimple, procedere come segue. Se si dispone di un servizio di gestione StorSimple esistente per gestire i dispositivi di 1200, ignorare questo passaggio e passare a [passaggio2: ottenere il codice di registrazione servizio](#step-2-get-the-service-registration-key).

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

> [AZURE.IMPORTANT]
>
> Se non è stato attivato la creazione di un account di archiviazione automatica con il servizio, sarà necessario creare almeno un account di archiviazione dopo aver creato un servizio.
>

> - Se non è stato creato automaticamente un account di archiviazione, passare a [Configura un nuovo account di archiviazione per il servizio](#optional-step-configure-a-new-storage-account-for-the-service) per istruzioni dettagliate.
>

> - Se è abilitata la creazione automatica di un account di archiviazione, passare a [passaggio 2: ottenere il codice di registrazione servizio](#step-2-get-the-service-registration-key).


## <a name="step-2-get-the-service-registration-key"></a>Passaggio 2: Ottenere il codice di registrazione di servizio


Dopo il servizio di gestione StorSimple sia in esecuzione, sarà necessario ottenere la chiave di registrazione del servizio. Questo tasto viene utilizzato per eseguire la registrazione e connettere il dispositivo StorSimple con il servizio.

Nel [portale classica Azure](https://manage.windowsazure.com/), procedere come segue.


[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

> [AZURE.NOTE]
>
> La chiave di registrazione del servizio viene utilizzata per registrare tutti i dispositivi di StorSimple Manager che è necessario registrare il servizio di gestione StorSimple.

## <a name="step-3-download-the-virtual-device-image"></a>Passaggio 3: Scaricare l'immagine di dispositivo virtuale

Dopo avere ottenuto la chiave di registrazione del servizio, sarà necessario scaricare l'immagine di dispositivo virtuale appropriata per effettuare il provisioning di un dispositivo virtuale nel sistema host. Le immagini di dispositivo virtuale sono sistema operativo specifico e possono essere scaricate dalla pagina Guida introduttiva nel portale di classica Azure.

> [AZURE.IMPORTANT] Il software in esecuzione su StorSimple virtuale matrice può essere usato solo in combinazione con il servizio di gestione Storsimple.


Nel [portale classica Azure](https://manage.windowsazure.com/), procedere come segue.

#### <a name="to-get-the-virtual-device-image"></a>Per ottenere l'immagine di dispositivo virtuale

1.  Nella pagina **servizio StorSimple Manager** , fare clic su servizio che è stato creato. Verrà visualizzata la pagina **Avvio rapido** . (È possibile fare clic sull'icona di avvio veloce ![](./media/storsimple-ova-deploy1-portal-prep/image8.png) per accedere alla pagina **Guida introduttiva** in qualsiasi momento.)

1.  Fare clic sul collegamento corrispondente all'immagine che si desidera scaricare da Microsoft Download Center. I file di immagine sono circa 4,8 GB.

    -   VHDX per Hyper-V in Windows Server 2012 e versioni successive

    -   Disco rigido virtuale per Hyper-V in Windows Server 2008 R2 e versioni successive

    -   VMDK per VMWare ESXi 5.5 e versioni successive

2.  Scaricare e decomprimere il file in un'unità locale, effettuare una nota di dove si trova il file decompresso.

![icona del video](./media/storsimple-ova-deploy1-portal-prep/video_icon.png) **Video disponibile**

Guardare il video per informazioni dettagliate su come iniziare con la matrice virtuale StorSimple.

> [AZURE.VIDEO get-started-with-the-storsimple-virtual-array]



## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Passaggio facoltativo: configurare un nuovo account di archiviazione per il servizio

Si tratta di un passaggio facoltativo che deve essere eseguita solo se non è stato attivato la creazione di un account di archiviazione automatica con il servizio.

Se è necessario creare un account di archiviazione Azure in un'area diversa, vedere [come creare un account di archiviazione](storage-create-storage-account.md#create-a-storage-account) per istruzioni dettagliate.

Nel [portale classica Azure](https://manage.windowsazure.com/) nella pagina servizio gestore StorSimple per aggiungere un account di archiviazione di Microsoft Azure esistente, procedere come segue.

#### <a name="to-add-a-storage-account"></a>Per aggiungere un account di archiviazione

1.  Il servizio di gestione StorSimple pagina di destinazione, selezionare il servizio e fare doppio clic su esso. Verrà visualizzata la pagina **Avvio rapido** . Selezionare la pagina **Configura** .

2.  Fare clic su **Aggiungi/Modifica account di archiviazione**. Nella finestra di dialogo **Aggiungi/modifica Account di archiviazione** , eseguire le operazioni seguenti:

    1.  Fare clic su **Aggiungi nuovo**.

    1.  Specificare un nome per il proprio account di archiviazione.

    1.  Fornire primaria **Tasto di scelta rapida** per l'account di archiviazione di Microsoft Azure.

    1.  Selezionare **Enable SSL modalità** per creare un canale sicuro per la comunicazione di rete tra il dispositivo e nel cloud. Deselezionare la casella di controllo **Enable SSL modalità** solo se si opera all'interno di un cloud privato.

    1.  Fare clic sull'icona di controllo ![](./media/storsimple-ova-deploy1-portal-prep/image7.png). Verrà visualizzato dopo l'account di archiviazione viene creato correttamente.

        ![](./media/storsimple-ova-deploy1-portal-prep/image11.png)

1.  Account di archiviazione appena creato verrà visualizzato nella pagina **Configura** account **lo spazio di archiviazione**. Fare clic su **Salva** per salvare l'account di archiviazione appena creato. Fare clic su **OK** quando viene richiesto di confermare l'operazione.


## <a name="next-step"></a>Passaggio successivo

Il passaggio successivo consiste nel effettuare il provisioning di una macchina virtuale per il dispositivo virtuale StorSimple. A seconda del sistema operativo host, vedere le istruzioni dettagliate in:

-   [Effettuare il provisioning di una matrice virtuale StorSimple in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md)

-   [Effettuare il provisioning di una matrice virtuale StorSimple in VMware](storsimple-ova-deploy2-provision-vmware.md)
