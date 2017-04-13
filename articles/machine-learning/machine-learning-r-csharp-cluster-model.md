<properties 
    pageTitle="Cluster modello | Microsoft Azure" 
    description="Modello di cluster" 
    services="machine-learning" 
    documentationCenter="" 
    authors="FrancescaLazzeri" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="lazzeri"/> 


#<a name="cluster-model"></a>Modello di cluster    

Come è possibile prevedere gruppi di comportamenti titolari carta di credito per ridurre il rischio ammortamento della carta di credito banche? Come possiamo è definire gruppi di caratteristiche personalità dei dipendenti al fine di migliorare le prestazioni in ufficio? Come medici possano classificare pazienti in gruppi basati sulle caratteristiche delle loro malattie? In generale, tutte queste domande possono rispondere attraverso l'analisi del cluster.   


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 
   
Analisi cluster classifica un insieme di osservazioni in due o più si escludono a vicenda sconosciuti gruppi in base alle combinazioni di variabili. Lo scopo di analisi cluster è per individuare un sistema di organizzare le osservazioni, in genere persone o le caratteristiche, in gruppi, in cui i membri dei gruppi condividono proprietà. Questo [servizio](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) utilizza la metodologia K-medie, una tecnica cluster usata di frequente, ai dati non autorizzato cluster in gruppi. Questo servizio web accetta i dati e il numero di k cluster come input e produce le stime dei quali i gruppi di k a cui appartiene ogni osservazioni. 

>Questo servizio web potrebbe essere utilizzato dagli utenti – potenzialmente tramite un'app per dispositivi mobili, tramite un sito Web o anche in un computer locale, ad esempio. Tuttavia lo scopo del servizio web che funge da un esempio di utilizzo apprendimento Azure per creare servizi web nella parte superiore di codice R. Con poche righe di codice R e si fa clic sul pulsante all'interno di Azure Machine Learning Studio, è possibile creare con codice R una prova e pubblicato come un servizio web. Il servizio web può essere pubblicato in Azure Marketplace e consumato da utenti e dispositivi in tutto il mondo senza impostazioni infrastruttura dell'autore del servizio web.  

##<a name="consumption-of-web-service"></a>Consumo del servizio web   
Questo servizio web Raggruppa i dati in un set di gruppi k e visualizza l'assegnazione del gruppo per ogni riga. Il servizio web prevede l'utente finale di immettere dati come una stringa in righe da una virgola (,) e le colonne sono separate da punti e virgola (;). Il servizio web prevede 1 riga alla volta. Un set di dati di esempio Impossibile simile al seguente:

![Dati di esempio][1]

Si supponga che si desidera separare dati in 3 gruppi si escludono a vicenda. I dati di input per il set di dati indicati sarebbe le operazioni seguenti: valore = "10; 5; 2,18; 1; 6,7; 5; 5,22; 3; 4,12; 2; 1,10; 3; 4"; k = "3". L'output è l'appartenenza al gruppo previste per ognuna delle righe.

>Questo servizio, come ospitati in Azure Marketplace è un servizio di OData; Questi possono essere chiamati tramite POST o GET. 

Esistono diversi modi di utilizzo del servizio in modo automatico (è un'app riportato [di seguito](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Inizio del codice c# per l'utilizzo di servizi web:

    public class Input
    {
            public string value;
            public string k;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { value = TextBox1.Text, k = TextBox2.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }




##<a name="creation-of-web-service"></a>Creazione del servizio web  
>Questo servizio web è stato creato con Azure apprendimento. Per una versione di valutazione gratuita, nonché introduttivi sulla creazione di esperimenti e [servizi web di pubblicazione](machine-learning-publish-a-machine-learning-web-service.md), vedere [azure.com/ml](http://azure.com/ml). Di seguito è una schermata di prova che ha creato il codice di esempio e servizio web per ciascuno dei moduli all'interno di prova.

All'interno di apprendimento Azure, una nuova prova vuoto è stato creato e due [Eseguire Script R] [ execute-r-script] moduli inseriti nell'area di lavoro. Lo schema di dati creato con un semplice [Script R eseguire][execute-r-script]. Quindi, lo schema dei dati è stato collegato alla sezione modello cluster nuovamente creata con uno [Script R eseguire][execute-r-script]. In [Esegui Script R] [ execute-r-script] utilizzato per il modello, il servizio web utilizza quindi la funzione "k-medie", ossia predefiniti disponibili in [Esecuzione Script R] [ execute-r-script] di apprendimento Azure.    
   

     
![Flusso di prova][3]

####<a name="module-1"></a>Modulo 1: 
    #Enter the input data as a string 
    mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
    
    maml.mapOutputPort("mydata");     
    

####<a name="module-2"></a>Modulo 2:
    # Map 1-based optional input ports to variables
    mydata <- maml.mapInputPort(1) # class: data.frame

    data.split <- strsplit(mydata[1,1], ",")[[1]]
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- as.data.frame(t(data.split))

    data.split <- data.matrix(data.split)
    data.split <- data.frame(data.split)

    # K-Means cluster analysis
    fit <- kmeans(data.split, mydata$k) # k-cluster solution

    # Get cluster means 
    aggregate(data.split,by=list(fit$cluster),FUN=mean)
    # Append cluster assignment
    mydatafinal <- data.frame(t(fit$cluster))
    n_col=ncol(mydatafinal)
    colnames(mydatafinal) <- paste("V",1:n_col,sep="")

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("mydatafinal");
   
 
##<a name="limitations"></a>Limitazioni
Si tratta di un esempio molto semplice di un servizio web cluster. Nessun errore intercettazione implementato come può essere visualizzato da quello dell'esempio precedente, e il servizio presuppone che tutto sia una variabile di tipo continuo (nessuna funzionalità per categoria consentita), come i servizio solo input valori numerici al momento della creazione di questo servizio web. Inoltre, il servizio attualmente gestisce dimensioni dati limitati, scadenza alla natura richiesta/risposta della chiamata del servizio web e al fatto che il modello viene contenuto ogni volta che si chiama il servizio web. 

##<a name="faq"></a>Domande frequenti
Per le domande frequenti sul consumo del servizio web o la pubblicazione su Azure Marketplace, vedere [di seguito](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
