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


#<a name="upgrade-procedures"></a>Procedure di aggiornamento

Se è già stata integrata una versione precedente di Windows SDK nell'applicazione, è necessario tenere presente quanto segue durante l'aggiornamento di SDK.

È necessario eseguire alcune procedure notato diverse versioni di SDK. Se, ad esempio la migrazione da 1.4.0 a 1.6.0 che è necessario innanzitutto seguire la procedura "da 1.4.0 a 1.5.0" quindi la procedura "da 1.5.0 a 1.6.0".

Qualunque sia la versione l'aggiornamento da, è necessario sostituire il `mobile-engagement-VERSION.jar` con quello nuovo.

##<a name="from-420-to-421"></a>Da 4.2.0 a 4.2.1

Questo passaggio può essere eseguito in realtà in una versione di SDK, è una maggiore protezione quando si integra portata attività.

A questo punto è necessario aggiungere `exported="false"` a tutte le attività di mano.

Portata attività dovrebbe essere simile `AndroidManifest.xml`:

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

##<a name="from-400-to-410"></a>Da 4.0.0 a 4.1.0

Il SDK adesso quadratino nuovo modello delle autorizzazioni da M Android.

Se si utilizzano caratteristiche percorso o notifiche ampio, leggere [questa sezione](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

Oltre il nuovo modello di autorizzazione, è ora supportato configurazione percorso caratteristiche in fase di esecuzione.
Siamo ancora compatibile con i parametri manifesto per posizione, ma è obsoleta. Per usare la configurazione di runtime, rimuovere le sezioni seguenti dal ``AndroidManifest.xml``:

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

e leggere [questa procedura aggiornata](mobile-engagement-android-integrate-engagement.md#location-reporting) per utilizzare invece di configurazione di runtime.

##<a name="from-300-to-400"></a>Da 3.0.0 a 4.0.0

### <a name="native-push"></a>Push nativo

Push nativi (GCM/ADM) viene ora usato anche per le notifiche di app in modo che è necessario configurare le credenziali push nativi per qualsiasi tipo di campagna push.

Se non è già fatto, seguire [questa procedura](mobile-engagement-android-integrate-engagement-reach.md#native-push).

### <a name="androidmanifestxml"></a>AndroidManifest.xml

Integrazione di raggiungere è stato modificato ``AndroidManifest.xml``.

Sostituire seguente:

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

Base

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

È eventualmente caricamento schermo ora disponibile quando si fa clic su un annuncio (con testo/web contenuto) o un sondaggio.
È necessario aggiungere per tali campagne di lavorare in 4.0.0:

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### <a name="resources"></a>Risorse

Incorporare il nuovo `res/layout/engagement_loading.xml` file nel progetto.

##<a name="from-240-to-300"></a>Da 2.4.0 a 3.0.0

Di seguito viene descritto come eseguire la migrazione di un'integrazione SDK dal servizio di Capptain fornito dal Capptain SAS in un'app con tecnologia Azure Mobile coinvolgimento. Se si esegue la migrazione da una versione precedente, consultare il sito web di Capptain per eseguire la migrazione a 2.4.0 prima di tutto e quindi applicare la procedura seguente.

>[AZURE.IMPORTANT] Capptain e coinvolgimento Mobile non sono gli stessi servizi e la procedura indicata di seguito viene evidenziato solo come eseguire la migrazione dell'applicazione client. Migrazione SDK nell'app verrà non eseguire la migrazione dei dati dal server Capptain ai server coinvolgimento Mobile.

### <a name="jar-file"></a>File VASO

Sostituire `capptain.jar` da `mobile-engagement-VERSION.jar` nel `libs` cartella.

### <a name="resource-files"></a>File di risorse

Ogni file di risorse fornite (preceduta da `capptain_`) deve essere sostituiti da quelli nuovi (con prefisso `engagement_`).

Se i file personalizzati, è necessario applicare nuovamente la personalizzazione in nuovi file, **tutti gli identificatori nei file di risorse sono inoltre stati rinominati**.

### <a name="application-id"></a>ID applicazione

A questo punto coinvolgimento utilizza una stringa di connessione per configurare gli identificatori SDK, ad esempio l'identificatore dell'applicazione.

È necessario usare `EngagementAgent.init` metodo nell'attività avvio alla seguente:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

La stringa di connessione per l'applicazione viene visualizzata nel portale di Azure.

Rimuovere tutte le chiamate a `CapptainAgent.configure` come `EngagementAgent.init` sostituisce tale metodo.

Il `appId` non è più possibile configurare tramite `AndroidManifest.xml`.

In questa sezione da rimuovere il `AndroidManifest.xml` se si dispone:

            <meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### <a name="java-api"></a>API Java

Ogni chiamata a qualsiasi classe Java di Windows SDK è necessario rinominare; ad esempio `CapptainAgent.getInstance(this)` deve essere rinominato `EngagementAgent.getInstance(this)`, `extends CapptainActivity` deve essere rinominato `extends EngagementActivity` e così via...

Se sono stati integrati con i file delle preferenze agente predefinita, il nome di file predefinito è ora `engagement.agent` e la chiave è `engagement:agent`.

Quando si creano annunci web, il raccoglitore Javascript è ora `engagementReachContent`.

### <a name="androidmanifestxml"></a>AndroidManifest.xml

Molte modifiche apportate sono e il servizio non è stato condiviso più numerose ricevitori non sono più esportabili.

Dichiarazione del servizio è ora più semplice. rimuovere il filtro intento e tutti i metadati all'interno e aggiungere `exportable=false`.

Oltre a tutti gli elementi rinominato per l'utilizzo di impegno.

L'ora aspetto:

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

Quando si desidera abilitare log di test, i metadati a questo punto è stato spostato tag dell'applicazione ed sono stato rinominato:

            <application>
            
              <meta-data android:name="engagement:log:test" android:value="true" />
            
              <service/>
            
            </application>

Tutti gli altri metadati dati appena sono stati rinominati, ecco l'elenco completo (di Rinomina corso solo quelle utilizzate):

            <meta-data
              android:name="engagement:reportCrash"
              android:value="true"/>
            <meta-data
              android:name="engagement:sessionTimeout"
              android:value="10000"/>
            <meta-data
              android:name="engagement:burstThreshold"
              android:value="0"/>
            <meta-data
              android:name="engagement:connection:delay"
              android:value="0"/>
            <meta-data
              android:name="engagement:locationReport:lazyArea"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:background"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:fine"
              android:value="false"/>
            <meta-data
              android:name="engagement:agent:settings:name"
              android:value="engagement.agent"/>
            <meta-data
              android:name="engagement:agent:settings:mode"
              android:value="0"/>
            <meta-data
              android:name="engagement:gcm:sender"
              android:value="<YOUR_PROJECT_NUMBER>\n"/>
            <meta-data
              android:name="engagement:adm:register"
              android:value="true"/>
            <meta-data
              android:name="engagement:reach:notification:icon"
              android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>
            
            <activity android:name="SomeActivityWithoutReachOverlay">
              <meta-data
                android:name="engagement:notification:overlay"
                android:value="false"/>
            </activity>

Google Play e SmartAd verifica è stato rimosso da SDK è sufficiente rimuovere questo senza sostituzione:

            <meta-data 
                android:name="capptain:track:installReferrerForwardList"
                android:value="com.class1,com.class2"/>
            <meta-data
                android:name="capptain:track:adservers"
                android:value="smartad" />

Le attività di portata sono ora dichiarate come segue:

            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/html"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>
            
Se si dispone di attività portata personalizzate, è sufficiente modificare le azioni intento per corrisponde a `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` o `com.microsoft.azure.engagement.reach.intent.action.POLL`.

I destinatari trasmissione sono stati rinominati, oltre a questo punto si aggiunge `exported=false`. Ecco l'elenco completo di essi con la nuova specifica (di Rinomina corso solo quelle utilizzate):

            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
              android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
              android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
              android:permission="com.amazon.device.messaging.permission.SEND">
              <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
                <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>
            
            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
              </intent-filter>
            </receiver>

Verifica ricevitore è stata rimossa, pertanto è necessario rimuovere questa sezione:

          <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
            <intent-filter>
              <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
              <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
            </intent-filter>
          </receiver>

Si noti che la dichiarazione di implementazione del ricevitore trasmissione **EngagementMessageReceiver** vengono modificati i `AndroidManifest.xml`. Ciò avviene perché l'API per l'invio e rimuovere i messaggi di azienda non autorizzati da non autorizzata entità azienda e l'API per inviare e ricevere messaggi tra i dispositivi sono state rimosse. In questo modo, è necessario anche eliminare l'implementazione di **EngagementMessageReceiver** callback indicati di seguito:

            protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

e

            protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

eliminare tutte le chiamate in **EngagementAgent** per:

            sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

e

            sendXMPPMessage(android.os.Bundle msg)

### <a name="proguard"></a>Proguard

Configurazione proguard può essere influenzata dalla attribuzione dei marchi usando e le regole vengono ora come:

            -dontwarn android.**
            -keep class android.support.v4.** { *; }
            
            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
              <methods>;
            }
 
