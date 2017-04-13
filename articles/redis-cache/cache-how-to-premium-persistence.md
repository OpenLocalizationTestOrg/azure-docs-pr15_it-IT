<properties 
    pageTitle="Come configurare persistenza dei dati per una Redis Cache Premium Azure" 
    description="Informazioni su come configurare e gestire le istanze di Azure Redis Cache livello Premium persistenza dei dati" 
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
    ms.date="09/30/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-data-persistence-for-a-premium-azure-redis-cache"></a>Come configurare persistenza dei dati per una Redis Cache Premium Azure

Azure Cache Redis sono offerte cache diverso che forniscono flessibilità nella scelta delle dimensioni della cache e sulle funzionalità, tra cui il nuovo livello di Premium.

Il livello di premium Azure Redis Cache include caratteristiche quali il cluster, persistenza e supporto di rete virtuale. In questo articolo viene descritto come configurare persistenza in un'istanza di Azure Redis Cache premium.

Per informazioni su altre funzionalità di cache premium, vedere [Introduzione a livello di Azure Redis Cache Premium](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Che cos'è persistenza dei dati?
Persistenza redis consente di mantenere i dati archiviati in Redis. È anche possibile istantanee ed eseguire il backup dei dati, è possibile caricare in caso di un errore hardware. Si tratta di un enorme vantaggio rispetto a livello di base o Standard in cui tutti i dati vengono archiviati in memoria e possono essere presenti potenziale perdita di dati in caso di errore in cui sono nodi Cache verso il basso. 

Azure Cache Redis offre persistenza Redis utilizzando il [modello RDB](http://redis.io/topics/persistence), in cui vengono memorizzati in un account di archiviazione Azure. Quando si configura persistenza, Azure Redis Cache persiste uno snapshot della cache Redis in formato binario Redis su disco in base a una frequenza di backup configurabile. Se si verifica un errore irreversibile che disabilita i principali e replica della cache, la cache è ricostruzione utilizzando snapshot più recente.

È possibile configurare persistenza da e il **Nuovo Cache Redis** durante la creazione della cache e su e **l'Impostazioni** per le cache premium esistente.

## <a name="create-a-premium-cache"></a>Creare una cache premium

Per creare una cache e configurare persistenza, accesso al [portale di Azure](https://portal.azure.com) e fare clic su **Nuovo**->**dati + spazio di archiviazione**>**Redis Cache**.

![Creare una Cache Redis][redis-cache-new-cache-menu]

Per configurare persistenza, è prima di tutto selezionare una delle cache **Premium** in e lo **scegliere il livello dei prezzi** .

![Scegliere il livello prezzo][redis-cache-premium-pricing-tier]

Dopo aver selezionato un premio prezzi livello, fare clic su **Redis persistenza**.

![Redis persistenza][redis-cache-persistence]

I passaggi indicati nella sezione seguente viene descritto come configurare persistenza Redis la nuova cache premium. Dopo aver configurata Redis persistenza, fare clic su **Crea** per creare la nuova cache premium con una connessione permanente Redis.

## <a name="configure-redis-persistence"></a>Configurare persistenza Redis

Redis persistenza è configurata nel e **Redis persistenza dei dati** . Per nuove cache, questo blade è possibile accedere durante il processo di creazione della cache, come descritto nella sezione precedente. Per le cache esistente, e il **Redis persistenza dei dati** è possibile accedere da e **l'Impostazioni** per la cache.

![Redis impostazioni][redis-cache-settings]

Per consentire la memorizzazione Redis, fare clic su **attivato** per attivare il backup RDB (Redis database). Per disattivare persistenza Redis su una cache premium attivato in precedenza, fare clic su **disattivato**.

Per configurare l'intervallo di backup, selezionare una **Frequenza di Backup** dall'elenco a discesa. Le scelte includono **15 minuti**, **30 minuti**, **60 minuti**, **6 ore**, **12 ore**e **24 ore**. Questo intervallo avvia rovescia dopo il completamento dell'operazione di backup precedente e quando trascorso un nuovo backup viene avviato.

Fare clic su **Account di archiviazione** per selezionare l'account di archiviazione da utilizzare e scegliere la **chiave primaria** o **chiave secondaria** da utilizzare dall'elenco a discesa **Chiave di archiviazione** . È necessario scegliere un account di archiviazione nella stessa regione la cache e un account di **Archiviazione Premium** è consigliabile perché lo spazio di archiviazione premium ha velocità effettiva superiore. 

>[AZURE.IMPORTANT] Se la chiave di archiviazione per l'account persistenza rigenerazione, è necessario rechoose il tasto desiderato dall'elenco a discesa **Chiave di archiviazione** .

![Redis persistenza][redis-cache-persistence-selected]

Fare clic su **OK** per salvare la configurazione di persistenza.

Backup successivo (o copia di backup prima di nuove cache) viene avviato dopo l'intervallo di frequenza di backup.



## <a name="persistence-faq"></a>Persistenza domande frequenti

Di seguito sono riportate le risposte alle domande frequenti su persistenza Azure Redis Cache.

-   [È possibile abilitare la persistenza in una cache creata in precedenza?](#can-i-enable-persistence-on-a-previously-created-cache)
-   [È possibile modificare la frequenza di backup dopo aver creato la cache?](#can-i-change-the-backup-frequency-after-i-create-the-cache)
-   [Perché se dispone di una frequenza di backup di 60 minuti è presente più di 60 minuti tra i backup?](#why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
-   [Cosa succede ai vecchi backup quando viene effettuata una nuova copia di backup?](#what-happens-to-the-old-backups-when-a-new-backup-is-made)
-   [Cosa succede se ho architetture di una dimensione diversa e una copia di backup viene ripristinato produttrice prima l'operazione di ridimensionamento?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>È possibile abilitare la persistenza in una cache creata in precedenza?

Sì, è possibile configurare Redis persistenza al momento della creazione della cache e nella cache premium esistente.

### <a name="can-i-change-the-backup-frequency-after-i-create-the-cache"></a>È possibile modificare la frequenza di backup dopo aver creato la cache?

Sì, è possibile modificare la frequenza di backup su e il **Redis persistenza dei dati** . Per ulteriori informazioni, vedere [configurare Redis persistenza](#configure-redis-persistence).

### <a name="why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Perché se dispone di una frequenza di backup di 60 minuti è presente più di 60 minuti tra i backup?

L'intervallo di frequenza di backup per avviare il processo di backup precedente è stata completata. Se la frequenza di backup è 60 minuti e richiede un processo di backup 15 minuti per completare correttamente, fino a 75 minuti dopo l'ora di inizio del backup precedente non viene avviato il backup successivo.

### <a name="what-happens-to-the-old-backups-when-a-new-backup-is-made"></a>Cosa succede ai vecchi backup quando viene effettuata una nuova copia di backup?

Tutti i backup ad eccezione di quella più recente vengono automaticamente eliminati. L'eliminazione potrebbe non avvenire immediatamente ma backup precedenti non vengono mantenute tempo indefinito.

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Cosa succede se ho architetture di una dimensione diversa e una copia di backup viene ripristinato produttrice prima l'operazione di ridimensionamento?

-   Se le prestazioni di una dimensione maggiore, non esiste alcun effetto.
-   Se dispone di architetture per ridurre le dimensioni e si dispone di un personalizzati [database](cache-configure.md#databases) impostazione che è maggiore di [limitare i database](cache-configure.md#databases) per la nuova dimensione dati in tali database non è possibile ripristinare. Per ulteriori informazioni, vedere [è i database personalizzati impostazione interessata durante il ridimensionamento?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
-   Se dispone di architetture per ridurre le dimensioni e non vi è sufficiente di dimensioni minori per contenere tutti i dati dall'ultimo backup, i tasti di eliminazione durante il processo di ripristino, in genere utilizzando i criteri di eliminazione [allkeys lru](http://redis.io/topics/lru-cache) .

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come usare altre caratteristiche di cache premium.

-   [Introduzione a livello di Azure Redis Cache Premium](cache-premium-tier-intro.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
