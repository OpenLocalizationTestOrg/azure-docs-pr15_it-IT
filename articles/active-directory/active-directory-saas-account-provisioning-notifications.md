<properties
    pageTitle="Notifiche di provisioning degli account | Microsoft Azure"
    description="Informazioni su come assicurarsi che la notifica di problemi relativi all'utente di provisioning che richiedono attenzione abilitando le notifiche del provisioning degli account."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="account-provisioning-notifications"></a>Account il Provisioning di notifiche

Con il provisioning dell'utente, è possibile automatizzare il processo di gestione degli utenti nelle applicazioni di terze parti SaaS. <br>
Mentre si tratta di un processo automatico, l'interazione con questo processo di tanto in tanto è obbligatorio. <br>
Si tratta, ad esempio il caso, quando la password dell'account è stato configurato per lo scambio di dati con una terza parte SaaS applicazione è scaduto. 

Abilitando le notifiche del provisioning degli account, è possibile assicurarsi che la notifica di problemi relativi all'utente di provisioning che richiedono l'attenzione dell'utente.

Attivare o disattivare le notifiche come parte dell'utente di configurazione per una terza parte applicazione SaaS di provisioning del provisioning degli account.

![Il Provisioning dell'utente][1] 



Per attivare le notifiche del provisioning degli account, selezionare la casella di controllo correlato nella pagina di finestra di dialogo di **Conferma** e quindi digitare l'alias di posta elettronica del destinatario.

![Account il Provisioning di notifiche][2]
 


È possibile immettere una lista di distribuzione come destinatario; Tuttavia, è importante tenere presente che il messaggio di notifica contiene collegamenti ai report solo accessibili per gli amministratori di Azure Active Directory.

Se si dispone abilitate le notifiche del provisioning degli account, si riceveranno messaggi di posta elettronica sui problemi critici che riguardano il provisioning dell'utente. Tuttavia, per evitare un sovraccarico di posta elettronica, si riceveranno solo una e-mail di notifica al giorno per ogni applicazione SaaS che è abilitata l'e-mail di notifica.


##<a name="related-articles"></a>Articoli correlati

- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
- [Automatizzare utente Provisioning/Deprovisioning alle App SaaS](active-directory-saas-app-provisioning.md)
- [Personalizzazione dei mapping degli attributi per il Provisioning dell'utente](active-directory-saas-customizing-attribute-mappings.md)
- [Scrittura di espressioni per mapping degli attributi](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Definizione dell'ambito filtri per il Provisioning dell'utente](active-directory-saas-scoping-filters.md)
- [Utilizzo di SCIM per consentire il provisioning automatico degli utenti e gruppi da Azure Active Directory per applicazioni](active-directory-scim-provisioning.md)
- [Elenco di esercitazioni su come integrare SaaS App](active-directory-saas-tutorial-list.md)



<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png