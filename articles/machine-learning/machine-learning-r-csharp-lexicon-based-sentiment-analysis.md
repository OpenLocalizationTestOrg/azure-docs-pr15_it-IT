<properties 
    pageTitle="Libreria di oggetti in base a analisi di valutazione in | Microsoft Azure" 
    description="Libreria di oggetti in base a valutazione in analisi" 
    services="machine-learning" 
    documentationCenter="" 
    authors="pengxia" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/16/2016" 
    ms.author="pengxia"/> 



#<a name="lexicon-based-sentiment-analysis"></a>Libreria di oggetti in base a valutazione in analisi 

Come si misura opinioni degli utenti e tempestivo verso marchi o negli argomenti di social network online, ad esempio Facebook pubblica, TWEET, revisioni e così via.? Analisi di valutazione in fornisce un metodo per analizzare tali domande.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

In genere sono disponibili due metodi per l'analisi di valutazione in. Uno utilizza un algoritmo di risorse controllate e gli altri possono essere considerato risorse senza supervisione. Un algoritmo di risorse controllate in genere crea un modello di classificazione in un grande corpus con annotazioni. La precisione è principalmente sulla base della qualità di tale annotazione e in genere il processo di formazione richiederà molto tempo. Oltre che, quando si applica l'algoritmo a un altro dominio, il risultato non è in genere buono. Confrontare controllati apprendimento, in base a dizionario utilizza risorse senza supervisione un dizionario di valutazione in, che non richieda l'archiviazione di un corpus grandi quantità di dati e risorse di formazione: rendendo l'intero processo molto più veloce. 

Il [servizio](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) si basa sul dizionario soggettività MPQA (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/), che corrisponde a uno dei dizionari soggettività più comunemente usati. 5097 negative e 2533 positive parole presenti MPQA. E tutte le parole vengono contrassegnate come polarità forte o debole. Intero corpus si trova sotto GNU General Public License. Per le frasi breve, ad esempio TWEET e tutti i post Facebook, è possibile utilizzare il servizio web. 

>Questo servizio web può essere utilizzato da utenti – potenzialmente tramite un'app per dispositivi mobili, tramite un sito Web o anche in un computer locale, ad esempio. Tuttavia lo scopo del servizio web che funge da un esempio di utilizzo apprendimento Azure per creare servizi web nella parte superiore di codice R. Con poche righe di codice R e si fa clic sul pulsante all'interno di Azure Machine Learning Studio, è possibile creare con codice R una prova e pubblicato come un servizio web. Il servizio web può essere pubblicato in Azure Marketplace e consumato da utenti e dispositivi in tutto il mondo senza impostazioni infrastruttura dell'autore del servizio web.

##<a name="consumption-of-web-service"></a>Consumo del servizio web

I dati di input possono essere qualsiasi testo, ma il servizio web funziona meglio con breve frasi. Il risultato è un valore numerico compreso tra -1 e 1. Qualsiasi valore inferiore a 0 indica che la valutazione in del testo è negativo; Se è positivo maggiore di 0. Il valore assoluto del risultato indica la forza della funzione di ciascun associato. 

>Questo servizio, come ospitati in Azure Marketplace è un servizio di OData; Questi possono essere chiamati tramite POST o GET. 

Esistono diversi modi di utilizzo del servizio in modo automatico (è un'app riportato [di seguito](http://microsoftazuremachinelearning.azurewebsites.net/)).

###<a name="starting-c-code-for-web-service-consumption"></a>Inizio del codice c# per l'utilizzo di servizi web:

    public class ScoreResult
    {
            [DataMember]
            public double result
            {
                get;
                set;
            }
    }

    void main()
    {
            using (var wb = new WebClient())
            {
                var acitionUri = new Uri("PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score");
                DataServiceContext ctx = new DataServiceContext(acitionUri);
                var cred = new NetworkCredential("PutEmailAddressHere", "ChangeToAPIKey");
                var cache = new CredentialCache();
    
                cache.Add(acitionUri, "Basic", cred);
                ctx.Credentials = cache;
                var query = ctx.Execute<ScoreResult>(acitionUri, "POST", true, new BodyOperationParameter("Text", TextBox1.Text));
                ScoreResult scoreResult = query.ElementAt(0);
                double result = scoreResult.result;
            }
    }



L'input è "Oggi è un buon giorno". L'output è "1", che indica una valutazione in positivo associato con la frase input. 

##<a name="creation-of-web-service"></a>Creazione del servizio web
>Questo servizio web è stato creato con Azure apprendimento. Per una versione di valutazione gratuita, nonché introduttivi sulla creazione di esperimenti e [servizi web di pubblicazione](machine-learning-publish-a-machine-learning-web-service.md), vedere [azure.com/ml](http://azure.com/ml). Di seguito è una schermata di prova che ha creato il codice di esempio e servizio web per ciascuno dei moduli all'interno di prova.


All'interno di apprendimento Azure, è stato creato un nuovo prova vuoto. Nella figura seguente è illustrato il flusso di prova di analisi di valutazione in base dizionario. Il file "sent_dict.csv" è il dizionario di soggettività MPQA e impostare un tipo di input di [Eseguire Script R][execute-r-script]. Un altro input è una revisione campionata dal set di dati revisione Amazon per test, in cui è eseguita la selezione, modificarne il nome di colonna e dividere le operazioni. Viene utilizzato un pacchetto di hash per archiviare il dizionario soggettività nella memoria e accelerare il processo di calcolo di punteggio. L'intero testo verrà suddiviso in token dal pacchetto "tm" e confrontato con la parola nel dizionario di valutazione in. Infine, un punteggio verrà calcolato aggiungendo dello spessore di ogni parola soggettiva nel testo. 

###<a name="experiment-flow"></a>Flusso di prova:

![Provare il flusso][2]


####<a name="module-1"></a>Modulo 1:
    
    # Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
 
   # <a name="install-hash-package"></a>Installare il pacchetto di hash
    install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE)
    library(tm)
    library(stringr)

    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)

    #  Compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
    polarity_ratio <- 0
    polarity_total <- 0
    not <- 0
    sentence <- tolower(dataset2[m,1])
    if (nchar(sentence) > 0){
        token_array <- scan_tokenizer(sentence)
        for (j in 1:length(token_array)){
            word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
            for (k in 1:length(negation_word)){
              if (word == negation_word[k]){
                not <- (not+1) %% 2

              }
            }
            if (word != ""){
                if (!is.null(sent_dict[[word]])){
                  polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
                  polarity_total <- polarity_total + abs(sent_dict[[word]])
                }
            }
          
        }
    }
    if (polarity_total > 0){
        result <- c(result, polarity_ratio/polarity_total)
    }else{
        result<- c(result,0)
    }
    }

    # Sample operation
    data.set <- data.frame(result)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")
    


##<a name="limitations"></a>Limitazioni

Dal punto di vista algoritmo analisi di valutazione in base dizionario è uno strumento di analisi di valutazione in generale, non può offrire prestazioni migliori rispetto al metodo di classificazione per campi specifici. Il problema di negazione non è ben gestito con. Abbiamo hardcoded negazione diverse parole nostro programma, ma una soluzione migliore usando un dizionario di negazione e creare alcune regole. Il servizio web prestazioni migliori su frasi brevi e semplici, ad esempio TWEET e tutti i post Facebook, più su frasi lunghe e complesse, ad esempio recensioni Amazon. 

##<a name="faq"></a>Domande frequenti
Per le domande frequenti sul consumo del servizio web o la pubblicazione su Azure Marketplace, vedere [di seguito](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

 
