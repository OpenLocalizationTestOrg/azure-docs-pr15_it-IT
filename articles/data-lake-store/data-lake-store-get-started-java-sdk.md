<properties
   pageTitle="Utilizzare dati Lake archivio Java SDK per lo sviluppo di applicazioni | Microsoft Azure"
   description="Utilizzare Azure dati Lake archivio Java SDK per lo sviluppo di applicazioni"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/17/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-java"></a>Guida introduttiva a archivio Lake Azure utilizzando Java

> [AZURE.SELECTOR]
- [Portale](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Linguaggio SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node](data-lake-store-manage-use-nodejs.md)

Informazioni su come usare Azure dati Lake archivio Java SDK per eseguire operazioni di base, ad esempio durante la creazione di cartelle, caricare e scaricare i file di dati e così via. Per ulteriori informazioni sui dati Lake, vedere [Archivio Lake dati di Azure](data-lake-store-overview.md).

È possibile accedere a documenti Java SDK API per Azure dati Lake archivio [all'API Java di Azure dati Lake archivio documenti](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Prerequisiti

* Java Development Kit (JDK 7 o versioni successiva, utilizzando linguaggio 1.7 o versione successiva)
* Account Azure dati Lake Store. Seguire le istruzioni nella [Guida introduttiva di Azure dati Lake archivio tramite il portale di Azure](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). In questa esercitazione utilizza Maven per le dipendenze di compilazione e project. Sebbene sia possibile creare senza utilizzare un sistema di compilazione come Maven o Gradle, effettuare questi sistemi è molto più facile gestire le relazioni.
* (Facoltativo) E IDE come [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) o [Eclisse](https://www.eclipse.org/downloads/) o simili.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Come si esegue l'autenticazione mediante Azure Active Directory?

In questa esercitazione serve un segreto del client di applicazione di Azure Active Directory per recuperare un token di Azure Active Directory (servizio di autenticazione). Questo token vengono utilizzati per creare un oggetto di client archivio Lake dati per eseguire le operazioni di file e directory operazioni. Per istruzioni su come eseguire l'autenticazione con Azure dati Lake archivio utilizzando il segreto del client, è eseguire le operazioni seguenti:

1. Creare un'applicazione web di Azure Active Directory
2. Recuperare l'ID client, segreto del client e token endpoint per l'applicazione web di Azure Active Directory.
3. Configurare l'accesso per l'applicazione web di Azure Active Directory sul file/cartella archivio Lake dati che si desidera accedere dall'applicazione Java che si sta creando.

Per istruzioni su come eseguire questa procedura, vedere [creare un'applicazione di Active Directory](data-lake-store-authenticate-using-active-directory.md#create-an-active-directory-application).

Azure Active Directory sono disponibili altre opzioni anche per recuperare un token. È possibile scegliere da un numero di meccanismi di autenticazione diverso in base alle proprie dello scenario, ad esempio, un'applicazione in esecuzione in un browser, un'applicazione distribuita come applicazione desktop o un'applicazione di server in esecuzione in locale o in una macchina virtuale Azure. È anche possibile scegliere tra tipi diversi di credenziali come password, certificati, autenticazione fattore di 2 e così via. Inoltre, Azure Active Directory consente di sincronizzare gli utenti di Active Directory locale con il cloud. Per informazioni dettagliate, vedere [Scenari di autenticazione per Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md). 

## <a name="create-a-java-application"></a>Creare un'applicazione Java

Il codice di esempio disponibile [su GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) percorsi per il processo di creazione di file in SharePoint store concatenando i file, scaricare un file ed eliminare alcuni file nell'archivio. In questa sezione dell'articolo guidano l'utente attraverso le parti principali del codice.

1. Creare un progetto Maven utilizzando [sistema per mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) dalla riga di comando o un IDE. Per istruzioni su come creare un progetto di linguaggio utilizzando IntelliJ, vedere [di seguito](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Per istruzioni su come creare un progetto utilizzando Eclipse, vedere [di seguito](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Aggiungere le dipendenze seguenti a un file di **pom.xml** Maven. Aggiungere il seguente frammento di testo tra le ** \</version >** tag e le ** \</project >** tag:

        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.0.4-SNAPSHOT</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>

    La prima dipendenza consiste nell'usare Data Lake archivio SDK (`azure-datalake-store`) dal repository maven. La seconda dipendenza (`slf4j-nop`) è possibile specificare quali framework registrazione da utilizzare per questa applicazione. Data Lake archivio SDK utilizza [slf4j](http://www.slf4j.org/) registrazione facciata, che consente di scegliere da un numero di Framework di registrazione comuni, come log4j, linguaggio registrazione logback e così via, o nessuna registrazione. In questo esempio verrà disabilitata la registrazione, pertanto è utilizzare l'associazione **slf4j nop** . Per utilizzare altre opzioni di registrazione nell'app, vedere [di seguito](http://www.slf4j.org/manual.html#projectDep).

### <a name="add-the-application-code"></a>Aggiungere il codice dell'applicazione

Esistono tre parti principali al codice.

1. Ottenere il token di Azure Active Directory

2. Utilizzare il token per creare un client archivio Lake dati.

3. Utilizzare il client di archivio di dati Lake per eseguire operazioni.

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>Passaggio 1: Ottenere un token di Azure Active Directory.

Data Lake archivio SDK contiene metodi pratici che consentono di ottenere i token di sicurezza necessari per comunicare con l'account di archivio di dati Lake. Tuttavia, il SDK non sarà più essere utilizzato solo questi metodi. È possibile utilizzare qualsiasi mezzo di ottenere token, simile all'utilizzo di [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java)o codice personalizzato.

Per utilizzare Data Lake archivio SDK per ottenere token per l'applicazione di Active Directory Web creata in precedenza, utilizzare i metodi statici in `AzureADAuthenticator` classe. Sostituire **Selezione IN qui** con i valori effettivi per l'applicazione Web di Azure Active Directory.

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AzureADToken token = AzureADAuthenticator.getTokenUsingClientCreds(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>Passaggio 2: Creare un oggetto di archivio di Azure dati Lake client (ADLStoreClient)

Creazione di un oggetto [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) è necessario specificare il nome dell'account archivio Lake dati e il token di Azure Active Directory generate nell'ultimo passaggio. Si noti che il nome dell'account archivio Lake dati deve essere un nome di dominio completo. Ad esempio, sostituire **Selezione IN qui** con simile al **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, token);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>Passaggio 3: Utilizzare il ADLStoreClient per eseguire operazioni di file e directory

Il codice riportato di seguito contiene frammenti di esempio di alcune operazioni comuni. È possibile esaminare il completo [dati Lake archivio Java SDK API documenti](https://azure.github.io/azure-data-lake-store-java/javadoc/) dell'oggetto **ADLStoreClient** per visualizzare altre operazioni.
 
Si noti che i file sono lettura e scritti in utilizzando flussi di linguaggio standard. Questo errore indica che è possibile creare livelli di uno dei flussi di linguaggio sopra i flussi di archivio di dati Lake vantaggi offerti dalle funzionalità di linguaggio standard (ad esempio, stampa flussi di output formattato o uno qualsiasi dei flussi di compressione o la crittografia per fornire funzionalità aggiuntive nella parte superiore e così via.).

    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>Passaggio 4: Creare ed eseguire l'applicazione

1. Per eseguire da un IDE, individuare e fare clic sul pulsante **Esegui** . Per eseguire da Maven, usare [exec: exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).

2. Per produrre un vaso autonomo che è possibile eseguire dalla riga di comando creare vaso con tutte le dipendenze incluse, utilizzando il [plug-in assembly Maven](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). Pom.xml nel [codice sorgente di esempio in github](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) contiene un esempio di come eseguire questa operazione.


## <a name="next-steps"></a>Passaggi successivi

- [Proteggere i dati nell'archivio dati Lake](data-lake-store-secure-data.md)
- [Usare dati di Azure Lake Analitica con dati Lake archivio](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usare Azure HDInsight con dati Lake archivio](data-lake-store-hdinsight-hadoop-use-portal.md)
