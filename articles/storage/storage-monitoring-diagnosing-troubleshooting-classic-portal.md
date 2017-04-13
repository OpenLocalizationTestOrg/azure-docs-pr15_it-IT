<properties
    pageTitle="Monitorare, diagnosticare e risolvere i problemi di spazio di archiviazione | Microsoft Azure"
    description="Utilizzare caratteristiche quali analitica lo spazio di archiviazione, la registrazione lato client e altri strumenti di terze parti per identificare, diagnosticare e risolvere i problemi di archiviazione Azure."
    services="storage"
    documentationCenter=""
    authors="jasonnewyork"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="jahogg"/>

# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Monitorare, diagnosi e risolvere i problemi di spazio di archiviazione di Microsoft Azure

[AZURE.INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Panoramica

La diagnosi e risoluzione dei problemi in un'applicazione distribuita ospitato in un ambiente cloud può essere più complesse in ambienti tradizionali. Applicazioni possono essere distribuite in un'infrastruttura PaaS o IaaS in locale in un dispositivo mobile o in una combinazione di questi piani. In genere, il traffico di rete dell'applicazione potrebbe attraversano reti pubbliche e privatehttp e l'applicazione può utilizzare più tecnologie di archiviazione, ad esempio tabelle di Microsoft Azure archiviazione BLOB, code o file oltre ad altri dati archivia ad esempio relazionale e database di documenti.

Per la gestione di tali applicazioni correttamente è necessario eseguire il monitoraggio delle loro tempestiva e si conoscono come individuare e risolvere i problemi di tutti gli aspetti e le tecnologie. Come utente del servizio di archiviazione di Azure, è sempre deve monitorare i servizi di archiviazione che l'applicazione utilizza per modifiche imprevisti del comportamento (ad esempio inferiore i tempi di risposta comune) e utilizzare la registrazione per la raccolta di dati più dettagliati e analizzare un problema in modo approfondito. Le informazioni di diagnostica che i presso il monitoraggio e la registrazione verranno consentono di determinare la causa del problema rilevato dell'applicazione. È quindi possibile risolvere il problema e determinare le procedure appropriate per risolvere il. Archiviazione Azure è un servizio Azure principale e moduli di una parte importante della maggior parte delle soluzioni clienti distribuiscono l'infrastruttura di Azure. Spazio di archiviazione Azure include funzionalità per semplificare il monitoraggio, la diagnosi e risoluzione dei problemi di spazio di archiviazione in applicazioni basate su cloud.

> [AZURE.NOTE] Gli account di archiviazione a un tipo di replica di spazio di archiviazione zona ridondanti (ZRS) non si hanno la metrica o la funzionalità di registrazione abilitata al momento. 

Per una guida pratica alla risoluzione dei problemi-to-end nelle applicazioni di archiviazione Azure, vedere [To-End risoluzione dei problemi con Azure metriche di archiviazione e la registrazione, AzCopy e analisi di messaggio](storage-e2e-troubleshooting.md).

+ [Introduzione]
    + [Organizzazione di questa Guida]
+ [Monitorare il servizio di archiviazione]
    + [Monitorare l'integrità dei servizi]
    + [Monitoraggio della capacità]
    + [Il controllo della disponibilità]
    + [Monitoraggio delle prestazioni]
+ [Diagnosticare i problemi di archiviazione]
    + [Problemi di integrità dei servizi]
    + [Problemi di prestazioni]
    + [Diagnostica degli errori]
    + [Problemi di emulatore lo spazio di archiviazione]
    + [Strumenti di registrazione di spazio di archiviazione]
    + [Utilizzo degli strumenti di registrazione di rete]
+ [Analisi di fine-fine]
    + [Correlazione di dati dei registri]
    + [ID richiesta client]
    + [ID richiesta server]
    + [Data e ora]
+ [Indicazioni per la risoluzione dei problemi]
    + [Metrica Mostra AverageE2ELatency alta e bassa AverageServerLatency]
    + [Metriche mostrano AverageE2ELatency bassa e AverageServerLatency bassa, ma il client si è verificato latenza elevata]
    + [Metrica Mostra AverageServerLatency elevato]
    + [Si sono verificati ritardi imprevisti in recapito dei messaggi in una coda]
    + [Metrica Mostra un aumento nel PercentThrottlingError]
    + [Metrica Mostra un aumento nel PercentTimeoutError]
    + [Metrica Mostra un aumento nel PercentNetworkError]
    + [Il client sta ricevendo messaggi HTTP 403 (accesso negato)]
    + [Il client sta ricevendo messaggi HTTP 404 (non disponibile)]
    + [Il client sta ricevendo messaggi HTTP 409 (conflitto)]
    + [Metrica Mostra PercentSuccess bassa o voci di log analitica avere operazioni con stato ClientOtherErrors]
    + [Metrica capacità Mostra un aumento imprevisto in utilizzo della capacità di archiviazione]
    + [Si verifica il riavvio imprevisto di macchine virtuali che dispone di un numero elevato di dischi rigidi virtuali allegati]
    + [Il problema si verifica da tramite emulatore lo spazio di archiviazione per lo sviluppo o test]
    + [Si problemi durante l'installazione di Azure SDK per .NET]
    + [Si dispone di un problema diverso relativo a un servizio di archiviazione]
+ [Appendici]
    + [Appendice 1: Con Fiddler per acquisire il traffico HTTP e HTTPS]
    + [Appendice 2: Utilizzando Wireshark per acquisire il traffico di rete]
    + [Appendice 3: Utilizzando Microsoft messaggio Analyzer per acquisire il traffico di rete]
    + [Appendice 4: Utilizzo di Excel per visualizzare le metriche e registrare i dati]
    + [Appendice 5: Monitoraggio con informazioni dettagliate sui applicazione per Visual Studio Team Services]

## <a name="introduction"></a>Introduzione

Questa guida viene illustrato come utilizzare caratteristiche quali Analitica lo spazio di archiviazione di Azure, i problemi di accesso, la raccolta di Client lo spazio di archiviazione di Azure e altri strumenti di terze parti per identificare, diagnosticare e risolvere i problemi di archiviazione Azure sul lato client.

![][1]

*Figura 1 monitoraggio, diagnostica e risoluzione dei problemi*

Questa guida è rivolta a essere letti principalmente agli sviluppatori di servizi online che utilizzano i servizi di archiviazione Windows Azure e i professionisti IT responsabile della gestione di tali servizi online. Gli obiettivi di questa guida sono:

- Per mantenere l'integrità e le prestazioni degli account di archiviazione Azure.
- Per fornire i processi necessari e strumenti per informazioni su come determinare se un problema in un'applicazione si riferisce allo spazio di archiviazione Azure.
- Per fornire Guida per la risoluzione dei problemi relativi allo spazio di archiviazione Azure.

### <a name="how-this-guide-is-organized"></a>Organizzazione di questa Guida

La sezione "[monitoraggio il servizio di archiviazione di]" viene descritto come monitorare l'integrità e le prestazioni dei servizi archiviazione Azure con Azure archiviazione Analitica metriche (metriche di archiviazione).

La sezione "[diagnosi dei problemi di archiviazione]" viene descritto come diagnosticare i problemi di utilizzo Azure archiviazione Analitica Logging (registrazione di spazio di archiviazione). Viene inoltre descritto attivare la registrazione lato client utilizzando le funzioni in una delle raccolte client, ad esempio la raccolta di Client lo spazio di archiviazione per .NET o Azure SDK per Java.

La sezione "[analisi - to-end]" descrive come è possibile creare una relazione tra le informazioni contenute in vari file di log e dati di metrica.

La sezione "[indicazioni per la risoluzione dei problemi]" fornisce indicazioni per la risoluzione dei problemi per alcuni dei problemi relativi a spazio di archiviazione comuni che possono verificarsi.

"[Appendici]" includono informazioni sull'uso di altri strumenti, ad esempio Netmon e Wireshark per l'analisi dei dati nel pacchetto di rete, Fiddler per l'analisi dei messaggi HTTP/HTTPS e Microsoft messaggio Analyzer per correlare i dati del registro.


## <a name="monitoring-your-storage-service"></a>Monitorare il servizio di archiviazione

Se si ha familiarità con il monitoraggio delle prestazioni di Windows, è possibile pensare di metriche di archiviazione come un equivalente di archiviazione Azure contatori Performance Monitor di Windows. In metriche di archiviazione sono disponibili una serie completa di metriche (contatori nella terminologia di Performance Monitor di Windows), ad esempio la disponibilità dei servizi, numero totale di richieste al servizio, o una percentuale di richieste completate al servizio (per un elenco completo dei parametri disponibili, vedere <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">Schema tabella metriche di archiviazione Analitica</a> su MSDN). È possibile specificare se si desidera che il servizio di archiviazione per raccogliere e aggregare metriche ogni ora o minuto. Per ulteriori informazioni su come abilitare metriche e monitorare l'account di archiviazione, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=510865" target="_blank">metriche di archiviazione di attivazione</a> su MSDN.

È possibile scegliere quali metriche orarie che si desidera visualizzare nel portale classica di Azure e configurare le regole per informare gli amministratori da inviare tramite posta elettronica ogni volta che un'unità di misura metriche oraria supera una determinata soglia (per ulteriori informazioni, vedere la pagina <a href="http://msdn.microsoft.com/library/azure/dn306638.aspx" target="_blank">come: ricevere notifiche di avviso e gestire le regole di avviso in Azure</a>). Il servizio di archiviazione raccoglie metriche usando migliore, ma non è possibile registrare ogni operazione di archiviazione.

2 nella figura seguente illustra la pagina Monitor nel portale classica di Azure in cui è possibile visualizzare metriche, ad esempio disponibilità, le richieste di totali e numeri sulla latenza media per un account di archiviazione. Una regola di notifica è stata impostata per avvisare un amministratore se disponibilità inferiore a un determinato livello. Visualizzazione dati, da un'area possibili per l'analisi corrisponde alla percentuale di successo di servizio tabella viene inferiore al 100% (per ulteriori informazioni, vedere la sezione "[metriche mostrano PercentSuccess bassa o voci di log analitica avere operazioni con lo stato di ClientOtherErrors transazione]").

![][2]

*Figura 2 visualizzazione metriche di archiviazione nel portale classica di Azure*

È sempre necessario monitorare le applicazioni Azure per assicurarsi che siano corretti ed esecuzione come previsto dal:

- Definizione di alcune metrica di base per l'applicazione che consente di confrontare i dati correnti e identificare modifiche significative al comportamento di Azure dello spazio di archiviazione e l'applicazione. I valori dai parametri di previsione saranno, in molti casi specifici dell'applicazione ed è necessario stabilire quando si verifica l'applicazione delle prestazioni.
- Registrazione metriche minute e il relativo utilizzo per attivamente verificare la presenza di errori e alterazioni, ad esempio picchi errore conta o richiedere tariffe.
- Registrazione orarie metriche e utilizzarli per monitorare i valori Media come calcolare la media di conteggi di errori e richiedere tariffe.
- In analisi potenziali problemi con gli strumenti di diagnostica come illustrato in seguito nella sezione "[diagnosi dei problemi di archiviazione]".

I tipi di grafici nella figura 3 riportata di seguito viene illustrato come Media che si verifica per le metriche orarie possibile nascondere picchi nell'attività. Metrica oraria vengono visualizzate per mostrare una velocità costante delle richieste, mentre il minuto metriche rivelare il variazioni che sono effettivamente eseguita.

![][3]

Il resto di questa sezione descrive le metriche monitorare e perché.

### <a name="monitoring-service-health"></a>Monitorare l'integrità dei servizi

È possibile usare il [Portale classica Azure](https://manage.windowsazure.com) per visualizzare lo stato del servizio di archiviazione (e altri servizi di Azure) in tutte le aree Azure tutto il mondo. In questo modo è possibile vedere immediatamente se un problema all'esterno del controllo riguarda il servizio di archiviazione nell'area che utilizzare per l'applicazione.

Portale classica di Azure anche possibile con le notifiche di problemi che influiscono su diversi servizi di Azure.
Nota: Queste informazioni erano precedentemente disponibile, insieme ai dati cronologici nel Dashboard di servizio di Azure in <a href="http://status.azure.com" target="_blank">http://status.azure.com</a>.

Mentre il portale classica Azure raccoglie informazioni integrità all'interno di Azure Data Center (interno fuori monitoraggio), è inoltre possibile adottare un approccio per l'accesso esterno per generare transazioni sintetiche che accedono periodicamente l'applicazione web ospitato Azure da più percorsi. I servizi offerti da <a href="http://www.keynote.com/solutions/monitoring/web-monitoring" target="_blank">note</a>, <a href="https://www.gomeznetworks.com/?g=1" target="_blank">compresi Gomez</a>e applicazione approfondimenti per Visual Studio Team Services sono illustrati esempi di questo approccio di esterno. Per ulteriori informazioni sull'applicazione approfondimenti per Visual Studio Team Services, vedere l'appendice "[appendice 5: monitoraggio con applicazione approfondimenti per Visual Studio Team Services]."

### <a name="monitoring-capacity"></a>Monitoraggio della capacità

Metriche di archiviazione memorizza solo metriche capacità per il servizio di blob perché BLOB account in genere per proporzione più grande di dati memorizzati (in fase di scrittura, non è possibile usare metriche di archiviazione per controllare la capacità delle tabelle e code). È possibile trovare i dati nella tabella **$MetricsCapacityBlob** se è stata attivata monitoraggio per il servizio Blob. Metriche di archiviazione registra i dati una volta al giorno ed è possibile utilizzare il valore di **RowKey** per determinare se la riga contiene un'entità che si riferisce a dati utente (dei valori **dei dati**) o analitica (valore **analitica**). Ogni entità archiviata contiene informazioni la quantità di spazio di archiviazione utilizzato (**capacità** misurato in byte) e il numero corrente della contenitori (**ContainerCount**) e BLOB (**ObjectCount**) in uso nell'account di archiviazione. Per ulteriori informazioni su metriche capacità archiviate nella tabella **$MetricsCapacityBlob** , vedere <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">Schema tabella metriche di archiviazione Analitica</a> su MSDN.

> [AZURE.NOTE] Monitorare i valori di un avviso primo che si stanno per raggiungere i limiti di capacità del proprio account di archiviazione. Nel portale classica di Azure, nella pagina **Monitor** per il proprio account di archiviazione, è possibile aggiungere regole di avviso per la notifica da se usare lo spazio di archiviazione aggregazione superiore o inferiore le soglie specificate.

Per informazioni su come la stima delle dimensioni dei diversi oggetti dello spazio di archiviazione, ad esempio BLOB, vedere il blog <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx" target="_blank">Understanding Azure archiviazione fatturazione – della larghezza di banda, le transazioni e capacità</a>.

### <a name="monitoring-availability"></a>Il controllo della disponibilità

Monitorare la disponibilità dei servizi di archiviazione nell'account di archiviazione controllando il valore nella colonna **disponibilità** nelle tabelle metriche ore o minuti, ovvero **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. La colonna di **disponibilità** contiene un valore percentuale che indica la disponibilità del servizio o l'operazione di API rappresentata dalla riga (il **RowKey** viene visualizzato se la riga contiene le metriche per il servizio nel suo insieme o per una specifica operazione API).

Qualsiasi valore minore di 100% indica che alcune richieste di spazio di archiviazione hanno esito negativo. È possibile vedere perché non riescono esaminando le altre colonne nei dati di metriche che mostrano il numero di richieste con tipi di errore diverso, ad esempio **ServerTimeoutError**. È necessario prevedere che il **disponibilità** appaiono temporaneamente inferiore al 100% per motivi, ad esempio il timeout del server temporaneo mentre il servizio passa partizioni alla richiesta di bilanciamento del carico maggiore; la logica di ripetizione nell'applicazione client dovrà gestire tali condizioni intermittente. La pagina <a href="http://msdn.microsoft.com/library/azure/hh343260.aspx" target="_blank"></a> sono elencati i tipi di transazione contenente metriche di archiviazione per il calcolo **della disponibilità** .

Nel portale classica di Azure, nella pagina **Monitor** per il proprio account di archiviazione, è possibile aggiungere regole di avviso per la notifica di **disponibilità** di un servizio cade soglia specificata.

La sezione "[indicazioni per la risoluzione dei problemi]" di questa guida vengono illustrati alcuni problemi comuni di servizio di archiviazione relative alla disponibilità.

### <a name="monitoring-performance"></a>Monitoraggio delle prestazioni

Per eseguire il monitoraggio delle prestazioni dei servizi di archiviazione, è possibile utilizzare le seguenti metriche dalle tabelle metriche ore e minuti.

- I valori nelle **AverageE2ELatency** e **AverageServerLatency** mostrano il tempo medio il servizio di archiviazione o il tipo di operazione API richiede per elaborare le richieste. **AverageE2ELatency** è una misura della latenza-to-end che include il tempo impiegato per leggere la richiesta e inviare la risposta oltre il tempo necessario per elaborare la richiesta (pertanto sono inclusi latenza di rete quando la richiesta raggiunge il servizio di archiviazione); **AverageServerLatency** è una misura il tempo di elaborazione e pertanto esclude qualsiasi latenza di rete correlato a comunicare con il cliente. Vedere la sezione "[metriche mostrano AverageE2ELatency alta e bassa AverageServerLatency]" più avanti in questa guida per una descrizione delle perché potrebbero esserci una differenza significativa tra questi due valori.
- I valori nelle colonne **TotalIngress** e **TotalEgress** indicano la quantità totale di dati, in byte, entrata e in fuori il servizio di archiviazione o tramite un tipo di operazione API specifico.
- I valori nella colonna **TotalRequests** indicano il numero totale di richieste di tipo che riceve il servizio di archiviazione dell'operazione di API. **TotalRequests** è il numero totale di richieste che riceve il servizio di archiviazione.

In genere monitorare le modifiche impreviste in uno di questi valori come un indicatore si dispone di un problema che richiede indagini.

Nel portale classica di Azure, nella pagina **Monitor** per il proprio account di archiviazione, è possibile aggiungere regole di avviso per la notifica da se uno qualsiasi di misurazione delle prestazioni per il servizio scendere di sotto o superano la soglia specificata.

La sezione "[indicazioni per la risoluzione dei problemi]" di questa guida vengono illustrati alcuni problemi comuni di servizio di archiviazione correlati alle prestazioni.


## <a name="diagnosing-storage-issues"></a>Diagnosticare i problemi di archiviazione

Esistono diversi modi che ci si potrebbe rende conto di un problema segnalato nell'applicazione, ad esempio:

- Errore principale che causa l'applicazione in modo anomalo o smettere di funzionare.
- Modifiche significative dai valori previsto le metriche sta controllando come descritto nella sezione precedente "[monitoraggio il servizio di archiviazione]".
- Report dagli utenti dell'applicazione che alcune operazioni particolare non è stata completata nel modo previsto o che alcune funzionalità non è valida.
- Errori all'interno dell'applicazione che vengono visualizzati nei file di log o tramite un altro metodo di notifica.

Problemi relativi ai servizi di archiviazione Azure rientrano in genere, in uno dei quattro categorie generali:

- L'applicazione ha un problema di prestazioni, segnalati dagli utenti o animato utilizzando le modifiche in misurazioni sulle prestazioni.
- Esiste un problema con l'infrastruttura di archiviazione Azure in una o più aree.
- L'applicazione è causa di un errore, segnalati dagli utenti o animato utilizzando un aumento in uno dei è monitorare le metriche di conteggio errore.
- Durante lo sviluppo e test, è possibile utilizzare emulatore archivio locale; è possibile riscontrare alcuni problemi che si riferiscono specificamente per l'utilizzo dell'emulatore lo spazio di archiviazione.

Le sezioni che seguono la procedura è necessario seguire per la diagnosi e risoluzione dei problemi di ognuna di queste quattro categorie. La sezione "[indicazioni per la risoluzione dei problemi]" più avanti in questa guida offre informazioni più dettagliate per alcuni problemi comuni che possono verificarsi.

### <a name="service-health-issues"></a>Problemi di integrità dei servizi

Problemi di integrità dei servizi sono in genere all'esterno del controllo. Portale classica di Azure fornisce informazioni sulle eventuali problemi in corso con Azure servizi inclusi quelli dello spazio di archiviazione. Se si scelto per l'accesso in lettura geografico ridondanti archiviazione durante la creazione dell'account di archiviazione, quindi in caso di dati che non è disponibile nel percorso principale, l'applicazione possibile passare temporaneamente la copia di sola lettura nel percorso secondario. A tale scopo, l'applicazione deve essere in grado di cambiare con i percorsi di archiviazione primario e secondario ed essere in grado di lavorare in modalità con funzionalità ridotte con dati di sola lettura. Le raccolte di Azure lo spazio di archiviazione Client consentono di definire i criteri di Riprova in grado di leggere dallo spazio di archiviazione secondario nel caso in cui si verifica un errore di lettura dallo spazio di archiviazione principale. L'applicazione deve inoltre tenere presente che i dati nella posizione secondaria non hanno un limite coerenti. Per ulteriori informazioni, vedere il blog di registrare <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/04/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx" target="_blank">le opzioni di ridondanza di archiviazione Azure e lo spazio di archiviazione di accesso in lettura geografico ridondanti</a>.

### <a name="performance-issues"></a>Problemi di prestazioni

Le prestazioni di un'applicazione possono essere soggettiva, in particolare dal punto di vista utente. Pertanto, è importante avere disponibili per identificare più facilmente la metrica di base in cui potrebbe essersi verificato un problema di prestazioni. Molti fattori potrebbero rallentare le prestazioni di un servizio di archiviazione Azure dal punto di vista dell'applicazione client. Questi fattori potrebbero funzionare il servizio di archiviazione, il client o dell'infrastruttura di rete; pertanto è importante avere una strategia per identificare l'origine del problema di prestazioni.

Dopo aver identificato il percorso probabilmente della causa del problema di prestazioni dalle metriche, quindi utilizzare i file di log per trovare informazioni dettagliate per la diagnosi e risoluzione dei problemi ulteriormente il problema.

La sezione "[indicazioni per la risoluzione dei problemi]" più avanti in questa guida sono disponibili ulteriori informazioni sulle prestazioni alcune comuni relativi i problemi che potrebbe verificarsi.

### <a name="diagnosing-errors"></a>Diagnostica degli errori

Gli utenti dell'applicazione potrebbero invio di una notifica degli errori segnalati dall'applicazione client. Metriche di archiviazione registrano anche conteggi dei tipi di errore diverso dai servizi di archiviazione, ad esempio **NetworkError**, **ClientTimeoutError**o **AuthorizationError**. Mentre metriche di archiviazione solo i record conteggi dei tipi di errore diverso, è possibile ottenere informazioni più dettagliate sulle singole richieste esaminando sul lato server, sul lato client e registri di rete. In genere, il codice di stato HTTP restituito dal servizio di archiviazione per ottenere un'indicazione del motivo del mancato la richiesta.

> [AZURE.NOTE] Tenere presente che dovrebbero aspettarsi Vedere errori intermittente: ad esempio errori a causa di condizioni di rete temporanee o gli errori dell'applicazione.

Le risorse seguenti nel sito Web MSDN sono utili per informazioni sui codici di stato e di errore correlati dello spazio di archiviazione:

- <a href="http://msdn.microsoft.com/library/azure/dd179357.aspx" target="_blank">Codici di errore API REST comuni</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179439.aspx" target="_blank">Codici di errore servizio BLOB</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179446.aspx" target="_blank">Codici di errore servizio coda</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179438.aspx" target="_blank">Tabella codici di errore di servizio</a>

### <a name="storage-emulator-issues"></a>Problemi di emulatore lo spazio di archiviazione

Azure SDK include un emulatore di spazio di archiviazione che è possibile eseguire una workstation di sviluppo. Questo emulatore simula la maggior parte del comportamento dei servizi di archiviazione Azure e può essere utile durante lo sviluppo e test, che consente di eseguire applicazioni che utilizzano i servizi di archiviazione Azure senza la necessità di un abbonamento a Azure e un account di archiviazione Azure.

La sezione "[indicazioni per la risoluzione dei problemi]" di questa guida vengono illustrati alcuni problemi comuni riscontrati utilizzando l'emulatore lo spazio di archiviazione.

### <a name="storage-logging-tools"></a>Strumenti di registrazione di spazio di archiviazione

La registrazione di spazio di archiviazione offre registrazione lato server delle richieste di spazio di archiviazione nell'account di archiviazione Azure. Per ulteriori informazioni su come attivare la registrazione lato server e access i dati di log, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=510867" target="_blank">registrazione lato server tramite</a> su MSDN.

La raccolta di Client lo spazio di archiviazione per .NET consente di raccogliere dati log lato client che si riferisce a operazioni di archiviazione eseguite dall'applicazione in uso. Per ulteriori informazioni su come abilitare l'accesso e la registrazione lato client i dati di log, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=510868" target="_blank">la registrazione lato Client utilizzando la libreria Client lo spazio di archiviazione</a> su MSDN.

> [AZURE.NOTE] In alcuni casi (ad esempio errori di autorizzazione SA), un utente può inviare una segnalazione un errore per il quale è non possibile trovare nessun dato richiesta i registri di spazio di archiviazione sul lato server. È possibile usare le funzionalità di registrazione della libreria Client lo spazio di archiviazione per verificare se la causa del problema è sul client o utilizzare gli strumenti di monitoraggio di rete per analizzare la rete.

### <a name="using-network-logging-tools"></a>Utilizzo degli strumenti di registrazione di rete

È possibile acquisire il traffico tra il client e server per informazioni dettagliate sui dati che scambio di client e server e le condizioni di rete sottostante. Strumenti di registrazione di rete utili includono:

- Fiddler (<a href="http://www.telerik.com/fiddler" target="_blank">http://www.telerik.com/fiddler</a>) è un web gratuito debug proxy che consente di esaminare le intestazioni e i dati del payload dei messaggi di richiesta e risposta HTTP e HTTPS. Per ulteriori informazioni, vedere "[appendice 1: utilizzo Fiddler per acquisire il traffico HTTP e HTTPS]".
- Microsoft Network Monitor (Netmon) (<a href="http://www.microsoft.com/download/details.aspx?id=4865" target="_blank">http://www.microsoft.com/download/details.aspx?id=4865</a>) e Wireshark (<a href="http://www.wireshark.org/" target="_blank">http://www.wireshark.org/</a>) sono analisi del protocollo di rete gratuito che consentono di visualizzare informazioni dettagliate pacchetto per un'ampia gamma di protocolli di rete. Per ulteriori informazioni su Wireshark, vedere "[Appendice 2: utilizzando Wireshark per acquisire il traffico di rete]".
- Microsoft messaggio Analyzer è uno strumento da Microsoft che sostituisce Netmon e che oltre l'acquisizione di dati del pacchetto di rete, consente di visualizzare e analizzare i dati del registro acquisiti da altri strumenti. Per ulteriori informazioni, vedere "[appendice 3: uso Microsoft messaggio Analyzer per acquisire il traffico di rete]".
- Se si desidera eseguire un test di connettività di base per verificare che il computer client è possibile connettersi al servizio di archiviazione Azure attraverso la rete, non è possibile farlo utilizzando lo strumento standard **ping** sul client. Tuttavia, è possibile utilizzare lo strumento **tcping** per controllare la connettività. **Tcping** è disponibile per il download all'indirizzo <a href="http://www.elifulkerson.com/projects/tcping.php" target="_blank">http://www.elifulkerson.com/projects/tcping.php</a>.

In molti casi, i dati del Registro di registrazione di spazio di archiviazione e la libreria di Client lo spazio di archiviazione sono sufficienti per la diagnosi un problema, ma in alcuni casi potrebbe essere necessario le informazioni più dettagliate in grado di fornire questi strumenti di registrazione di rete. Ad esempio utilizzando Fiddler per visualizzare i messaggi HTTP e HTTPS consente di visualizzare i dati di intestazione e payload inviati da e verso i servizi di archiviazione, che consentono di esaminare come un'applicazione client tentativi le operazioni di archiviazione. Analisi del protocollo, ad esempio Wireshark funzionare a livello di pacchetto che consente di visualizzare i dati TCP, che consentono di risolvere i problemi di connettività e perdita di pacchetti. Messaggio analizzatore di lavorare al layer TCP e HTTP.

## <a name="end-to-end-tracing"></a>Analisi di fine-fine

Analisi-to-end con una vasta gamma di file di log è una tecnica utile per analizzare i potenziali problemi. È possibile usare le informazioni di data/ora dai dati metriche come indicazione del punto in cui inizia la ricerca nei file di log per informazioni dettagliate che consentono di risolvere il problema.

### <a name="correlating-log-data"></a>Correlazione di dati dei registri

Quando si visualizzano i registri dalle applicazioni client, tracce di rete e le richieste di registrazione di archiviazione sul lato server che è fondamentale per consentire di correlare tra file di log. I file di log includono un numero di campi diversi utili come gli identificatori di correlazione. L'id di richiesta client è particolarmente utile campo da utilizzare per correlare le voci nei log diversi. Tuttavia in alcuni casi può essere utile usare l'id della richiesta di server o data e ora. Nelle sezioni seguenti vengono forniscono ulteriori informazioni su queste opzioni.

### <a name="client-request-id"></a>ID richiesta client

La raccolta di Client archiviazione genera automaticamente un id richiesta client univoco per ogni richiesta.

- Nel log lato client che consente di creare la raccolta di Client lo spazio di archiviazione, l'id di richiesta client viene visualizzato nel campo **ID richiesta Client** di ogni voce del registro relative alla richiesta.
- In una traccia di rete, ad esempio uno acquisito da Fiddler, l'id di richiesta client è visibile nei messaggi di richiesta come valore dell'intestazione **x-ms-client--id richiesta** HTTP.
- Nel log delle archiviazione registrazione lato server l'id di richiesta client viene visualizzato nella colonna ID richiesta Client.

> [AZURE.NOTE]È possibile che le richieste di più condividere lo stesso id di richiesta di client perché il client può assegnare il valore (anche se la raccolta di Client archiviazione assegna automaticamente un nuovo valore). In caso di tentativi dal client, tutti i tentativi di condividono lo stesso id richiesta client. Nel caso dei batch inviato dal client, il batch dispone di un id di richiesta solo client.


### <a name="server-request-id"></a>ID richiesta server

Il servizio di archiviazione genera automaticamente gli ID di richiesta di server.

- Nel log delle archiviazione registrazione lato server l'id di richiesta di server viene visualizzata la colonna **ID della richiesta di intestazione** .
- In una traccia di rete, ad esempio uno acquisito da Fiddler, l'id di richiesta di server viene visualizzato nei messaggi di risposta come il valore dell'intestazione di **id x-ms-richiesta** HTTP.
- Nel log lato client che consente di creare la raccolta di Client lo spazio di archiviazione, l'id di richiesta di server viene visualizzato nella colonna **Operazione testo** della voce di log che mostra i dettagli della risposta del server.

> [AZURE.NOTE] Il servizio di archiviazione sempre assegna un server univoco id richiesta a ogni richiesta che riceve, in modo che ogni tentativi dal client e tutte le operazioni incluse in un batch dispone di un id di richiesta di server univoco.

Se la raccolta di Client archiviazione genera un **StorageException** nel client, la proprietà **RequestInformation** contiene un oggetto **RequestResult** che include una proprietà **ServiceRequestID** . È anche possibile accedere a un oggetto **RequestResult** da un'istanza di **OperationContext** .

Nell'esempio seguente viene illustrato come impostare un valore personalizzato **ClientRequestId** associando un oggetto **OperationContext** la richiesta per il servizio di archiviazione. Viene illustrato come recuperare il valore di **ServerRequestId** dal messaggio di risposta.

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
    OperationContext oc = new OperationContext();
    oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

    try
    {
        CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
        ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
        var downloadToPath = string.Format("./{0}", blob.Name);
        using (var fs = File.OpenWrite(downloadToPath))
        {
            blob.DownloadToStream(fs, null, null, oc);
            Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
        }
    }
    catch (StorageException storageException)
    {
        Console.WriteLine("Storage exception {0} occurred", storageException.Message);
        // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
        foreach (var result in oc.RequestResults)
        {
                Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
        }
    }


### <a name="timestamps"></a>Data e ora

È anche possibile usare timestamp per individuare le voci di log correlate, facendo attenzione di qualsiasi inclinazione orologio tra il client e server che può essere presente. È necessario eseguire una ricerca più o meno 15 minuti per abbinare i movimenti sul lato server in base a timestamp sul client. Tenere presente che i metadati blob per BLOB contenenti metriche indicano l'intervallo di tempo per le metriche memorizzate nel blob; Ciò è utile se si dispone di molti BLOB metriche per lo stesso minuto o ore.

## <a name="troubleshooting-guidance"></a>Indicazioni per la risoluzione dei problemi

In questa sezione consente di effettuare la diagnosi e risoluzione dei problemi di alcuni dei problemi comuni dell'applicazione possono verificarsi quando si utilizzano i servizi di archiviazione Azure. Usare l'elenco seguente per individuare informazioni pertinenti per il problema specifico.

**Risoluzione dei problemi albero decisionale**

----------

Il problema riguardano le prestazioni di uno dei servizi di archiviazione?

- [Metrica Mostra AverageE2ELatency alta e bassa AverageServerLatency]
- [È possibile visualizzare metriche AverageE2ELatency bassa e bassa AverageServerLatency ma il client si è verificato latenza elevata]
- [Metrica Mostra AverageServerLatency elevato]
- [Si sono verificati ritardi imprevisti in recapito dei messaggi in una coda]

----------

Il problema si riferiscono alla disponibilità di uno dei servizi di archiviazione?

- [Metrica Mostra un aumento nel PercentThrottlingError]
- [Metrica Mostra un aumento nel PercentTimeoutError]
- [Metrica Mostra un aumento nel PercentNetworkError]

----------

L'applicazione client riceve una risposta 4XX (ad esempio 404) HTTP da un servizio di archiviazione?

- [Il client sta ricevendo messaggi HTTP 403 (accesso negato)]
- [Il client sta ricevendo messaggi HTTP 404 (non disponibile)]
- [Il client sta ricevendo messaggi HTTP 409 (conflitto)]

----------

[Metrica Mostra PercentSuccess bassa o voci di log analitica avere operazioni con stato ClientOtherErrors]

----------

[Metrica capacità Mostra un aumento imprevisto in utilizzo della capacità di archiviazione]

----------

[Si verifica il riavvio imprevisto di macchine virtuali che dispone di un numero elevato di dischi rigidi virtuali allegati]

----------

[Il problema si verifica da tramite emulatore lo spazio di archiviazione per lo sviluppo o test]

----------

[Si problemi durante l'installazione di Azure SDK per .NET]

----------

[Si dispone di un problema diverso relativo a un servizio di archiviazione]

----------

### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Metrica Mostra AverageE2ELatency alta e bassa AverageServerLatency

Soffiare via l'illustrazione tramite lo strumento di monitoraggio portale classica Azure viene illustrato un esempio in cui è molto superiori a **AverageServerLatency** **AverageE2ELatency** .

![][4]

Si noti che il servizio di archiviazione solo calcola l'unità di misura metriche **AverageE2ELatency** per le richieste di esito negativo e, diversamente da quanto succede **AverageServerLatency**include il tempo di client per inviare i dati e ricevere conferma dal servizio di archiviazione. Di conseguenza, potrebbe essere una differenza tra **AverageE2ELatency** e **AverageServerLatency** a causa dell'applicazione client lentezza di rispondere o a causa di condizioni sulla rete.

> [AZURE.NOTE] È anche possibile visualizzare **E2ELatency** e **ServerLatency** per le operazioni di archiviazione singoli nei dati del Registro di registrazione di spazio di archiviazione.

#### <a name="investigating-client-performance-issues"></a>Analisi dei problemi di prestazioni client

Possibili motivi per cui il client risponde lentamente includono la presenza di un numero limitato di connessioni disponibili o thread. È possibile risolvere il problema modificando il codice di client per aumentare l'efficienza (ad esempio tramite chiamate asincrone al servizio di archiviazione), oppure una macchina virtuale più grande (con più core e memoria).

Per i servizi di tabella e coda algoritmo Nagle può causare alta **AverageE2ELatency** rispetto a **AverageServerLatency**: per altre informazioni, vedere il post <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx" target="_blank">algoritmo Nagle è non descrittivo verso richieste di piccole dimensioni</a> nel Blog del Team Microsoft Azure lo spazio di archiviazione. È possibile disattivare l'algoritmo Nagle nel codice utilizzando la classe **ServicePointManager** dello spazio dei nomi **System.Net** . Eseguire questa operazione prima che le chiamate alla tabella o dell'apertura di servizi di coda nell'applicazione poiché questa operazione non ha alcun effetto connessioni già presenti. Nell'esempio seguente viene fornito dal metodo **Application_Start** in un ruolo di lavoro.

    var storageAccount = CloudStorageAccount.Parse(connStr);
    ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
    tableServicePoint.UseNagleAlgorithm = false;
    ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
    queueServicePoint.UseNagleAlgorithm = false;

Controllare i log lato client per visualizzare il numero di richieste l'invio di un'applicazione client e cercare .NET generali relativi problemi di prestazioni nel client di, ad esempio CPU, simultanea .NET, utilizzo della rete o memoria (come punto di partenza per la risoluzione dei problemi nelle applicazioni client .NET, vedere <a href="http://msdn.microsoft.com/library/7fe0dd2y(v=vs.110).aspx" target="_blank">debug individua e analisi</a> su MSDN).

#### <a name="investigating-network-latency-issues"></a>Analisi dei problemi di latenza di rete

Ad alta latenza-to-end causata dalla rete è in genere, a causa di condizioni temporanee. È possibile esaminare entrambi i problemi temporanei e persistente rete, ad esempio pacchetti ignorati mediante strumenti, ad esempio Wireshark o Microsoft messaggio Analyzer.

Per ulteriori informazioni sull'uso di Wireshark per la risoluzione dei problemi di rete, vedere "[Appendice 2: utilizzare Wireshark per acquisire il traffico di rete]."

Per ulteriori informazioni sull'utilizzo di Microsoft messaggio Analyzer per la risoluzione dei problemi di rete, vedere "[appendice 3: utilizzando Microsoft Message Analyzer per acquisire il traffico di rete]."

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>È possibile visualizzare metriche AverageE2ELatency bassa e bassa AverageServerLatency ma il client si è verificato latenza elevata

In questo scenario, probabilmente è un ritardo richieste per raggiungere il servizio di archiviazione. È necessario individuare perché le richieste dal client non rendono tramite al servizio blob.

Possibili motivi per cui il client ritardare l'invio di richieste includono la presenza di un numero limitato di connessioni disponibili o thread. È necessario verificare se il client effettua tentativi ripetuti e individuare la causa in questo caso. È possibile eseguire questo livello di codice la ricerca nell'oggetto **OperationContext** associato alla richiesta e il recupero del valore **ServerRequestId** . Per ulteriori informazioni, vedere l'esempio di codice nella sezione "[ID richiesta Server]".

Se non esistono problemi nel client, è necessario individuare potenziali problemi di rete, ad esempio la perdita di pacchetti. È possibile utilizzare gli strumenti, ad esempio Wireshark o Microsoft messaggio Analyzer per esaminare i problemi di rete.

Per ulteriori informazioni sull'uso di Wireshark per la risoluzione dei problemi di rete, vedere "[Appendice 2: utilizzando Wireshark per acquisire il traffico di rete]."

Per ulteriori informazioni sull'utilizzo di Microsoft messaggio Analyzer per la risoluzione dei problemi di rete, vedere "[appendice 3: utilizzando Microsoft Message Analyzer per acquisire il traffico di rete]."

### <a name="metrics-show-high-AverageServerLatency"></a>Metrica Mostra AverageServerLatency elevato

In caso di alta **AverageServerLatency** per le richieste di download blob, è necessario utilizzare i registri di registrazione di spazio di archiviazione per verificare se sono presenti richieste ripetute per lo stesso blob (o set di BLOB). Le richieste di caricamento blob, è necessario verificare quali blocco dimensione il client utilizzato (ad esempio, si blocca inferiore a 64 KB di dimensioni causando costi generali a meno che non legge presenti anche inferiore a 64 KB blocchi), e se più client caricano blocchi in blob stesso in parallelo. È necessario anche verificare le metriche al minuto per picchi il numero di richieste di tipo che causano il superamento il per i siti di destinazione di secondo scalabilità: Vedere anche "[metriche mostrano un aumento PercentTimeoutError]."

Se si visualizzano alta **AverageServerLatency** per blob scaricare le richieste quando vi sono richieste ripetute lo stesso blob o set di BLOB, è necessario considerare la memorizzazione nella cache tali BLOB tramite Cache Azure o Azure contenuti rete (CDN). Per le richieste di caricamento, è possibile migliorare la produttività con una dimensione maggiore per il blocco. Per le query alle tabelle, è anche possibile implementare la cache lato client in client che eseguono le stesse operazioni di query e in cui i dati non vengono modificati frequentemente.

Valori alti **AverageServerLatency** possono anche essere sintomo di progettato in maniera insufficiente tabelle o query che risultato in operazioni di analisi o che seguono il anti-modello di Accodamento/posticipato. Per ulteriori informazioni, vedere "[metriche mostrano un aumento PercentThrottlingError]".

> [AZURE.NOTE] È possibile trovare un elenco esaustivo delle prestazioni elenco di controllo qui: [Microsoft Azure archiviazione prestazioni e scalabilità elenco di controllo](storage-performance-checklist.md).

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Si sono verificati ritardi imprevisti in recapito dei messaggi in una coda

Se si verifica un ritardo tra il momento di che un'applicazione aggiunge un messaggio a una coda e il tempo che diventa disponibile da leggere dalla coda, è necessario eseguire le operazioni seguenti per diagnosticare il problema:

- Verificare che l'applicazione consiste nell'aggiungere correttamente i messaggi nella coda. Verificare che l'applicazione non effettua altri tentativi il metodo **AddMessage** più volte prima riuscire. I registri di spazio di archiviazione Client raccolta verranno visualizzati eventuali tentativi ripetuti di operazioni di archiviazione.
- Verificare che è presente alcun orologio asimmetria tra il ruolo di lavoro che aggiunge il messaggio alla coda e il ruolo di lavoro che legge il messaggio dalla coda di rende vengono visualizzati come se si verifica un ritardo di elaborazione.
- Verificare se il ruolo di lavoro che legge i messaggi dalla coda ha esito negativo. Se un client coda chiama il metodo **GetMessage** ma cessa di rispondere con una risposta, il messaggio rimarrà invisibile nella coda finché non scade il periodo di **invisibilityTimeout** . A questo punto, il messaggio diventa disponibile per l'elaborazione di nuovo.
- Verificare se la lunghezza della coda aumenta nel tempo. Questo può verificarsi se non è sufficiente worker disponibili per l'elaborazione di tutti i messaggi che inserisce nella coda altri colleghi. Controllare inoltre le metriche per verificare se le richieste delete sono esito negativo e il conteggio di annullamento dell'accodamento sui messaggi, è possibile ripetuti tentativi di eliminare il messaggio.
- Esaminare i registri registrazione lo spazio di archiviazione per le operazioni di coda che dispongano di un superiore a quello previsti valori **E2ELatency** e **ServerLatency** per un periodo di tempo del previsto.


### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Metrica Mostra un aumento nel PercentThrottlingError

Quando si superano le destinazioni scalabilità del servizio di archiviazione, si verificano errori di limitazioni. Il servizio di archiviazione responsabile per garantire che non solo client o tenant può usare il servizio rispetto alle altre. Per ulteriori informazioni, vedere <a href="http://msdn.microsoft.com/library/azure/dn249410.aspx" target="_blank">scalabilità dello spazio di archiviazione di Azure e obiettivi</a> per informazioni dettagliate su destinazioni scalabilità per gli account di archiviazione e obiettivi per le partizioni all'interno di account di archiviazione.

Se l'unità di misura metriche **PercentThrottlingError** mostrano un aumento della percentuale di richieste che hanno esito negativo con un errore di limitazione, è necessario provare a utilizzare uno dei due scenari:

- [Aumento temporaneo PercentThrottlingError]
- [Aumento permanente PercentThrottlingError errore]

Un aumento **PercentThrottlingError** volte si presenta allo stesso tempo aumenta il numero di richieste di spazio di archiviazione o quando sono inizialmente caricare il test dell'applicazione. Questa operazione si manifesti anche nel client come "503 Server occupato" o "Timeout dell'operazione 500" HTTP stato messaggi da operazioni di archiviazione.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Aumento temporaneo PercentThrottlingError

Se si visualizzano due picchi il valore di **PercentThrottlingError** in occasione di periodi di attività elevata per l'applicazione, è necessario implementare un indietro (non lineare) esponenziale disattivare strategia di tentativi nel client: verrà ridurre il carico immediato sulla partizione e Guida l'applicazione per arrotondare picchi di traffico. Per ulteriori informazioni su come implementare criteri Riprova utilizzando la libreria di Client lo spazio di archiviazione, vedere <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx" target="_blank">Namespace Microsoft.WindowsAzure.Storage.RetryPolicies</a> su MSDN.

> [AZURE.NOTE] Potrebbe essere anche visualizzata picchi il valore di **PercentThrottlingError** che non coincide con periodi di attività elevata per l'applicazione: probabilmente qui è il servizio di archiviazione spostare le partizioni per migliorare il bilanciamento del carico.

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Aumento permanente PercentThrottlingError errore

Se viene visualizzato un valore in modo coerente elevato per **PercentThrottlingError** dopo un aumento permanente i volumi delle transazioni o durante il caricamento iniziale di test sull'applicazione, quindi è necessario valutare come l'applicazione utilizza le partizioni dello spazio di archiviazione e se si avvicina a destinazioni scalabilità per un account di archiviazione. Ad esempio, se viene visualizzata la limitazione per gli errori in una coda (che viene conteggiato come una singola partizione), quindi è necessario ricorrere code aggiuntive per distribuire le transazioni tra più partizioni. Se viene visualizzata la limitazione per gli errori in una tabella, è necessario si consiglia di utilizzare una combinazione di partizione diversa per distribuire sotto controllo le transazioni tra più partizioni con una vasta gamma di valori di chiave partizione. Una delle cause comune di questo problema è il prepend/accodare anti-modello in cui si seleziona la data come chiave di partizione e quindi tutti i dati in un determinato giorno viene scritto a una partizione: in condizioni di carico, questo può comportare una bottiglia di scrittura. Valutare la possibilità di uno stile di partizione diverso o valutare se usando archiviazione blob potrebbe essere una soluzione migliore. Occorre controllare se la limitazione si verifica in seguito picchi il traffico e provare a utilizzare modi di smorzamento il modello di richieste di tipo.

Se si distribuiscono sotto controllo le transazioni su più partizioni, è necessario prestare tenere presenti i limiti di scalabilità impostati per l'account di archiviazione. Ad esempio, se è stata utilizzata dieci code ogni elaborazione il numero massimo di 2.000 messaggi 1KB al secondo, sarà al limite complessivo di 20.000 messaggi al secondo per l'account di archiviazione. Se è necessario per l'elaborazione di più di 20.000 entità al secondo, considerare l'utilizzo di più account di archiviazione. È anche importante tenere presente che le dimensioni le richieste e le entità hanno un impatto sulla quando il servizio di archiviazione limita i clienti: se si dispone di più grande entità e le richieste, potrebbe limitato in anticipo.

Struttura query inefficace potrebbe anche raggiunga i limiti di scalabilità per partizioni delle tabelle. Ad esempio, una query con un filtro che seleziona solo una percentuale delle entità in una partizione ma che analizza tutte le entità di una partizione sarà necessario accedere a ogni entità. Ogni entità leggere vengono conteggiati per calcolare il numero totale di transazioni in tale partizione; di conseguenza, può facilmente raggiungere gli obiettivi di scalabilità.

> [AZURE.NOTE] Il test delle prestazioni dovrebbero essere visualizzati le strutture delle query inefficace dell'applicazione.

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Metrica Mostra un aumento nel PercentTimeoutError

Le metriche mostrano un aumento nel **PercentTimeoutError** per uno dei servizi di archiviazione. Allo stesso tempo il client riceve un volume elevato di messaggi di stato HTTP "Timeout dell'operazione 500" da operazioni di archiviazione.

> [AZURE.NOTE] Potrebbe essere visualizzato un errore di timeout temporaneamente come il servizio di archiviazione caricare le richieste di saldo spostando una partizione in un nuovo server.

Unità di misura metriche **PercentTimeoutError** è un'aggregazione di quelle riportate di seguito: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**e **SASServerTimeoutError**.

Timeout del server sono causati da un messaggio di errore sul server. I valore di timeout di client dovuto al fatto che un'operazione nel server ha superato il timeout specificato dal client; ad esempio, un client utilizzando la libreria di Client lo spazio di archiviazione è possibile impostare un timeout per un'operazione utilizzando la proprietà **ServerTimeout** della classe **QueueRequestOptions** .

Timeout del server indica un problema con il servizio di archiviazione che richiede ulteriori indagini. È possibile utilizzare le metriche per vedere se si raggiunge i limiti di scalabilità per il servizio e identificare eventuali picchi di traffico che potrebbero causare il problema. Se il problema è intermittente, potrebbe essere a causa di bilanciamento del carico attività nel servizio. Se il problema è permanente e non può essere dovuto dall'applicazione raggiungere i limiti di scalabilità del servizio, è necessario generare un problema di supporto. Per client timeout, è necessario decidere se il timeout è impostato su un valore appropriato nel client e il valore di timeout set nel client uno dei due modifiche o esaminare come per migliorare le prestazioni di esecuzione delle operazioni del servizio di archiviazione, ad esempio, ottimizzazione delle query di tabella o ridurre la dimensione dei messaggi.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Metrica Mostra un aumento nel PercentNetworkError

Le metriche mostrano un aumento nel **PercentNetworkError** per uno dei servizi di archiviazione. Unità di misura metriche **PercentNetworkError** è un'aggregazione di quelle riportate di seguito: **NetworkError**, **AnonymousNetworkError**e **SASNetworkError**. Si verificano quando il servizio di archiviazione rileva un errore di rete quando il client effettua una richiesta di spazio di archiviazione.

La causa più comune di errore simile al seguente è un client disconnessione prima della scadenza di un timeout del servizio di archiviazione. È necessario individuare il codice nel client di comprendere perché e quando il client si disconnette dal servizio di archiviazione. È anche possibile usare Wireshark, Microsoft messaggio Analyzer o Tcping per analizzare i problemi di connettività di rete dal client. Questi strumenti sono descritti nelle [appendici].

### <a name="the-client-is-receiving-403-messages"></a>Il client sta ricevendo messaggi HTTP 403 (accesso negato)

Se l'applicazione client è generare errori HTTP 403 (accesso negato), è un probabilmente che il client utilizza un scaduto condiviso accesso firma (SA) quando viene inviata una richiesta di spazio di archiviazione (anche se altre possibili cause includono chiavi non validi, asimmetria orologio e intestazioni vuote). Se la causa è una chiave SA scaduta, non si vedranno tutte le voci nei dati log archiviazione registrazione lato server. Nella tabella seguente mostra un campione di log lato client generati dalla libreria Client lo spazio di archiviazione che illustri questo problema si verifichi:

Origine|Livello di dettaglio|Livello di dettaglio|Id richiesta client|Testo operazione
---|---|---|---|---
Microsoft.WindowsAzure.Storage|Informazioni|3|85d077ab-...|Operazione a partire da posizione principale per la modalità di posizione PrimaryOnly.
Microsoft.WindowsAzure.Storage|Informazioni|3|85d077ab-...|Avvio richiesta icona del https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;sr = c&amp;si = mypolicy&amp;firma = OFnd4Rd7z01fIvh % 2BmcR6zbudIH2F5Ikm % 2FyhNYZEmJNQ % 3D&amp;versione api = 2014-02-14.
Microsoft.WindowsAzure.Storage|Informazioni|3|85d077ab-...|In attesa di risposta.
Microsoft.WindowsAzure.Storage|Avviso|2|85d077ab-...|Eccezione in attesa di risposta: il server remoto ha restituito un errore: accesso negato (403).
Microsoft.WindowsAzure.Storage|Informazioni|3|85d077ab-...|Risposta ricevuta. Codice di stato = 403, richiedere l'ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = ETag =.
Microsoft.WindowsAzure.Storage|Avviso|2|85d077ab-...|Eccezione durante l'operazione: il server remoto ha restituito un errore: accesso negato (403)..
Microsoft.WindowsAzure.Storage|Informazioni|3 |85d077ab-...|Verifica se l'operazione deve essere ripetuta. Conteggio tentativi = 0, il codice di stato HTTP = 403, eccezione = il server remoto ha restituito un errore: accesso negato (403)..
Microsoft.WindowsAzure.Storage|Informazioni|3|85d077ab-...|Posizione successiva è stata impostata su primario, in base alla modalità di posizione.
Microsoft.WindowsAzure.Storage|Errore|1|85d077ab-...|Criteri di Riprova non ha consentito per riprovare. Errore con il server remoto ha restituito un errore: accesso negato (403).

In questo scenario, è necessario individuare il motivo per cui in scadenza token SA prima che il client invia il token al server:

- Non è in genere possibile impostare un'ora di inizio quando si crea una SA per un client a utilizzare subito. Se esistono differenze piccolo orologio tra l'host generazione SA utilizzando il servizio di archiviazione e l'ora correnti, quindi è possibile che il servizio di archiviazione ricevere un sa che non è ancora valido.
- Impostare una scadenza molto brevi non su un SA. Nuovo differenze piccolo orologio nell'host generazione le associazioni di protezione e il servizio di archiviazione possono comportare un SA apparentemente in scadenza anteriori previsto.
- Indica il parametro versione nella chiave SA (ad esempio **sv = 12-02-2012**) corrisponde alla versione della libreria di Client lo spazio di archiviazione in uso. Utilizzare sempre la versione più recente della libreria di Client lo spazio di archiviazione. Per ulteriori informazioni sul controllo delle versioni token SA, vedere [Novità di Microsoft Azure archiviazione](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/14/what-s-new-for-microsoft-azure-storage-at-teched-2014.aspx).
- 
- Se si rigenera i tasti di spazio di archiviazione (fare clic su **Gestisci i tasti di scelta** in qualsiasi pagina account di archiviazione nel portale classica di Azure) questo può invalidate qualsiasi token sa esistente. Può trattarsi di un problema se si genera token sa con una scadenza lungo alle applicazioni client di cache.

Se si utilizza la libreria Client lo spazio di archiviazione per generare i token SA, quindi è facile creare un token valido. Tuttavia, se si utilizza l'API REST di spazio di archiviazione e la costruzione token SA manualmente con attenzione leggere l'argomento <a href="http://msdn.microsoft.com/library/azure/ee395415.aspx" target="_blank">Delega di accesso con una firma di Access condiviso</a> su MSDN.

### <a name="the-client-is-receiving-404-messages"></a>Il client sta ricevendo messaggi HTTP 404 (non disponibile)
Se l'applicazione client riceve un messaggio HTTP 404 (non trovato) dal server, ciò significa che l'oggetto che il tentativo di usare (ad esempio un'entità, una tabella, blob, contenitore o coda) non esiste nel servizio di archiviazione. Esistono un numero di possibili cause, ad esempio:

- [Il client o un altro processo precedentemente eliminato l'oggetto]
- [Un problema di autorizzazioni condiviso Access firma (SA)]
- [Codice JavaScript lato client non dispone delle autorizzazioni per accedere all'oggetto]
- [Errore di rete]

#### <a name="client-previously-deleted-the-object"></a>Il client o un altro processo precedentemente eliminato l'oggetto
In scenari in cui il client tenta di leggere, aggiornare o eliminare i dati in un servizio di archiviazione è un'operazione semplice identificare nei log lato server un'operazione precedente eliminato l'oggetto in questione dal servizio di archiviazione. Molto spesso i dati del registro indicano che un altro utente o un processo eliminato l'oggetto. Nel Registro di spazio di archiviazione registrazione lato server, il tipo di operazione e le colonne di chiave di oggetto richiesto mostrano quando un client eliminato un oggetto.

In uno scenario in cui un client tenta di inserire un oggetto, potrebbe non essere immediatamente visibile perché il risultato di una risposta HTTP 404 (non trovato) dato che il client crea un nuovo oggetto. Tuttavia, se il client consiste nel creare un blob deve essere riesce a trovare il contenitore blob, se il client crea un messaggio deve essere in grado di trovare una coda e il client consiste nell'aggiungere una riga deve essere riesce a trovare la tabella.

È possibile utilizzare il log lato client dalla raccolta di Client di spazio di archiviazione per ottenere un quadro più dettagliato di quando il client invia richieste specifiche per il servizio di archiviazione.

Nel log lato client seguente generato dalla libreria Client di spazio di archiviazione di seguito viene illustrato il problema quando il client non è possibile trovare il contenitore per il blob che viene creata. Questo registro include dettagli delle seguenti operazioni di archiviazione:

ID richiesta|Operazione
---|---
07b26a5d-...|Metodo **DeleteIfExists** per eliminare il contenitore blob. Si noti che questa operazione include una richiesta di **testa** per verificare l'esistenza del contenitore.
e2d06d78...|Metodo **CreateIfNotExists** per creare il contenitore blob. Si noti che questa operazione include una richiesta di **testa** che verifica la presenza del contenitore. Restituisce un messaggio 404 **testa** , ma continua.
de8b1c3c-...|**UploadFromStream** per creare il blob. La richiesta di **mettere** non riesce a un messaggio 404

Voci di log:

ID richiesta |  Operazione testo
---|---
07b26a5d-...|Icona del richiesta per https://domemaildist.blob.core.windows.net/azuremmblobcontainer di inizio.
07b26a5d-...|StringToSign = HEAD...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 03 giugno 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
07b26a5d-...|In attesa di risposta.
07b26a5d-... | Risposta ricevuta. Codice di stato = 200, richiedere l'ID = eeead849-... Contenuto MD5 = ETag = &quot;0x8D14D2DC63D059B&quot;.
07b26a5d-... | Intestazioni di risposta elaborate correttamente, procedere con il resto dell'operazione.
07b26a5d-... | Scaricare il corpo di risposta.
07b26a5d-... | Operazione completata.
07b26a5d-... | Icona del richiesta per https://domemaildist.blob.core.windows.net/azuremmblobcontainer di inizio.
07b26a5d-... | StringToSign = DELETE...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 03 giugno 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
07b26a5d-... | In attesa di risposta.
07b26a5d-... | Risposta ricevuta. Codice di stato = 202, richiedere l'ID = 6ab2a4cf-..., Content-MD5 = ETag =.
07b26a5d-... | Intestazioni di risposta elaborate correttamente, procedere con il resto dell'operazione.
07b26a5d-... | Scaricare il corpo di risposta.
07b26a5d-... | Operazione completata.
e2d06d78-... | Richiesta di asincrono iniziale di https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td>
e2d06d78-... | StringToSign = HEAD...x-ms-client-request-id:e2d06d78-...x-ms-date:Tue, 03 giugno 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-...| In attesa di risposta.
de8b1c3c-... | Icona del richiesta per https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt di inizio.
de8b1c3c-... |  StringToSign = inserire... 64.qCmF+TQLPhq/YYK50mP9ZQ==...x-ms-BLOB-Type:BlockBlob.x-ms-client-Request-ID:de8b1c3c-...x-ms-date:tue, 03 giugno 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt.
de8b1c3c-... | Preparazione per la scrittura dei dati richiesta.
e2d06d78-... | Eccezione durante l'attesa di risposta: il server remoto ha restituito un errore: (404) non trovato..
e2d06d78-... | Risposta ricevuta. Codice di stato = 404, richiedere l'ID = 353ae3bc-..., Content-MD5 = ETag =.
e2d06d78-... | Intestazioni di risposta elaborate correttamente, procedere con il resto dell'operazione.
e2d06d78-... | Scaricare il corpo di risposta.
e2d06d78-... | Operazione completata.
e2d06d78-... | Richiesta di asincrono iniziale di https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
e2d06d78-...|StringToSign = inserire... 0...x-ms-client-Request-ID:e2d06d78-...x-ms-date:tue, 03 giugno 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-... | In attesa di risposta.
de8b1c3c-... | Scrittura di dati richiesta.
de8b1c3c-... | In attesa di risposta.
e2d06d78-... | Eccezione durante l'attesa di risposta: il server remoto ha restituito un errore: conflitto (409).
e2d06d78-... | Risposta ricevuta. Codice di stato = 409, richiedere l'ID = c27da20e-..., Content-MD5 = ETag =.
e2d06d78-... | Risposta errore di download.
de8b1c3c-... | Eccezione durante l'attesa di risposta: il server remoto ha restituito un errore: (404) non trovato..
de8b1c3c-... | Risposta ricevuta. Codice di stato = 404, richiedere l'ID = 0eaeab3e-..., Content-MD5 = ETag =.
de8b1c3c-...| Eccezione durante l'operazione: il server remoto ha restituito un errore: (404) non trovato..
de8b1c3c-... | Criteri di Riprova non ha consentito per riprovare. Errore con il server remoto ha restituito un errore: (404) non trovato..
e2d06d78-... | Criteri di Riprova non ha consentito per riprovare. Errore con il server remoto ha restituito un errore: conflitto (409).

In questo esempio, il registro indica che il client è sovrapposizione richieste dal metodo **CreateIfNotExists** (richiesta id e2d06d78...) con le richieste dal metodo **UploadFromStream** (de8b1c3c-...). Ciò avviene perché l'applicazione client richiama questi metodi in modo asincrono. È necessario modificare il codice asincrono nel client per assicurarsi che viene creato il contenitore prima di tentare di caricare uno qualsiasi dei dati in un blob in tale contenitore. Se possibile, è necessario creare tutti i contenitori in anticipo.

#### <a name="SAS-authorization-issue"></a>Un problema di autorizzazioni condiviso Access firma (SA)

Se l'applicazione client tenta di utilizzare una chiave sa che non dispone delle autorizzazioni necessarie per l'operazione, il servizio di archiviazione restituisce un messaggio HTTP 404 (non trovato) al client. Nello stesso momento verrà anche visualizzato un valore diverso da zero per **SASAuthorizationError** tali valori.

Nella tabella seguente viene visualizzato un messaggio di log lato server di esempio dal file di log di registrazione lo spazio di archiviazione:

<table>
  <tr>
    <td>Richiedere l'ora di inizio</td>
    <td>2014-05-30T06:17:48.4473697Z</td>
  </tr>
  <tr>
    <td>Tipo di operazione</td>
    <td>GetBlobProperties</td>
  </tr>
  <tr>
    <td>Stato della richiesta</td>
    <td>SASAuthorizationError</td>
  </tr>
  <tr>
    <td>Codice di stato HTTP</td>
    <td>404</td>
  </tr>
  <tr>
    <td>Tipo di autenticazione</td>
    <td>Associazioni di protezione</td>
  </tr>
  <tr>
    <td>Tipo di servizio</td>
    <td>BLOB</td>
  </tr>
  <tr>
    <td>Richiedere l'URL</td>
    <td>
    https://domemaildist.BLOB.Core.Windows.NET/azureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;amp; sr = c&amp;amp; si = mypolicy&amp;amp; firma = XXXXX&amp;amp; versione api = 2014-02-14&amp;amp;</td>
  </tr>
  <tr>
    <td>Richiedere l'intestazione di id</td>
    <td>a1f348d5-8032-4912-93ef-b393e5252a3b</td>
  </tr>
  <tr>
    <td>ID richiesta client</td>
    <td>2d064953-8436-4ee0-aa0c-65cb874f7929</td>
  </tr>
</table>

È necessario individuare il motivo per cui l'applicazione client tenta di eseguire un'operazione per che non dispone delle autorizzazioni.

#### <a name="JavaScript-code-does-not-have-permission"></a>Codice JavaScript lato client non dispone delle autorizzazioni per accedere all'oggetto

Se si utilizza un client JavaScript e il servizio di archiviazione restituisce messaggi HTTP 404, per verificare gli errori seguenti JavaScript nel browser:

    SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
    SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.

> [AZURE.NOTE] È possibile utilizzare gli strumenti di sviluppo F12 in Internet Explorer per tenere traccia dei messaggi scambiati tra il browser e il servizio di archiviazione durante la risoluzione di problemi JavaScript sul lato client.

Questi errori nel web browser implementa restrizione di protezione <a href="http://www.w3.org/Security/wiki/Same_Origin_Policy" target="_blank">stessa origine dei criteri</a> che impedisce che la chiamata a una API in un dominio diverso da quello del dominio da che della pagina proviene una pagina web.

Per risolvere il problema JavaScript, è possibile configurare incrociato origine risorsa condivisione (CORS) per il servizio di archiviazione che il client accede. Per ulteriori informazioni, vedere <a href="http://msdn.microsoft.com/library/azure/dn535601.aspx" target="_blank">condividere risorse tra origine (CORS) supporto per i servizi di archiviazione Windows Azure</a> su MSDN.

Nell'esempio seguente viene illustrato come configurare il servizio blob per consentire di JavaScript in esecuzione nel dominio Contoso accedere a un blob nel servizio di archiviazione blob:

    CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
    // Set the service properties.
    ServiceProperties sp = client.GetServiceProperties();
    sp.DefaultServiceVersion = "2013-08-15";
    CorsRule cr = new CorsRule();
    cr.AllowedHeaders.Add("*");
    cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
    cr.AllowedOrigins.Add("http://www.contoso.com");
    cr.ExposedHeaders.Add("x-ms-*");
    cr.MaxAgeInSeconds = 5;
    sp.Cors.CorsRules.Clear();
    sp.Cors.CorsRules.Add(cr);
    client.SetServiceProperties(sp);

#### <a name="network-failure"></a>Errore di rete

In alcuni casi, perdita di pacchetti possono comportare la restituzione dei messaggi HTTP 404 al client il servizio di archiviazione. Ad esempio, quando l'applicazione client sta eliminando un'entità dal servizio di tabella viene visualizzato il client che venga generata un'eccezione dello spazio di archiviazione dei report un "HTTP 404 (non trovato)" messaggio di stato del servizio di tabella. Quando si analizza tabella del servizio di archiviazione tabella, viene visualizzato che il servizio Elimina l'entità come richiesto.

Dettagli dell'eccezione nel client di includono l'id di richiesta (7e84f12d) assegnato dal servizio di tabella per la richiesta: è possibile utilizzare queste informazioni per individuare i dettagli della richiesta nei log lato server dello spazio di archiviazione per la ricerca nella colonna **dell'intestazione di id richiesta** nel file di log. È anche possibile utilizzare le metriche per identificare quando, ad esempio questo presentano e quindi cercare i file di log in base all'ora che le metriche registrato questo errore. Questa voce di log mostra che l'eliminazione non riuscita con un messaggio di stato "Client HTTP (404) altro tipo di errore". La voce del registro stesso include anche l'id di richiesta generato dal client nella colonna **id di richiesta client** (813ea74f).

Log lato server include anche un'altra voce con lo stesso valore di **id di richiesta client** (813ea74f) per un'operazione di eliminazione per la stessa entità e dallo stesso client. Questa operazione di eliminazione ha avuto luogo presto prima di eliminare il volume richiesta.

Probabilmente di questo scenario è che il client ha inviato una richiesta di eliminazione per l'entità al servizio di tabella, che ha avuto esito positivo, ma non ha ricevuto un riconoscimento dal server (ad esempio a causa di un problema temporaneo rete). Il client quindi automaticamente ripetuta l'operazione (con lo stesso **id di richiesta client**) e il tentativo non riuscito poiché l'entità era già stata eliminata.

Se il problema si verifica frequentemente, è necessario individuare il motivo per cui il client non riesce a ricevere riconoscimenti dal servizio di tabella. Se il problema è intermittente, è necessario intercettare l'errore "HTTP (404) non trovata" e accedere nel client, ma consentire al client continuare.

### <a name="the-client-is-receiving-409-messages"></a>Il client sta ricevendo messaggi HTTP 409 (conflitto)

La tabella seguente mostra un estratto log lato server per due operazioni client: **DeleteIfExists** seguita dall' **CreateIfNotExists** con lo stesso nome contenitore blob. Si noti che ogni risultati di un'operazione client in due richieste inviato al server, prima di tutto una richiesta di **GetContainerProperties** per verificare se è presente il contenitore, seguita da richiesta **DeleteContainer** o **CreateContainer** .

Timestamp|Operazione|Risultato|Nome del contenitore|Id richiesta client
---|---|---|---|---
05:10:13.7167225|GetContainerProperties|200|mmcont|c9f52c89-...
05:10:13.8167325|DeleteContainer|202|mmcont|c9f52c89-...
05:10:13.8987407|GetContainerProperties|404|mmcont|bc881924-...
05:10:14.2147723|CreateContainer|409|mmcont|bc881924-...

Il codice nell'applicazione client ricrea quindi immediatamente un contenitore di blob utilizzando lo stesso nome: il metodo **CreateIfNotExists** (Client richiesta ID bc881924-...) non viene individuata con l'errore HTTP 409 (conflitto). Quando un client Elimina contenitori blob, tabelle o code esiste un breve periodo di tempo prima del nome nuovamente disponibile.

L'applicazione client deve utilizzare nomi contenitore univoco ogni volta che crea nuovi contenitori se il criterio di eliminazione e creazione di è comune.

### <a name="metrics-show-low-percent-success"></a>Metrica Mostra PercentSuccess bassa o voci di log analitica avere operazioni con stato ClientOtherErrors

Unità di misura metriche **PercentSuccess** acquisisce la percentuale di operazioni che è stata eseguita correttamente in base a codice di stato. Operazioni con i codici di stato di 2XX contano come ha esito positivo, mentre le operazioni con i codici di stato in intervalli 3XX, 4XX e 5XX vengono considerate come ha esito negativo e abbassare il valore di metriche **PercentSucess** . Nei file di log di archiviazione sul lato server queste operazioni sono registrate con lo stato transazione **ClientOtherErrors**.

È importante tenere presente che queste operazioni completate e pertanto non influiscono altre metriche, ad esempio disponibilità. Alcuni esempi di operazioni da eseguire correttamente ma che può comportare codici di stato HTTP non riusciti:
- **ResourceNotFound** (Non viene trovato 404), ad esempio da una richiesta GET per un blob non esiste.
- **ResouceAlreadyExists** (Conflitto 409), ad esempio da un'operazione di **CreateIfNotExist** nel punto in cui la risorsa esiste già.
- **ConditionNotMet** (Non modificati 304), ad esempio da un'operazione condizionale, ad esempio quando un client invia un valore **ETag** e un'intestazione HTTP **If-None-Match** per richiedere un'immagine solo se è stata aggiornata dopo l'ultima operazione.

È possibile trovare un elenco dei codici di errore comuni API REST che restituiscono i servizi di archiviazione nella pagina <a href="http://msdn.microsoft.com/library/azure/dd179357.aspx" target="_blank">Codici di errore comuni API REST</a>.

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Metrica capacità Mostra un aumento imprevisto in utilizzo della capacità di archiviazione


Se viene visualizzato inaspettata, modifiche impreviste nell'utilizzo di capacità nel proprio account di archiviazione, è possibile esaminare i motivi esaminando prima la misurazione della disponibilità; ad esempio un aumento del numero di eliminazione non richieste potrebbero causare un aumento della quantità di spazio di archiviazione blob in uso come operazioni di pulizia specifica applicazione che potrebbe avere previsto per essere liberare spazio potrebbero non funzionare come previsto (ad esempio, in quanto i token SA utilizzati per liberare spazio scaduto).

### <a name="you-are-experiencing-unexpected-reboots"></a>Si verifica il riavvio imprevisto di macchine virtuali di Azure che dispone di un numero elevato di dischi rigidi virtuali allegati

Se un Azure virtuale macchina dispone di un numero elevato di allegati dischi rigidi virtuali presenti lo stesso account di archiviazione, si superi le destinazioni scalabilità per un account di archiviazione singoli causa macchine Virtuali di esito negativo. È necessario verificare le metriche minute per l'account di archiviazione (**TotalRequests**/**TotalIngress**/**TotalEgress**) per picchi che superano le destinazioni scalabilità per un account di archiviazione. Vedere la sezione "[che metriche mostrano un aumento PercentThrottlingError]" per determinare se la limitazione si è verificato nel proprio account di archiviazione.

In generale, ogni singolo input o output operazione su un disco rigido virtuale da una macchina virtuale viene convertita in **Pagina ottenere** o **Inserire** operazioni su sottostante blob di pagine. Pertanto, è possibile utilizzare IOPS stimati per l'ambiente per ottimizzare quanti dischi rigidi virtuali possibile in un account di archiviazione basato sul comportamento specifico dell'applicazione. Non è consigliabile la presenza di più di 40 dischi in un account di archiviazione singola. <a href="http://msdn.microsoft.com/library/azure/dn249410.aspx" target="_blank">Scalabilità lo spazio di archiviazione di Azure e obiettivi</a> per informazioni vedere le destinazioni scalabilità corrente per gli account di archiviazione, in particolare il totale della larghezza di banda per il tipo di account di archiviazione in uso e la velocità di richiesta totale.
Se si superano le destinazioni scalabilità per l'account di archiviazione, inserire i dischi rigidi virtuali in più account di archiviazione diversa per ridurre l'attività in ogni singolo conto.

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>Il problema si verifica da tramite emulatore lo spazio di archiviazione per lo sviluppo o test

In genere utilizzare emulatore lo spazio di archiviazione in fase di sviluppo e testare per evitare l'utilizzo di un account di archiviazione Azure. Problemi comuni che possono verificarsi quando si utilizza l'emulatore lo spazio di archiviazione sono:

- [Caratteristica "X" non è attivo in emulatore lo spazio di archiviazione]
- [Errore "il valore di una delle intestazioni HTTP non è nel formato corretto" quando si utilizza l'emulatore lo spazio di archiviazione]
- [Esegue l'emulatore lo spazio di archiviazione richiede privilegi di amministratore]

#### <a name="feature-X-is-not-working"></a>Caratteristica "X" non compatibili con emulatore lo spazio di archiviazione

Emulatore lo spazio di archiviazione non supporta tutte le caratteristiche dei servizi di archiviazione Azure, ad esempio il servizio di file. Per ulteriori informazioni, vedere <a href="http://msdn.microsoft.com/library/azure/gg433135.aspx" target="_blank">differenze tra lo spazio di archiviazione emulatore e servizi di archiviazione Windows Azure</a> su MSDN.

Per le caratteristiche che non supporta l'emulatore lo spazio di archiviazione, utilizzare il servizio di archiviazione Azure nel cloud.

#### <a name="error-HTTP-header-not-correct-format"></a>Errore "il valore di una delle intestazioni HTTP non è nel formato corretto" quando si utilizza l'emulatore lo spazio di archiviazione

Testare l'applicazione che utilizzano la libreria di Client archiviazione contro emulatore archivio locale e metodo chiama ad esempio **CreateIfNotExists** non riuscire con il messaggio di errore "il valore di una delle intestazioni HTTP non è nel formato corretto." Indica che la versione dell'emulatore lo spazio di archiviazione in uso non supporta la versione della libreria client lo spazio di archiviazione in uso. La raccolta di Client lo spazio di archiviazione aggiunge l' intestazione **x-ms-versione** di tutte le richieste che rende. Se l'emulatore lo spazio di archiviazione non riconosce il valore nell'intestazione **x-ms-versione** , rifiuta la richiesta.

È possibile utilizzare i registri di spazio di archiviazione raccolta Client per visualizzare il valore dell' **intestazione x-ms-versione** di invio. È anche possibile visualizzare il valore dell' **intestazione x-ms-versione** se si utilizza Fiddler per tracciare le richieste dall'applicazione client.

In genere questo scenario si verifica se si installa e Usa la versione più recente della libreria Client lo spazio di archiviazione senza aggiornare emulatore lo spazio di archiviazione. È necessario installare la versione più recente dell'emulatore lo spazio di archiviazione o utilizzare lo spazio di archiviazione cloud anziché l'emulatore per lo sviluppo e test.

#### <a name="storage-emulator-requires-administrative-privileges"></a>Esegue l'emulatore lo spazio di archiviazione richiede privilegi di amministratore

Quando si esegue l'emulatore lo spazio di archiviazione verrà chiesto di credenziali di amministratore. Questo problema si verifica solo quando si inizializzazione emulatore lo spazio di archiviazione per la prima volta. Dopo avere inizializzato emulatore lo spazio di archiviazione, non è necessaria privilegi di amministratore per eseguire nuovamente.

Per ulteriori informazioni, vedere <a href="http://msdn.microsoft.com/library/azure/gg433132.aspx" target="_blank">inizializzare emulatore lo spazio di archiviazione utilizzando lo strumento della riga di comando</a> su MSDN (è inoltre possibile inizializzare emulatore lo spazio di archiviazione in Visual Studio, che richiedono anche privilegi amministrativi).

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Si problemi durante l'installazione di Azure SDK per .NET

Quando si tenta di installare SDK, non riesce si prova a installare l'emulatore lo spazio di archiviazione sul computer locale. Il Registro di installazione contiene uno dei messaggi seguenti:

- CAQuietExec: Errore: Impossibile accedere istanza di SQL Server
- CAQuietExec: Errore: Impossibile creare database

La causa è un problema di installazione LocalDB esistente. Per impostazione predefinita, l'emulatore lo spazio di archiviazione utilizza LocalDB per mantenere i dati quando vengono simulate i servizi di archiviazione Azure. È possibile reimpostare l'istanza LocalDB eseguendo i comandi seguenti in una finestra del prompt dei comandi prima di provare a installare SDK.

    sqllocaldb stop v11.0
    sqllocaldb delete v11.0
    delete %USERPROFILE%\WAStorageEmulatorDb3*.*
    sqllocaldb create v11.0

Il comando **Elimina** per rimuovere i file di database obsoleti da installazioni precedenti di emulatore lo spazio di archiviazione.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Si dispone di un problema diverso relativo a un servizio di archiviazione

Se le sezioni di risoluzione dei problemi precedente non includano il problema riscontrato con un servizio di archiviazione, si dovrebbe adottare il seguente approccio per la diagnosi e la risoluzione del problema.

- Controllare la metrica per verificare se è presente alcuna modifica rispetto al comportamento della linea di base previsto. Da metriche, è possibile determinare se il problema è temporaneo o permanente e quali operazioni di archiviazione che interessano il problema.
- È possibile usare le informazioni di metriche che consentono di cercare i dati di log lato server per informazioni più dettagliate sugli eventuali errori che si verificano. Queste informazioni consentono di risolvere il problema.
- Se le informazioni nei log lato server non sono sufficienti per risolvere il problema correttamente, è possibile utilizzare i log lato client libreria Client lo spazio di archiviazione per esaminare il comportamento dell'applicazione client e strumenti, ad esempio Fiddler Wireshark e Microsoft messaggio Analyzer per analizzare la rete.

Per ulteriori informazioni sull'utilizzo Fiddler, vedere "[appendice 1: utilizzando Fiddler per acquisire il traffico HTTP e HTTPS]."

Per ulteriori informazioni sull'uso di Wireshark, vedere "[Appendice 2: utilizzando Wireshark per acquisire il traffico di rete]."

Per ulteriori informazioni sull'uso di Microsoft messaggio Analyzer, vedere "[appendice 3: utilizzando Microsoft Message Analyzer per acquisire il traffico di rete]."

## <a name="appendices"></a>Appendici

Appendici descrivono diversi strumenti che possono risultare utili quando si diagnostica e risoluzione dei problemi con l'archiviazione Azure (e altri servizi). Questi strumenti non fanno parte di spazio di archiviazione di Azure e alcune sono i prodotti di terze parti. Di conseguenza, gli strumenti descritti nelle appendici non rientrano un contratto di supporto che potrebbe essere a Microsoft Azure o lo spazio di archiviazione di Azure, e pertanto come parte del processo di valutazione è necessario esaminare la licenza e al supporto opzioni disponibili dal provider di servizi di questi strumenti.

### <a name="appendix-1"></a>Appendice 1: Con Fiddler per acquisire il traffico HTTP e HTTPS

Fiddler è uno strumento utile per l'analisi del traffico HTTP e HTTPS tra l'applicazione client e il servizio di archiviazione Azure in uso. È possibile scaricare Fiddler da <a href="http://www.telerik.com/fiddler" target="_blank">http://www.telerik.com/fiddler</a>.

> [AZURE.NOTE] Fiddler decodificare il traffico HTTPS; è consigliabile leggere la documentazione Fiddler con attenzione per comprendere come questa operazione viene eseguita e comprenderne le implicazioni di sicurezza.

Viene fornisce una breve procedura dettagliata su come configurare Fiddler per acquisire il traffico tra il computer locale in cui è installato Fiddler e i servizi di archiviazione Azure.

Dopo avere avviato Fiddler, verrà avviata l'acquisizione di traffico HTTP e HTTPS sul computer locale. Alcuni comandi utili per il controllo Fiddler sono i seguenti:

- Arrestare e avviare il processo di traffico. Nel menu principale, fare clic su **File** e quindi su **Acquisire il traffico** per attivare o disattivare l'acquisizione attivato e disattivato.
- Salva il traffico dati acquisiti. Nel menu principale, passare al **File**, fare clic su **Salva**e quindi fare clic su **Tutte le sessioni**: consente di salvare il traffico in un file di archivio di sessione. È possibile ricaricare un archivio di sessione in un secondo momento per l'analisi o inviarla se richiesto al supporto tecnico Microsoft.

Per limitare la quantità di traffico Fiddler acquisisce, è possibile utilizzare i filtri che consentono di configurare nella scheda **filtri** . La schermata seguente mostra un filtro che acquisisce solo il traffico inviato all'endpoint **contosoemaildist.table.core.windows.net** lo spazio di archiviazione:

![][5]

### <a name="appendix-2"></a>Appendice 2: Utilizzando Wireshark per acquisire il traffico di rete

Wireshark è un analizzatore di rete che consente di visualizzare informazioni dettagliate pacchetto per un'ampia gamma di protocolli di rete. È possibile scaricare Wireshark da <a href="http://www.wireshark.org/" target="_blank">http://www.wireshark.org/</a>.

La procedura seguente viene illustrato come acquisire informazioni dettagliate pacchetto per il traffico dal computer locale in cui è installato Wireshark al servizio di tabella nell'account di archiviazione Azure.

1.  Avviare Wireshark sul computer locale.
2.  Nella sezione **avviare** selezionare la rete locale o più interfacce connessi a internet.
3.  Fare clic su **Opzioni di acquisizione**.
4.  Aggiungere un filtro per la casella di testo di **Filtro acquisizione** . Ad esempio **host contosoemaildist.table.core.windows.net** configurerà Wireshark per acquisire solo i pacchetti inviati da e verso l'endpoint del servizio di tabella nella finestra account di archiviazione **contosoemaildist** . Per un elenco completo dei filtri per acquisire vedere <a href="http://wiki.wireshark.org/CaptureFilters" target="_blank">http://wiki.wireshark.org/CaptureFilters</a>.

    ![][6]

5.  Fare clic su **Start**. Wireshark verrà acquisito tutti i pacchetti scambiati in o da endpoint del servizio tabella quando si usa l'applicazione client nel computer locale.
6.  Al termine, nel menu principale fare clic su **acquisire** e quindi su **Interrompi**.
7.  Per salvare i dati acquisiti in un File di acquisizione Wireshark, nel menu principale fare clic su **File** e quindi su **Salva**.

WireShark evidenzia gli errori che sono presenti nella finestra di **packetlist** . È anche possibile utilizzare la finestra di **Informazioni esperto** (fare clic su **Analizza**e quindi **Informazioni esperto**) per visualizzare un riepilogo di errori e avvisi.

![][7]

È anche possibile scegliere di visualizzare i dati TCP come livello di applicazione possa vederlo facendo sui dati TCP **seguire flusso TCP**. Questa operazione è particolarmente utile se si acquisito l'immagine senza un filtro di acquisizione. Vedere <a href="http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html" target="_blank">di seguito</a> per ulteriori informazioni.

![][8]

> [AZURE.NOTE] Per ulteriori informazioni sull'utilizzo Wireshark, vedere la <a href="http://www.wireshark.org/docs/wsug_html_chunked/" target="_blank">Guida per gli utenti di Wireshark</a>.

### <a name="appendix-3"></a>Appendice 3: Utilizzando Microsoft messaggio Analyzer per acquisire il traffico di rete

È possibile utilizzare Microsoft messaggio Analyzer per acquisire il traffico HTTP e HTTPS in modo simile a Fiddler e acquisire il traffico di rete in modo simile a Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Configurare una sessione di analisi web utilizzando l'analizzatore messaggio Microsoft

Per configurare una sessione di analisi web per il traffico HTTP e HTTPS tramite Microsoft messaggio Analyzer, eseguire l'applicazione di Microsoft messaggio Analyzer, quindi **scegliere/Traccia**dal menu **File** . Nell'elenco di scenari di analisi disponibili selezionare **Proxy Web**. Nel Pannello di **Configurazione di uno Scenario di analisi** , nella casella di testo **HostnameFilter** , quindi aggiungere i nomi degli endpoint lo spazio di archiviazione (è possibile cercare i nomi seguenti nel portale classica di Azure). Ad esempio, se il nome del proprio account Azure dello spazio di archiviazione è **contosodata**, è necessario aggiungere le operazioni seguenti per la casella di testo **HostnameFilter** :

    contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net

> [AZURE.NOTE] Uno spazio separa il nome host.

Quando si è pronti iniziare la raccolta di dati di traccia, fare clic sul pulsante **Inizia con** .

Per ulteriori informazioni sull'analisi Microsoft messaggio Analyzer **Proxy Web** , vedere <a href="http://technet.microsoft.com/library/jj674814.aspx" target="_blank">Provider PEF WebProxy</a> su TechNet.

Traccia dei **Proxy Web** incorporata in Microsoft messaggio Analyzer si basa su Fiddler; può acquisire il traffico HTTPS lato client e visualizzare i messaggi HTTPS non crittografati. La traccia dei **Proxy Web** funziona configurando un proxy locale per tutto il traffico HTTP e HTTPS che consente l'accesso ai messaggi crittografati.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnosticare i problemi di rete utilizzando Microsoft messaggio Analyzer

Oltre a utilizzare la traccia di Microsoft messaggio Analyzer **Proxy Web** per acquisire dettagli del traffico HTTP/HTTPs tra l'applicazione client e il servizio di archiviazione, è possibile usare anche la traccia di **Livello di collegamento locali** predefinita per acquisire informazioni sui pacchetti di rete. In questo modo per acquisire i dati simili a quello che è possibile acquisire con Wireshark e diagnosticare i problemi di rete come pacchetti ignorati.

La schermata seguente mostra un esempio traccia **Locale livello di collegamento** con alcuni messaggi **informativo** nella colonna **DiagnosisTypes** . Fare clic su un'icona nella colonna **DiagnosisTypes** vengono visualizzati i dettagli del messaggio. In questo esempio, il server ritrasmessi messaggio #305 perché non ha ricevuto una conferma dal client:

![][9]

Quando si crea la sessione di analisi in Microsoft messaggio Analyzer, è possibile specificare i filtri per ridurre la quantità di rumore nella traccia. Nella pagina **acquisire / individua** nel punto in cui è possibile definire la traccia fare clic sul collegamento **Configura** accanto a **Microsoft-Windows-NDIS-PacketCapture**. La figura seguente viene illustrata una configurazione che filtra il traffico TCP per gli indirizzi IP di tre servizi di archiviazione:

![][10]

Per ulteriori informazioni sull'analisi Microsoft messaggio Analyzer locale livello di collegamento, vedere <a href="http://technet.microsoft.com/library/jj659264.aspx" target="_blank">PacketCapture NDIS PEF Provider</a> su TechNet.

### <a name="appendix-4"></a>Appendice 4: Utilizzo di Excel per visualizzare le metriche e registrare i dati

Molti strumenti consentono di scaricare i dati di metriche di archiviazione dall'archivio tabelle Azure in un formato con valori delimitati da che semplifica l'individuazione caricare i dati in Excel per la visualizzazione e analisi. Dati di registrazione di spazio di archiviazione da archiviazione blob Azure sono già in un formato con valori delimitati da cui è possibile caricare in Excel. Tuttavia, è necessario aggiungere intestazioni di colonna appropriato in base a informazioni in <a href="http://msdn.microsoft.com/library/azure/hh343259.aspx" target="_blank">Formato di memorizzazione Analitica Log</a> e <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">Lo spazio di archiviazione Analitica metriche Schema della tabella</a>.

Per importare i dati di registrazione di spazio di archiviazione in Excel dopo avere scaricato dallo spazio di archiviazione blob:

- Menu **dati** , fare clic su **Da testo**.
- Individuare il file di log che si desidera visualizzare e fare clic su **Importa**.
- Nel passaggio 1 dell' **Importazione guidata testo**selezionare **delimitati**.

Nel passaggio 1 dell' **Importazione guidata testo**, selezionare **un punto e virgola** come delimitatore solo e scegliere doppie come **qualificatore di testo**. Quindi fare clic su **Fine** e scegliere dove inserire i dati nella cartella di lavoro.

### <a name="appendix-5"></a>Appendice 5: Monitoraggio con informazioni dettagliate sui applicazione per Visual Studio Team Services

È anche possibile usare la caratteristica applicazione approfondimenti per Visual Studio Team Services includendo le prestazioni e il monitoraggio della disponibilità. Questo strumento è possibile:

- Verificare che il servizio web sia disponibile e tempestivo. Se l'app è un sito web o da un'app di dispositivo che utilizza un servizio web, è possibile provare l'URL alcuni minuti da posizioni in tutto il mondo e comunicare se si è verificato un problema.
- Individuare rapidamente eventuali problemi di prestazioni o eccezioni del servizio web. Scoprire se sono allungati CPU o altre risorse, è possibile ottenere le tracce dello stack dall'eccezioni e cercare facilmente tramite le tracce di log. Se le prestazioni dell'app inferiore limiti accettabili, è possibile inviare un messaggio e-mail. È possibile monitorare servizi web .NET e Java.

In fase di scrittura approfondimenti applicazione è in anteprima. Per altre informazioni <a href="http://msdn.microsoft.com/library/azure/dn481095.aspx" target="_blank">all'Applicazione approfondimenti per Visual Studio Team Services su MSDN</a>.


<!--Anchors-->
[Introduzione]: #introduction
[Organizzazione di questa Guida]: #how-this-guide-is-organized

[Monitorare il servizio di archiviazione]: #monitoring-your-storage-service
[Monitorare l'integrità dei servizi]: #monitoring-service-health
[Monitoraggio della capacità]: #monitoring-capacity
[Il controllo della disponibilità]: #monitoring-availability
[Monitoraggio delle prestazioni]: #monitoring-performance

[Diagnosticare i problemi di archiviazione]: #diagnosing-storage-issues
[Problemi di integrità dei servizi]: #service-health-issues
[Problemi di prestazioni]: #performance-issues
[Diagnostica degli errori]: #diagnosing-errors
[Problemi di emulatore lo spazio di archiviazione]: #storage-emulator-issues
[Strumenti di registrazione di spazio di archiviazione]: #storage-logging-tools
[Utilizzo degli strumenti di registrazione di rete]: #using-network-logging-tools

[Analisi di fine-fine]: #end-to-end-tracing
[Correlazione di dati dei registri]: #correlating-log-data
[ID richiesta client]: #client-request-id
[ID richiesta server]: #server-request-id
[Data e ora]: #timestamps

[Indicazioni per la risoluzione dei problemi]: #troubleshooting-guidance
[Metrica Mostra AverageE2ELatency alta e bassa AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Metriche mostrano AverageE2ELatency bassa e AverageServerLatency bassa, ma il client si è verificato latenza elevata]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Metrica Mostra AverageServerLatency elevato]: #metrics-show-high-AverageServerLatency
[Si sono verificati ritardi imprevisti in recapito dei messaggi in una coda]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Metrica Mostra un aumento nel PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Aumento temporaneo PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Aumento permanente PercentThrottlingError errore]: #permanent-increase-in-PercentThrottlingError
[Metrica Mostra un aumento nel PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Metrica Mostra un aumento nel PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[Il client sta ricevendo messaggi HTTP 403 (accesso negato)]: #the-client-is-receiving-403-messages
[Il client sta ricevendo messaggi HTTP 404 (non disponibile)]: #the-client-is-receiving-404-messages
[Il client o un altro processo precedentemente eliminato l'oggetto]: #client-previously-deleted-the-object
[Un problema di autorizzazioni condiviso accesso firma (SA)]: #SAS-authorization-issue
[Codice JavaScript lato client non dispone delle autorizzazioni per accedere all'oggetto]: #JavaScript-code-does-not-have-permission
[Errore di rete]: #network-failure
[Il client sta ricevendo messaggi HTTP 409 (conflitto)]: #the-client-is-receiving-409-messages

[Metrica Mostra PercentSuccess bassa o voci di log analitica avere operazioni con stato ClientOtherErrors]: #metrics-show-low-percent-success
[Metrica capacità Mostra un aumento imprevisto in utilizzo della capacità di archiviazione]: #capacity-metrics-show-an-unexpected-increase
[Si verifica il riavvio imprevisto di macchine virtuali che dispone di un numero elevato di dischi rigidi virtuali allegati]: #you-are-experiencing-unexpected-reboots
[Il problema si verifica da tramite emulatore lo spazio di archiviazione per lo sviluppo o test]: #your-issue-arises-from-using-the-storage-emulator
[Caratteristica "X" non è attivo in emulatore lo spazio di archiviazione]: #feature-X-is-not-working
[Errore "il valore di una delle intestazioni HTTP non è nel formato corretto" quando si utilizza l'emulatore lo spazio di archiviazione]: #error-HTTP-header-not-correct-format
[Esegue l'emulatore lo spazio di archiviazione richiede privilegi di amministratore]: #storage-emulator-requires-administrative-privileges
[Si problemi durante l'installazione di Azure SDK per .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Si dispone di un problema diverso relativo a un servizio di archiviazione]: #you-have-a-different-issue-with-a-storage-service

[Appendici]: #appendices
[Appendice 1: Con Fiddler per acquisire il traffico HTTP e HTTPS]: #appendix-1
[Appendice 2: Utilizzando Wireshark per acquisire il traffico di rete]: #appendix-2
[Appendice 3: Utilizzando Microsoft messaggio Analyzer per acquisire il traffico di rete]: #appendix-3
[Appendice 4: Utilizzo di Excel per visualizzare le metriche e registrare i dati]: #appendix-4
[Appendice 5: Monitoraggio con informazioni dettagliate sui applicazione per Visual Studio Team Services]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/overview.png
[2]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/portal-screenshot.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/mma-screenshot-2.png
