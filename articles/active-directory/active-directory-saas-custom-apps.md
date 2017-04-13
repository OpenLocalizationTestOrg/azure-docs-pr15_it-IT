<properties 
    pageTitle="Configurazione single sign-on per le applicazioni che non sono presenti nella raccolta di applicazione di Azure Active Directory | Microsoft Azure" 
    description="Informazioni su come connettere le app di Azure Active Directory utilizzando SAML e basate su password Single Sign-On in modalità self-service" 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/09/2016" 
    ms.author="asmalser" />

#<a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Configurazione single sign-on per le applicazioni che non sono presenti nella raccolta di applicazione di Azure Active Directory

In questo articolo è su una caratteristica che consente agli amministratori di configurare single sign-on per le applicazioni non è presente il Azure Active Directory app raccolta *senza scrivere codice*. Questa caratteristica è stata rilasciata technical preview su 18 novembre 2015 e incluse in [Azure Active Directory Premium](active-directory-editions.md). Se invece si sta cercando istruzioni per gli sviluppatori su come integrare App personalizzate con Azure Active Directory tramite il codice, vedere [Scenari di autenticazione per Azure Active Directory](active-directory-authentication-scenarios.md).

La raccolta di applicazione di Azure Active Directory per un elenco di applicazioni che supportano un modulo di single sign-on con Azure Active Directory, come descritto in [questo articolo](active-directory-appssoaccess-whatis.md). Dopo aver (come un IT specialist o sistema sistemi nel organizzazione) trovato l'applicazione di cui si desidera connettersi, è possibile iniziare da seguire le istruzioni dettagliate presentate nel portale di gestione Azure per abilitare il single sign-on.

I clienti con licenze di [Azure Active Directory Premium](active-directory-editions.md) riceveranno queste funzionalità aggiuntive:

* Integrazione modalità self-service per qualsiasi applicazione che supporta i provider di identità SAML 2.0 (SP avviata o avviata IdP)
* Modalità self-service integrazione di qualsiasi applicazione web che contiene una basate su HTML pagina di accesso tramite [Single Sign-on basate su password](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Modalità self-service connessione delle applicazioni che utilizzano il protocollo SCIM per utente provisioning (come[descritto di seguito](active-directory-scim-provisioning.md))
* Possibilità di aggiungere collegamenti a qualsiasi applicazione di [avvio delle app di Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) o il [Pannello di accesso di Azure Active Directory](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)

Può trattarsi di non solo le applicazioni di SaaS che utilizzano, ma hanno non è ancora stato nel-aggiunta nella raccolta di applicazione di Azure Active Directory, ma le applicazioni web di terze parti che l'organizzazione ha distribuito ai server che controllare, nel cloud o locale.

Queste funzionalità, noto anche come *modelli di integrazione di app*, forniscono punti di connessione basate su standard per le applicazioni che supportano SAML, SCIM o l'autenticazione basata su moduli e includono opzioni flessibili e impostazioni per la compatibilità con un numero generali delle applicazioni. 

##<a name="adding-an-unlisted-application"></a>Aggiunta di un'applicazione non in elenco

Per connettere un'applicazione tramite un modello di integrazione di app, accedere al portale di gestione Azure con il proprio account di amministratore di Azure Active Directory e individuare il **Active Directory > [Directory] > applicazioni** delle sezioni, selezionare **Aggiungi**e quindi **aggiungere un'applicazione dalla raccolta**. 

![][1]

Nella raccolta app è possibile aggiungere un'app dall'utilizzando la categoria **personalizzata** sul lato sinistro oppure selezionando il collegamento **Aggiungi applicazione non in elenco** che viene visualizzato nei risultati della ricerca se non è stata trovata l'app desiderata. Dopo aver immesso un nome per l'applicazione, è possibile configurare le opzioni di single sign-on e il comportamento. 

**Suggerimento**: ottimale, utilizzare la funzione di ricerca per verificare se l'applicazione esiste già nella raccolta dell'applicazione. Se viene trovata l'app e la relativa descrizione menzioni "single sign-on", quindi l'applicazione è già supportato per single sign-on federati. 

![][2]

Aggiunta di un'applicazione in questo modo offre un'esperienza molto simile a quello disponibile per le applicazioni già integrate. Per iniziare, selezionare **Configura Single Sign-On**. Nella schermata successiva presenta tre opzioni per la configurazione di single sign on, sono descritti nelle sezioni seguenti.

![][3]


##<a name="azure-ad-single-sign-on"></a>Azure Active Directory Single Sign-On

Selezionare questa opzione per configurare l'autenticazione basata su SAML per l'applicazione. È necessario che il supporto di applicazione SAML 2.0 ed è necessario raccogliere le informazioni su come usare le funzionalità SAML dell'applicazione prima di continuare. Dopo aver selezionato **Avanti**, verrà richiesto di immettere tre diversi URL corrispondente per gli endpoint SAML per l'applicazione. 

![][4]
 
Di seguito sono:

* **Accesso su URL (SP avviati solo)** , in cui l'utente passa ad accedere a questa applicazione. Se l'applicazione è configurato per eseguire servizi avviati provider servizio single sign on, quindi quando un utente accede a questo URL, il provider di servizi produrrà il reindirizzamento necessario per Azure Active Directory per eseguire l'autenticazione e accesso dell'utente in. Se questo campo è vuoto, Azure AD utilizzare per avviare l'applicazione di Office 365 e il pannello di Azure Active Directory Access questo URL. Se questo campo è ommited, quindi Azure Active Directory verrà eseguita provider di identità-avviati sign-on quando l'app viene avviata da Office 365, il pannello di Azure Active Directory Access o da Azure AD singolo URL sign-on (copiable dalla scheda Dashboard).

* Viene configurato **URL emittente** - autorità URL è necessario identifichi l'applicazione per il servizio single sign in. Si tratta del valore Azure Active Directory invia all'applicazione come il parametro del **gruppo di destinatari** del token SAML e l'applicazione si prevede di convalida. Questo valore viene visualizzato anche come l' **ID di entità** in qualsiasi metadati SAML fornito dall'applicazione. Controllare la documentazione dell'applicazione SAML per informazioni dettagliate su cosa sia ID entità o il valore di gruppo di destinatari. Di seguito è illustrato un esempio di come l'URL pubblico viene visualizzato nel token SAML restituito all'applicazione:

```
    <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
```

* **URL di risposta** - risposta URL è nel punto in cui l'applicazione prevede la ricezione di token SAML. Questa operazione si intende anche come **URL asserzione Consumer Service (ACS)**. Controllare la documentazione dell'applicazione SAML per informazioni dettagliate su che cos'è la sua risposta token SAML URL o ACS.
 Dopo avere immesso queste, fare clic su **Avanti** per passare alla schermata successiva. Questa schermata vengono fornite informazioni sulle operazioni necessarie per configurare sul lato dell'applicazione in modo da accettare un token SAML da Azure Active Directory. 

![][5]

Sono necessari quali valori verranno variare a seconda dell'applicazione, quindi la documentazione dell'applicazione SAML per informazioni dettagliate. **Accesso** e **disconnessione** service URL entrambe risolvere allo stesso endpoint, ossia l'endpoint di gestione delle richieste SAML per l'istanza di Azure Active Directory. L'URL emittente è quello che viene visualizzata come "Autorità" all'interno di token SAML rilasciato all'applicazione. 

Dopo aver configurato l'applicazione, fare clic sul pulsante **Avanti** e quindi **completare** per chiudere la finestra di dialogo. 

##<a name="assigning-users-and-groups-to-your-saml-application"></a>Assegnazione di utenti e gruppi per l'applicazione SAML 

Dopo l'applicazione è stato configurato per l'utilizzo di Azure Active Directory come un provider di identità basata su SAML, è quasi pronto per provare a. Come un controllo di sicurezza, Azure Active Directory non viene generato un token di modo che possano accedere all'applicazione, a meno che non dispongono di accesso con Azure Active Directory. Gli utenti sono consentiti accedere direttamente o tramite un gruppo a cui sono membri di. 

Per assegnare un utente o gruppo all'applicazione, fare clic sul pulsante **Assegna utenti** . Selezionare l'utente o gruppo che si desidera assegnare e quindi selezionare il pulsante **Assegna** . 

![][6]

Assegnazione di un utente consentirà Azure Active Directory di rilasciare il token per l'utente, nonché a causa di un riquadro dell'applicazione venga visualizzato nel Pannello di accesso dell'utente. Se si utilizza Office 365, il riquadro di un'applicazione verrà inoltre visualizzati in avvio dell'applicazione di Office 365. 

È possibile caricare un logo riquadro per l'applicazione tramite il pulsante **Carica Logo** nella scheda **Configura** per l'applicazione. 

###<a name="customizing-the-claims-issued-in-the-saml-token"></a>Personalizzazione delle attestazioni rilasciate in token SAML 

Se un utente viene autenticato all'applicazione, Azure Active Directory visualizzerà un token SAML all'app contenente informazioni (o delle attestazioni) relative all'utente che li identifica in modo univoco. Per impostazione predefinita sono inclusi nome utente dell'utente, indirizzo di posta elettronica, nome e cognome. 

È possibile visualizzare o modificare le attestazioni inviate in token SAML all'applicazione nella scheda **attributi** . 

![][7]

Ci sono due possibili motivi perché potrebbe essere necessario modificare le attestazioni rilasciate in token SAML: applicazione •The è stato scritto in modo da richiedere un set di Richiedi URI diverso o valori Richiedi •Your applicazione è stata distribuita in modo che richiede la NameIdentifier dovrebbero essere un numero diverso da username (nome dell'entità utente AKA) archiviati in Azure Active Directory. 

Per informazioni su come aggiungere e modificare le domande di questi scenari, vedere questo [articolo sulla personalizzazione dei reclami](active-directory-saml-claims-customization.md). 

###<a name="testing-the-saml-application"></a>Test dell'applicazione SAML 

Dopo aver configurati l'URL di SAML e certificati in Azure Active Directory e nell'applicazione, gli utenti o gruppi sono stati assegnati all'applicazione in Azure, crediti sono stati esaminati e modificati se necessario, quindi l'utente è pronto per accedere all'applicazione. 

Per verificare, è sufficiente accesso-all'interno Azure AD accedere a pannello di https://myapps.microsoft.com utilizzando un account utente è stata assegnata all'applicazione e quindi fare clic sul riquadro dell'applicazione avviare la procedura. In alternativa, è possibile passare direttamente all'URL Sign-On per l'applicazione e accesso da tale posizione. 

Per suggerimenti per il debug, vedere l' [articolo su come eseguire il debug basata su SAML servizio single sign-on per le applicazioni](active-directory-saml-debugging.md) 

##<a name="password-single-sign-on"></a>Password Single Sign-On

Selezionare questa opzione per configurare [basate su password servizio single sign-on](active-directory-appssoaccess-whatis.md) per un'applicazione web che contiene una pagina di accesso a HTML. Basata su password Single Sign-on, noto anche come password di archiviazione, consente di gestire l'accesso degli utenti e la password per applicazioni web che non supportano la federazione delle identità. È anche utile per situazioni in cui è necessario condividere un singolo account, ad esempio agli account app di social media dell'organizzazione più utenti. 

Dopo aver selezionato **Avanti**, verrà richiesto di immettere l'URL del basata sul web pagina di accesso dell'applicazione. Si noti che deve essere della pagina che include i campi di input nome utente e password. Dopo avere immesso, Azure Active Directory avvia un processo per analizzare la pagina di accesso per un nome utente di input e una password di input. Se il processo non è riuscito, quindi vengono tramite un processo di installazione di un'estensione del browser (richiede Internet Explorer, Chrome o Firefox) che consentirà di acquisire manualmente i campi alternativo.

Dopo la pagina di accesso viene acquisita, possono essere assegnati agli utenti e gruppi e criteri credenziali possono essere impostati come regolare [password Single Sign-on App](active-directory-appssoaccess-whatis.md).

Nota: È possibile caricare un logo riquadro per l'applicazione tramite il pulsante **Carica Logo** nella scheda **Configura** per l'applicazione. 

##<a name="existing-single-sign-on"></a>Single Sign-On esistente

Selezionare questa opzione per aggiungere un collegamento a un'applicazione al portale di Office 365 o comando Azure Active Directory dell'organizzazione. È possibile utilizzare per aggiungere collegamenti alle App web personalizzata che fanno uso Azure Active Directory Federation Services (o altro servizio di federazione) invece di Azure Active Directory per l'autenticazione. In alternativa, è possibile aggiungere collegamenti complete a pagine di SharePoint specifiche o altre pagine web da visualizzare in due pannelli accesso dell'utente. 

Dopo aver selezionato **Avanti**, verrà richiesto di immettere l'URL dell'applicazione a cui collegarsi. Al termine, gli utenti e gruppi possono assegnati all'applicazione, che determina l'applicazione di [avvio delle app di Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) o il [Pannello di accesso di Azure Active Directory](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) per tali utenti.

Nota: È possibile caricare un logo riquadro per l'applicazione tramite il pulsante **Carica Logo** nella scheda **Configura** per l'applicazione.

## <a name="related-articles"></a>Articoli correlati

- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
- [Personalizzazione delle attestazioni rilasciate in Token SAML per App pre-integrate](active-directory-saml-claims-customization.md)
- [Risoluzione dei problemi basata su SAML Single Sign-On](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png
