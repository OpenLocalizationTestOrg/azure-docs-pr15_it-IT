<properties
    pageTitle="Azure B2C Directory attiva: Token di sessione e single sign-on configurazione | Microsoft Azure"
    description="Token, sessione e configurazione single sign-on in Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure B2C Directory attiva: Token di sessione e configurazione single sign-on

Questa caratteristica offre il controllo granulare, in [base al criterio](active-directory-b2c-reference-policies.md)di:
 
1. Durata del token di sicurezza emesso da B2C Azure Active Directory (Azure Active Directory).
2. Durata delle sessioni di un'applicazione web gestite da Azure Active Directory B2C.
3. Comportamento Single sign-on (SSO) in più applicazioni e i criteri nel tenant di B2C.

È possibile utilizzare questa caratteristica nel tenant di B2C come indicato di seguito:

1. Seguire questa procedura per [passare a e il caratteristiche B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure.
2. Fare clic su **criteri di accesso**. *Nota: È possibile utilizzare questa caratteristica su qualsiasi tipo di criteri, non solo ad* *I criteri di accesso** *.
3. Aprire un criterio, fare clic su esso. Ad esempio, fare clic su **B2C_1_SiIn**.
4. Fare clic su **Modifica** nella parte superiore e il.
5. Fare clic su **Token, sessione e configurazione single sign-on**.
6. Apportare le modifiche desiderate. Informazioni sulle proprietà disponibili nelle sezioni successive.
7. Fare clic su **OK**.
8. Fare clic su **Salva** nella parte superiore e il.

![Schermata del token, sessione e configurazione single sign-on](./media/active-directory-b2c-token-session-sso/token-session-sso.png)

## <a name="token-lifetimes-configuration"></a>Configurazione di durata token

Azure Active Directory B2C supporta il [protocollo di autorizzazione OAuth 2.0](active-directory-b2c-reference-protocols.md) per consentire l'accesso sicuro a risorse protette. Per implementare questo supporto, Azure Active Directory B2C crea vari [token di sicurezza](active-directory-b2c-reference-tokens.md). Queste sono le proprietà che è possibile utilizzare per gestire la durata del token di sicurezza emesso da Azure Active Directory B2C:

- **Accesso e ID durata token (minuti)**: la durata del token del titolare OAuth 2.0 utilizzato per accedere a una risorsa protetta. Azure Active Directory B2C problemi solo i token ID al momento. Questo valore viene applicato a token di accesso, anche quando si aggiunge il relativo supporto.
   - Predefinito = 60 minuti.
   - Minimo (inclusivo) = 5 minuti.
   - Massimo (inclusivo) = 1440 minuti.
- **Aggiornamento durata (giorni)**: il periodo di tempo massimo prima del quale un token di aggiornamento può essere utilizzato per acquisire un token di ID o di nuovo l'accesso (e facoltativamente un nuovo aggiornamento token, se l'applicazione era stata concessa il `offline_access` ambito).
   - Predefinito = 14 giorni.
   - Minimo (inclusivo) = 1 giorno.
   - Massimo (inclusivo) = 90 giorni.
- **Token di aggiornare la variabile di durata di una finestra (giorni)**: questo periodo di tempo trascorso l'utente dovrà eseguire nuovamente l'autenticazione, indipendentemente dal periodo di validità dell'ultimo aggiornamento token acquisita dall'applicazione. Può essere fornita solo se l'opzione è impostata su **Bounded**. Deve essere maggiore o uguale alla **aggiornamento durata (giorni)** valore. Se l'opzione è impostata su **Unbounded**, è possibile fornire un valore specifico.
   - Predefinito = 90 giorni.
   - Minimo (inclusivo) = 1 giorno.
   - Massimo (inclusivo) = 365 giorni.

Ecco un paio di casi di utilizzo che è possibile abilitare l'utilizzo di queste proprietà:

- Consentire a un utente restare connessi in un'applicazione mobile tempo indefinito, purché lui è continuamente attiva sulla barra dell'applicazione. È possibile eseguire questa operazione mediante l'impostazione di **aggiornamento token la durata di una finestra (giorni)** passare a **Unbounded** nei criteri di accesso.
- Soddisfare i requisiti di conformità e sicurezza del settore impostando la durata di token di accesso appropriato.

## <a name="session-configuration"></a>Configurazione della sessione

Azure Active Directory B2C supporta il [protocollo di autenticazione OpenID connessione](active-directory-b2c-reference-oidc.md) per l'attivazione di accessohttp sicura alle applicazioni web. Queste sono le proprietà che è possibile utilizzare per gestire le sessioni di un'applicazione web:

- **App Web la durata della sessione (minuti)**: la durata del cookie di una sessione di B2C di Azure Active Directory archiviato nel browser dell'utente volta eseguita l'autenticazione.
   - Predefinito = 1440 minuti.
   - Minimo (inclusivo) = 15 minuti.
   - Massimo (inclusivo) = 1440 minuti.
- **Timeout di sessione Web app**: se questa opzione è impostata su **assoluto**, l'utente dovrà ripetere l'autenticazione dopo il periodo di tempo specificato dal trascorre **Web app, la durata della sessione (minuti)** . Se questa opzione è impostata su **materiale** (impostazione predefinita), l'utente rimane l'accesso al finché l'utente è continuamente attiva nell'applicazione web.

Ecco un paio di casi di utilizzo che è possibile abilitare l'utilizzo di queste proprietà:

- Soddisfano i requisiti di sicurezza e conformità del settore mediante l'impostazione della sessione dell'applicazione web appropriato durata.
- Forzare la nuova autenticazione dopo un periodo di tempo durante l'interazione dell'utente con una parte di protezione avanzata dell'applicazione web. 

## <a name="single-sign-on-sso-configuration"></a>Configurazione di Single sign-on (SSO)

Se si dispone di più applicazioni e i criteri nel tenant di B2C, è possibile gestire le interazioni dell'utente tra di loro utilizzando la proprietà di **configurazione del servizio Single sign-on** . È possibile impostare la proprietà a uno dei seguenti impostazioni:

- **Tenant**: si tratta l'impostazione predefinita. Utilizzo di questa impostazione consente più applicazioni e i criteri nel tenant di B2C per condividere la sessione dell'utente stesso. Ad esempio, dopo che un utente accede a un'applicazione, Contoso acquisti l'utente può anche diretta accedere a un altro farmacia Contoso un unico e, dopo l'accesso.
- **Applicazione**: questa sezione consente di mantenere una sessione utente esclusivamente per un'applicazione, indipendente da altre applicazioni. Ad esempio, se si vuole che l'utente per accedere a Contoso farmacia (con le stesse credenziali), anche se lui è già stato eseguito l'accesso a acquisti di Contoso, un'altra applicazione sullo stesso B2C tenant. 
- **Criteri**: questa sezione consente di mantenere una sessione utente esclusivamente per un criterio, indipendente dalle applicazioni usarlo. Ad esempio, se l'utente ha già eseguito l'accesso e completato un passaggio di autenticazione (MFA) con più fattori, lui può essere specificato accesso a parti di protezione maggiore di più applicazioni, purché non scada sessione collegata al criterio.
- **Disattivato**: in questo modo all'utente di eseguire tramite viaggio intera utente per ogni esecuzione del criterio. Ad esempio, in questo modo sarà più utenti di iscriversi a un'applicazione (in uno scenario di desktop condiviso), anche durante un singolo utente rimane l'accesso al durante l'intero periodo di tempo.
