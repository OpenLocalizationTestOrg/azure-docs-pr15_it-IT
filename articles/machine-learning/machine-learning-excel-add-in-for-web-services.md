<properties
    pageTitle="Componente aggiuntivo per i servizi Web di Microsoft Learning Machine Excel | Microsoft Azure"
    description="Come usare i servizi Web di Microsoft Azure Machine Learning direttamente in Excel senza scrivere codice."
    services="machine-learning"
    documentationCenter=""
    authors="tedway"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/05/2016"
    ms.author="tedway;garye" />

# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Componente aggiuntivo di Excel per i servizi Web di Microsoft Azure Machine Learning

Excel consente di chiamare servizi Web direttamente senza la necessità di scrivere codice.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Procedura per utilizzare un servizio Web esistente nella cartella di lavoro

1. Aprire il [file di Excel di esempio](http://aka.ms/amlexcel-sample-2), che contiene il componente aggiuntivo di Excel e i dati sulle persone nel Titanic.
2. Scegliere il servizio Web-"Titanic superstite previsioni (componente aggiuntivo di Excel esempio) [punteggio]" in questo esempio.

    ![Selezionare servizio Web][01]

3. Verrà visualizzata la sezione **Predict** .  Questa cartella di lavoro contiene già dei dati di esempio, ma per una cartella di lavoro vuota è possibile selezionare una cella in Excel e fare clic su **dati di esempio di utilizzo**.
4. Selezionare i dati con intestazioni e fare clic sull'icona di intervallo di dati di input.  Verificare che sia selezionata la casella "dati con intestazioni".
5. In **Output**, immettere il numero di celle in cui si desidera l'output per essere, ad esempio "H1" di seguito.
6. Fare clic su **prevedere**.

    ![Prevedere sezione][02]

## <a name="steps-to-add-a-new-web-service"></a>Procedura per aggiungere un servizio Web nuovo

Distribuire un servizio Web o utilizzare un servizio Web esistente. Per ulteriori informazioni sulla distribuzione di un servizio Web, vedere [procedura dettagliata passaggio 5: distribuire il servizio Web di Microsoft Azure Machine Learning](machine-learning-walkthrough-5-publish-web-service.md).

È possibile ottenere la chiave dell'API per il servizio Web. In questo caso dipende se è stato pubblicato un servizio Web di Microsoft Learning Machine classica di un servizio Web di apprendimento nuovo computer.

**Utilizzare un servizio Web classico** 

1. Nel computer risorse Studio, fare clic sulla sezione **Servizi WEB** nel riquadro a sinistra e quindi selezionare il servizio Web.

    ![Selezionare Studio un servizio Web][04]

2. Copiare la chiave dell'API per il servizio Web.

    ![Chiave API Studio][05]

3. Nella scheda **DASHBOARD** per il servizio Web, fare clic sul collegamento **Richiesta/risposta** .
4. Cercare la sezione **URI richiesta** .  Copiare e salvare l'URL.

>[AZURE.NOTE] È ora possibile accedere al portale di [Servizi Web di Windows Azure Machine Learning](https://services.azureml.net) per ottenere la chiave dell'API di un servizio Web di Microsoft Learning Machine classica.

**Utilizzare un servizio Web nuovo**

1. Nel portale di [Servizi Web di Windows Azure computer risorse](https://services.azureml.net) , fare clic su **Servizi Web**, quindi selezionare il servizio Web. 
2. Fare clic su **utilizzare**.
3. Cercare la sezione **informazioni di base consumo** . Copiare e salvare la **Chiave primaria** e l'URL di **Risposta convocazione** .


## <a name="steps-to-add-a-new-web-service"></a>Procedura per aggiungere un servizio Web nuovo

1. Distribuire un servizio Web o utilizzare un servizio Web esistente. Per ulteriori informazioni sulla distribuzione di un servizio Web, vedere [procedura dettagliata passaggio 5: distribuire il servizio Web di Microsoft Azure Machine Learning](machine-learning-walkthrough-5-publish-web-service.md).
2. Fare clic su **utilizzare**.
3. Cercare la sezione **informazioni di base consumo** . Copiare e salvare la **Chiave primaria** e l'URL di **Risposta convocazione** .
2. In Excel, passare alla sezione **Servizi Web** (se si è nella sezione **Predict** , scegliere la freccia Indietro per tornare all'elenco dei servizi Web).

    ![Passare alla selezione del servizio Web][03]
    
3. Fare clic su **Aggiungi servizio Web**.
4. Incollare l'URL in Excel aggiungere nella casella di testo **URL**.
5. Incollare la chiave primaria/API nella casella di testo **chiave dell'API**.
6. Fare clic su **Aggiungi**.

    ![URL e API chiave per un servizio Web classica.][06]

10. Per utilizzare il servizio Web, seguire le istruzioni precedenti, "Procedure per utilizzare un servizio Web esistente."

## <a name="sharing-your-workbook"></a>Condividere la cartella di lavoro

Se si salva la cartella di lavoro, viene salvata anche la chiave dell'API/principale per i servizi Web che sono stati aggiunti. Ciò significa che è necessario condividere la cartella di lavoro solo con persone che attendibili.

Porre domande, la sezione seguente contiene commenti o al [forum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png
