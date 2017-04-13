<properties 
    pageTitle="Azure App Web App servizi per aziende" 
    description="Viene illustrato come utilizzare Azure App servizio Web Apps per creare soluzioni di sito Web per l'azienda enterprise" 
    services="app-service\web" 
    documentationCenter="" 
    authors="apwestgarth" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/29/2016" 
    ms.author="anwestg"/>

# <a name="azure-app-service-web-apps-offerings-for-enterprise-whitepaper"></a>Azure App Web App servizi per white paper Enterprise #

La necessità di ridurre i costi e soluzioni IT più veloce in un ambiente in rapida evoluzione crea nuove sfide per sviluppatori, i professionisti IT e responsabili. Gli utenti sono sempre più ricerca di applicazioni web linea di Business (Line) essere veloce, rispondere e disponibile da qualsiasi dispositivo. Nello stesso momento aziende siano tentando di sfruttare la produttività e l'efficienza fornito dall'integrazione con servizi per dispositivi mobili e cloud, è possibile semplice come il single sign-on da dispositivi con Active Directory per la collaborazione in Office 365 usando i dati estratti da un'applicazione line interna che a sua volta recupera dati dall'applicazione aziendale di Salesforce. [Azure App servizio Web App](http://go.microsoft.com/fwlink/?LinkId=529714) è un servizio cloud aziendali per lo sviluppo, test e l'esecuzione web e applicazioni per dispositivi mobili, API Web e siti Web di tipo generico. Può essere utilizzato per l'esecuzione di siti Web aziendale, siti intranet, App aziendali e campagne di marketing digitale in una rete globale del Data Center ottimizzato per scala e la disponibilità, oltre al supporto per l'integrazione continua e attrezzi moderna consigliate.  

Questo articolo evidenzia le funzionalità del servizio [Web Apps](/services/app-service/web/) specificamente incentrati su LOB applicazioni Web, che comprende la migrazione delle applicazioni web esistenti e distribuzione di nuove applicazioni web line sulla piattaforma in esecuzione. 

## <a name="audience"></a>Gruppo di destinatari ##

Professionisti IT, progettisti e Manager che desiderano eseguire la migrazione ai carichi di lavoro web cloud attualmente in esecuzione in locale. Carichi di lavoro Web interessare Business per dipendente o Business con le applicazioni web partner.

## <a name="introduction"></a>Introduzione ##

Applicazione del servizio Web Apps è una piattaforma ideale su cui ospitare applicazioni web interne ed esterne e servizi come fornisce una soluzione conveniente, scalabilità, gestita in modo da concentrarsi su l'esecuzione di valore aziendale per gli utenti invece di spesa grandi quantità di tempo e denaro gestione e il supporto di separare gli ambienti. Web App offre una piattaforma flessibile su cui distribuire le applicazioni web aziendali che offre la possibilità di continuare a eseguire l'autenticazione in Active Directory locale tramite l'integrazione con Microsoft Azure Active Directory, supporto delle distribuzioni semplice e rapide che usa l'interno continua integrazione e distribuzione consigliate, durante il ridimensionamento automatico per adattarsi alle esigenze aziendali - tutti in una piattaforma gestita che consente di concentrarsi su un'applicazione e non l'infrastruttura. 

## <a name="problem-definition"></a>Definizione del problema ##

Orizzontale IT cambia rapidamente, con una sposta all'esterno di hosting su server tradizionale con i costi di capitale elevati su tempi lunghi in un'applicazione basata su richiesta utilizzare i servizi che si adattano automaticamente per gestire il carico. I reparti IT vengono costantemente per ridurre il costo e dell'infrastruttura e la manutenzione dei consumi sull'ambiente dedicato con l'obiettivo di riduzione dell'investimento di capitale aumentando flessibilità. Al termine della vita precedenti piattaforme di infrastruttura, ad esempio Windows Server 2003, è zeri i reparti IT per verificare la migrazione cloud potenziali consente di evitare i costi di capitale di nuovo a lungo termine. In passato, CIO consentirebbe di acquistare per altri reparti; Tuttavia, sempre più CMOs e altri punte unità business sta impiegando un ruolo più attivo in modalità di spesa loro bilancio e qual è il rendimento dell'investimento. Sempre più aziende necessario personale di essere molto più dispositivi mobili più mai ai dipendenti lavorando in remoto, impiegare più tempo con i clienti che devono accedere alle necessità di sistemi gratuito.

Esigenze aziendali mensile, settimanale, ogni giorno. Aziende siano cercando immediata scala globale con normali servizi aggiornati completi delle nuove caratteristiche, fornite da terze parti o internamente.  In alcuni casi imprese anche cercata le funzionalità per le applicazioni di isolare e mentre anche effettuare l'accesso alle risorse uso delle strutture di Cloud pubblica. Gli utenti hanno aspettative superiore, con molti che si avvalgono di servizi di vita privato, ad esempio Office 365. Si aspettano abbiano accesso ai servizi RTF funzionalità simile, aggiornati, nel loro ciclo di vita di lavoro. Per far fronte a questa domanda IT deve aziendali come abilitare questa funzionalità tramite selezione e l'integrazione con terze parti l'aspetto dei servizi, attenzione selezione delle piattaforme che possibile adattare le esigenze aziendali senza anche affidabile con costi ridotti totali di proprietà.

Team di sviluppo siano cercando di fornire vantaggio commerciale immediato, l'esecuzione di nuove funzionalità regolarmente. Che stanno cercando una piattaforma affidabile e conveniente che si integra con i test consigliate-sviluppo e gli strumenti esistente, rilasciare; e lavorare insieme i reparti IT automatizza distribuzione, gestione e gli avvisi, con l'obiettivo di zero tempi di inattività.

<a href="highlevel" />
## <a name="high-level-solution"></a>Soluzione a livello di elevato ##

Framework e piattaforme web sempre più in uso per sviluppare, testare e ospitare applicazioni aziendali.  Con una tipica applicazione line-of business, ad esempio un sistema di spesa dipendente interno, spesso costituito un'app web con un database di backup per archiviare i dati connessi con l'applicazione.

Servizio di App Web App è consigliabile per l'hosting di tali applicazioni, che offre scalabilità e affidabilità infrastruttura che gestita e patch con vicino zero intervento manuale e il tempo di inattività. La piattaforma Microsoft Azure offre molte opzioni relative all'archiviazione dei dati per supportare le applicazioni web App Web da Database SQL di Microsoft Azure, un gestito scalable relazionali database-come-a-service, per servizi utilizzati dal partner, ad esempio MySQL Database ClearDB e MongoDB.

In alternativa è effettuare utilizzare dell'investimento esistente in locale. Nello scenario di esempio, un sistema di spesa dipendente, è consigliabile mantenere l'archivio di dati all'interno di infrastruttura interna. Può trattarsi per l'integrazione con sistemi interni (creazione di report, retribuzioni, fatturazione e così via) o per soddisfare un requisito di governance IT.  Web Apps sono disponibili diversi metodi di consentire all'utente a cui connettersi infrastruttura locale attivato:

- [App servizio ambienti](app-service-app-service-environment-intro.md) - App servizio ambienti (base) costituiscono una nuova caratteristica Premium recente è stata aggiunta l'offerta di servizi di Microsoft Azure App.  ASEs fornire un ambiente completamente isolato e dedicato per l'esecuzione in modo sicuro le applicazioni di servizio App Azure scala elevato offrendo anche isolamento e accesso alla rete protetto   
- [Connessioni ibrido](../biztalk-services/integration-hybrid-connection-overview.md) : le connessioni ibrido sono una funzionalità di Microsoft Azure BizTalk Services e abilitato le app Web a cui connettersi locale risorse in modo sicuro, ad esempio SQL Server, MySQL, API Web e servizi web personalizzata. 
- [Integrazione di rete virtuale](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) -integrazione di Web Apps con Azure virtuali consente di connettere un'app web a una rete virtuale Azure che a sua volta può essere collegato all'infrastruttura locale attivato tramite VPN da sito. 

I diagrammi seguenti illustrano una soluzione di alto livello di esempio con le opzioni di integrazione applicativa sulle risorse locale.  Nel primo esempio viene illustrato questo può essere ottenuto utilizzando funzionalità standard del servizio di App Azure e il secondo mostra come può essere eseguita mediante premium che offre la possibilità, App servizio ambienti.

Utilizzo delle funzionalità di servizio App Standard:![](./media/web-sites-enterprise-offerings/on-premise-connectivity-solutions.png "Using Standard App Service Features")

Con un ambiente App:![](./media/web-sites-enterprise-offerings/on-premise-connectivity-solutions-ASE.png "Using an App Service Environment")

## <a name="business-benefits"></a>Vantaggi aziendali ##

Servizio di App Web App offre una vasta gamma di vantaggi che consentono la funzione per essere molto più conveniente e flessibile in l'esecuzione per le esigenze aziendali. 

### <a name="paas-model"></a>Modello PaaS ###

Servizio di App Web App si basa su una piattaforma come modello di servizio che fornisce un numero di risparmi sui costi e l'efficienza.  Non è necessario dedicare ore gestione macchine virtuali, patch sistemi operativi e Framework. Web App è un ambiente automaticamente patch che consente di evidenziare la gestione di applicazioni web e non macchine virtuali, uscire dal team gratuito fornire il valore di altre aziende.

Il modello PaaS sostenere Web Apps consente medici della metodologia attrezzi soddisfare gli obiettivi. Come azienda significa del totale e integrazione in tutta l'intero ciclo di vita, inclusa sviluppo, test, release, monitoraggio e la gestione e supporto tecnico. 

Per team di sviluppo, è possibile configurare continue integrazione e distribuzione i flussi di lavoro da Visual Studio Team Services, GitHub, TeamCity, Hudson o BitBucket, per consentire di compilazione automatica, test e distribuzione attivazione release processore cicli mentre riducendo l'attrito coinvolge il rilascio di infrastruttura esistente. Web App supporta anche la creazione di più verifiche e ambienti per il flusso di lavoro di rilascio di gestione temporanea, non è necessaria riservare o allocare hardware a tale scopo, è possibile creare ambienti il numero desiderato e definire il proprio promozione per rilasciare del flusso di lavoro. Come un'azienda che è possibile decidere di rilascio per un intervallo di test aperto dal controllo origine, eseguire una serie di test e dopo il completamento promuovere spazio secondario e infine scambio di produzione con senza tempi di inattività, con i vantaggi che vengono precaricate e hot per garantire la migliore esperienza di applicazioni web ospitate su Web Apps.  Inoltre imprese possono sfruttare il Testing in funzionalità di produzione di App del servizio Web Apps per indirizzare una sezione del traffico a un intervallo diverso aperto, convalidare le modifiche, prima di tutto il traffico il passaggio alla nuova distribuzione o ripristino di tutto il traffico alla distribuzione precedente. 

Team operativi possibile certi che si trovano nella posizione meglio possibili per rispondere a eventuali problemi con le applicazioni web ospitate in Web App con incorporato in termini di caratteristiche di monitoraggio e avvisi. Deve team operativi hanno già investito in analitica e soluzioni di monitoraggio di tali da Microsoft Visual Studio applicazione approfondimenti, nuovo Relic e AppDynamics. Sono inoltre completamente supportate nella Web App per consentire la continuità e un ambienti familiari da cui si desidera eseguire il monitoraggio delle applicazioni web.

Infine, Web Apps offre funzionalità di backup automatico le app che e i database ospitati diretta a un contenitore di archiviazione Blob Azure. Che forniscono un metodo semplice modo e molto conveniente con cui eseguire il ripristino di emergenza, riducendo la necessità di complessi in locale hardware e software.

### <a name="ease-of-migration"></a>Facilità di migrazione ###

Rotazione e la manutenzione dell'hardware è importante per le aziende come velocizzare cicli di rilascio per sistemi operativi e hardware. Forse si dispone di un numero di server Windows Server 2003 R2 ottenuti alla fine del supporto in 2015, ma vengono comunque ospitano applicazioni web principali per l'azienda? Servizio di App Web App è un ottimo candidato in cui pubblicare le applicazioni web e per poter razionalizzare immobiliare di hardware business. Web App consente di accedere a un intervallo di specifiche hardware che vengono gestite e gestiti come parte del servizio, tenere in considerazione sostituzione i costi e gestione come parte del budget infrastruttura più bisogno.  Migrazione possa essere costituiti semplicemente da una copia operazione e Incolla della distribuzione esistente ad App Web o una migrazione più complessa in cui l'utilizzo dell'Assistente Web App di migrazione aggiungerà valore. Le applicazioni web migrati sfrutteranno gamma completa di servizi di Azure, integrare servizi aggiuntivi per le applicazioni web. Ad esempio, è possibile aggiungere Azure Active Directory per controllare l'accesso all'applicazione in base a associazione degli utenti ai gruppi di sicurezza. Un altro esempio possibile aggiungere servizi di Cache per migliorare le prestazioni e ridurre la latenza, fornire complessivo migliore esperienza utente. 

### <a name="enterprise-class-hosting"></a>Hosting di classe Enterprise ###

Servizio di App Web App fornisce una piattaforma stabile e affidabile che si in grado di gestire un'ampia varietà di business deve da piccole interni sviluppo e test carichi di lavoro, ai siti Web altamente scalate traffico elevato. Tramite Web Apps, si apportano della stessa piattaforma hosting enterprise classe da Microsoft di utilizzata una società carichi di lavoro web valore elevato. App Web, insieme a tutti i servizi della piattaforma Azure, sono progettate con sicurezza e conformità alle normative, ad esempio ISO (ISO/IEC 27001:2005); SOC1 e SOC2 SSAE 16/ISAE 3402 attestati, HIPAA BAA, PCI e Fedramp, un componente di base di ogni elemento e le funzionalità per ulteriori informazioni vedere [http://aka.ms/azurecompliance](/support/trust-center/compliance/). 

Piattaforma Microsoft Azure consente ai ruoli in base a autorizzazione controlli enterprise livelli di controllo per le risorse all'interno di applicazioni Web. RBAC consente alle aziende la possibilità di implementare i propri criteri di gestione di accesso per tutte le risorse nell'ambiente di Azure, assegnando utenti ai gruppi e a sua assegnazione delle autorizzazioni necessarie a questi gruppi risorse, ad esempio un'app web. Per ulteriori informazioni su RBAC in Azure, vedere [http://aka.ms/azurerbac](../active-directory/role-based-access-control-configure.md). Utilizzando Web Apps, può essere che le applicazioni web sono distribuite in un ambiente protetto e disporre il controllo completo nel cui territorio vengono distribuite le risorse. 

Azure App servizio ambienti [http://aka.ms/aseintro](http://aka.ms/aseintro) sono una nuova opzione piano di servizio premium per clienti aziendali desideri utilizzare del servizio di App Azure e che consente di fornire un ambiente completamente isolato e dedicato.  In questo modo ai clienti aziendali di distribuire le applicazioni che è possono sfruttare scala molto elevata, anche il controllo completo sul traffico di rete in ingresso e in uscita e ASEs consentono alle applicazioni di connessione sicura ad alta velocità tramite reti virtuali alle risorse locale.

Servizio di App Web Apps sono in grado di utilizzare gli investimenti in locale, che offre la possibilità di connettersi alle risorse interne, ad esempio la data warehouse o l'ambiente di SharePoint. Come indicato in soluzione di [Alto livello](#highlevel) è possibile apportare uso delle connessioni ibrida e la connettività di rete virtuale per stabilire connessioni in locale infrastruttura e i servizi.

### <a name="global-scale"></a>Scala globale ###

Servizio App Web App è una piattaforma globale e scalabilità, che consente di ingrandimento e adattare in base alle esigenze dell'impresa sempre più velocemente e con minime a lungo termine pianificazione e il costo le applicazioni web. In tipico in scenari di infrastruttura locale, espansione e aumento della domanda sia in locale e aree geografiche da richiedere una grande quantità di gestione, pianificazione e le spese effettuare il provisioning e gestire l'infrastruttura aggiuntiva. Web App offre la possibilità di scalare applicazioni web con il ebb e flusso delle proprie esigenze. Ad esempio utilizzando l'applicazione spese ad esempio, per la maggior parte del mese gli utenti sono light utenti dell'applicazione ma come la data di scadenza ogni mese per invii spese l'immissione e utilizzo aumenta sull'applicazione, Web Apps è in grado di eseguire il provisioning automaticamente ulteriori infrastruttura dell'applicazione e quindi dopo l'utilizzo con subsided nuovamente è possibile ridimensionare all'infrastruttura di base che è possibile definire.

Web App è disponibile globalmente nei data 24 Center in tutto il mondo e crescente. Per l'elenco più aggiornato delle aree geografiche e posizione, vedere [http://aka.ms/azlocations](http://aka.ms/azlocations). Con Web Apps, è possibile utilizzare l'azienda facilmente ottenere scala e copertura globale. Man mano che aumenta la propria azienda in nuove aree, il dashboard di applicazione in uso e l'host nella Web App creazione di report possono agevolmente distribuiti in Data Center aggiuntive e gestire gli utenti locali molto più rapidamente tramite la combinazione di applicazioni Web e Azure il traffico di gestione tutti con i vantaggi dell'infrastruttura di scalable sotto la possibilità di contratto ed espandere come le esigenze di modifica uffici.
 
## <a name="solution-details"></a>Dettagli della soluzione ##

Esaminiamo un esempio di uno scenario di migrazione dell'applicazione. Seguente illustra i dettagli di come caratteristiche dell'applicazione del servizio Web Apps collaborano fornire ottima soluzione e valore aziendale.
 
In questo esempio la riga dell'applicazione aziendale che verrà preso è un'applicazione che consente ai dipendenti di inviare loro le spese di rimborso spese. L'applicazione è ospitata in un Windows Server 2003 R2 esecuzione IIS6 e il database è un database di SQL Server 2005. Il motivo è scegliere precedenti si trova di server con la fine del servizio per Windows Server 2003 R2 e SQL Server 2005 prossimi e include [Strumenti](http://aka.ms/websitesmigration) e [indicazioni](http://aka.ms/websitesmigrationresources) migrare automaticamente i carichi di lavoro in Azure. Tenendo presente, il modello utilizzato in questo esempio si applica a un'ampia varietà di scenari di migrazione. 

### <a name="migrate-existing-application"></a>Eseguire la migrazione di applicazione esistente ###

Passaggio 1 della soluzione globale per lo spostamento di un'applicazione line-of-business alle Web App è identificare l'architettura e le risorse dell'applicazione. L'esempio in questo documento è un'applicazione web ASP.NET ospitata in un unico Server IIS con il database ospitato in un Server SQL separato, come illustrato nella figura seguente. Accesso dei dipendenti al sistema utilizzando una combinazione di nome utente e password, immettere i dettagli delle spese e caricare digitalizzate copie delle entrate, nel database, per ogni elemento della spesa. 
 
![](./media/web-sites-enterprise-offerings/on-premise-app-example.png)

#### <a name="items-to-consider"></a>Aspetti da considerare ####

Quando l'applicazione di migrazione da un ambiente locale, è consigliabile da tenere in considerazione, alcuni vincoli Web Apps. Ecco alcuni argomenti più importanti da tenere in considerazione durante la migrazione di applicazioni web alla Web Apps ([http://aka.ms/websitesmigrationresources](http://aka.ms/websitesmigrationresources)):

-   Associazioni porta – Web App supporta solo la porta 80 per HTTP e 443 per il traffico HTTPS. Se l'applicazione Usa qualsiasi altra porta, e quindi una volta eseguita la migrazione dell'applicazione renderà l'utilizzo delle porte 80 per HTTP e porta 443 per il traffico HTTPS. Questo è spesso un problema sia dannoso, in quanto è comune nelle distribuzioni locali per rendere uso delle porte diverse in modo da coprire l'utilizzo di nomi di dominio, in particolare in ambienti di sviluppo e test
-   Autenticazione – Web App supporta l'autenticazione anonima per impostazione predefinita e l'autenticazione basata su identificate da un'applicazione. Web App possono offrire l'autenticazione di Windows quando l'applicazione è integrato con Azure Active Directory e ADFS solo. Questa è una caratteristica che viene descritto dettagliatamente più avanti [in questo caso](http://aka.ms/azurebizapp) 
-   Cache globale in base a assembly: Web Apps non consente la distribuzione di assembly per la Cache (assembly globale). Pertanto, se l'applicazione viene eseguita la migrazione rende utilizzano questa caratteristica locale, è consigliabile spostare gli assembly nella cartella bin dell'applicazione.
-   IIS 5 Modalità di compatibilità-Web App non supporta la modalità compatibilità IIS 5 e pertanto ogni istanza Web Apps e tutte le applicazioni web sotto l'istanza di Web Apps padre eseguire nello stesso processo di lavoro all'interno di un singolo pool.
-   Utilizzo di librerie COM – Web App non consente la registrazione dei componenti della piattaforma. Di conseguenza l'applicazione effettua utilizzare di tutti i componenti COM, questi dovrà essere sostituito nel codice gestito e distribuito con l'applicazione.
-   Filtri, filtri possono essere supportati su Web Apps. Utenti che dovranno essere distribuiti come parte dell'applicazione e registrato in config dell'applicazione web. Per ulteriori informazioni, vedere [http://aka.ms/azurewebsitesxdt](web-sites-transform-extend.md). 

Una volta questi argomenti sono stati considerati, applicazione web deve essere pronto per il Cloud. E non occorre se alcuni argomenti non vengono soddisfatti completamente, lo strumento di migrazione otterranno basata migliore per la migrazione. 

Passaggi successivi nel processo di migrazione devono per creare un'applicazione servizio web app e un Database di SQL Azure. Sono disponibili più dimensioni delle istanze di applicazioni Web con un numero differente di CPU Core e gli importi di RAM disponibili per la selezione in base alle proprie esigenze di applicazioni web. Per ulteriori informazioni e prezzi, vedere [http://aka.ms/azurewebsitesskus](/pricing/details/websites/). Analogamente, Database SQL di Microsoft Azure si occupa delle esigenze di un'azienda con diversi livelli di servizio e livelli di prestazioni per soddisfare requisiti. Sono disponibili ulteriori informazioni su [http://aka.ms/azuresqldbskus](/pricing/details/sql-database/). Una volta creato, l'applicazione di caricamento di App del servizio Web Apps, tramite FTP o WebDeploy e quindi passare al database.

In questa migrazione la soluzione utilizza Database SQL Azure, ma che è non nel database solo è supportato in Azure. Società inserirvi anche utilizzo di MySQL, MongoDB, Azure DocumentDB e molti altri tramite i componenti aggiuntivi che possono essere acquistati in [Azure Store](/marketplace/partner-program/). 

Quando si crea un Database di SQL Azure numerose opzioni è disponibili per importare un database esistente da un server in locale da generazione di uno script di un database esistente all'utilizzo del [livello di dati applicazione esportare e importare](http://aka.ms/dacpac). 

Database dell'applicazione spese è stato creato mediante la creazione di un nuovo Database SQL Azure, la connessione al database con SQL Server Management Studio e l'esecuzione di script per creare lo schema di database e inserire i dati da un database locale.

Il passaggio finale in questa fase prima della migrazione è necessario aggiornare delle stringhe di connessione al database dell'applicazione. Questo risultato può essere ottenuto tramite il portale Azure. Per ogni web app è possibile modificare impostazioni specifiche dell'applicazione, incluse tutte le stringhe di connessione utilizzate dall'applicazione per la connessione a un database in uso.

### <a name="alternatives-to-using-azure-sql-database"></a>Alternative all'utilizzo di Database SQL Azure ###

La piattaforma Azure offre numerose alternative all'utilizzo di Database SQL Azure come database primario applicazioni web, in questo modo viene diversi carichi di lavoro ad esempio usare una soluzione NoSQL o abilitare la piattaforma in base alle esigenze dei dati di un'azienda. Ad esempio un'azienda può contenere dati che non devono essere archiviati fuori sede o in un ambiente cloud pubblico e pertanto starebbe per mantenere l'uso dei propri database locale.

#### <a name="connectivity-to-on-premises-resources"></a>Connettività a sulle risorse locale ####
Servizio App Web App offre diverse opzioni per la connessione a risorse locali, ad esempio i database, che consente di riutilizzo dell'infrastruttura di valore elevato esistente. Le opzioni sono come indicato di seguito:

- Servizio App ambienti sono isolati e creati in una subnet di una rete virtuale, consentendo pertanto l'ambiente di comunicare con endpoint privato all'interno della stessa rete virtuale - [http://aka.ms/appserviceasenetworking](http://aka.ms/appserviceasenetworking)
- Integrazione di rete virtuale Web App supporta l'integrazione tra Web Apps e una rete virtuale di Azure, che consente l'accesso alle risorse in esecuzione in rete virtuale che, se connessi alla rete locale attivato con VPN da sito, consente la connessione diretto ai sistemi locali attivato.
- Connessioni ibrido sono una caratteristica di Azure BizTalk Services e forniscono un modo semplice per connettersi a singoli locale risorse, ad esempio SQL Server, MySQL, API Web HTTP e più servizi Web personalizzati.

#### <a name="scale-and-resiliency"></a>Scalabilità e resilienza ####

Esigenze impresa propri dipendenti tramite acquisizioni o crescita organica naturale, in modo troppo deve web scala di applicazioni per soddisfare le richieste di nuove. Effettivamente oggi comune per visualizzare una maggiore diffusione di team collocati e dipendenti remoti, ad esempio forzare società con uffici negli Stati Uniti, Europa e Asia, con le vendite per dispositivi mobili in molte altre aree. Web App è in grado di gestire le modifiche flessibile in scala comodamente e automaticamente.

Servizio di App Web App consente alle applicazioni web da configurare per ridimensionare automaticamente tramite il portale di Azure, a seconda di due vettori-pianificato orari o per l'utilizzo della CPU. Il ridimensionamento automatico di Web App offre un modo estremamente flessibile e conveniente per soddisfare maggiore modifiche in uso per tutte le applicazioni aziendali, dalle applicazioni web come il nostro spese di sistema per i siti Web, un burst elevato di traffico esperienza utente per una breve durata della promozione di marketing. Per ulteriori informazioni e indicazioni sulla scala applicazioni web con Web Apps, vedere [come siti Web scala](web-sites-scale.md).

Oltre a flessibilità scala di applicazioni Web, la piattaforma complessiva consente la continuità aziendale e adattabilità tramite la distribuzione possibile di applicazioni web e delle risorse in più Data Center e aree geografiche.

## <a name="summary"></a>Riepilogo ##
Servizio App Web App offre una soluzione flessibile, conveniente e rispondere alle esigenze dinamiche di business in un ambiente in rapida evoluzione. Web App consente alle aziende incrementare la produttività e l'efficienza grazie uso di una piattaforma gestita con funzionalità attrezzi moderna e ridotte mani sulla gestione, fornendo funzionalità enterprise in scala, resilienza, sicurezza e l'integrazione con l'opzione risorse locale.

## <a name="call-to-action"></a>Azione di chiamata ##
Per ulteriori informazioni sul servizio Azure App servizio Web Apps, visitare [http://aka.ms/enterprisewebsites](/services/websites/enterprise/) nel punto in cui è possano origine ulteriori informazioni e accesso per una versione di valutazione oggi alle [http://aka.ms/azuretrial](/pricing/free-trial/) per valutare il servizio e vantaggi per l'azienda.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 
