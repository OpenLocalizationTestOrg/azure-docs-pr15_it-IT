<properties 
    pageTitle="Stati di Microsoft Azure a più fattori autenticazione utente"
    description="Informazioni sulle stati utente in Azure MFA."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="user-states-in-azure-multi-factor-authentication"></a>Stati utente in Azure autenticazione a più fattori

Account utente di autenticazione a più fattori Azure sono i seguenti tre stati distinti:

Stato | Descrizione |App browser non interessate| Note
:-------------: | :-------------: |:-------------: |:-------------: |
Disattivato | Stato predefinito per un nuovo utente non registrato in autenticazione a più fattori.|No|L'utente non utilizza l'autenticazione a più fattori.
Attivato |L'utente sia stata registrata in autenticazione a più fattori.|No.  Continuano a funzionare fino al completamento del processo di registrazione.|L'utente è abilitato ma non è stata completata la procedura di registrazione. Verrà richiesto di completare il processo per l'accesso successivo.
Stabilite|L'utente sia stata registrata e completamento del processo di registrazione per l'uso di autenticazione a più fattori.|Sì.  App richiedono la password di app. | L'utente può o non ha effettuato la registrazione. Se completato il processo di registrazione, si sta utilizzando l'autenticazione a più fattori. In caso contrario, l'utente verrà richiesto per completare il processo per l'accesso successivo.

## <a name="changing-a-user-state"></a>Modifica dello stato di un utente
Modifiche allo stato di utenti a seconda che siano o meno trascorse configurazione per MFA e se l'utente ha completato il processo.  Quando si attiva MFA per un utente, gli utenti dello stato cambierà da disabilitato abilitate.  Dopo che l'utente il cui stato è stato modificato per abilitato, accede e completa il processo, il relativo stato verrà modificato in imposta.  

### <a name="to-view-a-users-state"></a>Per visualizzare lo stato dell'utente
--------------------------------------------------------------------------------
1.  Accedere al **portale classica Azure** come amministratore.
2.  Sul lato sinistro fare clic su **Active Directory**.
3.  In, fare clic su **Directory** nella directory dell'utente che si desidera attivare.
![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Nella parte superiore, fare clic su **utenti**.
5.  Nella parte inferiore della pagina, fare clic su **Gestisci autenticazione a più fattori**.
![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Verrà aperta una nuova scheda del browser.  Sarà possibile visualizzare lo stato di utenti.
![Fare clic su Directory](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

###<a name="to-change-the-state-from-disabled-to-enabled"></a>Per modificare lo stato disabilitato su abilitato
1.  Accedere al **portale classica Azure** come amministratore.
2.  Sul lato sinistro fare clic su **Active Directory**.
3.  In, fare clic su **Directory** nella directory dell'utente che si desidera attivare.
![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Nella parte superiore, fare clic su **utenti**.
5.  Nella parte inferiore della pagina, fare clic su **Gestisci autenticazione a più fattori**.
![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Verrà aperta una nuova scheda del browser.  Trovare l'utente che si desidera abilitare per l'autenticazione a più fattori. Potrebbe essere necessario modificare la visualizzazione nella parte superiore. Verificare che lo stato **disabilitato.** 
 ![Consentire all'utente](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Inserire un **controllo** nella casella accanto al nome.
7.  A destra, fare clic su **Attiva**.
![Abilitare utente](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Fare clic su **Attiva autenticazione a più fattori**.
![Abilitare utente](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Si noterà che dell'utente è stato **disattivato** **abilitate**.
![Consentire agli utenti](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  Dopo aver attivato gli utenti, è consigliabile informarli tramite posta elettronica.  Consigliabile informarli inoltre come utilizzare le applicazioni non browser per evitare di essere bloccato.

### <a name="to-change-the-state-from-enabledenforced-to-disabled"></a>Per modificare lo stato da attivato applicata a disattivato
1.  Accedere al **portale classica Azure** come amministratore.
2.  Sul lato sinistro fare clic su **Active Directory**.
3.  In, fare clic su **Directory** nella directory dell'utente che si desidera attivare.
![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Nella parte superiore, fare clic su **utenti**.
5.  Nella parte inferiore della pagina, fare clic su **Gestisci autenticazione a più fattori**.
![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Verrà aperta una nuova scheda del browser.  Trovare l'utente che si desidera disattivare. Potrebbe essere necessario modificare la visualizzazione nella parte superiore. Assicurarsi che lo stato è uno dei due **attivata** o **stabilite.**
7.  Inserire un **controllo** nella casella accanto al nome.
7.  A destra, fare clic su **Disattiva**.
![Disabilitare l'utente](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
8.  Verrà richiesto di confermare questa.  Fare clic su **Sì**.
![Disabilitare l'utente](./media/multi-factor-authentication-get-started-user-states/userstate3.png)
9.  Risulta quindi che ha esito negativo.  Fare clic su **chiudere.** 
 ![Disattiva utente](./media/multi-factor-authentication-get-started-user-states/userstate4.png)
