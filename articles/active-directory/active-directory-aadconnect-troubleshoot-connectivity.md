<properties
    pageTitle="Azure AD Connect: Risolvere i problemi di connettività | Microsoft Azure"
    description="In questo articolo viene spiegato come risolvere i problemi di connettività con Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="billmath"/>

# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Risolvere i problemi di connettività con Azure AD Connect
In questo articolo viene descritto il funzionamento di connessione tra Azure AD Connect e Azure Active Directory e per la risoluzione dei problemi di connettività. Questi problemi sono più probabilmente in un ambiente con un server proxy.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Risoluzione dei problemi di connettività nell'installazione guidata
Azure AD Connect utilizza l'autenticazione moderno (usando la raccolta ADAL) per l'autenticazione. L'installazione guidata e il motore di sincronizzazione corretto richiedono Machine. config per essere configurati correttamente poiché si tratta di applicazioni .NET.

In questo articolo si illustra come Fabrikam si connette a Azure Active Directory mediante il proxy. Il server proxy denominato fabrikamproxy e utilizza la porta 8080.

Prima di tutto è necessario assicurarsi che sia configurato correttamente [**Machine. config**](active-directory-aadconnect-prerequisites.md#connectivity) .  
![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

>[AZURE.NOTE]
In alcuni blog di Microsoft non descritte che deve essere modificata miiserver.exe.config invece. Tuttavia, il file è sovrascritto nel ogni aggiornamento pertanto anche se funziona durante l'installazione iniziale, il sistema si arresta nel primo aggiornamento. Per questo motivo il suggerimento consiste nell'aggiornare invece Machine. config.

Il server proxy deve inoltre includere gli URL richiesti aperti. L'elenco ufficiale descritte in [Office 365 URL e intervalli di indirizzi IP ](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Le opzioni nella tabella seguente è il livello minimo assoluto in grado di connettersi tutti Azure Active Directory. In questo elenco non include le funzionalità facoltative, ad esempio password writeback o Azure Active Directory connettersi integrità. È descritta di seguito per facilitare la risoluzione dei problemi per la configurazione iniziale.

URL | Porta | Descrizione
---- | ---- | ----
mscrl.microsoft.com | HTTP/80 | Consente di scaricare gli elenchi CRL.
\*. verisign.com | HTTP/80 | Consente di scaricare gli elenchi CRL.
\*. entrust.com | HTTP/80 | Consente di scaricare gli elenchi CRL per MFA.
\*. windows.net | HTTPS/443 | Consente di accedere a Azure Active Directory.
Secure.aadcdn.microsoftonline p.com | HTTPS/443 | Usato per MFA.
\*. microsoftonline.com | HTTPS/443 | Consente di configurare la directory di Azure Active Directory e importazione/esportazione dati.

## <a name="errors-in-the-wizard"></a>Errori della procedura guidata
L'installazione guidata utilizza due diversi contesti di protezione. Nella pagina **Connetti a Azure Active Directory** utilizza attualmente l'accesso all'utente. Nella pagina **Configura** in fase di modifica all' [account di servizio per il motore di sincronizzazione](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts). Le configurazioni proxy si sono globali al computer se si verifica un problema, il problema sarà più probabilmente già visualizzato nella pagina di **connettersi a Active Directory Azure** della procedura guidata.

Questi sono gli errori più comuni che verrà visualizzato nell'installazione guidata.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>L'installazione guidata non è stato configurato correttamente
Verrà visualizzato questo errore quando la procedura guidata non riesce a raggiungere il proxy.
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

- Se viene visualizzato, verificare che il [file Machine. config](active-directory-aadconnect-prerequisites.md#connectivity) sia stato configurato correttamente.
- Se che sono corrette, seguire la procedura descritta in [connettività proxy per verificare](#verify-proxy-connectivity) se il problema è presenta anche la creazione guidata.

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Non è possibile raggiungere l'endpoint MFA
Se non è possibile raggiungere l' endpoint **https://secure.aadcdn.microsoftonline-p.com** e l'amministratore globale ha MFA abilitato, viene visualizzato questo errore.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

- Se viene visualizzato, verificare che l'endpoint secure.aadcdn.microsoftonline-p.com è stato aggiunto al proxy di.

### <a name="the-password-cannot-be-verified"></a>Non è possibile verificare la password
Se l'installazione guidata è corretta per la connessione a Azure Active Directory, ma non è possibile verificare la password verrà visualizzato: ![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

- Contiene la password una password temporanea e devono essere modificati? È stata effettivamente la password corretta? Provare a effettuare l'accesso https://login.microsoftonline.com (in un altro computer rispetto al server Azure AD Connect) e verificare che l'account è utilizzabile.

### <a name="verify-proxy-connectivity"></a>Verificare la connettività proxy
Per verificare se il server Azure AD Connect include anche la connettività effettiva con il Proxy e Internet alcune PowerShell verrà usata per verificare se il proxy consente richieste web o meno. Prompt dei comandi di PowerShell eseguire `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Tecnicamente la prima chiamata consiste nel https://login.microsoftonline.com e questo metodo funziona anche ma altri URI è più veloce per rispondere.)

PowerShell utilizzerà la configurazione in Machine. config per contattare il proxy. Le impostazioni in winhttp/netsh non devono avere un impatto questi cmdlet.

Se il proxy è configurato correttamente, è necessario ottenere uno stato successo: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Se si riceve **Impossibile connettersi al server remoto** quindi PowerShell tenta di effettuare una chiamata diretta senza mediante il proxy o il DNS non è configurato correttamente. Verificare che il file **Machine. config** sia configurato correttamente.
![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Se il proxy non è configurato correttamente, si verificherà un errore: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png)
![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

Errore | Testo dell'errore | Commento
---- | ---- | ---- |
403 | Accesso negato | Non è stato aperto il proxy per l'URL richiesto. Rivedere la configurazione del proxy e assicurarsi che gli [URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) sono stati aperti.
407 | Autenticazione del proxy richiesta | Il server proxy richiesta di accesso e non è stato fornito. Se il server proxy richiede l'autenticazione, assicurarsi di avere questo configurato in Machine. config. Verificare anche che si utilizza gli account di dominio per l'utente che esegue la procedura guidata per l'account di servizio.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Il modello di comunicazione tra Azure AD Connect e Azure Active Directory
Se sono state eseguite correttamente questa procedura precedente e ancora non riesce a connettersi è possibile iniziare a questo punto esamina registri di rete. In questa sezione è documentando un motivo di connettività normale ed esito negativo. Inoltre è voce herrings rosso comuni che possono essere ignorati se si siano leggendo i registri di rete.

- Si verificherà chiamate a https://dc.services.visualstudio.com. Non è necessario questo aperta in proxy per l'installazione venga eseguita correttamente e possono essere ignorati.
- Si noterà che la risoluzione dns verrà elencati gli host effettivi per il nsatc.net spazio nome DNS e altri spazi dei nomi non in microsoftonline.com. Tuttavia, non verrà eventuali richieste di servizio web ai nomi di server effettivo e non è necessario aggiungerli al proxy.
- Il adminwebservice endpoint provisioningapi (vedere di seguito nei log) endpoint individuazione e utilizzato per trovare l'endpoint effettivo da usare e sarà diversa a seconda del paese.

### <a name="reference-proxy-logs"></a>Guida di riferimento proxy registri
Ecco un'immagine da un log proxy effettivo e pagina della procedura guidata installazione nel punto in cui è stata accettata (sono state rimosse le voci duplicate allo stesso endpoint). Può essere utilizzata come riferimento per file di log proxy e di rete. Endpoint effettivo potrebbero essere diversi, nell'ambiente (in particolare quelli in *corsivo*).

**Connettersi a Azure Active Directory**

Ora | URL
--- | ---
1/11/2016 8:31 | Connect://Login.microsoftonline.com:443
1/11/2016 8:31 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:32 | connettersi: / /*bba800 ancoraggio*. microsoftonline.com:443
1/11/2016 8:32 | Connect://Login.microsoftonline.com:443
1/11/2016 8:33 | Connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:33 | connettersi: / /*inoltro bwsc02*. microsoftonline.com:443

**Configurare**

Ora | URL
--- | ---
1/11/2016 8:43 | Connect://Login.microsoftonline.com:443
1/11/2016 8:43 | connettersi: / /*bba800 ancoraggio*. microsoftonline.com:443
1/11/2016 8:43 | Connect://Login.microsoftonline.com:443
1/11/2016 8:44 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | connettersi: / /*bba900 ancoraggio*. microsoftonline.com:443
1/11/2016 8:44 | Connect://Login.microsoftonline.com:443
1/11/2016 8:44 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | connettersi: / /*bba800 ancoraggio*. microsoftonline.com:443
1/11/2016 8:44 | Connect://Login.microsoftonline.com:443
1/11/2016 8:46 | Connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:46 | connettersi: / /*inoltro bwsc02*. microsoftonline.com:443

**Sincronizzazione iniziale**

Ora | URL
--- | ---
1/11/2016 8:48 | Connect://Login.Windows.NET:443
1/11/2016 8:49 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:49 | connettersi: / /*bba900 ancoraggio*. microsoftonline.com:443
1/11/2016 8:49 | connettersi: / /*bba800 ancoraggio*. microsoftonline.com:443

## <a name="authentication-errors"></a>Errori di autenticazione
In questa sezione vengono descritti gli errori che possono essere restituiti da ADAL (la libreria di autenticazione utilizzata da Azure AD Connect) e PowerShell. L'errore spiegato deve comprendere nei passaggi successivi.

### <a name="invalid-grant"></a>Concedere il non valido
Nome utente non valido o la password. Per ulteriori informazioni, vedere [non è possibile verificare la password](#the-password-cannot-be-verified) .

### <a name="unknown-user-type"></a>Tipo di utente sconosciuto
Directory di Azure Active Directory non è possibile rilevate o risolti. Forse si tenta di accedere con un nome utente in un dominio non verificato?

### <a name="user-realm-discovery-failed"></a>Individuazione dell'area di autenticazione utente non è riuscito
Problemi di configurazione di rete o del proxy. La rete non può essere raggiunto, vedere [risolvere i problemi di connettività nell'installazione guidata](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Password utente scaduto
Le credenziali scadute. Cambiare la password.

### <a name="authorizationfailure"></a>AuthorizationFailure
Errore sconosciuto.

### <a name="authentication-cancelled"></a>Autenticazione annullata
È stata annullata la richiesta di autenticazione a più fattori (MFA).

### <a name="connecttomsonline"></a>ConnectToMSOnline
Autenticazione ha avuto esito positivo, ma PowerShell di Azure Active Directory ha un problema di autenticazione.

### <a name="azurerolemissing"></a>AzureRoleMissing
Autenticazione è stata completata. Non si è un amministratore globale.

### <a name="privilegedidentitymanagement"></a>PrivilegedIdentityManagement
Autenticazione è stata completata. Gestione delle identità privilegi è stata attivata e attualmente non è un amministratore globale. Per ulteriori informazioni, vedere [Privilegi di gestione delle identità](active-directory-privileged-identity-management-getting-started.md) .

### <a name="companyinfounavailable"></a>CompanyInfoUnavailable
Autenticazione è stata completata. Impossibile recuperare informazioni sulla società da Azure Active Directory.

### <a name="retrievedomains"></a>RetrieveDomains
Autenticazione è stata completata. Impossibile recuperare le informazioni sul dominio da Azure Active Directory.

## <a name="troubleshooting-steps-for-previous-releases"></a>Procedure di risoluzione dei problemi per le versioni precedenti.
Con le versioni a partire dal numero di build 1.1.105.0 (rilasciato febbraio 2016) è stata ritirata Assistente per l'accesso. In questa sezione e la configurazione di non essere più necessari, ma viene conservata come riferimento.

Per il single sign-Assistente per l'uso, è necessario configurare winhttp. Questa operazione può essere eseguita con [**netsh**](active-directory-aadconnect-prerequisites.md#connectivity).  
![Netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>Assistente per l'accesso non è stato configurato correttamente
Questo messaggio di errore visualizzato quando l'Assistente di accesso non riesce a raggiungere il proxy o il proxy non consente la richiesta.
![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

- Se viene visualizzato, osservare la configurazione del proxy in [netsh](active-directory-aadconnect-prerequisites.md#connectivity) e verificare che sia corretto.
![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
- Se che sono corrette, seguire la procedura descritta in [connettività proxy per verificare](#verify-proxy-connectivity) se il problema è presenta anche la creazione guidata.

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
