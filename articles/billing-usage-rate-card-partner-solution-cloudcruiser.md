<properties
   pageTitle="Cruiser e fatturazione API integrazione di Microsoft Azure cloud | Microsoft Azure"
   description="Fornisce una prospettiva univoca da Microsoft Azure fatturazione partner Cruiser Cloud, nel loro esperienze integrazione le API di fatturazione di Azure il prodotto.  Questa operazione è particolarmente utile per i clienti di Azure e Cloud Cruiser sono interessati a utilizzando/provare Cruiser Cloud di Microsoft Azure Pack."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"
   />

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="09/08/2016"
   ms.author="mobandyo;sirishap;bryanla"/>

# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Cloud Cruiser e fatturazione API integrazione di Microsoft Azure

Questo articolo descrive come le informazioni acquisite dal nuovo Microsoft Azure fatturazione API utilizzabile in Cruiser Cloud per simulazione costo del flusso di lavoro e l'analisi.

## <a name="azure-ratecard-api"></a>API RateCard Azure
L'API RateCard fornisce informazioni sul tasso comuni. Dopo l'autenticazione con le credenziali appropriate, è possibile richiedere l'API per raccogliere metadati sui servizi disponibili su Azure, insieme a tassi associati con l'ID offerta.

Di seguito è una risposta di esempio da API che mostra i prezzi per il A0 istanza (Windows):

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0,
        "MeterStatus": "Active"
    },

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>Cloud interfaccia del Cruiser di Azure RateCard API
Cloud Cruiser possibile sfruttare le informazioni dell'API RateCard in modi diversi. In questo articolo verrà illustrato come può essere utilizzato per rendere IaaS carico di lavoro simulazione e analisi dei costi.

Per dimostrare in questo caso di utilizzo, immaginare un carico di lavoro di più istanze in esecuzione in Microsoft Azure Pack (WAP). L'obiettivo è simulare questo stesso carico di lavoro in Azure e stimare i costi di eseguendo tali migrazione. Per creare la simulazione, sono disponibili due attività principali da eseguire:

1. **Importare ed elaborare le informazioni sul servizio raccolti dall'API RateCard.** Questa operazione viene eseguita anche nelle cartelle di lavoro, in cui l'estratto l'API RateCard viene trasformato e pubblicato in un nuovo piano di frequenza. Il nuovo piano tariffe da utilizzare sul simulazioni per stimare i prezzi Azure.

2. **Normalizzare WAP e Azure services per IaaS.** Per impostazione predefinita, i servizi WAP sono basati sulle singole risorse (CPU, la dimensione della memoria, dimensioni del disco, e così via) mentre si Azure servizi basati sulle dimensioni istanza (A0, A1, A2 e così via). Questa prima attività possono essere eseguiti dal motore ETL del Cloud Cruiser, denominata cartelle di lavoro, in cui queste risorse possono essere inserite nelle dimensioni istanza, analoghe a servizi istanza Azure.

### <a name="import-data-from-the-ratecard-api"></a>Importare dati da API RateCard

Le cartelle di lavoro di cloud Cruiser rappresentano un modo automatico per raccogliere ed elaborare le informazioni provenienti da API RateCard.  Le cartelle di lavoro ETL (caricamento trasformazione estratto) consentono di configurare la raccolta, la trasformazione e la pubblicazione dei dati nel database Cruiser Cloud.

Ogni cartella di lavoro può contenere uno o più raccolte, consentendo di creare una relazione tra le informazioni provenienti da origini diverse per integrare o per migliorare i dati di utilizzo. Due schermate seguenti viene illustrato come creare una nuova *raccolta* in una cartella di lavoro esistente e importare le informazioni nella *raccolta* dall'API RateCard:

![Figura 1 - Creazione di una nuova raccolta][1]

![Figura 2: importare dati dalla nuova raccolta][2]

Dopo aver importato i dati nella cartella di lavoro, è possibile creare più passaggi e processi di trasformazione, modificare e modellare i dati. In questo esempio, dal momento che si desidera solo infrastruttura-come-a-Service (IaaS) è possibile utilizzare la procedura di trasformazione per rimuovere le righe non necessarie o dei record, relative ai servizi diversa da quella IaaS.

La schermata seguente illustra la procedura di trasformazione utilizzata per elaborare i dati raccolti dal RateCard API:

![Figura 3 - operazioni di trasformazione per l'elaborazione di dati raccolti dall'API RateCard][3]

### <a name="defining-new-services-and-rate-plans"></a>Definizione di frequenza e nuovi servizi plan di messaggistica unificata

Esistono diversi modi per definire i servizi nel Cloud Cruiser. Una delle opzioni è per importare i dati di utilizzo dei servizi. Questo metodo viene generalmente utilizzato quando si lavora con cloud pubblici, in cui i servizi sono già definiti dal provider.

Un piano di velocità è un insieme di tariffe o i prezzi che possono essere applicati a diversi servizi, in base alle date di validità o gruppo di clienti, tra le altre opzioni. Piani tasso è utilizzabile anche nel Cloud Cruiser per creare la simulazione o scenari "Simulazione", per comprendere come modifiche apportate ai servizi possono influire sul costo totale di un carico di lavoro.

In questo esempio, le informazioni sul servizio da API RateCard verrà usata per definire nuovi servizi nel Cloud Cruiser. Nello stesso modo, è possibile utilizzare le tariffe associate ai servizi per creare un nuovo piano di velocità su Cloud Cruiser.

Al termine del processo di trasformazione, è possibile creare una nuova azione e pubblicare i dati da API RateCard come tariffe e nuovi servizi.

![Figura 4 - pubblicare i dati da API RateCard come nuove tariffe e servizi][4]

### <a name="verify-azure-services-and-rates"></a>Verificare le tariffe e i servizi di Azure

Dopo la pubblicazione dei servizi e tariffe, è possibile verificare l'elenco dei servizi importati nella scheda *servizi* di Cloud Cruiser:

![Figura 5 - verificare i nuovi servizi][5]

Nella scheda *Tasso plan di messaggistica unificata* , è possibile controllare il nuovo piano tasso denominato "AzureSimulation" con le tariffe importati da API RateCard.

![Figura 6 - verifica del nuovo tasso di pianificare e tariffe associate][6]

### <a name="normalize-wap-and-azure-services"></a>Normalizzare WAP e i servizi di Azure

Per impostazione predefinita, WAP fornisce informazioni sull'utilizzo in base all'utilizzo di elaborazione, memoria e risorse di rete. In Cruiser Cloud, è possibile definire i servizi basati su direttamente sul allocazione o a consumo l'uso di queste risorse. Ad esempio, impostare un tasso di base per ogni ora di utilizzo della CPU oppure applicare costi GB di memoria per un'istanza.

In questo esempio, per confrontare i costi tra WAP e Azure, è necessario aggregare l'utilizzo delle risorse in WAP in bundle, che è possibile mappare i servizi di Azure. È possibile implementare facilmente questa trasformazione nelle cartelle di lavoro:

![Figura 7 - trasformare dati WAP per normalizzare i servizi][7]

L'ultimo passaggio nella cartella di lavoro è di pubblicare i dati nel database Cruiser Cloud. Durante questo passaggio, i dati di utilizzo sono ora inclusi nei servizi (eseguire il mapping a servizi di Windows Azure) e collegati ai tassi predefinito per creare le spese.

Dopo aver completato la cartella di lavoro, è possibile automatizzare l'elaborazione dei dati, aggiungendo un'attività nell'utilità di pianificazione e specificare la frequenza e l'ora per la cartella di lavoro per l'esecuzione.

![Figura 8: cartella di lavoro Pianificazione][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Creare report per l'analisi di simulazione costi carico di lavoro

Dopo l'utilizzo che verrà raccolte e in base alle tariffe caricati nel database Cruiser Cloud, è possibile sfruttare il modulo Cloud Cruiser approfondimenti per creare il carico di lavoro costi simulazione che si desidera.

Per dimostrare questo scenario, viene creato il report seguente:

![Confronto di costi][9]

Il grafico superiore mostra un confronto di costi per i servizi di confronto tra il prezzo di eseguire il carico di lavoro per ogni servizio specifica tra WAP (blu scuro) e Azure (azzurro).

Il grafico inferiore mostra gli stessi dati, ma suddivisi per reparto. Mostra i costi per ogni reparto eseguire il carico di lavoro su WAP e Azure, oltre la differenza tra di esse nella barra risparmi (verde).

## <a name="azure-usage-api"></a>L'uso di Azure API


### <a name="introduction"></a>Introduzione

Microsoft ha recentemente introdotto l'API l'uso di Azure, per consentire agli abbonati in modo da estrarre a livello di programmazione dati sull'utilizzo di ottenere informazioni approfondite in consumo. Si tratta novità per i clienti di Cloud Cruiser che è possibile sfruttare il set di dati più complete disponibili tramite l'API.

Cloud Cruiser possibile sfruttare l'integrazione con l'API di utilizzo in diversi modi. Granularità (ogni ora informazioni sull'utilizzo) e informazioni sui metadati delle risorse disponibili tramite l'API fornisce il set di dati necessari per supportare flessibile Showback o rifiuto di modelli. 

In questa esercitazione verranno presentati un esempio di come Cruiser Cloud possono trarre vantaggio dalle informazioni sull'utilizzo dell'API. In particolare, si verrà creato un gruppo di risorse in Azure, associare tag per la struttura di account e quindi descrivere il processo di estrazione e l'elaborazione delle informazioni tag nel Cloud Cruiser.
 
L'obiettivo finale è la possibilità di creare report come quella riportata di seguito e sarà possibile analizzare i costi e consumo sulla base della struttura di account popolata dai tag.

![Figura 10 - Report con suddivisioni utilizzando i tag][10]

### <a name="microsoft-azure-tags"></a>Tag di Microsoft Azure

I dati disponibili tramite l'API di utilizzo di Azure includono non solo informazioni sul consumo, ma anche dei metadati delle risorse inclusi eventuali tag è associato. Categorie rappresentano un modo semplice per organizzare le risorse, ma per essere efficace, è necessario assicurarsi che:

- Tag vengono applicati correttamente alle risorse in fase di provisioning
- Tag vengono utilizzati correttamente il processo di Showback/rifiuto per collegare dell'utilizzo della struttura di account dell'organizzazione.

Entrambe le condizioni può essere difficile, soprattutto se esiste un processo manuale di provisioning o Ricarica lato. Tag digitati, errato o mancante anche risultano comuni reclami dei clienti utilizzando tag e questi errori può semplificare sul lato Ricarica molto difficile.

Con la nuova API di utilizzo di Azure, Cloud Cruiser possibile estrarre informazioni tagging risorsa e tramite sofisticato strumento ETL denominato cartelle di lavoro, correggere gli errori di tagging comuni. Mediante la trasformazione utilizzando espressioni regolari e la correlazione dei dati, Cloud Cruiser possibile identificare le risorse in modo non corretto con tag e applicare contrassegni corretti garantire l'associazione corretta delle risorse al consumer.

Sul lato Ricarica Cloud Cruiser consente di automatizzare il processo di Showback/rifiuto e possibile sfruttare le informazioni sui tag per collegare l'uso in consumer appropriato (reparto, divisione, Project e così via). Questa automazione fornisce un enorme miglioramento ed essere in un processo Ricarica coerente e controllabile.
 

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Creazione di un gruppo di risorse con contrassegni nel Microsoft Azure
Il primo passaggio in questa esercitazione consiste nel creare un gruppo di risorse nel portale di Azure, creare nuovi tag per associare alle risorse. In questo esempio, che verrà creata seguenti tag: reparto, ambiente, proprietario, progetto.

Nella schermata seguente mostra un esempio gruppo di risorse con tag associati.

![Figura 11 - gruppo di risorse con tag associati nel portale Azure][11]

Il passaggio successivo consiste nel recuperare le informazioni da API di utilizzo in Cruiser Cloud. L'API di utilizzo attualmente fornisce le risposte nel formato JSON. Ecco un esempio dei dati recuperati:


    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Importare dati da API di utilizzo in Cruiser Cloud

Le cartelle di lavoro di cloud Cruiser rappresentano un modo automatico per raccogliere ed elaborare le informazioni provenienti da API di utilizzo. Una cartella di lavoro (caricamento trasformazione estratto) ETL consente di configurare la raccolta, la trasformazione e la pubblicazione dei dati nel database Cruiser Cloud.

Ogni cartella di lavoro può contenere uno o più raccolte. In questo modo è possibile correlare le informazioni provenienti da origini diverse per integrare o per migliorare i dati di utilizzo. In questo esempio si creerà un nuovo foglio nella cartella di lavoro dei modelli di Azure (_UsageAPI)_ e impostare una nuova _raccolta_ per importare informazioni da API di utilizzo.

![Figura 3 - dati sull'utilizzo dell'API importati foglio UsageAPI][12]

Si noti che la cartella di lavoro contiene già altri fogli di importare servizi da Azure (_ImportServices_) ed elaborare le informazioni di consumo dall'API di fatturazione (_PublishData_).

È quindi verranno utilizzare l'API di utilizzo per popolare il foglio _UsageAPI_ e correlare le informazioni con i dati consumo API fatturazione nel foglio _PublishData_ .

### <a name="processing-the-tag-information-from-the-usage-api"></a>Elaborazione delle informazioni tag dall'API di utilizzo

Dopo aver importato i dati nella cartella di lavoro, si creerà operazioni di trasformazione nel foglio di _UsageAPI_ per elaborare le informazioni delle API. Primo passaggio consiste nell'utilizzare un processore "JSON dividere" per estrarre i tag da un singolo campo e quindi creare campi per ognuna di esse (reparto, Project, proprietario e ambiente).

![Figura 4 - creare nuovi campi per le informazioni di tag][13]

Si noti il servizio "Rete" mancano le informazioni di tag (casella gialla), ma è possibile verificare che sia parte del gruppo di risorse stesso esaminando il campo _ResourceGroupName_ . Poiché è stata creata i contrassegni per le altre risorse da questo gruppo di risorse, è possibile utilizzare queste informazioni per applicare i tag mancanti a questa risorsa in un punto successivo del processo.

Il passaggio successivo consiste nel creare una tabella di ricerca associazione le informazioni dai contrassegni a _ResourceGroupName_. Questa tabella di ricerca da utilizzare nel passaggio successivo per migliorare i dati di consumo con informazioni relative ai tag.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>Aggiungere le informazioni di tag ai dati consumo

A questo punto è possibile passare al foglio _PublishData_ , che elabora le informazioni di consumo dall'API di fatturazione e aggiungere i campi estratti dai tag. Questo processo viene eseguito esaminando la tabella di ricerca creata nel passaggio precedente, utilizzando _ResourceGroupName_ come chiave per le ricerche.

![Figura 5 - compilare la struttura dei conti con le informazioni disponibili le opzioni di ricerca][14]

Si noti che sono stati applicati i campi di struttura account appropriato per il servizio "Rete", la risoluzione del problema con i tag mancanti. Campi della struttura di account per le risorse è stata inoltre riempita diversa da quella il nostro obiettivo gruppo di risorse con "Altro" per distinguerli nei report.

A questo punto è sufficiente aggiungere un passaggio per pubblicare i dati di utilizzo. Durante questo passaggio, le tariffe appropriate per ogni servizio definiti sul nostro tasso pianificare verranno applicate a informazioni sull'utilizzo con le spese risultante caricata nel database.

La parte migliore è che è sufficiente eseguire questa procedura una sola volta. Al termine della cartella di lavoro, è sufficiente aggiungere per l'utilità di pianificazione e viene eseguito oraria o giornaliera all'ora pianificata. È sufficiente di creazione di nuovi report o personalizzazione di quelli esistenti, per analizzare i dati per ottenere informazioni approfondite significativi dall'utilizzo di cloud.

### <a name="next-steps"></a>Passaggi successivi

+ Per istruzioni dettagliate sulla creazione di report e le cartelle di lavoro Cruiser Cloud, fare riferimento alla online [documentazione](http://docs.cloudcruiser.com/) (valido accesso obbligatorio del Cloud Cruiser).  Per ulteriori informazioni su Cloud Cruiser, contattare [info@cloudcruiser.com](mailto:info@cloudcruiser.com).
+ Per una panoramica delle RateCard APIs e l'utilizzo delle risorse di Azure, vedere [ottenere informazioni approfondite nel consumo di risorse di Microsoft Azure](billing-usage-rate-card-overview.md) .
+ Controllare i [Riferimenti alle API REST di fatturazione Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) per ulteriori informazioni su entrambe le API che fanno parte del set di API fornite da Gestione risorse di Azure.
+ Se si preferisce approfondire direttamente il codice di esempio, vedere esempi di codice il nostro Microsoft Azure fatturazione API su [Azure esempi di codice](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>Ulteriori informazioni
+ Vedere l'articolo [Panoramica di gestione risorse Azure](azure-resource-manager/resource-group-overview.md) per altre informazioni su Gestione risorse di Azure.

<!--Image references-->
 
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Figura 1 - Creazione di una nuova raccolta"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Figura 2: importare dati dalla nuova raccolta"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Figura 3 - operazioni di trasformazione per l'elaborazione di dati raccolti dall'API RateCard"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Figura 4 - pubblicare i dati da API RateCard come nuove tariffe e servizi"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Figura 5 - verificare i nuovi servizi"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Figura 6 - verifica del nuovo tasso di pianificare e tariffe associate"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Figura 7 - trasformare dati WAP per normalizzare i servizi"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figura 8: cartella di lavoro Pianificazione"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Figura 9 - Report di esempio per lo scenario di confronto dei costi di carico di lavoro"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Figura 10 - Report con suddivisioni utilizzando i tag"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Figura 11 - gruppo di risorse con tag associati nel portale Azure"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Figura 12 - dati sull'utilizzo dell'API importati foglio UsageAPI"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Figura 13 - creare nuovi campi per le informazioni di tag"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Figura 14 - compilare la struttura dei conti con le informazioni disponibili le opzioni di ricerca"
