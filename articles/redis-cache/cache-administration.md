<properties 
    pageTitle="Come amministrare Azure Redis Cache | Microsoft Azure"
    description="Informazioni su come eseguire attività amministrative, ad esempio gli aggiornamenti di pianificazione e riavviare il computer per Azure Redis Cache"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags 
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="09/27/2016"
    ms.author="sdanie" />

# <a name="how-to-administer-azure-redis-cache"></a>Come amministrare Azure Redis Cache

In questo argomento viene descritto come eseguire attività amministrative, ad esempio il riavvio e pianificazione degli aggiornamenti per le istanze di Azure Redis Cache.

>[AZURE.IMPORTANT] Le impostazioni e caratteristiche descritte in questo articolo sono disponibili solo per le cache di livello Premium.


## <a name="administration-settings"></a>Impostazioni di amministrazione

Le impostazioni della Cache Redis Azure **amministrazione** consentono di eseguire le seguenti attività amministrative per la cache premium. Per accedere alle impostazioni di amministrazione, fare clic su **Impostazioni** o **tutte le impostazioni** dal blade Redis Cache e scorrere fino alla sezione **amministrazione** in e **l'Impostazioni** .

![Amministrazione](./media/cache-administration/redis-cache-administration.png)

-   [Riavviare il computer](#reboot)
-   [Pianificare gli aggiornamenti](#schedule-updates)

## <a name="reboot"></a>Riavviare il computer

E il **riavvio** consente di riavviare uno o più nodi della cache. In questo modo è possibile verificare l'applicazione per la resilienza in caso di errore.

![Riavviare il computer](./media/cache-administration/redis-cache-reboot.png)

Se si dispone di una cache premium con il servizio cluster abilitato, è possibile selezionare quali shards della cache per riavviare il computer.

![Riavviare il computer](./media/cache-administration/redis-cache-reboot-cluster.png)

Per riavviare uno o più nodi della cache, selezionare i nodi desiderati e fare clic su **Riavvia**. Se si dispone di una cache premium con il servizio cluster abilitato, selezionare shard(s) riavviare il computer e quindi fare clic su **Riavvia**. Dopo alcuni minuti, riavviare il computer nodi selezionata e sono riportate in linea pochi minuti dopo.

L'impatto sulle applicazioni client variabile a seconda i nodi che si riavvia.

-   **Schema** - al riavvio il nodo principale, Azure Redis Cache ha esito negativo al livello di nodo replica e promuove allo schema. In questo caso di failover, potrebbe essere un breve intervallo in cui le connessioni potrebbero non riuscire alla cache.
-   **Secondario** - al riavvio il nodo secondario, in genere non ha alcun impatto ai client di cache.
-   **Entrambi master e secondaria** - quando sono riavviare entrambi i nodi cache, tutti i dati vengono perse nella cache e connessioni a fail cache fino a quando non il nodo principale in modalità online. Se è stata configurata [persistenza dei dati](cache-how-to-premium-persistence.md), quando la cache ritorna in linea viene ripristinato il backup più recente. Si noti che qualsiasi scrive cache che si sono verificati dopo il backup più recente vengono perse.
-   **Nodi di una cache premium con il servizio cluster abilitato** - quando si riavvia i nodi di una cache premium con il servizio cluster abilitato, il comportamento è diverso da quello di quando si riavvia nodi di una cache non cluster.


>[AZURE.IMPORTANT] Riavviare il computer è disponibile solo per le cache di livello Premium.

## <a name="reboot-faq"></a>Riavviare domande frequenti

-   [Il nodo è consigliabile riavviare per testare l'applicazione?](#which-node-should-i-reboot-to-test-my-application)
-   [È possibile riavviare la cache per cancellare le connessioni client?](#can-i-reboot-the-cache-to-clear-client-connections)
-   [Perderanno dati la cache caso di riavviare il computer?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
-   [È possibile riavviare la cache tramite PowerShell, CLI o altri strumenti di gestione?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
-   [Quali prezzi livelli, è possibile utilizzare la funzionalità di riavviare il computer?](#what-pricing-tiers-can-use-the-reboot-functionality)


### <a name="which-node-should-i-reboot-to-test-my-application"></a>Il nodo è consigliabile riavviare per testare l'applicazione?

Per testare la flessibilità dell'applicazione in caso di errore del nodo principale della cache, riavviare il nodo **principale** . Per testare la flessibilità dell'applicazione in caso di errore del nodo secondario, riavviare il nodo **secondario** . Per testare la flessibilità dell'applicazione in caso di errore totale della cache, riavviare **entrambi** i nodi.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>È possibile riavviare la cache per cancellare le connessioni client?

Sì, se si riavvia la cache di che tutte le connessioni client siano deselezionate. Questa operazione può essere utile anche nel caso in cui tutte le connessioni client vengono utilizzate verso l'alto, ad esempio a causa di un errore logico o un bug nelle applicazioni client. Una volta questi limiti sono raggiunto, non più client vengono accettate connessioni ogni livello prezzo ha diversi [limiti delle connessioni client](cache-configure.md#default-redis-server-configuration) di varie dimensioni. Riavviare la cache consente di cancellare tutte le connessioni client.

>[AZURE.IMPORTANT] Se vengono utilizzate le connessioni client a causa di un errore logico o bug nel codice del client, tenere presente che StackExchange.Redis verrà riconnettersi automaticamente dopo il nodo Redis è in modalità online. Se non viene risolto il problema sottostante, le connessioni client continuerà a utilizzare.

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Perderanno dati la cache caso di riavviare il computer?

Se il riavvio nodi sia **principale** e **secondaria** tutti i dati nella cache (o in tale condiviso se si utilizza una cache premium con il servizio cluster abilitato) viene perso. Se è stata configurata [persistenza dei dati](cache-how-to-premium-persistence.md), il backup più recente verrà ripristinato quando la cache ritorna in linea. Si noti che qualsiasi scrive cache che si sono verificati dopo che è stato eseguito il backup vengono perse.

Se si riavvia solo uno dei nodi, dati non sono in genere persi, ma è comunque possibile. Ad esempio, se il riavvio di nodo principale e la scrittura di cache è in corso, i dati da scrittura cache sarà perso. Un altro scenario per la perdita di dati è il riavvio di un nodo e l'altro nodo viene eseguita per spostarsi in basso a causa di un errore nello stesso momento. Per ulteriori informazioni sulle possibili cause di perdita dei dati, vedere [modifiche apportate ai dati personali in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md).

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>È possibile riavviare la cache tramite PowerShell, CLI o altri strumenti di gestione?

Sì, per PowerShell le istruzioni visualizzate [per riavviare una cache Redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Quali prezzi livelli, è possibile utilizzare la funzionalità di riavviare il computer?

Riavviare il computer è disponibile solo in premium prezzi livello.

## <a name="schedule-updates"></a>Pianificare gli aggiornamenti

E il **pianificare gli aggiornamenti** consente di designare una finestra di manutenzione per la cache. Se viene specificata la finestra di manutenzione, qualsiasi Redis server gli aggiornamenti durante questo periodo. Si noti che la finestra di manutenzione si applica solo alle Redis aggiornamenti server e non in tutti Azure aggiorna o aggiorna al sistema operativo di macchine virtuali che ospitano la cache.

![Pianificare gli aggiornamenti](./media/cache-administration/redis-schedule-updates.png)

Per specificare una finestra di manutenzione, selezionare i giorni desiderati e specificare l'ora di inizio finestra manutenzione per ogni giorno e fare clic su **OK**. Si noti che l'ora della finestra della manutenzione in formato UTC. 

>[AZURE.NOTE] Nella finestra di manutenzione predefinita aggiornamenti è 5 ore. Questo valore non è possibile configurare dal portale di Azure, ma è possibile configurare PowerShell usando il `MaintenanceWindow` parametro del cmdlet [New-AzureRmRedisCacheScheduleEntry](https://msdn.microsoft.com/library/azure/mt763833.aspx) . Per ulteriori informazioni, vedere [è gestito aggiornamenti pianificati tramite PowerShell, CLI o altri strumenti di gestione?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

## <a name="schedule-updates-faq"></a>Pianificare gli aggiornamenti domande frequenti

-   [Quando gli aggiornamenti verificarsi se non si utilizza la funzionalità di aggiornamenti di programmazione?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
-   [Quali tipi di aggiornamenti effettuati durante la finestra di manutenzione pianificata?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
-   [È possibile gestiti gli aggiornamenti pianificati tramite PowerShell, CLI o altri strumenti di gestione?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
-   [Quali prezzi livelli, è possibile utilizzare la funzionalità di aggiornamenti di programmazione?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Quando gli aggiornamenti verificarsi se non si utilizza la funzionalità di aggiornamenti di programmazione?

Se non si specifica una finestra di manutenzione, è possibile eseguire aggiornamenti in qualsiasi momento.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Quali tipi di aggiornamenti effettuati durante la finestra di manutenzione pianificata?

Solo Redis server gli aggiornamenti effettuati durante la finestra di manutenzione pianificata. La finestra di manutenzione non è valida per gli aggiornamenti di Azure o gli aggiornamenti del sistema operativo macchine Virtuali.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>È possibile gestiti gli aggiornamenti pianificati tramite PowerShell, CLI o altri strumenti di gestione?

Sì, è possibile gestire gli aggiornamenti pianificati utilizzando il seguente cmdlet di PowerShell.

-   [Get-AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763835.aspx)
-   [Nuovo AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763834.aspx)
-   [Nuovo AzureRmRedisCacheScheduleEntry](https://msdn.microsoft.com/library/azure/mt763833.aspx)
-   [Rimuovi AzureRmRedisCachePatchSchedule](https://msdn.microsoft.com/library/azure/mt763837.aspx)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Quali prezzi livelli, è possibile utilizzare la funzionalità di aggiornamenti di programmazione?

Pianificare gli aggiornamenti è disponibile solo in premium prezzi livello.

## <a name="next-steps"></a>Passaggi successivi

-   Esplorare altre caratteristiche [del livello premium Azure Redis Cache](cache-premium-tier-intro.md) .





