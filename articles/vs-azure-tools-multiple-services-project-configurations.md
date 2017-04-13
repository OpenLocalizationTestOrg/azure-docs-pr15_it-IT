<properties
   pageTitle="Configurare il progetto Azure con più configurazioni di servizio | Microsoft Azure"
   description="Informazioni su come configurare un progetto di servizio cloud Azure modificando i file ServiceDefinition.csdef e ServiceConfiguration. cscfg."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="configuring-your-azure-project-using-multiple-service-configurations"></a>Configurare il progetto Azure con servizi multipli

Un progetto di servizio cloud Azure include due file di configurazione: ServiceDefinition.csdef e ServiceConfiguration. cscfg. Questi file di pacchetto con l'applicazione di servizio cloud Azure di installazione in Azure.

- Il file **ServiceDefinition.csdef** contiene i metadati necessarie per l'ambiente di Azure ai requisiti dell'applicazione di servizio cloud, inclusi i ruoli che contiene. Il file contiene anche le impostazioni di configurazione da applicare a tutte le istanze. Queste impostazioni di configurazione possono essere letti in fase di esecuzione tramite l'API di Azure del servizio di Hosting Runtime. Il file non può essere aggiornato mentre il servizio è in esecuzione in Azure.

- Il file **ServiceConfiguration. cscfg** set di valori per le impostazioni di configurazione definite nel file di definizione di servizio e specifica il numero di istanze da eseguire per ogni ruolo. Durante l'esecuzione in Azure il servizio cloud, è possibile aggiornare il file.

Gli strumenti di Azure per Microsoft Visual Studio forniscono pagine delle proprietà che è possibile utilizzare per configurare le impostazioni di configurazione archiviate in questi file. Per accedere alle pagine di proprietà, fare doppio clic sul riferimento ruolo di sotto del progetto di servizio cloud Azure in Esplora soluzioni o fare riferimento al ruolo e scegliere **proprietà**, come illustrato nella figura seguente.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

Per informazioni sugli schemi sottostante per la definizione di servizio e i file di configurazione del servizio, vedere il [Riferimento allo Schema](https://msdn.microsoft.com/library/azure/dd179398.aspx). Per ulteriori informazioni sulla configurazione del servizio, vedere [come configurare i servizi Cloud](./cloud-services/cloud-services-how-to-configure.md).

## <a name="configuring-role-properties"></a>Configurazione delle proprietà di ruolo

Le pagine delle proprietà per un ruolo web e un ruolo di lavoro sono simili, anche se vi sono alcune differenze, sottolineate nelle sezioni seguenti.

Nella pagina di **memorizzazione nella cache** , è possibile configurare Azure memorizzazione nella cache servizi.

### <a name="configuration-page"></a>Pagina di configurazione

Nella pagina **configurazione** , è possibile impostare queste proprietà:

**Istanze**

Impostare la proprietà di conteggio **istanza** al numero di istanze che l'esecuzione del servizio per questo ruolo.

Impostare la proprietà **dimensione memoria virtuale** **Molto ridotta**, **Small**, **Medium**, **grande**o **Molto grande**.  Per ulteriori informazioni, vedere [formati per i servizi Cloud](./cloud-services/cloud-services-sizes-specs.md).

**Azione di avvio** (Solo ruolo web)

Impostare questa proprietà per specificare che Visual Studio deve essere avviato un web browser per gli endpoint HTTP o i punti finali HTTPS o entrambi quando si avvia il debug.

L'opzione endpoint HTTPS è disponibile solo se già stato definito un endpoint HTTPS per il proprio ruolo. È possibile definire un endpoint HTTPS nella pagina delle proprietà **i punti finali** .

Se è già stato aggiunto un endpoint HTTPS, l'opzione endpoint HTTPS è attivata per impostazione predefinita e Visual Studio verrà avviato un browser per l'endpoint quando si avvia il debug, oltre a un browser per l'endpoint HTTP. Si presuppone che entrambe le opzioni di avvio sono attivate.

**Diagnostica**

Per impostazione predefinita, diagnostica è abilitata per il ruolo Web. L'account di project e lo spazio di archiviazione di servizio cloud Azure sono impostate per utilizzare l'emulatore archivio locale. Quando si è pronti distribuire Azure, è possibile selezionare il pulsante Generatore (**.**) per aggiornare l'account di archiviazione per l'utilizzo di archiviazione Azure nel cloud. È possibile trasferire i dati di diagnostica per l'account di archiviazione su richiesta o intervalli programmata automaticamente. Per ulteriori informazioni sulla diagnostica Windows Azure, vedere [Attivazione diagnostica in macchine virtuali e servizi Cloud Windows Azure](./cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Pagina Impostazioni

Nella pagina **Impostazioni** è possibile aggiungere le impostazioni di configurazione per il servizio. Impostazioni di configurazione sono coppie nome-valore. Codice in esecuzione il ruolo è possibile leggere i valori delle impostazioni di configurazione in fase di esecuzione utilizzando classi fornite dalla [Libreria gestita Azure](http://go.microsoft.com/fwlink?LinkID=171026). In particolare, il metodo [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) restituirà il valore di un'impostazione di configurazione denominata in fase di esecuzione.

### <a name="configuring-a-connection-string-to-a-storage-account"></a>Configurazione di una stringa di connessione a un account di archiviazione

Una stringa di connessione è un'impostazione di configurazione che fornisce informazioni di autenticazione e connessione per l'emulatore lo spazio di archiviazione o per un account di archiviazione Azure. Ogni volta che il codice deve accedere archiviazione Azure servizi dati: vale a dire blob, coda o dati di una tabella: dal codice in esecuzione in un ruolo, è necessario definire una stringa di connessione per l'account di archiviazione.

Una stringa di connessione che fa riferimento a un account di archiviazione Azure deve utilizzare un formato definito. Per informazioni su come creare stringhe di connessione, vedere [Configurare le stringhe di connessione di Azure lo spazio di archiviazione](./storage/storage-configure-connection-string.md).

Quando si è pronti testare il servizio servizi di archiviazione Azure o quando si è pronti distribuire il servizio cloud di Azure, è possibile modificare il valore di tutte le stringhe di connessione in modo che puntino all'account di archiviazione Azure. Selezionare **le credenziali dell'account di archiviazione di invio**selezionare (**.**). Immettere le informazioni dell'account che include il nome account e la chiave account. Nella finestra di dialogo **Stringa di connessione di Account di archiviazione** , è possibile indicare se si desidera utilizzare i punti finali HTTPS predefinita (opzione predefinita), endpoint HTTP predefinito o personalizzato endpoint. È possibile decidere di utilizzare endpoint personalizzato se è stato registrato un nome di dominio personalizzato per il servizio, come descritto in [configurare un nome di dominio personalizzato per i dati blob in un account di archiviazione Azure](./storage/storage-custom-domain-name.md).

>[AZURE.IMPORTANT] È necessario modificare le stringhe di connessione in modo che puntino a un account di archiviazione Azure prima di distribuire il servizio. Non funziona eseguire questa operazione può causare il proprio ruolo non per avviare o per spostarsi tra gli stati inizializzazione, occupati e di arresto.

## <a name="endpoints-page"></a>Pagina endpoint

Un ruolo di lavoro possa essere presenti più endpoint HTTP, HTTPS o TCP. Endpoint possono essere endpoint di input, sono disponibili per i clienti esterni, o i punti finali interni sono disponibili per altri ruoli in esecuzione nel servizio.

- Per rendere disponibile un endpoint HTTP per Web browser e client esterni, modificare il tipo di endpoint in ingresso e specificare un nome e numero di porta pubblico.

- Per rendere disponibile un endpoint HTTPS per Web browser e client esterni, modificare il tipo di endpoint di **input**e specificare un nome di un numero di porta pubblico e un nome di certificato di gestione.

    Si noti che, prima che sia possibile specificare un certificato di gestione, è necessario definire il certificato nella pagina delle proprietà **dei certificati** .

- Per rendere disponibile per l'accesso interno un endpoint da altri ruoli nel servizio cloud, modificare il tipo di endpoint all'interno e specificare un nome e possibili porte private per l'endpoint.

## <a name="local-storage-page"></a>Pagina archivio locale

È possibile utilizzare la pagina delle proprietà **Archivio locale** per prenotare una o più risorse di archivio locale per un ruolo. Una risorsa di archiviazione locale è una directory riservata nel file system della macchina virtuale Azure in cui è in esecuzione un'istanza di un ruolo.

## <a name="certificates-page"></a>Pagina certificati

Nella pagina **certificati** , è possibile associare i certificati con il proprio ruolo. I certificati che aggiungono utilizzabili per configurare gli endpoint HTTPS nella pagina delle proprietà **i punti finali** .

La pagina delle proprietà **dei certificati** aggiunge informazioni relative ai certificati alla configurazione del servizio. Si noti che i certificati non sono stato inseriti con il servizio. è necessario caricare separatamente i certificati di Azure tramite il [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

Per associare un certificato con il proprio ruolo, specificare un nome per il certificato. Utilizzare questo nome per fare riferimento al certificato quando si configura un endpoint HTTPS nella pagina delle proprietà **i punti finali** . Successivamente, specificare se l'archivio certificati **Computer locale** o **Utente corrente** e il nome dell'archivio. Infine, immettere identificazione personale del certificato. Se il certificato nell'utente corrente\Personale archivio (personale), è possibile immettere identificazione personale del certificato, selezionare il certificato da un elenco popolato. Se si trova in un altro percorso, immettere il valore di identificazione personale manualmente.

Quando si aggiunge un certificato dall'archivio certificati, gli eventuali certificati intermedi vengono aggiunti automaticamente alle impostazioni di configurazione dell'utente. Questi certificati intermedi devono essere inviati anche a Azure per configurare correttamente il servizio per SSL.

Gli eventuali certificati gestione associare il servizio di applicano al servizio solo quando è in esecuzione nel cloud. Quando il servizio è in esecuzione nell'ambiente di sviluppo locale, utilizza un certificato standard viene gestito da emulatore di calcolo.

## <a name="configuring-the-azure-cloud-service-project"></a>Configurazione del progetto di servizio cloud Azure

Per configurare le impostazioni relative a un progetto di servizio cloud di Azure intera, è innanzitutto aprire il menu di scelta rapida per il nodo del progetto e scegliere Proprietà per aprire finestre delle proprietà. Nella tabella seguente mostra le pagine delle proprietà.

|Pagina delle proprietà|Descrizione|
|---|---|
|Applicazione|Da questa pagina, è possibile visualizzare informazioni sulla versione di strumenti di Azure che utilizza il progetto di servizio cloud, ed è possibile eseguire l'aggiornamento alla versione corrente degli strumenti.|
|Eventi di generazione|Da questa pagina, è possibile impostare gli eventi precedenti e successive alla compilazione.|
|Sviluppo|Da questa pagina, è possibile specificare le istruzioni di configurazione di compilazione e le condizioni in cui vengono eseguiti gli eventi di post-compilazione.|
|Web|Da questa pagina, è possibile configurare le impostazioni relative al server web.|
