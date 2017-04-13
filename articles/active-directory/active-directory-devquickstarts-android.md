<properties
    pageTitle="Azure Active Directory Android introduzione | Microsoft Azure"
    description="Modalità di compilazione di un'applicazione Android che si integra con Azure Active Directory per accedere e chiama Azure Active Directory protetto API utilizzando OAuth."
    services="active-directory"
    documentationCenter="android"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="integrate-azure-ad-into-an-android-app"></a>Integrare Azure Active Directory in un'App di Android

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)] 

Se si sviluppano un'applicazione desktop, Azure Active Directory rende semplice e diretto per l'autenticazione degli utenti con il proprio account Active Directory.  Consente inoltre sicuro utilizzare qualsiasi web API protetto da Azure Active Directory, ad esempio le API di Office 365 o l'API di Azure nell'applicazione.

Per i client Android necessaria per accedere alle risorse protette, Azure Active Directory è Active Directory Authentication Library o ADAL.  Obiettivo esclusiva di ADAL è per rendere più semplice per l'app ottenere i token di accesso.  Per mostrare solo quanto è semplice, qui verrà creata un'applicazione di Android elenco di attività che:

-   Ottiene access token per chiamare un API di elenco attività mediante il [protocollo di autenticazione OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Ottiene l'elenco di attività dell'utente
-   Gli utenti segni.

Per iniziare, è necessario un tenant di Azure Active Directory in cui è possibile creare utenti e registrare un'applicazione.  Se si dispone già di un tenant, [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

> [AZURE.TIP] Provare l'anteprima del nuovo [portale per sviluppatori](https://identity.microsoft.com/Docs/Android) che consentono di iniziare a utilizzarlo con Azure Active Directory in pochi minuti!  Portale per sviluppatori di verrà descritto il processo di registrazione di un'app e integrazione del codice di Azure Active Directory.  Al termine, si avrà una semplice applicazione che può eseguire l'autenticazione di utenti al tenant e un back-end che possono accettare i token ed eseguire la convalida. 

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>Passaggio 1: Scaricare ed eseguire nel Server dell'esempio Node TODO API REST

In questo esempio è scritto specificamente per l'utilizzo con un campione esistente per la creazione di un singolo tenant API REST cose da fare per Microsoft Azure Active Directory. Si tratta di un prerequisito per la Guida introduttiva.

Per informazioni su come specificare questa impostazione, visitare il nostro esempi di esistenti:

* [Servizio Microsoft Azure Active Directory esempio resto API per Node](active-directory-devquickstarts-webapi-nodejs.md)

## <a name="step-2-register-your-web-api-with-your-microsoft-azure-ad-tenant"></a>Passaggio 2: Registrare l'API Web di Microsoft Azure Active Directory Tenant

**Cosa sto facendo?**

*Microsoft Active Directory supporta l'aggiunta di due tipi di applicazioni. API che offrono servizi per gli utenti e applicazioni (sia sul web o un'applicazione in esecuzione in un dispositivo) che accedono a tali API Web database Web. In questo passaggio si registra l'API Web è in esecuzione di test in questo esempio. In genere questa API Web è un servizio REST che offre funzionalità che si desidera un'app per accedere. Microsoft Azure Active Directory è possibile proteggere qualsiasi endpoint!*

*Di seguito si suppone che si desidera registrare l'API REST TODO indicato in precedenza, ma funziona con qualsiasi API Web si Azure Active Directory per la protezione.*

Procedura per registrare un API Web di Microsoft Azure Active Directory

1. Accedere al [portale di gestione Azure](https://manage.windowsazure.com).
2. Fare clic su Active Directory esplorazione a sinistra
3. Fare clic su tenant directory in cui si desidera registrare l'applicazione di esempio.
4. Fare clic sulla scheda applicazioni.
5. Nel cassetto, fare clic su Aggiungi.
6. Fare clic su "Aggiungi un'applicazione di sviluppo di organizzazione".
7. Immettere un nome descrittivo per l'istruzione select di applicazione, ad esempio "TodoListService", "Applicazione Web e/o il API Web" e fare clic su Avanti.
8. Per l'URL di accesso, immettere l'URL di base per l'esempio, per impostazione predefinita `https://localhost:8080`.
9. Per URI ID App, immettere `https://<your_tenant_name>/TodoListService`, sostituire `<your_tenant_name>` con il nome del tenant di Azure Active Directory.  Fare clic su OK per completare la registrazione.
10. Nel portale di Azure, fare clic sulla scheda Configurazione dell'applicazione.
11. **Trovare il valore di ID Client e copiarlo prevedere**, sarà necessario seguente in un secondo momento durante la configurazione dell'applicazione.

## <a name="step-3-register-the-sample-android-native-client-application"></a>Passaggio 3: Registrare l'applicazione di Android Native Client di esempio

La registrazione dell'applicazione web è il primo passaggio. Successivamente, sarà necessario informare Azure Active Directory anche l'applicazione. In questo modo, l'applicazione di comunicare con l'API Web appena registrato

**Cosa sto facendo?**  

*Come descritto in precedenza, Microsoft Azure Active Directory supporta l'aggiunta di due tipi di applicazioni. API che offrono servizi per gli utenti e applicazioni (sia sul web o un'applicazione in esecuzione in un dispositivo) che accedono a tali API Web database Web. In questo passaggio si registra l'applicazione in questo esempio. È necessario eseguire che in ordine essere in grado di richiesta per accedere all'API Web che appena registrata questa applicazione. Azure Active Directory Rifiuta consentire anche l'applicazione chiedere queste informazioni per l'accesso a meno che non è registrato! Che fa parte di protezione del modello.*

*Di seguito si suppone che si desidera registrare l'applicazione di esempio indicata in precedenza, ma funziona con qualsiasi app che si sviluppa.*

**Perché viene aggiunta un'applicazione e un'API Web in un tenant?**

*Come può immaginare, è possibile creare un'applicazione che accede a una API esterna registrate in Azure Active Directory da un altro tenant. In questo caso, i clienti verranno richiesto di confermare l'uso delle API nell'applicazione. La parte pile consiste, Active Directory Authentication Library per iOS gestisce il proprio consenso dell'utente. Man mano alle caratteristiche più avanzate, si noterà che si tratta di una parte importante del lavoro necessario per accedere alla famiglia di Microsoft APIs da Azure e Office così come qualsiasi altro provider di servizi. Per ora, perché è stato registrato il API Web e l'applicazione in stesso tenant non è possibile vedere le istruzioni visualizzate per consenso. Si tratta in genere se si sviluppa un'applicazione solo per la propria società da utilizzare.*

1. Accedere al [portale di gestione Azure](https://manage.windowsazure.com).
2. Fare clic su Active Directory esplorazione a sinistra
3. Fare clic su tenant directory in cui si desidera registrare l'applicazione di esempio.
4. Fare clic sulla scheda applicazioni.
5. Nel cassetto, fare clic su Aggiungi.
6. Fare clic su "Aggiungi un'applicazione di sviluppo di organizzazione".
7. Immettere un nome descrittivo per l'applicazione, ad esempio "TodoListClient Android", selezionare "Native Client" e fare clic su Avanti.
8. Per URI reindirizzare immettere `http://TodoListClient`.  Fare clic su Fine.
9. Fare clic sulla scheda Configurazione dell'applicazione.
10. Trovare il valore di ID Client e copiarlo, sarà necessario seguente in un secondo momento durante la configurazione dell'applicazione.
11. "Le autorizzazioni da altre applicazioni", fare clic su "Aggiungi applicazione".  Selezionare "Altro" nell'elenco a discesa "Mostra" e fare clic sul segno di spunta superiore.  Individuare e fare clic sul TodoListService e fare clic sul segno di spunta nella parte inferiore per aggiungere l'applicazione.  Selezionare "Accesso TodoListService" nell'elenco a discesa "Delegato autorizzazioni" e salvare la configurazione.



Per creare con Maven, è possibile utilizzare la pom.xml al livello superiore

  * Duplicare questo repo in una directory di propria scelta:

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`  

  * Seguire i passaggi descritti nella [sezione Prerequests per impostare il maven per android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)
  * Configurazione emulatore con SDK 19
  * Passare alla cartella radice in cui si duplicati il repo
  * Eseguire il comando: installare mvn libera
  * Modificare la directory di esempio Guida introduttiva: cd samples\hello
  * Eseguire il comando: mvn android: distribuire android: Esegui
  * Dovrebbero essere visualizzati avvio app
  * Immettere le credenziali utente per test per provare!

Pacchetti di vaso verranno inviati anche accanto il pacchetto aar.

### <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>Passaggio 4: Scaricare Android ADAL e aggiungerlo nell'area di lavoro Eclisse

Sono state apportate semplice in cui sono disponibili più opzioni per l'utilizzo della raccolta di un progetto Android:

* È possibile utilizzare il codice sorgente per importare questa raccolta Eclisse e collegamento per l'applicazione.
* Se Usa Studio Android, è possibile utilizzare il formato di pacchetto *aar* e fanno riferimento i file binari.

####<a name="option-1-source-zip"></a>Opzione 1: Origine Zip

Per scaricare una copia del codice sorgente, fare clic su "ZIP di Download" sul lato destro della pagina oppure fare clic [qui](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz).

####<a name="option-2-source-via-git"></a>Opzione 2: Origine tramite fra

Per ottenere il codice sorgente di SDK tramite fra sufficiente digitare:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

####<a name="option-3-binaries-via-gradle"></a>Opzione 3: Binari tramite Gradle

È possibile ottenere i file binari da repo centrale Maven. Pacchetto AAR può essere inclusi come indicato di seguito in un progetto in AndroidStudio:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

####<a name="option-4-aar-via-maven"></a>Opzione 4: aar tramite Maven

Se si utilizza il plug-in m2e in Eclisse, è possibile specificare la relazione in un file di pom.xml:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


####<a name="option-5-jar-package-inside-libs-folder"></a>Opzione 5: pacchetto di vaso all'interno di librerie cartella
È possibile ottenere il file vaso da maven la repo e rilasciare nella cartella *librerie* di un progetto. È necessario copiare le risorse necessarie al progetto anche dal momento che i pacchetti di vaso non includono.


### <a name="step-5-add-references-to-android-adal-to-your-project"></a>Passaggio 5: Aggiungere i riferimenti agli Android ADAL al progetto


2. Aggiungere un riferimento a un progetto e specificare come una raccolta di Android. Se non si è certi come eseguire questa operazione, [fare clic qui per ulteriori informazioni] (http://developer.android.com/tools/projects/projects-eclipse.html)

3. Aggiunta della dipendenza di progetto per il debug in per le impostazioni del progetto

4. Aggiornare il file AndroidManifest.xml del progetto da includere:

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

7. Creare un'istanza di AuthenticationContext nella propria attività principale. I dettagli di questa chiamata non rientrano nell'ambito del file Leggimi, ma è possibile ottenere un buon inizio esaminando [Android esempio Client nativo](https://github.com/AzureADSamples/NativeClient-Android). Di seguito è illustrato un esempio:

    ```Java
    // Authority is in the form of https://login.windows.net/yourtenant.onmicrosoft.com
    mContext = new AuthenticationContext(MainActivity.this, authority, true); // This will use SharedPreferences as            default cache
    ```
  * Nota: mContext è un campo nell'attività

8. Copiare il blocco di codice per gestire la fine di AuthenticationActivity dopo aver immesso le credenziali e riceve il codice di autorizzazione:

    ```Java
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         if (mContext != null) {
             mContext.onActivityResult(requestCode, resultCode, data);
         }
     }
    ```

9. Per richiedere un token, è possibile definire un callback

    ```Java
    private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };
    ```
10. Infine, chiedere un token utilizzando il callback:

    ```Java
     mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                    callback);
    ```

Spiegazione dei parametri:

  * Risorsa è necessaria e risorse che si sta tentando di accedere.
  * ClientID è necessaria e proviene dal portale di AzureAD.
  * È possibile impostare redirectUri come il packagename. Non è necessaria per la chiamata acquireToken.
  * PromptBehavior consente di richiedere le credenziali ignorare cache e cookie.
  * Verrà chiamato callback dopo scambio di codice di autorizzazione per un token.

  Il Callback avrà un oggetto di AuthenticationResult con accesstoken, data di scadenza e informazioni idtoken.

Facoltativo: **acquireTokenSilent**

È possibile chiamare **acquireTokenSilent** per gestire la memorizzazione nella cache e aggiornamento dei token. Vengono forniti anche versione sincronizzazione. Accetta ID utente come parametro.

    ```java
     mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

11. **Gestore**: app del portale aziendale di Microsoft Intune fornirà il componente gestore. ADAL verrà Usa l'account di gestore, se esiste un account utente viene creato in questa autenticazione e sviluppo scegliere di non essendo obbligatoria. Sviluppo possibile ignorare l'utente gestore con:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```

 Lo sviluppatore dovrà eseguire la registrazione redirectUri speciali per l'utilizzo di gestore. RedirectUri è nel formato di msauth://packagename/Base64UrlencodedSignature. È possibile ottenere il redirecturi per l'app utilizzando lo script "brokerRedirectPrint.ps1" o utilizzare API chiamata mContext.getBrokerRedirectUri. I certificati di firma corrisponde alla riga della firma.

 Modello gestore corrente è per un utente. AuthenticationContext fornisce API metodo per ottenere l'utente gestore.

 ```java
 String brokerAccount =  mContext.getBrokerUser();
 ```
 Gestore viene visualizzata se l'account è valido.

 Manifesto dell'app deve disporre delle autorizzazioni per usare gli account per AccountManager: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


Utilizzare questa procedura dettagliata, è necessario disporre è necessario integrare correttamente con Azure Active Directory. Per altri esempi di questo lavoro, visitare il AzureADSamples / archivio in GitHub.

## <a name="important-information"></a>Informazioni importanti

### <a name="customization"></a>Personalizzazione

Le risorse del progetto di una raccolta possono essere sovrascritti dalle risorse dell'applicazione. Si verifica questo evento durante la creazione di un'applicazione. Per questo motivo, è possibile personalizzare il layout di attività di autenticazione nel modo desiderato. È necessario assicurarsi di mantenere l'id dei controlli che uses(Webview) ADAL.

### <a name="broker"></a>Gestore

Componente gestore verrà recapitati con app del portale aziendale di Microsoft Intune. L'account verrà creato in Account Manager. Tipo di account è "com.microsoft.workaccount". Consente solo singolo account Single Sign-on. Dopo aver completato in attesa di dispositivo per una delle App verrà creato cookie Single Sign-on per questo utente.

### <a name="authority-url-and-adfs"></a>ADFS e Url dell'autorità

ADFS non è riconosciuto come produzione servizio token di sicurezza, pertanto è necessario attivare di individuazione istanza e passare false al costruttore AuthenticationContext.

Url dell'autorità deve istanza servizio token di sicurezza e il nome tenant: https://login.windows.net/yourtenant.onmicrosoft.com

### <a name="querying-cache-items"></a>La ricerca di elementi nella cache

ADAL fornisce cache predefinita in SharedPreferences con alcune semplice cache funzioni di query. È possibile ottenere la cache corrente da AuthenticationContext con:
```Java
 ITokenCacheStore cache = mContext.getCache();
```
È possibile fornire l'implementazione della cache, anche se si desidera personalizzarlo.
```Java
mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);
```

### <a name="promptbehavior"></a>PromptBehavior

ADAL è possibile specificare comportamento. Se non è valido token di aggiornamento e sono necessarie le credenziali utente dell'interfaccia utente verrà visualizzata PromptBehavior.Auto. PromptBehavior.Always ignorerà l'utilizzo della cache e mostrare sempre dell'interfaccia utente.

### <a name="silent-token-request-from-cache-and-refresh"></a>Richiesta di token invisibile dalla cache e aggiornamento

Questo metodo non utilizza pop dell'interfaccia utente configurazione e non richiede un'attività. Restituisce il token dalla cache, se disponibile. Se è scaduto token, tenterà di aggiornarla. Se l'aggiornamento token è scaduto o non è riuscita, verrà restituito AuthenticationException.

    ```Java
    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

È anche possibile sfruttare sincronizzazione chiamare con questo metodo. È possibile impostare null callback oppure utilizzare acquireTokenSilentSync.

### <a name="diagnostics"></a>Diagnostica

Di seguito sono le fonti di informazioni per la diagnosi problemi principali:

+ Eccezioni
+ Log
+ Tracce di rete

Inoltre, tenere presente che gli ID di correlazione centrali per i test di diagnostica nella raccolta. È possibile impostare la correlazione ID in base al richiesta se si desidera creare una relazione tra un ADAL richiesta con altre operazioni nel codice. Se non si imposta un id correlazioni quindi ADAL verrà generato un casuale uno and tutti i messaggi di log e le chiamate di rete verranno contrassegnate con l'id di correlazione. Self generato modifiche id su ogni richiesta.

#### <a name="exceptions"></a>Eccezioni

Si tratta evidenti Diagnostics prima. Si tenta di fornire utili messaggi di errore. Se è presente uno che non è comunque utile segnalare un problema e indicare. Anche fornire informazioni sul dispositivo, ad esempio modello e SDK #.

#### <a name="logs"></a>Log

È possibile configurare la raccolta per generare messaggi di log è possibile utilizzare per diagnosticare i problemi. Per configurare la registrazione, effettuare la chiamata seguente per configurare un callback ADAL utilizzerà per disattivare ogni messaggio log come viene generato.


 ```Java
 Logger.getInstance().setExternalLogger(new ILogger() {
     @Override
     public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
      ...
      // You can write this to logfile depending on level or errorcode.
      writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
     }
 }
 ```
Come indicato di seguito, è possono creare messaggi da un file di log personalizzato. Purtroppo non è possibile standard di recupero dei registri da un dispositivo. Esistono alcuni servizi che consentono di con questo valore. È possibile anche creare personalizzato, ad esempio inviare il file a un server.

```Java
private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
}
```

##### <a name="logging-levels"></a>Livelli di registrazione

+ Error(Exceptions)
+ Warn(Warning)
+ Info (informativo)
+ Dettagliato (ulteriori dettagli)

Impostare il livello di registrazione alla seguente:
```Java
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);
 ```

 Registro tutti i messaggi inviati a logcat oltre a qualsiasi callback log personalizzato.
È possibile ottenere log a logcat un modulo file come belog illustrato:

 ```
  adb logcat > "C:\logmsg\logfile.txt"
 ```
 Ulteriori esempi sulle adb cmd: https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat

#### <a name="network-traces"></a>Tracce di rete

È possibile usare vari strumenti per acquisire il traffico HTTP ADAL generato da.  Questa operazione è particolarmente utile se si ha familiarità con il protocollo OAuth o se è necessario fornire le informazioni di diagnostica Microsoft o altri canali di supporto.

Fiddler è lo strumento di analisi HTTP più semplice.  Utilizzare i collegamenti seguenti per configurare fino a correttamente record ADAL traffico di rete.  Per essere utile, è necessario configurare fiddler o altri strumenti, ad esempio Charles per registrare il traffico SSL non crittografato.  Nota: Le tracce generate in questo modo possono contenere informazioni privilegi elevati, ad esempio i token di accesso, nomi utente e password.  Se si usa l'account di produzione, non si condividono le tracce con parti 3 °.  Se è necessario fornire una traccia a un utente per ottenere supporto, riprodurre il problema con un account temporaneo con i nomi utente e la password che non si voglia condivisione.

+ [La configurazione Fiddler per Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
+ [Configurare regole di Fiddler per ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)


### <a name="dialog-mode"></a>Modalità finestra di dialogo
metodo acquireToken senza attività supporta le finestre di dialogo.

### <a name="encryption"></a>Crittografia

ADAL crittografa i token e store in SharedPreferences per impostazione predefinita. È possibile esaminare la classe StorageHelper per visualizzare i dettagli. Android introdotto AndroidKeyStore per l'archiviazione protetta 4.3(API18) delle chiavi private. ADAL utilizzati per API18 e versioni successive. Se si desidera utilizzare ADAL per le versioni SDK inferiore, è necessario fornire segreto in AuthenticationSettings.INSTANCE.setSecretKey

### <a name="oauth2-bearer-challenge"></a>Verifica del titolare Oauth2

Classe AuthenticationParameters fornisce funzionalità per ottenere il authorization_uri da verifica titolare Oauth2.

### <a name="session-cookies-in-webview"></a>Cookie di sessione in visualizzazione Web

La visualizzazione Web Android non consente di cancellare i cookie della sessione dopo la chiusura di app. È possibile gestire con codice di esempio riportato di seguito:
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
Ulteriori informazioni sui cookie: http://developer.android.com/reference/android/webkit/CookieSyncManager.html

### <a name="resource-overrides"></a>Override di risorse

Raccolta di ADAL include stringhe inglesi per i messaggi di ProgressDialog due seguenti.

L'applicazione deve sovrascriverli se si desidera stringhe localizzate.

```Java
<string name="app_loading">Loading...</string>
<string name="broker_processing">Broker is processing</string>
<string name="http_auth_dialog_username">Username</string>
<string name="http_auth_dialog_password">Password</string>
<string name="http_auth_dialog_title">Sign In</string>
<string name="http_auth_dialog_login">Login</string>
<string name="http_auth_dialog_cancel">Cancel</string>
```

=======

### <a name="ntlm-dialog"></a>Finestra di dialogo NTLM
Versione adal 1.1.0 supporta NTLM finestra di dialogo che viene elaborato tramite onReceivedHttpAuthRequest evento da WebViewClient. Possono essere personalizzate stringhe e finestra di dialogo layout.

### <a name="cross-app-sso"></a>Cross-app Single Sign-on
Informazioni su [come abilitare tra app Single Sign-On in Android utilizzando ADAL](active-directory-sso-android.md)  


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
