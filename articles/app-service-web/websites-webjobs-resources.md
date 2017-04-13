<properties 
    pageTitle="Risorse di documentazione WebJobs Azure" 
    description="Risorse per imparare a usare WebJobs Azure e Azure WebJobs SDK consigliate." 
    services="app-service" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="tdykstra"/>

# <a name="azure-webjobs-documentation-resources"></a>Risorse di documentazione WebJobs Azure

## <a name="overview"></a>Panoramica

Questo argomento fornisce collegamenti a risorse di documentazione sull'utilizzo WebJobs Azure e Azure WebJobs SDK. Azure WebJobs offrono un modo semplice per eseguire script o programmi come processi in background nel contesto di un' [applicazione servizio web app, API app o app per dispositivi mobili](../app-service/app-service-value-prop-what-is.md). È possibile caricare ed eseguire un file eseguibile ad esempio come cmd, bat, exe (.NET), ps1, m, php, copia, js e vaso. Questi programmi vengono eseguiti come WebJobs una pianificazione (cron) o continuamente.

Lo scopo di [SDK WebJobs](websites-webjobs-resources.md) è per semplificare il codice che scritto per le attività comuni che un WebJob possono eseguire, ad esempio l'elaborazione di immagini, coda di elaborazione, RSS aggregazione, manutenzione del file e invio di messaggi di posta elettronica. SDK WebJobs sono caratteristiche incorporate per l'utilizzo di archiviazione di Azure e Bus di servizio per la pianificazione delle attività e la gestione degli errori e per molti altri scenari comuni. Inoltre, sono progettate per essere extensible ed esiste un [aprire repository di origine per le estensioni](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview). [Funzioni di Azure](../azure-functions/functions-overview.md) (attualmente nella versione di anteprima) si basa su una versione di SDK WebJobs che funziona con c# script Node e altre lingue. 

Creazione, la distribuzione e gestione WebJobs avviene con integrato utensili in Visual Studio. È possibile creare WebJobs dai modelli, pubblicare e gestire (Esegui/Interrompi/monitor/debug) loro. 

Il dashboard WebJobs nel portale di Azure offre funzionalità di gestione potenti che forniscono controllo completo sull'esecuzione di WebJobs, compresa la possibilità di richiamare singole funzioni all'interno di WebJobs. Il dashboard viene visualizzata anche funzione runtime e l'output di registrazione. 

##<a name="getstarted"></a>Introduzione a WebJobs e WebJobs SDK

* [Introduzione a WebJobs Azure](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Azure WebJobs sono amici e devono iniziare a utilizzare subito!](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html) (Post di Blog di risposta Troy).
* [Caratteristiche WebJobs Azure](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Che cos'è SDK WebJobs](websites-dotnet-webjobs-sdk.md)
* [Indicazioni di processi in background, modelli di Microsoft e procedure consigliate](/documentation/articles/best-practices-background-jobs/)
* [Annuncio di 1.1.0 RTM di Microsoft Azure WebJobs SDK](/blog/azure-webjobs-sdk-1-1-0-rtm/)
* [Guida introduttiva di Azure SDK WebJobs](websites-dotnet-webjobs-sdk-get-started.md)
* [Come utilizzare lo spazio di archiviazione di Azure coda con SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Utilizzo di archiviazione blob Azure con SDK WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Utilizzo di archiviazione tabelle Azure con SDK WebJobs](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Come utilizzare Bus di servizio Azure con SDK WebJobs](websites-dotnet-webjobs-sdk-service-bus.md)
* [Come utilizzare WebHooks con SDK WebJobs, con gli esempi per GitHub, IFTTT e HTTP](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/WebHooks-Walkthrough)
* [Azure WebJobs SDK Guida di riferimento rapido (Scarica PDF)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* [Documentazione di impostazioni WebJobs in GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* Video
    * [WebJobs e SDK WebJobs](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
    * [Serie di video WebJobs Azure su Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
    * [Introduzione a WebJobs utensili per Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [WebJobs utensili e il debug remoto](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)
    * [Aggiornamento WebJobs Azure con Pranav Rastogi - estensibilità nella versione 1.1](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-183-Azure-WebJobs-Update-with-Pranav-Rastogi)

Vedere anche nelle sezioni seguenti vengono [WebJobs la distribuzione](#deploy) e [Testing e debug WebJobs](#debug).

##<a name="deploy"></a>Distribuzione WebJobs

* [Come distribuire Azure WebJobs utilizzando Visual Studio](websites-dotnet-deploy-webjobs.md)
* [Come distribuire WebJobs tramite il portale di Azure](web-sites-create-web-jobs.md)
* [Attivazione della riga di comando o continuo recapito di Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Distribuire un'app di console .NET a Azure utilizzando WebJobs fra](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [Distribuzione di un WebJob F # in Azure](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx)
* [Distribuzione di servizi personalizzati come Webjobs Azure](http://withouttheloop.com/articles/2015-06-23-deploying-custom-services-as-azure-webjobs/)
* Video
    * [Introduzione a WebJobs utensili per Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [WebJobs utensili e il debug remoto](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>Pianificazione WebJobs

* [La finestra di dialogo WebJob Azure aggiunta](websites-dotnet-deploy-webjobs.md#configure)
* [Creare un WebJob pianificata nel portale di Azure](web-sites-create-web-jobs.md#CreateScheduled)
* [Associazione di un processo di pianificazione di un WebJob](http://blog.davidebbo.com/2015/05/scheduled-webjob.html)
* [Pianificazione WebJobs Azure con le espressioni cron](http://blog.amitapple.com/post/2015/06/scheduling-azure-webjobs/)
* [Pianificazione singole funzioni WebJob utilizzando il TimerTrigger SDK WebJobs](websites-dotnet-webjobs-sdk.md#schedule)

##<a name="debug"></a>Test e debug WebJobs

* [Nuovo sviluppatore e funzionalità di debug per Azure WebJobs in Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Visualizzare il Dashboard WebJobs](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Come scrivere i registri tramite SDK WebJobs e visualizzarle nel Dashboard](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [WebJobs debug remoto](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [Autore tale blob?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Codice di interattivi hosting nel Cloud](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Aggiunta di traccia a WebJobs Azure](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Monitorare, diagnosticare e risolvere i problemi di spazio di archiviazione di Microsoft Azure](../storage/storage-monitoring-diagnosing-troubleshooting.md)
* Video
    * [WebJobs utensili e il debug remoto](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>Ridimensionamento WebJobs

* [Ridimensionamento di un'applicazione Web di siti Web Azure](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Azure servizio App: architettura enorme scala Ready For Business Web App](https://channel9.msdn.com/Events/Build/2014/3-626). Copertine scala di applicazioni web con WebJobs, tra cui SDK WebJobs.
* Video
    * [Scalabilità WebJobs](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>Risorse aggiuntive WebJobs

* [Azure GA WebJobs post di blog Magnus Mårtensson](http://magnusmartensson.com/azure-webjobs-ga)
* [Esecuzione di processi di Powershell Web Azure del servizio di App](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Ricevere notifiche quando il Azure attivate WebJobs completa](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Criteri di conservazione Web App Backup semplici con WebJobs](https://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Azure Web App e servizi Cloud lenta alla prima richiesta](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). In questo articolo viene illustrato come utilizzare WebJobs per simulare la caratteristica AlwaysOn che è disponibile solo per il livello di prezzo Standard.
* [Spegnimento WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Spegnimento per WebJobs SDK, vedere [spegnimento](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).)
* [Per creare i backup con Azure WebJobs & AzCopy](http://markjbrown.com/azure-webjobs-azcopy/)
* Video
    * [Azure video WebJobs per Magnus Mårtensson](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
    * [Serie di video WebJobs Azure su Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Risorse aggiuntive WebJobs SDK

* [Note sulla versione SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes)
* [Codice sorgente WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk)
* [Codice sorgente estensioni WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions), guide [dettagliate per il modello di estensibilità](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).  
* [Codice sorgente WebJobs SDK Script](https://github.com/Azure/azure-webjobs-sdk-script/) (utilizzato per le [Funzioni di Azure](../azure-functions/functions-overview.md))
* [WebJob caricare file FREB in archiviazione Azure con SDK WebJobs](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Hosting Azure webjobs all'esterno di Azure, con i vantaggi di registrazione di un Azure ospitato webjob](http://bypassion.dk/?p=510)
* [Creazione di uno strumento di importazione di dati con WebJobs Azure](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* [Panoramica delle funzioni Azure](../azure-functions/functions-overview.md)
* Video
    * [Serie di video WebJobs Azure su Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>WebJob applicazioni di esempio

* [Applicazioni di esempio fornite dal team WebJobs in GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Semplice Azure Web App con WebJobs Backend tramite SDK WebJobs](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Viene illustrato l'utilizzo della WebJobs pianificata e basate su eventi. Vedere il blog [ricostruzione SiteMonitR con Azure WebJobs SDK](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

##<a name="blogs"></a>Blog

* [Blog di Azure](/blog)
* [Blog Amit Apple](http://blog.amitapple.com/). È incentrata su WebJobs (non SDK).
* [Blog di Magnus Mårtensson](http://magnusmartensson.com/)

##<a name="gethelp"></a>Visualizzazione della Guida con WebJobs

* [StackOverflow per WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow SDK WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [StackOverflow per le funzioni di Azure](http://stackoverflow.com/questions/tagged/azure-functions)
* [Forum su Azure e ASP.NET](http://forums.asp.net/1247.aspx)
* [Forum di Azure App servizio Web Apps](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Sito Web App utente vocali Azure](https://feedback.azure.com/forums/169385-websites/)
* [Twitter](http://twitter.com/). Usare hashtag #AzureWebJobs.
* [Inviare una segnalazione WebJobs bug o problema](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues)

