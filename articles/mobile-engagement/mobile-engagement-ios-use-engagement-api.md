<properties
    pageTitle="Come usare l'API coinvolgimento iOS"
    description="Ultima iOS SDK - come usare l'API coinvolgimento iOS"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />


#<a name="how-to-use-the-engagement-api-on-ios"></a>Come usare l'API coinvolgimento iOS

In questo documento è un componente aggiuntivo per il documento come integrare impegno in iOS: fornisce antero-posteriore informazioni dettagliate su come utilizzare l'API di impegno per segnalare le statistiche dell'applicazione.

Tenere presente che se si vuole solo impegno per segnalare l'applicazione sessioni, attività, anomalo e informazioni tecniche, quindi il modo più semplice per rendere tutti personalizzato `UIViewController` oggetti ereditano corrispondente `EngagementViewController` classe.

Se si desidera eseguire operazioni più complesse, ad esempio se è necessario inviare una segnalazione eventi specifici dell'applicazione, gli errori e processi, o se si dispone segnalare le attività dell'applicazione in modo diverso rispetto a quella implementata nel `EngagementViewController` classi, è necessario utilizzare l'API di impegno.

L'API di impegno viene fornita tramite il `EngagementAgent` classe. Un'istanza di questa classe può essere recuperata chiamando il `[EngagementAgent shared]` metodo statico (si noti che la `EngagementAgent` oggetto restituito è un singleton).

Prima delle chiamate API di `EngagementAgent` oggetto deve essere inizializzato utilizzando il metodo`[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`

##<a name="engagement-concepts"></a>Concetti di impegno

Dalle parti seguenti consentono di migliorare i comuni [Mobile coinvolgimento concetti](mobile-engagement-concepts.md) per la piattaforma iOS.

### <a name="session-and-activity"></a>`Session`e`Activity`

Un' *attività* è generalmente associato a una schermata dell'applicazione, vale a dire l' *attività* viene avviato quando la schermata viene visualizzata e si arresta quando la schermata viene chiusa: si tratta le maiuscole/minuscole quando SDK coinvolgimento è integrato con la `EngagementViewController` classi.

Ma *attività* anche possibile controllare manualmente tramite l'API di impegno. In questo modo per dividere una schermata specificata in più parti sub per ottenere ulteriori informazioni sull'utilizzo di questa schermata (ad esempio per noti con quale frequenza e per quanto tempo all'interno di questa schermata vengono utilizzate le finestre di dialogo).

##<a name="reporting-activities"></a>Attività di Reporting

### <a name="user-starts-a-new-activity"></a>Si inizia una nuova attività

            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

È necessario chiamare `startActivity()` ogni volta che le modifiche di attività utente. La prima chiamata a questa funzione avvia una nuova sessione utente.

### <a name="user-ends-his-current-activity"></a>Utente termina la propria attività corrente

            [[EngagementAgent shared] endActivity];

> [AZURE.WARNING] Si deve **mai** chiamare questa funzione per se stessi, a meno che non si desidera dividere un utilizzo dell'applicazione in più sessioni: una chiamata a questa funzione da terminare la sessione corrente immediatamente, pertanto, una chiamata successiva al `startActivity()` inizia una nuova sessione. Questa funzione è chiamata automaticamente da SDK quando l'applicazione è chiuso.

##<a name="reporting-events"></a>Segnalazione di eventi

### <a name="session-events"></a>Eventi della sessione

Gli eventi della sessione vengono in genere utilizzati per segnalare le azioni eseguite da un utente durante la sessione.

**Esempio senza dati aggiuntivi:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**Esempio con dati aggiuntivi:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>Eventi autonomo

Contrasto con gli eventi di sessione autonoma possono essere utilizzati all'esterno del contesto di una sessione.

**Esempio:**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

##<a name="reporting-errors"></a>Segnalazione degli errori

### <a name="session-errors"></a>Errori sessione

Errori sessione vengono in genere utilizzati per inviare una segnalazione errori impatto l'utente durante la sessione.

**Esempio:**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>Errori autonomo

Contrasto con errori di sessione autonoma errori possono essere utilizzati all'esterno del contesto di una sessione.

**Esempio:**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

##<a name="reporting-jobs"></a>Processi di report

**Esempio:**

Si supponga di voler segnalare la durata del processo di accesso:

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>Segnalazione errori durante un processo

Errori possono essere correlati a un processo in esecuzione anziché correlate alla sessione utente corrente.

**Esempio:**

Si supponga di voler segnalato un errore durante il processo di accesso:

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>Eventi durante un processo

Eventi possono essere correlati a un processo in esecuzione anziché correlate alla sessione utente corrente.

**Esempio:**

Si supponga che dispone di un social network e serve un processo al report il tempo totale durante il quale l'utente è connesso al server. L'utente può ricevere i messaggi da suoi amici, si tratta di un evento del processo.

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

##<a name="extra-parameters"></a>Parametri aggiuntivi

Per gli eventi, gli errori, attività e processi l'associazione dati non autorizzati.

Struttura di dati, viene utilizzata classe NSDictionary di iOS.

Si noti che possono contenere extra `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` o un altro `NSDictionary` istanze.

> [AZURE.NOTE] Il parametro aggiuntivo è serializzato in JSON. Se si desidera passare oggetti diversi rispetto a quelle descritte in precedenza, è necessario implementare il metodo seguente in una classe:
>
             -(NSString*)JSONRepresentation;
>
> Il metodo deve restituire una rappresentazione in formato JSON dell'oggetto.

### <a name="example"></a>Esempio

    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>Limiti

#### <a name="keys"></a>Chiavi

Ogni chiave di `NSDictionary` deve corrispondere l'espressione seguente:

`^[a-zA-Z][a-zA-Z_0-9]*`

Significa che tasti devono iniziare con almeno una lettera, seguita da lettere, cifre o caratteri di sottolineatura (\_).

#### <a name="size"></a>Dimensioni

Funzionalità aggiuntive sono limitate a **1024** caratteri per chiamata (una sola volta codificato in JSON dall'agente di impegno).

Nell'esempio precedente, JSON inviati al server è 58 caratteri:

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##<a name="reporting-application-information"></a>Informazioni sull'applicazione di creazione di report

È possibile segnalare manualmente verifica informazioni (o qualsiasi altra informazione applicazione specifica) utilizzando il `sendAppInfo:` funzione.

Si noti che queste informazioni possono essere inviati in modo incrementale: l'ultimo valore per una determinata chiave verrà conservato per un determinato dispositivo.

Ad esempio extra evento, la `NSDictionary` classe viene utilizzata per estrarre le informazioni dell'applicazione, tenere presente che le matrici o dizionari secondari verranno trattati come stringhe flat (con serializzazione JSON).

**Esempio:**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>Limiti

#### <a name="keys"></a>Chiavi

Ogni chiave di `NSDictionary` deve corrispondere l'espressione seguente:

`^[a-zA-Z][a-zA-Z_0-9]*`

Significa che tasti devono iniziare con almeno una lettera, seguita da lettere, cifre o caratteri di sottolineatura (\_).

#### <a name="size"></a>Dimensioni

Informazioni sull'applicazione sono limitate a **1024** caratteri per chiamata (una sola volta codificato in JSON dall'agente di impegno).

Nell'esempio precedente, JSON inviati al server è 44 caratteri:

    {"birthdate":"1983-12-07","gender":"female"}
