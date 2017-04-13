<properties
    pageTitle="Integrazione di Android SDK Azure impegno per dispositivi mobili"
    description="Ultimi aggiornamenti e le procedure per Android SDK per Azure Mobile coinvolgimento"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />


#<a name="how-to-integrate-adm-with-engagement"></a>Come integrare i ADM con coinvolgimento

> [AZURE.IMPORTANT] È necessario seguire la procedura di integrazione descritta in come integrare impegno Android documento prima di iniziare questa Guida.
>
> In questo documento è utile solo se già integrato il modulo portata e si intende push dispositivi di Amazon. Per integrare campagne portata nell'applicazione, leggere prima di tutto come integrare impegno raggiungere in Android.

##<a name="introduction"></a>Introduzione

Integrazione ADM consente all'applicazione di essere inserito quando la destinazione dispositivi Android Amazon.

Payload ADM inserito a SDK sempre contenere il `azme` chiave nell'oggetto dati. In questo modo se si usa ADM altro scopo nell'applicazione, è possibile filtrare in base a tale tasto push.

> [AZURE.IMPORTANT] Solo Amazon Kindle dispositivi che eseguono Android alla 4.0.3 o versioni successive sono supportati da un dispositivo di messaggistica di Amazon; Tuttavia, è possibile integrare il codice in modo sicuro negli altri dispositivi.

##<a name="sign-up-to-adm"></a>Iscriversi a ADM

Se non è già fatto, è necessario abilitare ADM nel proprio account di Amazon.

La procedura descritta in dettaglio in: [<https://developer.amazon.com/sdk/adm/credentials.html>].

Dopo avere completato la procedura, viene visualizzato:

-   OAuth credenziali (un ID Client e un segreto del Client) per l'impegno per poter inserire i dispositivi.
-   Una chiave dell'API che deve essere integrato con l'applicazione.

##<a name="sdk-integration"></a>Integrazione di SDK

### <a name="managing-device-registrations"></a>Gestione registrazioni di dispositivo

Ogni dispositivo necessario inviare un comando di registrazione ai server ADM, altrimenti non può essere raggiunto.

Se si usa già [libreria client ADM]e già [ADM integrata] è possibile accedere direttamente al android sdk-adm ricevere.

Se non è stata integrata ancora ADM, impegno è un modo più semplice per abilitare nell'applicazione:

Modificare il `AndroidManifest.xml` file:

-   Aggiungere lo spazio di Amazon, il file deve iniziare alla seguente:

        <?xml version="1.0" encoding="utf-8"?>
        <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                  xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   All'interno di `<application/>` tag, aggiungere in questa sezione:

        <amazon:enable-feature
           android:name="com.amazon.device.messaging"
           android:required="false"/>

        <meta-data android:name="engagement:adm:register" android:value="true" />

-   Dopo aver aggiunto il contrassegno di amazon, potrebbe essere un errore di generazione se la destinazione Build progetto è inferiore a 2.1 Android. È necessario usare una destinazione di generazione **Android 2.1 +** (non, è comunque possibile impostare un `minSdkVersion` impostato su 4).
-   Integrare la chiave dell'API ADM come una risorsa da seguente [questa procedura].

Seguire le istruzioni della sezione successive.

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Comunicare identificativo al servizio di impegno Push e ricevere notifiche

Per comunicare l'id di registrazione del dispositivo per il servizio di impegno Push e ricevere le notifiche, aggiungere le operazioni seguenti per il `AndroidManifest.xml` file all'interno di `<application/>` tag (anche se si usa ADM senza coinvolgimento):

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

Assicurarsi che si dispone delle autorizzazioni seguenti `AndroidManifest.xml` (prima di `</application>` tag).

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##<a name="grant-engagement-oauth-credentials"></a>Concedere l'impegno OAuth credenziali

Inviare le credenziali OAuth (ID Client e segreto del Client) nel portale di impegno.

[< https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[Libreria client ADM]:https://developer.amazon.com/sdk/adm/setup.html
[ADM integrata]:https://developer.amazon.com/sdk/adm/integrating-app.html
[Questa procedura]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
