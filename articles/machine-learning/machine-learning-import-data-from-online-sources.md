<properties
    pageTitle="Importare dati in computer risorse Studio da origini dati online | Microsoft Azure"
    description="Informazioni su come importare i dati di formazione su Azure Machine Learning Studio da diverse origini online."
    keywords="importare dati, il formato dati, i tipi di dati, origini dati, dati di formazione"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="bradsev;garye" />


# <a name="import-data-into-azure-machine-learning-studio-from-various-online-data-sources-with-the-import-data-module"></a>Importare dati in Azure Machine Learning Studio da diverse origini dati in linea con il modulo Importa dati

In questo articolo viene descritto il supporto per l'importazione di dati online da diverse origini e le informazioni necessarie per spostare dati da queste origini in una prova di apprendimento Azure.

> [AZURE.NOTE] In questo articolo vengono fornite informazioni generali sui [Importa dati] [ import-data] modulo. Per ulteriori informazioni sui tipi di dati è possibile accedere, formati, parametri e le risposte alle domande comuni, vedere l'argomento di riferimento modulo per [Importare dati] [ import-data] modulo.

<!-- -->

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

## <a name="introduction"></a>Introduzione

È possibile accedere ai dati all'interno di Studio di apprendimento Azure computer da una delle diverse origini dati online mentre è in esecuzione la prova utilizzando i [Dati di importazione] [ import-data] modulo:

- Un URL Web tramite HTTP
- Hadoop con HiveQL
- Archiviazione blob Azure
- Tabelle di Azure
- Azure database SQL o SQL Server in macchine Virtuali di Azure
- Database di SQL Server locale
- Un feed OData attualmente provider di dati
 
Il flusso di lavoro per esperimenti alle Azure Machine Learning Studio è costituito da componenti di trascinamento della selezione nell'area di lavoro. Per accedere a origini dati online, aggiungere i [Dati di importazione] [ import-data] modulo per la prova, selezionare l' **origine dati**e quindi specificare i parametri necessari per accedere ai dati. Nella tabella seguente vengono classificate le origini dati online che sono supportate. In questa tabella sono riepilogate anche i formati di file supportati e parametri utilizzati per accedere ai dati.

Si noti che questi dati formazione viene eseguito mentre è in esecuzione la prova, è disponibile solo in tale prova. Dati memorizzati in un modulo di set di dati di confronto, sono disponibili per qualsiasi prova nell'area di lavoro.

> [AZURE.IMPORTANT] Attualmente, i [Dati di importazione] [ import-data] ed [Esporta dati] [ export-data] moduli possano leggere e scrivere dati solo dallo spazio di archiviazione Azure creata utilizzando il modello di distribuzione classica. In altre parole, il nuovo tipo di account di archiviazione Blob Azure che offre un livello di accesso lo spazio di archiviazione attivo o interessanti dello spazio di archiviazione accesso non è ancora. 

> In genere, tutti gli account di archiviazione Azure che è possibile creare prima di questa opzione di servizio è stata introdotta potrebbero non essere ottimali. Se è necessario creare un nuovo account, selezionare **classico** per il modello di distribuzione o utilizzare Gestione risorse e per **tipo di Account**, selezionare **Generale** , invece di **archiviazione Blob**. 

> Per ulteriori informazioni, vedere [archiviazione Blob Azure: attivo e interessanti livelli di spazio di archiviazione](../storage/storage-blob-storage-tiers.md).



## <a name="supported-online-data-sources"></a>Origini dati online supportate
Modulo di Azure apprendimento **Importa dati** supporta le origini dati seguenti:

Origine dati | Descrizione | Parametri |
---|---|---|
URL Web tramite HTTP |Legge i dati con valori delimitati da virgole (CSV), valori delimitati da tabulazioni (TSV), formato di file attributo relazione (ARFF) e formati di supporto vettore macchine (SVM chiaro), da un URL web che utilizza HTTP | <b>URL</b>: Specifica il nome completo del file, inclusi l'URL del sito e il nome di file con estensione. <br/><br/><b>Formato dati</b>: consente di specificare uno dei dati supportati formati: SVM light, ARFF, TSV o CSV. Se i dati hanno una riga di intestazione, viene utilizzato per assegnare i nomi di colonna.|
Hadoop/HDFS|Legge i dati dallo spazio di archiviazione distribuito in Hadoop. Specificare i dati desiderati utilizzando HiveQL, un linguaggio di query SQL simili. HiveQL può anche essere utilizzato per aggregare i dati ed eseguire il filtro prima di aggiungere i dati al computer risorse Studio di dati.|<b>Hive una query di database</b>: specifica la query Hive usata per generare i dati.<br/><br/><b>URI del server HCatalog</b> : specificato il nome del cluster utilizzando il formato * &lt;il nome del cluster&gt;. azurehdinsight.net.*<br/><br/><b>Nome dell'account utente Hadoop</b>: consente di specificare il nome dell'account utente Hadoop utilizzato per eseguire il provisioning del cluster.<br/><br/><b>Password dell'account utente Hadoop</b> : consente di specificare le credenziali utilizzate durante il provisioning del cluster. Per ulteriori informazioni, vedere [creare Hadoop cluster in HDInsight](../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Posizione dei dati di output</b>: Specifica se i dati vengono memorizzati in un Hadoop distribuita file system (HDFS) o in Azure. <br/><ul>Se si archiviano i dati di output in HDFS, specificare il server HDFS URI. (Assicurarsi di usare il nome del cluster HDInsight senza il prefisso HTTPS://). <br/><br/>Se si archiviano i dati di output in Azure, è necessario specificare il nome dell'account di archiviazione Azure, tasto lo spazio di archiviazione e il nome di contenitore di spazio di archiviazione.</ul>|
Database SQL |Legge i dati memorizzati in un database SQL Azure o in un database di SQL Server in esecuzione in un computer virtuale Azure. | <b>Nome del server di database</b>: consente di specificare il nome del server in cui viene eseguito il database.<br/><ul>In caso di Database SQL Azure immettere il nome del server che viene generato. In genere ha il formato * &lt;generated_identifier&gt;. database.windows.net.* <br/><br/>In caso di SQL server ospitato su un computer virtuale Azure immettere *tcp:&lt;nome DNS macchina virtuale&gt;, 1433*</ul><br/><b>Nome del database </b>: consente di specificare il nome del database nel server. <br/><br/><b>Nome dell'account utente server</b>: specifica un nome utente per un account dotato di autorizzazioni di accesso per il database. <br/><br/><b>Password dell'account utente server</b>: specifica la password per l'account utente.<br/><br/><b>Accettare un certificato server</b>: usare questa opzione (meno sicura) se si desidera esaminare il certificato del sito prima di leggere i dati.<br/><br/><b>Query di database</b>: immettere un'istruzione SQL che descriva i dati che si vuole leggere.|
Database SQL locale |Legge i dati memorizzati in un database SQL in locale. | <b>Gateway di dati</b>: consente di specificare il nome del Gateway di gestione dati installato nel computer in cui è possibile accedere al database SQL Server. Per informazioni sulla configurazione del gateway, vedere [eseguire avanzate analitica con Azure apprendimento utilizzando i dati da un server SQL locale](machine-learning-use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nome del server di database</b>: consente di specificare il nome del server in cui viene eseguito il database.<br/><br/><b>Nome del database </b>: consente di specificare il nome del database nel server. <br/><br/><b>Nome dell'account utente server</b>: specifica un nome utente per un account dotato di autorizzazioni di accesso per il database. <br/><br/><b>Nome utente e password</b>: fare clic su <b>valori di invio</b> per immettere le credenziali di database. È possibile utilizzare l'autenticazione di Windows o SQL Server a seconda della configurazione SQL Server locale.<br/><br/><b>Query di database</b>: immettere un'istruzione SQL che descriva i dati che si vuole leggere.|
Tabelle di Azure|Legge i dati dal servizio di tabella in archiviazione Azure.<br/><br/>Se si legge raramente grandi quantità di dati, utilizzare il servizio di tabella Azure. Fornisce un flessibile non relazionali (NoSQL), soluzione di archiviazione market scalable, economico e disponibilità.| Le opzioni nella finestra **Importa dati** variano a seconda che si accede a informazioni pubblico o un account di archiviazione privato che richiede le credenziali di accesso. Ciò dipende dal <b>Tipo di autenticazione</b> che può avere valore di "PublicOrSAS" o "Conto", ognuno dei quali dispone di un insieme di parametri. <br/><br/><b>Pubblico o condiviso accesso firma (SA) URI</b>: I parametri sono:<br/><br/><ul><b>Tabella URI</b>: Specifica il pubblico o l'URL SA per la tabella.<br/><br/><b>Specifica le righe per cercare i nomi delle proprietà</b>: I valori sono <i>TopN</i> per analizzare il numero specificato di righe o <i>ScanAll</i> per ottenere tutte le righe della tabella. <br/><br/>Se i dati sono omogeneo e prevedibili, è consigliabile selezionare *TopN* e immettere un numero per N. Per tabelle di grandi dimensioni, questo può comportare tempi di lettura più veloce.<br/><br/>Se i dati sono strutturati con un insieme di proprietà variano in base alle antero-posteriore e la posizione della tabella, scegliere l'opzione *ScanAll* per eseguire la scansione tutte le righe. In questo modo l'integrità dei metadati conversione proprietà risultante.<br/><br/></ul><b>Account di archiviazione privato</b>: I parametri sono: <br/><br/><ul><b>Nome dell'account</b>: consente di specificare il nome dell'account che contiene la tabella da leggere.<br/><br/><b>Chiave account</b>: specifica la chiave di spazio di archiviazione associata all'account.<br/><br/><b>Nome tabella</b> : consente di specificare il nome della tabella che contiene i dati da leggere.<br/><br/><b>Righe per cercare i nomi delle proprietà</b>: I valori sono <i>TopN</i> per analizzare il numero specificato di righe o <i>ScanAll</i> per ottenere tutte le righe della tabella.<br/><br/>Se i dati sono omogeneo e prevedibili, è consigliabile selezionare *TopN* e immettere un numero per N. Per tabelle di grandi dimensioni, questo può comportare tempi di lettura più veloce.<br/><br/>Se i dati sono strutturati con un insieme di proprietà variano in base alle antero-posteriore e la posizione della tabella, scegliere l'opzione *ScanAll* per eseguire la scansione tutte le righe. In questo modo l'integrità dei metadati conversione proprietà risultante.<br/><br/>|
Archiviazione Blob Azure | Legge i dati archiviati nel servizio Blob Azure lo spazio di archiviazione, ad esempio immagini, testo non strutturato o dati binari.<br/><br/>È possibile utilizzare il servizio Blob per esporre pubblicamente dati o per privatamente archiviare dati dell'applicazione. È possibile accedere ai dati ovunque tramite connessioni HTTP o HTTPS. | Le opzioni nel modulo **Importa dati** variano a seconda che si accede a informazioni pubblico o un account di archiviazione privato che richiede le credenziali di accesso. Questo è il <b>Tipo di autenticazione</b> che può contenere un valore di "PublicOrSAS" o "Conto".<br/><br/><b>Pubblico o condiviso accesso firma (SA) URI</b>: I parametri sono:<br/><br/><ul><b>URI</b>: consente di specificare il pubblico o l'URL SA per blob lo spazio di archiviazione.<br/><br/><b>Formato di file</b>: Specifica il formato dei dati nel servizio Blob. I formati supportati sono CSV, TSV e ARFF.<br/><br/></ul><b>Account di archiviazione privato</b>: I parametri sono: <br/><br/><ul><b>Nome dell'account</b>: consente di specificare il nome dell'account che contiene il blob che si vuole leggere.<br/><br/><b>Chiave account</b>: specifica la chiave di spazio di archiviazione associata all'account.<br/><br/><b>Percorso contenitore, directory o blob</b> : Specifica il nome del blob che contiene i dati da leggere.<br/><br/><b>Formato di file di archivio BLOB</b>: Specifica il formato dei dati nel servizio blob. I formati di dati supportati sono CSV, TSV, ARFF, CSV con una codifica specificata ed Excel. <br/><br/><ul>Se il formato CSV o TSV, assicurarsi di indicare se il file contiene una riga di intestazione.<br/><br/>È possibile utilizzare l'opzione di Excel per leggere i dati dalle cartelle di lavoro di Excel. Nella casella <i>formato dati di Excel</i> , indicare che i dati siano in un foglio di lavoro Excel o in una tabella di Excel. Nell'opzione di <i>foglio di Excel o tabella incorporata </i>, specificare il nome del foglio o nella tabella che si desidera leggere.</ul><br/>|
Provider di Feed di dati | Legge i dati da un provider di feed supportato. Attualmente è supportato solo il formato di dati OData (Open Protocol). | <b>Tipo di contenuto dati</b>: consente di specificare il formato di OData.<br/><br/><b>URL di origine</b>: specifica l'URL completo per il feed di dati. <br/>Ad esempio, l'URL seguente legge il database di esempio Northwind: http://services.odata.org/northwind/northwind.svc/|


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/
