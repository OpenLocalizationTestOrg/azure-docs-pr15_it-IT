<properties 
    pageTitle="Quali sono le applicazioni di logica?" 
    description="Maggiori informazioni sulle App servizio logica App" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article" 
    ms.date="10/12/2016"
    ms.author="klam"/>

# <a name="what-are-logic-apps"></a>Che cos'è la logica App?

Logica App consentono di semplificare e implementare flussi di lavoro e le integrazioni scalable nel cloud. Fornisce una finestra di progettazione per modellare e automatizzare il processo come una serie di passaggi noto come un flusso di lavoro.  Sono disponibili [molti connettori](../connectors/apis-list.md) tra il cloud e locale di integrare rapidamente su servizi e i protocolli.  Un'app logica inizia con un trigger (come "quando si aggiunge un account a Dynamics CRM") e dopo l'attivazione può iniziare molte azioni combinazioni, le conversioni e logica condizione.

Vantaggi dell'utilizzo di logica di App sono i seguenti:  

- Risparmiare tempo con la progettazione di processi complessi utilizzando semplice da comprendere gli strumenti di progettazione
- Implementazione diretta motivi e i flussi di lavoro, che in caso contrario sarebbe difficile implementare codice
- Guida introduttiva rapidamente dai modelli
- Personalizzare l'app logica con il proprio API, codice e azioni personalizzate
- Connettere e sincronizzare sistemi in locale e nel cloud
- Creare di fuori del BizTalk server, gestione API, funzioni di Azure e Bus di servizio Azure con supporto integrazione costrutto

Logica App è un iPaaS completamente gestito (integrazione di piattaforma come servizio) consentendo agli sviluppatori di non avere a disposizione sulla creazione di hosting, scalabilità, disponibilità e gestione.  Logica App verrà ingrandite automaticamente per rispondere alla domanda.

![Finestra di progettazione del flusso di app](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Come detto, con le applicazioni di logica, è possibile automatizzare processi aziendali. Ecco alcuni esempi:  
 
* Spostare i file caricati in un server FTP nell'archivio di Azure
* Gli ordini di processo e la distribuzione in locale e cloud sistemi
* Monitorare tutti TWEET relativi a un determinato argomento, analizzare la valutazione in e creare gli avvisi e attività per gli elementi che necessitano di completamento.

Scenari, ad esempio questi possono essere configurati tutti nella finestra di progettazione e senza scrivere una sola riga di codice. Guida introduttiva [si crea un'applicazione di logica ora][create].  Dopo aver scritto - un'app di logica può essere [distribuito e riconfigurare rapidamente](app-service-logic-create-deploy-template.md) in più ambienti e aree geografiche.

## <a name="why-logic-apps"></a>Perché la logica App?

Logica App porta velocità e scalabilità nello spazio dell'integrazione dell'organizzazione.  Facilità d'uso della finestra di progettazione, diversi tipi di trigger e azioni e potenti strumenti di gestione verificare centralizzazione l'API più semplice che mai.  Come imprese spostano verso digitalization, App logica consente di connettere sistemi legacy e all'avanguardia.

Inoltre, con l' [Account di integrazione Enterprise] [ biztalk] ridimensionate per scadono scenari di integrazione con la potenza di un [messaggistica XML][xml], [gestione dei partner commerciali][tpm]e altro ancora.

- **Strumenti di progettazione facile da usare** - app di logica può essere progettato-to-end nel browser o con gli strumenti di Visual Studio. Iniziare con un trigger - da una programmazione semplice per quando viene creato un problema di GitHub. Progettare quindi qualsiasi numero di azioni usando la raccolta elaborata dei connettori.

- **API di connettersi facilmente** -attività di composizione pari semplici indicare che sono difficili da implementare nel codice. Logica App rende più facile connettersi sistemi diversi. Connettersi del cloud marketing soluzione ai propri locali sistema di fatturazione? Se si vuole centralizzare messaggistica attraverso API e sistemi con Bus di servizio dell'organizzazione? Logica App rappresentano il modo più veloce e affidabile di offrire soluzioni a questi problemi.

- **Per iniziare rapidamente dai modelli** - consentono di iniziare è disponibile una [raccolta di modelli di] [ templates] che consentono di creare rapidamente alcune soluzioni comuni. Da avanzate soluzioni B2B alla connettività SaaS semplice e anche alcuni che sono riguarda solo le' divertente', la raccolta è il modo più veloce per iniziare con la potenza di logica di App.

- **Estensibilità baked in** - non visualizzare il connettore è necessario? Logica App è progettata per funzionare con il proprio API e il codice. è possibile creare facilmente l'applicazione di API per l'utilizzo di un connettore personalizzato o intervenire in una [Funzione di Azure](https://functions.azure.com) eseguire frammenti di codice richiesta. 

- **Potenza integrazione reale** - avviare semplice e aumento in base alle esigenze. Logica App facilmente possibile sfruttare le potenzialità di BizTalk, settore iniziale integrazione soluzione Microsoft per consentire ai professionisti integrazione di creare soluzioni che hanno bisogno. Trovare ulteriori informazioni sull' [Organizzazione Integration Pack](./app-service-logic-enterprise-integration-overview.md).

## <a name="logic-app-concepts"></a>Logica App concetti

Di seguito sono alcune delle parti chiave che comprendono l'esperienza di logica app. 

- **Flusso di lavoro** - App logica offre un modo grafico per modellare i processi aziendali come una serie di passaggi o un flusso di lavoro.
- **Connettori gestiti** - App logica devono poter accedere ai dati e servizi. Connettori gestiti vengono creati specificamente per facilitare il quando si connette a e la gestione dei dati. Vedere l'elenco dei connettori ora disponibili in [gestite connettori][managedapis].
- **Trigger** - alcuni connettori gestite inoltre funzionare come trigger. Un trigger avvia una nuova istanza di un flusso di lavoro in base a un evento specifico, ad esempio l'arrivo di un messaggio di posta elettronica o una modifica nell'account di archiviazione Azure.
-  **Azioni** - istruzioni dettagliate chiamato un'azione trigger in un flusso di lavoro. Ogni azione è in genere associata a un'operazione su un connettore gestiti o App API personalizzate.
- **Enterprise Integration Pack** - per scenari di integrazione più avanzati, logica App include funzionalità da BizTalk. BizTalk è settore iniziale integrazione la piattaforma Microsoft. I connettori dell'organizzazione Integration Pack consentono di includere facilmente convalida, trasformazione e altre funzionalità in per i flussi di lavoro logica App.

## <a name="getting-started"></a>Guida introduttiva  

- Per iniziare a utilizzare App logica, seguire la [creazione di un'App di logica] [ create] esercitazione.  
- [Visualizzare esempi e scenari comuni](app-service-logic-examples-and-scenarios.md)
- [È possibile automatizzare processi aziendali con logica di App](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Informazioni su come integrare i sistemi con logica di App](http://channel9.msdn.com/Events/Build/2016/P462)

[biztalk]: app-service-logic-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-enterprise-integration-accounts.md
[xml]: app-service-logic-enterprise-integration-b2b.md
[templates]: app-service-logic-use-logic-app-templates.md
