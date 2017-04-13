<properties
    pageTitle="Accesso condizionato Azure Active Directory domande frequenti su | Microsoft Azure"
    description="Domande frequenti relative all'accesso condizionato "
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-conditional-access-faq"></a>Accesso condizionato Azure Active Directory domande frequenti

## <a name="which-applications-work-with-conditional-access-policies"></a>Quali applicazioni funzionano con i criteri di accesso condizionale?

**A:** Vedere l'argomento [condizionale applicazioni di accesso possibili sono supportate](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Criteri di accesso condizionale valgono per la collaborazione B2B e utenti guest?

**A:** Per gli utenti di collaborazione B2B sono applicati criteri. Tuttavia, in alcuni casi, un utente potrebbe non essere possibile soddisfare requisiti dei criteri se, ad esempio un'organizzazione non supporta l'autenticazione a più fattori. 

Il criterio attualmente non viene applicato per gli utenti guest di SharePoint. La relazione guest viene mantenuta all'interno di SharePoint. Gli utenti guest account non sono soggetti a access criteri al server di autenticazione. Accesso guest può essere gestito in SharePoint.

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Criteri di SharePoint Online si applicano anche a OneDrive for Business?

**A:** Sì.
 
## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Perché non è possibile impostare un criterio sulle applicazioni client, come Word o Outlook?

**A:** Un criterio di accesso condizionale imposta requisiti per l'accesso a un servizio e viene applicato quando si verifica l'autenticazione a tale servizio. Il criterio non è impostato direttamente in un'applicazione client. Se, tuttavia, viene applicato quando accede a un servizio. Ad esempio, un criterio impostato in SharePoint applica ai client la chiamata a SharePoint e si applica un criterio impostato su Exchange in Outlook.


## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Un criterio di accesso condizionale si applica agli account del servizio?

**A:** Criteri di accesso condizionale applicano a tutti gli account utente. Sono inclusi gli account utente utilizzati come account di servizio. In molti casi, un account di servizio che viene eseguita automaticamente non è in grado di soddisfare un criterio. Si tratta, ad esempio il caso, quando è necessario MFA. In questi casi, è possibile escluse account servizi da un criterio, con impostazioni di gestione dei criteri di accesso condizionato. Altre informazioni sull'applicazione di un criterio agli utenti di seguito.
