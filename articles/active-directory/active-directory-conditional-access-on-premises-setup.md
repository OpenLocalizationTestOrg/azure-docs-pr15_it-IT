<properties
    pageTitle="Impostazione dell'accesso condizionale locali con Azure Active Directory dispositivo registrazione | Microsoft Azure"
    description="Una Guida dettagliata per consentire l'accesso condizionale alle applicazioni locali con Active Directory Federation Services (ADFS) in Windows Server 2012 R2."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>


# <a name="setting-up-on-premises-conditional-access-using-azure-active-directory-device-registration"></a>Impostazione dell'accesso condizionale locali con Azure Active Directory dispositivo registrazione

Personalmente proprietà dispositivi gli utenti possono essere contrassegnati noti per l'organizzazione richiedendo agli utenti di join luogo di lavoro ai dispositivi per il servizio di Azure Active Directory dispositivo registrazione. Di seguito è una Guida dettagliata per consentire l'accesso condizionale alle applicazioni locali con Active Directory Federation Services (ADFS) in Windows Server 2012 R2.

> [AZURE.NOTE]
> Licenza di Office 365 o la licenza corrente di Azure Active Directory Premium è necessario quando si utilizzano dispositivi registrato in Criteri di accesso condizionato del servizio di registrazione di Azure Active Directory dispositivo. Sono inclusi dai criteri applicati da Active Directory Federation Services (ADFS) alle risorse locale.

Per ulteriori informazioni sugli scenari di accesso condizionato per locale, vedere [partecipare alla rete aziendale da qualsiasi dispositivo per Single Sign-on e continua secondo fattore di autenticazione tra applicazioni aziendali](https://technet.microsoft.com/library/dn280945.aspx).

Queste funzionalità sono disponibili per i clienti che acquistare una licenza di Azure Active Directory Premium.

<a name="supported-devices"></a>Dispositivi supportati
-------------------------------------------------------------------------
* Dispositivi di dominio di Windows 7.
* Windows 8.1 personale e il dominio il join di dispositivi.
* iOS 6 e versioni successive, per browser Safari
* Android 4.0 o versione successiva, Samsung GS3 o sopra i telefoni, Samsung Note2 o sopra Tablet.


<a name="scenario-prerequisites"></a>Prerequisiti di uno scenario
------------------------------------------------------------------------
* Abbonamento a Office 365 o Premium Azure Active Directory
* Un tenant di Azure Active Directory
* Windows Server Active Directory (Windows Server 2008 o versione successiva)
* Schema aggiornato in Windows Server 2012 R2
* Licenza per Premium Azure Active Directory
* Windows Server 2012 R2 Federation Services, configurato per Single Sign-on di Azure Active Directory
* Windows Server 2012 R2 Web applicazione Proxy Microsoft Azure Active Directory connettersi (Azure AD Connect). [Scaricare Azure AD Connect qui](http://www.microsoft.com/en-us/download/details.aspx?id=47594).
* Dominio verificato.

<a name="known-issues-in-this-release"></a>Problemi noti di questa versione
-------------------------------------------------------------------------------
* I criteri di accesso condizionato dispositivo basato su richiedono dispositivo oggetto writeback di Active Directory da Azure Active Directory. Può richiedere fino a 3 ore per gli oggetti dispositivo sia indietro scritte in Active Directory
* i dispositivi iOS 7 sempre richiede all'utente di selezionare un certificato durante l'autenticazione certificato client.
* Alcune versioni di iOS8, prima di iOS 8.3 non funzionano.

## <a name="scenario-assumptions"></a>Ipotesi di uno scenario
Questo scenario presuppone che si disponga di un ambiente ibrido costituita da un tenant di Azure Active Directory e un ambiente Active Directory locale. Questi tenant deve essere connesso tramite Azure AD Connect e con un dominio verificato e ADFS per Single Sign-on. L'elenco di controllo riportata di seguito consentono di configurare l'ambiente per il passaggio descritto in precedenza.

<a name="checklist-prerequisites-for-conditional-access-scenario"></a>Elenco di controllo: I prerequisiti per gli scenari di accesso condizionato
--------------------------------------------------------------
Connettere il tenant di Azure Active Directory con Active Directory locale.

## <a name="configure-azure-active-directory-device-registration-service"></a>Configurare il servizio di registrazione dispositivi di Azure Active Directory
Usare questa guida per distribuire e configurare il servizio di Azure Active Directory dispositivo registrazione per l'organizzazione.

Questa guida presuppone che sia stato configurato Windows Server Active Directory e ha un abbonamento a Microsoft Azure Active Directory. Vedere prerequisiti sopra.

Per distribuire la servizio di registrazione di Azure Active Directory dispositivo con il tenant di Azure Active Directory, completare le attività nell'elenco di controllo seguente, nell'ordine. Quando un collegamento di riferimento consente di accedere a un argomento concettuale, tornare all'elenco di controllo dopo avere esaminato l'argomento concettuale in modo da poter continuare con le attività rimanenti in questo elenco di controllo. Alcune attività includerà un passaggio di convalida scenario che consentono di verificare che il passaggio è stato completata.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>Parte 1: Attiva registrazione periferica Azure Active Directory

Seguire l'elenco di controllo seguente per abilitare e configurare il servizio di registrazione Azure Active Directory dispositivi.

| Attività                                                                                                                                                                                                                                                                                                                                                                                             | Guida di riferimento                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Attivare la registrazione di dispositivo nel tenant di Azure Active Directory per consentire ai dispositivi partecipare sul luogo di lavoro. Per impostazione predefinita, l'autenticazione a più fattori non è abilitato per il servizio. Tuttavia, è consigliabile l'autenticazione a più fattori durante la registrazione di un dispositivo. Prima di attivare l'autenticazione a più fattori in ADRS, assicurarsi che ADFS sia configurato per un provider di autenticazione a più fattori. | [Attivare la registrazione di dispositivo Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)               |
| Dispositivi scopriranno il servizio di registrazione Azure Active Directory dispositivo riconoscibili noto record DNS. È necessario configurare la propria azienda DNS in modo che i dispositivi di individuare il servizio di registrazione Azure Active Directory dispositivo.                                                                                                                                                   | [Configurare Individuazione di registrazione di Azure Active Directory dispositivo.](active-directory-conditional-access-device-registration-overview.md) |

##<a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>Parte 2: Distribuire e configurare Windows Server 2012 R2 Active Directory Federation Services e stabilire una relazione federazione con Azure Active Directory

| Attività                                                                                                                                                                                                                                                                                                                                                                                             | Guida di riferimento                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Distribuire il dominio di servizi di dominio Active Directory con le estensioni dello schema di Windows Server 2012 R2. Non è necessario aggiornare un controller di dominio a Windows Server 2012 R2. L'aggiornamento dello schema è l'unico requisito. | [Aggiornare lo Schema di servizi di dominio Active Directory](#upgrade-your-active-directory-domain-services-schema)               |
| Dispositivi scopriranno il servizio di registrazione Azure Active Directory dispositivo riconoscibili noto record DNS. È necessario configurare la propria azienda DNS in modo che i dispositivi di individuare il servizio di registrazione Azure Active Directory dispositivo.                                                                                                                                                   | [Preparare i dispositivi di supporto di Active Directory](#prepare-your-active-directory-to-support-devices) |


##<a name="part-3-enable-device-writeback-in-azure-ad"></a>Parte 3: Abilita dispositivo writeback in Azure Active Directory

| Attività                                                                                                                                                                                                                                                                                                                                                                                             | Guida di riferimento                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Completare parte 2 di attivazione dispositivo writeback di Azure AD Connect. Al termine, tornare questo questa Guida. | [Attivazione writeback di dispositivo in Azure AD Connect](#upgrade-your-active-directory-domain-services-schema)               |


##<a name="optional-part-4-enable-multi-factor-authentication"></a>[Facoltativo] Parte 4: Abilitare l'autenticazione a più fattori

Si consiglia di configurare una delle opzioni disponibili per l'autenticazione a più fattori. Se si vuole richiedere MFA, vedere [scelta della soluzione di protezione a più fattori dell'utente](../multi-factor-authentication/multi-factor-authentication-get-started.md). Include una descrizione di ogni soluzione e collegamenti che consentono di configurare la soluzione di propria scelta.

## <a name="part-5-verification"></a>Parte 5: verifica

La distribuzione è stata completata. A questo punto è possibile provare alcuni scenari. Seguire i collegamenti seguenti per esercitarsi con il servizio e acquisire familiarità con le caratteristiche


| Attività                                                                                                                                                                                                                         | Guida di riferimento                                                                       |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| Aggiungere alcuni dispositivi alla rete aziendale con Azure Active Directory dispositivo registrazione. È possibile partecipare, Windows, dispositivi iOS e Android                                                                                         | [Partecipare a dispositivi alla rete aziendale con Azure Active Directory dispositivo registrazione](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Consente di visualizzare e abilitare/disabilitare dispositivi registrati tramite il portale di amministrazione. Visualizzare alcuni dispositivi registrati tramite il portale di amministrazione.                                                        | [Panoramica di registrazione di Azure Active Directory dispositivo](active-directory-conditional-access-device-registration-overview.md)                             |
| Verificare che gli oggetti dispositivo vengono scritte da Azure Active Directory per Windows Server Active Directory.                                                                                                                  | [Verificare che dispositivi registrati siano scritti indietro in Active Directory](#verify-registered-devices-are-written-back-to-active-directory)                  |
| Ora che gli utenti possono registrare i dispositivi, è possibile creare l'applicazione di criteri di accesso in ADFS che consentono solo registrati dispositivi. In questa attività che è necessario creare una regola di accesso dell'applicazione e personalizzato accesso negato messaggio | [Creare un criterio di accesso dell'applicazione e personalizzato messaggio di accesso negato](#create-an-application-access-policy-and-custom-access-denied-message)            |



## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Integrare Azure Active Directory con Active Directory locale
Sarà quindi possibile integrare il tenant di Azure Active Directory di active directory locale, con Azure AD Connect. Anche se la procedura è disponibile nel portale di classica Azure, prendere nota delle istruzioni speciali elencato in questa sezione.

1.  Accedere al portale classico Azure usando un account che sia un amministratore globale in Azure Active Directory.
2.  Nel riquadro sinistro selezionare **Active Directory**.
3.  Nella scheda **Directory** selezionare directory.
4.  Selezionare la scheda **Integrazione della Directory** .
5.  Nella sezione **distribuzione e la gestione** , seguire i passaggi da 1 a 3 per integrare Azure Active Directory con la directory locale.
  1.    Aggiungere domini.
  2.    Installare ed eseguire Azure AD Connect: installare Azure AD Connect utilizzando le seguenti istruzioni di [installazione personalizzata di Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md).
  3. Verificare e gestire la sincronizzazione della directory. Single sign-on istruzioni sono disponibili all'interno di questo passaggio.

  > [AZURE.NOTE]
  > Configurare la federazione con ADFS come descritto nel documento in precedenza. Non è necessario configurare le funzionalità di anteprima.


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Aggiornare lo schema di servizi di dominio Active Directory

> [AZURE.NOTE]
> Aggiornamento dello schema di Active Directory non può essere annullato. È consigliabile innanzitutto eseguire questa operazione in un ambiente di testing.

1. Accedere a controller di dominio con un account che dispone dei diritti di amministratore dell'organizzazione e amministratore dello Schema.
2. Copiare la directory **\support\adprep [media]** e sottodirectory a uno dei controller di dominio Active Directory.
3. Dove [media] è il percorso per il supporto di installazione di Windows Server 2012 R2.
4. Dal prompt dei comandi, passare alla directory adprep ed eseguire: **/forestprep adprep.exe**. Seguire le istruzioni visualizzate per completare l'aggiornamento dello schema.

## <a name="prepare-your-active-directory-to-support-devices"></a>Preparare l'ambiente di Active Directory per dispositivi

>[AZURE.NOTE] Si tratta di un'operazione che si che è necessario eseguire per preparare la foresta di Active Directory per supportare i dispositivi. È necessario essere connessi con autorizzazioni di amministratore dell'organizzazione e le strutture di Active Directory devono avere lo schema di Windows Server 2012 R2 per completare questa procedura.


##<a name="prepare-your-active-directory-forest-to-support-devices"></a>Preparare la foresta di Active Directory per dispositivi

> [AZURE.NOTE]
>Si tratta di un'operazione che si che è necessario eseguire per preparare la foresta di Active Directory per supportare i dispositivi. È necessario essere connessi con autorizzazioni di amministratore dell'organizzazione e le strutture di Active Directory devono avere lo schema di Windows Server 2012 R2 per completare questa procedura.

### <a name="prepare-your-active-directory-forest"></a>Preparare la foresta Active Directory

1.  Nel server federativo, aprire una finestra di comando di Windows PowerShell e digitare: inizializzazione ADDeviceRegistration
2.  Quando viene richiesto **ServiceAccountName**, immettere il nome dell'account del servizio che è stato selezionato come l'account di servizio per AD FS. Se si tratta di un account gMSA, immettere l'account in formato **dominio\nomeaccount$** . Per un account di dominio, usare il formato **dominio\nomeaccount**.



### <a name="enable-device-authentication-in-ad-fs"></a>Abilitare l'autenticazione dei dispositivi in ADFS

1. Nel server federativo, aprire la console di gestione di ADFS e spostarsi **ADFS** > **Criteri di autenticazione**.
2. Selezionare **Modifica autenticazione principale globale...** nel riquadro **Azioni** .
3. Selezionare **Abilita l'autenticazione dei dispositivi** , quindi scegliere**OK**.
4. Per impostazione predefinita, ADFS rimuoverà periodicamente dispositivi non utilizzati da Active Directory. Quando si utilizza la registrazione di Azure Active Directory dispositivo in modo che i dispositivi possono essere gestiti in Azure, è necessario disattivare questa operazione.


### <a name="disable-unused-device-cleanup"></a>Disabilitare pulizia dispositivo non utilizzati
1. Nel server federativo, aprire una finestra di comando di Windows PowerShell e digitare: Set-AdfsDeviceRegistration - MaximumInactiveDays 0

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Preparare l'ambiente di Azure AD Connect per writeback di dispositivo

1.  La parte 1: Preparare Azure AD connettersi.


## <a name="join-devices-to-your-workplace-using-azure-active-directory-device-registration"></a>Partecipare a dispositivi alla rete aziendale con Azure Active Directory dispositivo registrazione

### <a name="join-an-ios-device-using-azure-active-directory-device-registration"></a>Partecipare a un dispositivo iOS con Azure Active Directory dispositivo registrazione

Azure registrazione di Active Directory dispositivo viene utilizzato il processo di registrazione Over-the-Air profilo per i dispositivi iOS. Questo processo inizia con l'utente che si connette all'URL di registrazione profilo utilizzando il web browser Safari. Il formato di URL è come indicato di seguito:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Dove `yourdomainname` è il nome di dominio configurati con Azure Active Directory. Ad esempio, se il nome di dominio è contoso.com, l'URL è:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Esistono diversi modi per comunicare questo URL per gli utenti. È uno consigliabile per pubblicare questo URL in un'applicazione personalizzata accesso negato messaggio in ADFS. Questo argomento viene trattato nella sezione successiva: [Crea un criterio di accesso dell'applicazione e personalizzato messaggio di accesso negato](#create-an-application-access-policy-and-custom-access-denied-message).

###<a name="join-a-windows-81-device-using-azure-active-directory-device-registration"></a>Partecipare a un dispositivo Windows 8.1 con Azure Active Directory dispositivo registrazione

1. In un dispositivo Windows 8.1, passare a **Impostazioni PC** > **rete** > **luogo di lavoro**.
2. Immettere il nome utente nel formato UPN. Ad esempio dan@contoso.com..
3. Selezionare **Partecipa**.
4. Quando richiesto, effettuare l'accesso con le credenziali. A questo punto viene aggiunto il dispositivo.

###<a name="join-a-windows-7-device-using-azure-active-directory-device-registration"></a>Partecipare a un dispositivo Windows 7 con Azure Active Directory dispositivo registrazione
Per registrare il dominio di Windows 7 il join di dispositivi, è necessario distribuire il pacchetto di software di registrazione di dispositivo. Il pacchetto di software è denominato luogo di lavoro partecipare per Windows 7 ed è disponibile per il download all'indirizzo [sito Web Microsoft Connect](https://connect.microsoft.com/site1164). Istruzioni sull'uso del pacchetto sono disponibili al [Configura registrazione dispositivo automatico per il dominio di Windows 7 aggiungono dispositivi](active-directory-conditional-access-automatic-device-registration-windows7.md).

### <a name="join-an-android-device-using-azure-active-directory-device-registration"></a>Partecipare a un dispositivo Android con Azure Active Directory dispositivo registrazione

[Azure autenticatore per l'argomento Android](active-directory-conditional-access-azure-authenticator-app.md) contiene istruzioni su come installare autenticatore Azure app nel dispositivo Android e aggiungere un account di lavoro. Quando viene creato un account di lavoro viene eseguito correttamente a un dispositivo Android, tale dispositivo è luogo di lavoro eseguito il join per l'organizzazione.

## <a name="verify-registered-devices-are-written-back-to-active-directory"></a>Verificare che dispositivi registrati siano scritti indietro in Active Directory
È possibile visualizzare e verificare che gli oggetti dispositivo sono stati scritti indietro di Active Directory con LDP.exe o Modifica ADSI. Entrambi sono disponibili con gli strumenti di amministratore di Active Directory.

Per impostazione predefinita, verranno inseriti oggetti dispositivo indietro scritto da Azure Active Directory nello stesso dominio farm di ADFS.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Creare un criterio di accesso dell'applicazione e personalizzato messaggio di accesso negato
Considerare il seguente scenario: creare un'applicazione di protezione di terze parti Relying in ADFS e configurare una regola di autorizzazione del rilascio che consente solo i dispositivi registrati. A questo punto sono consentiti solo i dispositivi registrati per accedere all'applicazione. Per rendere più semplice per gli utenti di accedere all'applicazione, configurare un personalizzato messaggio di accesso negato che include istruzioni su come partecipare a un dispositivo. A questo punto gli utenti hanno un modo semplice per registrare i dispositivi per accedere a un'applicazione.

La procedura seguente illustra come implementare questo scenario.

>[AZURE.NOTE]
In questa sezione presuppone che sia stato già configurato basarsi festa attendibile per l'applicazione in ADFS.

1. Aprire lo strumento AD FS MMC e spostarsi ADFS > relazioni di Trust > attendibilità componente.
2. Individuare l'applicazione per il quale verrà applicata la nuova regola di accesso. Il pulsante destro dell'applicazione e selezionare Modifica regole attestazione...
3. Selezionare la scheda **Regole di autorizzazione del rilascio** e quindi selezionare **Aggiungi regola**
4. Selezionare la **regola Richiedi** modello elenco a discesa, **consentire o impedire agli utenti in base a un'attestazione in ingresso**. Selezionare **Avanti**.
5. Nel nome della regola attestazione: tipo di campo: **consentire l'accesso da dispositivi registrati**
6. Dalla posta in arrivo tipo attestazione: nell'elenco a discesa, selezionare **È stato registrato utente**.
7. Nella posta in arrivo richiedere valore: campo, digitare: **vero**
8. Selezionare il pulsante di opzione **consentire l'accesso agli utenti con la richiesta in arrivo** .
9. Selezionare **Fine** e quindi selezionare **Applica**.
10. Rimuovere tutte le regole che vengono valore più restrittiva rispetto alla regola che appena creata. Ad esempio, rimuovere la regola di **Consentire l'accesso a tutti gli utenti** predefinita.

L'applicazione è ora configurato per consentire l'accesso solo quando l'utente è provenienti da un dispositivo che hanno registrato e unito sul luogo di lavoro. Per criteri di accesso più avanzata, vedere [Gestione dei rischi con il controllo dell'accesso a più fattori](https://technet.microsoft.com/library/dn280949.aspx).

Punto, configurare un messaggio di errore personalizzato per l'applicazione. Il messaggio di errore verrà informare gli utenti che devono accedono il dispositivo per il luogo di lavoro prima di poter accedere all'applicazione. È possibile creare un'applicazione personalizzata messaggio di accesso negato usando Windows PowerShell e HTML personalizzate.

Nel server federativo, aprire una finestra di comando di Windows PowerShell e digitare il comando seguente. Sostituire parti del comando con gli elementi specifici al sistema di:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
È necessario registrare il dispositivo poter accedere a questa applicazione.

**Se si utilizza un dispositivo iOS, selezionare questo collegamento per partecipare a un dispositivo**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Partecipare a questo dispositivo iOS alla rete aziendale.


**Se si utilizza un dispositivo Windows 8.1**, è possibile partecipare a un dispositivo facendo clic su **Impostazioni PC ** >  **rete** > **luogo di lavoro**.


Dove "**basarsi nome protezione parte**" è il nome dell'oggetto che si basa protezione di terze parti applicazioni in ADFS.
Dove **yourdomain.com** è il nome di dominio configurati con Azure Active Directory. Ad esempio contoso.com.
Assicurarsi di rimuovere il contenuto html che è passare il cmdlet **Set-AdfsRelyingPartyWebContent** le interruzioni di riga (se presente).


A questo punto quando gli utenti accedono applicazione da un dispositivo che non è registrato con il servizio di registrazione Azure Active Directory dispositivo, riceveranno una pagina in cui sarà simile alla schermata di seguito.

![Screeshot di un errore quando gli utenti non hanno registrato il dispositivo con Azure Active Directory](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)

##<a name="related-articles"></a>Articoli correlati

- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
