<properties
    pageTitle="Integrazione di Android SDK Azure impegno per dispositivi mobili"
    description="Ultimi aggiornamenti e le procedure per Android SDK per Azure Mobile coinvolgimento"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-gcm-with-mobile-engagement"></a>Come integrare GCM con coinvolgimento per dispositivi mobili

> [AZURE.IMPORTANT] È necessario seguire la procedura di integrazione descritta in come integrare impegno Android documento prima di iniziare questa Guida.
>
> In questo documento è utile solo se già integrato il modulo portata e si intende push Google Play dispositivi. Per integrare campagne portata nell'applicazione, leggere prima di tutto come integrare impegno raggiungere in Android.

##<a name="introduction"></a>Introduzione

Integrazione GCM consente di inserire l'applicazione.

Payload GCM inserito a SDK sempre contenere il `azme` chiave nell'oggetto dati. In questo modo se si usa GCM per altri scopi nell'applicazione, è possibile filtrare in base a tale tasto push.

> [AZURE.IMPORTANT] Solo i dispositivi che eseguono Android 2.2 o superiore della finestra fare con Google Play installati e verificano Google connessione sfondo abilitata possibile inserite utilizzando GCM; Tuttavia, è possibile integrare questo codice in modo sicuro su dispositivi non supportati (utilizza solo intenti).

##<a name="create-a-google-cloud-messaging-project-with-api-key"></a>Creare un progetto Google Cloud messaggistica con chiave dell'API

[AZURE.INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

##<a name="sdk-integration"></a>Integrazione di SDK

### <a name="managing-device-registrations"></a>Gestione registrazioni di dispositivo

Ogni dispositivo necessario inviare un comando di registrazione ai server Google, altrimenti non può essere raggiunto.

Un dispositivo può anche annullare la registrazione delle notifiche GCM (il dispositivo è automaticamente annullato se l'applicazione viene disinstallata).

Se non si usa [Google Play SDK] o già si non invia lo scopo di registrazione, è possibile rendere coinvolgimento registrare automaticamente il dispositivo dell'utente.

A tale scopo, aggiungere le operazioni seguenti per il `AndroidManifest.xml` file all'interno di `<application/>` tag:

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Comunicare identificativo al servizio di impegno Push e ricevere notifiche

Per comunicare l'id di registrazione del dispositivo per il servizio di impegno Push e ricevere le notifiche, aggiungere le operazioni seguenti per il `AndroidManifest.xml` file all'interno di `<application/>` tag (anche se si gestiscono personalmente registrazioni dispositivo):

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

Assicurarsi che si dispone delle autorizzazioni seguenti `AndroidManifest.xml` (dopo il `</application>` tag).

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##<a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Concedere l'accesso Mobile coinvolgimento alla chiave dell'API GCM

Seguire [questa guida](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key) per concedere l'accesso Mobile coinvolgimento alla chiave dell'API GCM.

[Google Play SDK]:https://developers.google.com/cloud-messaging/android/start
