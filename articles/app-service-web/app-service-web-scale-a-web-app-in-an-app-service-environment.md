<properties 
    pageTitle="Come ridurre un'App in un ambiente App" 
    description="Ridimensionamento di un'app in un ambiente App" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="stefsch" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="ccompy"/>

# <a name="scaling-apps-in-an-app-service-environment"></a>Ridimensionamento delle App in un ambiente App #

Nel servizio App Azure sono in genere tre elementi che è possibile ridimensionare:

- prezzi piano
- dimensioni del lavoro 
- numero di istanze.

In una base non è necessario selezionare o modificare il piano di prezzo.  In termini di funzionalità è già prezioso prezzi livello di capacità.  

Per quanto riguarda le dimensioni di lavoro, l'amministratore ASE può assegnare le dimensioni della risorsa elaborazione da utilizzare per ogni pool di lavoro.  Che indica che è possibile avere lavoro Pool 1 con risorse di elaborazione P4 e lavoro Pool 2 con P1 calcolare risorse, se lo si desidera.  Non devono essere in ordine di dimensioni.  Per informazioni dettagliate sul intorno le dimensioni e i prezzi vedere di seguito il documento [Azure App servizio prezzi][AppServicePricing].  In questo modo le opzioni di ridimensionamento per web apps e piani di servizio App in un ambiente App come:

- selezione del pool di lavoro
- numero di istanze

La modifica di uno dei due elemento viene eseguita tramite l'interfaccia utente appropriato visualizzata per il ASE ospitato App servizio plan di messaggistica unificata.  

![][1]

Non è possibile ridimensionare la ASP oltre il numero di risorse di elaborazione disponibili nel pool di lavoro a cui fa parte la pagina ASP.  Se è necessario calcolare le risorse del pool di lavoro è necessario richiedere all'amministratore di base per aggiungerli.  Per informazioni sul nuovamente la configurazione del ASE leggere le informazioni qui: [come configurare un ambiente App][HowtoConfigureASE].  È consigliabile anche in modo da sfruttare le caratteristiche di autoscale ASE aggiungere capacità in base a pianificazione o metrica.  Per ottenere maggiori informazioni sulla configurazione autoscale per il ASE ambiente stesso informazioni su [come configurare autoscale per un ambiente App][ASEAutoscale].

È possibile creare app più piani di servizio utilizzando risorse di elaborazione dal pool di lavoro diverso oppure è possibile utilizzare lo stesso pool di lavoro.  Ad esempio se si dispone di risorse di elaborazione disponibili (10) 1, Pool di lavoro, è possibile scegliere di creare un piano di servizio app utilizzando (6) risorse di elaborazione e pianificare un secondo servizio app che usa (4) per calcolare le risorse.

### <a name="scaling-the-number-of-instances"></a>Il numero di istanze di ridimensionamento ###

Quando si crea un'app web in un ambiente App inizia con l'1 istanza.  È quindi possibile scalare in istanze aggiuntive per fornire ampliare risorse per l'app.   

Se il ASE capacità sia sufficiente tratta semplice.  È accedere all'App servizio piano di che contiene i siti che si desidera scalare e selezionare scala.  Verrà visualizzata l'interfaccia utente in cui è possibile impostare la scala per la pagina ASP o configurare manualmente autoscale regole per la pagina ASP.  Per ridimensionare manualmente l'app è sufficiente imposta la ***scala da*** a ***un conteggio delle istanze immesso manualmente***.  Da qui trascinare il dispositivo di scorrimento per la quantità desiderata o immettere nella casella accanto al dispositivo di scorrimento.  

![][2] 

Le regole di autoscale per un ASP in un ASE funzionano come avviene normalmente.  È possibile selezionare ***Percentuale di CPU*** in ***proporzioni*** e creare autoscale regole per la pagina ASP basata sulla percentuale di CPU oppure è possibile creare regole più complesse utilizzando ***le regole pianificazione e le prestazioni***.  Per visualizzare informazioni più dettagliate sulla configurazione di autoscale usare la Guida qui [scalare un'applicazione di servizio App Azure][AppScale]. 


### <a name="worker-pool-selection"></a>Selezione del Pool di lavoro ###

Come indicato in precedenza, la selezione del pool di lavoro è possibile accedere dall'interfaccia utente ASP.  Aprire e il per ASP che si desidera ridimensionare e selezionare pool di lavoro.  Verranno visualizzati tutti i pool di lavoro che è stato configurato l'ambiente App.  Se si dispone del pool di lavoro solo una quindi solo vedrete un pool elencato.  Per modificare il pool di lavoro la pagina ASP è in, è sufficiente selezionare pool di lavoro a cui si desidera che il piano di servizio App per passare alla.  

![][3]

Prima di iniziare la pagina ASP del pool un lavoro a un altro è importante verificare che sarà necessario capacità appropriata per la pagina ASP.  Nell'elenco dei pool di lavoro, non solo è elencato il nome del pool di lavoro ma è anche possibile visualizzare il numero dei collaboratori sono disponibili in tale pool di lavoro.  Assicurarsi che sono presenti abbastanza istanze disponibili per contenere il piano di servizio App.  Se è necessario calcolare più risorse nel pool di lavoro che si desidera spostare, quindi richiedere all'amministratore di base per aggiungerli.  

> [AZURE.NOTE] Spostare che una pagina ASP del pool lavoro una sola causerà fredda avvia App in tale ASP.  In questo modo le richieste di esecuzione lenta l'app viene fredda avviato sulle nuove risorse di calcolo.  Evitare di start fredda tramite l' [applicazione riscaldamento capacità] [ AppWarmup] in Azure App servizio.  Modulo applicazione inizializzazione descritto nell'articolo funziona anche per gli avvii freddo perché il processo di inizializzazione viene richiamato anche quando App sono fredda avviato sulle nuove risorse di calcolo. 

## <a name="getting-started"></a>Guida introduttiva

Per iniziare a utilizzare App servizio ambienti, vedere [Come per creare un'App ambiente del servizio][HowtoCreateASE]

Per ulteriori informazioni sulla piattaforma Azure App servizio, vedere [Servizio App Azure][AzureAppService].

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[AppScale]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[AppWarmup]: http://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
