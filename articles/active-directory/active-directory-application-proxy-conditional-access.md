<properties
    pageTitle="Accesso condizionato per le applicazioni pubblicate con Proxy di applicazione di Azure Active Directory"
    description="Descrive come configurare l'accesso condizionale Applications che pubblica per l'accesso in modalità remota utilizzando Proxy di applicazione Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="working-with-conditional-access"></a>Utilizzo di access condizionale

È possibile configurare le regole di accesso per concedere l'accesso condizionale alle applicazioni pubblicate tramite Proxy dell'applicazione. In questo modo è possibile:

- Richiede l'autenticazione a più fattori per ogni applicazione
- Richiede l'autenticazione a più fattori solo se gli utenti non al lavoro
- Impedire agli utenti l'accesso all'applicazione quando non sono in ufficio

Le regole possono essere applicate a tutti gli utenti e gruppi o solo a determinati utenti e gruppi. Per impostazione predefinita verrà applicata la regola a tutti gli utenti che hanno accesso all'applicazione. Tuttavia è possibile limitare la regola anche agli utenti che appartengono a gruppi di sicurezza specificato.  

Quando un utente accede a un'applicazione federata che utilizza OAuth 2.0, OpenID connettersi, SAML o WS-Federation valutazione delle regole di accesso. Inoltre, le regole di accesso vengono valutate con OAuth 2.0 e connettere OpenID quando un token di aggiornamento consente di acquisire un token di accesso.

## <a name="conditional-access-prerequisites"></a>Prerequisiti di accesso condizionato

- Sottoscrizione a Premium Azure Active Directory
- Un tenant di Azure Active Directory federato o gestito
- Tenant federati richiedono l'autenticazione a più fattori (MFA) sia attivato  
    ![Configurare regole di accesso - richiedono l'autenticazione a più fattori](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## <a name="configure-per-application-multi-factor-authentication"></a>Configurare l'autenticazione a più fattori all'applicazione
1. Accedere come amministratore nel portale di classica Azure.
2. Passare a Active Directory e selezionare la directory in cui si vuole abilitare Proxy dell'applicazione.
3. Fare clic su **applicazioni** e scorrere fino alla sezione **Le regole di accesso** . La sezione di regole di accesso viene visualizzata solo per le applicazioni pubblicate tramite Proxy di applicazione che usa l'autenticazione federato.
4. Attivare la regola selezionando **Abilitare le regole di accesso** su **On**.
5. Specificare gli utenti e gruppi a cui verranno applicate le regole. Utilizzare il pulsante **Aggiungi gruppo** per selezionare uno o più gruppi a cui verrà applicata la regola di accesso. Questa finestra di dialogo può anche essere utilizzato per rimuovere gruppi selezionati.  Quando si selezionano le regole da applicare ai gruppi, le regole di accesso verranno applicate solo per gli utenti che appartengono a uno dei gruppi di sicurezza specificati.  

  - Per escludere in modo esplicito i gruppi di sicurezza in base alla regola, **ad eccezione di** controllo e specificare uno o più gruppi. Utenti che fanno parte di un gruppo nell'elenco ad eccezione non sia necessario eseguire l'autenticazione a più fattori.  

  - Se un utente è stato configurato con la funzionalità di autenticazione a più fattori per utente, questa impostazione avrà la precedenza rispetto alle regole di autenticazione a più fattori dell'applicazione. Questo significa che un utente che è stato configurato per l'autenticazione a più fattori per utente sarà necessario eseguire l'autenticazione a più fattori anche se sono stati esenti da regole di autenticazione a più fattori dell'applicazione. Ulteriori informazioni [sull'autenticazione a più fattori e le impostazioni per utente](../multi-factor-authentication/multi-factor-authentication.md).

6. Selezionare la regola di accesso per impostare:
    - **Autenticazione a più fattori richiedono**: gli utenti a cui applicano le regole di accesso saranno necessari per completare l'autenticazione a più fattori prima di accedere l'applicazione a cui applicare la regola.
    - **Richiedi autenticazione a più fattori quando non è in ufficio**: gli utenti che tentano di accedere all'applicazione da un indirizzo IP attendibile non verranno richiesto di eseguire l'autenticazione a più fattori. Nella pagina Impostazioni di autenticazione a più fattori, è possono configurare gli intervalli di indirizzi IP attendibili.
    - **Bloccare l'accesso quando non è in ufficio**: gli utenti che tentano di accedere all'applicazione dall'esterno della rete aziendale non sarà possibile accedere all'applicazione.


## <a name="configuring-mfa-for-federation-services"></a>Configurazione MFA per federation services.
Per federati tenant, può essere eseguita l'autenticazione a più fattori (MFA) oppure Azure Active Directory locale nel server ADFS. Per impostazione predefinita, MFA verificherà in qualsiasi pagina ospitato da Azure Active Directory. Per configurare MFA locale, eseguire Windows PowerShell e utilizzare la proprietà – SupportsMFA per impostare il modulo di Azure Active Directory.

Nell'esempio seguente viene illustrato come abilitare MFA locale utilizzando il [cmdlet Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) nel tenant contoso.com:`Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

Oltre a impostare questo flag, l'istanza di ADFS federato tenant Active Directory deve essere configurato per eseguire l'autenticazione a più fattori. Seguire le istruzioni per [la distribuzione di Microsoft Azure autenticazione a più fattori locale](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).


## <a name="see-also"></a>Vedere anche

- [Utilizzo di applicazioni sulle attestazioni](active-directory-application-proxy-claims-aware-apps.md)
- [Pubblicare applicazioni con Proxy dell'applicazione](active-directory-application-proxy-publish.md)
- [Abilitare single sign-in](active-directory-application-proxy-sso-using-kcd.md)
- [Pubblicare le applicazioni che utilizzano il nome di dominio](active-directory-application-proxy-custom-domains.md)

Per essere sempre informati e gli aggiornamenti più recenti, visitare il [blog di Proxy di applicazione](http://blogs.technet.com/b/applicationproxyblog/)
