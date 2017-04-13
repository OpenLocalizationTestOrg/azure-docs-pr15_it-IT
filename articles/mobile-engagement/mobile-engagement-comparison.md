<properties
    pageTitle="Confronto tra Azure Mobile coinvolgimento con altri servizi di Azure simili"
    description="Confronto tra Azure Mobile coinvolgimento con altri servizi di Azure simili - HockeyApp, AppInsights, hub di notifica"
    services="mobile-engagement"
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="comparing-azure-mobile-engagement-with-other-similar-azure-services"></a>Confronto tra Azure Mobile coinvolgimento con altri servizi di Azure simili

L'elenco dei servizi offerti da Microsoft Azure continua è espansione e in alcuni casi è potrebbe chiedersi come Azure Mobile impegno diverso da quello di questo altro servizio appena leggere o viene a conoscenza. In questo articolo tenta di eliminare la confusione e vengono fornite informazioni per scegliere Azure Mobile coinvolgimento quando più appropriato per l'utilizzo. 
 
Azure coinvolgimento Mobile è un servizio destinate specificamente **per marketing/CMOs digitali** , ma potrebbe essere utilizzato da qualsiasi **autore o un proprietario di app per dispositivi mobili** per aumentare l'utilizzo, criteri di conservazione e monetizzazione della loro App per dispositivi mobili. 

*È una piattaforma di coinvolgimento utente (SaaS) software come servizio che fornisce informazioni approfondite basati sui dati in utilizzo dell'app, segmentazione degli utenti in tempo reale e consente le notifiche push presente in base al contesto e la messaggistica in app.* 

Suddividere questa definizione, sono disponibili le seguenti caratteristiche principali che evidenzia anche la proposta di valore univoco:

1.  **Notifiche push in base al contesto importanti e in app messaggistica**
        
    Questo è il tema centrale del prodotto, eseguire le notifiche push di destinazione e personalizzata. Quindi a tale scopo, raccolti dati analitica comportamento RTF. 

2.  **Approfondimenti basati sui dati in uso di app**

    Sono disponibili per piattaforme diverse SDK per raccogliere analitica comportamento relative agli utenti di app. Si noti l'analitica comportamento termini (nei confronti analitica prestazioni) perché è concentrarsi su come gli utenti di app Usa l'app. Raccolte di dati analitica prestazioni di base relativi errori, anomalo e così via, ma che è non lo stato attivo principale del prodotto. 

3.  **Segmentazione degli utenti in tempo reale**

    Dopo aver raccolto dati analitica comportamento degli utenti di app, si consentono di segmento al pubblico non solo in base a vari parametri e dei dati raccolti a consentono di eseguire campagne push di destinazione. 

4.  **Software-come-a-servizio:**

    Si dispone di un portale distinto dal portale di gestione Azure ottimizzato per interagire e visualizzare RTF analitica comportamento sugli utenti app ed eseguire push campagne di marketing. Il prodotto è orientato per preliminari non!   
 
Con questo set di distinzione in mano, ecco come è confrontare e altre offerte di Azure apparentemente simili - si noti che l'articolo non è un confronto livello ma accetta uno scenario con più l'approccio per definire il prodotto funziona meglio basato su:
 
1.  [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) è soluzione attrezzi per dispositivi mobili Microsoft. Con, è possibile distribuire build a beta tester, raccogliere dati di un arresto anomalo e suggerimenti dagli utenti. Si integra con Visual Studio Team Services attivazione distribuzioni generatori facile e l'integrazione di elementi di lavoro. 
    
    Lo stato attivo è attrezzi e la raccolta analitica i dati sulle prestazioni di App per dispositivi mobili. Potrebbe essere necessario con integrazione entrambi HockeyApps e coinvolgimento Mobile in un'applicazione e che non saranno insolito perché anche se non vi è sovrapposizione alcuni tra i dati raccolti, lo stato attivo core dei prodotti è diverso e sono utili per ottenere diversi obiettivi dell'utente.  

2.  [Informazioni dettagliate sui applicazione](../application-insights/app-insights-overview.md) Se l'app per dispositivi mobili ha un lato server, quindi utilizzare applicazione approfondimenti per monitorare web sul lato server l'app ma per l'App per dispositivi mobili sul lato client, è necessario utilizzare HockeyApp. 

3.  [Hub di notifica](https://azure.microsoft.com/services/notification-hubs/) fornisce un semplice servizio nel senso che non è necessario un SDK integrato in app per dispositivi mobili e avere controllo completo dell'app per dispositivi mobili e consente l'invio di notifiche push con le funzionalità di tagging base. Questo è ideale per qualsiasi proprietario app non interessato minore sull'utilizzo e altre informazioni l'invio di comunicazione istantaneamente agli utenti di app (trasmissione a una popolazione di grandi dimensioni). 

    Si noti lo stato attivo qui sull'invio di notifiche veloce per eccezionali con calendario di base. 

Vediamo alcuni scenari:

1.  TIM fa parte del team di marketing digitali all'archivio di Adventure Works che pubblica App per dispositivi mobili per gli utenti. Obiettivo di TIM è assicurarsi che gli utenti che scaricare le App per dispositivi mobili continuano a usarlo e frequenza. Questo aumenta non solo un marchio allegare con gli utenti di app, ma anche monetizzazione aumenta quando gli utenti di app effettuano acquisti App per dispositivi mobili. Per questa Tim sarà necessario inviare notifiche target per gli utenti app sono utili favorire per aprire l'app e tornare alla schermata viene spesso. Verrà visualizzata nel punto in cui Tim utili coinvolgimento Mobile. 

2.  Scritto JoAnn fa parte del team di sviluppo di App per dispositivi mobili Adventure Works e ricerca di un prodotto per informazioni dettagliate su anomalo, eccezioni, accedere e ottenere telemetria prestazioni da un'app. Verrà visualizzata nel punto in cui scritto Joann utili HockeyApp. Scritto JoAnn Impossibile integrare entrambi HockeyApp per la sua scenari di sviluppo attivo e Azure Mobile coinvolgimento TIM nella stessa applicazione per ottenere il massimo di entrambi. 

3.  Marco fa parte del team di sviluppo di App per dispositivi mobili in rete di Contoso News e tutti che si desidera consiste nell'inviare resistenza gli avvisi di news per tutti gli utenti senza molto segmentazione non appena viene generato l'avviso di news. Verrà visualizzata nel punto in cui Marco utili hub di notifica. In questo scenario, è possibile tuttavia che come mano che matura app per dispositivi mobili, è necessario eseguire molto più complessa segmentazione e ottenere informazioni dettagliate sul comportamento dell'utente app. Al momento, sarà necessario Marco valutare Azure Mobile coinvolgimento. 
 
In breve, lo scopo del coinvolgimento Mobile non è solo per raccogliere analitica - non è "un altro prodotto Analitica da Microsoft". È informazioni sull'invio di notifiche push target e di destinazione, raccolte dati analitica comportamento ma lo stato attivo rimane sull'invio di notifiche push che rendono più utili per gli utenti di app in modo che non viene come posta indesiderata. 

Per ulteriori informazioni, consultare questo [breve video](mobile-engagement-overview.md) sulle coinvolgimento Mobile in poche parole. 

