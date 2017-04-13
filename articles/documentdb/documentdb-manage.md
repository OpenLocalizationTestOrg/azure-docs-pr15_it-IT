<properties
    pageTitle="DocumentDB lo spazio di archiviazione e le prestazioni | Microsoft Azure" 
    description="Informazioni sull'archiviazione dei dati e archiviazione di documenti in DocumentDB e come è possibile ridimensionare DocumentDB per soddisfare le esigenze di capacità dell'applicazione." 
    keywords="archiviazione dei documenti"
    services="documentdb" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/18/2016" 
    ms.author="syamk"/>

# <a name="learn-about-storage-and-predictable-performance-provisioning-in-documentdb"></a>Ulteriori informazioni sull'archiviazione e le prestazioni prevedibili il provisioning in DocumentDB
Azure DocumentDB è un completamente gestita scalable orientati al documento NoSQL database servizio per i documenti JSON. Con DocumentDB, non è necessario in locazione macchine virtuali, la distribuzione del software o eseguire il monitoraggio database. DocumentDB è gestito e costantemente dai tecnici Microsoft per offrire disponibilità di classe del mondo, prestazioni e protezione dei dati.  

Per iniziare con un [database DocumentDB](documentdb-create-database.md) tramite il [portale di Azure](https://portal.azure.com/)e DocumentDB mediante la [creazione di un account del database](documentdb-create-account.md) . Database DocumentDB sono disponibili in unità di misura delle unità (SSD) sottoposta lo spazio di archiviazione e la velocità effettiva. Queste unità di spazio di archiviazione vengono effettuato il provisioning mediante la [creazione di insiemi di database](documentdb-create-collection.md) nel proprio account di database, ogni raccolta fino a riservate che possono essere ridimensionati verso l'alto o verso il basso in qualsiasi momento per soddisfare le esigenze dell'applicazione. 

Se l'applicazione supera la velocità riservata per una o più raccolte, richieste è limitata alla scala cronologica per ogni raccolta. Questo errore indica che alcune richieste di applicazione potrebbero esito positivo mentre gli altri utenti possono essere limitati.

In questo articolo viene fornita una panoramica delle risorse e metriche disponibili per gestire la capacità e pianificare spazio di archiviazione di dati. 

## <a name="database-account"></a>Account del database
Come sottoscrittore di Azure, è possibile effettuare il provisioning di uno o più account di database DocumentDB per gestire le risorse del database. Ciascuna sottoscrizione è associata a una singola sottoscrizione Azure. 

Account DocumentDB possono essere create tramite il [portale di Azure](documentdb-create-account.md)o utilizzando [un modello ARM o CLI Azure](documentdb-automation-resource-manager-cli.md).

## <a name="databases"></a>Database
Un solo database DocumentDB può contenere praticamente una quantità illimitata di spazio di archiviazione di documenti raggruppato in raccolte. Raccolte prestazioni isolare - può effettuare il provisioning di ogni raccolta fino a che non vengono condivisi con altre raccolte stesso database dell'account. Un database DocumentDB è flessibile dimensioni, che vanno dal GB per lo spazio di archiviazione di questo servizio di SSD eseguito il documento e la velocità effettiva provisioning. A differenza di un database RDBMS tradizionale un database in DocumentDB non è definito per un singolo computer e può comprendere più computer o cluster.  

Con DocumentDB, che è necessario ridimensionare le applicazioni, è possibile creare ulteriori raccolte o database o entrambe. Tramite il [portale di Azure](documentdb-create-database.md) o una qualsiasi delle [DocumentDB SDK](documentdb-dotnet-samples.md), è possono creare i database.   

## <a name="database-collections"></a>Insiemi di database
Ogni database di DocumentDB può contenere uno o più raccolte. Le raccolte funzionano come partizioni dati altamente disponibili per l'archiviazione di documenti ed elaborazione. Ogni raccolta consentono di memorizzare i documenti contenenti schema eterogeneo. Indicizzazione automatica del DocumentDB e funzionalità di query consentono di filtrare e recuperare documenti facilmente. Una raccolta fornisce l'ambito per l'esecuzione di query e lo spazio di archiviazione di documenti. Una raccolta è anche un dominio transazione per tutti i documenti in essa contenuti. Le raccolte vengono assegnate velocità in base al valore impostato nel portale di Azure oppure tramite il SDK. 

Le raccolte vengono automaticamente suddivise in uno o più server fisici da DocumentDB. Quando si crea una raccolta, è possibile specificare la velocità di provisioning in termini di unità richiesta al secondo e una proprietà chiave partizione. Il valore di questa proprietà viene utilizzato da DocumentDB per distribuire documenti tra partizioni e indirizzare le richieste come query. Il valore di chiave partizione funge anche dal limite di transazione per stored procedure e trigger. Ogni raccolta è presente una quantità riservata di velocità specifico a tale raccolta, non vengono condivisi con altre raccolte nello stesso account. Di conseguenza, è possibile ridimensionare dell'applicazione in termini di spazio di archiviazione e la velocità di trasmissione. 

È possibile creare raccolte tramite il [portale di Azure](documentdb-create-collection.md) o una qualsiasi delle [DocumentDB SDK](documentdb-sdk-dotnet.md).   
 
## <a name="request-units-and-database-operations"></a>Richiedere l'unità e operazioni di database

Quando si crea una raccolta, si riserva velocità in termini di [unità richiesta (RU)](documentdb-request-units.md) al secondo. Invece di pensa e gestione delle risorse hardware, è possibile pensare di un' **unità richiesta** come una singola misura per le risorse necessarie per una richiesta di applicazione di servizio ed eseguire varie operazioni di database. Lettura di un documento di 1 KB utilizza la stessa 1 RU indipendentemente dal numero di elementi archiviati nella raccolta oppure il numero di richieste simultanee in esecuzione allo stesso. Tutte le richieste contro DocumentDB, incluse operazioni complesse query SQL ha un valore RU prevedibile che può essere determinato in fase di sviluppo. Se si conosce la dimensione dei documenti e la frequenza di ogni operazione (lettura, scrittura e query) per il supporto per l'applicazione, è possibile eseguire il provisioning l'importo esatto di trasmissione in base alle esigenze dell'applicazione e ridimensionare il database su e giù come le prestazioni alle esigenze. 

È possibile prenotare ogni raccolta fino a in blocchi di 100 unità richiesta al secondo da centinaia fino a milioni di unità richiesta al secondo. È possibile regolare la velocità di provisioning per tutta la durata di una raccolta per adattarsi alle esigenze di elaborazione e accedere ai modelli dell'applicazione. Per ulteriori informazioni, vedere [livelli di prestazioni DocumentDB](documentdb-performance-levels.md). 

>[AZURE.IMPORTANT] Raccolte sono entità fatturabile. Il costo è la velocità di provisioning della raccolta misurata in unità richiesta al secondo insieme di spazio di archiviazione totale consumata in gigabyte. 

Il numero di unità richiesta utilizzerà una determinata operazione come Inserisci, Elimina, query o esecuzione di stored procedure? Un'unità richiesta è una misura normalizzata della richiesta l'elaborazione di costo. Lettura di un documento di 1 KB è 1 RU, ma una richiesta di inserire, sostituire o eliminare lo stesso documento utilizzerà ulteriori elaborazioni dal servizio e di conseguenza più unità di richiesta. Ogni risposta dal servizio include un'intestazione personalizzata (`x-ms-request-charge`) che restituisce l'unità richiesta utilizzato per la richiesta. Questa intestazione è possibile accedere tramite [SDK](documentdb-sdk-dotnet.md). In .NET SDK [RequestCharge](https://msdn.microsoft.com/library/azure/dn933057.aspx#P:Microsoft.Azure.Documents.Client.ResourceResponse`1.RequestCharge) è una proprietà dell'oggetto [ResourceResponse](https://msdn.microsoft.com/library/azure/dn799209.aspx) . Se si vuole valutare le esigenze di velocità prima di effettuare una chiamata singola, è possibile utilizzare la [pianificazione delle capacità](documentdb-request-units.md#estimating-throughput-needs) per agevolare la stima. 

>[AZURE.NOTE] La previsione di 1 unità richiesta per un documento di 1 KB corrisponde a un semplice GET del documento con [Coerenza sessione](documentdb-consistency-levels.md). 

Esistono vari fattori che influiscono su unità richiesta utilizzato per un'operazione con un account di database DocumentDB. Questi fattori:

- Dimensioni del documento. Come le dimensioni di documento aumentano le unità utilizzate per leggere o scrivere che i dati anche aumenterà.
- Conteggio di proprietà. Presupponendo che l'indicizzazione predefinita di tutte le proprietà, le unità utilizzate per scrivere un documento ne aumenterà mentre aumenta il numero di proprietà.
- Coerenza dei dati. Quando si usa livelli la coerenza dei dati di sicuro o delimitata obsolescenza, unità aggiuntive verranno utilizzate per leggere i documenti.
- Proprietà indicizzate. Un criterio di indice in ogni raccolta determina quali proprietà siano indicizzati per impostazione predefinita. È possibile ridurre il consumo di unità richiesta limitando il numero di proprietà indicizzate. 
- Documento di indicizzazione. Per impostazione predefinita che verrà indicizzato automaticamente ogni documento, si dovrà utilizzare meno unità richiesta se non si desidera indicizzare alcuni dei documenti.

Per ulteriori informazioni, vedere [unità richiesta DocumentDB](documentdb-request-units.md). 

Ecco, ad esempio, una tabella che mostra il numero di unità richiesta effettuare il provisioning di tre dimensioni di vari documenti (1KB, 4KB e 64KB) e due livelli di prestazioni diversi (500 legge/secondo 100 scrive/secondo e 500 legge/secondo + 500 scrive/secondo). La coerenza dei dati è stato configurato in sessione e i criteri di indicizzazione è stato impostato su Nessuno.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Dimensione documento</strong></p></td>
            <td valign="top"><p><strong>Lettura/secondo</strong></p></td>
            <td valign="top"><p><strong>Scrittura/secondo</strong></p></td>
            <td valign="top"><p><strong>Richiedere l'unità di misura</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1) + (100* 5) = 1.000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *5) + (100* 5) = 3.000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1.3) + (100* 7) = 1,350 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *1.3) + (500* 7) = 4,150 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *10) + (100* 48) = 9,800 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *10) + (500* 48) = 29,000 RU/s</p></td>
        </tr>
    </tbody>
</table>

Le query, stored procedure e trigger occupare unità richiesta in base alla complessità delle operazioni eseguite. Quando si sviluppa l'applicazione, esaminare l'intestazione addebito richiesta per comprendere meglio come ogni operazione è costituita richiesta unità capacità.  


## <a name="choice-of-consistency-level-and-throughput"></a>Scelta di livello la coerenza e la velocità di trasmissione
La scelta del livello di coerenza predefinito ha un impatto sulla velocità e la latenza. È possibile impostare il livello di coerenza predefinito sia a livello di programmazione che tramite il portale di Azure. È anche possibile ignorare il livello di coerenza alla scala cronologica per ogni richiesta. Per impostazione predefinita, il livello di coerenza è impostato su **sessione**, che fornisce monotona di lettura/scrittura e leggere le garanzie di scrittura. La coerenza sessione ideale per applicazioni basate su utente e fornisce un equilibrio perfetto della coerenza e prestazioni compromessi.    

Per istruzioni su come modificare il livello di coerenza nel portale di Azure, vedere [come gestire un DocumentDB Account](documentdb-manage-account.md#consistency). In alternativa, per ulteriori informazioni sui livelli di coerenza, vedere [livelli di usare la coerenza](documentdb-consistency-levels.md).

## <a name="provisioned-document-storage-and-index-overhead"></a>Sovraccarico di spazio di archiviazione e l'indice di provisioning documento
DocumentDB supporta la creazione delle raccolte singola partizione e partizionate. Ogni partizione DocumentDB supporta fino a 10 GB di spazio di archiviazione SSD eseguito il backup. 10GB di spazio di archiviazione di documenti include i documenti più spazio di archiviazione per l'indice. Per impostazione predefinita, una raccolta di DocumentDB è configurata per indicizzare automaticamente tutti i documenti senza in modo esplicito qualsiasi indici secondari o schema. In base alle applicazioni che utilizzano DocumentDB, il sovraccarico di indice tipica è compresa tra 2-20%. La tecnologia indicizzazione utilizzata da DocumentDB garantisce che, indipendentemente dai valori delle proprietà, il sovraccarico di indice non superi superiore all'80% delle dimensioni dei documenti con le impostazioni predefinite. 

Per impostazione predefinita tutti i documenti vengono indicizzati dal DocumentDB automaticamente. Tuttavia, se si desidera ottimizzare il sovraccarico di indice, è possibile rimuovere alcuni documenti dall'indicizzazione al momento dell'inserimento o sostituzione di un documento, come descritto nelle [regole di indicizzazione DocumentDB](documentdb-indexing-policies.md). È possibile configurare una raccolta di DocumentDB per escludere tutti i documenti all'interno della raccolta dall'indicizzazione. È inoltre possibile configurare una raccolta di DocumentDB per l'indice in modo selettivo solo determinate proprietà o percorsi con caratteri jolly dei documenti JSON, come descritto nella [configurazione dei criteri di indicizzazione di una raccolta](documentdb-indexing-policies.md#configuring-the-indexing-policy-of-a-collection). Esclusione dei documenti o proprietà migliora la velocità di scrittura, che indica che verrà utilizzato meno unità richiesta.   

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul funzionamento del DocumentDB, vedere [partizionamento e proporzioni dei caratteri in Azure DocumentDB](documentdb-partition-data.md).

Per ulteriori informazioni sul monitoraggio delle prestazioni nel portale di Azure, vedere [Monitor di un account DocumentDB](documentdb-monitor-accounts.md). Per ulteriori informazioni sulla scelta delle prestazioni per le raccolte, vedere [livelli di prestazioni in DocumentDB](documentdb-performance-levels.md).
 
