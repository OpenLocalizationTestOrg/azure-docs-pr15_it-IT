<properties
    pageTitle="Scalabilità orizzontale con il Database di SQL Azure | Microsoft Azure"
    description="Software come gli sviluppatori di servizio poter creare facilmente database flessibile, scalable nel cloud tramite questi strumenti"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="ddove"/>

# <a name="scaling-out-with-azure-sql-database"></a>Scalabilità con Database di SQL Azure

È possibile adattare il database di SQL Azure con gli strumenti di **Database flessibile** . Questi strumenti e caratteristiche consentono di utilizzare le risorse di database praticamente illimitata di **Database SQL Azure** per creare soluzioni per transazione carichi di lavoro e in particolare Software come applicazioni di servizio. Caratteristiche di Database flessibile sono costituite le operazioni seguenti:

* [Libreria client Database flessibile](sql-database-elastic-database-client-library.md): la raccolta di client non è una caratteristica che consente di creare e gestire i database sharded.  Vedere [Guida introduttiva a strumenti Database flessibile](sql-database-elastic-scale-get-started.md).
* [Strumento di unione Dividi Database flessibile](sql-database-elastic-scale-overview-split-and-merge.md): consente di spostare dati tra database sharded. Questo è utile per spostare i dati da un database multi-tenant a un database tenant singolo (o viceversa). Vedere [database flessibile esercitazione strumento unione divisa](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Processi di Database flessibile](sql-database-elastic-jobs-overview.md) (preview): utilizzo dei processi per gestire un numero elevato di database SQL Azure. Eseguire facilmente operazioni amministrative, ad esempio le modifiche allo schema, la gestione delle credenziali, gli aggiornamenti dei dati di riferimento, raccolta dati sulle prestazioni o raccolta di telemetria tenant (cliente) utilizzando i processi.
* [Query di Database flessibile](sql-database-elastic-query-overview.md) (preview): consente di eseguire una query Transact-SQL che si estende su più database. In questo modo connessione agli strumenti dei report, ad esempio Excel, ottenere informazioni, Tableau e così via.
* [Transazioni flessibile](sql-database-elastic-transactions-overview.md): questa caratteristica consente di eseguire operazioni che si estendono su più database nel Database di SQL Azure. Le transazioni del database flessibile sono disponibili per le applicazioni di .NET tramite ADO .NET e integrano con la familiare esperienza di programmazione utilizzando le [classi System. Transaction](https://msdn.microsoft.com/library/system.transactions.aspx).

L'immagine riportata di seguito mostra un'architettura che include le **caratteristiche di Database flessibile** relazione a un insieme di database.

Nell'elemento grafico, i colori del database rappresentano gli schemi. Database con lo stesso colore condividono lo stesso schema.

1. Un set di **database SQL Azure** sono ospitati su Azure con architettura sharding.
2. La **libreria client flessibile Database** viene utilizzato per gestire un gruppo condiviso.
3. Un sottoinsieme dei database vengono inseriti in un **pool di Database flessibile**. (Vedere [che cos'è un pool?](sql-database-elastic-pool.md)).
4. Un **processo di Database flessibile** esegue programmata o Reporting ad-hoc T-SQL script in tutti i database.
5. Lo **strumento di unione di divisione** viene utilizzato per spostare i dati da un condiviso a un altro.
6. La **query di Database flessibile** consente di creare una query che si estende su tutti i database nel set di condiviso.
7. **Transazioni flessibile** consente di eseguire operazioni che si estendono su più database. 


![Strumenti di Database flessibile][1]


## <a name="why-use-the-tools"></a>Perché usare gli strumenti?

Proporzioni e la flessibilità la per applicazioni cloud è stata semplice per macchine virtuali e archiviazione blob, è sufficiente aggiungere o sottrarre unità oppure aumentare power. Ma che è rimasto un'operazione impegnativa per informazioni sullo stato elaborazione dei dati nei database relazionali. Problemi legati alla pratica in questi scenari:

* Ingrandimento o riduzione della capacità per la parte di database relazionale del carico di lavoro.
* Gestire le aree sensibili che possono verificarsi che interessano uno specifico sottoinsieme di dati, ad esempio un particolarmente occupato cliente finale (tenant).

In genere, questi scenari sono stati risolti effettuando investimenti in server di database su larga scala per supportare l'applicazione. Tuttavia, questa opzione è limitata nel cloud in tutta l'elaborazione accade sull'hardware merci predefiniti. Se, tuttavia, la distribuzione dei dati ed elaborazione attraverso molti database strutturati identico (un modello di scalabilità noto come "sharding") fornisce un'alternativa alla tradizionale approcci scalabilità in termini di costo e la flessibilità.

## <a name="horizontal-and-vertical-scaling"></a>Ridimensionamento orizzontale e verticale

La figura seguente mostra le dimensioni orizzontali e verticali di ridimensionamento, che sono i metodi di base che è possono ridimensionare i database flessibile.

![Orizzontale o verticale scalabilità][2]

Scalabilità orizzontale si intende aggiungendo o rimuovendo i database per regolare le prestazioni della capacità o globale. Detta anche "scala". Sharding, in cui i dati sono suddiviso in una raccolta di database in modo identico strutturati, è in genere implementare scalabilità orizzontale.  

Ridimensionamento verticale si riferisce a aumentare o diminuire il livello di prestazioni di un singolo database, è anche nota come "scala".

La maggior parte delle applicazioni di database cloud scala utilizza una combinazione di questi due strategie. Ad esempio, un Software come un'applicazione di servizio può utilizzare scalabilità orizzontale per effettuare il provisioning di nuovi clienti di fine e ridimensionamento verticale per consentire database del ogni cliente finale aumentare o ridurre le risorse in base alle esigenze il carico di lavoro.

* Scalabilità orizzontale viene gestita tramite la [libreria client Database flessibile](sql-database-elastic-database-client-library.md).

* Ridimensionamento verticale si basa sull'utilizzo dei cmdlet di PowerShell Azure per modificare il livello di servizio, o posizionando il database in un pool di flessibile.

## <a name="sharding"></a>Sharding

*Sharding* è una tecnica per distribuire grandi quantità di dati strutturati in modo identico in un numero di database indipendenti. È particolarmente utilizzato con gli sviluppatori cloud la creazione di Software come un offerte di servizio (SAAS) per i clienti di fine o imprese. Questi utenti finali vengono spesso indicati come "tenant". Sharding potrebbero essere necessari per diversi motivi:  

* La quantità totale di dati è troppo grande per rientra nei vincoli di un singolo database
* La velocità effettiva delle transazioni del carico di lavoro globale supera le capacità di un singolo database
* Tenant possono richiedere isolamento fisico tra loro, database separati sono necessari per ogni tenant
* Sezioni diverse di un database potrebbero essere necessario si trovano in diverse aree geografiche per conformità, le prestazioni o motivi geopolitici.

In altri scenari, ad esempio di acquisizione di dati da dispositivi distribuiti, sharding può essere utilizzato per riempire un insieme di database sono organizzati temporaneamente in. Un database separato, ad esempio, può essere dedicato a ogni giorno o per settimana. In questo caso, la chiave sharding può essere un numero intero che rappresenta la data (presenta in tutte le righe delle tabelle sharded) e il recupero di informazioni per un intervallo di date query deve essere instradata applicando il subset di database che copre l'intervallo in questione.

Sharding funziona meglio se ogni transazione in un'applicazione può essere limitati a un singolo valore di una chiave sharding. Che garantisce che tutte le transazioni locali rispetto a un database specifico.

## <a name="multi-tenant-and-single-tenant"></a>Multi-tenant e singolo tenant

Alcune applicazioni utilizzano l'approccio più semplice di creazione di un database distinto per ogni tenant. Si tratta del **motivo sharding tenant singolo** che fornisce isolamento, la possibilità di backup e ripristino e risorse proporzioni granularità tenant. Con sharding tenant singolo, ogni database è associata a un valore ID tenant specifica (o valore chiave cliente), ma tale tasto non sempre è necessario nei dati stesso. È responsabilità dell'applicazione per indirizzare ogni richiesta al database appropriato, e la libreria client può semplificare l'operazione.

![Singolo tenant e multi-tenant][4]

Altri scenari pack da acquistare più tenant insieme nel database, anziché l'isolamento in database distinti. Si tratta di un tipico **motivo sharding multi-tenant** – e dovuta al fatto che un'applicazione di gestione è affidata a un numero elevato di molto piccolo tenant. In sharding multi-tenant, le righe delle tabelle di database sono tutti progettate per eseguire un tasto che identifica l'ID di tenant o sharding. Di nuovo il livello di applicazione è responsabile per il routing della richiesta del tenant al database appropriato e si possono essere supportato dalla libreria dei client di database flessibile. Inoltre, la protezione a livello di riga può essere utilizzato per filtrare le righe in cui è possibile accedere ogni tenant: per informazioni dettagliate, vedere [applicazioni multi-tenant con Strumenti database flessibile e protezione a livello di riga](sql-database-elastic-tools-multi-tenant-row-level-security.md). Ridistribuzione dei dati nei database potrebbero essere necessarie con il criterio sharding multi-tenant e mediante lo strumento di unione Dividi database flessibile. Per ulteriori informazioni sui modelli di progettazione per applicazioni SaaS utilizzando pool flessibile, vedere [Modelli di progettazione per le applicazioni SaaS multi-tenant con Database di SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Spostare i dati da più ai database affitto singola

Quando si crea un'applicazione di SaaS, è normale per l'offerta clienti potenziali una versione di valutazione del software. In questo caso, è conveniente per utilizzare un database multi-tenant per i dati. Tuttavia, quando un prospect diventa un cliente, un database singolo tenant è migliore poiché fornisce prestazioni migliori. Se il cliente è stato creato dati durante il periodo di valutazione, usare lo [strumento di unione di divisione](sql-database-elastic-scale-overview-split-and-merge.md) per spostare i dati da più tenant per il nuovo database singolo tenant.

## <a name="next-steps"></a>Passaggi successivi

Per un'applicazione di esempio che illustra la libreria client, vedere [Guida introduttiva a strumenti Datababase flessibile](sql-database-elastic-scale-get-started.md).

Per convertire un database esistente per utilizzare gli strumenti, vedere [eseguire la migrazione database esistenti scalabilità](sql-database-elastic-convert-to-use-elastic-tools.md).

Per visualizzare le specifiche del pool di Database flessibile, vedere [considerazioni di prezzo e prestazioni di un pool di database flessibile](sql-database-elastic-pool-guidance.md)o creare un nuovo pool con [esercitazione](sql-database-elastic-pool-create-portal.md).  

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

