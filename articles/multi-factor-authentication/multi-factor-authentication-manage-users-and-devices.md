<properties 
    pageTitle="Report di Azure autenticazione a più fattori"
    description="Descrive come modificare le impostazioni utente, ad esempio imporre agli utenti di eseguire nuovamente il processo di prova."
    documentationCenter=""
    services="multi-factor-authentication"
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

# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gestione delle impostazioni utente con autenticazione a più fattori Azure nel cloud

Un amministratore, è possibile gestire le seguenti impostazioni utente e dispositivo.  

- [Richiedere agli utenti selezionati di fornire nuovamente i metodi di contatto](#require-selected-users-to-provide-contact-methods-again)
- [Eliminare utenti esistenti password dell'app](#delete-users-existing-app-passwords)
- [Ripristinare MFA su tutti i dispositivi sospesi per un utente](#restore-mfa-on-all-suspended-devices-for-a-user)






Questa funzionalità è utile se un computer o dispositivo perdita o furto oppure è necessario rimuovere l'accesso agli utenti.


## <a name="require-selected-users-to-provide-contact-methods-again"></a>Richiedere agli utenti selezionati di fornire nuovamente i metodi di contatto

Ciò impostazione forza l'utente per completare il processo di registrazione nuovamente quando l'utente accede. Tenere presente che non browser App continueranno a funzionare se l'utente ha password dell'app per loro.  È possibile eliminare la password dell'app utenti selezionando anche **eliminare tutte le password app esistente generate dagli utenti selezionati**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Come richiedere agli utenti di fornire nuovamente i metodi di contatto




1. Effettuare l'accesso al portale di classica Azure.
2. Sul lato sinistro fare clic su Active Directory.
3. In, fare clic su Directory nella directory dell'utente per richiedere specificare di nuovo il metodo di contatto.
4. Nella parte superiore, fare clic su utenti.
5. Nella parte inferiore della pagina, fare clic su Gestisci autenticazione a più fattori Verrà aperta la pagina di autenticazione a più fattori.
6. Trovare l'utente che si desidera gestire e inserire un segno di spunta nella casella Trova accanto al nome. Potrebbe essere necessario modificare la visualizzazione nella parte superiore.
7. Verrà visualizzata sul collegamento **gestisce le impostazioni utente** a destra. Fare clic su.
8. Inserire un controllo richiedono **utenti selezionati fornire nuovamente metodi di contatto**.
![Fornire metodi di contatto](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
10. Fare clic su Salva.
11. Fare clic su Chiudi

## <a name="delete-users-existing-app-passwords"></a>Eliminare utenti esistenti password dell'app

Consente di eliminare tutte le password app che un utente ha creato. App browser non associati a tali password app prossimamente non funzionare se non viene creata una nuova password di app.

### <a name="how-to-delete-users-existing-app-passwords"></a>Come eliminare utenti esistenti password dell'app

1. Effettuare l'accesso al portale di classica Azure.
2. Sul lato sinistro fare clic su Active Directory.
3. In, fare clic su Directory nella directory dell'utente che si desidera eliminare la password dell'app.
4. Nella parte superiore, fare clic su utenti.
5. Nella parte inferiore della pagina, fare clic su Gestisci autenticazione a più fattori Verrà aperta la pagina di autenticazione a più fattori.
6. Trovare l'utente che si desidera gestire e inserire un segno di spunta nella casella Trova accanto al nome. Potrebbe essere necessario modificare la visualizzazione nella parte superiore.
7. Verrà visualizzata sul collegamento **gestisce le impostazioni utente** a destra. Fare clic su.
8. Inserire un controllo in **eliminare tutte le password app esistente generate dagli utenti selezionati**.
![Eliminare le password app](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
10. Fare clic su Salva.
10. Fare clic su Chiudi.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Ripristinare MFA su tutti i dispositivi da ricordare per un utente

Gli amministratori hanno la possibilità di ripristinare l'autenticazione a più fattori nel browser e dispositivi di utenti. In tal caso, memorizza MFA verrà rimosso da tutti i dispositivi dell'utente e browser e all'utente verrà richiesto di utilizzare MFA quando si accede alla successiva.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Come ripristinare MFA su tutti i dispositivi sospesi per un utente

1. Effettuare l'accesso al portale di classica Azure.
2. Sul lato sinistro fare clic su Active Directory.
3. In, fare clic su Directory nella directory dell'utente che si desidera ripristinare mfa in.
4. Nella parte superiore, fare clic su utenti.
5. Nella parte inferiore della pagina, fare clic su Gestisci autenticazione a più fattori Verrà aperta la pagina di autenticazione a più fattori.
6. Trovare l'utente che si desidera gestire e inserire un segno di spunta nella casella Trova accanto al nome. Potrebbe essere necessario modificare la visualizzazione nella parte superiore.
7. Verrà visualizzata sul collegamento **gestisce le impostazioni utente** a destra. Fare clic su.
8. Inserire un segno di **ripristinare l'autenticazione a più fattori su tutti i dispositivi memorizzati**
![eliminare password dell'app](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. Fare clic su Salva.
10. Fare clic su Chiudi.
