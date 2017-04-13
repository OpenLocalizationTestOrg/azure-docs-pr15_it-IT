<properties
    pageTitle="Esercitazione: Azure Active Directory integrazione ServiceNow e ServiceNow Express | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e ServiceNow e ServiceNow Express."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-servicenow-and-servicenow-express"></a>Esercitazione: Azure Active Directory integrazione ServiceNow e ServiceNow Express.


In questa esercitazione si imparerà a integrare ServiceNow e ServiceNow Express con Azure Active Directory (Azure Active Directory).

Integrazione di ServiceNow e ServiceNow Express con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a ServiceNow e ServiceNow Express
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a ServiceNow e ServiceNow Express (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con ServiceNow e ServiceNow Express, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Per ServiceNow, un'istanza o tenant di ServiceNow, versione Calgary o versioni successive
- Per ServiceNow Express, un'istanza di ServiceNow Express, versione Helsinki o versioni successive
- ServiceNow tenant deve avere [Più Provider singolo segno nel plug-in di](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) attivazione. A tale scopo inviando una richiesta di assistenza in <https://hi.service-now.com/> 


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione si testare Azure Active Directory il single sign-on in un ambiente di testing. Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di ServiceNow dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on per ServiceNow o ServiceNow Express


## <a name="adding-servicenow-from-the-gallery"></a>Aggiunta di ServiceNow dalla raccolta
Per configurare l'integrazione di ServiceNow o ServiceNow Express in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito ServiceNow dalla raccolta. 

**Per aggiungere ServiceNow dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **ServiceNow**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)

7. Nel riquadro dei risultati, selezionare **ServiceNow**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
In questa sezione, configurare e test Azure Active Directory single sign-on con ServiceNow o ServiceNow Express in base a un utente di test denominato "Britta Simon".

Per single sign-on per l'uso, è necessario indicare sia l'utente corrispondente in ServiceNow a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente ServiceNow deve essere stabilito.
È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in ServiceNow. Per configurare e testare Azure Active Directory single sign-on con ServiceNow, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On per ServiceNow](#configuring-azure-ad-single-sign-on-for-servicenow)** : per consentire agli utenti di utilizzare questa caratteristica.
2. **[Configurazione di Azure Active Directory Single Sign-On per ServiceNow Express](#configuring-azure-ad-single-sign-on-for-servicenow-express)** - per consentire agli utenti di utilizzare questa caratteristica.
3. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di un ServiceNow test utente](#creating-a-servicenow-test-user)** - che equivalente di Britta Simon nel ServiceNow collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
6. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

> [AZURE.NOTE] Se si desidera configurare ServiceNow omettere passaggio 2. Analogamente, se si desidera configurare ServiceNow Express omettere passaggio 1.

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>Configurazione di Azure Active Directory Single Sign-On per ServiceNow

1.  Nel portale classico di Azure Active Directory, nella pagina integrazione applicazione **ServiceNow** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al ServiceNow** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurare il single sign-on")

3.  Nella pagina **Configura impostazioni di App** , procedere come segue:

    ![Configurare URL di app] (./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurare URL di app")

    un. Nella casella di testo **ServiceNow su URL di accesso** , digitare l'URL utilizzato da agli utenti di accesso a un'applicazione di ServiceNow modello: `https://<instance-name>.service-now.com`.

    b. Nella casella di testo **identificatore** , digitare l'URL utilizzato dagli utenti per accesso a un'applicazione di ServiceNow modello: `https://<instance-name>.service-now.com`.

    c. Fare clic su **Avanti**

4.  Per configurare automaticamente ServiceNow per l'autenticazione basata su SAML Azure Active Directory, immettere il nome dell'istanza ServiceNow, nome utente di amministratore e password di amministratore nel modulo **Configura automaticamente il single sign-on** e fare clic su *Configura*. Si noti che il nome utente amministratore fornito devono avere il ruolo di **security_admin** assegnato in ServiceNow per l'oggetto per l'uso. In caso contrario, per configurare manualmente ServiceNow per l'utilizzo di Azure Active Directory come un provider di identità SAML, fare clic su **Configura manualmente l'applicazione per single sign-on**, quindi fare clic su **Avanti** e completare la procedura seguente.

    ![Configurare URL di app] (./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurare URL di app")



5.  Nella pagina **Configura il single sign-on in ServiceNow** fare clic su **Scarica certificato**, salvare il file di certificato in locale nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurare il single sign-on")

1. Accesso a un'applicazione di ServiceNow come amministratore.
2. Attivare il plug-in di _integrazione - più Provider Single Sign-On Installer_ seguendo la procedura seguente:

    un. Nel riquadro di spostamento sul lato sinistro, passare alla sezione **Definizione di sistema** e quindi fare clic su **plug-in**.

    ![Configurare URL di app] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "Plug-in attiva")
    
    b. Ricerca per _l'integrazione - più Provider Single Sign-On programma di installazione_.

    ![Configurare URL di app] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "Plug-in attiva")

    c. Selezionare il plug-in. Fare clic su e selezionare **Attiva/Aggiorna**.
    
    d. Fare clic sul pulsante **Attiva** .

2. Nel riquadro di spostamento sul lato sinistro fare clic su **proprietà**.  

    ![Configurare URL di app] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "Configurare URL di app")


3. Nella finestra di dialogo **Proprietà di più Provider Single Sign-on** , procedere come segue:

    ![Configurare URL di app] (./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configurare URL di app")

    un. Come **abilitare più provider Single Sign-on**, selezionare **Sì**.

    b. Come **attivare la registrazione di debug ottenuto il provider più integrazione Single Sign-on**, selezionare **Sì**.

    c. Nella casella di testo, **il campo all'utente della tabella che...** digitare **nome_utente**.

    d. Fare clic su **Salva**.



1. Nel riquadro di spostamento sul lato sinistro fare clic su **certificati x509**.

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Configurare il single sign-on")


1. Nella finestra di dialogo **509** , fare clic su **Nuovo**.

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configurare il single sign-on")


1. Nella finestra di dialogo **509** , procedere come segue:

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurare il single sign-on")

    un. Fare clic su **Nuovo**.

    b. Nella casella di testo **nome** digitare un nome per la configurazione (ad esempio: **TestSAML2.0**).

    c. Selezionare **Attiva**.

    d. Come **formato**, selezionare **PEM**.

    e. Come **tipo**, selezionare **Protezione archivio certificati**.
    
    f. Aprire il certificato di base 64 codificato nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **PEM certificato** .

    g. Fare clic su **Aggiorna**.


1. Nel riquadro di spostamento sul lato sinistro fare clic su **Provider di identità**.

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Configurare il single sign-on")

1. Nella finestra di dialogo **Provider di identità** , fare clic su **Nuovo**:

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configurare il single sign-on")


1. Nella finestra di dialogo **Provider di identità** , fare clic su **SAML2 Update1?**:

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configurare il single sign-on")


1. Nella finestra di dialogo Proprietà Update1 SAML2, procedere come segue:

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configurare il single sign-on")


    un. Nella casella di testo **nome** digitare un nome per la configurazione (ad esempio: **SAML 2.0**).

    b. Nel **Campo utente** casella di testo, tipo **posta elettronica** o **user_id**, a seconda di quale campo viene utilizzato per identificare gli utenti nella distribuzione ServiceNow. 
    
    > [AZURE.NOTE] È possibile configue Azure Active Directory per generare l'ID utente di Azure Active Directory (nome dell'entità utente) o l'indirizzo di posta elettronica come identificatore univoco in token SAML facendo clic sul **ServiceNow > attributi > Single Sign-On** sezione del portale di classica Azure ed eseguire il mapping del campo desiderato per l'attributo **nameidentifier** . Il valore memorizzato per l'attributo selezionato Azure Active Directory (ad esempio nome dell'entità utente) deve corrispondere al valore archiviato in ServiceNow per il campo immesso (ad esempio user_id)

    c. Nel portale classico di Azure Active Directory, copiare il valore di **ID Provider di identità** e quindi incollarla nella casella di testo **URL del Provider di identità** .

    d. Nel portale classico di Azure Active Directory, copiare il valore di **URL di richiesta di autenticazione** e quindi incollarla nella casella di testo **AuthnRequest del Provider di identità** .

    e. Nel portale classico di Azure Active Directory, copiare il valore **Singolo URL del servizio di Sign-Out** e quindi incollarla nella casella di testo **SingleLogoutRequest del Provider di identità** .

    f. Nella casella di testo **Home page di ServiceNow** , digitare l'URL della home page di istanza ServiceNow.

    > [AZURE.NOTE] La home page istanza ServiceNow rappresenta una concatenazione dei **/navpage.do** **ServieNow tenant URL** (ad esempio:`https://fabrikam.service-now.com/navpage.do`).
 

    g. Nel **ID entità / emittente** casella di testo, digitare l'URL del tenant di ServiceNow.

    h. Nella casella di testo **URL pubblico** , digitare l'URL del tenant di ServiceNow. 

    si. Nella casella di testo **Protocollo associazione per SingleLogoutRequest del IDP** digitare **urn: oasis: nomi: tc: SAML:2.0:bindings:HTTP-reindirizzare**.

    j. Nella casella di testo NameID criteri digitare **urn: oasis: nomi: tc: SAML:1.1:nameid-formato: specificato**.

    k. Deselezionare l'opzione **Crea un AuthnContextClass**.

    g. **Metodo AuthnContextClassRef**, digitare `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`. È necessario solo se si è cloud unica organizzazione. Se si usa distribuzione locale ADFS o MFA per l'autenticazione non è necessario configurare questo valore. 

    m. Nella casella di testo **Orologio inclinare** digitare **60**.

    n. Come **Singolo segno su Script**, selezionare **MultiSSO_SAML2_Update1**.

    o. Come **x509 certificato**, selezionare il certificato creato nel passaggio precedente.

    p. Fare clic su **Invia**. 



6. Nel portale classico di Azure Active Directory, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**. 

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurare il single sign-on")

7. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.
 
    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurare il single sign-on")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>Configurazione di Azure Active Directory Single Sign-On per ServiceNow Express

1.  Nel portale classico di Azure Active Directory, nella pagina integrazione applicazione **ServiceNow** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al ServiceNow** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurare il single sign-on")

3.  Nella pagina **Configura impostazioni di App** , procedere come segue:

    ![Configurare URL di app] (./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurare URL di app")

    un. Nella casella di testo **ServiceNow su URL di accesso** , digitare l'URL utilizzato da agli utenti di accesso a un'applicazione di ServiceNow modello: `https://<instance-name>.service-now.com`.

    b. Nella casella di testo **Emittente URL** digitare l'URL utilizzato dagli utenti per accesso a un'applicazione di ServiceNow modello di `https://<instance-name>.service-now.com`.

    c. Fare clic su **Avanti**

4.  Fare clic su **Configura manualmente l'applicazione per single sign-on**, quindi fare clic su **Avanti** e completare la procedura seguente.

    ![Configurare URL di app] (./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurare URL di app")

5.  Nella pagina **Configura il single sign-on in ServiceNow** fare clic su **Scarica certificato**, salvare il file di certificato in locale nel computer e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurare il single sign-on")

6. Accesso all'applicazione ServiceNow Express come amministratore.

7. Nel riquadro di spostamento sul lato sinistro fare clic su **Single Sign-On**.  

    ![Configurare URL di app] (./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "Configurare URL di app")


8. Nella finestra di dialogo **Single Sign-On** , fare clic sull'icona di configurazione in alto a destra e impostare le proprietà seguenti:

    ![Configurare URL di app] (./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "Configurare URL di app")

    un. Attivare o disattivare **abilitare più provider Single Sign-on** a destra.

    b. Attivare o disattivare **attivare la registrazione per l'integrazione di Single Sign-on provider più di debug** a destra.

    c. Nella casella di testo, **il campo all'utente della tabella che...** digitare **nome_utente**.


9. Nella finestra di dialogo **Single Sign-On** , fare clic su **Aggiungi nuovo certificato**.

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Configurare il single sign-on")



10. Nella finestra di dialogo **509** , procedere come segue:

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurare il single sign-on")

    un. Nella casella di testo **nome** digitare un nome per la configurazione (ad esempio: **TestSAML2.0**).

    b. Selezionare **Attiva**.

    c. Come **formato**, selezionare **PEM**.

    d. Come **tipo**, selezionare **Protezione archivio certificati**.

    e. Creare un file con codificata base 64 del certificato scaricata.
   
    > [AZURE.NOTE] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).
    
    f. Aprire il certificato di base 64 codificato nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **PEM certificato** .

    g. Fare clic su **Aggiorna**.


11. Nella finestra di dialogo **Single Sign-On** , fare clic su **Aggiungi nuovo IdP**.

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Configurare il single sign-on")


12. Nella finestra di dialogo **Aggiungi nuovo Provider di identità** in **Configurare Provider di identità**, procedere come segue:

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Configurare il single sign-on")


    un. Nella casella di testo **nome** digitare un nome per la configurazione (ad esempio: **SAML 2.0**).

    b. Nel portale classico di Azure Active Directory, copiare il valore di **ID Provider di identità** e quindi incollarla nella casella di testo **URL del Provider di identità** .

    c. Nel portale classico di Azure Active Directory, copiare il valore di **URL di richiesta di autenticazione** e quindi incollarla nella casella di testo **AuthnRequest del Provider di identità** .

    d. Nel portale classico di Azure Active Directory, copiare il valore **Singolo URL del servizio di Sign-Out** e quindi incollarla nella casella di testo **SingleLogoutRequest del Provider di identità** .

    e. **Certificato di Provider di identità**, selezionare il certificato creato nel passaggio precedente.


13. Fare clic su **Impostazioni avanzate**e in **Altre proprietà del Provider di identità**, eseguire le operazioni seguenti:

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Configurare il single sign-on")

    un. Nella casella di testo **Protocollo associazione per SingleLogoutRequest del IDP** digitare **urn: oasis: nomi: tc: SAML:2.0:bindings:HTTP-reindirizzare**.

    b. Nella casella di testo **NameID criteri** digitare **urn: oasis: nomi: tc: SAML:1.1:nameid-formato: specificato**.    

    c. **Metodo AuthnContextClassRef**, digitare **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.
    
    d. Deselezionare l'opzione **Crea un AuthnContextClass**.

14. In **Proprietà aggiuntive Provider di servizi**, procedere come segue:

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Configurare il single sign-on")

    un. Nella casella di testo **Home page di ServiceNow** , digitare l'URL della home page di istanza ServiceNow.

    > [AZURE.NOTE] La home page istanza ServiceNow rappresenta una concatenazione dei **/navpage.do** **ServieNow tenant URL** (ad esempio: `https://fabrikam.service-now.com/navpage.do`).

    b. Nel **ID entità / emittente** casella di testo, digitare l'URL del tenant di ServiceNow.

    c. Nella casella di testo **URI gruppo di destinatari** , digitare l'URL del tenant di ServiceNow. 

    d. Nella casella di testo **Orologio inclinare** digitare **60**.

    e. Nel **Campo utente** casella di testo, tipo **posta elettronica** o **user_id**, a seconda di quale campo viene utilizzato per identificare gli utenti nella distribuzione ServiceNow.
    
    > [AZURE.NOTE] È possibile configue Azure Active Directory per generare l'ID utente di Azure Active Directory (nome dell'entità utente) o l'indirizzo di posta elettronica come identificatore univoco in token SAML facendo clic sul **ServiceNow > attributi > Single Sign-On** sezione del portale di classica Azure ed eseguire il mapping del campo desiderato per l'attributo **nameidentifier** . Il valore memorizzato per l'attributo selezionato Azure Active Directory (ad esempio nome dell'entità utente) deve corrispondere al valore archiviato in ServiceNow per il campo immesso (ad esempio user_id)

    f. Fare clic su **Salva**. 


15. Nel portale classico di Azure Active Directory, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**. 

    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurare il single sign-on")

16. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.
 
    ![Configurare il single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurare il single sign-on")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
L'obiettivo di questa sezione è viene illustrato come attivare il provisioning degli account utente di Active Directory per ServiceNow dell'utente.


### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1. Nel portale classico di Azure Management, nella pagina integrazione applicazione **ServiceNow** , fare clic su **Configura il provisioning dell'utente**. 

    ![Il provisioning dell'utente] (./media/active-directory-saas-servicenow-tutorial/IC769498.png "Il provisioning dell'utente")


2. Nella pagina **Immettere le credenziali ServiceNow per abilitare il provisioning automatico dell'utente** , specificare le seguenti impostazioni di configurazione: configurare il Provisioning dell'utente 

     un. Nella casella di testo **Nome dell'istanza ServiceNow** , digitare il nome dell'istanza ServiceNow.

     b. Nella casella di testo **Nome utente amministratore ServiceNow** , digitare il nome dell'account di amministratore ServiceNow.

     c. Nella casella di testo **Password dell'amministratore ServiceNow** , digitare la password per l'account.

     d. Fare clic su **convalida** per verificare la configurazione.

     e. Fare clic sul pulsante **Avanti** per aprire la pagina **passaggi successivi** .

     f. Se si vuole effettuare il provisioning di tutti gli utenti all'applicazione, selezionare "**automaticamente effettuato il provisioning tutti gli account utente nella directory all'applicazione**". 

    ![Passaggi successivi] (./media/active-directory-saas-servicenow-tutorial/IC698804.png "Passaggi successivi")

     g. Nella pagina **passaggi successivi** , fare clic su **completa** per salvare la configurazione.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
In questa sezione, si crea un utente di test nel portale di classica chiamato Britta Simon.

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:
 
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   


### <a name="creating-a-servicenow-test-user"></a>Creazione di un utente di test ServiceNow

In questa sezione, si crea un utente chiamato Britta Simon in ServiceNow. In questa sezione, si crea un utente chiamato Britta Simon in ServiceNow. Se non si conosce come aggiungere un utente nell'account ServiceNow o ServiceNow Express, contattare il team di supporto ServiceNow.

### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

In questa sezione, viene attivata Britta Simon usare il single sign-on Azure concedendo la sua ServiceNow.

![Assegnare utente][200] 

**Per assegnare Britta Simon ServiceNow, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **ServiceNow**.

    ![Configurare Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203] 

1. Nell'elenco di tutti gli utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]


### <a name="testing-single-sign-on"></a>Test il single sign-on

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.

Quando si fa clic sul riquadro ServiceNow nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione ServiceNow.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png
