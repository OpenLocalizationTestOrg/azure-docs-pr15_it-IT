<properties 
    pageTitle="Regressione lineare multivariate | Microsoft Azure" 
    description="Multivariate regressione lineare" 
    services="machine-learning" 
    documentationCenter="" 
    authors="jaymathe" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/14/2016" 
    ms.author="jaymathe"/> 


#<a name="multivariate-linear-regression"></a>Multivariate regressione lineare   
 

 
Si supponga che si dispone di un set di dati e si desidera prevedere rapidamente una variabile dipendente (y) per ogni singolo (i) in base alle variabili indipendente. Regressione lineare è una tecnica statistica popolari utilizzata per tale previsioni. Di seguito y variabile dipendente verrà considerato uguale a un valore continuo.  


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

Un semplice scenario potrebbe essere in cui l'Organizzatore ricerche sta tentando di prevedere lo spessore di una persona (y) in base all'altezza (x). Uno scenario con più avanzato potrebbe essere nel punto in cui l'Organizzatore ricerche contiene informazioni aggiuntive per la persona (ad esempio lo spessore, sesso, competizione) e tenta di indovinare lo spessore della persona. Questo [servizio web]( https://datamarket.azure.com/dataset/aml_labs/multivariate_regression) si inserisce il modello di regressione lineare ai dati e restituisce il valore previsto (y) per ognuna delle osservazioni nei dati.

>Questo servizio web potrebbe essere utilizzato dagli utenti – potenzialmente tramite un'app per dispositivi mobili, tramite un sito Web o anche in un computer locale, ad esempio. Tuttavia lo scopo del servizio web che funge da un esempio di utilizzo apprendimento Azure per creare servizi web nella parte superiore di codice R. Con poche righe di codice R e si fa clic sul pulsante all'interno di Azure Machine Learning Studio, è possibile creare con codice R una prova e pubblicato come un servizio web. Il servizio web può essere pubblicato in Azure Marketplace e consumato da utenti e dispositivi in tutto il mondo senza impostazioni infrastruttura dell'autore del servizio web.  

##<a name="consumption-of-web-service"></a>Consumo del servizio web  
Questo servizio web restituisce i valori stimati della variabile dipendente in base alle variabili indipendente per tutte le osservazioni. Il servizio web prevede l'utente finale di immettere dati come una stringa in righe da una virgola (,) e le colonne sono separate da punti e virgola (;). Il servizio web prevede 1 riga alla volta e prevede la prima colonna da variabile dipendente. Un set di dati di esempio Impossibile simile al seguente:

![Dati di esempio][1]

Osservazioni senza una variabile dipendente devono essere input come "ND" per y. I dati di input per il set di dati indicati sarebbe la stringa seguente: "10; 5; 2,18; 1; 6,6; 5.3; 2.1,7; 5; 5,22; 3; 4,12; 2; 1 ND; 3; 4". L'output è il valore previsto per ognuna delle righe in base alle variabili indipendente. 

>Questo servizio, come ospitati in Azure Marketplace è un servizio di OData; Questi possono essere chiamati tramite POST o GET. 

Esistono diversi modi di utilizzo del servizio in modo automatico (è un'app riportato [di seguito](http://microsoftazuremachinelearning.azurewebsites.net/MultipleLinearRegressionService.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Inizio del codice c# per l'utilizzo di servizi web:

    public class Input
    {
            public string value;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { value = TextBox1.Text };
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


All'interno di apprendimento Azure, una nuova prova vuoto è stato creato e due [Eseguire Script R] [ execute-r-script] moduli sono stati estratti nell'area di lavoro. Questo servizio web viene eseguita una prova di apprendimento Azure con uno script R sottostante. Esistono 2 parti per questa prova: definizione di schema e training del modello + punteggio. Primo modulo definisce la struttura prevista del set di dati input, in cui la prima variabile è la variabile dipendente e le variabili rimanenti sono indipendenti. Il secondo modulo adatto a un modello di regressione lineare generico per i dati di input.  
  
![Flusso di prova][3]

####<a name="module-1"></a>Modulo 1:
 
####<a name="schema-definition"></a>Definizione dello schema  
    data <- data.frame(value = "1;2;3,4;5;6,7;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####<a name="module-2"></a>Modulo 2:
####<a name="lm-modeling"></a>Modellazione LM   
    data <- maml.mapInputPort(1) # class: data.frame  
  
    data.split <- strsplit(data[1,1], ",")[[1]]  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- as.data.frame(t(data.split)) 
    data.split <- data.matrix(data.split) 
    data.split <- data.frame(data.split) 
    model <- lm(data.split)  

    out=data.frame(predict(model,data.split))  
    out <- data.frame(t(out))

    maml.mapOutputPort("out");  
 
##<a name="limitations"></a>Limitazioni
Si tratta di un esempio di un servizio web di regressione lineare più molto semplice. Nessun errore intercettazione implementato come può essere visualizzato da quello dell'esempio precedente, e il servizio presuppone che tutto sia una variabile di tipo continuo (nessuna funzionalità per categoria consentita), come i servizio solo input valori numerici al momento della creazione di questo servizio web. Inoltre, il servizio attualmente gestisce dimensioni dati limitati, scadenza alla natura richiesta/risposta della chiamata del servizio web e al fatto che il modello viene contenuto ogni volta che si chiama il servizio web. 

##<a name="faq"></a>Domande frequenti
Per le domande frequenti sul consumo del servizio web o la pubblicazione su Azure Marketplace, vedere [di seguito](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img1.png
[2]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img2.png
[3]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
