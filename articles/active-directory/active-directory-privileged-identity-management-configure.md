<properties
    pageTitle="Gestione delle identità di Azure Active Directory privilegi | Microsoft Azure"
    description="Un argomento che illustra come usare PIM per migliorare la protezione del cloud e qual è la gestione delle identità privilegi di Azure Active Directory."
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
    ms.date="09/16/2016"
    ms.author="kgremban"/>

# <a name="azure-ad-privileged-identity-management"></a>Gestione delle identità privilegi di Azure Active Directory

Con Azure Active Directory (AD) privilegi di gestione delle identità, è possibile gestire, il controllo e monitorare l'accesso all'interno dell'organizzazione. Consente l'accesso alle risorse in Azure Active Directory e altri servizi online di Microsoft Office 365 o Microsoft Intune.  

> [AZURE.NOTE] Privilegi di gestione delle identità è disponibile solo con l'edizione Premium P2 di Azure Active Directory. Per ulteriori informazioni, vedere [edizioni di Azure Active Directory](active-directory-editions.md).

Le organizzazioni desiderano ridurre il numero di persone che hanno accesso a proteggere le informazioni o risorse, poiché che riduce le possibilità di un utente non autorizzato l'accesso. Tuttavia, è comunque gli utenti devono eseguire operazioni con privilegi di Azure, Office 365 o SaaS app. Organizzazioni concedono agli utenti l'accesso privilegi in Azure Active Directory senza monitoraggio cosa stanno facendo gli utenti con i privilegi di amministratore. Gestione delle identità di Azure Active Directory privilegi consente di risolvere il rischio.  

Gestione delle identità di Azure Active Directory privilegi consente di:  

- Vedere quali utenti sono amministratori di Azure Active Directory
- Abilitare le connessioni, "just in time" accesso amministrativo a Microsoft Online Services come Office 365 e Intune
- Ricevere report Cronologia di accesso di amministratore e le modifiche nelle assegnazioni di amministratore
- Ricevere avvisi sull'accesso a un ruolo privilegiato

Azure Active Directory privilegi di gestione delle identità possono gestire incorporati ruoli dell'organizzazione di Azure Active Directory, tra cui:  

- Amministratore globale
- Amministratore fatturazione
- Amministratore del servizio  
- Amministratore di utenti
- Amministratore password

## <a name="just-in-time-administrator-access"></a>In accesso come amministratore ora

Storicamente, è possibile assegnare un utente a un ruolo di amministratore tramite il portale di classica Azure e Windows PowerShell. Di conseguenza, tale utente diventa un **amministratore permanente**, sempre attiva in ruoli assegnati. Gestione delle identità di Azure Active Directory privilegi introduce il concetto di un **amministratore idoneo**. Gli amministratori idonei dovrebbero essere gli utenti che devono accedere privilegi ora e quindi, ma non ogni giorno. Il ruolo è inattivo finché l'utente deve accedere, quindi hanno completare il processo di attivazione e diventare amministratore attivo per un periodo di tempo predeterminato.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Abilitare i privilegi di gestione delle identità per la directory

È possibile iniziare a usare Azure Active Directory privilegi di gestione delle identità nel [portale di Azure](https://portal.azure.com/).

>[AZURE.NOTE] È necessario essere un amministratore globale con un account aziendale (ad esempio @yourdomain.com), non un account Microsoft (ad esempio @outlook.com), per consentire di Azure Active Directory privilegi di gestione delle identità per una directory.

1. Accedere al [portale di Azure](https://portal.azure.com/) come un amministratore globale della directory.
2. Se l'organizzazione ha più di una directory, selezionare il nome utente nell'angolo superiore destro del portale di Azure. Selezionare la cartella in cui si userà Azure Active Directory privilegi di gestione delle identità.
3. Selezionare **altri servizi** e utilizzare la casella di testo di filtro per cercare i **privilegi di Azure Active Directory di gestione delle identità**.
4. Selezionare **Aggiungi a dashboard** e quindi fare clic su **Crea**. Apertura dell'applicazione di privilegi di gestione delle identità.

Se si è la prima persona che usa Azure Active Directory privilegi di gestione delle identità nella directory, quindi l' [impostazione guidata sicurezza](active-directory-privileged-identity-management-security-wizard.md) illustra l'esperienza di assegnazione iniziale. Dopo che diventa automaticamente il primo **amministratore della sicurezza** e l' **amministratore ruoli privilegi** della directory.

Solo l'amministratore di ruolo privilegi possa gestire l'accesso per altri amministratori. È possibile [concedere ad altri utenti la possibilità di gestire PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="privileged-identity-management-dashboard"></a>Dashboard di gestione delle identità privilegi

Gestione di identità Azure Active Directory privilegi fornisce un dashboard che forniscono informazioni importanti, ad esempio:

- Avvisi che segnalano le opportunità per migliorare la sicurezza
- Il numero di utenti assegnati a ogni ruolo privilegiato  
- Il numero di amministratori idonei e permanenti
- Vengono esaminate in access in corso

![Dashboard PIM - schermata][2]

## <a name="privileged-role-management"></a>Gestione dei ruoli privilegi

Con privilegi di Azure Active Directory di gestione delle identità, è possibile gestire gli amministratori aggiungendo o rimuovendo gli amministratori di permanenti o idonei per ogni ruolo.

![Amministratori di aggiunta/rimozione PIM - schermata][3]

## <a name="configure-the-role-activation-settings"></a>Configurare le impostazioni di attivazione di ruolo

Usare le [impostazioni dei ruoli](active-directory-privileged-identity-management-how-to-change-default-settings.md) è possibile configurare le proprietà di attivazione di ruolo idoneo tra cui:

- La durata del periodo di attivazione ruolo
- La notifica di attivazione di ruolo
- Le informazioni di che un utente deve fornire durante il processo di attivazione del ruolo  

![Schermata di impostazioni - attivazione amministratore - PIM][4]

Si noti che nell'immagine, i pulsanti per **l'Autenticazione a più fattori** sono disabilitati. Per alcuni, altamente privilegi ruoli, è necessario MFA per la protezione avanzata.

## <a name="role-activation"></a>Attivazione di ruolo  

Per [attivare un ruolo](active-directory-privileged-identity-management-how-to-activate-role.md), un amministratore idoneo richieste una volta associate ai "attivazione" per il ruolo. È possibile richiedere l'attivazione tramite l'opzione **Attiva il ruolo** di Azure Active Directory privilegi di gestione delle identità.

Amministratori che desiderano attivare un ruolo sono necessario inizializzare Azure Active Directory privilegi di gestione delle identità nel portale di Azure.

Attivazione di ruolo è personalizzabile. In impostazioni PIM, è possibile determinare la lunghezza di attivazione e quali informazioni l'amministratore deve fornire per attivare il ruolo.

![Attivazione di ruolo PIM amministratore richiesta - schermata][5]

## <a name="review-role-activity"></a>Attività di revisione ruolo

Esistono due modi per tenere traccia di come i dipendenti e amministratori con privilegi ruoli. La prima opzione utilizza [controllare la cronologia](active-directory-privileged-identity-management-how-to-use-audit-log.md). La cronologia di controllo Registra revisioni in assegnazioni di ruolo privilegi e la cronologia di attivazione di ruolo.

![Cronologia di attivazione PIM - schermata][6]

La seconda opzione consiste nel configurare regolare [vengono esaminate in access](active-directory-privileged-identity-management-how-to-start-security-review.md). Questi controlli di access possono essere eseguiti da e assegnati revisore (ad esempio un responsabile del team) o i dipendenti possono verificare se stessi. Questo è il modo migliore per controllare chi deve comunque accedere e utente non è più responsabile.


## <a name="next-steps"></a>Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png
