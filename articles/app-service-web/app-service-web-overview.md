<properties
    pageTitle="Panoramica di applicazioni Web | Microsoft Azure"
    description="Informazioni su come servizio App Azure consente di sviluppare e ospitare applicazioni web"
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/28/2016"
    ms.author="cephalin"/>

# <a name="web-apps-overview"></a>Panoramica di Web App

*Applicazione del servizio Web Apps* è una piattaforma di elaborazione completamente gestita ottimizzate per l'hosting di applicazioni web e siti Web. Questa offerta di [piattaforma come servizio](https://en.wikipedia.org/wiki/Platform_as_a_service) PaaS () di Microsoft Azure consente è mettere in risalto logica aziendale mentre Azure si occupa dell'infrastruttura per eseguire e ridimensionare le app.

Il video di 5 minuti seguente introduce Azure App servizio Web Apps.

[AZURE.VIDEO azure-app-service-web-apps-with-yochay-kiriaty]

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="what-is-a-web-app-in-app-service"></a>Che cos'è un'app web nel servizio App?

Nel servizio App un' *app web* è le risorse di elaborazione Azure sono disponibili per l'hosting di una sito Web o un'applicazione web.  

Le risorse di elaborazione potrebbero essere in condivise o dedicate macchine (), a seconda del livello prezzo che si è scelto. Il codice dell'applicazione viene eseguito in una macchina gestita isolato da altri utenti.

Il codice può essere in qualsiasi lingua o framework supportate dal [Servizio di App Azure](../app-service/app-service-value-prop-what-is.md), ad esempio ASP.NET, Node, Java, PHP o Python. È anche possibile eseguire script che utilizzano [PowerShell e altri linguaggi di script](web-sites-create-web-jobs.md#acceptablefiles) in un'app web.

Per esempi di scenari di applicazioni tipici che è possibile utilizzare Web Apps per, vedere la sezione **suggerimenti e scenari** di [servizio App Azure, il confronto macchine virtuali, tessuti, servizio e servizi Cloud](choose-web-site-cloud-service-vm.md#scenarios)e [scenari di app Web](https://azure.microsoft.com/documentation/scenarios/web-app/) .

## <a name="why-use-web-apps"></a>Perché usare Web App?

Ecco alcune caratteristiche chiave di App servizio che si applicano alle Web App:

- **Più lingue e Framework** - servizio App supporta costrutto ASP.NET, Node, Java, PHP e Python. È anche possibile eseguire [PowerShell e altri script o eseguibili](../app-service-web/web-sites-create-web-jobs.md) in macchine virtuali di servizio di App.

- **Ottimizzazione attrezzi** - configurare la [distribuzione e l'integrazione continua](../app-service-web/app-service-continuous-deployment.md) con Visual Studio Team Services, GitHub o BitBucket. Alzare di livello aggiornamenti tramite [test e ambienti di gestione temporanea](../app-service-web/web-sites-staged-publishing.md). Eseguire [A / B test](../app-service-web/app-service-web-test-in-production-get-start.md). Gestire le app nel servizio di App tramite [PowerShell Azure](../powershell-install-configure.md) o l' [interfaccia della riga di comando multipiattaforma (CLI)](../xplat-cli-install.md).

- **Global scala con disponibilità** , scala [backup](../app-service-web/web-sites-scale.md) o [fuori](../monitoring-and-diagnostics/insights-how-to-scale.md) manualmente o automaticamente. Includere le App in un punto qualsiasi in data center globale infrastruttura di Microsoft e il servizio di App [contratto di servizio](https://azure.microsoft.com/support/legal/sla/app-service/) garantisce disponibilità.

- **Connessioni a dati locali e piattaforme SaaS** - scegliere da più di 50 [connettori](../connectors/apis-list.md) per i sistemi aziendali (ad esempio SAP, Siebel e Oracle), servizi SaaS (ad esempio Salesforce e Office 365) e i servizi internet (ad esempio Facebook e Twitter). Accesso locale dati tramite [Connessioni ibrida](../biztalk-services/integration-hybrid-connection-overview.md) e [Reti virtuali Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Sicurezza e conformità** - servizio App è [ISO, SOC e PCI conforme](https://www.microsoft.com/TrustCenter/).

- **Modelli di applicazione** - scegliere da un elenco completo dei modelli di applicazione in [Azure Marketplace](https://azure.microsoft.com/marketplace/) che consentono di utilizzare una procedura guidata per installare il software open source più comune, ad esempio WordPress, Joomla e Drupal.

- **Integrazione di visual Studio** - strumenti dedicati in Visual Studio semplificare le operazioni di creazione, la distribuzione e il debug.

Inoltre, un'app web possa usufruire delle funzionalità offerte dalle [App API](../app-service-api/app-service-api-apps-why-best-platform.md) (ad esempio CORS supporto) e [App Mobile](../app-service-mobile/app-service-mobile-value-prop.md) (ad esempio le notifiche push). Per ulteriori informazioni sui tipi di app nel servizio di App, vedere [Panoramica sul servizio App Azure](../app-service/app-service-value-prop-what-is.md).

Oltre ai Web Apps nel servizio di App, Azure offre altri servizi che possono essere usate per l'hosting di applicazioni web e siti Web. Per la maggior parte dei casi, Web Apps è la scelta migliore.  Per microservice architettura, valutare la possibilità di [Servizio tessuti](https://azure.microsoft.com/documentation/services/service-fabric)e se è necessario un maggiore controllo macchine virtuali che il codice viene eseguita nel, è consigliabile [macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Per ulteriori informazioni su come scegliere tra questi servizi Azure, vedere [servizio App Azure, macchine virtuali, tessuti servizio e servizi Cloud confronto](choose-web-site-cloud-service-vm.md).

## <a name="getting-started"></a>Guida introduttiva

Per informazioni introduttive per la distribuzione di codice di esempio in una nuova app web nel servizio di App, seguire l'esercitazione [distribuire un'applicazione web prima di Azure 5 minuti](app-service-web-get-started.md) . È necessario un account Azure gratuito.

Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.
