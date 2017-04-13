<properties
    pageTitle="Connettersi a un servizio Web di apprendimento computer | Microsoft Azure"
    description="Con c# o Python, connettersi a un servizio Web di Microsoft Azure Machine Learning mediante una chiave di autorizzazione."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016" 
    ms.author="garye" />


# <a name="connect-to-an-azure-machine-learning-web-service"></a>Connettersi a un servizio Web di apprendimento Machine Azure

L'esperienza di sviluppo di apprendimento Azure è una API del servizio Web per rendere le stime dai dati di input in tempo reale o in modalità batch. Utilizzare Azure Machine Learning Studio per creare le stime e distribuire un servizio Web di Microsoft Azure Machine Learning.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Per informazioni su come creare e distribuire un servizio Web di Microsoft Learning computer utilizzando Machine Learning Studio:

- Per un'esercitazione su come creare una prova in computer risorse Studio, vedere [creare la prima prova](machine-learning-create-experiment.md).
- Per informazioni dettagliate su come distribuire un servizio Web, vedere [distribuire un servizio Web di Microsoft Learning computer](machine-learning-publish-a-machine-learning-web-service.md).
- Per ulteriori informazioni sull'apprendimento in generale, visitare la [Documentazione Centro risorse](https://azure.microsoft.com/documentation/services/machine-learning/).

## <a name="azure-machine-learning-web-service"></a>Servizio Web di Microsoft Learning Machine Azure ##

Con il servizio Web di Microsoft Azure Machine Learning, un'applicazione esterna comunica con un modello punteggio del flusso di lavoro di apprendimento in tempo reale. Una chiamata al servizio Web di Microsoft Learning computer restituisce risultati stima a un'applicazione esterna. Per rendere un servizio Web di Microsoft Learning Machine chiamata, passare una chiave dell'API creato quando si distribuisce una previsione. Il servizio Machine Learning Web dipende dal resto, una scelta architettura comuni per i progetti di programmazione web.

Apprendimento Azure sono disponibili due tipi di servizi:

- Servizio di richiesta di risposta (record): una latenza bassa, scalabilità servizio che offre un'interfaccia per i modelli senza informazioni sullo stato creato e distribuito da Studio risorse computer.
- Un asincrono di servizio esecuzione batch (BES)-servizio che punteggi un batch di record di dati.

Per ulteriori informazioni sui servizi Web di Microsoft Learning Machine, vedere [distribuire un servizio Web di Microsoft Learning computer](machine-learning-publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Ottenere una chiave di autorizzazione di apprendimento Azure ##

Quando si distribuisce il prova, vengono generate chiavi API per il servizio Web. È possibile recuperare i tasti da diverse posizioni.

## <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Dal portale di servizi Web di Microsoft Azure macchina risorse

Accedere al portale di [Servizi Web di Microsoft Azure macchina risorse](https://services.azureml.net) .

Per recuperare la chiave dell'API per un servizio Web di apprendimento nuovo computer:

1. Nel portale di servizi Web di Windows Azure computer risorse, fare clic su **Servizi Web** menu superiore.
2. Fare clic su servizio Web per il quale si desidera recuperare la chiave.
3. Nel menu superiore, fare clic su **consumo**.
4. Copiare e salvare la **Chiave primaria**.


Per recuperare la chiave dell'API di un servizio Web di Microsoft Learning Machine classica:

1. Nel portale di servizi Web di Windows Azure computer risorse, fare clic su **Servizi Web classica** menu superiore.
2. Fare clic su servizio Web a cui si sta lavorando.
3. Fare clic sull'endpoint per il quale si desidera recuperare la chiave.
3. Nel menu superiore, fare clic su **consumo**.
4. Copiare e salvare la **Chiave primaria**.

## <a name="classic-web-service"></a>Servizio Web classica ##

 È inoltre possibile recuperare una chiave di un servizio Web classico dal computer risorse Studio o il portale di Azure.

### <a name="machine-learning-studio"></a>Computer risorse Studio ###

1. Nel computer risorse Studio, fare clic su **Servizi WEB** sul lato sinistro.
2. Fare clic su un servizio Web. **Chiave dell'API** è nella scheda **DASHBOARD** .

### <a name="azure-portal"></a>Portale di Azure ###

1. Fare clic su **Apprendimento** a sinistra.
2. Fare clic su area di lavoro in cui si trova il servizio Web.
3. Fare clic su **servizi WEB**.
4. Fare clic su un servizio Web.
5. Fare clic su un punto finale. Nell'angolo inferiore destro viene premuto il tasto di API"".

## <a id="connect"></a>Connettersi a un servizio Web di Microsoft Learning computer

È possibile connettersi a un servizio Web di Microsoft Learning computer utilizzando qualsiasi linguaggio di programmazione che supporta la risposta e richiesta HTTP. È possibile visualizzare esempi in c#, Python e R da una pagina di Guida del servizio Web di Microsoft Learning computer.

**Guida per l'apprendimento API computer** Guida all'API di apprendimento computer viene creata quando si distribuisce un servizio Web. Vedere [procedura dettagliata di apprendimento Azure - distribuzione di servizio Web](machine-learning-walkthrough-5-publish-web-service.md).
La Guida Machine Learning API contiene i dettagli di una funzione di completamento del servizio Web.

2. Fare clic su servizio Web a cui si sta lavorando.
3. Fare clic sull'endpoint per il quale si desidera visualizzare la pagina API della Guida.
3. Nel menu superiore, fare clic su **consumo**.
3. Fare clic su **pagina Guida per l'API** in risposta convocazione o i punti finali esecuzione Batch.

**Alla visualizzazione apprendimento API della Guida per un servizio Web nuovo**

Nel computer di Azure portale Servizi Web di apprendimento:

1. Fare clic su **Servizi WEB** nel menu principale.
2. Fare clic su servizio Web per il quale si desidera recuperare la chiave.

Fare clic su **consumo** per ottenere gli URI per la richiesta Reposonse e servizi per l'esecuzione Batch e il codice di esempio in c#, R e Python.

Fare clic su **Swagger API** per ottenere documentazione Swagger in base a API denominato da URI specificato.

### <a name="c-sample"></a>Esempio di c# ###

Per connettersi a un servizio Web di Microsoft Learning computer, usare un **HttpClient** passando ScoreData. ScoreData contiene un FeatureVector un vettore n dimensionali delle caratteristiche numerici che rappresenta la ScoreData. L'autenticazione per il servizio di apprendimento con una chiave dell'API.

Per connettersi a un servizio Web di Microsoft Learning computer, è necessario installare il pacchetto NuGet **Microsoft.AspNet.WebApi.Client** .

**Installare Microsoft.AspNet.WebApi.Client NuGet in Visual Studio**

1. Pubblicare il set di dati di Download da UCI: set di dati di 2 adulto classe servizio Web.
2. Fare clic su **Strumenti** > **Manager pacchetto NuGet** > **Console di gestione pacchetti**.
2. Scegliere **Microsoft.AspNet.WebApi.Client pacchetto di installazione**.

**Per eseguire l'esempio di codice**

1. Pubblicare "esempio 1: scaricare set di dati da UCI: set di dati classe adulto 2" prova, parte della raccolta di esempio di apprendimento.
2. Assegnare apiKey con la chiave da un servizio Web. Vedere **ottenere una chiave di autorizzazione di apprendimento Azure** sopra.
3. Assegnare serviceUri con URI richiesta.


### <a name="python-sample"></a>Esempio Python ###

Per connettersi a un servizio Web di Microsoft Learning computer, utilizzare la libreria **urllib2** passando ScoreData. ScoreData contiene un FeatureVector un vettore n dimensionali delle caratteristiche numerici che rappresenta la ScoreData. L'autenticazione per il servizio di apprendimento con una chiave dell'API.


**Per eseguire l'esempio di codice**

1. Distribuire "esempio 1: scaricare set di dati da UCI: set di dati classe adulto 2" prova, parte della raccolta di esempio di apprendimento.
2. Assegnare apiKey con la chiave da un servizio Web. Vedere la sezione **ottenere una chiave di autorizzazione di apprendimento Azure** accanto all'inizio di questo articolo.
3. Assegnare serviceUri con URI richiesta.
