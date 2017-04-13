<properties
    pageTitle="Azure B2C Directory attiva: Chiamare dell'API web da un'applicazione Android | Microsoft Azure"
    description="In questo articolo viene illustrato come creare un dispositivo Android 'elenco' app che chiama web Node API utilizzando i token del titolare OAuth 2.0. App Android e le API web consente di Azure Active Directory B2C gestione delle identità utente e autenticazione degli utenti."
    services="active-directory-b2c"
    documentationCenter="android"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure Active Directory B2C: Chiamare dell'API web da un'applicazione Android

> [AZURE.WARNING] In questa esercitazione richiede alcuni aggiornamenti importanti, in particolare per rimuovere l'uso di ADAL Android per B2C.  Procedere alla pubblicazione aggiornate istruzioni per l'uso di Azure Active Directory B2C nelle App Android della settimana successiva ed è consigliabile tenere fino a quel momento.  Ma se si vuole semplicemente tentare di elementi, è possibile continuare con l'articolo seguente.



Utilizzando B2C Azure Active Directory (Azure Active Directory), è possibile aggiungere funzionalità di gestione delle identità potente strumento self-service per il web API in pochi passaggi brevi e App di Android. In questo articolo viene illustrato come creare un dispositivo Android "elenco da fare" app che chiama web Node API utilizzando i token del titolare OAuth 2.0. App Android e le API web consente di Azure Active Directory B2C gestione delle identità utente e autenticazione degli utenti.

Questa Guida introduttiva è necessario disporre dell'API protetto da Azure Active Directory con B2C per lavorare completamente un web. SDL si basano uno per .NET e node per l'uso. Per eseguire questa procedura è configurato l'esempio dell'API web Node. Per ulteriori informazioni, vedere [web Azure Active Directory B2C API per Node esercitazione](active-directory-b2c-devquickstarts-api-node.md).

Per i client Android necessaria per accedere alle risorse protette, Azure Active Directory offre la raccolta di autenticazione Active Directory (ADAL). L'unico scopo di ADAL è per rendere più semplice per l'app ottenere i token di accesso. Per illustrare quanto sia semplice, in questa guida è necessario creare un'applicazione di elenco attività Android che:

- Ottiene i token di accesso da un elenco di cose da fare API chiamate utilizzando il [protocollo di autenticazione OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
- Ottiene gli elenchi di attività degli utenti.
- Segni gli utenti.

> [AZURE.NOTE] In questo articolo non copre come implementare l'accesso, per l'abbonamento e gestione dei profili con Azure Active Directory B2C. Incentrata sulle modalità di chiamata web API dopo l'autenticazione dell'utente. Se non è ancora disponibile, è necessario avviare con [.NET web app esercitazione introduttiva](active-directory-b2c-devquickstarts-web-dotnet.md) per informazioni sui concetti di base di Azure Active Directory B2C.

## <a name="get-an-azure-ad-b2c-directory"></a>Ottenere una directory di Azure Active Directory B2C

Prima di poter usare B2C di Azure Active Directory, è necessario creare una directory o del tenant. Una directory è un contenitore per tutti gli utenti, App, gruppi e altro. Se non è una già disponibile, [creare una directory B2C](active-directory-b2c-get-started.md) prima di procedere in questa Guida.

## <a name="create-an-application"></a>Creare un'applicazione

È necessario creare un'app nella directory B2C. In questo modo le informazioni di Azure Active Directory che è necessario comunicare in modo sicuro con l'app. Web API e app sono rappresentati da un singolo **ID applicazione** in questo caso, poiché comprendono un'app logica. Per creare un'app, seguire [queste istruzioni](active-directory-b2c-app-registration.md). Assicurarsi di:

- Includere un' **app web**/**dell'API web** nell'applicazione.
- Immettere `urn:ietf:wg:oauth:2.0:oob` come **URL di risposta**. È l'URL predefinito per questo esempio di codice.
- Creare un **segreto applicazione** per l'applicazione e copiarlo. Sarà necessario in un secondo momento. Si noti che questo valore deve essere [XML escape](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) prima di utilizzare.
- Copiare l' **ID dell'applicazione** che sono state assegnate a un'applicazione. È anche necessario seguente in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Creare i criteri

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

In Azure Active Directory B2C, ogni esperienza utente è definita da un [criterio](active-directory-b2c-reference-policies.md). Questa app contiene tre esperienze identità: iscriversi, accedere e accedere con Facebook.  È necessario creare un criterio di ogni tipo, come descritto nell' [articolo di riferimento dei criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando si creano i tre criteri, assicurarsi di:

- Scegliere il **nome visualizzato** e altri attributi iscrizione nei criteri di iscrizione.
- Scegliere le richieste di applicazione **nome visualizzato** e **ID** in ogni criterio. È possibile scegliere anche altri reclami.
- Copiare il **nome** di ogni criterio dopo averlo creato. Deve avere il prefisso `b2c_1_`.  È necessario i nomi dei criteri in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato tre criteri, si è pronti per creare l'applicazione.

Si noti che in questo articolo non illustra come utilizzare i criteri che appena creato. Per informazioni su come funzionano i criteri in Azure Active Directory B2C, iniziare con [.NET web app esercitazione introduttiva](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Scaricare il codice

Il codice per questa esercitazione [viene mantenuto per GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Per creare il campione durante il lavoro, è possibile [scaricare il progetto di base come file ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip). È anche possibile duplicare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE] **Viene richiesto di scaricare la struttura per completare questa esercitazione.** A causa di complessità dell'implementazione di un'applicazione Android completa, la struttura include codice dell'esperienza utente che verrà eseguito dopo avere completato questa esercitazione. Questa è una misura di risparmiare tempo per gli sviluppatori. Il codice dell'esperienza utente non è pertinenti per l'argomento relativo all'aggiunta B2C a un'applicazione Android.

L'app completa è [disponibile](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) anche come file con estensione zip o sul `complete` ramo dell'archivio stesso.

Per creare con Maven, è possibile utilizzare `pom.xml` al livello superiore.

  1. Seguire i passaggi indicati nella [sezione Prerequisiti configurare Maven per Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  2. Configurare un emulatore con SDK 21.
  3. Passare alla cartella radice in cui si duplicati il repo.
  4. Eseguire il comando `mvn clean install`.
  5. Passare alla directory l'esempio di Guida introduttiva `cd samples\hello`.
  6. Eseguire il comando `mvn android:deploy android:run`.

Verrà visualizzata l'app di avvio. Immettere le credenziali utente per test per provare a.

Pacchetti di archivio Java (VASO) verranno inviati anche accanto il pacchetto di archivio Android (AAR).

## <a name="download-the-android-adal-and-add-it-to-your-android-studio-workspace"></a>Scaricare ADAL Android e aggiungerlo nell'area di lavoro di Studio Android

Sono disponibili opzioni per la procedura per l'utilizzo della raccolta di un progetto Android:

* È possibile usare il codice sorgente di importare la libreria Eclisse e creare un collegamento all'applicazione.
* Se si usa Studio Android, è possibile utilizzare il formato del pacchetto AAR e i file binari fare riferimento.

### <a name="option-1-binaries-via-gradle-recommended"></a>Opzione 1: File binari tramite Gradle (scelta consigliata)

È possibile ottenere i file binari dal repo centrale Maven. Il pacchetto AAR può essere inclusi nel progetto di Studio Android (ad esempio, in `build.gradle`) in questo modo:

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
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

### <a name="option-2-aar-via-maven"></a>Opzione 2: AAR tramite Maven

Se si usa il `m2e` plug-in Eclisse, è possibile specificare la dipendenza nel `pom.xml` file:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

### <a name="option-3-source-via-git-last-resort"></a>Opzione 3: Origine tramite fra (ultimo risorsa)

Per ottenere il codice sorgente di SDK tramite fra, immettere:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

Utilizzare la diramazione **convergenza.**

## <a name="set-up-your-configuration-file"></a>Configurare il file di configurazione

Utilizzare la configurazione di configurazione precedente nel portale di B2C per configurare il progetto Android.

Apri `helpes/Constants.java` e immettere i valori per le operazioni seguenti:

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
- `SCOPES`: Gli ambiti passare al server che si desidera richiedere dal server quando un utente accede. Per l'anteprima B2C passare `client_id`. Tuttavia, è previsto per passare alla `read scopes` in futuro. Verrà aggiornato il documento che si verifica.
- `ADDITIONAL_SCOPES`: Gli ambiti aggiuntivi da utilizzare per l'applicazione. Previsti da utilizzare in futuro.
- `CLIENT_ID`: L'ID dell'applicazione ottenuto dal portale di.
- `REDIRECT_URL`: Il reindirizzamento in cui si prevede che il token verrà inserito indietro.
- `EXTRA_QP`: Alcun componente aggiuntivo si desidera passare al server in un formato con codifica URL.
- `FB_POLICY`: Si sta chiamando il criterio. Questa è la parte più importante per questa procedura.
- `EMAIL_SIGNIN_POLICY`: Si sta chiamando il criterio. Questa è la parte più importante per questa procedura.
- `EMAIL_SIGNUP_POLICY`: Si sta chiamando il criterio. Questa è la parte più importante per questa procedura.

## <a name="add-references-to-android-adal-to-your-project"></a>Aggiungere i riferimenti agli Android ADAL al progetto

> [AZURE.NOTE]  ADAL per Android utilizza un modello basato su intento per richiamare l'autenticazione. Intenti "il layout" app per lo svolgimento delle attività. In questo esempio intero e tutti ADAL per Android, centri su come gestire intenti e passare informazioni tra di esse.

Prima di tutto, informare Android il layout dell'applicazione, inclusi i tipi di cui che si desidera utilizzare. Questi intenti verranno spiegati dettagliatamente più avanti in questa esercitazione.

Aggiornare il progetto `AndroidManifest.xml` per includere tutti i tipi di file:

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

Come si può notare, è possibile definire cinque attività. Occorre usare tutte le regole.

- `AuthenticationActivity`: Questo deriva dalla ADAL e fornisce la visualizzazione web di accesso.
- `LoginActivity`: Consente di visualizzare i criteri di accesso e i pulsanti per ogni criterio.
- `SettingsActivity`: Consente di modificare le impostazioni di app in fase di esecuzione.
- `AddTaskActivity`: Consente di aggiungere attività all'API REST che sono protetti da Azure Active Directory.
- `ToDoActivity`: Questa opzione è l'attività principale che visualizza le attività.

## <a name="create-the-sign-in-activity"></a>Creare l'attività di accesso

Creare un'attività principale e chiamarlo `LoginActivity`.

Creare un file denominato `LoginActivity.java`.

È necessario inizializzare l'attività e aggiungere alcuni pulsanti che consentono di controllare l'interfaccia utente. Si tratta familiare se è scritto codice Android prima.

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
È stata creata pulsanti che richiamano i `ToDoActivity` finalità (che chiama ADAL quando è necessario un token). Quello utilizzando l'attività come un riferimento e un parametro aggiuntivo. Questo parametro aggiuntivo viene passato per il `intent.putExtra()` metodo. È possibile definire `"thePolicy"` tramite specificato nel `Constants.java`. In questo modo lo scopo del criterio da richiamare durante l'autenticazione.

## <a name="create-the-settings-activity"></a>Creare attività impostazioni

Si tratta di un'attività che consente di compilare le impostazioni dell'interfaccia utente.

Creare un file denominato `SettingsActivity.java` per semplice creare, leggere, aggiornare ed eliminazione (CRUD).

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

    //private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_settings);

        loadSettings();
//      checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//      checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


    }

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

    private void saveSettings(String key, boolean value) {
        SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
                Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
        Editor prefsEditor = prefs.edit();
        prefsEditor.putBoolean(key, value);
        prefsEditor.commit();
    }
}
```

## <a name="create-the-add-task-activity"></a>Creare attività aggiungere attività

È possibile utilizzare questa attività per aggiungere un'attività per l'endpoint API REST.

Creare un file denominato `AddTaskActivity.java` e scrivere le operazioni seguenti.

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

## <a name="create-the-to-do-list-activity"></a>Creare attività di elenco da fare

Questo è l'attività più importante. È possibile utilizzarla per ottenere un token da Azure Active Directory per i criteri e quindi utilizzare tale token per chiamare il server di API REST di attività.

Creare un file denominato `ToDoActivity.java` e scrivere le operazioni seguenti. (Le chiamate verranno spiegate in un secondo momento.)

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;


    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}

```


 Si sarà notato che questa operazione si basa sui metodi che non è ancora stato scritto. Includono `updateLoggedInUser()`, `clearSessionCookie()`, e `getTasks()`. È possibile scrivere quelli più avanti in questa Guida. È possibile ignorare gli errori di Studio Android per ora.

Spiegazione dei parametri:

  - `SCOPES`: Obbligatorio, gli ambiti si sta tentando di richiedere l'accesso. Per l'anteprima B2C questo è diverso da quello di `client_id`, ma è previsto che cambiare in futuro.
  - `POLICY`: Il criterio di quando la modalità di autenticazione dell'utente.
  - `CLIENT_ID`: Obbligatorio, proviene dal portale di Azure Active Directory.
  - `redirectUri`: Può essere configurato come nome del pacchetto. Non è necessario prevedere il `acquireToken` chiamata.
  - `getUserInfo()`: Il modo per cercare informazioni su se un utente è già nella cache. Il parametro viene inoltre descritto come richiedere all'utente se tale utente non viene trovato o token di accesso dell'utente non è valido. Questo metodo verrà scritto più avanti in questa Guida.
  - `PromptBehavior.always`: Consente di richiedere le credenziali ignorare la cache e i cookie.
  - `Callback`: Chiamato dopo che un codice di autorizzazione verrà scambiato per un token. Avrà un oggetto `AuthenticationResult`, che contiene token di accesso, data di scadenza e le informazioni sul token ID.

> [AZURE.NOTE]  L'app portale aziendale di Microsoft Intune è disponibile il componente gestore e potrebbe essere installato nel dispositivo dell'utente. L'app fornisce single sign-on (SSO) l'accesso a tutte le applicazioni sul dispositivo. Gli sviluppatori devono essere preparati a consentire Intune. Se esiste un account utente creato in autenticatore, ADAL per Android utilizzerà l'account di gestore. Per utilizzare il gestore, è necessario registrare una speciale lo sviluppatore `redirectUri` per il gestore da utilizzare. `redirectUri`è nel formato di msauth://packagename/Base64UrlencodedSignature. È possibile ottenere `redirectUri` per l'app utilizzando lo script `brokerRedirectPrint.ps1` o tramite la chiamata API `mContext.getBrokerRedirectUri()`. La firma è correlata ai certificati di firma da Google Play store.

 È possibile ignorare l'utente gestore utilizzando:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE] Per ridurre la complessità di questa Guida introduttiva B2C, si è scelto di ignorare il gestore di esempio.

Creare metodi di supporto che ottenere il token solo durante le chiamate di autenticazione all'API di attività.

Nella stessa `ToDoActivity.java` file, scrivere quanto segue.

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

Aggiungere anche metodi che verranno "impostazione" e "Ottieni" `AuthenticationResult` (che ha il token) al modello globale `Constants`. Anche se `ToDoActivity.java` utilizza `sResult` nella relativa flussi, è necessario aggiungere questi metodi. In caso contrario, le altre attività non sarà possibile accedere al token per lo svolgimento delle attività (ad esempio aggiungere un'attività in `AddTaskActivity.java`).

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }


```
## <a name="create-a-method-to-return-a-user-identifier"></a>Creare un metodo per restituire un ID utente

ADAL per Android rappresenta l'utente in forma di una `UserIdentifier` oggetto. Consente di gestire l'utente. È possibile utilizzare l'oggetto per identificare se lo stesso nome utente viene utilizzato nelle chiamate. Con questa informazione, è possibile si basano sulla cache, anziché effettuare una chiamata di nuova al server. Per semplificare, sono stati creati i `getUserInfo()` metodo che restituisce `UserIdentifier`. È possibile utilizzare questa operazione con `acquireToken()`. È inoltre possibile creare un `getUniqueId()` metodo che restituisce l'ID di `UserIdentifier` nella cache.

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }

```

### <a name="write-helper-methods"></a>Scrivere metodi di supporto

Scrivere quindi alcuni metodi di supporto per cancellare i cookie e fornire `AuthenticationCallback`. Questi metodi vengono utilizzati esclusivamente per il campione per verificare che si è in uno stato pulito quando si chiama il `ToDo` attività.

Nello stesso file denominato `ToDoActivity.java`, scrivere quanto segue.

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
```

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }

```   

## <a name="call-the-task-api"></a>Chiamare l'API di attività

Dopo aver ottenuto l'attività pronti per acquisire i token, è possibile scrivere l'API per accedere al server delle attività.

`getTasks`fornisce una matrice che rappresenta le attività nel server.

Iniziare con `getTasks`.

Nello stesso file denominato `ToDoActivity.java`, scrivere quanto segue.

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }

```

Anche scrivere un metodo che verrà inizializzato le tabelle alla prima esecuzione.

Nello stesso file denominato `ToDoActivity.java`, scrivere quanto segue.

```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```

È possibile vedere che questo codice richiede alcuni metodi aggiuntivi per eseguire il proprio lavoro. Scrivere tali Avanti.

### <a name="create-an-endpoint-url-generator"></a>Creare un generatore di URL endpoint

È necessario generare l'URL di endpoint che è necessario connettersi. Farlo nello stesso file di classe.

**Nello stesso file** denominato `ToDoActivity.java`, scrivere quanto segue.

 ```
    private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

 ```


Nota aggiungere token di accesso alla richiesta di codice descritto nella sezione successiva.

## <a name="write-some-ux-methods"></a>Scrivere alcuni metodi dell'esperienza utente

Android è necessario gestire alcuni di questi callback per il funzionamento dell'app. Si tratta `createAndShowDialog` e `onResume()`. Si tratta familiare se è scritto codice Android prima.

Nello stesso file denominato `ToDoActivity.java`, scrivere quanto segue.

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }


```

Successivamente, gestire callback la finestra di dialogo.

Nello stesso file denominato `ToDoActivity.java`, scrivere quanto segue.

```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }

```

È necessario avere un `ToDoActivity.java` file che consente di compilare. L'intero progetto anche necessario compilare questo punto.

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

Infine, creare ed eseguire l'app in Studio Android o Eclisse. Effettuare l'iscrizione o dell'accesso nell'app. Creare attività per l'utente effettuato l'accesso. Disconnettersi e accedere come un altro utente e quindi creare attività per l'utente.

Si noti che le attività sono archiviate per utente sull'API, poiché l'API estrae l'identità dell'utente da token di accesso che riceve.

Per riferimento, l' esempio completo [fornito come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). È possibile anche clonare da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


## <a name="important-information"></a>Informazioni importanti


### <a name="encryption"></a>Crittografia

ADAL crittografa il token e store in `SharedPreferences` per impostazione predefinita. È possibile esaminare il `StorageHelper` classe per visualizzare i dettagli. Android introdotto **AndroidKeyStore per 4.3(API18)** archiviazione protetta delle chiavi private. ADAL utilizzati per API18 e versioni successive. Se si desidera utilizzare ADAL per le versioni SDK inferiore, è necessario fornire una chiave segreta in `AuthenticationSettings.INSTANCE.setSecretKey`.

### <a name="session-cookies-in-web-view"></a>Cookie di sessione in visualizzazione web

La visualizzazione web Android non consente di cancellare i cookie della sessione dopo la chiusura dell'app. È possibile gestire con il codice di esempio seguente.
```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
[Ulteriori informazioni sui cookie](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).
