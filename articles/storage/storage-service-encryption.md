<properties
    pageTitle="La crittografia del servizio di archiviazione Azure per dati inattivi | Microsoft Azure"
    description="Usare la caratteristica la crittografia del servizio di archiviazione di Azure per crittografare l'archiviazione Blob Azure sul lato del servizio per l'archiviazione dei dati e decrittografarlo quando recupera i dati."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="robinsh"/>

# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Crittografia del servizio di archiviazione Azure per dati inattivi

Azure lo spazio di archiviazione servizio di crittografia (SSE) per dati inattivi consente di proteggere e proteggere i dati per soddisfare gli impegni di conformità e la sicurezza dell'organizzazione. Con questa caratteristica, lo spazio di archiviazione di Azure automaticamente crittografa i dati prima di salvare in modo permanente allo spazio di archiviazione e decrittografa prima di recupero. La crittografia, decrittografia e la gestione delle chiavi sono totalmente trasparenti agli utenti.

Le sezioni seguenti includono indicazioni dettagliate su come usare le funzionalità di crittografia di servizio di archiviazione, nonché gli scenari supportati e l'utente si verificano.

## <a name="overview"></a>Panoramica

Spazio di archiviazione Azure fornisce una serie completa di funzionalità di sicurezza che insieme consentono agli sviluppatori di creare applicazioni protette. Proteggere i dati in transito tra un'applicazione e Azure utilizzando [La crittografia lato Client](storage-client-side-encryption.md), HTTPs o 3.0 piccole e medie imprese. La crittografia del servizio di archiviazione offre crittografia inattivi, gestione crittografia, decrittografia e la gestione delle chiavi in modo completamente trasparente. Tutti i dati vengono crittografati tramite 256 bit [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), uno dei blocchi più avanzati disponibile.

SSE funziona crittografando i dati quando è scritto allo spazio di archiviazione di Azure e possono essere usata per blocco BLOB, BLOB di pagine e accoda BLOB. Funziona per le operazioni seguenti:

-   Gli account di archiviazione generale e gli account di archiviazione Blob
-   Spazio di archiviazione standard e lo spazio di archiviazione Premium 
-   Ridondanza tutti i livelli (LRS, ZRS, GRS, RA GRS)
-   Lo spazio di archiviazione di Azure Manager delle risorse account (ma non classica) 
-   Tutte le aree

Per abilitare o disabilitare la crittografia del servizio di archiviazione per un account di archiviazione, accedere al [portale di Azure](https://azure.portal.com) e selezionare un account di archiviazione. Nella e impostazioni, cercare la sezione servizio Blob come mostrato in questa schermata e fare clic su crittografia.

![Opzione di crittografia portale schermata che mostra](./media/storage-service-encryption/image1.png)

Dopo aver fatto clic l'impostazione di crittografia, è possibile attivare o disattivare la crittografia del servizio di archiviazione.

![Portale schermata che mostra le proprietà di crittografia](./media/storage-service-encryption/image2.png)

##<a name="encryption-scenarios"></a>Scenari di crittografia

La crittografia del servizio di archiviazione per attivare un livello di account di archiviazione. Supporta scenari seguenti:

-   La crittografia dei blob blocco, aggiungere BLOB e BLOB di pagine.

-   Crittografia di archiviati dischi rigidi virtuali e modelli scaricati in Azure locali.

-   La crittografia del sistema operativo sottostante e dischi di dati per le macchine virtuali IaaS creati con i dischi rigidi virtuali.

SSE comporta le limitazioni seguenti:

-   Crittografia di account di archiviazione classica non è supportata.

-   Crittografia di spazio di archiviazione classica viene eseguita la migrazione di account per gli account di archiviazione di Manager delle risorse non è supportata.

-   Dati esistenti - SSE Crittografa dati appena creato solo dopo aver attivata la crittografia. Se ad esempio si crea un nuovo account di archiviazione di Manager delle risorse ma non attiva la crittografia e quindi caricare BLOB o dischi rigidi virtuali archiviati in account di archiviazione e quindi attivare SSE, tali BLOB non verranno crittografati a meno che vengono riscritte o copiati.

-   Supporto Marketplace - abilita la crittografia delle macchine virtuali creati da Marketplace tramite il [portale di Azure](https://portal.azure.com), PowerShell e Azure CLI. L'immagine di base del disco rigido virtuale rimarrà non crittografata; Tuttavia, qualsiasi scrive eseguite dopo la macchina virtuale è caricare un verranno crittografati.

-   Tabella, code, e dati di file non verranno crittografati.

##<a name="getting-started"></a>Guida introduttiva

###<a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Passaggio 1: [creare un nuovo account di archiviazione](storage-create-storage-account.md).

###<a name="step-2-enable-encryption"></a>Passaggio 2: Abilitare la crittografia.

È possibile abilitare la crittografia tramite il [portale di Azure](https://portal.azure.com).

> [AZURE.NOTE] Se si desidera abilitare o disabilitare la crittografia di servizio di archiviazione di un account di archiviazione a livello di programmazione, è possibile utilizzare l' [API REST di Azure lo spazio di archiviazione delle risorse Provider](https://msdn.microsoft.com/library/azure/mt163683.aspx), la [Libreria di spazio di archiviazione delle risorse Provider Client per .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx), [PowerShell Azure](../powershell-install-configure.md)o [CLI Azure](storage-azure-cli.md).

###<a name="step-3-copy-data-to-storage-account"></a>Passaggio 3: Copiare i dati all'account di archiviazione

Se si abilita SSE in un account di archiviazione e quindi scrivere BLOB in quell'account lo spazio di archiviazione, i BLOB verranno crittografati. Qualsiasi BLOB già disponibile nell'account di archiviazione non verranno crittografati solo dopo che riscrittura. È possibile copiare i dati da account uno spazio di archiviazione a uno con SSE crittografati, o persino attivare SSE e copiare i BLOB da un contenitore per assicurarsi che dati precedenti sono crittografati. È possibile usare uno dei seguenti strumenti per eseguire questa operazione.

#### <a name="using-azcopy"></a>Utilizzo AzCopy

AzCopy è un'utilità della riga di comando di Windows progettata per la copia di dati da e verso archiviazione Blob di Microsoft Azure, File e una tabella usando i comandi semplici con prestazioni ottimali. È possibile usare questa per copiare il BLOB da un account di archiviazione a un altro che ha SSE abilitato. 

Per ulteriori informazioni, visitare [il trasferimento di dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md).

#### <a name="using-the-storage-client-libraries"></a>Utilizzo di raccolte di Client lo spazio di archiviazione

È possibile copiare dati blob a e dallo spazio di archiviazione blob o tra gli account di archiviazione utilizzando il nostro vasta gamma di librerie Client lo spazio di archiviazione tra .NET, C++, Java, Android, Node, PHP, Python e trascrizione.

Per ulteriori informazioni, vedere la [Guida introduttiva a archiviazione Blob Azure con .NET](storage-dotnet-how-to-use-blobs.md).

#### <a name="using-a-storage-explorer"></a>Utilizzando una finestra di esplorazione di spazio di archiviazione

È possibile utilizzare una finestra di esplorazione di spazio di archiviazione per creare gli account di archiviazione, caricare e scaricare i dati, visualizzare contenuto di BLOB e spostarsi tra directory. È possibile usare uno di questi piani per caricare BLOB al proprio account di archiviazione con attivata la crittografia. Con alcune finestre di esplorazione di spazio di archiviazione, è anche possibile copiare dati dallo spazio di archiviazione blob esistente in un contenitore diverso nell'account di archiviazione o un nuovo account di archiviazione contenente SSE abilitato.

Per ulteriori informazioni, visitare [Gli elenchi di cartelle di Azure lo spazio di archiviazione](storage-explorers.md).

###<a name="step-4-query-the-status-of-the-encrypted-data"></a>Passaggio 4: Verificare lo stato dei dati crittografati

È stata distribuita una versione aggiornata di raccolte Client lo spazio di archiviazione che consente di eseguire una query lo stato di un oggetto per determinare se è crittografato o meno. Esempi verranno aggiunti al documento nel prossimo futuro.

Nel frattempo, è possibile chiamare [Ottenere le proprietà dell'Account](https://msdn.microsoft.com/library/azure/mt163553.aspx) per verificare che l'account di archiviazione è attivata la crittografia o visualizzare le proprietà di account di archiviazione nel portale di Azure.

##<a name="encryption-and-decryption-workflow"></a>Crittografia e decrittografia flusso di lavoro

Ecco una breve descrizione del flusso di lavoro crittografia/decrittografia:

-   Il cliente abilita la crittografia per l'account di archiviazione.

-   Quando il cliente scrive nuovi dati (inserire Blob, inserire blocco, inserire pagina e così via) a archiviazione Blob; ogni scrittura è crittografato tramite 256 bit [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), uno dei blocchi più avanzati disponibile.

-   Quando il cliente deve per accedere ai dati (ottenere Blob e così via), i dati vengono automaticamente decrittografare prima di restituire all'utente.

-   Se la crittografia è disattivata, scrive nuovi non vengono crittografate e dati crittografati esistenti rimangono crittografati solo dopo riscrittura dall'utente. La crittografia è attivata, verranno crittografati scrive a archiviazione Blob. Lo stato dei dati non modificata con l'utente per alternare tra attivazione/disattivazione di crittografia per l'account di archiviazione.

-   Tutte le chiavi di crittografia vengono archiviate, crittografate e gestite da Microsoft.

##<a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Domande frequenti sulla crittografia del servizio di archiviazione per i dati inattivi

**D: si ha un account di archiviazione classica esistente. È possibile attivare SSE su di esso?**

R: no, SSE è disponibile solo per gli account di archiviazione di Manager delle risorse.

**D: come è possibile crittografare i dati nell'account di archiviazione classica?**

R: è possibile creare un nuovo account di archiviazione di Manager delle risorse e copiare i dati usando [AzCopy](storage-use-azcopy.md) dall'account di archiviazione classica esistente al proprio account di archiviazione Manager delle risorse appena creato. 

È anche possibile eseguire la migrazione di account di archiviazione classica a un account di archiviazione gestire le risorse. Per ulteriori informazioni, vedere [Piattaforme supportate risorse per la migrazione di IaaS da classico a Manager delle risorse](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/).

**D: si ha un account di spazio di archiviazione Manager delle risorse esistenti. È possibile attivare SSE su di esso?**

R: Sì, ma solo appena scritto BLOB verranno crittografati. Non tornare indietro e crittografare i dati che era già presenti. 

**D: si desidera crittografare i dati correnti in un account di archiviazione Manager delle risorse esistente?**

R: è possibile abilitare SSE in qualsiasi momento in un account di archiviazione di Manager delle risorse. Tuttavia, non verranno crittografati BLOB già presenti. Per crittografare tali BLOB, è possibile copiarli in un altro nome o un altro contenitore e quindi rimuovere le versioni non crittografate.

**D: utilizzo di memorizzazione Premium. è possibile utilizzare SSE?**

R: Sì, SSE è supportata in archiviazione Standard e Premium lo spazio di archiviazione.

**D: se crea un nuovo account di archiviazione e Abilita SSE e quindi crea una nuova macchina virtuale utilizzando l'account di archiviazione, significa che la macchina virtuale è crittografato?**

R: Sì. Saranno crittografati i dischi creati che utilizzano il nuovo account di archiviazione, come vengono creati dopo l'attivazione SSE. Se la macchina virtuale è stata creata utilizzando mercato Azure, l'immagine di base del disco rigido virtuale rimarrà non crittografata; Tuttavia, qualsiasi scrive eseguite dopo la macchina virtuale è caricare un verranno crittografati.

**D: è possibile creare nuovi account di archiviazione con SSE abilitato tramite PowerShell Azure e Azure CLI?**

R: Sì.

**D: come molto più spazio di archiviazione di Azure costo se è abilitata la SSE?**

R: non esiste senza costi aggiuntivi.

**D: chi gestisce le chiavi di crittografia?**

R: i tasti vengono gestiti da Microsoft.

**D: è possibile usare il proprio chiavi di crittografia?**

R: stiamo lavorando a fornire funzionalità per i clienti visualizzare le proprie chiavi di crittografia.

**D: è possibile revocare l'accesso a chiavi crittografia?**

R: non in questa fase. i tasti completamente vengono gestiti da Microsoft.

**D: è SSE attivata per impostazione predefinita, quando crea un nuovo account di archiviazione?**

R: SSE non è abilitata per impostazione predefinita. è possibile utilizzare il portale di Azure per attivarlo. Anche a livello di programmazione, è possibile abilitare questa funzionalità mediante l'API REST di spazio di archiviazione delle risorse Provider.

**D: come è diversa da crittografia unità Azure?**

R: questa funzionalità è utilizzata per crittografare i dati in archiviazione Blob Azure. La crittografia disco Azure viene usata per crittografare il sistema operativo e dati dischi in macchine virtuali IaaS. Per ulteriori informazioni, vedere la [Guida sulla sicurezza di spazio di archiviazione](storage-security-guide.md).

**D: cosa fare se Abilita SSE, quindi passare e abilitare la crittografia del disco di Azure sui dischi?**

R: questo funziona perfettamente. I dati vengono crittografati da entrambi i metodi.

**Q: account lo spazio di archiviazione sia configurato per essere replicate geografico ridondanti. Se Abilita SSE, verrà copia personale ridondante inoltre crittografata?**

R: Sì, tutte le copie dell'account di archiviazione vengono crittografate e tutte le opzioni di ridondanza localmente ridondanti dello spazio di archiviazione (LRS), zona ridondanti dello spazio di archiviazione (ZRS), geografico ridondanti dello spazio di archiviazione (GRS) e lo spazio di archiviazione di accesso in lettura geografico ridondanti (RA GRS): sono supportate.

**D: non abilitare la crittografia nella pagina account personale lo spazio di archiviazione.**

R: si tratta di un account di archiviazione Manager delle risorse? Gli account di archiviazione classica non sono supportati. 

**D: SSE consentito solo in aree specifiche?**

R: il SSE è disponibile in tutti i paesi. 

**D: come è possibile contattare qualcuno se si verificano problemi o si desidera fornire commenti e suggerimenti?**

R:, contattare [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) per eventuali problemi correlati per la crittografia del servizio di archiviazione.

##<a name="next-steps"></a>Passaggi successivi

Spazio di archiviazione Azure fornisce una serie completa di funzionalità di sicurezza che insieme consentono agli sviluppatori di creare applicazioni protette. Per ulteriori informazioni, visitare la [Guida sulla sicurezza di spazio di archiviazione](storage-security-guide.md).
