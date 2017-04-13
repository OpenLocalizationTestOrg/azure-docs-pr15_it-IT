<properties
    pageTitle="Introduzione all'archiviazione | Microsoft Azure"
    description="Panoramica di spazio di archiviazione di Azure, archiviazione di dati online Microsoft nel cloud. Informazioni su come utilizzare la soluzione migliore di spazio di archiviazione cloud disponibili nelle applicazioni."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="tamram"/>

# <a name="introduction-to-microsoft-azure-storage"></a>Introduzione all'archiviazione di Microsoft Azure

## <a name="overview"></a>Panoramica

Spazio di archiviazione Azure è la soluzione di archiviazione cloud per moderne applicazioni basate su durata, disponibilità e scalabilità per soddisfare le esigenze dei clienti. Leggere questo articolo, gli sviluppatori, i professionisti IT e business decision ai responsabili delle informazioni sul:

- Qual è lo spazio di archiviazione di Azure, e come è possibile eseguire i vantaggi del cloud per dispositivi mobili, server e le applicazioni desktop
- Quali tipi di dati è possibile archiviare con i servizi di archiviazione Azure: blob dati (oggetto), dati di una tabella NoSQL, messaggi coda e condivisioni file.
- Come viene gestito il accesso ai dati in archiviazione Azure
- Come i dati di Azure lo spazio di archiviazione sono una chiamata effettuati permanenti tramite ridondanza e di replica
- Dove trovare accanto a compilare un'applicazione di archiviazione Azure

Per iniziare a utilizzarlo con lo spazio di archiviazione di Azure rapidamente, vedere [Guida introduttiva di Azure lo spazio di archiviazione di cinque minuti](storage-getting-started-guide.md).

Per informazioni dettagliate sugli strumenti, raccolte e altre risorse per l'utilizzo di archiviazione di Azure, vedere [Passaggi successivi](#next-steps) .

## <a name="what-is-azure-storage"></a>Qual è lo spazio di archiviazione di Azure?

Il cloud computing consente nuovi scenari per applicazioni che richiedono scalable, permanente e disponibilità dello spazio di archiviazione per i dati – sono esattamente perché Microsoft sviluppato archiviazione Azure. Oltre consentendo agli sviluppatori di creare applicazioni su larga scala per supportare nuovi scenari, lo spazio di archiviazione di Azure fornisce anche la base dello spazio di archiviazione per macchine virtuali di Azure, testimonia ulteriormente la sua resistenza.

Archiviazione Azure è market scalable, pertanto è possibile archiviare e processo centinaia di terabyte di dati per supportare gli scenari di dati necessari affinché analisi scientifiche, finanziari e le applicazioni multimediali. Oppure è possibile archiviare piccole quantità di dati necessari per un sito Web small business. Nel punto in cui possono essere suddivise alle proprie esigenze, pagando solo per i dati che è archiviato. Archiviazione Azure attualmente archivia le decine di trilioni di oggetti customer univoci e gestisce milioni di richieste al secondo in Media.

Spazio di archiviazione Azure è flessibile, pertanto è possibile progettare applicazioni per il pubblico globale e ridimensionare le applicazioni in base alle esigenze - in termini della quantità di dati archiviati e il numero di richieste per tale oggetto. Pagando solo per i quali si utilizza e solo quando si utilizza.

Archiviazione Azure utilizza un sistema automatico partizioni che automaticamente carica i saldi i dati in base al traffico. Questo errore indica che alle richieste su Ingrandisci l'applicazione, lo spazio di archiviazione di Azure automaticamente Assegna risorse appropriate in base alle loro.

Archiviazione Azure è accessibile da un punto qualsiasi nel mondo, da qualsiasi tipo di applicazione, se è in esecuzione nel cloud, sul desktop, su un server locale o su un cellulare o tablet dispositivo. È possibile utilizzare lo spazio di archiviazione di Azure in scenari per dispositivi mobili nel punto in cui l'applicazione contiene un sottoinsieme di dati sul dispositivo e Sincronizza con una serie completa di dati archiviati nel cloud.

Spazio di archiviazione Azure supporta clienti utilizzando un set di sistemi operativi (inclusi Windows e Linux) e una varietà di linguaggi di programmazione (inclusi .NET, Java, Node, Python, trascrizione, PHP e C++ e linguaggi di programmazione per dispositivi mobili) per lo sviluppo di comodo. Inoltre, lo spazio di archiviazione Azure esposta tramite le API REST semplici, sono disponibili per qualsiasi client in grado di inviare e ricevere dati tramite HTTP/HTTPS le risorse dati.

Lo spazio di archiviazione di Azure Premium offre il supporto per unità massime prestazioni, bassa latenza per i/o intensivo carichi di lavoro di esecuzione in macchine virtuali di Azure. Con l'archiviazione di Azure Premium, è possibile collegare più dischi di dati permanenti una macchina virtuale e configurarli per soddisfare i requisiti di prestazioni. Ogni disco dati è supportato da un disco SSD nello spazio di archiviazione di Azure Premium per ottenere prestazioni ottimali dei / o. Vedere [lo spazio di archiviazione di Premium: spazio di archiviazione di High-Performance per carichi di lavoro di Azure macchina virtuale](storage-premium-storage.md) per altri dettagli.

## <a name="introducing-the-azure-storage-services"></a>Introduzione ai servizi di archiviazione Azure

Spazio di archiviazione Azure fornisce i seguenti quattro servizi: Blob lo spazio di archiviazione, archiviazione tabella, lo spazio di archiviazione di coda e archiviazione dei File.

- Archiviazione BLOB memorizza i dati non strutturati oggetto. Un blob può essere qualsiasi tipo di testo o dati binari, ad esempio un documento, un file multimediale o un programma di installazione applicazioni. Archiviazione BLOB viene definita anche lo spazio di archiviazione di oggetto.
- Archiviazione tabella archivia set di dati strutturati. Archiviazione tabelle è un archivio di dati dell'attributo chiave NoSQL, che consente di accedere rapidamente alle grandi quantità di dati e un rapido sviluppo.
- Spazio di archiviazione coda offre messaggistica affidabile per l'elaborazione del flusso di lavoro e per le comunicazioni tra i componenti di servizi cloud.
- Spazio di archiviazione file offre condivisa dello spazio di archiviazione per le applicazioni legacy utilizzando il protocollo SMB standard. Azure macchine virtuali e servizi cloud possono condividere i dati di file tra componenti dell'applicazione tramite condivisioni collegate e applicazioni locale possono accedere ai dati di file in una condivisione tramite il servizio di File API REST.

Un account di archiviazione Azure è un account protetto che consente di accedere a servizi di archiviazione Azure. L'account di archiviazione offre lo spazio dei nomi univoco per le risorse di archiviazione. L'immagine riportata di seguito sono riportate le relazioni tra le risorse di archiviazione Azure in un account di archiviazione:

![Risorse di archiviazione Azure](./media/storage-introduction/storage-concepts.png)

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

[AZURE.INCLUDE [storage-versions-include](../../includes/storage-versions-include.md)]

## <a name="blob-storage"></a>Archiviazione BLOB

Per gli utenti con grandi quantità di dati non strutturati oggetto per l'archiviazione nel cloud, archiviazione Blob offre una soluzione conveniente e scalabilità. È possibile utilizzare lo spazio di archiviazione Blob per archiviare il contenuto, ad esempio:

- Documenti
- Dati di social networking, ad esempio foto, video, musica e blog
- Backup dei file, computer, database e dispositivi
- Immagini e testo per le applicazioni web
- Dati di configurazione per applicazioni cloud
- Dati, ad esempio i registri e altri set di dati di grandi dimensioni

Ogni blob sono organizzate in un contenitore. I contenitori forniscono anche utile per assegnare criteri di sicurezza ai gruppi di oggetti. Un account di archiviazione può contenere un numero di contenitori, e un contenitore può contenere qualsiasi numero di BLOB fino al limite di capacità di 500 TB dell'account di archiviazione.  

BLOB archiviazione offre tre tipi di BLOB, bloccare BLOB, accodare BLOB e BLOB di pagina (dischi).

- Blocco BLOB sono ottimizzati per streaming e archiviazione di oggetti cloud e costituiscono un'ottima scelta per l'archiviazione di documenti, file multimediali, l'esecuzione di backup e così via.
- Accodare BLOB sono simili a BLOB blocco, ma sono ottimizzati per le operazioni di Accodamento. Solo per l'aggiunta di un nuovo blocco alla fine, è possibile aggiornare un blob di Accodamento. Accodare BLOB costituiscono un'ottima scelta per gli scenari, ad esempio la registrazione, nel punto in cui devono essere scritti solo fino alla fine del blob nuovi dati.
- Pagina BLOB sono ottimizzati per la rappresentazione IaaS dischi e supporto casuale scrive e potrebbe essere fino a 1 TB dimensioni. Una rete macchina virtuale Azure associati IaaS disco è un disco rigido virtuale memorizzato come blob pagina.

Per grandi set di dati in cui le limitazioni della rete apportare durante il caricamento o il download dei dati a archiviazione Blob in rete non realistici, è possibile fornire un disco rigido a Microsoft per importare o esportare i dati direttamente dall'interfaccia di dati. Vedere [usare il servizio di importazione/esportazione Microsoft Azure per trasferire i dati per lo spazio di archiviazione Blob](storage-import-export-service.md).

## <a name="table-storage"></a>Archiviazione tabella

Applicazioni moderne spesso richiedono archivi dati con maggiore scalabilità e flessibilità rispetto alle generazioni precedenti di software necessari. Archiviazione tabella offre disponibilità, market scalable lo spazio di archiviazione, in modo che l'applicazione può automaticamente ridimensionare in base alle esigenze degli utenti. Archiviazione tabella archivio di Microsoft NoSQL tasto/attributi, la relativa progettazione schemaless, rendendo diversa da database relazionali tradizionali. Con un archivio di dati schemaless, è facile adattare i dati come le esigenze dei evolve applicazione. Archiviazione tabelle è facile da usare in modo che gli sviluppatori possono creare rapidamente le applicazioni. Accesso ai dati è veloce e conveniente per tutti i tipi di applicazioni.  Archiviazione tabelle è in genere notevolmente inferiori costo tradizionale SQL per indicare i volumi simili dei dati.

Archiviazione tabelle è un archivio attributo key, vale a dire che ogni valore in una tabella viene archiviato con un nome di proprietà tipizzata. Il nome della proprietà utilizzabili per filtrare e specificare i criteri di selezione. Un insieme di proprietà e i rispettivi valori costituiscono un'entità. Poiché archiviazione tabelle schemaless, due entità nella stessa tabella possono contenere diversi insiemi di proprietà e le proprietà possono essere di tipo diverso.

È possibile utilizzare lo spazio di archiviazione tabella per memorizzare flessibile set di dati, ad esempio dati utente per le applicazioni web, rubriche, informazioni sul dispositivo e qualsiasi altro tipo di metadati richiesti dal servizio.  È possibile archiviare qualsiasi numero di entità in una tabella e un account di archiviazione può contenere qualsiasi numero di tabelle, fino al limite di capacità dell'account di archiviazione.

Ad esempio BLOB e code, gli sviluppatori possono gestire e tabella di access protocolli archiviazione con resto standard, ma lo spazio di archiviazione tabella supporta anche un sottoinsieme del protocollo OData, semplificando la funzionalità di query avanzate e l'attivazione JSON e AtomPub (basati su XML) formati.

Per le applicazioni basate su Internet odierna, database NoSQL lo spazio di archiviazione tabella offrono un'alternativa al database relazionali tradizionali.

## <a name="queue-storage"></a>Spazio di archiviazione di coda

Nella progettazione di applicazioni per scala, componenti dell'applicazione spesso sono disaccoppiati, in modo che sia possibile adattarli in modo indipendente. Spazio di archiviazione coda offre una soluzione di messaggistica affidabile per le comunicazioni asincrone tra i componenti di applicazioni, se sono in esecuzione nel cloud, sul desktop, su un server locale o in un dispositivo mobile. Archiviazione coda supporta anche la gestione attività asincrone e creazione di flussi di lavoro di processo.

Un account di archiviazione può contenere un numero di code. Una coda può contenere un numero di messaggi, fino al limite di capacità dell'account di archiviazione. Singoli messaggi potrebbero essere fino a 64 KB.

## <a name="file-storage"></a>Spazio di archiviazione file

Spazio di archiviazione File Azure offre condivisioni file di piccole e medie imprese basate su cloud, in modo che è possibile eseguire la migrazione di applicazioni legacy che utilizzano condivisioni file in Azure rapidamente e senza riscrittura costosa. Con lo spazio di archiviazione di File di Azure, applicazioni in esecuzione in macchine virtuali di Azure o servizi cloud possono collegare una condivisione file nel cloud, nello stesso modo in cui un'applicazione desktop installa una tipica condivisione piccole e medie imprese. Qualsiasi numero di componenti dell'applicazione può installare e accedere alla condivisione di spazio di archiviazione File contemporaneamente.

Poiché una condivisione File di spazio di archiviazione è una condivisione di file di piccole e medie imprese standard, le applicazioni in esecuzione in Azure possono accedere dati nella condivisione tramite il sistema di file I/O APIs. Gli sviluppatori possono pertanto sfruttare il codice esistente e le competenze necessarie per eseguire la migrazione di applicazioni esistenti. Professionisti IT possono utilizzare i cmdlet di PowerShell per creare, installare e gestire condivisioni dello spazio di archiviazione File come parte dell'amministrazione delle applicazioni Azure.

Come qualsiasi altro servizio di archiviazione Azure spazio di archiviazione File espone un API REST per accedere ai dati in una condivisione. Le applicazioni in locale possono chiamare spazio di archiviazione File API REST per accedere ai dati in una condivisione file. In questo modo, un'organizzazione può scegliere di eseguire la migrazione di alcune applicazioni legacy in Azure e continuare ad altri utenti di esecuzione all'interno della propria organizzazione. Si noti che sono possibili solo per le applicazioni in esecuzione in Azure; montaggio una condivisione file condivisione di file tramite l'API REST può accedere solo a un'applicazione locale.

Applicazioni distribuite inoltre possono utilizzare lo spazio di archiviazione di File per archiviare e condividere dati applicazione utile e sviluppo e strumenti per la verifica. Ad esempio, un'applicazione può archiviare i file di configurazione e dati di diagnostica, ad esempio un arresto anomalo, metriche e registri trasferisce in un File di spazio di archiviazione condividere in modo che siano disponibili a più macchine virtuali o ruoli. Gli sviluppatori e per gli amministratori possono archiviare utilità necessarie per creare o gestire un'applicazione in una condivisione di spazio di archiviazione di File che è disponibile per tutti i componenti, invece di installarli in ogni macchina virtuale o istanza del ruolo.

## <a name="access-to-blob-table-queue-and-file-resources"></a>Accedere a Blob, tabella, coda e le risorse del File

Per impostazione predefinita, solo il proprietario dell'account di archiviazione può accedere alle risorse di account di archiviazione. Per la protezione dei dati, ogni richiesta effettuata risorse nell'account deve essere autenticato. Autenticazione basata su un modello di chiave condivisa. BLOB possono anche essere configurati per supportare l'autenticazione anonima.

L'account di archiviazione è assegnata due privato tasti di scelta al momento della creazione utilizzati per l'autenticazione. La presenza di due chiavi garantisce che l'applicazione rimane disponibile quando si regolarmente rigenerare le chiavi per la gestione delle chiavi protezione comuni.

Se è necessario consentire agli utenti controllo dell'accesso alle risorse di archiviazione, è possibile creare una firma accesso condiviso. Una firma di accesso condiviso (SA) è un token che può essere aggiunto a un URL che consente l'accesso delegato a una risorsa di archiviazione. Chiunque possiede il token può accedere alla risorsa che punta a con le autorizzazioni che specifica per il periodo di tempo che non è valido. A partire da versione 04/2015/05, lo spazio di archiviazione di Azure supporta due tipi di firme accesso condiviso: servizio SA e account sa.

Il servizio SA delega accesso a una risorsa in solo uno dei servizi di archiviazione: il servizio Blob, coda, una tabella o File.

Un account sa delega accesso alle risorse in uno o più i servizi di archiviazione. Si può delegare l'accesso alle operazioni a livello di servizio che non sono disponibili in un servizio SA. È inoltre possibile delegare l'accesso in lettura, scrittura ed eliminare operazioni su blob contenitori, tabelle, code e nelle condivisioni file che non sono consentite con un servizio SA.

Infine, è possibile specificare che un contenitore e il relativo BLOB o blob specifico, sono disponibili per l'accesso pubblico. Quando si indica che un contenitore o blob sia pubblica, tutti gli utenti possano leggerlo in modalità anonima; autenticazione non è necessaria.  BLOB e contenitori pubblici sono utili per l'esposizione di risorse, ad esempio documenti ospitate in siti Web ed elementi multimediali.  Per ridurre la latenza di rete per un pubblico globale, è possibile memorizzare dati blob utilizzati da un sito Web con la rete CDN di Azure.

Per ulteriori informazioni sulle firme di accesso condiviso, vedere [Utilizzo condiviso accesso firme (SA)](storage-dotnet-shared-access-signature-part-1.md) . Per ulteriori informazioni sull'accesso sicuro all'account di archiviazione, vedere [gestire anonimo accesso in lettura contenitori e BLOB](storage-manage-access-to-resources.md) e [l'autenticazione per i servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx) .

## <a name="replication-for-durability-and-high-availability"></a>Replica per la durata e una maggiore disponibilità

I dati nell'account di archiviazione di Microsoft Azure viene sempre replicati per garantire la durata e disponibilità. La replica copia i dati all'interno dello stesso data center, o in un secondo data center, in base all'opzione replica scelto. La replica consente di proteggere i dati e mantiene l'applicazione i tempi in caso di errori hardware temporaneo. Se i dati sono replicati in un secondo data center, che anche consente di proteggere i dati da un errore irreversibile nella posizione principale.

La replica garantisce che l'account di archiviazione soddisfi il [Livello di servizio contratto (contratto di servizio) per l'archiviazione](https://azure.microsoft.com/support/legal/sla/storage/) anche in caso di errori. Vedere che il contratto di servizio per informazioni sull'archiviazione di Azure garanzie per la durata e la disponibilità. 

Quando si crea un account di archiviazione, è possibile selezionare una delle seguenti opzioni di replica:  

- **Archiviazione localmente ridondanti (LRS).** Spazio di archiviazione in locale ridondante mantiene tre copie dei dati. LRS viene replicato tre volte all'interno di un unico centro dati in una singola regione. LRS consente di proteggere i dati da errori hardware normale, ma non in caso di errore di un unico centro dati.  

    LRS è disponibile uno sconto. Per la durata massima, è consigliabile utilizzare geografico ridondanti lo spazio di archiviazione, descritto di seguito.


- **Zona ridondanti dello spazio di archiviazione (ZRS).** Spazio di archiviazione zona ridondanti mantiene tre copie dei dati. ZRS è replicate tre volte nelle strutture di due o tre, in una singola regione o tra due aree, fornire durata superiore a LRS. ZRS garantisce che i dati permanenti in una singola regione.  

    ZRS fornisce superiori di durata LRS; Tuttavia, per la durata massima, è consigliabile utilizzare geografico ridondanti lo spazio di archiviazione, descritto di seguito.  

    > [AZURE.NOTE] ZRS non è attualmente disponibile solo per i BLOB blocco ed è solo supportati per le versioni 2014-02-14 e versioni successive.
    >
    > Dopo aver creato l'account di archiviazione e selezionato ZRS, non è possibile convertire, è possibile utilizzare qualsiasi altro tipo di replica, o viceversa.

- **Spazio di archiviazione ridondanti geografico (GRS)**. GRS mantiene sei copie dei dati. Con GRS, i dati vengono replicati tre volte all'interno dell'area principale e anche replicati tre volte in un'area secondaria centinaia di miglia all'esterno dell'area principale, fornire il massimo livello di durata. In caso di errore all'area principale, lo spazio di archiviazione di Azure sarà il controllo a area secondario. GRS garantisce che i dati permanenti in due aree separate.

    Per informazioni su associazioni primario e secondario per area geografica, vedere [Le aree di Azure](https://azure.microsoft.com/regions/).

- **Accesso in lettura geografico ridondanti dello spazio di archiviazione (RA GRS)**. Spazio di archiviazione ridondanti geografico di accesso in lettura replica i dati in un'area geografica secondaria e vengono forniti anche accesso in lettura ai dati del percorso secondario. Spazio di archiviazione geografico ridondanti di accesso in lettura consente di accedere ai dati da primario o posizione secondaria nel caso in cui non è disponibile un'unica posizione. Spazio di archiviazione ridondanti geografico di accesso in lettura è l'opzione predefinita per l'account di archiviazione per impostazione predefinita, quando si crea. 

    > [AZURE.IMPORTANT] È possibile modificare la modalità di replica dei dati dopo la creazione di account di archiviazione, a meno che non è specificato ZRS durante la creazione dell'account. Tuttavia, si noti che possono causare un trasferimento di dati aggiuntivi monouso costo se si passa dalla LRS GRS o RA GRS.

Per informazioni dettagliate sulle opzioni di replica di archiviazione, vedere [replica di archiviazione Azure](storage-redundancy.md) .

Per informazioni sui prezzi delle replica account lo spazio di archiviazione, vedere [Lo spazio di archiviazione di Azure prezzi](https://azure.microsoft.com/pricing/details/storage/). Per ulteriori informazioni su quali servizi sono disponibili in ogni area geografica, vedere [Le aree di Azure](https://azure.microsoft.com/regions/#services) .

Per informazioni dettagliate architettonica sulla durata con lo spazio di archiviazione di Azure, vedere [SOSP carta - archiviazione Azure: A altamente disponibili servizio di archiviazione Cloud con coerenza sicuro](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).


## <a name="transferring-data-to-and-from-azure-storage"></a>Trasferire i dati da e verso lo spazio di archiviazione Azure

È possibile utilizzare l'utilità della riga di comando AzCopy per copiare blob, file e dati di una tabella nel proprio account di archiviazione o tra gli account di archiviazione. Per ulteriori informazioni, vedere [trasferire i dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md) .

AzCopy integrato nella parte superiore della [Raccolta di spostamento dei dati di Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), che non è attualmente disponibile in anteprima.

Il servizio di importazione/esportazione Azure consente di importare i dati blob o esportare dati blob dal proprio account di archiviazione tramite un disco rigido inviato all'interfaccia di dati di Azure. Per ulteriori informazioni sul servizio di importazione/esportazione, vedere [usare il servizio di importazione/esportazione Microsoft Azure per trasferire dati a archiviazione Blob](storage-import-export-service.md).

## <a name="pricing"></a>Prezzi

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

## <a name="storage-apis-libraries-and-tools"></a>Strumenti, raccolte e le API di spazio di archiviazione

Risorse di archiviazione Azure è possibile accedervi da qualsiasi linguaggio che rendono le richieste di HTTP/HTTPS. Inoltre, lo spazio di archiviazione di Azure offre le librerie di programmazione per diverse lingue popolari. Queste librerie di semplificare molti aspetti dell'utilizzo di Azure lo spazio di archiviazione per la gestione dei dettagli, ad esempio chiamata icona del e asincrono, il batch di operazioni, la gestione delle eccezioni, tentativi automatici, comportamento operativo e così via. Raccolte sono attualmente disponibili per le lingue e piattaforme con altri utenti nella pipeline seguenti:

### <a name="azure-storage-data-services"></a>Servizi di dati di archiviazione Azure

- [Servizi di archiviazione API REST](http://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Raccolta di Client di spazio di archiviazione per .NET, Windows Phone e Windows Runtime](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Raccolta di Client di spazio di archiviazione per C++](https://github.com/Azure/azure-storage-cpp)
- [Raccolta di Client lo spazio di archiviazione per Java/Android](/develop/java/)
- [Raccolta di Client di spazio di archiviazione per Node](http://dl.windowsazure.com/nodestoragedocs/index.html)
- [Raccolta di Client di spazio di archiviazione per PHP](/develop/php/)
- [Raccolta di Client di spazio di archiviazione per trascrizione](/develop/ruby/)
- [Raccolta di Client di spazio di archiviazione per Python](/develop/python/)
- [Cmdlet di spazio di archiviazione per PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt269418.aspx)

### <a name="azure-storage-management-services"></a>Servizi di gestione di archiviazione Azure

- [Riferimento API REST del Provider di spazio di archiviazione delle risorse](https://msdn.microsoft.com/library/azure/mt163683.aspx)
- [Libreria di spazio di archiviazione delle risorse Provider Client per .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx)
- [Cmdlet di Provider di spazio di archiviazione delle risorse per PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt607151.aspx)
- [API REST di Gestione servizio di archiviazione (classica)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-services"></a>Servizi di spostamento dei dati di archiviazione Azure

- [API REST di servizio di archiviazione Importa/Esporta](https://msdn.microsoft.com/library/azure/dn529096.aspx)
- [Raccolta di spazio di archiviazione dati movimento Client per .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### <a name="tools-and-utilities"></a>Strumenti e utilità

- [Esplora archivi Azure](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
- [Strumenti Client di archiviazione Azure](storage-explorers.md)
- [Strumenti e SDK azure](https://azure.microsoft.com/tools/)
- [Spazio di archiviazione Azure emulatore](http://www.microsoft.com/download/details.aspx?id=43709)
- [PowerShell Azure](../powershell-install-configure.md)
- [Utilità AzCopy](http://aka.ms/downloadazcopy)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'archiviazione di Azure, esplorare queste risorse:

### <a name="documentation"></a>Documentazione

- [Documentazione di archiviazione Azure](https://azure.microsoft.com/documentation/services/storage/)

### <a name="for-administrators"></a>Per gli amministratori

- [Utilizzo di PowerShell Azure con lo spazio di archiviazione Azure](storage-powershell-guide-full.md)
- [Usa CLI Azure con lo spazio di archiviazione Azure](storage-azure-cli.md)

### <a name="for-net-developers"></a>Per gli sviluppatori .NET

- [Guida introduttiva a archiviazione Blob Azure con .NET](storage-dotnet-how-to-use-blobs.md)
- [Guida introduttiva a archiviazione tabella Azure tramite .NET](storage-dotnet-how-to-use-tables.md)
- [Guida introduttiva di archiviazione di Azure coda mediante .NET](storage-dotnet-how-to-use-queues.md)
- [Guida introduttiva a spazio di archiviazione di Azure File in Windows](storage-dotnet-how-to-use-files.md)

### <a name="for-javaandroid-developers"></a>Per gli sviluppatori Java/Android

- [Come usare archiviazione Blob da Java](storage-java-how-to-use-blob-storage.md)
- [Come utilizzare lo spazio di archiviazione tabella da Java](storage-java-how-to-use-table-storage.md)
- [Come utilizzare lo spazio di archiviazione coda da Java](storage-java-how-to-use-queue-storage.md)
- [Come utilizzare lo spazio di archiviazione di File da Java](storage-java-how-to-use-file-storage.md)

### <a name="for-nodejs-developers"></a>Per gli sviluppatori Node

- [Come usare archiviazione Blob da Node](storage-nodejs-how-to-use-blob-storage.md)
- [Come utilizzare lo spazio di archiviazione tabella da Node](storage-nodejs-how-to-use-table-storage.md)
- [Come utilizzare lo spazio di archiviazione coda da Node](storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>Per gli sviluppatori PHP

- [Come usare archiviazione Blob da PHP](storage-php-how-to-use-blobs.md)
- [Come utilizzare lo spazio di archiviazione tabella da PHP](storage-php-how-to-use-table-storage.md)
- [Come utilizzare lo spazio di archiviazione coda da PHP](storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Per gli sviluppatori trascrizione

- [Come usare archiviazione Blob da trascrizione](storage-ruby-how-to-use-blob-storage.md)
- [Come utilizzare lo spazio di archiviazione tabella da trascrizione](storage-ruby-how-to-use-table-storage.md)
- [Come utilizzare lo spazio di archiviazione di coda da trascrizione](storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Per gli sviluppatori di Python

- [Come usare archiviazione Blob da Python](storage-python-how-to-use-blob-storage.md)
- [Come utilizzare lo spazio di archiviazione tabella da Python](storage-python-how-to-use-table-storage.md)
- [Come utilizzare lo spazio di archiviazione coda da Python](storage-python-how-to-use-queue-storage.md)
- [Come utilizzare lo spazio di archiviazione di File da Python](storage-python-how-to-use-file-storage.md)
