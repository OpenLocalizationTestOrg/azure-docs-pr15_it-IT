<properties
   pageTitle="Soluzioni in operazioni Management Suite (OMS) | Microsoft Azure"
   description="Soluzioni estendono la funzionalità di operazioni di gestione famiglia di prodotti (OMS), fornendo scenari di gestione dei pacchetti che i clienti possono aggiungere alla propria area di lavoro OMS.  Questo articolo fornisce informazioni dettagliate su come personalizzate soluzioni create da clienti e partner."
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
   ms.date="10/17/2016"
   ms.author="bwren" />

# <a name="management-solutions-in-operations-management-suite-oms-preview"></a>Soluzioni di gestione di operazioni di gestione famiglia di prodotti (OMS) (versione di anteprima)

>[AZURE.NOTE]Si tratta documentazione preliminare per soluzioni di gestione in OMS che sono attualmente in anteprima.    

Soluzioni di gestione estendono la funzionalità di operazioni di gestione famiglia di prodotti (OMS), fornendo scenari di gestione dei pacchetti che i clienti possono aggiungere al proprio ambiente.  Oltre a [soluzioni fornito da Microsoft](../log-analytics/log-analytics-add-solutions.md), partner e clienti possono creare soluzioni di gestione da utilizzare nel proprio ambiente o resi disponibili per i clienti tramite alla community.

## <a name="finding-and-installing-management-solutions"></a>Ricerca e l'installazione di soluzioni di gestione
Sono disponibili diversi metodi per individuare e installare soluzioni di gestione come descritto nelle sezioni seguenti.

### <a name="azure-marketplace"></a>Azure Marketplace
Soluzioni di gestione fornito da Microsoft e partner attendibili possono essere installati da Azure Marketplace nel portale di Azure.

1. Accedere al portale di Azure.
2. Nel riquadro sinistro selezionare **altri servizi**.
3. È possibile scorrere fino a **soluzioni** oppure digitare *soluzioni* nella finestra di dialogo **filtro** .
4. Fare clic sul pulsante **Aggiungi +** .
5. Ricerca di soluzioni che si sono interessati a esplorazione, fare clic sul pulsante **filtro** o digitare nella casella di **Ricerca tutto** .
6. Fare clic su un elemento marketplace per visualizzare le relative informazioni dettagliate.
4. Fare clic su **Crea** per aprire il riquadro **Aggiungi soluzione** .
5. Verrà richiesto di informazioni necessarie, ad esempio l' [account di automazione e area di lavoro OMS](#oms-workspace-and-automation-account) oltre a valori per i parametri della soluzione.
6. Fare clic su **Crea** per installare la soluzione.

### <a name="oms-portal"></a>Portale OMS
Soluzioni di gestione fornite da Microsoft potrebbero essere installate dalla raccolta soluzioni nel portale di OMS.

1. Accedere al portale di OMS.
2. Fare clic sul riquadro **Raccolta soluzioni** .
2. Nella pagina Raccolta soluzioni OMS Scopri ogni soluzione disponibili. Fare clic sul nome della soluzione che si desidera aggiungere alla OMS.
3. Nella pagina per la soluzione che si è scelto, verranno visualizzate informazioni dettagliate sulla soluzione. Fare clic su **Aggiungi**.
4. Nuovo riquadro per la soluzione che è stato aggiunto viene visualizzato nella panoramica sulla pagina in OMS ed è possibile iniziare a usarlo dopo il servizio Outlook Mobile elabora i dati.

### <a name="azure-quickstart-templates"></a>Guida introduttiva di Azure modelli
I membri della community possono inviare le soluzioni di gestione ai modelli di Guida introduttiva di Azure.  È possibile scaricare questi modelli per l'installazione successiva o verificare la presenza di informazioni su come [creare soluzioni personalizzate](#creating-a-solution).

1. Seguire la procedura descritta nella sezione [account di automazione e area di lavoro OMS](#oms-workspace-and-automation-account) per collegare un account e l'area di lavoro.
2. Passare alla [Guida introduttiva di Azure modelli](https://azure.microsoft.com/documentation/templates/).  
3. Cercare una soluzione che si è interessati a.
4. Selezionare la soluzione dalla casella risultati per visualizzarne i dettagli.
5. Fare clic sul pulsante **Distribuisci in Azure** .
6. Verrà richiesto di specificare informazioni, ad esempio il gruppo di risorse e il percorso oltre a valori per i parametri della soluzione.
7. Fare clic su **Acquista** per installare la soluzione.

### <a name="deploy-azure-resource-manager-template"></a>Distribuire il modello di gestione risorse di Azure
Soluzioni ottenere dalla community di o che si [creeranno personalmente](#creating-a-solution) sono implementata come modello Manager delle risorse che è possibile usare uno dei metodi standard per [la distribuzione di un modello](../resource-group-template-deploy-portal.md).  Si noti che prima di installare la soluzione, è necessario creare e collegare [account di automazione e area di lavoro OMS](#oms-workspace-and-automation-account).

## <a name="oms-workspace-and-automation-account"></a>Area di lavoro OMS e account di automazione
La maggior parte delle soluzioni di gestione richiedono un' [area di lavoro OMS](../log-analytics/log-analytics-manage-access.md) per contenere le visualizzazioni e un [account di automazione](../automation/automation-security-overview.md#automation-account-overview) per contenere runbook e le relative risorse. L'area di lavoro e l'account deve soddisfare i requisiti seguenti.

- Una soluzione è possibile utilizzare solo un'area di lavoro OMS e un account di automazione.  
- L'area di lavoro OMS e l'account di automazione utilizzato da una soluzione devono essere collegati tra loro. Un'area di lavoro OMS solo può essere collegato a un account di automazione e un account di automazione solo può essere collegato a un'area di lavoro OMS.
- Per essere collegati, l'area di lavoro OMS e l'account di automazione deve essere nello stesso gruppo di risorse e opzioni internazionali.  L'eccezione è un'area di lavoro OMS nell'area degli Stati Uniti orientali ed e dell'account di automazione negli Stati Uniti orientali 2.

### <a name="creating-a-link-between-an-oms-workspace-and-automation-account"></a>Creazione di un collegamento tra un'area di lavoro OMS e account di automazione
Come si specifica l'area di lavoro OMS e account automazione dipende il metodo di installazione per la soluzione.

- Quando si installa una soluzione Microsoft tramite il portale OMS, viene installato nell'area di lavoro OMS corrente ed non è necessario alcun account di automazione.

- Quando si installa una soluzione attraverso Azure Marketplace, viene chiesto di un'area di lavoro OMS e account di automazione e verrà creato il collegamento tra di esse.  

- Per le soluzioni all'esterno di Azure Marketplace, è necessario collegare l'area di lavoro OMS e l'account di automazione prima di installare la soluzione.  È possibile eseguire questa operazione selezionando qualsiasi soluzione in Azure Marketplace, selezionare l'area di lavoro OMS e l'account di automazione.  Non è necessario installare effettivamente la soluzione perché il collegamento verrà creato come l'area di lavoro OMS e l'account di automazione siano selezionate.  Dopo aver creato il collegamento, è possibile utilizzare tale account di automazione e area di lavoro OMS per qualsiasi soluzione. 

### <a name="verifying-the-link-between-an-oms-workspace-and-automation-account"></a>Verificare il collegamento tra un'area di lavoro OMS e account di automazione
È possibile verificare il collegamento tra un'area di lavoro OMS e un account di automazione tramite la procedura seguente.

1. Selezionare l'account di automazione nel portale di Azure.
2. Scorrere verso il basso del riquadro **Impostazioni** .
3. Se c'è una sezione denominata **Risorse OMS** nel riquadro **Impostazioni** , l'account è collegato a un'area di lavoro OMS.
4. Selezionare l' **area di lavoro** per visualizzare i dettagli dell'area di lavoro OMS collegate a questo account di automazione.


## <a name="listing-management-solutions"></a>Elenco delle opzioni di interlinea
Utilizzare per la procedura seguente per visualizzare le soluzioni di gestione nelle aree di lavoro collegati al proprio abbonamento Azure.

1. Accedere al portale di Azure.
2. Nel riquadro sinistro selezionare **altri servizi**.
3. È possibile scorrere fino a **soluzioni** oppure digitare *soluzioni* nella finestra di dialogo **filtro** .
4. Verranno elencate soluzioni installate in tutte le aree di lavoro.

Si noti che è possibile visualizzare solo le soluzioni Microsoft installate nell'area di lavoro corrente tramite il portale OMS.

## <a name="removing-a-management-solution"></a>Rimozione di una soluzione di gestione
Quando si rimuove una soluzione di gestione, vengono rimossi anche tutte le risorse della soluzione.  

1. Individuare la soluzione nel portale di Azure con la procedura nella [voce soluzioni](#listing-solutions).
2. Selezionare la soluzione che si desidera rimuovere.
3. Fare clic sul pulsante **Elimina** .

## <a name="creating-a-management-solution"></a>Creazione di una soluzione di gestione
Indicazioni dettagliate sulla creazione di soluzioni di gestione sono disponibili [soluzioni di creazione di operazioni di gestione famiglia di prodotti (OMS)](operations-management-suite-solutions-creating.md). 


## <a name="next-steps"></a>Passaggi successivi

- Ricerca di [Modelli di Guida introduttiva di Azure](https://azure.microsoft.com/documentation/templates) per esempi dei diversi modelli di Manager delle risorse.
- Creare le proprie [soluzioni di gestione](operations-management-suite-solutions-creating.md).
 