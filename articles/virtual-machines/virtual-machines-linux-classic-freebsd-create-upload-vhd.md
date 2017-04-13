<properties
   pageTitle="Creare e caricare un'immagine FreeBSD VM | Microsoft Azure"
   description="Informazioni su come creare e caricare un disco rigido virtuale (disco rigido virtuale) che contiene il sistema operativo FreeBSD per creare una macchina virtuale Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/29/2016"
   ms.author="kyliel"/>

# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Creare e caricare un VHD FreeBSD Azure

In questo articolo viene illustrato come creare e caricare un disco rigido virtuale (disco rigido virtuale) che contiene il sistema operativo FreeBSD. Dopo il caricamento di esso, è possibile utilizzare come la propria immagine per creare una macchine () in Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## <a name="prerequisites"></a>Prerequisiti
In questo articolo si presuppone che sia gli elementi seguenti:

- **Azure un abbonamento**: se non si dispone di un account, è possibile creare in pochi minuti. Se si dispone di un abbonamento MSDN, vedere [credito Azure mensile per gli abbonati a Visual Studio.](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). In caso contrario, informazioni su come [creare un account di valutazione gratuito](https://azure.microsoft.com/pricing/free-trial/).  

- **Strumenti di azure PowerShell**, PowerShell di Azure il modulo deve essere installato e configurato per usare l'abbonamento Azure. Per scaricare il modulo, vedere [download di Azure](https://azure.microsoft.com/downloads/). Un'esercitazione che descrive come installare e configurare il modulo è disponibile qui. Utilizzare il cmdlet [Azure download](https://azure.microsoft.com/downloads/) per caricare il disco rigido virtuale.

- **Sistema operativo FreeBSD installato in un file con estensione vhd**- supportate FreeBSD installato un sistema operativo deve essere un disco rigido virtuale. Sono disponibili più strumenti per creare file con estensione vhd. Ad esempio, è possibile utilizzare una soluzione di virtualizzazione, ad esempio Hyper-V per creare il file con estensione vhd e installare il sistema operativo. Per istruzioni su come installare e usare Hyper-V, vedere [installare Hyper-V e creare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Nel nuovo formato VHDX non è supportato in Azure. È possibile convertire il disco in formato disco rigido virtuale utilizzando Hyper-V Manager o i cmdlet [disco rigido virtuale Converti](https://technet.microsoft.com/library/hh848454.aspx). Inoltre, esiste un' [esercitazione su MSDN su come utilizzare FreeBSD con Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).

Questa operazione include i cinque passaggi seguenti.

## <a name="step-1-prepare-the-image-for-upload"></a>Passaggio 1: Preparare l'immagine per il caricamento

Sul computer virtuale in cui è installato il sistema operativo FreeBSD, completare le procedure seguenti:

1. Attivare il DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart

2. Attivare SSH.

    SSH è attivata per impostazione predefinita dopo l'installazione da disco. Se non è abilitata per qualche motivo o se si usa FreeBSD VHD direttamente, digitare quanto segue:

        # echo 'sshd_enable="YES"' >> /etc/rc.conf
        # ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
        # ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
        # service sshd restart

3. Configurare una console seriale.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf

4. Installare sudo.

    L'account principale è disabilitata in Azure. Di conseguenza, che è necessario utilizzare sudo da un utente senza privilegi per l'esecuzione di comandi con privilegi elevati.

        # pkg install sudo
;
5. Prerequisiti per l'agente di Azure.

        # pkg install python27  
        # pkg install Py27-setuptools27   
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git

6. Installare agente Azure.

    La versione più recente dell'agente di Azure può sempre trovarsi in [github](https://github.com/Azure/WALinuxAgent/releases). Versione 2.0.10 + ufficiale supporta 10 FreeBSD & 10,1 e la versione 2.1.4 ufficiale supporta FreeBSD 10.2 e versioni successive.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    Per 2.0, utilizzare 2.0.16 ad esempio:

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    Nella versione 2.1, utilizzare 2.1.4 ad esempio:

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

    >[AZURE.IMPORTANT] Dopo l'installazione dell'agente di Azure, è consigliabile verificare che sia in esecuzione:

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # service –e | grep waagent
        /etc/rc.d/waagent
        # cat /var/log/waagent.log

7. Eseguire il sistema.

    Eseguire il sistema per pulire e renderli più adatto per il provisioning di nuovo. Il seguente comando Elimina anche l'account utente provisioning ultimo e i dati associati:

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    A questo punto è possibile arrestare la macchina virtuale.

## <a name="step-2-create-a-storage-account-in-azure"></a>Passaggio 2: Creare un account di archiviazione in Azure ##

È necessario un account di archiviazione in Azure caricare un file con estensione vhd in modo che possono essere usata per creare una macchina virtuale. È possibile usare il portale classico Azure per creare un account di archiviazione.

1. Accedere al [portale classica Azure](https://manage.windowsazure.com).

2. Sulla barra dei comandi selezionare **Nuovo**.

3. Selezionare **i servizi di dati** > **lo spazio di archiviazione** > **Creazione rapida**.

    ![Rapidi creare un account di archiviazione](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-quick-create.png)

4. Compilare i campi nel modo seguente:

    - Nel campo **URL** digitare un nome di sottodominio da utilizzare nell'URL account lo spazio di archiviazione. La voce può contenere da 3-24 numeri e lettere minuscole. Questo nome diventa il nome host all'interno dell'URL che viene utilizzato per risolvere archiviazione Blob Azure, lo spazio di archiviazione di Azure coda o le risorse di archiviazione tabella Azure per la sottoscrizione.

    - Nel menu a discesa **Gruppo percorso/affinità** , scegliere la **posizione o gruppo affinità** per l'account di archiviazione. Un gruppo di affinità consente di inserire i servizi cloud e lo spazio di archiviazione in dello stesso data center.

    - Nel campo **replica** decidere se usare replica **Ridondanti geografico** per l'account di archiviazione. La replica geografico è attivata per impostazione predefinita. Questa opzione replica i dati in una posizione secondaria, senza costi per l'utente, in modo che lo spazio di archiviazione ha esito negativo su fino a tale posizione se si verifica un errore principale in corrispondenza della posizione principale. Posizione secondaria viene assegnata automaticamente e non può essere modificata. Se è necessario un maggiore controllo la posizione dello spazio di archiviazione basato sul cloud a causa di requisiti legali o criteri dell'organizzazione, è possibile disattivare la replica geografico. Tuttavia, tenere presente che se si attiva in un secondo momento geografico replica, viene addebitata una Commissione di trasferimento dati unica replicare i dati esistenti in posizione secondaria. Servizi di archiviazione senza replica geografico sono offerti uno sconto. Ulteriori informazioni sulla gestione della replica geografico degli account di archiviazione sono disponibili qui: [creare, gestire, o eliminare un account di archiviazione](../storage-create-storage-account/#replication-options).

    ![Immettere i dettagli di account di archiviazione](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-create-account.png)


5. Selezionare **Crea l'Account di archiviazione**. L'account verrà visualizzata sotto **lo spazio di archiviazione**.

    ![Account di archiviazione completata](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storagenewaccount.png)

6. Creare un contenitore per i file caricati VHD. Selezionare il nome dell'account di archiviazione e quindi selezionare **i contenitori**.

    ![Dettagli account lo spazio di archiviazione](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_detail.png)

7. Selezionare **Crea un contenitore**.

    ![Dettagli account lo spazio di archiviazione](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_container.png)

8. Nel campo **nome** digitare un nome per il contenitore. Quindi, nel menu a discesa **accesso** , selezionare il tipo di criterio di accesso desiderato.

    ![Nome del contenitore](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_containervalues.png)

    > [AZURE.NOTE] Per impostazione predefinita, il contenitore è privato e accessibili solo tramite il proprietario dell'account. Per consentire l'accesso in lettura pubblico per BLOB nel contenitore, ma non per le proprietà del contenitore e dei metadati, utilizzare l'opzione **Blob pubblico** . Per consentire l'accesso in lettura pubblica completa per il contenitore BLOB, utilizzare l'opzione **Contenitore pubblico** .

## <a name="step-3-prepare-the-connection-to-azure"></a>Passaggio 3: Preparare la connessione al Azure

È possibile caricare un file con estensione vhd, è necessario stabilire una connessione protetta tra il computer e l'abbonamento Azure. Per eseguire questa operazione, è possibile utilizzare il metodo di Azure Active Directory (Azure Active Directory) oppure il certificato.

### <a name="use-the-azure-ad-method-to-upload-a-vhd-file"></a>Utilizzare il metodo di Azure Active Directory per caricare un file con estensione vhd

1. Aprire la console di Azure PowerShell.

2. Digitare il comando seguente:  
    `Add-AzureAccount`

    Apre una finestra di accesso in cui è possibile accedere con l'account aziendale o dell'istituto di istruzione.

    ![Finestra di PowerShell](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/add_azureaccount.png)

3. Azure autentica e consente di salvare le informazioni sulle credenziali. Quindi chiude la finestra.

### <a name="use-the-certificate-method-to-upload-a-vhd-file"></a>Usare il certificato per caricare un file con estensione vhd

1. Aprire la console di Azure PowerShell.

2. Type:  `Get-AzurePublishSettingsFile`.

3. Una finestra del browser si apre e viene richiesto di scaricare un file publishsettings. Il file contiene informazioni e un certificato per l'abbonamento Azure.

    ![Pagina di download del browser](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)

3. Salvare il file publishsettings.

4. Type:  `Import-AzurePublishSettingsFile <PathToFile>`, dove `<PathToFile>` è il percorso completo del file publishsettings.

   Per ulteriori informazioni, vedere [Guida introduttiva a cmdlet Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx).

   Per ulteriori informazioni sull'installazione e configurazione di PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## <a name="step-4-upload-the-vhd-file"></a>Passaggio 4: Caricare file con estensione vhd

Quando si carica i file con estensione vhd, è possibile inserirla in un punto qualsiasi all'interno di spazio di archiviazione Blob. Ecco alcuni termini in cui si userà quando si carica i file:
-  **BlobStorageURL** è l'URL per l'account di archiviazione che è stato creato nel passaggio 2.
-  **YourImagesFolder** è il contenitore all'interno dell'archiviazione Blob in cui si desidera archiviare le immagini.
- **VHDName** è l'etichetta visualizzata nel portale di classica Azure per identificare il disco rigido virtuale.
- **PathToVHDFile** è il percorso completo e il nome del file con estensione vhd.


Nella finestra di PowerShell Azure usato nel passaggio precedente, digitare:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-5-create-a-vm-with-the-uploaded-vhd-file"></a>Passaggio 5: Creare una macchina virtuale con i file con estensione vhd caricato
Dopo il caricamento di file con estensione vhd, è possibile aggiungere come un'immagine nell'elenco di immagini personalizzate che sono associate all'abbonamento e creare una macchina virtuale con questa immagine personalizzata.

1. Nella finestra di PowerShell Azure usato nel passaggio precedente, digitare:

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

    > [AZURE.NOTE]Utilizzare Linux come tipo di sistema operativo. La versione corrente di Azure PowerShell accetta solo "Linux" o "Windows" come parametro.

2. Dopo aver completato la procedura precedente, la nuova immagine viene elencata quando si sceglie la scheda **immagini** nel portale di classica Azure.  

    ![Scegliere un'immagine](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/addfreebsdimage.png)

3. Creare una macchina virtuale dalla raccolta. Questa nuova immagine è ora disponibile in **Immagini personali**.
4. Selezionare la nuova immagine. Passare alle istruzioni visualizzate per configurare un nome host, password, SSH chiave e così via.

    ![Immagine personalizzata](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/createfreebsdimageinazure.png)

4. Dopo aver completato il provisioning, verrà visualizzato il VM FreeBSD in esecuzione in Azure.

    ![Immagine di FreeBSD in azure](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/freebsdimageinazure.png)
