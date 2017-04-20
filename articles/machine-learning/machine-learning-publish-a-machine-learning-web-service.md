<properties
    pageTitle="Distribuire un servizio web di apprendimento | Microsoft Azure"
    description="Come convertire un esperimento predittivo un esperimento formazione, preparazione alla distribuzione e quindi viene distribuito come un servizio web di apprendimento Azure."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="garye"/>

# <a name="deploy-an-azure-machine-learning-web-service"></a>Distribuire un servizio web l'apprendimento Azure

Apprendimento Azure consente di creare, testare e distribuire soluzioni analitiche predittive.

Da una punto-di-panoramica generale, questa operazione viene eseguita nei tre passaggi:

- **[Crea un esperimento training]** - Azure automatica Learning Studio è un ambiente di sviluppo visual collaborazione che consente di organizzare la formazione e test di un modello analitica predittiva utilizzando dati di training impostato.
- **[Convertirlo in un esperimento predittivo]** - una volta il modello con le competenze con i dati esistenti e si è pronti per essere utilizzato per punteggio nuovi dati di preparazione e semplificare l'esperimento per le stime.
- **Distribuirlo come un servizio web** - è possibile distribuire l'esperimento predittiva come servizio Azure web [nuove] o [classica] . Gli utenti possono inviare dati al modello e ricevere le stime del modello.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>Creare un esperimento di formazione

Per formare un modello analitica predittiva, si utilizza Azure automatica di apprendimento Studio per creare un esperimento formazione dove si includono vari moduli di caricamento dei dati di formazione, preparare i dati in base alle esigenze, applicare gli algoritmi di apprendimento automatica e valutare i risultati. È possibile eseguire un'iterazione in un esperimento e provare gli algoritmi di apprendimento diversi per confrontare e valutare i risultati.

Il processo di creazione e gestione sperimentazione di formazione viene descritto in modo più in un' posizione. Per ulteriori informazioni, consultare questi articoli:

- [Creare un esperimento semplice in Azure automatica Learning Studio](machine-learning-create-experiment.md)
- [Sviluppare una soluzione predittiva con Azure l'apprendimento](machine-learning-walkthrough-develop-predictive-solution.md)
- [Importare i dati di formazione in Azure automatica Learning Studio](machine-learning-data-science-import-data.md)
- [Gestire le iterazioni esperimento in Azure automatica Learning Studio](machine-learning-manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertire un esperimento predittivo esperimento formazione

Dopo che è stato addestrati del modello, si è pronti per convertire la formazione esperimento in un esperimento predittivo per punteggio nuovi dati.

La conversione di un esperimento predittivo, si dispone del modello adeguatamente formato pronto per essere distribuito come un servizio web di punteggio. Gli utenti del servizio web possono inviare dati di input per il modello e il modello verrà inviato nuovamente i risultati della stima. Quando viene convertito in un esperimento predittivo, tenere in considerazione come si prevede che il modello da utilizzare per altri utenti.

Per convertire la formazione esperimento in un esperimento predittivo, fare clic su **Esegui** nella parte inferiore dell'area di prova, fare clic su **Backup servizio Web**, quindi selezionare **Predittiva servizio Web**.

![Convertire punteggio esperimento](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

Per ulteriori informazioni su come eseguire la conversione, vedere [Convert un esperimento di formazione di apprendimento per un esperimento predittivo](machine-learning-convert-training-experiment-to-scoring-experiment.md).

I passaggi seguenti descrivono la distribuzione di un esperimento predittivo come un nuovo servizio web. È inoltre possibile distribuire esperimento come servizio web classica.

## <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>Distribuire esperimento predittiva come un nuovo servizio web

Ora che sia stato preparato esperimento predittiva, è possibile distribuire come servizio web di Azure. Utilizzo del servizio web, gli utenti possono inviare dati al modello e il modello restituirà le previsioni.

Per distribuire l'esperimento predittiva, scegliere **Esegui** nella parte inferiore dell'area di prova. Al termine dell'esecuzione esperimento, fare clic su **Servizio Web di distribuire** e **distribuzione di servizio Web [New]**.  Verrà visualizzata la pagina di distribuzione del portale del servizio Web di apprendimento automatica.

### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>Deploy esperimento pagina del portale di servizio Web di apprendimento di computer

Nella pagina distribuzione esperimento, immettere un nome per il servizio web.
Selezionare un piano di prezzo. Se si dispone di un piano di prezzo esistente, che è possibile selezionare, in caso contrario è necessario creare un nuovo piano di prezzi per il servizio.

1.  In **Plan prezzo** a discesa, selezionare un piano esistente o selezionare l'opzione **Selezionare nuovo piano** .
2.  **Nome del piano**, digitare un nome che identificherà il piano sulla fattura online.
3.  Selezionare uno dei **Livelli pianificare mensile**. Il valore predefinito livelli per il piano per i piani per l'area predefinita e il servizio web viene distribuito in tale area.

Fare clic su **Distribuisci** e della pagina **della Guida rapida** per l'apertura di servizio web.

Pagina della Guida rapida del servizio web offre accesso e informazioni aggiuntive sulle attività più comuni eseguite dopo la creazione di un servizio web. Da qui è possibile accedere facilmente la pagina di prova e consumare pagina.

<!-- ![Deploy the web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-web-service"></a>Testare il servizio web

Per testare il nuovo servizio web, selezionare **Test di servizio web** nell'area attività comuni. Nella pagina Test, è possibile testare il servizio web come un servizio di richiesta-risposta (RR) o un Batch Execution (BES).

La pagina di test RR Visualizza gli input, output ed eventuali parametri globali che sono stati definiti per l'esperimento. Per testare il servizio web, è possibile immettere i valori appropriati per l'input manualmente o fornire un file formattato valore (con estensione CSV) separato da virgole contenente i valori di test.

Per eseguire il test utilizzando record di risorse, la modalità di visualizzazione elenco, immettere i valori appropriati per l'input e fare clic su **Test richiesta-risposta**. I risultati della stima visualizzato nella colonna output a sinistra.

![Distribuire il servizio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Per testare il BES, fare clic su **Batch**. Nella pagina test Batch, scegliere Sfoglia sotto l'input dell'utente e selezionare un file CSV contenente i valori di esempio appropriato. Se non si dispone di un file CSV ed è stato creato l'esperimento predittiva tramite Studio Learning automatica, è possibile scaricare il set di dati per la stima esperimento e utilizzarlo.

Per scaricare il set di dati, aprire automatica Learning Studio. Aprire l'esperimento predittiva e destro del mouse su input per l'esperimento. Menu di scelta rapida, selezionare **set di dati** e quindi selezionare **Scarica**.

![Distribuire il servizio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-7-mls-download.png)

Fare clic **Test**. Consente di visualizzare lo stato del processo di esecuzione del Batch verso destra in **Processi Batch Test**.

![Distribuzione del servizio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)-->

Nella pagina **configurazione** si possono modificare la descrizione, il titolo, aggiornare la chiave di account di archiviazione e abilitare i dati di esempio per il servizio web.

![Configurare il servizio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-8-arm-configure.png)

Dopo che è stato distribuito il servizio web, è possibile:

- **Accesso** tramite l'API del servizio web.
- **Gestisci** tramite portale di servizi web di apprendimento Azure o il portale classico Azure.
- **Aggiornamento** , se il modello viene modificato.

### <a name="access-the-web-service"></a>Accedere al servizio web

Una volta che si distribuisce il servizio web da Studio Learning automatica, è possibile inviare dati al servizio e ricevere risposte a livello di programmazione.

Pagina di **consumare** offre tutte le informazioni necessarie per accedere al servizio web. Ad esempio, il tasto API viene fornito per consentire l'accesso al servizio autorizzato.

Per ulteriori informazioni sull'accesso a un servizio web di apprendimento, vedere [all'utilizzo di un servizio web di apprendimento Azure distribuito](machine-learning-consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Gestire il nuovo servizio web

È possibile gestire il portale di servizi Web di apprendimento macchina servizi web classica. Nella [pagina del portale principale](https://services.azureml-test.net/), fare clic su **Servizi Web**. Nella pagina servizi web è possibile eliminare o copiare un servizio. Per monitorare un servizio specifico, fare clic sul servizio e quindi fare clic su **Dashboard**. Per monitorare i processi batch associati al servizio web, fare clic su **Registro richieste Batch**.

## <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>Distribuire esperimento predittiva come un servizio web classica

Ora che esperimento predittiva sufficientemente preparato, è possibile distribuire come un servizio web Azure. Utilizzo del servizio web, gli utenti possono inviare dati al modello e il modello restituirà le previsioni.

Per distribuire l'esperimento predittiva, fare clic su **Esegui** nella parte inferiore dell'area di prova e quindi fare clic su **Servizio Web di distribuzione**. Il servizio web è configurato e è incluso nel dashboard di servizio web.

![Distribuire il servizio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

È possibile testare il servizio web nel portale dei servizi Web di apprendimento automatica o automatica Learning Studio.

Per testare il servizio web richiesta di risposta, fare clic sul pulsante **Test** nel dashboard di servizio web. Una finestra di dialogo visualizzata per richiedere i dati di input per il servizio. Queste sono le colonne prevede dal punteggio esperimento. Immettere un blocco di dati e quindi fare clic su **OK**. I risultati generati dal servizio web vengono visualizzati nella parte inferiore del dashboard.

È possibile fare clic sul collegamento anteprima **Test** per verificare il servizio nel portale dei servizi Web di Azure macchina apprendimento, come illustrato precedentemente nella sezione nuovo servizio web.

Per testare il servizio di esecuzione in Batch, fare clic sul collegamento anteprima **di testing** . Nella pagina test Batch, fare clic su Sfoglia nell'input dell'utente e selezionare un file CSV contenente i valori di esempio appropriato. Se non si dispone di un file CSV ed è stato creato l'esperimento predittiva tramite Studio Learning automatica, è possibile scaricare il set di dati per la stima esperimento e utilizzarlo.

![Testare il servizio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

Nella pagina **configurazione** , è possibile modificare il nome visualizzato del servizio e fornire una descrizione. Il nome e la descrizione viene visualizzata nel [portale classica Azure](http://manage.windowsazure.com/) in cui è gestire i servizi web.

È possibile fornire una descrizione per i dati di input, dati di output e i parametri dei servizi web immettendo una stringa per ogni colonna in **SCHEMA di INPUT**, **SCHEMA di OUTPUT**e **Parametro del servizio Web**. Le descrizioni vengono utilizzate nella documentazione di codice di esempio fornita per il servizio web.

È possibile abilitare la registrazione diagnosticare gli eventuali errori che sta visualizzando quando è possibile accedere al servizio web. Per ulteriori informazioni, vedere [abilitare la registrazione per l'apprendimento i servizi web](machine-learning-web-services-logging.md).

![Configurare il servizio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)

È inoltre possibile configurare gli endpoint del servizio web del portale di servizi di Azure automatica apprendimento Web simile alla procedura illustrata in precedenza nella sezione nuovo servizio web. Le opzioni sono diverse, è possibile aggiungere o modificare la descrizione del servizio, Attiva registrazione e attiva i dati di esempio per i test.

### <a name="access-the-web-service"></a>Accedere al servizio web

Una volta che si distribuisce il servizio web da Studio Learning automatica, è possibile inviare dati al servizio e ricevere risposte a livello di programmazione.

Il dashboard offre tutte le informazioni necessarie per accedere al servizio web. Ad esempio chiave API fornita per consentire l'accesso al servizio autorizzato e pagine della Guida API sono disponibili per iniziare a scrivere il codice.

Per ulteriori informazioni sull'accesso a un servizio web di apprendimento, vedere [all'utilizzo di un servizio web di apprendimento Azure distribuito](machine-learning-consume-web-services.md).

### <a name="manage-the-web-service"></a>La gestione del servizio web

Sono disponibili diverse operazioni eseguibili per monitorare un servizio web. È possibile aggiornare ed eliminare lo. È inoltre possibile aggiungere altri endpoint in un servizio web classica oltre l'endpoint predefinito creato durante la distribuzione.

Per ulteriori informazioni, vedere [Manage un'area di lavoro di apprendimento Azure](machine-learning-manage-workspace.md) e [Manage un servizio web utilizzando il portale di servizi Web Learning automatica di Azure](machine-learning-manage-new-webservice.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

## <a name="update-the-web-service"></a>Aggiornare il servizio web

È possibile apportare modifiche al servizio web, ad esempio l'aggiornamento del modello con dati formazione aggiuntiva e distribuire nuovamente, sovrascrivendo il servizio web originale.

Per aggiornare il servizio web, aprire il primo esperimento predittiva utilizzati per distribuire il servizio web e creare una copia modificabile facendo clic su **Salva con nome**. Apportare le modifiche desiderate e quindi fare clic su **Servizio Web di distribuzione**.

Dal momento che è stato distribuito questo esperimento prima, viene chiesto se si desidera sovrascrivere (servizio Web classica) o aggiornare il servizio esistente (nuovo servizio web). Fare clic su **Sì** o **Update** consente di arrestare il servizio web esistente e distribuisce il nuovo esperimento predittiva viene distribuito in sostituzione.

> [AZURE.NOTE] Se sono state apportate modifiche alla configurazione del servizio web originale, ad esempio, immettere un nuovo nome visualizzato o la descrizione, è necessario immettere nuovamente questi valori.

Un'opzione per il servizio web di aggiornamento consiste nel addestrare nuovamente il modello a livello di programmazione. Per ulteriori informazioni, vedere [reimpostare l'apprendimento modelli a livello di programmazione](machine-learning-retrain-models-programmatically.md).


<!-- internal links -->
[Creare un esperimento di formazione]: #create-a-training-experiment
[Convertire in un esperimento predittivo]: #convert-the-training-experiment-to-a-predictive-experiment
[Nuovo]: #deploy-the-predictive-experiment-as-a-new-Web-service
[classica]: #deploy-the-predictive-experiment-as-a-new-Web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
