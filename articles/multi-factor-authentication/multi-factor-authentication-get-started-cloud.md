<properties
    pageTitle="Guida introduttiva di Azure MFA nel cloud | Microsoft Azure"
    description="Questa è la pagina di autenticazione a più fattori Microsoft Azure che descrive come iniziare a utilizzare MFA Azure nel cloud."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Guida introduttiva di autenticazione a più fattori Azure nel cloud
In questo articolo viene descritto come iniziare a utilizzare l'autenticazione a più fattori Azure nel cloud.

> [AZURE.NOTE]  La documentazione seguente vengono fornite informazioni su come consentire agli utenti tramite il **Portale classica Azure**. Se cercano informazioni su come configurare l'autenticazione a più fattori Azure per gli utenti di Office 365, vedere [configurare l'autenticazione a più fattori per Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![MFA nel Cloud](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisites"></a>Prerequisiti
I prerequisiti seguenti sono necessari affinché sia possibile selezionare autenticazione a più fattori Azure per gli utenti.


1. [Iscrizione per un abbonamento a Azure](https://azure.microsoft.com/pricing/free-trial/) - se non si dispone già di un abbonamento a Azure, è necessario per l'abbonamento per uno. Se si solo dall'inizio e la MFA Azure è possibile utilizzare una sottoscrizione di valutazione
2. [Creare un Provider di autenticazione a più fattori](multi-factor-authentication-get-started-auth-provider.md) e assegnare alla directory o [assegnare licenze agli utenti](multi-factor-authentication-get-started-assign-licenses.md)

> [AZURE.NOTE]  Licenze sono disponibili per gli utenti che dispongono di Azure MFA, Azure Active Directory Premium o Enterprise mobilità famiglia di prodotti (EMS).  MFA è incluso in Azure Active Directory Premium e di questa funzionalità. Se si dispone di licenze sufficiente, non è necessario creare un Provider di autenticazione.


## <a name="turn-on-two-step-verification-for-users"></a>Attivare la verifica in due passaggi per gli utenti
Per avviare la richiesta di verifica due inizio per un utente, modificare lo stato dell'utente da disattivato ad attivato.  Per ulteriori informazioni su stati utente, vedere [Gli stati utente di autenticazione a più fattori Azure](multi-factor-authentication-get-started-user-states.md)

Utilizzare la procedura seguente per abilitare MFA per gli utenti.

### <a name="to-turn-on-multi-factor-authentication"></a>Per attivare l'autenticazione a più fattori

1.  Accedere al [portale classica Azure](https://manage.windowsazure.com) come amministratore.
2.  Sul lato sinistro fare clic su **Active Directory**.
3.  In Directory, selezionare la cartella per l'utente che si desidera attivare.
![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Nella parte superiore, fare clic su **utenti**.
5.  Nella parte inferiore della pagina, fare clic su **Gestisci autenticazione a più fattori**. Verrà aperta una nuova scheda del browser.
![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Trovare l'utente che si desidera attivare per la verifica in due passaggi. Potrebbe essere necessario modificare la visualizzazione nella parte superiore. Verificare che lo stato **disabilitato.** 
 ![Consentire all'utente](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Inserire un **controllo** nella casella accanto al nome.
7.  A destra, fare clic su **Attiva**.
![Abilitare utente](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Fare clic su **Attiva autenticazione a più fattori**.
![Abilitare utente](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Si noti che lo stato dell'utente è cambiato da **disattivato** **abilitate**.
![Consentire agli utenti](./media/multi-factor-authentication-get-started-cloud/user.png)

Dopo aver attivato gli utenti, è opportuno avvisare tramite posta elettronica. La volta successiva che si tenta di accedere, verrà chiesto di registrare il proprio account per la verifica in due passaggi. Una volta deve iniziare con la verifica in due passaggi, necessario anche configurare la password dell'app per evitare il blocco di App diverso dal browser.


## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>Usare PowerShell per automatizzare l'attivazione della verifica in due passaggi

Per modificare lo [stato](multi-factor-authentication-whats-next.md) tramite [PowerShell di Azure Active Directory](../powershell-install-configure.md), è possibile utilizzare le operazioni seguenti.  È possibile modificare `$st.State` deve essere uguale a uno degli stati seguenti:

- Attivato
- Stabilite
- Disattivato  

> [AZURE.IMPORTANT]  Se ne sconsiglia contro dallo utenti direttamente lo stato di disattivare lo stato imposto. App basata su browser si arresta in quanto l'utente non ha effettuato la registrazione MFA e ottenuto una [password per l'app](multi-factor-authentication-whats-next.md#app-passwords). Se si dispone non basati su browser App e richiedere la password di app, è consigliabile passare da uno stato disabilitato su attivato. In questo modo agli utenti di eseguire la registrazione e ottenere le password di app. In seguito, è possibile spostarle in imposto.

Utilizzo di PowerShell sarebbe un'opzione per consentire agli utenti in blocco. Attualmente caratteristica non è disponibile in blocco Abilita nel portale di Azure ed è necessario selezionare singolarmente ogni utente. Se si dispone di molti utenti può essere molto un'attività. Tramite la creazione di un PowerShell script tramite le operazioni seguenti, è possibile scorrere un elenco di utenti e abilitarle.

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Ecco un esempio:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Per ulteriori informazioni, vedere [gli stati utente di autenticazione a più fattori Azure](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>Passaggi successivi
Ora che è stata configurata l'autenticazione a più fattori Azure nel cloud, è possibile configurare e configurare la distribuzione. Per ulteriori informazioni, vedere [Configurazione di autenticazione a più fattori Azure](multi-factor-authentication-whats-next.md) .
