<properties 
    pageTitle="Accedere a set di dati con la raccolta di client Machine Learning Python | Microsoft Azure" 
    description="Installare e utilizzare la libreria di client Python per accedere e gestire dati di Azure apprendimento in modo sicuro da un ambiente Python locale." 
    services="machine-learning" 
    documentationCenter="python" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="huvalo;bradsev" />


#<a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Set di dati di Access con Python mediante la raccolta di client Azure Machine Learning Python 

L'anteprima della libreria di Microsoft Azure Machine Learning Python client possibile attivare l'accesso sicuro per il set di dati di Azure apprendimento da un ambiente Python locale e consente la creazione e gestione di set di dati in un'area di lavoro.

In questo argomento fornisce istruzioni su come:

* installare la libreria di Python risorse computer client 
* accedere e caricare set di dati, incluse le istruzioni su come ottenere l'autorizzazione per accedere a set di dati di Azure apprendimento dall'ambiente Python locale
*  accedere a set di dati intermedio da esperimenti
*  Utilizzare la libreria di client Python enumerare set di dati, per accedere a metadati, leggere il contenuto di un set di dati, creare nuovi set di dati e set di dati esistente

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

 
##<a name="prerequisites"></a>Prerequisiti

La raccolta di client Python verificata in ambienti seguenti:

 - Windows, Mac e Linux
 - Python 2.7, 3.3 e 3.4

Ha una dipendenza su pacchetti seguenti:

 - richieste di
 - Python dateutil
 - pandas

Si consiglia di utilizzare che una distribuzione Python quali [Anaconda](http://continuum.io/downloads#all) o [medio copertura](https://store.enthought.com/downloads/), quale accompagnato da Python IPython e tre pacchetti elencati sopra installata. Sebbene IPython non è obbligatoria, è un ambiente ideale per la modifica e la visualizzazione di dati in modo interattivo.


###<a name="installation"></a>Come installare la libreria di client Azure Machine Learning Python

La raccolta di client Azure Machine Learning Python deve essere installata anche per completare le attività descritte in questo argomento. È disponibile dall' [Indice pacchetto Python](https://pypi.python.org/pypi/azureml). Per eseguire l'installazione nel proprio ambiente Python, eseguire il comando seguente dall'ambiente Python locale:

    pip install azureml

In alternativa, è possibile scaricare e installare dalle origini sul [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Se si dispone fra installato nel computer in uso, è possibile utilizzare pip per installare direttamente dal repository fra:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


##<a name="datasetAccess"></a>Utilizzare frammenti di codice di Studio per accedere a set di dati

La raccolta di client Python permette l'accesso per il set di dati esistenti da esperimenti che sono stati eseguiti.

Dall'interfaccia web Studio, è possibile generare frammenti di codice che includono tutte le informazioni necessarie per scaricare e deserializzare set di dati come oggetti Pandas DataFrame nel computer in uso posizione.

### <a name="security"></a>Protezione per l'accesso ai dati

I frammenti di codice forniti da Studio per usarli con la raccolta di client Python includono l'id dell'area di lavoro e l'autorizzazione token. Questi accesso completo per l'area di lavoro e deve essere protetto, ad esempio una password.

Per motivi di sicurezza, le funzionalità di frammento di codice disponibile solo per gli utenti che hanno il proprio ruolo impostare come **proprietario** dell'area di lavoro. Il proprio ruolo viene visualizzato in Azure Machine Learning Studio nella pagina **utenti** in **Impostazioni**.

![Sicurezza][security]

Se il proprio ruolo non è impostato come **proprietario**, è possibile richiedere di invito come proprietario o chiedere al proprietario dell'area di lavoro per fornire il frammento di codice.

Per ottenere il token di autorizzazione, è possibile eseguire una delle operazioni seguenti:



- Richiedere un token da un proprietario. Proprietari accedere token di autorizzazione dalla pagina Impostazioni della propria area di lavoro in Studio. Selezionare **Impostazioni** nel riquadro di sinistra e scegliere **I token di autorizzazione** per visualizzare i token primari e secondari.  Sebbene primario o i token di autorizzazione secondario possono essere utilizzati nel frammento di codice, è consigliabile che i proprietari condividano solo i token di autorizzazione secondario.

![](./media/machine-learning-python-data-access/ml-python-access-settings-tokens.png)

- Chiedere di essere promossi a ruolo del proprietario.  A tale scopo, un proprietario corrente dell'area di lavoro è necessario prima rimuovere l'area di lavoro, quindi nuovamente invitati a tale come proprietario.

Dopo aver ottenuto gli sviluppatori l'id dell'area di lavoro e l'autorizzazione token, sono in grado di accedere l'area di lavoro utilizzando il frammento di codice indipendentemente dalla loro ruolo.

I token di autorizzazione vengono gestiti nella pagina **I token di autorizzazione** in **Impostazioni**. È possibile rigenerare, ma questa procedura revoca l'accesso al token precedente.

### <a name="accessingDatasets"></a>Set di dati di Access da un'applicazione di Python locale

1. Nel computer risorse Studio, fare clic su **set di dati** nella barra di spostamento a sinistra.

2. Selezionare il set di dati che si desidera accedere. È possibile selezionare uno dei set di dati dall'elenco di **Set di dati personali** o dall'elenco di **esempi** .

3. Sulla barra degli strumenti nella parte inferiore, fare clic su **Genera codice di accesso ai dati**. Se i dati in un formato compatibile con la raccolta di client Python, questo pulsante è disattivato.

    ![Set di dati][datasets]

4. Selezionare il frammento di codice nella finestra visualizzata e copiarlo negli Appunti.

    ![Codice di accesso][dataset-access-code]

5. Incollare il codice nel blocco appunti dell'applicazione Python locale.

    ![Blocco appunti][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Set di dati intermedio accesso da esperimenti di apprendimento

Dopo l'esecuzione di una prova di Studio di apprendimento computer, è possibile accedere ai set di dati intermedio dei nodi di output dei moduli. Set di dati intermedio sono dati che sono stati creati e usati per i passaggi intermedi quando uno strumento di modello è stato eseguito.

A condizione che sia compatibile con la raccolta di client Python il formato dei dati, è possibile accedere intermedio set di dati.

Sono supportati i formati seguenti (costanti per queste sono le `azureml.DataTypeIds` classe):

 - Testo normale
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader

È possibile determinare il formato, passare il mouse su un nodo di output modulo. Viene visualizzato con il nome del nodo, in una descrizione comando.

Alcuni dei moduli, ad esempio la [divisione] [ split] modulo di output in un formato denominato `Dataset`, non è supportato dalla libreria client Python.

![Formato di set di dati][dataset-format]

È necessario utilizzare un modulo di conversione, ad esempio [convertire in formato CSV][convert-to-csv], per ottenere un output in un formato supportato.

![Formato GenericCSV][csv-format]

La procedura seguente mostra un esempio che crea una prova, viene eseguito e accede il set di dati intermedio.

1. Creare una nuova prova.

2. Inserire un modulo di **set di dati adulto censimento reddito binario classificazione** .

3. Inserire una [divisione] [ split] modulo, il numero e connettersi input all'output modulo set di dati.

4. Inserire una [conversione in formato CSV] [ convert-to-csv] modulo e connettere l'input in una finestra [divisa] [ split] modulo genera.

5. Salvare la prova, eseguirlo e attendere il termine dell'esecuzione.

6. Fare clic su un nodo di output scegliere [Converti in formato CSV] [ convert-to-csv] modulo.

7. Quando viene visualizzato il menu di scelta rapida, selezionare **Genera codice di accesso ai dati**.

    ![Menu di scelta rapida][experiment]

8. Selezionare il frammento di codice e copiarlo negli Appunti dalla finestra visualizzata.

    ![Codice di accesso][intermediate-dataset-access-code]

9. Incollare il codice del blocco appunti.

    ![Blocco appunti][ipython-intermediate-dataset]

10. È possibile visualizzare i dati utilizzando matplotlib. Verrà visualizzato in un istogramma per la colonna Età:

    ![Istogramma][ipython-histogram]


##<a name="clientApis"></a>Utilizzare la libreria di client Machine Learning Python per accedere, leggere, creare e gestire set di dati

### <a name="workspace"></a>Area di lavoro

L'area di lavoro è il punto di ingresso per la raccolta di client Python. Fornire la `Workspace` token di classe con l'id dell'area di lavoro e l'autorizzazione per creare un'istanza:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Consente di enumerare set di dati

Enumerare tutti set di dati in un'area di lavoro assegnato:

    for ds in ws.datasets:
        print(ds.name)

Enumerare solo i set di dati creati dall'utente:

    for ds in ws.user_datasets:
        print(ds.name)

Enumerare solo i set di dati di esempio:

    for ds in ws.example_datasets:
        print(ds.name)

È possibile accedere a un set di dati in base al nome (ossia maiuscole e minuscole):

    ds = ws.datasets['my dataset name']

In alternativa, è possibile accedere in base all'indice:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadati

Set di dati associati metadati, oltre al contenuto. (Intermedio set di dati rappresentano un'eccezione a questa regola e non dispone di tutti i metadati).

Alcuni valori dei metadati vengono assegnate dall'utente al momento della creazione:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Altri sono valori assegnati da Azure ML:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Vedere la `SourceDataset` classe per altre informazioni su metadati disponibili.


### <a name="read-contents"></a>Leggere il contenuto

I frammenti di codice forniti da computer risorse Studio automaticamente scaricare e deserializzare il set di dati a un oggetto Pandas DataFrame. Questa operazione viene eseguita con la `to_dataframe` metodo:

    frame = ds.to_dataframe()

Se si preferisce scaricare i dati non elaborati ed eseguire la deserializzazione se stessi, che è un'opzione. Al momento, questa è l'unica opzione disponibile per i formati, ad esempio 'ARFF', non è possibile deserializzare la libreria di client Python.

Per leggere il contenuto come testo:

    text_data = ds.read_as_text()

Per leggere il contenuto in formato binario:

    binary_data = ds.read_as_binary()

È anche possibile aprire flusso al contenuto:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Creare un nuovo set di dati

La raccolta di client Python consente di caricare set di dati da un'applicazione di Python. Questi set di dati sono quindi disponibili per l'utilizzo nell'area di lavoro.

Se si dispone dei dati in un DataFrame Pandas, utilizzare il codice seguente:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Se i dati già serializzati, è possibile usare:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

La raccolta di client Python in grado di serializzare un DataFrame Pandas nei formati seguenti (costanti per queste sono le `azureml.DataTypeIds` classe):

 - Testo normale
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader


### <a name="update-an-existing-dataset"></a>Aggiornare un set di dati esistente

Se si tenta di caricare un nuovo set di dati con un nome che corrisponde a un set di dati esistente, viene visualizzato un errore di conflitto.

Per aggiornare un set di dati esistente, è necessario innanzitutto ottenere un riferimento per il set di dati esistente:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Usare quindi `update_from_dataframe` per serializzare e sostituire il contenuto del set di dati in Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Se si desidera serializzare i dati in un formato diverso, specificare un valore per facoltativa `data_type_id` parametro.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Facoltativamente, è possibile impostare una nuova descrizione specificando un valore per il `description` parametro.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Facoltativamente, è possibile impostare un nuovo nome, specificando un valore per il `name` parametro. In futuro recuperate il set di dati utilizzando il nuovo nome. Il codice seguente aggiorna dati, nome e descrizione.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

Il `data_type_id`, `name` e `description` parametri sono facoltativi e predefinito in base al valore precedente. Il `dataframe` parametro è sempre necessario.

Se i dati già serializzati, utilizzare `update_from_raw_data` anziché `update_from_dataframe`. Se si passa solo `raw_data` anziché `dataframe`, funziona in modo analogo.



<!-- Images -->
[security]:./media/machine-learning-python-data-access/security.png
[dataset-format]:./media/machine-learning-python-data-access/dataset-format.png
[csv-format]:./media/machine-learning-python-data-access/csv-format.png
[datasets]:./media/machine-learning-python-data-access/datasets.png
[dataset-access-code]:./media/machine-learning-python-data-access/dataset-access-code.png
[ipython-dataset]:./media/machine-learning-python-data-access/ipython-dataset.png
[experiment]:./media/machine-learning-python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/machine-learning-python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/machine-learning-python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/machine-learning-python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 
