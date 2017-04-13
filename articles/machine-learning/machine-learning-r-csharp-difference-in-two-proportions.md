<properties 
    pageTitle="Differenza tra le proporzioni Test | Microsoft Azure" 
    description="Differenza tra le proporzioni Test" 
    services="machine-learning" 
    documentationCenter="" 
    authors="aniedea" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="aniedea"/> 


#<a name="difference-in-proportions-test"></a>Differenza tra le proporzioni Test


Sono due proporzioni forma statistica diverse? Si supponga che si desidera confrontare due filmati per determinare se un filmato ha una percentuale superiore dei "mi piace" quando rispetto a altra. Con un campione di grandi dimensioni, potrebbe essere una forma statistica importante differenza tra le proporzioni 0,50 e 0.51. Con un piccolo esempio sono potrebbe non essere dati sufficienti per determinare se queste proporzioni sono effettivamente diverse. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Questo [servizio web]( https://datamarket.azure.com/dataset/aml_labs/prop_test) esegue un test ipotesi della differenza in due proporzioni in base all'input utente del numero di successi e il numero totale di prove per i gruppi di 2 confronto. In uno scenario, il servizio web potrebbe essere chiamato all'interno di un'app di confronto filmato, per indicare se uno dei filmati è davvero 'mi' più spesso rispetto a altro, in base alle valutazioni filmato.

>Questo servizio web potrebbe essere utilizzato dagli utenti – potenzialmente tramite un'app per dispositivi mobili, tramite un sito Web o anche in un computer locale, ad esempio. Tuttavia lo scopo del servizio web che funge da un esempio di utilizzo apprendimento Azure per creare servizi web nella parte superiore di codice R. Con poche righe di codice R e si fa clic sul pulsante all'interno di Azure Machine Learning Studio, è possibile creare con codice R una prova e pubblicato come un servizio web. Il servizio web può essere pubblicato in Azure Marketplace e consumato da utenti e dispositivi in tutto il mondo senza impostazioni infrastruttura dell'autore del servizio web.


##<a name="consumption-of-web-service"></a>Consumo del servizio web

Questo servizio accetta 4 argomenti e un'ipotesi testare di proporzioni.

Gli argomenti di input sono:

* Successes1 - numero di eventi di successo nel campione 1.
* Successes2 - numero di eventi di successo nel campione 2.
* Total1 - dimensione del campione 1.
* Total2 - dimensione del campione 2.

L'output del servizio è il risultato delle ipotesi testare insieme al valore di p chi-quadrato statistico, test e proporzioni dei limiti di 1/2 e l'intervallo di confidenza di esempio.

>Questo servizio, come ospitati in Azure Marketplace è un servizio di OData; Questi possono essere chiamati tramite POST o GET. 

Esistono diversi modi di utilizzo del servizio in modo automatico (è un'app riportato [di seguito](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Inizio del codice c# per l'utilizzo di servizi web:

    public class Input
    {
            public string successes1;
            public string successes2;
            public string total1;
            public string total2;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { successes1 = TextBox1.Text, successes2 = TextBox2.Text, total1 = TextBox3.Text, total2 = TextBox4.Text };
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

All'interno di apprendimento Azure, una nuova prova vuoto è stato creato con due [Eseguire Script R] [ execute-r-script] moduli. Primo modulo che viene definito lo schema dei dati, mentre la seconda modulo utilizza il comando prop.test all'interno di R per eseguire il test di ipotesi di 2 proporzioni. 


###<a name="experiment-flow"></a>Flusso di prova:

![Flusso di prova][2]


####<a name="module-1"></a>Modulo 1:
    ####Schema definition  
    data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
    maml.mapOutputPort("data.set"); #send data to output port
    dataset1 = maml.mapInputPort(1) #read data from input port
    

####<a name="module-2"></a>Modulo 2:

    test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

    result=data.frame(
    result=ifelse(test$p.value<0.05,"The proportions are different!",
    "The proportions aren't different statistically."),
    ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
    pvalue=round(test$p.value,4),
    proportion1=round(test$estimate[1],4),
    proportion2=round(test$estimate[2],4),
    confintlow=round(test$conf.int[1],4),
    confinthigh=round(test$conf.int[2],4)) 

    maml.mapOutputPort("result"); #output port
    

##<a name="limitations"></a>Limitazioni 

Si tratta di un esempio molto semplice di un test della differenza in 2 proporzioni. Come è possibile rilevare da quello dell'esempio precedente, rilevamento di errori non è implementato e il servizio si presuppone che tutte le variabili continue.

##<a name="faq"></a>Domande frequenti
Per le domande frequenti sul consumo del servizio web o la pubblicazione su Azure Marketplace, vedere [di seguito](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
