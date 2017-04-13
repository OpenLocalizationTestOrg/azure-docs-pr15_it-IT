<properties
    pageTitle="Domande frequenti su Azure Active Directory | Microsoft Azure"
    description="Azure Active Directory nelle domande frequenti vengono fornite le risposte alle domande in combinazione con accesso Azure e Azure Active Directory, gestione e l'applicazione accesso tramite password."
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
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="markusvi"/>

# <a name="azure-active-directory-faq"></a>Domande frequenti su Azure Active Directory

Azure Active Directory è un'identità esaustivo come una soluzione di servizio (IDaaS) che si estende su tutti gli aspetti dell'identità, gestione di access e sicurezza.


Per ulteriori informazioni, vedere [Azure Active Directory?](active-directory-whatis.md).



## <a name="accessing-azure-and-azure-active-directory"></a>Accesso Azure e Azure Active Directory


**D: Perché ricevo "Nessuna sottoscrizione trovata" quando si tenta di accedere Azure Active Directory nel portale di classica Azure (https://manage.windowsazure.com)?**

**A:** Accedere al portale di classico Azure richiede ogni utente disponga delle autorizzazioni per un abbonamento Azure. Se si dispone di un pagamento a Office 365 o Azure Active Directory passare a [http://aka.ms/accessAAD](http://aka.ms/accessAAD) per un passaggio di attivazione unica, in caso contrario è necessario attivare un abbonamento a pagamento o un intero [Azure versione di valutazione](https://azure.microsoft.com/pricing/free-trial/) . 

Per ulteriori informazioni, vedere:

- [Modalità di Azure sottoscrizioni associate Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Gestire la directory per l'abbonamento a Office 365 in Azure](active-directory-manage-o365-subscription.md)

---

**D: qual è la relazione tra Azure Active Directory, Office 365 e Azure?**

**A:** Azure Active Directory offre funzionalità di identità e accesso comuni a tutti i servizi online Microsoft. Se si utilizza Office 365, Microsoft Azure, Intune o gli altri utenti, si utilizza già un annuncio Azure di attivare sign-on e accedere a gestione per tutti questi servizi. 

Infatti, tutti gli utenti che sono abilitati per Microsoft Online services sono definiti come account utente in una o più istanze di Azure Active Directory. È possibile abilitare queste funzionalità account gratuitamente Azure Active Directory, ad esempio l'accesso cloud dell'applicazione.
 
Inoltre, Azure Active Directory pagato servizi (ad esempio: basic Azure Active Directory, Premium, EMS, e così via) complementari Online servizi, ad esempio Office 365 e Microsoft Azure con le soluzioni di gestione e la sicurezza scala completa.


---



## <a name="getting-started-with-hybrid-azure-ad"></a>Guida introduttiva a ibrido Azure Active Directory


**D: come è possibile collegare la directory locale di Azure Active Directory?**

**A:** È possibile connettersi Azure Active Directory utilizzando **Azure AD Connect**directory locale. 

Per ulteriori informazioni, vedere [integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).


---

**D: come configurare Single Sign-on tra la directory locale e le applicazioni cloud?**

**A:** È sufficiente configurare Single Sign-on quello Azure Active directory locale. Come si accede alle applicazioni cloud tramite Azure Active Directory, il servizio unità automaticamente gli utenti autenticati correttamente con le proprie credenziali locale.

L'implementazione di Single Sign-on locali può essere ottenuto facilmente con le soluzioni di federazione, ad esempio ADFS o configurando la sincronizzazione delle password hash. È possibile distribuire facilmente entrambe le opzioni mediante la configurazione guidata di Azure AD Connect.
  

Per ulteriori informazioni, vedere [integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
  

---

**D: Azure Active Directory fornire un portale self-service per gli utenti dell'organizzazione?**

**A:** Sì, Azure Active Directory consente con il [comando Azure Active Directory](http://myapps.microsoft.com) per utente modalità self-service e accesso alle applicazioni. Se si è un cliente di Office 365, è possibile trovare molte delle stesse capacità di nel portale di Office 365. 

Per ulteriori informazioni, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md). 



---

**D: Azure AD suggerimenti per la gestione dell'infrastruttura locale?**

**A:** Sì, viene eseguita. La versione Azure Active Directory Premium consente di **Connettere integrità**. Azure Active Directory connettersi integrità consente di eseguire il monitoraggio e approfondire l'infrastruttura di identità locale e i servizi di sincronizzazione.  

Per ulteriori informazioni, vedere [monitorare le locale identità dell'infrastruttura e sincronizzazione i servizi nel cloud](active-directory-aadconnect-health.md).  

---

## <a name="password-management"></a>Gestione delle password

**D: è possibile utilizzare Azure Active Directory password writeback senza la sincronizzazione delle password? (Nello specifico, preferisce utilizzare Azure Active Directory SSPR con writeback password ma non si desidero le password archiviate nel cloud?)**

**A:** Non è necessario sincronizzare le password di Active Directory di Azure Active Directory per abilitare writeback. In un ambiente federato Single Sign-on di Azure Active Directory si basa su directory locale di autenticazione dell'utente. Questo scenario non richiede la password locale per tenere traccia in Azure Active Directory.

---

**D: come tempo una password per la scrittura nuovamente su Active Directory locale?**

**A:** Password writeback funziona in tempo reale. 

Per ulteriori informazioni, vedere [Guida introduttiva alla gestione delle Password](active-directory-passwords-getting-started.md) 


---

**D: è possibile usare la password writeback con password che vengono gestite da un amministratore?**

**A:** Sì, se si dispone di password writeback attivata, le operazioni di password eseguite da un amministratore vengono scritte per l'ambiente locale.  

Per altre risposte alla password ad altre domande, vedere [Domande frequenti gestione Password](active-directory-passwords-faq.md).

---

## <a name="application-access"></a>Accesso alle applicazioni


**D: dove è possibile trovare un elenco di applicazioni che sono già integrati con Azure Active Directory e le relative funzionalità?**

**A:** Azure Active Directory ha più 2600 applicazioni già integrate da Microsoft, il provider di servizi di applicazione o partner. Tutte le applicazioni già integrate supportano Single Sign-on. Single Sign-on consente di utilizzare le credenziali dell'organizzazione per accedere alle app. Alcune delle applicazioni supportano automatizzato il provisioning e deselezionare il provisioning

Per un elenco completo delle applicazioni di pre-integrate, vedere [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).


---

**D: cosa fare se l'applicazione che è necessario non è presente in marketplace Azure Active Directory?**

**A:** Con Azure Active Directory Premium, è possibile aggiungere e configurare applicazioni desiderato. A seconda delle funzionalità dell'applicazione e le preferenze, è possibile configurare Single Sign-on e il provisioning automatico.  

Per ulteriori informazioni, vedere:

- [Configurazione single sign-on per le applicazioni che non sono presenti nella raccolta di applicazione di Azure Active Directory](active-directory-saas-custom-apps.md)
- [Utilizzo di SCIM per consentire il provisioning automatico degli utenti e gruppi da Azure Active Directory per applicazioni](active-directory-scim-provisioning.md) 


---

**D: come gli utenti accedere nelle applicazioni che utilizzano Azure Active Directory?**
 
**A:** Azure Active directory offre diversi modi per gli utenti di visualizzare e accedere alle applicazioni, ad esempio:

- Il pannello di accesso di Azure Active Directory

- Avvio dell'applicazione di Office 365

- Accesso diretto alle App federati

- Collegamenti complete federato, basate su password o App esistente

Per ulteriori informazioni, vedere [distribuzione Azure Active Directory integrata applicazioni agli utenti](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).


---

**D: quali sono i diversi metodi Azure Active Directory consente l'autenticazione e single sign-on alle applicazioni?**
 
**A:** Azure Active Directory supporta molti protocolli standard per l'autenticazione e l'autorizzazione ad esempio SAML 2.0, OpenID connettersi, OAuth 2.0 e WS-Federation. Azure Active Directory supporta anche archiviazione password e funzionalità di accesso automatica per le applicazioni che supportano solo l'autenticazione basata su moduli.  

Per ulteriori informazioni, vedere:

- [Scenari di autenticazione per Azure Active Directory](active-directory-authentication-scenarios.md)

- [Protocolli di autenticazione Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)

- [Il servizio single sign-on di lavoro di Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)


---

**D: è possibile aggiungere applicazioni è in esecuzione in locale?**

**A:** Proxy di applicazione Azure Active Directory offre un accesso semplice e sicuro alle applicazioni web locale che si è scelto. È possibile accedere a queste applicazioni nello stesso modo che si accede alle App SaaS di Azure Active Directory. Non è necessario per una connessione VPN o la modifica dell'infrastruttura di rete.  

Per ulteriori informazioni, vedere [come fornire l'accesso remoto sicuro alle applicazioni locale](active-directory-application-proxy-get-started.md).


--- 

**D: come richiedono MFA per gli utenti che accedono a una determinata applicazione**

**A:** Con l'accesso condizionale Azure Active Directory, è possibile assegnare un criterio di accesso univoco per ogni applicazione. Il criterio, è possibile richiedere MFA in qualsiasi momento oppure quando gli utenti non si è connessi alla rete locale.  

Per ulteriori informazioni, vedere [protezione l'accesso a Office 365 e altre App connessi a Azure Active Directory](active-directory-conditional-access.md).


---

**D: che cos'è automatica il Provisioning dell'utente per App SaaS?**

**A:** Azure Active Directory consente di automatizzare la creazione, manutenzione e rimozione delle identità utente in molte applicazioni comuni cloud (SaaS). 

Per ulteriori informazioni, vedere [automatizzare utente il Provisioning e Deprovisioning alle applicazioni SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)

---



