
<properties 
   pageTitle="Note sulla versione di Azure SDK per .NET 2.7 e .NET 2.7.1" 
   description="Note sulla versione di Azure SDK per .NET 2.7 e .NET 2.7.1" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/17/2016"
   ms.author="juliako"/>

# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Note sulla versione di Azure SDK per .NET 2.7 e .NET 2.7.1

##<a name="overview"></a>Panoramica

In questo documento contiene le note sulla versione di Azure SDK versione 2.7 .NET. 

Il documento contiene anche le note sulla versione per Azure SDK per .NET 2.7.1 rilasciare.

Azure SDK 2.7 è supportata solo in Visual Studio 2015 e Visual Studio 2013. [Azure SDK 2.6](https://azure.microsoft.com/downloads/) è che l'ultimo supportate SDK per Visual Studio 2012.

Per informazioni dettagliate su questa versione, vedere [annuncio di Azure SDK 2.7 post](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) e [annuncio di Azure SDK 2.7.1 post](http://go.microsoft.com/fwlink/?LinkId=623850).

##<a name="azure-sdk-for-net-27"></a>Azure SDK per .NET 2.7

###<a name="sign-in-improvements-for-visual-studio-2015"></a>Accedere miglioramenti per Visual Studio 2015

Azure 2.7 SDK per Visual Studio 2015 supporta le nuove caratteristiche di gestione delle identità in Visual Studio 2015.  Incluso il supporto per gli account di accesso Azure tramite controllo dell'accesso basato su ruoli, fornitori di soluzioni Cloud, DreamSpark e altri tipi di account e abbonamento.

I miglioramenti di accesso inclusi con Azure SDK 2.7 sono disponibili solo in Visual Studio 2015. Supporto per Visual Studio 2013 è incluso in Azure SDK 2.7.1.


###<a name="mobile-sdk"></a>SDK per dispositivi mobili

Aggiornare i modelli di **App Mobile** in modo da rispecchiare processo [pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) e configurazione più recente.

###<a name="service-bus"></a>Bus di servizio 

Miglioramenti e correzioni di bug generale. Per informazioni sulle caratteristiche e gli aggiornamenti, fare riferimento alle note sulla versione del più recente [Del servizio Bus NuGet](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

###<a name="hdinsight-tools"></a>Strumenti HDInsight 

In questa versione sono state aggiornate le seguenti. Gli aggiornamenti sono in anteprima. Per ulteriori informazioni, vedere [questo blog](http://go.microsoft.com/fwlink/?LinkId=619108).

- Grafici hive per Hive sui processi Tez
- Supporto completo Hive IntelliSense DML
- Supporto del modello maialino
- Modelli di eccesso per i servizi di Azure

####<a name="breaking-changes"></a>Ultime modifiche

- Quando si utilizza questa versione degli strumenti, è necessario aggiornare il progetto **eccesso** precedente. Per ulteriori informazioni, vedere [questo blog](http://go.microsoft.com/fwlink/?LinkId=619108).
- Visual Studio Web Express non è più supportata. Per ulteriori informazioni, vedere [questo blog](http://go.microsoft.com/fwlink/?LinkId=619108).

###<a name="azure-app-service-tools"></a>Strumenti di Azure App servizio

In questa versione sono stati apportati gli aggiornamenti seguenti estensioni degli strumenti Web. Per ulteriori informazioni, vedere [questo](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blog. 

- Supporto per gli account DreamSpark aggiunti
- Modifica completo agli strumenti di Azure apportate per supportare le nuove API di gestione delle risorse di Azure
- Supporto per il servizio di App Azure aggiunto a [Soluzioni Cloud](#cloud_explorer)

####<a name="known-issues"></a>Problemi noti

Nodi di un intervallo di aperto distribuzione App Web non vengono visualizzati sotto il nodo bande orarie in Esplora Server e nodi figlio intervallo aperto di distribuzione Web App non vengono caricate in Esplora risorse Cloud. Questo problema è stati risolto e preparato per la prossima versione SDK. 


###<a name="cloud_explorer"></a>Esplora cloud per Visual Studio 2015

Azure 2.7 SDK include Cloud Explorer per Visual 2015 Studio che consente di visualizzare le risorse Azure, controllare le relative proprietà ed eseguire azioni di sviluppo chiave da Visual Studio. 

Cloud explorer supporta le operazioni seguenti:

- Gruppo di risorse e tipo di risorsa visualizzazioni delle risorse di Azure 
- Ricerca di risorse in base al nome (disponibile nella visualizzazione del tipo di risorsa)
- Supporto per sottoscrizioni e risorse che dispongono di ruoli in base a Access controllo (RBAC) applicato 
- Pannello azione integrata che mostra gli sviluppatori azioni specifiche per le risorse selezionate. Ad esempio: connettere debugger remoto per macchine virtuali create con Manager delle risorse Stack, visualizzare i dati di diagnostica per macchine virtuali e così via.
- Pannello proprietà integrata che mostra le proprietà incentrati sui sviluppo simboli necessari durante dev/test 
- Passaggio rapido dell'account da utilizzare durante l'enumerazione risorse (comando impostazioni sulla barra degli strumenti) 
- Filtro degli abbonamenti da utilizzare quando enumerazione delle risorse (comando impostazioni sulla barra degli strumenti) 
- Collegamenti complete al portale di Azure per la gestione delle risorse e gruppi di risorse 
 
 
###<a name="azure-resource-manager-tools"></a>Strumenti di gestione risorse di Azure 

Gli strumenti di gestione risorse Azure sono stati aggiornati per lavorare con ruolo in base a Access controllo (RBAC) e nuovi tipi di sottoscrizione.  Incluso in queste modifiche è la possibilità di usare i nuovi account di archiviazione, oltre al sistema di archiviazione classico, per archiviare gli elementi durante la distribuzione.  

Se si usa un progetto di gruppo di risorse Azure da una versione precedente di SDK con 2.7 SDK, è necessario un nuovo script di distribuzione per distribuire tramite un nuovo account di archiviazione invece che lo spazio di archiviazione classica.  Verrà richiesto prima di apportare modifiche al progetto per aggiungere il nuovo script.  Lo script precedente verrà rinominato e sarà necessario apportare manualmente le modifiche per il nuovo script.
 
 
###<a name="storage-explorer-tools"></a>Strumenti di Explorer lo spazio di archiviazione 

- Supporto per la visualizzazione accodare BLOB. Altre informazioni in [questo post di blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
- Supporto per la visualizzazione, gli account di archiviazione Premium tramite Esplora Server. Esplora server verrà visualizzati solo BLOB di pagina per gli account di archiviazione premium così come sono l'unico tipo supportato per gli account di archiviazione premium.

###<a name="azure-data-factory-tools-for-visual-studio"></a>Dati di Azure Factory Tools per Visual Studio 

Introduzione **agli strumenti Factory dati Azure** per Visual Studio. Di seguito sono caratteristiche abilitate. [In questo blog](http://go.microsoft.com/fwlink/?LinkId=617530) per ulteriori informazioni, vedere.

- **Creazione di modello in base a**: modelli, modelli di spostamento dei dati o i modelli di elaborazione dei dati per distribuire una soluzione di integrazione dati-to-end e introduzione pratica rapidamente con Factory dati in base a selezionare Usa le maiuscole/minuscole. 
- **Integrazione con Esplora risorse per la creazione e distribuzione di entità Factory dati**: creare e distribuire tubazioni ed entità correlate come progetti di Visual Studio. 
- **Integrazione con diagramma visualizzare per l'interazione visivo durante la creazione**: visivamente dell'autore tubazioni e set di dati con aiuto dalla visualizzazione Diagramma. 
- **Integrazione con Esplora Server per l'esplorazione e interazione con entità già distribuita**: sfruttare le soluzioni di Server a Sfoglia già distribuito dati factory ed entità corrispondenti. Importare una fabbrica dati distribuito o qualsiasi entità (Pipeline, servizi collegati, set di dati) nel progetto. 
- **Modifica JSON con la convalida dello schema e intellisense RTF**: in modo efficiente configurare e modificare documenti JSON di entità Factory dati con convalida intellisense e schema RTF 
- **Pubblicazione di più ambienti**: pubblicare creato pipeline di sviluppo, test o ambiente di produzione mediante la creazione di file di configurazione distinta per ogni ambiente.
- **Supporto di elaborazione dei dati in base a maialino, Hive e .net**: il supporto per maialino e Hive script nel progetto Factory dati. Supporto per il progetto c# di riferimento per la gestione di .net attività.

##<a name="azure-sdk-for-net-271"></a>Azure SDK per .NET 2.7.1

La sezione seguente contiene aggiornamenti che sono stati introdotti con Azure SDK per .NET 2.7.1 rilasciare.
###<a name="hdinsight-tools"></a>Strumenti HDInsight 

Per ulteriori informazioni sugli aggiornamenti strumenti HDInsight, vedere [questo blog](http://go.microsoft.com/fwlink/?LinkId=623831).

- Visualizzazione di operatore processo hive (una nuova caratteristica)

    Per comprendere meglio la query Hive migliorato, la caratteristica di vista dell'operatore Hive è stato aggiunto. Per visualizzare tutti gli operatori all'interno di un vertice, fare doppio clic sui vertici del grafico di processo. Per visualizzare ulteriori dettagli di un operatore specifico, passare il mouse su tale operatore.
- Indicatore di errore hive (una nuova caratteristica)

    Per consentire di visualizzare gli errori grammaticali immediatamente, la caratteristica Hive indicatore errore è stato aggiunto. Inoltre, sono stati migliorati messaggi di errore ed è ora possibile visualizzare gli errori grammaticali dettagliate istantaneamente (fino a questa versione, era necessario inviare uno script Hive al cluster e attendere che venga po' di tempo prima di ottenere i dettagli del messaggio di errore).  
- Grafico di topologia eccesso (una nuova caratteristica)

    La visualizzazione è molto importante quando si desidera visualizzare se la topologia funziona come previsto. In questa versione è aggiunta la visualizzazione per i grafici eccesso. È possibile visualizzare le metriche importanti per la topologia (ad esempio, un colore indica meteo un determinato bulloni "occupato" o meno). È possibile anche fare doppio clic bulloni/beccuccio per visualizzare ulteriori dettagli.

- Supporto per i cluster HDInsight che sono stati creati nel portale di Azure (una correzione)

    È ora possibile utilizzare Visual Studio per visualizzare e inviare i processi per tutti i cluster HDInsight indipendentemente da dove sono stati creati cluster.

- Supporto IntelliSense ulteriori & metadati Hive caricamento più rapido (un miglioramento)

    Sono stati migliorati la IntelliSense mediante l'aggiunta di altri suggerimenti da usare. Ad esempio alias di tabella può ora anche essere suggeriti IntelliSense in modo che è possibile scrivere la query più facilmente. Inoltre, sono stati migliorati i metadati Hive caricamento in modo che solo richiederà alcuni secondi per visualizzare un elenco di database, tabelle e colonne del metastore Hive.

Per ulteriori informazioni sugli aggiornamenti strumenti HDInsight, vedere [questo blog](http://go.microsoft.com/fwlink/?LinkId=623831).

###<a name="improvements-in-visual-studio-2013"></a>Miglioramenti introdotti in Visual Studio 2013

- Azure SDK 2.7.1 consente Visual Studio 2013 per accedere a account Azure e abbonamenti tramite controllo dell'accesso basato su ruoli, Cloud analisti e Dreamspark.
- Con Azure SDK 2.7.1, la nuova finestra Explorer Cloud anche è ora disponibile in Visual Studio 2013.

###<a name="known-issues"></a>Problemi noti

L'installazione di 2.6 SDK Azure o 2.7.1 per Visual Studio Community 2013 in un non - sistema operativo inglese verrà visualizzato un avviso che le risorse in lingua inglese e localizzata di Visual Studio potrebbero risultare errate. Questo avviso può essere chiusa in modo sicuro. Si verificherà solo se il computer non contiene un'installazione precedente di Visual Studio Community 2013 e si installa il SDK su un non - sistema operativo inglese. Dopo che il language pack applica le risorse RTM Visual Studio, ma prima di applicare aggiornamento 4, viene visualizzato il messaggio di avviso. Chiudere l'avviso consentirà del language pack per continuare e completare l'applicazione la versione 4 di aggiornamento del contenuto language pack.

LightSwitch progetti non è compatibile con questa versione. Questo problema sarà risolto con la nuova versione SDK.

##<a name="also-see"></a>Vedere anche
[Azure SDK 2.7.1 post annuncio](http://go.microsoft.com/fwlink/?LinkId=623850)

[Post di annuncio SDK 2.7 Azure](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Supporto e informazioni pensionistico per Azure SDK per .NET e le API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)
