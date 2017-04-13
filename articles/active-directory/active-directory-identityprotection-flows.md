<properties
    pageTitle="Accesso esperienze con la protezione dell'identità di Azure Active Directory | Microsoft Azure"
    description="Viene fornita una panoramica dell'esperienza utente quando la protezione dell'identità è ridotto o risolte un utente o quando è necessaria l'autenticazione a più fattori da un criterio."
    services="active-directory"
    keywords="protezione dell'identità di Azure active directory, individuazione app cloud, la gestione applicazioni, sicurezza, rischio, il livello di rischio, vulnerabilità, criteri di sicurezza"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Esperienze di accesso con la protezione dell'identità di Azure Active Directory

Con protezione dell'identità di Azure Active Directory, è possibile:

- richiedere agli utenti di eseguire la registrazione per l'autenticazione a più fattori

- gestire gli accessi rischiosi e i relativi utenti compromessi

La risposta del sistema di questi problemi ha un impatto sulla esperienza di accesso dell'utente perché semplicemente direttamente accesso aggiuntivo fornendo un nome utente e una password non sarà più possibile. Passaggi aggiuntivi necessari per ottenere un utente in modo sicuro nel business.

In questo argomento fornisce una panoramica dell'esperienza di accesso dell'utente per tutti i casi che possono verificarsi.

**Autenticazione a più fattori**

- Registrazione di autenticazione a più fattori



**Segno di rischio**

- Ripristino di accesso rischioso

- Rischiosa accesso aggiuntivo bloccato

- Registrazione di autenticazione a più fattori durante un accesso rischiosa
 

**Utente al rischio**

- Ripristino account compromesso

- Account compromesso bloccati




## <a name="multi-factor-authentication-registration"></a>Registrazione di autenticazione a più fattori

L'esperienza utente migliore per entrambi, il flusso di ripristino account compromesso e rischioso flusso accesso è quando l'utente automatica consente di recuperare. Se gli utenti sono registrati per l'autenticazione a più fattori, si dispone già di un numero di telefono associato il proprio account che può essere utilizzato per passare i problemi legati alla sicurezza. Nessun coinvolgimento tecnico o l'amministratore della Guida è necessarie per recuperare dalla violazione dell'account. In questo modo, altamente è consigliabile per chiedere agli utenti registrati per l'autenticazione a più fattori. 

Gli amministratori possono:

- impostare un criterio che gli utenti dovranno configurare gli account per la verifica rafforzare la sicurezza. 
- Consenti omissione di registrazione di autenticazione a più fattori per fino a 30 giorni, nel caso in cui si desidera consentire agli utenti un periodo di tolleranza prima di registrare.

**La registrazione di autenticazione a più fattori sono previsti tre passaggi:**

1. Nel primo passaggio, l'utente riceve una notifica relativa alla necessità di configurare l'account per l'autenticazione a più fattori. 

    ![Monitoraggio e aggiornamento] (./media/active-directory-identityprotection-flows/140.png "Monitoraggio e aggiornamento")


2. Per configurare l'autenticazione a più fattori, è necessario attendere che il sistema sa come associare a essere contattato.

    ![Monitoraggio e aggiornamento] (./media/active-directory-identityprotection-flows/141.png "Monitoraggio e aggiornamento")
 
3. Il sistema invia una richiesta al proprietario che è necessario rispondere.

    ![Monitoraggio e aggiornamento] (./media/active-directory-identityprotection-flows/142.png "Monitoraggio e aggiornamento")

 



## <a name="risky-sign-in-recovery"></a>Ripristino di accesso rischioso

Quando un amministratore ha configurato un criterio per i rischi di accesso, gli utenti interessati sono una notifica quando tentano di accedere. 

**Il flusso di accesso rischioso include due passaggi:** 

1. L'utente viene informato che qualcosa di insolito è stato rilevato sulle loro accesso aggiuntivo, ad esempio l'accesso da una nuova posizione, dispositivo o app. 

    ![Monitoraggio e aggiornamento] (./media/active-directory-identityprotection-flows/120.png "Monitoraggio e aggiornamento")

2. L'utente è necessario provare la propria identità per risolvere un problema di sicurezza. Se l'utente è registrato per l'autenticazione a più fattori devono round trip un codice di sicurezza per il numero di telefono. Poiché si tratta di un solo un rischiosa effettuare l'accesso e non un account compromesso, l'utente non è necessario modificare la password in questo flusso. 

    ![Monitoraggio e aggiornamento] (./media/active-directory-identityprotection-flows/121.png "Monitoraggio e aggiornamento")



 
## <a name="risky-sign-in-blocked"></a>Rischiosa accesso aggiuntivo bloccato
Gli amministratori inoltre possibile impostare un criterio di rischio di accesso per impedire agli utenti al momento dell'accesso a seconda del livello di rischio. Per sbloccare, gli utenti finali devono contattare un amministratore o il supporto tecnico o tentano l'accesso da una posizione familiare o dispositivo. Il ripristino automatico risolvendo autenticazione a più fattori non è è un'opzione in questo caso.

![Monitoraggio e aggiornamento] (./media/active-directory-identityprotection-flows/200.png "Monitoraggio e aggiornamento")




## <a name="compromised-account-recovery"></a>Ripristino account compromesso

Quando è stato configurato un criterio di protezione di rischio utente, gli utenti che soddisfano l'utente corre il rischio livello specificato nel criterio (e pertanto si presuppone che sia stato compromesso) deve passare attraverso il flusso di ripristino compromesso utente prima che possono accedere aggiuntivo. 

**Il flusso di ripristino compromesso utente sono previsti tre passaggi:**

1. L'utente viene informato che il livello di protezione account rischio a causa di attività sospetta o perdita delle credenziali.

    ![Monitoraggio e aggiornamento] (./media/active-directory-identityprotection-flows/101.png "Monitoraggio e aggiornamento")

2.  L'utente è necessario provare la propria identità per risolvere un problema di sicurezza. Se l'utente è registrato per l'autenticazione a più fattori può automatica il ripristino da violazione. Occorrerà di andata e ritorno un codice di sicurezza per il numero di telefono. 

    ![Monitoraggio e aggiornamento] (./media/active-directory-identityprotection-flows/110.png "Monitoraggio e aggiornamento")


3.  Infine, l'utente è necessario cambiare la propria password poiché un altro utente può hanno accesso al proprio account. Schermate dell'esperienza sono inferiori.
 
    ![Monitoraggio e aggiornamento] (./media/active-directory-identityprotection-flows/111.png "Monitoraggio e aggiornamento")



## <a name="compromised-account-blocked"></a>Account compromesso bloccati 

Per ottenere un utente che è stato bloccato da un criterio di sicurezza di rischio utente sbloccato, l'utente deve rivolgersi all'amministratore o dell'help desk. Il ripristino automatico risolvendo autenticazione a più fattori non è è un'opzione in questo caso.


![Monitoraggio e aggiornamento] (./media/active-directory-identityprotection-flows/104.png "Monitoraggio e aggiornamento")



 
## <a name="reset-password"></a>Reimpostazione password

Se compromesso gli utenti non possono accedere, un amministratore può generare una password temporanea per loro. È necessario che gli utenti di cambiare la propria password durante un accesso successivo.

![Monitoraggio e aggiornamento] (./media/active-directory-identityprotection-flows/160.png "Monitoraggio e aggiornamento")


 




 

## <a name="see-also"></a>Vedere anche

- [Protezione dell'identità di Azure Active Directory](active-directory-identityprotection.md) 