<properties 
    pageTitle="Creare un'App Web nel servizio di App Azure con Azure SDK per Java" 
    description="Informazioni su come creare un'App Web nel servizio di App Azure a livello di programmazione tramite Azure SDK per Java." 
    tags="azure-classic-portal"
    services="app-service\web" 
    documentationCenter="Java" 
    authors="donntrenton" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="02/25/2016" 
    ms.author="v-donntr"/>


# <a name="create-a-web-app-in-azure-app-service-using-the-azure-sdk-for-java"></a>Creare un'App Web nel servizio di App Azure con Azure SDK per Java

<!-- Azure Active Directory workflow is not yet available on the Azure Portal -->

## <a name="overview"></a>Panoramica

Questa procedura viene illustrato come creare un SDK Azure per applicazione Java che consente di creare un'App Web nel [Servizio di Azure App][]e quindi distribuire un'applicazione. È costituito da due parti:

- Parte 1 viene illustrato come creare un'applicazione di linguaggio che consente di creare un'app web.
- Parte 2 viene illustrato come creare un semplice JSP "Hello World" applicazione e quindi utilizzare un FTP client per distribuire il codice servizio App.


## <a name="prerequisites"></a>Prerequisiti

### <a name="software-installations"></a>Installazioni del software

Il codice dell'applicazione AzureWebDemo in questo articolo è stato scritto con Azure linguaggio SDK 0.7.0, che è possibile installare tramite [Installazione guidata piattaforma Web][] (WebPI). Inoltre, assicurarsi di usare la versione più recente del [Toolkit di Azure per Eclisse][]. Dopo aver installato il SDK, aggiornare le dipendenze del progetto Eclipse eseguendo **Aggiorna indice** disponibili negli **Archivi Maven**e quindi aggiungere di nuovo l'ultima versione di ogni pacchetto nella finestra **relazioni** . È possibile verificare la versione del software installato in Eclisse facendo clic su **Guida > i dettagli sull'installazione**; è necessario disporre almeno le versioni seguenti:

- Pacchetto per le raccolte di Microsoft Azure in linguaggio 0.7.0.20150309
- Eclipse IDE per sviluppatori di Edition Java 4.4.2.20150219


### <a name="create-and-configure-cloud-resources-in-azure"></a>Creare e configurare le risorse Cloud in Azure

Prima di iniziare questa procedura, è necessario disporre di un abbonamento di Azure active e impostare un valore predefinito di Active Directory (AD) in Azure.


### <a name="create-an-active-directory-ad-in-azure"></a>Creare un server Active Directory (AD) in Azure

Se non si dispone già un Active Directory (AD) su abbonamento Azure, accedere al [portale classica Azure][] con l'account Microsoft. Se si hanno più abbonamenti, fare clic su **abbonamenti** e selezionare la directory predefinita per l'abbonamento a cui che si desidera utilizzare per il progetto. Fare clic su **Applica** per passare alla visualizzazione abbonamento.

1. Selezionare **Active Directory** dal menu a sinistra. **Fare clic su Nuovo > Directory > personalizzata creare**.

2. In **Aggiungi Directory**, selezionare **Crea nuova Directory**.

3. In **nome**immettere un nome di directory.

4. Nel **dominio**, immettere un nome di dominio. Si tratta di un nome di dominio base inclusi per impostazione predefinita con la directory; forma `<domain_name>.onmicrosoft.com`. È possibile assegnare il nome in base al nome della directory o un altro nome di dominio che si è proprietari. In un secondo momento, è possibile aggiungere un altro nome di dominio che utilizza l'organizzazione ha già.

5. In **paese o area geografica**, selezionare le impostazioni locali.

Per ulteriori informazioni su Active Directory, vedere [che cos'è una directory di Azure Active Directory][].


### <a name="create-a-management-certificate-for-azure"></a>Creare un certificato di gestione per Azure

Azure SDK per Java utilizza Gestione certificati per l'autenticazione con Azure abbonamenti. Si tratta di x. 509 v3 che consente di eseguire l'autenticazione di un'applicazione client che utilizza l'API di gestione del servizio per agire per conto del proprietario della sottoscrizione per gestire le risorse di sottoscrizione.

Il codice in questa procedura utilizza un certificato autofirmato per eseguire l'autenticazione con Azure. Per questa procedura, è necessario creare un certificato e caricarlo nel [portale classica Azure][] in anticipo. Si prevede i passaggi seguenti:

- Generare un file PFX che rappresenta il certificato client e salvarlo in locale.
- Generare un certificato di gestione (file CER) dal file PFX.
- Caricare il file CER all'abbonamento Azure.
- Convertire il file PFX in JKS, in quanto tale formato linguaggio utilizzato per eseguire l'autenticazione mediante i certificati.
- Scrivere codice di autenticazione dell'applicazione, si riferisce a file JKS locale.

Una volta completata questa procedura, il certificato CER si troverà nella abbonamento Azure e il certificato JKS si trova nell'unità locale. Per ulteriori informazioni sui certificati di gestione, vedere [creare e caricare un certificato di gestione per Azure][].


#### <a name="create-a-certificate"></a>Creare un certificato

Per creare un certificato autofirmato, aprire una console di comando nel sistema operativo ed eseguire i comandi seguenti.

> **Nota:**  Nel computer in cui si esegue il comando deve essere JDK installato. Inoltre, il percorso di keytool dipende dalla posizione in cui si installa JDK. Per ulteriori informazioni, vedere [strumento di gestione di certificati (keytool) e chiavi][] nei documenti online linguaggio.

Per creare il file. pfx:

    <java-install-dir>/bin/keytool -genkey -alias <keystore-id>
     -keystore <cert-store-dir>/<cert-file-name>.pfx -storepass <password>
     -validity 3650 -keyalg RSA -keysize 2048 -storetype pkcs12
     -dname "CN=Self Signed Certificate 20141118170652"

Per creare il file:

    <java-install-dir>/bin/keytool -export -alias <keystore-id>
     -storetype pkcs12 -keystore <cert-store-dir>/<cert-file-name>.pfx
     -storepass <password> -rfc -file <cert-store-dir>/<cert-file-name>.cer

dove:

- `<java-install-dir>`è il percorso della directory in cui è installato Java.
- `<keystore-id>`è l'identificatore di voce keystore (ad esempio `AzureRemoteAccess`).
- `<cert-store-dir>`è il percorso della directory in cui si desidera memorizzare i certificati (ad esempio `C:/Certificates`).
- `<cert-file-name>`è il nome del file di certificato (ad esempio `AzureWebDemoCert`).
- `<password>`la password che si sceglie di proteggere il certificato. deve essere almeno 6 caratteri. È non possibile immettere alcuna password, anche se non è consigliabile.
- `<dname>`è il nome distinto 500 da associare alias e viene utilizzato come i campi emittente e soggetto del certificato autofirmato.

Per ulteriori informazioni, vedere [creare e caricare un certificato di gestione per Azure][].


#### <a name="upload-the-certificate"></a>Caricare il certificato

Per caricare un certificato autofirmato Azure, passare alla pagina delle **Impostazioni** del portale classica e quindi fare clic sulla scheda **Certificati di gestione** . Fare clic su **Carica** nella parte inferiore della pagina e passare al percorso del file CER creato.


#### <a name="convert-the-pfx-file-into-jks"></a>Convertire il file PFX in JKS

In Windows Prompt dei comandi (eseguito come amministratore), cd alla directory contenente i certificati ed eseguire il comando seguente, in cui `<java-install-dir>` è la cartella in cui è installato Java nel computer in uso:

    <java-install-dir>/bin/keytool.exe -importkeystore
     -srckeystore <cert-store-dir>/<cert-file-name>.pfx
     -destkeystore <cert-store-dir>/<cert-file-name>.jks
     -srcstoretype pkcs12 -deststoretype JKS

1. Quando richiesto, immettere la password di keystore destinazione; Questo sarà la password per il file JKS.

2. Quando richiesto, immettere la password di keystore origine; si tratta della password specificato per il file PFX.

Le due password non può essere lo stesso. È non possibile immettere alcuna password, anche se non è consigliabile.


## <a name="build-a-web-app-creation-application"></a>Creare un'applicazione di creazione Web App

### <a name="create-the-eclipse-workspace-and-maven-project"></a>Creare le Eclisse dell'area di lavoro e Maven progetto

In questa sezione è creare un'area di lavoro e un progetto Maven per l'applicazione di creazione web app, denominato AzureWebDemo.

1. Creare un nuovo progetto Maven. Fare clic su **File > Nuovo > progetto Maven**. Nel **Nuovo progetto Maven**, selezionare **Crea un progetto semplice** e **percorso dell'area di lavoro predefinito usare**.

2. Nella seconda pagina del **Nuovo progetto Maven**, specificare quanto segue:

    - ID gruppo:`com.<username>.azure.webdemo`
    - ID elemento: AzureWebDemo
    - Versione: 0.0.1-SNAPSHOT
    - Imballaggio: vaso
    - Nome: AzureWebDemo

    Fare clic su **Fine**.

3. Aprire il nuovo file del progetto pom.xml in Gestione progetti. Selezionare la scheda **relazioni** . Come si tratta di un nuovo progetto, pacchetti non sono elencati ancora.

4. Aprire la visualizzazione di archivi Maven. **Fare clic su finestra > Mostra visualizzazione > altri > Maven > Maven archivi** e fare clic su **OK**. La visualizzazione di **Archivi Maven** verrà visualizzato nella parte inferiore dell'IDE.

5. Aprire **Archivi globale**, rapida repository **centrale** e selezionare **La ricostruzione indice**.

    ![][1]
    
    Questo passaggio può richiedere alcuni minuti a seconda della velocità della connessione. Quando si ricrea l'indice, verranno visualizzati i pacchetti di Microsoft Azure **centrale Maven** .

6. **Dipendenze**, fare clic su **Aggiungi**. Nel **Gruppo di invio ID...** immettere `azure-management`. Selezionare i pacchetti di base gestione delle e applicazione del servizio Web App:

        com.microsoft.azure  azure-management
        com.microsoft.azure  azure-management-websites

    > **Nota:** Se si siano aggiornando le dipendenze dopo una nuova versione, è necessario aggiungere nuovamente ognuna delle dipendenze in questo elenco.
    > Fare clic su **Aggiungi** e selezionare ciascuna dipendenza viene visualizzato con il nuovo numero di versione nell'elenco **dipendenze** .

Fare clic su **OK**. I pacchetti di Azure verranno quindi visualizzati nell'elenco **dipendenze** .


### <a name="writing-java-code-to-create-a-web-app-by-calling-the-azure-sdk"></a>Scrivere codice Java per creare un'App Web chiamando SDK Azure

Successivamente, scrivere il codice che chiama API in Azure SDK per Java per creare il servizio di App web app.

1. Creare una classe Java per contenere il codice di punto di ingresso principale. In Gestione progetti, pulsante destro del mouse sul nodo del progetto e selezionare **Nuovo > classe**.

2. Nella **Nuova classe Java**, assegnare un nome della classe `WebCreator` e selezionare la casella di controllo **principali void statico pubblico** . Le selezioni devono essere visualizzati come indicato di seguito:

    ![][2]

3. Fare clic su **Fine**. Il file WebCreator.java viene visualizzata in Esplora progetti.


### <a name="calling-the-azure-api-to-create-an-app-service-web-app"></a>Richiamare l'API Azure per creare un'applicazione servizio Web App


#### <a name="add-necessary-imports"></a>Aggiungere importazioni necessarie

Nella finestra WebCreator.java, aggiungere le seguenti importazioni; queste importazioni forniscono l'accesso alle classi nelle librerie di gestione per l'utilizzo di Azure API:

    // General imports
    import java.net.URI;
    import java.util.ArrayList;
    
    // Imports for Exceptions
    import java.io.IOException;
    import java.net.URISyntaxException;
    import javax.xml.parsers.ParserConfigurationException;
    import com.microsoft.windowsazure.exception.ServiceException;
    import org.xml.sax.SAXException;
    
    // Imports for Azure App Service management configuration
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.management.configuration.ManagementConfiguration;
    
    // Service management imports for App Service Web Apps creation
    import com.microsoft.windowsazure.management.websites.*;
    import com.microsoft.windowsazure.management.websites.models.*;
    
    // Imports for authentication
    import com.microsoft.windowsazure.core.utils.KeyStoreType;


#### <a name="define-the-main-entry-point-class"></a>Definire la classe di punto di ingresso principale

Perché lo scopo dell'applicazione AzureWebDemo consiste nel creare un'applicazione servizio Web App, assegnare un nome della classe principale dell'applicazione `WebAppCreator`. Questa classe fornisce il codice di punto di ingresso principale che chiama l'API di gestione del servizio di Azure per creare l'applicazione web.

Aggiungere le seguenti definizioni di parametro per il web app e webspace. È necessario fornire le informazioni di certificati e ID il proprio abbonamento Azure.

    public class WebAppCreator {
    
        // Parameter definitions used for authentication.
        private static String uri = "https://management.core.windows.net/";
        private static String subscriptionId = "<subscription-id>";
        private static String keyStoreLocation = "<certificate-store-path>";
        private static String keyStorePassword = "<certificate-password>";
    
        // Define web app parameter values.
        private static String webAppName = "WebDemoWebApp";
        private static String domainName = ".azurewebsites.net";
        private static String webSpaceName = WebSpaceNames.WESTUSWEBSPACE;
        private static String appServicePlanName = "WebDemoAppServicePlan";

dove:

- `<subscription-id>`è l'ID di abbonamento Azure in cui si desidera creare la risorsa.
- `<certificate-store-path>`è il percorso e il nome del file JKS nella directory archivio certificati locale. Ad esempio `C:/Certificates/CertificateName.jks` per Linux e `C:\Certificates\CertificateName.jks` per Windows.
- `<certificate-password>`è la password specificata al momento della creazione del certificato JKS.
- `webAppName`può essere qualsiasi nome che scelto; Questa procedura con lo stesso nome `WebDemoWebApp`. Il nome di dominio completo di `webAppName` con la `domainName` , pertanto in questo caso il dominio completo viene aggiunta `webdemowebapp.azurewebsites.net`.
- `domainName`deve essere specificato come illustrato sopra.
- `webSpaceName`deve essere uno dei valori definiti nella classe [WebSpaceNames][] .
- `appServicePlanName`deve essere specificato come illustrato sopra.

> **Nota:** Ogni volta che si esegue questa applicazione, è necessario impostare il valore di `webAppName` e `appServicePlanName` (o eliminare un'app web nel portale di Azure) prima di eseguire l'applicazione. In caso contrario, esecuzione riuscirà perché la stessa risorsa esiste già in Azure.


#### <a name="define-the-web-creation-method"></a>Definire il metodo di creazione web

Successivamente, definire un metodo per creare l'applicazione web. Questo metodo, `createWebApp`, specifica i parametri di web app e la webspace. Crea e configura il client di gestione dell'applicazione del servizio Web Apps, definito dall'oggetto [WebSiteManagementClient][] . Il client management è fondamentale per la creazione di applicazioni Web. Fornisce servizi web REST che consente alle applicazioni gestire applicazioni web (ad esempio create, aggiornamento ed eliminazione, eseguire le operazioni) chiamando l'API di gestione di servizio.

    private static void createWebApp() throws Exception {

        // Specify configuration settings for the App Service management client.
        Configuration config = ManagementConfiguration.configure(
            new URI(uri),
            subscriptionId,
            keyStoreLocation,  // Path to the JKS file
            keyStorePassword,  // Password for the JKS file
            KeyStoreType.jks   // Flag that you are using a JKS keystore
        );

        // Create the App Service Web Apps management client to call Azure APIs
        // and pass it the App Service management configuration object.
        WebSiteManagementClient webAppManagementClient = WebSiteManagementService.create(config);

        // Create an App Service plan for the web app with the specified parameters.
        WebHostingPlanCreateParameters appServicePlanParams = new WebHostingPlanCreateParameters();
        appServicePlanParams.setName(appServicePlanName);
        appServicePlanParams.setSKU(SkuOptions.Free);
        webAppManagementClient.getWebHostingPlansOperations().create(webSpaceName, appServicePlanParams);

        // Set webspace parameters.
        WebSiteCreateParameters.WebSpaceDetails webSpaceDetails = new WebSiteCreateParameters.WebSpaceDetails();
        webSpaceDetails.setGeoRegion(GeoRegionNames.WESTUS);
        webSpaceDetails.setPlan(WebSpacePlanNames.VIRTUALDEDICATEDPLAN);
        webSpaceDetails.setName(webSpaceName);

        // Set web app parameters.
        // Note that the server farm name takes the Azure App Service plan name.
        WebSiteCreateParameters webAppCreateParameters = new WebSiteCreateParameters();
        webAppCreateParameters.setName(webAppName);
        webAppCreateParameters.setServerFarm(appServicePlanName);
        webAppCreateParameters.setWebSpace(webSpaceDetails);

        // Set usage metrics attributes.
        WebSiteGetUsageMetricsResponse.UsageMetric usageMetric = new WebSiteGetUsageMetricsResponse.UsageMetric();
        usageMetric.setSiteMode(WebSiteMode.Basic);
        usageMetric.setComputeMode(WebSiteComputeMode.Shared);

        // Define the web app object.
        ArrayList<String> fullWebAppName = new ArrayList<String>();
        fullWebAppName.add(webAppName + domainName);
        WebSite webApp = new WebSite();
        webApp.setHostNames(fullWebAppName);

        // Create the web app.
        WebSiteCreateResponse webAppCreateResponse = webAppManagementClient.getWebSitesOperations().create(webSpaceName, webAppCreateParameters);

        // Output the HTTP status code of the response; 200 indicates the request succeeded; 4xx indicates failure.
        System.out.println("----------");
        System.out.println("Web app created - HTTP response " + webAppCreateResponse.getStatusCode() + "\n");

        // Output the name of the web app that this application created.
        String shinyNewWebAppName = webAppCreateResponse.getWebSite().getName();
        System.out.println("----------\n");
        System.out.println("Name of web app created: " + shinyNewWebAppName + "\n");
        System.out.println("----------\n");
    }

Il codice verrà visualizzato lo stato HTTP della risposta che indica esito positivo o negativo e in caso contrario, verrà restituito il nome dell'applicazione web creata.


#### <a name="define-the-main-method"></a>Definire il metodo Main)

Specificare il codice del metodo Main () che chiama createWebApp() per creare l'applicazione web.

Infine, chiamare `createWebApp` da `main`:

        public static void main(String[] args)
            throws IOException, URISyntaxException, ServiceException,
            ParserConfigurationException, SAXException, Exception {

            // Create web app
            createWebApp();

        }  // end of main()

    }  // end of WebAppCreator class


#### <a name="run-the-application-and-verify-web-app-creation"></a>Eseguire l'applicazione e verificare che la creazione di web app

Per verificare che l'applicazione viene eseguita, fare clic su **eseguire > eseguire**. Al termine dell'esecuzione dell'applicazione, verrà visualizzato il seguente output nella console di Eclisse:

    ----------
    Web app created - HTTP response 200
    
    ----------
    
    Name of web app created: WebDemoWebApp
    
    ----------

Accedere al portale classico Azure e fare clic su **App Web**. La nuova app web dovrebbero essere visualizzate nell'elenco Web Apps nell'arco di qualche minuto.


## <a name="deploying-an-application-to-the-web-app"></a>Distribuzione di un'applicazione all'App Web

Dopo aver eseguito AzureWebDemo e creare la nuova app web, accedere al portale classico, fare clic su **App Web**e selezionare **WebDemoWebApp** nell'elenco di **Applicazioni Web** . Nella pagina dashboard dell'applicazione web, fare clic su **Sfoglia** (o fare clic sull'URL `webdemowebapp.azurewebsites.net`) per spostarsi su di esso. Si vedrà una pagina vuota segnaposto alcun contenuto non pubblicato ancora all'app web.

Si verrà quindi creare un'applicazione "Hello World" e distribuirlo in web app.


### <a name="create-a-jsp-hello-world-application"></a>Creare un'applicazione Hello World JSP

#### <a name="create-the-application"></a>Creare l'applicazione

Per dimostrare come distribuire un'applicazione web, la procedura seguente viene illustrato come creare un'applicazione di linguaggio "Hello World" semplice e caricarlo in App servizio Web App che ha creato l'applicazione.

1. Fare clic su **File > Nuovo > progetto Web Dynamic**. Assegnare un nome `JSPHello`. Non è necessario modificare le altre impostazioni in questa finestra di dialogo. Fare clic su **Fine**.

    ![][3]

2. In Esplora progetti espandere il progetto **JSPHello** , rapida **DatiWeb**e quindi fare clic su **Nuovo > File JSP**. Nella finestra di dialogo Nuovo File JSP, assegnare un nome file nuovo `index.jsp`. Fare clic su **Avanti**.

3. Nella finestra di dialogo **Seleziona modello JSP** , selezionare **Nuovo File JSP (html)** e fare clic su **Fine**.

4. In index.jsp, aggiungere il codice seguente nel `<head>` e `<body>` tag sezioni:

        <head>
          ...
          java.util.Date date = new java.util.Date();
        </head>
    
        <body>
          Hello, the time is <%= date %> 
        </body>


#### <a name="run-the-hello-world-application-in-localhost"></a>Eseguire l'applicazione Hello World nell'host locale

Prima di eseguire questa applicazione, è necessario configurare alcune proprietà.

1. Fare clic sul progetto **JSPHello** e scegliere **proprietà**.

2. Nella finestra di dialogo **proprietà** : selezionare **Il percorso di compilazione di linguaggio**, selezionare la scheda **ordine ed Esporta** , selezionare **Una raccolta di sistema JRE**, quindi fare clic **su** per spostarla nella parte superiore dell'elenco.

    ![][4]

3. Anche nella finestra di dialogo **proprietà** : selezionare **Destinate runtime** e fare clic su **Nuovo**.

4. Nella finestra di dialogo **Nuovo ambiente di Runtime del Server** , selezionare un server, ad esempio **Apache Tomcat 7.0** e fare clic su **Avanti**. Nella finestra di dialogo **Server Tomcat** impostare **nome** su `Apache Tomcat v7.0`e impostare **Directory di installazione Tomcat** alla directory in cui è installata la versione del server Tomcat che si desidera utilizzare.

    ![][5]

    Fare clic su **Fine**.

5. Quindi tornare alla pagina **Riservata runtime** della finestra di dialogo **proprietà** . Selezionare **7.0 Tomcat Apache**, quindi fare clic su **OK**.

    ![][6]

6. Nel menu Eclisse **eseguire** fare clic su **Esegui**. Nella finestra di dialogo **Esegui come** , selezionare **Esegui nel Server**. Nella finestra di dialogo **Esegui sul Server** , selezionare **Tomcat 7.0 Server**:

    ![][7]

    Fare clic su **Fine**.

7. Quando viene eseguita l'applicazione, verrà visualizzata la **JSPHello** pagina vengono visualizzate in una finestra host locale in Eclisse (`http://localhost:8080/JSPHello/`), verrà visualizzato il messaggio seguente:

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### <a name="export-the-application-as-a-war"></a>Esportare l'applicazione come una GUERRA

Esportare file di progetto come file di archivio (WAR) web in modo che è possibile distribuirlo in web app. I file di project web seguenti si trovano nella cartella DatiWeb:

    META-INF
    WEB-INF
    index.jsp

1. Pulsante destro del mouse sulla cartella DatiWeb e selezionare **Esporta**.

2. Nella finestra di dialogo **Esporta selezionare** , fare clic su **Web > WAR** file e quindi fare clic su **Avanti**.

3. Nella finestra di dialogo **Esporta WAR** , selezionare la directory src nel progetto corrente e includere il nome del file WAR alla fine. Per esempio:

    `<project-path>/JSPHello/src/JSPHello.war`

Per ulteriori informazioni sulla distribuzione dei file WAR, vedere [aggiunta di un'applicazione Java in Azure App servizio Web Apps](web-sites-java-add-app.md).


### <a name="deploying-the-hello-world-application-using-ftp"></a>Distribuzione di un'applicazione Hello World tramite FTP

Selezionare un client FTP di terze parti per pubblicare l'applicazione. In questa procedura viene due opzioni: console Kudu integrata in Azure; e FileZilla, uno strumento popolari con un'interfaccia utente grafica, pratico.

> **Nota:** Il Toolkit di Azure per Eclisse supporta la distribuzione per gli account di archiviazione e servizi cloud, ma non supporta attualmente la distribuzione di applicazioni web. È possibile distribuire gli account di archiviazione e i servizi cloud in un progetto di distribuzione di Azure come descritto nella [creazione di un'applicazione Hello World di Azure in Eclisse](http://msdn.microsoft.com/library/azure/hh690944.aspx), ma non alle applicazioni web. Usare altri metodi, ad esempio FTP o GitHub per trasferire file in un'applicazione web.

> **Nota:** Non è consigliabile utilizzare FTP dal prompt dei comandi di Windows (FTP.EXE utilità della riga di fornito con Windows). Client FTP che utilizzano FTP attiva, ad esempio FTP.EXE, spesso non riescono a lavorare su Firewall. Attiva FTP specifica un indirizzo interno basate su LAN, in cui un server FTP probabile che non potranno connettersi.

Per ulteriori informazioni sulla distribuzione a un servizio di App web app tramite FTP, vedere gli argomenti seguenti:

- [Distribuire con un'utilità FTP](web-sites-deploy.md)


#### <a name="set-up-deployment-credentials"></a>Configurare le credenziali di distribuzione

Assicurarsi che è stato eseguito l'applicazione **AzureWebDemo** per creare un'app web. Si verranno trasferire file in questa posizione.

1. Accedere al portale classico e fare clic su **App Web**. Verificare che **WebDemoWebApp** viene visualizzato nell'elenco di applicazioni web e assicurarsi che sia in esecuzione. Fare clic su **WebDemoWebApp** per aprire la relativa pagina **Dashboard** .

2. Nella pagina **Dashboard** in **Riepilogo veloce**, fare clic su **configurare le credenziali di distribuzione** (se si dispone già di credenziali di distribuzione, viene letto **reimpostare le credenziali di distribuzione**).

    Le credenziali di distribuzione sono associate a un account Microsoft. È necessario specificare un nome utente e password utilizzati per distribuire utilizzando fra e FTP. È possibile utilizzare le credenziali per distribuire a qualsiasi app web in tutte le sottoscrizioni Azure associata al proprio account Microsoft. Specificare le credenziali di distribuzione fra e FTP nella finestra di dialogo e registrare il nome utente e la password per utilizzi futuri.


#### <a name="get-ftp-connection-information"></a>Ottenere informazioni sulla connessione FTP

Per utilizzare FTP per distribuire file dell'applicazione all'app web appena creato, è necessario ottenere informazioni di connessione. Esistono due modi per ottenere informazioni di connessione. È possibile visitare pagina **Dashboard** dell'applicazione web. altro modo è scaricare web dell'applicazione profilo di pubblicazione. Il profilo di pubblicazione è un file XML che fornisce informazioni, ad esempio FTP host nome e le credenziali per App web di Azure App servizio. È possibile usare il nome utente e la password per la distribuzione a qualsiasi app web in tutte le sottoscrizioni associati all'account Azure, non solo questa occorrenza.

Per ottenere informazioni di connessione FTP da blade dell'applicazione web nel [Portale di Azure][]:

1. In **Essentials**, individuare e copiare il **nome host FTP**. Si tratta di un URI simile a `ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net`.

2. In **Essentials**, individuare e copiare **il nome utente FTP/distribuzione**. Questo avrà il formato *nome utente webappname\deployment*; ad esempio `WebDemoWebApp\deployer77`.

Per ottenere informazioni di connessione FTP dal profilo pubblica:

1. In blade dell'applicazione web, fare clic su **Recupera profilo di pubblicazione**. Questa operazione verrà scaricare un file publishsettings nell'unità locale.

2. Aprire il file publishsettings in un editor XML o un editor di testo e trovare il `<publishProfile>` elemento contenente `publishMethod="FTP"`. Dovrebbe essere simile al seguente:

        <publishProfile
            profileName="WebDemoWebApp - FTP"
            publishMethod="FTP"
            publishUrl="ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net/site/wwwroot"
            ftpPassiveMode="True"
            userName="WebDemoWebApp\$WebDemoWebApp"
            userPWD="<deployment-password>"
            ...
        </publishProfile>

3. Si noti che l'applicazione web `publishProfile` mappa di impostazioni per le impostazioni di gestione di siti FileZilla come descritto di seguito:

- `publishUrl`è uguale al **nome host FTP**, il valore impostato **nell'Host**.
- `publishMethod="FTP"`indica che l'impostazione di **protocollo** **FTP - File Transfer Protocol**e **la crittografia** da **utilizzare FTP normale**.
- `userName`e `userPWD` sono chiavi per i valori di password specificato quando è reimpostare le credenziali di distribuzione e il nome utente effettivo. `userName`corrisponde al **distribuzione / FTP utente**. A cui sono mappati **utente** e la **Password** in FileZilla.
- `ftpPassiveMode="True"`indica che il sito FTP utilizza trasferimento FTP passivo. Selezionare **passivo** nella scheda **Impostazioni trasferimento** .


#### <a name="configure-the-web-app-to-host-a-java-application"></a>Configurare l'applicazione Web per ospitare un'applicazione Java

Prima di pubblicare l'applicazione, è necessario modificare alcune impostazioni di configurazione in modo che l'applicazione web può ospitare un'applicazione Java.

1. Nel portale classico, passare alla pagina **Dashboard** dell'applicazione web e fare clic su **Configura**. Nella pagina **Configura** specificare le impostazioni seguenti.

2. Nella **versione Java** il valore predefinito è **disattivata**. Selezionare la versione del linguaggio destinata l'applicazione; ad esempio 1.7.0_51. Al termine dell'operazione, assicurarsi che **il contenitore Web** sia impostato su una versione del Server Tomcat.

3. Nei **Documenti predefiniti**, aggiungere index.jsp e spostare fino all'inizio dell'elenco. (File predefinito per le applicazioni web è hostingstart.html).

4. Fare clic su **Salva**.


#### <a name="publish-your-application-using-kudu"></a>Pubblicare l'applicazione con Kudu

Per pubblicare l'applicazione è possibile utilizzare la console di debug Kudu integrata in Azure. Kudu noto come stabile e coerente con l'applicazione del servizio Web Apps e Tomcat Server. Accedere alla console per l'applicazione web passando a un URL nel formato seguente:

`https://<webappname>.scm.azurewebsites.net/DebugConsole`

1. Per questa procedura si trova all'indirizzo seguente; console Kudu passare al percorso seguente:

    `https://webdemowebapp.scm.azurewebsites.net/DebugConsole`

2. Dal menu superiore, selezionare **Console di Debug > CMD**.

3. Nella riga di comando console passare a `/site/wwwroot` (oppure fare clic su `site`, quindi `wwwroot` nella visualizzazione directory nella parte superiore della pagina):

    `cd /site/wwwroot`

4. Dopo avere specificato **versione Java**, server Tomcat deve creare una directory webapp. Nella riga di comando console passare alla directory WebApp:

    `mkdir webapps`

    `cd webapps`

5. Trascinare JSPHello.war da `<project-path>/JSPHello/src/` e rilasciarlo nella visualizzazione della directory Kudu in `/site/wwwroot/webapps`. Non trascinarlo nell'area "Trascinare qui per caricare e zip", poiché Tomcat verrà decomprimere essa.

  ![][8]

Al primo JSPHello.war visualizzata nell'area di directory da sola:

  ![][9]

In breve tempo (probabilmente minore di 5 minuti) Tomcat Server verrà decomprimere il file WAR in una directory JSPHello decompressa. Fare clic su cartella principale per vedere se index.jsp è stata decompressi e copiati in tale posizione. In questo caso, passare alla directory WebApp per verificare se è stata creata la directory JSPHello decompressa. Se questi elementi non è disponibile, attendere e ripetere.

  ![][10]


#### <a name="publish-your-application-using-filezilla-optional"></a>Pubblicare l'applicazione con FileZilla (facoltativo)

Un altro strumento, che è possibile utilizzare per pubblicare l'applicazione è FileZilla, un client FTP di terze parti comuni con un'interfaccia utente grafica, pratico. È possibile scaricare e installare FileZilla da [http://filezilla-project.org/](http://filezilla-project.org/) se non è già stata. Per ulteriori informazioni sull'utilizzo del client, vedere la [documentazione FileZilla](https://wiki.filezilla-project.org/Documentation) e questo post di blog su [client FTP - parte 4: FileZilla](http://blogs.msdn.com/b/robert_mcmurray/archive/2008/12/17/ftp-clients-part-4-filezilla.aspx).

1. In FileZilla, fare clic su **File > gestore del sito**.
2. Nella finestra di dialogo **Gestore del sito** , fare clic su **Nuovo sito**. Verrà visualizzato un nuovo sito FTP vuoto nella **Voce selezionare** chiede di specificare un nome. Per questa procedura, denominarlo `AzureWebDemo-FTP`.

    Nella scheda **Generale** , specificare le impostazioni seguenti:
    - **Host:** Immettere il **Nome Host FTP** copiato nel dashboard.
    - **Porta:** (Lasciare questo vuoto, si tratta di un trasferimento passivo e il server determinerà la porta da utilizzare.)
    - **Protocollo:** Protocollo di trasferimento File FTP
    - **Crittografia:** Utilizzare FTP normale
    - **Tipo di accesso:** Visualizzazione normale
    - **Utente:** Immettere la distribuzione / FTP utente che sono stati copiati nel dashboard. Questo è il nome utente FTP completo, che ha il modulo *webappname\username*.
    - **Password:** Immettere la password specificata quando si impostano le credenziali di distribuzione.

    Nella scheda **Impostazioni trasferimento** selezionare **passiva**.

3. Fare clic su **Connetti**. Se ha esito positivo, console di FileZilla visualizzerà una `Status: Connected` messaggio e problema un `LIST` comando per visualizzare un elenco il contenuto della directory.

4. Nel riquadro sito **locale** , selezionare la directory di origine in cui si trova il file JSPHello.war; il percorso sarà simile alla seguente:

    `<project-path>/JSPHello/src/`

5. Nel riquadro sito **remoto** , selezionare la cartella di destinazione. Si distribuisce il file WAR nel `webapps` directory radice dell'applicazione web. Passare a `/site/wwwroot`, fare clic su `wwwroot`e selezionare **Crea directory**. Assegnare un nome nella directory `webapps` e immettere la directory.

6. Trasferire JSPHello.war a `/site/wwwroot/webapps`. Selezionare JSPHello.war nell'elenco di file **locale** , pulsante destro del mouse su di esso e quindi **caricare**. È consigliabile visualizzarlo vengono visualizzate in `/site/wwwroot/webapps`.

7. Dopo aver copiato JSPHello.war alla directory webapp, Tomcat Server verranno installati automaticamente (decomprimere) file WAR per il file. Sebbene Tomcat Server inizia decompressione quasi immediatamente, potrebbe richiedere un valore long tempo (eventualmente ore) per i file vengano visualizzati nel client FTP.


#### <a name="run-the-hello-world-application-on-the-web-app"></a>Eseguire l'applicazione Hello World in Web App

1. Dopo aver caricato il file WAR e verificare che il server Tomcat ha creato un decompressi `JSPHello` directory, passare al `http://webdemowebapp.azurewebsites.net/JSPHello` per eseguire l'applicazione.

    > **Nota:** Se si fa clic su **Sfoglia** dal portale di classico, potrebbe essere visualizzato la pagina Web predefinita, dire "questa applicazione web Java basati su è stata creata correttamente." Potrebbe essere necessario aggiornare la pagina Web per visualizzare l'output dell'applicazione anziché una pagina Web predefinita.

2. Quando viene eseguita l'applicazione, viene visualizzata una pagina web con l'output seguente:

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### <a name="clean-up-azure-resources"></a>Pulire le risorse Azure

Questa procedura consente di creare un'app web di servizio di App. Per la fatturazione per la risorsa, purché esistente. A meno che non si prevede di continuare a usare l'app web per il testing o sviluppo, è consigliabile interrompere o eliminarlo. Un'applicazione web che è stata interrotta comporta sempre una piccola carica, ma è possibile riavviare in qualsiasi momento. Eliminazione di un'app web Cancella tutti i dati che sono caricati a esso.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

  [1]: ./media/java-create-azure-website-using-java-sdk/eclipse-maven-repositories-rebuild-index.png
  [2]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-java-class.png
  [3]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-dynamic-web-project.png
  [4]: ./media/java-create-azure-website-using-java-sdk/eclipse-java-build-path.png
  [5]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-tomcat-server.png
  [6]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-properties-page.png
  [7]: ./media/java-create-azure-website-using-java-sdk/eclipse-run-on-server.png
  [8]: ./media/java-create-azure-website-using-java-sdk/kudu-console-drag-drop.png
  [9]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-1.png
  [10]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-2.png
 

[Servizio App Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
[Installazione guidata piattaforma Web]: http://go.microsoft.com/fwlink/?LinkID=252838
[Azure Toolkit per Eclisse]: https://msdn.microsoft.com/library/azure/hh690946.aspx
[Portale classica Azure]: https://manage.windowsazure.com
[Che cos'è una directory di Azure Active Directory]: http://technet.microsoft.com/library/jj573650.aspx
[Creare e caricare un certificato di gestione di Azure]: ../cloud-services/cloud-services-certs-create.md
[Strumento di gestione di certificati (keytool) e chiave]: http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html
[WebSiteManagementClient]: http://azure.github.io/azure-sdk-for-java/com/microsoft/azure/management/websites/WebSiteManagementClient.html
[WebSpaceNames]: http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/models/WebSpaceNames.html
[Portale di Azure]: https://portal.azure.com
