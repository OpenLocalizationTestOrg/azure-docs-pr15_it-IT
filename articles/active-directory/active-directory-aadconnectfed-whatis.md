<properties
    pageTitle="Azure AD Connect e federazione | Microsoft Azure"
    description="Questa pagina è il punto centrale per tutta la documentazione riguarda le operazioni di ADFS con Azure AD Connect"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="anandy"/>


# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect e federazione

Azure AD Connect consente di configurare la federazione con i locale AD FS e Azure Active Directory. Con la federazione di accesso, è possibile consentire agli utenti di accedere ai servizi di Azure Active Directory in base a con la propria password locale e, nella rete aziendale, senza dover immettere nuovamente la password. La federazione con ADFS consente di distribuire un nuovo o specificare un ADFS esistente in Windows Server 2012 R2 farm.

In questo argomento è la home page per informazioni sulla federazione riguardanti tutti gli altri argomenti relativi a tale funzionalità per i collegamenti di Azure AD Connect ed elenchi. Per i collegamenti per Azure AD Connect, vedere integrazione le identità locali con Azure Active Directory.

## <a name="azure-ad-connect---federation-topics"></a>Azure AD Connect - gli argomenti della federazione

| Argomento | Vengono illustrate e quando da leggere |
|:------|:-----------|
| **Azure AD Connect utente opzioni di accesso** ||
| [Informazioni sulle opzioni di accesso utente](active-directory-aadconnect-user-signin.md) | Descrizione delle varie opzioni di utente e relativa influenza esperienza utente di accesso Azure |
| **Installazione di ADFS Active Directory utilizzando Azure AD Connect**||
| [Prerequisiti](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) | Prerequisiti per una corretta installazione di ADFS tramite Azure AD Connect|
| [Configurare ADFS farm](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) | Come installare una nuova farm di ADFS con Azure AD Connect |
| **Modifica della configurazione di ADFS** | |
| [Il ripristino di protezione](active-directory-aadconnect-federation-management.md#reparing-the-trust) | Come ripristinare le relazioni di trust corrente tra locale ADFS e in Office 365 / Azure |
| [Aggiunta di un nuovo server ADFS](active-directory-aadconnect-federation-management.md#adding-a-new-ad-fs-server) | Espansione della farm di ADFS con ADFS server post iniziale installazione aggiuntive |
| [Aggiunta di un nuovo server AD FS WAP](active-directory-aadconnect-federation-management.md#adding-a-new-wap-server) | Espansione della farm di ADFS con ulteriori WAP server post installazione iniziale |
| [Aggiungere un nuovo dominio federato](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain) | Aggiungere un altro dominio federato con Azure Active Directory |
|**Attività successive all'installazione**||
| [Aggiungere il logo della società personalizzato / illustrazione](active-directory-aadconnect-federation-management.md#add-custom-company-logo-or-illustration)| Modificare l'esperienza di accesso specificando il logo personalizzato che verrà visualizzato nella pagina di accesso di ADFS |
| [Aggiungere una descrizione di accesso](active-directory-aadconnect-federation-management.md#add-sign-in-description) | La modifica di descrizione di accesso nella pagina di accesso di ADFS | 
| [Modifica ADFS richiedere regole](active-directory-aadconnect-federation-management.md#modifying-ad-fs-claim-rules) | Modificare o aggiungere regole attestazione in ADFS corrispondente alla configurazione di sincronizzazione di Azure AD Connect |


## <a name="additional-resources"></a>Risorse aggiuntive

* [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
* [Distribuzione di ADFS Active Directory in Azure](active-directory-aadconnect-azure-adfs.md)
* [Distribuzione di ADFS disponibilità Active Directory tra geografici in Azure con Azure il traffico Manager](active-directory-adfs-in-azure-with-azure-traffic-manager.md)


