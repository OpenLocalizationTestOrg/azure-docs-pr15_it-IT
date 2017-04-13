<properties
    pageTitle="Panoramica delle metriche di Microsoft Azure | Microsoft Azure"
    description="Panoramica delle metriche e il relativo utilizzo in Microsoft Azure"
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="ashwink"/>

# <a name="overview-of-metrics-in-microsoft-azure"></a>Panoramica delle metriche di Microsoft Azure 

In questo articolo vengono descritte le metriche in Microsoft Azure, i vantaggi e come loro utilizzo.  

## <a name="what-are-metrics"></a>Quali sono le metriche?

Monitor Azure consente di utilizzare telemetria per migliorare la visibilità sulle prestazioni e l'integrità dei carichi di lavoro su Azure. Il tipo di dati di telemetria Azure più importante sono metriche (denominate anche contatori) emesse dalle risorse più Azure. Monitor Azure offre diversi modi per configurare e utilizzare queste metriche per il monitoraggio e risoluzione dei problemi.


## <a name="what-can-you-do-with-metrics"></a>Operazioni eseguibili con metriche?

Metrica è una fonte efficace di telemetria e consentono di eseguire le operazioni seguenti:

- **Traccia le prestazioni** della risorsa (ad esempio una macchina virtuale, un sito Web o logica App), tracciato dei valori relative metriche in un grafico del portale e che il grafico a un dashboard di blocco.
- **Ricevere una notifica di un problema** impatto sulle prestazioni della risorsa quando una metrica supera una determinata soglia.
- **Configurazione automatica azioni**, ad esempio il ridimensionamento automatico una risorsa o generando un runbook quando una metrica supera una determinata soglia.
- **Esegui avanzate analitica** o creazione di report sulle prestazioni o uso tendenze delle risorse.
- **Archiviazione** della cronologia prestazioni o integrità delle risorse **per il controllo della** conformità/esigenze.

##  <a name="metric-characteristics"></a>Caratteristiche metriche
Metrica presentano le caratteristiche seguenti:

- Tutte le metriche hanno **1 minuti**. Si riceve un valore metrico minuto dalla risorsa è dato vicino la visibilità in tempo reale sullo stato e dell'integrità della risorsa.
- Sono **disponibili componenti di-pronte senza che sia necessario acconsentire esplicitamente** o la configurazione di diagnostica aggiuntiva.
- È possibile accedere **30 giorni di cronologia** per ogni unità di misura metriche. È possibile visualizzare rapidamente le tendenze recenti e mensile le prestazioni dell'integrità della risorsa.

Si può:

- Individuare facilmente l'accesso e **visualizzare tutte le metriche** tramite il portale Azure quando si seleziona una risorsa e li tracciare in un grafico. 
- Configurare una **regola di avviso che invia una notifica o interviene automatizzato** metriche quando l'unità di misura metriche supera la soglia che è stata impostata. AutoScale è un'azione automatica speciale che consente di scalare la risorsa per soddisfare le richieste in arrivo o caricare nel sito web o calcolare le risorse. È possibile configurare una regola di impostazione Autoscale le proporzioni esterna / in base a un'unità di misura metriche che attraversano una determinata soglia.
- Metriche di **archiviazione** per più o utilizzarli per i report offline. È possibile distribuire le metriche nell'archiviazione blob quando si configurano impostazioni di diagnostiche per la risorsa.
- Metriche di **flusso** a un Hub di evento, che consente di quindi inviare loro Azure flusso Analitica o App personalizzate per l'analisi di tempo quasi reale. È possibile eseguire l'utilizzando le impostazioni di diagnostiche.
- **Instradare** tutti metriche a OMS Log Analitica sbloccare immediata analitica, ricerca e avvisi personalizzati sui dati metriche dalle risorse.
- **Consumo** metriche tramite API REST di nuovo Azure Monitor.
- Metriche di **query** utilizzando i PowerShell Cmdlets o API REST multipiattaforma.

 ![Distribuzione di metriche di Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview0.png)

## <a name="access-metrics-via-portal"></a>Metriche di accesso tramite portale
Ecco una rapida procedura dettagliata di creazione di un grafico metrico tramite il portale di Azure

### <a name="view-metrics-after-creating-a-resource"></a>Visualizzazione metriche dopo la creazione di una risorsa
1. Apri portale Azure
2. Creare un nuovo servizio App - sito Web.
3. Dopo aver creato un sito Web, passare a e il Panoramica del sito web.
4. È possibile visualizzare nuove metriche come riquadro 'Monitoraggio'. È possibile modificare il riquadro e selezionare ulteriori metriche

 ![Metrica a una risorsa in Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview1.png)    

### <a name="access-all-metrics-in-a-single-place"></a>Accedere a tutte le metriche in un'unica posizione
1. Aprire il portale di Azure 
2. Passare alla scheda Monitor nuovo e selezionare l'opzione metriche sotto di esso 
3. È possibile selezionare l'abbonamento, gruppo di risorse e il nome della risorsa dall'elenco a discesa. 
4. È ora possibile visualizzare l'elenco di statistiche disponibili. 
5. Selezionare l'unità di misura metriche sono interessati e tracciare in. 
6. È possibile aggiungere al dashboard facendo clic su pin nell'angolo superiore destro.

 ![Accedere a tutte le metriche in un'unica posizione nel Monitor Azure](./media/monitoring-overview-metrics/MetricsOverview2.png) 


>[AZURE.NOTE] È possibile accedere a livello di host metriche di macchine virtuali (Manager delle risorse di Azure in base a) e dei set di scala macchine Virtuali senza le impostazioni di diagnostica aggiuntive. Queste nuove metriche di livello host sono disponibili per le istanze di Windows e Linux. Queste metriche non devono essere confuso con le metriche di livello del sistema operativo Guest che è possibile accedere quando si attiva la diagnostica Azure su macchine virtuali o VMSS. Per ulteriori informazioni sulla configurazione di diagnostica di Azure, vedere [Novità](../azure-diagnostics.md)di Microsoft Azure diagnostica.

## <a name="access-metrics-via-rest-api"></a>Metriche di accesso tramite API REST
Metriche di Azure sono accessibili tramite API Monitor Azure. Esistono due API che consentono di individuare e accedere a metriche. Utilizzo di: 

- [Definizioni di unità di misura metriche Monitor azure API REST](https://msdn.microsoft.com/library/mt743621.aspx) per accedere all'elenco dei parametri disponibili per un servizio.
- [API REST di azure Monitor metriche](https://msdn.microsoft.com/library/mt743622.aspx) per accedere ai dati metriche effettivo

>[AZURE.NOTE] In questo articolo è descritta la metrica tramite la [nuova API per metriche](https://msdn.microsoft.com/library/dn931930.aspx) per le risorse Azure. La versione dell'API per le nuove definizioni di metriche API è 2016-03-01 e la versione per metriche API è 2016-09-01. Le definizioni di metriche legacy e metriche accessibile con la versione dell'api 2014-04-01.

Per una descrizione più dettagliata utilizzando le API REST di Azure Monitor, vedere [Procedura dettagliata API REST di Azure Monitor](monitoring-rest-api-walkthrough.md).

## <a name="export-options-for-metrics"></a>Opzioni per le metriche di esportazione
È possibile passare a e il log di diagnostica nella scheda Monitor e visualizzare le opzioni di esportazione per metriche. È possibile selezionare metriche (e registri diagnostici) a archiviazione Blob, evento hub o a OMS Log Analitica per casi di utilizzo indicati in precedenza in questo articolo. 

 ![Opzioni di esportazione per metriche di Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview3.png)   

È possibile configurare tramite modelli di Manager delle risorse, [PowerShell](insights-powershell-samples.md), [CLI](insights-cli-samples.md) o [API REST](https://msdn.microsoft.com/library/dn931943.aspx). 

## <a name="take-action-on-metrics"></a>Intervenire sui dati statistici relativi
È possibile ricevere notifiche o intraprendere azioni automatizzate metriche dati. È possibile configurare le regole di avviso o impostazioni Autoscale per farlo.

### <a name="alert-rules"></a>Regole di avviso
È possibile configurare le regole di avviso sui dati statistici relativi. Queste regole di avviso è possono verificare se una metrica sono incrociate una determinata soglia e invio di una notifica tramite posta elettronica o generare un webhook utilizzabili per eseguire qualsiasi script personalizzato. Utilizzare la webhook per configurare le integrazioni prodotto 3 °.

 ![Metriche e le regole di avviso nel Monitor Azure](./media/monitoring-overview-metrics/MetricsOverview4.png)

### <a name="autoscale"></a>AutoScale
Alcune risorse Azure supportano più istanze per ridimensionare o l'estrazione di gestire i carichi di lavoro. AutoScale si applica a servizi App (applicazioni Web), macchina virtuale scala set (VMSS) e servizi Cloud classica. È possibile configurare le regole di autoscale le dimensioni o quando un determinato unità di misura metriche impatto il carico di lavoro supera una soglia specificata. Per ulteriori informazioni, vedere [Panoramica di adattamento automatico](monitoring-overview-autoscale.md).

 ![Metrica e autoscale nel Monitor Azure](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="supported-services-and-metrics"></a>Servizi supportati e metriche
Monitor Azure è una nuova infrastruttura metriche. Supporto per i servizi di Azure seguenti nel portale di Azure e la nuova versione dell'API Monitor Azure:

- Macchine virtuali (Manager delle risorse di Azure in base a)
- Set di scala macchine Virtuali
- Batch
- Spazio dei nomi Hub di evento 
- Spazio dei nomi Bus di servizio (solo SKU premium)
- SQL (versione 12)
- Pool SQL flessibile
- Siti Web
- Web Server farm
- App logica
- Hub IoT
- Redis Cache
- Rete: Gateway per applicazioni
- Ricerca

È possibile visualizzare un un elenco dettagliato di tutti i servizi supportati e le metriche al [Monitor di Azure metriche - metriche supportate per ogni tipo di risorsa](monitoring-supported-metrics.md). 


## <a name="next-steps"></a>Passaggi successivi

Fare clic sui collegamenti in questo articolo. Inoltre, informazioni su:  

- su [metriche comuni per il ridimensionamento automatico](insights-autoscale-common-metrics.md)
- come [creare regole di avviso](insights-alerts-portal.md)




