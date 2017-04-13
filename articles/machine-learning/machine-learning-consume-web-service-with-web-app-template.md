<properties
    pageTitle="Utilizzare un servizio web di apprendimento con un modello di app web | Microsoft Azure"
    description="Utilizzare un modello di app web di Azure Marketplace per utilizzare un servizio web previsione in Azure apprendimento."
    keywords="servizio Web, operationalization, API REST, lavorare risorse"
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
    ms.date="10/10/2016"
    ms.author="garye;raymondl"/>

# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>Utilizzo di un servizio web apprendimento Azure con un modello di app web

>[AZURE.NOTE] In questo argomento vengono descritte le tecniche applicabili a un servizio web classica. 

Dopo aver sviluppato il modello di previsione e distribuito come un servizio web Azure mediante Machine Learning Studio o mediante strumenti, ad esempio R o Python, è possibile accedere al modello operationalized utilizzando un API REST.

Esistono diversi modi per utilizzare l'API REST e accedere al servizio web. Ad esempio, è possibile scrivere un'applicazione in c#, R o Python mediante il codice di esempio generato automaticamente quando si distribuisce il servizio web (disponibile nella pagina Guida API nel dashboard di servizio web nel computer risorse Studio). È possibile utilizzare la cartella di lavoro Microsoft Excel creato (disponibile anche nel dashboard di servizio web Studio).

Ma il modo più semplice e veloce per accedere al servizio web consiste nell'usare i modelli di App Web disponibili nelle [App Web di Azure Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>Azure apprendimento modelli Web App

Modelli di app web disponibili in Azure Marketplace è possono creare un'app web personalizzata che conosca il servizio web dati di input e i risultati previsti. È sufficiente è concedere l'accesso app web al servizio web e i dati e il modello di tutte le altre applicazioni.

Sono disponibili due modelli:

- [Modello di Azure ML richiesta-risposta del servizio Web App](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
- [Modello di Azure ML Batch esecuzione del servizio Web App](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Ogni modello consente di creare un'applicazione ASP.NET di esempio, utilizzando API URI e la chiave per il servizio web e lo distribuisce come sito web in Azure. Il modello di risposta di richiesta di servizio (record) crea un'applicazione web che consente di inviare una singola riga di dati al servizio web per ottenere un singolo risultato. Il modello di servizio esecuzione Batch (BES) crea un'app web che consente di inviare il numero di righe di dati per ottenere più risultati.

Per utilizzare questi modelli non è necessario alcun codice. Sufficiente inserire l'API URI e la chiave e il modello consente di creare l'applicazione.

## <a name="how-to-use-the-request-response-service-rrs-template"></a>Come utilizzare il modello di risposta di richiesta di servizio (record)

Dopo aver distribuito il servizio web, è possibile eseguire la procedura seguente per utilizzare il modello di app web di record di risorse, come illustrato nella figura seguente.

![Procedura per utilizzare il modello di web record di risorse][image1]

1. Nel computer risorse Studio, aprire la scheda **Servizi Web** e quindi aprire il servizio web che si desidera accedere. Copiare la chiave elencata nella **chiave dell'API** e salvarlo.

    ![Chiave dell'API][image3]

2. Aprire la pagina di Guida API di **Richiesta/risposta** . Nella parte superiore della pagina della Guida, in **richiesta**, copiare il valore di **Richiedere URI** e salvarlo. Questo valore aspetto simile al seguente:

        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true

    ![URI richiesta][image4]

3. Passare al [portale di Azure](https://portal.azure.com), **accesso**, fare clic su **Nuovo**, cercare e selezionare **Azure ML risposta richiesta di servizio Web App**e quindi fare clic su **Crea**. 

    - Assegnare un'app web di un nome univoco. L'URL dell'applicazione web sarà questo nome seguito da `.azurewebsites.net.` , ad esempio,`http://carprediction.azurewebsites.net.`

    - Selezionare l'abbonamento Azure e servizi in cui viene eseguito il servizio web.

    - Fare clic su **Crea**.

    ![Creare web app][image5]

4. Al termine dell'app web di distribuzione Azure, fare clic sull' **URL** della pagina web app impostazioni in Azure o immettere l'URL in un web browser. Per esempio`http://carprediction.azurewebsites.net.`

5. Al primo avvio dell'applicazione web, verrà chiesto per **l'URL del Post API** e **Come chiave dell'API**.
Immettere i valori salvata in precedenza:
    - **URI della richiesta** di pagina della Guida all'API **dell'URL del Post API**
    - **Chiave dell'API** nel dashboard di servizio web per la **Chiave dell'API**.

    Fare clic su **Invia**.

    ![Inserire Post URI e come chiave dell'API][image6]

6. L'applicazione web consente di visualizzare la pagina di **Configurazione Web App** con le impostazioni del servizio web corrente. Qui è possibile apportare modifiche alle impostazioni usate da web app.

    > [AZURE.NOTE] Modifica delle impostazioni di solo li modifica per questa applicazione web. Essa non modifica le impostazioni predefinite del servizio web. Ad esempio, se si cambia la **Descrizione** di seguito non modificarla la descrizione visualizzata nel dashboard di servizio web nel computer risorse Studio.

    Al termine, fare clic su **Salva modifiche**e quindi fare clic su **Vai alla Home Page**.

7. Nella home page è possibile immettere valori per inviare al servizio web, fare clic su **Invia**e verrà restituito il risultato.

Se si desidera tornare alla pagina di **configurazione** , passare al `setting.aspx` page dell'app web. Ad esempio: `http://carprediction.azurewebsites.net/setting.aspx.` verrà richiesto di immettere nuovamente la chiave dell'API, è necessario che per accedere alla pagina e aggiornare le impostazioni.

È possibile interrompere, riavviare o eliminare un'app web nel portale di Azure come qualsiasi altra app web. Come è in esecuzione è possibile cercare l'indirizzo web iniziale e immettere nuovi valori.

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>Come utilizzare il modello di servizio esecuzione Batch (BES)

È possibile utilizzare il modello di app web BES nello stesso modo come modello di record di risorse, ad eccezione del fatto che l'applicazione web creata consentono di più righe di dati di inviare e ricevere più risultati.

I risultati da un servizio web di esecuzione batch vengono memorizzati in un contenitore di archiviazione Azure; i valori di input possono provenire da archiviazione Azure o file locale.
Sì, è necessario un contenitore di archiviazione Azure per contenere i risultati restituiti da web app e necessari per preparare i dati di input.

![Procedura per utilizzare il modello di web BES][image2]

1. Seguire la stessa procedura per creare il BES web app per il modello di record di risorse, ad eccezione di:
    - Ottenere l' **URI di richiesta** dalla pagina di Guida di **Esecuzione BATCH** API per il servizio web.
    - Passare al [Modello di Azure ML Batch esecuzione del servizio Web App](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) per aprire il modello BES in Azure Marketplace e fare clic su **Crea Web App**.

2. Per specificare in cui i risultati archiviati, immettere le informazioni sul contenitore di destinazione nella home page di web app. Specificare anche in un percorso web app accessibile i valori di input, in un file locale o un contenitore di archiviazione Azure.
Fare clic su **Invia**.

    ![Informazioni di archiviazione][image7]

L'applicazione web verrà visualizzata una pagina con lo stato dei processi.
Completato il processo vengono lasciati la posizione dei risultati in archiviazione blob Azure. È inoltre la possibilità di scaricare i risultati in un file locale.

## <a name="for-more-information"></a>Per ulteriori informazioni

Per ulteriori informazioni su...

- creazione di una prova di apprendimento con computer formazione Studio, vedere [creare la prima prova Azure Machine formazione Studio](machine-learning-create-experiment.md)

- come distribuire quanto appreso machine provare come servizio web, vedere [distribuire un servizio web formazione Machine Azure](machine-learning-publish-a-machine-learning-web-service.md)

- altri modi per accedere al servizio web, vedere [come utilizzare un servizio web apprendimento Azure](machine-learning-consume-web-services.md)


[image1]: media\machine-learning-consume-web-service-with-web-app-template\rrs-web-template-flow.png
[image2]: media\machine-learning-consume-web-service-with-web-app-template\bes-web-template-flow.png
[image3]: media\machine-learning-consume-web-service-with-web-app-template\api-key.png
[image4]: media\machine-learning-consume-web-service-with-web-app-template\post-uri.png
[image5]: media\machine-learning-consume-web-service-with-web-app-template\create-web-app.png
[image6]: media\machine-learning-consume-web-service-with-web-app-template\web-service-info.png
[image7]: media\machine-learning-consume-web-service-with-web-app-template\storage.png
