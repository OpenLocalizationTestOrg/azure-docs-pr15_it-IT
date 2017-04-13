<properties
    pageTitle="Integrare Azure Active Directory single sign-on con SaaS App |  Microsoft Azure"
    description="Abilitare l'autenticazione single sign-on e gestione centralizzata dei accesso App SaaS Azure Active Directory di provisioning dell'utente. Cenni preliminari sull'integrazione di Azure Active Directory alle App SaaS."
    services="active-directory"
      keywords="integrazione di Azure Active Directory con SaaS App"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/30/2016"
    ms.author="curtand"/>

# <a name="integrate-azure-active-directory-single-sign-on-with-saas-apps"></a>Integrare Azure Active Directory single sign-on con SaaS App  

> [AZURE.SELECTOR]
- [Portale di Azure](active-directory-enterprise-apps-manage-sso.md)
- [Portale classica Azure](active-directory-sso-integrate-saas-apps.md)

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Per iniziare a configurare single sign-on per un'applicazione che sta caricarli nella propria organizzazione, si utilizzeranno una directory esistente di Azure Active Directory (Azure Active Directory). È possibile utilizzare una directory di Azure Active Directory che si ottengono tramite Microsoft Azure, Office 365 o Windows Intune. Se si dispone di due o più di questi piani, vedere [amministrare la directory di Azure Active Directory](active-directory-administer.md) per determinare quella da utilizzare.

## <a name="authentication"></a>Autenticazione

Per le applicazioni che supporta il SAML 2.0 WS-Federation o connettersi OpenID protocolli, ottimali di Azure Active Directory per stabilire relazioni di trust certificati di firma. Per ulteriori informazioni, vedere [gestione dei certificati per single sign-on federati](active-directory-sso-certs.md).

Per le applicazioni che supportano solo HTML basata su moduli accesso aggiuntivo, Azure Active Directory utilizza "archiviazione password" per stabilire relazioni di trust. In questo modo gli utenti dell'organizzazione accedere automaticamente a un'applicazione di SaaS da Azure Active Directory utilizzando le informazioni sull'account utente dall'applicazione SaaS. Azure Active Directory raccoglie e sicuro archivia le informazioni sull'account utente e la password correlata. Per ulteriori informazioni, vedere [basate su Password servizio single sign-on](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

## <a name="authorization"></a>Autorizzazione

Un account di provisioning consente all'utente di essere autorizzati a utilizzare un'applicazione dopo che sono autenticati tramite il single sign-on. Il provisioning dell'utente può essere eseguito manualmente o in alcuni casi è possibile aggiungere e rimuovere le informazioni utente dall'app SaaS in base alle modifiche apportate in Azure Active Directory. Per ulteriori informazioni sull'utilizzo di connettori di Azure Active Directory esistenti per il provisioning automatico, vedere [automatizzato utente il provisioning e deselezionare il provisioning per applicazioni SaaS](active-directory-saas-app-provisioning.md).

In caso contrario, è possibile manualmente aggiungere informazioni per l'utente a un'app o utilizzare altre soluzioni provisioning sono disponibili sul mercato.

## <a name="access"></a>Accesso

Azure Active Directory offre diversi modi personalizzabili per distribuire applicazioni per gli utenti finali dell'organizzazione. Non è bloccato in qualsiasi distribuzione specifica o una soluzione di accesso. È possibile utilizzare [la soluzione che meglio si adatta alle proprie esigenze](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

## <a name="additional-considerations-for-applications-already-in-use"></a>Considerazioni per le applicazioni già in uso

Configurare single sign in per un'applicazione che utilizza l'organizzazione ha già è un processo diverso dal processo di creazione di nuovi account per una nuova applicazione. Esistono un paio di passaggi preliminari inclusi: mapping delle identità utente dell'applicazione alle identità di Azure Active Directory e la comprensione come utenti saranno l'esperienza accesso a un'applicazione dopo l'integrazione.

> [AZURE.NOTE] Per configurare Single Sign-on per un'applicazione esistente, è necessario disporre dei diritti di amministratore globale in entrambi Azure AD e sull'applicazione SaaS.

### <a name="mapping-user-accounts"></a>Il mapping degli account utente

Identità dell'utente sono in genere un identificatore univoco che può essere un indirizzo di posta elettronica o il nome dell'entità utente (UPN). Sarà necessario collegamento (mappa) ogni identità dell'utente dell'applicazione le rispettive identità di Azure Active Directory. Esistono due modi per eseguire questa operazione a seconda di come il requisito di autenticazione l'applicazione.

Per ulteriori informazioni sul mapping identità di applicazione delle identità di Azure Active Directory, vedere [personalizzazione delle attestazioni rilasciate in token SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) e [mapping degli attributi di personalizzazione per il provisioning](active-directory-saas-customizing-attribute-mappings.md).

### <a name="understanding-the-users-log-in-experience"></a>Informazioni di accesso dell'utente esperienza

Quando si integra Single Sign-on per un'applicazione che è già in uso, è importante tenere presente che interesserà l'esperienza utente. Per tutte le applicazioni, gli utenti inizierà usando le proprie credenziali di Azure AD effettuare l'accesso. È possibile che devono utilizzare un portale diverso per accedere alle applicazioni.

Single Sign-on per alcune applicazioni può essere eseguita su accesso dell'applicazione nell'interfaccia, ma per le altre applicazioni, l'utente avrà elaborata un portale centrale, ad esempio ([App personali](http://myapps.microsoft.com) o [Office 365](http://portal.office.com/myapps)). Ulteriori informazioni sui diversi tipi di esperienza utente in [Novità](active-directory-appssoaccess-whatis.md)di access di applicazione e single sign-on con Azure Active Directory e Single Sign-on.

Un altro strumento prezioso è *Suppressing utente acconsente* nell'articolo [orientamenti sviluppatori](active-directory-applications-guiding-developers-for-lob-applications.md) .

## <a name="next-steps"></a>Passaggi successivi


Per le applicazioni SaaS disponibili nella raccolta App, Azure Active Directory fornisce una serie di [esercitazioni su come integrare SaaS App](active-directory-saas-tutorial-list.md).

Se l'app non è presente nella raccolta App, è possibile [aggiungerlo alla raccolta Azure Active Directory App come un'applicazione personalizzata](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

C'è molto più dettagli su tutti questi problemi nella raccolta Azure.com a partire da [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory.](active-directory-appssoaccess-whatis.md).

## <a name="see-also"></a>Vedere anche

- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
