<properties
    pageTitle="Gestire il servizio Web tramite il portale di Azure Machine Learning Web Serivces | Microsoft Azure"
    description="Gestire l'accesso alle aree di lavoro di apprendimento Azure e distribuire e gestire ML API web servizi"
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="v-donglo"/>


# <a name="manage-a-web-service-using-the-azure-machine-learning-web-services-portal"></a>Gestire il servizio Web tramite il portale di servizi Web di Windows Azure Machine Learning

È possibile gestire computer risorse nuova e servizi Web classico tramite il portale di servizi Web di Microsoft Azure macchina risorse. Poiché servizi Web classico e servizi Web nuovo sono basati sulle tecnologie sottostante diverse, è necessario funzionalità di gestione leggermente diverse per ognuno di essi.

Nel portale di computer servizi Web di risorse è possibile:

- Monitorare l'utilizzo del servizio web.
- Configurare la descrizione, aggiornare le chiavi per il servizio web (solo nuovo), l'archiviazione account chiave (solo nuovo), attivare la registrazione, aggiornare e attivare o disattivare i dati di esempio.
- Eliminare il servizio web.
- Creazione, eliminazione o aggiornamento fatturazione plan di messaggistica unificata (solo nuovo).
- Aggiungere ed eliminare gli endpoint (solo classica)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="manage-new-web-services"></a>Gestione dei servizi Web nuovo

Per gestire i servizi di nuovo sito Web:

1.  Accedere al portale di [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) con il proprio account di Microsoft Azure: usare l'account associato all'abbonamento Azure.
2.  Nel menu fare clic su **Servizi Web**.

Verrà visualizzato un elenco di servizi Web distribuiti per l'abbonamento. 

Per gestire un servizio Web, fare clic su servizi Web. Dalla pagina servizi Web è possibile:

- Fare clic su servizio web per gestirlo.
- Fare clic su fatturazione piano per il servizio web per aggiornarlo.
- Eliminare un servizio web.
- Copiare un servizio web e distribuirlo a un'altra area.

Quando si fa clic su un servizio web, verrà visualizzata la pagina Guida introduttiva del servizio web. Pagina Guida introduttiva del servizio web include due opzioni di menu che consentono di gestire il servizio web:

- **DASHBOARD** - consente di visualizzare informazioni sull'utilizzo del servizio Web.
- **Configura** - consente di aggiungere un testo descrittivo, aggiornare la chiave per l'account di archiviazione associato al servizio Web e attivare o disattivare i dati di esempio.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorare l'utilizzo del servizio web ###

Fare clic sulla scheda **DASHBOARD** .

Dal dashboard, è possibile visualizzare l'utilizzo complessivo del servizio Web per un periodo di tempo. È possibile selezionare il periodo da visualizzare dal menu a discesa periodo in alto a destra dei grafici l'uso. Dashboard di Mostra le informazioni seguenti:

- **Le richieste nel tempo** consente di visualizzare un grafico di passaggio del numero di richieste nel periodo di tempo selezionato. È possibile identificare se si verificano picchi in uso.
- **Le richieste di richiesta risposta** consente di visualizzare il numero totale di chiamate richiesta risposta che il servizio ha ricevuto il periodo di tempo selezionato e non è riuscita quanti di loro.
- **Tempo medio richiesta risposta calcolare** visualizzerà una media del tempo necessario per eseguire richieste ricevute.
- **Richieste batch** consente di visualizzare il numero totale di richieste Batch il servizio ha ricevuto il periodo di tempo selezionato e non è riuscita quanti di loro.
- **Latenza media di processo** visualizzerà una media del tempo necessario per eseguire richieste ricevute.
- **Errori** Visualizza il numero complessivo degli errori che si sono verificati in chiamate al servizio web.
- **I costi di servizi** consente di visualizzare le spese per il piano di fatturazione associata al servizio.

### <a name="configuring-the-web-service"></a>Configurare il servizio web ###

Fare clic sull'opzione di menu di **configurazione** .

È possibile aggiornare le proprietà seguenti:

* **Descrizione** consente di immettere una descrizione per il servizio Web.
* **Titolo** consente di immettere un titolo per il servizio Web
* **Chiavi** consente di ruotare le chiavi API primarie e secondarie.
* **Chiave account lo spazio di archiviazione** consente di aggiornare la chiave per l'account di archiviazione associata alle modifiche di servizio Web. 
* **Dati di esempio abilitare** consente di fornire i dati di esempio che è possibile utilizzare per testare il servizio di risposta convocazione. Se è stato creato il servizio web nel computer risorse Studio, i dati di esempio viene accettati dai dati dell'utilizzato per formare il modello. Se il servizio è stato creato a livello di programmazione, i dati viene accettati dai dati di esempio forniti come parte del pacchetto JSON.

### <a name="managing-billing-plans"></a>Gestione dei piani di fatturazione ###

Fare clic sull'opzione di menu **plan di messaggistica unificata** dalla pagina Guida introduttiva di servizi web. È anche possibile scegliere il piano associato specifico servizio Web per gestire il piano.

* **Nuovo** consente di creare un nuovo piano.
* **Aggiungi/Rimuovi piano istanza** consente di "scalabilità" un piano esistente da aggiungere capacità.
* **Aggiornamento/DownGrade** consente di "scalabilità" un piano esistente da aggiungere capacità.
* **Eliminare** consente di eliminare un piano.

Fare clic su un piano per visualizzare il dashboard. Dashboard di offre l'utilizzo di snapshot o piano rispetto a un periodo di tempo. Per selezionare il periodo di tempo per la visualizzazione, fare clic sull'elenco a discesa **periodo** nella parte superiore destra del dashboard. 

Dashboard di piano fornisce le informazioni seguenti:

* **Descrizione piano** consente di visualizzare informazioni sui costi e capacità associato al piano.
* **Pianificare l'utilizzo** Visualizza il numero delle transazioni e ore di elaborazione addebitato rispetto al piano.
* **Servizi Web** consente di visualizzare il numero dei servizi Web che utilizzano il piano.
* **Superiore per chiamate ai servizi Web** consente di visualizzare i servizi Web primi quattro chiamate vengano applicati in base al piano.
* **Servizi Web superiore per calcolare ore** consente di visualizzare i servizi Web primi quattro che utilizzano risorse di elaborazione addebitato rispetto al piano.

## <a name="manage-classic-web-services"></a>Gestione dei servizi Web classico

> [AZURE.NOTE] Le procedure descritte in questa sezione sono relative alla gestione dei servizi web classica tramite il portale di servizi Web di Windows Azure computer risorse. Per informazioni sulla gestione dei servizi Web classico tramite il portale classico Azure e di Studio di risorse del computer, vedere [gestire un'area di lavoro di apprendimento Azure](machine-learning-manage-workspace.md).

Per gestire i servizi Web classico:

1.  Accedere al portale di [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) con il proprio account di Microsoft Azure: usare l'account associato all'abbonamento Azure.
2.  Nel menu, fare clic su **Servizi Web classica**.

Per gestire un servizio Web classica, fare clic su **Servizi Web classica**. Dalla pagina servizi Web classica è possibile:

- Fare clic su servizio web per visualizzare l'endpoint associati.
- Eliminare un servizio web.

Quando si gestisce un servizio Web classico, gestire tutti i punti finali separatamente. Quando si fa clic su un servizio web nella pagina servizi Web, si apre l'elenco di endpoint associato al servizio. 

Nella pagina endpoint servizio Web classica, è possibile aggiungere ed eliminare gli endpoint del servizio. Per ulteriori informazioni sull'aggiunta di endpoint, vedere [Creazione di endpoint](machine-learning-create-endpoint.md).

Fare clic su uno dei punti finali per aprire la pagina Guida introduttiva di servizio web. Nella pagina Guida introduttiva sono disponibili due opzioni di menu che consentono di gestire il servizio web:

- **DASHBOARD** - consente di visualizzare informazioni sull'utilizzo del servizio Web.
- **Configura** - consente di aggiungere un testo descrittivo, attivare la registrazione degli errori e disattivare Aggiorna la chiave per lo spazio di archiviazione account associato al servizio Web e attivare e disattivare i dati di esempio.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorare l'utilizzo del servizio web ###

Fare clic sulla scheda **DASHBOARD** .

Dal dashboard, è possibile visualizzare l'utilizzo complessivo del servizio Web per un periodo di tempo. È possibile selezionare il periodo da visualizzare dal menu a discesa periodo in alto a destra dei grafici l'uso. Dashboard di Mostra le informazioni seguenti:

- **Le richieste nel tempo** consente di visualizzare un grafico di passaggio del numero di richieste nel periodo di tempo selezionato. È possibile identificare se si verificano picchi in uso.
- **Le richieste di richiesta risposta** consente di visualizzare il numero totale di chiamate richiesta risposta che il servizio ha ricevuto il periodo di tempo selezionato e non è riuscita quanti di loro.
- **Tempo medio richiesta risposta calcolare** visualizzerà una media del tempo necessario per eseguire richieste ricevute.
- **Richieste batch** consente di visualizzare il numero totale di richieste Batch il servizio ha ricevuto il periodo di tempo selezionato e non è riuscita quanti di loro.
- **Latenza media di processo** visualizzerà una media del tempo necessario per eseguire richieste ricevute.
- **Errori** Visualizza il numero complessivo degli errori che si sono verificati in chiamate al servizio web.
- **I costi di servizi** consente di visualizzare le spese per il piano di fatturazione associata al servizio.

### <a name="configuring-the-web-service"></a>Configurare il servizio web ###

Fare clic sull'opzione di menu di **configurazione** .

È possibile aggiornare le proprietà seguenti:

* **Descrizione** consente di immettere una descrizione per il servizio Web. Descrizione il campo è obbligatorio.
* **Registrazione** consente di attivare o disattivare la registrazione per l'endpoint errori. Per ulteriori informazioni sulla registrazione, vedere abilita [registrazione per i servizi web di apprendimento](machine-learning-web-services-logging.md).
* **Dati di esempio abilitare** consente di fornire i dati di esempio che è possibile utilizzare per testare il servizio di risposta convocazione. Se è stato creato il servizio web nel computer risorse Studio, i dati di esempio viene accettati dai dati dell'utilizzato per formare il modello. Se il servizio è stato creato a livello di programmazione, i dati viene accettati dai dati di esempio forniti come parte del pacchetto JSON.

## <a name="grant-or-suspend-access-to-web-services-for-users-in-the-portal"></a>Concedere o revocare l'accesso ai servizi Web per gli utenti nel portale

Tramite il portale classico Azure, è possibile consentire o negare l'accesso a utenti specifici.

### <a name="access-for-users-of-new-web-services"></a>Accesso per gli utenti dei servizi Web nuovo

Per consentire ad altri utenti di lavorare con i servizi Web nel portale di servizi Web di Windows Azure computer risorse, è necessario aggiungerli come amministratori co nell'abbonamento a Azure.

Accedere al [portale classica Azure](https://manage.windowsazure.com/) usando l'account di Microsoft Azure: usare l'account associato all'abbonamento Azure.

1. Nel riquadro di spostamento, fare clic su **Impostazioni**, quindi fare clic su **amministratori**.
2. Nella parte inferiore della finestra, fare clic su **Aggiungi**. 
3. Nella finestra di dialogo ADD A CO-ADMINISTRATOR, digitare l'indirizzo di posta elettronica della persona che si desidera aggiungere come amministratore condivisa e quindi selezionare l'abbonamento a cui si desidera CO-amministratore per accedere.
4. Fare clic su **Salva**.

### <a name="access-for-users-of-classic-web-services"></a>Accesso per gli utenti dei servizi Web classica

Per gestire un'area di lavoro:

Accedere al [portale classica Azure](https://manage.windowsazure.com/) usando l'account di Microsoft Azure: usare l'account associato all'abbonamento Azure.

1. Nel Pannello di servizi di Microsoft Azure, fare clic su **Apprendimento**.
1. Fare clic su area di lavoro che si desidera gestire.
1. Fare clic sulla scheda **Configura** .

Dalla scheda configurazione è possibile revocare l'accesso all'area di lavoro apprendimento facendo clic su **Nega**. Gli utenti non saranno possibile aprire l'area di lavoro in computer risorse Studio. Per ripristinare l'accesso, fare clic su **Consenti**.

A utenti specifici:

Per gestire gli account aggiuntivi che hanno accesso all'area di lavoro nel computer risorse Studio, fare clic su **Accedi a ML Studio** nella scheda **DASHBOARD** . Verrà visualizzata l'area di lavoro nel computer risorse Studio. Da qui, fare clic su **utenti**e scheda **Impostazioni** . È possibile fare clic su **Invita altri utenti** per concedere l'accesso all'area di lavoro, oppure selezionare un utente e fare clic su **Rimuovi**.

> [AZURE.NOTE] Collegamento **al Studio ML** apre Studio risorse computer utilizzando l'Account Microsoft attualmente effettuato l'accesso. L'Account Microsoft usato per accedere al portale di classica Azure per creare un'area di lavoro non dispone automaticamente dell'autorizzazione per aprire l'area di lavoro. Per aprire un'area di lavoro, è necessario essere connessi all'account Microsoft che è stata definita come il proprietario dell'area di lavoro oppure è necessario ricevere un invito dal proprietario per partecipare l'area di lavoro.
