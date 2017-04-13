<properties
    pageTitle="Opzioni avanzate di creazione di report per Azure Mobile coinvolgimento Android SDK"
    description="Viene descritto come eseguire la segnalazione avanzate per acquisire analitica per Azure Mobile coinvolgimento Android SDK"
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
    ms.date="08/10/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-reporting-with-engagement-on-android"></a>Report avanzati con coinvolgimento in Android

> [AZURE.SELECTOR]
- [Windows universale](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

In questo argomento descrive scenari di creazione di report aggiuntivi nell'applicazione Android. È possibile applicare queste opzioni per l'applicazione creata in esercitazione [Introduzione](mobile-engagement-android-get-started.md) .

## <a name="prerequisites"></a>Prerequisiti

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

L'esercitazione è completata è stata deliberatamente diretto e semplice, ma sono disponibili le opzioni avanzate è possibile scegliere.

## <a name="modifying-your-activity-classes"></a>Modificare il `Activity` classi

Nell' [esercitazione Introduzione](mobile-engagement-android-get-started.md), tutto era stato per rendere il `*Activity` sottoclassi ereditano corrispondente `Engagement*Activity` classi. Ad esempio, se l'attività legacy esteso `ListActivity`, si consentirebbe estendere `EngagementListActivity`.

> [AZURE.IMPORTANT] Quando si usa `EngagementListActivity` o `EngagementExpandableListActivity`, verificare che tutte le chiamate a `requestWindowFeature(...);` viene effettuato prima la chiamata a `super.onCreate(...);`, in caso contrario si verifica un arresto anomalo.

È possibile trovare queste classi di `src` cartella e possibile copiarli nel progetto. Le classi sono inoltre **JavaDoc**.

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Alternare metodo: chiamare `startActivity()` e `endActivity()` manualmente

Se non è possibile o non si desidera overload il `Activity` classi, è invece possibile iniziare e finire l'attività chiamando il `EngagementAgent`di metodi direttamente.

> [AZURE.IMPORTANT] Android SDK non chiama mai il `endActivity()` metodo, anche quando l'applicazione viene chiusa (Android, le applicazioni non vengono mai chiuse). Pertanto è *altamente* consigliati per chiamare il `startActivity()` metodo nel `onResume` callback di *tutte* le attività e la `endActivity()` metodo nel `onPause()` callback di *tutte* le attività. Questo è l'unico modo per assicurarsi che non perdite delle sessioni. Se viene perso a una sessione, il servizio coinvolgimento mai effettua la disconnessione da back-end coinvolgimento (poiché il servizio rimane collegato, purché sia in attesa di una sessione).

Ecco un esempio:

    public class MyActivity extends Some3rdPartyActivity
    {
      @Override
      protected void onResume()
      {
        super.onResume();
        String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
        EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
      }

      @Override
      protected void onPause()
      {
        super.onPause();
        EngagementAgent.getInstance(this).endActivity();
      }
    }

In questo esempio è simile al `EngagementActivity` classe e le relative varianti, il cui codice sorgente è fornito nel `src` cartella.

## <a name="using-applicationoncreate"></a>Utilizzo Application.onCreate()

Il codice inserire nel `Application.onCreate()` e in un'altra applicazione callback viene eseguito per i processi tutti dell'applicazione, incluso il servizio di impegno. Potrebbe avere effetti indesiderati, ad esempio le allocazioni di memoria non necessari e thread nel processo di impegno, o ricevitori trasmissione duplicati o servizi.

Se esegue l'override `Application.onCreate()`, è consigliabile aggiungere il frammento di codice seguenti all'inizio del `Application.onCreate()` funzione:

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

È possibile eseguire la stessa operazione per `Application.onTerminate()`, `Application.onLowMemory()`, e `Application.onConfigurationChanged(...)`.

È anche possibile estendere `EngagementApplication` invece di estensione `Application`: il callback `Application.onCreate()` esegue il controllo di processo e le chiamate perse `Application.onApplicationProcessCreate()` solo se il processo corrente non è quello che include il servizio di impegno, applicano le stesse regole per le altre richiamate.

## <a name="tags-in-the-androidmanifestxml-file"></a>Tag nel file AndroidManifest.xml

Nel numero di servizio nel file AndroidManifest.xml il `android:label` attributo consente di scegliere il nome del servizio di impegno come viene visualizzato agli utenti finali nella schermata "Che esegue servizi" del proprio telefono. È consigliabile impostare questo attributo `"<Your application name>Service"` (ad esempio `"AcmeFunGameService"`).

Specificare la `android:process` attributo assicura che il servizio di impegno viene eseguito nel proprio processo (impegno è in esecuzione lo stesso processo come l'applicazione effettua il thread principali/UI potenzialmente tempi).

## <a name="building-with-proguard"></a>Compilazione con ProGuard

Se si creazione pacchetto dell'applicazione con ProGuard, è necessario mantenere alcune classi. È possibile utilizzare il frammento di codice di configurazione seguenti:

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
    }
