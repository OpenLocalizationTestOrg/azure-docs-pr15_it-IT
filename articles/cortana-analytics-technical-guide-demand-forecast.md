<properties
    pageTitle="Richiedere previsione in Guide tecniche di energia | Microsoft Azure"
    description="Guida tecnica per il modello di soluzione con Microsoft Cortana Intelligence per previsione in energia della domanda."
    services="cortana-analytics"
    documentationCenter=""
    authors="yijichen"
    manager="ilanr9"
    editor="yijichen"/>

<tags
    ms.service="cortana-analytics"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="inqiu;yijichen;ilanr9"/>

# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>Guide tecniche al modello di soluzione di Business Intelligence Cortana per previsione in energia della domanda

## <a name="overview"></a>**Panoramica**

Modelli di soluzioni sono progettati per accelerare il processo di creazione di una demo E2E nella parte superiore di famiglia di prodotti Business Intelligence Cortana. Un modello distribuito verrà provisioning l'abbonamento con componente di Business Intelligence Cortana necessario e creare le relazioni tra. Inoltre semi pipeline di dati con dati di esempio Guida generati da un'applicazione di simulazione di dati. Scaricare il simulatore di dati tramite il collegamento indicato e installarlo nel computer locale, consultare il file Leggimi per istruzioni su utilizza simulatore. Dati generati da simulatore verranno idrato la pipeline di dati e avviare la creazione di previsione di apprendimento computer che può quindi essere visualizzato nel dashboard di Power BI.

Sono disponibili nel modello di soluzione [qui](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1) 

Il processo di distribuzione consentirà diversi passaggi per configurare le credenziali di soluzione. Assicurarsi che la registrazione di queste credenziali, ad esempio Nome soluzione, nome utente e password forniti durante la distribuzione.

L'obiettivo di questo documento è illustrare l'architettura di riferimento e diversi componenti effettuato il provisioning in abbonamento come parte di questo modello di soluzione. Il documento parla anche come sostituire i dati di esempio con dati reali nel forum relativo a essere in grado di visualizzare informazioni dettagliate sui/le stime da di dati. Inoltre, il documento parla parti del modello di soluzione che devono essere modificate se si desidera personalizzare la soluzione sui propri dati. Vengono fornite istruzioni su come creare dashboard di Power BI per il modello di soluzione alla fine.

## <a name="big-picture"></a>**Quadro generale**

![](media\cortana-analytics-technical-guide-demand-forecast\ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>Architettura spiegato
Quando viene distribuita la soluzione, diversi servizi di Azure all'interno di Cortana Analitica Suite sono attivati (*ad esempio* Hub di evento, flusso Analitica, HDInsight, Data Factory, apprendimento e *così via*). Architettura Mostra il diagramma precedente, ad alto livello, come la richiesta di previsione per il modello di soluzione di energia è costituita da-to-end. Sarà possibile provare a utilizzare questi servizi facendo clic su di essi nel diagramma modello di soluzione creato con la distribuzione della soluzione. Le sezioni seguenti descrivono ogni parte.

## <a name="data-source-and-ingestion"></a>**Acquisizione e l'origine dati**

### <a name="synthetic-data-source"></a>Origine dati sintetici

Per il modello viene generato l'origine dati utilizzata da un'applicazione desktop che verranno scaricati ed eseguire localmente al termine della distribuzione. Sono disponibili le istruzioni per scaricare e installare l'applicazione nella barra della proprietà quando si seleziona il primo nodo denominato simulatore di dati di previsione di energia nel diagramma modello soluzione. Questa applicazione feed il servizio di [Azure evento Hub](#azure-event-hub) con punti dati o gli eventi, che verranno utilizzati il resto del flusso di soluzione.

Evento generazione verrà inserito Hub evento Azure solo durante l'esecuzione nel computer in uso.

### <a name="azure-event-hub"></a>Hub evento Azure

Il servizio di [Azure evento Hub](https://azure.microsoft.com/services/event-hubs/) è il destinatario dell'input forniti dall'origine dati sintetici descritte in precedenza.

## <a name="data-preparation-and-analysis"></a>**Analisi e preparazione dei dati**


### <a name="azure-stream-analytics"></a>Flusso Azure Analitica

Per fornire vicino analitica in tempo reale sul flusso di input dal servizio di [Azure evento Hub](#azure-event-hub) e pubblicare i risultati in un dashboard di [Power BI](https://powerbi.microsoft.com) e l'archiviazione di tutti gli eventi in arrivo elaborati al servizio di [Archiviazione Azure](https://azure.microsoft.com/services/storage/) per l'elaborazione successiva mediante il servizio di [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) viene usato il servizio di [Azure flusso Analitica](https://azure.microsoft.com/services/stream-analytics/) .

### <a name="hd-insights-custom-aggregation"></a>Aggregazione personalizzata approfondimenti HD

Per eseguire gli script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (gestiti da Azure Data Factory) per fornire le aggregazioni negli eventi che sono stati archiviati mediante il servizio di Azure flusso Analitica viene usato il servizio di Azure HD comprensione.

### <a name="azure-machine-learning"></a>Apprendimento Azure

Viene usato il servizio di [Apprendimento Azure](https://azure.microsoft.com/services/machine-learning/) (gestito da Azure Data Factory) per eseguire previsioni sulla consumo di energia future di una determinata data input ricevuti.

## <a name="data-publishing"></a>**Pubblicazione dei dati**


### <a name="azure-sql-database-service"></a>Servizio di Database SQL Azure

Il servizio di [Database SQL Azure](https://azure.microsoft.com/services/sql-database/) viene utilizzato per archiviare (gestita da Azure Data Factory) le stime ricevute dal servizio apprendimento Azure utilizzati nel dashboard di [Power BI](https://powerbi.microsoft.com) .

## <a name="data-consumption"></a>**Consumo di dati**

### <a name="power-bi"></a>Power BI

Per visualizzare un dashboard che contiene le aggregazioni forniti da [Azure flusso Analitica](https://azure.microsoft.com/services/stream-analytics/) servizio come richiesta previsione risultati archiviati nel [Database di SQL Azure](https://azure.microsoft.com/services/sql-database/) generate mediante il servizio di [Apprendimento Azure](https://azure.microsoft.com/services/machine-learning/) viene usato il servizio di [Power BI](https://powerbi.microsoft.com) . Per istruzioni su come creare dashboard di Power BI per il modello di soluzione, vedere la sezione seguente.

## <a name="how-to-bring-in-your-own-data"></a>**Come importare i propri dati**

In questa sezione viene descritto come importare i propri dati in Azure e quali aree richiedono modifiche per i dati che importare questa architettura.

Non viene in genere che qualsiasi set di dati che è importare corrisponderebbe il set di dati utilizzato per il modello di soluzione. Informazioni sui dati e i requisiti sarà fondamentale come si modifica il modello per gestire i propri dati. Se si tratta del primo esposizione al servizio di apprendimento Azure, è possibile ottenere un'introduzione a essa mediante l'esempio su [come creare il primo prova](machine-learning\machine-learning-create-experiment.md).

Nelle sezioni seguenti verranno illustrati le sezioni del modello che richiedono modifiche quando è stato introdotto un nuovo set di dati.

### <a name="azure-event-hub"></a>Hub evento Azure

Il servizio di [Azure evento Hub](https://azure.microsoft.com/services/event-hubs/) è molto generico, in modo che all'hub in formato CSV o JSON, è possibile registrare i dati. Si verifica alcuna elaborazione speciale nell'Hub evento Azure, ma è importante che comprendere i dati che vengono caricati.

In questo documento non descrive come acquisire i dati, ma uno può facilmente inviare dati o eventi a un Hub di eventi di Azure, tramite l' [API Hub evento](event-hubs\event-hubs-programming-guide.md).

### <a name="azure-stream-analytics"></a>Flusso Azure Analitica

Per fornire vicino analitica in tempo reale importando da flussi di dati e output dei dati a un numero qualsiasi di origini viene usato il servizio di [Azure flusso Analitica](https://azure.microsoft.com/services/stream-analytics/) .

Per la richiesta la previsione per il modello di soluzione di energia, la query di Azure flusso Analitica è costituito da due sottoquery, ogni utilizzo degli eventi del servizio di Azure evento Hub come input e di dover output di due percorsi distinti. Questi output è costituito da un set di dati di Power BI e un percorso di archiviazione di Azure.

La query di [Azure flusso Analitica](https://azure.microsoft.com/services/stream-analytics/) può essere trovata tramite:

-   Accesso al [portale di gestione di Azure](https://manage.windowsazure.com/)

-   Individuare i processi di analitica flusso ![](media\cortana-analytics-technical-guide-demand-forecast\icon-stream-analytics.png) che sono state generate quando la soluzione è stata distribuita. Uno per l'inserimento di dati a blob lo spazio di archiviazione (ad esempio mytest1streaming432822asablob) e l'altro valore per l'inserimento di dati a Power BI (ad esempio mytest1streaming432822asapbi).


-   La selezione

    -   ***Ingressi*** per visualizzare l'input di query

    -   ***QUERY*** per visualizzare la query stessa

    -   ***Invia*** per visualizzare diversi output

Informazioni sulla creazione della query di Azure flusso Analitica disponibili nel [Riferimento Query Analitica flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx) su MSDN.

In questa soluzione, il processo di Azure flusso Analitica che produce set di dati con vicino informazioni analitica in tempo reale il flusso di dati in arrivo a un dashboard di Power BI viene fornito come parte di questo modello di soluzione. Poiché non esiste una conoscenza implicita informazioni sul formato di dati in arrivo, tali query necessario intervenire in base al formato dati.

Processo Analitica flusso Azure restituisce tutti gli eventi di [Evento Hub](https://azure.microsoft.com/services/event-hubs/) allo [Spazio di archiviazione di Azure](https://azure.microsoft.com/services/storage/) e pertanto non richiede alcuna modifica indipendentemente il formato dati come le informazioni sugli eventi completo trasmessi in streaming allo spazio di archiviazione.

### <a name="azure-data-factory"></a>Dati di Azure Factory

Il servizio di [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) coordina lo spostamento e l'elaborazione dei dati. La richiesta la previsione per il modello di soluzione di energia factory dati è costituito da dodici [pipeline](data-factory\data-factory-create-pipelines.md) spostare ed elaborare dati utilizzando diverse tecnologie.

  È possibile accedere il produttore dati aprendo il nodo Data Factory nella parte inferiore del diagramma modello di soluzione creata con la distribuzione della soluzione. Verrà visualizzata la factory di dati nel portale di gestione Azure. Se vengono visualizzati errori nel set di dati, è possibile ignorare quelli presenti a causa di factory dati distribuito prima il generatore di dati è stato avviato. Gli errori non factory i dati di funzionare.

In questa sezione vengono illustrate le necessarie [tubazioni](data-factory\data-factory-create-pipelines.md) e [le attività](data-factory\data-factory-create-pipelines.md) contenute in [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Di seguito è la visualizzazione Diagramma della soluzione.

![](media\cortana-analytics-technical-guide-demand-forecast\ADF2.png)

Cinque delle pipeline di questa factory contengono script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) utilizzati per suddividere e aggregare i dati. Quando indicato, gli script sono disponibili nell'account di [Archiviazione Azure](https://azure.microsoft.com/services/storage/) creato durante l'installazione. Il percorso sarà: demandforecasting\\\\script\\\\hive\\ \\ (o https://[Your soluzione name].blob.core.windows.net/demandforecasting).

Analogamente alle query [Azure flusso Analitica](#azure-stream-analytics-1) , gli script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) hanno una conoscenza implicita informazioni sul formato di dati in arrivo, tali query dovrà essere modificato in base alle proprie esigenze di [Progettazione di funzionalità](machine-learning\machine-learning-feature-selection-and-engineering.md) e formato dati.

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*

Questa pipeline [pipeline](data-factory\data-factory-create-pipelines.md) contiene una singola attività - un'attività [HDInsightHive](data-factory\data-factory-hive-activity.md) utilizzando [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) che esegue uno script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) per aggregare ogni 10 secondi in streaming nei dati di richiesta di livello sottostazione a ogni ora l'area geografica e inserire in [Archiviazione Azure](https://azure.microsoft.com/services/storage/) mediante il processo di Azure flusso Analitica.

Lo script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) per questa attività partizione è ***AggregateDemandRegion1Hr.hql***


#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*

Questa [pipeline](data-factory\data-factory-create-pipelines.md) contiene due attività:
- Attività [HDInsightHive](data-factory\data-factory-hive-activity.md) utilizzando [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) che esegue uno script Hive per aggregare i dati di richiesta cronologia oraria nel livello sottostazione a ogni ora l'area geografica e mettere in archiviazione Azure durante il processo di Azure flusso Analitica

- Attività di [Copia](https://msdn.microsoft.com/library/azure/dn835035.aspx) che consente di spostare i dati aggregati da blob Azure lo spazio di archiviazione al Database SQL Azure è stato eseguito il provisioning come parte dell'installazione di modello soluzione.

Script [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) per questa attività è ***AggregateDemandHistoryRegion.hql***.


#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*

Il cui risultato finale è le stime punteggio da prova apprendimento Azure associato a questo modello di soluzione questi [pipeline](data-factory\data-factory-create-pipelines.md) contiene diverse attività. Sono quasi identici, ad eccezione di ciascuno di essi gestisce solo l'area diversa ovvero da diversi RegionID passato pipeline alimentatore automatico e lo script hive per ogni area.  
Le attività contenute in questo sono:
-   Attività [HDInsightHive](data-factory\data-factory-hive-activity.md) utilizzando [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) che esegue script Hive per l'esecuzione di aggregazioni e Progettazione funzionalità necessarie per la prova di apprendimento Azure. Gli script Hive per questa attività sono rispettivi ***PrepareMLInputRegionX.hql***.

-   Attività di [Copia](https://msdn.microsoft.com/library/azure/dn835035.aspx) che consente di spostare i risultati dall'attività [HDInsightHive](data-factory\data-factory-hive-activity.md) a un singolo blob Azure lo spazio di archiviazione che si può accedere dall'attività [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) .

-   Attività di [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) che chiama l'apprendimento di Azure sperimentare blob in base alla quale i risultati vengono inseriti in un unico spazio di archiviazione di Azure.

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
Questi [pipeline](data-factory\data-factory-create-pipelines.md) contengono una singola attività - un'attività di [Copia](https://msdn.microsoft.com/library/azure/dn835035.aspx) riguardante i risultati di prova apprendimento Azure dal rispettivi ***MLScoringRegionXPipeline*** al Database SQL Azure è stato eseguito il provisioning come parte dell'installazione di modello soluzione.

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
Questa [pipeline](data-factory\data-factory-create-pipelines.md) contengono una singola attività - un'attività di [Copia](https://msdn.microsoft.com/library/azure/dn835035.aspx) riguardante i dati aggregati richiesta in corso dal ***LoadHistoryDemandDataPipeline*** al Database SQL Azure è stato eseguito il provisioning come parte dell'installazione di modello soluzione.

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
Questi [pipeline](data-factory\data-factory-create-pipelines.md) contengono una singola attività - un'attività di [Copia](https://msdn.microsoft.com/library/azure/dn835035.aspx) che consente di spostare i dati di riferimento della regione/sottostazione/Topologygeo caricati blob Azure archiviazione come parte dell'installazione di modello soluzione al Database SQL Azure è stato eseguito il provisioning come parte dell'installazione di modello soluzione.

### <a name="azure-machine-learning"></a>Apprendimento Azure
Prova [Apprendimento Azure](https://azure.microsoft.com/services/machine-learning/) utilizzata per questo modello di soluzione offre la stima di richiesta dell'area. La prova specifica per il set di dati utilizzato e pertanto sarà necessaria la modifica o sostituzione specifiche dei dati che viene caricati in.

## <a name="monitor-progress"></a>**Monitorare l'avanzamento**
Una volta che viene avviato il generatore di dati, la pipeline inizia a ottenere Alluminosilicato e i diversi componenti della soluzione iniziare qui in seguito azione i comandi rilasciati da Factory dati. Esistono due modi per monitorare la pipeline.

1. Controllare i dati dallo spazio di archiviazione Blob Azure.

    Uno dei processi flusso Analitica scrive i dati non elaborati in arrivo a archiviazione blob. Se si fa clic sul componente di **Archiviazione Blob Azure** della soluzione dalla schermata è correttamente installato la soluzione e quindi fare clic su **Apri** nel Pannello di destra, si passerà al [portale di gestione Azure](https://portal.azure.com). Una volta, fare clic su **BLOB**. Nella schermata successiva, si vedrà un elenco di contenitori. Fare clic su **"energysadata"**. Nella schermata successiva, verrà visualizzata la cartella **"demandongoing"** . All'interno della cartella rawdata, verranno visualizzate cartelle con nomi, ad esempio Data = 2016-01-28 e così via. Se viene visualizzato queste cartelle, significa che i dati non elaborati correttamente generata nel computer in uso e archiviati in archiviazione blob. Verranno visualizzati i file che devono avere dimensioni limitate in MB in tali cartelle.

2. Controllare i dati da Database SQL Azure.

    L'ultimo passaggio della pipeline è scrivere i dati (ad esempio le stime di apprendimento) nel Database di SQL. Potrebbe essere necessario attendere fino a 2 ore per visualizzare i dati nel Database di SQL. È possibile controllare la quantità di dati è disponibile nel Database di SQL è tramite [il portale di gestione Azure](https://manage.windowsazure.com/). Nel riquadro sinistro individuare database SQL![](media\cortana-analytics-technical-guide-demand-forecast\SQLicon2.png) e fare clic su esso. Quindi individuare il database (ad esempio demo123456db) e fare clic su di esso. Nella pagina successiva della sezione **"Connettersi al database di"** , fare clic su **"Esegui Transact-SQL query sul database di SQL"**.

    In questo caso, è possibile fare clic su Nuova Query e query per il numero di righe (ad esempio "select count da DemandRealHourly)" durante il database che si sviluppa, necessario aumentare il numero di righe nella tabella.)

3. Controllare i dati dal dashboard di Power BI.

    È possibile impostare dashboard di Power BI percorso critico per monitorare i dati non elaborati in arrivo. Seguire le istruzioni nella sezione "Power BI Dashboard".



## <a name="power-bi-dashboard"></a>**Dashboard di Power BI**

### <a name="overview"></a>Panoramica

In questa sezione viene descritto come configurare dashboard di Power BI per visualizzare i dati in tempo reale da analitica flusso Azure (percorso critico), come previsione risultati da Azure apprendimento automatico (percorso fredda).


### <a name="setup-hot-path-dashboard"></a>Configurazione del Dashboard percorso critico

La procedura seguente mostrerà come visualizzare l'output dei dati in tempo reale processi flusso Analitica generati in fase di distribuzione di soluzioni. Per eseguire la procedura seguente è necessario un account di [Power BI online](http://www.powerbi.com/) . Se non si dispone di un account, è possibile [crearne uno](https://powerbi.microsoft.com/pricing).

1.  Aggiungere l'output di Power BI in Azure flusso Analitica (ASA).

    -  È necessario seguire le istruzioni in [Azure flusso Analitica e Power BI: un dashboard in tempo reale analitica visibilità in tempo reale del flusso di dati](stream-analytics-power-bi-dashboard.md) per impostare l'output del processo di Azure flusso Analitica come il dashboard di Power BI.

    - Individuare il processo di analitica flusso nel [portale di gestione Azure](https://manage.windowsazure.com). Il nome del processo deve essere: YourSolutionName + "streamingjob" + numero casuale + "asapbi" (ad esempio demostreamingjob123456asapbi).

    - Aggiungere un output di ottenere informazioni per il processo ASA. Impostare l' **Alias di Output** come **'PBIoutput'**. Impostare il **Nome di set di dati** e il **nome della tabella** come **'EnergyStreamData'**. Una volta aggiunti l'output, fare clic su **"Start"** nella parte inferiore della pagina per avviare il processo di flusso Analitica. Viene visualizzato un messaggio di conferma (*ad esempio*, "Avvio flusso analitica processo myteststreamingjob12345asablob ha avuto esito positivo").

2. Accedere a [Power BI in linea](http://www.powerbi.com)

    -   Nel riquadro sinistro di sezione set di dati nell'area di lavoro personale, dovrebbe essere possibile visualizzare un nuovo set di dati che mostra nel riquadro sinistro di Power BI. Si tratta dei dati flussi inserito da Azure flusso Analitica nel passaggio precedente.

    -   Verificare che il riquadro ***visualizzazioni*** sia aperto e viene visualizzato sul lato destro dello schermo.

3. Creare il riquadro "Richiesta da Timestamp":
    -   Fare clic su set di dati **'EnergyStreamData'** nel riquadro sinistro sezione set di dati.

    -   Fare clic sull'icona **"grafico a linee"** ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic8.png).

    -   Fare clic su 'EnergyStreamData' nel riquadro **campi** .

    -   Fare clic su **"Timestamp"** e assicurarsi che mostra in "Assi". Fare clic su **"Carica"** e assicurarsi che mostra in "I valori".

    -   Fare clic su **Salva** nella parte superiore e assegnare un nome report come "EnergyStreamDataReport". Verrà visualizzato il report denominato "EnergyStreamDataReport" nella sezione report nel riquadro di spostamento a sinistra.

    -   Fare clic su **"Pin visivo"** ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) icona angolo superiore destro di questo grafico a linee, una finestra "Pin per Dashboard" vengano visualizzate per è possibile scegliere un dashboard. Selezionare "EnergyStreamDataReport", quindi fare clic su "Aggiungi".

    -   Posizionare il puntatore del mouse sul riquadro nel dashboard, fare clic su "Modifica" icona angolo superiore destro per modificare il titolo di "Richiesta da Timestamp"

4.  Creare gli altri riquadri del dashboard in base a set di dati appropriato. Di seguito è riportata la visualizzazione di dashboard finale.
        ![](media\cortana-analytics-technical-guide-demand-forecast\PBIFullScreen.png)


### <a name="setup-cold-path-dashboard"></a>Imposta percorso fredda Dashboard
Nella pipeline di dati percorso fredda, l'obiettivo fondamentale è ottenere la previsione della domanda di ogni area. Power BI si connette a un database SQL Azure come origine dati, in cui sono memorizzati i risultati di previsione.

> [AZURE.NOTE] 1) bastano poche ore di raccogliere i risultati sufficientemente previsioni per il dashboard. Si consiglia di iniziare questa procedura 2-3 ore successive si pranzo il generatore di dati. 2) in questo passaggio il prerequisito è di scaricare e installare il software gratuito [Power BI desktop](https://powerbi.microsoft.com/desktop).



1.  È possibile ottenere le credenziali del database.

    Sarà necessario **nome del server di database, nome del database, nome utente e password** prima di passare alla procedura successiva. Ecco i passaggi da Guida come trovarli più facilmente.

    -   Una volta **"Database di SQL Azure"** nel diagramma modello soluzione diventa verde, fare clic su esso e quindi fare clic su **"Apri"**. Saranno illustrati al portale di gestione Azure e verrà aperta la pagina di informazioni database anche.

    -   Nella pagina, è possibile trovare una sezione "Database". Vengono elencati i database che è stato creato. Il nome del database deve essere **"Il nome della soluzione numero casuale +"db""** (ad esempio "mytest12345db").

    -   Fare clic su database, nella nuova popout pannel, è possibile trovare il nome del server database nella parte superiore. La disponibilità database server per nome nome deve essere **"Il nome della soluzione numero casuale + 'database.windows.net,1433'"** (ad esempio "mytest12345.database.windows.net,1433").

    -   Il database, **nome utente** e la **password** è lo stesso nome utente e password registrata in precedenza durante la distribuzione della soluzione.

2.  Aggiornare l'origine dati del file di Power BI percorso fredda
    -  Assicurarsi che è stata installata la versione più recente di [Power BI desktop](https://powerbi.microsoft.com/desktop).

    -   Nella cartella **"DemandForecastingDataGeneratorv1.0"** è stato scaricato, fare doppio clic sul file **' Power BI Template\DemandForecastPowerBI.pbix'.** Le visualizzazioni iniziale basate sui dati fittizi. **Nota:** Se viene visualizzato un messaggio di errore convertono, assicurarsi che è stata installata la versione più recente di Power BI Desktop.

        Dopo aver aperto, nella parte superiore del file, scegliere **' Modifica query '**. Nella finestra popout, fare doppio clic **'Source'** nel Pannello di destra.
    ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic1.png)

    -   Nella finestra popout sostituire **"Server"** e **"Database"** con il proprio server e nomi di database e quindi fare clic su **"OK"**. Nome del server, assicurarsi di specificare la porta 1433 (**YourSolutionName.database.windows.net, 1433**). Ignorare i messaggi di avviso che vengono visualizzati sullo schermo.

    -   Nella finestra, successiva popout, verranno visualizzate due opzioni nel riquadro a sinistra (**Windows** e **Database**). Fare clic su **"Database"**, inserire nome **"Utente"** e **"Password"** (è il nome utente e la password che è stata immessa quando si prima distribuita la soluzione e creata un database SQL Azure). In ***Selezionare il livello per applicare queste impostazioni***, selezionare l'opzione livello di database. Fare clic su **"Connetti"**.

    -   Una volta che sta interattiva la pagina precedente, chiudere la finestra. Verrà visualizzato un messaggio out, fare clic su **Applica**. Infine, fare clic sul pulsante **Salva** per salvare le modifiche. Il file di Power BI è ora stabilita connessione al server. Se le visualizzazioni sono vuote, assicurarsi di che deselezionare le opzioni desiderate nelle visualizzazioni per visualizzare tutti i dati facendo clic sull'icona per la gomma nell'angolo superiore destro delle legende. Utilizzare il pulsante Aggiorna in modo da rispecchiare i nuovi dati in visualizzazioni. Inizialmente, solo vedrete dati iniziali sulle visualizzazioni come factory dati è programmato per aggiornare ogni tre ore. Dopo 3 ore, si vedrà stime di nuovi applicate visualizzazioni quando si aggiornano i dati.

3. (Facoltativo) Pubblicare il dashboard percorso fredda in [Power BI online](http://www.powerbi.com/). Si noti che questo passaggio è necessario un account di Power BI (o account Office 365).

    -   Fare clic su **"Pubblica"** e alcuni secondi dopo una verrà visualizzata la finestra "Pubblicazione in Power BI successo!" con un segno di spunta verde. Fare clic sul collegamento sotto "Apri demoprediction.pbix ripiegato". Per informazioni dettagliate, vedere [pubblicazione da Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

    -   Per creare un nuovo dashboard: fare clic sul **+** segno più accanto alla sezione **Dashboard** nel riquadro sinistro. Immettere il nome "Richiesta della previsione della Demo" per il nuovo dashboard.

    -   Dopo aver aperto il report, fare clic su ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) consente di aggiungere tutte le visualizzazioni al dashboard. Per trovare istruzioni dettagliate, vedere [aggiungere un riquadro a un dashboard di Power BI da un report](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
        Passare alla pagina del dashboard e regolare le dimensioni e posizione delle visualizzazioni e modificare i titoli. Per trovare istruzioni dettagliate su come modificare i riquadri, vedere [Modifica un riquadro - Ridimensiona, sposta, Rinomina, pin, eliminare, aggiungere collegamento ipertestuale](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Ecco un dashboard di esempio con alcune visualizzazioni di percorso fredda aggiunte a tale.

        ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic7.png)

4. (Facoltativo) Pianificare l'aggiornamento dell'origine dati.
    -     Per pianificare l'aggiornamento dei dati, passare il mouse sopra il set di dati **EnergyBPI finale** , fare clic su ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic3.png) e quindi scegliere **Pianifica aggiornamento**.
    **Nota:** Se vengono visualizzati messaggi di avviso, fare clic su **Modifica credenziali** e assicurarsi che le credenziali di database sono equivalenti a quelli descritti nel passaggio 1.

    ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic4.png)

    -   Espandere la sezione **Pianifica aggiornamento** . Attivare "mantenere aggiornati i dati".

    -   Pianificare l'aggiornamento in base alle proprie esigenze. Per ulteriori informazioni, vedere [aggiornamento dei dati in Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).


## <a name="how-to-delete-your-solution"></a>**Come eliminare la soluzione**
Assicurarsi che il generatore di dati si arresta quando si usa non attivamente la soluzione come eseguire il generatore di dati comporta costi più elevati. Eliminare la soluzione se non si sta utilizzando. Eliminare la soluzione si elimineranno tutti i componenti effettuato il provisioning in abbonamento quando la distribuzione della soluzione. Per eliminare la soluzione fare clic sul nome della soluzione nel riquadro sinistro del modello di soluzione e fare clic su Elimina.

## <a name="cost-estimation-tools"></a>**Strumenti stima dei costi**

I due strumenti seguenti sono disponibili per comprendere meglio i costi totali previsti dall'esecuzione della previsione della richiesta per il modello di soluzione di energia nell'abbonamento:

-   [Strumento stima di Microsoft Azure costo (online)](https://azure.microsoft.com/pricing/calculator/)

-   [Microsoft Azure costo stima strumento (desktop)](http://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**Riconoscimenti**
In questo articolo è stato creato da scienziato dati Yijing Chen e tecnico Qiu Min in Microsoft.
