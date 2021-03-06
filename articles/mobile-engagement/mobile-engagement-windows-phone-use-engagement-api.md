<properties 
    pageTitle="Come usare il coinvolgimento API nel Windows Phone Silverlight" 
    description="Come usare il coinvolgimento API nel Windows Phone Silverlight"    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede"
    editor="" /> 

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="how-to-use-the-engagement-api-on-windows-phone-silverlight"></a>Come usare il coinvolgimento API nel Windows Phone Silverlight

In questo documento è un componente aggiuntivo per il documento [come integrare impegno Mobile nell'app Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md). Fornisce in antero-posteriore informazioni dettagliate su come utilizzare l'API di impegno per segnalare le statistiche dell'applicazione.

Se si vuole solo impegno per segnalare l'applicazione sessioni, attività, anomalo e informazioni tecniche, quindi il modo più semplice consiste nell'apportare tutte le `PhoneApplicationPage` classi secondari ereditano dalla `EngagementPage` classe.

Se si desidera eseguire operazioni più complesse, ad esempio se è necessario inviare una segnalazione eventi specifici dell'applicazione, gli errori e processi, o se si dispone segnalare le attività dell'applicazione in modo diverso rispetto a quella implementata nel `EngagementPage` classi, è necessario utilizzare l'API di impegno.

L'API di impegno viene fornita tramite il `EngagementAgent` classe. È possibile accedere a tali metodi tramite `EngagementAgent.Instance`.

Anche se il modulo agente non inizializzato, ogni chiamata all'API è posticipato e verrà eseguito nuovamente quando l'agente è disponibile.

##<a name="engagement-concepts"></a>Concetti di impegno

Dalle parti seguenti consentono di migliorare i concetti di impegno Mobile per la piattaforma Windows Phone.

### <a name="session-and-activity"></a>`Session`e`Activity`

Un' *attività* è generalmente associato a una pagina dell'applicazione, vale a dire l' *attività* viene avviato quando viene visualizzata la pagina di si arresta quando la pagina è chiuso: si tratta le maiuscole/minuscole quando SDK coinvolgimento è integrato con la `EngagementPage` classe.

Ma *attività* anche possibile controllare manualmente tramite l'API di impegno. In questo modo per dividere una determinata pagina in più parti sub per ottenere ulteriori informazioni sull'utilizzo di questa pagina (ad esempio per noti con quale frequenza e per quanto tempo all'interno di questa pagina vengono utilizzate le finestre di dialogo).

##<a name="reporting-activities"></a>Attività di Reporting

### <a name="user-starts-a-new-activity"></a>Si inizia una nuova attività

#### <a name="reference"></a>Guida di riferimento

            void StartActivity(string name, Dictionary<object, object> extras = null)

È necessario chiamare `StartActivity()` ogni volta che le modifiche di attività utente. La prima chiamata a questa funzione avvia una nuova sessione utente.

> [AZURE.IMPORTANT] SDK automaticamente il metodo fine attività quando l'applicazione viene chiusa. In questo modo, si consiglia di chiamare il metodo StartActivity ogni volta che l'attività dell'utente la modifica e su mai chiamare il metodo di fine attività, poiché questo metodo impone la sessione corrente a interromperlo.

#### <a name="example"></a>Esempio

            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>Utente termina la propria attività corrente

#### <a name="reference"></a>Guida di riferimento

            void EndActivity()

È necessario chiamare `EndActivity()` almeno una volta quando l'utente termina la propria attività ultima. Informa SDK impegno che l'utente si trova attualmente inattivo e che è necessario chiudere una volta il timeout di sessione la sessione utente scadrà (se si chiama `StartActivity()` prima della scadenza il timeout di sessione, è sufficiente continua la sessione).

#### <a name="example"></a>Esempio

            EngagementAgent.Instance.EndActivity();

##<a name="reporting-jobs"></a>Processi di report

### <a name="start-a-job"></a>Avviare un processo

#### <a name="reference"></a>Guida di riferimento

            void StartJob(string name, Dictionary<object, object> extras = null)

È possibile utilizzare il processo per tenere traccia di alcune attività per un periodo di tempo.

#### <a name="example"></a>Esempio

            // An upload begins...
            
            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");
            
            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>Terminare un processo

#### <a name="reference"></a>Guida di riferimento

            void EndJob(string name)

Come un'attività verrà registrata un processo terminata, è necessario chiamare il metodo EndJob per questo processo, fornendo il nome del processo.

#### <a name="example"></a>Esempio

            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends
            
            EngagementAgent.Instance.EndJob("uploadData");

##<a name="reporting-events"></a>Segnalazione di eventi

Esiste tre tipi di eventi:

-   Eventi autonomo
-   Eventi della sessione
-   Eventi di processo

### <a name="standalone-events"></a>Eventi autonomo

#### <a name="reference"></a>Guida di riferimento

            void SendEvent(string name, Dictionary<object, object> extras = null)

Eventi autonoma possono verificarsi all'esterno del contesto di una sessione.

#### <a name="example"></a>Esempio

            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>Eventi della sessione

#### <a name="reference"></a>Guida di riferimento

            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Gli eventi della sessione vengono in genere utilizzati per segnalare le azioni eseguite da un utente durante la sessione.

#### <a name="example"></a>Esempio

**Senza dati:**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");
            
            // or
            
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Con i dati:**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>Eventi di processo

#### <a name="reference"></a>Guida di riferimento

            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Processo eventi vengono in genere utilizzati per segnalare le azioni eseguite da un utente durante un processo.

#### <a name="example"></a>Esempio

            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##<a name="reporting-errors"></a>Segnalazione degli errori

Esiste tre tipi di errori:

-   Errori autonomo
-   Errori sessione
-   Errori di processo

### <a name="standalone-errors"></a>Errori autonomo

#### <a name="reference"></a>Guida di riferimento

            void SendError(string name, Dictionary<object, object> extras = null)

Contrasto con errori di sessione, possono verificarsi errori autonoma all'esterno del contesto di una sessione.

#### <a name="example"></a>Esempio

            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>Errori sessione

#### <a name="reference"></a>Guida di riferimento

            void SendSessionError(string name, Dictionary<object, object> extras = null)

Errori sessione vengono in genere utilizzati per inviare una segnalazione errori impatto l'utente durante la sessione.

#### <a name="example"></a>Esempio

            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Errori di processo

#### <a name="reference"></a>Guida di riferimento

            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Errori possono essere correlati a un processo in esecuzione anziché correlate alla sessione utente corrente.

#### <a name="example"></a>Esempio

            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##<a name="reporting-crashes"></a>Creazione di report anomalo

L'agente fornisce due metodi di occuparsi di anomalo.

### <a name="send-an-exception"></a>Inviare un'eccezione

#### <a name="reference"></a>Guida di riferimento

            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>Esempio

È possibile inviare un'eccezione in qualsiasi momento chiamando:

            EngagementAgent.Instance.SendCrash(aCatchedException);

È anche possibile utilizzare un parametro facoltativo per terminare la sessione di impegno nello stesso momento rispetto all'invio di un arresto anomalo. A tale scopo, chiamare:

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

In questo caso, la sessione e processi verranno chiusa solo dopo l'invio di un arresto anomalo.

### <a name="send-an-unhandled-exception"></a>Inviare un'eccezione non gestita

#### <a name="reference"></a>Guida di riferimento

            void SendCrash(ApplicationUnhandledExceptionEventArgs e)

Coinvolgimento vengono forniti anche un metodo per inviare le eccezioni non gestite. Questa operazione è particolarmente utile se utilizzato all'interno del gestore di evento UnhandledException silverlight.

Ciò metodo **sempre** terminare la sessione di impegno e processi dopo essere stato chiamato.

#### <a name="example"></a>Esempio

È possibile utilizzare per implementare un proprio gestore UnhandledException (in particolare se è stata disattivata l'arresto anomalo automatica delle funzionalità di impegno report). Ad esempio, nel `Application_UnhandledException` metodo del `App.xaml.cs` file:

            // In your App.xaml.cs file
            
            // Code to execute on Unhandled Exceptions
            private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
            {
              // your own code
            
              EngagementAgent.Instance.SendCrash(e);
            }

##<a name="onactivated"></a>OnActivated

### <a name="reference"></a>Guida di riferimento

            void OnActivated(ActivatedEventArgs e)

Quando l'utente si sposta in avanti, lontano da un'applicazione di dopo l'evento Deactivated, il sistema operativo tenterà di inserire l'applicazione in uno stato inattivo. L'applicazione sarà eliminazione definitiva. In questo processo viene interrotta un'applicazione ma viene mantenuti alcuni dati relativi allo stato dell'applicazione e le singole pagine all'interno dell'applicazione.

È necessario inserire `EngagementAgent.Instance.OnActivated(e)` nel `Application_Activated` metodo dal file App.xaml.cs per reimpostare l'agente di partecipazione attiva quando l'applicazione è stato eliminato definitivamente.

### <a name="example"></a>Esempio

            // Inside your App.xaml.cs file
            
            // Code to execute when the application is activated (brought to foreground)
            // This code will not execute when the application is first launched
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
              EngagementAgent.Instance.OnActivated(e);
            }

##<a name="device-id"></a>Id dispositivo

            String GetDeviceId()

È possibile ottenere l'id di dispositivo coinvolgimento chiamando questo metodo.

##<a name="extras-parameters"></a>Parametri extra

Dati non autorizzati possono essere collegati a un evento, un messaggio di errore, un'attività o un processo. Questi dati possono essere strutturate usando un dizionario. Chiavi e i valori possono essere di qualsiasi tipo.

Dati extra vengono serializzati in modo che se si desidera inserire il proprio tipo in extra è necessario aggiungere un contratto per questo tipo di dati.

### <a name="example"></a>Esempio

Verrà creata una nuova classe "Utente".

            using System.Runtime.Serialization;
            
            namespace Engagement.Agent
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }
            
                // Properties
            
                [DataMember]
                public int Age
                {
                  get;
                  set;
                }
            
                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

Quindi, verrà aggiunto un `Person` istanza a un ulteriore.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);
            
            EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING] Se si inseriscono altri tipi di oggetti, assicurarsi che il metodo Value viene implementato per restituire una stringa leggibile.

### <a name="limits"></a>Limiti

#### <a name="keys"></a>Chiavi

Ogni chiave dell'oggetto deve corrispondere l'espressione seguente:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Significa che tasti devono iniziare con almeno una lettera, seguita da lettere, cifre o caratteri di sottolineatura (\_).

#### <a name="size"></a>Dimensioni

Funzionalità aggiuntive sono limitate a **1024** caratteri per la chiamata.

##<a name="reporting-application-information"></a>Informazioni sull'applicazione di creazione di report

### <a name="reference"></a>Guida di riferimento

            void SendAppInfo(Dictionary<object, object> appInfos)

È possibile segnalare manualmente funzione tracciabilità informazioni o altre applicazioni specifiche informazioni mediante la SendAppInfo().

Si noti che queste informazioni possono essere inviati in modo incrementale: l'ultimo valore per una determinata chiave verrà mantenuto per un determinato dispositivo. Come evento extra, usare un dizionario\<oggetto,\> allegare informazioni.

### <a name="example"></a>Esempio

            Dictionary<object, object> appInfo = new Dictionary<object, object>()
            {
               {"subscription", "2013-12-07"},
               {"premium", "true"}
            };
            
            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Limiti

#### <a name="keys"></a>Chiavi

Ogni chiave dell'oggetto deve corrispondere l'espressione seguente:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Significa che tasti devono iniziare con almeno una lettera, seguita da lettere, cifre o caratteri di sottolineatura (\_).

#### <a name="size"></a>Dimensioni

Informazioni sull'applicazione sono limitate a **1024** caratteri per la chiamata.

Nell'esempio precedente, JSON inviati al server è 44 caratteri:

            {"subscription":"2013-12-07","premium":"true"}
 
##<a name="logging"></a>Registrazione
###<a name="enable-logging"></a>Attivare la registrazione

SDK può essere configurato per produrre log di test nella console di IDE.
I registri non sono attivati per impostazione predefinita. Per personalizzare questo, aggiornare la proprietà `EngagementAgent.Instance.TestLogEnabled` un valore disponibile la `EngagementTestLogLevel` enumerazione, ad esempio:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();
