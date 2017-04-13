<properties 
    pageTitle="Download di Azure SDK per Java" 
    description="Informazioni su come scaricare Azure SDK per Java, con codice di esempio fornito per Maven progetti e procedura di installazione di base per il Toolkit di Azure per Eclisse." 
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
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="download-the-azure-sdk-for-java"></a>Download di Azure SDK per Java

In questo articolo è contenute istruzioni per scaricare e installare le librerie di Azure per Java.

**Nota:** Le raccolte di Azure per Java sono distribuite in [licenza di Apache, versione 2.0][license].

## <a name="azure-libraries-for-java---manual-download"></a>Azure raccolte per Java - Download manuale

Per installare manualmente le raccolte di Azure per Java, fare clic su <http://go.microsoft.com/fwlink/?LinkId=690320> per scaricare un file ZIP che contiene tutte le raccolte e tutte le dipendenze.

Dopo avere scaricato il file zip nel computer in uso, estrarre il contenuto e utilizzare una delle opzioni seguenti per aggiungere i file VASO al progetto:

* Importare file VASO in un progetto di linguaggio in Eclisse.

* Configurare il **Percorso compilare** per il progetto di linguaggio in Eclisse per includere il percorso file VASO.

Per informazioni dettagliate sull'impostazione dei percorsi di compilazione in Eclipse, vedere l'articolo [Linguaggio creare percorso] sul sito Web Eclisse.

**Nota:** Vedere la License e ThirdPartyNotices.txt file all'interno il file ZIP per licenza e altre informazioni.

## <a name="azure-libraries-for-java---building-with-maven"></a>Azure raccolte per Java - creazione con Maven

### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>Passaggio 1 - configurare un progetto da utilizzare Maven per compilazione

Per creare progetti Maven in Eclisse che utilizzano le librerie di Azure per Java, seguire le istruzioni nella [Guida introduttiva di Azure gestione delle raccolte per Java] [ maven-getting-started] articolo. 

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>Passaggio 2: configurare le impostazioni di Maven con le dipendenze necessarie

Dopo il progetto è stato configurato per l'utilizzo Maven per creare, è possibile aggiungere le dipendenze necessarie al file pom.xml utilizzando la sintassi come nell'esempio seguente. Si noti che non è necessaria aggiungere tutte le dipendenze elencato nell'esempio seguente; è sufficiente aggiungere le dipendenze specifiche che richiede il progetto.

> [AZURE.NOTE] All'interno di ogni `<version>` elemento nell'esempio seguente, sostituire i segnaposto "n.n.n" in questo esempio con numeri di versione valido, che possono essere ottenuti dal [Repository raccolte Azure su Maven].

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## <a name="installing-the-azure-toolkit-for-eclipse"></a>Installare il Toolkit di Azure per Eclisse

In questa sezione è contenute istruzioni di base per installare il Toolkit di Azure per Eclisse; Per informazioni dettagliate, vedere [installare il Toolkit di Azure per Eclisse].

### <a name="prerequisites"></a>Prerequisiti

1. Sistemi operting Windows elencati nell'articolo [Novità del Toolkit di Azure per Eclisse] .
1. Macintosh o Linux sistemi operting elencati nell'articolo [Novità del Toolkit di Azure per Eclisse] .
1. Eclipse IDE per sviluppatori Java Edition, il o versione successiva. Può essere scaricato dal <http://www.eclipse.org/downloads/>.

### <a name="basic-installation-steps"></a>Procedura di installazione di base

1. In Eclisse, selezionare **Installa nuovo Software**dal menu **?** .
1. Immettere il percorso del sito <http://dl.microsoft.com/eclipse> e premere **INVIO**.
1. Selezionare gli elementi da installare e fare clic su **Fine**.

Il Toolkit di Azure per Eclisse utilizza la versione più recente di Azure SDK. Questa operazione può essere scaricata utilizzando l'installazione guidata piattaforma Web (WebPI) <http://go.microsoft.com/fwlink/?LinkID=252838>. Tuttavia, se non è installato, il quando si crea progetto di distribuzione Azure prima, il Toolkit di Azure per Eclisse automaticamente installare la versione appropriata di Azure SDK.

## <a name="see-also"></a>Vedere anche

[Azure Toolkit per Eclisse]

[Installare il Toolkit di Azure per Eclisse] 

[Creazione di un'applicazione Hello World per Azure in Eclisse]

Per ulteriori informazioni sull'utilizzo di Azure con linguaggio, vedere il [Centro per sviluppatori di linguaggio Azure].

<!-- URL List -->

[Centro per sviluppatori di Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Archivio di Azure raccolte in Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Azure Toolkit per Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creazione di un'applicazione Hello World per Azure in Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installare il Toolkit di Azure per Eclisse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Percorso di compilazione di linguaggio]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[Novità di Azure Toolkit per Eclisse]: http://go.microsoft.com/fwlink/?LinkId=690333
