<properties 
    pageTitle="Utilizzare parametri di servizio Web di apprendimento Azure | Microsoft Azure" 
    description="Come utilizzare parametri di servizio Web di Azure computer risorse per modificare il comportamento del modello quando si accede al servizio web." 
    services="machine-learning" 
    documentationCenter="" 
    authors="raymondlaghaeian" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="raymondl;garye"/>

#<a name="use-azure-machine-learning-web-service-parameters"></a>Utilizzare parametri di servizio Web di apprendimento Azure

Un servizio web apprendimento Azure viene creato mediante la pubblicazione di una prova contenente moduli con parametri configurabili. In alcuni casi può essere necessario modificare il comportamento di modulo mentre è in esecuzione il servizio web. *Parametri di servizio Web* consentono di eseguire questa operazione. 

Impostazione di un tipico esempio [Importa dati] [ reader] modulo in modo che l'utente del servizio web pubblicato può specificare un'altra origine dati quando si accede al servizio web. O configurando [Esporta dati] [ writer] modulo in modo che è possibile specificare una destinazione diversa. Alcuni altri esempi sono la modifica del numero di bit per [L'Hashing caratteristica] [ feature-hashing] modulo o il numero di bene accolta caratteristiche per la [selezione delle caratteristiche di filtro basato] [ filter-based-feature-selection] modulo. 

È possibile impostare i parametri di servizio Web e associarli a uno o più parametri modulo nel test e specificare se sono obbligatori o facoltativi. L'utente del servizio web possa quindi fornire i valori per questi parametri quando si chiama il servizio web. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##<a name="how-to-set-and-use-web-service-parameters"></a>Come installare e utilizzare parametri di servizio Web

Per definire un parametro di servizio Web, fare clic sull'icona accanto al parametro per un modulo e selezionando "Imposta come parametro al servizio web". Verrà creato un nuovo parametro servizio Web e si connette al parametro di modulo. Quindi, quando si accede al servizio web, l'utente può specificare un valore per il parametro del servizio Web e viene applicato al parametro modulo.

Dopo aver definito un parametro di servizio Web, è disponibile per tutti gli altri parametri di modulo di prova. Se si definisce un parametro di servizio Web associato a un parametro per un modulo, è possibile utilizzare il parametro di servizio Web stesso per eventuali altri moduli, come il parametro prevede lo stesso tipo di valore. Ad esempio, se il parametro del servizio Web è un valore numerico, quindi può solo essere utilizzato per i parametri del modulo che prevede un valore numerico. Quando l'utente imposta un valore per il parametro del servizio Web, verrà applicato a tutti i parametri di modulo associato.

È possibile decidere se per fornire un valore predefinito per il parametro del servizio Web. In tal caso, il parametro è facoltativo per l'utente del servizio web. Se non si fornisce un valore predefinito, l'utente è richiesto di immettere un valore quando si accede al servizio web.

La documentazione API per il servizio web include informazioni per l'utente del servizio web su come specificare il parametro del servizio Web a livello di programmazione quando si accede al servizio web.

>[AZURE.NOTE] La documentazione API per un servizio web classica viene fornita tramite il collegamento **pagina Guida per l'API** del servizio web **DASHBOARD** in computer risorse Studio. La documentazione API per un nuovo servizio web disponibile tramite il portale di [Servizi Web di Windows Azure Machine Learning](https://services.azureml.net/Quickstart) nelle pagine **consumo** e **Swagger API** per il servizio web.


##<a name="example"></a>Esempio

Ad esempio, si supponga di disporre un sperimentare un [Esporta dati] [ writer] modulo che invia informazioni a archiviazione blob Azure. È necessario definire un parametro di servizio Web denominato "Blob percorso" che consente all'utente di servizio web modificare il percorso di archiviazione blob quando si accede al servizio.

1.  Nel computer risorse Studio, fare clic su [Esporta dati] [ writer] modulo per selezionarlo. Le relative proprietà vengono visualizzate nel riquadro delle proprietà a destra dell'area di prova.

2.  Specificare il tipo di archiviazione:

    - In **specificare la destinazione dei dati**, selezionare "Archiviazione Blob Azure".
    - In **specificare il tipo di autenticazione**, selezionare "Conto".
    - Immettere le informazioni sull'account per l'archiviazione blob Azure. 
    <p />

3.  Fare clic sull'icona a destra del **percorso di blob che inizia con parametro contenitore**. Simile al seguente:

    ![Icona di parametro al servizio Web][icon]

    Scegliere "Imposta come parametro al servizio web".

    Viene aggiunta una voce in **Parametri di servizio Web** nella parte inferiore del riquadro delle proprietà con il nome "Percorso blob che inizia con contenitore". Si tratta del parametro di servizio Web che è associato a questo [Esporta dati] [ writer] parametro modulo.

4.  Per rinominare il parametro del servizio Web, fare clic sul nome, immettere "Blob percorso" e premere **INVIO** . 
 
5.  Per specificare un valore predefinito per il parametro del servizio Web, fare clic sull'icona a destra del nome, selezionare "Fornire valore predefinito", immettere un valore (ad esempio, "container1/output1.csv") e premere **INVIO** .

    ![Parametro del servizio Web][parameter]

6.  Fare clic su **Esegui**. 

7.  Fare clic su **Servizio Web di distribuzione** e selezionare **La distribuzione del servizio Web [classica]** o **Distribuire un servizio Web [New] per distribuire il servizio web** .

L'utente del servizio web ora possibile specificare una nuova destinazione per [Esportare i dati] [ writer] modulo quando si accede al servizio web.

##<a name="more-information"></a>Ulteriori informazioni

Per un esempio più dettagliato, vedere la voce di [Parametri di servizio Web](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) nel [Computer risorse Blog](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Per ulteriori informazioni sull'accesso a un servizio web formazione computer, vedere [come utilizzare un servizio web pubblicato di apprendimento](machine-learning-consume-web-services.md).



<!-- Images -->
[icon]: ./media/machine-learning-web-service-parameters/icon.png
[parameter]: ./media/machine-learning-web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
 
