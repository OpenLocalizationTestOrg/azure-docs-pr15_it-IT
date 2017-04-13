<properties
    pageTitle="Utilizzo di importazione/esportazione per trasferire i dati a archiviazione Blob | Microsoft Azure"
    description="Informazioni su come creare importare ed esportare processi nel portale di Azure classico per trasferire i dati per lo spazio di archiviazione blob."
    authors="renashahmsft"
    manager="aungoo"
    editor="tysonn"
    services="storage"
    documentationCenter=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="renash"/>


# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-blob-storage"></a>Utilizzare il servizio di importazione/esportazione di Microsoft Azure per trasferire i dati a archiviazione Blob

## <a name="overview"></a>Panoramica

Servizio di importazione/esportazione Azure consente di sicuro trasferire grandi quantità di dati a archiviazione blob Azure base spedizione unità disco rigido per un centro di dati di Azure. È anche possibile usare questo servizio per trasferire dati da archiviazione blob Azure a unità disco rigido e spedire al sito locale. Questo servizio è adatto nelle situazioni in cui si desidera trasferire questo servizio diversi di dati da Azure, ma il caricamento o il download in rete non è possibile a causa di larghezza di banda limitata o i costi di rete elevato.

Il servizio è necessario che l'unità disco rigido deve essere Raccoglitore bit crittografati per la protezione dei dati. Il servizio supporta gli account di archiviazione classica presenti in tutte le aree di Azure pubblico. È necessario fornire le unità disco rigido a uno dei percorsi supportati specificati più avanti in questo articolo.

In questo articolo verranno fornite informazioni del servizio di importazione/esportazione Azure e su come spedire unità per copiare i dati da e verso archiviazione Blob Azure.

> [AZURE.IMPORTANT] È possibile creare e gestire importare ed esportare processi per l'archiviazione classica tramite il portale classica o il [servizio di importazione/esportazione API REST](http://go.microsoft.com/fwlink/?LinkID=329099). Gli account di archiviazione di Manager delle risorse non sono supportati in questa fase.

## <a name="when-should-i-use-the-azure-importexport-service"></a>Quando è utile usare il servizio di importazione/esportazione Azure?

È possibile utilizzare il servizio di importazione/esportazione di Azure durante il caricamento o il download dei dati in rete è troppo lenta o ottenere la larghezza di banda di rete aggiuntive è costo estremamente complessa.

È possibile utilizzare questo servizio in scenari, ad esempio:

- La migrazione dei dati nel cloud: spostare rapidamente grandi quantità di dati di Azure e dei costi in modo efficace.
- Distribuzione di contenuto: inviare rapidamente i dati per i siti dei clienti.
- Backup: Eseguire backup dei dati in locale per archiviare in archiviazione blob Azure.
- Ripristino dei dati: recuperare grandi quantità di dati archiviati in archiviazione blob e averla recapitato alla posizione locale.

## <a name="pre-requisites"></a>Prerequisiti in corso

In questa sezione sono riportati i prerequisiti necessari per utilizzare il servizio. Esaminarli attentamente prima di consegnare le unità.

### <a name="storage-account"></a>Account di archiviazione

È necessario disporre di una sottoscrizione di Azure esistente e uno o più account di archiviazione **classico** per utilizzare il servizio di importazione/esportazione. Ogni processo può essere usata per trasferire i dati in o da un solo account di archiviazione classica. In altre parole, un processo di importazione/esportazione non interessano più account di archiviazione. Per informazioni sulla creazione di un nuovo account di archiviazione, vedere [come creare un Account di archiviazione](storage-create-storage-account.md#create-a-storage-account).

### <a name="blob-types"></a>Tipi di BLOB

È possibile utilizzare il servizio di Azure Importa/Esporta per copiare i dati in **blocco** BLOB o BLOB di **pagine** . Al contrario, è possibile esportare solo **blocco** BLOB, BLOB di **pagine** o BLOB **Accodamento** dallo spazio di archiviazione Azure tramite questo servizio.

### <a name="job"></a>Processo

Per iniziare il processo di importazione o esportazione dallo spazio di archiviazione Blob, creare innanzitutto un processo. Un processo di importazione o un processo di esportazione, può essere un processo:

- Creare un processo di importazione quando si desidera trasferire i dati sono locale a BLOB nell'account di archiviazione Azure.
- Creare un processo di esportazione quando si desidera trasferire i dati memorizzati come BLOB nell'account di archiviazione per unità disco rigido forniti all'utente.

Quando si crea un processo, è avvisare il servizio di importazione/esportazione che si verranno spedizione uno o più dischi rigidi a un centro di dati di Azure.

- Per un processo di importazione, verrà spedizione unità disco rigido che contiene i dati.
- Per un processo di esportazione verrà spedizione unità disco rigido vuota.
- È possibile spedire fino a 10 unità disco rigido per ogni processo.

È possibile creare un'importazione o esportazione processo tramite il [portale classica](https://manage.windowsazure.com/) o l' [API REST di Azure archiviazione Importa/Esporta](http://go.microsoft.com/fwlink/?LinkID=329099).

### <a name="client-tool"></a>Strumento client

La creazione di un'operazione di **importazione** il primo passaggio consiste nel preparare l'unità in cui verrà inviata per l'importazione. Per preparare l'unità, è necessario connettersi a un server locale ed eseguire lo strumento di Client Importa/Esporta Azure sul server locale. Questo strumento client semplifica l'esecuzione di copiare i dati all'unità, la crittografia dei dati sull'unità con BitLocker e generare i file del diario di unità.

I file del diario memorizzano le informazioni di base sul proprio lavoro e unità, ad esempio il numero di serie di unità e nome dell'account di archiviazione. Il file del diario non è archiviato il disco. Viene utilizzato durante la creazione del processo di importazione. Step by step i dettagli della creazione del processo sono vedere più avanti in questo articolo.

Lo strumento client è compatibile solo con sistema operativo Windows a 64 bit. Vedere la sezione [del sistema operativo](#operating-system) per specifiche versioni del sistema operativo è supportato.

Scaricare la versione più recente dello [strumento client Azure Importa/Esporta](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409). Per ulteriori informazioni sull'uso dello strumento di importazione/esportazione Azure, vedere il [Riferimento di Azure importazione/esportazione strumento](http://go.microsoft.com/fwlink/?LinkId=329032).

### <a name="hard-disk-drives"></a>Unità disco rigido

3,5 solo dischi rigidi interni SATA II/III sono supportati per l'utilizzo con il servizio di importazione/esportazione. È possibile utilizzare dischi rigidi fino a 10TB.
Per i processi di importazione, verrà elaborato solo il primo volume di dati sull'unità. Il volume di dati deve essere formattato con NTFS.
Quando si copiano dati sul disco rigido, è possibile allegarlo direttamente tramite un connettore SATA oppure è possibile allegarlo esternamente usando un adattatore SATA II/III USB esterna. È consigliabile utilizzare uno delle schede SATA II/III USB esterna seguenti:

- Anker 68UPSATAA - 02 IP
- Anker 68UPSHHDS-IP
- Startech SATADOCK22UE
- Sharkoon QuickPort XT HC

Se si dispone di un convertitore di cui non è elencato sopra, è possibile provare a eseguire lo strumento di importazione/esportazione Azure con il convertitore per preparare l'unità e vedere se funziona prima dell'acquisto di un convertitore di supportati.

> [AZURE.IMPORTANT] Dischi rigidi esterni inclusi in un adattatore USB incorporato non sono supportate dal servizio. Inoltre, non è possibile utilizzare il disco all'interno di maiuscole e minuscole di un disco rigido esterno; non inviare dischi rigidi esterni.

### <a name="encryption"></a>Crittografia

I dati sull'unità devono essere crittografati con crittografia unità BitLocker. Consente di proteggere i dati durante il transito.

Per i processi di importazione, esistono due modi per eseguire la crittografia. Il primo consiste nell'utilizzare la / crittografare parametro durante l'esecuzione dello strumento client durante la preparazione di unità. Il secondo modo consiste nel attivare BitLocker manualmente nell'unità e specificare la chiave di crittografia nella riga di comando strumento client durante la preparazione di unità.

Per i processi di esportazione, dopo aver copiati i dati per le unità, il servizio verrà crittografato unità con BitLocker prima di distribuirlo al mittente. La chiave di crittografia sarà fornita all'utente tramite il portale classica.  

### <a name="operating-system"></a>Sistema operativo

Per preparare il disco rigido tramite lo strumento di importazione/esportazione Azure prima di spedire l'unità in Azure, è possibile utilizzare uno dei seguenti sistemi operativi a 64 bit:

Windows 7 Enterprise, Ultimate di Windows 7, Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Enterprise Windows 8.1, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Tutti questi sistemi operativi supportano unità BitLocker.

> [AZURE.IMPORTANT] <sup>1</sup> Se si usa un computer Windows 10 per preparare il disco rigido, scaricare la versione più recente dello strumento di importazione/esportazione Azure.

### <a name="locations"></a>Percorsi

Il servizio di importazione/esportazione Azure supporta la copia di dati con tutti gli account di archiviazione di Azure pubblico. È possibile spedire dischi rigidi a uno dei seguenti percorsi. Se l'account di archiviazione è in un percorso di Azure pubblico che non viene specificato, un percorso di spedizione alternativo sarà fornito durante la creazione del processo tramite il portale classica o l'API REST Importa/Esporta.

Supportati spedizione percorsi:

- Stati Uniti orientali

- Usa ovest

- Stati Uniti orientali 2

- Centrale USA

- America del Nord centrale USA

- Sud centrale USA

- Europa Nord America

- Europa ovest

- Asia orientale

- Area Asia sudorientale

- Australia orientale

- Sudest Australia

- Giappone ovest

- Giappone est

- India centrale


### <a name="shipping"></a>Spedizione

**Unità di spedizione al data center:**

Quando si crea un processo di importazione o esportazione, verranno forniti un indirizzo di spedizione di uno dei percorsi supportati da spedire le unità. L'indirizzo di spedizione specificato dipenderà la posizione del proprio account di archiviazione, ma potrebbe non essere lo stesso percorso di archiviazione dei account.

È possibile utilizzare i vettori come FedEx, DHL, Sportivo o il servizio di postale Contattaci per spedire le unità per l'indirizzo di spedizione.

**Unità di spedizione dal centro dati:**

Quando si crea un processo di importazione o esportazione, è necessario specificare un indirizzo del mittente per Microsoft da utilizzare quando si distribuisce le unità, tornare al termine del processo. Assicurarsi di specificare un indirizzo del mittente valido per evitare ritardi nella elaborazione.

È inoltre necessario fornire un vettore FedEx o DHL valido numero utilizzati da Microsoft per nuovamente le unità di spedizione conto. È necessario per unità di spedizione dal posizioni Europa e Usa un numero di account FedEx. È necessario per unità di spedizione dal posizioni Asia e Australia un numero di account DHL. Se non si dispone di uno, è possibile creare un [FedEx](http://www.fedex.com/us/oadr/) (per gli Stati Uniti ed Europe) o un conto vettore [DHL](http://www.dhl.com/) (Asia e Australia). Se si dispone già di un account di questo, verificare che sia valido.

I pacchetti di spedizione, è necessario seguire i termini in [Condizioni del servizio di Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [AZURE.IMPORTANT] Si noti che il supporto fisico spedizione potrebbe essere necessario incrociato paesi diversi. Sei responsabile per garantire che il supporto fisico e i dati vengono importati e/o esportati conformemente alle leggi applicabili. Prima della distribuzione supporti fisici, contattare i consulenti per verificare che i file multimediali e dati legge possono essere spediti al centro dati identificato. In questo modo per garantire che raggiunge Microsoft in modo tempestivo. Ad esempio, qualsiasi pacchetto che si intersecazione bordi internazionali deve una fattura commerciale a accompagnato dal pacchetto (ad eccezione di se che attraversano i bordi all'interno dell'Unione europea). Impossibile stampare una copia con riempimento della fattura commerciale dal sito Web carrier. Esempio di commerciale fattura sono [fattura commerciale DHL] (http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) o [FedEx fattura commerciale](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Verificare che Microsoft non è stato contrassegnato come esportatore.

## <a name="how-does-the-azure-importexport-service-work"></a>Come funziona il servizio di importazione/esportazione di Azure

È possibile trasferire dati tra il sito locale e archiviazione blob Azure mediante il servizio di importazione/esportazione Azure creando processi e spedizione unità disco rigido per un centro di dati di Azure. Ogni unità disco rigido che si spediscono è associata a un singolo processo. Ogni processo è associata a un account di archiviazione singola. Esaminare la [sezione Prerequisiti](#pre-requisites) con attenzione per conoscere le specifiche di questo servizio come supportato blob tipi, tipi di disco, posizioni e spedizione.

In questa sezione verranno illustrate brevemente i passaggi necessari per importare ed esportare i processi. In un secondo momento nella [sezione Guida introduttiva](#quick-start)fornite istruzioni dettagliate per creare un'importazione e processo di esportazione.

### <a name="inside-an-import-job"></a>All'interno di un processo di importazione

Un alto livello, un processo di importazione prevede i passaggi seguenti:

- Determinare i dati da importare e il numero di unità che sarà necessario.
- Identificare i BLOB di destinazione per i dati in archiviazione Blob.
- Usare lo strumento di importazione/esportazione Azure per copiare i dati in una o più unità disco rigido e li crittografa con BitLocker.  
- Creare un processo di importazione nell'account di archiviazione classica destinazione tramite il portale classica o l'API REST Importa/Esporta. Se tramite il portale classica, caricare i file del diario di unità.
- Specificare l'indirizzo del mittente e il numero di account vettore da utilizzare per le unità di spedizione al mittente.
- Spedire le unità disco rigido per l'indirizzo di spedizione disponibili durante la creazione del processo.
- Aggiornare il recapito il numero di dettagli del processo di importazione di rilevamento e inviare il processo di importazione.
- Unità sono ricevute ed elaborazione di Azure data center.
- Unità vengono spedite con il proprio account vettore per l'indirizzo del mittente specificato nel processo di importazione.

    ![Figura del flusso di lavoro 1:Import](./media/storage-import-export-service/importjob.png)


### <a name="inside-an-export-job"></a>All'interno di un processo di esportazione

Un alto livello, un processo di esportazione prevede i passaggi seguenti:

- Determinare i dati da esportare e il numero di unità che sarà necessario.
- Identificare i percorsi di contenitore dei dati in archiviazione Blob o BLOB di origine.
- Creare un processo di esportazione nell'account di archiviazione di origine tramite il portale classica o l'API REST Importa/Esporta.
- Specificare i percorsi di contenitore dei dati o BLOB di origine del processo di esportazione.
- Specificare l'indirizzo del mittente e il numero di account vettore per da utilizzare per le unità di spedizione al mittente.
- Spedire le unità disco rigido per l'indirizzo di spedizione disponibili durante la creazione del processo.
- Aggiornare il recapito il numero di dettagli del processo di esportazione di rilevamento e inviare il processo di esportazione.
- Le unità sono ricevute ed elaborazione di Azure data center.
- Le unità vengono crittografate con BitLocker. i tasti sono disponibili tramite il portale classica.  
- Le unità vengono fornite con il proprio account vettore per l'indirizzo del mittente specificato nel processo di importazione.

    ![Figura del flusso di lavoro 2:Export](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-status"></a>Visualizzare lo stato di processo

È possibile tenere traccia dello stato dell'importazione o esportazione di processi dal portale di classica. Passare al proprio account di archiviazione nel portale di classica e fare clic sulla scheda **Importa/Esporta** . Nella pagina verrà visualizzato un elenco dei processi. È possibile filtrare l'elenco sul numero di verifica, il nome del processo, tipo di processo o lo stato dei processi.

Verrà visualizzato uno dei seguenti stati di processo a seconda della posizione nell'unità del processo.

| Stato dei processi   | Descrizione                                                                                                                                                                                                                                                                                                                                                                        |
|:-------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Creazione di     | È stato creato il processo, ma non si è ancora fornito le informazioni di spedizione.                                                                                                                                                                                                                                                                                                |
| Spedizione     | È stato creato il processo e ha fornito le informazioni di spedizione. **Nota**: quando l'unità viene recapitato all'interfaccia di dati di Azure, lo stato può comunque visualizzare "Spedizione" per un certo periodo. Una volta che viene avviato il servizio copiare i dati, lo stato assumerà "Trasferimento". Se si desidera visualizzare lo stato di più specifico dell'unità, è possibile utilizzare l'API REST Importa/Esporta. |
| Il trasferimento | I dati vengono trasferiti dal disco rigido (per un processo di importazione) o sul disco rigido (per un processo di esportazione).                                                                                                                                                                                                                                                                 |
| Confezioni per i prodotti    | Trasferimento dei dati è stato completato e nel disco rigido per la preparazione per la distribuzione al mittente.                                                                                                                                                                                                                                                                             |
| Completare     | Il disco rigido è stato trasferito al mittente.                                                                                                                                                                                                                                                                                                                                      |

### <a name="time-to-process-job"></a>Tempo di processo

La quantità di tempo necessario per processo un processo di importazione/esportazione varia in base a fattori diversi, ad esempio il tempo di spedizione processo tipo, tipo e dimensioni dei dati copiati e le dimensioni dei dischi fornito. Il servizio di importazione/esportazione non dispone di un contratto di servizio. È possibile utilizzare l'API REST per tenere traccia dello stato di processo più da vicino. È un parametro percentuale di completamento dell'operazione Elenca i processi che fornisce un'indicazione sullo stato di avanzamento di copia. Raggiungere Contattaci se occorre una stima per completare un processo di importazione/esportazione critico ora.

### <a name="pricing"></a>Prezzi

**Unità tariffa**

Esiste una Commissione di gestione di unità per ogni unità elaborato come parte dell'importazione o esportazione di processo. Visualizzare i dettagli sui [Prezzi di Azure Importa/Esporta](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Spese di spedizione**

Quando si spediscono unità in Azure, pagando le spese di spedizione il vettore. Quando Microsoft restituisce le unità per l'utente, le spese di spedizione viene addebitata all'account vettore è specificato al momento della creazione di processi.

**Costi delle transazioni**

Importare dati in archiviazione blob sono non disponibili costi delle transazioni. In base alle tariffe standard uscita sono applicabili quando esportate dallo spazio di archiviazione blob. Per ulteriori informazioni sui costi di transazione, vedere [dati trasferire prezzi.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="quick-start"></a>Guida introduttiva

In questa sezione vengono fornite istruzioni dettagliate per la creazione di un'importazione e un processo di esportazione. Verificare che siano soddisfatti tutti i [Prerequisiti](#pre-requisites) prima di iniziare.

## <a name="how-to-create-an-import-job"></a>Come è possibile creare un processo di importazione?

Creare un processo di importazione per copiare i dati al proprio account di archiviazione Azure dal disco rigido da spedizione una o più unità contenente i dati all'interfaccia di dati specificato. Il processo di importazione raccoglie informazioni dettagliate sull'unità disco rigido, dati copiati, assegnare account di archiviazione e informazioni di spedizione al servizio di Azure Importa/Esporta. La creazione di un processo di importazione è tre passaggi. Preparare le unità utilizzando lo strumento client Azure Importa/Esporta. In secondo luogo, inviare un processo di importazione tramite il portale classica. Terza spedire le unità per l'indirizzo di spedizione disponibili durante la creazione del processo e aggiornare le informazioni di spedizione nei dettagli processo.   

> [AZURE.IMPORTANT] È possibile inviare un solo processo per conto di spazio di archiviazione. Ogni unità che si spediscono possono essere importati a un account di archiviazione. Ad esempio, supponiamo che si desidera importare dati in due account di archiviazione. È necessario utilizzare unità disco rigido separate per ciascun account di archiviazione e creare processi distinti per ogni account di archiviazione.

### <a name="prepare-your-drives"></a>Preparare le unità

Il primo passaggio quando si importano dati tramite il servizio di importazione/esportazione Azure è per preparare le unità tramite lo strumento di client Azure Importa/Esporta. Seguire la procedura seguente per preparare le unità.

1.  Identificare i dati da importare. Può trattarsi di directory e autonomo file sul server locale o in una condivisione di rete.  

2.  Determinare il numero di unità che sarà necessario a seconda di dimensione totale dei dati. Acquisizione delle risorse il numero di unità disco rigido SATA II/III di 3,5 necessari.

3.  Identificare l'account di archiviazione di destinazione, contenitore, virtuali e BLOB.

4.  Determinare la directory e/o file autonomi che verranno copiati in ogni unità disco rigido.

5.  Usare lo [Strumento di importazione/esportazione Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) per copiare i dati in una o più unità disco rigido.

    - Lo strumento di Azure Importa/Esporta crea sessioni copia per copiare i dati dall'origine per le unità disco rigido. In una sessione di copia singola, lo strumento possibile copiare una singola directory con le relative sottodirectory o un singolo file.

    - Potrebbe essere necessario copia più sessioni se i dati di origine si estende molte directory.

    - Ogni unità disco rigido che è preparare richiederà sessione di almeno una copia.

6.  È possibile specificare il / crittografare parametro per attivare BitLocker sull'unità disco rigido. In alternativa, è possibile attivare BitLocker manualmente sull'unità disco rigido e fornire la chiave durante l'esecuzione dello strumento.

7.  Lo strumento di importazione/esportazione Azure genera un file di diario di unità per ogni unità come viene preparato. Il file di registro unità archiviato nel computer locale, non sull'unità stessa. Quando si crea il processo di importazione, si verrà caricare il file del diario. Un file di registro unità include l'ID di unità e la chiave BitLocker, nonché altre informazioni sull'unità.
**Importante**: ogni unità disco rigido è preparare verrà generato un file di diario. Durante la creazione del processo di importazione tramite il portale classica, è necessario caricare tutti i file del diario di unità che fanno parte del processo di importazione. Unità senza diario file non verrà eseguiti.

8.  Non modificare i dati in unità disco rigido o il file journal dopo aver completato la preparazione disco.

Di seguito sono i comandi ed esempi per preparare l'unità disco rigido strumento client Azure Importa/Esporta.

Azure importazione/esportazione strumento PrepImport comando client per la prima sessione di copia copiare una directory:

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Esempio:**

Nell'esempio seguente si siano copiando tutti i file e sub directory da H:\Video per l'unità disco rigido installato su x. I dati verranno importati all'account di archiviazione di destinazione specificata per la chiave account lo spazio di archiviazione e nel contenitore di spazio di archiviazione chiamata video. Se il contenitore di spazio di archiviazione non è presenta, verrà creato. Questo comando anche formattare e crittografare l'unità disco rigido di destinazione.

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:storageaccountkey /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

Azure importazione/esportazione strumento PrepImport comando client per le sessioni successive copia copiare una directory:

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

Per le sessioni di copia successive alla stessa unità disco rigido, specificare lo stesso nome file del diario e fornire un nuovo ID sessione; non è necessario specificare di nuovo, l'unità di chiave e destinazione account lo spazio di archiviazione né per formattare o crittografare le unità. In questo esempio stiamo copiando la cartella H:\Photo e le relative directory sub nella stessa unità di destinazione, nel contenitore di spazio di archiviazione denominato foto.

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

Lo strumento client Azure importazione/esportazione PrepImport comando per la prima sessione di copia copiare un file:

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

Lo strumento client Azure importazione/esportazione comando PrepImport per le sessioni successive copia copiare un file:

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Memorizza**: per impostazione predefinita, i dati da importare come BLOB di blocco. È possibile utilizzare il parametro /BlobType per importare i dati come un BLOB di pagine. Ad esempio, se si stanno importando file disco rigido virtuale che verranno installati come dischi in una macchina virtuale Azure, è necessario importarli come BLOB di pagine. Se non si è certi di quale blob digitare da usare, è utile per determinare il tipo giusto è possibile specificare /blobType:auto. In questo caso, tutti i file disco rigido virtuale e vhdx verranno importati come BLOB di pagine e il resto verrà importato come BLOB di blocco.

Visualizzare altri dettagli sull'uso dello strumento di client Azure Importa/Esporta in [Preparazione unità disco rigido per l'importazione](https://msdn.microsoft.com/library/dn529089.aspx).

Inoltre, fare riferimento al [Flusso di lavoro di esempio per preparare l'unità disco rigido per un processo di importazione](https://msdn.microsoft.com/library/dn529097.aspx) per ulteriori informazioni dettagliate.  

### <a name="create-the-import-job"></a>Creare il processo di importazione

1.  Dopo aver preparato nell'unità, passare al proprio account di archiviazione nel [portale classica](https://manage.windowsazure.com) e visualizzare il Dashboard. In **Riepilogo veloce**, fare clic su **Crea un processo di importazione**. Esaminare i passaggi e selezionare la casella di controllo per indicare che è stato preparato nell'unità e di avere il file di registro unità disponibile.

2.  Nel passaggio 1, fornire informazioni di contatto della persona responsabile per questo processo di importazione e l'indirizzo del mittente valido. Se si desidera salvare i dati di registro dettagliato per il processo di importazione, selezionare l'opzione per **salvare il registro dettagliato il contenitore di blob 'waimportexport'**.

3.  Nel passaggio 2, caricare i file del diario unità che è stato acquistato durante l'elaborazione di unità. È necessario caricare un file per ogni unità in cui è stato creato.

    ![Creare il processo di importazione - passaggio 3](./media/storage-import-export-service/import-job-03.png)

4.  Nel passaggio 3, immettere un nome descrittivo per il processo di importazione. Si noti che il nome che immesso può contenere solo lettere, numeri, trattini e caratteri di sottolineatura, deve iniziare con una lettera e non può contenere spazi. Utilizzare il nome che si desidera tenere traccia dei processi mentre sono in corso e una volta completati.

    Selezionare l'area centro dati dall'elenco. Area dati centro indica il centro dati e l'indirizzo al quale è necessario fornire il pacchetto. Vedere le domande frequenti sotto per ulteriori informazioni.

5.  Passaggio 4, selezionare il vettore mittente dall'elenco e immettere il numero di account vettore. Microsoft utilizzerà l'account da spedire le unità al mittente una volta completato il processo di importazione.

    Se si dispone del numero di registrazione, selezionare l'operatore di consegna nell'elenco e immettere il numero di elementi di verifica.

    Se non si non dispone di un numero di registrazione, scegliere **fornire le informazioni di spedizione per questo processo di importazione dopo che state spedizione il pacchetto**, quindi completare il processo di importazione.

6. Per immettere il numero di elementi di verifica dopo che sono stati spediti il pacchetto, torna nella pagina **Importa/Esporta** per l'account di archiviazione nel portale classica, selezionare il processo dall'elenco e scegliere **Informazioni di spedizione**. Spostarsi tra la procedura guidata e immettere il numero di elementi di verifica nel passaggio 2.

    Se il numero di elementi di verifica non è aggiornato all'interno di 2 settimane di la creazione del processo, scadrà il processo.

    Se il processo è la creazione, spedizione o trasferimento, è anche possibile aggiornare il numero di account vettore nel passaggio 2 della procedura guidata. Dopo aver aggiunto il processo lo stato di confezioni per i prodotti, non è possibile aggiornare il numero di account vettore per tale processo.

7. È possibile tenere traccia dello stato di avanzamento processo nel portale dashboard. Vedere significato di ogni stato processo nella sezione precedente, [visualizzare lo stato di processo](#viewing-your-job-status).

## <a name="how-to-create-an-export-job"></a>Come è possibile creare un processo di esportazione?

Creare un processo di esportazione per la notifica il servizio di importazione/esportazione che è necessario essere spedizione una o più unità vuota al data center in modo che dati possono essere esportati dal proprio account di archiviazione per le unità e le unità quindi inviati per.

### <a name="prepare-your-drives"></a>Preparare le unità

Di seguito viene pre-verificato consigliati per la preparazione le unità di un processo di esportazione:

1. Controllare il numero di dischi necessari comando PreviewExport dello strumento Azure Importa/Esporta. Per ulteriori informazioni, vedere [La visualizzazione in anteprima l'uso di unità per un processo di esportazione](https://msdn.microsoft.com/library/azure/dn722414.aspx). Consente di visualizzare in anteprima l'uso di unità per BLOB è selezionata, in base alla dimensione le unità in cui si desidera utilizzare.

2. Verificare che possono leggere e scrivere sul disco rigido fornito per il processo di esportazione.

### <a name="create-the-export-job"></a>Creare il processo di esportazione

1.  Per creare un processo di esportazione, passare al proprio account di archiviazione nel [portale classica](https://manage.windowsazure.com)e visualizzare il Dashboard. In **Riepilogo veloce**, fare clic su **Crea un processo di esportazione** e procedere con la procedura guidata.

2.  Nel passaggio 2, fornire informazioni di contatto della persona responsabile per il processo di esportazione. Se si desidera salvare i dati di registro dettagliato per il processo di esportazione, selezionare l'opzione per **salvare il registro dettagliato il contenitore di blob 'waimportexport'**.

3.  Nel passaggio 3, specificare i dati blob che si desidera esportare dal proprio account di archiviazione all'unità vuota o unità. È possibile scegliere di esportare tutti i dati blob di account di archiviazione oppure è possibile specificare che BLOB o agli insiemi di BLOB da esportare.

    Per specificare un blob da esportare, utilizzare il selettore **Uguale a** e specificare il percorso relativo a blob, che iniziano con il nome del contenitore. Utilizzare *$root* per specificare il contenitore radice.

    Per specificare tutti i BLOB a partire da un prefisso, utilizzare il selettore **Inizia con** e specificare il prefisso, a partire da una barra "/". Il prefisso potrebbe essere il prefisso il nome del contenitore, il nome del contenitore di completamento o il nome del contenitore completo seguito dal prefisso del nome del blob.

    Nella tabella seguente è illustrati esempi di percorsi blob validi:

    Strumento di selezione|Percorso di BLOB|Descrizione
    ---|---|---
    Inizia con|/|Esporta tutti i BLOB di account di archiviazione
    Inizia con|/$root /|Esporta tutti i BLOB nel contenitore principale
    Inizia con|/Book|Esporta tutti i BLOB in qualsiasi contenitore che inizia con prefisso **della Rubrica**
    Inizia con|/Music/|Esporta tutti i BLOB contenitore **musicale**
    Inizia con|musica/amore|Esporta tutti i BLOB contenitore **musica** che iniziano con prefisso **apprezzi**
    Uguale a|$root/logo.bmp|Esportazioni blob **logo. bmp** nel contenitore principale
    Uguale a|videos/Story.mp4|Esportazioni blob **story.mp4** nel contenitore **video**

    È necessario specificare i percorsi blob in formati validi per evitare errori durante l'elaborazione, come mostrato in questa schermata.

    ![Creare il processo di esportazione - passaggio 3](./media/storage-import-export-service/export-job-03.png)


4.  Passaggio 4, immettere un nome descrittivo per il processo di esportazione. Il nome che immesso può contenere solo lettere, numeri, trattini e caratteri di sottolineatura, deve iniziare con una lettera e non può contenere spazi.

    Area dati centro indica il centro di dati a cui è necessario fornire il pacchetto. Vedere le domande frequenti sotto per ulteriori informazioni.

5.  Passaggio 5, selezionare l'operatore mittente dall'elenco e immettere il numero di account vettore. Microsoft utilizzerà l'account da spedire le unità al mittente una volta completato il processo di esportazione.

    Se si dispone del numero di registrazione, selezionare l'operatore di consegna nell'elenco e immettere il numero di elementi di verifica.

    Se non si non dispone di un numero di registrazione, scegliere **fornire le informazioni di spedizione per il processo di esportazione dopo ho spedizione il pacchetto**, quindi completare il processo di esportazione.

6. Per immettere il numero di elementi di verifica dopo che sono stati spediti il pacchetto, torna nella pagina **Importa/Esporta** per l'account di archiviazione nel portale classica, selezionare il processo dall'elenco e scegliere **Informazioni di spedizione**. Spostarsi tra la procedura guidata e immettere il numero di elementi di verifica nel passaggio 2.

    Se il numero di elementi di verifica non è aggiornato all'interno di 2 settimane di la creazione del processo, scadrà il processo.

    Se il processo è la creazione, spedizione o trasferimento, è anche possibile aggiornare il numero di account vettore nel passaggio 2 della procedura guidata. Dopo aver aggiunto il processo lo stato di confezioni per i prodotti, non è possibile aggiornare il numero di account vettore per tale processo.

    > [AZURE.NOTE] Se il blob da esportare è in uso in fase di copia sul disco rigido, servizio di Azure Importa/Esporta un'istantanea del blob e copiare lo snapshot.

7.  È possibile tenere traccia dello stato di avanzamento processo nel dashboard nel portale di classica. Vedere significato di ogni stato processo nella sezione precedente in "visualizzare lo stato di processo".

8.  Dopo aver ricevuto le unità contenente i dati esportati, è possibile visualizzare e copiare le chiavi BitLocker generate dal servizio per l'unità. Passare al proprio account di archiviazione nel portale di classica e fare clic sulla scheda Importa/Esporta. Selezionare il processo di esportazione dall'elenco e fare clic sul pulsante Visualizza chiavi. I tasti di BitLocker vengono visualizzati come illustrato di seguito:

    ![Visualizzare i tasti di BitLocker per processo di esportazione](.\media\storage-import-export-service\export-job-bitlocker-keys.png)

Passare tra la sezione Domande frequenti su come vengono illustrate le domande più frequenti rilevati dai clienti quando si usa il servizio.

## <a name="frequently-asked-questions"></a>Domande frequenti ##


**Quanto tempo sarà per copiare i dati dopo le unità raggiunge il centro dati?**

Il tempo di copia varia a seconda del tipo diversi fattori quali il tipo di processo, tipo e la dimensione di dati da copiare, dimensioni dei dischi fornito e il carico di lavoro esistente. Può variare da un paio di giorni che vanno a due settimane, in base a questi fattori. È difficile fornire una stima generale. Il servizio tenta di ottimizzare il processo mediante la copia di più unità in parallelo laddove possibile. Se si dispone di un processo di importazione/esportazione critico ora, raggiungere Contattaci per una stima.

**Quando è utile usare il servizio di importazione/esportazione Azure?**
Uno necessario prendere in considerazione l'utilizzo di Azure Importa Esporta se durante il caricamento o il download in rete richiede circa stimare oltre 7 giorni. È possibile calcolare quanto tempo richiederà utilizzando una calcolatrice online o tramite [download](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/archive/master.zip) quello che si trova in un Azure Importa Esporta resto API campione in repository esempi Azure alla [Calcolatrice velocità trasferire dati](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html). Non si tratta di un calcolo preciso ma solo un'indicazione approssimativa.

**È possibile usare il servizio di importazione/esportazione Azure con un account di archiviazione di Manager delle risorse?**

No, è possibile copiare dati a oppure da un account di archiviazione di Manager delle risorse direttamente mediante il servizio di Azure Importa/Esporta. Il servizio supporta solo gli account di archiviazione classica. Sarà presto disponibile il supporto per account di archiviazione di Manager delle risorse. In alternativa, è possibile importare dati in un account di archiviazione classica ed eseguire la migrazione al proprio account di archiviazione di Manager delle risorse utilizzando [AzCopy](storage-use-azcopy.md) o CopyBlob.

**È possibile copiare i file di Azure usando il servizio di Azure importazione/esportazione?**

No, il servizio di importazione/esportazione Azure supporta solo blocco BLOB e BLOB di pagine. Tutti gli altri tipi di spazio di archiviazione, inclusi file di Azure, tabelle e code non sono supportati.

**È disponibile per gli abbonamenti CSP il servizio di importazione/esportazione Azure?**

No, il servizio di importazione/esportazione Azure non supporta le sottoscrizioni CSP. Il supporto verrà aggiunti in futuro.

**È possibile ignorare il passaggio di preparazione di unità per un processo di importazione o è possibile preparare un'unità senza copiare?**

Qualsiasi unità che si desidera rendere disponibile per l'importazione di dati deve essere preparato tramite lo strumento di client Azure Importa/Esporta. È necessario utilizzare lo strumento di client per copiare i dati all'unità.

**È necessario eseguire qualsiasi preparazione disco durante la creazione di un processo di esportazione?**

Sono consigliabile non, ma alcuni pre controlli. Controllare il numero di dischi necessari comando PreviewExport dello strumento Azure Importa/Esporta. Per ulteriori informazioni, vedere [La visualizzazione in anteprima l'uso di unità per un processo di esportazione](https://msdn.microsoft.com/library/azure/dn722414.aspx). Consente di visualizzare in anteprima l'uso di unità per BLOB è selezionata, in base alla dimensione le unità in cui si desidera utilizzare. Controllare inoltre che è possibile leggere e scrivere sul disco rigido fornito per il processo di esportazione.

**Cosa succede se si invia un accidentalmente un disco rigido conforme ai requisiti supportati?**

Interfaccia di dati di Azure restituirà l'unità in cui non è conforme ai requisiti supportati per l'utente. Se solo alcune delle unità nel pacchetto soddisfa i requisiti di supporto, verranno elaborate tali unità e verranno restituite le unità che non soddisfano i requisiti per l'utente.

**È possibile annullare il processo?**

È possibile annullare un processo durante il suo stato è la creazione o spedizione.

**Per quanto tempo è possibile visualizzare lo stato dei processi completati nel portale classica?**

È possibile visualizzare lo stato dei processi completati fino a 90 giorni. Processi completati verranno eliminati dopo 90 giorni.

**Se desidera importare o esportare più di 10 unità, cosa fare?**

Un'importazione o esportazione processo può fare riferimento solo 10 unità in un singolo processo per il servizio di importazione/esportazione. Se si desidera rendere disponibile più di 10 unità, è possibile creare più processi. Unità associati lo stesso processo devono essere spedite insieme nello stesso pacchetto.

**È possibile utilizzare un adattatore USB SATA non incluso nell'elenco consigliato?**

Se si dispone di un convertitore di cui non è elencato sopra, è possibile provare a eseguire lo strumento di importazione/esportazione Azure con il convertitore per preparare l'unità e vedere se funziona prima dell'acquisto di un convertitore di supportati.

**Il servizio formatta le unità prima che questi?**

No. Tutte le unità vengono crittografate con BitLocker.

**È possibile acquistare unità per i processi di importazione/esportazione da Microsoft?**

No. È necessario spedire unità per entrambi importare ed esportare i processi.

**Al termine del processo di importazione, verrà i dati nell'account di archiviazione? Verrà mantenuto la gerarchia di directory?**

Durante la preparazione di un'unità disco rigido per un processo di importazione, la destinazione specificata per la /dstdir: parametro. Si tratta del contenitore di destinazione nell'account di archiviazione a cui viene copiati dati dal disco rigido. In questo contenitore di destinazione, BLOB vengono creati per file virtuali vengono create per le cartelle dal disco rigido.

**Se l'unità ha file già presenti nell'account di archiviazione, il servizio sovrascriverà BLOB esistenti nell'account di archiviazione?**

Quando si prepara l'unità, è possibile specificare se i file di destinazione devono essere sovrascritti o ignorati utilizzando il parametro denominato /Disposition: < rinominare | sovrascrivere no | sovrascrivere >. Per impostazione predefinita, il servizio di rinominare i nuovi file invece di sovrascrivere BLOB esistente.

**Lo strumento di client Azure importazione/esportazione è compatibile con sistemi operativi a 32 bit?**
No. Lo strumento client è compatibile solo con sistemi operativi Windows a 64 bit. Fare riferimento alla sezione sistemi operativi in [Prerequisiti](#pre-requisites) per un elenco completo delle versioni del sistema operativo supportate.

**È consigliabile includere un valore diverso da disco rigido nel mio pacchetto?**

Spedire solo le unità disco rigido. Non includere elementi come cavi dell'alimentatore o cavi USB.

**È necessario fornire le unità USA DHL o FedEx?**

È possibile spedire unità al data center utilizzando qualsiasi vettore noti come FedEx, DHL, Sportivo o Contattaci servizio postale. Per le unità al mittente dall'interfaccia di dati, tuttavia, è necessario specificare un numero di account FedEx Stati Uniti e dell'Unione europea o un numero di account DHL nelle aree Asia e Australia.

**Esistono restrizioni con unità internazionale di spedizione?**

Si noti che il supporto fisico spedizione potrebbe essere necessario incrociato paesi diversi. Sei responsabile per garantire che il supporto fisico e i dati vengono importati e/o esportati conformemente alle leggi applicabili. Prima della distribuzione supporti fisici, consultare i propri consulenti, per verificare che i file multimediali e dati legge possono essere spediti al centro dati identificato. In questo modo per garantire che raggiunge Microsoft in modo tempestivo.

**Quando si crea un processo, l'indirizzo di spedizione è un percorso diverso da posizione account lo spazio di archiviazione. Cosa dovrei fare?**

Alcuni percorsi di archiviazione account sono mappati a posizioni di spedizione alternativo. In precedenza posizioni disponibili spedizione è possono anche temporaneamente mappare una posizione alternativa. Controllare sempre l'indirizzo di spedizione disponibili durante la creazione del processo prima di consegnare le unità.

**Perché è il significato dello stato di processo la classica portale pronunciare "spedizione" quando il sito Web Carrier Mostra il pacchetto viene recapitato?**

Lo stato nel portale di classica viene modificato da spedizione per il trasferimento quando l'unità dell'inizio dell'elaborazione. Se l'unità raggiunta la funzione, ma non ha avviato l'elaborazione, lo stato di processo apparirà come spedizione.

**Quando si distribuisce unità, il vettore chiede il nome di contatto centro di dati e il numero di telefono. Che cos'è opportuno inviare?**

Il numero di telefono è disponibile durante la creazione del processo. Se è necessario il nome del contatto, contatta l'assistenza clienti in waimportexport@microsoft.com e si fornirà queste informazioni.

**È possibile usare il servizio di Azure Importa/Esporta per copiare le cassette postali PST e dati di SharePoint in Office 365?**

Fare riferimento al [file PST di importazione o ai dati di SharePoint a Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**È possibile usare il servizio di Azure Importa/Esporta per copiare il backup non in linea per il servizio di Backup di Azure?**

Fare riferimento al [flusso di lavoro di Backup non in linea in Azure Backup](../backup/backup-azure-backup-import-export.md).

## <a name="see-also"></a>Vedere anche:

- [Configurare lo strumento di client Azure Importa/Esporta](https://msdn.microsoft.com/library/dn529112.aspx)

- [Trasferire i dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)

- [Importazione Azure esportazione resto API di esempio](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)
