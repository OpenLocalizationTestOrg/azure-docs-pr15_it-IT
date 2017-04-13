<properties
    pageTitle="Guida introduttiva a hub di notifica di Azure utilizzando Baidu | Microsoft Azure"
    description="In questa esercitazione si imparerà a utilizzare Azure notifica hub per le notifiche push per dispositivi Android con Baidu."
    services="notification-hubs"
    documentationCenter="android"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="mobile-baidu"
    ms.workload="mobile"
    ms.date="08/19/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-using-baidu"></a>Guida introduttiva a hub di notifica tramite Baidu

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Panoramica

Baidu cloud push è un servizio cloud cinese che è possibile utilizzare per inviare le notifiche push per dispositivi mobili. Questo servizio è particolarmente utile in Cina, in cui il recapito delle notifiche push per Android è complesso a causa la presenza di archivi diversi app e servizi push, oltre la disponibilità di dispositivi Android che non si è in genere connessi a GCM (Google Cloud messaggistica).

##<a name="prerequisites"></a>Prerequisiti

In questa esercitazione richiede le operazioni seguenti:

+ Android SDK (si presuppone che si utilizzerà Eclisse), che è possibile scaricare dal <a href="http://go.microsoft.com/fwlink/?LinkId=389797">sito Android</a>
+ [Servizi mobili Android SDK]
+ [SDK Android Push Baidu]

>[AZURE.NOTE] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).


##<a name="create-a-baidu-account"></a>Creare un account Baidu

Per utilizzare Baidu, è necessario disporre di un account Baidu. Se si è già presente, accedere al [portale di Baidu] e andare al passaggio successivo. In caso contrario, vedere le istruzioni sotto su come creare un account Baidu.  

1. Accedere al [portale di Baidu] e fare clic sul collegamento**登录**(**accesso**). Fare clic su**立即注册**per avviare il processo di registrazione dell'account.

    ![][1]

2. Immettere i dettagli necessari, telefono/posta elettronica indirizzo, password e verifica il codice e fare clic su **abbonamento**.

    ![][2]

3. Si riceverà un messaggio di posta elettronica all'indirizzo di posta elettronica che è stata immessa con un collegamento per attivare l'account Baidu.

    ![][3]

4. Accedere al proprio account di posta elettronica, aprire il messaggio di attivazione Baidu e fare clic sul collegamento di attivazione per attivare l'account Baidu.

    ![][4]

Dopo avere creato un account Baidu attivato, accedere al [portale di Baidu].

##<a name="register-as-a-baidu-developer"></a>Registrare gli sviluppatori Baidu

1. Quando ha effettuato l'accesso al [portale di Baidu], fare clic su**更多 >>** (**più**).

    ![][5]

2. Scorrere verso il basso nella sezione**站长与开发者服务 (Webmaster e servizi di sviluppo)** e fare clic su**百度开放云平台**(**Baidu aprire piattaforma cloud**).

    ![][6]

3. Nella pagina successiva, fare clic su**开发者服务**(**Developer Services**) nell'angolo superiore destro.

    ![][7]

4. Nella pagina successiva, fare clic su**注册开发者**(**Registrato sviluppatori**) dal menu nell'angolo superiore destro.

    ![][8]

5. Immettere il nome, descrizione e un numero di telefono cellulare per ricevere un messaggio di testo di verifica e quindi fare clic su**送验证码**(**Inviare il codice di verifica**). Si noti che per i numeri di telefono internazionali, è necessario racchiudere il codice paese tra parentesi. Ad esempio, per un numero di Stati Uniti, sarà **(1) 1234567890**.

    ![][9]

6. Viene visualizzato un messaggio di testo con un numero di verifica, come illustrato nell'esempio seguente:

    ![][10]

7. Immettere il numero di verifica del messaggio in**验证码**(**codice di conferma**).

8. Infine, completare la registrazione di sviluppo accettare il contratto di Baidu e facendo clic su**提交**(**Invia**). Verrà visualizzata la pagina seguente al termine dell'esecuzione della registrazione:

    ![][11]

##<a name="create-a-baidu-cloud-push-project"></a>Creare un progetto di push Baidu cloud

Quando si crea un progetto di push Baidu cloud, si riceve il proprio ID app API chiave e chiave privata.

1. Quando ha effettuato l'accesso al [portale di Baidu], fare clic su**更多 >>** (**più**).

    ![][5]

2. Scorrere verso il basso nella sezione**站长与开发者服务**(**Webmaster e i servizi di sviluppo**) e fare clic su**百度开放云平台**(**Baidu aprire piattaforma cloud**).

    ![][6]

3. Nella pagina successiva, fare clic su**开发者服务**(**Developer Services**) nell'angolo superiore destro.

    ![][7]

4. Nella pagina successiva, fare clic su**云推送**(**Cloud Push**) nella sezione**云服务**(**Servizi Cloud**).

    ![][12]

5. Dopo avere uno sviluppatore registrato, si vedrà**管理控制台**(**Management Console**) nel menu superiore. Fare clic su**开发者服务管理**(**gli sviluppatori di gestione dei servizi**).

    ![][13]

6. Nella pagina successiva, fare clic su**创建工程**(**Crea progetto**).

    ![][14]

7. Immettere un nome di applicazione e fare clic su**创建**(**Crea**).

    ![][15]

8. Alla creazione di un progetto di push Baidu cloud, verrà visualizzata una pagina con **AppID**, **API chiave**e **Chiave segreta**. Prendere nota della chiave dell'API e chiave segreta, che verrà utilizzato in un secondo momento.

    ![][16]

9. Configurare il progetto per le notifiche push facendo clic su**云推送**(**Cloud Push**) nel riquadro sinistro.

    ![][31]

10. Nella pagina successiva, fare clic sul pulsante**推送设置**(**Push impostazioni**).

    ![][32]  

11. Nella pagina configurazione, aggiungere il nome del pacchetto che si verranno utilizzando nel progetto Android nel campo**应用包名**(**pacchetto dell'applicazione**) e quindi fare clic su**保存设置**(**Salva**).  

    ![][33]

Verrà visualizzata la**保存成功!** (**salvato!**) messaggio.

##<a name="configure-your-notification-hub"></a>Configurare l'hub di notifica

1. Accedere al [Portale classica Azure]e quindi fare clic su **+ Nuovo** nella parte inferiore dello schermo.

2. Fare clic su **Servizi di App**, fare clic su **Bus di servizio**, fare clic su **Notifica Hub**e quindi fare clic su **Creazione rapida**.

3. Specificare un nome per l' **Hub di notifica**, selezionare **l'area geografica** e **Namespace** in cui verrà creato questo hub di notifica e quindi fare clic su **Crea un nuovo Hub di notifica**.  

    ![][17]

4. Fare clic sullo spazio dei nomi in cui è stato creato l'hub di notifica e quindi fare clic su **Hub di notifica** nella parte superiore.

    ![][18]

5. Selezionare l'hub di notifica che è stato creato e quindi fare clic su **Configura** dal menu superiore.

    ![][19]

6. Scorrere fino alla sezione **impostazioni di notifica baidu** e immettere la chiave dell'API e il segreto proveniente da console Baidu in precedenza per il progetto di push Baidu cloud. Fare clic su **Salva**.

    ![][20]

7. Fare clic sulla scheda **Dashboard** nella parte superiore per l'hub di notifica e quindi fare clic su **Visualizza stringa di connessione**.

    ![][21]

8. Prendere nota della **DefaultListenSharedAccessSignature** e **DefaultFullSharedAccessSignature** dalla finestra **informazioni sulla connessione di accesso** .

    ![][22]

##<a name="connect-your-app-to-the-notification-hub"></a>Connettere l'app all'hub di notifica

1. In ADT Eclisse, creare un nuovo progetto Android (**File** > **Nuovo** > **Android progetto di applicazione**).

    ![][23]

2. Immettere il **Nome dell'applicazione** e assicurarsi che la versione **Minima necessaria SDK** sia impostata su **API 16: Android 4.1**.

    ![][24]

3. Fare clic su **Avanti** e continuare a seguire la procedura guidata fino a quando non viene visualizzata la finestra **Crea attività** . Verificare che sia selezionata **Attività vuota** e infine selezionare **Fine** per creare una nuova applicazione Android.

    ![][25]

4. Assicurarsi che la **Destinazione Build progetto** sia impostata correttamente.

    ![][26]

5. Scaricare il file 0.4.jar di hub notifica dalla scheda **file** della [Notifica-hub-Android-SDK su Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4). Aggiungere il file nella cartella **librerie** del progetto Eclipse e aggiornare la cartella di *librerie* .

6. Scaricare e decomprimere [Baidu Push Android SDK], aprire la cartella di **librerie** e quindi copiare il file di vaso **x.y.z pushservice** e **armeabi** & **mips** cartelle nella cartella **librerie** dell'applicazione Android.

7. Aprire il file **AndroidManifest.xml** del progetto Android e aggiungere le autorizzazioni necessarie per SDK Baidu.

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.WRITE_SETTINGS" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
        <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8. Aggiungere la proprietà **android: nome** dell'elemento **dell'applicazione** in **AndroidManifest.xml**, sostituendo *yourprojectname* (ad esempio, **com.example.BaiduTest**). Assicurarsi che il nome del progetto corrisponda a quello che è stato configurato nella console di Baidu.

        <application android:name="yourprojectname.DemoApplication"

9. Aggiungere le seguenti operazioni all'interno dell'elemento dell'applicazione dopo il **. MainActivity** elemento attività, sostituendo *yourprojectname* (ad esempio, **com.example.BaiduTest**):

        <receiver android:name="yourprojectname.MyPushMessageReceiver">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
                <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
                <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
                <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>
        </receiver>

        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. Aggiungere una nuova classe denominata **ConfigurationSettings.java** al progetto.

    ![][28]

    ![][29]

10. Aggiungere il codice seguente:

        public class ConfigurationSettings {
                public static String API_KEY = "...";
                public static String NotificationHubName = "...";
                public static String NotificationHubConnectionString = "...";
            }

    Impostare il valore di **API_KEY** con recuperato dal Baidu cloud progetto in precedenza, **NotificationHubName** con il proprio nome hub notifica dal portale classica di Azure e **NotificationHubConnectionString** con DefaultListenSharedAccessSignature dal portale classica di Azure.

11. Aggiungere una nuova classe denominata **DemoApplication.java**e aggiungere il codice seguente:

        import com.baidu.frontia.FrontiaApplication;

        public class DemoApplication extends FrontiaApplication {
            @Override
            public void onCreate() {
                super.onCreate();
            }
        }

12. Aggiungere un'altra classe denominata **MyPushMessageReceiver.java**e aggiungere il codice seguente. Si tratta della classe che gestisce le notifiche push ricevuti dal server push Baidu.

        import java.util.List;
        import android.content.Context;
        import android.os.AsyncTask;
        import android.util.Log;
        import com.baidu.frontia.api.FrontiaPushMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
            /** TAG to Log */
            public static NotificationHub hub = null;
            public static String mChannelId, mUserId;
            public static final String TAG = MyPushMessageReceiver.class
                    .getSimpleName();

            @Override
            public void onBind(Context context, int errorCode, String appid,
                    String userId, String channelId, String requestId) {
                String responseString = "onBind errorCode=" + errorCode + " appid="
                        + appid + " userId=" + userId + " channelId=" + channelId
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
                mChannelId = channelId;
                mUserId = userId;

                try {
                 if (hub == null) {
                        hub = new NotificationHub(
                                ConfigurationSettings.NotificationHubName,
                                ConfigurationSettings.NotificationHubConnectionString,
                                context);
                        Log.i(TAG, "Notification hub initialized");
                    }
                } catch (Exception e) {
                   Log.e(TAG, e.getMessage());
                }

                registerWithNotificationHubs();
            }

            private void registerWithNotificationHubs() {
               new AsyncTask<Void, Void, Void>() {
                  @Override
                  protected Void doInBackground(Void... params) {
                     try {
                         hub.registerBaidu(mUserId, mChannelId);
                         Log.i(TAG, "Registered with Notification Hub - '"
                                + ConfigurationSettings.NotificationHubName + "'"
                                + " with UserId - '"
                                + mUserId + "' and Channel Id - '"
                                + mChannelId + "'");
                     } catch (Exception e) {
                         Log.e(TAG, e.getMessage());
                     }
                     return null;
                 }
               }.execute(null, null, null);
            }

            @Override
            public void onSetTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onSetTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onDelTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onDelTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onListTags(Context context, int errorCode, List<String> tags,
                    String requestId) {
                String responseString = "onListTags errorCode=" + errorCode + " tags="
                        + tags;
                Log.d(TAG, responseString);
            }

            @Override
            public void onUnbind(Context context, int errorCode, String requestId) {
                String responseString = "onUnbind errorCode=" + errorCode
                        + " requestId = " + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onNotificationClicked(Context context, String title,
                    String description, String customContentString) {
                String notifyString = "title=\"" + title + "\" description=\""
                        + description + "\" customContent=" + customContentString;
                Log.d(TAG, notifyString);
            }

            @Override
            public void onMessage(Context context, String message,
                    String customContentString) {
                String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
                Log.d(TAG, messageString);
            }
        }

13. Aprire **MainActivity.java**e aggiungere il metodo **onCreate** quanto segue:

            PushManager.startWork(getApplicationContext(),
                    PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

14. Aprire le seguenti istruzioni di importazione nella parte superiore:

            import com.baidu.android.pushservice.PushConstants;
            import com.baidu.android.pushservice.PushManager;

##<a name="send-notifications-to-your-app"></a>Invio di notifiche applicazione in uso.


È possibile verificare rapidamente ricezione delle notifiche nell'app tramite l'invio di notifiche nel [Portale di Azure](https://portal.azure.com/) utilizzo del pulsante **Test inviare** hub notifica, come illustrato nell'immagine seguente.

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

Notifiche push normalmente vengono inviate in un servizio di back-end come servizi mobili o ASP.NET utilizzando una raccolta compatibile. È anche possibile utilizzare API REST direttamente a inviare messaggi di notifica se una raccolta non è disponibile per il back-end.

In questa esercitazione verrà privilegiare la semplicità e appena dimostrare-test dell'applicazione client mediante l'invio di notifiche tramite .NET SDK per hub di notifica in un'applicazione console invece di un servizio di back-end. È consigliabile l'esercitazione di [Utilizzare gli hub di notifica per le notifiche push agli utenti](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) il passaggio successivo per l'invio di notifiche da un back-end ASP.NET. Tuttavia, è possono utilizzare approcci seguenti per l'invio di notifiche:

* **Interfaccia REST**: È possibile supportare notifica su qualsiasi piattaforma back-end tramite l' [interfaccia REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **Microsoft Azure notifica hub .NET SDK**: In the Nuget pacchetto Manager per Visual Studio, eseguire [Microsoft.Azure.NotificationHubs pacchetto di installazione](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node** : [come usare gli hub di notifica da Node](notification-hubs-nodejs-push-notification-tutorial.md).

* **App mobile**: per informazioni su come inviare notifiche da un back-end Azure App servizio Mobile App che è integrato con gli hub di notifica, vedere [aggiungere le notifiche push per l'app per dispositivi mobili](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).

* **Linguaggio / PHP**: per informazioni su come inviare le notifiche tramite le API REST, vedere "Come usare gli hub di notifica da Java/PHP" ([linguaggio](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

##<a name="optional-send-notifications-from-a-net-console-app"></a>(Facoltativo) Inviare notifiche da un'app per console .NET.

In questa sezione, questo webinar mostra un messaggio di notifica un'app di console .NET.

1. Creare una nuova applicazione console c#:

    ![][30]

2. Nella finestra Gestione pacchetti Console set **predefinito di project** per il nuovo progetto applicazione console e quindi nella finestra della console, eseguire il comando seguente:

        Install-Package Microsoft.Azure.NotificationHubs

    Aggiungere un riferimento di Azure notifica hub SDK utilizzando il <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacchetto Microsoft.Azure.Notification hub NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. Aprire il file **Program.cs** e aggiungere la seguente istruzione:

        using Microsoft.Azure.NotificationHubs;

4. Nel `Program` per la classe, aggiungere il metodo seguente e sostituire *DefaultFullSharedAccessSignatureSASConnectionString* e *NotificationHubName* con i valori che si dispone.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
            string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
            var result = await hub.SendBaiduNativeNotificationAsync(message);
        }

5. Aggiungere le righe seguenti nel metodo **Main** :

         SendNotificationAsync();
         Console.ReadLine();

##<a name="test-your-app"></a>Verificare l'app

Per testare l'app con un telefono effettivo, è sufficiente connettere il telefono nel computer in uso tramite un cavo USB. Consente di caricare l'app sul telefono, allegato.

Per testare l'app con l'emulatore, sulla barra degli strumenti Eclisse superiore, fare clic su **Esegui**e quindi selezionare l'app. Si avvia l'emulatore e quindi carica e viene eseguita l'app.

L'app recupera "ID utente" e 'channelId' dal servizio di notifica Baidu Push e registra con l'hub di notifica.

Per inviare una notifica di test che è possibile utilizzare la scheda debug del portale classica di Azure. Se è stata creata l'applicazione console .NET per Visual Studio, è sufficiente premere F5 in Visual Studio per eseguire l'applicazione. L'applicazione invierà una notifica che verrà visualizzato nell'area di notifica superiore del dispositivo o emulatore.


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[Servizi mobili Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[SDK Android Push Baidu]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Portale classica Azure]: https://manage.windowsazure.com/
[Portale Baidu]: http://www.baidu.com/
