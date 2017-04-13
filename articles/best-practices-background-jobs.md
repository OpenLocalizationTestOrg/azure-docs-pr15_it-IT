<properties
   pageTitle="Indicazioni di processi di sfondo | Microsoft Azure"
   description="Indicazioni sullo sfondo delle attività in esecuzione in modo indipendente dell'interfaccia utente."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2016"
   ms.author="masashin"/>

# <a name="background-jobs-guidance"></a>Indicazioni di processi in background

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

## <a name="overview"></a>Panoramica

Molti tipi di applicazioni richiedono attività in background che eseguono indipendente l'interfaccia (). Esempi di processi batch, attività di elaborazione in modo intensivo e processi di lunga, ad esempio i flussi di lavoro. Processi in background possono essere eseguiti non richiedono l'interazione, ossia l'applicazione può avviare il processo e quindi continuare a elaborare le richieste interattive dagli utenti. Questo può aiutare a ridurre il carico sull'applicazione dell'interfaccia utente, che consente di migliorare la disponibilità e ridurre i tempi di risposta interattivi.

Ad esempio, se è necessaria un'applicazione per generare le anteprime delle immagini che vengono caricate dagli utenti, può eseguire questa operazione come un processo in background e salvare l'anteprima allo spazio di archiviazione, al termine, senza dover attendere che il processo per il completamento. Nello stesso modo un utente di eseguire un ordine può avviare un flusso di lavoro in background elabora l'ordine, mentre l'interfaccia utente consente all'utente di continuare l'esplorazione web app. Una volta completato il processo in background, può aggiornare i dati degli ordini stored e inviare un messaggio di posta elettronica all'utente per confermare l'ordine.

Quando si decidere se per implementare un'attività come un processo in background, il criterio principale è se l'attività supporti l'esecuzione di senza interazione dell'utente e senza interfaccia utente di dover attendere che il processo per il completamento. Attività che richiedono l'utente o l'interfaccia utente in attesa, mentre completati potrebbe non essere appropriata come processi in background.

## <a name="types-of-background-jobs"></a>Tipi di processi in background

Processi in background sono in genere uno o più tipi di processi seguenti:

- Processi di CPU che richiede significative attività, ad esempio calcoli matematici o analisi modello strutturale.
- Processi dei / O-che richiede significative attività, ad esempio l'esecuzione di una serie di transazioni di archiviazione o l'indicizzazione di file.
- Processi batch, ad esempio gli aggiornamenti dei dati di notte o elaborazione pianificata.
- Esecuzione prolungata flussi di lavoro, ad esempio esecuzione dell'ordine o il provisioning di servizi e sistemi.
- Elaborazione dati sensibili nel punto in cui l'attività viene trasferita a una posizione più sicura per l'elaborazione. Ad esempio, non è consigliabile per l'elaborazione di dati riservati all'interno di un'app web. Se, tuttavia, è possibile utilizzare un modello, ad esempio [Gatekeeper](http://msdn.microsoft.com/library/dn589793.aspx) per trasferire i dati in un processo in background isolato che acceda all'archiviazione protetta.

## <a name="triggers"></a>Trigger

In molti modi diversi, è possono avviare processi in background. Rientrano in una delle categorie seguenti:

- [**Trigger basate su eventi**](#event-driven-triggers). L'attività viene avviato in risposta a un evento, in genere un'azione da un utente o un passaggio in un flusso di lavoro.
- [**Trigger basata sulle risorse di programmazione**](#schedule-driven-triggers). L'attività viene richiamato su una pianificazione basata su un timer. Può trattarsi di una pianificazione ricorrente o una chiamata variante specificata per un secondo momento.

### <a name="event-driven-triggers"></a>Trigger basate su eventi

Chiamata basate su eventi utilizza un trigger per avviare attività in background. Esempi di utilizzo di trigger basate su eventi:

- L'interfaccia utente o un altro processo inserisce un messaggio in una coda. Il messaggio contiene dati relativi a un'azione che ha avuto luogo, ad esempio l'utente eseguire un ordine. Attività in background è in attesa nella coda e rileva l'arrivo di un nuovo messaggio. Legge il messaggio e utilizza i dati come input per il processo in background.
- L'interfaccia utente o un altro processo Salva o aggiornato un valore dello spazio di archiviazione. Attività in background monitora lo spazio di archiviazione e rileva modifiche. Legge i dati e viene usato come input per il processo in background.
- L'interfaccia utente o un altro processo effettua una richiesta di un estremo, ad esempio un URI HTTPS o un'API esposta come un servizio web. Invia i dati necessari per completare l'attività in background come parte della richiesta. Il punto finale o servizio web richiama attività in background, che utilizza i dati come input.

Tipico attività sono adatti alla chiamata basate su eventi esempi di elaborazione delle immagini, flussi di lavoro, inviare informazioni ai servizi remoto, l'invio di messaggi di posta elettronica e il provisioning di nuovi utenti nelle applicazioni multi-tenant.

### <a name="schedule-driven-triggers"></a>Trigger basata sulle risorse di programmazione

Programmazione basati su chiamata utilizza un timer per avviare attività in background. Esempi di utilizzo di programmazione basati su trigger:

- Timer è in esecuzione in locale all'interno dell'applicazione o come parte del sistema operativo dell'applicazione richiama un'attività in background a intervalli regolari.
- Un timer che è in esecuzione in un'altra applicazione o un servizio timer, ad esempio utilità di pianificazione di Azure, invia una richiesta a un servizio web o API a intervalli regolari. Il servizio web o API richiama attività in background.
- Un'applicazione o un processo separato avvia un timer che causa l'attività in background da richiamare una volta dopo un intervallo di tempo specificato o un'ora specifica.

Tipico attività sono adatti alla chiamata di programmazione basati su esempi di routine di elaborazione batch (ad esempio, l'aggiornamento di elenchi di prodotti correlati per gli utenti in base a tale comportamento recente), attività di elaborazione dei dati routine (ad esempio all'aggiornamento degli indici o generare risultati accumulati), analisi dei dati per i report giornaliero, pulizia di conservazione dei dati e controlli per la coerenza dei dati.

Se si usa un'attività basata sulle risorse di programmazione che deve essere eseguito come una singola istanza, tenere presente quanto segue:

- Se viene ridimensionata l'istanza di calcolo che esegue l'utilità di pianificazione (ad esempio una macchina virtuale utilizzando pianificate Windows), sarà necessario più istanze dell'utilità di pianificazione in esecuzione. Questi Impossibile avviare più istanze dell'attività.
- Se le attività eseguito per maggiore del periodo tra gli eventi di utilità di pianificazione, l'utilità di pianificazione può avviare un'altra istanza dell'attività mentre quella precedente è ancora in esecuzione.

## <a name="returning-results"></a>I risultati vengono restituiti

Processi in background eseguire in modo asincrono in un processo separato o anche in un percorso diverso, l'interfaccia utente o al processo chiamante attività in background. Se possibile, le attività in background sono operazioni "generato automaticamente" e lo stato di avanzamento di esecuzione non influisce sull'interfaccia utente o il processo di chiamata. Questo errore indica che il processo di chiamata non attendere il completamento delle attività. Di conseguenza, non rilevate automaticamente alla fine dell'attività.

Se si richiede un'attività in background per comunicare con l'attività chiamante per indicare lo stato di avanzamento o il completamento, è necessario implementare un meccanismo per l'oggetto. Alcuni esempi sono:

- Scrivere il valore di un indicatore di stato per lo spazio di archiviazione che è possibile accedere all'attività dell'interfaccia utente o chiamante che è possibile eseguire il monitoraggio o controllare questo valore quando richiesto. Altri dati che l'attività in background deve restituire al chiamante possono essere inseriti nell'archivio stesso.
- Stabilire una coda di risposta che l'interfaccia utente o chiamante ascolto. Attività in background è possibile inviare messaggi a coda che indicano lo stato e completamento. Dati che l'attività in background deve restituire al chiamante possono essere inseriti nei messaggi. Se si utilizza Bus di servizio Azure, è possibile utilizzare le proprietà **Rispondi** e **CorrelationId** per implementare questa funzionalità. Per ulteriori informazioni, vedere [correlazione nel servizio Bus negoziate messaggistica](http://www.cloudcasts.net/devguide/Default.aspx?id=13029).
- Esporre un endpoint da attività in background che l'interfaccia utente o chiamante può accedere per ottenere informazioni sullo stato o l'API. Dati che l'attività in background deve restituire al chiamante possono essere inclusi nella risposta.
- Utente chiamato attività in background tornare all'interfaccia utente o chiamante tramite un'API per indicare lo stato in determinati punti o al completamento. È possibile tramite gli eventi generati in locale o tramite un meccanismo di pubblicazione e sottoscrizione. Dati che l'attività in background deve restituire al chiamante possono essere inclusi nel payload richiesta o un evento.

## <a name="hosting-environment"></a>Ambiente host

È possibile ospitare attività in background utilizzando un intervallo di servizi della piattaforma Azure diversi:

- [**WebJobs e azure Web Apps**](#azure-web-apps-and-webjobs). È possibile utilizzare WebJobs per eseguire processi personalizzati in base a un intervallo di diversi tipi di script o programmi eseguibili nel contesto di un'app web.
- [**Ruoli web e lavoro servizi Cloud Windows azure**](#azure-cloud-services-web-and-worker-roles). È possibile scrivere codice all'interno di un ruolo che viene eseguita in background.
- [**Macchine virtuali di azure**](#azure-virtual-machines). Se si dispone di un servizio di Windows o si desidera utilizzare l'utilità di pianificazione di Windows, è comune pubblicare le attività in background all'interno di un computer virtuale dedicato.
- [**Batch azure**](./batch/batch-technical-overview.md). Si tratta di un servizio di piattaforma che consente di pianificare lavoro complesse per l'esecuzione in una raccolta gestita macchine virtuali e può automaticamente scala calcolare le risorse per soddisfare le esigenze dei processi.

Nelle sezioni seguenti descrivono ognuna di queste opzioni in modo più dettagliato e includono considerazioni per informazioni su come scegliere l'opzione appropriata.

## <a name="azure-web-apps-and-webjobs"></a>WebJobs e azure Web Apps

È possibile utilizzare WebJobs Azure eseguire processi personalizzati come attività in background all'interno di un'App Web di Azure. WebJobs eseguire all'interno del contesto dell'applicazione web come un processo continuo. WebJobs anche eseguire in risposta a un evento di attivazione di Azure utilità di pianificazione o fattori esterni, ad esempio le modifiche apportate a archiviazione BLOB e code di messaggi. Processi possono essere avviati e arrestati su richiesta e arrestati correttamente. Se un WebJob continuo non riesce, viene automaticamente riavviato. Possono essere configurate azioni di errore e riprovare.

Quando si configura un WebJob:

- Se si desidera il processo per rispondere a un trigger basate su eventi, è necessario configurarlo come **esecuzione continua**. Lo script o il programma è archiviato nella cartella denominata sito/wwwroot/app_data/processi/continua.
- Se si desidera il processo per rispondere a un trigger basata sulle risorse di programmazione, è necessario configurarlo come **esecuzione pianificata**. Lo script o il programma è archiviato nella cartella denominata sito/wwwroot/app_data/processi/attivato.
- Se si sceglie l'opzione **Esegui su richiesta** quando si configura un processo, eseguirà il codice stesso come l'opzione **Esegui alla programmazione di un** quando si avvia.

Azure WebJobs eseguito in modalità sandbox di web app. Ciò significa che saranno loro possono accedere alle variabili ambiente e condividere informazioni, ad esempio stringhe di connessione, con l'app web. Il processo ha accesso all'identificatore univoco del computer che esegue il processo. La stringa di connessione denominata **AzureWebJobsStorage** consente l'accesso code Azure dello spazio di archiviazione BLOB e tabelle dei dati delle applicazioni e accesso al servizio Bus per la messaggistica e comunicazione. La stringa di connessione denominata **AzureWebJobsDashboard** consente di accedere ai file di registro azione del processo.

Azure WebJobs hanno le caratteristiche seguenti:

- **Sicurezza**: WebJobs sono protetti tramite le credenziali di distribuzione di web app.
- **Tipi di file supportati**: È possibile definire WebJobs mediante gli script di comandi (cmd), file batch (bat), gli script di PowerShell (ps1), bash script di shell (.sh), gli script PHP (PHP), script Python (.py), codice JavaScript (js) e programmi eseguibili (.exe, JAR e altro).
- **Distribuzione**: È possibile distribuire script ed eseguibili tramite il portale di Azure, tramite il componente aggiuntivo [WebJobsVs](https://visualstudiogallery.msdn.microsoft.com/f4824551-2660-4afa-aba1-1fcc1673c3d0) per Visual Studio o [Visual Studio 2013 aggiornamento 4](http://www.visualstudio.com/news/vs2013-update4-rc-vs) (è possibile creare e distribuire con questa opzione), tramite [Azure WebJobs SDK](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)o se la copia direttamente nei seguenti percorsi:
  - Per attivate esecuzione: sito/wwwroot/app_data/processi/attivate / {processo nome}
  - Per l'esecuzione continua: sito/wwwroot/app_data/processi/continuo / {processo nome}
- **Registrazione**: console viene considerato (contrassegnato) INFO. Console.Error viene considerato come errore. È possibile accedere a informazioni di diagnostica e il monitoraggio tramite il portale di Azure. È possibile scaricare i file di log direttamente dal sito. Vengono salvati nei seguenti percorsi:
  - Per attivate esecuzione: Vfs/dati/processi/attivate/specificare nome di processo
  - Per l'esecuzione continua: Vfs/dati/processi/continuo/specificare nome di processo
- **Configurazione**: È possibile configurare WebJobs tramite il portale, API REST e PowerShell. È possibile usare un file di configurazione denominato settings.job nella stessa directory radice lo script dei processi per fornire informazioni di configurazione per un processo. Per esempio:
  - {"stopping_wait_time": 60}
  - {"is_singleton": true}

### <a name="considerations"></a>Considerazioni

- Per impostazione predefinita, WebJobs scala con web app. Tuttavia, è possibile configurare processi da eseguire in singola istanza impostando la proprietà di configurazione **is_singleton** su **true**. Singola istanza WebJobs sono utili per le attività che non si desidera ridimensionare o eseguire come simultaneo più istanze, ad esempio la reindicizzazione e analisi dei dati e attività simili.
- Per ridurre al minimo l'impatto dei processi sulle prestazioni dell'applicazione web, è consigliabile creare un'istanza di Azure Web App vuota in un nuovo piano di servizio App all'host WebJobs che potrebbe essere in esecuzione lungo o delle risorse in modo intensivo.

### <a name="more-information"></a>Ulteriori informazioni

- [Azure WebJobs consigliati risorse](./app-service-web/websites-webjobs-resources.md) sono elencati i molte risorse utili, download ed esempi per WebJobs.

## <a name="azure-cloud-services-web-and-worker-roles"></a>Ruoli di lavoro e web servizi Cloud Azure

È possibile eseguire attività in background all'interno di un ruolo web o in un ruolo di lavoro separato. Prima di decidere se si desidera utilizzare un ruolo di lavoro, è consigliabile scalabilità e la flessibilità requisiti, durata delle attività, rilasciare digitazione, sicurezza, tolleranza, conflitto, complessità e l'architettura logica. Per ulteriori informazioni, vedere [Il calcolo delle risorse consolidamento motivo](http://msdn.microsoft.com/library/dn589778.aspx).

Esistono diversi modi per implementare le attività in background all'interno di un ruolo di servizi Cloud:

- Creare un'implementazione della classe **RoleEntryPoint** il ruolo e utilizzare i metodi per eseguire attività in background. Le attività eseguite nel contesto di WaIISHost.exe. È possibile utilizzare il metodo **GetSetting** della classe **CloudConfigurationManager** per caricare le impostazioni di configurazione. Per ulteriori informazioni, vedere [ciclo di vita (Cloud Services)](#lifecycle-cloud-services).
- Usare le attività di configurazione per eseguire attività in background all'avvio dell'applicazione. Per forzare l'attività di restare in esecuzione in background, impostare la proprietà **taskType** su **sfondo** (se non esegue questa operazione, il processo di avvio verrà arresta e attendere il completamento dell'attività). Per ulteriori informazioni, vedere [eseguire attività di avvio in Azure](./cloud-services/cloud-services-startup-tasks.md).
- Utilizzare SDK WebJobs per implementare le attività in background, ad esempio WebJobs vengono avviate come un'attività di avvio. Per ulteriori informazioni, vedere [Guida introduttiva di Azure WebJobs SDK](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md).
- Utilizzare un'attività di avvio per installare un servizio di Windows che consente di eseguire una o più attività in background. È necessario impostare la proprietà **taskType** allo **sfondo** in modo che il servizio venga eseguita in background. Per ulteriori informazioni, vedere [eseguire attività di avvio in Azure](./cloud-services/cloud-services-startup-tasks.md).

### <a name="running-background-tasks-in-the-web-role"></a>Esecuzione di attività in background in ruolo web

Il vantaggio principale dell'esecuzione delle attività in background il ruolo web è il salvataggio in hosting costi perché non è necessario per distribuire ruoli aggiuntivi.

### <a name="running-background-tasks-in-a-worker-role"></a>Esecuzione di attività in background in un ruolo di lavoro

L'esecuzione di attività in background in un ruolo di lavoro offre diversi vantaggi:

- Consente di gestire le proporzioni dei caratteri separatamente per ogni tipo di ruolo. Ad esempio, potrebbe essere necessario più istanze di un ruolo web per supportare il carico corrente, ma le istanze di un numero minore del ruolo di lavoro che consente di eseguire attività in background. Ridimensionando le istanze di calcolo delle attività in background separatamente dai ruoli dell'interfaccia utente, è possibile ridurre i costi di hosting, mantenendo al contempo prestazioni accettabili.
- Libera il sovraccarico di elaborazione per le attività in background dal ruolo di web. Il ruolo web che fornisce l'interfaccia utente può rimanere attiva e potrebbe essere che meno istanze sono necessari per supportare un determinato volume richieste dagli utenti.
- È possibile implementare la separazione dei problemi. Ogni tipo di ruolo è possibile implementare un determinato set di attività chiaramente definito e correlati. In questo modo la progettazione e gestione del codice più semplice perché è minore interdipendenza codice e le funzionalità tra ogni ruolo.
- Può aiutare a isolare dati e processi riservati. Ad esempio, non è necessario che ruoli web implementano l'interfaccia utente ha accesso ai dati che viene gestiti e applicando un ruolo di lavoro. Può essere utile per rafforzare la protezione, soprattutto quando si usa un modello, ad esempio il [Motivo Gatekeeper](http://msdn.microsoft.com/library/dn589793.aspx).  

### <a name="considerations"></a>Considerazioni

Tenere presente quanto segue quando si sceglie dove e come distribuire le attività in background quando si utilizzano i ruoli di lavoro e web servizi Cloud:

- Che ospita le attività in background in un ruolo web esistente, è possibile salvare il costo di esecuzione di un ruolo di lavoro separato solo per le operazioni seguenti. Tuttavia, è probabile che siano esposte le prestazioni e disponibilità dell'applicazione se esiste un conflitto di elaborazione e altre risorse. Utilizzando un ruolo di lavoro separato consente di proteggere il ruolo web dall'impatto dell'attività in background lunga o risorse.
- Se si ospitano attività in background utilizzando la classe **RoleEntryPoint** , è possibile facilmente spostare a un altro ruolo. Ad esempio, se si crea la classe in un ruolo web e in seguito si decide che è necessario eseguire le attività in un ruolo di lavoro, è possibile spostare l'implementazione della classe **RoleEntryPoint** al ruolo di lavoro.
- Attività di avvio sono progettate per eseguire un programma o uno script. Distribuzione di un processo in background come programma eseguibile potrebbe essere più difficile, soprattutto se anche richiede l'esecuzione di distribuzione di assembly dipendenti. Potrebbe essere più semplice distribuire e utilizzare uno script per definire un processo in background quando si utilizza l'attività di avvio.
- Eccezioni che causano un'attività in background di abbiano un impatto diverso, a seconda nel modo che siano inclusi:
  - Se si utilizza l'approccio di classe **RoleEntryPoint** , un'operazione non riuscita causeranno il ruolo riavviare in modo che l'attività si riavvia automaticamente. Si possono influire sulla disponibilità dell'applicazione. Per evitare questo problema, assicurarsi di includere eccezioni affidabile all'interno della classe **RoleEntryPoint** e tutte le attività in background. Utilizzare codice per riavviare l'attività che hanno esito negativo nel punto in cui si tratta appropriata e che venga generata un'eccezione per riavviare il ruolo solo se non è possibile ripristinare correttamente in caso di errore all'interno del codice.
  - Se si usa l'attività di avvio, si è responsabile della gestione l'esecuzione di attività e la verifica in caso di errore.
- Gestione e il monitoraggio delle attività di avvio è difficoltà maggiori rispetto a quella di classe **RoleEntryPoint** . Tuttavia, Azure WebJobs SDK include un dashboard per rendere più semplice gestire WebJobs che si avvia le attività di avvio.

### <a name="more-information"></a>Ulteriori informazioni

- [Calcolare motivo consolidamento delle risorse](http://msdn.microsoft.com/library/dn589778.aspx)
- [Guida introduttiva di Azure WebJobs SDK](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)

## <a name="azure-virtual-machines"></a>Macchine virtuali di Azure

Attività in background potrebbero essere implementate in modo da impedirne che vengano distribuiti ad App Web di Azure o servizi Cloud oppure queste opzioni potrebbero non essere appropriate. Esempi tipici sono i servizi di Windows e utilità di terze parti e programmi eseguibili. Un altro esempio potrebbe essere programmi scritti per un ambiente di esecuzione diverso da quello che ospita l'applicazione. Ad esempio, potrebbe essere un programma Unix o Linux che si desidera eseguire da un'applicazione di Windows o .NET. È possibile scegliere da un intervallo di sistemi operativi per una macchina virtuale Azure ed eseguire il servizio o eseguibile sul computer virtuale.

Per informazioni su come scegliere quando utilizzare macchine virtuali, vedere [servizi App di Windows Azure, servizi Cloud e macchine virtuali di confronto](./app-service-web/choose-web-site-cloud-service-vm.md). Per informazioni sulle opzioni disponibili per macchine virtuali, vedere [dimensioni macchina virtuale e servizio Cloud per Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Per ulteriori informazioni sui sistemi operativi e immagini predefinite disponibili per macchine virtuali, vedere [Macchine virtuali di Azure Marketplace](https://azure.microsoft.com/gallery/virtual-machines/).

Per avviare attività in background in un computer virtuale separato, si dispone di un intervallo di opzioni:

- È possibile eseguire l'attività su richiesta direttamente dall'applicazione tramite l'invio di una richiesta di un estremo esposti l'attività. Questa passa tutti i dati che richiede l'attività. Questo endpoint richiama l'attività.
- È possibile configurare l'attività da eseguire in base alla pianificazione utilizzando un'utilità di pianificazione o timer è disponibile nel sistema operativo scelto. In Windows, ad esempio, è possibile utilizzare utilità di pianificazione Windows per eseguire script e attività. In alternativa, se è stato installato nel computer virtuale di SQL Server, è possibile utilizzare l'agente di SQL Server per eseguire script e attività.
- È possibile utilizzare l'utilità di pianificazione di Azure per avviare l'attività mediante l'aggiunta di un messaggio a una coda che l'attività è in ascolto o inviando una richiesta a un'API esposti l'attività.

Vedere la sezione precedente [trigger](#triggers) per ulteriori informazioni su come è possibile avviare attività in background.  

### <a name="considerations"></a>Considerazioni

Prima di decidere se si desidera distribuire le attività in background in un computer virtuale Azure, tenere presente quanto segue:

- Che ospita le attività in background in un computer virtuale separato Azure offre flessibilità e consente di controllare con precisione avvio, esecuzione, pianificazione e assegnazione delle risorse. Tuttavia, se verrà aumentata runtime costo una macchina virtuale devono essere distribuita solo per eseguire attività in background.
- Non è possibile monitorare le attività di Azure portale e alcuna possibilità di riavvio automatico di attività non riuscite, sebbene sia possibile controllare lo stato di base della macchina virtuale e gestire utilizzando i [Cmdlet per la gestione del servizio Azure](http://msdn.microsoft.com/library/azure/dn495240.aspx). Esistono tuttavia altre unità per controllare processi e thread in nodi di calcolo. In genere, utilizzando una macchina virtuale richiederà le risorse aggiuntive per implementare un meccanismo che consente di raccogliere dati da strumentazione nell'attività e dal sistema operativo nella macchina virtuale. Una soluzione che potrebbe essere appropriata consiste nell'usare [System Center Management Pack per Azure](http://technet.microsoft.com/library/gg276383.aspx).
- È consigliabile creare monitoraggio le ricerche che vengono esposte tramite endpoint HTTP. Il codice per le ricerche Impossibile eseguire verifiche integrità, raccogliere informazioni operative e le statistiche o fascicolare informazioni sugli errori e tornare a un'applicazione di gestione. Per ulteriori informazioni, vedere [Integrità Endpoint monitoraggio motivo](http://msdn.microsoft.com/library/dn589789.aspx).

### <a name="more-information"></a>Ulteriori informazioni

- [Macchine virtuali](https://azure.microsoft.com/services/virtual-machines/) su Azure
- [Domande frequenti su Azure macchine virtuali](virtual-machines/virtual-machines-linux-classic-faq.md)

## <a name="design-considerations"></a>Considerazioni sulla progettazione

Esistono vari fattori fondamentali da considerare quando si progettano le attività in background. Nelle sezioni seguenti partizioni, conflitti e relazione.

## <a name="partitioning"></a>Partizione

Se si decide di includere le attività in background all'interno di un'istanza di calcolo esistente (ad esempio un'app web, ruolo web, esistente ruolo di lavoro o macchina virtuale), è necessario considerare come questa interesserà gli attributi di qualità dell'istanza di elaborazione e attività in background stesso. Questi fattori consentono di decidere se colocate le attività con l'istanza di calcolo esistente o separare in un'istanza di elaborazione separata:

- **Disponibilità**: attività in Background potrebbero non essere per lo stesso livello di disponibilità come altre parti dell'applicazione, in particolare l'interfaccia utente e altre parti che partecipano direttamente all'interazione dell'utente. Attività in background potrebbero essere maggiore tolleranza di latenza, gli errori delle connessioni ritentato, e altri fattori che disponibilità influenza perché le operazioni possono essere accodate. Tuttavia, deve essere sufficiente per evitare che il backup di richieste che potrebbe bloccare code e influire sull'intera applicazione.
- **Scalabilità**: attività in Background sono potrebbe avere un requisito di scalabilità diverso rispetto all'interfaccia utente e le parti interattive dell'applicazione. Ridimensionamento dell'interfaccia utente potrebbe essere necessario soddisfare picchi della domanda, mentre attività in sospeso in background potrebbero essere completate durante meno disponibilità da una meno numero di istanze di calcolo.
- **Flessibilità**: errore di un'istanza di calcolo contenente solo le attività in background potrebbe non viene influiscono sull'applicazione nel suo insieme se le richieste di queste operazioni possono essere accodate o posticipate finché l'attività è disponibile nuovo. Se è possono riavviare l'istanza di elaborazione e/o le attività in un intervallo appropriato, gli utenti dell'applicazione potrebbero essere irrilevante.
- **Sicurezza**: attività in Background potrebbero avere requisiti di sicurezza diversi o restrizioni rispetto l'interfaccia utente o altre parti dell'applicazione. Utilizzando un'istanza di elaborazione separato, è possibile specificare un ambiente di protezione diversi per le attività. È anche possibile utilizzare i modelli, ad esempio Gatekeeper per isolare le istanze di elaborazione sfondo dall'interfaccia utente per ottimizzare la protezione e separazione.
- **Prestazioni**: È possibile scegliere il tipo di istanza di elaborazione per le attività in background alla corrispondenza specificamente i requisiti di prestazioni delle attività. Ciò potrebbe indicare usando un'opzione di calcolo meno costosa se le attività non richiede le stesse capacità di elaborazione come l'interfaccia utente o un'istanza di dimensioni maggiore se richiedono risorse e capacità.
- **Gestibilità**: attività in Background potrebbero avere un ritmo di sviluppo e la distribuzione diverso dall'interfaccia utente o il codice principale dell'applicazione. Distribuirle in un'istanza di elaborazione separato consente di semplificare gli aggiornamenti e controllo delle versioni.
- **Costo**: aggiunta di calcolare le istanze per l'esecuzione in background attività aumenta i costi di hosting. Valutare attentamente il compromesso tra capacità aggiuntive e i costi aggiuntivi.

Per ulteriori informazioni, vedere [Il modello di designazione carattere di riempimento](http://msdn.microsoft.com/library/dn568104.aspx) e [Concorrenti consumatori motivo](http://msdn.microsoft.com/library/dn568101.aspx).

## <a name="conflicts"></a>Conflitti

Se si dispone di più istanze di un processo in background, è possibile che saranno in conflitto per l'accesso alle risorse e servizi, ad esempio database e lo spazio di archiviazione. Accesso simultaneo causando conflitti tra le risorse, che potrebbero causare conflitti in disponibilità dei servizi e l'integrità dei dati in un archivio. È possibile risolvere i conflitti tra le risorse utilizzando un approccio blocco pessimistico. In questo modo concorrenti le istanze di un'attività dalla contemporaneamente accesso a un servizio o danneggiare i dati.

Per risolvere i conflitti anche per definire le attività in background come singleton, in modo che non vi è sempre un'unica istanza in esecuzione. Tuttavia, per evitare i vantaggi di affidabilità e le prestazioni in grado di fornire una configurazione più istanze. Questa operazione è particolarmente vera se l'interfaccia utente può fornire lavoro sufficiente per mantenere occupato più attività in background.

È fondamentale per garantire che le attività in background è possibile riavviare automaticamente e che abbia sufficiente per affrontare picchi della domanda. È possibile ottenere questo assegnando un'istanza di calcolo con risorse sufficienti, mediante l'implementazione di un meccanismo di accodamento che consentono di memorizzare le richieste di esecuzione successiva quando si ridotta richiesta o utilizzando una combinazione di queste tecniche.

## <a name="coordination"></a>Relazione

Le attività in background potrebbero essere complesse e possono richiedere più singole attività da eseguire per produrre un risultato o tutte le esigenze. Vengono in genere in questi scenari per dividere le attività in sottoattività che possono essere eseguite da più utenti o minori distinte passaggi. Più passaggi processi possono risultare più efficiente e più flessibile perché singoli passaggi possono essere riutilizzati più processi. È anche facile da aggiungere, rimuovere o modificare l'ordine dei passaggi.

Coordinare più attività e i passaggi può essere difficile, ma sono disponibili tre modelli comuni che è possibile utilizzare per l'implementazione di una soluzione di Guida:

- **La scomposizione di un'attività in più passaggi riutilizzabili**. Un'applicazione potrebbe essere necessario eseguire alcune attività di diversa complessità delle informazioni che vengono elaborati. Per eseguire questo tipo di elaborazione come modulo su potrebbe essere un approccio semplice ma non flessibile all'implementazione dell'applicazione. Tuttavia, questo approccio è soggetto a ridurre le opportunità per refactoring del codice, ottimizzandolo o il riutilizzo se parti dell'elaborazione della stessa sono necessarie in un' posizione all'interno dell'applicazione. Per ulteriori informazioni, vedere [Pipes e filtri motivo](http://msdn.microsoft.com/library/dn568100.aspx).
- **Gestione dell'esecuzione dei passaggi per un'attività**. Un'applicazione è possibile eseguire attività che comprendono numerosi passaggi (alcuni dei quali può richiamare i servizi remoti o accedere alle risorse remote). I singoli passaggi potrebbero essere indipendenti tra loro, ma sono coordinati dalla logica dell'applicazione che implementa l'attività. Per ulteriori informazioni, vedere [Utilità di pianificazione agente Supervisore motivo](http://msdn.microsoft.com/library/dn589780.aspx).
- **La gestione del ripristino per i passaggi dell'operazione che esito negativo**. Un'applicazione potrebbe essere necessario annullare il lavoro eseguito da una serie di passaggi (che contribuiscono a definire un'operazione non hanno un limite coerenza) se una o più delle operazioni esito negativo. Per ulteriori informazioni, vedere [Compensatori transazione motivo](http://msdn.microsoft.com/library/dn589804.aspx).

## <a name="lifecycle-cloud-services"></a>Ciclo di vita (Cloud Services)

 Se si decide di implementare processi in background per le applicazioni di servizi Cloud che utilizzano i ruoli web e di lavoro utilizzando la classe **RoleEntryPoint** , è importante conoscere il ciclo di vita di questa classe per poter usare correttamente.

Ruoli Web e lavoro accedere tramite un insieme di fasi distinte come iniziare, eseguire e Interrompi. La classe **RoleEntryPoint** espone una serie di eventi che indicano quando si verificano questi passaggi. Per utilizzare queste inizializzare, eseguire o arrestare l'attività in background personalizzato. L'intero ciclo è:

- Azure ricerche per una classe che deriva da **RoleEntryPoint**e carica assembly ruolo.
- Se vengono trovati questa classe, chiama **RoleEntryPoint.OnStart()**. Si esegue l'override di questo metodo per inizializzare l'attività in background.
- Al termine del metodo **OnStart** Azure chiamate a **Application_Start()** nel file globale dell'applicazione se questo presentano (ad esempio asax in un ruolo web che esegue ASP.NET).
- Azure chiama **RoleEntryPoint.Run()** su un nuovo thread in primo piano per l'esecuzione in parallelo con **OnStart()**. Si esegue l'override di questo metodo per avviare l'attività in background.
- Al termine del metodo di esecuzione, Azure chiama innanzitutto **Application_End()** nel file globale dell'applicazione se questo è presenta, quindi chiama **RoleEntryPoint.OnStop()**. Si esegue l'override del metodo **OnStop** per interrompere l'attività in background, pulire le risorse, eliminare gli oggetti e chiudere le connessioni che le attività potrebbero essere utilizzati.
- Il processo di host del ruolo di lavoro Azure viene interrotta. A questo punto, il ruolo sarà inserimento Cestino e verrà riavviato.

Per ulteriori informazioni e un esempio di utilizzo dei metodi della classe **RoleEntryPoint** , vedere [Calcolare motivo consolidamento delle risorse](http://msdn.microsoft.com/library/dn589778.aspx).

## <a name="considerations"></a>Considerazioni

Quando si pianifica la modalità di esecuzione attività in background in un ruolo web o lavoro, tenere presente quanto segue:

- L'impostazione predefinita **eseguire** implementazione del metodo nella classe **RoleEntryPoint** contiene una chiamata al **Infinite** che mantiene il ruolo attivo tempo indefinito. Se si esegue l'override il metodo **Run** (che è in genere è necessario eseguire attività in background), è necessario non consentire il codice uscire dal metodo a meno che non si vuole Cestino istanza del ruolo.
- Una tipica implementazione del metodo **Run** include codice per iniziare a ognuno dei dipendenti le attività in background e un costrutto di ciclo che verifica periodicamente se lo stato di tutte le attività in background. È possibile riavviare esito negativo o verificare la presenza di token di annullamento che indicano che processi è sono completata.
- Se un'attività in background genera un'eccezione non gestita, tale attività deve essere inserimento Cestino mentre altre attività di sfondo nel ruolo di continuare l'esecuzione. Tuttavia, se l'eccezione è causato dal danneggiamento di oggetti all'esterno di attività, ad esempio lo spazio di archiviazione condivisa, l'eccezione dovrebbe essere gestito per la classe **RoleEntryPoint** , tutte le attività devono essere annullate e il metodo **Run** deve essere consentito per terminare. Azure verrà quindi riavviare il ruolo.
- Utilizzare il metodo **OnStop** per sospendere o eliminare le attività in background e pulire le risorse. Questo potrebbe richiedere l'arresto attività lunga o più passaggi. È importante considerare come in questo modo è possibile per evitare incoerenze nei dati. Se si interrompe un'istanza del ruolo per qualsiasi motivo diverso da un arresto avviata dall'utente, il codice in esecuzione nel metodo **OnStop** deve essere completato entro 5 minuti prima di forzare la è terminata. Verificare che il codice può essere completato in un'ora specifica oppure tollerabile non è in esecuzione fino al completamento.  
- Servizio di bilanciamento del carico Azure avvia indirizza il traffico verso l'istanza di ruolo quando il metodo **RoleEntryPoint.OnStart** restituisce il valore **vero**. Pertanto, considerare posizionando il codice di inizializzazione nel metodo **OnStart** in modo che le istanze di ruolo ricoperto dai partecipanti che non viene eseguito correttamente al inizializzare non riceverà il traffico.
- È possibile utilizzare Avvio attività oltre i metodi della classe **RoleEntryPoint** . È necessario utilizzare attività di avvio per inizializzare le impostazioni che è necessario modificare nel sistema di bilanciamento del carico Azure perché queste attività verranno eseguito prima il ruolo riceve le richieste. Per ulteriori informazioni, vedere [eseguire attività di avvio in Azure](./cloud-services/cloud-services-startup-tasks.md).
- Se si verifica un errore in un'attività di avvio, è possibile forzare il ruolo di continuamente e riavviare. Poiché potrebbe impedire l'esecuzione di scambio di indirizzo IP (VIP) virtuale di una versione in precedenza a fasi perché lo scambio richiede l'accesso esclusivo al ruolo. Questa operazione non è possibile ottenere durante il riavvio del ruolo. Per risolvere il problema:
    -  Aggiungere il codice seguente all'inizio dei metodi **OnStart** ed **eseguire** il proprio ruolo:

    ```C#
    var freeze = CloudConfigurationManager.GetSetting("Freeze");
    if (freeze != null)
    {
        if (Boolean.Parse(freeze))
        {
            Thread.Sleep(System.Threading.Timeout.Infinite);
        }
    }
    ```

   - Aggiungere la definizione dell'impostazione **Blocca** come un valore Boolean alla ServiceDefinition.csdef e ServiceConfiguration. *cscfg file per il ruolo e impostarla su* *Falso* *. Se il ruolo entra in modalità di riavvio ripetuti, è possibile modificare l'impostazione in modo * *vero** per bloccare l'esecuzione di ruolo e consentire la scambiati con una versione precedente.

## <a name="resiliency-considerations"></a>Considerazioni sulla resilienza

Attività in background devono essere flessibile per fornire servizi affidabili all'applicazione. Quando si pianificano e creazione di attività in background, considerare quanto segue:

- Attività in background devono essere in grado di gestire correttamente riavvio del ruolo o servizio senza danneggiare dati o introduzione incoerenze nell'applicazione. Per le attività lunga o più passaggi, è preferibile _verificare che punta_ salvando lo stato dei processi nell'archivio permanente o come messaggi in una coda se si tratta appropriata. Ad esempio, è possibile mantenere le informazioni sullo stato in un messaggio in una coda e aggiornare in modo incrementale in queste informazioni sullo stato con lo stato di avanzamento delle attività in modo che l'attività può essere elaborato dal punto di ultima noto buona arresto - anziché il riavvio dall'inizio. Quando si usa code Bus di servizio Azure, è possibile utilizzare le sessioni di messaggistica per consentire lo stesso scenario. Sessioni consentono di salvare e recuperare lo stato di elaborazione dell'applicazione tramite i metodi [SetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) e [GetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx) . Per ulteriori informazioni sulla progettazione flussi di lavoro e affidabili processi più passaggi, vedere [Utilità di pianificazione agente Supervisore motivo](http://msdn.microsoft.com/library/dn589780.aspx).
- Quando si usa web o lavoro ruoli per ospitare più attività in background, progettare l'override del metodo **eseguire** per verificare la presenza di attività bloccata o non riuscita e riavviare i. Se non si tratta pratica e si utilizza un ruolo di lavoro, forzare il ruolo di lavoro per riavviare uscendo dal metodo **eseguire** .
- Quando si utilizzano code per comunicare con le attività in background, code può essere utilizzato come un buffer per memorizzare le richieste inviate le attività durante l'applicazione è in più comune carico elevato. In questo modo delle attività di riunione con l'interfaccia utente meno periodi di picco. Inoltre, significa che il ruolo di riciclaggio non bloccare l'interfaccia utente. Per ulteriori informazioni, vedere [modello di livellamento carico basato su coda](http://msdn.microsoft.com/library/dn589783.aspx). Se alcune attività sono più importanti di altri utenti, è possibile implementare [Priorità coda motivo](http://msdn.microsoft.com/library/dn589794.aspx) per garantire che le attività seguenti eseguire prima di quelli meno importanti.
- Attività in background avviate dal processo messaggi o i messaggi devono essere progettate per gestire le incoerenze, ad esempio messaggi in arrivo nell'ordine corretto, i messaggi che causano un errore (detti per _i messaggi danneggiati_) e i messaggi inviati più volte. Tenere presente quanto segue:
  - Messaggi che devono essere elaborati in un ordine specifico, ad esempio quelli che modificano i dati in base al valore di dati esistente (ad esempio l'aggiunta di un valore a un valore esistente), non possono provenire da ordine originale in cui sono stati inviati. In alternativa, potrebbe essere gestite da diverse istanze di un'attività in background in un ordine diverso a causa di vari livelli di carico in ogni istanza. I messaggi che devono essere elaborati in un ordine specifico devono includere un numero di sequenza, chiave o alcuni altri indicatore di attività in background è possono utilizzare per assicurarsi che vengono elaborate nell'ordine corretto. Se si utilizza Bus di servizio Azure, è possibile utilizzare le sessioni di messaggistica per garantire l'ordine di recapito. Tuttavia, è in genere più efficace, se possibile, per progettare il processo in modo che l'ordine di messaggio non è importante.
  - In genere, un'attività in background verrà leggere i messaggi nella coda, ovvero nascondere temporaneamente da altri utenti di messaggio. Quindi Elimina i messaggi dopo che sono state elaborate correttamente. Se un'attività in background non riesce durante l'elaborazione di un messaggio, i messaggi verranno visualizzati nuovamente nella coda dopo scade il timeout di anteprima. Verrà elaborata da un'altra istanza dell'attività o successivo ciclo di elaborazione di questa istanza. Se il messaggio in modo coerente genera un errore nel consumer, non viene interrotto l'attività, coda e alla fine dell'applicazione quando la coda è pieno. È fondamentale per rilevare e rimuovere i messaggi danneggiati dalla coda. Se si utilizza Bus di servizio Azure, i messaggi che causano un errore possono essere spostati automaticamente o manualmente a una coda di lettera inattiva associato.
  - Code sono garantite _almeno una volta_ meccanismi di consegna, ma potrebbe offrire lo stesso messaggio più volte. Inoltre, se un'attività in background non funziona dopo l'elaborazione di un messaggio ma prima di eliminare dalla coda, il messaggio sarà disponibile per l'elaborazione di nuovo. Attività in background dovrebbero essere idempotente, il che significa che l'elaborazione lo stesso messaggio venga ripetuto non causano un errore o incoerenze nei dati dell'applicazione. Alcune operazioni sono naturalmente idempotente, ad esempio l'impostazione di un valore memorizzato su un nuovo valore specifico. Operazioni, ad esempio l'aggiunta di un valore per un valore memorizzato presente senza verificare che il valore memorizzato è sempre uguale quando il messaggio originale è stato inviato, tuttavia, si verificherà incoerenze. Azure code Bus di servizio possono essere configurate per rimuovere automaticamente i messaggi duplicati.
  - Alcuni sistemi di messaggistica, ad esempio lo spazio di archiviazione Azure code e Bus di servizio Azure, supportano una proprietà di conteggio de-queue che indica il numero di volte in cui che un messaggio è stato letto dalla coda. Può essere utile per la gestione dei messaggi ripetuti e danneggiati. Per ulteriori informazioni, vedere [Nozioni di base di messaggistica asincrono](http://msdn.microsoft.com/library/dn589781.aspx) e [Motivi idempotenza](http://blog.jonathanoliver.com/2010/04/idempotency-patterns/).

## <a name="scaling-and-performance-considerations"></a>Considerazioni sulla scalabilità e prestazioni

Attività in background devono offrire prestazioni sufficienti per assicurarsi che non blocca l'applicazione, o se il sistema in condizioni di carico causare incoerenze a causa di operazione posticipato. In genere, migliorare le prestazioni ridimensionando le istanze di calcolo che ospitano le attività in background. Quando si pianificano e creazione di attività in background, considerare i seguenti punti intorno a prestazioni e scalabilità:

- Azure supporta il ridimensionamento automatico (scalabilità e proporzioni dei caratteri in) sulla base della domanda corrente e carico - o in un programma predefinito per le applicazioni Web, di web servizi Cloud e ruoli di lavoro e macchine virtuali di contenuti distribuzioni. Utilizzare questa caratteristica per assicurarsi che l'applicazione nel suo insieme è sufficienti le prestazioni riducendo al minimo i costi di runtime.
- Se le attività in background hanno una funzionalità di prestazioni diversi dalle altre parti di un'applicazione di servizi Cloud (ad esempio, l'interfaccia utente o componenti, ad esempio il livello di accesso ai dati), che ospita le attività in background in un ruolo di lavoro separato consente l'interfaccia utente e sfondo ruoli di attività da scalare in modo indipendente per gestire il carico. Se più attività in background le prestazioni in modo significativo diversi tra loro, valutare se diviso in ruoli di lavoro separato e proporzioni dei caratteri in modo indipendente ogni tipo di ruolo. Tuttavia, si noti che questa potrebbe aumentare i costi di runtime rispetto alla combinazione di tutte le attività in meno ruoli.
- È sufficiente scala i ruoli potrebbe non essere sufficiente per evitare la perdita delle prestazioni in condizioni di carico. È anche necessario ridimensionare code lo spazio di archiviazione e altre risorse per impedire un unico punto dell'intero catena da più ottimale di elaborazione. Inoltre, prendere in considerazione altre limitazioni, ad esempio la velocità massima dello spazio di archiviazione e altri servizi che l'applicazione e le attività in background si basano su.
- Attività in background devono essere progettate per il ridimensionamento. Ad esempio, sono deve essere in grado di rilevare dinamicamente il numero di code di spazio di archiviazione in uso per ascolto o inviare messaggi alla coda appropriata.
- Per impostazione predefinita, scala WebJobs con l'istanza di Azure Web Apps associato. Tuttavia, se si desidera WebJob per l'esecuzione come una singola istanza, è possibile creare un file di Settings.job che contiene i dati JSON **{"is_singleton": true}**. In questo modo Azure eseguire solo una sola istanza di WebJob, anche se sono presenti più istanze dell'app web associato. Può trattarsi di una tecnica utile per programmate che deve essere eseguito come una singola istanza.

## <a name="related-patterns"></a>Modelli correlati

- [Nozioni di base sulla messaggistica asincrono](http://msdn.microsoft.com/library/dn589781.aspx)
- [Indicazioni per il ridimensionamento automatico](http://msdn.microsoft.com/library/dn589774.aspx)
- [Motivo transazione compensatori](http://msdn.microsoft.com/library/dn589804.aspx)
- [Motivo consumatori concorrenti](http://msdn.microsoft.com/library/dn568101.aspx)
- [Calcolare partizione indicazioni](http://msdn.microsoft.com/library/dn589773.aspx)
- [Calcolare motivo consolidamento delle risorse](http://msdn.microsoft.com/library/dn589778.aspx)
- [Motivo gatekeeper](http://msdn.microsoft.com/library/dn589793.aspx)
- [Modello di designazione carattere di riempimento](http://msdn.microsoft.com/library/dn568104.aspx)
- [Modello di filtri e Pipes](http://msdn.microsoft.com/library/dn568100.aspx)
- [Priorità coda motivo](http://msdn.microsoft.com/library/dn589794.aspx)
- [Carico basato su coda livellamento motivo](http://msdn.microsoft.com/library/dn589783.aspx)
- [Utilità di pianificazione agente Supervisore motivo](http://msdn.microsoft.com/library/dn589780.aspx)

## <a name="more-information"></a>Ulteriori informazioni

- [Ridimensionamento delle applicazioni Azure con ruoli di lavoro](http://msdn.microsoft.com/library/hh534484.aspx#sec8)
- [Esecuzione di attività in Background](http://msdn.microsoft.com/library/ff803365.aspx)
- [Ciclo di vita avvio ruolo Azure](http://blog.syntaxc4.net/post/2011/04/13/windows-azure-role-startup-life-cycle.aspx) (post di blog)
- [Ciclo di vita del ruolo Servizi Cloud Azure](http://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Windows-Azure-Cloud-Services-Role-Lifecycle) (video)
- [Guida introduttiva di Azure SDK WebJobs](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
- [Azure code e servizio Bus - confrontati e rispetto](./service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Come abilitare diagnostica in un servizio Cloud](./cloud-services/cloud-services-dotnet-diagnostics.md)
