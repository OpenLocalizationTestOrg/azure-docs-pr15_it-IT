<properties 
    pageTitle="Spostare dati tra database fuori architetture cloud | Microsoft Azure" 
    description="In questo articolo viene spiegato come modificare shards e spostare i dati tramite un servizio indipendente usando flessibile API di database." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="moving-data-between-scaled-out-cloud-databases"></a>Spostare dati tra database cloud scalabilità orizzontale

Se ci si trova un software per gli sviluppatori di servizio e improvvisamente l'app subisce enorme richiesta, è necessario soddisfare la crescita. Quindi si aggiungono più database (shards). Come si ridistribuire i dati per i nuovi database senza interrompere l'integrità dei dati? Usare lo **strumento di unione di divisione** per spostare i dati da database limitati ai nuovi database.  

Lo strumento di unione di divisione viene eseguito come un servizio web Azure. Un amministratore o uno sviluppatore utilizza lo strumento per spostare shardlets (dati da un condiviso) tra database diversi (shards). Lo strumento utilizza la gestione della mappa condiviso per gestire il database di metadati del servizio e assicurarsi che i mapping coerenti.

![Panoramica][1]

## <a name="download"></a>Download
[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)


## <a name="documentation"></a>Documentazione
1. [Esercitazione dello strumento database flessibile unione divisa](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
* [Configurazione della protezione di stampa unione di divisione](sql-database-elastic-scale-split-merge-security-configuration.md)
* [Considerazioni sulla protezione di stampa unione di divisione](sql-database-elastic-scale-split-merge-security-configuration.md)
* [Gestione dei mapping condiviso](sql-database-elastic-scale-shard-map-management.md)
* [Eseguire la migrazione di database esistenti a scalabilità orizzontale](sql-database-elastic-convert-to-use-elastic-tools.md)
* [Strumenti database flessibile](sql-database-elastic-scale-introduction.md)
* [Glossario di strumenti di Database flessibile](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Perché usare lo strumento di unione divisa?

**Flessibilità**

Le applicazioni devono allontanare flessibile superano i limiti di un singolo database DB di SQL Azure. Usare lo strumento per spostare i dati in base alle esigenze per i nuovi database mantenendo integrità.

**Dividi aumenti** 

È necessario aumentare la capacità complessiva per gestire crescita esponenziale. A tale scopo, creare capacità aggiuntiva per sharding i dati e per la distribuzione tra database in modo incrementale ulteriori fino a quando non sono soddisfatte esigenze di capacità. Si tratta di un esempio di funzione "dividere". 

**Stampa unione per ridurre**

Capacità deve Riduci a causa del carattere stagionale dell'impresa. Lo strumento consente di rimpicciolire meno unità scala quando business rallentamento. La caratteristica 'unione' nel servizio di stampa unione divisa scalabilità flessibile copre questo requisito. 

**Gestire le aree sensibili spostando shardlets**

Con più tenant per ogni database, l'allocazione di shardlets a shards può causare bottiglia capacità sul alcune shards. È necessario riassegnazione shardlets o shardlets occupato per shards nuovo o meno utilizzata. 

## <a name="concepts--key-features"></a>Concetti e le funzionalità principali

**Servizi ospitati cliente**

L'unione di divisione viene recapitata come un servizio ospitato cliente. Distribuire e ospitare il servizio nell'abbonamento a Microsoft Azure. Il pacchetto scaricato dal NuGet contiene un modello di configurazione per completare le informazioni per la distribuzione specifica. Vedere l' [esercitazione di stampa unione di divisione](sql-database-elastic-scale-configure-deploy-split-and-merge.md) per informazioni dettagliate. Poiché il servizio viene eseguito nell'abbonamento Azure, è possibile controllare e configurare molti aspetti di sicurezza del servizio. Il modello predefinito include opzioni per configurare SSL, l'autenticazione basata su certificato client, la crittografia per le credenziali archiviate, DoS protegge e restrizioni. Si ulteriori informazioni sugli aspetti della protezione seguente documento [configurazione della protezione di stampa unione di divisione](sql-database-elastic-scale-split-merge-security-configuration.md).

Il valore predefinito distribuito viene eseguito il servizio con un lavoro e un ruolo di web. Ogni utilizza le dimensioni di macchine Virtuali A1 in servizi Cloud Windows Azure. Mentre è possibile modificare queste impostazioni per la distribuzione il pacchetto, è possibile modificare al termine della distribuzione nel servizio cloud in esecuzione (tramite il portale di Azure). Si noti che il ruolo di lavoro non deve essere configurato per più di una singola istanza per motivi tecnici. 

**Integrazione di mappa condiviso**

Il servizio di stampa unione divisa interagisce con la mappa condiviso dell'applicazione. Quando si usa il servizio di stampa unione di divisione per dividere o unire intervalli o shardlets tra shards, il servizio mantiene automaticamente la mappa condiviso aggiornati. A tale scopo, il servizio si connette al database condiviso mappa manager dell'applicazione e mantiene intervalli e i mapping come stato di avanzamento delle richieste di Unisci/Dividi/sposta. In questo modo che la mappa condiviso viene sempre una vista aggiornata quando le operazioni di unione divisa sono in corso. Dividere, Unisci e shardlet operazioni di spostamento sono implementate spostando un batch di shardlets condiviso origine condiviso destinazione. Durante la shardlet operazione di spostamento shardlets soggetto a batch corrente contrassegnato come non in linea nella mappa condiviso e non sono disponibili per le connessioni di routing dipendenti dai dati mediante **OpenConnectionForKey** API. 

**Connessioni shardlet coerente**

Quando si avvia lo spostamento dei dati per un nuovo batch shardlets, qualsiasi mappa condiviso fornita dipendenti dai dati routing le connessioni a condiviso l'archiviazione di shardlet sono abbattuti e quelle successive dalla mappa condiviso API per questi shardlets vengono bloccati durante lo spostamento dei dati per evitare le incoerenze. Connessioni a altri shardlets nella stessa condiviso anche ottenere eliminate, ma avrà esito positivo nuovo immediatamente in un tentativo successivo. Dopo aver spostato il batch, il shardlets vengono contrassegnati online ancora per condiviso destinazione e i dati di origine sono stata rimossa dal condiviso origine. Il servizio passa con la procedura per ogni batch fino a quando non sono state spostate shardlets tutti. Questa operazione comporta diverse operazioni di interruzione di connessione nel corso dell'operazione di divisione/unione/spostamento completo.  

**La gestione della disponibilità shardlet**

Limitare la connessione interruzione al batch corrente di shardlets come indicato in precedenza limita l'ambito della mancata disponibilità di un batch di shardlets alla volta. Questo è preferibile un approccio nel punto in cui condiviso completo rimarrà non in linea per tutto il relativo shardlets nel corso di un'operazione di divisione o unione. Le dimensioni del batch, definito come il numero di shardlets distinti per passare alla volta, sono un parametro di configurazione. Può essere definito per ogni operazione di divisione e unione in base alle esigenze di disponibilità e le prestazioni dell'applicazione. Si noti che l'intervallo bloccati nella mappa condiviso può essere supera rispetto alla dimensione batch specificata. Ciò avviene perché il servizio rileva le dimensioni dell'intervallo in modo che il numero effettivo di valori di chiave sharding nei dati corrisponde circa la dimensione del batch. Questo è importante ricordare in particolare per le chiavi sharding scarsamente compilato. 

**Archiviazione dei metadati**

Il servizio di stampa unione divisa utilizza un database per mantenere il suo stato e per mantenere i log durante l'elaborazione della richiesta. L'utente viene creato il database in abbonamento e prevede la stringa di connessione nel file di configurazione per la distribuzione di servizio. Gli amministratori di organizzazione dell'utente possono inoltre connettersi al database per verificare lo stato di avanzamento di richiesta e per analizzare informazioni dettagliate sui potenziali errori.

**Informazioni sulla presenza sharding**

Il servizio di stampa unione di divisione viene fatta distinzione tra tabelle (1) sharded, (2) riferimento tabelle e tabelle (3) normale. La semantica di un'operazione di divisione/unione/spostamento dipende dal tipo della tabella utilizzata e è i seguenti: 

* **Tabelle sharded**: divisione, unione e le operazioni di spostamento spostare shardlets dall'origine condiviso di destinazione. Dopo il completamento della richiesta globale, tali shardlets non sono più presente nel sito di origine. Si noti che le tabelle di destinazione devono essere disponibili in condiviso destinazione e non può contenere dati nell'intervallo di destinazione prima dell'elaborazione dell'operazione. 

* **Tabelle di riferimento**: per unire le tabelle di riferimento, la divisione e spostare le operazioni di copiare i dati dal sito di origine condiviso destinazione. Notare che non vengono apportate modifiche a condiviso destinazione per una determinata tabella se tutte le righe sono già presente in questa tabella nella destinazione. La tabella deve essere vuota per le operazioni di copia tabella di riferimento per l'elaborazione.

* **Altre tabelle**: altre tabelle possono essere presenti nel sito di origine o di destinazione di un'operazione di divisione e unione. Il servizio di stampa unione di divisione ignora le tabelle per le operazioni di copia o spostamento dei dati. Notare che possono interferire con queste operazioni in caso di vincoli.

Sono disponibili le informazioni nel riferimento e tabelle sharded da **SchemaInfo** API sulla mappa condiviso. Nell'esempio seguente viene illustrato l'utilizzo di queste API in un oggetto smm di condiviso specificato mappa manager: 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

Le tabelle 'l'area geografica' 'paese' sono definiti come tabelle di riferimento e verrà copiati con le operazioni di Unisci/Dividi/spostamento. "cliente" e 'orders', a sua volta sono definiti come tabelle sharded. C_CUSTKEY e O_CUSTKEY funga da chiave sharding. 

**Integrità referenziale**

Il servizio di stampa unione di divisione consente di analizzare le dipendenze tra le tabelle e Usa relazioni di chiave primaria di chiave esterna per le operazioni per spostare le tabelle di riferimento e shardlets sul passaggio. In generale, le tabelle di riferimento vengono copiate prima di tutto in ordine di dipendenza, quindi shardlets sono stati copiati in ordine delle relative dipendenze all'interno di ogni batch. Questo è necessario in modo che i vincoli di chiave primaria di chiave esterna condiviso destinazione vengono rispettati in arrivano di nuovi dati. 

**La coerenza mappa condiviso e all'eventuale completamento**

In presenza di errori, il servizio di stampa unione divisa riprende operazioni dopo qualsiasi interruzione e ha lo scopo per completare le richieste di stato di avanzamento. Tuttavia, si possono verificare irreversibile situazioni, ad esempio, quando viene perso o danneggiato di là ripristino condiviso destinazione. In questi casi, alcuni shardlets che sono state dovrebbe essere spostato può continuare a si trovano sul condiviso origine. Il servizio garantisce che i mapping shardlet vengono aggiornati solo dopo i dati necessari sono stati copiati correttamente alla destinazione. Shardlets vengono eliminati solo nel sito di origine dopo che tutti i dati sono stati copiati alla destinazione e aggiornamento completato i mapping corrispondenti. L'operazione di eliminazione viene eseguita in background durante l'intervallo è già in linea condiviso destinazione. Il servizio di stampa unione di divisione garantisce sempre la correttezza dei mapping archiviati nella mappa condiviso.


## <a name="the-split-merge-user-interface"></a>L'interfaccia utente di stampa unione di divisione

Il pacchetto del servizio di stampa unione divisa include un ruolo di lavoro e un ruolo web. Il ruolo di web viene utilizzato per inviare le richieste di stampa unione di divisione in modo interattivo. Componenti principali dell'interfaccia utente sono i seguenti:

-    Tipo di operazione: Il tipo di operazione è un pulsante di opzione che consente di controllare il tipo di operazione eseguita dal servizio per la richiesta. È possibile scegliere tra la divisione, unire e scenari di spostamento. È anche possibile annullare un'operazione inviata in precedenza. È possibile utilizzare divisa, unire e spostare le richieste di intervallo condiviso mappe. Condiviso elenco cartine solo le operazioni di spostamento di supporto.

-    Eseguire il mapping condiviso: La sezione successiva di parametri della richiesta vengono fornite informazioni sul mappa condiviso e il database che ospita la mappa condiviso. In particolare, è necessario specificare il nome del server di Database SQL Azure e database che ospita la shardmap credenziali per connettersi al database condiviso per il mapping e infine il nome della mappa condiviso. Attualmente, l'operazione accetta solo un singolo set di credenziali. Queste credenziali devono avere autorizzazioni sufficienti per apportare modifiche alla mappa condiviso anche per quanto riguarda i dati degli utenti di shards.

-    Intervallo di origine (divisione e unire): un'operazione di divisione e unione elabora un intervallo usando la chiave minima e massimo. Per specificare un'operazione con un valore di chiave elevato illimitato, selezionare la casella di controllo "High key è max" e lasciare vuoto il campo chiave elevato. Non è necessario che i valori chiave dell'intervallo che specificano corrispondano esattamente a un mapping e i bordi nella mappa condiviso. Se non si specificano i limiti di intervallo tutto il servizio verrà dedurre l'intervallo più vicino necessità automaticamente. È possibile utilizzare lo script di PowerShell GetMappings.ps1 per recuperare i mapping correnti in una mappa condiviso specificato.

-    Comportamento di origine e divisione (divisione): per le operazioni di divisione, definire il punto per dividere l'intervallo di origine. Questo caso, fornendo la chiave sharding in cui si desidera venga divisa. Usare il pulsante di opzione specificare se si desidera la parte inferiore dell'intervallo (esclusa la chiave diviso) spostare o se si desidera la parte superiore per spostare (inclusa la chiave di divisione).

-    Origine Shardlet (sposta): spostare le operazioni sono diverse da operazioni di divisione o unione come non richiedono un intervallo per indicare l'origine. Una fonte per spostare semplicemente è identificata dal valore chiave sharding che si desidera spostare.

-    Destinazione condiviso (divisione): dopo aver assegnato le informazioni sull'origine dell'operazione di divisione, è necessario definire nel punto in cui si desidera che i dati da copiare fornendo il nome del server e database Db di SQL Azure per il sito di destinazione.

-    Intervallo di destinazione (unire): operazioni di unione spostare shardlets un condiviso esistente. Per identificare condiviso esistente, fornire i limiti di intervallo dell'intervallo esistente che si desidera unire.

-    Dimensione batch: Le dimensioni del batch controlla il numero di shardlets che vengano indirizzati offline in un momento durante lo spostamento dei dati. Questo è un valore intero in cui è possibile utilizzare i valori più piccoli quando sono riservati per lunghi periodi di tempo di inattività per shardlets. Valori più grandi richiederà più tempo che un determinato shardlet ma non in linea per migliorare le prestazioni.

-    Id operazione (Annulla): Se si dispone di un'operazione in corso non è più necessaria, è possibile annullare l'operazione fornendo l'ID di operazione in questo campo. È possibile recuperare l'ID di operazione dalla tabella richiesta stato (vedere la sezione 8.1) o dall'output nel browser web in cui si ha inviato la richiesta.


## <a name="requirements-and-limitations"></a>Requisiti e limitazioni 

L'implementazione corrente del servizio di stampa unione di divisione è soggetto a requisiti e delle limitazioni seguenti: 

* il shards necessario esistere ed essere registrati nella mappa condiviso prima di poter eseguire un'operazione di divisione unione in queste shards. 

* Il servizio non creare tabelle o altri oggetti di database automaticamente come parte delle operazioni. Questo errore indica che lo schema per sharded tutte le tabelle e tabelle di riferimento devono essere disponibili in condiviso destinazione prima di qualsiasi operazione Unisci/Dividi/sposta. Tabelle sharded in particolare devono essere vuote nell'intervallo in cui shardlets nuovo devono essere aggiunti da un'operazione di divisione/unione/spostamento. In caso contrario, l'operazione non riesce la coerenza iniziale su condiviso destinazione. Si noti inoltre che fanno riferimento a dati vengono copiati solo se il riferimento di tabella è vuota e che non esistono garanzie coerenza per quanto riguarda altri simultanea scrivere operazioni sulle tabelle di riferimento. È consigliabile questo: durante l'esecuzione di operazioni di divisione/unione, altre operazioni di scrittura apportare modifiche alle tabelle di riferimento.

* Il servizio si basa sull'identità riga stabilita tramite un indice univoco o chiave che include la chiave sharding per migliorare le prestazioni e affidabilità per shardlets di grandi dimensioni. In questo modo il servizio spostare i dati un granularità anche maggiore rispetto solo il valore di chiave sharding. In questo modo per ridurre la maggior quantità di spazio di log e blocchi necessari durante l'operazione. È consigliabile creare un indice univoco o una chiave primaria incluso il tasto sharding in una determinata tabella se si desidera utilizzare la tabella con le richieste di Unisci/Dividi/sposta. Per motivi di prestazioni chiave sharding dovrebbe essere la colonna iniziale nella chiave o l'indice.

* Nel corso dell'elaborazione della richiesta, alcuni dati shardlet potrebbero essere presenti sia sul sito di origine e condiviso destinazione. Questo è necessario per la protezione contro gli errori durante lo spostamento shardlet. L'integrazione di divisione unione con la mappa condiviso garantisce che le connessioni tra i dati dipendenti routing API utilizzando il metodo **OpenConnectionForKey** sulla mappa condiviso non è visibile eventuali incoerenze nelle stati intermedi. Tuttavia, quando ci si connette alla fonte o shards destinazione senza utilizzare il metodo **OpenConnectionForKey** , stati intermedi incoerenti potrebbero essere visibili quando le richieste di Unisci/Dividi/sposta sono in corso. Queste connessioni possono visualizzare i risultati parziali o duplicati a seconda dell'intervallo o condiviso sottostante la connessione. Questa limitazione include attualmente connessioni dalle scalabilità flessibile Multi-Shard-query.

* Database dei metadati del servizio di stampa unione Dividi non deve essere condivise tra ruoli diversi. Ad esempio un ruolo del servizio di stampa unione divisa in esecuzione in gestione temporanea deve in modo che puntino a un database di metadati diversi rispetto al ruolo di produzione.
 

## <a name="billing"></a>Fatturazione 

Come servizio cloud nell'abbonamento a Microsoft Azure viene eseguito il servizio di stampa unione di divisione. Di conseguenza i servizi cloud si applicano le spese l'istanza del servizio. A meno che non si eseguono operazioni Unisci/Dividi/sposta di frequente, è consigliabile che si elimina il servizio cloud di stampa unione di divisione. Che consente di salvare i costi per l'esecuzione o distribuiti istanze del servizio cloud. È possibile ridistribuire e avviare la configurazione facilmente eseguibile ogni volta che è necessario eseguire le operazioni di divisione o unione. 
 
## <a name="monitoring"></a>Monitoraggio 
### <a name="status-tables"></a>Tabelle di stato 

Il servizio di stampa unione di divisione offre la tabella di **stato richiesta** nei metadati archivio database per il monitoraggio delle richieste completate e in corso. Nella tabella sono elencati una riga per ogni richiesta di stampa unione di divisione che è stato inviato a questa istanza del servizio di stampa unione divisa. Fornisce le informazioni seguenti per ogni richiesta:

* **Timestamp**: quando è stata avviata la richiesta di data e ora.

* **OperationId**: un GUID che identifica la richiesta. La richiesta anche utilizzabile per annullare l'operazione mentre è ancora in corso.

* **Stato**: lo stato corrente della richiesta. Per le richieste in corso, vengono elencati anche la fase corrente in cui è la richiesta.

* **CancelRequest**: un contrassegno che indica se la convocazione è stata annullata.

* **Corso**: una stima della percentuale di completamento per l'operazione. Il valore di 50 indica che l'operazione è di circa il 50% completamento.

* **Dettagli**: valore XML che fornisce informazioni più dettagliate sullo stato. Il rapporto di stato viene aggiornato periodicamente in set di righe vengono copiate dall'origine di destinazione. In caso di errori o eccezioni, questo articolo include anche informazioni più dettagliate sull'errore.


### <a name="azure-diagnostics"></a>Diagnostica Windows Azure

Il servizio di stampa unione divisa utilizza diagnostica Azure in base a Azure SDK 2,5 per il monitoraggio e diagnostica. È possibile controllare la configurazione di diagnostica come indicato di seguito: [Abilitazione diagnostica in macchine virtuali e servizi Cloud Windows Azure](../cloud-services/cloud-services-dotnet-diagnostics.md). Il pacchetto di download include due configurazioni di diagnostica: uno per il ruolo web e uno per il ruolo di lavoro. Queste configurazioni di diagnostica per il servizio di seguono le indicazioni [Sulle nozioni fondamentali sul servizio Cloud di Microsoft Azure](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Include le definizioni per registrare contatori, i registri IIS, i registri eventi di Windows e registri applicazioni divisa unione. 

## <a name="deploy-diagnostics"></a>Distribuire diagnostica 

Per abilitare il monitoraggio e diagnostica utilizzando la configurazione di diagnostica per i ruoli di lavoro e web forniti dal pacchetto NuGet, eseguire i comandi seguenti usando Azure PowerShell: 

    $storage_name = "<YourAzureStorageAccount>" 
    
    $key = "<YourAzureStorageAccountKey" 
    
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  
    
    
    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 
    
    
    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

È possibile trovare ulteriori informazioni su come configurare e distribuire le impostazioni di diagnostica qui: [Abilitazione diagnostica in macchine virtuali e servizi Cloud Windows Azure](../cloud-services/cloud-services-dotnet-diagnostics.md). 

## <a name="retrieve-diagnostics"></a>Recuperare diagnostica 

È possibile accedere facilmente del sistema di diagnostica da Visual Studio Server Explorer nella parte Azure della struttura di Esplora Server. Aprire un'istanza di Visual Studio e nella barra dei menu fare clic su Visualizza ed Esplora Server. Fare clic sull'icona Azure per connettersi al proprio abbonamento Azure. Passare quindi alla Azure -> spazio di archiviazione -> <your storage account> -> tabelle -> WADLogsTable. Per ulteriori informazioni, vedere [Risorse di archiviazione esplorazione con Esplora risorse Server](http://msdn.microsoft.com/library/azure/ff683677.aspx). 

![WADLogsTable][2]

WADLogsTable evidenziato nella figura precedente contiene degli eventi di registro dell'applicazione del servizio di stampa unione di divisione. Si noti che la configurazione predefinita di pacchetto scaricato è rivolto a una distribuzione di produzione. Pertanto l'intervallo recapitati registri e contatori da istanze del servizio è grande (5 minuti). Per test e lo sviluppo, ridurre l'intervallo modificando le impostazioni di diagnostica del web o il ruolo di lavoro per le proprie esigenze. Pulsante destro del mouse sul ruolo in Visual Studio Server Explorer (vedere sopra) e quindi modificare il periodo di trasferire nella finestra di dialogo per le impostazioni di configurazione di diagnostica: 

![Configurazione][3]


## <a name="performance"></a>Prestazioni

In generale, migliorare le prestazioni sono previsto da più alto, più livelli di servizio efficienti in Database SQL Azure. Allocazioni IO, CPU e memoria superiore per i livelli di servizio superiore trarre vantaggio la copia di massa ed eliminare le operazioni che usa il servizio di stampa unione di divisione. Per questo motivo, aumentare il livello di servizio solo per i database per un periodo di tempo definito limitato.

Il servizio esegue anche le query di convalida durante il normale funzionamento. Queste query convalida controllare imprevisti presenza di dati nell'intervallo di destinazione e assicurarsi che qualsiasi operazione Unisci/Dividi/Sposta inizia da uno stato coerente. Tutte le query funzionano in intervalli di chiavi sharding definiti in base all'ambito dell'operazione e le dimensioni del batch forniti come parte di definizione della richiesta. Queste query risultati migliori quando un indice è presente con la chiave sharding come colonna iniziale. 

Inoltre, una proprietà univocità insieme al tasto sharding come colonna iniziale consentirà al servizio di utilizzare un approccio ottimizzato che limita l'utilizzo delle risorse in termini di memoria e spazio di log. Questa proprietà univocità è necessaria per spostare le dimensioni di dati di grandi dimensioni (in genere superiore a 1GB). 

## <a name="how-to-upgrade"></a>Come eseguire l'aggiornamento

1. Seguire i passaggi descritti in [Distribuisci un servizio di stampa unione di divisione](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Modificare il file di configurazione del servizio cloud per la distribuzione di stampa unione divisa in modo da rispecchiare i nuovi parametri di configurazione. Un nuovo parametro richiesto è le informazioni sul certificato utilizzato per la crittografia. Un modo semplice per eseguire questa operazione consiste nel confrontare nuovo file di modello configurazione del download contro la configurazione esistente. Assicurarsi di aggiungere le impostazioni per "DataEncryptionPrimaryCertificateThumbprint" e "DataEncryptionPrimary" per il web e il ruolo di lavoro.
3. Prima di distribuire l'aggiornamento di Azure, verificare che siano state completamente tutte le operazioni di stampa unione attualmente in esecuzione divisa. È possibile eseguire facilmente questa eseguendo le tabelle di stato richiesta e PendingWorkflows nel database di metadati unione di divisione per le richieste in corso.
4. Aggiornare la distribuzione di servizio cloud esistente per unione divisa nell'abbonamento Azure con il nuovo pacchetto e il file di configurazione del servizio aggiornate.

Non è necessario effettuare il provisioning di un nuovo database di metadati per divisa unione eseguire l'aggiornamento. La nuova versione aggiornerà automaticamente il database di metadati esistenti alla nuova versione. 

## <a name="best-practices--troubleshooting"></a>Procedure consigliate e risoluzione dei problemi
 
-    Definire un tenant di test e sperimentare operazioni Unisci/Dividi/spostare più importanti con tenant test tra più shards. Assicurarsi che tutti i metadati sono definito correttamente nella mappa condiviso e le operazioni non violano vincoli o chiavi esterne.
-    Mantenere il tenant di test problemi relativi alla dimensione di dati sopra le dimensioni massime dei dati del tenant più grande per assicurarsi che non si è verificato la dimensione dei dati. Consente di valutare un limite superiore nel tempo che necessario per spostare un singolo tenant. 
-    Assicurarsi che lo schema consente le eliminazioni. Il servizio di stampa unione divisione richiede la possibilità di rimuovere i dati dal condiviso origine dopo i dati sono stati copiati correttamente alla destinazione. Ad esempio, **eliminare trigger** può impedire l'eliminazione dei dati nell'origine del servizio e può causare operazioni di non riuscire.
-    Il tasto sharding dovrebbe essere la colonna iniziale la chiave primaria o definizione di indice univoco. Che garantisce le massime prestazioni per le query di unione o divisione convalida e per le operazioni di spostamento e l'eliminazione dei dati effettivi che funzionano sempre o gli intervalli chiave sharding.
-    Inserire il servizio di stampa unione di divisione nell'interfaccia di dati e area geografica in cui si trovano i database. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]



<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
 
