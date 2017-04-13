<properties 
    pageTitle="Importare ed esportare i dati nella Cache Redis Azure | Microsoft Azure" 
    description="Informazioni su come importare ed esportare i dati da e verso archiviazione blob con le istanze di Azure Redis Cache premium" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="import-and-export-data-in-azure-redis-cache"></a>Importare ed esportare i dati nella Cache Redis Azure

Importazione/esportazione è un'operazione di gestione dati Azure Redis Cache, che consente di importare i dati nella Cache Redis Azure o esportare dati dalla Cache Redis Azure mediante l'importazione e l'esportazione di uno snapshot Redis Cache Database (RDB) da una cache premium in un blob di pagine in un Account di archiviazione Azure. Importazione/esportazione consente di eseguire la migrazione tra diverse istanze di Azure Redis Cache o popolare la cache con i dati prima di utilizzare.

In questo articolo viene fornita una Guida per l'importazione ed esportazione di dati con Azure Redis Cache e vengono fornite le risposte alle domande frequenti.

>[AZURE.IMPORTANT] Importazione/esportazione in anteprima ed è disponibile solo per [livello premium](cache-premium-tier-intro.md) cache.

## <a name="import"></a>Importazione

Importazione può essere utilizzato per visualizzare i file specifici RDB compatibili Redis da qualsiasi server Redis in esecuzione in qualsiasi cloud ambiente o, inclusi Redis eseguono Linux, Windows o un provider del cloud, ad esempio servizi Web di Amazon e gli altri utenti. Importazione di dati è un modo semplice per creare una cache con i dati prepopolati. Durante il processo di importazione, Azure Redis Cache carica i file RDB dallo spazio di archiviazione Azure in memoria e quindi inserisce le chiavi nella cache.

>[AZURE.NOTE] Prima di iniziare l'operazione di importazione, verificare che il file di Database Redis (RDB) o i file vengono caricati in BLOB di pagine in archiviazione Azure, nello stesso abbonamento come l'istanza di Azure Redis Cache e opzioni internazionali. Per ulteriori informazioni, vedere [Guida introduttiva a archiviazione Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md). Se è stata esportata i file RDB mediante la funzionalità di [Azure Redis Cache esportazione](#export) , il file RDB già archiviato in un blob di pagine ed è pronto per l'importazione.

1. Per importare uno o più BLOB cache esportato, [individuare la cache](cache-configure.md#configure-redis-cache-settings) nel portale di Azure e fare clic su **Importa dati** da e **l'Impostazioni** dell'istanza della cache.

    ![Importare dati][cache-import-data]

2. Fare clic su **Scegliere Blob(s)** e selezionare l'account di archiviazione che contiene i dati da importare.

    ![Scegliere account di archiviazione][cache-import-choose-storage-account]

3. Fare clic sul contenitore che contiene i dati da importare.

    ![Selezionare il contenitore][cache-import-choose-container]

4. Selezionare uno o più BLOB importare, facendo clic sull'area a sinistra del nome del blob e quindi fare clic su **Seleziona**.

    ![Scegliere BLOB][cache-import-choose-blobs]

5. Fare clic su **Importa** per iniziare il processo di importazione.

    >[AZURE.IMPORTANT] La cache non è accessibile dai client cache durante il processo di importazione e tutti i dati esistenti nella cache viene eliminati.

    ![Importazione][cache-import-blobs]

    È possibile monitorare l'avanzamento dell'operazione di importazione seguendo le notifiche dal portale di Azure o visualizzando gli eventi in [controllo accedere](cache-configure.md#support-amp-troubleshooting-settings).

    ![Stato dell'importazione][cache-import-data-import-complete] 


## <a name="export"></a>Esportazione

Esportazione consente di esportare i dati memorizzati nella Cache Redis Azure per Redis i file specifici RDB compatibili. È possibile utilizzare questa caratteristica per spostare i dati da un'istanza di Azure Redis Cache a un altro o a un altro server di Redis. Durante il processo di esportazione, viene creato un file temporaneo nella macchina virtuale che ospita l'istanza di server Azure Redis Cache e il file viene caricato per l'account di archiviazione designato. Al termine dell'operazione di esportazione con uno stato esito positivo o negativo, viene eliminato il file temporaneo.

1. Per esportare il contenuto della cache allo spazio di archiviazione, [individuare la cache](cache-configure.md#configure-redis-cache-settings) nel portale di Azure corrente e fare clic su **Esporta dati** da e **l'Impostazioni** dell'istanza della cache.

    ![Selezionare il contenitore di spazio di archiviazione][cache-export-data-choose-storage-container]

2. Fare clic su **Scegliere contenitore dello spazio di archiviazione** e selezionare l'account di archiviazione desiderato. Account di archiviazione deve essere nello stesso abbonamento e opzioni internazionali come la cache.

    >[AZURE.IMPORTANT] Importazione/esportazione con BLOB di pagine, che sono supportati da classica e gli account di archiviazione ARM, ma non sono supportati [dall'account di archiviazione Blob](../storage/storage-blob-storage-tiers.md#blob-storage-accounts) al momento.

    ![Account di archiviazione][cache-export-data-choose-account]

3. Scegliere il contenitore blob desiderato e fare clic su **Seleziona**. Per utilizzare di nuovo un contenitore, fare clic su **Aggiungi contenitore** per aggiungerlo prima di tutto e quindi selezionare nell'elenco.

    ![Selezionare il contenitore di spazio di archiviazione][cache-export-data-container]

4. Digitare un **prefisso nome Blob** e fare clic su **Esporta** per avviare il processo di esportazione. Il prefisso del nome blob è utilizzato come prefisso i nomi dei file generati dall'operazione di esportazione.

    ![Esportazione][cache-export-data]

    È possibile monitorare l'avanzamento dell'operazione di esportazione seguendo le notifiche dal portale di Azure o visualizzando gli eventi in [controllo accedere](cache-configure.md#support-amp-troubleshooting-settings).

    ![][cache-export-data-export-complete]

    Cache rimangono disponibili per l'utilizzo durante il processo di esportazione.


## <a name="importexport-faq"></a>Domande frequenti su Importa/Esporta

In questa sezione contiene domande frequenti sulla funzionalità Importa/Esporta.

-   [Quali prezzi livelli, è possibile utilizzare importazione/esportazione?](#what-pricing-tiers-can-use-importexport)
-   [È possibile importare dati da qualsiasi server Redis?](#can-i-import-data-from-any-redis-server)
-   [La cache sarà disponibile durante un'operazione di importazione/esportazione?](#will-my-cache-be-available-during-an-importexport-operation)
-   [È possibile utilizzare l'importazione/esportazione con cluster Redis?](#can-i-use-importexport-with-redis-cluster)
-   [Importazione/esportazione funzionamento con un database personalizzato impostazione?](#how-does-importexport-work-with-a-custom-databases-setting)
-   [Come è diversa da Redis persistenza Importa/Esporta?](#how-is-importexport-different-from-redis-persistence)
-   [È possibile automatizzare l'importazione/esportazione con PowerShell, CLI o altri client di gestione?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
-   [Si riceve un errore di timeout durante l'operazione personale Importa/Esporta. Che cosa significa?](#i-received-a-timeout-error-during-my-importexport-operation.-what-does-it-mean)
-   [Viene visualizzato un messaggio di errore durante l'esportazione di dati all'archivio Blob Azure. Cos'è successo?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage.-what-happened)


### <a name="what-pricing-tiers-can-use-importexport"></a>Quali prezzi livelli, è possibile utilizzare importazione/esportazione?

Importazione/esportazione è disponibile solo in premium prezzi livello.

### <a name="can-i-import-data-from-any-redis-server"></a>È possibile importare dati da qualsiasi server Redis?

Sì, oltre a importare i dati esportati da istanze di Azure Redis Cache, è possibile importare file RDB da qualsiasi server Redis in esecuzione in qualsiasi cloud o ambiente, ad esempio Linux, Windows, o nel cloud provider, ad esempio servizi Web di Amazon. A tale scopo, caricare il file RDB dal server Redis desiderato in un blob di pagine in un Account di archiviazione di Azure e quindi importarlo l'istanza di Azure Redis Cache premium. È consigliabile, ad esempio, da cui esportare i dati la cache di produzione e importare in una cache utilizzata come parte di un ambiente di gestione temporanea per il testing o la migrazione. 

### <a name="will-my-cache-be-available-during-an-importexport-operation"></a>La cache sarà disponibile durante un'operazione di importazione/esportazione?

-   **Esportare** - cache rimangono disponibili ed è possibile continuare a utilizzare la cache durante un'operazione di esportazione.
-   **Importare** - cache diventano disponibili all'avvio di un'operazione di importazione e diventano disponibili per l'utilizzo al termine dell'operazione di importazione.


### <a name="can-i-use-importexport-with-redis-cluster"></a>È possibile utilizzare l'importazione/esportazione con cluster Redis?

Sì, è possibile ruotare tra una cache raggruppata e una cache non cluster e. Poiché Redis cluster [supporta solo database 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), non è possibile importare i dati nei database diverso da 0. Quando si importano dati nella cache raggruppate, le chiavi sono ridistribuite tra shards del cluster. 

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Importazione/esportazione funzionamento con un database personalizzato impostazione?

Alcuni livelli prezzi sono diversi [limiti di database](cache-configure.md#databases), pertanto non esistono alcune considerazioni quando si importano se è stato configurato un valore personalizzato per il `databases` impostazione durante la creazione della cache.

-   Durante l'importazione in un livello prezzo con un basso `databases` limite rispetto al livello dal quale è stata esportata:
    -   Se si utilizza il numero predefinito di `databases` che non è 16 per tutti i livelli dei prezzi, dati andranno persi.
    -   Se si usa un numero personalizzato di `databases` che si trova entro i limiti per il livello a cui si sta importando, dati non andranno persi.
    -   Se i dati esportati contengono dati in un database che supera i limiti del nuovo livello, i dati provenienti da questi database superiori non vengono importati.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Come è diversa da Redis persistenza Importa/Esporta?

Azure persistenza Redis Cache consente di mantenere i dati archiviati in Redis allo spazio di archiviazione Azure. Quando si configura persistenza, Azure Redis Cache persiste uno snapshot della cache Redis in formato binario Redis su disco in base a una frequenza di backup configurabile. Se si verifica un errore irreversibile disabilita cache i principali e di replica, i dati della cache viene ripristinati automaticamente con snapshot più recente. Per ulteriori informazioni, vedere [come configurare persistenza dei dati per una Redis Cache Premium Azure](cache-how-to-premium-persistence.md).

Importazione / esportazione consente di importare dati in o esportare dalla Cache Redis Azure. Non configurare il backup e ripristino tramite Redis persistenza.


### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>È possibile automatizzare l'importazione/esportazione con PowerShell, CLI o altri client di gestione?

Sì, per PowerShell istruzioni visualizzate [per importare una cache Redis](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) e [per esportare una cache Redis](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache).



### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Si riceve un errore di timeout durante l'operazione personale Importa/Esporta. Che cosa significa?

Se si rimane in e il **dati di importazione** o **esportazione di dati** per più di 15 minuti prima di iniziare l'operazione, si riceverà un messaggio di errore analogo al seguente.

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Per risolvere questo problema, avviare l'importazione o esportazione prima di 15 minuti trascorso.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Viene visualizzato un messaggio di errore durante l'esportazione di dati all'archivio Blob Azure. Cos'è successo?

Importazione/esportazione funziona solo con file RDB archiviati come BLOB di pagine. Altri tipi di blob non sono supportati in questa fase, inclusi account di archiviazione blob con livelli attivo e interessanti.


## <a name="next-steps"></a>Passaggi successivi
Informazioni su come usare altre caratteristiche di cache premium.

-   [Introduzione a livello di Azure Redis Cache Premium](cache-premium-tier-intro.md)    

  
<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png








