<properties
    pageTitle="Sincronizzazione di Azure AD Connect: come gestire l'account di servizio di Azure Active Directory | Microsoft Azure"
    description="In questo argomento viene illustrato come ripristinare l'account di servizio di Azure Active Directory."
    services="active-directory"
    keywords="AADSTS70002, AADSTS50054, come reimpostare la password per la sincronizzazione di Azure AD Connect degli account del servizio connettore"
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
    ms.date="09/01/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Sincronizzazione di Azure AD Connect: come gestire l'account di servizio di Azure Active Directory
L'account di servizio utilizzato dal connettore Azure Active Directory dovrebbe essere servizio gratuito. Se è necessario reimpostare le credenziali, in questo argomento è automaticamente. Ad esempio, se un amministratore globale ha conseguenze reimpostarla la password sull'account di servizio tramite PowerShell.

## <a name="reset-the-credentials"></a>Reimpostare le credenziali
Se l'account di servizio definito sul connettore Azure Active Directory non è possibile contattare Azure Active Directory a causa di problemi di autenticazione, è possibile reimpostare la password.

1. Accedere al server di sincronizzazione di Azure AD Connect e iniziare a PowerShell.
2. Eseguire `Add-ADSyncAADServiceAccount`.  
![Addadsyncaadserviceaccount cmdlet di PowerShell](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Specificare le credenziali di amministratore globale di Azure Active Directory.

Questo cmdlet consente di reimpostare la password per l'account di servizio e aggiornarlo in Azure Active Directory e nel motore di sincronizzazione.

## <a name="known-issues-these-steps-can-solve"></a>Questa procedura può aiutare a risolvere i problemi
In questa sezione è riportato un elenco degli errori segnalati dai clienti sono stati corretti da un credenziali reimpostare sull'account di servizio Azure Active Directory.

-----------
Evento 6900  
Il server ha rilevato un errore imprevisto durante l'elaborazione di una notifica di modifica password:  
AADSTS70002: Errore convalida credenziali. AADSTS50054: Vecchia password viene usata per l'autenticazione.

----------
Evento 659  
Errore durante il recupero di configurazione di sincronizzazione dei criteri di password. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Errore convalida credenziali. AADSTS50054: Vecchia password viene usata per l'autenticazione.

## <a name="next-steps"></a>Passaggi successivi

**Argomenti della panoramica**

- [Sincronizzazione di Azure AD Connect: comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
- [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
