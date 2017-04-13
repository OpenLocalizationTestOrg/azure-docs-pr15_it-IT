<properties
   pageTitle="Progettare modelli per le applicazioni SaaS multi-tenant e Database di SQL Azure | Microsoft Azure"
   description="In questo articolo vengono illustrati i requisiti e modelli di architettura dati comuni di database multi-tenant applicazioni in esecuzione in un ambiente basato su cloud necessario prendere in considerazione e compromessi vari associati a questi modelli. Viene inoltre come Database di SQL Azure, le relative flessibile pool e flessibile strumenti risolvere questi requisiti in modo senza compromessi."
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-design"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="design-patterns-for-multitenant-saas-applications-and-azure-sql-database"></a>Progettare modelli per le applicazioni SaaS multi-tenant e Database di SQL Azure

In questo articolo per informazioni sui requisiti e dati comuni modelli di architettura del software multi-tenant come applicazioni di database un servizio in esecuzione in un ambiente basato su cloud. Viene inoltre i fattori da considerare e compromessi dei modelli di progettazione diversi. Pool flessibile e flessibile strumenti di Database SQL Azure consentono di soddisfare specifiche esigenze senza compromettere altri obiettivi.

Gli sviluppatori talvolta rendere scelte che funzionano con loro interesse a lungo termine quando si progettano modelli affitto per i livelli di dati delle applicazioni multi-tenant. Inizialmente, almeno, uno sviluppatore avrà notato facilità di sviluppo e ridurre i costi di provider del servizio cloud come più importante di isolamento di tenant o scalabilità di un'applicazione. Questa opzione può comportare le preoccupazioni soddisfazione dei clienti e una correzione corso costosa in un secondo momento.

Un'applicazione multi-tenant è un'applicazione ospitata in un ambiente basato su cloud che fornisce lo stesso set di servizi a centinaia o migliaia di tenant che non condividere o visualizzare i dati. Un esempio è un'applicazione di SaaS che offre servizi a tenant situati in un ambiente ospitato cloud.

## <a name="multitenant-applications"></a>Applicazioni multi-tenant

Nelle applicazioni multi-tenant, dati e il carico di lavoro possono essere facilmente suddivise. È possibile suddividere dati e il carico di lavoro, ad esempio lungo limiti del tenant, poiché la maggior parte delle richieste verranno inoltrate entro i confini di un tenant. La proprietà è inerente i dati e il carico di lavoro e favoriscono i modelli di applicazione descritti in questo articolo.

Gli sviluppatori utilizzare questo tipo di applicazione per l'intera gamma di applicazioni basate su cloud, tra cui:

- Applicazioni di database partner che sono transizione nel cloud come applicazioni SaaS
- Applicazioni SaaS compilate per il cloud da zero
- Applicazioni dirette, per i clienti
- Applicazioni enterprise connesse a dipendente

Le applicazioni SaaS progettate per il cloud e quelle ottenute con radici come partner applicazioni di database in genere sono applicazioni multi-tenant. Queste applicazioni SaaS offrono un'applicazione software specializzata come servizio ai loro tenant. Tenant possono accedere al servizio dell'applicazione e avere la proprietà completa del associato dati archiviati come parte dell'applicazione. Ma per sfruttare i vantaggi dei SaaS, tenant devono cedere di controllare i propri dati. Si considera attendibile il provider di servizi SaaS per mantenere i dati attendibili e isolato dai dati di altre tenant. Esempi di questo tipo di applicazione SaaS multi-tenant sono MYOB, SnelStart e Salesforce.com. Ognuna di queste applicazioni può essere suddiviso lungo limiti del tenant e supporto tecnico dell'applicazione progettare modelli verranno illustrate in questo articolo.

Le applicazioni che forniscono un servizio diretto ai clienti o ai dipendenti di un'organizzazione (detti per gli utenti, anziché tenant) sono un'altra categoria sullo spettro applicazione multi-tenant. I clienti sottoscrivere il servizio e non si possiede i dati che il provider di servizi raccoglie e archivia. Provider di servizi hanno meno requisiti per mantenere i dati dei clienti isolati tra loro oltre normative definito per enti pubblici. Esempi di questo tipo di applicazione multi-tenant per i clienti sono provider di contenuti multimediali quali Netflix Spotify e Xbox LIVE. Altri esempi di applicazioni facilmente importante per i clienti, applicazioni su Internet o applicazioni Internet di elementi (IoT) in cui ogni cliente o un dispositivo può fungono da una partizione. Limiti della partizione possibile separare gli utenti e i dispositivi.

Non tutte le partizioni applicazioni facilmente lungo una singola proprietà, ad esempio tenant, cliente, utente o un dispositivo. Una pianificazione applicazione (ERP), delle risorse aziendali complesse, ad esempio, sono i prodotti, ordini e clienti. In genere include uno schema complesso con migliaia di tabelle altamente interconnesse.

Nessuna strategia singola partizione applicare a tutte le tabelle e collabora in carico di lavoro dell'applicazione. In questo articolo è incentrato sulle applicazioni multi-tenant che hanno carichi di lavoro e dati facilmente importante.

## <a name="multitenant-application-design-trade-offs"></a>Compromessi applicazione multi-tenant di progettazione

Il modello di progettazione che uno sviluppatore di applicazioni multi-tenant sceglie in genere si basa in considerazione i fattori seguenti:

-   **Isolamento tenant**. Lo sviluppatore deve per assicurarsi che nessuna tenant abbia accesso non autorizzato ai dati di altre tenant. Il requisito di isolamento estende ad altre proprietà, ad esempio fornendo protezione da altre istanze disturbate, la possibilità di ripristinare i dati del tenant e implementare le personalizzazioni specifiche del tenant.
-   **Costo risorsa cloud**. Un'applicazione di SaaS deve essere competitivo. Uno sviluppatore di applicazioni multi-tenant possibile ottimizzare per costi di utilizzo delle risorse di cloud, ad esempio lo spazio di archiviazione e calcolare i costi.
-   **Facilità di attrezzi**. Uno sviluppatore di applicazioni multi-tenant deve incorporare la protezione con isolamento, mantenere e monitorare lo stato del loro applicazione e allo schema del database e risoluzione dei problemi di tenant. Complessità di sviluppo di applicazioni e funzionamento ruolo direttamente aumento dei costi e verifica con la soddisfazione del tenant.
-   **Scalabilità**. La possibilità di aggiungere in modo incrementale ulteriori tenant e capacità per tenant che ne hanno bisogno è fondamentale per un corretto funzionamento di SaaS.

Ognuno dei fattori seguenti è compromessi rispetto a un'altra. Cloud di costo più basso che offre possono non offrire l'esperienza di sviluppo più comodo. È importante per uno sviluppatore di effettuare scelte informate su queste opzioni e i compromessi durante il processo di progettazione dell'applicazione.

Un modello di sviluppo comuni è per comprimere più tenant di in una o più database. I vantaggi di questo approccio sono un costo poiché si paga per alcuni database e la relativa semplicità di utilizzo di un numero limitato di database. Ma nel tempo, uno sviluppatore di applicazioni multi-tenant SaaS verrà tenere presente che questa opzione è sostanziale svantaggi isolamento tenant e scalabilità. Se non è più importante isolamento tenant, aggiuntive è necessario proteggere i dati tenant dell'archiviazione condivisa da accessi non autorizzati o disturbati adiacenti. Questo impegno aggiuntivo potrebbe promuovere in modo significativo di sviluppo e dei costi di manutenzione di isolamento. Analogamente, se è necessaria l'aggiunta di tenant, questo modello di progettazione richiede in genere competenze ridistribuire tenant dati tra database per ridimensionare correttamente il livello di dati di un'applicazione.  

Tenant isolamento spesso è fondamentale nelle applicazioni multi-tenant SaaS che li forniscono per le aziende e organizzazioni. Uno sviluppatore può essere tentato da vantaggi percepite semplicità e del costo rispetto isolamento tenant e scalabilità. Questo compromesso può rivelarsi complessa e costosa come il servizio che si sviluppa e requisiti di isolamento tenant diventano più importanti e gestiti a livello di applicazione. Tuttavia, nelle applicazioni multi-tenant che forniscono un servizio diretto, consumer rivolta ai clienti di isolamento tenant potrebbe essere una priorità più bassa rispetto all'ottimizzazione di cloud risorsa costo.

## <a name="multitenant-data-models"></a>Modelli di dati multi-tenant

Comuni consigliate per la progettazione dell'immissione dati tenant eseguire tre modelli distinti, illustrati nella figura 1.


  ![Modelli di dati di applicazione multi-tenant](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png)
 figura 1: consigliate per la progettazione comuni per i modelli di dati multi-tenant

-   **Database per tenant**. Ogni tenant è il proprio database. Tutti i dati specifici di tenant è limitato al database del tenant e isolato da altri tenant e i relativi dati.
-   **Sharded database condiviso**. Più tenant di condividere uno dei più database. Un set di valori univoci di tenant viene assegnato a ogni database utilizzando una strategia di gestione delle partizione, ad esempio hash, intervallo o elenco partizioni. Questa strategia di distribuzione dati spesso viene definita sharding.
-   **Singolo di database condiviso**. Un solo database talvolta grande contiene dati per tutti i tenant, identificati in una colonna ID tenant.

> [AZURE.NOTE] Lo sviluppatore dell'applicazione potrebbe scegliere di inserire tenant diversi in schemi di database diverso e quindi utilizzare il nome dello schema per distinguere diversi tenant. Si consiglia di questo approccio non richiede in genere l'uso di SQL dinamico, e non può essere efficace per la memorizzazione nella cache di piano. Nel resto di questo articolo, l'attenzione su quella tabella condivisi per la categoria di applicazione multi-tenant.

## <a name="popular-multitenant-data-models"></a>Modelli di dati multi-tenant comuni

È importante valutare i diversi tipi di modelli di dati multi-tenant in termini di applicazione progettazione compromessi che sono già stati identificati. Questi fattori sono utili per caratterizzare i tre modelli di dati multi-tenant più comuni descritti in precedenza e il relativo utilizzo di database, come illustrato nella figura 2.

-   **Isolamento**. Il livello di isolamento tra tenant può essere una misura di isolamento quantità tenant raggiunge un modello di dati.
-   **Costo risorsa cloud**. L'importo della risorsa condivisione tra tenant può ottimizzare cloud risorsa costo. Una risorsa può essere definita come il costo di elaborazione e di archiviazione.
-   **Costo attrezzi**. La facilità di sviluppo di applicazioni, distribuzione e la gestibilità riduce costi di operazione SaaS complessivo.  

Nella figura 2, l'asse Y mostra il livello di isolamento tenant. L'asse X illustra il livello di condivisione di risorse. Grigio, freccia diagonale al centro indica la direzione di costi attrezzi, automatiche per aumentare o diminuire.

![Modelli di progettazione di applicazioni multi-tenant popolari](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png) figura 2: modelli di dati multi-tenant comuni

In questa fase in basso a destra nella figura 2 mostra il modello di applicazione che utilizza una grande, solo database condiviso e la tabella condivisi (o schema separato) approccio. È consigliabile per risorsa condivisione perché tutti i tenant utilizzano le stesse risorse di database (CPU, memoria, ingresso/uscita) in un unico database. Ma tenant isolamento è limitato. Potrebbe essere necessario eseguire altre operazioni aggiuntive per proteggere i tenant da altro livello di applicazione. Questi passaggi aggiuntivi aumenta notevolmente il costo di attrezzi di sviluppo e la gestione dell'applicazione. Scalabilità è limitata, la scala dell'hardware che ospita i database.

In questa fase in basso a sinistra nella figura 2 illustra più tenant sharded su più database (in genere hardware diverso scalare unità). Ogni database ospita un sottoinsieme dei tenant, che consente di risolvere il problema di scalabilità di altri modelli. Se è necessaria per tenant ulteriori capacità più, è possibile inserire facilmente il tenant nel nuovi database a nuove unità scala hardware. Tuttavia, viene ridotta la quantità di condivisione di risorse. Solo i tenant collocato nella stessa unità di misura scala Condividi risorse. Questo approccio offre miglioramento little per tenant isolamento perché tenant molti sono ancora collocato senza automaticamente protetto da azioni di altro. La complessità dell'applicazione rimane alta.

In questa fase alto a sinistra nella figura 2 è il terzo approccio. Dati di ogni del tenant viene inserito in un database. Questo approccio dispone di proprietà di isolamento di tenant buona ma limita la condivisione delle risorse quando ogni database viene utilizzato i proprio risorse dedicate. Questo approccio è utile se dispone di tutti i tenant prevedibili carichi di lavoro. Se carichi di lavoro tenant diventano meno prevedibili, il provider di servizi non è possibile ottimizzare la condivisione delle risorse. Potenziali sono comuni per le applicazioni SaaS. Il provider di servizi deve provisioning in eccesso per soddisfare le esigenze o risorse inferiore. Entrambe le azioni comporta costi più elevati oppure inferiore soddisfazione del tenant. Maggiore della risorsa condivisione tra tenant diventa utile per rendere la soluzione più conveniente. Aumento del numero di database aumenta anche costo attrezzi per distribuire e gestire l'applicazione. Nonostante questi problemi, questo metodo fornisce isolamento migliore e più semplice per tenant.

Questi fattori influiscono anche il modello di progettazione di che un cliente sceglie:

-   **Proprietà dei dati tenant**. Un'applicazione in cui tenant di mantengono la proprietà sui propri dati favorisce la stessa combinazione di un solo database per ogni tenant.
-   **Scala**. Un'applicazione destinata centinaia di migliaia o milioni di tenant favorisce la condivisione approcci, ad esempio sharding del database. Requisiti di isolamento ancora possono causare problemi.
-   **Valore modello aziendale e**. Se un'applicazione dei ricavi per-tenant se opportuno per piccole (minore di dollaro), i requisiti di isolamento diventano meno critici e un database condiviso. Se i ricavi-tenant sono pochi euro, un modello di database per ogni tenant è più appropriato. Potrebbe essere utile ridurre i costi di sviluppo.

Dato progettazione compromessi illustrati nella figura 2, un modello multi-tenant ideale deve incorporare le proprietà di isolamento tenant efficace con ottimale condivisione delle risorse tra i tenant. Questo modello adatto nella categoria descritta nella fase angolo superiore destro della figura 2.

## <a name="multitenancy-support-in-azure-sql-database"></a>Supporto "multitenancy" nel Database di SQL Azure

Database SQL Azure supporta tutti i modelli di applicazione multi-tenant descritti nella figura 2. Con i pool flessibile, supporta anche un modello di applicazione che combina la condivisione delle risorse efficace e i vantaggi di isolamento di database per tenant approccio (vedere in questa fase alto a destra nella figura 3). Strumenti database flessibile e funzionalità di Database SQL di ridurre i costi per sviluppare e utilizzare un'applicazione che contiene numerosi database (mostrati nell'area di ombreggiatura nella figura 3). Questi strumenti consentono di creare e gestire applicazioni che utilizzano uno dei modelli più database.

![Modelli di Database SQL Azure](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png) figura 3: modelli di applicazione multi-tenant nel Database di SQL Azure

## <a name="database-per-tenant-model-with-elastic-pools-and-tools"></a>Modello di database per tenant flessibile pool e strumenti

Pool flessibile in Database SQL di combinare isolamento tenant con condivisione delle risorse tra i database tenant per un migliore supporto approccio database per tenant. Database SQL è una soluzione di livelli di dati per i provider SaaS applicazioni multi-tenant. Il carico di lavoro di condivisione delle risorse tra i tenant viene spostato dal livello di applicazione il livello di servizio del database. La complessità della gestione e query in scala tra database è stata semplificata con processi flessibile, flessibile query, le transazioni flessibile e la libreria di client database flessibile.

| Requisiti dell'applicazione | Funzioni di database SQL |
| ------------------------ | ------------------------- |
| Tenant isolamento e la condivisione delle risorse | [Pool flessibile](sql-database-elastic-pool.md): allocare un pool di risorse di Database SQL e condividere le risorse tra vari database. Inoltre, singoli database possono disegnare quante risorse dal pool per adattarli capacità picchi variazioni carichi di lavoro tenant. Il pool di flessibile può essere ridimensionato verso l'alto o verso il basso in base alle esigenze. Pool flessibile anche semplificano la gestione e il monitoraggio e risoluzione dei problemi a livello del pool. |
| Facilità di attrezzi in database | [Pool flessibile](sql-database-elastic-pool.md): come indicato in precedenza.|
||[Query flessibile](sql-database-elastic-query-horizontal-partitioning.md): Query su database per i report o tra tenant di analisi.|
||[Processi flessibile](sql-database-elastic-jobs-overview.md): pacchetto e distribuire in modo affidabile le operazioni di gestione di database o le modifiche allo schema di database a più database.|
||[Transazioni flessibile](sql-database-elastic-transactions-overview.md): processo di modifica a più database in modo atomico e isolato. Se le applicazioni necessita di "tutto o niente" garanzie su varie operazioni di database, sono necessarie transazioni flessibile. |
||[Libreria client database flessibile](sql-database-elastic-database-client-library.md): gestire le distribuzioni di dati ed eseguire il mapping tenant ai database. |

## <a name="shared-models"></a>Modelli condivisi

Come descritto in precedenza, per la maggior parte dei provider di SaaS, un approccio modello condiviso può comportare problemi con problemi di isolamento tenant e le complessità di sviluppo di applicazioni e la manutenzione. Tuttavia, per applicazioni multi-tenant che forniscono un servizio direttamente ai consumer, requisiti di isolamento tenant potrebbero non essere una priorità alta come come la riduzione a icona costo. Potrebbe potrà essere compresse tenant in uno o più database ad alta densità per ridurre i costi. Modelli di database condiviso con un solo database o più database sharded potrebbe aggiungere altre opzioni costo complessivo e condivisione delle risorse. Database SQL Azure fornisce alcune funzionalità che consentono di compilazione isolamento per maggiore sicurezza e la gestione solo a livello di dati.

| Requisiti dell'applicazione | Funzioni di database SQL |
| ------------------------ | ------------------------- |
| Funzionalità di isolamento di sicurezza | [Protezione a livello di riga](https://msdn.microsoft.com/library/dn765131.aspx) |
|| [Schema di database](https://msdn.microsoft.com/library/dd207005.aspx) |
| Facilità di attrezzi in database | [Query flessibile](sql-database-elastic-query-horizontal-partitioning.md) |
|| [Processi flessibile](sql-database-elastic-jobs-overview.md) |
|| [Transazioni flessibile](sql-database-elastic-transactions-overview.md) |
|| [Libreria client database flessibile](sql-database-elastic-database-client-library.md) |
|| [Divisione di database flessibile e unione](sql-database-elastic-scale-overview-split-and-merge.md) |

## <a name="summary"></a>Riepilogo

Requisiti di isolamento tenant sono importanti per la maggior parte delle applicazioni multi-tenant SaaS. L'opzione ottimale per isolare leans modo intensivo verso l'approccio di database per tenant. Altri due approcci richiedono investimenti nei livelli di applicazione complessa che richiedono il team di sviluppo qualificato per fornire isolamento che in modo significativo costi e i rischi. Se i requisiti di isolamento non conteggiati fase iniziale per lo sviluppo di servizio, adattamento loro può essere ancora più costosa nelle prime due modelli. Svantaggi principali associati al modello di database per tenant sono correlati ai costi delle risorse cloud maggiore a causa di condivisione ridotte e la gestione e la gestione di molti database. Gli sviluppatori di applicazioni SaaS spesso difficoltà quando effettuano tali compromessi.

Sebbene compromessi potrebbero essere ostacoli principali con la maggior parte dei provider di servizi di database cloud, Database SQL Azure Elimina gli ostacoli con il pool flessibile e funzionalità di database flessibile. Gli sviluppatori SaaS possano combinare le caratteristiche di isolamento di un modello di database per tenant e ottimizzare la condivisione delle risorse e la gestibilità di molti database mediante pool flessibile e strumenti associati.

Provider di applicazioni multi-tenant che non dispone di alcun requisito di isolamento tenant e chi può pack tenant in un database ad alta densità possono risultare che dati condivisi modelli risultato condivisione di risorse aggiuntive dell'efficienza e ridurre i costi totali. Strumenti di database flessibile Database SQL Azure, sharding raccolte e le funzionalità di protezione sono utili per i provider SaaS creare e gestire applicazioni multi-tenant.

## <a name="next-steps"></a>Passaggi successivi

[Guida introduttiva a Strumenti database flessibile](sql-database-elastic-scale-get-started.md) a un'applicazione di esempio che illustra la libreria client.

Creare un [dashboard personalizzato pool flessibile per SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) con un'applicazione di esempio che utilizza pool flessibile per una soluzione di database scalable e conveniente.

Utilizzare gli strumenti di Database SQL Azure per [eseguire la migrazione di database esistenti a scalare](sql-database-elastic-convert-to-use-elastic-tools.md).

Visualizzare le esercitazioni su come [creare un pool flessibile](sql-database-elastic-pool-create-portal.md).  

Informazioni su come [monitorare e gestire un pool flessibile](sql-database-elastic-pool-manage-portal.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Che cos'è un pool flessibile Azure?](sql-database-elastic-pool.md)
- [Scalabilità orizzontale con il Database di SQL Azure](sql-database-elastic-scale-introduction.md)
- [Multi-tenant applicazioni con protezione a livello di riga e gli strumenti di database flessibile](sql-database-elastic-tools-multi-tenant-row-level-security.md)
- [Autenticazione nelle App multi-tenant con Azure Active Directory e connettere OpenID](../guidance/guidance-multitenant-identity-authenticate.md)
- [Applicazione di sondaggi Tailspin](../guidance/guidance-multitenant-identity-tailspin.md)
- [Avvio rapido soluzione](sql-database-solution-quick-starts.md)

## <a name="questions-and-feature-requests"></a>Domande e le richieste di funzionalità

Per eventuali domande, contattarci nel [forum di Database SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Aggiungere una richiesta di funzionalità nel [forum di Database SQL di commenti e suggerimenti](https://feedback.azure.com/forums/217321-sql-database/).
