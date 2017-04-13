<properties 
    pageTitle="Ricerca di scienze di dati in Linux dati scienza Virtual Machine | Microsoft Azure" 
    description="Informazioni su come eseguire le operazioni di ricerca di scienze dati diverse con la macchina virtuale scienza dati Linux." 
    services="machine-learning"
    documentationCenter="" 
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
    ms.author="bradsev;paulsh" />


# <a name="data-science-on-the-linux-data-science-virtual-machine"></a>Ricerca di scienze di dati in Linux dati scienza Virtual Machine

Questa procedura viene illustrato come eseguire diverse attività comuni di dati scienza con la macchina virtuale scienza dati Linux. Linux dati scienza macchina virtuale (DSVM) è un'immagine di macchina virtuale disponibile in Azure che sia già installato con un insieme di strumenti comuni per analitica dati e apprendimento. I componenti software chiave vengono classificati nell'argomento con [disposizione Linux dati scienza Virtual Machine](machine-learning-data-science-linux-dsvm-intro.md) . L'immagine di macchine Virtuali rende più facile per iniziare eseguendo scienza dati in minuti, senza dover installare e configurare gli strumenti singolarmente. È possibile facilmente scalare macchine Virtuali, se necessario e interromperlo quando non è in uso. Pertanto la risorsa è flessibile ed economico. 

Le attività di ricerca di scienze dati illustrata Segui questa procedura dettagliata i passaggi descritti nel [Processo di scienze dei dati del Team](https://azure.microsoft.com/documentation/learning-paths/data-science-process/). Questo processo fornisce un approccio sistematico alla ricerca di scienze di dati che consente ai team di scienziati dati per la collaborazione in modo efficace al ciclo di vita di creazione di applicazioni intelligente. Il processo di scienze dati fornisce anche un'infrastruttura iterativa per scienza dati che può essere seguita da una persona.

Abbiamo analizzare il set di dati [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) in questa procedura dettagliata. Si tratta di un insieme di messaggi di posta elettronica contrassegnato come posta indesiderata o prosciutto (ovvero non sono la posta indesiderata), e contiene anche alcune statistiche al contenuto dei messaggi di posta elettronica. Statistiche incluse sono descritti nella successiva ma una sezione. 


## <a name="prerequisites"></a>Prerequisiti

Prima di utilizzare una macchina virtuale di Linux dati scienza, è necessario disporre le operazioni seguenti:

- Un **abbonamento Azure**. Se non già presente, vedere [creare oggi l'account Azure gratuito](https://azure.microsoft.com/free/).
- Una [**ricerca di scienze dati Linux macchine Virtuali**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Per informazioni su questa macchina virtuale di provisioning, vedere [disposizione Linux dati scienza Virtual Machine](machine-learning-data-science-linux-dsvm-intro.md). 
- Aprire una sessione XFCE [X2Go](http://wiki.x2go.org/doku.php) perché installato nel computer. Per informazioni sull'installazione e configurazione di un **client X2Go**, vedere [installazione e configurazione di client X2Go](machine-learning-data-science-linux-dsvm-intro.md#Installing-and-configuring-X2Go-client). 
- Un **account AzureML**. Se non hai già uno, iscriversi a uno nuovo nella [home page di AzureML](https://studio.azureml.net/). Esiste un livello di utilizzo gratuito per iniziare.


## <a name="download-the-spambase-dataset"></a>Scaricare il set di dati spambase

Il set di dati [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) è un numero relativamente ridotto di dati che contiene solo 4601 esempi. Si tratta di una dimensioni centralizzata da utilizzare per dimostrare che alcune delle caratteristiche chiave di macchine Virtuali di scienze i dati, poiché mantiene i requisiti minimi.

>[AZURE.NOTE] In questa procedura dettagliata è stata creata in D2 dimensioni v2 Linux dati scienza Virtual Machine. Questa dimensione DSVM è in grado di gestire le procedure descritte in questa procedura dettagliata.

Se è necessario più spazio di archiviazione, è possibile creare ulteriori dischi e collegarli alle macchine Virtuali. Questi dischi utilizzano l'archivio di Azure permanente, in modo che i dati vengono mantenuti anche quando il server viene di nuovo a causa di ridimensionamento o viene chiuso. Per aggiungere un disco e collegarlo alle macchine Virtuali, seguire le istruzioni in [Aggiungi un disco a una macchina virtuale Linux](../virtual-machines/virtual-machines-linux-add-disk.md). Questa procedura utilizza l'interfaccia di riga di comando di Azure Azure CLI (), che è già installato nel DSVM. Così è possono eseguire queste procedure interamente da macchine Virtuali stesso. Un'altra opzione per aumentare lo spazio di archiviazione consiste nell'usare [file Azure](../storage/storage-how-to-use-files-linux.md).

Per scaricare i dati, aprire una finestra del terminale e il seguente comando:

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Il file scaricato non ha una riga di intestazione, verrà creato un altro file che dispone di un'intestazione. Eseguire questo comando per creare un file con intestazioni appropriate:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Quindi concatenare due file con il comando:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Il set di dati sono diversi tipi di statistiche in ogni messaggio di posta elettronica: 

- Ad esempio le colonne ***word\_frequenza\_WORD*** indicano la percentuale di parole in messaggio di posta elettronica che corrispondono a *WORD*. Ad esempio, se *word\_frequenza\_apportare* è 1, 1% di tutte le parole di messaggio di posta elettronica sono state *apportare*. 
- Ad esempio le colonne ***char\_frequenza\_CHAR*** indicano la percentuale di tutti i caratteri nel messaggio di posta elettronica che sono state *caratt*. 
- ***capitale\_eseguire\_lunghezza\_più lunga*** è la durata più lunga di una sequenza di lettere maiuscole. 
- ***capitale\_eseguire\_lunghezza\_Media*** è la lunghezza media di tutte le sequenze delle lettere maiuscole. 
- ***capitale\_eseguire\_lunghezza\_totale*** è la lunghezza totale di tutte le sequenze delle lettere maiuscole. 
- ***posta indesiderata*** indica se il messaggio è stato considerato posta indesiderata o meno (1 = posta indesiderata, 0 = non posta indesiderata).


## <a name="explore-the-dataset-with-microsoft-r-open"></a>Esplorare il set di dati con Microsoft R aperto

Di seguito esaminare i dati ed eseguire alcune base apprendimento con R. Macchine Virtuali di scienze dati viene fornito con [Microsoft R Apri](https://mran.revolutionanalytics.com/open/) preinstallato. Le librerie matematiche multithreading in questa versione di R offrono prestazioni migliori rispetto alle versioni multithread diversi. Microsoft R aperto vengono forniti anche riproducibilità utilizzando uno snapshot dell'archivio pacchetto CRAN.

Per ottenere copie degli esempi di codice utilizzati in questa procedura dettagliata, clonare repository **Azure-Machine-Learning-dati-scienza** utilizzando operazioni, che sia già installato nella macchina virtuale. Dalla riga di comando fra eseguire:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Aprire una finestra del terminale e avviare una nuova sessione di R con la console interattiva R.

>[AZURE.NOTE] È anche possibile utilizzare RStudio per le procedure seguenti. Per installare RStudio, eseguire il comando presso un terminale:`./Desktop/DSVM\ tools/installRStudio.sh`

Per importare i dati e configurare l'ambiente, eseguire:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Per visualizzare le statistiche di riepilogo relativi a ogni colonna:

    summary(data)

Per una visualizzazione diversa dei dati:

    str(data)

Seguente illustra il tipo di ogni variabile e prima alcuni valori nel set di dati. 

La colonna *posta indesiderata* è stato letto come numero intero, ma è effettivamente una categoria variabile (o fattore). Per impostare il tipo:

    data$spam <- as.factor(data$spam)

Per eseguire un'analisi esplorativo, utilizzare il pacchetto di [ggplot2](http://ggplot2.org/) , una raccolta di grafica più diffuso per R è già installato nella macchina virtuale. Si noti dai dati di riepilogo visualizzati in precedenza, sono disponibili statistiche di riepilogo sulla frequenza del carattere punto esclamativo. Di seguito tracciare tali frequenza con i comandi seguenti:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Poiché zero barra è inclinazione il tracciato, si rimuovono è:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Esiste una densità non semplice sopra 1 che ha un aspetto interessante. Diamo un'occhiata solo i dati:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Quindi dividerla da prosciutto vs posta indesiderata:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

In questi esempi consentono di rendere simili posti delle altre colonne per esplorare i dati contenuti nel loro.


## <a name="train-and-test-an-ml-model"></a>Organizzare la formazione e testare un modello di ML

A questo punto si formare un paio di modelli di apprendimento machine per classificare i messaggi di posta elettronica nel set di dati come contenente intervallo o prosciutto. Abbiamo formare un modello e un modello di foresta casuale in questa sezione e quindi verificare la precisione delle stime. 

>[AZURE.NOTE] Il pacchetto di rpart (ricorsiva partizioni e le strutture di regressione) utilizzato nel codice riportato di seguito è già installato in macchine Virtuali di scienze dati.


Prima, è possibile divisa il set di dati in set di test e formazione:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

E quindi si crea un albero decisionale per classificare i messaggi di posta elettronica.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Ecco il risultato:

![1](./media/machine-learning-data-science-linux-dsvm-walkthrough/decision-tree.png)

Per determinare sulle prestazioni sul set di formazione, utilizzare il codice seguente:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Per determinare sulle prestazioni sul set di test:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Provare anche un modello di foresta casuali. Insiemi casuali formare una vasta gamma di decisionali e generare l'output di una classe è la modalità di classificazione da tutte le strutture di decisione individuale. Offrono un computer più potente formazione approccio quando sono corretti per la tendenza di un modello di albero decisionale per overfit un set di dati di formazione. 

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Distribuire un modello a ML Azure

[Azure apprendimento Studio](https://studio.azureml.net/) (AzureML) è un servizio cloud che consente di compilare e distribuire modelli di previsione analitica facilmente. Uno dei lati positivi di AzureML è la possibilità di pubblicare qualsiasi funzione R come servizio web. Il pacchetto AzureML R semplice distribuzione direttamente dalla sessione R sul DSVM. 

Per distribuire il codice di albero decisionale dalla sezione precedente, è necessario effettuare l'accesso a Azure Machine Learning Studio. È necessario l'ID dell'area di lavoro e un token di autorizzazione a sigh in. Per trovare questi valori e inizializzare le variabili di AzureML con loro:

Scegliere **Impostazioni** dal menu sinistro. Nota l' **ID dell'area di lavoro**. ![2](./media/machine-learning-data-science-linux-dsvm-walkthrough/workspace-id.png)

Selezionare **I token di autorizzazione** dal menu generali e prendere nota del **Token di autorizzazione principale**. ![3](./media/machine-learning-data-science-linux-dsvm-walkthrough/workspace-token.png)

Caricare il pacchetto **AzureML** e quindi impostare i valori delle variabili con il proprio ID token e area di lavoro nella sessione R il DSVM:


    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Di seguito semplificare il modello per facilitare la dimostrazione implementare. Selezionare le tre variabili decisionale più vicino alla radice e creare una nuova struttura usando solo le tre variabili:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Occorre una funzione di completamento che utilizza le caratteristiche come input e restituisce i valori previsti:

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

Pubblicare la funzione predictSpam AzureML utilizzando la funzione **publishWebService** : 

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

Questa funzione utilizza la funzione **predictSpam** , viene creato un servizio web denominato **spamWebService** con input definito e output e restituisce informazioni sul nuovo endpoint.

Visualizzare i dettagli del servizio web pubblicato, inclusi il punto finale dell'API e i tasti con il comando:

    spamWebService[[2]]

Per provare il primo set di 10 righe del test:

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## <a name="use-other-tools-available"></a>Usare altri strumenti disponibili

Le sezioni rimanente viene illustrato come utilizzare alcuni degli strumenti installati nella macchina virtuale di scienze dati Linux. Ecco l'elenco degli strumenti descritte:

- XGBoost
- Python
- Jupyterhub
- Sonagli
- PostgreSQL & esce SQL
- Data Warehouse SQL Server


## <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) è uno strumento che fornisce un'implementazione veloce e accurato albero invece un incremento.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

XGBoost inoltre possibile chiamare da python o una riga di comando.

## <a name="python"></a>Python

Per lo sviluppo tramite Python, le distribuzioni di Anaconda Python 2.7 e 3.5 state installate il DSVM. 

>[AZURE.NOTE] La distribuzione Anaconda include [Condas](http://conda.pydata.org/docs/index.html), che possono essere utilizzati per creare ambienti personalizzati per Python che dispongono di versioni diverse e/o pacchetti installati.

Di seguito in alcuni dei set di dati spambase e classificare i messaggi di posta elettronica con i computer vettore supporto scikit-informazioni su:

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Per rendere le stime:

    clf.predict(X.ix[0:20, :])

Per mostrare come pubblicare un endpoint AzureML, questo punto, eseguire un modello più semplice tre variabili come è stato fatto quando il modello R pubblicato in precedenza. 

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Per pubblicare il modello per AzureML:

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)

>[AZURE.NOTE] Questo è disponibile solo per python 2.7 e non è ancora supportato in 3.5. Eseguire con **/anaconda/bin/python2.7**.


## <a name="jupyterhub"></a>Jupyterhub

La distribuzione di Anaconda nel DSVM viene fornito con un blocco appunti Jupyter, un ambiente multipiattaforma condividere analisi e il codice Python, R o Julia. Il blocco appunti Jupyter per accedere a JupyterHub. Accedere usando il nome utente Linux locale e la password in ***https://\<VM DNS nome o indirizzo IP\>: 8000 /***. Tutti i file di configurazione per JupyterHub sono disponibili nella directory **/etc/jupyterhub**.

Diversi esempi di blocchi appunti sono già installate nella macchina virtuale:

- Vedere [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) per un blocco appunti Python di esempio.
- Vedere [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) per un blocco appunti **R** di esempio.
- Vedere [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) per un altro blocco appunti **Python** di esempio.

>[AZURE.NOTE] La lingua Julia disponibile anche dalla riga di comando nella macchina virtuale di scienze dati Linux.


## <a name="rattle"></a>Sonagli

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (la R Analytical strumento per informazioni su facilmente) è uno strumento R grafico per il data mining. Ha un'interfaccia intuitiva che semplifica l'individuazione di caricare, esplorare e trasformare i dati e creare e valutare i modelli.  L'articolo [Rattle: A dati Mining grafica per R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) fornisce una procedura dettagliata che illustra le funzionalità.

Installare e avviare sonagli con i comandi seguenti:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

>[AZURE.NOTE] Installazione non è necessaria nel DSVM. Ma sonagli può essere richiesto di installare pacchetti aggiuntivi durante il caricamento.

Sonaglio utilizza un'interfaccia basata su scheda. La maggior parte delle schede corrispondono ai passaggi del [Processo di scienze dei dati](https://azure.microsoft.com/documentation/learning-paths/data-science-process/), ad esempio il caricamento dei dati o esplorazione. Flusso del processo di scienze di dati da sinistra a destra tra le schede. Ma ultima scheda contiene un file di log dei comandi R mostrarli sonagli. 


Per caricare e configurare il set di dati:

- Per caricare il file, selezionare la scheda **dati** , quindi 
- Scegliere il selettore accanto al **nome del file** e quindi **spambaseHeaders.data**. 
- Per caricare il file. Selezionare **Esegui** nella riga superiore dei pulsanti. Verrà visualizzato un riepilogo di ogni colonna, incluso il tipo di dati identificato, sia che si tratti di input, un valore di destinazione o altro tipo di variabile e il numero di valori univoci.
- Sonaglio è correttamente individuato la colonna **posta indesiderata** con quello di destinazione. Selezionare la colonna posta indesiderata, quindi impostare il **Tipo di dati di destinazione** su **Categoric**.

Per esplorare i dati: 

- Selezionare la scheda **Esplora** . 
- Fare clic su **Riepilogo**, quindi **Esegui**per vedere alcune informazioni sui tipi di variabili e alcune statistiche di riepilogo. 
- Per visualizzare altri tipi di statistiche relative a ogni variabile, selezionare altre opzioni **sono descritti** o **concetti di base**.

La scheda **Esplora** consente anche di generare numerosi grafici dettagliati. Per tracciare un istogramma dei dati:


- Selezionare **le distribuzioni**.
- Cercare **istogramma** **word_freq_remove** e **word_freq_you**.
- Selezionare **Esegui**. Verrà visualizzato entrambi posti densità in un'unica area grafica, in cui sia chiaramente che la parola "è" viene visualizzato molto più spesso in messaggi di posta elettronica che "Rimuovi".

Posti correlazione sono inoltre interessante. Per creare una:


- Scegliere **la correlazione** come **tipo**, quindi 
- Selezionare **Esegui**. 
- Sonaglio avvisa si consiglia di un massimo di 40 variabili. Selezionare **Sì** per visualizzare il tracciato. 

Esistono alcune interessanti correlazioni visualizzate: "technology" è strettamente correlato alla "HP" e "esercitazioni", ad esempio. Inoltre strettamente è correlato alla "650", poiché il prefisso dei donatori set di dati è 650.

I valori numerici per correlazioni tra le parole sono disponibili nella finestra Esplora. È interessante nota, ad esempio, che "technology" negativamente correlato con "uno" e "denaro".

Sonaglio trasformare il set di dati per gestire alcuni problemi comuni. Ad esempio, è possibile modificare la scala caratteristiche, imputare valori mancanti, gestire valori erratici e rimuovere variabili o osservazioni con dati mancanti. Sonaglio inoltre possibile identificare le regole di associazione tra osservazioni e/o variabili. Queste schede rientrano nell'ambito di questa procedura dettagliata introduttiva.

Sonaglio può anche eseguire analisi di cluster. Di seguito escludere alcune funzionalità per migliorare la leggibilità l'output. Nella scheda **dati** scegliere **Ignora** accanto a ciascuna delle variabili ad eccezione di questi dieci elementi:

- word_freq_hp
- word_freq_technology
- word_freq_george
- word_freq_remove
- word_freq_your
- word_freq_dollar
- word_freq_money
- capital_run_length_longest
- word_freq_business
- posta indesiderata

Quindi passare alla scheda **Cluster** scegliere **KMeans**e impostare il *numero di cluster* a 4. **Eseguire**. I risultati vengono visualizzati nella finestra di output. Un cluster ha ad alta frequenza di "Giorgio" e "hp" ed è probabilmente un messaggio di posta elettronica aziendale legittimi.

Per creare un modello di apprendimento machine albero decisionale: 

- Selezionare la scheda **modello** 
- Scegliere **struttura** come **tipo**. 
- Selezionare **Esegui** per visualizzare l'albero sotto forma di testo nella finestra di output. 
- Selezionare il pulsante **disegno** per visualizzare una versione con interfaccia grafica. Questo è simile alla struttura che abbiamo ottenuto in precedenza mediante *rpart*.

Uno dei lati positivi di sonagli è la possibilità di eseguire diversi metodi di risorse di computer e valutarli rapidamente. Ecco la procedura:

- Per il **tipo**, scegliere **tutti** . 
- Selezionare **Esegui**. 
- Al termine è possibile fare clic su qualsiasi singolo **tipo**, ad esempio **SVM**e visualizzare i risultati. 
- È inoltre possibile confrontare le prestazioni dei modelli nella convalida set mediante la scheda **valuta** . Ad esempio, la selezione di **Una matrice di errore** viene la matrice confusione, errore generale ed errore Media della classe per ogni modello sul set di convalida. 
- È anche possibile tracciare curve ROC, eseguire l'analisi di sensibilità ed eseguire altri tipi di valutazioni del modello.

Dopo aver terminato la creazione di modelli, selezionare la scheda **Log** per visualizzare il codice R mostrarli sonagli durante la sessione. È possibile selezionare il pulsante **Esporta** per salvarlo. 

>[AZURE.NOTE] Nella versione corrente di sonagli c'è un errore. Per modificare lo script o usarlo per ripetere i passaggi in un secondo momento, è necessario inserire un carattere # davanti *esportare il registro …* nel testo del registro. 


## <a name="postgresql--squirrel-sql"></a>PostgreSQL & esce SQL

Il DSVM viene fornito con PostgreSQL installato. PostgreSQL è un database relazionale sofisticato, Apri origine. In questa sezione viene illustrato come caricare il set di dati di posta indesiderata in PostgreSQL e quindi eseguire una query.

È possibile caricare i dati, è necessario consentire l'autenticazione tramite password dall'host locale. Al prompt dei comandi:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Nella parte inferiore del file di configurazione sono diverse righe che ne illustrano le connessioni consentite:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Modificare la riga "Connessioni locale IPv4" per utilizzare md5 anziché ident, in modo che la possibilità di accedere con un nome utente e password:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Riavviare il servizio postgres:

    sudo systemctl restart postgresql

Per avviare psql, un terminale interattivo per PostgreSQL come utente postgres incorporato, eseguire il seguente comando da un prompt di:

    sudo -u postgres psql

Creare un nuovo account utente, con lo stesso nome utente dell'account Linux attualmente effettuato l'accesso come e assegnargli una password:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Collegarsi a psql come l'utente:

    psql

E importare i dati in un nuovo database:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

A questo punto, esaminiamo i dati ed eseguire alcune query **SQL esce**, uno strumento grafico che consente di interagire con i database tramite un driver JDBC.

Per iniziare, avviare SQL esce dal menu applicazioni. Per impostare il driver:

- Selezionare **Windows**, quindi **driver di visualizzazione**. 
- Fare clic su **PostgreSQL** e selezionare **Il Driver della modifica**. 
- Selezionare **il percorso della classe aggiuntivi**, quindi **Aggiungi**. 
- Immettere il **nome del File** ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** e 
- Scegliere **Apri**.
- Scegliere il driver di elenco, quindi selezionare **org.postgresql.Driver** nel **Nome della classe**e fare clic su **OK**.

Per configurare la connessione al server locale:
 
- Selezionare **Windows**, quindi **consente di visualizzare gli alias.** 
- Scegliere la **+** pulsante per rendere un nuovo alias. 
- Nome *database della posta indesiderata*, scegliere **PostgreSQL** nell'elenco a discesa **Driver** .
- Impostare l'URL per *jdbc:postgresql://localhost/spam*. 
- Immettere il *nome utente* e la *password*. 
- Fare clic su **OK**. 
- Per aprire la finestra di **connessione** , fare doppio clic su alias ***del database della posta indesiderata*** . 
- Selezionare **la connessione**.

Per eseguire alcune query:

- Selezionare la scheda **SQL** .
- Inserire una query semplice, ad esempio `SELECT * from data;` nella casella di testo query nella parte superiore della scheda SQL. 
- Premere **Ctrl + Invio** per eseguirlo. Per impostazione predefinita SQL esce restituisce le prime 100 righe dalla query. 

Sono disponibili molte altre query che è possibile eseguire per esplorare i dati. Ad esempio, come la frequenza di parola *apportare* si differenza tra la posta indesiderata e prosciutto?

    SELECT avg(word_freq_make), spam from data group by spam;

O quali sono le caratteristiche di posta elettronica contenenti spesso *3d*?

    SELECT * from data order by word_freq_3d desc;

La maggior parte dei messaggi di posta elettronica contenenti un'elevato occorrenza *3d* sono apparentemente protezione da posta indesiderata, in modo che sia una caratteristica utile per la creazione di un modello di stima per classificare i messaggi di posta elettronica.

Se si desidera eseguire apprendimento con i dati archiviati in un database PostgreSQL, è preferibile [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>Data Warehouse SQL Server

Azure SQL Data Warehouse è un database basato su cloud, scalabilità in grado di elaborare grandi quantità di dati, non relazionali e relazionali. Per ulteriori informazioni, vedere [Novità Azure SQL Data Warehouse?](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Per connettersi al data warehouse e creare la tabella, eseguire il seguente comando da un prompt dei comandi:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Quindi al prompt dei comandi sqlcmd:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Copiare i dati con bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

>[AZURE.NOTE] Le terminazioni di riga del file scaricato sono stile di Windows, ma bcp prevede stile UNIX, in modo che occorre fornire bcp che con il flag - r.

E query con sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

È anche possibile eseguire query con esce SQL. Seguire una procedura simile per PostgreSQL utilizza Microsoft MSSQL Server JDBC Driver, che può trovarsi in ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica degli argomenti che guidano l'utente attraverso le attività che costituiscono il processo di scienze di dati di Azure, vedere [Processo di scienze dei dati del Team](http://aka.ms/datascienceprocess).

Per una descrizione delle altre procedure dettagliate-to-end che illustrano i passaggi del processo di scienze dei dati di Team per scenari specifici, vedere [procedure dettagliate processo scienza dei dati del Team](data-science-process-walkthroughs.md). Le procedure dettagliate anche illustrano come combinare gli strumenti cloud e in locale e servizi in un flusso di lavoro o pipeline per creare un'applicazione intelligente.

