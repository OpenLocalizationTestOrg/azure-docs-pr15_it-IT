<properties 
   pageTitle="Note sulla versione di Azure SDK per .NET 2.5.1" 
   description="Note sulla versione di Azure SDK per .NET 2.5.1" 
   services="app-service" 
   documentationCenter=".net,nodejs,java" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/10/2016"
   ms.author="juliako"/>


# <a name="azure-sdk-for-net-251-release-notes"></a>Note sulla versione di Azure SDK per .NET 2.5.1

Questo documento contiene le note sulla versione di Azure SDK per .NET 2.5.1 rilasciare. 

##<a name="azure-sdk-for-net-251-release-notes"></a>Note sulla versione di Azure SDK per .NET 2.5.1

Di seguito sono nuove caratteristiche e gli aggiornamenti in Azure SDK per .NET 2.5.1.

- Nuovo features\scenarios correlati a **Estensioni degli strumenti Web**. 

    - Siti Web Azure è stata rinominata al servizio App Azure. Per ulteriori informazioni, vedere [servizio Azure App e servizi di Windows Azure esistente](app-service-changes-existing-services.md).
    - Azure supporto API App (Preview) è stato aggiunto in modo che i clienti possono pubblicare progetti ASP.NET come API App e quindi utilizzare il componente > gesto di Azure API App Client in c# progetti per generare il codice in base alla struttura dell'App API distribuito. 
    - Il nodo siti Web in Esplora Server è stato sostituito anziché il nodo servizio App Azure, che contiene il supporto per risorse basati su raggruppamento di Azure API Apps, App Mobile e Web Apps.
    - Azure supporto App Mobile (Preview) è stato aggiunto in modo che gli utenti possono creare nuovi progetti App Mobile, aggiungere App Mobile controller, pubblicare i progetti e quando in modalità remota il debug delle applicazioni.
    - Aggiungere > gesto di Azure API App Client supporta ora file Swagger JSON locali, in modo che gli sviluppatori API Web possono utilizzare NuGets di terze parti come Swashbuckle per generare Swagger o creare manualmente. In questo modo, gli sviluppatori di client possono utilizzare la funzionalità di generazione di codice a utilizzare qualsiasi endpoint Swagger nei progetti c#. 
    - Web App e finestre di dialogo pubblicazione API App sono state migliorate per supportare il concetto di Azure portale di raggruppamento delle risorse e selezione/creazione di gruppi di risorse Azure e piani di servizio App sono rappresentati in Web App e API App provisioning finestra di dialogo Nuovo. 
    - Azure API App i nodi Esplora Server sono disponibili collegamenti al collegamento completa di App API nel portale di Azure, nonché altre caratteristiche come Streaming Log e il debug remoto.

    Per problemi noti e limitazioni corrente di [Azure SDK .NET 2.5.1 in questa](app-service-release-notes.md#known_issues_2_5_1) sezione riportata di seguito.


- Nuovo features\scenarios correlati agli **Strumenti HDInsight** in Visual Studio sono abilitate in questa versione. 
    - Convalida locali di script hive. Fare clic sul pulsante di script di convalida nella barra degli strumenti per verificare se sono presenti errori in uno script. 
    - Debug dei processi Hive migliorato. È possibile eseguire il debug Hive processi accedendo registri filati in Visual Studio. Se l'applicazione ha problemi di prestazioni, l'analisi dei registri filati verranno fornite informazioni utili..
    - (Pubblico anteprima) Supporto per il completamento automatico di parole chiave e IntelliSense per Hive. Per semplificare la creazione di script Hive, HDInsight Tools per Visual Studio aggiunto il completamento automatico di parole chiave e il supporto IntelliSense per Hive.
    - Supporto per eccesso. È ora possibile usare HDInsight Tools per Visual Studio per sviluppare eccesso topologie/Spouts/bulloni in c#. È quindi possibile inviare la topologia sviluppata in un cluster di eccesso e visualizzare lo stato della topologia/bulloni/beccuccio. È possibile utilizzare per la risoluzione del eccesso topologie/bulloni/Spouts registri di sistema e clienti. Utilizzare le risorse di linguaggio in eccesso su HDInsight.
    
    Per ulteriori informazioni, vedere [Introduzione all'utilizzo di HDInsight Hadoop Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).



##<a id="known_issues_2_5_1"></a>Azure SDK per .NET 2.5.1 problemi noti e limitazioni

- Azure API App è visibile come una destinazione di distribuzione per le App per dispositivi mobili. Web App dovrebbe essere la destinazione sola per le App per dispositivi mobili fino a una versione successiva. 
- Il provisioning di Azure API App, è possibile risultato successo ma occasionalmente in non è possibile aggiornare lo stato di avanzamento nella finestra di Azure App servizio attività. Soluzione alternativa consiste nel controllare lo stato dell'App API Azure nuovo nel portale di Azure. 
- File > Nuovo progetto > App API > esperienza F5 genera un errore HTTP poiché non esiste alcun default/index.html. Soluzione alternativa consiste nel passare manualmente all'URL /api/values. 
- Talvolta, le icone Esplora Server vengono visualizzate piatta. Il riavvio di Visual Studio consente di risolvere il problema. 
- Se un'eccezione durante il provisioning di Web App o API App (ad esempio ha superato la quota errori o duplicati nome del gateway Azure API App), gli errori mostrano alcune testo non elaborato JSON. 
- Problemi della creazione di progetti intermittente quando approfondimenti applicazione è selezionata al momento della creazione di progetto.
- In alcuni casi, il codice di Azure API App Client generato manca spazi dei nomi, è necessario includere manualmente (o automaticamente importati tramite Visual Studio pile) per il codice per la compilazione. 
- Progetti App mobile devono essere pubblicati in web App, ma è necessario selezionare un sito che è stato creato come un'App Mobile nel portale di Azure poiché progetti App Mobile necessita di un database. 
- La pagina iniziale per le App per dispositivi mobili viene utilizzato il termine "servizio mobile" anziché "App per dispositivi mobili" 
- La creazione del progetto App mobile potrebbe richiedere un minuto per creare. 
- Durante l'API App provisioning (in alcuni casi) un messaggio di errore torno da Azure API che riflettono che le autorizzazioni non è possibile impostare in modo corretto, mentre l'API App provisioning correttamente ed è pronto per l'utilizzo. È possibile impostare manualmente le autorizzazioni tramite il portale di Azure.
- Informazioni dettagliate sui applicazione non è supportato in modelli di API App e App Mobile.
- Progetti API App non possono essere utilizzati in combinazione con i progetti di servizio Cloud.
- Modelli di progetto API App sono disponibili solo nel c#.
- Consumo API App tramite il menu di scelta rapida di "Aggiungere Azure API App Client" è disponibile solo in c#.

 
