<properties
    pageTitle="Azure esperienza MFA accedere con l'autenticazione a più fattori di Azure"
    description="Questa pagina fornirà indicazioni su dove è possibile per vedere i diversi metodi di accesso disponibili con Azure MFA."
    keywords="autenticazione dell'utente, esperienza di accesso, effettuare l'accesso con telefono cellulare, effettuare l'accesso con telefono dell'ufficio"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kgremban"/>

# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Accedi esperienza con autenticazione a più fattori Azure
> [AZURE.NOTE]  La documentazione seguente disponibile in questa pagina illustra una tipica esperienza di accesso.  Per assistenza con l'accesso, vedere [ci sono problemi con l'autenticazione a più fattori di Azure](multi-factor-authentication-end-user-manage-settings.md)



## <a name="what-will-your-sign-in-experience-be"></a>Cosa sarà l'esperienza di accesso?
L'esperienza dell'utente variano a seconda di come accedere e utilizzare l'autenticazione a più fattori.  In questa sezione è verranno fornite informazioni su cosa aspettarsi quando si accede.  Scegliere quello che descrive le operazioni:


Che stai facendo?|Descrizione
:------------- | :------------- |
[Accedere con il telefono cellulare o su office](#signing-in-with-mobile-or-office-phone) | È previsto di effettuare l'accesso tramite telefono cellulare o office.
[Accedere con l'app Microsoft Authenticator con notifica](#signing-in-with-the-microsoft-authenticator-app-using-notification) | Si tratta di cosa aspettarsi mediante l'app Microsoft Authenticator con le notifiche.
[Accedere con l'app Microsoft Authenticator con codice di verifica](#signing-in-with-the-microsoft-authenticator-app-using-verification-code)|Si tratta di cosa aspettarsi uso thapp Microsoft Authenticator con un codice di verifica.
[Accedere con un metodo alternativo](#signing-in-with-an-alternate-method)|In questo modo verranno visualizzati cosa aspettarsi se si desidera utilizzare un metodo alternativo.

## <a name="signing-in-with-mobile-or-office-phone"></a>Accedere con il telefono cellulare o su office

Le informazioni seguenti per descrivere l'esperienza dell'utilizzo di autenticazione a più fattori con il telefono cellulare o su office.

### <a name="to-sign-in-with-a-call-to-your-office-or-mobile-phone"></a>Accedere con una chiamata per ufficio o cellulare

- Accedere a un'applicazione o un servizio, ad esempio Office 365 usando il nome utente e la password.
- Microsoft eseguiranno una chiamata è.

![Chiamate Microsoft](./media/multi-factor-authentication-end-user-signin-phone/call.png)

- Rispondere al telefono e premere il tasto #.

![Risposta](./media/multi-factor-authentication-end-user-signin-phone/phone.png)

- A questo punto deve essere firmato.</li>

## <a name="signing-in-with-the-microsoft-authenticator-app-using-notification"></a>Accedere con l'app Microsoft Authenticator con notifica

Le informazioni seguenti per descrivere l'esperienza dell'utilizzo di autenticazione a più fattori con l'app Microsoft Authenticator quando si riceve una notifica.

### <a name="to-sign-in-with-a-notification-sent-the-microsoft-authenticator-app"></a>Per accedere con una notifica inviata l'app Microsoft Authenticator

- Accedere a un'applicazione o un servizio, ad esempio Office 365 usando il nome utente e la password.
- Microsoft invierà una notifica.

![Microsoft invia una notifica](./media/multi-factor-authentication-end-user-signin-app-notify/notify.png)


- Rispondere al telefono e premere il tasto di verifica.  Se è necessario un PIN verrà chiesto di seguito per renderla.

![Verificare](./media/multi-factor-authentication-end-user-signin-app-notify/phone.png)

![Programma di installazione](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

- A questo punto deve essere firmato.


## <a name="signing-in-with-the-microsoft-authenticator-app-using-verification-code"></a>Accedere con l'app Microsoft Authenticator con codice di verifica

Le informazioni seguenti per descrivere l'esperienza dell'utilizzo di autenticazione a più fattori con l'app Microsoft Authenticator quando si usa con un codice di verifica.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Per accedere con un codice di verifica con l'app Microsoft Authenticator

- Accedere a un'applicazione o un servizio, ad esempio Office 365 usando il nome utente e la password.
- Microsoft verrà chiesto di un codice di verifica.

![Immettere il codice di verifica](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)

- Aprire l'app Microsoft Authenticator sul telefono e immettere il codice nella casella in cui si esegue l'accesso.

![Riceve il codice](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)



- A questo punto deve essere firmato.


## <a name="signing-in-with-an-alternate-method"></a>Accedere con un metodo alternativo


La sezione seguente illustra come effettuare l'accesso con un metodo alternativo quando il metodo principale potrebbe non essere disponibile.

### <a name="to-sign-in-with-an-alternate-method"></a>Accedere con un metodo alternativo

- Accedere a un'applicazione o un servizio, ad esempio Office 365 usando il nome utente e la password.
- Selezionare utilizza un'opzione di verifica diverso.  Si presenta con una scelta delle opzioni diverse. Il numero viene visualizzato verranno dipende il numero è stato impostato.

![Utilizzare il metodo alternativo](./media/multi-factor-authentication-end-user-signin-alt/alt.png)

- Scegliere un metodo alternativo e l'accesso.
