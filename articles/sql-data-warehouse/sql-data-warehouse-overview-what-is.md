<properties
   pageTitle="Che cos'è Warehouse di dati di SQL Azure? | Microsoft Azure"
   description="Aziendale distribuito database in grado di elaborazione petabyte volumi di dati relazionali e non relazionali. Si tratta prima cloud data warehouse del settore con ingrandimento, ridurre e posizionare il puntatore in secondi."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/27/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>


# <a name="what-is-azure-sql-data-warehouse"></a>Che cos'è Warehouse di dati di SQL Azure?

Azure SQL Data Warehouse è un database basato su cloud, scalabilità in grado di elaborare grandi quantità di dati, non relazionali e relazionali. Basato su architettura elaborazione market parallela (MPP), SQL Data Warehouse possono gestire il carico di lavoro dell'organizzazione.

SQL Data Warehouse:

- Combina i database relazionale di SQL Server con funzionalità scalabilità cloud Azure. È possibile aumentare, ridurre, sospendere o riprendere calcolo in secondi. Si risparmiare sui costi scalabilità CPU quando è necessario e taglio nuovamente l'utilizzo durante le ore di scarsa.
- Consente di sfruttare la piattaforma Azure. È facile distribuire, diretta gestiti e completamente fault tolleranza di errore a causa di backup automatico.
- Si integra con l'ecosistema di SQL Server. È possibile sviluppare con strumenti di modifica e familiarità SQL Server Transact-SQL (Transact-SQL).

In questo articolo descrive le caratteristiche chiave di SQL Data Warehouse.

## <a name="massively-parallel-processing-architecture"></a>Architettura di elaborazione market in parallelo

SQL Data Warehouse rappresenta un'elaborazione market parallela (MPP) sistema di database. Dividendo dati e funzionalità di elaborazione in più nodi, SQL Data Warehouse può offrire scalabilità enorme - molto avanzata rispetto a qualsiasi singolo sistema.  Dietro le quinte, SQL Data Warehouse si estende i dati tra numerosi lo spazio di archiviazione di condivisione e unità di elaborazione. I dati archiviati in archiviazione localmente ridondanti Premium è collegati per calcolare i nodi per l'esecuzione di query. Con questa architettura SQL Data Warehouse richiede un approccio "suddividere la parte che" per l'esecuzione di caricamento e query complesse. Le richieste vengono ricevute dal nodo del controllo, ottimizzate e quindi passate dei nodi di elaborazione per svolgere il proprio lavoro in parallelo.

Grazie alla combinazione MPP architettura e le funzionalità di archiviazione Azure, SQL Data Warehouse è possibile:

- Aumentare o ridurre lo spazio di archiviazione indipendente dal calcolo.
- Aumentare o ridurre calcolo senza spostare dati.
- Pausa calcolare capacità mantenendo invariata dati.
- Curriculum calcolare capacità un preavviso.

Di seguito è illustrata l'architettura in modo più dettagliato.

![Architettura di Data Warehouse di dati SQL][1]


**Nodo controllo:** Il nodo del controllo gestisce e query di ottimizzazione. È il front-end che interagisce con tutte le applicazioni e connessioni. In SQL Data Warehouse, il nodo del controllo è una tecnologia Database SQL e la connessione a tale struttura e l'aspetto lo stesso. Sotto la superficie, il nodo del controllo coordinate tutte di spostamento e di calcolo necessari per eseguire query parallele sui dati distribuiti. Quando si invia una query SQL T per SQL Data Warehouse, il nodo del controllo si trasforma in query distinte che eseguono su ogni nodo di calcolo in parallelo.

**Nodi di calcolo:** I nodi di elaborazione fungono da power dietro SQL Data Warehouse. Sono database di SQL in archiviare i dati ed elaborare la query. Quando si aggiungono dati, SQL Data Warehouse distribuisce le righe per i nodi di calcolo. I nodi di elaborazione sono i colleghi che eseguono query parallele sui dati. Dopo l'elaborazione, che passano i risultati al nodo del controllo. Per completare la query, il nodo del controllo aggrega i risultati e restituisce il risultato finale.

**Lo spazio di archiviazione:** I dati vengono archiviati in archiviazione Blob Azure. Quando i nodi di calcolo interagiscono con i dati, sono scrivere e leggere direttamente da e verso archiviazione blob. Poiché lo spazio di archiviazione Azure espande notevolmente e in modo trasparente, SQL Data Warehouse può effettuare la stessa. Poiché l'elaborazione e archiviazione sono indipendenti, SQL Data Warehouse può automaticamente le dimensioni dello spazio di archiviazione separatamente dal ridimensionamento calcolo e viceversa. Archiviazione Blob Azure anche completamente tolleranza e consente di semplificare il processo di backup e ripristino.

**Servizio di spostamento dei dati:** Servizio di spostamento dei dati (DMS) consente di spostare dati tra i nodi. DMS consente di accedere nodi di calcolo di dati che necessari per linee di join e le aggregazioni. DMS non è un servizio di Azure. È un servizio di Windows in esecuzione insieme a Database di SQL in tutti i nodi. Poiché DMS viene eseguito in background, non è possibile interagire con tale direttamente. Tuttavia, quando osserva il piano di query, si noterà che includono alcune operazioni DMS dopo lo spostamento dei dati è necessario eseguire tutte le query in parallelo.


## <a name="optimized-for-data-warehouse-workloads"></a>Ottimizzati per i carichi di lavoro warehouse dati

L'approccio MPP è supportato da un numero di data warehouse ottimizzazione delle prestazioni specifici, tra cui:

- Un'utilità di ottimizzazione di query distribuita e un set di statistiche complesse di tutti i dati. Utilizza le informazioni sulla distribuzione e la dimensione dei dati, il servizio in grado di ottimizzare le query valutando il costo di operazioni di query distribuito specifico.

- Algoritmi avanzati e tecniche integrato con il processo di spostamento dei dati in modo efficiente spostare dati tra le risorse di elaborazione necessarie per eseguire la query. Queste operazioni di spostamento dei dati sono progettate e tutte le ottimizzazioni per il servizio di spostamento dei dati è stato eseguito automaticamente.

- Per impostazione predefinita, a barre raggruppate **columnstore** indici. Utilizzando lo spazio di archiviazione basata su colonne SQL Data Warehouse Ottiene gli utili di compressione x mediamente 5 sull'archiviazione tradizionale orientata per riga e fino a 10 volte e miglioramenti delle prestazioni più query. Query Analitica che è necessario eseguire l'analisi di un numero elevato di lavoro righe perfettamente su indici columnstore.


## <a name="predictable-and-scalable-performance"></a>Prestazioni prevedibili e scalabilità

SQL Data Warehouse separa lo spazio di archiviazione e di calcolo, che consente al scalare in modo indipendente. SQL Data Warehouse possono scalare semplice e veloce per aggiungere risorse ampliare in un preavviso. Integrazione questo è l'uso di archiviazione Blob Azure. BLOB prevedono non solo stabile, replicato lo spazio di archiviazione, ma anche l'infrastruttura di espansione facili basso costo. Con questa combinazione di spazio di archiviazione cloud scala e calcolo Azure, SQL Data Warehouse consente di acquistare spazio di archiviazione e le prestazioni delle query quando è necessario. Modificare la quantità di calcolo è semplice come lo spostamento di un dispositivo di scorrimento nel portale di Azure a sinistra o a destra o può inoltre pianificata utilizzando Transact-SQL e PowerShell.

Oltre alla possibilità di controllare completamente la quantità di elaborazione indipendente dello spazio di archiviazione, SQL Data Warehouse consente di sospendere completamente data warehouse, ovvero che non pagare elaborazione quando non è necessario. Mantenendo lo spazio di archiviazione sul posto, calcolo tutti viene rilasciata nel pool principale di Azure, riducendo i costi. Se necessario, è sufficiente riprendere il calcolo e disporre i dati e calcolare disponibili per il carico di lavoro.

## <a name="data-warehouse-units"></a>Unità di magazzino dati

Assegnazione delle risorse per la Data Warehouse SQL viene misurato in unità di magazzino dati (DWUs). DWUs è una misura delle risorse sottostante come CPU e memoria, IOPS, allocata per il Data Warehouse SQL. Aumento del numero di DWUs aumenta risorse e le prestazioni. In particolare, DWUs contribuire a garantire che:

- In grado di scalabilità data warehouse, senza dover conoscere i componenti hardware o software in.

- È possibile prevedere miglioramento delle prestazioni per un livello DWU prima di modificare le dimensioni del data warehouse.

- Modificare o spostare senza influire sulle prestazioni il carico di lavoro di hardware e software dell'istanza sottostante.

- Microsoft è possibile apportare modifiche all'architettura sottostante del servizio senza influire sulle prestazioni del carico di lavoro.

- Microsoft rapidamente migliorare le prestazioni in SQL Data Warehouse, in modo da scalabilità e in modo uniforme gli effetti del sistema.

Unità di magazzino dati offrono una misura di tre metriche precisione altamente correlate con data warehouse le prestazioni di carico di lavoro. L'obiettivo è che le seguenti metriche chiave carico di lavoro verranno ridimensionata in modo lineare con DWUs che si è scelto per l'archivio di dati.

**Analisi/aggregazione:** Questa metrica carico di lavoro ha una standard data warehouse query che analizza un numero elevato di righe e quindi esegue un'aggregazione complessa. Questa è un'operazione in modo intensivo i/o e CPU.

**Carico:** Questa metrica misura la possibilità di acquisizione di dati nel servizio. Carica vengono completate con PolyBase il caricamento di un set di dati representative dallo spazio di archiviazione Blob Azure. Questa metrica è progettata per sottolineare rete e aspetti della CPU del servizio.

**Creare una tabella come selezionare (CTAS):** CTAS misura la possibilità di copiare una tabella. Questo comporta si leggono dati dallo spazio di archiviazione, la distribuzione tra i nodi dell'accessorio e scrivere nuovamente allo spazio di archiviazione. È un'operazione in modo intensivo CPU, IO e di rete.

## <a name="pause-and-scale-on-demand"></a>Pausa e scala su richiesta

Quando è necessario più velocemente dei risultati, aumentare il DWUs e pagare prestazioni più elevate. Quando è necessario meno calcolare power, ridurre il DWUs, pagare solo per le informazioni necessarie. Si potrebbe pensare sulla modifica del DWUs in questi scenari:

- Quando non è necessario eseguire query, ad esempio nel sera o i fine settimana, disattivare gradualmente le query. Sospendi le risorse di elaborazione per evitare acquistati DWUs quando non sono necessari.

- Quando il sistema ha richiesta bassa, provare a ridurre DWU di dimensioni ridotte. È ancora possibile accedere ai dati, ma con un notevole risparmio.

- Quando si esegue un'operazione di caricamento o una trasformazione dati elevato, può essere necessario scalare in modo che i dati sono disponibili più rapidamente.

Per capire che cos'è il valore DWU ideale, provare il ridimensionamento alto e verso il basso e l'esecuzione di query alcuni dopo il caricamento dei dati. Poiché il ridimensionamento è veloce, è possibile provare un numero di livelli diversi di prestazioni in un'ora o meno.  Tenere presente che SQL Data Warehouse è progettato per elaborare grandi quantità di dati e visualizzare le relative funzionalità true per il ridimensionamento, in particolare in scala maggiore che offerti è consigliabile usare un set di dati di grandi dimensioni che si avvicina o è maggiore di 1 TB.


## <a name="built-on-sql-server"></a>Basata su SQL Server

SQL Data Warehouse dipende dal motore di database relazionali di SQL Server e include molte delle caratteristiche atteso da un data warehouse aziendale. Se si conosce già T-SQL, è facile trasferire la conoscenza SQL Data Warehouse. Se avanzate o ancora, gli esempi attraverso la documentazione aiuta a iniziare. In generale, è possibile pensare sul modo in cui è stata creata gli elementi del linguaggio di SQL Data Warehouse come indicato di seguito:

- SQL Data Warehouse utilizza la sintassi SQL T molte operazioni. Supporta anche una vasta gamma di costrutti SQL tradizionale, ad esempio stored procedure, funzioni definite dall'utente, partizione delle tabelle, indici e le regole di confronto.

- SQL Data Warehouse contiene anche un numero di funzionalità più recenti di SQL Server, tra cui: raggruppate **columnstore** , PolyBase integrazione, dati e indici di controllo (completo di valutazione rischio).

- Alcuni elementi del linguaggio Transact-SQL che sono meno comuni per data warehouse carichi di lavoro o sono più recenti per SQL Server potrebbero non essere disponibili. Per ulteriori informazioni, vedere la [documentazione di migrazione][].

Con compatibilità Transact-SQL e funzionalità tra SQL Server, SQL Data Warehouse, Database SQL e il sistema di piattaforma Analitica, è possibile sviluppare una soluzione in base alle proprie esigenze di dati. È possibile decidere dove mantenere i dati in base alle prestazioni, sicurezza e scala requisiti e quindi trasferire i dati in base alle esigenze tra sistemi diversi.

## <a name="data-protection"></a>Protezione dei dati

SQL Data Warehouse archivia tutti i dati in un archivio locale ridondante Azure Premium. Icona del copie dei dati vengono mantenute nel centro dati locali per garantire la protezione dei dati trasparente in caso di errori localizzati. Inoltre, SQL Data Warehouse automaticamente il backup del database (riavviati) attivi a intervalli regolari tramite snapshot di spazio di archiviazione Azure. Per ulteriori informazioni su come backup e ripristino funziona, vedere [Panoramica di Backup e ripristino][].

## <a name="integrated-with-microsoft-tools"></a>Integrato con strumenti di Microsoft

SQL Data Warehouse si integra anche molti degli strumenti di SQL Server gli utenti potrebbero avere familiari con. Sono inclusi:

**Strumenti di SQL Server tradizionale:** SQL Data Warehouse è completamente integrato con SQL Server Analysis Services, servizi di integrazione e Reporting Services.

**Strumenti basato sul cloud:** SQL Data Warehouse può essere utilizzato insieme a un numero di nuovi strumenti di Azure, inclusi Data Factory, flusso Analitica, apprendimento e Power BI. Per un elenco più completo, vedere [Panoramica di strumenti integrati][].

**Strumenti di terze parti:** Un numero elevato di provider di strumenti di terze parti è certificati integrazione dei propri strumenti con SQL Data Warehouse. Per un elenco completo, vedere [SQL Data Warehouse soluzione partner][].

## <a name="hybrid-data-sources-scenarios"></a>Scenari di origini dati ibrido

Usare SQL Data Warehouse con PolyBase consente agli utenti senza precedenti per spostare i dati relativi ecosistema, sbloccare la possibilità di configurare scenari avanzati ibrido con non relazionali e origini dati locali.

Polybase consente di sfruttare i dati provenienti da origini diverse utilizzando i comandi T-SQL comuni. Polybase consente di eseguire query sui dati non relazionali contenuti nell'archiviazione Blob Azure come se si trattasse di una tabella normale. Utilizzare Polybase eseguire query sui dati non relazionali o per importare i dati non relazionali in SQL Data Warehouse.

- PolyBase utilizza tabelle esterne per accedere ai dati non relazionali. Le definizioni delle tabelle vengono archiviate in SQL Data Warehouse, è possibile accedervi utilizzando SQL e strumenti di come si accede a dati relazionali normale.

- Polybase è indipendente nella sua integrazione. Vengono esposte le caratteristiche e funzionalità per tutte le origini che supporta la stessa. Dati letti da Polybase possono essere in diversi formati, ad esempio file delimitati o file ORCO.

- PolyBase può essere utilizzato per accedere all'archiviazione blob utilizzato anche come spazio di archiviazione per un cluster di HDInsight. Consente di accedere agli stessi dati con strumenti non relazionali e relazionali.

## <a name="sla"></a>CONTRATTO DI SERVIZIO

SQL Data Warehouse offre un prodotto livello contratto servizio () come parte del contratto di servizio di Microsoft Online Services. Per ulteriori informazioni, visitare il [contratto di servizio per SQL Data Warehouse][]. Per informazioni contratto di servizio su tutti gli altri prodotti è possibile visitare Azure [I contratti di servizio] di pagina o scaricarli nella pagina [Contratti multilicenza][] . 

## <a name="next-steps"></a>Passaggi successivi

Dopo aver scoperto un po' su SQL Data Warehouse, informazioni su come rapidamente [creare SQL Data Warehouse][] e [caricare dati di esempio][]. Se ha familiarità con Azure, potrebbero risultare [glossario Azure][] utili quando si verificano nuovi termini. In alternativa, esaminiamo alcune di queste altre risorse Warehouse dati SQL.  

- [Storie di successo]
- [Blog]
- [Richieste di funzionalità]
- [Video]
- [Blog del Team avviso cliente]
- [Creare ticket di supporto]
- [Forum MSDN]
- [Forum di Overflow dello stack]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Creare ticket di supporto]: ./sql-data-warehouse-get-started-create-support-ticket.md
[caricare dati di esempio]: ./sql-data-warehouse-load-sample-databases.md
[creare un Data Warehouse SQL]: ./sql-data-warehouse-get-started-provision.md
[Documentazione di migrazione]: ./sql-data-warehouse-overview-migrate.md
[Partner di soluzioni SQL Data Warehouse]: ./sql-data-warehouse-partner-business-intelligence.md
[Panoramica degli strumenti integrata]: ./sql-data-warehouse-overview-integrate.md
[Panoramica di backup e ripristino]: ./sql-data-warehouse-restore-database-overview.md
[Glossario di Azure]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Storie di successo]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
[Blog]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blog del Team avviso cliente]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Richieste di funzionalità]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Forum di Overflow dello stack]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[Contratto di servizio per SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Contratti multilicenza]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Contratti di servizio]: https://azure.microsoft.com/en-us/support/legal/sla/
