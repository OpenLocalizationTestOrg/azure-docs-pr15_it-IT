<properties
   pageTitle="Ruoli di PIM | Microsoft Azure"
   description="Informazioni su quali ruoli vengono utilizzati per le identità privilegi con l'estensione di Azure privilegi di gestione delle identità."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# <a name="roles-in-azure-ad-privileged-identity-management"></a>Ruoli di Azure Active Directory con privilegi di gestione delle identità

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

È possibile assegnare agli utenti dell'organizzazione a diversi ruoli amministrativi in Azure Active Directory. Queste assegnazioni di ruolo controllano quali attività, ad esempio aggiungere o rimuovere utenti o modificare le impostazioni del servizio, gli utenti possono eseguire in Azure Active Directory, Office 365 e altri servizi Online Microsoft e applicazioni connesse.  

Un amministratore globale può eseguire l'aggiornamento quali utenti sono **definitivamente** assegnate ai ruoli Azure Active Directory, uso dei cmdlet di PowerShell ad esempio `Add-MsolRoleMember` e `Remove-MsolRoleMember`, o tramite il portale classico come descritto in [assegnazione di ruoli di amministratore di Azure Active Directory](active-directory-assign-admin-roles.md).

Azure Active Directory privilegi di gestione delle identità (PIM) consente di gestire i criteri per l'accesso con privilegi per gli utenti in Azure Active Directory. PIM viene assegnato agli utenti uno o più ruoli in Azure Active Directory ed è possibile assegnare un' persona a essere definitivamente il ruolo, o idoneo per il ruolo. Quando un utente in modo permanente assegnato a un ruolo o attiva un'assegnazione di ruolo idoneo, quindi possano gestire Azure Active Directory, Office 365 e altre applicazioni con le autorizzazioni assegnate ai ruoli.

Non vi è alcuna differenza nella finestra di accesso assegnato a un utente con permanente rispetto a un'assegnazione di ruolo idoneo. L'unica differenza è che alcuni utenti non necessario che l'accesso sempre questa impostazione. Effettuati idonei per il ruolo e possibile attivare e disattivare ogni volta che è necessario.

## <a name="roles-managed-in-pim"></a>Ruoli gestiti in PIM

Privilegi di gestione delle identità consente di assegnare utenti ai ruoli di amministratore comuni, tra cui:


- **Amministratore globale** (noto anche come amministratore della società) ha accesso a tutte le funzioni amministrative. È possibile impostare più di un amministratore globale dell'organizzazione. La persona che si iscrive per acquistare Office 365 automaticamente diventa un amministratore globale.
- **Amministratore ruoli privilegi** gestisce Azure Active Directory PIM e aggiorna assegnazioni di ruolo per altri utenti.  
- **Amministratore fatturazione** effettua acquisti, gestisce abbonamenti, gestisce ticket di supporto ed esegue il monitoraggio dell'integrità dei servizi.
- **Amministratore password** Reimposta la password, gestisce le richieste di servizio ed esegue il monitoraggio dell'integrità dei servizi. Gli amministratori password sono limitati alla reimpostazione della password per gli utenti.
- **Amministratore del servizio** gestisce le richieste di servizio e monitor assistenza sanitaria.

  > [AZURE.NOTE] Se si utilizza Office 365, quindi prima di assegnare il ruolo di amministratore di servizio a un utente, prima di tutto assegnare all'utente le autorizzazioni amministrative a un servizio, ad esempio Exchange Online.

- **Amministratore Gestione utenti** Reimposta la password, esegue il monitoraggio dell'integrità dei servizi e gestisce gli account utente, gruppi di utenti e le richieste di servizi. L'amministratore Gestione utenti non è possibile eliminare un amministratore globale, creare altri ruoli di amministratore o reimpostare la password per la fatturazione, globali e amministratori dei servizi.
- **Amministratore di Exchange** ha accesso amministrativo a Exchange Online tramite l'Exchange admin center EAC () e può eseguire pressoché qualsiasi attività in Exchange Online.
- **Amministratore di SharePoint** ha accesso amministrativo a SharePoint Online tramite l'interfaccia di amministrazione di SharePoint Online e può eseguire pressoché qualsiasi attività in SharePoint Online.
- **Skype per amministratore aziendale** dispone di accesso amministrativo a Skype for Business tramite Skype per Business admin center e può eseguire pressoché qualsiasi attività in Skype for Business Online.

Leggere questi articoli per ulteriori informazioni [sull'assegnazione di ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles.md) e [l'assegnazione di ruoli di amministratore in Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


Da PIM, è possibile [assegnare i ruoli a un utente](active-directory-privileged-identity-management-how-to-add-role-to-user.md) in modo che l'utente può [attivare il ruolo quando necessario](active-directory-privileged-identity-management-how-to-activate-role.md).

Se si desidera assegnare un altro utente l'accesso a gestire PIM stesso, i ruoli che PIM richiede all'utente ulteriori informazioni sono vedere [come concedere l'accesso a PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Ruoli non gestiti in PIM

Ruoli all'interno di Exchange Online o SharePoint Online, ad eccezione di quelli indicati in precedenza, non vengono rappresentati in Azure Active Directory e pertanto non sono visibili in PIM. Per ulteriori informazioni sulla modifica delle assegnazioni di ruolo diffusamente questi servizi di Office 365, vedere [autorizzazioni in Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Sottoscrizioni di Azure e gruppi di risorse anche non vengono rappresentati in Azure Active Directory. Per gestire le sottoscrizioni di Azure, informazioni su [come aggiungere o modificare i ruoli di amministratore Azure](../billing-add-change-azure-subscription-administrator.md) e per ulteriori informazioni su Azure RBAC vedere [Controllo dell'accesso Azure Role-Based](role-based-access-control-configure.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Ruoli degli utenti e l'accesso
Per alcune applicazioni e servizi Microsoft, l'assegnazione di un utente a un ruolo potrebbe non essere sufficiente per consentire all'utente di essere un amministratore.

Accesso al portale di classica Azure richiede che all'utente di essere un amministratore di servizio o condivisa su un abbonamento a Azure, anche se l'utente non necessarie per gestire le sottoscrizioni Azure.  Per gestire le impostazioni di configurazione per Azure Active Directory nel portale di classica, ad esempio, un utente deve essere un amministratore globale in Azure Active Directory e un CO-amministratore di abbonamento a un abbonamento a Azure.  Per informazioni su come aggiungere utenti a abbonamenti Azure, informazioni su [come aggiungere o modificare i ruoli di amministratore Azure](../billing-add-change-azure-subscription-administrator.md).

Accesso a Microsoft Online Services possono richiedere all'utente di essere assegnati anche una licenza prima di poter aprire il portale del servizio o eseguire attività amministrative.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Assegnare una licenza a un utente in Active Directory Azure

1. Accedere a [portal Azure classica] (http://manage.windowsazure.com) con un account di amministratore globale o un account di amministratore condivisa.
2. Selezionare **Tutti gli elementi** dal menu principale.
3. Selezionare la directory che si desidera lavorare e che abbiano le licenze è associate.
4. Selezionare **licenze**. Verrà visualizzato l'elenco delle licenze disponibili.
5. Selezionare il piano di licenza che contiene le licenze che si desidera distribuire.
6. Selezionare **assegnare agli utenti**.
7. Selezionare l'utente che si desidera assegnare una licenza.
8. Fare clic sul pulsante **Assegna** .  L'utente può accedere al Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
