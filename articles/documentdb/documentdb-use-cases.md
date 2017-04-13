<properties 
    pageTitle="Casi di utilizzo comune DocumentDB | Microsoft Azure" 
    description="Informazioni nella parte superiore cinque casi di utilizzo per DocumentDB: generato dall'utente contenuto, la registrazione eventi, i dati del catalogo, dati preferenze utente e Internet di elementi (IoT)." 
    services="documentdb" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="hawong"/>

# <a name="common-documentdb-use-cases"></a>Casi di utilizzo DocumentDB comuni
In questo articolo viene fornita una panoramica dei diversi casi di utilizzo comune per DocumentDB.  Suggerimenti riportati in questo articolo fungono da punto di partenza in fase di sviluppo dell'applicazione con DocumentDB.   

Dopo aver letto in questo articolo, sarà possibile rispondere alle domande seguenti: 
 
- Quali sono i casi di utilizzo comune per DocumentDB?
- Quali sono i vantaggi dell'utilizzo DocumentDB come utente generato archivio del contenuto?
- Quali sono i vantaggi dell'utilizzo DocumentDB come dati catalogo archivia?
- Quali sono i vantaggi dell'utilizzo DocumentDB come un log archivia?
- Quali sono i vantaggi dell'utilizzo DocumentDB come dati preferenze utente archivia?
- Quali sono i vantaggi dell'utilizzo DocumentDB come archivio dati per i sistemi Internet di elementi (IoT)?

## <a name="common-use-cases-for-documentdb"></a>Casi di utilizzo comune per DocumentDB
Azure DocumentDB è un database di NoSQL generica usata in una vasta gamma di applicazioni e use case. È consigliabile per tutte le applicazioni che richiede tempi di risposta ordine di millisecondi bassa ed sono necessario ridimensionare rapidamente. Di seguito sono alcuni attributi di DocumentDB che rendono ideali per le applicazioni di prestazioni elevate.

- DocumentDB partizioni a livello nativo disponibilità e scalabilità con i dati.
- DocumentDB ha lo spazio di archiviazione SSD backup con i tempi di risposta di ordine di MS latenza ridotta.
- Supporto del DocumentDB per i livelli la coerenza come finale, sessione e obsolescenza delimitata aumenta la basso costo-a-rapporto prestazioni. 
- DocumentDB contiene un modello di prezzo orientata dati flessibile che metri lo spazio di archiviazione e la velocità di trasmissione in modo indipendente.
- Modello di velocità riservato del DocumentDB consente di pensare in termini di numero di lettura/scrittura anziché CPU/memoria/IOPs dell'hardware sottostante.
- Consente di progettazione del DocumentDB che scala a volumi enorme richiesta nell'ordine miliardi di richieste al giorno.

Questi attributi sono particolarmente utili quando si tratta di web per dispositivi mobili, gioco e le applicazioni IoT necessitano tempi di risposta bassa ed è necessario gestire grandi quantità di lettura e scrittura. 

## <a name="user-generated-content"></a>Contenuto generati dall'utente
Un caso di utilizzo comune per DocumentDB prevede l'archiviazione e query utente generato contenuto (UGC) per dispositivi mobili applicazioni, in particolare social media e web.  Alcuni esempi di UGC sono le sessioni di chat, TWEET, post di blog, valutazioni e commenti.  Spesso, UGC nelle applicazioni di social networking è una combinazione di testo in formato libero, proprietà, tag e le relazioni che non sono limitate rigido struttura.   

Contenuto, ad esempio chat, commenti e i post possono essere creati in DocumentDB senza trasformazioni o oggetto complesso ai livelli di mapping relazionale.  Proprietà dati possono essere aggiunte o modificate con facilità in base ai requisiti come gli sviluppatori scorrere il codice dell'applicazione, pertanto promuovere sviluppo rapido.  

Applicazioni che si integrano con diversi social network devono adattarsi alle schemi da queste reti.  Come dati verrà indicizzati automaticamente per impostazione predefinita in DocumentDB, i dati sono pronti per eseguire la query in qualsiasi momento.  Di conseguenza, queste applicazioni hanno la possibilità di recuperare previsioni in base alle proprie esigenze rispettive.       

Molte delle applicazioni di social networking eseguire scala globale e può presentare criteri di utilizzo imprevisti.  Flessibilità scala archivio dati è essenziale come livello di applicazione scale in base alla richiesta di utilizzo.  È possibile scalare aggiungendo le partizioni dati aggiuntivi con un account DocumentDB.  Inoltre, è possibile creare anche altri account DocumentDB tra più aree. DocumentDB disponibilità del servizio l'area geografica, vedere [Le aree di Azure](https://azure.microsoft.com/regions/#services).   

## <a name="catalog-data"></a>Catalogo dati
Scenari di utilizzo del catalogo dati riguardano l'archiviazione e la ricerca di un insieme di attributi per entità quali persone, luoghi e prodotti.  Alcuni esempi di dati del catalogo sono gli account utente, cataloghi di prodotti, registri di dispositivo per IoT e bollo di sistemi di materiali.  Gli attributi dei dati possono variare e possono cambiare nel tempo in modo da adattarlo requisiti dell'applicazione.  

Valutare la possibilità di un esempio di un catalogo di prodotti per un fornitore di parti componenti auto. Ogni parte, potrebbe essere dei propri attributi oltre agli attributi comuni che condividono tutte le parti.  Inoltre, gli attributi di una parte specifica è possono modificare l'anno successivo quando viene rilasciato un nuovo modello.  Come archiviare un documento JSON, DocumentDB supporta schemi flessibili e consente di rappresentare dati con le proprietà annidate e pertanto è adatto per l'archiviazione dei dati del catalogo di prodotti.       

## <a name="logging-and-time-series-data"></a>Registrazione e dati serie temporale
La registrazione delle applicazioni viene spesso generata in volumi elevati e si possono basati su versione applicazione distribuita o gli eventi di registrazione componente vari attributi.  Registra i dati non è limitati da strutture rigide o relazioni complesse. Sempre più log dati vengono mantenuti nel formato JSON poiché JSON è semplice per gli utenti di leggere e leggera.
   
In genere esistono due casi di utilizzo principali correlati ai dati di registro eventi.  Il primo caso di utilizzo consiste nell'eseguire query ad hoc su un sottoinsieme di dati per la risoluzione dei problemi.  Durante la risoluzione dei problemi, un sottoinsieme di dati prima di tutto recuperato dai registri, in genere dal serie temporale.  Quindi, viene eseguito un drill-down filtrando il set di dati con livelli di errore o messaggi di errore. Si tratta in cui archiviare i registri eventi DocumentDB è dei vantaggi. Per impostazione predefinita verrà indicizzati automaticamente i dati di registro archiviati in DocumentDB e pertanto è possibile eseguire una query in qualsiasi momento. Inoltre, dati del registro possono essere persistente nel partizioni di dati come una serie temporale. Per refrigerazione per i criteri di conservazione, è possono annullare registri precedenti.          

Il secondo caso di utilizzo comporta l'esecuzione prolungata processi analitica dati eseguiti offline su un volume elevato di dati del registro.  Analisi di disponibilità di server, analisi degli errori dell'applicazione e analisi dei dati clickstream esempi di questo caso di utilizzo.  In genere, Hadoop viene utilizzato per eseguire questi tipi di analisi.  Con il connettore Hadoop per DocumentDB, DocumentDB database fungono da origini dati e sink per i processi di maialino, Hive e mapping e riduzione. Per informazioni dettagliate sul connettore Hadoop per DocumentDB, vedere [eseguire un processo Hadoop con DocumentDB e HDInsight](documentdb-run-hadoop-with-hdinsight.md).      

## <a name="gaming"></a>Gioco
Il livello di database è un componente fondamentale delle applicazioni di gioco. Giochi moderni elaborazione grafica client mobile/console, ma si basano su cloud per offrire contenuto personalizzato e personalizzato come Stat gioco, integrazione di social media e classifiche punteggi. Giochi richiedono scarsa latenza di lettura e scrittura per fornire un svolgano in gioco esperienza e il livello di database deve gestire i valori massimi e minimi tassi richiesta durante il lancio di gioco nuovi e degli aggiornamenti delle caratteristiche.

DocumentDB viene utilizzato da giochi enorme scala come [di alcuni inattivo: terra dell'uomo n](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) da [Giochi successiva](http://www.nextgames.com/)e [alone 5: tutori](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). In entrambi i casi di utilizzo, i principali vantaggi di DocumentDB sono i seguenti:

- DocumentDB consente prestazioni adatta verso l'alto o verso il basso illimitate. In questo modo gioco gestire aggiornamento profilo e STAT da decine di milioni di giocatori simultanei effettuando una chiamata API singola.
- DocumentDB supporta millisecondi lettura e scrittura al fine di evitare qualsiasi ritardi durante il gioco.
- Indicizzazione automatica del DocumentDB consente il filtraggio rispetto a più proprietà diverse in tempo reale, ad esempio individuare lettori dal loro lettore interno ID, loro GameCenter, Facebook, Google ID o query in base all'appartenenza player una Guida. Questo è possibile senza la creazione di indicizzazione complessa o infrastruttura sharding.
- Caratteristiche di social networking inclusi messaggi della chat in gioco, le appartenenze Guida di Windows Media player, sfide completate, classifiche punteggi e grafici sociali sono più facili da implementare con uno schema flessibile.
- DocumentDB come un gestito piattaforma-come-a-service (PaaS) obbligatorio gestione e installazione minima lavoro per consentire l'iterazione rapido e ridurre i tempi di mercato.


## <a name="user-preferences-data"></a>Dati delle preferenze utente
Oggi più moderni applicazioni web e dispositivi mobili accompagnato da esperienze e viste complesse. Queste visualizzazioni ed esperienze sono in genere dinamiche, ristorazione le preferenze dell'utente o espressione e personalizzazione alle esigenze.  Di conseguenza, le applicazioni devono essere in grado recuperare le impostazioni personalizzate in modo efficace per rendere rapidamente esperienze e gli elementi dell'interfaccia utente. 

JSON è un formato efficace per rappresentare i dati di layout dell'interfaccia utente come non è solo semplice, ma anche possono essere facilmente interpretato da JavaScript.  DocumentDB offre livelli coerenza regolabili che consentono di legge rapidi con scrittura bassa latenza. Di conseguenza, la memorizzazione dei dati di layout dell'interfaccia utente quali le impostazioni personalizzate come documenti JSON DocumentDB è un modo efficace per ottenere i dati in rete.

## <a name="iot-and-device-sensor-data"></a>Dati sensore IoT e dispositivo
Casi di utilizzo IoT condividono in genere alcuni modelli come il caricamento, elaborare e archiviare i dati.  Prima di tutto, tali sistemi consentono per assunzione di dati che è in grado di acquisire burst di dati da sensori dispositivo delle impostazioni locali diverse.  Successivamente, questi sistemi elaborano e analizzare i dati di flussi per derivano approfondimenti in tempo reale. E infine, la maggior parte in caso contrario tutti i dati non hanno un limite verranno inserita in un archivio di dati per analitica in linea e query ad hoc.    

Microsoft Azure offre servizi RTF che possono essere utilizzati per IoT use case.  Azure IoT servizi sono un insieme di servizi inclusi Azure evento hub, DocumentDB Azure, Azure flusso Analitica, Hub di notifica di Azure, apprendimento Azure, Azure HDInsight e ottenere informazioni. 

Burst di dati possibile caricamento da Azure evento hub offrendo acquisizione di dati alta velocità con latenza ridotta. Dati caricamento che devono essere elaborati per comprendere in tempo reale possono funneled a Azure flusso Analitica per analitica in tempo reale. Dati possono essere caricati in DocumentDB per eseguire la query ad hoc. Una volta i dati vengono caricati in DocumentDB, i dati sono pronti per eseguire la query.  I dati in DocumentDB possono essere utilizzati come dati di riferimento come parte del tempo reale analitica. Inoltre, dati possono ulteriormente essere raffinati ed elaborati tramite una connessione dati DocumentDB a HDInsight per i processi di maialino, Hive o mapping e riduzione.  Dati raffinati vengono caricati quindi tornare alla DocumentDB per i report.   

Per una soluzione di esempio IoT mediante DocumentDB, EventHubs ed eccesso, vedere [esempi di eccesso hdinsight archivio in GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Per ulteriori informazioni sulle offerte Azure per IoT, vedere [creare Internet di elementi di](http://www.microsoft.com/en-us/server-cloud/internet-of-things.aspx).

## <a name="next-steps"></a>Passaggi successivi
 
Per iniziare a utilizzare DocumentDB, è possibile creare un [account](https://azure.microsoft.com/pricing/free-trial/) e quindi seguire il [percorso di formazione](https://azure.microsoft.com/documentation/learning-paths/documentdb/) per informazioni su DocumentDB e le informazioni che necessarie. 

Oppure, se si preferisce per saperne di più sui clienti che utilizzano DocumentDB, sono disponibili le seguenti storie di clienti:

- [Giochi successivi](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). Inattiva Walking: No uomo della terra gioco raggiunge su 1 # supportato da Azure DocumentDB.
- [Alone](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Implementazione social gioco con Azure DocumentDB alone 5.
- [Cortana Analitica raccolta](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Cortana Analitica raccolta - un sito della community scalable basato su Azure DocumentDB.
- [Semplicissimo](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Iniziali integratore offre informazioni globale imprese multinazionali in minuti con le tecnologie Cloud flessibile.
- [Repubblica di news](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Aggiunta di business intelligence per essere sempre informati per fornire informazioni con scopo per cittadini impegnati. 
- [Gruppi di archiviazione internazionale](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Per il colore coerente in tutto il mondo, marchi principali agevolare gruppi di archiviazione. E gruppi di archiviazione attiva in Azure.
- [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Carattere di riempimento globale Telenor utilizzato nel cloud per spostare con la velocità di un avvio. 
- [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). L'archivio del futuro viene eseguita nella ricerca rapida e il flusso di dati.
