<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con SAP Business ByDesign | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e SAP Business ByDesign."
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
    ms.date="09/09/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Esercitazione: Integrazione di Azure Active Directory con ByDesign di SAP Business

In questa esercitazione si imparerà a integrare SAP Business ByDesign con Azure Active Directory (Azure Active Directory).

Integrazione di SAP Business ByDesign con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a ByDesign di SAP Business
- È possibile consentire agli utenti di automaticamente ottenere firmato-on di SAP Business ByDesign (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con ByDesign di SAP Business, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un SAP Business ByDesign single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione si testare Azure Active Directory il single sign-on in un ambiente di testing.

Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di SAP Business ByDesign dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Aggiunta di SAP Business ByDesign dalla raccolta
Per configurare l'integrazione di SAP Business ByDesign in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito SAP Business ByDesign dalla raccolta.

**Per aggiungere SAP Business ByDesign dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.


3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **SAP Business ByDesign**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_01.png)

7. Nel riquadro dei risultati selezionare **SAP Business ByDesign**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
In questa sezione, configurare e testare Azure Active Directory single sign-on di SAP Business ByDesign in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario riconosce l'utente corrispondente in ByDesign di SAP Business a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente di SAP Business ByDesign deve essere stabilito.

Assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** di SAP Business ByDesign stabilita la relazione di collegamento.

Per configurare e testare Azure Active Directory single sign-on con ByDesign di SAP Business, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
3. **[Creazione di un ByDesign di SAP Business testare utente](#creating-an-sap-business-bydesign-test-user)** - disporre equivalente di Britta Simon di SAP Business ByDesign collegato per la rappresentazione di Azure Active Directory di lei.
4. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione Azure Active Directory single sign-on

In questa sezione, attivare Azure Active Directory single sign-on nel portale di classica e configurare il single sign-on nell'applicazione di SAP Business ByDesign.

Applicazione di SAP Business ByDesign prevede asserzioni SAML in un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori degli attributi dalla scheda **"Atrribute"** dell'applicazione. Schermata seguente è illustrato un esempio per l'oggetto. 


**Per configurare Azure Active Directory single sign-on con ByDesign di SAP Business, procedere come segue:**


1. Nel portale di classica Azure, nella pagina integrazione applicazione **SAP Business ByDesign** , nel menu nella parte superiore, fare clic su **attributi**.

    ![Configurare Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_80.png) 


2. Nell'elenco attributi token SAML attributi, selezionare l'attributo nameidentifier e quindi fare clic su **Modifica**.

    ![Configurare Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_84.png) 

3. Nella finestra di dialogo Modifica attributo utente, procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_85.png) 

    un. Selezionare la funzione in cui vengono **ExtractMailPrefix()** dall'elenco valore dell'attributo

    b. Nell'elenco di stampa, selezionare l'attributo utente che si desidera utilizzare per l'implementazione. 
    Ad esempio, se si desidera utilizzare il campo ID dipendente come ID utente univoco e già archiviato il valore dell'attributo nel ExtensionAttribute2, quindi selezionare **user.extensionattribute2**. 

    c. Fare clic su **completa**. 
    

4. Nel portale classico, nella pagina integrazione applicazione **ByDesign di SAP Business** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .
     
    ![Configurare Single Sign-On][6] 

5. Nella pagina **specificare come gli utenti di accedere al SAP Business ByDesign** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_03.png) 

6. Nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_04.png) 

    un. Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato da agli utenti di accesso a un'applicazione di SAP Business ByDesign utilizzando il modello seguente:`https://<servername>.sapbydesign.com`
    
    b. Fare clic su **Avanti**
 
7. Nella pagina **Configura il single sign-on in ByDesign di SAP Business** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_05.png)

    un. Fare clic su **Scarica metadati**e quindi salvare il file nel computer in uso.

    b. Fare clic su **Avanti**.


8. Per ottenere SSO configurato per l'applicazione, procedere come segue:

    un. Accedere al portale di SAP Business ByDesign con diritti di amministratore.

    b. Passare **all'applicazione e attività comuni di gestione di utenti** e fare clic sulla scheda **Provider di identità** .

    c. Fare clic su **Nuovo Provider di identità** e selezionare il file XML di metadati scaricato dal portale di classica Azure. Importando i metadati, il sistema carica automaticamente il certificato di firma richiesta e certificato di crittografia.

    ![Configurare Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

    d. Per includere l' **URL del servizio Consumer asserzione** nella convocazione SAML, selezionare **Includi asserzione Consumer servizio URL**.

    e. Fare clic su **Attiva Single Sign-On**.

    f. Salvare le modifiche.

    g. Fare clic sulla scheda **Risorse del sistema** .

    ![Configurare Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

    h. Copiare l' **URL di Single Sign-on** e incollarla nella casella di testo di **Azure Active Directory Sign-On URL** .

    ![Configurare Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

    si. Specificare se il dipendente manualmente è possibile scegliere tra effettuando l'accesso con l'ID utente e la password o Single Sign-on selezionando **Selezione del Provider di identità manuale**.

    j. Nella sezione **URL di Single Sign-on** di specificare l'URL che deve essere utilizzato per il dipendente per l'accesso al sistema. 
    In inviato URL all'elenco a discesa dipendenti, è possibile scegliere tra le opzioni seguenti:
    
    **URL non Single Sign-on**
 
    Il sistema invia solo l'URL di sistema normale per il dipendente. Il dipendente non è possibile eseguire l'accesso tramite Single Sign-on e deve usare password o certificato invece.

    **URL DI SINGLE SIGN-ON** 

    Il sistema invia solo l'URL di Single Sign-on per il dipendente. Il dipendente può accedere tramite Single Sign-on. Richiesta di autenticazione viene reindirizzato tramite il IdP.

    **Selezione automatica**
 
    Se questa caratteristica non è attiva, il sistema invia l'URL di sistema normale per il dipendente. Se è attiva Single Sign-on, viene controllato se il dipendente con una password. Se una password è disponibile, sia URL Single Sign-on e Single Sign-on Non vengono inviati al dipendente. Tuttavia, se il dipendente non ha password, solo l'URL di Single Sign-on viene inviato per il dipendente.

    k. Salvare le modifiche.

9. Nel portale classico, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.
    
    ![Azure Active Directory Single Sign-On][10]

10. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  
 
    ![Azure Active Directory Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
In questa sezione, si crea un utente di test nel portale di classica chiamato Britta Simon.


![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_05.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_07.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-an-sap-business-bydesign-test-user"></a>Creazione di un utente di test di SAP Business ByDesign

In questa sezione, si crea un utente chiamato Britta Simon di SAP Business ByDesign. Lavorare con il team di supporto SAP Business ByDesign per aggiungere gli utenti della piattaforma di SAP Business ByDesign. 

> [AZURE.NOTE] Assicurarsi che il valore di NameID deve corrispondere con il campo nome utente della piattaforma di SAP Business ByDesign.

### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

In questa sezione, viene attivata Britta Simon usare il single sign-on Azure concedendo la sua ByDesign di SAP Business.

![Assegnare utente][200] 

**Per assegnare Britta Simon ByDesign di SAP Business, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **ByDesign di SAP Business**.

    ![Configurare Single Sign-On](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_50.png) 

3. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203]

4. Nell'elenco di utenti, selezionare **Sandro**.

5. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]


### <a name="testing-single-sign-on"></a>Test il single sign-on

In questa sezione è testare Azure Active Directory single sign-on configurazione mediante il pannello di accesso.

Quando si fa clic sul riquadro di SAP Business ByDesign nel Pannello di accesso, dovrebbe ottenere automaticamente firmato-on per l'applicazione di SAP Business ByDesign.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_205.png
