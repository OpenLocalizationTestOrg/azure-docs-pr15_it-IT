<properties
   pageTitle="Passaggi successivi tessuti servizio progetto creazione | Microsoft Azure"
   description="In questo articolo vengono forniti collegamenti a un insieme di attività di sviluppo principali per tessuti servizio"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# <a name="your-service-fabric-application-and-next-steps"></a>L'applicazione di servizio tessuti e i passaggi successivi
L'applicazione di Azure servizio tessuti è stata creata. Questo articolo descrive la struttura del progetto e alcuni passaggi successivi potenziali.

## <a name="your-application"></a>L'applicazione
Ogni nuova applicazione include un progetto di applicazione. Può essere uno o due altri progetti, a seconda del tipo di servizio scelto.

### <a name="the-application-project"></a>Il progetto di applicazione
Il progetto di applicazione è costituito da:

- Un set di riferimenti ai servizi che costituiscono l'applicazione.

- Due pubblicare profili (locali e Cloud) che è possibile utilizzare per gestire le preferenze per l'utilizzo di ambienti diversi, ad esempio le preferenze relative a un endpoint cluster e se è necessario eseguire l'aggiornamento distribuzioni per impostazione predefinita.

- Due applicazione parametro file (locali e Cloud) che è possibile utilizzare per gestire le configurazioni specifiche dell'ambiente applicazione, ad esempio il numero di partizioni per creare un servizio.

- Script di distribuzione che è possibile utilizzare per distribuire l'applicazione dalla riga di comando o all'interno di una pipeline automatizzata integrazione e la distribuzione continua.

- Manifesto dell'applicazione che descrive l'applicazione. È possibile trovare il manifesto nella cartella ApplicationPackageRoot.

### <a name="stateless-service"></a>Servizio senza informazioni sullo stato
Quando si aggiunge un nuovo servizio senza informazioni sullo stato, Visual Studio viene aggiunto un progetto di servizio alla soluzione che include un tipo da `StatelessService`. Il servizio viene incrementato una variabile locale in un contatore.

### <a name="stateful-service"></a>Servizio informazioni sullo stato
Quando si aggiunge un nuovo servizio informazioni sullo stato, Visual Studio viene aggiunto un progetto di servizio alla soluzione che include un tipo da `StatefulService`. Il servizio viene incrementato un contatore nella relativa `RunAsync` metodo e memorizzare i risultati in un `ReliableDictionary`.

### <a name="actor-service"></a>Servizio attore
Quando si aggiunge un nuovo attore affidabile, Visual Studio viene aggiunto due progetti alla soluzione: un progetto dell'attore e un progetto di interfaccia.

Il progetto attore fornisce metodi per l'impostazione e ottenere il valore di un contatore viene mantenuto in modo affidabile all'interno dello stato dell'attore. Il progetto interfaccia offre un'interfaccia utilizzabile per richiamare l'attore altri servizi.

### <a name="stateless-web-api"></a>Dell'API Web senza informazioni sullo stato
Il progetto API Web senza informazioni sullo stato fornisce un servizio web di base che è possibile utilizzare per aprire l'applicazione client esterni. Per ulteriori informazioni su come project strutturati, vedere [servizi API del servizio Web di tessuti con OWIN hosting](service-fabric-reliable-services-communication-webapi.md).

### <a name="aspnet-core"></a>ASP.NET di base

Service tessuti SDK fornisce lo stesso set di ASP.NET di base modelli disponibili per autonoma progetti ASP.NET di base: svuotare, [API Web][aspnet-webapi]e [l'Applicazione Web][aspnet-webapp].

## <a name="next-steps"></a>Passaggi successivi
### <a name="create-an-azure-cluster"></a>Creare un cluster di Azure
Service tessuti SDK fornisce un cluster locale per sviluppo e verifica. Per creare un cluster di Azure, vedere [impostazione di un cluster di servizio tessuti dal portale di Azure][create-cluster-in-portal].

### <a name="try-deploying-to-azure-for-free-with-party-clusters"></a>Provare la distribuzione di Azure gratuitamente con i cluster di terze parti

Se si vuole provare la distribuzione e gestione applicazioni in Azure senza impostare il proprio cluster, è possibile utilizzare il [servizio cluster di terze parti](http://aka.ms/tryservicefabric)gratuito.

### <a name="publish-your-application-to-azure"></a>Pubblicare l'applicazione in Azure
È possibile pubblicare l'applicazione direttamente da Visual Studio a un cluster di Azure. Per ulteriori informazioni, vedere [la pubblicazione dell'applicazione in Azure][publish-app-to-azure].

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>Servizio tessuti Explorer consente di visualizzare il cluster
Servizio tessuti Explorer offre un modo semplice per visualizzare il cluster, tra cui le applicazioni distribuite e layout fisico. Per ulteriori informazioni, vedere [la visualizzazione del cluster tramite Esplora file tessuti servizio][visualize-with-sfx].

### <a name="version-and-upgrade-your-services"></a>Versione e all'aggiornamento dei servizi
Servizio tessuti consente versioni indipendenti e l'aggiornamento dei servizi indipendenti in un'applicazione. Per ulteriori informazioni, vedere [aggiornare il provider di servizi e controllo delle versioni][app-upgrade-tutorial].

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>Configurare l'integrazione continua con Visual Studio Team Services
Per informazioni su come è possibile impostare un processo continuo integrazione per l'applicazione di servizio tessuti, vedere [configurare continua integrazione con Visual Studio Team Services][ci-with-vso].


<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html
