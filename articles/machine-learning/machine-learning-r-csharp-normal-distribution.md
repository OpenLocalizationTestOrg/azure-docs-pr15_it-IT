<properties 
    pageTitle="Famiglia di servizi Web di distribuzione normale | Microsoft Azure" 
    description="Famiglia di servizi Web di distribuzione normale" 
    services="machine-learning" 
    documentationCenter="" 
    authors="ireiter" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="ireiter"/> 

#<a name="normal-distribution-suite"></a>Gruppo di distribuzione normale



Gruppo di distribuzione normale è un insieme di esempio i servizi web ([Generatore]( https://datamarket.azure.com/dataset/aml_labs/ndg7)di [Tabelle della legge del calcolo]( https://datamarket.azure.com/dataset/aml_labs/ndq5), [Probabilità calcolatrice]( https://datamarket.azure.com/dataset/aml_labs/ndp5)) che consente di generare e gestiscono le distribuzioni normali. I servizi consentono la generazione di una sequenza di distribuzione normale di qualsiasi lunghezza, il calcolo quantiles da una data probabilità e il calcolo delle probabilità da un determinato tabelle della legge del. Ognuno dei servizi genera output diversi basato sul servizio selezionato (vedere la descrizione riportata di seguito). Gruppo di distribuzione normale si basa R funzioni qnorm, rnorm e pnorm, che sono inclusi nel pacchetto Stat R.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>Questo servizio web potrebbe essere utilizzato dagli utenti – potenzialmente tramite un'app per dispositivi mobili, tramite un sito Web o anche in un computer locale, ad esempio. Tuttavia lo scopo del servizio web che funge da un esempio di utilizzo apprendimento Azure per creare servizi web nella parte superiore di codice R. Con poche righe di codice R e si fa clic sul pulsante all'interno di Azure Machine Learning Studio, è possibile creare con codice R una prova e pubblicato come un servizio web. Il servizio web può essere pubblicato in Azure Marketplace e consumato da utenti e dispositivi in tutto il mondo senza impostazioni infrastruttura dell'autore del servizio web.  
 

##<a name="consumption-of-web-service"></a>Consumo del servizio web
Gruppo di distribuzione normale include i seguenti 3 servizi.

###<a name="normal-distribution-quantile-calculator"></a>Distribuzione normale tabelle della legge del calcolo
Questo servizio accetta 4 argomenti di una distribuzione normale e calcola tabelle della legge del associato.

Gli argomenti di input sono:

* p - una singola probabilità di un evento con distribuzione normale. 
* Media - Media distribuzione normale.
* SD - la deviazione standard di distribuzione normale. 
* Lato - L per il lato inferiore della distribuzione e U per il lato superiore della distribuzione.

L'output del servizio è calcolata tabelle della legge del associato la probabilità specificata.

###<a name="normal-distribution-probability-calculator"></a>Distribuzione normale probabilità calcolatrice
Questo servizio accetta 4 argomenti di una distribuzione normale e calcola la probabilità associata.

Gli argomenti di input sono:

* domande-tabelle della legge del singolo di un evento con distribuzione normale. 
* Media - Media distribuzione normale.
* SD - la deviazione standard di distribuzione normale. 
* Lato - L per il lato inferiore della distribuzione e U per il lato superiore della distribuzione.

L'output del servizio è la probabilità calcolata associato tabelle della legge del specificato.

###<a name="normal-distribution-generator"></a>Generatore di distribuzione normale
Questo servizio accetta 3 argomenti di una distribuzione normale e genera una sequenza casuale di numeri normalmente distribuiti. Gli argomenti seguenti devono essere forniti a tale all'interno della richiesta:

* n - il numero di osservazioni. 
* Media - Media distribuzione normale.
* SD - la deviazione standard di distribuzione normale. 

L'output del servizio è una sequenza di lunghezza n con una distribuzione normale basata sugli argomenti Media e sd.

>Questo servizio, come ospitati in Azure Marketplace è un servizio di OData; Questi possono essere chiamati tramite POST o GET. 

Esistono diversi modi di utilizzo del servizio in modo automatico (app di esempio sono qui: [Generatore](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx), [Probabilità calcolatrice](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx) [Tabelle della legge del calcolatrice](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx)).

###<a name="starting-c-code-for-web-service-consumption"></a>Inizio del codice c# per l'utilizzo di servizi web:

###<a name="normal-distribution-quantile-calculator"></a>Distribuzione normale tabelle della legge del calcolo
    public class Input
    {
            public string p;
            public string mean;
            public string sd;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { p = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


###<a name="normal-distribution-probability-calculator"></a>Distribuzione normale probabilità calcolatrice
    public class Input
    {
            public string q;
            public string mean;
            public string sd;
            public string side;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { q = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }

###<a name="normal-distribution-generator"></a>Generatore di distribuzione normale
    public class Input
    {
            public string n;
            public string mean;
            public string sd;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { n = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text };
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
>Questo servizio web è stato creato con Azure apprendimento. Per una versione di valutazione gratuita, nonché introduttivi sulla creazione di esperimenti e [servizi web di pubblicazione](machine-learning-publish-a-machine-learning-web-service.md), vedere [azure.com/ml](http://azure.com/ml). 

Di seguito è una schermata di prova che ha creato il codice di esempio e servizio web per ciascuno dei moduli all'interno di prova.

###<a name="normal-distribution-quantile-calculator"></a>Distribuzione normale tabelle della legge del calcolo

Flusso di prova:

![Flusso di prova][2]
 
    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    if (param$p < 0 ) {
    print('Bad input: p must be between 0 and 1')
    param$p = 0
    } else if (param$p > 1) {
    print('Bad input: p must be between 0 and 1')
    param$p = 1
    }
    q = qnorm(param$p,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    q = 2* param$mean - q
    } else if (param$side =='L') {
    q = q
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(q)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");
    
###<a name="normal-distribution-probability-calculator"></a>Distribuzione normale probabilità calcolatrice
Flusso di prova:

![Flusso di prova][3]
 
    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(q=-1,mean=0,sd=1,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    prob = pnorm(param$q,mean=param$mean,sd=param$sd)

    if (param$side == 'U'){
    prob = 1 - prob
    } else if (param$side =='B') {
    prob = ifelse(prob<=0.5,prob * 2, 1)
    } else if (param$side =='L') {
    prob = prob
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(prob)
    
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");
    
###<a name="normal-distribution-generator"></a>Generatore di distribuzione normale
Flusso di prova:

![Flusso di prova][4]

    #Data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,mean=0,sd=1);
    maml.mapOutputPort("data.set"); #send data to output port
    
    # Map 1-based optional input ports to variables
    dataset1 <- maml.mapInputPort(1) # class: data.frame

    param = dataset1
    dist = rnorm(param$n,mean=param$mean,sd=param$sd)

    output = as.data.frame(t(dist))

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##<a name="limitations"></a>Limitazioni 

Questi sono molto semplici esempi che circonda la distribuzione normale. Come è possibile rilevare da quello dell'esempio precedente, intercettazione errore piccola è implementata.

##<a name="faq"></a>Domande frequenti
Per le domande frequenti sul consumo del servizio web o la pubblicazione su Azure Marketplace, vedere [di seguito](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png
 
