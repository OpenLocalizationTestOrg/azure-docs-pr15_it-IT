<properties
    pageTitle="Come abilitare tra app Single Sign-On in iOS utilizzando ADAL | Microsoft Azure"
    description="Come usare le caratteristiche di SDK ADAL per attivare Single Sign-On tra le applicazioni. "
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>


# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Come abilitare tra app Single Sign-On in iOS utilizzando ADAL


Grazie a Single Sign-On (SSO) in modo che gli utenti dovranno immettere una sola volta le proprie credenziali e disporre automaticamente di tali credenziali con cui collabora applicazioni a questo punto è previsto dai clienti. La difficoltà di immettere il nome utente e la password di piccole dimensioni, spesso volte combinati con un fattore aggiuntivo (2FA) come una chiamata telefonica o un codice texted, i risultati in rapido problema relativo se un utente ha eseguire questa operazione più volte per il prodotto.

Inoltre, se si utilizzano una piattaforma identità con altre applicazioni, ad esempio Accounts di Microsoft o un account di lavoro da Office 365, clienti si aspettano che quelle le credenziali per poter usare tutte le applicazioni indipendentemente dal fornitore.

La piattaforma Microsoft Identity, insieme a SDK nostro identità Microsoft, non tutto il lavoro già completato necessità e offre la possibilità di soddisfano i clienti con Single Sign-On nel proprio gruppo di applicazioni o, come con la funzionalità di gestore e le applicazioni di autenticatore, attraverso l'intera unità.

In questa procedura dettagliata illustra come configurare Windows SDK all'interno dell'applicazione per permettere ai clienti.

Questa procedura si applica a:

* Azure Active Directory
* B2C Azure Active Directory
* B2B Azure Active Directory
* Accesso condizionato Azure Active Directory


Si noti che il documento seguente presuppone si ha una conoscenza di come [le applicazioni di provisioning nel portale di legacy per Azure Active Directory](./develop/active-directory-how-to-integrate.md) , nonché integrati [Microsoft Identity iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc)dell'applicazione.

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Concetti di Single Sign-on della piattaforma Microsoft Identity

### <a name="microsoft-identity-brokers"></a>Microsoft Identity di connessione

Microsoft offre applicazioni per ogni piattaforma per dispositivi mobili che consentono di provvisorio delle credenziali per le applicazioni di diversi fornitori, nonché consente di caratteristiche avanzate speciali che richiedono una posizione centralizzata protetta da cui si desidera convalidare le credenziali. Si chiama questi **intermediari**. In iOS e Android possono essere tramite applicazioni scaricabili che i clienti installare in modo indipendente o è possono inserire il dispositivo da una società che gestisce alcuni o tutti del dispositivo per il dipendente. Questi intermediari supportano la protezione gestione solo per alcune applicazioni o l'intera unità in base a quali desiderano gli amministratori IT. In Windows questa funzionalità viene fornita da una selezione di account incorporati nel sistema operativo, noto tecnicamente come gestore di autenticazione Web.

Per capire come si usa questi intermediari e come i clienti potrebbero visualizzarle nel loro flusso di accesso per la piattaforma Microsoft Identity continuare a leggere per ulteriori informazioni.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Motivi per l'accesso su dispositivi mobili

Accesso alle credenziali nei dispositivi eseguire due schemi di base per la piattaforma Microsoft Identity seguenti:

* Account di accesso non gestore assistito
* Del broker assistito gli account di accesso

#### <a name="non-broker-assisted-logins"></a>Account di accesso non gestore assistito

Gli accessi assistiti gestore non sono esperienze di accesso che si verificano in linea con l'applicazione e utilizzano l'archiviazione locale nel dispositivo, per tale applicazione. Questo spazio di archiviazione può essere condivisi tra applicazioni, ma le credenziali sono strettamente associate all'app o famiglia di App tramite tale credenziali. Si tratta l'esperienza è probabile che si sono verificati in molte applicazioni per dispositivi mobili in cui è immettere un nome utente e password all'interno dell'applicazione stesso.

Questi account di accesso sono i seguenti vantaggi:

-  Esperienza utente esiste interamente all'interno dell'applicazione.
-  Credenziali possono essere condiviso tra le applicazioni che hanno eseguito l'accesso tramite lo stesso certificato, offrendo un'esperienza single sign-on per la famiglia di applicazioni.
-  Controllo intorno l'esperienza di accesso, è disponibile nell'applicazione prima e dopo l'accesso.

Questi account di accesso sono i seguenti svantaggi:

- Utente non è possibile riscontrare single-sign-on in tutte le applicazioni che utilizzano un Microsoft Identity solo tra tali Identities Microsoft applicazione proprietario e configurato.
- L'applicazione non può essere utilizzato con funzionalità di business più avanzate, ad esempio accesso condizionato o utilizzare la famiglia InTune dei prodotti.
- L'applicazione non supporta l'autenticazione basata su certificato per gli utenti aziendali.

Ecco una rappresentazione di funzionamento dei Microsoft identità SDK con lo spazio di archiviazione condivisa delle applicazioni per attivare Single Sign-on:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| Azure SDK  | | Azure SDK  |  | Azure SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Del broker assistito gli account di accesso

Esperienze di accesso che si verificano all'interno dell'applicazione gestore e utilizzano l'archiviazione e la sicurezza del gestore per condividere le credenziali per tutte le applicazioni nel dispositivo in grado di sfruttare la piattaforma Microsoft Identity sono assistito gestore account di accesso. Questo errore indica che le applicazioni dipenderà il gestore per eseguire l'accesso agli utenti. In iOS e Android possono essere tramite applicazioni scaricabili che i clienti installare in modo indipendente o è possono inserire il dispositivo da una società che gestisce il dispositivo per utente. Esempio di questo tipo di applicazione è l'applicazione di Azure autenticatore in iOS. In Windows questa funzionalità viene fornita da una selezione di account incorporati nel sistema operativo, noto tecnicamente come gestore di autenticazione Web.
L'esperienza varia a seconda della piattaforma e in alcuni casi può essere quanto per gli utenti se non vengono gestiti in modo corretto. Si è probabile che sia più familiarità con questo modello se si dispone dell'applicazione Facebook installato e utilizza la funzionalità di accesso di Facebook in un'altra applicazione. La piattaforma Microsoft Identity utilizza lo stesso modello.

Per iOS che questo conduce a una transizione"" animazione nel punto in cui l'applicazione viene inviato allo sfondo durante le applicazioni di Azure autenticatore viene fornito in primo piano per l'utente selezionare l'account che si desidera per l'accesso.  

Per Android e Windows selezione degli account viene visualizzato nella parte superiore dell'applicazione è meno interrompa l'utente.

#### <a name="how-the-broker-gets-invoked"></a>Come viene richiamato il gestore

Se un gestore compatibile è installato nel dispositivo, come nell'applicazione di Azure autenticatore, Microsoft identità SDK eseguirà automaticamente l'operazione della chiamata gestore dell'utente quando un utente indica che desiderano agli utenti di accedere con qualunque account dalla piattaforma Microsoft Identity. Può trattarsi di un Account Microsoft personale, un lavoro o account dell'istituto di istruzione, o un account che forniscono e host in Azure con prodotti B2C e B2B. Tramite la crittografia e algoritmi estremamente sicuro è assicurarsi che le credenziali sono frequenti per e recapitate all'applicazione in modo sicuro. I dettagli tecnici esatto di questi meccanismi non è stato pubblicato ma sono state sviluppate alle funzionalità di collaborazione Apple e Google.

**Lo sviluppatore ha la possibilità di se Microsoft identità SDK chiama il gestore o viene utilizzato il flusso assistito non gestore.** Tuttavia se lo sviluppatore sceglie di non usare il flusso relativo al gestore assistito perdono il vantaggio di sfruttare le credenziali di Single Sign-On che l'utente è già stato aggiunto in precedenza nel dispositivo, nonché impedisce loro applicazione in uso con caratteristiche di business che Microsoft offre suoi clienti, ad esempio accesso condizionato, funzionalità di gestione Intune, e l'autenticazione basata su certificati.

Questi account di accesso sono i seguenti vantaggi:

-  Utente esperienze Single Sign-on tutte le applicazioni indipendentemente dal fornitore.
-  L'applicazione è possibile sfruttare le funzionalità di business più avanzate, ad esempio accesso condizionato o utilizzare la famiglia InTune dei prodotti.
-  L'applicazione supportino autenticazione basata su certificato per gli utenti aziendali.
- Esperienza di accesso molto più sicura come identità dell'applicazione e all'utente vengono verificate dall'applicazione gestore con algoritmi rafforzare la sicurezza e la crittografia.

Questi account di accesso sono i seguenti svantaggi:

- In iOS l'utente passa dall'esperienza dell'applicazione mentre credenziali ne vengono scelti.
- Perdita della capacità di gestire l'esperienza di accesso per i clienti all'interno dell'applicazione.



Ecco una rappresentazione del funzionamento Microsoft identità SDK con le applicazioni di gestore per attivare Single Sign-on:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

Grazie a queste informazioni di base che dovrebbe essere possibile comprendere meglio e implementare SSO all'interno dell'applicazione con la piattaforma Microsoft Identity e SDK.


## <a name="enabling-cross-app-sso-using-adal"></a>Attivazione di Single Sign-on cross-app tramite ADAL

Di seguito si userà iOS ADAL SDK per:

- Attivare Single Sign-on assistito non gestore per il gruppo delle App
- Attivare il supporto per assistito gestore Single Sign-on

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Attivare Single Sign-on per non gestore assistito Single Sign-on

Per Single Sign-on assistito non gestore tra le applicazioni Microsoft identità SDK gestire la complessità di Single Sign-on dell'utente. Sono inclusi ricerca utente appropriate nella cache e gestire un elenco di utenti ha effettuato l'accesso per poter eseguire una query.

Per attivare Single Sign-on per le applicazioni che si è proprietari che è necessario eseguire le operazioni seguenti:

1. Assicurarsi che tutti gli utente applicazioni la stessa Client o applicazione ID.
* Assicurarsi che tutte le applicazioni condividere lo stesso certificato di firma presso Apple, in modo da poterla condividere portachiavi
* Richiedere lo stesso diritto portachiavi per ognuna delle applicazioni.
* Informare Microsoft identità SDK keychain condiviso che si desidera utilizzare.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Con lo stesso ID Client / applicazione ID per tutte le applicazioni di famiglia di prodotti delle App

Affinché la piattaforma Microsoft Identity fonti di informazioni che sono autorizzati a condividere i token delle applicazioni, ognuna delle applicazioni sarà necessario condividere la stessa Client o applicazione ID. Questo è l'identificatore univoco fornito all'utente al momento della registrazione prima applicazione nel portale.

Per comprendere come sarà possibile identificare varie App al servizio Microsoft Identity Se Usa lo stesso ID dell'applicazione. La risposta è con **URI reindirizzare la chiamata**. Ogni applicazione può avere più URI reindirizzare registrato nel portale di valutazione. Ogni app nella famiglia di prodotti avrà un diverso URI di reindirizzamento. Di seguito viene illustrato un esempio di questo aspetto:

Reindirizzamento App1 URI:`x-msauth-mytestiosapp://com.myapp.mytestapp`

Reindirizzamento App2 URI:`x-msauth-mytestiosapp://com.myapp.mytestapp2`

Reindirizzamento App3 URI:`x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Questi sono annidati sotto lo stesso ID client / ID applicazione e la ricerca in base alle URI mittente per ricevere nella configurazione SDK di reindirizzamento.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```


*Si noti che il formato degli URI reindirizzare vengono illustrate di seguito. È possibile utilizzare qualsiasi URI reindirizzare a meno che non si desidera supportare gestore, nel qual caso che deve essere simile le risposte sopra*



#### <a name="create-keychain-sharing-between-applications"></a>Creare portachiavi condividere tra le applicazioni

Attivazione di condivisione del keychain è lo scopo di questo documento e coperto da Apple loro il documento di [Aggiunta di funzionalità](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Che cos'è importante è decidere il portachiavi da chiamare desiderato e aggiungere tale funzionalità tra tutte le applicazioni.

Quando si dispone di diritti configurare correttamente è visualizzato un file nella directory del progetto diritto `entitlements.plist` che contiene un elemento che ha un aspetto simile al seguente:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Dopo avere il diritto di portachiavi abilitato in ognuna delle applicazioni e si è pronti a utilizzare Single Sign-on, informare Microsoft identità SDK portachiavi mediante l'impostazione seguente nel `ADAuthenticationSettings` con le impostazioni seguenti:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [AZURE.WARNING]
Quando si condividono un portachiavi tra le applicazioni qualsiasi applicazione di eliminare utenti o peggiore eliminare tutti i token in tutta l'applicazione. Questa operazione è particolarmente gravi se si dispone di applicazioni basate su token da sfondo lavoro. Condivisione di un portachiavi indica che è necessario prestare attenzione in tutte le rimuove operazioni tramite Microsoft identità SDK.

Questo è tutto! Microsoft identità SDK condividerà ora credenziali tra tutte le applicazioni. Nell'elenco degli utenti anche essere condiviso tra le istanze dell'applicazione.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Attivare Single Sign-on per gestore assistito Single Sign-on

La possibilità di un'applicazione di utilizzare qualsiasi gestore installato nel dispositivo è **disattivata per impostazione predefinita**. Per utilizzare l'applicazione con il gestore è necessario apportare alcune modifiche alla configurazione e aggiungere codice all'applicazione.

La procedura da seguire è:

1. Attivare la modalità gestore nella chiamata del codice applicazione per SDK MS.
2. Definire un nuovo URI di reindirizzamento e che forniscono l'app e la registrazione di app.
3. Registrazione di una combinazione di URL.
4. Supporto iOS9: aggiungere un'autorizzazione a un file info.plist.


#### <a name="step-1-enable-broker-mode-in-your-application"></a>Passaggio 1: Abilitare la modalità di gestore nell'applicazione
La possibilità di eseguire l'applicazione per utilizzare il gestore viene attivata quando si crea il "contesto" o l'installazione e configurazione iniziali dell'oggetto l'autenticazione. A procedere come segue per impostare il tipo di credenziali nel codice:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
Il `AD_CREDENTIALS_AUTO` impostazione consentirà Microsoft identità SDK tentare di chiamare il gestore `AD_CREDENTIALS_EMBEDDED` impedirà Microsoft identità SDK dalla chiamata per il gestore.

#### <a name="step-2-registering-a-url-scheme"></a>Passaggio 2: La registrazione di una combinazione di URL
La piattaforma Microsoft Identity vengono utilizzati URL per richiamare il gestore e quindi tornare controllo all'applicazione. Per completare tale andata e ritorno è necessario una combinazione di URL registrata per l'applicazione che verrà conoscere la piattaforma Microsoft Identity. Può trattarsi oltre alle altre combinazioni di app che potrebbe avere registrata in precedenza con l'applicazione.

> [AZURE.WARNING]
È consigliabile apportare la combinazione di URL abbastanza univoci per ridurre al minimo le possibilità di un'altra app usando la stessa combinazione di URL. Apple non garantisce l'univocità degli schemi di URL che sono registrate nell'app store.

Di seguito è illustrato un esempio di come viene visualizzata nel configurazione del progetto. È anche possibile eseguire questa operazione in XCode anche:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Passaggio 3: Creare un nuovo reindirizzamento URI con la combinazione di URL

Per assicurarsi che i token delle credenziali viene sempre restituito all'applicazione corretta, è necessario assicurarsi che è richiamare all'applicazione in modo che è possibile verificare il sistema operativo iOS. Il sistema operativo iOS report per le applicazioni di Microsoft gestore ID globale dell'applicazione chiamarlo. Non è possibile eseguire spoofing da un'applicazione di malintenzionati. Pertanto abbiamo sfruttare questa insieme URI dell'applicazione gestore per garantire la corretta applicazione vengono restituiti i token. È necessario stabilire il reindirizzamento URI sia univoco all'interno dell'applicazione e impostare come URI reindirizzare nel portale per sviluppatori.

Il reindirizzamento URI deve avere la forma corretta di:

`<app-scheme>://<your.bundle.id>`

ex: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Per specificare la registrazione di app tramite il [portale classica Azure](https://manage.windowsazure.com/)questo URI reindirizzare la chiamata. Per ulteriori informazioni sulla registrazione di app Azure Active Directory, vedere [integrazione con Azure Active Directory](./develop/active-directory-how-to-integrate.md).


##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Passaggio 3: aggiungere un reindirizzamento URI nel portale di app e dev per supportare l'autenticazione basata su certificato

Per supportare certificato di autenticazione che una seconda "msauth" deve essere registrato dell'applicazione e [Azure portale classico](https://manage.windowsazure.com/) per gestire l'autenticazione certificato se si desidera aggiungere che supportano nell'applicazione basata su.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

ex: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*


#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>Passaggio 4: iOS9: aggiungere un parametro di configurazione applicazione in uso.

ADAL utilizza – canOpenURL: per verificare se il gestore è installato nel dispositivo. In iOS Apple 9 bloccati quali combinazioni di un'applicazione possono per la query. È necessario aggiungere "msauth" nella sezione LSApplicationQueriesSchemes del `info.plist file`.

<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>

### <a name="youve-configured-sso"></a>È ora configurato Single Sign-on!

A questo punto Microsoft identità SDK automaticamente sia condividerà le credenziali tra le applicazioni e richiamare il gestore se è presenta sul dispositivo.
