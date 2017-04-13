<properties 
    pageTitle="Azure ricerca sviluppo Case Study: Come WhatToPedia integrato un portale infomedia in Microsoft Azure | Microsoft Azure | Servizio di ricerca cloud ospitato" 
    description="Informazioni su come creare un informazioni portale e metatag motore di ricerca utilizzando la ricerca di Azure, un servizio di ricerca cloud ospitato per gli sviluppatori." 
    services="search, sql-database,  storage, web-sites" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="jhubbard"/>

<tags 
    ms.service="search" 
    ms.devlang="NA" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="search" 
    ms.date="08/29/2016" 
    ms.author="heidist"/>

# <a name="azure-search-developer-case-study"></a>Ricerca Azure sviluppo Case Study

## <a name="how-whattopediacomhttpwhattopediacom-built-an-infomedia-portal-on-microsoft-azure"></a>La modalità di compilazione un portale infomedia [WhatToPedia.com](http://whattopedia.com/) in Microsoft Azure

 ![][6]  &nbsp;&nbsp;&nbsp;  <font size="9">Grande idea</font> 


L'idea consiste nel creare un portale di informazioni che consente di connettersi con rivenditori tramite un'esperienza particolarmente significativo, ambito di ricerca, simile a come viaggio portali corrispondenza turisti backup con alberghi, ristoranti e intrattenimento in caso di territorio esplorato per quanto acquirenti. 

Il portale che è previsione verrà eseguita un'esperienza di ricerca di alta qualità eccezionale su dati rivenditore in un determinato mercato, consentendo acquirenti trovare archivi in base a posizione e altre strutture rivenditore fornisce. Si verrà inizializzare il motore di ricerca con un set di dati iniziale, ma verrà create più approfondita valore nel tempo, con l'aiuto degli abbonati a rivenditore che registra informazioni sulla propria attività. Promozioni, nuovi prodotti, marchi popolari, servizi speciali interni--tutti sono illustrati esempi di dati che aggiunge valore al sito. Questi dati vengono segnalati automatica e integrati corpus ricerca dopo il rivenditore si iscrive come sottoscrittore. Relative alla pubblicità, oltre al modello di abbonamento, fornisce profitti dell'azienda nuovo.

Ricerca sarà il modello di interazione utente principale, su una piattaforma cloud pure. Ai fini della scala e i costi di bassa, quasi tutte le caratteristiche in tal caso dall'esperienza portale al controllo origine, sarà tramite un servizio online. Utilizzando un motore di ricerca che fornisce le caratteristiche occorre all'esterno della casella, è possibile creare rapidamente un'applicazione di ricerca, senza la necessità di creare e gestire un motore di ricerca noi.

## <a name="what-we-built"></a>È stato creato

WhatToPedia è una società infomedia. È stato creato [WhatToPedia.com](http://whattopedia.com/) – - attualmente test mercato in Nord Europa con la data 2 febbraio 2015 live Vai. La base clienti è principalmente negozi mattoni fisici che hanno l'esigenza di presenza in linea accessibile è facile da gestire.

Le attività consiste nel attirare acquirenti grazie a un'esperienza di ricerca online efficace, migliorare i risultati in base sulla città o risorse, marchi, memorizzare i nomi o archiviare tipi. Attirare acquirenti ha un effetto a catena, infatti rivenditori per la sottoscrizione al sito portale. Le sottoscrizioni sono basate su tariffa, conveniente tasso di interesse.

 ![][7] 

Dopo l'iscrizione a un abbonamento, un rivenditore avrà sul profilo esistente (creato personalmente da Contattaci dai dati acquistati), aggiornarla con i dati aggiuntivi relativi promozioni, marchi in primo piano o annunci. Le funzionalità interne, ad esempio le lingue conosciute, valute accettata, sotto gli acquisti, può essere automatica segnalati al meglio attirare acquirenti che cercano tali strutture.

## <a name="who-we-are"></a>Chi siamo

Il mio nome è Thomas Segato (Microsoft Consulting) e ha esito positivo con Jesper Boelling, cliente potenziale sviluppatore WhatToPedia per realizzare la soluzione. 

WhatToPedia è un avvio test marketing sua nuova attività portale in Svezia, in cui la maggior parte dei rivenditori 60.000 inclusi sono mattoni fisici PMI (piccole e medie imprese). Poiché è noto che una persona acquisti europei più lingue ed esegue più valute, creiamo soluzioni che adattato un acquirente multilingua. È necessaria e trovare un motore di ricerca che supporta il nostro requisiti multilingui in Azure ricerca.

Azure è stata applicata un modificatore gioco per il progetto. Prima di disponibilità di ricerca di Azure, sono impiegate energia notevole uso tramite i potenziali problemi di creazione di un motore di ricerca. Visto Azure ricerca come un servizio online rimossa la soluzione di imparare tecnici e amministrativi principali, che consente di gestire accesso al mercato più velocemente e con un'esperienza di ricerca più efficace.  

## <a name="how-we-did-it"></a>Come abbiamo

Il nostro obiettivo era creare un'infrastruttura completa basata su cloud services solo. Microsoft è stato scelto come la piattaforma strategic perché non è stato il provider che offre le necessarie servizi (per la collaborazione e sviluppo), scala su richiesta e prezzi.
 
### <a name="high-level-components"></a>Componenti di alto livello

È stato creato un business, non solo per un sito. Supporto l'intero lavoro necessaria una gamma completa di applicazioni e gli strumenti. Abbiamo adottato Visual Studio e Visual Studio Team Services per lo sviluppo, in linea di Team Foundation Service (TFS) per il codice sorgente e gestione di scrum, Office 365 per la comunicazione e collaborazione, naturalmente Microsoft Azure per tutte le operazioni relative al sito e lo spazio di archiviazione. Con Visual Studio, IDE fornito il provisioning diretto a Azure, con l'integrazione a Online per fornire un produttività aggiuntive.

L'illustrazione seguente mostra i componenti di alto livello utilizzati dell'infrastruttura WhatToPedia.

   ![][8]

### <a name="how-we-use-microsoft-azure"></a>Come si usa Microsoft Azure

Esaminando le caselle di colore verde nel diagramma precedente, si noterà che la soluzione WhatToPedia si basa su questi servizi:

- [Ricerca di Azure](https://azure.microsoft.com/services/search/)
- [Azure siti Web utilizzando MVC 4](https://azure.microsoft.com/services/websites/)
- [Azure WebJobs per le attività pianificate](../app-service-web/websites-webjobs-resources.md)
- [Database SQL Azure](https://azure.microsoft.com/services/sql-database/)
- [Archiviazione BLOB Azure](https://azure.microsoft.com/services/storage/)
- [Recapito della posta elettronica SendGrid](https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

Cuore molto della soluzione è dati e ricerca. Il flusso di dati dal provider di rivenditore al cliente finale viene illustrato di seguito:

  ![][9]

Archivio dati primario è il rivenditore e dati contabili contenuti nel Database di SQL Azure. Si tratta di set di dati iniziale e dati specifici di rivenditore aggiunti nel tempo. Si utilizza un WebJob Azure per la pubblicazione degli aggiornamenti da un Database SQL corpus ricerca nella ricerca Azure.

### <a name="presentation-layer"></a>Livello di presentazione

Il portale è un sito Web di Azure, implementata MVC 4 e [Avvio Twitter](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29). Abbiamo scelto MVC perché offre chiaro in formato HTML di sviluppo ASP.NET basata su moduli. Per evitare di creare App per più dispositivi e gestire più piattaforme di dispositivi mobili, avvio Twitter scelto per il supporto di tutti i dispositivi e piattaforme.

### <a name="authentication-permissions-and-sensitive-data"></a>L'autenticazione, autorizzazioni e dati riservati

Acquirenti esplorazione del sito in forma anonima. Di conseguenza, non esistono requisiti login per acquirenti né viene memorizzato eventuali dati consumer. 

Rivenditori sono un brano diverso. In questo caso, è memorizzata informazioni del profilo pubblico, informazioni di fatturazione e contenuti multimediali che si desiderano visualizzare nel sito. È possibile ottenere un account di accesso utente, utilizzato per eseguire l'autenticazione utente prima di eseguire aggiornamenti al profilo dell'abbonato ogni rivenditore che consente di seguire il sito.  Si archiviano in modo sicuro tutti i dati di sottoscrittore in archiviazione BLOB Azure e Database di SQL Azure.
Abbiamo scelto un modello di autenticazione basato sull'autenticazione basata su moduli .NET. Abbiamo scelto questo approccio per la semplicità; non è stato necessario i ruoli, il supporto dell'interfaccia utente e altre funzionalità estranei forniti con altri approcci. 

Per assicurarsi che i rivenditori visualizzati solo i dati che appartiene a tali, viene creato un rivenditore ID per ogni rivenditore utilizzato successivamente su tutti leggere e scrivere le operazioni relative ai dati specifici rivenditore. Con questo approccio è stato rilevato non occorre concedere le autorizzazioni di database per i rivenditori singoli. Tutti i rivenditori interagiscono con il sistema in un unico ruolo del database, con l'ID rivenditore come il nostro tecnica di isolamento di dati.

Poiché il nostro business è costituita da tutti sugli effetti downstream (per creare maggiore business ai rivenditori, la creazione di incentivi per annunciare e sottoscrivere) poter tracciare una riga di gestione degli acquisti sul web. Di conseguenza, non sono disponibili carrello nel sito, che consente di semplificare i requisiti di sicurezza. 

Un altro semplificazione che abbiamo impiegato è stato affidare le operazioni pagabili account e fatturazione. Per informazioni di pagamento cliente routing direttamente a terze parti ([SveaWebPay](http://www.sveawebpay.se/)) è ridurre i rischi di associazione con l'archiviazione e la protezione dei dati riservati punti dati. 

### <a name="search-engine"></a>Motore di ricerca

Alla base della soluzione è il motore di ricerca basato su servizio di ricerca di Azure. Inizialmente, è stato creato un motore di ricerca personalizzato, ma durante questo processo è realizzato la complessità e sono state molto elevato effettivamente e che viene richiesto di prendere in considerazione altre alternative. 

Funzionalità di base che sono state più importante inclusi:

- Filtri
- Spostamento in base a facet
- Miglioramento dei risultati
- Spostamento AJAX

Una ricerca in internet state introdotte per il video seguente ispirazione di prova ricerca Azure: [Approfondita Europe TechEd](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410) 

Dopo la riproduzione del video, siamo pronti per creare un prototipo in base a quello visualizzato. Perché si dispone già di un modello di dati in MVC, la creazione di prototipo era semplice in quanto i dati contenuti termini che supportano le ricerche ed è stato già utilizzato i requisiti come voler facet, ordinare e filtrare i dati. 

Verrà visualizzata la modalità di compilazione prototipo.

**Configurare il servizio di ricerca Azure**

1. Accesso al portale classica Azure e aggiungere il servizio di ricerca per la sottoscrizione. È stato usato la versione condivisa (gratuita con la sottoscrizione).
2. Creare un indice. Per il prototipo è stato usato il portale dell'interfaccia utente per definire i campi di ricerca e creare i profili di punteggio. Il profilo di punteggio basato su dati sulla posizione: paese | città | indirizzo (vedere: aggiungere profili punteggio).
3. Copiare l'URL del servizio e amministrazione chiave api per i file di configurazione. Il tasto MAIUSC è attivato la pagina del servizio di ricerca nel portale e viene utilizzato per eseguire l'autenticazione al servizio.
    
**Sviluppare un processo dell'indicizzatore ricerca-Console di Windows**

1. Leggere tutti i rivenditori dal database.
2. Chiamare l'API del servizio di ricerca di Azure per caricare i rivenditori uno alla volta (vedere: http://msdn.microsoft.com/library/azure/dn798930.aspx).
3. Impostare una proprietà nel database rivenditore verrà indicizzato per l'indicizzazione incrementale. Abbiamo mediante l'aggiunta di un campo 'indicizzatore' che memorizza lo stato di indice di ogni profilo (indicizzato o meno). 

Vedere Appendice per il frammento di codice che genera il processo dell'indicizzatore.

**Sviluppare un portale Web ricerca – MVC**

1. Chiamare il servizio di ricerca di Azure per visualizzare tutti i documenti dalla ricerca (vedere: http://msdn.microsoft.com/library/azure/dn798927.aspx)
2. Estrarre seguiti dalla risposta del servizio di ricerca (mediante json.net http://james.newtonking.com/json)
   - Risultati
   - Facet
   - Conteggi dei risultati
   - Sviluppare un'interfaccia utente per la visualizzazione dei risultati della ricerca, facet e i conteggi (è già stato seguente).

Questo è il codice che è stato usato per ottenere i risultati di ricerca di Azure:

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**Aumento in base alla posizione**

Probabilmente il requisito più importante per verificare il prototipo incluse l'aggiunta di una parola chiave di ricerca percorso alla query. Per il portale che se si immette un nome di città nella ricerca una query, che è fondamentale dei rivenditori città specificato da classificare superiore dei rivenditori con la parola chiave città nella descrizione. Per questo requisito è stato usato un profilo punteggio collocare il campo Città superiore a altri campi.

**Supporta più lingue**

È necessario per visualizzare i risultati della ricerca corretti nelle lingue corrette ed è disponibile un'opzione per trovare gli stessi risultati ottenuti in lingue diverse. Due lati a questo problema sono: 

- Ricerca di parole in più lingue
- Visualizza i risultati di ricerca nella lingua corretta

Abbiamo risolto parte presentazione mediante l'aggiunta di un documento per ogni lingua con testo localizzato e una proprietà con la lingua. Quando si immette un termine di ricerca, abbiamo utente `$filter` espressioni per filtrare in base a quella l'utente ha scelto.

Tutti i documenti sono proprietà nascosta denominata "città" basata su tipo di insieme. Questa proprietà memorizza i nomi di città in tutte le lingue che consente all'utente eseguire ricerche in più lingue.

###<a name="data-storage"></a>Archiviazione dei dati

Tutti i dati (profilo, sottoscrizione e contabilità) vengono archiviati in Database SQL. Tutti i file multimediali vengono archiviati in archiviazione BLOB Azure, tra cui immagini e video forniti dal rivenditore. Uso archiviazione BLOB separata isola gli effetti di caricamento dei file; file non vengono mai condivisa mingled con il sito Web, in modo che non è necessario ricreare il sito ogni volta che si aggiunge i file.

Particolarmente importante della progettazione dello spazio di archiviazione è che gli sviluppatori più possono condividere un archivio di sviluppo singola. Uno dei requisiti per il progetto WhatToPedia era in grado di creare un ambiente di sviluppo all'interno di 15 minuti, inclusi i video, immagini e dati rivenditore. Recupero di dati più recenti da in linea per, eseguendo uno script SQL e l'esecuzione del processo di importazione, un ambiente completo può essere superato non tutto. Questa esercitazione migliora anche il processo di gestione temporaneo.

###<a name="webjobs"></a>WebJobs

Azure WebJobs vengono utilizzati per aggiornare i dati per l'indice. Tramite la creazione di un processo dell'indicizzatore di ricerca, la parte dell'indicizzazione è stato molto semplice integrare la soluzione. Modifica del codice solo abbiamo fatto era per contenere il processo dell'indicizzatore per aggiungere un `Indexed` campo per il modello di dati per indicare lo stato dell'indice. Ogni volta che viene aggiunto o aggiornato, un nuovo profilo di `Indexed` campo è impostato su false. Lo stesso avviene se il rivenditore modifica il suo dati profilo tramite il portale.  

Il processo consente di cercare tutte le righe che `Indexed` impostato su false. Quando viene trovata la riga, il documento è registrato nella ricerca di Azure e quindi la `Indexed` campo è impostato su true. Non è necessario pianificare per l'aggiunta e aggiornamento dei dati in quanto tramite il servizio di ricerca di Azure effettivamente vengono dell'oggetto. Se si aggiunge un documento che è già presenta, il servizio non produrrà un aggiornamento automaticamente.

Tutti i processi web sono stati sviluppati come applicazioni console che possono essere caricate in Azure siti web come file ZIP, decompressi e quindi pianificate.

Il processo è programmato per l'esecuzione di ogni 5 minuti come un'attività programmata web. Viene calcolato che il servizio richiede circa 3 minuti per caricare 3.000 documenti, che è stato compreso i requisiti. 

> [AZURE.NOTE] Esiste una caratteristica di indicizzatore prototipo recente introdotta nella ricerca Azure. Questa caratteristica è stato acquistato un ritardo per la usarla nel primo rilascio, ma viene visualizzato per risolvere il problema che è stato usato il processo dell'indicizzatore, ovvero per automatizzare operazioni di caricamento di dati.


###<a name="backup-strategy"></a>Strategia di backup

Abbiamo progettato una strategia di backup a più livelli per recuperare un intervallo di scenari, un problema grave, fino a ripristino di una singola transazione. Risorse per la protezione includono tre tipi di dati (sito web, dati del sottoscrittore e i file multimediali). 

Prima di tutto, mantenendo il codice di origine del sito web di per Online, è possibile sapere se il sito viene interrotto, pubblicandolo da TFS è possibile ricreare la. 

Dati dei sottoscrittori nel Database di SQL Azure è bene più sensibile. Abbiamo nuovamente questa configurazione utilizzando l'incorporata portare in primo piano (vedere [Backup del Database SQL Azure e il ripristino](http://msdn.microsoft.com/library/azure/jj650016.aspx)). La pianificazione di backup è backup completo del database una volta alla settimana, differenziali una volta al giorno e log delle transazioni ogni 5 minuti.  Date le dimensioni dei dati, questa soluzione è più adeguata per il nostro volumi dati immediata e prevista.

Terzo è memorizzare file immagine e video in archiviazione BLOB Azure. Stiamo ancora valutando il piano di backup ultimo per i dati, prendere in considerazione Explorer Cloudberry per Azure come una possibile soluzione. Per il momento serve un WebJob per copiare immagini e video in un'altra posizione.

##<a name="what-we-learned"></a>Quanto appreso

Perché si dispone già di dati, è stato facile stabilire di prova. Entro ore, era un prototipo con facet e contatori, spostamento, classificati profili e dei risultati della ricerca. I risultati della ricerca sono altrettanto precisi, che si è deciso di rimuovere alcuni filtri presentati al cliente finale. 

Sorprese principali per noi era velocità è possibile scoprire Azure ricerca e quanto spazio è tornato. In forma letterale, abbiamo stabilito di prova in poche ore (vedi nota riportata di seguito), la sostituzione di 500 righe di codice con 3 righe di codice nell'applicazione front-end (più di un nuovo WebJob) e si ottengono risultati migliori. 

In precedenza, il nostro codice implementata suddivisione in pagine, conteggi e altre funzionalità standard per la ricerca. Funzionalità di ricerca di Azure, i risultati che si torna indietro includono occorrenze trovate, facet, spostamento dati, conteggi, ossia tutte le informazioni sono necessarie e sono state dover fornire noi. Inoltre, inclusi miglioramento e predefinito spostamento in base a facet non è la soluzione originale.

Il problema maggiore durante l'implementazione era che era una versione di anteprima e ricerca di informazioni ed esperienze condivise era difficile. Una volta abbiamo connessa alcuni punti, trovati che mediante il servizio di ricerca di Azure è stata semplice a causa di formato per i dati di API REST e JSON. È possibile chiamare il framework degli direttamente da più aperto plug-in origine come JQuery JSON.Net ed è possibile utilizzare strumenti come Fiddler per sperimentazione veloce e il debug. 

> [AZURE.NOTE] Oltre a disporre dei dati predisposto, che ha contribuito che per la creazione di prototipi già interpretati funzionamento grazie alla tecnologia, rendendo Aiutaci a migliorare la produttività e più desiderano una funzionalità incorporate della ricerca. Se è necessario come nella creazione della query di ricerca, in base a facet spostamento, filtri e così via è necessario attendere la creazione di prototipi per richiedere più tempo. 

###<a name="controlling-facets-in-the-search-presentation-page"></a>Controllare gli aspetti nella pagina di presentazione della ricerca

Uno delle esperienze durante la verifica dei concetti è stato per la pianificazione con attenzione spese facet. Dopo avere caricato una grande quantità di dati nella soluzione, visto che il volume di facet era troppo alto per presentare agli utenti. 

Abbiamo risolto questo problema limitando il parametro count facet. Il parametro count impone un limite al numero di facet restituito all'utente. Un collegamento che include una discussione del parametro count sono disponibili [qui](search-faceted-navigation.md).

###<a name="webjobs-for-scheduling-tasks"></a>WebJobs per la programmazione delle attività

Ricerca Azure non sorprese solo piacevole per noi. Abbiamo scoperto che utilizzando WebJobs per automatizzare le operazioni di caricamento di dati per la ricerca di Azure è stata di gran lunga superiore per l'approccio precedente, d'istruzione sotto utilizzando una macchina virtuale dedicata che esegue Windows utilità di pianificazione, con attività pianificate per aggiornare l'indice di ricerca. WebJobs è stata più semplice configurare e più semplice da debug e naturalmente molto più la necessità di acquistare una macchina virtuale dedicata conveniente.

###<a name="azure-blob-storage-explorer-for-updating-images"></a>Soluzioni di archiviazione BLOB Azure per l'aggiornamento di immagini

È stato rilevato che con [Esplora risorse di archiviazione BLOB Azure](https://azurestorageexplorer.codeplex.com/) (disponibile su codeplex) per essere molto utile per la gestione degli aggiornamenti di video e immagine al sito. Abbiamo usarla come strumento di sviluppo per aggiornare manualmente le immagini e video che fanno parte del sito principale. Abbiamo trovato che non sia più flessibile di distribuire le modifiche al portale e si elimina un'iterazione test completo ogni volta che è necessario aggiornare un'immagine. 

##<a name="a-few-final-words"></a>Alcune parole finale

Grazie alla grande voglio WhatToPedia per consentire di condividere informazioni.  

Ci auguriamo che questo case study utili. Se di continuare a usare la ricerca di Azure, consigliabile alcune risorse per aumentare la velocità è lungo:

- [Forum MSDN dedicato per la ricerca di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
- [StackOverflow è installato anche un tag](http://stackoverflow.com/questions/tagged/azure-search)
- [Pagina documentazione Azure.com](https://azure.microsoft.com/documentation/services/search/)
- [Azure documentazione di ricerca su MSDN](http://msdn.microsoft.com/library/azure/dn798933.aspx)


##<a name="appendix-search-indexer-webjob"></a>Appendice: Ricerca indicizzatore WebJob

Il codice seguente consente di creare indicizzatore indicata nella sezione sulla creazione di prototipi.

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            
            
            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }
            
        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }
                    
                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            
          


<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Subheading 4]: #subheading-4
[Subheading 5]: #subheading-5
[Next steps]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
 
