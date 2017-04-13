<properties
    pageTitle="Azure App servizio web, mobili e le API App | Microsoft Azure"
    description="Informazioni su come servizio App Azure consente di sviluppare, distribuire e gestire web e App per dispositivi mobili."
    keywords="servizio di App, servizio app azure, app costo di assistenza, scala scalable, distribuzione delle app, distribuzione app azure, paas, piattaforma come servizio, sito Web, sito web, web, azure per dispositivi mobili"
    services="app-service"
    documentationCenter=""
    authors="omarkmsft"
    manager="erikre"
    editor="cephalin"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/26/2016"
    ms.author="omark"/>

# <a name="what-is-azure-app-service"></a>Che cos'è il servizio di App Azure?

*App* è una [piattaforma come servizio](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) che offre la possibilità di Microsoft Azure. Creare web e App per dispositivi mobili per qualsiasi piattaforma o dispositivo. Integrare le app con soluzioni SaaS, connettersi con le applicazioni in locale e automatizzare processi aziendali. Azure esegue le App in completamente gestite macchine (), con corrispondente a risorse condivise macchine Virtuali o macchine virtuali dedicate.

Servizio App include le web e le funzionalità per dispositivi mobili recapitati è precedentemente separatamente come siti Web di Azure e Azure Mobile servizi. Include inoltre nuove funzionalità per l'hosting cloud API e automazione dei processi aziendali. Come un unico servizio integrato, il servizio di App consente di comporre diversi componenti, siti Web, estremità posteriore app per dispositivi mobili, API REST e processi aziendali, ossia in un'unica soluzione.

Il video 4 minuti seguente fornisce una breve spiegazione di come servizio App si riferisce a offerte Azure precedenti e quali sono le novità al suo interno.

+[AZURE.VIDEO app-service-history-lesson]

## <a name="why-use-app-service"></a>Perché usare servizio App?

Ecco alcune caratteristiche chiave e le funzionalità di servizio App:

- **Più lingue e Framework** - servizio App supporta costrutto ASP.NET, Node, Java, PHP e Python. È anche possibile eseguire [Windows PowerShell e altri script o eseguibili](../app-service-web/web-sites-create-web-jobs.md) in macchine virtuali di servizio di App.

- **Ottimizzazione attrezzi** - configurare la [distribuzione e l'integrazione continua](../app-service-web/app-service-continuous-deployment.md) con Visual Studio Team Services, GitHub o BitBucket. Alzare di livello aggiornamenti tramite [test e ambienti di gestione temporanea](../app-service-web/web-sites-staged-publishing.md). Eseguire [A / B test](../app-service-web/app-service-web-test-in-production-get-start.md). Gestire le app nel servizio di App tramite [PowerShell Azure](../powershell-install-configure.md) o l' [interfaccia della riga di comando multipiattaforma (CLI)](../xplat-cli-install.md).

- **Global scala con disponibilità** , scala [backup](../app-service-web/web-sites-scale.md) o [fuori](../monitoring-and-diagnostics/insights-how-to-scale.md) manualmente o automaticamente. Includere le App in un punto qualsiasi in data center globale infrastruttura di Microsoft e il servizio di App [contratto di servizio](https://azure.microsoft.com/support/legal/sla/app-service/) garantisce disponibilità.

- **Connessioni a dati locali e piattaforme SaaS** - scegliere da più di 50 [connettori](../connectors/apis-list.md) per i sistemi aziendali (ad esempio SAP, Siebel e Oracle), servizi SaaS (ad esempio Salesforce e Office 365) e i servizi internet (ad esempio Facebook e Twitter). Accesso locale dati tramite [Connessioni ibrida](../biztalk-services/integration-hybrid-connection-overview.md) e [Reti virtuali Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Sicurezza e conformità** - servizio App è [ISO, SOC e PCI conforme](https://www.microsoft.com/TrustCenter/).

- **Modelli di applicazione** - scegliere da un elenco completo dei modelli di applicazione in [Azure Marketplace](https://azure.microsoft.com/marketplace/) che consentono di utilizzare una procedura guidata per installare il software open source più comune, ad esempio WordPress, Joomla e Drupal.

- **Integrazione di visual Studio** - strumenti dedicati in Visual Studio semplificare le operazioni di creazione, la distribuzione e il debug.

## <a name="app-types-in-app-service"></a>Tipi di App nel servizio di App

Servizio di App offre vari *tipi di app*, ognuno dei quali è destinato a ospitare un tipo specifico di carico di lavoro:

- [**App web**](../app-service-web/app-service-web-overview.md) - per l'hosting di applicazioni web e siti Web.

- [**App per dispositivi mobili**](../app-service-mobile/app-service-mobile-value-prop.md) Per l'hosting app per dispositivi mobili indietro estremità.

- [**API App**](../app-service-api/app-service-api-apps-why-best-platform.md) - per l'hosting API REST.

- [**Logica App**](../app-service-logic/app-service-logic-what-are-logic-apps.md) - per automazione dei processi aziendali e l'integrazione di sistemi e dati tra aree senza scrivere codice.

Word *app* qui si riferisce alle risorse di hosting dedicate all'esecuzione di un carico di lavoro. Creazione di "web app" ad esempio, si è abituati a ritenere di un'app web come risorse di elaborazione e il codice dell'applicazione che insieme offrono funzionalità a un browser. Ma nel servizio App un' *app web* le risorse di elaborazione Azure fornisce per l'hosting codice dell'applicazione. Se è composto da un'applicazione di un web front-end e API REST back-end, è possibile distribuire entrambi per un'app web o è possibile distribuire il codice front-end per un'app web e il codice di back-end per un'app API. L'applicazione può essere composto da più applicazioni di servizio App tipi diversi di.

## <a name="app-service-plans"></a>Servizio di App plan di messaggistica unificata

[Piani di servizio App](azure-web-sites-web-hosting-plans-in-depth-overview.md) specificare il tipo di risorse di elaborazione che vengono eseguite le app sui. Se si prevedono di carichi di traffico light, è possibile utilizzare macchine virtuali condivise (**gratuito** e **condivisi** prezzi livelli). Per carichi superiori, è possibile scegliere tra diversi dimensioni delle macchine virtuali dedicate (livelli**Basic**, **Standard**e **Premium** ). Più applicazioni di servizio App possono condividere lo stesso piano e la loro su e giù livelli prezzi insieme nel piano.

Se sono necessarie altre isolamento della rete e scalabilità, è possibile eseguire le App in un [Ambiente di servizi di App](../app-service-web/app-service-app-service-environment-intro.md).

## <a name="pricing"></a>Prezzi

Per informazioni sui costi quantità servizio App, vedere [App servizio prezzi](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="test-drive-app-service"></a>Testare il servizio di App

[Creare un'app web di esempio, app per dispositivi mobili o app logica](http://go.microsoft.com/fwlink/?LinkId=523751) e Riproduci usarli per 1 ora, con nessuna carta di credito non obbligatoria, impegni senza problemi.

O aprire un [account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/)e provare uno dei nostro esercitazioni Guida introduttiva:

* [Esercitazione: Creare un'app web](../app-service-web/app-service-web-get-started.md)
* [Esercitazione: Creare un'app per dispositivi mobili](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Esercitazione: Creare un'app API](../app-service-api/app-service-api-dotnet-get-started.md)
* [Esercitazione: Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md)
