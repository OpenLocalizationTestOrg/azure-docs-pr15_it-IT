<properties
    pageTitle="Informazioni sulla gestione dei servizi web AzureML tramite Gestione API | Microsoft Azure"
    description="Guide che mostra come gestire i servizi web AzureML tramite Gestione API."
    keywords="apprendimento automatico, gestione api"
    services="machine-learning"
    documentationCenter=""
    authors="roalexan"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="roalexan" />


# <a name="learn-how-to-manage-azureml-web-services-using-api-management"></a>Informazioni sulla gestione dei servizi web AzureML con API Management

##<a name="overview"></a>Panoramica

Questa guida viene illustrato come iniziare rapidamente a gestione di API per gestire i servizi web AzureML.

##<a name="what-is-azure-api-management"></a>Che cos'è Azure API Management?

Gestione dell'API Azure è un servizio Azure che consente di gestire gli endpoint API REST definendo l'accesso degli utenti, la limitazione all'utilizzo e il monitoraggio di dashboard. Fare clic [qui](https://azure.microsoft.com/services/api-management/) per informazioni dettagliate sulla gestione delle API Azure. Fare clic [qui](../api-management/api-management-get-started.md) per una Guida su come iniziare a utilizzare Azure API Management. Questa Guida altri, questa guida è basata su, è descritta più argomenti, inclusi le configurazioni di notifica, livello prezzi, la gestione di risposta, autenticazione dell'utente, la creazione di prodotti, le sottoscrizioni di sviluppo e dashboarding l'uso.

##<a name="what-is-azureml"></a>Che cos'è AzureML?

AzureML è un servizio di Azure apprendimento automatico che consente di creare, distribuire e la condivisione delle soluzioni analitica avanzate. Fare clic [qui](https://azure.microsoft.com/services/machine-learning/) per informazioni dettagliate su AzureML.

##<a name="prerequisites"></a>Prerequisiti

Per completare questa Guida, è necessario:

* Un account Azure. Se non si dispone di un account Azure, fare clic [qui](https://azure.microsoft.com/pricing/free-trial/) per informazioni dettagliate su come creare un account di valutazione gratuito.
* Un account AzureML. Se non si dispone di un account AzureML, fare clic [qui](https://studio.azureml.net/) per informazioni dettagliate su come creare un account di valutazione gratuito.
* L'area di lavoro, il servizio e api_key per una prova di AzureML distribuito come un servizio web. Fare clic [qui](machine-learning-create-experiment.md) per informazioni dettagliate su come creare una prova di AzureML. Fare clic [qui](machine-learning-publish-a-machine-learning-web-service.md) per informazioni dettagliate su come distribuire una prova di AzureML come servizio web. In alternativa, appendice include istruzioni per la procedura creare e testare una prova AzureML semplice e distribuirlo come un servizio web.

##<a name="create-an-api-management-instance"></a>Creare un'istanza di gestione delle API

Di seguito sono i passaggi per l'utilizzo di gestione di API per gestire il servizio web AzureML. Prima di tutto creare un'istanza del servizio. Accedere al [Portale classica](https://manage.windowsazure.com/) e fare clic su **Nuovo** > **Servizi App** > **API Gestione** > **Crea**.

![Crea istanza](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-instance.png)

Specificare un **URL**univoco. Questa guida utilizza **demoazureml** , è necessario scegliere un diverso. Scegliere la **sottoscrizione** e **l'area** desiderata per l'istanza del servizio. Dopo aver apportato le opzioni desiderate, fare clic sul pulsante Avanti.

![creare-servizio-1](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-1.png)

Specificare un valore per il **Nome dell'organizzazione**. Questa guida utilizza **demoazureml** , è necessario scegliere un diverso. Immettere l'indirizzo di posta elettronica nel campo **e-mail amministratore** . Questo indirizzo di posta elettronica viene utilizzato per le notifiche del sistema di gestione dell'API.

![creare-servizio-2](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-2.png)

Fare clic sulla casella di controllo per creare l'istanza del servizio. *Richiede fino a 30 minuti per un nuovo servizio da creare*.

##<a name="create-the-api"></a>Creare l'API

Dopo aver creato l'istanza del servizio, il passaggio successivo consiste nel creare l'API. Una API è costituita da un set di operazioni che è possibile richiamare da un'applicazione client. Operazioni API vengano inoltrate ai servizi web esistenti. Questa Guida crea API tale proxy ai servizi web AzureML RRS e BES esistenti.

API create e configurate dal portale di publisher API, è possibile accedere tramite il portale classica Azure. Per accedere al portale di publisher, selezionare l'istanza del servizio.

![Selezionare istanza di servizio](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-service-instance.png)

Fare clic su **Gestisci** nel portale classica di Azure per il servizio di gestione dell'API.

![servizio di gestione](./media/machine-learning-manage-web-service-endpoints-using-api-management/manage-service.png)

Fare clic su **API** dal menu **Gestione API** sul lato sinistro e quindi fare clic su **Aggiungi API**.

![menu di gestione dell'API](./media/machine-learning-manage-web-service-endpoints-using-api-management/api-management-menu.png)

Digitare **AzureML Demo API** come il **nome dell'API Web**. Digitare **https://ussouthcentral.services.azureml.net** come l' **URL del servizio Web**. Digitare **azureml demo** come **suffisso URL API Web**. Selezionare **HTTPS** come la combinazione di **URL API Web** . Selezionare **Starter** come **prodotti**. Al termine, fare clic su **Salva** per creare l'API.

![aggiungere nuovi-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-new-api.png)

##<a name="add-the-operations"></a>Aggiungere le operazioni

Fare clic su **Aggiungi operazione** per operazioni di aggiunta a questa API.

![operazione di aggiunta](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-operation.png)

Verrà visualizzata la finestra **nuova operazione** e la scheda **firma** verrà selezionata per impostazione predefinita.

##<a name="add-rrs-operation"></a>Aggiungere record di risorse operazione

Prima di tutto creare un'operazione per il servizio AzureML RRS. Selezionare **Inserisci** come **verbo HTTP**. Tipo di **/services/ /workspaces/ {area di lavoro} {servizio} / execute?api versione = {apiversion} & dettagli = {dettagli}** come **modello URL**. Digitare **Record di risorse eseguire** come il **nome visualizzato**.

![aggiungere record di risorse-operazione-firme](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

Fare clic su **risposte** > **aggiungere** a sinistra e selezionare **200 OK**. Fare clic su **Salva** per salvare l'operazione.

![aggiungere record di risorse-operazione-risposta](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

##<a name="add-bes-operations"></a>Aggiungere le operazioni BES

Screenshot non sono inclusi per le operazioni di BES così come sono molto simili a quelle per l'aggiunta dell'operazione di record di risorse.

###<a name="submit-but-not-start-a-batch-execution-job"></a>Inviare un processo di esecuzione Batch (ma non inizino)

Fare clic su **Aggiungi operazione** per aggiungere l'operazione BES AzureML all'API. Selezionare **POST** per il **verbo HTTP**. Tipo di **/services/ /workspaces/ {area di lavoro} {servizio} / jobs?api versione = {apiversion}** per il **modello di URL**. Digitare **BES inviare** per il **nome visualizzato**. Fare clic su **risposte** > **aggiungere** a sinistra e selezionare **200 OK**. Fare clic su **Salva** per salvare l'operazione.

###<a name="start-a-batch-execution-job"></a>Avviare un processo di esecuzione Batch

Fare clic su **Aggiungi operazione** per aggiungere l'operazione BES AzureML all'API. Selezionare **POST** per il **verbo HTTP**. Tipo di **/jobs/ /services/ {servizio} /workspaces/ {area di lavoro} {ID processo} / start?api versione = {apiversion}** per il **modello di URL**. Per il **nome visualizzato**, digitare **BES Start** . Fare clic su **risposte** > **aggiungere** a sinistra e selezionare **200 OK**. Fare clic su **Salva** per salvare l'operazione.

###<a name="get-the-status-or-result-of-a-batch-execution-job"></a>Ottenere lo stato o il risultato di un processo di esecuzione Batch

Fare clic su **Aggiungi operazione** per aggiungere l'operazione BES AzureML all'API. Selezionare **OTTIENI** per il **verbo HTTP**. Tipo di **?api versioni /workspaces/ {area di lavoro} {servizio} /services/ /jobs/ {ID processo} = {apiversion}** per il **modello di URL**. Digitare **BES stato** per il **nome visualizzato**. Fare clic su **risposte** > **aggiungere** a sinistra e selezionare **200 OK**. Fare clic su **Salva** per salvare l'operazione.

###<a name="delete-a-batch-execution-job"></a>Eliminare un processo di esecuzione Batch

Fare clic su **Aggiungi operazione** per aggiungere l'operazione BES AzureML all'API. Per il **verbo HTTP**, selezionare **Elimina** . Tipo di **?api versioni /workspaces/ {area di lavoro} {servizio} /services/ /jobs/ {ID processo} = {apiversion}** per il **modello di URL**. Digitare **BES eliminare** per il **nome visualizzato**. Fare clic su **risposte** > **aggiungere** a sinistra e selezionare **200 OK**. Fare clic su **Salva** per salvare l'operazione.

##<a name="call-an-operation-from-the-developer-portal"></a>Chiamare un'operazione dal portale per sviluppatori

Operazioni possono essere chiamate direttamente dal portale per sviluppatori, che consente di visualizzare e testare le operazioni di un'API. In questo passaggio Guida chiamare il metodo di **Record di risorse eseguire** che è stato aggiunto all' **API Demo AzureML**. Fare clic su **portale per sviluppatori** dal menu nella parte superiore destra del portale di classica.

![portale per sviluppatori](./media/machine-learning-manage-web-service-endpoints-using-api-management/developer-portal.png)

Fare clic su **API** dal menu superiore e quindi fare clic su **AzureML Demo API** per visualizzare le operazioni disponibili.

![api demoazureml](./media/machine-learning-manage-web-service-endpoints-using-api-management/demoazureml-api.png)

Selezionare **Esegui record di risorse** per l'operazione. Fare clic su **Provalo**.

![Provare-it](./media/machine-learning-manage-web-service-endpoints-using-api-management/try-it.png)

Per i parametri di richiesta, digitare **l'area di lavoro**, **servizio**, **2.0** per **apiversion**e **true** per i **Dettagli**. È possibile trovare **l'area di lavoro** e i **servizi** nel dashboard di servizio web AzureML (vedere **verificare il servizio web** nell'appendice).

Per le intestazioni di richiesta, fare clic su **Aggiungi intestazione** e digitare **Tipo di contenuto** e **applicazione/json**, quindi fare clic su **Aggiungi intestazione** e digitare **autorizzazione** e **titolare <YOUR AZUREML SERVICE API-KEY> **. È possibile trovare la **chiave dell'api** nel dashboard di servizio web AzureML (vedere **verificare il servizio web** nell'appendice).

Tipo **{"Input": {"input1": {"ColumnNames": i valori"["Col2"],": [["Questa è un buono giorno"]]}}, "GlobalParameters": {}}** per il corpo della richiesta.

![api di demo azureml](./media/machine-learning-manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

Fare clic su **Invia**.

![Invia](./media/machine-learning-manage-web-service-endpoints-using-api-management/send.png)

Dopo il richiamo di un'operazione, il portale per sviluppatori consente di visualizzare l' **URL richiesto** dal servizio back-end, lo **stato della risposta**, le **intestazioni di risposta**e il **contenuto della risposta**.

![stato risposta](./media/machine-learning-manage-web-service-endpoints-using-api-management/response-status.png)

##<a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Appendice a - creazione e verifica AzureML un semplice servizio web

###<a name="creating-the-experiment"></a>Creazione di prova

Di seguito sono i passaggi per la creazione di una prova AzureML semplice e distribuzione come un servizio web. Assume il servizio web come input una colonna di testo non autorizzato e restituisce un set di caratteristiche rappresentati da numeri interi. Per esempio:

Testo | Testo hashing
--- | ---
Si tratta di un buon giorno | 1 1 2 2 0 2 0 1

Prima di tutto, utilizzare un browser preferito, passare a: [https://studio.azureml.net/](https://studio.azureml.net/) e immettere le credenziali per l'accesso. Creare una nuova prova vuoto.

![modelli di prova di ricerca](./media/machine-learning-manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Rinominarla **SimpleFeatureHashingExperiment**. Espandere **Salvato set di dati** e trascinare il prova **Recensioni da Amazon** .

![semplice-caratteristica-hashing-prova](./media/machine-learning-manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Espandere **La trasformazione dei dati** e **la modifica** e trascinare il prova **Seleziona colonne nel set di dati** . Connettere **recensioni da Amazon** per **Selezionare le colonne nel set di dati**.

![Selezionare colonne](./media/machine-learning-manage-web-service-endpoints-using-api-management/project-columns.png)

Fare clic su **Seleziona colonne nel set di dati** e quindi fare clic su **Avvia selettore di colonna** e selezionare **Col2**. Fare clic sul segno di spunta per applicare le modifiche.

![Selezionare colonne](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-columns.png)

Espandere **Analitica testo** e trascinare **L'Hashing caratteristica** di prova. Connettere **selezionare colonne nel set di dati** alla **caratteristica Hashing**.

![connettere colonne di progetto](./media/machine-learning-manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Digitare **3** per **bitsize Hashing**. Verrà creato 8 (23) colonne.

![l'hashing bitsize](./media/machine-learning-manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

A questo punto, può essere necessario fare clic su **Esegui** per eseguire il test di prova.

![Correre](./media/machine-learning-manage-web-service-endpoints-using-api-management/run.png)

###<a name="create-a-web-service"></a>Creare un servizio web

Creare un servizio web. Espandere **Servizio Web** e trascinare la prova di **Input** . Connettere **l'Hashing caratteristica**di **Input** . Anche trascinare **output** la prova. Connettere **l'Hashing caratteristica**di **Output** .

![output-a-caratteristica-hashing](./media/machine-learning-manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Fare clic su **servizio web pubblica**.

![servizio web pubblica](./media/machine-learning-manage-web-service-endpoints-using-api-management/publish-web-service.png)

Fare clic su **Sì** per pubblicare la prova.

![Sì per pubblicare](./media/machine-learning-manage-web-service-endpoints-using-api-management/yes-to-publish.png)

###<a name="test-the-web-service"></a>Verificare il servizio web

Un servizio web AzureML è costituito da RSS (servizio richiesta/risposta) e i punti finali (servizio di esecuzione batch) BES. RSS è l'icona del esecuzione. BES riguarda esecuzione del processo asincrono. Per testare il servizio web con l'origine di Python di esempio seguente, è necessario scaricare e installare Azure SDK per Python (vedere: [come installare Python](../python-how-to-install.md)).

È necessario anche **l'area di lavoro**, **il servizio**e **api_key** della prova per l'origine di esempio seguente. È possibile trovare l'area di lavoro e il servizio, fare clic su **Richiesta/risposta** o **Esecuzione Batch** per la prova nel dashboard di servizio web.

![Trova-area di lavoro-e-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

È possibile trovare **api_key** facendo clic sulla prova nel dashboard di servizio web.

![chiave dell'api di ricerca](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-api-key.png)

####<a name="test-rrs-endpoint"></a>Punto finale di record di risorse di test

#####<a name="test-button"></a>Pulsante test

Un modo semplice per testare l'endpoint di record di risorse consiste nel fare clic su **Test** nel dashboard di servizio web.

![test](./media/machine-learning-manage-web-service-endpoints-using-api-management/test.png)

Digitare **si tratta di un giorno utile** per **col2**. Fare clic sul segno di spunta.

![Immettere dati](./media/machine-learning-manage-web-service-endpoints-using-api-management/enter-data.png)

Si vedrà simile al

![esempio di output](./media/machine-learning-manage-web-service-endpoints-using-api-management/sample-output.png)

#####<a name="sample-code"></a>Codice di esempio

È possibile testare il record di risorse dal codice client. Se si fa clic su **richiesta/risposta** sulla barra del dashboard e scorrere fino alla fine, verrà visualizzato il codice di esempio per c#, Python e R. Verrà inoltre visualizzata la sintassi della richiesta di record di risorse, tra cui la richiesta URI, intestazioni e corpo.

Questa guida mostra un esempio Python di lavoro. È necessario modificare con **l'area di lavoro**, **il servizio**e **api_key** della prova.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
    data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
    }
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
    body = str.encode(json.dumps(data))
    try:
        req = urllib2.Request(url, body, headers)
        response = urllib2.urlopen(req)
        result = response.read()
        print "result:" + result
            except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))
        print(error.info())
        print(json.loads(error.read()))

####<a name="test-bes-endpoint"></a>Test BES endpoint
Fare clic su **esecuzione Batch** nel dashboard e scorrere fino alla fine. Verrà visualizzato il codice di esempio per c#, Python e R. Verrà visualizzata anche la sintassi delle richieste BES per inviare un processo, avviare un processo, ottenere lo stato o i risultati di un processo ed eliminare un processo.

Questa guida mostra un esempio Python di lavoro. È necessario modificare con **l'area di lavoro**, **il servizio**e **api_key** della prova. Inoltre, è necessario modificare il **nome dell'account di archiviazione**, **chiave account lo spazio di archiviazione**e **nome del contenitore di spazio di archiviazione**. Infine, sarà necessario modificare il percorso del **file di input** e il percorso del **file di output**.

    import urllib2
    import json
    import time
    from azure.storage import *
    workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
    service = "<REPLACE WITH YOUR SERVICE ID>"
    api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
    storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
    storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
    storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
    input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
    output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
    input_blob_name = "mydatablob.csv"
    output_blob_name = "myresultsblob.csv"
    def printHttpError(httpError):
    print("The request failed with status code: " + str(httpError.code))
    print(httpError.info())
    print(json.loads(httpError.read()))
    return
    def saveBlobToFile(blobUrl, resultsLabel):
    print("Reading the result from " + blobUrl)
    try:
        response = urllib2.urlopen(blobUrl)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    with open(output_file, "w+") as f:
        f.write(response.read())
    print(resultsLabel + " have been written to the file " + output_file)
    return
    def processResults(result):
    first = True
    results = result["Results"]
    for outputName in results:
        result_blob_location = results[outputName]
        sas_token = result_blob_location["SasBlobToken"]
        base_url = result_blob_location["BaseLocation"]
        relative_url = result_blob_location["RelativeLocation"]
        print("The results for " + outputName + " are available at the following Azure Storage location:")
        print("BaseLocation: " + base_url)
        print("RelativeLocation: " + relative_url)
        print("SasBlobToken: " + sas_token)
        if (first):
            first = False
            url3 = base_url + relative_url + sas_token
            saveBlobToFile(url3, "The results for " + outputName)
    return

    def invokeBatchExecutionService():
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
    blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
    print("Uploading the input to blob storage...")
    data_to_upload = open(input_file, "r").read()
    blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
    print "Uploaded the input to blob storage"
    input_blob_path = "/" + storage_container_name + "/" + input_blob_name
    connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
    payload =  {
        "Input": {
            "ConnectionString": connection_string,
            "RelativeLocation": input_blob_path
        },
        "Outputs": {
            "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
        },
        "GlobalParameters": {
        }
    }
        body = str.encode(json.dumps(payload))
    headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
    print("Submitting the job...")
    # submit the job
    req = urllib2.Request(url + "?api-version=2.0", body, headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = response.read()
    job_id = result[1:-1] # remove the enclosing double-quotes
    print("Job ID: " + job_id)
    # start the job
    print("Starting the job...")
    req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    url2 = url + "/" + job_id + "?api-version=2.0"

    while True:
        print("Checking the job status...")
        # If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
        req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
        try:
            response = urllib2.urlopen(req)
        except urllib2.HTTPError, error:
            printHttpError(error)
            return
        result = json.loads(response.read())
        status = result["StatusCode"]
        print "status:" + status
        if (status == 0 or status == "NotStarted"):
            print("Job " + job_id + " not yet started...")
        elif (status == 1 or status == "Running"):
            print("Job " + job_id + " running...")
        elif (status == 2 or status == "Failed"):
            print("Job " + job_id + " failed!")
            print("Error details: " + result["Details"])
            break
        elif (status == 3 or status == "Cancelled"):
            print("Job " + job_id + " cancelled!")
            break
        elif (status == 4 or status == "Finished"):
            print("Job " + job_id + " finished!")
            processResults(result)
            break
        time.sleep(1) # wait one second
    return
    invokeBatchExecutionService()
