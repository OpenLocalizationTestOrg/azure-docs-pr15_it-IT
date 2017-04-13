<properties
   pageTitle="Risoluzione dei problemi: 'Active Directory' elemento è mancanti o non disponibile | Microsoft Azure "
   description="Cosa fare quando la voce di menu Active Directory non viene visualizzata nel portale di gestione di Azure."
   services="active-directory"
   documentationCenter="na"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>Risoluzione dei problemi: 'Active Directory' elemento è mancanti o non disponibile

Molte delle istruzioni per l'uso di servizi e le funzionalità di Azure Active Directory iniziano con "Accedere al portale di gestione Azure e fare clic su **Active Directory"**. Ma cosa fare se non viene visualizzato l'elemento di estensione o il menu di Active Directory o se è contrassegnata come **Non disponibile**? In questo argomento è consentire agli utenti. Descrive le condizioni di **Active Directory** non viene visualizzato o non è disponibile e viene spiegato come procedere.

## <a name="active-directory-is-missing"></a>Active Directory è mancante

In genere, un elemento di **Active Directory** viene visualizzato nel menu di spostamento sinistro. Le istruzioni visualizzate nelle procedure di Azure Active Directory presuppongono che questo elemento si trova la visualizzazione.

![Schermata: Active Directory in Azure](./media/active-directory-troubleshooting/typical-view.png)

L'elemento di Active Directory viene visualizzato nel menu di spostamento a sinistra quando una delle condizioni seguenti è vera. In caso contrario, l'elemento non viene visualizzato.

* L'utente corrente firmato il con un account Microsoft (precedentemente noto come Windows Live ID).

    OR

* Tenant Azure ha una directory e del conto corrente è un amministratore della directory.

    OR

* Tenant Azure ha almeno uno spazio dei nomi di controllo dell'accesso di Azure Active Directory (ACS). Per ulteriori informazioni, vedere [Namespace di controllo di accesso](https://msdn.microsoft.com/library/azure/gg185908.aspx).

    OR

* Azure tenant include almeno un provider di autenticazione a più fattori Azure. Per ulteriori informazioni, vedere [Amministrazione provider di autenticazione a più fattori Azure](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

Per creare uno spazio dei nomi di controllo dell'accesso o un provider di autenticazione a più fattori, fare clic su **+ Nuovo** > **Servizi App** > **Active Directory**.

Per ottenere i diritti amministrativi in una directory, che un amministratore di assegnare un ruolo di amministratore al proprio account. Per informazioni dettagliate, vedere [assegnazione di ruoli di amministratore](active-directory-assign-admin-roles.md).

## <a name="active-directory-is-not-available"></a>Active Directory non è disponibile

Quando si fa clic su **+ Nuovo** > viene visualizzato un elemento di **Active Directory** **App servizi**. In particolare, l'elemento di Active Directory viene visualizzato quando una delle funzionalità di Active Directory, ad esempio Directory, il controllo dell'accesso o il Provider di autenticazione a più fattori, sono disponibile per l'utente corrente.

Tuttavia, durante il caricamento della pagina, l'elemento è disattivata e viene contrassegnata come **Non disponibile**. Si tratta di uno stato temporaneo. Se si attendere alcuni secondi, l'elemento diventa disponibile. Se il ritardo viene prolungato, aggiornare la pagina web spesso risolve il problema.

![Schermata: Active Directory non è disponibile](./media/active-directory-troubleshooting/not-available.png)
