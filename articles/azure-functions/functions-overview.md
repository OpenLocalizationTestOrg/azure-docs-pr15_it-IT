<properties
   pageTitle="Azure funzioni Panoramica | Microsoft Azure"
   description="Imparare a utilizzare le funzioni di Azure per ottimizzare i carichi di lavoro asincroni in minuti."
   services="functions"
   documentationCenter="na"
   authors="mattchenderson"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure funzioni, funzioni, elaborazione dell'evento, webhooks, calcolo dinamico, architettura senza server"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/29/2016"
   ms.author="cfowler;mahender;glenga"/>
   
   
# <a name="azure-functions-overview"></a>Panoramica delle funzioni Azure

Funzioni di Azure è una soluzione per l'esecuzione facilmente frammenti di codice o "funzioni," nel cloud. È possibile scrivere solo il codice che necessario per il problema, senza preoccuparsi di un'intera applicazione o dell'infrastruttura per eseguirlo. In questo modo ancora più produttivi sviluppo ed è possibile utilizzare il linguaggio di sviluppo della scelta effettuata, ad esempio c#, F #, Node, Python o PHP. Pagare solo per la protezione Azure e l'ora che viene eseguito il codice per ridimensionare in base alle esigenze.

In questo argomento viene fornita una panoramica delle funzioni di Azure. Se si desidera iniziare e iniziare a utilizzare le funzioni di Azure, iniziare con l'opzione [Crea la prima funzione Azure](functions-create-first-azure-function.md). Se si sta cercando ulteriori informazioni tecniche su funzioni, vedere la [Guida di riferimento per sviluppatori](functions-reference.md).

## <a name="features"></a>Caratteristiche

Ecco alcune caratteristiche chiave di funzioni di Azure:
    
* **Scelta della lingua** : funzioni di scrittura utilizzando c#, F #, Node, Python, PHP, batch, bash, Java o qualsiasi file eseguibile.
* **Pagare costo per uso del sito** : non pagare solo l'ora di eseguire il codice. Visualizzare l'opzione piano di servizio App dinamica nei [prezzi sezione](#pricing) sotto.  
* **Visualizzare il proprio dipendenze** - funzioni supporta NuGet e NPM, in modo che è possibile utilizzare le raccolte preferite.  
* **Sicurezza integrata** - attivate proteggere HTTP funzioni con provider OAuth, ad esempio Azure Active Directory, Facebook, Google, Twitter e Account Microsoft.  
* **Integrazione semplificato** - facilmente sfruttare i servizi di Azure e offerte software-come-a-servizio. Vedere la [sezione integrazioni](#integrations) sotto per alcuni esempi.  
* **Sviluppo flessibile** - codice il diritto di funzioni nel portale o impostare l'integrazione continua e distribuire il codice mediante GitHub, Visual Studio Team Services e altri [Strumenti di sviluppo è supportato](../app-service-web/web-sites-deploy.md#deploy-using-an-ide).  
* **Apri origine** - funzioni di runtime è [disponibile in GitHub](https://github.com/azure/azure-webjobs-sdk-script)e Apri origine.  

## <a name="what-can-i-do-with-functions"></a>Cosa può fare con le funzioni?

Funzioni di Azure è un'ottima soluzione per i dati di elaborazione, integrare sistemi, utilizzo di internet-di-elementi (IoT) e la creazione di API semplice e microservices. Valutare la possibilità di funzioni per attività come immagine o un ordine di elaborazione, manutenzione del file, attività lunga che si desidera eseguire in un thread in background o per le attività che si desidera eseguire in base alla pianificazione. 

Funzioni sono disponibili modelli per iniziare a usare gli scenari principali, incluse le operazioni seguenti:

* **BlobTrigger** - BLOB di archiviazione di Azure processo quando vengono aggiunti a contenitori. È utile per il ridimensionamento delle immagini.
* **EventHubTrigger** - rispondere a eventi recapitato a un Hub di evento Azure. Particolarmente utile in strumentazione delle applicazioni, elaborazione del flusso di lavoro o esperienza utente e gli scenari Internet di elementi (IoT).
* **Webhook generico** - processo webhook HTTP richiede a qualsiasi servizio che supporta webhooks.
* **GitHub webhook** - rispondere a eventi che si verificano nei repository GitHub. Ad esempio, vedere [creare un webhook o la funzione API](functions-create-a-web-hook-or-api-function.md).
* **HTTPTrigger** - Trigger l'esecuzione di codice utilizzando una richiesta HTTP.
* **QueueTrigger** - rispondere a messaggi in arrivo in una coda di archiviazione Azure. Ad esempio, vedere [creare una funzione di Azure che viene associato a un servizio Azure](functions-create-an-azure-connected-function.md).
* **ServiceBusQueueTrigger** - connettersi codice ad altri servizi Azure o servizi in locale in attesa di code di messaggi. 
* **ServiceBusTopicTrigger** - connettersi codice ad altri servizi Azure o servizi in locale mediante la sottoscrizione ad argomenti. 
* **TimerTrigger** - eseguire pulizia o altre attività batch su una pianificazione predefinita. Ad esempio, vedere [creazione di un evento funzione di elaborazione](functions-create-an-event-processing-function.md).

Funzioni di Azure supporta *trigger*, che sono modi per avviare l'esecuzione di codice e *le associazioni*, modi per semplificare la codifica per i dati di input e di output. Per una descrizione dettagliata del trigger e associazioni che fornisce le funzioni di Azure, vedere [funzioni di Azure trigger e riferimento per sviluppatori associazioni](functions-triggers-bindings.md).


## <a name="integrations"></a>Integrazioni

Funzioni di Azure si integra con una serie di Azure e servizi di terze parti 3o. È possibile utilizzare queste per la funzione di attivazione e avviare l'esecuzione o per fungere da input e di output per il codice. Integrazioni servizio seguenti sono supportate dalle funzioni di Azure. 

* DocumentDB Azure
* Hub evento Azure 
* Azure App Mobile (tabelle)
* Hub di notifica Azure
* Azure Bus di servizio (code e argomenti)
* Archiviazione Azure (blob, code e tabelle) 
* GitHub (webhooks)
* Locale (con Service Bus)

## <a name="pricing"></a>Quanto costa funzioni costo?

Funzioni di Azure ha due tipi di prezzi piani, scegliere quello più adatto alle proprie esigenze: 

* **Piano di Hosting dinamiche** - quando viene eseguita la funzione, Azure fornisce tutte le risorse di calcolo necessarie. Non è necessario preoccuparsi di gestione delle risorse e di pagare solo l'ora che il codice viene eseguito. Tutti i dettagli sui prezzi sono disponibili nella [pagina funzioni prezzi](/pricing/details/functions). 

* **Piano di servizio app** - eseguire le funzioni come il web, mobili e le API app. Quando si utilizza già App servizio per le altre applicazioni, è possibile eseguire le funzioni nello stesso piano senza costi aggiuntivi. Dettagli completi sono disponibili nella [pagina App servizio prezzi](/pricing/details/app-service/).

Per ulteriori informazioni sul ridimensionamento delle funzioni, vedere [come ridimensionare le funzioni di Azure](functions-scale.md).

##<a name="next-steps"></a>Passaggi successivi

+ [Creare la prima funzione Azure](functions-create-first-azure-function.md)  
Creare la prima funzione mediante la Guida introduttiva di Azure funzioni subito in. 
+ [Riferimenti per sviluppatori di funzioni Azure](functions-reference.md)  
Fornisce informazioni tecniche più runtime funzioni Azure e un riferimento per la codifica di funzioni e la definizione di trigger e le associazioni.
+ [Funzioni di Azure test](functions-test-a-function.md)  
Descrive diverse tecniche per testare le funzioni e gli strumenti.
+ [Come ridimensionare le funzioni di Azure](functions-scale.md)  
Vengono descritti i piani di servizio disponibili con le funzioni di Azure, inclusi il piano di servizio dinamico e su come scegliere il piano a destro. 
+ [Ulteriori informazioni sul servizio App Azure](../app-service/app-service-value-prop-what-is.md)  
Funzioni di Azure consente di sfruttare la piattaforma Azure App servizio per le funzionalità principali quali distribuzioni variabili e diagnostica. 