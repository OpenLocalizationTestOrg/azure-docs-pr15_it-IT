<properties
   pageTitle="Applicazione web Scalable | Architettura di riferimento Azure | Microsoft Azure"
   description="Migliorare la scalabilità in un'applicazione web che esegue Microsoft Azure."
   services="app-service,app-service\web,sql-database"
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/12/2016"
   ms.author="mwasson"/>


# <a name="improving-scalability-in-a-web-application"></a>Migliorare la scalabilità in un'applicazione web 

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Questo articolo descrive un'architettura consigliata per migliorare le prestazioni in un'applicazione web in esecuzione in Microsoft Azure e scalabilità. L'architettura si basa su [architettura di riferimento Azure: applicazione web base][basic-web-app]. I suggerimenti e le considerazioni da tale articolo applicano a questa architettura anche.

>[AZURE.NOTE] Azure include due diversi modelli di distribuzione: Gestione risorse e classica. In questo articolo utilizza Manager delle risorse, Microsoft consiglia per le distribuzioni di nuove.

## <a name="architecture-diagram"></a>Diagramma dell'architettura

![[0]][0]

L'architettura è i seguenti componenti:

- **Gruppo di risorse**. Un [gruppo di risorse] [ resource-group] è un contenitore logico per le risorse Azure. 

- ** [Web app] [ app-service-web-app] ** e ** [API app][app-service-api-app]**. Una tipica applicazione moderna potrebbe contenere un sito Web e uno o più web REST API. API web potrebbe essere utilizzato dai browser client tramite AJAX, dalle applicazioni client nativo o dalle applicazioni sul lato server. Considerazioni sulla progettazione web API, per vedere [indicazioni per la progettazione dell'API][api-guidance].    

- **WebJob**. Utilizzare [Azure WebJobs] [ webjobs] per eseguire attività lunga in background. WebJobs supporti l'esecuzione di una programmazione aggiunge, o in risposta a un trigger, ad esempio l'inserimento di un messaggio in una coda. Un WebJob viene eseguita in background nel contesto di un'applicazione di servizio di App. 

- **Coda**. Architettura illustrato di seguito, code applicazione attività in background inserendo un messaggio in un' [Archiviazione coda Azure] [ queue-storage] coda. Il messaggio è attiva una funzione di WebJob. 

    In alternativa, è possibile utilizzare code Bus di servizio. Per un confronto, vedere [code Azure e servizio Bus - compared e rispetto][queues-compared].

- **Cache**. Archiviare dati semistrutturati statici nella [Cache Redis Azure][azure-redis].  

- **Rete CDN**. Utilizzare [Azure Content Delivery Network] [ azure-cdn] (CDN) per il contenuto disponibile al pubblico cache, latenza inferiore e processore della distribuzione del contenuto.

- **Archiviazione dei dati.** Utilizzare [Il Database di SQL Azure] [ sql-db] per dati relazionali. Per i dati non relazionali, è consigliabile un punto vendita NoSQL, ad esempio archivio tabelle Azure o [DocumentDB][documentdb].

- **Ricerca di azure**. Utilizzare la [Ricerca di Azure] [ azure-search] per aggiungere funzionalità di ricerca, inclusi i suggerimenti di ricerca, Cerca sfocata e ricerca specifiche della lingua. Ricerca Azure in genere viene utilizzato in combinazione con un altro archivio di dati, in particolare se l'archivio dati primario richiede la coerenza strict. In questo approccio, da dati rilevanti in altro archivio di dati e inserire l'indice di ricerca in ricerca Azure. Ricerca Azure può anche essere utilizzato per consolidare un indice di ricerca singola da più archivi dati.  

- **Messaggio di posta elettronica o SMS**. Se l'applicazione di inviare posta elettronica o un messaggio SMS, utilizzare un servizio di terze parti, ad esempio SendGrid o Twilio, anziché la creazione di questa funzionalità direttamente nell'applicazione.

## <a name="recommendations"></a>Consigli

### <a name="app-service-apps"></a>Applicazioni di servizio di App 

È consigliabile creare l'applicazione web e l'API web come le applicazioni di servizio di App separate. Questo modello consente di eseguirli nei piani di servizio App separati, che a sua volta consente di scalare loro in modo indipendente. Se non è necessario quel livello di scalabilità nella prima di tutto, è possibile distribuire le applicazioni nello stesso piano e spostarli in piani separati in seguito, se necessario. (Per i piani di base, Standard e Premium vengono fatturati le istanze di macchine Virtuali nel piano, non per app. Vedere [servizio App prezzi][app-service-pricing])

Se si prevede di utilizzare le *Tabelle semplici* o le caratteristiche di *Facile API* dell'App Mobile servizio App, creare un'applicazione di servizio App separata a tale scopo.  Queste caratteristiche si basano su un framework applicazione specifica per consentire loro.

### <a name="webjobs"></a>WebJobs

Se il WebJob risorse, è possibile distribuire a un'applicazione di servizio App vuota all'interno di un piano di servizio App separato, per fornire istanze dedicate per la WebJob. Vedere [Guida di processi in Background][webjobs-guidance].  

### <a name="cache"></a>Cache

È possibile migliorare le prestazioni e scalabilità utilizzando [Azure Redis Cache] [ azure-redis] per alcuni dati nella cache. Considerare l'utilizzo della Cache Redis per:

- Dati delle transazioni semistrutturati statico.

- Stato della sessione.

- Output HTML. Può essere utile nelle applicazioni che il rendering di output HTML complessi. 

Per ulteriori informazioni sulla progettazione di una strategia di memorizzazione nella cache, vedere [memorizzazione nella cache indicazioni][caching-guidance].

### <a name="cdn"></a>RETE CDN 

Usare [Azure CDN] [ azure-cdn] in contenuto statico cache. Il vantaggio di una rete CDN è ridurre la latenza per gli utenti, poiché viene inserito nella cache contenuto in un *server perimetrale* limitazioni geografiche Chiudi per l'utente. CDN possibile anche ridurre carico sull'applicazione, perché il traffico non vengono gestito dall'applicazione.

- Se l'app è costituita prevalentemente pagine statiche, è preferibile CDN per memorizzare nella cache l'intero app. Vedere [usare CDN Azure nel servizio App Azure][cdn-app-service].

- In caso contrario, mettere in contenuto statico, ad esempio immagini, CSS e HTML file, in archiviazione Azure e usare CDN per i file memorizzati nella cache. Vedere [integrare un Account di archiviazione con CDN][cdn-storage-account].

> [AZURE.NOTE] Azure CDN non è possibile presentare contenuto che richiede l'autenticazione.

Per istruzioni dettagliate, vedere [indicazioni per la rete di recapito contenuti (CDN)][cdn-guidance]. 

### <a name="storage"></a>Spazio di archiviazione

Applicazioni moderne spesso elaborano grandi quantità di dati. Per ridimensionare per il cloud, è importante scegliere il tipo di archiviazione destra. Ecco alcuni suggerimenti previsto.  Per istruzioni dettagliate, vedere [La valutazione delle funzionalità di archivio dati per le soluzioni persistenza Polyglot][polyglot-storage].

Cosa si desidera memorizzare | Esempio | Archiviazione consigliata
--- | --- | ---
File | Immagini, documenti, file PDF | Archiviazione Blob Azure
Coppie di parole chiave/valore | Dati dei profili utente cercato dalla ID utente | Archivio tabelle Azure
Messaggi brevi devono attivare ulteriori elaborazioni | Richieste di ordini | Archiviazione coda Azure, servizio Bus coda o servizio Bus argomento
Dati non relazionali, con uno schema flessibile, che richiede l'esecuzione di query base | Catalogo di prodotti | Database di documento, ad esempio DocumentDB Azure, MongoDB o Apache CouchDB
Dati relazionali, richiedere supporto avanzato di query, schema restrittive e/o la coerenza sicura | Inventario dei prodotti | Database SQL Azure

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

### <a name="app-service-app"></a>Applicazione di servizio di App

Se la soluzione include più applicazioni di servizio App, è consigliabile distribuirle per separare i piani di servizio di App. Questo approccio consente di ridimensionare loro in modo indipendente, poiché vengono eseguiti in istanze separate. Per ulteriori informazioni sulla scalabilità, vedere le [Considerazioni sulla scalabilità] [ basic-web-app-scalability] sezione [architettura delle applicazioni web base][basic-web-app].

Allo stesso modo, valutare la possibilità di inserire un WebJob nel proprio piano, in modo che le attività in background non vengono eseguite nelle stesse istanze che gestiscono le richieste HTTP.  

### <a name="sql-database"></a>Database SQL

Aumentare la scalabilità di un database SQL di *sharding* il database &mdash; , ovvero partizioni orizzontalmente il database. Sharding consente di scalare orizzontalmente, il database utilizzando [strumenti Database flessibile][sql-elastic]. Vantaggi potenziali di sharding:

- Migliore velocità effettiva delle transazioni.

- Query consente di velocizzare esecuzione su un sottoinsieme dei dati. 

### <a name="azure-search"></a>Ricerca di Azure

Ricerca Azure rimuove il sovraccarico di esecuzione di ricerche di dati complessi dall'archivio dati principale, che possono essere personalizzate per gestire carico. Vedere [livelli di risorse scala per la query e indicizzazione i carichi di lavoro di Azure ricerca][azure-search-scaling].

## <a name="security-considerations"></a>Considerazioni sulla protezione

### <a name="cross-origin-resource-sharing-cors"></a>Croce origine condivisore (CORS)

Se si crea un sito Web e dell'API web come App separato, il sito Web non è possibile effettuare chiamate AJAX lato client l'API a meno che non si attiva CORS. 

> [AZURE.NOTE] Protezione del browser impedisce a una pagina web di effettuare richieste AJAX a un altro dominio. Questa limitazione si chiama il criterio della stessa origine e impedisce a un sito di si leggono dati sentitive da un altro sito. CORS è uno standard W3C che consente a un server per ridurre il criterio della stessa origine e consentire alcune richieste tra origine durante il rifiuto di altri utenti.

Servizi di App è incorporato il supporto per CORS, senza dover scrivere codice dell'applicazione. Vedere [consumo un'app di API da JavaScript utilizzando CORS][cors]. Aggiungere il sito Web all'elenco delle origini consentiti per l'API. 

### <a name="sql-database-encryption"></a>Crittografia del Database SQL

Utilizzare [La crittografia trasparente] [ sql-encryption] se è necessario crittografare i dati al resto del database. Questa caratteristica consente di eseguire in tempo reale crittografia e decrittografia di un intero database, inclusi l'esecuzione di backup e file di registro delle transazioni, senza apportare modifiche all'applicazione. Crittografia aggiungere alcuni latenza, pertanto è buona norma per separare i dati che devono essere protetto in un database e abilitare la crittografia solo per il database.  

## <a name="next-steps"></a>Passaggi successivi

- Per garantire una maggiore disponibilità, distribuire l'applicazione in più aree e gestione di [traffico Azure] [ tm] per failover. Per ulteriori informazioni, vedere [architettura di riferimento Azure: applicazione Web con disponibilità][web-app-multi-region].    

<!-- links -->

[api-guidance]: ../best-practices-api-design.md
[app-service-web-app]: ../app-service-web/app-service-web-overview.md
[app-service-api-app]: ../app-service-api/app-service-api-apps-why-best-platform.md
[app-service-pricing]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[azure-cdn]: https://azure.microsoft.com/en-us/services/cdn/
[azure-redis]: https://azure.microsoft.com/en-us/services/cache/
[azure-search]: https://azure.microsoft.com/en-us/documentation/services/search/
[azure-search-scaling]: ../search/search-capacity-planning.md
[background-jobs]: ../best-practices-background-jobs.md
[basic-web-app]: guidance-web-apps-basic.md
[basic-web-app-scalability]: guidance-web-apps-basic.md#scalability-considerations 
[caching-guidance]: ../best-practices-caching.md
[cdn-app-service]: ../app-service-web/cdn-websites-with-cdn.md
[cdn-storage-account]: ../cdn/cdn-create-a-storage-account-with-cdn.md
[cdn-guidance]: ../best-practices-cdn.md
[cors]: ../app-service-api/app-service-api-cors-consume-javascript.md
[documentdb]: https://azure.microsoft.com/en-us/documentation/services/documentdb/
[polyglot-storage]: https://github.com/mspnp/azure-guidance/blob/master/Polyglot-Solutions.md
[queue-storage]: ../storage/storage-dotnet-how-to-use-queues.md
[queues-compared]: ../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md
[resource-group]: ../resource-group-overview.md
[sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[sql-elastic]: ../sql-database/sql-database-elastic-scale-introduction.md
[sql-encryption]: https://msdn.microsoft.com/en-us/library/dn948096.aspx
[tm]: https://azure.microsoft.com/en-us/services/traffic-manager/
[web-app-multi-region]: ./guidance-web-apps-multi-region.md
[webjobs-guidance]: ../best-practices-background-jobs.md
[webjobs]: ../app-service/app-service-webjobs-readme.md
[0]: ./media/blueprints/paas-web-scalability.png "Applicazione Web in Azure con scalabilità migliorata"