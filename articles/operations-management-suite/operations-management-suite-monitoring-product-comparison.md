<properties 
   pageTitle="Monitoraggio di confronto tra prodotti Microsoft | Microsoft Azure"
   description="Famiglia di prodotti (OMS, le operazioni di gestione Order) è soluzione di gestione IT che consente di gestire e proteggere le in locale e infrastruttura cloud basato su cloud di Microsoft.  In questo articolo sono riportati i diversi servizi inclusi in OMS e vengono forniti collegamenti in base al contenuto dettagliato."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="microsoft-monitoring-product-comparison"></a>Confronto tra prodotti monitoraggio Microsoft

In questo articolo viene fornito un confronto tra System Center Operations Manager (SCOM) e Analitica Log in operazioni di gestione famiglia di prodotti (OMS) per quanto riguarda l'architettura, la logica di come si esegue il monitoraggio di risorse e come vengono eseguire analisi dei dati che raccolti.  Si tratta di notificare acquisire familiarità con le differenze e illustrati i vantaggi offerti.  

## <a name="basic-architecture"></a>Architettura di base
### <a name="system-center-operations-manager"></a>System Center Operations Manager

Tutti i componenti SCOM sono installati nel centro dati.  [Gli agenti siano installati](http://technet.microsoft.com/library/hh551142.aspx) nel computer Windows e Linux che vengono gestite dal SCOM.  Agenti connettersi al [Server di gestione](https://technet.microsoft.com/library/hh301922.aspx) di comunicare con il magazzino SCOM database e i dati.  Gli agenti si basano su autenticazione del dominio per connettersi al server di gestione.  Quelli all'esterno di un dominio trusted può eseguire l'autenticazione certificato o connettersi a un [Server Gateway](https://technet.microsoft.com/library/hh212823.aspx).

SCOM richiede due database SQL, uno per i dati operativi e un'altra data warehouse per supportare l'analisi dei dati e creazione di report.  SQL Reporting Services per creare un report sul dati dal data warehouse viene eseguito un [Server di report](https://technet.microsoft.com/library/hh298611.aspx) . 

SCOM monitorare risorse cloud usando management pack per i prodotti, ad esempio [Azure](https://www.microsoft.com/download/details.aspx?id=38414), [Office 365](https://www.microsoft.com/download/details.aspx?id=43708)e [AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html).  Questi management pack consente di uno o più agenti locali come proxy per i flussi di lavoro in esecuzione e individuazione risorse cloud misurano le prestazioni e disponibilità.  Agenti proxy vengono utilizzati anche per [i dispositivi di rete monitor](https://technet.microsoft.com/library/hh212935.aspx) e altre risorse esterne.

La Console è un'applicazione di Windows che si connette a uno dei server di gestione e consente all'amministratore di visualizzare e analizzare i dati raccolti e configurare l'ambiente di SCOM.  Una console basata sul web può essere ospitata in qualsiasi server IIS e fornisce analisi dei dati tramite un browser.

![Architettura SCOM](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Registro Analitica

La maggior parte dei componenti OMS sono nel cloud Azure in modo che è possibile distribuire e gestire con costo minimo e di lavoro di amministrazione.  Tutti i dati raccolti dal Log Analitica vengono archiviati in archivio OMS.

Registro Analitica può raccogliere dati da uno dei tre origini:

- Fisica e macchine virtuali in esecuzione Windows e [Microsoft monitoraggio agente (MMA)](https://technet.microsoft.com/library/mt484108.aspx) o Linux e l' [Agente di famiglia di prodotti di gestione di operazioni per Linux](https://technet.microsoft.com/library/mt622052.aspx).  Questi computer possono essere locale o macchine virtuali in Azure o in un altro cloud.
- Un account di archiviazione Azure con dati di [Azure diagnostica](../cloud-services/cloud-services-dotnet-diagnostics.md) raccolti dal ruolo di lavoro Azure, ruolo web o macchina virtuale.
- [Connessione a un gruppo di gestione SCOM](https://technet.microsoft.com/library/mt484104.aspx).  In questa configurazione, gli agenti di comunicano con i server di gestione SCOM che inviano i dati nel database di SCOM nel punto in cui viene quindi recapitato all'archivio dati OMS.
Gli amministratori di analizzare i dati raccolti e configurare Analitica Log con il portale OMS che è ospitato in Azure ed è possibile accedervi da qualsiasi browser.  App per dispositivi mobili per accedere a questi dati sono disponibili per le piattaforme standard.

![Architettura Analitica log](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>Integrazione di SCOM e Analitica Log

Quando SCOM viene utilizzata come origine dati per Analitica Log è possibile sfruttare le funzionalità di entrambi i prodotti in una distribuzione ibrida monitoraggio ambiente.  È possibile configurare gli agenti SCOM esistenti tramite la Console per essere gestito da OMS, oltre a continuare eseguire management pack da SCOM.  
Dati da un gruppo di gestione SCOM connesso viene recapitati Log Analitica utilizzando uno dei quattro metodi:

- Eventi e dati sulle prestazioni sono raccolti dall'agente e recapitati SCOM.  Server di gestione di SCOM recapitare quindi i dati Log Analitica.
- Alcuni eventi, ad esempio i registri di IIS e gli eventi di protezione continuano a essere recapitata direttamente al Log Analitica dall'agente.
- Alcune soluzioni verranno eseguita software aggiuntivo in agente di o richiedono l'installazione di software per raccogliere dati aggiuntivi.  In genere questi dati verranno inviati direttamente al Log Analitica.
- Alcune soluzioni raccogliere i dati direttamente dal server di gestione SCOM che non hanno origine dall'agente.  Ad esempio, la [soluzione di gestione degli avvisi](https://technet.microsoft.com/library/mt484092.aspx) raccoglie gli avvisi da SCOM dopo che sono state create.

## <a name="monitoring-logic"></a>Controllo logica
SCOM e Log Analitica compatibili con simili dati raccolti dal agenti ma hanno differenze sostanziali tra la definizione e implementare la logica per la raccolta di dati e come vengono analizzare i dati in cui sono raccolti.

### <a name="operations-manager"></a>Operations Manager
Monitoraggio logica SCOM è implementato in [management pack](https://technet.microsoft.com/library/hh457558.aspx) che contengono la logica per l'individuazione di componenti per monitorare, misurare l'integrità dei componenti e per la raccolta di dati da analizzare.  Monitoraggio dei dati può essere costituiti semplicemente la raccolta di un indicatore di prestazioni o evento oppure è possibile utilizzare logica complessa implementata in uno script.  Management Pack che includono il monitoraggio completo sono disponibili per un'ampia varietà di [applicazioni di terze parti](http://go.microsoft.com/fwlink/?LinkId=82105) oltre ai dispositivi hardware e di rete.  È possibile [creare il proprio management pack](http://aka.ms/mpauthor) per le applicazioni personalizzate.

Management Pack contengono più flussi di lavoro in cui ogni esegue alcune funzione distinct monitoraggio, ad esempio campione un contatore delle prestazioni, verifica dello stato di un servizio o esecuzione di uno script.  Ogni flusso di lavoro viene eseguito in modo indipendente e definisce i propri risultati, ad esempio il database verrà scritta in e se verrà generato un avviso. 

È possibile ignorare i dettagli del flusso di lavoro, ad esempio la frequenza che vengono eseguiti, la soglia in cui sono valutare la possibilità di un messaggio di errore e la gravità dell'avviso che generano.  È anche possibile fornire funzionalità aggiuntiva mediante l'aggiunta di un flusso di lavoro.

![Esegue l'override](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

Management Pack sono installati nel database Operations Manager e distribuito automaticamente ai agenti dal server di gestione.  Ogni agente verrà automaticamente scaricare management pack e caricare i flussi di lavoro i contenuti pertinenti per le applicazioni di che tali utenti abbiano installato.  Dati raccolti mediante l'agente viene recapitati al server di gestione per l'inserimento nel warehouse SCOM database e i dati.  Console di operazioni consente di visualizzare e analizzare i dati mediante le visualizzazioni personalizzate, dashboard e report, incluse in management pack.

Nella figura seguente è illustrata la distribuzione dei management pack.

![Management pack flusso](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Registro Analitica
#### <a name="event-and-performance-collection"></a>Nuovo evento e raccolta delle prestazioni
Registro Analitica raccoglie eventi e contatori da sistemi agente utilizzando origini, ad esempio registro eventi di Windows, i registri di IIS e Registro di sistema.  È possibile definire i criteri per cui sono raccolti tramite il portale Analitica Log e quindi creare query Log per analizzare i dati raccolti.  Quando si crea l'area di lavoro OMS e definire i dati aggiuntivi per determinate applicazioni, viene definito un set di criteri standard. 

![Definire i registri eventi nel Log Analitica](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

Mentre SCOM ha più dettagliate flussi di lavoro che in genere definire criteri specifici per dati e le azioni da eseguire in risposta, Analitica Log ha più generale criteri per la raccolta di dati.  Registra query e le soluzioni forniscono più target criteri per l'analisi e usarle in dati specifici nel cloud dopo è stata raccolta.

#### <a name="solutions"></a>Soluzioni
Soluzioni forniscono la logica aggiuntiva per l'analisi e raccolta di dati.  È possibile selezionare le soluzioni da aggiungere al proprio abbonamento OMS dalla raccolta soluzioni.

![Raccolta soluzioni](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

Soluzioni eseguite principalmente nel cloud fornire analisi di eventi e contatori raccolti repository OMS.  Si possono inoltre definire dati aggiuntivi per raccogliere che possono essere analizzati con registro query o tramite l'interfaccia utente aggiuntive fornite dalla soluzione in dashboard OMS. 

Ad esempio, [il rilevamento delle modifiche soluzione](https://technet.microsoft.com/library/mt484099.aspx) rileva modifiche di configurazione dei sistemi agente e scrive eventi a un archivio di OMS che può essere analizzato con più viste grafiche riepilogativi rilevato modifiche.  È possibile eseguire il drill down dalla visualizzazione riepilogativa in query log che visualizzano dati dettagliate raccolte dalla soluzione.


Mentre è possibile selezionare le soluzioni aggiungere all'abbonamento, non è attualmente la possibilità di creare soluzioni personalizzate.  È possibile selezionare gli eventi e contatori delle prestazioni per raccogliere e creare visualizzazioni personalizzate in base a query di log.

Nel diagramma seguente verrà riepilogato logica alla base monitoraggio Log Analitica.

![Registro Analitica soluzione flusso](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>Il monitoraggio dell'integrità
### <a name="operations-manager"></a>Operations Manager
SCOM può diversi componenti di un'applicazione del modello e fornire un sistema in tempo reale per ogni.  In questo modo è non solo visualizzare rilevato gli errori e le prestazioni nel tempo, ma anche per convalidare lo stato effettivo di un'applicazione o sistema e ognuno dei relativi componenti in qualsiasi momento.  Poiché riconosce i periodi di tempo che un'applicazione è disponibile, il motore di integrità SCOM supporta anche livello contratti di servizio che analizzare e generare report sulla disponibilità di un'applicazione nel tempo.

Ad esempio, la visualizzazione seguente mostra lo stato in tempo reale motori di database SQL monitorate da SCOM.  Lo stato di tutti i database per uno dei motori di database viene visualizzato nella parte inferiore metà della vista.

![Visualizzazione dello stato](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

Esplorazione dell'integrità per uno dei motori di database è illustrato di seguito con monitor che vengono utilizzati per determinare le condizioni generali.  Questi monitor sono definiti nel management pack per SQL ed eseguire su tutti i motori di database SQL rilevati da SCOM.

![Esplorazione dell'integrità](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

Componenti su più sistemi possono essere combinati per valutare l'integrità di un'applicazione distribuita.  Può essere particolarmente utile per applicazioni aziendali che includono più componenti distribuiti.  È possibile creare un modello che misura lo stato di ogni componente che riporta in disponibilità per l'applicazione.

Active Directory è illustrato un esempio di un management pack che fornisce un modello per analizzare i relativi componenti distribuiti.  Il diagramma di esempio seguente mostra lo stato dell'ambiente globale e la relazione tra insiemi di strutture, domini e controller di dominio.  Ognuno di essi include componenti secondari e più monitor simili all'esempio SQL precedente.

![Vista diagramma SCOM](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Registro Analitica
OMS non include un motore comune alle applicazioni di modello o misurare la loro salute in tempo reale.  Soluzioni individuali possono valutare l'andamento di particolari servizi basati sui dati raccolti e può installare logica personalizzata in un agente di eseguire l'analisi in tempo reale.  Poiché le soluzioni eseguire nel cloud con l'accesso all'archivio OMS, possono spesso forniscono un'analisi più approfondita che in genere viene eseguita dal management pack. 

Ad esempio, le [soluzioni di valutazione di Active Directory e dei rischi SQL](https://technet.microsoft.com/library/mt484102.aspx) analizzare i dati raccolti ed esprimere una valutazione per diversi aspetti dell'ambiente.  Include consigli per miglioramenti che possono essere eseguiti per migliorare la disponibilità e le prestazioni dell'ambiente.

![Soluzione di valutazione di Active Directory](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>Analisi dei dati
SCOM e Log Analitica offrono diverse caratteristiche per analizzare i dati raccolti.  SCOM sono visualizzazioni e dashboard nella Console di operazioni di analisi dei dati più recenti in diversi formati e i report per presentare i dati sotto forma di dati.  Log Analitica offre un'interfaccia e il linguaggio di query log completamento per l'analisi dei dati in archivio OMS.  Quando SCOM viene utilizzato come origine dati per Log Analitica, il repository include i dati raccolti mediante SCOM in modo che gli strumenti Analitica Log possono essere utilizzati per analizzare i dati di entrambi i sistemi.

### <a name="operations-manager"></a>Operations Manager

#### <a name="views"></a>Visualizzazioni
Visualizzazioni nella Console consentono di visualizzare diversi tipi di dati raccolti dal SCOM in diversi formati, in genere tabulari per gli eventi, avvisi e dati relativi allo stato e a linee grafici per dati sulle prestazioni.  Visualizzazioni eseguono analisi minimo o il consolidamento dei dati, ma consentono di filtrare in base a specifici criteri. 

![Visualizzazioni](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

Management Pack in genere costituiranno più visualizzazioni dell'applicazione o un sistema che esegue il monitoraggio di supporto.  Può trattarsi di viste di stato per gli oggetti diversi individua il management pack, viste degli avvisi per i problemi rilevati e nelle visualizzazioni delle prestazioni per contatori.

Visualizzazioni sono particolarmente adatte per analizzare lo stato corrente dell'ambiente compresi gli avvisi aperti e lo stato di integrità dei sistemi monitorati e degli oggetti.  È possibile drill-down a evento dettagliata o dati sulle prestazioni di supporto un particolare avviso per individuare la causa principale. Analogamente, è possibile visualizzare le prestazioni e l'integrità dei diversi componenti di un'applicazione di valutare il relativo stato di integrità corrente.

#### <a name="dashboards"></a>Dashboard
Dashboard nella Console principalmente funzionano con gli stessi dati visualizzazioni, ma sono maggiormente configurabili e possono includere più visualizzazioni.  Un set di Dashboard standard disponibili che è possibile personalizzare facilmente secondo le proprie esigenze.  È inoltre possibile utilizzare un widget di PowerShell che è possibile visualizzare i dati restituiti da una query di PowerShell.

![Dashboard](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

Gli sviluppatori hanno la possibilità di aggiungere componenti personalizzati ai dashboard che includono nei loro management pack.  Questi potrebbero essere altamente specializzati di una determinata applicazione, ad esempio il dashboard in management pack SQL riportato di seguito.  Questo dashboard è utilizzabile anche come modello per le versioni personalizzate.

![Dashboard di SQL](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>Report
Report in SCOM analizzare i dati sotto forma di dati.  Possono essere stampati e pianificati per il recapito automatico in diversi formati di file tra cui PDF, CSV e Word.  Rapporti di lavorare con i dati dal data warehouse in modo che siano ideale per l'analisi delle tendenze a lungo termine.

Management Pack fornirà in genere i report personalizzati per una specifica applicazione.  È anche possibile selezionare da una raccolta di report generici che è possibile personalizzare per le applicazioni personalizzate o per l'esecuzione di analisi ad hoc.

Di seguito è riportato un report di prestazioni di esempio che mostra i dati raccolti da Active Directory Management Pack.

![Report](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Registro Analitica
Registro Analitica ha un [linguaggio di query](https://technet.microsoft.com/library/mt484120.aspx) che è possibile utilizzare per eseguire analisi nei dati da più applicazioni senza la necessità di creare una visualizzazione personalizzata o un report.  Poiché OMS è implementata nel cloud, prestazioni delle query e analisi dei dati non sono soggette a eventuali limitazioni hardware e analizzare rapidamente query incluse milioni di record. 

Query nel registro Analitica sono anche base di altre funzionalità.  È possibile salvare una query, esportare i risultati in Excel o automaticamente eseguire a intervalli regolari e generare un avviso se i risultati corrispondono a specifici criteri.  

![Registro query flusso](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

Di seguito è illustrato un esempio di una query Log Analitica.  In questo esempio vengono restituiti e raggruppati in base all'evento tutti gli eventi con "avviato" del nome dell'ID.  L'utente fornisce semplicemente la query e Log Analitica genera in modo dinamico l'interfaccia utente per eseguire l'analisi.  Gli elementi selezionati nell'elenco restituirà i dati dell'evento dettagliate.

![Query di log](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

Oltre ad hoc analisi, è possibile salvare per utilizzi futuri query nel registro Analitica e viene aggiunto anche il [dashboard OMS](http://technet.microsoft.com/library/mt484090.aspx) come illustrato nell'esempio seguente.

![Dashboard OMS](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>Passaggi successivi

- Distribuire [System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh205987.aspx).
- Iscriversi a [Log Analitica](https://azure.microsoft.com/documentation/services/log-analytics).  
