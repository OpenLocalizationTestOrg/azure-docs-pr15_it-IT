<properties
    pageTitle="Azure Active Directory linguaggio introduzione | Microsoft Azure"
    description="Come creare un'app di riga di comando di linguaggio che accede agli utenti per accedere a un'API."
    services="active-directory"
    documentationCenter="java"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
  ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>


# <a name="using-java-command-line-app-to-access-an-api-with-azure-ad"></a>Utilizzo di linguaggio della riga di comando App per accedere a un'API con Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure Active Directory rende semplice e diretto affidare la gestione delle identità dell'applicazione web, fornendo singolo accesso e disconnessione con poche righe di codice.  In linguaggio web App, è possibile eseguire questa operazione utilizzando implementazione Microsoft di ADAL4J basato sulla community.

  Di seguito si userà ADAL4J per:
- Accedere all'app utilizzando Azure Active Directory come provider di identità dell'utente.
- Visualizzare le informazioni relative all'utente.
- Eseguire l'accesso all'utente dall'app.

Per eseguire questa operazione, è necessario:

1. Registrare un'applicazione di Azure Active Directory
2. Configurare l'app per utilizzare la libreria ADAL4J.
3. Utilizzare la raccolta di ADAL4J per inoltrare le richieste di accesso e disconnessione di Azure Active Directory.
4. Stampare i dati relativi all'utente.

Per iniziare a, [scaricare la struttura di app](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) o il [download di esempio completo](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\/archive/complete.zip).  È necessario anche un tenant di Azure Active Directory in cui si desidera registrare l'applicazione.  Se non hai già, [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>1. registrare un'applicazione di Azure Active Directory
Per abilitare l'app eseguire l'autenticazione degli utenti, è necessario prima di registrare una nuova applicazione nel tenant.

- Accedere al portale di gestione Azure.
- Nel riquadro di spostamento a sinistra, fare clic su **Active Directory**.
- Selezionare il tenant nel punto in cui si desidera registrare l'applicazione.
- Fare clic sulla scheda **applicazioni** e fare clic su Aggiungi nel cassetto inferiore.
- Seguire le istruzioni e creare una nuova **applicazione Web e/o WebAPI**.
    - Il **nome** dell'applicazione per descrivere l'applicazione agli utenti finali
    - L' **URL di accesso** è l'URL di base dell'app.  Il valore predefinito della struttura è `http://localhost:8080/adal4jsample/`.
    - **App ID URI** è un identificatore univoco dell'applicazione.  La convenzione consiste nell'usare `https://<tenant-domain>/<app-name>`, ad esempio`http://localhost:8080/adal4jsample/`
- Dopo aver completato la registrazione, AAD assegna l'app un identificatore univoco client.  È necessario questo valore nelle sezioni successive, quindi copiarlo dalla scheda Configura.

Una volta nel portale per l'app creazione di un' **Applicazione segreto** per l'applicazione e copiare verso il basso.  Sarà necessario più avanti.


## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisites-using-maven"></a>2. configurare l'app da usare ADAL4J libreria e i prerequisiti utilizzando Maven
In questo caso, si verrà configurare ADAL4J per utilizzare il protocollo di autenticazione OpenID connettersi.  ADAL4J verrà utilizzato per eseguire le richieste di accesso e disconnessione, gestire la sessione dell'utente e ottenere informazioni sull'utente, tra l'altro.

-   Nella directory radice del progetto, apertura/creazione `pom.xml` e individuare il `// TODO: provide dependencies for Maven` e sostituire con le operazioni seguenti:

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>public-client-adal4j-sample</artifactId>
    <packaging>jar</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>public-client-adal4j-sample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.nimbusds</groupId>
            <artifactId>oauth2-oidc-sdk</artifactId>
            <version>4.5</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20090211</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>
</dependencies>
    <build>
        <finalName>public-client-adal4j-sample</finalName>
        <plugins>
                <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install</id>
                        <phase>install</phase>
                        <goals>
                            <goal>sources</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
        </plugins>
    </build>

</project>


```



## <a name="3-create-the-java-publicclient-file"></a>3. creare il file di PublicClient java

Come indicato in precedenza, si utilizzeranno l'API di grafico per recuperare i dati relativi all'utente ha effettuato l'accesso. Abbiamo bisogna creare un file per rappresentare un **Oggetto Directory** e un singolo file per rappresentare l' **utente** in modo che il modello OO del linguaggio può essere utilizzato per in modo che sia facile per noi.

1. Creare un file denominato `DirectoryObject.java` che verrà usata per archiviare i dati su qualsiasi DirectoryObject (è possibile è possibile utilizzare questo valore in un secondo momento per tutte le altre query grafico è possibile eseguire). È possibile Taglia e incolla questo tra i seguenti:

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


##<a name="compile-and-run-the-sample"></a>Compilare ed eseguire l'esempio

Modificare indietro alla directory radice ed eseguire il seguente comando per generare l'esempio è inserire collaborare usando `maven`. Verrà utilizzato il `pom.xml` file è stato scritto per le dipendenze.

`$ mvn package`

È necessario avere un `adal4jsample.war` dei file nel `/targets` directory. È possibile distribuire che nel contenitore di Tomcat e visitare l'URL 

`http://localhost:8080/adal4jsample/`


> [AZURE.NOTE] 
È molto semplice distribuire una GUERRA con i server Tomcat più recente. Spostarsi semplicemente a `http://localhost:8080/manager/` e seguire le istruzioni visualizzate durante il caricamento del ' adal4jsample.war' file. Verrà autodeploy automaticamente con l'endpoint corretto.

##<a name="next-steps"></a>Passaggi successivi

Congratulazioni! Ora è un'applicazione di linguaggio che è in grado di eseguire l'autenticazione di utenti, in modo sicuro funzionante API Web usando 2.0 OAuth di chiamata e ottenere informazioni di base relative all'utente.  Se non è ancora disponibile, è il momento per popolare il tenant con alcuni utenti.

Per riferimento, di esempio completo (senza i valori di configurazione) [viene fornito come un file ZIP](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip)oppure è possibile duplicare il da GitHub:

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

