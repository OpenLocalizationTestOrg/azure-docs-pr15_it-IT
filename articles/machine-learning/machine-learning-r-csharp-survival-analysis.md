<properties 
    pageTitle="Analisi di sopravvivenza con Azure apprendimento | Microsoft Azure" 
    description="Probabilità di occorrenza di sopravvivenza analisi evento" 
    services="machine-learning" 
    documentationCenter="" 
    authors="zhangya" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="zhangya"/> 


#<a name="survival-analysis"></a>Analisi di sopravvivenza 

In molti casi il risultato principale in valutazione è il tempo a un evento di interesse. In altre parole, la domanda "durante l'evento si verificherà?" è richiesto. Ad esempio tenere conto delle situazioni in cui i dati descrivono il tempo trascorso (giorni, anni, chilometraggio, ecc.) finché l'evento di interesse (relapse malattia, grado dottore di ricerca ricevuto o errori freno pad) si verifica. Ogni istanza nei dati rappresenta un oggetto specifico (pazienti, uno studente, un'auto e così via).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Questo [servizio web]( https://datamarket.azure.com/dataset/aml_labs/survivalanalysis) risponde alla domanda "Qual è la probabilità che si verifichi l'evento di interesse da n ora per oggetto x?" Grazie a un modello di analisi di sopravvivenza, questo servizio web consente agli utenti fornire dati per formare il modello e testarlo. Il tema principale della prova consiste nel modellare l'intervallo di tempo trascorso fino a quando non si verifica l'evento di interesse. 

>Questo servizio web potrebbe essere utilizzato dagli utenti – potenzialmente tramite un'app per dispositivi mobili, tramite un sito Web o anche in un computer locale, ad esempio. Tuttavia lo scopo del servizio web che funge da un esempio di utilizzo apprendimento Azure per creare servizi web nella parte superiore di codice R. Con poche righe di codice R e si fa clic sul pulsante all'interno di Azure Machine Learning Studio, è possibile creare con codice R una prova e pubblicato come un servizio web. Il servizio web può essere pubblicato in Azure Marketplace e consumato da utenti e dispositivi in tutto il mondo senza impostazioni infrastruttura dell'autore del servizio web.  

##<a name="consumption-of-web-service"></a>Consumo del servizio web

Lo schema di dati di input del servizio web viene visualizzato nella tabella seguente. Sei tipi di informazioni sono necessarie come input: i dati di training, verifica dei dati, ora di interesse, l'indice di "ora" dimensione, l'indice di dimensione "evento" e i tipi di variabile (continuo o fattore). Dati di training viene rappresentati con una stringa, in cui le righe sono separate da virgola e le colonne sono separate da punti e virgola. Il numero delle caratteristiche dei dati è flessibile. Tutti gli elementi nella stringa di input devono essere numerici. Nei dati di training, la dimensione "time" indica che il numero di unità di tempo (giorni, anni, chilometraggio, ecc.) trascorsi il punto di partenza di Studio (pazienti ricezione farmaco trattamento programmi, uno Studio dottore di ricerca iniziale degli studenti, un'auto iniziare a essere basata sulle risorse, ecc) finché l'evento di interesse (attesa restituzione per l'uso di droghe, lo studente ottenere il grado di dottore di ricerca, auto con errore freno tastiera ecc.) si verifica. La dimensione "evento" indica se l'evento di interesse si verifica alla fine di Studio. Un valore di "evento = 1" indica che si verifica l'evento di interesse al momento specificato dalla quota di "ora". "evento = 0" indica che l'evento di interesse non si è verificato al momento specificato da dimensione "time".

- trainingdata - una stringa di caratteri. Righe sono separate da virgola e le colonne sono separate da punti e virgola. Ogni riga include dimensione "time", "evento" dimensione e variabili previsioni.
- testingdata - una riga di dati che contiene le variabili di previsioni per un determinato oggetto.
- time_of_interest - il tempo trascorso di interesse n.
- index_time - l'indice di colonna della dimensione "time" (a partire da 1).
- index_event - l'indice di colonna della dimensione "evento" (a partire da 1).
- variable_types - una stringa di caratteri con un punto e virgola come separatori al suo interno. 0 rappresenta variabili continue e 1 rappresenta fattore variabili.


L'output è la probabilità di un evento che si verificano entro una data specifica. 

>Questo servizio, come ospitati in Azure Marketplace è un servizio di OData; Questi possono essere chiamati tramite POST o GET. 

Esistono diversi modi di utilizzo del servizio in modo automatico (è un'app riportato [di seguito](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx)). 

###<a name="starting-c-code-for-web-service-consumption"></a>Inizio del codice c# per l'utilizzo di servizi web:
    public class Input
    {
            public string trainingdata;
            public string testingdata;
            public string timeofinterest;
            public string indextime;
            public string indexevent;
            public string variabletypes;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { trainingdata = TextBox1.Text, testingdata = TextBox2.Text, timeofinterest = TextBox3.Text, indextime = TextBox4.Text, indexevent = TextBox5.Text, variabletypes = TextBox6.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }




Interpretazione del test è come indicato di seguito. Presupponendo che l'obiettivo dei dati consiste nel modellare il tempo trascorso fino al ritorno per l'uso di droghe per pazienti che hanno ricevuto uno dei programmi di due trattamento. L'output della finestra di servizio web è: per pazienti da 35 anni, visto precedente libbre di farmaco trattamento 2 volte, eseguendo il programma di trattamento tempo residenziale, con l'utilizzo di heroin e cocaine, la probabilità di restituzione per l'utilizzo di farmaco 95.64% in giorno 500.

##<a name="creation-of-web-service"></a>Creazione del servizio web

>Questo servizio web è stato creato con Azure apprendimento. Per una versione di valutazione gratuita, nonché introduttivi sulla creazione di esperimenti e [servizi web di pubblicazione](machine-learning-publish-a-machine-learning-web-service.md), vedere [azure.com/ml](http://azure.com/ml). Di seguito è una schermata di prova che ha creato il codice di esempio e servizio web per ciascuno dei moduli all'interno di prova.

All'interno di apprendimento Azure, una nuova prova vuoto è stato creato e due [Eseguire Script R] [ execute-r-script] moduli sono stati estratti nell'area di lavoro. Lo schema di dati creato con un semplice [Script R eseguire][execute-r-script], che definisce lo schema di dati di input per il servizio web. In questo modulo verrà collegato al secondo [Eseguire Script R] [ execute-r-script] modulo che principali lavoro. In questo modulo non pre-elaborazione dei dati, la compilazione del modello e le stime. Nel passaggio pre-elaborazione dei dati, i dati di input rappresentati da stringhe lungo viene trasformati e convertiti in un intervallo di dati. Nel passaggio modello predefinito, un pacchetto R esterno "survival_2.37 7.zip" prima di tutto è installato per effettuare analisi di sopravvivenza. Quindi viene eseguita la funzione "coxph" dopo un'attività di elaborazione dei dati della serie. I dettagli della funzione "coxph" per l'analisi di sopravvivenza possono essere letti da documentazione R. Nel passaggio stima un'istanza di test viene fornita nel modello di esperti con la funzione "surfit" e la curva di sopravvivenza per questa istanza di test viene visualizzato come variabile "curva". Infine, si ottiene la probabilità del tempo di interesse. 

###<a name="experiment-flow"></a>Flusso di prova:

![Provare il flusso][1]

####<a name="module-1"></a>Modulo 1:

    #Data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1”
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    
    # 0 - continuous; 1 -  factor
    variable_types="0;0;1;1"

    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)

    maml.mapOutputPort("sampleInput"); #send data to output port
    
####<a name="module-2"></a>Modulo 2:

    #Read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")

    # Preprocessing training data
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)

    # Preprocessing testing data
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))

    # Preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types

    # Necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)

    # Prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # Construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # Fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # Based on user input, find the event occurrence probability
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }

    #Pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##<a name="limitations"></a>Limitazioni

Questo servizio web può accettare solo valori numerici come variabili caratteristica (colonne). La colonna "evento" può richiedere solo valore 0 o 1. La colonna "ora" deve essere un numero intero positivo.

##<a name="faq"></a>Domande frequenti
Per le domande frequenti sul consumo del servizio web o la pubblicazione su Azure Marketplace, vedere [di seguito](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
