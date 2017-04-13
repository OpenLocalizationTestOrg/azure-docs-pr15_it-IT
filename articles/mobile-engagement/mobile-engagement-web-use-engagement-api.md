<properties
    pageTitle="Azure coinvolgimento Mobile Web SDK API | Microsoft Azure"
    description="Gli ultimi aggiornamenti e procedure di Web SDK per Azure Mobile coinvolgimento"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="piyushjo" />

# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>Utilizzare l'API di impegno di Azure Mobile in un'applicazione web

In questo documento è un componente aggiuntivo per il documento in cui viene illustrato come [integrare impegno Mobile in un'applicazione web](mobile-engagement-web-integrate-engagement.md). Fornisce approfondite informazioni dettagliate su come utilizzare l'API di impegno Mobile Azure per segnalare le statistiche dell'applicazione.

L'API di impegno Mobile viene fornito dalla `engagement.agent` oggetto. Il valore predefinito è alias Azure Mobile coinvolgimento Web SDK `engagement`. È possibile ridefinire l'alias dalla configurazione SDK.

## <a name="mobile-engagement-concepts"></a>Concetti di impegno mobile

Dalle parti seguenti consentono di migliorare [Mobile coinvolgimento concetti](mobile-engagement-concepts.md) comuni per la piattaforma web.

### <a name="session-and-activity"></a>`Session`e`Activity`

Se l'utente resta inattivo per più di alcuni secondi tra due attività, sequenza dell'utente di attività viene suddiviso in due sessioni distinte. Questi alcuni secondi sono denominati il timeout di sessione.

Se l'applicazione web non dichiara la fine delle attività dell'utente per sé (chiamando il `engagement.agent.endActivity` funzione), il server Mobile coinvolgimento scade automaticamente la sessione dell'utente all'interno di tre minuti dopo la chiusura della pagina dell'applicazione. Questo comando si chiama il timeout di sessione server.

### `Crash`

Report automatici di eccezioni JavaScript non rilevate non vengono creati per impostazione predefinita. Tuttavia, è possibile segnalare anomalo in modo manuale mediante la `sendCrash` funzione (vedere la sezione report anomalo).

## <a name="reporting-activities"></a>Attività di Reporting

Creazione di report sull'attività dell'utente include quando si inizia una nuova attività e quando l'utente termina l'attività corrente.

### <a name="user-starts-a-new-activity"></a>Si inizia una nuova attività

    engagement.agent.startActivity("MyUserActivity");

È necessario chiamare `startActivity()` ogni attività utente viene modificato. La prima chiamata a questa funzione avvia una nuova sessione utente.

### <a name="user-ends-the-current-activity"></a>Utente termina l'attività corrente

    engagement.agent.endActivity();

È necessario chiamare `endActivity()` almeno una volta quando l'utente termina loro ultima attività. Mobile coinvolgimento Web SDK informa che l'utente è attualmente inattivo e che la sessione utente deve essere chiuso dopo il timeout di sessione scade. Se si chiama `startActivity()` prima della scadenza il timeout di sessione, è sufficiente ripresa la sessione.

Poiché non esiste alcun chiamata affidabile per quando si chiude la finestra di selezione, è spesso difficile o Impossibile intercettare la fine delle attività dell'utente all'interno di un ambiente web. Questo è il motivo per cui il server Mobile coinvolgimento scade automaticamente la sessione dell'utente all'interno di tre minuti dopo la chiusura della pagina dell'applicazione.

## <a name="reporting-events"></a>Segnalazione di eventi

Creazione di report gli eventi è descritta agli eventi di sessione di autonomo.

### <a name="session-events"></a>Eventi della sessione

Gli eventi della sessione vengono in genere utilizzati per segnalare le azioni eseguite da un utente durante la sessione dell'utente.

**Esempio senza dati aggiuntivi:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**Esempio con dati aggiuntivi:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>Eventi autonomo

Diversamente da quanto succede gli eventi della sessione, gli eventi autonomo possono verificarsi di fuori di una sessione.

Per tale, utilizzare ``engagement.agent.sendEvent`` anziché ``engagement.agent.sendSessionEvent``.

## <a name="reporting-errors"></a>Segnalazione degli errori

Segnalazione errori copre errori di sessione e autonomo.

### <a name="session-errors"></a>Errori sessione

Gli errori di sessione in genere vengono utilizzati per inviare una segnalazione errori che hanno un impatto sull'utente durante la sessione dell'utente.

**Esempio senza dati aggiuntivi:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**Esempio con dati aggiuntivi:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>Errori autonomo

A differenza degli errori di sessione, possono verificarsi errori autonoma di fuori di una sessione.

Per tale, utilizzare `engagement.agent.sendError` anziché `engagement.agent.sendSessionError`.

## <a name="reporting-jobs"></a>Processi di report

Creazione di report in processi copertine reporting errori ed eventi che si verificano durante un processo e si blocca.

**Esempio:**

Se si desidera eseguire il monitoraggio di una richiesta AJAX, utilizzare le operazioni seguenti:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>Segnalazione degli errori durante un processo

Errori possono essere correlati a un processo in esecuzione invece che alla sessione utente corrente.

**Esempio:**

Se si desidera segnalare un errore se si verifica un errore di una richiesta AJAX:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>Segnalazione di eventi durante un processo

Eventi possono essere correlati a un processo in esecuzione invece che alla sessione utente corrente, grazie alla `engagement.agent.sendJobEvent` funzione.

Questa funzione funziona esattamente come `engagement.agent.sendJobError`.

### <a name="reporting-crashes"></a>Creazione di report anomalo

Utilizzare la `sendCrash` funzione al report determina l'arresto anomalo manualmente.

Il `crashid` argomento è una stringa che identifica il tipo di arresto anomalo.
Il `crash` argomento è in genere la traccia dello stack di arresto anomalo come stringa.

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>Parametri aggiuntivi

È possibile allegare dati non autorizzati a un evento, l'errore, attività o processo.

I dati possono essere qualsiasi oggetto JSON (ma non una matrice o tipo di base).

**Esempio:**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>Limiti

Limiti si applicano ai parametri aggiuntivi disponibili nelle aree di espressioni regolari per tasti, i tipi di valore e le dimensioni.

#### <a name="keys"></a>Chiavi

Ogni chiave dell'oggetto deve corrispondere l'espressione seguente:

    ^[a-zA-Z][a-zA-Z_0-9]*

Ciò significa che tasti devono iniziare con almeno una lettera, seguito da lettere, cifre o caratteri di sottolineatura (\_).

#### <a name="values"></a>Valori

I valori sono limitati alla stringa, numero e tipi di valore Boolean.

#### <a name="size"></a>Dimensioni

Funzionalità aggiuntive sono limitate alla 1.024 caratteri per chiamata (dopo Mobile coinvolgimento Web SDK viene eseguita la codifica in JSON).

## <a name="reporting-application-information"></a>Informazioni sull'applicazione di creazione di report

È possibile segnalare verifica informazioni (o altre informazioni specifiche di un'applicazione) manualmente utilizzando il `sendAppInfo()` funzione.

Si noti che queste informazioni possono essere inviate in modo incrementale. L'ultimo valore per una specifica chiave verrà mantenuto per un dispositivo specifico.

Ad esempio extra evento, è possibile utilizzare qualsiasi oggetto JSON per estrarre le informazioni dell'applicazione. Si noti che le matrici o oggetti secondari vengono considerati come stringhe flat (con serializzazione JSON).

**Esempio:**

Ecco un esempio di codice per l'invio di sesso dell'utente e la data di nascita:

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>Limiti

Limiti che si applicano alle informazioni sulle applicazioni disponibili nelle aree di espressioni regolari per chiavi e dimensioni.

#### <a name="keys"></a>Chiavi

Ogni chiave dell'oggetto deve corrispondere l'espressione seguente:

    ^[a-zA-Z][a-zA-Z_0-9]*

Ciò significa che tasti devono iniziare con almeno una lettera, seguito da lettere, cifre o caratteri di sottolineatura (\_).

#### <a name="size"></a>Dimensioni

Informazioni sull'applicazione è limitate ai 1.024 caratteri per chiamata (dopo Mobile coinvolgimento Web SDK viene eseguita la codifica in JSON).

Nell'esempio precedente, JSON inviati al server è 44 caratteri:

    {"birthdate":"1983-12-07","gender":"female"}
