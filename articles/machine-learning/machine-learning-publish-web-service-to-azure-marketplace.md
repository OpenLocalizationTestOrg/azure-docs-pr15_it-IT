<properties 
    pageTitle="Pubblicare apprendimento servizio a Azure Marketplace web | Microsoft Azure" 
    description="Come pubblicare il servizio Web di Azure Machine Learning a Azure Marketplace" 
    services="machine-learning" 
    documentationCenter="" 
    authors="BharathS" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="bharaths"/>

# <a name="publish-azure-machine-learning-web-service-to-the-azure-marketplace"></a>Pubblicare servizio Web risorse computer Azure Azure Marketplace 

Azure Marketplace offre la possibilità di pubblicare servizi web di apprendimento Azure come pagata o liberare servizi per consumi dai clienti esterni. In questo articolo viene fornita una panoramica del processo con collegamenti alle linee guida per iniziare. Con questo processo, è possibile rendere disponibile per gli altri sviluppatori di utilizzare nelle applicazioni servizi web.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="overview-of-the-publishing-process"></a>Panoramica del processo di pubblicazione 

Di seguito sono i passaggi per la pubblicazione di un servizio web apprendimento Azure a Azure Marketplace:

1. Creare e pubblicare un servizio di risposta con computer risorse richiesta (record)
2. Distribuire il servizio di produzione e ottenere le informazioni sull'endpoint chiave dell'API e OData.
3. Utilizzare l'URL del servizio web pubblicato per pubblicare in [Azure Marketplace (Data Market)](https://publish.windowsazure.com/workspace/) 
4. Dopo aver inviato, l'offerta viene esaminata e deve essere approvati prima i clienti può avviare acquisto. Il processo di pubblicazione può richiedere alcuni giorni lavorativi. 

## <a name="walk-through"></a>Scorrere
###<a name="step-1-create-and-publish-a-machine-learning-request-response-service-rrs"></a>Passaggio 1: Creare e pubblicare un servizio di risposta con computer risorse richiesta (record)###
 Se si dispone non già eseguito l'operazione, dare un'occhiata di questo [scorrere](machine-learning-walkthrough-5-publish-web-service.md).

###<a name="step-2-deploy-the-service-to-production-and-obtain-the-api-key-and-odata-endpoint-information"></a>Passaggio 2: Distribuire il servizio di produzione e ottenere la chiave dell'API e le informazioni sull'endpoint OData###
1. Dal [Portale classica di Azure](http://manage.windowsazure.com), selezionare l'opzione di **Apprendimento** dalla barra di spostamento a sinistra e selezionare l'area di lavoro. 

2. Fare clic sulla scheda **Servizi WEB** e selezionare il servizio web per la pubblicazione Marketplace.

    ![Azure Marketplace][workspace]

3. Selezionare l'endpoint di cui che si desidera utilizzare per il marketplace. Se non è stato creato eventuali altri endpoint, è possibile selezionare l'endpoint **predefinito** .

4. Dopo aver selezionato il punto finale, sarà possibile visualizzare la **Chiave dell'API**. È necessario il supporto delle informazioni in un secondo momento nel passaggio 3, dunque, una copia.

    ![Azure Marketplace][apikey]

5. Fare clic sul metodo **Richiesta/risposta** , in corrispondenza del punto che servizi pubblicazione per l'esecuzione batch Marketplace non è supportata. Si passerà alla pagina della Guida all'API per il metodo di richiesta/risposta.

6. Copiare l' **Indirizzo dell'Endpoint OData**, è necessario queste informazioni in un secondo momento nel passaggio 3.

    ![Azure Marketplace][odata]




distribuire il servizio nell'ambiente di produzione.



###<a name="step-3-use-the-url-of-the-published-web-service-to-publish-to-azure-marketplace-datamarket"></a>Passaggio 3: Usare l'URL del servizio web pubblicato per pubblicare in Azure Marketplace (DataMarket)###

1.  Passare al [Azure Marketplace (Data Market)](http://datamarket.azure.com/home) 
2.  Fare clic sul collegamento della **pubblicazione** nella parte superiore della pagina. Verrà visualizzata al [Portale di pubblicazione di Microsoft Azure](https://publish.windowsazure.com)
3.  Fare clic sulla sezione **autori** per registrare come un autore.
4.  Quando si crea una nuova offerta, selezionare **I servizi di dati**, quindi fare clic su **Crea un nuovo servizio dati**. 
 
    ![Azure Marketplace][image1]

    <br />


5.  In **plan di messaggistica unificata** fornisce informazioni sugli, che comprende un piano di prezzo. Decidere se si offrirà un servizio gratuito o a pagamento. Per ottenere pagato, fornire informazioni sul pagamento, ad esempio le informazioni banca e imposte.

6.  In **Marketing** sono riportate informazioni l'offerta, ad esempio il titolo e una descrizione per l'offerta.

7.  In **prezzi** è possibile impostare il prezzo per i piani di paesi specifici o attendere che il sistema "autoprice" l'offerta.

8. Nella scheda **Servizio dati** , fare clic su **Servizio Web** come **Origine dati**.

    ![Azure Marketplace][image2]

9.  Ottenere la chiave di URL e API del servizio web dal portale classica di Azure, come descritto nel passaggio 2 precedente.

10. Nella finestra di dialogo Installazione di servizio dati Marketplace, incollare l'indirizzo di endpoint OData nella casella di testo **URL del servizio** .

11. Per l' **autenticazione**, scegliere **intestazione** come **Schema di autenticazione**.

    - Immettere "Autorizzazione" per il **nome di intestazione**.
    - Per il **Valore di intestazione**, immettere "Titolare" (senza virgolette) e quindi incollare la chiave dell'API la barra **SPAZIATRICE** .
    - Selezionare la casella di controllo **questo servizio è OData** .
    - Fare clic su **Test connessione** per verificare la connessione.

12. In **categorie**, assicurarsi che sia selezionata **L'apprendimento** .

13. Una volta completata l'immissione di tutti i metadati sull'offerta, fare clic su **pubblica**e quindi **Push di gestione temporanea**. A questo punto, l'utente verrà informato di eventuali problemi rimanenti necessari per la risoluzione.

14. Dopo aver verificato il completamento di tutti i problemi in sospeso, fare clic su **richiedere l'approvazione da inserire nell'ambiente di produzione**. Il processo di pubblicazione può richiedere alcuni giorni lavorativi. 


[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]:./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]:./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]:./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png
 
