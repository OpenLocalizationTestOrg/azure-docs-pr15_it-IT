<properties
    pageTitle="Configurare una macchina virtuale come server blocco appunti IPython | Microsoft Azure"
    description="Impostare backup una Azure macchina virtuale per l'utilizzo in un ambiente di scienze dati con IPython Server per analitica avanzate."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="set-up-an-azure-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Configurare una macchina virtuale Azure come un server di blocco appunti IPython per analitica avanzate

In questo argomento viene illustrato come preparare e configurare una macchina virtuale Azure per analitica avanzate che può essere utilizzato come parte di un ambiente di scienze di dati. La macchina virtuale di Windows è configurata con strumenti come ad esempio blocco appunti IPython, Esplora archivi Azure, AzCopy, nonché altre utilità sono utili per i progetti analitica avanzate di supporto. Esplora archivi Azure e AzCopy, ad esempio, fornire agevolmente per caricare dati in archiviazione blob Azure dal computer locale o per scaricare nel computer locale dallo spazio di archiviazione blob.

## <a name="create-vm"></a>Passaggio 1: Creare un generiche Azure macchina virtuale

Se si dispone già di una macchina virtuale Azure e appena si vuole configurare un server IPython blocco appunti, è possibile ignorare questo passaggio e passare alla [passaggio 2: aggiungere un endpoint per i blocchi appunti IPython a una macchina virtuale esistente](#add-endpoint).

Prima di avviare il processo di creazione di una macchina virtuale in Azure, è necessario determinare le dimensioni del computer in cui è necessaria per elaborare i dati per il proprio progetto. Computer più piccole hanno meno memoria e meno core CPU più grandi computer, ma sono anche meno costose. Per un elenco dei tipi di computer e i prezzi, vedere la pagina <a href="http://azure.microsoft.com/pricing/details/virtual-machines/" target="_blank">Macchine virtuali di prezzi</a>

1. Accedere al <a href="https://manage.windowsazure.com" target="_blank">Portale classica Azure</a>e fare clic su **Nuovo** nell'angolo inferiore sinistro. Verrà visualizzata una finestra. Selezionare **il calcolo** -> **macchina virtuale** -> **Da raccolta**.

    ![Creare l'area di lavoro][24]

2. Scegliere una delle seguenti immagini:

    * Data Center di Windows Server 2012 R2
    * Esperienza di nozioni di base di Windows Server (Windows Server 2012 R2)

    Quindi fare clic sulla freccia rivolta verso destra in basso a destra per visualizzare la pagina di configurazione successiva.

    ![Creare l'area di lavoro][25]

3. Immettere un nome per la macchina virtuale che si desidera creare, selezionare le dimensioni del computer (predefinito: A3) in base alle dimensioni dei dati nel computer sta per processo e potenti come desiderato il computer da (dimensione della memoria e il numero di calcolo core), immettere un nome utente e la password per il computer. Quindi fare clic sulla freccia rivolta verso destra per tornare alla pagina di configurazione successiva.

    ![Creare l'area di lavoro][26]

4. Selezionare **L'area geografica/AFFINITÀ gruppo/virtuale rete** che contiene l' **ACCOUNT di archiviazione** che si prevede di utilizzare per questa macchina virtuale e quindi selezionare l'account di archiviazione. Aggiungere un endpoint nella parte inferiore del campo **endpoint** , immettere il nome dell'endpoint (di seguito per "IPython"). È possibile scegliere qualsiasi stringa come **nome** del punto finale e qualsiasi numero intero compreso tra 0 e 65536 **disponibili** come **Porta pubblica**. La **Porta privato** deve essere **9999**. Gli utenti devono **evitare** l'utilizzo di porte pubbliche che sono già state assegnate per i servizi internet. <a href="http://www.chebucto.ns.ca/~rakerman/port-table.html" target="_blank">Porte per i servizi Internet</a> comprende un elenco di porte che sono state assegnate ed evitare.

    ![Creare l'area di lavoro][27]

5. Fare clic sul segno di spunta per avviare la macchina virtuale processo di provisioning.

    ![Creare l'area di lavoro][28]


Può richiedere 25 15 minuti per completare la macchina virtuale processo di provisioning. Dopo aver creata la macchina virtuale, lo stato del computer in uso deve essere visualizzata come **in esecuzione**.

![Creare l'area di lavoro][29]

## <a name="add-endpoint"></a>Passaggio 2: Aggiungere un endpoint per i blocchi appunti IPython a una macchina virtuale esistente

Se è stata creata seguendo le istruzioni disponibili in passaggio 1, l'endpoint per IPython blocco appunti è già stato aggiunto e possibile saltare questo passaggio.

Se è necessario aggiungere un endpoint per IPython blocco appunti che si intende installare nel passaggio 3 sotto primo accesso al portale classica Azure la macchina virtuale esiste già, selezionare la macchina virtuale e aggiungere l'endpoint per server IPython blocco appunti. Nella figura seguente contiene una schermata del portale dopo il punto finale per IPython blocco appunti è stato aggiunto a una macchina virtuale di Windows.

![Creare l'area di lavoro][17]

## <a name="run-commands"></a>Passaggio 3: Installare blocco appunti IPython e altri strumenti di supporto

Dopo aver creata la macchina virtuale, utilizzare Remote protocollo RDP (Desktop) per accedere alla macchina virtuale di Windows. Per ulteriori informazioni, vedere [come accedere a una macchina virtuale che eseguono Windows Server](../virtual-machines/virtual-machines-windows-classic-connect-logon.md). Aprire il **prompt dei comandi** (**non nella finestra di comando di Powershell**) come **amministratore** ed eseguire il comando seguente.

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Al termine dell'installazione, il server di blocco appunti IPython viene avviato automaticamente nel *c:\\utenti\\\<nome utente\>\\documenti\\blocchi appunti IPython* directory.

Quando richiesto, immettere una password per il blocco appunti IPython e la password dell'amministratore del computer. In questo modo il blocco appunti IPython per l'esecuzione come servizio nel computer.

## <a name="access"></a>Passaggio 4: Accedere ai blocchi appunti IPython da un web browser
Per accedere al server IPython blocco appunti, aprire un web browser e l'input *https://&#60;virtual nome DNS >: & #60; numero di porta pubblica >* nella casella di testo URL. In questo caso, il *& #60; numero di porta pubblica >* dovrebbe essere il numero di porta specificato quando è stato aggiunto l'endpoint IPython blocco appunti.

La *& #60; macchina virtuale DNS nome >* disponibili sul portale classica di Azure. Dopo l'accesso al portale di classica, fare clic su **macchine VIRTUALI di**, selezionare il computer è stato creato e quindi selezionare **DASHBOARD**, il nome DNS verrà visualizzato come segue:

![Creare l'area di lavoro][19]

Si verifica un avviso per segnalare che non _esiste un problema con il certificato di sicurezza del sito Web_ (Internet Explorer) o _la connessione è non privata_ (Chrome), come illustrato nella figura riportata di seguito. Fare clic su **Continua per il sito Web (scelta non consigliato)** (Internet Explorer) o **Impostazioni avanzate** e quindi * *continua a & #60;* Nome DNS*> (potenzialmente pericolose) * * (Chrome) per continuare. Quindi immettere la password per accedere ai blocchi appunti IPython specificato in precedenza.

**Internet Explorer:**
![creare area di lavoro][20]

**Chrome:**
![creare area di lavoro][21]

Dopo l'accesso al blocco appunti IPython, una directory *DataScienceSamples* viene visualizzata nel browser. Questa directory contiene esempi IPython di blocchi appunti condivisi da Microsoft per consentire agli utenti di eseguire operazioni di ricerca di scienze di dati. Questi esempi di blocchi appunti IPython vengono estratti dal [**repository Github**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) alle macchine virtuali durante il server di blocco appunti IPython impostato processo. Microsoft mantiene e aggiorna questo archivio di frequente. Gli utenti possono visitare repository Github per ottenere i blocchi appunti IPython esempio aggiornati più di recente.
![Creare l'area di lavoro][18]

## <a name="upload"></a>Passaggio 5: Caricare un blocco appunti IPython esistente da un computer locale nel server di blocco appunti IPython

I blocchi appunti IPython offrono un modo semplice per gli utenti caricare un blocco appunti esistente IPython sul proprio computer locale nel server di blocco appunti IPython nelle macchine virtuali. Dopo che gli utenti accedono al blocco appunti IPython in un web browser, fare clic su nella **directory** caricati per il blocco appunti IPython. Selezionare un file di .ipynb IPython blocco appunti da caricare dal computer locale in **Esplora File**e quindi trascinarli nella directory IPython blocco appunti nel web browser. Fare clic sul pulsante **Carica** per caricare il file .ipynb nel server IPython blocco appunti. Altri utenti possono quindi iniziare a usarlo dal browser web.

![Creare l'area di lavoro][22]

![Creare l'area di lavoro][23]


##<a name="shutdown"></a>Arresto e rimozione dell'assegnazione macchina virtuale quando non è in uso

Azure macchine virtuali di prezzo come **pagare solo per i quali si utilizza**. Per garantire che non sta fatturato quando non si utilizza la macchina virtuale, deve essere impostato su **arrestato (Deallocated)** quando non è in uso.

> [AZURE.NOTE] Se si arresta la macchina virtuale dall'interno della macchina virtuale (con opzioni risparmio energia), la macchina virtuale viene interrotto ma rimane allocata. Per assicurarsi che non si continua a essere fatturato, interrompere sempre macchine virtuali dal [Portale classica Azure](http://manage.windowsazure.com/). È anche possibile interrompere la macchina virtuale tramite Powershell chiamando **ShutdownRoleOperation** con "PostShutdownAction" è uguale a "StoppedDeallocated".

Per arrestare e rilasciare la macchina virtuale:

1. Accedere al [Portale classica Azure](http://manage.windowsazure.com/) usando l'account.  

2. Selezionare **macchine VIRTUALI** dalla barra di spostamento sinistro.

3. Nell'elenco di macchine virtuali, fare clic sul nome del computer virtuale, quindi passare alla pagina del **DASHBOARD** .

4. Nella parte inferiore della pagina, scegliere **ARRESTA**.

![Arresto macchine Virtuali][15]

La macchina virtuale verranno rilasciata ma non eliminata. È possibile riavviare il computer virtuale in qualsiasi momento dal portale classica di Azure.

## <a name="your-azure-vm-is-ready-to-use-whats-next"></a>È possibile utilizzare le macchine Virtuali Azure: che cos'è successiva?

La macchina virtuale è ora possibile utilizzare gli esercizi di scienze di dati. La macchina virtuale è pronto per essere utilizzato come server IPython blocco appunti per l'esplorazione e l'elaborazione dei dati e altre attività in combinazione con Azure apprendimento e il processo di scienze dei dati del Team.

I passaggi del processo di scienze Team dei dati sono mappati nel [Percorso formativo](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) e potrebbero includere procedure dettagliate che spostare i dati nel HDInsight, elaborano e in tale posizione di esempio in preparazione per l'apprendimento dai dati di apprendimento di Azure computer.


[15]: ./media/machine-learning-data-science-setup-virtual-machine/vmshutdown.png
[17]: ./media/machine-learning-data-science-setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/machine-learning-data-science-setup-virtual-machine/sample-ipnbs.png
[19]: ./media/machine-learning-data-science-setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning-ie.png
[21]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning.png
[22]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-6.png
