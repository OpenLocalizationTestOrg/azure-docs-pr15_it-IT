<properties
    pageTitle="Novità di Azure Toolkit per IntelliJ | Microsoft Azure"
    description="Informazioni sulle caratteristiche più recenti nella cartella Toolkit di Azure per IntelliJ."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/26/2016" 
    ms.author="robmcm;asirveda;martinsawicki"/>

# <a name="whats-new-in-the-azure-toolkit-for-intellij"></a>Novità di Azure Toolkit per IntelliJ

## <a name="azure-toolkit-for-intellij-releases"></a>Azure Toolkit per le versioni IntelliJ

In questo articolo contiene informazioni diverse versioni e aggiornamenti più recenti per il Toolkit di Azure per IntelliJ.

> [AZURE.NOTE] È inoltre disponibile un Toolkit di Azure per Eclipse IDE. Per ulteriori informazioni, vedere [Toolkit di Azure per Eclisse].

### <a name="august-26-2016"></a>26 agosto 2016

Il Toolkit di Azure per IntelliJ - versione di agosto 2016 include i miglioramenti seguenti:

* **Distribuzioni JDK personalizzato**. Il Toolkit di Azure per IntelliJ supporta ora specificando e distribuzione di una versione JDK non autorizzata per il contenitore di Azure Web App:
  - Oltre JDKs fornito da Azure, è anche possibile scegliere da un'ampia scelta di versioni OpenJDK Zulu messo a disposizione su Azure Azul sistemi.
  - È inoltre possibile specificare il proprio distribuzione JDK se si carica come file ZIP al proprio account di archiviazione.
* **Miglioramenti per la visualizzazione di Azure Explorer**:
  - Supporto per la gestione di macchina virtuale utilizzando il nuovo modello di gestione risorse di Azure: è possibile dell'elenco, creare ed eliminare macchine virtuali basate su manager delle risorse senza uscire dall'IDE.
  - Supporto per Account di archiviazione blob management tramite Gestione risorse di Azure, che integra le funzionalità esistenti per la gestione degli account "classico" spazio di archiviazione.
* **Driver di Microsoft JDBC 6.0 per SQL Server**. Questo aggiornamento include il driver JDBC più recente di Microsoft SQL Server (versione 6.0), che è ora incluse come una raccolta che è possibile aggiungere facilmente ai progetti Java, in modo da sostituire la versione precedente.

### <a name="june-29-2016"></a>29 giugno 2016

Il Toolkit di Azure per IntelliJ - giugno 2016 release include i miglioramenti seguenti:

* **Requisito di linguaggio 8**. Il Toolkit di Azure per IntelliJ richiede linguaggio 8, anche se questo requisito è valido solo per il toolkit - le applicazioni è possono continuare a usare tutte le versioni di linguaggio supportate da Azure.
* **Supporto per la più recente di linguaggio JDKs**. Le versioni più recenti di JDKs Java sono ora supportate dal Toolkit Azure IntelliJ.
* **Supporto per v2.9.1 SDK Azure**. L'ultima versione di Azure SDK è ora il minimo prerequisito per il Toolkit di Azure per IntelliJ.
* **Esempi di integrate**. Il Toolkit di Azure per IntelliJ funzionalità diverse applicazioni di esempio per aiutare gli sviluppatori per iniziare.
* **Integrazione strumenti HDInsight**. HDInsight strumenti di Azure ora un pacchetto con il Toolkit di Azure IntelliJ. Per ulteriori informazioni, vedere [Plug-in strumenti di HDInsight per IntelliJ].
* **Il debug di linguaggio Web Apps remoto**. Il Toolkit di Azure per IntelliJ supporta ora il debug remoto di linguaggio web apps nel servizio App Azure.

### <a name="april-12-2016"></a>12 aprile 2016

Il Toolkit di Azure per IntelliJ - 2016 aprile release include i miglioramenti seguenti:

* **Supporto per v2.9.0 SDK Azure**. L'ultima versione di Azure SDK è ora il minimo prerequisito per il Toolkit di Azure per IntelliJ.
* **Varie facilità di utilizzo, la velocità di risposta e prestazioni miglioramenti relativi al supporto di Azure Web App**. Ottimizzazione delle prestazioni in modalità di comunicazione con i risultati Azure il Toolkit di un numero in un'interfaccia utente più efficiente.
* **Possibilità di eliminare un contenitore di applicazione Web esistente in Azure all'interno di IntelliJ**. Il Toolkit di Azure per IntelliJ consente di eliminare un contenitore di Azure Web esistente senza uscire da IntelliJ.

## <a name="see-also"></a>Vedere anche ##

Per ulteriori informazioni su Toolkit Azure per Java IDE, vedere i collegamenti seguenti:

- [Azure Toolkit per Eclisse]
  - [Installare il Toolkit di Azure per Eclisse]
  - [Creare un'App Web di Hello World per Azure in Eclisse]
  - [Novità di Azure Toolkit per Eclisse]
- [Azure Toolkit per IntelliJ]
  - [Installare il Toolkit di Azure per IntelliJ]
  - [Creare un'App Web di Hello World per Azure in IntelliJ]
  - *Novità di Azure Toolkit per IntelliJ (in questo articolo)*

Per ulteriori informazioni sull'utilizzo di Azure con linguaggio, vedere il [Centro per sviluppatori di linguaggio Azure].

<!-- URL List -->

[Azure Toolkit per Eclisse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit per IntelliJ]: ./azure-toolkit-for-intellij.md
[Creare un'App Web di Hello World per Azure in Eclisse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Creare un'App Web di Hello World per Azure in IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installare il Toolkit di Azure per Eclisse]: ./azure-toolkit-for-eclipse-installation.md
[Installare il Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Novità di Azure Toolkit per Eclisse]: ./azure-toolkit-for-eclipse-whats-new.md
[What's New in the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro per sviluppatori di Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547

[Plug-in strumenti di HDInsight per IntelliJ]: ./hdinsight/hdinsight-apache-spark-intellij-tool-plugin.md
