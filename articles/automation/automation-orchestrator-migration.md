<properties
   pageTitle="Migrazione da Orchestrator a automazione Azure | Microsoft Azure"
   description="In questo articolo viene descritto come eseguire la migrazione Pack runbook e integrazione da System Center Orchestrator all'automazione di Azure."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />


# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrazione da Orchestrator a automazione Azure (Beta)

Runbook [System Center](http://technet.microsoft.com/library/hh237242.aspx) Orchestrator seguono si basano sulle attività da integration pack scritti specificamente per Orchestrator mentre runbook in Azure automazione basati su Windows PowerShell.  [Runbook grafica](automation-runbook-types.md#graphical-runbooks) in Azure automazione hanno un aspetto simile a Orchestrator runbook relativi alle attività che rappresenta i cmdlet PowerShell, runbook figlio e risorse.

[System Center Orchestrator migrazione Toolkit](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) include strumenti per facilitare la conversione di runbook da Orchestrator automazione Azure.  Oltre a conversione runbook se stessi, è necessario convertire i pacchetti di integrazione con le attività che utilizzano il runbook ai moduli di integrazione con i cmdlet di Windows PowerShell.  

Di seguito è riportato il processo di base per la conversione Orchestrator runbook in Azure automazione.  Ognuna di queste operazioni viene descritta in dettaglio nelle sezioni seguenti.

1.  Scaricare il [Toolkit di migrazione di System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) che contiene gli strumenti e i moduli descritti in questo articolo.
2.  Importare [il modulo di attività Standard](#standard-activities-module) automazione Azure.  Sono inclusi convertire le versioni delle attività Orchestrator standard che possono essere usate dal runbook convertire.
3.  Importare [System Center Orchestrator integrazione moduli](#system-center-orchestrator-integration-modules) in Azure automazione per i pacchetti di integrazione utilizzati il runbook che accedono System Center.
4.  Convertire personalizzata e terze parti integration pack tramite il [Convertitore Pack integrazione](#integration-pack-converter) e importare in automazione Azure.
5.  Convertire runbook Orchestrator tramite il [Convertitore Runbook](#runbook-converter) e installare l'automazione Azure.
6.  Creare manualmente risorse Orchestrator necessari Azure automazione poiché il Runbook convertire queste risorse.
7.  Configurare un [Lavoro Runbook ibrida](#hybrid-runbook-worker) nell'interfaccia di dati locali per l'esecuzione convertire runbook che avrà accesso alle risorse locali.

## <a name="service-management-automation"></a>Automazione di gestione dei servizi

[Automazione di gestione dei servizi](http://technet.microsoft.com/library/dn469260.aspx) (SMA) archivia ed esegue runbook nell'interfaccia di dati locali come Orchestrator e utilizza i moduli di integrazione stesso come automazione Azure. Il [Convertitore Runbook](#runbook-converter) converte Orchestrator runbook runbook grafica attraverso che non sono supportate in SMA.  È ancora possibile installare il [Modulo attività Standard](#standard-activities-module) e [System Center Orchestrator integrazione moduli](#system-center-orchestrator-integration-modules) in SMA, ma è necessario manualmente [il runbook di riscrittura](http://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Lavoro Runbook ibrido

Runbook in Orchestrator vengono memorizzati in un server di database ed eseguire sui server runbook, entrambe nell'interfaccia di dati locali.  Runbook in Azure automazione archiviati nel cloud Azure ed eseguire nel centro dati locale mediante un [Lavoro Runbook ibrida](automation-hybrid-runbook-worker.md).  Verrà visualizzata come in genere si eseguiranno runbook convertito Orchestrator poiché sono progettate per eseguire analisi sui server locali.

## <a name="integration-pack-converter"></a>Integrazione con Service Pack convertitore

Il convertitore Pack integrazione converte integration pack create [Orchestrator integrazione Toolkit (OIT)](http://technet.microsoft.com/library/hh855853.aspx) ai moduli integrazione basata su Windows PowerShell che possono essere importati in Azure automazione o automazione di gestione dei servizi.  

Quando si esegue il convertitore Pack integrazione, viene visualizzata una creazione guidata che consente di selezionare un file di pacchetto (.oip) integrazione.  La procedura guidata quindi Elenca le attività incluse in tale pack integrazione e consente di selezionare cui verrà eseguita la migrazione.  Dopo aver completato la procedura guidata, viene creato un modulo di integrazione che include un cmdlet corrispondente per ogni attività nel pacchetto di integrazione originale.


### <a name="parameters"></a>Parametri

Tutte le proprietà di un'attività nel pacchetto di integrazione vengono convertite in parametri del cmdlet corrispondente nel modulo integrazione.  Cmdlet di Windows PowerShell dispone di un insieme di [parametri comuni](http://technet.microsoft.com/library/hh847884.aspx) che possono essere usate con tutti i cmdlet.  Ad esempio,-parametro genera un cmdlet generare l'output informazioni dettagliate relative al funzionamento.  Nessun cmdlet potrebbe avere un parametro con lo stesso nome di un parametro comune.  Se un'attività dispone di una proprietà con lo stesso nome di un parametro comune, la procedura guidata verrà chiesto di specificare un nome diverso per il parametro.

### <a name="monitor-activities"></a>Monitor attività

Monitor runbook in Orchestrator iniziare con un [monitorare l'attività](http://technet.microsoft.com/library/hh403827.aspx) ed eseguire sempre in attesa di essere richiamato da un determinato evento.  Automazione Azure non supporta runbook monitor, in modo che tutte le attività monitor nel pacchetto di integrazione non essere convertite.  Se, tuttavia, viene creato un cmdlet segnaposto nel modulo di integrazione per monitorare l'attività.  Questo cmdlet non ha funzionalità, ma consente qualsiasi runbook convertito che utilizza l'installazione.  Questo runbook non saranno in grado di eseguire in Azure automazione, ma può essere installato in modo da poterlo modificare.

### <a name="integration-packs-that-cannot-be-converted"></a>Pacchetti di integrazione che non possono essere convertiti

Pacchetti di integrazione che non sono stati creati con OIT non possono essere convertiti con il convertitore Pack integrazione. Esistono alcune integration pack fornito da Microsoft che attualmente non può essere convertito in questo strumento.  Convertire le versioni di questi Pack integrazione sono stati [fornito per il download](#system-center-orchestrator-integration-modules) in modo che può essere installati in Azure automazione o automazione di gestione dei servizi.


## <a name="standard-activities-module"></a>Modulo attività standard

Orchestrator include una serie di [attività standard](http://technet.microsoft.com/library/hh403832.aspx) non vengono incluse in un pacchetto di integrazione ma utilizzati dal runbook molti.  Le attività Standard è un modulo di integrazione che include un cmdlet equivalente per ognuna di queste attività.  È necessario installare il modulo di integrazione di Azure automazione prima di importare qualsiasi runbook convertiti che utilizzano un'attività standard.

Oltre al supporto runbook convertiti, i cmdlet nel modulo attività standard possono essere usati dal chiunque abbia familiarità con Orchestrator per creare nuove runbook in Azure automazione.  Durante la funzionalità di tutte le attività standard può essere eseguita con i cmdlet, potrebbero funzionare in modo leggermente diverso.  I cmdlet nel modulo attività standard convertito verranno funzionano allo stesso modo le loro attività corrispondente e utilizzare gli stessi parametri.  Questo può consentire la creazione di runbook Orchestrator esistente in del passaggio a runbook automazione Azure.

## <a name="system-center-orchestrator-integration-modules"></a>System Center Orchestrator integrazione moduli

Microsoft offre [integration pack](http://technet.microsoft.com/library/hh295851.aspx) per la creazione di runbook per automatizzare componenti System Center e altri prodotti.  Alcuni di questi Pack integrazione attualmente basati su OIT ma attualmente non può essere convertiti ai moduli integrazione a causa di problemi noti.  [System Center Orchestrator integrazione moduli](https://www.microsoft.com/download/details.aspx?id=49555) include convertire le versioni di questi Pack integrazione che possono essere importati in Azure automazione e automazione di gestione dei servizi.  

Nella versione RTM di questo strumento, versioni aggiornate dei pacchetti di integrazione in base a OIT che può essere convertito con il convertitore Pack integrazione verranno pubblicate.  Verranno inoltre fornite indicazioni per facilitare la conversione runbook mediante le attività da integration pack non basato sul OIT.

## <a name="runbook-converter"></a>Convertitore runbook

Il convertitore Runbook converte Orchestrator runbook in [runbook grafica](automation-runbook-types.md#graph-runbooks) che possono essere importati in Azure automazione.  

Convertitore runbook è implementata come modulo PowerShell con un cmdlet denominato **ConvertFrom SCORunbook** che viene eseguita la conversione.  Quando si installa lo strumento, verrà creato un collegamento a una sessione di PowerShell che carica il cmdlet.   

Di seguito è riportato il processo di base per convertire un runbook Orchestrator e importare in automazione Azure.  Nelle sezioni seguenti per informazioni dettagliate sulla tramite lo strumento e l'utilizzo con runbook convertire.

1. Esportare una o più runbook da Orchestrator.
2. Ottenere i moduli di integrazione per tutte le attività dal runbook.
3. Convertire runbook Orchestrator nel file esportato.
4. Esaminare le informazioni nei registri per convalidare la conversione e determinare qualsiasi attività manuale richiesta.
4. È possibile importare runbook convertito in Azure automazione.
5. Creare tutte le risorse necessarie in Azure automazione.
6. Modificare runbook di automazione di Azure per modificare le attività necessarie.

### <a name="using-runbook-converter"></a>Utilizzo di Runbook convertitore

La sintassi per **ConvertFrom SCORunbook** è il seguente:

    ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string> 

- RunbookPath - percorso del file di esportazione contenente runbook da convertire.
- Modulo - elenco delimitato di moduli di integrazione contenente le attività dei runbook.
- CartellaOutput - percorso della cartella per creare convertiti runbook grafica. 


Il comando di esempio seguente converte runbook in un file di esportazione denominato **MyRunbooks.ois_export**.  Questi runbook utilizzare i pacchetti di integrazione con Active Directory e gestione di protezione di dati.

    ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks" 


### <a name="log-files"></a>File di log

Il convertitore Runbook creerà i file di log seguenti nella stessa posizione runbook convertire.  Se i file esistono già, quindi verranno sovrascritti con le informazioni ultima conversione.

| File | Contenuto |
|:---|:---|
| Convertitore runbook - Progress | Procedura dettagliata della conversione quali informazioni per ogni attività convertito e avviso per ogni attività non convertito. |
| Convertitore runbook - Summary.log  | Riepilogo della conversione ultima inclusi tutti gli avvisi e seguire le attività che è necessario eseguire, ad esempio la creazione di una variabile necessaria per convertire runbook.  |

### <a name="exporting-runbooks-from-orchestrator"></a>Esportazione runbook da Orchestrator

Il convertitore Runbook funziona con un file di esportazione da Orchestrator che contiene uno o più runbook.  Verrà creato un runbook Azure automazione corrispondente per ogni runbook Orchestrator nel file di esportazione.  

Per esportare un runbook da Orchestrator, pulsante destro del mouse sul nome dal runbook Runbook Designer e selezionare **Esporta**.  Per esportare tutti runbook in una cartella, pulsante destro del mouse sul nome della cartella e scegliere **Esporta**.


### <a name="runbook-activities"></a>Attività runbook

Il convertitore Runbook converte ciascuna attività runbook Orchestrator in un'attività corrispondente nel modello di automazione Azure.  Per le attività non è possibile convertire una segnaposto creazione di attività nel runbook con testo di avviso.  Dopo aver importato runbook convertito in Azure automazione, è necessario sostituire tutte queste attività con attività valida che eseguono le funzionalità necessarie.

Verranno convertiti qualsiasi attività Orchestrator nel [Modulo attività Standard](#standard-activities-module) .  Esistono alcune attività Orchestrator standard che non sono in questo modulo attraverso e non vengono convertite.  **Ad esempio, inviare piattaforma evento** senza automazione Azure equivalente poiché l'evento è specifico di Orchestrator

[Attività monitor](https://technet.microsoft.com/library/hh403827.aspx) non vengono convertiti poiché non esiste alcun equivalente a tali automazione Azure.  L'eccezione sono attività monitor [convertire integration pack](#integration-pack-converter) che verranno convertite in attività segnaposto.

Verranno convertiti qualsiasi attività da un [convertire pack integrazione](#integration-pack-converter) se si specifica il percorso del modulo di integrazione con il parametro **moduli** .  Per System Center Integration Pack, è possibile utilizzare [System Center Orchestrator integrazione moduli](#system-center-orchestrator-integration-modules).


### <a name="orchestrator-resources"></a>Risorse Orchestrator

Il convertitore Runbook converte solo runbook, non ad altre risorse Orchestrator, ad esempio contatori, variabili o connessioni.  Contatori non sono supportati in Azure automazione.  Le variabili e connessioni sono supportate, ma è necessario creare manualmente.  I file di log verranno si determinano se dal runbook richiede tali risorse e specificare risorse corrispondenti che è necessario creare in Azure automazione per convertire runbook per il corretto funzionamento.

Ad esempio un runbook possono utilizzare una variabile per popolare un valore specifico in un'attività.  Convertire runbook verrà convertire in attività e specificare una risorsa variabile in Azure automazione con lo stesso nome della variabile Orchestrator.  Questo verrà indicato nel file **Convertitore Runbook - Summary.log** creati dopo la conversione.  È necessario creare manualmente questa variabile bene Azure automazione prima di utilizzare dal runbook. 

 
### <a name="input-parameters"></a>Parametri di input

Runbook in Orchestrator accettare parametri di input con le attività **Inizializzare dei dati** .  Se dal runbook convertito include questa attività, viene creato un [parametro di input](automation-graphical-authoring-intro.md#runbook-input-and-output) in runbook Azure automazione per ogni parametro nell'attività.  Verrà creata un'attività di [controllo Script del flusso di lavoro](automation-graphical-authoring-intro.md#activities) in runbook convertito che recupera e restituisce ogni parametro.  Qualsiasi attività dal runbook che utilizzano un parametro di input fare riferimento all'output dall'attività.

Il motivo viene utilizzata questa strategia è meglio fedelmente la funzionalità di runbook Orchestrator.  Attività nei nuovi runbook grafica devono fare riferimento direttamente parametri di input utilizzando un'origine dati di input Runbook.


### <a name="invoke-runbook-activity"></a>Richiamare Runbook attività

Runbook in Orchestrator avviare altri runbook all'attività **Runbook richiamare** . Se runbook convertito include questa attività è impostata l'opzione **attendere il completamento** , un'attività runbook viene creata per renderla in runbook convertire.  Se non è impostata l'opzione **attendere il completamento** , un'attività del flusso di lavoro Script viene creata utilizzato **AzureAutomationRunbook inizio** per avviare dal runbook.  Dopo aver importato runbook convertito in Azure automazione, è necessario modificare questa attività con le informazioni specificate nell'attività.




## <a name="related-articles"></a>Articoli correlati

- [System Center 2012 - Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
- [Automazione di gestione dei servizi](https://technet.microsoft.com/library/dn469260.aspx)
- [Lavoro Runbook ibrido](automation-hybrid-runbook-worker.md)
- [Attività Standard Orchestrator](http://technet.microsoft.com/library/hh403832.aspx)
- [Toolkit di migrazione di download System Center Orchestrator](https://www.microsoft.com/en-us/download/details.aspx?id=47323)
 
