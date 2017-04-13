<properties
    pageTitle="Gestire un'area di lavoro di apprendimento | Microsoft Azure"
    description="Gestire l'accesso alle aree di lavoro di apprendimento Azure e distribuire e gestire ML API web servizi"
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
    ms.date="10/05/2016"
    ms.author="garye"/>


# <a name="manage-an-azure-machine-learning-workspace"></a>Gestire un'area di lavoro di apprendimento Azure

>[AZURE.NOTE] Le procedure descritte in questo articolo sono i contenuti pertinenti per servizi Web classica di Azure computer risorse. Per informazioni sulla gestione dei servizi Web nel portale di computer servizi Web di risorse, vedere [gestire un servizio Web tramite il portale di servizi Web di Windows Azure computer risorse](machine-learning-manage-new-webservice.md).

Tramite il portale classico Azure, è possibile gestire le aree di lavoro di apprendimento per:

- Monitorare l'utilizzo l'area di lavoro
- Configurare l'area di lavoro per consentire o negare l'accesso
- Gestire i servizi Web creati nell'area di lavoro
- Eliminare l'area di lavoro

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Inoltre, la scheda dashboard fornisce una panoramica relativa all'utilizzo di un'area di lavoro e una rapida panoramica delle informazioni dell'area di lavoro.  

> [AZURE.TIP] In Azure Machine Learning Studio, nella scheda **Servizi WEB** è di aggiungere, aggiornare o eliminare un servizio Web di Microsoft Learning computer.

Per gestire un'area di lavoro:

1.  Accedere al [portale classica Azure](https://manage.windowsazure.com/) usando l'account di Microsoft Azure: usare l'account associato all'abbonamento Azure.
2.  Nel Pannello di servizi di Microsoft Azure, fare clic su **Apprendimento**.
3.  Fare clic su area di lavoro che si desidera gestire.

La pagina dell'area di lavoro è presenti tre schede:

- **DASHBOARD** - consente di visualizzare informazioni sull'utilizzo dell'area di lavoro e informazioni
- **Configura** - consente di gestire l'accesso all'area di lavoro
- **Servizi WEB** - consente di gestire i servizi Web pubblicati dall'area di lavoro

## <a name="to-monitor-how-the-workspace-is-being-used"></a>Per controllare come viene usata l'area di lavoro

Fare clic sulla scheda **DASHBOARD** .

Dal dashboard, è possibile visualizzare l'uso generale dell'area di lavoro e ottenere una rapida panoramica delle informazioni di un'area di lavoro.

- **Calcolare** Mostra le risorse di elaborazione usate per l'area di lavoro. È possibile modificare la visualizzazione per visualizzare relativo o valori assoluti ed è possibile modificare il periodo di tempo visualizzato nel grafico.
- **Panoramica di utilizzo** consente di visualizzare lo spazio di archiviazione Azure usata per l'area di lavoro.
- **Visualizzazione rapida** viene fornito un riepilogo delle informazioni di un'area di lavoro e collegamenti utili.

> [AZURE.NOTE] Collegamento **al Studio ML** apre Studio risorse computer utilizzando l'Account Microsoft attualmente effettuato l'accesso. L'Account Microsoft usato per accedere al portale di classica Azure per creare un'area di lavoro non dispone automaticamente dell'autorizzazione per aprire l'area di lavoro. Per aprire un'area di lavoro, è necessario essere connessi all'account Microsoft che è stata definita come il proprietario dell'area di lavoro oppure è necessario ricevere un invito dal proprietario per partecipare l'area di lavoro.


## <a name="to-grant-or-suspend-access-for-users"></a>Per concedere o revocare l'accesso per gli utenti ##

Fare clic sulla scheda **Configura** .

Dalla scheda configurazione è possibile:

- Revocare l'accesso all'area di lavoro apprendimento facendo clic su Nega. Gli utenti non saranno possibile aprire l'area di lavoro in computer risorse Studio. Per ripristinare l'accesso, fare clic su Consenti.

Per gestire gli account aggiuntivi che hanno accesso all'area di lavoro nel computer risorse Studio, fare clic su **Accedi a ML Studio** nella scheda **DASHBOARD** (vedere la nota precedente relativa **al Studio ML**). Verrà visualizzata l'area di lavoro nel computer risorse Studio. Da qui, fare clic su **utenti**e scheda **Impostazioni** . È possibile fare clic su **Invita altri utenti** per concedere l'accesso all'area di lavoro, oppure selezionare un utente e fare clic su **Rimuovi**.


## <a name="to-manage-web-services-in-this-workspace"></a>Per gestire i servizi web nell'area di lavoro

Fare clic sulla scheda **Servizi WEB** .

Verrà visualizzato un elenco di servizi web pubblicati dall'area di lavoro.
Per gestire un servizio web, fare clic sul nome dell'elenco per aprire la pagina del servizio Web.

Un servizio Web, potrebbe essere uno o più endpoint definito.

- È possibile definire più endpoint oltre l'endpoint "Predefinito". Per aggiungere il punto finale, fare clic su **Gestione endpoint** nella parte inferiore del dashboard per aprire il portale di servizi Web di Windows Azure computer risorse.

- Per eliminare un endpoint (non è possibile eliminare l'endpoint "Predefinito"), fare clic sulla casella di controllo all'inizio della riga endpoint e fare clic su **Elimina**. Consente di rimuovere l'endpoint del servizio Web.

    > [AZURE.NOTE] Se un'applicazione utilizza l'endpoint del servizio web quando viene eliminato il punto finale, l'applicazione verrà visualizzato un messaggio di errore la volta successiva che tenta di accedere al servizio.

Fare clic sul nome di un endpoint del servizio Web per aprirlo. 

Dal dashboard, è possibile visualizzare l'utilizzo complessivo del servizio Web per un periodo di tempo. È possibile selezionare il periodo da visualizzare dal menu a discesa periodo in alto a destra dei grafici l'uso. Dashboard di Mostra le informazioni seguenti:

- **Le richieste nel tempo** consente di visualizzare un grafico di passaggio del numero di richieste nel periodo di tempo selezionato. È possibile identificare se si verificano picchi in uso.
- **Le richieste di richiesta risposta** consente di visualizzare il numero totale di chiamate richiesta risposta che il servizio ha ricevuto il periodo di tempo selezionato e non è riuscita quanti di loro.
- **Tempo medio richiesta risposta calcolare** visualizzerà una media del tempo necessario per eseguire richieste ricevute.
- **Richieste batch** consente di visualizzare il numero totale di richieste Batch il servizio ha ricevuto il periodo di tempo selezionato e non è riuscita quanti di loro.
- **Latenza media di processo** visualizzerà una media del tempo necessario per eseguire richieste ricevute.
- **Errori** Visualizza il numero complessivo degli errori che si sono verificati in chiamate al servizio web.
- **I costi di servizi** consente di visualizzare le spese per il piano di fatturazione associata al servizio.

Nella pagina di configurazione, è possibile aggiornare le proprietà seguenti:

* **Descrizione** consente di immettere una descrizione per il servizio Web. Descrizione il campo è obbligatorio.
* **Registrazione** consente di attivare o disattivare la registrazione per l'endpoint errori. Per ulteriori informazioni sulla registrazione, vedere abilita [registrazione per i servizi web di apprendimento](machine-learning-web-services-logging.md).
* **Dati di esempio abilitare** consente di fornire i dati di esempio che è possibile utilizzare per testare il servizio di risposta convocazione. Se è stato creato il servizio web nel computer risorse Studio, i dati di esempio viene accettati dai dati dell'utilizzato per formare il modello. Se il servizio è stato creato a livello di programmazione, i dati viene accettati dai dati di esempio forniti come parte del pacchetto JSON.

[consume]: machine-learning-consume-web-services.md
[marketplace]: machine-learning-publish-web-service-to-azure-marketplace.md
