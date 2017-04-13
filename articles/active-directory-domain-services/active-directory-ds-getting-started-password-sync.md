<properties
    pageTitle="Servizi di dominio Active Directory Azure: Abilitare la sincronizzazione delle password | Microsoft Azure"
    description="Guida introduttiva di servizi di dominio di Azure Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="maheshu"/>

# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Attivare la sincronizzazione delle password in servizi di dominio Active Directory di Azure
Nelle attività precedente, l'utente abilitato Servizi di dominio Active Directory di Azure per il tenant di Azure Active Directory. È necessario abilitare hash credenziali necessarie per l'autenticazione Kerberos e NTLM per la sincronizzazione con servizi di dominio Active Directory Azure. Dopo aver configurata la sincronizzazione delle credenziali, gli utenti possono accedere al dominio gestito utilizzando le proprie credenziali aziendale.

Le fasi sono diversi in base a se l'organizzazione ha un annuncio di Azure basata solo su cloud tenant o è impostato per la sincronizzazione con la directory locali con Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Azure AD basata solo su cloud tenant](active-directory-ds-getting-started-password-sync.md)
- [Sincronizzato tenant di Azure Active Directory](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-cloud-only-azure-ad-tenant"></a>Passaggio 5: Abilitare la sincronizzazione delle password per i servizi di dominio AAD per un Azure basata solo su cloud tenant di Active Directory
Azure servizi di dominio Active Directory richiede credenziali hash in un formato adatto per l'autenticazione Kerberos e NTLM, per eseguire l'autenticazione gli utenti del dominio gestito. A meno che non si abilita AAD servizi di dominio per il tenant, Azure Active Directory non generare o archiviare hash delle credenziali nel formato richiesto per l'autenticazione Kerberos o NTLM. Per motivi di sicurezza più evidenti, Azure Active Directory anche non memorizza le credenziali in forma di testo non crittografato. Di conseguenza, Azure Active Directory non esiste un modo per generare questi hash credenziali NTLM o Kerberos in base alle credenziali esistente degli utenti.

> [AZURE.NOTE] Se l'organizzazione ha un basata solo su cloud tenant di Azure Active Directory, gli utenti che devono utilizzare i servizi di dominio Active Directory Azure necessario cambiare la propria password.

Il processo di modifica password causa le credenziali hash richieste dai servizi di dominio di Azure Active Directory per l'autenticazione Kerberos e NTLM deve essere generato in Azure Active Directory. Può essere scadere le password per tutti gli utenti nel tenant di che è necessario utilizzare servizi di dominio Active Directory Azure o chiedere agli utenti di cambiare la propria password.


### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>Attivare la generazione hash credenziali NTLM e Kerberos per un Azure basata solo su cloud tenant di Active Directory
Ecco le istruzioni che è necessario specificare gli utenti finali, in modo che possono modificare la password:

1. Passare alla pagina comando Azure Active Directory per l'organizzazione in [http://myapps.microsoft.com](http://myapps.microsoft.com).

2. Selezionare la scheda **profilo** in questa pagina.

3. Fare clic sul riquadro di **Modifica della password** in questa pagina.

    ![Creare una rete virtuale per i servizi di dominio di Azure Active Directory.](./media/active-directory-domain-services-getting-started/user-change-password.png)

    > [AZURE.NOTE] Se l'opzione di **Modifica della password** nella pagina pannello di accesso non è visualizzata, assicurarsi che l'organizzazione ha configurato [la gestione delle password in Azure Active Directory](../active-directory/active-directory-passwords-getting-started.md).

4. Nella pagina **Cambia password** digitare la password esistente (precedente) e digitare una nuova password e confermarla. Fare clic su **Invia**.

    ![Creare una rete virtuale per i servizi di dominio di Azure Active Directory.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Dopo avere modificato la password, la nuova password sarà utilizzabile in servizi di dominio Active Directory Azure subito. Dopo alcuni minuti (in genere circa 20 minuti), è possibile accedere al computer connessi al dominio gestito utilizzando la password appena modificata.

<br>

## <a name="related-content"></a>Contenuti correlati

- [Come aggiornare la propria password](../active-directory/active-directory-passwords-update-your-own-password.md)

- [Guida introduttiva di gestione delle Password in Azure Active Directory](../active-directory/active-directory-passwords-getting-started.md).

- [Attivare la sincronizzazione delle password per AAD i servizi di dominio per un Azure sincronizzata tenant di Active Directory](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Amministrare un dominio gestito di servizi di dominio Active Directory di Azure](active-directory-ds-admin-guide-administer-domain.md)

- [Aggiungere una macchina virtuale di Windows a un dominio gestito di servizi di dominio Active Directory di Azure](active-directory-ds-admin-guide-join-windows-vm.md)

- [Aggiungere una macchina virtuale Red Hat Enterprise Linux a un dominio gestito di servizi di dominio Active Directory di Azure](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
