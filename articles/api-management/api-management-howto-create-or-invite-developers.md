<properties 
    pageTitle="Come gestire gli account utente di Azure API Management | Microsoft Azure" 
    description="Informazioni su come creare o invitare gli utenti in Azure API Management" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Come gestire gli account utente di gestione delle API di Azure

In Gestione API gli sviluppatori sono gli utenti delle API esposte tramite Gestione API. Questa guida illustra come creare e invitare gli sviluppatori a utilizzare le API e prodotti apportate renderli disponibili con l'istanza di gestione delle API. Per informazioni sulla gestione degli account utente a livello di programmazione, vedere la documentazione di [entità utente](https://msdn.microsoft.com/library/azure/dn776330.aspx) nel riferimento [API REST di gestione](https://msdn.microsoft.com/library/azure/dn776326.aspx) .

## <a name="create-developer"> </a>Creare un nuovo sviluppatore

Per creare un nuovo sviluppatore, fare clic su **Gestisci** nel portale classica di Azure per il servizio di gestione dell'API. Verrà visualizzato il portale di gestione delle API publisher. Se non è ancora stato creato un'istanza di servizio di gestione delle API, vedere [creare un'istanza di servizio di gestione delle API][] nell'esercitazione [Introduzione Azure API Management][] .

![Portale di Publisher][api-management-management-console]

Fare clic su **utenti** dal menu **Gestione API** sul lato sinistro e quindi fare clic su **Aggiungi utente**.

![Creazione di sviluppo][api-management-create-developer]

Immettere il **messaggio di posta elettronica**, la **Password**e **nome** per gli sviluppatori di nuovo e fare clic su **Salva**.

![Creazione di sviluppo][api-management-add-new-user]

Per impostazione predefinita, gli account di sviluppo appena creato **attivi**e associati al gruppo di **sviluppatori** .

![Nuovo sviluppatore][api-management-new-developer]

Account di sviluppo che lo stato **attivo** possono essere utilizzati per accedere a tutti di API per le quali sono le sottoscrizioni. Per associare lo sviluppatore appena creato gruppi aggiuntivi, vedere [come associare gruppi con gli sviluppatori][].

## <a name="invite-developer"> </a>Invitare uno sviluppatore

Per invitare uno sviluppatore, fare clic su **utenti** dal menu **Gestione API** sul lato sinistro e quindi fare clic su **Invita utenti**.

![Invita per sviluppatori][api-management-invite-developer]

Immettere l'indirizzo di posta elettronica e nome dello sviluppatore e fare clic su **Invita**.

![Invita per sviluppatori][api-management-invite-developer-window]

Viene visualizzato un messaggio di conferma, ma lo sviluppatore appena invitato non viene visualizzato nell'elenco fino a dopo aver accettato l'invito. 

![Invitare conferma][api-management-invite-developer-confirmation]

Quando uno sviluppatore è invitato, viene inviato un messaggio di posta elettronica per gli sviluppatori. Questo messaggio di posta elettronica viene generato utilizzando un modello e personalizzabile. Per ulteriori informazioni, vedere [configurare modelli di posta elettronica][].

Dopo aver accettato l'invito, l'account diventa attiva.

## <a name="block-developer"></a> Disattivare o riattivare un account per sviluppatori

Per impostazione predefinita, gli account di sviluppo appena creato o invitati sono **attivi**. Per disattivare un account di sviluppo, fare clic su **Blocca**. Per riattivare un account di sviluppo bloccati, fare clic su **Attiva**. Un account di sviluppo bloccati non possa accedere al portale per sviluppatori o chiamata alcuna API. Per eliminare un account utente, fare clic su **Elimina**.

![Sviluppo di blocco][api-management-new-developer]

## <a name="reset-a-user-password"></a>Reimpostare una password utente

Per reimpostare la password per un account utente, fare clic sul nome dell'account.

![Reimpostazione password][api-management-view-developer]

Fare clic su **Reimposta password** per inviare un collegamento all'utente di reimpostare la password.

![Reimpostazione password][api-management-reset-password]

Per lavorare a livello di programmazione con gli account utente, vedere la documentazione di [entità utente](https://msdn.microsoft.com/library/azure/dn776330.aspx) nel riferimento [API REST di gestione](https://msdn.microsoft.com/library/azure/dn776326.aspx) . Per reimpostare una password dell'account utente su un valore specifico, è possibile utilizzare l'operazione di [aggiornamento di un utente](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) e specificare la password desiderata.

## <a name="pending-verification"></a>In attesa di verifica

![In attesa di verifica][api-management-pending-verification]

## <a name="next-steps"> </a>Passaggi successivi

Dopo aver creato un account di sviluppo, è possibile associare ai ruoli e sottoscrizione di prodotti e le API. Per ulteriori informazioni, vedere [come creare e utilizzare gruppi][].


[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png
[]: ./media/api-management-howto-create-or-invite-developers/.png



[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[Come creare e utilizzare i gruppi]: api-management-howto-create-groups.md
[Procedura per associare gruppi agli sviluppatori]: api-management-howto-create-groups.md#associate-group-developer

[Guida introduttiva di Azure API Management]: api-management-get-started.md
[Creare un'istanza di servizio di gestione delle API]: api-management-get-started.md#create-service-instance
[Configurare modelli di posta elettronica]: api-management-howto-configure-notifications.md#email-templates