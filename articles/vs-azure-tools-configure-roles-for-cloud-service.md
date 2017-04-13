<properties
   pageTitle="Configurare i ruoli per un servizio Cloud Azure con Visual Studio | Microsoft Azure"
   description="Informazioni su come installare e configurare i ruoli per i servizi cloud Azure mediante Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="configure-the-roles-for-an-azure-cloud-service-with-visual-studio"></a>Configurare i ruoli per un servizio Cloud Azure con Visual Studio

Un servizio cloud Azure può contenere uno o più lavoro o ruoli web. Per ogni ruolo è necessario definire come impostare il ruolo e inoltre configurare la modalità di esecuzione di tale ruolo. Per ulteriori informazioni sui ruoli di servizi cloud, vedere il video [Introduzione ai servizi Cloud Windows Azure](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). Le informazioni per il servizio cloud archiviate nei file seguenti:

- **ServiceDefinition.csdef**

    File di definizione del servizio consente di definire le impostazioni di runtime per il servizio cloud, inclusi i ruoli necessari, i punti finali e le dimensioni del computer virtuale. Nessuno dei dati memorizzati in questo file possono essere modificate quando il proprio ruolo è in esecuzione.

- **ServiceConfiguration. cscfg**

    Il file di configurazione del servizio configura quante istanze di un ruolo da eseguire e i valori delle impostazioni definite per un ruolo. I dati archiviati in questo file possono essere modificati mentre il proprio ruolo è in esecuzione.

Per poter archiviare valori diversi per le impostazioni per la modalità di esecuzione il proprio ruolo, è più configurazioni di servizio. È possibile utilizzare una configurazione di servizi diversa per ogni ambiente di distribuzione. Ad esempio, è possibile impostare la stringa di connessione dello spazio di archiviazione account usare emulatore archiviazione Azure locale in una configurazione del servizio locale e creare un'altra configurazione del servizio per utilizzare l'archiviazione Azure nel cloud.

Quando si crea un nuovo servizio cloud Azure in Visual Studio, due configurazioni di servizio vengono create per impostazione predefinita. Queste configurazioni vengono aggiunti al progetto Azure. Le configurazioni sono denominate:

- ServiceConfiguration.Cloud.cscfg

- ServiceConfiguration.Local.cscfg

## <a name="configure-an-azure-cloud-service"></a>Configurare un servizio cloud Azure

È possibile configurare un servizio cloud Azure da Esplora soluzioni in Visual Studio, come illustrato nella figura seguente.

![Configurare il servizio Cloud](./media/vs-azure-tools-configure-roles-for-cloud-service/IC713462.png)

### <a name="to-configure-an-azure-cloud-service"></a>Per configurare un servizio cloud Azure

1. Per configurare ogni ruolo di un progetto Azure da **Esplora soluzioni**, aprire il menu di scelta rapida per il ruolo nel progetto di Azure e quindi scegliere **proprietà**.

    Nell'editor di Visual Studio viene visualizzata una pagina con il nome del ruolo. La pagina consente di visualizzare i campi per la scheda **configurazione** .

1. Nell'elenco **Di configurazione del servizio** , scegliere il nome della configurazione del servizio che si desidera modificare.

    Se si desidera apportare modifiche a tutte le configurazioni dei servizi per questo ruolo, è possibile scegliere **Tutte le configurazioni**.

    >[AZURE.IMPORTANT] Se si sceglie una configurazione del servizio specifici, alcune proprietà sono disattivati in quanto può essere impostati solo per tutte le configurazioni. Per modificare queste proprietà, è necessario scegliere tutte le configurazioni.

    È possibile scegliere una scheda per aggiornare tutte le proprietà abilitate per la vista.

## <a name="change-the-number-of-role-instances"></a>Modificare il numero di istanze del ruolo

Per migliorare le prestazioni del servizio cloud, è possibile modificare il numero di istanze di un ruolo che sono in esecuzione, in base al numero di utenti o il carico previsto per un particolare ruolo. Un computer virtuale separato viene creato per ogni istanza di un ruolo quando viene eseguito il servizio cloud in Azure. Questa operazione modificherà la fatturazione per la distribuzione di questo servizio cloud. Per ulteriori informazioni sulla fatturazione, vedere [informazioni sulla fattura per Microsoft Azure](billing/billing-understand-your-bill.md).

### <a name="to-change-the-number-of-instances-for-a-role"></a>Per modificare il numero di istanze per un ruolo

1. Scegliere la scheda **configurazione** .

1. Nell'elenco **Di configurazione del servizio** , scegliere la configurazione del servizio che si desidera aggiornare.

    >[AZURE.NOTE] È possibile impostare il conteggio delle istanze per una configurazione specifica del servizio o per tutte le configurazioni di servizio.

1. Nella casella di testo **conteggio delle istanze** , immettere il numero di istanze che si desidera iniziare per questo ruolo.

    >[AZURE.NOTE] Ogni istanza viene eseguita in un computer virtuale separato quando si pubblica il servizio cloud in Azure.

1. Scegliere il pulsante **Salva** sulla barra degli strumenti per salvare le modifiche al file di configurazione del servizio.

## <a name="manage-connection-strings-for-storage-accounts"></a>Gestire le stringhe di connessione per gli account di archiviazione

È possibile aggiungere, rimuovere o modificare le stringhe di connessione per le configurazioni di servizio. È consigliabile stringhe di connessione diverse per le configurazioni di servizi diversa. Ad esempio, può essere utile una stringa di connessione locale per una configurazione del servizio locale contenente un valore di `UseDevelopmentStorage=true`. È consigliabile inoltre configurare un servizio cloud che utilizza un account di archiviazione in Azure.

>[AZURE.WARNING] Quando si immettono le informazioni di chiave account Azure dello spazio di archiviazione per una stringa di connessione di account di archiviazione, queste informazioni vengono archiviate in locale nel file di configurazione del servizio. Tuttavia, queste informazioni attualmente non memorizzate come testo crittografato.

Utilizzando un valore diverso per ogni configurazione del servizio, non è necessario utilizzare stringhe di connessione diverse del servizio cloud o modificare il codice quando si pubblica il servizio cloud in Azure. È possibile utilizzare lo stesso nome per la stringa di connessione nel codice e il valore sarà variano in base alla configurazione del servizio che si seleziona quando si creano i servizi cloud oppure quando viene pubblicato.

### <a name="to-manage-connection-strings-for-storage-accounts"></a>Per gestire le stringhe di connessione per gli account di archiviazione

1. Scegliere la scheda **Impostazioni** .

1. Nell'elenco **Di configurazione del servizio** , scegliere la configurazione del servizio che si desidera aggiornare.

    >[AZURE.NOTE] È possibile aggiornare le stringhe di connessione per una configurazione del servizio specifico, ma se è necessario aggiungere o eliminare una stringa di connessione è necessario selezionare tutte le configurazioni.

1. Per aggiungere una stringa di connessione, scegliere il pulsante **Aggiungi impostazione** . Viene aggiunta una nuova voce all'elenco.

1. Nella casella di testo **nome** digitare il nome che si desidera utilizzare per la stringa di connessione.

1. Nell'elenco a discesa **tipo** scegliere **Stringa di connessione**.

1. Per modificare il valore della stringa di connessione, scegliere i puntini di sospensione (...). Viene visualizzata la finestra di dialogo **Crea stringa di connessione di spazio di archiviazione** .

1. Per utilizzare l'emulatore account archivio locale, fare clic sul pulsante di opzione **emulatore lo spazio di archiviazione di Microsoft Azure** e quindi scegliere il pulsante **OK** .

1. Per usare un account di archiviazione in Azure, fare clic sul pulsante di opzione **l'abbonamento** e selezionare l'account di archiviazione desiderato.

1. Per utilizzare credenziali personalizzate, fare clic sul pulsante Opzioni **immesso manualmente le credenziali** di seguito. Immettere il nome dell'account di archiviazione e la chiave primaria o nella seconda. Per informazioni su come creare un account di archiviazione e su come immettere i dettagli dell'account di archiviazione nella finestra di dialogo **Crea stringa di connessione di spazio di archiviazione** , vedere [preparare per pubblicare o distribuire un'applicazione Azure da Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Per eliminare una stringa di connessione, selezionare la stringa di connessione e quindi fare clic sul pulsante **Rimuovi impostazione** .

1. Fare clic sull'icona **Salva** sulla barra degli strumenti per salvare le modifiche al file di configurazione del servizio.

1. Per visualizzare la stringa di connessione nel file di configurazione del servizio, è necessario ottenere il valore dell'impostazione di configurazione. Il codice seguente mostra un esempio in cui è stato creato archiviazione blob e caricati utilizzando una stringa di connessione dati `MyConnectionString` dal file di configurazione del servizio quando un utente sceglie **Button1** nella pagina default. aspx nel ruolo web per un servizio cloud Azure. Aggiungere le seguenti istruzioni Default.aspx.cs using:

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Aprire Default.aspx.cs in visualizzazione struttura e aggiungere un pulsante dalla casella degli strumenti. Aggiungere il codice seguente per il `Button1_Click` metodo. Questo codice utilizza `GetConfigurationSettingValue` per ottenere il valore dal file di configurazione del servizio per la stringa di connessione. Viene creato un blob nell'account di archiviazione che fa riferimento nella stringa di connessione `MyConnectionString` e infine il programma di testo all'interno del blob.

    ```
    protected void Button1_Click(object sender, EventArgs e)
    {
        // Setup the connection to Azure Storage
        var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("MyConnectionString"));
        var blobClient = storageAccount.CreateCloudBlobClient();
        // Get and create the container
        var blobContainer = blobClient.GetContainerReference("quicklap");
        blobContainer.CreateIfNotExists();
        // upload a text blob
        var blob = blobContainer.GetBlockBlobReference(Guid.NewGuid().ToString());
        blob.UploadText("Hello Azure");

    }
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Aggiungere le impostazioni personalizzate per utilizzare il servizio cloud Azure

Impostazioni personalizzate nel file di configurazione del servizio consentono di inserire un nome e un valore per una stringa per una configurazione del servizio specifico. È possibile scegliere di utilizzare questa impostazione per configurare una caratteristica del servizio cloud, leggere il valore dell'impostazione e usando questo valore per controllare la logica nel codice. È possibile modificare questi valori di configurazione del servizio senza dover ricreare il pacchetto di servizio o quando viene eseguito il servizio cloud. Il codice possibile controllare le notifiche di quando l'impostazione cambia. Vedere [RoleEnvironment.Changing evento](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

È possibile aggiungere, rimuovere o modificare le impostazioni personalizzate per le configurazioni di servizio. È consigliabile valori diversi per le stringhe per le configurazioni di servizi diversa.

Utilizzando un valore diverso per ogni configurazione del servizio, non è necessario utilizzare stringhe diverse del servizio cloud o modificare il codice quando si pubblica il servizio cloud in Azure. È possibile utilizzare lo stesso nome per la stringa nel codice e il valore sarà variano in base alla configurazione del servizio che si seleziona quando si creano i servizi cloud oppure quando viene pubblicato.

### <a name="to-add-custom-settings-to-use-in-your-azure-cloud-service"></a>Per aggiungere impostazioni personalizzate per utilizzare il servizio cloud Azure

1. Scegliere la scheda **Impostazioni** .

1. Nell'elenco **Di configurazione del servizio** , scegliere la configurazione del servizio che si desidera aggiornare.

    >[AZURE.NOTE] È possibile aggiornare le stringhe per una configurazione del servizio specifico, ma se è necessario aggiungere o eliminare una stringa, è necessario selezionare **Tutte le configurazioni**.

1. Per aggiungere una stringa, scegliere il pulsante **Aggiungi impostazione** . Viene aggiunta una nuova voce all'elenco.

1. Nella casella di testo **nome** digitare il nome che si desidera utilizzare per la stringa.

1. Nell'elenco a discesa **tipo** selezionare **stringa**.

1. Per aggiungere o modificare il valore di stringa, nella casella di testo **valore** digitare il nuovo valore.

1. Per eliminare una stringa, selezionare la stringa e quindi fare clic sul pulsante **Rimuovi impostazione** .

1. Scegliere il pulsante **Salva** sulla barra degli strumenti per salvare le modifiche al file di configurazione del servizio.

1. Per accedere alla stringa nel file di configurazione del servizio, è necessario ottenere il valore dell'impostazione di configurazione.

    È necessario assicurarsi che la descritta di seguito le istruzioni sono già state aggiunte a Default.aspx.cs allo stesso modo della procedura precedente.

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Aggiungere il codice seguente per il `Button1_Click` per accedere a questa stringa nello stesso modo in cui si accede a una stringa di connessione. Il codice eseguire il codice specifico in base al valore della stringa di impostazioni per il file di configurazione del servizio utilizzato.

    ```
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("MySetting");
    if (settingValue == “ThisValue”)
    {
    // Perform these lines of code
    }
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>Gestire l'archiviazione locale per ogni istanza del ruolo

È possibile aggiungere lo spazio di archiviazione di file locale del sistema per ogni istanza di un ruolo. È possibile archiviare i dati locali di seguito non è necessario accedere da altri ruoli. Tutti i dati che non è necessario salvare in una tabella, blob o lo spazio di archiviazione di Database SQL possono essere creati in questa posizione. Ad esempio, è possibile utilizzare questo archivio locale per memorizzare i dati che dovranno essere riutilizzato. Questi dati archiviati non sono accessibili da altre istanze di un ruolo. 

Impostazioni di archiviazione locale applicano a tutte le configurazioni di servizio. È possibile aggiungere solo, rimuovere o modificare lo spazio di archiviazione locale per tutte le configurazioni di servizio.

### <a name="to-manage-local-storage-for-each-role-instance"></a>Per gestire l'archiviazione locale per ogni istanza del ruolo

1. Scegliere la scheda **Archiviazione locale** .

1. Nell'elenco **Di configurazione del servizio** , selezionare **Tutte le configurazioni**.

1. Per aggiungere una voce di spazio di archiviazione locale, scegliere il pulsante **Aggiungi archivio locale** . Viene aggiunta una nuova voce all'elenco.

1. Nella casella di testo **nome** digitare il nome che si desidera utilizzare per questo archivio locale.

1. Nella casella di testo **dimensioni** digitare le dimensioni in MB necessari per questo archivio locale.

1. Per rimuovere i dati in questo archivio locale quando il computer virtuale per questo ruolo è inserimento Cestino, selezionare la casella di controllo **Pulisci sul ruolo Cestino** .

1. Per modificare una voce di spazio di archiviazione locale esistente, selezionare la riga che è necessario aggiornare. È possibile modificare i campi, come descritto nei passaggi precedenti.

1. Per eliminare una voce di spazio di archiviazione locale, scegliere la voce di spazio di archiviazione nell'elenco e quindi fare clic sul pulsante **Rimuovi spazio di archiviazione locale** .

1. Per salvare le modifiche ai file di configurazione del servizio, scegliere l'icona **Salva** sulla barra degli strumenti.

1. Per accedere all'archiviazione locale che sono stati aggiunti nel file di configurazione del servizio, è necessario ottenere il valore dell'impostazione di configurazione di risorse locale. Utilizzare le righe di codice seguenti per accedere a questo valore e creare un file denominato **MyStorageTest.txt** scrivere una riga di dati di test in tale file. È possibile aggiungere questo codice nel `Button_Click` metodo utilizzato nelle procedure precedenti:

1. È necessario assicurarsi che la descritta di seguito vengono aggiunte istruzioni Default.aspx.cs:

    ```
    using System.IO;
    using System.Text;
    ```

1. Aggiungere il codice seguente per il `Button1_Click` metodo. Viene creato il file dell'archivio locale e inserisce il file di dati di test.

    ```
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("LocalStorage1");

    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyStorageTest.txt" };
    String filePath = Path.Combine(paths);

    using (FileStream writeStream = File.Create(filePath))
    {
          Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
          writeStream.Write(textToWrite, 0, textToWrite.Length);
    }
    ```

1. (Facoltativo) Per visualizzare il file creato quando si esegue il servizio cloud in locale, utilizzare la procedura seguente:

  1. Eseguire il ruolo web e selezionare **Button1** per accertarsi che il codice all'interno `Button1_Click` chiamata.

  1. Nell'area di notifica, aprire il menu di scelta rapida per l'icona di Azure e scegliere **Mostra interfaccia utente di emulatore calcolare**. Verrà visualizzata la finestra di dialogo **Emulatore di calcolo di Azure** .

  1. Selezionare il ruolo di web.

  1. Sulla barra dei menu scegliere **Strumenti** **aprire archivio locale**. Viene visualizzata una finestra di Esplora risorse.

  1. Sulla barra dei menu, immettere **MyStorageTest.txt** nella casella di testo di **ricerca** e quindi scegliere **INVIO** per avviare la ricerca.

    Il file viene visualizzato nei risultati della ricerca.

  1. Per visualizzare il contenuto del file, aprire il menu di scelta rapida per il file e scegliere **Apri**.

## <a name="collect-cloud-service-diagnostics"></a>Raccogliere diagnostica servizio cloud

È possibile raccogliere dati di diagnostica per il servizio cloud Azure. Questi dati viene aggiunto a un account di archiviazione. È consigliabile stringhe di connessione diverse per le configurazioni di servizi diversa. Ad esempio, può essere utile un account di archiviazione locale per una configurazione del servizio locale contenente un valore di UseDevelopmentStorage = true. È consigliabile inoltre configurare un servizio cloud che utilizza un account di archiviazione in Azure. Per ulteriori informazioni sulla diagnostica Windows Azure, vedere raccogliere dati registrazione dalla tramite diagnostica Azure.

>[AZURE.NOTE] La configurazione del servizio locale è già configurata per l'utilizzo di risorse locali. Se si utilizza la configurazione del servizio cloud per pubblicare il servizio cloud Azure, la stringa di connessione che specifica quando si pubblicano viene usata anche per la stringa di connessione di diagnostica a meno che non è stata specificata una stringa di connessione. Se si inserisce il servizio cloud utilizzando Visual Studio, la stringa di connessione nella configurazione del servizio non viene modificata.

### <a name="to-collect-cloud-service-diagnostics"></a>Per raccogliere diagnostica servizio cloud

1. Scegliere la scheda **configurazione** .

1. Nell'elenco **Di configurazione del servizio** , scegliere la configurazione del servizio che si desidera aggiornare o scegliere **Tutte le configurazioni**.

    >[AZURE.NOTE] È possibile aggiornare l'account di archiviazione per una configurazione del servizio specifico, ma se si desidera attivare o disattivare diagnostica è necessario scegliere tutte le configurazioni.

1. Per abilitare la diagnostica, selezionare la casella di controllo **Abilita diagnostica** .

1. Per modificare il valore per l'account di archiviazione, scegliere i puntini di sospensione (...).

    Viene visualizzata la finestra di dialogo **Crea stringa di connessione di spazio di archiviazione** .

1. Per utilizzare una stringa di connessione locale, scegliere l'opzione emulatore archiviazione Azure e quindi scegliere il pulsante **OK** .

1. Per usare un account di archiviazione associato all'abbonamento Azure, scegliere l'opzione di **abbonamento** .

1. Per usare un account di archiviazione per la stringa di connessione locale, scegliere l'opzione **immesso manualmente le credenziali** .

    Per ulteriori informazioni su come creare un account di archiviazione e su come immettere i dettagli dell'account di archiviazione nella finestra di dialogo **Crea stringa di connessione di spazio di archiviazione** , vedere [preparare per pubblicare o distribuire un'applicazione Azure da Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Scegliere l'account di archiviazione da usare in **nome Account**.

    Se ci si trova manualmente immettere le credenziali dell'account di archiviazione, copiare o digitare la chiave primaria nella **chiave Account**. Questo tasto può essere copiato dal [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Per copiare la chiave, la procedura seguente dalla visualizzazione **Gli account di archiviazione** nel [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885):
    
  1. Selezionare l'account di archiviazione che si desidera utilizzare per il servizio cloud.

  1. Fare clic sul pulsante **Gestisci i tasti di scelta** nella parte inferiore dello schermo. Viene visualizzata la finestra di dialogo **Gestisci i tasti di scelta** .

  1. Per copiare il tasto di scelta, scegliere il pulsante **Copia negli Appunti** . Il campo **chiave Account** a questo punto è possibile incollare questo tasto.

1. Per utilizzare l'account di archiviazione fornite, è consigliabile come stringa di connessione per diagnostica (e la memorizzazione nella cache) quando si pubblica il servizio cloud in Azure, selezionare la casella di controllo **Aggiorna sviluppo archiviazione stringhe di connessione per diagnostica e memorizzazione nella cache con lo spazio di archiviazione Azure account credenziali durante la pubblicazione in Azure** .

1. Scegliere il pulsante **Salva** sulla barra degli strumenti per salvare le modifiche al file di configurazione del servizio.

## <a name="change-the-size-of-the-virtual-machine-used-for-each-role"></a>Modificare le dimensioni della macchina virtuale utilizzata per ogni ruolo

È possibile impostare le dimensioni di macchina virtuale per ogni ruolo. È possibile impostare solo queste dimensioni per tutte le configurazioni di servizio. Se si seleziona una versione ridotta computer, quindi meno core CPU, memoria e disco locale lo spazio di archiviazione è assegnato. La larghezza di banda allocato è inferiore. Per ulteriori informazioni su queste dimensioni e le risorse assegnate, vedere [formati per i servizi Cloud](cloud-services/cloud-services-sizes-specs.md).

Le risorse necessarie per ogni macchina virtuale in Azure influisce sul costo di esecuzione del servizio cloud in Azure. Per ulteriori informazioni sulla fatturazione Azure, vedere [informazioni sulla fattura per Microsoft Azure](billing/billing-understand-your-bill.md).

### <a name="to-change-the-size-of-the-virtual-machine"></a>Per modificare le dimensioni della macchina virtuale

1. Scegliere la scheda **configurazione** .

1. Nell'elenco **Di configurazione del servizio** , selezionare **Tutte le configurazioni**.

1. Per selezionare le dimensioni per la macchina virtuale per questo ruolo, scegliere le dimensioni appropriate dall'elenco di **dimensione memoria virtuale** .

1. Scegliere il pulsante **Salva** sulla barra degli strumenti per salvare le modifiche al file di configurazione del servizio.

## <a name="manage-endpoints-and-certificates-for-your-roles"></a>Gestire i punti finali e certificati per i ruoli

Configurare i punti finali reti specificando il protocollo, il numero di porta e, per HTTPS, le informazioni del certificato SSL. Versioni precedenti giugno 2012 supportano HTTP, HTTPS e TCP. La versione giugno 2012 supporta i protocolli e UDP. Non è possibile utilizzare UDP per gli endpoint inpui nell'emulatore di calcolo. È possibile utilizzare tale protocollo solo per gli endpoint interni.

Per migliorare la protezione del servizio cloud Azure, è possibile creare endpoint che utilizzano il protocollo HTTPS. Ad esempio, se si dispone di un servizio cloud che viene utilizzato dai clienti per gli ordini di acquisto, si desidera assicurarsi che le informazioni vengono protette utilizzando SSL.

È anche possibile aggiungere endpoint che possono essere usate internamente o esternamente. Endpoint esterni sono denominati endpoint di input. Un endpoint input consente a un altro punto di accesso per gli utenti del servizio cloud. Se si dispone di un servizio WCF, è consigliabile per esporre un endpoint interno per un ruolo web da utilizzare per accedere a questo servizio.

>[AZURE.IMPORTANT] È possibile aggiornare solo i punti finali per tutte le configurazioni di servizio.

Se si aggiungono i punti finali HTTPS, è necessario usare un certificato SSL. A questo scopo è possibile associare i certificati con il proprio ruolo per tutte le configurazioni di servizi e utilizzarli per gli endpoint.

>[AZURE.IMPORTANT] I certificati non sono stato inseriti con il servizio. È necessario caricare separatamente i certificati di Azure tramite il [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

Gli eventuali certificati gestione associazione con le configurazioni dei servizi è applicabile solo se il servizio cloud viene eseguito in Azure. Quando viene eseguito il servizio cloud nell'ambiente di sviluppo locale, viene utilizzato un certificato standard viene gestito da emulatore calcolo Azure.

### <a name="to-add-a-certificate-to-a-role"></a>Per aggiungere un certificato per un ruolo

1. Scegliere la scheda **certificati** .

1. Nell'elenco **Di configurazione del servizio** , selezionare **Tutte le configurazioni**.

    >[AZURE.NOTE] Per aggiungere o rimuovere i certificati, è necessario selezionare tutte le configurazioni. Se necessario, è possibile aggiornare il nome e l'identificazione personale per una configurazione del servizio specifico.

1. Per aggiungere un certificato per questo ruolo, scegliere il pulsante **Aggiungi certificato** . Viene aggiunta una nuova voce all'elenco.

1. Nella casella di testo **nome** immettere il nome per il certificato.

1. Nell'elenco **Percorso di archivio** di scegliere la posizione per il certificato che si desidera aggiungere.

1. Nell'elenco **Nome archiviare** , scegliere l'archivio che si desidera utilizzare per selezionare il certificato.

1. Per aggiungere il certificato, scegliere i puntini di sospensione (...). Verrà visualizzata la finestra di dialogo **Sicurezza di Windows** .

1. Scegliere il certificato che si desidera utilizzare nell'elenco e quindi scegliere il pulsante **OK** .

    >[AZURE.NOTE] Quando si aggiunge un certificato dall'archivio certificati, gli eventuali certificati intermedi vengono automaticamente aggiunti alle impostazioni di configurazione dell'utente. Questi certificati intermedi devono essere inviati anche a Azure per configurare correttamente il servizio per SSL.

1. Per eliminare un certificato, scegliere il certificato e quindi fare clic sul pulsante **Rimuovi certificato** .

1. Scegliere l'icona **Salva** nella barra degli strumenti per salvare le modifiche ai file di configurazione del servizio.

### <a name="to-manage-endpoints-for-a-role"></a>Per gestire i punti finali per un ruolo

1. Scegliere la scheda **i punti finali** .

1. Nell'elenco **Di configurazione del servizio** , selezionare **Tutte le configurazioni**.

1. Per aggiungere un endpoint, scegliere il pulsante **Aggiungi Endpoint** . Viene aggiunta una nuova voce all'elenco.

1. Nella casella di testo **nome** digitare il nome che si desidera utilizzare per l'endpoint.

1. Scegliere il tipo di endpoint che è necessario dall'elenco **tipo** .

1. Scegliere il protocollo per l'endpoint che è necessario dall'elenco **protocollo** .

1. Se si tratta di un estremo input nella casella di testo **Porta pubblica** , immettere la porta pubblica da utilizzare.

1. Nella casella di testo **Privato porta** digitare la porta privata da utilizzare.

1. Se l'endpoint richiede il protocollo https, scegliere un certificato da utilizzare nell'elenco **Nome del certificato SSL** .

    >[AZURE.NOTE] Questo elenco Mostra i certificati che sono stati aggiunti per questo ruolo nella scheda **certificati** .

1. Scegliere il pulsante **Salva** sulla barra degli strumenti per salvare le modifiche ai file di configurazione del servizio.

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni su Azure progetti in Visual Studio, leggere [la configurazione di un progetto di Azure](vs-azure-tools-configuring-an-azure-project.md). Ulteriori informazioni sullo schema di servizio cloud, leggere [Riferimento allo Schema](https://msdn.microsoft.com/library/azure/dd179398).
