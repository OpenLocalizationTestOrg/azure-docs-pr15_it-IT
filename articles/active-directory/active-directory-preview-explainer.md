<properties
    pageTitle="Explainer di anteprima Azure Active Directory | Microsoft Azure"
    description="Un argomento che illustra le differenze tra l'anteprima di Azure Active Directory nel portale di Azure e Azure Active Directory nel portale di classica."
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
    ms.date="09/12/2016"
    ms.author="curtand"/>


# <a name="preview-of-the-azure-active-directory-management-experience-in-the-azure-portal"></a>Anteprima dell'ambiente di gestione dei Azure Active Directory nel portale di Azure

L'esperienza di gestione di Azure Active Directory (Azure Active Directory) è in anteprima nel portale di Azure. È possibile provarlo indietro effettuando l'accesso al [portale di Azure](https://portal.azure.com) come un amministratore globale della directory. Quindi selezionare Azure Active Directory nell'elenco servizi se è visibile o selezionare **altri servizi** per visualizzare l'elenco di tutti i servizi. Non è necessaria una sottoscrizione Azure per l'utilizzo di Azure AD management esperienza nel portale di Azure.


## <a name="capabilities-of-the-preview-experience"></a>Funzionalità dell'esperienza di anteprima

L'esperienza di anteprima consente di gestire molte risorse di directory, ad esempio gli utenti, gruppi e le applicazioni, nonché le impostazioni directory, nel portale di Azure. Stiamo migliorando l'esperienza per includere tutte le funzionalità che sono presenti di Azure AD management esperienza nel [portale classica Azure](https://manage.windowsazure.com). Nel frattempo, esistono alcune gestione directory attività che è comunque necessario completare nel portale di classica.

## <a name="manage-the-same-azure-ad-tenants"></a>Gestire lo stessa tenant di Azure Active Directory

L'esperienza di anteprima legge e scrive stesso tenant di Azure Active Directory come il portale classico e interfaccia di amministrazione di Office 365. Le modifiche apportate in uno di questi portali risulteranno disponibili in tutte le altre.

## <a name="use-the-same-authorization-logic"></a>Utilizzare la stessa logica di autorizzazione

L'esperienza di anteprima viene utilizzata la stessa logica di autorizzazione come client di Active Directory esistente. Gli utenti siano autorizzati per apportare modifiche alle risorse di directory in base al ruolo ricoperto directory, ad esempio amministratore globale, Amministratore utenti, amministratore password. Con un ruolo di risorse Azure o una sottoscrizione di Azure non autorizzare un utente per gestire le risorse di directory. Per ulteriori informazioni ruoli di gestione di Azure Active Directory, vedere [assegnazione di ruoli di amministratore di Azure Active Directory](active-directory-assign-admin-roles.md). 

L'esperienza di anteprima è ottimizzato per gli amministratori globali. Se si utilizza l'esperienza di anteprima mentre effettuato l'accesso come utente che non è un amministratore globale, potrebbe essere un'esperienza peggiore. Ad esempio, potrebbe essere possibile selezionare un pulsante che permette di avviare un'attività che non è possibile completare nella directory. Stiamo migliorando l'esperienza breve.
 
## <a name="tell-us-what-you-think"></a>Inviare commenti e suggerimenti si ritiene che

È possibile fornire commenti e suggerimenti sull'esperienza anteprima nella sezione Amministrazione portale dell' [area feedback Azure Active Directory](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&filter=alltypes&sort=lastpostdesc).
