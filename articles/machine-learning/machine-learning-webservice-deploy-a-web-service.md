<properties
   pageTitle="Distribuzione di un nuovo servizio Web"
   description="Il flusso di lavoro di distribuzione di un processore basato su servizio web"
   services="machine-learning"
   documentationCenter=""
   authors="vDonGlover"
   manager="raymondl"
   editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="v-donglo"/>

# <a name="deploy-a-new-web-service"></a>Distribuire un nuovo servizio web

Microsoft Azure apprendimento ora offre servizi web che si basano sulle [Manager delle risorse Azure](../azure-resource-manager/resource-group-overview.md) consentendo di nuove opzioni del piano di fatturazione e distribuzione del servizio web per più paesi.

Il flusso di lavoro per distribuire un servizio web utilizzando servizi Web di Microsoft Azure macchina risorse è:

* Creare una prova di Office
* distribuirlo
* configurare il relativo nome
* piano di fatturazione
* eseguire il test
* utilizzarlo.

Nell'immagine seguente viene illustrato il flusso di lavoro.

![Flusso di lavoro distribuzione del servizio Web][1]
 
## <a name="deploy-web-service-from-studio"></a>Distribuire il servizio web da Studio 

Per distribuire una prova come un nuovo servizio web. Accedere a di Studio di risorse del computer e creare un nuovo servizio web previsione. 

**Nota**: se già stato distribuito una prova come servizio web classico non è possibile distribuirlo come un nuovo servizio web.
 
Fare clic su **Esegui** nella parte inferiore dell'area di prova e quindi fare clic su **Servizio Web di distribuzione** e **Distribuire il servizio Web [New]**. Verrà aperta la pagina di distribuzione di computer risorse Web Service manager.

## <a name="machine-learning-web-service-manager-deploy-experiment-page"></a>Gestione del servizio Web di formazione computer distribuire pagina di prova
Nella pagina distribuire prova immettere un nome per il servizio web.
Selezionare un piano di prezzo. Se si dispone di un piano di prezzo esistente, che è possibile selezionarla, in caso contrario è necessario creare un nuovo piano prezzo per il servizio. 

1.  Nel **Piano prezzo** elenco a discesa selezionare un piano esistente oppure selezionare l'opzione **Seleziona nuovo piano** .
2.  Nel **Nome del piano**, digitare un nome che consente di identificare il piano nella fattura.
3.  Selezionare uno dei **Livelli di piano mensile**. Si noti che le impostazioni predefinite livelli piano per i piani per l'area geografica predefinita e il servizio web viene distribuita in tale area.

Fare clic su **Distribuisci** e della pagina Guida introduttiva per aprirà il servizio web.

## <a name="quickstart-page"></a>Pagina Guida introduttiva
Pagina Guida introduttiva del servizio web offre accesso e indicazioni su più comuni operazioni eseguite dopo la creazione di un nuovo servizio web. Da qui è possibile accedere facilmente la pagina di **prova** e di pagina **consumo** .

## <a name="testing-your-web-service"></a>Verifica del servizio web

Nella pagina Guida introduttiva, fare clic su servizio web di Test in attività comuni.   

Per provare il servizio web come una risposta di richiesta di servizio (record):

* Fare clic su **Test** sulla barra dei menu.
* Fare clic su **Risposta convocazione**.
* Immettere i valori appropriati per le colonne inpue della prova.
* Fare clic su Test **Richiesta-risposta**.

I risultati verranno visualizzati sul lato destro della pagina.

Per verificare un servizio web Batch esecuzione servizio (BES), utilizzare un file CSV:

* Fare clic su **Test** sulla barra dei menu.
* Fare clic su **Batch**.
* Nell'input, fare clic su Sfoglia e passare al file di dati di esempio.
* Fare clic su **prova**.

Lo stato del test viene visualizzato in **Test processi Batch**.

## <a name="consuming-your-web-service"></a>Utilizzo del servizio Web

Quando distribuito come un servizio web, esperimenti apprendimento Azure specificare una API REST che possono essere utilizzati da una vasta gamma di dispositivi e piattaforme. In questo modo semplice API REST accetta e risponde con JSON formattato messaggi. Il portale di apprendimento Azure rappresenta il codice che può essere utilizzato per chiamare il servizio web R, c# e Python.
 
Nella pagina consumo è possibile trovare:

* Chiave dell'API e URI per l'utilizzo del servizio web nelle app.
* I modelli di app web ed Excel per iniziare immediatamente a iniziare il processo di consumo.
* Codice di esempio in c#, python e R per iniziare.

Per ulteriori informazioni sull'utilizzo dei servizi web, vedere [come utilizzare un servizio web Azure apprendimento che è stato distribuito da un computer di prova di apprendimento](machine-learning-consume-web-services.md).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'utilizzo dei servizi web, vedere:

[Come utilizzare un servizio web apprendimento Azure che è stato distribuito da un computer di prova di formazione](machine-learning-consume-web-services.md)

[Azure Machine Learning Web Services: Distribuzione e consumo](machine-learning-deploy-consume-web-service-guide.md)

<!--Image references-->
[1]: ./media/machine-learning-webservice-deploy-a-web-service/armdeploymentworkflow.png


<!--links-->
