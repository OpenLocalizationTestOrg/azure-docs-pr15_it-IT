<properties
   pageTitle="Guida di geografico con più | Microsoft Azure"
   description="Informazioni su come creare un'area di lavoro e pubblicare un servizio web in un'area di Azure diversa da Stati Uniti centrali Sud (SCUS) area Azure."
   services="machine-learning"
   documentationCenter=""
   authors="tedway"
   manager="jhubbard"
   editor="rmca14"
   tags=""/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="tedway; neerajkh"/>

# <a name="multi-geo-help-documentation"></a>Guida di multi-geografico

Questo articolo descrive come è possibile creare un'area di lavoro e pubblicare un servizio web in altre aree di Azure.

## <a name="create-a-workspace"></a>Creare un'area di lavoro

1. Accedere al portale di classica Azure.

2.  Fare clic su **+ Nuovo** > **servizi dati** > **apprendimento** > **Crea rapido**.  In **percorso** selezionare un altro paese, ad esempio **Sudest asiatico**.
![Immagine con più geografico Guida 1][1]
3. Selezionare l'area di lavoro e quindi fare clic su **Accedi a ML Studio**.
![Immagine con più geografico Guida 2][2]

4. Ora è un'area di lavoro in un'altra area possono essere utilizzati come per qualsiasi dell'area di lavoro. Per passare tra le aree di lavoro, esaminare in alto a destra dello schermo. Fare clic sull'elenco a discesa, selezionare l'area e quindi selezionare l'area di lavoro. Tutte le informazioni sono locale all'area di lavoro; ad esempio tutti i servizi web creati da un'area di lavoro sarà nella stessa regione in che l'area di lavoro si trova.
![Immagine con più geografico Guida 3][3]

## <a name="open-an-experiment-from-gallery"></a>Aprire una prova dalla raccolta

Se si apre una prova dalla raccolta, è anche possibile selezionare quale area che si desidera copiare prova a.

![Immagine con più geografico Guida 4][4a]

## <a name="web-service-management"></a>Gestione del servizio Web

Per gestire a livello di programmazione servizi web, ad esempio per formazione, utilizzare l'indirizzo specifico del paese:
- https://asiasoutheast.Management.azureml.NET
- https://europewest.Management.azureml.NET

### <a name="things-to-note"></a>Notare quanto

1.  È possibile copiare solo esperimenti tra le aree di lavoro che appartiene alla stessa regione in questo modo. Se è necessario copiare prova nelle aree di lavoro in aree diverse, è possibile utilizzare il cmdlet di [PowerShell](http://aka.ms/amlps) [*Copia AmlExperiment*](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment) per eseguire questa operazione. Soluzione alternativa consiste nel pubblicare prova nella raccolta in modalità non in elenco, quindi aprirlo nell'area di lavoro dall'area di altri.
2.  Selettore di area verrà mostrati solo le aree di lavoro per una regione alla volta. In futuro, sarà possibile visualizzare un elenco completo delle aree di lavoro che è possibile accedere attraverso tutte le aree contemporaneamente.  
3.  Un gratuito area di lavoro o l'accesso Guest (anonimo) verrà creato e inserito in Sud centrale degli Stati Uniti In futuro, sarà possibile creare aree di lavoro Access Free/Guest nell'area che si è scelto.  
4.  Servizi Web distribuiti da un'area di lavoro in Sudest asiatico anche essere ospitati in Sudest asiatico. In futuro, sarà possibile hanno la possibilità di creazione di esperimenti in un'area e distribuzione generato endpoint del servizio web in aree geografiche diverse.  

## <a name="more-information"></a>Ulteriori informazioni

Porre una domanda nel [forum di apprendimento Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png
