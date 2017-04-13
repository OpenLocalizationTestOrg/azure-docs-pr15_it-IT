<properties
    pageTitle="Invio di notifiche Push protetto con gli hub di notifica Azure"
    description="Informazioni su come inviare le notifiche push sicura a un'app Android da Azure. Esempi di codice scritti in Java e c#."
    documentationCenter="android"
    keywords="invio di notifiche, le notifiche push push messaggi, le notifiche push android"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

#<a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Invio di notifiche Push protetto con gli hub di notifica Azure

> [AZURE.SELECTOR]
- [Universale di Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

##<a name="overview"></a>Panoramica

> [AZURE.IMPORTANT] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Supporto di notifica push di Microsoft Azure consente di accedere a un'infrastruttura di messaggio push di semplice utilizzo, multipiattaforma e scalabilità orizzontale, semplificando notevolmente l'implementazione delle notifiche push per le applicazioni aziendali e per le piattaforme di dispositivi mobili.

A causa di normative o ai vincoli di sicurezza, a volte un'applicazione può decidere di includere nel messaggio di notifica che non è possibile trasmettere attraverso l'infrastruttura di notifica push standard. In questa esercitazione viene illustrato come ottenere la stessa esperienza mediante l'invio di informazioni riservate tramite una connessione protetta e autenticata tra il dispositivo Android client e back-end app.

Un alto livello, il flusso è:

1. App back-end:
    - Payload sicura archivia nel database back-end.
    - Invia l'ID di questa notifica al dispositivo Android (nessuna informazione sicura viene inviata).
2. L'app nel dispositivo, quando si riceve la notifica:
    - Il dispositivo Android back-end richiede payload sicuro dei contatti.
    - L'app è possibile visualizzare il payload di una notifica sul dispositivo.

È importante tenere presente che nel flusso precedente e in questa esercitazione, si presuppone che il dispositivo memorizza un token di autenticazione in un archivio locale, dopo l'utente accede. In questo modo si garantisce un'esperienza completamente trasparente, mentre il dispositivo è possibile recuperare payload sicura di notifica con questo token. Se l'applicazione non memorizzare i token di autenticazione sul dispositivo o se questi token possono essere scaduti, l'app di dispositivo, alla ricezione di notifica push dovrebbe essere visualizzata una notifica generica che richiede all'utente per avviare l'app. L'app quindi autentica l'utente e Mostra il payload di notifica.

In questa esercitazione viene illustrato come inviare le notifiche push sicura. Viene creata in esercitazione [Informare gli utenti](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) in modo che è necessario completare la procedura in tale esercitazione prima di tutto se non è già.

> [AZURE.NOTE] In questa esercitazione si presuppone che è stato creato e configurato l'hub di notifica, come descritto nella [Guida introduttiva di notifica hub (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Modificare il progetto di Android

Ora che è stato modificato l'app back-end per inviare solo l' *id* di una notifica push, è necessario modificare l'app di Android per gestire la notifica di richiamare il back-end per recuperare il messaggio protetto da visualizzare.
A tale scopo, è necessario assicurarsi che l'app di Android sa come autenticarsi con il back-end quando riceve le notifiche push.

È ora verrà modificato il flusso di *accesso* per salvare il valore dell'intestazione di autenticazione nelle preferenze di condivise dell'app. Meccanismi analoghe possono essere usate per archiviare qualsiasi token di autenticazione (ad esempio i token OAuth) che l'app sarà necessario utilizzare senza le credenziali dell'utente.

1. Nel progetto app Android, aggiungere le seguenti costanti nella parte superiore della classe **MainActivity** :

        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";

2. Ancora in corso **MainActivity** aggiornare il `getAuthorizationHeader()` metodo per contenere il codice seguente:

        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

            return basicAuthHeader;
        }

3. Aggiungere quanto segue `import` le istruzioni nella parte superiore del file **MainActivity** :

        import android.content.SharedPreferences;

Modificare ora il gestore chiamata quando si riceve la notifica.

4. In **MyHandler** classe cambia la `OnReceive()` metodo per contenere:

        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }

5. Quindi aggiungere il `retrieveNotification()` metodo, sostituendo il segnaposto `{back-end endpoint}` con l'endpoint di back-end ottenuto quando si distribuisce il back-end:

        private void retrieveNotification(final String secureMessageId) {
            SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                        request.addHeader("Authorization", "Basic "+authorizationHeader);
                        HttpResponse response = new DefaultHttpClient().execute(request);
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                            throw new RuntimeException("Error retrieving secure notification");
                        }
                        String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                        JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                        sendNotification(secureNotification.getString("Payload"));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
        }


Questo metodo chiama l'app back-end per recuperare il contenuto di notifica mediante le credenziali archiviate nelle preferenze di condiviso e lo visualizza come una notifica normale. Messaggio di notifica consente all'utente di app esattamente come qualsiasi altro notifica push.

Si noti che è preferibile gestire casi di proprietà di intestazione mancanti autenticazione o il rifiuto il back-end. Gestione specifica di questi casi dipendono prevalentemente l'esperienza utente di destinazione. È possibile visualizzare una notifica con la richiesta generica per gli utenti autenticati per recuperare la notifica effettiva.

## <a name="run-the-application"></a>Eseguire l'applicazione

Per eseguire l'applicazione, eseguire le operazioni seguenti:

1. Assicurarsi che **AppBackend** viene distribuito in Azure. In Visual Studio, eseguire l'applicazione di API Web **AppBackend** . Viene visualizzata una pagina web ASP.NET.

2. In Eclisse, eseguire l'app in un dispositivo Android fisico o emulatore.

3. Nell'app di Android dell'interfaccia utente, immettere un nome utente e password. Può trattarsi di qualsiasi stringa, ma devono essere lo stesso valore.

4. Nell'app di Android dell'interfaccia utente, fare clic su **accesso**. Fare clic su **Invia push**.
