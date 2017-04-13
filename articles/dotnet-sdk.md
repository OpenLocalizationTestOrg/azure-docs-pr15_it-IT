<properties
    pageTitle="Che cos'è Azure .NET SDK"
    description="Informazioni su cosa è incluso in .NET SDK Azure."
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor="mollybos"
    services=""/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="rachelap"/>

# <a name="what-is-the-azure-sdk-for-net"></a>Che cos'è il Azure SDK per .NET?

## <a name="overview"></a>Panoramica

Azure SDK per .NET è un set di strumenti di Visual Studio, strumenti della riga di comando, binari runtime e le raccolte di client che consentono di sviluppano, testare e distribuire applicazioni che vengono eseguite in Azure. In questo articolo viene descritta viene visualizzato quando si installa il SDK. È possibile scaricare il SDK dalla [pagina Download Azure](https://azure.microsoft.com/downloads/).

Azure SDK per .NET comprende anche [le raccolte di client per richiedere servizi Azure](http://go.microsoft.com/fwlink/?LinkId=510472). Le raccolte vengono installate separatamente utilizzando NuGet.

##<a id="included"></a>Che cos'è inclusa in Azure SDK per .NET

Azure SDK per .NET installazioni seguenti prodotti:

- [Visual Studio Community Edition 2015](#vwd)
- [Emulatore lo spazio di archiviazione di Microsoft Azure](#stgemulator)
- [Strumenti di spazio di archiviazione di Microsoft Azure](#stgtools)
- [Strumenti di creazione condivisa di Microsoft Azure](#auth)
- [Emulatore Microsoft Azure](#emulator)
- [HDInsight Tools per Visual Studio e Microsoft Hive Driver ODBC](#hdinsight)
- [Raccolte di Microsoft Azure per .NET](#libraries)
- [SDK App per dispositivi mobili Microsoft Azure](#mobile)
- [PowerShell di Microsoft Azure](#ps)
- [Strumenti di Microsoft Azure per Microsoft Visual Studio](#tools)
- [Microsoft ASP.NET e Web Tools per Visual Studio](#wte)
- [Microsoft Azure dati Lake Tools per Visual Studio](#datalake)

###<a id="vwd"></a>Visual Studio Community Edition 2015

Se non si dispone di Visual Studio nel computer in uso, SDK verrà installato [Visual Studio Community Edition 2015](https://www.visualstudio.com/products/visual-studio-community-vs).

###<a id="stgemulator"></a>Emulatore lo spazio di archiviazione di Microsoft Azure

[Azure archiviazione emulatore](http://msdn.microsoft.com/library/hh403989.aspx) utilizza un'istanza di SQL Server e nel file system locale per simulare lo spazio di archiviazione di Azure (code, tabelle, BLOB), in modo che è possibile verificare in locale.

###<a id="stgtools"></a>Strumenti di spazio di archiviazione di Microsoft Azure

In questo modo si installa [AzCopy](http://aka.ms/AzCopy), uno strumento della riga di comando, è possibile utilizzare per trasferire i dati in e disconnettersi da un account di archiviazione Azure.

###<a id="auth"></a>Strumenti di creazione condivisa di Microsoft Azure

Sono incluse le operazioni seguenti:

* Lo [strumento della riga di comando CSPack](http://msdn.microsoft.com/library/gg432988.aspx) per la creazione di pacchetti di distribuzione.
* [strumento della riga di comando CSEncrypt](http://msdn.microsoft.com/library/hh404001.aspx) per la crittografia delle password utilizzati per accedere a istanze del ruolo di servizio cloud tramite una connessione desktop remoto.
* I progetti di servizi cloud binari runtime richiedono per comunicare con il proprio ambiente di runtime e per diagnostica. Questi file binari non sono disponibili in pacchetti NuGet.

###<a id="emulator"></a>Emulatore Microsoft Azure

[Azure emulatore](http://msdn.microsoft.com/library/dn339018.aspx) consente di simulare l'ambiente di servizio cloud, in modo che è possibile verificare i progetti di servizi cloud in locale nel computer in uso prima dell'implementazione Azure.

###<a id="hdinsight"></a>HDInsight Tools per Visual Studio e Microsoft Hive Driver ODBC

Strumenti HDInsight in Esplora Server consentono di esplorare database Hive e gli account di archiviazione collegate per i cluster HDInsight, creare tabelle e creare e inviare le query Hive. Per ulteriori informazioni, vedere [Introduzione all'utilizzo di HDInsight Hadoop Tools per Visual Studio](hdinsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

###<a id="libraries"></a>Raccolte di Microsoft Azure per .NET

Sono incluse le operazioni seguenti:

* Pacchetti NuGet per lo spazio di archiviazione di Azure, Bus di servizio e memorizzazione nella cache che sono archiviati nel computer in modo che Visual Studio consente di creare nuovi progetti di servizio durante la modalità offline.
* Visual Studio plug-in che consente di progetti [In ruolo Cache](http://msdn.microsoft.com/library/dn386103.aspx) per l'esecuzione in locale in Visual Studio.

###<a id="mobile"></a>SDK App per dispositivi mobili Microsoft Azure

Strumenti per l'utilizzo di [Azure App servizio Mobile App](app-service-mobile/app-service-mobile-value-prop.md).

###<a id="ps"></a>PowerShell di Microsoft Azure

PowerShell Azure consente di [automatizzare la distribuzione e la creazione dell'ambiente Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

###<a id="tools"></a>Strumenti di Microsoft Azure per Microsoft Visual Studio

In questo modo è possibile gestire le risorse Azure, principalmente servizi Cloud e macchine virtuali:

* [Creazione, apertura e pubblicare i progetti di servizi cloud](cloud-services/cloud-services-dotnet-get-started.md).
* [Creare i pacchetti di distribuzione per i progetti di servizio cloud](http://msdn.microsoft.com/library/ff683672.aspx).
* [Creare macchine virtuali di Azure durante la creazione di nuovi progetti web](virtual-machines/virtual-machines-windows-classic-web-app-visual-studio.md).
* [Script di PowerShell creare durante la creazione di nuove macchine virtuali](http://msdn.microsoft.com/library/dn642480.aspx).
* [Visualizzare e gestire le impostazioni di progetto di servizio cloud nelle finestre di proprietà dei progetti di Visual Studio](http://msdn.microsoft.com/library/ee405486.aspx).
* Visualizzare e gestire [servizi cloud](http://msdn.microsoft.com/library/ff683675.aspx), [macchine virtuali](http://msdn.microsoft.com/library/jj131259.aspx)e [Bus di servizio](http://msdn.microsoft.com/library/jj149828.aspx) in Esplora Server.
* [Esegui in modalità di debug in remoto per servizi cloud e macchine virtuali](http://msdn.microsoft.com/library/ff683670.aspx).
* [Automatizzare il provisioning di risorse con progetti di distribuzione di Azure risorse gruppo](https://msdn.microsoft.com/library/dn872471.aspx)

###<a id="wte"></a>Servizio di Microsoft App Tools per Visual Studio

In questo modo per gestire i siti Web di Azure:

* [Progetti web pubblica ai siti Web di Azure](app-service-web/web-sites-dotnet-get-started.md).
* [Progetti di Azure WebJobs applicazione console di pubblicazione](app-service-web/websites-dotnet-deploy-webjobs.md).
* [Sito Web di Azure crea e Database SQL di risorse durante la creazione di un nuovo progetto web o durante la pubblicazione di un progetto web](app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
* [Script di PowerShell creare distribuzione durante la creazione di nuovi siti Web](http://msdn.microsoft.com/library/dn642480.aspx).
* [Gestire e risolvere i problemi di siti Web di Azure in Esplora Server](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#sitemanagement).
* [Esegui in modalità di debug in remoto per i siti Web e WebJobs](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug).

>[AZURE.NOTE] Non è necessario installare Azure SDK per .NET per usare queste caratteristiche; sono inclusi anche negli aggiornamenti di Visual Studio.

##<a id="datalake"></a>Microsoft Azure dati Lake Tools per Visual Studio

Per ulteriori informazioni, vedere [Esercitazione: sviluppare script U SQL mediante dati Lake Tools per Visual Studio](data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

##<a id="notincluded"></a>Gli elementi che non sono inclusi durante l'installazione di Azure SDK per .NET

Esistono alcune operazioni che è consigliabile per lo sviluppo di Azure che non sono inclusi durante l'installazione di SDK. Più importanti di questi sono i seguenti:

* [Raccolte di client](http://go.microsoft.com/fwlink/?LinkId=510472).

    Azure SDK include librerie di client per l'utilizzo di servizi di Azure, ma non tutti sono installati durante l'installazione di SDK. Se l'applicazione necessita di una raccolta di client SDK non viene installato, è possibile ottenere da [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Se l'applicazione utilizza una raccolta di client SDK di installazione, è consigliabile aggiornarlo con la versione corrente nella NuGet.org.

    **Copie locali dei librerie client.** Azure SDK per .NET copia nel computer in uso pacchetti NuGet per alcune librerie di Azure client, ad esempio lo spazio di archiviazione, Bus di servizio e memorizzazione nella cache. Queste librerie client vengono automaticamente inclusi nei nuovi progetti di servizio cloud, in modo che i pacchetti NuGet locali abilitare Visual Studio per creare progetti, anche se non si è connessi a Internet. Librerie client vengono in genere aggiornate più spesso vengono rilasciate nuove versioni SDK, in modo che le librerie client in NuGet.org sono spesso più aggiornate rispetto a quelle viene visualizzato con SDK.

    **Modelli di progetto che includono librerie client.** Solo i modelli di progetto [Servizio Cloud di Azure](cloud-services/cloud-services-dotnet-get-started.md) e Azure App servizio [App Mobile](./app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) includono automaticamente alcune librerie client. Per altre raccolte o altri modelli, installare [i pacchetti NuGet raccolta client](http://go.microsoft.com/fwlink/?LinkId=510472) necessari.

* [Modelli di progetto App Mobile](./app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

    Modelli di App mobile sono disponibili solo in Visual Studio 2013 aggiornamento 2 e versioni successive. Non sono disponibili in Visual Studio 2012 o versioni precedenti e non in Visual Studio 2013 aggiornamento 1 o versioni precedenti, anche se si installa Azure SDK per .NET.

##<a id="faq"></a>Domande frequenti

- [Molte caratteristiche Azure sono già in Visual Studio. È necessario installare Azure SDK per .NET?](#azinvs)
- [Si desidera una libreria client. È necessario installare Azure SDK per .NET ottenerlo?](#clientlib)
- [Dove reperibili versioni precedenti di Azure SDK per .NET](#olderversions)
- [Informazioni sui criteri del ciclo di vita per le versioni di Azure SDK per .NET](#lifecycle)
- [Quali sono le versioni del sistema operativo guest è compatibile con Azure SDK per .NET?](#guestos)
- [Come disinstallare Azure SDK per .NET?](#uninstall)

###<a id="azinvs"></a>Molte caratteristiche Azure sono già in Visual Studio. È necessario installare Azure SDK per .NET?

È consigliabile installare SDK se si desidera sviluppare per Azure usando gli strumenti più recente. Se si preferisce non installare SDK, è possibile eseguire questa operazione se le seguenti condizioni sono vere:

* È stato installato l'ultimo [Aggiornamento di Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5).
* Si sviluppano soluzioni solo per i siti Web di Azure o servizi Mobile, non per i servizi Cloud e/o virtuali.
* L'applicazione non utilizza un sistema di archiviazione, o utilizza lo spazio di archiviazione, ma non è necessario emulatore lo spazio di archiviazione o lo strumento AzCopy.

###<a id="clientlib"></a>Si desidera una libreria client. È necessario installare Azure SDK per .NET ottenerlo?

SDK installa librerie client solo, è possibile creare cloud servizio progetti anche se non si è connessi a Internet. Raccolte di client corrente sono disponibili in pacchetti NuGet [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Per ulteriori informazioni, vedere [gli elementi che non sono inclusi durante l'installazione di Azure SDK per .NET](#notincluded) in precedenza in questo documento.

###<a id="olderversions"></a>Dove reperibili versioni precedenti di Azure SDK per .NET

Per le versioni precedenti, vedere [Azure SDK per .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) pagina di download.

###<a id="lifecycle"></a>Informazioni sui criteri del ciclo di vita per le versioni di Azure SDK per .NET

Vedere [servizi Cloud Microsoft Azure supportano criteri del ciclo di vita](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq).

###<a id="guestos"></a>Quali sono le versioni del sistema operativo guest è compatibile con Azure SDK per .NET?

Vedere [le versioni del sistema operativo Guest Azure e nella matrice compatibilità SDK](http://msdn.microsoft.com/library/ee924680.aspx).

###<a id="uninstall"></a>Come disinstallare Azure SDK per .NET?

Ogni elemento illustrata in questo articolo in [quali applicazioni sono incluse in Azure SDK per .NET](#included) è un programma separato nell'elenco dei programmi installati nel Pannello di controllo di Windows **programmi e funzionalità**.  Non è possibile disinstallarle come un gruppo. è necessario disinstallare tutti i programmi singolarmente.

Quando si dispone di Azure SDK per .NET già installato e si installa una nuova versione, non è in genere necessario disinstallare quella precedente. Nella maggior parte dei casi, l'installazione di SDK aggiornato un programma esistente invece di aggiungerne uno nuovo e lasciare il vecchio.

Tuttavia, se si desidera rimuovere no-più componenti necessari residui di una versione precedente, disinstallare solo i programmi che specificano il numero di versione precedente e disinstallarle solo se è presenta lo stesso programma con una versione più recente. Ad esempio, dopo l'aggiornamento da 2,5 a 2.6 venga visualizzato versioni 2.5 e 2.6 di "Strumenti di Microsoft Azure per Microsoft Visual Studio 2013" ed è possibile disinstallare la versione 2.5. Può essere visualizzata solo la versione 2.5 di "Microsoft Azure Authoring Tools", ma non essere sicuro disinstallare che.

Si noti che i numeri di versione in titoli di programmi visualizzati in **programmi e funzionalità** possono essere fuorvianti.  Ad esempio, SDK versione 2.6 include "Microsoft Azure Mobile App SDK 1.0" Se si installa il SDK per Visual Studio 2013 e "Microsoft Azure Mobile App SDK 2.0" per Visual Studio 2015. in questo caso la versione non è la versione SDK ma un indicatore di quale versione di Visual Studio il programma si applica a.

In questo articolo non sono elencati tutti i programmi che tutte le versioni precedenti di Azure SDK incluso; sono disponibili altri programmi che di versioni precedenti SDK, è possibile disinstallare perché le versioni precedenti di SDK incluso in alcuni casi programmi che sono stati omessi dalle versioni successive. Ad esempio, versione 2.5 installazioni "Azure risorsa Manager Tools per Visual Studio" ma che non è nell'elenco di questo articolo perché questo non verrà visualizzato come programma separato in **programmi e funzionalità**.  Questo articolo elenca solo le applicazioni che sono incluse in Azure SDK per .NET versione 2.6.

> **Nota:** Alcuni dei programmi che include il SDK potrebbe anche essere installato separatamente in altri contesti e potrebbero essere necessarie anche se non è necessario SDK completo. Lo stesso deve essere vero dei programmi installati dalle versioni precedenti di SDK ma non sono stati omessi dalle versioni successive SDK. Quando si disinstalla programmi, prestare attenzione evitare di rimuovere un elemento ancora necessario nel computer in uso.



##<a id="versions"></a>Versioni

Per verificare che la versione corrente o per scaricare le versioni precedenti, vedere la pagina di [Azure SDK per .NET cronologia](https://azure.microsoft.com/downloads/archive-net-downloads/) .

##<a id="resources"></a>Risorse

Per scaricare il corrente di Azure SDK per .NET o una raccolta di client, vedere la [pagina Download Azure](https://azure.microsoft.com/downloads/).

Per Azure SDK per codice sorgente .NET, incluse le librerie client, vedere [GitHub.com/Azure](https://github.com/azure/).

Per la documentazione della Guida di riferimento raccolta client Azure, vedere [Informazioni di riferimento .NET Azure](https://azure.microsoft.com/documentation/api/).
