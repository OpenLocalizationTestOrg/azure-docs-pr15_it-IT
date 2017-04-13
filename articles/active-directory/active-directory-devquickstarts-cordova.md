<properties
    pageTitle="Azure Active Directory Cordova introduzione | Microsoft Azure"
    description="Modalità di compilazione di un'applicazione di Cordova che si integra con Azure Active Directory per accedere e chiama Azure Active Directory protetto API utilizzando OAuth."
    services="active-directory"
    documentationCenter=""
    authors="vibronet"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="vittorib"/>

# <a name="integrate-azure-ad-with-an-apache-cordova-app"></a>Integrare Azure Active Directory in un quest'ultimo app Cordova

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Apache Cordova rende per lo sviluppo di applicazioni HTML5/JavaScript che possono essere eseguiti sui dispositivi mobili come applicazioni native complete.
Con Azure Active Directory, è possibile aggiungere funzionalità di autenticazione voto enterprise alle applicazioni di Cordova. Grazie a un plug-in Cordova disposizione SDK nativo di Azure Active Directory in iOS, Android, Windows Store e Windows Phone, è possibile migliorare l'applicazione per supportare accesso su con un account di Active Directory degli utenti, accedere a Office 365 e Azure API e proteggere anche le chiamate al proprio API Web personalizzata.

In questa esercitazione verrà utilizzato il plug-in Apache Cordova per Active Directory autenticazione raccolta (ADAL) per migliorare un'app semplice con le caratteristiche seguenti:

-   Con pochi righe di codice, eseguire l'autenticazione di un utente di Active Directory e ottenere un token per richiamare l'API di Azure Active Directory grafico.
-   Utilizzare tale token per richiamare l'API di grafico per tale directory della query e visualizzare i risultati  
-   Sfruttare la cache dei token ADAL per ridurre al minimo le richieste di autenticazione dell'utente.

Per eseguire questa operazione, è necessario:

2. Registrare un'applicazione di Azure Active Directory
2. Aggiungere codice per l'app richiesta token
3. Aggiungere il codice per utilizzare il token per eseguire la query dell'API di grafico e visualizzazione di risultati.
4. Creare il progetto di distribuzione Cordova con tutte le piattaforme di destinazione e plug-in di ADAL Cordova e testare la soluzione in emulatori.

## <a name="0--prerequisites"></a>*0. prerequisiti di*

Per completare questa esercitazione che sarà necessario:

- Un tenant di Azure Active Directory in cui si ha un account con diritti di sviluppo di app
- Un ambiente di sviluppo configurato per l'utilizzo Apache Cordova  

Se si dispone sia già configurato, procedere direttamente al passaggio 1.

Se non si dispone di un tenant di Azure Active Directory, è possibile trovare [istruzioni su come ottenerne uno qui](active-directory-howto-tenant.md).

Se non si dispone di Cordova Apache configurare nel computer, installare quanto segue:

- [Operazioni](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [NodeJS](https://nodejs.org/download/)
- [Cordova CLI](https://cordova.apache.org/) (può essere installato facilmente tramite Gestione pacchetti NPM: `npm install -g cordova`)

Si noti che quelle deve lavorare nel PC e su Mac.

Ogni piattaforma di destinazione è diversi prerequisiti.

- Per creare ed eseguire Windows Tablet/PC o telefono app versione
    - [Visual Studio 2013 per Windows con aggiornamento 2 o versione successiva](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express o un'altra versione).
- Per creare ed eseguire per iOS
    -   Xcode 5. x o versione successiva. Scaricare http://developer.apple.com/downloads o [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12)
    -   [ios Image](https://www.npmjs.org/package/ios-sim) : consente di avvio App iOS in iOS simulatore dalla riga di comando (può essere installato facilmente tramite il terminale: `npm install -g ios-sim`)

- Per creare ed eseguire l'applicazione per Android
    - Installare [Java Development Kit (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o versione successiva. Assicurarsi che `JAVA_HOME` (variabile di ambiente) sia impostata correttamente in base a percorso di installazione JDK (ad esempio c:\Programmi\Microsoft Files\Java\jdk1.7.0_75).
    - Installare [SDK Android](http://developer.android.com/sdk/installing/index.html?pkg=tools) e aggiungere `<android-sdk-location>\tools` percorso (ad esempio C:\tools\Android\android-sdk\tools) per il `PATH` variabile di ambiente.
    - Aprire Gestione SDK Android (ad esempio, da terminale: `android`) e installare
    - Piattaforma *5.0.1 Android (API 21)* SDK
    - *Strumenti di generazione SDK android* versione 19.1.0 o versioni successive
    - *Archivio di supporto per Android* (Extra)

  Android sdk non fornisce un'istanza di emulatore predefinito. Creare un nuovo PIN eseguendo `android avd` da terminale e quindi selezionare *Crea …* se si desidera eseguire Android app emulatore. Consigliato *Livello Api* è 19 o versioni successive, vedere del mese di AVD Manager (http://developer.android.com/tools/help/avd-manager.html) per ulteriori informazioni sulle opzioni di creazione ed emulatore Android.


## <a name="1--register-an-application-with-azure-ad"></a>*1. registrare un'applicazione di Azure Active Directory*

Nota: questo __passaggio è facoltativo__. L'esercitazione fornito pre-provisioning valori che sono possibile vedere l'esempio in azione senza eseguire qualsiasi provisioning nel proprio tenant. Tuttavia è consigliabile eseguire questo passaggio e acquisire familiarità con il processo, come saranno necessario quando si creerà le applicazioni.

Azure Active Directory visualizzerà solo i token per le applicazioni. Prima di poter usare Azure Active Directory dell'App, è necessario creare una voce per renderla nel tenant.  Registrare una nuova applicazione nel tenant di

-   Accedere al [portale di gestione di Azure](https://manage.windowsazure.com)
-   Nel riquadro di spostamento a sinistra, fare clic su **Active Directory**
-   Selezionare il tenant nel punto in cui si desidera registrare l'applicazione.
-   Fare clic sulla scheda **applicazioni** e fare clic su **Aggiungi** nel cassetto inferiore.
-   Seguire le istruzioni e creare una nuova **Applicazione Client nativa** (nonostante il fatto che Cordova App sono basate su HTML, viene creata qui l'applicazione client nativi in modo `Native Client Application` opzione deve essere selezionata, in caso contrario, l'applicazione non è utilizzabile).
    -   Il **nome** dell'applicazione per descrivere l'applicazione agli utenti finali
    -   **Reindirizzare URI** è l'URI utilizzato per restituire i token all'app. Immettere `http://MyDirectorySearcherApp`.

Dopo aver completato la registrazione, AAD assegna l'app un identificatore univoco client.  È necessario questo valore nelle sezioni successive: è possibile trovare nella scheda **Configura** dell'app appena creato.

Per eseguire `DirSearchClient Sample`, concedere l'autorizzazione app appena creato all' _API di Azure Active Directory grafico_della query:
-   Nella scheda **Configura** , individuare la sezione "Autorizzazioni da altre applicazioni".  Aggiungere l'autorizzazione di **accesso nella directory dell'utente registrato** con **Autorizzazioni di delega**per l'applicazione "Azure Active Directory".  In questo modo sarà l'applicazione per l'API di grafico per gli utenti della query.

## <a name="2-clone-the-sample-app-repository-required-for-the-tutorial"></a>*2. Clona repository app esempio necessari per l'esercitazione*

Dal shell o riga di comando, digitare il comando seguente:

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="3-create-the-cordova-app"></a>*3. creare app Cordova*

Esistono diversi modi per la creazione di applicazioni Cordova. In questa esercitazione verrà usata l'interfaccia di riga di comando Cordova (CLI).
Dal shell o riga di comando, digitare il comando seguente:


     cordova create DirSearchClient --copy-from="NativeClient-MultiTarget-Cordova/DirSearchClient"

Che verrà creato la struttura delle cartelle e pagine di supporto temporaneo per il progetto Cordova, copiare il contenuto del progetto di partenza nella sottocartella www.
Spostare la nuova cartella DirSearchClient.

    cd .\DirSearchClient

Aggiungere il plug-in proprietà consentite, necessarie per richiamare l'API di grafico.

     cordova plugin add cordova-plugin-whitelist

Aggiungere quindi tutte le piattaforme desiderato per il supporto. Per disporre di un campione di lavoro, sarà necessario eseguire almeno uno dei comandi riportati di seguito. Si noti che non sarà possibile emulare iOS in Windows o Windows/Windows Phone in un Mac.

    cordova platform add android
    cordova platform add ios
    cordova platform add windows

Infine, è possibile aggiungere ADAL per plug-in Cordova al progetto.

    cordova plugin add cordova-plugin-ms-adal

## <a name="3-add-code-to-authenticate-users-and-obtain-tokens-from-aad"></a>*3. aggiungere codice per l'autenticazione degli utenti e ottenere i token da AAD*

L'applicazione che si sviluppa in questa esercitazione fornisce una funzionalità di ricerca osso BI directory, nel punto in cui l'utente può digitare l'alias di qualsiasi utente nella directory e visualizzare alcuni attributi di base.  Il progetto di partenza contiene la definizione dell'interfaccia utente di base dell'app (in www/index.html) e la struttura mediante cavi evento app base cicli, le associazioni di interfaccia utente e dei risultati della logica di visualizzazione (in www/js/index.js). L'unica ha aggiunto consiste nell'aggiungere logica di implementazione delle attività di identità.

La prima cosa che occorre fare è introdurre nel codice i valori di protocollo utilizzati da AAD per individuare l'app e le risorse che destinazione. Tali valori verranno utilizzati per creare le richieste di token in un secondo momento. Inserire il frammento di codice sotto nella parte superiore del file index.js.

```javascript
    var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

Il `redirectUri` e `clientId` valori devono corrispondere i valori che descrive l'app in AAD. Sono disponibili quelle dalla scheda Configura nel portale di Azure, come descritto nel passaggio 1 in precedenza in questa esercitazione.
Nota: se si è scelto di non sta registrando una nuova app nel proprio tenant, è possibile semplicemente incollare i valori preconfigurati sopra così come sono - che consentono di visualizzare l'esecuzione di esempio, se è necessario creare una voce per le App per produzione.

È necessario aggiungere il codice di richiesta di token effettivo. Inserire il frammento di codice seguente tra le `search `e `renderdata `definizioni.

```javascript
    // Shows user authentication dialog if required.
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Esaminare la funzione dalla scomposizione nella relativa due parti principali.
In questo esempio è progettato per funzionare con qualsiasi tenant, invece che saranno collegati a una determinata. Utilizza l'endpoint "/ comuni", che consente all'utente di immettere tutti gli account in fase di autenticazione e indirizza la richiesta per il tenant appartiene.
La prima parte del metodo esamina cache ADAL per determinare se è già presente un token stored - e se c'è, utilizzerà il tenant che fosse stato inviato per reinizializzare ADAL. Questo è necessario per evitare ulteriori istruzioni, come l'utilizzo di "/ comuni" comporta sempre richiedere all'utente di immettere un nuovo account.
```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
Nella seconda parte del metodo esegue la richiesta tokewn corretto.
Il `acquireTokenSilentAsync` metodo chiede di ADAL per restituire un token per la risorsa specificata senza con qualsiasi UX. Questo può accadere se la cache ha già un token di accesso appropriata archiviato, o se è presente un token di aggiornamento che può essere utilizzato per ottenere un nuovo token di accesso senza shwoing qualsiasi richiesta.
Se il tentativo non riesce, è ricorrere a `acquireTokenAsync` -visibile che richiede all'utente di eseguire l'autenticazione.
```javascript
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Ora che è stata creata il token, è infine futuro sarà possibile richiamare l'API grafico ed eseguire la query di ricerca che vogliamo. Inserire il frammento di codice seguente immediatamente sotto il `authenticate` definizione.

```javascript
// Makes Api call to receive user list.
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
I file di punto di partenza fornito un barebone dell'esperienza utente per l'immissione di un alias utente in una casella di testo. Questo metodo utilizza tale valore per creare una query, combinare con il token di accesso, inviare al grafico e analizzare i risultati. Il metodo renderData, già presente nel file di punto di partenza, si occupa di visualizzare i risultati.

## <a name="4-run"></a>*4. Esegui*
L'app è pronto per l'esecuzione! Operativo è molto semplice: dopo l'avvio dell'applicazione, immettere nella casella di testo l'alias dell'utente che si desidera cercare, quindi fare clic sul pulsante. Verrà richiesto per l'autenticazione. Dopo l'autenticazione e completato la ricerca, verranno visualizzati gli attributi dell'utente cercata. Esecuzioni successive eseguirà la ricerca senza visualizzare prompt di ringraziamento presenza nella cache del token acquisita in precedenza.
La procedura per l'esecuzione dell'app concreta varia a seconda della piattaforma.

####<a name="windows-10"></a>Windows 10:

   Tablet PC:`cordova run windows --archs=x64 -- --appx=uap`

   Cellulare (richiede il dispositivo Mobile Windows10 è connesso al PC):`cordova run windows --archs=arm -- --appx=uap --phone`

   __Nota__: durante l'esecuzione prima potrebbe essere necessario eseguire l'accesso per una licenza di sviluppo. Per ulteriori informazioni, vedere [la licenza sviluppatore](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx) .

####<a name="windows-81-tabletpc"></a>Tablet/PC con Windows 8.1:

   `cordova run windows`

   __Nota__: durante l'esecuzione prima potrebbe essere necessario eseguire l'accesso per una licenza di sviluppo. Per ulteriori informazioni, vedere [la licenza sviluppatore](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx) .

####<a name="windows-phone-81"></a>Windows Phone 8.1:

   Per eseguire su dispositivo connesso:`cordova run windows --device -- --phone`

   Per eseguire emulatore predefinito:`cordova emulate windows -- --phone`

   Usare `cordova run windows --list -- --phone` per visualizzare tutte le destinazioni disponibili e `cordova run windows --target=<target_name> -- --phone` per eseguire l'applicazione specifico dispositivo o emulatore (ad esempio `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

####<a name="android"></a>Android:

   Per eseguire su dispositivo connesso:`cordova run android --device`

   Per eseguire emulatore predefinito:`cordova emulate android`

   __Nota__: verificare che creati istanza emulatore utilizzando *AVD Manager* come è illustrato nella sezione *Prerequisiti* .

   Usare `cordova run android --list` per visualizzare tutte le destinazioni disponibili e `cordova run android --target=<target_name>` per eseguire l'applicazione specifico dispositivo o emulatore (ad esempio `cordova run android --target="Nexus4_emulator"`).

####<a name="ios"></a>iOS:

   Per eseguire su dispositivo connesso:`cordova run ios --device`

   Per eseguire emulatore predefinito:`cordova emulate ios`

   __Nota__: assicurarsi di avere `ios-sim` pacchetto di installazione per eseguire emulatore. Per ulteriori informazioni vedere la sezione *Prerequisiti* .

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run application on specific device or emulator (for example,  `cordova run android --target="iPhone-6"`).

Usare `cordova run --help` vedere compilazione aggiuntive e l'esecuzione di opzioni.

Per riferimento, esempio completa (senza i valori di configurazione) verrà fornita [di seguito](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient).  È possibile procedere con a scenari più avanzati (ok e più interessanti).  È consigliabile provare:

[Proteggere un Web Node API con Azure Active Directory >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
