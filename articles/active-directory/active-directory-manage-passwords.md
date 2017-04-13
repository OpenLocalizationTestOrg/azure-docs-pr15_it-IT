<properties
    pageTitle="Gestire le password in Azure Active Directory | Microsoft Azure"
    description="Come gestire le password in Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="curtand"/>

# <a name="manage-passwords-in-azure-active-directory"></a>Gestire le password in Azure Active Directory

Se si è un amministratore, è possibile reimpostare una password utente in Azure Active Directory (Azure Active Directory) nel portale di classica Azure. Fare clic sul nome della cartella e nella pagina utenti, fare clic sul nome dell'utente e nella parte inferiore del portale fare clic su **Reimposta Password**.

Il resto di questo argomento illustra l'insieme completo di funzionalità di gestione password che supporta la Azure Active Directory, tra cui:

- Modifica **della password self-service** consente agli utenti finali o agli amministratori di cambiare la propria password scaduto o non scaduto senza la chiamata a un amministratore o l'helpdesk per il supporto.
- La reimpostazione **della password self-service** consente agli utenti finali o agli amministratori di reimpostare le password automaticamente senza la chiamata a un amministratore o l'helpdesk per il supporto. La reimpostazione della password self-service richiede Azure Active Directory Premium o base. Per ulteriori informazioni, vedere [edizioni di Azure Active Directory](active-directory-editions.md).
- **La reimpostazione della password di amministratore avviata** consente agli amministratori di reimpostazione password di un utente finale o un altro amministratore all'interno del portale classica Azure.
- **Report attività di gestione password** consentono agli amministratori approfondite attività registrazione e reimpostare la password che si verificano all'interno dell'organizzazione.
- **Password writeback** consente la gestione delle password locale dal cloud in modo che tutti gli scenari sopra possono essere eseguiti da o per conto di, federati o password utenti sincronizzati. Password writeback richiede Azure Active Directory Premium. Per ulteriori informazioni, vedere [Guida introduttiva di Azure Active Directory Premium](active-directory-get-started-premium.md).

> [AZURE.NOTE]
> Azure Active Directory Premium è disponibile per il cinese che usano l'istanza nel mondo di Azure Active Directory. Azure Active Directory Premium non è attualmente supportato nel servizio di Microsoft Azure gestito da 21Vianet in Cina. Per ulteriori informazioni, contattaci al [Forum di Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

Utilizzare i collegamenti seguenti per passare alla documentazione che si desidera in:

- [Panoramica: gestione delle password in Azure Active Directory](active-directory-passwords-how-it-works.md)
- [Modalità self-service la reimpostazione della password in Azure Active Directory: come abilitare, configurare e testare la reimpostazione della password self-service](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords)
- [Modalità self-service la reimpostazione della password in Azure Active Directory: personalizzare la password di reimpostazione in base alle esigenze](active-directory-passwords-customize.md)
- [Modalità self-service la reimpostazione della password in Azure Active Directory: procedure consigliate per la distribuzione e gestione](active-directory-passwords-best-practices.md)
- [Report di gestione delle password in Azure Active Directory: come visualizzare l'attività di gestione password nel tenant](active-directory-passwords-get-insights.md)
- [Password writeback: come configurare Azure Active Directory per gestire le password in locale](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
- [Risoluzione dei problemi di gestione delle password Azure Active Directory](active-directory-passwords-troubleshoot.md)
- [Domande frequenti per la gestione delle password Azure Active Directory](active-directory-passwords-faq.md)


## <a name="whats-next"></a>Che cos'è successiva

- [Amministrazione di Azure Active Directory](active-directory-administer.md)
- [Creare o modificare utenti in Azure Active Directory](active-directory-create-users.md)
- [Gestire i gruppi in Azure Active Directory](active-directory-manage-groups.md)
