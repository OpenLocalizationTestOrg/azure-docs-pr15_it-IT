<properties
    pageTitle="Single sign-on gestione App aziendali nell'anteprima di Azure Active Directory | Microsoft Azure"
    description="Informazioni su come gestire l'accesso singolo per App aziendali con Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="asmalser"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/30/2016"
    ms.author="asmalser"/>

# <a name="preview-managing-single-sign-on-for-enterprise-apps-in-the-new-azure-portal"></a>Anteprima: Gestione di single sign-on per App aziendali nel portale di Azure nuovo

> [AZURE.SELECTOR]
- [Portale di Azure](active-directory-enterprise-apps-manage-sso.md)
- [Portale classica Azure](active-directory-sso-integrate-saas-apps.md)

In questo articolo viene descritto come utilizzare il [portale di Azure](https://portal.azure.com) per gestire impostazioni single sign-on per le applicazioni, in particolare quelli che sono state aggiunte dalla [raccolta di Azure Active Directory (Azure Active Directory) dell'applicazione](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). L'esperienza di gestione di Azure Active Directory per single sign-on è attualmente in anteprima pubblica e in questo articolo descrive le nuove caratteristiche, nonché alcune limitazioni temporanee che saranno disponibili solo durante il periodo di anteprima. [Che cos'è nel riquadro di anteprima?](active-directory-preview-explainer.md)

##<a name="finding-your-apps-in-the-new-portal"></a>Trovare le app nel portale di nuovo

In settembre 2016, tutte le applicazioni che sono state configurate per single sign-on in una directory da un amministratore di directory usando la [raccolta di Azure Active Directory dell'applicazione](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) all'interno del [portale classica Azure](https://manage.windowsazure.com), possono ora essere visualizzate e gestite nel portale di Azure.

Queste applicazioni sono disponibili nella sezione **Applicazioni aziendali** del portale di Azure, un collegamento a cui sono disponibili nell'elenco **Altri servizi** nel [portale](https://portal.azure.com). App aziendali sono applicazioni che sono state distribuite e in uso da parte degli utenti all'interno dell'organizzazione.

![Blade applicazioni aziendali][1]

Selezionare **tutte le applicazioni** per visualizzare un elenco di tutte le app che sono stati configurati, tra cui App che sono stati aggiunti dalla raccolta. Selezione di un'app carica e delle risorse per tale app, in cui per tale app, è possibile visualizzare i report e una serie di impostazioni può essere gestita.

Per gestire le impostazioni del servizio single sign-on, selezionare **servizio Single sign-on**.

![Blade risorsa applicazione][2]


##<a name="single-sign-on-modes"></a>Modalità di Single sign-on

E il **servizio Single sign-on** inizia con un menu **modalità** , che consente la modalità single sign-on da configurare. Le opzioni disponibili includono:

* **Basata su SAML sign-on** - questa opzione è disponibile se l'applicazione supporta completo federati il single sign-on con Azure Active Directory mediante il protocollo SAML 2.0.

* **Basata su password sign-on** - questa opzione è disponibile se Azure Active Directory supporta la compilazione dell'applicazione di moduli di password.

* **Collegato accesso dal** - precedentemente noto come "Esistente single sign-on", questa opzione consente agli amministratori di inserire un collegamento all'applicazione in avvio dell'applicazione Pannello Azure Active Directory accesso o di Office 365 dell'utente.

Per ulteriori informazioni su queste modalità, vedere [come servizio single sign-on di lavoro di Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).


##<a name="saml-based-sign-on"></a>Basata su SAML sign-on

Opzione **basata su SAML sign-on** consente di visualizzare una pala suddivise in quattro sezioni:

###<a name="domains-and-urls"></a>URL e domini
Si tratta in cui tutti i dettagli dell'applicazione dominio e gli URL vengono aggiunti alla directory Azure Active Directory. Tutti gli input necessari per rendere single sign-on lavoro app sono visualizzati direttamente sullo schermo, mentre tutti gli input facoltativi possono essere visualizzati selezionando la casella di controllo **Mostra avanzate impostazioni URL** . L'elenco completo di input supportati include:

* **Accesso su URL** : nel punto in cui l'utente passa ad accedere a questa applicazione. Se l'applicazione è configurato per eseguire servizi avviati provider servizio single sign on, quindi quando un utente accede a questo URL, il provider di servizi non necessario reindirizzamento di Azure Active Directory per eseguire l'autenticazione e all'utente l'accesso. Se questo campo è vuoto, Azure AD utilizzare per avviare l'applicazione di Office 365 e il pannello di Azure Active Directory Access questo URL. Se questo campo viene omesso, quindi Azure Active Directory esegue invece provider di identità-avviati sign-on quando l'app viene avviata da Office 365, il pannello di Azure Active Directory Access o da Azure Active Directory single sign-on URL.

* **Identificatore** - URI questa devono identificare l'applicazione per la singola accesso dal è in corso la configurazione. Si tratta del valore Azure Active Directory invia all'applicazione come il parametro del gruppo di destinatari del token SAML e l'applicazione si prevede di convalida. Questo valore viene visualizzato anche come l'ID di entità in qualsiasi metadati SAML fornito dall'applicazione.

* **URL di risposta** - risposta URL è nel punto in cui l'applicazione prevede la ricezione di token SAML. Questa operazione si intende anche come URL asserzione Consumer Service (ACS). Dopo avere immesso queste, fare clic su Avanti per passare alla schermata successiva. Questa schermata vengono fornite informazioni sulle operazioni necessarie per configurare sul lato dell'applicazione in modo da accettare un token SAML da Azure Active Directory.

* **Stato di inoltro** - lo stato di inoltro è un parametro facoltativo che consente di indicare dove reindirizzare l'utente al termine dell'autenticazione dell'applicazione. In genere il valore è un URL valido per l'applicazione, ma alcune applicazioni di utilizzano questo campo in modo diverso (nella documentazione per informazioni dettagliate, vedere Accesso singolo dell'app). La possibilità di impostare lo stato di inoltro è una nuova caratteristica univoco per il nuovo portale Azure.

###<a name="user-attributes"></a>Attributi dell'utente
Si tratta in cui gli amministratori possono visualizzare e modificare gli attributi che vengono inviati in token SAML che Azure Active Directory problemi relativi all'applicazione di ogni volta che gli utenti di accedere.

Per la prima versione di anteprima, l'attributo modificabile solo supportato è l'attributo **ID utente** . Il valore dell'attributo è il campo in Azure Active Directory che identifica in modo univoco ogni utente all'interno dell'applicazione. Ad esempio, se l'app è stata distribuita utilizzando l'indirizzo di posta elettronica"" come il nome utente e un identificatore univoco, il valore da impostare per il campo "user.mail" in Azure Active Directory.

Modifica degli attributi aggiuntivi sarà supportata in un'anteprima dei successiva.

###<a name="saml-signing-certificate"></a>Certificato di firma SAML
In questa sezione vengono visualizzati i dettagli del certificato Azure Active Directory utilizza per firmare i token SAML inviati all'applicazione ogni volta che l'utente viene autenticato. In questo modo le proprietà del certificato corrente da controllare, tra cui la data di scadenza.

La possibilità di attivazione il certificato e gestire certificato ulteriore opzioni saranno supportate nelle versioni successive di anteprima. Si noti che completa gestione dei certificati ancora può essere eseguita nel [portale classica Azure](active-directory-sso-certs.md).

###<a name="application-configuration"></a>Configurazione dell'applicazione
La sezione finale fornisce documentazione e/o controlli necessari per configurare l'applicazione di utilizzare Azure Active Directory come provider di identità.

Il menu a comparsa **Applicazione configurare** istruzioni nuovo concisi e incorporata per la configurazione dell'applicazione. Si tratta di un'altra nuova caratteristica univoco per il nuovo portale Azure.

> [AZURE.NOTE] Per un esempio completo della documentazione incorporata, vedere l'applicazione di Salesforce.com. Documentazione App aggiuntive viene aggiunto continuamente durante l'anteprima.

![Documenti incorporati][3]

##<a name="password-based-sign-on"></a>Basata su password sign-on
Se supportati per l'applicazione, selezionando la modalità di Single Sign-on basate su password, selezionare **Salva** istantaneamente configurato in modo da effettuare Single Sign-on basate su password. Per ulteriori informazioni sulla distribuzione basata su password Single Sign-on, vedere [come servizio single sign-on di lavoro di Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Basata su password sign-on][4]


##<a name="linked-sign-on"></a>Collegate sign-on
Se è supportata per l'applicazione, selezionare la modalità di Single Sign-on collegata consente di immettere l'URL che si desidera il pannello di Azure Active Directory Access o Office 365 per il reindirizzamento a quando si fa clic su questa app. Per ulteriori informazioni collegate Single Sign-On (precedentemente noto come "esistente Single Sign-on"), vedere [come servizio single sign-on di lavoro di Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Collegate sign-on][5]

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG
