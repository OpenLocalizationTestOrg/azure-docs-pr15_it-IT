<properties 
    pageTitle="Smorzamento esponenziale con la previsione | Microsoft Azure" 
    description="Servizio Web: Smorzamento esponenziale con la previsione" 
    services="machine-learning" 
    documentationCenter="" 
    authors="xueshanz" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016" 
    ms.author="xueshzha"/> 


#<a name="forecasting---exponential-smoothing"></a>La previsione - Smorzamento esponenziale 

Questo [servizio web]( https://datamarket.azure.com/dataset/aml_labs/ets) implementato il modello di smorzamento esponenziale (ETS) per generare le stime in base ai dati cronologici forniti dall'utente. La domanda per un prodotto specifico aumenterà anno corrente? È possibile prevedere le vendite del prodotto per il periodo di Natale, in modo che è possibile pianificare in modo efficace gli inventario? Modelli di previsione sono soggetto a tali domande di indirizzi. Dato dati passati, questi modelli esaminare le tendenze nascoste e stagionalità prevedere le tendenze future.  


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
 
>Questo servizio web potrebbe essere utilizzato dagli utenti – potenzialmente tramite un'app per dispositivi mobili, tramite un sito Web o anche in un computer locale, ad esempio. Tuttavia lo scopo del servizio web che funge da un esempio di utilizzo apprendimento Azure per creare servizi web nella parte superiore di codice R. Con poche righe di codice R e si fa clic sul pulsante all'interno di Azure Machine Learning Studio, è possibile creare con codice R una prova e pubblicato come un servizio web. Il servizio web può essere pubblicato in Azure Marketplace e consumato da utenti e dispositivi in tutto il mondo senza impostazioni infrastruttura dell'autore del servizio web.
 
##<a name="consumption-of-web-service"></a>Consumo del servizio web 
 
Questo servizio accetta 4 argomenti e calcola le previsioni ETS.
Gli argomenti di input sono:

* Frequenza - indica la frequenza di dati non elaborati (ogni giorno/settimana/mensili/trimestrale/annuali).
* Orizzonte - futuro previsione intervallo di tempo.
* Data: aggiungere i nuovi dati serie ora per volta.
* Valore: aggiungere i nuovi valori di dati di serie ora.

L'output del servizio è il previsioni valori calcolati.

Potrebbe essere input di esempio: 

* Frequenza - 12
* Orizzonte - 12
* Data - 1/15/2012 15/2/2012; 3/15/2012; 4/15/2012; 5/15/2012; 15/6/2012; 15/7/2012; 8 / 15/2012 15/9/2012 15/10/2012; 15/11/2012; 12/15/2012; 15/1/2013; 15/2/2013; 15/3/2013; 15/4/2013; 5/15/2013; 15/6/2013; 15/7/2013; 8 / 15/2013; 15/9/2013; 15/10/2013; 15/11/2013; 12/15/2013; 15/1/2014 15/2/2014; 3/15/2014; 15/4/2014; 5/15/2014; 15/6/2014; 15/7/2014; 8 / 15/2014; 15/9/2014
* Valore - 3.479; 3.68; 3.832; 3.941; 3.797; 3.586; 3.508; 3.731; 3.915; 3.844; 3.634; 3.549; 3.557; 3.785; 3.782; 3.601; 3.544; 3.556; 3.65; 3.709; 3.682; 3.511; 3.429 3.51 3.523; 3.525; 3.626; 3.695; 3.711; 3.711; 3.693; 3.571; 3.509
 
>Questo servizio, come ospitati in Azure Marketplace è un servizio di OData; Questi possono essere chiamati tramite POST o GET. 

Esistono diversi modi di utilizzo del servizio in modo automatico (è un'app riportato [di seguito](http://microsoftazuremachinelearning.azurewebsites.net/etsForecasting.aspx)).

###<a name="starting-c-code-for-web-service-consumption"></a>Inizio del codice c# per l'utilizzo di servizi web:
    public class Input
    {
            public string frequency;
            public string horizon;
            public string date;
            public string value;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };
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

All'interno di apprendimento Azure, è stato creato un nuovo prova vuoto. Dati di input di esempio è stati caricati con uno schema di dati predefiniti. Collegato allo schema di dati è uno [Script R eseguire] [ execute-r-script] modulo che genera ETS previsioni modello mediante le funzioni 'ets', 'previsione' dei R. 


![Flusso di prova][2]

####<a name="module-1"></a>Modulo 1:
 
    # Add in the CSV file with the data in the format shown below 
![Esempio di dati][3]   

####<a name="module-2"></a>Modulo 2:
    # Data input
    data <- maml.mapInputPort(1) # class: data.frame
    library(forecast)
    
    # Preprocessing
    colnames(data) <- c("frequency", "horizon", "dates", "values")
    dates <- strsplit(data$dates, ";")[[1]]
    values <- strsplit(data$values, ";")[[1]]
    
    dates <- as.Date(dates, format = '%m/%d/%Y')
    values <- as.numeric(values)
    
    # Fit a time-series model
    train_ts<- ts(values, frequency=data$frequency)
    fit1 <- ets(train_ts)
    train_model <- forecast(fit1, h = data$horizon)
    plot(train_model)
    
    # Produce forcasting
    train_pred <- round(train_model$mean,2)
    data.forecast <- as.data.frame(t(train_pred))
    colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
    
    # Data output
    maml.mapOutputPort("data.forecast");

 
##<a name="limitations"></a>Limitazioni 

Si tratta di un esempio molto semplice per la previsione ETS. Come è possibile rilevare da quello dell'esempio precedente, rilevamento di errori non è implementato e il servizio si presuppone che tutte le variabili siano valori positivi/continua e la frequenza deve essere un numero intero maggiore di 1. La lunghezza dei vettori data e il valore deve essere lo stesso. La variabile data deve essere conforme al formato "gg/mm/aaaa".

##<a name="faq"></a>Domande frequenti
Per le domande frequenti sul consumo del servizio web o la pubblicazione su Azure Marketplace, vedere [di seguito](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img1.png
[2]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img2.png
[3]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
