<properties
    pageTitle="Come usare coinvolgimento API nel dispositivo Android"
    description="Versione più recente SDK Android - come utilizzare il coinvolgimento API in Android"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="piyushjo;ricksal" />

#<a name="how-to-use-the-engagement-api-on-android"></a>Come usare coinvolgimento API nel dispositivo Android

In questo documento è un componente aggiuntivo per il documento [Opzioni avanzate dei report per Android Mobile coinvolgimento SDK](mobile-engagement-android-advanced-reporting.md). Fornisce in antero-posteriore informazioni dettagliate su come utilizzare l'API di impegno per segnalare le statistiche dell'applicazione.

Tenere presente che se si vuole solo impegno per segnalare l'applicazione sessioni, attività, anomalo e informazioni tecniche, quindi il modo più semplice per rendere tutti i `Activity` classi secondari ereditano corrispondente `EngagementActivity` classe.

Se si desidera eseguire operazioni più complesse, ad esempio se è necessario inviare una segnalazione eventi specifici dell'applicazione, gli errori e processi, o se si dispone segnalare le attività dell'applicazione in modo diverso rispetto a quella implementata nel `EngagementActivity` classi, è necessario utilizzare l'API di impegno.

L'API di impegno viene fornita tramite il `EngagementAgent` classe. Un'istanza di questa classe può essere recuperata chiamando il `EngagementAgent.getInstance(Context)` metodo statico (si noti che la `EngagementAgent` oggetto restituito è un singleton).

##<a name="engagement-concepts"></a>Concetti di impegno

Dalle parti seguenti consentono di migliorare i comuni [Concetti coinvolgimento Mobile](mobile-engagement-concepts.md), per la piattaforma Android.

### <a name="session-and-activity"></a>`Session`e`Activity`

Se l'utente resta più di alcuni secondi di inattività tra due *attività*, il suo sequenza delle *attività* viene suddiviso in due distinti *sessioni*. Questi alcuni secondi sono denominati "timeout di sessione".

Un' *attività* è generalmente associato a una schermata dell'applicazione, vale a dire l' *attività* viene avviato quando la schermata viene visualizzata e si arresta quando la schermata viene chiusa: si tratta le maiuscole/minuscole quando SDK coinvolgimento è integrato con la `EngagementActivity` classi.

Ma *attività* anche possibile controllare manualmente tramite l'API di impegno. In questo modo dividere uno schermo specificato in più parti sub per ottenere ulteriori informazioni sull'utilizzo di questa schermata (ad esempio per noti con quale frequenza e per quanto tempo all'interno di questa schermata vengono utilizzate le finestre di dialogo).

##<a name="reporting-activities"></a>Attività di Reporting

> [AZURE.IMPORTANT] Non è necessario report come le attività descritte in questa sezione se si usa il `EngagementActivity` classe e le sue varianti come descritto nella procedura integrare impegno documento Android.

### <a name="user-starts-a-new-activity"></a>Si inizia una nuova attività

            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

È necessario chiamare `startActivity()` ogni volta che le modifiche di attività utente. La prima chiamata a questa funzione avvia una nuova sessione utente.

Il modo migliore per chiamare questa funzione è in ogni attività `onResume` callback.

### <a name="user-ends-his-current-activity"></a>Utente termina la propria attività corrente

            EngagementAgent.getInstance(this).endActivity();

È necessario chiamare `endActivity()` almeno una volta quando l'utente termina la propria attività ultima. Informa SDK impegno che l'utente si trova attualmente inattivo e che è necessario chiudere una volta il timeout di sessione la sessione utente scadrà (se si chiama `startActivity()` prima della scadenza il timeout di sessione, è sufficiente ripresa alla sessione).

Il modo migliore per chiamare questa funzione è in ogni attività `onPause` callback.

##<a name="reporting-events"></a>Segnalazione di eventi

### <a name="session-events"></a>Eventi della sessione

Gli eventi della sessione vengono in genere utilizzati per segnalare le azioni eseguite da un utente durante la sessione.

**Esempio senza dati aggiuntivi:**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**Esempio con dati aggiuntivi:**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### <a name="standalone-events"></a>Eventi autonomo

Contrasto con gli eventi della sessione, gli eventi autonomo possono verificarsi all'esterno del contesto di una sessione.

**Esempio:**

Si supponga di voler eventi di report che si verificano quando un ricevitore trasmissione viene attivato:

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

##<a name="reporting-errors"></a>Segnalazione degli errori

### <a name="session-errors"></a>Errori sessione

Errori sessione vengono in genere utilizzati per inviare una segnalazione errori impatto l'utente durante la sessione.

**Esempio:**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### <a name="standalone-errors"></a>Errori autonomo

Contrasto con errori di sessione, possono verificarsi errori autonoma all'esterno del contesto di una sessione.

**Esempio:**

Nell'esempio seguente viene illustrato come segnalare l'errore ogni volta che la memoria diventa bassa nel telefono durante l'esecuzione del processo dell'applicazione.

            public MyApplication extends EngagementApplication {

              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

##<a name="reporting-jobs"></a>Processi di report

### <a name="example"></a>Esempio

Si supponga di voler segnalare la durata del processo di accesso:

            [...]
            public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);

              [... sign in ...]

              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="report-errors-during-a-job"></a>Segnalazione errori durante un processo

Errori possono essere correlati a un processo in esecuzione anziché correlate alla sessione utente corrente.

**Esempio:**

Si supponga che si desidera segnalare un errore durante il si accesso processo:

[...] Accesso void pubblico (contesto contesto,...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);

              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);

                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="reporting-events-during-a-job"></a>Segnalazione di eventi durante un processo

Eventi possono essere correlati a un processo in esecuzione anziché correlate alla sessione utente corrente.

**Esempio:**

Si supponga che dispone di un social network e serve un processo al report il tempo totale durante il quale l'utente è connesso al server. L'utente può rimanere connesso in background anche quando si usa un'altra applicazione o il telefono è inattivo, in modo che nessuna sessione.

L'utente può ricevere i messaggi da suoi amici, si tratta di un evento del processo.

            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

##<a name="extra-parameters"></a>Parametri aggiuntivi

Per gli eventi, gli errori, attività e processi l'associazione dati non autorizzati.

Struttura di dati, viene utilizzata classe aggregazione di Android (in realtà, vale come parametri aggiuntivi in intenti Android). Si noti che può contenere un raggruppamento matrici o un'altra aggregazione istanze.

> [AZURE.IMPORTANT] Se si inseriscono in parametri parcelable o serializzabili, assicurarsi che le `toString()` metodo implementato per restituire una stringa leggibile. DLL che contengono i campi non temporanei che non sono serializzabili effettuerà Android arresto anomalo quando eseguiranno una chiamata`bundle.putSerializable("key",value);`

> [AZURE.WARNING] Matrici sparse in parametri aggiuntivi non sono supportate, vale a dire non essere serializzato sotto forma di matrice. È necessario convertirli in matrici standard prima di utilizzare parametri aggiuntivi.

### <a name="example"></a>Esempio

            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### <a name="limits"></a>Limiti

#### <a name="keys"></a>Chiavi

Ogni chiave di `Bundle` deve corrispondere l'espressione seguente:

`^[a-zA-Z][a-zA-Z_0-9]*`

Significa che tasti devono iniziare con almeno una lettera, seguita da lettere, cifre o caratteri di sottolineatura (\_).

#### <a name="size"></a>Dimensioni

Funzionalità aggiuntive sono limitate a **1024** caratteri per chiamata (una sola volta codificato in JSON dal servizio coinvolgimento).

Nell'esempio precedente, JSON inviati al server è 58 caratteri:

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##<a name="reporting-application-information"></a>Informazioni sull'applicazione di creazione di report

È possibile segnalare manualmente verifica informazioni (o qualsiasi altra informazione applicazione specifica) utilizzando il `sendAppInfo()` funzione.

Si noti che queste informazioni possono essere inviati in modo incrementale: l'ultimo valore per una determinata chiave verrà mantenuto per un determinato dispositivo.

Ad esempio extra evento, la classe di aggregazione viene utilizzata per estrarre le informazioni dell'applicazione, tenere presente che le matrici o bundle secondari verranno trattati come stringhe flat (con serializzazione JSON).

### <a name="example"></a>Esempio

Ecco un esempio di codice per inviare data di nascita e sesso utente:

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### <a name="limits"></a>Limiti

#### <a name="keys"></a>Chiavi

Ogni chiave di `Bundle` deve corrispondere l'espressione seguente:

`^[a-zA-Z][a-zA-Z_0-9]*`

Significa che tasti devono iniziare con almeno una lettera, seguita da lettere, cifre o caratteri di sottolineatura (\_).

#### <a name="size"></a>Dimensioni

Informazioni sull'applicazione sono limitate a **1024** caratteri per chiamata (una sola volta codificato in JSON dal servizio coinvolgimento).

Nell'esempio precedente, JSON inviati al server è 44 caratteri:

            {"expiration":"2016-12-07","status":"premium"}
